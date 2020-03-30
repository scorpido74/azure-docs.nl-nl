---
title: Azure Importeren/exporteren gebruiken om gegevens over te dragen naar Azure-bestanden | Microsoft Documenten
description: Meer informatie over het maken van importtaken in de Azure-portal om gegevens over te zetten naar Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 201d0c0a545c5ba7ae1bb0b5e119f7acb1ae362f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268299"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Azure Import/Export-service gebruiken om gegevens te importeren naar Azure Files

In dit artikel vindt u stapsgewijze instructies over het gebruik van de Azure Import/Export-service om grote hoeveelheden gegevens veilig te importeren in Azure-bestanden. Als u gegevens wilt importeren, moet u voor de service ondersteunde schijven met uw gegevens verzenden naar een Azure-datacenter.  

De import/exportservice ondersteunt alleen het importeren van Azure-bestanden in Azure Storage. Het exporteren van Azure-bestanden wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voordat u een importtaak maakt om gegevens over te dragen naar Azure-bestanden, controleert u zorgvuldig en voltooit u de volgende lijst met vereisten. U moet:

- Heb een actief Azure-abonnement om te gebruiken met import/exportservice.
- Hebben ten minste één Azure Storage-account. Zie de lijst [met ondersteunde opslagaccounts en opslagtypen voor import/exportservice](storage-import-export-requirements.md). Zie [Een opslagaccount maken](storage-account-create.md)voor informatie over het maken van een nieuw opslagaccount .
- Hebben voldoende aantal schijven van [ondersteunde typen](storage-import-export-requirements.md#supported-disks).
- Een Windows-systeem met een [ondersteunde os-versie](storage-import-export-requirements.md#supported-operating-systems).
- [Download de WAImportExport versie 2](https://aka.ms/waiev2) op het Windows-systeem. Rits uit de `waimportexport`standaardmap . Bijvoorbeeld `C:\WaImportExport`.
- Heb een FedEx/DHL account. Als u een andere provider dan FedEx/DHL wilt gebruiken, `adbops@microsoft.com`neemt u contact op met het Azure Data Box Operations-team op .  
    - De rekening moet geldig zijn, moet saldo hebben en moet retourverzendmogelijkheden hebben.
    - Een volgnummer genereren voor de exporttaak.
    - Elke taak moet een apart volgnummer hebben. Meerdere taken met hetzelfde trackingnummer worden niet ondersteund.
    - Als je geen carrieraccount hebt, ga je naar:
        - [Een FedEX-account aanmaken,](https://www.fedex.com/en-us/create-account.html)of
        - [Maak een DHL-account](http://www.dhl-usa.com/en/express/shipping/open_account.html)aan .



## <a name="step-1-prepare-the-drives"></a>Stap 1: De stations voorbereiden

Met deze stap wordt een journaalbestand gegenereerd. Het logboekbestand slaat basisgegevens op, zoals het serienummer van de stations, de versleutelingssleutel en de gegevens van het opslagaccount.

Voer de volgende stappen uit om de stations voor te bereiden.

1. Sluit onze schijven aan op het Windows-systeem via SATA-connectors.
2. Maak één NTFS-volume op elk station. Wijs een stationsletter toe aan het volume. Gebruik geen bevestigingspunten.
3. Wijzig het *bestand dataset.csv* in de hoofdmap waar het gereedschap zich bevindt. Afhankelijk van of u een bestand of map of beide wilt importeren, voegt u vermeldingen toe in het *bestand dataset.csv,* vergelijkbaar met de volgende voorbeelden.  

   - **Een bestand importeren:** In het volgende voorbeeld bevinden de gegevens die u wilt kopiëren zich in het F:station. Uw bestand *MyFile1.txt* wordt gekopieerd naar de hoofdmap van het *MyAzureFileshare1*. Als de *MyAzureFileshare1* niet bestaat, wordt deze gemaakt in het Azure Storage-account. De mapstructuur blijft behouden.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Een map importeren:** alle bestanden en mappen onder *MyFolder2* worden opnieuw gekopieerd naar fileshare. De mapstructuur blijft behouden.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Er kunnen meerdere vermeldingen worden gemaakt in hetzelfde bestand dat overeenkomt met mappen of bestanden die worden geïmporteerd.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Meer informatie over [het voorbereiden van het CSV-bestand van de gegevensset](storage-import-export-tool-preparing-hard-drives-import.md).


4. Wijzig het bestand *driveset.csv* in de hoofdmap waar het gereedschap zich bevindt. Voeg vermeldingen toe in het bestand *driveset.csv,* vergelijkbaar met de volgende voorbeelden. Het driveset-bestand heeft de lijst met schijven en bijbehorende stationsletters, zodat het hulpprogramma de lijst met schijven die moet worden voorbereid correct kan kiezen.

    In dit voorbeeld wordt ervan uitgegaan dat er twee schijven zijn gekoppeld en dat basisNTFS-volumes G:\ en H:\ worden gemaakt. H:\is niet versleuteld terwijl G: is al versleuteld. Het hulpprogramma formateert en versleutelt de schijf die H:\ host alleen (en niet\)G: .

   - **Voor een schijf die niet is versleuteld:** Geef *Versleutelen* op om BitLocker-versleuteling op de schijf in te schakelen.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Voor een schijf die al is versleuteld:** Geef *AlreadyEncrypted op* en lever de BitLocker-sleutel.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Meerdere vermeldingen kunnen worden gemaakt in hetzelfde bestand dat overeenkomt met meerdere stations. Meer informatie over [het voorbereiden van het CSV-bestand van de driveset](storage-import-export-tool-preparing-hard-drives-import.md).

5. Gebruik `PrepImport` de optie om gegevens naar het schijfstation te kopiëren en voor te bereiden. Voer de volgende opdracht uit voor de eerste copy sessie om mappen en/of bestanden te kopiëren met een nieuwe kopieersessie:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Hieronder ziet u een importvoorbeeld.

       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```

6. Voor elke run van `/j:` de opdrachtregel wordt een journaalbestand met de naam die u bij de parameter hebt opgegeven. Elk station dat u voorbereidt, heeft een journaalbestand dat moet worden geüpload wanneer u de importtaak maakt. Stations zonder dagboekbestanden worden niet verwerkt.

    > [!IMPORTANT]
    > - Wijzig de gegevens op de schijven of het logboekbestand niet nadat u de schijfvoorbereiding hebt voltooid.

Ga voor aanvullende voorbeelden naar [Voorbeelden voor dagboekbestanden.](#samples-for-journal-files)

## <a name="step-2-create-an-import-job"></a>Stap 2: Een importtaak maken

Voer de volgende stappen uit om een importtaak te maken in de Azure-portal.
1. Log hier https://portal.azure.com/in
2. Ga naar **Alle services >-> Import/export-taken**.

    ![Ga naar Importeren/exporteren](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klik **op Import/exporttaak maken**.

    ![Klik op Taak importeren/exporteren](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. In **de basis:**

    - Selecteer **Importeren in Azure**.
    - Voer een beschrijvende naam in voor de importtaak. Gebruik deze naam om uw taken bij te houden terwijl ze aan de gang zijn en zodra ze zijn voltooid.
        -  Deze naam mag alleen kleine letters, cijfers, koppeltekens en underscores bevatten.
        -  De naam moet beginnen met een letter en mag geen spaties bevatten.
    - Selecteer een abonnement.
    - Selecteer een resourcegroep.

        ![Importtaak maken - Stap 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. In **Job details:**

    - Upload de logboekbestanden die u hebt gemaakt tijdens de voorafgaande [stap 1: De stations voorbereiden.](#step-1-prepare-the-drives)
    - Selecteer het opslagaccount waarde gegevens worden geïmporteerd.
    - De locatie voor het afzetten wordt automatisch ingevuld op basis van de regio van het geselecteerde opslagaccount.

       ![Importtaak maken - Stap 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. In **Ruil verzendkosten info:**

    - Selecteer de provider in de vervolgkeuzelijst. Als u een andere vervoerder dan FedEx/DHL wilt gebruiken, kiest u een bestaande optie in de vervolgkeuzelijst. Neem contact op met `adbops@microsoft.com` het Azure Data Box Operations-team met de informatie over de provider die u wilt gebruiken.
    - Voer een geldig provideraccountnummer in dat u bij die provider hebt gemaakt. Microsoft gebruikt dit account om de stations naar u terug te sturen zodra uw importtaak is voltooid.
    - Geef een volledige en geldige naam van de contactpersoon, telefoon, e-mail, adres, stad, zip, staat/provincie en land/regio.

        > [!TIP]
        > Geef in plaats van een e-mailadres op te geven voor één gebruiker, een groepse-mail op. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder vertrekt.

       ![Importtaak maken - Stap 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. In de **samenvatting**:

    - Geef het azure-datacenterverzendadres op voor het verzenden van schijven terug naar Azure. Zorg ervoor dat de naam van de taak en het volledige adres worden vermeld op het verzendlabel.
    - Klik op **OK** om het maken van importbanen te voltooien.

        ![Importtaak maken - Stap 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Stap 3: De schijven verzenden naar het Azure-datacenter

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Stap 4: De taak bijwerken met trackinginformatie

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Stap 5: Gegevens uploaden naar Azure verifiëren

Volg de taak tot voltooiing. Zodra de taak is voltooid, controleert u of uw gegevens zijn geüpload naar Azure. Verwijder de on-premises gegevens pas nadat u hebt geverifieerd dat de upload is gelukt.

## <a name="samples-for-journal-files"></a>Voorbeelden voor dagboekbestanden

Als **u meer stations wilt toevoegen,** maakt u een nieuw driveset-bestand en voert u de opdracht uit zoals hieronder.

Voor volgende kopieersessies naar de verschillende schijven dan opgegeven in *initialDriveset .csv-bestand,* geeft u een `AdditionalDriveSet`nieuw csv-bestand met driveset *op* en geeft u deze als waarde voor de parameter . Gebruik **dezelfde bestandsnaam van het logboek** en geef een **nieuwe sessie-id op**. De indeling van het CSV-bestand met extraDriveset is hetzelfde als de initialDriveSet-indeling.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Hieronder ziet u een importvoorbeeld.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Als u extra gegevens aan dezelfde driveset wilt toevoegen, gebruikt u de opdracht PrepImport voor volgende kopieersessies om extra bestanden/map te kopiëren.

Voor volgende kopieersessies naar dezelfde harde schijven die zijn opgegeven in het bestand *InitialDriveset.csv,* geeft u **dezelfde bestandsnaam op** en geeft u een **nieuwe sessie-id**op ; het is niet nodig om de opslagaccountsleutel te verstrekken.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Hieronder ziet u een importvoorbeeld.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Volgende stappen

* [De taak- en rijstatus weergeven](storage-import-export-view-drive-status.md)
* [Import-/exportvereisten controleren](storage-import-export-requirements.md)
