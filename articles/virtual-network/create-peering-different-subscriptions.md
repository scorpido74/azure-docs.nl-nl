---
title: Een VNet-peering maken - verschillende abonnementen
titlesuffix: Azure Virtual Network
description: Meer informatie over het maken van een virtueel netwerk dat kan worden gemaakt tussen virtuele netwerken die zijn gemaakt via Resource Beheer die in verschillende Azure-abonnementen bestaan.
services: virtual-network
documentationcenter: ''
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: anavin
ms.openlocfilehash: d085279167b498b13cfb79b97703cfdff7d6dd8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245120"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Een virtueel netwerk peering maken - Resource Manager, verschillende abonnementen

In deze zelfstudie leert u een virtueel netwerk peering te maken tussen virtuele netwerken die zijn gemaakt via Resource Manager. De virtuele netwerken bestaan in verschillende abonnementen. Met peering twee virtuele netwerken kunnen resources in verschillende virtuele netwerken met elkaar communiceren met dezelfde bandbreedte en latentie alsof de resources zich in hetzelfde virtuele netwerk bevinden. Meer informatie over [virtueel netwerkpeeren](virtual-network-peering-overview.md).

De stappen om een virtueel netwerkpeering te maken zijn verschillend, afhankelijk van of de virtuele netwerken zich in dezelfde of verschillende abonnementen bevinden en welk [Azure-implementatiemodel](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de virtuele netwerken worden gemaakt. Meer informatie over het maken van een virtueel netwerk peering in andere scenario's door het scenario te selecteren in de volgende tabel:

|Azure-implementatiemodel  | Azure-abonnement  |
|--------- |---------|
|[Beide in Resource Manager](tutorial-connect-virtual-networks-portal.md) |Hetzelfde|
|[Eén in Resource Manager, één klassiek](create-peering-different-deployment-models.md) |Hetzelfde|
|[Eén in Resource Manager, één klassiek](create-peering-different-deployment-models-subscriptions.md) |Verschillend|

Een virtueel netwerkpeering kan niet worden gemaakt tussen twee virtuele netwerken die via het klassieke implementatiemodel worden geïmplementeerd. Als u virtuele netwerken moet verbinden die beide zijn gemaakt via het klassieke implementatiemodel, u een Azure [VPN-gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) gebruiken om de virtuele netwerken met elkaar te verbinden.

In deze zelfstudie worden virtuele netwerken in dezelfde regio gepeers. U ook virtuele netwerken peerin verschillende [ondersteunde regio's](virtual-network-manage-peering.md#cross-region). Het wordt aanbevolen dat u vertrouwd te raken met de [peering eisen en beperkingen](virtual-network-manage-peering.md#requirements-and-constraints) voordat peering virtuele netwerken.

U de [Azure-portal,](#portal)de Azure [command-line interface](#cli) (CLI), Azure [PowerShell](#powershell)of een [Azure Resource Manager-sjabloon](#template) gebruiken om een virtueel netwerkpeering te maken. Selecteer een van de vorige gereedschapskoppelingen om rechtstreeks naar de stappen te gaan voor het maken van een virtueel netwerk peering met behulp van uw tool naar keuze.

Als de virtuele netwerken zich in verschillende abonnementen bevinden en de abonnementen zijn gekoppeld aan verschillende Azure Active Directory-tenants, voert u de volgende stappen uit voordat u verdergaat:
1. Voeg de gebruiker van elke Active Directory-tenant toe als [gastgebruiker](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) in de tegenovergestelde Azure Active Directory-tenant.
1. Elke gebruiker moet de uitnodiging voor gastgebruikers van de tegenovergestelde Azure Active Directory-tenant accepteren.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Peering maken - Azure-portal

In de volgende stappen worden voor elk abonnement verschillende accounts gebruikt. Als u een account gebruikt met machtigingen voor beide abonnementen, u voor alle stappen hetzelfde account gebruiken, de stappen overslaan om u uit te loggen bij de portal en de stappen overslaan voor het toewijzen van machtigingen van een andere gebruiker aan de virtuele netwerken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als *UserA*. Het account waarmee u zich aanmeldt, moet over de benodigde machtigingen beschikken om een virtueel netwerkpeering te maken. Zie Machtigingen voor virtuele [netwerkpeering](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
2. Selecteer **+ Een resource maken,** selecteer **Netwerken**en selecteer **Virtueel netwerk**.
3. Selecteer of voer de volgende voorbeeldwaarden in voor de volgende instellingen en selecteer **Vervolgens Maken:**
    - **Naam**: *myVnetA*
    - **Adresruimte**: *10.0.0.0/16*
    - **Subnetnaam**: *standaard*
    - **Subnetadresbereik**: *10.0.0.0/24*
    - **Abonnement**: Selecteer abonnement A.
    - **Resourcegroep**: Selecteer **Nieuw maken** en voer *myResourceGroupA* in
    - **Locatie**: *Oost-VS*
4. Typ *myVnetA*in het vak **Zoekresources** boven aan de portal . Selecteer **myVnetA** wanneer deze in de zoekresultaten wordt weergegeven. 
5. Selecteer **Toegangsbesturingselement (IAM)** in de verticale lijst met opties aan de linkerkant.
6. Onder **myVnetA - Access control (IAM)** selecteert u **+ Roltoewijzing toevoegen**.
7. Selecteer **Netwerkbijdrager** in het vak **Rol.**
8. Selecteer **in** het vak Selecteren de optie *UserB*of typ het e-mailadres van UserB om ernaar te zoeken.
9. Selecteer **Opslaan**.
10. Selecteer **Eigenschappen** in de verticale lijst met opties aan de linkerkant onder **myVnetA - Access control (IAM).** Kopieer de **RESOURCE-id**, die in een latere stap wordt gebruikt. De resource-id is vergelijkbaar `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`met het volgende voorbeeld: .
11. Meld u af bij de portal als UserA en meld u vervolgens aan als UserB.
12. Voer stap 2-3 uit en voer de volgende waarden in stap 3 in of selecteer deze:

    - **Naam**: *myVnetB*
    - **Adresruimte**: *10.1.0.0/16*
    - **Subnetnaam**: *standaard*
    - **Subnetadresbereik**: *10.1.0.0/24*
    - **Abonnement**: Selecteer abonnement B.
    - **Resourcegroep**: Selecteer **Nieuw maken** en voer *myResourceGroupB in*
    - **Locatie**: *Oost-VS*

13. Typ *myVnetB*in het vak **Zoekresources** boven aan de portal . Selecteer **myVnetB** wanneer deze in de zoekresultaten wordt weergegeven.
14. Selecteer **Eigenschappen** in de verticale lijst met opties aan de linkerkant onder **myVnetB.** Kopieer de **RESOURCE-id**, die in een latere stap wordt gebruikt. De resource-id is vergelijkbaar `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`met het volgende voorbeeld: .
15. Selecteer **Toegangsbeheer (IAM)** onder **myVnetB**en voer stap 5-10 voor myVnetB uit en voer **UserA** in stap 8 in.
16. Meld u af bij de portal als UserB en log in als UserA.
17. Typ *myVnetA*in het vak **Zoekresources** boven aan de portal . Selecteer **myVnetA** wanneer deze in de zoekresultaten wordt weergegeven.
18. Selecteer **myVnetA**.
19. Selecteer **Peerings**onder **INSTELLINGEN**.
20. Onder **myVnetA - Peerings**selecteert u **+ Toevoegen**
21. Selecteer **onder Peering toevoegen,** voer de volgende opties in of selecteer deze en selecteer **vervolgens OK:**
     - **Naam**: *myVnetAToMyVnetB*
     - **Virtueel netwerkimplementatiemodel:** **Resourcebeheer selecteren**.
     - **Ik weet mijn resource-ID:** Schakel dit selectievakje in.
     - **Resource-id:** voer de resource-id in vanaf stap 14.
     - **Virtuele netwerktoegang toestaan:** Controleer of **Ingeschakeld** is geselecteerd.
    Er worden geen andere instellingen gebruikt in deze zelfstudie. Lees [Virtuele netwerkpeeringen beheren](virtual-network-manage-peering.md#create-a-peering)voor meer informatie over alle peering-instellingen.
22. Het peering dat u hebt gemaakt, wordt een korte wachttijd weergegeven nadat u OK in de vorige stap **hebt** geselecteerd. **Gestart wordt** vermeld in de kolom **PEERING STATUS** voor de **myVnetAToMyVnetB** peering die u hebt gemaakt. Je hebt myVnetA naar myVnetB gekeken, maar nu moet je myVnetB naar myVnetA kijken. Het peering moet in beide richtingen worden gemaakt om resources in de virtuele netwerken in staat te stellen met elkaar te communiceren.
23. Meld u af bij de portal als UserA en meld u aan als UserB.
24. Complete stappen 17-21 weer voor myVnetB. Geef in stap 21 de naam peering *myVnetBToMyVnetA*, selecteer *myVnetA* voor **virtueel netwerk**en voer de ID in vanaf stap 10 in het vak **Resource-ID.**
25. Een paar seconden na het selecteren van **OK** om het peering voor myVnetB te maken, wordt de **myVnetBToMyVnetA-peering** die u zojuist hebt gemaakt, weergegeven met **Verbonden** in de kolom **PEERING STATUS.**
26. Meld u af bij de portal als UserB en log in als UserA.
27. Voltooi de stappen 17-19 opnieuw. De **PEERING STATUS** voor de **myVnetAToVNetB** peering is nu ook **Verbonden.** De peering is tot stand gekomen nadat u **Verbonden bent in** de kolom **PEERING STATUS** voor beide virtuele netwerken in peering. Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u standaard Azure-naamomzetting voor de virtuele netwerken gebruikt, kunnen de bronnen in de virtuele netwerken geen namen in de virtuele netwerken oplossen. Als u namen in virtuele netwerken in een peering wilt oplossen, moet u uw eigen DNS-server maken. Meer informatie over het instellen [van naamomzetting met uw eigen DNS-server.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
28. **Optioneel:** Hoewel het maken van virtuele machines niet in deze zelfstudie wordt behandeld, u in elk virtueel netwerk een virtuele machine maken en verbinding maken van de ene virtuele machine naar de andere om de connectiviteit te valideren.
29. **Optioneel:** voer de stappen in het gedeelte [Resources](#delete-portal) verwijderen van dit artikel uit om de resources die u in deze zelfstudie maakt, te verwijderen.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Peering maken - Azure CLI

Deze zelfstudie gebruikt verschillende accounts voor elk abonnement. Als u een account gebruikt met machtigingen voor beide abonnementen, u voor alle stappen hetzelfde account gebruiken, de stappen overslaan om u uit te loggen bij Azure en de regels voor scriptonderdelen verwijderen. Vervang UserA@azure.com UserB@azure.com en in alle volgende scripts met de gebruikersnamen die u gebruikt voor UserA en UserB. 

De volgende scripts:

- Hiervoor is de Azure CLI-versie 2.0.4 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)als u wilt upgraden.
- Werkt in een Bash shell. Zie [De Azure CLI installeren in Windows](/cli/azure/install-azure-cli-windows) voor opties voor het uitvoeren van Azure CLI-scripts in de Windows-client.

In plaats van de CLI en de afhankelijkheden ervan te installeren, u de Azure Cloud Shell gebruiken. De Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de knop **Probeer het** in het script dat volgt, waarin een Cloud Shell wordt aangeroepen waarmee u zich aanmelden bij uw Azure-account.

1. Open een CLI-sessie en meld u `azure login` aan bij Azure als UserA met de opdracht. Het account waarmee u zich aanmeldt, moet over de benodigde machtigingen beschikken om een virtueel netwerkpeering te maken. Zie Machtigingen voor virtuele [netwerkpeering](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
2. Kopieer het volgende script naar een teksteditor op uw pc, vervang `<SubscriptionA-Id>` de ID van SubscriptionA, kopieer `Enter`het gewijzigde script, plak het in uw CLI-sessie en druk op . Als u uw abonnement-id niet `az account show` kent, voert u de opdracht in. De waarde voor **id** in de uitvoer is uw abonnements-id.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. Meld u af bij Azure `az logout` als UserA met de opdracht en meld u vervolgens aan bij Azure als UserB. Het account waarmee u zich aanmeldt, moet over de benodigde machtigingen beschikken om een virtueel netwerkpeering te maken. Zie Machtigingen voor virtuele [netwerkpeering](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
4. Maak myVnetB. Kopieer de inhoud van het script in stap 2 naar een teksteditor op uw pc. Vervang `<SubscriptionA-Id>` door de ID van SubscriptionB. Wijzig 10.0.0.0/16 naar 10.1.0.0/16, wijzig alle B's en alle B's in A. Kopieer het `Enter`gewijzigde script, plak het in uw CLI-sessie en druk op .
5. Meld u af bij Azure als UserB en meld u aan bij Azure als UserA.
6. Maak een virtueel netwerk peering van myVnetA naar myVnetB. Kopieer de volgende scriptinhoud naar een teksteditor op uw pc. Vervang `<SubscriptionB-Id>` door de ID van SubscriptionB. Als u het script wilt uitvoeren, kopieert u het gewijzigde script, plakt u het in uw CLI-sessie en drukt u op Enter.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Bekijk de peering staat van myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    De staat wordt **gestart.** Het verandert **in Verbonden** zodra u het peering aan myVnetA van myVnetB maakt.

8. Meld UserA uit azure en meld u aan bij Azure als UserB.
9. Maak het peering van myVnetB naar myVnetA. Kopieer de inhoud van het script in stap 6 naar een teksteditor op uw pc. Vervang `<SubscriptionB-Id>` door de ID voor SubscriptionA en verander alle B's en alle B's in A. Zodra u de wijzigingen hebt aangebracht, kopieert u het gewijzigde `Enter`script, plakt u het in uw CLI-sessie en drukt u op .
10. Bekijk de peering staat van myVnetB. Kopieer de inhoud van het script in stap 7 naar een teksteditor op uw pc. Wijzig A in B voor de brongroep en virtuele netwerknamen, kopieer het script, `Enter`plak het gewijzigde script in uw CLI-sessie en druk op . De peeringstatus is **verbonden**. De peering-status van myVnetA verandert in **Verbonden** nadat u het peering hebt gemaakt van myVnetB naar myVnetA. U UserA opnieuw aanmelden bij Azure en stap 7 opnieuw voltooien om de peeringstatus van myVnetA te verifiëren. 

    > [!NOTE]
    > Het peering wordt pas vastgesteld als de peeringstatus is **verbonden** voor beide virtuele netwerken.

11. **Optioneel:** Hoewel het maken van virtuele machines niet in deze zelfstudie wordt behandeld, u in elk virtueel netwerk een virtuele machine maken en verbinding maken van de ene virtuele machine naar de andere om de connectiviteit te valideren.
12. **Optioneel:** voer de stappen [in](#delete-cli) Bronnen in dit artikel verwijderen uit om de resources die u in deze zelfstudie maakt, te verwijderen.

Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u standaard Azure-naamomzetting voor de virtuele netwerken gebruikt, kunnen de bronnen in de virtuele netwerken geen namen in de virtuele netwerken oplossen. Als u namen in virtuele netwerken in een peering wilt oplossen, moet u uw eigen DNS-server maken. Meer informatie over het instellen [van naamomzetting met uw eigen DNS-server.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

## <a name="create-peering---powershell"></a><a name="powershell"></a>Peering maken - PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Deze zelfstudie gebruikt verschillende accounts voor elk abonnement. Als u een account gebruikt met machtigingen voor beide abonnementen, u voor alle stappen hetzelfde account gebruiken, de stappen overslaan om u uit te loggen bij Azure en de regels voor scriptonderdelen verwijderen. Vervang UserA@azure.com UserB@azure.com en in alle volgende scripts met de gebruikersnamen die u gebruikt voor UserA en UserB.

1. Controleer of u Azure PowerShell-versie 1.0.0 of hoger hebt. U dit doen `Get-Module -Name Az` door de We recommend het installeren van de nieuwste versie van de PowerShell [Az module.](/powershell/azure/install-az-ps) Zie [Overzicht van Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json) als u nog geen ervaring hebt met Azure PowerShell. 
2. Start een PowerShell-sessie.
3. Log in PowerShell in bij Azure als `Connect-AzAccount` UserA door de opdracht in te voeren. Het account waarmee u zich aanmeldt, moet over de benodigde machtigingen beschikken om een virtueel netwerkpeering te maken. Zie Machtigingen voor virtuele [netwerkpeering](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
4. Maak een brongroep en virtueel netwerk A. Kopieer het volgende script naar een teksteditor op uw pc. Vervang `<SubscriptionA-Id>` door de ID van SubscriptionA. Als u uw abonnements-id niet `Get-AzSubscription` kent, voert u de opdracht in om deze te bekijken. De waarde voor **id** in de geretourneerde uitvoer is uw abonnements-ID. Als u het script wilt uitvoeren, kopieert u het `Enter`gewijzigde script, plakt u het in PowerShell en drukt u op .

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Meld UserA uit azure en log in UserB. Het account waarmee u zich aanmeldt, moet over de benodigde machtigingen beschikken om een virtueel netwerkpeering te maken. Zie Machtigingen voor virtuele [netwerkpeering](virtual-network-manage-peering.md#permissions)voor een lijst met machtigingen.
6. Kopieer de inhoud van het script in stap 4 naar een teksteditor op uw pc. Vervang `<SubscriptionA-Id>` de id voor abonnement B. Wijzig 10.0.0.0/16 naar 10.1.0.0/16. Verander alle B en alle B's in A. Als u het script wilt uitvoeren, kopieert u `Enter`het gewijzigde script, plakt u in PowerShell en drukt u op .
7. Meld UserB uit azure en log je aan bij UserA.
8. Maak het peering van myVnetA naar myVnetB. Kopieer het volgende script naar een teksteditor op uw pc. Vervang `<SubscriptionB-Id>` door de ID van abonnement B. Als u het script wilt uitvoeren, kopieert u het `Enter`gewijzigde script, plakt u in PowerShell en drukt u op .

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Bekijk de peering staat van myVnetA.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    De staat wordt **gestart.** Het verandert **in Verbonden** zodra u het peering aan myVnetA van myVnetB instelt.

10. Meld UserA uit azure en log in UserB.
11. Maak het peering van myVnetB naar myVnetA. Kopieer de inhoud van het script in stap 8 naar een teksteditor op uw pc. Vervang `<SubscriptionB-Id>` door de ID van abonnement A en verander alle B's en alle B's naar A. Als u het script wilt uitvoeren, kopieert u het `Enter`gewijzigde script, plakt u het in PowerShell en drukt u op .
12. Bekijk de peering staat van myVnetB. Kopieer de inhoud van het script in stap 9 naar een teksteditor op uw pc. Wijzig A in B voor de resourcegroep en namen van virtuele netwerken. Als u het script wilt uitvoeren, plakt `Enter`u het gewijzigde script in PowerShell en drukt u op . De status is **verbonden.** De peering-status van **myVnetA** verandert in **Connected** nadat u het peering hebt gemaakt van **myVnetB** naar **myVnetA.** U UserA opnieuw aanmelden bij Azure en stap 9 opnieuw voltooien om de peeringstatus van myVnetA te verifiëren.

    > [!NOTE]
    > Het peering wordt pas vastgesteld als de peeringstatus is **verbonden** voor beide virtuele netwerken.

    Alle Azure-resources die u in een virtueel netwerk maakt, kunnen nu met elkaar communiceren via hun IP-adressen. Als u standaard Azure-naamomzetting voor de virtuele netwerken gebruikt, kunnen de bronnen in de virtuele netwerken geen namen in de virtuele netwerken oplossen. Als u namen in virtuele netwerken in een peering wilt oplossen, moet u uw eigen DNS-server maken. Meer informatie over het instellen [van naamomzetting met uw eigen DNS-server.](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

13. **Optioneel:** Hoewel het maken van virtuele machines niet in deze zelfstudie wordt behandeld, u in elk virtueel netwerk een virtuele machine maken en verbinding maken van de ene virtuele machine naar de andere om de connectiviteit te valideren.
14. **Optioneel:** voer de stappen [in](#delete-powershell) Bronnen in dit artikel verwijderen uit om de resources die u in deze zelfstudie maakt, te verwijderen.

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>Peering maken - Sjabloon Resourcemanager

1. Als u een virtueel netwerk wilt maken en de juiste [machtigingen](virtual-network-manage-peering.md#permissions)wilt toewijzen, voert u de stappen uit in de secties [Portal,](#portal) [Azure CLI](#cli)of [PowerShell](#powershell) van dit artikel.
2. Sla de tekst die volgt op in een bestand op uw lokale computer. Vervang `<subscription ID>` door de abonnements-ID van UserA. U het bestand opslaan als bijvoorbeeld vnetpeeringA.json.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Meld u aan bij Azure als UserA en implementeer de sjabloon via de [portal,](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template) [PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template)of azure [CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Geef de bestandsnaam op waarop u de voorbeeldtekst hebt opgeslagen in stap 2.
4. Kopieer het voorbeeld json van stap 2 naar een bestand op uw computer en breng wijzigingen aan in de regels die beginnen met:
   - **naam**: *MyVnetA/myVnetAToMyVnetB* wijzigen in *myVnetB/myVnetBToMyVnetA*.
   - **id**: `<subscription ID>` Vervang de abonnements-ID van UserB en wijzig *myVnetB* in *myVnetA*.
5. Voltooi stap 3 opnieuw en logt in op Azure als UserB.
6. **Optioneel:** Hoewel het maken van virtuele machines niet in deze zelfstudie wordt behandeld, u in elk virtueel netwerk een virtuele machine maken en verbinding maken van de ene virtuele machine naar de andere om de connectiviteit te valideren.
7. **Optioneel:** voer de stappen in het gedeelte [Resources](#delete) verwijderen van dit artikel uit met de Azure-portal, PowerShell of Azure CLI als u de resources wilt verwijderen die u in deze zelfstudie maakt.

## <a name="delete-resources"></a><a name="delete"></a>Resources verwijderen
Wanneer u deze zelfstudie hebt voltooid, u de resources verwijderen die u in de zelfstudie hebt gemaakt, zodat u geen gebruikskosten hoeft te maken. Als u een resourcegroep verwijdert, worden ook alle resources in de resourcegroep verwijderd.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure-portal

1. Meld u aan bij de Azure-portal als UserA.
2. Voer in het zoekvak van de portal **mijnResourceGroupA**in . Selecteer **myResourceGroupA**in de zoekresultaten .
3. Selecteer **Verwijderen**.
4. Als u de verwijdering wilt bevestigen, voert u in het vak **NAAM VAN DE BRONGROEP** **myResourceGroupA**in en selecteert u **Verwijderen**.
5. Meld u af bij de portal als UserA en meld u aan als UserB.
6. Voer stap 2-4 uit voor myResourceGroupB.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure-CLI

1. Meld u aan bij Azure als UserA en voer de volgende opdracht uit:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Meld u af bij Azure als UserA en meld u aan als UserB.
3. Voer de volgende opdracht uit:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>Powershell

1. Meld u aan bij Azure als UserA en voer de volgende opdracht uit:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Meld u af bij Azure als UserA en meld u aan als UserB.
3. Voer de volgende opdracht uit:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Volgende stappen

- Maak jezelf grondig vertrouwd met belangrijke [beperkingen en gedragingen voor virtuele netwerken](virtual-network-manage-peering.md#requirements-and-constraints) voordat u een virtueel netwerk peering maakt voor productiegebruik.
- Meer informatie over alle [instellingen voor virtuele netwerkpeering](virtual-network-manage-peering.md#create-a-peering).
- Meer informatie over het [maken van een hub- en spoke-netwerktopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) met virtuele netwerkpeering.
