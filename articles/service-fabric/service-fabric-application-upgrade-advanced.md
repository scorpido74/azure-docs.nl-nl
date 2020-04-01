---
title: Onderwerpen voor geavanceerde upgrade van toepassingen
description: Dit artikel behandelt een aantal geavanceerde onderwerpen met betrekking tot het upgraden van een Service Fabric-toepassing.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 182ab6dc1663e160561b8941ebf3a36b5af3d950
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422814"
---
# <a name="service-fabric-application-upgrade-advanced-topics"></a>Upgrade servicefabric-toepassing: geavanceerde onderwerpen

## <a name="add-or-remove-service-types-during-an-application-upgrade"></a>Servicetypen toevoegen of verwijderen tijdens een toepassingsupgrade

Als een nieuw servicetype wordt toegevoegd aan een gepubliceerde toepassing als onderdeel van een upgrade, wordt het nieuwe servicetype toegevoegd aan de geïmplementeerde toepassing. Een dergelijke upgrade heeft geen invloed op een van de service-exemplaren die al deel uitmaakten van de toepassing, maar een instantie van het servicetype dat is toegevoegd, moet worden gemaakt om het nieuwe servicetype actief te laten zijn (zie [New-ServiceFabricService).](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)

Op dezelfde manier kunnen servicetypen uit een toepassing worden verwijderd als onderdeel van een upgrade. Alle service-exemplaren van het te verwijderen servicetype moeten echter worden verwijderd voordat de upgrade wordt uitgevoerd (zie [Remove-ServiceFabricService).](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricservice?view=azureservicefabricps)

## <a name="avoid-connection-drops-during-stateless-service-planned-downtime-preview"></a>Voorkom dat de verbinding daalt tijdens geplande downtime van de geplande servicezonder service (preview)

Voor geplande stateless instance downtimes, zoals application/cluster upgrade of node deactivation, kunnen verbindingen worden verwijderd als gevolg van het blootgestelde eindpunt wordt verwijderd nadat de instantie uitvalt, wat resulteert in gedwongen verbindingssluitingen.

Om dit te voorkomen, configureert u de functie *RequestDrain* (preview) door een *instantie afsluitingsduur* toe te voegen aan de serviceconfiguratie om afvoer mogelijk te maken tijdens het ontvangen van aanvragen van andere services binnen het cluster en gebruikt u Reverse Proxy of gebruikt u resolve API met meldingsmodel voor het bijwerken van eindpunten. Dit zorgt ervoor dat het eindpunt dat door de instantie staatloos wordt *geadverteerd,* wordt verwijderd voordat de vertraging begint voordat de instantie wordt gesloten. Met deze vertraging kunnen bestaande aanvragen gracieus worden afgetapt voordat de instantie daadwerkelijk naar beneden gaat. Clients worden op de hoogte gesteld van de wijziging van het eindpunt door een callback-functie op het moment van het starten van de vertraging, zodat ze het eindpunt opnieuw kunnen oplossen en kunnen voorkomen dat nieuwe aanvragen naar de instantie worden verzonden die naar beneden gaat.

### <a name="service-configuration"></a>Serviceconfiguratie

Er zijn verschillende manieren om de vertraging aan de servicezijde te configureren.

 * **Geef bij het maken van een nieuwe service**een `-InstanceCloseDelayDuration`:

    ```powershell
    New-ServiceFabricService -Stateless [-ServiceName] <Uri> -InstanceCloseDelayDuration <TimeSpan>`
    ```

 * **Terwijl u de service definieert in de sectie Standaardinstellingen in het toepassingsmanifest,** wijst u de `InstanceCloseDelayDurationSeconds` eigenschap toe:

    ```xml
          <StatelessService ServiceTypeName="Web1Type" InstanceCount="[Web1_InstanceCount]" InstanceCloseDelayDurationSeconds="15">
              <SingletonPartition />
          </StatelessService>
    ```

 * **Geef bij het bijwerken van een bestaande service**een `-InstanceCloseDelayDuration`:

    ```powershell
    Update-ServiceFabricService [-Stateless] [-ServiceName] <Uri> [-InstanceCloseDelayDuration <TimeSpan>]`
    ```

### <a name="client-configuration"></a>Clientconfiguratie

