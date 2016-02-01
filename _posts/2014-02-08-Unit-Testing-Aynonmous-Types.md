---
layout: post
title:  "Unit testing aynomous types"
date:   2014-02-01 21:22:52 +1100
comments: true
categories: C#
---
A colleague had a case where he needed to test the values assigned in an anonymous type returned by a class. With anyonmous types the .Equals() behaves a little a differently from typed objects. To paraphrase [MSDN](http://msdn.microsoft.com/en-us/library/bb397696.aspx) if, within the same assembly, two or more anonymous object initialisers define a sequence of properties that are equal in sequence, name and type the compiler treats them as instances of the same type. For example if we wrote the following test you would find it will pass.

{% highlight csharp %}
[Fact]
public void CompareTwoAnonymousTypes()
{
    var expected = new  { FirstName = "John", LastName = "Cale" };
    var actual = new  { FirstName = "John", LastName = "Cale" };
    
    Assert.Equal(expected, actual);
}
{% endhighlight %}

On the other hand if we swapped the properties around on one of the initialisers the test would fail (in fact it would raise a compiler error).

{% highlight csharp %}   
[Fact]
public void CompareTwoAnonymousTypesWithDifferentPropertySequence()
{
    var expected = new { LastName = "Cale",FirstName = "John" };
    var actual = new { FirstName = "John", LastName = "Cale" };

    Assert.Equal(expected, actual);
}
{% endhighlight %}

But this not the real issue. As started previously, anonymous must be in the same assembly for them to be considered the same type. Given we have the following class in another assembly:
{% highlight csharp %}    
public class TestClass
{
    public object GetAnonymousType()
    {
        return new {  FirstName = "Lou", LastName = "Reed" };
    }
}
{% endhighlight %}
any attempt to validate the return value in a unit test will fail.

So what the solution?

A great tool for testing two instances of a given class to ensure that their property values are equal is [FluentAssertions](https://github.com/dennisdoomen/fluentassertions).  FluentAssertions has a method called ShouldBeEquivalentTo() which does an object graph comparison. For example:

{% highlight csharp %}
[Fact]
public void CompareTwoInstanceOfPerson()
{
    var expected = new Person{FirstName = "James", LastName = "Brown"};
    var actual = new Person{FirstName = "James", LastName = "Brown"};

    actual.ShouldBeEquivalentTo(expected);
}
{% endhighlight %}

This also works for comparing two different classes that have the same properties such as comparing a dto with the source of the data.
 
{% highlight csharp %}  
[Fact]
public void PersonDtoShouldEqualPerson()
{
    var person = new Person {FirstName = "Lou", LastName = "Reed"};
    var personDto = new PersonDto { FirstName = "Lou", 
    LastName = "Reed" };

    personDto.ShouldBeEquivalentTo(person);
}
{% endhighlight %}

So does this help with my colleague's problem? Will yes it does but with one twist. By convention, with unit tests, we test the actual value against the expected value. For example actual.ShouldBeEquivalentTo(expected). When evaluating anonymous types we need to flip it around and test the expected value.

{% highlight csharp %}
[Fact]
public void ObjectShouldBeEquivalentToReturnValue()
{
    var tester = new TestClass();

    var expected = new {FirstName = "Ted", LastName = "Dancer"};

    var actual = tester.GetAnonymousType();

    expected.ShouldBeEquivalentTo(actual);
}
{% endhighlight %}

    
