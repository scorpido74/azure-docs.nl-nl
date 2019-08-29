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
ms.date: 08/11/2019
ms.author: sagonzal
ms.custom: aaddev
ms.openlocfilehash: 549b4457ee38504001e83c4b831cd321e1542068
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125464"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Quickstart: Aanmelden met micro soft toevoegen aan een Java-Web-app

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

In deze Quick Start leert u hoe u een Java-webtoepassing integreert met het micro soft Identity-platform. Uw app meldt zich aan bij een gebruiker, haalt een toegangs token op om de Microsoft Graph-API aan te roepen en brengt een aanvraag naar de Microsoft Graph-API. 

Wanneer u de hand leiding hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke micro soft-accounts (waaronder outlook.com, live.com en anderen) en werk-of school accounts van elk bedrijf of organisatie die gebruikmaakt van Azure Active Directory.

![Toont hoe de voor beeld-app die door deze Quick start is gegenereerd, werkt](media/quickstart-v2-java-webapp/java-quickstart.svg)

> ## <a name="prerequisites"></a>Vereisten
> Als u dit voor beeld wilt uitvoeren, hebt u het volgende nodig: 
> - Java Development Kit (JDK) 8 of hoger en Maven.
> - Een Azure Active Directory-Tenant (Azure AD). Zie [een Azure AD-Tenant verkrijgen](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-create-new-tenant) voor meer informatie over het verkrijgen van een Azure AD-Tenant.
>
> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * ExpressCard [Optie 1: Registreer en configureer uw app automatisch en down load vervolgens het code voorbeeld.](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Handmatig: [Optie 2: Registreer en configureer uw voor beeld van de toepassing en code hand matig.](#option-2-register-and-manually-configure-your-application-and-code-sample)
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de [Azure Portal-app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om uw nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
> 
>
> #### <a name="step-1-download-the-code-sample"></a>Stap 1: Het code voorbeeld downloaden
> 
> - [Het code voorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
>
> #### <a name="step-2-open-applicationproperties"></a>Stap 2: Toepassing openen. eigenschappen
>
> 1. Pak het zip-bestand uit naar een lokale map.
> 1. Beschrijving Als u een Integrated Development Environment gebruikt, opent u het voor beeld in uw favoriete IDE.
> 1. Open het bestand *Application. Properties* . U gaat waarden invoegen voor `aad.clientId`, `aad.authority`en `aad.secretKey` wanneer u uw toepassing registreert in de volgende stap.


> #### <a name="step-3-register-your-application"></a>Stap 3: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang hebt tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek en stelt u de portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecteer **nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>    - Voer in de sectie **Naam** een beschrijvende toepassingsnaam. Deze wordt zichtbaar voor gebruikers van de app. Bijvoorbeeld: `java-webapp`.
>    - Laat de omleidings- **URI** nu leeg en selecteer **registreren**.
> 1. Zoek de waarde van de **toepassing (client)-ID** van de toepassing. Werk de waarde van `Enter_the_Application_Id_here` in het bestand *Application. Properties* bij.
> 1. Zoek de **ID-waarde van de directory (Tenant)** van de toepassing. Werk de waarde van `Enter_the_Tenant_Info_Here` in het bestand *Application. Properties* bij. 
> 1. Selecteer het menu **Verificatie** en voeg dan de volgende gegevens toe:
>    - Voeg `http://localhost:8080/msal4jsamples/secure/aad` en`https://localhost:8080/msal4jsamples/graph/users`toe bij omleidings-uri's.
>    - Selecteer **Opslaan**.
> 1. Kies in het menu links de optie **certificaten & geheimen** en klik in de sectie **client geheimen** op **Nieuw client geheim** :
>     
>    - Typ een beschrijving voor de sleutel (van het app-geheim van de instantie).
>    - Selecteer een sleutel duur van **in één jaar**.
>    - Wanneer u op **toevoegen**klikt, wordt de waarde van de sleutel weer gegeven. 
>    - Kopieer de waarde van de sleutel. Open het bestand *Application. Properties* dat u eerder hebt gedownload en werk de waarde `Enter_the_Client_Secret_Here` bij met de sleutel waarde. 
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> Het code voorbeeld voor deze Quick Start werkt alleen als u:
> 1. Antwoord-url's toevoegen `http://localhost:8080/msal4jsamples/secure/aad` als `https://localhost:8080/msal4jsamples/graph/users`en.
> 1. Maak een client geheim.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.
> 
> #### <a name="step-2-download-the-code-sample"></a>Stap 2: Het code voorbeeld downloaden
> 
> - [Het code voorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
> 
> #### <a name="step-3-configure-the-code-sample"></a>Stap 3: Het code voorbeeld configureren 
> 
> 1. Pak het zip-bestand uit naar een lokale map.
> 1. Als u een Integrated Development Environment gebruikt, opent u het voor beeld in uw favoriete IDE (optioneel).
> 1. Open het bestand **Application. Properties** , dat u kunt vinden in *src/main/resources/* .
> 1. Toepassings eigenschappen vervangen.
>   1. Zoek `aad.clientId` en werk de waarde bij `Enter_the_Application_Id_here` met de waarde van de **toepassing (client) ID** van de toepassing die u hebt geregistreerd. 
>   1. Zoek `aad.authority` en werk de `Enter_the_Tenant_Name_Here` waarde bij met de **id-waarde van de directory (Tenant)** van de toepassing die u hebt geregistreerd.
>   1. Zoek `aad.secretKey` en werk de `Enter_the_Client_Secret_Here` waarde bij met het **client geheim** dat u hebt gemaakt in **Certificaten & geheimen** voor de toepassing die u hebt geregistreerd.

#### <a name="step-4-run-the-code-sample"></a>Stap 4: Het code voorbeeld uitvoeren
1. Voer het code voorbeeld uit en open een browser en ga naar *http://localhost:8080* .
1. De front page bevat een knop **Aanmelden** . Klik op de knop **Aanmelden** om naar Azure Active Directory om te leiden. De gebruiker wordt om referenties gevraagd.  
1. Nadat is geverifieerd op Azure Active Directory, worden ze omgeleid naar *http://localhost:8080/msal4jsamples/secure/aad* . Ze zijn officieel aangemeld bij de toepassing en op de pagina moet informatie voor het aangemelde account worden weer gegeven. Het bevat ook knoppen voor: 
    - *Afmelden*: De huidige gebruiker wordt afgemeld bij de toepassing en deze wordt omgeleid naar de start pagina.
    - *Gebruikers weer geven*: Haalt een token voor de Microsoft Graph op en roept vervolgens de Microsoft Graph aan met het token dat aan de aanvraag is gekoppeld om alle gebruikers in de Tenant op te halen.


## <a name="more-information"></a>Meer informatie

### <a name="getting-msal"></a>MSAL ophalen
MSAL4J is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens die worden gebruikt voor toegang tot een API die wordt beveiligd door het micro soft Identity-platform. U kunt MSAL4J toevoegen aan uw toepassing met behulp van Maven of Gradle voor het beheren van uw afhankelijkheden door de volgende wijzigingen aan te brengen in het bestand pom. XML of build. Gradle in uw toepassing. 

```
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.5.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```


### <a name="msal-initialization"></a>Msal-initialisatie
U kunt de verwijzing naar MSAL4J toevoegen door de volgende code toe te voegen aan de bovenkant van het bestand waarin u MSAL4J gaat gebruiken: 

```
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
