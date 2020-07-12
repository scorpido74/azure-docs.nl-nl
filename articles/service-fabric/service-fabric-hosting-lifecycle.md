---
title: Levens cyclus van Azure Service Fabric hosten voor activering en deactivering
description: Geeft een uitleg over de levens cyclus van toepassingen en ServicePackage op een knoop punt
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: b106061805ea5485893df292c40974d3ee9bcadb
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258819"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Levens cyclus van Azure Service Fabric-hosting
Dit artikel bevat een overzicht van gebeurtenissen die zich voordoen wanneer een toepassing wordt geactiveerd op een knoop punt en verschillende cluster configuraties die worden gebruikt voor het beheren van het gedrag.

Voordat u verder gaat, moet u de verschillende concepten en relaties begrijpen die worden uitgelegd in [model a Application in service Fabric][a1]. 

> [!NOTE]
> In dit artikel, tenzij expliciet vermeld als iets anders:
>
> - *Replica* verwijst naar een replica van een stateful service en een exemplaar van een stateless service.
> - *Code package* wordt behandeld als equivalent aan een *ServiceHost* -proces dat een *service*type registreert en replica's van services van die *service*type host.
>

## <a name="activation-of-applicationservicepackage"></a>Activering van toepassings-ServicePackage

De pijp lijn voor activering is als volgt:

1. Down load de ApplicationPackage. Bijvoorbeeld: ApplicationManifest.xml etc.
2. Stel een omgeving in voor de toepassing, bijvoorbeeld: gebruikers maken, enz.
3. Traceer toepassing starten voor deactivering.
4. Down load ServicePackage. Bijvoorbeeld: ServiceManifest.xml, code, configuratie, gegevens pakketten, enzovoort.
5. Omgeving instellen voor service pakket voor de installatie van de firewall, het toewijzen van poorten voor eind punten, enzovoort.
6. Start het bijhouden van ServicePackage voor deactivering.
7. Start SetupEntryPoint van CodePackages en wacht tot de bewerking is voltooid.
8. MainEntryPoint van CodePackages starten.

### <a name="servicetype-blocklisting"></a>Service type Blocklisting
**ServiceTypeDisableFailureThreshold** bepaalt het aantal fouten (activering, down loads, code package-fouten) waarna de service type is gepland voor blocklisting. De eerste keer dat de activering is mislukt, of code package vastloopt, wordt de planning van Service type blocklisting geactiveerd. De **ServiceTypeDisableGraceInterval** -configuratie bepaalt het interval van de respijt periode waarna Service type wordt ingesteld op blocklisted op dat knoop punt. Houd er rekening mee dat voor het uitvoeren van de activering/down load/code package opnieuw opstarten de modus voor opnieuw proberen intern moet blijven en wordt bijgehouden door het hosting subsysteem. Opnieuw proberen, bijvoorbeeld: code package wordt gepland om opnieuw te starten na het vastlopen of Service Fabric probeert pakketten opnieuw te downloaden.
Na blocklisted moet er een fout melding worden weer gegeven over de gerapporteerde fout ' System. hosting ' voor de eigenschap ' ServiceTypeRegistration: Service type '. Service type is uitgeschakeld op het knoop punt.

Service type wordt weer ingeschakeld op het knoop punt 
- Als de activerings bewerking is gelukt of **ActivationMaxFailureCount** nieuwe pogingen bij het mislukken worden bereikt.
- Als de Download bewerking is gelukt of **DeploymentMaxFailureCount** nieuwe pogingen bij een fout worden bereikt.
- Als een code package die is vastgelopen, back-ups maakt en de service type registreert.

De reden voor het inschakelen van de service type opnieuw na **ActivationMaxFailureCount** / **DeploymentMaxFailureCount** -pogingen is dat het maximum aantal pogingen service Fabric worden uitgevoerd om een toepassing op een knoop punt te activeren of te downloaden. Als dit niet lukt, wordt de huidige bewerking niet opnieuw uitgevoerd en omdat Service Fabric wil dat de service een andere mogelijkheid heeft om te activeren, wat kan leiden tot een probleem met de levens cyclus van de activerings-en download bewerking. Bij een nieuwe activering/Download bewerking die wordt geactiveerd door plaatsing van een replica, kan de service type-blocklisting opnieuw worden geactiveerd of slaagt.

> [!NOTE]
> Als uw code package waarop geen service type wordt geregistreerd, vastloopt, heeft dit geen invloed op de service type. Alleen de code package die als host fungeert voor een replica-crash, is van invloed op de service type.
>

### <a name="codepackage-crash"></a>Code package vastlopen
Wanneer een code package vastloopt, gebruikt Service Fabric een back-out om het opnieuw te starten en wordt de back-up onafhankelijk van het feit of het code pakket een type met ons heeft geregistreerd of niet.

