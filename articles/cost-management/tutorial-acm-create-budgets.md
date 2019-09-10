---
title: Zelfstudie - maken en beheren van Azure budgetten | Microsoft Docs
description: In deze zelfstudie kunt plannen en -account voor de kosten van Azure-services die u verbruikt.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 59ae3b587751bd4af2c9e5ab0abefb8a5c3bf8e6
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70843934"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Zelfstudie: Azure-budgetten maken en beheren

Met budgetten in Cost Management kunt u de verantwoordelijkheid in de organisatie beter plannen en stimuleren. Met budgetten kunt u de Azure-services die u gebruikt of waarop u zich abonneert voor een specifieke periode bijhouden. Ze helpen u anderen te informeren over hun uitgaven aan proactief kosten kunt beheren en controleren hoe uitgaven verloopt na verloop van tijd. Wanneer de budgetdrempels die u hebt gemaakt, worden overschreden, worden alleen meldingen worden geactiveerd. Geen van uw resources worden beïnvloed en uw verbruik is niet gestopt. U kunt budgetten gebruiken om te vergelijken en als u kosten analyseren uitgave bij te houden.

Maandelijkse budgetten worden geëvalueerd op basis van elke vier uur. Gegevens voor verbruikte resources zijn echter binnen acht uur beschikbaar. Wanneer aan een budget drempel wordt voldaan, worden e-mail meldingen doorgaans in minder dan 12 uur ontvangen. 

Budgetten automatisch opnieuw instellen aan het einde van een punt (maandelijks, per kwartaal of per jaar) voor hetzelfde budgetbedrag wanneer u een vervaldatum in de toekomst selecteert. Omdat ze opnieuw met hetzelfde budgetbedrag instelt, moet u afzonderlijke budgetten wanneer gebudgetteerde maken bedragen verschillen voor toekomstige perioden.

De voorbeelden in deze zelfstudie begeleidt u bij het maken en bewerken van een budget voor een Azure Enterprise Agreement (EA)-abonnement.

Bekijk [hoe u een budget maakt om uw uitgaven te bewaken met Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) video om te zien hoe u budgetten kunt maken in azure om uitgaven te bewaken.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maken van een budget in de Azure portal
> * Een budget bewerken

## <a name="prerequisites"></a>Vereisten

Budgetten worden ondersteund voor diverse typen Azure-accounts. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](understand-cost-mgt-data.md). Voor het weer geven van budgetten moet u ten minste lees toegang hebben voor uw Azure-account.

 Voor Azure EA-abonnementen moet u lees toegang hebben om budgetten weer te geven. Als u wilt maken en beheren van budgetten, moet u de machtiging Inzender hebt. U kunt afzonderlijke budgetten voor EA-abonnementen en resourcegroepen. U kan echter budgetten maken voor facturering van accounts EA.

De volgende Azure-machtigingen of-bereiken worden ondersteund per abonnement op budgetten per gebruiker en groep. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

- Eigenaar: eigenaren kunnen budgetten voor een abonnement instellen, wijzigen en verwijderen.
- Inzender en Inzender van Cost Management – kunt maken, wijzigen of verwijderen van hun eigen budgetten. Inzenders kunnen ook de budgetbedragen aanpassen voor budgetten die zijn gemaakt door anderen.
- Lezer en kostenbeheer lezer – kunt budgetten die ze gemachtigd zijn om te bekijken.

