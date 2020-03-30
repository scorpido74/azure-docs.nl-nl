---
title: Oplossingen bouwen met Visual Studio-sjablonen - Azure Batch | Microsoft Documenten
description: Ontdek hoe projectsjablonen van Visual Studio u kunnen helpen bij het implementeren en uitvoeren van uw computerintensieve workloads op Azure Batch.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a71dbd1b38ff58ccf1eb7a4d50daad5b24922e2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022746"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Projectsjablonen voor Visual Studio gebruiken om batchoplossingen een vliegende start te geven

De **Job Manager-** en **Taakprocessor Visual Studio-sjablonen** voor Batch bieden code waarmee u uw computerintensieve workloads op Batch met de minste inspanning implementeren en uitvoeren. Dit document beschrijft deze sjablonen en biedt richtlijnen voor het gebruik ervan.

> [!IMPORTANT]
> In dit artikel wordt alleen informatie besproken die van toepassing is op deze twee sjablonen en wordt ervan uitgegaan dat u bekend bent met de Batch-service en de bijbehorende sleutelconcepten: pools, compute nodes, taken en taken, taken voor taakbeheer, omgevingsvariabelen en andere relevante Informatie. Meer informatie vindt u in het overzicht [van de basisbeginselen van azure batch-](batch-technical-overview.md) en [batchfuncties voor ontwikkelaars.](batch-api-basics.md)
> 
> 

## <a name="high-level-overview"></a>Overzicht op hoog niveau
De sjablonen JobManager en Taakprocessor kunnen worden gebruikt om twee nuttige componenten te maken:

* Een taak voor taakbeheer die een taaksplitter implementeert die een taak kan opsplitsen in meerdere taken die onafhankelijk kunnen worden uitgevoerd, parallel.
* Een taakprocessor die kan worden gebruikt om voorbewerking en nabewerking uit te voeren rond een opdrachtregel voor toepassingen.

In een scenario voor het renderen van films wordt bijvoorbeeld door de taaksplitter van één filmtaak omgezet in honderden of duizenden afzonderlijke taken die afzonderlijke frames afzonderlijk verwerken. De taakverwerker zou de rendering-toepassing en alle afhankelijke processen die nodig zijn om elk frame weer te geven, en eventuele aanvullende acties uitvoeren (bijvoorbeeld het kopiëren van het gerenderde frame naar een opslaglocatie).

> [!NOTE]
> De sjablonen JobManager en Taakbeheer zijn onafhankelijk van elkaar, zodat u ervoor kiezen om beide of slechts één van deze sjablonen te gebruiken, afhankelijk van de vereisten van uw rekentaak en uw voorkeuren.
> 
> 

Zoals in het onderstaande diagram wordt weergegeven, gaat een rekentaak die deze sjablonen gebruikt drie fasen:

1. De clientcode (bijvoorbeeld toepassing, webservice, enz.) verzendt een taak bij de Batch-service op Azure, waarbij het taakbeheerprogramma als taakmanager wordt opgegeven.
2. The Batch service runs the job manager task on a compute node and the job splitter launches the specified number of task processor tasks, on as many compute nodes as required, based on the parameters and specifications in the job splitter code.
3. De taakprocessortaken worden onafhankelijk uitgevoerd, parallel, om de invoergegevens te verwerken en de uitvoergegevens te genereren.

![Diagram met de interactie van clientcode met de Batch-service][diagram01]

## <a name="prerequisites"></a>Vereisten
Als u de batchsjablonen wilt gebruiken, hebt u het volgende nodig:

* Een computer met Visual Studio 2015 geïnstalleerd. Batchsjablonen worden momenteel alleen ondersteund voor Visual Studio 2015.
* De Batch-sjablonen, die beschikbaar zijn in de [Visual Studio Gallery][vs_gallery] als Visual Studio-extensies. Er zijn twee manieren om de sjablonen te krijgen:
  
  * Installeer de sjablonen met het dialoogvenster **Extensies en updates** in Visual Studio (zie Visual [Studio-extensies zoeken en gebruiken][vs_find_use_ext]voor meer informatie). Zoek **en** download in het dialoogvenster Extensies en updates de volgende twee extensies:
    
    * Azure Batch Job Manager met Job Splitter
    * Azure Batch-taakprocessor
  * De sjablonen downloaden uit de online galerie voor Visual Studio: [Microsoft Azure Batch Project Templates][vs_gallery_templates]
