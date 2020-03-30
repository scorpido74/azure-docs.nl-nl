---
title: Microsoft identity platform accounts & tenant profielen op Android | Azure
description: Een overzicht van Microsoft identity platform accounts voor Android
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: d0497ad68e7b29e6d8c83dd860ba8f509e229579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611884"
---
# <a name="accounts--tenant-profiles-android"></a>Accounts en tenantprofielen (Android)

In dit artikel vindt `account` u een overzicht van wat een is in het Microsoft-identiteitsplatform.

De MSAL-API (Microsoft Authentication Library) vervangt de term *gebruiker* door het *term-account*. Een van de redenen is dat een gebruiker (mens of software agent) kan hebben, of kan gebruiken, meerdere accounts. Deze accounts kunnen zich in de eigen organisatie van de gebruiker bevinden en/of in andere organisaties waarvan de gebruiker lid is.

Een account in het Microsoft-identiteitsplatform bestaat uit:

- Een unieke id.  
- Een of meer referenties die worden gebruikt om eigendom/controle van het account aan te tonen.
- Een of meer profielen bestaande uit kenmerken zoals:
  - Afbeelding, voornaam, familienaam, titel, kantoorlocatie
- Een account heeft een bron van autoriteit of registratiesysteem. Dit is het systeem waarin het account wordt gemaakt en waar de referenties die aan dat account zijn gekoppeld, worden opgeslagen. In multi-tenant systemen zoals het Microsoft-identiteitsplatform `tenant` is het registratiesysteem het waar het account is gemaakt. Deze huurder wordt ook `home tenant`wel de .
- Accounts in het Microsoft-identiteitsplatform hebben de volgende registratiesystemen:
  - Azure Active Directory, inclusief Azure Active Directory B2C.
  - Microsoft-account (Live).
- Accounts van recordsystemen buiten het Microsoft-identiteitsplatform zijn vertegenwoordigd binnen het Microsoft-identiteitsplatform, waaronder:
  - identiteiten van verbonden on-premises mappen (Windows Server Active Directory)
  - externe identiteiten van LinkedIn, GitHub, enzovoort.
  In deze gevallen heeft een account zowel een oorsprongssysteem van registratie als een registratiesysteem binnen het Microsoft-identiteitsplatform.
- Met het Microsoft-identiteitsplatform kan één account worden gebruikt om toegang te krijgen tot bronnen van meerdere organisaties (Azure Active Directory-tenants).
  - Als u wilt vastleggen dat een account van het ene recordsysteem (AAD Tenant A) toegang heeft tot een resource in een ander systeem van record (AAD Tenant B), moet het account worden weergegeven in de tenant waar de resource is gedefinieerd. Dit wordt gedaan door het maken van een lokale record van het account van systeem A in systeem B.
  - Deze lokale record, dat is de weergave van het account, is gebonden aan het oorspronkelijke account.
  - MSAL onthult dit lokale `Tenant Profile`record als een .
  - Tenantprofiel kan verschillende kenmerken hebben die geschikt zijn voor de lokale context, zoals functietitel, kantoorlocatie, contactgegevens, enz.
- Omdat een account aanwezig kan zijn in een of meer huurders, kan een account meer dan één profiel hebben.