De waarde voor de back-up is altijd min (RetryTime, **ActivationMaxRetryInterval**) en deze waarde kan constant, lineair of exponentiële zijn op basis van **ActivationRetryBackoffExponentiationBase** config.

- Constante: if **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = **ActivationRetryBackoffInterval**;
- Lineair: als **ActivationRetryBackoffExponentiationBase** = = 0 then RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** waarbij ContinousFailureCount het aantal keren is dat een code package vastloopt of niet kan worden geactiveerd.
- Exponentieel: RetryTime = (**ActivationRetryBackoffInterval** in seconden) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
U kunt het gedrag bepalen dat u wilt, zoals snel opnieuw opstarten. Over lineaire communicatie. Dit betekent dat als een code package vastloopt, het start interval na 10, 20, 30 40 sec tot de code package wordt gedeactiveerd. 
    
De maximale tijds duur Service Fabric back-ups maken (wacht) nadat een fout is onderhevig aan de **ActivationMaxRetryInterval**
    
Als uw code package vastloopt en er een back-up van wordt gemaakt, moet de IT-afdeling blijven voor **CodePackageContinuousExitFailureResetInterval** voor service Fabric om het te kunnen beschouwen als in orde waarbij het status rapport wordt overschreven als OK en de ContinousFailureCount opnieuw wordt ingesteld.

### <a name="codepackage-not-registering-servicetype"></a>Code package niet registreren voor service type
Als een code package blijft leven en naar verwachting een service type met ons registreert, maar nooit, wordt in dat Service Fabric geval een waarschuwing HealthReport gegenereerd nadat **ServiceTypeRegistrationTimeout** de melding dat Service type niet is geconfigureerd binnen de time-out.

### <a name="activation-failure"></a>Activerings fout
Service Fabric maakt altijd gebruik van een lineaire back-out (hetzelfde als code package-crash) wanneer deze een fout vindt tijdens de activering. Dit betekent dat de activerings bewerking na (0 + 10 + 20 + 30 + 40) = 100 SEC (eerste nieuwe poging onmiddellijk) wordt uitgevoerd. Nadat deze activering niet opnieuw is uitgevoerd.
    
De maximale activerings uitstel kan worden **ActivationMaxRetryInterval** en opnieuw worden **ActivationMaxFailureCount**.

### <a name="download-failure"></a>Fout bij het downloaden
Service Fabric maakt altijd gebruik van een lineaire back-up wanneer er een fout optreedt tijdens het downloaden. Dit betekent dat de activerings bewerking na (0 + 10 + 20 + 30 + 40) = 100 SEC (eerste nieuwe poging onmiddellijk) wordt uitgevoerd. Nadat deze down load niet opnieuw is uitgevoerd. De lineaire back-off voor down load is gelijk aan ContinuousFailureCount ***DeploymentRetryBackoffInterval** en kan Maxi maal worden teruggedraaid naar **DeploymentMaxRetryInterval**. Net als bij activeringen kan de Download bewerking opnieuw worden uitgevoerd voor **ActivationMaxFailureCount**.

> [!NOTE]
> Voordat u de configuratie wijzigt, zijn hier enkele voor beelden die u moet onthouden.

* Als de code package vastloopt en er back-ups worden verwijderd, wordt service type uitgeschakeld. Maar als de activerings configuratie zodanig is dat deze snel opnieuw wordt opgestart, kan de code package enkele keren worden bereikt voordat het uitschakelen van Service type kan worden weer geven. Bijvoorbeeld: aangenomen dat uw code package actief is, registreert u het Service type met Service Fabric en loopt vervolgens vast. In dat geval wordt de **ServiceTypeDisableGraceInterval** -periode geannuleerd zodra de hosting een type registratie heeft ontvangen. En dit kan worden herhaald totdat uw code package wordt teruggedraaid naar een waarde die groter is dan **ServiceTypeDisableGraceInterval** en vervolgens op Service type wordt uitgeschakeld op het knoop punt. Daarom kan het enige tijd duren voordat uw service type is uitgeschakeld op het knoop punt.

