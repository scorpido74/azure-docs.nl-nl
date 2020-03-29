---
title: Artefact-fouten in een virtuele azure DevTest Labs-machine diagnosticeren
description: DevTest Labs bieden informatie die u gebruiken om een artefact-fout te diagnosticeren. In dit artikel ziet u hoe u artefact-fouten oplossen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760314"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Artefact-fouten in het lab diagnosticeren 
Nadat u een artefact hebt gemaakt, u controleren of het is gelukt of mislukt. Artefactlogboeken in Azure DevTest Labs bieden informatie die u gebruiken om een artefact-fout te diagnosticeren. U hebt een aantal opties voor het bekijken van de artefact-logboekgegevens voor een Windows-vm:

* In de Azure Portal
* In de VM

> [!NOTE]
> Om ervoor te zorgen dat fouten correct worden geïdentificeerd en uitgelegd, is het belangrijk dat het artefact de juiste structuur heeft. Zie [Aangepaste artefacten maken](devtest-lab-artifact-author.md)voor informatie over het correct construeren van een artefact. Als u een voorbeeld van een goed gestructureerd artefact wilt zien, raadpleegt u het artefact [Voor parametertypen testen.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Artefact-fouten oplossen met behulp van de Azure-portal

1. Selecteer uw lab in de Azure-portal in uw lijst met bronnen.
2. Kies de Windows-VM met het artefact dat u wilt onderzoeken.
3. Selecteer in het linkerdeelvenster onder **ALGEMEEN**de optie **Artefacten**. Er wordt een lijst weergegeven met artefacten die aan die VM zijn gekoppeld. De naam van het artefact en de artefactstatus worden aangegeven.

   ![Artefactstatus](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Selecteer een artefact met een **mislukte** status. Het artefact gaat open. Er wordt een extensiebericht weergegeven met details over het mislukken van het artefact.

   ![Foutbericht Artefact](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Artefact-fouten oplossen vanuit de virtuele machine

1. Meld u aan bij de VM met het artefact dat u wilt diagnosticeren.
2. Ga naar C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, waarbij *1.9* het versienummer van de Azure Custom Script Extension is.

   ![Het statusbestand](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Open het **statusbestand.**

Zie het volgende artikel: Gebruik de Azure Custom [Script Extension Version 2 met Virtuele Machines van Linux](../virtual-machines/extensions/custom-script-linux.md#troubleshooting) voor instructies over het vinden van de logbestanden op een **Linux-vm.**


## <a name="related-blog-posts"></a>Gerelateerde blogberichten
* [Een VM deelnemen aan een bestaand Active Directory-domein met behulp van een resourcemanagersjabloon in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [toevoegen van een Git-repository aan een lab.](devtest-lab-add-artifact-repo.md)

