---
title: Een SCIM-eindpunt ontwikkelen voor gebruikersvoorziening voor apps vanuit Azure AD
description: System for Cross-domain Identity Management (SCIM) standaardiseert automatische gebruikersinrichting. Leer een SCIM-eindpunt te ontwikkelen, integreer uw SCIM-API met Azure Active Directory en automatiseerders en groepen in uw cloudtoepassingen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0507989ec25db595a85b89f15d8ff7d056a970f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297684"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Een SCIM-eindpunt maken en gebruikersinrichting configureren met Azure Active Directory (Azure AD)

Als toepassingsontwikkelaar u de SCIM-gebruikersbeheer-API (System for Cross-Domain Identity Management) gebruiken om automatische inrichting van gebruikers en groepen tussen uw toepassing en Azure AD mogelijk te maken. In dit artikel wordt beschreven hoe u een SCIM-eindpunt bouwen en integreren met de Azure AD-inrichtingsservice. De SCIM-specificatie biedt een gemeenschappelijk gebruikersschema voor inprovisioning. In combinatie met federatiestandaarden zoals SAML of OpenID Connect biedt SCIM beheerders een end-to-end, op standaarden gebaseerde oplossing voor toegangsbeheer.

SCIM is een gestandaardiseerde definitie van twee eindpunten: een eindpunt /Gebruikers en een /Groups eindpunt. Het gebruikt veelvoorkomende REST-werkwoorden om objecten te maken, bij te werken en te verwijderen, en een vooraf gedefinieerd schema voor algemene kenmerken zoals groepsnaam, gebruikersnaam, voornaam, achternaam en e-mail. Apps die een SCIM 2.0 REST API aanbieden, kunnen de pijn van het werken met een eigen API voor gebruikersbeheer verminderen of elimineren. Elke compatibele SCIM-client weet bijvoorbeeld hoe u een HTTP-POST van een JSON-object maken met het eindpunt /Gebruikers om een nieuwe gebruikersvermelding te maken. In plaats van een iets andere API nodig te hebben voor dezelfde basisacties, kunnen apps die voldoen aan de SCIM-standaard direct profiteren van reeds bestaande clients, hulpprogramma's en code. 

