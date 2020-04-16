---
title: Wat is de Face-service?
titleSuffix: Azure Cognitive Services
description: De Azure Cognitive Services Face-service biedt algoritmen die worden gebruikt om menselijke gezichten in afbeeldingen te detecteren, herkennen en analyseren.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 6714a0c4b967d80ad683ef023b5811423bdcb022
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403455"
---
# <a name="what-is-the-azure-face-service"></a>Wat is de Azure Face-service?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

De Azure Cognitive Services Face-service biedt algoritmen die menselijke gezichten in afbeeldingen detecteren, herkennen en analyseren. De mogelijkheid om menselijke gezichtsinformatie te verwerken is belangrijk in veel verschillende softwarescenario's. Voorbeelden scenario's zijn beveiliging, natuurlijke gebruikersinterface, beeldinhoud analyse en beheer, mobiele apps en robotica.

De Face-service biedt verschillende functies die elk in de volgende secties worden beschreven.

## <a name="face-detection"></a>Gezichtsdetectie

De Gezichtsservice detecteert menselijke gezichten in een afbeelding en retourneert de rechthoekcoördinaten van hun locaties. Optioneel kan gezichtsherkenning een reeks gezichtsgerelateerde kenmerken extraheren. Voorbeelden zijn hoofdpose, geslacht, leeftijd, emotie, gezichtshaar en een bril.

> [!NOTE]
> De gezichtsherkenningsfunctie is ook beschikbaar via de [Computer Vision API.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) Als u verdere bewerkingen met gezichtsgegevens wilt uitvoeren, gebruikt u de Face-service, de service die in dit artikel wordt besproken.

![Een beeld van een vrouw en een mens, met rechthoeken die rond hun gezichten en leeftijd en geslacht worden getrokken getoond](./Images/Face.detection.jpg)

Zie het artikel [Gezichtsherkenningsconcepten](concepts/face-detection.md) voor meer informatie over gezichtsherkenning. Zie ook de [referentiedocumentatie voor API detecteren.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

## <a name="face-verification"></a>Gezichtsverificatie

De VERIFY API doet een verificatie tegen twee gedetecteerde gezichten of van één gedetecteerd gezicht naar één persoonsobject. Waar het eigenlijk op neerkomt is dat de Verificatie-API evalueert of twee gezichten van dezelfde persoon zijn. Deze mogelijkheid is mogelijk nuttig in beveiligingsscenario's. Zie de handleiding [voor gezichtsherkenningsconcepten](concepts/face-recognition.md) of de [referentiedocumentatie voor API verifiëren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) voor meer informatie.

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

De Api Vergelijkbare zoeken vergelijkt een doelgezicht met een reeks kandidaat-gezichten om een kleinere set gezichten te vinden die lijken op het doelgezicht. Twee werkmodi, matchPerson en matchFace, worden ondersteund. De modus matchPerson retourneert vergelijkbare gezichten nadat deze voor dezelfde persoon is gefilters met behulp van de [API verifiëren.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) De matchFace-modus negeert het filter voor dezelfde persoon. Het retourneert een lijst met vergelijkbare kandidaat-gezichten die wel of niet van dezelfde persoon zijn.

In het volgende voorbeeld ziet u het doelvlak:

![Een glimlachende vrouw](./Images/FaceFindSimilar.QueryFace.jpg)

En deze beelden zijn de kandidaat gezichten:

![Vijf afbeeldingen van glimlachende mensen Afbeeldingen a en b tonen dezelfde persoon.](./Images/FaceFindSimilar.Candidates.jpg)

Om vier vergelijkbare gezichten te vinden, retourneert de matchPerson-modus a en b, die dezelfde persoon als het doelvlak weergeven. De matchFace-modus retourneert a,&mdash;b, c en d precies vier kandidaten, zelfs als sommige niet dezelfde persoon als het doel zijn of een lage gelijkenis hebben. Zie voor meer informatie de handleiding [voor concepten voor gezichtsherkenning](concepts/face-recognition.md) of de [referentiedocumentatie voor vergelijkbare API zoeken.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)

## <a name="face-grouping"></a>Gezichtsgroepering

De Groeperings-API verdeelt een set onbekende gezichten in groepen op basis van gelijkenis. Elke groep is een niet-aaneengesloten juiste subset van de oorspronkelijke set van gezichten. Alle gezichten in een groep zijn waarschijnlijk van dezelfde persoon. Er kunnen verschillende groepen voor een enkele persoon. De groepen worden onderscheiden door een andere factor, zoals expressie, bijvoorbeeld. Zie voor meer informatie de handleiding [voor concepten voor gezichtsherkenning](concepts/face-recognition.md) of de referentiedocumentatie [voor groeps-API's.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)

## <a name="person-identification"></a>Identificatie van personen

De Identify API wordt gebruikt om een gedetecteerd gezicht te identificeren tegen een database van mensen. Deze functie kan handig zijn voor het automatisch taggen van afbeeldingen in fotobeheersoftware. U maakt de database van tevoren en u deze na verloop van tijd bewerken.

In de volgende afbeelding ziet `"myfriends"`u een voorbeeld van een database met de naam . Elke groep kan maximaal 1 miljoen verschillende persoonsobjecten bevatten. Voor elk persoonsobject kunnen maximaal 248 gezichten zijn geregistreerd.

![Een raster met drie kolommen voor verschillende mensen, elk met drie rijen gezichtsafbeeldingen](./Images/person.group.clare.jpg)

Nadat u een database hebt gemaakt en getraind, u identificatie doen ten opzichte van de groep met een nieuw gedetecteerd gezicht. Als het gezicht wordt geïdentificeerd als een persoon in de groep, wordt het persoonsobject geretourneerd.

Zie de handleiding [voor gezichtsherkenningsconcepten](concepts/face-recognition.md) of de referentiedocumentatie [voor API identificeren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) voor meer informatie over persoonsidentificatie.

## <a name="containers"></a>Containers

[Gebruik de Face-container](face-how-to-install-containers.md) om gezichten te detecteren, herkennen en identificeren door een gestandaardiseerde Docker-container dichter bij uw gegevens te installeren.

## <a name="sample-apps"></a>Voorbeeld-apps

De volgende voorbeeldtoepassingen tonen een aantal manieren om de Face-service te gebruiken:

- [Face API: Windows Client Library and sample](https://github.com/Microsoft/Cognitive-Face-Windows) is een WPF-app die verschillende scenario's van gezichtsherkenning, analyse en identificatie demonstreert.
- [FamilyNotes UWP-app](https://github.com/Microsoft/Windows-appsample-familynotes) is een Universal Windows Platform (UWP)-app die gezichtsidentificatie gebruikt, samen met spraak, Cortana, inkt en camera in een scenario voor het delen van gezinsnotities.

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals met alle bronnen voor Cognitive Services moeten ontwikkelaars die de Face-service gebruiken, op de hoogte zijn van het beleid van Microsoft op het beleid van microsoft op klantgegevens. Zie de pagina [Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Volg een snelle start om een scenario voor gezichtsherkenning in code te implementeren:

- [Snelstart: gezichten in een afbeelding detecteren met behulp van de .NET SDK met C#](quickstarts/csharp.md). Andere talen zijn beschikbaar.
