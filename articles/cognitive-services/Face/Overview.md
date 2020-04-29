---
title: Wat is de Face-service?
titleSuffix: Azure Cognitive Services
description: De Azure Cognitive Services Face-service biedt algoritmen die worden gebruikt voor het detecteren, herkennen en analyseren van menselijke gezichten in installatie kopieën.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 6714a0c4b967d80ad683ef023b5811423bdcb022
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81403455"
---
# <a name="what-is-the-azure-face-service"></a>Wat is Azure face service?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

De Azure Cognitive Services Face-service biedt algoritmen die menselijke gezichten in afbeeldingen detecteren, herkennen en analyseren. De mogelijkheid om informatie over Human gezicht te verwerken is belang rijk in veel verschillende software scenario's. Voorbeeld scenario's zijn onder meer beveiliging, natuurlijke gebruikers interface, analyse van afbeeldings inhoud en beheer, mobiele apps en Robotics.

De face-service biedt verschillende functies die elk in de volgende secties worden beschreven.

## <a name="face-detection"></a>Gezichtsdetectie

De face-service detecteert menselijke gezichten in een afbeelding en retourneert de rechthoek coördinaten van hun locaties. Een voor beeld van gezichts detectie kan een reeks kenmerken met betrekking tot het gezicht extra heren. Voor beelden zijn Head pose, gender, Age, Emotion, gezichts haar en glazen.

> [!NOTE]
> De functie voor gezichts detectie is ook beschikbaar via de [Computer Vision-API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Als u meer bewerkingen wilt uitvoeren met gezichts gegevens, gebruikt u de face-service, de service die in dit artikel wordt besproken.

![Een afbeelding van een vrouw en een man met rechthoeken die zijn getekend rond hun gezichten en de weer gegeven leeftijd en geslacht](./Images/Face.detection.jpg)

Zie het artikel [gezichts detectie](concepts/face-detection.md) concepten voor meer informatie over gezichts detectie. Zie ook de referentie documentatie voor [API detecteren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

## <a name="face-verification"></a>Gezichtsverificatie

De verify-API voert een verificatie uit op twee gedetecteerde gezichten of van een gedetecteerd gezicht naar één persoons object. Waar het eigenlijk op neerkomt is dat de Verificatie-API evalueert of twee gezichten van dezelfde persoon zijn. Deze mogelijkheid is mogelijk nuttig in beveiligings scenario's. Voor meer informatie raadpleegt u de hand leiding voor [gezichts herkenning](concepts/face-recognition.md) of de naslag documentatie voor [API-verificatie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) .

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

Met de zoek vergelijk bare API vergelijkt u een doel vlak met een verzameling kandidaten voor een kleiner aantal gezichten dat lijkt op het doel gezicht. Twee werk modi, matchPerson en matchFace, worden ondersteund. De modus matchPerson retourneert vergelijk bare gezichten nadat deze is gefilterd op dezelfde persoon met behulp van de [verificatie-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). In de modus matchFace wordt het filter van dezelfde persoon genegeerd. Er wordt een lijst met vergelijk bare kandidaten geretourneerd die tot dezelfde persoon behoren.

In het volgende voor beeld ziet u het doel gezicht:

![Een glimlachende vrouw](./Images/FaceFindSimilar.QueryFace.jpg)

En deze installatie kopieën zijn de gezichten van de kandidaat:

![Vijf afbeeldingen van glimlachende mensen Afbeeldingen a en b tonen dezelfde persoon.](./Images/FaceFindSimilar.Candidates.jpg)

Als u vier vergelijk bare gezichten zoekt, retourneert de matchPerson-modus a en b, die dezelfde persoon als het doel gezicht weergeeft. De matchFace-modus retourneert a, b, c en d&mdash;exact vier kandidaten, zelfs als sommige niet dezelfde persoon als het doel zijn of weinig gelijkenis hebben. Voor meer informatie raadpleegt u de hand leiding voor [gezichts herkenning](concepts/face-recognition.md) of de documentatie over het [zoeken naar vergelijk bare API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) .

## <a name="face-grouping"></a>Gezichtsgroepering

De Groeperings-API verdeelt een set onbekende gezichten in groepen op basis van gelijkenis. Elke groep is een niet-aaneengesloten juiste subset van de oorspronkelijke set van gezichten. Alle gezichten in een groep zijn waarschijnlijk bij dezelfde persoon. Er kunnen verschillende groepen voor één persoon zijn. De groepen worden onderscheiden door een andere factor, zoals Expression, bijvoorbeeld. Zie voor meer informatie de hand leiding voor [gezichts herkenning](concepts/face-recognition.md) of de naslag documentatie over [groeps-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) .

## <a name="person-identification"></a>Identificatie van personen

De Identify-API wordt gebruikt om een gedetecteerd gezicht te identificeren voor een Data Base van personen. Deze functie kan handig zijn voor het automatisch Toep assen van afbeeldingen in foto beheer software. U maakt de data base vooraf en u kunt deze na verloop van tijd bewerken.

In de volgende afbeelding ziet u een voor beeld van `"myfriends"`een Data Base met de naam. Elke groep kan Maxi maal 1.000.000 verschillende personen-objecten bevatten. Voor elk persoonsobject kunnen maximaal 248 gezichten zijn geregistreerd.

![Een raster met drie kolommen voor verschillende personen, elk met drie rijen met gezichts afbeeldingen](./Images/person.group.clare.jpg)

Nadat u een Data Base hebt gemaakt en getraind, kunt u met een nieuw gedetecteerd gezicht identificatie uitvoeren voor de groep. Als het gezicht wordt geïdentificeerd als een persoon in de groep, wordt het persoonsobject geretourneerd.

Voor meer informatie over persoons-id raadpleegt u de hand leiding voor [gezichts herkenning](concepts/face-recognition.md) of de naslag informatie over [API identificeren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) .

## <a name="containers"></a>Containers

[Gebruik de face-container](face-how-to-install-containers.md) om gezichten te detecteren, herkennen en identificeren door een gestandaardiseerde docker-container dichter bij uw gegevens te installeren.

## <a name="sample-apps"></a>Voorbeeld-apps

De volgende voorbeeld toepassingen bevatten een aantal manieren om de face-service te gebruiken:

- [Face-API: Windows-client bibliotheek en](https://github.com/Microsoft/Cognitive-Face-Windows) -voor beeld is een WPF-app die verschillende Scenario's van gezichts detectie, analyse en identificatie toont.
- [FAMILYNOTES UWP-app](https://github.com/Microsoft/Windows-appsample-familynotes) is een universeel Windows-platform-app (UWP) die gebruikmaakt van gezichts identificatie samen met spraak, Cortana, inkt en camera in een scenario voor het delen van familie notities.

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Net als bij alle Cognitive Services resources moeten ontwikkel aars die gebruikmaken van de face-service op de hoogte zijn van het beleid van micro soft op klant gegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) in het micro soft vertrouwens centrum voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Volg een Snelstartgids om een scenario voor een gezichts detectie in code te implementeren:

- [Quick Start: gezichten detecteren in een installatie kopie met behulp van de .NET SDK met C#](quickstarts/csharp.md). Er zijn andere talen beschikbaar.
