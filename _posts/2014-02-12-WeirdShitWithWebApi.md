---
layout: post
title:  "Weird shit with WebAPI, Basic Authentication & Session Authentication Modules"
date:   2014-02-12 21:22:52 +1100
categories: WebApi
comments: true
---
On a current client's site we have the following:

* A custom login REST service endpoint that allows the passing in of a username and password using basic authentication and issue a Json Web Token (JWT).

* Another REST service to update data. This requires the JWT to be passed in with the call as a Bearer token in the Authorization header.

Within the web application itself this has been working fine XHR and adding the Authorization header with the appropriate value where required. 

This has all been working fine. 

Recently the client has requested that their clients would like to have the ability to update data from their own systems rather than entering it through the website. Fine, not a problem since the REST services already exist. I thought I would knock up a quick proof of concept just (as a console app) to confirm that all works as expected and to provide guidance for external users.

Now I have done this numerous times and here is a sample of the code:

{% highlight csharp %}	
private static TokenResponse GetAuthenticationToken(HttpClient client, 
        Uri loginUri)
{
    var encoding = Encoding.GetEncoding("iso-8859-1");
    TokenResponse tokenResponse = null;
    var authHeader = new AuthenticationHeaderValue(
    "Basic", 
    Convert.ToBase64String(
    encoding.GetBytes(
        string.Format("{0}:{1}", "testUser", "somepassword"))));

    client.DefaultRequestHeaders.Authorization = authHeader;
    client.DefaultRequestHeaders.Accept.ParseAdd("application/json");

    var result = client.GetAsync(loginUri).Result;

    if (result.IsSuccessStatusCode)
    {
        var response = result.Content.ReadAsStringAsync().Result;

        tokenResponse = 
        JsonConvert.DeserializeObject<TokenResponse>(response);
    }
    else
    {
        Console.WriteLine(result.StatusCode);
    }
    return tokenResponse;
}
{% endhighlight %}

Thing is every time I ran it the call to the login endpoint returned a 401.
Okay clearly I am doing some wrong. Fire up Fiddler and check the header. Nope all good I have an Authorization header. Okay next debug into the delegating handler that handles the basic authentication. Inspect the request header and I have no Authorization header. Somewhere between making the call and hitting the delegating handler the Authorization header has been nuked.

As you could appreciate this is a little disconcerting and after a few hours of exploration and hair pulling downright frustrating. And then I noticed this in the web.config.

{% highlight xml %}
<add name="SessionAuthenticationModule" 
     type="System.IdentityModel.Services.SessionAuthenticationModule, System.IdentityModel.Services, Version=4.0.0.0, Culture=neutral, PublicKeyToken=B77A5C561934E089" />
 {% endhighlight %}
Hmmm at some point we must have playing around with the idea of using WIF for our authentication. Surely that can't be causing the problem but with nothing to lose at this point I decided to comment it out. Ran my test app and it all worked. 

Now why this was nuking my Authorization header I am not sure. I have debugged into the class but can't Identity where it is doing it but removing it has fixed the problem so I am going to leave it at. 

**Note to self:** remember to removed unneeded settings from the web.config