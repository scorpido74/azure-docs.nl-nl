---
title: Wat zijn Azure-reserveringen?
description: Meer informatie over Azure Reservations en prijzen om uw virtuele machines, SQL-data bases, Azure Cosmos DB en andere resource kosten op te slaan.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: bb90a9dec161746356b8c13df448718c53626684
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806341"
---
# <a name="what-are-azure-reservations"></a>Wat zijn Azure-reserveringen?

Met Azure Reservations kunt u geld besparen door over te slaan naar een virtuele machine van één of drie jaar, SQL Database reken capaciteit, Azure Cosmos DB door Voer of andere Azure-resources. Door door te voeren kunt u een korting krijgen op de resources die u gebruikt. Met reserve ringen kunt u uw virtuele machine aanzienlijk verlagen, SQL database reken-, Azure Cosmos DB-of andere resource kosten tot 72% op prijzen voor betalen per gebruik. Reserve ringen bieden een facturerings korting en zijn niet van invloed op de runtime status van uw resources.

U kunt een reserve ring vooraf of maandelijks betalen. De totale kosten vooraf en maandelijkse reserve ringen zijn hetzelfde en u betaalt geen extra kosten wanneer u maandelijks betaalt. Maandelijkse betaling is beschikbaar voor Azure-reserve ringen, niet voor producten van derden.

