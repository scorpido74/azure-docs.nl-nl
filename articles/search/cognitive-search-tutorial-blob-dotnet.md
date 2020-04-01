---
title: 'Zelfstudie: C# en AI over Azure-blobs'
titleSuffix: Azure Cognitive Search
description: Stap door een voorbeeld van tekstextractie en verwerking van natuurlijke taal over inhoud in Blob-opslag met C# en de Azure Cognitive Search .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 169a33d12e98235dcb4e4f317dbb8d91eb7446a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78851135"
---
# <a name="tutorial-use-c-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Zelfstudie: C# en AI gebruiken om doorzoekbare inhoud uit Azure-blobs te genereren

Als u ongestructureerde tekst of afbeeldingen in Azure Blob-opslag hebt, kan een [AI-verrijkingspijplijn](cognitive-search-concept-intro.md) informatie extraheren en nieuwe inhoud maken die handig is voor scenario's voor zoeken in volledige tekst of kennismijnbouw. Pas in deze C#-zelfstudie OCR (Optical Character Recognition) toe op afbeeldingen en voer natuurlijke taalverwerking uit om nieuwe velden te maken die u gebruiken in query's, facetten en filters.

In deze zelfstudie worden C# en de [.NET SDK](https://aka.ms/search-sdk) gebruikt om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Begin met toepassingsbestanden en afbeeldingen in Azure Blob-opslag.
> * Definieer een pijplijn om OCR, tekstextractie, taaldetectie, entiteit en herkenning van sleutelzinnen toe te voegen.
> * Definieer een index om de uitvoer op te slaan (ruwe inhoud, plus door pijplijn gegenereerde naamwaardeparen).
> * Voer de pijplijn uit om transformaties en analyses te starten en de index te maken en te laden.
> * Bekijk de resultaten met behulp van zoeken met volledige tekst en een syntaxis van uitgebreide query's.

Als u geen Azure-abonnement hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Een](search-create-service-portal.md) [bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) maken of zoeken 

> [!Note]
> U gebruik maken van de gratis service voor deze tutorial. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerders en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u begint, moet u ervoor zorgen dat u ruimte hebt op uw service om de nieuwe bronnen te accepteren.

## <a name="download-files"></a>Bestanden downloaden

1. Open deze [OneDrive-map](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) en klik in de linkerbovenhoek op **Downloaden** om de bestanden naar uw computer te kopiëren. 

1. Klik met de rechtermuisknop op het zip-bestand en selecteer **Alles uitpakken**. Er zijn 14 bestanden van verschillende soorten. Gebruik ze allemaal voor deze tutorial.

## <a name="1---create-services"></a>1 - Services maken

Deze zelfstudie maakt gebruik van Azure Cognitive Search voor indexering en query's, Cognitive Services op de backend voor AI-verrijking en Azure Blob-opslag om de gegevens te verstrekken. Deze zelfstudie blijft onder de gratis toewijzing van 20 transacties per indexer per dag op Cognitive Services, dus de enige services die u moet maken zijn zoeken en opslaan.

Maak indien mogelijk zowel in dezelfde regio als in de resourcegroep voor nabijheid en beheerbaarheid. In de praktijk kan uw Azure Storage-account zich in elke regio bevinden.

### <a name="start-with-azure-storage"></a>Beginnen met Azure Storage

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/) en klik op **+ Resource maken.**

1. Zoek naar *een opslagaccount* en selecteer het opslagaccountaanbod van Microsoft.

   ![Opslagaccount maken](media/cognitive-search-tutorial-blob/storage-account.png "Opslagaccount maken")

1. Op het tabblad Basisbeginselen zijn de volgende items vereist. Accepteer de standaardinstellingen voor al het andere.

   + **Resourcegroep**. Selecteer een bestaande of maak een nieuwe, maar gebruik dezelfde groep voor alle services, zodat u ze collectief beheren.

   + **Naam van het opslagaccount**. Als u denkt dat u meerdere bronnen van hetzelfde type hebt, gebruikt u de naam om disambiguate te disambiguateop per type en regio, bijvoorbeeld *blobstoragewestus*. 

   + **Locatie**. Kies indien mogelijk dezelfde locatie die wordt gebruikt voor Azure Cognitive Search en Cognitive Services. Een enkele locatie vervalt bandbreedtekosten.

   + **Account soort**. Kies de standaardinstelling *StorageV2 (v2 voor algemeen gebruik).*

