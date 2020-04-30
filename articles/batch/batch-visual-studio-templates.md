---
title: Oplossingen ontwikkelen met Visual Studio-sjablonen-Azure Batch | Microsoft Docs
description: Meer informatie over hoe u met Visual Studio project-sjablonen uw computerintensieve werk belastingen op Azure Batch kunt implementeren en uitvoeren.
ms.topic: article
ms.date: 02/27/2017
ms.custom: seodec18
ms.openlocfilehash: 8e8d5be4a9f0fb5482ba6c86a8766a25e5713c09
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117519"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Visual Studio-project sjablonen gebruiken om batch-oplossingen te starten

De **Visual Studio-sjablonen** van **taak beheer** en taak processor voor batch bevatten code waarmee u uw computerintensieve werk belastingen op batch kunt implementeren en uitvoeren met de minste inspanning. In dit document worden deze sjablonen beschreven en vindt u richt lijnen voor het gebruik ervan.

> [!IMPORTANT]
> In dit artikel worden alleen de gegevens van deze twee sjablonen beschreven en wordt ervan uitgegaan dat u bekend bent met de batch-service en de belangrijkste concepten die hieraan zijn gerelateerd: groepen, reken knooppunten, taken en taken, taak beheer taken, omgevings variabelen en andere relevante informatie. Meer informatie vindt u in [basis beginselen van Azure batch](batch-technical-overview.md) en [Batch functie overzicht voor ontwikkel aars](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Overzicht op hoog niveau
De sjablonen taak beheer en taak processor kunnen worden gebruikt voor het maken van twee nuttige onderdelen:

* Een taak beheer taak waarmee een taak splitsing kan worden opgesplitst in meerdere taken die onafhankelijk van elkaar kunnen worden uitgevoerd.
* Een taak processor die kan worden gebruikt voor het uitvoeren van voor bereiding en naverwerking rondom een opdracht regel van de toepassing.

In een scenario voor het weer geven van films zou de taak Splitser bijvoorbeeld één film taak omzetten in honderden of duizenden afzonderlijke taken die afzonderlijke frames afzonderlijk verwerken. De taak processor zou de rendering-toepassing en alle afhankelijke processen die nodig zijn om elk frame weer te geven, ook aanroepen, en eventuele aanvullende acties uitvoeren (bijvoorbeeld het gerenderde frame naar een opslag locatie kopiëren).

> [!NOTE]
> De sjablonen voor taak beheer en taak processor zijn onafhankelijk van elkaar, zodat u kunt kiezen of u beide wilt gebruiken, afhankelijk van de vereisten van uw reken taak en uw voor keuren.
> 
> 

Zoals u in het onderstaande diagram kunt zien, loopt een reken taak die gebruikmaakt van deze sjablonen drie fasen:

1. De client code (bijvoorbeeld toepassing, webservice, enz.) verzendt een taak naar de batch-service op Azure, waarbij de taak manager-taak het programma job manager wordt opgegeven.
2. De batch-service voert de taak job manager uit op een reken knooppunt en de taak Splitter Start het opgegeven aantal taak verwerkings taken, op zoveel reken knooppunten als vereist, op basis van de para meters en specificaties in de job splitter-code.
3. De taak verwerkings taken worden onafhankelijk uitgevoerd, parallel, om de invoer gegevens te verwerken en de uitvoer gegevens te genereren.

![Diagram waarin wordt weer gegeven hoe client code communiceert met de batch-service][diagram01]

## <a name="prerequisites"></a>Vereisten
Als u de batch-sjablonen wilt gebruiken, hebt u het volgende nodig:

* Een computer waarop Visual Studio 2015 is geïnstalleerd. Batch-sjablonen worden momenteel alleen ondersteund voor Visual Studio 2015.
* De batch-sjablonen, die beschikbaar zijn in de [Visual Studio-galerie][vs_gallery] als Visual Studio-uitbrei dingen. Er zijn twee manieren om de sjablonen op te halen:
  
  * De sjablonen installeren met behulp van het dialoog venster **extensies en updates** in Visual Studio (Zie [Visual Studio-uitbrei dingen zoeken en gebruiken][vs_find_use_ext]) voor meer informatie. Zoek en down load in het dialoog venster **extensies en updates** de volgende twee uitbrei dingen:
    
    * Taak beheer Azure Batch met taak splitsing
    * Azure Batch-taak processor
  * Down load de sjablonen uit de online galerie voor Visual Studio: [Microsoft Azure batch project sjablonen][vs_gallery_templates]
* Als u van plan bent de functie [toepassings pakketten](batch-application-packages.md) te gebruiken om de taak-en taak processor te implementeren op de batch Compute-knoop punten, moet u een opslag account koppelen aan uw batch-account.

## <a name="preparation"></a>Voorbereiding
We raden u aan om een oplossing te maken die uw taak manager en uw taak processor kan bevatten, omdat dit het gemakkelijker maakt om code te delen tussen uw taak beheer-en taak verwerkings Programma's. Voer de volgende stappen uit om deze oplossing te maken:

1. Open Visual Studio en selecteer **bestand** > **Nieuw** > **project**.
2. Onder **sjablonen**vouwt u **andere project typen**uit, klikt u op **Visual Studio-oplossingen**en selecteert u **lege oplossing**.
3. Typ een naam die uw toepassing beschrijft en het doel van deze oplossing (bijvoorbeeld ' LitwareBatchTaskPrograms ').
4. Klik op **OK**om de nieuwe oplossing te maken.

## <a name="job-manager-template"></a>Taak beheer sjabloon
De job manager-sjabloon helpt u bij het implementeren van een taak beheer taak die de volgende acties kan uitvoeren:

* Een taak in meerdere taken splitsen.
* Dien deze taken in om uit te voeren op batch.

> [!NOTE]
> Zie [overzicht van batch-functies voor ontwikkel aars](batch-api-basics.md#job-manager-task)voor meer informatie over taken van taak beheer.
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Een taak beheerder maken met behulp van de sjabloon
Als u een taak beheerder wilt toevoegen aan de oplossing die u eerder hebt gemaakt, voert u de volgende stappen uit:

1. Open uw bestaande oplossing in Visual Studio.
2. Klik in Solution Explorer met de rechter muisknop op de oplossing en vervolgens op**Nieuw project** **toevoegen** > .
3. Klik onder **Visual C#** op **Cloud**en klik vervolgens op **Azure batch job manager met Job splitter**.
4. Typ een naam die uw toepassing beschrijft en identificeert dit project als taak beheerder (bijvoorbeeld ' LitwareJobManager ').
5. Klik op **OK**om het project te maken.
6. Stel tot slot het project op om te forceren dat Visual Studio alle NuGet-pakketten waarnaar wordt verwezen, laadt en om te controleren of het project geldig is voordat u het gaat wijzigen.

### <a name="job-manager-template-files-and-their-purpose"></a>Taak beheer sjabloon bestanden en hun doel
Wanneer u een project maakt met behulp van de job manager-sjabloon, worden er drie groepen code bestanden gegenereerd:

* Het hoofd programma bestand (Program.cs). Dit bevat het programma-ingangs punt en de verwerking van uitzonde ringen op het hoogste niveau. Normaal gesp roken hoeft u dit niet te wijzigen.
* De Framework-map. Dit bevat de bestanden die verantwoordelijk zijn voor het ' standaard ' werk dat door het job manager-programma wordt uitgevoerd – het uitpakken van para meters, het toevoegen van taken aan de batch-taak, enzovoort. Normaal gesp roken hoeft u deze bestanden niet te wijzigen.
* Het taak splitter-bestand (JobSplitter.cs). Hier plaatst u uw toepassingsspecifieke logica voor het splitsen van een taak in taken.

Natuurlijk kunt u extra bestanden toevoegen die nodig zijn om uw job splitter-code te ondersteunen, afhankelijk van de complexiteit van de logica voor het splitsen van taken.

De sjabloon genereert ook standaard-.NET-project bestanden, zoals een. csproj-bestand, app. config, packages. config, enzovoort.

In de rest van deze sectie worden de verschillende bestanden en hun code structuur beschreven en wordt uitgelegd wat elke klasse doet.

![Visual Studio Solution Explorer de oplossing van de taak manager-sjabloon weer geven][solution_explorer01]

**Framework-bestanden**

* `Configuration.cs`: Het laden van taak configuratie gegevens, zoals Details van de batch-account, de referenties van een gekoppeld opslag account, taak-en taak gegevens en taak parameters. Het biedt ook toegang tot met batch gedefinieerde omgevings variabelen (Zie omgevings instellingen voor taken in de batch-documentatie) via de klasse Configuration. EnvironmentVariable.
* `IConfiguration.cs`: Abstracten de implementatie van de configuratie klasse, zodat u uw taak splitsing kunt testen met behulp van een configuratie object voor een valse of model.
* `JobManager.cs`: Coördineert de onderdelen van het programma taak beheer. Het is verantwoordelijk voor het initialiseren van de taak splitsing, het aanroepen van de taak splitsing en het verzenden van de taken die door de taak splitsing naar de taak indiener worden geretourneerd.
* `JobManagerException.cs`: Dit is een fout die vereist dat de taak beheerder wordt beëindigd. JobManagerException wordt gebruikt om ' verwachte ' fouten op te slaan waarbij specifieke diagnostische gegevens kunnen worden verstrekt als onderdeel van de beëindiging.
* `TaskSubmitter.cs`: Deze klasse is verantwoordelijk voor het toevoegen van taken die zijn geretourneerd door de taak splitter naar de batch-taak. De JobManager-klasse voegt de volg orde van taken in batches toe voor een efficiënte maar tijdige aanvulling op de taak en roept vervolgens TaskSubmitter. SubmitTasks aan op een achtergrond thread voor elke batch.

**Taak splitsen**

`JobSplitter.cs`: Deze klasse bevat toepassingsspecifieke logica voor het splitsen van de taak in taken. Het Framework roept de methode JobSplitter. split op om een reeks taken te verkrijgen, die wordt toegevoegd aan de taak als deze door de methode wordt geretourneerd. Dit is de klasse waar u de logica van uw taak gaat injecteren. Implementeer de split-methode om een reeks CloudTask-exemplaren te retour neren die de taken vertegenwoordigen waarin u de taak wilt partitioneren.

**Standard .NET-opdracht regel project bestanden**

* `App.config`: Standaard .NET-toepassings configuratie bestand.
* `Packages.config`: Standaard NuGet package-afhankelijkheids bestand.
* `Program.cs`: Bevat het programma-ingangs punt en uitzonde ringen op het hoogste niveau.

### <a name="implementing-the-job-splitter"></a>De taak splitter implementeren
Wanneer u het taak beheer sjabloon project opent, wordt het JobSplitter.cs-bestand standaard geopend in het project. U kunt de Splits logica voor de taken in uw workload implementeren met behulp van de methode split () die hieronder wordt weer gegeven:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> De sectie met aantekeningen in de `Split()` methode is de enige sectie van de sjabloon voor de taak manager die is bedoeld om te wijzigen door de logica toe te voegen om uw taken te splitsen in verschillende taken. Als u een andere sectie van de sjabloon wilt wijzigen, moet u ervoor zorgen dat u bekend bent met de manier waarop batch werkt en kunt u enkele voor [beelden van batch code][github_samples]uitproberen.
> 
> 

Uw Split-implementatie () heeft toegang tot:

* De taak parameters via het `_parameters` veld.
* Het eigenschap cloudjob-object dat de taak via het `_job` veld vertegenwoordigt.
* Het CloudTask-object dat de taak beheerder via het `_jobManagerTask` veld vertegenwoordigt.

Uw `Split()` implementatie hoeft geen taken rechtstreeks aan de taak toe te voegen. In plaats daarvan moet uw code een reeks CloudTask-objecten retour neren. deze worden automatisch aan de taak toegevoegd door de Framework klassen die de taak splitter aanroepen. Het is gebruikelijk de functie iterator (`yield return`) van C# te gebruiken om taak splitsingen te implementeren, omdat hierdoor de taken zo snel mogelijk kunnen worden uitgevoerd in plaats van dat er wordt gewacht tot alle taken moeten worden berekend.

**Taak splitter-fout**

Als uw job splitter een fout tegen komt, moet dit een van de volgende zijn:

* Beëindig de reeks met de C# `yield break` -instructie. in dat geval wordt de taak beheerder als geslaagd behandeld. of
* Hiermee wordt een uitzonde ring gegenereerd. in dat geval wordt de taak beheerder als mislukt beschouwd en wordt er mogelijk een nieuwe poging gedaan, afhankelijk van hoe de client deze heeft geconfigureerd.

In beide gevallen kunnen taken die al zijn geretourneerd door de taak splitter en worden toegevoegd aan de batch-taak, in aanmerking komen voor uitvoering. Als u dit niet wilt doen, kunt u het volgende doen:

* De taak beëindigen voordat deze wordt geretourneerd vanuit de taak splitter
* De volledige taak verzameling formuleren voordat u deze retourneert (dat wil zeggen, `ICollection<CloudTask>` een `IList<CloudTask>` of in plaats van de taak splitsing te implementeren met behulp van een C#-iterator)
* Taak afhankelijkheden gebruiken om alle taken te laten afhangen van de voltooide voltooiing van de taak beheerder

**Nieuwe pogingen taak beheer**

Als de taak beheerder mislukt, wordt het mogelijk opnieuw door de batch-service getracht, afhankelijk van de instellingen voor nieuwe pogingen van de client. In het algemeen is dit veilig, omdat wanneer het Framework taken aan de taak toevoegt, alle taken die al bestaan, worden genegeerd. Als het berekenen van taken duur is, wilt u mogelijk de kosten voor het opnieuw berekenen van taken die al zijn toegevoegd aan de taak niet uitvoeren. Als het opnieuw uitvoeren van de taak-Id's echter niet is gegarandeerd, zal het gedrag voor het negeren van duplicaten niet worden gestart. In deze gevallen moet u uw taak splitsen ontwerpen om het werk te detecteren dat al is uitgevoerd en dit niet herhalen, bijvoorbeeld door het uitvoeren van een eigenschap cloudjob. ListTasks voordat u begint met het leveren van taken.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Afsluit codes en uitzonde ringen in de job manager-sjabloon
Afsluit codes en uitzonde ringen bieden een mechanisme om het resultaat van het uitvoeren van een programma te bepalen, en ze kunnen u helpen bij het identificeren van problemen met de uitvoering van het programma. De job manager-sjabloon implementeert de afsluit codes en uitzonde ringen die in deze sectie worden beschreven.

Een taak beheer taak die is geïmplementeerd met de job manager-sjabloon kan drie mogelijke afsluit codes retour neren:

| Code | Beschrijving |
| --- | --- |
| 0 |De taak beheerder is voltooid. De taak splitter-code is voltooid en alle taken zijn aan de taak toegevoegd. |
| 1 |De taak taak beheer is mislukt met een uitzonde ring in een ' verwacht ' deel van het programma. De uitzonde ring is omgezet naar een JobManagerException met diagnostische gegevens en, waar mogelijk, suggesties voor het oplossen van de fout. |
| 2 |De taak taak beheer is mislukt met een onverwachte uitzonde ring. De uitzonde ring is vastgelegd in de standaard uitvoer, maar de taak beheerder kan geen verdere diagnostische of herstel gegevens toevoegen. |

In het geval van taak beheer mislukt, zijn er mogelijk nog enkele taken aan de service toegevoegd voordat de fout optrad. Deze taken worden normaal uitgevoerd. Zie ' taak splitter-fout ' hierboven voor discussie over dit codepad.

Alle informatie die wordt geretourneerd door uitzonde ringen, wordt geschreven naar stdout. txt en stderr. txt-bestanden. Zie voor meer informatie [fout afhandeling](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Overwegingen voor de client
In deze sectie worden enkele vereisten voor client implementatie beschreven bij het aanroepen van een taak beheerder op basis van deze sjabloon. Zie [para meters en omgevings variabelen door geven uit de client code](#pass-environment-settings) voor meer informatie over het door geven van para meters en omgevings instellingen.

**Verplichte referenties**

Om taken toe te voegen aan de Azure Batch-taak, moeten de URL en de sleutel van uw Azure Batch-account zijn vereist voor de taak beheer taak. U moet deze door geven in omgevings variabelen met de naam YOUR_BATCH_URL en YOUR_BATCH_KEY. U kunt deze instellen in de taak omgevings instellingen van taak beheer. Bijvoorbeeld in een C#-client:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Opslag referenties**

Normaal gesp roken hoeft de client niet de referenties voor het gekoppelde opslag account op te geven voor de taak van taak beheer, omdat (a) de meeste taak beheerders geen expliciete toegang nodig hebben tot het gekoppelde opslag account. (b) het gekoppelde opslag account wordt vaak door gegeven aan alle taken als een algemene omgevings instelling voor de taak. Als u het gekoppelde opslag account niet opgeeft via de instellingen van de gemeen schappelijke omgeving en de taak beheerder toegang tot de gekoppelde opslag vereist, moet u de gekoppelde opslag referenties als volgt opgeven:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Taak instellingen taak beheer**

De client moet de taak beheerder *killJobOnCompletion* -vlag instellen op **False**.

Het is doorgaans veilig voor de client om *runExclusive* in te stellen op **False**.

De client moet de verzameling *Resource files* of *applicationPackageReferences* gebruiken om het uitvoer bare bestand van de job manager (en de vereiste dll's) te laten implementeren naar het reken knooppunt.

De taak beheerder wordt standaard niet opnieuw geprobeerd als deze mislukt. Afhankelijk van de logica van uw taak beheer, is het mogelijk dat de client opnieuw proberen in te scha kelen via *beperkingen*/*maxTaskRetryCount*.

**Taakinstellingen**

Als de taak splitsing taken met afhankelijkheden aflevert, moet de client de usesTaskDependencies van de taak instellen op True.

In het taak scheidings model is het ongebruikelijk dat clients taken willen toevoegen aan taken boven en boven wat de taak splitsing maakt. De client moet daarom normaal gesp roken de *onAllTasksComplete* van de taak instellen op **terminatejob**.

## <a name="task-processor-template"></a>Taak processor sjabloon
Een taak processor sjabloon helpt u bij het implementeren van een taak processor die de volgende acties kan uitvoeren:

* Stel de informatie in die voor elke batch taak vereist is om uit te voeren.
* Alle acties uitvoeren die voor elke batch taak zijn vereist.
* Sla de taak uitvoer op in permanente opslag.

Hoewel een taak processor niet vereist is voor het uitvoeren van taken voor batch, is het belangrijkste voor deel van het gebruik van een taak processor dat het een wrapper biedt om alle taken voor taak uitvoering op één locatie uit te voeren. Als u bijvoorbeeld meerdere toepassingen in de context van elke taak moet uitvoeren, of als u gegevens naar permanente opslag moet kopiëren nadat elke taak is voltooid.

De acties die door de taak processor worden uitgevoerd, kunnen zo eenvoudig of complex zijn, en zo veel of zo weinig, zoals vereist voor uw werk belasting. Daarnaast kunt u, door alle taak acties in één taak processor te implementeren, acties direct bijwerken of toevoegen op basis van wijzigingen in toepassingen of werk belasting vereisten. In sommige gevallen is het echter mogelijk dat een taak processor niet de optimale oplossing is voor uw implementatie, omdat deze een onnodige complexiteit kan toevoegen, bijvoorbeeld wanneer taken worden uitgevoerd die snel kunnen worden gestart vanaf een eenvoudige opdracht regel.

### <a name="create-a-task-processor-using-the-template"></a>Een taak processor maken met behulp van de sjabloon
Als u een taak processor wilt toevoegen aan de oplossing die u eerder hebt gemaakt, voert u de volgende stappen uit:

1. Open uw bestaande oplossing in Visual Studio.
2. Klik in Solution Explorer met de rechter muisknop op de oplossing, klikt u op **toevoegen**en klik vervolgens op **Nieuw project**.
3. Klik onder **Visual C#** op **Cloud**en klik vervolgens op **Azure batch taak processor**.
4. Typ een naam die uw toepassing beschrijft en identificeert dit project als de taak processor (bijvoorbeeld ' LitwareTaskProcessor ').
5. Klik op **OK**om het project te maken.
6. Stel tot slot het project op om te forceren dat Visual Studio alle NuGet-pakketten waarnaar wordt verwezen, laadt en om te controleren of het project geldig is voordat u het gaat wijzigen.

### <a name="task-processor-template-files-and-their-purpose"></a>Sjabloon bestanden voor taak processor en hun doel
Wanneer u een project maakt met behulp van de taak processor sjabloon, worden er drie groepen code bestanden gegenereerd:

* Het hoofd programma bestand (Program.cs). Dit bevat het programma-ingangs punt en de verwerking van uitzonde ringen op het hoogste niveau. Normaal gesp roken hoeft u dit niet te wijzigen.
* De Framework-map. Dit bevat de bestanden die verantwoordelijk zijn voor het ' standaard ' werk dat door het job manager-programma wordt uitgevoerd – het uitpakken van para meters, het toevoegen van taken aan de batch-taak, enzovoort. Normaal gesp roken hoeft u deze bestanden niet te wijzigen.
* Het taak processor bestand (TaskProcessor.cs). Hier plaatst u uw toepassingsspecifieke logica voor het uitvoeren van een taak (meestal door een bestaand uitvoerbaar bestand aan te roepen). De code vóór en na het verwerken, zoals het downloaden van extra gegevens of het uploaden van resultaat bestanden, komt hier ook.

Natuurlijk kunt u extra bestanden toevoegen die nodig zijn om uw taak processor code te ondersteunen, afhankelijk van de complexiteit van de logica voor het splitsen van taken.

De sjabloon genereert ook standaard-.NET-project bestanden, zoals een. csproj-bestand, app. config, packages. config, enzovoort.

In de rest van deze sectie worden de verschillende bestanden en hun code structuur beschreven en wordt uitgelegd wat elke klasse doet.

![Visual Studio Solution Explorer de oplossing van de taak processor sjabloon weer geven][solution_explorer02]

**Framework-bestanden**

* `Configuration.cs`: Het laden van taak configuratie gegevens, zoals Details van de batch-account, de referenties van een gekoppeld opslag account, taak-en taak gegevens en taak parameters. Het biedt ook toegang tot met batch gedefinieerde omgevings variabelen (Zie omgevings instellingen voor taken in de batch-documentatie) via de klasse Configuration. EnvironmentVariable.
* `IConfiguration.cs`: Abstracten de implementatie van de configuratie klasse, zodat u uw taak splitsing kunt testen met behulp van een configuratie object voor een valse of model.
* `TaskProcessorException.cs`: Dit is een fout die vereist dat de taak beheerder wordt beëindigd. TaskProcessorException wordt gebruikt om ' verwachte ' fouten op te slaan waarbij specifieke diagnostische gegevens kunnen worden verstrekt als onderdeel van de beëindiging.

**Taak processor**

* `TaskProcessor.cs`: De taak wordt uitgevoerd. Het Framework roept de methode TaskProcessor. run aan. Dit is de klasse waarin u de toepassingsspecifieke logica van uw taak wilt injecteren. Implementeer de methode Run voor het volgende:
  * Taak parameters parseren en valideren
  * De opdracht regel opstellen voor elk extern programma dat u wilt aanroepen
  * Diagnostische gegevens registreren die u mogelijk nodig hebt voor fout opsporing
  * Een proces starten met die opdracht regel
  * Wacht tot het proces is afgesloten
  * Leg de afsluit code van het proces vast om te bepalen of dit is gelukt of mislukt
  * Sla uitvoer bestanden op die u wilt behouden voor permanente opslag

**Standard .NET-opdracht regel project bestanden**

* `App.config`: Standaard .NET-toepassings configuratie bestand.
* `Packages.config`: Standaard NuGet package-afhankelijkheids bestand.
* `Program.cs`: Bevat het programma-ingangs punt en uitzonde ringen op het hoogste niveau.

## <a name="implementing-the-task-processor"></a>De taak processor implementeren
Wanneer u het taak processor sjabloon project opent, wordt het TaskProcessor.cs-bestand standaard geopend in het project. U kunt de uitvoerings logica voor de taken in uw workload implementeren met behulp van de methode Run () die hieronder wordt weer gegeven:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> De sectie met aantekeningen in de methode Run () is de enige sectie van de sjabloon voor de taak processor die u wilt aanpassen door de uitvoerings logica voor de taken in uw werk belasting toe te voegen. Als u een andere sectie van de sjabloon wilt wijzigen, moet u eerst nagaan hoe batch werkt door de batch-documentatie te controleren en een aantal van de batch-code voorbeelden uit te proberen.
> 
> 

De methode Run () is verantwoordelijk voor het starten van de opdracht regel, het starten van een of meer processen, wacht op het volt ooien van het proces, het opslaan van de resultaten en uiteindelijk retour met een afsluit code. Met de methode Run () implementeert u de verwerkings logica voor uw taken. Het taak verwerkings raamwerk roept de methode Run () aan voor u. u hoeft deze niet zelf aan te roepen.

Uw run ()-implementatie heeft toegang tot:

* De taak parameters, via het `_parameters` veld.
* De taak-en taak-id's via `_jobId` de `_taskId` velden en.
* De taak configuratie, via het `_configuration` veld.

**Taak fout**

Als er een fout is opgetreden, kunt u de methode Run () afsluiten door een uitzonde ring uit te voeren, maar hierdoor blijft de uitzonderings-handler op het hoogste niveau in de controle over de afsluit code van de taak. Als u de afsluit code moet bepalen zodat u verschillende soorten storingen kunt onderscheiden, bijvoorbeeld voor diagnostische doel einden of omdat sommige fout modi de taak moeten beëindigen en andere niet, moet u de methode Run () afsluiten door een afsluit code van niet nul te retour neren. Dit wordt de afsluit code van de taak.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Afsluit codes en uitzonde ringen in de taak processor sjabloon
Afsluit codes en uitzonde ringen bieden een mechanisme om het resultaat van het uitvoeren van een programma te bepalen, en ze kunnen u helpen bij het identificeren van problemen met de uitvoering van het programma. De taak verwerkings sjabloon implementeert de afsluit codes en uitzonde ringen die in deze sectie worden beschreven.

Een taak processor taak die is geïmplementeerd met de taak processor sjabloon kan drie mogelijke afsluit codes retour neren:

| Code | Beschrijving |
| --- | --- |
| [Process. ExitCode][process_exitcode] |De taak processor is voltooid. Houd er rekening mee dat dit niet impliceert dat het programma dat u hebt aangeroepen, is geslaagd: alleen dat de taak processor het heeft aangeroepen en zonder uitzonde ringen na de verwerking is uitgevoerd. De betekenis van de afsluit code is afhankelijk van het aangeroepen programma, meestal afsluit code 0 betekent dat het programma is geslaagd en andere afsluit code betekent dat het programma is mislukt. |
| 1 |De taak processor is mislukt met een uitzonde ring in een ' verwacht ' deel van het programma. De uitzonde ring is vertaald `TaskProcessorException` naar een met diagnostische gegevens en, waar mogelijk, suggesties voor het oplossen van de fout. |
| 2 |De taak processor is mislukt met een onverwachte uitzonde ring. De uitzonde ring is vastgelegd in de standaard uitvoer, maar de taak processor kan geen verdere diagnostische of herstel gegevens toevoegen. |

> [!NOTE]
> Als het programma dat u aanroept afsluit codes 1 en 2 gebruikt om specifieke fout modi aan te geven, is het gebruik van afsluit codes 1 en 2 voor fouten van de taak processor dubbel zinnig. U kunt deze fout codes voor taak processors wijzigen in onderscheidende afsluit codes door de uitzonderings cases in het Program.cs-bestand te bewerken.
> 
> 

Alle informatie die wordt geretourneerd door uitzonde ringen, wordt geschreven naar stdout. txt en stderr. txt-bestanden. Zie fout afhandeling in de batch-documentatie voor meer informatie.

### <a name="client-considerations"></a>Overwegingen voor de client
**Opslag referenties**

Als uw taak processor gebruikmaakt van Azure Blob-opslag om uitvoer te behouden, bijvoorbeeld met behulp van de bestands conventies helper-bibliotheek, *moet u toegang hebben tot de referenties* van het Cloud Storage-account *of* een BLOB-container-URL die een Shared Access Signature (SAS) bevat. De sjabloon bevat ondersteuning voor het opgeven van referenties via algemene omgevings variabelen. De client kan de opslag referenties als volgt door geven:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Het opslag account is vervolgens beschikbaar in de TaskProcessor-klasse via `_configuration.StorageAccount` de eigenschap.

Als u liever een container-URL met SAS gebruikt, kunt u dit ook door geven via een gemeen schappelijke omgevings instelling, maar de taak verwerkings sjabloon bevat momenteel geen ingebouwde ondersteuning.

**Opslag instellen**

Het wordt aanbevolen dat de client of taak beheer-taak containers maakt voor taken voordat u de taken toevoegt aan de taak. Dit is verplicht als u een container-URL met SAS gebruikt, omdat een URL geen machtiging bevat voor het maken van de container. Het wordt aanbevolen zelfs als u referenties voor het opslag account doorgeeft, omdat hiermee elke taak wordt opgeslagen die CloudBlobContainer. CreateIfNotExistsAsync op de container aanroept.

## <a name="pass-parameters-and-environment-variables"></a>Para meters en omgevings variabelen door geven
### <a name="pass-environment-settings"></a>Instellingen van de omgeving door geven
Een client kan gegevens door geven aan de taak van de taak beheerder in de vorm van omgevings instellingen. Deze informatie kan vervolgens worden gebruikt door de taak taak beheer bij het genereren van de taak verwerkings taken die worden uitgevoerd als onderdeel van de reken taak. Voor beelden van de informatie die u kunt door geven als omgevings instellingen zijn:

* Naam en account sleutels van het opslag account
* URL van het batch-account
* Batch-account sleutel

De batch-service heeft een eenvoudig mechanisme om omgevings instellingen door te geven aan een taak beheer `EnvironmentSettings` taak met behulp van de eigenschap in [micro soft. Azure. batch. JobManagerTask][net_jobmanagertask].

Als u bijvoorbeeld het `BatchClient` exemplaar voor een batch-account wilt ophalen, kunt u door geven als omgevings variabelen van de client code de URL en de referenties van de gedeelde sleutel voor het batch-account. Op dezelfde manier kunt u toegang krijgen tot het opslag account dat is gekoppeld aan het batch-account door de naam van het opslag account en de sleutel van het opslag account als omgevings variabelen door te geven.

### <a name="pass-parameters-to-the-job-manager-template"></a>Para meters door geven aan de job manager-sjabloon
In veel gevallen is het handig om per taak para meters door te geven aan de taak beheer taak, hetzij om het proces voor het splitsen van taken te beheren of om de taken voor de taak te configureren. U kunt dit doen door een JSON-bestand met de naam para meters. json te uploaden als een resource bestand voor de taak taak beheer. De para meters kunnen vervolgens beschikbaar worden `JobSplitter._parameters` in het veld in de job manager-sjabloon.

> [!NOTE]
> De ingebouwde para meter-handler ondersteunt alleen teken reeks-naar-teken reeks woordenlijsten. Als u complexe JSON-waarden wilt door geven als parameter waarden, moet u deze door geven als teken reeksen en ze parseren in de taak splitsing, of de methode `Configuration.GetJobParameters` van het Framework wijzigen.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Para meters door geven aan de taak processor sjabloon
U kunt ook para meters door geven aan afzonderlijke taken die zijn geïmplementeerd met behulp van de taak verwerkings sjabloon. Net als bij de taak beheer sjabloon zoekt de taak processor sjabloon naar een bron bestand met de naam

para meters. json, en als deze wordt gevonden, wordt deze geladen als de woorden lijst met para meters. Er zijn een aantal opties voor het door geven van para meters aan taak verwerkings taken:

* De JSON van de taak parameters opnieuw gebruiken. Dit werkt goed als de enige para meters voor de hele taak zijn (bijvoorbeeld een weergave hoogte en-breedte). Als u dit wilt doen, voegt u bij het maken van een CloudTask in de taak splitter een verwijzing toe naar het Resource object para meters. json van`JobSplitter._jobManagerTask.ResourceFiles`de Resource files () van de job manager-taak naar de Resource files-verzameling van CloudTask.
* Genereer en upload een gebruikersspecifieke para meters. JSON-document als onderdeel van de uitvoering van taak splitter en referentie die Blob in de verzameling bron bestanden van de taak. Dit is nodig als verschillende taken verschillende para meters hebben. Een voor beeld hiervan is een 3D-rendering-scenario waarbij de frame-index wordt door gegeven aan de taak als een para meter.

> [!NOTE]
> De ingebouwde para meter-handler ondersteunt alleen teken reeks-naar-teken reeks woordenlijsten. Als u complexe JSON-waarden wilt door geven als parameter waarden, moet u deze door geven als teken reeksen en ze parseren in de taak processor, of de methode `Configuration.GetTaskParameters` van het Framework wijzigen.
> 
> 

## <a name="next-steps"></a>Volgende stappen
### <a name="persist-job-and-task-output-to-azure-storage"></a>Taak-en taak uitvoer persistent maken naar Azure Storage
Een ander handig hulp middel bij het ontwikkelen van batch oplossingen is [Azure batch bestands conventies][nuget_package]. Gebruik deze .NET-klassebibliotheek (momenteel in Preview) in uw batch .NET-toepassingen om snel taak uitvoer van en naar Azure Storage op te slaan en op te halen. [Azure batch taak en taak uitvoer blijven behouden](batch-task-output.md) , bevat een volledige bespreking van de tape wisselaar en het gebruik ervan.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://github.com/Azure/batch-extension-templates
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
