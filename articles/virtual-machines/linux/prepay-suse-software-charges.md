---
title: Vooruitbetalen voor software plannen-Azure Reservations
description: Meer informatie over hoe u kunt indrukken voor software plannen om geld te besparen op basis van uw betalen naar gebruik-kosten.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: e4cab8963e3083d265d0254c81b78fbabb84a269
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091528"
---
# <a name="prepay-for-azure-software-plans"></a>Vooruitbetalen voor Azure-softwareabonnementen

Wanneer u het gebruik van uw SUSE-en RedHat-software in azure promijnert, kunt u geld besparen via uw betalen naar gebruik-kosten. De kortingen zijn alleen van toepassing op SUSE-en RedHat-meters en niet op het gebruik van de virtuele machine. U kunt reserve ringen voor virtuele machines afzonderlijk kopen voor extra besparingen.

U kunt de software plannen van SUSE en RedHat kopen in de Azure Portal. Een abonnement kopen:

- U moet de rol eigenaar hebben voor ten minste één ondernemings-of individueel abonnement met betalen per gebruik-prijzen.
- Voor ondernemings abonnementen moet de optie **gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA-Portal](https://ea.azure.com/). Als de instelling is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement.
- Voor het programma Cloud Solution Provider (CSP) kunnen de beheerders of verkoop medewerkers de software plannen kopen.

## <a name="buy-a-software-plan"></a>Een software abonnement kopen

1. Meld u aan bij de Azure Portal en ga naar [reserve ringen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Klik op **toevoegen** en selecteer vervolgens het software abonnement dat u wilt kopen.
Vul de vereiste velden in. Elke SUSE Linux VM-of RedHat-VM die overeenkomt met de kenmerken van wat u koopt, krijgt de korting. Het werkelijke aantal implementaties dat de korting krijgt, is afhankelijk van het bereik en de geselecteerde hoeveelheid.
3. Selecteer een abonnement. Het wordt gebruikt voor het betalen van het abonnement.
Voor de betalings wijze van het abonnement worden de kosten vooraf in rekening gebracht voor de reserve ring. Het abonnements type moet een Enterprise Agreement zijn (aantal aanbiedingen: MS-AZR-0017P of MS-AZR-0148P) of afzonderlijke overeenkomst met betalen per gebruik-prijs (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P).
    - Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het toezeggingsbedrag of ze worden in rekening gebracht als overschrijding.
    - Voor een individueel abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht voor de credit card van het abonnement of de betalings wijze van de factuur.
4. Selecteer een bereik. Het bereik kan één abonnement of meerdere abonnementen beslaan (gedeeld bereik).
    - Eén abonnement: de plan korting wordt toegepast op het overeenkomende gebruik in het abonnement.
    - Gedeeld: de plan korting wordt toegepast op overeenkomende exemplaren in een abonnement in uw facturerings context. Voor zakelijke klanten is de facturerings context de inschrijving en omvat alle abonnementen in de inschrijving. Voor een afzonderlijk abonnement met betalen per gebruik-prijzen, is de facturerings context alle afzonderlijke abonnementen met prijzen voor betalen per gebruik die door de account beheerder zijn gemaakt.
5. Selecteer een product om de VM-grootte en het type installatie kopie te kiezen. De korting geldt alleen voor de geselecteerde VM-grootte.
6. Selecteer een periode van één of drie jaar.
7. Kies een hoeveelheid. Dit is het aantal vooraf betaalde VM-exemplaren dat de factuur korting kan ophalen.
8. Het product toevoegen aan de winkel wagen, bekijken en kopen.

De reserverings korting wordt automatisch toegepast op de software meter waarvoor u vooraf betaalt voor. Kosten voor VM-berekeningen vallen niet onder het plan. U kunt de VM-reserve ringen afzonderlijk aanschaffen.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Korting is van toepassing op verschillende SUSE VM-grootten

Zoals gereserveerde VM-exemplaren, bieden SUSE Linux-abonnementen de flexibiliteit van instantie grootte. Uw korting is zelfs van toepassing wanneer u een VM implementeert die een andere grootte heeft dan het SUSE-abonnement dat u hebt aangeschaft. Zie [begrijpen hoe de korting voor software plannen wordt toegepast](../../billing/billing-understand-suse-reservation-charges.md)voor meer informatie.

## <a name="redhat-plan-discount"></a>RedHat-plan korting

Plannen zijn alleen beschikbaar voor virtuele machines van Red Hat Enterprise Linux. De korting is niet van toepassing op Red Hat Enter prise Linux SAP HANA Vm's of RedHat Enter prise Linux SAP Business Apps Vm's.

RedHat-abonnement kortingen gelden alleen voor de grootte van de virtuele machine die u selecteert op het moment van aankoop. RHEL-abonnementen kunnen niet worden gerestitueerd of uitgewisseld na de aankoop.


## <a name="cancellation-and-exchanges-not-allowed"></a>Annulering en uitwisseling niet toegestaan

U kunt een SUSE-of RedHat-abonnement dat u hebt gekocht, niet annuleren of uitwisselen. Controleer uw gebruik om er zeker van te zijn dat u het juiste abonnement koopt. Zie [begrijpen hoe de korting van het software plan wordt toegepast](../../billing/billing-understand-suse-reservation-charges.md)voor hulp bij het bepalen van wat u wilt kopen.

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [Maak een ondersteuningsaanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-reserve ringen beheren](../../billing/billing-manage-reserved-vm-instance.md)voor meer informatie over het beheren van een reserve ring.

Raadpleeg de volgende artikelen voor meer informatie:

- [Wat zijn Azure Reservations?](../../billing/billing-save-compute-costs-reservations.md)
- [Reserve ringen in azure beheren](../../billing/billing-manage-reserved-vm-instance.md)
- [Begrijpen hoe de SUSE-reserverings korting wordt toegepast](../../billing/billing-understand-suse-reservation-charges.md)
- [Het gebruik van de reserve ring begrijpen voor uw abonnement voor betalen naar gebruik](../../billing/billing-understand-reserved-instance-usage.md)
- [Het gebruik van de reserve ring begrijpen voor uw Enter prise-inschrijving](../../billing/billing-understand-reserved-instance-usage-ea.md)
