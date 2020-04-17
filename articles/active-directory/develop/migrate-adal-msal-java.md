---
title: ADAL naar MSAL migratiegids (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het migreren van uw Azure Active Directory Authentication Library (ADAL) Java-app naar de Microsoft Authentication Library (MSAL).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 7ba845e79074313f0ccf2c066ba016bd72d46efe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534564"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>ADAL naar MSAL migratiegids voor Java

In dit artikel worden wijzigingen beschreven die u moet aanbrengen om een app te migreren die de Azure Active Directory Authentication Library (ADAL) gebruikt om de MsAL (Microsoft Authentication Library) te gebruiken.

Zowel Microsoft Authentication Library for Java (MSAL4J) als Azure AD Authentication Library for Java (ADAL4J) worden gebruikt om Azure AD-entiteiten te verifiëren en tokens van Azure AD aan te vragen. Tot nu toe hebben de meeste ontwikkelaars samengewerkt met Azure AD voor ontwikkelaarsplatform (v1.0) om Azure AD-identiteiten (werk- en schoolaccounts) te verifiëren door tokens aan te vragen met Azure AD-verificatiebibliotheek (ADAL).

MSAL biedt de volgende voordelen:

- Omdat het het eindpunt van het nieuwere Microsoft-identiteitsplatform wordt gebruikt, u een bredere set Microsoft-identiteiten verifiëren, zoals Azure AD-identiteiten, Microsoft-accounts en sociale en lokale accounts via Azure AD Business to Consumer (B2C).
- Uw gebruikers krijgen de beste single-sign-on ervaring.
- Uw toepassing kan incrementele toestemming inschakelen en het ondersteunen van voorwaardelijke toegang is eenvoudiger.

MSAL voor Java is de auth-bibliotheek die u aanraden te gebruiken met het Microsoft-identiteitsplatform. Er worden geen nieuwe functies geïmplementeerd op ADAL4J. Alle inspanningen in de toekomst zijn gericht op het verbeteren van MSAL.

## <a name="differences"></a>Verschillen

Als u hebt gewerkt met het Azure AD voor ontwikkelaars (v1.0) eindpunt (en ADAL4J), wilt u misschien lezen [Wat is er anders aan het Eindpunt van het Microsoft-identiteitsplatform (v2.0).](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

## <a name="scopes-not-resources"></a>Scopes niet resources

ADAL4J verwerft tokens voor resources, terwijl MSAL voor Java tokens voor scopes verwerft. Een aantal MSAL voor Java-klassen vereisen een scopeparameter. Deze parameter is een lijst met tekenreeksen die de gewenste machtigingen en resources declareren die worden aangevraagd. Zie [de scopes van Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference) om voorbeeldscopes te bekijken.

## <a name="core-classes"></a>Kernklassen

In ADAL4J `AuthenticationContext` vertegenwoordigt de klasse uw verbinding met de Security Token Service (STS) of autorisatieserver via een autoriteit. MSAL voor Java is echter ontworpen rond clienttoepassingen. Het biedt twee afzonderlijke `PublicClientApplication` `ConfidentialClientApplication` klassen: en om clienttoepassingen te vertegenwoordigen.  Dit laatste `ConfidentialClientApplication`is een toepassing die is ontworpen om een geheim veilig te bewaren, zoals een toepassings-id voor een daemon-app.

In de volgende tabel ziet u hoe ADAL4J functies inkaart brengt naar de nieuwe FUNCTIES MSAL voor Java:

| ADAL4J-methode| MSAL4J-methode|
|------|-------|
|acquireToken(Tekenreeksbron, ClientCredential-referenties, AuthenticationCallback callback) | acquireToken(ClientCredentialParameters)|
|acquireToken(Tekenreeksbron, clientassertion-bewering, AuthenticationCallback-terugroepen)|acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, AsymmetricKeyCredential credential, AuthenticationCallback callback)|acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, String clientId, String gebruikersnaam, String password, AuthenticationCallback callback)| acquireToken(UsernamePasswordParameters)|
|acquireToken(String resource, String clientId, String gebruikersnaam, String password=null, AuthenticationCallback callback)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken(String resource, UserAssertion userAssertion, ClientCredential credential, AuthenticationCallback callback)| acquireToken(OnBehalfOfParameters)|
|verwervenTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| devicecode() en acquireTokenByDeviceCode()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IAccount in plaats van IUser

