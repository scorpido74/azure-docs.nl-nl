---
title: Azure Application Insights-trechters
description: Ontdek hoe u Trechters gebruiken om te ontdekken hoe klanten omgaan met uw toepassing.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 89440a6385bab5b917a866b686e8d2ba828c92e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671049"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Ontdek hoe klanten uw toepassing gebruiken met Application Insights Funnels

Inzicht in de klantervaring is van het grootste belang voor uw bedrijf. Als uw toepassing meerdere fasen omvat, moet u weten of de meeste klanten het hele proces doorlopen of dat ze het proces op een bepaald moment beëindigen. De voortgang door een reeks stappen in een webtoepassing staat bekend als een *trechter.* U Azure Application Insights Funnels gebruiken om inzicht te krijgen in uw gebruikers en stapsgewijze conversiepercentages te controleren. 

## <a name="create-your-funnel"></a>Uw trechter maken
Voordat u uw trechter maakt, bepaalt u de vraag die u wilt beantwoorden. U wilt bijvoorbeeld weten hoeveel gebruikers de startpagina bekijken, een klantprofiel bekijken en een ticket maken. In dit voorbeeld willen de eigenaren van het bedrijf Fabrikam Fiber weten hoeveel klanten met succes een klantticket hebben gemaakt.

Hier volgen de stappen die ze nemen om hun trechter te maken.

1. **Selecteer**Nieuw in het gereedschap Trechters van Toepassingsinzichten .
1. Selecteer Laatst **90 dagen**in het vervolgkeuzemenu **Tijdbereik** . Selecteer **Mijn trechters** of **Gedeelde trechters**.
1. Selecteer **Index**in de vervolgkeuzelijst **Stap 1** . 
1. Selecteer **Klant**in de lijst **stap 2** .
1. Selecteer **in** de lijst Stap 3 de optie **Maken**.
1. Voeg een naam toe aan de trechter en selecteer **Opslaan**.

De volgende schermafbeelding toont een voorbeeld van het soort gegevens dat het gereedschap Trechters genereert. De Fabrikam eigenaren kunnen zien dat in de afgelopen 90 dagen, 54,3 procent van hun klanten die de startpagina bezocht een klantticket gemaakt. Ze kunnen ook zien dat 2.700 van hun klanten kwam naar de index van de startpagina. Dit kan duiden op een vernieuwingsprobleem.


![Schermafbeelding van het gereedschap Trechters met gegevens](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Trechtersfuncties
De vorige schermafbeelding bevat vijf gemarkeerde gebieden. Dit zijn kenmerken van trechters. In de volgende lijst wordt meer uitgelegd over elk corresponderend gebied in de schermafbeelding:
1. Als uw app wordt bemonsterd, ziet u een voorbeeldbanner. Als u de banner selecteert, wordt een contextvenster geopend waarin wordt uitgelegd hoe u de bemonstering uitschakelen. 
2. U uw trechter exporteren naar [Power BI.](../../azure-monitor/app/export-power-bi.md )
3. Selecteer een stap om meer details aan de rechterkant te zien. 
4. De historische conversiegrafiek toont de conversiepercentages van de afgelopen 90 dagen. 
5. Begrijp uw gebruikers beter door toegang te krijgen tot de gebruikerstool. U filters in elke stap gebruiken. 

## <a name="next-steps"></a>Volgende stappen
  * [Overzicht van gebruik](usage-overview.md)
  * [Gebruikers, sessies en gebeurtenissen](usage-segmentation.md)
  * [Retentie](usage-retention.md)
  * [Werkmappen](../../azure-monitor/app/usage-workbooks.md)
  * [Gebruikerscontext toevoegen](usage-send-user-context.md)
  * [Exporteren naar Power BI](../../azure-monitor/app/export-power-bi.md )

