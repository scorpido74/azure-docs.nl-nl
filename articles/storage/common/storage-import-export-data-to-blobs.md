---
title: Azure Importeren/exporteren gebruiken om gegevens over te dragen aan Azure Blobs | Microsoft Documenten
description: Meer informatie over het maken van import- en exporttaken in azure-portal om gegevens van en naar Azure Blobs over te dragen.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 570c663861361a19190f6fb5d608b6aa029a0885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282491"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>De Azure Import/Export-service gebruiken om gegevens te importeren naar Azure Blob Storage

In dit artikel vindt u stapsgewijze instructies over het gebruik van de Azure Import/Export-service om grote hoeveelheden gegevens veilig te importeren in Azure Blob-opslag. Als u gegevens wilt importeren in Azure Blobs, moet u met de service versleutelde schijven met uw gegevens verzenden naar een Azure-datacenter.  

## <a name="prerequisites"></a>Vereisten

Voordat u een importtaak maakt om gegevens over te dragen naar Azure Blob Storage, controleert u zorgvuldig en voltooit u de volgende lijst met vereisten voor deze service.
U moet:

* Zorg voor een actief Azure-abonnement dat kan worden gebruikt voor de import/exportservice.
* Hebben ten minste één Azure Storage-account met een opslagcontainer. Zie de lijst [met ondersteunde opslagaccounts en opslagtypen voor import/exportservice](storage-import-export-requirements.md).
  * Zie [Een opslagaccount maken](storage-account-create.md)voor informatie over het maken van een nieuw opslagaccount .
  * Ga voor informatie over de opslagcontainer naar [Een opslagcontainer maken.](../blobs/storage-quickstart-blobs-portal.md#create-a-container)
* Hebben voldoende aantal schijven van [ondersteunde typen](storage-import-export-requirements.md#supported-disks).
* Een Windows-systeem met een [ondersteunde os-versie](storage-import-export-requirements.md#supported-operating-systems).
* BitLocker inschakelen op het Windows-systeem. Zie [Hoe u BitLocker inschakelt](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Download de nieuwste WAImportExport versie 1](https://www.microsoft.com/download/details.aspx?id=42659) op het Windows-systeem. De nieuwste versie van de tool heeft beveiligingsupdates om een externe beschermer voor de BitLocker-toets en de bijgewerkte ontgrendelmodusfunctie mogelijk te maken.

  * Rits uit de `waimportexportv1`standaardmap . Bijvoorbeeld `C:\WaImportExportV1`.
* Heb een FedEx/DHL account. Als u een andere provider dan FedEx/DHL wilt gebruiken, `adbops@microsoft.com`neemt u contact op met het Azure Data Box Operations-team op .  
  * De rekening moet geldig zijn, moet saldo hebben en moet retourverzendmogelijkheden hebben.
  * Een volgnummer genereren voor de exporttaak.
  * Elke taak moet een apart volgnummer hebben. Meerdere taken met hetzelfde trackingnummer worden niet ondersteund.
  * Als je geen carrieraccount hebt, ga je naar:
    * [Een FedEX-account aanmaken,](https://www.fedex.com/en-us/create-account.html)of
    * [Maak een DHL-account](http://www.dhl-usa.com/en/express/shipping/open_account.html)aan .

## <a name="step-1-prepare-the-drives"></a>Stap 1: De stations voorbereiden

Met deze stap wordt een journaalbestand gegenereerd. Het logboekbestand slaat basisgegevens op, zoals het serienummer van de stations, de versleutelingssleutel en de gegevens van het opslagaccount.

Voer de volgende stappen uit om de stations voor te bereiden.

1. Sluit uw schijven aan op het Windows-systeem via SATA-connectors.
2. Maak één NTFS-volume op elk station. Wijs een stationsletter toe aan het volume. Gebruik geen bevestigingspunten.
3. BitLocker-versleuteling inschakelen op het NTFS-volume. Als u een Windows Server-systeem gebruikt, gebruikt u de instructies in [BitLocker inschakelen op Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
4. Kopieer gegevens naar versleuteld volume. Gebruik slepen en neerzetten of Robocopy of een dergelijk kopieergereedschap. Er wordt een journal *(.jrn)* bestand gemaakt in dezelfde map waar u het gereedschap uitvoert.

   Als het station is vergrendeld en u het station moet ontgrendelen, kunnen de stappen om te ontgrendelen verschillen, afhankelijk van uw use case.

   * Als u gegevens hebt toegevoegd aan een vooraf versleuteld station (WAImportExport-tool is niet gebruikt voor versleuteling), gebruikt u de BitLocker-sleutel (een numeriek wachtwoord dat u opgeeft) in de pop-up om het station te ontgrendelen.

   * Als u gegevens hebt toegevoegd aan een station dat is versleuteld door het gereedschap WAImportExport, gebruikt u de volgende opdracht om het station te ontgrendelen:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Open een PowerShell- of opdrachtregelvenster met beheerdersbevoegdheden. Voer de volgende opdracht uit om de map met de rits te wijzigen in de map zonder rits:

    `cd C:\WaImportExportV1`
6. Voer de volgende opdracht uit om de BitLocker-toets van het station te krijgen:

    `manage-bde -protectors -get <DriveLetter>:`
7. Voer de volgende opdracht uit om de schijf voor te bereiden. **Afhankelijk van de gegevensgrootte kan dit enkele uren tot dagen duren.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Er wordt een journaalbestand gemaakt in dezelfde map waar u het gereedschap hebt uitgevoerd. Er worden ook twee andere bestanden gemaakt: een *.xml-bestand* (map waarin u het gereedschap uitvoert) en een *bestand drive-manifest.xml* (map waarin gegevens zich bevinden).

    De gebruikte parameters worden beschreven in de volgende tabel:

    |Optie  |Beschrijving  |
    |---------|---------|
    |/j:     |De naam van het journaalbestand, met de .jrn extensie. Per station wordt een journaalbestand gegenereerd. We raden u aan het serienummer van de schijf als naam van het logboekbestand te gebruiken.         |
    |/ID:     |De sessie-ID. Gebruik een uniek sessienummer voor elk exemplaar van de opdracht.      |
    |/t:     |De drive letter van de schijf te worden verzonden. Bijvoorbeeld, rijden `D`.         |
    |/bk:     |De BitLocker-toets voor het station. Het numerieke wachtwoord van de uitvoer van`manage-bde -protectors -get D:`      |
    |/srcdir:     |De drive letter van de schijf `:\`te worden verzonden, gevolgd door . Bijvoorbeeld `D:\`.         |
    |/dstdir:     |De naam van de doelcontainer in Azure Storage.         |
    |/blobtype:     |Met deze optie geeft u het type blobs op waarnaar u de gegevens wilt importeren. Voor blokblobs is `BlockBlob` dit en voor paginablobs is `PageBlob`dit .         |
    |/skipwrite:     |De optie die aangeeft dat er geen nieuwe gegevens nodig zijn om te worden gekopieerd en bestaande gegevens op de schijf moeten worden voorbereid.          |
    |/enablecontentmd5:     |De optie wanneer ingeschakeld, zorgt ervoor dat MD5 wordt berekend en ingesteld als `Content-md5` eigenschap op elke blob. Gebruik deze optie alleen als `Content-md5` u het veld wilt gebruiken nadat de gegevens naar Azure zijn geüpload. <br> Deze optie heeft geen invloed op de controle van de gegevensintegriteit (die standaard plaatsvindt). De instelling verhoogt de tijd die nodig is om gegevens te uploaden naar de cloud.          |
8. Herhaal de vorige stap voor elke schijf die moet worden verzonden. Voor elke run van de opdrachtregel wordt een journaalbestand met de opgegeven naam gemaakt.

    > [!IMPORTANT]
    > * Samen met het journaalbestand wordt ook een `<Journal file name>_DriveInfo_<Drive serial ID>.xml` bestand gemaakt in dezelfde map waar het gereedschap zich bevindt. Het .xml-bestand wordt gebruikt in plaats van het logboekbestand bij het maken van een taak als het logboekbestand te groot is.

## <a name="step-2-create-an-import-job"></a>Stap 2: Een importtaak maken

Voer de volgende stappen uit om een importtaak te maken in de Azure-portal.

1. Log hier https://portal.azure.com/in
2. Ga naar **Alle services >-> Import/export-taken**.

    ![Ga naar Taken importeren/exporteren](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Klik **op Import/exporttaak maken**.

    ![Klik op Import/export maken](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. In **de basis:**

   * Selecteer **Importeren in Azure**.
   * Voer een beschrijvende naam in voor de importtaak. Gebruik de naam om de voortgang van uw taken bij te houden.
       * De naam mag alleen kleine letters, cijfers en koppeltekens bevatten.
       * De naam moet beginnen met een letter en mag geen spaties bevatten.
   * Selecteer een abonnement.
   * Voer een resourcegroep in of selecteer deze.  

     ![Importtaak maken - Stap 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. In **Job details:**

   * Upload de drive-logboekbestanden die u hebt verkregen tijdens de voorbereidingsstap van het station. Als `waimportexport.exe version1` u wordt gebruikt, uploadt u één bestand voor elk station dat u hebt voorbereid. Als de grootte van het logboekbestand groter `<Journal file name>_DriveInfo_<Drive serial ID>.xml` is dan 2 MB, u het ook gemaakte logboekbestand gebruiken.
   * Selecteer het doelopslagaccount waar gegevens zich bevinden.
   * De locatie voor het afzetten wordt automatisch ingevuld op basis van de regio van het geselecteerde opslagaccount.

   ![Importtaak maken - Stap 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. In **Ruil verzendkosten info:**

   * Selecteer de provider in de vervolgkeuzelijst. Als u een andere vervoerder dan FedEx/DHL wilt gebruiken, kiest u een bestaande optie in de vervolgkeuzelijst. Neem contact op met `adbops@microsoft.com` het Azure Data Box Operations-team met de informatie over de provider die u wilt gebruiken.
   * Voer een geldig provideraccountnummer in dat u bij die provider hebt gemaakt. Microsoft gebruikt dit account om de stations naar u terug te sturen zodra uw importtaak is voltooid. Als u geen rekeningnummer hebt, maakt u een [FedEx-](https://www.fedex.com/us/oadr/) of [DHL-carrieraccount](https://www.dhl.com/) aan.
   * Geef een volledige en geldige naam van de contactpersoon, telefoon, e-mail, adres, stad, zip, staat/provincie en land/regio.

       > [!TIP]
       > Geef in plaats van een e-mailadres op te geven voor één gebruiker, een groepse-mail op. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder vertrekt.

     ![Importtaak maken - Stap 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. In de **samenvatting**:

   * Bekijk de functie-informatie in de samenvatting. Noteer de taaknaam en het Azure-datacenterverzendadres om schijven terug te sturen naar Azure. Deze informatie wordt later gebruikt op het verzendlabel.
   * Klik op **OK** om de importtaak te maken.

     ![Importtaak maken - Stap 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-optional-configure-customer-managed-key"></a>Stap 3 (Optioneel): Klantbeheersleutel configureren

Sla deze stap over en ga naar de volgende stap als u de door Microsoft beheerde sleutel wilt gebruiken om uw BitLocker-sleutels voor de stations te beschermen. Als u uw eigen sleutel wilt configureren om de BitLocker-sleutel te beschermen, volgt u de instructies in [De sleutels configureren met Azure Key Vault voor Azure Import/Export in de Azure-portal](storage-import-export-encryption-key-portal.md)

## <a name="step-4-ship-the-drives"></a>Stap 4: Verzend de schijven

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Stap 5: De taak bijwerken met trackinginformatie

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Stap 6: Gegevens uploaden naar Azure verifiëren

Volg de taak tot voltooiing. Zodra de taak is voltooid, controleert u of uw gegevens zijn geüpload naar Azure. Verwijder de on-premises gegevens pas nadat u hebt geverifieerd dat de upload is gelukt.

## <a name="next-steps"></a>Volgende stappen

* [De taak- en rijstatus weergeven](storage-import-export-view-drive-status.md)
* [Import-/exportvereisten controleren](storage-import-export-requirements.md)
