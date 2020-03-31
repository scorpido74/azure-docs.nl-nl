---
title: Aanbevolen procedures - Azure Batch
description: Lees aanbevolen procedures en handige tips voor het ontwikkelen van uw Azure Batch-oplossing.
author: LauraBrenner
ms.author: labrenne
ms.date: 11/22/2019
ms.service: batch
ms.topic: article
manager: evansma
ms.openlocfilehash: 16fb2786f180b1e28b76d9246d599a871278d00d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022729"
---
# <a name="azure-batch-best-practices"></a>Aanbevolen procedures voor Azure Batch

In dit artikel wordt een verzameling aanbevolen procedures besproken voor het effectief en efficiënt gebruiken van de Azure Batch-service. Deze best practices zijn afgeleid van onze ervaring met Batch en de ervaringen van Batch-klanten. Het is belangrijk om dit artikel te begrijpen om ontwerpvalkuilen, potentiële prestatieproblemen en antipatronen te vermijden tijdens het ontwikkelen en gebruiken van Batch.

In dit artikel leer je:

> [!div class="checklist"]
> - Wat zijn de best practices
> - Waarom u best practices moet gebruiken
> - Wat kan er gebeuren als u de aanbevolen procedures niet volgt?
> - Hoe de best practices te volgen

## <a name="pools"></a>Pools

Batchgroepen zijn de rekenbronnen voor het uitvoeren van taken op de Batch-service. In de volgende secties vindt u richtlijnen voor de beste procedures die u moet volgen bij het werken met batchgroepen.

### <a name="pool-configuration-and-naming"></a>Configuratie en naamgeving van de groep

