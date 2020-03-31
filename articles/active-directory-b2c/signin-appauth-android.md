---
title: Een token aanschaffen in een Android-toepassing
titleSuffix: Azure AD B2C
description: Een Android-app maken die AppAuth gebruikt met Azure Active Directory B2C om gebruikersidentiteiten te beheren en gebruikers te verifiëren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31ad373b1544fc601a9c37e05e324a9c1dfb3f73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183773"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Aanmelden met een Android-toepassing in Azure Active Directory B2C

Op het Microsoft Identity-platform wordt gebruikgemaakt van open standaarden, zoals OAuth2 en OpenID Connect. Met deze standaarden u gebruikmaken van elke bibliotheek die u wilt integreren met Azure Active Directory B2C. Om u te helpen andere bibliotheken te gebruiken, u een walkthrough als deze gebruiken om aan te tonen hoe u bibliotheken van derden configureren om verbinding te maken met het Microsoft-identiteitsplatform. De meeste bibliotheken die [de RFC6749 OAuth2-specificaties](https://tools.ietf.org/html/rfc6749) implementeren, kunnen verbinding maken met het Microsoft Identity-platform.

> [!WARNING]
> Microsoft biedt geen oplossingen voor bibliotheken van derden en heeft deze bibliotheken niet beoordeeld. In dit voorbeeld wordt een bibliotheek van derden genaamd AppAuth gebruikt die is getest op compatibiliteit in basisscenario's met de Azure AD B2C. Problemen en functieaanvragen moeten worden gericht op het open-sourceproject van de bibliotheek. Zie [dit artikel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) voor meer informatie.
>
>

Als u nog geen ervaring hebt met OAuth2 of OpenID Connect, zal een groot gedeelte van deze voorbeeldconfiguratie u niet veel zeggen. U wordt geadviseerd eerst een beknopt [overzicht van het hier gedocumenteerde protocol te bekijken](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Een Azure AD B2C-directory maken

Voordat u Azure AD B2C kunt gebruiken, moet u een directory, of tenant, maken. Een directory is een container voor alle gebruikers, apps, groepen en meer. Als u nog geen directory hebt, [maakt u een B2C-directory](tutorial-create-tenant.md) voordat u verdergaat.

## <a name="create-an-application"></a>Een app maken

Registreer vervolgens een toepassing in uw Azure AD B2C-tenant. Dit geeft Azure AD de informatie die het nodig heeft om veilig te communiceren met uw app.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Neem de **toepassings-id (client) op** voor gebruik in een latere stap.

Neem ook uw aangepaste omleiding URI voor gebruik in een latere stap. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Uw gebruikersstromen maken

In Azure AD B2C wordt elke gebruikerservaring gedefinieerd door een [gebruikersstroom](user-flow-overview.md), een set beleidsregels die het gedrag van Azure AD beheren. Deze toepassing vereist een aanmeldings- en aanmeldingsgebruikersstroom. Wanneer u de gebruikersstroom maakt, moet u het als:

* Kies de **weergavenaam** als aanmeldingskenmerk in uw gebruikersstroom.
* Kies de **claimen Weergavenaam** en **Object ID-toepassing** in elke gebruikersstroom. U kunt ook andere claims kiezen.
* Kopieer de **naam** van elke gebruikersstroom nadat u deze hebt gemaakt. Deze moet het voorvoegsel `b2c_1_` bevatten.  U hebt de naam van de gebruikersstroom later nodig.

Nadat u uw gebruikersstromen hebt gemaakt, bent u klaar om uw app te bouwen.

## <a name="download-the-sample-code"></a>De voorbeeldcode downloaden

We hebben een werkend voorbeeld geleverd dat AppAuth gebruikt met Azure AD B2C [op GitHub.](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) U de code downloaden en uitvoeren. U snel aan de slag met uw eigen app met uw eigen Azure AD B2C-configuratie door de instructies in de [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md)te volgen.

Het monster is een wijziging van het monster van [AppAuth](https://openid.github.io/AppAuth-Android/). Ga naar hun pagina voor meer informatie over AppAuth en de functies ervan.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Uw app wijzigen om Azure AD B2C met AppAuth te gebruiken

> [!NOTE]
> AppAuth ondersteunt Android API 16 (Jellybean) en hoger. We raden aan API 23 en hoger te gebruiken.
>

### <a name="configuration"></a>Configuratie

U de communicatie met Azure AD B2C configureren door de detectie-URI op te geven of door zowel het autorisatieeindpunt als de token-eindpunt-URI's op te geven. In beide gevallen hebt u de volgende informatie nodig:

* Tenant-id (bijv. contoso.onmicrosoft.com)
* Gebruikersnaam (bijvoorbeeld B2C\_1\_SignUpIn)

Als u ervoor kiest om automatisch de autorisatie- en token-eindpunt-URI's te ontdekken, moet u informatie ophalen uit de detectie-URI. De detectie URI kan worden gegenereerd\_door de\_tenant-id en de beleidsnaam in de volgende URL te vervangen:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

U vervolgens de URL's voor autorisatie en tokeneindpunt en een object AuthorizationServiceConfiguration maken door het volgende uit te voeren:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

In plaats van detectie te gebruiken om de autorisatie- en token-eindpunt-URI's te verkrijgen, u deze ook expliciet opgeven door de tenant-id\_en de beleidsnaam\_in de onderstaande URL's te vervangen:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Voer de volgende code uit om het object AuthorizationServiceConfiguration te maken:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Machtigen

Na het configureren of ophalen van een autorisatieserviceconfiguratie kan een autorisatieaanvraag worden samengesteld. Als u het verzoek wilt maken, hebt u de volgende informatie nodig:

* Client-ID (APPLICATION ID) die u eerder hebt opgenomen. Bijvoorbeeld `00000000-0000-0000-0000-000000000000`.
* Aangepaste omleiding URI die u eerder hebt opgenomen. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Beide items hadden moeten worden opgeslagen toen u [uw app registreerde.](#create-an-application)

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Raadpleeg de [AppAuth-handleiding](https://openid.github.io/AppAuth-Android/) voor het voltooien van de rest van het proces. Als je snel aan de slag wilt met een werkende app, bekijk dan [ons voorbeeld.](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) Volg de stappen in de [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) om uw eigen Azure AD B2C-configuratie in te voeren.
