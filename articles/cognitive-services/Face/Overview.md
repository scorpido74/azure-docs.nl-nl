---
title: Wat is de Face-service?
titleSuffix: Azure Cognitive Services
description: De Azure Cognitive Services Face-service biedt algoritmen die worden gebruikt voor het detecteren, herkennen en analyseren van menselijke gezichten in afbeeldingen.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 9/01/2020
ms.author: pafarley
ms.openlocfilehash: 1dc970a16c3b031b311c5b98ca43613d9ecf39d4
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421568"
---
# <a name="what-is-the-azure-face-service"></a>Wat is de Azure Face-service?

> [!WARNING]
> Op 11 juni 2020 kondigde Microsoft aan dat het geen gezichtsherkenningssoftware verkoopt aan politieafdelingen in de Verenigde Staten totdat er solide wetgeving op basis van mensenrechten in werking is getreden. Daardoor is het mogelijk dan klanten gezichtsherkenningsfuncties of een functionaliteit in Azure Services, zoals Face of Video Indexer, niet kunnen gebruiken als de klant deze services gebruikt of laat gebruiken voor of door een politieafdeling in de Verenigde Staten.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

De Azure Cognitive Services Face-service biedt algoritmen voor het detecteren, herkennen en analyseren van menselijke gezichten in afbeeldingen. De mogelijkheid om informatie over menselijke gezichten te verwerken is belangrijk in veel verschillende softwarescenario's. Voorbeelden hiervan zijn scenario's voor beveiliging, natuurlijke gebruikersinterface, analyse en beheer van inhoud van afbeeldingen, mobiele apps en robotica.

De Face-service biedt verschillende functies die in de volgende secties worden beschreven.

## <a name="face-detection"></a>Gezichtsdetectie

De Face-service is in staat om menselijke gezichten te detecteren op een afbeelding en de locatiecoördinaten van de gezichtsrechthoek te retourneren. Desgewenst kan met gezichtsdetectie een reeks kenmerken met betrekking tot het gezicht worden geëxtraheerd. Voorbeelden hiervan zijn hoofdhouding, geslacht, leeftijd, emotie, gezichtshaar en bril.

