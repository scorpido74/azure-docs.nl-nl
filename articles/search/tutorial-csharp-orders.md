---
title: C# zelfstudie over het bestellen van resultaten
titleSuffix: Azure Cognitive Search
description: In deze zelfstudie wordt uitgelegd hoe u zoekresultaten bestellen. Het bouwt voort op een eerder hotels project, bestellen op primaire eigenschap, secundaire eigenschap, en bevat een scoreprofiel toe te voegen stimuleren criteria.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 812085a5a4b3e8d1233f19c947d2fd5e433f6ab7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121559"
---
# <a name="c-tutorial-order-the-results---azure-cognitive-search"></a>C# zelfstudie: De resultaten bestellen - Azure Cognitive Search

Tot op dit punt in onze reeks tutorials worden de resultaten geretourneerd en weergegeven in een standaardvolgorde. Dit kan de volgorde zijn waarin de gegevens zich bevinden, of mogelijk is een _standaardscoreprofiel_ gedefinieerd, dat wordt gebruikt wanneer er geen bestelparameters zijn opgegeven. In deze zelfstudie gaan we in op hoe we resultaten kunnen bestellen op basis van een primaire eigenschap en vervolgens naar resultaten die dezelfde primaire eigenschap hebben, hoe we die selectie op een secundaire eigenschap kunnen bestellen. Als alternatief voor bestellen op basis van numerieke waarden, laat het laatste voorbeeld zien hoe te bestellen op basis van een aangepast scoreprofiel. We zullen ook een beetje dieper in gaan op de weergave van _complexe types._

