---
nav_title: Creating Feature Flags
article_title: Creating Feature Flags
page_order: 20
description: "This reference article covers how to create feature flags to coordinate new feature rollouts."
tool: Feature Flags
platform:
  - iOS
  - Android
  - Web

---

# Creating feature flags

> Feature flags allow you to remotely enable or disable functionality for a selection of users. Create a new feature flag within the Braze dashboard. Provide a name and an `ID`, a target audience, and a percentage of users for whom to enable to this feature. Then, using that same `ID` in your app or website's code, you can conditionally run certain parts of your business logic. To learn more about feature flags and how you can use them in Braze, see [About feature flags][5].

## Prerequisites

### SDK version

To use feature flags, ensure your SDKs are up to date with at least these minimum versions:

{% sdk_min_versions swift:5.9.0 android:24.2.0 web:4.6.0 unity:4.1.0 cordova:5.0.0 reactnative:4.1.0 flutter:6.0.0 roku:1.0.0 %}

### Braze permissions

To manage feature flags in the dashboard, you'll either need to be an Administrator, or have the following [permissions][9]:

| Permission                                                                    | What you can do                           |
|-------------------------------------------------------------------------------|-------------------------------------------|
| **Manage Feature Flags**                                                      | View, create, and edit feature flags.     |
| **Access Campaigns, Canvases, Cards, Feature Flags, Segments, Media Library** | View the list of available feature flags. |
{: .reset-td-br-1 .reset-td-br-2 .reset-td-br-3}

## Creating a feature flag

### Step 1: Create a new feature flag

Go to **Messaging** > **Feature Flags**, then select **Create Feature Flag**.

![A list of previously created feature flags on the Braze dashboard][1]{: style="max-width:75%"}

### Step 2: Fill out the details

Under **Details**, enter a name, ID, and description for your feature flag.

| Field        | Description                                                                                                                                                                                                         |
|--------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Name         | A human-readable title for your marketers and administrators.                                                                                                                                                       |
| ID           | The unique ID you'll use in your code to check if this feature is [enabled for a user](#enabled). This ID cannot be changed later, so review our [ID naming best practices](#naming-conventions) before continuing. |
| Description  | An optional description that gives some context about your feature flag.                                                                                                                                            |
{: .reset-td-br-1 .reset-td-br-2 .reset-td-br-3}

### Step 3: Create custom properties

Under **Properties**, create custom properties your app can access through the Braze SDK when your feature is enabled. You can assign a string, boolean, image, timestamp, JSON or a number value to each variable, as well as set a default value.

{% tabs local %}
{% tab example %}
In the following example, the feature flag shows an out-of-stock banner for an ecommerce store using the custom properties listed: 

|Property Name|Type|Value|
|--|--|--|
|`banner_height`|`number`|`75`|
|`banner_color`|`string`|`blue`|
|`banner_text`|`string`|`Widgets are out of stock until July 1.`|
|`dismissible`|`boolean`|`false`|
|`homepage_icon`|`image`|`http://s3.amazonaws.com/[bucket_name]/`|
|`account_start`|`timestamp`|`2011-01-01T12:00:00Z`|
|`footer_settings`|`JSON`|`{ "colors": [ "red", "blue", "green" ], "placement": 123 }`|

{% alert tip %}
There is no limit to the number of properties you can add. However, a feature flag's properties are limited to a total of 10kB. Both property values and keys are limited to 255 characters in length.
{% endalert %}
{% endtab %}
{% endtabs %}

### Step 4: Choose segments to target

Before rolling out a feature flag, you need to choose a [segment]({{site.baseurl}}/user_guide/engagement_tools/segments/) of users to target. Use the **Add Filter** dropdown menu to filter users out of your target audience. Add multiple filters to narrow your audience further.

![Two dropdown menus. The first reads Target Users by Segment. The second reads Additional Filters.][3]

### Step 5: Set the rollout traffic {#rollout}

Feature flags are always disabled by default, allowing you to separate your feature release's date from your total user activation. To begin your rollout, use the **Rollout Traffic** slider to choose the percentage of random users in your selected segment to receive this new feature.

![A slider labeled Rollout Traffic, spanning between 0 and 100.][4]

{% alert important %}
Do not set your rollout traffic above 0% until you are ready for your new feature to go live. When you initially define your feature flag in the dashboard, leave this setting at 0%.
{% endalert %}

## Verifying the feature flag {#enabled}

