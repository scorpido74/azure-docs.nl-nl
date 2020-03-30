---
title: Werken met de beheerde clientbibliotheek
description: Meer informatie over het gebruik van de .NET-clientbibliotheek voor Azure App Service Mobile Apps met Windows- en Xamarin-apps.
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 1c9fba3c13cc6e5476377d59130a95a2edaa324d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249371"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>De beheerde client gebruiken voor Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Overzicht
In deze handleiding ziet u hoe u veelvoorkomende scenario's uitvoeren met behulp van de beheerde clientbibliotheek voor Azure App Service Mobile Apps voor Windows- en Xamarin-apps. Als u nieuw bent in Mobiele Apps, u overwegen eerst de [snelstartzelfstudie voor Azure Mobile Apps][1] in te vullen. In deze gids richten we ons op de klantgerichte sdk. Zie de documentatie voor de [.NET Server SDK][2] of de [Node.js Server SDK][3]voor meer informatie over de SDK aan de serverzijde voor mobiele apps.

## <a name="reference-documentation"></a>Referentiedocumentatie
De referentiedocumentatie voor de client SDK bevindt zich hier: [Azure Mobile Apps .NET-clientreferentie][4].
U ook verschillende clientvoorbeelden vinden in de [GitHub-opslagplaats Azure-Samples.][5]

## <a name="supported-platforms"></a>Ondersteunde platformen
Het .NET-platform ondersteunt de volgende platforms:

* Xamarin Android releases voor API 19 tot en met 24 (KitKat via Nougat)
* Xamarin iOS releases voor iOS versies 8.0 en hoger
* Universeel Windows-platform
* Windows Phone 8.1
* Windows Phone 8.0, behalve voor Silverlight-toepassingen

De verificatie 'serverstroom' maakt gebruik van een WebView voor de gepresenteerde gebruikersinterface.  Als het apparaat geen WebView-gebruikersinterface kan presenteren, zijn andere verificatiemethoden nodig.  Deze SDK is dus niet geschikt voor Watch-type of soortgelijke beperkte apparaten.

## <a name="setup-and-prerequisites"></a><a name="setup"></a>Instellingen en vereisten
We gaan ervan uit dat u uw backendproject voor mobiele apps al hebt gemaakt en gepubliceerd, dat ten minste één tabel bevat.  In de code die in dit `TodoItem` onderwerp wordt gebruikt, `Id`wordt `Text`de `Complete`tabel benoemd en bevat de volgende kolommen: , , en . Deze tabel is dezelfde tabel die is gemaakt wanneer u de snelstart van [Azure Mobile Apps voltooit.][1]

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

Het [JsonPropertyAttribute][6] wordt gebruikt om de *Toewijzing PropertyName* tussen het clientveld en het tabelveld te definiëren.

Zie het [SDK-onderwerp .NET Server SDK][7] of het [SDK-onderwerp Node.js Server][8]voor meer informatie over het maken van tabellen in de back-end van uw mobiele apps. Als u uw back-end voor mobiele app hebt gemaakt in de Azure-portal met de QuickStart, u ook de instelling **Eenvoudige tabellen** gebruiken in de [Azure-portal.]

### <a name="how-to-install-the-managed-client-sdk-package"></a>Hoe: Het managed client SDK-pakket installeren
Gebruik een van de volgende methoden om het managed client SDK-pakket voor mobiele apps van [NuGet][9]te installeren:

* **Visual Studio** Klik met de rechtermuisknop op uw project, `Microsoft.Azure.Mobile.Client` klik op **NuGet-pakketten beheren,** zoek naar het pakket en klik vervolgens op **Installeren**.
* **Xamarin Studio** Klik met de rechtermuisknop op uw project, klik op > **NuGet-pakketten** **toevoegen,** zoek naar het `Microsoft.Azure.Mobile.Client` pakket en klik vervolgens op Pakket **toevoegen**.

Vergeet in uw hoofdactiviteitsbestand het volgende toe te voegen **met een** instructie:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Alle ondersteuningspakketten waarnaar wordt verwezen in uw Android-project, moeten dezelfde versie hebben. De SDK `Xamarin.Android.Support.CustomTabs` heeft afhankelijkheid voor Android-platform, dus als uw project nieuwere ondersteuningspakketten gebruikt, moet u dit pakket met de vereiste versie rechtstreeks installeren om conflicten te voorkomen.

### <a name="how-to-work-with-debug-symbols-in-visual-studio"></a><a name="symbolsource"></a>How to: Werken met foutopsporingssymbolen in Visual Studio
De symbolen voor de naamruimte van Microsoft.Azure.Mobile zijn beschikbaar op [SymbolSource][10].  Raadpleeg de [instructies van SymbolSource][11] om SymbolSource te integreren met Visual Studio.

## <a name="create-the-mobile-apps-client"></a><a name="create-client"></a>De client Mobiele apps maken
Met de volgende code wordt het [MobileServiceClient-object][12] gemaakt dat wordt gebruikt om toegang te krijgen tot de back-end van uw mobiele app.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

