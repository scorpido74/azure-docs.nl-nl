---
title: Migratie plan bouwen met Azure Migrate | Microsoft Docs
description: Biedt hulp bij het maken van een migratie plan met Azure Migrate.
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/21/2020
ms.author: rajosh
ms.openlocfilehash: e80ec71bef8c34331fac076b7b08be48a9571c2e
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107696"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Migratieplan bouwen met Azure Migrate

In dit artikel vindt u een korte hand leiding voor het bouwen van uw migratie plan naar Azure met [Azure migrate](migrate-services-overview.md).Als u andere vragen hebt, raadpleegt u deze bronnen:

- [Algemene vragen](resources-faq.md) over Azure migrate
- Vragen over het [Azure migrate apparaat](common-questions-appliance.md)
- Vragen over [server migratie](common-questions-server-migration.md)
- Beantwoorde vragen in het [Azure migrate-forum](https://aka.ms/AzureMigrateForum) ontvangen

## <a name="define-the-goals-of-cloud-migration"></a>De doelen van Cloud migratie definiëren

Voordat u een migratie plan bouwt, is het belang rijk om de [motivatie](/azure/cloud-adoption-framework/strategy/motivations) te begrijpen en te evalueren om over te stappen op de Cloud, waardoor u meer succes volle bedrijfs resultaten kunt produceren. Naarmate het [Cloud-acceptatie raamwerk voor Azure](/azure/cloud-adoption-framework) wordt uitgelegd, kunnen er verschillende triggers en migratie methoden zijn die geschikt zijn voor uw bedrijf:  

**Kritieke zakelijke gebeurtenissen** | **Resultaat van de migratie**
--- | ---
Afsluiten van Data Center | Kostenbesparingen
Fusie, overname of divestiture | Reductie van leverancier of technische complexiteit
Vermindering van de kapitaal kosten | Optimalisatie van interne bewerkingen
Einde van ondersteuning voor essentiële technologieën | Toename in bedrijfs flexibiliteit
Reactie op wijzigingen van de naleving van de regelgeving | Voor bereiding op nieuwe technische mogelijkheden
Nieuwe vereisten voor gegevens soevereiniteit | Schalen om te voldoen aan de markt vraag
Vermindering van onderbrekingen en verbetering van de IT-stabiliteit | Schalen om te voldoen aan de geografische eisen

Uw migratie motivatie kan u ook helpen bij de strategische doelen en resultaten die u wilt bereiken door te migreren naar Azure. De volgende stap is het identificeren en plannen van een migratie naar Azure dat is afgestemd op uw workloads. Azure Migrate: Server Assessment hulp programma helpt u bij het beoordelen van on-premises workloads en biedt hulp middelen en hulpprogram ma's waarmee u kunt migreren.

## <a name="understand-your-digital-estate"></a>Meer informatie over uw digitale onroerend goed

Begin met het leren van uw on-premises infra structuur, toepassingen en afhankelijkheden voor het identificeren van werk belastingen die u naar Azure wilt migreren en voor het ophalen van geoptimaliseerde kosten prognoses. Het hulp programma voor Server evaluatie helpt u bij het beantwoorden van de volgende vragen:

### <a name="what-workloads-are-in-use"></a>Welke workloads worden er gebruikt?

Gebruik het Lightweight Azure Migrate-apparaat om een agentloze detectie uit te voeren van uw on-premises virtuele VMware-machines, Hyper-V-Vm's en fysieke servers. De continue detectie verzamelt de meta gegevens van de machine configuratie en prestaties en kan ook worden gebruikt voor het ophalen van de inventaris van geïnstalleerde toepassingen, en rollen/functies die op uw on-premises machines worden uitgevoerd. Het Azure Migrate apparaat verzamelt:

- Details van meta gegevens van de computers, schijven en Nic's

- Geïnstalleerde toepassingen, waaronder toepassingen en rollen/onderdelen  

- Prestatie gegevens met inbegrip van CPU-en geheugen gebruik, schijf-IOPS en door Voer

Vervolgens exporteert u de lijst met toepassings inventarisatie om te zien welke SQL Server exemplaren op uw workloads worden uitgevoerd en gebruikt u het hulp programma Azure Migrate: data base Assessment om de gereedheid te begrijpen.

 ![Toepassings inventaris op Portal](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Export van toepassings voorraad](./media/concepts-migration-planning/application-inventory-export.png)

Samen met de detectie gegevens van het hulp programma voor Server evaluatie, kunt u uw bestaande CMDB-gegevens gebruiken om onze weer gave van uw server en Data Base te bouwen en server distributie te begrijpen voor alle bedrijfs eenheden, toepassings eigenaren, geografi, enzovoort.

### <a name="what-dependencies-exist-between-workloads"></a>Welke afhankelijkheden bestaan er tussen werk belastingen?

Wanneer u uw servers hebt gedetecteerd, gebruikt u afhankelijkheids toewijzing zonder agent voor het visualiseren en identificeren van afhankelijkheden tussen servers en optimalisatie strategieën voor het verplaatsen van onafhankelijke servers naar Azure. De visualisatie helpt u te begrijpen of bepaalde machines in gebruik zijn of dat ze uit bedrijf kunnen worden genomen in plaats van te worden gemigreerd.  Zorg ervoor dat u afhankelijkheden analyseert om ervoor te zorgen dat er geen onverwachte storingen optreden tijdens de migratie. Wanneer u de toepassings voorraad en de afhankelijkheids toewijzing hebt uitgevoerd, kunt u groepen met hoge betrouw baarheid maken en de servers beoordelen.

 ![Toewijzing van afhankelijkheden](./media/concepts-migration-planning/expand-client-group.png)

### <a name="are-they-optimized-and-sized-properly"></a>Zijn ze geoptimaliseerd en hebben ze een juiste grootte?

Omdat Azure flexibiliteit biedt om de grootte van uw Cloud capaciteit gedurende de loop der tijd te verg Roten, kunt u het CPU-en geheugen resources die zijn toegewezen aan uw servers optimaliseren. Maak een evaluatie van de groep die is geïdentificeerd om de prestatie geschiedenis van de werk belastingen te begrijpen. deze is zeer belang rijk voor de aanbevolen VM-Sku's en schijven aanbevelingen op Azure.

## <a name="assess-your-readiness-for-migration"></a>Uw gereedheid voor migratie beoordelen

### <a name="readiness-and-suitability-analysis-for-azure"></a>Gereedheids-en geschiktheids analyse voor Azure
Exporteer het Azure VM-evaluatie rapport en filtreer door de volgende gereedheids categorieën om inzicht te krijgen in de VM-gereedheid voor Azure:

- **Gereed voor Azure**: u kunt deze machines naar Azure migreren zonder dat u wijzigingen hoeft aan te brengen  

- **Voorwaardelijk gereed voor Azure**: u kunt deze machines migreren naar Azure, maar u hebt kleine wijzigingen nodig op deze servers volgens de richt lijnen voor herstel van de evaluatie

- **Niet gereed voor Azure**: u kunt deze machines niet naar Azure migreren, maar u moet de problemen oplossen volgens de herstel richtlijnen vóór de migratie

- **Gereedheid onbekend**: de Azure migrate kan de gereedheid van de machine niet bepalen vanwege onvoldoende meta gegevens

Met behulp van de evaluatie van de Data Base kunt u de gereedheid voor het migreren van uw SQL Server-Data-to-Azure SQL Database of Azure SQL Managed instances beoordelen. U ziet het percentage migratie gereedheids status voor elk exemplaar van SQL Server. Voor elk van de instanties kunt u ook het aanbevolen doel in azure zien, mogelijke migratie blok keringen, het aantal wijzigingen, de gereedheid voor Azure SQL DB/Azure SQL-VM en het compatibiliteits niveau. U kunt dieper ingrijpen om inzicht te krijgen in de gevolgen van de migratie blokken en aanbevelingen om ze te corrigeren.

 ![Database evaluaties](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Aanbevelingen voor de grootte

Nadat de computer is gemarkeerd als gereed voor Azure, maakt de server evaluatie aanbevelingen voor het identificeren van de virtuele machine van Azure en de schijf SKU voor uw virtuele machines. U kunt ervoor kiezen om de grootte aanbeveling te bekijken op basis van de prestatie geschiedenis (zodat u resources optimaliseert tijdens het migreren) of op basis van de on-premises configuratie zonder dat u rekening houdt met de prestatie geschiedenis. Voor data bases kunt u de aanbevelingen voor de data base-SKU, de prijs categorie en het reken niveau in uw database evaluatie bekijken.  

### <a name="compute-assessments-to-get-estimated-costs-for-running-the-workloads-in-azure"></a>Reken analyses voor het verkrijgen van de geschatte kosten voor het uitvoeren van de werk belastingen in azure

Met de optie voor de juiste grootte op *basis van prestaties* in evaluaties kunt u werk belastingen voor Azure optimaliseren. Naast supportte zijn er nog enkele andere mogelijkheden om kosten te besparen:

- **Gereserveerde instanties**: met gereserveerde instanties kunt u de kosten aanzienlijk verlagen vergeleken met betalen per gebruik-tarieven met 1 of 3 jaar voor Windows en Linux (vm's)

- **Azure Hybrid bene fits**: u kunt on-premises Windows Server-licenties met Software Assurance naar Azure brengen en deze combi neren met opties voor gereserveerde instanties

- **Enterprise Agreement aanbieding (EA)**: de Enterprise Agreement biedt ingebouwde besparingen die van toepassing zijn op uw abonnement

- **Aanbiedingen**: er zijn meerdere Azure-aanbiedingen, bijvoorbeeld Pay-As-You-Go Dev/Test en Enterprise dev/test die lagere tarieven bieden voor ontwikkel-of test-vm's

- **VM-uptime**: u kunt de duur in dagen per maand en uur per dag vermelden wanneer de virtuele machines van Azure worden uitgevoerd om uw kosten te verlagen (niet van toepassing op RI)

- **Doel regio**: u kunt in verschillende regio's meerdere beoordelingen maken om te vergelijken of de migratie naar een bepaalde regio in een geografie een rendabelere

- **Aanbevelingen op basis van prestaties**: als Best Practice, kunt u gebruikmaken van de aanbevolen Azure VM-aanbevelingen die u helpen bij het opslaan van Cloud kosten

### <a name="visualize-data"></a>Gegevens visualiseren

U kunt het server beoordelings rapport met de Azure-gereedheid en maandelijkse kosten distributie in de portal bekijken en de evaluatie exporteren om meer visualisaties op de detectie-en evaluatie gegevens toe te passen om het migratie plan uitgebreid te maken. U kunt meerdere beoordelingen maken voor verschillende combi Naties van eigenschappen en de set eigenschappen kiezen die het meest geschikt is voor uw bedrijf.  

 ![Overzicht van evaluaties](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gaps-and-potential-blockers"></a>Hiaten en mogelijke blok keringen evalueren

Bepaal bij het bepalen van de toepassingen en de onderliggende infra structuur de downtime voor deze toepassingen en zoek naar operationele afhankelijkheden tussen uw toepassingen en de onderliggende infra structuur. Deze analyse kan u helpen bij het plannen van migraties die voldoen aan uw beoogde herstel tijd (RTO) en zorgen ervoor dat er mini maal nul gegevens verloren gaan. Voordat u migreert, raden we u aan om compatibiliteits problemen of niet-ondersteunde functies te controleren en te verhelpen, waarbij u de migratie van uw servers en SQL-data bases kunt blok keren met behulp van het server evaluatie rapport en Azure Migrate: aanbevelingen voor de evaluatie van de data base.

### <a name="first-workloads-to-target-and-approach"></a>Eerste werk belastingen op doel en benadering

Nu u alle essentiële informatie hebt om uw migratie beslissing te nemen, moet u bepalen welke toepassingen en werk belastingen het eerst moeten worden gemigreerd. Ontwikkel een ' toep assen en leren ' benadering om uw beoogde toepassingen op een systematische en bewaak bare manier te migreren, zodat u eventuele fouten in deze strategie kunt verstrijken voordat u een volledige migratie inneemt. U kunt ook strategische factoren, zoals-complexiteit en tijd, gebruiken om te migreren, een urgente, productie-of productie omgeving, naleving en beveiligings vereisten, toepassings kennis, enzovoort om te bepalen welke toepassings groepen moeten worden gemigreerd.

Enkele aanbevolen migratie strategieën zijn:

- Geef **een prioriteit voor uw snelle WINS**: u kunt de evaluatie rapporten gebruiken om te identificeren dat er weinig hangende vruchten zijn, met inbegrip van de servers en data bases die volledig klaar zijn en minimale inspanning vereisen om te migreren naar Azure:
    - Gereed voor Azure: Exporteer uw evaluatie rapport en filter alle computers die ' gereed voor Azure ' zijn. Dit kan uw eerste groep machines zijn die u kunt optillen en SHIFT met de Azure Migrate: hulp programma voor server migratie.
    - Einde van de ondersteuning van het besturings systeem: Exporteer het evaluatie rapport en filter alle computers waarop de Windows Server 2008-en Windows Server 2008 R2-besturings systemen worden uitgevoerd. Deze Sku's zijn einde van ondersteuning en alleen Azure biedt u gratis drie jaar beveiligings updates wanneer u ze naar Azure migreert. Wanneer u gereserveerde instanties combi neren, Azure Hybrid Benefit en gebruiken, kan het opslaan veel hoger zijn.
    - SQL Server migratie: gebruik de aanbevelingen voor de evaluatie van de data base om de data bases die gereed zijn voor Azure SQL-data bases te migreren met behulp van de Azure Migrate: database migratie en de data bases die gereed zijn voor Azure SQL VM met de Azure Migrate: Server
    - Software-einde van ondersteuning: Exporteer uw toepassings voorraad en filtreer een van de software/uitbrei dingen die mogelijk het einde van de ondersteuning bereiken. U moet prioriteit geven aan deze toepassingen.
    - Over ingerichte Vm's: Exporteer uw evaluatie rapport en filtreer computers met een laag CPU-gebruik (%) en geheugen gebruik (%).  U kunt deze mogelijkheid gebruiken om te migreren naar een gefactureerde VM in Azure en op te slaan wat u hebt betaald voor gefactureerde resources.
    - Capaciteits beperkingen: Exporteer uw evaluatie rapport en filtreer computers met een hoog CPU-gebruik (%) en geheugen gebruik (%).  U kunt voor komen dat de overbelaste Vm's afbreken en de prestaties verbeteren door ze naar Azure te migreren en de mogelijkheden voor automatisch schalen te gebruiken om aan de vraag te voldoen. U kunt ook het beoordelings rapport bekijken om inzicht te krijgen in uw opslag beperkingen door de schijf-IOPS en-door voer te analyseren en het aanbevolen schijf type te vinden dat geschikt is voor uw behoeften.

- **Begin klein en vervolgens groot**: begin met het verplaatsen van toepassingen en werk belastingen die minimale Risico's en minder complex zijn, om het vertrouwen in uw migratie strategie te bouwen. U kunt ook uw Azure Migrate beoordelings aanbevelingen door lopen met de CMDB-opslag plaats van uw organisatie om de werk belasting van ontwikkel-en test omgevingen te vinden en te migreren in uw pilot migraties. De informatie van deze Pilotes kan worden gebruikt bij het migreren van productie werkbelastingen.  

- **Voldoen aan uw vereisten voor reglementaire/branche**: Azure houdt de grootste portefeuille voor naleving in de hele branche, in termen van de breedte en diepte van de aanbiedingen. Gebruik deze mogelijkheid om uw migraties een prioriteit te geven aan Azure en te voldoen aan uw nationale, regionale en branchespecifieke normen en wetten. Dit geldt met name voor organisaties die met bedrijfs kritieke gegevens omgaan of gevoelige informatie bevatten of die zich in zwaar gereglementeerde branches bevinden, waarbij de normen en voor Schriften Abound en in bepaalde gevallen vaak kunnen veranderen, waardoor het lastig is om bij te houden.  

## <a name="finalize-the-migration-planandprepare-formigration"></a>Het migratie plan volt ooien en de migratie voorbereiden

Voordat u uw migratie plan voltooit, moet u ervoor zorgen dat de volgende belang rijke overwegingen met betrekking tot de migratie niet worden afgespeeld voor uw migratie planning:

- Evalueer beperkingen van de netwerk bandbreedte en latentie, waardoor onverwachte vertragingen kunnen optreden en de migratie replicatie snelheid wordt verstoord.

- Buffer in tijd voor het uitvoeren van prestaties en acceptatie tests door gebruikers voor de gemigreerde toepassingen of het uitvoeren van aanpassingen voor de apps na de migratie, zoals het bijwerken van database verbindings reeksen en webserver configuraties, het uitvoeren van cutover en opschonen, enzovoort.

- Controleer de aanbevolen Azure-machtigingen en uw server/database toegangs rollen en het machtigings model dat nodig is voor de migratie.

- Bereid uw organisatie voor en zorg ervoor dat personeel de digitale trans formatie kan uitlijnen. Een solide trainings basis is belang rijk voor een succes volle wijziging van de organisatie. Bekijk de gratis training die beschikbaar is op [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), met inbegrip van cursussen over de basis principes van Azure, de architectuur van de oplossing en de beveiliging. Moedig uw team aan om ook [Azure-certificering](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)te verkennen   .  

- Ontvang zo nodig ondersteuning voor uw implementatie. Veel organisaties kiezen buiten de hulp om hun Cloud migratie te ondersteunen. Als u snel wilt overschakelen naar Azure en wilt vertrouwen met persoonlijke hulp, overweeg dan een [Azure expert managed service provider](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)   of [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF).  

Maak een effectief migratie plan voor de cloud dat gedetailleerde informatie bevat over de lijst/groepen toepassingen die u wilt migreren, de beschik baarheid van apps en data bases en downtime-beperkingen en gewenste migratie mijlpaal. Het migratie plan moet ook bepalen hoe lang het kopiëren van de gegevens duurt en een considerate-buffer voor testen na de migratie en cutover-activiteiten bevat. De tests na de migratie moeten functionele, integratie-, beveiligings-en prestatie tests omvatten om ervoor te zorgen dat de gemigreerde toepassingen naar verwachting werken en dat alle database objecten en gegevens relaties zijn overgedragen naar de Cloud. De tests na de migratie moeten functionele, integratie-, beveiligings-en prestatie tests omvatten om ervoor te zorgen dat de gemigreerde toepassingen naar verwachting werken en dat alle database objecten en gegevens relaties zijn overgedragen naar de Cloud.  

Gebruik deze analyse voor het bouwen van een migratie schema en het declareren van een onderhouds venster voor het migreren van uw toepassingen en data bases met een minimale downtime van nul en het beperken van de mogelijke operationele/business-impact tijdens de migratie.  

We raden u aan om de *test migratie* van de Azure migrate altijd te testen en door te gaan met het uitvoeren van de volledige migratie naar Azure. Deze werkelijke gegevens helpen u bij het schatten van de daad werkelijke tijd en het maken van de nodige aanpassingen voor uw migratie plan. De test migratie biedt ook een kans om mogelijke problemen met het migratie plan op te sporen en deze op te lossen voordat de daad werkelijke migratie plaatsvindt.  

Wanneer u klaar bent om te migreren, gebruikt u het *hulp programma voor server migratie* van Azure migrate en de *service voor gegevens migratie* van Azure migrate voor een naadloze en geïntegreerde migratie-ervaring met end-to-end-tracering. Het hulp programma voor migratie van servers ondersteunt de migratie van Vm's en servers die on-premises worden gehost in het Data Center van klanten of een andere particuliere of open bare Cloud, waaronder AWS, GCP, etc. met circa 0 uitval tijd. De Azure Database Migration Service is een volledig beheerde service die is ontworpen om naadloze migraties van meerdere database bronnen naar Azure-gegevens platforms mogelijk te maken met minimale downtime.  

> [!NOTE]
> Voor virtuele VMware-machines gebruikt server assessment het besturings systeem dat is opgegeven voor de virtuele machine in vCenter Server voor het afhandelen van de analyse van het gast besturingssysteem. Voor Linux-Vm's die worden uitgevoerd op VMware, wordt momenteel niet de exacte kernel-versie van het gast besturingssysteem geïdentificeerd.

## <a name="next-steps"></a>Volgende stappen

- Onderzoek de [reis voor Cloud migratie](/azure/architecture/cloud-adoption/getting-started/migrate)   in het Azure Cloud-acceptatie Framework.
- [Aan de slag](https://youtu.be/wFfq3YPxYHE) met Azure Migrate.
- Een evaluatie maken voor virtuele [VMware-machines](tutorial-assess-vmware.md) of [virtuele Hyper-V-machines](tutorial-assess-hyper-v.md).
