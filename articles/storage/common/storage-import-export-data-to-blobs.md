---
title: Azure import/export gebruiken om gegevens over te dragen naar Azure-blobs | Microsoft Docs
description: Meer informatie over het maken van import-en export taken in Azure Portal om gegevens over te dragen van en naar Azure-blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/06/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: cab9d309d052acca493e112965c8477a325d8c88
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75944759"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>De Azure import/export-service gebruiken om gegevens te importeren in Azure Blob Storage

In dit artikel vindt u stapsgewijze instructies voor het gebruik van de Azure import/export-service om grote hoeveel heden gegevens veilig te importeren in Azure Blob-opslag. Als u gegevens wilt importeren in azure-blobs, moet u met de service versleutelde schijf stations met uw gegevens naar een Azure-Data Center verzenden.  

## <a name="prerequisites"></a>Vereisten

Voordat u een import taak maakt om gegevens over te dragen naar Azure Blob Storage, moet u de volgende lijst met vereisten voor deze service aandachtig door nemen en volt ooien. U moet het volgende doen:

- Een actief Azure-abonnement hebben dat kan worden gebruikt voor de import/export-service.
- U hebt ten minste één Azure Storage account met een opslag container. Zie de lijst met [ondersteunde opslag accounts en opslag typen voor de import/export-service](storage-import-export-requirements.md). 
    - Zie voor meer informatie over het maken van een nieuw opslagaccount [over het maken van een Storage-Account](storage-quickstart-create-account.md). 
    - Voor informatie over opslag container gaat u naar [een opslag container maken](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Voldoende aantal schijven van [ondersteunde typen](storage-import-export-requirements.md#supported-disks)hebben. 
- Een Windows-systeem met een [ondersteunde besturingssysteem versie](storage-import-export-requirements.md#supported-operating-systems)hebben. 
- Schakel BitLocker in op het Windows-systeem. Zie [BitLocker inschakelen voor meer informatie](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
- [Down load de WAImportExport-versie 1](https://www.microsoft.com/download/details.aspx?id=42659) op het Windows-systeem. Unzip de standaard map `waimportexportv1`. Bijvoorbeeld `C:\WaImportExportV1`.
- Een FedEx/DHL-account hebben. Als u een andere transporteur dan FedEx/DHL wilt gebruiken, neemt u contact op met Azure Data Box Operations-team op `adbops@microsoft.com`.  
    - Het account moet geldig zijn, moet een saldo hebben en moet de retour verzendings mogelijkheden hebben.
    - Genereer een tracking nummer voor de export taak.
    - Elke taak moet een afzonderlijk tracking nummer hebben. Meerdere taken met hetzelfde tracking nummer worden niet ondersteund.
    - Als u geen draaggolf account hebt, gaat u naar:
        - [Een FedEX-account maken](https://www.fedex.com/en-us/create-account.html)of 
        - [Maak een DHL-account](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Stap 1: de stations voorbereiden

Met deze stap wordt een logboek bestand gegenereerd. In het logboek bestand worden basis gegevens opgeslagen, zoals serie nummer van het station, de versleutelings sleutel en Details van het opslag account. 

Voer de volgende stappen uit om de stations voor te bereiden.

1.  Verbind uw schijf stations met het Windows-systeem via SATA-connectors.
1.  Maak één NTFS-volume op elk station. Wijs een stationsletter aan het volume toe. Gebruik mountpoints niet.
2.  BitLocker-versleuteling inschakelen op het NTFS-volume. Als u een Windows Server-systeem gebruikt, gebruikt u de instructies in [How to Enable BitLocker in Windows server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
3.  Gegevens kopiëren naar versleuteld volume. Gebruik slepen en neerzetten of Robocopy of een dergelijk Kopieer programma.
4.  Open een Power shell-of opdracht regel venster met Administrator bevoegdheden. Als u de map wilt wijzigen in de map ungezipte, voert u de volgende opdracht uit:
    
    `cd C:\WaImportExportV1`
5.  Als u de BitLocker-sleutel van het station wilt ophalen, voert u de volgende opdracht uit:
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  Voer de volgende opdracht uit om de schijf voor te bereiden. **Afhankelijk van de gegevens grootte kan dit enkele uren tot dagen duren.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite 
    ```
    Er wordt een logboek bestand gemaakt in dezelfde map waarin u het hulp programma hebt uitgevoerd. Er zijn twee andere bestanden gemaakt: een *. XML* -bestand (de map waarin u het hulp programma uitvoert) en een *Drive-manifest. XML* -bestand (de map waarin de gegevens zich bevinden).
    
    De gebruikte para meters worden in de volgende tabel beschreven:

    |Optie  |Beschrijving  |
    |---------|---------|
    |/j:     |De naam van het logboek bestand met de extensie. jrn. Er wordt een logboek bestand gegenereerd per station. Het is raadzaam om het serie nummer van de schijf als de naam van het logboek bestand te gebruiken.         |
    |/ID:     |De sessie-ID. Gebruik een uniek sessie nummer voor elk exemplaar van de opdracht.      |
    |/t:     |De stationsletter van de schijf die moet worden verzonden. Bijvoorbeeld station `D`.         |
    |/bk:     |De BitLocker-sleutel voor het station. Het numerieke wacht woord van de uitvoer van `manage-bde -protectors -get D:`      |
    |/srcdir:     |De stationsletter van de schijf die moet worden verzonden, gevolgd door `:\`. Bijvoorbeeld `D:\`.         |
    |/dstdir:     |De naam van de doel container in Azure Storage.         |
    |/blobtype:     |Met deze optie geeft u het type blobs op waarnaar u de gegevens wilt importeren. Voor blok-blobs is dit `BlockBlob` en voor pagina-blobs is dit `PageBlob`.         |
    |/skipwrite:     |De optie waarmee wordt aangegeven dat er geen nieuwe gegevens moeten worden gekopieerd en dat bestaande gegevens op de schijf moeten worden voor bereid.          |
    |/enablecontentmd5:     |Als u deze optie inschakelt, zorgt u ervoor dat MD5 wordt berekend en wordt ingesteld als `Content-md5` eigenschap op elke blob. Gebruik deze optie alleen als u het veld `Content-md5` wilt gebruiken nadat de gegevens zijn geüpload naar Azure. <br> Deze optie is niet van invloed op de gegevens integriteits controle (dit gebeurt standaard). De instelling neemt de benodigde tijd voor het uploaden van gegevens naar de Cloud toe.          |
7. Herhaal de vorige stap voor elke schijf die moet worden verzonden. Er wordt een logboek bestand met de gegeven naam gemaakt voor elke uitvoering van de opdracht regel.
    
    > [!IMPORTANT]
    > - Samen met het logboek bestand wordt ook een `<Journal file name>_DriveInfo_<Drive serial ID>.xml`-bestand gemaakt in de map waarin het hulp programma zich bevindt. Het. XML-bestand wordt gebruikt in plaats van het logboek bestand bij het maken van een taak als het logboek bestand te groot is. 

## <a name="step-2-create-an-import-job"></a>Stap 2: een import taak maken

Voer de volgende stappen uit om een import taak te maken in de Azure Portal.

1. Meld u aan bij https://portal.azure.com/.
2. Ga naar **alle services > opslag > import/export-taken**. 
    
    ![Ga naar import/export-taken](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klik op **import/export-taak maken**.

    ![Klik op import/export-taak maken](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. In de **basis beginselen**:

   - Selecteer **importeren in azure**.
   - Voer een beschrijvende naam in voor de import taak. Gebruik de naam om de voortgang van uw taken bij te houden.
       - De naam mag alleen kleine letters, cijfers en afbreek streepjes bevatten.
       - De naam moet beginnen met een letter en mag geen spaties bevatten.
   - Selecteer een abonnement.
   - Voer een resource groep in of Selecteer deze.  

     ![Import taak maken-stap 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. In **taak Details**:

    - Upload de schijf logboek bestanden die u hebt verkregen tijdens de stap voor bereiding van het station. Als `waimportexport.exe version1` is gebruikt, uploadt u één bestand voor elk station dat u hebt voor bereid. Als de grootte van het logboek bestand groter is dan 2 MB, kunt u de `<Journal file name>_DriveInfo_<Drive serial ID>.xml` gebruiken die ook is gemaakt met het logboek bestand. 
    - Selecteer het doel-opslag account waarin de gegevens worden opgeslagen. 
    - De locatie van de dropoff wordt automatisch ingevuld op basis van de regio van het geselecteerde opslag account.
   
   ![Import taak maken-stap 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. In **retour verzendings gegevens**:

   - Selecteer de transporteur in de vervolg keuzelijst. Als u een andere transporteur dan FedEx/DHL wilt gebruiken, kiest u een bestaande optie in de vervolg keuzelijst. Neem contact op met Azure Data Box Operations-team op `adbops@microsoft.com` met de informatie over de provider die u wilt gebruiken.
   - Voer een geldig account nummer van een transporteur in dat u hebt gemaakt met die transporteur. Micro soft gebruikt dit account om de schijven terug naar u te verzenden zodra uw import taak is voltooid. Als u geen account nummer hebt, maakt u een [FedEx](https://www.fedex.com/us/oadr/) -of [DHL](https://www.dhl.com/) -draaggolf account.
   - Geef een volledige en geldige naam op voor de contact persoon, telefoon, e-mail, adres, plaats, post code, provincie en land/regio. 
        
       > [!TIP] 
       > In plaats van een e-mail adres voor één gebruiker op te geven, moet u een groeps-e-mail opgeven. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder deze verlaat.

     ![Import taak maken-stap 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. In de **samen vatting**:

   - Bekijk de taak gegevens die in de samen vatting worden weer gegeven. Noteer de naam van de taak en het verzend adres van Azure Data Center om schijven terug te sturen naar Azure. Deze informatie wordt later op het verzend label gebruikt.
   - Klik op **OK** om de import taak te maken.

     ![Import taak maken-stap 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>Stap 3: de stations verzenden 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Stap 4: de taak bijwerken met tracerings informatie

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Stap 5: gegevens uploaden naar Azure controleren

De taak bijhouden tot voltooiing. Nadat de taak is voltooid, controleert u of uw gegevens zijn geüpload naar Azure. Verwijder de on-premises gegevens pas nadat u hebt gecontroleerd of de upload is geslaagd.

## <a name="next-steps"></a>Volgende stappen

* [De taak en de status van het station weer geven](storage-import-export-view-drive-status.md)
* [Vereisten voor importeren/exporteren controleren](storage-import-export-requirements.md)


