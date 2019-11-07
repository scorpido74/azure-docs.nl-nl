---
title: Prijs opwaardering voor Data Explorer van vooruitbetalen om geld te besparen
description: Meer informatie over het kopen van Azure Data Explorer gereserveerde capaciteit om uw kosten voor Azure Data Explorer op te slaan.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 66c5644df7d796669105693d08788548334ae93a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681628"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Progeschikte-eenheden voor Azure-Data Explorer met Azure Data Explorer gereserveerde capaciteit

Bespaar geld met Azure Data Explorer door vooraf te betalen voor de prijs opschrijvingen vergeleken met de prijzen voor betalen per gebruik. Met Azure Data Explorer gereserveerde capaciteit neemt u een toezeg ging voor Azure Data Explorer voor een periode van één of drie jaar om een aanzienlijke korting op de kosten voor de Azure-Data Explorer op te nemen. Als u de gereserveerde capaciteit van Azure Data Explorer wilt kopen, hoeft u alleen de term op te geven. deze wordt toegepast op alle implementaties van Azure Data Explorer in alle regio's.

Als u een reserve ring aanschaft, betaalt u de prijs van de verhoging vooraf voor een periode van één of drie jaar. Zodra u een reserve ring koopt, worden de kosten voor Azure Data Explorer-opmaak die overeenkomen met de reserverings kenmerken niet meer in rekening gebracht tegen de betalen naar gebruik-tarieven. Azure Data Explorer-clusters die al worden uitgevoerd of die nieuw zijn geïmplementeerd, krijgen automatisch het voor deel. Deze reserve ring geldt niet voor compute-, netwerk-of opslag kosten die zijn gekoppeld aan de clusters. Gereserveerde capaciteit voor deze resources moet afzonderlijk worden aangeschaft. Aan het einde van de reserverings termijn wordt het facturerings voordeel verrekend en worden de Azure Data Explorer Markup-eenheden gefactureerd op basis van de betalen naar gebruik-prijs. Reserve ringen worden niet automatisch verlengd. Zie de pagina met prijzen voor [Azure Data Explorer](https://azure.microsoft.com/pricing/details/data-explorer/)voor prijs informatie.

U kunt de gereserveerde capaciteit van Azure Data Explorer kopen in de [Azure Portal](https://portal.azure.com). Voor het kopen van Azure Data Explorer gereserveerde capaciteit:

* U moet de eigenaar zijn van ten minste één Enter prise-of betalen per gebruik-abonnement.
* Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com). Als deze instelling is uitgeschakeld, moet u ook een EA-beheerder zijn voor het abonnement.
* Voor het programma Cloud Solution Provider (CSP) kunnen alleen beheerders van de beheerder of de verkoop agents de gereserveerde capaciteit van Azure Data Explorer kopen.

Zie voor meer informatie over hoe klanten van de onderneming en betalen per gebruik in rekening worden gebracht voor reserverings aankopen:
* [Meer informatie over het gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](../billing/billing-understand-reserved-instance-usage-ea.md) 
* Meer [informatie over het gebruik van Azure-reserve ringen voor uw abonnement met betalen per gebruik](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-markup-usage-before-purchase"></a>Het juiste opmaak gebruik bepalen vóór de aankoop

De grootte van de reserve ring moet worden gebaseerd op het totale aantal Azure Data Explorer-indelings eenheden dat door de bestaande of binnenkort geïmplementeerde Azure Data Explorer-clusters wordt gebruikt. Het aantal markerings eenheden is gelijk aan het aantal cluster kernen van Azure Data Explorer engine in productie (exclusief de SKU voor dev/test). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Gereserveerde capaciteit van Azure Data Explorer kopen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer **alle services** > **reserve ringen** > **Nu kopen**. Selecteer **Toevoegen**
1. Selecteer in het deel venster **product type selecteren** de optie **Azure Data Explorer** om een nieuwe reserve ring te kopen voor Azure Data Explorer-markerings eenheden. 
1. Selecteer **kopen**
1. Vul de vereiste velden in. 

    ![Pagina aankoop](media/pricing-reserved-capacity/purchase-page.png)

1. Controleer de kosten van de reserve ring van de gereserveerde capaciteit van Azure Data Explorer in het gedeelte **kosten** .
1. Selecteer **Aankoop**.
1. Selecteer **deze reserve ring weer geven** om de status van uw aankoop te bekijken.

## <a name="cancellations-and-exchanges"></a>Annuleren en omwisselen

Als u de reserve ring van de gereserveerde capaciteit van Azure Data Explorer moet annuleren, zijn er mogelijk 12% vroege ontslag kosten. De restituties zijn gebaseerd op de laagste prijs van uw aankoop prijs of de huidige prijs van de reserve ring. Restituties zijn beperkt tot $ 50.000 per jaar. De restitutie die u ontvangt, is het resterende bedrag naar rato saldo minus de kosten voor vroegtijdige beëindiging van 12%. Als u een annulering wilt aanvragen, gaat u naar de reserve ring in het Azure Portal en selecteert u **terugbetaling** om een ondersteunings aanvraag te maken.

Als u de reserve ring van uw Azure Data Explorer gereserveerde capaciteit moet wijzigen naar een andere periode, kunt u deze uitwisselen voor een andere reserve ring met een waarde die gelijk of hoger is. De begindatum van de periode voor de nieuwe reservering wordt niet meegenomen naar de uitgewisselde reservering. De periode van 1 of 3 jaar begint bij het maken van de nieuwe reserve ring. Als u een uitwisseling wilt aanvragen, gaat u naar de reserve ring in het Azure Portal en selecteert u **Exchange** om een ondersteunings aanvraag te maken.

Zie [reserverings uitwisselingen en](../billing/billing-azure-reservations-self-service-exchange-and-refund.md)terugbetalingen voor meer informatie over het omruilen of terugbetalen van reserve ringen.

## <a name="manage-your-reserved-capacity-reservation"></a>Uw gereserveerde capaciteits reservering beheren

De korting voor de reserverings eenheden van Azure Data Explorer wordt automatisch toegepast op het aantal markerings eenheden dat overeenkomt met het gereserveerde bereik en de kenmerken voor de Azure-capaciteits reservering Data Explorer. 


> [!NOTE]
> * U kunt het bereik van de reserve ring van Azure Data Explorer gereserveerde capaciteit bijwerken via de [Azure Portal](https://portal.azure.com), Power shell, CLI of via de API.
> * Zie [azure Data Explorer gereserveerde capaciteit beheren](../billing/billing-understand-kusto-azuredataexplorer-reservation-charges.md)voor meer informatie over het beheren van de reserve ring van de gereserveerde capaciteit van Azure Data Explorer.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

* [Wat zijn Azure-reserveringen?](../billing/billing-save-compute-costs-reservations.md)
* [Azure-reserveringen beheren](../billing/billing-manage-reserved-vm-instance.md)
* [Korting op Azure-reserveringen begrijpen](../billing/billing-understand-reservation-charges.md)
* [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../billing/billing-understand-reserved-instance-usage.md)
* [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../billing/billing-understand-reserved-instance-usage-ea.md)
* [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
