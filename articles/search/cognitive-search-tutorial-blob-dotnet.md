---
title: C#-zelfstudie met behulp van AI over Azure-blobs
titleSuffix: Azure Cognitive Search
description: Bekijk een voorbeeld van tekstextractie en natuurlijke taalverwerking over inhoud in blob-opslag met behulp van C# en de .NET SDK van Azure Cognitive Search.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 13e3f5a7d86d2e9b705fbeb104ba4f8eb690cb3a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534094"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Zelfstudie: Door AI gegenereerde doorzoekbare inhoud van Azure-blobs met behulp van de .NET SDK

Als u ongestructureerde tekst of afbeeldingen in Azure Blob-opslag hebt, kunt u met een [AI-verrijkingspijplijn](cognitive-search-concept-intro.md) informatie extraheren en nieuwe inhoud maken die nuttig is voor zoekopdrachten in volledige tekst of kennisanalyse. In deze C#-zelfstudie past u OCR (Optical Character Recognition) toe op afbeeldingen en voert u natuurlijke-taalverwerking uit om nieuwe velden te maken die u kunt gebruiken in query's, facetten en filters.

In deze zelfstudie wordt gebruikgemaakt van C# en de [.NET SDK](/dotnet/api/overview/azure/search) om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Begin met toepassingsbestanden en installatiekopieën in Azure Blob Storage.
> * Definieer een pijplijn om OCR, tekstextractie, taaldetectie, entiteits- en sleutelwoordherkenning toe te voegen.
> * Definieer een index voor het opslaan van de uitvoer (onbewerkte inhoud, plus pijplijn gegenereerde naam/waardeparen).
> * Voer de pijplijn uit om transformaties en analyses te starten en om de index te maken en te laden.
> * Bekijk de resultaten met zoeken in volledige tekst en een uitgebreide query-syntaxis.

