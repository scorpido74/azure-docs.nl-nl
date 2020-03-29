---
title: VHD-bestand uploaden naar Azure DevTest Labs met PowerShell | Microsoft Documenten
description: In dit artikel vindt u een walkthrough waarin u laat zien hoe u een VHD-bestand uploadt naar Azure DevTest Labs met PowerShell.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a9e5a69cd7840bbaff081a4d47cb60f62a76eda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759752"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>VHD-bestand uploaden naar het opslagaccount van het lab met PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

In Azure DevTest Labs kunnen VHD-bestanden worden gebruikt om aangepaste afbeeldingen te maken, die worden gebruikt om virtuele machines in te richten. Met de volgende stappen u powershell gebruiken om een VHD-bestand te uploaden naar het opslagaccount van een lab. Zodra u uw VHD-bestand hebt geüpload, bevat de [sectie Volgende stappen](#next-steps) enkele artikelen die illustreren hoe u een aangepaste afbeelding maakt van het geüploade VHD-bestand. Zie [Inleiding tot beheerde schijven](../virtual-machines/linux/managed-disks-overview.md) voor meer informatie over schijven en VHD's in Azure

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

In de volgende stappen u een VHD-bestand uploaden naar Azure DevTest Labs met PowerShell. 

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met laboratoria het gewenste lab.  

1. Selecteer **Configuratie**op het blad van het lab. 

1. Selecteer **aangepaste afbeeldingen (VHD's)** in het **configuratieblad** van het lab.

1. Selecteer **Custom images** **+Toevoegen**. 

1. Selecteer **VHD**in het **aangepaste afbeeldingsblad** .

1. Selecteer op het **VHD-blad** de optie **Een VHD uploaden met PowerShell**.

    ![VHD uploaden met PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Kopieer het gegenereerde PowerShell-script naar een teksteditor in het **afbeeldingsblad uploaden met PowerShell-blad.**

1. Wijzig de parameter **LocalFilePath** van de cmdlet **Add-AzureVhd** om te wijzen op de locatie van het VHD-bestand dat u wilt uploaden.

1. Voer bij een PowerShell-prompt de cmdlet **Add-AzureVhd** uit (met de gewijzigde parameter **LocalFilePath).**

> [!WARNING] 
> 
> Het proces van het uploaden van een VHD-bestand kan lang zijn, afhankelijk van de grootte van het VHD-bestand en uw verbindingssnelheid.

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste afbeelding maken in Azure DevTest Labs vanuit een VHD-bestand met behulp van de Azure-portal](devtest-lab-create-template.md)
- [Een aangepaste afbeelding maken in Azure DevTest Labs vanuit een VHD-bestand met PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
