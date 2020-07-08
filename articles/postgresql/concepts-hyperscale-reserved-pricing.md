---
title: Prijzen voor gereserveerde berekeningen-Azure Database for PostgreSQL-grootschalige (Citus)
description: Vooruitbetalen voor Azure Database for PostgreSQL-grootschalige (Citus) Compute-resources met gereserveerde capaciteit
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: dcf38a3bcd41ba41bb7cf3b16d022a7a45734c4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85218007"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Vooruitbetalen voor Azure Database for PostgreSQL-grootschalige (Citus) Compute-resources met gereserveerde capaciteit

Azure Database for PostgreSQL – grootschalige (Citus) helpt u nu bij het besparen van geld door vooraf te betalen voor reken resources vergeleken met prijzen voor betalen per gebruik. Met de gereserveerde capaciteit van grootschalige (Citus) kunt u een periode van één of drie jaar vooraf instellen op de Server groep grootschalige (Citus) om een aanzienlijke korting op de reken kosten te krijgen. Als u de gereserveerde capaciteit van grootschalige (Citus) wilt kopen, moet u de Azure-regio, de reserverings periode en de facturerings frequentie opgeven.

> [!IMPORTANT]
> Deze pagina is over gereserveerde capaciteit voor Azure Database for PostgreSQL – grootschalige (Citus). Op [Deze pagina](/azure/postgresql/concept-reserved-pricing) vindt u informatie over gereserveerde capaciteit voor Azure database for PostgreSQL-één server.

