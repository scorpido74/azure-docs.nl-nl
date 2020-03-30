---
title: Azure Private Link configureren voor een Azure Cosmos-account
description: Meer informatie over het instellen van Azure Private Link om toegang te krijgen tot een Azure Cosmos-account met behulp van een privé-IP-adres in een virtueel netwerk.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 944341b1ef88c7e3d64a74536720eb9fb1d17321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152737"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Azure Private Link configureren voor een Azure Cosmos-account

Door Azure Private Link te gebruiken, u verbinding maken met een Azure Cosmos-account via een privéeindpunt. Het privéeindpunt is een set privé-IP-adressen in een subnet binnen uw virtuele netwerk. U vervolgens de toegang tot een Azure Cosmos-account beperken via privé-IP-adressen. Wanneer Private Link wordt gecombineerd met een beperkt NSG-beleid, helpt het het risico op gegevensexfiltratie te verminderen. Zie het artikel Azure Private [Link](../private-link/private-link-overview.md) voor meer informatie over privéeindpunten.

Met Private Link kunnen gebruikers toegang krijgen tot een Azure Cosmos-account vanuit het virtuele netwerk of vanuit een virtueel netwerk met een peered. Resources die zijn toegewezen aan Private Link zijn ook on-premises toegankelijk via private peering via VPN of Azure ExpressRoute. 

U verbinding maken met een Azure Cosmos-account dat is geconfigureerd met Private Link met behulp van de automatische of handmatige goedkeuringsmethode. Zie de sectie [Goedkeuringswerkstroom](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) van de privékoppelingsdocumentatie voor meer informatie. 

In dit artikel worden de stappen beschreven om een privéeindpunt te maken. Het gaat ervan uit dat u de automatische goedkeuringsmethode gebruikt.

> [!NOTE]
> Ondersteuning voor privéeindpunten is momenteel algemeen alleen beschikbaar in ondersteunde regio's voor de gateway-verbindingsmodus. Voor de directe modus is het beschikbaar als voorbeeldfunctie.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Een privéeindpunt maken met behulp van de Azure-portal

Gebruik de volgende stappen om een privéeindpunt voor een bestaand Azure Cosmos-account te maken met behulp van de Azure-portal:

1. Kies in het deelvenster **Alle bronnen** een Azure Cosmos-account.