* Als Service Fabric systeem een replica in een knoop punt moet plaatsen, wordt in het geval van activeringen het hosting subsysteem door RA (ReconfigurationAgent) om de toepassing te activeren en wordt elke 15 sec (**RAPMessageRetryInterval**) opnieuw geprobeerd om de activerings aanvraag uit te voeren. Voor Service Fabric systeem om te weten dat Service type is uitgeschakeld, moet de activerings bewerking in de hosting gedurende een langere periode actief zijn dan het interval voor nieuwe pogingen en de **ServiceTypeDisableGraceInterval**. Bijvoorbeeld: laat het cluster de configuratie **ActivationMaxFailureCount** instellen op 5 en **ActivationRetryBackoffInterval** ingesteld op 1 sec. Dit betekent dat de activerings bewerking na (0 + 1 + 2 + 3 + 4) = 10 sec (de eerste nieuwe poging onmiddellijk) wordt uitgevoerd en dat de hosting het opnieuw probeert. In dit geval wordt de activerings bewerking voltooid en wordt na 15 seconden geen nieuwe poging gedaan. Er is een fout opgetreden omdat Service Fabric alle pogingen binnen 15 seconden uitgeput zijn. Bij elke nieuwe poging van ReconfigurationAgent wordt dus een nieuwe activerings bewerking gemaakt in het hosting subsysteem en het patroon blijft herhalen en service type wordt nooit uitgeschakeld op het knoop punt. Omdat het Service type niet wordt uitgeschakeld op het onderdeel FM van het node SF-systeem (FailoverManager), wordt de replica niet verplaatst naar een ander knoop punt.
> 

## <a name="deactivation"></a>Deactivering

Wanneer een ServicePackage wordt geactiveerd op een knoop punt, wordt deze bijgehouden voor deactivering. Deactivator is de entiteit die deze bijhoudt.
De deactiveering werkt op twee manieren:

1.  Regel matig: bij elke **DeactivationScanInterval**wordt gecontroleerd op ServicePackages, die nooit een replica hebben gehost en deze markeert als kandidaten voor deactivering.
2.  ReplicaClose: als een replica is gesloten, haalt Deactivator een DecrementUsageCount op. Als de telling wordt ingesteld op 0, betekent dit dat de ServicePackage geen replica host en daarom een kandidaat is voor deactivering.

 Op basis van de activerings modus [exclusief/gedeeld][a2], worden de kandidaten voor deactivering gepland na **DeactivationGraceInterval** voor SharedMode/ **ExclusiveModeDeactivationGraceInterval** voor ExclusiveMode. Als in tussen deze tijd een nieuwe replica-plaatsing wordt opgehaald, wordt de deactivering geannuleerd.

### <a name="periodically"></a>Periodieke
Voor beeld 1: Stel dat het Deactiveer een scan op tijdstip T (**DeactivationScanInterval**) uitvoert. De volgende scan is op 2T. Stel dat er een ServicePackage-activering is opgetreden op T + 1. Deze ServicePackage is geen host van een replica en moet daarom worden gedeactiveerd. De ServicePackage moet een kandidaat zijn voor deactivering, maar moet in de staat van geen replica zijn voor ten minste een T/m tijd. Dit betekent dat deze in aanmerking komt voor deactivering op 2T + 1. De scan op 2T vindt deze ServicePackage dus niet als een kandidaat voor deactivering. Bij de volgende deactiverings cyclus 3T gebruiken wordt deze ServicePackage gepland voor het deactiveren van de activering omdat er nu geen replica status is ingesteld voor tijd T.  

Voor beeld 2: Stel dat een ServicePackage wordt geactiveerd op het moment van T-1 en het deactiveren van een scan op T. De ServicePackage host geen replica. Vervolgens wordt bij de volgende scan 2T deze ServicePackage als een kandidaat voor deactiveren gevonden en daarom gepland voor deactivering.  

Voor beeld 3: Stel dat een ServicePackage wordt geactiveerd op T – 1 en dat deactiveert een scan op T. De ServicePackage host nog geen replica. Nu op T + 1 wordt een replica geplaatst, dat wil zeggen Hosting haalt een IncrementUsageCount op, wat betekent dat er een replica is gemaakt. Nu kunt u 2T deze ServicePackage niet plannen voor deactivering. De deactivering wordt nu verplaatst naar de ReplicaClose-logica die hieronder wordt uitgelegd.

Voor beeld 4: Stel dat uw ServicePackage groot is, zoals 10 GB, het kan even duren voordat het is gedownload op het knoop punt. Zodra een toepassing is geactiveerd, wordt de levens cyclus door de Deactivator bijgehouden. Als u nu de **DeactivationScanInterval** config Small hebt, kunt u problemen ondervinden waarbij uw ServicePackage geen tijd krijgt om te activeren op het knoop punt, omdat de tijd voor het downloaden is overgegaan. Om het probleem op te lossen, kunt u [de ServicePackage vooraf downloaden van het knoop punt][p1]. 

> [!NOTE]
> Een ServicePackage kan dus overal worden gedeactiveerd tussen (**DeactivationScanInterval** naar 2 ***DeactivationScanInterval**) + **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 
>

