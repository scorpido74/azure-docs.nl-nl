---
title: Azure Monitor voor service providers | Microsoft Docs
description: Met Azure Monitor kunt u Msp's (Managed Service Providers), grote ondernemingen, Isv's (Independent Software Vendors) en hosting service providers servers beheren en bewaken in de on-premises of Cloud infrastructuur van de klant.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 08/06/2019
ms.openlocfilehash: b0f25d01421edd329b03d8f2b7e1aafaa2ba67d5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932065"
---
# <a name="azure-monitor-for-service-providers"></a>Azure Monitor voor service providers
Log Analytics-werk ruimten in Azure Monitor kunnen Msp's (Managed Service Providers), grote ondernemingen, onafhankelijke software leveranciers (Isv's) en hosting serviceproviders servers in de on-premises of Cloud infrastructuur van de klant beheren en bewaken. 

Grote bedrijven delen veel overeenkomsten met service providers, met name wanneer er sprake is van een gecentraliseerd IT-team dat verantwoordelijk is voor het beheer van een groot aantal verschillende bedrijfs eenheden. Voor het gemak gebruikt dit document de term *service provider* , maar dezelfde functionaliteit is ook beschikbaar voor ondernemingen en andere klanten.

Voor partners en service providers die deel uitmaken van het programma [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , is Log Analytics in azure monitor een van de Azure-Services die beschikbaar zijn in [Azure CSP-abonnementen](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Architecturen voor service providers

Log Analytics-werk ruimten bieden een methode voor de beheerder om de stroom en isolatie van [logboek](data-platform-logs.md) gegevens te beheren en een architectuur te maken voor de specifieke zakelijke behoeften. In [dit artikel](design-logs-deployment.md) worden de aandachtspunten voor het ontwerp, de implementatie en de migratie van een werk ruimte beschreven en het artikel [toegangs beheer](manage-access.md) bevat informatie over het Toep assen en beheren van machtigingen voor logboek gegevens. Service providers hebben extra overwegingen.

Er zijn drie mogelijke architecturen voor service providers die betrekking hebben op Log Analytics-werk ruimten:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. gedistribueerde logboeken worden opgeslagen in werk ruimten die zich bevinden in de Tenant van de klant 

In deze architectuur wordt een werk ruimte geïmplementeerd in de Tenant van de klant die wordt gebruikt voor alle logboeken van die klant. De beheerders van de service provider krijgen toegang tot deze werk ruimte via [Azure Active Directory gast gebruikers (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). De beheerder van de service provider moet overschakelen naar de map van de klant in de Azure Portal om toegang te kunnen krijgen tot deze werk ruimten.

De voor delen van deze architectuur zijn:
* De klant kan de toegang tot de logboeken beheren met behulp van hun eigen [op rollen gebaseerde toegang](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Elke klant kan verschillende instellingen voor hun werk ruimte hebben, zoals retentie en het afgetopten van gegevens.
* Isolatie tussen klanten voor regelgevings-en nalevings.
* De kosten voor elke werk ruimte worden meegenomen in het abonnement van de klant.
* Logboeken kunnen worden verzameld van alle typen resources, niet alleen op basis van een agent. Bijvoorbeeld Azure audit logs.

De nadelen van deze architectuur zijn:
* Het is moeilijker voor de service provider om een groot aantal klant tenants tegelijk te beheren.
* Beheerders van de service provider moeten worden ingericht in de klanten Directory.
* De service provider kan geen gegevens over de klanten analyseren.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. centrale logboeken worden opgeslagen in een werk ruimte die zich bevindt in de Tenant van de service provider

In deze architectuur worden de logboeken niet opgeslagen in de tenants van de klant, maar alleen op een centrale locatie binnen een van de abonnementen van de service provider. De agents die zijn geïnstalleerd op de virtuele machines van de klant, zijn geconfigureerd voor het verzenden van hun logboeken naar deze werk ruimte met behulp van de werk ruimte-ID en de geheime sleutel.

De voor delen van deze architectuur zijn:
* Het is eenvoudig om een groot aantal klanten te beheren en ze te integreren op verschillende back-end-systemen.

* De service provider heeft volledig eigendom van de logboeken en de verschillende artefacten, zoals functies en opgeslagen query's.

* De service provider kan analyses uitvoeren voor alle klanten.

De nadelen van deze architectuur zijn:
* Deze architectuur is alleen van toepassing op VM-gegevens op basis van een agent, die geen PaaS, SaaS en Azure Fabric-gegevens bronnen bevat.

* Het kan lastig zijn om de gegevens tussen de klanten te scheiden wanneer ze in één werk ruimte worden samengevoegd. De enige manier om dit te doen is door de Fully Qualified Domain Name van de computer (FQDN) of via de ID van het Azure-abonnement te gebruiken. 

* Alle gegevens van alle klanten worden opgeslagen in dezelfde regio met één factuur en dezelfde Bewaar periode en configuratie-instellingen.

* Voor Azure Fabric-en PaaS-services zoals Azure Diagnostics en Azure-audit logboeken moet de werk ruimte zich in dezelfde Tenant bevinden als de resource, waardoor de logboeken niet kunnen worden verzonden naar de centrale werk ruimte.

* Alle VM-agents van alle klanten worden met dezelfde werk ruimte-ID en-sleutel geauthenticeerd naar de centrale werk ruimte. Er is geen methode om logboeken van een specifieke klant te blok keren zonder andere klanten te onderbreken.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. hybride logboeken worden opgeslagen in de werk ruimte die zich bevindt in de Tenant van de klant en sommige daarvan worden naar een centrale locatie verplaatst.

De derde architectuur mix tussen de twee opties. Het is gebaseerd op de eerste gedistribueerde architectuur waarbij de logboeken lokaal zijn voor elke klant, maar een bepaald mechanisme gebruiken om een centrale opslag plaats van logboeken te maken. Een gedeelte van de logboeken wordt binnengebracht in een centrale locatie voor rapportage en analyse. Dit gedeelte kan klein aantal gegevens typen zijn of een samen vatting van de activiteit, zoals dagelijkse statistieken.

Er zijn twee opties voor het implementeren van Logboeken op een centrale locatie:

1. Centrale werk ruimte: de service provider kan een werk ruimte maken in de Tenant en een script gebruiken dat gebruikmaakt van de [query-API](https://dev.loganalytics.io/) met de [API voor gegevens verzameling](../../azure-monitor/platform/data-collector-api.md) om de gegevens van de verschillende werk ruimten naar deze centrale locatie te brengen. Een andere optie, behalve een script, is het gebruik van [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI als centrale locatie: Power BI kunnen fungeren als centrale locatie wanneer de verschillende werk ruimten gegevens exporteren met behulp van de integratie tussen de Log Analytics-werk ruimte en [Power bi](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Volgende stappen

* Het maken en configureren van werk ruimten automatiseren met behulp van [Resource Manager-sjablonen](template-workspace-configuration.md)

* Het maken van werk ruimten automatiseren met [Power shell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* [Waarschuwingen](../../azure-monitor/platform/alerts-overview.md) gebruiken om te integreren met bestaande systemen

* Samenvattings rapporten genereren met behulp van [Power bi](../../azure-monitor/platform/powerbi.md)

* Controleer het proces van het [configureren van log Analytics en Power bi om meerdere CSP-klanten te bewaken](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
