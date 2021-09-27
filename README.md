# FlyBuy SDK for Xamarin.Android

### Nuget

The FlyBuy SDK is published on nuget.org at the following location: https://www.nuget.org/packages/Radiusnetworks.Flybuy.Android

### Add FlyBuySDK to your Xamarin.Android project

Select _Packages_ in your VS project and go to _Manage NuGet Packages..._. Search for `Radiusnetworks.Flybuy.IOS` and click `Add Package`. The latest SDK will be added to your project and can be referenced with the following:
```
using Flybuy;
using Flybuy.Data;
```

## Creating Android bindings library in Xamarin for FlyBuy

1. Download the latest native SDK release from https://github.com/RadiusNetworks/flybuy-android/releases.

1. Unzip the archive and copy the `aar` files for each module to their related `Jars` directories. E.G.
    ```
    flybuy-android-2.1.5/aar/pickup-release.aar --> FlyBuyPickup/Jars
    ```

1. The bindings project will now reference the new `aar` files and can be built.


