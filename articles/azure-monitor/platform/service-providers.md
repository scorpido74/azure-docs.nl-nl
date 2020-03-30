---
title: Azure-monitorlogboeken voor serviceproviders | Microsoft Documenten
description: Azure Monitor Logs kunnen Managed Service Providers (MSP's), grote ondernemingen, Independent Software Vendors (ISV's) en hostingserviceproviders helpen bij het beheren en monitoren van servers in de on-premises of cloudinfrastructuur van de klant.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658877"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Azure-monitorlogboeken voor serviceproviders

Log Analytics-werkruimten in Azure Monitor kunnen managed service providers (MSP's), grote ondernemingen, onafhankelijke softwareleveranciers (ISV's) en hostingserviceproviders helpen bij het beheren en bewaken van servers in de on-premises of cloudinfrastructuur van de klant.

Grote ondernemingen delen veel overeenkomsten met serviceproviders, vooral wanneer er een gecentraliseerd IT-team is dat verantwoordelijk is voor het beheer van IT voor veel verschillende business units. Voor de eenvoud gebruikt dit document de term *serviceprovider,* maar dezelfde functionaliteit is ook beschikbaar voor ondernemingen en andere klanten.

Voor partners en serviceproviders die deel uitmaken van het [CSP-programma (Cloud Solution Provider)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) is Log Analytics in Azure Monitor een van de Azure-services die beschikbaar zijn in Azure CSP-abonnementen.

