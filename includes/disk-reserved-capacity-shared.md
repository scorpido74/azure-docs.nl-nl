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
ms.openlocfilehash: 89586f932ee358664a2869c87ced72594336b404
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847132"
---
Bespaard op uw Premium SSD-gebruik (Solid-State Drive) met gereserveerde capaciteit, gecombineerd met gereserveerde exemplaren van virtuele machines, kunt u de totale VM-kosten verlagen. De reserverings korting wordt automatisch toegepast op de overeenkomende schijven in het geselecteerde reserverings bereik. u hoeft geen reserve ring toe te wijzen aan een beheerde schijf om de kortingen op te halen. Kortingen worden op elk uur toegepast op het schijf gebruik en alle niet-gebruikte gereserveerde capaciteit wordt niet overgedragen. De korting voor een beheerde schijf reservering is niet van toepassing op niet-beheerde schijven, Ultra schijven of het gebruik van pagina-blobs.

## <a name="determine-your-storage-needs"></a>Uw opslag behoeften bepalen

Voordat u een reserve ring aanschaft, moet u bepalen wat uw opslag behoeften zijn. Op dit moment is schijf reservering alleen beschikbaar voor Select Premium SSD Sku's. De SKU van een Premium SSD bepaalt de grootte en prestaties van de schijf. Bij het bepalen van uw opslag behoeften raden wij u niet aan om te denken aan schijven, net als bij een totale capaciteit, kunt u geen reserve ring voor een grotere schijf gebruiken (zoals een P40) en gebruiken om te betalen voor twee kleinere schijven (P30). Bij het aanschaffen van een reserve ring koopt u alleen het totale aantal schijven per SKU.

Schijf reservering wordt per schijf-SKU gemaakt, daarom is het reserverings verbruik gebaseerd op de eenheid van de schijf-Sku's in plaats van de opgegeven grootte. Als u bijvoorbeeld een gereserveerde capaciteit van 1 P40 van 2 TiB hebt, maar alleen 2 P30-schijven hebt toegewezen, wordt het twee P30 verbruik niet verwerkt voor P40-reserve ring en betaalt u het betalen naar gebruik-tarief.



