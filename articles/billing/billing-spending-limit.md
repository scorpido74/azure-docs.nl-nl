---
title: Bestedings limiet van Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe een bestedings limiet van Azure werkt en hoe u deze kunt verwijderen.
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
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114439"
---
# <a name="azure-spending-limit"></a>Bestedings limiet van Azure

De bestedings limiet in azure voor komt dat uw tegoed wordt gespendeerd. Alle nieuwe klanten die zich aanmelden voor een proef versie van Azure of aanbiedingen met een tegoed van meer dan een maand, hebben de bestedings limiet standaard ingeschakeld. De bestedings limiet is $0 en kan niet worden gewijzigd. U kunt de bestedings limiet bijvoorbeeld niet wijzigen in $100. U kunt de bestedings limiet echter wel verwijderen. U hebt dus geen limiet of u hebt een limiet van nul die voor komt dat u uit de meeste soorten uitgaven kunt kiezen. De bestedings limiet is niet beschikbaar voor abonnementen onder sommige plannen van dergelijke toezeggings plannen en plannen met betalen per gebruik-tarieven. Bekijk de [volledige lijst met Azure-aanbiedingen en de beschik baarheid van de bestedings limiet](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Een bestedings limiet bereiken

Wanneer uw gebruik leidt tot kosten die de maandelijkse bedragen die zijn opgenomen in uw Azure-abonnement, worden de services die u hebt geïmplementeerd, uitgeschakeld voor de rest van die facturerings periode.

Als u bijvoorbeeld alle tegoed van uw abonnement besteedt, worden de Azure-resources die u hebt geïmplementeerd, verwijderd uit productie en worden uw virtuele Azure-machines gestopt en opnieuw toegewezen. De gegevens in uw opslag accounts zijn beschikbaar als alleen-lezen.

Aan het begin van de volgende facturerings periode wordt uw abonnement automatisch opnieuw ingeschakeld als uw abonnement een tegoed heeft van meer dan een maand. Vervolgens kunt u uw Azure-resources opnieuw implementeren en beschikt u over volledige toegang tot uw opslag accounts en data bases.

Azure verzendt e-mail meldingen wanneer u de bestedings limiet voor uw abonnement bereikt. Meld u aan bij het [account centrum](https://account.windowsazure.com/Subscriptions) om meldingen te bekijken over abonnementen die de bestedings limiet hebben bereikt.

Als u een gratis proef abonnement hebt en de bestedings limiet hebt bereikt, kunt u een upgrade uitvoeren naar een abonnement met [betalen per gebruik-](billing-upgrade-azure-subscription.md) tarieven om de bestedings limiet te verwijderen en het abonnement automatisch in te scha kelen.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>De bestedings limiet in het account centrum verwijderen

U kunt de bestedings limiet op elk gewenst moment verwijderen, zolang er een geldige betalings methode is gekoppeld aan uw Azure-abonnement. Voor aanbiedingen met een tegoed van meer dan één maand kunt u de bestedings limiet ook inschakelen aan het begin van de volgende facturerings periode.

Ga als volgt te werk om de bestedingslimiet te verwijderen:

1. Meld u aan bij het [account centrum](https://account.windowsazure.com/Subscriptions).
1. Selecteer een abonnement. Als het abonnement is uitgeschakeld omdat de bestedings limiet is bereikt, klikt u op de melding: **Het abonnement heeft de bestedings limiet bereikt en is uitgeschakeld om kosten te voor komen.** Klik anders op **bestedings limiet verwijderen** in het gedeelte **abonnement status** .
1. Selecteer een voor u geschikte optie.

![Een optie selecteren om de bestedings limiet te verwijderen](./media/billing-spending-limit/remove-spending-limit.PNG)

| Optie | Effect |
| --- | --- |
| Uitgavenlimiet voor onbepaalde tijd verwijderen | Hiermee wordt de bestedingslimiet verwijderd, zonder dat deze automatisch weer wordt ingeschakeld aan het begin van de volgende factureringsperiode. |
| Bestedingslimiet voor de huidige factureringsperiode verwijderen | Hiermee wordt de bestedingslimiet verwijderd, waarna deze automatisch weer wordt ingeschakeld aan het begin van de volgende factureringsperiode. |

## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Waarom u de bestedings limiet wilt verwijderen

De bestedings limiet kan ertoe leiden dat u bepaalde services van derden en micro soft niet kunt implementeren of gebruiken. Hier volgen de situaties waarin u de bestedings limiet voor uw abonnement moet verwijderen.

-  U bent van plan om installatie kopieën van de eerste partij, zoals Oracle en services, zoals Azure DevOps Services, te implementeren. Deze situatie zorgt ervoor dat u de bestedings limiet bijna onmiddellijk kunt overschrijden en zorgt ervoor dat uw abonnement wordt uitgeschakeld.
- U hebt services die u niet wilt onderbreken.
- U hebt services en bronnen met instellingen zoals virtuele IP-adressen die u niet wilt kwijtraken. Deze instellingen gaan verloren wanneer de toewijzing van de services en bronnen ongedaan wordt gemaakt.

## <a name="turn-on-the-spending-limit-after-removing"></a>De bestedings limiet inschakelen na het verwijderen

Deze functie is alleen beschikbaar wanneer de bestedings limiet voor onbepaalde tijd is verwijderd. Wijzig deze optie om automatisch aan het begin van de volgende facturerings periode in te scha kelen.

1. Meld u aan bij het [account centrum](https://account.windowsazure.com/Subscriptions).
1. Klik op de gele banner om de optie voor de bestedings limiet te wijzigen.
1. Kies **bestedings \<limiet inschakelen in de volgende begin datum van de facturerings periode van facturerings periode\>**

## <a name="custom-spending-limit"></a>Aangepaste bestedings limiet

Aangepaste bestedings limieten zijn niet beschikbaar.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Een bestedings limiet voor komt niet alle kosten

[Sommige externe services die zijn gepubliceerd in azure Marketplace](billing-understand-your-azure-marketplace-charges.md) , kunnen niet worden gebruikt met uw abonnements tegoeden en kunnen afzonderlijke kosten in rekening worden gebracht, zelfs wanneer uw bestedings limiet is ingesteld. Voor beelden hiervan zijn Visual Studio-licenties, Azure Active Directory Premium, ondersteunings abonnementen en de meeste merk bare services van derden. Wanneer u een nieuwe externe service inricht, wordt er een waarschuwing weer gegeven om u te laten weten dat de services afzonderlijk worden gefactureerd:

![Waarschuwing voor kopen op Marketplace](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, kunt u [een ondersteunings aanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- Voer een upgrade uit naar een abonnement met prijzen voor [betalen naar gebruik](billing-upgrade-azure-subscription.md) .
