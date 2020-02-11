---
title: 'Zelfstudie: Azure-budgetten maken en beheren | Microsoft Docs'
description: Deze zelfstudie helpt u bij het plannen en verantwoorden van de kosten van Azure-Services die u gebruikt.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/22/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: bb02c4903348a3b8c1d129f02be64109ec0f48eb
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76769831"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Zelfstudie: Azure-budgetten maken en beheren

Met budgetten in Cost Management kunt u de verantwoordelijkheid in de organisatie beter plannen en stimuleren. Met budgetten kunt u de Azure-services die u gebruikt of waarop u zich abonneert voor een specifieke periode bijhouden. Ze helpen u anderen te informeren over hun uitgaven om kosten proactief te beheren en uitgaven in de loop van de tijd bij te houden. Wanneer de budgetdrempels die u hebt gemaakt, worden overschreden, worden alleen meldingen geactiveerd. Dit heeft geen gevolgen voor uw resources en uw gebruik wordt niet gestopt. U kunt budgetten gebruiken om bestedingen te vergelijken en bij te houden bij het analyseren van kosten.

Kosten- en gebruiksgegevens zijn doorgaans binnen 12 tot 16 uur beschikbaar en budgetten worden elke vier uur geëvalueerd op basis van deze kosten. E-mailmeldingen worden doorgaans binnen 12 tot 16 uur ontvangen.

Wanneer u een vervaldatum in de toekomst selecteert, worden budgetten bovendien aan het einde van een periode (maand, kwartaal of jaar) automatisch opnieuw ingesteld op hetzelfde bedrag. Omdat ze opnieuw worden ingesteld met hetzelfde budgetbedrag, moet u afzonderlijke budgetten maken wanneer de gebudgetteerde valutabedragen voor toekomstige perioden verschillen.

In de voorbeelden in deze zelfstudie leert u hoe u een budget voor een Azure EA-abonnement (Enterprise Agreement) kunt maken en bewerken.

Bekijk de video [Apply budgets to subscriptions using the Azure portal](https://www.youtube.com/watch?v=UrkHiUx19Po) om te zien hoe u in Azure budgetten kunt maken om uitgaven te bewaken.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een budget maken in de Azure-portal
> * Een budget bewerken

## <a name="prerequisites"></a>Vereisten

Budgetten worden ondersteund voor verschillende typen Azure-accounts. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](understand-cost-mgt-data.md). Als u budgetten wilt weergeven, hebt u minimaal leestoegang voor uw Azure-account nodig.

 Voor Azure EA-abonnementen moet u leestoegang hebben om budgetten weer te geven. Als u budgetten wilt maken en beheren, moet u de machtiging Inzender hebben. U kunt afzonderlijke budgetten voor EA-abonnementen en resourcegroepen maken. U kunt echter geen budgetten voor EA-factureringsrekeningen maken.

In abonnementen worden de volgende Azure-machtigingen, of bereiken, ondersteund voor budgetten voor gebruikers en groepen. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.

- Eigenaar: eigenaren kunnen budgetten voor een abonnement instellen, wijzigen en verwijderen.
- Inzender en Inzender Cost Management: inzenders kunnen hun eigen budgetten instellen, wijzigen en verwijderen. Inzenders kunnen ook de budgetbedragen aanpassen voor budgetten die zijn gemaakt door anderen.
- Lezer en Lezer Cost Management: lezers kunnen budgetten inzien waarvoor ze zijn gemachtigd.

Zie [Toegang tot gegevens van Cost Management toewijzen](../../cost-management/assign-access-acm-data.md) voor meer informatie over het toewijzen van toegang tot de gegevens van Cost Management.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

- Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Een budget maken in de Azure-portal

U kunt een budget voor een Azure-abonnement maken voor een periode van een maand, kwartaal of jaar. Uw navigatie-inhoud in de Azure-portal bepaalt of u een budget maakt voor een abonnement of voor een beheergroep.

Als u een budget wilt maken of bekijken, opent u het gewenste bereik in de Azure-portal en selecteert u **Budgetten** in het menu. Ga bijvoorbeeld naar **Abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens **Budgetten** in het menu. Gebruik het besturingselement **Bereik** om in Budgetten over te schakelen naar een ander bereik, zoals een beheergroep. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.

