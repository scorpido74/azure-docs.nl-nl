---
title: Veelgestelde vragen voor Azure Service Fabric Mesh
description: Meer informatie over veelgestelde vragen en antwoorden voor Azure Service Fabric Mesh.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 2a5c2ea63d162eb6fb78ab702e0519f8ac25dcc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252495"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Veelgestelde Service Fabric Mesh-vragen

Azure Service Fabric Mesh is een volledig beheerde service waarmee ontwikkelaars microservices-toepassingen kunnen implementeren zonder virtuele machines, opslag of netwerken hoeven te beheren. Dit artikel heeft antwoorden op veelgestelde vragen.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Hoe rapporteer ik een probleem of stel ik een vraag?

Stel vragen, krijg antwoorden van Microsoft-technici en rapporteer problemen in de [GitHub-repo voor service-fabric-mesh-preview.](https://aka.ms/sfmeshissues)

## <a name="quota-and-cost"></a>Quota en kosten

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Wat zijn de kosten van deelname aan de preview?

Er zijn momenteel geen kosten verbonden aan het implementeren van toepassingen of containers in de mesh-preview. Let op updates in mei voor de inschakeling voor facturering. We raden u echter aan de resources die u implementeert te verwijderen en ze niet te laten draaien, tenzij u ze actief test.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Is er een quotumlimiet van het aantal kernen en RAM?

Ja. De quota voor elk abonnement zijn:

- Aantal aanvragen: 5
- Kernen per toepassing: 12
- Totaal RAM per toepassing: 48 GB
- Netwerk- en ingress-eindpunten: 5
- Azure-volumes die u koppelen: 10
- Aantal servicereplica's: 3
- De grootste container die u implementeren is beperkt tot 4 cores en 16GB RAM.
- U gedeeltelijke kernen toewijzen aan uw containers in stappen van 0,5 cores, tot maximaal 6 cores.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Hoe lang kan ik mijn toepassing laten implementeren?

We hebben momenteel de levensduur van een aanvraag beperkt tot twee dagen. Dit is om het gebruik van de gratis kernen toegewezen aan de preview te maximaliseren. Als gevolg hiervan mag u een bepaalde implementatie slechts 48 uur continu uitvoeren, waarna deze wordt afgesloten.

Als u dit ziet gebeuren, u valideren dat `az mesh app show` het systeem het systeem afsluit door de opdracht uit te voeren in de Azure CLI. Controleren of het retourneert`"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Bijvoorbeeld: 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Als u de brongroep `az group delete <nameOfResourceGroup>` wilt verwijderen, gebruikt u de opdracht.

## <a name="deployments"></a>Implementaties

### <a name="what-container-images-are-supported"></a>Welke containerafbeeldingen worden ondersteund?

Als u een Windows Fall Creators Update -machine (versie 1709) ontwikkelt, u alleen afbeeldingen van Windows-versie 1709-docker gebruiken.

Als u een Windows 10 April 2018-update (versie 1803) ontwikkelt, u windows-versie 1709 of Windows-versie 1803-dockerafbeeldingen gebruiken.

De volgende container-BE-afbeeldingen kunnen worden gebruikt om services te implementeren:
- Windows - windowsservercore en nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Geen bekende beperkingen

> [!NOTE]
> Visual Studio-tooling voor Mesh biedt nog geen ondersteuning voor implementatie in Windows Server 2019- en 1809-containers.

### <a name="what-types-of-applications-can-i-deploy"></a>Welke soorten toepassingen kan ik implementeren? 

U alles implementeren dat wordt uitgevoerd in containers die passen binnen de beperkingen die zijn geplaatst op een toepassingsbron (zie hierboven voor meer informatie over quota). Als we vaststellen dat u Mesh gebruikt voor het uitvoeren van illegale workloads of misbruik maken van het systeem (d.w.z. mining), behouden we ons het recht voor om uw implementaties te beëindigen en uw abonnement te blokkeren vanaf het uitvoeren van de service. Neem contact met ons op als u vragen heeft over het uitvoeren van een specifieke werklast. 

## <a name="developer-experience-issues"></a>Problemen met de ervaring van ontwikkelaars

### <a name="dns-resolution-from-a-container-doesnt-work"></a>DNS-resolutie van een container werkt niet

Uitgaande DNS-query's van een container naar de DNS-service Service Fabric kunnen onder bepaalde omstandigheden mislukken. Dit wordt onderzocht. Om te beperken:

- Gebruik Windows Fall Creators-update (versie 1709) of hoger als afbeelding van uw basiscontainer.
- Als de servicenaam alleen niet werkt, probeert u de volledig gekwalificeerde naam: ServiceName.ApplicationName.
- Voeg in het Docker-bestand `EXPOSE <port>` voor uw service toe waar de poort is waar u uw service op blootstelt. Bijvoorbeeld:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS werkt niet hetzelfde als voor Service Fabric-ontwikkelingsclusters en in Mesh

Mogelijk moet u in uw cluster voor lokale ontwikkeling anders naar services verwijzen dan in Azure Mesh.

In uw lokale `{serviceName}.{applicationName}`ontwikkeling cluster gebruik . Gebruik in Azure Service `{servicename}`Fabric Mesh . 

Azure Mesh biedt momenteel geen ondersteuning voor DNS-resolutie voor alle toepassingen.

Zie: Windows-containers en [bekende DNS-problemen](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues) [debuggen](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) voor andere bekende DNS-problemen bij het uitvoeren van een cluster voor de ontwikkeling van servicefabric op Windows 10.

### <a name="networking"></a>Netwerken

Het ServiceFabric-netwerk NAT kan verdwijnen tijdens het gebruik van uw app op uw lokale machine. Voer het volgende uit vanaf een opdrachtprompt om te bepalen of dit is gebeurd:

`docker network ls`en let `servicefabric_nat` op of wordt vermeld.  Als dit niet het zo is, voert u de volgende opdracht uit:`docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Dit zal het probleem oplossen, zelfs als de app al lokaal en in een ongezonde staat is geïmplementeerd.

### <a name="issues-running-multiple-apps"></a>Problemen met het uitvoeren van meerdere apps

Mogelijk u cpu-beschikbaarheid en limieten tegenkomen die voor alle toepassingen worden opgelost. Om te beperken:
- Maak een cluster met vijf nodes.
- Verminder het CPU-gebruik in services in de app die wordt geïmplementeerd. Wijzig bijvoorbeeld in het bestand service.yaml `cpu: 1.0` van uw service`cpu: 0.5`

Meerdere toepassingen kunnen niet worden geïmplementeerd in een cluster met één knooppunt. Om te beperken:
- Gebruik een cluster met vijf knooppunten bij het implementeren van meerdere apps in een lokaal cluster.
- Verwijder apps die u momenteel niet test.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>VS Tooling heeft beperkte ondersteuning voor Windows-containers

De Visual Studio-tooling ondersteunt vandaag de dag alleen het implementeren van Windows Containers met een basisversie van Windows Server 1709 en 1803. 

## <a name="feature-gaps-and-other-known-issues"></a>Functiehiaten en andere bekende problemen

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Na het implementeren van mijn toepassing heeft de netwerkbron die eraan is gekoppeld geen IP-adres

Er is een bekend probleem waarbij het IP-adres niet onmiddellijk beschikbaar komt. Controleer de status van de netwerkbron in een paar minuten om het bijbehorende IP-adres te bekijken.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Mijn toepassing heeft geen toegang tot de juiste netwerk-/volumebron

Gebruik in uw toepassingsmodel de volledige resource-id voor netwerken en volumes om toegang te krijgen tot de bijbehorende bron. Hier is een voorbeeld van het voorbeeld snelstart:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Wanneer ik uitschaal, worden al mijn containers beïnvloed, inclusief het uitvoeren van

Dit is een bug en een oplossing wordt geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

Lees het [overzicht](service-fabric-mesh-overview.md)voor meer informatie over Service Fabric Mesh.
