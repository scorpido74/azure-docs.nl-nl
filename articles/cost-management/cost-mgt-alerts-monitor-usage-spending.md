---
title: Gebruik en uitgaven bewaken met kosten meldingen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u met kosten waarschuwingen het gebruik en de uitgaven in Azure Cost Management kunt bewaken.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: alavital
ms.custom: ''
ms.openlocfilehash: 4be484cdff2014f11c872da9a246ef8406447712
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230119"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Kostenwaarschuwingen gebruiken om gebruik en uitgaven te controleren

Dit artikel helpt u bij het begrijpen en gebruiken van Cost Management-waarschuwingen voor het bewaken van uw Azure-gebruik en-uitgaven. Kosten waarschuwingen worden automatisch gegenereerd op basis van de verbruikte Azure-resources. In waarschuwingen worden alle actieve waarschuwingen voor kosten beheer en facturering samen op één plek weer gegeven. Wanneer uw verbruik een bepaalde drempel waarde heeft bereikt, worden er waarschuwingen gegenereerd door Cost Management. Er zijn drie soorten kosten meldingen: budget waarschuwingen, tegoed meldingen en quota voor het bestedings bericht van afdelingen.

## <a name="budget-alerts"></a>Budget waarschuwingen

Met budget waarschuwingen wordt u gewaarschuwd wanneer een uitgave, op basis van het gebruik of de kosten, de hoeveelheid bereikt of overschrijdt die is gedefinieerd in de [waarschuwings voorwaarde van het budget](tutorial-acm-create-budgets.md). Cost Management budgetten worden gemaakt met behulp van de Azure Portal of de Azure-API voor [verbruik](https://docs.microsoft.com/rest/api/consumption) .

In de Azure Portal worden budgetten gedefinieerd op basis van de kosten. Met behulp van de Azure-verbruiks-API worden budgetten gedefinieerd op basis van kosten of gebruik van verbruik. Budget waarschuwingen ondersteunen zowel op kosten gebaseerde als op gebruik gebaseerde budgetten. Budget waarschuwingen worden automatisch gegenereerd wanneer wordt voldaan aan de budget waarschuwings voorwaarden. U kunt alle kosten waarschuwingen weer geven in de Azure Portal. Wanneer er een waarschuwing wordt gegenereerd, wordt deze weer gegeven in kosten meldingen. Er wordt ook een e-mail bericht verzonden naar de personen in de lijst met ontvangers van het budget.

## <a name="credit-alerts"></a>Krediet waarschuwingen

Met krediet waarschuwingen wordt u gewaarschuwd wanneer uw Azure-tegoed monetaire toezeg gingen worden verbruikt. Monetaire toezeg gingen zijn voor organisaties met een Enter prise Agreement. Tegoeden worden automatisch gegenereerd om 90% en 100% van uw Azure-tegoed. Wanneer er een waarschuwing wordt gegenereerd, worden deze weer gegeven in kosten meldingen en in het e-mail bericht dat wordt verzonden naar de eigen aren van het account.

## <a name="department-spending-quota-alerts"></a>Quota voor afdelings bestedings waarschuwingen

Met quota waarschuwingen voor afdelingen krijgt u een melding wanneer de kosten van een afdeling een vaste drempel van het quotum bereiken. Uitgave quota worden geconfigureerd in de EA-Portal. Wanneer aan een drempel wordt voldaan, wordt er een e-mail bericht naar de afdelings eigenaren gegenereerd en weer gegeven in kosten meldingen. Bijvoorbeeld 50% of 75% van het quotum.

## <a name="supported-alert-features-by-offer-categories"></a>Ondersteunde waarschuwings functies per aanbiedings categorie

Ondersteuning voor waarschuwings typen is afhankelijk van het type Azure-account dat u hebt (micro soft-aanbieding). In de volgende tabel ziet u de waarschuwings functies die worden ondersteund door diverse micro soft-aanbiedingen. U kunt de volledige lijst met micro soft-aanbiedingen bekijken met [informatie over Cost Management gegevens](understand-cost-mgt-data.md).

| Waarschuwingstype | Enterprise Agreement | Microsoft-klantovereenkomst | Web direct/betalen naar gebruik |
|---|---|---|---|
| Budget | ✔ | ✔ | ✔ |
| Tegoed | ✔ |✘ | ✘ |
| Bestedings quotum afdeling | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Kosten waarschuwingen weer geven

Als u de kosten meldingen wilt weer geven, opent u het gewenste bereik in de Azure Portal en selecteert u **budgetten** in het menu. Gebruik het **bereik** Pill om over te scha kelen naar een ander bereik. Selecteer **kosten meldingen** in het menu. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

![Voorbeeld afbeelding van waarschuwingen die worden weer gegeven in Cost Management](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Het totale aantal actieve en genegeerde waarschuwingen wordt weer gegeven op de pagina kosten waarschuwingen.

Voor alle waarschuwingen wordt het type waarschuwing weer gegeven. Een budget waarschuwing toont de reden waarom het is gegenereerd en de naam van het budget dat van toepassing is op. Elke waarschuwing toont de datum waarop deze is gegenereerd, de status en het bereik (abonnement of beheer groep) waarop de waarschuwing van toepassing is.

Mogelijke statussen zijn onder andere **actief** en **gesloten**. Actieve status geeft aan dat de waarschuwing nog steeds relevant is. De status genegeerd geeft aan dat iemand de waarschuwing heeft gemarkeerd om deze in te stellen als niet meer relevant.

Selecteer een waarschuwing in de lijst om de details ervan weer te geven. Waarschuwings details geven meer informatie over de waarschuwing. Budget waarschuwingen bevatten een koppeling naar het budget. Als er een aanbeveling beschikbaar is voor een budget waarschuwing, wordt er ook een koppeling naar de aanbeveling weer gegeven. Waarschuwingen voor budget, tegoed en afdelings bestedingen zijn een koppeling voor het analyseren van de kosten analyse, waar u de kosten voor het bereik van de waarschuwing kunt verkennen. In het volgende voor beeld ziet u uitgaven voor een afdeling met waarschuwings Details.

![Voorbeeld afbeelding met uitgaven voor een afdeling met waarschuwings Details](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Wanneer u de details van een genegeerde waarschuwing bekijkt, kunt u deze opnieuw activeren als hand matige actie vereist is. In de volgende afbeelding ziet u een voorbeeld.

![Voorbeeld afbeelding met opties voor negeren en opnieuw activeren](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Zie ook

- Als u nog geen budget hebt gemaakt of waarschuwings voorwaarden voor een budget hebt ingesteld, voltooit u de zelf studie voor het [maken en beheren](tutorial-acm-create-budgets.md) van budgetten.