Nadat u budgetten hebt gemaakt, wordt er een eenvoudig overzicht van uw huidige uitgaven versus uw budgetten weergegeven.

Klik op **Add**.

![Voorbeeld van een lijst met budgetten die al zijn gemaakt](./media/tutorial-acm-create-budgets/budgets01.png)

Controleer of het weergegeven bereik juist is in het venster **Create budget**. Kies eventuele filters die u wilt toevoegen. Filters bieden u de mogelijkheid om budgetten te maken voor specifieke kosten, zoals resourcegroepen in een abonnement of een service, zoals virtuele machines. Elk filter dat u kunt gebruiken voor kostenanalyse, kan ook worden toegepast op een budget.

Nadat u het bereik en filters hebt opgegeven, typt u een naam voor het budget. Kies vervolgens een maand, kwartaal of jaar als periode voor het opnieuw instellen van het budget. Deze periode bepaalt het tijdvenster dat door het budget wordt geanalyseerd. De kosten die door het budget worden geëvalueerd, beginnen bij nul aan het begin van elke nieuwe periode. Wanneer u een kwartaalbudget maakt, werkt dit proces op dezelfde manier als voor een maandbudget. Het enige verschil is dat het budgetbedrag voor het kwartaal gelijkmatig wordt verdeeld over de drie maanden van het kwartaal. Een jaarbudgetbedrag wordt evenredig verdeeld over de 12 maanden van het kalenderjaar.

Als u een Betalen per gebruik-, MSDN- of Visual Studio-abonnement hebt, komt de factureringsperiode van uw factuur mogelijk niet overeen met de kalendermaand. Voor deze typen abonnementen en resourcegroepen kunt u een budget maken dat is afgestemd op uw factuurperiode of op kalendermaanden. Als u een budget wilt maken dat is afgestemd op uw factuurperiode, selecteert u **Factuurmaand**, **Factuurkwartaal** of **Factuurjaar** bij Periode voor opnieuw instellen. Als u een budget wilt maken dat is afgestemd op de kalendermaand, selecteert u **Maandelijks**, **Driemaandelijks** of **Jaarlijks** bij Periode voor opnieuw instellen.

Bepaal vervolgens de vervaldatum wanneer het budget ongeldig wordt en de kosten niet meer worden geëvalueerd.

Op basis van de velden die tot dusver in het budget zijn gekozen, wordt een grafiek weergegeven om u te helpen bij het selecteren van een drempelwaarde voor uw budget. Het voorgestelde budget is gebaseerd op de hoogste geraamde kosten die u in toekomstige perioden kunt maken. U kunt het budgetbedrag wijzigen.