* Als u van plan bent de functie [Toepassingspakketten](batch-application-packages.md) te gebruiken om de taakbeheer en taakprocessor te implementeren naar de batchcomputeknooppunten, moet u een opslagaccount koppelen aan uw Batch-account.

## <a name="preparation"></a>Voorbereiding
We raden u aan een oplossing te maken die uw jobmanager en uw taakprocessor kan bevatten, omdat dit het gemakkelijker kan maken om code te delen tussen uw jobmanager en taakprocessorprogramma's. Voer de volgende stappen uit om deze oplossing te maken:

1. Open Visual Studio en selecteer **Bestand** > **Nieuw** > **project**.
2. Vouw **onder Sjablonen**andere **projecttypen**uit, klik op Visuele **studio-oplossingen**en selecteer Vervolgens Lege **oplossing**.
3. Typ een naam die uw toepassing en het doel van deze oplossing beschrijft (bijvoorbeeld 'LitwareBatchTaskPrograms').
4. Als u de nieuwe oplossing wilt maken, klikt u op **OK**.

## <a name="job-manager-template"></a>Sjabloon Jobmanager
Met de sjabloon Jobmanager u een taak voor taak voor taak voor taakbeheer implementeren die de volgende acties kan uitvoeren:

* Een taak splitsen in meerdere taken.
* Die taken verzenden om op Batch uit te voeren.

