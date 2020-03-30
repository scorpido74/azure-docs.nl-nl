---
title: Lijst met gebeurtenislijst azure-servicefabric
description: Uitgebreide lijst met gebeurtenissen die door Azure Service Fabric worden geleverd om clusters te controleren.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258536"
---
# <a name="list-of-service-fabric-events"></a>Lijst met servicefabric-gebeurtenissen 

Service Fabric onthult een primaire set clustergebeurtenissen om u te informeren over de status van uw cluster als [Service Fabric-gebeurtenissen.](service-fabric-diagnostics-events.md) Deze zijn gebaseerd op acties die door Service Fabric op uw knooppunten worden uitgevoerd en uw cluster- of beheerbeslissingen die zijn genomen door een clustereigenaar/operator. Deze gebeurtenissen kunnen worden geopend door op een aantal manieren te configureren, waaronder het configureren van [Azure Monitor-logboeken met uw cluster](service-fabric-diagnostics-oms-setup.md)of het opvragen van de [EventStore.](service-fabric-diagnostics-eventstore.md) Op Windows-machines worden deze gebeurtenissen in de EventLog ingevoerd, zodat u Service Fabric-gebeurtenissen in logboeken zien. 

Hier zijn enkele kenmerken van deze gebeurtenissen
* Elke gebeurtenis is gekoppeld aan een specifieke entiteit in het cluster, bijvoorbeeld Toepassing, Service, Knooppunt, Replica.
* Elke gebeurtenis bevat een set algemene velden: EventInstanceId, EventName en Categorie.
* Elke gebeurtenis bevat velden die de gebeurtenis koppelen aan de entiteit waaraan de gebeurtenis is gekoppeld. De gebeurtenis ApplicationCreated heeft bijvoorbeeld velden die de naam van de aangemaakte toepassing identificeren.
* Gebeurtenissen zijn zo gestructureerd dat ze kunnen worden geconsumeerd in een verscheidenheid van tools om verdere analyse te doen. Bovendien worden relevante details voor een gebeurtenis gedefinieerd als afzonderlijke eigenschappen in tegenstelling tot een lange tekenreeks. 
* Gebeurtenissen worden geschreven door verschillende subsystemen in Service Fabric worden hieronder geïdentificeerd door Source(Task). Meer informatie is beschikbaar over deze subsystemen in [Service Fabric Architecture](service-fabric-architecture.md) and Service Fabric Technical [Overview.](service-fabric-technical-overview.md)

Hier is een lijst van deze Service Fabric-evenementen georganiseerd door entiteit.

## <a name="cluster-events"></a>Clustergebeurtenissen

**Clusterupgradegebeurtenissen**

Meer details over cluster upgrades vindt u [hier.](service-fabric-cluster-upgrade-windows-server.md)

| EventId (EventId) | Name | Categorie | Beschrijving |Bron (taak) | Niveau | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradegestart | Upgraden | Er is een clusterupgrade gestart | CM | Informatief |
| 29628 | ClusterUpgradevoltooid | Upgraden | Een clusterupgrade is voltooid | CM | Informatief | 
| 29629 | ClusterUpgradeRollbackStarted | Upgraden | Er is een clusterupgrade gestart om terug te draaien  | CM | Waarschuwing | 
| 29630 | ClusterUpgradeRollbackVoltooid | Upgraden | Een clusterupgrade is voltooid | CM | Waarschuwing | 
| 29631 | ClusterUpgradeDomainCompleted | Upgraden | Een upgradedomein is klaar met upgraden tijdens een clusterupgrade | CM | Informatief | 

## <a name="node-events"></a>Knooppuntgebeurtenissen

**Gebeurtenissen in de levenscyclus van knooppunten** 

| EventId (EventId) | Name | Categorie | Beschrijving |Bron (taak) | Niveau |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateVoltooid | StatusOvergang | Deactivering van een knooppunt is voltooid | FM | Informatief | 
| 18603 | Knooppunt Up | StatusOvergang | Het cluster heeft gedetecteerd dat er een knooppunt is opgestart | FM | Informatief | 
| 18604 | Knooppunt Down | StatusOvergang | Het cluster heeft gedetecteerd dat er een knooppunt is afgesloten. Tijdens een knooppuntopnieuw opstarten ziet u een NodeDown-gebeurtenis gevolgd door een NodeUp-gebeurtenis |  FM | Fout | 
| 18605 | NodeAddedToCluster | StatusOvergang |  Er is een nieuw knooppunt toegevoegd aan het cluster en Service Fabric kan toepassingen implementeren op dit knooppunt | FM | Informatief | 
| 18606 | NodeRemovedFromCluster | StatusOvergang |  Er is een knooppunt uit het cluster verwijderd. Service Fabric implementeert geen toepassingen meer op dit knooppunt | FM | Informatief | 
| 18607 | NodeDeactivateStarted | StatusOvergang |  Deactivering van een knooppunt is begonnen | FM | Informatief | 
| 25621 | NodeOpenGeslaagd | StatusOvergang |  Een knooppunt is succesvol gestart | FabricNode | Informatief | 
| 25622 | NodeOpenMislukt | StatusOvergang |  Een knooppunt kan de ring niet starten en lid worden | FabricNode | Fout | 
| 25624 | Knooppunt Gesloten | StatusOvergang |  Een knooppunt dat is afgesloten | FabricNode | Informatief | 
| 25626 | Knooppunt Afgebroken | StatusOvergang |  Een knooppunt is op onsierlijke wijze afgesloten | FabricNode | Fout | 

