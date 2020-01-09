---
title: Azure Monitor logboek processen automatiseren met Microsoft Flow
description: Meer informatie over hoe u Microsoft Flow kunt gebruiken om Herhaal bare processen snel te automatiseren met behulp van de Azure Log Analytics-connector.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/29/2017
ms.openlocfilehash: 3c1df04e96aff55ae5b9f7130b1593638e3ea1b3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75395069"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Azure Monitor logboek processen automatiseren met de connector voor Microsoft Flow
Met [Microsoft flow](https://ms.flow.microsoft.com) kunt u geautomatiseerde werk stromen maken met honderden acties voor diverse services. Uitvoer van de ene actie kan worden gebruikt als invoer voor een andere, zodat u integratie tussen verschillende services kunt maken.  Met de Azure Log Analytics-connector voor Microsoft Flow kunt u werk stromen bouwen met gegevens die zijn opgehaald door logboek query's van een Log Analytics-werk ruimte in Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

U kunt bijvoorbeeld Microsoft Flow gebruiken om Azure Monitor logboek gegevens te gebruiken in een e-mail melding van Office 365, een bug te maken in azure DevOps of een bericht over een toegestane vertraging te plaatsen.  U kunt een werk stroom activeren op basis van een eenvoudig schema of van een actie in een verbonden service, zoals wanneer een e-mail of een tweet wordt ontvangen.  

In de zelf studie in dit artikel leest u hoe u een stroom maakt waarmee automatisch de resultaten van een Azure Monitor logboek query per e-mail worden verzonden, maar één voor beeld van hoe u de Log Analytics-connector in Microsoft Flow kunt gebruiken. 


## <a name="step-1-create-a-flow"></a>Stap 1: een stroom maken
1. Meld u aan bij [Microsoft flow](https://flow.microsoft.com)en selecteer **mijn stromen**.
2. Klik op **+ leeg item maken**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Stap 2: een trigger maken voor uw stroom
1. Klik op **honderden connectors en triggers zoeken**.
2. Typ **schema** in het zoekvak.
3. Selecteer **planning**en selecteer vervolgens **schema-terugkeer patroon**.
4. In het vak **frequentie** selecteert u **dag** en voert u in het vak **interval** **1**in.<br><br>![Microsoft Flow trigger dialoog venster](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Stap 3: een Log Analytics actie toevoegen
1. Klik op **+ Nieuwe stap** en selecteer **Een actie toevoegen**.
2. Zoeken naar **log Analytics**.
3. Klik op **Azure log Analytics: Query's uitvoeren en resultaten visualiseren**.<br><br>![query venster Log Analytics uitvoeren](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Stap 4: de Log Analytics actie configureren

1. Geef de details op voor uw werk ruimte, met inbegrip van de abonnements-ID, resource groep en werkruimte naam.
2. Voeg de volgende logboek query toe aan het **query** -venster.  Dit is alleen een voorbeeld query en u kunt vervangen door een andere waarde die gegevens retourneert.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. Selecteer de **HTML-tabel** voor het **grafiek type**.<br><br>![Log Analytics actie](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Stap 5: de stroom configureren voor het verzenden van e-mail

1. Klik op **nieuwe stap**en vervolgens op **+ een actie toevoegen**.
2. Zoek naar **Office 365 Outlook**.
3. Klik op **Office 365 Outlook – een E-mail verzenden**.<br><br>![Office 365](media/flow-tutorial/flow04.png) selectie venster van Outlook

4. Geef het e-mail adres van een ontvanger op in het venster **aan** en een onderwerp voor het e-mail bericht in het **onderwerp**.
5. Klik op een wille keurige plaats in het vak **hoofd tekst** .  Een venster met **dynamische inhoud** wordt geopend met waarden van eerdere acties.  
6. Selecteer **hoofd tekst**.  Dit zijn de resultaten van de query in de Log Analytics actie.
6. Klik op **Geavanceerde opties weer geven**.
7. Selecteer in het vak **is HTML** de optie **Ja**.<br><br>![Office 365-e-mail configuratie venster](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Stap 6: uw stroom opslaan en testen
1. Voeg in het vak **stroom naam** een naam voor de stroom toe en klik vervolgens op **stroom maken**.<br><br>stroom](media/flow-tutorial/flow06.png) ![opslaan
2. De stroom wordt nu gemaakt en wordt uitgevoerd na een dag die het schema is dat u hebt opgegeven. 
3. Als u de stroom onmiddellijk wilt testen, klikt u op **nu uitvoeren** en vervolgens op **stroom uitvoeren**.<br><br>stroom](media/flow-tutorial/flow07.png) ![uitvoeren
3. Wanneer de stroom is voltooid, controleert u het e-mail adres van de ontvanger die u hebt opgegeven.  U moet een e-mail bericht hebben ontvangen met een hoofd tekst die er ongeveer als volgt uitziet:<br><br>![Voor beeld-e-mail](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logboek query's vindt u in azure monitor](../log-query/log-query-overview.md).
- Meer informatie over [Microsoft flow](https://ms.flow.microsoft.com).



