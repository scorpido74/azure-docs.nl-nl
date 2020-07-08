---
title: Vooruitbetalen voor reken kracht met gereserveerde capaciteit-Azure cache voor redis
description: Vooruitbetalen voor Azure cache voor redis Compute-resources met gereserveerde capaciteit
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 8af8db6b6853d6b8cbd4ba1105f05ebb9bcf771b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84974837"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Vooruitbetalen voor Azure cache voor redis Compute-resources met gereserveerde capaciteit

Azure cache voor redis helpt u nu bij het besparen van geld door vooraf te betalen voor reken resources vergeleken met de prijzen voor betalen per gebruik. Met Azure cache voor de gereserveerde capaciteit van redis maakt u voor een periode van één of drie jaar vooraf een toezeg ging voor de cache om een aanzienlijke korting op de reken kosten te krijgen. Als u Azure cache wilt kopen voor redis gereserveerde capaciteit, moet u de Azure-regio, de servicelaag en de periode opgeven.

U hoeft de reserve ring niet toe te wijzen aan een specifieke Azure-cache voor redis-exemplaren. Een Azure-cache die al wordt uitgevoerd voor redis of die zojuist is geïmplementeerd, krijgt automatisch het voor deel van gereserveerde prijzen, tot de gereserveerde cache grootte. Als u een reserve ring aanschaft, betaalt u de reken kosten vooraf voor een periode van één of drie jaar. Zodra u een reserve ring koopt, worden de kosten voor Azure-cache voor redis die overeenkomen met de reserverings kenmerken niet meer in rekening gebracht tegen de betalen naar gebruik-tarieven. Een reserve ring dekt geen netwerk-of opslag kosten die zijn gekoppeld aan de cache. Aan het einde van de reserverings termijn verloopt het facturerings voordeel en de Azure-cache voor redis wordt gefactureerd op basis van de betalen naar gebruik-prijs. Reserve ringen worden niet automatisch vernieuwd. Voor prijs informatie raadpleegt u de [Azure-cache voor redis gereserveerde capaciteit](https://azure.microsoft.com/pricing/details/cache).

U kunt in de [Azure Portal](https://portal.azure.com/)Azure-cache kopen voor redis gereserveerde capaciteit. De gereserveerde capaciteit kopen:

* U moet de rol van eigenaar zijn voor minstens één bedrijf of een afzonderlijk abonnement met betalen per gebruik-tarieven.
* Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com/). Als deze instelling is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement.
* Voor de Cloud Solution Provider (CSP)-programma kunnen alleen de beheerders of verkoop agenten Azure-cache kopen voor redis gereserveerde capaciteit.

Zie het [gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) en inzicht krijgen in het gebruik van [Azure-reserve ringen voor uw abonnement op basis van betalen per gebruik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)voor meer informatie over de wijze waarop klanten en betalen per gebruik in rekening worden gebracht voor reserverings aankopen.


## <a name="determine-the-right-cache-size-before-purchase"></a>De juiste cache grootte bepalen vóór de aankoop

De grootte van de reserve ring moet worden gebaseerd op de totale hoeveelheid geheugen die wordt gebruikt door de bestaande of binnenkort geïmplementeerde cache binnen een bepaalde regio en met dezelfde servicelaag.

Stel bijvoorbeeld dat u twee caches gebruikt, één bij 13 GB en de andere bij 26 GB. U hebt beide voor ten minste één jaar nodig. Stel dat u van plan bent om de bestaande 13 GB-caches voor een maand te schalen naar 26 GB om te voldoen aan de behoeften van uw seizoen en vervolgens terug te schalen. In dit geval kunt u 1 P1-cache en 1 P2-cache of 3 P1-caches aanschaffen met een reserve ring van één jaar om de besparingen te maximaliseren. U ontvangt korting op de totale hoeveelheid cache geheugen die u reserveert, onafhankelijk van de manier waarop deze hoeveelheid wordt toegewezen in uw caches.


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Azure-cache kopen voor redis gereserveerde capaciteit

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer **toevoegen** en selecteer in het deel venster Inkoop reserveringen de optie **Azure-cache voor redis** om een nieuwe reserve ring voor uw caches te kopen.
4. Vul de vereiste velden in. Bestaande of nieuwe data bases die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de korting op gereserveerde capaciteit. Het werkelijke aantal van uw Azure-cache voor redis-instanties die de korting krijgen, is afhankelijk van het bereik en de geselecteerde hoeveelheid.


![Overzicht van gereserveerde prijzen](media/cache-reserved-pricing/cache-reserved-price.png)


In de volgende tabel worden de vereiste velden beschreven.

| Veld | Beschrijving |
| :------------ | :------- |
| Abonnement   | Het abonnement dat wordt gebruikt voor de betaling van de Azure-cache voor gereserveerde capaciteits reservering redis. Voor de betalings methode voor het abonnement worden de kosten vooraf in rekening gebracht voor de Azure-cache voor gereserveerde capaciteits reservering redis. Het abonnements type moet een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een afzonderlijke overeenkomst met betalen per gebruik-prijs (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het toezeggingsbedrag of ze worden in rekening gebracht als overschrijding. Voor een individueel abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card of factuur betalings methode voor het abonnement.
| Bereik | Het bereik van de reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert: </br></br> **Gedeeld**, de reserverings korting wordt toegepast op Azure cache voor redis-exemplaren die worden uitgevoerd in abonnementen binnen uw facturerings context. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten bestaat het gedeelde bereik uit alle abonnementen op gebruiksbasis gemaakt door de accountbeheerder.</br></br> **Eén abonnement**: de reserverings korting wordt toegepast op Azure cache voor redis-exemplaren in dit abonnement. </br></br> **Eén resource groep**, de reserverings korting wordt toegepast op Azure cache voor redis-exemplaren in het geselecteerde abonnement en de geselecteerde resource groep in dat abonnement.
| Regio | De Azure-regio die wordt gedekt door de Azure-cache voor gereserveerde capaciteits reservering van redis.
| Prijscategorie | De servicelaag voor de Azure-cache voor redis-servers.
| Termijn | Eén jaar of drie jaar
| Aantal | De hoeveelheid reken resources die in de Azure-cache worden gekocht voor gereserveerde capaciteits reservering redis. De hoeveelheid is een aantal caches in de geselecteerde Azure-regio en service tier die worden gereserveerd en de facturerings korting krijgt. Als u bijvoorbeeld werkt met of plant om een Azure-cache uit te voeren voor redis-servers met de totale cache capaciteit van 26 GB in de regio VS-Oost, geeft u het aantal op van 26 om het voor deel van alle caches te maximaliseren.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund) voor meer informatie.

## <a name="cache-size-flexibility"></a>Flexibiliteit van cache grootte

Met de flexibiliteit van cache grootte kunt u binnen een servicelaag en regio omhoog of omlaag schalen zonder het voor deel van de gereserveerde capaciteit te verliezen.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

De reserverings korting wordt automatisch toegepast op de Azure-cache voor redis-exemplaren die overeenkomen met het reserverings bereik en de kenmerken. U kunt het bereik van de reserve ring bijwerken via de Azure Portal, Power shell, Azure CLI of de API.

*  Zie [inzicht in de korting voor Azure-reserve ringen](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md) voor meer informatie over hoe gereserveerde capaciteits kortingen worden toegepast op Azure cache voor redis.

* Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

    * [Wat zijn Azure-reserveringen?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Azure-reserveringen beheren](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Korting op Azure-reserveringen begrijpen](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](https://docs.microsoft.com/partner-center/azure-reservations)