- **Pooltoewijzingsmodus**  
    Wanneer u een Batch-account maakt, u kiezen tussen twee toewijzingsmodi voor een groep: **Batchservice** of **gebruikersabonnement**. In de meeste gevallen moet u de standaardbatchservicemodus gebruiken, waarin groepen achter de schermen worden toegewezen in batchbeheerabonnementen. In de alternatieve modus Gebruikersabonnement worden Batch-VM's en andere resources rechtstreeks in uw abonnement gemaakt wanneer er een groep wordt gemaakt. Gebruikersabonnementsaccounts worden voornamelijk gebruikt om een belangrijke, maar kleine subset van scenario's in te schakelen. U meer lezen over de abonnementsmodus van gebruikers bij [Extra configuratie voor de gebruikersabonnementsmodus](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Overweeg de uitvoeringstijd van taken en taken bij het bepalen van taak om toewijzing te maken.**  
    Als u taken hebt die voornamelijk bestaan uit kortlopende taken en de verwachte totale taaktellingen klein zijn, zodat de totale verwachte looptijd van de taak niet lang is, wijst u geen nieuwe groep toe voor elke taak. De toewijzingstijd van de knooppunten vermindert de looptijd van de taak.

- **Pools moeten meer dan één compute node hebben.**  
    Individuele knooppunten zijn niet gegarandeerd altijd beschikbaar. Hoewel ongewoon, hardwarefouten, updates van het besturingssysteem en een groot aantal andere problemen kunnen leiden tot offline zijn de afzonderlijke knooppunten. Als uw batchwerkworkload deterministische, gegarandeerde voortgang vereist, moet u groepen toewijzen met meerdere knooppunten.

- **Gebruik resourcenamen niet opnieuw.**  
    Batch resources (jobs, pools, etc.) komen en gaan vaak na verloop van tijd. U bijvoorbeeld een groep op maandag maken, deze op dinsdag verwijderen en op donderdag een andere groep maken. Elke nieuwe resource die u maakt, moet een unieke naam krijgen die u nog niet eerder hebt gebruikt. Dit kan worden gedaan met behulp van een GUID (als de volledige resourcenaam of als onderdeel ervan) of door de tijd in te sluiten die de resource is gemaakt in de resourcenaam. Batch ondersteunt [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), die kan worden gebruikt om een resource een menselijke leesbare naam te geven, zelfs als de werkelijke resource-ID iets is dat niet zo menselijk vriendelijk is. Het gebruik van unieke namen maakt het makkelijker voor u om te differentiëren welke specifieke bron iets heeft gedaan in logboeken en statistieken. Het verwijdert ook ambiguïteit als u ooit een ondersteuningsaanvraag voor een bron moet indienen.

- **Continuïteit tijdens zwembadonderhoud en storing.**  
    Het is het beste om uw vacatures dynamisch te laten gebruiken. Als uw taken gebruik maken van dezelfde pool voor alles, is er een kans dat uw banen niet zal worden uitgevoerd als er iets mis gaat met het zwembad. Dit is vooral belangrijk voor tijdgevoelige workloads. Als u dit wilt oplossen, selecteert of maakt u een groep dynamisch wanneer u elke taak plant, of hebt u een manier om de naam van het zwembad te overschrijven, zodat u een ongezonde groep omzeilen.

- **Bedrijfscontinuïteit tijdens zwembadonderhoud en -storing**  
    Er zijn vele mogelijke oorzaken die kunnen voorkomen dat een pool groeit tot de gewenste grootte, zoals interne fouten, capaciteitsbeperkingen, enz. Om deze reden moet u klaar zijn om taken te retargeten bij een andere groep (mogelijk met een andere VM-grootte - Batch ondersteunt dit via [UpdateJob)](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)indien nodig. Vermijd het gebruik van een statische pool-ID met de verwachting dat deze nooit zal worden verwijderd en nooit zal veranderen.

### <a name="pool-lifetime-and-billing"></a>Levensduur en facturering van de pool

De levensduur van de groep kan variëren, afhankelijk van de toewijzingsmethode en de opties die worden toegepast op de poolconfiguratie. Pools kunnen op elk moment een willekeurige levensduur en een wisselend aantal compute nodes in de groep hebben. Het is uw verantwoordelijkheid om de compute nodes in de groep expliciet te beheren, of via functies die door de service worden geleverd (autoscale of autopool).

- **Houd zwembaden vers.**  
    U moet het formaat van uw pools om de paar maanden wijzigen naar nul om ervoor te zorgen dat u de nieuwste node-agent-updates en bugfixes krijgt. Uw groep ontvangt geen nodeagent-updates, tenzij deze opnieuw is gemaakt of is aangepast tot 0 compute-knooppunten. Voordat u uw pool opnieuw maakt of het formaat ervan wijzigt, wordt aanbevolen om logboeken van knooppunten te downloaden voor foutopsporingsdoeleinden, zoals besproken in de sectie [Knooppunten.](#nodes)

- **Opnieuw maken van een pool**  
    Op een vergelijkbare opmerking is het niet aan te raden om uw pools dagelijks te verwijderen en opnieuw te maken. Maak in plaats daarvan een nieuwe groep, werk uw bestaande taken bij om naar de nieuwe groep te wijzen. Zodra alle taken naar de nieuwe groep zijn verplaatst, verwijdert u vervolgens de oude groep.

- **Efficiëntie en facturering bij de pool**  
    Batch zelf brengt geen extra kosten met zich mee, maar u brengt wel kosten in rekening voor de gebruikte rekenresources. U wordt gefactureerd voor elke compute-knooppunt in de groep, ongeacht de status waarin het zich bevindt. Dit omvat alle kosten die nodig zijn voor het knooppunt om te worden uitgevoerd, zoals opslag- en netwerkkosten. Zie [Kostenanalyse en budgetten voor Azure Batch voor](budget.md)meer aanbevolen procedures.

### <a name="pool-allocation-failures"></a>Fouten in de toewijzing van de groep

Fouten in de toewijzing van de groep kunnen op elk moment optreden tijdens de eerste toewijzing of de volgende wijzigingen. Dit kan te wijten zijn aan tijdelijke capaciteitsuitputting in een regio of storingen in andere Azure-services waarop Batch vertrouwt. Uw kernquotum is geen garantie, maar een limiet.

### <a name="unplanned-downtime"></a>Niet-geplande uitvaltijd

Batchpools kunnen downtime-gebeurtenissen in Azure ervaren. Dit is belangrijk om in gedachten te houden bij het plannen en ontwikkelen van uw scenario of workflow voor Batch.

In het geval dat een knooppunt mislukt, probeert Batch deze compute nodes automatisch namens u te herstellen. Dit kan leiden tot het opnieuw plannen van een lopende taak op het knooppunt dat wordt hersteld. Zie [Ontwerpen voor nieuwe pogingen](#designing-for-retries-and-re-execution) voor meer informatie over onderbroken taken.

- **Afhankelijkheid van Azure-regio**  
    Het wordt aangeraden om niet afhankelijk te zijn van één Azure-regio als u een tijdgevoelige of productiewerkbelasting hebt. Hoewel zeldzaam, zijn er problemen die een hele regio kunnen beïnvloeden. Als uw verwerking bijvoorbeeld op een bepaald tijdstip moet beginnen, u overwegen de groep in uw primaire regio *ruim voor uw begintijd*op te schalen. Als die poolschaal mislukt, u terugvallen op het opschalen van een groep in een back-upgebied (of regio's). Pools over meerdere accounts in verschillende regio's bieden een kant-en-klare, gemakkelijk toegankelijke back-up als er iets misgaat met een andere pool. Zie [Uw aanvraag voor hoge beschikbaarheid ontwerpen voor](high-availability-disaster-recovery.md)meer informatie.

## <a name="jobs"></a>Taken

Een taak is een container die is ontworpen om honderden, duizenden of zelfs miljoenen taken te bevatten.

- **Veel taken in een taak plaatsen**  
    Het gebruik van een taak om één taak uit te voeren is inefficiënt. Het is bijvoorbeeld efficiënter om één taak te gebruiken met 1000 taken in plaats van 100 taken te maken die elk 10 taken bevatten. Het uitvoeren van 1000 banen, elk met een enkele taak, zou de minst efficiënte, langzaamste en duurste aanpak te nemen.  

    Ontwerp geen Batch-oplossing waarvoor duizenden actieve taken nodig zijn. Er is geen quotum voor taken, dus het uitvoeren van zo veel taken onder zo weinig mogelijk taken maakt efficiënt gebruik van uw [taak- en taakplanningsquota.](batch-quota-limit.md#resource-quotas)

- **Levensduur van de baan**  
    Een batchtaak heeft een levensduur voor onbepaalde tijd totdat deze uit het systeem is verwijderd. De status van een taak geeft aan of deze meer taken voor het plannen kan accepteren of niet. Een taak wordt niet automatisch verplaatst naar voltooide status, tenzij deze expliciet wordt beëindigd. Dit kan automatisch worden geactiveerd via de eigenschap [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) of [maxWallClockTime.](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)

Er is een [standaardquotum voor actieve taak en taakplanning](batch-quota-limit.md#resource-quotas). Taken en taakroosters in voltooid staat tellen niet mee voor dit quotum.

## <a name="tasks"></a>Taken

Taken zijn afzonderlijke werkeenheden die een taak omvatten. Taken worden door de gebruiker ingediend en door Batch op op gegevensknooppunten gepland. Er zijn verschillende ontwerpoverwegingen te maken bij het maken en uitvoeren van taken. In de volgende secties worden veelvoorkomende scenario's uitgelegd en hoe u uw taken ontwerpen om problemen op te lossen en efficiënt uit te voeren.

- **Takengegevens opslaan als onderdeel van de taak.**  
    Compute-knooppunten zijn van nature vluchtig. Er zijn veel functies in Batch, zoals autopool en autoscale die het gemakkelijk maken voor knooppunten verdwijnen. Wanneer knooppunten de pool verlaten (vanwege een formaat of een groep verwijderen), worden ook alle bestanden op die knooppunten verwijderd. Vanwege dit, het wordt aanbevolen dat voordat een taak is voltooid, het beweegt de output af van het knooppunt wordt uitgevoerd op en naar een duurzame winkel, op dezelfde manier als een taak mislukt moet logs die nodig zijn om het falen van een duurzame winkel te diagnosticeren. Batch heeft ondersteuning Azure Storage geïntegreerd om gegevens te uploaden via [OutputFiles,](batch-task-output-files.md)evenals een verscheidenheid aan gedeelde bestandssystemen, of u de upload zelf uitvoeren in uw taken.

### <a name="task-lifetime"></a>Levensduur van de taak

- **Taken verwijderen wanneer ze zijn voltooid.**  
    Verwijder taken wanneer ze niet meer nodig zijn of stel een beperking [van de bewaartijdtaak](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) in. Als `retentionTime` een batch is ingesteld, ruimt Batch automatisch de `retentionTime` schijfruimte op die door de taak wordt gebruikt wanneer deze verloopt.  

    Het verwijderen van taken bereikt twee dingen. Het zorgt ervoor dat u geen opbouw van taken in de taak hebt, waardoor het zoeken/vinden van de taak waarin u geïnteresseerd bent moeilijker wordt (omdat u door de voltooide taken moet filteren). Het ruimt ook de bijbehorende taakgegevens `retentionTime` op het knooppunt op (opgegeven is nog niet geraakt). Dit zorgt ervoor dat uw knooppunten niet vol lopen met taakgegevens en geen schijfruimte meer hebben.

### <a name="task-submission"></a>Taakindiening

- **Dien een groot aantal taken in een verzameling in.**  
    Taken kunnen individueel of in collecties worden ingediend. Dien taken in [verzamelingen](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) van maximaal 100 tegelijk in wanneer u taken in bulk indient om de overhead- en indieningstijd te verkorten.

### <a name="task-execution"></a>Taakuitvoering

- **Uw maximale taken per knooppunt kiezen**  
    Batch ondersteunt overondertekenentaken op knooppunten (het uitvoeren van meer taken dan een knooppunt heeft kernen). Het is aan u om ervoor te zorgen dat uw taken "passen" in de knooppunten in uw pool. U bijvoorbeeld een gedegradeerde ervaring hebben als u acht taken probeert te plannen die elk 25% CPU-gebruik verbruiken op één knooppunt (in een groep met `maxTasksPerNode = 8`).

### <a name="designing-for-retries-and-re-execution"></a>Ontwerpen voor herplaatsen en heruitvoering

Taken kunnen automatisch opnieuw worden geprobeerd door Batch. Er zijn twee soorten nieuwe pogingen: door de gebruiker gecontroleerd en intern. Door de gebruiker gecontroleerde retries worden opgegeven door de [maxTaskRetryCount van](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet)de taak. Wanneer een programma dat is opgegeven in de taak wordt afgesloten met een niet-nulexitcode, wordt de taak opnieuw geprobeerd tot de waarde van de `maxTaskRetryCount`.

Hoewel deze zeldzaam is, kan een taak intern opnieuw worden geprobeerd vanwege fouten op het rekenknooppunt, zoals het niet kunnen bijwerken van de interne status of een fout op het knooppunt terwijl de taak wordt uitgevoerd. De taak wordt opnieuw geprobeerd op hetzelfde compute-knooppunt, indien mogelijk, tot een interne limiet voordat u de taak opgeeft en de taak uitstelt die door Batch moet worden opnieuw gepland, mogelijk op een ander rekenknooppunt.

- **Duurzame taken bouwen**  
    Taken moeten zodanig zijn ontworpen dat ze bestand zijn tegen storingen en dat ze opnieuw kunnen worden geprobeerd. Dit is vooral belangrijk voor langlopende taken. Zorg ervoor dat taken hetzelfde, eenenkel resultaat genereren, zelfs als ze meer dan één keer worden uitgevoerd. Een manier om dit te bereiken is om uw taken "doel zoeken". Een andere manier is om ervoor te zorgen dat uw taken zijn idempotent (taken hebben hetzelfde resultaat, ongeacht hoe vaak ze worden uitgevoerd).

    Een veelvoorkomend voorbeeld is een taak om bestanden naar een compute-knooppunt te kopiëren. Een eenvoudige aanpak is een taak die alle opgegeven bestanden elke keer dat het wordt uitgevoerd kopieën, die inefficiënt is en niet is gebouwd om bestand te zijn tegen falen. Maak in plaats daarvan een taak om ervoor te zorgen dat de bestanden zich op het compute-knooppunt bevinden. een taak die geen bestanden kopieert die al aanwezig zijn. Op deze manier gaat de taak verder waar deze was gebleven als deze werd onderbroken.

- **Knooppunten met lage prioriteit**  
    Er zijn geen ontwerpverschillen bij het uitvoeren van uw taken op speciale of knooppunten met een lage prioriteit. Of een taak wordt uitgesteld tijdens het uitvoeren op een knooppunt met lage prioriteit of onderbroken als gevolg van een fout op een specifiek knooppunt, beide situaties worden beperkt door het ontwerpen van de taak om bestand te zijn tegen falen.

- **Uitvoeringstijd van taak**  
    Vermijd taken met een korte uitvoeringstijd. Taken die slechts één tot twee seconden worden uitgevoerd, zijn niet ideaal. U moet proberen om een aanzienlijke hoeveelheid werk te doen in een individuele taak (10 seconden minimum, gaan tot uren of dagen). Als elke taak één minuut (of meer) wordt uitgevoerd, is de planningsoverhead als een fractie van de totale rekentijd klein.

## <a name="nodes"></a>Knooppunten

- **Starttaken moeten idempotent zijn**  
    Net als bij andere taken moet de taak voor het starten van het knooppunt idempotent zijn, omdat deze telkens opnieuw wordt uitgevoerd wanneer het knooppunt wordt opgestart. Een idempotente taak is gewoon een taak die een consistent resultaat oplevert wanneer het meerdere keren wordt uitgevoerd.

- **Beheer langlopende services via de interface voor besturingssysteemservices.**  
    Soms is het nodig om een andere agent naast de batchagent in het knooppunt uit te voeren, bijvoorbeeld om gegevens van het knooppunt te verzamelen en te rapporteren. We raden u aan deze agents te implementeren als OS-services, zoals een Windows-service of een Linux-service. `systemd`  

    Bij het uitvoeren van deze services mogen ze geen bestandsvergrendelingen opnemen op bestanden in door Batch beheerde mappen op het knooppunt, omdat batch anders deze mappen niet kan verwijderen vanwege de bestandsvergrendelingen. Als u bijvoorbeeld een Windows-service installeert in een starttaak, kopieert u de bestanden in plaats van de service rechtstreeks vanaf de werkmap voor de starttaak te starten (als de bestanden bestaan, slaat u de kopie gewoon over). Installeer de service vanaf die locatie. Wanneer Batch uw starttaak opnieuw uitvoert, wordt de werkmap voor de starttaak verwijderd en opnieuw gemaakt. Dit werkt omdat de service bestandsvergrendelingen heeft op de andere map en niet de werkmap voor starttaken.

- **Voorkomen dat u mapknooppunten maakt in Windows**  
    Directory-knooppunten, ook wel directory-harde koppelingen genoemd, zijn moeilijk te behandelen tijdens taak- en taakopruiming. Gebruik symlinks (soft-links) in plaats van harde links.

- **De batchagentlogboeken verzamelen als er een probleem is**  
    Als u een probleem ziet met betrekking tot het gedrag van een knooppunt of taken die op een knooppunt worden uitgevoerd, wordt aanbevolen om de batchagentlogboeken te verzamelen voordat de knooppunten in kwestie worden verwijderd. De batchagentlogboeken kunnen worden verzameld met de API voor batchlogboeken uploaden. Deze logboeken kunnen worden geleverd als onderdeel van een ondersteuningsticket voor Microsoft en helpen bij het oplossen van problemen en het oplossen van problemen.

## <a name="security"></a>Beveiliging

### <a name="security-isolation"></a>Beveiliging isolement

Voor de toepassing van isolatie, als uw scenario vereist het isoleren van taken van elkaar, dan moet u deze taken te isoleren door ze in aparte zwembaden. Een groep is de beveiligingsisolatiegrens in Batch en standaard zijn twee groepen niet zichtbaar of kunnen ze met elkaar communiceren. Vermijd het gebruik van afzonderlijke Batch-accounts als isolatiemiddel.

## <a name="moving"></a>Verplaatsen

### <a name="move-batch-account-across-regions"></a>Batch-account verplaatsen tussen regio's 

Er zijn verschillende scenario's waarin u uw bestaande Batch-account van de ene regio naar de andere wilt verplaatsen. U bijvoorbeeld naar een andere regio verhuizen als onderdeel van de planning voor noodherstel.

Azure Batch-accounts kunnen niet van het ene gebied naar het andere worden verplaatst. U echter wel een Azure Resource Manager-sjabloon gebruiken om de bestaande configuratie van uw Batch-account te exporteren.  U de resource vervolgens in een andere regio fasen door het batchaccount naar een sjabloon te exporteren, de parameters te wijzigen die overeenkomen met het doelgebied en de sjabloon vervolgens te implementeren in de nieuwe regio. Nadat u de sjabloon naar de nieuwe regio hebt geüpload, moet u certificaten, taakschema's en toepassingspakketten opnieuw maken. Als u de wijzigingen wilt vastleggen en de verplaatsing van het Batch-account wilt voltooien, moet u er rekening mee houden dat u het oorspronkelijke Batch-account of de brongroep wilt verwijderen.  

Zie [Snelaande: Azure Resource Manager-sjablonen maken en implementeren met behulp van de Azure-portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)voor meer informatie over ResourceBeheer en sjablonen.


