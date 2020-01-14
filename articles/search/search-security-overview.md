---
title: Beveiliging en privacy van gegevens
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search voldoet aan SOC 2, HIPAA en andere certificeringen. Verbinding en gegevens versleuteling, authenticatie en identiteits toegang via beveiligings-id's van gebruikers en groepen in filter expressies.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1949aca26f68f12dfb133da8ef45662294140c25
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922563"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Beveiliging en privacy van gegevens in azure Cognitive Search

Uitgebreide beveiligings functies en toegangs beheer zijn ingebouwd in azure Cognitive Search om ervoor te zorgen dat persoonlijke inhoud op die manier blijft. In dit artikel worden de beveiligings functies en-standaarden opgesomd die zijn ingebouwd in azure Cognitive Search.

De beveiligings architectuur van Azure Cognitive Search omvat fysieke beveiliging, versleutelde verzen dingen, versleutelde opslag en naleving van de vereisten voor het hele platform. Als Azure Cognitive Search worden alleen geverifieerde aanvragen geaccepteerd. U kunt desgewenst toegangs beheer per gebruiker aan inhoud toevoegen via beveiligings filters. Dit artikel is van invloed op de beveiliging van elke laag, maar is voornamelijk gericht op hoe gegevens en bewerkingen worden beveiligd in azure Cognitive Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Naleving van standaarden: ISO 27001, SOC 2, HIPAA

