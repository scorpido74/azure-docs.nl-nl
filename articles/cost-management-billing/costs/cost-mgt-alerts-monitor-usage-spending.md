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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988498"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Kostenwaarschuwingen gebruiken om gebruik en uitgaven te controleren

Dit artikel helpt u bij het begrijpen en gebruiken van Cost Management-waarschuwingen voor het bewaken van uw Azure-gebruik en-uitgaven. Kosten waarschuwingen worden automatisch gegenereerd op basis van de verbruikte Azure-resources. In waarschuwingen worden alle actieve waarschuwingen voor kosten beheer en facturering samen op één plek weer gegeven. Wanneer uw verbruik een bepaalde drempel waarde heeft bereikt, worden er waarschuwingen gegenereerd door Cost Management. Er zijn drie soorten kostenwaarschuwingen: budgetwaarschuwingen, kredietwaarschuwingen en waarschuwingen voor bestedingsquota van afdelingen.

## <a name="budget-alerts"></a>Budgetwaarschuwingen

Met budget waarschuwingen wordt u gewaarschuwd wanneer een uitgave, op basis van het gebruik of de kosten, de hoeveelheid bereikt of overschrijdt die is gedefinieerd in de [waarschuwings voorwaarde van het budget](tutorial-acm-create-budgets.md). Cost Management budgetten worden gemaakt met behulp van de Azure Portal of de Azure-API voor [verbruik](https://docs.microsoft.com/rest/api/consumption) .

In Azure Portal worden budgetten gedefinieerd op basis van de kosten. Met de Azure Consumption-API worden budgetten gedefinieerd op basis van de kosten of het gebruik. Budgetwaarschuwingen ondersteunen zowel budgetten die op kosten zijn gebaseerd als budgetten die op gebruik zijn gebaseerd. Budgetwaarschuwingen worden automatisch gegenereerd wanneer aan de budgetwaarschuwingsvoorwaarden wordt voldaan. U kunt alle kostenwaarschuwingen in Azure Portal bekijken. Wanneer er een waarschuwing wordt gegenereerd, wordt deze weergegeven in de kostenmeldingen. Er wordt ook een waarschuwingsmail verzonden naar de personen in de lijst met ontvangers van waarschuwingen voor het budget.

## <a name="credit-alerts"></a>Tegoedwaarschuwingen

Met tegoedwaarschuwingen wordt u op de hoogte gebracht wanneer de financiële toezeggingen voor uw Azure-tegoed zijn verbruikt. Financiële toezeggingen worden gebruikt voor organisaties met een Enterprise Overeenkomst. Tegoedwaarschuwingen worden automatisch gegenereerd bij 90% en 100% van het saldo voor uw Azure-tegoed. Wanneer er een waarschuwing wordt gegenereerd, wordt deze weergegeven in de kostenmeldingen en in het e-mailbericht dat naar de eigenaren van het account wordt verzonden.

## <a name="department-spending-quota-alerts"></a>Waarschuwingen voor bestedingsquota van afdelingen

Bij waarschuwingen voor bestedingsquota van afdelingen krijgt u een melding wanneer de uitgaven van een afdeling een vaste drempelwaarde van het quotum bereiken. Uitgavenquota worden in EA Portal geconfigureerd. Wanneer een drempelwaarde wordt bereikt, wordt een e-mailbericht gegenereerd, naar de afdelingseigenaren verzonden en weergegeven in de kostenmeldingen. Bijvoorbeeld 50% of 75% van het quotum.

## <a name="supported-alert-features-by-offer-categories"></a>Ondersteunde waarschuwingsfuncties per aanbiedingscategorie

Ondersteuning voor waarschuwingstypen is afhankelijk van het type Azure-account dat u hebt (Microsoft-aanbieding). In de volgende tabel worden de waarschuwingsfuncties vermeld die worden ondersteund door de verschillende Microsoft-aanbiedingen. U kunt de volledige lijst met micro soft-aanbiedingen bekijken met [informatie over Cost Management gegevens](understand-cost-mgt-data.md).

| Waarschuwingstype | Enterprise Agreement | Microsoft-klantovereenkomst | Web direct/betalen naar gebruik |
|---|---|---|---|
| Budget | ✔ | ✔ | ✔ |
| Tegoed | ✔ |✘ | ✘ |
| Bestedingsquotum van afdeling | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Kostenwaarschuwingen bekijken

Als u kostenwaarschuwingen wilt bekijken, opent u het gewenste bereik in Azure Portal en selecteert u in het menu de optie **Budgetten**. Gebruik de pill **Bereik** om naar een ander bereik over te schakelen. Selecteer in het menu de optie **Kostenwaarschuwingen**. Zie voor meer informatie over bereiken [begrijpen en werken met scopes](understand-work-scopes.md).

![Afbeelding met voorbeeld van waarschuwingen die in Cost Management worden weergegeven](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Het totale aantal actieve en genegeerde waarschuwingen wordt weergegeven op de pagina met kostenwaarschuwingen.

Voor alle waarschuwingen wordt het waarschuwingstype weergegeven. Voor een budgetwaarschuwing wordt vermeld waarom deze is gegenereerd en wat de naam is van het budget waarop deze van toepassing is. Elke waarschuwing toont de datum waarop deze is gegenereerd, de status en het bereik (abonnement of beheer groep) waarop de waarschuwing van toepassing is.

Mogelijke statussen zijn **Actief** en **Genegeerd**. De status Actief geeft aan dat de waarschuwing nog steeds van toepassing is. De status Genegeerd geeft aan dat iemand de waarschuwing heeft gemarkeerd om aan te geven dat deze niet meer van toepassing is.

Selecteer een waarschuwing in de lijst om de details hiervan te bekijken. Met de waarschuwingsdetails wordt meer informatie gegeven over de waarschuwing. Budgetwaarschuwingen bevatten een koppeling naar het budget. Als er een aanbeveling beschikbaar is voor een budgetwaarschuwing, wordt er ook een koppeling naar de aanbeveling weergegeven. Budgetwaarschuwingen, kredietwaarschuwingen en waarschuwingen voor bestedingsquota van afdelingen bevatten een koppeling naar de kostenanalyse, waar u de kosten voor het bereik van de waarschuwing kunt bekijken. In het volgende voor beeld ziet u uitgaven voor een afdeling met waarschuwings Details.

![Voorbeeld afbeelding met uitgaven voor een afdeling met waarschuwings Details](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Wanneer u de details van een genegeerde waarschuwing bekijkt, kunt u deze opnieuw activeren als hand matige actie vereist is. In de volgende afbeelding ziet u een voorbeeld.

![Voorbeeld afbeelding met opties voor negeren en opnieuw activeren](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Zie ook

- Als u nog geen budget hebt gemaakt of waarschuwings voorwaarden voor een budget hebt ingesteld, voltooit u de zelf studie voor het [maken en beheren](tutorial-acm-create-budgets.md) van budgetten.
