---
title: Uitvoer fout beleidsregels in Azure Stream Analytics
description: Meer informatie over de uitvoer-beleid beschikbaar in Azure Stream Analytics voor foutafhandeling.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 22112272bb302769e5969cf6995d486438deb41f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431610"
---
# <a name="azure-stream-analytics-output-error-policy"></a>Azure Stream Analytics uitvoer foutbeleid
Dit artikel beschrijft de uitvoer gegevens foutafhandeling beleid die kunnen worden geconfigureerd in Azure Stream Analytics.

Uitvoer foutafhandeling beleid alleen voor gegevensconversiefouten die zich voordoen geldt wanneer de uitvoergebeurtenis geproduceerd door een Stream Analytics-taak komt niet overeen met het schema van de doel-sink. U kunt dit beleid configureren door het kiezen van een **opnieuw** of **neerzetten**. In de Azure-portal, terwijl in een Stream Analytics-taak, onder **configureren**, selecteer **Foutbeleid** uw selectie te maken.

![Azure Stream Analytics de uitvoerlocatie voor fout beleid](./media/stream-analytics-output-error-policy/stream-analytics-error-policy-locate.png)


## <a name="retry"></a>Opnieuw proberen
Wanneer een fout optreedt, Azure Stream Analytics nieuwe pogingen voor het schrijven van de gebeurtenis voor onbepaalde tijd totdat de schrijfbewerking is geslaagd. Er is geen time-out voor nieuwe pogingen. Uiteindelijk alle volgende gebeurtenissen hebben geen toegang tot verwerking door de gebeurtenis die probeert het opnieuw. Deze optie is de standaardfout van de uitvoer verwerking van beleid.

## <a name="drop"></a>Verwijderen
Door Azure Stream Analytics worden uitvoergebeurtenissen die tot een gegevensconversiefout leiden verwijderd. De verwijderde gebeurtenissen kunnen niet worden hersteld voor latere verwerking.


Alle tijdelijke fouten (bijvoorbeeld netwerkfouten) opnieuw kunnen worden uitgevoerd, ongeacht de configuratie van beleid voor foutafhandeling van uitvoer.


## <a name="next-steps"></a>Volgende stappen
[Gids voor probleemoplossing voor Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
