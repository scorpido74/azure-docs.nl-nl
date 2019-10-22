---
title: Gegevens samenvatten
titleSuffix: Azure Machine Learning service
description: Informatie over het gebruik van de module gegevens samenvatten in Azure Machine Learning service voor het genereren van een basis beschrijvende statistieken rapport voor de kolommen in een gegevensset.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 54d7235ef56a94c9c0e7b780c53cbd9791bf4f53
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694712"
---
# <a name="summarize-data"></a>Gegevens samenvatten

In dit artikel wordt een module van Azure Machine Learning visuele interface beschreven.

Gebruik de module gegevens samenvatten om een set standaard statistische maat eenheden te maken waarmee elke kolom in de invoer tabel wordt beschreven.

Samenvattings statistieken zijn handig wanneer u inzicht wilt krijgen in de kenmerken van de volledige gegevensset. U moet bijvoorbeeld het volgende weten:

- Hoeveel ontbrekende waarden zijn er in elke kolom?
- Hoeveel unieke waarden zijn er in een functie kolom?
- Wat is het gemiddelde en de standaard afwijking voor elke kolom?

De module berekent de belang rijke scores voor elke kolom en retourneert een rij met samenvattings statistieken voor elke variabele (gegevens kolom) die is opgegeven als invoer.

## <a name="how-to-configure-summarize-data"></a>Samenvattings gegevens configureren  

1. Voeg de module **gegevens samenvatten** toe aan uw pijp lijn. U kunt deze module vinden in de categorie **statistische functies** in de visuele interface.

1. Verbind de gegevensset waarvoor u een rapport wilt genereren.

    Als u alleen op bepaalde kolommen wilt rapporteren, gebruikt u de module [select columns in dataset](select-columns-in-dataset.md) om een subset van kolommen te projecteren waarmee u wilt werken.

1. Er zijn geen aanvullende para meters vereist. Standaard analyseert de module alle kolommen die als invoer worden opgegeven en, afhankelijk van het type waarden in de kolommen, een relevante set statistieken, zoals beschreven in de sectie [resultaten](#results) .

1. Voer de pijp lijn uit of klik met de rechter muisknop op de module en selecteer **geselecteerde uitvoeren**.

## <a name="results"></a>Resultaten

Het rapport van de module kan de volgende statistieken bevatten. 

|Kolom naam|Beschrijving|
|------|------|  
|**Functie**|Naam van de kolom|
|**Aantal**|Aantal rijen|
|**Aantal unieke waarden**|Aantal unieke waarden in kolom|
|**Aantal ontbrekende waarden**|Aantal unieke waarden in kolom|
|**Haal**|Laagste waarde in kolom|  
|**Aantal**|Hoogste waarde in kolom|
|**Greenwich**|Gemiddelde van alle kolom waarden|
|**Gemiddelde afwijking**|Gemiddelde afwijking van kolom waarden|
|**1e kwartiel**|Waarde bij eerste kwartiel|
|**Mediaan**|Kolom waarde mediaan|
|**3e kwartiel**|Waarde bij derde kwartiel|
|**Penmodus**|Modus van kolom waarden|
|**Range**|Geheel getal dat het aantal waarden tussen de maximum-en minimum waarden vertegenwoordigt|
|**Voorbeeld afwijking**|Variantie voor kolom; Zie opmerking|
|**Standaard deviatie voor beeld**|Standaard afwijking voor kolom; Zie opmerking|
|**Voor beeld scheefheid**|Scheefheid voor kolom; Zie opmerking|
|**Voor beeld van kurtosis**|Kurtosis voor kolom; Zie opmerking|
|**P 0,5**|0,5% percentiel|
|**P1**|1% percentiel|
|**P5**|5% percentiel|
|**P95**|95% percentiel|
|**P 99,5**|99,5% percentiel |

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set beschik bare modules](module-reference.md) voor Azure machine learning service.  