Als u geen abonnement op Azure hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Maak](search-create-service-portal.md) of [vind een bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> U kunt de gratis service voor deze zelfstudie gebruiken. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerfuncties en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u aan de slag gaat, zorg ervoor dat uw service voldoende ruimte heeft voor de nieuwe resources.

## <a name="download-files"></a>Bestanden downloaden

1. Open deze [OneDrive-map](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) en klik in de linkerbovenhoek op **Downloaden** om de bestanden naar uw computer te kopiëren. 

1. Klik met de rechtermuisknop op het zip-bestand en selecteer **Alles extraheren**. Er zijn 14 bestanden van verschillende typen. U gebruikt 7 voor deze oefening.

U kunt ook de broncode voor deze zelfstudie downloaden. De broncode bevindt zich in de map tutorial-ai-enrichment, in de opslagplaats [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="1---create-services"></a>1 - Services maken

In deze zelfstudie maakt gebruik van Azure Cognitive Search voor het indexeren en uitvoeren van query's, Cognitive Services op de back-end voor AI-verrijking en Azure Blob-opslag om de gegevens op te geven. Deze zelfstudie bevindt zich onder de gratis toewijzing van 20 transacties per indexeerfunctie per dag op Cognitive Services, dus de enige services die u moet maken, zijn zoeken en opslag.

Maak, indien mogelijk, beide in dezelfde regio en resourcegroep voor nabijheid en beheerbaarheid. In de praktijk kan uw Azure Storage-account zich in een willekeurige regio bevinden.

### <a name="start-with-azure-storage"></a>Aan de slag met Azure Storage

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/) en klik op **En resourcegroepen maken**.

1. Zoek naar *Opslagaccount* en selecteer de aanbieding van het Microsoft-opslagaccount.

   ![Een opslagaccount maken](media/cognitive-search-tutorial-blob/storage-account.png "Een opslagaccount maken")

1. Op het tabblad Basisinstellingen zijn de volgende items vereist. Accepteer de standaardwaarden voor alle andere.

   + **Resourcegroep**. Selecteer een bestaande naam of maak een nieuwe, maar gebruik dezelfde groep voor alle services, zodat u ze gezamenlijk kunt beheren.

   + **Naam van opslagaccount**. Als u denkt dat u mogelijk meerdere resources van hetzelfde type hebt, gebruikt u de naam om op type en regio te onderscheiden, bijvoorbeeld *blobstoragewestus*. 

   + **Locatie**. Kies indien mogelijk dezelfde locatie die wordt gebruikt voor Azure Cognitive Search en Cognitive Services. Een enkele locatie vermindert bandbreedtekosten aanzienlijk.

   + **Soort account**. Kies de standaardinstelling *StorageV2 (algemeen gebruik v2)* .

1. Klik vervolgens op **Beoordelen en maken** om de service te maken.

1. Zodra de app is gemaakt, klikt u op **Ga naar de resource** om de pagina Overzicht te openen.

1. Klik op **Blobs**-service.

1. Klik op **En container** om een container te maken en geef deze de naam *cog-search-demo*.

1. Selecteer *cog-search-demo* en klik vervolgens op **Uploaden** om de map te openen waar u de downloadbestanden hebt opgeslagen. Selecteer alle veertien bestanden en klik op **OK** om ze te uploaden.

   ![Voorbeeldbestanden uploaden](media/cognitive-search-quickstart-blob/sample-data.png "Voorbeeldbestanden uploaden")

1. Voordat u Azure Storage verlaat, moet u een verbindingsreeks ophalen zodat u een verbinding in Azure Cognitive Search kunt formuleren. 

   1. Ga terug naar de pagina Overzicht van uw opslagaccount (we hebben *blobstoragewestus* als voorbeeld gebruikt). 
   
   1. Selecteer in het navigatiedeelvenster links de optie **Toegangstoetsen** en kopieer een van de verbindingsreeksen. 

   De verbindingsreeks is een URL die er ongeveer als volgt uitziet:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Sla de verbindingsreeks op in Kladblok. U hebt deze later nodig bij het instellen van de gegevensbronverbinding.

### <a name="cognitive-services"></a>Cognitive Services

AI-verrijking wordt ondersteund door Cognitive Services, waaronder Text Analytics en Computer Vision voor de verwerking van natuurlijke taal en afbeeldingen. Als u een echt prototype of project wilt voltooien, moet u op dit punt Cognitive Services (in dezelfde regio als Azure Cognitive Search) inrichten, zodat u het kunt koppelen aan indexbewerkingen.

Voor deze oefening kunt u echter het inrichten van resources overslaan omdat Azure Cognitive Search verbinding kan maken met Cognitive Services achter de schermen en u 20 gratis transacties per uitvoering van indexeerfunctie geeft. Omdat in deze zelfstudie veertien transacties worden gebruikt, is de gratis toewijzing voldoende. Voor grotere projecten plant u het inrichten van Cognitive Services op de S0-laag voor betalen per gebruik. Zie voor meer informatie [Cognitive Services koppelen](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Het derde onderdeel is Azure Cognitive Search, dat u kunt [maken in de portal](search-create-service-portal.md). U kunt de gratis laag gebruiken om dit voorbeeld te voltooien. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en URL voor Azure Cognitive Search ophalen

Als u wilt communiceren met uw Azure Cognitive Search-service, hebt u de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal ook de querysleutel op. Het is een aanbevolen procedure voor het uitgeven van queryaanvragen met alleen-lezen-toegang.

   ![De naam van de service en de querysleutels voor beheer ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-your-environment"></a>2 - De omgeving instellen

Open eerst Visual Studio en maak een nieuw Console App-project dat kan worden uitgevoerd op .NET Core.

### <a name="install-nuget-packages"></a>NuGet-pakketten installeren

De [.NET SDK van Azure Cognitive Search](/dotnet/api/overview/azure/search) bestaat uit een aantal clientbibliotheken waarmee u uw indexen, gegevensbronnen, indexeerfuncties en vaardighedensets kunt beheren U kunt ook documenten uploaden en beheren en query's uitvoeren, zonder dat u zich hoeft bezig te houden met de details van HTTP en JSON. Deze clientbibliotheken worden allemaal gedistribueerd als NuGet-pakketten.

Voor dit project installeert u versie 9 of hoger van NuGet-pakket `Microsoft.Azure.Search`.

1. Ga in een browser naar de [pagina met NuGet-pakket Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Selecteer de nieuwste versie (9 of nieuwer).

1. Kopieer de opdracht Package Manager.

1. Open de Package Manager-console. Selecteer **Hulpprogramma's** > **NuGet Package Manager** > **Package Manager-console**. 

1. Plak en voer de opdracht uit die u in de vorige stap hebt gekopieerd.

Installeer vervolgens het nieuwste NuGet-pakket `Microsoft.Extensions.Configuration.Json`.

1. Selecteer **Hulpprogramma's** > **NuGet Package Manager** > **NuGet-pakketten voor oplossing...** . 

1. Klik op **Bladeren** en zoek NuGet-pakket `Microsoft.Extensions.Configuration.Json`. 

1. Selecteer het pakket, selecteer uw project, bevestig dat de versie de nieuwste stabiele versie. Klik vervolgens op **Installeren**.

### <a name="add-service-connection-information"></a>Verbindingsgegevens van de service toevoegen

1. Klik met de rechtermuisknop in Solution Explorer en selecteer **Toevoegen** > **Nieuw item...** . 

1. Geef het bestand de naam `appsettings.json` en selecteer **Toevoegen**. 

1. Neem dit bestand op in de uitvoermap.
    1. Klik met de rechtermuisknop op `appsettings.json` en selecteer **Eigenschappen**. 
    1. Wijzig de waarde van **Naar uitvoermap kopiëren** in **Kopiëren indien nieuwer**.

1. Kopieer de onderstaande JSON in uw nieuwe JSON-bestand.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Voeg de gegevens van uw zoekservice en blob-opslagaccount toe. U kunt deze informatie ophalen via de stappen voor het inrichten van de service, zoals aangegeven in de vorige sectie.

Voor **SearchServiceName** voert u de korte servicenaam in (niet de volledige URL).

### <a name="add-namespaces"></a>Naamruimten toevoegen

Voeg in `Program.cs` de volgende naamruimten toe.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Client maken

Maak onder `Main` een exemplaar van de klasse `SearchServiceClient`.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` maakt een nieuwe `SearchServiceClient` met behulp van waarden die zijn opgeslagen in het configuratiebestand van de toepassing (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> De klasse `SearchServiceClient` beheert verbindingen met uw zoekservice. Als u wilt voorkomen dat er te veel verbindingen worden geopend, kunt u proberen één instantie van `SearchServiceClient` in uw toepassing te delen, indien mogelijk. De bijbehorende methoden zijn thread-safe om delen in te schakelen.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Functie toevoegen om het programma te sluiten tijdens een fout

Deze zelfstudie is bedoeld om u inzicht te geven in elke stap van de indexeringspijplijn. Als er een kritiek probleem is waarmee wordt voorkomen dat het programma de gegevensbron, vaardighedenset, index of indexeerfunctie kan maken, wordt het foutbericht uitgevoerd en wordt het programma afgesloten, zodat er inzicht in het probleem kan worden verkregen en het kan worden opgelost.

Voeg `ExitProgram` toe aan `Main` voor het afhandelen van scenario's waarvoor het programma moet worden afgesloten.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 - De pijplijn maken

In Azure Cognitive Search treedt AI-verwerking op tijdens het indexeren (of gegevensopname). In dit deel van het scenario worden vier objecten gemaakt: gegevensbron, indexdefinitie, vaardighedenset, indexeerfunctie. 

### <a name="step-1-create-a-data-source"></a>Stap 1: Een gegevensbron maken

`SearchServiceClient` heeft een `DataSources`-eigenschap. Deze beschikt biedt alle methoden die u nodig hebt om een gegevensbronnen voor Azure Cognitive Search te maken, weer te geven, bij te werken of te verwijderen.

Maak een nieuw `DataSource`-exemplaar door `serviceClient.DataSources.CreateOrUpdate(dataSource)` aan te roepen. Voor `DataSource.AzureBlobStorage` is vereist dat u de naam van de gegevensbron, de verbindingsreeks en de naam van de blob-container opgeeft.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "cog-search-demo",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

Als de aanvraag is geslaagd, wordt de gemaakte gegevensbron geretourneerd. Als er een probleem is met de aanvraag, bijvoorbeeld een ongeldige parameter, wordt er een uitzondering gegenereerd.

Voeg nu een regel toe aan `Main` om de `CreateOrUpdateDataSource`-functie aan te roepen die u zojuist hebt toegevoegd.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```

Bouw de oplossing en voer deze uit. Aangezien dit uw eerste aanvraag is, controleert u de Azure-portal om te bevestigen dat de gegevensbron in Azure Cognitive Search is gemaakt. Controleer op de dashboardpagina van de zoekservice of de tegel Gegevensbronnen een nieuw item heeft. U moet mogelijk een paar minuten wachten tot de portalpagina is vernieuwd.

  ![De tegel gegevensbronnen in de portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "De tegel gegevensbronnen in de portal")

### <a name="step-2-create-a-skillset"></a>Stap 2: Een set vaardigheden maken

In deze stap definieert u een reeks verrijkingsstappen die u op uw gegevens wilt toepassen. Elke verrijkingsstap wordt een *vaardigheid* genoemd en de reeks verrijkingsstappen een *vaardighedenset*. In deze zelfstudie wordt gebruikgemaakt van [ingebouwde cognitieve tekstvaardigheden](cognitive-search-predefined-skills.md) voor de set vaardigheden:

+ [Optical Character Recognition](cognitive-search-skill-ocr.md), voor het herkennen van gedrukte en handgeschreven tekst in afbeeldingsbestanden.

+ [Tekst samenvoegen](cognitive-search-skill-textmerger.md), om tekst uit een verzameling velden in één veld samen te voegen.

+ [Taaldetectie](cognitive-search-skill-language-detection.md) om de taal van de inhoud vast te stellen.

+ [Tekst splitsen](cognitive-search-skill-textsplit.md) om grote inhoud in kleinere stukken op te delen voordat u de vaardigheid voor sleuteltermextractie en die voor entiteitsherkenning aanroept. Sleuteltermextractie en entiteitsherkenning accepteren een invoer van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen.

+ [Herkenning van entiteiten](cognitive-search-skill-entity-recognition.md) voor het extraheren van de namen van organisaties uit inhoud van de blobcontainer.

+ [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) voor het ophalen van de belangrijkste sleuteltermen.

Tijdens de initiële verwerking opent Azure Cognitive Search elk document om inhoud van verschillende bestandsindelingen te lezen. Gevonden tekst uit het bronbestand wordt in een gegenereerd ```content```-veld geplaatst, één voor elk document. Stel de invoer als ```"/document/content"``` in om deze tekst te gebruiken. 

Uitvoeren kunnen aan een index worden toegewezen, als invoer worden gebruikt voor een downstream-vaardigheid, of beide zoals bij taalcode. Een taalcode is in de index handig om te filteren. Taalcode wordt als invoer door vaardigheden voor tekstanalyse gebruikt om de taalregels voor woordafbreking in te stellen.

Zie [How to define a skillset](cognitive-search-defining-skillset.md) (Een set vaardigheden definiëren) voor meer informatie over de grondbeginselen van vaardigheden.

### <a name="ocr-skill"></a>Vaardigheid OCR

De vaardigheid **OCR** haalt tekst op uit afbeeldingen. Bij deze vaardigheid wordt ervan uitgegaan dat er een veld normalized_images bestaat. Als u dit veld wilt genereren, stelt u verderop in de zelfstudie de ```"imageAction"```-configuratie in de definitie van de indexeerfunctie in op ```"generateNormalizedImages"```.

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "text",
        targetName: "text")
    };

    OcrSkill ocrSkill = new OcrSkill(
        description: "Extract text (plain and structured) from image",
        context: "/document/normalized_images/*",
        inputs: inputMappings,
        outputs: outputMappings,
        defaultLanguageCode: OcrSkillLanguage.En,
        shouldDetectOrientation: true);

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Vaardigheid Samenvoegen

In deze sectie maakt u een vaardigheid **Samenvoeg** die het veld documentinhoud samenvoegt met de tekst die door de vaardigheid OCR is geproduceerd.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"),
        new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"),
        new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text")
    };

    MergeSkill mergeSkill = new MergeSkill(
        description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        insertPreTag: " ",
        insertPostTag: " ");

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Vaardigheid Taaldetectie

De vaardigheid **Taaldetectie** detecteert in welke taal de ingevoerde tekst is geschreven en rapporteert één taalcode voor elk document dat bij de aanvraag is ingediend. De uitvoer van de vaardigheid **Taaldetectie** wordt gebruikt als onderdeel van de invoer voor de vaardigheid **Tekst splitsen**.

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode")
    };

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Vaardigheid Tekst splitsen

