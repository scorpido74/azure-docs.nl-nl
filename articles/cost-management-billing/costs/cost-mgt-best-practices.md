---
title: Optimaliseer uw investeringen in de cloud met Azure Cost Management | Microsoft Docs
description: Dit artikel helpt u optimaal van uw investeringen in de cloud, uw kosten verlagen en evalueren waar uw geld wordt besteed.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 47a53136a8f299e5c8654d122d8e319be4ae29db
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988381"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Over het optimaliseren van uw investeringen in de cloud met Azure Cost Management

Kostenbeheer van Azure biedt u de hulpmiddelen om te plannen, analyseren en reduceren uw uitgaven voor uw cloud-investering te maximaliseren. Dit document hebt u een methodisch benadering van kostenbeheer en de hulpprogramma's die beschikbaar zijn voor u worden gemarkeerd als u problemen van de kosten van uw organisatie te pakken. Azure maakt het eenvoudig om te bouwen en implementeren van oplossingen voor cloudopslag. Het is echter belangrijk dat deze oplossingen zijn geoptimaliseerd voor het minimaliseren van de kosten voor uw organisatie. De principes die worden beschreven in dit document en met behulp van dat onze hulpprogramma's voor helpt om te controleren of uw organisatie te volgen is voorbereid op succes.

## <a name="methodology"></a>Methodologie

Kostenbeheer is een probleem met organisatie- en moet een actieve praktijk die begint voordat u alleen geld aan cloud-bronnen geven. Als u wilt met succes implementeren kostenbeheer en kosten optimaliseren, moet uw organisatie:

- Worden voorbereid met de juiste hulpprogramma's voor geslaagd
- Verantwoordelijk voor de kosten worden
- Onderneem gepaste actie om te optimaliseren voor de uitgavelimiet

Drie belangrijkste groepen in de onderstaande moeten zijn uitgelijnd voordat in uw organisatie om te controleren of zijn de kosten te beheren.

- **Financiën** -mensen die verantwoordelijk zijn voor het goedkeuren van aanvragen van budget in de hele organisatie op basis van de cloud prognoses uitgaven. Ze de bijbehorende factuur te betalen en kosten toewijzen aan verschillende teams station aansprakelijkheid.
- **Managers** - zakelijke besluitvormers in een organisatie die nodig hebt om te begrijpen van de cloud voor de uitgavelimiet voor het vinden van de beste resultaten uitgaven.
- **App-teams** - Engineers beheren op dagelijks, ontwikkeling van services om te voldoen aan de behoeften van de organisatie. Deze teams moeten de flexibiliteit in hun gedefinieerde budgetten bezorgen in de meeste waarde.

### <a name="key-principles"></a>Basisprincipes

Gebruik de onderstaande plaatsen van uw organisatie voor succes in de cloud kostenbeheer principes.

#### <a name="planning"></a>Planning

Uitgebreide, vooraf plannen, kunt u op maat maken gebruik van de cloud voor uw specifieke zakelijke vereisten. Stel uzelf de:

- Wat is het zakelijke probleem ben ik oplossen?
- Welke gebruikspatronen ik verwachten van mijn resources?

Uw antwoorden krijgt u de aanbiedingen die geschikt voor u zijn selecteert. Ze bepalen en de infrastructuur moet worden gebruikt en hoe deze wordt gebruikt voor het maximaliseren van uw Azure-efficiëntie.

#### <a name="visibility"></a>Zichtbaarheid

Wanneer goed gestructureerd, met Cost Management kunt u mensen informeren over de kosten voor Azure zijn ze verantwoordelijk voor of voor de geld besteden. Azure heeft diensten die zijn ontworpen om u inzicht geven in *waar* wordt besteed aan uw geld. Profiteer van deze hulpprogramma's. Ze kunt u zoeken naar bronnen die onderbezette zijn, verwijdert u verspilling en geld te besparen kansen te maximaliseren.

#### <a name="accountability"></a>Aansprakelijkheid

Kenmerk kosten in uw organisatie om ervoor te zorgen dat gebruikers verantwoordelijk zijn verantwoordelijk voor de uitgaven van hun team. Om te begrijpen volledig aan Azure-uitgaven voor uw organisatie, moet u uw resources om te maximaliseren inzicht in de kosten attribution indelen. Goede organisatie helpt bij het beheren en kosten te verlagen en houd personen verantwoordelijk voor het efficiënt uitgaven in uw organisatie.

