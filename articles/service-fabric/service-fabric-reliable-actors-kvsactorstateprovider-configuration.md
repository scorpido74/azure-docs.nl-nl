---
title: KvSActorStateProvider-instellingen wijzigen
description: Meer informatie over het configureren van Azure Service Fabric stateful actoren van het type KVSActorStateProvider.
author: sumukhs
ms.topic: conceptual
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: cdb115bd57cf3d5af4388f4efa03c2522feef9ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609771"
---
# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Betrouwbare actoren configureren--KVSactorstateProvider
U de standaardconfiguratie van KVSActorStateProvider wijzigen door het bestand settings.xml te wijzigen dat wordt gegenereerd in de hoofdmap Microsoft Visual Studio-pakket onder de map Config voor de opgegeven actor.

De runtime azure servicefabric zoekt naar vooraf gedefinieerde sectienamen in het bestand settings.xml en verbruikt de configuratiewaarden terwijl de onderliggende runtime-componenten worden gemaakt.

> [!NOTE]
> Verwijder of wijzig de sectienamen van de volgende configuraties **niet** in het bestand settings.xml dat wordt gegenereerd in de Visual Studio-oplossing.
> 
> 

## <a name="replicator-security-configuration"></a>Beveiligingsconfiguratie Replicator
Replicator-beveiligingsconfiguraties worden gebruikt om het communicatiekanaal te beveiligen dat wordt gebruikt tijdens de replicatie. Dit betekent dat services elkaars replicatieverkeer niet kunnen zien, zodat de gegevens die in hoge mate beschikbaar worden gesteld, ook veilig zijn.
Standaard voorkomt een lege beveiligingsconfiguratiesectie replicatiebeveiliging.

> [!IMPORTANT]
> Op Linux-knooppunten moeten certificaten pem-geformatteerd zijn. Zie Certificaten configureren [op Linux](./service-fabric-configure-certificates-linux.md)voor meer informatie over het zoeken en configureren van certificaten voor Linux. 
> 

### <a name="section-name"></a>Sectienaam
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Replicatorconfiguratie
Replicatorconfiguraties configureren de replicator die verantwoordelijk is voor het maken van de status Actor State Provider zeer betrouwbaar.
De standaardconfiguratie wordt gegenereerd door de Visual Studio-sjabloon en moet volstaan. In deze sectie wordt gesproken over aanvullende configuraties die beschikbaar zijn om de replicator af te stemmen.

### <a name="section-name"></a>Sectienaam
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Configuratienamen
| Name | Eenheid | Standaardwaarde | Opmerkingen |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |Seconden |0.015 |Tijdsperiode waarvoor de replicator bij de secundaire wacht na ontvangst van een bewerking voordat u een bevestiging naar de primaire stuurt. Alle andere bevestigingen die binnen dit interval moeten worden verzonden voor bewerkingen, worden als één antwoord verzonden. |
| ReplicatorEndpoint |N.v.t. |Geen standaardparameter |IP-adres en poort die de primaire/secundaire replicator zal gebruiken om te communiceren met andere replicators in de replicaset. Hiermee moet worden verwezen naar een TCP-resourceeindpunt in het servicemanifest. Raadpleeg [bronnen voor het manifest service](service-fabric-service-manifest-resources.md) om meer te lezen over het definiëren van eindpuntbronnen in het servicemanifest. |
| Opnieuw proberenInterval |Seconden |5 |Tijdsperiode waarna de replicator een bericht opnieuw verzendt als deze geen bevestiging voor een bewerking ontvangt. |
| MaxReplicatieMessageSize |Bytes |50 MB |Maximale grootte van replicatiegegevens die in één bericht kunnen worden verzonden. |
| MaxPrimaryReplicationQueueSize MaxPrimaryReplicationQueueSize MaxprimaryReplicationQueueSize Maxprimary |Aantal bewerkingen |1024 |Maximaal aantal bewerkingen in de primaire wachtrij. Een bewerking wordt vrijgemaakt nadat de primaire replicator een bevestiging heeft ontvangen van alle secundaire replicators. Deze waarde moet groter zijn dan 64 en een vermogen van 2. |
| MaxSecondaryReplicatieWachtrijgrootte |Aantal bewerkingen |2048 |Maximaal aantal bewerkingen in de secundaire wachtrij. Een operatie wordt vrijgemaakt na het maken van de staat zeer beschikbaar door middel van persistentie. Deze waarde moet groter zijn dan 64 en een vermogen van 2. |

## <a name="store-configuration"></a>Winkelconfiguratie
Winkelconfiguraties worden gebruikt om de lokale winkel te configureren die wordt gebruikt om de status die wordt gerepliceerd, voort te zetten.
De standaardconfiguratie wordt gegenereerd door de Visual Studio-sjabloon en moet volstaan. In dit gedeelte wordt gesproken over aanvullende configuraties die beschikbaar zijn om de lokale winkel af te stemmen.

### <a name="section-name"></a>Sectienaam
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Configuratienamen
| Name | Eenheid | Standaardwaarde | Opmerkingen |
| --- | --- | --- | --- |
| MaxAsyncCommitDelayInMilliseconds |Milliseconden |200 |Hiermee stelt u het maximale batchinterval in voor duurzame lokale winkelcommits. |
| MaxverPages MaxverPages |Aantal pagina's |16384 |Het maximum aantal versiepagina's in de lokale winkeldatabase. Het bepaalt het maximum aantal uitstaande transacties. |

## <a name="sample-configuration-file"></a>Voorbeeldconfiguratiebestand
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
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
## <a name="remarks"></a>Opmerkingen
De parameter BatchAcknowledgementInterval regelt replicatielatentie. Een waarde van '0' resulteert in de laagst mogelijke latentie, ten koste van doorvoer (omdat er meer bevestigingsberichten moeten worden verzonden en verwerkt, elk met minder bevestigingen).
Hoe groter de waarde voor BatchAcknowledgementInterval, hoe hoger de totale replicatiedoorvoer, ten koste van hogere bewerkingslatentie. Dit vertaalt zich direct naar de latentie van transactiecommits.