![Inrichten van Azure AD naar een app met SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Met het standaard gebruikersobjectschema en rust-API's voor beheer gedefinieerd in SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644)](https://tools.ietf.org/html/rfc7644)kunnen identiteitsproviders en apps gemakkelijker met elkaar worden geïntegreerd. Toepassingsontwikkelaars die een SCIM-eindpunt bouwen, kunnen integreren met elke SCIM-compatibele client zonder dat ze op maat hoeven te werken.

Voor het automatiseren van de inrichting van een toepassing is het bouwen en integreren van een SCIM-eindpunt met de Azure AD SCIM-client vereist. Voer de volgende stappen uit om gebruikers en groepen in uw toepassing in te richten. 
    
  * **[Stap 1: Ontwerp uw gebruikers- en groepsschema.](#step-1-design-your-user-and-group-schema)** Identificeer de objecten en kenmerken die uw toepassingsbehoeften hebben en bepaal hoe ze worden toegewezen aan het gebruikers- en groepsschema dat wordt ondersteund door de Azure AD SCIM-implementatie.

  * **[Stap 2: De Implementatie van Azure AD SCIM begrijpen.](#step-2-understand-the-azure-ad-scim-implementation)** Begrijpen hoe de Azure AD SCIM-client is geïmplementeerd en modelleer de afhandeling en reacties van uw SCIM-protocolaanvraag.

  * **[Stap 3: Bouw een SCIM-eindpunt.](#step-3-build-a-scim-endpoint)** Een eindpunt moet SCIM 2.0-compatibel zijn om te integreren met de Azure AD-inrichtingsservice. Als optie u CLI-bibliotheken (Microsoft Common Language Infrastructure) en codevoorbeelden gebruiken om uw eindpunt te bouwen. Deze monsters zijn alleen ter referentie en test; we raden u aan uw productie-app niet te coderen om er afhankelijk van te zijn.

  * **[Stap 4: Integreer uw SCIM-eindpunt met de Azure AD SCIM-client.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Als uw organisatie een toepassing van derden gebruikt die het profiel van SCIM 2.0 implementeert dat Azure AD ondersteunt, u meteen beginnen met het automatiseren van zowel het inrichten als deprovisioning van gebruikers en groepen.

  * **[Stap 5: Uw toepassing publiceren in de azure AD-toepassingsgalerie.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Maak het klanten gemakkelijk om uw toepassing te ontdekken en eenvoudig inrichten te configureren. 

![Stappen voor de integratie van een SCIM-eindpunt met Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Stap 1: Uw gebruikers- en groepsschema ontwerpen

Elke toepassing vereist verschillende kenmerken om een gebruiker of groep te maken. Start uw integratie door de objecten (gebruikers, groepen) en kenmerken (naam, manager, functietitel, enz.) te identificeren die uw toepassing vereist. De SCIM-standaard definieert een schema voor het beheren van gebruikers en groepen. Het kerngebruikersschema vereist slechts drie kenmerken: **id** (door de serviceprovider gedefinieerde id), **externalId** (client defined identifier) en **meta** (alleen-lezen meta's die door de serviceprovider worden onderhouden). Alle andere kenmerken zijn optioneel. Naast het kerngebruikersschema definieert de SCIM-standaard een bedrijfsgebruikersextensie en een model voor het uitbreiden van het gebruikersschema om aan de behoeften van uw toepassing te voldoen. Als uw toepassing bijvoorbeeld de manager van een gebruiker nodig heeft, u het bedrijfsgebruikersschema gebruiken om de manager van de gebruiker en het kernschema te verzamelen om de e-mail van de gebruiker te verzamelen. Volg de onderstaande stappen om uw schema te ontwerpen:
  1. Vermeld de kenmerken die uw toepassing vereist. Het kan handig zijn om uw vereisten op te splitsen in de kenmerken die nodig zijn voor verificatie (bijvoorbeeld loginName en e-mail), kenmerken die nodig zijn om de levenscyclus van de gebruiker te beheren (bijvoorbeeld status / actief) en andere kenmerken die nodig zijn om uw specifieke toepassing te laten werken (bijvoorbeeld manager, tag).
  2. Controleer of deze kenmerken al zijn gedefinieerd in het basisgebruikersschema of het bedrijfsgebruikersschema. Als u kenmerken hebt die u nodig hebt en die niet worden behandeld in de kern- of bedrijfsgebruikersschema's, moet u een extensie definiëren voor het gebruikersschema dat de kenmerken dekt die u nodig hebt. In het onderstaande voorbeeld hebben we een extensie aan de gebruiker toegevoegd om het inrichten van een "tag" op een gebruiker toe te staan. Het is het beste om te beginnen met alleen de kern- en bedrijfsgebruikersschema's en later uit te breiden naar aanvullende aangepaste schema's.  
  3. De SCIM-kenmerken toewijzen aan de gebruikerskenmerken in Azure AD. Als een van de kenmerken die u hebt gedefinieerd in uw SCIM-eindpunt geen duidelijke tegenhanger heeft op het Azure AD-gebruikersschema, is de kans groot dat de gegevens helemaal niet op het gebruikersobject zijn opgeslagen op de meeste tenants. Overweeg of dit kenmerk optioneel kan zijn voor het maken van een gebruiker. Als het kenmerk essentieel is dat uw toepassing werkt, leidt u de tenantbeheerder om zijn schema uit te breiden of gebruikt u een extensiekenmerk zoals hieronder wordt weergegeven voor de eigenschap 'tags'.

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabel 1: De kenmerken die u nodig hebt, schetsen 
| Stap 1: Kenmerken bepalen die uw app nodig heeft| Stap 2: App-vereisten toewijzen aan SCIM-standaard| Stap 3: SCIM-kenmerken toewijzen aan de Azure AD-kenmerken|
|--|--|--|
|Loginnaam|userName|userPrincipalName|
|voornaam|name.givenName|givenName|
|achternaam|name.achternaam|achternaam|
|workMail|E-mails[type eq "werk"].waarde|Mail|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schema's:extension:2.0:CustomExtension:tag|extensieAttribuut1|
|status|actief|isSoftDeleted (berekende waarde niet opgeslagen op gebruiker)|

Het schema hierboven gedefinieerd zou worden weergegeven met behulp van de Json payload hieronder. Houd er rekening mee dat de JSON-representatie naast de kenmerken die nodig zijn voor de toepassing, de vereiste 'id', 'externalId' en 'meta'-kenmerken bevat.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>Tabel 2: Toewijzing van standaardgebruikerskenmerken
U de onderstaande tabel vervolgens gebruiken om te begrijpen hoe de kenmerken die uw toepassing nodig heeft, kunnen toewijzen aan een kenmerk in Azure AD en de SCIM RFC. U [aanpassen](customize-application-attributes.md) hoe kenmerken worden toegewezen tussen Azure AD en uw SCIM-eindpunt. Houd er rekening mee dat u niet zowel gebruikers als groepen of alle onderstaande kenmerken hoeft te ondersteunen. Ze zijn een referentie voor hoe kenmerken in Azure AD vaak worden toegewezen aan eigenschappen in het SCIM-protocol. 

| Azure Active Directory-gebruiker | "urn:ietf:params:scim:schema's:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |actief |
|department|urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:department|
| displayName |displayName |
|Employeeid|urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-Telefoonnummer |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitel |titel |
| mail |e-mails[type eq "werk"].waarde |
| e-mailNickname |extern id |
| manager |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:manager |
| mobiel |phoneNumbers[type eq "mobile"].value |
| Postcode |adressen[type eq "werk"].postcode |
| proxy-adressen |e-mails[type eq "andere"]. Waarde |
| fysieke-Delivery-OfficeName |adressen[type eq "andere"]. Geformatteerd |
| streetAddress |adressen[type eq "werk"].streetAddress |
| surname |name.familyName |
| telefoonnummer |phoneNumbers[type eq "werk"].waarde |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabel 3: Toewijzing van standaardkenmerkkenmerken

| Azure Active Directory-groep | urn:ietf:params:scim:schema's:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |e-mails[type eq "werk"].waarde |
| e-mailNickname |displayName |
| leden |leden |
| objectId |extern id |
| proxyAddresses |e-mails[type eq "andere"]. Waarde |

Er zijn verschillende eindpunten gedefinieerd in de SCIM RFC. U aan de slag met het eindpunt /Gebruiker en vervolgens vanaf daar uitbreiden. Het eindpunt /Schema's is handig bij het gebruik van aangepaste kenmerken of als uw schema regelmatig verandert. Hiermee kan een client het meest up-to-date schema automatisch ophalen. Het /Bulk-eindpunt is vooral handig bij het ondersteunen van groepen. De onderstaande tabel beschrijft de verschillende eindpunten die zijn gedefinieerd in de SCIM-standaard. Het eindpunt /Schema's is handig bij het gebruik van aangepaste kenmerken of als uw schema regelmatig verandert. Hiermee kan een client het meest up-to-date schema automatisch ophalen. Het /Bulk-eindpunt is vooral handig bij het ondersteunen van groepen. De onderstaande tabel beschrijft de verschillende eindpunten die zijn gedefinieerd in de SCIM-standaard. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabel 4: Bepaal de eindpunten die u wilt ontwikkelen
|Eindpunt|BESCHRIJVING|
|--|--|
|/Gebruiker|Crud-bewerkingen uitvoeren op een gebruikersobject.|
|/Groep|Crud-bewerkingen uitvoeren op een groepsobject.|
|/ServiceProviderConfig|Geeft informatie over de functies van de SCIM-standaard die worden ondersteund, bijvoorbeeld de resources die worden ondersteund en de verificatiemethode.|
|/ResourceTypes|Hiermee geeft u metagegevens op over elke resource|
|/Schema's|De set kenmerken die door elke client en serviceprovider worden ondersteund, kan variëren. Terwijl de ene serviceprovider 'naam', 'titel' en 'e-mails' kan bevatten, terwijl een andere serviceprovider 'naam', 'titel' en 'telefoonnummers' gebruikt. Het eindpunt van de schema's maakt het mogelijk om de ondersteunde kenmerken te ontdekken.|
|/Bulk|Met bulkbewerkingen u bewerkingen uitvoeren op een grote verzameling resourceobjecten in één bewerking (bijvoorbeeld updatelidmaatschappen voor een grote groep).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Stap 2: Inzicht in de Azure AD SCIM-implementatie
> [!IMPORTANT]
> Het gedrag van de Azure AD SCIM-implementatie is voor het laatst bijgewerkt op 18 december 2018. Zie [SCIM 2.0-protocol naleving van de Azure AD User Provisioning-service](application-provisioning-config-problem-scim-compatibility.md)voor informatie over wat er is gewijzigd.

Als u een toepassing bouwt die een SCIM 2.0-gebruikersbeheer-API ondersteunt, wordt in deze sectie in detail beschreven hoe de Azure AD SCIM-client wordt geïmplementeerd. Het laat ook zien hoe u uw SCIM-protocol aanvraag verwerking en reacties model. Zodra u uw SCIM-eindpunt hebt geïmplementeerd, u het testen door de procedure te volgen die in de vorige sectie is beschreven.

Binnen de [SCIM 2.0 protocol specificatie](http://www.simplecloud.info/#Specification)moet uw toepassing aan deze eisen voldoen:

* Ondersteunt het maken van gebruikers, en optioneel ook groepen, volgens sectie [3.3 van het SCIM-protocol](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Ondersteunt het wijzigen van gebruikers of groepen met PATCH-aanvragen, volgens [punt 3.5.2 van het SCIM-protocol](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Ondersteunt het ophalen van een bekende bron voor een gebruiker of groep die eerder is gemaakt, volgens [sectie 3.4.1 van het SCIM-protocol](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Ondersteunt het opvragen van gebruikers of groepen, volgens punt [3.4.2 van het SCIM-protocol](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Standaard worden gebruikers opgehaald door `id` hun en `username` worden `externalid`ze opgevraagd door `displayName`hun en , en worden groepen opgevraagd door .  
* Ondersteunt het opvragen van gebruikers per id en per manager, volgens sectie 3.4.2 van het SCIM-protocol.  
* Ondersteunt querygroepen per id en per lid, volgens sectie 3.4.2 van het SCIM-protocol.  
* Accepteert één token aan toonder voor verificatie en autorisatie van Azure AD voor uw toepassing.

Volg deze algemene richtlijnen bij het implementeren van een SCIM-eindpunt om compatibiliteit met Azure AD te garanderen:

* `id`is een vereiste eigenschap voor alle middelen. Elk antwoord dat een resource retourneert, moet `ListResponse` ervoor zorgen dat elke resource deze eigenschap heeft, behalve met nul leden.
* Het antwoord op een query/filteraanvraag moet altijd een `ListResponse`.
* Groepen zijn optioneel, maar worden alleen ondersteund als de SCIM-implementatie PATCH-aanvragen ondersteunt.
* Het is niet nodig om de volledige resource op te nemen in de PATCH-respons.
* Microsoft Azure AD gebruikt alleen de volgende operatoren:  
    - `eq`
    - `and`
* Vereist geen hoofdlettergevoelige overeenkomst over structurele elementen in SCIM, met name patch-bewerkingswaarden, `op` zoals gedefinieerd in https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD zendt de waarden 'op' uit als `Add`, `Replace`en `Remove`.
* Microsoft Azure AD doet verzoeken om een willekeurige gebruiker en groep op te halen om ervoor te zorgen dat het eindpunt en de referenties geldig zijn. Het wordt ook gedaan als onderdeel van **testverbindingsstroom** in de [Azure-portal.](https://portal.azure.com) 
* Het kenmerk waarop de resources kunnen worden opgevraagd, moet worden ingesteld als een overeenkomend kenmerk op de toepassing in de [Azure-portal.](https://portal.azure.com) Zie [Toewijzingen voor gebruikersinrichting van gebruikers aanpassen](customize-application-attributes.md) voor meer informatie

### <a name="user-provisioning-and-deprovisioning"></a>Gebruikersinrichting en -deprovisioning

In de volgende afbeelding ziet u de berichten die Azure Active Directory naar een SCIM-service verzendt om de levenscyclus van een gebruiker in het identiteitsarchief van uw toepassing te beheren.  

![Toont de volgorde voor het inrichten en deprovisionen van de gebruiker](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Volgorde voor het inrichten en deprovisionen van gebruikers*

### <a name="group-provisioning-and-deprovisioning"></a>Groepsinrichting en deprovisioning

Groepsinrichting en deprovisioning zijn optioneel. Wanneer deze is geïmplementeerd en ingeschakeld, ziet u in de volgende afbeelding de berichten die Azure AD naar een SCIM-service verzendt om de levenscyclus van een groep in het identiteitsarchief van uw toepassing te beheren.  Deze berichten verschillen op twee manieren van de berichten over gebruikers:

* Verzoeken om groepen op te halen geven aan dat het kenmerk van de leden moet worden uitgesloten van een resource die is verstrekt als reactie op de aanvraag.  
* Verzoeken om te bepalen of een referentiekenmerk een bepaalde waarde heeft, zijn aanvragen over het kenmerk van de leden.  

![Toont de volgorde voor het inrichten en deprovisionen van de groep](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Groepsinrichting en deprovisioning-reeks*

### <a name="scim-protocol-requests-and-responses"></a>SCIM-protocolaanvragen en -antwoorden
In deze sectie vindt u bijvoorbeeld SCIM-aanvragen die worden uitgezonden door de Azure AD SCIM-client en voorbeeld verwachte antwoorden. Voor de beste resultaten moet u uw app coderen om deze aanvragen in deze indeling af te handelen en de verwachte antwoorden uit te zenden.

> [!IMPORTANT]
> Zie de sectie [Inrichtingscycli: Initiaal en incrementeel](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in hoe en wanneer de azure AD-gebruikersinrichtingsservice de onderstaande bewerkingen [uitzendt.](how-provisioning-works.md)

[Gebruikersbewerkingen](#user-operations)
  - [Gebruiker maken](#create-user) ([Antwoord op](#response)[aanvraag](#request) / )
  - [Gebruiker ontvangen](#get-user) ([Antwoord op](#response-1)[verzoek](#request-1) / )
  - [Gebruiker op query krijgen](#get-user-by-query) ([Antwoord op verzoek)](#request-2) / [Response](#response-2)
  - [Gebruiker op query krijgen - Nul resultaten](#get-user-by-query---zero-results) ([Antwoord](#response-3)[op verzoek](#request-3)
/ )
  - [Gebruiker bijwerken [Eigenschappen met meerdere waarden]](#update-user-multi-valued-properties) ([Antwoord op](#response-4)[verzoek](#request-4) /  )
  - [Gebruiker bijwerken [Eigenschappen met één waarde]](#update-user-single-valued-properties) ([Antwoord op](#response-5)[verzoek](#request-5)
/ ) 
  - [Gebruiker uitschakelen](#disable-user) ([Antwoord op](#response-14)[aanvraag](#request-14) / 
)
  - [Gebruiker verwijderen](#delete-user) ([Antwoord op](#response-6)[verzoek](#request-6) / 
)


[Groepsbewerkingen](#group-operations)
  - [Groep maken](#create-group) ([Antwoord op](#response-7) [aanvraag)](#request-7) / 
  - [Groep ontvangen](#get-group) ([Antwoord op](#response-8) [verzoek)](#request-8) / 
  - [Groep op displayName](#get-group-by-displayname)  / [(verzoeks)](#response-9)[Request](#request-9)
  - [Groep bijwerken [Niet-ledenkenmerken]](#update-group-non-member-attributes) ( [Antwoord op](#response-10)[verzoek](#request-10) /
 )
  - [Groep bijwerken [Leden toevoegen]](#update-group-add-members) ([Antwoord op](#response-11) [verzoek](#request-11) /
)
  - [Groep bijwerken [Leden verwijderen]](#update-group-remove-members) ([Antwoord op](#response-12) [verzoek](#request-12) /
)
  - [Groep verwijderen](#delete-group) ([Antwoord op](#response-13)[verzoek](#request-13) /
)

### <a name="user-operations"></a>Gebruikersbewerkingen

* Gebruikers kunnen worden `userName` opgevraagd door of `email[type eq "work"]` attributen.  

#### <a name="create-user"></a>Gebruiker maken

###### <a name="request"></a>Aanvraag

*POST /Gebruikers*
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

*HTTP/1.1 201 Gemaakt*
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

###### <a name="request"></a><a name="request-1"></a>Aanvraag
*GET /Gebruikers/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Reactie (gebruiker gevonden)
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
*GET /Gebruikers/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Reactie (Gebruiker niet gevonden. Houd er rekening mee dat de details niet vereist zijn, alleen status.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Gebruiker op query krijgen

##### <a name="request"></a><a name="request-2"></a>Aanvraag

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Reactie

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

#### <a name="get-user-by-query---zero-results"></a>Gebruiker op query krijgen - Nul resultaten

##### <a name="request"></a><a name="request-3"></a>Aanvraag

*GET / Gebruikers?filter=userName eq "niet-bestaande gebruiker"*

##### <a name="response"></a><a name="response-3"></a>Reactie

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

#### <a name="update-user-multi-valued-properties"></a>Gebruiker bijwerken [Eigenschappen met meerdere waarden]

##### <a name="request"></a><a name="request-4"></a>Aanvraag

*PATCH /Gebruikers/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response"></a><a name="response-4"></a>Reactie

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

#### <a name="update-user-single-valued-properties"></a>Gebruiker bijwerken [Eigenschappen met één waarde]

##### <a name="request"></a><a name="request-5"></a>Aanvraag

*PATCH /Gebruikers/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-5"></a>Reactie

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

### <a name="disable-user"></a>Gebruiker uitschakelen

##### <a name="request"></a><a name="request-14"></a>Aanvraag

*PATCH /Gebruikers/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Reactie

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Gebruiker verwijderen

##### <a name="request"></a><a name="request-6"></a>Aanvraag

*VERWIJDEREN /Gebruikers/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Reactie

*HTTP/1.1 204 Geen inhoud*

### <a name="group-operations"></a>Groepsbewerkingen

* Groepen worden altijd gemaakt met een lege ledenlijst.
* Groepen kunnen worden opgevraagd door het `displayName` kenmerk.
* Update naar de groep PATCH-aanvraag moet een *HTTP 204 Geen inhoud* opleveren in het antwoord. Het retourneren van een lichaam met een lijst van alle leden is niet aan te raden.
* Het is niet nodig om de terugkeer van alle leden van de groep te ondersteunen.

#### <a name="create-group"></a>Groep maken

##### <a name="request"></a><a name="request-7"></a>Aanvraag

*POST /Groepen HTTP/1.1*
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

##### <a name="response"></a><a name="response-7"></a>Reactie

*HTTP/1.1 201 Gemaakt*
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

##### <a name="request"></a><a name="request-8"></a>Aanvraag

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Reactie
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

#### <a name="get-group-by-displayname"></a>Groep op displayName krijgen

##### <a name="request"></a><a name="request-9"></a>Aanvraag
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Reactie

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

#### <a name="update-group-non-member-attributes"></a>Groep bijwerken [Niet-ledenkenmerken]

##### <a name="request"></a><a name="request-10"></a>Aanvraag

*PATCH /Groepen/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response"></a><a name="response-10"></a>Reactie

*HTTP/1.1 204 Geen inhoud*

### <a name="update-group-add-members"></a>Groep bijwerken [Leden toevoegen]

##### <a name="request"></a><a name="request-11"></a>Aanvraag

*PATCH /Groepen/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-11"></a>Reactie

*HTTP/1.1 204 Geen inhoud*

#### <a name="update-group-remove-members"></a>Groep bijwerken [Leden verwijderen]

##### <a name="request"></a><a name="request-12"></a>Aanvraag

*PATCH /Groepen/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-12"></a>Reactie

*HTTP/1.1 204 Geen inhoud*

#### <a name="delete-group"></a>Groep verwijderen

##### <a name="request"></a><a name="request-13"></a>Aanvraag

*VERWIJDEREN /Groepen/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Reactie

*HTTP/1.1 204 Geen inhoud*

### <a name="security-requirements"></a>Beveiligingseisen
**TLS-protocolversies**

De enige acceptabele TLS-protocolversies zijn TLS 1.2 en TLS 1.3. Andere versies van TLS zijn niet toegestaan. Er is geen versie van SSL toegestaan. 
- RSA-toetsen moeten ten minste 2.048 bits zijn.
- ECC-toetsen moeten ten minste 256 bits zijn, gegenereerd met behulp van een goedgekeurde elliptische curve


**Sleutellengtes**

Alle services moeten X.509-certificaten gebruiken die zijn gegenereerd met cryptografische sleutels van voldoende lengte, wat betekent:

**Cipher Suites**

Alle services moeten zijn geconfigureerd om de volgende cipher suites te gebruiken, in de exacte volgorde hieronder opgegeven. Houd er rekening mee dat als u alleen een RSA-certificaat hebt, de ECDSA-cijfersuites geen effect hebben. </br>

MINIMAAL TLS 1.2 Cipher Suites-balk:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Stap 3: Een SCIM-eindpunt bouwen

Nu u uw schema hebt ontworpen en de Azure AD SCIM-implementatie hebt begrepen, u aan de slag met het ontwikkelen van uw SCIM-eindpunt. In plaats van helemaal opnieuw te beginnen en de implementatie volledig alleen te bouwen, u vertrouwen op een aantal open source SCIM-bibliotheken die zijn gepubliceerd door de SCIM-gemeenschap.

De open source .NET [Core-referentiecode](https://aka.ms/SCIMReferenceCode) die is gepubliceerd door het Azure AD-inrichtingsteam, is een van die bronnen die uw ontwikkeling kunnen starten. Zodra u uw SCIM-eindpunt hebt gebouwd, wilt u het uittesten. U de verzameling [van postbodetests](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) gebruiken die als onderdeel van de referentiecode worden verstrekt of de [bovenstaande](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)voorbeeldaanvragen/antwoorden doorlopen.  

   > [!Note]
   > De referentiecode is bedoeld om u te helpen aan de slag te gaan met het bouwen van uw SCIM-eindpunt en wordt geleverd als 'AS IS'. Bijdragen van de gemeenschap zijn welkom om te helpen bij het opbouwen en onderhouden van de code.

De oplossing bestaat uit twee projecten, _Microsoft.SCIM_ en _Microsoft.SCIM.WebHostSample._

Het _Microsoft.SCIM-project_ is de bibliotheek die de onderdelen van de webservice definieert die voldoet aan de SCIM-specificatie. Het verklaart de interface _Microsoft.SCIM.IProvider_, verzoeken worden vertaald in oproepen naar de methoden van de provider, die zou worden geprogrammeerd om te werken op een identiteit op te slaan.

![Uitsplitsing: een aanvraag vertaald in oproepen naar de methoden van de provider](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Het _Microsoft.SCIM.WebHostSample-project_ is een Visual Studio ASP.NET Core Web Application, gebaseerd op de _sjabloon Leeg._ Hierdoor kan de voorbeeldcode worden geïmplementeerd als zelfstandige, gehost in containers of binnen Internet Information Services. Het implementeert ook de _Microsoft.SCIM.IProvider-interface_ die klassen in het geheugen bijhoudt als een voorbeeldidentiteitsarchief.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Een aangepast SCIM-eindpunt bouwen

De SCIM-service moet beschikken over een HTTP-adres- en serververificatiecertificaat waarvan de hoofdcertificeringsinstantie een van de volgende namen is:

* CNNIC (CNNIC)
* Comodo
* CyberTrust CyberTrust
* DigiCert
* GeoTrust (GeoTrust)
* GlobalSign
* Ga papa
* Verisign
* WoSign WoSign

De .NET Core SDK bevat een HTTPS-ontwikkelingscertificaat dat kan worden gebruikt tijdens de ontwikkeling, het certificaat wordt geïnstalleerd als onderdeel van de first-run ervaring. Afhankelijk van hoe u de ASP.NET Core Web Application uitvoert, luistert deze naar een andere poort:

* Microsoft.SCIM.WebHostVoorbeeld:https://localhost:5001
* IIS Express:https://localhost:44359/

Voor meer informatie over HTTPS in ASP.NET Core gebruik de volgende link: [Https afdwingen in ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Endpoint-verificatie verwerken

Aanvragen van Azure Active Directory bevatten een OAuth 2.0-token aan toonder. Elke service die de aanvraag ontvangt, moet de uitgever verifiëren als Azure Active Directory voor de verwachte Azure Active Directory-tenant.

In het token wordt de uitgever geïdentificeerd door `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`een iss-claim, zoals . In dit voorbeeld wordt azure Active `https://sts.windows.net`Directory geïdentificeerd als het basisadres van de claimwaarde als de uitgever, terwijl het relatieve adressegment _Cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_een unieke id is van de Azure Active Directory-tenant waarvoor het token is uitgegeven.

Het publiek voor het token is de toepassingssjabloon-ID voor de toepassing in de galerie, `iss` elk van de aanvragen die in één tenant zijn geregistreerd, kan dezelfde claim ontvangen met SCIM-verzoeken. De toepassingssjabloon-ID voor elke toepassing in [ProvisioningFeedback@microsoft.com](mailto:ProvisioningFeedback@microsoft.com) de galerie varieert, neem contact op voor vragen over de toepassingssjabloon-id voor een galerijtoepassing. De toepassingssjabloon-ID voor alle aangepaste apps is _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_.

In de voorbeeldcode worden aanvragen geverifieerd met het pakket Microsoft.AspNetCore.Authentication.JwtBearer. De volgende code dwingt af dat aanvragen voor een van de eindpunten van de service worden geverifieerd met behulp van het token aan toonder dat is uitgegeven door Azure Active Directory voor een opgegeven tenant:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Een token aan toonder is ook vereist om de meegeleverde [postmantests](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) te gebruiken en lokale foutopsporing uit te voeren met behulp van localhost. De voorbeeldcode gebruikt ASP.NET Core-omgevingen om de verificatieopties tijdens de ontwikkelingsfase te wijzigen en het gebruik van een zelfondertekend token in te schakelen.

Voor meer informatie over meerdere omgevingen in ASP.NET Core gebruik de volgende koppeling: [Gebruik meerdere omgevingen in ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

De volgende code dwingt af dat aanvragen voor een van de eindpunten van de service worden geverifieerd met behulp van een token aan toonder dat is ondertekend met een aangepaste sleutel:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

Stuur een GET-aanvraag naar de Token-controller om een geldig token aan toonder te krijgen, de methode _GenerateJSONWebToken_ is verantwoordelijk voor het maken van een token dat overeenkomt met de parameters die zijn geconfigureerd voor ontwikkeling:

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Afhandeling van voorzieningen en devoorziening van gebruikers

***Voorbeeld 1. De service opvragen voor een overeenkomende gebruiker***

Azure Active Directory stelt de service op voor een gebruiker met een externe Id-kenmerkwaarde die overeenkomt met de kenmerkwaarde mailNickname van een gebruiker in Azure AD. De query wordt uitgedrukt als een HTTP-aanvraag (Hypertext Transfer Protocol), zoals dit voorbeeld, waarbij Jyoung een voorbeeld is van een e-mailNickname van een gebruiker in Azure Active Directory.

>[!NOTE]
> Dit is slechts een voorbeeld. Niet alle gebruikers hebben een kenmerk mailNickname en de waarde die een gebruiker heeft, is mogelijk niet uniek in de map. Het kenmerk dat wordt gebruikt voor matching (in dit geval externId) is ook configureerbaar in de [Azure AD-kenmerktoewijzingen.](customize-application-attributes.md)

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

In de voorbeeldcode wordt de aanvraag vertaald naar een aanroep naar de QueryAsync-methode van de provider van de service. Hier is de handtekening van die methode: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

In de voorbeeldquery zijn voor een gebruiker met een bepaalde waarde voor het kenmerk externalId de waarden van de argumenten die worden doorgegeven aan de queryasync-methode:

* Parameters. AlternateFilters.Telling: 1
* Parameters. Alternatefilters.elementat(0). AttributePath: "externalId"
* Parameters. Alternatefilters.elementat(0). ComparisonOperator: ComparisonOperator.Equals
* Parameters. Alternatefilter.elementAt(0). VergelijkingWaarde: "jyoung"

***Voorbeeld 2. Voorziening van een gebruiker***

Als het antwoord op een query op de webservice voor een gebruiker met een externe id-kenmerkwaarde die overeenkomt met de kenmerkwaarde mailNickname van een gebruiker geen gebruikers retourneert, wordt in Azure Active Directory gevraagd dat de servicevoorziening een gebruiker aanbiedt die overeenkomt met de waarde van de gebruiker in Azure Active Directory.  Hier is een voorbeeld van een dergelijk verzoek: 

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

In de voorbeeldcode wordt de aanvraag vertaald naar een aanroep naar de CreateAsync-methode van de provider van de service. Hier is de handtekening van die methode: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

In een verzoek om een gebruiker in te richten, is de waarde van het resourceargument een instantie van de klasse Microsoft.SCIM.Core2EnterpriseUser, gedefinieerd in de Microsoft.SCIM.Schema's-bibliotheek.  Als het verzoek tot voorziening van de gebruiker slaagt, wordt verwacht dat de implementatie van de methode een instantie van de klasse Microsoft.SCIM.Core2EnterpriseUser retourneert, waarbij de waarde van de eigenschap Identifier is ingesteld op de unieke id van de nieuw ingerichte Gebruiker.  

***Voorbeeld 3. De huidige status van een gebruiker opvragen*** 

Als u een gebruiker wilt bijwerken waarvan bekend is dat deze bestaat in een identiteitsarchief dat is gefronted door een SCIM, gaat Azure Active Directory verder door de huidige status van die gebruiker van de service op te vragen met een verzoek zoals: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

In de voorbeeldcode wordt de aanvraag vertaald naar een aanroep naar de RetrieveAsync-methode van de provider van de service. Hier is de handtekening van die methode: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

In het voorbeeld van een verzoek om de huidige status van een gebruiker op te halen, zijn de waarden van de eigenschappen van het object die als de waarde van het argument parameters worden opgegeven, als volgt: 
  
* Identificatie: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schema's:extension:enterprise:2.0:User"

***Voorbeeld 4. De waarde van een referentiekenmerk opvragen dat moet worden bijgewerkt*** 

Als een referentiekenmerk moet worden bijgewerkt, wordt in Azure Active Directory de service opgevraagd om te bepalen of de huidige waarde van het referentiekenmerk in het identiteitsarchief dat door de service is voorgenomen, al overeenkomt met de waarde van dat kenmerk in Azure Active Directory. Voor gebruikers is het enige kenmerk waarvan de huidige waarde op deze manier wordt opgevraagd het kenmerk Manager. Hier volgt een voorbeeld van een aanvraag om te bepalen of het kenmerk manager van een gebruikersobject momenteel een bepaalde waarde heeft: in de voorbeeldcode wordt de aanvraag vertaald naar een aanroep naar de QueryAsync-methode van de provider van de service. De waarde van de eigenschappen van het object als de waarde van het argument parameters is als volgt: 
  
* Parameters. AlternateFilters.Telling: 2
* Parameters. AlternateFilters.ElementAt(x). AttributePath: "ID"
* Parameters. AlternateFilters.ElementAt(x). ComparisonOperator: ComparisonOperator.Equals
* Parameters. AlternateFilter.ElementAt(x). Vergelijkingswaarde: "54D382A4-2050-4C03-94D1-E769F1D15682"
* Parameters. AlternateFilters.ElementAt(y). AttributePath: "manager"
* Parameters. AlternateFilters.ElementAt(y). ComparisonOperator: ComparisonOperator.Equals
* Parameters. AlternateFilter.ElementAt(y). VergelijkingSwaarde: "2819c223-7f76-453a-919d-413861904646"
* Parameters. Opgevraagde kenmerken.ElementAt(0): "ID"
* Parameters. SchemaIdentifier: "urn:ietf:params:scim:schema's:extension:enterprise:2.0:User"

Hier kan de waarde van de index x 0 zijn en kan de waarde van de index y 1 zijn, of de waarde van x kan 1 zijn en de waarde van y kan 0 zijn, afhankelijk van de volgorde van de expressies van de parameter filterquery.   

***Voorbeeld 5. Verzoek van Azure AD aan een SCIM-service om een gebruiker bij te werken*** 

Hier vindt u een voorbeeld van een verzoek van Azure Active Directory aan een SCIM-service om een gebruiker bij te werken: 

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

In de voorbeeldcode wordt de aanvraag vertaald naar een aanroep naar de UpdateAsync-methode van de provider van de service. Hier is de handtekening van die methode: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

In het voorbeeld van een verzoek om een gebruiker bij te werken, heeft het object dat wordt opgegeven als de waarde van het veld patch de volgende eigenschapswaarden: 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schema's:extension:enterprise:2.0:User"
* (PatchRequest als PatchRequest2). Operations.Count: 1
* (PatchRequest als PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
* (PatchRequest als PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "manager"
* (PatchRequest als PatchRequest2). Operations.ElementAt(0). Waarde.aantal: 1
* (PatchRequest als PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referentie: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest als PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Waarde: 2819c223-7f76-453a-919d-413861904646

***Voorbeeld 6. Een gebruiker deprovisioneneren***

Als u een gebruiker wilt devoorziening van een identiteitsarchief dat is voorogen door een SCIM-service, verzendt Azure AD een verzoek zoals:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

In de voorbeeldcode wordt de aanvraag vertaald naar een aanroep naar de DeleteAsync-methode van de provider van de service. Hier is de handtekening van die methode: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Het object dat wordt opgegeven als de waarde van het argument resourceIdentifier heeft deze eigenschapswaarden in het voorbeeld van een verzoek om een gebruiker te deprovisioneren: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schema's:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Stap 4: Uw SCIM-eindpunt integreren met de Azure AD SCIM-client

Azure AD kan worden geconfigureerd om toegewezen gebruikers en groepen automatisch in te richten op toepassingen die een specifiek profiel van het [SCIM 2.0-protocol](https://tools.ietf.org/html/rfc7644)implementeren. De specifieke kenmerken van het profiel zijn gedocumenteerd in [stap 2: De Azure AD SCIM-implementatie begrijpen.](#step-2-understand-the-azure-ad-scim-implementation)

Neem contact op met uw toepassingsprovider of de documentatie van uw toepassingsprovider voor verklaringen van compatibiliteit met deze vereisten.

> [!IMPORTANT]
> De Azure AD SCIM-implementatie is gebouwd bovenop de Azure AD-service voor het inrichten van gebruikers, die is ontworpen om gebruikers voortdurend synchroon te houden tussen Azure AD en de doeltoepassing, en een zeer specifieke set standaardbewerkingen implementeert. Het is belangrijk om dit gedrag te begrijpen om het gedrag van de Azure AD SCIM-client te begrijpen. Zie voor meer informatie de sectie [Inrichtingscycli: Initieel en incrementeel](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in [hoe de inrichting werkt.](how-provisioning-works.md)

### <a name="getting-started"></a>Aan de slag

Toepassingen die het in dit artikel beschreven SCIM-profiel ondersteunen, kunnen worden verbonden met Azure Active Directory met behulp van de functie 'niet-galerietoepassing' in de Azure AD-toepassingsgalerie. Eenmaal verbonden, voert Azure AD elke 40 minuten een synchronisatieproces uit waarin het SCIM-eindpunt van de toepassing wordt opgevraagd voor toegewezen gebruikers en groepen en deze wordt gemaakt of gewijzigd op basis van de toewijzingsgegevens.

**Ga als een toepassing met ondersteuning voor SCIM:**

1. Meld u aan bij de [Azure Active Directory-portal](https://aad.portal.azure.com). Houd er rekening mee dat u toegang krijgen tot een gratis proefversie voor Azure Active Directory met P2-licenties door u aan te melden voor het [ontwikkelaarsprogramma](https://developer.microsoft.com/office/dev-program)
2. Selecteer **Enterprise-toepassingen** in het linkerdeelvenster. Er wordt een lijst met alle geconfigureerde apps weergegeven, inclusief apps die vanuit de galerie zijn toegevoegd.
3. Selecteer **+ Nieuwe toepassing** > **Alle** > **niet-galerijtoepassingen**.
4. Voer een naam voor uw toepassing in en selecteer **Toevoegen** om een app-object te maken. De nieuwe app wordt toegevoegd aan de lijst met bedrijfstoepassingen en wordt geopend op het scherm voor app-beheer.

   ![Schermafbeelding van de azure AD-toepassingsgalerie](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD-toepassingsgalerie*

5. Selecteer In het scherm appbeheer de optie **Inrichten** in het linkerdeelvenster.
6. Selecteer **Automatisch**in het menu **Inrichtingsmodus** .

   ![Voorbeeld: de inrichtingspagina van een app in de Azure-portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Inrichten configureren in de Azure-portal*

7. Voer in het veld Url van **tenant** de URL van het SCIM-eindpunt van de toepassing in. Voorbeeld: `https://api.contoso.com/scim/`
8. Als het SCIM-eindpunt een OAuth-token aan toonder vereist van een andere uitgever dan Azure AD, kopieert u het vereiste OAuth-token aan toonder naar het optionele veld **Secret Token.** Als dit veld leeg blijft, bevat Azure AD bij elke aanvraag een OAuth-token aan toonder dat is uitgegeven vanuit Azure AD. Apps die Azure AD als identiteitsprovider gebruiken, kunnen dit door Azure AD uitgegeven token valideren. 
   > [!NOTE]
   > Het wordt ***afgeraden*** om dit veld leeg te laten en te vertrouwen op een token dat is gegenereerd door Azure AD. Deze optie is voornamelijk beschikbaar voor testdoeleinden.
9. Selecteer **Testverbinding** om Azure Active Directory te laten proberen verbinding te maken met het SCIM-eindpunt. Als de poging mislukt, worden foutgegevens weergegeven.  

    > [!NOTE]
    > **Testverbinding** query's het SCIM-eindpunt voor een gebruiker die niet bestaat, met behulp van een willekeurige GUID als de overeenkomende eigenschap geselecteerd in de Azure AD-configuratie. De verwachte juiste reactie is HTTP 200 OK met een leeg SCIM ListResponse-bericht.

10. Als de pogingen om verbinding te maken met de toepassing slagen, selecteert u **Opslaan** om de beheerdersreferenties op te slaan.
11. In de sectie **Toewijzingen** zijn er twee selecteerbare sets [van kenmerktoewijzingen:](customize-application-attributes.md)een voor gebruikersobjecten en een voor groepsobjecten. Selecteer elk om de kenmerken te controleren die zijn gesynchroniseerd vanuit Azure Active Directory naar uw app. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikers en groepen in uw app te matchen voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen door te voeren.

    > [!NOTE]
    > U het synchroniseren van groepsobjecten optioneel uitschakelen door de toewijzing van 'groepen' uit te schakelen.

12. Onder **Instellingen**bepaalt het veld **Bereik** welke gebruikers en groepen worden gesynchroniseerd. Selecteer **Alleen toegewezen gebruikers en groepen** synchroniseren (aanbevolen) om alleen gebruikers en groepen te synchroniseren die zijn toegewezen op het tabblad Gebruikers en **groepen.**
13. Zodra uw configuratie is voltooid, stelt u de **instelstatus** in **op Aan**.
14. Selecteer **Opslaan** om de Azure AD-inrichtingsservice te starten.
15. Als u alleen toegewezen gebruikers en groepen synchroniseert (aanbevolen), moet u het tabblad **Gebruikers en groepen** selecteren en de gebruikers of groepen toewijzen die u wilt synchroniseren.

Zodra de eerste cyclus is gestart, u **Logboeken inrichten** in het linkerdeelvenster selecteren om de voortgang te controleren, die alle acties van de inrichtingsservice in uw app weergeeft. Zie [Rapportage over automatische gebruikersaccountinrichting voor](check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

> [!NOTE]
> De eerste cyclus duurt langer om uit te voeren dan later synchroniseert, die ongeveer elke 40 minuten optreden, zolang de service wordt uitgevoerd.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Stap 5: Uw toepassing publiceren in de Azure AD-toepassingsgalerie

Als u een toepassing bouwt die door meer dan één tenant wordt gebruikt, u deze beschikbaar stellen in de azure AD-toepassingsgalerie. Dit maakt het voor organisaties gemakkelijk om de toepassing te ontdekken en provisioning te configureren. Het publiceren van uw app in de Azure AD-galerie en het beschikbaar stellen aan anderen is eenvoudig. Bekijk [hier](../develop/howto-app-gallery-listing.md)de stappen. Microsoft werkt met u samen om uw toepassing te integreren in onze galerij, uw eindpunt te testen en [onboarding-documentatie](../saas-apps/tutorial-list.md) vrij te geven die klanten kunnen gebruiken. 

### <a name="gallery-onboarding-checklist"></a>Checklist voor galerieonboarding
Volg de onderstaande checklist om ervoor te zorgen dat uw toepassing snel aan boord is en dat klanten een soepele implementatie-ervaring hebben. De informatie wordt van u verzameld bij het instappen naar de galerie. 
> [!div class="checklist"]
> * Ondersteuning voor een [SCIM 2.0-gebruiker](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) en groepseindpunt (er is er slechts één vereist, maar beide worden aanbevolen)
> * Ondersteuning van ten minste 25 aanvragen per seconde per tenant (vereist)
> * Technische en ondersteunende contacten leggen om klanten te begeleiden na galerijonboarding (Vereist)
> * 3 Niet-verlopende testreferenties voor uw toepassing (vereist)
> * Ondersteuning van de OAuth autorisatie code grant of een langlevend token zoals hieronder beschreven (Vereist)
> * Een engineering- en ondersteuningscontactpunt opzetten om klanten te ondersteunen na galerijonboarding (Vereist)
> * Ondersteuning voor het bijwerken van meerdere groepslidmaatschappen met één PATCH (Aanbevolen) 
> * Uw SCIM-eindpunt openbaar documenteren (aanbevolen) 
> * [Detectie van ondersteuningsschema's](https://tools.ietf.org/html/rfc7643#section-6) (aanbevolen)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorisatie voor het inrichten van connectoren in de toepassingsgalerie
De SCIM-spec definieert geen SCIM-specifiek schema voor authenticatie en autorisatie. Zij is afhankelijk van het gebruik van bestaande industrienormen. De Azure AD-inrichtingsclient ondersteunt twee autorisatiemethoden voor toepassingen in de galerie. 

|Autorisatiemethode|Voordelen|Nadelen|Ondersteuning|
|--|--|--|--|
|Gebruikersnaam en wachtwoord (niet aanbevolen of ondersteund door Azure AD)|Eenvoudig te implementeren|Onzeker - [Uw Pa$$word maakt niet uit](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Ondersteund op een case-by-case basis voor galerij apps. Wordt niet ondersteund voor niet-galerij-apps.|
|Langlevende drager token|Langlevende tokens vereisen geen gebruiker om aanwezig te zijn. Ze zijn gemakkelijk te gebruiken voor beheerders bij het opzetten van provisioning.|Langlevende tokens kunnen moeilijk te delen zijn met een beheerder zonder onveilige methoden zoals e-mail te gebruiken. |Ondersteund voor galerij- en niet-galerij-apps. |
|OAuth-autorisatiecodeverlening|Toegangstokens zijn veel korter dan wachtwoorden en hebben een geautomatiseerd vernieuwingsmechanisme dat langlevende tokens aan toonder niet hebben.  Een echte gebruiker moet aanwezig zijn tijdens de eerste autorisatie, het toevoegen van een niveau van verantwoording. |Vereist dat een gebruiker aanwezig is. Als de gebruiker de organisatie verlaat, is het token ongeldig en moet de autorisatie opnieuw worden voltooid.|Ondersteund voor galerij-apps. Ondersteuning voor niet-galerij apps is aan de gang.|
|Toekenning van OAuth-clientreferenties|Toegangstokens zijn veel korter dan wachtwoorden en hebben een geautomatiseerd vernieuwingsmechanisme dat langlevende tokens aan toonder niet hebben. Zowel de autorisatiecodegrant als de clientreferenties verlenen hetzelfde type toegangstoken, zodat het verplaatsen tussen deze methoden transparant is voor de API.  Inrichten kan volledig geautomatiseerd worden en nieuwe tokens kunnen in stilte worden aangevraagd zonder interactie van de gebruiker. ||Wordt niet ondersteund voor galerij- en niet-galerij-apps. Ondersteuning zit in onze achterstand.|

[!NOTE] Het wordt afgeraden om het tokenveld leeg te laten in de aangepaste configuratie-gebruikersinterface van Azure AD. Het gegenereerde token is voornamelijk beschikbaar voor testdoeleinden.

**Subsidiestroom van OAuth-autorisatiecode:** De inrichtingsdienst ondersteunt de verlening van de [autorisatiecode](https://tools.ietf.org/html/rfc6749#page-24). Nadat u uw aanvraag voor publicatie van uw app in de galerie hebt ingediend, zal ons team met u samenwerken om de volgende informatie te verzamelen:
*  Autorisatie-URL: een URL van de client om autorisatie te verkrijgen van de eigenaar van de bron via omleiding door de gebruiker.Authorization URL: A URL by the client to obtain authorization from the resource owner via user-agent redirection. De gebruiker wordt doorgestuurd naar deze URL om toegang te autoriseren. 
*  URL voor tokenuitwisseling: een URL van de client om een autorisatieverlening voor een toegangstoken uit te wisselen, meestal met clientverificatie.
*  Client-id: de autorisatieserver geeft de geregistreerde client een client-id op, wat een unieke tekenreeks is die de registratiegegevens van de client vertegenwoordigt.  De client-id is geen geheim; het wordt blootgesteld aan de eigenaar van de bron en **mag niet** alleen worden gebruikt voor clientverificatie.  
*  Clientgeheim: Het clientgeheim is een geheim dat door de autorisatieserver wordt gegenereerd. Het moet een unieke waarde die alleen bekend is bij de autorisatieserver. 

Merk op dat OAuth v1 niet wordt ondersteund als gevolg van blootstelling van het clientgeheim. OAuth v2 wordt ondersteund.  

Aanbevolen , maar niet vereist:
* Ondersteuning voor meerdere omleidings-URL's. Beheerders kunnen de inrichting configureren vanuit zowel portal.azure.com als 'aad.portal.azure.com'. Als u meerdere omleidings-URL's ondersteunt, zorgt u ervoor dat gebruikers toegang vanaf beide portalen kunnen autoriseren.
* Ondersteun meerdere geheimen om een soepele geheime vernieuwing te garanderen, zonder downtime. 

**Langlevende OAuth-dragerspenningen:** Als uw toepassing de subsidiestroom van de OAuth-autorisatiecode niet ondersteunt, u ook een langlevend OAuth-token aan toonder genereren dan een beheerder kan gebruiken om de inrichtingsintegratie in te stellen. Het token moet eeuwigdurend zijn, anders wordt de inrichtingstaak [in quarantaine geplaatst](application-provisioning-quarantine-status.md) wanneer het token verloopt. Dit token moet minder dan 1 KB groot zijn.  

Voor aanvullende verificatie- en autorisatiemethoden laat u het ons weten op [UserVoice.](https://aka.ms/appprovisioningfeaturerequest)

### <a name="gallery-go-to-market-launch-check-list"></a>Checklist voor de lancering van galerijgo-to-market
Om de bewustwording en vraag naar onze gezamenlijke integratie te stimuleren, raden we u aan uw bestaande documentatie bij te werken en de integratie in uw marketingkanalen te versterken.  Hieronder vindt u een reeks checklist-activiteiten die u aanbevelen om de lancering te ondersteunen

* **Verkoop- en klantenservicegereedheid.** Zorg ervoor dat uw verkoop- en ondersteuningsteams op de hoogte zijn en kunnen spreken met de integratiemogelijkheden. Informeer uw verkoop- en ondersteuningsteam, voorzie hen van veelgestelde vragen en neem de integratie op in uw verkoopmateriaal. 
* **Blogpost en/of persbericht.** Maak een blogpost of persbericht dat de gezamenlijke integratie, de voordelen en hoe aan de slag te beschrijven. [Voorbeeld: Persbericht van Imprivata en Azure Active Directory](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Sociale media.** Maak gebruik van uw sociale media zoals Twitter, Facebook of LinkedIn om de integratie aan uw klanten te bevorderen. Zorg ervoor @AzureAD dat je je bericht retweeten. [Voorbeeld: Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
* **Marketing website.** Maak of werk uw marketingpagina's (bijvoorbeeld integratiepagina, partnerpagina, prijspagina, enz.) aan om de beschikbaarheid van de gezamenlijke integratie op te nemen. [Voorbeeld: Pingboard-integratiepagina](https://pingboard.com/org-chart-for), [Smartsheet-integratiepagina,](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad) [Monday.com prijspagina](https://monday.com/pricing/) 
* **Technische documentatie.** Maak een helpcenterartikel of technische documentatie over hoe klanten aan de slag kunnen. [Voorbeeld: Envoy + Microsoft Azure Active Directory integration.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Klantcommunicatie.** Waarschuw klanten voor de nieuwe integratie via uw klantcommunicatie (maandelijkse nieuwsbrieven, e-mailcampagnes, productreleasenotes). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>IP-adressen toestaan die worden gebruikt door de Azure AD-inrichtingsservice om SCIM-aanvragen uit te voeren

Bepaalde apps staan binnenkomend verkeer toe naar hun app. Om ervoor te laten weten dat de Azure AD-inrichtingsservice naar verwachting kan functioneren, moeten de gebruikte IP-adressen zijn toegestaan. Zie het JSON-bestand - [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519)voor een lijst met IP-adressen voor elke servicetag/regio. U deze IP's downloaden en programmeren in uw firewall als dat nodig is. De gereserveerde IP-bereiken voor Azure AD-inrichting zijn te vinden onder 'AzureActiveDirectoryDomainServices'.

## <a name="related-articles"></a>Verwante artikelen:

* [Gebruikersinrichting en deprovisioning automatiseren naar SaaS-apps](user-provisioning.md)
* [Kenmerkentoewijzingen aanpassen voor gebruikersinrichting](customize-application-attributes.md)
* [Expressies schrijven voor kenmerktoewijzingen](functions-for-customizing-application-data.md)
* [Scopingfilters voor gebruikersinrichting](define-conditional-rules-for-provisioning-user-accounts.md)
* [Kennisgevingen voor het inrichten van een account](user-provisioning.md)
* [Lijst met zelfstudies over het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
