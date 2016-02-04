---
layout: post
title:  "The ng-include, the $watch and the child scope"
date:   2014-02-18 21:22:52 +1100
categories: Angularjs JavaScript
comments: true
---

"Beware the child scope for it will bite you on the arse"

Even after eight months on a project that is using AngularJS the most I can claim is I know enough to be dangerous and to be lulled into a false sense of security. This false sense of security lead to no small amount of hair-pulling the over day. 

The issue I encountered involved a custom directive we have for auto-completing values in a text field. The field itself is used to look-up hospitals. As the user types in a name the directive displays a list of possible matches and when the user selects one it takes the corresponding hospital object and assigns it to a property on the scope that was passed into the directive. A watch is set-up in the scope to listen to the property so that when it changes it extracts values from the hospital object and displays them to the user. To express this is in code, the controller had the following:
    
    var controller = function ($scope, patientReferralService) {
            $scope.selectedPatientHospital = undefined;
    
            $scope.$watch("selectedPatientHospital", function (newValue, oldValue) {
                // if we have a hospital then populate required values. 
                if (newValue) {
                    $scope.referral.Patient.Location.Hospital = newValue.Nickname;
                    $scope.referral.Patient.Location.HospitalId = newValue.Id;
                    if (newValue.hasOwnProperty("SwitchboardPhone")) {
                        $scope.referral.Patient.Location.SwitchboardNumber = newValue.SwitchboardPhone;
                    }
                }
            });
            //rest of code removed for brevity
        };

And the html:

    <div>
        <input type="text" data-ng-model="referral.Patient.Location.Hospital" id="inputHospitalName" name="inputHospitalName"
               data-campus-auto-complete="selectedPatientHospital" data-source="campuses"
        />
    </div>

All well and good, and this has been working just fine until I decided extract some of the UI into a partial view and add it to the form as an ng-include. Now when I ran the application the $watch was not being fired.

Just to show how much I still have to learn, what I didn't know is that when you use ng-include (or  ng-repeat, ng-include, ng-switch, ng-view, ng-controller) is that Angular creates a new/child scope which prototypically inherits from its parent scope. 

This is usually not a problem until you either attempt a 2-way binding on a primitive value defined on the parent or, as in my case, try to add a new instance of an object defined on the parent. The problem is not with Angular but with how prototypical inheritance works in JavaScript.

In essence what happens, why my code no longer worked, is that when you assign a value to a primitive on the child, instead of walking the inheritance tree of the child to find the corresponding property it creates a new property which shadows/hides the parent property.

The solution, once I understood this was fairly simple. Rather than having a property on the parent that the directive changed directly I created an object that would have properties that could assigned to and watched. The modified code looks something like this:

    var controller = function ($scope, patientReferralService) {
    $scope.thingsToWatch = {
        selectedPatientHospital: undefined,
        selectedPlannedDestinationSite: undefined
    };
    $scope.$watch("thingsToWatch.selectedPatientHospital", function (newValue, oldValue) {
        // if we have a hospital then populate required values. 
        if (newValue) {
            $scope.referral.Patient.Location.Hospital = newValue.Nickname;
            $scope.referral.Patient.Location.HospitalId = newValue.Id;
            if (newValue.hasOwnProperty("SwitchboardPhone")) {
                $scope.referral.Patient.Location.SwitchboardNumber = newValue.SwitchboardPhone;
            }
        }
    });
    //rest of code removed for brevity
    };

    <div>
        <input type="text" data-ng-model="referral.Patient.Location.Hospital" id="inputHospitalName" name="inputHospitalName"
    data-campus-auto-complete="thingsToWatch.selectedPatientHospital" data-source="campuses"/>
    </div>

If you want a more in-depth explantation of the problem, I found this article really helpful [Understanding Scopes](https://github.com/angular/angular.js/wiki/Understanding-Scopes#wiki-ngInclude).

















