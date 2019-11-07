---
title: Een persoonlijke Azure-koppeling configureren voor een Azure Cosmos-account
description: Meer informatie over het instellen van een persoonlijke Azure-koppeling voor toegang tot een Azure Cosmos-account met behulp van een privé-IP-adres in een virtueel netwerk.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 254c2645d842a6f6a2eaaeca2369b93a81e1a8cd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681683"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Een persoonlijke Azure-koppeling configureren voor een Azure Cosmos-account (preview-versie)

Met behulp van een persoonlijke Azure-koppeling kunt u verbinding maken met een Azure Cosmos-account via een persoonlijk eind punt. Het persoonlijke eind punt is een reeks privé-IP-adressen in een subnet binnen het virtuele netwerk. Met behulp van een persoonlijke koppeling kunt u de toegang tot een bepaald Azure Cosmos-account beperken via privé-IP-adressen. In combi natie met een beperkt NSG-beleid helpt een persoonlijke koppeling het risico van gegevens exfiltration te verminderen. Zie het artikel over een [persoonlijke Azure-koppeling](../private-link/private-link-overview.md) voor meer informatie over privé-eind punten.

Daarnaast kan een Azure Cosmos-account toegankelijk zijn vanuit het virtuele netwerk of een peered virtueel netwerk. Resources die zijn toegewezen aan een privé koppeling, zijn ook toegankelijk vanuit on-premises via privé-peering via VPN of ExpressRoute. 

U kunt verbinding maken met een Azure Cosmos-account dat is geconfigureerd met een persoonlijke koppeling met behulp van de goedkeurings methoden ' automatisch ' of ' hand matig '. Zie de sectie [goedkeurings werk stroom](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) van de documentatie van de persoonlijke koppeling voor meer informatie. In dit artikel worden de stappen beschreven voor het maken van een privé-koppeling, ervan uitgaande dat u automatische goedkeurings methode gebruikt.

## <a name="create-a-private-link-using-the-azure-portal"></a>Een persoonlijke koppeling maken met behulp van de Azure Portal

Gebruik de volgende stappen om een persoonlijke koppeling te maken voor een bestaand Azure Cosmos-account met behulp van Azure Portal:

1. Ga in het deel venster **alle resources** naar het Azure Cosmos DB-account dat u wilt beveiligen.