Log Analytics in Azure Monitor kan ook worden gebruikt door een serviceprovider die klantbronnen beheert via de azure delegated resource management-mogelijkheden in [Azure Lighthouse.](https://docs.microsoft.com/azure/lighthouse/overview)

## <a name="architectures-for-service-providers"></a>Architecturen voor serviceproviders

Log Analytics-werkruimten bieden de beheerder een methode om de stroom en isolatie van [logboekgegevens](data-platform-logs.md) te beheren en een architectuur te maken die voldoet aan de specifieke bedrijfsbehoeften. [In dit artikel](design-logs-deployment.md) worden de overwegingen ontwerp, implementatie en migratie voor een werkruimte uitgelegd en wordt in het artikel [beheertoegang](manage-access.md) uitgelegd hoe machtigingen voor logboekgegevens kunnen worden toegepast en beheren. Dienstverleners hebben aanvullende overwegingen.

Er zijn drie mogelijke architecturen voor serviceproviders met betrekking tot Log Analytics-werkruimten:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Gedistribueerd - Logboeken worden opgeslagen in werkruimten in de tenant van de klant

In deze architectuur wordt een werkruimte geïmplementeerd in de tenant van de klant die wordt gebruikt voor alle logboeken van die klant.

Er zijn twee manieren waarop beheerders van serviceproviders toegang kunnen krijgen tot een Log Analytics-werkruimte in een klanttenant:

- Een klant kan individuele gebruikers van de serviceprovider toevoegen als [Azure Active Directory-gastgebruikers (B2B).](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) De beheerders van de serviceprovider moeten zich aanmelden bij de map van elke klant in de Azure-portal om toegang te krijgen tot deze werkruimten. Dit vereist ook dat de klanten individuele toegang beheren voor elke beheerder van de serviceprovider.
- Voor meer schaalbaarheid en flexibiliteit kunnen serviceproviders de Azure [delegated resource management-mogelijkheid](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) van [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) gebruiken om toegang te krijgen tot de tenant van de klant. Met deze methode worden de beheerders van de serviceprovider opgenomen in een Azure AD-gebruikersgroep in de tenant van de serviceprovider en krijgt deze groep toegang tijdens het onboardingproces voor elke klant. Deze beheerders hebben vervolgens toegang tot de werkruimten van elke klant vanuit hun eigen serviceprovidertenant, in plaats van afzonderlijk in te loggen op de tenant van elke klant. Als u op deze manier toegang krijgt tot de log analytics-werkruimtes van uw klanten, wordt het werk dat aan de klantzijde nodig is, verminderd en u gemakkelijker gegevens verzamelen en analyseren van meerdere klanten die door dezelfde serviceprovider worden beheerd via hulpprogramma's zoals [Azure Monitor Workbooks.](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview) Zie [Klantbronnen op schaal controleren voor](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale)meer informatie.

De voordelen van de gedistribueerde architectuur zijn:

* De klant kan specifieke niveaus van machtigingen bevestigen via [azure gedelegeerd resourcebeheer](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)of de toegang tot de logboeken beheren met behulp van hun eigen [op rollen gebaseerde toegang.](https://docs.microsoft.com/azure/role-based-access-control/overview)
* Logboeken kunnen worden verzameld uit alle soorten resources, niet alleen op agentgebaseerde VM-gegevens. Azure Audit Logs bijvoorbeeld.
* Elke klant kan verschillende instellingen voor zijn werkruimte hebben, zoals retentie en gegevenslimieten.
* Isolatie tussen klanten voor regelgeving en compliancy.
* De kosten voor elke werkruimte worden in het abonnement van de klant gerold.

De nadelen van de gedistribueerde architectuur zijn:

* Het centraal visualiseren en analyseren van gegevens voor klanttenants met hulpprogramma's zoals Azure Monitor Workbooks kan leiden tot tragere ervaringen, vooral bij het analyseren van gegevens in meer dan 50+ werkruimten.
* Als klanten niet zijn aangesloten op azure-gedelegeerd resourcebeheer, moeten beheerders van serviceproviders worden ingericht in de klantenmap en is het moeilijker voor de serviceprovider om een groot aantal klanttenants tegelijk te beheren.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Centraal - Logboeken worden opgeslagen in een werkruimte in de serviceprovidertenant

In deze architectuur worden de logboeken niet opgeslagen in de huurders van de klant, maar alleen op een centrale locatie binnen een van de abonnementen van de serviceprovider. De agents die op de VM's van de klant zijn geïnstalleerd, zijn geconfigureerd om hun logboeken naar deze werkruimte te verzenden met behulp van de werkruimte-id en de geheime sleutel.

De voordelen van de gecentraliseerde architectuur zijn:

* Het is eenvoudig om een groot aantal klanten te beheren en te integreren in verschillende backend systemen.
* De serviceprovider heeft de volledige eigendom van de logboeken en de verschillende artefacten, zoals functies en opgeslagen query's.
* De serviceprovider kan analyses uitvoeren voor al zijn klanten.

De nadelen van de gecentraliseerde architectuur zijn:

* Deze architectuur is alleen van toepassing op vm-gegevens op basis van agenten, maar heeft geen betrekking op PaaS-, SaaS- en Azure-fabricgegevensbronnen.
* Het kan moeilijk zijn om de gegevens te scheiden tussen de klanten wanneer ze worden samengevoegd in één werkruimte. De enige goede methode om dit te doen is het gebruik van de volledig gekwalificeerde domeinnaam van de computer (FQDN) of via de Azure-abonnements-ID. 
* Alle gegevens van alle klanten worden opgeslagen in dezelfde regio met één factuur en dezelfde bewaar- en configuratie-instellingen.
* Azure-fabric- en PaaS-services zoals Azure Diagnostics en Azure Audit Logs vereisen dat de werkruimte zich in dezelfde tenant bevindt als de bron, zodat ze de logboeken niet naar de centrale werkruimte kunnen verzenden.
* Alle VM-agents van alle klanten worden geverifieerd naar de centrale werkruimte met dezelfde werkruimte-id en sleutel. Er is geen methode om logboeken van een specifieke klant te blokkeren zonder andere klanten te onderbreken.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybride - Logboeken worden opgeslagen in werkruimte in de tenant van de klant en sommige van hen worden naar een centrale locatie getrokken.

De derde architectuur mix tussen de twee opties. Het is gebaseerd op de eerste gedistribueerde architectuur waarbij de logboeken lokaal zijn voor elke klant, maar met behulp van een mechanisme om een centrale opslagplaats van logboeken te maken. Een deel van de logboeken wordt naar een centrale locatie voor rapportage en analyse getrokken. Dit gedeelte kan een klein aantal gegevenstypen zijn of een overzicht van de activiteit, zoals dagelijkse statistieken.

Er zijn twee opties om logboeken op een centrale locatie te implementeren:

1. Centrale werkruimte: de serviceprovider kan een werkruimte in de tenant maken en een script gebruiken dat de [Query-API](https://dev.loganalytics.io/) met de [API voor gegevensverzameling](../../azure-monitor/platform/data-collector-api.md) gebruikt om de gegevens van de verschillende werkruimten naar deze centrale locatie te brengen. Een andere optie dan een script is het gebruik van [Azure Logic Apps.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)

2. Power BI als centrale locatie: Power BI kan fungeren als de centrale locatie wanneer de verschillende werkruimten gegevens naar de centrale locatie exporteren met behulp van de integratie tussen de Log Analytics-werkruimte en [Power BI.](../../azure-monitor/platform/powerbi.md) 

## <a name="next-steps"></a>Volgende stappen

* Het maken en configureren van werkruimten automatiseren met behulp van [Resource Manager-sjablonen](template-workspace-configuration.md)

* Het maken van werkruimten automatiseren met [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* [Waarschuwingen gebruiken](../../azure-monitor/platform/alerts-overview.md) om te integreren met bestaande systemen

* Overzichtsrapporten genereren met [Power BI](../../azure-monitor/platform/powerbi.md)

* Klanten aan boord van [Azure gedelegeerd resourcebeheer](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).
