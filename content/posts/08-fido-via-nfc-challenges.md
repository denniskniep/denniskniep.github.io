---
title: "FIDO via NFC challenges"
date: 2025-04-01T01:00:00+02:00
draft: false
tags: ["FIDO", "Passkeys"]
author: ["Dennis Kniep"]
comment: true
---

Imagine if one could simply use FIDO Security Keys or Security Cards on any device through a unified interface that works seamlessly. When examining pluggable interfaces, we find various types such as USB-A, USB-C, and Thunderbolt, which often limit cross-device usability or require adapters. Maybe devices don’t have a pluggable interface at all due to design decisions, security restrictions or something similar. In theory there is one interface that appears to be a promising solution: NFC. However, using FIDO via NFC presents several challenges. This blog post explores the challenges and issues, offering insights for users and developers navigating the complexities of FIDO authentication via NFC. But it should act also as motivation for contributors of the Passkey ecosystem to implement the missing pieces.

## No Discoverable Credentials via NFC on Android

One of the primary limitations of NFC on Android is its support for U2F only, which does not encompass the concept of Discoverable Credentials. This means that FIDO credentials created as discoverable are not compatible. As a consequence, a user who registered a Security Key or Security Card as a Discoverable Credential on another operating systems cannot use it on Android via NFC.

## No User Verification via NFC on Android

Another challenge is the lack of support for User Verification via NFC on Android. User Verification involves prompting for PIN, fingerprint or face recognition during FIDO authentication. This limitation means that the authentication flow can only request a single factor. You need to prompt for another factor, i.e. a password, which is what we want to eliminate. Furthermore, this limitation may introduce additional technical complexities, as developers must distinguish between different scenarios.

## Platform Passkeys Hide Roaming Authenticators on Android

On Android, registering a Platform Passkey prevents the use of a Security Key or Security Card via NFC or USB.
Without a button to switch authenticator type, users are limited in their ability to use stronger device-bound FIDO credentials. This limitation can hinder users from opting for more secure authentication methods when needed. It might be okay to use a synced platform passkey for non-critical apps, but in certain scenarios where higher assurance is necessary, a security key could be requested. However, it cannot be used if it's not selectable.

## Issues with Chrome Passphrase

When Chrome is configured to sync its profile with a [custom passphrase](https://support.google.com/chrome/answer/165139?hl=en&co=GENIE.Platform%3DAndroid#zippy=%2Cuse-your-chrome-history-to-personalize-google%
2Ccreate-your-own-passphrase), users may encounter a direct error message, "NotAllowed" in Chrome. This error occurs when starting FIDO authentication with "allowedCredentials" set (non-Discoverable). This error occurs without any prior notifications, disrupting the authentication process. Removing the custom passphrase resolves this issue. To do so, navigate to `Chrome > Settings > [Account Name] > Encryption`.

## Data Transfer Limitations on iOS NFC

When starting a FIDO Authentication, the "allowedCredentials" are sent to the authenticator. If you are using NFC, that data transfer needs to be done via the NFC channel. The size of the transferred data depends on the number of registered FIDO Credentials of that particular account. When User Verification is required and too much data needs to be transferred during FIDO authentication, iOS NFC becomes unresponsive. And it looks like for the User as if NFC is not working at all, meaning no popup, no haptic feedback and no reaction at all.

It's hard to say what is too much. However, looking at my test results, I can confirm that it is deterministic, even though it varies from model to model. One Security Card I tested did not work when two other FIDO credentials were also registered on the tested account. Another model start to experience that issue when six FIDO Credentials were registered on the used account.

This issue does not appear on Windows. On Android there is not even the possibility to use User Verification set to required (as discussed [here](#no-user-verification-via-nfc-on-android))

If User Verification is set to "discouraged", then there seems to be no easily reachable limit (Tested with a Security Key)

Interesting sidenote: Android and Windows have a maximum limit of 64 allowedCredentials. If you have more credentials, you receive the following error: "RangeError: The `allowCredentials` attribute exceeds the maximum allowed size (64)."
This is a very reasonable limitation.

## No Indicator of NFC Data Transfer Progress on iOS & Windows

As already discussed, the transferred data depends on the number of registered FIDO Credentials with that particular account. Naturally, the time required to transmit all data increases with the number of registered FIDO Credentials. On iOs and Windows there is no signal indicating the progress of data transmission. Without feedback, users might move the Security Key or Security Card (to find the antenna) or remove it, which interrupts the data transfer. There is no further error message, it looks like basically nothing happens. Even though the process started, it couldn't transfer all the data and complete successfully.

## Connect the Security Key before starting the authentication

Sometimes, if a Security Key or Security Card is not connected prior to starting FIDO authentication, users may encounter a 'NotReadableError,' which indicates an unknown issue with the credential manager. Ensuring the key is connected prior to the authentication prompt can mitigate this issue, but it requires awareness and preparation from the user.

## Add NFC to Workstations by Default

NFC is available on most mobile devices, such as Android or Apple phones. That's great, but workstations are often not shipped with NFC by default. Why? That's unfortunate because its not very expensive to include it and there is definitely a great use case out there for Security Cards which by design have no USB or other plug.
To make those cards usable in future, companies should definetly change their order process to include NFC readers in workstations. For the consumer market it would also be a great use case if we look at online banking! Using your bank card with your workstation to do authentication and payment confirmation with your FIDO enabled bank card using NFC...!
