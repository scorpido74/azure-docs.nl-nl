---
title: Experimentherhalingen beheren
titleSuffix: ML Studio (classic) - Azure
description: Experimentiteraties beheren in Azure Machine Learning Studio (klassiek). U eerdere uitvoeringen van uw experimenten op elk gewenst moment bekijken om eerdere veronderstellingen uit te dagen, opnieuw te bekijken en uiteindelijk te bevestigen of te verfijnen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 5c8a278f09fdb3b605020e4c2fcf7aa2776906e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204304"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Experimentiteraties beheren in Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Het ontwikkelen van een voorspellend analysemodel is een iteratief proces - als u de verschillende functies en parameters van uw experiment wijzigt, convergeren uw resultaten totdat u tevreden bent dat u een getraind, effectief model hebt. De sleutel tot dit proces is het bijhouden van de verschillende iteraties van uw experimentparameters en -configuraties.



U eerdere uitvoeringen van uw experimenten op elk gewenst moment bekijken om eerdere veronderstellingen uit te dagen, opnieuw te bekijken en uiteindelijk te bevestigen of te verfijnen. Wanneer u een experiment uitvoert, houdt Machine Learning Studio (klassiek) een geschiedenis van de uitvoering bij, inclusief gegevensset, module en poortverbindingen en parameters. Deze geschiedenis legt ook resultaten, runtime-informatie zoals start- en stoptijden, logboekberichten en uitvoeringsstatus vast. U op elk van deze runs op elk gewenst moment terugkijken om de chronologie van uw experiment en tussentijdse resultaten te bekijken. U zelfs een eerdere uitvoering van uw experiment gebruiken om een nieuwe fase van onderzoek en ontdekking te starten op uw pad naar het maken van eenvoudige, complexe of zelfs ensemblemodelleringsoplossingen.

> [!NOTE]
> Wanneer u een eerdere uitvoering van een experiment bekijkt, is die versie van het experiment vergrendeld en kan deze niet worden bewerkt. U er echter een kopie van opslaan door op **OPSLAAN AS** te klikken en een nieuwe naam voor de kopie op te geven. Machine Learning Studio (klassiek) opent het nieuwe exemplaar, dat u vervolgens bewerken en uitvoeren. Deze kopie van uw experiment is beschikbaar in de **lijst EXPERIMENTEN** samen met al uw andere experimenten.
> 
> 

## <a name="viewing-the-prior-run"></a>De eerdere run bekijken
Wanneer u een experiment hebt geopend dat u ten minste één keer hebt uitgevoerd, u de vorige uitvoering van het experiment bekijken door op **Prior Run** in het eigenschappenvenster te klikken.

Stel dat u een experiment maakt en versies ervan uitvoert op 11:23, 11:42 en 11:55. Als u de laatste uitvoering van het experiment opent (11:55) en op **Prior Run klikt,** wordt de versie die u om 11:42 hebt uitgevoerd, geopend.

## <a name="viewing-the-run-history"></a>De rungeschiedenis weergeven
U alle eerdere uitvoeringen van een experiment bekijken door in een geopend experiment op **Run-geschiedenis weergeven** te klikken.

Stel dat u een experiment maakt met de module [Lineaire regressie][linear-regression] en het effect wilt waarnemen van het wijzigen van de waarde van **de leersnelheid** op uw experimentresultaten. U voert het experiment meerdere keren uit met verschillende waarden voor deze parameter, als volgt:

| Waarde leersnelheid | Begintijd uitvoeren |
| --- | --- |
| 0.1 |9/11/2014 16:18:58 |
| 0,2 |9/11/2014 16:24:33 |
| 0,4 |9/11/2014 16:28:36 |
| 0.5 |9/11/2014 16:33:31 |

Als u op **GESCHIEDENIS VAN WEERGAVE KLIKT,** ziet u een lijst met al deze uitvoeringen:

![Voorbeeld run geschiedenis](./media/manage-experiment-iterations/viewrunhistory.jpg)

Klik op een van deze uitvoeringen om een momentopname van het experiment te bekijken op het moment dat u het experiment hebt uitgevoerd. De configuratie, parameterwaarden, opmerkingen en resultaten blijven behouden om u een volledige registratie te geven van die uitvoering van uw experiment.

> [!TIP]
> Als u uw iteraties van het experiment wilt documenteren, u de titel elke keer wijzigen wanneer u het uitvoert, u het **overzicht** van het experiment bijwerken in het eigenschappenvenster en u opmerkingen over afzonderlijke modules toevoegen of bijwerken om uw wijzigingen op te nemen. De opmerkingen over titel, overzicht en module worden opgeslagen bij elke run van het experiment.
> 
> 

De lijst met experimenten op het tabblad **EXPERIMENTEN** in Machine Learning Studio (klassiek) geeft altijd de nieuwste versie van een experiment weer. Als u een eerdere uitvoering van het experiment opent (met de geschiedenis van **Prior Run** of **VIEW RUN),** u terugkeren naar de conceptversie door op DE GESCHIEDENIS VAN DE UITVOERING **BEKIJKEN** te klikken en de iteratie te selecteren met een **status** van **bewerkbaar**.

## <a name="iterating-on-a-previous-run"></a>Herhalen op een vorige run
Wanneer u op **Eerdere run-** of **RUN-GESCHIEDENIS** klikt en een vorige run opent, u een voltooid experiment weergeven in de alleen-lezen modus.

Als u een iteratie van uw experiment wilt starten, te beginnen met de manier waarop u het voor een vorige run hebt geconfigureerd, u dit doen door de run te openen en op **OPSLAAN ALS**te klikken. Hiermee wordt een nieuw experiment gemaakt, met een nieuwe titel, een lege rungeschiedenis en alle componenten en parameterwaarden van de vorige run. Dit nieuwe experiment wordt vermeld op het tabblad **EXPERIMENTEN** op de startpagina van Machine Learning Studio (klassieke) en u het wijzigen en uitvoeren, waarbij een nieuwe rungeschiedenis wordt gestart voor deze iteratie van uw experiment. 

Stel dat u de geschiedenis van de experimentrun in de vorige sectie hebt weergegeven. U wilt observeren wat er gebeurt wanneer u de parameter **Leersnelheid** instelt op 0,4 en verschillende waarden voor de parameter **Aantal trainingstijdperken** proberen.

1. Klik op **RUN HISTORY BEKIJKEN** en open de iteratie van het experiment dat u hebt uitgevoerd om 16:28:36 uur (waarbij u de parameterwaarde instelt op 0,4).
2. Klik **op OPSLAAN ALS**.
3. Voer een nieuwe titel in en klik op het **OK-vinkje.** Er wordt een nieuwe kopie van het experiment gemaakt.
4. Wijzig de parameter **Aantal trainingstijdperken.**
5. Klik **op UITVOEREN**.

U deze versie van uw experiment nu blijven wijzigen en uitvoeren, waarbij een nieuwe rungeschiedenis wordt opgebouwd om uw werk op te nemen.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
