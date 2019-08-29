---
title: Zelf studie voor het kopiëren van gegevens naar Azure Data Box Disk | Microsoft Docs
description: In deze zelfstudie leest u hoe u gegevens kopieert naar uw Azure Data Box-schijf
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: a0c34e30e52bd2a6d57e2cf8299f231f7f2960d9
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147946"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Zelfstudie: Gegevens kopiëren naar Azure Data Box Disk en deze gegevens controleren

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box-disk-and-validate"></a>Gegevens kopiëren naar Azure Data Box Disk en valideren

Nadat de schijven zijn verbonden en ontgrendeld, kunt u gegevens van de bron gegevens server naar uw schijven kopiëren. Wanneer het kopiëren van de gegevens is voltooid, moet u de gegevens die u hebt gekopieerd. De validatie zorgt ervoor dat de gegevens later naar Azure kunnen worden geüpload.

::: zone-end

::: zone target="docs"

In deze zelfstudie wordt beschreven hoe u vanaf uw hostcomputer gegevens kopieert en vervolgens controlesommen genereert om de gegevensintegriteit te controleren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gegevens kopiëren naar de Data Box-schijf
> * Gegevens controleren

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:
- U hebt de zelfstudie [ uw Azure Data Box Disk installeren en configureren](data-box-disk-deploy-set-up.md) voltooid.
- Uw schijven worden ontgrendeld en verbonden met een clientcomputer.
- Er moet een [ondersteund besturingssysteem](data-box-disk-system-requirements.md##supported-operating-systems-for-clients) worden uitgevoerd op de clientcomputer die wordt gebruikt om gegevens naar de schijven te kopiëren.
- Zorg ervoor dat het beoogde opslagtype voor uw gegevens overeenkomt met [Ondersteunde opslagtypen](data-box-disk-system-requirements.md#supported-storage-types-for-upload).
- Bekijk de [limieten voor beheerde schijven in azure-object grootte limieten](data-box-disk-limits.md#azure-object-size-limits).


## <a name="copy-data-to-disks"></a>Gegevens naar schijven kopiëren

Bekijk de volgende overwegingen voordat u de gegevens naar de schijven kopieert:

- Het is uw verantwoordelijkheid om ervoor te zorgen dat u de gegevens kopieert naar mappen met de juiste gegevensindeling. U moet bijvoorbeeld de blok-blobgegevens naar de map voor blok-blobs kopiëren. Als de gegevensindeling niet overeenkomt met de betreffende map (opslagtype), zal de gegevensupload naar Azure op een later tijdstip mislukken.
- Zorg er tijdens het kopiëren van gegevens voor dat de gegevensgrootte voldoet aan de limieten die staan beschreven in de [limieten voor Azure-opslag en Data Box-schijven](data-box-disk-limits.md).
- Als de gegevens die door Data Box Disk worden geüpload gelijktijdig door andere toepassingen buiten Data Box Disk worden geüpload, kan dit tot fouten voor de uploadtaak en beschadigde gegevens leiden.

   > [!IMPORTANT]
   >  Als u beheerde schijven hebt opgegeven als een van de opslag doelen tijdens het maken van de order, is de volgende sectie van toepassing.

- U kunt slechts één beheerde schijf met een opgegeven naam in een resource groep hebben over alle voorgemaakte mappen en in alle Data Box Disk. Dit betekent dat de Vhd's die zijn geüpload naar de voorgemaakte mappen unieke namen moeten hebben. Zorg ervoor dat de opgegeven naam niet overeenkomt met een bestaande beheerde schijf in een resource groep. Als Vhd's dezelfde naam hebben, wordt er slechts één VHD geconverteerd naar een beheerde schijf met die name. De andere Vhd's worden als pagina-blobs geüpload naar het staging Storage-account.
- Kopieer de Vhd's altijd naar een van de voorgemaakte mappen. Als u de Vhd's buiten deze mappen of in een map die u hebt gemaakt kopieert, worden de Vhd's geüpload naar Azure Storage-account als pagina-blobs en niet-beheerde schijven.
- Alleen de vaste Vhd's kunnen worden geüpload om beheerde schijven te maken. Dynamische Vhd's, differentiërende Vhd's of VHDX-bestanden worden niet ondersteund.


Voer de volgende stappen uit om verbinding te maken en gegevens van uw computer naar de Data Box-schijf te kopiëren.

1. Geef de inhoud van het ontgrendelde station weer. De lijst met voorgemaakte mappen en submappen in het station verschilt, afhankelijk van de opties die zijn geselecteerd bij het plaatsen van de Data Box Disk order.

    |Geselecteerde opslag bestemming  |Type opslagaccount|Type staging Storage-account |Mappen en submappen  |
    |---------|---------|---------|------------------|
    |Storage-account     |GPv1 of GPv2                 | N.v.t. | BlockBlob <br> PageBlob <br> AzureFile        |
    |Storage-account     |Blob-opslag account         | N.v.t. | BlockBlob        |
    |Managed Disks     |N.v.t. | GPv1 of GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |Storage-account <br> Managed Disks     |GPv1 of GPv2 | GPv1 of GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |Storage-account <br> Managed Disks    |Blob-opslag account | GPv1 of GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    Hieronder ziet u een voor beeld van een scherm opname van een volg orde waarin een GPv2-opslag account is opgegeven:

    ![De inhoud van het schijf station](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Kopieer de gegevens die moeten worden geïmporteerd als blok-blobs in naar de map *BlockBlob* . Kopieer op dezelfde manier gegevens als VHD/VHDX naar *PageBlob* map en gegevens in de map *AzureFile* .

    Er wordt voor elke submap onder de BlockBlob- en PageBlob-mappen een container gemaakt in het Azure-opslagaccount. Alle bestanden onder de mappen BlockBlob en PageBlob worden naar een standaardcontainer `$root` onder het Azure-opslagaccount gekopieerd. Bestanden in de `$root`-container worden altijd als blok-blobs geüpload.

   Kopieer bestanden naar een map in de map *AzureFile* . Een submap in de map *AzureFile* maakt een file share. Bestanden die rechtstreeks naar de map *AzureFile* worden gekopieerd, mislukken en worden geüpload als blok-blobs.

    Als er in de hoofdmap bestanden en mappen voorkomen, moet u deze naar een andere map verplaatsen voordat u met het kopiëren van gegevens begint.

    > [!IMPORTANT]
    > Alle containers, blobs en bestands namen moeten voldoen aan de [naamgevings conventies van Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Als u zich niet aan deze regels houdt, mislukt de gegevensupload naar Azure.

3. Zorg er bij het kopiëren van bestanden voor dat bestanden niet groter zijn dan ~ 4,7 TiB voor blok-blobs, ~ 8 TiB voor pagina-blobs en ~ 1 TiB voor Azure Files. 
4. U kunt door middel van slepen en neerzetten de gegevens vanuit Verkenner kopiëren. U kunt ook elk programma voor het kopiëren van bestanden dat compatibel is met SMB, zoals Robocopy, gebruiken om de gegevens te kopiëren. Er kunnen meerdere kopieertaken worden gestart met de volgende Robocopy-opdracht:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    De parameters en opties voor de opdracht worden in de volgende tabel weergegeven:
    
    |Parameters/opties  |Description |
    |--------------------|------------|
    |Source            | Hiermee geeft u het pad naar de bronmap op.        |
    |Bestemming       | Hiermee geeft u het pad naar de doelmap op.        |
    |/E                  | Hiermee kopieert u submappen, met inbegrip van lege mappen. |
    |/MT[:N]             | Hiermee maakt u kopieën met meerdere (N) threads, waarbij N een geheel getal tussen 1 en 128 is. <br>De standaardwaarde voor N is 8.        |
    |/R: \<N>             | Hiermee geeft u het aantal nieuwe pogingen bij mislukte kopieerbewerkingen op. De standaardwaarde van N is 1.000.000 (één miljoen nieuwe pogingen).        |
    |/W: \<N>             | Hiermee geeft u de wachttijd tussen nieuwe pogingen op in seconden. De standaardwaarde van N is 30 (wachttijd 30 seconden).        |
    |/NFL                | Hiermee geeft u op dat bestandsnamen niet moeten worden vastgelegd.        |
    |/NDL                | Hiermee geeft u op dat mapnamen niet moeten worden vastgelegd.        |
    |/FFT                | Hiermee wordt uitgegaan van FAT-bestandstijden (precisie van twee seconden).        |
    |/Log:\<logboek bestand >     | Hiermee schrijft u de statusuitvoer naar het logboekbestand (en overschrijft u het bestaande logboekbestand).         |

    Er kunnen gelijktijdig meerdere schijven worden gebruikt terwijl er op elke schijf meerdere taken worden uitgevoerd.

6. Controleer de kopieerstatus wanneer de taak wordt uitgevoerd. In het volgende voorbeeld ziet u de uitvoer van de Robocopy-opdracht voor het kopiëren van bestanden naar de Data Box-schijf.

    ```
    C:\Users>robocopy
        -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:53 PM
           Simple Usage :: ROBOCOPY source destination /MIR
    
                 source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                   /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    Gebruik de volgende parameters in Robocopy om de prestaties te optimaliseren als u de gegevens kopieert.

    |    Platform    |    Voornamelijk kleine bestanden van < 512 KB                           |    Voornamelijk middelgrote bestanden van 512 KB - 1 MB                      |    Voornamelijk grote bestanden van > 1 MB                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    Data Box Disk        |    4 Robocopy-sessies* <br> 16 threads per sessie    |    2 Robocopy-sessies* <br> 16 threads per sessie    |    2 Robocopy-sessies* <br> 16 threads per sessie    |
    
    **Elke Robocopy-sessie kan maximaal 7.000 mappen en 150 miljoen bestanden hebben.*
    
    >[!NOTE]
    > De hierboven voorgestelde parameters zijn gebaseerd op de omgeving die wij hebben gebruikt voor interne tests.
    
    Ga voor meer informatie over opdrachten voor Robocopy naar [Robocopy en een paar voorbeelden](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

6. Open de doelmap om de gekopieerde bestanden weer te geven en te controleren. Als er fouten zijn opgetreden tijdens het kopiëren, downloadt u de logboekbestanden om de problemen op te lossen. De logboekbestanden bevinden zich op de locatie die in de Robocopy-opdracht is aangegeven.
 
### <a name="split-and-copy-data-to-disks"></a>Gegevens splitsen en kopiëren naar schijven

U kunt deze optionele procedure gebruiken als u meerdere schijven gebruikt en u over een grote gegevensset beschikt die moet worden gesplitst en gekopieerd naar alle schijven. Met de Split Copy Tool van Data Box kunt u de gegevens op een Windows-computer splitsen en kopiëren.

>[!IMPORTANT]
> De Data Box Split Copy tool valideert ook uw gegevens. Als u de Data Box Split Copy tool gebruikt om gegevens te kopiëren, kunt u de [validatiestap](#validate-data) overslaan.
> Het hulp programma voor het splitsen van kopiëren wordt niet ondersteund voor beheerde schijven.

1. Zorg er op uw Windows-computer voor dat u de Split Copy Tool van Data Box hebt gedownload en uitgepakt in een lokale map. Het hulpprogramma is gedownload toen u de werkset voor de Data Box-schijf voor Windows downloadde.
2. Open Verkenner. Maak een notitie van het gegevensbronstation en welke stationsletters zijn toegewezen aan de Data Box-schijf. 

     ![Gegevens splitsen en kopiëren](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Zoek de brongegevens die u wilt kopiëren. In dit geval bijvoorbeeld:
    - De volgende blok-blobgegevens zijn geïdentificeerd.

         ![Gegevens splitsen en kopiëren](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - De volgende pagina-blobgegevens zijn geïdentificeerd.

         ![Gegevens splitsen en kopiëren](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Ga naar de map waarin de software is uitgepakt. Zoek het bestand `SampleConfig.json` in die map. Dit is een alleen-lezen-bestand dat u kunt wijzigen en opslaan.

   ![Gegevens splitsen en kopiëren](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. Wijzig het bestand `SampleConfig.json`.
 
   - Geef een taaknaam op. Hiermee maakt u een map in de Data Box-schijf. Uiteindelijk wordt dit de container in het Azure-opslagaccount dat is gekoppeld aan deze schijven. De taaknaam moet voldoen aan de naamgevingsconventies voor Azure-containers. 
   - Geef een bronpad op en noteer de padindeling in `SampleConfigFile.json`. 
   - Voer de stationsletters in voor de doelschijven. De gegevens worden via het bronpad verzameld en gekopieerd naar meerdere schijven.
   - Geef een pad op voor de logboekbestanden. Standaard worden deze naar de huidige map verzonden, waar het `.exe`-bestand zich bevindt.

     ![Gegevens splitsen en kopiëren](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. Ga naar `JSONlint` om de bestandsindeling te controleren. Sla het bestand op als `ConfigFile.json`. 

     ![Gegevens splitsen en kopiëren](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Open een opdrachtpromptvenster. 

8. Voer `DataBoxDiskSplitCopy.exe` uit. type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Gegevens splitsen en kopiëren](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Druk op Enter om door te gaan met het script.

    ![Gegevens splitsen en kopiëren](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. Wanneer de gegevensset is gesplitst en gekopieerd, wordt een overzicht van de Split Copy Tool over de kopieersessie weergegeven. Hieronder ziet u een voorbeeld van de uitvoer.

    ![Gegevens splitsen en kopiëren](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Controleer of de gegevens zijn gesplitst en over de doelschijven zijn verdeeld. 
 
    ![Gegevens splitsen en kopiëren](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![Gegevens splitsen en kopiëren](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Als u de inhoud van het station `n:` verder bekijkt, ziet u dat er twee submappen zijn gemaakt, een voor gegevens in de blok-blobindeling en een voor gegevens in de pagina-blobindeling.
    
     ![Gegevens splitsen en kopiëren](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Als het kopiëren mislukt, voert u een herstel uit en hervat u de bewerking. Gebruik de volgende opdracht:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

Als er fouten worden weer geven met het hulp programma splitsen, gaat u naar [problemen met het oplossen van fouten in het hulp programma splitsen](data-box-disk-troubleshoot-data-copy.md).

Nadat het kopiëren van gegevens voltooid is, kunt u doorgaan met het valideren van uw gegevens. Als u het hulpprogramma Split Copy gebruikt, slaat u de validatie over (Split Copy valideert ook) en gaat u door met de volgende zelfstudie.


## <a name="validate-data"></a>Gegevens valideren

Als u niet de Split Copy tool gebruikt hebt om gegevens te kopiëren, moet u uw gegevens valideren. Voer de volgende stappen uit om de gegevens te controleren.

1. Voer `DataBoxDiskValidation.cmd` uit in de map *DataBoxDiskImport* van het station om de controlesom te controleren.
    
    ![Uitvoer van validatieprogramma van Data Box Disk](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Kies de juiste optie. **Wij adviseren om altijd optie 2 te selecteren om de bestanden te valideren en controlesommen te genereren**. Afhankelijk van de gegevensgrootte kan deze stap enige tijd in beslag nemen. Als het script is voltooid, sluit u het opdrachtvenster. Als er fouten optreden tijdens de validatie en het genereren van de controlesom, krijgt u hiervan een melding en ziet u ook een koppeling naar de foutenlogboeken.

    ![Uitvoer van de controlesom](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Reset het hulpprogramma tussen twee uitvoeringen.
    > - Gebruik optie 1 als het gaat om een grote gegevensset met kleine bestanden (~ KB). Met deze optie worden alleen de bestanden gevalideerd, omdat het genereren van een controlesom erg lang kan duren en de prestaties dus tegenvallen.

3. Als u meerdere schijven gebruikt, voert u de opdracht uit voor elke schijf.

Als tijdens de validatie fouten worden weer geven, raadpleegt u [problemen met validatie fouten oplossen](data-box-disk-troubleshoot.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot de Azure Data Box-schijf, zoals:

> [!div class="checklist"]
> * Gegevens kopiëren naar de Data Box-schijf
> * De gegevensintegriteit controleren

Ga naar de volgende zelfstudie om te lezen hoe u de Data Box-schijf retourneert en de gegevensupload naar Azure controleert.

> [!div class="nextstepaction"]
> [Uw Azure Data Box-schijf retourneren naar Microsoft](./data-box-disk-deploy-picked-up.md)

::: zone-end

::: zone target="chromeless"

### <a name="copy-data-to-disks"></a>Gegevens naar schijven kopiëren

Voer de volgende stappen uit om verbinding te maken en gegevens te kopiëren van uw computer naar het Data Box Disk.

1. Geef de inhoud van het ontgrendelde station weer. De lijst met voorgemaakte mappen en submappen in het station verschilt, afhankelijk van de opties die zijn geselecteerd bij het plaatsen van de Data Box Disk order.
2. Kopieer de gegevens naar mappen die overeenkomen met de juiste gegevens indeling. Kopieer bijvoorbeeld de ongestructureerde gegevens naar de map voor *BlockBlob* -map-, VHD-of VHDX-gegevens naar *PageBlob* map en bestanden naar *AzureFile*. Als de gegevens indeling niet overeenkomt met de juiste map (opslag type), mislukt de gegevens in een latere stap naar Azure.

    - Zorg ervoor dat alle containers, blobs en bestanden voldoen aan de [Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) -naamgevings conventies en [Azure-object grootte limieten](data-box-disk-limits.md#azure-object-size-limits). Als deze regels of limieten niet worden gevolgd, mislukt het uploaden van gegevens naar Azure.     
    - Als uw bestelling Managed Disks als een van de opslag doelen, raadpleegt u de naamgevings conventies voor [beheerde schijven](data-box-disk-limits.md#managed-disk-naming-conventions).
    - Er wordt voor elke submap onder de BlockBlob- en PageBlob-mappen een container gemaakt in het Azure-opslagaccount. Alle bestanden onder *BlockBlob* -en *PageBlob* -mappen worden gekopieerd naar een standaard container $root onder het Azure Storage-account. Alle bestanden in de $root container worden altijd geüpload als blok-blobs.
    - Maak een submap in de map *AzureFile* . Deze submap wordt toegewezen aan een file share in de Cloud. Kopieer de bestanden naar de submap. Bestanden die rechtstreeks naar de map *AzureFile* worden gekopieerd, mislukken en worden geüpload als blok-blobs.
    - Als er in de hoofdmap bestanden en mappen voorkomen, moet u deze naar een andere map verplaatsen voordat u met het kopiëren van gegevens begint.

3. Gebruik slepen en neerzetten met bestanden Verkenner of een hulp programma voor het kopiëren van SMB-compatibele bestanden zoals Robocopy om uw gegevens te kopiëren. U kunt meerdere Kopieer taken starten met de volgende opdracht:

    ```
    Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt
    ```
4. Open de doelmap om de gekopieerde bestanden weer te geven en te controleren. Als er fouten zijn opgetreden tijdens het kopiëren, downloadt u de logboekbestanden om de problemen op te lossen. De logboekbestanden bevinden zich op de locatie die in de Robocopy-opdracht is aangegeven.

Gebruik de optionele procedure voor [splitsen en kopiëren](data-box-disk-deploy-copy-data.md#split-and-copy-data-to-disks) wanneer u meerdere schijven gebruikt en een grote gegevensset hebt die moet worden gesplitst en gekopieerd op alle schijven.

### <a name="validate-data"></a>Gegevens valideren

Voer de volgende stappen uit om uw gegevens te verifiëren.

1. Voer `DataBoxDiskValidation.cmd` uit in de map *DataBoxDiskImport* van het station om de controlesom te controleren.
2. Gebruik optie 2 om uw bestanden te valideren en controle sommen te genereren. Afhankelijk van de gegevensgrootte kan deze stap enige tijd in beslag nemen. Als er fouten optreden tijdens de validatie en het genereren van de controlesom, krijgt u hiervan een melding en ziet u ook een koppeling naar de foutenlogboeken.

    Zie [gegevens valideren](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-copy-data#validate-data)voor meer informatie over gegevens validatie. Zie [problemen oplossen met validatie fouten](https://docs.microsoft.com/en-us/azure/databox/data-box-disk-troubleshoot){: target = "_blank"} als u tijdens de validatie fouten ondervindt.

::: zone-end
