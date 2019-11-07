---
title: Zelf studie-Azure-budgetten maken en beheren | Microsoft Docs
description: Deze zelf studie helpt u bij het plannen en bepalen van de kosten van Azure-Services die u gebruikt.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 8c3c0574389fc9808af3cd70c928ede82d375076
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720727"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Zelf studie: Azure-budgetten maken en beheren

Met budgetten in Cost Management kunt u de verantwoordelijkheid in de organisatie beter plannen en stimuleren. Met budgetten kunt u de Azure-services die u gebruikt of waarop u zich abonneert voor een specifieke periode bijhouden. Ze helpen u anderen te informeren over hun uitgaven om proactief kosten te beheren en om te controleren hoe de uitgaven in de loop van de tijd worden uitgevoerd. Wanneer de budget drempels die u hebt gemaakt, worden overschreden, worden alleen meldingen geactiveerd. Er zijn geen gevolgen voor uw resources en uw verbruik wordt niet gestopt. U kunt budgetten gebruiken om bestedingen te vergelijken en bij te houden bij het analyseren van de kosten.

Kosten-en gebruiks gegevens zijn doorgaans binnen 12-16 uur beschikbaar en budgetten worden elke vier uur geëvalueerd op basis van deze kosten. E-mail meldingen worden normaal gesp roken binnen 12-16 uur ontvangen.

Budgetten worden automatisch opnieuw ingesteld aan het einde van een periode (maandelijks, elk kwar taal of jaarlijks) voor hetzelfde budget bedrag wanneer u een verval datum in de toekomst selecteert. Omdat ze opnieuw worden ingesteld met hetzelfde budget bedrag, moet u afzonderlijke budgetten maken wanneer de gebudgetteerde valuta bedragen voor toekomstige Peri Oden verschillen.

In de voor beelden in deze zelf studie leert u hoe u een budget voor een Azure Enterprise Agreement (EA)-abonnement kunt maken en bewerken.

Bekijk [hoe u een budget maakt om uw uitgaven te bewaken met Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) video om te zien hoe u budgetten kunt maken in azure om uitgaven te bewaken.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een budget maken in de Azure Portal
> * Een budget bewerken

## <a name="prerequisites"></a>Vereisten

Budgetten worden ondersteund voor diverse typen Azure-accounts. Zie [Gegevens van kostenbeheer begrijpen](understand-cost-mgt-data.md) voor de volledige lijst met ondersteunde accounttypen. Voor het weer geven van budgetten moet u ten minste lees toegang hebben voor uw Azure-account.

 Voor Azure EA-abonnementen moet u lees toegang hebben om budgetten weer te geven. Als u budgetten wilt maken en beheren, moet u de machtiging Inzender hebben. U kunt afzonderlijke budgetten voor EA-abonnementen en-resource groepen maken. U kunt echter geen budgetten voor EA-facturerings accounts maken.

De volgende Azure-machtigingen of-bereiken worden ondersteund per abonnement op budgetten per gebruiker en groep. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

- Eigenaar: eigenaren kunnen budgetten voor een abonnement instellen, wijzigen en verwijderen.
- Inzender en Cost Management Inzender: kan hun eigen budgetten maken, wijzigen of verwijderen. Inzenders kunnen ook de budgetbedragen aanpassen voor budgetten die zijn gemaakt door anderen.
- Lezer en Cost Management lezer: kan budgetten weer geven waarvoor ze zijn gemachtigd.

Zie [toegang tot Cost Management gegevens toewijzen](assign-access-acm-data.md)voor meer informatie over het toewijzen van machtigingen aan Cost Management gegevens.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

- Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Een budget maken in de Azure Portal

U kunt een Azure-abonnement budget maken voor een maandelijks, elk kwar taal of per jaar. Uw navigatie-inhoud in de Azure Portal bepaalt of u een budget maakt voor een abonnement of voor een beheer groep.

Als u een budget wilt maken of weer geven, opent u het gewenste bereik in de Azure Portal en selecteert u **budgetten** in het menu. Ga bijvoorbeeld naar **abonnementen**, selecteer een abonnement in de lijst en selecteer vervolgens **budgetten** in het menu. Gebruik het **bereik** Pill om over te scha kelen naar een ander bereik, zoals een beheer groep, in budgetten. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

Nadat u budgetten hebt gemaakt, wordt er een eenvoudige weer gave van uw huidige uitgaven weer gegeven.

Klik op **Toevoegen**.

![Voor beeld van een lijst met budgetten die al zijn gemaakt](./media/tutorial-acm-create-budgets/budgets01.png)

