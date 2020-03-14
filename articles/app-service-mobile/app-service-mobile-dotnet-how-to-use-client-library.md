---
title: Werken met de beheerde client bibliotheek
description: Meer informatie over het gebruik van de .NET-client bibliotheek voor Azure App Service Mobile Apps met Windows-en Xamarin-apps.
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1c9fba3c13cc6e5476377d59130a95a2edaa324d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249371"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>De beheerde client gebruiken voor Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Overzicht
In deze hand leiding wordt beschreven hoe u algemene scenario's uitvoert met behulp van de beheerde client bibliotheek voor Azure App Service-Mobile Apps voor Windows-en Xamarin-apps. Als u nog niet bekend bent met Mobile Apps, kunt u eerst de zelf studie voor [Azure Mobile apps Snelstartgids][1] volt ooien. In deze hand leiding richten we ons op de aan de client zijde beheerde SDK. Zie de documentatie voor de [.NET Server SDK][2] of de [node. js-Server-SDK][3]voor meer informatie over de sdk's aan de server zijde voor Mobile apps.

## <a name="reference-documentation"></a>Referentie documentatie
De referentie documentatie voor de client-SDK bevindt zich hier: [Azure Mobile apps .net-client referentie][4].
U kunt ook verschillende client voorbeelden vinden in de [github-opslag plaats Azure-samples][5].

## <a name="supported-platforms"></a>Ondersteunde platforms
Het .NET-platform ondersteunt de volgende platforms:

* Xamarin Android-releases voor API 19 tot 24 (KitKat via Nougat)
* Xamarin iOS-releases voor iOS-versies 8,0 en hoger
* Universeel Windows-platform
* Windows Phone 8.1
* Windows Phone 8,0, met uitzonde ring van Silverlight-toepassingen

De verificatie server stroom gebruikt een webweergave voor de weer gegeven gebruikers interface.  Als het apparaat geen gebruikers interface voor webweergave kan presen teren, zijn er andere verificatie methoden nodig.  Deze SDK is dus niet geschikt voor controle-en soort gelijke apparaten.

## <a name="setup"></a>Installatie en vereisten
We gaan ervan uit dat u het back-upproject voor de mobiele app al hebt gemaakt en gepubliceerd, dat ten minste één tabel bevat.  In de code die in dit onderwerp wordt gebruikt, heeft de tabel de naam `TodoItem` en heeft het de volgende kolommen: `Id`, `Text`en `Complete`. Deze tabel is dezelfde tabel die is gemaakt wanneer u de [Snelstartgids van Azure Mobile apps][1]hebt voltooid.

Het overeenkomstige type client-side in C# is de volgende klasse:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

De [JsonPropertyAttribute][6] wordt gebruikt voor het definiëren van de toewijzing van de naam van de *eigenschap* tussen het veld client en het tabel veld.

Zie het [onderwerp .NET Server SDK][7] of het [onderwerp node. js Server SDK][8]voor meer informatie over het maken van tabellen in uw Mobile apps back-end. Als u de back-end van uw mobiele app hebt gemaakt in de Azure Portal met behulp van Quick Start, kunt u ook de instelling **eenvoudige tabellen** gebruiken in de [Azure-portal].

### <a name="how-to-install-the-managed-client-sdk-package"></a>Procedure: het SDK-pakket voor beheerde clients installeren
Gebruik een van de volgende methoden om het beheerde client SDK-pakket voor Mobile Apps te installeren vanuit [NuGet][9]:

* **Visual Studio** Klik met de rechter muisknop op uw project, selecteer **NuGet-pakketten beheren**, zoek naar het `Microsoft.Azure.Mobile.Client`-pakket en klik vervolgens op **installeren**.
* **Xamarin Studio** Klik met de rechter muisknop op uw project, klik op **toevoegen** > **NuGet-pakketten toe te voegen**, zoek naar het `Microsoft.Azure.Mobile.Client` pakket en klik vervolgens op **pakket toevoegen**.

Vergeet niet het volgende toe te voegen **met behulp** van de instructie in het hoofd bestand van de activiteit:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Alle ondersteuningspakketten waarnaar wordt verwezen in uw Android-project, moeten dezelfde versie hebben. De SDK heeft `Xamarin.Android.Support.CustomTabs` afhankelijkheid voor Android-platform, dus als uw project nieuwere ondersteunings pakketten gebruikt, moet u dit pakket rechtstreeks installeren met de vereiste versie om conflicten te voor komen.

### <a name="symbolsource"></a>Procedure: werken met symbolen voor fout opsporing in Visual Studio
De symbolen voor de naam ruimte micro soft. Azure. Mobile zijn beschikbaar op [SymbolSource][10].  Raadpleeg de [SymbolSource-instructies][11] voor het integreren van SymbolSource met Visual Studio.

## <a name="create-client"></a>De Mobile Apps-client maken
Met de volgende code wordt het [mobileserviceclient te maken][12] -object gemaakt dat wordt gebruikt voor toegang tot de back-end van uw mobiele app.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

