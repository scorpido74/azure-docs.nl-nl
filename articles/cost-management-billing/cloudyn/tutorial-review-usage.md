---
title: 'Zelfstudie: Gebruik en kosten controleren met Cloudyn in Azure | Microsoft Docs'
description: In deze zelfstudie bekijkt u gebruik en kosten om trends bij te houden, inefficiëntie te detecteren en waarschuwingen te maken.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.custom: seodec18
ms.reviewer: benshy
ms.openlocfilehash: a2d4525b0b29feb230f5496196a84e651e651e52
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76774031"
---
<!-- Intent: As a cloud-consuming user, I need to view usage and costs for my cloud resources and services.
-->

# <a name="tutorial-review-usage-and-costs"></a>Zelfstudie: Gebruik en kosten bekijken

Cloudyn geeft gebruik en kosten weer, zodat u trends kunt bijhouden, inefficiëntie kunt detecteren en waarschuwingen kunt maken. Alle gegevens over gebruik en kosten worden weergegeven in Cloudyn-dashboards en -rapporten. Aan de hand van de voorbeelden in deze zelfstudie bekijkt u het gebruik en de kosten met behulp van dashboards en rapporten.

Azure Cost Management biedt vergelijkbare functionaliteit als Cloudyn. Azure Cost Management is een systeemeigen Azure-oplossing voor kostenbeheer. Het helpt u kosten te analyseren, budgetten te maken en beheren, gegevens te exporteren en aanbevelingen voor optimalisatie te bekijken en er actie op te ondernemen om geld te besparen. Zie [Azure Cost Management](../cost-management-billing-overview.md) voor meer informatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gebruiks- en kostentrends bijhouden
> * Inefficiënt gebruik detecteren
> * Waarschuwingen maken voor ongebruikelijke uitgaven of te hoge uitgaven
> * Gegevens exporteren

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- U moet een Azure-account hebben.
- U moet een proefregistratie of een betaald abonnement voor Cloudyn hebben.

## <a name="open-the-cloudyn-portal"></a>De Cloudyn-portal openen

U bekijkt het gebruik en de kosten in de Cloudyn-portal. Open de Cloudyn-portal vanuit Azure Portal of ga naar https://azure.cloudyn.com en meld u aan.

## <a name="track-usage-and-cost-trends"></a>Gebruiks- en kostentrends bijhouden

U houdt de werkelijke uitgaven voor gebruik en kosten bij met rapporten om trends in de loop van de tijd te identificeren. Als u trends wilt bekijken, gebruikt u het rapport Actual Cost Over Time (Werkelijke kosten in de loop van de tijd). Klik linksboven in de portal op **Costs** > **Cost Analysis** > **Actual Cost Over Time**. Wanneer u het rapport voor het eerst opent, zijn er geen groepen of filters op toegepast.

Hier volgt een voorbeeldrapport:

![Voorbeeld: rapport Werkelijke kosten in de loop der tijd](./media/tutorial-review-usage/actual-cost01.png)

Het rapport toont alle uitgaven gedurende de afgelopen 30 dagen. Als u alleen uitgaven voor Azure-services wilt weergeven, past u de groep Service toe en filtert u op alle Azure-services. In de volgende afbeelding ziet u de gefilterde services.

![Voorbeeld met gefilterde Azure-services](./media/tutorial-review-usage/actual-cost02.png)

In het voorgaande voorbeeld is vanaf 29/10/2018 minder geld uitgegeven. Te veel kolommen kunnen een duidelijke trend echter moeilijk zichtbaar maken. U kunt de rapportweergave wijzigen in een lijn- of vlakdiagram om de gegevens in andere weergaven te zien. In de volgende afbeelding wordt de trend duidelijker weergegeven.

![Voorbeeld met een afnemende kostentrend voor Azure-VM](./media/tutorial-review-usage/actual-cost03.png)

In het voorbeeld ziet u dat de kosten voor Azure-VM zijn gedaald. Kosten voor andere Azure-services begonnen op deze dag ook te dalen. Wat veroorzaakte deze verlaging in de uitgaven? In dit voorbeeld is een groot werkproject voltooid, waardoor het verbruik van veel Azure-services ook is gedaald.

