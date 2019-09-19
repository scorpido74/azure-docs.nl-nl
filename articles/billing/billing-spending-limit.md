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
ms.openlocfilehash: 1324b60de05805cd409975358ff78e5b2b27eef1
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70114439"
---
# <a name="azure-spending-limit"></a>Bestedingslimiet voor Azure

Met de bestedingslimiet in Azure voorkomt u dat u uw tegoed overschrijdt. Voor alle nieuwe klanten die zich aanmelden voor een Azure-proefversie of voor aanbiedingen met tegoeden verspreid over een aantal maanden, is de bestedingslimiet standaard ingeschakeld. De bestedingslimiet is $ 0 en deze kan niet worden gewijzigd. U kunt de bestedingslimiet bijvoorbeeld niet wijzigen in $ 100. U kunt de bestedingslimiet echter wel verwijderen. U hebt dus ofwel geen limiet ofwel u hebt een limiet van nul die allerlei uitgaven voorkomt. De bestedingslimiet is niet beschikbaar voor abonnementen onder sommige plannen, zoals toezeggingsplannen en plannen met betalen per gebruik-tarieven. Zie de [volledige lijst met Azure-aanbiedingen en de beschikbaarheid van de bestedingslimiet](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Een bestedingslimiet bereiken

Wanneer uw gebruik leidt tot kosten die hoger zijn dan de maandelijkse tegoeden van uw Azure-abonnement, worden de services waarvan u gebruikt hebt gemaakt voor de rest van die factureringsperiode uitgeschakeld.

Als u bijvoorbeeld al het tegoed van uw abonnement besteedt, worden de Azure-resources die u hebt geïmplementeerd, verwijderd uit productie en worden uw virtuele Azure-machines gestopt en opnieuw toegewezen. De gegevens in uw opslagaccounts zijn beschikbaar als alleen-lezen.

Aan het begin van de volgende factureringsperiode wordt uw abonnement automatisch opnieuw ingeschakeld als uw abonnement tegoeden heeft die verspreid zijn over meerdere maanden. Vervolgens kunt u uw Azure-resources opnieuw implementeren en beschikt u over volledige toegang tot uw opslagaccounts en databases.

Azure verzendt e-mailmeldingen wanneer u de bestedingslimiet voor uw abonnement bereikt. Meldt u aan bij [Accountcentrum](https://account.windowsazure.com/Subscriptions) om meldingen te zien over abonnementen waarvan de bestedingslimiet is bereikt.

Als u een gratis proefabonnement hebt en de bestedingslimiet hebt bereikt, kunt u een upgrade uitvoeren naar een plan met tarieven voor [betalen per gebruik](billing-upgrade-azure-subscription.md) om de bestedingslimiet te verwijderen en het abonnement automatisch in te schakelen.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>De bestedingslimiet verwijderen in Accountcentrum

U kunt de bestedingslimiet op elk gewenst moment verwijderen zolang er een geldige betaalmethode aan uw Azure-abonnement is gekoppeld. Voor aanbiedingen met tegoeden verspreid over een aantal maanden kunt u de bestedingslimiet ook aan het begin van de volgende factureringsperiode inschakelen.

Ga als volgt te werk om de bestedingslimiet te verwijderen:

1. Meld u aan bij het [Accountcentrum](https://account.windowsazure.com/Subscriptions).
1. Selecteer een abonnement. Als het abonnement is uitgeschakeld omdat de bestedingslimiet is bereikt, klikt u op de melding: **De bestedingslimiet van het abonnement is bereikt. Het abonnement is uitgeschakeld om verdere onkosten te voorkomen.** Als dat niet het geval is, klikt u in het gebied **ABONNEMENTSSTATUS** op **Bestedingslimiet verwijderen**.
1. Selecteer een voor u geschikte optie.

![Een optie selecteren om de bestedingslimiet te verwijderen](./media/billing-spending-limit/remove-spending-limit.PNG)

| Optie | Effect |
| --- | --- |
| Bestedingslimiet voor onbepaalde tijd verwijderen | Hiermee wordt de bestedingslimiet verwijderd zonder dat deze aan het begin van de volgende factureringsperiode automatisch weer wordt ingeschakeld. |
| Bestedingslimiet voor de huidige factureringsperiode verwijderen | Hiermee wordt de bestedingslimiet verwijderd waarna deze automatisch weer wordt ingeschakeld aan het begin van de volgende factureringsperiode. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Waarom u de bestedingslimiet zou willen verwijderen

De bestedingslimiet kan ervoor zorgen dat u bepaalde Microsoft-services en services van derden niet meer kunt implementeren of gebruiken. Hieronder ziet u de situaties waarin u het beste de bestedingslimiet van uw abonnement kunt verwijderen.

-  U bent van plan eigen installatiekopieën als Oracle en services als Azure DevOps Services te implementeren. In deze situatie overschrijdt u bijna meteen de bestedingslimiet, waardoor uw abonnement wordt uitgeschakeld.
- U hebt services die u niet wilt onderbreken.
- U hebt services en bronnen met instellingen zoals virtuele IP-adressen die u niet wilt kwijtraken. Deze instellingen gaan verloren wanneer de toewijzing van de services en bronnen ongedaan wordt gemaakt.

## <a name="turn-on-the-spending-limit-after-removing"></a>De bestedingslimiet inschakelen na het verwijderen

Deze functie is alleen beschikbaar wanneer de bestedingslimiet voor onbepaalde tijd is verwijderd. Wijzig deze om deze optie automatisch aan het begin van de volgende factureringsperiode in te schakelen.

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
