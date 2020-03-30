---
title: Vooraf betalen voor Azure Data Explorer-opslag om geld te besparen
description: Meer informatie over het kopen van gereserveerde capaciteit van Azure Data Explorer om te besparen op uw Azure Data Explorer-kosten.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969274"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Vooraf betalen voor azure Data Explorer-markeringseenheden met gereserveerde capaciteit van Azure Data Explorer

Bespaar geld met Azure Data Explorer door vooraf te betalen voor de opmaakeenheden in vergelijking met betalen per gebruik.Spaar geld met Azure Data Explorer door vooraf te betalen voor de opmaakeenheden in vergelijking met betalen per gebruik. Met de gereserveerde capaciteit van Azure Data Explorer maakt u een toezegging voor het gebruik van Azure Data Explorer voor een periode van één of drie jaar om een aanzienlijke korting te krijgen op de opmaakkosten van Azure Data Explorer. Als u de gereserveerde capaciteit van Azure Data Explorer wilt aanschaffen, hoeft u alleen de term op te geven, deze is van toepassing op alle implementaties van Azure Data Explorer in alle regio's.

Door een reservering aan te kopen, betaalt u de opmaakkosten voor een periode van één of drie jaar. Zodra u een reservering koopt, worden de markupkosten van Azure Data Explorer die overeenkomen met de reserveringskenmerken, niet langer in rekening gebracht tegen de betalenper-you-tarieven. Azure Data Explorer-clusters die al worden uitgevoerd of clusters die onlangs zijn geïmplementeerd, krijgen automatisch het voordeel. Deze reservering heeft geen betrekking op reken-, netwerk- of opslagkosten die zijn gekoppeld aan de clusters. Gereserveerde capaciteit voor deze resources moet afzonderlijk worden aangeschaft. Aan het einde van de reserveringsperiode verloopt het factureringsvoordeel en worden de azure data explorer-opmaakeenheden gefactureerd tegen de betalen-as-you-go-prijs. Reserveringen worden niet automatisch verlengd. Zie de [prijspagina van Azure Data Explorer](https://azure.microsoft.com/pricing/details/data-explorer/)voor prijsinformatie .

U gereserveerde capaciteit van Azure Data Explorer kopen in de [Azure-portal.](https://portal.azure.com) Ga als bedoeld als u de gereserveerde capaciteit van Azure Data Explorer wilt kopen:

* U moet eigenaar zijn van ten minste één Enterprise- of Pay-As-You-Go-abonnement.
* Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com). Als die instelling is uitgeschakeld, moet u ook een EA-beheerder op het abonnement zijn.
* Voor het CSP-programma (Cloud Solution Provider) kunnen alleen de beheerdersagenten of verkoopmedewerkers gereserveerde capaciteit van Azure Data Explorer aanschaffen.

Zie voor meer informatie over hoe zakelijke klanten en Pay-As-You-Go-klanten in rekening worden gebracht voor reserveringsaankopen:
* [Inzicht in Azure-reserveringsgebruik voor uw Enterprise-inschrijving](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [Inzicht in Azure-reserveringsgebruik voor uw Abonnement per gebruik](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-the-right-markup-usage-before-purchase"></a>Het juiste merkgebruik bepalen voordat u de aankoop aanschaft

De grootte van de reservering moet worden gebaseerd op het totale aantal Azure Data Explorer-markeringseenheden dat wordt gebruikt door de bestaande of binnenkort te implementeren Azure Data Explorer-clusters. Het aantal markeringseenheden is gelijk aan het aantal Azure Data Explorer-engineclusterkernen in productie (exclusief de dev/test SKU). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Gereserveerde azure Data Explorer-capaciteit kopen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Alle services** > **reserveringen** > **aankoop nu**. Selecteer **Toevoegen**
1. Selecteer in het deelvenster **Producttype selecteren** de optie **Azure Data Explorer** om een nieuwe reservering voor azure Data Explorer-opmaakeenheden aan te schaffen. 
1. Selecteer **Kopen**
1. Vul de vereiste velden in. 

    ![Aankooppagina](media/pricing-reserved-capacity/purchase-page.png)

1. Bekijk de kosten van de azure data explorer-markering gereserveerde capaciteitsreservering in de sectie **Kosten.**
1. Selecteer **Aankoop**.
1. Selecteer **Deze reservering weergeven** om de status van uw aankoop te bekijken.

## <a name="cancellations-and-exchanges"></a>Annuleren en omwisselen

Als u uw reservering van de gereserveerde capaciteitsreservering van Azure Data Explorer moet annuleren, kunnen er kosten voor vroegtijdige beëindiging van de periode van 12% worden betaald. Terugbetalingen zijn gebaseerd op de laagste prijs van uw aankoopprijs of de huidige prijs van de reservering. Restituties zijn beperkt tot $ 50.000 per jaar. De restitutie die u ontvangt, is het resterende bedrag naar rato saldo minus de kosten voor vroegtijdige beëindiging van 12%. Als u een annulering wilt aanvragen, gaat u naar de reservering in de Azure-portal en selecteert **u Restitutie** om een ondersteuningsaanvraag te maken.

Als u de gereserveerde capaciteitsreservering van Azure Data Explorer moet wijzigen in een andere term, u deze inruilen voor een andere reservering die even groot of groter is. De begindatum van de periode voor de nieuwe reservering wordt niet meegenomen naar de uitgewisselde reservering. De termijn van 1 of 3 jaar begint vanaf het moment dat u de nieuwe reservering maakt. Als u een uitwisseling wilt aanvragen, gaat u naar de reservering in de Azure-portal en selecteert **u Exchange** om een ondersteuningsaanvraag te maken.

Zie [Reserveringsuitwisselingen en terugbetalingen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)voor meer informatie over het uitwisselen of terugbetalen van reserveringen.

## <a name="manage-your-reserved-capacity-reservation"></a>Uw reservering voor gereserveerde capaciteit beheren

De reserveringskorting voor reserveringseenheden van Azure Data Explorer wordt automatisch toegepast op het aantal markeringseenheden dat overeenkomt met de gereserveerde capaciteitsreserveringsscope en -kenmerken van Azure Data Explorer. 


> [!NOTE]
> * U het bereik van de gereserveerde capaciteitsreservering van Azure Data Explorer bijwerken via de [Azure-portal,](https://portal.azure.com)PowerShell, CLI of via de API.
> * Zie [Gereserveerde capaciteit beheren van Azure Data Explorer](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md)voor meer informatie over het beheren van de gereserveerde capaciteitsreservering van Azure Data Explorer.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

* [Wat zijn Azure-reserveringen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Azure-reserveringen beheren](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Korting op Azure-reserveringen begrijpen](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [maakt u een ondersteuningsverzoek.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
