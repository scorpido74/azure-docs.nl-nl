---
title: Grafieken voor gegevens stromen
description: Werken met data factory data flow-grafieken
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 0d357c4c671070a5c5e9d4587e2f90b6628996f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81605359"
---
# <a name="mapping-data-flow-graphs"></a>Grafieken voor gegevens stromen toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Het ontwerp oppervlak voor het toewijzen van gegevens stromen is een constructie-Opper vlak waar u gegevens stromen van boven naar beneden maakt, van links naar rechts. Er is een werkset aan elke trans formatie gekoppeld met een plus teken (+). Richt u op uw bedrijfs logica in plaats van knoop punten te verbinden via randen in een vrije-vorm DAG-omgeving.

Hieronder vindt u ingebouwde mechanismen voor het beheren van de gegevens stroom grafiek.

## <a name="move-nodes"></a>Knooppunten verplaatsen

![Aggregatie opties voor trans formatie](media/data-flow/agghead.png "aggregator-header")

Zonder een paradigma voor slepen en neerzetten, de manier om een transformatie knooppunt te verplaatsen, is het wijzigen van de inkomende stroom. In plaats daarvan gaat u trans formaties verplaatsen door de "binnenkomende stroom" te wijzigen.

## <a name="streams-of-data-inside-of-data-flow"></a>Gegevens stromen in gegevens stroom

In Azure Data Factory gegevens stroom vertegenwoordigen streams de stroom van gegevens. In het deel venster trans formatie-instellingen ziet u het veld ' inkomende stroom '. Zo weet u welke binnenkomende gegevens stroom de trans formatie verfeedt. U kunt de fysieke locatie van uw transformatie knooppunt in de grafiek wijzigen door te klikken op de naam van de inkomende stroom en een andere gegevens stroom te selecteren. De huidige trans formatie en alle daaropvolgende transformaties voor die stroom worden vervolgens verplaatst naar de nieuwe locatie.

Als u een trans formatie met een of meer transformaties verplaatst, wordt de nieuwe locatie in de gegevens stroom gekoppeld via een nieuwe vertakking.

Als u geen daaropvolgende trans formaties hebt nadat het knoop punt dat u hebt geselecteerd, wordt alleen dat transformatie verplaatst naar de nieuwe locatie.

## <a name="hide-graph-and-show-graph"></a>Grafiek verbergen en grafiek weer geven

Er bevindt zich een knop aan de rechter kant van het onderste configuratie deel venster, waar u het onderste deel venster kunt uitbreiden naar het volledige scherm wanneer u aan transformatie configuraties werkt. Zo kunt u de knoppen Vorige en volgende gebruiken om door de configuraties van de grafiek te navigeren. Als u terug wilt gaan naar de grafiek weergave, klikt u op de knop omlaag en gaat u terug naar het scherm splitsen.

## <a name="search-graph"></a>Zoeken in grafiek

U kunt de grafiek doorzoeken met de zoek knop op het ontwerp oppervlak.

![Zoeken](media/data-flow/search001.png "Zoeken in grafiek")

## <a name="next-steps"></a>Volgende stappen

Nadat u het ontwerp van de gegevens stroom hebt voltooid, schakelt u de knop fout opsporing in en test u deze in de foutopsporingsmodus rechtstreeks in de [Data flow Designer](concepts-data-flow-debug-mode.md) of de [fout opsporing voor de pijp lijn](control-flow-execute-data-flow-activity.md).
