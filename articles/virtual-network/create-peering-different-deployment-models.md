---
title: Een Azure virtual network peering maken - verschillende implementatiemodellen - hetzelfde abonnement | Microsoft Documenten
description: Meer informatie over het maken van een virtueel netwerk dat kan worden gemaakt tussen virtuele netwerken die zijn gemaakt via verschillende Azure-implementatiemodellen die in hetzelfde Azure-abonnement bestaan.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 61df13e78dc7115d4f4d45ab18b9ffdae107dc96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023256"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>Een virtueel netwerk peering maken - verschillende implementatiemodellen, hetzelfde abonnement

In deze zelfstudie leert u een virtueel netwerk peering te maken tussen virtuele netwerken die zijn gemaakt via verschillende implementatiemodellen. Beide virtuele netwerken bestaan in hetzelfde abonnement. Met peering twee virtuele netwerken kunnen resources in verschillende virtuele netwerken met elkaar communiceren met dezelfde bandbreedte en latentie alsof de resources zich in hetzelfde virtuele netwerk bevinden. Meer informatie over [virtueel netwerkpeeren](virtual-network-peering-overview.md).

De stappen om een virtueel netwerkpeering te maken zijn verschillend, afhankelijk van of de virtuele netwerken zich in dezelfde of verschillende abonnementen bevinden en welk [Azure-implementatiemodel](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuele netwerken worden gemaakt. Meer informatie over het maken van een virtueel netwerk peering in andere scenario's door te klikken op het scenario in de volgende tabel:

|Azure-implementatiemodel  | Azure-abonnement  |
|--------- |---------|
|[Beide in Resource Manager](tutorial-connect-virtual-networks-portal.md) |Hetzelfde|
|[Beide in Resource Manager](create-peering-different-subscriptions.md) |Verschillend|
|[Eén in Resource Manager, één klassiek](create-peering-different-deployment-models-subscriptions.md) |Verschillend|

Een virtueel netwerk peering kan niet worden gemaakt tussen twee virtuele netwerken geïmplementeerd via het klassieke implementatiemodel. Als u virtuele netwerken moet verbinden die beide zijn gemaakt via het klassieke implementatiemodel, u een Azure [VPN-gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) gebruiken om de virtuele netwerken met elkaar te verbinden.

In deze zelfstudie worden virtuele netwerken in dezelfde regio gepeers. U ook virtuele netwerken peerin verschillende [ondersteunde regio's](virtual-network-manage-peering.md#cross-region). Het wordt aanbevolen dat u vertrouwd te raken met de [peering eisen en beperkingen](virtual-network-manage-peering.md#requirements-and-constraints) voordat peering virtuele netwerken.

U de Azure-portal, de Azure [command-line interface](#cli) (CLI), Azure [PowerShell](#powershell)of een Azure Resource Manager-sjabloon gebruiken om een virtueel netwerkpeering te maken. Klik op een van de vorige knopkoppelingen om rechtstreeks naar de stappen te gaan voor het maken van een virtueel netwerk peering met behulp van uw tool naar keuze.

## <a name="create-peering---azure-portal"></a>Peering maken - Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Het account waarmee u zich aanmeldt, moet over de vereiste machtigingen beschikken om een virtueel netwerkpeering te maken. Zie Machtigingen voor virtuele [netwerkpeering](virtual-network-manage-peering.md#requirements-and-constraints)voor een lijst met machtigingen.
2. Klik **op + Nieuw**, klik op **Netwerken**en klik vervolgens op Virtueel **netwerk**.
3. Voer in **het virtuele netwerkblad maken** waarden in of selecteer waarden voor de volgende instellingen en klik op **Maken:**
    - **Naam**: *myVnet1*
    - **Adresruimte**: *10.0.0.0/16*
    - **Subnetnaam**: *standaard*
    - **Subnetadresbereik**: *10.0.0.0/24*
    - **Abonnement:** Selecteer uw abonnement
    - **Resourcegroep**: Selecteer **Nieuw maken** en voer *myResourceGroup* in
    - **Locatie**: *Oost-VS*
4. Klik op **+ New**. Typ *Virtueel netwerk*in het vak **Marketplace** zoeken . Klik **op Virtueel netwerk** wanneer het wordt weergegeven in de zoekresultaten.
5. Selecteer in het **virtuele netwerkblad** **Klassiek** in het vak **Een implementatiemodel selecteren** en klik op **Maken**.
6. Voer in **het virtuele netwerkblad maken** waarden in of selecteer waarden voor de volgende instellingen en klik op **Maken:**
    - **Naam**: *myVnet2*
    - **Adresruimte**: *10.1.0.0/16*
    - **Subnetnaam**: *standaard*
    - **Subnetadresbereik**: *10.1.0.0/24*
    - **Abonnement:** Selecteer uw abonnement
    - **Resourcegroep**: Bestaande **gebruiken** selecteren en *myResourceGroup* selecteren
    - **Locatie**: *Oost-VS*
7. Typ *myResourceGroup*in het vak **Zoekresources** boven aan de portal . Klik **op myResourceGroup** wanneer deze wordt weergegeven in de zoekresultaten. Er verschijnt een blad voor de brongroep **myresourcegroep.** De resourcegroep bevat de twee virtuele netwerken die in eerdere stappen zijn gemaakt.
8. Klik op **myVNet1**.
9. Klik in het **myVnet1-blad** dat wordt weergegeven op **Peerings** uit de verticale lijst met opties aan de linkerkant van het blad.
10. Klik in het **myVnet1 - Peerings-mes** dat is verschenen op **+ Toevoegen**
11. Klik in het **peeringblad toevoegen** dat wordt weergegeven, voer de volgende opties in of selecteer deze op **OK:**
     - **Naam**: *myVnet1ToMyVnet2*
     - **Virtueel netwerkimplementatiemodel:** **Selecteer Klassiek**.
     - **Abonnement:** Selecteer uw abonnement
     - **Virtueel netwerk**: Klik op **Een virtueel netwerk kiezen**en klik vervolgens op **myVnet2**.
     - **Virtuele netwerktoegang toestaan:** Controleer of **Ingeschakeld** is geselecteerd.
    Er worden geen andere instellingen gebruikt in deze zelfstudie. Lees [Virtuele netwerkpeeringen beheren](virtual-network-manage-peering.md#create-a-peering)voor meer informatie over alle peering-instellingen.
12. Na het klikken op **OK** in de vorige stap, de **Peering** blade toevoegen sluit en zie je de **myVnet1 - Peerings mes** weer. Na een paar seconden verschijnt het peering dat u hebt gemaakt in het blad. **Verbonden** wordt vermeld in de kolom **PEERING STATUS** voor de **myVnet1ToMyVnet2** peering die u hebt gemaakt.

    Het peering is nu gevestigd. Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u standaard Azure-naamomzetting voor de virtuele netwerken gebruikt, kunnen de bronnen in de virtuele netwerken geen namen in de virtuele netwerken oplossen. Als u namen in virtuele netwerken in een peering wilt oplossen, moet u uw eigen DNS-server maken. Meer informatie over het instellen [van naamomzetting met uw eigen DNS-server.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
13. **Optioneel:** Hoewel het maken van virtuele machines niet in deze zelfstudie wordt behandeld, u in elk virtueel netwerk een virtuele machine maken en verbinding maken van de ene virtuele machine naar de andere om de connectiviteit te valideren.
14. **Optioneel:** voer de stappen in het gedeelte [Resources](#delete-portal) verwijderen van dit artikel uit om de resources die u in deze zelfstudie maakt, te verwijderen.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Peering maken - Azure CLI

Voer de volgende stappen uit met de Azure-klassieke CLI en azure cli. U de stappen uitvoeren vanuit de Azure Cloud Shell door de knop **Probeer het** in een van de volgende stappen te selecteren, of door de [klassieke CLI](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) en [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) te installeren en de opdrachten op uw lokale computer uit te voeren.

1. Als u de Cloud Shell gebruikt, gaat u naar stap 2, omdat de Cloud Shell u automatisch aanmeldt bij Azure. Open een opdrachtsessie en meld `azure login` u aan bij Azure met de opdracht.
2. Voer de MODUS CLI in `azure config mode asm` Servicebeheer uit door de opdracht in te voeren.
3. Voer de volgende opdracht in om het virtuele netwerk (klassiek) te maken:

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. Voer het volgende bash CLI-script uit met behulp van de CLI, niet met de klassieke CLI. Zie De Azure CLI op Windows [installeren voor](/cli/azure/install-azure-cli-windows)opties voor het uitvoeren van CLI-scripts op Windows-computers.

   ```azurecli-interactive
   #!/bin/bash

   # Create a resource group.
   az group create \
     --name myResourceGroup \
     --location eastus

   # Create the virtual network (Resource Manager).
   az network vnet create \
     --name myVnet1 \
     --resource-group myResourceGroup \
     --location eastus \
     --address-prefix 10.0.0.0/16
   ```

5. Maak een virtueel netwerk peering tussen de twee virtuele netwerken gemaakt via de verschillende implementatiemodellen met behulp van de CLI. Kopieer het volgende script naar een teksteditor op uw pc. Vervang `<subscription id>` door uw abonnements-ID. Als u uw abonnements-ID niet `az account show` kent, voert u de opdracht in. De waarde voor **id** in de uitvoer is uw abonnements-ID. Plak het gewijzigde script in uw CLI-sessie en druk op `Enter`.

   ```azurecli-interactive
   # Get the ID for VNet1.
   vnet1Id=$(az network vnet show \
     --resource-group myResourceGroup \
     --name myVnet1 \
     --query id --out tsv)

   # Peer VNet1 to VNet2.
   az network vnet peering create \
     --name myVnet1ToMyVnet2 \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
     --allow-vnet-access
   ```

6. Nadat het script is uitgevoerd, controleert u het peering voor het virtuele netwerk (Resource Manager). Kopieer de volgende opdracht, plak deze in `Enter`uw CLI-sessie en druk op:

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   De uitvoer toont **Verbonden** in de kolom **PeeringState.**

   Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u standaard Azure-naamomzetting voor de virtuele netwerken gebruikt, kunnen de bronnen in de virtuele netwerken geen namen in de virtuele netwerken oplossen. Als u namen in virtuele netwerken in een peering wilt oplossen, moet u uw eigen DNS-server maken. Meer informatie over het instellen [van naamomzetting met uw eigen DNS-server.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
7. **Optioneel:** Hoewel het maken van virtuele machines niet in deze zelfstudie wordt behandeld, u in elk virtueel netwerk een virtuele machine maken en verbinding maken van de ene virtuele machine naar de andere om de connectiviteit te valideren.
8. **Optioneel:** voer de stappen [in](#delete-cli) Bronnen in dit artikel verwijderen uit om de resources die u in deze zelfstudie maakt, te verwijderen.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Peering maken - PowerShell

1. Installeer de nieuwste versie van de PowerShell Azure- en [Az-modules.](https://www.powershellgallery.com/packages/Azure) [Az](https://www.powershellgallery.com/packages/Az/) Zie [Overzicht van Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) als u nog geen ervaring hebt met Azure PowerShell.
2. Start een PowerShell-sessie.
3. Meld u in PowerShell aan `Add-AzureAccount` bij Azure door de opdracht in te voeren. Het account waarmee u zich aanmeldt, moet over de vereiste machtigingen beschikken om een virtueel netwerkpeering te maken. Zie Machtigingen voor virtuele [netwerkpeering](virtual-network-manage-peering.md#requirements-and-constraints)voor een lijst met machtigingen.
4. Als u een virtueel netwerk (klassiek) wilt maken met PowerShell, moet u een nieuw netwerkconfiguratiebestand maken of wijzigen. Meer informatie over het [exporteren, bijwerken en importeren van netwerkconfiguratiebestanden](virtual-networks-using-network-configuration-file.md). Het bestand moet het volgende **VirtualNetworkSite-element** bevatten voor het virtuele netwerk dat in deze zelfstudie wordt gebruikt:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Het importeren van een gewijzigd netwerkconfiguratiebestand kan leiden tot wijzigingen in bestaande virtuele netwerken (klassiek) in uw abonnement. Zorg ervoor dat u alleen het vorige virtuele netwerk toevoegt en dat u bestaande virtuele netwerken niet uit uw abonnement wijzigt of verwijdert.
5. Meld u aan bij Azure om het virtuele `Connect-AzAccount` netwerk (Resource Manager) te maken door de opdracht in te voeren. Het account waarmee u zich aanmeldt, moet over de vereiste machtigingen beschikken om een virtueel netwerkpeering te maken. Zie Machtigingen voor virtuele [netwerkpeering](virtual-network-manage-peering.md#requirements-and-constraints)voor een lijst met machtigingen.
6. Maak een resourcegroep en een virtueel netwerk (Resource Manager). Kopieer het script, plak het in `Enter`PowerShell en druk op .

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. Maak een virtueel netwerk peering tussen de twee virtuele netwerken gemaakt via de verschillende implementatiemodellen. Kopieer het volgende script naar een teksteditor op uw pc. Vervang `<subscription id>` door uw abonnements-ID. Als u uw abonnements-ID niet `Get-AzSubscription` kent, voert u de opdracht in om deze te bekijken. De waarde voor **id** in de geretourneerde uitvoer is uw abonnements-ID. Als u het script wilt uitvoeren, kopieert u het gewijzigde script vanuit uw `Enter`teksteditor, klikt u met de rechtermuisknop in uw PowerShell-sessie en drukt u op .

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. Nadat het script is uitgevoerd, controleert u het peering voor het virtuele netwerk (Resource Manager). Kopieer de volgende opdracht, plak deze in uw `Enter`PowerShell-sessie en druk op:

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    De uitvoer toont **Verbonden** in de kolom **PeeringState.**

    Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u standaard Azure-naamomzetting voor de virtuele netwerken gebruikt, kunnen de bronnen in de virtuele netwerken geen namen in de virtuele netwerken oplossen. Als u namen in virtuele netwerken in een peering wilt oplossen, moet u uw eigen DNS-server maken. Meer informatie over het instellen [van naamomzetting met uw eigen DNS-server.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

9. **Optioneel:** Hoewel het maken van virtuele machines niet in deze zelfstudie wordt behandeld, u in elk virtueel netwerk een virtuele machine maken en verbinding maken van de ene virtuele machine naar de andere om de connectiviteit te valideren.
10. **Optioneel:** voer de stappen [in](#delete-powershell) Bronnen in dit artikel verwijderen uit om de resources die u in deze zelfstudie maakt, te verwijderen.

## <a name="delete-resources"></a><a name="delete"></a>Resources verwijderen

Wanneer u deze zelfstudie hebt voltooid, u de resources verwijderen die u in de zelfstudie hebt gemaakt, zodat u geen gebruikskosten hoeft te maken. Als u een resourcegroep verwijdert, worden ook alle resources in de resourcegroep verwijderd.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure-portal

1. Voer in het zoekvak van de portal **mijnResourcegroep**in . Klik in de zoekresultaten op **myResourceGroup**.
2. Klik op het **myResourceGroup-blad** op het pictogram **Verwijderen.**
3. Als u de verwijdering wilt bevestigen, voert u in het vak **NAAM VAN DE BRONGROEP** **naam myResourceGroup**in en klikt u op **Verwijderen**.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure-CLI

1. Gebruik de Azure CLI om het virtuele netwerk (Resource Manager) te verwijderen met de volgende opdracht:

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. Gebruik de klassieke CLI om het virtuele netwerk (klassiek) te verwijderen met de volgende opdrachten:

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="powershell"></a><a name="delete-powershell"></a>Powershell

1. Voer de volgende opdracht in om het virtuele netwerk (Resource Beheer) te verwijderen:

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. Als u het virtuele netwerk (klassiek) met PowerShell wilt verwijderen, moet u een bestaand netwerkconfiguratiebestand wijzigen. Meer informatie over het [exporteren, bijwerken en importeren van netwerkconfiguratiebestanden](virtual-networks-using-network-configuration-file.md). Verwijder het volgende VirtualNetworkSite-element voor het virtuele netwerk dat in deze zelfstudie wordt gebruikt:

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > Het importeren van een gewijzigd netwerkconfiguratiebestand kan leiden tot wijzigingen in bestaande virtuele netwerken (klassiek) in uw abonnement. Zorg ervoor dat u alleen het vorige virtuele netwerk verwijdert en dat u geen andere bestaande virtuele netwerken uit uw abonnement wijzigt of verwijdert.

## <a name="next-steps"></a>Volgende stappen

- Maak jezelf grondig vertrouwd met belangrijke [beperkingen en gedragingen voor virtuele netwerken](virtual-network-manage-peering.md#requirements-and-constraints) voordat u een virtueel netwerk peering maakt voor productiegebruik.
- Meer informatie over alle [instellingen voor virtuele netwerkpeering](virtual-network-manage-peering.md#create-a-peering).
- Meer informatie over het [maken van een hub- en spoke-netwerktopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) met virtuele netwerkpeering.
