---
title: Azure Cloud Services Def. LoadBalancerProbe-schema | Microsoft Documenten
description: De door de klant gedefinieerde LoadBalancerProbe is een statussonde van eindpunten in rolexemplaren. Het combineert met web- of werknemersrollen in een servicedefinitiebestand.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 14
author: georgewallace
ms.author: tagore
ms.openlocfilehash: 6d0e84b6724d9df4162d4be3e06a9952087a53a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537343"
---
# <a name="azure-cloud-services-definition-loadbalancerprobe-schema"></a>Azure Cloud Services Definition LoadBalancerProbe-schema
De load balancer-sonde is een door de klant gedefinieerde statussonde van UDP-eindpunten en eindpunten in rolexemplaren. Het `LoadBalancerProbe` is geen standalone element; het wordt gecombineerd met de webrol of de rol van de werknemer in een servicedefinitiebestand. A `LoadBalancerProbe` kan worden gebruikt door meer dan een rol.

De standaardextensie voor het servicedefinitiebestand is .csdef.

## <a name="the-function-of-a-load-balancer-probe"></a>De functie van een lastenbalancersonde
De Azure Load Balancer is verantwoordelijk voor het routeren van binnenkomend verkeer naar uw rolexemplaren. De load balancer bepaalt welke instanties verkeer kunnen ontvangen door elke instantie regelmatig te onderzoeken om de status van die instantie te bepalen. De load balancer sondes elke instantie meerdere keren per minuut. Er zijn twee verschillende opties voor het verstrekken van bijvoorbeeld status aan de load balancer - de standaard load balancer sonde, of een aangepaste load balancer sonde, die wordt geïmplementeerd door het definiëren van de LoadBalancerProbe in de .csdef bestand.

De standaard load balancer sonde maakt gebruik van de Gast agent in de virtuele machine, die luistert en reageert met een HTTP 200 OK reactie alleen wanneer de instantie is in de status Klaar (zoals wanneer de instantie niet in de status Bezet, Recycling, Stoppen, etc. staat). Als de gastagent niet reageert met HTTP 200 OK, markeert de Azure Load Balancer de instantie als niet-reagerend en stopt het verzenden van verkeer naar die instantie. De Azure Load Balancer blijft de instantie pingen en als de gastagent reageert met een HTTP 200, stuurt de Azure Load Balancer opnieuw verkeer naar die instantie. Bij het gebruik van een webrol wordt uw websitecode meestal uitgevoerd in w3wp.exe, wat niet wordt gecontroleerd door de Azure-fabric of gastagent, wat fouten in w3wp.exe betekent (bijv. HTTP 500-antwoorden) worden niet gemeld aan de gastagent en de load balancer weet niet dat hij dat exemplaar uit de rotatie moet halen.

De aangepaste lastenbalancersonde overschrijft de standaard gastagentsonde en stelt u in staat om uw eigen aangepaste logica te maken om de status van de rolinstantie te bepalen. De load balancer sonde regelmatig uw eindpunt (elke 15 seconden, standaard) en de instantie wordt in rotatie beschouwd als deze binnen de time-outperiode reageert met een TCP ACK of HTTP 200 (standaard 31 seconden). Dit kan handig zijn om uw eigen logica te implementeren om exemplaren uit de rotatie van de load balancer te verwijderen, bijvoorbeeld door een niet-200-status terug te sturen als de instantie boven de 90% CPU ligt. Voor webrollen met w3wp.exe betekent dit ook dat u automatisch uw website controleren, omdat fouten in uw websitecode een niet-200-status teruggeven aan de load balancer-sonde. Als u een LoadBalancerProbe niet definieert in het .csdef-bestand, wordt het standaard gedrag van de load balancer (zoals eerder beschreven) gebruikt.

Als u een aangepaste lastenbalancersonde gebruikt, moet u ervoor zorgen dat uw logica rekening houdt met de methode RoleEnvironment.OnStop. Bij het gebruik van de standaard load balancer-sonde wordt de instantie uit de rotatie gehaald voordat OnStop wordt aangeroepen, maar een aangepaste lastenbalancersonde kan een 200 OK blijven retourneren tijdens de gebeurtenis OnStop. Als u de gebeurtenis OnStop gebruikt om cache op te schonen, de service te stoppen of anderszins wijzigingen aan te brengen die van invloed kunnen zijn op het runtimegedrag van uw service, moet u ervoor zorgen dat de logica van de aangepaste lastbalansssonde de instantie uit de rotatie verwijdert.

