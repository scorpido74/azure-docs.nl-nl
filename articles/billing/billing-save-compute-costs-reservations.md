---
title: Wat zijn Azure-reserveringen?
description: Meer informatie over Azure-reserveringen en prijzen om geld te besparen op uw virtuele machines, SQL-databases, Azure Cosmos DB en andere resourcekosten.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 02a4ccc1bd7c18c36a7203f7d81cce8923ecf59a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499243"
---
# <a name="what-are-azure-reservations"></a>Wat zijn Azure-reserveringen?

Met behulp van Azure-reserveringen kunt u geld besparen omdat u zich vastlegt voor één of drie jaar aan rekencapaciteit van virtuele machines, SQL Database, Azure Cosmos DB-doorvoer of andere Azure-resources. Door u vast te leggen krijgt u korting op de resources die u gebruikt. Met reserveringen kunt u een aanzienlijke vermindering van kosten voor rekenkracht van virtuele machines, SQL Database, Azure Cosmos DB en andere resources realiseren van wel 72% vergeleken met prijzen voor betalen per gebruik. Reserveringen voorzien in een korting op de factuur en zijn niet van invloed op de runtime-status van uw resources.

U kunt vooraf of maandelijks voor een reservering betalen. De totale kosten van betalingen vooraf en per maand voor reserveringen zijn hetzelfde en u hoeft ook geen toeslag te betalen wanneer u voor maandelijks betalen kiest. Maandelijkse betaling is beschikbaar voor Azure-reserveringen, niet voor producten van derden.