[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Overwegingen bij de aankoop

We raden u aan de volgende aanbevolen procedures uit te voeren bij het overwegen van een aankoop van schijf reservering:

- Analyseer uw gebruiks gegevens om te helpen bepalen welke reserve ringen u moet aanschaffen. Zorg ervoor dat u het gebruik volgt in schijf-Sku's in plaats van de ingerichte schijf capaciteit. 
- Controleer uw schijf reservering samen met uw VM-reserve ring. Het wordt ten zeerste aangeraden om reserve ring te maken voor zowel het VM-als het schijf gebruik voor maximale opslag. U kunt beginnen met het bepalen van de juiste VM-reserve ring en vervolgens de schijf reservering evalueren. Over het algemeen hebt u een standaard configuratie voor elk van uw workloads, bijvoorbeeld een SQL-Server kan twee P40-gegevens schijven en één P30-besturingssysteem schijf bevatten. Dit soort patroon kan u helpen bij het bepalen van de hoeveelheid reserve ringen die u kunt aanschaffen. Deze aanpak kan het evaluatie proces vereenvoudigen en er ook voor zorgen dat u beschikt over een uitgelijnd abonnement voor zowel virtuele machines als schijven, zoals abonnementen, regio's en anderen. 

## <a name="purchase-restrictions"></a>Aankoop beperkingen

Er zijn momenteel geen reserverings kortingen beschikbaar voor de volgende schijven:
- Niet-beheerde schijven/pagina-blobs
- Standard-SSD of standaard HDD
- Premium-SSD Sku's die kleiner zijn dan P30: reserve ringen zijn niet beschikbaar voor P1/P2/P3/P4/P6/P10/P15/P20 Premium-SSD Sku's.
- Clouds-reserve ringen zijn niet beschikbaar voor aankopen in regio's van Azure gov, Duitsland en China.
- Capaciteits beperkingen: in zeldzame omstandigheden beperkt Azure de aanschaf van nieuwe reserve ringen voor de subset van schijf-Sku's, vanwege een geringe capaciteit in een regio.

## <a name="buy-a-disk-reservation"></a>Een schijf reservering aanschaffen

U kunt Azure-schijf reserveringen kopen via de [Azure Portal](https://portal.azure.com/). U kunt betalen voor de reserve ring vóór of met maandelijkse betalingen. Zie [reserve ringen met maandelijkse betalingen kopen](../articles/cost-management-billing/reservations/monthly-payments-reservations.md)voor meer informatie over het aanschaffen met maandelijkse betalingen.

Volg deze stappen om gereserveerde capaciteit aan te schaffen:

1. Navigeer naar de Blade [aankoop reserveringen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) in het Azure Portal.
1. Selecteer **Azure Managed disks** om een reserve ring aan te schaffen.

    ![disks-reserved-Purchase-reservation. png](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png)    

1. Vul de vereiste velden in zoals beschreven in de volgende tabel:

   |Veld  |Beschrijving  |
   |---------|---------|
   |**Bereik**   |  Hiermee wordt aangegeven hoeveel abonnementen het facturerings voordeel kunnen gebruiken dat is gekoppeld aan de reserve ring. Het bepaalt ook hoe de reserve ring wordt toegepast op specifieke abonnementen. <br/><br/> Als u **gedeeld**selecteert, wordt de reserverings korting toegepast op Azure storage capaciteit in een abonnement binnen uw facturerings context. De facturerings context is gebaseerd op de manier waarop u zich hebt geregistreerd voor Azure. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten bevat het gedeelde bereik alle afzonderlijke abonnementen met betalen per gebruik-tarieven die zijn gemaakt door de account beheerder.  <br/><br/>  Als u **één abonnement**selecteert, wordt de reserverings korting toegepast op Azure storage capaciteit in het geselecteerde abonnement. <br/><br/> Als u **één resource groep**selecteert, wordt de reserverings korting toegepast op Azure storage capaciteit in het geselecteerde abonnement en de geselecteerde resource groep in dat abonnement. <br/><br/> U kunt het reserverings bereik wijzigen nadat u de reserve ring hebt aangeschaft.  |
   |**Abonnement**  | Het abonnement dat wordt gebruikt om te betalen voor de Azure Storage reserve ring. De betalings wijze voor het geselecteerde abonnement wordt gebruikt bij het opladen van de kosten. Het abonnement moet een van de volgende typen zijn: <br/><br/>  Enterprise Agreement (nummers van aanbiedingen: MS-AZR-0017P of MS-AZR-0148P): voor een Enter prise-abonnement worden de kosten in rekening gebracht op basis van de monetaire toezeg ging van de inschrijving of worden aangerekend als overschrijding. <br/><br/> Individueel abonnement met betalen per gebruik-tarieven (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P): voor een afzonderlijk abonnement met betalen per gebruik-tarieven, worden de kosten in rekening gebracht op de credit card-of factuur betalings methode voor het abonnement.    |
   | **Schijven** | De SKU die u wilt maken. |
   | **Regio** | De regio waar de reserve ring van kracht is. |
   | **Facturerings frequentie** | Hiermee wordt aangegeven hoe vaak het account wordt gefactureerd voor de reserve ring. De opties zijn *maandelijks* of *vooraf*. |

    ![Premium-SSD-reserved-Purchase-Selection. png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Nadat u de para meters voor de reserve ring hebt geselecteerd, worden de kosten weer gegeven in de Azure Portal. In de portal wordt ook het kortings percentage weer gegeven over betalen per gebruik-facturering. Selecteer **volgende** om door te gaan naar de Blade **aankoop reserveringen** .

1. In het deel venster **reserve ringen voor aankoop** kunt u uw reserve ring een naam geven en het totale aantal reserve ringen selecteren dat u wilt maken. Het aantal reserve ringen dat is toegewezen aan het aantal schijven. Als u bijvoorbeeld honderden schijven wilt reserveren, wijzigt u de **hoeveelheid** in 100.
1. Bekijk de totale kosten van de reserve ring.

    ![Premium-SSD-reserved-selecting-SKU-Total-purchase. png](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Nadat u een reserve ring hebt aangeschaft, wordt deze automatisch toegepast op alle bestaande Azure Disk Storage-resources die overeenkomen met de voor waarden van de reserve ring. Als u nog geen Azure Disk Storage-resources hebt gemaakt, wordt de reserve ring toegepast wanneer u een resource maakt die overeenkomt met de voor waarden van de reserve ring. In beide gevallen begint de periode van de reserve ring onmiddellijk na een geslaagde aankoop.

## <a name="exchange-or-refund-a-reservation"></a>Een reserve ring uitwisselen of terugbetalen

Met bepaalde beperkingen kunt u een reserve ring uitwisselen of terugbetalen.

Als u een reserve ring wilt omruilen of terugbetalen, gaat u naar de reserverings details in het Azure Portal. Selecteer **Exchange of terugbetaling**en volg de instructies om een ondersteunings aanvraag in te dienen. Wanneer de aanvraag is verwerkt, ontvangt micro soft u een e-mail om de voltooiing van de aanvraag te bevestigen.

Zie voor meer informatie over Azure Reservations-beleid [self-service uitwisselingen en terugstortingen voor Azure Reservations](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Een reserve ring uitwisselen

Door een reserve ring uit te wisselen, kunt u een evenredige restitutie ontvangen op basis van het niet-gebruikte gedeelte van de reserve ring. U kunt de terugbetaling vervolgens Toep assen op de aankoop prijs van een nieuwe Azure-schijf reservering.
Er bestaat geen limiet voor het aantal uitwisselingen. Daarnaast zijn er geen kosten verbonden aan een uitwisseling. De nieuwe reserve ring die u aanschaft, moet gelijk zijn aan of hoger zijn dan het gefactureerde tegoed van de oorspronkelijke reserve ring. Een Azure-schijf reservering kan alleen worden uitgewisseld voor een andere Azure-schijf reservering en niet voor een reserve ring voor een andere Azure-service.

### <a name="refund-a-reservation"></a>Een reserve ring terugbetalen

U kunt op elk gewenst moment een Azure-schijf reservering annuleren. Als u annuleert, ontvangt u een gefactureerde restitutie op basis van de resterende termijn van de reserve ring, min de kosten voor een vroege beëindiging van 12 procent. De maximale terugbetaling per jaar is $50.000.
Annuleren van een reserve ring beëindigt onmiddellijk de reserve ring en retourneert de resterende maanden naar micro soft. Het resterende tarief saldo, min de vergoeding, wordt terugbetaald naar de oorspronkelijke aankoop vorm.

## <a name="expiration-of-a-reservation"></a>Verval datum van een reserve ring

Wanneer een reserve ring verloopt, wordt elke Azure-schijf capaciteit die u gebruikt onder die reserve ring, gefactureerd op basis van het betalen naar gebruik-tarief. Reserveringen worden niet automatisch vernieuwd.
U ontvangt een e-mail melding van 30 dagen vóór het verstrijken van de reserve ring en opnieuw op de verval datum. Om te kunnen profiteren van de kosten besparingen die een reserve ring biedt, verlengt u deze niet later dan de verval datum.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Wat zijn Azure-reserveringen?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Begrijpen hoe uw reserverings korting wordt toegepast op Azure Disk Storage](../articles/cost-management-billing/reservations/understand-disk-reservations.md)