## <a name="basic-service-definition-schema-for-a-load-balancer-probe"></a>Basisservicedefinitieschema voor een load balancer-sonde
 De basisindeling van een servicedefinitiebestand met een load balancer-sonde is als volgt.

```xml
<ServiceDefinition …>
   <LoadBalancerProbes>
      <LoadBalancerProbe name="<load-balancer-probe-name>" protocol="[http|tcp]" path="<uri-for-checking-health-status-of-vm>" port="<port-number>" intervalInSeconds="<interval-in-seconds>" timeoutInSeconds="<timeout-in-seconds>"/>
   </LoadBalancerProbes>
</ServiceDefinition>
```

## <a name="schema-elements"></a>Schema-elementen
Het `LoadBalancerProbes` element van het servicedefinitiebestand bevat de volgende elementen:

- [LoadBalancerProbes Element](#LoadBalancerProbes)
- [LoadBalancerProbe Element](#LoadBalancerProbe)

##  <a name="loadbalancerprobes-element"></a><a name="LoadBalancerProbes"></a>LoadBalancerProbes Element
Het `LoadBalancerProbes` element beschrijft de verzameling van load balancer sondes. Dit element is het bovenliggende element van het [Element LoadBalancerProbe](#LoadBalancerProbe). 

##  <a name="loadbalancerprobe-element"></a><a name="LoadBalancerProbe"></a>LoadBalancerProbe Element
Het `LoadBalancerProbe` element definieert de statussonde voor een model. U meerdere load balancer probes definiëren. 

In de volgende tabel worden `LoadBalancerProbe` de kenmerken van het element beschreven:

|Kenmerk|Type|Beschrijving|
| ------------------- | -------- | -----------------|
| `name`              | `string` | Vereist. De naam van de lastensonde. De naam moet uniek zijn.|
| `protocol`          | `string` | Vereist. Hiermee geeft u het protocol van het eindpunt op. Mogelijke waarden zijn `http` en `tcp`. Als `tcp` dit is opgegeven, is een ontvangen ACK vereist om de sonde succesvol te laten zijn. Als `http` dit is opgegeven, is een 200 OK-antwoord van de opgegeven URI vereist om de sonde succesvol te laten zijn.|
| `path`              | `string` | De URI die wordt gebruikt voor het aanvragen van de status van de status van de VM. `path`is vereist `protocol` als `http`is ingesteld op . Anders is het niet toegestaan.<br /><br /> Er is geen standaardwaarde.|
| `port`              | `integer` | Optioneel. De poort voor het communiceren van de sonde. Dit is optioneel voor elk eindpunt, omdat dezelfde poort vervolgens voor de sonde wordt gebruikt. U een andere poort configureren voor hun indringende, als goed. Mogelijke waarden variëren van 1 tot 65535, inclusief.<br /><br /> De standaardwaarde wordt ingesteld op het eindpunt.|
| `intervalInSeconds` | `integer` | Optioneel. Het interval, in seconden, voor hoe vaak het eindpunt voor de status van de status moet worden gesonde. Meestal is het interval iets minder dan de helft van de toegewezen time-outperiode (in seconden) waarmee twee volledige sondes kunnen worden uitgevoerd voordat de instantie uit de rotatie wordt gehaald.<br /><br /> De standaardwaarde is 15, de minimumwaarde 5.|
| `timeoutInSeconds`  | `integer` | Optioneel. De time-outperiode, in seconden, toegepast op de sonde waar geen reactie zal resulteren in het stoppen van verdere verkeer wordt geleverd aan het eindpunt. Met deze waarde kunnen eindpunten sneller of langzamer uit de rotatie worden gehaald dan de typische tijden die in Azure worden gebruikt (de standaardwaarden).<br /><br /> De standaardwaarde is 31, de minimumwaarde 11.|

## <a name="see-also"></a>Zie ook
[Cloud Service (klassiek) definitieschema](schema-csdef-file.md)