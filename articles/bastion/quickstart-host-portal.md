---
title: 'Quickstart: Een Bastion-host maken op basis van een VM en verbinding maken via een privé-IP-adres'
titleSuffix: Azure Bastion
description: In deze quickstart leert u hoe u een Azure Bastion-host maakt op basis van een virtuele machine en veilig verbinding maakt met behulp van een privé-IP-adres.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6f451e7b115c00bc7b2cf350e00b9f704ab1d29f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019049"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Quickstart: Maak verbinding met een virtuele machine met behulp van een privé-IP-adres en Azure Bastion

In dit quickstart-artikel ziet u hoe u via uw browser verbinding kunt maken met een virtuele machine met behulp van Azure Bastion en de Azure-portal. In de Azure-portal, vanuit uw Azure-VM, kunt u Bastion implementeren in uw virtuele netwerk. Na de implementatie van Bastion kunt u verbinding maken met de virtuele machine via het bijbehorende privé-IP-adres met behulp van de Azure-portal. Er is voor uw VM geen openbaar IP-adres of speciale software nodig. Een voordeel van het maken van een Bastion-host voor uw VNet rechtstreeks vanuit uw VM is dat veel van de instellingen vooraf voor u zijn ingevuld.

Zodra de service is ingericht, is de RDP/SSH-ervaring beschikbaar voor alle virtuele machines in hetzelfde virtuele netwerk. Zie [Wat is Azure Bastion?](bastion-overview.md) voor meer informatie over Azure Bastion.

## <a name="prerequisites"></a><a name="prereq"></a>Vereisten

* Een virtueel netwerk.
* Een virtuele Windows-machine in een virtueel netwerk.
* De volgende vereiste rollen:
  * De lezerrol op de virtuele machine.
  * De lezerrol op de NIC met het privé-IP-adres van de virtuele machine.

* Poorten: Als u verbinding wilt maken met de VM, moeten de volgende poorten zijn geopend op de VM:
  * Poorten voor inkomend verkeer: RDP (3389)

### <a name="example-values"></a>Voorbeeldwaarden

|**Naam** | **Waarde** |
| --- | --- |
| Naam |  TestVNet1-bastion |
| Virtueel netwerk |  TestVNet1 (op basis van de VM) |
| + Subnet-naam | AzureBastionSubnet |
| AzureBastionSubnet-adressen |  10.1.254.0/27 |
| Openbaar IP-adres |  Nieuwe maken |
| Naam openbaar IP-adres | VNet1BastionPIP  |
| Openbaar IP-adres SKU |  Standard  |
| Toewijzing  | Statisch |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Een Bastion-host maken

Wanneer u een Bastion-host in de Azure-portal maakt met behulp van een bestaande virtuele machine, worden verschillende instellingen automatisch standaard in overeenstemming met uw virtuele machine en/of virtuele netwerk.

1. Open de [Azure Portal](https://portal.azure.com). Ga naar uw virtuele machine en selecteer vervolgens **Verbinding maken**.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="Instellingen van virtuele machines" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. In de vervolgkeuzelijst selecteert u **Bastion**.
1. Op de **pagina TestVM | Verbinding maken** selecteer **Bastion gebruiken**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Instellingen van virtuele machines" border="false":::

1. Vul op de pagina **Bastion** de volgende instellingsvelden in:

   * **Naam**: Geef de Bastion-host een naam.
   * **Subnet**: Het subnet in het virtuele netwerk waarop de Bastion-resource zal worden geïmplementeerd. Het subnet moet worden gemaakt met de naam **AzureBastionSubnet**. De naam geeft Azure aan op welk subnet de Bastion-resources moeten worden geïmplementeerd. Dit wijkt af van een gateway-subnet. U moet een subnet van minimaal /27 of groter (/27, /26, /25, enzovoort) gebruiken.
   
      * Selecteer **Subnetconfiguratie beheren**.
      * Selecteer **AzureBastionSubnet**.
      * Indien nodig past u het adresbereik aan in CIDR-notatie. Bijvoorbeeld: 10.1.254.0/27.
      * Pas geen andere instellingen aan. Selecteer **OK** om de wijzigingen in het subnet te accepteren en op te slaan, of selecteer **x** bovenaan de pagina als u geen wijzigingen wilt aanbrengen.
1. Klik op de knop terug in de browser om terug te gaan naar de pagina **Bastion** en door te gaan met het opgeven van waarden.
   * **Openbare IP-adresnaam**: De naam van de resource voor het openbare IP-adres.
   * **Openbaar IP-adres**: Dit is het openbare IP-adres van de Bastion-bron waarop RDP/SSH wordt geopend (via poort 443). Maak een nieuw openbaar IP-adres.
1. Selecteer **Maken** om de bastion-host te maken. Azure valideert uw instellingen en maakt vervolgens de host. Het duurt ongeveer vijf minuten om de host en de resources te maken en te implementeren.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="Instellingen van virtuele machines":::

## <a name="connect"></a><a name="connect"></a>Verbinding maken

Nadat Bastion in het virtuele netwerk is geïmplementeerd, verschijnt de pagina Verbinding maken.

1. Voer de gebruikersnaam en het wachtwoord voor uw virtuele machine in. Selecteer vervolgens **Verbinding maken**.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="Instellingen van virtuele machines":::
1. De RDP-verbinding met deze virtuele machine via Bastion wordt rechtstreeks geopend in de Azure Portal (via HTML5) met behulp van poort 443 en de Bastion-service.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="Instellingen van virtuele machines":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het gebruiken van het virtuele netwerk en de virtuele machines, verwijdert u de resourcegroep en alle resources die deze groep bevat:

1. Voer de naam van uw resourcegroep in het vak **Zoeken** bovenaan de portal in en selecteer het in de zoekresultaten.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer uw resourcegroep in voor **TYP DE NAAM VAN DE RESOURCEGROEP** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Bastion-host voor uw virtuele netwerk gemaakt en vervolgens veilig verbonden met een virtuele machine via de Bastion-host. U kunt nu doorgaan met de volgende stap als u verbinding wilt maken met een virtuele-machineschaalset.

> [!div class="nextstepaction"]
> [Verbinding maken met een virtuele-machineschaalset met behulp van Azure Bastion](bastion-connect-vm-scale-set.md)
