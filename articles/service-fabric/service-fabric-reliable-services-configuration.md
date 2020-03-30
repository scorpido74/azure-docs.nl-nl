---
title: Betrouwbare services voor Azure-servicefabric configureren
description: Meer informatie over het configureren van stateful Reliable Services in een Azure Service Fabric-toepassing wereldwijd en voor één service.
author: sumukhs
ms.topic: conceptual
ms.date: 10/02/2017
ms.author: sumukhs
ms.openlocfilehash: 9743213394b59af701b25b8be9dd48cf4310b499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645511"
---
# <a name="configure-stateful-reliable-services"></a>Stateful betrouwbare services configureren
Er zijn twee sets configuratie-instellingen voor betrouwbare services. De ene set is globaal voor alle betrouwbare services in het cluster, terwijl de andere set specifiek is voor een bepaalde betrouwbare service.

## <a name="global-configuration"></a>Globale configuratie
De globale betrouwbare serviceconfiguratie is opgegeven in het clustermanifest voor het cluster onder de sectie KtlLogger. Het maakt configuratie van de gedeelde log locatie en grootte plus de wereldwijde geheugenlimieten gebruikt door de logger. Het clustermanifest is één XML-bestand met instellingen en configuraties die van toepassing zijn op alle knooppunten en services in het cluster. Het bestand wordt meestal ClusterManifest.xml genoemd. U het clustermanifest voor uw cluster bekijken met de opdracht Get-ServiceClusterManifest powershell.

### <a name="configuration-names"></a>Configuratienamen
| Name | Eenheid | Standaardwaarde | Opmerkingen |
| --- | --- | --- | --- |
| WritebuffermemorypoolminimumInKB |Kilobytes |8388608 |Minimum aantal KB toe te wijzen in kernel modus voor de logger schrijf buffer memory pool. Deze geheugengroep wordt gebruikt voor het plaatsen van statusgegevens voordat u naar de schijf schrijft. |
| WritebuffermemorypoolmaximumInKB |Kilobytes |Geen limiet |Maximale grootte waarop de logger buffer geheugen pool kan groeien. |
| SharedLogId |GUID |"" |Hiermee geeft u een unieke GUID op die moet worden gebruikt voor het identificeren van het standaard gedeelde logboekbestand dat wordt gebruikt door alle betrouwbare services op alle knooppunten in het cluster die de SharedLogId niet opgeven in hun servicespecifieke configuratie. Als SharedLogId is opgegeven, moet ook SharedLogPath worden opgegeven. |
| SharedLogPath |Volledig gekwalificeerde padnaam |"" |Hiermee geeft u het volledig gekwalificeerde pad op waarin het gedeelde logboekbestand dat wordt gebruikt door alle betrouwbare services op alle knooppunten in het cluster die het SharedLogPath niet opgeven in hun servicespecifieke configuratie. Als SharedLogPath echter is opgegeven, moet SharedLogId ook worden opgegeven. |
| SharedLogSizeInMB |Megabytes |8192 |Hiermee geeft u het aantal MB schijfruimte op dat statisch moet worden toegewezen voor het gedeelde logboek. De waarde moet 2048 of hoger zijn. |

In Azure ARM- of on-premises JSON-sjabloon ziet u in het onderstaande voorbeeld hoe u het gedeelde transactielogboek wijzigen dat wordt gemaakt om betrouwbare verzamelingen voor stateful services te herstellen.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="sample-local-developer-cluster-manifest-section"></a>Voorbeeld van de sectie clustermanifest van lokale ontwikkelaars
Als u dit wilt wijzigen in uw lokale ontwikkelomgeving, moet u het bestand lokaal clustermanifest.xml bewerken.

```xml
   <Section Name="KtlLogger">
     <Parameter Name="SharedLogSizeInMB" Value="4096"/>
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
   </Section>
```

