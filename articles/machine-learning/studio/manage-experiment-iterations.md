---
title: Experimentherhalingen beheren
titleSuffix: Azure Machine Learning Studio (classic)
description: Experiment herhalingen beheren in Azure Machine Learning Studio (klassiek). U kunt op elk gewenst moment eerdere uitvoeringen van uw experimenten bekijken om te controleren, opnieuw te bezoeken en uiteindelijk de vorige hypo Thesen te bevestigen of te verfijnen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: d2c1f31db93d2caecc5576de18eb15e01cc94461
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492847"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Experiment herhalingen beheren in Azure Machine Learning Studio (klassiek)
Het ontwikkelen van een voorspellend analyse model is een iteratief proces: wanneer u de verschillende functies en para meters van uw experiment wijzigt, worden de resultaten geconvergeerd voordat u tevreden bent over een getraind, effectief model. De sleutel voor dit proces is het bijhouden van de verschillende iteraties van uw experiment-para meters en configuraties.



U kunt op elk gewenst moment eerdere uitvoeringen van uw experimenten bekijken om te controleren, opnieuw te bezoeken en uiteindelijk de vorige hypo Thesen te bevestigen of te verfijnen. Wanneer u een experiment uitvoert, houdt Machine Learning Studio (klassiek) een geschiedenis bij van de uitvoering, met inbegrip van gegevensset, module en poort verbindingen en-para meters. Deze geschiedenis legt ook resultaten vast, runtime-informatie, zoals start-en stop tijden, logboek berichten en uitvoerings status. U kunt op elk gewenst moment terugkeren naar een van deze uitvoeringen om de weer gave van uw experiment en de tussenliggende resultaten te bekijken. U kunt zelfs een vorige uitvoering van uw experiment gebruiken om te starten in een nieuwe fase van de query en detectie op uw pad om eenvoudige, complexe of zelfs ensemble-model oplossingen te maken.

> [!NOTE]
> Wanneer u een vorige uitvoering van een experiment bekijkt, is die versie van het experiment vergrendeld en kan het niet worden bewerkt. U kunt echter een kopie ervan opslaan door te klikken op **Opslaan als** en een nieuwe naam op te geven voor de kopie. Machine Learning Studio (klassiek) Hiermee opent u de nieuwe kopie, die u vervolgens kunt bewerken en uitvoeren. Deze kopie van uw experiment is beschikbaar in de lijst met **experimenten** en al uw andere experimenten.
> 
> 

## <a name="viewing-the-prior-run"></a>De eerdere uitvoering weer geven
Wanneer u een experiment hebt geopend dat u ten minste één keer hebt uitgevoerd, kunt u de voor gaande uitvoering van het experiment bekijken door te klikken op **vorige uitvoeren** in het deel venster Eigenschappen.

Stel dat u een experiment maakt en voert u een van de volgende versies uit op 11:23, 11:42 en 11:55. Als u de laatste uitvoering van het experiment (11:55) opent en op **eerdere uitvoering**klikt, wordt de versie 11:42 geopend.

## <a name="viewing-the-run-history"></a>De uitvoerings geschiedenis weer geven
U kunt alle vorige uitvoeringen van een experiment bekijken door te klikken op **uitvoerings geschiedenis bekijken** in een open experiment.

Stel dat u een experiment met de [lineaire regressie][linear-regression] module maakt en u wilt weten hoe u de waarde van het **leer tempo** van de resultaten van uw experiment kunt wijzigen. U voert het experiment meerdere keren uit met verschillende waarden voor deze para meter, als volgt:

| Waarde leer tempo | Begin tijd van uitvoering |
| --- | --- |
| 0.1 |9/11/2014 4:18:58 uur |
| 0,2 |9/11/2014 4:24:33 uur |
| 0,4 |9/11/2014 4:28:36 uur |
| 0,5 |9/11/2014 4:33:31 uur |

Als u op **uitvoerings geschiedenis weer geven**klikt, ziet u een lijst met al deze uitvoeringen:

![Voor beeld van uitvoerings geschiedenis](./media/manage-experiment-iterations/viewrunhistory.jpg)

Klik op een van deze uitvoeringen om een moment opname van het experiment weer te geven op het moment dat u het hebt uitgevoerd. De configuratie, parameter waarden, opmerkingen en de resultaten worden allemaal bewaard, zodat u een volledig overzicht krijgt van de uitvoering van uw experiment.

> [!TIP]
> Als u de iteraties van het experiment wilt documenteren, kunt u de titel wijzigen telkens wanneer u deze uitvoert. u kunt de **samen vatting** van het experiment bijwerken in het deel venster Eigenschappen en u kunt opmerkingen toevoegen of bijwerken voor afzonderlijke modules om uw wijzigingen vast te leggen. De opmerkingen titel, samen vatting en module worden bij elke uitvoering van het experiment opgeslagen.
> 
> 

In de lijst met experimenten op het tabblad **experimenten** in machine learning Studio (klassiek) wordt altijd de nieuwste versie van een experiment weer gegeven. Als u een vorige uitvoering van het experiment (met **eerdere uitvoering** of **uitvoerings geschiedenis**) opent, kunt u terugkeren naar de concept versie door te klikken op **uitvoerings geschiedenis weer geven** en de iteratie te selecteren met de **status** **bewerkbaar**.

## <a name="iterating-on-a-previous-run"></a>Een vorige uitvoering herhalen
Wanneer u klikt op **vorige uitvoeren** of **uitvoerings geschiedenis bekijken** en een eerdere uitvoering openen, kunt u een voltooid experiment bekijken in de modus alleen-lezen.

Als u een herhaling van uw experiment wilt beginnen, te beginnen met de manier waarop u deze hebt geconfigureerd voor een eerdere uitvoering, kunt u dit doen door de uitvoeren te openen en op **Opslaan als**te klikken. Hiermee maakt u een nieuw experiment, met een nieuwe titel, een lege uitvoerings geschiedenis en alle onderdelen en parameter waarden van de vorige uitvoering. Dit nieuwe experiment wordt vermeld op het tabblad **experimenten** op de start pagina van machine learning Studio (klassiek) en u kunt het wijzigen en uitvoeren, zodat er een nieuwe uitvoerings geschiedenis wordt weer gegeven voor deze herhaling van uw experiment. 

Stel dat u de geschiedenis voor het uitvoeren van experimenten hebt weer gegeven in de vorige sectie. U wilt weten wat er gebeurt wanneer u de para meter **Learning rate** instelt op 0,4 en verschillende waarden voor de para meter **aantal trainings-epoches** probeert.

1. Klik op **uitvoerings geschiedenis weer geven** en open de herhaling van het experiment dat u hebt uitgevoerd om 4:28:36 uur (waarin u de waarde van de para meter instelt op 0,4).
2. Klik op **Opslaan als**.
3. Voer een nieuwe titel in en klik op het selectie vakje **OK** . Er wordt een nieuw exemplaar van het experiment gemaakt.
4. Wijzig de para meter **aantal trainings-epoches** .
5. Klik op **uitvoeren**.

U kunt nu door gaan met het wijzigen en uitvoeren van deze versie van uw experiment, een nieuwe uitvoerings geschiedenis bouwen om uw werk vast te leggen.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
