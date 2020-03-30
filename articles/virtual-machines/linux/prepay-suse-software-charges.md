---
title: Vooraf betalen voor softwareplannen - Azure-reserveringen
description: Ontdek hoe u vooraf betalen voor softwareplannen om geld te besparen op uw kosten voor betalen per gebruik.
author: bandersmsft
manager: yashesvi
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3bb7a62433993f1af26b1ce8bcb4ed258c34623c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973136"
---
# <a name="prepay-for-azure-software-plans"></a>Vooruitbetalen voor Azure-softwareabonnementen

Wanneer u vooraf betaalt voor uw SUSE- en RedHat-softwaregebruik in Azure, u geld besparen op uw kosten voor betalen per gebruik. De kortingen gelden alleen voor SUSE- en RedHat-meters en niet voor het gebruik van de virtuele machine. U reserveringen voor virtuele machines apart kopen voor extra besparingen.

U Softwareplannen voor SUSE en RedHat kopen in de Azure-portal. Ga als lid van het volgende over een abonnement:

- U moet de eigenaarrol hebben voor ten minste één Enterprise- of individueel abonnement met betalen per gebruik.
- Voor Enterprise-abonnementen moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in [EA Portal](https://ea.azure.com/). Als de instelling is uitgeschakeld, moet u een EA-beheerder voor het abonnement zijn.
- Voor het CSP-programma (Cloud Solution Provider) kunnen de beheerders of verkoopagenten de softwareplannen kopen.

## <a name="buy-a-software-plan"></a>Een softwareplan kopen

1. Meld u aan bij de Azure-portal en ga naar [Reserveringen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Klik **op Toevoegen** en selecteer vervolgens het softwareplan dat u wilt kopen.
Vul de vereiste velden in. Elke SUSE Linux VM of RedHat VM die overeenkomt met de attributen van wat je koopt krijgt de korting. Het werkelijke aantal implementaties dat de korting krijgt, is afhankelijk van het geselecteerde bereik en het geselecteerde aantal.
3. Selecteer een abonnement. Het wordt gebruikt om het plan te betalen.
De betaalmethode voor het abonnement wordt de aanloopkosten voor de reservering in rekening gebracht. Het abonnementstype moet een Enterprise Agreement zijn (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P) of een individuele overeenkomst met pay-as-you-go-prijzen (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P).
    - Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het toezeggingsbedrag of ze worden in rekening gebracht als overschrijding.
    - Voor een individueel abonnement met betalen per gebruik worden de kosten in rekening gebracht op de creditcard of factuurbetalingsmethode van het abonnement.
4. Selecteer een bereik. Het bereik kan betrekking hebben op één abonnement of meerdere abonnementen (gedeelde scope).
    - Eén abonnement - De abonnementskorting wordt toegepast op het overeenkomenvan het gebruik in het abonnement.
    - Gedeeld - De abonnementskorting wordt toegepast op overeenkomende exemplaren in een abonnement in uw factureringscontext. Voor zakelijke klanten is de factureringscontext de inschrijving en omvat alle abonnementen in de inschrijving. Voor een individueel abonnement met klanten met een vergoeding per gebruik, is de factureringscontext alle afzonderlijke abonnementen met abonnementen op betalen per gebruik die door de accountbeheerder zijn gemaakt.
5. Selecteer een product om de VM-grootte en het afbeeldingstype te kiezen. De korting is alleen van toepassing op de geselecteerde VM-grootte.
6. Selecteer een termijn van één jaar of drie jaar.
7. Kies een aantal, het aantal prepaid VM-exemplaren dat de factureringskorting kan krijgen.
8. Voeg het product toe aan de winkelwagen, bekijk en koop.

De reserveringskorting wordt automatisch toegepast op de softwaremeter waarvoor u vooraf betaalt. VM-rekenkosten worden niet gedekt door het plan. U de VM-reserveringen apart aanschaffen.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Korting geldt voor verschillende SUSE VM-formaten

Net als gereserveerde VM-exemplaren bieden SUSE Linux-abonnementen flexibiliteit in instantiegrootte. Uw korting is zelfs van toepassing wanneer u een VM implementeert die een andere grootte heeft dan het SUSE-abonnement dat u hebt gekocht. Zie [Begrijpen hoe de korting op het softwareplan wordt toegepast](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)voor meer informatie.

## <a name="redhat-plan-discount"></a>RedHat plan korting

Plannen zijn alleen beschikbaar voor Red Hat Enterprise Linux virtuele machines. De korting is niet van toepassing op RedHat Enterprise Linux SAP HANA VM's of RedHat Enterprise Linux SAP Business Apps VM's.

Kortingen in het RedHat-abonnement zijn alleen van toepassing op de VM-grootte die u op het moment van aankoop selecteert. RHEL-abonnementen kunnen niet worden terugbetaald of geruild na aankoop.


## <a name="cancellation-and-exchanges-not-allowed"></a>Annulering en uitwisselingen niet toegestaan

U een SUSE- of RedHat-abonnement dat u hebt gekocht, niet annuleren of inruilen. Controleer uw gebruik om er zeker van te zijn dat u het juiste abonnement koopt. Zie [Begrijpen hoe de korting op het softwareplan wordt toegepast](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)voor hulp om te bepalen wat u moet kopen.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [maakt u een ondersteuningsverzoek.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-reserveringen beheren](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)voor meer informatie over het beheren van een reservering.

Zie voor meer informatie de volgende artikelen:

- [Wat zijn Azure-reserveringen?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Reserveringen beheren in Azure](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Begrijpen hoe de SUSE-reserveringskorting wordt toegepast](../../cost-management-billing/reservations/understand-suse-reservation-charges.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
