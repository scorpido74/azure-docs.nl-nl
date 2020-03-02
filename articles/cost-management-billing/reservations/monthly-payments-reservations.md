---
title: Azure-reserveringen aanschaffen met betaling vooraf of per maand
description: Meer informatie over het aanschaffen van Azure-reserveringen met betaling vooraf of per maand.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: banders
ms.openlocfilehash: 77d663fa01e24acf63acd68d0b8d7cf4cc741055
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587087"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Reserveringen aanschaffen met maandelijkse betalingen

Tot nu toe moesten Azure-reserveringen vooraf worden betaald. Nu kunt u reserveringen aanschaffen met maandelijkse betalingen. In tegenstelling tot een aankoop vooraf waarbij u het volledige bedrag betaalt, worden bij de optie voor maandelijkse betaling de totale kosten van de reservering gelijkmatig verdeeld over alle maanden in de periode. De totale kosten van betalingen vooraf en per maand voor reserveringen zijn hetzelfde en u hoeft ook geen toeslag te betalen wanneer u voor maandelijks betalen kiest.

Uw maandbedrag kan variëren op basis van de wisselkoers van de huidige maand voor uw lokale valuta.

Maandelijkse betalingen zijn beschikbaar voor:

- Virtuele machines
- Azure Storage
- SQL Databases
- SQL Data Warehouse
- Cosmos DB
- App Service-zegelkosten
- Managed Disk
- Azure Data Explorer
- Azure Database for MariaDB, MySQL en PostgreSQL
- Azure VMware Solution by CloudSimple


Schaf reserveringen aan in [Azure Portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

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

Net als bij andere reserveringen is terugbetaling of omruiling mogelijk van reserveringen die zijn aangeschaft met maandelijkse facturering. Momenteel kunt u een ondersteuningsaanvraag indienen om een omruiling of terugbetaling te starten voor een reservering die is aangeschaft met maandelijkse facturering.

Wanneer u een reservering met maandelijkse betaling omruilt, moeten de kosten voor de totale levensduur van de nieuwe aankoop hoger zijn dan de resterende betalingen die worden geannuleerd voor de geretourneerde reservering. Er zijn geen andere limieten of kosten verbonden aan omruiling. U kunt een reservering die vooraf is betaald, omruilen voor een nieuwe reservering die maandelijks wordt gefactureerd. De waarde van de levensduur van de nieuwe reservering moet echter hoger zijn dan de waarde naar rato van de reservering die wordt geretourneerd.

Als u een reservering met maandelijkse betaling annuleert, kan Microsoft annuleringskosten van 12% in rekening brengen voor de toegezegde betalingen die zijn geannuleerd. Momenteel brengt Microsoft deze boete echter niet in rekening. Geannuleerde toegezegde betalingen tellen mee voor de terugbetalingslimiet van $ 50.000. Als een annuleringsboete in rekening wordt gebracht, is dit niet van invloed op de terugbetalingslimiet.

Zie [Selfservice voor omruiling en terugbetaling van Azure-reserveringen](exchange-and-refund-azure-reservations.md) voor meer informatie over omruiling en terugbetaling.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md) voor meer informatie over reserveringen.
- Zie [De juiste VM-grootte bepalen voordat u koopt](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy) voor meer informatie over de taken die u moet uitvoeren voordat u een reservering aanschaft
