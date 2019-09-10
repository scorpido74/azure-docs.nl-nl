---
title: Zelfstudie voor het via SMB kopiëren van gegevens naar Azure Data Box Heavy | Microsoft Docs
description: Leer hoe u via SMB gegevens kopieert naar uw Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 4267b8299e13f1705b218e65b268c45bd5a658e2
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70240304"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Zelfstudie: Gegevens kopiëren naar Azure Data Box Heavy via SMB

::: zone-end

::: zone target = "chromeless"

## <a name="copy-data-to-azure-data-box-heavy"></a>Gegevens kopiëren naar Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

Deze zelfstudie beschrijft hoe u verbinding kunt maken en gegevens kunt kopiëren vanaf uw hostcomputer via de lokale gebruikersinterface.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Verbinding maken met Data Box Heavy
> * Gegevens kopiëren naar Data Box Heavy

::: zone-end

::: zone target = "chromeless"

U kunt gegevens van de bronserver naar uw Data Box kopiëren via SMB, NFS, REST, een service voor het kopiëren van gegevens, of naar beheerde schijven.

Zorg er altijd voor dat de namen van shares en mappen, en de gegevensgrootte, de richtlijnen volgen die worden beschreven in [Servicelimieten van Azure Storage en Data Box Heavy](data-box-heavy-limits.md).

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de zelfstudie [ Azure Data Box Heavy instellen](data-box-deploy-set-up.md) voltooid.
2. U hebt de Data Box Heavy ontvangen en de orderstatus in de portal is **Geleverd**.
3. U beschikt over een hostcomputer waarop de gegevens staan die u naar de Data Box Heavy wilt kopiëren. Op uw hostcomputer moet
    - Een [ondersteund besturingssysteem](data-box-system-requirements.md) worden uitgevoerd.
    - Verbonden zijn met een netwerk met hoge snelheid. Voor de snelste kopieersnelheden kunnen twee verbindingen van 40 GbE (één per knooppunt) naast elkaar worden gebruikt. Als u niet beschikt over een 40-GbE-verbinding, is het advies dat u ten minste twee verbindingen van 10 GbE (één per knooppunt) gebruikt.
   

## <a name="connect-to-data-box-heavy-shares"></a>Verbinding maken met Data Box Heavy-shares

Op basis van het geselecteerde opslagaccount maakt Data Box Heavy maximaal:
- Drie shares voor elk gekoppeld opslagaccount voor GPv1 en GPv2.
- Eén share voor premium opslag.
- Eén share voor een blob-opslagaccount.

Deze shares worden gemaakt op beide knooppunten van het apparaat.

Onder shares voor blok-blobs en pagina-blobs:
- Entiteiten op het hoogste niveau zijn containers.
- Entiteiten op het tweede niveau zijn blobs.

Onder shares voor Azure Files:
- Entiteiten op het hoogste niveau zijn shares.
- Entiteiten op het tweede niveau zijn bestanden.

In de volgende tabel ziet u het UNC-pad naar de shares op uw Data Box Heavy en de URL van het Azure Storage-pad waarnaar de gegevens worden geüpload. De uiteindelijke URL van het Azure Storage-pad kan worden afgeleid van het UNC-pad naar de shares.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blok-blobs | <li>UNC-pad naar shares: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-pagina-blobs  | <li>UNC-pad naar shares: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-pad naar shares: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Er zijn verschillende stappen nodig om verbinding te maken met behulp van een Windows-of Linux-client.

> [!NOTE]
> Volg dezelfde stappen om gelijktijdig verbinding te maken met beide knooppunten van het apparaat.

### <a name="connect-on-a-windows-system"></a>Verbinding maken op een Windows-systeem

Als u een hostcomputer met Windows Server gebruikt, voert u deze stappen uit om verbinding met de Data Box Heavy te maken.

