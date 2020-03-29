---
title: Gereserveerde capaciteit in Azure Cosmos DB om kosten te optimaliseren
description: Meer informatie over het kopen van gereserveerde capaciteit van Azure Cosmos DB om te besparen op uw rekenkosten.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 16e8f770445218e10ab7e7645a81325d11be55da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505965"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Kosten met gereserveerde capaciteit optimaliseren in Azure Cosmos DB

Gereserveerde Azure Cosmos DB-capaciteit helpt om geld te besparen doordat u zich verplicht tot een reservering voor Azure Cosmos DB-resources voor een periode van één of drie jaar. Met gereserveerde Azure Cosmos DB-capaciteit kunt u een korting krijgen op de doorvoer die wordt ingericht voor Cosmos DB-resources. Voorbeelden van resources zijn databases en containers (tabellen, verzamelingen en grafieken).

De gereserveerde capaciteit van Azure Cosmos&mdash;DB kan uw Cosmos DB-kosten tot 65 procent verlagen op reguliere prijzen met een verbintenis van één jaar of drie jaar vooraf. Gereserveerde capaciteit biedt een factureringskorting en heeft geen invloed op de runtime-status van uw Azure Cosmos DB-resources.

De gereserveerde capaciteit van Azure Cosmos DB dekt de doorvoer die voor uw resources is ingericht. De opslag- en netwerkkosten vallen hier niet onder. Zodra u een reservering koopt, worden de doorvoerkosten die overeenkomen met de reserveringskenmerken niet langer in rekening gebracht tegen de pay-as-you-go-tarieven. Zie het [azure-reserveringsartikel](../cost-management-billing/reservations/save-compute-costs-reservations.md) voor meer informatie over reserveringen.

U gereserveerde azure cosmos DB-capaciteit kopen via de [Azure-portal.](https://portal.azure.com) Betaal [vooraf of per maand](../cost-management-billing/reservations/monthly-payments-reservations.md) voor de reservering. Gereserveerde capaciteit kopen:

* U moet in de rol Eigenaar zijn voor ten minste één Enterprise- of individueel abonnement met betalen per gebruik.  
* Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com). Of als die instelling is uitgeschakeld, moet u een EA-beheerder op het abonnement zijn.
* Voor het CSP-programma (Cloud Solution Provider) kunnen alleen beheerders of verkoopagenten gereserveerde capaciteit van Azure Cosmos DB kopen.

## <a name="determine-the-required-throughput-before-purchase"></a>Bepaal de vereiste doorvoer vóór aankoop

De grootte van de aankoop van gereserveerde capaciteit moet worden gebaseerd op de totale hoeveelheid doorvoer die de bestaande of binnenkort te implementeren Azure Cosmos DB-resources op uurbasis zullen gebruiken. Bijvoorbeeld: Koop gereserveerde capaciteit van 30.000 RU/s als dat uw consistente gebruikspatroon per uur is. In dit voorbeeld wordt elke ingerichte doorvoer boven de 30.000 RU/s gefactureerd met uw pay-as-you-go-tarief. Als de ingerichte doorvoer in een uur onder de 30.000 RU/s ligt, wordt de extra gereserveerde capaciteit voor dat uur verspild.

We berekenen aankoopaanbevelingen op basis van uw gebruikspatroon per uur. Het gebruik in de afgelopen 7, 30 en 60 dagen wordt geanalyseerd en de aankoop van gereserveerde capaciteit wordt aanbevolen. U aanbevolen reserveringsgroottes in de Azure-portal weergeven met de volgende stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).  

2. Selecteer **Alle services** > **Reserveringen** > **toevoegen**.

3. Kies **Azure Cosmos DB**in het deelvenster **Reserveringen kopen** .

4. Selecteer het **tabblad Aanbevolen** om aanbevolen reserveringen weer te geven:

U aanbevelingen filteren op de volgende kenmerken:

- **Termijn** (1 jaar of 3 jaar)
- **Factureringsfrequentie** (maandelijks of vooraf)
- **Doorvoertype** (RU's vs Multi-master RU's)

Bovendien u aanbevelingen in één resourcegroep, één abonnement of uw volledige Azure-inschrijving gebruiken. 

Hier volgt een voorbeeldaanbeveling:

![Aanbevelingen voor gereserveerde capaciteit](./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png)

Deze aanbeveling om een reservering van 30.000 RU/s aan te schaffen geeft aan dat onder 3 jaar reserveringen een reserveringsgrootte van 30.000 RU/s besparingen zal maximaliseren. In dit geval wordt de aanbeveling berekend op basis van de afgelopen 30 dagen van azure cosmos DB-gebruik. Als deze klant verwacht dat de afgelopen 30 dagen van Azure Cosmos DB-gebruik representatief is voor toekomstig gebruik, zouden ze besparingen maximaliseren door een reservering van 30.000 RU/s aan te schaffen.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Gereserveerde Azure Cosmos DB-capaciteit kopen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).  

2. Selecteer **Alle services** > **Reserveringen** > **toevoegen**.  

3. Kies azure **cosmos DB** in het deelvenster **Reserveringen kopen** om een nieuwe reservering te kopen.  