1. Klik **op Controleren + Maken** om de service te maken.

1. Zodra deze is gemaakt, klikt u op **Ga naar de resource om** de pagina Overzicht te openen.

1. Klik **op Blobs-service.**

1. Klik **op + Container** om een container te maken en deze *basic-demo-data-pr een*naam te geven.

1. Selecteer *basic-demo-data-pr* en klik op **Uploaden** om de map te openen waar u de downloadbestanden hebt opgeslagen. Selecteer alle veertien bestanden en klik op **OK** om te uploaden.

   ![Voorbeeldbestanden uploaden](media/cognitive-search-quickstart-blob/sample-data.png "Voorbeeldbestanden uploaden")

1. Voordat u Azure Storage verlaat, krijgt u een verbindingstekenreeks zodat u een verbinding formuleren in Azure Cognitive Search. 

   1. Blader terug naar de overzichtspagina van uw opslagaccount (we *gebruikten blobstragewestus* als voorbeeld). 
   
   1. Selecteer **access-toetsen** in het linkernavigatiedeelvenster en kopieer een van de verbindingstekenreeksen. 

   De verbindingstekenreeks is een URL die lijkt op het volgende voorbeeld:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Sla de verbindingstekenreeks op in Kladblok. U hebt het later nodig bij het instellen van de gegevensbronverbinding.

### <a name="cognitive-services"></a>Cognitive Services

AI-verrijking wordt ondersteund door Cognitive Services, waaronder Text Analytics en Computer Vision voor natuurlijke taal- en beeldverwerking. Als het doel was om een echt prototype of project te voltooien, zou u op dit punt cognitieve services inrichten (in dezelfde regio als Azure Cognitive Search), zodat u het koppelen aan indexeringsbewerkingen.

Voor deze oefening u echter resourceprovisioning overslaan omdat Azure Cognitive Search achter de schermen verbinding kan maken met Cognitive Services en u 20 gratis transacties per indexerrun geven. Aangezien deze zelfstudie 7 transacties gebruikt, is de gratis toewijzing voldoende. Voor grotere projecten, plannen op het inrichten van Cognitive Services op de pay-as-you-go S0 laag. Zie [Cognitieve services bijvoegen](cognitive-search-attach-cognitive-services.md)voor meer informatie.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

De derde component is Azure Cognitive Search, die u [maken in de portal.](search-create-service-portal.md) U de gratis laag gebruiken om deze walkthrough te voltooien. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een api-sleutel voor beheerders en URL ophalen voor Azure Cognitive Search

