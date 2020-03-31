---
title: DNS-service azure service fabric
description: Gebruik de dns-service van Service Fabric voor het ontdekken van microservices vanuit het cluster.
ms.topic: conceptual
ms.date: 7/20/2018
ms.openlocfilehash: 317aa81238ec7a0dc24b69b1d00568901b9bc34f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458028"
---
# <a name="dns-service-in-azure-service-fabric"></a>DNS Service in Azure Service Fabric
De DNS-service is een optionele systeemservice die u in uw cluster inschakelen om andere services te ontdekken met behulp van het DNS-protocol. 

Veel services, met name containerservices, zijn adresseerbaar via een reeds bestaande URL. In staat zijn om deze services op te lossen met behulp van het standaard DNS-protocol, in plaats van het Service Fabric Naming Service-protocol, is wenselijk. Met de DNS-service u DNS-namen toewijzen aan een servicenaam en dus eindpuntIP-adressen oplossen. Dergelijke functionaliteit behoudt de draagbaarheid van gecontaineriseerde services op verschillende platforms en kan "lift and shift"-scenario's eenvoudiger maken, dooru bestaande service-URL's te laten gebruiken in plaats van code te moeten herschrijven om gebruik te maken van de naamgevingsservice. 

De DNS-service brengt DNS-namen toe aan servicenamen, die op hun beurt worden opgelost door de naamgevingsservice om het serviceeindpunt terug te sturen. De DNS-naam voor de service wordt verstrekt op het moment van maken. In het volgende diagram ziet u hoe de DNS-service werkt voor stateless services.

![serviceeindpunten](./media/service-fabric-dnsservice/stateless-dns.png)

Vanaf Service Fabric versie 6.3 is het SERVICE Fabric DNS-protocol uitgebreid met een schema voor het aanpakken van partitionerende stateful services. Deze extensies maken het mogelijk om specifieke IP-adressen van partities op te lossen met behulp van een combinatie van een stateful service DNS-naam en de partitienaam. Alle drie de scheidingsschema's worden ondersteund:

- Benoemde partitionering
- Gescheiden partitionering
- Singleton partitionering

In het volgende diagram ziet u hoe de DNS-service werkt voor partitievolle services.

![stateful serviceeindpunten](./media/service-fabric-dnsservice/stateful-dns.png)

