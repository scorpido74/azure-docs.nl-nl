---
title: Bespaar kosten met het gereserveerde exemplaar van Azure VMware-oplossing
description: Meer informatie over het kopen van een gereserveerde instantie voor de Azure VMware-oplossing.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: a477b1730d70ec729a2be333b545b6faeb009998
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91492425"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Bespaar kosten met de Azure VMware-oplossing

Wanneer u een gereserveerde instantie van Azure VMware-oplossing doorvoert, kunt u geld besparen. De reserverings korting wordt automatisch toegepast op het aantal actieve Azure VMware Solution-hosts die overeenkomen met het reserverings bereik en de kenmerken. U hoeft geen reserve ring aan een speciale host toe te wijzen om de kortingen te krijgen. Een gereserveerde instantie koopt alleen het reken onderdeel van uw gebruik en bevat software licentie kosten. Zie het [overzicht van de Azure VMware-oplossing](introduction.md).

## <a name="purchase-restriction-considerations"></a>Overwegingen voor de aankoop beperking

Gereserveerde instanties zijn beschikbaar met enkele uitzonde ringen.

-   **Clouds**   -Reserve ringen zijn alleen beschikbaar in de regio's die worden vermeld op de pagina [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Onvoldoende quotum**   -Een reserve ring die is toegewezen aan een enkel/gedeeld abonnement moet hosts quota hebben die beschikbaar zijn in het abonnement voor het nieuwe gereserveerde exemplaar. U kunt een [aanvraag voor quotum verhoging maken](enable-azure-vmware-solution.md) om dit probleem op te lossen.

-   **Aanbiedings recht**: u hebt een [Azure Enterprise Agreement (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements)   met micro soft nodig.

-   **Capaciteits beperkingen**   -In zeldzame gevallen beperkt Azure de aanschaf van nieuwe reserve ringen voor Azure VMware Solution host Sku's, vanwege een geringe capaciteit in een regio.

## <a name="buy-a-reservation"></a>Een reservering kopen

U kunt een gereserveerd exemplaar van een Azure VMware Solution host-exemplaar kopen in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

Betaal voor de reserve ring [vóór of met maandelijkse betalingen](../cost-management-billing/reservations/prepare-buy-reservation.md).

Deze vereisten zijn van toepassing op het kopen van een gereserveerde, toegewezen host-instantie:

-   U moet een rol van eigenaar zijn voor ten minste één EA-abonnement of een abonnement met een betalen naar gebruik-tarief.

-   Voor EA-abonnementen moet de optie **gereserveerde instanties toevoegen**   zijn ingeschakeld in de [EA-Portal](https://ea.azure.com/). Of, als deze instelling is uitgeschakeld, moet u een EA-beheerder van het abonnement zijn.

Een instantie kopen:

1. Meld u aan bij de  [Azure-portal](https://portal.azure.com/).

2. Selecteer **alle services**-   >  **reserve ringen**.

3. Selecteer **toevoegen**   om een nieuwe reserve ring te kopen en selecteer vervolgens **Azure VMware-oplossing**.

4. Vul de verplichte velden in. Met de Azure VMware-oplossing-hosts die overeenkomen met de kenmerken die u selecteert, komt u in aanmerking voor de reserverings korting. Het werkelijke aantal hosts van de Azure VMware-oplossing dat de korting krijgt, is afhankelijk van het bereik en de geselecteerde hoeveelheid.

   Als u een EA-overeenkomst hebt, kunt u de **optie meer toevoegen**gebruiken   om snel extra instanties toe te voegen. De optie is niet beschikbaar voor andere typen abonnementen.

   | Veld        |  Beschrijving |
   | ------------ | ------------ |
   | Abonnement | Het abonnement dat wordt gebruikt om te betalen voor de reserve ring. Via de betalingswijze voor het abonnement worden de kosten voor de reservering in rekening gebracht. Het abonnements type moet een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een micro soft-klant overeenkomst of een afzonderlijk abonnement met betalen per gebruik-tarieven (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). De kosten worden in mindering gebracht op het saldo van het reserveringsbedrag, indien beschikbaar, of in rekening gebracht als overschrijding. Voor een abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card-of factuur betalings methode voor het abonnement. |
   | Bereik        | Het bereik van de reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert:<br><ul><li><b>Bereik van één resource groep: past de reserverings korting alleen toe op de overeenkomende resources in de geselecteerde resource groep.</li><li><b>Bereik van één abonnement: past de reserverings korting toe op de overeenkomende resources in het geselecteerde abonnement.</li><li><b>Gedeeld bereik: past de reserverings korting toe op overeenkomende resources in in aanmerking komende abonnementen in de facturerings context. Voor EA-klanten is de facturerings context de inschrijving. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.</li></ul>       |
   | Region       | De Azure-regio die wordt gedekt door de reserve ring.   |
   | Grootte van de host    | AV36    |
   | Term         | Eén jaar of drie jaar.  |
   | Hoeveelheid     | Het aantal exemplaren dat wordt aangeschaft binnen de reserve ring. De hoeveelheid is het aantal actieve Azure VMware-oplossings hosts dat de facturerings korting kan krijgen.    |

## <a name="usage-data-and-reservation-utilization"></a>Gebruiks gegevens en reserverings gebruik

Uw gebruiksgegevens kosten niets voor het deel waarvoor u een reserveringskorting verkrijgt. U kunt zien welk exemplaar van de Azure VMware-oplossing de reserverings korting voor elke reserve ring heeft ontvangen.

Zie [het gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)voor meer informatie over hoe reserverings kortingen worden weer gegeven in gebruiks gegevens en u bent een EA-klant. Zie [het gebruik van Azure-reserve ringen voor uw abonnement op basis van betalen per gebruik voor meer informatie](../cost-management-billing/reservations/understand-reserved-instance-usage.md)over een afzonderlijk abonnement.

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
> - Hoeveelheid
> - Duur
>
>U kunt echter wel *exchange*   een reserve ring uitwisselen als u wijzigingen wilt aanbrengen.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [self-service-uitwisseling en terugbetalingen voor Azure Reservations](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)voor meer informatie.
