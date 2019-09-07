---
title: C#Zelf studie voor het aanroepen van Cognitive Services-API's in een indexerings pijplijn-Azure Search
description: In deze zelfstudie ziet u een voorbeeld van gegevensextractie, natuurlijke taal en AI-beeldverwerking in Azure Search indexeren voor gegevensextractie en transformatie.
manager: nitinme
author: MarkHeff
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: 260f6a6141903ea1fd7edcfe2e031091bba322be
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744758"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>C#Vind Cognitive Services-API's aanroepen in een Azure Search Indexing-pijp lijn

In deze zelfstudie leert u de mechanismen achter gegevensverrijking programmeren in Azure Search met behulp van *cognitieve vaardigheden*. Vaardig heden worden ondersteund door NLP (natuurlijke taal verwerking) en mogelijkheden voor de analyse van installatie kopieën in Cognitive Services. Via de vaardig heden-compositie en-configuratie kunt u tekst-en tekst representaties van een afbeelding of gescand document bestand extra heren. U kunt ook taal, entiteiten, sleutel zinnen en meer detecteren. Het eind resultaat is uitgebreide extra inhoud in een Azure Search index, gemaakt door een AI-indexerings pijplijn.

In deze zelf studie gebruikt u de .NET SDK om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een indexeringspijplijn maken die voorbeeldgegevens onderweg naar een index verrijkt
> * Ingebouwde vaardig heden Toep assen: optische teken herkenning, tekst fusie, taal detectie, tekst splitsing, entiteit herkenning, extractie van sleutel zinnen
> * Vaardigheden aan elkaar koppelen door invoeren aan uitvoeren toe te wijzen in een set vaardigheden
> * Aanvragen uitvoeren en resultaten bekijken
> * De index en indexeerfuncties opnieuw instellen voor verdere ontwikkeling

Uitvoer is een volledige doorzoekbare index in Azure Search. U kunt de index uitbreiden met andere standaardfuncties, zoals [synoniemen](search-synonyms.md), [scoreprofielen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analyses](search-analyzers.md) en [filters](search-filters.md).

