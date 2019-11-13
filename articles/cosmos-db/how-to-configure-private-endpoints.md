---
title: Een persoonlijke Azure-koppeling configureren voor een Azure Cosmos-account
description: Meer informatie over het instellen van een persoonlijke Azure-koppeling voor toegang tot een Azure Cosmos-account met behulp van een privé-IP-adres in een virtueel netwerk.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 826fe1195a142bd0826d6311eab5eb208bbc7e35
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007425"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Een persoonlijke Azure-koppeling configureren voor een Azure Cosmos-account (preview-versie)

Met behulp van een persoonlijke Azure-koppeling kunt u verbinding maken met een Azure Cosmos-account via een persoonlijk eind punt. Het persoonlijke eind punt is een reeks privé-IP-adressen in een subnet binnen het virtuele netwerk. Vervolgens kunt u de toegang tot een Azure Cosmos-account beperken via privé-IP-adressen. Wanneer een persoonlijke koppeling wordt gecombineerd met een beperkt NSG-beleid, vermindert dit het risico van gegevens exfiltration. Zie het artikel over een [persoonlijke Azure-koppeling](../private-link/private-link-overview.md) voor meer informatie over privé-eind punten.

Met persoonlijke koppeling kunnen gebruikers toegang krijgen tot een Azure Cosmos-account vanuit het virtuele netwerk of via een peered virtueel netwerk. Resources die zijn toegewezen aan een privé koppeling, zijn ook on-premises toegankelijk via privé-peering via VPN of Azure ExpressRoute. 

U kunt verbinding maken met een Azure Cosmos-account dat is geconfigureerd met een persoonlijke koppeling met behulp van de automatische of hand matige goedkeurings methode. Zie de sectie [goedkeurings werk stroom](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) van de documentatie van de persoonlijke koppeling voor meer informatie. 

In dit artikel worden de stappen beschreven voor het maken van een persoonlijk eind punt. Hierbij wordt ervan uitgegaan dat u de automatische goedkeurings methode gebruikt.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Een persoonlijk eind punt maken met behulp van de Azure Portal

Gebruik de volgende stappen om een persoonlijk eind punt te maken voor een bestaand Azure Cosmos-account met behulp van de Azure Portal:

1. Kies in het deel venster **alle resources** een Azure Cosmos-account.