Met de onderstaande Vaardigheid **Splitsen** wordt de tekst op pagina's gesplitst en wordt de paginalengte beperkt tot 4000 tekens, zoals gemeten door `String.Length`. Het algoritme splitst de tekst in fragmenten die maximaal `maximumPageLength` groot zijn. In dit geval zal het algoritme de zin afbreken op de grens van een zin, zodat de grootte van het fragment iets kleiner dan `maximumPageLength` kan zijn.

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"),
        new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages")
    };

    SplitSkill splitSkill = new SplitSkill(
        description: "Split content into pages",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        textSplitMode: TextSplitMode.Pages,
        maximumPageLength: 4000);

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Vaardigheid Entiteitsherkenning

Dit `EntityRecognitionSkill`-exemplaar is ingesteld om categorietype `organization` te herkennen. De vaardigheid **Entiteitherkenning** kan ook de categorietypen `person` en `location` herkennen.

U ziet dat het 'contextveld' is ingesteld op ```"/document/pages/*"``` met een asterisk, wat betekent dat de verrijkingsstap wordt aangeroepen voor elke pagina onder ```"/document/pages"```.

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>
    {
        new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*")
    };

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>
    {
        new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations")
    };

    List<EntityCategory> entityCategory = new List<EntityCategory>
    {
        EntityCategory.Organization
    };

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
        description: "Recognize organizations",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings,
        categories: entityCategory,
        defaultLanguageCode: EntityRecognitionSkillLanguage.En);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Vaardigheid Sleuteltermextractie

