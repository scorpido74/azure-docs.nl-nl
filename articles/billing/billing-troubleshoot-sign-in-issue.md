---
title: Problemen met aanmelding bij Azure-abonnementen oplossen
description: Helpt bij het oplossen van de problemen waarbij u zich niet kunt aanmelden bij het Azure Portal of het Azure-account centrum.
author: v-miegge
manager: na
editor: na
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: ca641813e8b01a39d31a56e3730424b0fa1d6436
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657045"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Problemen met aanmelding bij Azure-abonnementen oplossen

Deze hand leiding helpt u bij het oplossen van de problemen waarbij u zich niet kunt aanmelden bij de Azure Portal of het Azure-account centrum. 

## <a name="issues"></a>Problemen

### <a name="page-hangs-in-the-loading-status"></a>De pagina loopt vast in de laad status

Als uw Internet browser pagina vastloopt, voert u de volgende stappen uit totdat u naar de Azure Portal kunt gaan.

- Vernieuw de pagina.
- Gebruik een andere Internet browser.
- Gebruik de modus voor persoonlijke navigatie in uw browser. Voor Internet Explorer: Klik op **extra** > **veiligheid** > InPrivate-**Navigatie**en zoek naar het [Azure Portal](https://portal.azure.com/) of het [Azure-account centrum](https://account.azure.com/Subscriptions)en meld u aan.

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>U wordt automatisch aangemeld als een andere gebruiker

Dit probleem kan optreden als u meer dan één gebruikers account gebruikt in een Internet browser.

Probeer een van de volgende methoden om het probleem op te lossen:

- De cache wissen en Internet cookies verwijderen. Klik in Internet Explorer op **extra** > **Internet opties** > **verwijderen**. Zorg ervoor dat de selectie vakjes voor tijdelijke bestanden, cookies, wacht woord en browse geschiedenis zijn geselecteerd en klik vervolgens op verwijderen.
- Stel de instellingen van Internet Explorer opnieuw in om persoonlijke instellingen die u hebt gemaakt, te herstellen. Klik op **extra** > **Internet opties** > **Geavanceerd** > Schakel het selectie vakje **persoonlijke instellingen verwijderen** in > opnieuw in te **stellen**.
- Gebruik de modus voor persoonlijke navigatie in uw browser. Voor Internet Explorer:  Klik op **extra** > **veiligheid** > InPrivate-**Navigatie**en zoek naar het [Azure Portal](https://portal.azure.com/) of het [Azure-account centrum](https://account.azure.com/Subscriptions)en meld u aan.

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Ik kan me aanmelden, maar ik zie *geen abonnementen* .

Dit probleem treedt op als u hebt geselecteerd op de verkeerde directory, of als uw account beschikt niet over voldoende machtigingen.

**Scenario 1:** Er wordt een fout bericht ontvangen in de [Azure Portal](https://portal.azure.com/)

Dit probleem oplossen:

- Zorg ervoor dat de juiste Azure-map is geselecteerd door te klikken op uw account in de rechterbovenhoek.
- Als de juiste Azure-Directory is geselecteerd, maar u nog steeds het fout bericht ontvangt, moet uw account worden [toegevoegd als een eigenaar](billing-add-change-azure-subscription-administrator.md).

**Scenario 2:** Er wordt een fout bericht ontvangen in de [Azure-Accountcentrum](https://account.windowsazure.com/Subscriptions)

Controleer of het account dat u gebruikt de accountbeheerder. Om te controleren wie de accountbeheerder is, de volgende stappen uit:

1. Aanmelden bij de [abonnementen weergeven in Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selecteer het abonnement dat u wilt controleren, en ga vervolgens naar **instellingen**.

3. Selecteer **eigenschappen**. De accountbeheerder voor het abonnement wordt weergegeven in de **accountbeheerder** vak.

## <a name="additional-help-resources"></a>Aanvullende hulp bronnen

Andere artikelen over het oplossen van problemen met Azure-facturering en-abonnementen

- [Geweigerde kaart](billing-troubleshoot-declined-card.md)
- [Problemen bij het registreren van abonnementen](billing-troubleshoot-azure-sign-up.md)
- [Geen abonnementen gevonden](billing-no-subscriptions-found.md)
- [Weergave voor bedrijfskosten is uitgeschakeld](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Neem contact met ons op voor hulp

Als u vragen hebt of hulp nodig hebt, [Maak een ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor Azure billing](index.md)