Als u een melding wilt ontvangen wanneer een eindpunt is gewijzigd, moeten clients een callback registreren zie [ServiceNotificationFilterDescription](https://docs.microsoft.com/dotnet/api/system.fabric.description.servicenotificationfilterdescription).
De wijzigingsmelding is een indicatie dat de eindpunten zijn gewijzigd, dat de client de eindpunten opnieuw moet oplossen en geen gebruik moet maken van de eindpunten die niet meer worden geadverteerd, omdat ze binnenkort naar beneden zullen gaan.

### <a name="optional-upgrade-overrides"></a>Optionele upgradeoverschrijvingen

Naast het instellen van standaardvertragingsduur per service, u de vertraging`InstanceCloseDelayDurationSec`ook overschrijven tijdens de upgrade van toepassing/cluster met dezelfde ( ) optie:

```powershell
Start-ServiceFabricApplicationUpgrade [-ApplicationName] <Uri> [-ApplicationTypeVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]

Start-ServiceFabricClusterUpgrade [-CodePackageVersion] <String> [-ClusterManifestVersion] <String> [-InstanceCloseDelayDurationSec <UInt32>]
```

De vertragingsduur is alleen van toepassing op de ingeroepen upgrade-instantie en verandert anders geen afzonderlijke configuratie van servicevertraging. U dit bijvoorbeeld gebruiken om `0` een vertraging op te geven om vooraf geconfigureerde upgradevertragingen over te slaan.

> [!NOTE]
> De instelling voor het aftappen van aanvragen wordt niet gehonoreerd voor aanvragen van Azure Load balancer. De instelling wordt niet gehonoreerd als de oproepservice gebruik maakt van op klachten gebaseerde oplossing.
>
>

> [!NOTE]
> Deze functie kan worden geconfigureerd in bestaande services met behulp van Update-ServiceFabricService-cmdlet zoals hierboven vermeld, wanneer de versie van de clustercode 7.1.XXX of hoger is.
>
>

## <a name="manual-upgrade-mode"></a>Handmatige upgrademodus

> [!NOTE]
> De *monitored-upgrademodus* wordt aanbevolen voor alle Service Fabric-upgrades.
> De upgrademodus *UnmonitoredManual* moet alleen in aanmerking komen voor mislukte of opgeschorte upgrades. 
>
>

In *de monitormodus* past Service Fabric gezondheidsbeleid toe om ervoor te zorgen dat de toepassing in orde is naarmate de upgrade vordert. Als het gezondheidsbeleid wordt geschonden, wordt de upgrade opgeschort of automatisch teruggedraaid, afhankelijk van de opgegeven *FailureAction*.

In de modus *Niet-monitoredManual* heeft de toepassingsbeheerder volledige controle over de voortgang van de upgrade. Deze modus is handig bij het toepassen van aangepaste gezondheidsevaluatiebeleid of het uitvoeren van niet-conventionele upgrades om statusbewaking volledig te omzeilen (bijvoorbeeld de toepassing is al in gegevensverlies). Een upgrade die in deze modus wordt uitgevoerd, wordt onderbroken na het voltooien van elke UD en moet expliciet worden hervat met [cv-serviceFabricApplicationUpgrade.](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) Wanneer een upgrade is opgeschort en klaar is om door de gebruiker te worden hervat, wordt de upgradestatus *RollforwardPending* weergegeven (zie [UpgradeState](https://docs.microsoft.com/dotnet/api/system.fabric.applicationupgradestate?view=azure-dotnet)).

Ten slotte is de *modus UnmonitoredAuto* handig voor het uitvoeren van snelle upgrade-iteraties tijdens serviceontwikkeling of -tests, omdat er geen gebruikersinvoer vereist is en er geen beleid voor toepassingsstatus wordt geëvalueerd.

## <a name="upgrade-with-a-diff-package"></a>Upgraden met een diff-pakket

In plaats van het inrichten van een compleet toepassingspakket, kunnen upgrades ook worden uitgevoerd door het inrichten van diff-pakketten die alleen de bijgewerkte code/config/datapakketten bevatten, samen met het volledige toepassingsmanifest en volledige servicemanifesten. Complete toepassingspakketten zijn alleen vereist voor de eerste installatie van een toepassing op het cluster. Latere upgrades kunnen afkomstig zijn van complete toepassingspakketten of diff-pakketten.  

Elke verwijzing in het toepassingsmanifest of de servicemanifesten van een diff-pakket dat niet in het toepassingspakket kan worden gevonden, wordt automatisch vervangen door de momenteel ingerichte versie.

Scenario's voor het gebruik van een diff-pakket zijn:

* Wanneer u een groot toepassingspakket hebt dat verwijst naar verschillende servicemanifestbestanden en/of meerdere codepakketten, config-pakketten of gegevenspakketten.
* Wanneer u een implementatiesysteem hebt dat de build-indeling rechtstreeks vanuit uw toepassingsopbouwproces genereert. In dit geval, hoewel de code niet is gewijzigd, krijgen nieuw gebouwde assemblages een andere checksum. Als u een volledig toepassingspakket gebruikt, moet u de versie op alle codepakketten bijwerken. Met behulp van een diff-pakket biedt u alleen de bestanden die zijn gewijzigd en de manifestbestanden waarin de versie is gewijzigd.

Wanneer een toepassing wordt bijgewerkt met Visual Studio, wordt een diff-pakket automatisch gepubliceerd. Om handmatig een diff-pakket te maken, moeten het toepassingsmanifest en de servicemanifesten worden bijgewerkt, maar alleen de gewijzigde pakketten moeten in het uiteindelijke toepassingspakket worden opgenomen.

Laten we bijvoorbeeld beginnen met de volgende toepassing (versienummers die worden verstrekt voor het gemak van begrijpen):

```text
app1           1.0.0
  service1     1.0.0
    code       1.0.0
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

Laten we aannemen dat u alleen het codepakket van service1 wilt bijwerken met behulp van een diff-pakket. Uw bijgewerkte toepassing heeft de volgende versiewijzigingen:

```text
app1           2.0.0      <-- new version
  service1     2.0.0      <-- new version
    code       2.0.0      <-- new version
    config     1.0.0
  service2     1.0.0
    code       1.0.0
    config     1.0.0
```

In dit geval werkt u het toepassingsmanifest bij naar 2.0.0 en het servicemanifest voor service1 om de update van het codepakket weer te geven. De map voor uw aanvraagpakket heeft de volgende structuur:

```text
app1/
  service1/
    code/
```

Met andere woorden, maak normaal een compleet toepassingspakket en verwijder vervolgens alle map/config/datapakketmappen waarvoor de versie niet is gewijzigd.

## <a name="upgrade-application-parameters-independently-of-version"></a>Toepassingsparameters onafhankelijk van versie bijwerken

Soms is het wenselijk om de parameters van een Service Fabric-toepassing te wijzigen zonder de manifestversie te wijzigen. Dit kan handig met de vlag **-ApplicationParameter** met de **Start-ServiceFabricApplicationUpgrade** Azure Service Fabric PowerShell-cmdlet. Ga uit van een Service Fabric-toepassing met de volgende eigenschappen:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "1"; "NewParameter" = "testBefore" }
```

Upgrade nu de toepassing met de **cmdlet Start-ServiceFabricApplicationUpgrade.** In dit voorbeeld wordt een bewaakte upgrade weergegeven, maar een niet-bewaakte upgrade kan ook worden gebruikt. Zie de naslaggids voor De Azure Service [Fabric PowerShell-module](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps#parameters) voor een volledige beschrijving van vlaggen die door deze cmdlet zijn geaccepteerd,

```PowerShell
PS C:\> $appParams = @{ "ImportantParameter" = "2"; "NewParameter" = "testAfter"}

PS C:\> Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/Application1 -ApplicationTypeVers
ion 1.0.0 -ApplicationParameter $appParams -Monitored

```

Controleer na het upgraden of de toepassing de bijgewerkte parameters en dezelfde versie heeft:

```PowerShell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/Application1

ApplicationName        : fabric:/Application1
ApplicationTypeName    : Application1Type
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : { "ImportantParameter" = "2"; "NewParameter" = "testAfter" }
```

## <a name="roll-back-application-upgrades"></a>Toepassingsupgrades terugdraaien

Terwijl upgrades kunnen worden uitgerold in een van de drie modi *(Monitored*, *UnmonitoredAuto*, of *UnmonitoredManual*), kunnen ze alleen worden teruggedraaid in *de modus UnmonitoredAuto* of *UnmonitoredManual.* Terugdraaien in de modus *UnmonitoredAuto* werkt op dezelfde manier als naar voren rollen met de uitzondering dat de standaardwaarde van *UpgradeReplicaSetCheckTimeout* anders is - zie [Parameters voor toepassingsupgrade](service-fabric-application-upgrade-parameters.md). Terugdraaien in *de Modus UnmonitoredManual* werkt op dezelfde manier als naar voren rollen - de terugdraaiing schort zichzelf op na het voltooien van elke UD en moet expliciet worden hervat met [cv-serviceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/resume-servicefabricapplicationupgrade?view=azureservicefabricps) om door te gaan met de terugdraaiing.

Rollbacks kunnen automatisch worden geactiveerd wanneer het gezondheidsbeleid van een upgrade in *de monitormodus* met een *foutactie* van *terugdraaien* wordt geschonden (zie [Parameters voor toepassingsupgrade)](service-fabric-application-upgrade-parameters.md)of expliciet [start-servicefabricapplicationrollback](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationrollback?view=azureservicefabricps)gebruikt.

Tijdens het terugdraaien kunnen de waarde van *UpgradeReplicaSetCheckTimeout* en de modus nog steeds op elk gewenst moment worden gewijzigd met [Update-ServiceFabricApplicationUpgrade.](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricapplicationupgrade?view=azureservicefabricps)

## <a name="next-steps"></a>Volgende stappen
[Upgraden van uw toepassing Met Visual Studio](service-fabric-application-upgrade-tutorial.md) u een applicatie-upgrade doorlopen met Visual Studio.

[Het upgraden van uw toepassing met Powershell](service-fabric-application-upgrade-tutorial-powershell.md) leidt u door een applicatie-upgrade met PowerShell.

Bepaal hoe uw toepassing wordt bijgewerkt met [upgradeparameters.](service-fabric-application-upgrade-parameters.md)

Maak uw toepassingsupgrades compatibel door te leren hoe [u Data Serialization kunt](service-fabric-application-upgrade-data-serialization.md)gebruiken.

Los veelvoorkomende problemen op in toepassingsupgrades door te verwijzen naar de stappen in [het oplossen van toepassingsupgrades.](service-fabric-application-upgrade-troubleshooting.md)