### <a name="replica-close"></a>Replica sluiten:
Deactivator houdt het aantal Replica's bij dat een ServicePackage bevat. Als een ServicePackage een replica houdt en de replica is gesloten/uitgeschakeld, haalt host een DecrementUsageCount op. Wanneer een replica wordt geopend, wordt er een IncrementUsageCount door gehost. Het verlagen houdt in dat de ServicePackage nu als host fungeert voor een minder replica en wanneer het aantal wordt neergezet op 0, de ServicePackage is gepland voor deactivering en de tijd waarna deze wordt gedeactiveerd, wordt **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 

Bijvoorbeeld: laten we zeggen dat een verlaging plaatsvindt op T en ServicePackage is gepland om te deactiveren op 2T + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**). Als tijdens deze tijd hosting een IncrementUsage wordt opgehaald, wat betekent dat er een replica wordt gemaakt, wordt de deactivering geannuleerd.

> [!NOTE]
>Wat betekent deze configuratie eigenlijk: **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: de tijd die aan een ServicePackage wordt toegewezen om opnieuw een andere replica te hosten wanneer deze een replica heeft gehost. 
**DeactivationScanInterval**: de minimale tijd die aan ServicePackage wordt gegeven voor het hosten van een replica als deze nooit een replica heeft gehost, d.w.z. Als deze niet wordt gebruikt.
>

### <a name="ctrlc"></a>Ctrl+C
Nadat een ServicePackage de **DeactivationGraceInterval** / -**ExclusiveModeDeactivationGraceInterval** heeft door gegeven en nog steeds geen host is van een replica, kan de deactivering niet worden geannuleerd. Code package worden een CTRL + C-handler uitgegeven. Dit betekent dat u de pijp lijn voor deactivering moet door lopen om het proces uit te voeren. Als er gedurende deze tijd een nieuwe replica voor dezelfde ServicePackage wordt opgehaald, mislukt dit omdat er geen overgang kan worden uitgevoerd van deactivering naar activering.

## <a name="cluster-configs"></a>Cluster configuraties

Configuraties met de standaard instellingen die van invloed zijn op de activerings-decativation.

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**: standaard 1. De drempel waarde voor het aantal mislukte pogingen waarna FM (FailoverManager) wordt gewaarschuwd om het Service type op dat knoop punt uit te scha kelen en een ander knoop punt voor plaatsing te proberen.
**ServiceTypeDisableGraceInterval**: standaard 30 sec. tijds interval waarna het Service type kan worden uitgeschakeld.
**ServiceTypeRegistrationTimeout**: standaard 300 sec. De time-out voor registratie bij Service Fabric van het Service type.

### <a name="activation"></a>Activering
**ActivationRetryBackoffInterval**: standaard 10 sec. uitstel-interval bij elke activerings fout.
**ActivationMaxFailureCount**: standaard 20. Maximum aantal waarvoor het systeem opnieuw moet worden geactiveerd voordat de activering wordt uitgevoerd. 
**ActivationRetryBackoffExponentiationBase**: standaard 1,5.
**ActivationMaxRetryInterval**: standaard 3600 sec. Maximale back-out voor activering bij fouten.
**CodePackageContinuousExitFailureResetInterval**: standaard 300 sec. De time-out voor het opnieuw instellen van het aantal doorlopende afsluit fouten voor code package.

### <a name="download"></a>Downloaden
**DeploymentRetryBackoffInterval**: standaard 10. Back-outinterval voor de implementatie fout.
**DeploymentMaxRetryInterval**: standaard 3600 sec. maximum aantal back-ups voor de implementatie op fouten.
**DeploymentMaxFailureCount**: standaard 20. De implementatie van de toepassing wordt opnieuw geprobeerd voor DeploymentMaxFailureCount tijden voordat de implementatie van die toepassing op het knoop punt is mislukt.

### <a name="deactivation"></a>Deactivering
**DeactivationScanInterval**: standaard 600 sec. de minimale tijd die aan ServicePackage is gegeven voor het hosten van een replica als deze nooit een replica heeft gehost, d.w.z. Als deze niet wordt gebruikt.
**DeactivationGraceInterval**: standaard 60 sec. De tijd die aan een ServicePackage is toegewezen voor het hosten van een andere replica zodra deze een replica heeft gehost in het geval van een **gedeeld** proces model.
**ExclusiveModeDeactivationGraceInterval**: standaard 1 sec. De tijd die aan een ServicePackage is toegewezen voor het hosten van een andere replica zodra deze een replica in het geval van een **exclusief** proces model heeft gehost.

## <a name="next-steps"></a>Volgende stappen
[Een toepassing inpakken][a3] en deze voorbereiden om te implementeren.

[Toepassingen implementeren en verwijderen][a4]. In dit artikel wordt beschreven hoe u Power shell gebruikt voor het beheren van toepassings exemplaren.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