## <a name="application-events"></a>Toepassingsgebeurtenissen

**Gebeurtenissen in de levenscyclus van toepassingen**

| EventId (EventId) | Name | Categorie | Beschrijving |Bron (taak) | Niveau | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | Toepassinggemaakt | Levenscyclus | Er is een nieuwe toepassing gemaakt | CM | Informatief | 
| 29625 | Toepassing Verwijderd | Levenscyclus | Een bestaande toepassing is verwijderd | CM | Informatief | 
| 23083 | ToepassingsprocesUitgesloten | Levenscyclus | Een proces binnen een toepassing is afgesloten | Hosting | Informatief | 

**Toepassingsupgradegebeurtenissen**

Meer details over applicatie-upgrades vindt u [hier.](service-fabric-application-upgrade.md)

| EventId (EventId) | Name | Categorie | Beschrijving |Bron (taak) | Niveau | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Upgraden | Er is een upgrade gestart voor een toepassing | CM | Informatief | 
| 29622 | Toepassingsupgradevoltooid | Upgraden | Een upgrade van een toepassing is voltooid | CM | Informatief | 
| 29623 | ApplicationUpgradeRollbackStarted | Upgraden | Een upgrade van een toepassing is begonnen met terugdraaien |CM | Waarschuwing | 
| 29624 | ToepassingsUpgradeRollbackVoltooid | Upgraden | Een upgrade van een toepassing is voltooid | CM | Waarschuwing | 
| 29626 | ApplicationUpgradeDomainCompleted | Upgraden | Een upgradedomein is klaar met upgraden tijdens een toepassingsupgrade | CM | Informatief | 

## <a name="service-events"></a>Servicegebeurtenissen

**Gebeurtenissen in de levenscyclus van services**

| EventId (EventId) | Name | Categorie | Beschrijving |Bron (taak) | Niveau | 
| --- | --- | ---| --- | --- | --- |
| 18657 | Servicegemaakt | Levenscyclus | Er is een nieuwe service gemaakt | FM | Informatief | 
| 18658 | ServiceVerwijderd | Levenscyclus | Een bestaande service is verwijderd | FM | Informatief | 

## <a name="partition-events"></a>Partitiegebeurtenissen

**Gebeurtenissen voor het verplaatsen van partities**

| EventId (EventId) | Name | Categorie | Beschrijving |Bron (taak) | Niveau | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Levenscyclus | Een partitieherconfiguratie is voltooid | RA | Informatief | 

## <a name="replica-events"></a>Replicagebeurtenissen

**Gebeurtenissen over de levenscyclus van replica's**

