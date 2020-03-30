---
title: Vooraf betalen voor rekenkracht met gereserveerde capaciteit - Azure-cache voor Redis
description: Vooraf betalen voor Azure Cache voor Redis-rekenbronnen met gereserveerde capaciteit
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530300"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Vooraf betalen voor Azure Cache voor Redis-rekenbronnen met gereserveerde capaciteit

Azure Cache voor Redis helpt u nu geld te besparen door vooraf te betalen voor rekenresources in vergelijking met betalen per gebruik-prijzen. Met azure cache voor gereserveerde capaciteit van Redis, maakt u vooraf een toezegging over cache voor een periode van één of drie jaar om een aanzienlijke korting op de rekenkosten te krijgen. Als u Azure Cache voor gereserveerde capaciteit van Redis wilt aanschaffen, moet u de Azure-regio, de servicelaag en de term opgeven.

U hoeft de reservering niet toe te wijzen aan specifieke Azure-cache voor Redis-exemplaren. Een reeds draaiende Azure-cache voor Redis of degenen die onlangs zijn geïmplementeerd, krijgt automatisch het voordeel van gereserveerde prijzen, tot de gereserveerde cachegrootte. Door een reservering aan te schaffen, betaalt u de rekenkosten voor een periode van één of drie jaar vooraf. Zodra u een reservering koopt, worden de rekenkosten voor Azure Cache voor Redis die overeenkomen met de reserveringskenmerken niet langer in rekening gebracht tegen de pay-as-you-go-tarieven. Een reservering dekt geen netwerk- of opslagkosten die aan de cache zijn gekoppeld. Aan het einde van de reserveringsperiode verloopt het factureringsvoordeel en wordt de Azure-cache voor Redis gefactureerd tegen de prijs voor betalen per gebruik. Reserveringen worden niet automatisch verlengd. Zie de aanbieding [voor gereserveerde capaciteit azure cache voor Redis voor](https://azure.microsoft.com/pricing/details/cache)prijsinformatie .

U Azure Cache voor gereserveerde capaciteit van Redis kopen in de [Azure-portal.](https://portal.azure.com/) Ga als bedoeld als bedoeld als gevolg van de aankoop van de gereserveerde capaciteit:

* U moet in de eigenaarrol zijn voor ten minste één Enterprise- of individueel abonnement met betalen per gebruik.
* Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com/). Of als die instelling is uitgeschakeld, moet u een EA-beheerder op het abonnement zijn.
* Voor Het CSP-programma (Cloud Solution Provider) kunnen alleen de beheerdersagenten of verkoopmedewerkers Azure Cache kopen voor gereserveerde capaciteit van Redis.

Zie [inzicht in azure-reserveringsgebruik voor uw Enterprise-inschrijving](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) en [inzicht in azure-reserveringsgebruik voor uw Pay-As-You-Go-abonnement](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)voor meer informatie over hoe zakelijke klanten en betalen per gebruik-klanten in rekening worden gebracht.


## <a name="determine-the-right-cache-size-before-purchase"></a>Bepaal de juiste cachegrootte vóór aankoop

De grootte van de reservering moet worden gebaseerd op de totale hoeveelheid rekenkracht die wordt gebruikt door de bestaande of binnenkort te implementeren cache binnen een bepaald gebied en dezelfde servicelaag gebruikt.

Stel dat u één algemeen doel hebt, Gen5 - 32 vCore-cache en twee geoptimaliseerd geheugen, Gen5 - 16 vCore-caches. Verder, laten we verondersteld dat u van plan bent om te implementeren binnen de volgende maand een extra algemeen doel, Gen5 - 32 vCore database server, en een geheugen geoptimaliseerd, Gen5 - 16 vCore database server. Stel dat u weet dat u deze bronnen minstens 1 jaar nodig hebt. In dit geval moet u een 64 (2x32) vCores, 1 jaar reserveren voor single database general purpose - Gen5 en een 48 (2x16 + 16) vCore 1 jaar reserveren voor single database geheugen geoptimaliseerd - Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Azure-cache kopen voor gereserveerde capaciteit van Redis

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer **Toevoegen** en selecteer vervolgens in het deelvenster Inkoopreserveringen de optie **Azure Cache voor Redis** om een nieuwe reservering voor uw caches te kopen.
4. Vul de vereiste velden in. Bestaande of nieuwe databases die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de korting op de gereserveerde capaciteit. Het werkelijke aantal van uw Azure-cache voor Redis-exemplaren waarmee de korting wordt verkregen, is afhankelijk van het geselecteerde bereik en de geselecteerde hoeveelheid.


![Overzicht van gereserveerde prijzen](media/cache-reserved-pricing/cache-reserved-price.png)


In de volgende tabel worden de vereiste velden beschreven.

| Veld | Beschrijving |
| :------------ | :------- |
| Abonnement   | Het abonnement dat wordt gebruikt om te betalen voor de gereserveerde capaciteitsreservering van Azure Cache voor Redis. De betalingsmethode voor het abonnement wordt in rekening gebracht voor de kosten vooraf voor de azure-cache voor gereserveerde capaciteitsreservering van Redis. Het abonnementstype moet een ondernemingsovereenkomst zijn (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P) of een individuele overeenkomst met pay-as-you-go-prijzen (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P). Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het toezeggingsbedrag of ze worden in rekening gebracht als overschrijding. Voor een individueel abonnement met betalen per gebruik worden de kosten in rekening gebracht op de creditcard- of factuurbetalingsmethode op het abonnement.
| Bereik | Het bereik van de reservering kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u: </br></br> **Gedeeld**, de reserveringskorting wordt toegepast op Azure Cache voor Redis-exemplaren die worden uitgevoerd in abonnementen binnen uw factureringscontext. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat het alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten bestaat het gedeelde bereik uit alle abonnementen op gebruiksbasis gemaakt door de accountbeheerder.</br></br> **Eén abonnement**, de reserveringskorting wordt toegepast op Azure Cache voor Redis-exemplaren in dit abonnement. </br></br> **Eén resourcegroep**, de reserveringskorting wordt toegepast op Azure Cache voor Redis-exemplaren in het geselecteerde abonnement en de geselecteerde resourcegroep binnen dat abonnement.
| Regio | Het Azure-gebied dat wordt gedekt door de reservering van azure cache voor Redis gereserveerde capaciteit.
| Prijscategorie | De servicelaag voor de Azure-cache voor Redis-servers.
| Termijn | Een jaar of drie jaar
| Aantal | De hoeveelheid rekenresources die worden aangeschaft in de Azure-cache voor gereserveerde capaciteitsreservering van Redis. Het aantal is een aantal caches in de geselecteerde Azure-regio en servicelaag die worden gereserveerd en krijgt de factureringskorting. Als u bijvoorbeeld een Azure-cache voor Redis-servers uitvoert of van plan bent met de totale cachecapaciteit van 26 GB in de regio Oost-VS, geeft u het aantal op als 26 om het voordeel voor alle caches te maximaliseren.

## <a name="cancel-exchange-or-refund-reservations"></a>Annulering, omwisseling of terugbetaling van reserveringen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund) voor meer informatie.

## <a name="cache-size-flexibility"></a>Flexibiliteit in cachegrootte

Met de flexibiliteit van de cachegrootte u omhoog of omlaag schalen binnen een servicelaag en -regio, zonder dat u het gereserveerde capaciteitsvoordeel verliest.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [maakt u een ondersteuningsverzoek.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Volgende stappen

De reserveringskorting wordt automatisch toegepast op de Azure-cache voor Redis-exemplaren die overeenkomen met de reserveringsscope en -kenmerken. U het bereik van de reservering bijwerken via de Azure-portal, PowerShell, Azure CLI of de API.

*  Zie [De azure-reserveringskorting begrijpen](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md) voor meer informatie over de korting op gereserveerde capaciteit voor Azure-cache voor Redis

* Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

    * [Wat zijn Azure-reserveringen?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Azure-reserveringen beheren](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Korting op Azure-reserveringen begrijpen](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](https://docs.microsoft.com/partner-center/azure-reservations)