U kunt een reservering kopen via [Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Redenen om een reservering te kopen

Als u virtuele machines, Azure Cosmos DB of SQL-databases hebt die gedurende een lange periode worden uitgevoerd en u een reservering koopt, profiteert u van de meest rendabele optie. Wanneer u bijvoorbeeld continu vier instanties van een service uitvoert zonder een reservering te gebruiken, wordt u gefactureerd met tarieven voor betalen per gebruik. Wanneer u een reservering voor die resources koopt, krijgt u direct korting op de reservering. De resources worden niet langer gefactureerd met de tarieven voor betalen per gebruik.

## <a name="charges-covered-by-reservation"></a>Kosten die onder de reservering vallen

Service-abonnementen:

- **Gereserveerde VM-instantie**: een reservering dekt alleen de rekenkosten van de virtuele machine. Aanvullende software-, netwerk- of opslagkosten vallen hier niet onder.
- **Gereserveerde Azure Cosmos DB-capaciteit**: onder een reservering valt de doorvoer die voor uw resources is ingericht. De opslag- en netwerkkosten vallen hier niet onder.
- **Gereserveerde SQL Database-vCore**: alleen de rekenkosten worden opgenomen in een reservering. De licentie wordt afzonderlijk gefactureerd.
- **SQL Data Warehouse**: onder een reservering valt het cDWU-gebruik. Een reservering omvat geen opslag- en netwerkkosten die samenhangen met het gebruik van SQL Data Warehouse.
- **App Service-zegelkosten**: onder een reservering valt het gebruik van zegels. De reservering is niet van toepassing op werkrollen, dus alle andere resources die aan de zegel zijn gekoppeld, worden afzonderlijk in rekening gebracht.

Voor virtuele Windows-machines en SQL Database kunt u de licentiekosten dekken met [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Wie komt in aanmerking voor de aanschaf van een reservering?

Als u een abonnement wilt kopen, moet u een eigenaarsrol voor het abonnement hebben in een zakelijke abonnement (MS-AZR-0017P of MS-AZR-0148P) of een abonnement waarvoor wordt betaald per gebruik (MS-AZR-0003P of MS-AZR-0023P) of een abonnement in een Microsoft-klantovereenkomst. Cloud Solution Providers kunnen Azure-reserveringen aanschaffen via Azure Portal of het  [Partnercentrum](/partner-center/azure-reservations) .

Enterprise Agreement-klanten (EA) kunnen het aantal aankopen voor EA-beheerders beperken door de optie **Gereserveerde instanties toevoegen** in de EA-portal uit te schakelen. EA-beheerders moeten een abonnementseigenaar zijn voor minimaal één EA-abonnement om een reservering te kunnen aanschaffen. De optie is nuttig voor bedrijven die hun reserveringen voor verschillende kostenplaatsen willen laten aanschaffen door een gecentraliseerd team. Na de aankoop kunnen gecentraliseerde teams kostenplaatseigenaars aan de reserveringen toevoegen. Eigenaars kunnen de reservering vervolgens aan hun abonnementen koppelen. Het centraleteam heeft geen toegang als abonnementseigenaar nodig tot de locatie waar de reservering wordt aangeschaft.

Reserveringskorting is alleen van toepassing op resources die zijn gekoppeld aan abonnementen die via Enterprise, een Cloud Solution Provider (CSP), een Microsoft-klantovereenkomst of afzonderlijk abonnementen met tarieven voor betalen per gebruik zijn aangeschaft.

## <a name="scope-reservations"></a>Reserveringen koppelen

U kunt een reservering aan een abonnement of aan resourcegroepen koppelen. Bij het instellen van het bereik voor een reservering selecteert u op welk punt de reserveringskorting van toepassing is. Wanneer u de reservering aan een resourcegroep koppelt, is reserveringskorting alleen van toepassing op de resourcegroep, niet op het hele abonnement.

### <a name="reservation-scoping-options"></a>Opties voor het koppelen van reserveringen

Met koppelen voor resourcegroepen beschikt u over drie opties om een reservering te koppelen, afhankelijk van uw behoeften:

- **Bereik van één resourcegroep**: de reserveringskorting wordt alleen toegepast op de overeenkomende resources in de geselecteerde resourcegroep.
- **Bereik van één abonnement**: de reserveringskorting wordt toegepast op de overeenkomende resources in het geselecteerde abonnement.
- **Gedeeld bereik**: de reserveringskorting wordt toegepast op overeenkomende resources binnen in aanmerking komende abonnementen die zich in de factureringscontext bevinden. Voor Enterprise Agreement-klanten is de inschrijving de factureringscontext. Voor Microsoft-klantovereenkomst-klanten is het factuurbereik het factureringsprofiel. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.

Wanneer u reserveringskorting op uw gebruik toepast, wordt de reservering in deze volgorde door Azure verwerkt:

1. Reserveringen die worden gekoppeld aan een resourcegroep
2. Reserveringen met één bereik
3. Reserveringen met gedeeld bereik

Voor één resourcegroep is reserveringskorting vanuit meerdere reserveringen beschikbaar, afhankelijk van de manier waarop u uw reserveringen hebt gekoppeld.

### <a name="scope-a-reservation-to-a-resource-group"></a>Een reservering aan een resourcegroep koppelen

U kunt de reservering aan een resourcegroep koppelen wanneer u de reservering koopt of het bereik na aankoop instelt. U moet een abonnementseigenaar zijn om de reservering aan een resourcegroep te koppelen.

Ga naar de pagina [Reservering kopen](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) in Azure Portal om het bereik in te stellen. Selecteer het reserveringstype dat u wilt kopen. In het selectieformulier **Het product selecteren dat u wilt aanschaffen** wijzigt u de waarde van het bereik in Eén resourcegroep. Selecteer vervolgens een resourcegroep.

![Voorbeeld van het selecteren van een VM-reservering die u wilt aanschaffen](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Aankoopaanbevelingen voor de resourcegroep in de reservering van de virtuele machine worden weergegeven. Aanbevelingen worden berekend door uw gebruik van de afgelopen 30 dagen te analyseren. Een aankoopaanbeveling wordt gemaakt als de kosten voor het uitvoeren van de resources met gereserveerde instanties lager zijn dan de kosten voor het uitvoeren van resources met tarieven voor betalen per gebruik. Zie [Aankoopaanbevelingen krijgen voor gereserveerde instanties op basis van gebruikspatronen](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations) voor meer informatie over aankoopaanbevelingen voor reserveringen.

U kunt het bereik altijd bijwerken nadat u een reservering hebt gekocht. Ga hiervoor naar de reservering, klik op **Configuratie** en koppel de reservering opnieuw. Het opnieuw koppelen van een reservering wordt niet als commerciële transactie beschouwd. Uw reserveringstermijn blijft ongewijzigd. Zie [Het bereik bijwerken na aanschaf van een reservering](billing-manage-reserved-vm-instance.md#change-the-reservation-scope) voor meer informatie over het bijwerken van het bereik.

![Voorbeeld van het wijzigen van een reserveringsbereik](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Reserveringsgebruik bewaken en optimaliseren

U kunt uw reserveringsgebruik op meerdere manieren bewaken, via Azure Portal, met API's of aan de hand van gebruiksgegevens. Ga naar **Reserveringen** in Azure Portal om alle reserveringen te zien waartoe u toegang hebt. In het raster met reserveringen ziet u het meest recent vastgelegde gebruikspercentage voor de reservering. Klik op de reservering om het gebruik van de reservering op lange termijn weer te geven.

U kunt ook reserveringsgebruik krijgen met behulp van [API's](billing-reservation-apis.md#see-reservation-usage) en vanuit uw [gebruiksgegevens](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) als u een Enterprise Agreement- of Microsoft-klantovereenkomst-klant bent.

Als u ziet dat het gebruik van uw aan een resourcegroep gekoppelde reservering laag is, kunt u het reserveringsbereik bijwerken naar een enkel abonnement of delen binnen de factureringscontext. U kunt de reservering ook splitsen en de resulterende reserveringen op verschillende resourcegroepen toepassen.

### <a name="other-considerations"></a>Andere overwegingen

Als u geen overeenkomende resources in een resourcegroep hebt, wordt er te weinig gebruikgemaakt van de reservering. De reservering wordt niet automatisch toegepast op een andere resourcegroep of een ander abonnement met laag gebruik.

Een reserveringsbereik wordt niet automatisch bijgewerkt als u de resourcegroep van het ene naar het abonnement verplaatst. Het bereik wordt niet bijgewerkt als u de resourcegroep verwijdert. U moet [de reservering opnieuw koppelen](billing-manage-reserved-vm-instance.md#change-the-reservation-scope). Anders wordt er niet genoeg gebruikgemaakt van de reservering.

## <a name="discounted-subscription-and-offer-types"></a>Typen abonnementen en aanbiedingen met korting

Reserveringskortingen zijn van toepassing op de volgende in aanmerking komende typen abonnementen en aanbiedingen.

- Enterprise Agreement (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P)
- Abonnementen met een Microsoft-klantovereenkomst.
- Afzonderlijke abonnementen met tarieven voor betalen naar gebruik (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P)
- CSP-abonnementen

Voor resources die voor een abonnement met andere typen aanbiedingen worden uitgevoerd, wordt geen reserveringskorting verkregen.

## <a name="how-is-a-reservation-billed"></a>Op welke manier wordt een reservering gefactureerd?

De aangeschafte reservering wordt verrekend volgens de betalingswijze die is gekoppeld aan het abonnement. De reserveringskosten worden afgetrokken van het saldo van uw financiële toezegging, indien beschikbaar. Wanneer het saldo van uw financiële toezegging ontoereikend is voor de kosten van de reservering, wordt de overschrijding gefactureerd. Als u een abonnement hebt van een afzonderlijk plan met tarieven voor betalen per gebruik, worden kosten voor vooruitbetalingen onmiddellijk in rekening gebracht op de creditcard die in uw account is geregistreerd. Maandelijkse betalingen worden op uw factuur weergegeven en deze kosten worden maandelijks op uw creditcard in rekening gebracht. Wanneer u per factuur wordt gefactureerd, ziet u de kosten op uw volgende factuur.

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

De reserveringskorting is van toepassing op het resourcegebruik dat overeenkomt met de kenmerken die u selecteert wanneer u de reservering koopt. Kenmerken zijn onder andere het bereik waarop de overeenkomende VM's, SQL-databases, Azure Cosmos DB of andere resources worden uitgevoerd. Als u bijvoorbeeld reserveringskorting voor vier Standard D2 virtuele machines in de regio US - west wilt gebruiken, selecteert u het abonnement waarop de VM's worden uitgevoerd.

Voor een reserveringskorting geldt: *gebruiken of verliezen*. Als u geen overeenkomende resources voor eender welk uur hebt, verliest u een reserveringshoeveelheid voor dat uur. U kunt niet-gebruikte gereserveerde uren niet meenemen.

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomende resources in het opgegeven bereik worden gevonden, *verliest* u de gereserveerde uren.

Stel dat u bijvoorbeeld later een resource wilt maken en dat u een ondergebruikte overeenkomende reservering hebt. De reserveringskorting is automatisch van toepassing op de nieuwe overeenkomende resource.

Als de virtuele machines worden uitgevoerd in verschillende abonnementen binnen uw inschrijving/account, selecteert u het bereik als Gedeeld. Dankzij gedeeld bereik kan de reserveringskorting worden toegepast op verschillende abonnementen. U kunt het bereik wijzigen nadat u een reservering hebt gekocht. Zie [Azure-reserveringen beheren](billing-manage-reserved-vm-instance.md) voor meer informatie.

Reserveringskorting is alleen van toepassing op resources die zijn gekoppeld met Enterprise, Microsoft-klantovereenkomst, CSP of abonnementen met tarieven voor betalen per gebruik. Voor resources die voor een abonnement met andere typen aanbiedingen worden uitgevoerd, wordt geen reserveringskorting verkregen.

## <a name="when-the-reservation-term-expires"></a>Wanneer de reserveringstermijn verloopt

Aan het einde van de reserveringtermijn verloopt de factureringskorting en worden kosten voor resources in rekening gebracht op basis van gebruik. De standaardinstelling is dat reserveringen niet automatisch worden verlengd. U kunt ervoor kiezen om een reservering automatisch te verlengen door de optie te selecteren in de verlengingsinstellingen. Met automatische verlenging wordt een vervangingsreservering aangeschaft wanneer de bestaande reservering verloopt. De vervangende reservering heeft standaard dezelfde kenmerken als de verlopende reservering. Het is mogelijk om de factureringsfrequentie, -termijn of -kwantiteit in de verlengingsinstellingen aan te passen. Gebruikers met eigenaarstoegang tot de reservering en het abonnement dat wordt gebruikt voor facturering kunnen verlenging instellen.  

## <a name="discount-applies-to-different-sizes"></a>De korting is van toepassing op verschillende grootten

Wanneer u een reservering koopt, kan de korting ook worden toegepast op andere instanties met kenmerken binnen de groep met VM's van dezelfde grootte. Deze functie wordt flexibiliteit van de instantiegrootte genoemd. De flexibiliteit van de kortingsdekking hangt af van het type reservering en de kenmerken die u kiest wanneer u de reservering koopt.

Service-abonnementen:

- Gereserveerde VM-instanties: Wanneer u de reservering koopt en **Geoptimaliseerd voor flexibiliteit van de instantiegrootte** selecteert, hangt de kortingsdekking af van de VM-grootte die u selecteert. De reservering kan worden toegepast op de VM-grootten in de groep met series van dezelfde grootte. Zie [Flexibiliteit van de VM-grootte met gereserveerde VM-instanties](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md) voor meer informatie.
- Gereserveerde capaciteit voor SQL Database: De kortingsdekking hangt af van de prestatielaag die u kiest. Zie [Begrip van de manier waarop Azure-reserveringskorting wordt toegepast](billing-understand-reservation-charges.md) voor meer informatie.
- Gereserveerde Azure Cosmos DB-capaciteit: De kortingsdekking hangt af van de ingerichte doorvoer. Zie [Begrip van de manier waarop Azure Cosmos DB-reserveringskorting wordt toegepast](billing-understand-cosmosdb-reservation-charges.md) voor meer informatie.

## <a name="reservation-notifications"></a>Reserveringsmeldingen

Afhankelijk van de manier waarop u voor uw Azure-abonnement betaalt, sturen we reserveringsmeldingen per e-mail naar de volgende gebruikers in uw organisatie. Meldingen worden verzonden voor verschillende gebeurtenissen, zoals:

- Kopen
- Reserveringen die binnenkort verlopen
- Vervaldatum
- Verlenging
- Opzegging
- Gewijzigd bereik

Voor klanten met EA-abonnementen:
- Meldingen over aankopen worden naar de koper en de contactpersonen voor EA-meldingen verzonden.
- Andere meldingen over de levenscyclus van de reservering worden alleen naar contactpersonen voor EA-meldingen verzonden.
- Gebruikers die aan een reservering zijn toegevoegd met behulp van de machtiging voor RBAC (IAM), krijgen geen enkele e-mailmelding.

Voor klanten met afzonderlijk abonnementen:
- De koper ontvangt een melding over de aankoop.
- Op het moment van de aankoop krijgt de eigenaar van het factureringsaccount van het abonnement een melding over de aankoop.
- De accounteigenaar ontvangt alle andere meldingen.


## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over Azure-reserveringen met de volgende artikelen:
    - [Azure-reserveringen beheren](billing-manage-reserved-vm-instance.md)
    - [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](billing-understand-reserved-instance-usage.md)
    - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](billing-understand-reserved-instance-usage-ea.md)
    - [Kosten van Windows-software zijn niet inbegrepen in reserveringen](billing-reserved-instance-windows-software-costs.md)
    - [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](/partner-center/azure-reservations)

- Meer informatie over reserveringen voor serviceplannen:
    - [Virtual Machines met Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB-resources met gereserveerde Azure Cosmos DB-capaciteit](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Rekenresources van SQL Database met gereserveerde capaciteit voor Azure SQL Database](../sql-database/sql-database-reserved-capacity.md) Meer informatie over reserveringen voor software-abonnementen:
    - [Red Hat-softwareabonnementen vanuit Azure-reserveringen](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [SUSE-softwareabonnementen vanuit Azure-reserveringen](../virtual-machines/linux/prepay-suse-software-charges.md)