ADAL4J manipuleerde gebruikers. Hoewel een gebruiker één menselijke of softwareagent vertegenwoordigt, kan deze een of meer accounts in het Microsoft-identiteitssysteem hebben. Een gebruiker kan bijvoorbeeld meerdere Azure AD-, Azure AD B2C- of Microsoft-persoonlijke accounts hebben.

MSAL voor Java definieert het `IAccount` concept van Account via de interface. Dit is een brekende verandering van ADAL4J, maar het is een goede omdat het het feit vangt dat dezelfde gebruiker meerdere accounts kan hebben, en misschien zelfs in verschillende Azure AD-mappen. MSAL voor Java biedt betere informatie in gastscenario's omdat thuisaccountinformatie wordt verstrekt.

## <a name="cache-persistence"></a>Cache persistentie

ADAL4J had geen ondersteuning voor tokencache.
MSAL voor Java voegt een [tokencache](msal-acquire-cache-tokens.md) toe om het beheer van tokenlevensduur te vereenvoudigen door verlopen tokens automatisch te vernieuwen wanneer mogelijk en onnodige aanwijzingen voor de gebruiker te voorkomen om waar mogelijk referenties te verstrekken.

## <a name="common-authority"></a>Gemeenschappelijke Autoriteit

Als u de `https://login.microsoftonline.com/common` autoriteit gebruikt, kunnen gebruikers zich in v1.0 aanmelden met een Azure Active Directory (AAD)-account (voor elke organisatie).

Als u `https://login.microsoftonline.com/common` de autoriteit in v2.0 gebruikt, kunnen gebruikers zich aanmelden bij elke AAD-organisatie of zelfs een persoonlijk Microsoft-account (MSA). In MSAL voor Java, als u wilt inloggen beperken tot `https://login.microsoftonline.com/organizations` een AAD-account, moet u de autoriteit te gebruiken (dat is hetzelfde gedrag als met ADAL4J). Als u een bevoegdheid `authority` wilt opgeven, stelt u de parameter `PublicClientApplication` in de methode [PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) in wanneer u uw klasse maakt.

## <a name="v10-and-v20-tokens"></a>v1.0- en v2.0-tokens

Het v1.0-eindpunt (gebruikt door ADAL) zendt alleen v1.0-tokens uit.

Het v2.0-eindpunt (gebruikt door MSAL) kan v1.0- en v2.0-tokens uitzenden. Een eigenschap van het toepassingsmanifest van de web-API stelt ontwikkelaars in staat om te kiezen welke versie van token wordt geaccepteerd. Zie `accessTokenAcceptedVersion` in de referentiedocumentatie van het [toepassingsmanifest.](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)

Zie [Azure Active Directory-toegangstokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)voor meer informatie over v1.0- en v2.0-tokens.

## <a name="adal-to-msal-migration"></a>ADAL naar MSAL-migratie

In ADAL4J werden de refresh-tokens blootgesteld - waardoor ontwikkelaars ze in de cache konden opslaan. Ze zouden `AcquireTokenByRefreshToken()` vervolgens gebruiken om oplossingen in te schakelen, zoals het implementeren van langlopende services die dashboards vernieuwen namens de gebruiker wanneer de gebruiker niet langer is verbonden.

MSAL voor Java stelt om veiligheidsredenen geen vernieuwingstokens bloot. In plaats daarvan verwerkt MSAL verfrissende tokens voor u.

MSAL voor Java heeft een API waarmee u vernieuwingstokens die u met ADAL4j hebt aangeschaft, migreren naar de ClientApplication: [acquireToken(RefreshTokenParameters).](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-) Met deze methode u het eerder gebruikte vernieuwingstoken bieden, samen met alle scopes (resources) die u wenst. Het vernieuwingstoken wordt ingeruild voor een nieuwe en in de cache opgeslagen voor gebruik door uw toepassing.

In het volgende codefragment wordt een migratiecode weergegeven in een vertrouwelijke clienttoepassing:

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

De `IAuthenticationResult` retourneert een toegangstoken en ID-token, terwijl uw nieuwe vernieuwingstoken wordt opgeslagen in de cache.
De applicatie bevat nu ook een IAccount:

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

Als u de tokens wilt gebruiken die zich nu in de cache bevinden, belt u het:

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build();
IAuthenticationResult result = app.acquireToken(parameters);
```