### <a name="remarks"></a>Opmerkingen
De logger heeft een wereldwijde pool van geheugen toegewezen van niet-gepageerde kernel geheugen dat beschikbaar is voor alle betrouwbare services op een knooppunt voor caching state data voordat ze worden geschreven naar de speciale log in verband met de betrouwbare service replica. De grootte van de groep wordt geregeld door de instellingen WriteBufferMemoryPoolMinimumInKB en WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB geeft zowel de initiële grootte van deze geheugengroep als de laagste grootte op waarop de geheugengroep kan krimpen. WriteBufferMemoryPoolMaximumInKB is de hoogste grootte waarop de geheugengroep kan groeien. Elke betrouwbare servicereplica die wordt geopend, kan de grootte van de geheugengroep vergroten met een door het systeem bepaald bedrag tot WriteBufferMemoryPoolMaximumInKB. Als er meer vraag is naar geheugen uit de geheugengroep dan beschikbaar is, worden aanvragen voor geheugen vertraagd totdat het geheugen beschikbaar is. Als de schrijfbuffergeheugenpool te klein is voor een bepaalde configuratie, kunnen de prestaties dus lijden.

De instellingen SharedLogId en SharedLogPath worden altijd samen gebruikt om de GUID en locatie voor het standaard gedeelde logboek voor alle knooppunten in het cluster te definiëren. Het standaard gedeelde logboek wordt gebruikt voor alle betrouwbare services die de instellingen in de instellingen.xml voor de specifieke service niet opgeven. Voor de beste prestaties moeten gedeelde logboekbestanden op schijven worden geplaatst die uitsluitend worden gebruikt voor het gedeelde logboekbestand om de twist te verminderen.

SharedLogSizeInMB geeft de hoeveelheid schijfruimte op die vooraf moet worden toegewezen voor het standaard gedeelde logboek op alle knooppunten.  SharedLogId en SharedLogPath hoeven niet te worden opgegeven om SharedLogSizeInMB te kunnen specificeren.

## <a name="service-specific-configuration"></a>Servicespecifieke configuratie
U de standaardconfiguraties van Stateful Reliable Services wijzigen met behulp van het configuratiepakket (Config) of de service-implementatie (code).

* **Config** - Configuratie via het config-pakket wordt bereikt door het bestand Settings.xml te wijzigen dat wordt gegenereerd in de microsoft visual studio-pakketroot onder de map Config voor elke service in de toepassing.
* **Code** - Configuratie via code wordt uitgevoerd door een ReliableStateManager te maken met behulp van een Object ReliableStateManagerConfiguration met de juiste optiesset.

Standaard zoekt de runtime azure servicefabric naar vooraf gedefinieerde sectienamen in het bestand Settings.xml en verbruikt de configuratiewaarden terwijl de onderliggende runtime-componenten worden gemaakt.

> [!NOTE]
> Verwijder de sectienamen van de volgende configuraties **niet** in het bestand Settings.xml dat wordt gegenereerd in de Visual Studio-oplossing, tenzij u van plan bent uw service via code te configureren.
> Als u de naam van het config-pakket of de sectienamen wijzigt, is een codewijziging vereist bij het configureren van de ReliableStateManager.
> 
> 

### <a name="replicator-security-configuration"></a>Beveiligingsconfiguratie Replicator
Replicator-beveiligingsconfiguraties worden gebruikt om het communicatiekanaal te beveiligen dat wordt gebruikt tijdens de replicatie. Dit betekent dat services elkaars replicatieverkeer niet kunnen zien, zodat de gegevens die in hoge mate beschikbaar worden gesteld, ook veilig zijn. Standaard voorkomt een lege beveiligingsconfiguratiesectie replicatiebeveiliging.

> [!IMPORTANT]
> Op Linux-knooppunten moeten certificaten pem-geformatteerd zijn. Zie Certificaten configureren [op Linux](./service-fabric-configure-certificates-linux.md)voor meer informatie over het zoeken en configureren van certificaten voor Linux. 
> 
> 

### <a name="default-section-name"></a>Standaardsectienaam
ReplicatorSecurityConfig

> [!NOTE]
> Als u de naam van deze sectie wilt wijzigen, overschrijft u de parameter replicatorSecuritySectionName naar de constructor ReliableStateManagerConfiguration wanneer u de ReliableStateManager voor deze service maakt.
> 
> 

### <a name="replicator-configuration"></a>Replicatorconfiguratie
Replicatorconfiguraties configureren de replicator die verantwoordelijk is voor het zeer betrouwbaar maken van de status van de stateful Reliable Service door de status lokaal te repliceren en voort te zetten.
De standaardconfiguratie wordt gegenereerd door de Visual Studio-sjabloon en moet volstaan. In deze sectie wordt gesproken over aanvullende configuraties die beschikbaar zijn om de replicator af te stemmen.

### <a name="default-section-name"></a>Standaardsectienaam
ReplicatorConfig

