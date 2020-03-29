---
title: Een aangepaste afbeelding maken van VHD-bestand met Azure PowerShell
description: Het maken van een aangepaste afbeelding in Azure DevTest Labs automatiseren vanuit een VHD-bestand met PowerShell
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: cd144659dd8a8e981e267be998c9c783b7482840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169580"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Een aangepaste afbeelding maken op basis van een VHD-bestand met PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

In de volgende stappen u een aangepaste afbeelding maken van een VHD-bestand met PowerShell:

1. Meld u bij een PowerShell-prompt aan bij uw Azure-account met de volgende oproep bij de cmdlet **Connect-AzAccount.**

    ```powershell
    Connect-AzAccount
    ```

1.  Selecteer het gewenste Azure-abonnement door de cmdlet **Select-AzSubscription** aan te roepen. Vervang de volgende tijdelijke aanduiding voor de **$subscriptionId** variabele door een geldige Azure-abonnements-id.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Haal het labobject op door de **get-AzResource-cmdlet** te bellen. Vervang de volgende tijdelijke aanduidingen voor de **$labRg** en **$labName** variabelen door de juiste waarden voor uw omgeving.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Vervang de volgende tijdelijke aanduiding voor de **$vhdUri** variabele door de URI naar uw geüploade VHD-bestand. U de URI van het VHD-bestand ophalen via het blobblad van het opslagaccount in de Azure-portal.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Maak de aangepaste afbeelding met de cmdlet **Nieuw-AzResourceGroupDeployment.** Vervang de volgende tijdelijke aanduidingen voor de **$customImageName** en **$customImageDescription** variabelen in betekenisvolle namen voor uw omgeving.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>PowerShell-script om een aangepaste afbeelding te maken op basis van een VHD-bestand

Het volgende PowerShell-script kan worden gebruikt om een aangepaste afbeelding te maken op basis van een VHD-bestand. Vervang de tijdelijke aanduidingen (begin en einde met hoekbeugels) door de juiste waarden voor uw behoeften.

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Gerelateerde blogberichten

- [Aangepaste afbeeldingen of formules?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Aangepaste afbeeldingen kopiëren tussen Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Volgende stappen

- [Een vm toevoegen aan uw lab](devtest-lab-add-vm.md)
