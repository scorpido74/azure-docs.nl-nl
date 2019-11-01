---
title: SCIM-gebruikers inrichten met Azure Active Directory | Microsoft Docs
description: Leer hoe u een SCIM-eind punt bouwt, uw SCIM-API integreert met Azure Active Directory en begin met het automatiseren van provisoning-gebruikers en-groepen in uw toepassingen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9c15a462692c257fac759998698679d9e59dc53
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242291"
---
# <a name="scim-user-provisioning-with-azure-active-directory"></a>SCIM-gebruikers inrichten met Azure Active Directory

Het systeem voor Identity Management ([scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)) tussen domeinen is een gestandaardiseerd protocol en schema waarmee meer consistentie wordt gestimuleerd in de manier waarop identiteiten op verschillende systemen worden beheerd. Wanneer een toepassing een SCIM-eind punt voor gebruikers beheer ondersteunt, kan de Azure AD User Provisioning-Service aanvragen verzenden om toegewezen gebruikers en groepen te maken, wijzigen of verwijderen aan dit eind punt.

Veel van de toepassingen waarvoor Azure AD [vooraf geïntegreerde automatische gebruikers inrichting](../saas-apps/tutorial-list.md) ondersteunt, implementeren scim als de manier om meldingen over gebruikers wijzigingen te ontvangen.  Daarnaast kunnen klanten toepassingen verbinden die een specifiek profiel van de [SCIM 2,0-protocol specificatie](https://tools.ietf.org/html/rfc7644) ondersteunen met behulp van de algemene integratie optie ' niet-galerie ' in de Azure Portal.

De hoofd focus van dit artikel bevindt zich op het profiel van SCIM 2,0 dat door Azure AD wordt geïmplementeerd als onderdeel van de algemene SCIM-connector voor niet-galerij-apps. Geslaagde tests van een toepassing die ondersteuning biedt voor SCIM met de algemene Azure AD-connector is echter een stap voor het verkrijgen van een app die wordt vermeld in de Azure AD-galerie als ondersteuning van de gebruikers inrichting. Zie [How to: uw toepassing in de Azure AD-toepassings galerie](../develop/howto-app-gallery-listing.md)weer geven voor meer informatie over het ophalen van uw toepassing in de Azure AD-toepassings galerie.

> [!IMPORTANT]
> Het gedrag van de Azure AD SCIM-implementatie is voor het laatst bijgewerkt op 18 december 2018. Zie [SCIM 2,0-protocol compatibiliteit van de Azure AD User Provisioning Service](application-provisioning-config-problem-scim-compatibility.md)voor meer informatie over wat er is gewijzigd.

![toont de inrichting van Azure AD naar een app of id Store][0]<br/>
*Afbeelding 1: inrichten van Azure Active Directory naar een toepassing of identiteits opslag die SCIM implementeert*

Dit artikel is onderverdeeld in vier secties:

* **[Gebruikers en groepen inrichten voor toepassingen van derden die ondersteuning bieden voor SCIM 2,0](#provisioning-users-and-groups-to-applications-that-support-scim)** -als uw organisatie gebruikmaakt van een toepassing van derden die het profiel van scim 2,0 implementeert dat door Azure AD wordt ondersteund, kunt u beginnen met het automatiseren van zowel inrichten als de inrichting van gebruikers en groepen wordt vandaag ongedaan gemaakt.
* Meer **[informatie over de implementatie van Azure AD scim](#understanding-the-azure-ad-scim-implementation)** : als u een toepassing bouwt die een scim 2,0-gebruikers beheer-API ondersteunt, wordt in deze sectie beschreven hoe de Azure AD scim-client wordt geïmplementeerd en hoe u uw scim-protocol moet model leren. afhandeling van aanvragen en antwoorden.
* Het **[bouwen van een scim-eind punt met behulp van micro soft cli-bibliotheken](#building-a-scim-endpoint-using-microsoft-cli-libraries)** -bibliotheken met gemeen schappelijke taal infrastructuur (CLI) en code voorbeelden laten zien hoe u een scim-eind punt ontwikkelt en scim-berichten omzet.  
* **[Verwijzing naar gebruikers-en groeps schema](#user-and-group-schema-reference)** : beschrijft het gebruikers-en groeps schema dat wordt ondersteund door de Azure AD scim-implementatie voor niet-galerij-apps.

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Gebruikers en groepen inrichten voor toepassingen die SCIM ondersteunen

Azure AD kan worden geconfigureerd voor het automatisch inrichten van toegewezen gebruikers en groepen aan toepassingen die een specifiek profiel van het [SCIM 2,0-protocol](https://tools.ietf.org/html/rfc7644)implementeren. De details van het profiel zijn gedocumenteerd in [de uitleg van de Azure AD scim-implementatie](#understanding-the-azure-ad-scim-implementation).

Neem contact op met uw toepassings provider of de documentatie van uw toepassings provider voor instructies over de compatibiliteit van deze vereisten.

> [!IMPORTANT]
> De Azure AD SCIM-implementatie is gebaseerd op de Azure AD User Provisioning-Service, die is ontworpen om gebruikers voortdurend te synchroniseren tussen Azure AD en de doel toepassing, en een zeer specifieke set standaard bewerkingen uit te voeren. Het is belang rijk dat u dit gedrag begrijpt om inzicht te krijgen in het gedrag van de Azure AD SCIM-client. Zie [Wat gebeurt er tijdens het inrichten van gebruikers?](user-provisioning.md#what-happens-during-provisioning)voor meer informatie.

### <a name="getting-started"></a>Aan de slag

Toepassingen die ondersteuning bieden voor het SCIM-profiel dat in dit artikel wordt beschreven, kunnen worden verbonden met Azure Active Directory met behulp van de functie ' niet-galerie toepassing ' in de Azure AD-toepassings galerie. Zodra de verbinding tot stand is gebracht, voert Azure AD elke 40 minuten een synchronisatie uit waarbij het SCIM-eind punt van de toepassing wordt opgevraagd voor toegewezen gebruikers en groepen, en worden ze gemaakt of gewijzigd op basis van de toewijzings Details.

**Verbinding maken met een toepassing die ondersteuning biedt voor SCIM:**

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com). Houd er rekening mee dat u toegang krijgt tot een gratis proef versie voor Azure Active Directory met P2-licenties door u aan te melden voor het [ontwikkelaars programma](https://developer.microsoft.com/office/dev-program)
1. Selecteer **bedrijfs toepassingen** in het linkerdeel venster. Er wordt een lijst met alle geconfigureerde apps weer gegeven, met inbegrip van apps die zijn toegevoegd vanuit de galerie.
1. Selecteer **+ nieuwe toepassing** > **alle** > **niet-galerie toepassing**.
1. Voer een naam in voor uw toepassing en selecteer **toevoegen** om een app-object te maken. De nieuwe app wordt toegevoegd aan de lijst met bedrijfs toepassingen en wordt geopend op het scherm voor het beheren van apps.

   ![scherm afbeelding toont de Azure AD-toepassings galerie][1]<br/>
   *Afbeelding 2: Azure AD-toepassings galerie*

1. Selecteer in het scherm voor het beheren van apps de optie **inrichten** in het linkerdeel venster.
1. Selecteer in het menu **inrichtings modus** de optie **automatisch**.

   ![voor beeld: de inrichtings pagina van een app in de Azure Portal][2]<br/>
   *Afbeelding 3: inrichten configureren in de Azure Portal*

1. Voer in het veld **Tenant-URL** de URL in van het scim-eind punt van de toepassing. Voorbeeld: https://api.contoso.com/scim/
1. Als het SCIM-eind punt een OAuth Bearer-token van een andere uitgever dan Azure AD vereist, kopieert u het vereiste OAuth Bearer-token naar het veld optionele **geheime token** . Als dit veld leeg blijft, bevat Azure AD een OAuth Bearer-token dat is uitgegeven door Azure AD met elke aanvraag. Apps die gebruikmaken van Azure AD als id-provider kunnen dit door Azure AD uitgegeven token valideren.
1. Selecteer **verbinding testen** om te laten proberen Azure Active Directory verbinding te maken met het scim-eind punt. Als de poging mislukt, wordt er informatie over de fout weer gegeven.  

    > [!NOTE]
    > **Test verbindings** QUERY'S het scim-eind punt voor een gebruiker die niet bestaat, met behulp van een wille keurige GUID als de overeenkomende eigenschap die is geselecteerd in de Azure AD-configuratie. Het verwachte juiste antwoord is HTTP 200 OK met een leeg SCIM ListResponse-bericht.

1. Als de pogingen om verbinding te maken met de toepassing slagen, selecteert u **Opslaan** om de beheerders referenties op te slaan.
1. In de sectie **toewijzingen** zijn er twee sets met kenmerk toewijzingen: één voor gebruikers objecten en één voor groeps objecten. Selecteer elk item om de kenmerken te controleren die zijn gesynchroniseerd van Azure Active Directory naar uw app. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers en groepen in uw app voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren.

    > [!NOTE]
    > U kunt de synchronisatie van groeps objecten eventueel uitschakelen door de toewijzing groepen uit te scha kelen.

1. Onder **instellingen**definieert het **bereik** veld welke gebruikers en groepen worden gesynchroniseerd. Selecteer **alleen toegewezen gebruikers en groepen synchroniseren** (aanbevolen) om gebruikers en groepen te synchroniseren die zijn toegewezen op het tabblad **gebruikers en groepen** .
1. Wanneer de configuratie is voltooid, stelt u de **inrichtings status** **in op aan**.
1. Selecteer **Opslaan** om de Azure AD-inrichtings service te starten.
1. Als u alleen toegewezen gebruikers en groepen wilt synchroniseren (aanbevolen), selecteert u het tabblad **gebruikers en groepen** en wijst u de gebruikers of groepen toe die u wilt synchroniseren.

Zodra de eerste cyclus is gestart, kunt u **audit logboeken** selecteren in het linkerdeel venster om de voortgang te controleren. Hiermee worden alle acties weer gegeven die door de inrichtings service in uw app worden uitgevoerd. Zie [rapportage over het automatisch inrichten van gebruikers accounts](check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

> [!NOTE]
> De eerste cyclus duurt langer dan de volgende synchronisaties, wat ongeveer elke 40 minuten gebeurt, zolang de service wordt uitgevoerd.

**Uw toepassing publiceren in de Azure AD-toepassings galerie:**

Als u een toepassing bouwt die wordt gebruikt voor meer dan één Tenant, kunt u deze beschikbaar maken in de Azure AD-toepassings galerie. Dit maakt het eenvoudig voor organisaties om de toepassing te detecteren en inrichtingen te configureren. Het publiceren van uw app in de Azure AD-galerie en het beschikbaar maken van de inrichting voor anderen is eenvoudig. Bekijk de stappen die [hier](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)worden beschreven. 
## <a name="understanding-the-azure-ad-scim-implementation"></a>Informatie over de Azure AD SCIM-implementatie

Als u een toepassing bouwt die een SCIM 2,0-gebruikers beheer-API ondersteunt, wordt in dit gedeelte beschreven hoe de Azure AD SCIM-client wordt geïmplementeerd en hoe u de verwerking van de aanvraag en reacties van uw SCIM-protocol moet model leren. Nadat u uw SCIM-eind punt hebt geïmplementeerd, kunt u dit testen door de procedure te volgen die in de vorige sectie is beschreven.

Binnen de [SCIM 2,0-protocol specificatie](http://www.simplecloud.info/#Specification)moet uw toepassing aan de volgende vereisten voldoen:

* Biedt ondersteuning voor het maken van gebruikers, en optioneel ook groepen, zoals wordt bepaald door para graaf [3,3 van het scim-protocol](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Ondersteunt het wijzigen van gebruikers of groepen met PATCH aanvragen, zoals wordt bepaald door [de sectie 3.5.2 van het scim-protocol](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Biedt ondersteuning voor het ophalen van een bekende resource voor een eerder gemaakte gebruiker of groep, zoals wordt bepaald door [de sectie 3.4.1 van het scim-protocol](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Biedt ondersteuning voor het uitvoeren van query's op gebruikers of groepen, zoals wordt bepaald door sectie [3.4.2 van het scim-protocol](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Gebruikers worden standaard opgehaald door hun `id` en hebben een query uitgevoerd op de `username` en de `externalid`, en groepen worden door `displayName`opgevraagd.  
* Biedt ondersteuning voor het uitvoeren van query's op de gebruiker door de ID en per Manager, zoals wordt bepaald door de sectie 3.4.2 van het SCIM-protocol.  
* Ondersteunt het opvragen van groepen op ID en op lid, zoals wordt bepaald door de sectie 3.4.2 van het SCIM-protocol.  
* Hiermee wordt één Bearer-token geaccepteerd voor verificatie en autorisatie van Azure AD voor uw toepassing.

Volg deze algemene richt lijnen bij het implementeren van een SCIM-eind punt om te zorgen voor compatibiliteit met Azure AD:

* `id` is een vereiste eigenschap voor alle resources. Elk antwoord dat een resource retourneert, moet ervoor zorgen dat elke resource deze eigenschap bevat, met uitzonde ring van `ListResponse` met nul leden.
* De reactie op een query/filter aanvraag moet altijd een `ListResponse`zijn.
* Groepen zijn optioneel, maar worden alleen ondersteund als de implementatie van de SCIM PATCH aanvragen ondersteunt.
* Het is niet nodig om de volledige resource op te neemen in de reactie van de PATCH.
* Microsoft Azure AD maakt alleen gebruik van de volgende Opera tors:  
     - `eq`
     - `and`
* Geen hoofdletter gevoelige overeenkomst voor structurele elementen in SCIM vereist, met name PATCH `op` bewerking waarden, zoals gedefinieerd in https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD verzendt de waarden van ' op ' als `Add`, `Replace`en `Remove`.
* Microsoft Azure AD maakt aanvragen voor het ophalen van een wille keurige gebruiker en groep om ervoor te zorgen dat het eind punt en de referenties geldig zijn. Het wordt ook uitgevoerd als onderdeel van een **test verbindings** stroom in de [Azure Portal](https://portal.azure.com). 
* Het kenmerk waarin de resources kunnen worden opgevraagd, moet worden ingesteld als een overeenkomend kenmerk op de toepassing in de [Azure Portal](https://portal.azure.com). Zie voor meer informatie [aanpassen van kenmerk toewijzingen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings) voor het inrichten van gebruikers

### <a name="user-provisioning-and-de-provisioning"></a>Gebruikers inrichten en de inrichting ongedaan maken

In de volgende afbeelding ziet u de berichten die Azure Active Directory verzendt naar een SCIM-service om de levens cyclus van een gebruiker in de identiteits opslag van uw toepassing te beheren.  

![toont het inrichten en de inrichtings volgorde van de gebruiker][4]<br/>
*Afbeelding 4: gebruikers inrichten en de inrichtings volgorde ongedaan maken*

### <a name="group-provisioning-and-de-provisioning"></a>Groepen inrichten en de inrichting ongedaan maken

Groeps inrichting en het ongedaan maken van de inrichting zijn optioneel. Wanneer de volgende afbeelding is geïmplementeerd en ingeschakeld, ziet u de berichten die Azure AD verzendt naar een SCIM-service om de levens cyclus van een groep in de identiteits opslag van uw toepassing te beheren.  Deze berichten verschillen van de berichten over gebruikers op twee manieren:

* Bij aanvragen voor het ophalen van groepen wordt opgegeven dat het kenmerk members moet worden uitgesloten van alle resources die zijn opgegeven in reactie op de aanvraag.  
* Aanvragen om te bepalen of een verwijzings kenmerk een bepaalde waarde heeft aanvragen voor het kenmerk members.  

![toont de reeks voor het inrichten en de inrichtings volgorde van de groep][5]<br/>
*Afbeelding 5: groeps inrichting en de inrichtings volgorde*

### <a name="scim-protocol-requests-and-responses"></a>Aanvragen en antwoorden van het SCIM-Protocol
Deze sectie bevat voor beelden van SCIM-aanvragen die worden verzonden door de Azure AD SCIM-client en voor beelden van verwachte reacties. Voor de beste resultaten moet u de app coderen om deze aanvragen in deze indeling af te handelen en de verwachte reacties te verzenden.

> [!IMPORTANT]
> Zie [Wat gebeurt er tijdens het inrichten van gebruikers?](user-provisioning.md#what-happens-during-provisioning)voor meer informatie over hoe en wanneer de Azure AD User Provisioning-Service de bewerkingen verzendt die hieronder worden beschreven?.

- [Gebruikers bewerkingen](#user-operations)
  - [Gebruiker maken](#create-user)
    - [Aanvraag](#request)
    - [Antwoord](#response)
  - [Gebruiker ophalen](#get-user)
    - [Aanvraag](#request-1)
    - [Antwoord](#response-1)
  - [Gebruiker op query ophalen](#get-user-by-query)
    - [Aanvraag](#request-2)
    - [Antwoord](#response-2)
  - [Gebruiker ophalen op basis van query-nul resultaten](#get-user-by-query---zero-results)
    - [Aanvraag](#request-3)
    - [Antwoord](#response-3)
  - [Gebruiker bijwerken [eigenschappen met meerdere waarden]](#update-user-multi-valued-properties)
    - [Aanvraag](#request-4)
    - [Antwoord](#response-4)
  - [Gebruiker bijwerken [eigenschappen met één waarde]](#update-user-single-valued-properties)
    - [Aanvraag](#request-5)
    - [Antwoord](#response-5)
  - [Gebruiker verwijderen](#delete-user)
    - [Aanvraag](#request-6)
    - [Antwoord](#response-6)
- [Groeps bewerkingen](#group-operations)
  - [Groep maken](#create-group)
    - [Aanvraag](#request-7)
    - [Antwoord](#response-7)
  - [Groep ophalen](#get-group)
    - [Aanvraag](#request-8)
    - [Antwoord](#response-8)
  - [Groep op displayName ophalen](#get-group-by-displayname)
    - [Aanvraag](#request-9)
    - [Antwoord](#response-9)
  - [Update groep [niet-lideigenschappen]](#update-group-non-member-attributes)
    - [Aanvraag](#request-10)
    - [Antwoord](#response-10)
  - [Groep bijwerken [leden toevoegen]](#update-group-add-members)
    - [Aanvraag](#request-11)
    - [Antwoord](#response-11)
  - [Groep bijwerken [leden verwijderen]](#update-group-remove-members)
    - [Aanvraag](#request-12)
    - [Antwoord](#response-12)
  - [Groep verwijderen](#delete-group)
    - [Aanvraag](#request-13)
    - [Antwoord](#response-13)

### <a name="user-operations"></a>Gebruikers bewerkingen

* Gebruikers kunnen worden doorzocht op `userName`-of `email[type eq "work"]`-kenmerken.  

#### <a name="create-user"></a>Gebruiker maken

###### <a name="request"></a>Aanvraag

*/Users plaatsen*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Antwoord

*HTTP/1.1 201 gemaakt*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Gebruiker ophalen

###### <a name="request-1"></a>Schot
*/Users/5d48a0a8e9f04aa38008 ophalen* 

###### <a name="response-1"></a>Antwoord (gebruiker gevonden)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Aanvraag
*/Users/5171a35d82074e068ce2 ophalen* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Antwoord (gebruiker niet gevonden. Houd er rekening mee dat de details niet vereist zijn, alleen status.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Gebruiker op query ophalen

##### <a name="request-2"></a>Schot

*/Users ophalen? filter = gebruikers naam EQ "Test_User_dfeef4c5-5681 -4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Beantwoord

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Gebruiker ophalen op basis van query-nul resultaten

##### <a name="request-3"></a>Schot

*/Users ophalen? filter = gebruikers naam EQ "niet-bestaande gebruiker"*

##### <a name="response-3"></a>Beantwoord

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Gebruiker bijwerken [eigenschappen met meerdere waarden]

##### <a name="request-4"></a>Schot

*PATCH/users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response-4"></a>Beantwoord

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Gebruiker bijwerken [eigenschappen met één waarde]

##### <a name="request-5"></a>Schot

*PATCH/users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response-5"></a>Beantwoord

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="delete-user"></a>Gebruiker verwijderen

##### <a name="request-6"></a>Schot

*/Users/5171a35d82074e068ce2 HTTP/1.1 verwijderen*

##### <a name="response-6"></a>Beantwoord

*HTTP/1.1 204 geen inhoud*

### <a name="group-operations"></a>Groeps bewerkingen

* Groepen moeten altijd worden gemaakt met een lijst met lege leden.
* Groepen kunnen worden opgevraagd met het kenmerk `displayName`.
* Update naar de groeps PATCH-aanvraag moet resulteren in een *HTTP 204 geen inhoud* in het antwoord. Het retour neren van een hoofd tekst met een lijst met alle leden is niet aanbevolen.
* Het is niet nodig om te ondersteunen bij het retour neren van alle leden van de groep.

#### <a name="create-group"></a>Groep maken

##### <a name="request-7"></a>Schot

*POST/groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response-7"></a>Beantwoord

*HTTP/1.1 201 gemaakt*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Groep ophalen

##### <a name="request-8"></a>Schot

*/Groups/40734ae655284ad3abcc ophalen? excludedAttributes = leden HTTP/1.1*

##### <a name="response-8"></a>Beantwoord
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Groep op displayName ophalen

##### <a name="request-9"></a>Schot
*/Groups ophalen? excludedAttributes = leden & filter = DisplayName EQ "displayName" HTTP/1.1*

##### <a name="response-9"></a>Beantwoord

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Update groep [niet-lideigenschappen]

##### <a name="request-10"></a>Schot

*PATCH/groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response-10"></a>Beantwoord

*HTTP/1.1 204 geen inhoud*

### <a name="update-group-add-members"></a>Groep bijwerken [leden toevoegen]

##### <a name="request-11"></a>Schot

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-11"></a>Beantwoord

*HTTP/1.1 204 geen inhoud*

#### <a name="update-group-remove-members"></a>Groep bijwerken [leden verwijderen]

##### <a name="request-12"></a>Schot

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-12"></a>Beantwoord

*HTTP/1.1 204 geen inhoud*

#### <a name="delete-group"></a>Groep verwijderen

##### <a name="request-13"></a>Schot

*/Groups/cdb1ce18f65944079d37 HTTP/1.1 verwijderen*

##### <a name="response-13"></a>Beantwoord

*HTTP/1.1 204 geen inhoud*

## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Een SCIM-eind punt bouwen met micro soft CLI-bibliotheken

Als u een SCIM-webservice maakt die interfaces met Azure Active Directory, kunt u automatische gebruikers inrichting inschakelen voor vrijwel elke toepassing of identiteits opslag.

Dit werkt als volgt:

1. Azure AD biedt een CLI-bibliotheek (Common Language Infrastructure) met de naam micro soft. SystemForCrossDomainIdentityManagement, die is opgenomen in de onderstaande code voorbeelden. Systeem integrators en ontwikkel aars kunnen deze bibliotheek gebruiken om een SCIM-webservice-eind punt te maken en te implementeren waarmee Azure AD kan worden verbonden met de identiteits opslag van elke toepassing.
2. Toewijzingen worden in de webservice geïmplementeerd om het gestandaardiseerde gebruikers schema toe te wijzen aan het gebruikers schema en het protocol dat door de toepassing wordt vereist. 
3. De eind punt-URL wordt geregistreerd in azure AD als onderdeel van een aangepaste toepassing in de toepassings galerie.
4. Gebruikers en groepen worden toegewezen aan deze toepassing in azure AD. Na toewijzing worden ze in een wachtrij geplaatst om te worden gesynchroniseerd met de doel toepassing. Het synchronisatie proces dat de wachtrij verwerkt, wordt elke 40 minuten uitgevoerd.

### <a name="code-samples"></a>Codevoorbeelden

Om dit proces gemakkelijker te maken, worden er [code voorbeelden](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) gegeven, waarmee een scim-webservice-eind punt wordt gemaakt en automatische inrichting wordt gedemonstreerd. Het voor beeld is van een provider die een bestand onderhoudt met rijen door komma's gescheiden waarden die gebruikers en groepen vertegenwoordigen.

**Vereisten**

* Visual Studio 2013 of hoger
* [Azure-SDK voor .NET](https://azure.microsoft.com/downloads/)
* Windows-machine die ondersteuning biedt voor het ASP.NET Framework 4,5, moet worden gebruikt als het SCIM-eind punt. Deze computer moet toegankelijk zijn vanuit de Cloud.
* [Een Azure-abonnement met een proef-of gelicentieerde versie van Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Aan de slag

De eenvoudigste manier om een SCIM-eind punt te implementeren dat inrichtings aanvragen van Azure AD accepteert, is het maken en implementeren van het code voorbeeld waarmee de ingerichte gebruikers worden uitgevoerd naar een bestand met door komma's gescheiden waarden (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Een voor beeld-SCIM-eind punt maken

1. Down load het voorbeeld pakket voor de code op [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Pak het pakket uit en plaats het op de Windows-computer op een locatie zoals C:\AzureAD-BYOA-Provisioning-Samples\.
1. In deze map opent u het FileProvisioning\Host\FileProvisioningService.csproj-project in Visual Studio.
1. Selecteer **Hulpprogram ma's** > **NuGet package manager** > **Package Manager-console**en voer de volgende opdrachten uit voor het FileProvisioningService-project om de oplossings referenties op te lossen:

   ```powershell
    Update-Package -Reinstall
   ```

1. Bouw het FileProvisioningService-project.
1. Start de opdracht prompt toepassing in Windows (als beheerder) en gebruik de **cd** -opdracht om de map te wijzigen in de map **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug** .
1. Voer de volgende opdracht uit en vervang `<ip-address>` door het IP-adres of de domein naam van de Windows-computer:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. Selecteer in Windows onder **Windows-instellingen** > **netwerk & Internet instellingen**de optie **Windows Firewall** > **Geavanceerde instellingen**en maak een **regel** voor binnenkomend verkeer waarmee inkomende toegang tot poort 9000 wordt toegestaan.
1. Als de Windows-computer zich achter een router bevindt, moet de router worden geconfigureerd voor het uitvoeren van de vertaling van netwerk toegang tussen de poort 9000 die wordt blootgesteld aan Internet en poort 9000 op de Windows-computer. Deze configuratie is vereist voor Azure AD om toegang te krijgen tot dit eind punt in de Cloud.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Het SCIM-eind punt voor het voor beeld registreren in azure AD

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com). 
1. Selecteer **bedrijfs toepassingen** in het linkerdeel venster. Er wordt een lijst met alle geconfigureerde apps weer gegeven, met inbegrip van apps die zijn toegevoegd vanuit de galerie.
1. Selecteer **+ nieuwe toepassing** > **alle** > **niet-galerie toepassing**.
1. Voer een naam in voor uw toepassing en selecteer **toevoegen** om een app-object te maken. Het gemaakte toepassings object is bedoeld om de doel-app weer te geven die u zou inrichten voor en het implementeren van eenmalige aanmelding voor en niet alleen het SCIM-eind punt.
1. Selecteer in het scherm voor het beheren van apps de optie **inrichten** in het linkerdeel venster.
1. Selecteer in het menu **inrichtings modus** de optie **automatisch**.    
1. Voer in het veld **Tenant-URL** de URL in van het scim-eind punt van de toepassing. Voorbeeld: https://api.contoso.com/scim/

1. Als het SCIM-eind punt een OAuth Bearer-token van een andere uitgever dan Azure AD vereist, kopieert u het vereiste OAuth Bearer-token naar het veld optionele **geheime token** . Als dit veld leeg blijft, bevat Azure AD een OAuth Bearer-token dat is uitgegeven door Azure AD met elke aanvraag. Apps die gebruikmaken van Azure AD als id-provider kunnen dit door Azure AD uitgegeven token valideren.
1. Selecteer **verbinding testen** om te laten proberen Azure Active Directory verbinding te maken met het scim-eind punt. Als de poging mislukt, wordt er informatie over de fout weer gegeven.  

    > [!NOTE]
    > **Test verbindings** QUERY'S het scim-eind punt voor een gebruiker die niet bestaat, met behulp van een wille keurige GUID als de overeenkomende eigenschap die is geselecteerd in de Azure AD-configuratie. Het verwachte juiste antwoord is HTTP 200 OK met een leeg SCIM ListResponse-bericht

1. Als de pogingen om verbinding te maken met de toepassing slagen, selecteert u **Opslaan** om de beheerders referenties op te slaan.
1. In de sectie **toewijzingen** zijn er twee sets met kenmerk toewijzingen: één voor gebruikers objecten en één voor groeps objecten. Selecteer elk item om de kenmerken te controleren die zijn gesynchroniseerd van Azure Active Directory naar uw app. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers en groepen in uw app voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren.
1. Onder **instellingen**definieert het **bereik** veld welke gebruikers en of groepen worden gesynchroniseerd. Selecteer **alleen toegewezen gebruikers en groepen synchroniseren** (aanbevolen) om alleen gebruikers en groepen te synchroniseren die zijn toegewezen op het tabblad **gebruikers en groepen** .
1. Wanneer de configuratie is voltooid, stelt u de **inrichtings status** **in op aan**.
1. Selecteer **Opslaan** om de Azure AD-inrichtings service te starten.
1. Als u alleen toegewezen gebruikers en groepen wilt synchroniseren (aanbevolen), selecteert u het tabblad **gebruikers en groepen** en wijst u de gebruikers of groepen toe die u wilt synchroniseren.

Zodra de eerste cyclus is gestart, kunt u **audit logboeken** selecteren in het linkerdeel venster om de voortgang te controleren. Hiermee worden alle acties weer gegeven die door de inrichtings service in uw app worden uitgevoerd. Zie [rapportage over het automatisch inrichten van gebruikers accounts](check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

De laatste stap bij het controleren van het voor beeld is het openen van het TargetFile. CSV-bestand in de map \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug op de Windows-computer. Zodra het inrichtings proces is uitgevoerd, worden in dit bestand de details weer gegeven van alle toegewezen en ingerichte gebruikers en groepen.

### <a name="development-libraries"></a>Ontwikkel bibliotheken

Als u uw eigen webservice wilt ontwikkelen die voldoet aan de SCIM-specificatie, moet u eerst vertrouwd zijn met de volgende bibliotheken van micro soft om het ontwikkelings proces te versnellen:

* CLI-bibliotheken (Common Language Infrastructure) worden aangeboden voor gebruik met talen die zijn gebaseerd op die infra C#structuur, zoals. Een van deze bibliotheken, micro soft. SystemForCrossDomainIdentityManagement. service, declareert een interface, micro soft. SystemForCrossDomainIdentityManagement. IProvider, die wordt weer gegeven in de volgende afbeelding. Een ontwikkelaar die de bibliotheken gebruikt, implementeert die interface met een klasse die algemeen kan worden aangeduid als een provider. De-bibliotheken kunnen door de ontwikkelaar een webservice implementeren die voldoet aan de SCIM-specificatie. De webservice kan worden gehost in Internet Information Services of een uitvoer bare CLI-assembly. De aanvraag wordt omgezet in aanroepen naar de methoden van de provider, die door de ontwikkelaar zijn geprogrammeerd voor het uitvoeren van een archief met identiteiten.
  
   ![Uitsplitsing: een aanvraag die is vertaald naar de methoden van de provider][3]
  
* [Express route-handlers](https://expressjs.com/guide/routing.html) zijn beschikbaar voor het parseren van node. js-aanvraag objecten die aanroepen vertegenwoordigen (zoals gedefinieerd door de scim-specificatie), gemaakt in een node. js-webservice.

### <a name="building-a-custom-scim-endpoint"></a>Een aangepast SCIM-eind punt bouwen

Ontwikkel aars die gebruikmaken van de CLI-bibliotheken kunnen hun services hosten binnen elke uitvoer bare CLI-assembly of binnen Internet Information Services. Hier volgt een voorbeeld code voor het hosten van een service binnen een uitvoer bare assembly, op het adres http://localhost:9000: 

   ```csharp
    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }
   ```

Deze service moet een HTTP-adres en Server verificatie certificaat hebben waarvan de basis certificerings instantie een van de volgende namen is: 

* CNNIC
* Comodo
* Cyber Trust
* DigiCert
* GeoTrust
* GlobalSign
* Ga Daddy
* VeriSign
* WoSign

Een certificaat voor Server verificatie kan worden gebonden aan een poort op een Windows-host met behulp van het hulp programma voor netwerk shell: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

De waarde die is gegeven voor het argument certhash is de vinger afdruk van het certificaat, terwijl de waarde die is gegeven voor het argument AppID een wille keurig Globally Unique Identifier is.  

Als u de service in Internet Information Services wilt hosten, bouwt een ontwikkelaar een CLI-code bibliotheek-assembly met een klasse met de naam Startup in de standaard naam ruimte van de assembly.  Hier volgt een voor beeld van een dergelijke klasse: 

   ```csharp
    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }
   ```

### <a name="handling-endpoint-authentication"></a>Verificatie van eind punten afhandelen

Aanvragen van Azure Active Directory een OAuth 2,0 Bearer-token bevatten.   Alle services die de aanvraag ontvangen, moeten de verlener verifiëren als Azure Active Directory voor de verwachte Azure Active Directory Tenant, voor toegang tot de Azure Active Directory Graph-webservice.  In het token wordt de verlener geïdentificeerd door een ISS-claim, zoals "ISS": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  In dit voor beeld is het basis adres van de claim waarde, https://sts.windows.net, Azure Active Directory als verlener geïdentificeerd, terwijl het relatieve adres segment cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 een unieke id is van de Azure Active Directory Tenant waarvoor de het token is uitgegeven. De doel groep van het token is de toepassings sjabloon-ID voor de app in de galerie. De toepassings sjabloon-ID voor alle aangepaste apps is 8adf8e6e-67b2-4cf2-A259-e3dc5476c621. De toepassings sjabloon-ID voor elke app in de galerie varieert. Neem contact op met ProvisioningFeedback@microsoft.com voor vragen over de toepassings sjabloon-ID voor een galerie toepassing. Elk van de toepassingen die in één Tenant zijn geregistreerd, kan dezelfde `iss` claim ontvangen met SCIM-aanvragen.

Ontwikkel aars die gebruikmaken van de CLI-bibliotheken van micro soft voor het bouwen van een SCIM-service kunnen aanvragen van Azure Active Directory met het pakket micro soft. Owin. Security. ActiveDirectory verifiëren door de volgende stappen te volgen: 

1. Implementeer in een provider de eigenschap micro soft. SystemForCrossDomainIdentityManagement. IProvider. StartupBehavior door het retour neren van een methode die wordt aangeroepen wanneer de service wordt gestart: 

   ```csharp
     public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

1. Voeg de volgende code toe aan deze methode om een aanvraag naar een van de eind punten van de service te laten verifiëren als een token dat is uitgegeven door Azure Active Directory voor een opgegeven Tenant, voor toegang tot de Azure AD Graph-webservice: 

   ```csharp
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Inrichting en ongedaan maken van de inrichting van gebruikers afhandelen

1. Azure Active Directory vraagt de service om een gebruiker met een externalId-kenmerk waarde die overeenkomt met de kenmerk waarde mailnickname van een gebruiker in azure AD. De query wordt weer gegeven als een Hypertext Transfer Protocol (HTTP)-aanvraag, zoals dit voor beeld, waarbij jyoung een voor beeld is van een mailnickname van een gebruiker in Azure Active Directory.

    >[!NOTE]
    > Dit is een voor beeld. Niet alle gebruikers hebben een mailnickname-kenmerk en de waarde die een gebruiker heeft, is mogelijk niet uniek in de Directory. Het kenmerk dat wordt gebruikt voor het vergelijken (in dit geval externalId) kan ook worden geconfigureerd in de [kenmerk toewijzingen van Azure AD](customize-application-attributes.md).

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   Als de service is gebouwd met behulp van de CLI-bibliotheken die door micro soft zijn meegeleverd voor het implementeren van SCIM Services, wordt de aanvraag omgezet in een aanroep van de query methode van de provider van de service.  Dit is de hand tekening van deze methode: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ```
   Hier volgt de definitie van de interface Micro soft. SystemForCrossDomainIdentityManagement. IQueryParameters: 
   ```csharp
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }
   ```

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   Als de service is gebouwd met behulp van de gemeen schappelijke taal infrastructuur bibliotheken van micro soft voor het implementeren van SCIM Services, wordt de aanvraag omgezet in een aanroep van de query methode van de provider van de service.  Dit is de hand tekening van deze methode: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Hier volgt de definitie van de interface Micro soft. SystemForCrossDomainIdentityManagement. IQueryParameters: 

   ```csharp
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   In het volgende voor beeld van een query voor een gebruiker met een opgegeven waarde voor het kenmerk externalId, zijn de waarden van de argumenten die zijn door gegeven aan de query methode: 
   * instellen. AlternateFilters. Count: 1
   * instellen. AlternateFilters. ElementAt (0). AttributePath: "externalId"
   * instellen. AlternateFilters. ElementAt (0). ComparisonOperator: ComparisonOperator. is gelijk aan
   * instellen. AlternateFilter. ElementAt (0). ComparisonValue: "jyoung"
   * correlationIdentifier: System .net. http. HttpRequestMessage. GetOwinEnvironment ["owin. Aanvraag-+ '] 

1. Als het antwoord op een query naar de webservice voor een gebruiker met een externalId-kenmerk waarde die overeenkomt met de kenmerk waarde mailnickname van een gebruiker geen gebruikers retourneert, worden Azure Active Directory aanvragen dat de service een gebruiker inricht die overeenkomt met de-server in Azure Active Directory.  Hier volgt een voor beeld van een dergelijke aanvraag: 

   ```
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
   ```
   De CLI-bibliotheken die door micro soft worden verschaft voor het implementeren van SCIM Services, vertalen die aanvraag naar een aanroep van de methode Create van de provider van de service.  De methode Create heeft deze hand tekening: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
   ```
   In een aanvraag om een gebruiker in te richten, is de waarde van het argument resource een exemplaar van de micro soft. SystemForCrossDomainIdentityManagement. De klasse Core2EnterpriseUser, die is gedefinieerd in de bibliotheek micro soft. SystemForCrossDomainIdentityManagement. schemas.  Als de aanvraag om de gebruiker in te richten slaagt, wordt de implementatie van de methode verwacht om een exemplaar van de micro soft. SystemForCrossDomainIdentityManagement te retour neren. Klasse Core2EnterpriseUser, waarbij de waarde van de eigenschap identifier is ingesteld op de unieke id van de nieuwe ingerichte gebruiker.  

1. Als u een gebruiker wilt bijwerken waarvan bekend is dat deze bestaat in een identiteits opslag die wordt gebruikt door een SCIM, wordt Azure Active Directory teruggevraagd door de huidige status van die gebruiker aan te vragen bij de service met een aanvraag zoals: 
   ```
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ```
   In een service die is gebouwd met behulp van de CLI-bibliotheken die door micro soft worden meegeleverd voor het implementeren van SCIM Services, wordt de aanvraag omgezet in een aanroep van de methode ophalen van de provider van de service.  Dit is de hand tekening van de methode ophalen: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
   ```
   In het voor beeld van een aanvraag voor het ophalen van de huidige status van een gebruiker, zijn de waarden van de eigenschappen van het object die zijn opgegeven als de waarde van het argument para meters als volgt: 
  
   * Id: ' 54D382A4-2050-4C03-94D1-E769F1D15682 '
   * SchemaIdentifier: "urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: User"

1. Als een referentie kenmerk moet worden bijgewerkt, wordt door Azure Active Directory de service gevraagd om te bepalen of de huidige waarde van het referentie kenmerk in de identiteits opslag die door de service wordt aangestuurd, al overeenkomt met de waarde van dat kenmerk in azure Active Uitvoermap. Voor gebruikers is het kenmerk alleen van de Manager van het kenmerk waarvan de huidige waarde op deze manier wordt opgevraagd. Hier volgt een voor beeld van een aanvraag om te bepalen of het kenmerk Manager van een bepaald gebruikers object momenteel een bepaalde waarde heeft: 

   Als de service is gebouwd met behulp van de CLI-bibliotheken die door micro soft zijn meegeleverd voor het implementeren van SCIM Services, wordt de aanvraag omgezet in een aanroep van de query methode van de provider van de service. De waarde van de eigenschappen van het object die wordt opgegeven als de waarde van het argument para meters, zijn als volgt: 
  
   * instellen. AlternateFilters. Count: 2
   * instellen. AlternateFilters. ElementAt (x). AttributePath: "ID"
   * instellen. AlternateFilters. ElementAt (x). ComparisonOperator: ComparisonOperator. is gelijk aan
   * instellen. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * instellen. AlternateFilters. ElementAt (y). AttributePath: "Manager"
   * instellen. AlternateFilters. ElementAt (y). ComparisonOperator: ComparisonOperator. is gelijk aan
   * instellen. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * instellen. RequestedAttributePaths. ElementAt (0): "ID"
   * instellen. SchemaIdentifier: "urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: User"

   Hier kan de waarde van de index x 0 zijn en de waarde van de index y kan 1 zijn, of de waarde van x kan 1 zijn, en de waarde van y kan 0 zijn, afhankelijk van de volg orde van de expressies van de filter query parameter.   

1. Hier volgt een voor beeld van een aanvraag van Azure Active Directory naar een SCIM-service om een gebruiker bij te werken: 
   ```
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```
   Met de micro soft CLI-bibliotheken voor het implementeren van SCIM Services wordt de aanvraag omgezet in een aanroep van de update-methode van de provider van de service. Dit is de hand tekening van de methode Update: 
   ```csharp
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}
   ```

   Als de service is gebouwd met behulp van de gemeen schappelijke taal infrastructuur bibliotheken van micro soft voor het implementeren van SCIM Services, wordt de aanvraag omgezet in een aanroep van de query methode van de provider van de service. De waarde van de eigenschappen van het object die wordt opgegeven als de waarde van het argument para meters, zijn als volgt: 
  
* instellen. AlternateFilters. Count: 2
* instellen. AlternateFilters. ElementAt (x). AttributePath: "ID"
* instellen. AlternateFilters. ElementAt (x). ComparisonOperator: ComparisonOperator. is gelijk aan
* instellen. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* instellen. AlternateFilters. ElementAt (y). AttributePath: "Manager"
* instellen. AlternateFilters. ElementAt (y). ComparisonOperator: ComparisonOperator. is gelijk aan
* instellen. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* instellen. RequestedAttributePaths. ElementAt (0): "ID"
* instellen. SchemaIdentifier: "urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: User"

  Hier kan de waarde van de index x 0 zijn en de waarde van de index y kan 1 zijn, of de waarde van x kan 1 zijn, en de waarde van y kan 0 zijn, afhankelijk van de volg orde van de expressies van de filter query parameter.   

1. Hier volgt een voor beeld van een aanvraag van Azure Active Directory naar een SCIM-service om een gebruiker bij te werken: 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
     {
       "schemas": 
       [
         "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
       "Operations":
       [
         {
           "op":"Add",
           "path":"manager",
           "value":
             [
               {
                 "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                 "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```

   De gemeen schappelijke taal infrastructuur bibliotheken van micro soft voor de implementatie van SCIM Services omzetten de aanvraag in een aanroep van de update methode van de provider van de service. Dit is de hand tekening van de methode Update: 

   ```csharp
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
   ```

    In het voor beeld van een aanvraag om een gebruiker bij te werken, heeft het object dat wordt gegeven als de waarde van het argument patch deze eigenschaps waarden: 
  
   * ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier. SchemaIdentifier: "urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: User"
   * (PatchRequest als PatchRequest2). Bewerkingen. aantal: 1
   * (PatchRequest als PatchRequest2). Bewerkingen. ElementAt (0). Operationname: Operationname. add
   * (PatchRequest als PatchRequest2). Bewerkingen. ElementAt (0). Path. AttributePath: "Manager"
   * (PatchRequest als PatchRequest2). Bewerkingen. ElementAt (0). Waarde. Count: 1
   * (PatchRequest als PatchRequest2). Bewerkingen. ElementAt (0). Waarde. ElementAt (0). Verwijzing: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest als PatchRequest2). Bewerkingen. ElementAt (0). Waarde. ElementAt (0). Waarde: 2819c223-7f76-453a-919d-413861904646

1. Om het inrichten van een gebruiker uit een identiteits opslag voor een SCIM-service ongedaan te maken, verzendt Azure AD een aanvraag, zoals: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Als de service is gebouwd met behulp van de gemeen schappelijke taal infrastructuur bibliotheken van micro soft voor het implementeren van SCIM Services, wordt de aanvraag omgezet in een aanroep van de methode Delete van de provider van de service.   Deze methode heeft deze hand tekening: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   Het object dat als waarde voor het argument resourceIdentifier wordt gegeven, heeft deze eigenschaps waarden in het voor beeld van een aanvraag om de inrichting van een gebruiker ongedaan te maken: 

1. Om het inrichten van een gebruiker uit een identiteits opslag voor een SCIM-service ongedaan te maken, verzendt Azure AD een aanvraag, zoals: 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Als de service is gebouwd met behulp van de CLI-bibliotheken die door micro soft zijn meegeleverd voor het implementeren van SCIM Services, wordt de aanvraag omgezet in een aanroep van de methode Delete van de provider van de service.   Deze methode heeft deze hand tekening: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   Het object dat als waarde voor het argument resourceIdentifier wordt gegeven, heeft deze eigenschaps waarden in het voor beeld van een aanvraag om de inrichting van een gebruiker ongedaan te maken: 
  
   * ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier. SchemaIdentifier: "urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: User"

## <a name="user-and-group-schema-reference"></a>Verwijzing naar gebruikers-en groeps schema

Azure Active Directory kunt twee soorten resources inrichten voor SCIM-webservices.  Deze typen resources zijn gebruikers en groepen.  

Gebruikers resources worden geïdentificeerd aan de hand van de schema-ID, `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, die is opgenomen in deze protocol specificatie: https://tools.ietf.org/html/rfc7643.  De standaard toewijzing van de kenmerken van gebruikers in Azure Active Directory aan de kenmerken van gebruikers bronnen is in tabel 1 gegeven.  

Groeps resources worden geïdentificeerd aan de hand van de schema-ID, `urn:ietf:params:scim:schemas:core:2.0:Group`. In tabel 2 ziet u de standaard toewijzing van de kenmerken van groepen in Azure Active Directory aan de kenmerken van groeps resources.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabel 1: standaard toewijzing van gebruikers kenmerken

| Azure Active Directory gebruiker | "urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: User" |
| --- | --- |
| IsSoftDeleted |actief |
| displayName |displayName |
| Fax-TelephoneNumber |phoneNumbers [type EQ "fax"]. waarde |
| GivenName |name. naam |
| JobTitle |titel |
| mail |e-mail berichten [type EQ "werk]. waarde |
| MailNickname |externalId |
| Manager |Manager |
| provider |phoneNumbers [type EQ "Mobile"]. waarde |
| Id |Id |
| Code |adressen [type EQ "werk]. post code |
| proxy-adressen |e-mail berichten [type EQ "Overig"]. Value |
| fysieke levering-Office-locatie |adressen [type EQ "other"]. Opgemaakt |
| streetAddress |adressen [type EQ "werk]. streetAddress |
| surname |naam. familielid |
| telefoon nummer |phoneNumbers [type EQ "werk]. waarde |
| gebruiker-principalnaam |Gebruikers |

### <a name="table-2-default-group-attribute-mapping"></a>Tabel 2: standaard toewijzing van groeps kenmerken

| Azure Active Directory groep | urn: IETF: params: scim: schemas: kern: 2.0: groep |
| --- | --- |
| displayName |externalId |
| mail |e-mail berichten [type EQ "werk]. waarde |
| MailNickname |displayName |
| producten |producten |
| Id |Id |
| proxyAddresses |e-mail berichten [type EQ "Overig"]. Value |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>IP-adressen die worden gebruikt door de Azure AD-inrichtings service toestaan om SCIM-aanvragen te maken

Bepaalde apps staan inkomend verkeer naar hun app toe. De Azure AD-inrichtings service werkt alleen zoals verwacht als de gebruikte IP-adressen zijn toegestaan. Zie voor een lijst met IP-adressen voor elke servicetag/regio het JSON-bestand- [Azure IP-bereiken en de service Tags – open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519). U kunt deze Ip's naar behoefte downloaden en Program meren in uw firewall. De gereserveerde IP-bereiken voor Azure AD-inrichting vindt u onder ' AzureActiveDirectoryDomainServices '.

## <a name="related-articles"></a>Verwante artikelen:

* [Gebruikers inrichting en ongedaan maken van de inrichting van SaaS-apps automatiseren](user-provisioning.md)
* [Kenmerk toewijzingen aanpassen voor het inrichten van gebruikers](customize-application-attributes.md)
* [Expressies schrijven voor kenmerk toewijzingen](functions-for-customizing-application-data.md)
* [Filters voor het inrichten van gebruikers in bereik](define-conditional-rules-for-provisioning-user-accounts.md)
* [Meldingen voor het inrichten van accounts](user-provisioning.md)
* [Lijst met zelf studies voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
