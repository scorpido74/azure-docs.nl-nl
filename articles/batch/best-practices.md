---
title: Aanbevolen procedures-Azure Batch
description: Leer de aanbevolen procedures en handige tips voor het ontwikkelen van uw Azure Batch-oplossing.
author: ju-shim
ms.author: jushiman
ms.date: 11/22/2019
ms.service: batch
ms.topic: article
manager: gwallace
ms.openlocfilehash: 20fc7844054fc7e05f56105e69ad6bd8a4272ed8
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026158"
---
# <a name="azure-batch-best-practices"></a>Aanbevolen procedures Azure Batch

In dit artikel wordt een verzameling aanbevolen procedures beschreven voor het effectief en efficiënt gebruik van de Azure Batch-service. Deze aanbevolen procedures zijn afgeleid van onze ervaring met batch en de ervaringen van batch-klanten. Het is belang rijk dat u dit artikel begrijpt om te voor komen dat u problemen met het ontwerpen, potentiële prestaties en anti patronen tijdens het ontwikkelen voor en gebruikt, batch.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> - Wat zijn de aanbevolen procedures
> - Waarom u aanbevolen procedures moet gebruiken
> - Wat er kan gebeuren als u de aanbevolen procedures niet kunt volgen
> - De aanbevolen procedures volgen

## <a name="pools"></a>Pools

Batch-Pools zijn de reken resources voor het uitvoeren van taken voor de batch-service. In de volgende secties vindt u richt lijnen voor de aanbevolen procedures voor het werken met batch-Pools.

### <a name="pool-configuration-and-naming"></a>Groeps configuratie en-naamgeving