U hoeft de reserve ring niet toe te wijzen aan specifieke grootschalige (Citus)-Server groepen. Een grootschalige-Server groep (Citus) die al wordt uitgevoerd, krijgt automatisch het voor deel van de gereserveerde prijzen. Als u een reserve ring aanschaft, betaalt u voor de reken kosten vooraf een periode van één of drie jaar. Zodra u een reserve ring koopt, worden de kosten voor de grootschalige (Citus) die overeenkomen met de reserverings kenmerken, niet meer in rekening gebracht tegen de betalen naar gebruik-tarieven. Een reserve ring geldt niet voor software-, netwerk-of opslag kosten die zijn gekoppeld aan de grootschalige (Citus)-Server groepen. Aan het einde van de reserverings termijn verloopt het facturerings voordeel en worden de grootschalige (Citus)-Server groepen gefactureerd op basis van de betalen naar gebruik-prijs. Reserve ringen worden niet verlengd. Zie voor prijs informatie de [grootschalige-aanbieding (Citus) gereserveerde capaciteit van Azure database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

U kunt gereserveerde capaciteit van grootschalige (Citus) kopen in de [Azure Portal](https://portal.azure.com/). Betaal [vooraf of per maand](https://docs.microsoft.com/azure/cost-management-billing/reservations/monthly-payments-reservations) voor de reservering. De gereserveerde capaciteit kopen:

* U moet de rol van eigenaar zijn voor minstens één bedrijf of een afzonderlijk abonnement met betalen per gebruik-tarieven.
* Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com/). Als deze instelling is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement.
* Voor de Cloud Solution Provider (CSP)-programma kunnen alleen de beheerders of verkoop medewerkers de gereserveerde capaciteit van grootschalige (Citus) kopen.

Zie het [gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) en [inzicht krijgen in het gebruik van Azure-reserve ringen voor uw abonnement op basis van betalen per gebruik](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)voor meer informatie over hoe klanten en betalen per gebruik in rekening worden gebracht voor reserverings aankopen.

## <a name="determine-the-right-server-group-size-before-purchase"></a>De juiste grootte van de Server groep bepalen vóór de aankoop

De grootte van de reserve ring moet worden gebaseerd op de totale reken tijd die wordt gebruikt door de bestaande of binnenkort geïmplementeerde coördinator en worker-knoop punten in grootschalige (Citus)-Server groepen binnen een bepaalde regio.

Stel bijvoorbeeld dat u één grootschalige-Server groep (Citus) met 16 vCore Coordinator en drie 8 vCore worker-knoop punten uitvoert. Verder gaan we ervan uit dat u in de volgende maand een extra grootschalige-Server groep (Citus) wilt implementeren met een 32 vCore-coördinator en twee 4 vCore-werk knooppunten. Stel dat u deze resources ten minste één jaar nodig hebt.

In dit geval moet u een reserve ring van één jaar aanschaffen voor

* Totaal 16 vCores + 32 vCores = 48 vCores voor coördinator knooppunten
* Totaal drie knoop punten x 8 vCores + 2 knoop punten x 4 vCores = 24 + 8 = 32 vCores voor worker-knoop punten

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure Database for PostgreSQL gereserveerde capaciteit kopen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer **toevoegen** en selecteer in het deel venster reserve ringen de optie **Azure database for PostgreSQL** om een nieuwe reserve ring te kopen voor uw postgresql-data bases.
4. Selecteer grootschalige (Citus) reken type dat u wilt kopen en klik op de knop **selecteren** .
5. Controleer de hoeveelheid voor het geselecteerde reken type op het tabblad **producten** .
4. Ga naar het tabblad **kopen + beoordeling** om uw aankoop te volt ooien.

In de volgende tabel worden de vereiste velden beschreven.

| Veld        | Beschrijving                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Abonnement | Het abonnement dat wordt gebruikt om te betalen voor de reserve ring van de Azure Database for PostgreSQL gereserveerde capaciteit. Voor de betalings methode voor het abonnement worden de kosten vooraf in rekening gebracht voor de reserve ring van de Azure Database for PostgreSQL gereserveerde capaciteit. Het abonnements type moet een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een afzonderlijke overeenkomst met betalen per gebruik-prijs (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). Voor een Enter prise-abonnement worden de kosten afgetrokken van het monetaire toezeggings saldo van de inschrijving of worden deze gefactureerd als overschrijding. Voor een individueel abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card of factuur betalings methode voor het abonnement.                                                                                  |
| Bereik        | Het bereik van de vCore-reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert: <br><br> **Gedeeld,** de vCore-reserverings korting wordt toegepast op grootschalige (Citus)-Server groepen die worden uitgevoerd in abonnementen binnen uw facturerings context. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat alle abonnementen binnen de inschrijving.  Voor betalen per gebruik-klanten bestaat het gedeelde bereik uit alle abonnementen op gebruiksbasis gemaakt door de accountbeheerder. <br><br> **Eén abonnement,** de vCore-reserverings korting wordt toegepast op grootschalige (Citus)-Server groepen in dit abonnement. <br><br> **Eén resource groep,** de reserverings korting wordt toegepast op grootschalige (Citus)-Server groepen in het geselecteerde abonnement en de geselecteerde resource groep in dat abonnement. |
| Regio       | De Azure-regio die wordt gedekt door de gereserveerde capaciteits reservering Azure Database for PostgreSQL – grootschalige (Citus).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Termijn         | Een jaar of drie jaar.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Aantal     | De hoeveelheid reken resources die worden aangeschaft binnen de gereserveerde capaciteits reservering grootschalige (Citus). Met name het aantal coördinator-of worker-knoop punten dat is vCores in de geselecteerde Azure-regio die wordt gereserveerd en die de facturerings korting krijgt. Als u bijvoorbeeld (of plant om te worden uitgevoerd) grootschalige (Citus)-Server groepen met de totale reken capaciteit van 64 Coordinator-knoop punt vCores en 32 worker-knoop punt vCores in de regio VS-Oost, geeft u het aantal op, respectievelijk 64 en 32 voor de coördinator en worker-knoop punten om het voor deel van alle servers te maximaliseren.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund) voor meer informatie.

## <a name="vcore-size-flexibility"></a>flexibiliteit van vCore-grootte

met de flexibiliteit van vCore-grootte kunt u de coördinator en worker-knoop punten binnen een regio omhoog of omlaag schalen zonder dat u het voor deel van de gereserveerde capaciteit kwijtraakt.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

De vCore-reserverings korting wordt automatisch toegepast op het aantal grootschalige (Citus)-Server groepen dat overeenkomt met de Azure Database for PostgreSQL gereserveerde capaciteits scope en-kenmerken. U kunt het bereik van de reserve ring van de gereserveerde capaciteit van Azure data base for PostgreSQL – grootschalige (Citus) bijwerken via Azure Portal, Power shell, CLI of via de API.

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

* [Wat zijn Azure-reserveringen?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Azure-reserveringen beheren](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Korting op Azure-reserveringen begrijpen](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](https://docs.microsoft.com/partner-center/azure-reservations)
