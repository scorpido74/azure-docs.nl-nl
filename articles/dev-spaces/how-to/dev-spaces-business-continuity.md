---
title: Bedrijfscontinuïteit en herstel na noodgevallen
services: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Leer Azure Dev Spaces en Azure Kubernetes Services gebruiken om bedrijfscontinuïteit te bieden en u voor te bereiden op herstel na noodgevallen
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 37c0048bfa7e72b25eb56603fc027045eba25cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295824"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Bedrijfscontinuïteit en noodherstel in Azure Dev Spaces

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Richtlijnen voor noodherstel voor Azure Kubernetes Service (AKS) bekijken

Azure Dev Spaces is een functie van Azure Kubernetes Service (AKS). U moet op de hoogte zijn van richtlijnen voor herstel na noodgevallen in AKS en overwegen of deze van toepassing zijn op de AKS-clusters die u voor Dev Spaces gebruikt. Raadpleeg aanbevolen procedures [voor bedrijfscontinuïteit en disaster recovery in Azure Kubernetes Service (AKS) voor](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) meer informatie.

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Dev Spaces inschakelen op AKS-clusters in verschillende regio's

Als u Dev Spaces inschakelt op AKS-clusters in verschillende regio's, u dev-ruimten onmiddellijk na een azure-regiofout weer gebruiken.

Zie Plannen voor implementatie met [meerdere regio's voor](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment) algemene informatie over implementaties met meerdere regio's van AKS

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Dev Spaces inschakelen via de Azure-portal

Selecteer het **menu-item Dev Spaces** onder de instellingen van elk cluster in de Azure-portal. Kies vervolgens de optie om Dev Spaces in te schakelen en op te slaan.

![Dev Spaces inschakelen via Azure-portal](../media/common/enable-dev-spaces.jpg)

Herhaal dit proces voor elk cluster.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Dev Spaces inschakelen via de Azure CLI

U dev-spaties ook inschakelen op de opdrachtregel:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>De basislijn van uw team implementeren voor elk cluster

Wanneer u met Dev Spaces werkt, implementeert u doorgaans de volledige toepassing naar een bovenliggende ontwikkelaarsruimte op uw Kubernetes-cluster. Standaard wordt `default` de ruimte gebruikt. De eerste implementatie omvat alle services en de externe resources waarvan deze services afhankelijk zijn, zoals databases of wachtrijen. Dit staat bekend als de *basislijn.* Zodra u een basislijn in de bovenliggende dev-ruimte hebt ingesteld, u afzonderlijke services in onderliggende dev-ruimten herhalen en debuggen.

U moet de meest recente versies van uw basislijnset van services implementeren in clusters in meerdere regio's. Als u uw basislijnservices op deze manier bijwerkt, u Dev Spaces blijven gebruiken als er een Azure-regiomislukt is. Als u bijvoorbeeld uw basislijn implementeert via een CI/CD-pijplijn, wijzigt u de pijplijn zodat deze wordt geïmplementeerd in meerdere clusters in verschillende regio's.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Selecteer het juiste AKS-cluster dat u wilt gebruiken voor Dev Spaces

Zodra u een back-upcluster met de basislijn van uw team hebt geconfigureerd, u op elk gewenst moment snel overschakelen naar het back-upcluster. Vervolgens u de afzonderlijke services die u werkt in onderliggende dev-ruimten opnieuw uitvoeren.

Selecteer een ander cluster met de volgende opdracht CLI:

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Met de volgende opdracht u de beschikbare dev-ruimten op het nieuwe cluster weergeven:

```cmd
azds space list
```

U een nieuwe dev-ruimte maken om in te werken of een bestaande dev-ruimte selecteren met de volgende opdracht:

```cmd
azds space select -n <space name>
```

Na het uitvoeren van deze opdrachten wordt de geselecteerde cluster- en dev-ruimte gebruikt voor volgende CLI-bewerkingen en voor het opsporen van projecten met behulp van de visual studiocode-extensie voor Azure Dev Spaces.

Als u Visual Studio gebruikt, u het cluster dat door een bestaand project wordt gebruikt, via de volgende stappen schakelen:

1. Open uw project in Visual Studio.
1. Klik met de rechtermuisknop op de projectnaam in Solution Explorer en klik op **Eigenschappen**
1. Klik in het linkerdeelvenster op **Foutopsporing**
1. Klik op de pagina Eigenschappen van Foutopsporing op de vervolgkeuzelijst **Profiel** en kies **Azure Dev Spaces**.
1. Klik op de knop **Wijzigen.**
1. Selecteer in het dialoogvenster dat wordt weergegeven het AKS-cluster dat u wilt gebruiken. Kies desgewenst een andere dev-ruimte om in te werken of maak een nieuwe dev-ruimte door de juiste optie te selecteren in de vervolgkeuzelijst **Ruimte.**

Zodra u het juiste cluster en de juiste ruimte hebt geselecteerd, u op F5 drukken om de service in Dev Spaces uit te voeren.

Herhaal deze stappen voor andere projecten die zijn geconfigureerd om het oorspronkelijke cluster te gebruiken.

## <a name="access-a-service-on-a-backup-cluster"></a>Toegang tot een service op een back-upcluster

Als u uw service hebt geconfigureerd om een openbare DNS-naam te gebruiken, heeft de service een andere URL als u deze uitvoert op een back-upcluster. Openbare DNS-namen staan `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`altijd in de indeling. Als u overschakelt naar een ander cluster, veranderen de cluster-GUID en mogelijk de regio.

Dev Spaces toont altijd de juiste `azds up`URL voor de service wanneer deze wordt uitgevoerd of in het venster Uitvoer in Visual Studio onder **Azure Dev Spaces**.

U de URL ook `azds list-uris` vinden door de opdracht uit te voeren:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Gebruik deze URL bij het openen van de service.
