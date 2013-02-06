Tealium WinRT Tagger
====================

This library provides Tealium customers the means to tag their WinRT XAML
applications for the purpose of leveraging the vendor-neutral tag management
platform offered by Tealium.  

It provides:

- web-analytics integration via the Tealium platform
- automatic view controller tracking, similar to traditional web page tracking, utilizing your favorite analytics vendor
- intelligent network-sensitive caching
- simple to use messages, including singleton or dependency-injection-friendly instances.
- custom action tracking
- implemented with the user in mind. All tracking calls are asynchronous as to not interfere or degrade the user experience. 


Tealium Requirements
--------------------
First, ensure an active Tealium account exists. You will need the following items:
- Your Tealium Account Id (it will likely be your company name)
- The Tealium Profile name to be associated with the app (your account may have several profiles, ideally one of them is dedicated to your iOS app)
- The Tealium environment to use:
  - TealiumTargetProd
  - TealiumTargetQA
  - TealiumTargetDev

Windows 8 - WinRT with XAML+C# Apps
-----------------------------------

The library is built for use in XAML+C# applications for WinRT.  Applications which use 
HTML+WinJS can integrate the Tealium tracking code directly.

Installation
------------
Download and compile the source code under "Release" configuration in Visual Studio 2012
and include the DLL output (TealiumWinRT.DLL) in your project.  You may also include
the source code as a separate project in your solution.

How To Use
----------------------------------

### Initialization

In your App.xaml.cs file, add the following code to the OnLaunched event:

 - TealiumTagger.Initialize(new TealiumSettings("YOUR_TEALIUM_ACCOUNT", "YOUR_TEALIUM_PROFILE", TealiumEnvironment.TealiumTargetDev ));
 - Replace "YOUR_TEALIUM_ACCOUNT" and "YOUR_TEALIUM_PROFILE" with your appropriate account settings.
 - Use conditional compilation flags (e.g. "#if DEBUG") to select the appropriate TealiumEnvironment setting based on the selected configuration.
 - If default settings are used, this will automatically track all page views in the
app with no additional coding required.

### View Tracking

Using default settings, page views are automatically tracked with every new forward
navigation in your app.  This is controlled by the "AutoTrackPageViews" property in
the TealiumSettings object.  By default, it will report the object/class name of your XAML
page as the page name.  You can override this value by decorating your class definition
with an instance of the TrackPageViewAttribute attribute.

Example:

```csharp

    [TrackPageView("homepage")]
    public sealed partial class MyPage : Common.LayoutAwarePage
    {
      . . .
    }

```

Setting the "TrackPageView" attribute on a page will report a page view metric,
regardless of whether "AutoTrackPageViews" is enabled.
Additional properties can be set using the "TrackPropertyAttribute" and
"TrackNavigationParameterAttribute" decorators on the class definition.

Alternatively, you can manually record a page view metric.  You may choose to do this if 
you need to include a custom collection of properties or if you wish to delay reporting
(such as waiting for data to load).  If you are manually recording 'view' metrics, then
you will need to set AutoTrackPageViews=false, otherwise you will have duplicates.

Example:

```csharp

TealiumTagger.Instance.TrackScreenViewed("my-page-name", new Dictionary<string, string>() { { "custom-prop-1", "value-1" }, { "custom-prop-2", someObject.SomeValue } });

```


### Custom Item Click Tracking

The Tealium Tagger is capable of tracking any action occurring within the app utilizing 
one of these two methods:

```csharp

TealiumTagger.Instance.TrackItemClicked(itemId);

TealiumTagger.Instance.TrackCustomEvent(eventVarName);

```

For convenience, an attached behavior has also been created for use in XAML for the
purpose of reporting custom events.
To use this, first register the "Tealium" namespace at the top of your XAML file(s):

```html

<common:LayoutAwarePage
    x:Name="pageRoot"
    xmlns:tealium="using:Tealium"
    >

````

Then on any control that has an event you wish to handle, register the attached property:

```html

        <GridView
            x:Name="itemGridView">
            <tealium:TealiumEventBehavior.Event>
                <tealium:TealiumEvent EventName="ItemClick" VariableName="click">
                    <tealium:ParameterValue PropertyName="custom-prop-1" PropertyValue="value-1" />
                    <tealium:ParameterValue PropertyName="custom-prop-2" PropertyValue="value-2" />
                </tealium:TealiumEvent>
            </tealium:TealiumEventBehavior.Event>
        . . .
        </GridView>

```

In the above example, we are registering for the "ItemClick" event on a GridView and will
report it to Tealium as a "click".  The example also includes two custom properties on
the call.



Support
-------

For additional help and support, please send all inquires to mobile_support@tealium.com