| EventId (EventId) | Name | Categorie | Beschrijving |Bron (taak) | Niveau |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened ReliableDictionaryOpened ReliableDictionaryOpened ReliableDictionary | Levenscyclus | Betrouwbaar woordenboek is geopend | GedistribueerdWoordenboek | Informatief |
| 61702 | ReliableDictionaryClosed ReliableDictionaryClosed ReliableDictionaryClosed ReliableDictionary | Levenscyclus | Betrouwbaar woordenboek is gesloten | GedistribueerdWoordenboek | Informatief |
| 61703 | ReliableDictionaryCheckpointRecovered ReliableDictionaryCheckpointRecovered ReliableDictionaryCheckpointRecovered ReliableDictionary | Levenscyclus | Betrouwbaar woordenboek heeft zijn controlepunt hersteld | GedistribueerdWoordenboek | Informatief |
| 61704 | ReliableDictionaryCheckpointFilesSent ReliableDictionaryCheckpoint | Levenscyclus | Replica heeft betrouwbare woordenboek checkpoint bestanden verzonden | GedistribueerdWoordenboek | Informatief |
| 61705 | ReliableDictionaryCheckpointFilesOntvangen ReliableDictionary | Levenscyclus | Replica heeft betrouwbare woordenboek checkpoint bestanden ontvangen | GedistribueerdWoordenboek | Informatief |
| 61963 | ReliableQueueGeopend | Levenscyclus | Betrouwbare wachtrij is geopend | Gedistribueerde wachtrij | Informatief |
| 61964 | ReliableQueueGesloten | Levenscyclus | Betrouwbare wachtrij is gesloten | Gedistribueerde wachtrij | Informatief |
| 61965 | ReliableQueueCheckpointRecovered ReliableQueueCheckpointRecovered ReliableQueueCheckpointRecovered ReliableQueue | Levenscyclus | Betrouwbare wachtrij heeft zijn controlepunt hersteld | Gedistribueerde wachtrij | Informatief |
| 61966 | ReliableQueueCheckpointFilesSent ReliableQueueCheckpointFilesSent ReliableQueueCheckpointFilesSent ReliableQueue | Levenscyclus | Replica heeft betrouwbare controlepuntenbestanden van wachtrijen verzonden | Gedistribueerde wachtrij | Informatief |
| 63647 | ReliableQueueCheckpointFilesOntvangen | Levenscyclus | Replica heeft betrouwbare controlepuntenbestanden van wachtrijen ontvangen | Gedistribueerde wachtrij | Informatief |
| 63648 | ReliableConcurrentQueuegeopend | Levenscyclus | Betrouwbare gelijktijdige wachtrij is geopend | ReliableConcurrentQueue ReliableConcurrentQueue ReliableConcurrentQueue ReliableConcurrent | Informatief |
| 63649 | ReliableConcurrentQueuegesloten | Levenscyclus | Betrouwbare gelijktijdige wachtrij is gesloten | ReliableConcurrentQueue ReliableConcurrentQueue ReliableConcurrentQueue ReliableConcurrent | Informatief |
| 63650 | ReliableConcurrentQueueCheckpointrecovered ReliableConcurrentQueueCheckpointRecovered ReliableConcurrentQueueCheckpointRecovered ReliableConcurrent | Levenscyclus | Betrouwbare gelijktijdige wachtrij heeft zijn controlepunt hersteld | ReliableConcurrentQueue ReliableConcurrentQueue ReliableConcurrentQueue ReliableConcurrent | Informatief |
| 61687 | TStoreFout | Fout | Betrouwbare verzameling heeft een onverwachte fout ontvangen | TStore (TStore) | Fout |
| 63831 | PrimairFullCopy-geïnitieerd | Levenscyclus | Primaire replica heeft een volledige kopie gestart | TReplicator TReplicator | Informatief |
| 63832 | Primaire Partiële Kopiegeïnitieerd | Levenscyclus | Primaire replica heeft een gedeeltelijke kopie gestart | TReplicator TReplicator | Informatief |
| 16831 | BuildIdleReplicaStarted | Levenscyclus | Primaire replica is begonnen met het bouwen van niet-actieve replica | Replicatie | Informatief |
| 16832 | BuildIdleReplicaVoltooid | Levenscyclus | Primaire replica heeft voltooide gebouw niet-actieve replica | Replicatie | Informatief |
| 16833 | BuildIdleReplica mislukt | Levenscyclus | Primaire replica is mislukt gebouw niet-actieve replica | Replicatie | Waarschuwing |
| 16834 | PrimaryReplicatiewachtrijvolledig | Status | De replicatiewachtrij van de primaire replica is vol | Replicatie | Waarschuwing |
| 16835 | PrimaireReplicatiewachtrijwaarschuwing | Status | De replicatiewachtrij van primaire replica is bijna vol | Replicatie | Waarschuwing |
| 16836 | PrimaireReplicatieWachtrijWaarschuwingbeperkt | Status | De replicatiewachtrij van primaire replica is in orde | Replicatie | Informatief |
| 16837 | SecondaryReplicationQueueFull | Status | De replicatiewachtrij van secundaire replica is vol | Replicatie | Waarschuwing |
| 16838 | Secundairreplicatiewachtrijwaarschuwing | Status | De replicatiewachtrij van secundaire replica is bijna vol | Replicatie | Waarschuwing |
| 16839 | SecundairreplicatiewachtrijWaarschuwingbeperkt | Status | De replicatiewachtrij van secundaire replica is in orde | Replicatie | Informatief |
| 16840 | PrimaryFaultedSlowSecondary | Status | Primaire replica heeft een langzame secundaire replica verweten | Replicatie | Waarschuwing |
| 16841 | ReplicatorFaulted | Status | Replica heeft fouten gemaakt | Replicatie | Waarschuwing |