1. Selecteer **Privéeindpuntverbindingen** in de lijst met instellingen en selecteer **Vervolgens Privéeindpunt:**

   ![Selecties voor het maken van een privéeindpunt in de Azure-portal](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. Voer in het deelvenster **Een privéeindpunt maken - Basisbeginselen** de volgende details in of selecteer u:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectdetails** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer een resourcegroep.|
    | **Instantiedetails** |  |
    | Name | Voer een naam in voor uw privéeindpunt. Als deze naam wordt aangenomen, maakt u een unieke naam. |
    |Regio| Selecteer het gebied waar u Privékoppeling wilt implementeren. Maak het privéeindpunt op dezelfde locatie waar uw virtuele netwerk bestaat.|
    |||
1. Selecteer **Volgende: Resource**.
1. Voer in **Een privéeindpunt maken - Resource**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer **Verbinding maken met een Azure-bron in mijn map**. <br/><br/> U vervolgens een van uw resources kiezen om Privékoppeling in te stellen. Of u verbinding maken met de bron van iemand anders met behulp van een bron-ID of alias die ze met u hebben gedeeld.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **Microsoft.AzureCosmosDB/databaseAccounts**. |
    | Resource |Selecteer uw Azure Cosmos-account. |
    |Subresource target |Selecteer het Azure Cosmos DB API-type dat u wilt toewijzen. Dit is standaard slechts één keuze voor de SQL-, MongoDB- en Cassandra-API's. Voor de Gremlin- en Tabel-API's u ook kiezen voor **Sql** omdat deze API's interoperabel zijn met de SQL API. |
    |||

1. Selecteer **Volgende: Configuratie**.
1. Voer **in Een privéeindpunt maken - Configuratie**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**Networking**| |
    | Virtueel netwerk| Selecteer uw virtuele netwerk. |
    | Subnet | Selecteer uw subnet. |
    |**Private DNS-integratie**||
    |Integreren met private DNS-zone |Selecteer **Ja**. <br><br/> Als u privé verbinding wilt maken met uw privéeindpunt, hebt u een DNS-record nodig. We raden u aan uw privéeindpunt te integreren met een privé-DNS-zone. U ook uw eigen DNS-servers gebruiken of DNS-records maken met behulp van de hostbestanden op uw virtuele machines. |
    |Privé-DNS-zone |Selecteer **privatelink.documents.azure.com**. <br><br/> De private DNS-zone wordt automatisch bepaald. U het niet wijzigen door de Azure-portal te gebruiken.|
    |||

1. Selecteer **Controleren + maken**. Op de pagina **Controleren + maken** valideert Azure uw configuratie.
1. Wanneer u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken**.

Wanneer u Private Link voor een Azure Cosmos-account hebt goedgekeurd, is in de Azure-portal de optie **Alle netwerken** in het deelvenster Firewall en **virtuele netwerken** niet beschikbaar.

In de volgende tabel wordt de toewijzing tussen verschillende Azure Cosmos-accountAPI-typen, ondersteunde subresources en de bijbehorende namen van de privézone weergegeven. U hebt ook toegang tot de Gremlin- en Table API-accounts via de SQL API, dus er zijn twee vermeldingen voor deze API's.

|API-type Azure Cosmos-account  |Ondersteunde subresources (of groeps-i-adressen) |Naam privézone  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tabel    |    Tabel     |   privatelink.table.cosmos.azure.com    |
|Tabel     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>De privé-IP-adressen ophalen

Nadat het privéeindpunt is ingericht, u de IP-adressen opvragen. Ga als het gaat om het weergeven van de IP-adressen vanuit de Azure-portal:

1. Selecteer **Alle resources**.
1. Zoek naar het privéeindpunt dat u eerder hebt gemaakt. In dit geval is het **cdbPrivateEndpoint3**.
1. Selecteer het tabblad **Overzicht** om de DNS-instellingen en IP-adressen te bekijken.

![Privé-IP-adressen in de Azure-portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Per privéeindpunt worden meerdere IP-adressen gemaakt:

* Eén voor het globale (regio-agnostische) eindpunt van het Azure Cosmos-account
* Eén voor elke regio waar het Azure Cosmos-account wordt geïmplementeerd

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Een privéeindpunt maken met Azure PowerShell

Voer het volgende PowerShell-script uit om een privéeindpunt met de naam 'MyPrivateEndpoint' te maken voor een bestaand Azure Cosmos-account. Vervang de variabele waarden door de details voor uw omgeving.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Het privéeindpunt integreren met een privé-DNS-zone

Nadat u het privéeindpunt hebt gemaakt, u het integreren met een privé-DNS-zone met behulp van het volgende PowerShell-script:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>De privé-IP-adressen ophalen

Nadat het privéeindpunt is ingericht, u de IP-adressen en de FQDN-toewijzing opvragen met behulp van het volgende PowerShell-script:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Een privéeindpunt maken met Azure CLI

Voer het volgende Azure CLI-script uit om een privéeindpunt met de naam 'myPrivateEndpoint' te maken voor een bestaand Azure Cosmos-account. Vervang de variabele waarden door de details voor uw omgeving.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Het privéeindpunt integreren met een privé-DNS-zone

Nadat u het privéeindpunt hebt gemaakt, u het integreren met een privé-DNS-zone met behulp van het volgende Azure CLI-script:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Een privéeindpunt maken met behulp van een resourcemanagersjabloon

U Privékoppeling instellen door een privéeindpunt te maken in een virtueel netwerksubnet. U bereikt dit door een Azure Resource Manager-sjabloon te gebruiken.

Gebruik de volgende code om een resourcemanagersjabloon met de naam 'PrivateEndpoint_template.json' te maken. Met deze sjabloon wordt een privéeindpunt gemaakt voor een bestaand Azure Cosmos SQL API-account in een bestaand virtueel netwerk.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Het parametersbestand voor de sjabloon definiëren**

Maak een parametersbestand voor de sjabloon en noem de naam 'PrivateEndpoint_parameters.json'. Voeg de volgende code toe aan het parametersbestand:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**De sjabloon implementeren met behulp van een PowerShell-script**

Maak een PowerShell-script met de volgende code. Voordat u het script uitvoert, vervangt u de abonnements-ID, de naam van de resourcegroep en andere variabele waarden door de details voor uw omgeving.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

In het PowerShell-script kan de `GroupId` variabele slechts één waarde bevatten. Die waarde is het API-type van het account. Toegestane `Sql`waarden zijn: `MongoDB` `Cassandra`, `Gremlin`, `Table`, en . Sommige Azure Cosmos-accounttypen zijn toegankelijk via meerdere API's. Bijvoorbeeld:

* Een Gremlin API-account is toegankelijk vanaf zowel Gremlin- als SQL API-accounts.
* Een Tabel API-account is toegankelijk via zowel Tabel- als SQL API-accounts.

Voor deze accounts moet u één privéeindpunt maken voor elk API-type. Het bijbehorende API-type `GroupId` wordt opgegeven in de array.

Nadat de sjabloon is geïmplementeerd, ziet u een uitvoer die vergelijkbaar is met wat de volgende afbeelding weergeeft. De `provisioningState` waarde `Succeeded` is als de privéeindpunten correct zijn ingesteld.

![Implementatieuitvoer voor de sjabloon Resourcebeheer](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Nadat de sjabloon is geïmplementeerd, worden de privé-IP-adressen gereserveerd in het subnet. De firewallregel van het Azure Cosmos-account is geconfigureerd om alleen verbindingen vanaf het privéeindpunt te accepteren.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Het privéeindpunt integreren met een privé-DNS-zone

Gebruik de volgende code om een resourcemanagersjabloon met de naam 'PrivateZone_template.json' te maken. Met deze sjabloon wordt een privé-DNS-zone gemaakt voor een bestaand Azure Cosmos SQL API-account in een bestaand virtueel netwerk.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

Gebruik de volgende code om een resourcemanagersjabloon met de naam 'PrivateZoneRecords_template.json' te maken.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**Het parametersbestand voor de sjabloon definiëren**

Maak de volgende twee parameters bestand voor de sjabloon. Maak de 'PrivateZone_parameters.json'. met de volgende code:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Maak de 'PrivateZoneRecords_parameters.json'. met de volgende code:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**De sjabloon implementeren met behulp van een PowerShell-script**

Maak een PowerShell-script met de volgende code. Voordat u het script uitvoert, vervangt u de abonnements-ID, de naam van de resourcegroep en andere variabele waarden door de details voor uw omgeving.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Aangepaste DNS configureren

U moet een privé-DNS-zone gebruiken in het subnet waar u het privéeindpunt hebt gemaakt. Configureer de eindpunten zodat elk privé-IP-adres wordt toegewezen aan een DNS-item. (Zie `fqdns` de eigenschap in het antwoord dat eerder is weergegeven.)

Wanneer u het privéeindpunt maakt, u het integreren met een privé-DNS-zone in Azure. Als u ervoor kiest om in plaats daarvan een aangepaste DNS-zone te gebruiken, moet u deze configureren om DNS-records toe te voegen voor alle privé-IP-adressen die zijn gereserveerd voor het privéeindpunt.

## <a name="private-link-combined-with-firewall-rules"></a>Private Link gecombineerd met firewallregels

De volgende situaties en resultaten zijn mogelijk wanneer u Private Link gebruikt in combinatie met firewallregels:

* Als u geen firewallregels configureert, heeft al het verkeer standaard toegang tot een Azure Cosmos-account.

* Als u openbaar verkeer of een serviceeindpunt configureert en privéeindpunten maakt, worden verschillende typen binnenkomend verkeer geautoriseerd door het bijbehorende type firewallregel.

* Als u geen eindpunt voor openbaar verkeer of service configureert en privéeindpunten maakt, is het Azure Cosmos-account alleen toegankelijk via de privéeindpunten. Als u geen openbaar verkeer of een serviceeindpunt configureert, nadat alle goedgekeurde privéeindpunten zijn geweigerd of verwijderd, staat het account open voor het hele netwerk.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Een privéeindpunt bijwerken wanneer u een regio toevoegt of verwijdert

Voor het toevoegen of verwijderen van regio's aan een Azure Cosmos-account moet u DNS-vermeldingen voor dat account toevoegen of verwijderen. Nadat regio's zijn toegevoegd of verwijderd, u de privé-DNS-zone van het subnet bijwerken om de toegevoegde of verwijderde DNS-vermeldingen en de bijbehorende privé-IP-adressen weer te geven.

Stel je voor dat je een Azure Cosmos-account implementeert in drie regio's: 'West US', 'Central US' en 'West-Europa'. Wanneer u een privéeindpunt voor uw account maakt, worden vier privé-IP's gereserveerd in het subnet. Er is één IP voor elk van de drie regio's en er is één IP voor het wereldwijde/regio-agnostische eindpunt.

Later u een nieuwe regio (bijvoorbeeld 'Oost-VS') toevoegen aan het Azure Cosmos-account. Nadat u de nieuwe regio hebt toegevoegd, moet u een bijbehorende DNS-record toevoegen aan uw privé-DNS-zone of uw aangepaste DNS.

U dezelfde stappen gebruiken wanneer u een regio verwijdert. Nadat u de regio hebt verwijderd, moet u de bijbehorende DNS-record verwijderen uit uw privé-DNS-zone of uw aangepaste DNS.

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen zijn van toepassing wanneer u Privékoppeling gebruikt met een Azure Cosmos-account:

* Ondersteuning voor Private Link voor Azure Cosmos-accounts en virtuele netwerken is alleen beschikbaar in specifieke regio's. Zie de sectie [Beschikbare regio's](../private-link/private-link-overview.md#availability) van het artikel Privékoppeling voor een lijst met ondersteunde regio's. 

  > [!NOTE]
  > Als u een privéeindpunt wilt maken, moet u ervoor zorgen dat zowel het virtuele netwerk als het Azure Cosmos-account zich in ondersteunde regio's bevinden.

* Wanneer u Private Link met een Azure Cosmos-account gebruikt met behulp van een verbinding in de directe modus, u alleen het TCP-protocol gebruiken. Het HTTP-protocol wordt nog niet ondersteund.

* Ondersteuning voor privéeindpunten is momenteel algemeen alleen beschikbaar in ondersteunde regio's voor de gateway-verbindingsmodus. Voor de directe modus is het beschikbaar als voorbeeldfunctie.

* Wanneer u de API van Azure Cosmos DB voor MongoDB-accounts gebruikt, wordt een privéeindpunt alleen ondersteund voor accounts op serverversie 3.6 (dat wil zeggen accounts met het eindpunt in de indeling). `*.mongo.cosmos.azure.com` Private Link wordt niet ondersteund voor accounts op serverversie 3.2 (dat `*.documents.azure.com`wil zeggen accounts met het eindpunt in de indeling). Als u Privékoppeling wilt gebruiken, moet u oude accounts migreren naar de nieuwe versie.

* Wanneer u de API van Azure Cosmos DB voor MongoDB-accounts met Private Link gebruikt, u geen hulpprogramma's zoals Robo 3T, Studio 3T en Mongoose gebruiken. Het eindpunt kan alleen ondersteuning voor `appName=<account name>` Privékoppelingen hebben als de parameter is opgegeven. Een voorbeeld is `replicaSet=globaldb&appName=mydbaccountname`. Omdat deze hulpprogramma's de naam van de app niet doorgeven in de verbindingstekenreeks aan de service, u Geen Privékoppeling gebruiken. Maar u nog steeds toegang tot deze accounts met behulp van SDK drivers met de 3.6 versie.

* U een virtueel netwerk niet verplaatsen of verwijderen als het Privékoppeling bevat.

* U een Azure Cosmos-account niet verwijderen als het is gekoppeld aan een privéeindpunt.

* U niet falen over een Azure Cosmos-account naar een regio die niet is toegewezen aan alle privéeindpunten die aan het account zijn gekoppeld.

* Een netwerkbeheerder moet ten minste de machtiging "*/PrivateEndpointConnectionsApproval" krijgen op het Azure Cosmos-accountbereik om automatisch goedgekeurde privéeindpunten te maken.

### <a name="limitations-to-private-dns-zone-integration"></a>Beperkingen voor integratie van privé-DNS-zones

DNS-records in de privé-DNS-zone worden niet automatisch verwijderd wanneer u een privéeindpunt verwijdert of een regio verwijdert uit het Azure Cosmos-account. U moet de DNS-records handmatig verwijderen voordat:

* Het toevoegen van een nieuw privéeindpunt dat is gekoppeld aan deze privé-DNS-zone.
* Een nieuw gebied toevoegen aan een databaseaccount met privéeindpunten die zijn gekoppeld aan deze privé-DNS-zone.

Als u de DNS-records niet opschonen, kunnen er onverwachte problemen met het gegevensvlak optreden. Deze problemen omvatten gegevensuitval naar regio's die zijn toegevoegd na het verwijderen van privéeindpunten of het verwijderen van regio's.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over azure cosmos DB-beveiligingsfuncties:

* Zie [Firewall-ondersteuning](firewall-support.md)voor het configureren van een firewall voor Azure Cosmos DB.

* Zie Toegang configureren [vanaf virtuele netwerken](how-to-configure-vnet-service-endpoint.md)voor meer informatie over het configureren van een eindpunt voor een virtuele netwerkservice voor uw Azure Cosmos-account.

* Zie de [Azure Private Link-documentatie](../private-link/private-link-overview.md) voor meer informatie over Private Link.
