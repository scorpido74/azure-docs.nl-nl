---
title: Toegang op basis van virtueel netwerk configureren voor een Azure Cosmos-account
description: In dit document worden de stappen beschreven die nodig zijn om een eindpunt voor de virtuele netwerkservice in te stellen voor Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366234"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Toegang vanaf virtuele netwerken configureren (VNet)

U kunt Azure Cosmos DB-accounts configureren om alleen toegang toe te staan vanaf een specifiek subnet van een virtueel Azure-netwerk. Ga als lid van het e-mailaccount van Azure Cosmos met verbindingen van een subnet in een virtueel netwerk:

1. Schakel het subnet in om het subnet en de virtuele netwerkidentiteit naar Azure Cosmos DB te verzenden. U dit bereiken door een serviceeindpunt voor Azure Cosmos DB in te schakelen op het specifieke subnet.

1. Voeg een regel toe in het Azure Cosmos DB-account om het subnet op te geven als bron waaruit het account kan worden geopend.

> [!NOTE]
> Wanneer een serviceeindpunt voor uw Azure Cosmos DB-account is ingeschakeld op een subnet, schakelt de bron van het verkeer dat Azure Cosmos DB bereikt, over van een openbaar IP naar een virtueel netwerk en subnet. De verkeerwisseling is van toepassing op een Azure Cosmos DB-account dat is geopend vanaf dit subnet. Als uw Azure Cosmos DB-accounts een IP-gebaseerde firewall hebben om dit subnet toe te staan, komen aanvragen van het subnet met service-ingeschakeld niet langer overeen met de IP-firewallregels en worden ze afgewezen.
>
> Zie voor meer informatie de stappen die zijn beschreven in de [migratie van een IP-firewallregel naar een](#migrate-from-firewall-to-vnet) sectie met lijst met lijst met virtuele netwerktoegangsbeheer van dit artikel.

In de volgende secties wordt beschreven hoe u een eindpunt voor virtuele netwerkservice configureert voor een Azure Cosmos DB-account.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Een serviceeindpunt configureren met behulp van de Azure-portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Een serviceeindpunt configureren voor een bestaand virtueel Azure-netwerk en subnet

1. Zoek in het blade **Alle bronnen** het Azure Cosmos DB-account dat u wilt beveiligen.

1. Selecteer **Firewalls en virtuele netwerken** in het instellingenmenu en kies ervoor om toegang toe te staan vanuit geselecteerde **netwerken.**

1. Als u toegang wilt verlenen tot het subnet van een bestaand virtueel netwerk, selecteert u onder **Virtuele netwerken**de optie Bestaand **Virtueel Azure-netwerk toevoegen**.

1. Selecteer het **abonnement** waarvan u een virtueel Azure-netwerk wilt toevoegen. Selecteer de Azure **Virtual-netwerken** en **subnetten** die u toegang wilt bieden tot uw Azure Cosmos DB-account. Selecteer vervolgens **Inschakelen** om geselecteerde netwerken met serviceeindpunten in te schakelen voor "Microsoft.AzureCosmosDB". Als deze is voltooid, selecteert u **Toevoegen**.

   ![Virtueel netwerk en subnet selecteren](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Nadat het Azure Cosmos DB-account is ingeschakeld voor toegang vanuit een virtueel netwerk, wordt alleen verkeer van dit gekozen subnet mogelijk gemaakt. Het virtuele netwerk en subnet dat u hebt toegevoegd, moeten worden weergegeven zoals weergegeven in de volgende schermafbeelding:

   ![Virtueel netwerk en subnet dat is geconfigureerd](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Als u eindpunten voor virtuele netwerkservice wilt inschakelen, hebt u de volgende abonnementsmachtigingen nodig:
>   * Abonnement met virtueel netwerk: netwerkbijdrager
>   * Abonnement met Azure Cosmos DB-account: DocumentDB-accountbijdrager
>   * Als uw virtuele netwerk en Azure Cosmos DB-account zich in verschillende abonnementen `Microsoft.DocumentDB` bevinden, moet u ervoor zorgen dat het abonnement met een virtueel netwerk ook is geregistreerd als resourceprovider is geregistreerd. Zie Het artikel [Azure-bronproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md) als u een bronprovider wilt registreren.

Hier zijn de aanwijzingen voor het registreren van een abonnement bij resource provider.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Een serviceeindpunt configureren voor een nieuw virtueel Azure-netwerk en subnet

1. Zoek in het blade **Alle bronnen** het Azure Cosmos DB-account dat u wilt beveiligen.  

1. Selecteer **Firewalls en Azure virtuele netwerken** in het instellingenmenu en kies ervoor om toegang toe te staan vanuit geselecteerde **netwerken.**  

1. Als u toegang wilt verlenen tot een nieuw virtueel Azure-netwerk, selecteert u onder **Virtuele netwerken**de optie Nieuw virtueel **netwerk toevoegen**.  

1. Geef de details op die nodig zijn om een nieuw virtueel netwerk te maken en selecteer **Vervolgens Maken**. Het subnet wordt gemaakt met een serviceeindpunt voor "Microsoft.AzureCosmosDB" ingeschakeld.

   ![Een virtueel netwerk en subnet selecteren voor een nieuw virtueel netwerk](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Als uw Azure Cosmos DB-account wordt gebruikt door andere Azure-services, zoals Azure Cognitive Search, of is geopend vanuit Stream analytics of Power BI, geeft u toegang toe door Verbindingen accepteren te selecteren **vanuit wereldwijde Azure-datacenters.**

Als u vanuit de portal toegang hebt tot Azure Cosmos DB-statistieken, moet u **Toegang toestaan vanuit Azure-portalopties** inschakelen. Zie het artikel [Een IP-firewall configureren](how-to-configure-firewall.md) voor meer informatie over deze opties. Nadat u de toegang hebt ingeschakeld, selecteert **u Opslaan** om de instellingen op te slaan.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Een virtueel netwerk of subnet verwijderen

1. Zoek in het blade **Alle resources** het Azure Cosmos DB-account waarvoor u serviceeindpunten hebt toegewezen.  

1. Selecteer **Firewalls en virtuele netwerken** in het instellingenmenu.  

1. Als u een virtuele netwerk- of subnetregel wilt verwijderen, selecteert u **...** naast het virtuele netwerk of subnet en selecteert **u Verwijderen**.

   ![Een virtueel netwerk verwijderen](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Een serviceeindpunt configureren met Azure PowerShell

> [!NOTE]
> Wanneer u PowerShell of azure CLI gebruikt, moet u de volledige lijst met IP-filters en virtuele netwerkALS opgeven in parameters, niet alleen de parameters die moeten worden toegevoegd.

Gebruik de volgende stappen om een serviceeindpunt te configureren voor een Azure Cosmos DB-account met Azure PowerShell:  

1. Installeer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) en [meld u aan](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Schakel het serviceeindpunt in voor een bestaand subnet van een virtueel netwerk.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

1. Ontvang virtuele netwerkinformatie.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Een Cosmos DB Virtual Network-regel voorbereiden

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Azure Cosmos DB-accounteigenschappen bijwerken met de nieuwe eindpuntconfiguratie van het virtuele netwerk: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Voer de volgende opdracht uit om te controleren of uw Azure Cosmos DB-account is bijgewerkt met het eindpunt van de virtuele netwerkservice dat u in de vorige stap hebt geconfigureerd:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Een serviceeindpunt configureren met de Azure CLI

Azure Cosmos-accounts kunnen worden geconfigureerd voor serviceeindpunten wanneer ze op een later tijdstip worden gemaakt of bijgewerkt als het subnet er al voor is geconfigureerd. Serviceeindpunten kunnen ook worden ingeschakeld op het Cosmos-account waar het subnet nog niet voor hen is geconfigureerd en vervolgens beginnen te werken wanneer het subnet later is geconfigureerd. Deze flexibiliteit zorgt ervoor dat beheerders die geen toegang hebben tot zowel het Cosmos-account als de virtuele netwerkbronnen, hun configuraties onafhankelijk van elkaar kunnen maken.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Maak een nieuw Cosmos-account aan en verbind deze met een back-end subnet voor een nieuw virtueel netwerk

In dit voorbeeld wordt het virtuele netwerk en subnet gemaakt met serviceeindpunten die zijn ingeschakeld voor beide wanneer ze worden gemaakt.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Een Cosmos-account verbinden en configureren met een back-endsubnet onafhankelijk

Dit voorbeeld is bedoeld om te laten zien hoe u een Azure Cosmos-account koppelen aan een bestaand nieuw virtueel netwerk waarbij het subnet nog niet is geconfigureerd voor serviceeindpunten. Dit gebeurt met `--ignore-missing-vnet-service-endpoint` behulp van de parameter. Hierdoor kan de configuratie voor het Cosmos-account foutloos worden voltooid voordat de configuratie van het subnet van het virtuele netwerk is voltooid. Zodra de subnetconfiguratie is voltooid, is het Cosmos-account toegankelijk via het geconfigureerde subnet.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migreren van een IP-firewallregel naar een acl voor virtueel netwerk

Als u een Azure Cosmos DB-account wilt migreren van HET gebruik van IP-firewallregels naar het gebruik van eindpunten voor virtuele netwerkservices, gebruikt u de volgende stappen.

Nadat een Azure Cosmos DB-account is geconfigureerd voor een serviceeindpunt voor een subnet, worden aanvragen van dat subnet verzonden naar Azure Cosmos DB met virtuele netwerk- en subnetbrongegevens in plaats van een openbaar IP-adres van de bron. Deze aanvragen komen niet langer overeen met een IP-filter dat is geconfigureerd op het Azure Cosmos DB-account, daarom zijn de volgende stappen nodig om downtime te voorkomen.

Schakel het eindpunt van de Azure Cosmos DB-service op het virtuele netwerk en subnet in met de bovenstaande stap in 'Het serviceeindpunt inschakelen voor een bestaand subnet van een virtueel netwerk'.

1. Informatie over virtuele netwerken en subnets:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Een nieuw object met de regel van het virtuele netwerk voorbereiden voor het Azure Cosmos DB-account:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Werk het Azure Cosmos DB-account bij om serviceeindpunttoegang vanaf het subnet in te schakelen:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Herhaal de vorige stappen voor alle Azure Cosmos DB-accounts die vanaf het subnet worden geopend.

1. Verwijder de IP-firewallregel voor het subnet uit de Firewallregels van het Azure Cosmos DB-account.

## <a name="next-steps"></a>Volgende stappen

* Zie het ondersteuningsartikel Firewall om een firewall voor Azure Cosmos DB [te](firewall-support.md) configureren.
