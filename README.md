# FlyBuy SDK for Xamarin.Android

## Setup

The FlyBuy SDK is published on nuget.org at the following location: https://www.nuget.org/packages/Radiusnetworks.Flybuy.Android

### Add FlyBuySDK to your Xamarin.Android project

Select `Packages` in your VS project and go to `Manage NuGet Packages...`. Search for `Radiusnetworks.Flybuy.Android` and click `Add Package`. The latest SDK will be added to your project and can be referenced with the following:

```csharp
using Flybuy;
using Flybuy.Data;
```

### Initialize SDK on Launch

The Andorid SDK is broken up into 3 modules:

 * pickup
 * presence
 * notify

Flybuy needs to be setup and configured at application launch. However, it does not run in the background or use device resources until there is an active order.

The easiest way to configure Flybuy in your app is to extend the `FlyBuyApplication` and call the `FlyBuyCore.configure(...)` method from `onCreate()` to setup the auth token. Also, any modules that are used need to be configured. The example below shows all modules, but make sure to only configure the modules needed.

```csharp
[Application]
public class MyApplication : FlyBuyApplication
{
    public MyApplication(IntPtr javaReference, Android.Runtime.JniHandleOwnership transfer) : base(javaReference, transfer)
    {
    }

    public override void OnCreate()
    {
        base.OnCreate();
        Core.Configure(this, appToken);
        ((FlyBuy.Pickup.PickupManager)FlyBuy.Pickup.PickupManager.Manager.GetInstance(null)).Configure(this);
        ((FlyBuy.Presence.PresenceManager)FlyBuy.Presence.PresenceManager.Manager.GetInstance(null)).Configure(this);
        ((FlyBuy.Notify.NotifyManager)FlyBuy.Notify.NotifyManager.Manager.GetInstance(null)).Configure(this);
    }
}
```

The modules also provide methods for updating permissions. The following example handles location permission updates.

```csharp
private const int FLYBUY_PERMISSIONS_REQUEST = 42;

private void RequestLocationPermissions() {
    var permissions = CheckForMissingLocationPermissions();
    if (permissions.Count > 0) {
        ActivityCompat.RequestPermissions(this, permissions.ToArray(), FLYBUY_PERMISSIONS_REQUEST);
    } else {
        ((FlyBuy.Pickup.PickupManager)FlyBuy.Pickup.PickupManager.Manager.GetInstance(null)).OnLocationPermissionChanged();
    }
}

public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Permission[] grantResults)
{
    if(requestCode == FLYBUY_PERMISSIONS_REQUEST) {
        ((FlyBuy.Pickup.PickupManager)FlyBuy.Pickup.PickupManager.Manager.GetInstance(null)).OnLocationPermissionChanged();
    }
}
```

## Components

The following section provides some examples C# syntax for the customer and order components. Refer to the [native documentation](https://www.radiusnetworks.com/developers/flybuy/#/sdk-2.0/customer) for complete details on the SDK components.

Use the following to determine if a customer is associated with the app:
```csharp
Customer currentCustomer = Core.customer.Current;
```

If no customer, then create the customer using:
```csharp
CustomerInfo customerInfo = new CustomerInfo(
        "Marty McFly",
        "DeLorean",
        "Silver",
        "OUTATIME",
        "555-555-5555"
);
Core.customer.Create(customerInfo, true, true, null, null, createCustomerCallback);
```

To claim an order use:
```csharp
Core.orders.Claim(redemptionCode, customerInfo, null, claimCallback);
```

To update an order use:
```csharp
Core.orders.UpdateCustomerState(currentOrder.Id, CustomerState.Completed, completeOrderCallback);
```

## Creating Android bindings library in Xamarin for FlyBuy

The following steps can be used to update the FlyBuy Andorid bindings:

1. Download the latest native SDK release from https://github.com/RadiusNetworks/flybuy-android/releases.

1. Unzip the archive and copy the `aar` files for each module to their related `Jars` directories. E.G.
    ```
    flybuy-android-2.1.5/aar/pickup-release.aar --> FlyBuyPickup/Jars
    ```

1. The bindings project will now reference the new `aar` files and can be built.