## <a name="container-events"></a>Containergebeurtenissen

**Gebeurtenissen over de levenscyclus van containers** 

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerGeactiveerd | Er is een container gestart | Hosting | Informatief | 1 |
| 23075 | ContainerGedeactiveerd | Een container is gestopt | Hosting | Informatief | 1 |
| 23082 | ContainerExited | Een container is afgesloten - Controleer de vlag UnexpectedTermination | Hosting | Informatief | 1 |

## <a name="health-reports"></a>Gezondheidsrapporten

Het [Service Fabric Health Model](service-fabric-health-introduction.md) biedt een rijke, flexibele en uitbreidbare gezondheidsevaluatie en rapportage. Vanaf Service Fabric versie 6.2, gezondheidsgegevens is geschreven als Platform gebeurtenissen om historische records van de gezondheid te bieden. Om het aantal gezondheidsgebeurtenissen laag te houden, schrijven we alleen het volgende als Service Fabric-gebeurtenissen:

* Alle `Error` `Warning` of gezondheidsrapporten
* `Ok`gezondheidsrapporten tijdens overgangen
* Wanneer `Error` een `Warning` of gezondheidsgebeurtenis verloopt. Dit kan worden gebruikt om te bepalen hoe lang een entiteit ongezond is

**Clusterstatusrapportgebeurtenissen**

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Er is een nieuw clusterstatusrapport beschikbaar | Hm | Informatief | 1 |
| 54437 | ClusterHealthReportVerlopen | Een bestaand clusterstatusrapport is verlopen | Hm | Informatief | 1 |

**Gebeurtenissen in het knooppuntgezondheidsrapport**

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Er is een nieuw knooppuntgezondheidsrapport beschikbaar | Hm | Informatief | 1 |
| 54432 | NodeHealthReportVerlopen | Een bestaand knooppuntstatusrapport is verlopen | Hm | Informatief | 1 |

**Gebeurtenissen in het rapport Toepassingsstatus**

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Er is een nieuw rapport over toepassingsstatus gemaakt. Dit is voor niet-geïmplementeerde toepassingen. | Hm | Informatief | 1 |
| 54426 | GeïmplementeerdApplicationNewHealthReport | Er is een nieuw geïmplementeerd toepassingsstatusrapport gemaakt | Hm | Informatief | 1 |
| 54427 | GeïmplementeerdServicePakketNewHealthReport | Er is een nieuw geïmplementeerd servicestatusrapport gemaakt | Hm | Informatief | 1 |
| 54434 | ApplicationHealthReportVerlopen | Een bestaand toepassingsstatusrapport is verlopen | Hm | Informatief | 1 |
| 54435 | GeïmplementeerdApplicationHealthReportVerlopen | Een bestaand geïmplementeerd toepassingsstatusrapport is verlopen | Hm | Informatief | 1 |
| 54436 | GeïmplementeerdServicePackageHealthReportVerlopen | Een bestaand geïmplementeerd servicestatusrapport is verlopen | Hm | Informatief | 1 |

**Gebeurtenissen in de status van service**

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Er is een nieuw servicestatusrapport gemaakt | Hm | Informatief | 1 |
| 54433 | ServiceHealthReportVerlopen | Een bestaand servicestatusrapport is verlopen | Hm | Informatief | 1 |

**Statusrapportgebeurtenissen voor partitiestatus**

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Er is een nieuw rapport over partitiestatus gemaakt | Hm | Informatief | 1 |
| 54431 | PartitionHealthReportVerlopen | Een bestaand partitiestatusrapport is verlopen | Hm | Informatief | 1 |

**Gebeurtenissen in het rapport Replica**

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Er is een stateful replica-statusrapport gemaakt | Hm | Informatief | 1 |
| 54430 | StatelessInstanceNewHealthReport | Er is een nieuw statusloos instantierapport gemaakt | Hm | Informatief | 1 |
| 54438 | StatefulReplicaHealthReportVerlopen | Een bestaand stateful replica-statusrapport is verlopen | Hm | Informatief | 1 |
| 54439 | StatelessInstanceHealthReportVerlopen | Een bestaand statusloos instantiestatusrapport is verlopen | Hm | Informatief | 1 |

## <a name="chaos-testing-events"></a>Chaos testen gebeurtenissen 

**Chaos sessie gebeurtenissen**

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosGestart | Er is een chaostestsessie gestart | Testabiliteit | Informatief | 1 |
| 50023 | ChaosGestopt | Een Chaos-testsessie is gestopt | Testabiliteit | Informatief | 1 |

