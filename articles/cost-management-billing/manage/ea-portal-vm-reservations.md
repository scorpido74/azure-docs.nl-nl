---
title: Gereserveerde exemplaren Azure EA-VM
description: In dit artikel wordt samengevat hoe u met Azure-reserveringen voor gereserveerde VM-instanties geld kunt besparen met uw Enterprise Enrollment.
author: bandersmsft
ms.author: banders
ms.date: 06/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 649766d5daedbf880a3c17a1cb8282b0a9e4f2fe
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299057"
---
# <a name="azure-ea-vm-reserved-instances"></a>Gereserveerde exemplaren Azure EA-VM

In dit artikel wordt samengevat hoe u met Azure-reserveringen voor gereserveerde VM-instanties geld kunt besparen met uw Enterprise Enrollment. Zie [Wat zijn Azure-reserveringen?](../reservations/save-compute-costs-reservations.md) voor meer informatie over reserveringen.

## <a name="reservation-exchanges-and-refunds"></a>Omruilen en restituties bij reserveringen

U kunt een reservering inwisselen voor een andere reservering van hetzelfde type. Het is ook mogelijk om een restitutie (tot USD 50.000 per jaar) voor een reservering aan te vragen als u deze niet meer nodig hebt. De Azure-portal kan worden gebruikt voor omruiling of terugbetaling van een reservering. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="reservation-costs-and-usage"></a>Reserveringskosten en gebruik

Enterprise Agreement-klanten kunnen kosten en gebruiksgegevens weergeven in Azure Portal en REST API's. Voor reserveringskosten en gebruik kunt u:

- Aankoopgegevens van reserveringen ophalen.
- Nagaan voor welke abonnementen, resourcegroepen of resources reserveringen zijn gebruikt.
- Reserveringsgebruik toerekenen.
- Reserveringsbesparingen berekenen.
- Gegevens over ondergebruikte reserveringen ophalen.
- Reserveringskosten afschrijven.

Zie [Kosten en gebruik van Enterprise Agreement-reserveringen ophalen](../reservations/understand-reserved-instance-usage-ea.md) voor meer informatie over reserveringskosten en gebruik.

Zie [Prijsinformatie - Linux Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) of [Prijsinformatie - Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) voor meer informatie over prijzen.

## <a name="reserved-instances-api-support"></a>Gereserveerde instanties en API-ondersteuning

U kunt Azure-API's gebruiken om programmatisch informatie voor uw organisatie te verkrijgen over de Azure-service of softwarereserveringen. Zie [API's voor automatisering van Azure-reserveringen](../reservations/reservation-apis.md) voor meer informatie.

## <a name="azure-reserved-virtual-machine-instances"></a>Voor Azure gereserveerde VM-instanties

Gereserveerde instanties kunnen de kosten voor uw virtuele machine beperken tot 72 procent van de prijzen voor betalen per gebruik voor alle VM's, of tot 82 procent wanneer het wordt gecombineerd met het hybride Azure-voordeel. Het is mogelijk om workloads voorrang te geven, te budgetteren en beter te voorspellen met vooruitbetaling voor jaarlijkse of driejaarlijkse termijnen. U kunt reserveringen ook uitwisselen of annuleren als uw bedrijfsbehoeften veranderen.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Gereserveerde instanties van virtuele machines aanschaffen

