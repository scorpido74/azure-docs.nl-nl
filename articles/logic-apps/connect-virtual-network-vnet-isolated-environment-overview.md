---
title: Toegang tot virtuele Azure-netwerken
description: Overzicht van de manier waarop Integration service environments (ISEs) Logic apps toegang hebben tot Azure Virtual Networks (VNETs)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: d74303df74a1e877645b333fa0726a68055c819b
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734910"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Toegang tot Azure Virtual Network resources vanuit Azure Logic Apps met behulp van integratie service omgevingen (ISEs)

Soms hebben uw Logic apps toegang nodig tot beveiligde bronnen, zoals virtuele machines (Vm's) en andere systemen of services die zich binnen een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md)bevinden. Als u deze toegang wilt instellen, kunt u [een ISE ( *Integration service Environment* ) maken](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Een ISE is een geïsoleerd exemplaar van de Logic Apps service dat gebruikmaakt van toegewezen bronnen en wordt afzonderlijk uitgevoerd op basis van de ' wereld wijde ' multi-tenant Logic Apps-service.

Het uitvoeren van Logic apps in uw eigen afzonderlijke geïsoleerde instantie vermindert de invloed die andere Azure-tenants mogelijk hebben op de prestaties van uw apps, ook wel bekend als het [effect ' ruiserende neighbors '](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Een ISE biedt ook de volgende voor delen:

* Uw eigen vaste IP-adressen, die worden gescheiden van de statische IP-adressen die worden gedeeld door de Logic apps in de multi tenant-service. U kunt ook één openbaar, statisch en voorspelbaar uitgaand IP-adres instellen om te communiceren met de doel systemen. Op die manier hoeft u geen aanvullende firewall-openingen op deze doel systemen in te stellen voor elke ISE.

* Verhoogde limieten voor de uitvoerings duur, opslag behoud, door Voer, HTTP-aanvraag en time-outwaarden, bericht grootten en aangepaste connector aanvragen. Zie [limieten en configuratie voor Azure Logic apps](logic-apps-limits-and-config.md)voor meer informatie.

> [!NOTE]
> Sommige virtuele netwerken van Azure gebruiken privé-eind punten ([persoonlijke Azure-koppeling](../private-link/private-link-overview.md)) om toegang te bieden tot Azure PaaS-Services, zoals Azure Storage, Azure Cosmos DB of Azure SQL database, partner services of klanten services die worden gehost op Azure. Als uw Logic apps toegang nodig hebben tot virtuele netwerken die gebruikmaken van privé-eind punten, moet u deze Logic apps maken, implementeren en uitvoeren in een ISE.

Wanneer u een ISE maakt, *injecteert* Azure of implementeert deze ISE in uw virtuele Azure-netwerk. U kunt deze ISE vervolgens gebruiken als de locatie voor de Logic apps en integratie accounts die toegang nodig hebben.

![Integratie service omgeving selecteren](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Logic apps hebben toegang tot bronnen die zich binnen of verbonden zijn met uw virtuele netwerk met behulp van deze items, die worden uitgevoerd in dezelfde ISE als uw Logic apps:

* Een **kern**, ingebouwde trigger of actie, zoals de http-trigger of actie
* Een **ISE**-gelabelde connector voor dat systeem of deze service
* Een aangepaste connector

U kunt nog steeds connectors gebruiken die geen **core** -of **ISE** -label hebben met de Logic apps in uw ISE. Deze connectors worden in plaats daarvan uitgevoerd in de multi tenant-Logic Apps service. Zie voor meer informatie deze secties:

* [Geïsoleerd versus multi tenant](#difference)
* [Verbinding maken vanuit een integratie service omgeving](../connectors/apis-list.md#integration-service-environment)
* [ISE-connectors](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Logic apps, ingebouwde triggers, ingebouwde acties en connectors die worden uitgevoerd in uw ISE, gebruiken een prijs plan dat verschilt van het prijs plan op basis van verbruik. Zie [Logic apps prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie. Zie [Logic apps prijzen](../logic-apps/logic-apps-pricing.md)voor prijs informatie.

In dit overzicht vindt u meer informatie over hoe een ISE uw Logic apps direct toegang geeft tot uw virtuele Azure-netwerk en de verschillen tussen een ISE en de multi tenant-Logic Apps service vergelijkt.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Geïsoleerd versus multi tenant

Wanneer u logische apps maakt en uitvoert in een ISE, krijgt u dezelfde gebruikers ervaringen en vergelijk bare mogelijkheden als de multi tenant-Logic Apps service. U kunt alle dezelfde ingebouwde triggers, acties en beheerde connectors gebruiken die beschikbaar zijn in de multi tenant-Logic Apps service. Sommige beheerde connectors bieden extra ISE-versies. Het verschil tussen ISE-connectors en niet-ISE-connectors bevindt zich in de locatie waar ze worden uitgevoerd en de labels die ze hebben in de ontwerp functie voor logische apps wanneer u werkt binnen een ISE.

![Connectors met en zonder labels in een ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Bij ingebouwde triggers en acties wordt het **kern** label weer gegeven. Ze worden altijd uitgevoerd in dezelfde ISE als uw logische app. Beheerde connectors die het label **ISE** weer geven, worden ook uitgevoerd in dezelfde ISE als uw logische app.

  Dit zijn bijvoorbeeld enkele connectors die ISE-versies aanbieden:

  * Azure Blob Storage, File Storage en Table Storage
  * Azure-wacht rijen, Azure Service Bus, Azure Event Hubs en IBM MQ
  * FTP en SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse Azure Cosmos DB
  * AS2, X12 en EDIFACT

* Beheerde connectors die geen extra labels weer geven, worden altijd uitgevoerd in de multi tenant-Logic Apps service, maar u kunt deze connectors nog steeds gebruiken in een ISE-gehoste logische app.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Toegang tot on-premises systemen

Logische apps in een ISE kunnen deze items gebruiken om toegang te krijgen tot on-premises systemen of gegevens bronnen die zijn verbonden met een virtueel Azure-netwerk:

* HTTP-actie

* ISE-gelabelde connector voor dat systeem

  > [!NOTE]
  > Als u Windows-verificatie wilt gebruiken met de SQL Server-connector in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), gebruikt u de niet-ISE-versie van de connector met de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-install.md). De ISE-label versie biedt geen ondersteuning voor Windows-verificatie.

* Aangepaste connector

  * Als u aangepaste connectors hebt die de on-premises gegevens gateway nodig hebben en u deze connectors buiten een ISE hebt gemaakt, kunnen logische apps in een ISE ook deze connectors gebruiken.

  * Aangepaste connectors die zijn gemaakt in een ISE werken niet met de on-premises gegevens gateway. Deze connectors hebben echter rechtstreeks toegang tot on-premises gegevens bronnen die zijn verbonden met het virtuele netwerk dat als host fungeert voor de ISE. Logic apps in een ISE hebben daarom waarschijnlijk niet de gegevens gateway nodig bij het communiceren met deze resources.

Voor on-premises systemen die niet zijn verbonden met een virtueel netwerk of geen ISE-connectors hebben, moet u eerst [de on-premises gegevens gateway instellen](../logic-apps/logic-apps-gateway-install.md) voordat uw Logic apps verbinding met deze systemen kunnen maken.

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE Sku's

Wanneer u uw ISE maakt, kunt u de Developer SKU of Premium SKU selecteren. Dit zijn de verschillen tussen deze Sku's:

* **Ontwikkelaar**

  Biedt een voordelige ISE die u kunt gebruiken voor experimenteren, ontwikkelen en testen, maar niet voor productie-of prestatie testen. De Developer SKU bevat ingebouwde triggers en acties, standaard connectors, zakelijke connectors en één [gratis laag](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integratie account voor een vaste maandelijkse prijs. Deze SKU bevat echter geen SLA (Service Level Agreement), opties voor het schalen van de capaciteit of redundantie tijdens het recyclen, wat betekent dat u vertragingen of downtime mogelijk ondervindt.

* **Premium**

  Biedt een ISE die u kunt gebruiken voor productie en die SLA-ondersteuning, ingebouwde triggers en acties, standaard connectors, zakelijke connectors, een [standaard-laag](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integratie account, opties voor het schalen van de capaciteit en redundantie tijdens het recyclen van een vaste maandelijkse prijs.

> [!IMPORTANT]
> De SKU-optie is alleen beschikbaar bij het maken van ISE en kan later niet worden gewijzigd.

Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijs tarieven. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over de prijzen en facturerings werkzaamheden voor ISEs.

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Toegang tot ISE-eind punt

Wanneer u uw ISE maakt, kunt u kiezen of u interne of Externe toegangs punten wilt gebruiken. Uw selectie bepaalt of aanvragen of webhooks worden geactiveerd op Logic apps in uw ISE kan aanroepen ontvangen van buiten uw virtuele netwerk. Deze eind punten zijn ook van invloed op de manier waarop u toegang hebt tot de invoer en uitvoer van de geschiedenis van de uitvoeringen van de Logic apps.

> [!IMPORTANT]
> U kunt het toegangs eindpunt alleen selecteren tijdens het maken van ISE. deze optie kan later niet worden gewijzigd.

* **Intern**: met persoonlijke eind punten kunt u aan Logic apps in uw ISE aanroepen waar u invoer en uitvoer van Logic apps *alleen vanuit het virtuele netwerk*weer geven en er toegang toe hebt. Zorg ervoor dat u verbinding hebt met het netwerk tussen de privé-eind punten en de computer van waaruit u de geschiedenis van uitvoeringen wilt openen. Uw client computer kan bijvoorbeeld bestaan in het virtuele netwerk van de ISE of binnen een virtueel netwerk dat is verbonden met het virtuele netwerk van de ISE, bijvoorbeeld via peering of een virtueel particulier netwerk.

* **Extern**: open bare eind punten maken het mogelijk om Logic apps in uw ISE aan te roepen, waar u de invoer en uitvoer van de geschiedenis van Logic apps *van buiten uw virtuele netwerk*kunt bekijken en openen. Als u netwerk beveiligings groepen (Nsg's) gebruikt, moet u ervoor zorgen dat deze zijn ingesteld met regels voor binnenkomende verbindingen zodat de invoer en uitvoer van de uitvoerings geschiedenis toegankelijk zijn. Zie [Enable Access for ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)voor meer informatie.

Als u wilt bepalen of uw ISE een intern of extern toegangs punt gebruikt, selecteert u in het menu van uw ISE onder **instellingen**de optie **Eigenschappen**en gaat u naar de eigenschap **Access-eind punt** :

![ISE-toegangs punt zoeken](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integratie accounts met ISE

U kunt integratie accounts gebruiken met Logic apps binnen een Integration service Environment (ISE). Deze integratie accounts moeten echter *dezelfde ISE* gebruiken als de gekoppelde logische apps. Logic apps in een ISE kunnen alleen verwijzen naar de integratie accounts die zich in dezelfde ISE bevinden. Wanneer u een integratie account maakt, kunt u uw ISE als locatie voor uw integratie account selecteren. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over de prijzen en facturering voor integratie accounts met een ISE. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijs tarieven.

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Meer informatie over [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Meer informatie over de [integratie van virtuele netwerken voor Azure-Services](../virtual-network/virtual-network-for-azure-services.md)
