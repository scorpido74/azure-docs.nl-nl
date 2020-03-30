---
title: Aan de slag met de gegevensstroom in Azure Data Factory
description: Een zelfstudie over het voorbereiden van gegevens in Azure Data Factory met behulp van een gegevensstroom
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8ae109045381dba77610dedc5bb95e233b213eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682266"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Gegevens voorbereiden met de gegevensstroom van getouwtrek

## <a name="create-a-wrangling-data-flow"></a>Een getouwtrekgegevensstroom maken

Er zijn twee manieren om een getouwtrekgegevensstroom te maken in Azure Data Factory. Een manier is om op het pluspictogram te klikken en **Gegevensstroom** te selecteren in het deelvenster fabrieksresources.

![Gehakketak](media/wrangling-data-flow/tutorial7.png)

De andere methode bevindt zich in het deelvenster Activiteiten van het pijplijncanvas. Open de accordeon **Verplaatsen en Transformeren** en sleep de activiteit **Gegevensstroom** naar het canvas.

Selecteer in beide methoden in het zijvenster dat wordt geopend de optie **Nieuwe gegevensstroom maken** en kies **Wrangling-gegevensstroom**. Klik op OK.

![Gehakketak](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Auteur van een getouwtrek data stroom

Voeg een **brongegevensset toe** voor uw gegevensstroom. U een bestaande gegevensset kiezen of een nieuwe gegevensset maken. U ook een sink-gegevensset selecteren. U een of meer brongegevenssets kiezen, maar op dit moment is slechts één sink toegestaan. Het kiezen van een sink dataset is optioneel, maar er is ten minste één brongegevensset vereist.

> [!NOTE]
> Alleen ADLS Gen 2 delimited text worden ondersteund voor een beperkte preview. 

![Gehakketak](media/wrangling-data-flow/tutorial4.png)

Klik **op Maken** om de mashup-editor van Power Query Online te openen.

![Gehakketak](media/wrangling-data-flow/tutorial5.png)

Auteur van uw getouw getouw wrangling data flow met behulp van code-vrije gegevens voorbereiding. Zie [transformatiefuncties](wrangling-data-flow-functions.md) voor de lijst met beschikbare functies/

![Gehakketak](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Het uitvoeren en bewaken van een getouwtrek gegevensstroom

Als u een foutopsporingsrun voor pijplijnen van een gegevensstroom wilt uitvoeren, klikt u op **Foutopsporing** in het pijplijncanvas. Zodra u uw gegevensstroom publiceert, voert **Trigger nu** een on-demand run uit van de laatst gepubliceerde pijplijn. Getouwtrek gegevensstromen kunnen worden gepland met alle bestaande Azure Data Factory-triggers.

![Gehakketak](media/wrangling-data-flow/tutorial3.png)

Ga naar het tabblad **Monitor** om de uitvoer van een geactiveerde gegevensstroomactiviteit te visualiseren.

![Gehakketak](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een kaartgegevensstroom](tutorial-data-flow.md).