1. Selecteer **particuliere endpoint-verbindingen** in de lijst met instellingen en selecteer vervolgens **persoonlijk eind punt**:

   ![Selecties voor het maken van een persoonlijk eind punt in de Azure Portal](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. Voer in het deel venster **een persoonlijk eind punt maken (preview)-basis beginselen** de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Project Details** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer een resourcegroep.|
    | **Exemplaar Details** |  |
    | Naam | Voer een naam in voor uw privé-eind punt. Als deze naam wordt gebruikt, maakt u er een. |
    |Regio| Selecteer de regio waar u een persoonlijke koppeling wilt implementeren. Maak het persoonlijke eind punt op de locatie waar het virtuele netwerk zich bevindt.|
    |||
1. Selecteer **volgende: resource**.
1. Voer in **een persoonlijk eind punt maken-resource**in of Selecteer deze gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer **verbinding maken met een Azure-resource in mijn Directory**. <br/><br/> U kunt vervolgens een van uw resources kiezen om een persoonlijke koppeling in te stellen. U kunt ook verbinding maken met de resource van iemand anders door een resource-ID of alias te gebruiken die met u is gedeeld.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **micro soft. AzureCosmosDB/databaseAccounts**. |
    | Resource |Selecteer uw Azure Cosmos-account. |
    |Doel-subresource |Selecteer het Azure Cosmos DB-API-type dat u wilt toewijzen. Dit wordt standaard ingesteld op slechts één keuze voor de Api's SQL, MongoDB en Cassandra. Voor de Gremlin-en Table-Api's kunt u ook **SQL** kiezen omdat deze api's compatibel zijn met de SQL-API. |
    |||

1. Selecteer **volgende: Configuratie**.
1. Voer in **een persoonlijk eind punt maken (preview)-configuratie**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**Netwerken**| |
    | Virtueel netwerk| Selecteer uw virtuele netwerk. |
    | Subnet | Selecteer uw subnet. |
    |**Integratie van Privé-DNS**||
    |Integreren met een privé-DNS-zone |Selecteer **Ja**. <br><br/> Als u privé wilt verbinden met uw persoonlijke eind punt, moet u een DNS-record hebben. We raden u aan uw persoonlijke eind punt te integreren met een privé-DNS-zone. U kunt ook uw eigen DNS-servers gebruiken of DNS-records maken met behulp van de host-bestanden op uw virtuele machines. |
    |Privé-DNS zone |Selecteer **privatelink.Documents.Azure.com**. <br><br/> De privé-DNS-zone wordt automatisch bepaald. U kunt deze niet wijzigen met behulp van de Azure Portal.|
    |||

1. Selecteer **Controleren + maken**. Op de pagina **controleren en maken** valideert Azure uw configuratie.
1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

Wanneer u een persoonlijke koppeling hebt goedgekeurd voor een Azure Cosmos-account, is de optie **alle netwerken** in het deel venster **firewall en virtuele netwerken** niet beschikbaar in de Azure Portal.

In de volgende tabel ziet u de toewijzing tussen de verschillende Azure Cosmos-account-API-typen, ondersteunde subresources en de bijbehorende namen van de persoonlijke zones. U kunt ook toegang krijgen tot de Gremlin-en Table-API-accounts via de SQL-API, zodat er twee vermeldingen voor deze Api's zijn.

|API-type voor Azure Cosmos-account  |Ondersteunde subbronnen (of groeps-Id's) |Naam van particuliere zone  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tabel    |    Tabel     |   privatelink.table.cosmos.azure.com    |
|Tabel     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>De privé-IP-adressen ophalen

Nadat het persoonlijke eind punt is ingericht, kunt u een query uitvoeren op de IP-adressen. De IP-adressen van de Azure Portal weer geven:

1. Selecteer **Alle resources**.
1. Zoek naar het persoonlijke eind punt dat u eerder hebt gemaakt. In dit geval is het **cdbPrivateEndpoint3**.
1. Selecteer het tabblad **overzicht** om de DNS-instellingen en IP-adressen te bekijken.

![Privé-IP-adressen in de Azure Portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Er zijn meerdere IP-adressen gemaakt per particulier eind punt:

* Een voor het algemene eind punt (Region-neutraal) van het Azure Cosmos-account
* Eén voor elke regio waar het Azure Cosmos-account wordt geïmplementeerd

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Een persoonlijk eind punt maken met behulp van Azure PowerShell

Voer het volgende Power shell-script uit om een persoonlijk eind punt met de naam ' MyPrivateEndpoint ' te maken voor een bestaand Azure Cosmos-account. Vervang de variabele waarden door de gegevens voor uw omgeving.

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

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Het persoonlijke eind punt integreren met een privé-DNS-zone

Nadat u het persoonlijke eind punt hebt gemaakt, kunt u het integreren met een privé-DNS-zone met behulp van het volgende Power shell-script:

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

Nadat het persoonlijke eind punt is ingericht, kunt u een query uitvoeren op de IP-adressen en de FQDN-toewijzing met behulp van het volgende Power shell-script:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Een persoonlijk eind punt maken met behulp van een resource manager-sjabloon

U kunt een persoonlijke koppeling instellen door een persoonlijk eind punt te maken in een subnet van een virtueel netwerk. U kunt dit doen met behulp van een Azure Resource Manager sjabloon. 

Gebruik de volgende code om een resource manager-sjabloon met de naam ' PrivateEndpoint_template. json ' te maken. Met deze sjabloon maakt u een persoonlijk eind punt voor een bestaand Azure Cosmos SQL API-account in een bestaand virtueel netwerk.

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

### <a name="define-the-parameters-file-for-the-template"></a>Definieer het parameter bestand voor de sjabloon

Maak een parameter bestand voor de sjabloon en geef het de naam PrivateEndpoint_parameters. json. Voeg de volgende code toe aan het parameter bestand:

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

### <a name="deploy-the-template-by-using-a-powershell-script"></a>De sjabloon implementeren met behulp van een Power shell-script

Maak een Power shell-script met de volgende code. Voordat u het script uitvoert, moet u de abonnements-ID, de naam van de resource groep en andere variabelen waarden vervangen door de gegevens voor uw omgeving.

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

In het Power shell-script mag de variabele `GroupId` slechts één waarde bevatten. Deze waarde is het API-type van het account. Toegestane waarden zijn: `Sql`, `MongoDB`, `Cassandra`, `Gremlin`en `Table`. Sommige Azure Cosmos-account typen zijn toegankelijk via meerdere Api's. Bijvoorbeeld:

* U kunt toegang krijgen tot een Gremlin-API-account vanuit zowel de Gremlin-als SQL-API-accounts.
* Een Table-API account kan worden geopend vanuit zowel de tabel-als de SQL-API-accounts.

Voor deze accounts moet u één persoonlijk eind punt maken voor elk API-type. Het bijbehorende API-type is opgegeven in de matrix `GroupId`.

Nadat de sjabloon is geïmplementeerd, kunt u een uitvoer zien die vergelijkbaar is met wat er in de volgende afbeelding wordt weer gegeven. De `provisioningState` waarde wordt `Succeeded` als de persoonlijke eind punten correct zijn ingesteld.

![Implementatie-uitvoer voor de Resource Manager-sjabloon](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Nadat de sjabloon is geïmplementeerd, worden de privé-IP-adressen in het subnet gereserveerd. De firewall regel van het Azure Cosmos-account is geconfigureerd om alleen verbindingen van het privé-eind punt te accepteren.

## <a name="configure-custom-dns"></a>Aangepaste DNS configureren

U moet een privé-DNS-zone gebruiken in het subnet waar u het persoonlijke eind punt hebt gemaakt. Configureer de eind punten zodat elk privé-IP-adres wordt toegewezen aan een DNS-vermelding. (Zie de eigenschap `fqdns` in het antwoord dat eerder is weer gegeven.)

Wanneer u het persoonlijke eind punt maakt, kunt u het integreren met een privé-DNS-zone in Azure. Als u in plaats daarvan een aangepaste DNS-zone wilt gebruiken, moet u deze configureren voor het toevoegen van DNS-records voor alle privé-IP-adressen die zijn gereserveerd voor het persoonlijke eind punt.

## <a name="private-link-combined-with-firewall-rules"></a>Privé koppeling gecombineerd met firewall regels

De volgende situaties en resultaten zijn mogelijk wanneer u een persoonlijke koppeling gebruikt in combi natie met firewall regels:

* Als u geen firewall regels configureert, kan standaard al het verkeer toegang krijgen tot een Azure Cosmos-account.

* Als u openbaar verkeer of een service-eind punt configureert en u persoonlijke eind punten maakt, worden verschillende soorten binnenkomend verkeer geautoriseerd door het bijbehorende type firewall regel.

* Als u geen openbaar verkeer of service-eind punt configureert en u persoonlijke eind punten maakt, is het Azure Cosmos-account alleen toegankelijk via de persoonlijke eind punten. Als u geen openbaar verkeer of een service-eind punt configureert, wordt het account geopend voor het hele netwerk nadat alle goedgekeurde privé-eind punten zijn afgewezen of verwijderd.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Een persoonlijk eind punt bijwerken wanneer u een regio toevoegt of verwijdert

Als u regio's wilt toevoegen aan of verwijderen uit een Azure Cosmos-account, moet u DNS-vermeldingen voor dat account toevoegen of verwijderen. Werk deze wijzigingen dienovereenkomstig bij in het persoonlijke eind punt met behulp van de volgende stappen:

1. Wanneer de beheerder van de Azure Cosmos DB regio's toevoegt of verwijdert, ontvangt de netwerk beheerder een melding over de wijzigingen die in behandeling zijn. Voor het persoonlijke eind punt dat is toegewezen aan een Azure Cosmos-account, wordt de waarde van de eigenschap `ActionsRequired` gewijzigd van `None` in `Recreate`. Vervolgens werkt de netwerk beheerder het persoonlijke eind punt bij door een PUT-aanvraag uit te geven met dezelfde resource manager-nettolading die is gebruikt om deze te maken.

1. Nadat het persoonlijke eind punt is bijgewerkt, kunt u de privé-DNS-zone van het subnet bijwerken om de toegevoegde of verwijderde DNS-vermeldingen en de bijbehorende privé-IP-adressen weer te geven.

Stel bijvoorbeeld dat u een Azure Cosmos-account in drie regio's implementeert: "VS-West," "centraal VS" en "Europa-west". Wanneer u een persoonlijk eind punt voor uw account maakt, worden vier privé Ip's gereserveerd in het subnet. Er is één IP-adres voor elk van de drie regio's en er is één IP-adres voor het neutraal-eind punt globaal/regio.

U kunt later een nieuwe regio (bijvoorbeeld ' vs Oost ') toevoegen aan het Azure Cosmos-account. De nieuwe regio is standaard niet toegankelijk vanuit het bestaande persoonlijke eind punt. De beheerder van het Azure Cosmos-account moet de verbinding met het persoonlijke eind punt vernieuwen voordat deze vanuit de nieuwe regio wordt geopend. 

Wanneer u de ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` opdracht uitvoert, bevat de uitvoer van de opdracht de para meter `actionsRequired`. Deze para meter is ingesteld op `Recreate`. Deze waarde geeft aan dat het persoonlijke eind punt moet worden vernieuwd. Vervolgens voert de beheerder van het Azure Cosmos-account de `Set-AzPrivateEndpoint` opdracht uit om het vernieuwen van het persoonlijke eind punt te activeren.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Er wordt automatisch een nieuw privé-IP-adres gereserveerd in het subnet onder dit persoonlijke eind punt. De waarde voor `actionsRequired` wordt `None`. Als u geen persoonlijke bevat zone-integratie hebt (met andere woorden, als u een aangepaste privé-DNS-zone gebruikt), moet u uw persoonlijke DNS-zone configureren om een nieuwe DNS-record toe te voegen voor het privé-IP-adres dat overeenkomt met de nieuwe regio.

U kunt dezelfde stappen gebruiken wanneer u een regio verwijdert. Het privé-IP-adres van de verwijderde regio wordt automatisch vrijgemaakt en de `actionsRequired` markering wordt `None`. Als u geen zone-integratie met een particuliere bevat hebt, moet u uw privé-DNS-zone configureren om de DNS-record voor de verwijderde regio te verwijderen.

DNS-records in de privé-DNS-zone worden niet automatisch verwijderd wanneer een persoonlijk eind punt wordt verwijderd of een regio uit het Azure Cosmos-account wordt verwijderd. U moet de DNS-records hand matig verwijderen.

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen zijn van toepassing wanneer u een privé-verbinding met een Azure Cosmos-account gebruikt:

* Ondersteuning voor persoonlijke koppelingen voor Azure Cosmos-accounts en virtuele netwerken is alleen beschikbaar in bepaalde regio's. Zie de sectie [beschik bare regio's](../private-link/private-link-overview.md#availability) van het artikel over een persoonlijke koppeling voor een lijst met ondersteunde regio's. 

  > [!NOTE]
  > Als u een persoonlijk eind punt wilt maken, moet u ervoor zorgen dat zowel het virtuele netwerk als het Azure Cosmos-account in ondersteunde regio's worden ondersteund.

* Wanneer u een persoonlijke koppeling met een Azure Cosmos-account gebruikt via een directe modus verbinding, kunt u alleen het TCP-protocol gebruiken. Het HTTP-protocol wordt nog niet ondersteund.

* Wanneer u de API van Azure Cosmos DB gebruikt voor MongoDB-accounts, wordt een persoonlijk eind punt alleen ondersteund voor accounts op Server versie 3,6 (dat wil zeggen, accounts die gebruikmaken van het eind punt in de notatie `*.mongo.cosmos.azure.com`). Privé koppeling wordt niet ondersteund voor accounts op Server versie 3,2 (dat wil zeggen, accounts die gebruikmaken van het eind punt in de indeling `*.documents.azure.com`). Als u een persoonlijke koppeling wilt gebruiken, moet u oude accounts migreren naar de nieuwe versie.

* Wanneer u de API van het Azure Cosmos DB gebruikt voor MongoDB-accounts die een persoonlijke koppeling hebben, kunt u geen gebruik maken van hulpprogram ma's zoals Robo 3T gebruiken, Studio 3T gebruiken en Mongoose. Het eind punt kan alleen een persoonlijke koppeling ondersteunen als de para meter `appName=<account name>` is opgegeven. Een voorbeeld is `replicaSet=globaldb&appName=mydbaccountname`. Omdat deze hulpprogram ma's de naam van de app niet door geven in de connection string aan de service, kunt u geen persoonlijke koppeling gebruiken. Maar u hebt nog steeds toegang tot deze accounts met behulp van SDK-Stuur Programma's met de versie 3,6.

* U kunt een virtueel netwerk niet verplaatsen of verwijderen als het een persoonlijke koppeling bevat.

* U kunt een Azure Cosmos-account niet verwijderen als het is gekoppeld aan een persoonlijk eind punt.

* U kunt geen failover uitvoeren voor een Azure Cosmos-account naar een regio die niet is toegewezen aan alle persoonlijke eind punten die aan het account zijn gekoppeld.

* Aan een netwerk beheerder moet ten minste de machtiging ' */PrivateEndpointConnectionsApproval ' zijn toegewezen in het bereik van de Azure Cosmos-account om automatisch goedgekeurde persoonlijke eind punten te maken.

### <a name="limitations-to-private-dns-zone-integration"></a>Beperkingen voor de integratie van particuliere DNS-zones

DNS-records in de privé-DNS-zone worden niet automatisch verwijderd wanneer u een persoonlijk eind punt verwijdert of een regio verwijdert uit het Azure Cosmos-account. U moet de DNS-records hand matig verwijderen voordat:

* Het toevoegen van een nieuw persoonlijk eind punt dat is gekoppeld aan deze privé-DNS-zone.
* Het toevoegen van een nieuwe regio aan een database account met persoonlijke eind punten die zijn gekoppeld aan deze privé-DNS-zone.

Als u de DNS-records niet opschoont, kunnen er onverwachte problemen met het gegevens vlak optreden. Deze problemen bevatten gegevens onderbrekingen voor regio's die zijn toegevoegd na het verwijderen van het privé-eind punt of het verwijderen van de regio.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure Cosmos DB beveiligings functies:

* Zie [firewall ondersteuning](firewall-support.md)als u een firewall wilt configureren voor Azure Cosmos db.

* Zie [toegang vanaf virtuele netwerken configureren](how-to-configure-vnet-service-endpoint.md)voor meer informatie over het configureren van een service-eind punt voor een virtueel netwerk voor uw Azure Cosmos-account.

* Zie de documentatie van [Azure private link](../private-link/private-link-overview.md) voor meer informatie over persoonlijke koppelingen.
