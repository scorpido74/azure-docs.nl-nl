---
title: Iteratieve ontwikkeling en foutopsporing in Azure Data Factory
description: Meer informatie over het iteratief ontwikkelen en debuggen van Data Factory-pijplijnen in de Azure-portal.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411517"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iteratief ontwikkelen en fouten opsporen met Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met Azure Data Factory u iteratief Data Factory-pijplijnen ontwikkelen en debuggen.

Bekijk de volgende video voor een acht minuten durende introductie en demonstratie van deze functie:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Iteratieve foutopsporingsfuncties
Maak pijplijnen en doe testruns met de **foutopsporingsmogelijkheid** in het pijplijncanvas zonder één regel code te schrijven.

![Foutopsporingsfunctie op het pijplijncanvas](media/iterative-development-debugging/iterative-development-image1.png)

Bekijk de resultaten van uw testruns in het **uitvoervenster** van het pijplijncanvas.

![Uitvoervenster van het pijplijncanvas](media/iterative-development-debugging/iterative-development-image2.png)

Nadat een testrun is geslaagd, voegt u meer activiteiten toe aan uw pijplijn en blijft u op een iteratieve manier foutopsporing. U ook een testuitvoeren **annuleren** terwijl deze aan de gang is.

![Een testrun annuleren](media/iterative-development-debugging/iterative-development-image3.png)

Wanneer u testtests uitvoert, hoeft u uw wijzigingen in de gegevensfabriek niet te publiceren voordat u **Foutopsporing**selecteert. Deze functie is handig in scenario's waarin u ervoor wilt zorgen dat de wijzigingen werken zoals verwacht voordat u de werkstroom van de gegevensfabriek bijwerkt.

> [!IMPORTANT]
> Als **u Foutopsporing selecteert,** wordt de pijplijn uitgevoerd. Als de pijplijn bijvoorbeeld kopieeractiviteit bevat, kopieert de testrun gegevens van bron naar bestemming. Daarom raden we u aan testmappen te gebruiken in uw kopieeractiviteiten en andere activiteiten bij het foutopsporing. Nadat u de pijplijn hebt verwijderd, schakelt u over naar de werkelijke mappen die u bij normale bewerkingen wilt gebruiken.

## <a name="visualizing-debug-runs"></a>Foutopsporing van foutopsporing visualiseren

U alle foutopsporingsuitvoeringen die aan de gang zijn voor uw gegevensfabriek op één plaats visualiseren. Selecteer **Foutopsporing weergeven in** de rechterbovenhoek van de pagina. Deze functie is handig in scenario's waarin er masterpijplijnen zijn die foutopsporingsprogramma's voor onderliggende pijplijnen starten, en u wilt dat één weergave alle actieve foutopsporingsuitvoeringen ziet.

![Het pictogram Actieve foutopsporing weergeven selecteren](media/iterative-development-debugging/view-debug-runs-image1.png)

![Voorbeeldlijst met actieve foutopsporingsuitvoeringen](media/iterative-development-debugging/view-debug-runs-image2.png)

Als u actieve foutopsporingssessies in gegevensstroom hebt, worden deze sessies weergegeven in het onderste deel van het actieve foutopsporingsvenster. U een actieve gegevensstroomsessie selecteren en het betreffende cluster stoppen.

![Voorbeeldlijst met foutopsporing van actieve gegevensstromen](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Foutopsporing controleren

De testuitvoeringen die zijn gestart met de **foutopsporingsmogelijkheid,** zijn niet beschikbaar in de lijst op het tabblad **Monitor.** U alleen uitgevoerd e-runs zien die worden geactiveerd met **triggernow-,** **plannings-** of **tumblingwindow-triggers** op het tabblad **Monitor.** U de laatste testrun zien die is gestart met de **foutopsporingsmogelijkheid** in het **uitvoervenster** van het pijplijncanvas.

## <a name="setting-breakpoints-for-debugging"></a>Breekpunten instellen voor foutopsporing

Met Data Factory kun je ook debuggen totdat je een bepaalde activiteit op het pijplijncanvas hebt bereikt. Plaats gewoon een breekpunt op de activiteit totdat u wilt testen, en selecteer **Foutopsporing**. Data Factory zorgt ervoor dat de test alleen wordt uitgevoerd totdat de breekpuntactiviteit op het pijplijncanvas wordt uitgevoerd. Deze *foutopsporingsfunctie Tot* is handig als u niet de hele pijplijn wilt testen, maar slechts een subset van activiteiten in de pijplijn.

![Breekpunten op het pijplijncanvas](media/iterative-development-debugging/iterative-development-image4.png)

Als u een breekpunt wilt instellen, selecteert u een element op het pijplijncanvas. Een *optie Foutopsporing tot* wordt weergegeven als een lege rode cirkel in de rechterbovenhoek van het element.

![Voordat u een breekpunt instelt op het geselecteerde element](media/iterative-development-debugging/iterative-development-image5.png)

Nadat u de optie *Foutopsporing tot hebt* geselecteerd, wordt deze gewijzigd in een gevulde rode cirkel om aan te geven dat het breekpunt is ingeschakeld.

![Na het instellen van een breekpunt op het geselecteerde element](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Volgende stappen
[Continue integratie en implementatie in Azure Data Factory](continuous-integration-deployment.md)
