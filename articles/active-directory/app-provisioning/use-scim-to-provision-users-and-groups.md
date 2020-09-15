---
title: Een SCIM-eind punt ontwikkelen voor het inrichten van gebruikers voor apps vanuit Azure AD
description: Met het systeem voor Identity Management (SCIM) met meerdere domeinen wordt automatische gebruikers inrichting gestandaardiseerd. Leer hoe u een SCIM-eind punt ontwikkelt, uw SCIM-API integreert met Azure Active Directory en beginnen met het automatiseren van het inrichten van gebruikers en groepen in uw Cloud toepassingen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: fc77d8cbb88385d9be65ccb8df80e922704640a4
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563802"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-ad"></a>Een SCIM-eind punt bouwen en gebruikers inrichten met Azure AD configureren

Als ontwikkelaar van een toepassing kunt u het systeem gebruiken voor SCIM-gebruikers beheer-API (Cross-Domain Identity Management) om het automatisch inrichten van gebruikers en groepen tussen uw toepassing en Azure AD mogelijk te maken. In dit artikel wordt beschreven hoe u een SCIM-eind punt bouwt en integreert met de Azure AD-inrichtings service. De SCIM-specificatie biedt een gemeen schappelijk gebruikers schema voor het inrichten van. Bij gebruik in combi natie met Federatie standaarden zoals SAML of OpenID Connect Connect biedt beheerders een end-to-end, op standaarden gebaseerde oplossing voor toegangs beheer.

SCIM is een gestandaardiseerde definitie van twee eind punten: een/users-eind punt en een/groups-eind punt. Er worden algemene REST-werk woorden gebruikt om objecten te maken, bij te werken en te verwijderen, en een vooraf gedefinieerd schema voor algemene kenmerken zoals groeps naam, gebruikers naam, voor naam, achternaam en e-mail adres. Apps die een SCIM 2,0-REST API bieden, kunnen de pijn van het werken met een eigen API voor gebruikers beheer verminderen of elimineren. Elke compatibele SCIM-client weet bijvoorbeeld hoe u een HTTP POST van een JSON-object naar het/users-eind punt kunt maken om een nieuwe gebruikers vermelding te maken. Apps die voldoen aan de SCIM-standaard kunnen direct profiteren van bestaande clients, hulpprogram ma's en code, in plaats van een iets andere API voor dezelfde basis acties te hoeven gebruiken. 

