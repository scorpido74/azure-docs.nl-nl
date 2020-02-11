---
title: C#zelf studie over het ordenen van resultaten
titleSuffix: Azure Cognitive Search
description: In deze zelf studie wordt gedemonstreerd hoe u zoek resultaten kunt ordenen. Het maakt deel uit van een vorig project van een hotels, wordt geordend op basis van de eigenschap Primary, de secundaire eigenschap en bevat een score profiel voor het toevoegen van Boosting criteria.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 812085a5a4b3e8d1233f19c947d2fd5e433f6ab7
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121559"
---
# <a name="c-tutorial-order-the-results---azure-cognitive-search"></a>C#zelf studie: de resultaten best Ellen-Azure Cognitive Search

Tot dat moment in onze reeks zelf studies worden resultaten geretourneerd en weer gegeven in een standaard volgorde. Dit kan de volg orde zijn waarin de gegevens zich bevinden, of er is mogelijk een standaard _Score profiel_ gedefinieerd, dat wordt gebruikt wanneer er geen para meters voor de volg orde worden opgegeven. In deze zelf studie gaan we naar de volg orde van de resultaten op basis van een primaire eigenschap en vervolgens voor resultaten die dezelfde primaire eigenschap hebben, de volg orde van de selectie op een secundaire eigenschap. Als alternatief voor het rangschikken op basis van numerieke waarden ziet u in het laatste voor beeld hoe u kunt sorteren op basis van een aangepast Score profiel. We gaan ook iets dieper in op de weer gave van _complexe typen_.

