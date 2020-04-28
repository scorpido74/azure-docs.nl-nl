---
title: Een zoek index in Azure Portal maken
titleSuffix: Azure Cognitive Search
description: Meer informatie over het maken van een index voor Azure Cognitive Search met behulp van een ingebouwde portal index Designer.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74112841"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Een Azure Cognitive Search-index maken in de portal

Azure Cognitive Search bevat een ingebouwde index ontwerper in de portal die nuttig is voor prototypen of het maken van een [zoek index](search-what-is-an-index.md) die wordt gehost op uw Azure Cognitive Search-service. Het hulp programma wordt gebruikt voor het bouwen van een schema. Wanneer u de definitie opslaat, wordt een lege index volledig weer gegeven in azure Cognitive Search. Hoe u het laadt met Doorzoek bare inhoud, is voor u.

De index ontwerper is slechts één benadering voor het maken van een index. U kunt ook een index maken en laden met behulp van de [wizard gegevens importeren](search-get-started-portal.md). De wizard werkt alleen met indexen die deze zelf maakt. Via een programma kunt u een index maken met behulp van de [.net](search-create-index-dotnet.md) -of [rest](search-create-index-rest-api.md) -api's.

## <a name="start-index-designer"></a>Index Designer starten

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en open het servicedashboard. Klik in de koppelingsbalk op **Alle services** als u in het huidige abonnement naar bestaande 'zoekservices' wilt zoeken. 

2. Klik op de koppeling **index toevoegen** in de opdracht balk boven aan de pagina.

   ![Index koppeling toevoegen in de opdracht balk](media/search-create-index-portal/add-index.png "Index koppeling toevoegen in de opdracht balk")

3. Geef een naam op voor uw Azure Cognitive Search-index. Er wordt naar index namen verwezen in indexerings-en query bewerkingen. De naam van de index wordt onderdeel van de eind punt-URL die wordt gebruikt voor verbindingen met de index en voor het verzenden van HTTP-aanvragen in de Azure Cognitive Search REST API.

   * Begin met een letter.
   * Gebruik alleen kleine letters, cijfers of streepjes (-).
   * Zorg ervoor dat de naam maximaal 60 tekens bevat.

## <a name="add-fields"></a>Velden toevoegen

De index bevat onder andere een *Veldenverzameling* die de doorzoekbare gegevens in uw index definieert. Samen bevat de verzameling velden de structuur van documenten die u afzonderlijk uploadt. Een verzameling velden bevat vereiste en optionele velden, met de naam en getypt, met index kenmerken die bepalen hoe het veld kan worden gebruikt.

