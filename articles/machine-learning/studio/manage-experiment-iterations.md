---
title: Experimentherhalingen beheren
titleSuffix: ML Studio (classic) - Azure
description: Experiment herhalingen beheren in Azure Machine Learning Studio (klassiek). U kunt eerdere uitvoeringen van uw experimenten bekijken op elk gewenst moment om te kunnen verstrekken, bezoekt, en uiteindelijk bevestigen of vorige veronderstellingen verfijnen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 03/20/2017
ms.openlocfilehash: 71a20f0de9907b04b9dcfd63c0003f2540a2e370
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153380"
---
# <a name="manage-experiment-iterations-in-azure-machine-learning-studio-classic"></a>Experiment herhalingen beheren in Azure Machine Learning Studio (klassiek)
Het ontwikkelen van een predictive Analytics-model is in een iteratief proces - als u de verschillende functies en parameters van uw experiment wijzigt, worden de resultaten geconvergeerd tot u tevreden bent u een getraind en doeltreffend model hebt. Sleutel voor dit proces is het bijhouden van de verschillende iteraties van uw experiment parameters en configuraties.



U kunt eerdere uitvoeringen van uw experimenten bekijken op elk gewenst moment om te kunnen verstrekken, bezoekt, en uiteindelijk bevestigen of vorige veronderstellingen verfijnen. Wanneer u een experiment uitvoert, houdt Machine Learning Studio (klassiek) een geschiedenis bij van de uitvoering, met inbegrip van gegevensset, module en poort verbindingen en-para meters. Dit overzicht bevat ook resultaten, runtime-gegevens, zoals de begin- en stoptijden, logboekberichten en uitvoeringsstatus. U kunt kijken weer een van deze wordt uitgevoerd op elk gewenst moment om te controleren van de tijdlijn van uw experiment en tussenliggende resultaten genereren. U kunt zelfs een vorige uitvoering van uw experiment gebruiken om te starten in een nieuwe fase van de query en detectie van het pad voor het maken van eenvoudige, complexe of zelfs ensembles modellering van oplossingen.

> [!NOTE]
> Wanneer u een experiment dat eerder is uitgevoerd, wordt deze versie van het experiment is vergrendeld en kan niet worden bewerkt. U kunt echter een kopie ervan opslaan door te klikken op **Opslaan als** en een nieuwe naam op te geven voor de kopie. Machine Learning Studio (klassiek) Hiermee opent u de nieuwe kopie, die u vervolgens kunt bewerken en uitvoeren. Deze kopie van uw experiment is beschikbaar in de lijst met **experimenten** en al uw andere experimenten.
> 
> 

## <a name="viewing-the-prior-run"></a>De vorige uitvoering weergeven
Wanneer u een experiment hebt geopend dat u ten minste één keer hebt uitgevoerd, kunt u de voor gaande uitvoering van het experiment bekijken door te klikken op **vorige uitvoeren** in het deel venster Eigenschappen.

Stel bijvoorbeeld dat u een experiment maken en uitvoeren van versie ervan op 11:23, 11:42 en 11:55. Als u de laatste uitvoering van het experiment (11:55) opent en op **eerdere uitvoering**klikt, wordt de versie 11:42 geopend.

## <a name="viewing-the-run-history"></a>De Uitvoeringsgeschiedenis weergeven
U kunt alle vorige uitvoeringen van een experiment bekijken door te klikken op **uitvoerings geschiedenis bekijken** in een open experiment.

Stel dat u een experiment met de [lineaire regressie][linear-regression] module maakt en u wilt weten hoe u de waarde van het **leer tempo** van de resultaten van uw experiment kunt wijzigen. U voert het experiment meerdere keren met verschillende waarden voor deze parameter als volgt uit:

| De waarde voor Learning | Begintijd uitvoering |
| --- | --- |
| 0.1 |9/11/2014 4:18:58 uur |
| 0.2 |9/11/2014 4:24:33 pm |
| 0,4 |9/11/2014 4:28:36 uur |
| 0.5 |9/11/2014 4:33:31 uur |

Als u op **uitvoerings geschiedenis weer geven**klikt, ziet u een lijst met al deze uitvoeringen:

![Voorbeeld van de geschiedenis uitvoeren](./media/manage-experiment-iterations/viewrunhistory.jpg)

Klik op een van deze uitvoeringen om weer te geven van een momentopname van het experiment op het moment dat u deze hebt uitgevoerd. De configuratie, parameterwaarden, opmerkingen en resultaten blijven alle behouden zodat u een volledige gegevensrecord van deze uitvoering van uw experiment.

> [!TIP]
> Als u de iteraties van het experiment wilt documenteren, kunt u de titel wijzigen telkens wanneer u deze uitvoert. u kunt de **samen vatting** van het experiment bijwerken in het deel venster Eigenschappen en u kunt opmerkingen toevoegen of bijwerken voor afzonderlijke modules om uw wijzigingen vast te leggen. De titel, overzicht en module opmerkingen worden opgeslagen bij elke uitvoering van het experiment.
> 
> 

In de lijst met experimenten op het tabblad **experimenten** in machine learning Studio (klassiek) wordt altijd de nieuwste versie van een experiment weer gegeven. Als u een vorige uitvoering van het experiment (met **eerdere uitvoering** of **uitvoerings geschiedenis**) opent, kunt u terugkeren naar de concept versie door te klikken op **uitvoerings geschiedenis weer geven** en de iteratie te selecteren met de **status** **bewerkbaar**.

## <a name="iterating-on-a-previous-run"></a>Doorlopen van een vorige uitvoering
Wanneer u klikt op **vorige uitvoeren** of **uitvoerings geschiedenis bekijken** en een eerdere uitvoering openen, kunt u een voltooid experiment bekijken in de modus alleen-lezen.

Als u een herhaling van uw experiment wilt beginnen, te beginnen met de manier waarop u deze hebt geconfigureerd voor een eerdere uitvoering, kunt u dit doen door de uitvoeren te openen en op **Opslaan als**te klikken. Hiermee maakt u een nieuw experiment, met een nieuwe titel, een lege geschiedenis, uitvoeren en de onderdelen en parameterwaarden van de vorige uitvoeren. Dit nieuwe experiment wordt vermeld op het tabblad **experimenten** op de start pagina van machine learning Studio (klassiek) en u kunt het wijzigen en uitvoeren, zodat er een nieuwe uitvoerings geschiedenis wordt weer gegeven voor deze herhaling van uw experiment. 

Stel bijvoorbeeld dat u het experiment uitvoeringsgeschiedenis wordt weergegeven in de vorige sectie hebt. U wilt weten wat er gebeurt wanneer u de para meter **Learning rate** instelt op 0,4 en verschillende waarden voor de para meter **aantal trainings-epoches** probeert.

1. Klik op **uitvoerings geschiedenis weer geven** en open de herhaling van het experiment dat u hebt uitgevoerd om 4:28:36 uur (waarin u de waarde van de para meter instelt op 0,4).
2. Klik op **Opslaan als**.
3. Voer een nieuwe titel in en klik op het selectie vakje **OK** . Een nieuwe kopie van het experiment wordt gemaakt.
4. Wijzig de para meter **aantal trainings-epoches** .
5. Klik op **uitvoeren**.

U kunt nu met de blijven om te wijzigen en uitvoeren van deze versie van uw experiment, het bouwen van een nieuwe uitvoeringsgeschiedenis om vast te leggen van uw werk.

<!-- Module References -->
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
