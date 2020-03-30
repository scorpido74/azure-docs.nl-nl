---
title: Gegevens samenvatten
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Gegevens samenvatten in Azure Machine Learning om een basisrapport voor beschrijvende statistieken voor de kolommen in een gegevensset te genereren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: b0def12582dd3795e1b17334406e28d77c3c5656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477439"
---
# <a name="summarize-data"></a>Gegevens samenvatten

In dit artikel wordt een module van Azure Machine Learning designer (preview) beschreven.

Gebruik de module Gegevens samenvatten om een set standaardstatistische metingen te maken die elke kolom in de invoertabel beschrijven.

Overzichtsstatistieken zijn handig als u de kenmerken van de volledige gegevensset wilt begrijpen. U moet bijvoorbeeld weten:

- Hoeveel ontbrekende waarden zijn er in elke kolom?
- Hoeveel unieke waarden zijn er in een functiekolom?
- Wat is de gemiddelde en standaarddeviatie voor elke kolom?

De module berekent de belangrijke scores voor elke kolom en retourneert een rij overzichtsstatistieken voor elke variabele (gegevenskolom) die als invoer wordt geleverd.

## <a name="how-to-configure-summarize-data"></a>Samenvattengegevens configureren  

1. Voeg de module **Gegevens samenvatten** toe aan uw pijplijn. U vindt deze module in de categorie **Statistische functies** in de ontwerper.

1. Verbind de gegevensset waarvoor u een rapport wilt genereren.

    Als u slechts over enkele kolommen wilt rapporteren, gebruikt u de module [Kolommen selecteren in gegevensset](select-columns-in-dataset.md) om een subset van kolommen te projecteren waarmee u wilt werken.

1. Er zijn geen extra parameters vereist. Standaard analyseert de module alle kolommen die als invoer worden geleverd en wordt, afhankelijk van het type waarden in de kolommen, een relevante set statistieken uitgevoerd zoals beschreven in de sectie [Resultaten.](#results)

1. Verzend de pijplijn.

## <a name="results"></a>Resultaten

Het rapport van de module kan de volgende statistieken bevatten. 

|Kolomnaam|Beschrijving|
|------|------|  
|**Functie**|Naam van de kolom|
|**Count**|Aantal van alle rijen|
|**Aantal unieke waarden**|Aantal unieke waarden in kolom|
|**Aantal ontbrekende waarden**|Aantal unieke waarden in kolom|
|**Min.**|Laagste waarde in kolom|  
|**Max**|Hoogste waarde in kolom|
|**Gemiddelde**|Gemiddelde van alle kolomwaarden|
|**Gemiddelde afwijking**|Gemiddelde afwijking van kolomwaarden|
|**1e Kwartiel**|Waarde bij eerste kwartiel|
|**Mediaan**|Mediaankolomwaarde|
|**3e kwartiel**|Waarde bij derde kwartiel|
|**Modus**|Modus voor kolomwaarden|
|**Bereik**|Geheel getal dat het aantal waarden tussen de maximum- en minimumwaarden weergeeft|
|**Voorbeeldvariantie**|Variantie voor kolom; zie Opmerking|
|**Voorbeeld standaarddeviatie**|Standaarddeviatie voor kolom; zie Opmerking|
|**Voorbeeld scheefheid**|Scheefheid voor kolom; zie Opmerking|
|**Voorbeeld Kurtosis**|Kurtosis voor kolom; zie Opmerking|
|**P0.5**|0,5% percentiel|
|**P1**|1% percentiel|
|**P5**|5% percentiel|
|**P95**|95% percentiel|
|**P99.5**|99,5% percentiel |

## <a name="technical-notes"></a>Technische notities

- Voor niet-numerieke kolommen worden alleen de waarden voor Aantal, Aantal unieke waarden en aantal ontbrekende waarden berekend. Voor andere statistieken wordt een null-waarde geretourneerd.

- Kolommen die Booleaanse waarden bevatten, worden met behulp van de volgende regels verwerkt:

    - Bij het berekenen van Min wordt een logische AND toegepast.
    
    - Bij het berekenen van Max wordt een logische OK toegepast
    
    - Bij het berekenen van bereik controleert de module eerst of het aantal unieke waarden in de kolom gelijk is aan 2.
    
    - Bij het berekenen van een statistiek waarvoor floating-point berekeningen vereist zijn, worden waarden van True behandeld als 1.0 en worden waarden van False behandeld als 0.0.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning.  
