---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08/29/2019
ms.openlocfilehash: 99263b7c7efee54381c9a7f624429b343dba49d0
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806097"
---
# <a name="save-costs-with-azure-reserved-vm-instances"></a>Bespaar kosten met Azure Reserved VM Instances

Wanneer u een gereserveerde VM-instantie voor Azure doorvoert, kunt u geld besparen. De reserverings korting wordt automatisch toegepast op het aantal actieve virtuele machines die overeenkomen met het reserverings bereik en de kenmerken. U hoeft geen reserve ring aan een virtuele machine toe te wijzen om de kortingen te krijgen. Voor een gereserveerde instantie aankoop geldt alleen het reken onderdeel van uw VM-gebruik. Voor virtuele Windows-machines wordt de gebruiks meter gesplitst in twee afzonderlijke meters. Er is een compute-meter, die gelijk is aan de Linux meter en een Windows IP-meter. De kosten die u ziet wanneer u de aankoop uitvoert, zijn alleen voor de reken kosten. Kosten zijn niet van toepassing op Windows-software. Zie [software kosten die niet zijn opgenomen in azure reserved VM instances](../articles/billing/billing-reserved-instance-windows-software-costs.md)voor meer informatie over software kosten.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Bepaal de juiste VM-grootte voordat u koopt

Voordat u een reserve ring koopt, moet u de grootte van de virtuele machine bepalen die u nodig hebt. De volgende secties helpen u bij het bepalen van de juiste VM-grootte.

### <a name="use-reservation-recommendations"></a>Aanbevelingen voor reserverings vereisten gebruiken

U kunt reserverings aanbevelingen gebruiken om te helpen bij het bepalen van de reserve ringen die u moet aanschaffen.