Dynamische poorten worden niet ondersteund door de DNS-service. Als u services wilt oplossen die op dynamische poorten worden weergegeven, gebruikt u de [omgekeerde proxyservice](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>De DNS-service inschakelen
> [!NOTE]
> DNS-service voor Service Fabric-services wordt nog niet ondersteund op Linux.

Wanneer u een cluster maakt met de portal, is de DNS-service standaard ingeschakeld in het selectievakje **DNS-service opnemen** in het menu **Clusterconfiguratie:**

![DNS-service inschakelen via de portal](./media/service-fabric-dnsservice/enable-dns-service.png)

Als u de portal niet gebruikt om uw cluster te maken of als u een bestaand cluster bijwerkt, moet u de DNS-service in een sjabloon inschakelen:

- Als u een nieuw cluster wilt implementeren, u de [voorbeeldsjablonen](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) gebruiken of uw eigen resourcemanagersjabloon maken. 
- Als u een bestaand cluster wilt bijwerken, u naar de resourcegroep van het cluster op de portal navigeren en op **Automatiseringsscript** klikken om te werken met een sjabloon die de huidige status van het cluster en andere bronnen in de groep weergeeft. Zie [De sjabloon exporteren uit de resourcegroep](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template)voor meer informatie.

Nadat u een sjabloon hebt, u de DNS-service inschakelen met de volgende stappen:

1. Controleer of `apiversion` de `2017-07-01-preview` bron is `Microsoft.ServiceFabric/clusters` ingesteld op of hoger voor de resource en, zo niet, deze bij te werken zoals in het volgende voorbeeld wordt weergegeven:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Schakel nu de DNS-service op een van de volgende manieren in:

   - Als u de DNS-service met standaardinstellingen wilt inschakelen, voegt u deze toe aan de `addonFeatures` sectie in de `properties` sectie zoals in het volgende voorbeeld wordt weergegeven:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Als u de service wilt inschakelen `DnsService` met andere `fabricSettings` dan `properties` standaardinstellingen, voegt u een sectie toe aan de sectie in de sectie. In dit geval hoeft u de DnsService `addonFeatures`niet toe te voegen aan . Zie [DNS-serviceinstellingen](./service-fabric-cluster-fabric-settings.md#dnsservice)voor meer informatie over de eigenschappen die voor de DNS-service kunnen worden ingesteld.

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
3. Zodra u de clustersjabloon hebt bijgewerkt met uw wijzigingen, past u deze toe en laat u de upgrade voltooien. Wanneer de upgrade is voltooid, wordt de DNS-systeemservice in uw cluster uitgevoerd. De servicenaam `fabric:/System/DnsService`is, en u deze vinden onder de sectie **Systeemservice** in Service Fabric explorer. 

> [!NOTE]
> Wanneer u DNS upgradet van uitgeschakeld naar ingeschakeld, geeft Service Fabric Explorer mogelijk geen nieuwe status weer. Als u het probleem wilt oplossen, start u de knooppunten opnieuw door het upgradebeleid in uw Azure Resource Manager-sjabloon te wijzigen. Zie de [sjabloonverwijzing voor servicestructuur](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications) voor meer informatie.

> [!NOTE]
> Als u DNS-service inschakelt bij het ontwikkelen op een lokale machine, worden sommige DNS-instellingen overschreven. Als u problemen ondervindt bij het maken van verbinding met internet, controleert u uw DNS-instellingen.

## <a name="setting-the-dns-name-for-your-service"></a>De DNS-naam voor uw service instellen
U een DNS-naam voor uw services declaratief instellen voor standaardservices in het bestand ApplicationManifest.xml of via PowerShell-opdrachten.

De DNS-naam voor uw service is oplosbaar in het hele cluster, dus het is belangrijk om de uniciteit van de DNS-naam in het cluster te garanderen. 

Het wordt ten zeerste aanbevolen dat `<ServiceDnsName>.<AppInstanceName>`u een naamgevingsschema van ; bijvoorbeeld . `service1.application1` Als een toepassing wordt geïmplementeerd met Docker compose, worden services automatisch DNS-namen toegewezen met behulp van dit naamgevingsschema.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>De DNS-naam instellen voor een standaardservice in applicationmanifest.xml
Open uw project in Visual Studio of uw favoriete editor en open het bestand ApplicationManifest.xml. Ga naar de sectie standaardservices en `ServiceDnsName` voeg voor elke service het kenmerk toe. In het volgende voorbeeld ziet u hoe u de DNS-naam van de service instelt op`service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Zodra de toepassing is geïmplementeerd, wordt in de servicefabricverkenner de DNS-naam voor dit exemplaar weergegeven, zoals in de volgende afbeelding wordt weergegeven: 

![serviceeindpunten](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

In het volgende voorbeeld wordt de DNS-naam voor een stateful service ingesteld op `statefulsvc.app`. De service maakt gebruik van een benoemd partitieschema. Merk op dat de partitienamen kleine letters zijn. Dit is een vereiste voor partities die worden gericht in DNS-query's; Zie [DNS-query's maken op een stateful servicepartitie](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition)voor meer informatie.

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>De DNS-naam voor een service instellen met Powershell
U de DNS-naam voor een `New-ServiceFabricService` service instellen wanneer u deze maakt met de opdracht Powershell. In het volgende voorbeeld wordt een nieuwe statusloze service gemaakt met de DNS-naam`service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Preview] DNS-query's maken op een stateful servicepartitie
De DNS-service Service Fabric-service begint met Service Fabric-versie 6.3 en ondersteunt query's voor servicepartities.

Voor partities die worden gebruikt in DNS-query's, gelden de volgende naamgevingsbeperkingen:

   - Partitienamen moeten DNS-compatibel zijn.
   - Namen van meerdere etiketten (die punt, '.', in de naam bevatten) mogen niet worden gebruikt.
   - Partitienamen moeten lager zijn.

DNS-query's die een partitie targeten, worden als volgt opgemaakt:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Waar:

- *First-Label-Of-Partitioned-Service-DNSName* is het eerste deel van uw service DNS-naam.
- *PartitionPrefix* is een waarde die kan worden ingesteld in de sectie DnsService van het clustermanifest of via de resourcemanagersjabloon van het cluster. De standaardwaarde is "--". Zie [DNS-serviceinstellingen](./service-fabric-cluster-fabric-settings.md#dnsservice)voor meer informatie.
- *Doel-Partitie-Naam* is de naam van de partitie. 
- *PartitionSuffix* is een waarde die kan worden ingesteld in de sectie DnsService van het clustermanifest of via de resourcemanagersjabloon van het cluster. De standaardwaarde is een lege tekenreeks. Zie [DNS-serviceinstellingen](./service-fabric-cluster-fabric-settings.md#dnsservice)voor meer informatie.
- *Resterende-Partitioned-Service-DNSName* is het resterende deel van uw service DNS-naam.

In de volgende voorbeelden worden DNS-query's weergegeven voor partitieservices die worden uitgevoerd op een cluster met standaardinstellingen voor `PartitionPrefix` en: `PartitionSuffix` 

- Als u partitie "0" van `backendrangedschemesvc.application` een service met DNS-naam `backendrangedschemesvc-0.application`wilt oplossen die een bereikhebbende scheidingsschema gebruikt, gebruikt u .
- Als u de partitie "eerste" `backendnamedschemesvc.application` van een service met DNS-naam wilt oplossen die een benoemd partitieschema gebruikt, gebruikt u `backendnamedschemesvc-first.application`.

De DNS-service retourneert het IP-adres van de primaire replica van de partitie. Als er geen partitie is opgegeven, retourneert de service het IP-adres van de primaire replica van een willekeurig geselecteerde partitie.

## <a name="using-dns-in-your-services"></a>DNS gebruiken in uw services
Als u meer dan één service implementeert, u de eindpunten van andere services vinden waarmee u communiceren met behulp van een DNS-naam. De DNS-service werkt voor stateless services en, in Service Fabric-versie 6.3 en hoger, voor stateful services. Voor stateful services die worden uitgevoerd op versies van Service Fabric vóór 6.3, u de ingebouwde [reverse proxy-service](./service-fabric-reverseproxy.md) gebruiken voor http-oproepen om een bepaalde servicepartitie aan te roepen. 

Dynamische poorten worden niet ondersteund door de DNS-service. U de omgekeerde proxyservice gebruiken om services op te lossen die dynamische poorten gebruiken.

In de volgende code ziet u hoe u een stateless service aanroept via DNS. Het is gewoon een gewone http-oproep waarbij u de DNS-naam, de poort en een optioneel pad als onderdeel van de URL opgeeft.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

De volgende code toont een aanroep op een specifieke partitie van een stateful service. In dit geval bevat de DNS-naam de partitienaam (partitie1). De aanroep gaat uit van `PartitionPrefix` `PartitionSuffix`een cluster met standaardwaarden voor en .

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="known-issues"></a>Bekende problemen
* Voor Service Fabric-versies 6.3 en hoger is er een probleem met DNS-lookups voor servicenamen die een koppelteken in de DNS-naam bevatten. Voor meer informatie over dit probleem u het volgende [GitHub-probleem bijhouden.](https://github.com/Azure/service-fabric-issues/issues/1197) Een oplossing hiervoor komt in de volgende 6.3 update. 

* DNS-service voor Service Fabric-services wordt nog niet ondersteund op Linux. DNS-service wordt ondersteund voor containers op Linux. Handmatige oplossing met fabricclient/servicepartitieresolver is het beschikbare alternatief.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over servicecommunicatie binnen het cluster met [verbinding en communicatie met services](service-fabric-connect-and-communicate-with-services.md)