Once you have defined your feature flag, configure your app or site to check whether or not it is enabled for a particular user. When it is enabled, you'll set some action or reference the feature flag's variable properties based on your use case. The Braze SDK provides getter methods to pull your feature flag's status and its properties into your app. 

Feature flags are refreshed automatically at session start so that you can display the most up-to-date version of your feature upon launch. The SDK caches these values so they can be used while offline. 

{% alert note %}
Be sure to log [feature flag impressions](#impressions). 
{% endalert %}

Let's say you were to rolling out a new type of user profile for your app. You might set the `ID` as `expanded_user_profile`. Then, you would have your app check to see if it should display this new user profile to a particular user. For example:

{% tabs %}
{% tab JavaScript %}

```javascript
const featureFlag = braze.getFeatureFlag("expanded_user_profile");
if (featureFlag.enabled) {
  console.log(`expanded_user_profile is enabled`);
} else {
  console.log(`expanded_user_profile is not enabled`);
}
```

{% endtab %}
{% tab Swift %}

```swift
let featureFlag = braze.featureFlags.featureFlag(id: "expanded_user_profile")
if featureFlag.enabled {
  print("expanded_user_profile is enabled")
} else {
  print("expanded_user_profile is not enabled")
}
```
{% endtab %}
{% tab Java %}
```java
FeatureFlag featureFlag = braze.getFeatureFlag("expanded_user_profile");
if (featureFlag.getEnabled()) {
  Log.i(TAG, "expanded_user_profile is enabled");
} else {
  Log.i(TAG, "expanded_user_profile is not enabled");
}
```

{% endtab %}
{% tab Kotlin %}

```kotlin
val featureFlag = braze.getFeatureFlag("expanded_user_profile")
if (featureFlag.enabled) {
  Log.i(TAG, "expanded_user_profile is enabled.")
} else {
  Log.i(TAG, "expanded_user_profile is not enabled.")
}
```

{% endtab %}
{% tab React Native %}

```javascript
const featureFlag = await Braze.getFeatureFlag("expanded_user_profile");
if (featureFlag.enabled) {
  console.log(`expanded_user_profile is enabled`);
} else {
  console.log(`expanded_user_profile is not enabled`);
}
```

{% endtab %}
{% tab Unity %}
```csharp
var featureFlag = Appboy.AppboyBinding.GetFeatureFlag("expanded_user_profile");
if (featureFlag.Enabled) {
  Console.WriteLine("expanded_user_profile is enabled");
} else {
  Console.WriteLine("expanded_user_profile is not enabled");
}
```
{% endtab %}

{% tab Cordova %}
```javascript
const featureFlag = await BrazePlugin.getFeatureFlag("expanded_user_profile");
if (featureFlag.enabled) {
  console.log(`expanded_user_profile is enabled`);  
} else {
  console.log(`expanded_user_profile is not enabled`);
}
```
{% endtab %}
{% tab Flutter %}
```dart
BrazeFeatureFlag featureFlag = await braze.getFeatureFlagByID("expanded_user_profile");
if (featureFlag.enabled) {
  print("expanded_user_profile is enabled");
} else {
  print("expanded_user_profile is not enabled");
}
```
{% endtab %}

{% tab Roku %}
```brightscript
featureFlag = m.braze.getFeatureFlag("expanded_user_profile")
if featureFlag.enabled
  print "expanded_user_profile is enabled"
else
  print "expanded_user_profile is not enabled"
end if
```
{% endtab %}
{% endtabs %}

### Logging a feature flag impression {#impressions}

Track a feature flag impression whenever a user has had an opportunity to interact with your new feature, or when they __could__ have interacted if the feature is disabled (in the case of a control group in an A/B test).

Usually, you can put this line of code directly underneath where you reference your feature flag in your app:

{% tabs %}
{% tab JavaScript %}

```javascript
braze.logFeatureFlagImpression("expanded_user_profile");
```

{% endtab %}
{% tab Swift %}

```swift
braze.featureFlags.logFeatureFlagImpression(id: "expanded_user_profile")
```

{% endtab %}
{% tab Java %}

```java
braze.logFeatureFlagImpression("expanded_user_profile");
```

{% endtab %}
{% tab Kotlin %}

```kotlin
braze.logFeatureFlagImpression("expanded_user_profile")
```

{% endtab %}
{% tab React Native %}

```javascript
Braze.logFeatureFlagImpression("expanded_user_profile");
```

{% endtab %}
{% tab Unity %}

```csharp
Appboy.AppboyBinding.LogFeatureFlagImpression("expanded_user_profile");
```

{% endtab %}
{% tab Cordova %}
```javascript
BrazePlugin.logFeatureFlagImpression("expanded_user_profile");
```
{% endtab %}
{% tab Flutter %}
```dart
braze.logFeatureFlagImpression("expanded_user_profile");
```
{% endtab %}
{% tab Roku %}
```brightscript
m.Braze.logFeatureFlagImpression("expanded_user_profile");
```
{% endtab %}
{% endtabs %}

### Accessing properties {#accessing-properties}

To access the properties of a feature flag, use one of the following methods depending on the type you defined in the dashboard.

If a feature flag is not enabled, or a property you reference does not exist, these methods will return `null`.

{% tabs %}
{% tab JavaScript %}

```javascript
// Feature flag instance
const featureFlag = braze.getFeatureFlag("expanded_user_profile");
// String properties
const stringProperty = featureFlag.getStringProperty("color");
// Boolean properties
const booleanProperty = featureFlag.getBooleanProperty("expanded");
// Number properties
const numberProperty = featureFlag.getNumberProperty("height");
// returns the property as a number in milliseconds
const timestampProperty = featureFlag.getTimestampProperty("account_start");
// returns the property as a string of the image URL
const imageProperty = featureFlag.getImageProperty("homepage_icon");
// returns the JSON property as as JSON object 
const jsonProperty = featureFlag.getJsonProperty("footer_settings");
```

{% endtab %}
{% tab Swift %}

```swift
// Feature flag instance
let featureFlag: FeatureFlag = braze.featureFlags.featureFlag(id: "expanded_user_profile")
// String properties
let stringProperty: String? = featureFlag.stringProperty(key: "color")
// Boolean properties
let booleanProperty: Bool? = featureFlag.boolProperty(key: "expanded")
// Number properties
let numberProperty: Double? = featureFlag.numberProperty(key: "height")
// returns the property as a TimeInterval in milliseconds
let timestampProperty : Int? = featureFlag.timestampProperty(key: "account_start")
// returns the property as a string of the image URL 
let imageProperty : String? = featureFlag.imageProperty(key: "homepage_icon")
// returns the property as a [String: Any] dictionary
let jsonObjectProperty : [String: Any]? = featureFlag.jsonObjectProperty(key: "footer_settings")
```

{% endtab %}
{% tab Java %}

```java
// Feature flag instance
FeatureFlag featureFlag = braze.getFeatureFlag("expanded_user_profile");
// String properties
String stringProperty = featureFlag.getStringProperty("color");
// Boolean properties
Boolean booleanProperty = featureFlag.getBooleanProperty("expanded");
// Number properties
Number numberProperty = featureFlag.getNumberProperty("height");
// returns the property as a nullable long in milliseconds
Long timestampProperty = featureFlag.getTimestampProperty("account_start");
// returns the property as a string of the image URL 
String imageProperty = featureFlag.getImageProperty("homepage_icon");
// returns the property as a JSON Object
JSONObject jsonObjectProperty = featureFlag.getJSONProperty("footer_settings");
```

{% endtab %}
{% tab Kotlin %}

```kotlin
// feature flag instance
val featureFlag = braze.getFeatureFlag("expanded_user_profile")
// string properties
val stringProperty: String? = featureFlag.getStringProperty("color")
// boolean properties
val booleanProperty: Boolean? = featureFlag.getBooleanProperty("expanded")
// number properties
val numberProperty: Number? = featureFlag.getNumberProperty("height")
// returns the property as a nullable long in milliseconds
val timestampProperty: Long? = featureFlag.getTimestampProperty("account_start")
// returns the property as a string of the image URL 
val String imageProperty: String?  = featureFlag.getImageProperty("homepage_icon")
// returns the property as a JSON Object
val jsonObjectProperty: JSONObject? = featureFlag.getJSONProperty("footer_settings")
```

{% endtab %}
{% tab React Native %}

```javascript
// String properties
const stringProperty = await Braze.getFeatureFlagStringProperty("expanded_user_profile", "color");
// Boolean properties
const booleanProperty = await Braze.getFeatureFlagBooleanProperty("expanded_user_profile", "expanded");
// Number properties
const numberProperty = await Braze.getFeatureFlagNumberProperty("expanded_user_profile", "height");
```

{% endtab %}
{% tab Unity %}

```csharp
// Feature flag instance
var featureFlag = Appboy.AppboyBinding.GetFeatureFlag("expanded_user_profile");
// String properties
var stringProperty = featureFlag.getStringProperty("color");
// Boolean properties
var booleanProperty = featureFlag.getBooleanProperty("expanded");
// Number property as integer
var integerProperty = featureFlag.getIntegerProperty("height");
// Number property as double
var doubleProperty = featureFlag.getDoubleProperty("height");
```
{% endtab %}
{% tab Cordova %}

```javascript
// String properties
const stringProperty = await BrazePlugin.getFeatureFlagStringProperty("expanded_user_profile", "color");
// Boolean properties
const booleanProperty = await BrazePlugin.getFeatureFlagBooleanProperty("expanded_user_profile", "expanded");
// Number properties
const numberProperty = await BrazePlugin.getFeatureFlagNumberProperty("expanded_user_profile", "height");
```
{% endtab %}
{% tab Flutter %}
```dart
BrazeFeatureFlag featureFlag = await braze.getFeatureFlagByID("expanded_user_profile");
// String properties
var stringProperty = featureFlag.getStringProperty("color");
// Boolean properties
var booleanProperty = featureFlag.getBooleanProperty("expanded");
// Number properties
var numberProperty = featureFlag.getNumberProperty("height");
```
{% endtab %}
{% tab Roku %}
```brightscript
' String properties
color = featureFlag.getStringProperty("color")
' Boolean properties
expanded = featureFlag.getBooleanProperty("expanded")
' Number properties
height = featureFlag.getNumberProperty("height")
```
{% endtab %}
{% endtabs %}

### Getting a list of all feature flags {#get-list-of-flags}

{% tabs %}
{% tab JavaScript %}

```javascript
const features = getAllFeatureFlags();
for(const feature of features) {
  console.log(`Feature: ${feature.id}`, feature.enabled);
}
```

{% endtab %}
{% tab Swift %}

```swift
let features = braze.featureFlags.featureFlags
for let feature in features {
  print("Feature: \(feature.id)", feature.enabled)
}
```

{% endtab %}
{% tab Java %}

```java
List<FeatureFlag> features = braze.getAllFeatureFlags();
for (FeatureFlag feature: features) {
  Log.i(TAG, "Feature: ", feature.getId(), feature.getEnabled());
}
```

{% endtab %}
{% tab Kotlin %}

```kotlin
val featureFlags = braze.getAllFeatureFlags()
featureFlags.forEach { feature ->
  Log.i(TAG, "Feature: ${feature.id} ${feature.enabled}")
}
```

{% endtab %}
{% tab React Native %}

```javascript
const features = await Braze.getAllFeatureFlags();
for(const feature of features) {
  console.log(`Feature: ${feature.id}`, feature.enabled);
}
```

{% endtab %}
{% tab Unity %}

```csharp
List<FeatureFlag> features = Appboy.AppboyBinding.GetAllFeatureFlags();
foreach (FeatureFlag feature in features) {
  Console.WriteLine("Feature: {0} - enabled: {1}", feature.ID, feature.Enabled);
}
```

{% endtab %}
{% tab Cordova %}
```javascript
const features = await BrazePlugin.getAllFeatureFlags();
for(const feature of features) {
  console.log(`Feature: ${feature.id}`, feature.enabled);
}
```
{% endtab %}
{% tab Flutter %}
```dart
List<BrazeFeatureFlag> featureFlags = await braze.getAllFeatureFlags();
featureFlags.forEach((feature) {
  print("Feature: ${feature.id} ${feature.enabled}");
});
```
{% endtab %}
{% tab Roku %}
```brightscript
features = m.braze.getAllFeatureFlags()
for each feature in features
      print "Feature: " + feature.id + " enabled: " + feature.enabled.toStr()
end for
```
{% endtab %}
{% endtabs %}

### Refreshing feature flags {#refreshing}

You can refresh the current user's feature flags mid-session to pull the latest values from Braze.

{% alert tip %}
Refreshing happens automatically upon session start. Refreshing is only needed prior to important user actions, such as before loading a checkout page, or if you know a feature flag will be referenced.
{% endalert %}

{% tabs %}
{% tab JavaScript %}

```javascript
braze.refreshFeatureFlags(() => {
  console.log(`Feature flags have been refreshed.`);
}, () => {
  console.log(`Failed to refresh feature flags.`);
});
```

{% endtab %}
{% tab Swift %}

```swift
braze.featureFlags.requestRefresh { result in
  switch result {
  case .success(let features):
    print("Feature flags have been refreshed:", features)
  case .failure(let error):
    print("Failed to refresh feature flags:", error)
  }
}
```

{% endtab %}
{% tab Java %}

```java
braze.refreshFeatureFlags();
```

{% endtab %}
{% tab Kotlin %}

```kotlin
braze.refreshFeatureFlags()
```

{% endtab %}
{% tab React Native %}

```javascript
Braze.refreshFeatureFlags();
```

{% endtab %}
{% tab Unity %}

```csharp
Appboy.AppboyBinding.RefreshFeatureFlags();
```

{% endtab %}
{% tab Cordova %}
```javascript
BrazePlugin.refreshFeatureFlags();
```
{% endtab %}
{% tab Flutter %}
```dart
braze.refreshFeatureFlags();
```
{% endtab %}
{% tab Roku %}
```brightscript
m.Braze.refreshFeatureFlags()
```
{% endtab %}
{% endtabs %}

### Listening for changes {#updates}

You can configure the Braze SDK to listen and update your app when the SDK refreshes any feature flags.

This is useful if you want to update your app if a user is no longer eligible for a feature. For example, setting some state in your app based on whether or not a feature is enabled, or one of its property values.

{% tabs %}
{% tab JavaScript %}

```javascript
// Register an event listener
const subscriptionId = braze.subscribeToFeatureFlagsUpdates((features) => {
  console.log(`Features were updated`, features);
});
// Unregister this event listener
braze.removeSubscription(subscriptionId);
```

{% endtab %}
{% tab Swift %}

```swift
// Create the feature flags subscription
// - You must keep a strong reference to the subscription to keep it active
let subscription = braze.featureFlags.subscribeToUpdates { features in
  print("Feature flags were updated:", features)
}
// Cancel the subscription
subscription.cancel()
```

{% endtab %}
{% tab Java %}

```java
braze.subscribeToFeatureFlagsUpdates(event -> {
  Log.i(TAG, "Feature flags were updated.");
  for (FeatureFlag feature: event.getFeatureFlags()) {
    Log.i(TAG, "Feature: ", feature.getId(), feature.getEnabled());
  }
});
```

{% endtab %}
{% tab Kotlin %}

```kotlin
braze.subscribeToFeatureFlagsUpdates() { event ->
  Log.i(TAG, "Feature flags were updated.")
  event.featureFlags.forEach { feature ->
    Log.i(TAG, "Feature: ${feature.id}")
  }
}
```

{% endtab %}
{% tab React Native %}

```javascript
// Register an event listener
Braze.addListener(braze.Events.FEATURE_FLAGS_UPDATED, (featureFlags) => {
  console.log(`featureFlagUpdates`, JSON.stringify(featureFlags));
});
```

{% endtab %}
{% tab Unity %}

To listen for changes, set the values for **Game Object Name** and **Callback Method Name** under **Braze Configuration** > **Feature Flags** to the corresponding values in your application.

{% endtab %}
{% tab Cordova %}
```javascript
// Register an event listener
BrazePlugin.subscribeToFeatureFlagUpdates((featureFlags) => {
    console.log(`featureFlagUpdates`, JSON.stringify(featureFlags));
});
```
{% endtab %}
{% tab Flutter %}

In the Dart code in your app, use the following sample code:

```dart
// Create stream subscription
StreamSubscription featureFlagsStreamSubscription;

featureFlagsStreamSubscription = braze.subscribeToFeatureFlags((featureFlags) {
  print("Feature flags were updated");
});

// Cancel stream subscription
featureFlagsStreamSubscription.cancel();
```

Then, make these changes in the iOS native layer as well. Note that there are no additional steps needed on the Android layer.

1. Implement `featureFlags.subscribeToUpdates` to subscribe to feature flag updates as described in the [subscribeToUpdates](https://braze-inc.github.io/braze-swift-sdk/documentation/brazekit/braze/featureflags-swift.class/subscribetoupdates(_:)) documentation.

2. Your `featureFlags.subscribeToUpdates` callback implementation must call `BrazePlugin.processFeatureFlags(featureFlags)`.

For an example, see [AppDelegate.swift](https://github.com/braze-inc/braze-flutter-sdk/blob/master/example/ios/Runner/AppDelegate.swift) in our sample app.

{% endtab %}
{% tab Roku %}
```brightscript
' Define a function called `onFeatureFlagChanges` to be called when feature flags are refreshed
m.BrazeTask.ObserveField("BrazeFeatureFlags", "onFeatureFlagChanges")
```
{% endtab %}

{% tab React Hook %}
```typescript
import { useEffect, useState } from "react";
import {
  FeatureFlag,
  getFeatureFlag,
  removeSubscription,
  subscribeToFeatureFlagsUpdates,
} from "@braze/web-sdk";

export const useFeatureFlag = (id: string): FeatureFlag => {
  const [featureFlag, setFeatureFlag] = useState<FeatureFlag>(
    getFeatureFlag(id)
  );

  useEffect(() => {
    const listener = subscribeToFeatureFlagsUpdates(() => {
      setFeatureFlag(getFeatureFlag(id));
    });
    return () => {
      removeSubscription(listener);
    };
  }, [id]);

  return featureFlag;
};
```
{% endtab %}
{% endtabs %}

## Viewing the changelog

To view a feature flag's changelog, open a feature flag and select **Changelog**.

![A feature flag's "Edit" page, with the "Changelog" button highlighted.]({% image_buster /assets/img/feature_flags/changelog/open_changelog.png %}){: style="max-width:60%;"}

Here, you can review when a changed happened, who made the change, which category it belongs to, and more.

![The changelog of the selected feature flag.]({% image_buster /assets/img/feature_flags/changelog/changelog.png %}){: style="max-width:90%;"}

## Segmenting with feature flags {#segmentation}

{% alert note %}
Feature Flag membership filter is being gradually rolled out and may not be on your dashboard just yet.
{% endalert %}

Braze automatically keeps track of which users are currently eligible for or participating in a feature flag. You can create a segment or target messaging using the [**Feature Flag** filter][6]. For more information about filtering on segments, see [Creating a segment][7].

{% alert note %}
To prevent recursive segments, it is not possible to create a segment that references other feature flags.
{% endalert %}

## Best practices

### Don't combine rollouts with Canvases or experiments

To avoid users being enabled and disabled by different entry points, you should either set the rollouts slider to a value greater than zero OR enable the feature flag in a Canvas or experiment. As a best practice, if you plan to use a feature flag in a Canvas or experiment, keep the rollout percentage at zero.

### Naming conventions

To keep your code clear and consistent, consider using the following format when naming your feature flag ID:

```plaintext
BEHAVIOR_PRODUCT_FEATURE
```

Replace the following:

| Placeholder | Description                                                                                                               |
|-------------|---------------------------------------------------------------------------------------------------------------------------|
| `BEHAVIOR`  | The behavior of the feature. In your code, be sure the behavior is disabled by default and avoid using phrases like `disabled` in the feature flag name. |
| `PRODUCT`   | The product the feature belongs to.                                                                                       |
| `FEATURE`    | The name of the feature.                                                                                                  |
{: .reset-td-br-1 .reset-td-br-2 .reset-td-br-3}

Here's an example feature flag where `show` is the behavior, `animation_profile` is the product, and `driver` is the feature:

```plaintext
show_animation_profile_driver
```

### Planning ahead

Always play it safe. When considering new features that may require an off switch, it's better to release new code with a feature flag and not need it than it is to realize a new app update is required.

### Be descriptive

Add a description to your feature flag. While this is an optional field in Braze, it can help answer questions others may have when browsing available feature flags.

- Contact details for who is responsible for the enablement and behavior of this flag
- When this flag should be disable
- Links to documentation or notes about the new feature this flag controls
- Any dependencies or notes on how to use the feature

### Clean up old feature flags

We're all guilty of leaving features on at 100% rollout for longer than necessary.

To help keep your code (and Braze dashboard) clean, remove permanent feature flags from your code base after all users have upgraded and you no longer need the option to disable the feature. This helps reduce the complexity of your development environment, but also keeps your list of feature flags tidy.

[1]: {% image_buster /assets/img/feature_flags/feature-flags-list.png %} 
[2]: {% image_buster /assets/img/feature_flags/feature-flags-create.png %}
[3]: {% image_buster /assets/img/feature_flags/feature-flags-targeting.png %}
[4]: {% image_buster /assets/img/feature_flags/feature-flags-rollout.png %}
[5]: {{site.baseurl}}/developer_guide/platform_wide/feature_flags/about/
[6]: {{site.baseurl}}/user_guide/engagement_tools/segments/segmentation_filters#feature-flag
[7]: {{site.baseurl}}/user_guide/engagement_tools/segments/creating_a_segment/
[8]: {% image_buster /assets/img/feature_flags/feature-flags-manage-permission.png %}
[9]: {{site.baseurl}}/user_guide/administrative/app_settings/manage_your_braze_users/user_permissions/
