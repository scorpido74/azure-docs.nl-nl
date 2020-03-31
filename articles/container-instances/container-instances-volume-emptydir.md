---
title: LeegDir-volume monteren in containergroep
description: Meer informatie over het monteren van een leegDir-volume om gegevens te delen tussen de containers in een containergroep in Azure Container Instances
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117749"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Een leegDir-volume monteren in Azure Container Instances

Meer informatie over het monteren van een *leeg Dir-volume* om gegevens te delen tussen de containers in een containergroep in Azure Container Instances. Gebruik *lege Dir-volumes* als tijdelijke caches voor uw containerized workloads.

> [!NOTE]
> Het monteren van een *leegDir-volume* is momenteel beperkt tot Linux-containers. Terwijl we werken aan het brengen van alle functies naar Windows-containers, u actuele platformverschillen vinden in het [overzicht.](container-instances-overview.md#linux-and-windows-containers)

## <a name="emptydir-volume"></a>leegDir-volume

Het *emptyDir-volume* biedt een beschrijfbare map die toegankelijk is voor elke container in een containergroep. Containers in de groep kunnen dezelfde bestanden in het volume lezen en schrijven en deze kunnen worden gemonteerd met dezelfde of verschillende paden in elke container.

Enkele voorbeelden worden gebruikt voor een *leegDir-volume:*

* Krasruimte
* Controlecontrole tijdens langlopende taken
* Gegevens opslaan die door een zijspancontainer zijn opgehaald en door een toepassingscontainer worden aangeboden

Gegevens in een *leegDir-volume* blijven bestaan door containercrashes. Containers die opnieuw worden gestart, zijn echter niet gegarandeerd dat de gegevens in een *leegDir-volume* blijven bestaan. Als u een containergroep stopt, blijft het *lege Dir-volume* niet bestaan.

De maximale grootte van een Linux *leegDir* volume is 50 GB.

## <a name="mount-an-emptydir-volume"></a>Een leegDir-volume monteren

Als u een leeg Dir-volume wilt monteren in een containerinstantie, u implementeren met behulp van een [Azure Resource Manager-sjabloon,](/azure/templates/microsoft.containerinstance/containergroups)een [YAML-bestand](container-instances-reference-yaml.md)of andere programmatische methoden om een containergroep te implementeren.

Vul eerst `volumes` de array in `properties` het gedeelte containergroep van het bestand in. Vul vervolgens voor elke container in de containergroep waarin u het *lege Dir-volume* wilt monteren, de `volumeMounts` array in het `properties` gedeelte van de containerdefinitie.

Met de volgende sjabloon Resourcemanager wordt bijvoorbeeld een containergroep gemaakt die bestaat uit twee containers, die elk het *lege Dir-volume* monteert:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Zie [Een multicontainergroep implementeren met behulp van een resourcemanagersjabloon](container-instances-multi-container-group.md) en Een [multicontainergroep implementeren met behulp van een YAML-bestand](container-instances-multi-container-yaml.md)als u voorbeelden van implementatie van containergroepen wilt zien.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het monteren van andere volumetypen in Azure Container Instances:

* [Een Azure-bestandsshare koppelen in Azure Container Instances](container-instances-volume-azure-files.md)
* [Een gitRepo-volume monteren in Azure Container Instances](container-instances-volume-gitrepo.md)
* [Een geheim volume in Azure Container Instances monteren](container-instances-volume-secret.md)