1. U moet eerst een verificatie uitvoeren en een sessie starten. Ga naar **Verbinding maken en kopiëren**. Klik op **Referenties ophalen** om de toegangsreferenties te krijgen voor de shares die aan uw opslagaccount zijn gekoppeld.

    ![Sharereferenties 1 ophalen](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. Kopieer in het dialoogvenster Verbinding maken met share en gegevens kopiëren de **Gebruikersnaam** en het **Wachtwoord** voor de share. Klik op **OK**.
    
    ![Sharereferenties 1 ophalen](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Om vanaf uw hostcomputer toegang te krijgen tot de shares die zijn gekoppeld aan uw opslagaccount (*databoxe2etest* in het volgende voorbeeld), opent u een opdrachtvenster. Typ in de opdrachtprompt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Afhankelijk van de gegevensindeling, zijn dit de paden voor de shares:
    - Azure-blok-blob - `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Azure-pagina-blob - `\\10.100.10.100\databoxe2etest_PageBlob`
    - Azure Files - `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Voer het wachtwoord voor de share in wanneer er om wordt gevraagd. Het volgende voorbeeld laat zien hoe u verbinding met een share kunt maken via de voorgaande opdracht.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Druk op Windows-toets+R. Geef in het venster **Uitvoeren** het `\\<device IP address>` op. Klik op **OK** om Verkenner te openen.
    
    ![Verbinding met de share maken via Verkenner 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Als het goed is, worden de shares nu weergegeven als mappen.
    
    ![Verbinding met de share maken via Verkenner 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Maak altijd een map voor de bestanden die u van plan bent te kopiëren in de bestandsshare en kopieer de bestanden vervolgens naar die map**. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. Het is niet mogelijk om bestanden rechtstreeks te kopiëren naar de *root*-map in het opslagaccount.
    
### <a name="connect-on-a-linux-system"></a>Verbinding maken op een Linux-systeem

Als u een Linux-client gebruikt, gebruikt u de volgende opdracht om de SMB-share koppelen.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

De parameter `vers` is de versie van SMB die wordt ondersteund door uw Linux-host. Geef de juiste versie op in de bovenstaande opdracht.

Zie [Ondersteunde bestandssystemen voor Linux-clients](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients) voor versies van SMB die door de Data Box Heavy worden ondersteund.

## <a name="copy-data-to-data-box-heavy"></a>Gegevens kopiëren naar Data Box Heavy

Nadat u verbinding met de Data Box Heavy-shares hebt gemaakt, gaat u gegevens kopiëren.

### <a name="copy-considerations"></a>Aandachtspunten bij kopiëren

Neem de volgende punten door voordat u gegevens gaat kopiëren:

- Zorg dat u de gegevens kopieert naar shares die overeenkomen met de juiste gegevensindeling. U moet bijvoorbeeld de blok-blobgegevens naar de share voor blok-blobs kopiëren. Kopieer de VHD's naar pagina-blob.

    Als de gegevensindeling niet overeenkomt met het betreffende sharetype, zal het uploaden van gegevens naar Azure op een later tijdstip mislukken.
-  Zorg er tijdens het kopiëren van gegevens voor dat de gegevensgrootte voldoet aan de limieten die worden vermeld in [Limieten voor Azure-opslag en Data Box Heavy](data-box-heavy-limits.md).
- Als de gegevens die door Data Box Heavy worden geüpload gelijktijdig door andere toepassingen buiten Data Box Heavy worden geüpload, kan dit fouten met de uploadtaak veroorzaken en daardoor beschadigde gegevens opleveren.
- We raden aan dat:
    - U niet zowel SMB als NFS tegelijkertijd gebruikt.
    - Dezelfde gegevens naar dezelfde eindbestemming kopieert in Azure.
     
  In dergelijke gevallen kan de definitieve uitkomst namelijk niet worden vastgesteld.
- Maak altijd een map voor de bestanden die u van plan bent te kopiëren in de bestandsshare en kopieer de bestanden vervolgens naar die map. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. Het is niet mogelijk om bestanden rechtstreeks te kopiëren naar de *root*-map in het opslagaccount.

Begin met het kopiëren van gegevens nadat u verbinding met de SMB-share hebt gemaakt.

1. U kunt elk programma voor het kopiëren van bestanden dat compatibel is met SMB, zoals Robocopy, gebruiken om de gegevens te kopiëren. Er kunnen meerdere kopieertaken worden gestart met Robocopy. Gebruik de volgende opdracht:
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    De kenmerken worden in de volgende tabel beschreven.
    
    |Kenmerk  |Beschrijving  |
    |---------|---------|
    |/e      |Hiermee kopieert u submappen, met inbegrip van lege mappen.         |
    |/r:     |Hiermee geeft u het aantal nieuwe pogingen bij mislukte kopieerbewerkingen op.         |
    |/w:     |Hiermee geeft u de wachttijd tussen nieuwe pogingen op in seconden.         |
    |/is     |Neemt dezelfde bestanden op.         |
    |/nfl    |Hiermee geeft u op dat bestandsnamen niet moeten worden vastgelegd.         |
    |/ndl    |Hiermee geeft u op dat mapnamen niet moeten worden vastgelegd.        |
    |/np     |Geeft op dat de voortgang van de kopieerbewerking (het aantal bestanden of mappen dat tot nu toe is gekopieerd) niet zal worden weergegeven. Als de voortgang wordt weergegeven, zullen de prestaties aanzienlijk verminderen.         |
    |/MT     | Gebruik multithreading, 32 of 64-threads wordt aanbevolen. Deze optie wordt niet gebruikt voor versleutelde bestanden. U moet versleutelde en niet-versleutelde bestanden mogelijk scheiden. Kopiëren met één thread verlaagt de prestaties echter aanzienlijk.           |
    |/fft    | Gebruik deze optie om de granulariteit van tijdstempels voor een bestandssysteem te verminderen.        |
    |/b      | Kopieert bestanden in de back-upmodus.        |
    |/z      | Kopieert bestanden in de modus voor opnieuw opstarten; gebruik deze optie als de omgeving instabiel is. Deze optie beperkt doorvoer vanwege de aanvullende logboekregistratie.      |
    | /zb    | Gebruikt de modus voor opnieuw opstarten. Deze optie gebruikt de back-upmodus als de toegang is geweigerd. Deze optie beperkt doorvoer vanwege controlepunten.         |
    |/efsraw | Kopieert alle versleutelde bestanden in de onbewerkte EFS-modus. Alleen gebruiken met versleutelde bestanden.         |
    |log+:\<LogFile>| Voegt de uitvoer toe aan het bestaande logboekbestand.|
    
 
    In het volgende voorbeeld ziet u de uitvoer van de opdracht robocopy voor het kopiëren van bestanden naar de Data Box Heavy.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
    ```       

2. Gebruik de volgende parameters in Robocopy om de prestaties te optimaliseren als u de gegevens kopieert. (De waarden hieronder vertegenwoordigen de aanbevolen scenario's.)

    | Platform    | Voornamelijk kleine bestanden van < 512 KB    | Voornamelijk middelgrote bestanden van 512 KB - 1 MB  | Voornamelijk grote bestanden van > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 Robocopy-sessies <br> 24 threads per sessie | 6 Robocopy-sessies <br> 16 threads per sessie | 6 Robocopy-sessies <br> 16 threads per sessie |


    Ga voor meer informatie over opdrachten voor Robocopy naar [Robocopy en een paar voorbeelden](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

3. Open de doelmap om de gekopieerde bestanden weer te geven en te controleren.

    ![Gekopieerde bestanden bekijken](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Terwijl de gegevens worden gekopieerd:

    - De namen, grootte en indeling van bestanden worden gevalideerd om te controleren of deze voldoen aan de limieten van Azure voor objecten en opslag, evenals de naamgevingsconventies van Azure voor bestanden en containers.
    - Om de gegevensintegriteit te garanderen, wordt er ook inline een controlesom berekend.

    Download de foutbestanden om problemen op te lossen als er fouten zijn opgetreden tijdens het kopiëren. Selecteer het pijlpictogram om de foutbestanden te downloaden.

    ![Foutbestanden downloaden](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Zie [Foutenlogboeken bekijken tijdens het kopiëren van gegevens naar Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy) voor meer informatie. Zie [Problemen met Data Box Heavy oplossen](data-box-troubleshoot.md) voor een gedetailleerde lijst met fouten tijdens het kopiëren van gegevens.

5. Open het foutbestand in Kladblok. Het volgende foutbestand geeft aan dat de gegevens niet goed zijn uitgelijnd.

    ![Foutbestand openen](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Voor een pagina-blob moeten de gegevens op 512 bytes zijn uitgelijnd. Nadat deze gegevens zijn verwijderd, is de fout opgelost zoals u kunt zien in de volgende schermafbeelding.

    ![Fout opgelost](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Nadat het kopiëren is voltooid, gaat u naar de pagina **Dashboard weergeven**. Controleer de gebruikte ruimte en de vrije ruimte op uw apparaat.
    
    ![Vrije en ongebruikte ruimte verifiëren op het dashboard](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Herhaal de bovenstaande stappen om gegevens naar het tweede knooppunt van het apparaat te kopiëren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn verschillende onderwerpen besproken over de Azure Data Box Heavy, zoals:

> [!div class="checklist"]
> * Verbinding maken met Data Box Heavy
> * Gegevens kopiëren naar Data Box Heavy


Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box Heavy naar Microsoft verstuurt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box Heavy verzenden naar Microsoft](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>Gegevens kopiëren via SMB

1. Als u een Windows-host gebruikt, gebruikt u de volgende opdracht om verbinding te maken met de SMB-shares:

    `\\<IP address of your device>\ShareName`

2. Als u de toegangsreferenties voor de shares wilt ophalen, gaat u naar de pagina **Verbinding maken en kopiëren** in de lokale webgebruikersinterface van de Data Box.

3. Gebruik een programma voor het kopiëren van bestanden dat compatibel is met SMB, zoals Robocopy, om gegevens naar shares te kopiëren.

Ga voor stapsgewijze instructies naar [Zelfstudie: Gegevens naar Azure Data Box Disk kopiëren via SMB](data-box-heavy-deploy-copy-data.md).

### <a name="copy-data-via-nfs"></a>Gegevens kopiëren via NFS

1. Als u een NFS-host gebruikt, gebruikt u de volgende opdracht om de NFS-shares te koppelen:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Als u de toegangsreferenties voor de shares wilt ophalen, gaat u naar de pagina **Verbinding maken en kopiëren** in de lokale webgebruikersinterface van de Data Box Heavy.
3. Gebruik de opdracht `cp` of `rsync` om de gegevens te kopiëren. 
4. Herhaal deze stappen om verbinding te maken en gegevens te kopiëren naar het tweede knooppunt van uw Data Box Heavy.

Ga voor stapsgewijze instructies naar [Zelfstudie: Gegevens kopiëren naar Azure Data Box via NFS](data-box-heavy-deploy-copy-data-via-nfs.md).

### <a name="copy-data-via-rest"></a>Gegevens kopiëren via REST

1. Als u gegevens wilt kopiëren met behulp van Data Box-Blob-opslag via REST API's, kunt u verbinding maken via *http* of *https*.
2. Als u gegevens wilt kopiëren naar Data Box-Blob-opslag, kunt u AzCopy gebruiken.
3. Herhaal deze stappen om verbinding te maken en gegevens te kopiëren naar het tweede knooppunt van uw Data Box Heavy.

Ga voor stapsgewijze instructies naar [Zelfstudie: Gegevens kopiëren naar Azure Data Box-Blob-opslag via REST API's](data-box-heavy-deploy-copy-data-via-rest.md).

### <a name="copy-data-via-data-copy-service"></a>Gegevens kopiëren via een gegevenskopieerservice

1. U moet een taak maken als u gegevens wilt kopiëren met behulp van de gegevenskopieerservice. Ga in de lokale webgebruikersinterface van uw Data Box Heavy naar **Beheren > Gegevens kopiëren > Maken**.
2. Vul de parameters in en maak een taak.
3. Herhaal deze stappen om verbinding te maken en gegevens te kopiëren naar het tweede knooppunt van uw Data Box Heavy.

Ga voor stapsgewijze instructies naar [Zelfstudie: De gegevenskopieerservice gebruiken om gegevens te kopiëren naar Azure Data Box Heavy](data-box-heavy-deploy-copy-data-via-copy-service.md).

### <a name="copy-data-to-managed-disks"></a>Gegevens kopiëren naar beheerde schijven

1. Wanneer u het Data Box Heavy-apparaat bestelt, moet u beheerde schijven als opslaglocatie hebben geselecteerd.
2. U kunt verbinding maken met Data Box Heavy via SMB- of NFS-shares.
3. Vervolgens kunt u gegevens kopiëren via SMB- of NFS-hulpprogramma's.
4. Herhaal deze stappen om verbinding te maken en gegevens te kopiëren naar het tweede knooppunt van uw Data Box Heavy.

Ga voor stapsgewijze instructies naar [Zelfstudie: Data Box Heavy gebruiken om gegevens als beheerde schijven in Azure te importeren](data-box-heavy-deploy-copy-data-from-vhds.md).

::: zone-end


