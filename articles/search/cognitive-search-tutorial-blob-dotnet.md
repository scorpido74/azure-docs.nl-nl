---
title: 'Zelf studie C# : en AI over Azure-blobs'
titleSuffix: Azure Cognitive Search
description: Neem een voor beeld van tekst extractie en natuurlijke taal verwerking over inhoud in Blob Storage met behulp C# van en de Azure COGNITIVE Search .NET SDK.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 0c37f1ce2f173f4bf527e7cca30f010101b01720
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190684"
---
# <a name="tutorial-use-c-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Zelf studie: C# gebruik en AI voor het genereren van Doorzoek bare inhoud van Azure-blobs

Als u ongestructureerde tekst of afbeeldingen in Azure Blob-opslag hebt, kan een [AI-verrijkings pijplijn](cognitive-search-concept-intro.md) informatie ophalen en nieuwe inhoud maken die nuttig is voor Zoek opdrachten in volledige tekst of kennis analyse. In deze C# zelf studie past u optische teken herkenning (OCR) toe op afbeeldingen en voert u natuurlijke taal verwerking uit om nieuwe velden te maken die u kunt gebruiken in query's, facetten en filters.

In deze zelf C# studie wordt en de [.NET SDK](https://aka.ms/search-sdk) gebruikt om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Begin met toepassings bestanden en installatie kopieën in Azure Blob-opslag.
> * Definieer een pijp lijn om OCR, extractie van tekst, taal detectie, entiteit en sleutel woord herkenning toe te voegen.
> * Definieer een index voor het opslaan van de uitvoer (onbewerkte inhoud, plus pijp lijn gegenereerde naam/waarde-paren).
> * Voer de pijp lijn uit om trans formaties en analyses te starten en om de index te maken en te laden.
> * Bekijk de resultaten met zoeken in volledige tekst en een uitgebreide query syntaxis.

Als u geen Azure-abonnement hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Een bestaande zoek service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [maken](search-create-service-portal.md) of zoeken 

> [!Note]
> U kunt de gratis service voor deze zelf studie gebruiken. Een gratis zoek service beperkt u tot drie indexen, drie Indexeer functies en drie gegevens bronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u begint, moet u ervoor zorgen dat u over voldoende ruimte beschikt om de nieuwe resources te accepteren.

## <a name="download-files"></a>Bestanden downloaden

1. Open deze [OneDrive-map](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) en klik in de linkerbovenhoek op **downloaden** om de bestanden naar uw computer te kopiëren. 

1. Klik met de rechter muisknop op het zip-bestand en selecteer **Alles uitpakken**. Er zijn 14 bestanden van verschillende typen. Gebruik al deze zelf studie.

## <a name="1---create-services"></a>1-services maken

In deze zelf studie maakt gebruik van Azure Cognitive Search voor het indexeren en uitvoeren van query's, Cognitive Services op de back-end voor AI-verrijking en Azure Blob-opslag om de gegevens op te geven. Deze zelf studie bevindt zich onder de gratis toewijzing van 20 trans acties per Indexeer functie per dag op Cognitive Services, dus de enige services die u moet maken, zijn zoeken en opslag.

Maak, indien mogelijk, beide in dezelfde regio en resource groep voor nabijheid en beheer baarheid. In de praktijk kan uw Azure Storage-account zich in een wille keurige regio bevinden.

### <a name="start-with-azure-storage"></a>Beginnen met Azure Storage

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/) en klik op **+ resource maken**.

1. Zoek naar het *opslag account* en selecteer het opslag account van micro soft.

   ![Opslag account maken](media/cognitive-search-tutorial-blob/storage-account.png "Opslag account maken")

1. Op het tabblad basis beginselen zijn de volgende items vereist. Accepteer de standaard waarden voor alle andere.

   + **Resourcegroep**. Selecteer een bestaande naam of maak een nieuwe, maar gebruik dezelfde groep voor alle services, zodat u ze gezamenlijk kunt beheren.

   + **Naam van opslag account**. Als u denkt dat u mogelijk meerdere resources van hetzelfde type hebt, gebruikt u de naam van dubbel zinnigheid per type en regio, bijvoorbeeld *blobstoragewestus*. 

   + **Locatie**. Kies indien mogelijk dezelfde locatie die wordt gebruikt voor Azure Cognitive Search en Cognitive Services. Een enkele locatie vernietigt bandbreedte kosten.

   + **Soort account**. Kies de standaard *StorageV2 (algemeen gebruik v2)* .