Net als voor het `EntityRecognitionSkill`-exemplaar dat zojuist is gemaakt, wordt de vaardigheid **Sleuteltermextractie** voor elke pagina van het document aangeroepen.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "keyPhrases",
        targetName: "keyPhrases"));

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
        description: "Extract the key phrases",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings);

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>De vaardighedenset compileren en maken

Compileer de `Skillset` met behulp van de vaardigheden die u hebt gemaakt.

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Voeg de volgende regels toe aan `Main`.

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>
    {
        ocrSkill,
        mergeSkill,
        languageDetectionSkill,
        splitSkill,
        entityRecognitionSkill,
        keyPhraseExtractionSkill
    };

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>Stap 3: Een index maken

In dit gedeelte kunt u het indexschema definiëren door op te geven welke velden in de doorzoekbare index moeten worden opgenomen en de zoekkenmerken voor elk veld op te geven. Velden hebben een type en kunnen kenmerken opnemen die bepalen hoe het veld wordt gebruikt (doorzoekbaar, sorteerbaar enzovoort). Veldnamen in een index hoeven niet identiek te zijn aan de veldnamen in de bron. In een latere stap voegt u veldverwijzingen in een indexeerfunctie toe om bron-doelvelden te verbinden. Definieer voor deze stap de index met behulp van veldnaamconventies die relevant zijn voor uw zoektoepassing.

