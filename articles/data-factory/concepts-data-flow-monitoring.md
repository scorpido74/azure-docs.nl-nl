---
title: Visuele bewaking van gegevens stroom toewijzen
description: Azure Data Factory gegevens stromen visueel bewaken
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/17/2020
ms.openlocfilehash: 9594a2ddfaa0103e171618925ba6974bf9ad7f00
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83833962"
---
# <a name="monitor-data-flows"></a>Gegevens stromen bewaken

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nadat u klaar bent met het maken en opsporen van fouten in uw gegevens stroom, wilt u de gegevens stroom plannen om uit te voeren volgens een schema in de context van een pijp lijn. U kunt de pijp lijn plannen vanuit Azure Data Factory met behulp van triggers. U kunt ook de optie nu activeren van de opbouw functie voor het Azure Data Factory pijp lijn gebruiken om een uitvoering met één uitvoering uit te voeren om uw gegevens stroom in de pijplijn context te testen.

Wanneer u de pijp lijn uitvoert, kunt u de pijp lijn en alle activiteiten in de pijp lijn bewaken, met inbegrip van de activiteit gegevens stroom. Klik op het pictogram monitor in het deel venster aan de linkerkant Azure Data Factory gebruikers interface. U ziet een scherm zoals hieronder. Met de gemarkeerde pictogrammen kunt u inzoomen op de activiteiten in de pijp lijn, met inbegrip van de activiteit gegevens stroom.

![Bewaking van gegevens stromen](media/data-flow/mon001.png "Bewaking gegevensstroom")

U ziet de statistieken op dit niveau en ook de uitvoerings tijden en-status. De uitvoerings-ID op het activiteit niveau wijkt af van de uitvoerings-ID op het pijplijn niveau. De uitvoerings-ID op het vorige niveau is voor de pijp lijn. Als u de bril selecteert, krijgt u gedetailleerde informatie over de uitvoering van de gegevens stroom.

![Bewaking van gegevens stromen](media/data-flow/mon002.png "Bewaking gegevensstroom")

Wanneer u zich in de weer gave van het grafische knoop punt bevindt, ziet u een vereenvoudigde alleen-lezen versie van uw gegevens stroom diagram.

![Bewaking van gegevens stromen](media/data-flow/mon003.png "Bewaking gegevensstroom")

Hier volgt een video overzicht van het controleren van de prestaties van uw gegevens stromen vanuit het scherm van de ADF-bewaking:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Uitvoerings plannen voor gegevens stromen weer geven

Wanneer uw gegevens stroom in Spark wordt uitgevoerd, bepaalt Azure Data Factory optimale code paden op basis van het geheel van uw gegevens stroom. Daarnaast kunnen de uitvoerings paden optreden op verschillende scale-out knoop punten en gegevens partities. Daarom vertegenwoordigt de bewakings grafiek het ontwerp van uw stroom, waarbij rekening wordt gehouden met het pad van de uitvoering van de trans formaties. Wanneer u afzonderlijke knoop punten selecteert, ziet u ' groeperingen ' die de code vertegenwoordigen die samen in het cluster is uitgevoerd. De tijds instellingen en aantallen die u ziet, worden weer gegeven in plaats van de afzonderlijke stappen in uw ontwerp.

![Bewaking van gegevens stromen](media/data-flow/mon004.png "Bewaking gegevensstroom")

* Wanneer u in het venster bewaking de optie ruimte openen selecteert, worden in het onderste deel venster de timing en het aantal rijen weer gegeven voor elke Sink en de trans formaties die leiden tot de Sink-gegevens voor transformatie afkomst.

* Wanneer u afzonderlijke trans formaties selecteert, ontvangt u extra feedback over het rechterdeel venster waarin de partitie statistieken, kolom aantallen, scheefheid (hoe gelijkmatig de gegevens worden verdeeld over verschillende partities) en kurtosis (hoe piekige de gegevens zijn) worden weer gegeven.

* Wanneer u de sink in de knooppunt weergave selecteert, kunt u de kolom afkomst zien. Er zijn drie verschillende methoden die kolommen tijdens de gegevens stroom in de Sink worden verzameld. Dit zijn:

  * Berekend: u gebruikt de kolom voor voorwaardelijke verwerking of binnen een expressie in uw gegevens stroom, maar niet in de Sink
  * Afgeleid: de kolom is een nieuwe kolom die u in de stroom hebt gegenereerd, dat wil zeggen, deze is niet aanwezig in de bron
  * Toegewezen: de kolom afkomstig uit de bron en uw toewijzing is aan een Sink-veld gekoppeld
  * Status van de gegevens stroom: de huidige status van de uitvoering
  * Opstart tijd van het cluster: de hoeveelheid tijd voor het verkrijgen van de JIT Spark-Compute-omgeving voor het uitvoeren van gegevens stromen
  * Aantal trans formaties: hoeveel transformatie stappen worden uitgevoerd in uw stroom
  
![Bewaking van gegevens stromen](media/data-flow/monitornew.png "Gegevens stroom bewaking nieuw")  
  
## <a name="monitor-icons"></a>Bewaak pictogrammen

Dit pictogram geeft aan dat de transformatie gegevens al in de cache zijn opgeslagen op het cluster, waardoor de timing en het uitvoerings traject hebben geduurd in het account:

![Bewaking van gegevens stromen](media/data-flow/mon004.png "Bewaking gegevensstroom")

U ziet ook groene cirkel pictogrammen in de trans formatie. Ze vertegenwoordigen een telling van het aantal sinks dat de gegevens stromen.
