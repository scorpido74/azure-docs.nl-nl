---
title: Een virtueel Azure-netwerk (klassiek) met meerdere subnetten maken | Microsoft Docs
description: Meer informatie over het maken van een virtueel netwerk (klassiek) met meerdere subnetten in Azure.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: d934386a47c339cd3abdf72578736b44d40e7952
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059010"
---
# <a name="create-a-virtual-network-classic-with-multiple-subnets"></a>Een virtueel netwerk (klassiek) met meerdere subnetten maken

> [!IMPORTANT]
> Azure heeft twee [verschillende implementatie modellen](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor het maken van en werken met resources: Resource Manager en klassiek. Dit artikel gaat over het gebruik van het klassieke implementatiemodel. Micro soft raadt u aan om de meeste nieuwe virtuele netwerken te maken via het [Resource Manager](quick-create-portal.md) -implementatie model.

In deze zelf studie leert u hoe u een eenvoudig virtueel Azure-netwerk (klassiek) maakt met afzonderlijke open bare en privé-subnetten. U kunt Azure-resources maken, zoals virtuele machines en Cloud Services in een subnet. Resources die in virtuele netwerken (klassiek) zijn gemaakt, kunnen met elkaar communiceren en met bronnen in andere netwerken die zijn verbonden met een virtueel netwerk.

Meer informatie over alle instellingen van het [virtuele netwerk](manage-virtual-network.md) en het [subnet](virtual-network-manage-subnet.md) .

> [!WARNING]
> Virtuele netwerken (klassiek) worden direct verwijderd door Azure wanneer een [abonnement wordt uitgeschakeld](../billing/billing-subscription-become-disable.md?toc=%2fazure%2fvirtual-network%2ftoc.json#you-reached-your-spending-limit). Virtuele netwerken (klassiek) worden verwijderd ongeacht of er resources bestaan in het virtuele netwerk. Als u het abonnement later opnieuw inschakelt, moeten de resources die in het virtuele netwerk aanwezig waren opnieuw worden gemaakt.

U kunt een virtueel netwerk (klassiek) maken met behulp van de [Azure Portal](#portal), de [Azure-opdracht regel interface (CLI) 1,0](#azure-cli)of [Power shell](#powershell).

## <a name="portal"></a>Portal

1. Ga in een Internet browser naar de [Azure Portal](https://portal.azure.com). Meld u aan met uw [Azure-account](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Als u nog geen Azure-account hebt, kunt u zich registreren voor een [gratis proefversie](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Klik op **een resource maken** in de portal.
3. Geef het *virtuele netwerk* op in het vak **Zoeken in de Marketplace** bovenaan het **nieuwe** deel venster dat wordt weer gegeven. Klik op **virtueel netwerk** wanneer dit wordt weer gegeven in de zoek resultaten.
4. Selecteer **klassiek** in het vak **een implementatie model selecteren** in het deel venster **Virtual Network** dat wordt weer gegeven en klik vervolgens op **maken**. 
5. Voer de volgende waarden in het deel venster **virtueel netwerk maken (klassiek)** in en klik vervolgens op **maken**:

    |Instelling|Waarde|
    |---|---|
    |Name|myVnet|
    |Adresruimte|10.0.0.0/16|
    |Subnetnaam|Public|
    |Adresbereik van subnet|10.0.0.0/24|
    |Resource group|**Maak nieuw** geselecteerd en voer vervolgens **myResourceGroup**in.|
    |Abonnement en locatie|Selecteer uw abonnement en locatie.

    Als u niet bekend bent met Azure, kunt u meer te weten komen over [resource groepen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [abonnementen](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)en [locaties](https://azure.microsoft.com/regions) (ook wel *regio's*genoemd).
4. In de portal kunt u slechts één subnet maken wanneer u een virtueel netwerk maakt. In deze zelf studie maakt u een tweede subnet nadat u het virtuele netwerk hebt gemaakt. U kunt later meer via internet toegankelijke bronnen maken in het **open bare** subnet. U kunt ook resources maken die niet toegankelijk zijn vanaf internet in het **privé** -subnet. Als u het tweede subnet wilt maken, voert u **myVnet** in het vak **Zoek resources** boven aan de pagina in. Klik op **myVnet** wanneer deze wordt weer gegeven in de zoek resultaten.
5. Klik op **subnetten** (in de sectie **instellingen** ) in het deel venster **virtueel netwerk maken (klassiek)** dat wordt weer gegeven.
6. Klik op **+ toevoegen** in het deel venster **myVnet-subnetten** dat wordt weer gegeven.
7. Voer **privé** in bij **naam** in het deel venster **subnet toevoegen** . Voer **10.0.1.0/24** in voor het **adres bereik**.  Klik op **OK**.
8. In het deel venster **myVnet-subnetten** ziet u de **open bare** en **particuliere** subnetten die u hebt gemaakt.
9. **Optioneel**: Wanneer u deze zelf studie hebt voltooid, wilt u misschien de resources verwijderen die u hebt gemaakt, zodat u geen gebruik kunt maken van de kosten:
    - Klik op **overzicht** in het deel venster **myVnet** .
    - Klik op het pictogram **verwijderen** in het deel venster **myVnet** .
    - Klik op **Ja** in het vak **virtueel netwerk verwijderen** om de verwijdering te bevestigen.

## <a name="azure-cli"></a>Azure-CLI

1. U kunt [de Azure cli installeren en configureren](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), of de CLI gebruiken in de Azure Cloud shell. De Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u hulp nodig hebt bij CLI- `azure <command> --help`opdrachten, typt u. 
2. Meld u in een CLI-sessie aan bij Azure met de volgende opdracht. Als u in het onderstaande vak op **try** -out klikt, wordt een Cloud shell geopend. U kunt u aanmelden bij uw Azure-abonnement zonder de volgende opdracht in te voeren:

    ```azurecli-interactive
    azure login
    ```

3. Voer de volgende opdracht in om ervoor te zorgen dat de CLI zich in de Service beheer modus bevindt:

    ```azurecli-interactive
    azure config mode asm
    ```

4. Een virtueel netwerk maken met een privé subnet:

    ```azurecli-interactive
    azure network vnet create --vnet myVnet --address-space 10.0.0.0 --cidr 16  --subnet-name Private --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --location "East US"
    ```

5. Maak een openbaar subnet in het virtuele netwerk:

    ```azurecli-interactive
    azure network vnet subnet create --name Public --vnet-name myVnet --address-prefix 10.0.1.0/24
    ```    
    
6. Controleer het virtuele netwerk en subnetten:

    ```azurecli-interactive
    azure network vnet show --vnet myVnet
    ```

7. **Optioneel**: Mogelijk wilt u de resources verwijderen die u hebt gemaakt tijdens het volt ooien van deze zelf studie, zodat u geen gebruik kunt maken van de kosten:

    ```azurecli-interactive
    azure network vnet delete --vnet myVnet --quiet
    ```

> [!NOTE]
> Hoewel u geen resource groep kunt opgeven voor het maken van een virtueel netwerk (klassiek) in het gebruik van de CLI, maakt Azure het virtuele netwerk in een resource groep met de naam *standaard netwerk*.

## <a name="powershell"></a>PowerShell

1. Installeer de nieuwste versie van de Power shell [Azure](https://www.powershellgallery.com/packages/Azure) -module. Zie [Overzicht van Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) als u nog geen ervaring hebt met Azure PowerShell.
2. Start een Power shell-sessie.
3. Meld u in PowerShell aan bij Azure door het commando `Add-AzureAccount` in te voeren.
4. Wijzig het volgende pad en de bestands naam, indien van toepassing, en exporteer vervolgens uw bestaande netwerk configuratie bestand:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
    ```

5. Als u een virtueel netwerk met open bare en privé-subnetten wilt maken, gebruikt u een tekst editor om het **VirtualNetworkSite** -element toe te voegen dat volgt op het netwerk configuratie bestand.

    ```xml
    <VirtualNetworkSite name="myVnet" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Private">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Public">
            <AddressPrefix>10.0.1.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    ```

    Controleer het volledige [netwerk configuratie bestand schema](https://msdn.microsoft.com/library/azure/jj157100.aspx).

6. Importeer het netwerk configuratie bestand:

    ```powershell
    Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
    ```

    > [!WARNING]
    > Het importeren van een gewijzigd netwerk configuratie bestand kan wijzigingen veroorzaken in bestaande virtuele netwerken (klassiek) in uw abonnement. Zorg ervoor dat u het vorige virtuele netwerk alleen toevoegt en dat u geen bestaande virtuele netwerken wijzigt of verwijdert uit uw abonnement. 

7. Controleer het virtuele netwerk en subnetten:

    ```powershell
    Get-AzureVNetSite -VNetName "myVnet"
    ```

8. **Optioneel**: Mogelijk wilt u de resources verwijderen die u hebt gemaakt tijdens het volt ooien van deze zelf studie, zodat u geen gebruiks kosten hebt. Als u het virtuele netwerk wilt verwijderen, voert u de stappen 4-6 opnieuw uit. deze keer wordt het **VirtualNetworkSite** -element verwijderd dat u in stap 5 hebt toegevoegd.
 
> [!NOTE]
> Hoewel u geen resource groep kunt opgeven voor het maken van een virtueel netwerk (klassiek) in Power shell, maakt Azure het virtuele netwerk in een resource groep met de naam *standaard netwerk*.

---

## <a name="next-steps"></a>Volgende stappen

- Zie [virtuele netwerken beheren](manage-virtual-network.md) en [subnetten van het virtuele netwerk beheren](virtual-network-manage-subnet.md)voor meer informatie over alle instellingen van het virtuele netwerk en het subnet. U hebt verschillende opties voor het gebruik van virtuele netwerken en subnetten in een productie omgeving om te voldoen aan verschillende vereisten.
- Maak een virtuele machine met [Windows](../virtual-machines/windows/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Linux](../virtual-machines/linux/classic/createportal-classic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en verbind deze met een bestaand virtueel netwerk.
- Als u twee virtuele netwerken op dezelfde Azure-locatie wilt verbinden, maakt u een [virtueel netwerk peering](create-peering-different-deployment-models.md) tussen de virtuele netwerken. U kunt een virtueel netwerk (Resource Manager) koppelen aan een virtueel netwerk (klassiek), maar u kunt geen peering maken tussen twee virtuele netwerken (klassiek).
- Verbind het virtuele netwerk met een on-premises netwerk met behulp van een [VPN gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of een [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -circuit.
