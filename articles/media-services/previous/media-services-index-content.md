---
title: Mediabestanden indexeren met Azure Media Indexer
description: Azure Media Indexer stelt u in staat om inhoud van uw mediabestanden doorzoekbaar te maken en een volledige teksttranscriptie te genereren voor ondertiteling en trefwoorden. In dit onderwerp wordt uitgelegd hoe u Media Indexer gebruikt.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 7ccc2d5956b44a8cd85f19e0905539c32f58bc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163995"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Mediabestanden indexeren met Azure Media Indexer

> [!NOTE]
> De **mediaprocessor azure media-indexer** wordt buiten gebruik gesteld. Zie voor de pensioendata dit [onderwerp met oudere onderdelen.](legacy-components.md) [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) vervangt deze verouderde mediaprocessor. Zie [Migreren van Azure Media Indexer en Azure Media Indexer 2 naar Azure Media Services Video Indexer](migrate-indexer-v1-v2.md)voor meer informatie.

Azure Media Indexer stelt u in staat om inhoud van uw mediabestanden doorzoekbaar te maken en een volledige teksttranscriptie te genereren voor ondertiteling en trefwoorden. U één mediabestand of meerdere mediabestanden in een batch verwerken.  

Bij het indexeren van inhoud moet u mediabestanden gebruiken met duidelijke spraak (zonder achtergrondmuziek, ruis, effecten of een sisser van de microfoon). Enkele voorbeelden van de juiste inhoud zijn: opgenomen vergaderingen, lezingen of presentaties. De volgende inhoud is mogelijk niet geschikt voor indexering: films, tv-programma's, alles met gemengde audio- en geluidseffecten, slecht opgenomen inhoud met achtergrondgeluid (gesis).

Een indexeringstaak kan de volgende uitvoer genereren:

* Bestanden met ondertiteling in de volgende indelingen: **TTML**en **WebVTT**.
  
    Bestanden met ondertiteling bevatten een tag met de naam Herkenbaarheid, die een indexeringstaak scoort op basis van hoe herkenbaar de spraak in de bronvideo is.  U de waarde van Recognizeability gebruiken om uitvoerbestanden te screenen voor bruikbaarheid. Een lage score zou betekenen slechte indexering resultaten als gevolg van audiokwaliteit.
* Trefwoordbestand (XML).

In dit artikel ziet u hoe u indexeringstaken maakt om een asset te **indexeren** en **meerdere bestanden te indexeren**.

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Configuratie- en manifestbestanden gebruiken voor indexeringstaken
U meer details voor uw indexeringstaken opgeven met behulp van een taakconfiguratie. U bijvoorbeeld opgeven welke metagegevens u moet gebruiken voor uw mediabestand. Deze metadata wordt gebruikt door de taalengine om zijn woordenschat uit te breiden en verbetert de nauwkeurigheid van de spraakherkenning aanzienlijk.  U ook de gewenste uitvoerbestanden opgeven.

U ook meerdere mediabestanden tegelijk verwerken met behulp van een manifestbestand.

