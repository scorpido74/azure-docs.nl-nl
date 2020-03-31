---
title: 'Dubbele rijen verwijderen: naslaginformatie over modules'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Dubbele rijen verwijderen in Azure Machine Learning om potentiële duplicaten uit een gegevensset te verwijderen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 490d3305abcbcd906a0f727d736db8cab7e4287e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456018"
---
# <a name="remove-duplicate-rows-module"></a>Module Dubbele rijen verwijderen

In dit artikel wordt een module beschreven in Azure Machine Learning designer (preview).

Gebruik deze module om potentiële duplicaten uit een gegevensset te verwijderen.

Stel dat uw gegevens er als volgt uitziet en meerdere records voor patiënten vertegenwoordigen. 

| Patiënt-ID | Initialen| Geslacht|Leeftijd|Toegelaten|
|----|----|----|----|----|
|1|F.m.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.m.| M| 24| Feb|
|4| F.m.| M| 23| Feb|
| | F.m.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| Geen getal| |
|7| F.A.M.| M| Geen getal| |

Het is duidelijk dat dit voorbeeld meerdere kolommen heeft met mogelijk dubbele gegevens. Of ze daadwerkelijk duplicaten hangt af van uw kennis van de gegevens. 

+ U weet bijvoorbeeld dat veel patiënten dezelfde naam hebben. U zou geen duplicaten elimineren met behulp van naamkolommen, alleen de **ID-kolom.** Op die manier worden alleen de rijen met dubbele ID-waarden uitgefilterd, ongeacht of de patiënten dezelfde naam hebben of niet.

+ U ook besluiten om duplicaten toe te staan in het veld ID en een andere combinatie van bestanden gebruiken om unieke records te vinden, zoals voornaam, achternaam, leeftijd en geslacht.  

Als u de criteria wilt instellen voor het al dan niet dupliceren van een rij, geeft u één kolom of een set kolommen op die als **sleutels**moeten worden gebruikt. Twee rijen worden alleen als duplicaten beschouwd wanneer de waarden in **alle** belangrijke kolommen gelijk zijn. Als een rij ontbrekende waarde heeft voor **sleutels,** worden deze niet beschouwd als dubbele rijen. Als geslacht en leeftijd bijvoorbeeld zijn ingesteld als sleutels in bovenstaande tabel, zijn rij 6 en 7 geen dubbele rijen, omdat ze waarde missen in Leeftijd.

Wanneer u de module uitvoert, wordt een kandidaatgegevensset gemaakt en wordt een set rijen geretourneerd met geen duplicaten in de reeks kolommen die u hebt opgegeven.

> [!IMPORTANT]
> De brongegevensset wordt niet gewijzigd; deze module maakt een nieuwe gegevensset die wordt gefilterd om duplicaten uit te sluiten, op basis van de criteria die u opgeeft.

## <a name="how-to-use-remove-duplicate-rows"></a>Dubbele rijen verwijderen gebruiken

1. Voeg de module toe aan uw pijplijn. U de module **Dubbele rijen verwijderen** vinden onder **Gegevenstransformatie**, **Manipulatie**.  

2. Verbind de gegevensset die u wilt controleren op dubbele rijen.

3. Klik in het deelvenster **Eigenschappen** onder **Filterexpressie Sleutelkolomselectie**op **Kolomkiezer starten**om kolommen te kiezen die u wilt gebruiken bij het identificeren van duplicaten.

    In deze context betekent **Key** geen unieke id. Alle kolommen die u selecteert met de kolomkiezer, worden aangeduid als **sleutelkolommen**. Alle niet-geselecteerde kolommen worden beschouwd als niet-belangrijke kolommen. De combinatie van kolommen die u als toetsen selecteert, bepaalt de uniciteit van de records. (Zie het als een SQL-instructie die meerdere equalities joins gebruikt.)

    Voorbeelden:

    + "Ik wil ervoor zorgen dat id's uniek zijn": Kies alleen de ID-kolom.
    + "Ik wil ervoor zorgen dat de combinatie van voornaam, achternaam en ID uniek is": Selecteer alle drie de kolommen.

4. Gebruik het selectievakje **Eerste dubbele rij behouden** om aan te geven welke rij u wilt retourneren wanneer duplicaten worden gevonden:

    + Als deze optie is geselecteerd, wordt de eerste rij geretourneerd en worden andere weer verwijderd. 
    + Als u deze optie uitschakelt, wordt de laatste dubbele rij in de resultaten bewaard en worden andere verwijderd. 

5. Verzend de pijplijn.

6. Als u de resultaten wilt bekijken, klikt u met de rechtermuisknop op de module en selecteert u **Visualiseren**. 

> [!TIP]
> Als de resultaten moeilijk te begrijpen zijn of als u bepaalde kolommen van overweging wilt uitsluiten, u kolommen verwijderen met de module [Kolommen selecteren in gegevensset.](./select-columns-in-dataset.md)

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 