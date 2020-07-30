---
title: Gebruik en uitgaven bewaken met kostenwaarschuwingen
description: In dit artikel wordt beschreven hoe u met kostenwaarschuwingen het gebruik en de uitgaven in Azure Cost Management kunt bewaken.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: ce63756e19e0991ce7aee0b2b6c9c65712b1680b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290805"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Kostenwaarschuwingen gebruiken om gebruik en uitgaven te controleren

In dit artikel leert u hoe u met waarschuwingen van Cost Management uw Azure-gebruik en -uitgaven kunt controleren. Kostenwaarschuwingen worden automatisch gegenereerd op basis van wanneer Azure-resources worden verbruikt. In waarschuwingen worden alle actieve waarschuwingen voor kostenbeheer en facturering samen op één plek weergegeven. Wanneer uw verbruik een bepaalde drempelwaarde heeft bereikt, worden er waarschuwingen gegenereerd door Cost Management. Er zijn drie soorten kostenwaarschuwingen: budgetwaarschuwingen, tegoedwaarschuwingen en waarschuwingen voor het bestedingsquota van afdelingen.

## <a name="budget-alerts"></a>Budgetwaarschuwingen

Met budgetwaarschuwingen wordt u gewaarschuwd wanneer uitgaven, op basis van het gebruik of de kosten, de hoeveelheid overschrijden die is gedefinieerd in de [Waarschuwingsvoorwaarde van het budget](tutorial-acm-create-budgets.md). Cost Management-budgetten worden gemaakt met behulp van de Azure-portal of de [Azure-verbruiks](https://docs.microsoft.com/rest/api/consumption)-API.

In de Azure-portal worden budgetten gedefinieerd op basis van de kosten. Met behulp van de Azure-verbruiks-API worden budgetten gedefinieerd op basis van kosten of verbruik. Budgetwaarschuwingen ondersteunen zowel op kosten gebaseerde als op gebruik gebaseerde budgetten. Budgetwaarschuwingen worden automatisch gegenereerd wanneer wordt voldaan aan de voorwaarden voor budgetwaarschuwingen. U kunt alle kostenwaarschuwingen weergeven in de Azure-portal. Wanneer er een waarschuwing wordt gegenereerd, wordt deze weergegeven in kostenmeldingen. Er wordt ook een e-mailbericht verzonden naar de personen in de lijst met ontvangers van het budget.

## <a name="credit-alerts"></a>Tegoedwaarschuwingen

Met tegoedwaarschuwingen wordt u gewaarschuwd wanneer uw monetaire toezeggingen voor Azure-tegoed worden verbruikt. Monetaire toezeg gingen zijn voor organisaties met een Enterprise Agreement. Tegoedwaarschuwingen worden automatisch gegenereerd bij 90% en 100% van uw Azure-tegoed. Wanneer er een waarschuwing wordt gegenereerd, wordt deze weergegeven in kostenwaarschuwingen en in het e-mailbericht dat wordt verzonden naar de eigenaren van het account.

## <a name="department-spending-quota-alerts"></a>Waarschuwingen voor het bestedingsquotum van afdelingen

Met waarschuwingen voor het bestedingsquotum van afdelingen krijgt u een melding wanneer de kosten van een afdeling een vaste drempel van het quotum bereiken. Bestedingsquota worden geconfigureerd in het EA-portal. Wanneer aan een drempel wordt voldaan, wordt er een e-mailbericht naar de afdelingseigenaren gegenereerd en weergegeven in kostenwaarschuwingen. Bijvoorbeeld 50% of 75% van het quotum.

## <a name="supported-alert-features-by-offer-categories"></a>Ondersteunde waarschuwingsfuncties per aanbiedingscategorie

Ondersteuning voor waarschuwings typen is afhankelijk van het type Azure-account dat u hebt (Microsoft-aanbieding). In de volgende tabel ziet u de waarschuwingsfuncties die worden ondersteund door diverse Microsoft-aanbiedingen. U kunt de volledige lijst met Microsoft-aanbiedingen bekijken op [Inzicht in Cost Management-gegevens](understand-cost-mgt-data.md).

| Waarschuwingstype | Enterprise Agreement | Microsoft-klantovereenkomst | Web direct/betalen per gebruik |
|---|---|---|---|
| Budget | ✔ | ✔ | ✔ |
| Tegoed | ✔ |✘ | ✘ |
| Bestedingsquotum van de afdeling | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Kostenwaarschuwingen weergeven

Als u kostenwaarschuwingen wilt weergeven, opent u het gewenste bereik in de Azure-portal en selecteert u **Budget** in het menu. Gebruik de **Bereik**-pil om over te schakelen naar een ander bereik. Selecteer **Kostenwaarschuwingen** in het menu. Zie [Understand and work with scopes](understand-work-scopes.md) (Engelstalig) voor meer informatie over bereiken.

![Voorbeeldafbeelding van waarschuwingen die worden weergegeven in Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Het totale aantal actieve en genegeerde waarschuwingen wordt weergegeven op de pagina kostenwaarschuwingen.

Voor alle waarschuwingen wordt het type waarschuwing weergegeven. Een budgetwaarschuwing toont de reden waarom ze is gegenereerd en de naam van het budget waarop ze van toepassing is. Elke waarschuwing toont de datum waarop deze is gegenereerd, de status en het bereik (abonnement of beheergroep) waarop de waarschuwing van toepassing is.

Mogelijke statussen zijn onder andere **Actief** en **Genegeerd**. Actieve status geeft aan dat de waarschuwing nog steeds relevant is. De status Genegeerd geeft aan dat iemand de waarschuwing heeft gemarkeerd om deze in te stellen als niet meer relevant.

Selecteer een waarschuwing in de lijst om de details ervan weer te geven. Waarschuwingsdetails geven meer informatie over de waarschuwing. Budgetwaarschuwingen bevatten een koppeling naar het budget. Als er een aanbeveling beschikbaar is voor een budgetwaarschuwing, wordt er ook een koppeling naar de aanbeveling weergegeven. Waarschuwingen voor budget, tegoed en afdelingsbestedingen zijn een koppeling voor het analyseren van de kostenanalyse, waar u de kosten voor het bereik van de waarschuwing kunt verkennen. In het volgende voorbeeld ziet u uitgaven voor een afdeling met waarschuwingsdetails.

![Voorbeeldafbeelding met uitgaven voor een afdeling met waarschuwingsdetails](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Wanneer u de details van een genegeerde waarschuwing bekijkt, kunt u deze opnieuw activeren als handmatige actie vereist is. In de volgende afbeelding ziet u een voorbeeld.

![Voorbeeldafbeelding met opties voor negeren en opnieuw activeren](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Zie ook

- Als u nog geen budget hebt gemaakt of waarschuwingsvoorwaarden voor een budget hebt ingesteld, voltooit u de zelfstudie [Budgetten maken en beheren](tutorial-acm-create-budgets.md).
