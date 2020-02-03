---
title: Instellen van data science-omgevingen in Azure - Team Data Science Process
description: Instellen van data science-omgevingen op Azure voor gebruik in Team Data Science Process.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d11e1614307d5b6cb267ae72bae6f32adb4b244a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722217"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Gegevenswetenschapomgevingen instellen voor gebruik in Team Data Science Process
Het Team Data Science Process maakt gebruik van verschillende gegevenswetenschapomgevingen voor de opslag, verwerking en analyse van gegevens. Ze bevatten verschillende typen virtuele machines van Azure, HDInsight (Hadoop)-clusters en Azure Machine Learning-werkruimten in Azure Blob Storage. De beslissing over welke omgeving u wilt gebruiken is afhankelijk van het type en de hoeveelheid gegevens die worden gemodelleerd en de bestemming voor die gegevens in de cloud. 

* Zie [uw Azure machine learning data Science Environment plannen](plan-your-environment.md)voor meer informatie over vragen over het nemen van deze beslissing. 
* Zie [scenario's voor het team data Science process](plan-sample-scenarios.md) voor een catalogus met een aantal scenario's die u kunt tegen komen bij het uitvoeren van geavanceerde analyses.

De volgende artikelen wordt beschreven hoe u de verschillende data science-omgevingen die worden gebruikt door het Team Data Science Process instelt.

* [Azure-opslag account](../../storage/common/storage-account-create.md)
* [HDInsight-cluster (Hadoop)](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio (klassieke) werk ruimte](../studio/create-workspace.md)

**Micro soft data Science virtual machine (DSVM)** is ook beschikbaar als een installatie kopie van een virtuele machine van Azure (VM). Deze virtuele machine is vooraf geïnstalleerd en geconfigureerd met verschillende populaire hulpprogramma's die vaak worden gebruikt voor gegevensanalyse en machine learning. De DSVM is beschikbaar voor zowel Windows als Linux. Zie [Introduction to the cloud data Science virtual machine voor Linux en Windows](../data-science-virtual-machine/overview.md)voor meer informatie.

Meer informatie over het maken van:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