Deze zelf studie wordt uitgevoerd op de gratis service, maar het aantal gratis trans acties is beperkt tot 20 documenten per dag. Als u deze zelf studie meer dan één keer in dezelfde dag wilt uitvoeren, gebruikt u een kleinere set bestanden, zodat u in meer uitvoeringen kunt passen.

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u een factureer bare Cognitive Services resource koppelen. Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in Azure Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/) gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met Azure Search prijzen](https://go.microsoft.com/fwlink/?linkid=2042400).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie worden de volgende services, hulpprogram ma's en gegevens gebruikt. 

+ [Maak een Azure-opslag account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) voor het opslaan van de voorbeeld gegevens. Zorg ervoor dat het opslag account zich in dezelfde regio bevindt als Azure Search.

+ De [voorbeeld gegevens](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) bestaan uit een kleine set verschillende typen. 

+ [Installeer Visual Studio](https://visualstudio.microsoft.com/) om te gebruiken als IDE.

+ [Een Azure Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt voor deze zelf studie gebruikmaken van een gratis service.

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

Als u wilt communiceren met uw Azure Search-service, hebt u de service-URL en een toegangs sleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Search hebt toegevoegd aan uw abonnement, volgt u deze stappen om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal in **instellingen** > **sleutels**een beheerders sleutel op voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   ![Een HTTP-eind punt en toegangs sleutel ophalen](media/search-get-started-postman/get-url-key.png "Een HTTP-eind punt en toegangs sleutel ophalen")

Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="prepare-sample-data"></a>Voorbeeld gegevens voorbereiden

De verrijkingspijplijn haalt gegevens uit Azure-gegevensbronnen. Brongegevens moeten afkomstig zijn van een ondersteund type gegevensbron van een [Azure Search-indexeerfunctie](search-indexer-overview.md). In dit voorbeeld gebruiken we blobopslag om meerdere inhoudstypen te laten zien.

1. [Meld u aan bij de Azure Portal](https://portal.azure.com), navigeer naar uw Azure Storage-account, klik op **blobs**en klik vervolgens op **+ container**.

1. [Maak een BLOB-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) om voorbeeld gegevens te bevatten. U kunt het niveau van open bare toegang instellen op een van de geldige waarden. In deze zelf studie wordt ervan uitgegaan dat de container naam ' Basic-demo-data-PR ' is.

1. Nadat de container is gemaakt, opent u deze en selecteert u **uploaden** op de opdracht balk om de [voorbeeld gegevens](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)te uploaden.

   ![Bronbestanden in Azure-blobopslag](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Nadat de voorbeeldbestanden zijn geladen, haalt u de containernaam en een verbindingsreeks voor de Blob-opslag op. U kunt dit doen door te navigeren naar uw opslag account in de Azure Portal, **toegangs toetsen**te selecteren en vervolgens het **verbindings reeks** veld te kopiëren.

   De verbindingsreeks moet een URL zijn die er ongeveer als volgt uitziet:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Er zijn andere manieren om de verbindingsreeks op te geven, zoals een Shared Access Signature bieden. Zie [Indexing Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials) (Azure Blob Storage indexeren) voor meer informatie over referenties voor gegevensbronnen.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

Begin met het openen van Visual Studio en het maken van een nieuw console-app-project dat kan worden uitgevoerd op .NET core.

### <a name="install-nuget-packages"></a>NuGet-pakketten installeren

De [Azure Search .NET SDK](https://aka.ms/search-sdk) bestaat uit een aantal client bibliotheken waarmee u uw indexen, gegevens bronnen, Indexeer functies en vaardig heden kunt beheren, evenals documenten en query's kunt uploaden en uitvoeren, zonder dat u de details van http en JSON hoeft te hoeven afhandelen. . Deze client bibliotheken zijn allemaal gedistribueerd als NuGet-pakketten.

Voor dit project moet u versie 9 van het `Microsoft.Azure.Search` NuGet-pakket en het meest recente `Microsoft.Extensions.Configuration.Json` NuGet-pakket installeren.

Installeer het `Microsoft.Azure.Search` NuGet-pakket met behulp van de Package Manager-console in Visual Studio. Als u de Package Manager-console wilt openen, selecteert u **tools** > **NuGet package manager** > **Package Manager console**. Als u de opdracht wilt uitvoeren, gaat u naar de [pagina micro soft. Azure. Search NuGet package](https://www.nuget.org/packages/Microsoft.Azure.Search), selecteert u versie 9 en kopieert u de opdracht Package Manager. Voer deze opdracht uit in de Package Manager-console.

Als u het `Microsoft.Extensions.Configuration.Json` NuGet-pakket in Visual Studio wilt installeren, selecteert u **extra** > **NuGet package manager** > **NuGet-pakketten beheren voor oplossing...** . Selecteer Bladeren en zoek naar het `Microsoft.Extensions.Configuration.Json` NuGet-pakket. Zodra u de app hebt gevonden, selecteert u het pakket, selecteert u het project, bevestigt u dat de versie de laatste stabiele versie is en selecteert u installeren.

## <a name="add-azure-search-service-information"></a>Informatie over Azure Search-service toevoegen

Als u verbinding wilt maken met uw Azure Search-service, moet u de gegevens van de zoek service toevoegen aan uw project. Klik met de rechter muisknop op het project in de > Solution Explorer en selecteer**Nieuw item toevoegen...** . Geef het bestand `appsettings.json` een naam en selecteer **toevoegen**. 

Dit bestand moet worden opgenomen in de uitvoermap. Hiertoe klikt u met de rechter `appsettings.json` muisknop op en selecteert u **Eigenschappen**. Wijzig de waarde van **kopiëren naar uitvoermap** naar een **nieuwere versie**.

Kopieer de onderstaande JSON naar het nieuwe JSON-bestand.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Voeg de gegevens van uw zoek service en Blob-opslag account toe.

U kunt de informatie van uw zoek service ophalen via de pagina van uw zoek account in de Azure Portal. De account naam bevindt zich op de hoofd pagina en de sleutels kunnen worden gevonden door **sleutels**te selecteren.

U kunt de BLOB-connection string ophalen door te navigeren naar uw opslag account in de Azure Portal, **toegangs toetsen**te selecteren en vervolgens het **verbindings reeks** veld te kopiëren.

## <a name="add-namespaces"></a>Naam ruimten toevoegen

In deze zelf studie wordt een groot aantal verschillende typen van verschillende naam ruimten gebruikt. Als u deze typen wilt gebruiken, moet u het `Program.cs`volgende toevoegen aan.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Een client maken

Maak een instantie van de `SearchServiceClient` klasse.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient`Hiermee maakt u `SearchServiceClient` een nieuw met behulp van waarden die zijn opgeslagen in het configuratie bestand van de toepassing (appSettings. json).

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
> 

## <a name="create-a-data-source"></a>Een gegevensbron maken

Maak een nieuw `DataSource` exemplaar door aan `DataSource.AzureBlobStorage`te roepen. `DataSource.AzureBlobStorage`Hiervoor moet u de naam van de gegevens bron, de connection string en de naam van de BLOB-container opgeven.

Hoewel het niet in deze zelf studie wordt gebruikt, wordt er ook een voorlopig verwijderings beleid gedefinieerd, dat wordt gebruikt voor het identificeren van verwijderde blobs op basis van de waarde van een kolom voorlopig verwijderen. Het volgende beleid beschouwt een blob die moet worden verwijderd als deze een meta `IsDeleted` gegevens eigenschap met `true`de waarde bevat.

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

Nu u het `DataSource` object hebt geïnitialiseerd, maakt u de gegevens bron. `SearchServiceClient` heeft een `DataSources`-eigenschap. Deze eigenschap bevat alle methoden die u nodig hebt om Azure Search gegevens bronnen te maken, weer te geven, bij te werken of te verwijderen.

Voor een succes volle aanvraag retourneert de methode de gegevens bron die is gemaakt. Als er een probleem is met de aanvraag, zoals een ongeldige para meter, genereert de methode een uitzonde ring.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

Aangezien dit uw eerste aanvraag is, controleert u Azure Portal om te bevestigen dat de gegevensbron in Azure Search is gemaakt. Controleer op de dashboardpagina van de zoekservice of de tegel Gegevensbronnen een nieuw item heeft. U moet mogelijk een paar minuten wachten tot de portalpagina is vernieuwd.

  ![Tegel Gegevensbronnen in de portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "Tegel Gegevensbronnen in de portal")

## <a name="create-a-skillset"></a>Een set vaardigheden maken

In deze sectie definieert u een reeks verrijkings stappen die u wilt Toep assen op uw gegevens. Elke verrijkings stap wordt een *vaardigheid* genoemd en de set verrijkings stappen een *vaardig heden*. In deze zelfstudie wordt gebruikgemaakt van [vooraf gedefinieerde cognitieve vaardigheden](cognitive-search-predefined-skills.md) voor de set vaardigheden:

+ [Optische teken herkenning](cognitive-search-skill-ocr.md) voor het herkennen van gedrukte en handgeschreven tekst in afbeeldings bestanden.

+ [Tekst fusie](cognitive-search-skill-textmerger.md) voor het consolideren van tekst van een verzameling velden in één veld.

+ [Taaldetectie](cognitive-search-skill-language-detection.md) om de taal van de inhoud vast te stellen.

+ [Tekst splitsing](cognitive-search-skill-textsplit.md) voor het afsplitsen van grote inhoud in kleinere segmenten voordat u de sleutel woorden extractie vaardigheid en de kwalificatie voor entiteits herkenning aanroept. Sleutel woord extractie en entiteits herkenning accepteren invoer van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen.

+ [Entiteits herkenning](cognitive-search-skill-entity-recognition.md) voor het extra heren van de namen van organisaties uit inhoud in de BLOB-container.

+ [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) voor het ophalen van de belangrijkste sleuteltermen.

Tijdens de eerste verwerking wordt door Azure Search elke document gekraakt om inhoud uit verschillende bestands indelingen te lezen. Gevonden tekst uit het bronbestand wordt in een gegenereerd ```content```-veld geplaatst, één voor elk document. Stel, als zodanig, de invoer in ```"/document/content"``` voor het gebruik van deze tekst. 

Uitvoeren kunnen aan een index worden toegewezen, als invoer worden gebruikt voor een downstream-vaardigheid, of beide zoals bij taalcode. Een taalcode is in de index handig om te filteren. Taalcode wordt als invoer door vaardigheden voor tekstanalyse gebruikt om de taalregels voor woordafbreking in te stellen.

Zie [How to define a skillset](cognitive-search-defining-skillset.md) (Een set vaardigheden definiëren) voor meer informatie over de grondbeginselen van vaardigheden.

### <a name="ocr-skill"></a>OCR-vaardigheid

De **OCR** -vaardigheid extraheert tekst uit afbeeldingen. Bij deze vaardigheid wordt ervan uitgegaan dat er een normalized_images veld bestaat. Als u dit veld wilt genereren, stelt u verderop in de zelf ```"imageAction"``` studie de configuratie in de definitie van ```"generateNormalizedImages"```de Indexeer functie in op.

```csharp
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
```

### <a name="merge-skill"></a>Vaardigheid samen voegen

In deze sectie maakt u een **samenvoeg** vaardigheid die het veld document inhoud samenvoegt met de tekst die door de OCR-vaardigheid is geproduceerd.

```csharp
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
```

### <a name="language-detection-skill"></a>Vaardigheid van taal detectie

De **taaldetectie** vaardigheid detecteert de taal van de invoer tekst en rapporteert één taal code voor elk document dat voor de aanvraag wordt verzonden. We gebruiken de uitvoer van de **taaldetectie** vaardigheid als onderdeel van de invoer voor de kwalificatie **tekst splitsen** .

```csharp
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
```

### <a name="text-split-skill"></a>Vaardigheid tekst splitsen

Met de **onderstaande vaardigheid splitsen worden** tekst op pagina's gesplitst en wordt de pagina lengte beperkt tot 4.000 tekens zoals `String.Length`gemeten door. Het algoritme probeert de tekst te splitsen in segmenten die de meeste `maximumPageLength` grootte hebben. In dit geval is het algoritme het beste om de zin op een zin te kraken, zodat de grootte van het segment enigszins kleiner is dan `maximumPageLength`.

```csharp
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
```

### <a name="entity-recognition-skill"></a>Vaardigheid van entiteits herkenning

Dit `EntityRecognitionSkill` exemplaar is ingesteld op het categorie type `organization`wordt herkend. De kwalificatie voor **entiteits herkenning** kan ook categorie `person` typen `location`herkennen en.

U ziet dat het veld context is ingesteld op ```"/document/pages/*"``` met een asterisk, wat betekent dat de verrijkings stap voor elke ```"/document/pages"```pagina wordt aangeroepen.

```csharp
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
```

### <a name="key-phrase-extraction-skill"></a>Vaardigheid van sleutel frase extractie

Net als `EntityRecognitionSkill` het exemplaar dat zojuist is gemaakt, wordt de **Sleuteltermextractie** vaardigheid voor elke pagina van het document aangeroepen.

```csharp
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
```

### <a name="build-and-create-the-skillset"></a>De vaardig heden bouwen en maken

Bouw het `Skillset` met behulp van de vaardig heden die u hebt gemaakt.

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

Maak de vaardig heden in uw zoek service.

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>Een index maken

In dit gedeelte kunt u het indexschema definiëren door op te geven welke velden in de doorzoekbare index moeten worden opgenomen en de zoekkenmerken voor elk veld op te geven. Velden hebben een type en kunnen kenmerken opnemen die bepalen hoe het veld wordt gebruikt (doorzoekbaar, sorteerbaar enzovoort). Veldnamen in een index hoeven niet identiek te zijn aan de veldnamen in de bron. In een latere stap voegt u veldverwijzingen in een indexeerfunctie toe om bron-doelvelden te verbinden. Definieer voor deze stap de index met behulp van veldnaamconventies die relevant zijn voor uw zoektoepassing.

In deze oefening worden de volgende velden en veldtypen gebruikt:

| field-names: | `id`       | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>DemoIndex-klasse maken

De velden voor deze index worden gedefinieerd met behulp van een model klasse. Elke eigenschap van de modelklasse heeft kenmerken die het zoekgerelateerde gedrag van het bijbehorende indexveld bepalen. 

We voegen de model klasse toe aan een C# nieuw bestand. Klik met de rechter muisknop op uw > project en selecteer**Nieuw item toevoegen...** , selecteer "klasse" en `DemoIndex.cs`Geef het bestand een naam en selecteer vervolgens **toevoegen**.

Zorg ervoor dat u opgeeft dat u typen van de `Microsoft.Azure.Search` naam ruimten en `Microsoft.Azure.Search.Models` wilt gebruiken.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Voeg de definitie van de onderstaande model `DemoIndex.cs` klasse toe aan en voeg deze toe aan dezelfde naam ruimte waarin u de index gaat maken.

```csharp
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
```

Nu u een model klasse hebt gedefinieerd, kunt `Program.cs` u weer een index definitie vrij eenvoudig maken. De naam voor deze index is "demoindex".

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Tijdens het testen is het mogelijk dat u de index meer dan één keer probeert te maken. Controleer daarom of de index die u gaat maken al bestaat voordat u deze probeert te maken.

```csharp
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

Zie [Create Index (Azure Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Index maken (Azure Search REST API)) voor meer informatie over het definiëren van een index.

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Een indexeerfunctie maken, velden toewijzen en transformaties uitvoeren

Tot nu toe hebt u een gegevensbron, een set vaardigheden en een index gemaakt. Deze drie onderdelen gaan deel uitmaken van een [indexeerfunctie](search-indexer-overview.md) die de onderdelen combineert in één meervoudige bewerking. Als u deze in een indexeerfunctie wilt combineren, moet u veldtoewijzingen definiëren.

+ De fieldMappings worden vóór de vaardig heden verwerkt, waarbij bron velden van de gegevens bron worden toegewezen aan de doel velden in een index. Als veld namen en-typen gelijk zijn aan beide uiteinden, is toewijzing niet vereist.

+ De outputFieldMappings worden verwerkt na de vaardig heden en verwijzen naar sourceFieldNames die niet bestaan totdat het document wordt gekraakt of verrijkt. De targetFieldName is een veld in een index.

Naast het koppelen van invoer aan uitvoer, kunt u ook veld toewijzingen gebruiken om gegevens structuren samen te voegen. Zie [hoe u verrijkte velden aan een Doorzoek bare index kunt toewijzen](cognitive-search-output-field-mapping.md)voor meer informatie.

```csharp
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
    // Handle exception
}
```

Het kan even duren voordat het maken van de Indexeer functie is voltooid. De gegevensset is klein, maar analytische vaardigheden zijn berekeningsintensief. Sommige vaardigheden, zoals afbeeldingsanalyse, zijn langlopend.

> [!TIP]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er problemen zijn met het bereiken van de gegevens, het toewijzen van invoeren en uitvoeren of de volgorde van bewerkingen, worden ze in deze fase weergegeven.

### <a name="explore-creating-the-indexer"></a>Het maken van de Indexeer functie verkennen

De code wordt ```"maxFailedItems"``` ingesteld op-1, waarmee wordt aangegeven dat de indexerings engine fouten moet negeren tijdens het importeren van gegevens. Dit is nuttig omdat de demo-gegevensbron maar een paar documenten bevat. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

U ziet ook ```"dataToExtract"``` dat de is ```"contentAndMetadata"```ingesteld op. Deze instructie geeft de indexeerfunctie de opdracht om automatisch de inhoud van verschillende bestandsindelingen te extraheren, evenals metagegevens voor elk bestand.

Wanneer inhoud wordt uitgepakt, kunt u instellen dat `imageAction` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. De ```"imageAction"``` set to ```"generateNormalizedImages"``` Configuration, gecombineerd met de OCR-vaardigheid voor vaardig heden en tekst samenvoeging, vertelt de Indexeer functie om tekst uit de afbeeldingen te halen (bijvoorbeeld het woord ' Stop ' van een stop teken voor verkeer) en dit in te sluiten als onderdeel van het veld inhoud. Dit gedrag is van toepassing op zowel de afbeeldingen die zijn ingesloten in de documenten (zoals een afbeelding in een pdf-bestand) als afbeeldingen die zijn gevonden in de gegevensbron, zoals een JPG-bestand.

## <a name="check-indexer-status"></a>De status van de indexeerfunctie controleren

Nadat de indexeerfunctie is gedefinieerd, wordt deze automatisch uitgevoerd wanneer u de aanvraag verzendt. Afhankelijk van welke cognitieve vaardigheden u hebt gedefinieerd, kan het indexeren langer duren dan verwacht. Als u wilt weten of de Indexeer functie nog steeds wordt uitgevoerd, `GetStatus` gebruikt u de-methode.

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
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
    // Handle exception
}
```

`IndexerExecutionInfo`Hiermee worden de huidige status en de uitvoerings geschiedenis van een Indexeer functie aangeduid.

Waarschuwingen komen veel voor bij sommige combinaties van bronbestand en vaardigheid en wijzen niet altijd op een probleem. In deze zelfstudie zijn de waarschuwingen onschadelijk (bijvoorbeeld geen tekstinvoeren van de JPEG-bestanden).
 
## <a name="query-your-index"></a>Een query uitvoeren in uw index

Nadat het indexeren is voltooid, kunt u query's uitvoeren waarmee de inhoud van afzonderlijke velden wordt geretourneerd. Standaard retourneert Azure Search de 50 hoogste resultaten. De voorbeeldgegevens zijn beperkt, dus de standaardinstelling werkt prima. Als u echter werkt met grotere gegevenssets, moet u mogelijk parameters opnemen in de queryreeks om meer resultaten te retourneren. Zie [How to page results in Azure Search](search-pagination-page-layout.md) (Resultaten genereren in Azure Search) voor instructies.

Voer als verificatiestap query's uit op de index voor alle velden.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient`Hiermee maakt u `SearchIndexClient` een nieuw met behulp van waarden die zijn opgeslagen in het configuratie bestand van de toepassing (appSettings. json). Houd er rekening mee dat de query-API-sleutel van de zoek service wordt gebruikt en niet de Administrator-sleutel.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

De uitvoer is het schema van de index met de naam, het type en de kenmerken van elk veld.

Verzend een tweede query voor `"*"` om alle inhoud te retourneren van één veld, zoals `organizations`.

```csharp
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

Herhaal dit voor aanvullende velden: inhoud, language code, zinsdelen en organisaties in deze oefening. U kunt meerdere velden retourneren via `$select` met behulp van een door komma's gescheiden lijst.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de vroege experimentele stadia van de ontwikkeling is het de meest praktische benadering van het ontwerp om de objecten uit Azure Search te verwijderen en uw code toe te staan om ze opnieuw samen te stellen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

In deze zelf studie hebt u gecontroleerd op bestaande Indexeer functies en indexen en verwijdert u deze als deze al bestaan, zodat u de code opnieuw kunt uitvoeren.

U kunt ook de portal gebruiken om indexen, Indexeer functies en vaardig heden te verwijderen.

Naarmate uw code meer vormt krijgt, is het raadzaam om uw herbouwstrategie te verfijnen. Zie [How to rebuild an index](search-howto-reindex.md) (Een index herbouwen) voor meer informatie.

## <a name="takeaways"></a>Opgedane kennis

In deze zelf studie worden de basis stappen voor het bouwen van een verrijkte index pijplijn gemaakt door het maken van onderdeel onderdelen: een gegevens bron, vaardig heden, index en Indexeer functie.

[Vooraf gedefinieerde vaardigheden](cognitive-search-predefined-skills.md) zijn geïntroduceerd, samen met de definitie van de set vaardigheden en het mechanisme voor het koppelen van vaardigheden via in- en uitvoeren. U hebt ook geleerd dat `outputFieldMappings` vereist is in de definitie van de indexeerfunctie voor de routering van verrijkte waarden uit de pijplijn naar een doorzoekbare index op een Azure Search-service.

Tot slot hebt u geleerd hoe u resultaten kunt testen en het systeem opnieuw kunt instellen voor verdere iteraties. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. U hebt ook geleerd hoe u de status van de indexeerfunctie kunt controleren en welke objecten u moet verwijderen voordat u een pijplijn opnieuw uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

De snelste manier om op te schonen na een zelfstudie is om de resourcegroep met de Azure Search-service en Azure Blob service te verwijderen. Ervan uitgaande dat u beide services in dezelfde groep hebt geplaatst, verwijdert u de resourcegroep om alle inhoud ervan permanent te verwijderen, waaronder de services en alle opgeslagen inhoud die u voor deze zelfstudie hebt gemaakt. De naam van de resourcegroep staat in de portal op de pagina Overzicht van elke service.

## <a name="next-steps"></a>Volgende stappen

De pijplijn uitbreiden of aanpassen met aangepaste vaardigheden. Door een aangepaste vaardigheid te maken en deze toe te voegen aan een set vaardigheden, kunt u zelfgeschreven tekst- of afbeeldingsanalyse integreren.

> [!div class="nextstepaction"]
> [Voorbeeld: Een aangepaste vaardigheid maken voor cognitieve zoek acties](cognitive-search-create-custom-skill-example.md)