Azure Cognitive Search is gecertificeerd voor de volgende standaarden, zoals [aangekondigd in juni 2018](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/):

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Naleving van SOC 2 type 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Ga voor het volledige rapport naar [Azure-en Azure Government SOC 2-rapport](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Health Insurance-en verantwoordings Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Part 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS niveau 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Australië IRAP unclassificeerd DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

Naleving van standaarden is van toepassing op algemeen beschik bare functies. Preview-functies zijn gecertificeerd wanneer ze overstappen op algemene Beschik baarheid en mogen niet worden gebruikt in oplossingen met strikte normen vereisten. Nalevings certificering wordt beschreven in [overzicht van Microsoft Azure naleving](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) en het [vertrouwens centrum](https://www.microsoft.com/en-us/trustcenter). 

## <a name="encrypted-transmission-and-storage"></a>Versleutelde overdracht en opslag

Versleuteling loopt over de gehele indexerings pijplijn: vanuit verbindingen, via verzen ding en omlaag naar geïndexeerde gegevens die zijn opgeslagen in azure Cognitive Search.

| Beveiligingslaag | Beschrijving |
|----------------|-------------|
| Versleuteling 'in transit' <br>(HTTPS/SSL/TLS) | Azure Cognitive Search luistert naar HTTPS-poort 443. Op het platform worden verbindingen met Azure-Services versleuteld. <br/><br/>Alle interacties van client-naar-service Azure Cognitive Search zijn SSL/TLS 1,2-compatibel.  Zorg ervoor dat u TLSv 1.2 gebruikt voor SSL-verbindingen met uw service.|
| Versleuteling 'at rest' <br>Door micro soft beheerde sleutels | Versleuteling is volledig intern in het indexerings proces, met geen meetbaar effect op het indexeren van tijd tot voltooiing of index grootte. Deze fout treedt automatisch op bij alle indexeringen, met inbegrip van incrementele updates van een index die niet volledig is versleuteld (gemaakt vóór 2018 januari).<br><br>Intern is versleuteling gebaseerd op [Azure Storage-service versleuteling](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), met behulp van 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard).<br><br> Versleuteling is intern voor Azure Cognitive Search, met certificaten en versleutelings sleutels die intern door micro soft worden beheerd en universeel worden toegepast. U kunt versleuteling niet in-of uitschakelen, uw eigen sleutels beheren of vervangen, of versleutelings instellingen weer geven in de portal of via een programma.<br><br>Versleuteling op rest werd aangekondigd in 24 januari 2018 en is van toepassing op alle service lagen, inclusief de gratis laag, in alle regio's. Voor volledige versleuteling moet de indexen die vóór die datum zijn gemaakt, worden verwijderd en opnieuw worden opgebouwd om versleuteling te laten plaatsvinden. Anders worden alleen nieuwe gegevens die na 24 januari worden toegevoegd, versleuteld.|
| Versleuteling 'at rest' <br>Door klant beheerde sleutels | Versleuteling met door de klant beheerde sleutels is nu algemeen beschikbaar.<br><br>Azure Cognitive Search-indexen en synoniemen kaarten kunnen nu in rust worden versleuteld met sleutels die door de klant sleutels worden beheerd in Azure Key Vault. Zie [versleutelings sleutels beheren in Azure Cognitive Search](search-security-manage-encryption-keys.md)voor meer informatie.<br>Deze functie vervangt niet de standaard versleuteling op rest, maar er wordt een aanvulling op toegepast.<br>Als u deze functie inschakelt, wordt de index grootte verhoogd en wordt de query prestaties verkleind. Op basis van waarnemingen tot heden kunt u verwachten dat er in query tijden een toename van 30%-60% wordt weer geven, hoewel de werkelijke prestaties afhankelijk zijn van de index definitie en typen query's. Als gevolg van deze invloed van prestaties raden we u aan deze functie alleen in te scha kelen voor indexen die echt nodig zijn.

## <a name="azure-wide-user-access-controls"></a>Gebruikers toegangs beheer voor Azure-brede

Verschillende beveiligings mechanismen zijn beschikbaar voor het hele Azure en dus automatisch beschikbaar voor de Azure Cognitive Search-resources die u maakt.

+ [Vergrendelt op het niveau van de abonnement of het resource om verwijdering te voor komen](../azure-resource-manager/resource-group-lock-resources.md)
+ [Op rollen gebaseerd Access Control (RBAC) voor het beheren van de toegang tot informatie en beheer bewerkingen](../role-based-access-control/overview.md)

Alle Azure-Services bieden ondersteuning voor op rollen gebaseerde toegangs beheer (RBAC) voor het instellen van toegangs niveaus op consistente wijze voor alle services. Bijvoorbeeld: het weer geven van gevoelige gegevens, zoals de beheerders sleutel, is beperkt tot de rollen eigenaar en Inzender, terwijl de weer gave van de status van de service beschikbaar is voor leden van een wille keurige rol. RBAC biedt de rollen eigenaar, bijdrager en lezer. Standaard zijn alle service beheerders lid van de rol eigenaar.

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>Service toegang en-verificatie

Terwijl Azure Cognitive Search de beveiligings maatregelen van het Azure-platform overneemt, biedt het ook een eigen op sleutel gebaseerde verificatie. Een API-sleutel is een teken reeks die bestaat uit wille keurig gegenereerde cijfers en letters. Het type sleutel (beheerder of query) bepaalt het toegangs niveau. Het verzenden van een geldige sleutel wordt beschouwd als een bewijs dat de aanvraag afkomstig is van een vertrouwde entiteit. 

Er zijn twee toegangs niveaus voor uw zoek service, ingeschakeld door twee soorten sleutels:

* Beheerders toegang (geldig voor een lees-en schrijf bewerking voor de service)
* Query toegang (geldig voor alleen-lezen bewerkingen, zoals query's, op basis van de documenten verzameling van een index)

*Beheerders sleutels* worden gemaakt wanneer de service wordt ingericht. Er zijn twee beheer sleutels, aangeduid als *primair* en *secundair* , om ze recht te laten, maar in feite zijn ze uitwisselbaar. Elke service heeft twee beheer sleutels, zodat u één kunt implementeren zonder de toegang tot uw service te verliezen. U kunt de [beheer sleutel periodiek opnieuw genereren](search-security-api-keys.md#regenerate-admin-keys) volgens de aanbevolen procedures voor Azure-beveiliging, maar u kunt niet toevoegen aan het totale aantal beheerders sleutels. Er zijn Maxi maal twee beheer sleutels per zoek service.

*Query sleutels* worden als vereist gemaakt en zijn ontworpen voor client toepassingen die query's uitvoeren. U kunt Maxi maal 50 query sleutels maken. In toepassings code geeft u de zoek-URL en een query-API-sleutel op om alleen-lezen toegang tot de documenten verzameling van een specifieke index toe te staan. Samen, het eind punt, een API-sleutel voor alleen-lezen toegang en een doel index bepalen het bereik en toegangs niveau van de verbinding van uw client toepassing.

Verificatie is vereist voor elke aanvraag, waarbij elke aanvraag bestaat uit een verplichte sleutel, een bewerking en een object. Wanneer u samenvoegt, zijn de twee machtigings niveaus (volledig of alleen-lezen) plus de context (bijvoorbeeld een query bewerking in een index) voldoende voor het bieden van beveiliging op volledige spectrum voor service bewerkingen. Zie [API-sleutels maken en beheren](search-security-api-keys.md)voor meer informatie over sleutels.

## <a name="index-access"></a>Index toegang

In azure Cognitive Search is een afzonderlijke index geen beveiligbaar object. In plaats daarvan wordt de toegang tot een index bepaald op basis van de service laag (lees-of schrijf toegang), samen met de context van een bewerking.

Voor toegang door eind gebruikers kunt u query aanvragen structureren om verbinding te maken met behulp van een query sleutel, waardoor elke aanvraag alleen-lezen is, en de specifieke index die door uw app wordt gebruikt. In een query aanvraag is er geen idee van het samen voegen van indexen of het tegelijkertijd openen van meerdere indexen, zodat alle aanvragen een enkele index per definitie doel hebben. Als zodanig definieert de constructie van de query aanvraag zelf (een sleutel plus een enkele doel index) de beveiligings grens.

De beheerder en ontwikkelaars toegang tot indexen zijn niet-gedifferentieerd: beide moeten schrijf toegang hebben voor het maken, verwijderen en bijwerken van objecten die worden beheerd door de service. Iedereen met een beheerders sleutel voor uw service kan elke index in dezelfde service lezen, wijzigen of verwijderen. Voor beveiliging tegen onbedoelde of schadelijke verwijdering van indexen is uw interne broncode beheer voor code assets de oplossing voor het terugdraaien van een ongewenste index verwijderen of wijzigen. Azure Cognitive Search heeft een failover in het cluster om Beschik baarheid te garanderen, maar de code van uw eigen programma wordt niet opgeslagen of uitgevoerd voor het maken of laden van indexen.

Voor multitenancy-oplossingen die beveiligings grenzen vereisen op index niveau, bevatten dergelijke oplossingen doorgaans een middelste laag, die klanten gebruiken voor het verwerken van index isolatie. Zie [ontwerp patronen voor SaaS-toepassingen met meerdere tenants en Azure Cognitive Search](search-modeling-multitenant-saas-applications.md)voor meer informatie over de multi tenant-use-case.

## <a name="admin-access"></a>Beheerders toegang

[Op rollen gebaseerde toegang (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) bepaalt of u toegang hebt tot de besturings elementen voor de service en de inhoud ervan. Als u een eigenaar of bijdrager bent over een Azure Cognitive Search-service, kunt u de portal of de Power shell **AZ. Search** -module gebruiken om objecten in de service te maken, bij te werken of te verwijderen. U kunt ook de [Azure Cognitive Search Management-rest API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)gebruiken.

## <a name="user-access"></a>Gebruikerstoegang

Gebruikers toegang tot een index wordt standaard bepaald door de toegangs sleutel voor de query aanvraag. De meeste ontwikkel aars maken en toewijzen [*query sleutels*](search-security-api-keys.md) voor Zoek opdrachten aan client zijde. Een query sleutel verleent lees toegang tot alle inhoud in de index.

Als u nauw keuriger controle per gebruiker over inhoud nodig hebt, kunt u beveiligings filters voor uw query's maken en documenten retour neren die zijn gekoppeld aan een bepaalde beveiligings identiteit. In plaats van vooraf gedefinieerde rollen en roltoewijzingen, wordt toegangs beheer op basis van een id geïmplementeerd als een *filter* waarmee Zoek resultaten van documenten en inhoud worden verkleind op basis van identiteiten. In de volgende tabel worden twee benaderingen beschreven waarmee Zoek resultaten van niet-geautoriseerde inhoud worden bijgesneden.

| Methode | Beschrijving |
|----------|-------------|
|[Beveiligings beperking op basis van identiteits filters](search-security-trimming-for-azure-search.md)  | Documenteert de basis werk stroom voor het implementeren van toegangs beheer voor gebruikers identiteit. Het onderwerp bevat het toevoegen van beveiligings-id's aan een index en legt vervolgens een overzicht van de filtering uit voor dat veld om de resultaten van verboden inhoud te kunnen knippen. |
|[Beveiligings beperking op basis van Azure Active Directory-identiteiten](search-security-trimming-for-azure-search-with-aad.md)  | In dit artikel wordt het vorige artikel uitgebreid met stappen voor het ophalen van identiteiten van Azure Active Directory (AAD), een van de [gratis services](https://azure.microsoft.com/free/) in het Azure-Cloud platform. |

## <a name="table-permissioned-operations"></a>Tabel: machtigings bewerkingen

De volgende tabel bevat een overzicht van de bewerkingen die zijn toegestaan in azure Cognitive Search en met welke sleutel de toegang tot een bepaalde bewerking wordt ontgrendeld.

| Bewerking | Machtigingen |
|-----------|-------------------------|
| Een service maken | Eigenaar van het Azure-abonnement|
| Een service schalen | Beheerder sleutel, RBAC-eigenaar of Inzender voor de resource  |
| Een service verwijderen | Beheerder sleutel, RBAC-eigenaar of Inzender voor de resource |
| Objecten op de service maken, wijzigen, verwijderen: <br>Indexen en onderdeel onderdelen (inclusief analyse definities, Score profielen, CORS-opties), Indexeer functies, gegevens bronnen, synoniemen, Voorst Ellen. | Beheerder sleutel, RBAC-eigenaar of Inzender voor de resource  |
| Een query uitvoeren op een index | Beheerder of query sleutel (RBAC niet van toepassing) |
| Systeem gegevens opvragen, zoals het retour neren van statistieken, aantallen en lijsten met objecten. | Beheerder sleutel, RBAC op de bron (eigenaar, bijdrager, lezer) |
| Beheer sleutels beheren | De beheerder sleutel, de RBAC-eigenaar of de Inzender voor de resource. |
| Query sleutels beheren |  De beheerder sleutel, de RBAC-eigenaar of de Inzender voor de resource.  |

## <a name="physical-security"></a>Fysieke beveiliging

Micro soft data centers bieden toonaangevende fysieke beveiliging en voldoen aan een uitgebreide Port Folio van standaarden en voor Schriften. Ga voor meer informatie naar de pagina met [globale data](https://www.microsoft.com/cloud-platform/global-datacenters) Centers of Bekijk een korte video over de beveiliging van uw Data Center.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Zie ook

+ [Aan de slag met .NET (gedemonstreerd met behulp van een beheerders sleutel voor het maken van een index)](search-create-index-dotnet.md)
+ [Aan de slag-REST (gedemonstreerd met behulp van een beheerders sleutel voor het maken van een index)](search-create-index-rest-api.md)
+ [Toegangs beheer op basis van identiteiten met behulp van Azure Cognitive Search filters](search-security-trimming-for-azure-search.md)
+ [Active Directory toegangs beheer op basis van een id met behulp van Azure Cognitive Search filters](search-security-trimming-for-azure-search-with-aad.md)
+ [Filters in azure Cognitive Search](search-filters.md)