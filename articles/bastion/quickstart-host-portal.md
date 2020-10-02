---
title: 'Quickstart: Maak verbinding met een virtuele machine met behulp van een privé-IP-adres: Azure Bastion'
description: In dit artikel leert u hoe u een Azure Bastion-host maakt op basis van een virtuele machine en veilig verbinding maakt met behulp van een privé-IP-adres.
services: bastion
author: charwen
ms.service: bastion
ms.topic: quickstart
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: b0155ae92e3179918273d6a19773aa15b67949ea
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985584"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Quickstart: Maak verbinding met een virtuele machine met behulp van een privé-IP-adres en Azure Bastion

In dit quickstart-artikel ziet u hoe u verbinding kunt maken met een virtuele machine met behulp van een privé-IP-adres. Wanneer u verbinding maakt met Azure Bastion, hebben uw virtuele machines geen openbaar IP-adres nodig. Met de stappen in dit artikel kunt u Bastion implementeren in uw virtuele netwerk via de virtuele machine in de portal. Zodra de service is ingericht, is de RDP/SSH-ervaring beschikbaar voor alle virtuele machines in hetzelfde virtuele netwerk.

## <a name="prerequisites"></a><a name="prereq"></a>Vereisten

* Een Azure Virtual Network.
* Een virtuele machine van Azure die zich in het virtuele netwerk bevindt en waarvan poort 3389 is geopend.

### <a name="example-values"></a>Voorbeeldwaarden

|**Naam** | **Waarde** |
| --- | --- |
| Naam |  VNet1Bastion |
| Regio | eastus |
| Virtueel netwerk |  VNet1 |
| + Subnet-naam | AzureBastionSubnet |
| AzureBastionSubnet-adressen |  10.1.254.0/27 |
| Openbaar IP-adres |  Nieuwe maken |
| Naam openbaar IP-adres | VNet1BastionPIP  |
| Openbaar IP-adres SKU |  Standard  |
| Toewijzing  | Statisch |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Een Bastion-host maken

Wanneer u een Bastion-host in de portal maakt met behulp van een bestaande virtuele machine, worden verschillende instellingen automatisch standaard in overeenstemming met uw virtuele machine en/of virtuele netwerk.

1. Open [Azure Portal](https://portal.azure.com). Ga naar uw virtuele machine en klik vervolgens op **Verbinding maken**.

   ![Instellingen van virtuele machines](./media/quickstart-host-portal/vm-settings.png)
1. In de vervolgkeuzelijst selecteert u **Bastion**.
1. Selecteer op de pagina Verbinding de optie **Bastion gebruiken**.

   ![Selecteer Bastion](./media/quickstart-host-portal/select-bastion.png)

1. Vul op de pagina Bastion de volgende instellingsvelden in:

   * **Naam**: De naam van de Bastion-host
   * **Subnet**: Het subnet in het virtuele netwerk waarop de Bastion-resource zal worden geïmplementeerd. Het subnet moet worden gemaakt met de naam **AzureBastionSubnet**. De naam geeft Azure aan op welk subnet de Bastion-resources moeten worden geïmplementeerd. Dit wijkt af van een gatewaysubnet. U moet een subnet van minimaal /27 of groter (/27, /26, /25, enzovoort) gebruiken.
   
      * Selecteer **Subnetconfiguratie beheren** en selecteer vervolgens **+ Subnet**.
      * Op de pagina Subnet toevoegen typt u **AzureBastionSubnet**.
      * Specificeer het adresbereik in CIDR-notatie. Bijvoorbeeld: 10.1.254.0/27.
      * Selecteer **OK** om het subnet te maken. Ga aan de bovenkant van de pagina terug naar Bastion om de rest van de instellingen te voltooien.

         ![Navigeer naar Bastion-instellingen](./media/quickstart-host-portal/navigate-bastion.png)
   * **Openbaar IP-adres**: Dit is het openbare IP-adres van de Bastion-bron waarop RDP/SSH wordt geopend (via poort 443). Maak een nieuw, openbaar IP-adres of gebruik een bestaande. Het openbare IP-adres moet zich in dezelfde regio bevinden als de Bastion-resource die u aan het maken bent.
   * **Openbare IP-adresnaam**: De naam van de resource voor het openbare IP-adres.
1. Klik in het scherm Validatie op **Maken**. Wacht ongeveer 5 minuten tot de Bastion-resource is gemaakt en geïmplementeerd.

   ![Maak een Bastion-host](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Verbinding maken

Nadat Bastion in het virtuele netwerk is geïmplementeerd, verschijnt de pagina Verbinding maken.

1. Voer de gebruikersnaam en het wachtwoord voor uw virtuele machine in. Selecteer vervolgens **Verbinding maken**.

   ![Schermopname toont het dialoogvenster Verbinding maken met Azure Bastion, waarin u wordt gevraagd om een gebruikersnaam en wachtwoord.](./media/quickstart-host-portal/connect.png)
1. De RDP-verbinding met deze virtuele machine via Bastion wordt rechtstreeks geopend in de Azure Portal (via HTML5) met behulp van poort 443 en de Bastion-service.

   ![Verbinding maken met RDP](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het gebruiken van het virtuele netwerk en de virtuele machines, verwijdert u de resourcegroep en alle resources die deze groep bevat:

1. Voer *TestRG1* in in het **Zoek**-veld aan de bovenkant van de portal en selecteer **TestRG1** uit de zoekresultaten.

2. Selecteer **Resourcegroep verwijderen**.

3. Voer *TestRG1* in bij **Voer de naam van de resourcegroep in** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Bastion-host voor uw virtuele netwerk gemaakt en vervolgens veilig verbonden met een virtuele machine via de Bastion-host.

* Lees voor meer informatie over Azure Bastion het [Bastion-overzicht](bastion-overview.md) en [Veelgestelde vragen over Bastion](bastion-faq.md).
* Zie [Werken met NSG's](bastion-nsg.md) als u netwerkbeveiligingsgroepen wilt gebruiken met het Azure Bastion-subnet.
* Zie de [zelfstudie](bastion-create-host-portal.md) voor instructies die uitleg bevatten over de instellingen voor de Azure Bastion-host.
* Als u verbinding wilt maken met een schaalset voor virtuele machines, raadpleegt u [Verbinding maken met een schaalset voor virtuele machines met behulp van Azure Bastion](bastion-connect-vm-scale-set.md).