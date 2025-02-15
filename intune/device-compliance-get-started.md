---
# required metadata

title: Device compliance policies in Microsoft Intune - Azure | Microsoft Docs
description: Get started with use device compliance policies, overview of status and severity levels, using the InGracePeriod status, working with Conditional Access, handling devices without an assigned policy, and the differences in compliance in the Azure portal and classic portal in Microsoft Intune
keywords:
author: MandiOhlinger
ms.author: mandia
manager: dougeby
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: microsoft-intune
ms.localizationpriority: high
ms.technology:
ms.reviewer: joglocke

# optional metadata

#ROBOTS:
#audience:
#ms.devlang:
ms.suite: ems
search.appverid: MET150
#ms.tgt_pltfrm:
ms.custom: intune-azure
ms.collection: M365-identity-device-management
---

# Set rules on devices to allow access to resources in your organization using Intune

[!INCLUDE [azure_portal](./includes/azure_portal.md)]

Many mobile device management (MDM) solutions help protect organizational data by requiring users and devices to meet some requirements. In Intune, this feature is called "compliance policies". Compliance policies define the rules and settings that users and devices must meet to be compliant. When combined with Conditional Access, administrators can block users and devices that don't meet the rules.

For example, an Intune administrator can require:

- End users use a password to access organizational data on mobile devices
- The device isn't jail-broken or rooted
- A minimum or maximum operating system version on the device
- The device to be at, or under a threat level

You can also use this feature to monitor the compliance status on devices in your organization.

