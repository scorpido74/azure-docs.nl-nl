---
title: Data science-omgevingen instellen in Azure - Team Data Science Process
description: Stel data science-omgevingen in op Azure voor gebruik in het Team Data Science Process.
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
ms.openlocfilehash: 6d1185e13397b3e466bd1e8d609a46f2f68bf390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063942"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Gegevenswetenschapomgevingen instellen voor gebruik in Team Data Science Process
Het Team Data Science Process maakt gebruik van verschillende data science omgevingen voor de opslag, verwerking en analyse van gegevens. Ze omvatten Azure Blob Storage, verschillende typen Azure virtuele machines, HDInsight (Hadoop) clusters, en Azure Machine Learning werkruimten. De beslissing over welke omgeving u moet gebruiken, is afhankelijk van het type en de hoeveelheid gegevens die moeten worden gemodelleerd en de doelbestemming voor die gegevens in de cloud. 

* Zie Uw Azure Machine Learning Data [Science-omgeving plannen](plan-your-environment.md)voor richtlijnen over vragen die u moet overwegen bij het maken van deze beslissing. 
* Zie [Scenario's voor het Team Data Science-proces voor](plan-sample-scenarios.md) een catalogus met enkele scenario's die u tegenkomen bij het uitvoeren van geavanceerde analyses.

In de volgende artikelen wordt beschreven hoe u de verschillende data science-omgevingen instelt die worden gebruikt door het Team Data Science Process.

* [Azure-opslagaccount](../../storage/common/storage-account-create.md)
* [HDInsight (Hadoop) cluster](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio (klassieke) werkruimte](../studio/create-workspace.md)

De **Microsoft Data Science Virtual Machine (DSVM)** is ook beschikbaar als een Azure virtual machine (VM) afbeelding. Deze VM is vooraf geïnstalleerd en geconfigureerd met verschillende populaire tools die vaak worden gebruikt voor data-analyse en machine learning. De DSVM is beschikbaar op zowel Windows als Linux. Zie [Inleiding tot de cloudgebaseerde Data Science Virtual Machine voor Linux en Windows voor](../data-science-virtual-machine/overview.md)meer informatie.

Meer informatie over het maken van:

- [Windows-DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu-DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS-DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
