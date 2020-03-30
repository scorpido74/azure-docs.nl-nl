---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cb959b94807678187363d3132ece273584f13a0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590736"
---
Bespaar op uw Azure Disk Storage-gebruik met gereserveerde capaciteit. Met Azure Disk Storage-reserveringen in combinatie met Azure Reserved Virtual Machine Instances u de totale vm-kosten (virtual machine) verlagen. De reserveringskorting wordt automatisch toegepast op de overeenkomende schijven in het geselecteerde reserveringsbereik. Vanwege deze automatische toepassing hoeft u geen reservering toe te wijzen aan een beheerde schijf om de kortingen te krijgen.

Kortingen worden elk uur toegepast, afhankelijk van het schijfgebruik. Ongebruikte gereserveerde capaciteit wordt niet overgedragen. Kortingen op azure diskopslagreserveringen zijn niet van toepassing op onbeheerde schijven, ultraschijven of paginablobverbruik.

## <a name="determine-your-storage-needs"></a>Bepaal uw opslagbehoeften

Voordat u een reservering aanschaft, bepaalt u uw opslagbehoeften. Momenteel zijn Azure Disk Storage-reserveringen alleen beschikbaar voor bepaalde Azure premium SSD SKU's. De SKU van een premium SSD bepaalt de grootte en prestaties van de schijf.

Bij het bepalen van uw opslagbehoeften, denk niet aan schijven op basis van alleen capaciteit. U bijvoorbeeld geen reservering hebben voor een P40-schijf en deze gebruiken om te betalen voor twee kleinere P30-schijven. Bij de aankoop van een reservering koopt u alleen een reservering voor het totale aantal schijven per SKU.

Er wordt een schijfreservering gemaakt per schijf SKU. Als gevolg hiervan is het reserveringsverbruik gebaseerd op de eenheid van de schijfSKU's in plaats van de opgegeven grootte.

Stel dat u één P40-schijf reserveert met 2 TiB met ingerichte opslagcapaciteit. Ga er ook van uit dat u slechts twee P30-schijven toewijst. De P40 reservering in dat geval geen rekening houdt met P30 verbruik, en u betaalt de pay-as-you-go tarief op de P30 schijven.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Aankoopoverwegingen

We raden de volgende praktijken aan bij het overwegen van schijfreserveringsaankopen:

- Analyseer uw gebruiksgegevens om te bepalen welke reserveringen u moet kopen. Zorg ervoor dat u het gebruik in schijfSKU's bijhoudt in plaats van de ingerichte of gebruikte schijfcapaciteit.
- Bekijk uw schijfreservering samen met uw VM-reservering. We raden ten zeerste aan om reserveringen te maken voor zowel VM-gebruik als schijfgebruik voor maximale besparingen. U beginnen met het bepalen van de juiste VM-reservering en vervolgens de schijfreservering evalueren. Over het algemeen hebt u een standaardconfiguratie voor elk van uw workloads. Een SQL Server-server kan bijvoorbeeld twee P40-gegevensschijven en één P30-besturingssysteemschijf hebben.
  
  Dit soort patroon kan u helpen bij het bepalen van het gereserveerde bedrag dat u zou kunnen kopen. Deze aanpak kan het evaluatieproces vereenvoudigen en ervoor zorgen dat u een uitgelijnd plan hebt voor zowel uw VM als schijven. Het plan bevat overwegingen zoals abonnementen of regio's.

## <a name="purchase-restrictions"></a>Aankoopbeperkingen

Reserveringskortingen zijn momenteel niet beschikbaar voor het volgende:

- Onbeheerde schijven of paginablobs.
- Standaard SSD's of standaard harde schijven (HDD's).
- Premium SSD SKU's kleiner dan P30: P1, P2, P3, P4, P6, P10, P15 en P20 SSD SKU's.
- Schijven in azure-, Azure-Duitsland- of Azure China-regio's.

In zeldzame omstandigheden beperkt Azure de aankoop van nieuwe reserveringen tot een subset van schijfSKU's vanwege de lage capaciteit in een regio.

## <a name="buy-a-disk-reservation"></a>Een schijfreservering kopen