Om geretourneerde resultaten eenvoudig te vergelijken, bouwt dit project voort op het oneindige scrollproject dat is gemaakt in de [C# Tutorial: Search results pagetion - Azure Cognitive Search](tutorial-csharp-paging.md) tutorial.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Orderresultaten op basis van één eigenschap
> * Orderresultaten op basis van meerdere eigenschappen
> * Resultaten filteren op basis van een afstand tot een geografisch punt
> * Resultaten bestellen op basis van een scoreprofiel

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

Laat de oneindige schuifversie van de [C# Tutorial: Search results pagination - Azure Cognitive Search](tutorial-csharp-paging.md) project up and running gebruiken. Dit project kan uw eigen versie zijn of het installeren vanuit GitHub: [Eerste app maken.](https://github.com/Azure-Samples/azure-search-dotnet-samples)

## <a name="order-results-based-on-one-property"></a>Orderresultaten op basis van één eigenschap

Wanneer we resultaten bestellen op basis van één accommodatie, bijvoorbeeld hotelwaardering, willen we niet alleen de bestelde resultaten, we willen ook bevestiging dat de bestelling correct is. Met andere woorden, als we op beoordeling bestellen, moeten we de beoordeling in de weergave weergeven.

In deze tutorial, zullen we ook een beetje meer toe te voegen aan de weergave van de resultaten, de goedkoopste kamerprijs, en de duurste kamerprijs, voor elk hotel. Terwijl we ons verdiepen in het bestellen, voegen we ook waarden toe om ervoor te zorgen dat wat we bestellen ook in de weergave wordt weergegeven.

Het is niet nodig om een van de modellen te wijzigen om het bestellen mogelijk te maken. De weergave en de controller moeten worden bijgewerkt. Begin met het openen van de thuiscontroller.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>De eigenschap OrderBy toevoegen aan de zoekparameters

1. Het enige dat nodig is om resultaten te ordenen op basis van één numerieke eigenschap, is de parameter **OrderBy** instellen op de naam van de eigenschap. Voeg in de methode **Index(SearchData-model)** de volgende regel toe aan de zoekparameters.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > De standaardvolgorde is oplopend, maar u **asc** toevoegen aan de eigenschap om dit duidelijk te maken. Aflopende volgorde wordt opgegeven door **desc**toe te voegen .

2. Voer nu de app uit en voer een veelvoorkomende zoekterm in. De resultaten kunnen wel of niet in de juiste volgorde, als noch u als de ontwikkelaar, niet de gebruiker, heeft een gemakkelijke manier van het verifiëren van de resultaten!

3. Laten we duidelijk maken dat de resultaten worden besteld op rating. Ten eerste, vervang de **box1** en **box2** klassen in de hotels.css bestand met de volgende klassen (deze klassen zijn alle nieuwe die we nodig hebben voor deze tutorial).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    >[!Tip]
    >Browsers cache meestal css-bestanden, en dit kan leiden tot een oude css-bestand wordt gebruikt, en uw bewerkingen genegeerd. Een goede manier om dit te omzeilen is het toevoegen van een querytekenreeks met een versieparameter aan de koppeling. Bijvoorbeeld:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Werk het versienummer bij als u denkt dat een oud css-bestand wordt gebruikt door uw browser.

4. Voeg de eigenschap **Rating** toe aan de parameter **Selecteren** in de methode **Index(SearchData-model).**

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Open de weergave (index.cshtml) en vervang de renderingloop**&lt;(!-- De hotelgegevens&gt;weergeven.**

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. De classificatie moet beschikbaar zijn, zowel in de eerste weergegeven pagina, als in de volgende pagina's die worden aangeroepen via de oneindige rol. Voor de laatste van deze twee situaties moeten we zowel de **volgende** actie in de controller als de **gescrollde** functie in de weergave bijwerken. Als u begint met de controller, wijzigt u de methode **Volgende** in de volgende code. Deze code maakt en communiceert de classificatietekst.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. Werk nu de **gebladerte** functie in de weergave bij om de classificatietekst weer te geven.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 3) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                            }
                        });
                    }
                }
            </script>

    ```

8. Voer de app nu opnieuw uit. Zoek op een veelvoorkomende term, zoals 'wifi', en controleer of de resultaten worden geordend op basis van de aflopende volgorde van de hotelbeoordeling.

    ![Bestellen op basis van beoordeling](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    U zult merken dat verschillende hotels hebben een identieke rating, en dus hun verschijning in het display is weer de volgorde waarin de gegevens wordt gevonden, dat is willekeurig.

    Voordat we kijken naar het toevoegen van een tweede niveau van bestellen, laten we wat code toevoegen om het bereik van de kamerprijzen weer te geven. We voegen deze code toe aan beide tonen het extraheren van gegevens uit een _complex type,_ en ook zodat we de bestelresultaten kunnen bespreken op basis van prijs (goedkoopste eerste misschien).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Het bereik van de kamerprijzen toevoegen aan de weergave

1. Voeg eigenschappen met de goedkoopste en duurste kamerprijs toe aan het Hotel.cs model.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Bereken de kamersnelheden aan het einde van de actie **Index(SearchData-model)** in de thuiscontroller. Voeg de berekeningen toe na het opslaan van tijdelijke gegevens.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
    ```

3. Voeg de eigenschap **Kamers** toe aan de parameter **Selecteren** in de actiemethode **Index(SearchData-model)** van de controller.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Wijzig de renderinglus in de weergave om het frequentiebereik voor de eerste pagina met resultaten weer te geven.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. Wijzig de **volgende** methode in de thuiscontroller om het tariefbereik te communiceren voor volgende pagina's met resultaten.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. Werk de **gescrollde** functie in de weergave bij om de tekst van de kamertarieven te verwerken.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 4) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

