---
title: Toepassingsupgrades oplossen
description: In dit artikel worden enkele veelvoorkomende problemen met betrekking tot het upgraden van een Service Fabric-toepassing en hoe deze kunnen worden opgelost.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377919"
---
# <a name="troubleshoot-application-upgrades"></a>Problemen met toepassingsupgrades oplossen

In dit artikel worden enkele veelvoorkomende problemen met betrekking tot het upgraden van een Azure Service Fabric-toepassing en hoe deze kunnen worden opgelost.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Een mislukte toepassingsupgrade oplossen

Wanneer een upgrade mislukt, bevat de uitvoer van de opdracht **Get-ServiceFabricApplicationUpgrade** aanvullende informatie voor het opsporen van de fout.  In de volgende lijst wordt aangegeven hoe de aanvullende informatie kan worden gebruikt:

1. Identificeer het type fout.
2. Identificeer de reden van de fout.
3. Isoleer een of meer falende componenten voor verder onderzoek.

Deze informatie is beschikbaar wanneer Service Fabric de fout detecteert, ongeacht of de **FailureAction** de upgrade moet terugdraaien of opschorten.

### <a name="identify-the-failure-type"></a>Het type fout identificeren

In de uitvoer van **Get-ServiceFabricApplicationUpgrade**identificeert **FailureTimestampUtc** de tijdstempel (in UTC) waarbij een upgradefout is gedetecteerd door Service Fabric en **FailureAction** is geactiveerd. **FailureReason** identificeert een van de drie potentiële oorzaken op hoog niveau van de storing:

1. UpgradeDomainTimeout - Geeft aan dat het te lang duurde voordat een bepaald upgradedomein is voltooid en **dat UpgradeDomainTimeout** is verlopen.
2. OverallUpgradeTimeout - Geeft aan dat de algehele upgrade te lang duurde om te voltooien en **upgradetimeout** is verlopen.
3. HealthCheck - Geeft aan dat na het upgraden van een updatedomein de toepassing ongezond is gebleven volgens het opgegeven gezondheidsbeleid en **dat HealthCheckRetryTimeout** is verlopen.

Deze vermeldingen worden alleen weergegeven in de uitvoer wanneer de upgrade mislukt en begint terug te rollen. Meer informatie wordt weergegeven, afhankelijk van het type van de fout.

### <a name="investigate-upgrade-timeouts"></a>Upgradetime-outs onderzoeken

Fouten in de time-out van de upgrade worden meestal veroorzaakt door problemen met de beschikbaarheid van services. De uitvoer na deze alinea is typerend voor upgrades waarbij servicereplica's of -exemplaren niet worden gestart in de nieuwe codeversie. In het veld **UpgradeDomainProgressAtFailure** wordt een momentopname van een upgradein behandeling vastgelegd op het moment van de storing.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

In dit voorbeeld is de upgrade mislukt bij upgradedomein *MYUD1* en twee partities (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* en *4b43f4d8-b26b-424e-9307-7a7a62e79750*) bleven steken. De partities bleven vastzitten omdat de runtime geen primaire replica's *(WaitForPrimaryPlacement)* op doelknooppunten *Node1* en *Node4*kan plaatsen.

De opdracht **Get-ServiceFabricNode** kan worden gebruikt om te controleren of deze twee knooppunten zich in het upgradedomein *MYUD1 bevinden.* De *UpgradePhase* zegt *PostUpgradeSafetyCheck*, wat betekent dat deze veiligheidscontroles plaatsvinden nadat alle knooppunten in het upgradedomein klaar zijn met upgraden. Al deze informatie wijst op een mogelijk probleem met de nieuwe versie van de toepassingscode. De meest voorkomende problemen zijn servicefouten in de open of promotie naar primaire codepaden.

Een *upgradefase* van *PreUpgradeSafetyCheck* betekent dat er problemen waren met de voorbereiding van het upgradedomein voordat het werd uitgevoerd. De meest voorkomende problemen in dit geval zijn servicefouten in de afsluiting of degradatie van primaire codepaden.

De huidige **UpgradeState** is *RollingBackCompleted,* dus de oorspronkelijke upgrade moet zijn uitgevoerd met een rollback **FailureAction**, die automatisch teruggedraaid van de upgrade bij mislukking. Als de oorspronkelijke upgrade is uitgevoerd met een handmatige **FailureAction,** dan is de upgrade in plaats daarvan in een opgeschorte staat om live foutopsporing van de toepassing mogelijk te maken.

