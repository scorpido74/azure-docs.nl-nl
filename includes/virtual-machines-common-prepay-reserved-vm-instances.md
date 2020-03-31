---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 03/27/2020
ms.openlocfilehash: d41affc55134ad34c325c12ab4a14f4013c58f9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371651"
---
Wanneer u zich verbindt met een gereserveerde VM-instantie met Azure, u geld besparen. De reserveringskorting wordt automatisch toegepast op het aantal draaiende virtuele machines dat overeenkomt met het reserveringsbereik en de kenmerken. U hoeft geen reservering toe te wijzen aan een virtuele machine om de kortingen te krijgen. Een gereserveerde instantieaankoop dekt alleen het rekengedeelte van uw VM-gebruik. Voor Windows VM's wordt de gebruiksmeter opgesplitst in twee afzonderlijke meters. Er is een compute meter, die hetzelfde is als de Linux-meter, en een Windows IP-meter. De kosten die u ziet wanneer u de aankoop doet, zijn alleen voor de rekenkosten. Kosten zijn exclusief Windows-softwarekosten. Zie [Softwarekosten die niet zijn opgenomen bij azure reserved VM-exemplaren](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)voor meer informatie over softwarekosten.

## <a name="determine-the-right-vm-size-before-you-buy"></a>De juiste grootte van de virtuele machine bepalen voordat u overgaat tot aankoop

Voordat u een reservering koopt, moet u de grootte van de VM bepalen die u nodig hebt. Met de volgende secties u de juiste VM-grootte bepalen.

### <a name="use-reservation-recommendations"></a>Reserveringsaanbevelingen gebruiken

U reserveringsaanbevelingen gebruiken om te bepalen welke reserveringen u moet kopen.

- Aankoopaanbevelingen en aanbevolen hoeveelheid worden weergegeven wanneer u een vm-gereserveerde instantie in de Azure-portal aanschaft.
- Azure Advisor biedt aankoopaanbevelingen voor afzonderlijke abonnementen.  
- U de API's gebruiken om aankoopaanbevelingen te krijgen voor zowel gedeelde scope als één abonnementsbereik. Zie [API's voor aankoopaanbeveling voor voor gereserveerde instantie voor zakelijke klanten](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)voor meer informatie.
- Voor Klanten van Enterprise Agreement (EA) en Microsoft Customer Agreement (MCA) zijn aankoopaanbevelingen voor gedeelde en afzonderlijke abonnementsscopes beschikbaar met het [Azure Consumption Insights Power BI-inhoudspakket.](/power-bi/service-connect-to-azure-consumption-insights)

### <a name="services-that-get-vm-reservation-discounts"></a>Services waarop VM-reserveringskortingen van toepassing zijn

Uw VM-reserveringen zijn toepasbaar op het gebruik van VM’s dat vanuit meerdere services wordt verzonden – niet alleen op uw VM-implementaties. Resources die reserveringskortingen krijgen, veranderen afhankelijk van de instelling voor de flexibiliteit van de instantiegrootte.

#### <a name="instance-size-flexibility-setting"></a>Instelling voor de flexibiliteit van de instantiegrootte

De instelling voor de flexibiliteit van de instantiegrootte bepaalt welke services de kortingen voor de gereserveerde instanties ontvangen.

Of de instelling nu aan of uit staat, reserveringskortingen worden automatisch toegepast op elk overeenkomend VM-gebruik wanneer de *ConsumedService*`Microsoft.Compute` is. Controleer daarom uw gebruiksgegevens voor de *ConsumedService*-waarde. Voorbeelden zijn:

- Virtuele machines
- Virtuele-machineschaalsets
- Containerservice
- Azure Batch-implementaties (in de modus gebruikersabonnementen)
- Azure Kubernetes Service (AKS)
- Service Fabric

Als de instelling aan staat, worden de reserveringskortingen automatisch toegepast op elk overeenkomstig VM-gebruik wanneer het bij de *ConsumedService* om een van de volgende varianten gaat:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Controleer de *ConsumedService*-waarde in uw gebruiksgegevens om te bepalen of het gebruik in aanmerking komt voor reserveringskortingen.

Zie [Flexibiliteit van de VM-grootte met gereserveerde VM-instanties](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md) voor meer informatie over flexibiliteit met instantiegrootten.

### <a name="analyze-your-usage-information"></a>Uw gebruiksgegevens analyseren