![Voorbeeld van het maken van een budget met maandelijkse kostengegevens ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Nadat u het budgetbedrag hebt geconfigureerd, klikt u op **Volgende** om budgetwaarschuwingen te configureren. Voor budgetten is ten minste één kostendrempel (% van budget) en een bijbehorend e-mailadres vereist. U kunt maximaal vijf drempels en vijf e-mailadressen in één budget opnemen. Wanneer een budgetdrempel wordt bereikt, worden de bijbehorende e-mailmeldingen doorgaans binnen 20 uur ontvangen. Zie [Use cost alerts](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md) (Kostenwaarschuwingen gebruiken) voor meer informatie over meldingen. In het onderstaande voorbeeld wordt er een e-mailwaarschuwing gegenereerd wanneer 90% van het budget is besteed. Als u een budget maakt met de budget-API, kunt u ook rollen aan personen toewijzen voor het ontvangen van waarschuwingen. Het toewijzen van rollen aan personen wordt niet ondersteund in de Azure-portal. Zie [Budgets API ](/rest/api/consumption/budgets) voor meer informatie over de budget-API van Azure.

![Voorbeeld van waarschuwingsvoorwaarden](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Nadat u een budget hebt gemaakt, wordt het weergegeven in de kostenanalyse. Het weergeven van uw budget ten opzichte van uw uitgaventrend is een van de eerste stappen wanneer u begint met het [analyseren van uw kosten en uitgaven](../../cost-management/quick-acm-cost-analysis.md).

![Voorbeeld van budget en uitgaven in kostenanalyse](./media/tutorial-acm-create-budgets/cost-analysis.png)

In het voorgaande voorbeeld hebt u een budget voor een abonnement gemaakt. U kunt echter ook een budget voor een resourcegroep maken. Als u een budget voor een resourcegroep wilt maken, gaat u naar **Cost Management + facturering** &gt; **Abonnementen** &gt; een abonnement selecteren > **Resourcegroepen** > een resourcegroep selecteren > **Budgetten** > en kunt u vervolgens een budget **toevoegen**.

## <a name="costs-in-budget-evaluations"></a>Kosten in budgetevaluaties

Evaluaties van budgetkosten bevatten nu gegevens van gereserveerde instanties en aankopen. Als de kosten van toepassing zijn op u, kunt u waarschuwingen ontvangen wanneer de kosten zijn opgenomen in uw evaluaties. U wordt aangeraden zich aan te melden bij de [Azure-portal](https://portal.azure.com) om te controleren of de budgetdrempels goed zijn geconfigureerd voor de nieuwe kosten. De gefactureerde kosten voor Azure blijven gelijk. Budgetten worden nu geëvalueerd op basis van een uitgebreidere set kosten. Als de kosten niet op u van toepassing zijn, blijft uw budgetgedrag gelijk.

Als u de nieuwe kosten wilt filteren zodat budgetten alleen worden geëvalueerd voor de eerste partij voor Azure-gebruikskosten, voegt u de volgende filters toe aan uw budget:

- Type van uitgever: Azure
- Kostentype: Gebruik

Evaluaties van budgetkosten zijn gebaseerd op de werkelijke kosten. Ze bevatten geen afschrijvingen. Zie [Understanding grouping and filtering options](quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) voor meer informatie over de beschikbare filteropties in budgetten.


## <a name="trigger-an-action-group"></a>Een actiegroep activeren

Wanneer u een budget voor het bereik van een abonnement of resourcegroep maakt of bewerkt, kunt u dit configureren voor het aanroepen van een actiegroep. Met de actiegroep kunnen verschillende acties worden uitgevoerd wanneer de budgetdrempel is bereikt. Actiegroepen worden momenteel alleen ondersteund voor bereiken van abonnementen en resourcegroepen. Zie [Actiegroepen maken en beheren in de Azure-portal](../../azure-monitor/platform/action-groups.md) voor meer informatie over actiegroepen. Zie [Kosten beheren met Azure-budgetten](../manage/cost-management-budget-scenario.md) voor meer informatie over het gebruik van automatisering op basis van budgetten met actiegroepen.



Als u actiegroepen wilt maken of bijwerken, klikt u op **Actiegroepen beheren** tijdens het maken of bewerken van een budget.

![Voorbeeld van het maken van een budget voor het weergeven van Actiegroepen beheren](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Klik vervolgens op **Actiegroep toevoegen** en maak de actiegroep.


![Afbeelding van het vak Actiegroep toevoegen](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Nadat de actiegroep is gemaakt, sluit u het vak om terug te gaan naar uw budget.

Configureer uw budget zo dat uw actiegroep wordt gebruikt wanneer een afzonderlijke drempel wordt bereikt. Er worden maximaal vijf verschillende drempels ondersteund.

![Voorbeeld van het selecteren van een actiegroep voor een waarschuwingsvoorwaarde](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

In het volgende voorbeeld zijn budgetdrempels ingesteld op 50%, 75% en 100%. Elke drempel is geconfigureerd om de opgegeven acties binnen de aangewezen actiegroep te activeren.

![Voorbeeld van waarschuwingsvoorwaarden die zijn geconfigureerd met verschillende actiegroepen en typen acties](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

Budgetintegratie met actiegroepen werkt alleen voor actiegroepen waarvoor het algemene waarschuwingsschema is uitgeschakeld. Zie [Hoe kan ik het algemene waarschuwingsschema inschakelen?](../../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema) voor meer informatie over het uitschakelen van het schema.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een budget maken in de Azure-portal
> * Een budget bewerken

Ga door naar de volgende zelfstudie voor het maken van een terugkerende export voor uw kostenbeheergegevens.

> [!div class="nextstepaction"]
> [Geëxporteerde gegevens maken en beheren](tutorial-export-acm-data.md)
