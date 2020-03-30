---
title: Functiegegevens weergeven in de Azure Maps Android SDK | Microsoft Azure Maps
description: In dit artikel leert u hoe u functiegegevens op een kaart weergeeft met de Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911695"
---
# <a name="display-feature-information"></a>Functie-informatie weergeven

Ruimtelijke gegevens worden vaak weergegeven met behulp van punten, lijnen en veelhoeken. Deze gegevens bevatten vaak metadata-informatie die eraan is gekoppeld. Een punt kan bijvoorbeeld de locatie van een winkel weergeven en metagegevens over dat restaurant kunnen de naam, het adres en het type voedsel zijn dat het serveert. Deze metagegevens kunnen worden toegevoegd `JsonObject`als eigenschappen van deze functies met behulp van een . De volgende code maakt een `title` eenvoudige puntfunctie met een eigenschap die een waarde heeft van "Hello World!"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

Wanneer een gebruiker interactie heeft met een functie op de kaart, kunnen gebeurtenissen worden gebruikt om op die acties te reageren. Een veelvoorkomend scenario is het weergeven van een bericht gemaakt van de metagegevens eigenschappen van een functie waarmee de gebruiker interactie mee. De `OnFeatureClick` gebeurtenis is de belangrijkste gebeurtenis die wordt gebruikt om te detecteren wanneer de gebruiker een functie op de kaart heeft getikt. Er is ook `OnLongFeatureClick` een evenement. Wanneer u `OnFeatureClick` de gebeurtenis aan de kaart toevoegt, kan deze worden beperkt tot één laag door de ID van een laag door te geven om deze te beperken. Als er geen laag-ID wordt doorgegeven, zou het tikken op een functie op de kaart, ongeacht in welke laag deze zich bevindt, deze gebeurtenis afvuren. Met de volgende code wordt een symboollaag gemaakt om `OnFeatureClick` puntgegevens op de kaart weer te geven, voegt u vervolgens een gebeurtenis toe en beperkt deze tot deze symboollaag.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Een pop-upbericht weergeven

Een pop-upbericht is een van de gemakkelijkste manieren om informatie aan de gebruiker weer te geven en is beschikbaar in alle versies van Android. Het ondersteunt geen enkele vorm van gebruikersinvoer en wordt slechts voor een korte periode weergegeven. Als u de gebruiker snel iets wilt laten weten over wat ze hebben aangetikt, is een pop-upbericht mogelijk een goede optie. De volgende code laat zien hoe een `OnFeatureClick` pop-upbericht kan worden gebruikt met de gebeurtenis.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

![Animatie van een functie die wordt aangetikt en een pop-upbericht wordt weergegeven](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Naast pop-upberichten zijn er nog veel meer manieren om de metagegevenseigenschappen van een functie weer te geven, zoals:

- [Snakbar widget](https://developer.android.com/training/snackbar/showing.html) - Snackbars bieden lichtgewicht feedback over een operatie. Ze tonen een kort bericht aan de onderkant van het scherm op mobiele en linksonder op grotere apparaten. Snackbars verschijnen boven alle andere elementen op het scherm en slechts een kan worden weergegeven op een moment.
- [Dialoogvensters](https://developer.android.com/guide/topics/ui/dialogs) - Een dialoogvenster is een klein venster dat de gebruiker vraagt om een beslissing te nemen of aanvullende informatie in te voeren. Een dialoogvenster vult het scherm niet en wordt normaal gesproken gebruikt voor modale gebeurtenissen waarvoor gebruikers een actie moeten uitvoeren voordat ze verder kunnen gaan.
- Voeg een [fragment](https://developer.android.com/guide/components/fragments) toe aan de huidige activiteit.
- Navigeer naar een andere activiteit of weergave.

## <a name="next-steps"></a>Volgende stappen

Ga als u meer gegevens aan uw kaart toevoegen:

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Shapes toevoegen aan een Android-kaart](how-to-add-shapes-to-android-map.md)
