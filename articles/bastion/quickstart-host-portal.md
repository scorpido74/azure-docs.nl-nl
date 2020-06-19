---
title: 'Quick Start: verbinding maken met een virtuele machine met behulp van een privé-IP-adres: Azure Bastion'
description: In dit artikel leert u hoe u een Azure bastion-host maakt op basis van een virtuele machine en veilig verbinding maakt met behulp van een privé-IP-adres.
services: bastion
author: charwen
ms.service: bastion
ms.topic: quickstart
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: 3c2780c8c99fd4568a7213b625ce785d3a99129c
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84743980"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Quick Start: verbinding maken met een virtuele machine met behulp van een privé-IP-adres en Azure Bastion

In dit Quick Start-artikel ziet u hoe u verbinding kunt maken met een virtuele machine met behulp van een privé-IP-adres. Wanneer u verbinding maakt via Bastion, hebt u geen openbaar IP-adres nodig voor uw virtuele machines. Met de stappen in dit artikel kunt u Bastion implementeren in uw virtuele netwerk via de virtuele machine in de portal. Zodra de service is ingericht, is de RDP/SSH-ervaring beschikbaar voor alle virtuele machines in hetzelfde virtuele netwerk.

## <a name="prerequisites"></a><a name="prereq"></a>Vereisten

* Een Azure Virtual Network.
* Een virtuele machine van Azure die zich in het virtuele netwerk bevindt en poort 3389 is geopend.

### <a name="example-values"></a>Voorbeeldwaarden

|**Naam** | **Waarde** |
| --- | --- |
| Naam |  VNet1Bastion |
| Regio | eastus |
| Virtueel netwerk |  VNet1 |
| + Subnetnaam | AzureBastionSubnet |
| AzureBastionSubnet-adressen |  10.1.254.0/27 |
| Openbaar IP-adres |  Nieuwe maken |
| Naam openbaar IP-adres | VNet1BastionPIP  |
| SKU openbaar IP-adres |  Standard  |
| Toewijzing  | Statisch |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Een bastion-host maken

Wanneer u een bastion-host in de portal maakt met behulp van een bestaande virtuele machine, worden verschillende instellingen automatisch standaard in overeenstemming met uw virtuele machine en/of virtuele netwerk.

1. Open [Azure Portal](https://portal.azure.com). Ga naar uw virtuele machine en klik vervolgens op **verbinding maken**.

   ![instellingen voor virtuele machines](./media/quickstart-host-portal/vm-settings.png)
1. Selecteer **Bastion**in de vervolg keuzelijst.
1. Selecteer op de pagina verbinding maken de optie **Bastion gebruiken**.

   ![Bastion selecteren](./media/quickstart-host-portal/select-bastion.png)

1. Vul op de pagina Bastion de volgende instellingen in:

   * **Naam**: naam van de bastion-host
   * **Subnet**: het subnet in het virtuele netwerk waarop de Bastion-resource wordt geïmplementeerd. Het subnet moet worden gemaakt met de naam **AzureBastionSubnet**. Met de naam kan Azure weten met welk subnet de Bastion-resource moet worden geïmplementeerd. Dit wijkt af van een gateway-subnet. Gebruik een subnet van Mini maal/27 of groter (/27,/26,/25, enzovoort).
   
      * Selecteer **subnet-configuratie beheren**en selecteer vervolgens **+ subnet**.
      * Op de pagina subnet toevoegen typt u **AzureBastionSubnet**.
      * Geef het adres bereik in de CIDR-notatie op. Bijvoorbeeld 10.1.254.0/27.
      * Selecteer **OK** om het subnet te maken. Ga aan de bovenkant van de pagina terug naar Bastion om de rest van de instellingen te volt ooien.

         ![navigeren naar Bastion-instellingen](./media/quickstart-host-portal/navigate-bastion.png)
   * **Openbaar IP-adres**: dit is de open bare IP van de Bastion-bron waarop RDP/SSH wordt gebruikt (via poort 443). Maak een nieuw openbaar IP-adres of gebruik een bestaande. Het open bare IP-adres moet zich in dezelfde regio bevinden als de Bastion-resource die u maakt.
   * **Naam van openbaar IP-adres**: de naam van de resource voor het open bare IP-adres.
1. Klik op het scherm validatie op **maken**. Wacht ongeveer 5 minuten tot de Bastion-resource is gemaakt en geïmplementeerd.

   ![bastion-host maken](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Verbinding maken

Nadat bastion in het virtuele netwerk is geïmplementeerd, wordt het scherm gewijzigd op de pagina verbinding maken.

1. Typ de gebruikers naam en het wacht woord voor de virtuele machine. Selecteer vervolgens **verbinding maken**.

   ![verbinding maken](./media/quickstart-host-portal/connect.png)
1. De RDP-verbinding met deze virtuele machine via Bastion wordt rechtstreeks geopend in de Azure Portal (via HTML5) met behulp van poort 443 en de Bastion-service.

   ![RDP-verbinding](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het virtuele netwerk en de virtuele machines, verwijdert u de resource groep en alle resources die deze bevat:

1. Voer *TestRG1* in het **zoekvak** boven aan de portal in en selecteer **TestRG1** in de zoek resultaten.

2. Selecteer **Resourcegroep verwijderen**.

3. Voer *TestRG1* in bij **Typ de naam van de resource groep** en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een bastion-host voor uw virtuele netwerk gemaakt en vervolgens veilig verbonden met een virtuele machine via de bastion-host.

* Lees het [overzicht van Bastion](bastion-overview.md) en de [Veelgestelde vragen over Bastion](bastion-faq.md)voor meer informatie over Azure Bastion.
* Als u netwerk beveiligings groepen wilt gebruiken met het Azure Bastion-subnet, raadpleegt u [werken met nsg's](bastion-nsg.md).
* Raadpleeg de [zelf studie](bastion-create-host-portal.md)voor instructies die uitleg bevatten van de instellingen voor de Azure bastion-host.
* Als u verbinding wilt maken met een schaalset voor virtuele machines, raadpleegt [u verbinding maken met een schaalset voor virtuele machines met behulp van Azure Bastion](bastion-connect-vm-scale-set.md).