1. Selecteer **verbinding met particulier eind punt** in het menu instellingen en selecteer de optie **persoonlijk eind punt** :

   ![Een persoonlijk eind punt maken met Azure Portal](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. In het deel venster **een persoonlijk eind punt maken (preview): de basis beginselen**, de volgende gegevens invoeren of selecteren:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Project Details** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer een resourcegroep.|
    | **Exemplaar Details** |  |
    | Naam | Voer een naam in voor uw privé-eind punt. Als deze naam wordt gebruikt, maakt u er een. |
    |Regio| Selecteer de regio waar u de persoonlijke koppeling wilt implementeren. Het persoonlijke eind punt moet worden gemaakt op de locatie waar het virtuele netwerk zich bevindt.|
    |||
1. Selecteer **volgende: resource**.
1. Voer in **een persoonlijk eind punt maken-resource**in of Selecteer deze gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer verbinding maken met een Azure-resource in mijn Directory. <br/><br/> Met deze optie kunt u een van uw resources kiezen voor het instellen van een persoonlijke koppeling of verbinding maken met de resource van iemand anders met een resource-ID of alias die ze met u hebben gedeeld.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **micro soft. AzureCosmosDB/databaseAccounts**. |
    | Resource |Uw Azure Cosmos-account selecteren |
    |Doel-subresource |Selecteer het Cosmos DB-API-type dat u wilt toewijzen. Dit wordt standaard ingesteld op slechts één keuze voor de Api's SQL, MongoDB en Cassandra. Voor de Gremlin-en Table-Api's kunt u ook *SQL* kiezen omdat deze api's compatibel zijn met de SQL-API. |
    |||

1. Selecteer **volgende: Configuratie**.
1. Voer in **een persoonlijk eind punt maken (preview)-configuratie**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**Netwerken**| |
    | Virtueel netwerk| Selecteer uw virtuele netwerk. |
    | Subnet | Selecteer uw subnet. |
    |**Integratie van Privé-DNS**||
    |Integreren met een privé-DNS-zone |Selecteer **Ja**. <br><br/> Als u privé wilt verbinden met uw persoonlijke eind punt, moet u een DNS-record hebben. Het is raadzaam om uw persoonlijke eind punt te integreren met een privé-DNS-zone. U kunt ook uw eigen DNS-servers gebruiken of DNS-records maken met behulp van de host-bestanden op uw virtuele machines. |
    |Privé-DNS zone |*Privatelink.Documents.Azure.com* selecteren <br><br/> De Privé-DNS zone wordt automatisch vastgesteld en kan momenteel niet worden gewijzigd met behulp van de Azure Portal.|
    |||

1. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert.
1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**.

Wanneer u persoonlijke koppelingen hebt goedgekeurd voor een Azure Cosmos-account, wordt in de Azure Portal de optie **alle netwerken** in het deel venster **firewall en virtuele netwerken** grijs weer gegeven.

De volgende tabel toont de toewijzing tussen de verschillende Azure Cosmos-account-API-typen, ondersteunde subresources en de bijbehorende namen van de persoonlijke zones. De Gremlin-en Table-API-accounts zijn toegankelijk via SQL API en er zijn twee vermeldingen voor deze Api's:

|API-type voor Azure Cosmos-account  |Ondersteunde subbronnen (of groupIds) |Naam van particuliere zone  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tabel    |    Tabel     |   privatelink.table.cosmos.azure.com    |
|Tabel     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>De privé-IP-adressen ophalen

Nadat het persoonlijke eind punt is ingericht, kunt u een query uitvoeren op de IP-adressen. De IP-adressen van Azure Portal weer geven. Selecteer **alle resources**, zoek naar het persoonlijke eind punt dat u eerder hebt gemaakt in dit geval het ' dbPrivateEndpoint3 ' en selecteer het tabblad Overzicht voor een overzicht van de DNS-instellingen en IP-adressen:

![Privé-IP-adressen in Azure Portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Er zijn meerdere IP-adressen gemaakt per particulier eind punt:

* Een voor het algemene eind punt (Region-neutraal) van het Azure Cosmos-account.
* Eén voor elke regio waarin het Azure Cosmos-account wordt geïmplementeerd.

## <a name="create-a-private-link-using-azure-powershell"></a>Een persoonlijke koppeling maken met Azure PowerShell

Voer het volgende PowerSehll-script uit om een persoonlijke koppeling met de naam ' MyPrivateEndpoint ' te maken voor een bestaand Azure Cosmos-account. Zorg ervoor dat u de variabele waarden vervangt door de details die specifiek zijn voor uw omgeving.

```azurepowershell-interactive
Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Cosmos DB account: Sql or MongoDB or Cassandra or Gremlin or Table
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

### <a name="fetch-the-private-ip-addresses"></a>De privé-IP-adressen ophalen

Nadat het persoonlijke eind punt is ingericht, kunt u een query uitvoeren op de IP-adressen en de FQDN-toewijzingen met behulp van het volgende Power shell-script:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-link-using-a-resource-manager-template"></a>Een persoonlijke koppeling maken met een resource manager-sjabloon

U kunt een persoonlijke koppeling instellen door een persoonlijk eind punt te maken in een subnet van een virtueel netwerk. Dit wordt bereikt met behulp van een Azure Resource Manager sjabloon. Maak een resource manager-sjabloon met de naam ' PrivateEndpoint_template. json ' met de volgende code. Met deze sjabloon maakt u een persoonlijk eind punt voor een bestaand Azure Cosmos SQL API-account in een bestaand virtueel netwerk:

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

### <a name="define-the-template-parameters-file"></a>Het sjabloon parameter bestand definiëren

Maak een parameter bestand voor de sjabloon en geef het de naam ' PrivateEndpoint_parameters. json '. Voeg de volgende code toe aan het parameter bestand:

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

Maak vervolgens een Power shell-script met de volgende code. Voordat u het script uitvoert, moet u ervoor zorgen dat u de abonnements-ID, de naam van de resource groep en andere variabele waarden vervangt door de details die specifiek zijn voor uw omgeving:

```azurepowershell-interactive
### This script creates a private endpoint for an existing Cosmos DB account in an existing VNet

## Step 1: Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Cosmos DB account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

In het Power shell-script kan de variabele GroupId slechts één waarde bevatten. Dit is het API-type van het account. Toegestane waarden zijn: SQL, MongoDB, Cassandra, Gremlin en Table. Sommige Azure Cosmos-account typen zijn toegankelijk via meerdere Api's. Bijvoorbeeld:

* U kunt toegang krijgen tot een Gremlin-API-account vanuit zowel de Gremlin-als SQL-API-accounts.
* Een Table-API account kan worden geopend vanuit zowel de tabel-als de SQL-API-accounts.

Voor dergelijke accounts moet u één persoonlijk eind punt maken voor elk API-type, met het bijbehorende API-type dat is opgegeven in de array ' GroupId '.

Nadat de sjabloon is geïmplementeerd, kunt u een uitvoer zien die vergelijkbaar is met wat er in de volgende afbeelding wordt weer gegeven. De provisioningState-waarde is geslaagd als de persoonlijke eind punten correct zijn ingesteld.

![Implementatie-uitvoer van Resource Manager-sjabloon](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Nadat de sjabloon is geïmplementeerd, worden de privé-IP-adressen in het subnet gereserveerd. De firewall regel van het Azure Cosmos-account is geconfigureerd om alleen verbindingen van het privé-eind punt te accepteren.

## <a name="configure-custom-dns"></a>Aangepaste DNS configureren

Tijdens de preview-versie van persoonlijke koppelingen moet u een privé-DNS gebruiken in het subnet waar het persoonlijke eind punt is gemaakt. En configureer de eind punten zodanig dat elk particulier IP-adres wordt toegewezen aan een DNS-vermelding (Zie de eigenschap ' FQDN-namen ' in het antwoord hierboven hierboven).

Wanneer u het persoonlijke eind punt maakt, kunt u het integreren met een privé-DNS-zone in Azure. Als u uw privé-eind punt niet integreert met een particuliere DNS-zone in Azure en u in plaats daarvan een aangepaste DNS gebruikt, moet u uw DNS configureren om een nieuwe DNS-record toe te voegen voor het privé-IP-adres dat overeenkomt met de nieuwe regio.

## <a name="firewall-configuration-with-private-link"></a>Firewall configuratie met persoonlijke koppeling

Hieronder vindt u verschillende situaties en resultaten wanneer u een persoonlijke koppeling gebruikt in combi natie met firewall regels:

* Als er geen firewall regels zijn geconfigureerd, is een Azure Cosmos-account standaard toegankelijk voor al het verkeer.

* Als het open bare verkeer of service-eind punt is geconfigureerd en er persoonlijke eind punten worden gemaakt, worden verschillende soorten binnenkomend verkeer geautoriseerd door het bijbehorende type firewall regel.

* Als er geen openbaar verkeer of service-eind punt is geconfigureerd en er persoonlijke eind punten worden gemaakt, is het Azure Cosmos-account alleen toegankelijk via de persoonlijke eind punten. Als er geen openbaar verkeer of service-eind punt is geconfigureerd, nadat alle goedgekeurde privé-eind punten zijn afgewezen of verwijderd, is het account geopend voor al het netwerk.

## <a name="update-private-endpoint-when-you-add-or-remove-a-region"></a>Persoonlijk eind punt bijwerken wanneer u een regio toevoegt of verwijdert

Als u regio's wilt toevoegen aan of verwijderen uit een Azure Cosmos-account, moet u DNS-vermeldingen voor dat account toevoegen of verwijderen. Deze wijzigingen moeten dienovereenkomstig worden bijgewerkt in het persoonlijke eind punt. U moet deze wijziging op dit moment hand matig uitvoeren met de volgende stappen:

1. De Azure Cosmos DB beheerder regio's toevoegt of verwijdert. De netwerk beheerders ontvangen dan een melding over de wijzigingen die in behandeling zijn. Het persoonlijke eind punt dat is toegewezen aan een Azure Cosmos-account ziet de "ActionsRequired"-eigenschappen gewijzigd van ' geen ' in ' opnieuw maken '. Vervolgens werkt de netwerk beheerder het persoonlijke eind punt bij door een PUT-aanvraag uit te geven met dezelfde resource manager-nettolading die wordt gebruikt om deze te maken.

1. Na deze bewerking moet de privé-DNS van het subnet ook worden bijgewerkt om de toegevoegde of verwijderde DNS-vermeldingen en de bijbehorende privé-IP-adressen weer te geven.

Bijvoorbeeld, als u een Azure Cosmos-account in drie regio's implementeert: "VS-West", "centraal VS" en "Europa-west". Wanneer u een persoonlijk eind punt voor uw account maakt, worden vier privé Ip's gereserveerd in het subnet. Een voor elke regio, die het totaal van 3 en één voor het neutraal-eind punt voor Global/Region omvat.

Als u later een nieuwe regio toevoegt, bijvoorbeeld ' vs-Oost ', aan het Azure Cosmos-account. De nieuwe regio is standaard niet toegankelijk vanuit het bestaande persoonlijke eind punt. De beheerder van het Azure Cosmos-account moet de verbinding met het persoonlijke eind punt vernieuwen voordat de nieuwe regio wordt geopend. 

Wanneer u de ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` opdracht uitvoert, bevat de uitvoer van de opdracht de para meter `actionsRequired` die is ingesteld op opnieuw maken. Deze waarde geeft aan dat het persoonlijke eind punt moet worden vernieuwd. Vervolgens voert de beheerder van het Azure Cosmos-account de `Set-AzPrivateEndpoint` opdracht uit om het vernieuwen van het persoonlijke eind punt te activeren.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Er wordt automatisch een nieuw privé-IP-adres gereserveerd in het subnet onder dit persoonlijke eind punt en de waarde `actionsRequired` wordt `None`. Als u geen zone-integratie met een particuliere bevat hebt (met andere woorden, als u een aangepaste privé-DNS gebruikt), moet u uw privé-DNS configureren om een nieuwe DNS-record toe te voegen voor het privé-IP-adres dat overeenkomt met de nieuwe regio.

U kunt dezelfde stappen gebruiken wanneer u een regio verwijdert. Het privé-IP-adres van de verwijderde regio wordt automatisch vrijgemaakt en de `actionsRequired` markering wordt `None`. Als u geen zone-integratie met een particuliere bevat hebt, moet u uw particuliere DNS configureren om de DNS-record voor de verwijderde regio te verwijderen.

DNS-records in de privé-DNS-zone worden niet automatisch verwijderd wanneer een persoonlijk eind punt wordt verwijderd of een regio uit het Azure Cosmos-account wordt verwijderd. U moet de DNS-records hand matig verwijderen.

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen zijn van toepassing wanneer u de privé koppeling met een Azure Cosmos-account gebruikt:

* Wanneer u persoonlijke koppelingen met een Azure Cosmos-account gebruikt via verbinding via directe modus, kunt u alleen TCP-protocol gebruiken. Het HTTP-protocol wordt nog niet ondersteund

* Wanneer u de API van Azure Cosmos DB gebruikt voor MongoDB-accounts, wordt het persoonlijke eind punt alleen ondersteund voor accounts op Server versie 3,6 (dat wil zeggen accounts die gebruikmaken van het eind punt in de notatie `*.mongo.cosmos.azure.com`). Privé koppeling wordt niet ondersteund voor accounts op Server versie 3,2 (dat wil zeggen accounts die gebruikmaken van het eind punt in de indeling `*.documents.azure.com`). Als u een persoonlijke koppeling wilt gebruiken, moet u oude accounts migreren naar de nieuwe versie.

* Wanneer u de API van Azure Cosmos DB gebruikt voor MongoDB-accounts die een persoonlijke koppeling hebben, kunt u geen hulp middelen gebruiken zoals Robo 3T gebruiken, Studio 3T gebruiken, Mongoose etc. Het eind punt kan alleen ondersteuning bieden voor persoonlijke koppelingen als de para meter appName =<account name> is opgegeven. Bijvoorbeeld: replicaset = globaldb & appName = mydbaccountname. Omdat deze hulpprogram ma's de naam van de app in het connection string niet door geven aan de service, kunt u geen persoonlijke koppeling gebruiken. U hebt echter nog steeds toegang tot deze accounts met SDK-Stuur Programma's met 3,6-versie.

* Ondersteuning voor persoonlijke koppelingen voor Azure Cosmos-accounts en VNETs is alleen beschikbaar in bepaalde regio's. Zie de sectie [beschik bare regio's](../private-link/private-link-overview.md#availability) van het artikel over een persoonlijke koppeling voor een lijst met ondersteunde regio's. Het **VNET en het Azure Cosmos-account moeten zich in de ondersteunde regio's bezien om een persoonlijk eind punt te kunnen maken**.

* Een virtueel netwerk kan niet worden verplaatst of verwijderd als het een persoonlijke koppeling bevat.

* Een Azure Cosmos-account kan niet worden verwijderd als het is gekoppeld aan een persoonlijk eind punt.

* Er kan geen failover worden uitgevoerd voor een Azure Cosmos-account naar een regio die niet is toegewezen aan alle persoonlijke eind punten die eraan zijn gekoppeld. Zie regio's toevoegen of verwijderen in de vorige sectie voor meer informatie.

* Aan een netwerk beheerder moet ten minste de machtiging ' */PrivateEndpointConnectionsApproval ' zijn toegewezen in het bereik van de Azure Cosmos-account door een beheerder om automatisch goedgekeurde privé-eind punten te maken.

### <a name="private-dns-zone-integration-limitations"></a>Beperkingen voor de integratie van Privé-DNS zone

DNS-records in de privé-DNS-zone worden niet automatisch verwijderd wanneer een persoonlijk eind punt wordt verwijderd of een regio uit het Azure Cosmos-account wordt verwijderd. U moet de DNS-records hand matig verwijderen voordat:

* Het toevoegen van een nieuw persoonlijk eind punt dat is gekoppeld aan deze privé-DNS-zone.
* Het toevoegen van een nieuwe regio aan een database account met persoonlijke eind punten die zijn gekoppeld aan deze privé-DNS-zone.

Zonder het opschonen van de DNS-records worden onverwachte problemen met gegevens vlakken, zoals gegevens onderbreking naar regio's die zijn toegevoegd na het verwijderen van een persoonlijk eind punt of verwijderen van de regio, mogelijk

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over de andere Azure Cosmos DB beveiligings functies:

* Zie [firewall ondersteuning](firewall-support.md)als u een firewall wilt configureren voor Azure Cosmos db.

* [Het configureren van een service-eind punt voor een virtueel netwerk voor uw Azure Cosmos-account.](how-to-configure-vnet-service-endpoint.md)

* Zie de documentatie van [Azure private link](../private-link/private-link-overview.md) voor meer informatie over persoonlijke koppelingen.