Zie [Taakvoorinstelling voor Azure Media Indexer voor](https://msdn.microsoft.com/library/dn783454.aspx)meer informatie .

## <a name="index-an-asset"></a>Een actief indexeren
Met de volgende methode wordt een mediabestand als een actief geüpload en wordt een taak gemaakt om het actief te indexeren.

Als er geen configuratiebestand is opgegeven, wordt het mediabestand geïndexeerd met alle standaardinstellingen.

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
```

<!-- __ -->
### <a name="output-files"></a><a id="output_files"></a>Uitvoerbestanden
Standaard genereert een indexeringstaak de volgende uitvoerbestanden. De bestanden worden opgeslagen in het eerste uitvoeritem.

Wanneer er meer dan één invoermediabestand is, genereert Indexer een manifestbestand voor de taakuitvoer, genaamd 'JobResult.txt'. Voor elk invoermediabestand worden de resulterende TTML-, WebVTT- en trefwoordbestanden achtereenvolgens genummerd en benoemd met de naam 'Alias'.

| Bestandsnaam | Beschrijving |
| --- | --- |
| **InvoerFileName.ttml**<br/>**InputFileName.vtt** |CC-bestanden (Closed Caption) in TTML- en WebVTT-indelingen.<br/><br/>Ze kunnen worden gebruikt om audio- en videobestanden toegankelijk te maken voor mensen met een gehoorbeperking.<br/><br/>Ondertitelingsbestanden bevatten een tag met de naam <b>Herkenbaarheid</b> die een indexeringstaak scoort op basis van hoe herkenbaar de spraak in de bronvideo is.  U de waarde van <b>Recognizeability</b> gebruiken om uitvoerbestanden te screenen voor bruikbaarheid. Een lage score zou betekenen slechte indexering resultaten als gevolg van audiokwaliteit. |
| **InvoerFileName.kw.xml<br/>InputFileName.info** |Trefwoord- en infobestanden. <br/><br/>Trefwoordbestand is een XML-bestand dat trefwoorden bevat die zijn geëxtraheerd uit de spraakinhoud, met frequentie- en verschuivingsinformatie. <br/><br/>Infobestand is een bestand met platte tekst dat gedetailleerde informatie bevat over elke herkende term. De eerste regel is speciaal en bevat de Score van de Herkenbaarheid. Elke volgende regel is een lijst met de volgende gegevens: begintijd, eindtijd, woord/zin, vertrouwen. De tijden worden gegeven in seconden en het vertrouwen wordt gegeven als een nummer van 0-1. <br/><br/>Voorbeeldregel: "1.20 1.45 woord 0.67" <br/><br/>Deze bestanden kunnen worden gebruikt voor een aantal doeleinden, zoals spraakanalyses of blootgesteld aan zoekmachines zoals Bing, Google of Microsoft SharePoint om de mediabestanden beter vindbaar te maken of zelfs om relevantere advertenties weer te geven. |
| **JobResult.txt** |Uitvoermanifest, alleen aanwezig bij het indexeren van meerdere bestanden, met de volgende informatie:<br/><br/><table border="1"><tr><th>Invoerbestand</th><th>Alias</th><th>MediaLengte</th><th>Fout</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Als niet alle invoermediabestanden met succes worden geïndexeerd, mislukt de indexeringstaak met foutcode 4000. Zie [Foutcodes](#error_codes)voor meer informatie .

## <a name="index-multiple-files"></a>Meerdere bestanden indexeren
Met de volgende methode worden meerdere mediabestanden als een asset geüpload en wordt een taak gemaakt om al deze bestanden in een batch te indexeren.

Een manifestbestand met de extensie ".lst" wordt gemaakt en geüpload naar het actief. Het manifestbestand bevat de lijst met alle activabestanden. Zie [Taakvoorinstelling voor Azure Media Indexer voor](https://msdn.microsoft.com/library/dn783454.aspx)meer informatie .

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>Gedeeltelijk opgevolgd taak
Als niet alle invoermediabestanden met succes worden geïndexeerd, mislukt de indexeringstaak met foutcode 4000. Zie [Foutcodes](#error_codes)voor meer informatie .

Dezelfde outputs (als opgevolgd taken) worden gegenereerd. U verwijzen naar het uitvoermanifestbestand om erachter te komen welke invoerbestanden zijn mislukt, afhankelijk van de waarden van de kolom Fout. Voor invoerbestanden die zijn mislukt, worden de resulterende TTML-, WebVTT- en trefwoordbestanden NIET gegenereerd.

### <a name="task-preset-for-azure-media-indexer"></a><a id="preset"></a>Taakvoorinstelling voor Azure Media Indexer
De verwerking van Azure Media Indexer kan worden aangepast door naast de taak een optionele taakvoorinstelling op te geven.  Het volgende beschrijft de indeling van deze configuratie xml.

| Name | Require | Beschrijving |
| --- | --- | --- |
| **Input** |false |Assetbestand(en) dat u wilt indexeren.</p><p>Azure Media Indexer ondersteunt de volgende mediabestandsindelingen: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>U de bestandsnaam(s) opgeven in de **naam** of **het lijstkenmerk** van het **invoerelement** (zoals hieronder wordt weergegeven). Als u niet opgeeft welk actiefbestand moet worden geïndexeerd, wordt het primaire bestand gekozen. Als er geen primaire assetbestand is ingesteld, wordt het eerste bestand in het invoerelement geïndexeerd.</p><p>Ga als volgt te werk om de naam van het assetbestand expliciet op te geven:<br/>`<input name="TestFile.wmv">`<br/><br/>U ook meerdere assetbestanden tegelijk indexeren (maximaal 10 bestanden). Om dit te doen:<br/><br/><ol class="ordered"><li><p>Maak een tekstbestand (manifestbestand) en geef het een .lst-extensie. </p></li><li><p>Voeg een lijst met alle namen van assetbestanden in uw invoerelement toe aan dit manifestbestand. </p></li><li><p>Voeg het manifestbestand toe aan het item.Add (upload) the manifest file to the asset.  </p></li><li><p>Geef de naam op van het manifestbestand in het lijstkenmerk van de invoer.<br/>`<input list="input.lst">`</li></ol><br/><br/>Opmerking: Als u meer dan 10 bestanden aan het manifestbestand toevoegt, mislukt de indexeringstaak met de foutcode van 2006. |
| **Metagegevens** |false |Metagegevens voor het opgegeven assetbestand(en) dat wordt gebruikt voor woordenschataanpassing.  Handig om Indexer voor te bereiden op het herkennen van niet-standaard woordenschatwoorden zoals de juiste zelfstandige naamwoorden.<br/>`<metadata key="..." value="..."/>` <br/><br/>U **waarden** leveren voor vooraf gedefinieerde **sleutels.** Momenteel worden de volgende toetsen ondersteund:<br/><br/>"titel" en "beschrijving" - gebruikt voor woordenschat aanpassing aan het taalmodel voor uw werk te tweaken en de nauwkeurigheid van spraakherkenning te verbeteren.  De waarden zaaien internet zoekt naar contextueel relevante tekstdocumenten, met behulp van de inhoud om het interne woordenboek te vergroten voor de duur van uw indexeringstaak.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Functies** <br/><br/> Toegevoegd in versie 1.2. Momenteel is de enige ondersteunde functie spraakherkenning ('ASR'). |false |De functie Spraakherkenning heeft de volgende instellingentoetsen:<table><tr><th><p>Sleutel</p></th>        <th><p>Beschrijving</p></th><th><p>Voorbeeldwaarde</p></th></tr><tr><td><p>Taal</p></td><td><p>De natuurlijke taal die moet worden herkend in het multimediabestand.</p></td><td><p>Engels, Spaans</p></td></tr><tr><td><p>Bijschriftindelingen</p></td><td><p>een puntkomma-gescheiden lijst van de gewenste uitvoerbijschriftindelingen (indien aanwezig)</p></td><td><p>ttml;webvtt</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>Waar; Valse</p></td></tr><tr><td><p>Trefwoorden genereren</p></td><td><p>Een booleaanse vlag die aangeeft of een XML-trefwoordbestand vereist is.</p></td><td><p>Waar; Valse. </p></td></tr><tr><td><p>ForceFullCaption (ForceFullCaption)</p></td><td><p>Een booleaanse vlag die aangeeft of volledige bijschriften (ongeacht het betrouwbaarheidsniveau) al dan niet moeten worden gedwongen.  </p><p>Standaard is onjuist, in welk geval woorden en zinnen met een betrouwbaarheidsniveau van minder dan 50% worden weggelaten uit de uitvoer van het eindbijschrift en vervangen door ellipsen ("...").  De ellipsen zijn handig voor kwaliteitscontrole en controle van bijschriften.</p></td><td><p>Waar; Valse. </p></td></tr></table> |

### <a name="error-codes"></a><a id="error_codes"></a>Foutcodes
In het geval van een fout moet Azure Media Indexer een van de volgende foutcodes rapporteren:

| Code | Name | Mogelijke redenen |
| --- | --- | --- |
| 2000 |Ongeldige configuratie |Ongeldige configuratie |
| 2001 |Ongeldige invoerelementen |Ontbrekende invoeractiva of lege activa. |
| 2002 |Ongeldig manifest |Manifest is leeg of manifest bevat ongeldige items. |
| 2003 |Kan mediabestand niet downloaden |Ongeldige URL in manifestbestand. |
| 2004 |Niet-ondersteund protocol |De URL van het protocol van media wordt niet ondersteund. |
| 2005 |Niet-ondersteund bestandstype |Het bestandstype voor invoermedia wordt niet ondersteund. |
| 2006 |Te veel invoerbestanden |Er zijn meer dan 10 bestanden in het invoermanifest. |
| 3000 |Kan mediabestand niet decoderen |Niet-ondersteunde mediacodec <br/>of<br/> Beschadigd mediabestand <br/>of<br/> Geen audiostream in invoermedia. |
| 4000 |Batchindexering gedeeltelijk geslaagd |Sommige van de invoermediabestanden kunnen niet worden geïndexeerd. Zie <a href="#output_files">Uitvoerbestanden voor</a>meer informatie . |
| andere |Interne fouten |Neem contact op met het ondersteuningsteam. indexer@microsoft.com |

## <a name="supported-languages"></a><a id="supported_languages"></a>Ondersteunde talen
Momenteel worden de Engelse en Spaanse talen ondersteund.  

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwante koppelingen
[Overzicht van Azure Media Services Analytics](media-services-analytics-overview.md)

[Mediabestanden indexeren met Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md)

