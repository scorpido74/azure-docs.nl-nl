---
title: Toegang instellen voor meerdere ISEs
description: Voor meerdere integratie service omgevingen (ISEs) kunt u één openbaar uitgaand IP-adres instellen voor toegang tot externe systemen vanaf Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: f3b422a55b7e2abbc8b1538183fd57fb234900d4
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792698"
---
# <a name="set-up-access-for-multiple-integration-service-environments-in-azure-logic-apps"></a>Toegang instellen voor meerdere integratie service omgevingen in Azure Logic Apps

Wanneer u met Azure Logic Apps werkt, kunt u een [ISE ( *Integration service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) instellen voor het hosten van logische apps die toegang nodig hebben tot bronnen in een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md). Als u meerdere ISE-exemplaren hebt die toegang nodig hebben tot andere eind punten met IP-beperkingen, implementeert u een [Azure firewall](../firewall/overview.md) of een [virtueel netwerk apparaat](../virtual-network/virtual-networks-overview.md#filter-network-traffic) in uw virtuele netwerk en stuurt u uitgaand verkeer via die firewall of het virtuele netwerk apparaat. U kunt vervolgens alle ISE-exemplaren in uw virtuele netwerk gebruiken om één, voorspelbaar en openbaar IP-adres te communiceren met behulp van doel systemen. Op die manier hoeft u geen aanvullende firewall-openingen op de doel systemen in te stellen voor elke ISE. In dit onderwerp wordt beschreven hoe u uitgaand verkeer via een Azure Firewall omleiden, maar u kunt soort gelijke concepten Toep assen op een virtueel netwerk apparaat, zoals een firewall van derden, vanuit de Azure Marketplace.

## <a name="prerequisites"></a>Vereisten

* Een Azure-firewall die wordt uitgevoerd in hetzelfde virtuele netwerk als uw ISE. Als u geen firewall hebt, moet u eerst [een subnet](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) met de naam `AzureFirewallSubnet` toevoegen aan uw virtuele netwerk. U kunt vervolgens [een firewall maken en implementeren](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) in uw virtuele netwerk.

* Een Azure- [route tabel](../virtual-network/manage-route-table.md). Als u er nog geen hebt, moet u eerst [een route tabel maken](../virtual-network/manage-route-table.md#create-a-route-table). Zie [virtueel netwerk verkeer routeren](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie over route ring.

## <a name="set-up-route-table"></a>Route tabel instellen

1. Selecteer in de [Azure Portal](https://portal.azure.com)de route tabel, bijvoorbeeld:

   ![Route tabel selecteren met regel voor het omleiden van uitgaand verkeer](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Als u [een nieuwe route wilt toevoegen](../virtual-network/manage-route-table.md#create-a-route), selecteert u in het menu route tabel **routes** > **toevoegen**.

   ![Route toevoegen voor het omleiden van uitgaand verkeer](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. Stel in het deel venster **route toevoegen** [de nieuwe route](../virtual-network/manage-route-table.md#create-a-route) in met een regel waarmee wordt aangegeven dat het uitgaande verkeer naar het doel systeem het volgende gedrag volgt:

   * Maakt gebruik van het [**virtuele apparaat**](../virtual-network/virtual-networks-udr-overview.md#user-defined) als het type van de volgende hop.

   * Gaat naar het privé-IP-adres voor de firewall instantie als het adres van de volgende hop.

     Als u dit IP-adres wilt zoeken, selecteert u in het menu Firewall de optie **overzicht**en zoekt u het adres onder **persoonlijk IP-adres**, bijvoorbeeld:

     ![Privé-IP-adres van firewall zoeken](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Hier volgt een voor beeld waarin wordt getoond hoe een dergelijke regel eruit kan zien:

   ![Regel voor het omleiden van uitgaand verkeer instellen](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Routenaam** | <*unieke route naam*> | Een unieke naam voor de route in de route tabel |
   | **Adresvoorvoegsel** | <*doel adres*> | Het doel systeem adres waar u het verkeer wilt laten lopen. Zorg ervoor dat u [CIDR-notatie (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) gebruikt voor dit adres. |
   | **Volgend hoptype** | **Virtueel apparaat** | Het [type hop](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) dat wordt gebruikt door uitgaand verkeer |
   | **Adres van volgende hop** | <*firewall-persoonlijk IP-adres*> | Het privé-IP-adres voor uw firewall |
   |||

## <a name="set-up-network-rule"></a>Netwerk regel instellen

1. Zoek en selecteer uw firewall in de Azure Portal. Selecteer in het menu Firewall onder **instellingen**de optie **regels**. Selecteer in het deel venster regels de optie **netwerk regel verzameling** > **netwerk regel verzameling toevoegen**.

   ![Netwerk regel verzameling toevoegen aan firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Voeg in de verzameling een regel toe die verkeer naar het doel systeem toestaat.

   Stel bijvoorbeeld dat u een logische app hebt die in een ISE wordt uitgevoerd en moet communiceren met een SFTP-systeem. U maakt een netwerk regel verzameling met de naam `LogicApp_ISE_SFTP_Outbound`, die een netwerk regel bevat met de naam `ISE_SFTP_Outbound`. Deze regel staat het verkeer toe van het IP-adres van elk subnet waar uw ISE wordt uitgevoerd in uw virtuele netwerk naar het doel-SFTP-systeem met behulp van het privé-IP-adres van uw firewall.

   ![Netwerk regel voor Firewall instellen](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Eigenschappen van de verzameling netwerk regels**

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*netwerk-regel-verzameling-naam*> | De naam voor uw netwerk regel verzameling |
   | **Prioriteit** | <*prioriteits niveau*> | De volg orde van prioriteit die moet worden gebruikt voor het uitvoeren van de regel verzameling. Zie [Wat zijn enkele Azure firewall concepten](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)? voor meer informatie. |
   | **Actie** | **Dat** | Het actie type dat moet worden uitgevoerd voor deze regel |
   |||

   **Eigenschappen van netwerk regel**

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Naam** | <*netwerk regel-naam*> | De naam voor uw netwerk regel |
   | **Protocol** | <*verbinding-protocollen*> | De verbindings protocollen die moeten worden gebruikt. Als u bijvoorbeeld NSG-regels gebruikt, selecteert u zowel **TCP** als **UDP**, niet alleen **TCP**. |
   | **Bron adressen** | <*ISE-subnet-adressen*> | Het subnet-IP-adres waar uw ISE wordt uitgevoerd en waar verkeer van uw logische app afkomstig is |
   | **Doel adressen** | <*doel-IP-adres*> | Het IP-adres van het doel systeem waar u het verkeer wilt laten lopen |
   | **Doel poorten** | <*doel poorten*> | Poorten die het doel systeem gebruikt voor binnenkomende communicatie |
   |||

   Zie de volgende artikelen voor meer informatie over netwerk regels:

   * [Een netwerk regel configureren](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Logica voor de verwerking van Azure Firewall regels](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Veelgestelde vragen over Azure Firewall](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: AZ Network Firewall Network-Rule](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met virtuele Azure-netwerken vanuit Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)