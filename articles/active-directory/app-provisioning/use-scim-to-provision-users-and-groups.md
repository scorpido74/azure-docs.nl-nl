---
title: Een SCIM-eind punt ontwikkelen voor het inrichten van gebruikers voor apps vanuit Azure AD
description: Met het systeem voor Identity Management (SCIM) met meerdere domeinen wordt automatische gebruikers inrichting gestandaardiseerd. Leer hoe u een SCIM-eind punt ontwikkelt, uw SCIM-API integreert met Azure Active Directory en beginnen met het automatiseren van het inrichten van gebruikers en groepen in uw Cloud toepassingen.
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
ms.date: 02/18/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a44cf9aa5b3287a01617be6439cd04b9a5caa73
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484227"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Een SCIM-eind punt bouwen en gebruikers inrichten configureren met Azure Active Directory (Azure AD)

Als ontwikkelaar van een toepassing kunt u het systeem gebruiken voor SCIM-gebruikers beheer-API (Cross-Domain Identity Management) om het automatisch inrichten van gebruikers en groepen tussen uw toepassing en Azure AD mogelijk te maken. In dit artikel wordt beschreven hoe u een SCIM-eind punt bouwt en integreert met de Azure AD-inrichtings service. De SCIM-specificatie biedt een gemeen schappelijk gebruikers schema voor het inrichten van. Bij gebruik in combi natie met Federatie standaarden zoals SAML of OpenID Connect Connect biedt beheerders een end-to-end, op standaarden gebaseerde oplossing voor toegangs beheer.

SCIM is een gestandaardiseerde definitie van twee eind punten: een/users-eind punt en een/groups-eind punt. Er worden algemene REST-werk woorden gebruikt om objecten te maken, bij te werken en te verwijderen, en een vooraf gedefinieerd schema voor algemene kenmerken zoals groeps naam, gebruikers naam, voor naam, achternaam en e-mail adres. Apps die een SCIM 2,0-REST API bieden, kunnen de pijn van het werken met een eigen API voor gebruikers beheer verminderen of elimineren. Elke compatibele SCIM-client weet bijvoorbeeld hoe u een HTTP POST van een JSON-object naar het/users-eind punt kunt maken om een nieuwe gebruikers vermelding te maken. Apps die voldoen aan de SCIM-standaard kunnen direct profiteren van bestaande clients, hulpprogram ma's en code, in plaats van een iets andere API voor dezelfde basis acties te hoeven gebruiken. 

