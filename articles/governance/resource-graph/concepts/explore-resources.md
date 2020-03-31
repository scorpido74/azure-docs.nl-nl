---
title: Uw Azure-resources verkennen
description: Leer de querytaal Resourcegraph te gebruiken om uw bronnen te verkennen en te ontdekken hoe ze zijn verbonden.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: 0c191915b8c558d80ffef554ef758a35157e035c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76156978"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Azure-resources verkennen met Resource Graph

Azure Resource Graph biedt de mogelijkheid om uw Azure-bronnen snel en op schaal te verkennen en te ontdekken. Ontworpen voor snelle reacties, is het een geweldige manier om meer te weten te komen over uw omgeving en ook over de eigenschappen die deel uitmaken van uw Azure-bronnen.

## <a name="explore-virtual-machines"></a>Ontdek virtuele machines

Een veelgebruikte bron in Azure is een virtuele machine. Als resourcetype hebben virtuele machines veel eigenschappen die kunnen worden opgevraagd. Elke eigenschap biedt een optie voor het filteren of het vinden van precies de bron die u zoekt.

### <a name="virtual-machine-discovery"></a>Detectie van virtuele machines

Laten we beginnen met een eenvoudige query om een enkele VM uit onze omgeving te halen en te kijken naar de eigenschappen die worden geretourneerd.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> De Azure `Search-AzGraph` PowerShell-cmdlet retourneert standaard een **PSCustomObject.** Om de uitvoer er hetzelfde uit te laten `ConvertTo-Json` zien als wat wordt geretourneerd door Azure CLI, wordt de cmdlet gebruikt. De standaardwaarde voor **Diepte** is _2_. Als u deze instelt op _100,_ moeten alle geretourneerde niveaus worden geconverteerd.

De JSON-resultaten zijn gestructureerd vergelijkbaar met het volgende voorbeeld:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

De eigenschappen vertellen ons aanvullende informatie over de virtuele machine bron zelf, alles van SKU, OS, schijven, tags, en de resource groep en abonnement is het een lid van.

### <a name="virtual-machines-by-location"></a>Virtuele machines op locatie

Rekening houdend met wat we geleerd hebben over de bron van virtuele machines, laten we de **locatieeigenschap** gebruiken om alle virtuele machines per locatie te tellen. Als u de query wilt bijwerken, verwijderen we de limiet en vatten we het aantal locatiegegevens samen.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

De JSON-resultaten zijn gestructureerd vergelijkbaar met het volgende voorbeeld:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

We kunnen nu zien hoeveel virtuele machines we in elke Azure-regio hebben.

### <a name="virtual-machines-by-sku"></a>Virtuele machines van SKU

Terug te gaan naar de oorspronkelijke virtuele machine eigenschappen, laten we proberen om alle virtuele machines die een SKU grootte van **Standard_B2s**hebben te vinden. Kijkend naar de JSON terug, zien we dat het is opgeslagen in **properties.hardwareprofile.vmsize**. We werken de query bij om alle VM's te vinden die overeenkomen met deze grootte en alleen de naam van de VM en regio terug te geven.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Virtuele machines verbonden met schijven met een premium beheerde schijf

Als we de details van door premium beheerde schijven die zijn gekoppeld aan deze **Standard_B2s** virtuele machines wilden krijgen, kunnen we de query uitbreiden om ons de bron-ID van die beheerde schijven te geven.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Een andere manier om de SKU te krijgen zou zijn geweest met behulp van de **aliassen** eigenschap **Microsoft.Compute / virtualMachines / sku.name**. Zie de voorbeelden [Aliassen weergeven](../samples/starter.md#show-aliases) [en Verschillende aliaswaarden weergeven.](../samples/starter.md#distinct-alias-values)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Het resultaat is een lijst met schijf-id's.

### <a name="managed-disk-discovery"></a>Beheerde schijfdetectie

Met de eerste record van de vorige query verkennen we de eigenschappen die bestaan op de beheerde schijf die is gekoppeld aan de eerste virtuele machine. De bijgewerkte query gebruikt de schijf-id en wijzigt het type.

Voorbeeld uitvoer van de vorige query bijvoorbeeld:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Voordat u de query uitvoert, hoe wisten we dat het **type** nu **Microsoft.Compute/disks**moet zijn?
Als u de volledige id bekijkt, ziet u **/providers/Microsoft.Compute/disks/** als onderdeel van de tekenreeks. Dit tekenreeksfragment geeft u een hint naar welk type u moet zoeken. Een alternatieve methode zou zijn om de limiet te verwijderen op type en in plaats daarvan alleen zoeken op het id-veld. Omdat de ID uniek is, wordt slechts één record geretourneerd en de **eigenschap type** erop biedt dat detail.

> [!NOTE]
> Om dit voorbeeld te laten werken, moet u het id-veld vervangen door een resultaat uit uw eigen omgeving.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

De JSON-resultaten zijn gestructureerd vergelijkbaar met het volgende voorbeeld:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Virtuele machines verkennen om openbare IP-adressen te vinden

Met deze set query's worden eerst alle NIC-bronnen (network interfaces) gevonden en opgeslagen die zijn aangesloten op virtuele machines. Vervolgens gebruiken de query's de lijst met NIC's om elke IP-adresbron te vinden die een openbaar IP-adres is en deze waarden op te slaan. Ten slotte bieden de query's een lijst met de openbare IP-adressen.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

Gebruik het bestand (Azure CLI) of variable (Azure PowerShell) in de volgende query om de bijbehorende netwerkinterfacebronnen te krijgen waar een openbaar IP-adres aan de NIC is gekoppeld.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Gebruik ten slotte de lijst met openbare IP-adresbronnen die zijn opgeslagen in het bestand (Azure CLI) of variabele (Azure PowerShell) om het werkelijke openbare IP-adres van het gerelateerde object en de bijbehorende weergave te halen.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Zie de virtuele machines lijst met hun `join` [netwerkinterface en openbare IP-steekproef](../samples/advanced.md#join-vmpip) om te zien hoe u deze stappen in één query met de operator uitvoeren.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [querytaal](query-language.md).
- Zie de taal die wordt gebruikt in [Starter-query's](../samples/starter.md).
- Zie geavanceerde toepassingen in [Geavanceerde query's](../samples/advanced.md).