Als u de geretourneerde resultaten eenvoudig wilt vergelijken, wordt dit project gebaseerd op het oneindige Blader project dat in de [ C# zelf studie is gemaakt: Zoek resultaten pagineren-Azure Cognitive Search-](tutorial-csharp-paging.md) zelf studie.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Resultaten van de order op basis van één eigenschap
> * Resultaten van de order op basis van meerdere eigenschappen
> * Resultaten filteren op basis van een afstand van een geografisch punt
> * Resultaten sorteren op basis van een score profiel

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

Beschikken over de oneindige schuif versie van de [ C# zelf studie: Zoek resultaten pagineren-Azure Cognitive Search](tutorial-csharp-paging.md) project actief. Dit project kan uw eigen versie zijn of installeren vanaf GitHub: [Maak eerst een app](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Resultaten van de order op basis van één eigenschap

Als we de resultaten op basis van één eigenschap best Ellen, zegt u de classificatie van het Hotel, maar u wilt niet alleen de geordende resultaten controleren, maar ook om te bevestigen dat de order juist is. Met andere woorden, als we op classificatie indelen, moeten we de classificatie weer geven in de weer gave.

In deze zelf studie voegen we ook een beetje meer toe aan de weer gave van resultaten, het goedkoopste-kamer tempo en de duur van de meest dure kamer voor elk hotel. Zoals we in de praktijk hebben bestudeerd, zullen we ook waarden toevoegen om ervoor te zorgen dat de volg orde waarin we worden best Ellen ook wordt weer gegeven in de weer gave.

Het is niet nodig om een van de modellen te wijzigen om de volg orde in te scha kelen. De weer gave en de controller moeten zijn bijgewerkt. Begin met het openen van de start controller.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>De eigenschap OrderBy toevoegen aan de zoek parameters

1. Alles wat u nodig hebt om resultaten te best Ellen op basis van één numerieke eigenschap, is door de **OrderBy** para meter in te stellen op de naam van de eigenschap. Voeg in de **index methode (SearchData model)** de volgende regel toe aan de zoek parameters.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > De standaard volgorde is oplopend, maar u kunt wel **ASC** toevoegen aan de eigenschap om dit duidelijk te maken. Aflopende volg orde wordt opgegeven door **DESC**toe te voegen.

2. Voer nu de app uit en voer een algemene zoek term in. De resultaten zijn mogelijk niet in de juiste volg orde, omdat u niet als ontwikkelaar, niet de gebruiker, een eenvoudige manier hebt om de resultaten te controleren.

3. We gaan het wissen van de resultaten worden op waardering gesorteerd. Vervang eerst de klassen **box1** en **box2** in het bestand Hotels. CSS door de volgende klassen (deze klassen zijn allemaal de nieuwe typen die we nodig hebben voor deze zelf studie).

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
    >In browsers worden doorgaans CSS-bestanden in de cache opgeslagen. Dit kan leiden tot een oud CSS-bestand dat wordt gebruikt en uw bewerkingen worden genegeerd. Een goede manier om dit te ronden is het toevoegen van een query reeks met een versie parameter aan de koppeling. Bijvoorbeeld:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Werk het versie nummer bij als u denkt dat er een oud CSS-bestand wordt gebruikt door uw browser.

4. Voeg de eigenschap **rating** toe aan de para meter **Select** in de **index (SearchData model)** -methode.

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Open de weer gave (index. cshtml) en vervang de rendering-lus ( **&lt;!--de Hotel gegevens weer geven&gt;** ) met de volgende code.

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

6. De classificatie moet beschikbaar zijn op de eerste weer gegeven pagina en op de volgende pagina's die worden aangeroepen via de oneindige schuif balk. Voor de laatste van deze twee situaties moeten we zowel de **volgende** actie in de controller als de **gescrollde** functie in de weer gave bijwerken. Begin met de controller, wijzig de **volgende** methode in de volgende code. Met deze code wordt de tekst van de beoordeling gemaakt en gecommuniceerd.

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

7. Werk nu de **gescrollde** functie in de weer gave bij om de beoordelings tekst weer te geven.

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

8. Voer de app nu opnieuw uit. Zoek op een gemeen schappelijke term, zoals ' WiFi ', en controleer of de resultaten worden gesorteerd op de aflopende volg orde van de Hotel classificatie.

    ![Volg orde op basis van classificatie](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    U zult merken dat meerdere hotels een identieke classificatie hebben, zodat de weer gave in het scherm opnieuw de volg orde van de gegevens is, wat wille keurig is.

    Voordat we een tweede bestel niveau gaan toevoegen, voegen we code toe om het bereik van de kamer tarieven weer te geven. We voegen deze code toe om gegevens uit een _complex type_weer te geven. Daarnaast kunnen we de volg orde van de resultaten op basis van de prijs (goedkoopste First) bespreken.

### <a name="add-the-range-of-room-rates-to-the-view"></a>Het bereik van kamer tarieven toevoegen aan de weer gave

1. Voeg eigenschappen met de goedkoopste en de duurste kamer toe aan het Hotel.cs-model.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Bereken de kamer tarieven aan het einde van de actie van de **index (SearchData model)** in de start controller. Voeg de berekeningen toe na het opslaan van tijdelijke gegevens.

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

3. Voeg de eigenschap **rooms** toe aan de **Select** -para meter in de index-actie methode **(SearchData model)** van de controller.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Wijzig de rendering-lus in de weer gave om het frequentie bereik voor de eerste pagina met resultaten weer te geven.

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

5. Wijzig de **volgende** methode in de start controller om het frequentie bereik te communiceren voor de volgende pagina's met resultaten.

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

6. Werk de **gescrollde** functie in de weer gave bij om de tekst van de room-tarieven af te handelen.

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

7. Voer de app uit en controleer of de room-prijsbereiken worden weer gegeven.

    ![Room-frequentie bereiken weer geven](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

De eigenschap **OrderBy** van de zoek parameters accepteert geen vermelding zoals **kamers. BaseRate** om het goedkoopste-kamer tempo te bieden, zelfs als de kamers al zijn gesorteerd op rente. In dit geval worden de kamers niet gesorteerd op tarieven. Als u hotels wilt weer geven in de gegevensset voor de voor beeld, geordend op basis van een kamer, moet u de resultaten in de start controller sorteren en deze resultaten naar de weer gave in de gewenste volg orde verzenden.

## <a name="order-results-based-on-multiple-values"></a>Resultaten van de order op basis van meerdere waarden

De vraag is nu om onderscheid te maken tussen Hotels met dezelfde classificatie. Een goede manier om te best Ellen op basis van de laatste keer dat het hotel werd renovated. Met andere woorden, hoe meer recent het Hotel renovated was, hoe hoger het hotel wordt weer gegeven in de resultaten.

1. Als u een tweede niveau wilt best Ellen, wijzigt u de eigenschappen **OrderBy** en **selecteert** u in de **index methode (SearchData model)** de eigenschap **LastRenovationDate** .

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Een wille keurig aantal eigenschappen kan worden ingevoerd in de lijst **OrderBy** . Als Hotels dezelfde restrictie-en renovatie datum hadden, zou er een derde eigenschap kunnen worden ingevoerd om er onderscheid van te maken.

2. De datum van renovatie wordt weer gegeven in de weer gave, zodat u zeker weet dat de volg orde juist is. Voor een dergelijk renovatie is het waarschijnlijk dat alleen het jaar is vereist. Wijzig de rendering-lus in de weer gave in de volgende code.

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

3. Wijzig de **volgende** methode in de start controller om het jaar gedeelte van de laatste datum van renovatie door te sturen.

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

4. Wijzig de **gescrollde** functie in de weer gave om de tekst van renovatie weer te geven.

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

5. Voer de app uit. Zoek op een gemeen schappelijke term, zoals ' groep ' of ' weer gave ', en controleer of er hotels met dezelfde classificatie nu in aflopende volg orde van renovatie datum worden weer gegeven.

    ![Bestelling op renovatie datum](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Resultaten filteren op basis van een afstand van een geografisch punt

De classificatie en de datum van renovatie zijn voor beelden van eigenschappen die het beste in aflopende volg orde worden weer gegeven. Een alfabetische lijst zou een voor beeld zijn van een goed gebruik van een oplopende volg orde (bijvoorbeeld als er slechts één **OrderBy** -eigenschap was en deze is ingesteld op naam **Hotel** , dan wordt een alfabetische volg orde weer gegeven). Voor de voorbeeld gegevens is de afstand van een geografisch punt echter meer geschikt.

Om resultaten weer te geven op basis van de geografische afstand, zijn verschillende stappen vereist.

1. Alle hotels die zich buiten een opgegeven RADIUS van het opgegeven punt bevinden, filteren door een filter met de lengte graad, breedte graad en RADIUS-para meters in te voeren. De lengte graad moet eerst worden opgegeven voor de functie POINT. RADIUS is in kilo meters.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Met het bovenstaande filter worden de resultaten _niet_ geordend op basis van de afstand, maar de uitschieters worden verwijderd. Als u de resultaten wilt ordenen, voert u een **OrderBy** -instelling in die de geoafstands methode opgeeft.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Hoewel de resultaten door Azure Cognitive Search worden geretourneerd met behulp van een afstands filter, wordt de berekende afstand tussen de gegevens en het opgegeven punt _niet_ geretourneerd. De waarde in de weer gave of de controller opnieuw berekenen als u deze wilt weer geven in de resultaten.

    Met de volgende code wordt de afstand tussen twee Lat/Lon-punten berekend.

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

4. Nu moet u deze concepten aan elkaar koppelen. Deze code fragmenten zijn echter wel hetzelfde als onze zelf studie, het bouwen van een app op basis van een kaart is een oefening voor de lezer. Als u dit voor beeld verder wilt doen, kunt u een plaatsnaam invoeren met een RADIUS of een punt op een kaart zoeken en een RADIUS selecteren. Raadpleeg de volgende bronnen om deze opties verder te onderzoeken:

* [Azure Maps-documentatie](https://docs.microsoft.com/azure/azure-maps/)
* [Een adres zoeken met behulp van de Azure Maps Search-service](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Resultaten sorteren op basis van een score profiel

De voor beelden in de zelf studie laten zien hoe u de volg orde van de numerieke waarden (waardering, datum van renovatie, geografische afstand ) kunt best Ellen. Sommige Zoek opdrachten en sommige gegevens zijn echter niet geschikt voor een eenvoudige vergelijking tussen twee gegevens elementen. Azure Cognitive Search omvat het concept van _scores_. _Score profielen_ kunnen worden opgegeven voor een set gegevens die kan worden gebruikt om complexere en kwalitatieve vergelijkingen te bieden, die het meest waardevol moeten zijn wanneer u gegevens op basis van tekst vergelijkt om te bepalen welke u als eerste moet worden weer gegeven.

Score profielen worden niet door gebruikers gedefinieerd, maar doorgaans door beheerders van een gegevensset. Er zijn verschillende Score profielen ingesteld voor de hotels-gegevens. Laten we eens kijken hoe een score profiel is gedefinieerd en probeer vervolgens code te schrijven om erop te zoeken.

### <a name="how-scoring-profiles-are-defined"></a>Score profielen definiëren

Laten we eens kijken naar drie voor beelden van Score profielen _en bepalen hoe elk van_ invloed is op de volg orde van de resultaten. Als app-ontwikkelaar schrijft u deze profielen niet, ze zijn geschreven door de gegevens beheerder, maar het is wel handig om de syntaxis te bekijken.

1. Dit is het standaard Score profiel voor de gegevensverzameling hotels, gebruikt wanneer u geen **OrderBy** -of **ScoringProfile** -para meter opgeeft. Met dit profiel wordt de _Score_ voor een hotel verhoogd als de Zoek tekst aanwezig is in de naam, beschrijving of lijst met tags van het hotel (voorzieningen). U ziet hoe de gewichten van de Score van invloed zijn op bepaalde velden. Als de Zoek tekst in een ander veld wordt weer gegeven, is dit een gewicht van 1. Hoe hoger de score, hoe eerder een resultaat wordt weer gegeven in de weer gave.

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

2. Met het volgende Score profiel wordt de Score aanzienlijk verhoogd, als een opgegeven para meter een of meer van de Tags lijst bevat (waarmee we ' voorzieningen ' aanroepen). Het sleutel punt van dit profiel is dat er een para meter _moet_ worden opgegeven, die tekst bevat. Als de para meter leeg is of niet wordt opgegeven, wordt er een fout gegenereerd.
 
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

3. In dit derde voor beeld geeft de classificatie een aanzienlijke versterking van de score. De datum van de laatste renovated zal ook de Score verhogen, maar alleen als deze gegevens binnen 730 dagen (2 jaar) van de huidige datum vallen.

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

    Nu laten we zien of deze profielen werken zoals we denken.

### <a name="add-code-to-the-view-to-compare-profiles"></a>Code toevoegen aan de weer gave om profielen te vergelijken

1. Open het bestand index. cshtml en vervang de sectie &lt;Body&gt; door de volgende code.

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

3. Open het bestand Hotels. CSS en voeg de volgende HTML-klassen toe.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Code toevoegen aan de controller om een score profiel op te geven

1. Open het bestand Home controller. Voeg de volgende **using** -instructie toe (voor hulp bij het maken van lijsten).

    ```cs
    using System.Linq;
    ```

2.  Voor dit voor beeld hebben we de eerste aanroep van **index** nodig om iets meer te doen dan alleen de eerste weer gave retour neren. De methode zoekt nu tot 20 voorzieningen om weer te geven in de weer gave.

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

3. We hebben twee persoonlijke methoden nodig om de facetten op tijdelijke opslag op te slaan en om ze te herstellen vanaf tijdelijke opslag en een model te vullen.

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

4. We moeten de para meters **OrderBy** en **ScoringProfile** indien nodig instellen. Vervang de bestaande **index methode (SearchData model)** door het volgende.

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

    Lees de opmerkingen voor elk van de geselecteerde **switches** .

5. U hoeft geen wijzigingen aan te brengen in de **volgende** actie als u de aanvullende code voor de vorige sectie hebt ingevuld op basis van meerdere eigenschappen.

### <a name="run-and-test-the-app"></a>De app uitvoeren en testen

1. Voer de app uit. U ziet een volledige set voorzieningen in de weer gave.

2. Bij het selecteren van ' op numerieke waardering ' krijgt u de numerieke volg orde die u in deze zelf studie al hebt geïmplementeerd, waarbij de renovaties datum tussen Hotels met een gelijke classificatie.

![' Strand ' Best Ellen op basis van classificatie](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Probeer nu het profiel ' by voorzieningen '. Maak verschillende selecties van voorzieningen en controleer of Hotels met deze voorzieningen zijn bevorderd door de lijst met resultaten.

![' Strand ' Best Ellen op basis van het profiel](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Probeer het profiel ' op renovated datum/beoordeling ' om te zien of u de verwachte waarde krijgt. Alleen onlangs gerenovatedeerde hotels moeten een _nieuwe_ verbetering hebben.

### <a name="resources"></a>Bronnen

Zie voor meer informatie de volgende [Score profielen toevoegen aan een Azure Cognitive search-index](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Opgedane kennis

Houd rekening met de volgende Takeaways van dit project:

* Gebruikers verwachten dat Zoek resultaten worden besteld, die het meest relevant zijn voor het eerst.
* Gegevens behoeften moeten zodanig zijn gestructureerd dat het eenvoudig kan worden geordend. Het is niet mogelijk om eerst eenvoudig op ' goedkoopste ' te sorteren, omdat de gegevens niet zijn gestructureerd, zodat het ordenen kan worden uitgevoerd zonder extra code.
* Er kunnen veel niveaus zijn om te best Ellen, om onderscheid te maken tussen de resultaten met dezelfde waarde op een hoger niveau.
* Het is natuurlijk dat sommige resultaten in oplopende volg orde (bijvoorbeeld op afstand van een punt) en in aflopende volg orde (bijvoorbeeld de classificatie van de gast) worden besteld.
* Score profielen kunnen worden gedefinieerd wanneer numerieke vergelijkingen niet beschikbaar zijn of niet slim genoeg zijn voor een gegevensset. Met elk resultaat kunt u de resultaten op een slimme manier ordenen en weer geven.

## <a name="next-steps"></a>Volgende stappen

U hebt deze reeks C# zelf studies voltooid. u moet waardevolle kennis van de Azure Cognitive Search-api's hebben opgedaan.

Voor verdere Naslag informatie en zelf studies kunt u overwegen [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure)te bladeren of de andere zelf studies in de [documentatie van Azure Cognitive Search](https://docs.microsoft.com/azure/search/).