- Aanbevelingen voor aankopen en aanbevolen hoeveelheid worden weer gegeven wanneer u een gereserveerde VM-instantie in de Azure Portal koopt.
- Azure Advisor biedt inkoop aanbevelingen voor afzonderlijke abonnementen.  
- U kunt de Api's gebruiken voor het verkrijgen van inkoop aanbevelingen voor zowel het gedeelde bereik als het bereik van één abonnement. Zie voor meer informatie [gereserveerde instanties aankoop aanbeveling api's voor zakelijke klanten](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Voor Enterprise Agreement (EA)-klanten zijn er aanbevelingen voor het delen van gedeelde en enkelvoudige abonnementen beschikbaar met de [Azure Consumption Insights Power bi inhouds pakket](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Services die VM-reserverings kortingen ophalen

Uw VM-reserve ringen kunnen van toepassing zijn op het VM-gebruik dat wordt verzonden vanuit meerdere services, niet alleen voor uw VM-implementaties. Resources die reserverings kortingen ophalen, zijn afhankelijk van de instelling voor de flexibiliteit van de instantie grootte.

#### <a name="instance-size-flexibility-setting"></a>Instelling voor de flexibiliteit van instantie grootte

De instelling voor de flexibiliteit van de instantie grootte bepaalt welke services de gereserveerde exemplaar kortingen ophalen.

Of de instelling is ingeschakeld of uitgeschakeld, de reserverings kortingen zijn automatisch van toepassing op alle overeenkomende VM `Microsoft.Compute`-gebruik wanneer de ConsumedService is. Controleer daarom de gebruiks gegevens voor de waarde *ConsumedService* . Voorbeelden zijn:

- Virtuele machines
- Virtual Machine Scale Sets
- Containerservice
- Azure Batch implementaties (in de modus gebruikers abonnementen)
- Azure Kubernetes Service (AKS)
- Service Fabric

Als de instelling is ingeschakeld, worden de reserverings kortingen automatisch toegepast op het overeenkomende VM-gebruik wanneer de *ConsumedService* een van de volgende items is:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Controleer de waarde *ConsumedService* in uw gebruiks gegevens om te bepalen of het gebruik in aanmerking komt voor reserverings kortingen.

Zie flexibiliteit van de [grootte van virtuele machines met gereserveerde VM-instanties](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)voor meer informatie over de flexibiliteit van de grootte van het exemplaar.

### <a name="analyze-your-usage-information"></a>Uw gebruiks gegevens analyseren
Analyseer uw gebruiks gegevens om te helpen bepalen welke reserve ringen u moet aanschaffen.

Gebruiks gegevens zijn beschikbaar in het gebruiks bestand en Api's. Gebruik ze samen om te bepalen welke reserve ring moet worden gekocht. Controleren op VM-exemplaren met hoog gebruik op dagelijkse basis om het aantal te kopen reserve ringen te bepalen.

Vermijd de `Meter` subcategorie en `Product` velden in gebruiks gegevens. Ze maken geen onderscheid tussen VM-grootten die Premium-opslag gebruiken. Als u deze velden gebruikt om de grootte van de virtuele machine te bepalen voor de reserve ring, kunt u de verkeerde grootte aanschaffen. Vervolgens krijgt u niet de verwachte reserverings korting. In plaats daarvan raadpleegt `AdditionalInfo` u het veld in uw gebruiks bestand of gebruiks-API om de juiste VM-grootte te bepalen.

### <a name="purchase-restriction-considerations"></a>Overwegingen voor de aankoop beperking

Gereserveerde VM-instanties zijn beschikbaar voor de meeste VM-grootten met enkele uitzonde ringen. Er zijn geen reserverings kortingen van toepassing op de volgende Vm's:

- **VM-reeks** -A-serie, Av2-serie of G-serie.

- **Preview-of promotie vm's** : elke VM-serie of-grootte die in preview is of een promotie meter gebruikt.

- **Clouds** -reserve ringen zijn niet beschikbaar voor aankopen in de regio's Duitsland en China.

- **Onvoldoende quotum** : voor een reserve ring die is toegewezen aan één abonnement, moet vCPU quotum beschikbaar zijn in het abonnement voor de nieuwe ri. Als het doel abonnement bijvoorbeeld een quotum limiet heeft van 10 Vcpu's voor D-serie, kunt u geen reserve ring voor 11 Standard_D1-instanties kopen. De quotum controle voor reserve ringen omvat de Vm's die al zijn geïmplementeerd in het abonnement. Als het abonnement bijvoorbeeld een quotum heeft van 10 Vcpu's voor de D-serie en twee standard_D1-instanties zijn geïmplementeerd, kunt u een reserve ring kopen voor 10 standard_D1-instanties in dit abonnement. U kunt een [offerte verhogings aanvraag maken](../articles/azure-supportability/resource-manager-core-quotas-request.md) om dit probleem op te lossen.

- **Capaciteits beperkingen** : in zeldzame omstandigheden beperkt Azure de aanschaf van nieuwe reserve ringen voor SUBSET van VM-grootten, vanwege een geringe capaciteit in een regio.

## <a name="buy-a-reserved-vm-instance"></a>Een gereserveerde VM-instantie kopen

U kunt een gereserveerde VM-instantie kopen in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Betaal voor de reserve ring [vóór of met maandelijkse betalingen](../articles/billing/billing-monthly-payments-reservations.md).

Deze vereisten zijn van toepassing op het kopen van een gereserveerde VM-instantie:

- U moet een rol van eigenaar zijn voor ten minste één EA-abonnement of een abonnement met een betalen naar gebruik-tarief.
- Voor EA-abonnementen moet de optie **gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA-Portal](https://ea.azure.com/). Als deze instelling is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement.
- Voor het programma Cloud Solution Provider (CSP) kunnen alleen de beheerders of verkoop medewerkers reserve ringen kopen.

Een exemplaar kopen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **alle services** > -**reserve ringen**.
1. Selecteer **toevoegen** om een nieuwe reserve ring te kopen en klik vervolgens op **virtuele machine**.
1. Voer de vereiste velden in. Actieve VM-exemplaren die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de reserverings korting. Het werkelijke aantal VM-exemplaren dat de korting krijgt, is afhankelijk van het bereik en de geselecteerde hoeveelheid.

| Veld      | Description|
|------------|--------------|
|Subscription|Het abonnement dat wordt gebruikt om te betalen voor de reserve ring. De betalings wijze voor het abonnement wordt in rekening gebracht voor de kosten voor de reserve ring. Het abonnements type moet een Enter prise Agreement zijn (nummers van aanbiedingen: MS-AZR-0017P of MS-AZR-0148P) of een afzonderlijk abonnement met betalen naar gebruik-tarieven (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het toezeggingsbedrag of ze worden in rekening gebracht als overschrijding. Voor een abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card-of factuur betalings methode voor het abonnement.|    
|Scope       |Het bereik van de reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert: <ul><li>**Bereik van één resource groep** : past de reserverings korting alleen toe op de overeenkomende resources in de geselecteerde resource groep.</li><li>**Bereik van één abonnement** : past de reserverings korting toe op de overeenkomende resources in het geselecteerde abonnement.</li><li>**Gedeeld bereik** : past de reserverings korting toe op overeenkomende resources in in aanmerking komende abonnementen in de facturerings context. Voor EA-klanten is de facturerings context de inschrijving. Voor afzonderlijke abonnementen met betalen per gebruik-tarieven geldt het facturerings bereik uit alle in aanmerking komende abonnementen die zijn gemaakt door de account beheerder.</li></ul>|
|Regio    |De Azure-regio die wordt gedekt door de reserve ring.|    
|VM-grootte     |De grootte van de VM-exemplaren.|
|Optimaliseren voor     |De flexibiliteit van VM-instantie grootte is standaard geselecteerd. Klik op **Geavanceerde instellingen** om de waarde voor de flexibiliteit van de instantie grootte te wijzigen om de reserverings korting toe te passen op andere virtuele machines in dezelfde [VM-grootte groep](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). Met capaciteits prioriteit wordt de capaciteit van het Data Center voor uw implementaties prioriteit. Het biedt extra vertrouwen in uw vermogen om de VM-exemplaren te starten wanneer u ze nodig hebt. Capaciteits prioriteit is alleen beschikbaar wanneer het reserverings bereik één abonnement is. |
|Begrip        |Eén jaar of drie jaar.|
|Hoeveelheid    |Het aantal exemplaren dat wordt aangeschaft binnen de reserve ring. De hoeveelheid is het aantal actieve VM-exemplaren waarmee de factuur korting kan worden verkregen. Als u bijvoorbeeld 10 Standard_D2-Vm's uitvoert in het VS-Oost, geeft u hoeveelheid op als 10 om het voor deel voor alle actieve Vm's te maximaliseren. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Gebruiks gegevens en reserverings gebruik

Uw gebruiks gegevens hebben een goede prijs van nul voor het gebruik dat een reserverings korting krijgt. U kunt zien welke VM-instantie de reserverings korting voor elke reserve ring heeft ontvangen.

Zie het [gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](../articles/billing/billing-understand-reserved-instance-usage-ea.md) als u een EA-klant bent voor meer informatie over hoe reserverings kortingen worden weer gegeven in gebruiks gegevens. Zie [het gebruik van Azure-reserve ringen voor uw abonnement op basis van betalen per gebruik voor meer informatie](../articles/billing/billing-understand-reserved-instance-usage.md)over een afzonderlijk abonnement.

## <a name="change-a-reservation-after-purchase"></a>Een reserve ring wijzigen na aankoop

U kunt de volgende typen wijzigingen aanbrengen in een reserve ring na de aankoop:

- Reserverings bereik bijwerken
- Flexibiliteit van de instantie grootte (indien van toepassing)
- Beheer

U kunt ook een reserve ring in kleinere segmenten splitsen en al gesplitste reserve ringen samen voegen. Geen van de wijzigingen veroorzaakt een nieuwe commerciële trans actie of wijzigt de eind datum van de reserve ring.

U kunt de volgende typen wijzigingen na de aankoop niet rechtstreeks aanbrengen:

- Een bestaande reserverings regio
- SKU
- Hoeveelheid
- Duration

U kunt echter wel een reserve ring *uitwisselen* als u wijzigingen wilt aanbrengen.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserve ringen annuleren, omruilen of terugbetalen

U kunt reserve ringen annuleren, vervangen of terugbetalen met bepaalde beperkingen. Zie [self-service-uitwisseling en terugbetalingen voor Azure Reservations](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md)voor meer informatie.

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [Maak een ondersteuningsaanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Reservations beheren](../articles/billing/billing-manage-reserved-vm-instance.md)voor meer informatie over het beheren van een reserve ring.
- Raadpleeg de volgende artikelen voor meer informatie over Azure Reservations:
    - [Wat zijn Azure Reservations?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Reserve ringen in azure beheren](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Begrijpen hoe de reserverings korting wordt toegepast](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Meer informatie over reserverings gebruik voor een abonnement met betalen per gebruik-tarieven](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Windows-software kosten niet inbegrepen bij reserve ringen](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Azure Reservations in het Partner Center-programma van de Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
