---
title: Visuele bewaking van gegevens stroom Azure Data Factory toewijzing
description: Azure Data Factory gegevens stromen visueel bewaken
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 5d69e29c83bcbe433b800d6877ba1c7440eceedc
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387975"
---
# <a name="monitor-data-flows"></a>Gegevens stromen bewaken



Nadat u klaar bent met het maken en opsporen van fouten in uw gegevens stroom, moet u de gegevens stroom plannen om uit te voeren volgens een schema in de context van een pijp lijn. U kunt de pijp lijn plannen vanuit Azure Data Factory met behulp van triggers. U kunt ook de optie nu activeren van de opbouw functie voor het Azure Data Factory pijp lijn gebruiken om een uitvoering met één uitvoering uit te voeren om uw gegevens stroom in de pijplijn context te testen.

Wanneer u de pijp lijn uitvoert, kunt u de pijp lijn en alle activiteiten in de pijp lijn bewaken, met inbegrip van de activiteit gegevens stroom. Klik op het pictogram monitor in het deel venster aan de linkerkant Azure Data Factory gebruikers interface. Er wordt een scherm weer gegeven dat er ongeveer als volgt uitziet. Met de gemarkeerde pictogrammen kunt u inzoomen op de activiteiten in de pijp lijn, met inbegrip van de activiteit gegevens stroom.

![Gegevensstroom controleren](media/data-flow/mon001.png "Bewaking gegevensstroom")

Er worden statistieken op dit niveau weer geven, inclusief de uitvoerings tijden en de status. De run-ID op het activiteit niveau wijkt af van de run-ID op het pijplijn niveau. De uitvoerings-ID op het vorige niveau is voor de pijp lijn. Als u op de bril klikt, krijgt u gedetailleerde informatie over de uitvoering van de gegevens stroom.

![Gegevensstroom controleren](media/data-flow/mon002.png "Bewaking gegevensstroom")

Wanneer u zich in de weer gave van het grafische knoop punt bevindt, ziet u een vereenvoudigde versie van uw gegevens stroom diagram.

![Gegevensstroom controleren](media/data-flow/mon003.png "Bewaking gegevensstroom")

## <a name="view-data-flow-execution-plans"></a>Uitvoerings plannen voor gegevens stromen weer geven

Wanneer uw gegevens stroom in Spark wordt uitgevoerd, bepaalt Azure Data Factory optimale code paden op basis van het geheel van uw gegevens stroom. Daarnaast kunnen de uitvoerings paden optreden op verschillende scale-out knoop punten en gegevens partities. Daarom vertegenwoordigt de bewakings grafiek het ontwerp van uw stroom, waarbij rekening wordt gehouden met het pad van de uitvoering van de trans formaties. Wanneer u op afzonderlijke knoop punten klikt, worden ' groeperingen ' weer gegeven die de code vertegenwoordigen die samen in het cluster is uitgevoerd. De tijds instellingen en aantallen die u ziet, worden weer gegeven in plaats van de afzonderlijke stappen in uw ontwerp.

![Gegevensstroom controleren](media/data-flow/mon004.png "Bewaking gegevensstroom")

* Wanneer u klikt op de open ruimte in het venster bewaking, worden in de statistieken in het onderste deel venster de timing en het aantal rijen weer gegeven voor elke Sink en de trans formaties die leiden tot de Sink-gegevens voor transformatie afkomst.

* Wanneer u afzonderlijke trans formaties selecteert, ontvangt u extra feedback over het rechterdeel venster waarin de partitie statistieken, kolom aantallen, scheefheid (hoe gelijkmatig de gegevens worden verdeeld over verschillende partities) en kurtosis (hoe piekige de gegevens zijn) worden weer gegeven.

* Wanneer u in de weer gave knoop punt op de Sink klikt, wordt kolom afkomst weer gegeven. Er zijn drie verschillende methoden die kolommen tijdens de gegevens stroom in de Sink worden verzameld. Dit zijn:

  * Berekend: u gebruikt de kolom voor voorwaardelijke verwerking of binnen een expressie in uw gegevens stroom, maar niet in de Sink
  * Afgeleid: de kolom is een nieuwe kolom die u hebt gegenereerd in uw stroom, dat wil zeggen dat deze niet aanwezig is in de bron
  * Toegewezen: de kolom afkomstig uit de bron en uw toewijzing is aan een Sink-veld gekoppeld
  
## <a name="monitor-icons"></a>Bewaak pictogrammen

Dit pictogram geeft aan dat de transformatie gegevens al in de cache zijn opgeslagen op het cluster, waardoor de timing en het uitvoerings traject hebben geduurd in het account:

![Gegevensstroom controleren](media/data-flow/mon004.png "Bewaking gegevensstroom")

U ziet ook groene cirkel pictogrammen in de trans formatie. Ze vertegenwoordigen een telling van het aantal sinks dat de gegevens stromen.
