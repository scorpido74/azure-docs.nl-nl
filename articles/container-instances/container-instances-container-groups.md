---
title: Inleiding tot containergroepen
description: Meer informatie over containergroepen in Azure Container Instances, een verzameling exemplaren die een levenscyclus en bronnen delen, zoals opslag en netwerk
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 73781418321c3932bf3e0190b646dcd3bb178195
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247213"
---
# <a name="container-groups-in-azure-container-instances"></a>Containergroepen in Azure Container Instances

De bron op het hoogste niveau in Azure Container Instances is de *containergroep*. In dit artikel wordt beschreven wat containergroepen zijn en welke typen scenario's deze inschakelen.

## <a name="what-is-a-container-group"></a>Wat is een containergroep?

Een containergroep is een verzameling containers die op dezelfde hostmachine worden gepland. De containers in een containergroep delen een levenscyclus, resources, lokaal netwerk en opslagvolumes. Het is vergelijkbaar in concept met een *pod* in [Kubernetes][kubernetes-pod].

In het volgende diagram ziet u een voorbeeld van een containergroep die meerdere containers bevat:

![Diagram containergroepen][container-groups-example]

In dit voorbeeld containergroep:

* Is gepland op één hostmachine.
* Krijgt een DNS-naamlabel toegewezen.
* Hiermee wordt één openbaar IP-adres weergegeven, met één blootgestelde poort.
* Bestaat uit twee containers. De ene container luistert op poort 80, de andere luistert op poort 5000.
* Bevat twee Azure-bestandsshares als volumemounts en elke container monteert een van de shares lokaal.

> [!NOTE]
> Multicontainergroepen ondersteunen momenteel alleen Linux-containers. Voor Windows-containers ondersteunt Azure Container Instances alleen de implementatie van één containerinstantie. Terwijl we werken aan het brengen van alle functies naar Windows-containers, u actuele platformverschillen vinden in het [serviceoverzicht.](container-instances-overview.md#linux-and-windows-containers)

## <a name="deployment"></a>Implementatie

Hier volgen twee veelvoorkomende manieren om een groep met meerdere containers te implementeren: gebruik een [resourcemanagersjabloon][resource-manager template] of een [YAML-bestand][yaml-file]. Een sjabloon Resourcebeheer wordt aanbevolen wanneer u extra Azure-serviceresources (bijvoorbeeld een [Azure-bestandenaandeel)][azure-files]moet implementeren wanneer u de containerinstanties implementeert. Vanwege de beknoptere aard van de YAML-indeling wordt een YAML-bestand aanbevolen wanneer uw implementatie alleen containerexemplaren bevat. Zie de verwijzing naar de [resourcemanager-sjabloonof](/azure/templates/microsoft.containerinstance/containergroups) [de YAML-referentiedocumentatie](container-instances-reference-yaml.md) voor meer informatie over de eigenschappen die u instellen.

Als u de configuratie van een containergroep wilt behouden, u de configuratie exporteren naar een YAML-bestand met behulp van de export van [az-container van][az-container-export]de opdracht Azure CLI. Met Exporteren u de configuraties van uw containergroep opslaan in versiebeheer voor 'configuratie als code'. Of gebruik het geëxporteerde bestand als uitgangspunt bij het ontwikkelen van een nieuwe configuratie in YAML.



## <a name="resource-allocation"></a>Resourcetoewijzing

Azure Container Instances wijst resources zoals CPU's, geheugen en optioneel [GPU's][gpus] (voorbeeld) toe aan een groep met meerdere containers door de [resourceaanvragen][resource-requests] van de instanties in de groep toe te voegen. Als u cpu-resources als voorbeeld neemt, als u een containergroep maakt met twee containerexemplaren, die elk 1 CPU aanvragen, krijgt de containergroep 2 CPU's toegewezen.

### <a name="resource-usage-by-container-instances"></a>Resourcegebruik per containerinstanties

Aan elke containerinstantie in een groep worden de resources toegewezen die zijn opgegeven in de resourceaanvraag. De maximale resources die door een containerinstantie in een groep worden gebruikt, kunnen echter verschillen als u de optionele [eigenschap resourcelimiet][resource-limits] configureert. De resourcelimiet van een containerinstantie moet groter zijn dan of gelijk zijn aan de eigenschap verplichte [resourceaanvraag.][resource-requests]

