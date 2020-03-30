---
title: Tags toevoegen aan een lab in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het toevoegen van een tag aan een lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 1d7175d000eebd3f68665e7f097bbe3f68025bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302795"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Tags toevoegen aan een lab in Azure DevTest Labs

U aangepaste tags maken en deze toepassen op uw Resources van DevTest Labs om uw resources logisch te categoriseren. Later u snel en eenvoudig alle bronnen in uw abonnement zien die die tag hebben. Tags zijn handig wanneer u resources voor facturering of beheer moet ordenen.

Bronnen die worden ondersteund door tags, zijn o.a.

* VM's berekenen
* NIC’s
* IP-adressen
* Load balancers
* Opslagaccounts
* Managed Disks

U tags toepassen wanneer u [een lab maakt](devtest-lab-create-lab.md) en deze later beheren via het tagsblad onder Configuratie en instellingen.

Elke tag bestaat uit een/**naamwaardepaar.** **name** U bijvoorbeeld een tag maken met het *naamkostencentrum* met een waarde van *34543*. Een tag als deze kan u helpen om later labbronnen te identificeren die kunnen worden gefactureerd aan dit specifieke gebied van uw organisatie. U namen en waarden kiezen die zinvol zijn voor hoe u uw abonnement wilt organiseren.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Stappen om tags in een bestaand lab te beheren

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer indien nodig **Alle services**en selecteer **Vervolgens DevTest Labs** in de lijst. Uw lab wordt mogelijk al weergegeven op het dashboard onder **Alle bronnen**.
1. Selecteer in de lijst met labs het lab waarin u tags wilt toevoegen of beheren.
1. Selecteer **Configuratie en beleid**in het gebied **Overzicht** van het lab.

    ![Knop Configuratie en beleid](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. Selecteer **Labels**links onder **MANAGE**.
1. Als u een nieuwe tag voor dit lab **Save**wilt maken, voert u een paar **naamwaarden**/**Value** in en selecteert u Opslaan . U ook een bestaande tag in de lijst selecteren om de bronnen die aan die tag zijn gekoppeld, weer te geven of te beheren.

    ![Tags beheren](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> Tags die op labniveau zijn gemaakt, doorlopen alle factureerbare bronnen die het lab in uw abonnement opdraait. Lableveltags stromen bijvoorbeeld naar de onderliggende compute VM's van lab VM's.You tags gebruiken in de context van kostenbeheer. Tags op labniveau worden weergegeven in het tagfilter voor het kostenbeheer.

## <a name="understanding-limitations-to-tags"></a>Beperkingen voor tags begrijpen

Voor tags gelden de volgende beperkingen:

* Elke resource of resourcegroep mag maximaal 15 combinaties van tagnaam en -waarde hebben. Deze beperking geldt alleen voor tags die direct worden toegepast op de resourcegroep of resource. Een resourcegroep kan veel bronnen bevatten die elk 15 combinaties van tagnaam en -waarde bevatten.
* De tagnaam is beperkt tot 512 tekens en de tagwaarde is beperkt tot 256 tekens. Voor opslagaccounts is de tagnaam beperkt tot 128 tekens en de tagwaarde beperkt tot 256 tekens.
* Tags die zijn toegepast op de resourcegroep, worden niet overgenomen door de resources in deze resourcegroep.

[Gebruik tags om uw Azure-bronnen te ordenen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) met meer informatie over het gebruik van tags in Azure, waaronder het beheren van tags met PowerShell of Azure CLI.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Volgende stappen
* U beperkingen en conventies toepassen op uw abonnement met behulp van aangepast beleid. Een beleid dat u definieert, vereist mogelijk dat alle resources een waarde hebben voor een bepaalde tag. Zie [Beleid en planningen instellen](devtest-lab-set-lab-policy.md)voor meer informatie.
* Ontdek de [snelstartsjabloongalerie Voor Azure Resource Manager van DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