#### <a name="optimization"></a>Optimalisatie

Fungeren als u wilt verkleinen uw uitgaven. Haal alles uit op basis van de resultaten die zijn verzameld bij het plannen en vergroot de zichtbaarheid van de kosten. U kunt overwegen inkoop en licentieverlening optimalisaties, samen met wijzigingen in de infrastructuur-implementatie die verderop in dit document worden besproken.

#### <a name="iteration"></a>Herhaling

Iedereen in uw organisatie moet deelnemen aan het levenscyclusbeheer van kosten. Ze moeten blijven betrokken regelmatig kosten optimaliseren. Worden strenge over deze iteratief proces en geef deze als een belangrijk basisprincipe verantwoordelijk cloud governance in uw organisatie.

![Basisprincipes-diagram van zichtbaarheid, betere controleerbaarheid en optimalisatie](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Met de kosten in rekening met plannen

Voordat u cloudresources implementeert, evalueren de volgende items:

- De Azure-aanbieding die het beste aan uw behoeften
- De resources die u van plan bent te gebruiken
- Hoeveel ze mogelijk kosten

Azure biedt hulpprogramma's om u te helpen bij de evaluatie. De's bieden u een goed inzicht in de investering vereist voor het inschakelen van uw workloads. Vervolgens kunt u de aanbevolen configuratie voor uw situatie.

### <a name="azure-onboarding-options"></a>Opties voor onboarding van Azure

De eerste stap bij het maximaliseren van uw ervaring met Cost Management is om te onderzoeken en bepalen welke Azure-aanbieding voor u geschikt is. Denk na over hoe u van plan bent om Azure te gebruiken in de toekomst. Houd er ook rekening met hoe u wilt dat uw factureringsmodel dat is geconfigureerd. Wanneer u uw keuze maakt, houd rekening met de volgende vragen:

- Hoe lang plan ik het gebruik van Azure? Ben ik testen of plan ik om op de langere termijn infrastructuur te bouwen?
- Hoe weet ik wilt betalen voor Azure? Moet ik betaal vooruit voor een gereduceerde prijs of aan het einde van de maand ophalen gefactureerd?

Voor meer informatie over de verschillende opties, gaat u naar [Azure kopen](https://azure.microsoft.com/pricing/purchase-options/). Aantal van de meest voorkomende factureringsmodellen worden hieronder aangegeven.

#### <a name="freehttpsazuremicrosoftcomfree"></a>[Gratis](https://azure.microsoft.com/free/)

- 12 maanden populaire gratis services
- $200 aan tegoed om services 30 dagen te verkennen
- meer dan 25 services zijn altijd gratis

#### <a name="pay-as-you-gohttpsazuremicrosoftcomoffersms-azr-0003p"></a>[Betalen naar gebruik](https://azure.microsoft.com/offers/ms-azr-0003p)

- Geen minimumbedragen of toezeggingen
- Concurrerende prijzen
- Uitsluitend betalen voor wat u gebruikt
- Op elk gewenst moment annuleren

#### <a name="enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement"></a>[Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Opties voor het vooraf monetaire toezeggingen
- Toegang tot de beperkte Azure-prijzen

## <a name="estimate-the-cost-of-your-solution"></a>Maak een schatting van de kosten van uw oplossing

Voordat u een infrastructuur implementeert, evalueren hoeveel uw oplossing gaat kosten. De evaluatie helpt u bij het maken van een budget voor uw organisatie voor de werkbelasting, vooraf. Vervolgens kunt u een budget na verloop van tijd de geldigheid van uw eerste schatting van de benchmark-test uitvoeren. En u kunt vergelijken met de werkelijke kosten van uw geïmplementeerde oplossing.

### <a name="azure-pricing-calculator"></a>Azure-prijscalculator

De prijscalculator van Azure kunt u combineren en matchen verschillende combinaties van Azure-services om te zien van een schatting van de kosten. U kunt de oplossing met behulp van verschillende manieren in Azure implementeren - elk mogelijk van invloed op uw totale uitgaven. Na te denken over alle van de behoeften van de infrastructuur van uw cloudimplementatie vroeg helpt u zo efficiënt mogelijk gebruik van het hulpprogramma. U kunt een solide schatting van uw geschatte uitgaven in Azure.

Zie voor meer informatie de [prijscalculator van Azure](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate is een service die de huidige workloads van uw organisatie in on-premises datacenters. Dit biedt u inzicht in wat u mogelijk nodig hebt van een vervanging van Azure-oplossing. Eerst, analyseert Migrate uw on-premises computers om te bepalen of migratie haalbaar is. Klik, dit is de aanbevolen grootte van de virtuele machine in Azure om prestaties te maximaliseren. Ten slotte maakt het ook een geschatte kosten voor een oplossing op basis van Azure.

Zie voor meer informatie, [Azure Migrate](../../site-recovery/migrate-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Analyseer en beheer uw kosten

Blijf op de hoogte hoe de kosten van uw organisatie loop der tijd veranderen. Gebruik de volgende technieken om correct te begrijpen en beheren van uw uitgaven.

### <a name="organize-and-tag-your-resources"></a>Organiseren en taggen van uw resources

Organiseer uw resources met de kosten in rekening. Als u abonnementen en resourcegroepen hebt gemaakt, kunt u denken over de teams die verantwoordelijk voor de bijbehorende kosten zijn. Zorg ervoor dat uw organisatie uw rapportage blijft in gedachten. Abonnementen en resourcegroepen bieden goede buckets voor het organiseren en het kenmerk uitgaven in uw organisatie. -Tags bieden een goede manier om kenmerk kosten. U kunt tags gebruiken als een filter. En u ze kunt gebruiken om te groeperen op wanneer u gegevens analyseren en onderzoeken van kosten. Enterprise Agreement-klanten kunnen ook afdelingen maken en abonnementen onder deze plaatsen. Kosten op basis van organisatie in Azure wordt voorkomen dat de relevante personen in uw organisatie verantwoordelijk voor het verminderen van hun team uitgaven.

### <a name="use-cost-analysis"></a>Kostenanalyse gebruiken

Kostenanalyse kunt u voor het analyseren van uw organisatie gedetailleerde kosten door uw kosten met behulp van standaard broneigenschappen opdelen. Houd rekening met de volgende veelgestelde vragen als richtlijn voor uw analyse. Beantwoorden van deze vragen regelmatig, krijgt u meer op de hoogte blijven en meer kostenbewuste besluiten kunnen nemen.

- **De geschatte kosten voor de huidige maand** – hoeveel heb ik dus veel van deze maand gemaakt? Ik blijven onder mijn budget?
- **Afwijkingen onderzoeken** – routinematige controles om ervoor te zorgen dat kosten binnen een redelijke tal van normaal gebruik blijven uitvoeren. Wat zijn de trends? Zijn er uitbijters?
- **Factuur afstemming** -Is mijn laatste gefactureerde duurder dan de vorige maand? Hoe de bestedingslimiet gewoonten maand na maand wijzigen?
- **Interne terugstorting** - nu dat ik hoeveel ik ben wordt in rekening gebracht weten, hoe moeten deze kosten worden opgesplitst voor mijn organisatie?

Zie voor meer informatie, [analysis kosten](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Factureringsgegevens volgens een schema exporteren

Wilt u uw factureringsgegevens importeren in een extern systeem, zoals een dashboard of financiële systeem? Instellen van geautomatiseerde export naar Azure Storage en te voorkomen dat bestanden handmatig elke maand te downloaden. U kunt vervolgens gemakkelijk instellen automatische integratie met andere systemen aan uw factureringsgegevens gesynchroniseerd houden.

Zie voor meer informatie over het exporteren van factureringsgegevens [maken en beheren van de geëxporteerde gegevens](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Budgetten maken

Nadat u hebt geïdentificeerd en uw bestedingslimiet patronen geanalyseerd, is het belangrijk om te beginnen met limieten instellen voor uzelf en uw teams. Azure budgetten bieden u de mogelijkheid een kosten of de op gebruik gebaseerde budget met veel drempelwaarden en waarschuwingen in te stellen. Zorg ervoor dat u het bekijken van de budgetten die u regelmatig maakt om te zien van de voortgang van uw budget branden-omlaag en wijzigingen aanbrengen. Azure budgetten kunnen u een automation-trigger configureren als een bepaalde budgetdrempelwaarde is bereikt. U kunt bijvoorbeeld uw service afsluiten van virtuele machines te configureren. Of u uw infrastructuur kunt verplaatsen naar een andere prijscategorie in reactie op een trigger budget.

Zie voor meer informatie, [Azure budgetten](tutorial-acm-create-budgets.md).

Zie voor meer informatie over automatisering in op basis van budget [Budget op basis van Automation](../manage/cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Neem maatregelen om te optimaliseren
Gebruik de volgende manieren om te optimaliseren uitgaven.

### <a name="cut-out-waste"></a>Knip verspilling

Nadat u uw infrastructuur in Azure hebt geïmplementeerd, is het belangrijk ervoor te zorgen dat deze wordt gebruikt. De eenvoudigste manier om onmiddellijk met besparen te beginnen, is om uw resources te controleren en degene die niet worden gebruikt te verwijderen. Van daaruit, moet u bepalen als uw resources zo efficiënt mogelijk worden gebruikt.

#### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor is een service die, onder andere virtuele machines identificeert weinig gebruikte CPU of het netwerk gebruik vanuit het oogpunt van. Van daaruit kunt u bepalen of u afgesloten of grootte van de machine op basis van de geschatte kosten om door te gaan met het uitvoeren van de virtuele machines. Advisor biedt ook aanbevelingen voor de aankoop van gereserveerde instanties. De aanbevelingen zijn gebaseerd op de afgelopen 30 dagen van het gebruik van virtuele machines. Wanneer een of meer bewerkingen op, kunt de aanbevelingen u uw uitgaven te verminderen.

Zie voor meer informatie, [Azure Advisor](../../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>Grootte van uw virtuele machines goed

VM-grootte heeft een aanzienlijke invloed op de algemene Azure kosten. Het aantal virtuele machines nodig in Azure mogelijk niet overeen met wat u momenteel hebt geïmplementeerd in een on-premises datacenter. Zorg ervoor dat uw kiest u de juiste grootte voor de werkbelastingen die u van plan bent om uit te voeren.

Zie voor meer informatie, [Azure IaaS: juiste grootte en kosten](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/).

### <a name="use-purchase-discounts"></a>Gebruik aankoop kortingen

Azure heeft veel kortingen die uw organisatie benutten te om geld te besparen.

#### <a name="azure-reservations"></a>Azure-reserveringen

Azure reserveringen kunnen u Vooruitbetalen voor één jaar of drie jaar van de virtuele machine of SQL-Database compute-capaciteit. Vooraf betaalt, krijgt u krijg een korting op de resources die u gebruikt. Azure-reserveringen kunnen aanzienlijk verkorten uw virtuele machine of kosten voor rekenuren van SQL database — tot 72% bij betalen per gebruik met een eenjarige of driejarige toezegging vooraf. Reserveringen voorzien in een korting op de factuur en zijn niet van invloed op de runtime-status van uw virtuele machines of SQL-databases.

Zie voor meer informatie, [wat zijn Azure-reserveringen?](../reservations/save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Azure Hybrid Benefit gebruiken

Als u al Windows Server of SQL Server-licenties in uw on-premises implementaties hebt, kunt u het programma Azure Hybrid Benefit gebruiken om op te slaan in Azure. Met het voordeel van Windows Server dekt elke licentie de kosten van het besturingssysteem (maximaal twee virtuele machines) en betaalt u alleen voor de basisrekenkosten. U kunt bestaande SQL Server-licenties gebruiken om maximaal 55 procent te besparen op op vCore gebaseerde SQL Database-opties. De opties omvatten SQL Server in Azure Virtual Machines en SQL Server Integration Services.

Zie voor meer informatie, [besparingscalculator Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Overige bronnen

Azure heeft ook een service waarmee u services bouwen die van de overtollige capaciteit in Azure voor lagere tarieven gebruikmaken. Zie voor meer informatie, [gebruiken met lage prioriteit VM's met Batch](../../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Volgende stappen
- Als u geen ervaring met Cost Management, leest u [wat is Azure Cost Management?](../cost-management-billing-overview.md) voor meer informatie over hoe het helpt bij het bewaken en beheren van Azure-uitgaven en zodat er optimaal gebruik van de resource.
