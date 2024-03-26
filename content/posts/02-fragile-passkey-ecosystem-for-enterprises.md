---
title: "Fragile Passkey Ecosystem for Enterprises"
date: 2024-03-11T23:30:00+01:00
draft: false
tags: ["FIDO", "Passkeys"]
author: ["Dennis Kniep", "Andreas Pellengahr"]
comment: true
---
## TL;DR
Companies rely solely on Passkeys for authentication. Issues and breaking changes within the Passkey ecosystem can prevent users from signing in.
We kindly request all contributors of the Passkey ecosystem to be mindful of your responsibilities and act carefully.

## More details

### Companies rely solely on Passkeys for authentication
Some global companies have progressed beyond the adoption phase and now rely solely on Passkeys. 
In fact, they have chosen to discard other conventional authentication methods (i.e. sms, authenticator apps, etc.) and allow only Passkeys for sign-in.

### Issues within the Passkey ecosystem can prevent users from signing in
To support the Passkey-only initiative, it is essential to ensure the uninterrupted functionality of the Passkey ecosystem. 
Any disruption (i.e. issues or breaking changes) within Passkey authentication can lead to user lockouts, as there are no alternative login methods available. Which effectively means they cannot work anymore.

### Please be mindful of your responsibilities and act carefully
The request goes out to all contributors within the Passkey ecosystem to exercise caution and avoid sudden changes that could potentially exclude users from their work.

### Practical Examples
Are these assumptions theoretical? No, they are actual happening. Here are some practical examples that demonstrate the impact of issues and changes within the passkey ecosystem on real organizations. It is not the intention to blame anyone or cause offense. Instead, the purpose is to raise awareness of potential issues that could disrupt existing passkey implementations.