Vervang in de vorige `MOBILE_APP_URL` code de URL van de backend van de mobiele app, die wordt gevonden in het blad voor de back-end van uw mobiele app in de [Azure-portal.] Het MobileServiceClient-object moet een singleton zijn.

## <a name="work-with-tables"></a>Werken met tabellen
In de volgende sectie wordt beschreven hoe u records zoekt en ophaalt en de gegevens in de tabel wijzigt.  De volgende onderwerpen komen aan bod:

* [Een tabelverwijzing maken](#instantiating)
* [Querygegevens](#querying)
* [Geretourneerde gegevens filteren](#filtering)
* [Geretourneerde gegevens sorteren](#sorting)
* [Gegevens retourneren op pagina's](#paging)
* [Specifieke kolommen selecteren](#selecting)
* [Zoek een record op per id](#lookingup)
* [Omgaan met niet-getypte query's](#untypedqueries)
* [Gegevens invoegen](#inserting)
* Gegevens bijwerken
* [Gegevens verwijderen](#deleting)
* [Conflictoplossing en optimistische gelijktijdigheid](#optimisticconcurrency)
* [Binding aan een Windows-gebruikersinterface](#binding)
* [Het paginaformaat wijzigen](#pagesize)

### <a name="how-to-create-a-table-reference"></a><a name="instantiating"></a>Hoe: Een tabelverwijzing maken
Alle code die toegang heeft tot of wijzigt gegevens in `MobileServiceTable` een backend tabel roept functies op het object. Een verwijzing naar de tabel verkrijgen door de [methode GetTable] aan te roepen, als volgt:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Het geretourneerde object gebruikt het getypte serialisatiemodel. Een ongetypt serialisatiemodel wordt ook ondersteund. In het volgende [voorbeeld wordt een verwijzing gemaakt naar een niet-getypte tabel:]

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

In niet-getypte query's moet u de onderliggende OData-querytekenreeks opgeven.

### <a name="how-to-query-data-from-your-mobile-app"></a><a name="querying"></a>How to: Querygegevens van uw mobiele app
In deze sectie wordt beschreven hoe u query's uitgeven aan de back-end van de mobiele app, die de volgende functionaliteit bevat:

* [Geretourneerde gegevens filteren](#filtering)
* [Geretourneerde gegevens sorteren](#sorting)
* [Gegevens retourneren op pagina's](#paging)
* [Specifieke kolommen selecteren](#selecting)
* [Gegevens opzoeken op id](#lookingup)

> [!NOTE]
> Een paginaformaat op serverbasis wordt afgedwongen om te voorkomen dat alle rijen worden geretourneerd.  Paging voorkomt dat standaardaanvragen voor grote gegevenssets een negatieve invloed hebben op de service.  Als u meer dan 50 `Skip` `Take` rijen wilt retourneren, gebruikt u de methode en de methode, zoals beschreven in [Gegevens retourneren op pagina's](#paging).

### <a name="how-to-filter-returned-data"></a><a name="filtering"></a>Hoe: geretourneerde gegevens filteren
De volgende code illustreert hoe u `Where` gegevens filtert door een clausule in een query op te nemen. Het retourneert `todoTable` `Complete` alle items `false`van wie de eigenschap gelijk is aan . Met de functie [Waar] wordt een rijfilterpredicaat op de query op de tabel gebruikt.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

U de URI van het verzoek bekijken dat naar de backend wordt verzonden met behulp van software voor berichtinspectie, zoals hulpprogramma's voor browserontwikkelaars of [Fiddler.] Als u de aanvraag URI bekijkt, ziet u dat de querytekenreeks is gewijzigd:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Deze OData-aanvraag wordt door de Server SDK vertaald in een SQL-query:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

De functie die wordt `Where` doorgegeven aan de methode kan een willekeurig aantal voorwaarden hebben.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Dit voorbeeld wordt door de Server SDK vertaald naar een SQL-query:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Deze query kan ook worden opgesplitst in meerdere clausules:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

De twee methoden zijn gelijkwaardig en kunnen door elkaar worden gebruikt.  De voormalige&mdash;optie om meerdere predicaten in&mdash;één query samen te stellen, is compacter en aanbevolen.

De `Where` clausule ondersteunt bewerkingen die worden vertaald naar de OData-subset. De werkzaamheden omvatten:

* Relationele operatoren (==, <, <=, >, >=),
* Rekenkundige operatoren (+, -, /, *, %),
* Getalprecisie (Math.Floor, Math.Ceiling),
* Tekenreeksfuncties (Lengte, Subtekenreeks, Vervangen, IndexOf, StartsWith, EndsWith),
* Datumeigenschappen (jaar, maand, dag, uur, minuut, tweede),
* Toegangseigenschappen van een object en
* Expressies die een van deze bewerkingen combineren.

Wanneer u bedenkt wat de Server SDK ondersteunt, u de [OData v3-documentatie]overwegen.

### <a name="how-to-sort-returned-data"></a><a name="sorting"></a>Hoe: Geretourneerde gegevens sorteren
In de volgende code wordt uitgelegd hoe u gegevens sorteren door een functie [OrderBy] of [OrderByDescending] in de query op te nemen. Hiermee worden `todoTable` items geretourneerd van `Text` gesorteerde oplopend naar het veld.

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

### <a name="how-to-return-data-in-pages"></a><a name="paging"></a>How to: Gegevens retourneren op pagina's
Standaard retourneert de backend alleen de eerste 50 rijen. U het aantal geretourneerde rijen verhogen door de [methode Nemen aan te] roepen. Gebruik `Take` samen met de methode [Overslaan] om een specifieke "pagina" op te vragen van de totale gegevensset die door de query wordt geretourneerd. De volgende query, wanneer uitgevoerd, retourneert de drie bovenste items in de tabel.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

De volgende herziene query slaat de eerste drie resultaten over en retourneert de volgende drie resultaten. Deze query produceert de tweede "pagina" van gegevens, waarbij het paginaformaat drie items is.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

De methode [IncludeTotalCount] vraagt het totale aantal aanvragen voor *alle* records die zouden zijn geretourneerd, waarbij elke opgegeven paging/limit-clausule wordt genegeerd:

```csharp
query = query.IncludeTotalCount();
```

In een echte wereld-app u query's gebruiken die vergelijkbaar zijn met het vorige voorbeeld met een pieperbesturingselement of vergelijkbare gebruikersinterface om tussen pagina's te navigeren.

> [!NOTE]
> Als u de limiet van 50 rijen in een back-end van een mobiele app wilt overschrijven, moet u ook het [EnableQueryAttribuut] toepassen op de openbare GET-methode en het paginggedrag opgeven. Wanneer toegepast op de methode, stelt het volgende de maximale geretourneerde rijen in op 1000:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="how-to-select-specific-columns"></a><a name="selecting"></a>Hoe: Specifieke kolommen selecteren
U opgeven welke set eigenschappen in de resultaten moet worden opgenomen door een [selectieclausule] aan uw query toe te voegen. In de volgende code ziet u bijvoorbeeld hoe u slechts één veld selecteert en hoe u meerdere velden selecteert en opmaakt:

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

Alle functies die tot nu toe zijn beschreven zijn additief, dus we kunnen ze blijven ketenen. Elke geketende aanroep heeft meer invloed op de query. Nog een voorbeeld:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="how-to-look-up-data-by-id"></a><a name="lookingup"></a>How to: Gegevens opzoeken op id
De functie [LookupAsync] kan worden gebruikt om objecten uit de database op te zoeken met een bepaalde id.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="how-to-execute-untyped-queries"></a><a name="untypedqueries"></a>How to: Niet-getypte query's uitvoeren
Wanneer u een query uitvoert met een niet-getypt tabelobject, moet u de Querytekenreeks OData expliciet opgeven door [ReadAsync]aan te roepen, zoals in het volgende voorbeeld:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Je krijgt json-waarden terug die je gebruiken als een vastgoedtas. Zie de [site Json.NET] voor meer informatie over JToken en Newtonsoft Json.NET.

### <a name="how-to-insert-data-into-a-mobile-app-backend"></a><a name="inserting"></a>Hoe: Gegevens invoegen in een back-end van een mobiele app
Alle clienttypen moeten een lid met de naam **Id**bevatten, wat standaard een tekenreeks is. Deze **id** is vereist om CRUD-bewerkingen uit te voeren en voor offline synchronisatie. In de volgende code ziet u hoe u de methode [InsertAsync] gebruikt om nieuwe rijen in een tabel in te voegen. De parameter bevat de gegevens die als .NET-object moeten worden ingevoegd.

```csharp
await todoTable.InsertAsync(todoItem);
```

Als een unieke aangepaste ID-waarde `todoItem` niet is opgenomen in de tijdens een invoeging, wordt een GUID gegenereerd door de server.
U de gegenereerde id ophalen door het object te inspecteren nadat de aanroep is geretourneerd.

Als u niet-getypte gegevens wilt invoegen, u gebruik maken van Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Hier is een voorbeeld met een e-mailadres als een unieke tekenreeks-id:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Werken met ID-waarden
Mobile Apps ondersteunt unieke aangepaste tekenreekswaarden voor de **id-kolom** van de tabel. Met een tekenreekswaarde kunnen toepassingen aangepaste waarden gebruiken, zoals e-mailadressen of gebruikersnamen voor de id.  String-iDs bieden u de volgende voordelen:

* Id's worden gegenereerd zonder een retourtje naar de database te maken.
* Records zijn gemakkelijker te samenvoegen vanuit verschillende tabellen of databases.
* Id-waarden kunnen beter integreren met de logica van een toepassing.

Wanneer een tekenreeks-ID-waarde niet is ingesteld op een ingevoegde record, genereert de backend van de mobiele app een unieke waarde voor de ID. U de [Guid.NewGuid-methode] gebruiken om uw eigen ID-waarden te genereren, op de client of in de backend.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="how-to-modify-data-in-a-mobile-app-backend"></a><a name="modifying"></a>Hoe: gegevens wijzigen in een back-end van een mobiele app
De volgende code illustreert hoe u de [Methode UpdateAsync] gebruiken om een bestaande record met dezelfde ID bij te werken met nieuwe informatie. De parameter bevat de gegevens die moeten worden bijgewerkt als een .NET-object.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Als u niet-getypte gegevens wilt bijwerken, u als volgt gebruik maken van [Json.NET:]

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Een `id` veld moet worden opgegeven bij het bijwerken. De backend `id` gebruikt het veld om te bepalen welke rij moet worden bijgewerkt. Het `id` veld kan worden verkregen `InsertAsync` uit het resultaat van de oproep. Een `ArgumentException` wordt verhoogd als u een item `id` probeert bij te werken zonder de waarde op te geven.

### <a name="how-to-delete-data-in-a-mobile-app-backend"></a><a name="deleting"></a>Hoe: Gegevens verwijderen in een back-end van een mobiele app
In de volgende code ziet u hoe u de [methode DeleteAsync] gebruikt om een bestaande instantie te verwijderen. De instantie wordt geïdentificeerd `id` door het `todoItem`veld dat is ingesteld op de .

```csharp
await todoTable.DeleteAsync(todoItem);
```

Als u niet-getypte gegevens wilt verwijderen, u als volgt gebruik maken van Json.NET:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Wanneer u een verwijderverzoek indient, moet een ID worden opgegeven. Andere eigenschappen worden niet doorgegeven aan de service of worden genegeerd bij de service. Het resultaat `DeleteAsync` van een `null`gesprek is meestal . De ID die u moet doorgeven, `InsertAsync` kan worden verkregen uit het resultaat van het gesprek. A `MobileServiceInvalidOperationException` wordt gegooid wanneer u een item `id` probeert te verwijderen zonder het veld op te geven.

### <a name="how-to-use-optimistic-concurrency-for-conflict-resolution"></a><a name="optimisticconcurrency"></a>Hoe: Optimistische gelijktijdigheid gebruiken voor conflictoplossing
Twee of meer clients kunnen tegelijkertijd wijzigingen in hetzelfde item schrijven. Zonder conflictdetectie zou de laatste schrijven eerdere updates overschrijven. **Optimistische gelijktijdigheidscontrole** gaat ervan uit dat elke transactie kan worden gepleegd en maakt daarom geen gebruik van resourcevergrendeling.  Voordat u een transactie uitvoert, controleert de optimistische gelijktijdigheidscontrole of geen enkele andere transactie de gegevens heeft gewijzigd. Als de gegevens zijn gewijzigd, wordt de committing transactie teruggedraaid.

Mobile Apps ondersteunt optimistische gelijktijdigheidscontrole door `version` wijzigingen in elk item bij te houden met behulp van de kolom systeemeigenschap die is gedefinieerd voor elke tabel in de back-end van uw mobiele app. Elke keer dat een record wordt `version` bijgewerkt, stelt Mobile Apps de eigenschap voor die record in op een nieuwe waarde. Tijdens elk updateverzoek `version` wordt de eigenschap van de record die bij de aanvraag is opgenomen, vergeleken met dezelfde eigenschap voor de record op de server. Als de versie die met de aanvraag is doorgegeven, `MobileServicePreconditionFailedException<T>` niet overeenkomt met de backend, wordt een uitzondering gemaakt in de clientbibliotheek. Het type dat bij de uitzondering is opgenomen, is de record van de backend met de serversversie van de record. De toepassing kan deze informatie vervolgens gebruiken om te beslissen `version` of het updateverzoek opnieuw wordt uitgevoerd met de juiste waarde vanaf de backend om wijzigingen door te voeren.

Definieer een kolom in de `version` tabelklasse voor de eigenschap systeem om optimistische gelijktijdigheid mogelijk te maken. Bijvoorbeeld:

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

Toepassingen met niet-getypte tabellen maken `Version` optimistische gelijktijdigheid mogelijk door de vlag op de van de tabel als volgt in te `SystemProperties` stellen.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Naast het inschakelen van optimistische gelijktijdigheid, `MobileServicePreconditionFailedException<T>` moet u ook de uitzondering in uw code vangen wanneer u [UpdateAsync]aanroept.  Los het conflict op `version` door de juiste toepassing toe te passen op de bijgewerkte record- en [oproepupdateAsync] met de opgeloste record. In de volgende code ziet u hoe u een schrijfconflict oplossen nadat u dit hebt gedetecteerd:

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

Zie het onderwerp [Offline gegevens synchroniseren in Azure Mobile Apps] voor meer informatie.

### <a name="how-to-bind-mobile-apps-data-to-a-windows-user-interface"></a><a name="binding"></a>How to: Mobiele apps-gegevens binden aan een Windows-gebruikersinterface
In deze sectie ziet u hoe geretourneerde gegevensobjecten worden weergegeven met behulp van gebruikersinterface-elementen in een Windows-app.  De volgende voorbeeldcode wordt gekoppeld aan de bron van de lijst met een query voor onvolledige items. De [MobileServiceCollection] maakt een mobile apps-bewuste bindende collectie.

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

Sommige besturingselementen in de beheerde runtime ondersteunen een interface genaamd [ISupportIncrementalLoading]. Met deze interface kunnen besturingselementen extra gegevens opvragen wanneer de gebruiker schuift. Er is ingebouwde ondersteuning voor deze interface voor universele Windows-apps via [MobileServiceIncrementalLoadingCollection], die automatisch de oproepen van de besturingselementen afhandelt. Gebruik `MobileServiceIncrementalLoadingCollection` in Windows-apps als volgt:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Als u de nieuwe verzameling wilt gebruiken op Windows `ToCollection` Phone 8- `IMobileServiceTableQuery<T>` `IMobileServiceTable<T>`en 'Silverlight'-apps, gebruikt u de extensiemethoden op en . Als u gegevens `LoadMoreItemsAsync()`wilt laden, belt u .

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Wanneer u de verzameling `ToCollectionAsync` gebruikt `ToCollection`die is gemaakt door aan te roepen of , krijgt u een verzameling die kan worden gekoppeld aan ui-besturingselementen.  Deze collectie is paging-bewust.  Omdat de verzameling gegevens van het netwerk laadt, mislukt het laden soms. Als u dergelijke fouten `OnException` wilt `MobileServiceIncrementalLoadingCollection` afhandelen, overschrijft `LoadMoreItemsAsync`u de methode om uitzonderingen die voortvloeien uit aanroepen naar .

Overweeg of uw tabel veel velden heeft, maar u wilt er slechts een aantal weergeven in uw controle. U de richtlijnen in de vorige sectie['Specifieke kolommen selecteren'](#selecting)gebruiken om specifieke kolommen te selecteren die u in de gebruikersinterface wilt weergeven.

### <a name="change-the-page-size"></a><a name="pagesize"></a>Het paginaformaat wijzigen
Azure Mobile Apps retourneert standaard maximaal 50 items per aanvraag.  U de paging-grootte wijzigen door de maximale paginagrootte op zowel de client als de server te vergroten.  Geef op wanneer `PullOptions` u `PullAsync()`het gewenste paginaformaat wilt vergroten:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Ervan uitgaande dat `PageSize` u het aantal van de 100 of meer binnen de server hebt gemaakt, retourneert een verzoek maximaal 100 items.

## <a name="work-with-offline-tables"></a><a name="#offlinesync"></a>Werken met offlinetabellen
Offline tabellen gebruiken een lokale SQLite-winkel om gegevens op te slaan voor gebruik wanneer ze offline zijn.  Alle tabelbewerkingen worden uitgevoerd tegen de lokale SQLite-winkel in plaats van de externe serveropslag.  Als u een offlinetabel wilt maken, bereidt u eerst uw project voor:

1. Klik in Visual Studio met de rechtermuisknop op de oplossing > **NuGet-pakketten beheren voor oplossing...** en zoek en installeer vervolgens het **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-pakket voor alle projecten in de oplossing.
2. (Optioneel) Als u Windows-apparaten wilt ondersteunen, installeert u een van de volgende SQLite-runtime-pakketten:

   * **Windows 8.1 Runtime:** Installeer [SQLite voor Windows 8.1][3].
   * **Windows Phone 8.1:** Installeer [SQLite voor Windows Phone 8.1][4].
   * **Universeel Windows-platform** Installeer [SQLite voor de Universele Windows][5].
3. (Optioneel). Klik voor **Windows-apparaten** > op**Naslagwaarde...**, vouw de **Windows-map** uit > **Extensies**en schakel vervolgens de juiste **SQLite voor Windows** SDK in, samen met de **Visual C++ 2013 Runtime voor Windows** SDK.
    De SQLite SDK-namen variëren enigszins per Windows-platform.

Voordat een tabelverwijzing kan worden gemaakt, moet de lokale winkel worden voorbereid:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Winkelinitialisatie wordt normaal gesproken gedaan onmiddellijk nadat de client is gemaakt.  De **OfflineDbPath** moet een bestandsnaam zijn die geschikt is voor gebruik op alle platforms die u ondersteunt.  Als het pad een volledig gekwalificeerd pad is (dat wil zeggen, het begint met een slash), dan wordt dat pad gebruikt.  Als het pad niet volledig gekwalificeerd is, wordt het bestand op een platformspecifieke locatie geplaatst.

* Voor iOS- en Android-apparaten is het standaardpad de map 'Persoonlijke bestanden'.
* Voor Windows-apparaten is het standaardpad de toepassingsspecifieke map 'AppData'.

Een tabelverwijzing kan worden `GetSyncTable<>` verkregen volgens de methode:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

U hoeft zich niet te verifiëren om een offline tabel te gebruiken.  U hoeft alleen te verifiëren wanneer u communiceert met de backendservice.

### <a name="syncing-an-offline-table"></a><a name="syncoffline"></a>Een offlinetabel synchroniseren
Offlinetabellen worden standaard niet gesynchroniseerd met de backend.  Synchronisatie is opgesplitst in twee stukken.  U wijzigingen afzonderlijk pushen van het downloaden van nieuwe items.  Hier is een typische synchronisatiemethode:

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

Als het eerste `PullAsync` argument null is, wordt incrementele synchronisatie niet gebruikt.  Elke synchronisatiebewerking haalt alle records op.

De SDK voert `PushAsync()` een impliciete uit voordat records worden opgevraagd.

Conflictafhandeling gebeurt `PullAsync()` op een methode.  U conflicten op dezelfde manier behandelen als online tabellen.  Het conflict wordt `PullAsync()` geproduceerd wanneer wordt aangeroepen in plaats van tijdens het invoegen, bijwerken of verwijderen. Als er meerdere conflicten optreden, worden ze gebundeld in één MobileServicePushFailedException.  Behandel elke fout afzonderlijk.

## <a name="work-with-a-custom-api"></a><a name="#customapi"></a>Werken met een aangepaste API
Met een aangepaste API u aangepaste eindpunten definiëren die serverfunctionaliteit blootstellen die niet wordt toegewezen aan een bewerking voor invoegen, bijwerken, verwijderen of lezen. Door een aangepaste API te gebruiken, u meer controle hebben over berichten, waaronder het lezen en instellen van HTTP-berichtkoppen en het definiëren van een andere berichthoofdindeling dan JSON.

U roept een aangepaste API aan door een van de [InvokeApiAsync-methoden] op de client aan te roepen. De volgende coderegel stuurt bijvoorbeeld een POST-verzoek naar de **completeAll** API op de backend:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Dit formulier is een getypte methodeaanroep en vereist dat het **retourtype MarkAllResult** wordt gedefinieerd. Zowel getypte als niet-getypte methoden worden ondersteund.

De methode InvokeApiAsync() bereidt '/api/' voor op de API die u wilt aanroepen, tenzij de API begint met een '/'.
Bijvoorbeeld:

* `InvokeApiAsync("completeAll",...)`aanroepen /api/compleetAlles op de backend
* `InvokeApiAsync("/.auth/me",...)`gesprekken /.auth/me op de backend

U InvokeApiAsync gebruiken om een WebAPI aan te roepen, inclusief de WebAPI's die niet zijn gedefinieerd met Azure Mobile Apps.  Wanneer u InvokeApiAsync() gebruikt, worden de juiste headers, inclusief verificatiekoppen, verzonden met het verzoek.

## <a name="authenticate-users"></a><a name="authentication"></a>Gebruikers verifiëren
Mobile Apps ondersteunt het authenticeren en autoriseren van app-gebruikers met behulp van verschillende externe identiteitsproviders: Facebook, Google, Microsoft-account, Twitter en Azure Active Directory. U machtigingen voor tabellen instellen om de toegang voor specifieke bewerkingen te beperken tot alleen geverifieerde gebruikers. U ook de identiteit van geverifieerde gebruikers gebruiken om autorisatieregels in serverscripts te implementeren. Zie de zelfstudie [Verificatie toevoegen aan uw app] voor meer informatie.

Twee verificatiestromen worden ondersteund: *clientbeheerde* en *serverbeheerde* stroom. De serverbeheerde stroom biedt de eenvoudigste verificatie-ervaring, omdat deze is gebaseerd op de webverificatie-interface van de provider. De door de client beheerde stroom zorgt voor een diepere integratie met apparaatspecifieke mogelijkheden, omdat deze afhankelijk is van providerspecifieke apparaatspecifieke SDK's.

> [!NOTE]
> We raden u aan een door de klant beheerde stroom te gebruiken in uw productie-apps.

Als u verificatie wilt instellen, moet u uw app registreren bij een of meer identiteitsproviders.  De identiteitsprovider genereert een client-id en een clientgeheim voor uw app.  Deze waarden worden vervolgens ingesteld in uw backend om verificatie/autorisatie van Azure App Service in te schakelen.  Volg de gedetailleerde instructies in de zelfstudie [Verificatie toevoegen aan uw app voor]meer informatie.

In deze rubriek komen de volgende onderwerpen aan bod:

* [Verificatie die door de client wordt beheerd](#clientflow)
* [Serverbeheerde verificatie](#serverflow)
* [Het verificatietoken incachen](#caching)

### <a name="client-managed-authentication"></a><a name="clientflow"></a>Verificatie die door de client wordt beheerd
Uw app kan onafhankelijk contact opnemen met de identiteitsprovider en vervolgens het geretourneerde token opgeven tijdens het inloggen met uw backend. Met deze clientstroom u gebruikers één aanmeldingservaring bieden of aanvullende gebruikersgegevens ophalen bij de identiteitsprovider. Clientflow-verificatie heeft de voorkeur boven het gebruik van een serverstroom, omdat de identiteitsprovider SDK een meer native UX-gevoel biedt en extra aanpassingen mogelijk maakt.

Voorbeelden hiervan zijn de volgende client-flow verificatiepatronen:

* [Active Directory-verificatiebibliotheek](#adal)
* [Facebook of Google](#client-facebook)

#### <a name="authenticate-users-with-the-active-directory-authentication-library"></a><a name="adal"></a>Gebruikers verifiëren met de Active Directory-verificatiebibliotheek
U de Active Directory Authentication Library (ADAL) gebruiken om gebruikersverificatie vanuit de client te starten met Azure Active Directory-verificatie.

1. Configureer de backend van uw mobiele app voor AAD-aanmelding door de zelfstudie [App-service voor Active Directory-aanmelding te configureren.] Zorg ervoor dat u de optionele stap van het registreren van een native clienttoepassing voltooit.
2. Open uw project in Visual Studio of Xamarin `Microsoft.IdentityModel.Clients.ActiveDirectory` Studio en voeg een verwijzing toe naar het NuGet-pakket. Voeg bij het zoeken pre-releaseversies toe.
3. Voeg de volgende code toe aan uw toepassing, afhankelijk van het platform dat u gebruikt. Maak in elk van deze opties de volgende vervangingen:

   * Vervang **INSERT-AUTHORITY-HERE** door de naam van de tenant waarin u uw aanvraag heeft ingericht. Het formaat https://login.microsoftonline.com/contoso.onmicrosoft.commoet zijn . Deze waarde kan worden gekopieerd van het tabblad Domein in uw Azure Active Directory in de [Azure-portal.]
   * Vervang **INSERT-RESOURCE-ID-HERE** door de client-ID voor uw mobiele app back-end. U de client-id verkrijgen via het tabblad **Geavanceerd** onder **Azure Active Directory-instellingen** in de portal.
   * Vervang **INSERT-CLIENT-ID-HERE** door de client-id die u hebt gekopieerd van de native clienttoepassing.
   * Vervang **INSERT-REDIRECT-URI-HERE** door het *eindpunt /.auth/login/done van* uw site, met behulp van het HTTPS-schema. Deze waarde moet *https://contoso.azurewebsites.net/.auth/login/done*vergelijkbaar zijn met .

     De code die nodig is voor elk platform volgt:

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

#### <a name="single-sign-on-using-a-token-from-facebook-or-google"></a><a name="client-facebook"></a>Eén aanmelding met een token van Facebook of Google
U de clientstroom gebruiken zoals in dit fragment voor Facebook of Google.

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

### <a name="server-managed-authentication"></a><a name="serverflow"></a>Serverbeheerde verificatie
Zodra u uw identiteitsprovider hebt geregistreerd, belt u de [LoginAsync-methode] op de [MobileServiceClient] met de [mobileserviceauthenticatieprovider-waarde] van uw provider. De volgende code initieert bijvoorbeeld een serverstroomaanmelding met Facebook.

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

Als u een andere identiteitsprovider dan Facebook gebruikt, wijzigt u de waarde van [MobileServiceAuthenticationProvider] in de waarde voor uw provider.

In een serverstroom beheert Azure App Service de OAuth-verificatiestroom door de aanmeldingspagina van de geselecteerde provider weer te geven.  Zodra de identiteitsprovider terugkeert, genereert Azure App Service een Verificatietoken voor App Service. De [LoginAsync-methode] retourneert een [MobileServiceUser], die zowel de [userid] van de geverifieerde gebruiker als de [MobileServiceAuthenticationToken]levert, als een JSON-webtoken (JWT). Dit token kan worden opgeslagen in de cache en opnieuw worden gebruikt totdat het verloopt. Zie [Het verificatietoken caching voor](#caching)meer informatie.

### <a name="caching-the-authentication-token"></a><a name="caching"></a>Het verificatietoken incachen
In sommige gevallen kan de aanroep naar de inlogmethode worden vermeden na de eerste succesvolle verificatie door het verificatietoken van de provider op te slaan.  Microsoft Store- en UWP-apps kunnen [PasswordVault] als volgt gebruiken om het huidige verificatietoken in de cache te plaatsen na een succesvolle aanmelding:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

De userid-waarde wordt opgeslagen als de gebruikersnaam van de referentie en het token is het opgeslagen als wachtwoord. Bij volgende opstartapparaten u de **PasswordVault** controleren op referenties in de cache. In het volgende voorbeeld worden referenties in de cache gebruikt wanneer ze worden gevonden en wordt anderszins geprobeerd opnieuw te verifiëren met de backend:

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

Wanneer u een gebruiker afmeldt, moet u ook de opgeslagen referenties als volgt verwijderen:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Xamarin-apps gebruiken de [Xamarin.Auth] API's om referenties veilig op te slaan in een **accountobject.** Zie het AuthStore.cs-codebestand in [AuthStore.cs] het voorbeeld van het delen van [foto's van ContosoMoments voor](https://github.com/azure-appservice-samples/ContosoMoments)een voorbeeld van het gebruik van deze API's.

Wanneer u verificatie met clientbeheerde verificatie gebruikt, u ook het toegangstoken dat is verkregen bij uw provider, zoals Facebook of Twitter, in de cache opslaan. Dit token kan als volgt worden geleverd om een nieuw verificatietoken van de backend aan te vragen:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="push-notifications"></a><a name="pushnotifications"></a>Pushmeldingen
De volgende onderwerpen hebben betrekking op pushmeldingen:

* [Registreren voor pushmeldingen](#register-for-push)
* [Een Sid voor een Microsoft Store-pakket verkrijgen](#package-sid)
* [Registreren met cross-platform sjablonen](#register-xplat)

### <a name="how-to-register-for-push-notifications"></a><a name="register-for-push"></a>Hoe: Registreren voor pushmeldingen
Met de client Mobiele apps u zich registreren voor pushmeldingen met Azure Notification Hubs. Bij registratie krijgt u een greep die u verkrijgt van de platformspecifieke Push Notification Service (PNS). U geeft deze waarde vervolgens samen met eventuele tags wanneer u de registratie maakt. Met de volgende code wordt uw Windows-app geregistreerd voor pushmeldingen met de Windows Notification Service (WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Als u duwen naar WNS, dan moet je [het verkrijgen van een Microsoft Store pakket SID](#package-sid).  Zie [Pushmeldingen toevoegen aan uw app voor]meer informatie over Windows-apps, waaronder het registreren voor sjabloonregistraties.

Het aanvragen van tags van de client wordt niet ondersteund.  Tagverzoeken worden in stilte uit de registratie verwijderd.
Als u uw apparaat wilt registreren met tags, maakt u een aangepaste API die de API voor meldingshubs gebruikt om de registratie namens u uit te voeren.  Roep de aangepaste API `RegisterNativeAsync()` aan in plaats van de methode.

### <a name="how-to-obtain-a-microsoft-store-package-sid"></a><a name="package-sid"></a>How to: Een Microsoft Store-pakket SID verkrijgen
Een pakket SID is nodig voor het inschakelen van pushmeldingen in Microsoft Store-apps.  Als u een pakket SID wilt ontvangen, registreert u uw toepassing bij de Microsoft Store.

Om deze waarde te verkrijgen:

1. Klik in Visual Studio Solution Explorer met de rechtermuisknop op het Microsoft Store-app-project en klik op **App** > **in de winkel koppelen met de Store...**.
2. Klik in de wizard op **Volgende,** meld u aan met uw Microsoft-account, typ een naam voor uw app in **Een nieuwe app-naam behouden**en klik vervolgens op **Reserveren**.
3. Nadat de app-registratie is gemaakt, selecteert u de naam van de app, klikt u op **Volgende**en klikt u vervolgens op **Koppelen**.
4. Meld u aan bij het [Windows Dev Center] met uw Microsoft-account. Klik **onder Mijn apps**op de app-registratie die u hebt gemaakt.
5. Klik op > **App-beheer App-identiteit**en blader naar beneden om uw **pakket SID**te vinden. **App management**

Veel toepassingen van het pakket SID behandelen het als een URI, in welk geval je nodig hebt om *ms-app://* te gebruiken als de regeling. Noteer de versie van uw pakket SID gevormd door het concatening van deze waarde als een voorvoegsel.

Xamarin-apps hebben extra code nodig om een app te kunnen registreren die wordt uitgevoerd op de iOS- of Android-platforms. Zie voor meer informatie het onderwerp voor uw platform:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="how-to-register-push-templates-to-send-cross-platform-notifications"></a><a name="register-xplat"></a>How to: Pushtemplates registreren om cross-platform meldingen te verzenden
Als u sjablonen `RegisterAsync()` wilt registreren, gebruikt u de methode met de sjablonen als volgt:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Uw sjablonen `JObject` moeten typen zijn en kunnen meerdere sjablonen bevatten in de volgende JSON-indeling:

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

De methode **RegisterAsync()** accepteert ook secundaire tegels:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Alle tags worden verwijderd tijdens de registratie voor de veiligheid. Zie [Werken met de .NET-backendserver SDK voor Azure Mobile Apps voor het toevoegen van tags aan installaties of sjablonen in installaties].

Als u meldingen wilt verzenden met behulp van deze geregistreerde sjablonen, raadpleegt u de [API's voor meldingshubs].

## <a name="miscellaneous-topics"></a><a name="misc"></a>Diverse onderwerpen
### <a name="how-to-handle-errors"></a><a name="errors"></a>Hoe: Fouten verwerken
Wanneer er een fout optreedt in de `MobileServiceInvalidOperationException`backend, verhoogt de sdk van de client een .  In het volgende voorbeeld ziet u hoe u een uitzondering verwerken die wordt geretourneerd door de backend:

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

Een ander voorbeeld van het omgaan met foutvoorwaarden is te vinden in de [Mobile Apps Files Sample]. In het voorbeeld [LoggingHandler] wordt een logboekgemachtigdehandler gegeven om de aanvragen die worden uitgevoerd op de backend te registreren.

### <a name="how-to-customize-request-headers"></a><a name="headers"></a>How to: Aanvraagkoppen aanpassen
Als u uw specifieke app-scenario wilt ondersteunen, moet u de communicatie met de back-end van de mobiele app mogelijk aanpassen. U bijvoorbeeld een aangepaste koptekst toevoegen aan elke uitgaande aanvraag of zelfs statuscodes voor reacties wijzigen. U een aangepaste [DelegatingHandler]gebruiken, zoals in het volgende voorbeeld:

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
[App-service configureren voor aanmelding in Active Directory]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollectie]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementLoadingCollectie]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceGebruiker]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable (GetTable)]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[maakt een verwijzing naar een niet-getypte tabel]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[Totaal aantal opnemen]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[ApiAsync aanroepen]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[Orderby]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[Orderbydescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Nemen]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Selecteren]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Overslaan]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[Userid]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Waar]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure-portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid (Guid.NewGuid)]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/overview
[Delegerende Handler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[API's voor meldingshubs]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Voorbeeld van bestanden voor mobiele apps]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3-documentatie]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
