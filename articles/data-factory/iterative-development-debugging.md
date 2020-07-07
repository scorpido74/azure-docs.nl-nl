---
title: Iteratieve ontwikkeling en fout opsporing in Azure Data Factory
description: Meer informatie over het ontwikkelen en fouten opsporen Data Factory pijp lijnen iteratief in de Azure Portal.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 49e406f0730189439bb1c2529b23d8ad6d760a2f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81411517"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iteratief ontwikkelen en fouten opsporen met Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met Azure Data Factory kunt u Data Factory pijp lijnen iteratief ontwikkelen en fouten opsporen.

Bekijk de volgende video voor een inleiding en demonstratie van acht minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Herhaalde functies voor fout opsporing
Maak pijp lijnen en voer tests uit met de functie voor **fout opsporing** in het pijplijn doek zonder een regel code te schrijven.

![Mogelijkheid tot fouten opsporen op het pijplijn papier](media/iterative-development-debugging/iterative-development-image1.png)

Bekijk de resultaten van uw test uitvoeringen in het **uitvoer** venster van het pijp lijn papier.

![Uitvoer venster van het pijp lijn papier](media/iterative-development-debugging/iterative-development-image2.png)

Wanneer een test uitvoering slaagt, voegt u meer activiteiten aan uw pijp lijn toe en voert u de fout opsporing op een iteratieve manier door. U kunt ook een test uitvoering **Annuleren** terwijl deze wordt uitgevoerd.

![Een test uitvoering annuleren](media/iterative-development-debugging/iterative-development-image3.png)

Wanneer u test uitvoeringen uitvoert, hoeft u uw wijzigingen niet te publiceren naar de data factory voordat u **debug**selecteert. Deze functie is handig in scenario's waarin u er zeker van wilt zijn dat de wijzigingen werken zoals verwacht voordat u de data factory werk stroom bijwerkt.

> [!IMPORTANT]
> Als u **debug** selecteert, wordt de pijp lijn werkelijk uitgevoerd. Als de pijp lijn bijvoorbeeld Kopieer activiteit bevat, kopieert de test uitvoering de gegevens van de bron naar het doel. Als gevolg hiervan raden wij u aan test mappen in uw Kopieer activiteiten en andere activiteiten te gebruiken bij het opsporen van fouten. Nadat u de fout opsporing voor de pijp lijn hebt uitgevoerd, gaat u naar de daad werkelijke mappen die u wilt gebruiken voor normale bewerkingen.

## <a name="visualizing-debug-runs"></a>Fouten in de fout opsporing in visualiseren

U kunt alle debug-uitvoeringen die worden uitgevoerd voor uw data factory, op één plek visualiseren. Selecteer **debug-uitvoeringen weer geven** in de rechter bovenhoek van de pagina. Deze functie is handig in scenario's waarin u hoofd pijplijnen hebt voor het starten van debug-uitvoeringen voor onderliggende pijp lijnen en u wilt dat één weer gave alle actieve debug-uitvoeringen ziet.

![Selecteer het pictogram actieve debug-uitvoeringen weer geven](media/iterative-development-debugging/view-debug-runs-image1.png)

![Voorbeeld lijst met actieve debug-uitvoeringen](media/iterative-development-debugging/view-debug-runs-image2.png)

Als u actieve gegevens stroom fout opsporing sessies hebt, worden deze sessies weer gegeven in het onderste gedeelte van het actieve venster fout opsporing. U kunt een actieve Data flow-sessie selecteren en het respectieve cluster stoppen.

![Voorbeeld lijst van actieve gegevens stroom fout opsporing uitgevoerd](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Fout opsporing van bewaking wordt uitgevoerd

De test uitvoeringen die met de mogelijkheid voor **fout opsporing** worden gestart, zijn niet beschikbaar in de lijst op het tabblad **monitor** . U kunt alleen uitvoeringen weer geven die zijn geactiveerd met het venster **trigger Now**, **Schedule**of **tumblingvenstertriggers** op het tabblad **monitor** . U kunt zien dat de laatste test uitvoering is gestart met de functie voor **fout opsporing** in het **uitvoer** venster van het pijp lijn papier.

## <a name="setting-breakpoints-for-debugging"></a>Onderbrekings punten instellen voor fout opsporing

Met Data Factory kunt u ook fouten opsporen totdat u een bepaalde activiteit op het pijplijn doek hebt bereikt. Plaats een onderbrekings punt op de activiteit tot u wilt testen en selecteer **fout opsporing**. Data Factory zorgt ervoor dat de test alleen wordt uitgevoerd tot de activiteit onderbrekings punt op het pijp lijn-canvas. Deze functie voor het *opsporen van fouten* is handig wanneer u niet de volledige pijp lijn wilt testen, maar alleen een subset van activiteiten in de pijp lijn.

![Onderbrekings punten op het pijp lijn papier](media/iterative-development-debugging/iterative-development-image4.png)

Selecteer een element op het pijplijn doek om een onderbrekings punt in te stellen. De optie *debug until* wordt weer gegeven als een lege rode cirkel in de rechter bovenhoek van het element.

![Voordat een onderbrekings punt voor het geselecteerde element wordt ingesteld](media/iterative-development-debugging/iterative-development-image5.png)

Nadat u de optie *debug until* hebt geselecteerd, verandert deze in een gevulde rode cirkel om aan te geven dat het onderbrekings punt is ingeschakeld.

![Na het instellen van een onderbrekings punt voor het geselecteerde element](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Volgende stappen
[Continue integratie en implementatie in Azure Data Factory](continuous-integration-deployment.md)
