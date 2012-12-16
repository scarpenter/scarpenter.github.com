---
layout: post
title: "GetCustomAttributes() and Overridden Properties"
date: 2012-12-15 19:42
comments: false
categories:
---
When using custom attributes in .Net, one of the most common tasks is to query a member for any custom attributes that are applied.  The [MemberInfo](http://msdn.microsoft.com/en-us/library/8fek28hz.aspx) class has a [GetCustomAttributes](http://msdn.microsoft.com/en-us/library/dwc6ew1d.aspx) method for just this purpose.

{% codeblock lang:csharp %}
public class ExampleAttribute : Attribute
{
}

public class ClassWithAttribute
{
    [Example]
    public virtual string Name { get; set; }
    [Example]
    public virtual int Age { get; set; }
}

public static class AttributeChecker
{
    public static IEnumerable<string> PropertiesWithAttribute(Type type)
    {
        IList<string> propertiesWithAttribute = new List<string>();
        var properties = type.GetProperties(BindingFlags.Public | BindingFlags.Instance);
        foreach(var property in properties)
        {
            var attributes = property.GetCustomAttributes(typeof(ExampleAttribute), true);
            if (attributes.Length > 0) { propertiesWithAttribute.Add(property.Name); }
        }
        return propertiesWithAttribute;
    }
}

// Somewhere where the presence of the attribute is being checked
var properties = AttributeChecker.PropertiesWithAttribute(typeof(ClassWithAttribute));
{% endcodeblock %}

This is fairly straightforward reflection code that loops through all of the public properties on `ClassWithAttribute` and returns the names of any that have the `ExampleAttribute` applied.  In the example shown, on line 29 the `properties` variable will be a list containing the words "Name" and "Age".

But what happens if we use a class derived from `ClassWithAttribute`?

{%codeblock lang:csharp %}
public class DerivedClassWithAttribute : ClassWithAttribute {
    // Override the "Name" property to supply an additional attribute.
    [DefaultValue("Phil")]
    public override string Name { get; set;}
}

var properties = AttributeChecker.PropertiesWithAttribute(typeof(DerivedClassWithAttribute));
// What does properties contain?
{% endcodeblock %}

Perhaps surprisingly, the `properties` variable on line 7 contains the single word "Age".  This surprised me since I'm supplying `true` as the second parameter to `GetCustomAttributes()` on line 21 of the first example.  When the second parameter is true, the member's inheritance chain should be searched for attributes.  So why didn't it find the `ExampleAttribute` declared in `ClassWithAttribute`?  The answer is in the documentation, although I missed it the first few times I read it (emphasis mine):
{% blockquote MSDN http://msdn.microsoft.com/en-us/library/dwc6ew1d.aspx MemberInfo.GetCustomAttributes %}
inherit: true to search this member's inheritance chain to find the attributes; otherwise, false. <strong>This parameter is ignored for properties and events; see Remarks</strong>.
{% endblockquote %}

The second parameter to `GetCustomAttributes()` is ignored if the method is being called on a property or an event (we have a property in this case).  Luckily the "Remarks" section of the documentation has a solution (emphasis mine):
{% blockquote MSDN http://msdn.microsoft.com/en-us/library/dwc6ew1d.aspx MemberInfo.GetCustomAttributes %}
This method ignores the inherit parameter for properties and events. <strong>To search the inheritance chain for attributes on properties and events, use the appropriate overloads of the <a href="http://msdn.microsoft.com/en-us/library/system.attribute.getcustomattributes.aspx">Attribute.GetCustomAttributes</a> method</strong>.
{% endblockquote %}

So in this case we can simply replace line 21 in `AttributeChecker.PropertiesWithAttribute()` with the following:

{% codeblock lang:csharp %}
var attributes = Attribute.GetCustomAttributes(property, typeof(ExampleAttribute), true);
{% endcodeblock %}

Now running the code for `DerivedClassWithAttribute` will correctly return both "Name" and "Age".  A simple change, but something that tripped me up for a while none the less.