> [!NOTE]
> Zie Overzicht van de [functie Batch voor ontwikkelaars voor](batch-api-basics.md#job-manager-task)meer informatie over taken voor taakbeheer.
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Een JobManager maken met de sjabloon
Voer de volgende stappen uit om een taakmanager toe te voegen aan de oplossing die u eerder hebt gemaakt:

1. Open uw bestaande oplossing in Visual Studio.
2. Klik in Solution Explorer met de rechtermuisknop op de oplossing en klik op**Nieuw project** **toevoegen** > .
3. Klik **onder Visual C#** op **Cloud**en klik vervolgens op Azure Batch Job Manager met **Job Splitter**.
4. Typ een naam die uw sollicitatie beschrijft en identificeert dit project als de jobmanager (bijvoorbeeld "LitwareJobManager").
5. Als u het project wilt maken, klikt u op **OK**.
6. Bouw ten slotte het project om Visual Studio te dwingen alle nuget-pakketten te laden en om te controleren of het project geldig is voordat u begint met wijzigen.

### <a name="job-manager-template-files-and-their-purpose"></a>Job Manager-sjabloonbestanden en hun doel
Wanneer u een project maakt met de sjabloon Jobmanager, genereert het drie groepen codebestanden:

* Het hoofdprogrammabestand (Program.cs). Dit bevat het instappunt van het programma en de afhandeling van uitzondering op het hoogste niveau. Normaal gesproken hoeft u dit niet aan te passen.
* De kadermap. Dit bevat de bestanden die verantwoordelijk zijn voor de 'boilerplate' werk gedaan door de job manager programma - uitpakken parameters, het toevoegen van taken aan de Batch taak, enz. Normaal gesproken hoeft u deze bestanden niet te wijzigen.
* Het taaksplitterbestand (JobSplitter.cs). Hier zet u uw toepassingsspecifieke logica in voor het splitsen van een taak in taken.

Natuurlijk u extra bestanden toevoegen als dat nodig is om uw taaksplittercode te ondersteunen, afhankelijk van de complexiteit van de logica voor het splitsen van de taak.

De sjabloon genereert ook standaard .NET-projectbestanden, zoals een .csproj-bestand, app.config, packages.config, enz.

De rest van deze sectie beschrijft de verschillende bestanden en hun codestructuur en legt uit wat elke klasse doet.

![Visual Studio Solution Explorer met de sjabloonoplossing Voor JobManager][solution_explorer01]

**Framework-bestanden**

* `Configuration.cs`: Bevat het laden van taakconfiguratiegegevens zoals batchaccountgegevens, gekoppelde opslagaccountreferenties, taak- en taakgegevens en taakparameters. Het biedt ook toegang tot batch-gedefinieerde omgevingsvariabelen (zie Omgevingsinstellingen voor taken, in de batchdocumentatie) via de klasse Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Hernieuwt de implementatie van de klasse Configuration, zodat u uw taaksplitter testen met behulp van een nep- of nepconfiguratieobject.
* `JobManager.cs`: Orkestreert de onderdelen van het jobmanagerprogramma. Het is verantwoordelijk voor het initialiseren van de taaksplitter, het inroepen van de taaksplitter en het verzenden van de taken die door de taaksplitter worden geretourneerd naar de taakindiener.
* `JobManagerException.cs`: Vertegenwoordigt een fout die vereist dat de taakbeheerder moet worden beëindigd. JobManagerException wordt gebruikt om 'verwachte' fouten te verwerken waarbij specifieke diagnostische informatie kan worden verstrekt als onderdeel van beëindiging.
* `TaskSubmitter.cs`: Deze klasse is verantwoordelijk voor het toevoegen van taken die door de taaksplitter zijn geretourneerd aan de batchtaak. De klasse JobManager verzamelt de volgorde van taken in batches voor een efficiënte maar tijdige aanvulling op de taak en roept vervolgens Taakindiener.SubmitTasks op een achtergrondthread voor elke batch.

**Taaksplitter**

`JobSplitter.cs`: Deze klasse bevat toepassingsspecifieke logica voor het splitsen van de taak in taken. Het framework beroept zich op de JobSplitter.Split-methode om een reeks taken te verkrijgen, die het toevoegt aan de taak terwijl de methode deze retourneert. Dit is de klas waar je de logica van je werk injecteert. Implementeer de methode Splitsen om een reeks CloudTask-exemplaren te retourneren die de taken weergeven waarin u de taak wilt verdelen.

**Projectbestanden voor standaard .NET-opdrachtregel**

* `App.config`: Standaard configuratiebestand voor .NET-toepassingen.
* `Packages.config`: Standaard NuGet-pakketafhankelijkheidsbestand.
* `Program.cs`: Bevat het instappunt van het programma en de afhandeling van uitzonderingen op het hoogste niveau.

### <a name="implementing-the-job-splitter"></a>De taaksplitter implementeren
Wanneer u het sjabloonproject Jobmanager opent, wordt het JobSplitter.cs standaard geopend. U de gesplitste logica voor de taken in uw werkbelasting implementeren met de methode Split() hieronder weergeven:

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
> De geannoteerde sectie `Split()` in de methode is het enige gedeelte van de sjablooncode Jobmanager die u moet wijzigen door de logica toe te voegen om uw taken in verschillende taken op te splitsen. Als u een ander gedeelte van de sjabloon wilt wijzigen, moet u ervoor zorgen dat u vertrouwd bent met de werking van Batch en een paar van de [batchcodevoorbeelden][github_samples]uitproberen.
> 
> 

Uw Split()-implementatie heeft toegang tot:

* De taakparameters, `_parameters` via het veld.
* Het CloudJob-object dat de `_job` taak vertegenwoordigt, via het veld.
* Het object CloudTask dat de taak `_jobManagerTask` van de taak voor taaktaak functiebeheer vertegenwoordigt, via het veld.

Uw `Split()` implementatie hoeft geen taken rechtstreeks aan de taak toe te voegen. In plaats daarvan moet uw code een reeks CloudTask-objecten retourneren en deze worden automatisch aan de taak toegevoegd door de frameworkklassen die de taaksplitter aanroepen. Het is gebruikelijk om c #'s iterator (`yield return`) functie te gebruiken om taaksplitters te implementeren, omdat dit de taken in staat stelt om zo snel mogelijk te beginnen met draaien in plaats van te wachten tot alle taken worden berekend.

**Fout van taaksplitter**

Als uw taaksplitter een fout tegenkomt, moet deze ook:

* Beëindig de reeks met `yield break` de C#-instructie, in welk geval de jobmanager als succesvol wordt behandeld; Of
* Gooi een uitzondering, in welk geval de jobmanager als mislukt wordt behandeld en opnieuw kan worden geprobeerd, afhankelijk van hoe de client deze heeft geconfigureerd).

In beide gevallen komen taken die al zijn geretourneerd door de taaksplitter en die zijn toegevoegd aan de batchtaak, in aanmerking om uit te voeren. Als je niet wilt dat dit gebeurt, dan kun je:

* De taak beëindigen voordat u terugkeert van de taaksplitter
* Formuleer de volledige taakverzameling voordat u `ICollection<CloudTask>` deze `IList<CloudTask>` teruggeeft (dat wil zeggen, een of in plaats van uw taaksplitter te implementeren met behulp van een C# iterator)
* Taakafhankelijkheden gebruiken om alle taken afhankelijk te maken van de succesvolle voltooiing van de taakmanager

**Job manager nieuwe pogingen**

Als de taakmanager mislukt, kan deze opnieuw worden geprobeerd door de Batch-service, afhankelijk van de instellingen voor het opnieuw proberen van de client. In het algemeen is dit veilig, omdat wanneer het framework taken toevoegt aan de taak, het alle taken negeert die al bestaan. Als het berekenen van taken echter duur is, u niet de kosten maken voor het opnieuw berekenen van taken die al aan de taak zijn toegevoegd. omgekeerd, als de re-run is niet gegarandeerd om dezelfde taak-id's te genereren dan de 'negeren duplicaten' gedrag zal niet kick in. In deze gevallen moet u uw taaksplitter ontwerpen om het werk te detecteren dat al is gedaan en niet herhalen, bijvoorbeeld door een CloudJob.ListTasks uit te voeren voordat u taken begint op te leveren.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Codes en uitzonderingen afsluiten in de sjabloon Jobmanager
Exit codes en uitzonderingen bieden een mechanisme om de uitkomst van het uitvoeren van een programma te bepalen, en ze kunnen helpen om eventuele problemen met de uitvoering van het programma te identificeren. De sjabloon Jobmanager implementeert de exitcodes en uitzonderingen die in deze sectie worden beschreven.

Een taak voor taakbeheer die wordt geïmplementeerd met de sjabloon Jobmanager, kan drie mogelijke exitcodes retourneren:

| Code | Beschrijving |
| --- | --- |
| 0 |De jobmanager is succesvol afgerond. Uw taaksplittercode is voltooid en alle taken zijn toegevoegd aan de taak. |
| 1 |De taak voor taakbeheer is mislukt met een uitzondering in een 'verwacht' deel van het programma. De uitzondering werd vertaald naar een JobManagerException met diagnostische informatie en, waar mogelijk, suggesties voor het oplossen van de fout. |
| 2 |De taak voor taakbeheer is mislukt met een 'onverwachte' uitzondering. De uitzondering is vastgelegd op standaarduitvoer, maar de taakbeheerder kan geen aanvullende diagnostische of herstelgegevens toevoegen. |

In het geval van taakfout van taakbeheer zijn mogelijk nog steeds taken aan de service toegevoegd voordat de fout is opgetreden. Deze taken worden normaal uitgevoerd. Zie hierboven 'Fout fout in taak' voor bespreking van dit codepad.

Alle informatie die door uitzonderingen wordt geretourneerd, wordt geschreven in stdout.txt- en stderr.txt-bestanden. Zie [Foutafhandeling](batch-api-basics.md#error-handling)voor meer informatie .

### <a name="client-considerations"></a>Overwegingen van de klant
In deze sectie worden enkele vereisten voor clientimplementatie beschreven wanneer u een functiemanager inroept op basis van deze sjabloon. Zie [Hoe u parameters en omgevingsvariabelen uit de clientcode doorgeven](#pass-environment-settings) voor meer informatie over het doorgeven van parameters en omgevingsinstellingen.

**Verplichte referenties**

Om taken toe te voegen aan de Azure Batch-taak, vereist de taak voor taakbeheer uw URL en sleutel van het Azure Batch-account. U moet deze doorgeven in omgevingsvariabelen met de naam YOUR_BATCH_URL en YOUR_BATCH_KEY. U deze instellen in de instellingen voor taakomgeving jobbeheer. Bijvoorbeeld in een C#-client:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Opslagreferenties**

Doorgaans hoeft de client de gekoppelde opslagaccountreferenties niet aan de taak voor taak voor taak taak voor taak taak voor taak taken te verstrekken, omdat (a) de meeste taakmanagers niet expliciet toegang hoeven te krijgen tot het gekoppelde opslagaccount en (b) het gekoppelde opslagaccount vaak wordt verstrekt aan alle taken als een gemeenschappelijke omgeving instelling voor de taak. Als u het gekoppelde opslagaccount niet verstrekt via de algemene omgevingsinstellingen en de taakbeheerbeheerder toegang tot gekoppelde opslag vereist, moet u de gekoppelde opslagreferenties als volgt leveren:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Taakinstellingen voor taakbeheer**

De klant moet de jobmanager *killJobOnCompletion* vlag op **false**instellen.

Het is meestal veilig voor de klant om *runExclusive* in te stellen op **false.**

De client moet de *resourceFiles* of *applicationPackageReferences-verzameling* gebruiken om de taakmanager uitvoerbaar (en de vereiste DLL's) te laten implementeren in het compute-knooppunt.

Standaard wordt de jobmanager niet opnieuw geprobeerd als deze mislukt. Afhankelijk van de logica van uw jobmanager kan de client nieuwe pogingen inschakelen via *beperkingen*/*maxTaskRetryCount.*

**Taakinstellingen**

Als de taaksplitter taken met afhankelijkheden uitzendt, moet de client het gebruik van de taaktaakafhankelijkheden intrueinstellen.

In het taaksplittermodel is het ongebruikelijk dat clients taken willen toevoegen aan taken boven wat de taaksplitter maakt. De client moet daarom normaal gesproken de *taak onAllTasksComplete* instellen op **beëindiging van de taak**.

## <a name="task-processor-template"></a>Sjabloon Taakprocessor
Met een sjabloon Taakprocessor u een taakprocessor implementeren die de volgende acties kan uitvoeren:

* Stel de informatie in die vereist is voor elke batchtaak die moet worden uitgevoerd.
* Voer alle acties uit die vereist zijn voor elke batchtaak.
* Sla taakuitvoer op in permanente opslag.

Hoewel een taakprocessor niet vereist is om taken uit te voeren op Batch, is het belangrijkste voordeel van het gebruik van een taakprocessor dat deze een wrapper biedt om alle taakuitvoeringsacties op één locatie te implementeren. Als u bijvoorbeeld meerdere toepassingen in de context van elke taak moet uitvoeren of als u gegevens na het voltooien van elke taak naar permanente opslag moet kopiëren.

De acties die door de taakprocessor worden uitgevoerd, kunnen zo eenvoudig of complex zijn en zo veel of zo weinig, zoals vereist door uw werkbelasting. Bovendien u, door alle taakacties in één taakprocessor te implementeren, eenvoudig acties bijwerken of toevoegen op basis van wijzigingen in toepassingen of werkbelastingvereisten. In sommige gevallen is een taakprocessor echter niet de optimale oplossing voor uw implementatie, omdat deze onnodige complexiteit kan toevoegen, bijvoorbeeld bij het uitvoeren van taken die snel kunnen worden gestart vanaf een eenvoudige opdrachtregel.

### <a name="create-a-task-processor-using-the-template"></a>Een taakprocessor maken met de sjabloon
Voer de volgende stappen uit om een taakprocessor toe te voegen aan de oplossing die u eerder hebt gemaakt:

1. Open uw bestaande oplossing in Visual Studio.
2. Klik in Solution Explorer met de rechtermuisknop op de oplossing, klik op **Toevoegen**en klik vervolgens op **Nieuw project**.
3. Klik **onder Visual C#** op **Cloud**en klik vervolgens op Azure Batch **Task Processor**.
4. Typ een naam die uw toepassing beschrijft en identificeert dit project als de taakprocessor (bijvoorbeeld 'LitwareTaskProcessor').
5. Als u het project wilt maken, klikt u op **OK**.
6. Bouw ten slotte het project om Visual Studio te dwingen alle nuget-pakketten te laden en om te controleren of het project geldig is voordat u begint met wijzigen.

### <a name="task-processor-template-files-and-their-purpose"></a>Sjabloonbestanden taakprocessor en hun doel
Wanneer u een project maakt met de sjabloon taakverwerker, genereert het drie groepen codebestanden:

* Het hoofdprogrammabestand (Program.cs). Dit bevat het instappunt van het programma en de afhandeling van uitzondering op het hoogste niveau. Normaal gesproken hoeft u dit niet aan te passen.
* De kadermap. Dit bevat de bestanden die verantwoordelijk zijn voor de 'boilerplate' werk gedaan door de job manager programma - uitpakken parameters, het toevoegen van taken aan de Batch taak, enz. Normaal gesproken hoeft u deze bestanden niet te wijzigen.
* Het taakprocessorbestand (TaskProcessor.cs). Dit is waar u uw toepassingsspecifieke logica voor het uitvoeren van een taak plaatst (meestal door een bestaande uitvoerbare opdracht te geven). Pre- en post-processing code, zoals het downloaden van extra gegevens of het uploaden van resultaatbestanden, gaat hier ook.

Natuurlijk u extra bestanden toevoegen als dat nodig is om uw taakprocessorcode te ondersteunen, afhankelijk van de complexiteit van de logica voor taaksplitsing.

De sjabloon genereert ook standaard .NET-projectbestanden, zoals een .csproj-bestand, app.config, packages.config, enz.

De rest van deze sectie beschrijft de verschillende bestanden en hun codestructuur en legt uit wat elke klasse doet.

![Visual Studio Solution Explorer met de sjabloonoplossing Taakprocessor][solution_explorer02]

**Framework-bestanden**

* `Configuration.cs`: Bevat het laden van taakconfiguratiegegevens zoals batchaccountgegevens, gekoppelde opslagaccountreferenties, taak- en taakgegevens en taakparameters. Het biedt ook toegang tot batch-gedefinieerde omgevingsvariabelen (zie Omgevingsinstellingen voor taken, in de batchdocumentatie) via de klasse Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Hernieuwt de implementatie van de klasse Configuration, zodat u uw taaksplitter testen met behulp van een nep- of nepconfiguratieobject.
* `TaskProcessorException.cs`: Vertegenwoordigt een fout die vereist dat de taakbeheerder moet worden beëindigd. TaskProcessorException wordt gebruikt om 'verwachte' fouten te verwerken waarbij specifieke diagnostische informatie kan worden verstrekt als onderdeel van beëindiging.

**Taakprocessor**

* `TaskProcessor.cs`: Voert de taak uit. Het framework beroept zich op de methode TaskProcessor.Run. Dit is de klasse waar u de toepassingsspecifieke logica van uw taak zult injecteren. Implementeer de methode Uitvoeren om:
  * Alle taakparameters ontleden en valideren
  * Stel de opdrachtregel samen voor elk extern programma dat u wilt aanroepen
  * Log alle diagnostische informatie die u nodig hebt voor debugging doeleinden
  * Een proces starten met die opdrachtregel
  * Wacht tot het proces is afgesloten
  * De afsluitcode van het proces vastleggen om te bepalen of het is gelukt of mislukt
  * Alle uitvoerbestanden opslaan die u wilt behouden voor permanente opslag

**Projectbestanden voor standaard .NET-opdrachtregel**

* `App.config`: Standaard configuratiebestand voor .NET-toepassingen.
* `Packages.config`: Standaard NuGet-pakketafhankelijkheidsbestand.
* `Program.cs`: Bevat het instappunt van het programma en de afhandeling van uitzonderingen op het hoogste niveau.

## <a name="implementing-the-task-processor"></a>De taakprocessor implementeren
Wanneer u het sjabloonproject Taakprocessor opent, wordt het TaskProcessor.cs bestand standaard geopend. U de uitvoeringslogica voor de taken in uw werkbelasting implementeren met de methode Run() hieronder weergegeven:

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
> De geannoteerde sectie in de methode Run() is het enige gedeelte van de sjablooncode Taakprocessor dat u moet wijzigen door de run-logica voor de taken in uw werkbelasting toe te voegen. Als u een ander gedeelte van de sjabloon wilt wijzigen, moet u eerst vertrouwd raken met hoe Batch werkt door de batchdocumentatie te bekijken en een paar van de batchcodevoorbeelden uit te proberen.
> 
> 

De methode Run() is verantwoordelijk voor het starten van de opdrachtregel, het starten van een of meer processen, wachten tot alle processen zijn voltooid, het opslaan van de resultaten en uiteindelijk terugkeren met een exitcode. Met de methode Run() implementeert u de verwerkingslogica voor uw taken. Het taakprocessorframework roept de methode Run() voor u op; je hoeft het niet zelf te noemen.

Uw Run()-implementatie heeft toegang tot:

* De taakparameters, `_parameters` via het veld.
* De taak- en taak-id's, via de `_jobId` velden en `_taskId` velden.
* De taakconfiguratie, `_configuration` via het veld.

**Taakfout**

In het geval van een storing u de methode Run() afsluiten door een uitzondering te maken, maar dit laat de uitzonderingshandler op het hoogste niveau de controle over de taakexitcode. Als u de exitcode moet beheren, zodat u verschillende soorten fouten onderscheiden, bijvoorbeeld voor diagnostische doeleinden of omdat sommige foutmodi de taak moeten beëindigen en andere niet, dan moet u de methode Run() afsluiten door een niet-nul exitcode. Dit wordt de taakexitcode.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Codes en uitzonderingen afsluiten in de sjabloon Taakprocessor
Exit codes en uitzonderingen bieden een mechanisme om de uitkomst van het uitvoeren van een programma te bepalen, en ze kunnen helpen bij het identificeren van eventuele problemen met de uitvoering van het programma. De sjabloon Taakprocessor implementeert de exitcodes en uitzonderingen die in deze sectie worden beschreven.

Een taakverwerkertaak die is geïmplementeerd met de sjabloon Taakprocessor, kan drie mogelijke exitcodes retourneren:

| Code | Beschrijving |
| --- | --- |
| [Process.ExitCode][process_exitcode] |De taakprocessor is voltooid. Houd er rekening mee dat dit niet betekent dat het programma dat u aanriep, is geslaagd , alleen dat de taakverwerker het heeft aangeroepen en elke nabewerking zonder uitzonderingen heeft uitgevoerd. De betekenis van de exitcode is afhankelijk van het aangeroepen programma - meestal exit code 0 betekent dat het programma geslaagd en een andere exit code betekent dat het programma is mislukt. |
| 1 |De taakprocessor is mislukt met een uitzondering in een 'verwacht' deel van het programma. De uitzondering werd `TaskProcessorException` vertaald naar een met diagnostische informatie en, waar mogelijk, suggesties voor het oplossen van de fout. |
| 2 |De taakprocessor is mislukt met een 'onverwachte' uitzondering. De uitzondering werd vastgelegd op standaarduitvoer, maar de taakprocessor kan geen aanvullende diagnostische of herstelgegevens toevoegen. |

> [!NOTE]
> Als het programma dat u aanroept exitcodes 1 en 2 gebruikt om specifieke foutmodi aan te geven, is het gebruik van exitcodes 1 en 2 voor fouten in taakverwerkers dubbelzinnig. U deze foutcodes voor taakbewerkingen wijzigen in onderscheidende exitcodes door de uitzonderingsgevallen in het Program.cs bestand te bewerken.
> 
> 

Alle informatie die door uitzonderingen wordt geretourneerd, wordt geschreven in stdout.txt- en stderr.txt-bestanden. Zie Foutafhandeling in de batchdocumentatie voor meer informatie.

### <a name="client-considerations"></a>Overwegingen van de klant
**Opslagreferenties**

Als uw taakprocessor Azure blob-opslag gebruikt om uitvoer voort te houden, bijvoorbeeld met behulp van de helperbibliotheek voor bestandsconventies, heeft deze toegang nodig *tot* de referenties van de cloudopslagaccount *of* een URL van blobcontainers met een SAS (Shared Access Signature). De sjabloon bevat ondersteuning voor het verstrekken van referenties via algemene omgevingsvariabelen. Uw klant kan de opslagreferenties als volgt doorgeven:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Het opslagaccount is vervolgens beschikbaar in `_configuration.StorageAccount` de klasse TaskProcessor via de eigenschap.

Als u liever een container-URL met SAS gebruikt, u dit ook doorgeven via een algemene taakinstelling, maar de sjabloon voor taakbewerker bevat momenteel geen ingebouwde ondersteuning hiervoor.

**Opslaginstellingen**

Het wordt aanbevolen dat de taak client of jobmanager containers maakt die nodig zijn voor taken voordat de taken aan de taak worden toegevoegd. Dit is verplicht als u een container-URL met SAS gebruikt, omdat een URL geen toestemming bevat om de container te maken. Het wordt aanbevolen, zelfs als u de referenties van opslagaccountdoorgeeft, omdat het elke taak opslaat die CloudBlobContainer moet aanroepen.CreateIfNotExistsAsync op de container.

## <a name="pass-parameters-and-environment-variables"></a>Parameters en omgevingsvariabelen doorgeven
### <a name="pass-environment-settings"></a>Omgevingsinstellingen doorgeven
Een klant kan informatie doorgeven aan de taak voor taak van de taak voor de taak van de taak in de vorm van omgevinginstellingen. Deze informatie kan vervolgens worden gebruikt door de taak taak voor taakbeheer bij het genereren van de taakprocessortaken die worden uitgevoerd als onderdeel van de rekentaak. Voorbeelden van de informatie die u doorgeven als omgevingsinstellingen zijn:

* Naam van het opslagaccount en accountsleutels
* URL batchaccount
* Batch-accountsleutel

De Batch-service heeft een eenvoudig mechanisme om omgevingsinstellingen `EnvironmentSettings` door te geven aan een taak voor taakvoor taak met behulp van de eigenschap in [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Als u bijvoorbeeld `BatchClient` de instantie voor een Batch-account wilt ophalen, u als omgevingsvariabelen van de clientcode de URL en gedeelde sleutelreferenties voor het Batch-account doorgeven. Als u toegang wilt krijgen tot het opslagaccount dat is gekoppeld aan het Batch-account, u ook de naam van het opslagaccount en de opslagaccountsleutel doorgeven als omgevingsvariabelen.

### <a name="pass-parameters-to-the-job-manager-template"></a>Parameters doorgeven aan de sjabloon Jobmanager
In veel gevallen is het handig om parameters per taak door te geven aan de taak taak taak voor taakbeheer, om het taaksplitsingsproces te beheren of om de taken voor de taak te configureren. U dit doen door een JSON-bestand met de naam parameters.json te uploaden als bronbestand voor de taak voor taakbeheer. De parameters kunnen dan `JobSplitter._parameters` beschikbaar komen in het veld in de sjabloon Jobmanager.

> [!NOTE]
> De ingebouwde parameterhandler ondersteunt alleen dia-to-string woordenboeken. Als u complexe JSON-waarden als parameterwaarden wilt doorgeven, moet u deze als tekenreeksen doorgeven en deze `Configuration.GetJobParameters` in de taaksplitter ontleden of de methode van het framework wijzigen.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Parameters doorgeven aan de sjabloon Taakprocessor
U ook parameters doorgeven aan afzonderlijke taken die zijn geïmplementeerd met de sjabloon Taakprocessor. Net als bij de sjabloon voor taakbeheer zoekt de sjabloon voor taakbewerker naar een bronbestand met de naam

parameters.json, en als gevonden laadt het als de parameters woordenboek. Er zijn een aantal opties voor het doorgeven van parameters aan de taken van de taakverwerker:

* De taakparameters JSON opnieuw gebruiken. Dit werkt goed als de enige parameters taakbrede parameters zijn (bijvoorbeeld een renderhoogte en -breedte). Voeg hiervoor bij het maken van een CloudTask in de taaksplitter een verwijzing toe naar het resourcebestandobject parameters.json van de ResourceFiles ( ) van de taak ResourceFiles van`JobSplitter._jobManagerTask.ResourceFiles`de taak ( ) aan de ResourceFiles-verzameling van CloudTask.
* Een taakspecifieke parameters.json-document genereren en uploaden als onderdeel van de uitvoering van taaksplitters en verwijs naar die blob in de verzameling resourcebestanden van de taak. Dit is nodig als verschillende taken verschillende parameters hebben. Een voorbeeld hiervan is een 3D-renderingscenario waarbij de frameindex als parameter aan de taak wordt doorgegeven.

> [!NOTE]
> De ingebouwde parameterhandler ondersteunt alleen dia-to-string woordenboeken. Als u complexe JSON-waarden als parameterwaarden wilt doorgeven, moet u deze als tekenreeksen doorgeven en deze `Configuration.GetTaskParameters` in de taakprocessor ontleden of de methode van het framework wijzigen.
> 
> 

## <a name="next-steps"></a>Volgende stappen
### <a name="persist-job-and-task-output-to-azure-storage"></a>Taak- en taakuitvoer naar Azure Storage blijven uitvoeren
Een ander handig hulpmiddel bij batchoplossingsontwikkeling is [Azure Batch File Conventions][nuget_package]. Gebruik deze .NET-klassenbibliotheek (momenteel in preview) in uw Batch .NET-toepassingen om takenuitvoer van en naar Azure Storage eenvoudig op te slaan en op te halen. [Persist Azure Batch-taak- en taakuitvoer](batch-task-output.md) bevat een volledige discussie over de bibliotheek en het gebruik ervan.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
