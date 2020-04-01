---
title: De AZURE AD Graph-API gebruiken
description: De Azure Active Directory (Azure AD) Graph API biedt programmatische toegang tot Azure AD via OData REST API-eindpunten. Toepassingen kunnen Azure AD Graph API gebruiken om crud-bewerkingen (maken, lezen, bijwerken en verwijderen) uit te voeren op directorygegevens en -objecten.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 701aadcbfe03c82f6a4c341ef9698f8a1bf1a347
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476584"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>How to: De Azure AD Graph API gebruiken

> [!IMPORTANT]
> We raden u ten zeerste aan [Microsoft Graph](https://developer.microsoft.com/graph) te gebruiken in plaats van Azure AD Graph API om toegang te krijgen tot Azure Active Directory-resources (Azure AD). We richten ons momenteel op ontwikkelingen in Microsoft Graph. Voor Azure AD Graph API zijn geen verdere verbeteringen gepland. Er zijn een zeer beperkt aantal scenario's waarvoor de Azure AD Graph API nog geschikt kan zijn; Zie voor meer informatie het [blogbericht Microsoft Graph of Azure AD Graph](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) en Migratie van Azure AD [Graph-apps naar Microsoft Graph](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview).

De Azure AD Graph API biedt programmatische toegang tot Azure AD via OData REST API-eindpunten. Toepassingen kunnen Azure AD Graph API gebruiken om crud-bewerkingen (maken, lezen, bijwerken en verwijderen) uit te voeren op directorygegevens en -objecten. U bijvoorbeeld azure AD Graph API gebruiken om een nieuwe gebruiker te maken, de eigenschappen van de gebruiker te bekijken of bij te werken, het wachtwoord van de gebruiker te wijzigen, groepslidmaatschap te controleren op toegang op rollen, de gebruiker uit te schakelen of te verwijderen. Zie [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) en Azure AD Graph [API-vereisten](https://msdn.microsoft.com/library/hh974476.aspx)voor meer informatie over azure AD Graph API-functies en toepassingsscenario's. Azure AD Graph API werkt alleen met werk- of school-/organisatieaccounts.

Dit artikel is van toepassing op de Azure AD Graph API. Zie De Microsoft Graph API gebruiken voor vergelijkbare informatie met betrekking tot De API van [Microsoft Graph.](https://developer.microsoft.com/graph/docs/concepts/use_the_api)

## <a name="how-to-construct-a-graph-api-url"></a>Een URL van een Graph-API maken

In Graph API u URL's gebruiken op basis van het OData-protocol (Open Data) om toegang te krijgen tot directorygegevens en objecten (met andere woorden resources of entiteiten) waartegen u CRUD-bewerkingen wilt uitvoeren. De URL's die worden gebruikt in de Graph API bestaan uit vier hoofdonderdelen: serviceroot, tenant-id, resourcepad en querytekenreeksopties: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Neem het voorbeeld van `https://graph.windows.net/contoso.com/groups?api-version=1.6`de volgende URL: .

* **Serviceroot:** in Azure AD Graph API https://graph.windows.netis de serviceroot altijd .
* **Tenant-id**: Deze sectie kan een geverifieerde (geregistreerde) domeinnaam zijn, in het voorgaande voorbeeld contoso.com. Het kan ook een tenant object ID of de "myorganization" of "me" alias. Zie [Entiteiten en bewerkingen aanpakken in Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)voor meer informatie.
* **Resourcepad:** in dit gedeelte van een URL wordt aangegeven met welke resource moet worden samengewerkt (gebruikers, groepen, een bepaalde gebruiker of een bepaalde groep, enz.) In het bovenstaande voorbeeld is het het hoogste niveau "groepen" om die resourceset aan te pakken. U ook een specifieke entiteit aanspreken, bijvoorbeeld users/{objectId} of users/userPrincipalName.
* **Queryparameters:** een vraagteken (?) scheidt de sectie resourcepad van de sectie queryparameters. De queryparameter 'api-versie' is vereist voor alle aanvragen in de Azure AD Graph API. Azure AD Graph API ondersteunt ook de volgende OData-queryopties: **$filter,** **$orderby,** **$expand,** **$top**en **$format**. De volgende queryopties worden momenteel niet ondersteund: **$count,** **$inlinecount**en **$skip**. Zie [Ondersteunde query's, filters en pagingopties in Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options)voor meer informatie.

## <a name="graph-api-versions"></a>Api-versies voor grafieken

U geeft de versie op voor een Graph API-aanvraag in de queryparameter 'api-versie'. Voor versie 1.5 en hoger gebruikt u een numerieke versiewaarde; api-versie=1.6. Voor eerdere versies gebruikt u een datumtekenreeks die voldoet aan het formaat YYYY-MM-DD; bijvoorbeeld api-versie=2013-11-08. Gebruik de tekenreeks 'bèta' voor voorbeeldfuncties. bijvoorbeeld api-version=beta. Zie [Azure AD Graph API-versieing](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning)voor meer informatie over verschillen tussen Graph API-versies.

## <a name="graph-api-metadata"></a>Metagegevens van de Grafiek-API

Als u het azure AD Graph API-metagegevensbestand wilt retourneren, voegt u het segment '$metadata' `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`toe na de tenant-id in de URL Bijvoorbeeld retourneert de volgende URL metagegevens voor een demobedrijf: . U deze URL invoeren in de adresbalk van een webbrowser om de metagegevens te bekijken. Het geretourneerde DOCUMENT meteenmetagegevens CSDL beschrijft de entiteiten en complexe typen, hun eigenschappen en de functies en acties die worden blootgesteld door de versie van graph-API die u hebt aangevraagd. Als u de parameter api-versie weglaat, worden metagegevens geretourneerd voor de meest recente versie.

## <a name="common-queries"></a>Veelvoorkomende query's

[Algemene query's van azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) bevatten algemene query's die kunnen worden gebruikt met de Azure AD Graph, inclusief query's die kunnen worden gebruikt om toegang te krijgen tot bronnen op het hoogste niveau in uw map en query's om bewerkingen in uw map uit te voeren.

`https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` Retourneert bijvoorbeeld bedrijfsinformatie voor directory-contoso.com.

Of `https://graph.windows.net/contoso.com/users?api-version=1.6` geeft een lijst van alle gebruikersobjecten in de contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>De Azure AD Graph Explorer gebruiken
U de Azure AD Graph Explorer voor de Azure AD Graph API gebruiken om de directorygegevens op te vragen terwijl u uw toepassing bouwt.

De volgende schermafbeelding is de uitvoer die u zou zien als u naar `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` de Azure AD Graph Explorer wilt navigeren, u wilt aanmelden en invoeren om alle gebruikers in de map van de aangemelde gebruiker weer te geven:

![Voorbeelduitvoer in Azure AD Graph API Explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**De Azure AD Graph Explorer laden:** Als [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)u het gereedschap wilt laden, navigeert u naar . Klik **op Aanmelden** en aanmelden met uw Azure AD-accountreferenties om de Azure AD Graph Explorer tegen uw tenant uit te voeren. Als u Azure AD Graph Explorer uitvoert tegen uw eigen tenant, moet u of uw beheerder toestemming geven tijdens het aanmelden. Als u een Office 365-abonnement hebt, hebt u automatisch een Azure AD-tenant. De referenties die u gebruikt om u aan te melden bij Office 365 zijn in feite Azure AD-accounts en u deze referenties gebruiken met Azure AD Graph Explorer.

**Een query uitvoeren:** Als u een query wilt uitvoeren, typt u de query in het tekstvak van de aanvraag en klikt u op **GET** of klikt u op de **enter-toets.** De resultaten worden weergegeven in het antwoordvak. Hier worden `https://graph.windows.net/myorganization/groups?api-version=1.6` bijvoorbeeld alle groepsobjecten in de map van de aangemelde gebruiker weergegeven.

Let op de volgende functies en beperkingen van de Azure AD Graph Explorer:

* Mogelijkheid automatisch aanvullen op resourcesets. Als u deze functionaliteit wilt zien, klikt u op het tekstvak van het verzoek (waar de URL van het bedrijf wordt weergegeven). U een resourceset selecteren in de vervolgkeuzelijst.
* Vraag geschiedenis aan.
* Ondersteunt de "ik" en "myorganization" aanpakken aliassen. U bijvoorbeeld `https://graph.windows.net/me?api-version=1.6` het gebruikersobject van de aangemelde `https://graph.windows.net/myorganization/users?api-version=1.6` gebruiker retourneren of alle gebruikers in de map van de aangemelde gebruiker retourneren.
* Ondersteunt volledige CRUD-bewerkingen `POST`tegen `GET` `PATCH` uw `DELETE`eigen directory met behulp van , en .
* Een sectie met antwoordkoppen. Deze sectie kan worden gebruikt om problemen op te lossen die optreden bij het uitvoeren van query's.
* Een JSON-viewer voor de respons met uitvouw- en samenvouwmogelijkheden.
* Geen ondersteuning voor het weergeven of uploaden van een miniatuurfoto.

## <a name="using-fiddler-to-write-to-the-directory"></a>Fiddler gebruiken om naar de map te schrijven

Voor de toepassing van deze Quickstart-handleiding u de Fiddler Web Debugger gebruiken om te oefenen met het uitvoeren van 'schrijfbewerkingen' in vergelijking met uw Azure AD-map. U bijvoorbeeld de profielfoto van een gebruiker krijgen en uploaden (wat niet mogelijk is met Azure AD Graph Explorer). Zie [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler)voor meer informatie en om Fiddler te installeren.

In het onderstaande voorbeeld gebruikt u Fiddler Web Debugger om een nieuwe beveiligingsgroep 'MyTestGroup' te maken in uw Azure AD-map.

**Een toegangstoken verkrijgen:** om toegang te krijgen tot Azure AD Graph, moeten clients eerst met succes verifiëren naar Azure AD. Zie [Verificatiescenario's voor Azure AD voor](authentication-scenarios.md)meer informatie.

**Een query opstellen en uitvoeren:** Voer de volgende stappen uit:

1. Open Fiddler Web Debugger en ga naar het tabblad **Componist.**
2. Omdat u een nieuwe beveiligingsgroep wilt maken, selecteert u **Post** als http-methode in het keuzemenu. Zie [Groeperen](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) binnen de [API-verwijzing naar Azure AD Graph REST](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Typ in het veld naast **Bericht**de `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`volgende aanvraag-URL: .
   
   > [!NOTE]
   > U moet {mytenantdomain} vervangen door de domeinnaam van uw eigen Azure AD-map.

4. Typ in het veld direct onder Post pull-down de volgende HTTP-header:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Vervang &lt;uw toegangstoken&gt; door het toegangstoken voor uw Azure AD-map.

5. Typ in het veld **Hoofdtekst aanvragen** de volgende JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Zie Groep maken voor meer informatie over het maken van [groepen.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup)

Zie azure [AD Graph-API-referentie](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)voor meer informatie over au-media en -typen die worden weergegeven door Graph en informatie over de bewerkingen die op deze entiteiten kunnen worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Meer informatie over [azure AD Graph API-machtigingsscopes](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
