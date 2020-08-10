---
title: Overzicht van Azure DevOps Starter | Microsoft Docs
description: Lees hier meer over hoe u met Azure DevOps Starter apps kunt starten, implementeren en beheren vanuit één weergave in de Azure-portal.
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 91e62cae242279e1923fc31970c233ec70bf7f11
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461337"
---
# <a name="overview-of-azure-devops-starter"></a>Overzicht van Azure DevOps Starter

 Met Azure DevOps Starter kunt u eenvoudig aan de slag met Azure. U kunt met enkele eenvoudige stappen vanuit de Azure-portal uw favoriete type app starten in de Azure-service van uw keuze. 

 DevOps Starter stelt alles in wat u nodig hebt voor het ontwikkelen, implementeren en controleren van uw toepassing. Met het DevOps Starter-dashboard kunt u het doorvoeren, compileren en implementeren van code controleren, allemaal vanuit één weergave in de Azure-portal.

## <a name="advantages-of-using-devops-starter"></a>Voordelen van het gebruik van DevOps Starter

  DevOps Starter automatiseert de configuratie van een volledige pijplijn voor continue integratie (CI) en continue levering (CD) naar Azure.  U kunt beginnen met bestaande code of een van de gegeven voorbeeldtoepassingen gebruiken. Vervolgens kunt u die toepassing snel implementeren in verschillende Azure-services, zoals Virtual Machines, App Service, Azure Kubernetes Service (AKS), Azure SQL Database en Azure Service Fabric.  

  DevOps Starter verricht alle taken voor de eerste configuratie van een DevOps-pijplijn, met inbegrip van het instellen van de initiële Git-opslagplaats, het configureren van de CI/CD-pijplijn, het maken van een Application Insights-resource voor controledoeleinden en het genereren van één enkele weergave van de complete oplossing via een DevOps Projects-dashboard in de Azure-portal.

U kunt DevOps Starter gebruiken om:

* Snel een toepassing implementeren in Azure
* De installatie van een VSTS CI/CD-pijplijn te automatiseren
* Te bekijken en begrijpen hoe u een CI/CD-pijplijn correct instelt
* De release-pijplijnen verder aan te passen op basis van uw specifieke scenario's

## <a name="how-to-use-devops-starter"></a>Hoe kunt u DevOps Starter gebruiken?

  DevOps Starter is beschikbaar via de Azure-portal. U maakt een DevOps Starter-resource in de portal net zoals elke andere Azure-resource. DevOps Projects werkt zoals een wizard, wat inhoudt dat de verschillende configuratieopties achtereenvolgens worden aangeboden.  

U kiest een aantal configuratieopties om de eerste installatie uit te voeren. Het gaat om deze opties:

* De bijgeleverde voorbeeld-app of uw eigen code gebruiken
* Een taal voor de app selecteren
* Een app-framework kiezen op basis van de taal
* Een Azure-service (implementatiedoel) selecteren
* Een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken 
* Uw Azure-abonnement kiezen
* De locatie van Azure-services kiezen
* Kiezen uit verschillende prijscategorieën voor Azure-services

Nadat u DevOps Starter hebt gebruikt, kunt u alle resources ook vanaf één locatie verwijderen, namelijk vanuit het DevOps Starter-dashboard in de Azure-portal.

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter en Azure DevOps integreren

DevOps Starter werkt op basis van Azure DevOps. DevOps Starter automatiseert al het werk dat in Azure Pipelines nodig is voor het instellen van een CI/CD-pijplijn. Het maakt een Git-repository in een nieuwe of bestaande Azure DevOps-organisatie en voert vervolgens een voorbeeldtoepassing of uw bestaande code door in een nieuwe Git-repository.  

Met de automatisering wordt ook een CI-trigger voor de build gemaakt, zodat elke nieuwe codedoorvoer een build tot gevolg heeft. DevOps Starter maakt ook een CD-trigger en implementeert elke nieuwe geslaagde build in de Azure-service van uw keuze.  

De build- en release-pijplijnen kunnen worden aangepast voor aanvullende scenario's. U kunt de build- en release-pijplijnen ook klonen voor gebruik in andere projecten.

Nadat het DevOps Starter-project is gemaakt, kunt u het volgende doen:

* De build- en releasepijplijn aanpassen
* Met behulp van pull-aanvragen de codestroom beheren en een hoge kwaliteit garanderen
* Elke doorvoer (commit) testen en compileren voordat u de code samenvoegt om de kwaliteit nog verder te verbeteren
* Achterstand en problemen samen met de toepassing bijhouden

## <a name="getting-started-with-devops-starter"></a>Aan de slag met DevOps Starter

* [Aan de slag met DevOps Starter](https://docs.microsoft.com/azure/devops-project/azure-devops-project-github)

##  <a name="devops-starter-videos"></a>Video's over DevOps Starter

* [Create CI/CD with Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8) (Engelstalig)
