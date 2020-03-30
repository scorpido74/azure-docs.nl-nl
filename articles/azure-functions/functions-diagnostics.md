---
title: Overzicht van diagnostische gegevens over Azure Functions
description: Meer informatie over hoe u problemen met uw functie-app oplossen met azure-functiesdiagnostiek.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834039"
---
# <a name="azure-functions-diagnostics-overview"></a>Overzicht van diagnostische gegevens over Azure Functions

Wanneer u een functie-app uitvoert, wilt u voorbereid zijn op eventuele problemen die zich kunnen voordoen, van 4xx-fouten om fouten te activeren. Azure Functions diagnostics is een intelligente en interactieve ervaring om u te helpen problemen op te lossen met uw functie-app zonder configuratie of extra kosten. Wanneer u problemen ondervindt met uw functie-app, geeft Azure Functions-diagnose aan wat er mis is om u naar de juiste informatie te leiden om het probleem gemakkelijker en sneller op te lossen en op te lossen. In dit artikel ziet u de basisprincipes van het gebruik van Azure Functions-diagnose om problemen met de functie-app sneller te diagnosticeren en op te lossen.

## <a name="start-azure-functions-diagnostics"></a>Diagnose van Azure-functies starten

Ga als toegangsvoor azure-functies over:

1. Navigeer naar uw functie-app in de [Azure-portal.](https://portal.azure.com)
2. Selecteer het tabblad **Platformfuncties.**
3. Selecteer **Diagnose stellen en problemen oplossen** onder **Resourcebeheer**, waarmee Azure Functions-diagnose wordt geopend.
4. Kies een categorie die het probleem van uw functie-app het beste beschrijft met behulp van de trefwoorden in de tegel op de startpagina. U ook een trefwoord typen dat uw probleem het beste beschrijft in de zoekbalk. U bijvoorbeeld `execution` typen om een lijst met diagnostische rapporten met betrekking tot de uitvoering van uw functie-app te zien en deze rechtstreeks vanaf de startpagina te openen.

![Startpagina](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>De interactieve interface gebruiken

Zodra u een categorie op de startpagina hebt geselecteerd die het beste aansluit bij het probleem van uw functie-app, kan de interactieve interface van Azure Functions diagnostics, Genie, u begeleiden bij het diagnosticeren en oplossen van het probleem van uw app. U de tegelsneltoetsen van Genie gebruiken om het volledige diagnostische rapport van de probleemcategorie waarin u geïnteresseerd bent te bekijken. De tegelsneltoetsen bieden u een directe manier om toegang te krijgen tot uw diagnostische statistieken.

![Genie (Genie)](./media/functions-diagnostics/genie.png)

Nadat u een tegel hebt geselecteerd, ziet u een lijst met onderwerpen die verband houden met het probleem dat in de tegel wordt beschreven. Deze onderwerpen bieden fragmenten van opmerkelijke informatie uit het volledige rapport. U een van deze onderwerpen selecteren om de problemen verder te onderzoeken. U ook **Volledig rapport weergeven** selecteren om alle onderwerpen op één pagina te verkennen.

![Voorbeeld van diagnostisch rapport](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Een diagnostisch rapport weergeven

Nadat u een onderwerp hebt gekozen, u een diagnostisch rapport bekijken dat specifiek is voor uw functie-app. Diagnostische rapporten gebruiken statuspictogrammen om aan te geven of er specifieke problemen zijn met uw app. U ziet een gedetailleerde beschrijving van het probleem, aanbevolen acties, gerelateerde statistieken en nuttige documenten. Aangepaste diagnostische rapporten worden gegenereerd uit een reeks controles die worden uitgevoerd op uw functie-app. Diagnostische rapporten kunnen een handig hulpmiddel zijn om problemen in uw functie-app te lokaliseren en u te begeleiden bij het oplossen van het probleem.

## <a name="find-the-problem-code"></a>De probleemcode zoeken

Voor op scripts gebaseerde functies u **Functie-uitvoering en -fouten** gebruiken onder **Functie-app Omlaag of Fouten melden** om de coderegel te beperken die uitzonderingen of fouten veroorzaakt. Deze functie kan een handig hulpmiddel zijn om naar de hoofdoorzaak te gaan en problemen op te lossen van een specifieke coderegel. Deze optie is niet beschikbaar voor vooraf gecompileerde C#- en Java-functies.

![Diagnostisch rapport over foutievan functie-uitvoering](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Functie-uitzondering](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Volgende stappen

U vragen stellen of feedback geven over azure-functies diagnostiek bij [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Gelieve `[Diag]` op te nemen in de titel van uw feedback.

> [!div class="nextstepaction"]
> [Uw functie-apps controleren](functions-monitoring.md)
