---
title: E-mailverificatie uitschakelen tijdens het aanmelden van klanten met een aangepast beleid
titleSuffix: Azure AD B2C
description: Meer informatie over het uitschakelen van e-mailverificatie tijdens het aanmelden van klanten in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 13a5fa6a030d876d92651ca587e37fdc6a3ec600
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136139"
---
# <a name="disable-email-verification-during-customer-sign-up-using-a-custom-policy-in-azure-active-directory-b2c"></a>E-mailverificatie uitschakelen tijdens het aanmelden van klanten met een aangepast beleid in Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

## <a name="prerequisites"></a>Vereisten

Voer de stappen uit in [Aan de slag met aangepast beleid](custom-policy-get-started.md). U moet een werkend aangepast beleid hebben voor aanmelden en aanmelden met sociale en lokale accounts.

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Voeg de metadata toe aan het zelfverklaarde technische profiel

Het technisch profiel **LocalAccountSignUpWithLogonEmail** is een [zelfgeclaimde](self-asserted-technical-profile.md), die wordt aangeroepen tijdens de aanmeldingsstroom. Als u de verificatie `EnforceEmailVerification` van de e-mail wilt uitschakelen, stelt u de metagegevens in op false. Overschrijven van de technische profielen LocalAccountSignUpWithLogonEmail in het extensiebestand. 

1. Open het extensiesbestand van uw beleid. Bijvoorbeeld. <em> `SocialAndLocalAccounts/` </em>
1. Zoek `ClaimsProviders` het element. Als het element niet bestaat, voeg je het toe.
1. Voeg de volgende claimprovider toe aan het `ClaimsProviders` element:

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zorg ervoor dat u de map met uw Azure AD-tenant gebruikt door het **filter Directory + abonnement** in het bovenste menu te selecteren en de map te kiezen die uw Azure AD-tenant bevat.
3. Kies **Alle services** in de linkerbovenhoek van de Azure-portal en zoek en selecteer **app-registraties**.
4. Selecteer **Identity Experience Framework**.
5. Selecteer **Aangepast beleid uploaden**en upload de twee beleidsbestanden die u hebt gewijzigd.
2. Selecteer het aanmeldings- of aanmeldingsbeleid dat u hebt geüpload en klik op **nu uitvoeren.**
3. U moet zich kunnen aanmelden met een e-mailadres zonder de validatie.


## <a name="next-steps"></a>Volgende stappen

- Lees meer over het [zelfgeclaimde technische profiel](self-asserted-technical-profile.md) in de IEF-referentie.