In zeldzame gevallen kan het veld **UpgradeDomainProgressAtFailure** leeg zijn als de algehele upgradetijden uitvallen net zoals het systeem alle werkzaamheden voor het huidige upgradedomein voltooit. Als dit gebeurt, probeert u de parameterwaarden voor upgrade-upgradevan **UpgradeTimeout** en **UpgradeDomainTimeout** opnieuw en probeert u de upgrade opnieuw.

### <a name="investigate-health-check-failures"></a>Fouten in de statuscontrole onderzoeken

Fouten in de statuscontrole kunnen worden geactiveerd door verschillende problemen die kunnen optreden nadat alle knooppunten in een upgradedomein zijn bijgewerkt en alle veiligheidscontroles kunnen doorstaan. De uitvoer na deze alinea is typerend voor een upgradefout als gevolg van mislukte statuscontroles. In het veld **Ongezonde evaluaties** wordt een momentopname van statuscontroles vastgelegd die zijn mislukt op het moment van de upgrade volgens het opgegeven [gezondheidsbeleid](service-fabric-health-introduction.md).

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Voor het onderzoeken van fouten in de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status van de status Maar zelfs zonder een dergelijk diepgaand begrip, kunnen we zien dat twee diensten ongezond zijn: *stof:/DemoApp/Svc3* en *stof:/DemoApp/Svc2*, samen met de foutgezondheidsrapporten ("InjectedFault" in dit geval). In dit voorbeeld zijn twee van de vier services ongezond, wat lager is dan de standaarddoelstelling van 0% ongezonde *(MaxPercentUnhealthyServices).*

De upgrade werd opgeschort bij het mislukken door het opgeven van een **FailureAction** van de handleiding bij het starten van de upgrade. Deze modus stelt ons in staat om het live systeem in de mislukte toestand te onderzoeken voordat we verdere actie ondernemen.

### <a name="recover-from-a-suspended-upgrade"></a>Herstellen van een opgeschorte upgrade

Met een rollback **FailureAction**, is er geen herstel nodig, omdat de upgrade automatisch terug rolt bij het mislukken. Met een handmatige **FailureAction**zijn er verschillende herstelopties:

1.  een terugdraaien activeren
2. Doorgaan met de rest van de upgrade handmatig
3. De bewaakte upgrade hervatten

De opdracht **Start-ServiceFabricApplicationRollback** kan op elk gewenst moment worden gebruikt om de toepassing terug te draaien. Zodra de opdracht met succes wordt geretourneerd, is de terugdraaiaanvraag geregistreerd in het systeem en wordt het kort daarna gestart.

De opdracht **Cv-ServiceFabricApplicationUpgrade** kan worden gebruikt om de rest van de upgrade handmatig door te nemen, één upgradedomein tegelijk. In deze modus worden alleen veiligheidscontroles uitgevoerd door het systeem. Er worden geen gezondheidscontroles meer uitgevoerd. Deze opdracht kan alleen worden gebruikt wanneer de *upgradestate* *RollingForwardPending*weergeeft, wat betekent dat het huidige upgradedomein is voltooid met upgraden, maar de volgende is niet gestart (in behandeling).

De opdracht **Update-ServiceFabricApplicationUpgrade** kan worden gebruikt om de bewaakte upgrade te hervatten, waarbij zowel veiligheids- als gezondheidscontroles worden uitgevoerd.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

De upgrade gaat verder vanuit het upgradedomein waar het voor het laatst is opgeschort en gebruikt dezelfde upgradeparameters en het gezondheidsbeleid als voorheen. Indien nodig kan een van de upgradeparameters en het gezondheidsbeleid in de vorige uitvoer in dezelfde opdracht worden gewijzigd wanneer de upgrade wordt hervat. In dit voorbeeld is de upgrade hervat in de monitormodus, waarbij de parameters en het gezondheidsbeleid ongewijzigd zijn gebleven.

## <a name="further-troubleshooting"></a>Verdere probleemoplossing

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric volgt het opgegeven gezondheidsbeleid niet

Mogelijke oorzaak 1:

