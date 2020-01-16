---
title: Instellen van data science-omgevingen in Azure - Team Data Science Process
description: Instellen van data science-omgevingen op Azure voor gebruik in Team Data Science Process.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3e52f88136517339139bebd17dea929ac02201e3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982032"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Gegevenswetenschapomgevingen instellen voor gebruik in Team Data Science Process
Het Team Data Science Process maakt gebruik van verschillende gegevenswetenschapomgevingen voor de opslag, verwerking en analyse van gegevens. Ze bevatten verschillende typen virtuele machines van Azure, HDInsight (Hadoop)-clusters en Azure Machine Learning-werkruimten in Azure Blob Storage. De beslissing over welke omgeving u wilt gebruiken is afhankelijk van het type en de hoeveelheid gegevens die worden gemodelleerd en de bestemming voor die gegevens in de cloud. 

* Zie voor hulp bij het vragen om te overwegen bij het maken van deze beslissing, [van plan bent uw Azure Machine Learning Data Science-omgeving](plan-your-environment.md). 
* Zie voor een catalogus van enkele van de scenario's die optreden tijdens het uitvoeren van geavanceerde analyses, [scenario's voor het Team Data Science Process](plan-sample-scenarios.md)

De volgende artikelen wordt beschreven hoe u de verschillende data science-omgevingen die worden gebruikt door het Team Data Science Process instelt.

* [Azure storage-account](../../storage/common/storage-account-create.md)
* [HDInsight (Hadoop)-cluster](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio (klassieke) werk ruimte](../studio/create-workspace.md)

De **Microsoft Data Science Virtual Machine (DSVM)** is ook beschikbaar als een installatiekopie van een virtuele Azure-machine (VM). Deze virtuele machine is vooraf geïnstalleerd en geconfigureerd met verschillende populaire hulpprogramma's die vaak worden gebruikt voor gegevensanalyse en machine learning. De DSVM is beschikbaar voor zowel Windows als Linux. Zie voor meer informatie, [inleiding op de cloud gebaseerde Data Science Virtual Machine voor Linux en Windows](../data-science-virtual-machine/overview.md).

Meer informatie over het maken van:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu-DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
