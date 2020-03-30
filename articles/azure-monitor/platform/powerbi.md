---
title: Azure Log Analytics-gegevens importeren in Power BI | Microsoft Documenten
description: Power BI is een cloudgebaseerde business analytics-service van Microsoft die uitgebreide visualisaties en rapporten biedt voor analyse van verschillende sets gegevens.  In dit artikel wordt beschreven hoe u Log Analytics-gegevens configureert en importeert in Power BI en deze configureert om deze automatisch te vernieuwen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2019
ms.openlocfilehash: 8ff24d508eb35c4f2a04c7d024254fa6f1875da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659280"
---
# <a name="import-azure-monitor-log-data-into-power-bi"></a>Azure Monitor-logboekgegevens importeren in Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) is een cloudgebaseerde business analytics-service van Microsoft die uitgebreide visualisaties en rapporten biedt voor analyse van verschillende sets gegevens.  U de resultaten van een Azure Monitor-logboekquery importeren in een Power BI-gegevensset, zodat u profiteren van de functies, zoals het combineren van gegevens uit verschillende bronnen en het delen van rapporten op het web en mobiele apparaten.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview"></a>Overzicht
Als u gegevens uit een [Werkruimte Logboekanalyse](manage-access.md) in Azure Monitor wilt importeren in Power BI, maakt u een gegevensset in Power BI op basis van een [logboekquery](../log-query/log-query-overview.md) in Azure Monitor.  De query wordt uitgevoerd telkens wanneer de gegevensset wordt vernieuwd.  Vervolgens u Power BI-rapporten maken die gegevens uit de gegevensset gebruiken.  Als u de gegevensset in Power BI wilt maken, exporteert u uw query van Log Analytics naar [De taal van Power Query (M).](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification)  U gebruikt deze vervolgens om een query te maken in Power BI Desktop en deze vervolgens te publiceren naar Power BI als gegevensset.  De details voor dit proces worden hieronder beschreven.

![Logboekanalyses naar Power BI](media/powerbi/overview.png)

## <a name="export-query"></a>Query exporteren
Begin met het maken van een [logboekquery](../log-query/log-query-overview.md) die de gegevens retourneert die u de Power BI-gegevensset wilt invullen.  Vervolgens exporteert u die query naar [de Taal van Power Query (M)](https://docs.microsoft.com/powerquery-m/power-query-m-language-specification) die kan worden gebruikt door Power BI Desktop.

1. [Maak de logboekquery in Log Analytics](../log-query/get-started-portal.md) om de gegevens voor uw gegevensset te extraheren.
2. Selecteer**Power BI-query (M)** **exporteren** > .  Hiermee wordt de query geëxporteerd naar een tekstbestand met de naam **PowerBIQuery.txt**. 

    ![Logboekzoeken exporteren](media/powerbi/export-analytics.png)

3. Open het tekstbestand en kopieer de inhoud ervan.

## <a name="import-query-into-power-bi-desktop"></a>Query importeren in Power BI-bureaublad
Power BI Desktop is een bureaubladtoepassing waarmee u gegevenssets en rapporten maken die kunnen worden gepubliceerd in Power BI.  U deze ook gebruiken om een query te maken met de Power Query-taal die is geëxporteerd vanuit Azure Monitor. 

1. Installeer [Power BI Desktop](https://powerbi.microsoft.com/desktop/) als u het nog niet hebt en open de toepassing.
2. Selecteer **Gegevenslege query** > **opvragen** om een nieuwe query te openen.  Selecteer vervolgens **Geavanceerde editor** en plak de inhoud van het geëxporteerde bestand in de query. Klik op **Gereed**.

    ![Power BI-bureaubladquery](media/powerbi/desktop-new-query.png)

5. De query wordt uitgevoerd en de resultaten worden weergegeven.  Mogelijk wordt u gevraagd om referenties om verbinding te maken met Azure.  
6. Typ een beschrijvende naam voor de query.  De standaardinstelling is **Query1**. Klik **op Sluiten en toepassen** om de gegevensset aan het rapport toe te voegen.

    ![Power BI-bureaubladnaam](media/powerbi/desktop-results.png)



## <a name="publish-to-power-bi"></a>Publiceren naar Power BI
Wanneer u publiceert naar Power BI, worden een gegevensset en een rapport gemaakt.  Als u een rapport maakt in Power BI Desktop, wordt dit gepubliceerd met uw gegevens.  Zo niet, dan wordt er een leeg rapport gemaakt.  U het rapport wijzigen in Power BI of een nieuw rapport maken op basis van de gegevensset.

1. Maak een rapport op basis van uw gegevens.  Gebruik [Power BI Desktop-documentatie](https://docs.microsoft.com/power-bi/desktop-report-view) als u er niet bekend mee bent.  
1. Wanneer u klaar bent om het naar Power BI te verzenden, klikt u op **Publiceren**.  
1. Selecteer desgevraagd een bestemming in uw Power BI-account.  Tenzij u een specifieke bestemming in gedachten hebt, gebruikt u **Mijn werkruimte**.

    ![Power BI-bureaublad publiceren](media/powerbi/desktop-publish.png)

1. Wanneer de publicatie is voltooid, klikt u op **Openen in Power BI** om Power BI te openen met uw nieuwe gegevensset.


### <a name="configure-scheduled-refresh"></a>Geplande vernieuwing configureren
De gegevensset die in Power BI is gemaakt, heeft dezelfde gegevens die u eerder in Power BI-bureaublad hebt gezien.  U moet de gegevensset periodiek vernieuwen om de query opnieuw uit te voeren en deze te vullen met de nieuwste gegevens van Azure Monitor.  

1. Klik op de werkruimte waar u uw rapport hebt geüpload en selecteer het menu **Gegevenssets.** 
1. Selecteer het contextmenu naast de nieuwe gegevensset en selecteer **Instellingen**. 
1. Onder **Gegevensbronreferenties** moet u een bericht hebben dat de referenties ongeldig zijn.  Dit komt omdat u nog geen referenties hebt verstrekt die de gegevensset kan gebruiken wanneer de gegevens worden vernieuwd.  
1. Klik **op Referenties bewerken** en geef referenties op met toegang tot de werkruimte Log Analytics in Azure Monitor. Als u tweestapsverificatie nodig hebt, selecteert u **OAuth2** voor de **verificatiemethode** die moet worden gevraagd om in te loggen met uw referenties.

    ![Power BI-planning](media/powerbi/powerbi-schedule.png)

5. Schakel **onder Geplande vernieuwing** de optie in om uw gegevens **up-to-date te houden.**  U optioneel de **vernieuwingsfrequentie** en een of meer specifieke tijden wijzigen om de vernieuwing uit te voeren.

    ![Power BI-vernieuwing](media/powerbi/powerbi-schedule-refresh.png)



## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboekzoekopdrachten](../log-query/log-query-overview.md) om query's te maken die naar Power BI kunnen worden geëxporteerd.
* Meer informatie over [Power BI](https://powerbi.microsoft.com) om visualisaties te maken op basis van azure monitor-logboekexport.
