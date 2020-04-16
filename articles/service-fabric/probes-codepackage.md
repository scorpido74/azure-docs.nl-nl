---
title: Azure Service Fabric-sondes
description: Liveness Probe modelleren in Azure Service Fabric met behulp van toepassings- en servicemanifestbestanden.
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431213"
---
# <a name="liveness-probe"></a>Levendigheid Sonde
Beginnend met 7.1 Service Fabric ondersteunt Liveness Probe mechanisme voor [gecontaineriseerde][containers-introduction-link] toepassingen. Liveness Probe helpen kondigen de levendigheid van de containerized applicatie en wanneer ze niet tijdig reageren, zal dit resulteren in een herstart.
Dit artikel geeft een overzicht van hoe u een Liveness Probe definiëren via manifestbestanden.

Voordat we verder gaan met dit artikel, raden we aan om vertrouwd te raken met het [Service Fabric-toepassingsmodel][application-model-link] en het [Service Fabric-hostingmodel.][hosting-model-link]

> [!NOTE]
> Liveness Probe wordt alleen ondersteund voor containers in nat-netwerkmodus.

## <a name="semantics"></a>Semantiek
U slechts 1 Liveness Probe per container opgeven en het gedrag ervan met deze velden beheren:

* `initialDelaySeconds`: De eerste vertraging in seconden om te beginnen met het uitvoeren van sonde zodra de container is gestart. Ondersteunde waarde is int. Standaard is 0. Minimum is 0.

* `timeoutSeconds`: Periode in seconden waarna we sonde als mislukt beschouwen als deze niet is voltooid. Ondersteunde waarde is int. Standaard is 1. Minimum is 1.

* `periodSeconds`: Punt in seconden om aan te geven hoe vaak we sonde. Ondersteunde waarde is int. Standaard is 10. Minimum is 1.

* `failureThreshold`: Zodra we FailureThreshold hebben bereikt, wordt de container opnieuw opgestart. Ondersteunde waarde is int. Standaard is 3. Minimum is 1.

* `successThreshold`: Bij mislukking, voor sonde om als succes worden beschouwd moet het met succes voor SuccessThreshold uitvoeren. Ondersteunde waarde is int. Standaard is 1. Minimum is 1.

Er zal maximaal 1 sonde zijn om op een gegeven moment te containeren. Als de sonde niet volledig in **time-outSeconden** blijven we wachten en tellen naar de **failureThreshold**. 

Daarnaast zal ServiceFabric verhogen na sonde [gezondheidsrapporten][health-introduction-link] over geïmplementeerdServicepakket:

* `Ok`: Als de sonde slaagt voor **succesDrempel** dan melden we gezondheid als Ok.

* `Error`: Als de sonde misluktCount == **failureThreshold**, voordat u de container opnieuw start, melden we Fout.

* `Warning`: 
    1. Als de sonde uitvalt en de storingAantal < **failureThreshold** melden we Waarschuwing. Dit statusrapport blijft totdat failcount **bereikt mislukkingDrempel** of **succesDrempel**.
    2. Over succes na mislukking, rapporteren we nog steeds Waarschuwing, maar met bijgewerkte opeenvolgende succes.

## <a name="specifying-liveness-probe"></a>Liveness Probe opgeven

U de sonde opgeven in het ApplicationManifest.xml onder ServiceManifestImport:

Probe kan een van:

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>HTTP-sonde

Voor HTTP-sonde stuurt Service Fabric een HTTP-aanvraag naar de opgegeven poort en pad. Retourcode groter dan of gelijk aan 200 en minder dan 400 geeft succes aan.

Hier volgt een voorbeeld van het opgeven van httpGet-sonde:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

HttpGet-sonde heeft extra eigenschappen die u instellen:

* `path`: Pad naar toegang op het HTTP-verzoek.

* `port`: Poort voor toegang voor sondes. Bereik is 1 tot 65535. Verplicht.

* `scheme`: Schema te gebruiken voor het aansluiten op code pakket. Als u bent ingesteld op HTTPS, wordt de certificaatverificatie overgeslagen. Standaardwaarden voor HTTP

* `httpHeader`: Kopteksten die u in de aanvraag wilt instellen. U er meerdere opgeven.

* `host`: Host IP om verbinding mee te maken.

## <a name="tcp-probe"></a>TCP-sonde

Voor TCP-sonde probeert Service Fabric een socket op de container met de opgegeven poort te openen. Als het een verbinding kan vestigen, wordt de sonde beschouwd als succes. Hier volgt een voorbeeld van het opgeven van de sonde die TCP-socket gebruikt:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="exec-probe"></a>Exec Probe

Deze sonde geeft een exec in de container en wacht tot de opdracht is voltooid.

> [!NOTE]
> Exec commando neemt een komma gescheiden string. De volgende opdracht in het voorbeeld werkt voor Linux-container.
> Als u windows container probeert, gebruik <Command>cmd</Command>

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor gerelateerde informatie.
* [Service Fabric en containers.][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