4. Vul de vereiste velden in zoals beschreven in de volgende tabel:

   ![Vul het formulier voor gereserveerde capaciteit in](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |Veld  |Beschrijving  |
   |---------|---------|
   |Bereik   |   Optie waarmee wordt bepaalt hoeveel abonnementen het factureringsvoordeel kunnen gebruiken dat aan de reservering is gekoppeld. Het bepaalt ook hoe de reservering wordt toegepast op specifieke abonnementen. <br/><br/>  Als u **Gedeeld**selecteert, wordt de reserveringskorting toegepast op Azure Cosmos DB-exemplaren die worden uitgevoerd in een abonnement binnen uw factureringscontext. De factureringscontext is gebaseerd op de manier waarop u zich hebt aangemeld voor Azure. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat het alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten is het gedeelde bereik alle afzonderlijke abonnementen met betalen per gebruik-tarieven die door de accountbeheerder zijn gemaakt.  <br/><br/>  Als u **Eén abonnement selecteert,** wordt de reserveringskorting toegepast op Azure Cosmos DB-exemplaren in het geselecteerde abonnement. <br/><br/> Als u **Één resourcegroep**selecteert, wordt de reserveringskorting toegepast op Azure Cosmos DB-exemplaren in het geselecteerde abonnement en de geselecteerde resourcegroep binnen dat abonnement. <br/><br/> U het reserveringsbereik wijzigen nadat u de gereserveerde capaciteit hebt gekocht.  |
   |Abonnement  |   Abonnement dat wordt gebruikt om te betalen voor de gereserveerde azure cosmos DB-capaciteit. De betaalmethode op het geselecteerde abonnement wordt gebruikt bij het in rekening brengen van de kosten. Het abonnement moet een van de volgende typen zijn: <br/><br/>  Enterprise Agreement (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P): Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het monetaire toezeggingssaldo van de inschrijving of als overschrijding in rekening gebracht. <br/><br/> Individueel abonnement met betalen per gebruik -tarieven (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P): Voor een individueel abonnement met betalen per gebruik worden de kosten in rekening gebracht op de creditcard- of factuurbetaalmethode op het abonnement.    |
   | Resourcegroep | Resourcegroep waarop de gereserveerde capaciteitskorting wordt toegepast. |
   |Termijn  |   Een jaar of drie jaar.   |
   |Doorvoertype   |  Doorvoer is ingericht als aanvraageenheden. U een reservering kopen voor de ingerichte doorvoer voor beide opstellingen - één regio schrijft evenals meerdere regioschrijft. Het doorvoertype heeft twee waarden om uit te kiezen: 100 RU/s per uur en 100 Multi-master RU/s per uur.|
   | Gereserveerde capaciteitseenheden| De hoeveelheid doorvoer die u wilt reserveren. U deze waarde berekenen door de doorvoer te bepalen die nodig is voor al uw Cosmos DB-resources (bijvoorbeeld databases of containers) per regio. U vermenigvuldigt het vervolgens met het aantal regio's dat u aan uw Cosmos-database koppelt. Bijvoorbeeld: Als u vijf regio's met 1 miljoen RU/sec in elke regio hebt, selecteert u 5 miljoen RU/sec voor de aankoop van reserveringscapaciteit. |


5. Nadat u het formulier hebt ingevuld, wordt de prijs berekend die nodig is om de gereserveerde capaciteit aan te schaffen. De uitvoer toont ook het percentage van de korting die u krijgt met de gekozen opties. Klik volgende op **Selecteren**

6. Bekijk in het deelvenster **Reserveringen kopen** de korting en de prijs van de reservering. Deze reserveringsprijs is van toepassing op Azure Cosmos DB-resources met doorvoer die is ingericht in alle regio's.  

   ![Overzicht van gereserveerde capaciteit](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. Selecteer **Review + kopen** en koop **nu**. U ziet de volgende pagina wanneer de aankoop is geslaagd:

Nadat u een reservering hebt gekocht, wordt deze onmiddellijk toegepast op bestaande Azure Cosmos DB-resources die overeenkomen met de voorwaarden van de reservering. Als u geen bestaande Azure Cosmos DB-resources hebt, is de reservering van toepassing wanneer u een nieuw Cosmos DB-exemplaar implementeert dat overeenkomt met de voorwaarden van de reservering. In beide gevallen begint de reserveringsperiode direct na een succesvolle aankoop.

Wanneer uw reservering verloopt, blijven uw Azure Cosmos DB-exemplaren worden uitgevoerd en worden deze gefactureerd tegen de normale betalen per gebruik-tarieven.

## <a name="cancel-exchange-or-refund-reservations"></a>Annulering, omwisseling of terugbetaling van reserveringen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

De reserveringskorting wordt automatisch toegepast op de Azure Cosmos DB-resources die overeenkomen met het reserveringsbereik en de kenmerken. U het bereik van de reservering bijwerken via de Azure-portal, PowerShell, Azure CLI of de API.

*  Zie [De azure-reserveringskorting begrijpen](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)voor meer informatie over de manier waarop gereserveerde capaciteitskortingen worden toegepast op Azure Cosmos DB.

* Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

   * [Wat zijn Azure-reserveringen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Azure-reserveringen beheren](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Azure-reserveringen in het CSP-programma van het Partnercenter](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [maakt u een ondersteuningsverzoek.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