Als u een gereserveerde VM-instantie wilt aanschaffen, moet een beheerder van een zakelijke Azure-inschrijving de aankoopoptie _Instantie reserveren_ inschakelen in het gedeelte _Inschrijvingsdetails_ van het tabblad _Inschrijving_ in de [Azure EA-portal](https://ea.azure.com/).

Nadat de EA-inschrijving zo is ingesteld dat gereserveerde instanties kunnen worden toegevoegd, kan een accounteigenaar met een actief abonnement dat bij de EA-inschrijving hoort, een gereserveerde VM-instantie aanschaffen in de [Azure-portal](https://aka.ms/reservations). Raadpleeg [Vooruitbetalen voor virtuele machines en geld besparen met gereserveerde VM-instanties](https://go.microsoft.com/fwlink/?linkid=861721) voor meer informatie.

### <a name="how-to-view-reserved-instance-purchase-details"></a>Aankoopgegevens bekijken van gereserveerde instanties

U kunt de aankoopgegevens van uw gereserveerde instantie bekijken via het menu _Reservering_ aan de linkerzijde van de [Azure-portal](https://aka.ms/reservations) of via de [Azure EA-portal](https://ea.azure.com/). Selecteer **Rapporten** in het menu aan de linkerzijde en scrol naar beneden naar het gedeelte _Kosten per services_ in het tabblad _Gebruiksoverzicht_. Scrol naar de onderkant van het gedeelte, waar aankopen en gebruik van uw gereserveerde instanties worden vermeld met de aanduiding '1 jaar' of '3 jaar' naast de servicenaam, bijvoorbeeld: Standard_DS1_v2 eastus 1 jaar or Standard_D2s_v3 eastus2 3 jaar.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Hoe verander ik het abonnement dat bij de gereserveerde instantie hoort of hoe draag ik de voordelen van mijn gereserveerde instantie over aan een abonnement in hetzelfde account?

U verandert het abonnement dat de voordelen van gereserveerde instanties ontvangt als volgt:

- Aanmelden bij de [Azure-portal](https://aka.ms/reservations).
- Werk het toegepaste abonnementsbereik bij door een ander abonnement binnen hetzelfde account te koppelen.

Zie [Het reserveringsbereik wijzigen](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope) voor meer informatie over het wijzigen van het bereik van een reservering.

### <a name="how-to-view-reserved-instance-usage-details"></a>Gebruiksgegevens van gereserveerde instanties bekijken

U kunt de gebruiksgegevens van uw gereserveerde instanties bekijken in de [Azure-portal](https://aka.ms/reservations) of in de [Azure EA-portal](https://ea.azure.com/) (voor EA-klanten met toegang tot factureringsgegevens) in het gedeelte _Rapporten_ > _Gebruiksoverzicht_ > _Kosten per services_. Uw gereserveerde instanties kunnen worden geïdentificeerd als servicenamen met 'Reservering', bijvoorbeeld: Reservering - Basis-VM of Virtuele Machine-reservering - Windows Svr (1 kern).

Uw gebruiksgegevens en een geavanceerd CSV-rapport bevatten aanvullende gebruiksgegevens van de gereserveerde instantie. In het veld _Aanvullende informatie_ vindt u gebruiksgegevens van de gereserveerde instantie.

Als u geen gebruik hebt gemaakt van het hybride Azure-voordeel om de gereserveerde VM-instanties aan te schaffen, laten gereserveerde instanties twee meters zien (hardware en software). Als u het hybride Azure-voordeel hebt gebruikt om de gereserveerde instantie aan te schaffen, ziet u de softwaremeter niet in de gebruiksgegevens van uw gereserveerde instantie.

### <a name="reserved-instance-billing"></a>Facturering van gereserveerde instanties

Voor zakelijke klanten wordt financiële toezegging gebruikt om gereserveerde VM-instanties van Azure aan te schaffen. Als uw inschrijving genoeg financiële toezegging heeft om de aankoop van de gereserveerde instantie te dekken, wordt het bedrag afgeschreven van uw financiële toezegging en ontvangt u geen factuur voor de aankoop.

Wanneer Azure EA-klanten alle financiële toezeggingen hebben gebruikt, kunnen er nog steeds gereserveerde instanties worden gekocht. Deze aankopen worden in rekening gebracht op de volgende factuur voor overschrijding. Overschrijding van gereserveerde instanties, indien van toepassing, vormen onderdeel van uw reguliere overschrijdingsfactuur.

### <a name="reserved-instance-expiration"></a>Verloop van gereserveerde instanties

Voordat een instantie verloopt, ontvangt u 30 dagen en op het moment zelf een e-mailmelding. Wanneer de reservering verloopt, blijven geïmplementeerde VM's actief en worden deze in rekening gebracht tegen het tarief voor betalen per gebruik. Raadpleeg [Aanbieding van gereserveerde instanties van virtuele machines](https://azure.microsoft.com/pricing/reserved-vm-instances/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
- Zie [Wat zijn Azure-reserveringen?](../reservations/save-compute-costs-reservations.md) voor meer informatie over Azure-reserveringen.
- Zie [Kosten en gebruik van Enterprise Agreement-reserveringen ophalen](../reservations/understand-reserved-instance-usage-ea.md) voor meer informatie over Enterprise-reserveringskosten en -gebruik.
- Zie [Prijsinformatie - Linux Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) of [Prijsinformatie - Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) voor meer informatie over prijzen.