Analyseer uw gebruiksgegevens om te bepalen welke reserveringen u moet kopen. Gebruiksgegevens zijn beschikbaar in het gebruiksbestand en API's. Gebruik ze samen om te bepalen welke reservering te kopen. Controleer op VM-exemplaren die dagelijks een hoog gebruik hebben om de hoeveelheid te kopen reserveringen te bepalen. Vermijd `Meter` de subcategorie en `Product` velden in gebruiksgegevens. Ze maken geen onderscheid tussen VM-formaten die gebruik maken van premium opslag. Als u deze velden gebruikt om de VM-grootte te bepalen voor reserveringsaankopen, u de verkeerde maat kopen. Dan krijgt u niet de reserveringskorting die u verwacht. In plaats daarvan `AdditionalInfo` verwijst u naar het veld in uw gebruiksbestand of gebruiks-API om de juiste VM-grootte te bepalen.

Uw gebruiksbestand toont uw kosten op factuurperiode en dagelijks gebruik. Zie [Uw Azure-gebruik en kosten weergeven en downloaden](../articles/cost-management-billing/understand/download-azure-daily-usage.md)voor informatie over het downloaden van uw gebruiksbestand. Vervolgens u met behulp van de informatie over het gebruiksbestand [bepalen welke reservering u wilt kopen.](../articles/cost-management-billing/reservations/determine-reservation-purchase.md)

### <a name="purchase-restriction-considerations"></a>Overwegingen voor aankoopbeperking

Gereserveerde VM-exemplaren zijn beschikbaar voor de meeste VM-formaten, met enkele uitzonderingen. Reserveringskortingen zijn niet van toepassing op de volgende VM's:

- **VM-serie** - A-serie, Av2-serie of G-serie.

- **Preview of Promo VM's** - Elke VM-serie of grootte die in preview of maakt gebruik van promotionele meter.

- **Clouds** - Reserveringen zijn niet beschikbaar voor aankoop in Duitsland of China regio's.

- **Onvoldoende quotum** - Een reservering die is beperkt tot één abonnement, moet vCPU-quotum beschikbaar hebben in het abonnement voor de nieuwe RI. Als het doelabonnement bijvoorbeeld een quotumlimiet van 10 vCPU's voor D-serie heeft, u geen reservering kopen voor 11 Standard_D1 gevallen. De quotacontrole voor reserveringen omvat de VM's die al in het abonnement zijn geïmplementeerd. Als het abonnement bijvoorbeeld een quotum van 10 vCPU's voor D-series heeft en twee standard_D1 exemplaren heeft geïmplementeerd, u een reservering kopen voor 10 standard_D1 gevallen in dit abonnement. U [offerteverhogingsaanvragen maken](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md) om dit probleem op te lossen.

- **Capaciteitsbeperkingen** - In zeldzame omstandigheden beperkt Azure de aankoop van nieuwe reserveringen voor subsets van VM-formaten vanwege de lage capaciteit in een regio.

## <a name="buy-a-reserved-vm-instance"></a>Een gereserveerde VM-instantie kopen

U een gereserveerde VM-instantie kopen in de [Azure-portal.](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D) Betaal [vooraf of per maand](../articles/cost-management-billing/reservations/monthly-payments-reservations.md) voor de reservering.
Deze vereisten zijn van toepassing op het kopen van een gereserveerde VM-instantie:

