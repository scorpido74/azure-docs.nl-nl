---
title: Een openbaar uitgaand IP-adres instellen voor ISE's
description: Meer informatie over het instellen van één openbaar verouderd IP-adres voor integratieserviceomgevingen (ISEs) in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191503"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Eén IP-adres instellen voor een of meer integratieserviceomgevingen in Azure Logic Apps

Wanneer u met Azure Logic Apps werkt, u een [ *integratieserviceomgeving* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) instellen voor het hosten van logische apps die toegang nodig hebben tot bronnen in een [virtueel Azure-netwerk.](../virtual-network/virtual-networks-overview.md) Wanneer u meerdere ISE-exemplaren hebt die toegang nodig hebben tot andere eindpunten met IP-beperkingen, implementeert u een [Azure Firewall](../firewall/overview.md) of een [virtueel netwerktoestel](../virtual-network/virtual-networks-overview.md#filter-network-traffic) in uw virtuele netwerk en routeert u uitgaand verkeer via dat firewall of virtuele netwerktoestel. U vervolgens alle ISE-exemplaren in uw virtuele netwerk één openbaar, statisch en voorspelbaar IP-adres laten gebruiken om te communiceren met doelsystemen. Op die manier hoeft u geen extra firewallopeningen in te stellen op die bestemmingssystemen voor elke ISE.

In dit onderwerp wordt uitgelegd hoe u uitgaand verkeer routeren via een Azure Firewall, maar u vergelijkbare concepten toepassen op een virtueel netwerktoestel, zoals een firewall van derden vanuit de Azure Marketplace. Hoewel dit onderwerp zich richt op het instellen voor meerdere ISE-exemplaren, u deze benadering ook gebruiken voor één ISE wanneer uw scenario het aantal IP-adressen dat toegang nodig heeft, moet beperken. Overweeg of de extra kosten voor de firewall of het virtuele netwerktoestel zinvol zijn voor uw scenario. Meer informatie over [azure firewall-prijzen](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Vereisten

* Een Azure-firewall die wordt uitgevoerd in hetzelfde virtuele netwerk als uw ISE. Als u geen firewall hebt, voegt u eerst een `AzureFirewallSubnet` [subnet toe](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) dat is vernoemd naar uw virtuele netwerk. U vervolgens [een firewall maken en implementeren](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) in uw virtuele netwerk.

* Een [Azure-routetabel](../virtual-network/manage-route-table.md). Als u er geen hebt, [maakt u eerst een routetabel.](../virtual-network/manage-route-table.md#create-a-route-table) Zie [Routering van virtueel netwerkverkeer](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie over routering.

## <a name="set-up-route-table"></a>Routetabel instellen

1. Selecteer in de [Azure-portal](https://portal.azure.com)de routetabel, bijvoorbeeld:

   ![Routetabel selecteren met regel voor het aansturen van uitgaand verkeer](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Als u een nieuwe route wilt [toevoegen,](../virtual-network/manage-route-table.md#create-a-route)selecteert u Routes**toevoegen**in het menu **van** > de routetabel .

   ![Route toevoegen voor het aansturen van uitgaand verkeer](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. Stel **in** het deelvenster Route toevoegen [de nieuwe route in](../virtual-network/manage-route-table.md#create-a-route) met een regel die aangeeft dat al het uitgaande verkeer naar het doelsysteem dit gedrag volgt:

   * Gebruikt het [**virtuele toestel**](../virtual-network/virtual-networks-udr-overview.md#user-defined) als het volgende hoptype.

   * Gaat naar het privé-IP-adres voor de firewall-instantie als het volgende hopadres.

     Als u dit IP-adres wilt vinden, selecteert u **Overzicht**, zoek het adres onder **Privé-IP-adres,** bijvoorbeeld:

     ![Firewall privé-IP-adres zoeken](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Hier is een voorbeeld dat laat zien hoe een dergelijke regel eruit zou kunnen zien:

   ![Regel instellen voor het aansturen van uitgaand verkeer](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Routenaam** | <*unieke routenaam*> | Een unieke naam voor de route in de routetabel |
   | **Adresvoorvoegsel** | <*bestemmingsadres*> | Het adres van het bestemmingssysteem waar u het verkeer naartoe wilt brengen. Zorg ervoor dat u [cidr-notatie (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) gebruikt voor dit adres. |
   | **Volgend hoptype** | **Virtueel toestel** | Het [hoptype](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) dat wordt gebruikt door uitgaand verkeer |
   | **Adres van de volgende hop** | <*firewall-privé-IP-adres*> | Het privé-IP-adres voor uw firewall |
   |||

## <a name="set-up-network-rule"></a>Netwerkregel instellen

1. Zoek en selecteer uw firewall in de Azure-portal. Selecteer In het firewallmenu onder **Instellingen**de optie **Regels**. Selecteer in het deelvenster Regels de optie **Netwerkregelverzameling** > **Netwerkregelverzameling Toevoegen.**

   ![Verzameling van netwerkregels toevoegen aan firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Voeg in de verzameling een regel toe die verkeer naar het doelsysteem toestaat.

   Stel dat u een logische app hebt die in een ISE wordt uitgevoerd en moet communiceren met een SFTP-systeem. U maakt een netwerkregelverzameling `LogicApp_ISE_SFTP_Outbound`met de naam , `ISE_SFTP_Outbound`die een netwerkregel met de naam bevat . Deze regel maakt verkeer mogelijk vanaf het IP-adres van een subnet waar uw ISE in uw virtuele netwerk naar het sftp-doelsysteem draait met behulp van het privé-IP-adres van uw firewall.

   ![Netwerkregel instellen voor firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Eigenschappen voor het verzamelen van netwerkregels**

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*naam netwerkregel-verzameling*> | De naam voor uw netwerkregelverzameling |
   | **Prioriteit** | <*prioriteitsniveau*> | De volgorde van prioriteit die moet worden gebruikt voor het uitvoeren van de regelverzameling. Zie [Wat zijn enkele Azure Firewall-concepten](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)voor meer informatie? |
   | **Actie** | **Toestaan** | Het actietype dat voor deze regel moet worden uitgevoerd |
   |||

   **Eigenschappen van netwerkregel**

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*naam netwerkregel*> | De naam voor uw netwerkregel |
   | **Protocol** | <*verbindingsprotocollen*> | De verbindingsprotocollen die u moet gebruiken. Als u bijvoorbeeld NSG-regels gebruikt, selecteert u zowel **TCP** als **UDP,** niet alleen **TCP**. |
   | **Bronadressen** | <*ISE-subnetadressen*> | De subnet IP-adressen waar uw ISE wordt uitgevoerd en waar het verkeer van uw logica-app vandaan komt |
   | **Bestemmingsadressen** | <*bestemming-IP-adres*> | Het IP-adres voor uw bestemmingssysteem waar u het verkeer naartoe wilt |
   | **Bestemmingspoorten** | <*bestemmingspoorten*> | Alle poorten die uw bestemmingssysteem gebruikt voor binnenkomende communicatie |
   |||

   Zie de volgende artikelen voor meer informatie over netwerkregels:

   * [Een netwerkregel configureren](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Regels voor de logicaverwerking in Azure Firewall](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Veelgestelde vragen over Azure Firewall](../firewall/firewall-faq.md)
   * [Azure PowerShell: nieuwe azfirewallnetwerkregel](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: netwerknetwerkregel AZ-netwerk](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)