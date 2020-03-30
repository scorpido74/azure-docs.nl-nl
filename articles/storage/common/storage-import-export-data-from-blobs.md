---
title: Azure Importeren/exporteren gebruiken om gegevens uit Azure Blobs te exporteren | Microsoft Documenten
description: Meer informatie over het maken van exporttaken in Azure-portal om gegevens over te dragen vanuit Azure Blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: a5afa6439caa6b7c1572447e3b212f3357bf296a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282508"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>De Azure Import/Export-service gebruiken voor het exporteren van gegevens uit Azure Blob-opslag

In dit artikel vindt u stapsgewijze instructies over het gebruik van de Azure Import/Export-service om grote hoeveelheden gegevens veilig te exporteren uit Azure Blob-opslag. De service vereist dat u lege schijven verzendt naar het Azure-datacenter. De service exporteert gegevens van uw opslagaccount naar de stations en verzendt vervolgens de schijven terug.

## <a name="prerequisites"></a>Vereisten

Voordat u een exporttaak maakt om gegevens over te dragen uit Azure Blob Storage, controleert u zorgvuldig en voltooit u de volgende lijst met vereisten voor deze service.
U moet:

- Zorg voor een actief Azure-abonnement dat kan worden gebruikt voor de import/exportservice.
- Hebben ten minste één Azure Storage-account. Zie de lijst [met ondersteunde opslagaccounts en opslagtypen voor import/exportservice](storage-import-export-requirements.md). Zie [Een opslagaccount maken](storage-account-create.md)voor informatie over het maken van een nieuw opslagaccount .
- Hebben voldoende aantal schijven van [ondersteunde typen](storage-import-export-requirements.md#supported-disks).
- Heb een FedEx/DHL account. Als u een andere provider dan FedEx/DHL wilt gebruiken, `adbops@microsoft.com`neemt u contact op met het Azure Data Box Operations-team op .
  - De rekening moet geldig zijn, moet saldo hebben en moet retourverzendmogelijkheden hebben.
  - Een volgnummer genereren voor de exporttaak.
  - Elke taak moet een apart volgnummer hebben. Meerdere taken met hetzelfde trackingnummer worden niet ondersteund.
  - Als je geen carrieraccount hebt, ga je naar:
    - [Een FedEX-account aanmaken,](https://www.fedex.com/en-us/create-account.html)of
    - [Maak een DHL-account](http://www.dhl-usa.com/en/express/shipping/open_account.html)aan .

## <a name="step-1-create-an-export-job"></a>Stap 1: Een exporttaak maken

Voer de volgende stappen uit om een exporttaak te maken in de Azure-portal.

1. Log hier https://portal.azure.com/in
2. Ga naar **Alle services >-> Import/export-taken**.

    ![Ga naar Taken importeren/exporteren](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Klik **op Import/exporttaak maken**.

    ![Klik op Taak importeren/exporteren](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. In **de basis:**

    - Selecteer **Exporteren vanuit Azure**.
    - Voer een beschrijvende naam in voor de exporttaak. Gebruik de naam die u kiest om de voortgang van uw taken bij te houden.
        - De naam mag alleen kleine letters, cijfers, koppeltekens en underscores bevatten.
        - De naam moet beginnen met een letter en mag geen spaties bevatten.
    - Selecteer een abonnement.
    - Voer een resourcegroep in of selecteer deze.

        ![Basisbeginselen](./media/storage-import-export-data-from-blobs/export-from-blob3.png)

5. In **Job details:**

    - Selecteer het opslagaccount waar de te exporteren gegevens zich bevinden. Gebruik een opslagaccount in de buurt van waar u zich bevindt.
    - De locatie voor het afzetten wordt automatisch ingevuld op basis van de regio van het geselecteerde opslagaccount.
    - Geef de blobgegevens op die u vanuit uw opslagaccount naar uw lege schijf of stations wilt exporteren.
    - Kies ervoor om alle blobgegevens in het opslagaccount te **exporteren.**

         ![Alle exporteren](./media/storage-import-export-data-from-blobs/export-from-blob4.png)

    - U opgeven welke containers en blobs u wilt exporteren.
        - **Een blob opgeven om te exporteren:** Gebruik de **keuzeoptie Gelijk aan.** Geef het relatieve pad naar de blob op, te beginnen met de containernaam. Gebruik *$root* om de hoofdcontainer op te geven.
        - **Als u alle blobs wilt opgeven die beginnen met een voorvoegsel:** Gebruik de selectie **Start met.** Geef het voorvoegsel op, te beginnen met een voorwaartse slash '/'. Het voorvoegsel kan het voorvoegsel zijn van de containernaam, de volledige containernaam of de volledige containernaam gevolgd door het voorvoegsel van de blobnaam. U moet de blobpaden in geldige indeling opgeven om fouten tijdens de verwerking te voorkomen, zoals in deze schermafbeelding wordt weergegeven. Zie [Voorbeelden van geldige blobpaden](#examples-of-valid-blob-paths)voor meer informatie .

           ![Geselecteerde containers en blobs exporteren](./media/storage-import-export-data-from-blobs/export-from-blob5.png)

    - U exporteren vanuit het bloblijstbestand.

        ![Exporteren uit het bloblijstbestand](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  

   > [!NOTE]
   > Als de te exporteren blob wordt gebruikt tijdens het kopiëren van gegevens, maakt de Azure Import/Export-service een momentopname van de blob en kopieert de momentopname.

6. In **Ruil verzendkosten info:**

    - Selecteer de provider in de vervolgkeuzelijst. Als u een andere vervoerder dan FedEx/DHL wilt gebruiken, kiest u een bestaande optie in de vervolgkeuzelijst. Neem contact op met `adbops@microsoft.com` het Azure Data Box Operations-team met de informatie over de provider die u wilt gebruiken.
    - Voer een geldig provideraccountnummer in dat u bij die provider hebt gemaakt. Microsoft gebruikt dit account om de stations naar u terug te sturen zodra uw exporttaak is voltooid.
    - Geef een volledige en geldige naam van de contactpersoon, telefoon, e-mail, adres, stad, zip, staat/provincie en land/regio.

        > [!TIP]
        > Geef in plaats van een e-mailadres op te geven voor één gebruiker, een groepse-mail op. Dit zorgt ervoor dat u meldingen ontvangt, zelfs als een beheerder vertrekt.

7. **Samengevat**:

    - Bekijk de details van de taak.
    - Noteer de taaknaam en het opgegeven Azure-datacenterverzendadres voor het verzenden van schijven naar Azure.

        > [!NOTE]
        > Stuur de schijven altijd naar het datacenter dat is vermeld in de Azure-portal. Als de schijven naar het verkeerde datacenter worden verzonden, wordt de taak niet verwerkt.

    - Klik op **OK** om het maken van exportbanen te voltooien.

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Stap 2: Verzend de schijven

Als u niet weet hoeveel schijven u nodig hebt, gaat u naar [het aantal stations controleren.](#check-the-number-of-drives) Als u het aantal schijven weet, gaat u verder met het verzenden van de schijven.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Stap 3: De taak bijwerken met trackinginformatie

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Stap 4: De schijven ontvangen

Wanneer het dashboard meldt dat de taak is voltooid, worden de schijven naar u verzonden en is het volgnummer voor de zending beschikbaar op de portal.

1. Nadat u de schijven met geëxporteerde gegevens hebt ontvangen, moet u de BitLocker-sleutels hebben om de schijven te ontgrendelen. Ga naar de exporttaak in de Azure-portal. Klik op het tabblad **Importeren/exporteren.**
2. Selecteer en klik op uw exporttaak in de lijst. Ga naar **Encryptie** en kopieer de sleutels.

   ![BitLocker-sleutels weergeven voor exporttaak](./media/storage-import-export-service/export-job-bitlocker-keys-02.png)

3. Gebruik de BitLocker-toetsen om de schijven te ontgrendelen.

De export is voltooid.

## <a name="step-5-unlock-the-disks"></a>Stap 5: De schijven ontgrendelen

Als u versie 1.4.0.300 van het gereedschap WAImportExport gebruikt, gebruikt u de volgende opdracht om het station te ontgrendelen:

    `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`  

Als u eerdere versies van het gereedschap gebruikt, gebruikt u het dialoogvenster BitLocker om het station te ontgrendelen.

Op dit moment u de taak verwijderen of verlaten. Taken worden na 90 dagen automatisch verwijderd.

## <a name="check-the-number-of-drives"></a>Controleer het aantal stations

Met *deze optionele* stap u bepalen hoeveel schijven nodig zijn voor de exporttaak. Voer deze stap uit op een Windows-systeem met een [ondersteunde versie van het besturingssysteem](storage-import-export-requirements.md#supported-operating-systems).

1. [Download de WAImportExport versie 1](https://www.microsoft.com/download/details.aspx?id=42659) op het Windows-systeem.
2. Rits uit de `waimportexportv1`standaardmap . Bijvoorbeeld `C:\WaImportExportV1`.
3. Open een PowerShell- of opdrachtregelvenster met beheerdersbevoegdheden. Voer de volgende opdracht uit om de map met de rits te wijzigen in de map zonder rits:

    `cd C:\WaImportExportV1`

4. Voer de volgende opdracht uit om het aantal schijven te controleren dat nodig is voor de geselecteerde blobs:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    De parameters worden beschreven in de volgende tabel:

    |Command-line parameter|Beschrijving|  
    |--------------------------|-----------------|  
    |**/logdir:**|Optioneel. De logboekmap. Verbose log bestanden zijn geschreven naar deze directory. Als deze niet is opgegeven, wordt de huidige map gebruikt als logboekmap.|  
    |**/sn:**|Vereist. De naam van de opslagrekening voor de exporttaak.|  
    |**/sk:**|Alleen vereist als een container SAS niet is opgegeven. De accountsleutel voor de opslagrekening voor de exporttaak.|  
    |**/csas:**|Alleen vereist als een opslagaccountsleutel niet is opgegeven. De containerSAS voor het weergeven van de blobs die moeten worden geëxporteerd in de exporttaak.|  
    |**/ExportBlobListFile:**|Vereist. Pad naar het XML-bestand met lijst met blobpaden of blobpadvoorvoegsels voor de blobs die moeten worden geëxporteerd. De bestandsindeling die `BlobListBlobPath` wordt gebruikt in het element in de bewerking [Taak zetten](/rest/api/storageimportexport/jobs) van de REST-API voor import/exportservice.|  
    |**/DriveSize:**|Vereist. De grootte van schijven te gebruiken voor een export taak, *bijvoorbeeld,* 500 GB, 1,5 TB.|  

    Zie een [voorbeeld van de opdracht Voorbeeldexport](#example-of-previewexport-command).

5. Controleer of u lezen / schrijven naar de stations die worden verzonden voor de export taak.

### <a name="example-of-previewexport-command"></a>Voorbeeld van de opdracht PreviewExport

In het volgende `PreviewExport` voorbeeld wordt de opdracht getoond:  

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```  

Het bestand met de lijst met exportblobs kan blobnamen en blob-voorvoegsels bevatten, zoals hier wordt weergegeven:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

De Azure Import/Export Tool bevat alle blobs die moeten worden geëxporteerd en berekent hoe ze kunnen worden verpakt in stations van de opgegeven grootte, rekening houdend met de benodigde overhead, en schat vervolgens het aantal stations dat nodig is om de blobs vast te houden en gebruiksinformatie te stimuleren.  

Hier is een voorbeeld van de uitvoer, met informatieve logboeken weggelaten:  

```powershell
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  

Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>Voorbeelden van geldige blobpaden

In de volgende tabel worden voorbeelden weergegeven van geldige blobpaden:

   | Kiezer | Blobpad | Beschrijving |
   | --- | --- | --- |
   | Begint met |/ |Hiermee exporteert u alle blobs in het opslagaccount |
   | Begint met |/$root/ |Hiermee exporteert u alle blobs in de hoofdcontainer |
   | Begint met |/boek |Hiermee exporteert u alle blobs in een container die begint met **voorvoegselboek** |
   | Begint met |/muziek/ |Exporteert alle blobs in **containermuziek** |
   | Begint met |/muziek/liefde |Exporteert alle blobs in **containermuziek** die beginnen met **voorvoegselliefde** |
   | Gelijk aan |$root/logo.bmp |**Blob-logo.bmp** exporteren in de hoofdcontainer |
   | Gelijk aan |video's/story.mp4 |Exporteert blob **story.mp4** in **containervideo's** |

## <a name="next-steps"></a>Volgende stappen

- [De taak- en rijstatus weergeven](storage-import-export-view-drive-status.md)
- [Import-/exportvereisten controleren](storage-import-export-requirements.md)
