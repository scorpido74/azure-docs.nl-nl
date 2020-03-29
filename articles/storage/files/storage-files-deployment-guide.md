---
title: Azure-bestanden implementeren | Microsoft Documenten
description: Meer informatie over het implementeren van Azure-bestanden van begin tot eind.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 38339defc9d06f3e809bc24f957ebbb30abb46d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598779"
---
# <a name="how-to-deploy-azure-files"></a>Azure Files implementeren
[Azure Files](storage-files-introduction.md) biedt volledig beheerde bestandsshares in de cloud die toegankelijk zijn via het industriestandaard SMB-protocol. In dit artikel ziet u hoe u Azure-bestanden praktisch binnen uw organisatie implementeren.

We raden u ten zeerste aan [planning voor een Azure-bestanden-implementatie](storage-files-planning.md) te lezen voordat u de stappen in dit artikel volgt.

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u de volgende stappen al hebt voltooid:

- Een Azure Storage-account gemaakt met de gewenste tolerantie- en versleutelingsopties in de regio die u wenst. Zie [Een opslagaccount maken](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor stapsgewijze aanwijzingen over het maken van een opslagaccount.
- Een Azure-bestandsshare maken met het gewenste quotum in uw opslagaccount. Zie [Een bestandsshare maken](storage-how-to-create-file-share.md) voor stapsgewijze aanwijzingen over het maken van een bestandsshare.

## <a name="transfer-data-into-azure-files"></a>Gegevens overbrengen naar Azure-bestanden
Mogelijk wilt u bestaande bestandsshares, zoals die die on-premises zijn opgeslagen, migreren naar uw nieuwe Azure-bestandsshare. In deze sectie ziet u hoe u gegevens verplaatsen naar een Azure-bestandsshare via verschillende populaire methoden die zijn beschreven in de [planningshandleiding](storage-files-planning.md#migration)

### <a name="azure-file-sync"></a>Azure File Sync
Met Azure File Sync kunt u bestandsshares van uw organisatie in Azure Files centraliseren zonder in te leveren op de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver. Dit gebeurt door uw Windows-servers om te zetten in een snelle cache van uw Azure-bestandsshare. U kunt elk protocol dat beschikbaar is in Windows Server gebruiken voor lokale toegang tot uw gegevens (inclusief SMB, NFS en FTPS) en u kunt zoveel caches hebben als waar ook ter wereld u nodig hebt.

Azure File Sync kan worden gebruikt om gegevens te migreren naar een Azure-bestandsshare, zelfs als het synchronisatiemechanisme niet gewenst is voor langdurig gebruik. Meer informatie over het gebruik van Azure File Sync om gegevens over te zetten naar Azure-bestandsshare vindt u in [Planning voor een Azure File Sync-implementatie](storage-sync-files-planning.md) en Hoe Azure File Sync te [implementeren](storage-sync-files-deployment-guide.md).

### <a name="azure-importexport"></a>Azure Import/Export
Met de Azure Import/Export-service u op een veilige manier grote hoeveelheden gegevens overbrengen naar een Azure-bestandsshare door harde schijven naar een Azure-datacenter te verzenden. Zie [De service Microsoft Azure Importeren/exporteren gebruiken om gegevens over te zetten naar Azure-opslag](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor een gedetailleerder overzicht van de service.

> [!Note]  
> De Azure Import/Export-service ondersteunt op dit moment geen ondersteuning voor het exporteren van bestanden uit een Azure-bestandsshare.

De volgende stappen importeren gegevens van een on-premises locatie naar uw Azure-bestandsshare.

1. Verkrijg het vereiste aantal harde schijven om te mailen naar Azure. Harde schijven kunnen van elke schijfgrootte zijn, maar moeten een 2,5" of 3,5"-SSD of HDD zijn die de SATA II- of SATA III-standaard ondersteunt. 

2. Maak verbinding en monteer elke schijf op de server/pc en doe de gegevensoverdracht. Voor optimale prestaties raden we u aan de on-premises exporttaak lokaal uit te voeren op de server die de gegevens bevat. In sommige gevallen, zoals wanneer de bestandsserver die de gegevens bedient een NAS-apparaat is, is dit mogelijk niet mogelijk. In dat geval is het perfect aanvaardbaar om elke schijf op een pc te monteren.

3. Zorg ervoor dat elk station online is, geïnitialiseerd en een stationsletter toegewezen krijgt. Als u een station online wilt brengen, initialiseren en een stationsletter wilt toewijzen, opent u de module Disk Management MMC (diskmgmt.msc).

    - Als u een schijf online wilt brengen (als deze nog niet online is), klikt u met de rechtermuisknop op de schijf in het onderste deelvenster van het Disk Management MMC en selecteert u 'Online'.
    - Als u een schijf wilt initialiseren, klikt u met de rechtermuisknop op de schijf in het onderste deelvenster (nadat de schijf online is) en selecteert u 'Initialiseren'. Zorg ervoor dat u "GPT" selecteert wanneer u daarom vraagt.

        ![Een schermafbeelding van het menu Disk initialiseren in het Disk Management MMC](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - Als u een stationsletter aan de schijf wilt toewijzen, klikt u met de rechtermuisknop op de ruimte 'niet-toegewezen' van de online en geïnitialiseerde schijf en klikt u op 'Nieuw eenvoudig volume'. Hiermee u de stationsbrief toewijzen. Houd er rekening mee dat u het volume niet hoeft op te maken, omdat dit later zal gebeuren.

        ![Een schermafbeelding van de wizard Nieuw eenvoudig volume in de Disk Management MMC](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. Maak het CSV-bestand van de gegevensset. Het CSV-bestand van de gegevensset is een toewijzing tussen het pad naar de on-premises gegevens en de gewenste Azure-bestandsshare waarnaar de gegevens moeten worden gekopieerd. In het volgende CSV-bestand van de volgende set wordt bijvoorbeeld een on-premises bestandsshare ('F:\shares\scratch') toegewezen aan een Azure-bestandsshare ('MyAzureFileShare'):
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    Er kunnen meerdere aandelen met een opslagaccount worden opgegeven. Zie [CSV-bestand voor de set voorbereiden](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie.

5. Maak het CSV-bestand driveset. In het CSV-bestand driveset worden de schijven weergegeven die beschikbaar zijn voor de on-premises exportagent. De volgende CSV-bestandslijsten `X:` `Y:`van driveset `Z:` en stations die moeten worden gebruikt in de on-premises exporttaak:

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    Zie [Csv-bestand voor driveset voorbereiden](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie.

6. Gebruik het [GEREEDSCHAP WAImportExport](https://www.microsoft.com/download/details.aspx?id=55280) om uw gegevens naar een of meer harde schijven te kopiëren.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > Wijzig de gegevens op de harde schijven of het logboekbestand niet nadat u de schijfvoorbereiding hebt voltooid.

7. [Een importtaak maken](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy is een bekende kopieertool die wordt geleverd met Windows en Windows Server. Robocopy kan worden gebruikt om gegevens over te zetten naar Azure-bestanden door het bestandsaandeel lokaal te monteren en vervolgens de gemonteerde locatie als bestemming te gebruiken in de opdracht Robocopy. Het gebruik van Robocopy is vrij eenvoudig:

1. [Uw Azure-bestandsshare weergeven](storage-how-to-use-files-windows.md). Voor optimale prestaties raden we u aan het Azure-bestandsaandeel lokaal te monteren op de server die de gegevens bevat. In sommige gevallen, zoals wanneer de bestandsserver die de gegevens bedient een NAS-apparaat is, is dit mogelijk niet mogelijk. In dat geval is het perfect aanvaardbaar om het Azure-bestandsaandeel op een pc te monteren. In dit `net use` voorbeeld wordt u op de opdrachtregel gebruikt om de bestandsshare te monteren:

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. Gebruik `robocopy` op de opdrachtregel om gegevens naar de Azure-bestandsshare te verplaatsen:

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy heeft een aanzienlijk aantal opties om het kopieergedrag naar wens aan te passen. Bekijk voor meer informatie de [handleidingpagina van Robocopy.](https://technet.microsoft.com/library/cc733145.aspx)

### <a name="azcopy"></a>AzCopy
AzCopy is een command-line hulpprogramma dat is ontworpen voor het kopiëren van gegevens van en naar Azure Files, evenals Azure Blob-opslag, met behulp van eenvoudige opdrachten met optimale prestaties. AzCopy gebruiken is eenvoudig:

1. Download de [nieuwste versie van AzCopy op Windows](https://aka.ms/downloadazcopy) of [Linux.](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux#download-and-install-azcopy)
2. Gebruik `azcopy` op de opdrachtregel om gegevens naar de Azure-bestandsshare te verplaatsen. De syntaxis in Windows is als volgt: 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Op Linux is de syntaxis van de opdracht een beetje anders:

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy heeft een aanzienlijk aantal opties om het kopieergedrag naar wens aan te passen. Bekijk [AzCopy op Windows](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) en [AzCopy op Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor meer informatie.

## <a name="automatically-mount-on-needed-pcsservers"></a>Automatisch monteren op benodigde pc's/servers
Om een on-premises bestandsshare te vervangen, is het handig om de aandelen vooraf te monteren op de machines waarop deze worden gebruikt. Dit kan automatisch worden gedaan op een lijst van machines.

> [!Note]  
> Het monteren van een Azure-bestandsshare vereist het gebruik van de opslagaccountsleutel als wachtwoord, daarom raden we alleen aan om in vertrouwde omgevingen te worden geïnstalleerd. 

### <a name="windows"></a>Windows
PowerShell kan worden gebruikt voer de opdracht monteren op meerdere pc's. In het volgende `$computers` voorbeeld wordt handmatig ingevuld, maar u de lijst met computers genereren die automatisch kan worden gemonteerd. U deze variabele bijvoorbeeld vullen met resultaten uit Active Directory.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
Een eenvoudig bashscript in combinatie met SSH kan in het volgende voorbeeld hetzelfde resultaat opleveren. De `$computer` variabele wordt op dezelfde manier overgelaten om door de gebruiker te worden ingevuld:

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>Volgende stappen
- [Een Azure File Sync-implementatie plannen](storage-sync-files-planning.md)
- [Problemen met Azure Files in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
- [Problemen met Azure Files in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
