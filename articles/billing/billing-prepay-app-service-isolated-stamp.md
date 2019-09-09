---
title: Bespaar kosten voor Azure App Service geïsoleerde stempel kosten met gereserveerde capaciteit
description: Meer informatie over hoe u kosten kunt besparen voor Azure App Service de kosten voor een geïsoleerde stempel met gereserveerde capaciteit.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 5161f18499c082b7064eec2be612557ba09eec97
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806369"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Bespaar kosten voor Azure App Service geïsoleerde stempel kosten met gereserveerde capaciteit

U kunt geld besparen op Azure App Service kosten voor een afzonderlijke stempel door door te voeren naar een reserve ring voor uw stempel gebruik voor een duur van drie jaar. Als u de gereserveerde capaciteit voor de geïsoleerde stempel betaling wilt kopen, moet u de Azure-regio kiezen waar de stempel wordt geïmplementeerd en het aantal af te schaffen stem pels.

Wanneer u een reserve ring koopt, wordt het gebruik van de geïsoleerde stempel kosten dat overeenkomt met de reserverings kenmerken niet meer in rekening gebracht tegen de betalen naar gebruik-tarieven. De reserve ring wordt automatisch toegepast op het aantal geïsoleerde stem pels dat overeenkomt met het gereserveerde capaciteits bereik en de regio. U hoeft geen reserve ring toe te wijzen aan een geïsoleerd stempel. De reserve ring is niet van toepassing op werk nemers, dus alle andere resources die aan de stempel zijn gekoppeld, worden afzonderlijk in rekening gebracht.

Wanneer de gereserveerde capaciteit verloopt, blijven geïsoleerde stem pels actief, maar worden ze gefactureerd op basis van het tarief voor betalen naar gebruik. Reserveringen worden niet automatisch vernieuwd.

## <a name="determine-the-right-reservation-to-purchase"></a>Bepaal de juiste reserve ring voor aankoop

Door een reserve ring te kopen, moet u voor de volgende drie jaar een gereserveerde hoeveelheid gebruiken. Controleer uw gebruiks gegevens om te bepalen hoeveel App Service Isolated stem pels u consistent gebruikt en mogelijk in de toekomst.

Zorg er bovendien voor dat u begrijpt hoe de geïsoleerde stempel een Linux-of Windows-meter verzendt.

- Standaard wordt de Windows Stamp meter door een lege, geïsoleerde stempel verzonden. Bijvoorbeeld, zonder dat er werk nemers zijn geïmplementeerd. Deze meter wordt voortgezet als Windows-werk rollen op de stempel worden geïmplementeerd.
- De meter verandert in de Linux-stempel meter als u een Linux-werk nemer implementeert.
- In gevallen waarin zowel Linux-als Windows-werk rollen zijn geïmplementeerd, wordt de Windows-meter door de stempel verzonden.

De stempel meter kan dus worden gewijzigd tussen Windows en Linux gedurende de levens duur van de stempel.

Koop Windows Stamp-reserve ringen als u een of meer Windows-werk nemers hebt op de stempel. Het is de enige keer dat u een Linux-stempel reservering moet aanschaffen als u _alleen_ Linux-werk nemers op het stempel wilt hebben.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Gereserveerde capaciteit voor geïsoleerde stempel kopen

U kunt gereserveerde stempel capaciteit kopen in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Betaal voor de reserve ring [vóór of met maandelijkse betalingen](billing-monthly-payments-reservations.md). Als u gereserveerde capaciteit wilt kopen, moet u de rol eigenaar hebben voor ten minste één ondernemings abonnement of een afzonderlijk abonnement met betalen naar gebruik-tarieven.

- Voor ondernemings abonnementen moet de optie **gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA-Portal](https://ea.azure.com/). Als de instelling is uitgeschakeld, moet u een EA-beheerder zijn.
- Voor het programma Cloud Solution Provider (CSP) kunnen alleen beheerders of verkoop medewerkers SQL Data Warehouse gereserveerde capaciteit kopen.

**Te kopen:**

1. Ga naar de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Selecteer een abonnement. Gebruik de lijst met **abonnementen** om het abonnement te kiezen dat wordt gebruikt om te betalen voor de gereserveerde capaciteit. De betalings wijze van het abonnement wordt in rekening gebracht op de kosten voor de gereserveerde capaciteit. Het abonnements type moet een Enter prise Agreement zijn (nummers van aanbiedingen: MS-AZR-0017P of MS-AZR-0148P) of betalen naar gebruik (aantal aanbiedingen: MS-AZR-0003P of MS-AZR-0023P) of een CSP-abonnement.
    - Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het toezeggingsbedrag of ze worden in rekening gebracht als overschrijding.
    - Voor een Betalen per gebruik-abonnement worden de kosten in rekening gebracht op de creditcard of de factuurbetalingswijze van het abonnement.
1. Selecteer een **bereik** om een abonnements bereik te kiezen.
    - **Bereik van één resource groep** : past de reserverings korting alleen toe op de overeenkomende resources in de geselecteerde resource groep.
    - **Bereik van één abonnement** : past de reserverings korting toe op de overeenkomende resources in het geselecteerde abonnement.
    - **Gedeeld bereik** : past de reserverings korting toe op overeenkomende resources in in aanmerking komende abonnementen in de facturerings context. De facturerings context is voor Enterprise Agreement klanten de inschrijving. Voor afzonderlijke abonnementen met betalen per gebruik-tarieven geldt het facturerings bereik uit alle in aanmerking komende abonnementen die zijn gemaakt door de account beheerder.
1. Selecteer een **regio** om een Azure-regio te kiezen die wordt gedekt door de gereserveerde capaciteit en voeg de reserve ring toe aan de winkel wagen.
1. Selecteer een geïsoleerd plan type en klik vervolgens op **selecteren**.  
    ![Hierbij](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Voer het aantal App Service Isolated stem pels in dat moet worden gereserveerd. Een aantal van drie geeft u bijvoorbeeld drie gereserveerde stem pels een regio. Klik op **Next: Bekijk + kopen**.
1. Bekijk en klik op **Nu kopen**.

Na de aankoop gaat u naar [reserve ringen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) om de status van de aankopen te bekijken en deze op elk gewenst moment te controleren.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserve ringen annuleren, omruilen of terugbetalen

U kunt reserve ringen annuleren, vervangen of terugbetalen met bepaalde beperkingen. Zie [self-service-uitwisseling en terugbetalingen voor Azure Reservations](billing-azure-reservations-self-service-exchange-and-refund.md)voor meer informatie.

## <a name="discount-application-shown-in-usage-data"></a>Kortings toepassing die wordt weer gegeven in gebruiks gegevens

Uw gebruiks gegevens hebben een goede prijs van nul voor het gebruik dat een reserverings korting krijgt. De gebruiks gegevens tonen de reserverings korting voor elk stempel exemplaar in elke reserve ring.

Zie voor meer informatie over hoe reserverings korting wordt weer gegeven in gebruiks gegevens [ophalen Enterprise Agreement reserverings kosten en gebruik](billing-understand-reserved-instance-usage-ea.md) als u een debiteur van een Enterprise Agreement (EA) bent. Bekijk anders [het gebruik van Azure-reserve ringen voor uw afzonderlijke abonnement met betalen per gebruik-tarieven](billing-understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de volgende artikelen voor meer informatie over Azure Reservations:
  - [Wat zijn Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Inzicht krijgen in de manier waarop een Azure App Service voor de reserve ring van een geïsoleerde stempel wordt toegepast](billing-reservation-discount-app-service-isolated-stamp.md)
  - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](billing-understand-reserved-instance-usage-ea.md)
