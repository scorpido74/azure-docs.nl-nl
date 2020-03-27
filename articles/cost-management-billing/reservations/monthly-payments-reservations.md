---
title: Azure-reserveringen aanschaffen met betaling vooraf of per maand
description: Meer informatie over het aanschaffen van Azure-reserveringen met betaling vooraf of per maand.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: ede60adc13dadc38e18ee5ade468e01b16523f4f
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235733"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Reserveringen aanschaffen met maandelijkse betalingen

U kunt reserveringen aanschaffen met maandelijkse betalingen. In tegenstelling tot een aankoop vooraf waarbij u het volledige bedrag betaalt, worden bij de optie voor maandelijkse betaling de totale kosten van de reservering gelijkmatig verdeeld over alle maanden in de periode. De totale kosten van betalingen vooraf en per maand voor reserveringen zijn hetzelfde en u hoeft ook geen toeslag te betalen wanneer u voor maandelijks betalen kiest.

Als een reservering is aangeschaft met behulp van een Microsoft-klantovereenkomst (MCA), kan uw maandbedrag variëren op basis van de wisselkoers van de huidige maand voor uw lokale valuta.

Maandelijkse betalingen zijn niet beschikbaar voor: Databricks, SUSE Linux-reserveringen, Red Hat-abonnementen en Azure Red Hat OpenShift-rekenkracht.

Schaf reserveringen aan in [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs).

![Voorbeeld van een reserveringsaankoop](./media/monthly-payments-reservations/purchase-reservation.png)

Bij het aanschaffen van een reservering kunt u het betalingsschema bekijken. Klik op **Volledig betalingsschema weergeven**.

![Voorbeeld van het betalingsschema voor een reservering](./media/monthly-payments-reservations/prepurchase-schedule.png)

Als u het betalingsschema na aankoop wilt bekijken, selecteert u een reservering, klikt u op **Reserveringsorder-id** en klikt u vervolgens op het tabblad **Betalingen**.

## <a name="view-payments-made"></a>Gedane betalingen weergeven

U kunt gedane betalingen weergeven met behulp van API's en gebruiksgegevens of in de kostenanalyse. Voor reserveringen die maandelijks worden betaald, wordt de frequentiewaarde weergegeven als **Terugkerend** in gebruiksgegevens en de reserveringskosten-API. Voor reserveringen die vooraf zijn betaald, wordt de waarde weergegeven als **Eenmalig**.

Maandelijkse aankopen worden weergegeven in de standaardweergave van de kostenanalyse. Pas het filter **Aankoop** toe op **Kostentype** en het filter **Terugkerend** op **Frequentie** om alle aankopen te bekijken. Als u alleen reserveringen wilt weergeven, past u een filter toe op **Reservering**.

![Voorbeeld van de aanschafkosten van een reservering in de kostenanalyse](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Schakelen naar maandelijkse betalingen bij verlenging

Wanneer u een reservering verlengt, kunt u de factureringsfrequentie wijzigen in maandelijks.

## <a name="exchange-and-refunds"></a>Omruiling en terugbetaling

Net als bij andere reserveringen is terugbetaling of omruiling mogelijk van reserveringen die zijn aangeschaft met maandelijkse facturering. 

Wanneer u een reservering met maandelijkse betaling omruilt, moeten de kosten voor de totale levensduur van de nieuwe aankoop hoger zijn dan de resterende betalingen die worden geannuleerd voor de geretourneerde reservering. Er zijn geen andere limieten of kosten verbonden aan omruiling. U kunt een reservering die vooraf is betaald, omruilen voor een nieuwe reservering die maandelijks wordt gefactureerd. De waarde van de levensduur van de nieuwe reservering moet echter hoger zijn dan de waarde naar rato van de reservering die wordt geretourneerd.

Als u een reservering annuleert die maandelijks wordt betaald, worden geannuleerde toekomstige betalingen in rekening gebracht met een restitutielimiet van USD 50.000.

Zie [Selfservice voor omruiling en terugbetaling van Azure-reserveringen](exchange-and-refund-azure-reservations.md) voor meer informatie over omruiling en terugbetaling.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md) voor meer informatie over reserveringen.
- Zie [De juiste VM-grootte bepalen voordat u koopt](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy) voor meer informatie over de taken die u moet uitvoeren voordat u een reservering aanschaft
