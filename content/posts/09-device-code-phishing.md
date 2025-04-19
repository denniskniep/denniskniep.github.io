---
title: "Phishing despite FIDO, leveraging the device code flow"
date: 2025-04-03T20:00:00+02:00
draft: false
tags: ["FIDO", "Passkeys", "Security", "Phishing"]
author: ["Dennis Kniep"]
comment: true
---

If you use FIDO within your authentication process, then the assumption is that the user is resistant to phishing.
However, if you use FIDO in combination with OIDC/OAuth Device Code Flow, it turns out that the authentication procedure is no longer phishing resistant.

Because of the nature of the Device Code Flow, you are authenticating on a different device than the token is actually issued to. The FIDO mechanism cannot enforce phishing resistance on an out-of-band channel.

It's basically nothing new that Device Code Flow can be used for phishing. But in the non-FIDO era, it didn't matter if you were phished via Device Code Flow or via SMS, Push, or Voice Authentication. Now, when you are using exclusively FIDO as the sole authentication method and therefore [downgrade attacks](https://medium.com/@yudasm/bypassing-windows-hello-for-business-for-phishing-181f2271dc02#c32b) can not be utilized, it might be not so obvious that phishing resistance can be still undermined.

## Example with Azure Entra

Even though it is a general problem wherever this protocol flavor is used in combination with FIDO, I will describe it using Azure Entra as a concrete example.

Let's assume you use the FIDO authentication method in your tenant. You configured a Conditional Access Policy to enforce FIDO authentication for every app via [Authentication Strength Policies](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-authentication-strengths#how-multiple-conditional-access-authentication-strength-policies-are-evaluated-for-registering-security-info). You might assume that this configuration makes the authentication process phishing resistant?! But that's wrong! An attacker can exploit the Device Code Flow to phish the token, bypassing the phishing resistance.

## Reference for Deep Dive

I will not go into technical details of the Device Code Flow as there are other excellent blog posts that cover this topic in detail. For a deeper understanding, I recommend reading [Introducing a new phishing technique for compromising Office 365 accounts](https://aadinternals.com/post/phishing/)

## Normal Attack Model

I often see blogs describing the Device Code Flow phishing technique with certain limitations for the attackers. The assumed attack model typically follows these steps:

1. The attacker starts the Device Code Flow and receives a user-code
2. The attacker sends the code and the device-code-url to the victim
3. The victim opens the device-code-url and enters the user-code
4. The victim completes the authentication
5. The attacker is authenticated

The consequence of this attack model is that, once the code is generated, the attacker has only 10 minutes to convince the user to open the URL and enter the code. I agree that this would not be an easy task for an attacker.

## Sophisticated Attack Model

Let's look at a different attack model that eliminates the limitations of the one above:

1. The attacker sends a URL to the victim
2. The victim opens that URL
3. When the URL is opened, a headless browser is started, performing the following automated steps:
   - Starts the Device Code Flow with `<tenant>` and `<clientId>`
   - Opens the device-code webpage and enters the corresponding user-code
   - The device-code webpage forwards to the URL for interactive authentication
   - Returns the URL for interactive authentication as a redirect to the victim
4. The victim is redirected to the authentication URL
5. The victim completes the authentication
6. The attacker is authenticated

Using this attack model, it is just a URL that the user has to click. Since the Device Code Flow is only started when the user clicks on the URL, the 10 minute time frame limitation no longer limits the success.

## More dangerous than normal AitM phishing

Actually, Device Code Flow phishing is even worse than normal phishing, because the user is using the original website he expects. Therefore he cannot detect that he is currently being attacked based on an phishy URL. Additionally, the user might not need to authenticate interactivly because a session is still active. Therefore, the user has almost no time to realize that this is not legitimate. And not to forget that Device Code Flow is underminding FIDO's phishing resistance!

## Proof of Concept

I wrote a Proof of Concept (POC) of the described "Sophisticated Attack Model", which can be found here: [https://github.com/denniskniep/DeviceCodePhishing](https://github.com/denniskniep/DeviceCodePhishing). 

* It´s a phishing server leveraging the Device Code Flow
* Capable of bypassing FIDO, even if FIDO is the only authentication method available to the victim
* Victim visits the phishing URL and is instantly redirected to the authentication page (no need for the victim to manually enter the code)
* Victim completes authentication on the original webpage (no suspicious URL involved)

You can see it in action in this [Demo Video](https://gist.github.com/user-attachments/assets/bf6d1c2d-7199-4394-824d-e6f57e8136a2)

Currently, this tool can only be used to target Microsoft Azure Entra Users. However, the technique itself is not limited to a certain vendor.

## Mitigation

The only mitigation from my point of view is to deactivate the Device Code Flow!
For Azure Entra, you can implement a [Conditional Access Policy that blocks the Device Code Flow](https://learn.microsoft.com/en-us/entra/identity/conditional-access/policy-block-authentication-flows)

## Next steps with obtained tokens

Once you have successfully obtained tokens, you can use them with other attack tools, such as:

- https://github.com/dafthack/GraphRunner
- https://github.com/f-bader/TokenTacticsV2?tab=readme-ov-file#azure-json-web-token-jwt-manipulation-toolset
- https://github.com/secureworks/family-of-client-ids-research

## Further Links

- Microsoft's Threat Intelligence is also reporting about it: [Storm-2372 conducts device code phishing campaign](https://www.microsoft.com/en-us/security/blog/2025/02/13/storm-2372-conducts-device-code-phishing-campaign/#:~:text=In%20device%20code%20phishing%2C%20threat,compromised%20account%20has%20access%20to)
- [Official Microsoft Docs](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-device-code)
- [squarephish (using normal attack model)](https://github.com/secureworks/squarephish)
- [Blog about Storm-2372 (using normal attack model)](https://jeffreyappel.nl/how-to-protect-against-device-code-flow-abuse-storm-2372-attacks-and-block-the-authentication-flow/)
- [Device Code Phishing - Deep Dive](https://aadinternals.com/post/phishing/)
- [IETF - Cross Device Security](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-cross-device-security#name-cross-device-protocols-and-)
