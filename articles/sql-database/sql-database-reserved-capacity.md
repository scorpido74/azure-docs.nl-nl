---
title: Kosten besparen
description: Meer informatie over het kopen van Azure SQL Database gereserveerde capaciteit om uw reken kosten op te slaan.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 0713e1ed98cc87b95cad1f84672148cd062e0b58
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75979991"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Bespaar kosten voor SQL Database Compute-resources met Azure SQL Database gereserveerde capaciteit

Bespaar geld met Azure SQL Database door door te gaan naar een reserve ring voor reken resources vergeleken met de prijzen voor betalen per gebruik. Met Azure SQL Database gereserveerde capaciteit neemt u een toezeg ging voor SQL Database gebruik gedurende een periode van één of drie jaar om een aanzienlijke korting op de reken kosten te krijgen. Als u SQL Database gereserveerde capaciteit wilt kopen, moet u de Azure-regio, het implementatie type, de prestatie-laag en de periode opgeven.


U hoeft de reserve ring niet toe te wijzen aan specifieke SQL Database instanties (afzonderlijke data bases, elastische Pools of beheerde exemplaren). Overeenkomende SQL Database-exemplaren die al worden uitgevoerd of die nieuw zijn geïmplementeerd, krijgen automatisch het voor deel. Door een reserve ring te kopen, moet u het gebruik van de reken kosten voor een periode van één of drie jaar door voeren. Zodra u een reserve ring koopt, worden de kosten voor de SQL Database berekeningen die overeenkomen met de reserverings kenmerken niet meer in rekening gebracht tegen de betalen naar gebruik-tarieven. Een reserve ring geldt niet voor software-, netwerk-of opslag kosten die zijn gekoppeld aan het SQL Database exemplaar. Aan het einde van de reserverings termijn verloopt het factuur voordeel en worden de SQL-data bases gefactureerd op basis van de betalen naar gebruik-prijs. Reserve ringen worden niet automatisch vernieuwd. Voor prijs informatie raadpleegt u de [SQL database gereserveerde capaciteits aanbieding](https://azure.microsoft.com/pricing/details/sql-database/managed/).

U kunt Azure SQL Database gereserveerde capaciteit kopen in de [Azure Portal](https://portal.azure.com). Betaal [vooraf of per maand](../cost-management-billing/reservations/monthly-payments-reservations.md) voor de reservering. SQL Database gereserveerde capaciteit kopen:

- U moet de rol van eigenaar zijn voor minstens één bedrijf of een afzonderlijk abonnement met betalen per gebruik-tarieven.
- Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com). Als deze instelling is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement.
- Voor de Cloud Solution Provider (CSP)-programma kunnen alleen beheerders of verkoop medewerkers SQL Database gereserveerde capaciteit kopen.

De details over de manier waarop zakelijke klanten en betalen per gebruik-klanten in rekening worden gebracht voor reserverings aankopen, Zie [het gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) en [inzicht krijgen in het gebruik van Azure-reserve ringen voor uw abonnement op basis van betalen naar gebruik](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>De juiste SQL-grootte bepalen vóór de aankoop

De grootte van de reserve ring moet worden gebaseerd op de totale reken tijd die wordt gebruikt door de bestaande of binnenkort geïmplementeerde afzonderlijke data bases, elastische Pools of beheerde exemplaren binnen een bepaalde regio en met dezelfde prestatie-laag en hardwarematige generatie.

Stel bijvoorbeeld dat u een elastische, GEN5-16 vCore-pool gebruikt en twee essentiële, GEN5-4 vCore afzonderlijke data bases. Verder moet u in de volgende maand een extra algemeen doel, GEN5-16 vCore elastische pool en een elastische pool van het bedrijf kritiek, GEN5 – 32 vCore gebruiken. Stel dat u weet dat u deze resources ten minste één jaar nodig hebt. In dit geval moet u een 32 (2x16) vCores aanschaffen, een reserve ring van 1 jaar voor de enkelvoudige data base/elastische pool algemeen doel-GEN5 en een 40 (2x4 + 32) vCore 1 jaar reserve ring voor één data base/elastische groep bedrijfs kritiek-GEN5.

## <a name="buy-sql-database-reserved-capacity"></a>Reservecapaciteit voor SQL Database kopen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **alle services** > -**reserve ringen**.
3. Selecteer **toevoegen** en selecteer vervolgens in het deel venster reserve ringen **SQL database** om een nieuwe reserve ring voor SQL database aan te schaffen.
4. Vul de vereiste velden in. Bestaande of nieuwe afzonderlijke data bases, elastische Pools of beheerde exemplaren die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de gereserveerde capaciteits korting. Het werkelijke aantal exemplaren van uw SQL Database dat de korting krijgt, is afhankelijk van het bereik en de geselecteerde hoeveelheid.
    ![Scherm afbeelding voor het verzenden van de SQL Database gereserveerde capaciteits aanschaf](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

In de volgende tabel worden de vereiste velden beschreven.

| Veld      | Beschrijving|
|------------|--------------|
|Abonnement|Het abonnement dat wordt gebruikt om te betalen voor de reserve ring van de SQL Database gereserveerde capaciteit. Voor de betalings methode voor het abonnement worden de kosten vooraf in rekening gebracht voor de reserve ring van de SQL Database gereserveerde capaciteit. Het abonnements type moet een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een afzonderlijke overeenkomst met betalen per gebruik-prijs (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het toezeggingsbedrag of ze worden in rekening gebracht als overschrijding. Voor een individueel abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card of factuur betalings methode voor het abonnement.|
|Bereik       |Het bereik van de vCore-reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert: <br/><br/>**Gedeeld**, de vCore-reserverings korting wordt toegepast op SQL database exemplaren die worden uitgevoerd in abonnementen binnen uw facturerings context. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten bestaat het gedeelde bereik uit alle abonnementen op gebruiksbasis gemaakt door de accountbeheerder.<br/><br/>**Eén abonnement**, de vCore-reserverings korting wordt toegepast op SQL database exemplaren in dit abonnement. <br/><br/>**Eén resource groep**, de reserverings korting wordt toegepast op SQL database exemplaren in het geselecteerde abonnement en de geselecteerde resource groep binnen dat abonnement.|
|Regio      |De Azure-regio die wordt gedekt door de SQL Database gereserveerde capaciteits reservering.|
|Implementatie type|Het SQL-resource type waarvoor u de reserve ring wilt aanschaffen.|
|Prestatie niveau|De servicelaag voor de SQL Database exemplaren.
|Termijn        |Eén jaar of drie jaar.|
|Aantal    |De hoeveelheid reken resources die worden aangeschaft in de SQL Database gereserveerde capaciteits reservering. De hoeveelheid is een aantal vCores in de geselecteerde Azure-regio en-prestatie-laag die worden gereserveerd en de facturerings korting krijgt. Als u bijvoorbeeld uitvoert of van plan bent om SQL Database instanties uit te voeren met de totale reken capaciteit van GEN5 16 vCores in de regio VS-Oost, geeft u hoeveelheid op als 16 om het voor deel van alle exemplaren te maximaliseren. |

1. Controleer de kosten van de SQL Database gereserveerde capaciteits reservering in het gedeelte **kosten** .
1. Selecteer **Aankoop**.
1. Selecteer **deze reserve ring weer geven** om de status van uw aankoop te bekijken.

## <a name="cancel-exchange-or-refund-reservations"></a>Annulering, omwisseling of terugbetaling van reserveringen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [self-service-uitwisseling en terugbetalingen voor Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)voor meer informatie.

## <a name="vcore-size-flexibility"></a>flexibiliteit van vCore-grootte

met de flexibiliteit van vCore-grootte kunt u binnen een prestatie-laag en-regio omhoog of omlaag schalen zonder verlies van het voor deel van de gereserveerde capaciteit. SQL Database gereserveerde capaciteit biedt u de flexibiliteit om uw dynamische data bases tijdelijk te verplaatsen tussen Pools en individuele data bases als onderdeel van uw normale bewerkingen (binnen dezelfde regio en prestatie-laag) zonder verlies van het voor deel van de gereserveerde capaciteit. Door een niet-toegepaste buffer in uw reserve ring te bewaren, kunt u de prestatie pieken effectief beheren zonder uw budget te overschrijden.

## <a name="limitation"></a>Beperking

U kunt geen op DTU gebaseerde (Basic, Standard of Premium) SQL-data bases reserveren.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

De vCore-reserverings korting wordt automatisch toegepast op het aantal SQL Database exemplaren dat overeenkomt met het reserverings bereik en de kenmerken voor de gereserveerde capaciteit van de SQL Database. U kunt het bereik van de reserve ring van de SQL Database gereserveerde capaciteit bijwerken via [Azure Portal](https://portal.azure.com), Power shell, CLI of via de API.

Zie [SQL database gereserveerde capaciteit beheren](../cost-management-billing/reservations/manage-reserved-vm-instance.md)voor meer informatie over het beheren van de reserve ring voor gereserveerde capaciteit van SQL database.

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn Azure Reservations?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure-reserveringen beheren](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Korting op Azure-reserveringen begrijpen](../cost-management-billing/reservations/understand-reservation-charges.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](https://docs.microsoft.com/partner-center/azure-reservations)
