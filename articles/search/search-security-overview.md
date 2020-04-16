---
title: Beveiliging en gegevensprivacy
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search is compatibel met SOC 2, HIPAA en andere certificeringen. Verbindings- en gegevensversleuteling, verificatie en identiteitstoegang via gebruikers- en groepsbeveiligings-id's in filterexpressies.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: fe7d076fab6a70736843fc644cd56bef44a55df2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415127"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Beveiliging en gegevensprivacy in Azure Cognitive Search

Uitgebreide beveiligingsfuncties en toegangscontroles zijn ingebouwd in Azure Cognitive Search om ervoor te zorgen dat privé-inhoud op die manier blijft. In dit artikel worden de beveiligingsfuncties en standaarden opgesomd die zijn ingebouwd in Azure Cognitive Search.

Azure Cognitive Search-beveiligingsarchitectuur omvat fysieke beveiliging, versleutelde transmissies, versleutelde opslag en naleving van standaarden voor het hele platform. Operationeel accepteert Azure Cognitive Search alleen geverifieerde aanvragen. Optioneel u toegangsbesturingselementen per gebruiker toevoegen aan inhoud via beveiligingsfilters. Dit artikel gaat over beveiliging op elke laag, maar is vooral gericht op hoe gegevens en bewerkingen worden beveiligd in Azure Cognitive Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Naleving van de normen: ISO 27001, SOC 2, HIPAA