U Azure Disk Storage-reserveringen aanschaffen via de [Azure-portal.](https://portal.azure.com/) U de reservering vooraf of met maandelijkse betalingen betalen. Zie [Reserveringen kopen met maandelijkse betalingen](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)voor meer informatie over de aankoop met maandelijkse betalingen.

Volg de volgende stappen om gereserveerde capaciteit aan te schaffen:

1. Ga naar het deelvenster [Reserveringen kopen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) in de Azure-portal.

1. Selecteer **Azure Managed Disks** om een reservering aan te schaffen.

    ![Deelvenster voor het kopen van reserveringen](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Geef de vereiste waarden op die in de volgende tabel zijn beschreven:

   |Element  |Beschrijving  |
   |---------|---------|
   |**Scope**   |  Hoeveel abonnementen het factureringsvoordeel kunnen gebruiken dat aan de reservering is gekoppeld. Deze waarde geeft ook aan hoe de reservering wordt toegepast op specifieke abonnementen. <br/><br/> Als u **Gedeeld**selecteert, wordt de reserveringskorting toegepast op Azure Storage-capaciteit in elk abonnement binnen uw factureringscontext. De factureringscontext is gebaseerd op de manier waarop u zich hebt aangemeld voor Azure. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat het alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten omvat het gedeelde bereik alle afzonderlijke abonnementen met betalen per gebruik-tarieven die door de accountbeheerder zijn gemaakt.  <br/><br/>  Als u **Eén abonnement selecteert,** wordt de reserveringskorting toegepast op Azure Storage-capaciteit in het geselecteerde abonnement. <br/><br/> Als u **Één resourcegroep**selecteert, wordt de reserveringskorting toegepast op azure-opslagcapaciteit in het geselecteerde abonnement en in de geselecteerde resourcegroep van dat abonnement. <br/><br/> U het reserveringsbereik wijzigen nadat u de reservering hebt gekocht.  |
   |**Abonnement**  | Het abonnement dat u gebruikt om te betalen voor de Azure Storage-reservering. De betaalmethode op het geselecteerde abonnement wordt gebruikt bij het in rekening brengen van de kosten. Het abonnement moet een van de volgende typen zijn:<br/><ul><li> Enterprise Agreement (aanbiedingsnummers MS-AZR-0017P en MS-AZR-0148P). Voor een Enterprise-abonnement worden de kosten afgetrokken van het monetaire toezeggingssaldo van de inschrijving of als overschrijding in rekening gebracht.</li><br/><li>Individueel abonnement met pay-as-you-go tarieven (aanbiedingnummers MS-AZR-0003P en MS-AZR-0023P). Voor een individueel abonnement met betalen per gebruik worden de kosten in rekening gebracht op de creditcard- of factuurbetalingsmethode op het abonnement.</li></ul>    |
   | **Schijven** | De SKU die u wilt maken. |
   | **Regio** | De regio waar de reservering van kracht is. |
   | **Factureringsfrequentie** | Hoe vaak de rekening wordt gefactureerd voor de reservering. Opties zijn **maandelijks** en **vooraf**. |

    ![Deelvenster voor het selecteren van het product dat u wilt kopen.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Nadat u de waarden voor uw reservering hebt opgegeven, worden de kosten in de Azure-portal weergegeven. De portal toont ook het kortingspercentage ten opzichte van pay-as-you-go facturering. Selecteer **Volgende** om door te gaan naar het deelvenster **Reserveringen kopen.**

1. In het deelvenster **Reserveringen kopen** u uw reservering een naam geven en het totale aantal reserveringen selecteren dat u wilt maken. Het aantal reserveringen kaarten aan het aantal schijven. Als u bijvoorbeeld honderd schijven wilt reserveren, voert u de **waarde Hoeveelheid** **100**in.

1. Bekijk de totale kosten van de reservering.

    ![Het deelvenster Reserveringen kopen](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Nadat u een reservering hebt gekocht, wordt deze automatisch toegepast op bestaande schijfopslagbronnen die overeenkomen met de reserveringsvoorwaarden. Als u nog geen bronnen voor schijfopslag hebt gemaakt, is de reservering van toepassing wanneer u een resource maakt die overeenkomt met de reserveringsvoorwaarden. In beide gevallen begint de reserveringstermijn onmiddellijk na een succesvolle aankoop.

## <a name="cancel-exchange-or-refund-reservations"></a>Annulering, omwisseling of terugbetaling van reserveringen

U reserveringen binnen bepaalde beperkingen annuleren, ruilen of terugbetalen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations) voor meer informatie.

## <a name="expiration-of-a-reservation"></a>Het verstrijken van een reservering

Wanneer een reservering verloopt, wordt elke Azure Disk Storage-capaciteit die u onder die reservering gebruikt, gefactureerd tegen het tarief voor betalen per gebruik. Reserveringen worden niet automatisch vernieuwd.

U ontvangt 30 dagen voor het verstrijken van de reservering een e-mailmelding en opnieuw op de vervaldatum. Als u wilt blijven profiteren van de kostenbesparingen die een reservering biedt, verlengt u deze niet later dan de vervaldatum.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [maakt u een ondersteuningsverzoek.](https://go.microsoft.com/fwlink/?linkid=2083458)

## <a name="next-steps"></a>Volgende stappen

- [Wat zijn Azure-reserveringen?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Reserveringskorting toepassen op Azure Disk Storage](../articles/cost-management-billing/reservations/understand-disk-reservations.md)
