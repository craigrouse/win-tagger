Tealium Win Tagger - 1.1
========================

**********************
<img align="left" src="https://res.cloudinary.com/dfpz40r7j/image/upload/v1479312678/warning-icon-60_yd8bwd.png"> This library has been replaced by the [win-library](https://github.com/tealium/tealium-ios) repository. No further development or bug fixes planned for this repo.
**********************

The library included allow the native tagging of a mobile application once and then configuration of third-party analytic services remotely through [Tealium IQ](http://tealium.com/products/enterprise-tag-management/); all without needing to recode and redeploy an app for every update to these services. 


Requirements
--------------------
- Visual Studio 2013+
- Minimum build target of WinPhone 8.0+


XAML+C# Apps
------------

The libraries are built for use in XAML+C# applications for Windows Phone 8 or WinRT (Windows 8).  Applications which use 
HTML+WinJS can integrate the Tealium tracking code directly.

Installation
------------
Download, open the appropriate library .sln file (ie TealiumWinRT.sln) and compile the source code under "Release" configuration in Visual Studio (VS) 2012. Include the Dynamically Linked Library (DLL) output (ie TealiumWinRT.DLL) in your project.  You may also include the source code as a separate project in your solution.

To add the Tealium DLL to your project, do the following in VS:

  1. Open your project
  2. In the Solution Explorer -> Your app -> Right-click on References -> select "Add Reference..."
  3. In the Reference Manager -> select "Solution" in the left hand column -> click "Browse" -> find the appropriate Tealium library folder -> go into the "Bin" sub-folder -> goto the "Debug" or "Release" sub-folder -> select the appropriate TealiumXXXLibrary.dll file
  4. Click "Add"


How To Use
----------------------------------

### Import/Referencing

In any *.cs file where you need to implement a Tealium method, add "using Tealium" to the import/referencing header area.

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
Additional properties can be set using the "TrackProperty" attribute and
"TrackNavigationParameter" attribute decorators on the class definition.

Example:

```csharp

    [TrackPageProperty("myProperty1", "myValue1")]
    public sealed partial class MyPage : Common.LayoutAwarePage
    {
      . . .
    }

```


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

You can also attach additional event data for items clicked or custom events by including a dictionary as an argument:

Example:

```csharp

Dictionary<string, string> myEventData = new Dictionary<string, string>();
myEventData.add("myDataKey1", "myDataValue1");
myEventData.add("myDataKey2", "myDataValue2");
TealiumTagger.Instance.TrackItemClicked("myCustomClick", myEventData);


```

### Custom Global Data

The following instance methods are available for adding global data that will be sent with EVERY call dispatch:

```csharp

TealiumTagger.Instance.SetGlobalVariable(string, string);

TealiumTagger.Instance.SetGlobalVariables(dictionary);

```

Example:

```csharp

Dictionary<string, string> myGlobalData = new Dictionary<string, string>();
myGlobalData.add("myDataKey1", "myDataValue1");
myGlobalData.add("myDataKey2", "myDataValue2");
TealiumTagger.Instance.SetGlobalVariables(myGlobalData);


```

You can also add a dictionary of custom data to the init method:

```csharp

Dictionary<string, string> myGlobalData = new Dictionary<string, string>();
myGlobalData.add("myDataKey1", "myDataValue1");
myGlobalData.add("myDataKey2", "myDataValue2");
TealiumTagger.Initialize(applicationFrame, new TealiumSettings("yourAccountName", "yourProfileName", "targetEnvironment", myGlobalData);


```

### Alternate XAML behavior for WinRT 
For convenience, an attached behavior has also been created for use in XAML for the
purpose of reporting custom events.  At the moment, this feature is only available in WinRT projects.
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
the call.  NOTE: due to limitations in the Windows 8 WinRT APIs, the parameter name & value
must be a hard-coded string and cannot use databinding syntax.


Tealium Settings
----------------

To minimize the need to customize the library, a variety of settings are provided to
tailor it for different applications' needs.

 - Account / Profile / Environment - account-specific settings for your application
 - EnableOfflineMode (default:true) - caches analytics calls if the app is offline and
queues them for submission once connectivity is restored.  Note: the order of requests
is persisted, but there is no guarantee that timestamps will be correct once the
requests are processed.  The queue will only be processed when the application is
running.
 - UseSSL (default:false) - whether to referene the SSL/HTTPS version of the tracking
control (true) or the standard HTTP version (false).
 - AutoTrackPageViews (default:true) - whether to automatically log a 'view' metric with 
the Tealium Tagger whenever a new page navigation is performed.  Disable this if manually
tracking page views with TealiumTagger.Instance.TrackPageView().
 - ViewMetricEventName / ViewMetricIdParam / ClickMetricEventName / ClickMetricIdParam - 
overrides the default names given to the view and click events.

Support
-------

For additional help and support, please send contact your account manager.


--------------------------------------------------------
Copyright (C) 2012-2015, Tealium Inc.
