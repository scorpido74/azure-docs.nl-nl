---
title: Toegang tot virtuele Azure-netwerken
description: Overzicht over hoe integratieserviceomgevingen (ISE's) logische apps helpen toegang te krijgen tot Virtuele Azure-netwerken (VNET's)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: 9d5e0c088fe773f16e1fc57f292ca812906aa09c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127245"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Toegang tot Azure Virtual Network-bronnen vanuit Azure Logic Apps met behulp van integratieserviceomgevingen (ISE's)

Soms hebben uw logische apps toegang nodig tot beveiligde bronnen, zoals virtuele machines (VM's) en andere systemen of services die zich binnen een [virtueel Azure-netwerk bevinden.](../virtual-network/virtual-networks-overview.md) Als u deze toegang wilt instellen, u [een *integratieserviceomgeving* (ISE) maken.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) Een ISE is een geïsoleerd exemplaar van de Logic Apps-service die gebruik maakt van speciale resources en afzonderlijk wordt uitgevoerd van de 'globale' Logic Apps-service met meerdere tenant.

Het uitvoeren van logische apps in uw eigen afzonderlijke geïsoleerde instantie helpt de impact te verminderen die andere Azure-tenants kunnen hebben op de prestaties van uw apps, ook wel het [effect 'luidruchtige buren' genoemd.](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors) Een ISE biedt ook deze voordelen:

* Uw eigen statische IP-adressen, die los staan van de statische IP-adressen die worden gedeeld door de logische apps in de multi-tenantservice. U ook één openbaar, statisch en voorspelbaar uitgaand IP-adres instellen om te communiceren met bestemmingssystemen. Op die manier hoeft u geen extra firewallopeningen in te stellen op die bestemmingssystemen voor elke ISE.

* Verhoogde limieten voor runsduur, opslagretentie, doorvoer, HTTP-aanvraag- en antwoordtime-outs, berichtformaten en aangepaste verbindingsaanvragen. Zie [Limieten en configuratie voor Azure Logic Apps voor](logic-apps-limits-and-config.md)meer informatie.

> [!NOTE]
> Sommige Azure-virtuele netwerken gebruiken privéeindpunten[(Azure Private Link)](../private-link/private-link-overview.md)voor het bieden van toegang tot Azure PaaS-services, zoals Azure Storage, Azure Cosmos DB of Azure SQL Database, partnerservices of klantenservicedie op Azure worden gehost. Als uw logische apps toegang nodig hebben tot virtuele netwerken die privéeindpunten gebruiken, moet u deze logische apps in een ISE maken, implementeren en uitvoeren.

Wanneer u een ISE maakt, *injecteert* of implementeert Azure die ISE in uw virtuele Azure-netwerk. U deze ISE vervolgens gebruiken als locatie voor de logische apps en integratieaccounts die toegang nodig hebben.

![Integratieserviceomgeving selecteren](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Logische apps hebben toegang tot bronnen die zich binnen of verbonden zijn met uw virtuele netwerk met behulp van deze items, die in dezelfde ISE worden uitgevoerd als uw logische apps:

* Een ingebouwde trigger of actie met het label **CORE,** zoals de HTTP-trigger of actie
* Een **ISE-gelabelde**connector voor dat systeem of die service
* Een aangepaste connector

U nog steeds connectors gebruiken die niet het **CORE-** of **ISE-label** hebben met de logische apps in uw ISE. Deze connectors worden uitgevoerd in de logic-apps-service met meerdere frames. Zie voor meer informatie de volgende secties:

* [Geïsoleerd versus multi-tenant](#difference)
* [Verbinding maken vanuit een integratieserviceomgeving](../connectors/apis-list.md#integration-service-environment)
* [ISE-connectoren](../connectors/apis-list.md#ise-connectors)

> [!IMPORTANT]
> Logische apps, ingebouwde triggers, ingebouwde acties en connectors die in uw ISE worden uitgevoerd, gebruiken een prijsplan dat verschilt van het op verbruik gebaseerde prijsplan. Zie [Prijsmodel Logische Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie . Zie [Logic Apps-prijzen](../logic-apps/logic-apps-pricing.md)voor prijsdetails.

In dit overzicht wordt meer informatie beschreven over hoe een ISE uw logische apps directe toegang geeft tot uw virtuele Azure-netwerk en de verschillen tussen een ISE en de logic-apps-service met meerdere tenant's vergelijkt.

<a name="difference"></a>

## <a name="isolated-versus-multi-tenant"></a>Geïsoleerd versus multi-tenant

Wanneer u logische apps maakt en uitvoert in een ISE, krijgt u dezelfde gebruikerservaringen en vergelijkbare mogelijkheden als de logica-apps-service met meerdere tenantn. U dezelfde ingebouwde triggers, acties en beheerde connectors gebruiken die beschikbaar zijn in de logica-apps-service met meerdere tenantn. Sommige beheerde connectors bieden extra ISE-versies. Het verschil tussen ISE-connectors en niet-ISE-connectors bestaat in de plaats waar ze worden uitgevoerd en de labels die ze hebben in de Logic App Designer wanneer u binnen een ISE werkt.

![Connectoren met en zonder labels in een ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Ingebouwde triggers en acties geven het **CORE-label** weer. Ze draaien altijd in dezelfde ISE als uw logica-app. Beheerde connectors met het **ISE-label** worden ook uitgevoerd in dezelfde ISE als uw logische app.

  Hier volgen bijvoorbeeld enkele connectors die ISE-versies aanbieden:

  * Azure Blob-opslag, bestandsopslag en tabelopslag
  * Azure-wachtrijen, Azure-servicebus, Azure-gebeurtenishubs en IBM MQ
  * FTP en SFTP-SSH
  * SQL Server, Azure SQL Data Warehouse, Azure Cosmos DB
  * AS2, X12 en EDIFACT

* Beheerde connectors die geen extra labels weergeven, worden altijd uitgevoerd in de Logic Apps-service met meerdere tenantn, maar u deze connectors nog steeds gebruiken in een door ISE gehoste logica-app.

<a name="on-premises"></a>

### <a name="access-to-on-premises-systems"></a>Toegang tot on-premises systemen

Om toegang te krijgen tot on-premises systemen of gegevensbronnen die zijn verbonden met een virtueel Azure-netwerk, kunnen logische apps in een ISE deze items gebruiken:

* HTTP-actie

* ISE-gelabelde connector voor dat systeem

  > [!NOTE]
  > Als u Windows-verificatie wilt gebruiken met de SQL Server-connector in een [integratieserviceomgeving (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt u de niet-ISE-versie van de connector met de [on-premises gegevensgateway.](../logic-apps/logic-apps-gateway-install.md) De versie met ISE-label biedt geen ondersteuning voor Windows-verificatie.

* Aangepaste connector

  * Als u aangepaste connectors hebt waarvoor de on-premises gegevensgateway vereist is, en u deze connectors buiten een ISE hebt gemaakt, kunnen logische apps in een ISE deze connectors ook gebruiken.

  * Aangepaste connectors die in een ISE zijn gemaakt, werken niet met de on-premises gegevensgateway. Deze connectors hebben echter rechtstreeks toegang tot on-premises gegevensbronnen die zijn verbonden met het virtuele netwerk dat het ISE host. Logische apps in een ISE hebben de gegevensgateway dus waarschijnlijk niet nodig wanneer ze met die bronnen communiceren.

Voor on-premises systemen die niet zijn verbonden met een virtueel netwerk of die geen ISE-labelconnectors hebben, moet u eerst [de on-premises gegevensgateway instellen](../logic-apps/logic-apps-gateway-install.md) voordat uw logische apps verbinding kunnen maken met die systemen.

<a name="ise-level"></a>

## <a name="ise-skus"></a>ISE SKU's

Wanneer u uw ISE maakt, u de SKU voor ontwikkelaars of Premium SKU selecteren. Hier zijn de verschillen tussen deze SKU's:

* **Developer**

  Biedt een goedkopere ISE die u gebruiken voor experimenten, ontwikkeling en testen, maar niet voor productie- of prestatietests. De Developer SKU bevat ingebouwde triggers en acties, standaardconnectors, Enterprise-connectors en één [gratis integratieaccount](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) voor een vaste maandelijkse prijs. Deze SKU bevat echter geen service-level agreement (SLA), opties voor het opschalen van capaciteit of redundantie tijdens recycling, wat betekent dat u vertragingen of downtime ondervinden.

* **Premium**

  Biedt een ISE die u gebruiken voor productie en inclusief SLA-ondersteuning, ingebouwde triggers en acties, standaardconnectors, Enterprise-connectors, één [standaardintegratieaccount,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) opties voor het opschalen van de capaciteit en redundantie tijdens recycling voor een vaste maandelijkse prijs.

> [!IMPORTANT]
> De SKU-optie is alleen beschikbaar bij ISE-creatie en kan later niet worden gewijzigd.

Zie [Logic Apps-prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijstarieven. Zie het [prijsmodel van Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over hoe prijzen en facturering werken voor ISE's.

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>ISE-eindpunttoegang

Wanneer u uw ISE maakt, u ervoor kiezen om interne of externe toegangspunten te gebruiken. Uw selectie bepaalt of aanvragen of webhook-triggers op logische apps in uw ISE oproepen van buiten uw virtuele netwerk kunnen ontvangen.

Deze eindpunten hebben ook invloed op de manier waarop u toegang hebt tot ingangen en uitvoer in de rungeschiedenis van uw logische apps.

* **Intern:** privéeindpunten waarmee oproepen naar logische apps in uw ISE kunnen worden aangeschreven, waar u de ingangen en uitvoer van uw logische apps in de rungeschiedenis *alleen vanuit uw virtuele netwerk* bekijken en openen

* **Extern:** openbare eindpunten waarmee oproepen naar logische apps in uw ISE kunnen worden aangeschreven, waar u de ingangen en uitvoer van uw logische apps in de rungeschiedenis *van buiten uw virtuele netwerk*bekijken en openen. Als u netwerkbeveiligingsgroepen (NSG's) gebruikt, controleert u of ze zijn ingesteld met binnenkomende regels om toegang te krijgen tot de invoer en uitvoer van de run-geschiedenis. Zie [Toegang inschakelen voor ISE voor](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)meer informatie .

> [!IMPORTANT]
> De access endpoint-optie is alleen beschikbaar bij ISE-creatie en kan later niet worden gewijzigd.

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Integratieaccounts met ISE

U integratieaccounts gebruiken met logische apps in een integratieserviceomgeving (ISE). Deze integratieaccounts moeten echter *dezelfde ISE* gebruiken als de gekoppelde logische apps. Logische apps in een ISE kunnen alleen verwijzen naar de integratieaccounts die zich in dezelfde ISE bevinden. Wanneer u een integratieaccount maakt, u uw ISE selecteren als locatie voor uw integratieaccount. Zie het [prijsmodel van Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over hoe prijzen en facturering werken voor integratieaccounts met een ISE. Zie [Logic Apps-prijzen](https://azure.microsoft.com/pricing/details/logic-apps/)voor prijstarieven.

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Meer informatie over [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Meer informatie over [virtuele netwerkintegratie voor Azure-services](../virtual-network/virtual-network-for-azure-services.md)
