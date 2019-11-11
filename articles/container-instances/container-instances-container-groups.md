---
title: Azure Container Instances container groepen
description: Meer informatie over het werken met groepen met meerdere containers in Azure Container Instances
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 11/01/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: a785ecbfa09c54d3affa97c220d4808f9fe8d90b
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904451"
---
# <a name="container-groups-in-azure-container-instances"></a>Container groepen in Azure Container Instances

De resource op het hoogste niveau in Azure Container Instances is de *container groep*. In dit artikel wordt beschreven wat de container groepen zijn en welke scenario's er worden ingeschakeld.

## <a name="how-a-container-group-works"></a>Hoe een container groep werkt

Een container groep is een verzameling van containers die op dezelfde hostcomputer worden gepland. De containers in een container groep delen een levens cyclus, bronnen, lokaal netwerk en opslag volumes. Het is vergelijkbaar met het concept van een *pod* in [Kubernetes][kubernetes-pod].

In het volgende diagram ziet u een voor beeld van een container groep die meerdere containers bevat:

![Diagram container groepen][container-groups-example]

Deze voorbeeld container groep:

* Is gepland op één hostcomputer.
* Aan wordt een DNS-naam label toegewezen.
* Beschrijft één openbaar IP-adres met één blootgestelde poort.
* Bestaat uit twee containers. Eén container luistert op poort 80, terwijl de andere wordt geluisterd op poort 5000.
* Bevat twee Azure-bestands shares als volume koppels en elke container koppelt een van de shares lokaal.

> [!NOTE]
> Groepen met meerdere containers ondersteunen momenteel alleen Linux-containers. Voor Windows-containers ondersteunt Azure Container Instances alleen implementatie van één exemplaar. Hoewel we aan de slag gaan met het toevoegen van alle functies aan Windows-containers, kunt u de huidige platform verschillen vinden in het [overzicht](container-instances-overview.md#linux-and-windows-containers)van de service.

## <a name="deployment"></a>Implementatie

Hier volgen twee algemene manieren om een groep met meerdere containers te implementeren: gebruik een [Resource Manager-sjabloon][resource-manager template] of een [yaml-bestand][yaml-file]. Een resource manager-sjabloon wordt aanbevolen wanneer u aanvullende Azure-service resources (bijvoorbeeld een [Azure Files share][azure-files]) moet implementeren wanneer u de container instanties implementeert. Als gevolg van de YAML-indeling, wordt een YAML-bestand aanbevolen wanneer uw implementatie alleen container instanties bevat. Zie de naslag documentatie over [Resource Manager-sjablonen](/azure/templates/microsoft.containerinstance/containergroups) of [yaml](container-instances-reference-yaml.md) voor meer informatie over eigenschappen die u kunt instellen.

Als u de configuratie van een container groep wilt behouden, kunt u de configuratie exporteren naar een YAML-bestand met behulp van de Azure CLI-opdracht [AZ container export][az-container-export]. Met exporteren kunt u de configuraties van container groepen opslaan in versie beheer voor ' configuratie als code '. Of gebruik het geëxporteerde bestand als uitgangs punt bij het ontwikkelen van een nieuwe configuratie in YAML.



## <a name="resource-allocation"></a>Resource toewijzing

Azure Container Instances wijst resources, zoals Cpu's, geheugen en optioneel [gpu's][gpus] (preview), toe aan een container groep door de [resource-aanvragen][resource-requests] van de instanties in de groep toe te voegen. Het maken van CPU-resources als voor beeld als u een container groep maakt met twee exemplaren, die elk één CPU aanvragen, wordt twee Cpu's toegewezen aan de container groep.

### <a name="resource-usage-by-instances"></a>Resource gebruik per instantie

Aan elk container exemplaar zijn de resources toegewezen die zijn opgegeven in de resource-aanvraag. Het resource gebruik door een container instantie in een groep is echter afhankelijk van de configuratie van de optionele [resource limiet][resource-limits] eigenschap.

* Als u geen resource limiet opgeeft, is het maximale resource gebruik van het exemplaar hetzelfde als de resource aanvraag.

* Als u een resource limiet opgeeft voor een exemplaar, kunt u het resource gebruik van het exemplaar aanpassen voor de werk belasting, ofwel het gebruik beperken of verhogen ten opzichte van de resource aanvraag. De maximale resource limiet die u kunt instellen is het totale aantal resources dat aan de groep is toegewezen.
    
    In een groep met twee instanties die 1 CPU aanvragen, kan een van uw containers bijvoorbeeld een werk belasting uitvoeren waarvoor meer Cpu's moeten worden uitgevoerd dan de andere.

    In dit scenario kunt u een resource limiet van 0,5 CPU instellen voor één exemplaar en een limiet van 2 Cpu's voor de tweede. Met deze configuratie wordt het resource gebruik van de eerste container beperkt tot 0,5 CPU, waardoor de tweede container Maxi maal twee Cpu's kan gebruiken, indien beschikbaar.

Zie de eigenschap [ResourceRequirements][resource-requirements] in de container groepen rest API voor meer informatie.

### <a name="minimum-and-maximum-allocation"></a>Minimale en maximale toewijzing

* Wijs **Mini maal** 1 CPU en 1 GB aan geheugen toe aan een container groep. Afzonderlijke container instanties binnen een groep kunnen worden ingericht met minder dan 1 CPU en 1 GB geheugen. 

* Voor het **maximum aantal** resources in een container groep raadpleegt u de [Beschik baarheid van resources][region-availability] voor Azure container instances in de implementatie regio.

## <a name="networking"></a>Netwerken

Container groepen delen een IP-adres en een poort naam ruimte op dat IP-adres. Om externe clients in staat te stellen een container binnen de groep te bereiken, moet u de poort op het IP-adres en uit de container zichtbaar maken. Omdat containers binnen de groep een poort naam ruimte delen, wordt poort toewijzing niet ondersteund. Containers in een groep kunnen elkaar via localhost bereiken op de poorten die ze hebben blootgesteld, zelfs als deze poorten niet extern worden weer gegeven op het IP-adres van de groep.

Implementeer eventueel container groepen in een [virtueel Azure-netwerk][virtual-network] (preview) zodat containers veilig kunnen communiceren met andere resources in het virtuele netwerk.

## <a name="storage"></a>Storage

U kunt externe volumes opgeven om te koppelen binnen een container groep. U kunt deze volumes toewijzen aan specifieke paden binnen de afzonderlijke containers in een groep.

## <a name="common-scenarios"></a>Algemene scenario's

Groepen met meerdere containers zijn handig in gevallen waarin u één functionele taak wilt verdelen in een klein aantal container installatie kopieën. Deze installatie kopieën kunnen vervolgens worden geleverd door verschillende teams en hebben afzonderlijke resource vereisten.

Het gebruik van het voor beeld kan het volgende omvatten:

* Een container met een webtoepassing en een container die de meest recente inhoud van broncode beheer ophaalt.
* Een toepassings container en een logboek registratie-container. De logboek registratie container verzamelt de logboeken en metrische gegevens die worden uitgevoerd door de hoofd toepassing en schrijft deze naar lange termijn opslag.
* Een toepassings container en een bewakings container. De controle container maakt regel matig een aanvraag voor de toepassing om te controleren of deze actief is en correct reageert, en genereert een waarschuwing als dat niet het geval is.
* Een front-end-container en een back-end-container. De front-end kan een webtoepassing zijn, waarbij de back-end een service uitvoert om gegevens op te halen. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het implementeren van een container groep met meerdere containers met een Azure Resource Manager-sjabloon:

> [!div class="nextstepaction"]
> [Een container groep implementeren][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
