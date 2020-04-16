---
title: Grafieken voor gegevensstromen
description: Werken met grafieken voor gegevensfabrieksgegevens
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 1618011dc3c281cb2ef06ef80cfc7c8e69c4e091
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415493"
---
# <a name="mapping-data-flow-graphs"></a>Grafieken voor gegevensstromen toewijzen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Het ontwerpoppervlak voor kaartgegevensstromen is een "constructie"-oppervlak waar u gegevensstromen van boven naar beneden, van links naar rechts bouwt. Er is een gereedschapskist bevestigd aan elke transformatie met een plus (+) symbool. Concentreer u op uw bedrijfslogica in plaats van knooppunten via randen te verbinden in een vrije DAG-omgeving.

Hieronder vindt u ingebouwde mechanismen om de grafiek van de gegevensstroom te beheren.

## <a name="move-nodes"></a>Knooppunten verplaatsen

![Opties voor geaggregeerde transformatie](media/data-flow/agghead.png "aggregatorkop")

Zonder een drag-and-drop paradigma, de manier om "bewegen" een transformatie knooppunt, is het veranderen van de inkomende stroom. In plaats daarvan verplaatst u transformaties door de "binnenkomende stroom" te wijzigen.

## <a name="streams-of-data-inside-of-data-flow"></a>Gegevensstromen in de gegevensstroom

In Azure Data Factory Data Flow vertegenwoordigen streams de stroom van gegevens. In het deelvenster Transformatie-instellingen ziet u een veld 'Binnenkomende stroom'. Dit vertelt u welke inkomende gegevensstroom die transformatie voedt. U de fysieke locatie van uw transformatieknooppunt in de grafiek wijzigen door op de naam Inkomende stroom te klikken en een andere gegevensstroom te selecteren. De huidige transformatie samen met alle volgende transformaties op die stream zal dan naar de nieuwe locatie gaan.

Als u een transformatie verplaatst met een of meer transformaties erna, wordt de nieuwe locatie in de gegevensstroom samengevoegd via een nieuwe branch.

Als u na het geselecteerde knooppunt geen volgende transformaties hebt, wordt alleen die transformatie naar de nieuwe locatie verplaatst.

## <a name="hide-graph-and-show-graph"></a>Grafiek verbergen en grafiek weergeven

Er is een knop rechts onder in het onderste configuratievenster, waar u het onderste deelvenster uitbreiden naar volledig scherm wanneer u aan transformatieconfiguraties werkt. Hiermee u 'vorige' en 'volgende' knoppen gebruiken om door de configuraties van de grafiek te navigeren. Als u terug wilt gaan naar de grafiekweergave, klikt u op de knop Omlaag en keert u terug naar het gesplitste scherm.

## <a name="search-graph"></a>Zoekgrafiek

U de grafiek doorzoeken met de zoekknop op het ontwerpoppervlak.

![Zoeken](media/data-flow/search001.png "Zoekgrafiek")

## <a name="next-steps"></a>Volgende stappen

Nadat u het ontwerp van de gegevensstroom hebt voltooid, schakelt u de foutopsporingsknop in en test u deze in de foutopsporingsmodus, rechtstreeks in de [gegevensstroomontwerper](concepts-data-flow-debug-mode.md) of [pijplijnfoutbug.](control-flow-execute-data-flow-activity.md)