1. Klik op **beoordeling + maken** om de service te maken.

1. Zodra de app is gemaakt, klikt u op **Ga naar de resource** om de pagina overzicht te openen.

1. Klik op **blobs** -service.

1. Klik op **+ container** om een container te maken en geef deze de naam *Basic-demo-data-PR*.

1. Selecteer *Basic-demo-data-PR* en klik vervolgens op **uploaden** om de map te openen waar u de Download bestanden hebt opgeslagen. Selecteer alle veer tien bestanden en klik op **OK** om te uploaden.

   ![Voorbeeld bestanden uploaden](media/cognitive-search-quickstart-blob/sample-data.png "Voorbeeld bestanden uploaden")

1. Voordat u Azure Storage verlaat, moet u een connection string ophalen zodat u een verbinding in azure Cognitive Search kunt formuleren. 

   1. Ga terug naar de pagina overzicht van uw opslag account (we hebben *blobstragewestus* als voor beeld gebruikt). 
   
   1. Selecteer in het navigatie deel venster links de optie **toegangs sleutels** en kopieer een van de verbindings reeksen. 

   De connection string is een URL die vergelijkbaar is met het volgende voor beeld:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Sla de connection string op in Klad blok. U hebt deze later nodig bij het instellen van de gegevens bron verbinding.

### <a name="cognitive-services"></a>Cognitive Services

AI-verrijking wordt ondersteund door Cognitive Services, waaronder Text Analytics en Computer Vision voor de verwerking van natuurlijke taal en afbeelding. Als u een echt prototype of project wilt volt ooien, moet u op dit punt Cognitive Services (in dezelfde regio als Azure Cognitive Search), zodat u het kunt koppelen aan index bewerkingen.

Voor deze oefening kunt u echter het inrichten van resources overs Laan omdat Azure Cognitive Search verbinding kan maken met Cognitive Services achter de schermen en u 20 gratis trans acties per Indexeer functie uitvoert. Omdat in deze zelf studie 7 trans acties worden gebruikt, is de gratis toewijzing voldoende. Voor grotere projecten plant u het inrichten van Cognitive Services op de S0-laag voor betalen per gebruik. Zie [Cognitive Services koppelen](cognitive-search-attach-cognitive-services.md)voor meer informatie.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Het derde onderdeel is Azure Cognitive Search, dat u [in de portal kunt maken](search-create-service-portal.md). U kunt de gratis laag gebruiken om deze procedure te volt ooien. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en-URL voor Azure Cognitive Search ophalen

