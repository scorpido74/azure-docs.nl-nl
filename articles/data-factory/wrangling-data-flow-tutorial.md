---
title: Aan de slag met wrangling data flow in Azure Data Factory
description: Een zelf studie over het voorbereiden van gegevens in Azure Data Factory met behulp van wrangling-gegevens stroom
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: f5a7f372f286a7b26a4a9916ed9df913b151e967
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628466"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Gegevens voorbereiden met wrangling-gegevens stroom

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="create-a-wrangling-data-flow"></a>Een wrangling-gegevens stroom maken

Er zijn twee manieren om een wrangling-gegevens stroom te maken in Azure Data Factory. U kunt ook op het plus-pictogram klikken en **gegevens stroom** selecteren in het deel venster Factory resources.

![Wrangling](media/wrangling-data-flow/tutorial7.png)

De andere methode bevindt zich in het deel venster activiteiten van het pijp lijn-canvas. Open de accordeon voor **verplaatsen en transformeren** en sleep de activiteit **gegevens stroom** naar het canvas.

Selecteer in beide methoden in het zijvenster dat wordt geopend, de optie **nieuwe gegevens stroom maken** en kies **Wrangling data flow**. Klik op OK.

![Wrangling](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Een wrangling-gegevens stroom schrijven

Voeg een **bron gegevensset** toe voor uw wrangling-gegevens stroom. U kunt ofwel een bestaande gegevensset kiezen of een nieuwe maken. U kunt ook een Sink-gegevensset selecteren. U kunt een of meer bron gegevens sets kiezen, maar er is op dit moment slechts één sink toegestaan. Het kiezen van een Sink-gegevensset is optioneel, maar er is ten minste één bron-gegevensset vereist.

> [!NOTE]
> Alleen ADLS gen 2 gescheiden tekst worden ondersteund voor een beperkte preview. 

![Wrangling](media/wrangling-data-flow/tutorial4.png)

Klik op **maken** om de Power query online Mashup-Editor te openen.

![Wrangling](media/wrangling-data-flow/tutorial5.png)

Maak uw wrangling-gegevens stroom met behulp van code-Free Data Preparation. Zie [transformatie functies](wrangling-data-flow-functions.md) voor een lijst met beschik bare functies/

![Wrangling](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Een wrangling-gegevens stroom uitvoeren en controleren

Klik op **debug** in het canvas op de pijp lijn om het uitvoeren van een pijp lijn fout opsporing uit te voeren voor een wrangling-gegevens stroom. Zodra u de gegevens stroom hebt gepubliceerd, voert de **trigger nu** een uitvoering op aanvraag uit van de laatste gepubliceerde pijp lijn. Wrangling-gegevens stromen kunnen worden gepland met alle bestaande Azure Data Factory triggers.

![Wrangling](media/wrangling-data-flow/tutorial3.png)

Ga naar het tabblad **monitor** om de uitvoer te visualiseren van een triggered wrangling-activiteit voor het uitvoeren van gegevens stromen.

![Wrangling](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een toewijzings gegevens stroom](tutorial-data-flow.md).