- Je moet in een eigenaarrol zitten voor ten minste één EA-abonnement of een abonnement met een betalen per gebruik-tarief.
- Voor EA-abonnementen moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA-portal.](https://ea.azure.com/) Of, als deze instelling is uitgeschakeld, moet u een EA-beheerder van het abonnement zijn.
- Voor het CSP-programma (Cloud Solution Provider) kunnen alleen de beheerders of verkoopagenten reserveringen kopen.

Een exemplaar kopen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Alle services** > **Reserveringen**.
1. Selecteer **Toevoegen** om een nieuwe reservering te kopen en klik op **Virtuele machine**.
1. Vul de verplichte velden in. Actieve VM-instanties die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de reserveringskorting. Het werkelijke aantal VM-exemplaren dat de korting krijgt, is afhankelijk van het bereik en de geselecteerde hoeveelheid.

Als u een EA-overeenkomst hebt, u de **optie Meer toevoegen** gebruiken om snel extra exemplaren toe te voegen. De optie is niet beschikbaar voor andere abonnementstypen.


| Veld      | Beschrijving|
|------------|--------------|
|Abonnement|Het abonnement werd gebruikt om te betalen voor de reservering. De betalingsmethode op het abonnement wordt in rekening gebracht de kosten voor de reservering. Het abonnementstype moet een ondernemingsovereenkomst zijn (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P) of Microsoft Customer Agreement of een individueel abonnement met pay-as-you-go-tarieven (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P). De kosten worden in mindering gebracht op het monetaire vastleggingssaldo, indien beschikbaar, of in rekening gebracht als overschrijding. Voor een abonnement met betalen per gebruik worden de kosten in rekening gebracht op de creditcard- of factuurbetalingsmethode van het abonnement.|    
|Bereik       |Het bereik van de reservering kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u: <ul><li>**Bereik van één resourcegroep**: de reserveringskorting wordt alleen toegepast op de overeenkomende resources in de geselecteerde resourcegroep.</li><li>**Bereik van één abonnement**: de reserveringskorting wordt toegepast op de overeenkomende resources in het geselecteerde abonnement.</li><li>**Gedeeld bereik**: de reserveringskorting wordt toegepast op overeenkomende resources binnen in aanmerking komende abonnementen die zich in de factureringscontext bevinden. Voor EA-klanten is de factureringscontext de inschrijving. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.</li></ul>|
|Regio    |De Azure-regio die onder de reservering valt.|    
|VM-grootte     |De grootte van de VM-exemplaren.|
|Optimaliseren voor     |De flexibiliteit van vm-instantiegrootte is standaard geselecteerd. Klik **op Geavanceerde instellingen** om de flexibiliteitswaarde van de instantiegrootte te wijzigen om de reserveringskorting toe te passen op andere VM's in dezelfde groep [vm-grootte](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). Met de capaciteitsprioriteit wordt prioriteit toegekend aan de datacentercapaciteit voor uw implementaties. Het biedt extra vertrouwen in uw vermogen om de VM-exemplaren te starten wanneer u ze nodig hebt. Capaciteitsprioriteit is alleen beschikbaar wanneer de reserveringsscope één abonnement is. |
|Termijn        |Een jaar of drie jaar.|
|Aantal    |Het aantal exemplaren dat binnen de reservering wordt gekocht. Het aantal keer dat VM-exemplaren worden uitgevoerd, kan de factureringskorting krijgen. Als u bijvoorbeeld 10 Standard_D2 VM's uitvoert in de Oost-VS, geeft u het aantal op als 10 om het voordeel voor alle draaiende VM's te maximaliseren. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Gebruiksgegevens en reserveringsgebruik

Uw gebruiksgegevens kosten niets voor het deel waarvoor u een reserveringskorting verkrijgt. U zien welke VM-instantie de reserveringskorting voor elke reservering heeft ontvangen.

Zie [Azure-reserveringsgebruik voor uw Enterprise-inschrijving begrijpen](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) als u een EA-klant bent voor meer informatie over hoe reserveringskortingen worden weergegeven in gebruiksgegevens. Zie [Azure-reserveringsgebruik voor uw abonnement voor betalen per gebruik begrijpen](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)als u een individueel abonnement hebt.

## <a name="change-a-reservation-after-purchase"></a>Een reservering wijzigen na aankoop

Na aankoop kunt u de volgende typen wijzigingen aanbrengen in een reservering:

- Het bereik van de reservering bijwerken
- Flexibiliteit bij instantiegrootte (indien van toepassing)
- Eigendom

U een reservering ook splitsen in kleinere segmenten en al gesplitste reserveringen samenvoegen. Geen van de wijzigingen veroorzaakt een nieuwe commerciële transactie of wijzigt de einddatum van de reservering.

U de volgende typen wijzigingen na aankoop niet rechtstreeks aanbrengen:

- De regio van een bestaande reservering
- SKU
- Aantal
- Duur

U echter wel een reservering *wisselen* als u wijzigingen wilt aanbrengen.

## <a name="cancel-exchange-or-refund-reservations"></a>Annulering, omwisseling of terugbetaling van reserveringen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../articles/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [maakt u een ondersteuningsverzoek.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-reserveringen beheren](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md) voor meer informatie over het beheren van een reservering.
- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
    - [Wat zijn Azure-reserveringen?](../articles/cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Reserveringen beheren in Azure](../articles/cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Begrijpen hoe de reserveringskorting wordt toegepast](../articles/cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../articles/cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../articles/cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Windows-softwarekosten niet inbegrepen bij reserveringen](../articles/cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Azure-reserveringen in het CSP-programma (Cloud Solution Provider) van het Partnercentrum](https://docs.microsoft.com/partner-center/azure-reservations)
