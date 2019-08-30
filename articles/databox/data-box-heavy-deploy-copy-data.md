---
title: Zelf studie voor het kopiëren van gegevens via SMB op Azure Data Box Heavy | Microsoft Docs
description: Meer informatie over het kopiëren van gegevens naar uw Azure Data Box Heavy via SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 8cb763766ebb151ad1c59b63a33a63493a4f0069
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164377"
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

U kunt gegevens van de bron server naar uw Data Box kopiëren via SMB, NFS, REST, Data Copy service of naar Managed disks.

Controleer in elk geval of de namen van de shares en mappen en de gegevens grootte voldoen aan de richt lijnen die zijn beschreven in de [Azure Storage-en data Box Heavy-service limieten](data-box-heavy-limits.md).

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de zelfstudie [ Azure Data Box Heavy](data-box-deploy-set-up.md)instellen.
2. U hebt uw Data Box Heavy ontvangen en de status van de bestelling in dePortal is afgeleverd.
3. U hebt een hostcomputer met de gegevens die u wilt kopiëren naar Data Box Heavy. Op uw hostcomputer moet
    - Een [ondersteund besturingssysteem](data-box-system-requirements.md) worden uitgevoerd.
    - Verbonden zijn met een netwerk met hoge snelheid. Voor de snelste Kopieer snelheden kunnen 2 40-GbE-verbindingen (één per knoop punt) parallel worden gebruikt. Als u niet beschikt over 40-GbE-verbinding, kunt u het beste ten minste 2 10 GbE-verbindingen (één per knoop punt) hebben.
   

## <a name="connect-to-data-box-heavy-shares"></a>Verbinding maken met Data Box Heavy-shares

Op basis van het geselecteerde opslag account, maakt Data Box Heavy het volgende:
- Drie shares voor elk gekoppeld opslagaccount voor GPv1 en GPv2.
- Eén share voor Premium-opslag.
- Eén share voor het Blob Storage-account.

Deze shares worden gemaakt op beide knoop punten van het apparaat.

Onder blok-Blob en shares voor pagina-blobs:
- Entiteiten van het hoogste niveau zijn containers.
- Entiteiten van het tweede niveau zijn Blobs.

Onder shares voor Azure Files:
- Entiteiten van het hoogste niveau zijn shares.
- Entiteiten van het tweede niveau zijn bestanden.

De volgende tabel bevat het UNC-pad naar de shares op uw Data Box Heavy en Azure Storage pad-URL waarnaar de gegevens worden geüpload. De uiteindelijke URL van het Azure Storage-pad kan worden afgeleid van het UNC-pad naar de shares.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blok-blobs | <li>UNC-pad naar shares: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-pagina-blobs  | <li>UNC-pad naar shares: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-pad naar shares: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

De stappen om verbinding te maken met behulp van een Windows-of Linux-client verschillen.

> [!NOTE]
> Volg dezelfde stappen om gelijktijdig verbinding te maken met de knoop punten van het apparaat.

### <a name="connect-on-a-windows-system"></a>Verbinding maken op een Windows-systeem

Als u een Windows Server-hostcomputer gebruikt, voert u de volgende stappen uit om verbinding te maken met de Data Box Heavy.