Service Fabric vertaalt alle percentages naar werkelijke aantallen entiteiten (bijvoorbeeld replica's, partities en services) voor statusbeoordeling en rondt altijd af op hele entiteiten. Als de maximale *MaxPercentUnhealthyReplicasPerPartition* bijvoorbeeld 21% is en er vijf replica's zijn, staat Service`Math.Ceiling (5*0.21)`Fabric maximaal twee ongezonde replica's toe (dat wil zeggen). Daarom moet het gezondheidsbeleid dienovereenkomstig worden vastgesteld.

Mogelijke oorzaak 2:

Het gezondheidsbeleid wordt opgegeven in termen van percentages van de totale services en niet in specifieke service-exemplaren. Als een toepassing bijvoorbeeld vóór een upgrade vier service-exemplaren A, B, C en D heeft, waarbij service D niet in orde is, maar weinig invloed heeft op de toepassing. We willen de bekende ongezonde service D negeren tijdens de upgrade en de parameter *MaxPercentUnhealthyServices* instellen op 25%, ervan uitgaande dat alleen A, B en C gezond moeten zijn.

Echter, tijdens de upgrade, D kan gezond worden, terwijl C wordt ongezond. De upgrade zou nog steeds slagen omdat slechts 25% van de diensten ongezond zijn. Echter, het kan resulteren in onverwachte fouten als gevolg van C wordt onverwacht ongezond in plaats van D. In deze situatie moet D worden gemodelleerd als een ander servicetype dan A, B en C. Aangezien het gezondheidsbeleid per servicetype is opgegeven, kunnen verschillende ongezonde percentagedrempels worden toegepast op verschillende services. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Ik heb geen gezondheidsbeleid opgegeven voor het upgraden van toepassingen, maar de upgrade mislukt nog steeds voor een aantal time-outs die ik nooit heb opgegeven

Wanneer het gezondheidsbeleid niet wordt verstrekt aan de upgradeaanvraag, worden deze overgenomen uit de *toepassingsversie applicationManifest.xml.* Als u bijvoorbeeld Toepassing X upgradet van versie 1.0 naar versie 2.0, wordt toepassingsstatusbeleid gebruikt waarvoor in versie 1.0 is opgegeven. Als een ander gezondheidsbeleid moet worden gebruikt voor de upgrade, moet het beleid worden opgegeven als onderdeel van de API-aanroep voor toepassingsverbetering. Het beleid dat is opgegeven als onderdeel van de API-aanroep, is alleen van toepassing tijdens de upgrade. Zodra de upgrade is voltooid, worden de beleidsregels gebruikt die zijn opgegeven in *applicationmanifest.xml.*

### <a name="incorrect-time-outs-are-specified"></a>Onjuiste time-outs zijn opgegeven

Je hebt je misschien afgevraagd wat er gebeurt als time-outs inconsistent zijn ingesteld. U bijvoorbeeld een *upgradetime-out* hebben die kleiner is dan de *UpgradeDomainTimeout.* Het antwoord is dat een fout wordt geretourneerd. Fouten worden geretourneerd als de *UpgradeDomainTimeout* kleiner is dan de som *van HealthCheckWaitDuration* en *HealthCheckRetryTimeout*of als *UpgradeDomainTimeout* kleiner is dan de som *van HealthCheckWaitDuration* en *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Mijn upgrades duren te lang

De tijd die een upgrade moet voltooien, is afhankelijk van de opgegeven statuscontroles en time-outs. Statuscontroles en time-outs zijn afhankelijk van hoe lang het duurt om de toepassing te kopiëren, implementeren en stabiliseren. Te agressief zijn met time-outs kan meer mislukte upgrades betekenen, dus we raden aan om conservatief te beginnen met langere time-outs.

Hier vindt u een snelle opfriscursus over hoe de time-outs omgaan met de upgradetijden:

Upgrades voor een upgradedomein kunnen niet sneller worden voltooid dan *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Upgradefout kan niet sneller optreden dan *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

De upgradetijd voor een upgradedomein wordt beperkt door *UpgradeDomainTimeout.*  Als *HealthCheckRetryTimeout* en *HealthCheckStableDuration* zowel niet-nul zijn als de status van de toepassing heen en weer blijft schakelen, wordt de upgrade uiteindelijk opnieuw getimed op *UpgradeDomainTimeout.* *UpgradeDomainTimeout* begint af te tellen zodra de upgrade voor het huidige upgradedomein begint.

## <a name="next-steps"></a>Volgende stappen

[Upgraden van uw toepassing Met Visual Studio](service-fabric-application-upgrade-tutorial.md) u een applicatie-upgrade doorlopen met Visual Studio.

[Het upgraden van uw toepassing met Powershell](service-fabric-application-upgrade-tutorial-powershell.md) leidt u door een applicatie-upgrade met PowerShell.

Bepaal hoe uw toepassing wordt bijgewerkt met [upgradeparameters.](service-fabric-application-upgrade-parameters.md)

Maak uw toepassingsupgrades compatibel door te leren hoe [u Data Serialization kunt](service-fabric-application-upgrade-data-serialization.md)gebruiken.

Meer informatie over het gebruik van geavanceerde functionaliteit tijdens het upgraden van uw toepassing door te verwijzen naar [geavanceerde onderwerpen.](service-fabric-application-upgrade-advanced.md)