7. Voer de app uit en controleer of de kamerprijsbereiken worden weergegeven.

    ![Ruimtebereik weergeven](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

De **orderby-eigenschap** van de zoekparameters accepteert geen vermelding zoals **Rooms.BaseRate** om de goedkoopste kamerprijs te bieden, zelfs als de kamers al op prijs waren gesorteerd. In dit geval zijn de kamers niet op prijs gesorteerd. Om hotels weer te geven in de steekproef gegevens set, besteld op kamerprijs, zou je de resultaten in uw home controller sorteren, en stuur deze resultaten naar de weergave in de gewenste volgorde.

## <a name="order-results-based-on-multiple-values"></a>Resultaten ordenen op basis van meerdere waarden

De vraag is nu hoe onderscheid te maken tussen hotels met dezelfde rating. Een goede manier zou te bestellen op basis van de laatste keer dat het hotel werd gerenoveerd. Met andere woorden, hoe recenter het hotel werd gerenoveerd, hoe hoger het hotel in de resultaten verschijnt.

1. Als u een tweede bestelniveau wilt toevoegen, wijzigt u de eigenschappen **OrderBy** en **Select** in de methode **Index(SearchData-model)** om de eigenschap **LastRenovationDate** op te nemen.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Een willekeurig aantal eigenschappen kan worden ingevoerd in de **lijst OrderBy.** Als hotels dezelfde waardering en renovatiedatum hadden, kon een derde woning worden ingevoerd om onderscheid te maken tussen hen.

2. Nogmaals, we moeten de renovatie datum in het uitzicht te zien, gewoon om zeker te zijn van de bestelling correct is. Voor zoiets als een renovatie, waarschijnlijk alleen het jaar is vereist. Wijzig de renderinglus in de weergave naar de volgende code.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. Wijzig de **volgende** methode in de thuiscontroller om de jaarcomponent van de laatste renovatiedatum door te sturen.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Wijzig de **gebladerde** functie in de weergave om de vernieuwingstekst weer te geven.

    ```javascript
            <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
                            var div = document.getElementById('myDiv');

                            // Append the returned data to the current list of hotels.
                            for (var i = 0; i < data.length; i += 5) {
                                div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                            }
                        });
                    }
                }
            </script>
    ```

5. Voer de app uit. Zoek op een veelvoorkomende term, zoals 'pool' of 'view', en controleer of hotels met dezelfde beoordeling nu worden weergegeven in aflopende volgorde van de renovatiedatum.

    ![Bestellen op renovatiedatum](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Resultaten filteren op basis van een afstand tot een geografisch punt

Waardering en renovatiedatum zijn voorbeelden van eigenschappen die het best in een aflopende volgorde worden weergegeven. Een alfabetische vermelding zou een voorbeeld zijn van een goed gebruik van de opgaande volgorde (bijvoorbeeld als er slechts één **OrderBy-eigenschap** was en deze was ingesteld op **HotelName,** wordt een alfabetische volgorde weergegeven). Voor onze steekproefgegevens zou de afstand tot een geografisch punt echter passender zijn.

Om resultaten weer te geven op basis van geografische afstand, zijn verschillende stappen vereist.

1. Filter alle hotels die zich buiten een opgegeven straal bevinden vanaf het opgegeven punt door een filter in te voeren met lengte-, breedte- en straalparameters. De longitude wordt eerst gegeven aan de functie PUNT. Radius is in kilometers.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Het bovenstaande filter bestelt de resultaten _niet_ op basis van afstand, het verwijdert alleen de uitschieters. Als u de resultaten wilt ordenen, voert u een instelling **OrderBy** in die de methode geoDistance opgeeft.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Hoewel de resultaten zijn geretourneerd door Azure Cognitive Search met behulp van een afstandsfilter, wordt de berekende afstand tussen de gegevens en het opgegeven punt _niet_ geretourneerd. Bereken deze waarde opnieuw in de weergave of controller als u deze in de resultaten wilt weergeven.

    De volgende code berekent de afstand tussen twee lat/lonpunten.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. Nu moet je deze concepten aan elkaar koppelen. Echter, deze code fragmenten zijn voor zover onze tutorial gaat, het bouwen van een kaart-gebaseerde app wordt overgelaten als een oefening voor de lezer. Als u dit voorbeeld verder wilt nemen, u overwegen een plaatsnaam met een straal in te voeren of een punt op een kaart te vinden en een straal te selecteren. Zie de volgende bronnen om deze opties verder te onderzoeken:

* [Azure Maps-documentatie](https://docs.microsoft.com/azure/azure-maps/)
* [Een adres zoeken met de zoekservice Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Resultaten bestellen op basis van een scoreprofiel

De voorbeelden in de tutorial tot nu toe laten zien hoe te bestellen op numerieke waarden (rating, renovatie datum, geografische afstand), het verstrekken van een _exacte_ proces van bestellen. Sommige zoekopdrachten en sommige gegevens lenen zich echter niet voor zo'n eenvoudige vergelijking tussen twee gegevenselementen. Azure Cognitive Search bevat het concept van _scoren._ _Scoreprofielen_ kunnen worden opgegeven voor een set gegevens die kunnen worden gebruikt om complexere en kwalitatieve vergelijkingen te bieden, die het meest waardevol moeten zijn wanneer bijvoorbeeld op tekst gebaseerde gegevens worden vergeleken om te beslissen welke eerst moet worden weergegeven.

Scoreprofielen worden niet gedefinieerd door gebruikers, maar meestal door beheerders van een gegevensset. Op de gegevens van de hotels zijn verschillende scoreprofielen opgesteld. Laten we eens kijken hoe een scoreprofiel wordt gedefinieerd en probeer vervolgens code te schrijven om erop te zoeken.

### <a name="how-scoring-profiles-are-defined"></a>Hoe scoreprofielen worden gedefinieerd

Laten we eens kijken naar drie voorbeelden van scoreprofielen en nagaan hoe elk van deze profielen de volgorde van de resultaten _moet_ beïnvloeden. Als app-ontwikkelaar schrijft u deze profielen niet, ze zijn geschreven door de gegevensbeheerder, maar het is handig om naar de syntaxis te kijken.

1. Dit is het standaard scoreprofiel voor de hotelgegevensset die wordt gebruikt wanneer u geen parameter **OrderBy** of **ScoringProfile** opgeeft. Dit profiel verhoogt de _score_ voor een hotel als de zoektekst aanwezig is in de naam van het hotel, de beschrijving of de lijst met tags (voorzieningen). Merk op hoe de gewichten van het scoren bepaalde velden begunstigen. Als de zoektekst wordt weergegeven in een ander veld, niet hieronder vermeld, heeft deze een gewicht van 1. Uiteraard, hoe hoger de score, hoe eerder een resultaat verschijnt in de weergave.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. Het volgende scoreprofiel verhoogt de score aanzienlijk, als een meegeleverde parameter een of meer van de lijst met tags bevat (die we "voorzieningen" noemen). Het belangrijkste punt van dit profiel is dat een parameter _moet_ worden geleverd, met tekst. Als de parameter leeg is of niet wordt geleverd, wordt er een fout gegenereerd.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. In dit derde voorbeeld geeft de rating een aanzienlijke boost aan de score. De laatste gerenoveerde datum zal ook de score verhogen, maar alleen als die gegevens binnen 730 dagen (2 jaar) van de huidige datum vallen.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    Nu, laten we eens kijken of deze profielen werken zoals we denken dat ze zouden moeten!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Code toevoegen aan de weergave om profielen te vergelijken

1. Open het bestand index.cshtml &lt;en&gt; vervang de hoofdsectie door de volgende code.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

2. Open het SearchData.cs-bestand en vervang de klasse **SearchData** door de volgende code.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

3. Open het bestand hotels.css en voeg de volgende HTML-klassen toe.

    ```html
    .facetlist {
        list-style: none;
    }
    
    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Code toevoegen aan de controller om een scoreprofiel op te geven

1. Open het thuiscontrollerbestand. Voeg het volgende **toe met een** instructie (om te helpen bij het maken van lijsten).

    ```cs
    using System.Linq;
    ```

2.  Voor dit voorbeeld hebben we de eerste oproep naar **Index** nodig om iets meer te doen dan alleen de oorspronkelijke weergave retourneren. De methode zoekt nu naar maximaal 20 voorzieningen om in de weergave weer te geven.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

3. We hebben twee privémethoden nodig om de facetten van tijdelijke opslag op te slaan, ze terug te halen uit tijdelijke opslag en een model te vullen.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

4. We moeten de parameters **OrderBy** en **ScoringProfile** zo nodig instellen. Vervang de bestaande **methode Index(SearchData-model)** door het volgende.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                InitSearch();

                int page;

                if (model.paging != null && model.paging == "next")
                {
                    // Recover the facet text, and the facet check box settings.
                    RecoverFacets(model, true);

                    // Increment the page.
                    page = (int)TempData["page"] + 1;

                    // Recover the search text.
                    model.searchText = TempData["searchfor"].ToString();
                }
                else
                {
                    // First search with text. 
                    // Recover the facet text, but ignore the check box settings, and use the current model settings.
                    RecoverFacets(model,false);

                    // First call. Check for valid text input, and valid scoring profile.
                    if (model.searchText == null)
                    {
                        model.searchText = "";
                    }
                    if (model.scoring == null)
                    {
                        model.scoring = "Default";
                    }
                    page = 0;
                }

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
                    SearchMode = SearchMode.All,

                    // Skip past results that have already been returned.
                    Skip = page * GlobalVariables.ResultsPerPage,

                    // Take only the next page worth of results.
                    Top = GlobalVariables.ResultsPerPage,

                    // Include the total number of results.
                    IncludeTotalResultCount = true,
                };

                // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
                model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
                        if (rate < cheapest || cheapest == 0)
                        {
                            cheapest = (double)rate;
                        }
                        if (rate > expensive)
                        {
                            expensive = (double)rate;
                        }
                    }
                    model.resultList.Results[n].Document.cheapest = cheapest;
                    model.resultList.Results[n].Document.expensive = expensive;
                }
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Lees de reacties voor elk van de **switchselecties** door.

5. We hoeven geen wijzigingen aan te brengen in de actie **Volgende** als u de extra code voor de vorige sectie hebt voltooid op basis van het bestellen op basis van meerdere eigenschappen.

### <a name="run-and-test-the-app"></a>De app uitvoeren en testen

1. Voer de app uit. U moet een volledige set van voorzieningen in het uitzicht zien.

2. Voor het bestellen, het selecteren van "Door numerieke Rating" geeft u de numerieke volgorde die u al hebt geïmplementeerd in deze tutorial, met renovatie datum beslissen onder hotels van gelijke rating.

![Bestellen "strand" op basis van waardering](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Probeer nu het profiel "Door voorzieningen". Maak verschillende selecties van voorzieningen, en controleren of hotels met deze voorzieningen worden bevorderd tot de resultaten lijst.

![Bestellen van "strand" op basis van profiel](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Probeer het "Door gerenoveerde datum / Rating profiel" om te zien of je krijgt wat je verwacht. Alleen onlangs gerenoveerde hotels moeten een _versheid_ boost krijgen.

### <a name="resources"></a>Resources

Zie voor meer informatie de volgende [scoreprofielen toevoegen aan een Azure Cognitive Search-index](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Opgedane kennis

Denk aan de volgende afhaalmaaltijden van dit project:

* Gebruikers verwachten dat zoekresultaten worden besteld, het meest relevant eerst.
* Gegevens moeten zo gestructureerd zijn dat bestellen eenvoudig is. We waren niet in staat om te sorteren op "goedkoopste" eerste gemakkelijk, omdat de gegevens niet is gestructureerd om het bestellen te kunnen doen zonder extra code.
* Er kunnen veel niveaus aan het bestellen, om onderscheid te maken tussen resultaten die dezelfde waarde hebben op een hoger niveau van bestellen.
* Het is natuurlijk voor sommige resultaten te worden besteld in oplopende volgorde (laten we zeggen, afstand van een punt), en sommige in aflopende volgorde (laten we zeggen, beoordeling van de gast).
* Scoreprofielen kunnen worden gedefinieerd wanneer numerieke vergelijkingen niet of niet slim genoeg zijn voor een gegevensset. Het scoren van elk resultaat zal helpen om de resultaten intelligent te ordenen en weer te geven.

## <a name="next-steps"></a>Volgende stappen

Je hebt deze serie C# tutorials voltooid - je had waardevolle kennis moeten hebben opgedaan van de Azure Cognitive Search API's.

Voor verdere informatie en zelfstudies u bladeren door [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure)of de andere zelfstudies in de Azure Cognitive [Search Documentation](https://docs.microsoft.com/azure/search/).
