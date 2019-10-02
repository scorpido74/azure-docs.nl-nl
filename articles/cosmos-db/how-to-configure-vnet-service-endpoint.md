---
title: Virtueel netwerk en op subnet gebaseerde toegang voor uw Azure Cosmos DB-account configureren
description: In dit document worden de stappen beschreven die nodig zijn voor het instellen van een service-eind punt voor een virtueel netwerk voor Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 1c81045408a948820c8b9fef56e2c7d69cd39e08
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811919"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>Toegang vanaf virtuele netwerken (VNet) configureren

U kunt Azure Cosmos DB accounts configureren om alleen toegang toe te staan vanaf een specifiek subnet van een virtueel Azure-netwerk. De toegang tot een Azure Cosmos DB account beperken met verbindingen van een subnet in een virtueel netwerk:

1. Schakel het subnet in om de identiteit van het subnet en het virtuele netwerk naar Azure Cosmos DB te verzenden. U kunt dit doen door een service-eind punt in te scha kelen voor Azure Cosmos DB op het specifieke subnet.

1. Voeg een regel toe aan het Azure Cosmos DB-account om het subnet op te geven als bron waarvan het account toegang kan krijgen.

> [!NOTE]
> Wanneer een service-eind punt voor uw Azure Cosmos DB-account in een subnet is ingeschakeld, wordt de bron van het verkeer dat Azure Cosmos DB overschakelt van een openbaar IP-adres naar een virtueel netwerk en subnet. Het wisselen van verkeer is van toepassing op alle Azure Cosmos DB accounts die vanuit dit subnet toegankelijk zijn. Als uw Azure Cosmos DB accounts een op IP gebaseerde firewall hebben om dit subnet toe te staan, komen aanvragen van het service-subnet niet meer overeen met de IP-firewall regels en worden deze geweigerd.
>
> Zie de stappen die worden beschreven in de sectie [migreren van een IP-firewall regel naar een lijst met virtuele netwerk toegangs beheer](#migrate-from-firewall-to-vnet) in dit artikel voor meer informatie.

In de volgende secties wordt beschreven hoe u een service-eind punt voor een virtueel netwerk configureert voor een Azure Cosmos DB-account.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a id="configure-using-portal"></a>Een service-eind punt configureren met behulp van de Azure Portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Een service-eind punt configureren voor een bestaand virtueel Azure-netwerk en-subnet

1. Ga op de Blade **alle resources** naar het Azure Cosmos DB account dat u wilt beveiligen.

1. Selecteer **firewalls en virtuele netwerken** in het menu instellingen en kies ervoor om toegang vanaf **geselecteerde netwerken**toe te staan.

1. Als u toegang wilt verlenen tot een bestaand subnet van een virtueel netwerk, selecteert u onder **virtuele netwerken** **bestaande Azure Virtual Network toevoegen**.

1. Selecteer het **abonnement** waarvan u een virtueel Azure-netwerk wilt toevoegen. Selecteer de virtuele Azure- **netwerken** en **subnetten** die u toegang wilt verlenen tot uw Azure Cosmos DB-account. Selecteer vervolgens **inschakelen** om geselecteerde netwerken met Service-eind punten in te scha kelen voor ' micro soft. AzureCosmosDB '. Wanneer het is voltooid, selecteert u **toevoegen**.

   ![Virtueel netwerk en subnet selecteren](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Nadat het Azure Cosmos DB-account is ingeschakeld voor toegang vanuit een virtueel netwerk, is verkeer alleen toegestaan vanuit het gekozen subnet. Het virtuele netwerk en het subnet dat u hebt toegevoegd, moeten worden weer gegeven zoals in de volgende scherm afbeelding:

   ![Het virtuele netwerk en het subnet zijn geconfigureerd](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> Als u service-eind punten voor virtuele netwerken wilt inschakelen, hebt u de volgende abonnements machtigingen nodig:
>   * Abonnement met virtueel netwerk: Inzender voor netwerken
>   * Abonnement met Azure Cosmos DB account: Inzender voor DocumentDB-accounts
>   * Als uw virtuele netwerk en Azure Cosmos DB account zich in verschillende abonnementen bevinden, moet u ervoor zorgen dat er voor het abonnement met het virtuele netwerk ook een `Microsoft.DocumentDB`-Resource provider is geregistreerd. Als u een resource provider wilt registreren, raadpleegt u het artikel [Azure-resource providers en-typen](../azure-resource-manager/resource-manager-supported-services.md) .

Hier vindt u de instructies voor het registreren van een abonnement bij een resource provider.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Een service-eind punt configureren voor een nieuw virtueel Azure-netwerk en-subnet

1. Ga op de Blade **alle resources** naar het Azure Cosmos DB account dat u wilt beveiligen.  

1. Selecteer **firewalls en virtuele netwerken van Azure** in het menu instellingen en kies ervoor om toegang vanaf **geselecteerde netwerken**toe te staan.  

1. Als u toegang wilt verlenen tot een nieuw virtueel Azure-netwerk, selecteert u onder **virtuele netwerken** **nieuwe virtuele netwerk toevoegen**.  

1. Geef de vereiste gegevens op voor het maken van een nieuw virtueel netwerk en selecteer vervolgens **maken**. Het subnet wordt gemaakt met een service-eind punt voor ' micro soft. AzureCosmosDB ' ingeschakeld.

   ![Een virtueel netwerk en subnet selecteren voor een nieuw virtueel netwerk](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Als uw Azure Cosmos DB-account wordt gebruikt door andere Azure-Services zoals Azure Search, of als het is geopend vanuit stream Analytics of Power BI, kunt u toegang toestaan door verbindingen accepteren te selecteren in de **wereld wijde Azure-data centers**.

Om ervoor te zorgen dat u toegang hebt tot Azure Cosmos DB metrische gegevens uit de portal, moet u **toegang toestaan via Azure Portal** opties inschakelen. Zie het artikel [een IP-Firewall configureren](how-to-configure-firewall.md) voor meer informatie over deze opties. Nadat u toegang hebt ingeschakeld, selecteert u **Opslaan** om de instellingen op te slaan.

## <a id="remove-vnet-or-subnet"></a>Een virtueel netwerk of subnet verwijderen

1. Ga op de Blade **alle resources** naar het Azure Cosmos DB account waarvoor u service-eind punten hebt toegewezen.  

2. Selecteer **firewalls en virtuele netwerken** in het menu instellingen.  

3. Als u een virtuele netwerk-of subnet-regel wilt verwijderen, selecteert u **...** naast het virtuele netwerk of subnet en selecteert u **verwijderen**.

   ![Een virtueel netwerk verwijderen](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4. Selecteer **Opslaan** om uw wijzigingen toe te passen.

## <a id="configure-using-powershell"></a>Een service-eind punt configureren met behulp van Azure PowerShell

> [!NOTE]
> Wanneer u Power shell of de Azure CLI gebruikt, moet u ervoor zorgen dat u de volledige lijst met IP-filters en virtuele netwerk-Acl's opgeeft in para meters, niet alleen de items die moeten worden toegevoegd.

Gebruik de volgende stappen om een service-eind punt te configureren voor een Azure Cosmos DB-account met behulp van Azure PowerShell:  

1. Installeer [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) en [Meld u aan](https://docs.microsoft.com/powershell/azure/authenticate-azureps).  

1. Schakel het service-eind punt in voor een bestaand subnet van een virtueel netwerk.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
   ```

1. Gegevens van virtueel netwerk ophalen.

   ```powershell
   $vnProp = Get-AzVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. Haal eigenschappen van het Azure Cosmos DB-account op door de volgende cmdlet uit te voeren:  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initialiseer de variabelen voor later gebruik. Stel alle variabelen in van de bestaande account definitie.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Werk Azure Cosmos DB account eigenschappen bij met de nieuwe configuratie door de volgende cmdlets uit te voeren: 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. Voer de volgende opdracht uit om te controleren of uw Azure Cosmos DB-account is bijgewerkt met het virtuele netwerk service-eind punt dat u in de vorige stap hebt geconfigureerd:

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Een service-eind punt configureren met behulp van de Azure CLI

Azure Cosmos-accounts kunnen worden geconfigureerd voor service-eind punten wanneer deze op een later tijdstip worden gemaakt of bijgewerkt als het subnet al voor hen is geconfigureerd. Service-eind punten kunnen ook worden ingeschakeld voor het Cosmos-account waarin het subnet nog niet is geconfigureerd en die vervolgens zal werken wanneer het subnet later wordt geconfigureerd. Met deze flexibiliteit kunnen beheerders die geen toegang hebben tot zowel het Cosmos-account als de virtuele netwerk bronnen, hun configuraties onafhankelijk van elkaar maken.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Een nieuw Cosmos-account maken en deze verbinden met een back-end-subnet voor een nieuw virtueel netwerk

In dit voor beeld wordt het virtuele netwerk en het subnet gemaakt met Service-eind punten die zijn ingeschakeld voor beide wanneer ze worden gemaakt.

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

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Verbinding maken en configureren van een Cosmos-account op een back-end-subnet onafhankelijk

Dit voor beeld is bedoeld om te laten zien hoe u een Azure Cosmos-account verbindt met een bestaand nieuw virtueel netwerk waar het subnet nog niet is geconfigureerd voor service-eind punten. Dit wordt gedaan met behulp van de para meter `--ignore-missing-vnet-service-endpoint`. Hierdoor kan de configuratie voor het Cosmos-account zonder fouten worden voltooid voordat de configuratie naar het subnet van het virtuele netwerk is voltooid. Zodra de configuratie van het subnet is voltooid, is het Cosmos-account toegankelijk via het geconfigureerde subnet.

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

## <a id="migrate-from-firewall-to-vnet"></a>Migreren van een IP-firewall regel naar een virtuele netwerk-ACL

Gebruik de volgende stappen alleen voor Azure Cosmos DB accounts met bestaande IP-firewall regels die een subnet toestaan, wanneer u virtuele netwerk-en op subnet gebaseerde Acl's wilt gebruiken in plaats van een IP-firewall regel.

Nadat een service-eind punt voor een Azure Cosmos DB-account is ingeschakeld voor een subnet, worden de aanvragen verzonden met een bron die virtuele netwerk gegevens en subnetgegevens bevat in plaats van een openbaar IP-adres. Deze aanvragen komen niet overeen met een IP-filter. Deze bron switch treedt op voor alle Azure Cosmos DB accounts die worden geopend vanuit het subnet waarvoor een service-eind punt is ingeschakeld. Gebruik de volgende stappen om uitval tijd te voor komen:

1. Haal eigenschappen van het Azure Cosmos DB-account op door de volgende cmdlet uit te voeren:

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. Initialiseer de variabelen om deze later te gebruiken. Stel alle variabelen in van de bestaande account definitie. Voeg de toegangs beheer lijst van het virtuele netwerk toe aan alle Azure Cosmos DB accounts waartoe toegang wordt verkregen vanuit het subnet met de vlag `ignoreMissingVNetServiceEndpoint`.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. Werk Azure Cosmos DB account eigenschappen bij met de nieuwe configuratie door de volgende cmdlets uit te voeren:

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. Herhaal stap 1-3 voor alle Azure Cosmos DB accounts waartoe u toegang hebt vanuit het subnet.

1.  Wacht 15 minuten en werk vervolgens het subnet bij om het service-eind punt in te scha kelen.

1.  Schakel het service-eind punt in voor een bestaand subnet van een virtueel netwerk.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
    ```

1. Verwijder de IP-firewall regel voor het subnet.

## <a name="next-steps"></a>Volgende stappen

* Zie het artikel [firewall ondersteuning](firewall-support.md) voor het configureren van een firewall voor Azure Cosmos db.