![Inrichten vanuit Azure AD naar een app met SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Met het standaard gebruikers object schema en de rest Api's voor beheer dat is gedefinieerd in SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) kunnen id-providers en apps gemakkelijker met elkaar worden geïntegreerd. Toepassings ontwikkelaars die een SCIM-eind punt bouwen, kunnen worden geïntegreerd met elke SCIM-compatibele client zonder aangepaste werk.

Voor het automatiseren van de inrichting van een toepassing moet u een SCIM-eind punt bouwen en integreren met de Azure AD SCIM-client. Voer de volgende stappen uit om het inrichten van gebruikers en groepen in uw toepassing te starten. 
    
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
|voornaam|name. naam|givenName|
|achternaam|naam. lastName|achternaam|
|workMail|E-mail berichten [type EQ "werk]. waarde|Mail|
|manager|manager|manager|
|tag|urn: IETF: params: scim: schemas: extension: 2.0: CustomExtension: tag|extensionAttribute1|
|status|actief|isSoftDeleted (berekende waarde niet opgeslagen op gebruiker)|

Het hierboven gedefinieerde schema wordt weer gegeven met behulp van de JSON-nettolading hieronder. Houd er rekening mee dat naast de vereiste kenmerken voor de toepassing, de JSON-weer gave de vereiste `id` `externalId` kenmerken, en `meta` attributen bevat.

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

| Azure Active Directory gebruiker | "urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: User" |
| --- | --- |
| IsSoftDeleted |actief |
|department|urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: gebruiker: Department|
| displayName |displayName |
|employeeId|urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: employeeNumber|
| Fax-TelephoneNumber |phoneNumbers [type EQ "fax"]. waarde |
| givenName |name. naam |
| jobTitle |title |
| mail |e-mail berichten [type EQ "werk]. waarde |
| mailNickname |externalId |
| manager |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: Manager |
| mobiel |phoneNumbers [type EQ "Mobile"]. waarde |
| postalCode |adressen [type EQ "werk]. post code |
| proxy-adressen |e-mail berichten [type EQ "Overig"]. Value |
| fysieke levering-Office-locatie |adressen [type EQ "other"]. Opgemaakt |
| streetAddress |adressen [type EQ "werk]. streetAddress |
| surname |naam. familielid |
| telefoon nummer |phoneNumbers [type EQ "werk]. waarde |
| gebruiker-principalnaam |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabel 3: standaard toewijzing van groeps kenmerken

| Azure Active Directory groep | urn: IETF: params: scim: schemas: kern: 2.0: groep |
| --- | --- |
| displayName |displayName |
| mail |e-mail berichten [type EQ "werk]. waarde |
| mailNickname |displayName |
| leden |leden |
| objectId |externalId |
| proxyAddresses |e-mail berichten [type EQ "Overig"]. Value |

Er zijn verschillende eind punten gedefinieerd in de SCIM RFC. U kunt aan de slag met het/User-eind punt en vervolgens van daaruit uitbreiden. Het/schemas-eind punt is handig wanneer u aangepaste kenmerken gebruikt of als uw schema regel matig wordt gewijzigd. Hiermee kan een client automatisch het meest recente schema ophalen. Het/bulk-eind punt is vooral handig bij het ondersteunen van groepen. De volgende tabel beschrijft de verschillende eind punten die zijn gedefinieerd in de SCIM-standaard. Het/schemas-eind punt is handig wanneer u aangepaste kenmerken gebruikt of als uw schema regel matig wordt gewijzigd. Hiermee kan een client automatisch het meest recente schema ophalen. Het/bulk-eind punt is vooral handig bij het ondersteunen van groepen. De volgende tabel beschrijft de verschillende eind punten die zijn gedefinieerd in de SCIM-standaard. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabel 4: Bepaal de eind punten die u wilt ontwikkelen
|ENDPOINTS|BESCHRIJVING|
|--|--|
|/User|RUWE bewerkingen op een gebruikers object uit te voeren.|
|/Group|RUWE bewerkingen uitvoeren op een groeps object.|
|/ServiceProviderConfig|Bevat informatie over de functies van de SCIM-standaard die worden ondersteund, bijvoorbeeld de bronnen die worden ondersteund en de verificatie methode.|
|/ResourceTypes|Hiermee geeft u de meta gegevens van elke resource op|
|/Schemas|De set met kenmerken die door elke client en service provider wordt ondersteund, kan variëren. Een service provider kan `name` , `title` , en `emails` , terwijl een andere service provider, `name` `title` `phoneNumbers` , en. Het eind punt voor schema's maakt het mogelijk om de kenmerken te detecteren die worden ondersteund.|
|/Bulk|Met bulk bewerkingen kunt u bewerkingen uitvoeren op een grote verzameling Resource objecten in één bewerking (zoals lidmaatschappen van updates voor een grote groep).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Stap 2: inzicht in de implementatie van Azure AD SCIM
> [!IMPORTANT]
> Het gedrag van de Azure AD SCIM-implementatie is voor het laatst bijgewerkt op 18 december 2018. Zie [SCIM 2,0-protocol compatibiliteit van de Azure AD User Provisioning Service](application-provisioning-config-problem-scim-compatibility.md)voor meer informatie over wat er is gewijzigd.

Als u een toepassing bouwt die een SCIM 2,0-gebruikers beheer-API ondersteunt, wordt in deze sectie in detail beschreven hoe de Azure AD SCIM-client is geïmplementeerd. Ook wordt uitgelegd hoe u de verwerking van SCIM-protocol aanvragen en-antwoorden kunt model leren. Nadat u uw SCIM-eind punt hebt geïmplementeerd, kunt u dit testen door de procedure te volgen die in de vorige sectie is beschreven.

Binnen de [SCIM 2,0-protocol specificatie](http://www.simplecloud.info/#Specification)moet uw toepassing aan de volgende vereisten voldoen:

* Biedt ondersteuning voor het maken van gebruikers, en optioneel ook groepen, zoals wordt bepaald door para graaf [3,3 van het scim-protocol](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Ondersteunt het wijzigen van gebruikers of groepen met PATCH aanvragen, zoals wordt bepaald door [de sectie 3.5.2 van het scim-protocol](https://tools.ietf.org/html/rfc7644#section-3.5.2). Ondersteuning garandeert dat groepen en gebruikers op een uitvoerende manier worden ingericht. 
* Biedt ondersteuning voor het ophalen van een bekende resource voor een eerder gemaakte gebruiker of groep, zoals wordt bepaald door [de sectie 3.4.1 van het scim-protocol](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Biedt ondersteuning voor het uitvoeren van query's op gebruikers of groepen, zoals wordt bepaald door sectie [3.4.2 van het scim-protocol](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Gebruikers worden standaard opgehaald door hun en er wordt een query uitgevoerd op `id` de `username` `externalId` groepen en en er wordt een query uitgevoerd `displayName` .  
* Biedt ondersteuning voor het uitvoeren van query's op de gebruiker door de ID en per Manager, zoals wordt bepaald door de sectie 3.4.2 van het SCIM-protocol.  
* Ondersteunt het opvragen van groepen op ID en op lid, zoals wordt bepaald door de sectie 3.4.2 van het SCIM-protocol.  
* Hiermee wordt één Bearer-token geaccepteerd voor verificatie en autorisatie van Azure AD voor uw toepassing.
* Ondersteunt het voorlopig verwijderen van een gebruiker `active=false` en het herstellen `active=true` van de gebruiker (het gebruikers object moet worden geretourneerd in een aanvraag, ongeacht of de gebruiker actief is). De enige keer dat de gebruiker niet wordt geretourneerd, is wanneer deze permanent wordt verwijderd uit de toepassing. 

Volg deze algemene richt lijnen bij het implementeren van een SCIM-eind punt om te zorgen voor compatibiliteit met Azure AD:

* `id` is een vereiste eigenschap voor alle resources. Elk antwoord dat een resource retourneert, moet ervoor zorgen dat elke resource deze eigenschap bevat, met uitzonde ring van `ListResponse` nul leden.
* Antwoord op een query/filter aanvraag moet altijd a zijn `ListResponse` .
* Groepen zijn optioneel, maar worden alleen ondersteund als de implementatie van de SCIM PATCH aanvragen ondersteunt.
* Het is niet nodig om de volledige resource op te neemen in de reactie van de PATCH.
* Microsoft Azure AD maakt alleen gebruik van de volgende Opera tors:  
    - `eq`
    - `and`
* Geen hoofdletter gevoelige overeenkomst voor structurele elementen in SCIM vereist, in het bijzonder PATCH `op` bewerkings waarden, zoals gedefinieerd in https://tools.ietf.org/html/rfc7644#section-3.5.2 . Azure AD verzendt de waarden van ' op ' als `Add` , `Replace` en `Remove` .
* Microsoft Azure AD maakt aanvragen voor het ophalen van een wille keurige gebruiker en groep om ervoor te zorgen dat het eind punt en de referenties geldig zijn. Het wordt ook uitgevoerd als onderdeel van een **test verbindings** stroom in de [Azure Portal](https://portal.azure.com). 
* Het kenmerk waarin de resources kunnen worden opgevraagd, moet worden ingesteld als een overeenkomend kenmerk op de toepassing in de [Azure Portal](https://portal.azure.com). Zie voor meer informatie [aanpassen van kenmerk toewijzingen](customize-application-attributes.md) voor het inrichten van gebruikers
* Ondersteuning voor HTTPS op uw SCIM-eind punt

### <a name="user-provisioning-and-deprovisioning"></a>Gebruikers inrichten en de inrichting ongedaan maken

In de volgende afbeelding ziet u de berichten die Azure Active Directory verzendt naar een SCIM-service om de levens cyclus van een gebruiker in de identiteits opslag van uw toepassing te beheren.  

![Hiermee wordt de volg orde van de gebruikers inrichting en het ongedaan maken van de inrichting weer gegeven](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Volg orde voor het inrichten en opheffen van de inrichting van gebruikers*

### <a name="group-provisioning-and-deprovisioning"></a>Groeps inrichting en ongedaan maken van inrichting

Het inrichten en het ongedaan maken van de inrichting van groepen is optioneel. Wanneer de volgende afbeelding is geïmplementeerd en ingeschakeld, ziet u de berichten die Azure AD verzendt naar een SCIM-service om de levens cyclus van een groep in de identiteits opslag van uw toepassing te beheren.  Deze berichten verschillen van de berichten over gebruikers op twee manieren:

* Bij aanvragen voor het ophalen van groepen wordt opgegeven dat het kenmerk members moet worden uitgesloten van alle resources die zijn opgegeven in reactie op de aanvraag.  
* Aanvragen om te bepalen of een verwijzings kenmerk een bepaalde waarde heeft aanvragen voor het kenmerk members.  

![Toont de volg orde van de groeps inrichting en het ongedaan maken van de inrichting](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Volg orde van groeps inrichting en ongedaan maken van inrichting*

### <a name="scim-protocol-requests-and-responses"></a>Aanvragen en antwoorden van het SCIM-Protocol
Deze sectie bevat voor beelden van SCIM-aanvragen die worden verzonden door de Azure AD SCIM-client en voor beelden van verwachte reacties. Voor de beste resultaten moet u de app coderen om deze aanvragen in deze indeling af te handelen en de verwachte reacties te verzenden.

> [!IMPORTANT]
> Als u wilt weten hoe en wanneer de Azure AD User Provisioning-Service de hieronder beschreven bewerkingen afrondt, raadpleegt u de sectie [cycli inrichten: eerste en incrementeel](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in de werking van [inrichten](how-provisioning-works.md).

[Gebruikers bewerkingen](#user-operations)
  - [Gebruiker maken](#create-user) ([Request](#request)  /  [antwoord](#response)aanvraag)
  - [Gebruiker ophalen](#get-user) ([Request](#request-1)  /  [reactie](#response-1)op aanvraag)
  - [Gebruiker ophalen op basis van query](#get-user-by-query) (antwoord van[aanvraag](#request-2)  /  [Response](#response-2))
  - [Gebruiker ophalen op basis van query-nul resultaten](#get-user-by-query---zero-results) (reactie van[aanvraag](#request-3) 
/  [Response](#response-3))
  - [Gebruiker bijwerken [eigenschappen met meerdere waarden]](#update-user-multi-valued-properties) ([Request](#request-4)  /   [antwoord](#response-4)van aanvraag)
  - [Update gebruiker [eigenschappen met één waarde]](#update-user-single-valued-properties) ([Request](#request-5) 
/  [antwoord](#response-5)van aanvraag) 
  - [Gebruiker uitschakelen](#disable-user) ([Request](#request-14)  / 
 [antwoord](#response-14)aanvraag)
  - [Gebruiker verwijderen](#delete-user) ([Request](#request-6)  / 
 [antwoord](#response-6)aanvraag)


[Groeps bewerkingen](#group-operations)
  - [Groep maken](#create-group) ( [Request](#request-7)  /  [antwoord](#response-7)van aanvraag)
  - [Groep ophalen](#get-group) ( [Request](#request-8)  /  [reactie](#response-8)op aanvraag)
  - [Groeperen op DisplayName ophalen](#get-group-by-displayname) ([Request](#request-9)  /  [reactie](#response-9)op aanvraag)
  - [Update groep [kenmerken die geen deel](#update-group-non-member-attributes) uitmaken] ([aanvraag](#request-10) /
  [antwoord](#response-10))
  - [Update groep [leden toevoegen]](#update-group-add-members) ( [aanvraag](#request-11)  /
 [antwoord](#response-11))
  - [Update groep [leden verwijderen]](#update-group-remove-members) ( [Request](#request-12)  /
 [reactie](#response-12)van aanvraag)
  - [Groep verwijderen](#delete-group) ([Request](#request-13)  /
 [antwoord](#response-13)op aanvraag)

### <a name="user-operations"></a>Gebruikers bewerkingen

* Gebruikers kunnen worden doorzocht met `userName` of- `email[type eq "work"]` kenmerken.  

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

###### <a name="request"></a><a name="request-1"></a>Aanvraag
*/Users/5d48a0a8e9f04aa38008 ophalen* 

###### <a name="response-user-found"></a><a name="response-1"></a>Antwoord (gebruiker gevonden)
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

##### <a name="request"></a><a name="request-2"></a>Aanvraag

*/Users ophalen? filter = gebruikers naam EQ "Test_User_dfeef4c5-5681 -4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Antwoord

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

##### <a name="request"></a><a name="request-3"></a>Aanvraag

*/Users ophalen? filter = gebruikers naam EQ "niet-bestaande gebruiker"*

##### <a name="response"></a><a name="response-3"></a>Antwoord

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

##### <a name="request"></a><a name="request-4"></a>Aanvraag

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

##### <a name="response"></a><a name="response-4"></a>Antwoord

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

##### <a name="request"></a><a name="request-5"></a>Aanvraag

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

##### <a name="response"></a><a name="response-5"></a>Antwoord

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

##### <a name="response"></a><a name="response-14"></a>Antwoord

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

*/Users/5171a35d82074e068ce2 HTTP/1.1 verwijderen*

##### <a name="response"></a><a name="response-6"></a>Antwoord

*HTTP/1.1 204 geen inhoud*

### <a name="group-operations"></a>Groeps bewerkingen

* Groepen moeten altijd worden gemaakt met een lijst met lege leden.
* Groepen kunnen worden doorzocht op het `displayName` kenmerk.
* Update naar de groeps PATCH-aanvraag moet resulteren in een *HTTP 204 geen inhoud* in het antwoord. Het retour neren van een hoofd tekst met een lijst met alle leden is niet aanbevolen.
* Het is niet nodig om te ondersteunen bij het retour neren van alle leden van de groep.

#### <a name="create-group"></a>Groep maken

##### <a name="request"></a><a name="request-7"></a>Aanvraag

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

##### <a name="response"></a><a name="response-7"></a>Antwoord

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

##### <a name="request"></a><a name="request-8"></a>Aanvraag

*/Groups/40734ae655284ad3abcc ophalen? excludedAttributes = leden HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Antwoord
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

##### <a name="request"></a><a name="request-9"></a>Aanvraag
*/Groups ophalen? excludedAttributes = leden&filter = DisplayName EQ "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Antwoord

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

##### <a name="request"></a><a name="request-10"></a>Aanvraag

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

##### <a name="response"></a><a name="response-10"></a>Antwoord

*HTTP/1.1 204 geen inhoud*

### <a name="update-group-add-members"></a>Groep bijwerken [leden toevoegen]

##### <a name="request"></a><a name="request-11"></a>Aanvraag

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

##### <a name="response"></a><a name="response-11"></a>Antwoord

*HTTP/1.1 204 geen inhoud*

#### <a name="update-group-remove-members"></a>Groep bijwerken [leden verwijderen]

##### <a name="request"></a><a name="request-12"></a>Aanvraag

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

##### <a name="response"></a><a name="response-12"></a>Antwoord

*HTTP/1.1 204 geen inhoud*

#### <a name="delete-group"></a>Groep verwijderen

##### <a name="request"></a><a name="request-13"></a>Aanvraag

*/Groups/cdb1ce18f65944079d37 HTTP/1.1 verwijderen*

##### <a name="response"></a><a name="response-13"></a>Antwoord

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

### <a name="ip-ranges"></a>IP-bereiken
De Azure AD-inrichtings service werkt momenteel met de IP-bereiken voor AzureActiveDirectory zoals [hier](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all)wordt vermeld. U kunt de IP-bereiken onder het label AzureActiveDirectory toevoegen om verkeer van de Azure AD-inrichtings service toe te staan in uw toepassing. 

## <a name="step-3-build-a-scim-endpoint"></a>Stap 3: een SCIM-eind punt bouwen

Nu u uw schema hebt ontworpen en de Azure AD SCIM-implementatie hebt begrepen, kunt u aan de slag met het ontwikkelen van uw SCIM-eind punt. In plaats van helemaal zelf te beginnen en de implementatie helemaal zelf te bouwen, kunt u vertrouwen op een aantal open source SCIM-bibliotheken die zijn gepubliceerd door de SCIM-community.

De open-source .NET core- [referentie code](https://aka.ms/SCIMReferenceCode) die door het Azure AD-inrichtings team is gepubliceerd, is een van de bronnen waarmee u uw ontwikkeling kunt starten. Nadat u het SCIM-eind punt hebt gemaakt, moet u het testen. U kunt de verzameling [postman-testen](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) gebruiken die deel uitmaakt van de referentie code of door loop de [hierboven](#user-operations)vermelde voorbeeld aanvragen/antwoorden.  

   > [!Note]
   > De referentie code is bedoeld om u te helpen bij het bouwen van uw SCIM-eind punt en wordt ' AS IS ' weer gegeven. Bijdragen van de community zijn welkom bij het bouwen en onderhouden van de code.

De oplossing bestaat uit twee projecten: _micro soft. scim_ en _micro soft. scim. WebHostSample_.

Het project _micro soft. scim_ is de bibliotheek waarin de onderdelen van de webservice worden gedefinieerd die voldoen aan de scim-specificatie. Het declareert de interface _micro soft. scim. IProvider._ aanvragen worden omgezet in aanroepen naar de methoden van de provider, die worden geprogrammeerd om te worden uitgevoerd op een identiteits opslag.

![Uitsplitsing: een aanvraag die is vertaald naar de methoden van de provider](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Het _micro soft. scim. WebHostSample_ -project is een Visual Studio ASP.net core-webtoepassing, op basis van de _lege_ sjabloon. Hiermee kan de voorbeeld code worden geïmplementeerd als zelfstandig, gehost in containers of in Internet Information Services. Ook wordt de interface _micro soft. scim. IProvider_ geïmplementeerd, waarbij klassen in het geheugen worden bewaard als een voor beeld van een identiteits opslag.

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

### <a name="building-a-custom-scim-endpoint"></a>Een aangepast SCIM-eind punt bouwen

De SCIM-service moet een HTTP-adres en Server verificatie certificaat hebben waarvan de basis certificerings instantie een van de volgende namen is:

* CNNIC
* Comodo
* Cyber Trust
* DigiCert
* GeoTrust
* GlobalSign
* Ga Daddy
* VeriSign
* WoSign

Het .NET Core SDK bevat een HTTPS-ontwikkelings certificaat dat kan worden gebruikt tijdens de ontwikkeling. het certificaat wordt geïnstalleerd als onderdeel van de eerste sessie. Afhankelijk van hoe u de ASP.NET Core-webtoepassing uitvoert, wordt deze naar een andere poort geluisterd:

* Micro soft. SCIM. WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

Gebruik de volgende koppeling voor meer informatie over HTTPS in ASP.NET Core: [https afdwingen in ASP.net core](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Verificatie van eind punten afhandelen

Aanvragen van Azure Active Directory een OAuth 2,0 Bearer-token bevatten. Alle services die de aanvraag ontvangen, moeten de verlener verifiëren als Azure Active Directory voor de verwachte Azure Active Directory Tenant.

In het token wordt de verlener geïdentificeerd aan de hand van een ISS-claim, zoals `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"` . In dit voor beeld is het basis adres van de claim waarde, `https://sts.windows.net` dat Azure Active Directory als de verlener identificeert, terwijl het relatieve adres segment _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_een unieke id is van de Azure Active Directory Tenant waarvoor het token is uitgegeven.

De doel groep van het token is de toepassings sjabloon-ID voor de toepassing in de galerie. elk van de toepassingen die in één Tenant zijn geregistreerd, kan dezelfde `iss` claim ontvangen met scim-aanvragen. De toepassings sjabloon-ID voor alle aangepaste apps is _8adf8e6e-67b2-4cf2-A259-e3dc5476c621_. Het token dat is gegenereerd door de Azure AD-inrichtings service mag alleen worden gebruikt voor het testen. Het mag niet worden gebruikt in productie omgevingen.

In de voorbeeld code worden aanvragen geverifieerd met het pakket micro soft. AspNetCore. Authentication. JwtBearer. De volgende code dwingt dat aanvragen voor een van de service-eind punten worden geverifieerd met behulp van het Bearer-token dat is uitgegeven door Azure Active Directory voor een opgegeven Tenant:

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

Een Bearer-token is ook vereist voor het gebruik van de opgegeven [postman-tests](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) en voor het uitvoeren van lokale fout opsporing met localhost. De voorbeeld code maakt gebruik van ASP.NET Core omgevingen om de verificatie opties tijdens de ontwikkelings fase te wijzigen en het gebruik van een zelfondertekend token in te scha kelen.

Voor meer informatie over meerdere omgevingen in ASP.NET Core gebruikt u de volgende koppeling: [meerdere omgevingen gebruiken in ASP.net core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

De volgende code dwingt dat aanvragen voor een van de service-eind punten worden geverifieerd met behulp van een Bearer-token dat is ondertekend met een aangepaste sleutel:

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

Een GET-aanvraag naar de token controller verzenden om een geldig Bearer-token op te halen. de methode _GenerateJSONWebToken_ is verantwoordelijk voor het maken van een token dat overeenkomt met de para meters die zijn geconfigureerd voor ontwikkeling:

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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Inrichting en ongedaan maken van de inrichting van gebruikers afhandelen

***Voor beeld 1. Een query uitvoeren op de service voor een overeenkomende gebruiker***

Azure Active Directory vraagt de service om een gebruiker met een `externalId` kenmerk waarde die overeenkomt met de kenmerk waarde mailnickname van een gebruiker in azure AD. De query wordt weer gegeven als een Hypertext Transfer Protocol (HTTP)-aanvraag, zoals dit voor beeld, waarbij jyoung een voor beeld is van een mailnickname van een gebruiker in Azure Active Directory.

>[!NOTE]
> Dit is een voor beeld. Niet alle gebruikers hebben een mailnickname-kenmerk en de waarde die een gebruiker heeft, is mogelijk niet uniek in de Directory. Het kenmerk dat wordt gebruikt voor het vergelijken (in dit geval `externalId` ) kan ook worden geconfigureerd in de [kenmerk toewijzingen van Azure AD](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

In de voorbeeld code wordt de aanvraag omgezet in een aanroep naar de methode QueryAsync van de provider van de service. Dit is de hand tekening van deze methode: 

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

In de voorbeeld query voor een gebruiker met een opgegeven waarde voor het `externalId` kenmerk zijn de waarden van de argumenten die zijn door gegeven aan de methode QueryAsync:

* instellen. AlternateFilters. Count: 1
* instellen. AlternateFilters. ElementAt (0). AttributePath: "externalId"
* instellen. AlternateFilters. ElementAt (0). ComparisonOperator: ComparisonOperator. is gelijk aan
* instellen. AlternateFilter. ElementAt (0). ComparisonValue: "jyoung"

***Voor beeld 2. Een gebruiker inrichten***

Als het antwoord op een query naar de webservice voor een gebruiker met een `externalId` kenmerk waarde die overeenkomt met de kenmerk waarde mailnickname van een gebruiker geen gebruikers retourneert, worden Azure Active Directory aanvragen die de service inricht, een gebruiker die overeenkomt met de service in azure Active Directory.  Hier volgt een voor beeld van een dergelijke aanvraag: 

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

In de voorbeeld code wordt de aanvraag omgezet in een aanroep naar de methode CreateAsync van de provider van de service. Dit is de hand tekening van deze methode: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

In een aanvraag om een gebruiker in te richten, is de waarde van het argument resource een exemplaar van de klasse micro soft. SCIM. Core2EnterpriseUser, die is gedefinieerd in de bibliotheek micro soft. SCIM. schemas.  Als de aanvraag om de gebruiker in te richten slaagt, wordt de implementatie van de methode verwacht een exemplaar van de klasse micro soft. SCIM. Core2EnterpriseUser te retour neren, waarbij de waarde van de eigenschap identifier is ingesteld op de unieke id van de nieuwe ingerichte gebruiker.  

***Voor beeld 3. Een query uitvoeren op de huidige status van een gebruiker*** 

Als u een gebruiker wilt bijwerken waarvan bekend is dat deze bestaat in een identiteits opslag die wordt gebruikt door een SCIM, wordt Azure Active Directory teruggevraagd door de huidige status van die gebruiker aan te vragen bij de service met een aanvraag zoals: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

In de voorbeeld code wordt de aanvraag omgezet in een aanroep naar de methode RetrieveAsync van de provider van de service. Dit is de hand tekening van deze methode: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

In het voor beeld van een aanvraag voor het ophalen van de huidige status van een gebruiker, zijn de waarden van de eigenschappen van het object die zijn opgegeven als de waarde van het argument para meters als volgt: 
  
* Id: ' 54D382A4-2050-4C03-94D1-E769F1D15682 '
* SchemaIdentifier: "urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: User"

***Voor beeld 4. Een query uitvoeren op de waarde van een verwijzings kenmerk dat moet worden bijgewerkt*** 

Als een verwijzings kenmerk moet worden bijgewerkt, wordt door Azure Active Directory de service gevraagd om te bepalen of de huidige waarde van het referentie kenmerk in de identiteits opslag die door de service wordt aangestuurd, al overeenkomt met de waarde van dat kenmerk in Azure Active Directory. Voor gebruikers is het kenmerk alleen van de Manager van het kenmerk waarvan de huidige waarde op deze manier wordt opgevraagd. Hier volgt een voor beeld van een aanvraag om te bepalen of het kenmerk Manager van een gebruikers object momenteel een bepaalde waarde heeft: in de voorbeeld code wordt de aanvraag omgezet in een aanroep van de QueryAsync-methode van de provider van de service. De waarde van de eigenschappen van het object die wordt opgegeven als de waarde van het argument para meters, zijn als volgt: 
  
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

***Voor beeld 5. Aanvraag van Azure AD naar een SCIM-service om een gebruiker bij te werken*** 

Hier volgt een voor beeld van een aanvraag van Azure Active Directory naar een SCIM-service om een gebruiker bij te werken: 

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

In de voorbeeld code wordt de aanvraag omgezet in een aanroep naar de methode UpdateAsync van de provider van de service. Dit is de hand tekening van deze methode: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
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

***Voor beeld 6. De inrichting van een gebruiker ongedaan maken***

Voor het ongedaan maken van de inrichting van een gebruiker uit een identiteits opslag voor een SCIM-service, verzendt Azure AD een aanvraag, zoals:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

In de voorbeeld code wordt de aanvraag omgezet in een aanroep naar de methode DeleteAsync van de provider van de service. Dit is de hand tekening van deze methode: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Het object dat als waarde voor het argument resourceIdentifier wordt gegeven, heeft deze eigenschaps waarden in het voor beeld van een aanvraag om de inrichting van een gebruiker ongedaan te maken: 

* ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier. SchemaIdentifier: "urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Stap 4: uw SCIM-eind punt integreren met de Azure AD SCIM-client

Azure AD kan worden geconfigureerd voor het automatisch inrichten van toegewezen gebruikers en groepen aan toepassingen die een specifiek profiel van het [SCIM 2,0-protocol](https://tools.ietf.org/html/rfc7644)implementeren. De details van het profiel zijn beschreven in [stap 2: inzicht in de implementatie van Azure AD scim](#step-2-understand-the-azure-ad-scim-implementation).

Neem contact op met uw toepassings provider of de documentatie van uw toepassings provider voor instructies over de compatibiliteit van deze vereisten.

> [!IMPORTANT]
> De Azure AD SCIM-implementatie is gebaseerd op de Azure AD User Provisioning-Service, die is ontworpen om gebruikers voortdurend te synchroniseren tussen Azure AD en de doel toepassing, en een zeer specifieke set standaard bewerkingen uit te voeren. Het is belang rijk dat u dit gedrag begrijpt om inzicht te krijgen in het gedrag van de Azure AD SCIM-client. Zie voor meer informatie de sectie [cyclussen inrichten: eerste en incrementeel](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) in de werking van [inrichten](how-provisioning-works.md).

### <a name="getting-started"></a>Aan de slag

Toepassingen die ondersteuning bieden voor het SCIM-profiel dat in dit artikel wordt beschreven, kunnen worden verbonden met Azure Active Directory met behulp van de functie ' niet-galerie toepassing ' in de Azure AD-toepassings galerie. Zodra de verbinding tot stand is gebracht, voert Azure AD elke 40 minuten een synchronisatie uit waarbij het SCIM-eind punt van de toepassing wordt opgevraagd voor toegewezen gebruikers en groepen, en worden ze gemaakt of gewijzigd op basis van de toewijzings Details.

**Verbinding maken met een toepassing die ondersteuning biedt voor SCIM:**

1. Meld u aan bij de [Azure Active Directory Portal](https://aad.portal.azure.com). Houd er rekening mee dat u toegang krijgt tot een gratis proef versie voor Azure Active Directory met P2-licenties door u aan te melden voor het [ontwikkelaars programma](https://developer.microsoft.com/office/dev-program)
2. Selecteer **bedrijfs toepassingen** in het linkerdeel venster. Er wordt een lijst met alle geconfigureerde apps weer gegeven, met inbegrip van apps die zijn toegevoegd vanuit de galerie.
3. Selecteer **+ nieuwe toepassing**  >  **alle**  >  **niet-galerie toepassingen**.
4. Voer een naam in voor uw toepassing en selecteer **toevoegen** om een app-object te maken. De nieuwe app wordt toegevoegd aan de lijst met bedrijfs toepassingen en wordt geopend op het scherm voor het beheren van apps.

   ![Scherm opname toont de Azure AD-toepassings galerie](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD-toepassings galerie*

5. Selecteer in het scherm voor het beheren van apps de optie **inrichten** in het linkerdeel venster.
6. Selecteer in het menu **inrichtings modus** de optie **automatisch**.

   ![Voor beeld: de inrichtings pagina van een app in de Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Inrichting configureren in de Azure Portal*

7. Voer in het veld **Tenant-URL** de URL in van het scim-eind punt van de toepassing. Voorbeeld: `https://api.contoso.com/scim/`
8. Als het SCIM-eind punt een OAuth Bearer-token van een andere uitgever dan Azure AD vereist, kopieert u het vereiste OAuth Bearer-token naar het veld optionele **geheime token** . Als dit veld leeg blijft, bevat Azure AD een OAuth Bearer-token dat is uitgegeven door Azure AD met elke aanvraag. Apps die gebruikmaken van Azure AD als id-provider kunnen dit door Azure AD uitgegeven token valideren. 
   > [!NOTE]
   > Het is ***niet*** raadzaam dit veld leeg te laten en te vertrouwen op een token dat door Azure AD wordt gegenereerd. Deze optie is voornamelijk beschikbaar voor test doeleinden.
9. Selecteer **verbinding testen** om te laten proberen Azure Active Directory verbinding te maken met het scim-eind punt. Als de poging mislukt, wordt er informatie over de fout weer gegeven.  

    > [!NOTE]
    > **Test verbindings** QUERY'S het scim-eind punt voor een gebruiker die niet bestaat, met behulp van een wille keurige GUID als de overeenkomende eigenschap die is geselecteerd in de Azure AD-configuratie. Het verwachte juiste antwoord is HTTP 200 OK met een leeg SCIM ListResponse-bericht.

10. Als de pogingen om verbinding te maken met de toepassing slagen, selecteert u **Opslaan** om de beheerders referenties op te slaan.
11. In de sectie **toewijzingen** zijn er twee sets met [kenmerk toewijzingen](customize-application-attributes.md): één voor gebruikers objecten en één voor groeps objecten. Selecteer elk item om de kenmerken te controleren die zijn gesynchroniseerd van Azure Active Directory naar uw app. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers en groepen in uw app voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen toe te passen.

    > [!NOTE]
    > U kunt de synchronisatie van groeps objecten eventueel uitschakelen door de toewijzing groepen uit te scha kelen.

12. Onder **instellingen**definieert het **bereik** veld welke gebruikers en groepen worden gesynchroniseerd. Selecteer **alleen toegewezen gebruikers en groepen synchroniseren** (aanbevolen) om gebruikers en groepen te synchroniseren die zijn toegewezen op het tabblad **gebruikers en groepen** .
13. Wanneer de configuratie is voltooid, stelt u de **inrichtings status** **in op aan**.
14. Selecteer **Opslaan** om de Azure AD-inrichtings service te starten.
15. Als u alleen toegewezen gebruikers en groepen wilt synchroniseren (aanbevolen), selecteert u het tabblad **gebruikers en groepen** en wijst u de gebruikers of groepen toe die u wilt synchroniseren.

Zodra de eerste cyclus is gestart, kunt u **inrichtings logboeken** selecteren in het linkerdeel venster om de voortgang te controleren. hier worden alle acties weer gegeven die door de inrichtings service in uw app worden uitgevoerd. Zie [Rapportage over automatische inrichting van gebruikersaccounts](check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

> [!NOTE]
> De eerste cyclus duurt langer dan de volgende synchronisaties, wat ongeveer elke 40 minuten gebeurt, zolang de service wordt uitgevoerd.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Stap 5: Publiceer uw toepassing in de Azure AD-toepassings galerie

Als u een toepassing bouwt die wordt gebruikt door meer dan één Tenant, kunt u deze beschikbaar maken in de Azure AD-toepassings galerie. Dit maakt het eenvoudig voor organisaties om de toepassing te detecteren en inrichtingen te configureren. Het publiceren van uw app in de Azure AD-galerie en het beschikbaar maken van de inrichting voor anderen is eenvoudig. Bekijk de stappen die [hier](../azuread-dev/howto-app-gallery-listing.md)worden beschreven. Micro soft werkt samen met u om uw toepassing te integreren in onze galerie, uw eind punt te testen en onboarding- [documentatie](../saas-apps/tutorial-list.md) voor klanten te publiceren. 

### <a name="gallery-onboarding-checklist"></a>Controle lijst voor onboarding van galerie
Volg de onderstaande controle lijst om ervoor te zorgen dat uw toepassing voorbereid snelle en klanten een soepele implementatie-ervaring hebben. De gegevens worden verzameld van u bij het onboarden naar de galerie. 
> [!div class="checklist"]
> * Een [SCIM 2,0 ](#step-2-understand-the-azure-ad-scim-implementation) -gebruikers-en-groeps eindpunt ondersteunen (er is slechts één vereist, maar beide worden aanbevolen)
> * Ten minste 25 aanvragen per seconde per Tenant ondersteunen om ervoor te zorgen dat gebruikers en groepen zonder vertraging worden ingericht en ongedaan gemaakt (vereist)
> * Technische hulp en ondersteunings contacten tot stand brengen om klanten te begeleiden post galerie voorbereiden (vereist)
> * 3 niet-verlopende test referenties voor uw toepassing (vereist)
> * Ondersteuning voor de OAuth-autorisatie code subsidie of een lang bewaard token zoals hieronder wordt beschreven (vereist)
> * Een technisch en ondersteunings punt tot stand brengen om klanten te ondersteunen bij het voorbereiden van de galerie van berichten (vereist)
> * Ondersteuning voor het bijwerken van meerdere groepslid maatschappen met één PATCH (aanbevolen) 
> * Uw SCIM-eind punt openbaar documenteren (aanbevolen) 
> * [Ondersteuning voor schema detectie](https://tools.ietf.org/html/rfc7643#section-6) (aanbevolen)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorisatie voor het inrichten van connectors in de toepassings galerie
De SCIM spec definieert geen SCIM-specifiek schema voor verificatie en autorisatie. Het is afhankelijk van het gebruik van bestaande industrie normen. De Azure AD-inrichtings client ondersteunt twee autorisatie methoden voor toepassingen in de galerie. 

|Verificatiemethode|Voordelen|Nadelen|Ondersteuning|
|--|--|--|--|
|Gebruikers naam en wacht woord (niet aanbevolen of niet ondersteund door Azure AD)|Eenvoudig te implementeren|Onveilig: [uw PA $ $Word is niet van belang](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Per geval ondersteund voor galerij-apps. Niet ondersteund voor niet-galerij-apps.|
|Token met lange levens drager|Voor tokens met een lange levens duur hoeft geen gebruiker aanwezig te zijn. Ze kunnen eenvoudig worden gebruikt bij het instellen van de inrichting.|Tokens met een lange levens duur kunnen moeilijk worden gedeeld met een beheerder zonder gebruik te maken van onveilige methoden zoals e-mail. |Ondersteund voor Gallery-en niet-galerij-apps. |
|Verleende OAuth-autorisatie code|Toegangs tokens zijn veel korter dan wacht woorden en beschikken over een mechanisme voor automatisch vernieuwen die de tokens van de lange levens drager niet hebben.  Er moet een echte gebruiker aanwezig zijn tijdens de eerste autorisatie, waarbij een verantwoordelijkheids niveau wordt toegevoegd. |Hiervoor moet een gebruiker aanwezig zijn. Als de gebruiker de organisatie verlaat, is het token ongeldig en moet de autorisatie opnieuw worden uitgevoerd.|Ondersteund voor galerij-apps, maar niet voor niet-galerij-apps. Ondersteuning voor niet-galerie is in onze achterstand.|
|Verleende OAuth-client referenties|Toegangs tokens zijn veel korter dan wacht woorden en beschikken over een mechanisme voor automatisch vernieuwen die de tokens van de lange levens drager niet hebben. Zowel de autorisatie code Grant als de client referenties geven hetzelfde type toegangs token maken, zodat het verplaatsen tussen deze methoden transparant is voor de API.  Het inrichten kan volledig worden geautomatiseerd en nieuwe tokens kunnen zonder tussen komst van de gebruiker worden aangevraagd. ||Niet ondersteund voor apps uit de galerie en niet-galerij. Ondersteuning bevindt zich in onze achterstand.|

> [!NOTE]
> Het is niet raadzaam om het veld token leeg te laten in de aangepaste app-gebruikers interface van de Azure AD-inrichtings configuratie. Het gegenereerde token is voornamelijk beschikbaar voor test doeleinden.

**Overdrachts stroom OAuth-autorisatie code:** De inrichtings service ondersteunt de [toekenning van autorisatie code](https://tools.ietf.org/html/rfc6749#page-24). Na het verzenden van uw aanvraag voor het publiceren van uw app in de galerie, zal ons team met u samen werken om de volgende informatie te verzamelen:
*  Autorisatie-URL: een URL door de client voor het verkrijgen van autorisatie van de resource-eigenaar via omleiding van gebruikers agent. De gebruiker wordt omgeleid naar deze URL om toegang te autoriseren. Houd er rekening mee dat deze URL momenteel niet kan worden geconfigureerd per Tenant.
*  Token Exchange-URL: een URL door de client voor het uitwisselen van een autorisatie machtiging voor een toegangs token, meestal met client verificatie. Houd er rekening mee dat deze URL momenteel niet kan worden geconfigureerd per Tenant.
*  Client-ID: de autorisatie server geeft de geregistreerde client een client-id. Dit is een unieke teken reeks die de registratie gegevens vertegenwoordigt die door de client worden verstrekt.  De client-id is geen geheim. het wordt blootgesteld aan de resource-eigenaar en **mag niet** alleen voor client verificatie worden gebruikt.  
*  Client geheim: het client geheim is een geheim dat is gegenereerd door de autorisatie server. Dit moet een unieke waarde zijn die alleen bekend is bij de autorisatie server. 

OAuth v1 wordt niet ondersteund vanwege de bloot stelling aan het client geheim. OAuth v2 wordt ondersteund.  

Aanbevolen procedures (aanbevolen maar niet vereist):
* Ondersteuning voor meerdere omleidings-Url's. Beheerders kunnen het inrichten configureren van zowel ' portal.azure.com ' als ' aad.portal.azure.com '. Ondersteuning voor meerdere omleidings-Url's zorgt ervoor dat gebruikers toegang kunnen verlenen vanuit een van de portals.
* Meerdere geheimen ondersteunen om te zorgen voor een soepele geheime verlenging, zonder uitval tijd. 

**Langlopende OAuth Bearer-tokens:** Als uw toepassing geen ondersteuning biedt voor de overdrachts stroom van de OAuth-autorisatie code, kunt u ook een lang bewaarde OAuth Bearer-token genereren dan die een beheerder kan gebruiken om de inrichtings integratie in te stellen. Het token moet permanent zijn, anders wordt de inrichtings taak in [quarantaine geplaatst](application-provisioning-quarantine-status.md) wanneer het token verloopt. Dit token moet kleiner zijn dan 1 KB.  

Laat het ons weten op [UserVoice](https://aka.ms/appprovisioningfeaturerequest)voor aanvullende verificatie-en autorisatie methoden.

### <a name="gallery-go-to-market-launch-check-list"></a>Controle lijst voor het starten van de galerie
We raden u aan om uw bestaande documentatie bij te werken en de integratie in uw marketing kanalen te verg Roten om de bewustmaking en vraag van onze gezamenlijke integratie te stimuleren.  Hieronder vindt u een aantal controle activiteiten die u het beste kunt volt ooien om de start te ondersteunen

* **Gereedheid voor verkoop-en klant ondersteuning.** Zorg ervoor dat uw verkoop-en ondersteunings teams op de hoogte zijn en dat ze kunnen praten met de integratie mogelijkheden. Geef uw verkoop-en ondersteunings team een korte beschrijving en voeg de integratie in uw verkoop materialen toe. 
* **Blog bericht en/of druk op release.** Maak een blog bericht of druk op release met een beschrijving van de gezamenlijke integratie, de voor delen en hoe u aan de slag gaat. [Voor beeld: Imprivata en Azure Active Directory pers release](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Sociale media.** Maak gebruik van uw sociale media, zoals Twitter, Facebook of LinkedIn, om de integratie van uw klanten te stimuleren. Zorg ervoor dat u @AzureAD uw post kunt retweets. [Voor beeld: Imprivata Twitter post](https://twitter.com/azuread/status/1123964502909779968)
* **Marketing website.** Maak of werk uw marketing pagina's toe (bijvoorbeeld integratie pagina, partner pagina, prijs pagina enz.) om de beschik baarheid van de gezamenlijke integratie op te halen. [Voor beeld: Pingboard-integratie pagina](https://pingboard.com/org-chart-for), [Smart Sheet-integratie pagina](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), pagina met [Monday.com prijzen](https://monday.com/pricing/) 
* **Technische documentatie.** Maak een Help Center-artikel of technische documentatie over hoe klanten aan de slag kunnen. [Voor beeld: Envoy + Microsoft Azure Active Directory-integratie.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Communicatie van de klant.** Waarschuw klanten van de nieuwe integratie via uw klant communicatie (maandelijkse nieuws brieven, e-mail campagnes, opmerkingen bij de release van het product). 

## <a name="related-articles"></a>Verwante artikelen:

* [Gebruikers inrichten en het ongedaan maken van de inrichting van SaaS-apps automatiseren](user-provisioning.md)
* [Kenmerk toewijzingen voor gebruikers inrichting aanpassen](customize-application-attributes.md)
* [Expressies schrijven voor kenmerk toewijzingen](functions-for-customizing-application-data.md)
* [Bereikfilters voor het inrichten van gebruikers](define-conditional-rules-for-provisioning-user-accounts.md)
* [Meldingen voor het inrichten van accounts](user-provisioning.md)
* [Lijst met handleidingen voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)