Als u wilt communiceren met uw Azure Cognitive Search-service, hebt u de service-URL en een toegangs sleutel nodig. Een zoek service wordt met beide gemaakt, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal ook de query sleutel op. Het is een best practice voor het uitgeven van query aanvragen met alleen-lezen toegang.

   ![De service naam en de beheer-en query sleutels ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-your-environment"></a>2-uw omgeving instellen

Begin met het openen van Visual Studio en het maken van een nieuw console-app-project dat kan worden uitgevoerd op .NET core.

### <a name="install-nuget-packages"></a>NuGet-pakketten installeren

De [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) bestaat uit een aantal client bibliotheken waarmee u uw indexen, gegevens bronnen, Indexeer functies en vaardig heden kunt beheren, evenals documenten uploaden en beheren en query's uitvoeren, zonder dat u de details van http en JSON hoeft te hoeven afhandelen. Deze client bibliotheken zijn allemaal gedistribueerd als NuGet-pakketten.

Voor dit project installeert u versie 9 of hoger van het `Microsoft.Azure.Search` NuGet-pakket.

1. Open de Package Manager-console. Selecteer **Hulpprogram ma's** > **NuGet package manager** > **Package Manager-console**. 

1. Navigeer naar de [pagina micro soft. Azure. Search NuGet package](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Selecteer de nieuwste versie (9 of hoger).

1. Kopieer de opdracht Package Manager.

1. Ga terug naar de Package Manager-console en voer de opdracht uit die u in de vorige stap hebt gekopieerd.

Installeer vervolgens het meest recente `Microsoft.Extensions.Configuration.Json` NuGet-pakket.

1. Selecteer **Hulpprogram ma's** > **NuGet package manager** > **NuGet-pakketten beheren voor oplossing...** . 

1. Klik op **Bladeren** en zoek naar het `Microsoft.Extensions.Configuration.Json` NuGet-pakket. 

1. Selecteer het pakket, selecteer uw project, bevestig dat de versie de laatste stabiele versie is en klik vervolgens op **installeren**.

### <a name="add-service-connection-information"></a>Informatie over service verbinding toevoegen

1. Klik met de rechter muisknop op uw project in de Solution Explorer en selecteer > **Nieuw item toevoegen...** . 

1. Geef het bestands `appsettings.json` een naam en selecteer **toevoegen**. 

1. Neem dit bestand op in de uitvoermap.
    1. Klik met de rechter muisknop op `appsettings.json` en selecteer **Eigenschappen**. 
    1. Wijzig de waarde van **kopiëren naar uitvoermap** naar **kopiëren indien nieuwer**.

1. Kopieer de onderstaande JSON naar het nieuwe JSON-bestand.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Voeg de gegevens van uw zoek service en Blob-opslag account toe. U kunt deze informatie ophalen uit de stappen voor het inrichten van de service, zoals aangegeven in de vorige sectie.

### <a name="add-namespaces"></a>Naam ruimten toevoegen

Voeg in `Program.cs`de volgende naam ruimten toe.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Een client maken

Maak een instantie van de klasse `SearchServiceClient` onder Main.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` maakt een nieuwe `SearchServiceClient` met behulp van waarden die zijn opgeslagen in het configuratie bestand van de toepassing (appSettings. json).

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

## <a name="3---create-the-pipeline"></a>3: de pijp lijn maken

In azure Cognitive Search treedt AI-verwerking op tijdens het indexeren (of gegevens opname). In dit deel van het scenario worden vier objecten gemaakt: gegevens bron, index definitie, vaardig heden, Indexeer functie. 

### <a name="step-1-create-a-data-source"></a>Stap 1: een gegevensbron maken

`SearchServiceClient` heeft een `DataSources`-eigenschap. Deze eigenschap bevat alle methoden die u nodig hebt om Azure Cognitive Search-gegevens bronnen te maken, weer te geven, bij te werken of te verwijderen.

Maak een nieuw exemplaar van `DataSource` door het aanroepen van `serviceClient.DataSources.CreateOrUpdate(dataSource)`. `DataSource.AzureBlobStorage` vereist dat u de naam van de gegevens bron, de connection string en de naam van de BLOB-container opgeeft.

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

Voor een succes volle aanvraag retourneert de methode de gegevens bron die is gemaakt. Als er een probleem is met de aanvraag, zoals een ongeldige para meter, genereert de methode een uitzonde ring.

Voeg nu een regel toe aan het hoofd om de `CreateOrUpdateDataSource`-functie aan te roepen die u zojuist hebt toegevoegd.

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

Bouw de oplossing en voer deze uit. Aangezien dit uw eerste aanvraag is, controleert u de Azure Portal om te bevestigen dat de gegevens bron is gemaakt in azure Cognitive Search. Controleer op de dashboardpagina van de zoekservice of de tegel Gegevensbronnen een nieuw item heeft. U moet mogelijk een paar minuten wachten tot de portalpagina is vernieuwd.

  ![De tegel gegevens bronnen in de portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "De tegel gegevens bronnen in de portal")

### <a name="step-2-create-a-skillset"></a>Stap 2: een vaardig heden maken

In deze sectie definieert u een reeks verrijkings stappen die u wilt Toep assen op uw gegevens. Elke verrijkings stap wordt een *vaardigheid* genoemd en de set verrijkings stappen een *vaardig heden*. In deze zelf studie worden [ingebouwde cognitieve vaardig heden](cognitive-search-predefined-skills.md) voor de vaardig heden gebruikt:

+ [Optische teken herkenning](cognitive-search-skill-ocr.md) voor het herkennen van gedrukte en handgeschreven tekst in afbeeldings bestanden.

+ [Tekst fusie](cognitive-search-skill-textmerger.md) voor het consolideren van tekst van een verzameling velden in één veld.

+ [Taaldetectie](cognitive-search-skill-language-detection.md) om de taal van de inhoud vast te stellen.

+ [Tekst splitsing](cognitive-search-skill-textsplit.md) voor het afsplitsen van grote inhoud in kleinere segmenten voordat u de sleutel woorden extractie vaardigheid en de kwalificatie voor entiteits herkenning aanroept. Sleutel woord extractie en entiteits herkenning accepteren invoer van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen.

+ [Entiteits herkenning](cognitive-search-skill-entity-recognition.md) voor het extra heren van de namen van organisaties uit inhoud in de BLOB-container.

+ [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) voor het ophalen van de belangrijkste sleuteltermen.

Tijdens de eerste verwerking wordt door Azure Cognitive Search elke document gekraakt om inhoud te lezen uit verschillende bestands indelingen. Gevonden tekst uit het bronbestand wordt in een gegenereerd ```content```-veld geplaatst, één voor elk document. Stel de invoer als ```"/document/content"``` in om deze tekst te gebruiken. 

Uitvoeren kunnen aan een index worden toegewezen, als invoer worden gebruikt voor een downstream-vaardigheid, of beide zoals bij taalcode. Een taalcode is in de index handig om te filteren. Taalcode wordt als invoer door vaardigheden voor tekstanalyse gebruikt om de taalregels voor woordafbreking in te stellen.

Zie [How to define a skillset](cognitive-search-defining-skillset.md) (Een set vaardigheden definiëren) voor meer informatie over de grondbeginselen van vaardigheden.

### <a name="ocr-skill"></a>OCR-vaardigheid

De **OCR** -vaardigheid extraheert tekst uit afbeeldingen. Bij deze vaardigheid wordt ervan uitgegaan dat er een normalized_images veld bestaat. Als u dit veld wilt genereren, stelt u verderop in de zelf studie de ```"imageAction"``` configuratie in de definitie van de Indexeer functie in op ```"generateNormalizedImages"```.

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

### <a name="merge-skill"></a>Vaardigheid samen voegen

In deze sectie maakt u een **samenvoeg** vaardigheid die het veld document inhoud samenvoegt met de tekst die door de OCR-vaardigheid is geproduceerd.

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

### <a name="language-detection-skill"></a>Vaardigheid van taal detectie

De **taaldetectie** vaardigheid detecteert de taal van de invoer tekst en rapporteert één taal code voor elk document dat voor de aanvraag wordt verzonden. We gebruiken de uitvoer van de **taaldetectie** vaardigheid als onderdeel van de invoer voor de kwalificatie **tekst splitsen** .

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

### <a name="text-split-skill"></a>Vaardigheid tekst splitsen

Met de **onderstaande vaardigheid splitsen worden** tekst op pagina's gesplitst en wordt de pagina lengte beperkt tot 4.000 tekens, zoals gemeten door `String.Length`. Het algoritme probeert de tekst te splitsen in segmenten die Maxi maal `maximumPageLength` groot zijn. In dit geval is de-algoritme het beste om de zin op een zin te verkorten. de grootte van het segment kan dus iets kleiner zijn dan `maximumPageLength`.

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

### <a name="entity-recognition-skill"></a>Vaardigheid van entiteits herkenning

Dit `EntityRecognitionSkill`-exemplaar is ingesteld op het categorie type wordt herkend `organization`. De kwalificatie voor **entiteits herkenning** kan ook categorie typen herkennen `person` en `location`.

U ziet dat het veld context is ingesteld op ```"/document/pages/*"``` met een asterisk, wat betekent dat de verrijkings stap wordt aangeroepen voor elke pagina onder ```"/document/pages"```.

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

### <a name="key-phrase-extraction-skill"></a>Vaardigheid van sleutel frase extractie

Net als het `EntityRecognitionSkill` exemplaar dat zojuist is gemaakt, wordt de **Sleuteltermextractie** vaardigheid voor elke pagina van het document aangeroepen.

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

### <a name="build-and-create-the-skillset"></a>De vaardig heden bouwen en maken

Bouw de `Skillset` met behulp van de vaardig heden die u hebt gemaakt.

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

Voeg de volgende regels toe aan het hoofd.

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

### <a name="step-3-create-an-index"></a>Stap 3: een index maken

In dit gedeelte kunt u het indexschema definiëren door op te geven welke velden in de doorzoekbare index moeten worden opgenomen en de zoekkenmerken voor elk veld op te geven. Velden hebben een type en kunnen kenmerken opnemen die bepalen hoe het veld wordt gebruikt (doorzoekbaar, sorteerbaar enzovoort). Veldnamen in een index hoeven niet identiek te zijn aan de veldnamen in de bron. In een latere stap voegt u veldverwijzingen in een indexeerfunctie toe om bron-doelvelden te verbinden. Definieer voor deze stap de index met behulp van veldnaamconventies die relevant zijn voor uw zoektoepassing.

In deze oefening worden de volgende velden en veldtypen gebruikt:

| field-names: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


#### <a name="create-demoindex-class"></a>DemoIndex-klasse maken

De velden voor deze index worden gedefinieerd met behulp van een model klasse. Elke eigenschap van de modelklasse heeft kenmerken die het zoekgerelateerde gedrag van het bijbehorende indexveld bepalen. 

We voegen de model klasse toe aan een C# nieuw bestand. Klik met de rechter muisknop op uw project en selecteer > **Nieuw item toevoegen...** , selecteer klasse en geef het bestand de naam `DemoIndex.cs`en selecteer vervolgens **toevoegen**.

Zorg ervoor dat u opgeeft dat u typen wilt gebruiken van de `Microsoft.Azure.Search` en `Microsoft.Azure.Search.Models` naam ruimten.

Voeg de definitie van de onderstaande model klasse toe aan `DemoIndex.cs` en voeg deze toe aan de naam ruimte waarin u de index gaat maken.

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

Nu u een model klasse hebt gedefinieerd, kunt u in `Program.cs` weer een index definitie redelijk eenvoudig maken. De naam voor deze index wordt `demoindex`. Als er al een index met deze naam bestaat, wordt deze verwijderd.

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

Tijdens het testen is het mogelijk dat u de index meer dan één keer probeert te maken. Controleer daarom of de index die u gaat maken al bestaat voordat u deze probeert te maken.

Voeg de volgende regels toe aan het hoofd.

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

Zie [Create Index (Azure Cognitive Search rest API)](https://docs.microsoft.com/rest/api/searchservice/create-index)voor meer informatie over het definiëren van een index.

### <a name="step-4-create-and-run-an-indexer"></a>Stap 4: een Indexeer functie maken en uitvoeren

Tot nu toe hebt u een gegevensbron, een set vaardigheden en een index gemaakt. Deze drie onderdelen gaan deel uitmaken van een [indexeerfunctie](search-indexer-overview.md) die de onderdelen combineert in één meervoudige bewerking. Als u deze in een indexeerfunctie wilt combineren, moet u veldtoewijzingen definiëren.

+ De fieldMappings worden vóór de vaardig heden verwerkt, waarbij bron velden van de gegevens bron worden toegewezen aan de doel velden in een index. Als veld namen en-typen gelijk zijn aan beide uiteinden, is toewijzing niet vereist.

+ De outputFieldMappings worden verwerkt na de vaardig heden en verwijzen naar sourceFieldNames die niet bestaan totdat het document wordt gekraakt of verrijkt. De targetFieldName is een veld in een index.

Naast het koppelen van invoer aan uitvoer, kunt u ook veld toewijzingen gebruiken om gegevens structuren samen te voegen. Zie [hoe u verrijkte velden aan een Doorzoek bare index kunt toewijzen](cognitive-search-output-field-mapping.md)voor meer informatie.

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
Voeg de volgende regels toe aan het hoofd.

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Het kan even duren voordat het maken van de Indexeer functie is voltooid. De gegevensset is klein, maar analytische vaardigheden zijn berekeningsintensief. Sommige vaardigheden, zoals afbeeldingsanalyse, zijn langlopend.

> [!TIP]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er problemen zijn met het bereiken van de gegevens, het toewijzen van invoeren en uitvoeren of de volgorde van bewerkingen, worden ze in deze fase weergegeven.

### <a name="explore-creating-the-indexer"></a>Het maken van de Indexeer functie verkennen

Met de code wordt ```"maxFailedItems"```-1 ingesteld, waarmee de indexerings engine tijdens het importeren van gegevens fouten negeert. Dit is nuttig omdat de demo-gegevensbron maar een paar documenten bevat. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

U ziet ook dat de ```"dataToExtract"``` is ingesteld op ```"contentAndMetadata"```. Deze instructie geeft de indexeerfunctie de opdracht om automatisch de inhoud van verschillende bestandsindelingen te extraheren, evenals metagegevens voor elk bestand.

Wanneer inhoud wordt uitgepakt, kunt u instellen dat `imageAction` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. De ```"imageAction"``` ingesteld op ```"generateNormalizedImages"``` configuratie, in combi natie met de OCR-vaardigheid voor vaardig heden en tekst samen voegen, vertelt de Indexeer functie de tekst uit de afbeeldingen (bijvoorbeeld het woord ' Stop ' van een stop teken) en sluit het in als onderdeel van het inhouds veld. Dit gedrag is van toepassing op zowel de afbeeldingen die zijn ingesloten in de documenten (zoals een afbeelding in een pdf-bestand) als afbeeldingen die zijn gevonden in de gegevensbron, zoals een JPG-bestand.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4: indexeren controleren

Nadat de indexeerfunctie is gedefinieerd, wordt deze automatisch uitgevoerd wanneer u de aanvraag verzendt. Afhankelijk van welke cognitieve vaardigheden u hebt gedefinieerd, kan het indexeren langer duren dan verwacht. Als u wilt weten of de Indexeer functie nog steeds wordt uitgevoerd, gebruikt u de methode `GetStatus`.

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

`IndexerExecutionInfo` vertegenwoordigt de huidige status en de uitvoerings geschiedenis van een Indexeer functie.

Waarschuwingen komen veel voor bij sommige combinaties van bronbestand en vaardigheid en wijzen niet altijd op een probleem. In deze zelfstudie zijn de waarschuwingen onschadelijk (bijvoorbeeld geen tekstinvoeren van de JPEG-bestanden).

Voeg de volgende regels toe aan het hoofd.

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5-zoeken

Nadat het indexeren is voltooid, kunt u query's uitvoeren waarmee de inhoud van afzonderlijke velden wordt geretourneerd. Standaard worden de belangrijkste 50 resultaten door Azure Cognitive Search geretourneerd. De voorbeeldgegevens zijn beperkt, dus de standaardinstelling werkt prima. Als u echter werkt met grotere gegevenssets, moet u mogelijk parameters opnemen in de queryreeks om meer resultaten te retourneren. Zie [pagina resultaten in Azure Cognitive Search](search-pagination-page-layout.md)voor instructies.

Voer als verificatiestap query's uit op de index voor alle velden.

Voeg de volgende regels toe aan het hoofd.

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

`CreateSearchIndexClient` maakt een nieuwe `SearchIndexClient` met behulp van waarden die zijn opgeslagen in het configuratie bestand van de toepassing (appSettings. json). U ziet dat de query-API-sleutel van de zoek service wordt gebruikt en niet de Administrator-sleutel.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Voeg de volgende code toe aan Main. De eerste try-catch retourneert de index definitie, met de naam, het type en de kenmerken van elk veld. De tweede is een query met para meters, waarbij `Select` opgeeft welke velden moeten worden meegenomen in de resultaten, bijvoorbeeld `organizations`. Een zoek reeks van `"*"` retourneert alle inhoud van één veld.

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

Herhaal dit voor aanvullende velden: inhoud, language code, zinsdelen en organisaties in deze oefening. U kunt meerdere velden retour neren via de eigenschap [Select](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) met een door komma's gescheiden lijst.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de vroege experimentele stadia van de ontwikkeling kunt u het beste de objecten uit Azure Cognitive Search verwijderen en uw code zo instellen dat deze opnieuw worden opgebouwd. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

In de voorbeeld code voor deze zelf studie wordt gecontroleerd op bestaande objecten en worden deze verwijderd zodat u de code opnieuw kunt uitvoeren.

U kunt ook de portal gebruiken om indexen, Indexeer functies, gegevens bronnen en vaardig heden te verwijderen.

## <a name="takeaways"></a>Opgedane kennis

In deze zelf studie worden de basis stappen voor het bouwen van een verrijkte index pijplijn gemaakt door het maken van onderdeel onderdelen: een gegevens bron, vaardig heden, index en Indexeer functie.

Er zijn [ingebouwde vaardig heden](cognitive-search-predefined-skills.md) geïntroduceerd, samen met de definitie van de vaardig heden en de mechanismen voor het koppelen van vakkennis door middel van invoer en uitvoer. U hebt ook geleerd dat `outputFieldMappings` in de definitie van de Indexeer functie is vereist voor het door sturen van verrijkte waarden van de pijp lijn naar een Doorzoek bare index op een Azure Cognitive Search-service.

Tot slot hebt u geleerd hoe u resultaten kunt testen en het systeem opnieuw kunt instellen voor verdere iteraties. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. U hebt ook geleerd hoe u de status van de indexeerfunctie kunt controleren en welke objecten u moet verwijderen voordat u een pijplijn opnieuw uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het eind van een project aan het werk bent, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling alle resources of resource groepen in het navigatie deel venster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met alle objecten in een AI-verrijkings pijplijn, gaan we kijken naar de definities van vakkennis en individuele vaardig heden.

> [!div class="nextstepaction"]
> [Een vaardig heden maken](cognitive-search-defining-skillset.md)
