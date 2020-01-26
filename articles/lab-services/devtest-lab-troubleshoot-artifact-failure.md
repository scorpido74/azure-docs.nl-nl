---
title: Artefact fouten in een Azure DevTest Labs virtuele machine diagnosticeren
description: DevTest Labs biedt informatie die u kunt gebruiken om een artefact fout vast te stellen. In dit artikel leest u hoe u storingen in artefacten kunt oplossen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 7229f1ee4061eb38b7c6da09df21102ab302ab42
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760314"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Storingen in artefacten in het lab diagnosticeren 
Nadat u een artefact hebt gemaakt, kunt u controleren of deze is geslaagd of mislukt. Artefact Logboeken in Azure DevTest Labs bevatten informatie die u kunt gebruiken om een artefact fout vast te stellen. U hebt een aantal opties voor het weer geven van de artefact logboek gegevens voor een Windows-VM:

* In de Azure Portal
* In de VM

> [!NOTE]
> Om ervoor te zorgen dat fouten correct worden geïdentificeerd en verklaard, is het belang rijk dat het artefact de juiste structuur heeft. Zie [aangepaste artefacten maken](devtest-lab-artifact-author.md)voor meer informatie over het correct bouwen van een artefact. Als u een voor beeld van een correct gestructureerd artefact wilt zien, raadpleegt u het [test parameter typen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) artefacten.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Problemen met artefacten oplossen met behulp van de Azure Portal

1. Selecteer uw Lab in de Azure Portal in de lijst met resources.
2. Kies de Windows-VM die het artefact bevat dat u wilt onderzoeken.
3. Selecteer **artefacten**onder **Algemeen**in het linkerdeel venster. Er wordt een lijst weer gegeven met artefacten die zijn gekoppeld aan die VM. De naam van het artefact en de artefact status worden aangegeven.

   ![Artefactstatus](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Selecteer een artefact waarin de status **mislukt** wordt weer gegeven. Het artefact wordt geopend. Er wordt een uitbreidings bericht weer gegeven met details over het mislukken van het artefact.

   ![Fout bericht artefacten](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Problemen met artefacten in de virtuele machine oplossen

1. Meld u aan bij de virtuele machine die het artefact bevat dat u wilt diagnosticeren.
2. Ga naar C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1,9*\Status, waarbij *1,9* het versie nummer van de aangepaste script extensie voor Azure is.

   ![Het status bestand](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Open het **status** bestand.

Zie het volgende artikel voor instructies over het zoeken van de logboek bestanden op een **Linux** -VM: [Gebruik de aangepaste script extensie voor Azure versie 2 met virtuele Linux-machines](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>Gerelateerde blog berichten
* [Een virtuele machine toevoegen aan een bestaand Active Directory domein met behulp van een resource manager-sjabloon in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over hoe u [een Git-opslag plaats kunt toevoegen aan een Lab](devtest-lab-add-artifact-repo.md).