* Als u geen resourcelimiet opgeeft, is het maximale resourcegebruik van de containerinstantie hetzelfde als de resourceaanvraag.

* Als u een limiet opgeeft voor een containerinstantie, kan het maximale gebruik van de instantie groter zijn dan de aanvraag, tot de limiet die u hebt ingesteld. Het gebruik van resources door andere containerexemplaren in de groep kan dan ook afnemen. De maximale resourcelimiet die u instellen voor een containerinstantie, is de totale resources die aan de groep zijn toegewezen.
    
In een groep met twee containerexemplaren die elk 1 CPU aanvragen, kan een van uw containers een werkbelasting uitvoeren waarvoor meer CPU's moeten worden uitgevoerd dan de andere.

In dit scenario u een resourcelimiet van 2 CPU's instellen voor de containerinstantie. Met deze configuratie kan de containerinstantie maximaal de volledige 2 CPU's gebruiken indien beschikbaar.

### <a name="minimum-and-maximum-allocation"></a>Minimum- en maximale toewijzing

* Wijs **minimaal** 1 CPU en 1 GB geheugen toe aan een containergroep. Afzonderlijke containerexemplaren binnen een groep kunnen worden ingericht met minder dan 1 CPU en 1 GB geheugen. 

* Zie de [beschikbaarheid van resources][region-availability] voor Azure Container Instances in het implementatiegebied voor de **maximale** resources in een containergroep.

## <a name="networking"></a>Netwerken

Containergroepen kunnen een IP-adres op extern gericht adres, een of meer poorten op dat IP-adres en een DNS-label delen met een volledig gekwalificeerde domeinnaam (FQDN). Als u externe clients in staat wilt stellen een container binnen de groep te bereiken, moet u de poort op het IP-adres en vanuit de container blootstellen. Omdat containers binnen de groep een poortnaamruimte delen, wordt poorttoewijzing niet ondersteund. Het IP-adres van een containergroep en FQDN worden vrijgegeven wanneer de containergroep wordt verwijderd. 

Binnen een containergroep kunnen containerexemplaren elkaar bereiken via localhost op elke poort, zelfs als deze poorten niet extern worden weergegeven op het IP-adres van de groep of vanuit de container.

Implementeer eventueel containergroepen in een [virtueel Azure-netwerk,][virtual-network] zodat containers veilig kunnen communiceren met andere bronnen in het virtuele netwerk.

## <a name="storage"></a>Storage

U externe volumes opgeven die binnen een containergroep moeten worden gemonteerd. Ondersteunde volumes zijn:
* [Azure-bestandsshare][azure-files]
* [Geheim][secret]
* [Lege map][empty-directory]
* [Gekloonde git repo][volume-gitrepo]

U deze volumes toewijzen aan specifieke paden binnen de afzonderlijke containers in een groep. 

## <a name="common-scenarios"></a>Algemene scenario's

Multicontainergroepen zijn handig in gevallen waarin u één functionele taak wilt verdelen in een klein aantal containerafbeeldingen. Deze afbeeldingen kunnen vervolgens door verschillende teams worden geleverd en hebben afzonderlijke resourcevereisten.

Voorbeeldgebruik kan zijn:

* Een container met een webtoepassing en een container die de nieuwste inhoud uit bronbeheer haalt.
* Een applicatiecontainer en een loggingcontainer. De logging container verzamelt de logs en metrics output door de belangrijkste toepassing en schrijft ze naar langdurige opslag.
* Een toepassingscontainer en een bewakingscontainer. De bewakingscontainer doet periodiek een verzoek aan de toepassing om ervoor te zorgen dat deze correct wordt uitgevoerd en reageert, en geeft een waarschuwing als dit niet het is.
* Een front-end container en een back-end container. De front-end kan een webtoepassing bevatten, waarbij de back-end een service uitvoert om gegevens op te halen. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het implementeren van een containergroep met meerdere containers met een Azure Resource Manager-sjabloon:

> [!div class="nextstepaction"]
> [Een containergroep implementeren][resource-manager template]

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
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