Zie voor meer informatie over het toewijzen van machtigingen aan gegevens van Cost Management [toegang tot gegevens van kostenbeheer toewijzen](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

- Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Maken van een budget in de Azure portal

U kunt het budget van een Azure-abonnement maken voor een bepaalde maand, kwartaal of jaar. Uw navigatie-inhoud in de Azure Portal bepaalt of u een budget maakt voor een abonnement of voor een beheer groep.

Als u een budget wilt maken of weer geven, opent u het gewenste bereik in de Azure Portal en selecteert u **budgetten** in het menu. Ga bijvoorbeeld naar **abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens **budgetten** in het menu. Gebruik het **bereik** Pill om over te scha kelen naar een ander bereik, zoals een beheer groep, in budgetten. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

Nadat u budgetten maakt, worden ze een eenvoudige weergave van uw huidige uitgaven op basis hiervan weergegeven.

Klik op **Toevoegen**.

![Voor beeld van een lijst met budgetten die al zijn gemaakt](./media/tutorial-acm-create-budgets/budgets01.png)

Zorg er in het venster **budget maken** voor dat de weer gegeven Scope juist is. Kies de filters die u wilt toevoegen. Filters bieden u de mogelijkheid om budgetten te maken voor specifieke kosten, zoals resource groepen in een abonnement of een service, zoals virtuele machines. Elk filter dat u kunt gebruiken in de kosten analyse kan ook worden toegepast op een budget.

Nadat u uw bereik en filters hebt geïdentificeerd, typt u de naam van het budget. Kies vervolgens een maand, kwar taal of jaarlijks budget herstelings periode. Deze opnieuw ingestelde periode bepaalt het tijd venster dat door het budget wordt geanalyseerd. De kosten geëvalueerd door het budget beginnen bij nul aan het begin van elke nieuwe periode. Wanneer u een per kwartaal budget maakt, wordt het werkt op dezelfde manier als een maandelijkse budget. Het verschil is dat het budgetbedrag voor het kwartaal gelijkmatig wordt verdeeld over de drie maanden van het kwartaal. Een jaarlijks budget bedrag wordt evenredig verdeeld over de 12 maanden van het kalender jaar.

Als u een abonnement op basis van betalen per gebruik, MSDN of Visual Studio hebt, kan de facturerings periode van uw factuur niet worden uitgelijnd op de kalender maand. Voor deze typen abonnementen en resource groepen kunt u een budget maken dat is afgestemd op uw factuur periode of kalender maanden. Als u een budget wilt maken dat is afgestemd op uw factuur periode, selecteert u een herstel periode van **facturerings maand**, **facturerings kwartaal**of **factuur jaar**. Als u een budget wilt maken dat is afgestemd op de kalender maand, selecteert u een opnieuw ingestelde periode van **elke maand**, **elk kwar taal**of **jaarlijks**.

Bepaal vervolgens de verval datum wanneer het budget ongeldig wordt en stopt met het evalueren van de kosten.

Op basis van de velden die u in het budget hebt gekozen, wordt een grafiek weer gegeven om u te helpen bij het selecteren van een drempel waarde die voor uw budget moet worden gebruikt. Het voorgestelde budget is gebaseerd op de hoogste geraamde kosten die u in toekomstige Peri Oden kunt doen. U kunt het budget bedrag wijzigen.

![Voor beeld van het maken van een budget met maandelijkse kosten gegevens ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Nadat u het budget bedrag hebt geconfigureerd, klikt u op **volgende** om budget waarschuwingen te configureren. Budgetten moeten ten minste één kosten drempel (% van budget) en een bijbehorende e-mailadres. Eventueel kunt u maximaal vijf drempelwaarden en vijf e-mailadressen in één budget opnemen. Wanneer een budgetdrempelwaarde is bereikt, worden normaal gesproken e-mailmeldingen ontvangen in minder dan acht uur. Zie [cost Alerts gebruiken](cost-mgt-alerts-monitor-usage-spending.md)voor meer informatie over meldingen. In het onderstaande voor beeld wordt er een e-mail waarschuwing gegenereerd wanneer 90% van het budget wordt bereikt.

![Voor beeld van waarschuwings voorwaarden weer geven](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Nadat u een budget gemaakt, wordt deze weergegeven in kostenanalyse. Een van de eerste stappen wanneer u met begint het weergeven van uw budget ten opzichte van uw uitgaventrend is [analyseren van uw kosten en uitgaven](quick-acm-cost-analysis.md).

![Voorbeeld van budget en uitgaven wordt weergegeven in kostenanalyse](./media/tutorial-acm-create-budgets/cost-analysis.png)

In het voorgaande voorbeeld, moet u een budget voor een abonnement gemaakt. U kunt echter ook een budget voor een resourcegroep maken. Als u maken van een budget voor een resourcegroep wilt, gaat u naar **kostenbeheer en facturering** &gt; **abonnementen** &gt; Selecteer een abonnement > **Resource groepen** > Selecteer een resourcegroep > **budgetten** > en vervolgens **toevoegen** een budget.

## <a name="trigger-an-action-group"></a>Een actie groep activeren

Wanneer u een budget voor een abonnement of een bereik van een resource groep maakt of bewerkt, kunt u dit configureren om een actie groep aan te roepen. De actie groep kan verschillende acties uitvoeren wanneer aan uw budget drempel wordt voldaan. Zie voor meer informatie over actie groepen [actie groepen maken en beheren in de Azure Portal](../azure-monitor/platform/action-groups.md). Zie [kosten beheren met Azure-budgetten](../billing/billing-cost-management-budget-scenario.md)voor meer informatie over het gebruik van automatisering op basis van een budget met actie groepen.

Als u actie groepen wilt maken of bijwerken, klikt u op **actie groepen beheren** terwijl u een budget maakt of bewerkt.

![Voor beeld van het maken van een budget voor het weer geven van beheer actie groepen](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Klik vervolgens op **actie groep toevoegen** en maak de actie groep.


![Afbeelding van het vak actie groep toevoegen](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Nadat de actie groep is gemaakt, sluit u het vak om terug te gaan naar uw budget.

Stel uw budget zo in dat uw actie groep wordt gebruikt wanneer aan een afzonderlijke drempel wordt voldaan. Er worden Maxi maal vijf verschillende drempel waarden ondersteund.

![Voor beeld van het selecteren van een actie groep voor een waarschuwings voorwaarde](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

In het volgende voor beeld worden budget drempels weer gegeven die zijn ingesteld op 50%, 75% en 100%. Elk is geconfigureerd om de opgegeven acties binnen de aangewezen actie groep te activeren.

![Voor beeld waarin waarschuwings voorwaarden worden weer gegeven die zijn geconfigureerd met verschillende actie groepen en typen acties](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Maken van een budget in de Azure portal
> * Een budget bewerken

Ga naar de volgende zelfstudie voor het maken van een terugkerende exporteren voor uw gegevens van cost management.

> [!div class="nextstepaction"]
> [Maken en beheren van de geëxporteerde gegevens](tutorial-export-acm-data.md)