1. Voeg velden toe om de documenten die u uploadt volledig op te geven en een [gegevens type](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) voor elke document in te stellen. Als bijvoorbeeld documenten bestaan uit een *Hotel-id*, *Hotel naam*, *adres*, *plaats*en *regio*, maakt u een overeenkomend veld voor elke groep in de index. Raadpleeg de [ontwerp richtlijnen in de sectie hieronder](#design) voor hulp bij het instellen van kenmerken.

1. Als inkomende gegevens hiërarchisch zijn, moet uw schema [complexe typen](search-howto-complex-data-types.md) bevatten die de geneste structuren vertegenwoordigen. De ingebouwde voor beeld-gegevensset, hotels, illustreert complexe typen met behulp van een adres (bevat meerdere subvelden) die een een-op-een-relatie hebben met elk Hotel, en een verzameling van ruimten die complexe verzamelingen bevatten, waarbij meerdere kamers aan elk hotel zijn gekoppeld. 

1. Geef een *sleutel* veld op van het type EDM. String. Een sleutel veld is verplicht voor elke Azure Cognitive Search-index en moet een teken reeks zijn. De waarden voor dit veld moeten elk document uniek identificeren. De standaardnaam van het veld is *id*, maar u kunt de naam wijzigen, zolang de tekenreeks maar voldoet aan de [naamgevingsregels](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Als uw collectie Fields bijvoorbeeld *Hotel-id*bevat, kiest u dat voor uw sleutel. 

1. Stel kenmerken in voor elk veld. De index Designer sluit alle kenmerken uit die ongeldig zijn voor het gegevens type, maar worden niet voorgesteld wat moet worden opgenomen. Lees de instructies in de volgende sectie voor meer informatie over de kenmerken van.

    De documentatie voor Azure Cognitive Search API bevat code voorbeelden met een eenvoudige index voor *Hotels* . In de onderstaande scherm afbeelding ziet u de index definitie, inclusief de Franse taal analyse die tijdens de index definitie is opgegeven, die u als een Oefen oefening in de portal kunt maken.

    ![Demo-index van hotels](media/search-create-index-portal/field-definitions.png "Demo-index van hotels")

1. Wanneer u klaar bent, klikt u op **maken** om de index op te slaan en te maken.

<a name="design"></a>

## <a name="set-attributes"></a>Kenmerken instellen

Hoewel u op elk gewenst moment nieuwe velden kunt toevoegen, worden bestaande velddefinities voor de hele levensduur van de index vergrendeld. Daarom gebruiken ontwikkelaars de portal doorgaans om eenvoudige indexen te maken, om ideeën uit te testen of om een instelling op te zoeken met behulp van de portalpagina's. Een frequente iteratie van een index-ontwerp is efficiënter als u een op code gebaseerde benadering hanteert, zodat u uw index eenvoudig kunt herbouwen.

Voordat de index wordt opgeslagen, worden er analyses en suggesties aan velden gekoppeld. Zorg ervoor dat u taal analysen of suggesties toevoegt aan de index definitie terwijl u deze maakt.

Tekenreeksvelden zijn vaak gemarkeerd als **Doorzoekbaar** en **Ophalen mogelijk**. Velden die worden gebruikt om zoekopdrachten te verfijnen, zijn onder andere **Sorteerbaar**, **Filteren mogelijk** en **Facetten mogelijk**.

Veldkenmerken bepalen hoe een veld wordt gebruikt, bijvoorbeeld of het wordt gebruikt voor zoeken in volledige tekst, facetnavigatie, sorteerbewerkingen, enzovoort. In de volgende tabel wordt elk kenmerk beschreven.

|Kenmerk|Beschrijving|  
|---------------|-----------------|  
|**doorzoekbaar**|Zoeken in volledige tekst mogelijk, onderworpen aan lexicale analyse, zoals het afbreken van woorden tijdens het indexeren. Als u een doorzoekbaar veld instelt op een waarde als 'zonnige dag', wordt de waarde intern gesplitst in de afzonderlijke tokens 'zonnige' en 'dag'. Zie [Hoe zoeken in de volledige tekst werkt](search-lucene-query-architecture.md) voor meer informatie.|  
|**filterbaar**|Hier wordt naar verwezen in **$filter**-query's. Bij filterbare velden van het type `Edm.String` of `Collection(Edm.String)` worden woorden niet afgebroken, dus vergelijkingen gelden alleen voor exacte overeenkomsten. Als u zo'n veld bijvoorbeeld instelt op 'zonnige dag', worden er met `$filter=f eq 'sunny'` geen overeenkomsten gevonden, maar met `$filter=f eq 'sunny day'` wel. |  
|**sorteerbaar**|Het systeem sorteert de resultaten standaard op score, maar u kunt het sorteren configureren op basis van velden in de documenten. Velden van het type `Collection(Edm.String)` kunnen niet **sorteerbaar** zijn. |  
|**facetten**|Wordt doorgaans gebruikt bij een weergave van zoekresultaten met een treffertelling per categorie (bijvoorbeeld hotels in een specifieke stad). Deze optie kan niet worden gebruikt bij velden van het type `Edm.GeographyPoint`. Velden van het type `Edm.String` die **filterbaar** of **sorteerbaar** zijn, of waarvoor **Facetten mogelijk** zijn, kunnen een lengte hebben van maximaal 32 kB. Zie [Index maken (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) voor meer informatie.|  
|**prestatie**|Unieke id voor documenten binnen de index. Er moet precies één veld worden uitgekozen als sleutelveld. Dit veld moet van het type `Edm.String` zijn.|  
|**ophalen mogelijk**|Hiermee bepaalt u of het veld in een zoekresultaat kan worden geretourneerd. Dit is handig als u een veld (zoals *winstmarge*) wilt gebruiken als filter-, sorteer- of scoremechanisme, maar niet wilt dat het veld zichtbaar is voor de eindgebruiker. Dit kenmerk moet `true` zijn voor `key`-velden.|  

## <a name="next-steps"></a>Volgende stappen

Nadat u een Azure Cognitive Search-index hebt gemaakt, kunt u door gaan naar de volgende stap: [Doorzoek bare gegevens uploaden naar de index](search-what-is-data-import.md).

U kunt ook een [diep gaande blik op indexen](search-what-is-an-index.md)maken. Naast de Veldenverzameling bevat een index ook analyses, suggesties, scoreprofielen en CORS-instellingen. De portal biedt tabbladen voor het definiëren van de meest voorkomende elementen: velden, analyses en suggesties. Als u andere elementen wilt maken of wijzigen, kunt u de REST API of de .NET SDK gebruiken.

## <a name="see-also"></a>Zie ook

 [Hoe zoeken in de volledige tekst werkt](search-lucene-query-architecture.md)  
 [REST API voor Search-service](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

