---
title: Aanmeldingsproblemen voor Azure-abonnementen oplossen
description: Hulp bij het oplossen van problemen waardoor u zich niet kunt aanmelden bij Azure Portal of het Azure-accountcentrum.
services: azure
author: v-miegge
manager: dcscontentpm
editor: na
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: 7b467e42553d992231f8f5d45afcff37f077a9d2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223399"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Aanmeldingsproblemen voor Azure-abonnementen oplossen

Deze handleiding helpt bij het oplossen van problemen waardoor u zich niet kunt aanmelden bij Azure Portal of het Azure-accountcentrum. 

## <a name="issues"></a>Problemen

### <a name="page-hangs-in-the-loading-status"></a>De pagina loopt vast tijdens de laadstatus

Als uw internetbrowserpagina vastloopt, voert u elk van de volgende stappen uit totdat de Azure-portal bereikbaar is.

- Vernieuw de pagina.
- Gebruik een andere internetbrowser.
- Gebruik de modus voor privé browsen van uw browser. Voor Internet Explorer: Klik op **Extra** > **Veiligheid** > **InPrivate-browsing** en navigeer naar en meld u aan bij [Azure Portal](https://portal.azure.com/) of het [Azure-accountcentrum](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>U wordt automatisch aangemeld als een andere gebruiker

Dit probleem kan optreden als u meer dan één gebruikersaccount gebruikt in een internetbrowser.

Probeer een van de volgende methoden om dit probleem op te lossen:

- Wis de cache en verwijder internetcookies. Klik in Internet Explorer op **Extra** > **Internetopties** > **Verwijderen**. Zorg ervoor dat de selectievakjes voor tijdelijke bestanden, cookies, wachtwoorden en de browsegeschiedenis zijn geselecteerd en klik vervolgens op Verwijderen.
- Stel de instellingen van Internet Explorer opnieuw in om eventuele persoonlijke instellingen die u hebt gemaakt, te herstellen. Klik op **Extra** > **Internetopties** > **Geavanceerd** > selecteer het vakje **Persoonlijke instellingen verwijderen** > **Opnieuw instellen**.
- Gebruik de modus voor privé browsen van uw browser. Voor Internet Explorer:  Klik op **Extra** > **Veiligheid** > **InPrivate-browsing** en navigeer naar en meld u aan bij [Azure Portal](https://portal.azure.com/) of het [Azure-accountcentrum](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>Ik kan me aanmelden, maar ik zie *Er zijn geen abonnementen gevonden*

Dit probleem treedt op als u de verkeerde map hebt geselecteerd of als uw account niet over voldoende machtigingen beschikt.

**Scenario 1:** Het foutbericht is ontvangen in [Azure Portal](https://portal.azure.com/)

Dit probleem oplossen:

- Zorg ervoor dat de juiste Azure-map is geselecteerd door in de rechterbovenhoek op uw account te klikken.
- Als de juiste Azure-map is geselecteerd maar u nog steeds het foutbericht ontvangt, moet uw account worden [toegevoegd als eigenaar](billing-add-change-azure-subscription-administrator.md).

**Scenario 2:** Het foutbericht is ontvangen in het [Azure-accountcentrum](https://account.windowsazure.com/Subscriptions)

Controleer of het account dat u gebruikt de accountbeheerder is. Voer de volgende stappen uit om te controleren wie de accountbeheerder is:

1. Meld u aan bij de [Abonnementenweergave in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selecteer het abonnement dat u wilt controleren en kijk vervolgens bij **Instellingen**.

3. Selecteer **Eigenschappen**. De accountbeheerder van het abonnement wordt weergegeven in het vak **Accountbeheerder**.

## <a name="additional-help-resources"></a>Aanvullende ondersteuningsresources

Andere artikelen met probleemoplossingen voor Azure Billing en abonnementen

- [Geweigerde kaart](billing-troubleshoot-declined-card.md)
- [Registratieproblemen voor abonnementen](billing-troubleshoot-azure-sign-up.md)
- [Geen abonnementen gevonden](billing-no-subscriptions-found.md)
- [Weergave voor bedrijfskosten is uitgeschakeld](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contact met ons opnemen voor ondersteuning

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor Azure Billing](index.md)
