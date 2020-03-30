---
title: Netwerkopties van Azure Functions
description: Een overzicht van alle netwerkopties die beschikbaar zijn in Azure-functies.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: d8c3357325eadefec7bb97faba5d600e9c6793a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276710"
---
# <a name="azure-functions-networking-options"></a>Netwerkopties van Azure Functions

In dit artikel worden de netwerkfuncties beschreven die beschikbaar zijn voor de hostingopties voor Azure-functies. Alle volgende netwerkopties geven u de mogelijkheid om toegang te krijgen tot bronnen zonder internet-routeerbare adressen te gebruiken of om de toegang tot internet tot een functie-app te beperken.

De hostingmodellen hebben verschillende niveaus van netwerkisolatie beschikbaar. Als u de juiste kiest, u voldoen aan uw netwerkisolatievereisten.

U functie-apps op een aantal manieren hosten:

* Er is een reeks planopties die worden uitgevoerd op een infrastructuur met meerdere tenants, met verschillende niveaus van virtuele netwerkconnectiviteit en schaalopties:
    * Het [verbruiksplan](functions-scale.md#consumption-plan), dat dynamisch wordt geschaald in reactie op belasting en minimale netwerkisolatieopties biedt.
    * Het [Premium-abonnement](functions-scale.md#premium-plan), dat ook dynamisch schaalt, biedt een uitgebreidere netwerkisolatie.
    * Het Azure [App Service-abonnement](functions-scale.md#app-service-plan), dat op een vaste schaal werkt en vergelijkbare netwerkisolatie biedt als het Premium-abonnement.
* U functies uitvoeren in een [App Service-omgeving.](../app-service/environment/intro.md) Deze methode implementeert uw functie in uw virtuele netwerk en biedt volledige netwerkcontrole en isolatie.

## <a name="matrix-of-networking-features"></a>Matrix van netwerkfuncties

|                |[Consumptieplan](functions-scale.md#consumption-plan)|[Premium-abonnement](functions-scale.md#premium-plan)|[App-serviceplan](functions-scale.md#app-service-plan)|[App-serviceomgeving](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Binnenkomende IP-beperkingen & toegang tot privé-site](#inbound-ip-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|
|[Integratie van virtueel netwerk](#virtual-network-integration)|❌№|✅Ja (regionaal)|✅Ja (regionaal en gateway)|✅Ja|
|[Virtuele netwerktriggers (niet-HTTP)](#virtual-network-triggers-non-http)|❌№| ✅Ja |✅Ja|✅Ja|
|[Hybride verbindingen](#hybrid-connections) (alleen Windows)|❌№|✅Ja|✅Ja|✅Ja|
|[Uitgaande IP-beperkingen](#outbound-ip-restrictions)|❌№| ✅Ja|✅Ja|✅Ja|

## <a name="inbound-ip-restrictions"></a>Binnenkomende IP-beperkingen

U IP-beperkingen gebruiken om een door prioriteit geordende lijst met IP-adressen te definiëren die toegang tot uw app zijn toegestaan of geweigerd. De lijst kan IPv4- en IPv6-adressen bevatten. Wanneer er een of meer vermeldingen zijn, bestaat er een impliciete "alles ontkennen" aan het einde van de lijst. IP-beperkingen werken met alle functiehostingopties.

> [!NOTE]
> Met netwerkbeperkingen u de portaleditor alleen gebruiken vanuit uw virtuele netwerk of wanneer u het IP-adres van de machine die u gebruikt hebt geplaatst om toegang te krijgen tot de Azure-portal in de lijst Veilige geadresseerden. U echter nog steeds vanaf elke machine toegang krijgen tot alle functies op het tabblad **Platformfuncties.**

Zie [statische toegangsbeperkingen voor Azure App Service](../app-service/app-service-ip-restrictions.md)voor meer informatie.

## <a name="private-site-access"></a>Toegang tot privésite

Toegang tot privé-site verwijst naar het alleen toegankelijk maken van uw app vanuit een privénetwerk, zoals een virtueel Azure-netwerk.

* Toegang tot privé-site's is beschikbaar in de abonnementen [Premium,](./functions-premium-plan.md) [Consumption](functions-scale.md#consumption-plan)en [App Service](functions-scale.md#app-service-plan) wanneer serviceeindpunten zijn geconfigureerd.
    * Serviceeindpunten kunnen per app worden geconfigureerd onder **Platform-functies** > **Netwerkconfiguratietoegangsbeperkingen** > **Networking** > **Regels toevoegen**. Virtuele netwerken kunnen nu als regeltype worden geselecteerd.
    * Zie [eindpunten voor virtuele netwerkservice](../virtual-network/virtual-network-service-endpoints-overview.md)voor meer informatie.
    * Houd er rekening mee dat met serviceeindpunten uw functie nog steeds volledige uitgaande toegang tot het internet heeft, zelfs met geconfigureerde virtuele netwerkintegratie.
* Toegang tot privé-site's is ook beschikbaar in een App-serviceomgeving die is geconfigureerd met een interne load balancer (ILB). Zie [Een interne load balancer maken en gebruiken met een App Service-omgeving](../app-service/environment/create-ilb-ase.md)voor meer informatie.

Zie Toegang tot [de privésite](functions-create-private-site-access.md)van Azure Functions instellen voor meer informatie over het instellen van toegang tot privé-site's .

## <a name="virtual-network-integration"></a>Integratie van virtueel netwerk

Dankzij de integratie van virtuele netwerken heeft uw functie-app toegang tot bronnen binnen een virtueel netwerk. Azure Functions ondersteunt twee soorten virtuele netwerkintegratie:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Virtuele netwerkintegratie in Azure Functions maakt gebruik van gedeelde infrastructuur met App Service-webapps. Zie voor meer informatie over de twee typen virtuele netwerkintegratie:

* [Regionale virtuele netwerkintegratie](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Gateway vereist virtuele netwerkintegratie](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Zie [Een functie-app integreren met een virtueel Azure-netwerk](functions-create-vnet.md)voor meer informatie over het instellen van virtuele netwerkintegratie.

## <a name="regional-virtual-network-integration"></a>Regionale virtuele netwerkintegratie

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connecting-to-service-endpoint-secured-resources"></a>Verbinding maken met serviceeindpuntbeveiligde resources

Als u een hoger beveiligingsniveau wilt bieden, u een aantal Azure-services beperken tot een virtueel netwerk met behulp van serviceeindpunten. U moet vervolgens uw functie-app integreren met dat virtuele netwerk om toegang te krijgen tot de bron. Deze configuratie wordt ondersteund op alle plannen die virtuele netwerkintegratie ondersteunen.

[Meer informatie over eindpunten voor virtuele netwerkservice.](../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="restricting-your-storage-account-to-a-virtual-network"></a>Uw opslagaccount beperken tot een virtueel netwerk

Wanneer u een functie-app maakt, moet u een Azure Storage-account voor algemene doeleinden maken of koppelen dat blob-, wachtrij- en tabelopslag ondersteunt. U momenteel geen virtuele netwerkbeperkingen voor dit account gebruiken. Als u een eindpunt voor de virtuele netwerkservice configureert op het opslagaccount dat u voor uw functie-app gebruikt, wordt uw app afgebroken.

[Meer informatie over vereisten voor opslagaccount.](./functions-create-function-app-portal.md#storage-account-requirements)

## <a name="using-key-vault-references"></a>Key Vault-referenties gebruiken 

Met Key Vault-verwijzingen u geheimen van Azure Key Vault gebruiken in uw Azure-functietoepassing zonder dat er codewijzigingen nodig zijn. Azure Key Vault is een service die gecentraliseerd geheimenbeheer biedt, met volledige controle over toegangsbeleid en controlegeschiedenis.

Momenteel [werken Key Vault-referenties](../app-service/app-service-key-vault-references.md) niet als uw Key Vault is beveiligd met serviceeindpunten. Als u verbinding wilt maken met een Key Vault via virtuele netwerkintegratie, moet u de sleutelkluis in uw toepassingscode aanroepen.

## <a name="virtual-network-triggers-non-http"></a>Virtuele netwerktriggers (niet-HTTP)

Momenteel u niet-HTTP-triggerfuncties vanuit een virtueel netwerk op twee manieren gebruiken: 
+ Voer uw functie-app uit in een Premium-abonnement en schakel ondersteuning voor virtuele netwerktriggerin.
+ Voer uw functie-app uit in een App Service-abonnement of App Service-omgeving.

### <a name="premium-plan-with-virtual-network-triggers"></a>Premium Plan met virtuele netwerktriggers

Wanneer u een Premium-abonnement uitvoert, u niet-HTTP-triggerfuncties verbinden met services die binnen een virtueel netwerk worden uitgevoerd. Hiervoor moet u ondersteuning voor virtuele netwerktriggervoor uw functie-app inschakelen. De **instelling voor ondersteuning voor virtuele netwerktriggeren** wordt gevonden in de [Azure-portal](https://portal.azure.com) onder **Instellingen voor functie-apps**.

![VNETToggle](media/functions-networking-options/virtual-network-trigger-toggle.png)

U ook virtuele netwerktriggers inschakelen met de volgende opdracht Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

Virtuele netwerktriggers worden ondersteund in versie 2.x en hoger van de runtime functies. De volgende niet-HTTP-triggertypen worden ondersteund.

| Toestelnummer | Minimale versie |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 of hoger |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 of hoger|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 of hoger|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 of hoger|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 of hoger|

> [!IMPORTANT]
> Wanneer u ondersteuning voor virtuele netwerktriggeren inschakelt, worden alleen de triggertypen boven de toepassing dynamisch met uw toepassing geschaald. U nog steeds triggers gebruiken die hierboven niet zijn vermeld, maar ze worden niet geschaald buiten het aantal vooraf verwarmde instanties. Zie [triggers en bindingen](./functions-triggers-bindings.md#supported-bindings) voor de volledige lijst met triggers.

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service Plan en App Service Omgeving met virtuele netwerk triggers

Wanneer uw functie-app wordt uitgevoerd in een App Service Plan of een App Service Environment, u niet-HTTP trigger functies gebruiken. Als u uw functies correct wilt activeren, moet u zijn verbonden met een virtueel netwerk met toegang tot de bron die is gedefinieerd in de triggerverbinding. 

Stel dat u Azure Cosmos DB wilt configureren om alleen verkeer van een virtueel netwerk te accepteren. In dit geval moet u uw functie-app implementeren in een App Service-abonnement dat virtuele netwerkintegratie met dat virtuele netwerk biedt. Hierdoor kan een functie worden geactiveerd door die Azure Cosmos DB-bron. 

## <a name="hybrid-connections"></a>Hybride verbindingen

[Hybride verbindingen](../service-bus-relay/relay-hybrid-connections-protocol.md) is een functie van Azure Relay die u gebruiken om toegang te krijgen tot toepassingsbronnen in andere netwerken. Het biedt toegang van uw app tot een eindpunt van een toepassing. U het niet gebruiken om toegang te krijgen tot uw toepassing. Hybride verbindingen zijn beschikbaar voor functies die in alle functies op Windows worden uitgevoerd, behalve voor het verbruiksplan.

Zoals gebruikt in Azure Functions, correleert elke hybride verbinding met één TCP-host- en poortcombinatie. Dit betekent dat het eindpunt van de hybride verbinding op elk besturingssysteem en elke toepassing kan worden ingeschakeld, zolang u toegang hebt tot een TCP-luisterpoort. De functie Hybride verbindingen weet niet of geeft om wat het toepassingsprotocol is of wat u opent. Het biedt alleen toegang tot het netwerk.

Zie de documentatie [van app-service voor hybride verbindingen voor](../app-service/app-service-hybrid-connections.md)meer informatie . Dezelfde configuratiestappen ondersteunen Azure-functies.

>[!IMPORTANT]
> Hybride verbindingen worden alleen ondersteund op Windows-abonnementen. Linux wordt niet ondersteund

## <a name="outbound-ip-restrictions"></a>Uitgaande IP-beperkingen

Uitgaande IP-beperkingen zijn beschikbaar in een Premium-abonnement, App Service-abonnement of App Service-omgeving. U uitgaande beperkingen configureren voor het virtuele netwerk waar uw App Service-omgeving is geïmplementeerd.

Wanneer u een functie-app integreert in een Premium-abonnement of een App Service-abonnement met een virtueel netwerk, kan de app nog steeds standaard uitgaande gesprekken naar het internet voeren. Door een toepassingsinstelling `WEBSITE_VNET_ROUTE_ALL=1`toe te voegen, dwingt u al het uitgaande verkeer te worden verzonden naar uw virtuele netwerk, waar regels voor netwerkbeveiliging kunnen worden gebruikt om het verkeer te beperken.

## <a name="troubleshooting"></a>Problemen oplossen 

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Volgende stappen

Ga als voor meer informatie over netwerken en Azure-functies:

* [Volg de zelfstudie over aan de slag gaan met virtuele netwerkintegratie](./functions-create-vnet.md)
* [Lees de veelgestelde vragen over het netwerken van functies](./functions-networking-faq.md)
* [Meer informatie over virtuele netwerkintegratie met App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Meer informatie over virtuele netwerken in Azure](../virtual-network/virtual-networks-overview.md)
* [Meer netwerkfuncties en -bediening inschakelen met App-serviceomgevingen](../app-service/environment/intro.md)
* [Verbinding maken met afzonderlijke on-premises bronnen zonder firewallwijzigingen met hybride verbindingen](../app-service/app-service-hybrid-connections.md)
