---
title: Statistische trans formatie in toewijzing van gegevens stroom-Azure Data Factory | Microsoft Docs
description: Meer informatie over het samen stellen van gegevens op schaal in Azure Data Factory met de statistische trans formatie toewijzings gegevens stroom.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 0201cbdd05cd8aae4afb92b459bf58fb5ff6a142
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026973"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Statistische trans formatie in toewijzing van gegevens stroom 



De cumulatieve trans formatie is de locatie waar u aggregaties van kolommen in uw gegevens stromen definieert. Met de opbouw functie voor expressies kunt u verschillende typen aggregaties definiëren, zoals som, MIN, maximum en aantal dat kan worden gegroepeerd op bestaande of berekende kolommen.

## <a name="group-by"></a>Groeperen op
Selecteer een bestaande kolom of maak een nieuwe berekende kolom die u wilt gebruiken als een component Group by voor uw aggregatie. Als u een bestaande kolom wilt gebruiken, selecteert u de gewenste kolom in de vervolg keuzelijst. Als u een nieuwe berekende kolom wilt maken, houdt u de muis aanwijzer over de component en klikt u op berekende kolom. Hiermee wordt de [opbouw functie voor de data flow-expressie](concepts-data-flow-expression-builder.md)geopend. Wanneer u de berekende kolom maakt, voert u de naam van de uitvoer kolom onder het veld naam als in. Als u een extra component Group by wilt toevoegen, houdt u de muis aanwijzer over een bestaande component en klikt u op +.

Aggregatie ![van de transformatie groep op instellingen](media/data-flow/agg.png "geaggregeerde transformatie groep op instellingen")

> [!NOTE]
> Een component Group by is optioneel in een geaggregeerde trans formatie.

## <a name="aggregate-column"></a>Statistische kolom 
Kies het tabblad aggregaties om aggregatie-expressies te maken. U kunt een bestaande kolom kiezen en de waarde overschrijven met de aggregatie, of een nieuw veld maken met een nieuwe naam. De expressie aggregatie wordt ingevoerd in het rechter vak naast de kolom naam kiezer. Als u de expressie wilt bewerken, klikt u op het tekstvak om de opbouw functie voor expressies te openen. Als u een extra aggregatie wilt toevoegen, houdt u de muis aanwijzer boven een bestaande expressie en klikt u op + om een nieuwe aggregatie kolom of [kolom patroon](concepts-data-flow-column-pattern.md)te maken.

![instellingen voor trans formatie](media/data-flow/agg2.png "instellingen voor trans formatie")

> [!NOTE]
> Elke aggregatie-expressie moet ten minste één statistische functie bevatten.

> [!NOTE]
> In de foutopsporingsmodus kan de opbouw functie voor expressies geen gegevens previews maken met statistische functies. Voor het weer geven van voor beelden van gegevens voor samengevoegde trans formaties, sluit u de opbouw functie voor expressies en bekijkt u de gegevens via het tabblad gegevens voorbeeld.

## <a name="reconnect-rows-and-columns"></a>Rijen en kolommen opnieuw verbinden
Samengestelde trans formaties zijn nauw gelijkwaardig met SQL-query's voor aggregatie selectie. Kolommen die niet zijn opgenomen in de component Group by of statistische functies, stromen niet naar de uitvoer van uw geaggregeerde trans formatie. Als er andere kolommen zijn die u wilt opnemen in de cumulatieve uitvoer van de rijen, moet u:

* Gebruik een statistische functie om die extra kolom op te nemen, zoals Last () of First ()
* Gebruik het [Self-deelname patroon](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)om de kolommen opnieuw samen te voegen voor uw aggregatie.

## <a name="next-steps"></a>Volgende stappen

* Aggregatie op basis van een venster definiëren met behulp van de [venster transformatie](data-flow-window.md)
