---
title: Informatie over metrische gegevens voor Azure lente-Cloud
description: Meer informatie over het controleren van metrische gegevens in de Azure lente-Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 952dfc1c707df3b7fa61443ea6ea18630352f0dc
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607811"
---
# <a name="metrics-for-azure-spring-cloud"></a>Metrische gegevens voor Azure lente-Cloud

Azure Monitor Metrics Explorer is een onderdeel van de Microsoft Azure-portal waarmee grafieken kunnen worden getekend, trends visueel worden gecorreleerd en pieken en spannings dips in metrische gegevens worden onderzocht. Gebruik metrische gegevens Verkenner om de status en het gebruik van uw resources te onderzoeken. In azure lente-Cloud bieden we twee opties voor het weer geven van metrische gegevens: grafieken op de **overzichts** pagina van de toepassing en de pagina metrische gegevens van service niveau.

## <a name="application-overview-page"></a>Pagina Toepassings overzicht

De **overzichts** pagina van elke toepassing bevat een grafiek met metrische gegevens waarmee u een snelle status controle van uw toepassing kunt uitvoeren.  Ga naar de pagina Azure lente-Cloud service en selecteer **toepassings dashboard**en selecteer vervolgens een toepassing in de lijst.  

We bieden 5 grafieken met metrische gegevens die elke minuut worden bijgewerkt voor het volgende:

* **Http-server fouten**: aantal fouten voor HTTP-aanvragen aan uw app.
* **Gegevens in**: bytes ontvangen door uw app.
* **Gegevens uit**: verzonden bytes naar uw app.
* **Aanvragen**: aanvragen ontvangen door uw app.
* **Gemiddelde reactie tijd**: gemiddelde reactie tijd van uw app.

U kunt een tijds bereik voor de grafiek selecteren tussen 1 uur en Maxi maal 7 dagen.

## <a name="service-level-metric-queries"></a>Metrische query's op service niveau

Met Azure lente Cloud kunt u diverse metrische gegevens van toepassingen bewaken. Raadpleeg de [hand leiding om aan de slag te](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) gaan met Azure monitor metrische gegevens voor meer informatie over deze service.

Als u metrische gegevens wilt bekijken, selecteert u uw metriek, uw **aggregatie**en uw tijds bereik.  Deze concepten worden hieronder uitgelegd.

### <a name="aggregation"></a>Aggregatie 

De metrische gegevens van Azure worden elke minuut gepeild en bijgewerkt. Azure biedt drie manieren om gegevens te verzamelen voor een gekozen tijds periode:

* **Totaal**: som alle metrische gegevens als doel uitvoer.
* **Gemiddelde**: gebruik de gemiddelde waarde in de periode als doel uitvoer.
* **Maximum/min**: gebruik de waarde max/min in de periode als doel uitvoer.

### <a name="time-range"></a>Tijdsbereik

Selecteer een standaard periode of Definieer uw eigen tijds bereik.

### <a name="modifying-the-granularity-of-your-metric-query"></a>De granulatie van uw metrische query wijzigen

Standaard verzamelt Azure metrische gegevens voor alle Azure lente-Cloud service toepassingen. Als u metrische gegevens wilt bekijken op het niveau van de toepassing of het exemplaar, gebruikt u de functie filter.  
Selecteer **filter toevoegen**, stel de eigenschap in op **app** en selecteer de doel toepassing die u wilt bewaken. Gebruik eventueel de optie **splitsen Toep assen** om afzonderlijke regels voor elke app in de grafiek te tekenen.

>[!TIP]
> U kunt uw eigen grafieken maken op de pagina metrische gegevens en deze vastmaken aan uw **dash board**. Maak eerst een naam voor de grafiek.  Selecteer vervolgens **vastmaken aan dash board in de rechter bovenhoek**. U kunt nu uw toepassing op het **dash board**van de portal controleren.