**Chaos knooppunt gebeurtenissen**

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Een knooppunt is gepland om opnieuw te starten als onderdeel van een Chaos-testsessie | Testabiliteit | Informatief | 1 |
| 50087 | ChaosNodeRestartCompleted | Een knooppunt is klaar met opnieuw opstarten als onderdeel van een Chaos-testsessie | Testabiliteit | Informatief | 1 |

**Gebeurtenissen voor chaostoepassingen**

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageOpnieuw gepland | Een codepakket opnieuw opstarten is gepland tijdens een Chaos-testsessie | Testabiliteit | Informatief | 1 |
| 50101 | ChaosCodePackageOpnieuw voltooid | Een codepakket opnieuw opstarten is voltooid tijdens een Chaos-testsessie | Testabiliteit | Informatief | 1 |

**Chaospartitiegebeurtenissen**

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveGepland | Een primaire partitie is gepland om te worden verplaatst als onderdeel van een chaostestsessie | Testabiliteit | Informatief | 1 |
| 50077 | ChaosPartitionSecondaryMoveGepland | Een secundaire partitie is gepland om te worden verplaatst als onderdeel van een Chaos-testsessie | Testabiliteit | Informatief | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | De diepere analyse van de primaire partitiebeweging is beschikbaar | Testabiliteit | Informatief | 1 |

**Chaos replica gebeurtenissen**

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Een replica herstart is gepland als onderdeel van een Chaos testsessie | Testabiliteit | Informatief | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Een replica verwijdering is gepland als onderdeel van een Chaos testsessie | Testabiliteit | Informatief | 1 |
| 50093 | ChaosReplicaRemovalVoltooid | Een replica verwijdering is voltooid als onderdeel van een Chaos testsessie | Testabiliteit | Informatief | 1 |

## <a name="other-events"></a>Andere evenementen

**Correlatiegebeurtenissen**

| EventId (EventId) | Name | Beschrijving |Bron (taak) | Niveau | Versie |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelatieOperationeel | Er is een correlatie gedetecteerd | Testabiliteit | Informatief | 1 |

## <a name="events-prior-to-version-62"></a>Gebeurtenissen voorafgaand aan versie 6.2

Hier is een uitgebreide lijst van gebeurtenissen die door Service Fabric voorafgaand aan versie 6.2.

| EventId (EventId) | Name | Bron (taak) | Niveau |
| --- | --- | --- | --- |
| 25620 | KnooppuntOpening | FabricNode | Informatief |
| 25621 | NodeOpenedSucces | FabricNode | Informatief |
| 25622 | Knooppunt geopend mislukt | FabricNode | Informatief |
| 25623 | Knooppunt sluiten | FabricNode | Informatief |
| 25624 | Knooppunt Gesloten | FabricNode | Informatief |
| 25625 | Knooppunt Afbreken | FabricNode | Informatief |
| 25626 | Knooppunt Afgebroken | FabricNode | Informatief |
| 29627 | ClusterUpgradeStart | CM | Informatief |
| 29628 | ClusterUpgradeCompleet | CM | Informatief |
| 29629 | ClusterUpgradeRollback | CM | Informatief |
| 29630 | ClusterUpgradeRollbackVolledig | CM | Informatief |
| 29631 | ClusterUpgradeDomeinVolledig | CM | Informatief |
| 23074 | ContainerGeactiveerd | Hosting | Informatief |
| 23075 | ContainerGedeactiveerd | Hosting | Informatief |
| 29620 | Toepassinggemaakt | CM | Informatief |
| 29621 | ApplicationUpgradeStart | CM | Informatief |
| 29622 | ToepassingsUpgradeVolledig | CM | Informatief |
| 29623 | ApplicationUpgradeRollback | CM | Informatief |
| 29624 | ToepassingsUpgradeRollbackVolledig | CM | Informatief |
| 29625 | Toepassing Verwijderd | CM | Informatief |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informatief |
| 18566 | Servicegemaakt | FM | Informatief |
| 18567 | ServiceVerwijderd | FM | Informatief |

## <a name="next-steps"></a>Volgende stappen

* Krijg een overzicht van [diagnostiek in Service Fabric](service-fabric-diagnostics-overview.md)
* Meer informatie over het [EventStore-overzicht](service-fabric-diagnostics-eventstore.md) van De Eventstore van Service Fabric
* Uw [Azure Diagnostics-configuratie](service-fabric-diagnostics-event-aggregation-wad.md) wijzigen om meer logboeken te verzamelen
* [Toepassingsinzichten instellen](service-fabric-diagnostics-event-analysis-appinsights.md) om uw operationele kanaallogboeken te bekijken