Als u wilt communiceren met uw Azure Cognitive Search-service, hebt u de service-URL en een toegangssleutel nodig. Er wordt een zoekservice gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde informatie te krijgen:

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/)en ontvang de URL op de pagina **Overzicht** van uw zoekservice. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Ontvang **in Instellingentoetsen** > **Keys**een beheersleutel voor volledige rechten op de service. Er zijn twee verwisselbare beheerderssleutels, voorzien voor bedrijfscontinuïteit voor het geval u er een moet omdraaien. U de primaire of secundaire sleutel gebruiken voor aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal de querysleutel ook. Het is een aanbevolen manier om queryaanvragen uit te geven met alleen-lezen toegang.

   ![De servicenaam en -beheerder en querysleutels ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-your-environment"></a>2 - Uw omgeving instellen

Begin met het openen van Visual Studio en het maken van een nieuw Console App-project dat kan worden uitgevoerd op .NET Core.

### <a name="install-nuget-packages"></a>NuGet-pakketten installeren

De [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) bestaat uit een paar clientbibliotheken waarmee u uw indexen, gegevensbronnen, indexers en skillsets beheren, evenals documenten uploaden en beheren en query's uitvoeren, zonder dat u de details van HTTP en JSON hoeft te behandelen. Deze clientbibliotheken worden allemaal gedistribueerd als NuGet-pakketten.

Installeer voor dit project versie 9 `Microsoft.Azure.Search` of hoger van het NuGet-pakket.

1. Open de Package Manager Console. Selecteer **Tools** > **NuGet Package Manager** > **Package Manager Console**. 

1. Navigeer naar [de pakketpagina Microsoft.Azure.Search NuGet.](https://www.nuget.org/packages/Microsoft.Azure.Search)

1. Selecteer de nieuwste versie (9 of hoger).

1. Kopieer de opdracht Package Manager.

1. Ga terug naar de console Package Manager en voer de opdracht uit die u in de vorige stap hebt gekopieerd.

Installeer vervolgens het `Microsoft.Extensions.Configuration.Json` nieuwste NuGet-pakket.

1. Selecteer **Tools** > **NuGet Package Manager** > **Manage NuGet Packages for Solution...**. 

1. Klik **op Bladeren** `Microsoft.Extensions.Configuration.Json` en zoeken naar het NuGet-pakket. 

1. Selecteer het pakket, selecteer uw project, bevestig dat de versie de nieuwste stabiele versie is en klik op **Installeren.**

### <a name="add-service-connection-information"></a>Serviceverbindingsgegevens toevoegen

1. Klik met de rechtermuisknop op uw project in de Solution Explorer en selecteer Nieuw item **toevoegen...** > **New Item...** . 

1. Geef het `appsettings.json` bestand een naam en selecteer **Toevoegen**. 

1. Neem dit bestand op in uw uitvoermap.
    1. Klik met `appsettings.json` de rechtermuisknop op eigenschappen en selecteer **Eigenschappen**. 
    1. Wijzig de waarde van **Kopiëren in uitvoermap** in **Kopiëren als nieuwere**.

1. Kopieer de onderstaande JSON in uw nieuwe JSON-bestand.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Voeg uw zoekservice en blob-opslagaccountgegevens toe. Bedenk dat u deze informatie ophalen uit de stappen voor het inrichten van de service die in de vorige sectie zijn aangegeven.

### <a name="add-namespaces"></a>Naamruimten toevoegen

Voeg `Program.cs`daarin de volgende naamruimten toe.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Een client maken

Maak een instantie `SearchServiceClient` van `Main`de klasse onder .

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`hiermee wordt `SearchServiceClient` een nieuwe waarde gemaakt die is opgeslagen in het config-bestand van de toepassing (appsettings.json).

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

### <a name="add-function-to-exit-the-program-during-failure"></a>Functie toevoegen om het programma af te sluiten tijdens het mislukken

Deze zelfstudie is bedoeld om u te helpen begrijpen elke stap van de indexering pijplijn. Als er een kritiek probleem is waardoor het programma de gegevensbron, skillset, index of indexer niet kan maken, wordt het programma het foutbericht uitgevoerd en wordt afgesloten, zodat het probleem kan worden begrepen en opgelost.

Toevoegen `ExitProgram` `Main` aan om scenario's af te handelen waarvoor het programma moet worden afgesloten.

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

In Azure Cognitive Search vindt AI-verwerking plaats tijdens het indexeren (of inname van gegevens). Dit deel van de walkthrough maakt vier objecten: gegevensbron, indexdefinitie, skillset, indexer. 

### <a name="step-1-create-a-data-source"></a>Stap 1: een gegevensbron maken

`SearchServiceClient` heeft een `DataSources`-eigenschap. Deze eigenschap biedt alle methoden die u nodig hebt om Azure Cognitive Search-gegevensbronnen te maken, te vermelden, bij te werken of te verwijderen.

Een nieuw `DataSource` exemplaar `serviceClient.DataSources.CreateOrUpdate(dataSource)`maken door aan te roepen . `DataSource.AzureBlobStorage`vereist dat u de naam van de gegevensbron, verbindingstekenreeks en blobcontainernaam opgeeft.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "basic-demo-data-pr",
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

Voor een succesvolle aanvraag retourneert de methode de gemaakte gegevensbron. Als er een probleem is met de aanvraag, zoals een ongeldige parameter, wordt met de methode een uitzondering gemaakt.

Voeg nu een `Main` regel `CreateOrUpdateDataSource` toe om de functie aan te roepen die u zojuist hebt toegevoegd.

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


<!-- 
```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

Now that you have initialized the `DataSource` object, create the data source. `SearchServiceClient` has a `DataSources` property. This property provides all the methods you need to create, list, update, or delete Azure Cognitive Search data sources.

For a successful request, the method will return the data source that was created. If there is a problem with the request, such as an invalid parameter, the method will throw an exception.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
``` -->

Bouw de oplossing en voer deze uit. Aangezien dit uw eerste verzoek is, controleert u de Azure-portal om te controleren of de gegevensbron is gemaakt in Azure Cognitive Search. Controleer op de dashboardpagina van de zoekservice of de tegel Gegevensbronnen een nieuw item heeft. U moet mogelijk een paar minuten wachten tot de portalpagina is vernieuwd.

  ![Tegel gegevensbronnen in de portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "Tegel gegevensbronnen in de portal")

### <a name="step-2-create-a-skillset"></a>Stap 2: Een skillset maken

In deze sectie definieert u een reeks verrijkingsstappen die u op uw gegevens wilt toepassen. Elke verrijkingsstap wordt een *vaardigheid* en de set verrijkingsstappen een *skillset genoemd.* Deze zelfstudie maakt gebruik van [ingebouwde cognitieve vaardigheden](cognitive-search-predefined-skills.md) voor de skillset:

+ [Optische tekenherkenning](cognitive-search-skill-ocr.md) om afgedrukte en handgeschreven tekst in afbeeldingsbestanden te herkennen.

+ [Tekstfusie](cognitive-search-skill-textmerger.md) om tekst uit een verzameling velden in één veld te consolideren.

+ [Taaldetectie](cognitive-search-skill-language-detection.md) om de taal van de inhoud vast te stellen.

+ [Tekst splitsen](cognitive-search-skill-textsplit.md) om grote inhoud in kleinere stukken te splitsen voordat de sleutelzin extractie vaardigheid en de entiteit erkenning vaardigheid. Sleutelzinextractie en entiteitsherkenning accepteren invoer van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen.

+ [Entiteitsherkenning](cognitive-search-skill-entity-recognition.md) voor het extraheren van de namen van organisaties uit inhoud in de blobcontainer.

+ [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) voor het ophalen van de belangrijkste sleuteltermen.

Tijdens de eerste verwerking kraakt Azure Cognitive Search elk document om inhoud uit verschillende bestandsindelingen te lezen. Gevonden tekst uit het bronbestand wordt in een gegenereerd ```content```-veld geplaatst, één voor elk document. Stel daarom de invoer ```"/document/content"``` in om deze tekst te gebruiken. 

Uitvoeren kunnen aan een index worden toegewezen, als invoer worden gebruikt voor een downstream-vaardigheid, of beide zoals bij taalcode. Een taalcode is in de index handig om te filteren. Taalcode wordt als invoer door vaardigheden voor tekstanalyse gebruikt om de taalregels voor woordafbreking in te stellen.

Zie [How to define a skillset](cognitive-search-defining-skillset.md) (Een set vaardigheden definiëren) voor meer informatie over de grondbeginselen van vaardigheden.

### <a name="ocr-skill"></a>OCR-vaardigheid

De **OCR-vaardigheid** haalt tekst uit afbeeldingen. Deze vaardigheid gaat ervan uit dat er een normalized_images veld bestaat. Om dit veld te genereren, stellen we ```"imageAction"``` later in de ```"generateNormalizedImages"```zelfstudie de configuratie in de indexerdefinitie in op .

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "text",
        targetName: "text"));

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

### <a name="merge-skill"></a>Vaardigheid samenvoegen

In deze sectie maakt u een **samenvoegvaardigheid** die het veld documentinhoud samenvoegt met de tekst die is geproduceerd door de OCR-vaardigheid.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text"));

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

### <a name="language-detection-skill"></a>Taaldetectievaardigheid

De **vaardigheid Taaldetectie** detecteert de taal van de invoertekst en rapporteert één taalcode voor elk document dat op de aanvraag wordt ingediend. We gebruiken de uitvoer van de **taaldetectievaardigheid** als onderdeel van de invoer naar de **text split-vaardigheid.**

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode"));

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Vaardigheid voor tekstsplitsing

De onderstaande **gesplitste** vaardigheid splitst tekst op pagina's en beperkt de `String.Length`paginalengte tot 4.000 tekens, gemeten door . Het algoritme zal proberen om de tekst `maximumPageLength` te splitsen in brokken die maximaal in grootte. In dit geval zal het algoritme zijn best doen om de zin op een zinsgrens `maximumPageLength`te breken, zodat de grootte van de brok iets kleiner kan zijn dan .

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();

    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages"));

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

### <a name="entity-recognition-skill"></a>Entiteitserkenningsvaardigheid

Deze `EntityRecognitionSkill` instantie is ingesteld `organization`om categorietype te herkennen . De **entiteitserkenningsvaardigheid** kan `person` `location`ook categorietypen herkennen en .

Merk op dat het veld ```"/document/pages/*"``` 'context' is ingesteld op een sterretje, wat ```"/document/pages"```betekent dat de verrijkingsstap voor elke pagina wordt aangeroepen onder .

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations"));

    List<EntityCategory> entityCategory = new List<EntityCategory>();
    entityCategory.Add(EntityCategory.Organization);

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

### <a name="key-phrase-extraction-skill"></a>Sleutelzin extractie vaardigheid

Net `EntityRecognitionSkill` als de instantie die zojuist is gemaakt, wordt voor elke pagina van het document de vaardigheid **Sleutelzinextractie** ingeschakeld.

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

### <a name="build-and-create-the-skillset"></a>Bouw en maak de skillset

Bouw `Skillset` de vaardigheden die je hebt gemaakt.

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

Voeg de volgende `Main`regels toe aan .

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
    List<Skill> skills = new List<Skill>();
    skills.Add(ocrSkill);
    skills.Add(mergeSkill);
    skills.Add(languageDetectionSkill);
    skills.Add(splitSkill);
    skills.Add(entityRecognitionSkill);
    skills.Add(keyPhraseExtractionSkill);

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>Stap 3: Een index maken

In dit gedeelte kunt u het indexschema definiëren door op te geven welke velden in de doorzoekbare index moeten worden opgenomen en de zoekkenmerken voor elk veld op te geven. Velden hebben een type en kunnen kenmerken opnemen die bepalen hoe het veld wordt gebruikt (doorzoekbaar, sorteerbaar enzovoort). Veldnamen in een index hoeven niet identiek te zijn aan de veldnamen in de bron. In een latere stap voegt u veldverwijzingen in een indexeerfunctie toe om bron-doelvelden te verbinden. Definieer voor deze stap de index met behulp van veldnaamconventies die relevant zijn voor uw zoektoepassing.

In deze oefening worden de volgende velden en veldtypen gebruikt:

| field-names: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


#### <a name="create-demoindex-class"></a>DemoIndex-klasse maken

De velden voor deze index worden gedefinieerd met een modelklasse. Elke eigenschap van de modelklasse heeft kenmerken die het zoekgerelateerde gedrag van het bijbehorende indexveld bepalen. 

We voegen de modelklasse toe aan een nieuw C#-bestand. Klik met de rechtermuisknop op uw project en selecteer Nieuw `DemoIndex.cs`item > **toevoegen...**, selecteer 'Klasse' en geef het bestand een naam en selecteer **Toevoegen**. **Add**

Zorg ervoor dat u aangeeft dat `Microsoft.Azure.Search` `Microsoft.Azure.Search.Models` u typen uit de naamruimten en naamruimten wilt gebruiken.

Voeg de definitie van `DemoIndex.cs` de onderstaande modelklasse toe aan en voeg deze op in dezelfde naamruimte waar u de index maakt.

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

<!-- Add the below model class definition to `DemoIndex.cs` and include it in the same namespace where you'll create the index.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Cognitive Search .NET SDK.
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
``` -->

Nu u een modelklasse hebt gedefinieerd, `Program.cs` u vrij eenvoudig een indexdefinitie maken. De naam voor deze `demoindex`index zal zijn . Als er al een index bestaat met die naam, wordt deze verwijderd.

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

Tijdens het testen u merken dat u probeert om de index te maken meer dan eens. Controleer daarom of de index die u gaat maken al bestaat voordat u deze probeert te maken.

Voeg de volgende `Main`regels toe aan .

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Index demoIndex = CreateDemoIndex(serviceClient);
```

<!-- ```csharp
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
    // Handle exception
}
```
 -->

Zie [Index maken (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over het definiëren van een index.

### <a name="step-4-create-and-run-an-indexer"></a>Stap 4: Een indexer maken en uitvoeren

Tot nu toe hebt u een gegevensbron, een set vaardigheden en een index gemaakt. Deze drie onderdelen gaan deel uitmaken van een [indexeerfunctie](search-indexer-overview.md) die de onderdelen combineert in één meervoudige bewerking. Als u deze in een indexeerfunctie wilt combineren, moet u veldtoewijzingen definiëren.

+ De veldtoewijzingen worden vóór de skillset verwerkt en worden bronvelden van de gegevensbron toewijzen aan doelvelden in een index. Als veldnamen en -typen aan beide uiteinden hetzelfde zijn, is er geen toewijzing vereist.

+ De outputFieldMappings worden verwerkt na de skillset, verwijzend naar sourceFieldNames die niet bestaan totdat het kraken van documenten of verrijking ze maakt. De targetFieldName is een veld in een index.

Naast het aansluiten van ingangen op uitgangen, u ook veldtoewijzingen gebruiken om gegevensstructuren af te vlakken. Zie [Verrijkte velden toewijzen aan een doorzoekbare index voor](cognitive-search-output-field-mapping.md)meer informatie.

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

    List<FieldMapping> fieldMappings = new List<FieldMapping>();
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")));
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content"));

    List<FieldMapping> outputMappings = new List<FieldMapping>();
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode"));

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
Voeg de volgende `Main`regels toe aan .

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Verwacht dat het maken van de indexer zal een beetje tijd in beslag nemen. De gegevensset is klein, maar analytische vaardigheden zijn berekeningsintensief. Sommige vaardigheden, zoals afbeeldingsanalyse, zijn langlopend.

> [!TIP]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er problemen zijn met het bereiken van de gegevens, het toewijzen van invoeren en uitvoeren of de volgorde van bewerkingen, worden ze in deze fase weergegeven.

### <a name="explore-creating-the-indexer"></a>De indexeren van de indexer verkennen

De code ```"maxFailedItems"``` wordt ingesteld op -1, waardoor de indexeringsengine fouten tijdens het importeren van gegevens moet negeren. Dit is nuttig omdat de demo-gegevensbron maar een paar documenten bevat. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

Let ook ```"dataToExtract"``` op ```"contentAndMetadata"```de is ingesteld op . Deze instructie geeft de indexeerfunctie de opdracht om automatisch de inhoud van verschillende bestandsindelingen te extraheren, evenals metagegevens voor elk bestand.

Wanneer inhoud wordt uitgepakt, kunt u instellen dat `imageAction` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. De ```"imageAction"``` set ```"generateNormalizedImages"``` op configuratie, gecombineerd met de OCR Skill and Text Merge Skill, vertelt de indexer om tekst uit de afbeeldingen te extraheren (bijvoorbeeld het woord "stoppen" van een verkeersbord voor het verkeer) en deze in te sluiten als onderdeel van het inhoudsveld. Dit gedrag is van toepassing op zowel de afbeeldingen die zijn ingesloten in de documenten (zoals een afbeelding in een pdf-bestand) als afbeeldingen die zijn gevonden in de gegevensbron, zoals een JPG-bestand.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - Monitor indexering

Nadat de indexeerfunctie is gedefinieerd, wordt deze automatisch uitgevoerd wanneer u de aanvraag verzendt. Afhankelijk van welke cognitieve vaardigheden u hebt gedefinieerd, kan het indexeren langer duren dan verwacht. Gebruik de `GetStatus` methode om erachter te komen of de indexer nog steeds actief is.

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

`IndexerExecutionInfo`vertegenwoordigt de huidige status en uitvoeringsgeschiedenis van een indexeerder.

Waarschuwingen komen veel voor bij sommige combinaties van bronbestand en vaardigheid en wijzen niet altijd op een probleem. In deze zelfstudie zijn de waarschuwingen onschadelijk (bijvoorbeeld geen tekstinvoeren van de JPEG-bestanden).

Voeg de volgende `Main`regels toe aan .

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 - Zoeken

Nadat de indexering is voltooid, u query's uitvoeren die de inhoud van afzonderlijke velden retourneren. Azure Cognitive Search retourneert standaard de top 50-resultaten. De voorbeeldgegevens zijn beperkt, dus de standaardinstelling werkt prima. Als u echter werkt met grotere gegevenssets, moet u mogelijk parameters opnemen in de queryreeks om meer resultaten te retourneren. Zie Resultaten [van de pagina in Azure Cognitive Search](search-pagination-page-layout.md)voor instructies.

Voer als verificatiestap query's uit op de index voor alle velden.

Voeg de volgende `Main`regels toe aan .

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

`CreateSearchIndexClient`hiermee wordt `SearchIndexClient` een nieuwe waarde gemaakt die is opgeslagen in het config-bestand van de toepassing (appsettings.json). Merk op dat de API-sleutel voor zoekservicequery's wordt gebruikt en niet de beheersleutel.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Voeg de volgende `Main`code toe aan . De eerste try-catch retourneert de indexdefinitie, met de naam, het type en de kenmerken van elk veld. De tweede is een geparameteriseerde query, waarbij `Select` bijvoorbeeld wordt `organizations`aangegeven welke velden in de resultaten moeten worden opgenomen. Een zoekreeks `"*"` retourneert alle inhoud van één veld.

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

Herhaal dit voor extra velden: inhoud, taalcode, sleutelzinnen en organisaties in deze oefening. U meerdere velden retourneren via de eigenschap [Selecteren](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) met een lijst met door komma's afgebakende lijst.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de vroege experimentele ontwikkelingsfase is de meest praktische benadering voor ontwerpiteratie het verwijderen van de objecten uit Azure Cognitive Search en uw code in staat te stellen ze opnieuw op te bouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

De voorbeeldcode voor deze zelfstudie controleert op bestaande objecten en verwijdert deze, zodat u uw code opnieuw uitvoeren.

U de portal ook gebruiken om indexen, indexers, gegevensbronnen en skillsets te verwijderen.

## <a name="takeaways"></a>Opgedane kennis

Deze zelfstudie toonde de basisstappen voor het bouwen van een verrijkte indexeringspijplijn door het maken van onderdelen: een gegevensbron, vaardigheden, index en indexer.

[Ingebouwde vaardigheden](cognitive-search-predefined-skills.md) werden geïntroduceerd, samen met skillset definitie en de mechanica van chaining vaardigheden samen door middel van ingangen en uitgangen. U hebt `outputFieldMappings` ook geleerd dat in de indexerdefinitie vereist is voor het routeren van verrijkte waarden uit de pijplijn naar een doorzoekbare index op een Azure Cognitive Search-service.

Tot slot hebt u geleerd hoe u resultaten kunt testen en het systeem opnieuw kunt instellen voor verdere iteraties. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. U hebt ook geleerd hoe u de status van de indexeerfunctie kunt controleren en welke objecten u moet verwijderen voordat u een pijplijn opnieuw uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het einde van een project in uw eigen abonnement werkt, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling Alle resources of Resourcegroepen in het linkernavigatiedeelvenster.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met alle objecten in een AI-verrijkingspijplijn, bekijken we vaardigheden en individuele vaardigheden.

> [!div class="nextstepaction"]
> [Hoe maak je een skillset](cognitive-search-defining-skillset.md)