1. U moet eerst een verificatie uitvoeren en een sessie starten. Ga naar **Verbinding maken en kopiëren**. Klik op **Referenties ophalen** om de toegangsreferenties te krijgen voor de shares die aan uw opslagaccount zijn gekoppeld.

    ![Sharereferenties 1 ophalen](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. Kopieer in het dialoogvenster Verbinding maken met share en gegevens kopiëren de **Gebruikersnaam** en het **Wachtwoord** voor de share. Klik op **OK**.
    
    ![Sharereferenties 1 ophalen](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Als u toegang wilt krijgen tot de shares die zijn gekoppeld aan uw opslag account (*databoxe2etest* in het volgende voor beeld), opent u een opdracht venster. Typ in de opdrachtprompt:

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

De `vers` para meter is de versie van SMB die uw Linux-host ondersteunt. Sluit de juiste versie in de bovenstaande opdracht.

Zie [ondersteunde bestands systemen voor Linux-clients](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients)voor versies van SMB die door de data Box Heavy worden ondersteund.

## <a name="copy-data-to-data-box-heavy"></a>Gegevens kopiëren naar Data Box Heavy

Zodra u verbonden bent met de Data Box Heavy shares, is de volgende stap het kopiëren van gegevens.

### <a name="copy-considerations"></a>Aandachtspunten voor kopiëren

Neem de volgende punten door voordat u gegevens gaat kopiëren:

- Zorg dat u de gegevens kopieert naar shares die overeenkomen met de juiste gegevensindeling. U moet bijvoorbeeld de blok-blobgegevens naar de share voor blok-blobs kopiëren. Kopieer de VHD's naar pagina-blob.

    Als de gegevensindeling niet overeenkomt met het betreffende sharetype, zal het uploaden van gegevens naar Azure op een later tijdstip mislukken.
-  Zorg er bij het kopiëren van gegevens voor dat de gegevens grootte voldoet aan de grootte limieten die zijn beschreven in de [Azure Storage-en data Box Heavy](data-box-heavy-limits.md)-limieten.
- Als gegevens, die door Data Box Heavy worden geüpload, gelijktijdig worden geüpload door andere toepassingen buiten Data Box Heavy, kan dit leiden tot fouten bij het uploaden van taken en gegevens beschadiging.
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
    
    |Kenmerk  |Description  |
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
    |logboek +:\<logfile >| Voegt de uitvoer toe aan het bestaande logboekbestand.|
    
 
    In het volgende voor beeld ziet u de uitvoer van de Robocopy-opdracht om bestanden te kopiëren naar de Data Box Heavy.

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

2. Gebruik de volgende parameters in Robocopy om de prestaties te optimaliseren als u de gegevens kopieert. (De getallen hieronder vertegenwoordigen de aanbevolen scenario's.)

    | Platform    | Voornamelijk kleine bestanden van < 512 KB    | Voornamelijk middel grote bestanden 512 KB-1 MB  | Voornamelijk grote bestanden van > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 Robocopy-sessies <br> 24 threads per sessie | 6 Robocopy-sessies <br> 16 threads per sessie | 6 Robocopy-sessies <br> 16 threads per sessie |


    Ga voor meer informatie over opdrachten voor Robocopy naar [Robocopy en een paar voorbeelden](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

3. Open de doelmap om de gekopieerde bestanden weer te geven en te controleren.

    ![Gekopieerde bestanden weer geven](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Wanneer de gegevens worden gekopieerd:

    - De bestands namen, grootten en indelingen worden gevalideerd om ervoor te zorgen dat ze voldoen aan de Azure-object-en opslag limieten, evenals de naamgevings conventies voor Azure-bestanden en-containers.
    - Om de gegevens integriteit te waarborgen, wordt de controlesom ook berekend op inline.

    Download de foutbestanden om problemen op te lossen als er fouten zijn opgetreden tijdens het kopiëren. Selecteer het pijl pictogram om de fout bestanden te downloaden.

    ![Fout bestanden downloaden](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Zie voor meer informatie [fouten logboeken weer geven tijdens het kopiëren van gegevens naar Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). Zie [problemen met data Box Heavy oplossen](data-box-troubleshoot.md)voor een gedetailleerde lijst met fouten tijdens het kopiëren van gegevens.

5. Open het fout bestand in Klad blok. Het volgende fout bestand geeft aan dat de gegevens niet correct zijn uitgelijnd.

    ![Fout bestand openen](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Voor een pagina-BLOB moeten de gegevens 512 bytes zijn uitgelijnd. Nadat deze gegevens zijn verwijderd, wordt de fout opgelost zoals wordt weer gegeven in de volgende scherm afbeelding.

    ![Fout opgelost](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Nadat de kopie is voltooid, gaat u naar de pagina **dash board weer geven** . Controleer de gebruikte ruimte en de beschik bare ruimte op het apparaat.
    
    ![Vrije en ongebruikte ruimte verifiëren op het dashboard](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Herhaal de bovenstaande stappen om gegevens te kopiëren naar het tweede knoop punt van het apparaat.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd over Azure Data Box Heavy-onderwerpen, zoals:

> [!div class="checklist"]
> * Verbinding maken met Data Box Heavy
> * Gegevens kopiëren naar Data Box Heavy


Ga naar de volgende zelf studie voor meer informatie over het verzenden van Data Box Heavy terug naar micro soft.

> [!div class="nextstepaction"]
> [Uw Azure Data Box Heavy naar micro soft verzenden](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>Gegevens kopiëren via SMB

1. Als u een Windows-host gebruikt, gebruikt u de volgende opdracht om verbinding te maken met de SMB-shares:

    `\\<IP address of your device>\ShareName`

2. Als u de toegangsreferenties voor de shares wilt ophalen, gaat u naar de pagina **Verbinding maken en kopiëren** in de lokale webgebruikersinterface van de Data Box.

3. Gebruik een met SMB compatibel hulp programma voor het kopiëren van bestanden zoals Robocopy voor het kopiëren van gegevens naar shares.

Ga naar de volgende [zelf studie voor stapsgewijze instructies: Gegevens kopiëren naar Azure Data Box via SMB](data-box-heavy-deploy-copy-data.md).

### <a name="copy-data-via-nfs"></a>Gegevens kopiëren via NFS

1. Als u een NFS-host gebruikt, gebruikt u de volgende opdracht om de NFS-shares te koppelen:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Als u de toegangs referenties voor de share wilt ophalen, gaat u naar de **pagina verbinding maken & kopiëren** in de lokale webgebruikersinterface van de data Box Heavy.
3. Gebruik `cp` of`rsync` opdracht om uw gegevens te kopiëren. 
4. Herhaal deze stappen om verbinding te maken en gegevens te kopiëren naar het tweede knoop punt van uw Data Box Heavy.

Ga naar de volgende [zelf studie voor stapsgewijze instructies: Gegevens kopiëren naar Azure Data Box via NFS](data-box-heavy-deploy-copy-data-via-nfs.md).

### <a name="copy-data-via-rest"></a>Gegevens kopiëren via REST

1. Als u gegevens wilt kopiëren met behulp van Data Box Blob-opslag via REST Api's, kunt u verbinding maken via *http* of *https*.
2. Als u gegevens wilt kopiëren naar Data Box Blob-opslag, kunt u AzCopy gebruiken.
3. Herhaal deze stappen om verbinding te maken en gegevens te kopiëren naar het tweede knoop punt van uw Data Box Heavy.

Ga naar de volgende [zelf studie voor stapsgewijze instructies: Gegevens kopiëren naar Azure Data Box Blob-opslag via REST](data-box-heavy-deploy-copy-data-via-rest.md)api's.

### <a name="copy-data-via-data-copy-service"></a>Gegevens kopiëren via de Data Copy-service

1. Als u gegevens wilt kopiëren met behulp van de Data Copy-service, moet u een taak maken. Ga in de lokale web-UI van uw Data Box Heavy naar **beheer > gegevens kopiëren > maken**.
2. Vul de para meters in en maak een taak.
3. Herhaal deze stappen om verbinding te maken en gegevens te kopiëren naar het tweede knoop punt van uw Data Box Heavy.

Ga naar de volgende [zelf studie voor stapsgewijze instructies: Gebruik de Data Copy-service om gegevens te kopiëren](data-box-heavy-deploy-copy-data-via-copy-service.md)naar Azure data Box Heavy.

### <a name="copy-data-to-managed-disks"></a>Gegevens kopiëren naar Managed disks

1. Wanneer u het Data Box Heavy apparaat bestelt, moet u beheerde schijven als uw opslag bestemming hebben geselecteerd.
2. U kunt verbinding maken met Data Box Heavy via SMB-of NFS-shares.
3. U kunt vervolgens gegevens kopiëren via SMB-of NFS-hulpprogram ma's.
4. Herhaal deze stappen om verbinding te maken en gegevens te kopiëren naar het tweede knoop punt van uw Data Box Heavy.

Ga naar de volgende [zelf studie voor stapsgewijze instructies: Gebruik Data Box om gegevens te importeren als beheerde schijven in azure](data-box-heavy-deploy-copy-data-from-vhds.md).

::: zone-end


