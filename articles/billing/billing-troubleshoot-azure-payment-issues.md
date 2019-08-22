---
title: Problemen met Azure-betalingen oplossen
description: Oplossen van een probleem bij het bijwerken van het betalings informatie account in de Microsoft Azure-portal of het account centrum.
author: v-miegge
manager: na
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: jaserano
ms.openlocfilehash: 45f6f181ddbf7e76662f76c433d8ff68c18f0de1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657084"
---
# <a name="troubleshoot-azure-payment-issues"></a>Problemen met Azure-betalingen oplossen

Er kan een probleem optreden wanneer u probeert om het account met betalings gegevens bij te werken in het Microsoft Azure-portal of het Azure-account centrum. Houd rekening met de volgende richt lijnen voordat u het probleem oplost:

- Zorg ervoor dat de gegevens die u hebt ingevoerd voor uw Azure-account profiel (inclusief het e-mail adres van de contact persoon, het adres en het telefoon nummer) juist zijn.
- Controleer of de creditcardgegevens juist zijn.
- Controleer of u niet al een Microsoft-account hebt met dezelfde gegevens.

## <a name="issues"></a>Problemen

Als u eventuele fouten wilt oplossen, selecteert u het probleem dat optreedt wanneer u zich probeert aan te melden voor Azure.

### <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Een credit card kan niet worden verwijderd uit een opgeslagen betalings methode

U kunt het ontwerp van een credit card niet verwijderen uit het actieve abonnement.

Als u een bestaande kaart wilt verwijderen, moet er een nieuwe kaart aan het abonnement worden toegevoegd, zodat het oude betalings instrument kan worden verwijderd of u moet het abonnement annuleren. Hiermee verwijdert u het abonnement permanent en verwijdert u de kaart.

### <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>U kunt een oude Betalings wijze niet verwijderen nadat u een nieuwe betalings methode hebt toegevoegd

Het nieuwe betaalmiddel is mogelijk niet gekoppeld aan het abonnement. Zie [een credit card of bankpas voor Azure toevoegen, bijwerken of verwijderen](billing-how-to-change-credit-card.md)voor meer informatie over het koppelen van het betaal middel aan het abonnement.

Zie problemen met [geweigerde kaarten oplossen bij Azure-aanmelding](billing-troubleshoot-declined-card.md)voor meer informatie over het oplossen van problemen met een geweigerde kaart.

### <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Kan geen Betalings wijze verwijderen vanwege een fout bij het verwijderen van de *Betalings wijze*

Dit gebeurt vanwege een openstaand saldo. Wis openstaande saldi voordat u de betalings methode verwijdert.

### <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Kan abonnementen onder mijn account niet zien om de betalings wijze bij te werken

U gebruikt mogelijk een e-mail-ID die afwijkt van de naam die wordt gebruikt voor de abonnementen.

Als u dit probleem wilt oplossen, raadpleegt u [geen abonnementen gevonden aanmeldings fout voor Azure portal of het Azure-account centrum](billing-no-subscriptions-found.md).

### <a name="unable-to-make-payment-for-a-subscription"></a>Kan geen betaling voor een abonnement maken

Als het volgende fout bericht wordt weer gegeven: *De betaling is te laat. Er is een probleem met uw betalings wijze* of *de informatie kan niet worden opgeslagen. Sluit de browser en probeer het opnieuw.* , is er een betaling in behandeling op de kaart omdat de kaart is afgewezen door uw financiële instelling.

Controleer of de credit card voldoende saldo heeft om een betaling te doen. Als dat niet het geval is, gebruikt u een andere kaart om de betaling uit te voeren of neemt u contact op met uw financiële instelling om het probleem op te lossen.

Neem contact op met uw bank voor de volgende problemen:

- Internationale trans acties zijn niet ingeschakeld.
- De kaart heeft een krediet limiet en het saldo moet worden vereffend.
- Er is een terugkerende betaling ingeschakeld op de kaart.

### <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>De betalings wijze kan niet worden gewijzigd vanwege browser problemen (de browser reageert niet, wordt niet geladen, enzovoort)

Meld u af bij alle actieve Azure-sessies en volg de stappen in het [artikel bladeren in InPrivate in micro soft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) om een InPrivate-sessie in micro soft Edge of Internet Explorer te starten.

Volg in de privé sessie de stappen voor het [wijzigen van een credit card](billing-how-to-change-credit-card.md) om de creditcard gegevens bij te werken of te wijzigen.

U kunt ook proberen het volgende te doen:

- Uw browser vernieuwen
- Een andere browser gebruiken
- Cookies in de cache verwijderen

### <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>Mijn abonnement is nog steeds uitgeschakeld na het bijwerken van de betalings wijze.

Dit probleem treedt op vanwege een openstaand saldo. Wis openstaande saldi voordat u de betalings methode verwijdert.

### <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>De betalings wijze kan niet worden gewijzigd vanwege een XML-fout pagina

U ontvangt dit bericht als u [de Azure Portal](https://portal.azure.com/) gebruikt om een nieuwe credit card toe te voegen.

Meld u aan bij de Azure-account Portal met behulp van het e-mail adres van de account beheerder om kaart gegevens toe te voegen.

## <a name="additional-help-resources"></a>Aanvullende hulp bronnen

Andere artikelen over het oplossen van problemen met Azure-facturering en-abonnementen

- [Geweigerde kaart](billing-troubleshoot-declined-card.md)
- [Aanmeldings problemen voor abonnementen](billing-troubleshoot-sign-in-issue.md)
- [Geen abonnementen gevonden](billing-no-subscriptions-found.md)
- [Weergave voor bedrijfskosten is uitgeschakeld](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Neem contact met ons op voor hulp

Als u vragen hebt of hulp nodig hebt, [Maak een ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor Azure billing](index.md)
