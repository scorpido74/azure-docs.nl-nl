---
title: Uitvoerfoutbeleid in Azure Stream Analytics
description: Meer informatie over het beleid voor het verwerken van uitvoerfouten dat beschikbaar is in Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431610"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Azure Stream Analytics-uitvoerfoutbeleid
In dit artikel wordt het beleid voor het verwerken van uitvoergegevens beschreven die kunnen worden geconfigureerd in Azure Stream Analytics.

Beleid voor het verwerken van uitvoergegevensfouten is alleen van toepassing op fouten bij gegevensconversie die optreden wanneer de uitvoergebeurtenis die wordt geproduceerd door een Stream Analytics-taak niet voldoet aan het schema van de doelsink. U dit beleid configureren door **Opnieuw proberen** of **Neerzetten**te kiezen. Selecteer in de Azure-portal in een taak Stream Analytics onder **Foutbeleid** **configureren**om uw selectie te maken.

![Locatie van azure Stream Analytics-uitvoerfoutbeleidslocatie](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Opnieuw proberen
Wanneer er een fout optreedt, probeert Azure Stream Analytics het schrijven van de gebeurtenis voor onbepaalde tijd opnieuw te schrijven totdat de afschrijving is geslaagd. Er is geen time-out voor nieuwe pogingen. Uiteindelijk worden alle volgende gebeurtenissen geblokkeerd voor verwerking door de gebeurtenis die opnieuw wordt geprobeerd. Deze optie is het beleid voor het afhandelen van de standaarduitvoerfout.

## <a name="drop"></a>Drop
Door Azure Stream Analytics worden uitvoergebeurtenissen die tot een gegevensconversiefout leiden verwijderd. De verwijderde gebeurtenissen kunnen niet worden hersteld voor latere verwerking.


Alle tijdelijke fouten (bijvoorbeeld netwerkfouten) worden opnieuw geprobeerd, ongeacht de beleidsconfiguratie voor het afhandelen van uitvoerfouten.


## <a name="next-steps"></a>Volgende stappen
[Handleiding voor probleemoplossing voor Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