> [!IMPORTANT]
> Intune follows the device check-in schedule for all compliance evaluations on the device. [Policy and profile refresh cycles](device-profile-troubleshoot.md#how-long-does-it-take-for-devices-to-get-a-policy-profile-or-app-after-they-are-assigned) lists the estimated refresh times.

<!---### Actions for noncompliance

You can specify what needs to happen when a device is determined as noncompliant. This can be a sequence of actions during a specific time.
When you specify these actions, Intune will automatically initiate them in the sequence you specify. See the following example of a sequence of
actions for a device that continues to be in the noncompliant status for
a week:

- When the device is first determined to be noncompliant, an email with noncompliant notification is sent to the user.

- 3 days after initial noncompliance state, a follow up reminder is sent to the user.

- 5 days after initial noncompliance state, a final reminder with a notification that access to company resources will be blocked on the device in 2 days if the compliance issues are not remediated is sent to the user.

- 7 days after initial noncompliance state, access to company resources is blocked. This requires that you have Conditional Access policy that specifies that access from noncompliant devices should    be blocked for services such as Exchange and SharePoint.

### Grace Period

This is the time between when a device is first determined as
noncompliant to when access to company resources on that device is blocked. This time allows for time that the user has to resolve
compliance issues on the device. You can also use this time to create your action sequences to send notifications to the user before their access is blocked.

Remember that you need to implement Conditional Access policies in addition to compliance policies in order for access to company resources to be blocked.--->

## Device compliance policies work with Azure AD

Intune uses Azure Active Directory (AD) [Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) (opens another docs web site) to help enforce compliance. When a device enrolls in Intune, the Azure AD registration process starts, and device information is updated in Azure AD. One key piece of information is the device compliance status. This compliance status is used by Conditional Access policies to block or allow access to e-mail and other organization resources.

- [What is device management in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/device-management-introduction) is a great resource on why and how devices are registered in Azure AD.

- [Conditional Access](conditional-access.md) and [common ways to use Conditional Access](conditional-access-intune-common-ways-use.md) describe this feature as it relates to Intune.

## Ways to use device compliance policies

#### With Conditional Access

For devices that comply to policy rules, you can give those devices access to email and other organization resources. If the devices don't comply to policy rules, then they don't get access to organization resources. This is Conditional Access.

#### Without Conditional Access

You can also use device compliance policies without any Conditional Access. When you use compliance policies independently, the targeted devices are evaluated and reported with their compliance status. For example, you can get a report on how many devices aren't encrypted, or which devices are jail-broken or rooted. When you use compliance policies without Conditional Access, there aren't any access restrictions to organization resources.

## Ways to deploy device compliance policies

You can deploy compliance policy to users in user groups or devices in device groups. When a compliance policy is deployed to a user, all of the user's devices are checked for compliance. On Windows 10 version 1803 and newer devices, it's recommended to deploy to device groups *if* the primary user didn't enroll the device. Using device groups in this scenario helps with compliance reporting.

Intune also includes a set of built-in compliance policy settings. The following built-in policies get evaluated on all devices enrolled in Intune:

- **Mark devices with no compliance policy assigned as**: This property has two values:

  - **Compliant**: security feature off
  - **Not compliant** (default): security feature on

  If a device doesn't have a compliance policy assigned, then this device is considered not compliant. By default, devices are marked as **Not compliant**. If you use Conditional Access, we recommended you change the setting to **Not compliant**. If an end user isn't compliant because a policy isn't assigned, then the [Company Portal app](company-portal-app.md) shows `No compliance policies have been assigned`.

- **Enhanced jailbreak detection**: When enabled, this setting causes iOS devices to check in with Intune more frequently. Enabling this property uses the device’s location services, and impacts battery usage. The user location data isn't stored by Intune.

  Enabling this setting requires devices to:
  - Enable location services at the OS level.
  - Allow the company portal to use location services.
  - Evaluate and report its jailbreak status to Intune at least once every 72 hours. Otherwise, the device is marked not compliant. Evaluation is triggered by opening the Company Portal app or physically moving the device 500 meters or more. If the device doesn't move 500 meters in 72 hours, the user needs to open the Company Portal app for enhanced jail break evaluation.

- **Compliance status validity period (days)**: Enter the time period that devices report the status for all received compliance policies. Devices that don't return the status within this time period are treated as noncompliant. The default value is 30 days.

You can use these built-in policies to monitor these settings. Intune also [refreshes or checks for updates](create-compliance-policy.md#refresh-cycle-times) at different intervals, depending on the device platform. [Common questions, issues, and resolutions with device policies and profiles in Microsoft Intune](device-profile-troubleshoot.md) is a good resource.

Compliance reports are a great way to check the status of devices. [Monitor compliance policies](compliance-policy-monitor.md) includes some guidance.

## Non-compliance and Conditional Access on the different platforms

The following table describes how noncompliant settings are managed when a compliance policy is used with a Conditional Access policy.

---------------------------

|**Policy setting**| **Platform** |
| --- | ----|
| **PIN or password configuration** | - **Android 4.0 and later**: Quarantined</br>- **Samsung Knox Standard 4.0 and later**: Quarantined</br>- **Android Enterprise**: Quarantined</br></br>- **iOS 8.0 and later**: Remediated</br>- **macOS 10.11 and later**: Remediated</br></br>- **Windows 8.1 and later**: Remediated</br>- **Windows Phone 8.1 and later**: Remediated|
| **Device encryption** | - **Android 4.0 and later**: Quarantined</br>- **Samsung Knox Standard 4.0 and later**: Quarantined</br>- **Android Enterprise**: Quarantined</br></br>- **iOS 8.0 and later**: Remediated (by setting PIN)</br>- **macOS 10.11 and later**: Remediated (by setting PIN)</br></br>- **Windows 8.1 and later**: Not applicable</br>- **Windows Phone 8.1 and later**: Remediated |
| **Jailbroken or rooted device** | - **Android 4.0 and later**: Quarantined (not a setting)</br>- **Samsung Knox Standard 4.0 and later**: Quarantined (not a setting)</br>- **Android Enterprise**: Quarantined (not a setting)</br></br>- **iOS 8.0 and later**: Quarantined (not a setting)</br>- **macOS 10.11 and later**: Not applicable</br></br>- **Windows 8.1 and later**: Not applicable</br>- **Windows Phone 8.1 and later**: Not applicable |
| **Email profile** | - **Android 4.0 and later**: Not applicable</br>- **Samsung Knox Standard 4.0 and later**: Not applicable</br>- **Android Enterprise**: Not applicable</br></br>- **iOS 8.0 and later**: Quarantined</br>- **macOS 10.11 and later**: Quarantined</br></br>- **Windows 8.1 and later**: Not applicable</br>- **Windows Phone 8.1 and later**: Not applicable |
| **Minimum OS version** | - **Android 4.0 and later**: Quarantined</br>- **Samsung Knox Standard 4.0 and later**: Quarantined</br>- **Android Enterprise**: Quarantined</br></br>- **iOS 8.0 and later**: Quarantined</br>- **macOS 10.11 and later**: Quarantined</br></br>- **Windows 8.1 and later**: Quarantined</br>- **Windows Phone 8.1 and later**: Quarantined |
| **Maximum OS version** | - **Android 4.0 and later**: Quarantined</br>- **Samsung Knox Standard 4.0 and later**: Quarantined</br>- **Android Enterprise**: Quarantined</br></br>- **iOS 8.0 and later**: Quarantined</br>- **macOS 10.11 and later**: Quarantined</br></br>- **Windows 8.1 and later**: Quarantined</br>- **Windows Phone 8.1 and later**: Quarantined |
| **Windows health attestation** | - **Android 4.0 and later**: Not applicable</br>- **Samsung Knox Standard 4.0 and later**: Not applicable</br>- **Android Enterprise**: Not applicable</br></br>- **iOS 8.0 and later**: Not applicable</br>- **macOS 10.11 and later**: Not applicable</br></br>- **Windows 10 and Windows 10 Mobile**: Quarantined</br>- **Windows 8.1 and later**: Quarantined</br>- **Windows Phone 8.1 and later**: Not applicable |

---------------------------

**Remediated**: The device operating system enforces compliance. For example, the user is forced to set a PIN.

**Quarantined**: The device operating system doesn't enforce compliance. For example, Android and Android Enterprise devices don't force the user to encrypt the device. When the device isn't compliant, the following actions take place:

  - If a Conditional Access policy applies to the user, the device is blocked.
  - The Company Portal app notifies the user about any compliance problems.

## Azure classic portal vs. Azure portal

The main difference when using device compliance policies in the Azure portal:

- In the Azure portal, the compliance policies are created separately for each supported platform
- In the Azure classic portal, one device compliance policy is common to all supported platforms

<!--- - In the Azure portal, you have the ability to specify actions and notifications that are initiated when a device is determined to be noncompliant. This ability does not exist in the Intune admin console.

- In the Azure portal, you can set a grace period to allow time for the end-user to get their device back to compliance status before they completely lose the ability to get company data on their device. This is not available in the Intune admin console.--->

Device compliance policies created in the [classic portal](https://manage.microsoft.com) don't appear in the [Azure portal](https://portal.azure.com). However, they’re still targeted to users and manageable using the classic portal.

To use the device compliance-related features in the Azure portal, you must create new device compliance policies in the Azure portal. If you assign a device compliance policy in the Azure portal to a user who is also assigned a device compliance policy from the classic portal, then the device compliance policies from the Azure portal take precedence over the policies created in the classic portal.

## Next steps

- [Create a policy](create-compliance-policy.md) and view the prerequisites.
- See the compliance settings for the different device platforms:

  - [Android](compliance-policy-create-android.md)
  - [Android Enterprise](compliance-policy-create-android-for-work.md)
  - [iOS](compliance-policy-create-ios.md)
  - [macOS](compliance-policy-create-mac-os.md)
  - [Windows 10 and later](compliance-policy-create-windows.md)
  - [Windows Holographic for Business](compliance-policy-create-windows.md#windows-holographic-for-business)
  - [Windows 8.1 and Windows Phone 8.1](compliance-policy-create-windows-8-1.md)

- [Reference for policy entities](reports-ref-policy.md) has information about the Intune Data Warehouse policy entities.
