---
title: VHD-bestand uploaden naar Azure DevTest Labs met AzCopy | Microsoft Documenten
description: In dit artikel vindt u een walkthrough om het azcopy-hulpprogramma te gebruiken om een VHD-bestand te uploaden naar het opslagaccount van een lab in Azure DevTest Labs.
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
ms.openlocfilehash: 49dc70788bf2a44b6925c5f3f8226fdadab8768c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76757419"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>VHD-bestand uploaden naar het opslagaccount van het lab met AzCopy

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

In Azure DevTest Labs kunnen VHD-bestanden worden gebruikt om aangepaste afbeeldingen te maken, die worden gebruikt om virtuele machines in te richten. De volgende stappen doorlopen u met behulp van het AzCopy-opdrachtregelhulpprogramma om een VHD-bestand te uploaden naar het opslagaccount van een lab. Zodra u uw VHD-bestand hebt geüpload, bevat de [sectie Volgende stappen](#next-steps) enkele artikelen die illustreren hoe u een aangepaste afbeelding maakt van het geüploade VHD-bestand. Zie [Inleiding tot beheerde schijven](../virtual-machines/linux/managed-disks-overview.md) voor meer informatie over schijven en VHD's in Azure

> [!NOTE] 
>  
> AzCopy is een Windows-only command-line utility.

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

De volgende stappen lopen u door het uploaden van een VHD-bestand naar Azure DevTest Labs met behulp van [AzCopy](https://aka.ms/downloadazcopy). 

1. Download de naam van het opslagaccount van het lab via de Azure-portal:

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met laboratoria het gewenste lab.  

1. Selecteer **Configuratie**op het blad van het lab. 

1. Selecteer **aangepaste afbeeldingen (VHD's)** in het **configuratieblad** van het lab.

1. Selecteer **Custom images** **+Toevoegen**. 

1. Selecteer **VHD**in het **aangepaste afbeeldingsblad** .

1. Selecteer op het **VHD-blad** de optie **Een VHD uploaden met PowerShell**.

    ![VHD uploaden met PowerShell](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. Met **het uploaden van een afbeelding met PowerShell-blade** wordt een aanroep naar de cmdlet **Add-AzureVhd** weergegeven. De eerste parameter (*Bestemming*) bevat de URI voor een blobcontainer *(uploads)* in de volgende indeling:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Noteer de volledige URI zoals deze in latere stappen wordt gebruikt.

1. Upload het VHD-bestand met AzCopy:
 
1. [Download en installeer de nieuwste versie van AzCopy.](https://aka.ms/downloadazcopy)

1. Open een opdrachtvenster en navigeer naar de azcopy-installatiemap. Optioneel u de installatielocatie van AzCopy toevoegen aan uw systeempad. AzCopy is standaard geïnstalleerd in de volgende map:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Voer met de opslagaccountsleutel en blobcontainer URI de volgende opdracht uit op de opdrachtprompt. De *vhdFileName-waarde* moet tussen aanhalingstekens staan. Het proces van het uploaden van een VHD-bestand kan lang zijn, afhankelijk van de grootte van het VHD-bestand en uw verbindingssnelheid.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste afbeelding maken in Azure DevTest Labs vanuit een VHD-bestand met behulp van de Azure-portal](devtest-lab-create-template.md)
- [Een aangepaste afbeelding maken in Azure DevTest Labs vanuit een VHD-bestand met PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)