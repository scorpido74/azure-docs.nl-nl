---
title: Microsoft identity platform Java web app quickstart | Azure
description: Meer informatie over het implementeren van Microsoft-aanmelding op een Java Web App met OpenID Connect
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: c2f3272beb463a16f9488139b6c3a45febae6493
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79529631"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Snelstart: aanmelden met Microsoft toevoegen aan een Java-web-app

In deze quickstart leert u hoe u een Java-webtoepassing integreren met het Microsoft-identiteitsplatform. Uw app meldt zich aan bij een gebruiker, krijgt een toegangstoken om de Microsoft Graph-API aan te roepen en doet een verzoek voor de Microsoft Graph API.

Wanneer u deze quickstart hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke Microsoft-accounts (waaronder outlook.com, live.com en anderen) en werk- of schoolaccounts van bedrijven of organisaties die Azure Active Directory gebruiken. (Zie [hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.)

## <a name="prerequisites"></a>Vereisten

Om dit voorbeeld uit te voeren heb je het volgende nodig:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 of hoger, en [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De snelstart-app registreren en downloaden
> U hebt twee opties om uw quickstart-toepassing te starten: express (optie 1) of handleiding (optie 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: de app registreren en automatisch configureren, en vervolgens de voorbeeldcode downloaden
>
> 1. Ga naar de [Azure-portal - App-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
>
> 1. Navigeer naar de pagina Microsoft-identiteitsplatform voor ontwikkelaars [App-registraties.](/azure/active-directory/develop/)
> 1. Selecteer **Nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>    - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `java-webapp`.
>    - Laat Uri nu leeg **omleiden** en selecteer **Registreren**.
> 1. Zoek op de pagina **Overzicht** de id van toepassing **(client)** en de **id-waarden directory (tenant)** van de toepassing. Kopieer deze waarden voor later.
> 1. Selecteer de **verificatie** in het menu en voeg de volgende gegevens toe:
>    - Voeg in **OMLEIDINGs-URI's**toe `https://localhost:8080/msal4jsample/secure/aad` en `https://localhost:8080/msal4jsample/graph/me`.
>    - Selecteer **Opslaan**.
> 1. Selecteer de **certificaten & geheimen** van het menu en klik in de sectie **Klantgeheimen** op **Nieuw klantgeheim:**
>
>    - Typ een sleutelbeschrijving (bijvoorbeeld app-geheim).
>    - Selecteer een sleutelduur **in 1 jaar.**
>    - De sleutelwaarde wordt weergegeven wanneer u **Toevoegen**selecteert.
>    - Kopieer de waarde van de sleutel voor later. Deze sleutelwaarde wordt niet opnieuw weergegeven en wordt op geen enkele andere manier opgehaald, dus neem deze op zodra deze zichtbaar is vanaf de Azure-portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in de Azure-portal
>
> Voor het codevoorbeeld voor deze snelle start om te werken, moet u:
>
> 1. Voeg antwoord-URL's toe als `https://localhost:8080/msal4jsample/secure/aad` en `https://localhost:8080/msal4jsample/graph/me`.
> 1. Maak een clientgeheim.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-code-sample"></a>Stap 2: Download het voorbeeld van de code
> [!div renderon="docs"]
> [Het voorbeeld van de code downloaden](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Download het project en haal het zip-bestand naar een lokale map dichter bij de hoofdmap - bijvoorbeeld **C:\Azure-Samples**
> 
> Als u https wilt gebruiken met localhost, vult u de eigenschappen server.ssl.key in. Gebruik het hulpprogramma voor de sleuteltool (inbegrepen in JRE) om een zelfondertekend certificaat te genereren.
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12  
>   server.ssl.key-store=classpath:keystore.p12  
>   server.ssl.key-store-password=password  
>   server.ssl.key-alias=testCert
>   ```
>   Plaats het gegenereerde keystore-bestand in de map 'bronnen'.
   
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Stap 3: Het codevoorbeeld configureren
> 1. Pak het zipbestand uit in een lokale map.
> 1. Als u een geïntegreerde ontwikkelomgeving gebruikt, opent u het voorbeeld in uw favoriete IDE (optioneel).
> 1. Open het bestand application.properties, dat te vinden is in src/main/resources/map en vervang de waarde van de velden *aad.clientId,* *aad.authority* en *aad.secretKey* als volgt door de respectievelijke waarden **van Application Id,** **Tenant Id** en **Client Secret:**
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8080/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8080/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Waar:
>
> - `Enter_the_Application_Id_here`: de toepassings-id voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Client_Secret_Here`- is het **clientgeheim dat** u hebt gemaakt in **Certificaten & Geheimen** voor de door u geregistreerde toepassing.
> - `Enter_the_Tenant_Info_Here`- is de **directory (tenant) ID-waarde** van de toepassing die u hebt geregistreerd.
> 1. Als u https wilt gebruiken met localhost, vult u de eigenschappen server.ssl.key in. Gebruik het hulpprogramma voor de sleuteltool (inbegrepen in JRE) om een zelfondertekend certificaat te genereren.
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12  
>   server.ssl.key-store=classpath:keystore.p12  
>   server.ssl.key-store-password=password  
>   server.ssl.key-alias=testCert
>   ```
>   Plaats het gegenereerde keystore-bestand in de map 'bronnen'.


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Stap 3: Het codevoorbeeld uitvoeren
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Stap 4: Het codevoorbeeld uitvoeren

Als u het project wilt uitvoeren, u het:

Voer het rechtstreeks uit vanaf uw IDE met behulp van de ingebouwde veeropstartserver of verpak het naar een WAR-bestand met behulp van [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) en implementeer het naar een J2EE-containeroplossing zoals [Apache Tomcat.](http://tomcat.apache.org/)

##### <a name="running-from-ide"></a>Op de vlucht voor IDE

Als u de webtoepassing vanaf een IDE uitvoert, klikt u op uitvoeren en navigeert u vervolgens naar de startpagina van het project. Voor dit voorbeeld is https://localhost:8080de url van de standaardstartpagina .

1. Selecteer op de voorpagina de knop **Aanmelding** om door te verwijzen naar Azure Active Directory en vraag de gebruiker om zijn referenties.

1. Nadat de gebruiker is geverifieerd, worden *https://localhost:8080/msal4jsample/secure/aad*ze doorgestuurd naar . Ze zijn nu aangemeld en op de pagina vindt u informatie over het aangemelde account. De voorbeeldgebruikersinterface heeft de volgende knoppen:
    - *Afmelden:* hiermee wordt de huidige gebruiker uit de toepassing verwijderd en doorverwezen naar de startpagina.
    - *Gebruikersgegevens weergeven:* hiermee wordt een token voor Microsoft Graph aangenomen en wordt Microsoft Graph aanroept met een aanvraag met het token, waarmee basisinformatie over de aangemelde gebruiker wordt geretourneerd.


   
> [!IMPORTANT]
> Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als vertrouwelijke client. Omdat het clientgeheim wordt toegevoegd als een platte tekst aan uw projectbestanden, wordt het om veiligheidsredenen aanbevolen om een certificaat te gebruiken in plaats van een clientgeheim voordat u de toepassing als productietoepassing overweegt. Zie [Certificaatreferenties voor toepassingsverificatie voor](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials)meer informatie over het gebruik van een certificaat.

## <a name="more-information"></a>Meer informatie

### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
![Laat zien hoe de voorbeeld-app die door deze quickstart wordt gegenereerd, werkt](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>MsAL krijgen

MSAL for Java (MSAL4J) is de Java-bibliotheek die wordt gebruikt om gebruikers aan te melden en tokens aan te vragen die worden gebruikt om toegang te krijgen tot een API die wordt beschermd door het Microsoft-identiteitsplatform.

Voeg MSAL4J toe aan uw toepassing door Maven of Gradle te gebruiken om uw afhankelijkheden te beheren door de volgende wijzigingen aan te brengen in het pom.xml (Maven) of build.gradle -bestand van de toepassing.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>MSAL initialiseren

Voeg een verwijzing naar MSAL voor Java toe door de volgende code toe te voegen aan de bovenkant van het bestand waar u MSAL4J gaat gebruiken:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over machtigingen en toestemming:

> [!div class="nextstepaction"]
> [Machtigingen en toestemming](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Zie de oauth 2.0-autorisatiecodestroom voor meer informatie over de auth-stroom voor dit scenario:

> [!div class="nextstepaction"]
> [Autorisatiecode Oauth-stroom](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Help ons het Microsoft-identiteitsplatform te verbeteren. Vertel ons wat u ervan vindt door een korte enquête met twee vragen in te vullen.

> [!div class="nextstepaction"]
> [Enquête van microsoft-identiteitsplatform](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