In deze oefening worden de volgende velden en veldtypen gebruikt:

| Veldnamen | Veldtypen |
| --- | --- |
| `id` | Edm.String |
| `content` | Edm.String |
| `languageCode` | Edm.String |
| `keyPhrases` | List<Edm.String> |
| `organizations` | List<Edm.String> |

#### <a name="create-demoindex-class"></a>Klasse DemoIndex maken

De velden voor deze index worden gedefinieerd met behulp van een modelklasse. Elke eigenschap van de modelklasse heeft kenmerken die het zoekgerelateerde gedrag van het bijbehorende indexveld bepalen. 

De modelklasse wordt toegevoegd aan een nieuw C#-bestand. Klik met de rechtermuisknop op het project en selecteer **Toevoegen** > **Nieuw item...** , selecteer Klasse en geef het bestand de naam `DemoIndex.cs`. Selecteer vervolgens **Toevoegen**.

Geef aan dat u typen wilt gebruiken van de naamruimten `Microsoft.Azure.Search` en `Microsoft.Azure.Search.Models`.

Voeg de onderstaande definitie van de modelklasse toe aan `DemoIndex.cs` en neem deze op in dezelfde naamruimte waarin u de index gaat maken.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
    // It ensures that Pascal-case property names in the model class are mapped to camel-case
    // field names in the index.
    [SerializePropertyNamesAsCamelCase]
    public class DemoIndex
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsSearchable, IsSortable]
        public string Id { get; set; }

        [IsSearchable]
        public string Content { get; set; }

        [IsSearchable]
        public string LanguageCode { get; set; }

        [IsSearchable]
        public string[] KeyPhrases { get; set; }

        [IsSearchable]
        public string[] Organizations { get; set; }
    }
}
```

Nu u een modelklasse hebt gedefinieerd, kunt u in `Program.cs` vrij eenvoudig een indexdefinitie maken. De naam voor deze index wordt `demoindex`. Als er al een index met deze naam bestaat, wordt deze verwijderd.

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

    try
    {
        bool exists = serviceClient.Indexes.Exists(index.Name);

        if (exists)
        {
            serviceClient.Indexes.Delete(index.Name);
        }

        serviceClient.Indexes.Create(index);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Tijdens het testen is het mogelijk dat u de index meer dan één keer maakt. Controleer daarom of de index die u gaat maken al bestaat voordat u deze wilt maken.

Voeg de volgende regels toe aan `Main`.

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Microsoft.Azure.Search.Models.Index demoIndex = CreateDemoIndex(serviceClient);
```

