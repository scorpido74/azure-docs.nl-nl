---
title: Een Azure virtual network peering maken - verschillende implementatiemodellen -verschillende abonnementen
titlesuffix: Azure Virtual Network
description: Meer informatie over het maken van een virtueel netwerk dat kan worden gemaakt tussen virtuele netwerken die zijn gemaakt via verschillende Azure-implementatiemodellen die in verschillende Azure-abonnementen bestaan.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 6823514e284f75f0580578dcabaa1b1bdcbe2f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239841"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-and-subscriptions"></a>Een virtueel netwerk peering maken - verschillende implementatiemodellen en -abonnementen

In deze zelfstudie leert u een virtueel netwerk peering te maken tussen virtuele netwerken die zijn gemaakt via verschillende implementatiemodellen. De virtuele netwerken bestaan in verschillende abonnementen. Met peering twee virtuele netwerken kunnen resources in verschillende virtuele netwerken met elkaar communiceren met dezelfde bandbreedte en latentie alsof de resources zich in hetzelfde virtuele netwerk bevinden. Meer informatie over [virtueel netwerkpeeren](virtual-network-peering-overview.md).

De stappen om een virtueel netwerkpeering te maken zijn verschillend, afhankelijk van of de virtuele netwerken zich in dezelfde of verschillende abonnementen bevinden en welk [Azure-implementatiemodel](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuele netwerken worden gemaakt. Meer informatie over het maken van een virtueel netwerk peering in andere scenario's door te klikken op het scenario in de volgende tabel:

|Azure-implementatiemodel  | Azure-abonnement  |
|--------- |---------|
|[Beide in Resource Manager](tutorial-connect-virtual-networks-portal.md) |Hetzelfde|
|[Beide in Resource Manager](create-peering-different-subscriptions.md) |Verschillend|
|[Eén in Resource Manager, één klassiek](create-peering-different-deployment-models.md) |Hetzelfde|

Een virtueel netwerkpeering kan niet worden gemaakt tussen twee virtuele netwerken die via het klassieke implementatiemodel worden geïmplementeerd. Deze zelfstudie maakt gebruik van virtuele netwerken die in dezelfde regio bestaan. In deze zelfstudie worden virtuele netwerken in dezelfde regio gepeers. U ook virtuele netwerken peerin verschillende [ondersteunde regio's](virtual-network-manage-peering.md#cross-region). Het wordt aanbevolen dat u vertrouwd te raken met de [peering eisen en beperkingen](virtual-network-manage-peering.md#requirements-and-constraints) voordat peering virtuele netwerken.

Bij het maken van een virtueel netwerk peering tussen virtuele netwerken die in verschillende abonnementen bestaan, moeten de abonnementen beide worden gekoppeld aan dezelfde Azure Active Directory-tenant. Als u nog geen Azure Active Directory-tenant hebt, u er snel [een maken.](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant) U virtuele netwerken verbinden in verschillende abonnementen en verschillende Azure Active Directory-tenants via een Azure [VPN-gateway.](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

U de [Azure-portal](#portal), de Azure [command-line interface](#cli) (CLI) of Azure [PowerShell](#powershell) gebruiken om een virtueel netwerkpeering te maken. Klik op een van de vorige knopkoppelingen om rechtstreeks naar de stappen te gaan voor het maken van een virtueel netwerk peering met behulp van uw tool naar keuze.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Peering maken - Azure-portal

Deze zelfstudie gebruikt verschillende accounts voor elk abonnement. Als u een account gebruikt met machtigingen voor beide abonnementen, u voor alle stappen hetzelfde account gebruiken, de stappen overslaan om u uit te loggen bij de portal en de stappen overslaan voor het toewijzen van machtigingen van een andere gebruiker aan de virtuele netwerken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als UserA. Het account waarmee u zich aanmeldt, moet over de benodigde machtigingen beschikken om een virtueel netwerkpeering te maken. Zie Machtigingen voor virtuele [netwerkpeering](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
2. Klik **op + Nieuw**, klik op **Netwerken**en klik vervolgens op Virtueel **netwerk**.
3. Voer in **het virtuele netwerkblad maken** waarden in of selecteer waarden voor de volgende instellingen en klik op **Maken:**
    - **Naam**: *myVnetA*
    - **Adresruimte**: *10.0.0.0/16*
    - **Subnetnaam**: *standaard*
    - **Subnetadresbereik**: *10.0.0.0/24*
    - **Abonnement**: Selecteer abonnement A.
    - **Resourcegroep**: Selecteer **Nieuw maken** en voer *myResourceGroupA* in
    - **Locatie**: *Oost-VS*
4. Typ *myVnetA*in het vak **Zoekresources** boven aan de portal . Klik **op myVnetA** wanneer deze in de zoekresultaten wordt weergegeven. Er verschijnt een mes voor het **virtuele myVnetA-netwerk.**
5. Klik in het **myVnetA-blad** dat wordt weergegeven op **Toegangsbeheer (IAM)** in de verticale lijst met opties aan de linkerkant van het blad.
6. Klik in het **myVnetA - Access control (IAM)** blad dat wordt weergegeven op **+ Roltoewijzing toevoegen**.
7. Selecteer **netwerkbijdrager** in het vak **Roltoewijzing** toevoegen in het hoofdje **roltoewijzing.**
8. Selecteer **in het** vak Selecteren de optie UserB of typ het e-mailadres van UserB om ernaar te zoeken. De lijst met gebruikers die worden weergegeven, is van dezelfde Azure Active Directory-tenant als het virtuele netwerk waarvoor u peering instelt. Klik op UserB wanneer deze in de lijst wordt weergegeven.
9. Klik op **Opslaan**.
10. Meld u af bij de portal als UserA en meld u vervolgens aan als UserB.
11. Klik **op + Nieuw**, typ Virtueel *netwerk* in het vak Zoeken **in marketplace** en klik vervolgens op Virtueel **netwerk** in de zoekresultaten.
12. Selecteer in het blad **Virtueel netwerk** dat wordt weergegeven **de** optie Klassiek in het vak **Een implementatiemodel selecteren** en klik vervolgens op **Maken**.
13. Voer in het vak Virtueel netwerk (klassiek maken) dat wordt weergegeven, de volgende waarden in:

    - **Naam**: *myVnetB*
    - **Adresruimte**: *10.1.0.0/16*
    - **Subnetnaam**: *standaard*
    - **Subnetadresbereik**: *10.1.0.0/24*
    - **Abonnement**: Selecteer abonnement B.
    - **Resourcegroep**: Selecteer **Nieuw maken** en voer *myResourceGroupB in*
    - **Locatie**: *Oost-VS*

14. Typ *myVnetB*in het vak **Zoekresources** boven aan de portal . Klik **op myVnetB** wanneer deze in de zoekresultaten wordt weergegeven. Er verschijnt een mes voor het **virtuele myVnetB-netwerk.**
15. Klik in het **myVnetB-blad** dat wordt weergegeven op **Eigenschappen** uit de verticale lijst met opties aan de linkerkant van het blad. Kopieer de **RESOURCE-id**, die in een latere stap wordt gebruikt. De resource-id is vergelijkbaar met het volgende voorbeeld:`/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`
16. Voltooi stap 5-9 voor myVnetB en voer **UserA** in stap 8 in.
17. Meld u af bij de portal als UserB en log in als UserA.
18. Typ *myVnetA*in het vak **Zoekresources** boven aan de portal . Klik **op myVnetA** wanneer deze in de zoekresultaten wordt weergegeven. Er verschijnt een mes voor het **virtuele myVnet-netwerk.**
19. Klik op **myVnetA**.
20. Klik in het **myVnetA-blad** dat wordt weergegeven op **Peerings** uit de verticale lijst met opties aan de linkerkant van het blad.
21. In de **myVnetA - Peerings** mes dat verscheen, klik **+ Toevoegen**
22. Klik in het **peeringblad toevoegen** dat wordt weergegeven, voer de volgende opties in of selecteer deze op **OK:**
     - **Naam**: *myVnetAToMyVnetB*
     - **Virtueel netwerkimplementatiemodel:** **Selecteer Klassiek**.
     - **Ik weet mijn resource-ID:** Schakel dit selectievakje in.
     - **Resource-id:** voer de resource-id van myVnetB in vanaf stap 15.
     - **Virtuele netwerktoegang toestaan:** Controleer of **Ingeschakeld** is geselecteerd.
    Er worden geen andere instellingen gebruikt in deze zelfstudie. Lees [Virtuele netwerkpeeringen beheren](virtual-network-manage-peering.md#create-a-peering)voor meer informatie over alle peering-instellingen.
23. Na het klikken op **OK** in de vorige stap, de **Peering** blade toevoegen sluit en zie je de **myVnetA - Peerings** blade weer. Na een paar seconden verschijnt het peering dat u hebt gemaakt in het blad. **Verbonden** wordt vermeld in de kolom **PEERING STATUS** voor de **myVnetAToMyVnetB** peering die u hebt gemaakt. Het peering is nu gevestigd. Het is niet nodig om het virtuele netwerk (klassiek) te peeren naar het virtuele netwerk (Resource Manager).

    Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u standaard Azure-naamomzetting voor de virtuele netwerken gebruikt, kunnen de bronnen in de virtuele netwerken geen namen in de virtuele netwerken oplossen. Als u namen in virtuele netwerken in een peering wilt oplossen, moet u uw eigen DNS-server maken. Meer informatie over het instellen [van naamomzetting met uw eigen DNS-server.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

24. **Optioneel:** Hoewel het maken van virtuele machines niet in deze zelfstudie wordt behandeld, u in elk virtueel netwerk een virtuele machine maken en verbinding maken van de ene virtuele machine naar de andere om de connectiviteit te valideren.
25. **Optioneel:** voer de stappen in het gedeelte [Resources](#delete-portal) verwijderen van dit artikel uit om de resources die u in deze zelfstudie maakt, te verwijderen.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Peering maken - Azure CLI

Deze zelfstudie gebruikt verschillende accounts voor elk abonnement. Als u een account gebruikt met machtigingen voor beide abonnementen, u voor alle stappen hetzelfde account gebruiken, de stappen overslaan om u uit te loggen bij Azure en de regels voor scriptonderdelen verwijderen. Vervang UserA@azure.com UserB@azure.com en in alle volgende scripts met de gebruikersnamen die u gebruikt voor UserA en UserB. Voer de volgende stappen uit met de Azure-klassieke CLI en azure cli. U de stappen uitvoeren vanuit de Azure Cloud Shell door de knop **Probeer het** in een van de volgende stappen te selecteren, of door de [klassieke CLI](/cli/azure/install-classic-cli) en [CLI](/cli/azure/install-azure-cli) te installeren en de opdrachten op uw lokale computer uit te voeren.

1. Als u de Cloud Shell gebruikt, gaat u naar stap 2, omdat de Cloud Shell u automatisch aanmeldt bij Azure. Open een opdrachtsessie en meld `azure login` u aan bij Azure met de opdracht.
2. Voer de klassieke CLI in servicebeheermodus uit door de `azure config mode asm` opdracht in te voeren.
3. Voer de volgende klassieke CLI-opdracht in om het virtuele netwerk (klassiek) te maken:

    ```console
    azure network vnet create --vnet myVnetB --address-space 10.1.0.0 --cidr 16 --location "East US"
    ```

4. De resterende stappen moeten worden voltooid met behulp van een bash shell met de Azure CLI (niet de klassieke CLI).
5. Kopieer het volgende script naar een teksteditor op uw pc. Vervang `<SubscriptionB-Id>` door uw abonnements-ID. Als u uw abonnement-id niet `az account show` kent, voert u de opdracht in. De waarde voor **id** in de uitvoer is uw abonnements-id. Kopieer het gewijzigde `Enter`script, plak het in uw CLI-sessie en druk op .

    ```azurecli-interactive
    az role assignment create \
      --assignee UserA@azure.com \
      --role "Classic Network Contributor" \
      --scope /subscriptions/<SubscriptionB-Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

    Wanneer u het virtuele netwerk (klassiek) in stap 4 hebt gemaakt, heeft Azure het virtuele netwerk gemaakt in de brongroep *Standaardnetwerken.*
6. Log UserB uit azure en log in als UserA in de CLI.
7. Maak een resourcegroep en een virtueel netwerk (Resource Manager). Kopieer het volgende script, plak het in uw `Enter`CLI-sessie en druk op .

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroupA"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network A (Resource Manager).
    az network vnet create \
      --name myVnetA \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Get the id for myVnetA.
    vNetAId=$(az network vnet show \
      --resource-group $rgName \
      --name myVnetA \
      --query id --out tsv)

    # Assign UserB permissions to myVnetA.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope $vNetAId
    ```

8. Maak een virtueel netwerk peering tussen de twee virtuele netwerken gemaakt via de verschillende implementatiemodellen. Kopieer het volgende script naar een teksteditor op uw pc. Vervang `<SubscriptionB-id>` door uw abonnements-id. Als u uw abonnement-id niet `az account show` kent, voert u de opdracht in. De waarde voor **id** in de uitvoer is uw abonnements-id. Azure heeft het virtuele netwerk (klassiek) gemaakt dat u in stap 4 hebt gemaakt in een brongroep met de naam *Default-Networking*. Plak het gewijzigde script in uw `Enter`CLI-sessie en druk op .

    ```azurecli-interactive
    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnetAToMyVnetB \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --remote-vnet-id  /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB \
      --allow-vnet-access
    ```

9. Nadat het script is uitgevoerd, controleert u het peering voor het virtuele netwerk (Resource Manager). Kopieer het volgende script en plak het in uw CLI-sessie:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group $rgName \
      --vnet-name myVnetA \
      --output table
    ```

    De uitvoer toont **Verbonden** in de kolom **PeeringState.**

    Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u standaard Azure-naamomzetting voor de virtuele netwerken gebruikt, kunnen de bronnen in de virtuele netwerken geen namen in de virtuele netwerken oplossen. Als u namen in virtuele netwerken in een peering wilt oplossen, moet u uw eigen DNS-server maken. Meer informatie over het instellen [van naamomzetting met uw eigen DNS-server.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

10. **Optioneel:** Hoewel het maken van virtuele machines niet in deze zelfstudie wordt behandeld, u in elk virtueel netwerk een virtuele machine maken en verbinding maken van de ene virtuele machine naar de andere om de connectiviteit te valideren.
11. **Optioneel:** voer de stappen [in](#delete-cli) Bronnen in dit artikel verwijderen uit om de resources die u in deze zelfstudie maakt, te verwijderen.

## <a name="create-peering---powershell"></a><a name="powershell"></a>Peering maken - PowerShell

Deze zelfstudie gebruikt verschillende accounts voor elk abonnement. Als u een account gebruikt met machtigingen voor beide abonnementen, u voor alle stappen hetzelfde account gebruiken, de stappen overslaan om u uit te loggen bij Azure en de regels voor scriptonderdelen verwijderen. Vervang UserA@azure.com UserB@azure.com en in alle volgende scripts met de gebruikersnamen die u gebruikt voor UserA en UserB. 

1. Installeer de nieuwste versie van de PowerShell Azure- en [Az-modules.](https://www.powershellgallery.com/packages/Azure) [Az](https://www.powershellgallery.com/packages/Az) Zie [Overzicht van Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) als u nog geen ervaring hebt met Azure PowerShell.
2. Start een PowerShell-sessie.
3. Log in PowerShell in bij het abonnement van UserB als UserB door de `Add-AzureAccount` opdracht in te voeren. Het account waarmee u zich aanmeldt, moet over de benodigde machtigingen beschikken om een virtueel netwerkpeering te maken. Zie Machtigingen voor virtuele [netwerkpeering](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
4. Als u een virtueel netwerk (klassiek) wilt maken met PowerShell, moet u een nieuw netwerkconfiguratiebestand maken of wijzigen. Meer informatie over het [exporteren, bijwerken en importeren van netwerkconfiguratiebestanden](virtual-networks-using-network-configuration-file.md). Het bestand moet het volgende **VirtualNetworkSite-element** bevatten voor het virtuele netwerk dat in deze zelfstudie wordt gebruikt:

    ```xml
    <VirtualNetworkSite name="myVnetB" Location="East US">
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

5. Meld u aan bij het abonnement van UserB als UserB om Resource Manager-opdrachten te gebruiken door de `Connect-AzAccount` opdracht in te voeren.
6. Gebruikersmachtigingen toewijzen aan virtueel netwerk B. Kopieer het volgende script naar `<SubscriptionB-id>` een teksteditor op uw pc en vervang de id van abonnement B. Als u de abonnements-id niet `Get-AzSubscription` kent, voert u de opdracht in om deze te bekijken. De waarde voor **id** in de geretourneerde uitvoer is uw abonnements-ID. Azure heeft het virtuele netwerk (klassiek) gemaakt dat u in stap 4 hebt gemaakt in een brongroep met de naam *Default-Networking*. Als u het script wilt uitvoeren, kopieert u het `Enter`gewijzigde script, plakt u het in PowerShell en drukt u op .

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserA@azure.com `
      -RoleDefinitionName "Classic Network Contributor" `
      -Scope /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

7. Meld u af bij Azure als UserB en meld u aan `Connect-AzAccount` bij het abonnement van UserA als UserA door de opdracht in te voeren. Het account waarmee u zich aanmeldt, moet over de benodigde machtigingen beschikken om een virtueel netwerkpeering te maken. Zie Machtigingen voor virtuele [netwerkpeering](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
8. Maak het virtuele netwerk (Resource Manager) door het volgende script te kopiëren, `Enter`het in PowerShell te plakken en vervolgens op :

    ```powershell
    # Variables for common values
      $rgName='MyResourceGroupA'
      $location='eastus'

    # Create a resource group.
    New-AzResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network A.
    $vnetA = New-AzVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location
    ```

9. Gebruikersmachtigingen toewijzen aan myVnetA. Kopieer het volgende script naar een teksteditor op uw pc en vervang `<SubscriptionA-Id>` de ID van abonnement A. Als u de abonnements-id niet `Get-AzSubscription` kent, voert u de opdracht in om deze te bekijken. De waarde voor **id** in de geretourneerde uitvoer is uw abonnements-ID. Plak de gewijzigde versie van het script `Enter` in PowerShell en druk om het uit te voeren.

    ```powershell
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

10. Kopieer het volgende script naar een teksteditor `<SubscriptionB-id>` op uw pc en vervang de id van abonnement B. Als u myVnetA wilt peeren naar myVNetB, kopieert u `Enter`het gewijzigde script, plakt u het in PowerShell en drukt u op .

    ```powershell
    Add-AzVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vnetA `
      -RemoteVirtualNetworkId /subscriptions/<SubscriptionB-id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB
    ```

11. Bekijk de peering-status van myVnetA door het volgende script te `Enter`kopiëren, het in PowerShell te plakken en op te drukken .

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName $rgName `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    De status is **verbonden.** Het verandert **in Verbonden** zodra u het peering aan myVnetA van myVnetB instelt.

    Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u standaard Azure-naamomzetting voor de virtuele netwerken gebruikt, kunnen de bronnen in de virtuele netwerken geen namen in de virtuele netwerken oplossen. Als u namen in virtuele netwerken in een peering wilt oplossen, moet u uw eigen DNS-server maken. Meer informatie over het instellen [van naamomzetting met uw eigen DNS-server.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

12. **Optioneel:** Hoewel het maken van virtuele machines niet in deze zelfstudie wordt behandeld, u in elk virtueel netwerk een virtuele machine maken en verbinding maken van de ene virtuele machine naar de andere om de connectiviteit te valideren.
13. **Optioneel:** voer de stappen [in](#delete-powershell) Bronnen in dit artikel verwijderen uit om de resources die u in deze zelfstudie maakt, te verwijderen.

## <a name="delete-resources"></a><a name="delete"></a>Resources verwijderen
Wanneer u deze zelfstudie hebt voltooid, u de resources verwijderen die u in de zelfstudie hebt gemaakt, zodat u geen gebruikskosten hoeft te maken. Als u een resourcegroep verwijdert, worden ook alle resources in de resourcegroep verwijderd.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure-portal

1. Voer in het zoekvak van de portal **mijnResourceGroupA**in . Klik in de zoekresultaten op **myResourceGroupA**.
2. Klik op het **myResourceGroupA-blad** op het pictogram **Verwijderen.**
3. Als u de verwijdering wilt bevestigen, voert u in het vak **NAAM VAN DE BRONGROEP** **myResourceGroupA**in en klikt u op **Verwijderen**.
4. Typ *myVnetB*in het vak **Zoekresources** boven aan de portal . Klik **op myVnetB** wanneer deze in de zoekresultaten wordt weergegeven. Er verschijnt een mes voor het **virtuele myVnetB-netwerk.**
5. Klik in het **myVnetB-blad** op **Verwijderen**.
6. Als u de verwijdering wilt bevestigen, klikt u op **Ja** in het vak **Virtueel netwerk verwijderen.**

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure-CLI

1. Meld u aan bij Azure met de CLI om het virtuele netwerk (Resource Manager) met de volgende opdracht te verwijderen:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Meld u aan bij Azure met de klassieke CLI om het virtuele netwerk (klassiek) te verwijderen met de volgende opdrachten:

   ```console
   azure config mode asm

   azure network vnet delete --vnet myVnetB --quiet
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>Powershell

1. Voer bij de PowerShell-opdrachtprompt de volgende opdracht in om het virtuele netwerk (Resource Beheer) te verwijderen:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -Force
   ```

2. Als u het virtuele netwerk (klassiek) met PowerShell wilt verwijderen, moet u een bestaand netwerkconfiguratiebestand wijzigen. Meer informatie over het [exporteren, bijwerken en importeren van netwerkconfiguratiebestanden](virtual-networks-using-network-configuration-file.md). Verwijder het volgende VirtualNetworkSite-element voor het virtuele netwerk dat in deze zelfstudie wordt gebruikt:

   ```xml
   <VirtualNetworkSite name="myVnetB" Location="East US">
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