> [!NOTE]
> De functie voor gezichtsdetectie is ook beschikbaar via de [Computer Vision-API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Als u meer bewerkingen wilt uitvoeren met gezichtsgegevens, gebruikt u de Face-service. Dat is de service die in dit artikel wordt besproken.

![Een afbeelding van een vrouw en een man met rechthoeken getekend rond de gezichten, en de leeftijd en het geslacht weergegeven](./Images/Face.detection.jpg)

Zie het artikel over concepten van [gezichtsdetectie](concepts/face-detection.md) voor meer informatie over gezichtsdetectie. Zie ook de referentiedocumentatie over de [Detectie-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-verification"></a>Gezichtsverificatie

De Verificatie-API voert een verificatie uit op basis van twee gedetecteerde gezichten of op basis van een gedetecteerd gezicht in relatie tot een object van één persoon. Waar het eigenlijk op neerkomt is dat de Verificatie-API evalueert of twee gezichten van dezelfde persoon zijn. Deze mogelijkheid kan goed van pas komen in beveiligingsscenario's. Zie voor meer informatie de handleiding over concepten van [gezichtsherkenning](concepts/face-recognition.md) of de referentiedocumentatie over de [Verificatie-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

De Vergelijkbare zoeken-API vergelijkt een doelgezicht met een set gezichten die dit doelgezicht zouden kunnen zijn, en zoekt vervolgens naar een kleinere set gezichten die op het doelgezicht lijken. Er worden twee werkingsmodi, matchPerson en matchFace, ondersteund. De modus matchPerson retourneert vergelijkbare gezichten nadat er een filter is toegepast waarbij dezelfde persoon met behulp van de [Verificatie-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) als uitgangspunt is genomen. In de modus matchFace wordt het filter voor dezelfde persoon genegeerd. Er wordt een lijst met vergelijkbare gezichten geretourneerd die al dan niet tot dezelfde persoon behoren.

In het onderstaande voorbeeld ziet u het doelgezicht:

![Een glimlachende vrouw](./Images/FaceFindSimilar.QueryFace.jpg)

En deze afbeeldingen zijn de kandidaatgezichten:

![Vijf afbeeldingen van glimlachende mensen Afbeelding a en b zijn van dezelfde persoon.](./Images/FaceFindSimilar.Candidates.jpg)

Om vier vergelijkbare gezichten te vinden, retourneert de modus matchPerson a en b, die dezelfde persoon aanduiden als het doelgezicht. De modus matchFace retourneert a, b, c en d&mdash;precies vier kandidaten, zelfs als er een aantal andere personen zijn dan het doel of daar weinig gelijkenis mee vertonen. Zie voor meer informatie de handleiding over concepten van [gezichtsherkenning](concepts/face-recognition.md) of de referentiedocumentatie over de [Vergelijkbare zoeken-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="face-grouping"></a>Gezichtsgroepering

De Groeperings-API verdeelt een set onbekende gezichten in groepen op basis van gelijkenis. Elke groep is een niet-aaneengesloten juiste subset van de oorspronkelijke set van gezichten. Alle gezichten in een groep behoren waarschijnlijk tot dezelfde persoon. Er kunnen verschillende groepen voor één persoon zijn. De groepen worden onderscheiden door een andere factor, bijvoorbeeld de gezichtsuitdrukking. Zie voor meer informatie de handleiding over concepten van [gezichtsherkenning](concepts/face-recognition.md) of de referentiedocumentatie over de [Groeperings-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="person-identification"></a>Identificatie van personen

De Identificatie-API wordt gebruikt om een gedetecteerd gezicht te identificeren met behulp van een personendatabase. Deze functie kan nuttig zijn voor het automatisch taggen van afbeeldingen in software voor het beheren van foto's. U maakt de database vooraf en kunt deze na verloop van tijd bewerken.

In de volgende afbeelding ziet u een voorbeeld van een database met de naam `"myfriends"`. Elke groep kan maximaal 1 miljoen verschillende persoonsobjecten bevatten. Voor elk persoonsobject kunnen maximaal 248 gezichten zijn geregistreerd.

![Een raster met drie kolommen voor verschillende personen, elk met drie rijen van afbeeldingen van gezichten](./Images/person.group.clare.jpg)

Nadat u een database hebt gemaakt en getraind, kunt u een identificatie uitvoeren op basis van de groep en een nieuw gedetecteerd gezicht. Als het gezicht wordt geïdentificeerd als een persoon in de groep, wordt het persoonsobject geretourneerd.

Zie voor meer informatie de handleiding over concepten van [gezichtsherkenning](concepts/face-recognition.md) of de referentiedocumentatie over de [Identificatie-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="containers"></a>Containers

[Gebruik de Face-container](face-how-to-install-containers.md) om gezichten te detecteren, herkennen en identificeren. Hiertoe installeert u een gestandaardiseerde Docker-container die zich dichter bij uw gegevens bevindt.

## <a name="sample-apps"></a>Voorbeeld-apps

In de volgende voorbeeldtoepassingen ziet u een aantal manieren om de Face-service te gebruiken:

- [Face-API: Windows-clientbibliotheek en voorbeeld](https://github.com/Microsoft/Cognitive-Face-Windows) is een WPF-app in verschillende scenario's voor gezichtsdetectie, -analyse en -identificatie.
- [FamilyNotes UWP-app](https://github.com/Microsoft/Windows-appsample-familynotes) is een UWP-app (Universal Windows Platform) die gebruikmaakt van gezichtsidentificatie gecombineerd met spraak, Cortana, inkt en camera in een familiescenario waarin notities worden gedeeld.

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle resources van Cognitive Services, dienen ontwikkelaars die de Face-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Volg een quickstart voor het implementeren van een eenvoudig scenario voor gezichtsdetectie in code:

- [Snelstart: Gezichten in een afbeelding detecteren met behulp van de .NET SDK met C#](quickstarts/csharp.md). Er zijn andere talen beschikbaar.
