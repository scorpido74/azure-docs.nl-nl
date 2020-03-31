---
title: 'Toepassingsupgrade: upgradeparameters'
description: Beschrijft parameters met betrekking tot het upgraden van een Service Fabric-toepassing, inclusief statuscontroles om uit te voeren en beleid om de upgrade automatisch ongedaan te maken.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 42b5c52181cfb006ae57e43c183b96a059a9c63a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75377970"
---
# <a name="application-upgrade-parameters"></a>Parameters toepassingsupgrade
In dit artikel worden de verschillende parameters beschreven die van toepassing zijn tijdens de upgrade van een Azure Service Fabric-toepassing. Toepassingsupgradeparameters bepalen de time-outs en statuscontroles die tijdens de upgrade worden toegepast en specificeren het beleid dat moet worden toegepast wanneer een upgrade mislukt. Toepassingsparameters zijn van toepassing op upgrades met:
- PowerShell
- Visual Studio
- SFCTL (SFCTL)
- [Rest](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Toepassingsupgrades worden gestart via een van de drie door de gebruiker geselecteerde upgrademodi. Elke modus heeft zijn eigen set toepassingsparameters:
- Gecontroleerd
- Niet-gecontroleerd automatisch
- Niet-bewaakte handleiding

De toepasselijke vereiste en optionele parameters worden in elke sectie als volgt beschreven.

## <a name="visual-studio-and-powershell-parameters"></a>Visual Studio- en PowerShell-parameters

Upgrades van servicefabric-toepassingen met PowerShell gebruiken de opdracht [Start-ServiceFabricApplicationUpgrade.](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) De upgrademodus wordt geselecteerd door de parameter **Monitored**, **UnmonitoredAuto**of **UnmonitoredManual** door te geven aan [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

De upgradeparameters van de Visual Studio Service Fabric-toepassing worden ingesteld via het dialoogvenster Instellingen voor upgrade van Visual Studio. De upgrademodus van Visual Studio is geselecteerd met het vervolgkeuzeveld **Upgrademodus** om **monitorte,** **niet-bewaakte auto**of **Niet-bewaakte handmatige**modus te gebruiken. Zie [De upgrade van een Service Fabric-toepassing configureren in Visual Studio](service-fabric-visualstudio-configure-upgrade.md)voor meer informatie.

### <a name="required-parameters"></a>Vereiste parameters
(PS=PowerShell, VS=Visual Studio)

| Parameter | Van toepassing op | Beschrijving |
| --- | --- | --- |
ApplicationName |PS| Naam van de toepassing die wordt bijgewerkt. Voorbeelden: stof:/VisualObjects, fabric:/ClusterMonitor. |
Toepassingstypeversie|PS|De versie van het toepassingstype waarop de upgrade is gericht. |
FailureAction |PS, VS|Toegestane waarden zijn **Terugdraaien,** **Handmatig**en **Ongeldig**. De compenserende actie die moet worden uitgevoerd wanneer een *bewaakte* upgrade wordt geconfronteerd met schendingen van het beleid of schendingen van het gezondheidsbeleid. <br>**Terugdraaien** geeft aan dat de upgrade automatisch wordt teruggedraaid naar de pre-upgradeversie. <br>**Handleiding** geeft aan dat de upgrade zal overschakelen naar de *UnmonitoredManual* upgrade mode. <br>**Ongeldig** geeft aan dat de foutactie ongeldig is.|
Gecontroleerd |PS|Geeft aan dat de upgrademodus wordt bewaakt. Nadat de cmdlet een upgrade voor een upgradedomein heeft voltooid, als de status van het upgradedomein en het cluster voldoen aan het gezondheidsbeleid dat u definieert, verbetert Service Fabric het volgende upgradedomein. Als het upgradedomein of -cluster niet voldoet aan het gezondheidsbeleid, mislukt de upgrade en rolt Service Fabric de upgrade voor het upgradedomein terug of keert deze terug naar de handmatige modus volgens het opgegeven beleid. Dit is de aanbevolen modus voor toepassingsupgrades in een productieomgeving. |
UpgradeModus | Vs | Toegestane waarden **worden gecontroleerd** (standaard), **OnbewaakteAuto**of **Niet-bewaakte handmatige**waarde . Zie PowerShell-parameters voor elke modus in dit artikel voor meer informatie. |
Onbewaakte auto | PS | Geeft aan dat de upgrademodus niet-gecontroleerd automatisch is. Nadat Service Fabric een upgradedomein heeft bijgewerkt, heeft Service Fabric het volgende upgradedomein bijgewerkt, ongeacht de status van de toepassing. Deze modus wordt niet aanbevolen voor productie en is alleen nuttig tijdens de ontwikkeling van een toepassing. |
Niet-monitoredManual | PS | Geeft aan dat de upgrademodus niet-gecontroleerd handmatig is. Nadat Service Fabric een upgradedomein heeft bijgewerkt, wacht het op een upgrade van het volgende upgradedomein met behulp van de cmdlet *Cv-ServiceFabricApplicationUpgrade.* |

### <a name="optional-parameters"></a>Optionele parameters

De parameters voor gezondheidsevaluatie zijn optioneel. Als de criteria voor statusevaluatie niet zijn opgegeven wanneer een upgrade wordt gestart, gebruikt Service Fabric het toepassingsstatusbeleid dat is opgegeven in de toepassingsfunctie van de toepassingsinstantie.

> [!div class="mx-tdBreakAll"]
> | Parameter | Van toepassing op | Beschrijving |
> | --- | --- | --- |
> | ToepassingsParameter |PS, VS| Hiermee geeft u de overschrijvingen voor toepassingsparameters op.<br>PowerShell-toepassingsparameters worden opgegeven als hashtable name/value pairs. Bijvoorbeeld @{ "VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1" }.<br>De toepassingsparameters van Visual Studio kunnen worden opgegeven in het dialoogvenster Fabric-toepassing publiceren service in het veld **Toepassingsparametersbestand.**
> | Bevestigen |PS| Toegestane waarden zijn **Waar** en **Onwaar.** Vraagt om bevestiging voordat u de cmdlet uitvoert. |
> | OverweegWarningAsError |PS, VS |Toegestane waarden zijn **Waar** en **Onwaar.** De standaardwaarde is **False**. Behandel de waarschuwingsstatusgebeurtenissen voor de toepassing als fouten bij het evalueren van de status van de toepassing tijdens de upgrade. Standaard evalueert Service Fabric geen waarschuwingsstatusgebeurtenissen als fouten (fouten), zodat de upgrade kan doorgaan, zelfs als er waarschuwingsgebeurtenissen zijn. |
> | DefaultserviceTypeHealthPolicy | PS, VS |Hiermee geeft u het statusbeleid op voor het standaardservicetype dat moet worden gebruikt voor de bewaakte upgrade in de indeling MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices. Bijvoorbeeld, 5,10,15 geeft de volgende waarden aan: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Toegestane waarden zijn **Waar** en **Onwaar.** Geeft aan dat het upgradeproces het waarschuwingsbericht overslaat en de upgrade dwingt, zelfs als het versienummer niet is gewijzigd. Dit is handig voor lokale tests, maar wordt niet aanbevolen voor gebruik in een productieomgeving, omdat hiervoor de bestaande implementatie moet worden verwijderd die uitvaltijd en mogelijk gegevensverlies veroorzaakt. |
> | ForceRestart (ForceRestart) |PS, VS |Als u een configuratie of gegevenspakket bijwerkt zonder de servicecode bij te werken, wordt de service alleen opnieuw gestart als de eigenschap ForceRestart is ingesteld op **True.** Wanneer de update is voltooid, waarschuwt Service Fabric de service dat er een nieuw configuratiepakket of gegevenspakket beschikbaar is. De service is verantwoordelijk voor het toepassen van de wijzigingen. Indien nodig kan de service zichzelf opnieuw opstarten. |
> | HealthCheckRetryTimeoutSec |PS, VS |De duur (in seconden) die Service Fabric blijft uitvoeren van de statusevaluatie voordat de upgrade als mislukt wordt aangegeven. De standaardinstelling is 600 seconden. Deze duur begint nadat *HealthCheckWaitDurationSec* is bereikt. Binnen deze *HealthCheckRetryTimeout*kan Service Fabric meerdere statuscontroles van de toepassingsstatus uitvoeren. De standaardwaarde is 10 minuten en moet op de juiste manier worden aangepast voor uw toepassing. |
> | HealthCheckStableDurationSec |PS, VS |De duur (in seconden) om te controleren of de toepassing stabiel is voordat u naar het volgende upgradedomein gaat of de upgrade voltooit. Deze wachttijd wordt gebruikt om onopgemerkte gezondheidswijzigingen te voorkomen direct nadat de statuscontrole is uitgevoerd. De standaardwaarde is 120 seconden en moet op de juiste manier worden aangepast voor uw toepassing. |
> | HealthCheckWaitDurationSec |PS, VS | De tijd om te wachten (in seconden) nadat de upgrade is voltooid op het upgradedomein voordat Service Fabric de status van de toepassing evalueert. Deze duur kan ook worden beschouwd als de tijd dat een toepassing moet worden uitgevoerd voordat deze als gezond kan worden beschouwd. Als de statuscontrole verloopt, gaat het upgradeproces door naar het volgende upgradedomein.  Als de statuscontrole mislukt, wacht Service Fabric op [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) voordat u de statuscontrole opnieuw probeert totdat de *HealthCheckRetryTimeoutSec* is bereikt. De standaard waarde en de aanbevolen waarde is 0 seconden. |
> | MaxPercentUnhealthyDeployedApplications MaxPercentUnhealthyDePloyedApplications MaxPercentUnhealthyDePloyedApplications MaxPercent|PS, VS |De standaardwaarde en de aanbevolen waarde is 0. Geef het maximum aantal geïmplementeerde toepassingen op (zie de [sectie Gezondheid)](service-fabric-health-introduction.md)dat ongezond kan zijn voordat de toepassing als ongezond wordt beschouwd en de upgrade mislukt. Deze parameter definieert de toepassingsstatus op het knooppunt en helpt bij het detecteren van problemen tijdens de upgrade. Meestal worden de replica's van de toepassing load-balanced naar het andere knooppunt, waardoor de toepassing gezond kan lijken, waardoor de upgrade kan doorgaan. Door een strikte *MaxPercentUnhealthyDeployedApplications-status* op te geven, kan Service Fabric een probleem met het toepassingspakket snel detecteren en een fail-snelle upgrade helpen produceren. |
> | MaxPercentUnhealthyServices |PS, VS |Een parameter voor *DefaultServiceTypeHealthPolicy* en *ServiceTypeHealthPolicyMap*. De standaardwaarde en de aanbevolen waarde is 0. Geef het maximum aantal services op in de toepassingsinstantie dat ongezond kan zijn voordat de toepassing als ongezond wordt beschouwd en de upgrade mislukt. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |Een parameter voor *DefaultServiceTypeHealthPolicy* en *ServiceTypeHealthPolicyMap*. De standaardwaarde en de aanbevolen waarde is 0. Geef het maximum aantal partities in een service op dat ongezond kan zijn voordat de service als ongezond wordt beschouwd. |
> | MaxPercentUnhealthyReplicasPerPartition MaxPercentUnhealthyReplicasPerPartition MaxPercentUnhealthyReplicasPerPartition MaxPercent|PS, VS |Een parameter voor *DefaultServiceTypeHealthPolicy* en *ServiceTypeHealthPolicyMap*. De standaardwaarde en de aanbevolen waarde is 0. Geef het maximum aantal replica's in partitie op dat ongezond kan zijn voordat de partitie als ongezond wordt beschouwd. |
> | ServiceTypeHealthPolicyMap | PS, VS | Vertegenwoordigt het gezondheidsbeleid dat wordt gebruikt om de status van services van een servicetype te evalueren. Neemt een hash tabel invoer in de volgende indeling: @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} Bijvoorbeeld: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
> | Time-outSec | PS, VS | Hiermee geeft u de time-outperiode in seconden voor de bewerking op. |
> | UpgradeDomainTimeoutSec |PS, VS |Maximale tijd (in seconden) voor het upgraden van één upgradedomein. Als deze time-out is bereikt, stopt en gaat de upgrade door op basis van de instelling voor *FailureAction.* De standaardwaarde is nooit (Oneindig) en moet op de juiste manier worden aangepast voor uw toepassing. |
> | UpgradeReplicaSetCheckTimeoutSec |PS, VS |Gemeten in seconden.<br>**Stateless service**--Binnen één upgradedomein probeert Service Fabric ervoor te zorgen dat er extra exemplaren van de service beschikbaar zijn. Als het aantal doelinstanties meer dan één is, wacht Service Fabric tot er meer dan één instantie beschikbaar is, tot een maximale time-outwaarde. Deze time-out wordt opgegeven met de eigenschap *UpgradeReplicaSetCheckOutSec.* Als de time-out verloopt, gaat Service Fabric verder met de upgrade, ongeacht het aantal service-exemplaren. Als het aantal doelgevallen één is, wacht Service Fabric niet en gaat onmiddellijk verder met de upgrade.<br><br>**Stateful service**--Binnen één upgradedomein probeert Service Fabric ervoor te zorgen dat de replicaset een quorum heeft. Servicefabric wacht tot een quorum beschikbaar is, tot een maximale time-outwaarde (opgegeven door de eigenschap *UpgradeReplicaSetCheckTimeoutSec).* Als de time-out verloopt, gaat Service Fabric door met de upgrade, ongeacht het quorum. Deze instelling is ingesteld als nooit (oneindig) bij het naar voren rollen, en 1200 seconden bij het terugdraaien. |
> | UpgradeTimeoutSec |PS, VS |Een time-out (in seconden) die van toepassing is op de gehele upgrade. Als deze time-out is bereikt, stopt de upgrade en wordt *FailureAction* geactiveerd. De standaardwaarde is nooit (Oneindig) en moet op de juiste manier worden aangepast voor uw toepassing. |
> | WhatIf | PS | Toegestane waarden zijn **Waar** en **Onwaar.** Hiermee wordt weergegeven wat er zou gebeuren als u de cmdlet uitvoert. De cmdlet wordt niet uitgevoerd. |

De *maxpercentunhealthyservices,* *MaxPercentUnhealthyPartitionsPerService*en *MaxPercentUnhealthyReplicasPerPartition-criteria* kunnen per servicetype voor een toepassingsinstantie worden opgegeven. Als u deze parameters per service instelt, kan een toepassing verschillende servicestypen bevatten met verschillende evaluatiebeleidsregels. Een stateless gatewayservicetype kan bijvoorbeeld een *MaxPercentUnhealthyPartitionsPerService* hebben die verschilt van een stateful engineservicetype voor een bepaalde toepassingsinstantie.

## <a name="sfctl-parameters"></a>SFCTL-parameters

Upgrades van servicefabric-toepassingen met behulp van de Service Fabric CLI maken gebruik van de [upgradeopdracht sfctl-toepassing,](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) samen met de volgende vereiste en optionele parameters.

### <a name="required-parameters"></a>Vereiste parameters

| Parameter | Beschrijving |
| --- | --- |
| toepassings-id  |ID van de toepassing die wordt bijgewerkt. <br> Dit is meestal de volledige naam van de toepassing zonder de 'stof:' URI regeling. Vanaf versie 6.0 worden hiërarchische namen\~afgebakend met het ' teken.' Als de toepassingsnaam bijvoorbeeld 'fabric:/myapp/app1' is, is de\~identiteit van de toepassing 'myapp app1' in 6.0+ en 'myapp/app1' in eerdere versies.|
toepassingsversie |De versie van het toepassingstype waarop de upgrade is gericht.|
parameters  |Een JSON gecodeerde lijst van toepassingsparameteroverschrijvingen die moeten worden toegepast bij het upgraden van de toepassing.|

### <a name="optional-parameters"></a>Optionele parameters

| Parameter | Beschrijving |
| --- | --- |
standaardservice-statusbeleid | [Json](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) gecodeerde specificatie van het gezondheidsbeleid dat standaard wordt gebruikt om de status van een servicetype te evalueren. De kaart is standaard leeg. |
faalactie | Toegestane waarden zijn **Terugdraaien,** **Handmatig**en **Ongeldig**. De compenserende actie die moet worden uitgevoerd wanneer een *bewaakte* upgrade wordt geconfronteerd met schendingen van het beleid of schendingen van het gezondheidsbeleid. <br>**Terugdraaien** geeft aan dat de upgrade automatisch wordt teruggedraaid naar de pre-upgradeversie. <br>**Handleiding** geeft aan dat de upgrade zal overschakelen naar de *UnmonitoredManual* upgrade mode. <br>**Ongeldig** geeft aan dat de foutactie ongeldig is.|
force-restart | Als u een configuratie of gegevenspakket bijwerkt zonder de servicecode bij te werken, wordt de service alleen opnieuw gestart als de eigenschap ForceRestart is ingesteld op **True.** Wanneer de update is voltooid, waarschuwt Service Fabric de service dat er een nieuw configuratiepakket of gegevenspakket beschikbaar is. De service is verantwoordelijk voor het toepassen van de wijzigingen. Indien nodig kan de service zichzelf opnieuw opstarten. |
health-check-retry-time-out | De hoeveelheid tijd die nodig is om de statusevaluatie opnieuw te proberen wanneer de toepassing of het cluster niet in orde is voordat *FailureAction* wordt uitgevoerd. Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. Standaard: PT0H10M0S. |
health-check-stable-duration | De hoeveelheid tijd dat de toepassing of het cluster gezond moet blijven voordat de upgrade doorgaat naar het volgende upgradedomein. Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. Standaard: PT0H2M0S. |
health-check-wait-duration | De hoeveelheid tijd om te wachten na het voltooien van een upgradedomein voordat u het gezondheidsbeleid toepast. Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. Standaard: 0.|
max-unhealthy-apps | De standaardwaarde en de aanbevolen waarde is 0. Geef het maximum aantal geïmplementeerde toepassingen op (zie de [sectie Gezondheid)](service-fabric-health-introduction.md)dat ongezond kan zijn voordat de toepassing als ongezond wordt beschouwd en de upgrade mislukt. Deze parameter definieert de toepassingsstatus op het knooppunt en helpt bij het detecteren van problemen tijdens de upgrade. Meestal worden de replica's van de toepassing load-balanced naar het andere knooppunt, waardoor de toepassing gezond kan lijken, waardoor de upgrade kan doorgaan. Door een strikte status van *max-ongezonde apps* op te geven, kan Service Fabric snel een probleem met het toepassingspakket detecteren en een snelle upgrade helpen produceren. Vertegenwoordigd als een getal tussen 0 en 100. |
mode | Toegestane waarden **worden gecontroleerd,** **UpgradeMode**, **UnmonitoredAuto**, **UnmonitoredManual**. Standaard is **UnmonitoredAuto**. Zie de sectie *Vereiste parameters voor* Visual Studio en PowerShell voor beschrijvingen van deze waarden.|
tijdscontrole replica-set-check-time-out |Gemeten in seconden. <br>**Stateless service**--Binnen één upgradedomein probeert Service Fabric ervoor te zorgen dat er extra exemplaren van de service beschikbaar zijn. Als het aantal doelinstanties meer dan één is, wacht Service Fabric tot er meer dan één instantie beschikbaar is, tot een maximale time-outwaarde. Deze time-out wordt opgegeven met behulp van de eigenschap *replica-set-check-time-out.* Als de time-out verloopt, gaat Service Fabric verder met de upgrade, ongeacht het aantal service-exemplaren. Als het aantal doelgevallen één is, wacht Service Fabric niet en gaat onmiddellijk verder met de upgrade.<br><br>**Stateful service**--Binnen één upgradedomein probeert Service Fabric ervoor te zorgen dat de replicaset een quorum heeft. Service fabric wacht tot een quorum beschikbaar is, tot een maximale time-out waarde (opgegeven door de *replica-set-check-time-out* eigenschap). Als de time-out verloopt, gaat Service Fabric door met de upgrade, ongeacht het quorum. Deze instelling is ingesteld als nooit (oneindig) bij het naar voren rollen, en 1200 seconden bij het terugdraaien. |
service-health-beleid | Json-gecodeerde kaart met servicetypestatusbeleid per servicetypenaam. De kaart is leeg zijn standaard. [Parameter JSON-indeling.](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). Het JSON voor het gedeelte 'Waarde' bevat **MaxPercentUnhealthyServices,** **MaxPercentUnhealthyPartitionsPerService**en **MaxPercentUnhealthyReplicasPerPartition**. Zie de sectie Optionele parameters van Visual Studio en PowerShell voor beschrijvingen van deze parameters.
timeout | Hiermee geeft u de time-outperiode in seconden voor de bewerking op. Standaard: 60. |
upgrade-domein-time-out | De hoeveelheid tijd die elk upgradedomein moet voltooien voordat *FailureAction* wordt uitgevoerd. Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. De standaardwaarde is nooit (Oneindig) en moet op de juiste manier worden aangepast voor uw toepassing. Standaard: P10675199DT02H48M05.4775807S. |
upgrade-time-out | De hoeveelheid tijd die elk upgradedomein moet voltooien voordat *FailureAction* wordt uitgevoerd. Het wordt eerst geïnterpreteerd als een tekenreeks die een ISO 8601-duur vertegenwoordigt. Als dat niet lukt, wordt het geïnterpreteerd als een getal dat het totale aantal milliseconden vertegenwoordigt. De standaardwaarde is nooit (Oneindig) en moet op de juiste manier worden aangepast voor uw toepassing. Standaard: P10675199DT02H48M05.4775807S.|
waarschuwing per fout | Toegestane waarden zijn **Waar** en **Onwaar.** De standaardwaarde is **False**. Kan worden doorgegeven als een vlag. Behandel de waarschuwingsstatusgebeurtenissen voor de toepassing als fouten bij het evalueren van de status van de toepassing tijdens de upgrade. Standaard evalueert Service Fabric geen waarschuwingsstatusgebeurtenissen als fouten (fouten), zodat de upgrade kan doorgaan, zelfs als er waarschuwingsgebeurtenissen zijn. |

## <a name="next-steps"></a>Volgende stappen
[Upgraden van uw toepassing Met Visual Studio](service-fabric-application-upgrade-tutorial.md) u een applicatie-upgrade doorlopen met Visual Studio.

[Het upgraden van uw toepassing met Powershell](service-fabric-application-upgrade-tutorial-powershell.md) leidt u door een applicatie-upgrade met PowerShell.

[Het upgraden van uw toepassing met Behulp van Service Fabric CLI op Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) leidt u door een applicatie-upgrade met behulp van Service Fabric CLI.

[Uw toepassing upgraden met Service Fabric Eclipse-plug-in](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

Maak uw toepassingsupgrades compatibel door te leren hoe [u Data Serialization kunt](service-fabric-application-upgrade-data-serialization.md)gebruiken.

Meer informatie over het gebruik van geavanceerde functionaliteit tijdens het upgraden van uw toepassing door te verwijzen naar [geavanceerde onderwerpen.](service-fabric-application-upgrade-advanced.md)

Los veelvoorkomende problemen op in toepassingsupgrades door te verwijzen naar de stappen in [het oplossen van toepassingsupgrades.](service-fabric-application-upgrade-troubleshooting.md)
