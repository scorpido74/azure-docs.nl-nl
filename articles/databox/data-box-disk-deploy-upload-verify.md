---
title: Zelf studie voor het controleren van het uploaden van gegevens van Azure Data Box Disk naar het opslag account | Microsoft Docs
description: Gebruik deze zelf studie om te leren hoe u kunt controleren of de gegevens die u hebt geüpload van uw Azure Data Box Disk naar een Azure-opslag account.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: a4d814ab5b1f26a6a2b871a850fd5e3153e256f5
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240280"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Zelfstudie: Gegevens uploaden van Azure Data Box Disk controleren

Dit is de laatste zelfstudie van de serie Azure Data Box Disk implementeren. In deze zelf studie leert u het volgende:

> [!div class="checklist"]
> * De gegevensupload naar Azure controleren
> * Gegevens verwijderen van de Data Box Disk

## <a name="prerequisites"></a>Vereisten

Voltooi voordat u begint de [Zelfstudie: Retour Azure Data Box Disk](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

Nadat de schijven door de vervoerder zijn opgehaald, verandert de orderstatus in de portal in **Opgehaald**. Er wordt ook een tracerings-id weergegeven.

![Schijven opgehaald](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Wanneer Microsoft de schijf heeft ontvangen en gescand, wordt de taakstatus bijgewerkt naar **Ontvangen**. 

![Schijven ontvangen](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

De gegevens worden automatisch gekopieerd zodra de schijven worden verbonden met een server in het Azure-datacenter. Afhankelijk van de gegevensgrootte kan de kopieerbewerking enkele uren tot enkele dagen duren. U kunt de voortgang van de kopieertaak bewaken via de portal.

Nadat de kopie is voltooid, wordt de orderstatus bijgewerkt naar **Voltooid**.

![Gegevens kopiëren voltooid](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Zie [problemen met upload fouten oplossen](data-box-disk-troubleshoot-upload.md)als de Kopieer bewerking is voltooid.

Controleer of uw gegevens zich in de opslagaccount(s) bevinden voordat u deze uit de bron verwijdert. Uw gegevens kunnen de volgende zijn:

- Uw Azure Storage-account (s). Als u de gegevens naar Data Box kopieert, worden de gegevens naar een van de volgende paden in uw Azure Storage-account geüpload, afhankelijk van het gegevenstype.

  - Voor blok-blobs en pagina-blobs: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Voor Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    U kunt ook naar uw Azure-opslagaccount in de Azure-portal gaan en van daaruit navigeren.

- De resource groep (en) van de beheerde schijf. Bij het maken van beheerde schijven worden de Vhd's geüpload als pagina-blobs en vervolgens geconverteerd naar Managed disks. De beheerde schijven worden gekoppeld aan de resource groepen die zijn opgegeven op het moment dat de order wordt gemaakt.

  - Als uw kopie naar beheerde schijven in Azure is geslaagd, gaat u naar de details van de **order** in het Azure Portal en noteert u de resource groep die is opgegeven voor beheerde schijven.

      ![Bestellingsgegevens bekijken](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Ga naar de genoteerde resource groep en zoek uw beheerde schijven.

      ![Resource groep voor beheerde schijven](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Als u een VHDX of een dynamische/differentiërende VHD hebt gekopieerd, wordt de VHDX/VHD geüpload naar het opslag account voor staging als een blok-blob. Ga naar uw staging- **opslag account > blobs** en selecteer vervolgens de juiste container-StandardSSD, StandardHDD of PremiumSSD. De VHDX/Vhd's moeten worden weer gegeven als blok-blobs in uw staging Storage-account.
  
::: zone-end

::: zone target="chromeless"

# <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

Nadat de gegevens zijn geüpload naar Azure, controleert u of de gegevens zich in de opslag account (s) bevindt voordat u deze verwijdert uit de bron. Uw gegevens kunnen de volgende zijn:

- Uw Azure Storage-account (s). Als u de gegevens naar Data Box kopieert, worden de gegevens naar een van de volgende paden in uw Azure Storage-account geüpload, afhankelijk van het gegevenstype.

    - **Voor blok-blobs en pagina-blobs**: https://< storage_account_name >. blob. core. Windows.<containername>net//files/a.txt

    - **Voor Azure files**: https://< storage_account_name >. file. core. Windows. net/<sharename>/files/a.txt

- De resource groep (en) van de beheerde schijf. Bij het maken van beheerde schijven worden de Vhd's geüpload als pagina-blobs en vervolgens geconverteerd naar Managed disks. De beheerde schijven worden gekoppeld aan de resource groepen die zijn opgegeven op het moment dat de order wordt gemaakt.

::: zone-end

Voer de volgende stappen uit om te controleren of de gegevens zijn geüpload naar Azure:

1. Ga naar het opslagaccount dat is gekoppeld aan uw schijforder.
2. Ga naar **Blob-service > Blobs verkennen**. De lijst met containers wordt weergegeven. Er worden containers gemaakt in uw opslagaccount met dezelfde naam als die van de submappen die u hebt gemaakt onder de mappen *BlockBlob* en *PageBlob*.
    Als de mapnamen niet de naamgevingsregels van Azure volgen, mislukt het uploaden van gegevens naar Azure.

3. Als u wilt controleren of de volledige gegevensset is geladen, kunt u Microsoft Azure Storage Explorer gebruiken. Koppel het opslag account dat overeenkomt met de Data Box Disk order en bekijk vervolgens de lijst met Blob-containers. Selecteer een container, klikt u op **... Meer** en klik vervolgens op **Mapstatistieken**. De statistieken voor die map worden dan weergegeven in het deelvenster **Activiteiten**, waaronder het aantal blobs en de totale grootte van de blobs. De totale blobgrootte in bytes moet overeenkomen met de grootte van de gegevensset.

    ![Mapstatistieken in Storage Explorer](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Gegevens verwijderen van de Data Box Disk

Nadat de kopie is voltooid en u hebt gecontroleerd of de gegevens zich in het Azure-opslag account bevinden, worden schijven veilig gewist volgens de NIST-standaard.

::: zone target="docs"

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn verschillende onderwerpen besproken over de Azure Data Box-schijf, zoals:

> [!div class="checklist"]
> * De gegevensupload naar Azure controleren
> * Gegevens verwijderen van de Data Box Disk


Ga verder met de volgende handleiding voor meer informatie over het beheren van Data Box Disk via Azure Portal.

> [!div class="nextstepaction"]
> [Azure Portal gebruiken om Azure Data Box Disk te beheren](./data-box-portal-ui-admin.md)

::: zone-end