> [!NOTE]
> MSAL behandelt het Microsoft-accountsysteem (Live, MSA) als een andere tenant binnen het Microsoft-identiteitsplatform. De tenant-id van de Microsoft-accounttenant is:`9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagram met accountoverzicht

![Diagram met accountoverzicht](./media/accounts-overview/accounts-overview.svg)

In het bovenstaande diagram:

- Het `bob@contoso.com` account wordt gemaakt in de on-premises Windows Server Active Directory (origin on-premises systeem van registratie).
- Het `tom@live.com` account wordt gemaakt in de microsoft-accounttenant.
- `bob@contoso.com`heeft toegang tot ten minste één bron in de volgende Azure Active Directory-tenants:
  - contoso.com (cloud systeem van record - gekoppeld aan on-premises systeem van record)
  - fabrikam.com
  - woodgrovebank.com
  - Een tenant `bob@contoso.com` profiel voor bestaat in elk van deze huurders.
- `tom@live.com`heeft toegang tot bronnen in de volgende Microsoft-tenants:
  - contoso.com
  - fabrikam.com
  - Een tenant `tom@live.com` profiel voor bestaat in elk van deze huurders.
- Informatie over Tom en Bob in andere huurders kan verschillen van die in het systeem van record. Ze kunnen verschillen per kenmerk, zoals Functietitel, Office-locatie, enzovoort. Ze kunnen lid zijn van groepen en/of rollen binnen elke organisatie (Azure Active Directory Tenant). We verwijzen naar bob@contoso.com deze informatie als huurder profiel.

In het bob@contoso.com diagram tom@live.com en heb je toegang tot bronnen in verschillende Azure Active Directory-tenants. Zie [Azure Active Directory B2B-samenwerkingsgebruikers toevoegen in de Azure-portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)voor meer informatie.

## <a name="accounts-and-single-sign-on-sso"></a>Accounts en eenmalige aanmelding (SSO)

De MSAL-tokencache slaat *één vernieuwingstoken* per account op. Dat vernieuwingstoken kan worden gebruikt om in stilte toegangstokens aan te vragen bij meerdere microsoft-identiteitsplatformtenants. Wanneer een broker op een apparaat is geïnstalleerd, wordt het account beheerd door de makelaar en wordt apparaatbrede eenmalige aanmelding mogelijk.

> [!IMPORTANT]
> Business to Consumer (B2C) account en vernieuwen token gedrag verschilt van de rest van de Microsoft identity platform. Zie [B2C-beleid & Accounts voor](#b2c-policies--accounts)meer informatie .

## <a name="account-identifiers"></a>Account-id's

De MSAL-account-id is geen accountobject-id. Het is niet bedoeld om te worden ontleed en / of vertrouwd op iets anders dan uniciteit binnen de Microsoft-identiteit platform over te brengen.

Voor compatibiliteit met de Azure AD-verificatiebibliotheek (ADAL) en om de migratie van ADAL naar MSAL te vergemakkelijken, kan MSAL accounts opzoeken met behulp van een geldige id voor het account dat beschikbaar is in de MSAL-cache.  Als u bijvoorbeeld hetzelfde accountobject ophaalt tom@live.com omdat elk van de id's geldig is:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Toegang tot claims over een account

Naast het aanvragen van een toegangstoken vraagt MSAL ook altijd een ID-token aan bij elke tenant. Zij doet dit door altijd de volgende scopes aan te vragen:

- Openid
- profiel

Het ID-token bevat een lijst met claims. `Claims`zijn naam/waardeparen over het account en worden gebruikt om de aanvraag in te dienen.

Zoals eerder vermeld, kan elke tenant waar een account bestaat verschillende informatie over het account opslaan, inclusief maar niet beperkt tot kenmerken zoals: functietitel, kantoorlocatie, enzovoort.

Hoewel een account lid of gast kan zijn in meerdere organisaties, vraagt MSAL geen service op om een lijst te krijgen van de tenants waarvan het account lid is. In plaats daarvan bouwt MSAL een lijst op van tenants waarin het account aanwezig is, als gevolg van tokenaanvragen die zijn gedaan.

De claims die op het accountobject worden blootgesteld, zijn altijd de claims van de 'woninghuurder'/{autoriteit} voor een account. Als dat account niet is gebruikt om een token aan te vragen voor hun huistenant, kan MSAL geen claims indienen via het accountobject.  Bijvoorbeeld:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Als u een lijst met claims wilt zien die beschikbaar zijn in het accountobject, raadpleegt u [claims in een id_token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> Als u aanvullende claims in uw id_token wilt opnemen, raadpleegt u de optionele claimdocumentatie in [Hoe: Optionele claims voor uw Azure AD-app verstrekken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

### <a name="access-tenant-profile-claims"></a>Claimvoor tenantprofiel openen

Als u toegang wilt krijgen tot claims over een account zoals `IMultiTenantAccount`deze worden weergegeven in andere tenants, moet u eerst uw accountobject casten naar . Alle accounts kunnen multi-tenant zijn, maar het aantal tenantprofielen dat beschikbaar is via MSAL is gebaseerd op welke tenants u tokens hebt aangevraagd bij het gebruik van de lopende rekening.  Bijvoorbeeld:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C-beleid & accounts

Vernieuwingstokens voor een account worden niet gedeeld in het B2C-beleid. Als gevolg hiervan is eenmalige aanmelding met tokens niet mogelijk. Dit betekent niet dat eenmalige aanmelding niet mogelijk is. Het betekent dat single sign-on een interactieve ervaring moet gebruiken waarin een cookie beschikbaar is om eenmalige aanmelding mogelijk te maken.

Dit betekent ook dat in het geval van MSAL, als u tokens verwerft met verschillende B2C-beleidsregels, deze worden behandeld als afzonderlijke accounts - elk met hun eigen id. Als u een account wilt gebruiken `acquireTokenSilent`om een token aan te vragen met behulp van , moet u het account selecteren in de lijst met accounts die overeenkomt met het beleid dat u gebruikt met de tokenaanvraag. Bijvoorbeeld:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```