![Inrichten vanuit Azure AD naar een app met SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Met het standaard gebruikers object schema en de rest Api's voor beheer dat is gedefinieerd in SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) kunnen id-providers en apps gemakkelijker met elkaar worden geïntegreerd. Toepassings ontwikkelaars die een SCIM-eind punt bouwen, kunnen worden geïntegreerd met elke SCIM-compatibele client zonder aangepaste werk.

Voor het automatiseren van de inrichting van een toepassing moet u een SCIM-eind punt bouwen en integreren met de Azure AD SCIM-compatibel. Voer de volgende stappen uit om het inrichten van gebruikers en groepen in uw toepassing te starten. 
    
  * **[Stap 1: uw gebruikers-en groeps schema ontwerpen.](#step-1-design-your-user-and-group-schema)** Identificeer de objecten en kenmerken die uw toepassing nodig heeft en bepaal hoe deze worden toegewezen aan het gebruikers-en groeps schema dat wordt ondersteund door de Azure AD SCIM-implementatie.

  * **[Stap 2: inzicht in de implementatie van Azure AD SCIM.](#step-2-understand-the-azure-ad-scim-implementation)** Meer informatie over de implementatie van de Azure AD SCIM-client en het model leren van de verwerking van uw SCIM-protocol aanvragen en antwoorden.

  * **[Stap 3: een SCIM-eind punt bouwen.](#step-3-build-a-scim-endpoint)** Een eind punt moet SCIM 2,0-compatibel zijn om te kunnen worden geïntegreerd met de Azure AD-inrichtings service. Als optie kunt u gebruikmaken van micro soft CLI-bibliotheken en-code voorbeelden om uw eind punt te bouwen. Deze voor beelden zijn alleen ter referentie en alleen testen. u wordt aangeraden uw productie-app te coderen zodat deze afhankelijk is.

  * **[Stap 4: Integreer uw SCIM-eind punt met de Azure AD SCIM-client.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Als uw organisatie gebruikmaakt van een toepassing van derden die het profiel van SCIM 2,0 dat door Azure AD wordt ondersteund, implementeert, kunt u het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers en groepen direct starten.

  * **[Stap 5: Publiceer uw toepassing in de Azure AD-toepassings galerie.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Maak het eenvoudig voor klanten om uw toepassing te detecteren en de inrichting eenvoudig te configureren. 

![Stappen voor het integreren van een SCIM-eind punt met Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Stap 1: uw gebruikers-en groeps schema ontwerpen

Voor elke toepassing zijn verschillende kenmerken vereist om een gebruiker of groep te maken. Start uw integratie door de objecten (gebruikers, groepen) en kenmerken (naam, Manager, functie, enz.) te identificeren die nodig zijn voor uw toepassing. De SCIM-standaard definieert een schema voor het beheren van gebruikers en groepen. Voor het kern gebruikers schema zijn alleen drie kenmerken vereist: **id** (door de service provider gedefinieerde id), **externalId** (door client gedefinieerde id) en **META** (alleen-lezen meta gegevens die worden onderhouden door de service provider). Alle andere kenmerken zijn optioneel. Naast het kern gebruikers schema definieert de SCIM-standaard een gebruikers extensie voor ondernemingen en een model om het gebruikers schema uit te breiden om te voldoen aan de behoeften van uw toepassing. Als uw toepassing bijvoorbeeld een manager van een gebruiker vereist, kunt u het ondernemings gebruikers schema gebruiken om de Manager van de gebruiker en het kern schema te verzamelen voor het verzamelen van het e-mail adres van de gebruiker. Volg de onderstaande stappen om uw schema te ontwerpen:
  1. Geef een lijst van de kenmerken die uw toepassing nodig heeft. Het kan handig zijn om uw vereisten te onderverdelen in de kenmerken die nodig zijn voor authenticatie (bijvoorbeeld aanmeldings naam en e-mail adres), kenmerken die nodig zijn voor het beheren van de levens cyclus van de gebruiker (bijvoorbeeld status/actief) en andere kenmerken die nodig zijn om uw specifieke toepassing te laten werken (bijvoorbeeld Manager, tag).
  2. Controleer of deze kenmerken al zijn gedefinieerd in het basis gebruikers schema of het gebruikers schema van de onderneming. Als kenmerken die u nodig hebt en niet worden behandeld in de basis-of ENTER prise-gebruikers schema's, moet u een uitbrei ding definiëren voor het gebruikers schema dat betrekking heeft op de kenmerken die u nodig hebt. In het onderstaande voor beeld hebben we een extensie toegevoegd aan de gebruiker om het inrichten van een ' tag ' op een gebruiker toe te staan. U kunt het beste beginnen met alleen de basis-en ondernemings gebruikers schema's en later uitbreiden naar aanvullende aangepaste schema's.  
  3. Wijs de SCIM-kenmerken toe aan de gebruikers kenmerken in azure AD. Als een van de kenmerken die u hebt gedefinieerd in uw SCIM-eind punt geen duidelijke vergelijk bare soort heeft in het Azure AD-gebruikers schema, is de kans groot dat de gegevens niet in het gebruikers object op de meeste tenants worden opgeslagen. Bepaal of dit kenmerk optioneel kan zijn voor het maken van een gebruiker. Als het kenmerk essentieel is voor de werking van uw toepassing, kunt u de Tenant beheerder helpen hun schema uit te breiden of een uitbreidings kenmerk te gebruiken zoals hieronder wordt weer gegeven voor de eigenschap "Tags".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabel 1: een overzicht van de kenmerken die u nodig hebt 
| Stap 1: kenmerken bepalen die uw app nodig heeft| Stap 2: app-vereisten toewijzen aan SCIM Standard| Stap 3: SCIM-kenmerken toewijzen aan de Azure AD-kenmerken|
|--|--|--|
|Aanmeldings naam|userName|userPrincipalName|
|voornaam|name.givenName|givenName|
|achternaam|naam. lastName|achternaam|
|workMail|E-mail berichten [type EQ "werk]. waarde|Mail|
|Manager|Manager|Manager|
|Tag|urn: IETF: params: scim: schemas: extension: 2.0: CustomExtension: tag|extensionAttribute1|
|status|actief|isSoftDeleted (berekende waarde niet opgeslagen op gebruiker)|

Het hierboven gedefinieerde schema wordt weer gegeven met behulp van de JSON-nettolading hieronder. Houd er rekening mee dat naast de vereiste kenmerken voor de toepassing, de JSON-weer gave de vereiste kenmerken ' id ', ' externalId ' en ' meta ' bevat.

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

### <a name="table-2-default-user-attribute-mapping"></a>Tabel 2: standaard toewijzing van gebruikers kenmerken
U kunt de onderstaande tabel gebruiken om te begrijpen hoe de kenmerken die uw toepassing nodig heeft, kunnen worden toegewezen aan een kenmerk in azure AD en de SCIM RFC. U kunt [aanpassen](customize-application-attributes.md) hoe kenmerken worden toegewezen tussen Azure AD en uw scim-eind punt. Houd er rekening mee dat u niet zowel gebruikers als groepen of alle hieronder weer gegeven kenmerken moet ondersteunen. Ze zijn een verwijzing naar de manier waarop kenmerken in azure AD vaak worden toegewezen aan eigenschappen in het SCIM-protocol. 

| Azure Active Directory-gebruiker | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |actief |
|department|urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: gebruiker: Department|
| displayName |displayName |
|employeeId|urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers [type eq "fax"] .value |
| givenName |name.givenName |
| Functie |titel |
| mail |e-mailberichten [type eq 'werk'] .value |
| mailNickname |externalId |
| Manager |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: Manager |
| mobiele |phoneNumbers [type eq 'mobiel'] .value |
| Postcode |adressen type eq 'werk'.postalCode |
| proxy-adressen |e-mailberichten [Typ eq 'ander']. Waarde |
| physical-Delivery-OfficeName |adressen [Typ eq 'ander']. Indeling |
| streetAddress |adressen type eq 'werk'.streetAddress |
| surname |name.familyName |
| Telefoonnummer |phoneNumbers [type eq 'werk'] .value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabel 3: standaard toewijzing van groeps kenmerken

| Azure Active Directory-groep | urn: IETF: params: scim: schemas: kern: 2.0: groep |
| --- | --- |
| displayName |displayName |
| mail |e-mailberichten [type eq 'werk'] .value |
| mailNickname |displayName |
| leden |leden |
| object-id |externalId |
| proxyAddresses |e-mailberichten [Typ eq 'ander']. Waarde |

Er zijn verschillende eind punten gedefinieerd in de SCIM RFC. U kunt aan de slag met het/User-eind punt en vervolgens van daaruit uitbreiden. Het/schemas-eind punt is handig wanneer u aangepaste kenmerken gebruikt of als uw schema regel matig wordt gewijzigd. Hiermee kan een client automatisch het meest recente schema ophalen. Het/bulk-eind punt is vooral handig bij het ondersteunen van groepen. De volgende tabel beschrijft de verschillende eind punten die zijn gedefinieerd in de SCIM-standaard. Het/schemas-eind punt is handig wanneer u aangepaste kenmerken gebruikt of als uw schema regel matig wordt gewijzigd. Hiermee kan een client automatisch het meest recente schema ophalen. Het/bulk-eind punt is vooral handig bij het ondersteunen van groepen. De volgende tabel beschrijft de verschillende eind punten die zijn gedefinieerd in de SCIM-standaard. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabel 4: Bepaal de eind punten die u wilt ontwikkelen
|ENDPOINTS|BESCHRIJVING|
|--|--|
|/User|RUWE bewerkingen op een gebruikers object uit te voeren.|
|/Group|RUWE bewerkingen uitvoeren op een groeps object.|
|/ServiceProviderConfig|Bevat informatie over de functies van de SCIM-standaard die worden ondersteund, bijvoorbeeld de bronnen die worden ondersteund en de verificatie methode.|
|/ResourceTypes|Hiermee geeft u de meta gegevens van elke resource op|
|/Schemas|De set met kenmerken die door elke client en service provider wordt ondersteund, kan variëren. Hoewel een service provider ' naam, ' titel ' en ' e-mailen ' kan bevatten, terwijl een andere service provider ' naam ', ' titel ' en ' phoneNumbers ' gebruikt. Het eind punt voor schema's maakt het mogelijk om de kenmerken te detecteren die worden ondersteund.|
|/Bulk|Met bulk bewerkingen kunt u bewerkingen uitvoeren op een grote verzameling Resource objecten in één bewerking (zoals lidmaatschappen van updates voor een grote groep).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Stap 2: inzicht in de implementatie van Azure AD SCIM
> [!IMPORTANT]
> Het gedrag van de Azure AD SCIM-implementatie is voor het laatst bijgewerkt op 18 december 2018. Zie [SCIM 2,0-protocol compatibiliteit van de Azure AD User Provisioning Service](application-provisioning-config-problem-scim-compatibility.md)voor meer informatie over wat er is gewijzigd.

Als u een toepassing bouwt die een SCIM 2,0-gebruikers beheer-API ondersteunt, wordt in deze sectie in detail beschreven hoe de Azure AD SCIM-client is geïmplementeerd. Ook wordt uitgelegd hoe u de verwerking van SCIM-protocol aanvragen en-antwoorden kunt model leren. Nadat u uw SCIM-eind punt hebt geïmplementeerd, kunt u dit testen door de procedure te volgen die in de vorige sectie is beschreven.

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
* Het kenmerk waarin de resources kunnen worden opgevraagd, moet worden ingesteld als een overeenkomend kenmerk op de toepassing in de [Azure Portal](https://portal.azure.com). Zie voor meer informatie [aanpassen van kenmerk toewijzingen](customize-application-attributes.md) voor het inrichten van gebruikers

### <a name="user-provisioning-and-deprovisioning"></a>Gebruikers inrichten en de inrichting ongedaan maken

In de volgende afbeelding ziet u de berichten die Azure Active Directory verzendt naar een SCIM-service om de levens cyclus van een gebruiker in de identiteits opslag van uw toepassing te beheren.  

![toont de volg orde van de gebruikers inrichting en het ongedaan maken van de inrichting](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Volg orde voor het inrichten en opheffen van de inrichting van gebruikers*

### <a name="group-provisioning-and-deprovisioning"></a>Groeps inrichting en ongedaan maken van inrichting

Het inrichten en het ongedaan maken van de inrichting van groepen is optioneel. Wanneer de volgende afbeelding is geïmplementeerd en ingeschakeld, ziet u de berichten die Azure AD verzendt naar een SCIM-service om de levens cyclus van een groep in de identiteits opslag van uw toepassing te beheren.  Deze berichten verschillen van de berichten over gebruikers op twee manieren:

* Bij aanvragen voor het ophalen van groepen wordt opgegeven dat het kenmerk members moet worden uitgesloten van alle resources die zijn opgegeven in reactie op de aanvraag.  
* Verzoeken om te bepalen of een verwijzingskenmerk een bepaalde waarde heeft zijn aanvragen over het kenmerk leden.  

![toont de volg orde van de groeps inrichting en het ongedaan maken van de inrichting](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Volg orde van groeps inrichting en ongedaan maken van inrichting*

### <a name="scim-protocol-requests-and-responses"></a>Aanvragen en antwoorden van het SCIM-Protocol
Deze sectie bevat voor beelden van SCIM-aanvragen die worden verzonden door de Azure AD SCIM-client en voor beelden van verwachte reacties. Voor de beste resultaten moet u de app coderen om deze aanvragen in deze indeling af te handelen en de verwachte reacties te verzenden.

> [!IMPORTANT]
> Als u wilt weten hoe en wanneer de Azure AD User Provisioning-Service de hieronder beschreven bewerkingen afrondt, raadpleegt u de sectie [cycli inrichten: eerste en incrementeel](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in de werking van [inrichten](how-provisioning-works.md).

[Gebruikers bewerkingen](#user-operations)
  - [Gebruiker maken](#create-user) ([aanvraag](#request) / [)](#response)
  - [Gebruiker ophalen](#get-user) ([verzoek om](#request-1) [ / )](#response-1)
  - [Gebruiker ophalen op basis van query](#get-user-by-query) ([aanvraag](#request-2) [ / )](#response-2)
  - [Gebruiker ophalen op basis van query-nul resultaten](#get-user-by-query---zero-results) ([aanvraag](#request-3)
/ [antwoord](#response-3))
  - [Gebruiker bijwerken [eigenschappen met meerdere waarden]](#update-user-multi-valued-properties) ([aanvraag](#request-4) /  [antwoord](#response-4))
  - [Update gebruiker [eigenschappen met één waarde]](#update-user-single-valued-properties) ([aanvraag](#request-5)
/ [antwoord](#response-5)) 
  - [Gebruiker uitschakelen](#disable-user) ( [reactie](#response-14)van / 
[aanvragen](#request-14) )
  - [Gebruiker verwijderen](#delete-user) ([aanvraag](#request-6) / 
[antwoord](#response-6))


[Groeps bewerkingen](#group-operations)
  - [Groep maken](#create-group) ( [vraag](#request-7) / [antwoord](#response-7))
  - [Groep ophalen](#get-group) ( [aanvraag](#request-8) / - [antwoord](#response-8))
  - [Groep ophalen op DisplayName](#get-group-by-displayname) ([aanvraag](#request-9) / [antwoord](#response-9))
  - [Update groep [niet-lidtype kenmerken]](#update-group-non-member-attributes) ([aanvraag](#request-10) /
 [antwoord](#response-10))
  - [Update groep [leden toevoegen]](#update-group-add-members) ( [aanvraag](#request-11) /
[antwoord](#response-11))
  - [Update groep [leden verwijderen]](#update-group-remove-members) ( [aanvraag](#request-12) /
[antwoord](#response-12))
  - [Groep verwijderen](#delete-group) ([aanvraag](#request-13) /
[antwoord](#response-13))

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

### <a name="disable-user"></a>Gebruiker uitschakelen

##### <a name="request-14"></a>Schot

*PATCH/users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response-14"></a>Beantwoord

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

### <a name="security-requirements"></a>Beveiligings vereisten
**TLS-protocol versies**

De enige acceptabele TLS-protocol versies zijn TLS 1,2 en TLS 1,3. Er zijn geen andere versies van TLS toegestaan. Er is geen SSL-versie toegestaan. 
- RSA-sleutels moeten ten minste 2.048 bits zijn.
- ECC-sleutels moeten ten minste 256 bits zijn en worden gegenereerd met een goedgekeurde elliptische curve


**Sleutel lengten**

Alle services moeten X. 509-certificaten gebruiken die zijn gegenereerd met cryptografische sleutels van voldoende lengte, wat betekent het volgende:

**Coderings suites**

Alle services moeten worden geconfigureerd voor het gebruik van de volgende coderings suites in de exacte volg orde die hieronder is opgegeven. Houd er rekening mee dat als u alleen een RSA-certificaat hebt, de installatie van de ECDSA-coderings suites geen effect heeft. </br>

Minimale staaf voor TLS 1,2-coderings suites:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Stap 3: een SCIM-eind punt bouwen

Als u een SCIM-webservice maakt die interfaces met Azure Active Directory, kunt u automatische gebruikers inrichting inschakelen voor vrijwel elke toepassing of identiteits opslag.

Dit is hoe het werkt:

1. Azure AD biedt een CLI-bibliotheek (Common Language Infrastructure) met de naam micro soft. SystemForCrossDomainIdentityManagement, die is opgenomen in de onderstaande code voorbeelden. Systeem integrators en ontwikkel aars kunnen deze bibliotheek gebruiken om een SCIM-webservice-eind punt te maken en te implementeren waarmee Azure AD kan worden verbonden met de identiteits opslag van elke toepassing.
2. Toewijzingen worden geïmplementeerd in de webservice om het schema voor de gestandaardiseerde toewijzen aan het gebruikersschema en het protocol vereist voor de toepassing. 
3. De eindpunt-URL is geregistreerd in Azure AD als onderdeel van een aangepaste toepassing in de toepassingengalerie.
4. Gebruikers en groepen worden toegewezen aan deze toepassing in Azure AD. Na toewijzing worden ze in een wachtrij geplaatst om te worden gesynchroniseerd met de doel toepassing. Het proces van synchronisatie verwerken van de wachtrij wordt elke 40 minuten uitgevoerd.

### <a name="code-samples"></a>Codevoorbeelden

Om dit proces gemakkelijker te maken, worden er [code voorbeelden](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) gegeven, waarmee een scim-webservice-eind punt wordt gemaakt en automatische inrichting wordt gedemonstreerd. Het voor beeld is van een provider die een bestand onderhoudt met rijen door komma's gescheiden waarden die gebruikers en groepen vertegenwoordigen.

**Vereisten**

* Visual Studio 2013 of hoger
* [Azure-SDK voor .NET](https://azure.microsoft.com/downloads/)
* Windows machine die ondersteuning biedt voor het framework ASP.NET 4.5 moet worden gebruikt als het eindpunt SCIM. Deze computer moet toegankelijk zijn vanuit de Cloud.
* [Een Azure-abonnement met een proef-of gelicentieerde versie van Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Aan de slag

De eenvoudigste manier voor het implementeren van een eindpunt SCIM inrichting aanvragen van Azure AD kan accepteren, is te bouwen en implementeren van de voorbeeldcode die uitvoer van de ingerichte gebruikers naar een bestand met door komma's gescheiden waarden (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Een voor beeld-SCIM-eind punt maken

1. Down load het voorbeeld pakket voor de code op [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Pak het pakket en plaats deze op uw Windows-machine op een locatie zoals C:\AzureAD-BYOA-Provisioning-Samples\.
1. In deze map, start u het FileProvisioning\Host\FileProvisioningService.csproj-project in Visual Studio.
1. Selecteer **Hulpprogram ma's** > **NuGet package manager** > **Package Manager-console**en voer de volgende opdrachten uit voor het FileProvisioningService-project om de oplossings referenties op te lossen:

   ```powershell
    Update-Package -Reinstall
   ```

1. Bouw het project FileProvisioningService.
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
1. Voer een naam in voor uw toepassing en selecteer **toevoegen** om een app-object te maken. De toepassingsobject gemaakt is bedoeld om weer te geven van de doel-app u zou worden ingericht voor en uitvoering van eenmalige aanmelding voor en niet alleen het eindpunt SCIM.
1. Selecteer in het scherm voor het beheren van apps de optie **inrichten** in het linkerdeel venster.
1. Selecteer in het menu **inrichtings modus** de optie **automatisch**.    
1. Voer in het veld **Tenant-URL** de URL in van het scim-eind punt van de toepassing. Voorbeeld: https://api.contoso.com/scim/

1. Als het SCIM-eind punt een OAuth Bearer-token van een andere uitgever dan Azure AD vereist, kopieert u het vereiste OAuth Bearer-token naar het veld optionele **geheime token** . Als dit veld leeg blijft, bevat Azure AD een OAuth Bearer-token dat is uitgegeven door Azure AD met elke aanvraag. Apps die gebruikmaken van Azure AD als id-provider kunnen dit door Azure AD uitgegeven token valideren.
1. Selecteer **verbinding testen** om te laten proberen Azure Active Directory verbinding te maken met het scim-eind punt. Als de poging mislukt, wordt er informatie over de fout weer gegeven.  

    > [!NOTE]
    > **Test verbindings** QUERY'S het scim-eind punt voor een gebruiker die niet bestaat, met behulp van een wille keurige GUID als de overeenkomende eigenschap die is geselecteerd in de Azure AD-configuratie. Het verwachte juiste antwoord is HTTP 200 OK met een leeg SCIM ListResponse-bericht

1. Als de pogingen om verbinding te maken met de toepassing slagen, selecteert u **Opslaan** om de beheerders referenties op te slaan.
1. In de sectie **toewijzingen** zijn er twee sets met kenmerk toewijzingen: één voor gebruikers objecten en één voor groeps objecten. Selecteren om te controleren van de kenmerken die worden gesynchroniseerd vanuit Active Directory van Azure aan uw app. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers en groepen in uw app voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren.
1. Onder **instellingen**definieert het **bereik** veld welke gebruikers en of groepen worden gesynchroniseerd. Selecteer **alleen toegewezen gebruikers en groepen synchroniseren** (aanbevolen) om alleen gebruikers en groepen te synchroniseren die zijn toegewezen op het tabblad **gebruikers en groepen** .
1. Wanneer de configuratie is voltooid, stelt u de **inrichtings status** **in op aan**.
1. Selecteer **Opslaan** om de Azure AD-inrichtings service te starten.
1. Als u alleen toegewezen gebruikers en groepen wilt synchroniseren (aanbevolen), selecteert u het tabblad **gebruikers en groepen** en wijst u de gebruikers of groepen toe die u wilt synchroniseren.

Zodra de eerste cyclus is gestart, kunt u **audit logboeken** selecteren in het linkerdeel venster om de voortgang te controleren. Hiermee worden alle acties weer gegeven die door de inrichtings service in uw app worden uitgevoerd. Zie [rapportage over het automatisch inrichten van gebruikers accounts](check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

De laatste stap bij het controleren van het voorbeeld is de TargetFile.csv-bestand te openen in de map \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug op uw Windows-computer. Nadat het inrichtingsproces is uitgevoerd, wordt dit bestand bevat de details van alle toegewezen en ingericht gebruikers en groepen.

### <a name="development-libraries"></a>Ontwikkelingsbibliotheken

Voor het ontwikkelen van uw eigen webservice die aan de specificatie SCIM voldoet eerst raken met de volgende bibliotheken geleverd door Microsoft om te het ontwikkelingsproces versnellen:

* Common Language Infrastructure (CLI)-bibliotheken worden aangeboden voor gebruik met de talen die op basis van die infrastructuur, zoals C#. Een van deze bibliotheken, micro soft. SystemForCrossDomainIdentityManagement. service, declareert een interface, micro soft. SystemForCrossDomainIdentityManagement. IProvider, die wordt weer gegeven in de volgende afbeelding. Een ontwikkelaar die de bibliotheken gebruikt, implementeert die interface met een klasse die algemeen kan worden aangeduid als een provider. De-bibliotheken kunnen door de ontwikkelaar een webservice implementeren die voldoet aan de SCIM-specificatie. De webservice kan worden gehost in Internet Information Services of een uitvoer bare CLI-assembly. Aanvraag wordt omgezet in aanroepen van methoden van de provider, die door de ontwikkelaar zou worden geprogrammeerd om te worden uitgevoerd op bepaalde identiteitsarchief.
  
   ![Uitsplitsing: een aanvraag die is vertaald naar de methoden van de provider](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)
  
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

Deze service moet beschikken over een HTTP-adres en de server certificaat voor clientverificatie waarvan de basiscertificeringsinstantie een van de volgende namen is: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Een certificaat voor serververificatie kan worden gebonden aan een poort op een Windows-host met behulp van het hulpprogramma network shell:

```
netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}
```

De opgegeven waarde voor het argument certhash is hier de vingerafdruk van het certificaat, terwijl de waarde die is opgegeven voor het argument appid een wereldwijd unieke identifier is.  

Als u de service in Internet Information Services wilt hosten, bouwt een ontwikkelaar een CLI-code bibliotheek-assembly met een klasse met de naam Startup in de standaard naam ruimte van de assembly.  Hier volgt een voorbeeld van een klasse bestaat: 

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

### <a name="handling-endpoint-authentication"></a>Verwerking-eindpuntverificatie

Aanvragen van Azure Active Directory bevatten een OAuth 2.0-bearer-token.   Alle services die de aanvraag ontvangen, moeten de verlener verifiëren als Azure Active Directory voor de verwachte Azure Active Directory Tenant, voor toegang tot de Microsoft Graph API-service.  In het token wordt de verlener geïdentificeerd door een ISS-claim, zoals "ISS": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  In dit voor beeld is het basis adres van de claim waarde, https://sts.windows.net, Azure Active Directory als verlener geïdentificeerd, terwijl het relatieve adres segment cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 een unieke id is van de Azure Active Directory Tenant waarvoor het token is uitgegeven. De doel groep van het token is de toepassings sjabloon-ID voor de app in de galerie. De toepassings sjabloon-ID voor alle aangepaste apps is 8adf8e6e-67b2-4cf2-A259-e3dc5476c621. De toepassings sjabloon-ID voor elke app in de galerie varieert. Neem contact op met ProvisioningFeedback@microsoft.com voor vragen over de toepassings sjabloon-ID voor een galerie toepassing. Elk van de toepassingen die in één Tenant zijn geregistreerd, kan dezelfde `iss` claim ontvangen met SCIM-aanvragen.

Ontwikkel aars die gebruikmaken van de CLI-bibliotheken van micro soft voor het bouwen van een SCIM-service kunnen aanvragen van Azure Active Directory met het pakket micro soft. Owin. Security. ActiveDirectory verifiëren door de volgende stappen te volgen: 

Ten eerste, in een provider, implementeert u de eigenschap micro soft. SystemForCrossDomainIdentityManagement. IProvider. StartupBehavior door het retour neren van een methode die wordt aangeroepen wanneer de service wordt gestart: 

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

Voeg vervolgens de volgende code toe aan deze methode om een aanvraag naar een van de service-eind punten te laten verifiëren als een token dat is uitgegeven door Azure Active Directory voor een opgegeven Tenant, voor toegang tot de Microsoft Graph API-service: 

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

***Voor beeld 1. Een query uitvoeren op de service voor een overeenkomende gebruiker***

Azure Active Directory vraagt de service voor een gebruiker met een externalId kenmerkwaarde die overeenkomt met de waarde van het kenmerk mailNickname van een gebruiker in Azure AD. De query wordt weer gegeven als een Hypertext Transfer Protocol (HTTP)-aanvraag, zoals dit voor beeld, waarbij jyoung een voor beeld is van een mailnickname van een gebruiker in Azure Active Directory.

>[!NOTE]
> Dit is een voor beeld. Niet alle gebruikers hebben een mailnickname-kenmerk en de waarde die een gebruiker heeft, is mogelijk niet uniek in de Directory. Het kenmerk dat wordt gebruikt voor het vergelijken (in dit geval externalId) kan ook worden geconfigureerd in de [kenmerk toewijzingen van Azure AD](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

Als de service is gebouwd met behulp van de CLI-bibliotheken die door micro soft zijn meegeleverd voor het implementeren van SCIM Services, wordt de aanvraag omgezet in een aanroep van de query methode van de provider van de service.  Dit is de handtekening van deze methode: 

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

Hier volgt de definitie van de interface Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

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

Als de service is gebouwd met behulp van de Common Language Infrastructure-bibliotheken geleverd door Microsoft voor het implementeren van services SCIM, wordt klikt u vervolgens de aanvraag omgezet in een aanroep van de Query-methode van de provider van de service.  Dit is de handtekening van deze methode: 

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

Hier volgt de definitie van de interface Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

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

In het volgende voorbeeld van een query voor een gebruiker met een opgegeven waarde voor het kenmerk externalId zijn de waarden van de argumenten doorgegeven aan de Query-methode: 
* parameters.AlternateFilters.Count: 1
* de parameters. AlternateFilters.ElementAt(0). AttributePath: "externalId"
* de parameters. AlternateFilters.ElementAt(0). Vergelijkingsoperator: ComparisonOperator.Equals
* de parameters. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

***Voor beeld 2. Een gebruiker inrichten***

Als het antwoord op een query naar de webservice voor een gebruiker met een externalId-kenmerk waarde die overeenkomt met de kenmerk waarde mailnickname van een gebruiker geen gebruikers retourneert, worden Azure Active Directory aanvragen dat de service een gebruiker inricht die overeenkomt met de-server in Azure Active Directory.  Hier volgt een voorbeeld van een aanvraag voor: 

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

De CLI-bibliotheken die door micro soft worden verschaft voor het implementeren van SCIM Services, vertalen die aanvraag naar een aanroep van de methode Create van de provider van de service.  De methode voor maken, heeft deze handtekening:

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
 // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

 System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
   Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
   string correlationIdentifier);
```

In een aanvraag voor het inrichten van een gebruiker, is de waarde van de resource-argument een exemplaar van de Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser klasse is gedefinieerd in de bibliotheek Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Als de aanvraag voor het inrichten van de gebruiker is geslaagd, wordt de implementatie van de methode verwacht om terug te keren een exemplaar van de Microsoft.SystemForCrossDomainIdentityManagement. Klasse van Core2EnterpriseUser, met de waarde van de id-eigenschap ingesteld op de unieke id van de nieuw ingerichte gebruiker.  

***Voor beeld 3. Een query uitvoeren op de huidige status van een gebruiker*** 

Voor het bijwerken van een gebruiker bekend is dat in een archief voor de klantidentiteit fronted door een SCIM bestaan, wordt Azure Active Directory uitgevoerd door het aanvragen van de huidige status van die gebruiker van de service met een aanvraag, zoals: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

In een service die is gebouwd met behulp van de CLI-bibliotheken die door micro soft worden meegeleverd voor het implementeren van SCIM Services, wordt de aanvraag omgezet in een aanroep van de methode ophalen van de provider van de service.  Dit is de handtekening van de methode ophalen:

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

In het voorbeeld van een aanvraag voor het ophalen van de huidige status van een gebruiker, zijn de waarden van de eigenschappen van het object dat is opgegeven als de waarde van het argument parameters als volgt uit: 
  
* Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Voor beeld 4. Een query uitvoeren op de waarde van een verwijzings kenmerk dat moet worden bijgewerkt*** 

Als een referentie kenmerk moet worden bijgewerkt, wordt door Azure Active Directory de service gevraagd om te bepalen of de huidige waarde van het referentie kenmerk in de identiteits opslag die door de service wordt aangestuurd, al overeenkomt met de waarde van dat kenmerk in azure Active Uitvoermap. Voor gebruikers is het enige kenmerk waarvan de huidige waarde op deze manier wordt gevraagd het kenmerk manager. Hier volgt een voorbeeld van een verzoek om te bepalen of het kenmerk manager van een bepaalde gebruiker-object op dat moment een bepaalde waarde heeft: 

Als de service is gebouwd met behulp van de CLI-bibliotheken die door micro soft zijn meegeleverd voor het implementeren van SCIM Services, wordt de aanvraag omgezet in een aanroep van de query methode van de provider van de service. De waarde van de eigenschappen van het object dat is opgegeven als de waarde van het argument parameters zijn als volgt: 
  
* parameters.AlternateFilters.Count: 2
* de parameters. AlternateFilters.ElementAt(x). AttributePath: 'ID'
* de parameters. AlternateFilters.ElementAt(x). Vergelijkingsoperator: ComparisonOperator.Equals
* instellen. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* de parameters. AlternateFilters.ElementAt(y). AttributePath: "manager"
* de parameters. AlternateFilters.ElementAt(y). Vergelijkingsoperator: ComparisonOperator.Equals
* instellen. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* de parameters. RequestedAttributePaths.ElementAt(0): 'ID'
* de parameters. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

Hier kan de waarde van de index x 0 zijn en de waarde van de index y kan 1 zijn, of de waarde van x kan 1 zijn, en de waarde van y kan 0 zijn, afhankelijk van de volg orde van de expressies van de filter query parameter.   

***Voor beeld 5. Aanvraag van Azure AD naar een SCIM-service om een gebruiker bij te werken*** 

Hier volgt een voorbeeld van een aanvraag van Azure Active Directory aan een service SCIM om bij te werken van een gebruiker: 

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

De Microsoft Common Language Infrastructure-bibliotheken voor het implementeren van services SCIM vertaalt de aanvraag in een aanroep van de Update-methode van de provider van de service. Dit is de handtekening van de Update-methode: 

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

In het voorbeeld van een aanvraag voor het bijwerken van een gebruiker, is het object dat is opgegeven als de waarde van de patch-argument waarden van deze eigenschappen: 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest als PatchRequest2). Operations.Count: 1
* (PatchRequest als PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
* (PatchRequest als PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "manager"
* (PatchRequest als PatchRequest2). Operations.ElementAt(0). Value.Count: 1
* (PatchRequest als PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Verwijzing: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest als PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Waarde: 2819c223-7f76-453a-919d-413861904646

***Voor beeld 6. De inrichting van een gebruiker ongedaan maken***

Voor het ongedaan maken van de inrichting van een gebruiker uit een identiteits opslag voor een SCIM-service, verzendt Azure AD een aanvraag, zoals:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

Als de service is gebouwd met behulp van de Common Language Infrastructure-bibliotheken geleverd door Microsoft voor het implementeren van services SCIM, wordt klikt u vervolgens de aanvraag omgezet in een aanroep naar de Delete-methode van de provider van de service.   Deze methode heeft deze handtekening: 

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

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Stap 4: uw SCIM-eind punt integreren met de Azure AD SCIM-client

Azure AD kan worden geconfigureerd voor het automatisch inrichten van toegewezen gebruikers en groepen aan toepassingen die een specifiek profiel van het [SCIM 2,0-protocol](https://tools.ietf.org/html/rfc7644)implementeren. De details van het profiel zijn beschreven in [stap 2: inzicht in de implementatie van Azure AD scim](#step-2-understand-the-azure-ad-scim-implementation).

Neem contact op met de provider van uw toepassing of de documentatie van de toepassingsprovider van uw voor overzichten van compatibiliteit met deze vereisten.

> [!IMPORTANT]
> De Azure AD SCIM-implementatie is gebaseerd op de Azure AD User Provisioning-Service, die is ontworpen om gebruikers voortdurend te synchroniseren tussen Azure AD en de doel toepassing, en een zeer specifieke set standaard bewerkingen uit te voeren. Het is belang rijk dat u dit gedrag begrijpt om inzicht te krijgen in het gedrag van de Azure AD SCIM-client. Zie voor meer informatie de sectie [cyclussen inrichten: eerste en incrementeel](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in de werking van [inrichten](how-provisioning-works.md).

### <a name="getting-started"></a>Aan de slag

Toepassingen die ondersteuning bieden voor het SCIM-profiel dat wordt beschreven in dit artikel kunnen worden verbonden met Azure Active Directory met de functie 'buiten de galerie application' van de Azure AD-toepassingsgalerie. Eenmaal verbinding hebben, voert Azure AD een synchronisatieproces voor elke 40 minuten waar deze query's van de toepassing SCIM eindpunt voor toegewezen gebruikers en groepen, en maakt of wijzigt u ze op basis van de details van de toewijzing.

**Verbinding maken met een toepassing die ondersteuning biedt voor SCIM:**

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com). Houd er rekening mee dat u toegang krijgt tot een gratis proef versie voor Azure Active Directory met P2-licenties door u aan te melden voor het [ontwikkelaars programma](https://developer.microsoft.com/office/dev-program)
2. Selecteer **bedrijfs toepassingen** in het linkerdeel venster. Er wordt een lijst met alle geconfigureerde apps weer gegeven, met inbegrip van apps die zijn toegevoegd vanuit de galerie.
3. Selecteer **+ nieuwe toepassing** > **alle** > **niet-galerie toepassing**.
4. Voer een naam in voor uw toepassing en selecteer **toevoegen** om een app-object te maken. De nieuwe app wordt toegevoegd aan de lijst met bedrijfs toepassingen en wordt geopend op het scherm voor het beheren van apps.

   ![scherm afbeelding toont de Azure AD-toepassings galerie](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD-toepassings galerie*

5. Selecteer in het scherm voor het beheren van apps de optie **inrichten** in het linkerdeel venster.
6. Selecteer in het menu **inrichtings modus** de optie **automatisch**.

   ![voor beeld: de inrichtings pagina van een app in de Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Inrichting configureren in de Azure Portal*

7. Voer in het veld **Tenant-URL** de URL in van het scim-eind punt van de toepassing. Voorbeeld: https://api.contoso.com/scim/
8. Als het SCIM-eind punt een OAuth Bearer-token van een andere uitgever dan Azure AD vereist, kopieert u het vereiste OAuth Bearer-token naar het veld optionele **geheime token** . Als dit veld leeg blijft, bevat Azure AD een OAuth Bearer-token dat is uitgegeven door Azure AD met elke aanvraag. Apps die gebruikmaken van Azure AD als id-provider kunnen dit door Azure AD uitgegeven token valideren. 
   > [!NOTE]
   > Het is ***niet*** raadzaam dit veld leeg te laten en te vertrouwen op een token dat door Azure AD wordt gegenereerd. Deze optie is voornamelijk beschikbaar voor test doeleinden.
9. Selecteer **verbinding testen** om te laten proberen Azure Active Directory verbinding te maken met het scim-eind punt. Als de poging mislukt, wordt er informatie over de fout weer gegeven.  

    > [!NOTE]
    > **Test verbindings** QUERY'S het scim-eind punt voor een gebruiker die niet bestaat, met behulp van een wille keurige GUID als de overeenkomende eigenschap die is geselecteerd in de Azure AD-configuratie. Het verwachte juiste antwoord is HTTP 200 OK met een leeg SCIM ListResponse-bericht.

10. Als de pogingen om verbinding te maken met de toepassing slagen, selecteert u **Opslaan** om de beheerders referenties op te slaan.
11. In de sectie **toewijzingen** zijn er twee sets met [kenmerk toewijzingen](customize-application-attributes.md): één voor gebruikers objecten en één voor groeps objecten. Selecteren om te controleren van de kenmerken die worden gesynchroniseerd vanuit Active Directory van Azure aan uw app. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers en groepen in uw app voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren.

    > [!NOTE]
    > U kunt desgewenst uitschakelen door het uitschakelen van de toewijzing van 'groepen'-synchronisatie van de groepsobjecten worden weergegeven.

12. Onder **instellingen**definieert het **bereik** veld welke gebruikers en groepen worden gesynchroniseerd. Selecteer **alleen toegewezen gebruikers en groepen synchroniseren** (aanbevolen) om gebruikers en groepen te synchroniseren die zijn toegewezen op het tabblad **gebruikers en groepen** .
13. Wanneer de configuratie is voltooid, stelt u de **inrichtings status** **in op aan**.
14. Selecteer **Opslaan** om de Azure AD-inrichtings service te starten.
15. Als u alleen toegewezen gebruikers en groepen wilt synchroniseren (aanbevolen), selecteert u het tabblad **gebruikers en groepen** en wijst u de gebruikers of groepen toe die u wilt synchroniseren.

Zodra de eerste cyclus is gestart, kunt u **inrichtings logboeken** selecteren in het linkerdeel venster om de voortgang te controleren. hier worden alle acties weer gegeven die door de inrichtings service in uw app worden uitgevoerd. Zie [rapportage over het automatisch inrichten van gebruikers accounts](check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

> [!NOTE]
> De eerste cyclus duurt langer dan de volgende synchronisaties, wat ongeveer elke 40 minuten gebeurt, zolang de service wordt uitgevoerd.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Stap 5: Publiceer uw toepassing in de Azure AD-toepassings galerie

Als u een toepassing bouwt die wordt gebruikt door meer dan één Tenant, kunt u deze beschikbaar maken in de Azure AD-toepassings galerie. Dit maakt het eenvoudig voor organisaties om de toepassing te detecteren en inrichtingen te configureren. Het publiceren van uw app in de Azure AD-galerie en het beschikbaar maken van de inrichting voor anderen is eenvoudig. Bekijk de stappen die [hier](../develop/howto-app-gallery-listing.md)worden beschreven. Micro soft werkt samen met u om uw toepassing te integreren in onze galerie, uw eind punt te testen en onboarding- [documentatie](../saas-apps/tutorial-list.md) voor klanten te publiceren. 

### <a name="gallery-onboarding-checklist"></a>Controle lijst voor onboarding van galerie
Volg de onderstaande controle lijst om ervoor te zorgen dat uw toepassing voorbereid snelle en klanten een soepele implementatie-ervaring hebben. De gegevens worden verzameld van u bij het onboarden naar de galerie. 
> [!div class="checklist"]
> * [Ondersteuning voor SCIM 2,0](https://tools.ietf.org/html/draft-wahl-scim-profile-00) (vereist)
> * Ten minste 25 aanvragen per seconde per Tenant ondersteunen (vereist)
> * Ondersteuning voor schema detectie (aanbevolen)
> * Ondersteuning voor de OAuth-autorisatie code subsidie of een lang bewaard token zoals hieronder wordt beschreven (vereist)
> * Een technisch en ondersteunings punt tot stand brengen ter ondersteuning van de voor bereiding van de galerie met klant berichten (vereist)
> * Uw SCIM-eind punt openbaar documenteren (aanbevolen) 


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorisatie voor het inrichten van connectors in de toepassings galerie
De SCIM spec definieert geen SCIM-specifiek schema voor verificatie en autorisatie. Het is afhankelijk van het gebruik van bestaande industrie normen. De Azure AD-inrichtings client ondersteunt twee autorisatie methoden voor toepassingen in de galerie. 

|Autorisatie methode|Professionals|Nadelen|Ondersteuning|
|--|--|--|--|
|Gebruikers naam en wacht woord (niet aanbevolen of niet ondersteund door Azure AD)|Eenvoudig te implementeren|Onveilig: [uw PA $ $Word is niet van belang](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Per geval ondersteund voor galerij-apps. Niet ondersteund voor niet-galerij-apps.|
|Token met lange levens drager (ondersteund door Azure AD op dit moment)|Voor tokens met een lange levens duur hoeft geen gebruiker aanwezig te zijn. Ze kunnen eenvoudig worden gebruikt bij het instellen van de inrichting.|Tokens met een lange levens duur kunnen moeilijk worden gedeeld met een beheerder zonder gebruik te maken van onveilige methoden zoals e-mail. |Ondersteund voor Gallery-en niet-galerij-apps. |
|Verleende OAuth-autorisatie code (ondersteund door Azure AD op dit moment)|Toegangs tokens zijn veel korter dan wacht woorden en beschikken over een mechanisme voor automatisch vernieuwen die de tokens van de lange levens drager niet hebben.  Er moet een echte gebruiker aanwezig zijn tijdens de eerste autorisatie, waarbij een verantwoordelijkheids niveau wordt toegevoegd. |Hiervoor moet een gebruiker aanwezig zijn. Als de gebruiker de organisatie verlaat, is het token ongeldig en moet de autorisatie opnieuw worden uitgevoerd.|Ondersteund voor galerie-apps. Er wordt ondersteuning geboden voor niet-galerij-apps.|
|OAuth-client referenties verlenen (niet ondersteund, op ons schema)|Toegangs tokens zijn veel korter dan wacht woorden en beschikken over een mechanisme voor automatisch vernieuwen die de tokens van de lange levens drager niet hebben. Zowel de autorisatie code Grant als de client referenties geven hetzelfde type toegangs token maken, zodat het verplaatsen tussen deze methoden transparant is voor de API.  Het inrichten kan volledig worden geautomatiseerd en nieuwe tokens kunnen zonder tussen komst van de gebruiker worden aangevraagd. ||Niet ondersteund voor apps uit de galerie en niet-galerij. Ondersteuning bevindt zich in onze achterstand.|

**Overdrachts stroom OAuth-autorisatie code:** De inrichtings service ondersteunt de [toekenning van autorisatie code](https://tools.ietf.org/html/rfc6749#page-24). Na het verzenden van uw aanvraag voor het publiceren van uw app in de galerie, zal ons team met u samen werken om de volgende informatie te verzamelen:
*  Autorisatie-URL: een URL door de client voor het verkrijgen van autorisatie van de resource-eigenaar via omleiding van gebruikers agent. De gebruiker wordt omgeleid naar deze URL om toegang te autoriseren. 
*  Token Exchange-URL: een URL door de client voor het uitwisselen van een autorisatie machtiging voor een toegangs token, meestal met client verificatie.
*  Client-ID: de autorisatie server geeft de geregistreerde client een client-id. Dit is een unieke teken reeks die de registratie gegevens vertegenwoordigt die door de client worden verstrekt.  De client-id is geen geheim. het wordt blootgesteld aan de resource-eigenaar en **mag niet** alleen voor client verificatie worden gebruikt.  
*  Client geheim: het client geheim is een geheim dat is gegenereerd door de autorisatie server. Dit moet een unieke waarde zijn die alleen bekend is bij de autorisatie server. 

OAuth v1 wordt niet ondersteund vanwege de bloot stelling aan het client geheim. OAuth v2 wordt ondersteund.  

Aanbevolen procedures (aanbevolen maar niet vereist):
* Ondersteuning voor meerdere omleidings-Url's. Beheerders kunnen het inrichten configureren van zowel ' portal.azure.com ' als ' aad.portal.azure.com '. Ondersteuning voor meerdere omleidings-Url's zorgt ervoor dat gebruikers toegang kunnen verlenen vanuit een van de portals.
* Meerdere geheimen ondersteunen om te zorgen voor een soepele geheime verlenging, zonder uitval tijd. 

**Lange levens duur van OAuth Bearer-tokens:** Als uw toepassing geen ondersteuning biedt voor de overdrachts stroom van de OAuth-autorisatie code, kunt u ook een lang bewaarde OAuth Bearer-token genereren dan die een beheerder kan gebruiken om de inrichtings integratie in te stellen. Het token moet permanent zijn, anders wordt de inrichtings taak in [quarantaine geplaatst](application-provisioning-quarantine-status.md) wanneer het token verloopt. Dit token moet kleiner zijn dan 1 KB.  

Laat het ons weten op [UserVoice](https://aka.ms/appprovisioningfeaturerequest)voor aanvullende verificatie-en autorisatie methoden.

### <a name="gallery-go-to-market-launch-check-list"></a>Controle lijst voor het starten van de galerie
We raden u aan om uw bestaande documentatie bij te werken en de integratie in uw marketing kanalen te verg Roten om de bewustmaking en vraag van onze gezamenlijke integratie te stimuleren.  Hieronder vindt u een aantal controle activiteiten die u het beste kunt volt ooien om de start te ondersteunen

* **Gereedheid voor verkoop-en klant ondersteuning.** Zorg ervoor dat uw verkoop-en ondersteunings teams op de hoogte zijn en dat ze kunnen praten met de integratie mogelijkheden. Geef uw verkoop-en ondersteunings team een korte beschrijving en voeg de integratie in uw verkoop materialen toe. 
* **Blog bericht en/of druk op release.** Maak een blog bericht of druk op release met een beschrijving van de gezamenlijke integratie, de voor delen en hoe u aan de slag gaat. [Voor beeld: Imprivata en Azure Active Directory pers release](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Sociale media.** Maak gebruik van uw sociale media, zoals Twitter, Facebook of LinkedIn, om de integratie van uw klanten te stimuleren. Zorg ervoor dat @AzureAD worden toegevoegd, zodat we uw post kunnen retweets. [Voor beeld: Imprivata Twitter post](https://twitter.com/azuread/status/1123964502909779968)
* **Marketing website.** Maak of werk uw marketing pagina's toe (bijvoorbeeld integratie pagina, partner pagina, prijs pagina enz.) om de beschik baarheid van de gezamenlijke integratie op te halen. [Voor beeld: Pingboard-integratie pagina](https://pingboard.com/org-chart-for), [Smart Sheet-integratie pagina](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), pagina met [Monday.com prijzen](https://monday.com/pricing/) 
* **Technische documentatie.** Maak een Help Center-artikel of technische documentatie over hoe klanten aan de slag kunnen. [Voor beeld: Envoy + Microsoft Azure Active Directory-integratie.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Communicatie van de klant.** Waarschuw klanten van de nieuwe integratie via uw klant communicatie (maandelijkse nieuws brieven, e-mail campagnes, opmerkingen bij de release van het product). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>IP-adressen die worden gebruikt door de Azure AD-inrichtings service toestaan om SCIM-aanvragen te maken

Bepaalde apps staan inkomend verkeer naar hun app toe. De Azure AD-inrichtings service werkt alleen zoals verwacht als de gebruikte IP-adressen zijn toegestaan. Zie voor een lijst met IP-adressen voor elke servicetag/regio het JSON-bestand- [Azure IP-bereiken en de service Tags – open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519). U kunt deze Ip's naar behoefte downloaden en Program meren in uw firewall. De gereserveerde IP-bereiken voor Azure AD-inrichting vindt u onder ' AzureActiveDirectoryDomainServices '.

## <a name="related-articles"></a>Verwante artikelen

* [Gebruikers inrichten en het ongedaan maken van de inrichting van SaaS-apps automatiseren](user-provisioning.md)
* [Kenmerk toewijzingen voor gebruikers inrichting aanpassen](customize-application-attributes.md)
* [Expressies schrijven voor kenmerk toewijzingen](functions-for-customizing-application-data.md)
* [Filters voor het inrichten van gebruikers in bereik](define-conditional-rules-for-provisioning-user-accounts.md)
* [Meldingen voor het inrichten van accounts](user-provisioning.md)
* [Lijst met zelf studies voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