Zie [Analyzing your cloud billing data vs. time with Cloudyn](https://youtu.be/7LsVPHglM0g) als u een zelfstudievideo wilt bekijken over het bijhouden van gebruiks- en kostentrends.

## <a name="detect-usage-inefficiencies"></a>Inefficiënt gebruik detecteren

Optimalisatierapporten verbeteren de efficiëntie, optimaliseren het gebruik en vinden manieren om geld te besparen dat u uitgeeft aan uw cloudresources. Ze zijn vooral handig voor kosteneffectieve aanbevelingen betreffende afmetingen om zo niet-actieve of dure VM's te helpen verminderen.

Een veelvoorkomend probleem waarmee organisaties te maken krijgen wanneer ze resources naar de cloud verplaatsen, is hun virtualisatiestrategie. Ze hanteren vaak een benadering die vergelijkbaar is met de benadering die ze hanteren voor het maken van virtuele machines voor de on-premises virtualisatieomgeving. En ze gaan ervan uit dat de kosten afnemen wanneer ze hun lokale VM's ongewijzigd naar de cloud verplaatsen. Deze aanpak levert echter waarschijnlijk geen kostenbesparing op.

Het probleem is dat al voor de bestaande infrastructuur is betaald. Als gebruikers dat willen, kunnen ze grote VM's maken en houden, die al dan niet actief zijn, waarbij dit weinig gevolgen heeft. Als grote of niet-actieve VM's naar de cloud worden verplaatst, zullen de kosten echter waarschijnlijk *toenemen*. Kostentoewijzing voor resources is belangrijk wanneer u een overeenkomst sluit met een cloudserviceprovider. U moet betalen voor datgene waartoe u zich verplicht, of u de resource nu volledig gebruikt of helemaal niet.

Het rapport Cost Effective Sizing Recommendations (Aanbevelingen voor kosteneffectieve aanpassingen van de afmetingen) identificeert jaarlijkse besparingen door de capaciteit van VM-exemplaren te vergelijken met historische gegevens over hun CPU- en geheugengebruik.  

Klik op het menu bovenaan in de portal en klik op **Optimizer** > **Sizing Optimization** > **Cost Effective Sizing Recommendations**. Pas, indien nuttig, een filter toe om het aantal resultaten te verkleinen. Hier ziet u een voorbeeldafbeelding.

![Rapport Aanbeveling voor kosten-effectieve aanpassing voor virtuele Azure-machines](./media/tutorial-review-usage/sizing01.png)

In dit voorbeeld kan $ 2.382 worden bespaard door de aanbevelingen voor het wijzigen van de VM-exemplaartypen op te volgen. Klik op het plusteken (+) onder **Details** voor de eerste aanbeveling. Hier vindt u meer informatie over de eerste aanbeveling.

![Voorbeeld met details van de aanbeveling](./media/tutorial-review-usage/sizing02.png)

Bekijk de id's van de VM-exemplaren door te klikken op het plusteken naast **List of Candidates**.

![Voorbeeld met een lijst met VM-kandidaten waarvan het formaat kan worden aangepast](./media/tutorial-review-usage/sizing03.png)

Zie [Optimizing VM Size in Cloudyn](https://youtu.be/1xaZBNmV704) als u een zelfstudievideo wilt bekijken over het detecteren van inefficiënt gebruik.

Azure Cost Management biedt ook aanbevelingen over kostenbesparingen voor Azure-services. Zie [Zelfstudie: kosten optimaliseren op basis van aanbevelingen](../costs/tutorial-acm-opt-recommendations.md) voor meer informatie.

## <a name="create-alerts-for-unusual-spending"></a>Waarschuwingen maken voor ongebruikelijke uitgaven

Met waarschuwingen kunt u ook andere belanghebbenden automatisch op de hoogte stellen van afwijkende bestedingen of kans op budgetoverschrijdingen. U kunt waarschuwingen maken met behulp van rapporten die waarschuwingen op basis van budget en kostendrempels ondersteunen.

In dit voorbeeld wordt het rapport **Actual Cost Over Time** (Werkelijke kosten in de loop van de tijd) gebruikt om u een waarschuwing te sturen wanneer uw uitgaven voor een Azure-VM het totale budget naderen. In dit scenario hebt u een totaalbudget van $ 20.000. U wilt een waarschuwing ontvangen wanneer bij $ 9000 de kosten de helft van het budget beginnen te naderen en een extra waarschuwing wanneer het bedrag van $ 10.000 wordt bereikt.

1. Selecteer in het menu boven aan Cloudyn-portal de optie **Costs** > **Cost Analysis** > **Actual Cost Over Time**.
2. Stel **Groups** in op **Service** en stel **Filter on the service** in op **Azure/VM**.
3. Selecteer rechtsboven in het rapport de optie **Actions** en selecteer **Schedule report**.
4. Als u zelf op vaste tijden een e-mail met het rapport wilt ontvangen, selecteert u het tabblad **Scheduling** in het dialoogvenster **Save or Schedule this**. Selecteer **Send via email**. Alle labels, groepen en filters die u gebruikt, zijn opgenomen het rapport dat u per e-mail wordt toegezonden.
5. Selecteer het tabblad **Threshold** en selecteer **Actual Cost vs. Threshold**.
   1. In het vak voor de drempelwaarde **Red alert** voert u 10000 in.
   2. In het vak voor de drempelwaarde **Yellow alert** voert u 9000 in.
   3. In het vak **Number of consecutive alerts** voert u het aantal opeenvolgende te ontvangen waarschuwingen in. Wanneer u het totaal aantal waarschuwingen hebt ontvangen dat u hebt opgegeven, worden er geen extra waarschuwingen meer verzonden.
6. Selecteer **Opslaan**.

![Voorbeeld met rode en gele waarschuwingen op basis van uitgavedrempelwaarden](./media/tutorial-review-usage/schedule-alert01.png)

U kunt ook de drempeloptie **Cost Percentage vs. Budget** kiezen om waarschuwingen te maken. Hiermee kunt u de drempels opgeven als percentage van uw budget in plaats van bedragen.

## <a name="export-data"></a>Gegevens exporteren

U kunt vanuit elk rapport gegevens exporteren op dezelfde manier waarop u waarschuwingen voor rapporten maakt. Misschien wilt u, bijvoorbeeld, een lijst met Cloudyn-accounts of andere gebruikersgegevens exporteren. Als u een rapport wilt exporteren, opent u het rapport en klikt u vervolgens in de rechterbovenhoek van het rapport op **Actions**. Mogelijk wilt u **Export all report data** uitvoeren zodat u de gegevens kunt downloaden of afdrukken. Of u kunt **Schedule report** selecteren om te plannen dat het rapport per e-mail wordt verzonden.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Gebruiks- en kostentrends bijhouden
> * Inefficiënt gebruik detecteren
> * Waarschuwingen maken voor ongebruikelijke uitgaven of te hoge uitgaven
> * Gegevens exporteren


Ga naar de volgende zelfstudie als u wilt weten hoe u uitgaven kunt voorspellen aan de hand van historische gegevens.

> [!div class="nextstepaction"]
> [Toekomstige uitgaven voorspellen](../../cost-management/tutorial-forecast-spending.md)