Azure Cognitive Search is gecertificeerd voor de volgende standaarden, zoals [aangekondigd in juni 2018:](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/)

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 Type 2 compliance](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Ga voor het volledige rapport naar [Azure - en Azure Government SOC 2 Type II Report](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Deel 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST (HITRUST)](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS-niveau 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)

Naleving van de normen is van toepassing op algemeen beschikbare functies. Preview-functies zijn gecertificeerd wanneer ze overgaan naar algemene beschikbaarheid en mogen niet worden gebruikt in oplossingen met strenge normen. Nalevingscertificering is gedocumenteerd in [overzicht van microsoft Azure-compliance](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) en het [Vertrouwenscentrum.](https://www.microsoft.com/en-us/trustcenter) 

## <a name="encrypted-transmission-and-storage"></a>Versleutelde transmissie en opslag

Versleuteling strekt zich uit over de gehele indexeringspijplijn: van verbindingen, via transmissie en naar geïndexeerde gegevens die zijn opgeslagen in Azure Cognitive Search.

| Beveiligingslaag | Beschrijving |
|----------------|-------------|
| Versleuteling 'in transit' <br>(HTTPS/SSL/TLS) | Azure Cognitive Search luistert op HTTPS-poort 443. Op het platform worden verbindingen met Azure-services versleuteld. <br/><br/>Alle client-to-service Azure Cognitive Search-interacties zijn geschikt voor SSL/TLS 1.2.  Zorg ervoor dat u TLSv1.2 gebruikt voor SSL-verbindingen met uw service.|
| Versleuteling 'at rest' <br>Door Microsoft beheerde sleutels | Versleuteling wordt volledig geïnternaliseerd in het indexeringsproces, zonder meetbare impact op het indexeren van tijd tot voltooiing of indexgrootte. Het gebeurt automatisch op alle indexering, ook op incrementele updates van een index die niet volledig is versleuteld (gemaakt vóór januari 2018).<br><br>Intern is versleuteling gebaseerd op [Azure Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), met behulp van 256-bits [AES-versleuteling.](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)<br><br> Versleuteling is intern in Azure Cognitive Search, met certificaten en versleutelingssleutels die intern door Microsoft worden beheerd en universeel worden toegepast. U versleuteling niet in- of uitschakelen, uw eigen sleutels beheren of vervangen, of versleutelingsinstellingen bekijken in de portal of programmatisch.<br><br>Encryptie in rust werd aangekondigd in januari 24, 2018 en is van toepassing op alle servicelagen, inclusief de gratis laag, in alle regio's. Voor volledige versleuteling moeten indexen die vóór die datum zijn gemaakt, worden verwijderd en opnieuw worden opgebouwd om versleuteling te kunnen maken. Anders worden alleen nieuwe gegevens die na 24 januari zijn toegevoegd, versleuteld.|
| Versleuteling 'at rest' <br>Door klant beheerde sleutels | Versleuteling met door klanten beheerde sleutels is nu algemeen beschikbaar voor zoekservices die op of na januari 2019 zijn gemaakt. Het wordt niet ondersteund op gratis (gedeelde) diensten.<br><br>Azure Cognitive Search-indexen en synoniemenkaarten kunnen nu in rust worden versleuteld met door de klant beheerde sleutels in Azure Key Vault. Zie [Versleutelingssleutels beheren in Azure Cognitive Search](search-security-manage-encryption-keys.md)voor meer informatie.<br><br>Deze functie vervangt de standaardversleuteling niet in rust, maar wordt er naast toegepast.<br><br>Als u deze functie inschakelt, wordt de indexgrootte vergroot en de queryprestaties verslechteren. Op basis van waarnemingen tot nu toe u een toename van 30%-60% in querytijden verwachten, hoewel de werkelijke prestaties variëren afhankelijk van de indexdefinitie en typen query's. Vanwege deze impact op de prestaties raden we u aan deze functie alleen in te schakelen op indexen die dit echt nodig hebben.

## <a name="azure-wide-user-access-controls"></a>Besturingselementen voor gebruikerstoegang in Azure

Verschillende beveiligingsmechanismen zijn beschikbaar in Azure-breed en zijn dus automatisch beschikbaar voor de Azure Cognitive Search-resources die u maakt.

+ [Vergrendelt op abonnements- of resourceniveau om verwijdering te voorkomen](../azure-resource-manager/management/lock-resources.md)
+ [Op rollen gebaseerd toegangscontrole (RBAC) om de toegang tot informatie en administratieve bewerkingen te beheren](../role-based-access-control/overview.md)

Alle Azure-services ondersteunen op rollen gebaseerde toegangscontroles (RBAC) voor het consistent instellen van toegangsniveaus voor alle services. Het bekijken van gevoelige gegevens, zoals de beheersleutel, is bijvoorbeeld beperkt tot de rollen Eigenaar en Inzender. De status van de weergaveservice is echter beschikbaar voor leden van elke rol. RBAC biedt rollen voor eigenaar, bijdrager en lezer. Standaard zijn alle servicebeheerders lid van de rol Eigenaar.

<a name="service-access-and-authentication"></a>

## <a name="endpoint-access"></a>Toegang tot eindpunt

### <a name="public-access"></a>Toegang tot het publiek

Azure Cognitive Search erft de beveiligingsbeveiligingen van het Azure-platform en biedt zijn eigen sleutelverificatie. Een api-toets is een tekenreeks die bestaat uit willekeurig gegenereerde getallen en letters. Het type sleutel (beheerder of query) bepaalt het toegangsniveau. Het indienen van een geldige sleutel wordt beschouwd als het bewijs dat het verzoek afkomstig is van een vertrouwde entiteit. 

Er zijn twee niveaus van toegang tot uw zoekservice, ingeschakeld door twee soorten sleutels:

* Beheerderstoegang (geldig voor elke leesbewerking tegen de service)
* Querytoegang (geldig voor alleen-lezen bewerkingen, zoals query's, tegen het verzamelen van documenten van een index)

*Beheerderssleutels* worden gemaakt wanneer de service is ingericht. Er zijn twee beheerderssleutels, aangewezen als *primair* en *secundair* om ze recht te houden, maar in feite zijn ze uitwisselbaar. Elke service heeft twee beheerderssleutels, zodat u er een omdraaien zonder de toegang tot uw service te verliezen. U [de beheersleutel](search-security-api-keys.md#regenerate-admin-keys) periodiek regenereren volgens de aanbevolen procedures voor Azure-beveiliging, maar u niet toevoegen aan het totale aantal beheerderssleutels. Er zijn maximaal twee beheerderssleutels per zoekservice.

*Querysleutels* worden naar behoefte gemaakt en zijn ontworpen voor clienttoepassingen die query's uitvoeren. U maximaal 50 querysleutels maken. In toepassingscode geeft u de zoek-URL en een query-api-toets op om alleen-lezen toegang te geven tot het verzamelen van documenten van een specifieke index. Samen bepalen het eindpunt, een api-toets voor alleen-lezen toegang en een doelindex het bereik en het toegangsniveau van de verbinding vanuit uw clienttoepassing.

Verificatie is vereist op elk verzoek, waarbij elke aanvraag bestaat uit een verplichte sleutel, een bewerking en een object. Wanneer de twee machtigingsniveaus (volledig of alleen-lezen) en de context (bijvoorbeeld een querybewerking op een index) aan elkaar worden geketend, zijn voldoende om volledige spectrumbeveiliging op servicebewerkingen te bieden. Zie [Api-sleutels maken en beheren](search-security-api-keys.md)voor meer informatie over sleutels.

### <a name="restricted-access"></a>Beperkte toegang

Wanneer u een openbare dienst hebt en u het gebruik van de service wilt beperken, u de IP-beperkingsregel gebruiken in de Api-versie van Beheer REST: 2020-03-13, [IpRule](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service#IpRule). Met IpRule u de toegang tot uw service beperken door IP-adressen te identificeren, individueel of in een bereik, die u toegang wilt verlenen tot uw zoekservice. 

### <a name="private-access"></a>Privétoegang

[Met privéeindpunten](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) voor Azure Cognitive Search kan een client in een virtueel netwerk veilig toegang krijgen tot gegevens in een zoekindex via een [privékoppeling.](https://docs.microsoft.com/azure/private-link/private-link-overview) Het privéeindpunt gebruikt een IP-adres van de virtuele netwerkadresruimte voor uw zoekservice. Netwerkverkeer tussen de client en de zoekservice loopt via het virtuele netwerk en een privékoppeling op het Microsoft-backbonenetwerk, waardoor blootstelling van het openbare internet wordt geëlimineerd.

[Azure Virtual Network (VNet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) zorgt voor veilige communicatie tussen bronnen, met uw on-premises netwerk en het internet. 

## <a name="index-access"></a>Indextoegang

In Azure Cognitive Search is een individuele index geen securable object. In plaats daarvan wordt de toegang tot een index bepaald op de servicelaag (lees- of schrijftoegang), samen met de context van een bewerking.

Voor toegang tot eindgebruikers u queryaanvragen structureren om verbinding te maken met een querysleutel, die elke alleen-aanvraag-alleen-mogelijk maakt, en de specifieke index opnemen die door uw app wordt gebruikt. In een queryaanvraag is er geen concept van het samenvoegen van indexen of het gelijktijdig openen van meerdere indexen, zodat alle aanvragen per definitie op één index zijn gericht. Als zodanig definieert de constructie van de queryaanvraag zelf (een sleutel plus een enkele doelindex) de beveiligingsgrens.

De toegang van beheerders en ontwikkelaars tot indexen is ongedifferentieerd: beide hebben schrijftoegang nodig om objecten te maken, te verwijderen en bij te werken die door de service worden beheerd. Iedereen met een beheerderssleutel voor uw service kan een index in dezelfde service lezen, wijzigen of verwijderen. Voor bescherming tegen onbedoelde of kwaadwillige verwijdering van indexen, uw in-house broncontrole voor code activa is de remedie voor het omkeren van een ongewenste index verwijderen of wijzigen. Azure Cognitive Search heeft failover binnen het cluster om beschikbaarheid te garanderen, maar het slaat of voert uw eigen code niet op die wordt gebruikt om indexen te maken of te laden.

Voor multitenancy-oplossingen waarvoor beveiligingsgrenzen op indexniveau nodig zijn, omvatten dergelijke oplossingen doorgaans een middelste laag, die klanten gebruiken om indexisolatie te verwerken. Zie [Ontwerppatronen voor multitenant SaaS-toepassingen en Azure Cognitive Search voor](search-modeling-multitenant-saas-applications.md)meer informatie over de use case voor multitenant.

## <a name="authentication"></a>Verificatie

### <a name="admin-access"></a>Beheerderstoegang

[Op rollen gebaseerde toegang (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) bepaalt of u toegang hebt tot de controle over de service en de inhoud ervan. Als u eigenaar of bijdrager bent op een Azure Cognitive Search-service, u de portal of de PowerShell **Az.Search-module** gebruiken om objecten in de service te maken, bij te werken of te verwijderen. U ook de [Azure Cognitive Search Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)gebruiken.

### <a name="user-access"></a>Gebruikerstoegang

Standaard wordt de toegang van de gebruiker tot een index bepaald door de toegangssleutel op de queryaanvraag. De meeste ontwikkelaars maken en toewijzen [*querysleutels*](search-security-api-keys.md) voor zoekaanvragen aan clientzijde. Een querysleutel geeft leestoegang tot alle inhoud binnen de index.

Als u gedetailleerde, per gebruikercontrole over inhoud nodig hebt, u beveiligingsfilters voor uw query's bouwen en documenten retourneren die zijn gekoppeld aan een bepaalde beveiligingsidentiteit. In plaats van vooraf gedefinieerde rollen en roltoewijzingen wordt identiteitsgebaseerdtoegangsbeheer geïmplementeerd als een *filter* waarmee zoekresultaten van documenten en inhoud op basis van identiteiten worden bijgehouden. In de volgende tabel worden twee benaderingen beschreven voor het bijsnijden van zoekresultaten van ongeautoriseerde inhoud.

| Methode | Beschrijving |
|----------|-------------|
|[Beveiligingsbijsnijden op basis van identiteitsfilters](search-security-trimming-for-azure-search.md)  | Documenteert de basiswerkstroom voor het implementeren van toegangsbeheer voor gebruikersidentiteit. Het omvat het toevoegen van beveiligings-id's aan een index en legt vervolgens het filteren tegen dat veld uit om resultaten van verboden inhoud bij te snijden. |
|[Beveiliging bijsnijden op basis van Azure Active Directory-identiteiten](search-security-trimming-for-azure-search-with-aad.md)  | Dit artikel wordt uitgebreid naar het vorige artikel en biedt stappen voor het ophalen van identiteiten uit Azure Active Directory (AAD), een van de [gratis services](https://azure.microsoft.com/free/) in het Azure-cloudplatform. |

## <a name="table-permissioned-operations"></a>Tabel: Bewerkingen met machtigingen

In de volgende tabel worden de bewerkingen samengevat die zijn toegestaan in Azure Cognitive Search en welke sleutel toegang tot een bepaalde bewerking ontgrendelt.

| Bewerking | Machtigingen |
|-----------|-------------------------|
| Een service maken | Azure-abonnementshouder|
| Een service schalen | Beheersleutel, RBAC-eigenaar of bijdrager op de resource  |
| Een service verwijderen | Beheersleutel, RBAC-eigenaar of bijdrager op de resource |
| Objecten in de service maken, wijzigen, verwijderen: <br>Indexen en onderdelen (inclusief analyselijsten, scoreprofielen, CORS-opties), indexers, gegevensbronnen, synoniemen, suggesties. | Beheersleutel, RBAC-eigenaar of bijdrager op de resource  |
| Een index opvragen | Beheerder of querysleutel (RBAC niet van toepassing) |
| Informatie over querysystemen, zoals het retourneren van statistieken, tellingen en lijsten met objecten. | Beheersleutel, RBAC op de bron (eigenaar, bijdrager, lezer) |
| Beheerderssleutels beheren | Beheersleutel, RBAC-eigenaar of bijdrager op de resource. |
| Querysleutels beheren |  Beheersleutel, RBAC-eigenaar of bijdrager op de resource.  |

## <a name="physical-security"></a>Fysieke beveiliging

Microsoft-datacenters bieden toonaangevende fysieke beveiliging en voldoen aan een uitgebreid portfolio van standaarden en voorschriften. Ga voor meer informatie naar de pagina [Globale datacenters](https://www.microsoft.com/cloud-platform/global-datacenters) of bekijk een korte video over de beveiliging van datacenters.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Zie ook

+ [Aan de slag .NET (toont aan met behulp van een beheersleutel om een index te maken)](search-create-index-dotnet.md)
+ [Rest aan de slag (demonstreert met behulp van een beheersleutel om een index te maken)](search-create-index-rest-api.md)
+ [Op identiteit gebaseerdtoegangsbeheer met Azure Cognitive Search-filters](search-security-trimming-for-azure-search.md)
+ [Active Directory-toegangsbeheer op basis van identiteit met Azure Cognitive Search-filters](search-security-trimming-for-azure-search-with-aad.md)
+ [Filters in Azure Cognitive Search](search-filters.md)