---
title: Azure Service Fabric Programmatic Scaling
description: Een Azure Service Fabric-cluster in- of uitschalen, volgens aangepaste triggers
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.openlocfilehash: ffe07960c6d32bea8ec31b1fe8248b6abc2b63af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458281"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Een cluster servicestructuur programmatisch schalen 

Servicefabricclusters die in Azure worden uitgevoerd, zijn gebouwd bovenop virtuele machineschaalsets.  [Clusterschaling](./service-fabric-cluster-scale-up-down.md) beschrijft hoe Service Fabric-clusters handmatig of met regels voor automatische schaal kunnen worden geschaald. In dit artikel wordt beschreven hoe u referenties beheert en een cluster in- of uitschaalt met de vloeiend eis voor Azure compute SDK, wat een geavanceerder scenario is. Lees voor een overzicht [programmatische methoden voor het coördineren van Azure-schalingsbewerkingen.](service-fabric-cluster-scaling.md#programmatic-scaling) 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Referenties beheren
Een uitdaging van het schrijven van een service voor het verwerken van schalen is dat de service toegang moet hebben tot bronnen voor virtuele machineschaalsets zonder een interactieve login. Toegang tot het cluster Servicefabric is eenvoudig als de schaalservice de eigen Service Fabric-toepassing wijzigt, maar referenties zijn nodig om toegang te krijgen tot de schaalset. Als u zich wilt aanmelden, u een [serviceprincipal](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli) gebruiken die is gemaakt met de [Azure CLI.](https://github.com/azure/azure-cli)

Met de volgende stappen kan een serviceprincipal worden gemaakt:

1. Meld u aan bij`az login`de Azure CLI () als gebruiker met toegang tot de virtuele machineschaalset
2. De serviceprincipal maken met`az ad sp create-for-rbac`
    1. Noteer de appId (de zogenaamde 'client ID' elders), naam, wachtwoord en tenant voor later gebruik.
    2. U hebt ook uw abonnements-ID nodig, die kan worden bekeken met`az account list`

De vloeiende compute library kan zich als volgt aanmelden met deze `IAzure` referenties (houd er rekening mee dat de belangrijkste vloeiende Azure-typen zich in het [pakket Microsoft.Azure.Management.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) bevinden):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Eenmaal ingelogd, kan het aantal schaalsets `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`worden opgevraagd via .

## <a name="scaling-out"></a>Uitschalen
Met behulp van de vloeiende Azure compute SDK kunnen exemplaren worden toegevoegd aan de virtuele machineschaalset met slechts een paar oproepen -

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

Als alternatief kan de grootte van de virtuele machineschaal ook worden beheerd met PowerShell-cmdlets. [`Get-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss)kan het object met de virtuele machineschaalset ophalen. De huidige capaciteit is `.sku.capacity` beschikbaar via de eigenschap. Nadat de capaciteit is gewijzigd in de gewenste waarde, kan [`Update-AzVmss`](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) de virtuele machineschaal die is ingesteld in Azure met de opdracht worden bijgewerkt.

Net als bij het handmatig toevoegen van een knooppunt, moet het toevoegen van een schaalset-instantie alles zijn wat nodig is om een nieuw Service Fabric-knooppunt te starten, omdat de sjabloon voor de schaalset extensies bevat om automatisch nieuwe exemplaren aan het cluster Servicefabric toe te voegen. 

## <a name="scaling-in"></a>Schalen in

Inschaling is vergelijkbaar met uitschalen. De werkelijke virtuele machine schaal set veranderingen zijn vrijwel hetzelfde. Maar, zoals eerder werd besproken, Service Fabric reinigt alleen automatisch verwijderde knooppunten met een duurzaamheid van Goud of Zilver. Dus, in de Bronze-duurzaamheid schaal-in geval, is het noodzakelijk om te communiceren met de Service Fabric cluster af te sluiten van het knooppunt te worden verwijderd en vervolgens om de toestand te verwijderen.

Het voorbereiden van het knooppunt voor afsluiten omvat het vinden van het knooppunt dat moet worden verwijderd (de meest recent toegevoegde instantie voor virtuele machineschaal) en het deactiveren ervan. Exemplaren van de virtuele machineschaalworden genummerd in de volgorde waarin ze worden toegevoegd, zodat nieuwere knooppunten kunnen worden gevonden door het getalachtervoegsel in de namen van de knooppunten te vergelijken (die overeenkomen met de onderliggende namen van de virtuele machineschaalset). 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Zodra het te verwijderen knooppunt is gevonden, kan het worden `FabricClient` gedeactiveerd en `IAzure` verwijderd met dezelfde instantie en de instantie van vroeger.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Net als bij het uitschalen, kunnen PowerShell-cmdlets voor het wijzigen van de capaciteit van de virtuele machineschaal ook hier worden gebruikt als een scriptingbenadering de voorkeur verdient. Zodra de instantie van de virtuele machine is verwijderd, kan de status van het servicestructuurknooppunt worden verwijderd.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met het implementeren van uw eigen logica voor automatisch schalen, vertrouwd t.a.s.

- [Handmatig schalen of met regels voor automatische schaal](./service-fabric-cluster-scale-up-down.md)
- [Vloeiende Azure-beheerbibliotheken voor .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (handig voor interactie met de onderliggende virtuele machineschaalsets van een Service Fabric-cluster)
- [System.Fabric.FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) (handig voor interactie met een cluster van servicefabric en de knooppunten)
