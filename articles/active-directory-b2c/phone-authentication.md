---
title: Aanmelden via de telefoon en aanmelden met aangepast beleid (Voorbeeld)
titleSuffix: Azure AD B2C
description: Stuur eenmalige wachtwoorden (OTP) in tekstberichten naar de telefoons van uw toepassingsgebruikers met aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eadac0e973b361b1fdee63dcc9cfa848a0b2bacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183955"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Aanmelden voor telefoon en aanmelden met aangepast beleid instellen in Azure AD B2C (Voorbeeld)

Met aanmelding en aanmelding via azure Active Directory B2C (Azure AD B2C) kunnen uw gebruikers zich aanmelden en zich aanmelden bij uw toepassingen met behulp van een eenmalig wachtwoord (OTP) dat in een sms-bericht naar hun telefoon wordt verzonden. Eenmalige wachtwoorden kunnen helpen het risico te minimaliseren dat uw gebruikers hun wachtwoorden vergeten of in gevaar brengen.

Volg de stappen in dit artikel om het aangepaste beleid te gebruiken om uw klanten in staat te stellen zich aan te melden en zich aan te melden bij uw toepassingen met behulp van een eenmalig wachtwoord dat naar hun telefoon wordt verzonden.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Prijzen

Eenmalige wachtwoorden worden naar uw gebruikers verzonden met sms-berichten en voor elk verzonden bericht worden mogelijk kosten in rekening gebracht. Zie het gedeelte Afzonderlijke kosten van [Azure Active Directory B2C-prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)voor prijsinformatie. **Separate Charges**

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig voordat u OTP instelt.

* [Azure AD B2C-tenant](tutorial-create-tenant.md)
* [Webtoepassing geregistreerd](tutorial-register-applications.md) in uw tenant
* [Aangepast beleid](custom-policy-get-started.md) geüpload naar uw tenant

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Ontvang het aanmeldingspakket & aanmelding

Begin met het bijwerken van de aanmeldings- en aanmeldingsstandaardbestanden voor telefoon om te werken met uw Azure AD B2C-tenant.

De volgende stappen gaan ervan uit dat u de [vereisten](#prerequisites) hebt voltooid en de [aangepaste starterpackrepository][starter-pack] voor het beleid al hebt gekloond op uw lokale machine.

1. Zoek de [telefoon aanmeldings- en aanmeldingsbestanden][starter-pack-phone] in uw lokale kloon van het startpakket repo, of download ze rechtstreeks. De XML-beleidsbestanden bevinden zich in de volgende map:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Vervang in elk bestand `yourtenant` de tekenreeks door de naam van uw Azure AD B2C-tenant. Als bijvoorbeeld de naam van uw B2C-tenant *contosob2c*is, worden alle gevallen van `yourtenant.onmicrosoft.com` geworden `contosob2c.onmicrosoft.com`.

1. Voer de stappen uit in de [sectie Toepassings-id's toevoegen aan de sectie Aangepast beleid](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) van Aan de slag met aangepast beleid in Azure Active Directory [B2C](custom-policy-get-started.md). Werk in dit `/phone-number-passwordless/` **`Phone_Email_Base.xml`** geval bij met de **applicatie(client) id's** van de twee applicaties die je hebt geregistreerd bij het invullen van de voorwaarden, *IdentityExperienceFramework* en *ProxyIdentityExperienceFramework.*

## <a name="upload-the-policy-files"></a>De beleidsbestanden uploaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en navigeer naar uw Azure AD B2C-tenant.
1. Selecteer **onder Beleid**het Framework Voor **identiteitservaring**.
1. Selecteer **Aangepast beleid uploaden**.
1. Upload de beleidsbestanden in de volgende volgorde:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignuporSignInWithPhoneorEmail.xml*
    1. *ProfielEditPhoneOnly.xml*
    1. *ProfielEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Terwijl u elk bestand uploadt, `B2C_1A_`voegt Azure het voorvoegsel toe.

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Selecteer onder **Aangepast beleid** **B2C_1A_SignUpOrSignInWithPhone**.
1. Selecteer **onder Toepassing selecteren**de *webapp1-toepassing* die u hebt geregistreerd bij het invullen van de vereisten.
1. Kies `https://jwt.ms`voor **De antwoord-url selecteren**.
1. Selecteer **Nu uitvoeren** en meld u aan met een e-mailadres of een telefoonnummer.
1. Selecteer Nu opnieuw **uitvoeren** en meld u aan met hetzelfde account om te bevestigen dat u de juiste configuratie hebt.

## <a name="get-user-account-by-phone-number"></a>Gebruikersaccount ontvangen op telefoonnummer

Een gebruiker die zich aanmeldt met een telefoonnummer, maar geen herstel-e-mailadres opgeeft, wordt opgenomen in uw Azure AD B2C-map met zijn telefoonnummer als aanmeldingsnaam. Als de gebruiker vervolgens zijn telefoonnummer wil wijzigen, moet uw helpdesk of ondersteuningsteam eerst zijn account vinden en vervolgens zijn telefoonnummer bijwerken.

U een gebruiker vinden op basis van zijn telefoonnummer (aanmeldingsnaam) met [Microsoft Graph:](manage-user-accounts-graph-api.md)

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Bijvoorbeeld:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Volgende stappen

U vindt het aanmeldings- en aanmeldingspakket voor het beleid (en andere startpakketten) op GitHub:

[Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless][starter-pack-phone]

De beleidsbestanden van het startpakket maken gebruik van technische profielen voor meervoudige verificatie en wijzigingen in telefoonnummerclaims:

* [Een technisch azure multi-factor verificatieprofiel definiëren](multi-factor-auth-technical-profile.md)
* [Wijzigingen in telefoonnummerclaims definiëren](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
