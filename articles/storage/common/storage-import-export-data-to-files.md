---
title: Azure import/export gebruiken om gegevens over te dragen naar Azure Files | Microsoft Docs
description: Meer informatie over het maken van import taken in de Azure Portal om gegevens over te dragen naar Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: cbff2cbed37a4cff91116596f1c20dc3d170cae2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85513485"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Azure Import/Export-service gebruiken om gegevens te importeren naar Azure Files

In dit artikel vindt u stapsgewijze instructies voor het gebruik van de Azure import/export-service om grote hoeveel heden gegevens veilig te importeren in Azure Files. Als u gegevens wilt importeren, moet u ondersteunde schijf stations met uw gegevens verzenden naar een Azure-Data Center.  

De import/export-service ondersteunt alleen het importeren van Azure Files naar Azure Storage. Het exporteren van Azure Files wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voordat u een import taak maakt om gegevens over te dragen naar Azure Files, moet u de volgende lijst met vereisten zorgvuldig door nemen en volt ooien. U moet het volgende doen:

- Een actief Azure-abonnement hebben om te gebruiken met de import/export-service.
- Ten minste één Azure Storage account hebben. Zie de lijst met [ondersteunde opslag accounts en opslag typen voor de import/export-service](storage-import-export-requirements.md). Zie [een opslag account maken](storage-account-create.md)voor meer informatie over het maken van een nieuw opslag account.
- Voldoende aantal schijven van [ondersteunde typen](storage-import-export-requirements.md#supported-disks)hebben.
- Een Windows-systeem met een [ondersteunde besturingssysteem versie](storage-import-export-requirements.md#supported-operating-systems)hebben.
- [Down load de WAImportExport-versie 2](https://aka.ms/waiev2) op het Windows-systeem. Unzip naar de standaardmap `waimportexport` . Bijvoorbeeld `C:\WaImportExport`.
- Een FedEx/DHL-account hebben. Als u een andere transporteur dan FedEx/DHL wilt gebruiken, neemt u contact op met Azure Data Box Operations-team op `adbops@microsoft.com` .  
    - Het account moet geldig zijn, moet een saldo hebben en moet de retour verzendings mogelijkheden hebben.
    - Genereer een tracking nummer voor de export taak.
    - Elke taak moet een afzonderlijk traceringsnummer hebben. Meerdere taken met hetzelfde traceringsnummer worden niet ondersteund.
    - Als u geen draaggolf account hebt, gaat u naar:
        - [Een FedEX-account maken](https://www.fedex.com/en-us/create-account.html)of
        - [Maak een DHL-account](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>Stap 1: de stations voorbereiden

Met deze stap wordt een logboek bestand gegenereerd. In het logboek bestand worden basis gegevens opgeslagen, zoals serie nummer van het station, de versleutelings sleutel en Details van het opslag account.

Voer de volgende stappen uit om de stations voor te bereiden.

1. Verbind onze schijf stations met het Windows-systeem via SATA-connectors.
2. Maak één NTFS-volume op elk station. Wijs een stationsletter aan het volume toe. Gebruik mountpoints niet.
3. Wijzig het *dataset.csv* -bestand in de hoofdmap waar het hulp programma zich bevindt. Afhankelijk van of u een bestand of map of beide wilt importeren, voegt u in het *dataset.csv* -bestand vermeldingen toe die vergelijkbaar zijn met de volgende voor beelden.  

   - **Een bestand importeren**: in het volgende voor beeld bevindt de te kopiëren gegevens zich in het station F:. Uw bestands *MyFile1.txt* wordt gekopieerd naar de hoofdmap van de *MyAzureFileshare1*. Als de *MyAzureFileshare1* niet bestaat, wordt deze in het Azure Storage-account gemaakt. De mapstructuur wordt behouden.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Een map importeren**: alle bestanden en mappen onder *MyFolder2* worden recursief gekopieerd naar de bestands share. De mapstructuur wordt behouden.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Er kunnen meerdere vermeldingen worden gemaakt in hetzelfde bestand dat overeenkomt met de mappen of bestanden die worden geïmporteerd.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Meer informatie over [het voorbereiden van het CSV-bestand van de gegevensset](storage-import-export-tool-preparing-hard-drives-import.md).


4. Wijzig het *driveset.csv* -bestand in de hoofdmap waar het hulp programma zich bevindt. Voeg in het *driveset.csv* -bestand vermeldingen toe die vergelijkbaar zijn met de volgende voor beelden. Het stationset-bestand bevat de lijst met schijven en bijbehorende stationsletters, zodat het hulp programma de lijst met schijven die moeten worden voor bereid, correct kan selecteren.

    In dit voor beeld wordt ervan uitgegaan dat er twee schijven zijn gekoppeld en basis-NTFS-volumes G:\ en H:\ worden gemaakt. H:\is is niet versleuteld terwijl G: al is versleuteld. Het hulp programma formatteert en versleutelt de schijf die als host fungeert voor H:\ alleen (en niet G: \) .

   - **Voor een niet-versleutelde schijf**: Geef *versleutelen* op om BitLocker-versleuteling op de schijf in te scha kelen.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Voor een schijf die al is versleuteld**: Geef *AlreadyEncrypted* op en voer de BitLocker-sleutel in.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Er kunnen meerdere vermeldingen worden gemaakt in hetzelfde bestand dat overeenkomt met meerdere stations. Meer informatie over [het voorbereiden van het CSV-bestand van het station](storage-import-export-tool-preparing-hard-drives-import.md).

5. Gebruik de `PrepImport` optie om gegevens naar het schijf station te kopiëren en voor te bereiden. Voer de volgende opdracht uit voor de eerste Kopieer sessie om directory's en/of bestanden te kopiëren met een nieuwe Kopieer sessie:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Hieronder ziet u een voor beeld van een import.

       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```

6. Een logboek bestand met de naam die u hebt ingevoerd met `/j:` de para meter, wordt gemaakt voor elke uitvoering van de opdracht regel. Elke schijf die u voorbereidt, heeft een logboek bestand dat moet worden geüpload wanneer u de import taak maakt. Stations zonder logboek bestanden worden niet verwerkt.

    > [!IMPORTANT]
    > - Wijzig de gegevens op de schijf stations of het logboek bestand niet nadat de schijf voorbereiding is voltooid.

Voor extra voor beelden gaat u naar [voor beelden van logboek bestanden](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Stap 2: een import taak maken

Voer de volgende stappen uit om een import taak te maken in de Azure Portal.
1. Meld u aan bij https://portal.azure.com/ .
2. Ga naar **alle services > opslag > import/export-taken**.

    ![Ga naar importeren/exporteren](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klik op **import/export-taak maken**.

    ![Klik op import/export-taak](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. In de **basis beginselen**:

    - Selecteer **importeren in azure**.
    - Voer een beschrijvende naam in voor de import taak. Gebruik deze naam om uw taken bij te houden terwijl ze worden uitgevoerd en zodra ze zijn voltooid.
        -  Deze naam mag alleen kleine letters, cijfers, afbreek streepjes en onderstrepings tekens bevatten.
        -  De naam moet beginnen met een letter en mag geen spaties bevatten.
    - Selecteer een abonnement.
    - Selecteer een resourcegroep.

        ![Import taak maken-stap 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. In **taak Details**:

    - Upload de logboek bestanden die u hebt gemaakt tijdens de vorige [stap 1: bereid de stations](#step-1-prepare-the-drives)voor.
    - Selecteer het opslag account waarin de gegevens worden geïmporteerd.
    - De locatie van de dropoff wordt automatisch ingevuld op basis van de regio van het geselecteerde opslag account.

       ![Import taak maken-stap 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. In **retour verzendings gegevens**:

    - Selecteer de transporteur in de vervolg keuzelijst. Als u een andere transporteur dan FedEx/DHL wilt gebruiken, kiest u een bestaande optie in de vervolg keuzelijst. Neem contact op met Azure Data Box Operations-team `adbops@microsoft.com` met de informatie over de provider die u wilt gebruiken.
    - Voer een geldig account nummer van een transporteur in dat u hebt gemaakt met die transporteur. Micro soft gebruikt dit account om de schijven terug naar u te verzenden zodra uw import taak is voltooid.
    - Geef een volledige en geldige naam op voor de contact persoon, telefoon, e-mail, adres, plaats, post code, provincie en land/regio.

        > [!TIP]
        > In plaats van een e-mail adres voor één gebruiker op te geven, moet u een groeps-e-mail opgeven. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder deze verlaat.

       ![Import taak maken-stap 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. In de **samen vatting**:

    - Geef het Azure Data Center-verzend adres voor de verzend schijven terug naar Azure. Zorg ervoor dat de naam en het volledige adres van de taak worden vermeld op het verzend label.
    - Klik op **OK** om het maken van de import taak te volt ooien.

        ![Import taak maken-stap 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Stap 3: de stations naar het Azure-Data Center verzenden

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Stap 4: de taak bijwerken met tracerings informatie

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Stap 5: gegevens uploaden naar Azure controleren

De taak bijhouden tot voltooiing. Nadat de taak is voltooid, controleert u of uw gegevens zijn geüpload naar Azure. Verwijder de on-premises gegevens pas nadat u hebt gecontroleerd of de upload is geslaagd.

## <a name="samples-for-journal-files"></a>Voor beelden voor logboek bestanden

Als u **meer stations wilt toevoegen**, maakt u een nieuw stationset-bestand en voert u de volgende opdracht uit.

Voor volgende Kopieer sessies naar de verschillende schijf stations dan opgegeven in *InitialDriveset. CSV* -bestand, geeft u een nieuw *CSV* -bestand van stationset op en geeft u het op als waarde voor de para meter `AdditionalDriveSet` . Gebruik dezelfde naam voor het **logboek bestand** en geef een **nieuwe sessie-id**op. De indeling van het CSV-bestand van AdditionalDriveset is gelijk aan de InitialDriveSet-indeling.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Hieronder ziet u een voor beeld van een import.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Als u extra gegevens wilt toevoegen aan dezelfde schijfset, gebruikt u de opdracht PrepImport voor de volgende Kopieer sessies om extra bestanden/mappen te kopiëren.

Voor volgende Kopieer sessies naar dezelfde harde schijven die zijn opgegeven in *InitialDriveset.csv* bestand, geeft u dezelfde naam op voor het **logboek bestand** en geeft u een **nieuwe sessie-id**op. het is niet nodig om de sleutel van het opslag account op te geven.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Hieronder ziet u een voor beeld van een import.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Volgende stappen

* [De taak en de status van het station weer geven](storage-import-export-view-drive-status.md)
* [Vereisten voor importeren/exporteren controleren](storage-import-export-requirements.md)
