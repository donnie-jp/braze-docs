---
nav_title: iOS 14 Upgrade Guide
page_order: 10
platform: iOS
---

# iOS 14 SDK Upgrade Guide

This guide describes Braze-related changes introduced in iOS 14 and the required upgrade steps for your Braze iOS SDK integration.

For a complete list of new iOS 14 updates announced this year at WWDC, see Apple's [iOS 14 Preview](https://www.apple.com/ios/ios-14-preview/).

#### Summary of iOS 14 breaking changes

- Geofences are [no longer supported by iOS][4] for users who choose the new  _approximate location_ permission.
- Customers using the "Last Known Location" targeting features are required to upgrade their Braze iOS SDK to at least v3.26.1 for compatibility with _approximate location_ permission.
- IDFA collection now requires a permission prompt. Failure to update your IDFA collection code will result in a blank value as if a user declined to provide this permission.
- Apps targeting iOS 14 / Xcode 12 for beta releases can use our [iOS 14 Beta release][1], and our official iOS 14 release after Apple's "Golden Master" release.
- ~~Based on our testing, Apple may no longer display the provisional authorization dialog, and will instead set users as "authorized" as soon as the app requests provisional authorization. [Learn More](#push-provisional-auth)~~ This unexpected behavior was reverted in iOS Beta 5 (August 18) and no longer impacts iOS 14.

## Upgrade Summary

There are three features, which if you use, will require an upgrade to your Braze iOS SDK:

#### Location Targeting

Customers who are using the _Most Recent Location_ targeting features must upgrade to at least iOS SDK v3.26.1 which allows the new _Approximate Location_ feature to be collected on iOS 14 devices.

Even though your app might not target iOS 14, your end users may upgrade to iOS 14 and begin to use the new location accuracy option. Apps that do not upgrade to iOS SDK v3.26.1+ will not be able to reliably collect location attributes when users provide their _approximate location_  on iOS 14 devices.

#### IDFA permissions

Once users upgrade to iOS 14, all apps must use the new iOS 14 APIs from Xcode 12 to prompt users for IDFA ad tracking permission. If your app does not upgrade to use XCode 12 and the new `AppTrackingTransparency` framework, all IDFA values will be blank (`00000000-0000-0000-0000-000000000000`), similar to a user declining this new prompt.

#### iOS 14 + Xcode 12

We have released [Beta versions][1] of our upcoming iOS 14 / Xcode 12 compatible SDK. Customers who release Beta versions of their iOS apps with iOS 14 support are encouraged to use Braze's iOS 14 Beta SDK version which includes Xcode 12 compatibility and support for new iOS 14 APIs. 

We will continue to release updates and fixes to this beta as Apple continues to release newer versions of their iOS 14 beta. If you experience any issues or questions related to our iOS 14 compatibility or beta release, please open a new [Github Issue][2].

Our official iOS 14 compatible SDK will be released shortly after Apple's final iOS 14 beta is released, known as the "Golden Master" release.

## iOS 14 Behavior Changes

### Approximate Location Permission

![Precise Location]({% image_buster /assets/img/ios/ios14-approximate-location.png %}){: style="float:right;max-width:45%;margin-left:15px;"}

#### Overview

When requesting location permission, users will now have a choice to provide their _precise location_ (previous behavior), or the new _approximate location_. Approximate location will return a larger radius in which the user is located, instead of their exact coordinates.

#### Geofences {#geofences}

Geofences are [no longer supported by iOS][4] for users who choose the new  _approximate location_ permission. While no updates are required for your Braze SDK integration, you may need to adjust your [location-based marketing strategy](https://www.braze.com/blog/geofencing-geo-targeting-beaconing-when-to-use/) for campaigns that rely on geofences.

#### Location Targeting {#location-tracking}

To continue to collect users' _last known location_ when _approximate location_ is granted, your app will need to upgrade to at least v3.26.1 of the Braze iOS SDK. Keep in mind that the location will be less precise, and based on our testing has been upwards of 12,000 meters (7+ miles). When using the _last known location_ targeting options in the Braze Dashboard, be sure to increase the location's radius to account for new _approximate locations_ (we recommend at least a 1 mile/1.6km radius).

Apps that do not upgrade their Braze iOS SDK to at least v3.26.1 will no longer be able to use location tracking when _approximate location_ is granted on iOS 14 devices.

Users who have already granted location access will continue to provide _precise location_ after upgrading.

For more information on Approximate Location, see Apple's [What's New In Location](https://developer.apple.com/videos/play/wwdc2020/10660/) WWDC Video.

### IDFA and App Tracking Transparency {#idfa}

#### Overview

IDFA (Identity for Advertisers) is an identifier provided by Apple used by advertising and attribution partners for cross-device tracking and is tied to an individual's Apple ID.

Beginning in iOS 14, a new permission prompt (launched by the new `AppTrackingTransparency` framework) will require explicit user consent to access the IDFA. This permission to "track you across apps and websites owned by other companies" will be requested similarly to how you’d prompt users to request their location.

If a user does not accept the prompt, or if you do not upgrade to Xcode 12's `AppTrackingTransparency` framework, then a blank IDFA value (`00000000-0000-0000-0000-000000000000`) will be returned, and your app will not be allowed to prompt the user again.

{% alert important %}
These IDFA updates take effect once end-users upgrade their device to iOS 14. Please ensure your app is using the new `AppTransparencyFramework` and Xcode 12 even if you do not explicitly target iOS 14 support.
{% endalert %}

#### Changes to Braze IDFA collection
![App Clip]({% image_buster /assets/img/ios/ios14-idfa.png %}){: style="float:right;max-width:25%;margin-left:15px;border:0"}

1. Braze will continue to allow apps to provide a user's IDFA value _to_ the Braze SDK

2. The `ABK_ENABLE_IDFA_COLLECTION` macro, which would conditionally compile in optional automatic IDFA collection, will be removed in our iOS 14 release.

3. If your app has used IDFA or IDFV as your Braze External ID, we strongly recommend migrating away from these identifiers in favor of a UUID. For more information on migrating External IDs, see our new [External ID Migration API Endpoint](https://www.braze.com/docs/api/endpoints/user_data/external_id_migration/).

Read more from Apple about their [Privacy Updates](https://developer.apple.com/app-store/user-privacy-and-data-use/) and the new [App Tracking Transparency framework](https://developer.apple.com/documentation/apptrackingtransparency).

### Push Authorization {#push-provisional-auth}

{% alert important %}
In the first 4 beta versions of iOS 14, the Provisional Push authorization status was removed.

As of iOS Beta 5 (August 18) this behavior reverted back to its previous iOS 13 behavior, so no strategy or app changes will be necessary with regards to iOS push authorization.
{% endalert %}

## iOS 14 New Features

### App Privacy and Data Collection Overview {#app-privacy}

The App Store's new App Privacy feature will disclose to users what personal information an app collects and how it may track users across other apps and websites. Apple has [stated](https://www.apple.com/ios/ios-14-preview/), "Privacy information on the App Store will be coming in an iOS 14 update later this year".

Braze will continue to monitor this new feature announcement to help you understand how your usage of Braze may be disclosed in the App Privacy summary.

To learn more about this feature, see [Apple's Privacy and Data Use](https://developer.apple.com/app-store/user-privacy-and-data-use/).

### App Clips {#app-clips}

#### Overview

![App Clip]({% image_buster /assets/img/ios/ios14-app-clips.png %}){: style="float:right;max-width:45%;margin-left:15px;border:0"}

An _App clip_ is a small part of your app that can be quickly accessed without installation by visiting a URL or scanning a QR code.

This feature gives users quicker access to sample your app, with an opportunity to either upgrade to the full app experience or auto-delete after a period of inactivity.

To learn more about App Clips, see [Apple's App Clip Documentation](https://developer.apple.com/app-clips/)

#### Braze Support

For customers interested in using Braze within App Clips, please contact your Braze Success Team or Support Team.

We will be releasing support and documentation in the near future.


[1]: https://github.com/Appboy/appboy-ios-sdk/blob/ios14-beta/CHANGELOG.md
[2]: https://github.com/Appboy/appboy-ios-sdk/issues
[3]: {{site.baseurl}}/user_guide/engagement_tools/segments/segmentation_filters/#provisionally-authorized-on-ios
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager/3600215-accuracyauthorization