Zorg er in het venster **budget maken** voor dat de weer gegeven Scope juist is. Kies de filters die u wilt toevoegen. Filters bieden u de mogelijkheid om budgetten te maken voor specifieke kosten, zoals resource groepen in een abonnement of een service, zoals virtuele machines. Elk filter dat u kunt gebruiken in de kosten analyse kan ook worden toegepast op een budget.

Nadat u uw bereik en filters hebt geïdentificeerd, typt u de naam van het budget. Kies vervolgens een maand, kwar taal of jaarlijks budget herstelings periode. Deze opnieuw ingestelde periode bepaalt het tijd venster dat door het budget wordt geanalyseerd. De kosten geëvalueerd door het budget beginnen bij nul aan het begin van elke nieuwe periode. Wanneer u een driemaandelijks budget maakt, werkt het op dezelfde manier als een maandelijks budget. Het verschil is dat het budget bedrag voor het kwar taal gelijkmatig is verdeeld over de drie maanden van het kwar taal. Een jaarlijks budget bedrag wordt evenredig verdeeld over de 12 maanden van het kalender jaar.

Als u een abonnement op basis van betalen per gebruik, MSDN of Visual Studio hebt, kan de facturerings periode van uw factuur niet worden uitgelijnd op de kalender maand. Voor deze typen abonnementen en resource groepen kunt u een budget maken dat is afgestemd op uw factuur periode of kalender maanden. Als u een budget wilt maken dat is afgestemd op uw factuur periode, selecteert u een herstel periode van **facturerings maand**, **facturerings kwartaal**of **factuur jaar**. Als u een budget wilt maken dat is afgestemd op de kalender maand, selecteert u een opnieuw ingestelde periode van **elke maand**, **elk kwar taal**of **jaarlijks**.

Bepaal vervolgens de verval datum wanneer het budget ongeldig wordt en stopt met het evalueren van de kosten.

Op basis van de velden die u in het budget hebt gekozen, wordt een grafiek weer gegeven om u te helpen bij het selecteren van een drempel waarde die voor uw budget moet worden gebruikt. Het voorgestelde budget is gebaseerd op de hoogste geraamde kosten die u in toekomstige Peri Oden kunt doen. U kunt het budget bedrag wijzigen.

![Voor beeld van het maken van een budget met maandelijkse kosten gegevens ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Nadat u het budget bedrag hebt geconfigureerd, klikt u op **volgende** om budget waarschuwingen te configureren. Voor budgetten is ten minste één kosten drempel (% van budget) en een bijbehorend e-mail adres vereist. U kunt desgewenst Maxi maal vijf drempels en vijf e-mail adressen in één budget opneemt. Wanneer aan een budget drempel wordt voldaan, worden e-mail meldingen doorgaans in minder dan 20 uur ontvangen. Zie [cost Alerts gebruiken](cost-mgt-alerts-monitor-usage-spending.md)voor meer informatie over meldingen. In het onderstaande voor beeld wordt er een e-mail waarschuwing gegenereerd wanneer 90% van het budget wordt bereikt. Als u een budget maakt met de budgetten-API, kunt u ook rollen toewijzen aan personen om waarschuwingen te ontvangen. Het toewijzen van rollen aan personen wordt niet ondersteund in de Azure Portal. Zie [budgetten-API](/rest/api/consumption/budgets)voor meer informatie over de Azure budgets-API.

![Voor beeld van waarschuwings voorwaarden weer geven](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Nadat u een budget hebt gemaakt, wordt dit weer gegeven in de kosten analyse. Het weer geven van uw budget ten opzichte van uw bestedings trend is een van de eerste stappen wanneer u begint met [het analyseren van uw kosten en uitgaven](quick-acm-cost-analysis.md).

![Voor beeld van budget en uitgaven weer gegeven in kosten analyse](./media/tutorial-acm-create-budgets/cost-analysis.png)

In het voor gaande voor beeld hebt u een budget voor een abonnement gemaakt. U kunt echter ook een budget voor een resource groep maken. Als u een budget voor een resource groep wilt maken, gaat u naar **Cost Management + facturerings** &gt; **abonnementen** &gt; selecteert u een abonnement > **resource groepen** > selecteert u een resource groep > **budgetten** > en vervolgens **Voeg** een budget toe.

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

Budget integratie met actie groepen werkt alleen voor actie groepen waarvoor het schema common Alerts is uitgeschakeld. Zie voor meer informatie over het uitschakelen van het schema [Hoe kan ik het algemene waarschuwings schema inschakelen?](../azure-monitor/platform/alerts-common-schema.md#how-do-i-enable-the-common-alert-schema)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een budget maken in de Azure Portal
> * Een budget bewerken

Ga door naar de volgende zelf studie voor het maken van een terugkerende export voor uw kosten beheer gegevens.

> [!div class="nextstepaction"]
> [Geëxporteerde gegevens maken en beheren](tutorial-export-acm-data.md)