Vervang in de voor gaande code `MOBILE_APP_URL` door de URL van de back-end van de mobiele app. deze vindt u in de Blade voor de back-end van uw mobiele app in de [Azure-portal]. Het Mobileserviceclient te maken-object moet een singleton zijn.

## <a name="work-with-tables"></a>Werken met tabellen
De volgende sectie bevat informatie over het zoeken en ophalen van records en het wijzigen van de gegevens in de tabel.  De volgende onderwerpen komen aan bod:

* [Een tabel verwijzing maken](#instantiating)
* [Query gegevens](#querying)
* [Geretourneerde gegevens filteren](#filtering)
* [Geretourneerde gegevens sorteren](#sorting)
* [Gegevens in pagina's retour neren](#paging)
* [Specifieke kolommen selecteren](#selecting)
* [Een record opzoeken op basis van id](#lookingup)
* [Omgaan met niet-getypte query's](#untypedqueries)
* [Gegevens invoegen](#inserting)
* Gegevens bijwerken
* [Gegevens verwijderen](#deleting)
* [Conflict oplossing en optimistische gelijktijdigheid](#optimisticconcurrency)
* [Binden aan een Windows-gebruikers interface](#binding)
* [De pagina grootte wijzigen](#pagesize)

### <a name="instantiating"></a>Procedure: een tabel verwijzing maken
Alle code die gegevens opent of wijzigt in een back-end-tabel roept functies aan voor het `MobileServiceTable`-object. U kunt als volgt een verwijzing naar de tabel verkrijgen door de methode [Gett] aan te roepen:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Het geretourneerde object maakt gebruik van het getypeerde model voor serialisatie. Een niet-type serialisatie model wordt ook ondersteund. In het volgende voor beeld [Hiermee wordt een verwijzing naar een tabel zonder type gemaakt]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

In query's zonder type moet u de onderliggende OData-query teken reeks opgeven.

### <a name="querying"></a>Procedure: gegevens opvragen uit uw mobiele app
In deze sectie wordt beschreven hoe u query's uitgeeft aan de back-end van de mobiele app, die de volgende functionaliteit omvat:

* [Geretourneerde gegevens filteren](#filtering)
* [Geretourneerde gegevens sorteren](#sorting)
* [Gegevens in pagina's retour neren](#paging)
* [Specifieke kolommen selecteren](#selecting)
* [Gegevens opzoeken op ID](#lookingup)

> [!NOTE]
> Een door de server gestuurde pagina grootte wordt afgedwongen om te voor komen dat alle rijen worden geretourneerd.  Paginering houdt standaard aanvragen voor grote gegevens sets van een negatieve invloed op de service.  Als u meer dan 50 rijen wilt retour neren, gebruikt u de methode `Skip` en `Take`, zoals wordt beschreven in [gegevens in pagina's retour neren](#paging).

### <a name="filtering"></a>Procedure: geretourneerde gegevens filteren
De volgende code laat zien hoe u gegevens kunt filteren door een `Where`-component op te nemen in een query. Het retourneert alle items van `todoTable` waarvan de eigenschap `Complete` gelijk is aan `false`. Met de functie [Positie] past u een predikaat van een rij filtering toe op de query op basis van de tabel.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

U kunt de URI van de aanvraag die is verzonden naar de back-end weer geven met behulp van software voor het controleren van berichten, zoals ontwikkel hulpprogramma's voor browsers of [Fiddler]. Als u de aanvraag-URI bekijkt, ziet u dat de query reeks is gewijzigd:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Deze OData-aanvraag wordt omgezet in een SQL-query door de server-SDK:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

De functie die wordt door gegeven aan de `Where`-methode kan een wille keurig aantal voor waarden hebben.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Dit voor beeld wordt omgezet in een SQL-query door de server-SDK:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Deze query kan ook in meerdere componenten worden gesplitst:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

De twee methoden zijn gelijkwaardig en kunnen door elkaar worden gebruikt.  De optie voormalig&mdash;het samen voegen van meerdere predikaten in één query&mdash;compact en aanbevolen.

De component `Where` ondersteunt bewerkingen die in de OData-subset worden vertaald. Bewerkingen zijn onder andere:

* Relationele Opera tors (= =,! =, <, < =, >, > =),
* Reken kundige Opera tors (+,-,/, *,%),
* Getals precisie (math. Floor, math. plafond),
* Teken reeks functies (length, subtekenreeks, replace, IndexOf, StartsWith, EndsWith),
* Datum eigenschappen (jaar, maand, dag, uur, minuut, seconde),
* Toegang tot eigenschappen van een object en
* Expressies die een van deze bewerkingen combi neren.

Als u overweegt wat de server-SDK ondersteunt, kunt u de [OData v3-documentatie].

### <a name="sorting"></a>Procedure: geretourneerde gegevens sorteren
De volgende code laat zien hoe u gegevens kunt sorteren door een [OrderBy] -of [OrderByDescending] -functie in de query op te nemen. Hiermee worden items uit `todoTable` oplopend gesorteerd op basis van het veld `Text`.

```csharp
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Procedure: gegevens in pagina's retour neren
Standaard retourneert de back-end alleen de eerste 50 rijen. U kunt het aantal geretourneerde rijen verhogen door de methode [Houd] aan te roepen. Gebruik `Take` samen met de methode [Verdergaan] om een specifieke ' pagina ' aan te vragen van de totale gegevensset die door de query wordt geretourneerd. De volgende query, wanneer uitgevoerd, retourneert de bovenste drie items in de tabel.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Met de volgende gereviseerde query worden de eerste drie resultaten overgeslagen en worden de volgende drie resultaten geretourneerd. Deze query produceert de tweede pagina met gegevens, waarbij de pagina grootte drie items is.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

De methode [IncludeTotalCount] vraagt het totale aantal op voor *alle* records die zouden worden geretourneerd, waarbij eventuele wissel-en limiet componenten worden genegeerd:

```csharp
query = query.IncludeTotalCount();
```

In een echte wereld-app kunt u query's gebruiken die vergelijkbaar zijn met het vorige voor beeld met een paginerings besturings element of een vergelijk bare gebruikers interface om te navigeren tussen pagina's.

> [!NOTE]
> Als u de limiet van 50-rijen in een back-end van een mobiele app wilt overschrijven, moet u ook de [EnableQueryAttribute] Toep assen op de open bare Get-methode en het wissel gedrag opgeven. Wanneer wordt toegepast op de-methode, worden de volgende Maxi maal geretourneerde rijen ingesteld op 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Procedure: specifieke kolommen selecteren
U kunt opgeven welke set eigenschappen moet worden opgenomen in de resultaten door een [Selecteren] -component aan uw query toe te voegen. De volgende code laat bijvoorbeeld zien hoe u slechts één veld selecteert en hoe u meerdere velden kunt selecteren en opmaken:

```csharp
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

Alle functies die tot nu toe zijn beschreven, zijn additief, dus we kunnen ze blijven koppelen. Elke geketende oproep is van invloed op meer van de query. Nog een voor beeld:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Procedure: gegevens opzoeken op basis van ID
De functie [LookupAsync] kan worden gebruikt om objecten van de data base met een bepaalde id op te zoeken.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Procedure: niet-getypte query's uitvoeren
Wanneer u een query uitvoert met behulp van een niet-type tabel object, moet u expliciet de OData-query teken reeks opgeven door [ReadAsync]aan te roepen, zoals in het volgende voor beeld:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

U krijgt de JSON-waarden terug die u kunt gebruiken als een eigenschappen verzameling. Zie de [JSON.net] -site voor meer informatie over JToken en Newton Soft JSON.net.

### <a name="inserting"></a>Procedure: gegevens invoegen in een back-end voor een mobiele app
Alle client typen moeten een lid met de naam **id**hebben die standaard een teken reeks is. Deze **id** is vereist voor het uitvoeren van ruwe bewerkingen en voor offline synchronisatie. De volgende code laat zien hoe u de methode [InsertAsync] kunt gebruiken om nieuwe rijen in een tabel in te voegen. De para meter bevat de gegevens die moeten worden ingevoegd als .NET-object.

```csharp
await todoTable.InsertAsync(todoItem);
```

Als een unieke aangepaste ID-waarde niet is opgenomen in de `todoItem` tijdens het invoegen, wordt een GUID gegenereerd door de server.
U kunt de gegenereerde id ophalen door het object te controleren na het retour neren van de aanroep.

Voor het invoegen van niet-getypte gegevens kunt u gebruikmaken van Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Hier volgt een voor beeld van het gebruik van een e-mail adres als een unieke teken reeks-id:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Werken met ID-waarden
Mobile Apps ondersteunt unieke aangepaste teken reeks waarden voor de **id-** kolom van de tabel. Met een teken reeks waarde kunnen toepassingen aangepaste waarden gebruiken, zoals e-mail adressen of gebruikers namen voor de ID.  Teken reeks-Id's bieden de volgende voor delen:

* Id's worden gegenereerd zonder dat er een retour naar de data base wordt gemaakt.
* Records kunnen gemakkelijker worden samengevoegd vanuit verschillende tabellen of data bases.
* Id-waarden kunnen beter worden geïntegreerd met de logica van een toepassing.

Wanneer een teken reeks-ID-waarde niet is ingesteld voor een ingevoegde record, genereert de back-end van de mobiele app een unieke waarde voor de ID. U kunt de methode [GUID. NewGuid] gebruiken om uw eigen id-waarden te genereren, hetzij op de client hetzij in de back-end.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Procedure: gegevens in een back-end voor een mobiele app wijzigen
De volgende code laat zien hoe u de methode [UpdateAsync] gebruikt om een bestaande record bij te werken met dezelfde id met de nieuwe informatie. De para meter bevat de gegevens die moeten worden bijgewerkt als .NET-object.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Als u niet-getypte gegevens wilt bijwerken, kunt u als volgt gebruikmaken van [JSON.net] :

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Als u een update wilt uitvoeren, moet u een `id` veld opgeven. De back-end gebruikt het `id` veld om te bepalen welke rij moet worden bijgewerkt. Het `id` veld kan worden opgehaald uit het resultaat van de `InsertAsync`-aanroep. Er wordt een `ArgumentException` gegenereerd als u een item probeert bij te werken zonder de `id` waarde op te geven.

### <a name="deleting"></a>Procedure: gegevens in een back-end voor een mobiele app verwijderen
De volgende code laat zien hoe u de methode [DeleteAsync] kunt gebruiken om een bestaand exemplaar te verwijderen. Het exemplaar wordt geïdentificeerd door het `id` veld dat is ingesteld op de `todoItem`.

```csharp
await todoTable.DeleteAsync(todoItem);
```

Als u niet-getypte gegevens wilt verwijderen, kunt u Json.NET als volgt gebruiken:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Wanneer u een aanvraag voor verwijderen maakt, moet u een ID opgeven. Andere eigenschappen worden niet door gegeven aan de service of worden genegeerd bij de service. Het resultaat van een `DeleteAsync` aanroep wordt meestal `null`. De ID die moet worden door gegeven, kan worden verkregen op basis van het resultaat van de aanroep van `InsertAsync`. Er wordt een `MobileServiceInvalidOperationException` gegenereerd wanneer u een item probeert te verwijderen zonder het `id` veld op te geven.

### <a name="optimisticconcurrency"></a>Procedure: optimistische gelijktijdigheid gebruiken om conflicten op te lossen
Twee of meer clients kunnen tegelijkertijd wijzigingen naar hetzelfde item schrijven. Zonder conflict detectie zou de laatste schrijf bewerking eventuele eerdere updates overschrijven. **Optimistische gelijktijdigheids controle** gaat ervan uit dat elke trans actie kan worden doorgevoerd en daarom geen resource vergrendeling gebruikt.  Voordat een trans actie wordt doorgevoerd, wordt door optimistische gelijktijdigheids beheer gecontroleerd of de gegevens niet zijn gewijzigd door een andere trans actie. Als de gegevens zijn gewijzigd, wordt de doorgevoerde trans actie teruggedraaid.

Mobile Apps ondersteunt het optimistische gelijktijdigheids beheer door wijzigingen aan elk item bij te houden met behulp van de `version` systeem eigenschappen kolom die voor elke tabel in de back-end van uw mobiele app is gedefinieerd. Telkens wanneer een record wordt bijgewerkt, stelt Mobile Apps de `version` eigenschap voor die record in op een nieuwe waarde. Tijdens elke update aanvraag wordt de eigenschap `version` van de record die in de aanvraag is opgenomen, vergeleken met dezelfde eigenschap voor de record op de server. Als de versie die is door gegeven met de aanvraag niet overeenkomt met de back-end, wordt door de client bibliotheek een `MobileServicePreconditionFailedException<T>`-uitzonde ring gegenereerd. Het type dat is opgenomen in de uitzonde ring is de record van de back-end die de server versie van de record bevat. De toepassing kan deze informatie vervolgens gebruiken om te bepalen of de update aanvraag opnieuw moet worden uitgevoerd met de juiste `version` waarde van de back-end om wijzigingen door te voeren.

Definieer een kolom voor de klasse Table voor de eigenschap `version` systeem om optimistische gelijktijdigheid in te scha kelen. Bijvoorbeeld:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

Toepassingen die gebruikmaken van niet-getypte tabellen, maken optimistische gelijktijdigheid mogelijk door de `Version` vlag in te stellen op de `SystemProperties` van de tabel als volgt.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Naast het inschakelen van optimistische gelijktijdigheid moet u ook de uitzonde ring `MobileServicePreconditionFailedException<T>` in uw code opvangen bij het aanroepen van [UpdateAsync].  Los het conflict op door de juiste `version` toe te passen op de bijgewerkte record en [UpdateAsync] aan te roepen met de opgeloste record. De volgende code laat zien hoe u een schrijf conflict kunt oplossen dat eenmaal is gedetecteerd:

```csharp
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

Zie het onderwerp [Offlinesynchronisatie van gegevens in Azure Mobile Apps] voor meer informatie.

### <a name="binding"></a>Procedure: Mobile Apps gegevens binden aan een Windows-gebruikers interface
In deze sectie wordt uitgelegd hoe u geretourneerde gegevens objecten kunt weer geven met behulp van UI-elementen in een Windows-app.  De volgende voorbeeld code is afhankelijk van de bron van de lijst met een query voor onvolledige items. De [MobileServiceCollection] maakt een verzameling met Mobile apps bewuste binding.

```csharp
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Sommige besturings elementen in de beheerde runtime ondersteunen een interface met de naam [ISupportIncrementalLoading]. Met deze interface kunnen besturings elementen extra gegevens aanvragen wanneer de gebruiker schuift. Er is ingebouwde ondersteuning voor deze interface voor universele Windows-apps via [MobileServiceIncrementalLoadingCollection], die automatisch de aanroepen van de besturings elementen verwerkt. Gebruik `MobileServiceIncrementalLoadingCollection` in Windows-apps als volgt:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Als u de nieuwe verzameling wilt gebruiken op Windows Phone 8-en Silverlight-apps, gebruikt u de `ToCollection` extensie methoden in `IMobileServiceTableQuery<T>` en `IMobileServiceTable<T>`. Als u gegevens wilt laden, roept u `LoadMoreItemsAsync()`aan.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Wanneer u de verzameling gebruikt die is gemaakt door het aanroepen van `ToCollectionAsync` of `ToCollection`, krijgt u een verzameling die kan worden gebonden aan UI-besturings elementen.  Deze verzameling is gepagineerd.  Omdat de verzameling gegevens van het netwerk laadt, mislukt het laden soms. Als u dergelijke fouten wilt afhandelen, moet u de methode `OnException` op `MobileServiceIncrementalLoadingCollection` overschrijven om uitzonde ringen af te handelen die voortkomen uit aanroepen naar `LoadMoreItemsAsync`

Houd rekening met het volgende als uw tabel veel velden bevat, maar u wilt slechts een deel ervan in uw besturings element weer geven. U kunt de instructies in de voor gaande sectie '[specifieke kolommen selecteren](#selecting)' gebruiken om specifieke kolommen te selecteren die u wilt weer geven in de gebruikers interface.

### <a name="pagesize"></a>Het pagina formaat wijzigen
Azure Mobile Apps retourneert standaard Maxi maal 50 items per aanvraag.  U kunt de wissel grootte wijzigen door de maximale pagina grootte op zowel de client als de server te verhogen.  Als u de aangevraagde pagina grootte wilt verg Roten, geeft u `PullOptions` op wanneer u `PullAsync()`gebruikt:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Ervan uitgaande dat u de `PageSize` op de server gelijk hebt gemaakt aan of groter dan 100, retourneert een aanvraag tot 100 items.

## <a name="#offlinesync"></a>Werken met offline tabellen
Offline tabellen gebruiken een lokale SQLite-opslag voor het opslaan van gegevens voor gebruik wanneer u offline bent.  Alle tabel bewerkingen worden uitgevoerd op basis van de lokale SQLite-opslag in plaats van de externe server opslag.  Als u een offline tabel wilt maken, moet u eerst het project voorbereiden:

1. Klik in Visual Studio met de rechter muisknop op de oplossing > **NuGet-pakketten beheren voor oplossing...** en zoek en installeer het NuGet-pakket **micro soft. Azure. Mobile. client. SQLiteStore** voor alle projecten in de oplossing.
2. Beschrijving Als u Windows-apparaten wilt ondersteunen, installeert u een van de volgende SQLite-runtime pakketten:

   * **Windows 8,1 runtime:** Installeer [sqlite voor Windows 8,1][3].
   * **Windows Phone 8,1:** Installeer [sqlite voor Windows Phone 8,1][4].
   * **Universeel Windows-platform** Installeer [sqlite voor de universele Windows][5].
3. (Optioneel). Voor Windows-apparaten klikt u op **verwijzingen** > **referentie toevoegen...** , vouwt u de **Windows** -map > **extensies**uit en schakelt u vervolgens de juiste **SQLite voor Windows** SDK in samen met de **Visual C++ 2013 runtime voor Windows** SDK.
    De SQLite-SDK-namen variëren enigszins met elk Windows-platform.

Voordat een tabel verwijzing kan worden gemaakt, moet het lokale archief worden voor bereid:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

De initialisatie van Store wordt normaal gesp roken onmiddellijk uitgevoerd nadat de client is gemaakt.  De **OfflineDbPath** moet een bestands naam zijn die geschikt is voor gebruik op alle platforms die u ondersteunt.  Als het pad een volledig gekwalificeerd pad is (dat wil zeggen, begint met een slash), wordt dat pad gebruikt.  Als het pad niet volledig is gekwalificeerd, wordt het bestand opgeslagen op een platformspecifieke locatie.

* Voor iOS-en Android-apparaten is het standaardpad de map ' persoonlijke bestanden '.
* Voor Windows-apparaten is het standaardpad de toepassingsspecifiek map "AppData".

Een tabel verwijzing kan worden verkregen met behulp van de `GetSyncTable<>` methode:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

U hoeft niet te verifiëren om een offline tabel te gebruiken.  U hoeft alleen te verifiëren wanneer u communiceert met de back-end-service.

### <a name="syncoffline"></a>Een offline tabel synchroniseren
Offline tabellen worden standaard niet met de back-end gesynchroniseerd.  Synchronisatie is opgesplitst in twee delen.  U kunt wijzigingen onafhankelijk van het downloaden van nieuwe items pushen.  Hier volgt een typische synchronisatie methode:

```csharp
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

Als het eerste argument bij `PullAsync` null is, wordt incrementele synchronisatie niet gebruikt.  Elke synchronisatie bewerking haalt alle records op.

De SDK voert een impliciete `PushAsync()` uit voordat records worden opgehaald.

Conflict afhandeling gebeurt op een `PullAsync()` methode.  U kunt conflicten afhandelen op dezelfde manier als online tabellen.  Het conflict wordt veroorzaakt wanneer `PullAsync()` wordt aangeroepen in plaats van tijdens het invoegen, bijwerken of verwijderen. Als er meerdere conflicten optreden, worden deze gebundeld in één MobileServicePushFailedException.  Elke fout afzonderlijk te verwerken.

## <a name="#customapi"></a>Werken met een aangepaste API
Met een aangepaste API kunt u aangepaste eind punten definiëren die server functionaliteit beschikbaar maken die niet is toegewezen aan een insert-, update-, Delete-of Read-bewerking. U kunt met behulp van een aangepaste API meer controle over berichten, waaronder het lezen en instellen van HTTP-bericht koppen en het definiëren van een andere indeling dan JSON.

U roept een aangepaste API aan door een van de [InvokeApiAsync] -methoden op de client aan te roepen. Met de volgende regel code wordt bijvoorbeeld een POST-aanvraag verzonden naar de **completeAll** -API op de back-end:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Dit formulier is een aanroep van een getypeerde methode en vereist dat het retour type **MarkAllResult** is gedefinieerd. De getypte en niet-getypte methoden worden ondersteund.

De methode InvokeApiAsync () voegt '/API/' toe aan de API die u wilt aanroepen, tenzij de API begint met een '/'.
Bijvoorbeeld:

* /api/completeAll wordt aangeroepen op de back-end `InvokeApiAsync("completeAll",...)`
* /.auth/me wordt aangeroepen op de back-end `InvokeApiAsync("/.auth/me",...)`

U kunt InvokeApiAsync gebruiken om WebAPI aan te roepen, inclusief de webapi's die niet zijn gedefinieerd met Azure Mobile Apps.  Wanneer u InvokeApiAsync () gebruikt, worden de juiste headers, inclusief verificatie headers, verzonden met de aanvraag.

## <a name="authentication"></a>Gebruikers verifiëren
Mobile Apps ondersteunt het verifiëren en autoriseren van app-gebruikers met behulp van verschillende externe ID-providers: Facebook, Google, micro soft-account, Twitter en Azure Active Directory. U kunt machtigingen voor tabellen instellen om de toegang tot specifieke bewerkingen te beperken tot alleen geverifieerde gebruikers. U kunt ook de identiteit van geverifieerde gebruikers gebruiken voor het implementeren van autorisatie regels in Server scripts. Zie de zelfstudie [Verificatie toevoegen aan uw app] voor meer informatie.

Twee verificatie stromen worden ondersteund: door de *client beheerde* en *Server beheerde* stroom. De door de server beheerde stroom biedt de eenvoudigste verificatie-ervaring, omdat deze afhankelijk is van de webauthenticatie interface van de provider. De door de client beheerde stroom maakt een diep gaande integratie mogelijk met apparaatspecifieke mogelijkheden, afhankelijk van providerspecifieke apparaat-specifieke Sdk's.

> [!NOTE]
> We raden u aan een door de client beheerde stroom te gebruiken in uw productie-apps.

Als u verificatie wilt instellen, moet u uw app registreren bij een of meer id-providers.  De ID-provider genereert een client-ID en een client geheim voor uw app.  Deze waarden worden vervolgens ingesteld op uw back-end om Azure App Service verificatie/autorisatie in te scha kelen.  Volg de gedetailleerde instructies in de zelf studie [verificatie toevoegen aan uw app]voor meer informatie.

In deze sectie komen de volgende onderwerpen aan bod:

* [Door client beheerde verificatie](#clientflow)
* [Door de server beheerde verificatie](#serverflow)
* [Het verificatie token in de cache opslaan](#caching)

### <a name="clientflow"></a>Door client beheerde verificatie
Uw app kan onafhankelijk contact opnemen met de ID-provider en vervolgens het geretourneerde token opgeven tijdens de aanmelding met uw back-end. Met deze client stroom kunt u eenmalige aanmelding bieden voor gebruikers of extra gebruikers gegevens ophalen van de ID-provider. Client flow-verificatie verdient de voor keur om een server stroom te gebruiken omdat de ID-provider-SDK een meer systeem eigen UX-ervaring biedt en extra aanpassing mogelijk maakt.

Voor beelden zijn voor de volgende client-flow-verificatie patronen:

* [Active Directory Authentication Library](#adal)
* [Facebook of Google](#client-facebook)

#### <a name="adal"></a>Gebruikers verifiëren met de Active Directory Authentication Library
U kunt de Active Directory Authentication Library (ADAL) gebruiken voor het initiëren van gebruikers verificatie vanaf de client met behulp van Azure Active Directory-verificatie.

1. Configureer de back-end van uw mobiele app voor AAD-aanmelding door de zelf studie [App Service voor Active Directory-aanmelding configureren] volgen. Zorg ervoor dat u de optionele stap voor het registreren van een systeem eigen client toepassing hebt voltooid.
2. Open in Visual Studio of Xamarin Studio het project en voeg een verwijzing naar het NuGet-pakket van `Microsoft.IdentityModel.Clients.ActiveDirectory` toe. Bij het zoeken van voorlopige versies bevatten.
3. Voeg de volgende code toe aan uw toepassing, afhankelijk van het platform dat u gebruikt. Voer in elk de volgende vervangingen uit:

   * Vervang de **INVOEG instantie** door de naam van de Tenant waarin u uw toepassing hebt ingericht. De notatie moet https://login.microsoftonline.com/contoso.onmicrosoft.comzijn. Deze waarde kan worden gekopieerd van het tabblad domein in uw Azure Active Directory in de [Azure-portal].
   * Vervang de **Insert-resource-id hier** door de client-id voor de back-end van uw mobiele app. U kunt de client-ID verkrijgen via het tabblad **Geavanceerd** onder **Azure Active Directory instellingen** in de portal.
   * Vervang **Insert-client-id-hier** door de client-id die u hebt gekopieerd uit de systeem eigen client toepassing.
   * Vervang **Insert-redirect-Uri-hier** met het */.auth/login/done* -eind punt van uw site met behulp van het HTTPS-schema. Deze waarde moet gelijk zijn aan *https://contoso.azurewebsites.net/.auth/login/done* .

     De code die voor elk platform nodig is, is als volgt:

     **Windows:**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
     }
     ```

     **Xamarin.iOS**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync(UIViewController view)
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
     }
     ```

     **Xamarin.Android**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
     }
     protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
     {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
     }
     ```

#### <a name="client-facebook"></a>Eenmalige aanmelding met een token van Facebook of Google
U kunt de client stroom gebruiken zoals in dit code fragment voor Facebook of Google wordt weer gegeven.

```csharp
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="serverflow"></a>Door de server beheerde verificatie
Wanneer u uw ID-provider hebt geregistreerd, roept u de [LoginAsync] -methode aan op [mobileserviceclient te maken] met de [MobileServiceAuthenticationProvider] -waarde van uw provider. Met de volgende code wordt bijvoorbeeld een server stroom aanmelding gestart met behulp van Facebook.

```csharp
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Als u een andere ID-provider gebruikt dan Facebook, wijzigt u de waarde van [MobileServiceAuthenticationProvider] in de waarde voor uw provider.

In een server stroom beheert Azure App Service de OAuth-verificatie stroom door de aanmeldings pagina van de geselecteerde provider weer te geven.  Wanneer de ID-provider retourneert, wordt door Azure App Service een App Service-verificatie token gegenereerd. De methode [LoginAsync] retourneert een [MobileServiceUser], die zowel de [Naam] van de geverifieerde gebruiker als de [MobileServiceAuthenticationToken]als een JSON-webtoken (JWT) biedt. Dit token kan worden opgeslagen in de cache en opnieuw worden gebruikt totdat het verloopt. Zie [het verificatie token in de cache opslaan](#caching)voor meer informatie.

### <a name="caching"></a>Het verificatie token in de cache opslaan
In sommige gevallen kan de aanroep van de aanmeldings methode worden vermeden na de eerste geslaagde verificatie door het verificatie token van de provider op te slaan.  Microsoft Store-en UWP-apps kunnen [PasswordVault] gebruiken om het huidige verificatie token op te slaan na een geslaagde aanmelding, als volgt:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

De waarde van de gebruikers-id wordt opgeslagen als de gebruikers naam van de referentie en het token wordt opgeslagen als het wacht woord. Bij volgende start-ups kunt u de **PasswordVault** controleren op referenties in de cache. In het volgende voor beeld wordt gebruikgemaakt van referenties in de cache wanneer ze worden gevonden en op een andere manier opnieuw proberen te verifiëren met de back-end:

```csharp
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

Wanneer u zich afmeldt voor een gebruiker, moet u de opgeslagen referentie ook als volgt verwijderen:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Xamarin-apps gebruiken de [Xamarin. auth] api's om referenties veilig op te slaan in een **account** object. Voor een voor beeld van het gebruik van deze Api's raadpleegt u het [AuthStore.cs] -code bestand in de [ContosoMoments foto sharing](https://github.com/azure-appservice-samples/ContosoMoments)-voor beeld.

Wanneer u door client beheerde verificatie gebruikt, kunt u ook het toegangs token in de cache opslaan dat is opgehaald van uw provider, zoals Facebook of Twitter. Dit token kan als volgt worden verstrekt om een nieuw verificatie token aan te vragen bij de back-end:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Push meldingen
De volgende onderwerpen hebben betrekking op push meldingen:

* [Registreren voor push meldingen](#register-for-push)
* [Een Microsoft Store pakket-SID verkrijgen](#package-sid)
* [Registreren bij sjablonen voor meerdere platforms](#register-xplat)

### <a name="register-for-push"></a>Procedure: registreren voor push meldingen
Met de Mobile Apps-client kunt u zich registreren voor push meldingen met Azure Notification Hubs. Bij het registreren krijgt u een ingang die u ontvangt van de platformspecifieke Push Notification Service (PNS). Vervolgens geeft u deze waarde en alle tags op wanneer u de registratie maakt. Met de volgende code wordt uw Windows-app voor push meldingen geregistreerd bij de Windows Notification Service (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Als u naar WNS pusht, moet u [een Microsoft Store-pakket-sid verkrijgen](#package-sid).  Zie [Pushmeldingen toevoegen aan uw app]voor meer informatie over Windows-apps, onder andere hoe u zich kunt registreren voor sjabloon registraties.

Het aanvragen van tags van de client wordt niet ondersteund.  Label aanvragen worden op de achtergrond verwijderd uit de registratie.
Als u uw apparaat met tags wilt registreren, maakt u een aangepaste API die gebruikmaakt van de Notification Hubs-API om de registratie namens u uit te voeren.  Roep de aangepaste API aan in plaats van de `RegisterNativeAsync()` methode.

### <a name="package-sid"></a>Procedure: een Microsoft Store pakket-SID verkrijgen
Er is een pakket-SID nodig om Push meldingen in te scha kelen in Microsoft Store-apps.  Als u een pakket-SID wilt ontvangen, moet u uw toepassing registreren bij de Microsoft Store.

Om deze waarde te verkrijgen:

1. Klik in Visual Studio Solution Explorer met de rechter muisknop op het Microsoft Store app-project, klikt u op **opslaan** > **App koppelen aan de Store...** .
2. Klik in de wizard op **volgende**, Meld u aan met uw Microsoft-account, typ een naam voor uw app in **reserve een nieuwe app-naam**en klik vervolgens op **reserveren**.
3. Nadat de app-registratie is gemaakt, selecteert u de naam van de app, klikt u op **volgende**en vervolgens op **koppelen**.
4. Meld u met uw micro soft-account aan bij het [Windows-ontwikkelaars centrum] . Klik onder **mijn apps**op de app-registratie die u hebt gemaakt.
5. Klik op **app management** > **app-identiteit**en schuif omlaag om uw pakket- **sid**te vinden.

Veel toepassingen van de pakket-SID behandelen deze als een URI, in welk geval u *MS-app://* als het schema moet gebruiken. Noteer de versie van de pakket-SID die is gevormd door het samen voegen van deze waarde als voor voegsel.

Xamarin-apps hebben een aantal extra code nodig om een app te kunnen registreren die wordt uitgevoerd op de iOS-of Android-platforms. Zie het onderwerp voor uw platform voor meer informatie:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Procedure: push-sjablonen registreren om cross-platform meldingen te verzenden
Als u sjablonen wilt registreren, gebruikt u de `RegisterAsync()` methode met de sjablonen, als volgt:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Uw sjablonen moeten worden `JObject` typen en kunnen meerdere sjablonen bevatten in de volgende JSON-indeling:

```csharp
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

De methode **RegisterAsync ()** accepteert ook secundaire tegels:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Alle tags worden verwijderd tijdens de registratie van beveiliging. Zie [werken met de .NET back-end-server-SDK voor Azure Mobile Apps] als u labels wilt toevoegen aan installaties of sjablonen binnen installaties.

Als u meldingen wilt verzenden die gebruikmaken van deze geregistreerde sjablonen, raadpleegt u de [Notification hubs-api's].

## <a name="misc"></a>Diverse onderwerpen
### <a name="errors"></a>Procedure: fouten afhandelen
Als er een fout optreedt in de back-end, genereert de client-SDK een `MobileServiceInvalidOperationException`.  In het volgende voor beeld ziet u hoe u een uitzonde ring afhandelt die wordt geretourneerd door de back-end:

```csharp
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

Een ander voor beeld van het omgaan met fout voorwaarden vindt u in het voor [Voor beeld van Mobile Apps bestanden]. Het [LoggingHandler] -voor beeld bevat een handler voor het overdragen van Logboeken voor het vastleggen van de aanvragen die naar de back-end worden verzonden.

### <a name="headers"></a>Instructies: aanvraag headers aanpassen
Ter ondersteuning van uw specifieke app-scenario moet u mogelijk de communicatie aanpassen met de back-end van de mobiele app. U kunt bijvoorbeeld een aangepaste koptekst toevoegen aan elke uitgaande aanvraag of zelfs de status codes van antwoorden wijzigen. U kunt een aangepaste [DelegatingHandler]gebruiken, zoals in het volgende voor beeld:

```csharp
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: https://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: https://github.com/SymbolSource/SymbolSource
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Verificatie toevoegen aan uw app]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Offlinesynchronisatie van gegevens in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Pushmeldingen toevoegen aan uw app]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[App Service voor Active Directory-aanmelding configureren]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[Gett]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[Hiermee wordt een verwijzing naar een tabel zonder type gemaakt]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Houd]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Selecteren]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Verdergaan]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[Naam]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Positie]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure-portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[GUID. NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows-ontwikkelaars centrum]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs-Api's]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Voor beeld van Mobile Apps bestanden]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3-documentatie]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin. auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