> [!NOTE]
> Als u de naam van deze sectie wilt wijzigen, overschrijft u de parameter replicatorSettingsSectionName naar de constructor ReliableStateManagerConfiguration wanneer u de ReliableStateManager voor deze service maakt.
> 
> 

### <a name="configuration-names"></a>Configuratienamen
| Name | Eenheid | Standaardwaarde | Opmerkingen |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Seconden |0.015 |Tijdsperiode waarvoor de replicator bij de secundaire wacht na ontvangst van een bewerking voordat u een bevestiging naar de primaire stuurt. Alle andere bevestigingen die binnen dit interval moeten worden verzonden voor bewerkingen, worden als één antwoord verzonden. |
| ReplicatorEndpoint |N.v.t. |Geen standaardparameter |IP-adres en poort die de primaire/secundaire replicator zal gebruiken om te communiceren met andere replicators in de replicaset. Hiermee moet worden verwezen naar een TCP-resourceeindpunt in het servicemanifest. Raadpleeg [bronnen voor het manifest service](service-fabric-service-manifest-resources.md) om meer te lezen over het definiëren van eindpuntbronnen in een servicemanifest. |
| MaxPrimaryReplicationQueueSize MaxPrimaryReplicationQueueSize MaxprimaryReplicationQueueSize Maxprimary |Aantal bewerkingen |8192 |Maximaal aantal bewerkingen in de primaire wachtrij. Een bewerking wordt vrijgemaakt nadat de primaire replicator een bevestiging heeft ontvangen van alle secundaire replicators. Deze waarde moet groter zijn dan 64 en een vermogen van 2. |
| MaxSecondaryReplicatieWachtrijgrootte |Aantal bewerkingen |16384 |Maximaal aantal bewerkingen in de secundaire wachtrij. Een operatie wordt vrijgemaakt na het maken van de staat zeer beschikbaar door middel van persistentie. Deze waarde moet groter zijn dan 64 en een vermogen van 2. |
| CheckpointThresholdInMB |MB |50 |Hoeveelheid logboekbestandsruimte waarna de status wordt gecontroleereerd. |
| MaxRecordSizeInKB |KB |1024 |Grootste recordgrootte die de replicator in het logboek kan schrijven. Deze waarde moet een veelvoud van 4 en groter dan 16 zijn. |
| MinLogSizeInMB |MB |0 (systeem bepaald) |Minimale grootte van het transactielogboek. Het logboek mag niet worden afgekapt tot een maat onder deze instelling. 0 geeft aan dat de replicator de minimale logboekgrootte zal bepalen. Het verhogen van deze waarde verhoogt de mogelijkheid om gedeeltelijke kopieën en incrementele back-ups te maken, omdat de kans dat relevante logboekrecords worden afgekapt, wordt verminderd. |
| TruncationThresholdFactor |Factor |2 |Hiermee bepaalt u op welke grootte van het logboek de afsplitsing wordt geactiveerd. Afgekapte drempelwordt bepaald door MinLogSizeInMB vermenigvuldigd met TruncationThresholdFactor. TruncationThresholdFactor moet groter zijn dan 1. MinLogSizeInMB * TruncationThresholdFactor moet lager zijn dan MaxStreamSizeInMB. |
| BeperkingThresholdFactor |Factor |4 |Hiermee bepaalt u op welke grootte van het logboek de replica wordt beperkt. Beperkingsdrempel (in MB) wordt bepaald door Max((MinLogSizeInMB * ThrottlingThresholdFactor),(CheckpointThresholdInMB * ThrottlingThresholdFactor)). Beperkingsdrempel (in MB) moet groter zijn dan de afgekapte drempelwaarde (in MB). De afgekapte drempelwaarde (in MB) moet kleiner zijn dan MaxStreamSizeInMB. |
| MaxAccumulatedBackupLogSizeInMB |MB |800 |Maximale geaccumuleerde grootte (in MB) van back-uplogboeken in een bepaalde back-uplogboekketen. Een incrementele back-up aanvragen zal mislukken als de incrementele back-up zou een back-up log dat zou leiden tot de geaccumuleerde back-up logs, omdat de relevante volledige back-up groter is dan deze grootte. In dergelijke gevallen moet de gebruiker een volledige back-up maken. |
| SharedLogId |GUID |"" |Hiermee geeft u een unieke GUID op die u wilt gebruiken voor het identificeren van het gedeelde logboekbestand dat met deze replica wordt gebruikt. Services mogen deze instelling doorgaans niet gebruiken. Als SharedLogId echter is opgegeven, moet ook SharedLogPath worden opgegeven. |
| SharedLogPath |Volledig gekwalificeerde padnaam |"" |Hiermee geeft u het volledig gekwalificeerde pad op waar het gedeelde logboekbestand voor deze replica wordt gemaakt. Services mogen deze instelling doorgaans niet gebruiken. Als SharedLogPath echter is opgegeven, moet SharedLogId ook worden opgegeven. |
| SlowapimonitoringDuur |Seconden |300 |Hiermee stelt u het controle-interval in voor beheerde API-aanroepen. Voorbeeld: door de gebruiker verstrekte back-upcallback-functie. Nadat het interval is verstreken, wordt een waarschuwingsrapport naar de statusmanager verzonden. |
| LogTruncationIntervalSeconden |Seconden |0 |Configureerbaar interval waarbij op elke replica een afgekapte logboekafvloeiing wordt gestart. Het wordt gebruikt om ervoor te zorgen log is ook afgekapt op basis van tijd in plaats van alleen log grootte. Deze instelling dwingt ook zuivering van verwijderde items in betrouwbare woordenboek. Vandaar dat het kan worden gebruikt om ervoor te zorgen verwijderde items worden gezuiverd in een tijdige wijze. |
| EnableStableReads |Booleaans |False |Het inschakelen van stabiele reads beperkt secundaire replica's tot terugkerende waarden die zijn geplunderd. |

