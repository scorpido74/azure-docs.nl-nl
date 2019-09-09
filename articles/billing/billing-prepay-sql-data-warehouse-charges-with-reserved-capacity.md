---
title: Bespaar kosten voor SQL Data Warehouse kosten met gereserveerde Azure-capaciteit
description: Meer informatie over hoe u kosten bespaart voor SQL Data Warehouse kosten met gereserveerde capaciteit om geld te besparen.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 381a709c74f5fcf6bb1f89f07ad84d5e3af0c5e0
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806276"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Bespaar kosten voor SQL Data Warehouse kosten met gereserveerde capaciteit

U kunt geld besparen met Azure SQL Data Warehouse door door te voeren naar een reserve ring voor uw cDWU-gebruik gedurende een periode van één of drie jaar. Als u SQL Data Warehouse gereserveerde capaciteit wilt kopen, moet u de Azure-regio en de periode kiezen. Voeg daarna de SQL Data Warehouse-SKU toe aan uw winkelwagen en kies de hoeveelheid cDWU-eenheden die u wilt aanschaffen.

Wanneer u een reservering aanschaft, wordt het gebruik van SQL Data Warehouse dat met de reserveringskenmerken overeenkomt niet meer tegen het tarief voor betalen per gebruik in rekening gebracht.

Een reservering omvat geen opslag- en netwerkkosten die samenhangen met het gebruik van SQL Data Warehouse.

Wanneer de gereserveerde capaciteit vervalt, blijven de SQL Data Warehouse-exemplaren actief, maar wordt u gefactureerd tegen het tarief betalen per gebruik. Reserveringen worden niet automatisch vernieuwd.

Voor prijs informatie raadpleegt u de [SQL Data Warehouse gereserveerde capaciteits aanbieding](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

U kunt Azure SQL Data Warehouse gereserveerde capaciteit kopen in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Betaal voor de reserve ring [vóór of met maandelijkse betalingen](billing-monthly-payments-reservations.md). Gereserveerde capaciteit kopen:

- U moet de rol van eigenaar hebben voor ten minste één Enter prise-of betalen per gebruik-abonnement.
- Voor ondernemings abonnementen moet de optie **gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA-Portal](https://ea.azure.com/). Als de instelling is uitgeschakeld, moet u een EA-beheerder zijn.
- Voor het programma Cloud Solution Provider (CSP) kunnen alleen beheerders of verkoop medewerkers SQL Data Warehouse gereserveerde capaciteit kopen.

Zie het [gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](billing-understand-reserved-instance-usage-ea.md) en [inzicht krijgen in het gebruik van Azure-reserve ringen voor uw bedrijf voor meer informatie over de manier waarop zakelijke klanten en betalen per gebruik-klanten in rekening worden gebracht voor reserverings aankopen. Betalen per gebruik-abonnement](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Het juiste formaat kiezen voordat u het aankoopt

De SQL Data Warehouse reserverings grootte moet worden gebaseerd op de totale reken Data Warehouse-eenheden (cDWU) die u verbruikt. Aankopen worden gedaan in 100 cDWU-stappen.

Stel bijvoorbeeld dat uw totale verbruik van SQL Data Warehouse DW3000c is. U wilt gereserveerde capaciteit voor alles aanschaffen. Daarom moet u 30 eenheden van cDWU gereserveerde capaciteit kopen.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>SQL Data Warehouse gereserveerde capaciteit kopen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer **alle services** > -**reserve ringen**.
3. Selecteer een abonnement. Gebruik de lijst met abonnementen om het abonnement te kiezen dat wordt gebruikt om te betalen voor de gereserveerde capaciteit. De betalings wijze van het abonnement wordt in rekening gebracht op de kosten voor de gereserveerde capaciteit. Het abonnements type moet een Enter prise Agreement zijn (nummers van aanbiedingen: MS-AZR-0017P of MS-AZR-0148P) of betalen naar gebruik (aantal aanbiedingen: MS-AZR-0003P of MS-AZR-0023P).
   - Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het toezeggingsbedrag of ze worden in rekening gebracht als overschrijding.
   - Voor een Betalen per gebruik-abonnement worden de kosten in rekening gebracht op de creditcard of de factuurbetalingswijze van het abonnement.
4. Selecteer een bereik. Gebruik de scope lijst om een abonnements bereik te kiezen.
   - **Bereik van één resource groep** : past de reserverings korting alleen toe op de overeenkomende resources in de geselecteerde resource groep.
   - **Bereik van één abonnement** : past de reserverings korting toe op de overeenkomende resources in het geselecteerde abonnement.
   - **Gedeeld bereik** : past de reserverings korting toe op overeenkomende resources in in aanmerking komende abonnementen in de facturerings context. De facturerings context is voor Enterprise Agreement klanten de inschrijving. Voor afzonderlijke abonnementen met betalen per gebruik-tarieven geldt het facturerings bereik uit alle in aanmerking komende abonnementen die zijn gemaakt door de account beheerder.
   - Voor zakelijke klanten is de facturerings context de EA-inschrijving.
   - Voor betalen per gebruik-klanten is de gedeelde Scope alle abonnementen met betalen per gebruik, gemaakt door de account beheerder.
5. Selecteer een regio om een Azure-regio te kiezen die wordt gedekt door de gereserveerde capaciteit.
6. Kies een hoeveelheid. Voer het aantal 100 data warehouse-eenheden (cDWU) in dat u wilt kopen.    
   Zo geeft een hoeveelheid van 30 bijvoorbeeld elk uur 3.000 cDWU met gereserveerde capaciteit.
7. Bekijk de SQL Data Warehouse gereserveerde capaciteits kosten van de reserve ring in het gedeelte **kosten** .
8. Selecteer **Aankoop**.
9. Selecteer **deze reserve ring weer geven** om de status van uw aankoop te bekijken.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserve ringen annuleren, omruilen of terugbetalen

U kunt reserve ringen annuleren, vervangen of terugbetalen met bepaalde beperkingen. Zie [self-service-uitwisseling en terugbetalingen voor Azure Reservations](billing-azure-reservations-self-service-exchange-and-refund.md)voor meer informatie.

Er wordt automatisch een reserverings korting toegepast op het aantal SQL Data Warehouse exemplaren dat overeenkomt met het bereik en de regio van de gereserveerde capaciteit van SQL Data Warehouse. U kunt het bereik van de SQL Data Warehouse gereserveerde capaciteit bijwerken met de [Azure Portal](https://portal.azure.com/), Power shell, CLI of via de API.

## <a name="need-help-contact-us"></a>Hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [Maak een ondersteuningsaanvraag](https://portal.azure.com/).

## <a name="next-steps"></a>Volgende stappen

- Zie hoe reserverings kortingen van [toepassing zijn op Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md)voor meer informatie over hoe reserverings kortingen van toepassing zijn op Azure SQL Data Warehouse.

- Raadpleeg de volgende artikelen voor meer informatie over Azure Reservations:
  - [Wat zijn Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Azure-reserveringen beheren](billing-manage-reserved-vm-instance.md)
  - [Azure Reservations korting begrijpen](billing-understand-reservation-charges.md)
  - [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](billing-understand-reserved-instance-usage.md)
  - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](billing-understand-reserved-instance-usage-ea.md)
