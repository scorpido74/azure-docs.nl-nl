---
title: Bestedingslimiet van Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe een bestedingslimiet van Azure werkt en hoe u deze kunt verwijderen.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: banders
ms.openlocfilehash: 97e6e04bf7fab3a48f1ce0552e1b653b208be77b
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375440"
---
# <a name="azure-spending-limit"></a>Bestedingslimiet voor Azure

Met de bestedingslimiet in Azure voorkomt u dat u uw tegoed overschrijdt. Voor alle nieuwe klanten die zich aanmelden voor een gratis Azure-account of voor een abonnement met tegoeden verspreid over een aantal maanden, is de bestedingslimiet standaard ingeschakeld. De bestedingslimiet is gelijk aan de hoeveelheid van het tegoed en kan niet worden gewijzigd. Als u zich bijvoorbeeld hebt geregistreerd voor een gratis Azure-account, is uw bestedingslimiet $ 200 en kunt u deze niet wijzigen in $ 500. U kunt de bestedingslimiet echter wel verwijderen. U hebt dus geen limiet of u hebt een limiet die gelijk is aan de hoeveelheid van het tegoed. Hierdoor kunt u de meeste soorten uitgaven niet wijzigen. De bestedingslimiet is niet beschikbaar voor abonnementen met toezeggingsplannen en tarieven voor betalen per gebruik. Zie de [volledige lijst met Azure-abonnementstypen en de beschikbaarheid van de bestedingslimiet](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Een bestedingslimiet bereiken

Wanneer uw gebruik leidt tot kosten die hoger zijn dan uw bestedingslimiet, worden de services waarvan u gebruikt hebt gemaakt voor de rest van die factureringsperiode uitgeschakeld.

Als u bijvoorbeeld al het tegoed van uw gratis Azure-account besteedt, worden de Azure-resources die u hebt geïmplementeerd, verwijderd uit productie en worden uw virtuele Azure-machines gestopt en opnieuw toegewezen. De gegevens in uw opslagaccounts zijn beschikbaar als alleen-lezen.

Als uw type abonnement tegoeden heeft die verspreid zijn over meerdere maanden, wordt uw abonnement automatisch opnieuw ingeschakeld aan het begin van de volgende factureringsperiode. Vervolgens kunt u uw Azure-resources opnieuw implementeren en beschikt u over volledige toegang tot uw opslagaccounts en databases.

Azure verzendt e-mailmeldingen wanneer u de bestedingslimiet bereikt. Meld u aan bij de [Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) om meldingen te zien over abonnementen waarvan de bestedingslimiet is bereikt.

Als u een gratis Azure-account hebt en de bestedingslimiet bereikt, kunt u een upgrade uitvoeren naar tarieven voor [betalen per gebruik](billing-upgrade-azure-subscription.md) om de bestedingslimiet te verwijderen en uw abonnement automatisch weer in te schakelen.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-azure-portal"></a>De bestedingslimiet verwijderen in de Azure-portal

U kunt de bestedingslimiet op elk gewenst moment verwijderen zolang er een geldige betaalmethode aan uw Azure-abonnement is gekoppeld. Voor abonnementstypen met tegoeden die zijn verspreid over meerdere maanden, zoals Visual Studio Enterprise en Visual Studio Professional, kunt u de bestedingslimiet ook inschakelen aan het begin van uw volgende factureringsperiode.

Ga als volgt te werk om de bestedingslimiet te verwijderen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecteer een abonnement. Als de bestedingslimiet van het abonnement is bereikt, wordt het abonnement uitgeschakeld.
1. Selecteer **Bestedingslimiet verwijderen** boven aan de pagina.
1. Selecteer een voor u geschikte optie.

![Een optie selecteren om de bestedingslimiet te verwijderen](./media/billing-spending-limit/remove-spending-limit.PNG)

| Optie | Effect |
| --- | --- |
| Bestedingslimiet voor onbepaalde tijd verwijderen | Hiermee wordt de bestedingslimiet verwijderd zonder dat deze aan het begin van de volgende factureringsperiode automatisch weer wordt ingeschakeld. |
| Bestedingslimiet voor de huidige factureringsperiode verwijderen | Hiermee wordt de bestedingslimiet verwijderd waarna deze automatisch weer wordt ingeschakeld aan het begin van de volgende factureringsperiode. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Waarom u de bestedingslimiet zou willen verwijderen

De bestedingslimiet kan ervoor zorgen dat u bepaalde Microsoft-services en services van derden niet meer kunt implementeren of gebruiken. Hieronder ziet u de situaties waarin u het beste de bestedingslimiet van uw abonnement kunt verwijderen.

-  U bent van plan eigen installatiekopieën als Oracle of services als Azure DevOps Services te implementeren. In deze situatie bereikt u bijna meteen de bestedingslimiet, waardoor uw abonnement wordt uitgeschakeld.
- U hebt services die u niet wilt onderbreken. Wanneer u de bestedingslimiet bereikt, worden Azure-resources die u hebt geïmplementeerd uit de productie genomen. Ook worden uw virtuele machines van Azure stopgezet en wordt de toewijzing ervan ongedaan gemaakt. Als u services hebt die u niet wilt onderbreken, moet u de bestedingslimiet verwijderen.
- U hebt services en bronnen met instellingen zoals virtuele IP-adressen die u niet wilt kwijtraken. Deze instellingen gaan verloren wanneer u de bestedingslimiet bereikt en de toewijzing van de services en bronnen ongedaan wordt gemaakt.

## <a name="turn-on-the-spending-limit-after-removing"></a>De bestedingslimiet inschakelen na het verwijderen

Deze functie is alleen beschikbaar wanneer de bestedingslimiet voor onbepaalde tijd is verwijderd voor abonnementstypen met tegoeden die zijn verspreid over meerdere maanden. U kunt deze functie gebruiken om uw bestedingslimiet automatisch aan het begin van de volgende factureringsperiode in te schakelen.

1. Meld u aan bij het [Accountcentrum](https://account.windowsazure.com/Subscriptions).
1. Klik op de gele banner om de optie voor de bestedingslimiet te wijzigen.
1. Kies **Bestedingslimiet inschakelen in de volgende factureringsperiode \<begindatum van de factureringsperiode\>**

## <a name="custom-spending-limit"></a>Aangepast bestedingslimiet

Aangepaste bestedingslimieten zijn niet beschikbaar.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Een bestedingslimiet voorkomt niet alle kosten

[Sommige externe services die zijn gepubliceerd in Azure Marketplace](billing-understand-your-azure-marketplace-charges.md) kunnen niet worden gebruikt met uw abonnementstegoeden en er kunnen afzonderlijke kosten voor in rekening worden gebracht, zelfs wanneer uw bestedingslimiet is ingesteld. Voorbeelden hiervan zijn Visual Studio-licenties, Azure Active Directory Premium, ondersteuningsplannen en de meeste merkservices van derden. Wanneer u een nieuwe externe service inricht, wordt er een waarschuwing weergegeven om u te laten weten dat de services afzonderlijk worden gefactureerd:

![Waarschuwing Marketplace-aankoop](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- Voer een upgrade uit naar abonnement met prijzen voor [betalen per gebruik](billing-upgrade-azure-subscription.md).
