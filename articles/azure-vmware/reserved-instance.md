---
title: Bespaar kosten met het gereserveerde exemplaar van Azure VMware-oplossing
description: Meer informatie over het kopen van een gereserveerde instantie voor de Azure VMware-oplossing.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: bac2497c637a301c7ce8cbc44fc6945c3ef43b06
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370675"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Bespaar kosten met de Azure VMware-oplossing

Wanneer u een gereserveerde instantie van [Azure VMware-oplossing](introduction.md)doorvoert, bespaart u geld. De reserverings korting wordt automatisch toegepast op de actieve Azure VMware-oplossing hosts die overeenkomen met het reserverings bereik en de kenmerken. U hoeft geen reserve ring aan een speciale host toe te wijzen om de kortingen te krijgen. Een gereserveerde instantie koopt alleen op het reken deel van uw gebruik en bevat software licentie kosten. 


## <a name="purchase-restriction-considerations"></a>Overwegingen voor de aankoop beperking

Gereserveerde instanties zijn beschikbaar met enkele uitzonde ringen.

-   **Clouds** -reserve ringen zijn alleen beschikbaar in de regio's die worden vermeld op de pagina [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Onvoldoende quotum** : voor een reserverings scope in één/gedeeld abonnement moet het quotum van de host zijn opgenomen in het abonnement voor het nieuwe gereserveerde exemplaar. U kunt een [aanvraag voor quotum verhoging maken](enable-azure-vmware-solution.md) om dit probleem op te lossen.

-   **Aanbiedings recht**: u hebt een [Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) met micro soft nodig.

-   **Capaciteits beperkingen** : in zeldzame omstandigheden beperkt Azure de aanschaf van nieuwe reserve ringen voor Azure VMware Solution host sku's vanwege een geringe capaciteit in een regio.

## <a name="buy-a-reservation"></a>Een reservering kopen

U kunt een gereserveerd exemplaar van een Azure VMware Solution host-exemplaar kopen in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Betaal [vooraf of per maand](../cost-management-billing/reservations/prepare-buy-reservation.md) voor de reservering.

Deze vereisten zijn van toepassing op het kopen van een gereserveerde, toegewezen host-instantie:

-   U moet een rol van eigenaar zijn voor ten minste één EA-abonnement of een abonnement met een betalen naar gebruik-tarief.

-   Voor EA-abonnementen moet de optie **gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA-Portal](https://ea.azure.com/). Of, als deze instelling is uitgeschakeld, moet u een EA-beheerder van het abonnement zijn.

Een instantie kopen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer **Alle services** > **Reserveringen**.

3. Selecteer **toevoegen** om een nieuwe reserve ring te kopen en selecteer vervolgens **Azure VMware-oplossing**.

4. Voer de vereiste velden in. Als u de Azure VMware-oplossing host die overeenkomt met de kenmerken, selecteert u kwalificeren om de reserverings korting te krijgen. Het werkelijke aantal hosts van de Azure VMware-oplossing dat de korting krijgt, hangt af van het bereik en de geselecteerde hoeveelheid.

   Als u een EA-overeenkomst hebt, kunt u de **optie meer toevoegen** gebruiken om snel extra instanties toe te voegen. De optie is niet beschikbaar voor andere typen abonnementen.

   | Veld        |  Beschrijving |
   | ------------ | ------------ |
   | Abonnement | Het abonnement dat wordt gebruikt om te betalen voor de reserve ring. Via de betalingswijze voor het abonnement worden de kosten voor de reservering in rekening gebracht. Het abonnements type moet een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een micro soft-klant overeenkomst of een afzonderlijk abonnement met betalen per gebruik-tarieven (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). De kosten worden in mindering gebracht op het saldo van het reserveringsbedrag, indien beschikbaar, of in rekening gebracht als overschrijding. Voor een abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card-of factuur betalings methode voor het abonnement. |
   | Bereik        | Het bereik van de reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert:<br><ul><li><b>Bereik van één resource groep: past de reserverings korting alleen toe op de overeenkomende resources in de geselecteerde resource groep.</li><li><b>Bereik van één abonnement: past de reserverings korting toe op de overeenkomende resources in het geselecteerde abonnement.</li><li><b>Gedeeld bereik: past de reserverings korting toe op overeenkomende resources in in aanmerking komende abonnementen in de facturerings context. Voor EA-klanten is de facturerings context de inschrijving. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.</li></ul>       |
   | Regio       | De Azure-regio die wordt gedekt door de reserve ring.   |
   | Grootte van de host    | AV36    |
   | Termijn         | Eén jaar of drie jaar.  |
   | Aantal     | Het aantal exemplaren dat wordt aangeschaft binnen de reserve ring. De hoeveelheid is het aantal actieve Azure VMware-oplossings hosts dat de facturerings korting kan krijgen.    |

## <a name="usage-data-and-reservation-usage"></a>Gebruiks gegevens en reserverings gebruik

Uw gebruik dat een reserverings korting oplevert, heeft een goede prijs van nul. U kunt zien welk exemplaar van de Azure VMware-oplossing de reserverings korting voor elke reserve ring heeft ontvangen.

Voor meer informatie over hoe reserverings kortingen worden weer gegeven in gebruiks gegevens:

- Zie voor EA-klanten [inzicht in het gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- Zie [het gebruik van Azure-reserve ringen voor uw betalen per gebruik-abonnement](../cost-management-billing/reservations/understand-reserved-instance-usage.md) voor afzonderlijke abonnementen

## <a name="change-a-reservation-after-purchase"></a>Een reserve ring wijzigen na aankoop

Na aankoop kunt u de volgende typen wijzigingen aanbrengen in een reservering:

-   Het bereik van de reservering bijwerken

-   Flexibiliteit van de instantie grootte (indien van toepassing)

-   Eigendom

U kunt ook een reserve ring in kleinere segmenten splitsen of reserve ringen samen voegen. Geen van de wijzigingen veroorzaakt een nieuwe commerciële trans actie of wijzigt de eind datum van de reserve ring.

>[!NOTE]
>Zodra u de reserve ring hebt aangeschaft, kunt u de volgende typen wijzigingen niet rechtstreeks aanbrengen:
>
> - Een bestaande reserverings regio
> - SKU
> - Aantal
> - Duur
>
>U kunt echter wel een reserve ring *uitwisselen* als u wijzigingen wilt aanbrengen.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.