---
title: Aanmelden bij de telefoon en aanmelden met aangepast beleid (preview-versie)
titleSuffix: Azure AD B2C
description: Eenmalige wacht woorden (OTP) in tekst berichten verzenden naar de telefoons van uw toepassings gebruikers met aangepast beleid in Azure Active Directory B2C.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183955"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Stel de telefoon registratie in en meld u aan met aangepast beleid in Azure AD B2C (preview-versie)

Met aanmelden en aanmelden in Azure Active Directory B2C (Azure AD B2C) kunnen uw gebruikers zich registreren en aanmelden bij uw toepassingen met behulp van een eenmalig wacht woord (OTP) dat in een tekst bericht naar de telefoon wordt verzonden. Eenmalige wacht woorden kunnen u helpen het risico te verkleinen dat uw gebruikers hun wacht woord verg eten of het probleem hebben aangetast.

Volg de stappen in dit artikel om het aangepaste beleid te gebruiken zodat uw klanten zich kunnen registreren en aanmelden bij uw toepassingen met behulp van een eenmalig wacht woord dat naar hun telefoon wordt verzonden.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Prijzen

Eenmalige wacht woorden worden naar uw gebruikers verzonden met behulp van SMS-berichten en er worden kosten in rekening gebracht voor elk bericht dat wordt verzonden. Zie de sectie **afzonderlijke kosten** van [Azure Active Directory B2C prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)voor prijs informatie.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig voordat u OTP kunt instellen.

* [Azure AD B2C Tenant](tutorial-create-tenant.md)
* [Webtoepassing geregistreerd](tutorial-register-applications.md) in uw Tenant
* [Aangepast beleid](custom-policy-get-started.md) dat is geüpload naar uw Tenant

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Het Start pakket voor aanmelding via de telefoon &

Begin met het bijwerken van de aangepaste beleids bestanden voor het registreren van de telefoon en het aanmelden om met uw Azure AD B2C-Tenant te werken.

Bij de volgende stappen wordt ervan uitgegaan dat u de [vereiste onderdelen](#prerequisites) hebt voltooid en dat de [aangepaste beleids][starter-pack] opslagplaats voor het werk Archief al is gekloond op uw lokale machine.

1. Zoek de [aangepaste beleids bestanden][starter-pack-phone] voor het registreren van de telefoon en het aanmelden in uw lokale kloon van de opslag plaats van het Start pakket, of down load deze rechtstreeks. De XML-beleids bestanden bevinden zich in de volgende map:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Vervang in elk bestand de teken reeks `yourtenant` door de naam van uw Azure AD B2C-Tenant. Als de naam van uw B2C-Tenant bijvoorbeeld *contosob2c*is, worden `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com`alle exemplaren van.

1. Volg de stappen in de sectie [toepassings-Id's toevoegen aan het aangepaste beleid](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) van aan de [slag met aangepast beleid in azure Active Directory B2C](custom-policy-get-started.md). In dit geval moet u `/phone-number-passwordless/` **`Phone_Email_Base.xml`** bijwerken met de **toepassings-id's (client)** van de twee toepassingen die u hebt geregistreerd bij het volt ooien van de vereisten, *IdentityExperienceFramework* en *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>De beleids bestanden uploaden

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure AD B2C-Tenant.
1. Onder **beleids regels**selecteert u **identiteits ervaring-Framework**.
1. Selecteer **aangepast beleid uploaden**.
1. Upload de beleids bestanden in de volgende volg orde:
    1. *Phone_Email_Base. XML*
    1. *SignUpOrSignInWithPhone. XML*
    1. *SignUpOrSignInWithPhoneOrEmail. XML*
    1. *ProfileEditPhoneOnly. XML*
    1. *ProfileEditPhoneEmail. XML*
    1. *ChangePhoneNumber. XML*
    1. *PasswordResetEmail. XML*

Wanneer u elk bestand uploadt, voegt Azure het `B2C_1A_`voor voegsel toe.

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Selecteer **B2C_1A_SignUpOrSignInWithPhone**onder **aangepast beleid**.
1. Onder **toepassing selecteren**selecteert u de *webapp1* -toepassing die u hebt geregistreerd bij het volt ooien van de vereisten.
1. Kies `https://jwt.ms`bij **Selecteer antwoord-URL**.
1. Selecteer **nu uitvoeren** en meld u aan met een e-mail adres of telefoon nummer.
1. Selecteer **nu opnieuw uitvoeren** en meld u aan met hetzelfde account om te controleren of u de juiste configuratie hebt.

## <a name="get-user-account-by-phone-number"></a>Gebruikers account op telefoon nummer ophalen

Een gebruiker die zich aanmeldt met een telefoon nummer, maar geen herstel-e-mail adres opgeeft, wordt in uw Azure AD B2C Directory opgeslagen met hun telefoon nummer als aanmeldings naam. Als de gebruiker zijn of haar telefoon nummer wil wijzigen, moet uw Help Desk of ondersteunings team eerst zijn of haar account vinden en vervolgens hun telefoon nummer bijwerken.

U kunt een gebruiker vinden op basis van hun telefoon nummer (aanmeldings naam) door gebruik te maken van [Microsoft Graph](manage-user-accounts-graph-api.md):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Bijvoorbeeld:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Volgende stappen

U vindt de aanmeldings-en aanmeldings opties voor het aangepaste beleid voor de telefoon (en andere Starter Packs) op GitHub:

[Azure-samples/Active-Directory-B2C-Custom-Policy-starterpack/scenarios/telefoon nummer: wacht woordloos][starter-pack-phone]

De Starter Pack-beleids bestanden gebruiken multi-factor Authentication-technische profielen en claim transformaties voor het telefoon nummer:

* [Een Azure Multi-Factor Authentication Technical-profiel definiëren](multi-factor-auth-technical-profile.md)
* [Claim transformaties voor telefoon nummers definiëren](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
