---
title: Cloudyn-budgetten beheren in azure
description: Dit artikel helpt u snel budgetten maken en te beginnen met beheer van deze in Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 9d7d0e049d3c35aab56145beb94c8e41e56c5785
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219102"
---
# <a name="manage-azure-budgets-with-cloudyn"></a>Azure budgetten met Cloudyn beheren

Opzetten van budgetten en budget gebaseerde waarschuwingen helpen bij het verbeteren van uw cloud-governance en aansprakelijkheid. Dit artikel helpt u snel budgetten maken en te beginnen met beheer van deze in Cloudyn.

Wanneer u een Enterprise- of MSP-account hebt, kunt u uw hiërarchische kostenstructuur entiteit maandelijkse budget quota toewijzen aan verschillende bedrijfseenheden, afdelingen of een andere kostenentiteit. Wanneer u een Premium-account hebt, kunt u de functionaliteit voor het beheer van budget, dit vervolgens op uw uitgaven volledige in de cloud toegepast wordt. Alle budgetten worden handmatig toegewezen.

Op basis van toegewezen budgetten, kunt u drempelwaarde voor waarschuwingen op basis van uw budget dat wordt gebruikt voor het percentage instellen en definiëren van de ernst van elke drempelwaarde.

De rapporten met het toegewezen budget budget. Gebruikers kunnen bekijken als hun uitgaven boven, onder of ervoor met hun gebruik na verloop van tijd is. Wanneer u **velden weer geven/verbergen** boven aan een budget rapport selecteert, kunt u kosten, budget, gecumuleerde kosten of het totale budget weer geven.

Azure Cost Management biedt vergelijkbare functionaliteit als Cloudyn. Azure Cost Management is een systeemeigen Azure-oplossing voor kostenbeheer. Het helpt u kosten te analyseren, budgetten te maken en beheren, gegevens te exporteren en aanbevelingen voor optimalisatie te bekijken en er actie op te ondernemen om geld te besparen. Zie [budgetten maken en beheren](tutorial-acm-create-budgets.md)voor meer informatie over budgetten in cost management.

## <a name="create-budgets"></a>Budgetten maken

Wanneer u een budget maakt, stelt u deze voor het fiscale jaar en van toepassing op een bepaalde entiteit.

Maken van een budget en wijs deze toe aan een entiteit:

1. Navigeer naar **kosten** &gt; **Cost Management** &gt; **budget**.
2. Selecteer op de pagina budget beheer onder **entiteiten**de entiteit waarvoor u het budget wilt maken.
3. Selecteer in het budgetjaar, het jaar waarin u wilt maken van het budget.
4. Stel de Budgetwaarde van een voor elke maand. Wanneer u klaar bent, klikt u op **Opslaan**.
In dit voorbeeld worden de maandelijkse budget voor juni 2018 is ingesteld op $135.000. Het totale budget voor het jaar is $1,615,000.00.
![een budget pagina maken waar u een budget voor elke maand instelt](./media/manage-budgets/set-budget.png)


Voor het importeren van een bestand voor de jaarlijkse budget:

1. Onder **acties**selecteert u **exporteren** om een lege CSV-sjabloon te downloaden om te gebruiken als basis voor het budget.
2. Vul in het CSV-bestand met uw budget-fragmenten en lokaal opslaan.
3. Selecteer **importeren**onder **acties**.
4. Selecteer uw opgeslagen bestand en klik vervolgens op **OK**.

Als u uw voltooide budget wilt exporteren als een CSV-bestand, selecteert u onder **acties** **exporteren** om het bestand te downloaden.

## <a name="view-budget-in-reports"></a>Weergave budget in rapporten

Wanneer u klaar bent, wordt uw budget weer gegeven in de meeste kosten rapporten onder **kosten** &gt; **kosten analyse** en in het rapport kosten versus budget gedurende een periode. U kunt ook rapporten plannen op basis van budget drempels met behulp van **acties**.

Hier volgt een voorbeeld van het rapport Cost Analysis. Het toont de totale budget en kosten door de typen werkbelasting en het gebruik sinds het begin van het jaar.

![Voorbeeld van een rapport Cost Analysis met budget](./media/manage-budgets/cost-analysis-budget-example.png)

In dit voorbeeld wordt ervan uitgegaan dat de huidige datum is 22 juni. De kosten voor juni 2018 zijn $71,611.28 vergeleken met het maandelijkse budget van $135.000. De kosten is veel lager dan het maandelijkse budget omdat er nog steeds acht dagen aan vóór het einde van de maand.

Er is een andere manier om het rapport weer te geven om te kijken naar vs van de totale kosten uw budget. Als u de gecumuleerde kosten wilt zien, selecteert u **gecumuleerde kosten** en **totaal budget**onder **velden weer geven/verbergen**. Hier volgt een voorbeeld van de totale kosten sinds het begin van het jaar.

![Voor beelden van geaccumuleerde kosten en totaal budget weer gegeven in het rapport kosten versus budget gedurende een periode](./media/manage-budgets/accumulated-budget.png)

Enige tijd in de toekomst uw totale kosten kan groter zijn dan uw budget. U kunt beter zien dat als u de grafiek weergave wijzigt in het _regel_ type.

![Budget wordt weergegeven in een lijndiagram in de kosten door maanden rapport](./media/manage-budgets/budget-line.png)

## <a name="create-budget-alerts-for-a-filter"></a>Budget waarschuwingen voor een filter maken

In het vorige voorbeeld, kunt u zien dat de totale kosten het budget nadert. U kunt automatische budget waarschuwingen kunt maken, zodat u een bericht weergegeven wanneer de uitgaven van methoden of uw budget overschrijdt. De waarschuwing is in feite, een gepland rapport met een drempelwaarde. Budget waarschuwingsdrempel voor de metrische gegevens zijn onder andere:

- Resterende kosten versus budget – om op te geven van een valuta waarde drempelwaarde
- Kosten percentage versus budget – om op te geven van een drempelwaarde voor percentage waarde

We bekijken een voorbeeld.

Klik in het rapport kosten versus budget gedurende een periode op **acties** en selecteer vervolgens **rapport plannen**. Selecteer een drempelwaarde voor metrische gegevens op het tabblad drempelwaarde. Bijvoorbeeld **kosten percentage versus budget**. Selecteer een type waarschuwing en voer een percentage van het budget. Als u slechts één keer een melding wilt ontvangen, selecteert u **aantal opeenvolgende waarschuwingen** en typt u vervolgens _1_. Klik op **Opslaan**.

![Het maken van een budget waarschuwing bij het opslaan of dit vak rapport plannen](./media/manage-budgets/budget-alert.png)

## <a name="next-steps"></a>Volgende stappen

- Als u de eerste zelf studie voor Cloudyn nog niet hebt voltooid, leest u het [gebruik en de kosten](tutorial-review-usage.md).
- Meer informatie over de [rapporten die beschikbaar zijn in Cloudyn](use-reports.md).