**Overview:**
1. [Registered Passkey on mobile can not be used anymore](#registered-passkey-on-mobile-can-not-be-used-anymore)
1. [SecurityKey via NFC does not work anymore](#securitykey-via-nfc-does-not-work-anymore)
1. [Security Key or Passkey from different device can not be selected - due to stolen device protection](#security-key-or-passkey-from-different-device-can-not-be-selected---due-to-stolen-device-protection)
1. [Security Key can not be selected - due to transport selection](#security-key-can-not-be-selected---due-to-transport-selection)
1. [WebAuthN Conditional UI: Unable to choose different Passkey](#webauthn-conditional-ui-unable-to-choose-different-passkey)
1. [Frequently changing UIs and landing pages](#frequently-changing-uis-and-landing-pages)
1. [Technical error messages changed](#technical-error-messages-changed)

#### Registered Passkey on mobile can not be used anymore
With the iOS 16 update, Apple transitioned its approach from single-device passkeys to multi-device passkeys. Following this update, authentication using the previous single-device passkey became unavailable. As a consequence, users were required to create a new multi-device passkey from scratch, as the old one could no longer be used for authentication.

> ⚠ Affected users faced the need for account recovery. In some companies it includes identity verification and proved to be burdensome for them.

#### Security Key via NFC does not work anymore
Chrome on Android no longer provides the option to utilize an external Security Key via NFC if `userverification` is `required` by the webapplication ([see here](https://issues.chromium.org/issues/328710551)). As a result, certain users are unable to authenticate with their Security Key since this change was implemented.

> ⚠ Affected users can no longer authenticate i.e. Users which decided to solely use Security Keys, Clean-room devices without access to Face or Touch, etc.

[!["No NFC when userverification required"](/posts/img/02-fragile-passkey-ecosystem-for-enterprises/webauthN-Issue-android-no-nfc.png)](/posts/img/02-fragile-passkey-ecosystem-for-enterprises/webauthN-Issue-android-no-nfc.png)
{.post-preview-portrait-image}

#### Security Key or Passkey from different device can not be selected - due to stolen device protection
On iOS 17.4 Security Keys or Passkeys from different device can not be selected for authentication, if the new feature "stolen device protection" is enabled and you have a passkey on that device registered.
Then it demands TouchId when clicking on "Other Sign-in Options". After providing TouchId the dialog is closed and you cannot continue.

> ⚠ Affected users can no longer authenticate i.e use cases where a strong security level with an attested authentication via Security Key is necessary

[!["Touch ID Required when choosing Other Sign-in Options"](/posts/img/02-fragile-passkey-ecosystem-for-enterprises/webauthN-Issue-ios-17-4-stolen-device-protection.png)](/posts/img/02-fragile-passkey-ecosystem-for-enterprises/webauthN-Issue-ios-17-4-stolen-device-protection.png)
{.post-preview-portrait-image}

#### Security Key can not be selected - due to transport selection
In iOS 17.4, Security Keys cannot be selected for authentication if all potential transport values are configured in the `allowedcredentials.transport` property. Users can face authentication difficulties, as Security Keys cannot be selected for authentication anymore.

> ⚠ Affected users cannot authenticate anymore.

[!["No Security when all transports selected"](/posts/img/02-fragile-passkey-ecosystem-for-enterprises/webauthN-Issue-ios-17-4-transports-selected.png)](/posts/img/02-fragile-passkey-ecosystem-for-enterprises/webauthN-Issue-ios-17-4-transports-selected.png)
{.post-preview-portrait-image}

#### WebAuthN Conditional UI: Unable to choose different Passkey
In iOS 17.4, no other passkey is selectable during [WebAuthN Conditional UI](https://github.com/w3c/webauthn/wiki/Explainer%3A-WebAuthn-Conditional-UI#api-layer). 
It was possible before to select a different passkey than the proposed one (i.e. security key, synced passkey, etc.) by clicking on the key icon:

[!["webauthN-Issue-ChooseOtherPasskeyButton"](/posts/img/02-fragile-passkey-ecosystem-for-enterprises/webauthN-Issue-ChooseOtherPasskeyButton.png)](/posts/img/02-fragile-passkey-ecosystem-for-enterprises/webauthN-Issue-ChooseOtherPasskeyButton.png)
{.post-preview-portrait-image}


[!["webauthN-Issue-ChooseOtherPasskey"](/posts/img/02-fragile-passkey-ecosystem-for-enterprises/webauthN-Issue-ChooseOtherPasskey.png)](/posts/img/02-fragile-passkey-ecosystem-for-enterprises/webauthN-Issue-ChooseOtherPasskey.png)
{.post-preview-portrait-image}

That is no longer the case. There is no chance to select an other passkey than the proposed one.

[!["webauthN-Issue-ChooseOtherPasskey"](/posts/img/02-fragile-passkey-ecosystem-for-enterprises/webauthN-Issue-ChooseOtherPasskeyNotPossible.png)](/posts/img/02-fragile-passkey-ecosystem-for-enterprises/webauthN-Issue-ChooseOtherPasskeyNotPossible.png)
{.post-preview-portrait-image}

#### Frequently changing UIs and landing pages
Frequent changes to user interfaces can be confusing, particularly for non-tech-savvy individuals, leading to frustration and usability issues. Here are three examples illustrating this point:

1. In a recent Chrome update, the UI now immediately displays a QR code during the passkey authentication flow when no phones are paired. Despite the presence of a "Use a different device" button, users often begin scanning the QR code and then complain about the inability to register a security key or the passkey on the current device ([see here](https://issues.chromium.org/issues/40266801)).

2. Another instance occurred in Chrome for Windows, where an option was renamed from "winhello" to "security key." Users claim they cannot sign in anymore because the "winhello" option is missing (but just hidden behind the "security key" option) ([see here](https://issues.chromium.org/issues/41482364)).

3. Additionally, in Windows Chrome, both selection options during passkey authentication now contains "security key." This has led users to question which option is the correct one to choose, causing confusion ([see here](https://issues.chromium.org/issues/328698086)).

> ⚠ Although the authentication mechanism is technically feasible, users are struggling to locate the correct steps to successfully authenticate.


#### Technical error messages changed
In iOS 17.4, there was a modification to a specific error message that previously indicated the inability to utilize WebAuthN APIs within a WebView, despite the availability of the WebAuthN API object. Initially, the error message was `NotAllowedError: The 'navigator.credentials.get' API is only permitted in applications with the 'com.apple.developer.web-browser' entitlement`. Now, it has been changed to: `NotAllowedError: The request is not allowed by the user agent or the platform in the current context, possibly because the user denied permission`. However, the issue lies in the fact that the new error message is quite generic and could occur in various scenarios. For instance, it could be triggered when a user cancels the Passkey dialog displayed by either the OS or the Browser. As a result, in certain situations, it becomes challenging to differentiate between a user canceling the Passkey OS/Browser UI and the actual inability to use WebAuthN APIs in the WebView. Consequently, it becomes impossible to render a specific UI if Passkeys are not supported in these scenarios.

> ⚠ Affected users cannot be guided by the system. A sign in process that cannot even differenciate between a "cancel" or any other error leads to user frustration.

## Changelog

* **26.03.2024:** Added "WebAuthN Conditional UI: Unable to choose different Passkey" 