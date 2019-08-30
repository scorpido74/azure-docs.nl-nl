---
title: 'Importeren vanuit de web-URL via HTTP: Module verwijzing'
titleSuffix: Azure Machine Learning service
description: Informatie over het gebruik van de web-URL importeren via de HTTP-module in Azure Machine Learning-service om gegevens te lezen van een open bare webpagina voor gebruik in een machine learning experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 59b8e2e73b9904a503c16d8891e5a5bd771fc87f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128757"
---
# <a name="import-from-web-url-via-http-module"></a>Importeren uit web-URL via HTTP-module

In dit artikel wordt een module van de Visual Interface (preview) voor de Azure Machine Learning-service beschreven.

Gebruik deze module om gegevens te lezen van een open bare webpagina voor gebruik in een machine learning experiment.

De volgende beperkingen zijn van toepassing op de gegevens die op een webpagina worden gepubliceerd:

- De gegevens moeten een van de ondersteunde indelingen hebben: CSV, TSV, ARFF of SvmLight. Andere gegevens zullen fouten veroorzaken.
- Er is geen verificatie vereist of ondersteund. Gegevens moeten openbaar beschikbaar zijn. 

Er zijn twee manieren om gegevens op te halen: gebruik de wizard om de gegevens bron in te stellen of configureer deze hand matig.

## <a name="use-the-data-import-wizard"></a>De wizard gegevens importeren gebruiken

1. Voeg de module **gegevens importeren** toe aan uw experiment. U kunt de module vinden in de-interface, in de categorie **gegevens invoer en uitvoer** .

2. Klik op **wizard import data starten** en selecteer Web-URL via http.

3. Plak de URL en selecteer een gegevens indeling.

4. Wanneer de configuratie is voltooid.

Als u een bestaande gegevens verbinding wilt bewerken, start u de wizard opnieuw. De wizard laadt alle vorige configuratie details zodat u niet opnieuw hoeft te beginnen

## <a name="manually-set-properties-in-the-import-data-module"></a>Eigenschappen hand matig instellen in de module gegevens importeren

In de volgende stappen wordt beschreven hoe u de import bron hand matig configureert.

1. Voeg de module [gegevens importeren](import-data.md) toe aan uw experiment. U kunt de module vinden in de-interface, in de categorie **gegevens invoer en uitvoer** .

2. Voor **gegevens bron**selecteert u **Web-URL via http**.

3. Voor **URL**, typt of plakt u de volledige URL van de pagina die de gegevens bevat die u wilt laden.

    De URL moet de site-URL en het volledige pad, met de bestands naam en de extensie, bevatten naar de pagina die de gegevens bevat die moeten worden geladen.

    De volgende pagina bevat bijvoorbeeld de Iris gegevensset uit de machine learning opslag plaats van de University of Californië, Irvine:

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. Selecteer bij **gegevens indeling**een van de ondersteunde gegevens indelingen uit de lijst.

    U wordt aangeraden de gegevens altijd vooraf te controleren om de indeling te bepalen. Op de pagina UC-Irvine wordt de CSV-indeling gebruikt. Andere ondersteunde gegevens indelingen zijn TSV, ARFF en SvmLight.

5. Als de gegevens zich in CSV-of TSV-indeling bevindt, gebruikt u de optie **bestand heeft koprij** om aan te geven of de bron gegevens een koprij bevatten. De rij met koppen wordt gebruikt om kolom namen toe te wijzen.

6. Selecteer de opties **in cache opgeslagen resultaten** als u niet verwacht dat de gegevens veel worden gewijzigd of als u wilt voor komen dat u de gegevens opnieuw laadt telkens wanneer u het experiment uitvoert.

    Als deze optie is geselecteerd, laadt het experiment de gegevens op het moment dat de module voor het eerst wordt uitgevoerd en wordt vervolgens een versie van de gegevensset in de cache gebruikt.

    Als u de gegevensset voor elke herhaling van de gegevensset van de experiment opnieuw wilt laden, schakelt u de optie **resultaten in cache gebruiken** uit. De resultaten worden ook opnieuw geladen als er wijzigingen zijn aangebracht in de para meters van [gegevens importeren](import-data.md).

7. Voer het experiment uit.

## <a name="results"></a>Resultaten

Wanneer u klaar bent, klikt u op de uitvoer gegevensset en selecteert u visualiseren om te zien of de gegevens zijn geïmporteerd.


## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service. 