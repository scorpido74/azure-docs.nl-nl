---
title: Toegang tot Azure Virtual Networks
description: Overzicht van de manier waarop Integration service environments (ISEs) Logic apps toegang hebben tot Azure Virtual Networks (VNETs)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 12/16/2019
ms.openlocfilehash: d6bb57c8163f7653f4b10142d7ec2b34f50456f1
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75527855"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Toegang tot Azure Virtual Network resources vanuit Azure Logic Apps met behulp van integratie service omgevingen (ISEs)

Soms hebben uw Logic apps en integratie accounts toegang nodig tot beveiligde bronnen, zoals virtuele machines (Vm's) en andere systemen of services die zich in een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md)bevinden. Als u deze toegang wilt instellen, kunt u [een ISE ( *Integration service Environment* ) maken](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) waarin u uw Logic apps kunt uitvoeren en uw integratie accounts maakt.

Wanneer u een ISE maakt, *injecteert* Azure die ISE in uw virtuele Azure-netwerk, dat vervolgens een privé-en geïsoleerde instantie van de Logic apps-service implementeert in uw virtuele Azure-netwerk. Dit privé-exemplaar maakt gebruik van speciale resources zoals opslag en wordt afzonderlijk uitgevoerd vanuit de open bare, ' wereld wijde ' multi tenant-Logic Apps service. Het scheiden van uw geïsoleerde privé-exemplaar en het open bare Global-exemplaar verminderen ook de invloed die andere Azure-tenants mogelijk hebben op de prestaties van uw apps, ook wel bekend als het [effect "ruiserende neighbors"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Een ISE biedt u ook uw eigen vaste IP-adressen. Deze IP-adressen zijn gescheiden van de statische IP-adressen die worden gedeeld door de Logic apps in de open bare multi tenant-service.

Nadat u uw ISE hebt gemaakt, kunt u uw ISE als de logische app of de locatie van het integratie account selecteren wanneer u de logische app of het integratie account gaat maken:

![Integratie service omgeving selecteren](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Uw logische app kan nu rechtstreeks toegang krijgen tot systemen die zich binnen of verbonden zijn met uw virtuele netwerk door gebruik te maken van een van de volgende items:

* Een **ISE**-gelabelde connector voor dat systeem
* Een **kern**, ingebouwde trigger of actie, zoals de http-trigger of actie
* Een aangepaste connector

In dit overzicht vindt u meer informatie over hoe een ISE uw Logic apps en integratie accounts direct toegang geeft tot uw virtuele Azure-netwerk en de verschillen tussen een ISE en de Global Logic Apps-service vergelijkt.

> [!IMPORTANT]
> Logic apps, ingebouwde triggers, ingebouwde acties en connectors die worden uitgevoerd in uw ISE, gebruiken een prijs plan dat verschilt van het prijs plan op basis van verbruik. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over de prijzen en facturerings werkzaamheden voor ISEs. Zie [Logic apps prijzen](../logic-apps/logic-apps-pricing.md)voor prijs tarieven.
>
> Uw ISE heeft ook verhoogde limieten voor de duur van de uitvoering, opslag retentie, door Voer, HTTP-aanvraag-en reactie-time-outs, bericht grootten en aangepaste connector aanvragen. 
> Zie [limieten en configuratie voor Azure Logic apps](logic-apps-limits-and-config.md)voor meer informatie.

<a name="difference"></a>

## <a name="isolated-versus-global"></a>Geïsoleerd versus wereld wijd

Wanneer u een geïntegreerde service omgeving (ISE) in azure maakt, kunt u het virtuele Azure-netwerk selecteren waar u uw ISE wilt *injecteren* . Azure voert vervolgens een privé-exemplaar van de Logic Apps-service in uw virtuele netwerk in of implementeert deze. Met deze actie wordt een geïsoleerde omgeving gemaakt waarin u uw Logic apps op toegewezen resources kunt maken en uitvoeren. Wanneer u uw logische app maakt, selecteert u uw ISE als de locatie van uw app, waardoor uw logische app direct toegang heeft tot uw virtuele netwerk en de bronnen in dat netwerk.

Logic apps in een ISE bieden dezelfde gebruikers ervaringen en vergelijk bare mogelijkheden als de algemene Logic Apps service. U kunt niet alleen dezelfde ingebouwde triggers, ingebouwde acties en connectors van de Global Logic Apps-service gebruiken, maar u kunt ook ISE-specifieke connectors gebruiken. Dit zijn bijvoorbeeld enkele standaard connectoren die versies aanbieden die in een ISE worden uitgevoerd:

* Azure Blob Storage, File Storage en Table Storage
* Azure-wacht rijen, Azure Service Bus, Azure Event Hubs en IBM MQ
* FTP en SFTP-SSH
* SQL Server, Azure SQL Data Warehouse Azure Cosmos DB
* AS2, X12 en EDIFACT

Het verschil tussen ISE en niet-ISE-connectors bevindt zich op de locaties waar de triggers en acties worden uitgevoerd:

* In uw ISE worden ingebouwde triggers en acties, zoals HTTP, altijd uitgevoerd in dezelfde ISE als uw logische app en wordt het **kern** label weer gegeven.

  ![Selecteer kern ingebouwde triggers en acties](./media/connect-virtual-network-vnet-isolated-environment-overview/select-core-built-in-actions-triggers.png)

* Connectors die worden uitgevoerd in een ISE hebben openbaar gehoste versies die beschikbaar zijn in de Global Logic Apps service. Voor connectors die twee versies aanbieden, worden connectors met het label **ISE** altijd uitgevoerd in dezelfde ISE als uw logische app. Connectors zonder het label **ISE** worden uitgevoerd in de service globale Logic apps.

  ![ISE-connectors selecteren](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

Een ISE biedt ook verhoogde limieten voor de duur van de uitvoering, opslag retentie, door Voer, HTTP-aanvraag-en reactie-time-outs, bericht grootten en aangepaste connector aanvragen. Zie [limieten en configuratie voor Azure Logic apps](logic-apps-limits-and-config.md)voor meer informatie.

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE Sku's

Wanneer u uw ISE maakt, kunt u de Developer SKU of Premium SKU selecteren. Dit zijn de verschillen tussen deze Sku's:

* **Developer**

  Biedt een voordelige ISE die u kunt gebruiken voor experimenteren, ontwikkelen en testen, maar niet voor productie-of prestatie testen. De Developer SKU bevat ingebouwde triggers en acties, standaard connectors, zakelijke connectors en één [gratis laag](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integratie account voor een vaste maandelijkse prijs. Deze SKU bevat echter geen SLA (Service Level Agreement), opties voor het schalen van de capaciteit of redundantie tijdens het recyclen, wat betekent dat u vertragingen of downtime mogelijk ondervindt.

* **Premium**

  Biedt een ISE die u kunt gebruiken voor productie en die SLA-ondersteuning, ingebouwde triggers en acties, standaard connectors, zakelijke connectors, een [standaard-laag](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) integratie account, opties voor het schalen van de capaciteit en redundantie tijdens het recyclen van een vaste maandelijkse prijs.

> [!IMPORTANT]
> De SKU-optie is alleen beschikbaar bij het maken van ISE en kan later niet worden gewijzigd.

Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijs tarieven. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over de prijzen en facturerings werkzaamheden voor ISEs.

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Toegang tot ISE-eind punt

Wanneer u uw ISE maakt, kunt u kiezen of u interne of Externe toegangs punten wilt gebruiken. Uw selectie bepaalt of aanvragen of webhooks worden geactiveerd op Logic apps in uw ISE kan aanroepen ontvangen van buiten uw virtuele netwerk.

Deze eind punten zijn ook van invloed op de manier waarop u toegang tot invoer en uitvoer kunt krijgen in de uitvoerings geschiedenis van de Logic apps.

* **Intern**: privé-eind punten die aanroepen naar Logic apps in uw ISE waar u de invoer en uitvoer van uw logische apps in de uitvoerings geschiedenis *alleen vanuit uw virtuele netwerk* kunt weer geven en openen

* **Externe**: open bare eind punten die aanroepen naar Logic apps in uw ISE waar u de invoer en uitvoer van uw Logic apps kunt bekijken en openen in de uitvoerings geschiedenis *van buiten uw virtuele netwerk*. Als u netwerk beveiligings groepen (Nsg's) gebruikt, moet u ervoor zorgen dat deze zijn ingesteld met regels voor binnenkomende verbindingen zodat de invoer en uitvoer van de uitvoerings geschiedenis toegankelijk zijn. Zie [Enable Access for ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)voor meer informatie.

> [!IMPORTANT]
> De optie voor het toegangs punt is alleen beschikbaar bij het maken van ISE en kan later niet worden gewijzigd.

<a name="on-premises"></a>

## <a name="access-to-on-premises-data-sources"></a>Toegang tot on-premises gegevens bronnen

Voor on-premises systemen die zijn verbonden met een virtueel Azure-netwerk, wordt een ISE in dat netwerk geïnjecteerd zodat uw Logic apps rechtstreeks toegang hebben tot deze systemen met behulp van een van deze items:

* HTTP-actie

* ISE-gelabelde connector voor dat systeem

  > [!NOTE]
  > Als u Windows-verificatie wilt gebruiken met de SQL Server-connector in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), gebruikt u de niet-ISE-versie van de connector met de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-install.md). De ISE-label versie biedt geen ondersteuning voor Windows-verificatie.

* Aangepaste connector

  * Als u aangepaste connectors hebt die de on-premises gegevens gateway nodig hebben en u deze connectors buiten een ISE hebt gemaakt, kunnen logische apps in een ISE ook deze connectors gebruiken.
  
  * Aangepaste connectors die zijn gemaakt in een ISE werken niet met de on-premises gegevens gateway. Deze connectors hebben echter rechtstreeks toegang tot on-premises gegevens bronnen die zijn verbonden met het virtuele netwerk dat als host fungeert voor de ISE. Logic apps in een ISE hebben daarom waarschijnlijk niet de gegevens gateway nodig bij het communiceren met deze resources.

Voor on-premises systemen die niet zijn verbonden met een virtueel netwerk of geen ISE-labled-connectors hebben, moet u eerst [de on-premises gegevens gateway instellen](../logic-apps/logic-apps-gateway-install.md) voordat uw Logic apps verbinding met deze systemen kunnen maken.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integratie accounts met ISE

U kunt integratie accounts gebruiken met Logic apps binnen een Integration service Environment (ISE). Deze integratie accounts moeten echter *dezelfde ISE* gebruiken als de gekoppelde logische apps. Logic apps in een ISE kunnen alleen verwijzen naar de integratie accounts die zich in dezelfde ISE bevinden. Wanneer u een integratie account maakt, kunt u uw ISE als locatie voor uw integratie account selecteren. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over de prijzen en facturering voor integratie accounts met een ISE. Zie [Logic apps prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijs tarieven.

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met virtuele netwerken van Azure vanuit geïsoleerde Logic apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Artefacten toevoegen aan integratie service omgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integratie service omgevingen beheren](../logic-apps/ise-manage-integration-service-environment.md)
* Meer informatie over [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Meer informatie over de [integratie van virtuele netwerken voor Azure-Services](../virtual-network/virtual-network-for-azure-services.md)
