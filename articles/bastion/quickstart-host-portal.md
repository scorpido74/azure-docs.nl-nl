---
title: 'Snelstart: verbinding maken met een virtuele machine met een privé-IP-adres: Azure Bastion'
description: In dit artikel leert u hoe u een Azure Bastion-host maakt vanaf een virtuele machine en veilig verbinding maken met een privé-IP-adres.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 0b4f0a1fa4f9cce05aa544bb24e045209a6d24ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137455"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Snelstart: verbinding maken met een virtuele machine met een privé-IP-adres en Azure Bastion

In dit quickstart-artikel ziet u hoe u verbinding maken met een virtuele machine met behulp van een privé-IP-adres. Wanneer u verbinding maakt via Bastion, hebben uw virtuele machines geen openbaar IP-adres nodig. De stappen in dit artikel helpen u Bastion te implementeren in uw virtuele netwerk via uw virtuele machine in de portal. Zodra de service is ingericht, is de RDP/SSH-ervaring beschikbaar voor alle virtuele machines in hetzelfde virtuele netwerk.

## <a name="prerequisites"></a><a name="prereq"></a>Vereisten

* Een virtueel Azure-netwerk.
* Een Virtuele Azure-machine in het virtuele netwerk met poort 3389 open.

### <a name="example-values"></a>Voorbeeldwaarden

|**Naam** | **Waarde** |
| --- | --- |
| Name |  VNet1Bastion (VNET1Bastion) |
| Regio | eastus |
| Virtueel netwerk |  VNet1 |
| + Subnetnaam | AzureBastionSubnet |
| AzureBastionSubnet-adressen |  10.1.254.0/27 |
| Openbaar IP-adres |  Nieuwe maken |
| Naam openbaar IP-adres | Vnet1BastionPIP  |
| Openbaar IP-adres SKU |  Standard  |
| Toewijzing  | Statisch |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Een Bastion-host maken

Wanneer u een bastionhost in de portal maakt met behulp van een bestaande virtuele machine, komen verschillende instellingen automatisch overeen met uw virtuele machine en/of virtueel netwerk.

1. Open de [Azure Portal](https://portal.azure.com). Ga naar uw virtuele machine en klik op **Verbinding maken**.

   ![instellingen voor virtuele machines](./media/quickstart-host-portal/vm-settings.png)
1. Selecteer **Bastion**in de vervolgkeuzelijst.
1. Selecteer **bastion gebruiken**op de pagina Verbinding .

   ![Bastion selecteren](./media/quickstart-host-portal/select-bastion.png)

1. Vul op de pagina Bastion de volgende instellingenvelden in:

   * **Naam:** Naam van de bastionhost
   * **Subnet**: Het subnet in uw virtuele netwerk waarop Bastion-bron wordt geïmplementeerd. Het subnet moet worden gemaakt met de naam **AzureBastionSubnet**. Met de naam weet Azure naar welk subnet de Bastion-bron moet worden geïmplementeerd. Dit is anders dan een Gateway-subnet. Gebruik een subnet van ten minste /27 of groter (/27, /26, /25, enzovoort).
   
      * Selecteer **Subnetconfiguratie beheren**en selecteer **+ Subnet**.
      * Typ **AzureBastionSubnet**op de subnetpagina Toevoegen .
      * Geef het adresbereik op in CIDR-notatie. Bijvoorbeeld 10.1.254.0/27.
      * Selecteer **OK** om het subnet te maken. Navigeer boven aan de pagina terug naar Bastion om de rest van de instellingen te voltooien.

         ![navigeren naar bastion-instellingen](./media/quickstart-host-portal/navigate-bastion.png)
   * **Openbaar IP-adres**: Dit is het openbare IP-adres van de Bastion-bron waarop RDP/SSH zal worden geopend (via poort 443). Maak een nieuw openbaar IP-adres of gebruik een bestaand IP-adres. Het openbare IP-adres moet zich in dezelfde regio bevinden als de Bastion-bron die u maakt.
   * **Naam van het openbare IP-adres**: de naam van de bron van het openbare IP-adres.
1. Klik op het validatiescherm op **Maken**. Wacht ongeveer 5 minuten op het maken en implementeren van de Bastion-bron.

   ![bastionhost maken](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Verbinding maken

Nadat Bastion is geïmplementeerd in het virtuele netwerk, verandert het scherm in de verbindingspagina.

1. Typ de gebruikersnaam en het wachtwoord voor uw virtuele machine. Selecteer vervolgens **Verbinding maken**.

   ![verbinding maken](./media/quickstart-host-portal/connect.png)
1. De RDP-verbinding met deze virtuele machine via Bastion wordt rechtstreeks geopend in de Azure-portal (via HTML5) met poort 443 en de Bastion-service.

   ![RDP-verbinding](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het virtuele netwerk en de virtuele machines, verwijdert u de brongroep en alle bronnen die deze bevat:

1. Voer *TestRG1* in het vak **Zoeken** boven aan de portal in en selecteer **TestRG1** in de zoekresultaten.

2. Selecteer **Resourcegroep verwijderen**.

3. Typ *TestRG1* voor **TYPE DE NAAM VAN DE BRONGROEP** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Bastion-host voor uw virtuele netwerk gemaakt en vervolgens veilig verbonden met een virtuele machine via de Bastion-host.

* Lees het [Bastion-overzicht](bastion-overview.md) en de [veelgestelde vragen over Bastion voor](bastion-faq.md)meer informatie over Azure Bastion.
* Zie [Werken met NSGs](bastion-nsg.md)als u netwerkbeveiligingsgroepen wilt gebruiken met het subnet Azure Bastion.
* Zie De [zelfstudie](bastion-create-host-portal.md)voor instructies met uitleg over azure bastion-hostinstellingen.
* Zie Verbinding maken met een [virtuele machineschaalset met Azure Bastion](bastion-connect-vm-scale-set.md)als u verbinding wilt maken met een virtuele machineschaalset.