Voeg de volgende using-instructie toe om de dubbelzinnige verwijzing te corrigeren.

```csharp
using Index = Microsoft.Azure.Search.Models.Index;
```

Zie [Create Index (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index) (Index maken (Azure Cognitive Search REST API)) voor meer informatie over het definiëren van een index.

### <a name="step-4-create-and-run-an-indexer"></a>Stap 4: Indexeerfunctie maken en uitvoeren

Tot nu toe hebt u een gegevensbron, een set vaardigheden en een index gemaakt. Deze drie onderdelen gaan deel uitmaken van een [indexeerfunctie](search-indexer-overview.md) die de onderdelen combineert in één meervoudige bewerking. Als u deze in een indexeerfunctie wilt combineren, moet u veldtoewijzingen definiëren.

+ De fieldMappings worden vóór de vaardighedenset verwerkt, waarbij bronvelden van de gegevensbron worden toegewezen aan de doelvelden in een index. Als veldnamen en -typen gelijk zijn aan beide uiteinden, is toewijzing niet vereist.

+ De outputFieldMappings worden verwerkt na de vaardighedenset en verwijzen naar sourceFieldNames die niet bestaan totdat het document wordt gekraakt of verrijkt. De targetFieldName is een veld in een index.

Naast het koppelen van invoer naar uitvoer, kunt u ook veldtoewijzingen gebruiken om gegevensstructuren samen te voegen. Zie [Verrijkte velden toewijzen aan een doorzoekbare index](cognitive-search-output-field-mapping.md) voor meer informatie.

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
    IDictionary<string, object> config = new Dictionary<string, object>();
    config.Add(
        key: "dataToExtract",
        value: "contentAndMetadata");
    config.Add(
        key: "imageAction",
        value: "generateNormalizedImages");

    List<FieldMapping> fieldMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")),
        new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content")
    };

    List<FieldMapping> outputMappings = new List<FieldMapping>
    {
        new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"),
        new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"),
        new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode")
    };

    Indexer indexer = new Indexer(
        name: "demoindexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        description: "Demo Indexer",
        skillsetName: skillSet.Name,
        parameters: new IndexingParameters(
            maxFailedItems: -1,
            maxFailedItemsPerBatch: -1,
            configuration: config),
        fieldMappings: fieldMappings,
        outputFieldMappings: outputMappings);

    try
    {
        bool exists = serviceClient.Indexers.Exists(indexer.Name);

        if (exists)
        {
            serviceClient.Indexers.Delete(indexer.Name);
        }

        serviceClient.Indexers.Create(indexer);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
Voeg de volgende regels toe aan `Main`.

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer and executing the pipeline...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Het kan even duren voordat de indexeerfunctie is voltooid. De gegevensset is klein, maar analytische vaardigheden zijn berekeningsintensief. Sommige vaardigheden, zoals afbeeldingsanalyse, zijn langlopend.

> [!TIP]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er problemen zijn met het bereiken van de gegevens, het toewijzen van invoeren en uitvoeren of de volgorde van bewerkingen, worden ze in deze fase weergegeven.

### <a name="explore-creating-the-indexer"></a>Indexeerfunctie maken

De code stelt ```"maxFailedItems"``` op -1 in, wat de indexeerengine de opdracht geeft om fouten tijdens het importeren van gegevens te negeren. Dit is nuttig omdat de demo-gegevensbron maar een paar documenten bevat. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

U ziet ook dat de ```"dataToExtract"``` is ingesteld op ```"contentAndMetadata"```. Deze instructie geeft de indexeerfunctie de opdracht om automatisch de inhoud van verschillende bestandsindelingen te extraheren, evenals metagegevens voor elk bestand.

Wanneer inhoud wordt uitgepakt, kunt u instellen dat `imageAction` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. Met de configuratie waarbij ```"imageAction"``` is ingesteld op ```"generateNormalizedImages"```, in combinatie met de vaardigheid OCR en de vaardigheid Tekst samenvoegen, krijgt de indexeerfunctie de opdracht om tekst uit de afbeeldingen te extraheren (bijvoorbeeld het woord 'stop' van een Stop-verkeersbord) en deze in te sluiten als onderdeel van het inhoudsveld. Dit gedrag is van toepassing op zowel de afbeeldingen die zijn ingesloten in de documenten (zoals een afbeelding in een pdf-bestand) als afbeeldingen die zijn gevonden in de gegevensbron, zoals een JPG-bestand.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4: Het indexeren bewaken

Nadat de indexeerfunctie is gedefinieerd, wordt deze automatisch uitgevoerd wanneer u de aanvraag verzendt. Afhankelijk van welke cognitieve vaardigheden u hebt gedefinieerd, kan het indexeren langer duren dan verwacht. Als u wilt weten of de indexeerfunctie nog steeds wordt uitgevoerd, gebruikt u de methode `GetStatus`.

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

Met `IndexerExecutionInfo` wordt de huidige status en de uitvoeringsgeschiedenis van een indexeerfunctie aangegeven.

Waarschuwingen komen veel voor bij sommige combinaties van bronbestand en vaardigheid en wijzen niet altijd op een probleem. In deze zelfstudie zijn de waarschuwingen onschadelijk (bijvoorbeeld geen tekstinvoeren van de JPEG-bestanden).

Voeg de volgende regels toe aan `Main`.

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - Zoeken

U kunt, nadat het indexeren is voltooid, query's uitvoeren waardoor de inhoud van afzonderlijke velden wordt geretourneerd. Standaard retourneert Azure Cognitive Search de 50 hoogste resultaten. De voorbeeldgegevens zijn beperkt, dus de standaardinstelling werkt prima. Als u echter werkt met grotere gegevenssets, moet u mogelijk parameters opnemen in de queryreeks om meer resultaten te retourneren. Zie [How to page results in Azure Cogntive Search](search-pagination-page-layout.md) (Resultaten genereren in Azure Cognitive Search) voor instructies.

Voer als verificatiestap query's uit op de index voor alle velden.

Voeg de volgende regels toe aan `Main`.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` maakt een nieuwe `SearchIndexClient` met behulp van waarden die zijn opgeslagen in het configuratiebestand van de toepassing (appsettings.json). U ziet dat de query-API-sleutel van de zoekservice wordt gebruikt en niet de Admin-sleutel.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Voeg de volgende code toe aan `Main`. De eerste try-catch-instructie retourneert de indexdefinitie, met de naam, het type en de kenmerken van elk veld. De tweede is een geparameteriseerde query, waarbij `Select` opgeeft welke velden moeten worden meegenomen in de resultaten, bijvoorbeeld `organizations`. Een zoekreeks van `"*"` retourneert de hele inhoud van één veld.

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

Herhaal dit voor andere velden: inhoud, taalcode, sleuteltermen en organisaties in deze oefening. Met behulp van een door komma's gescheiden lijst kunt meerdere velden retourneren via de eigenschap [Select](/dotnet/api/microsoft.azure.search.models.searchparameters.select).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de eerste experimentele fasen van ontwikkeling, is de meest praktische aanpak voor ontwerpiteraties om de objecten uit Azure Cognitive Search te verwijderen en uw code ze te laten herbouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

In de voorbeeldcode voor deze zelfstudie wordt gecontroleerd op bestaande objecten. Deze worden verwijderd, zodat u de code opnieuw kunt uitvoeren.

U kunt de portal ook gebruiken om indexen, indexeerfuncties, gegevensbronnen en vaardighedensets te verwijderen.

## <a name="takeaways"></a>Opgedane kennis

In deze zelfstudie zijn de basisstappen voor het bouwen van een pijplijn voor verrijkte indexering gedemonstreerd via het maken van onderdelen: een gegevensbron, een vaardighedenset, een index en een indexeerfunctie.

[Ingebouwde vaardigheden](cognitive-search-predefined-skills.md) zijn geïntroduceerd, samen met de definitie van de vaardighedenset en het mechanisme voor het koppelen van vaardigheden via in- en uitvoer. U hebt ook geleerd dat `outputFieldMappings` vereist is in de definitie van de indexeerfunctie voor de routering van verrijkte waarden uit de pijplijn naar een doorzoekbare index op een Azure Cognitive Search-service.

Tot slot hebt u geleerd hoe u resultaten kunt testen en het systeem opnieuw kunt instellen voor verdere iteraties. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. U hebt ook geleerd hoe u de status van de indexeerfunctie kunt controleren en welke objecten u moet verwijderen voordat u een pijplijn opnieuw uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt en of u deze moet verwijderen. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling Alle resources of Resourcegroepen in het navigatiedeelvenster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met alle objecten in een AI-verrijkingspijplijn, gaan we kijken naar de definities van vaardighedensets en individuele vaardigheden.

> [!div class="nextstepaction"]
> [Een vaardighedenset maken](cognitive-search-defining-skillset.md)