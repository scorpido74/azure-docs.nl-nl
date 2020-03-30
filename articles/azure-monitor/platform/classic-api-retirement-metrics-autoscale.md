---
title: Azure Monitor met pensioen gaan van klassieke implementatiemodel API's voor statistieken en autoscale
description: Statistieken en automatische schaal klassieke API's, ook wel Azure Service Management (ASM) of RDFE-implementatiemodel genoemd, worden buiten gebruik gesteld
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 9dfa6b278587f4ed79b1c3cd9eff1defd09ec0bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294659"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor met pensioen gaan van klassieke implementatiemodel API's voor statistieken en autoscale

Azure Monitor (voorheen Azure Insights toen deze voor het eerst werd uitgebracht) biedt momenteel de mogelijkheid om autoscale-instellingen te maken en te beheren voor en statistieken uit klassieke VM's en klassieke Cloud Services te gebruiken. De oorspronkelijke set op klassieke implementatiemodellen gebaseerde API's worden **na 30 juni 2019 in** alle openbare azure-clouds en privéclouds in alle regio's buiten gebruik gesteld.   

Dezelfde bewerkingen worden al meer dan een jaar ondersteund via een reeks API's op basis van Azure Resource Manager. De Azure-portal gebruikt de nieuwe REST API's voor zowel autoscale als metrics. Er zijn ook een nieuwe SDK, PowerShell en CLI op basis van deze Resource Manager API's beschikbaar. Onze partnerservices voor monitoring verbruiken de nieuwe REST API's op basis van Resource Manager in Azure Monitor.  

## <a name="who-is-not-affected"></a>Wie wordt er niet beïnvloed

Als u automatisch schalen beheert via de Azure-portal, de [nieuwe Azure Monitor SDK-](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), PowerShell-, CLI- of Resource Manager-sjablonen, is er geen actie nodig.  

Als u metrics consumeert via de Azure-portal of via verschillende [monitoringpartnerservices,](../../azure-monitor/platform/partners.md)is er geen actie nodig. Microsoft werkt samen met monitoringpartners om te migreren naar de nieuwe API's.

## <a name="who-is-affected"></a>Wie is getroffen

Dit artikel is op u van toepassing als u de volgende onderdelen gebruikt:

- **Klassieke Azure Insights SDK** - Als u de [klassieke Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)gebruikt, schakelt u over op het gebruik van de nieuwe Azure Monitor SDK voor [.NET](https://github.com/azure/azure-libraries-for-net#download) of [Java](https://github.com/azure/azure-libraries-for-java#download). Download het [Azure Monitor SDK NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Klassieke autoscale** - Als u de [klassieke API's](https://msdn.microsoft.com/library/azure/mt348562.aspx) voor automatische schaalinstellingen aanroept vanuit uw op maat gemaakte hulpprogramma's of de [klassieke Azure Insights SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)gebruikt, moet u overschakelen naar het gebruik van de Api voor Azure Monitor REST van [Resource Manager.](https://docs.microsoft.com/rest/api/monitor/autoscalesettings)

- **Klassieke statistieken** - Als u statistieken gebruikt met behulp van de [klassieke REST API's](https://msdn.microsoft.com/library/azure/dn510374.aspx) of klassieke Azure Insights [SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) van op maat gemaakte hulpprogramma's, moet u overschakelen naar het gebruik van de Resource Manager Azure Monitor [REST API.](https://docs.microsoft.com/rest/api/monitor/autoscalesettings) 

Als u niet zeker weet of uw code of aangepaste hulpprogramma's de klassieke API's aanroepen, kijk dan op het volgende:

- Bekijk de URI waarnaar wordt verwezen in uw code of tool. De klassieke API's https://management.core.windows.netmaken gebruik van de URI. U moet de nieuwere URI gebruiken voor de API's op basis van Resource Manager, die begint met `https://management.azure.com/`.

- Vergelijk de naam van de montage op uw machine. De oudere klassieke https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/montage is op .

- Als u certificaatverificatie gebruikt om toegang te krijgen tot statistieken of API's automatisch te schalen, gebruikt u een klassiek eindpunt en een klassieke bibliotheek. De nieuwere API's voor Resource Manager vereisen Azure Active Directory-verificatie via een serviceprincipal of gebruikersprincipal.

- Als u oproepen gebruikt waarnaar wordt verwezen in de documentatie op een van de volgende koppelingen, gebruikt u de oudere klassieke API's.

  - [Windows.Azure.Management.Monitoring Class Library](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Monitoring (klassiek) .NET](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations Interface](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Waarom u moet overstappen

Alle bestaande mogelijkheden voor autoscale en metrics blijven werken via de nieuwe API's.  

Migreren naar nieuwere API's worden geleverd met resourcebeheergebaseerde mogelijkheden, zoals ondersteuning voor consistente RBAC (Role-Based Access Control) voor al uw bewakingsservices. U krijgt ook extra functionaliteit voor statistieken: 

- ondersteuning voor dimensies
- consistente metrische granulariteit van 1 minuut voor alle services 
- beter opvragen
- hogere gegevensbewaring (93 dagen statistieken vs. 30 dagen) 

Over het algemeen bieden de op Resource Manager gebaseerde Azure Monitor API's, net als alle andere services in Azure, betere prestaties, schaalbaarheid en betrouwbaarheid. 

## <a name="what-happens-if-you-do-not-migrate"></a>Wat gebeurt er als u niet migreert

### <a name="before-retirement"></a>Voor zijn pensioen

Uw Azure-services of hun workloads hebben geen directe gevolgen.  

### <a name="after-retirement"></a>Na pensionering

Oproepen naar de eerder genoemde klassieke API's mislukken en sturen foutberichten terug die vergelijkbaar zijn met de volgende:

Voor autoscale: *Deze API is afgeschaft. Gebruik de Azure-portal-, Azure Monitor SDK-, PowerShell-, CLI- of Resource Manager-sjablonen om instellingen voor automatisch schalen te beheren.*  

Voor statistieken: *deze API is afgeschaft. Gebruik de Azure-portal, Azure Monitor SDK, PowerShell, CLI om statistieken op te vragen.*

## <a name="email-notifications"></a>E-mailmeldingen

Er is een pensioenmelding verzonden naar e-mailadressen voor de volgende accountrollen: 

- Account- en servicebeheerders
- Medebeheerders  

Mocht u vragen hebben, MonitorClassicAPIhelp@microsoft.comneem dan contact met ons op op.  

## <a name="references"></a>Verwijzingen

- [Nieuwere REST-API's voor Azure-monitor](https://docs.microsoft.com/rest/api/monitor/) 
- [Nieuwere Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