U kunt een reserve ring kopen in de [Azure Portal](https://ms.portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

## <a name="why-buy-a-reservation"></a>Waarom een reserve ring aanschaffen?

Als u virtuele machines, Azure Cosmos DB of SQL-data bases hebt die gedurende lange tijd worden uitgevoerd, kunt u met een reserve ring de meest rendabele optie gebruiken. Wanneer u bijvoorbeeld continu vier exemplaren van een service zonder een reserve ring uitvoert, worden de betalen naar gebruik-tarieven in rekening gebracht. Wanneer u een reserve ring voor deze resources koopt, krijgt u onmiddellijk de reserverings korting. De resources worden niet meer in rekening gebracht op basis van de betalen naar gebruik-tarieven.

## <a name="charges-covered-by-reservation"></a>Kosten gedekt door reserve ring

Service plannen:

- **Gereserveerde virtuele machine-instantie** : een reserve ring geldt alleen voor de reken kosten van de virtuele machine. Dit geldt niet voor extra software-, netwerk-of opslag kosten.
- **Gereserveerde capaciteit Azure Cosmos DB** : een reserve ring dekt de door Voer ingericht voor uw resources. Dit geldt niet voor de kosten voor opslag en netwerken.
- **SQL database gereserveerde vCore** : alleen de reken kosten zijn opgenomen in een reserve ring. De licentie wordt afzonderlijk in rekening gebracht.
- **SQL Data Warehouse** : een reserve ring geldt voor het cDWU-gebruik. Dit geldt niet voor opslag-of netwerk kosten die zijn gekoppeld aan het SQL Data Warehouse gebruik.
- **Kosten voor app service stempel** : een reserve ring dekt het stempel gebruik. Het is niet van toepassing op werk nemers, dus alle andere resources die aan de stempel zijn gekoppeld, worden afzonderlijk in rekening gebracht.

Voor virtuele Windows-machines en SQL Database, kunt u de licentie kosten met [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)bedekking.

## <a name="whos-eligible-to-purchase-a-reservation"></a>Wie komt in aanmerking voor aankoop van een reserve ring?

Als u een plan wilt kopen, moet u een rol voor abonnements eigenaar hebben in een Enter prise (MS-AZR-0017P-of MS-AZR-0148P) of betalen per gebruik-abonnement (MS-AZR-0003P of MS-AZR-0023P). Cloud solution providers kunnen de Azure portal of het [partner centrum](/partner-center/azure-reservations) gebruiken om Azure Reservations te kopen.

Enterprise Agreement (EA)-klanten kunnen aankopen beperken tot EA-beheerders door de optie **gereserveerde instanties toevoegen** uit te scha kelen in de EA-Portal. EA-beheerders moeten een eigenaar van het abonnement zijn voor ten minste één EA-abonnement voor het aanschaffen van een reserve ring. De optie is handig voor bedrijven die willen dat een gecentraliseerd team reserve ringen voor verschillende kosten plaatsen kan aanschaffen. Na de aankoop kunnen gecentraliseerde teams eigen aren van kosten plaatsen toevoegen aan de reserve ringen. Eigen aren kunnen vervolgens de reserve ring op hun abonnementen bereiken. Het centrale team hoeft geen eigenaar van het abonnement te hebben waar de reserve ring wordt aangeschaft.

Een reserverings korting geldt alleen voor resources die zijn gekoppeld aan abonnementen die zijn aangeschaft via Enter prise, Cloud Solution Provider (CSP) en individuele abonnementen met betalen naar gebruik-tarieven.

## <a name="scope-reservations"></a>Bereik reserveringen

U kunt een reserve ring voor een abonnement of resource groepen bereiken. Wanneer u het bereik voor een reserve ring instelt, selecteert u waar de reserverings besparingen van toepassing zijn. Wanneer u de reserve ring naar een resource groep verwerkt, zijn de reserverings kortingen alleen van toepassing op de resource groep, niet op het hele abonnement.

### <a name="reservation-scoping-options"></a>Opties voor reserverings bereik

Met het bereik van de resource groep hebt u drie opties om een reserve ring te bereiken, afhankelijk van uw behoeften:

- **Bereik van één resource groep** : past de reserverings korting alleen toe op de overeenkomende resources in de geselecteerde resource groep.
- **Bereik van één abonnement** : past de reserverings korting toe op de overeenkomende resources in het geselecteerde abonnement.
- **Gedeeld bereik** : past de reserverings korting toe op overeenkomende resources in in aanmerking komende abonnementen in de facturerings context. De facturerings context is voor Enterprise Agreement klanten de inschrijving. Voor afzonderlijke abonnementen met betalen per gebruik-tarieven geldt het facturerings bereik uit alle in aanmerking komende abonnementen die zijn gemaakt door de account beheerder.

Tijdens het Toep assen van reserverings kortingen wordt de reserve ring in de volgende volg orde verwerkt door Azure:

1. Reserve ringen die binnen het bereik van een resource groep vallen
2. Reserve ringen voor één bereik
3. Gedeelde bereik reserveringen

Een enkele resource groep kan reserve ringen ophalen van meerdere reserves, afhankelijk van hoe u uw reserve ringen bereikt.

### <a name="scope-a-reservation-to-a-resource-group"></a>Een reserve ring van een resource groep beperken

U kunt het bereik van de reserve ring naar een resource groep instellen wanneer u de reserve ring koopt of het bereik na aankoop hebt ingesteld. U moet een eigenaar van het abonnement zijn om de reserve ring voor een resource groep te kunnen bereiken.

Als u het bereik wilt instellen, gaat u naar de pagina [reserve ring inkoop](https://ms.portal.azure.com/#blade/Microsoft\_Azure\_Reservations/CreateBlade/referrer/Browse\_AddCommand) in de Azure Portal. Selecteer het reserverings type dat u wilt kopen. Wijzig op het selectie **vakje product selecteren dat u wilt kopen** de bereik waarde in één resource groep. Selecteer vervolgens een resource groep.

![Voor beeld van een aankoop selectie voor VM-reserve ringen](./media/billing-save-compute-costs-reservations/select-product-to-purchase.png)

Er worden aanbevelingen voor de resource groep in de virtuele-machine reservering weer gegeven. Aanbevelingen worden berekend door uw gebruik in de afgelopen 30 dagen te analyseren. Er wordt een aankoop aanbeveling gedaan als de kosten voor het uitvoeren van resources met gereserveerde instanties goed koper zijn dan de kosten van het uitvoeren van resources met betalen naar gebruik-tarieven. Zie voor meer informatie over aanbevelingen voor reserverings aankopen [ophalen gereserveerde instanties aankoop aanbevelingen op basis van gebruiks patroon](https://azure.microsoft.com/blog/get-usage-based-reserved-instance-recommendations).

U kunt het bereik altijd bijwerken wanneer u een reserve ring koopt. Als u dit wilt doen, gaat u naar de reserve ring, klikt u op **configuratie**en verbereikt u de reserve ring. Rescoping een reserve ring is geen commerciële trans actie. De reserverings termijn is niet gewijzigd. Zie [het bereik bijwerken na aankoop van een reserve ring](billing-manage-reserved-vm-instance.md#change-the-reservation-scope)voor meer informatie over het bijwerken van het bereik.

![Voor beeld van een wijziging van het reserverings bereik](./media/billing-save-compute-costs-reservations/rescope-reservation-resource-group.png)

### <a name="monitor-and-optimize-reservation-usage"></a>Reserverings gebruik controleren en optimaliseren

U kunt uw reserverings gebruik op verschillende manieren bewaken: via Azure Portal, via Api's of met behulp van gebruiks gegevens. Als u alle reserve ringen wilt zien waartoe u toegang hebt, gaat u naar **reserve ringen** in de Azure Portal. Het raster reserve ringen toont het laatst vastgelegde verbruiks percentage voor de reserve ring. Klik op de reserve ring om de lange termijn capaciteit van de reserve ring te bekijken.

U kunt ook reserverings gebruik ophalen met behulp van [api's](billing-reservation-apis.md#see-reservation-usage) en uit uw [gebruiks gegevens](billing-understand-reserved-instance-usage-ea.md#common-cost-and-usage-tasks) als u een Enter prise Agreement-klant bent.

Als u ziet dat het gebruik van de gereserveerde reserve ring van uw resource groep laag is, kunt u de reserverings Scope bijwerken naar één abonnement of deze delen in de facturerings context. U kunt ook de reserve ring splitsen en de resulterende reserve ringen Toep assen op verschillende resource groepen.

### <a name="other-considerations"></a>Andere overwegingen

Als u geen overeenkomende resources in een resource groep hebt, wordt de reserve ring onderbezet. De reserve ring is niet automatisch van toepassing op een andere resource groep of een ander abonnement waarvoor een laag gebruik is gemaakt.

Een reserverings bereik wordt niet automatisch bijgewerkt als u de resource groep van het ene naar het andere abonnement verplaatst. Het bereik wordt niet bijgewerkt als u de resource groep verwijdert. U moet [de reserve ring opnieuw bereiken](billing-manage-reserved-vm-instance.md#change-the-reservation-scope). Anders wordt de reserve ring onderbezet.

## <a name="discounted-subscription-and-offer-types"></a>Abonnements-en aanbiedings typen met korting

Reserveringskortingen zijn van toepassing op de volgende in aanmerking komende typen abonnementen en aanbiedingen.

- Enterprise Agreement (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P)
- Afzonderlijke abonnementen met tarieven voor betalen naar gebruik (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P)
- CSP-abonnementen

Voor resources die voor een abonnement met andere typen aanbiedingen worden uitgevoerd, wordt geen reserveringskorting verkregen.

## <a name="how-is-a-reservation-billed"></a>Hoe wordt een reserve ring gefactureerd?

De reserve ring wordt in rekening gebracht op de betalings wijze die aan het abonnement is gekoppeld. Als u een Enter prise-abonnement hebt, worden de kosten voor de reserve ring afgetrokken van uw monetaire toezeggings saldo. Als uw monetaire toezeggings saldo niet de kosten van de reserve ring dekt, factureert u de overschrijding. Als u een abonnement hebt van een afzonderlijke abonnement met betalen per gebruik-tarieven, wordt de credit card die u op uw account hebt, direct in rekening gebracht voor aankopen vooraf. Maandelijkse betalingen worden weer gegeven op uw factuur en de kosten voor uw credit card worden maandelijks in rekening gebracht. Wanneer u per factuur factureert, ziet u de kosten op uw volgende factuur.

## <a name="how-reservation-discount-is-applied"></a>De manier waarop de reserverings korting wordt toegepast

De reserverings korting is van toepassing op het resource gebruik dat overeenkomt met de kenmerken die u selecteert wanneer u de reserve ring koopt. Kenmerken bevatten het bereik waar de overeenkomende Vm's, SQL-data bases, Azure Cosmos DB of andere resources worden uitgevoerd. Als u bijvoorbeeld een reserverings korting wilt voor vier Standard D2-virtuele machines in de regio vs-West, selecteert u het abonnement waarin de Vm's worden uitgevoerd.

Een reserverings korting is '*use-it-or-kwijtraakt-* '. Als u geen overeenkomende resources voor elk uur hebt, verliest u een reserverings hoeveelheid voor dat uur. U kunt niet-gebruikte gereserveerde uren niet door sturen.

Wanneer u een resource afsluit, wordt de reserverings korting automatisch toegepast op een andere overeenkomende resource in het opgegeven bereik. Als er geen overeenkomende resources worden gevonden in het opgegeven bereik, gaan de gereserveerde uren *verloren*.

U kunt bijvoorbeeld later een resource maken en een overeenkomende reserve ring hebben die is ondergebruikt. De reserverings korting is automatisch van toepassing op de nieuwe overeenkomende resource.

Als de virtuele machines worden uitgevoerd in verschillende abonnementen binnen uw registratie/account, selecteert u het bereik als gedeeld. Met een gedeeld bereik kan de reserverings korting worden toegepast op meerdere abonnementen. U kunt het bereik wijzigen nadat u een reserve ring hebt gekocht. Zie [Manage Azure Reservations](billing-manage-reserved-vm-instance.md)voor meer informatie.

Een reserverings korting geldt alleen voor resources die zijn gekoppeld aan Enter prise, CSP of abonnementen met betalen naar gebruik-tarieven. Voor resources die voor een abonnement met andere typen aanbiedingen worden uitgevoerd, wordt geen reserveringskorting verkregen.

## <a name="when-the-reservation-term-expires"></a>Wanneer de reserverings termijn verloopt

Aan het einde van de reserverings termijn verloopt de facturerings korting. De virtuele machine, SQL database, Azure Cosmos DB of andere resource wordt gefactureerd op basis van de betalen naar gebruik-prijs. Azure Reservations wordt niet automatisch vernieuwd. Als u de factuur korting wilt blijven ontvangen, moet u een nieuwe reserve ring kopen voor in aanmerking komende Services en software.

## <a name="discount-applies-to-different-sizes"></a>Korting is van toepassing op verschillende grootten

Wanneer u een reserve ring koopt, kan de korting van toepassing zijn op andere instanties met kenmerken die zich binnen dezelfde grootte groep bevinden. Deze functie wordt de flexibiliteit van de instantie grootte genoemd. De flexibiliteit van de kortings dekking is afhankelijk van het type reserve ring en de kenmerken die u kiest wanneer u de reserve ring koopt.

Service plannen:

- Gereserveerde VM-instanties: Wanneer u de reserve ring koopt en **geoptimaliseerd voor de flexibiliteit van de instantie grootte**selecteert, is de kortings dekking afhankelijk van de grootte van de virtuele machine die u selecteert. De reserve ring kan van toepassing zijn op de grootte van virtuele machines (Vm's) in dezelfde grootte reeks groep. Zie [flexibiliteit van virtuele machines met gereserveerde VM-instanties](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)voor meer informatie.
- Gereserveerde capaciteit SQL Database: De kortings dekking is afhankelijk van de prestatie tier die u kiest. Zie [begrijpen hoe een Azure-reserverings korting wordt toegepast](billing-understand-reservation-charges.md)voor meer informatie.
- Gereserveerde capaciteit Azure Cosmos DB: De kortings dekking is afhankelijk van de ingerichte door voer. Zie [begrijpen hoe een Azure Cosmos DB reserverings korting wordt toegepast](billing-understand-cosmosdb-reservation-charges.md)voor meer informatie.

## <a name="reservation-notifications"></a>Reserverings meldingen

Afhankelijk van hoe u betaalt voor uw Azure-abonnement, sturen we de reserverings meldingen naar de volgende gebruikers in uw organisatie. Er worden meldingen verzonden voor verschillende gebeurtenissen, waaronder:

- Aanschaffen
- Verval datum van de geplande reserve ring
- Expiry
- Verlenging
- Heffing
- Bereik wijziging

Voor klanten met EA-abonnementen:
- Er wordt een melding over een aankoop verzonden naar de koper en de contact personen van de EA-melding.
- Andere meldingen over levens duur van de reserve ring worden alleen verzonden naar de contact personen van EA-meldingen.
- Gebruikers die zijn toegevoegd aan een reserve ring met de machtiging RBAC (IAM), ontvangen geen e-mail meldingen.

Voor klanten met afzonderlijke abonnementen:
- De inkoper ontvangt een melding over een inkoop.
- Op het moment van aankoop ontvangt de eigenaar van het abonnements account een inkoop melding.
- De eigenaar van het account ontvangt alle andere meldingen.


## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, kunt u [een ondersteunings aanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over Azure Reservations met de volgende artikelen:
    - [Azure-reserveringen beheren](billing-manage-reserved-vm-instance.md)
    - [Meer informatie over reserverings gebruik voor uw abonnement met betalen per gebruik-tarieven](billing-understand-reserved-instance-usage.md)
    - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](billing-understand-reserved-instance-usage-ea.md)
    - [Windows-software kosten niet inbegrepen bij reserve ringen](billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations in het Partner Center-programma van de Cloud Solution Provider (CSP)](/partner-center/azure-reservations)

- Meer informatie over reserve ringen voor service plannen:
    - [Virtual Machines met Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB resources met Azure Cosmos DB gereserveerde capaciteit](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [SQL database Compute-resources met Azure SQL database gereserveerde capaciteit](../sql-database/sql-database-reserved-capacity.md) Meer informatie over reserve ringen voor software plannen:
    - [Red Hat-Software plannen van Azure Reservations](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [SUSE-software plannen van Azure Reservations](../virtual-machines/linux/prepay-suse-software-charges.md)
