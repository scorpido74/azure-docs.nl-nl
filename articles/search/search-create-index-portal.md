---
title: Een zoekindex maken in Azure-portal
titleSuffix: Azure Cognitive Search
description: Meer informatie over het maken van een index voor Azure Cognitive Search met behulp van een ingebouwde portalindexontwerper.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112841"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Een Azure Cognitive Search-index maken in de portal

Azure Cognitive Search bevat een ingebouwde indexontwerper in de portal die nuttig is voor prototypes of een [zoekindex](search-what-is-an-index.md) maakt die wordt gehost op uw Azure Cognitive Search-service. De tool wordt gebruikt voor schemaconstructie. Wanneer u de definitie opslaat, wordt een lege index volledig uitgedrukt in Azure Cognitive Search. Hoe je het laadt met doorzoekbare inhoud is aan jou.

De indexontwerper is slechts één benadering voor het maken van een index. U ook een index maken en laden met de [wizard Gegevens importeren](search-get-started-portal.md). De wizard werkt alleen met indexen die hij zelf maakt. Programmatisch u een index maken met behulp van de [API's .NET](search-create-index-dotnet.md) of [REST.](search-create-index-rest-api.md)

## <a name="start-index-designer"></a>Indexontwerper starten

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en open het servicedashboard. Klik in de koppelingsbalk op **Alle services** als u in het huidige abonnement naar bestaande 'zoekservices' wilt zoeken. 

2. Klik **op** de koppeling Index toevoegen op de opdrachtbalk boven aan de pagina.

   ![Indexkoppeling toevoegen aan de opdrachtbalk](media/search-create-index-portal/add-index.png "Indexkoppeling toevoegen aan de opdrachtbalk")

3. Geef uw Azure Cognitive Search-index een naam. Indexnamen worden verwezen in indexering en querybewerkingen. De indexnaam wordt onderdeel van de URL van het eindpunt die wordt gebruikt voor verbindingen met de index en voor het verzenden van HTTP-aanvragen in de Azure Cognitive Search REST API.

   * Begin met een letter.
   * Gebruik alleen kleine letters, cijfers of streepjes (-).
   * Zorg ervoor dat de naam maximaal 60 tekens bevat.

## <a name="add-fields"></a>Velden toevoegen

De index bevat onder andere een *Veldenverzameling* die de doorzoekbare gegevens in uw index definieert. In totaal geeft de veldenverzameling de structuur op van documenten die u afzonderlijk uploadt. Een verzameling Velden bevat de gewenste en optionele velden, benoemd en getypt, met indexkenmerken die bepalen hoe het veld kan worden gebruikt.

1. Voeg velden toe om de documenten die u uploadt volledig op te geven en stel een gegevenstype in voor elk [document.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Als documenten bijvoorbeeld bestaan uit een *hotel-id,* *hotelnaam,* *adres,* *stad*en *regio,* maakt u een overeenkomstig veld voor elk veld in de index. Bekijk de [ontwerprichtlijnen in het onderstaande gedeelte](#design) voor hulp bij het instellen van kenmerken.

1. Als binnenkomende gegevens hiërarchisch van aard zijn, moet uw schema [complexe typen](search-howto-complex-data-types.md) bevatten om de geneste structuren weer te geven. De ingebouwde voorbeeldgegevensset Hotels illustreert complexe typen met behulp van een adres (bevat meerdere subvelden) dat een één-op-één relatie heeft met elk hotel en een complexverzameling voor kamers, waarbij meerdere kamers aan elk hotel zijn gekoppeld. 

1. Geef een *toetsveld* op van het type Edm.String. Een sleutelveld is verplicht voor elke Azure Cognitive Search-index en moet een tekenreeks zijn. Waarden voor dit veld moeten elk document op unieke wijze identificeren. De standaardnaam van het veld is *id*, maar u kunt de naam wijzigen, zolang de tekenreeks maar voldoet aan de [naamgevingsregels](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Als uw veldenverzameling bijvoorbeeld *hotel-id*bevat, kiest u dat voor uw sleutel. 

1. Stel kenmerken in op elk veld. De indexontwerper sluit alle kenmerken uit die ongeldig zijn voor het gegevenstype, maar geeft niet aan wat u moet opnemen. Bekijk de richtlijnen in het volgende gedeelte om te begrijpen waar de kenmerken voor zijn.

    Azure Cognitive Search API-documentatie bevat codevoorbeelden met een eenvoudige *hotelindex.* In de onderstaande schermafbeelding ziet u de indexdefinitie, inclusief de Franse taalanalyzer die is opgegeven tijdens de indexdefinitie, die u opnieuw maken als een oefening in de portal.

    ![Hotels demo-index](media/search-create-index-portal/field-definitions.png "Hotels demo-index")

1. Klik als u klaar bent **met maken** om de index op te slaan en te maken.

<a name="design"></a>

## <a name="set-attributes"></a>Kenmerken instellen

Hoewel u op elk gewenst moment nieuwe velden kunt toevoegen, worden bestaande velddefinities voor de hele levensduur van de index vergrendeld. Daarom gebruiken ontwikkelaars de portal doorgaans om eenvoudige indexen te maken, om ideeën uit te testen of om een instelling op te zoeken met behulp van de portalpagina's. Een frequente iteratie van een index-ontwerp is efficiënter als u een op code gebaseerde benadering hanteert, zodat u uw index eenvoudig kunt herbouwen.

Voordat de index wordt opgeslagen, worden er analyses en suggesties aan velden gekoppeld. Zorg ervoor dat u taalanalysers of suggesties toevoegt aan uw indexdefinitie terwijl u deze maakt.

Tekenreeksvelden zijn vaak gemarkeerd als **Doorzoekbaar** en **Ophalen mogelijk**. Velden die worden gebruikt om zoekopdrachten te verfijnen, zijn onder andere **Sorteerbaar**, **Filteren mogelijk** en **Facetten mogelijk**.

Veldkenmerken bepalen hoe een veld wordt gebruikt, bijvoorbeeld of het wordt gebruikt voor zoeken in volledige tekst, facetnavigatie, sorteerbewerkingen, enzovoort. In de volgende tabel wordt elk kenmerk beschreven.

|Kenmerk|Beschrijving|  
|---------------|-----------------|  
|**Doorzoekbare**|Zoeken in volledige tekst mogelijk, onderworpen aan lexicale analyse, zoals het afbreken van woorden tijdens het indexeren. Als u een doorzoekbaar veld instelt op een waarde als 'zonnige dag', wordt de waarde intern gesplitst in de afzonderlijke tokens 'zonnige' en 'dag'. Zie [Hoe zoeken in de volledige tekst werkt](search-lucene-query-architecture.md) voor meer informatie.|  
|**filterbaar**|Hier wordt naar verwezen in **$filter**-query's. Bij filterbare velden van het type `Edm.String` of `Collection(Edm.String)` worden woorden niet afgebroken, dus vergelijkingen gelden alleen voor exacte overeenkomsten. Als u zo'n veld bijvoorbeeld instelt op 'zonnige dag', worden er met `$filter=f eq 'sunny'` geen overeenkomsten gevonden, maar met `$filter=f eq 'sunny day'` wel. |  
|**Sorteerbare**|Het systeem sorteert de resultaten standaard op score, maar u kunt het sorteren configureren op basis van velden in de documenten. Velden van het type `Collection(Edm.String)` kunnen niet **sorteerbaar** zijn. |  
|**facetable**|Wordt doorgaans gebruikt bij een weergave van zoekresultaten met een treffertelling per categorie (bijvoorbeeld hotels in een specifieke stad). Deze optie kan niet worden gebruikt bij velden van het type `Edm.GeographyPoint`. Velden van het type `Edm.String` die **filterbaar** of **sorteerbaar** zijn, of waarvoor **Facetten mogelijk** zijn, kunnen een lengte hebben van maximaal 32 kB. Zie [Index maken (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) voor meer informatie.|  
|**Sleutel**|Unieke id voor documenten binnen de index. Er moet precies één veld worden uitgekozen als sleutelveld. Dit veld moet van het type `Edm.String` zijn.|  
|**opvraagbaar**|Hiermee bepaalt u of het veld in een zoekresultaat kan worden geretourneerd. Dit is handig als u een veld (zoals *winstmarge*) wilt gebruiken als filter-, sorteer- of scoremechanisme, maar niet wilt dat het veld zichtbaar is voor de eindgebruiker. Dit kenmerk moet `true` zijn voor `key`-velden.|  

## <a name="next-steps"></a>Volgende stappen

Nadat u een Azure Cognitive Search-index hebt aangegaan, u naar de volgende stap gaan: [doorzoekbare gegevens uploaden naar de index.](search-what-is-data-import.md)

U ook dieper [ingaan op indexen.](search-what-is-an-index.md) Naast de Veldenverzameling bevat een index ook analyses, suggesties, scoreprofielen en CORS-instellingen. De portal biedt tabbladen voor het definiëren van de meest voorkomende elementen: velden, analyses en suggesties. Als u andere elementen wilt maken of wijzigen, kunt u de REST API of de .NET SDK gebruiken.

## <a name="see-also"></a>Zie ook

 [Hoe zoeken in de volledige tekst werkt](search-lucene-query-architecture.md)  
 [REST API voor Search-service](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

