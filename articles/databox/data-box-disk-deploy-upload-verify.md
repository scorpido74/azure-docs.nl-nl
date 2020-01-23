---
title: Zelfstudie voor het controleren van de gegevensupload van Azure Data Box Disk naar het opslagaccount | Microsoft Docs
description: Gebruik deze zelfstudie om te leren hoe u de gegevensupload van uw Azure Data Box Disk naar een Azure-opslagaccount controleert.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/04/2019
ms.author: alkohli
ms.openlocfilehash: e2851f7087151b09b5f859b7142aa28e5b18a4e5
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514082"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Zelfstudie: Gegevens uploaden vanuit Azure Data Box Disk controleren

Dit is de laatste zelfstudie van de serie Azure Data Box Disk implementeren. In deze zelfstudie leert u:

> [!div class="checklist"]
> * De gegevensupload naar Azure controleren
> * Gegevens verwijderen van de Data Box Disk

## <a name="prerequisites"></a>Vereisten

Voltooi voordat u begint de [Zelfstudie: Azure Data Box Disk retourneren](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

Nadat de schijven door de vervoerder zijn opgehaald, verandert de orderstatus in de portal in **Opgehaald**. Er wordt ook een tracerings-id weergegeven.

![Schijven opgehaald](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Wanneer Microsoft de schijf heeft ontvangen en gescand, wordt de taakstatus bijgewerkt naar **Ontvangen**. 

![Schijven ontvangen](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

De gegevens worden automatisch gekopieerd zodra de schijven worden verbonden met een server in het Azure-datacenter. Afhankelijk van de gegevensgrootte kan de kopieerbewerking enkele uren tot enkele dagen duren. U kunt de voortgang van de kopieertaak bewaken via de portal.

Nadat de kopie is voltooid, wordt de orderstatus bijgewerkt naar **Voltooid**.

![Gegevens kopiëren voltooid](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Zie [problemen met uploaden oplossen](data-box-disk-troubleshoot-upload.md) als het kopiëren met fouten wordt voltooid.

Controleer of uw gegevens zich in de opslagaccount(s) bevinden voordat u deze uit de bron verwijdert. Uw gegevens kunnen zich bevinden in:

- Uw Azure Storage-account(s). Als u de gegevens naar Data Box kopieert, worden de gegevens naar een van de volgende paden in uw Azure Storage-account geüpload, afhankelijk van het gegevenstype.

  - Voor blok-blobs en pagina-blobs: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Voor Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    U kunt ook naar uw Azure-opslagaccount in de Azure-portal gaan en van daaruit navigeren.

- Uw resourcegroep(en) op beheerde schijf. Bij het maken van beheerde schijven worden de VHD's geüpload als pagina-blobs en vervolgens naar beheerde schijven geconverteerd. De beheerde schijven worden gekoppeld aan de resourcegroepen die zijn opgegeven op het moment dat de order werd gemaakt.

  - Als het kopiëren naar beheerde schijven in Azure is geslaagd, gaat u naar **Orderdetails** in de Azure-portal en noteert u de resourcegroep die is opgegeven voor beheerde schijven.

      ![Orderdetails bekijken](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Ga naar de genoteerde resourcegroep en zoek uw beheerde schijven.

      ![Resourcegroep voor beheerde schijven](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Als u een VHDX of een dynamische/differentiërende VHD hebt gekopieerd, wordt de VHDX/VHD als een blok-blob naar het faseringsopslagaccount geüpload. Ga naar uw **Faseringsopslagaccount > Blobs** en selecteer de juiste container: StandardSSD, StandardHDD of PremiumSSD. De VHDX's/VHD's moeten als blok-blobs in uw faseringsopslagaccount worden weergegeven.
  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>De gegevensupload naar Azure controleren

Nadat de gegevens naar Azure zijn geüpload, controleert u of uw gegevens zich in de opslagaccount(s) bevinden voordat u deze uit de bron verwijdert. Uw gegevens kunnen zich bevinden in:

- Uw Azure Storage-account(s). Als u de gegevens naar Data Box kopieert, worden de gegevens naar een van de volgende paden in uw Azure Storage-account geüpload, afhankelijk van het gegevenstype.

    - **Voor blok-blobs en pagina-blobs**: https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt

    - **Voor Azure Files**: https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt

- Uw resourcegroep(en) op beheerde schijf. Bij het maken van beheerde schijven worden de VHD's geüpload als pagina-blobs en vervolgens naar beheerde schijven geconverteerd. De beheerde schijven worden gekoppeld aan de resourcegroepen die zijn opgegeven op het moment dat de order werd gemaakt.

::: zone-end

Voer de volgende stappen uit om te controleren of de gegevens naar Azure zijn geüpload:

1. Ga naar het opslagaccount dat is gekoppeld aan uw schijforder.
2. Ga naar **Blob-service > Blobs verkennen**. De lijst met containers wordt weergegeven. Er worden containers gemaakt in uw opslagaccount met dezelfde naam als die van de submappen die u hebt gemaakt onder de mappen *BlockBlob* en *PageBlob*.
    Als de mapnamen niet de naamgevingsregels van Azure volgen, mislukt het uploaden van gegevens naar Azure.

3. Als u wilt controleren of de volledige gegevensset is geladen, kunt u Microsoft Azure Storage Explorer gebruiken. Koppel het opslagaccount dat overeenkomt met de order voor de Data Box Disk en bekijk de lijst met blobcontainers. Selecteer een container, klikt u op **... Meer** en klik vervolgens op **Mapstatistieken**. De statistieken voor die map worden dan weergegeven in het deelvenster **Activiteiten**, waaronder het aantal blobs en de totale grootte van de blobs. De totale blobgrootte in bytes moet overeenkomen met de grootte van de gegevensset.

    ![Mapstatistieken in Storage Explorer](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Gegevens verwijderen van de Data Box Disk

Nadat de kopie is voltooid en u de gegevens in het Azure-opslagaccount hebt gecontroleerd, worden de schijven veilig gewist volgens de NIST-standaard.

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




