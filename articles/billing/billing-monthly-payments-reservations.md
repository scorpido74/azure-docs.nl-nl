---
title: Azure-reserve ringen aanschaffen met vooraf of maandelijks betalen
description: Meer informatie over hoe u Azure-reserve ringen kunt aanschaffen met de voor-en maandelijkse betalingen.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806946"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Reserve ringen aanschaffen met maandelijkse betalingen

Tot nu toe is een betaling vereist voor Azure-reserve ringen. U kunt nu betalen voor reserve ringen met maandelijkse betalingen. In tegens telling tot een aankoop vooraf waarbij u het volledige bedrag betaalt, worden met de optie voor maandelijkse betaling de totale kosten van de reserve ring gelijkmatig verdeeld over elke maand van de periode. De totale kosten vooraf en maandelijkse reserve ringen zijn hetzelfde en u betaalt geen extra kosten wanneer u maandelijks betaalt.

Uw maandelijkse betalings bedrag kan variëren, afhankelijk van de markt wisselkoers van de huidige maand voor uw lokale valuta.

Maandelijkse betalingen zijn beschikbaar voor:

- Virtuele machines
- SQL Database
- SQL Data Warehouse
- Cosmos DB
- Prijs App Service stempel

Reserve ringen aanschaffen in de [Azure Portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![Voor beeld van reserve ring van inkoop](./media/billing-monthly-payments-reservations/purchase-reservation.png)

Bij het maken van een reserverings aankoop kunt u het betalings schema bekijken. Klik op **volledig betalings schema weer geven**.

![Voor beeld van betalings schema voor reserverings](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

Als u het betalings schema na aankoop wilt bekijken, selecteert u een reserve ring, klikt u op de **reserverings order-id**en klikt u vervolgens op het tabblad **betalingen** .

## <a name="view-payments-made"></a>Gemaakte betalingen weer geven

U kunt betalingen weer geven die zijn gemaakt met behulp van Api's, gebruiks gegevens en in kosten analyse. Voor reserve ringen die maandelijks worden betaald, wordt de waarde van de frequentie als **terugkerend** weer gegeven in de API voor gebruiks gegevens en reserverings kosten. Voor reserve ringen die vooraf zijn betaald, wordt de waarde weer gegeven als **eenmalige**.

Kosten analyse toont maandelijkse aankopen in de standaard weergave. Pas het **aankoop** filter voor het **type toeslag** en **terugkerend** op de **frequentie** toe om alle aankopen te bekijken. Als u alleen reserve ringen wilt weer geven, past u een filter toe op **reserve ring**.

![Voor beeld van de aanschaf kosten van reserve ringen in kosten analyse](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Overschakelen naar maandelijkse betalingen bij verlenging

Wanneer u een reserve ring verlengt, kunt u de facturerings frequentie wijzigen in maandelijks.

## <a name="exchange-and-refunds"></a>Exchange en terugbetalingen

Net als bij andere reserve ringen kunt u terugbetalen of Exchange-reserve ringen aanschaffen met maandelijkse facturering. Op dit moment kunt u een ondersteunings aanvraag indienen om een ruil of terugbetaling te starten voor een reserve ring die is gekocht met maandelijkse facturering.

Wanneer u een reserve ring uitwisselt die maandelijks wordt betaald, moet de totale levens duur van de nieuwe aankoop groter zijn dan de overgebleven betalingen die worden geannuleerd voor de geretourneerde reserve ring. Er zijn geen andere limieten of kosten voor uitwisselingen. U kunt een reserve ring uitwisselen die vooraf wordt betaald om een nieuwe reserve ring te kopen die maandelijks wordt gefactureerd. De waarde voor de levens duur van de nieuwe reserve ring moet echter groter zijn dan de naar evenredige waarde van de reserve ring die wordt geretourneerd.

Als er een reserve ring wordt geannuleerd die maandelijks wordt betaald, kan micro soft de annulerings kosten voor toekomstige doorgevoerde betalingen die worden geannuleerd, Toep assen. De resterende toegezegde betalingen worden in balans gebracht tegen de restitutie limiet van $50.000 USD.

Voor meer informatie over Exchange en terugbetalingen raadpleegt u [self-service uitwisselingen en terugbetalingen voor Azure Reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="faq"></a>Veelgestelde vragen

V. Biedt Azure ' gedeeltelijke reserve ringen aan de voor zijde '? '<br>
A. Nee. Omdat de kosten voor reserve ringen die vooraf zijn betaald en maandelijks hetzelfde zijn, biedt micro soft geen ondersteuning voor gedeeltelijke betalingen op de voor grond.

V. Zijn er maandelijkse betalingen beschikbaar voor het Microsoft Cloud Solution Provider (CSP)-programma?<br>
A. Ja, partners kunnen reserve ringen voor hun CSP-klanten kopen in de Azure Portal. De mogelijkheid om reserve ringen te kopen met maandelijkse facturering is niet beschikbaar in het partner centrum.

V. Ik ben een Azure Government klant, kan ik maandelijks betalen voor reserverings aankopen?<br>
A. Momenteel niet.

V. Wanneer kan ik mezelf uitwisselt of terugbetalen in de Azure Portal, in plaats van een ondersteunings ticket te maken?<br>
A. Momenteel niet. Aanvragen voor het uitwisselen van reserve ringen en terugstortingen met maandelijkse betalingen worden verwerkt door de ondersteuning van Azure.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat zijn Azure Reservations?](billing-save-compute-costs-reservations.md) voor meer informatie over reserve ringen.
- Voor meer informatie over de taken die u moet uitvoeren voordat u een reserve ring aanschaft, raadpleegt u [de juiste VM-grootte bepalen voordat u het aankoopt](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)
