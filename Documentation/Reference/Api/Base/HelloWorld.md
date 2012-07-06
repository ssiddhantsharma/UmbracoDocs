# Simple /base samples

To explain how base works, we'll do a quick "Hello World" sample. Open Visual Studio (or visual C# express) (VB.net can also be used, but all the samples are written in C#)  

- Create a new project, this could be a class library or a asp.net web application project.  We will only be interested in the DLL that is produced on compile.

I created a namespace called `BaseTest` and a class called `TestClass`.

    namespace BaseTest {
        [RestExtension("myAlias")]
        public class TestClass {
            [RestExtensionMethod()]
            public static string Hello() {
                return "Hello World";
            }
        }
    } 

So here we have a simple static string called "Hello" which will return the string  "Hello World". Now let's get this hooked up to /base.

Compile your project and copy the projects .dll to your umbraco /Bin folder - In this sample the DLL is called BaseTest.dll.

###Automatic registration using Attributes
In the above example, Because we used the Attributes "RestExtension" and "RestExtensionMethod", umbraco is smart enough (since version 4.5) to hook up the methods automatically.  After coping the dll file to your umbraco bin folder, try calling the url /Base/myAlias/Hello.  You will get the response: "Hello World".

###Registration by configuration
If you prefer the use of config files instead of the attributes, you can change register your methods through the restExtensions.config file.

Open the /config/restExtensions.config in notepad, we will now register this method in /base. 

Edit the restExtensions.config file so it looks like this:

    <?xml version="1.0" encoding="utf-8"?>
    <RestExtensions>
      <ext assembly="/bin/BaseTest" type="BaseTest.TestClass" alias="TestAlias">
        <permission method="Hello" allowAll="true" />
      </ext>
    </RestExtensions> 

If you've followed the above tutorial you can now goto this url: <your umbraco installations domain>/Base/TestAlias/Hello.aspx

This should give you an xml page with a single node in it: `<value>Hello World</value>`

##So what just happened?
So what we just did was build a simple string method in .net and afterwards gained direct access to it's return value using a standard Url. Let's take a closer look on what we did to make that happen.

First we setup our "TestClass" in the /Base configuration file "restExtenstions.config" by adding this line:

    <ext assembly="/bin/BaseTest" type="BaseTest.TestClass" alias="TestAlias">

This line tells /Base that we're using "Basetest.dll" in the "bin" folder. And in this assembly we're using the class `TestClass` in the `BaseTest` Namespace, and lastly it tells /Base that we'll reference `TestClass` with the alias `TestAlias` So this basicly tells the system that all requests send to urls starting with /Base/TestAlias will be send to the methods within the `TestClass`

So now we have the class setup, now we need to setup permissions for each individual method with that class, so you don't need to expose everything in a class. To do that we added this line to the config

    <permission method="Hello" allowAll="true" />

It holds the methods name and allows everyone to access it. Later we'll look at how to control what umbraco members have access.

That's it. Now you've setup a very simple /base url. Next we'll look at how to return more complex data and how to send data to umbraco using simple urls.