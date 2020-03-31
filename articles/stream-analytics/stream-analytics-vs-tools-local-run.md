---
title: Azure Stream Analytics-query's lokaal testen in Visual Studio
description: In dit artikel wordt beschreven hoe u query's lokaal testen met Azure Stream Analytics Tools voor Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 34c8555356d5c0142d7b677c8119fe66806ab064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834902"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Query's van Stream Analytics lokaal testen met Visual Studio

U Azure Stream Analytics-hulpprogramma's voor Visual Studio gebruiken om uw Stream Analytics-taken lokaal te testen met voorbeeldgegevens of [livegegevens.](stream-analytics-live-data-local-testing.md) 

Gebruik deze [Quickstart](stream-analytics-quick-create-vs.md) om te leren hoe u een Stream Analytics-taak maakt met Visual Studio.

## <a name="test-your-query"></a>De query testen

Dubbelklik in uw Azure Stream Analytics-project op **Script.asaql** om het script in de editor te openen. U de query compileren om te zien of er syntaxisfouten zijn. De queryeditor ondersteunt IntelliSense, syntaxiskleuring en een foutmarkering.

![Query-editor](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Lokale invoer toevoegen

Als u uw query wilt valideren aan de hand van lokale statische gegevens, klikt u met de rechtermuisknop op de invoer en selecteert u **Lokale invoer toevoegen**.
   
![Lokale invoer toevoegen](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Selecteer in het pop-upvenster voorbeeldgegevens van uw lokale pad en **Sla op**.
   
![Lokale invoer toevoegen](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Een bestand met de naam **local_EntryStream.json** wordt automatisch toegevoegd aan uw invoermap.
   
![Bestandslijst met lokale invoermappen](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Selecteer **Lokaal uitvoeren** in de queryeditor. Of je drukt op F5.
   
![Lokaal uitvoeren](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
De uitvoer kan rechtstreeks vanuit Visual Studio in een tabelindeling worden bekeken.

![Uitvoer in tabelindeling](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

U het uitvoerpad vinden vanaf de uitgang van de console. Druk op een toets om de resultaatmap te openen.
   
![Lokaal uitvoeren](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Controleer de resultaten in de lokale map.
   
![Resultaat lokale map](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Voorbeeldinvoer
U ook voorbeeldinvoergegevens uit uw invoerbronnen verzamelen naar een lokaal bestand. Klik met de rechtermuisknop op het invoerconfiguratiebestand en selecteer **Voorbeeldgegevens**. 

![Voorbeeldgegevens](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

U alleen gegevensstreamen van Gebeurtenishubs of IoT-hubs bekijken. Andere invoerbronnen worden niet ondersteund. Vul in het pop-updialoogvenster het lokale pad in om de voorbeeldgegevens op te slaan en selecteer **Voorbeeld**.

![Voorbeeldgegevensconfiguratie](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
U de voortgang zien in het venster **Uitvoer.** 

![Voorbeeldgegevensuitvoer](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: een functie Stream Analytics maken met Visual Studio](stream-analytics-quick-create-vs.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)
* [Live gegevens lokaal testen met Azure Stream Analytics-hulpprogramma's voor Visual Studio (Preview)](stream-analytics-live-data-local-testing.md)
* [Zelfstudie: Een Azure Stream Analytics-taak implementeren met CI/CD met Azure DevOps](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Continue integratie en ontwikkeling met Stream Analytics-hulpprogramma’s](stream-analytics-tools-for-visual-studio-cicd.md)
