---
title: Gereserveerde rekenprijzen - Azure Database voor PostgreSQL - Single Server
description: Vooraf betalen voor Azure Database voor PostgreSQL-compute resources met gereserveerde capaciteit
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 66d7228e78f03196da0b26249e7f1f86e79d79fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159010"
---
# <a name="prepay-for-azure-database-for-postgresql-compute-resources-with-reserved-capacity"></a>Vooraf betalen voor Azure Database voor PostgreSQL-compute resources met gereserveerde capaciteit

Azure Database voor PostgreSQL helpt u nu geld te besparen door vooraf te betalen voor rekenresources in vergelijking met betalen per gebruik-prijzen. Met Azure Database voor gereserveerde capaciteit van PostgreSQL maakt u vooraf een toezegging op de PostgreSQL-server voor een periode van één of drie jaar om een aanzienlijke korting op de rekenkosten te krijgen. Als u Azure Database wilt aanschaffen voor gereserveerde postgreSQL-capaciteit, moet u de Azure-regio, het implementatietype, de prestatielaag en de term opgeven. </br>

U hoeft de reservering niet toe te wijzen aan specifieke Azure Database voor PostgreSQL-servers. Een reeds draaiende Azure Database voor PostgreSQL of nieuwe nieuwe bestanden, krijgt automatisch het voordeel van gereserveerde prijzen. Door een reservering aan te schaffen, betaalt u de rekenkosten voor een periode van één of drie jaar vooraf. Zodra u een reservering koopt, worden de Azure-database voor PostgreSQL-rekenkosten die overeenkomen met de reserveringskenmerken, niet langer in rekening gebracht tegen de pay-as-you-go-tarieven. Een reservering dekt geen software- en netwerkkosten of opslagkosten die zijn gekoppeld aan de PostgreSQL-databaseservers. Aan het einde van de reserveringsperiode verloopt het factureringsvoordeel en wordt de Azure Database voor PostgreSQL gefactureerd tegen de pay-as-you-go-prijs. Reserveringen worden niet automatisch verlengd. Zie voor prijsinformatie de [azure database voor gereserveerde capaciteit van PostgreSQL.](https://azure.microsoft.com/pricing/details/postgresql/) </br>

> [!IMPORTANT]
> Gereserveerde capaciteitsprijzen zijn alleen beschikbaar voor de Azure Database voor PostgreSQL [Single server-implementatie](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) en niet voor [Hyperscale Citus-implementatie.](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus)

U Azure Database kopen voor gereserveerde postgreSQL-capaciteit in de [Azure-portal.](https://portal.azure.com/) Ga als bedoeld als bedoeld als gevolg van de aankoop van de gereserveerde capaciteit:

* U moet in de eigenaarrol zijn voor ten minste één Enterprise- of individueel abonnement met betalen per gebruik.
* Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com/). Of als die instelling is uitgeschakeld, moet u een EA-beheerder op het abonnement zijn.
* Voor Het CSP-programma (Cloud Solution Provider) kunnen alleen de beheerdersagenten of verkoopagents Azure Database kopen voor gereserveerde postgreSQL-capaciteit. </br>

De details over hoe zakelijke klanten en Pay-As-You-Go-klanten in rekening worden gebracht voor reserveringsaankopen, zie [inzicht in azure-reserveringsgebruik voor uw Enterprise-inschrijving](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) en [inzicht in Azure-reserveringsgebruik voor uw Pay-As-You-Go-abonnement.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-server-size-before-purchase"></a>Bepaal de juiste servergrootte vóór aankoop

De grootte van de reservering moet worden gebaseerd op de totale hoeveelheid rekenkracht die wordt gebruikt door de bestaande of binnenkort te implementeren servers binnen een specifieke regio en dezelfde prestatielaag en hardwaregeneratie gebruikt.</br>

Stel dat u één algemeen doel gebruikt, Gen5 – 32 vCore PostgreSQL-database en twee geoptimaliseerd geheugen, Gen5 – 16 vCore PostgreSQL-databases. Verder, laten we verondersteld dat u van plan bent om te implementeren binnen de volgende maand een extra algemeen doel, Gen5 - 32 vCore database server, en een geheugen geoptimaliseerd, Gen5 - 16 vCore database server. Stel dat u weet dat u deze bronnen minstens 1 jaar nodig hebt. In dit geval moet u een 64 (2x32) vCores, 1 jaar reserveren voor single database general purpose - Gen5 en een 48 (2x16 + 16) vCore 1 jaar reserveren voor single database geheugen geoptimaliseerd - Gen5


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure Database kopen voor gereserveerde PostgreSQL-capaciteit

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer **Toevoegen** en selecteer vervolgens in het deelvenster Reserveringen kopen de optie **Azure Database voor PostgreSQL** om een nieuwe reservering voor uw PostgreSQL-databases te kopen.
4. Vul de vereiste velden in. Bestaande of nieuwe databases die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de korting op de gereserveerde capaciteit. Het werkelijke aantal van uw Azure Database voor PostgreSQL-servers die de korting krijgen, is afhankelijk van de geselecteerde scope en hoeveelheid.


![Overzicht van gereserveerde prijzen](media/concepts-reserved-pricing/postgresql-reserved-price.png)


In de volgende tabel worden de vereiste velden beschreven.

| Veld | Beschrijving |
| :------------ | :------- |
| Abonnement   | Het abonnement dat wordt gebruikt om te betalen voor de Gereserveerde capaciteitsreservering van Azure Database voor PostgreSQL. De betalingsmethode voor het abonnement wordt in rekening gebracht voor de vooraf gemaakte kosten voor de gereserveerde capaciteitsreservering van PostgreSQL. Het abonnementstype moet een ondernemingsovereenkomst zijn (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P) of een individuele overeenkomst met pay-as-you-go-prijzen (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P). Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het toezeggingsbedrag of ze worden in rekening gebracht als overschrijding. Voor een individueel abonnement met betalen per gebruik worden de kosten in rekening gebracht op de creditcard- of factuurbetalingsmethode op het abonnement.
| Bereik | Het bereik van de vCore-reservering kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u: </br></br> **Gedeeld**, de vCore-reserveringskorting wordt toegepast op Azure Database voor PostgreSQL-servers die worden uitgevoerd in abonnementen binnen uw factureringscontext. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat het alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten bestaat het gedeelde bereik uit alle abonnementen op gebruiksbasis gemaakt door de accountbeheerder.</br></br> **Eén abonnement**, de vCore-reserveringskorting wordt toegepast op Azure Database voor PostgreSQL-servers in dit abonnement. </br></br> **Eén resourcegroep**, de reserveringskorting wordt toegepast op Azure Database voor PostgreSQL-servers in het geselecteerde abonnement en de geselecteerde resourcegroep binnen dat abonnement.
| Regio | De Azure-regio die wordt gedekt door de reservering van de Azure Database voor postgreSQL gereserveerde capaciteit.
| Implementatietype | Het Azure Database for PostgreSQL-brontype waarvoor u de reservering wilt kopen.
| Prestatielaag | De servicelaag voor de Azure Database voor PostgreSQL-servers.
| Termijn | Een jaar
| Aantal | De hoeveelheid rekenresources die worden aangeschaft in de Azure Database voor gereserveerde capaciteitsreservering van PostgreSQL. Het aantal is een aantal vCores in de geselecteerde Azure-regio en prestatielaag die worden gereserveerd en krijgt de factureringskorting. Als u bijvoorbeeld een Azure-database uitvoert of van plan bent uit te voeren voor PostgreSQL-servers met de totale rekencapaciteit van Gen5 16 vCores in de regio Oost-VS, geeft u een hoeveelheid op als 16 om het voordeel voor alle servers te maximaliseren.

## <a name="cancel-exchange-or-refund-reservations"></a>Annulering, omwisseling of terugbetaling van reserveringen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund) voor meer informatie.

## <a name="vcore-size-flexibility"></a>vCore-grootteflexibiliteit

VCore-grootteflexibiliteit helpt u omhoog of omlaag te schalen binnen een prestatieniveau en regio, zonder het gereserveerde capaciteitsvoordeel te verliezen. 

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [maakt u een ondersteuningsverzoek.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Volgende stappen

De vCore-reserveringskorting wordt automatisch toegepast op het aantal Azure Database voor PostgreSQL-servers dat overeenkomt met de Azure Database voor postgreSQL gereserveerde capaciteitsreserveringsscope en kenmerken. U het bereik van de Azure-database voor gereserveerde postgreSQL-capaciteitsreservering bijwerken via Azure portal, PowerShell, CLI of via de API. </br></br>
Zie Azure Database beheren voor gereserveerde postgreSQL-capaciteit voor meer informatie over het beheren van de gereserveerde Capaciteit van Azure Database voor PostgreSQL.

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

* [Wat zijn Azure-reserveringen?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Azure-reserveringen beheren](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Korting op Azure-reserveringen begrijpen](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](https://docs.microsoft.com/partner-center/azure-reservations)
