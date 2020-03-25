---
title: Cloudyn-budgetten beheren in Azure
description: Dit artikel helpt u om snel budgetten te maken en deze te beheren in Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: vitavor
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: f2f8fdbf2b470e9f1eb0d82c4c6de230c2a9e78f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79464152"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Azure-budgetten beheren met Cloudyn

U kunt budgetten en budgetwaarschuwingen instellen om uw cloudgovernance en verantwoordelijkheid te verbeteren. Dit artikel helpt u om snel budgetten te maken en deze te beheren in Cloudyn.

Wanneer u een Enterprise- of MSP-account hebt, kunt u uw hiërarchische structuur van kostenentiteiten gebruiken om maandelijkse budgetquota toe te wijzen aan verschillende bedrijfseenheden, afdelingen of een andere kostenentiteit. Wanneer u een Premium-account hebt, kunt u de functionaliteit voor budgetbeheer gebruiken, die vervolgens wordt toegepast op de totale clouduitgaven. Alle budgetten worden handmatig toegewezen.

Op basis van toegewezen budgetten kunt u drempelwaarschuwingen instellen afhankelijk van het percentage van uw budget dat is verbruikt en de ernst van elke drempelwaarde definiëren.

Budgetrapporten laten het toegewezen budget zien. Gebruikers kunnen zien wanneer hun uitgaven hoger, lager of gelijk zijn dan hun verbruik in de loop van de tijd. Wanneer u bovenaan een budgetrapport **Velden weergeven/verbergen** selecteert, kunt u ervoor kiezen om de kosten, het budget, de samengevoegde kosten of het totale budget weer te geven.

Azure Cost Management biedt vergelijkbare functionaliteit als Cloudyn. Azure Cost Management is een systeemeigen Azure-oplossing voor kostenbeheer. Het helpt u kosten te analyseren, budgetten te maken en beheren, gegevens te exporteren en aanbevelingen voor optimalisatie te bekijken en er actie op te ondernemen om geld te besparen. Zie [Budgetten maken en beheren](../costs/tutorial-acm-create-budgets.md)voor meer informatie over budgetten in Cost Management.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="create-budgets"></a>Budgetten maken

Wanneer u een budget maakt, stelt u dit in voor het boekjaar en is het van toepassing op een specifieke entiteit.

Een budget maken en toewijzen aan een entiteit:

1. Ga naar **Costs** &gt; **Cost Management** &gt; **Budget**.
2. Selecteer op de pagina Budget Management, onder **Entities**, de entiteit waarvoor u het budget wilt maken.
3. Selecteer in het budgetjaar het jaar waarvoor u het budget wilt maken.
4. Stel voor elke maand een budgetwaarde in. Wanneer u klaar bent, klikt u op **Save**.
In dit voorbeeld is het maandelijkse budget voor juni 2018 ingesteld op $ 135.000. Het totale budget voor het jaar is $ 1.615.000,00.
![Een budgetpagina maken waar u een budget voor elke maand instelt](./media/manage-budgets/set-budget.png)


Een bestand voor het jaarlijkse budget importeren:

1. Selecteer **Export** onder **Actions** om een lege CSV-sjabloon te downloaden en deze te gebruiken als basis voor het budget.
2. Vul het CSV-bestand in met uw budgetgegevens en sla het bestand lokaal op.
3. Selecteer **Import** onder **Actions**.
4. Selecteer het opgeslagen bestand en klik vervolgens op **OK**.

Als u het voltooide budget wilt exporteren als een CSV-bestand, selecteert u **Export** onder **Actions** om het bestand te downloaden.

## <a name="view-budget-in-reports"></a>Budget in rapporten weergeven

Wanneer dit is voltooid, wordt uw budget weergegeven in de meeste kostenrapporten onder **Costs** &gt; **Cost Analysis**, en in het rapport Cost vs. Budget Over Time. U kunt rapporten ook plannen op basis van budgetdrempelwaarden met behulp van **Actions**.

Hier ziet u een voorbeeld van het rapport Cost Analysis. Het bevat het totale budget en de kosten per workload en gebruikstypen sinds het begin van het jaar.

![Voorbeeld van een rapport van Cost Analysis met budget](./media/manage-budgets/cost-analysis-budget-example.png)

In dit voorbeeld wordt ervan uitgegaan dat de huidige datum 22 juni is. De kosten voor juni 2018 zijn $ 71.611,28 vergeleken met het maandelijkse budget van $ 135.000. De kosten zijn veel lager dan het maandelijkse budget omdat er nog acht dagen te gaan zijn tot het einde van de maand.

Een andere manier om het rapport weer te geven is door te kijken naar de samengevoegde kosten in vergelijking met uw budget. Als u de samengevoegde kosten wilt zien, selecteert u onder **Show/Hide Fields** **Accumulated Cost** en **Total Budget**. Hier ziet u een voorbeeld van de samengevoegde kosten sinds het begin van het jaar.

![Voorbeeld van samengevoegde kosten en totale budget in het rapport Cost vs. Budget in de loop van de tijd](./media/manage-budgets/accumulated-budget.png)

Ergens op een moment in de toekomst bestaat de kans dat de samengevoegde kosten hoger zijn dan uw budget. U kunt dat gemakkelijker zien als u het type grafiek wijzigt in _lijn_.

![Budget in een lijndiagram in het rapport Cost by Months](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Budgetwaarschuwingen maken voor een filter

In het vorige voorbeeld kunt u zien dat de samengevoegde bijna het budget hebben bereikt. U kunt automatische budgetwaarschuwingen maken zodat u op de hoogte wordt gebracht wanneer de uitgaven het budget bijna of helemaal overschrijden. De waarschuwing is in feite een gepland rapport met een drempel. Dit zijn enkele metrische gegevens voor drempelwaarden voor budgetwaarschuwingen:

- Remaining Cost vs. Budget; om een drempel voor een valutawaarde op te geven
- Cost Percentage vs. Budget; om een drempel voor een percentagewaarde op te geven

Laten we eens naar een voorbeeld kijken.

Klik in het rapport Cost vs. Budget Over Time op **Actions** en selecteer vervolgens **Schedule report**. Selecteer op het tabblad Threshold een drempelwaarde, zoals **Cost Percentage vs Budget**. Selecteer een type waarschuwing en voer een percentagewaarde van het budget in. Als u slechts één keer een melding wilt ontvangen, selecteert u **Number of consecutive alerts** en typt u _1_. Klik op **Opslaan**.

![Een budgetwaarschuwing maken in het venster Save or Schedule this report](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Volgende stappen

- Als u de eerste zelfstudie voor Cloudyn nog niet hebt voltooid, leest u die op [Gebruik en kosten bekijken](tutorial-review-usage.md).
- Lees meer over de [rapporten die beschikbaar zijn in Cloudyn](use-reports.md).
