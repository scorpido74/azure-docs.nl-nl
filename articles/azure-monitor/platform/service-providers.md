---
title: Azure Monitor logboeken voor service providers | Microsoft Docs
description: Met Azure Monitor Logboeken kunt u Msp's (Managed Service Providers), grote ondernemingen, Isv's (Independent Software Vendors) en hosting service providers servers beheren en bewaken in de on-premises of Cloud infrastructuur van de klant.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658877"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Azure Monitor logboeken voor service providers

Log Analytics-werk ruimten in Azure Monitor kunnen Msp's (Managed Service Providers), grote ondernemingen, onafhankelijke software leveranciers (Isv's) en hosting serviceproviders servers in de on-premises of Cloud infrastructuur van de klant beheren en bewaken.

Grote ondernemingen delen veel overeenkomsten met serviceproviders, met name wanneer er een centrale IT-team dat verantwoordelijk is is voor het beheer van IT voor veel verschillende bedrijfseenheden. Voor het gemak gebruikt dit document de term *service provider* , maar dezelfde functionaliteit is ook beschikbaar voor ondernemingen en andere klanten.

Voor partners en service providers die deel uitmaken van het programma [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , is Log Analytics in azure monitor een van de Azure-Services die beschikbaar zijn in azure CSP-abonnementen.

Log Analytics in Azure Monitor kunnen ook worden gebruikt door een service provider die klant resources beheert via de Azure-beheer functie voor gedelegeerde resources in [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="architectures-for-service-providers"></a>Architecturen voor serviceproviders

Log Analytics-werk ruimten bieden een methode voor de beheerder om de stroom en isolatie van [logboek](data-platform-logs.md) gegevens te beheren en een architectuur te maken voor de specifieke zakelijke behoeften. In [dit artikel](design-logs-deployment.md) worden de aandachtspunten voor het ontwerp, de implementatie en de migratie van een werk ruimte beschreven en het artikel [toegangs beheer](manage-access.md) bevat informatie over het Toep assen en beheren van machtigingen voor logboek gegevens. Serviceproviders zijn aanvullende overwegingen.

Er zijn drie mogelijke architecturen voor serviceproviders met betrekking tot de Log Analytics-werkruimten:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. gedistribueerde logboeken worden opgeslagen in werk ruimten die zich bevinden in de Tenant van de klant

Een werkruimte wordt in deze architectuur wordt geïmplementeerd in de tenant van de klant die wordt gebruikt voor de logboeken van de betreffende klant.

Er zijn twee manieren om toegang te krijgen tot een Log Analytics werkruimte in een Tenant van de klant:

- Een klant kan afzonderlijke gebruikers van de service provider toevoegen als [Azure Active Directory gast gebruikers (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). De beheerder van de service provider moet zich aanmelden bij de directory van elke klant in het Azure Portal om toegang te kunnen krijgen tot deze werk ruimten. Dit vereist ook dat de klanten afzonderlijke toegang beheren voor elke beheerder van de service provider.
- Voor een grotere schaal baarheid en flexibiliteit kunnen service providers de Azure-functie voor [gedelegeerde resource beheer](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) van [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) gebruiken om toegang te krijgen tot de Tenant van de klant. Met deze methode worden de service provider beheerders opgenomen in een Azure AD-gebruikers groep in de Tenant van de service provider en krijgt deze groep toegang tijdens het voorbereidings proces voor elke klant. Deze beheerders hebben vervolgens vanaf hun eigen Tenant van de service provider toegang tot de werk ruimten van elke klant, in plaats van zich afzonderlijk aan te melden bij de Tenant van elke klant. Door toegang te krijgen tot de resources van de Log Analytics-werk ruimte van uw klanten op deze manier vermindert u het werk dat aan de klant is vereist en kunt u gemakkelijker gegevens verzamelen en analyseren voor meerdere klanten die worden beheerd door dezelfde service provider via hulpprogram ma's als [Azure monitor werkmappen](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview). Zie voor meer informatie [klant resources op schaal bewaken](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale).

De voor delen van de gedistribueerde architectuur zijn:

* De klant kan specifieke machtigings niveaus bevestigen via [Azure delegated resource management](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management), of u kunt de toegang tot de logboeken beheren met behulp van hun eigen [op rollen gebaseerde toegang](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Logboeken kunnen worden verzameld van alle typen resources, niet alleen voor de VM-gegevens op basis van de agent. Bijvoorbeeld: auditlogboeken van Azure.
* Elke klant kan hebben verschillende instellingen voor hun werkruimte zoals bewaren en gegevens beperking.
* Isolatie tussen klanten voor regelgeving en nalevingsbeleid.
* De kosten in rekening gebracht voor elke werkruimte worden doorgevoerd in het abonnement van de klant.

De nadelen van de gedistribueerde architectuur zijn:

* Het centraal visualiseren en analyseren van gegevens over de tenants van klanten met hulpprogram ma's als Azure Monitor werkmappen kan leiden tot tragere ervaringen, met name bij het analyseren van gegevens over meer dan 50 werk ruimten.
* Als klanten niet zijn voor bereid voor Azure delegated resource management, moeten beheerders van service providers worden ingericht in de klanten Directory en is het moeilijker voor de service provider om een groot aantal klant tenants tegelijk te beheren.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. centrale logboeken worden opgeslagen in een werk ruimte die zich bevindt in de Tenant van de service provider

In deze architectuur, worden de logboeken worden niet opgeslagen in de tenants van de klant, maar alleen op een centrale locatie binnen een van de serviceprovider-abonnementen. De agents die zijn geïnstalleerd op virtuele machines van de klant worden geconfigureerd voor het verzenden van hun logboeken voor deze werkruimte met behulp van de werkruimte-ID en de geheime sleutel.

De voor delen van de gecentraliseerde architectuur zijn:

* Het is eenvoudig voor het beheren van een groot aantal klanten en ze naar verschillende back-endsystemen integreren.
* De serviceprovider volledig eigenaar is van de logboeken en de verschillende artefacten, zoals functies en opgeslagen query's.
* De serviceprovider kan analyses uitvoeren voor alle klanten.

De nadelen van de gecentraliseerde architectuur zijn:

* Deze architectuur is alleen van toepassing op de VM-gegevens op basis van een agent, er wordt geen betrekking op gegevensbronnen PaaS, SaaS en Azure-infrastructuur.
* Het is mogelijk moeilijk te scheiden van de gegevens tussen de klanten wanneer ze worden samengevoegd in één werkruimte. De enige methode om dit te doen is voor het gebruik van de computer volledig gekwalificeerde domeinnaam (FQDN) of via het Azure-abonnement-id. 
* Alle gegevens van alle klanten worden, opgeslagen in dezelfde regio met één factuur en dezelfde bewaartermijn en configuratie-instellingen.
* Azure-infrastructuur- en PaaS-services zoals Azure Diagnostics- en auditlogboeken van Azure moet de werkruimte zich in dezelfde tenant als de resource, dus kunnen ze de logboeken verzenden naar de centrale werkruimte.
* Alle VM-agents van alle klanten worden met dezelfde werk ruimte-ID en-sleutel geauthenticeerd naar de centrale werk ruimte. Er is geen methode voor het blokkeren van Logboeken van een specifieke klant zonder te onderbreken van andere klanten.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. hybride logboeken worden opgeslagen in de werk ruimte die zich bevindt in de Tenant van de klant en sommige daarvan worden naar een centrale locatie verplaatst.

De derde architectuur combineren tussen de twee opties. Deze is gebaseerd op de eerste gedistribueerde architectuur waar de logboeken lokaal op elke klant worden, maar sommige mechanisme gebruiken voor het maken van een centrale opslagplaats voor Logboeken. Een gedeelte van de logboeken worden opgehaald in een centrale locatie voor rapportage en analyse. Dit gedeelte wordt mogelijk beperkt aantal gegevenstypen of een samenvatting van de activiteit zoals dagelijks statistieken.

Er zijn twee opties voor het implementeren van Logboeken op een centrale locatie:

1. Centrale werk ruimte: de service provider kan een werk ruimte maken in de Tenant en een script gebruiken dat gebruikmaakt van de [query-API](https://dev.loganalytics.io/) met de [API voor gegevens verzameling](../../azure-monitor/platform/data-collector-api.md) om de gegevens van de verschillende werk ruimten naar deze centrale locatie te brengen. Een andere optie, behalve een script, is het gebruik van [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI als centrale locatie: Power BI kunnen fungeren als centrale locatie wanneer de verschillende werk ruimten gegevens exporteren met behulp van de integratie tussen de Log Analytics-werk ruimte en [Power bi](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Volgende stappen

* Het maken en configureren van werk ruimten automatiseren met behulp van [Resource Manager-sjablonen](template-workspace-configuration.md)

* Het maken van werk ruimten automatiseren met [Power shell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* [Waarschuwingen](../../azure-monitor/platform/alerts-overview.md) gebruiken om te integreren met bestaande systemen

* Samenvattings rapporten genereren met behulp van [Power bi](../../azure-monitor/platform/powerbi.md)

* Onboarding van klanten naar [Azure gedelegeerd resource beheer](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).
