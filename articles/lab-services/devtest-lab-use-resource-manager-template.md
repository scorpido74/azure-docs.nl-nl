---
title: De Azure Resource Manager-sjabloon van een virtuele machine weergeven en gebruiken
description: Meer informatie over het gebruik van de azure resource manager-sjabloon van een virtuele machine om andere VM's te maken
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 7064fdeec04f4dc5ae2c73c1a3896cf2d10dd01d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169121"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Virtuele machines maken met een Azure Resource Manager-sjabloon 

Wanneer u een virtuele machine (VM) maakt in DevTest Labs via de [Azure-portal,](https://go.microsoft.com/fwlink/p/?LinkID=525040)u de sjabloon Azure Resource Manager bekijken voordat u de VM opslaat. De sjabloon kan vervolgens worden gebruikt als basis om meer lab VM's met dezelfde instellingen te maken.

In dit artikel worden multi-VM-versus single-VM Resource Manager-sjablonen beschreven en wordt u weergegeven hoe u een sjabloon weergeven en opslaan bij het maken van een vm.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Multi-VM vs. single-VM Resource Manager-sjablonen
Er zijn twee manieren om VM's te maken in DevTest Labs met behulp van een Resource Manager-sjabloon: de Microsoft.DevTestLab/labs/virtualmachines-bron inrichten of de Microsoft.Compute/virtualmachines-bron inrichten. Elk wordt gebruikt in verschillende scenario's en vereist verschillende machtigingen.

- Resourcebeheersjablonen die gebruik maken van een resourcetype Microsoft.DevTestLab/labs/virtualmachines (zoals aangegeven in de eigenschap Resource in de sjabloon) kunnen afzonderlijke laboratorium-VM's inrichten. Elke VM wordt vervolgens weergegeven als één item in de lijst met virtuele machines van DevTest Labs:

   ![Lijst van VM's als afzonderlijke items in de lijst met virtuele machines van DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Dit type Resourcemanager-sjabloon kan worden ingericht via de opdracht **New-AzResourceGroupDeployment in Azure PowerShell** of via de implementatie van de Azure CLI-opdracht **az-groep maken.** Het vereist beheerdersmachtigingen, zodat gebruikers die zijn toegewezen met een DevTest Labs-gebruikersrol de implementatie niet kunnen uitvoeren. 

- Resourcebeheersjablonen die een resourcetype Microsoft.Compute/virtualmachines gebruiken, kunnen meerdere VM's inrichten als één omgeving in de lijst met virtuele machines van DevTest Labs:

   ![Lijst van VM's als afzonderlijke items in de lijst met virtuele machines van DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   VM's in dezelfde omgeving kunnen samen worden beheerd en dezelfde levenscyclus delen. Gebruikers die zijn toegewezen met een DevTest Labs-gebruikersrol, kunnen omgevingen maken met behulp van deze sjablonen, zolang de beheerder het lab op die manier heeft geconfigureerd.

In de rest van dit artikel worden Resource Manager-sjablonen besproken die microsoft.DevTestLab/labs/virtualmachines gebruiken. Deze worden gebruikt door labadmins om het maken van lab-vm's (bijvoorbeeld claimbare VM's) of het genereren van gouden afbeeldingen (bijvoorbeeld beeldfabriek) te automatiseren.

[Aanbevolen procedures voor het maken van Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) bieden veel richtlijnen en suggesties om u te helpen azure resource manager-sjablonen te maken die betrouwbaar en gebruiksvriendelijk zijn.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>De resourcemanagersjabloon van een virtuele machine weergeven en opslaan
1. Volg de stappen bij [Uw eerste VM maken in een lab](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) om een virtuele machine te maken.
1. Voer de vereiste informatie voor uw virtuele machine in en voeg alle gewenste artefacten toe voor deze virtuele machine.
1. Swtich naar het tabblad **Geavanceerde instellingen.** 
1. Kies onder aan het venster Instellingen configureren de **optie ARM-sjabloon weergeven**.
1. Kopieer en sla de sjabloon Resourcemanager op om later te gebruiken om een andere virtuele machine te maken.

   ![Resourcemanager-sjabloon om op te slaan voor later gebruik](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Nadat u de sjabloon Resourcebeheer hebt opgeslagen, moet u de parameterssectie van de sjabloon bijwerken voordat u deze gebruiken. U een parameter.json maken die alleen de parameters aanpast, buiten de werkelijke resourcemanagersjabloon. 

![Parameters aanpassen met een JSON-bestand](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

De sjabloon Resourcemanager is nu klaar voor gebruik om een VM te [maken.](devtest-lab-create-environment-from-arm.md)

## <a name="set-expiration-date"></a>Vervaldatum instellen
In scenario's zoals training, demo's en proefversies u virtuele machines maken en deze automatisch verwijderen na een vaste duur, zodat u geen onnodige kosten hoeft te maken. U een lab-vm maken met een vervaldatum door de eigenschap **expiratiedatum** voor de VM op te geven. Bekijk dezelfde Resource Manager-sjabloon in [onze GitHub-repository.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration)



### <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [maken van multi-VM-omgevingen met Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md).
* [Een resourcemanagersjabloon implementeren om een vm te maken](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Ontdek meer quickstart Resource Manager-sjablonen voor DevTest Labs-automatisering vanuit de [openbare DevTest Labs GitHub-repo.](https://github.com/Azure/azure-quickstart-templates)
