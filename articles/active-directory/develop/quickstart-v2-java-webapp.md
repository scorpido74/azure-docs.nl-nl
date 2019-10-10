---
title: Snelstartgids voor Java-Web-apps voor micro soft Identity platform | Azure
description: Meer informatie over het implementeren van micro soft-aanmelding in een Java-Web-app met behulp van OpenID Connect Connect
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev
ms.openlocfilehash: 8bb9073ccb4aef81b46b3b2b87730ddede5c0ff7
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240192"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Snelstartgids: aanmelden toevoegen met micro soft aan een Java-Web-app

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

In deze Quick Start leert u hoe u een Java-webtoepassing integreert met het micro soft Identity-platform. Uw app meldt zich aan bij een gebruiker, haalt een toegangs token op om de Microsoft Graph-API aan te roepen en brengt een aanvraag naar de Microsoft Graph-API. 

Wanneer u de hand leiding hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke micro soft-accounts (waaronder outlook.com, live.com en anderen) en werk-of school accounts van elk bedrijf of organisatie die gebruikmaakt van Azure Active Directory.

![Toont hoe de voor beeld-app die door deze Quick start is gegenereerd, werkt](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>Vereisten

Als u dit voor beeld wilt uitvoeren, hebt u het volgende nodig:
- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 of hoger en [maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
> U hebt twee opties om uw Quick Start-toepassing te starten: Express (optie 1) of hand matig (optie 2)
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: de app registreren en automatisch configureren, en vervolgens de voorbeeldcode downloaden
>
> 1. Ga naar de [Azure Portal-app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: de toepassing en voorbeeldcode registreren en handmatig configureren
> 
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecteer **nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>    - Voer in de sectie **Naam** een beschrijvende toepassingsnaam. Deze wordt zichtbaar voor gebruikers van de app. Bijvoorbeeld: `java-webapp`.
>    - Laat de **omleidings-URI** nu leeg en selecteer **registreren**.
> 1. Zoek de waarde van de **toepassing (client)-ID** van de toepassing. Kopieer deze waarde, u hebt deze later nodig.
> 1. Zoek de **ID-waarde van de directory (Tenant)** van de toepassing. Kopieer deze waarde, u hebt deze later nodig.
> 1. Selecteer het menu **Verificatie** en voeg dan de volgende gegevens toe:
>    - In **omleidings-uri's**voegt u `http://localhost:8080/msal4jsamples/secure/aad` en `https://localhost:8080/msal4jsamples/graph/users` toe.
>    - Selecteer **Opslaan**.
> 1. Kies in het menu links de optie **certificaten & geheimen** en klik in de sectie **client geheimen** op **Nieuw client geheim** :
>     
>    - Typ een beschrijving voor de sleutel (van het app-geheim van de instantie).
>    - Selecteer een sleutel duur van **in één jaar**.
>    - Wanneer u op **toevoegen**klikt, wordt de waarde van de sleutel weer gegeven. 
>    - Kopieer de waarde van de sleutel, u hebt deze later nodig.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in de Azure-portal
> Het code voorbeeld voor deze Quick Start werkt alleen als u:
> 1. Antwoord-Url's toevoegen als `http://localhost:8080/msal4jsamples/secure/aad` en `https://localhost:8080/msal4jsamples/graph/users`.
> 1. Maak een client geheim.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-code-sample"></a>Stap 2: het code voorbeeld downloaden
 
 [Het code voorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
 
 #### <a name="step-3-configure-the-code-sample"></a>Stap 3: het code voorbeeld configureren 
 
 1. Pak het zip-bestand uit naar een lokale map.
 1. Als u een Integrated Development Environment gebruikt, opent u het voor beeld in uw favoriete IDE (optioneel).
 1. Open het bestand **Application. Properties** , dat u kunt vinden in *src/main/resources/* .
 1. Toepassings eigenschappen vervangen.
   1. Zoek `aad.clientId` en werk de waarde van `Enter_the_Application_Id_here` bij met de waarde van de **toepassing (client) ID** van de toepassing die u hebt geregistreerd. 
   1. Zoek `aad.authority` en werk de waarde van `Enter_the_Tenant_Name_Here` bij met de ID-waarde van de **Directory (Tenant)** van de toepassing die u hebt geregistreerd.
   1. Zoek `aad.secretKey` en werk de waarde van `Enter_the_Client_Secret_Here` bij met het **client geheim** dat u hebt gemaakt in **Certificaten & geheimen** voor de toepassing die u hebt geregistreerd.

> [!div renderon="docs"]
> Waar:
>
> - `Enter_the_Application_Id_here`: de toepassings-id voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Client_Secret_Here`-is het **client geheim** dat u hebt gemaakt in **Certificaten & geheimen** voor de toepassing die u hebt geregistreerd.

#### <a name="step-4-run-the-code-sample"></a>Stap 4: het code voorbeeld uitvoeren
1. Voer het code voorbeeld uit en open een browser en ga naar *http://localhost:8080* .
1. De front page bevat een knop **Aanmelden** . Klik op de knop **Aanmelden** om naar Azure Active Directory om te leiden. De gebruiker wordt om referenties gevraagd.  
1. Nadat de verificatie op Azure Active Directory is geslaagd, worden ze omgeleid naar *http://localhost:8080/msal4jsamples/secure/aad* . Ze zijn officieel aangemeld bij de toepassing en op de pagina moet informatie voor het aangemelde account worden weer gegeven. Het bevat ook knoppen voor: 
    - *Afmelden*: de huidige gebruiker wordt afgemeld bij de toepassing en deze wordt omgeleid naar de start pagina.
    - *Gebruikers weer geven*: ontvangt een token voor de Microsoft Graph en roept vervolgens de Microsoft Graph aan met het token dat is gekoppeld aan de aanvraag om alle gebruikers in de Tenant op te halen.

## <a name="more-information"></a>Meer informatie

### <a name="getting-msal"></a>MSAL ophalen
MSAL4J is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens die worden gebruikt voor toegang tot een API die wordt beveiligd door het micro soft Identity-platform. U kunt MSAL4J toevoegen aan uw toepassing met behulp van Maven of Gradle voor het beheren van uw afhankelijkheden door de volgende wijzigingen aan te brengen in het bestand pom. XML of build. Gradle in uw toepassing. 

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.5.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```


### <a name="msal-initialization"></a>MSAL initialiseren
U kunt de verwijzing naar MSAL4J toevoegen door de volgende code toe te voegen aan de bovenkant van het bestand waarin u MSAL4J gaat gebruiken: 

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over machtigingen en toestemming:

> [!div class="nextstepaction"]
> [Machtigingen en toestemming](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-permissions-and-consent)

Zie voor meer informatie over de verificatie stroom voor dit scenario de OAuth 2,0-autorisatie code stroom:

> [!div class="nextstepaction"]
> [OAuth-stroom van autorisatie code](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
