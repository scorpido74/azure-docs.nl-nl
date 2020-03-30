---
title: 'Kolommen toevoegen: moduleverwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Kolommen toevoegen in Azure Machine Learning om twee gegevenssets samen te voegen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: f2e067f76d6ed7d89a38e9b8920c407f161969a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456775"
---
# <a name="add-columns-module"></a>Module Kolommen toevoegen

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om twee gegevenssets te concaten. U combineert alle kolommen uit de twee gegevenssets die u opgeeft als invoer om één gegevensset te maken. Als u meer dan twee gegevenssets moet toevoegen, gebruikt u verschillende exemplaren van **Kolommen toevoegen**.



## <a name="how-to-configure-add-columns"></a>Kolommen toevoegen configureren
1. Voeg de module **Kolommen toevoegen** toe aan uw pijplijn.

2. Sluit de twee gegevenssets die u wilt koppelen. Als u meer dan twee gegevenssets wilt combineren, u verschillende combinaties van Kolommen toevoegen aan elkaar **ketenen.**

    - Het is mogelijk om twee kolommen met een verschillend aantal rijen te combineren. De uitvoergegevensset wordt opgevuld met ontbrekende waarden voor elke rij in de kleinere bronkolom.

    - U geen afzonderlijke kolommen kiezen om toe te voegen. Alle kolommen van elke gegevensset worden samengevoegd wanneer u **Kolommen toevoegen**gebruikt. Als u daarom alleen een subset van de kolommen wilt toevoegen, gebruikt u Kolommen selecteren in gegevensset om een gegevensset te maken met de gewenste kolommen.

3. Verzend de pijplijn.

### <a name="results"></a>Resultaten
Nadat de pijplijn is uitgevoerd:

- Als u de eerste rijen van de nieuwe gegevensset wilt bekijken, klikt u met de rechtermuisknop op de module **Kolommen toevoegen** en selecteert u Visualiseren. Of Selecteer de module en ga naar het tabblad **Uitvoer** in het rechterdeelvenster en klik op het histogrampictogram in de **poortuitvoer** om het resultaat te visualiseren.

Het aantal kolommen in de nieuwe gegevensset is gelijk aan de som van de kolommen van beide invoergegevenssets.

Als er twee kolommen met dezelfde naam in de invoergegevenssets staan, wordt een numeriek achtervoegsel toegevoegd aan de naam van de kolom. Als er bijvoorbeeld twee exemplaren van een kolom met de naam TargetOutcome zijn, wordt de linkerkolom TargetOutcome_1 hernoemd en wordt de rechterkolom TargetOutcome_2.

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 