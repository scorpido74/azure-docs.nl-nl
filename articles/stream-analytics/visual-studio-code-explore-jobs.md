---
title: Azure Stream Analytics-taken verkennen in Visual Studio-code
description: In dit artikel ziet u hoe u een Azure Stream Analytics-taak exporteert naar een lokaal project, taken weergeven en taakentiteiten weergeven.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479239"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Azure Stream Analytics verkennen met Visual Studio Code (Preview)

De Azure Stream Analytics for Visual Studio Code-extensie biedt ontwikkelaars een lichte ervaring voor het beheren van hun Stream Analytics-taken. Het kan worden gebruikt op Windows, Mac en Linux. Met de Azure Stream Analytics-extensie u het als nog niet doen:

- [Taken maken,](quick-create-vs-code.md)starten en stoppen
- Bestaande taken exporteren naar een lokaal project
- Taken weergeven en taakentiteiten weergeven

## <a name="export-a-job-to-a-local-project"></a>Een taak exporteren naar een lokaal project

Als u een taak wilt exporteren naar een lokaal project, zoekt u de taak die u wilt exporteren in de **Stream Analytics Explorer** in Visual Studio Code. Selecteer vervolgens een map voor uw project. Het project wordt geëxporteerd naar de map die u selecteert en u de taak blijven beheren vanuit Visual Studio Code. Zie de Visual Studio Code [snel aan](quick-create-vs-code.md)de slag voor meer informatie over het gebruik van Visual Studio Code voor het beheren van Stream Analytics-taken.

![ASA-taak exporteren in Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Taaktaak lijst en taakentiteiten weergeven

U de taakweergave gebruiken om te communiceren met Azure Stream Analytics-taken vanuit Visual Studio.


1. Klik op het **Azure-pictogram** op de activiteitsbalk van visual studiocode en vouw vervolgens **het knooppunt Stream Analytics**uit. Uw vacatures moeten onder uw abonnementen worden weergegeven.

   ![Open Stream Analytics Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Vouw uw taakknooppunt uit, u de taakquery, configuratie, invoer, uitvoer en functies openen en bekijken. 

3. Klik met de rechtermuisknop op uw taakknooppunt en kies het knooppunt **Taak openen in het** knooppunt Portal om de taakweergave in de Azure-portal te openen.

   ![Vacatureweergave openen in portal](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Stream Analytics-cloudtaak maken in Visual Studio Code (Preview)](quick-create-vs-code.md)
