---
title: Bespaar kosten
description: Meer informatie over het kopen van gereserveerde capaciteit voor Azure SQL Database om te besparen op uw rekenkosten.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979991"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Kosten besparen voor SQL Database compute resources met gereserveerde Azure SQL Database

Bespaar geld met Azure SQL Database door een reservering voor compute resources te maken in vergelijking met betalen per gebruik.Save with Azure SQL Database by committing to a reservation for compute resources compared to pay-as-you-go prices. Met de gereserveerde capaciteit van Azure SQL Database maakt u een toezegging voor SQL Database-gebruik voor een periode van één of drie jaar om een aanzienlijke korting op de rekenkosten te krijgen. Als u de gereserveerde SQL Database-capaciteit wilt aanschaffen, moet u de Azure-regio, het implementatietype, de prestatielaag en de term opgeven.


U hoeft de reservering niet toe te wijzen aan specifieke SQL Database-exemplaren (afzonderlijke databases, elastische groepen of beheerde exemplaren). Overeenkomende SQL Database-exemplaren, die al worden uitgevoerd of die opnieuw zijn geïmplementeerd, krijgen automatisch het voordeel. Door een reservering aan te schaffen, verbindt u zich ertoe de rekenkosten voor een periode van één of drie jaar te gebruiken. Zodra u een reservering koopt, worden de compute-kosten van de SQL Database die overeenkomen met de reserveringskenmerken niet langer in rekening gebracht tegen de pay-as-you-go-tarieven. Een reservering heeft geen betrekking op software, netwerken of opslagkosten die zijn gekoppeld aan de SQL Database-instantie. Aan het einde van de reserveringsperiode verloopt het factureringsvoordeel en worden de SQL-databases gefactureerd tegen de pay-as-you-go-prijs. Reserveringen worden niet automatisch verlengd. Zie het aanbod [voor gereserveerde capaciteit van SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijsinformatie .

U gereserveerde azure-database-capaciteit kopen in de [Azure-portal.](https://portal.azure.com) Betaal [vooraf of per maand](../cost-management-billing/reservations/monthly-payments-reservations.md) voor de reservering. Ga als bedoeld als bedoeld als het gaat om gereserveerde SQL Database-capaciteit te kopen:

- U moet in de eigenaarrol zijn voor ten minste één Enterprise- of individueel abonnement met betalen per gebruik.
- Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com). Of als die instelling is uitgeschakeld, moet u een EA-beheerder op het abonnement zijn.
- Voor Het CSP-programma (Cloud Solution Provider) kunnen alleen de beheerders- of verkoopagenten gereserveerde SQL Database-capaciteit aanschaffen.

De details over hoe zakelijke klanten en Pay-As-You-Go-klanten in rekening worden gebracht voor reserveringsaankopen, zie [inzicht in azure-reserveringsgebruik voor uw Enterprise-inschrijving](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) en [inzicht in Azure-reserveringsgebruik voor uw Pay-As-You-Go-abonnement.](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-sql-size-before-purchase"></a>Bepaal de juiste SQL-grootte vóór aankoop

De grootte van de reservering moet worden gebaseerd op de totale hoeveelheid rekenkracht die wordt gebruikt door de bestaande of binnenkort te implementeren afzonderlijke databases, elastische pools of beheerde exemplaren binnen een specifieke regio en met dezelfde prestatielaag en hardwaregeneratie.

Stel dat u één algemeen doel hebt, Gen5 - 16 vCore-elastische pool en twee bedrijfskritische, Gen5 - 4 vCore-databases. Verder, laten we verondersteld dat u van plan bent om te implementeren binnen de volgende maand een extra algemeen doel, Gen5 - 16 vCore elastische pool, en een business critical, Gen5 - 32 vCore elastische pool. Ook, laten we aannemen dat je weet dat je deze middelen nodig hebt voor ten minste 1 jaar. In dit geval moet u een 32 (2x16) vCores, 1 jaar reserveren voor een enkele database / elastische pool algemeen doel - Gen5 en een 40 (2x4 + 32) vCore 1 jaar reserveren voor enkele database / elastische pool business critical - Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Reservecapaciteit voor SQL Database kopen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer **Toevoegen** en selecteer vervolgens in het deelvenster Inkoopreserveringen de optie **SQL Database** om een nieuwe reservering voor SQL Database aan te schaffen.
4. Vul de vereiste velden in. Bestaande of nieuwe afzonderlijke databases, elastische pools of beheerde exemplaren die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de gereserveerde capaciteitskorting. Het werkelijke aantal SQL Database-exemplaren dat de korting krijgt, is afhankelijk van de geselecteerde scope en hoeveelheid.
    ![Schermafbeelding voordat u de aankoop van de gereserveerde SQL-database voor gereserveerde capaciteit indient](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

In de volgende tabel worden de vereiste velden beschreven.

| Veld      | Beschrijving|
|------------|--------------|
|Abonnement|Het abonnement dat wordt gebruikt om te betalen voor de gereserveerde capaciteitsreservering van SQL Database. De betalingsmethode op het abonnement wordt in rekening gebracht de aanloopkosten voor de gereserveerde capaciteitsreservering van SQL Database. Het abonnementstype moet een ondernemingsovereenkomst zijn (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P) of een individuele overeenkomst met pay-as-you-go-prijzen (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P). Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het toezeggingsbedrag of ze worden in rekening gebracht als overschrijding. Voor een individueel abonnement met betalen per gebruik worden de kosten in rekening gebracht op de creditcard- of factuurbetalingsmethode op het abonnement.|
|Bereik       |Het bereik van de vCore-reservering kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u: <br/><br/>**Gedeeld**, wordt de vCore-reserveringskorting toegepast op SQL Database-exemplaren die worden uitgevoerd in abonnementen binnen uw factureringscontext. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat het alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten bestaat het gedeelde bereik uit alle abonnementen op gebruiksbasis gemaakt door de accountbeheerder.<br/><br/>**Eén abonnement**, de vCore-reserveringskorting wordt toegepast op SQL Database-exemplaren in dit abonnement. <br/><br/>**Eén resourcegroep**, wordt de reserveringskorting toegepast op SQL Database-exemplaren in het geselecteerde abonnement en de geselecteerde resourcegroep binnen dat abonnement.|
|Regio      |Het Azure-gebied dat onder de gereserveerde capaciteitsreservering van SQL Database valt.|
|Implementatietype|Het SQL-brontype waarvoor u de reservering wilt kopen.|
|Prestatielaag|De servicelaag voor de SQL Database-exemplaren.
|Termijn        |Een jaar of drie jaar.|
|Aantal    |De hoeveelheid compute resources die worden aangeschaft binnen de gereserveerde capaciteitsreservering van SQL Database. Het aantal is een aantal vCores in de geselecteerde Azure-regio en prestatielaag die worden gereserveerd en krijgt de factureringskorting. Als u bijvoorbeeld SQL Database-exemplaren uitvoert of van plan bent uit te voeren met de totale rekencapaciteit van Gen5 16 vCores in de regio Oost-VS, geeft u de hoeveelheid op als 16 om het voordeel voor alle instanties te maximaliseren. |

1. Bekijk de kosten van de gereserveerde capaciteitsreservering van SQL Database in de sectie **Kosten.**
1. Selecteer **Aankoop**.
1. Selecteer **Deze reservering weergeven** om de status van uw aankoop te bekijken.

## <a name="cancel-exchange-or-refund-reservations"></a>Annulering, omwisseling of terugbetaling van reserveringen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="vcore-size-flexibility"></a>vCore-grootteflexibiliteit

VCore-grootteflexibiliteit helpt u omhoog of omlaag te schalen binnen een prestatieniveau en regio, zonder het gereserveerde capaciteitsvoordeel te verliezen. SQL Database gereserveerde capaciteit biedt u ook de flexibiliteit om uw hot databases tijdelijk te verplaatsen tussen pools en enkele databases als onderdeel van uw normale bewerkingen (binnen dezelfde regio en prestatielaag) zonder de gereserveerde capaciteit te verliezen Voordeel. Door een niet-toegepaste buffer in uw reservering te houden, u de prestatiepieken effectief beheren zonder uw budget te overschrijden.

## <a name="limitation"></a>Beperking

U geen Op DTU gebaseerde (basis-, standaard- of premium) SQL-databases reserveren.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [maakt u een ondersteuningsverzoek.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Volgende stappen

De vCore-reserveringskorting wordt automatisch toegepast op het aantal SQL Database-exemplaren dat overeenkomt met de gereserveerde capaciteitsreserveringsscope en -kenmerken van de SQL Database-reservecapaciteit. U het bereik van de gereserveerde capaciteitsreservering van SQL Database bijwerken via [Azure portal,](https://portal.azure.com)PowerShell, CLI of via de API.

Zie [Gereserveerde capaciteit SQL Database beheren](../cost-management-billing/reservations/manage-reserved-vm-instance.md)voor meer informatie over het beheren van de gereserveerde SQL Database.

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn Azure-reserveringen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure-reserveringen beheren](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Korting op Azure-reserveringen begrijpen](../cost-management-billing/reservations/understand-reservation-charges.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](https://docs.microsoft.com/partner-center/azure-reservations)