- **Pooltoewijzingsmodus**  
    Bij het maken van een batch-account kunt u kiezen uit twee pool toewijzings modi: **batch-service** of **gebruikers abonnement**. In de meeste gevallen moet u de standaard batch-service modus gebruiken, waarin Pools achter de schermen worden toegewezen in door batch beheerde abonnementen. In de alternatieve modus Gebruikersabonnement worden Batch-VM's en andere resources rechtstreeks in uw abonnement gemaakt wanneer er een groep wordt gemaakt. Abonnements accounts voor gebruikers worden voornamelijk gebruikt om een belang rijke, maar kleine subset van scenario's mogelijk te maken. Meer informatie over de modus gebruikers abonnement vindt u op [aanvullende configuratie voor de modus gebruikers abonnement](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Denk na over taak-en taak uitvoerings tijd bij het bepalen van de taak voor pool toewijzing.**  
    Als u taken hebt die voornamelijk uit korte uitvoeringen bestaan, en het verwachte totale aantal taken klein is, zodat de totale verwachte uitvoerings tijd van de taak niet lang is, kunt u geen nieuwe groep voor elke taak toewijzen. De toewijzings tijd van de knoop punten vermindert de uitvoerings tijd van de taak.

- **Pools moeten meer dan één reken knooppunt hebben.**  
    Afzonderlijke knoop punten zijn niet gegarandeerd altijd beschikbaar. Hoewel ongebruikelijk, hardwarestoringen, updates van het besturings systeem en een andere fout optreden, kunnen afzonderlijke knoop punten offline zijn. Als uw batch-workload deterministisch, gegarandeerde voortgang vereist, moet u Pools met meerdere knoop punten toewijzen.

- **Resource namen niet opnieuw gebruiken.**  
    Batch-resources (taken, Pools, enz.) zijn vaak beschikbaar en zijn in de loop van de tijd. U kunt bijvoorbeeld een pool maken op maandag, deze verwijderen op dinsdag en vervolgens een andere pool maken op donderdag. Elke nieuwe resource die u maakt, moet een unieke naam hebben die u nog niet eerder hebt gebruikt. Dit kan worden gedaan met behulp van een GUID (als de volledige resource naam of als onderdeel ervan) of het insluiten van de tijd waarin de resource is gemaakt in de resource naam. Batch ondersteunt [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), die kan worden gebruikt om een resource een door de mens lees bare naam te geven, zelfs als de werkelijke resource-id iets anders is dan dat van mensen vriendelijke. Door gebruik te maken van unieke namen is het gemakkelijker om te onderscheiden welke resource iets in Logboeken en metrische gegevens heeft gedaan. Er wordt ook dubbel zinnigheid verwijderd als u ooit een ondersteunings aanvraag voor een resource moet opslaan.

- **Continuïteit tijdens het onderhoud van de groep en de fout.**  
    Het is raadzaam om uw taken dynamisch gebruik van Pools te laten doen. Als uw taken dezelfde pool gebruiken voor alles, is er een kans dat uw taken niet worden uitgevoerd als er iets mis gaat met de groep. Dit is vooral belang rijk voor tijd gevoelige workloads. Om dit probleem op te lossen, selecteert of maakt u een pool dynamisch wanneer u een taak plant of een manier om de naam van de groep te overschrijven zodat u een beschadigde pool kunt overs Laan.

- **Bedrijfs continuïteit tijdens het onderhoud van Pools en storingen**  
    Er zijn veel mogelijke oorzaken waardoor een pool niet groter kan worden dan de vereiste grootte die u wenst, zoals interne fouten, capaciteits beperkingen, enzovoort. Daarom moet u de taken in een andere groep kunnen richten (mogelijk met een andere VM-grootte-batch ondersteunt deze via [UpdateJob](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)), indien nodig. Vermijd het gebruik van een statische groeps-ID met de verwachting dat deze nooit wordt verwijderd en nooit wordt gewijzigd.

### <a name="pool-lifetime-and-billing"></a>Levens duur van groep en facturering

De levens duur van de groep kan variëren, afhankelijk van de toewijzings methode en opties die worden toegepast op de groeps configuratie. Pools kunnen op elk moment een wille keurige levens duur hebben en een verschillend aantal reken knooppunten in de pool. Het is uw verantwoordelijkheid om de reken knooppunten in de groep expliciet te beheren of door de functies van de service (automatisch schalen of autogroepen).

- **Bewaar Pools nieuw.**  
    Wijzig de grootte van uw Pools in nul om de paar maanden om ervoor te zorgen dat u de nieuwste updates voor de knooppunt agent en oplossingen krijgt. Uw pool ontvangt geen updates voor de knooppunt agent, tenzij deze opnieuw worden gemaakt of het formaat van 0 reken knooppunten wordt gewijzigd. Voordat u de groep opnieuw maakt of het formaat ervan wijzigt, is het raadzaam om de logboeken van de-agent te downloaden voor fout opsporing, zoals beschreven in de sectie [knoop punten](#nodes) .

- **Groep opnieuw maken**  
    Het wordt afgeraden om uw Pools dagelijks te verwijderen en opnieuw te maken op een vergelijk bare opmerking. Maak in plaats daarvan een nieuwe pool, werk uw bestaande taken bij zodat deze naar de nieuwe groep wijzen. Wanneer alle taken zijn verplaatst naar de nieuwe groep, verwijdert u de oude groep.

- **Efficiëntie en facturering van groep**  
    Batch zelf maakt geen extra kosten in rekening, maar u kunt wel kosten betalen voor de gebruikte reken resources. U wordt gefactureerd voor elk reken knooppunt in de pool, onafhankelijk van de status waarin het knoop punt zich bevindt. Dit omvat alle kosten die nodig zijn om het knoop punt uit te voeren, zoals opslag-en netwerk kosten. Zie [kosten analyse en budgetten voor Azure batch voor](budget.md)meer informatie over best practices.

### <a name="pool-allocation-failures"></a>Fouten bij pool toewijzing

Groeps toewijzings fouten kunnen zich voordoen op elk moment tijdens de eerste toewijzing of de volgende grootte. Dit kan worden veroorzaakt door tijdelijke capaciteits uitputting in een regio of fouten in andere Azure-Services waarop batch van afhankelijk is. Uw kern quotum is geen garantie, maar een limiet.

### <a name="unplanned-downtime"></a>Niet-geplande uitvaltijd

Het is mogelijk dat batch-Pools downtime-gebeurtenissen in azure kunnen ervaren. Het is belang rijk dat u rekening houdt met het plannen en ontwikkelen van uw scenario of werk stroom voor batch.

In het geval dat een knoop punt uitvalt, probeert batch automatisch deze reken knooppunten te herstellen namens u. Dit kan leiden tot het opnieuw plannen van elke actieve taak op het knoop punt dat wordt hersteld. Zie [ontwerpen voor nieuwe pogingen voor](#designing-for-retries-and-re-execution) meer informatie over onderbroken taken.

- **Azure-regio afhankelijkheid**  
    Als u een tijd gevoelige of productie werk belasting hebt, is het raadzaam om niet afhankelijk te zijn van één Azure-regio. Soms zijn er problemen die invloed kunnen hebben op een hele regio. Als uw verwerking bijvoorbeeld op een specifiek tijdstip moet worden gestart, kunt u overwegen om de groep in uw primaire regio goed te schalen voor *de start tijd*. Als deze pool schaal mislukt, kunt u terugvallen om een pool omhoog te schalen in een back-upgebied (of regio's). Pools over meerdere accounts in verschillende regio's bieden een kant-en-klare back-up als er iets mis is met een andere groep. Zie [uw toepassing ontwerpen voor hoge Beschik baarheid](high-availability-disaster-recovery.md)voor meer informatie.

## <a name="jobs"></a>Taken

Een taak is een container die is ontworpen om honderden, duizenden of zelfs miljoenen taken te bevatten.

- **Veel taken in een taak opnemen**  
    Het gebruik van een taak voor het uitvoeren van één taak is inefficiënt. Het is bijvoorbeeld efficiënter om één taak te gebruiken met 1000 taken in plaats van dat u 100 taken maakt die elk 10 taken bevatten. Het uitvoeren van 1000-taken, elk met één taak, is de minst efficiënte, langzaamste en meest dure aanpak.  

    Ontwerp geen batch-oplossing die duizenden gelijktijdig actieve taken vereist. Er zijn geen quota voor taken, dus als u zoveel taken uitvoert, worden de [taak-en taak plannings quota](batch-quota-limit.md#resource-quotas)efficiënt gebruikt.

- **Taak levensduur**  
    Een batch-taak heeft een onbeperkte levens duur tot deze uit het systeem is verwijderd. De status van een taak bepaalt of er meer taken kunnen worden geaccepteerd voor de planning of niet. Een taak wordt niet automatisch verplaatst naar de status voltooid, tenzij deze expliciet is beëindigd. Dit kan automatisch worden geactiveerd via de eigenschap [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) of [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints).

Er is een standaard quotum voor taak- [en taak planning](batch-quota-limit.md#resource-quotas). Taken en taak planningen in de status voltooid tellen niet mee voor dit quotum.

## <a name="tasks"></a>Taken

Taken zijn afzonderlijke werk eenheden waaruit een taak bestaat. Taken worden door de gebruiker verzonden en gepland door batch op reken knooppunten. Er zijn verschillende ontwerp overwegingen voor het maken en uitvoeren van taken. In de volgende secties worden veelvoorkomende scenario's beschreven en wordt uitgelegd hoe u taken kunt ontwerpen om problemen op te lossen en efficiënt uit te voeren.

- **Taak gegevens opslaan als onderdeel van de taak.**  
    Reken knooppunten zijn van nature. Er zijn veel functies in batch, zoals automatisch groeperen en automatisch schalen, waarmee knoop punten eenvoudig kunnen worden verwijderd. Wanneer knoop punten de pool verlaten (vanwege het wijzigen van het formaat of het verwijderen van een pool), worden alle bestanden op deze knoop punten ook verwijderd. Als gevolg hiervan wordt het aanbevolen dat voordat een taak wordt voltooid, de uitvoer van het knoop punt dat wordt uitgevoerd, wordt verplaatst naar een duurzame opslag, op dezelfde manier als een taak is mislukt, moeten logboeken worden verplaatst die nodig zijn voor het vaststellen van de fout in een duurzame opslag. Batch heeft geïntegreerde ondersteunings Azure Storage voor het uploaden van gegevens via [OutputFiles](batch-task-output-files.md), evenals een groot aantal gedeelde bestands systemen, of u kunt de upload zelf in uw taken uitvoeren.

### <a name="task-lifetime"></a>Levens duur van taak

- **Taken verwijderen wanneer deze zijn voltooid.**  
    Verwijder taken wanneer ze niet meer nodig zijn of stel een beperking voor een [retentionTime](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet) -taak in. Als een `retentionTime` is ingesteld, wordt door batch automatisch de schijf ruimte opgeschoond die door de taak wordt gebruikt wanneer de `retentionTime` verloopt.  

    Als u taken verwijdert, worden twee dingen uitgevoerd. Het zorgt ervoor dat u niet beschikt over een build van taken in de taak, waardoor u de taak die u wilt doorzoeken en die u in een later moment wilt filteren, kunt uitvoeren. Ook worden de bijbehorende taak gegevens op het knoop punt opgeschoond (de `retentionTime` nog niet is bereikt). Zo zorgt u ervoor dat uw knoop punten niet worden gevuld met taak gegevens en er geen schijf ruimte meer beschikbaar is.

### <a name="task-submission"></a>Verzen ding van taken

- **Een groot aantal taken verzenden in een verzameling.**  
    Taken kunnen worden verzonden op individuele basis of in verzamelingen. Verzend taken in [verzamelingen](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) van maxi maal 100 tegelijk bij het bulksgewijs verzenden van taken om de overhead-en inzendings tijd te verminderen.

### <a name="task-execution"></a>Taak uitvoering

- **Het maximum aantal taken per knoop punt kiezen**  
    Batch ondersteunt het overabonneren van taken op knoop punten (het uitvoeren van meer taken dan een knoop punt heeft kern geheugens). Het is aan u om ervoor te zorgen dat de taken in de knoop punten in uw pool passen. U kunt bijvoorbeeld een gedegradeerde ervaring hebben als u probeert acht taken te plannen die elk een CPU-gebruik van 25% op één knoop punt (in een pool met `maxTasksPerNode = 8`) verbruikt.

### <a name="designing-for-retries-and-re-execution"></a>Ontwerpen voor nieuwe pogingen en opnieuw uitvoeren

Taken kunnen automatisch opnieuw worden uitgevoerd op batch. Er zijn twee soorten nieuwe pogingen: door de gebruiker beheerd en intern. Door de gebruiker beheerde nieuwe pogingen worden opgegeven door de [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet)van de taak. Wanneer een programma dat is opgegeven in de taak wordt afgesloten met een afsluit code die niet gelijk is aan nul, wordt de taak opnieuw uitgevoerd met de waarde van de `maxTaskRetryCount`.

Hoewel zeldzame gevallen kan een taak intern opnieuw worden geprobeerd vanwege storingen op het reken knooppunt, zoals het niet mogelijk is interne status of een fout op het knoop punt bij te werken terwijl de taak wordt uitgevoerd. De taak wordt indien mogelijk opnieuw geprobeerd op hetzelfde reken knooppunt, tot een interne limiet voordat de taak wordt uitgevoerd en de taak moet worden uitgesteld op basis van een batch, mogelijk op een ander reken knooppunt.

- **Duurzame taken bouwen**  
    Taken moeten zodanig zijn ontworpen dat ze zich kunnen voordoen en het opnieuw proberen. Dit is vooral belang rijk voor langlopende taken. Als u dit wilt doen, moet u ervoor zorgen dat taken hetzelfde resultaat genereren, zelfs als ze meer dan één keer worden uitgevoerd. Een manier om dit te doen is het maken van de taken "Doelzoeken". Een andere manier is om ervoor te zorgen dat uw taken idempotent zijn (taken hebben hetzelfde resultaat, ongeacht het aantal keren dat ze worden uitgevoerd).

    Een veelvoorkomend voor beeld is een taak voor het kopiëren van bestanden naar een reken knooppunt. Een eenvoudige benadering is een taak waarmee alle opgegeven bestanden elke keer dat deze wordt uitgevoerd, wordt gekopieerd, wat inefficiënt is en niet is opgebouwd om de fout op te vangen. Maak in plaats daarvan een taak om ervoor te zorgen dat de bestanden zich op het reken knooppunt bevinden. een taak waarmee bestanden die al aanwezig zijn, niet opnieuw worden gekopieerd. Op deze manier wordt de taak opgehaald waar deze wordt afgebroken als deze is onderbroken.

- **Knoop punten met een lage prioriteit**  
    Er zijn geen ontwerp verschillen bij het uitvoeren van uw taken op knoop punten met specifieke of lage prioriteit. Of een taak al dan niet wordt uitgevoerd op een knoop punt met lage prioriteit of wordt onderbroken als gevolg van een storing op een toegewezen knoop punt, maar beide situaties worden mogelijk verholpen door de taak te ontwerpen en de fout te achterhalen.

- **Uitvoerings tijd van de taak**  
    Vermijd taken met korte uitvoerings tijd. Taken die slechts een tot twee seconden worden uitgevoerd, zijn niet ideaal. Probeer een aanzienlijke hoeveelheid werk uit te voeren in een afzonderlijke taak (mini maal 10 seconden, Maxi maal uur of dagen). Als elke taak gedurende één minuut (of meer) wordt uitgevoerd, is de plannings overhead als Fractie van de totale reken tijd klein.

## <a name="nodes"></a>Knooppunten

- **Start taken moeten worden idempotent**  
    Net als bij andere taken moet de begin taak van het knoop punt idempotent zijn, aangezien deze elke keer dat het knoop punt wordt opgestart opnieuw wordt uitgevoerd. Een idempotent-taak is slechts een, wat een consistent resultaat oplevert wanneer er meerdere keren worden uitgevoerd.

- **Beheer langlopende Services via de service-interface van het besturings systeem.**  
    Soms moet er een andere agent naast de batch-agent in het knoop punt worden uitgevoerd, bijvoorbeeld om gegevens van het knoop punt te verzamelen en te rapporteren. U wordt aangeraden deze agents als OS-Services te implementeren, zoals een Windows-service of een Linux `systemd` service.  

    Bij het uitvoeren van deze services mogen ze geen bestands vergrendeling hebben op bestanden in door batch beheerde directory's op het knoop punt, omdat in andere gevallen deze directory's niet kunnen worden verwijderd vanwege de vergren deling van het bestand. Als u bijvoorbeeld een Windows-service in een begin taak installeert in plaats van de service rechtstreeks vanuit de werkmap voor het starten van de taak te starten, kopieert u de bestanden ergens anders (als de bestanden alleen over de kopie staan). Installeer de service vanaf die locatie. Wanneer uw start taak door batch opnieuw wordt uitgevoerd, wordt de werkmap voor het starten van de taak verwijderd en opnieuw gemaakt. Dit werkt omdat de service Bestands vergrendelingen heeft op de andere map, niet op de werkmap voor het starten van de taak.

- **Vermijd het maken van adreslijst koppelingen in Windows**  
    Directory-koppelingen, ook wel vaste koppelingen naar mappen genoemd, zijn moeilijk te verwerken tijdens het opschonen van taken en taken. Gebruik symlinks (soft-koppelingen) in plaats van vaste koppelingen.

- **De batch Agent-logboeken verzamelen als er een probleem is**  
    Als u een probleem ondervindt met betrekking tot het gedrag van een knoop punt of taken die worden uitgevoerd op een knoop punt, is het raadzaam om de batch Agent-logboeken te verzamelen voordat u de betreffende knoop punten ongedaan maakt. U kunt de batch Agent-logboeken verzamelen met de API voor het uploaden van batch-service Logboeken. Deze logboeken kunnen worden verstrekt als onderdeel van een ondersteunings ticket voor micro soft en helpt u bij het oplossen van problemen en oplossingen.

## <a name="security"></a>Beveiliging

### <a name="security-isolation"></a>Beveiligings isolatie

Als voor uw scenario het isoleren van taken van elkaar is vereist, moet u deze taken isoleren door ze in afzonderlijke groepen te laten staan. Een pool is de grens van beveiligings isolatie in batch, en twee groepen zijn standaard niet zichtbaar of kunnen met elkaar communiceren. Vermijd het gebruik van afzonderlijke batch-accounts als isolatie methode.
