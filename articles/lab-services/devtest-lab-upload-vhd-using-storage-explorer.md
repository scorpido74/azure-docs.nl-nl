---
title: VHD-bestand uploaden naar Azure DevTest Labs met Storage Explorer
description: VHD-bestand uploaden naar het opslagaccount van het lab met Microsoft Azure Storage Explorer
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
ms.openlocfilehash: defafdd5809b7e537b3b9abb78f8cb63d0033c16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170378"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>VHD-bestand uploaden naar het opslagaccount van het lab met Microsoft Azure Storage Explorer

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

In Azure DevTest Labs kunnen VHD-bestanden worden gebruikt om aangepaste afbeeldingen te maken, die worden gebruikt om virtuele machines in te richten. In dit artikel wordt uitgelegd hoe u [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) gebruiken om een VHD-bestand te uploaden naar het opslagaccount van een lab. Zodra u uw VHD-bestand hebt geüpload, bevat de [sectie Volgende stappen](#next-steps) enkele artikelen die illustreren hoe u een aangepaste afbeelding maakt van het geüploade VHD-bestand. Zie [Inleiding tot beheerde schijven](../virtual-machines/linux/managed-disks-overview.md) voor meer informatie over schijven en VHD's in Azure

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

De volgende stappen doorlopen u bij het uploaden van een VHD-bestand naar DevTest Labs met Behulp van [Microsoft Azure Storage Explorer.](../vs-azure-tools-storage-manage-with-storage-explorer.md)

1. [Download en installeer de nieuwste versie van de Microsoft Azure Storage Explorer](https://www.storageexplorer.com).

1. Download de naam van het opslagaccount van het lab via de Azure-portal:

    1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
    
    1. Selecteer in de lijst met laboratoria het gewenste lab.  
    
    1. Selecteer **Configuratie**op het blad van het lab. 
    
    1. Selecteer **aangepaste afbeeldingen (VHD's)** in het **configuratieblad** van het lab.
    
    1. Selecteer **Custom images** **+Toevoegen**. 
    
    1. Selecteer **VHD**in het **aangepaste afbeeldingsblad** .
    
    1. Selecteer op het **VHD-blad** de optie **Een VHD uploaden met PowerShell**.
    
        ![VHD uploaden met PowerShell][0]
    
    1. Met **het uploaden van een afbeelding met PowerShell-blade** wordt een aanroep naar de cmdlet **Add-AzureVhd** weergegeven. De eerste parameter (*Bestemming*) bevat de naam van het opslagaccount voor het lab in de volgende indeling:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Noteer de naam van het opslagaccount zoals deze in latere stappen wordt gebruikt.
    
1. Maak verbinding met een Azure-abonnementsaccount met Storage Explorer.

    > [!TIP] 
    > 
    > Storage Explorer ondersteunt verschillende verbindingsopties. In deze sectie wordt uitgelegd dat u verbinding maakt met een opslagaccount dat is gekoppeld aan uw Azure-abonnement. Als u de andere verbindingsopties wilt bekijken die door Storage Explorer worden ondersteund, raadpleegt u het artikel Aan de [slag met Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Open Storage Explorer.
    
    1. Selecteer in Storage Explorer **Azure-accountinstellingen**. 
    
        ![Azure-accountinstellingen][1]
    
    1. In het linkerdeelvenster worden de Microsoft-accounts weergegeven waarop u bent aangemeld. Selecteer **Een account toevoegen** om verbinding te maken met een ander account en volg de dialoogvensters om aan te melden met een Microsoft-account dat is gekoppeld aan ten minste één actief Azure-abonnement.
    
        ![Een account toevoegen][2]
    
    1. Wanneer u bent aangemeld met een Microsoft-account, worden in het linkerdeelvenster de Azure-abonnementen weergegeven die aan dat account zijn gekoppeld. Selecteer de Azure-abonnementen waarmee u wilt werken en selecteer vervolgens **Toepassen**. (Als u **Alle abonnementen** selecteert, schakelt u de selectie van alle of geen van de vermelde Azure-abonnementen in.)
    
        ![Selecteer Azure-abonnementen][3]
    
    1. In het linkerdeelvenster worden de opslagaccounts weergegeven die aan de geselecteerde Azure-abonnementen zijn gekoppeld.
    
        ![Geselecteerde Azure-abonnementen][4]

1. Zoek het opslagaccount van het lab:

    1. Zoek in het linkerdeelvenster Van Storage Explorer het knooppunt voor het Azure-abonnement dat eigenaar is van het lab op en vouw het uit.
    
    1. Vouw onder het knooppunt van het abonnement **opslagaccounts**uit .

    1. Het knooppunt voor opslagaccount van het lab uitbreiden om knooppunten voor **Blob-containers,** **bestandsshares,** **wachtrijen**en tabellen weer te **geven**.
    
    1. Het knooppunt **Blobcontainers** uitvouwen.
    
    1. Selecteer de blobcontainer uploadt om de inhoud ervan weer te geven in het rechterdeelvenster.
        
        ![Map uploaden][5]

1. Upload het VHD-bestand met Storage Explorer:

    1. In het rechterdeelvenster van Storage Explorer ziet u een lijst met de blobs in de **blobcontainer uploads** van het opslagaccount van het lab. Selecteer **Uploaden** op de werkbalk van de blobeditor 
        
        ![Knop Uploaden][6]
    
    1. Selecteer bestanden **uploaden** in het vervolgkeuzemenu **Uploaden...**.
    
    1. Selecteer in het dialoogvenster **Bestanden uploaden** de ellips.
        
        ![Bestand selecteren][8]  

    1. Blader in het dialoogvenster **Bestanden selecteren om te uploaden** naar het gewenste VHD-bestand, selecteer het en selecteer **Openen**.
    
    1. Wanneer u terugkeert naar het dialoogvenster **Bestanden uploaden,** wijzigt u **blobtype** in **Paginablob**.
    
    1. Selecteer **Uploaden**.

        ![Bestand selecteren][9]  
    
    1. In **het deelvenster Activiteitslogboek** van de Opslagverkenner wordt de downloadstatus weergegeven (samen met koppelingen om de upload te annuleren). Het proces van het uploaden van een VHD-bestand kan lang zijn, afhankelijk van de grootte van het VHD-bestand en uw verbindingssnelheid. 

        ![Status upload-bestand][10]  

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste afbeelding maken in Azure DevTest Labs vanuit een VHD-bestand met behulp van de Azure-portal](devtest-lab-create-template.md)
- [Een aangepaste afbeelding maken in Azure DevTest Labs vanuit een VHD-bestand met PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
