---
title: Een token verkrijgen in een Android-toepassing
titleSuffix: Azure AD B2C
description: Een Android-app maken die gebruikmaakt van AppAuth met Azure Active Directory B2C om gebruikers identiteiten te beheren en gebruikers te verifiëren.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183773"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Meld u aan met een Android-toepassing in Azure Active Directory B2C

Op het Microsoft Identity-platform wordt gebruikgemaakt van open standaarden, zoals OAuth2 en OpenID Connect. Met deze standaarden kunt u gebruikmaken van elke bibliotheek die u wilt integreren met Azure Active Directory B2C. Om u te helpen andere bibliotheken te gebruiken, kunt u een scenario als deze gebruiken om te laten zien hoe u bibliotheken van derden configureert om verbinding te maken met het micro soft Identity-platform. De meeste bibliotheken die [de RFC6749 OAuth2-spec](https://tools.ietf.org/html/rfc6749) implementeren, kunnen verbinding maken met het micro soft Identity-platform.

> [!WARNING]
> Micro soft biedt geen oplossingen voor bibliotheken van derden en heeft geen beoordeling uitgevoerd van die bibliotheken. In dit voor beeld wordt een bibliotheek van derden gebruikt met de naam AppAuth die is getest op compatibiliteit in basis scenario's met de Azure AD B2C. Problemen en functie aanvragen moeten worden omgeleid naar het open-source project van de bibliotheek. Raadpleeg [dit artikel](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) voor meer informatie.
>
>

Als u nog geen ervaring hebt met OAuth2 of OpenID Connect, zal een groot gedeelte van deze voorbeeldconfiguratie u niet veel zeggen. U wordt geadviseerd eerst een beknopt [overzicht van het hier gedocumenteerde protocol te bekijken](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Een Azure AD B2C-directory maken

Voordat u Azure AD B2C kunt gebruiken, moet u een directory, of tenant, maken. Een directory is een container voor alle gebruikers, apps, groepen en meer. Als u nog geen directory hebt, [maakt u een B2C-directory](tutorial-create-tenant.md) voordat u verdergaat.

## <a name="create-an-application"></a>Een app maken

Registreer vervolgens een toepassing in uw Azure AD B2C-Tenant. Dit geeft Azure AD de informatie die nodig is om veilig te communiceren met uw app.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Noteer de **id van de toepassing (client)** voor gebruik in een latere stap.

Neem ook uw aangepaste omleidings-URI op voor gebruik in een latere stap. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Uw gebruikers stromen maken

In Azure AD B2C wordt elke gebruikers ervaring gedefinieerd door een [gebruikers stroom](user-flow-overview.md). Dit is een set beleids regels die het gedrag van Azure AD regelen. Deze toepassing vereist een aanmeldings-en registratie-gebruikers stroom. Wanneer u de gebruikers stroom maakt, moet u het volgende doen:

* Kies de **weergave naam** als een aanmeldings kenmerk in uw gebruikers stroom.
* Kies de **weergave naam** en de **object-id** toepassings claims in elke gebruikers stroom. U kunt ook andere claims kiezen.
* Kopieer de **naam** van elke gebruikers stroom nadat u deze hebt gemaakt. Deze moet het voorvoegsel `b2c_1_` bevatten.  U hebt de naam van de gebruikers stroom later nodig.

Nadat u de gebruikers stromen hebt gemaakt, bent u klaar om uw app te bouwen.

## <a name="download-the-sample-code"></a>De voorbeeld code downloaden

We hebben een werkend voor beeld gegeven dat gebruikmaakt van AppAuth met Azure AD B2C [op github](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). U kunt de code downloaden en uitvoeren. U kunt snel aan de slag met uw eigen app met uw eigen Azure AD B2C configuratie door de instructies in de [README.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md)te volgen.

Het voor beeld is een wijziging van het voor beeld dat wordt verschaft door [AppAuth](https://openid.github.io/AppAuth-Android/). Ga naar de pagina voor meer informatie over AppAuth en de bijbehorende functies.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Uw app wijzigen om Azure AD B2C te gebruiken met AppAuth

> [!NOTE]
> AppAuth biedt ondersteuning voor Android API 16 (Jellybean) en hoger. U kunt het beste API 23 en hoger gebruiken.
>

### <a name="configuration"></a>Configuratie

U kunt communicatie met Azure AD B2C configureren door ofwel de detectie-URI op te geven of door zowel het autorisatie-eind punt als het token-eind punt-Uri's op te geven. In beide gevallen hebt u de volgende informatie nodig:

* Tenant-ID (bijvoorbeeld contoso.onmicrosoft.com)
* Gebruikers stroom naam (bijvoorbeeld B2C\_1\_SignUpIn)

Als u ervoor kiest om automatisch de autorisatie en Token-eind punt-Uri's te detecteren, moet u de gegevens van de detectie-URI ophalen. De detectie-URI kan worden gegenereerd door de Tenant\_-ID en het beleids\_naam in de volgende URL te vervangen:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

U kunt vervolgens de autorisatie-en Token-eind punt-Uri's ophalen en een AuthorizationServiceConfiguration-object maken door het volgende uit te voeren:

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

In plaats van detectie te gebruiken om de autorisatie-en Token-eind punt-Uri's te verkrijgen, kunt u ze ook expliciet opgeven door de Tenant\_-ID en het beleid\_naam in de onderstaande URL te vervangen:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Voer de volgende code uit om uw AuthorizationServiceConfiguration-object te maken:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autoriseren

Na het configureren of ophalen van een configuratie van een autorisatie service kan een autorisatie aanvraag worden samengesteld. Als u de aanvraag wilt maken, hebt u de volgende gegevens nodig:

* De client-ID (toepassings-ID) die u eerder hebt vastgelegd. Bijvoorbeeld `00000000-0000-0000-0000-000000000000`.
* Aangepaste omleidings-URI die u eerder hebt vastgelegd. Bijvoorbeeld `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Beide items moeten zijn opgeslagen bij [het registreren van uw app](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Raadpleeg de AppAuth- [hand leiding](https://openid.github.io/AppAuth-Android/) voor informatie over hoe u de rest van het proces kunt volt ooien. Als u snel aan de slag wilt gaan met een werkende app, kunt u het voor [beeld](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)bekijken. Volg de stappen in de [README.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) om uw eigen Azure AD B2C configuratie in te voeren.
