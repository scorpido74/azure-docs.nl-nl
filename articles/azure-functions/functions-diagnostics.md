---
title: Overzicht van Azure Functions diagnostische gegevens
description: Meer informatie over hoe u problemen met uw functie-app kunt oplossen met Azure Functions diagnostische gegevens.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 29f90508bf0bdd77f02d78cbcba8fdfe7009f0d2
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562048"
---
# <a name="azure-functions-diagnostics-overview"></a>Overzicht van Azure Functions diagnostische gegevens

Wanneer u een functie-app uitvoert, wilt u voor bereid zijn op eventuele problemen die zich kunnen voordoen, van 4xx-fouten om fouten te activeren. Azure Functions Diagnostics is een intelligente en interactieve ervaring om u te helpen bij het oplossen van problemen met uw functie-app zonder configuratie of extra kosten. Wanneer u problemen ondervindt met uw functie-app, Azure Functions diagnostische gegevens naar aanleiding van een probleem met de juiste informatie om snel en eenvoudig problemen op te lossen en het probleem op te lossen. In dit artikel leert u de basis beginselen van het gebruik van Azure Functions diagnostische gegevens voor het sneller vaststellen en oplossen van veelvoorkomende problemen met de functie-app.

## <a name="start-azure-functions-diagnostics"></a>Azure Functions diagnostische gegevens starten

Om toegang te krijgen tot Azure Functions diagnostische gegevens:

1. Navigeer naar uw functie-app in het [Azure Portal](https://portal.azure.com).
2. Selecteer het tabblad **platform functies** .
3. Selecteer **problemen vaststellen en oplossen** onder **resource beheer**, waarmee Azure functions diagnostische gegevens worden geopend.
4. Kies een categorie die het beste het probleem van uw functie-app beschrijft met behulp van de tref woorden in de tegel start pagina. U kunt ook een tref woord typen dat uw probleem het beste beschrijft in de zoek balk. U kunt bijvoorbeeld `execution` typen om een lijst met diagnostische rapporten te bekijken die betrekking hebben op de uitvoering van uw functie-app en deze rechtstreeks vanuit de start pagina te openen.

![Startpagina](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>De interactieve interface gebruiken

Wanneer u een startpagina categorie selecteert die het beste past bij het probleem van uw functie-app, kan Azure Functions diagnostische gegevens van de interactieve interface, genie, u helpen bij het vaststellen en oplossen van het probleem van uw app. U kunt de snelkoppelingen voor tegels in genie gebruiken om het volledige diagnostische rapport te bekijken van de probleem categorie waarin u geïnteresseerd bent. De snelkoppelingen voor tegels bieden u een directe manier om toegang te krijgen tot uw diagnostische gegevens.

![Genie](./media/functions-diagnostics/genie.png)

Nadat u een tegel hebt geselecteerd, ziet u een lijst met onderwerpen die betrekking hebben op het probleem dat wordt beschreven op de tegel. Deze onderwerpen bevatten fragmenten van belang rijke informatie uit het volledige rapport. U kunt een van deze onderwerpen selecteren om de problemen verder te onderzoeken. U kunt ook **volledig rapport weer geven** selecteren om alle onderwerpen op één pagina te verkennen.

![Voor beeld van diagnostisch rapport](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Een diagnostisch rapport weer geven

Nadat u een onderwerp hebt gekozen, kunt u een diagnostisch rapport bekijken dat specifiek is voor uw functie-app. Diagnostische rapporten gebruiken status pictogrammen om aan te geven of er specifieke problemen zijn met uw app. U ziet een gedetailleerde beschrijving van het probleem, aanbevolen acties, gerelateerde metrische gegevens en nuttige docs. Aangepaste diagnostische rapporten worden gegenereerd op basis van een reeks controles die worden uitgevoerd in uw functie-app. Diagnostische rapporten kunnen een nuttig hulp middel zijn voor het lokaliseren van problemen in de functie-app en het verhelpen van het probleem.

## <a name="find-the-problem-code"></a>De probleem code zoeken 

Voor functies op basis van een script kunt u **functie-uitvoering** en **fouten** onder **functie-app omlaag of fouten rapporteren** om te beperken op de regel code die uitzonde ringen of fouten veroorzaakt. Deze functie kan een nuttig hulp middel zijn voor het verkrijgen van de hoofd oorzaak en het oplossen van problemen met een specifieke regel code. Deze optie is niet beschikbaar voor vooraf C# gecompileerde en Java-functies.

![Diagnostisch rapport over fouten bij de uitvoering van functies](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Functie-uitzonde ring](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Volgende stappen

U kunt vragen stellen of feedback geven over Azure Functions diagnostische gegevens op [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Neem `[Diag]` op in de titel van uw feedback.

> [!div class="nextstepaction"]
> [Uw functie-apps bewaken](functions-monitoring.md)
