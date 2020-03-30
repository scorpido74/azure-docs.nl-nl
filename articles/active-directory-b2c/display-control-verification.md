---
title: Claims verifiëren met weergavebesturingselementen
titleSuffix: Azure AD B2C
description: Meer informatie over het gebruik van Azure AD B2C-weergavebesturingselementen om de claims te verifiëren in de gebruikersreizen die worden geleverd door uw aangepaste beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff2a8ad05e26ea31fc8100d4000171313881f4e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188780"
---
# <a name="verification-display-control"></a>Controlecontrole voor de weergave controle van verificatie

Gebruik een [verificatiecontroleom](display-controls.md) een claim te verifiëren, bijvoorbeeld een e-mailadres of telefoonnummer, met een verificatiecode die naar de gebruiker wordt verzonden.

## <a name="verificationcontrol-actions"></a>Acties VerificationControl

Het besturingselement voor de verificatieweergave bestaat uit twee stappen (acties):

1. Vraag een bestemming aan van de gebruiker, zoals een e-mailadres of telefoonnummer, waarnaar de verificatiecode moet worden verzonden. Wanneer de gebruiker de knop **Code verzenden** selecteert, wordt de **actie SendCode** van het controlebesturingselement voor verificatieweergave uitgevoerd. De **Actie SendCode** genereert een code, construeert de inhoud die moet worden verzonden en stuurt deze naar de gebruiker. De waarde van het adres kan vooraf worden ingevuld en dienen als een tweede-factor authenticatie.

    ![Voorbeeldpagina voor codeactie verzenden](media/display-control-verification/display-control-verification-email-action-01.png)

1. Nadat de code is verzonden, leest de gebruiker het bericht, voert de verificatiecode in het besturingselement dat door het weergavebesturingselement wordt verstrekt en selecteert **u Code verifiëren**. Door **Code verifiëren**te selecteren, wordt de **actie VerifyCode** uitgevoerd om de code te verifiëren die aan het adres is gekoppeld. Als de gebruiker **Nieuwe code**verzenden selecteert, wordt de eerste actie opnieuw uitgevoerd.

    ![Voorbeeldpagina voor codeactie verifiëren](media/display-control-verification/display-control-verification-email-action-02.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="verificationcontrol-required-elements"></a>VerificatieControle vereiste elementen

De **verificatiecontrole** moet de volgende elementen bevatten:

- Het type `DisplayControl` van `VerificationControl`de is .
- `DisplayClaims`
  - **Verzenden naar** - Een of meer claims waarin wordt aangegeven waar u de verificatiecode naartoe moet sturen. Bijvoorbeeld *e-mail-* of *landcode* en *telefoonnummer*.
  - **Verificatiecode** : de verificatiecodeclaim die de gebruiker verstrekt nadat de code is verzonden. Deze claim moet worden ingesteld `ControlClaimType` als vereist, `VerificationCode`en de moet worden ingesteld op .
- Uitvoerclaim (optioneel) die moet worden geretourneerd naar de zelfverklaarde pagina nadat de gebruiker het verificatieproces heeft voltooid. Bijvoorbeeld *e-mail-* of *landcode* en *telefoonnummer*. Het zelfverklaarde technische profiel gebruikt de claims om de gegevens voort te houden of de uitvoerclaims naar de volgende orchestration-stap op te voeren.
- Twee `Action`s met de volgende namen:
  - **SendCode** - Stuurt een code naar de gebruiker. Deze actie bevat meestal twee validatie technische profiel, om een code te genereren en te verzenden.
  - **VerifyCode** - Verifieert de code. Deze actie bevat meestal één technisch validatieprofiel.

In het onderstaande voorbeeld wordt een **e-mailtekstvak** weergegeven op de pagina. Wanneer de gebruiker zijn e-mailadres invoert en **SendCode**selecteert, wordt de actie **SendCode** geactiveerd in de Back-end van Azure AD B2C.

Vervolgens voert de gebruiker de **verificatiecode** in en selecteert **u VerifyCode** om de actie **VerifyCode** in de back-end te activeren. Als alle validaties slagen, wordt het **Verificatiebesturingselement** als voltooid beschouwd en kan de gebruiker doorgaan naar de volgende stap.

```XML
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email"  Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```
