---
title: Api-sleutels voor beheerders en query's maken, beheren en beveiligen
titleSuffix: Azure Cognitive Search
description: Een api-key regelt de toegang tot het serviceeindpunt. Beheerderssleutels verlenen schrijftoegang. Querysleutels kunnen worden gemaakt voor alleen-lezen toegang.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a17b8b3587077222a9ed2057927c8f16253c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794376"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Api-sleutels maken en beheren voor een Azure Cognitive Search-service

Alle aanvragen voor een zoekservice hebben een alleen-lezen api-sleutel nodig die speciaal voor uw service is gegenereerd. De api-key is het enige mechanisme voor het verifiëren van toegang tot uw zoekserviceeindpunt en moet op elk verzoek worden opgenomen. In [REST-oplossingen](search-get-started-postman.md)wordt de api-toets meestal opgegeven in een aanvraagkoptekst. In [.NET-oplossingen](search-howto-dotnet-sdk.md#core-scenarios)wordt een sleutel vaak opgegeven als configuratie-instelling en vervolgens doorgegeven als [Referenties](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (beheersleutel) of [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (querytoets) op [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

Sleutels worden gemaakt met uw zoekservice tijdens het inrichten van de service. U belangrijke waarden bekijken en verkrijgen in de [Azure-portal.](https://portal.azure.com)

![Portalpagina, sectie Instellingen, Sleutels](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Wat is een api-key

Een api-toets is een tekenreeks die bestaat uit willekeurig gegenereerde getallen en letters. Via [op rollen gebaseerde machtigingen](search-security-rbac.md)u de sleutels verwijderen of lezen, maar u een sleutel niet vervangen door een door de gebruiker gedefinieerd wachtwoord of Active Directory gebruiken als de primaire verificatiemethode voor toegang tot zoekbewerkingen. 

Er worden twee typen sleutels gebruikt om toegang te krijgen tot uw zoekservice: admin (read-write) en query (alleen-lezen).

|Sleutel|Beschrijving|Limieten|  
|---------|-----------------|------------|  
|Beheerder|Verleent volledige rechten voor alle bewerkingen, inclusief de mogelijkheid om de service te beheren, indexen, indexeerders en gegevensbronnen te maken en te verwijderen.<br /><br /> Twee beheerderssleutels, aangeduid als *primaire* en *secundaire* sleutels in de portal, worden gegenereerd wanneer de service wordt gemaakt en afzonderlijk op aanvraag kunnen worden geregenereerd. Met twee sleutels u één sleutel omrollen terwijl u de tweede sleutel gebruikt voor voortdurende toegang tot de service.<br /><br /> Beheerderssleutels worden alleen opgegeven in HTTP-aanvraagkoppen. U geen api-sleutel voor beheerders in een URL plaatsen.|Maximaal 2 per dienst|  
|Query’s uitvoeren|Verleent alleen-lezen toegang tot indexen en documenten en wordt meestal gedistribueerd naar clienttoepassingen die zoekverzoeken uitgeven.<br /><br /> Querysleutels worden op aanvraag gemaakt. U ze handmatig maken in de portal of programmatisch via de [Management REST API.](https://docs.microsoft.com/rest/api/searchmanagement/)<br /><br /> Querytoetsen kunnen worden opgegeven in een HTTP-aanvraagkop voor zoeken, suggestie of opzoekbewerking. U ook een querysleutel als parameter doorgeven aan een URL. Afhankelijk van hoe uw clienttoepassing de aanvraag formuleert, is het misschien eenvoudiger om de sleutel als queryparameter door te geven:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50 per dienst|  

 Visueel is er geen onderscheid tussen een beheersleutel of querysleutel. Beide toetsen zijn tekenreeksen die bestaan uit 32 willekeurig gegenereerde alfanumerieke tekens. Als u uit het oog verliest welk type sleutel in uw toepassing is opgegeven, u [de belangrijkste waarden in de portal controleren](https://portal.azure.com) of de [REST-API](https://docs.microsoft.com/rest/api/searchmanagement/) gebruiken om de waarde en het sleuteltype terug te geven.  

> [!NOTE]  
>  Het wordt beschouwd als een slechte beveiligingspraktijk `api-key` om gevoelige gegevens door te geven, zoals een in de aanvraag URI. Azure Cognitive Search accepteert daarom alleen een querysleutel als een `api-key` queryreeks en u moet dit voorkomen, tenzij de inhoud van uw index openbaar beschikbaar moet zijn. In het algemeen raden we `api-key` u aan uw als een aanvraagheader door te geven.  

## <a name="find-existing-keys"></a>Bestaande sleutels zoeken

U toegangssleutels verkrijgen in de portal of via de [Management REST API.](https://docs.microsoft.com/rest/api/searchmanagement/) Zie [Api-sleutels beheren en query's voor](search-security-api-keys.md)meer informatie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Vermeld de [zoekservices](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) voor uw abonnement.
3. Selecteer de service en klik op de pagina Overzicht op >**Instellingentoetsen** om beheer- en querysleutels weer te geven. **Settings**

   ![Portalpagina, sectie Instellingen, Sleutels](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Querytoetsen maken

Querysleutels worden gebruikt voor alleen-lezen toegang tot documenten in een index voor bewerkingen die gericht zijn op een verzameling van documenten. Zoek-, filter- en suggestiequery's zijn alle bewerkingen die een querysleutel uitvoeren. Voor elke alleen-lezenbewerking die systeemgegevens of objectdefinities retourneert, zoals een indexdefinitie of indexerstatus, is een beheersleutel vereist.

Het beperken van toegang en bewerkingen in client-apps is essentieel om de zoekmiddelen op uw service te beveiligen. Gebruik altijd een querysleutel in plaats van een beheersleutel voor elke query die afkomstig is van een client-app.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Vermeld de [zoekservices](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) voor uw abonnement.
3. Selecteer de service en klik op de pagina Overzicht op >**Instellingentoetsen**. **Settings**
4. Klik **op Querytoetsen beheren**.
5. Gebruik de querysleutel die al voor uw service is gegenereerd of maak maximaal 50 nieuwe querysleutels. De standaardquerysleutel wordt niet benoemd, maar er kunnen extra querysleutels worden benoemd voor beheerbaarheid.

   ![Een querysleutel maken of gebruiken](media/search-security-overview/create-query-key.png) 

> [!Note]
> Een codevoorbeeld met querysleutelgebruik is te vinden in [Query een Azure Cognitive Search-index in C#](search-query-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Beheerderssleutels regenereren

Voor elke service worden twee beheerderssleutels gemaakt, zodat u een primaire sleutel roteren met behulp van de secundaire sleutel voor bedrijfscontinuïteit.

1. Kopieer de secundaire sleutel op de pagina >**Instellingentoetsen.** **Settings**
2. Werk voor alle toepassingen de api-key-instellingen bij om de secundaire sleutel te gebruiken.
3. Regenereren van de primaire sleutel.
4. Werk alle toepassingen bij om de nieuwe primaire sleutel te gebruiken.

Als u per ongeluk beide sleutels tegelijkertijd regenereert, mislukken alle clientaanvragen met deze sleutels met HTTP 403 Forbidden. Inhoud wordt echter niet verwijderd en u wordt niet permanent buitengesloten. 

U hebt nog steeds toegang tot de service via de portal of de beheerlaag[(REST API,](https://docs.microsoft.com/rest/api/searchmanagement/) [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)of Azure Resource Manager). Beheerfuncties zijn operationeel via een abonnement-ID, geen service-api-key, en dus nog steeds beschikbaar, zelfs als uw api-sleutels dat niet zijn. 

Nadat u nieuwe sleutels hebt gemaakt via portal of beheerlaag, wordt de toegang hersteld tot uw inhoud (indexen, indexeerders, gegevensbronnen, synoniemkaarten) zodra u de nieuwe sleutels hebt en deze sleutels op verzoeken verstrekt.

## <a name="secure-api-keys"></a>Beveiligde api-sleutels
De beveiliging van de sleutel wordt gewaarborgd door de toegang via de portal- of Resource Manager-interfaces (PowerShell- of command-line-interface) te beperken. Zoals opgemerkt, kunnen abonnementsbeheerders alle api-sleutels bekijken en regenereren. Bekijk uit voorzorg roltoewijzingen om te begrijpen wie toegang heeft tot de beheerderssleutels.

+ Klik in het servicedashboard op **Toegangsbeheer (IAM)** en vervolgens op het tabblad **Toewijzingen van rollen** om roltoewijzingen voor uw service weer te geven.

Leden van de volgende rollen kunnen sleutels bekijken en regenereren: eigenaar, bijdrager, [bijdragers van de zoekservice](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Voor toegang op basis van identiteit via zoekresultaten u beveiligingsfilters maken om resultaten bij te snijden op identiteit, waarbij documenten worden verwijderd waarvoor de aanvrager geen toegang zou moeten hebben. Zie [Beveiligingsfilters](search-security-trimming-for-azure-search.md) en [Beveiligen met Active Directory](search-security-trimming-for-azure-search-with-aad.md)voor meer informatie.

## <a name="see-also"></a>Zie ook

+ [Op rollen gebaseerdtoegangsbeheer in Azure Cognitive Search](search-security-rbac.md)
+ [Beheren met PowerShell](search-manage-powershell.md) 
+ [Artikel over prestaties en optimalisatie](search-performance-optimization.md)