### <a name="sample-configuration-via-code"></a>Voorbeeldconfiguratie via code
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>Voorbeeldconfiguratiebestand
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### <a name="remarks"></a>Opmerkingen
BatchAcknowledgementInterval regelt replicatielatentie. Een waarde van '0' resulteert in de laagst mogelijke latentie, ten koste van doorvoer (omdat er meer bevestigingsberichten moeten worden verzonden en verwerkt, elk met minder bevestigingen).
Hoe groter de waarde voor BatchAcknowledgementInterval, hoe hoger de totale replicatiedoorvoer, ten koste van hogere bewerkingslatentie. Dit vertaalt zich direct naar de latentie van transactiecommits.

De waarde voor CheckpointThresholdInMB bepaalt de hoeveelheid schijfruimte die de replicator kan gebruiken om statusgegevens op te slaan in het specifieke logboekbestand van de replica. Als u dit verhoogt tot een hogere waarde dan de standaardwaarde, kan dit leiden tot snellere herconfiguratietijden wanneer een nieuwe replica aan de set wordt toegevoegd. Dit is te wijten aan de gedeeltelijke overdracht van de status die plaatsvindt als gevolg van de beschikbaarheid van meer geschiedenis van bewerkingen in het logboek. Dit kan mogelijk de hersteltijd van een replica na een crash verhogen.

Met de instelling MaxRecordSizeInKB wordt de maximale grootte gedefinieerd van een record die door de replicator in het logboekbestand kan worden geschreven. In de meeste gevallen is de standaard recordgrootte van 1024 KB optimaal. Als de service er echter voor zorgt dat grotere gegevensitems deel uitmaken van de statusgegevens, moet deze waarde mogelijk worden verhoogd. Er is weinig voordeel in het maken van MaxRecordSizeInKB kleiner dan 1024, als kleinere records gebruiken alleen de ruimte die nodig is voor de kleinere record. Wij verwachten dat deze waarde in slechts zeldzame gevallen moet worden gewijzigd.

De instellingen SharedLogId en SharedLogPath worden altijd samen gebruikt om een service een afzonderlijk gedeeld logboek te laten gebruiken van het standaard gedeelde logboek voor het knooppunt. Voor de beste efficiëntie moeten zoveel mogelijk services hetzelfde gedeelde logboek opgeven. Gedeelde logboekbestanden moeten op schijven worden geplaatst die uitsluitend worden gebruikt voor het gedeelde logboekbestand om de geschil over hoofdbewegingen te verminderen. Wij verwachten dat deze waarde in slechts zeldzame gevallen moet worden gewijzigd.

## <a name="next-steps"></a>Volgende stappen
* [Foutopsporing van uw Service Fabric-toepassing in Visual Studio](service-fabric-debugging-your-application.md)
* [Ontwikkelaarsreferentie voor betrouwbare services](https://msdn.microsoft.com/library/azure/dn706529.aspx)

