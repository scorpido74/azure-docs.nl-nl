---
title: Veelgestelde vragen
description: Antwoorden op veelgestelde vragen met betrekking tot de Azure Container Instances-service
author: dkkapur
ms.topic: article
ms.date: 01/07/2020
ms.openlocfilehash: 4a3fb4c1818d86f7fe2913790fd9e573c630cbfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247174"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Veelgestelde vragen over Azure Container Instances

In dit artikel worden veelgestelde vragen over Azure Container Instances beantwoord.

## <a name="deployment"></a>Implementatie

### <a name="how-large-can-my-container-image-be"></a>Hoe groot kan mijn containerafbeelding zijn?

De maximale grootte voor een inzetbare containerafbeelding op Azure Container Instances is 15 GB. Mogelijk u grotere afbeeldingen implementeren, afhankelijk van de exacte beschikbaarheid op het moment dat u implementeert, maar dit is niet gegarandeerd.

De grootte van uw containerafbeelding is van invloed op hoe lang het duurt om te implementeren, dus over het algemeen wilt u uw containerafbeeldingen zo klein mogelijk houden.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Hoe kan ik de inzet van mijn container versnellen?

Omdat een van de belangrijkste determinanten van implementatietijden de grootte van de afbeelding is, zoekt u naar manieren om de grootte te verkleinen. Verwijder lagen die u niet nodig hebt of verklein de grootte van lagen in de afbeelding (door een lichtere basis-OS-afbeelding te kiezen). Als u bijvoorbeeld Linux-containers gebruikt, u overwegen Alpine als basisafbeelding te gebruiken in plaats van als een volledige Ubuntu-server. Gebruik ook voor Windows-containers indien mogelijk een basisafbeelding van Nano Server. 

U moet ook de lijst met afbeeldingen met vooraf in de cache bekijken in Azure Container Images, beschikbaar via de API [voor afbeeldingen met lijstcache.](/rest/api/container-instances/listcachedimages) Mogelijk u een afbeeldingslaag voor een van de vooraf opgeslagen afbeeldingen uitschakelen. 

Zie meer [gedetailleerde richtlijnen](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) voor het verkorten van de opstarttijd van containers.

### <a name="what-windows-base-os-images-are-supported"></a>Welke Windows base OS-afbeeldingen worden ondersteund?

#### <a name="windows-server-2016-base-images"></a>Basisafbeeldingen van Windows Server 2016

* [Nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver) `10.0.14393.x`: ,`sac2016`
* [Windows Server](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2016`Core : ,`10.0.14393.x`

> [!NOTE]
> Windows-afbeeldingen op basis van semi-jaarlijkse kanaalrelease 1709 of 1803 worden niet ondersteund.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Afbeeldingen van Windows Server 2019 en clientbase (voorbeeld)

* [Nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver) `1809`: ,`10.0.17763.x`
* [Windows Server](https://hub.docker.com/_/microsoft-windows-servercore) `ltsc2019`Core `1809`: ,`10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows) `1809`: ,`10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Welke .NET- of .NET Core-afbeeldingslaag moet ik in mijn container gebruiken? 

Gebruik de kleinste afbeelding die aan uw vereisten voldoet. Voor Linux kun je een *runtime-alpine* .NET Core-afbeelding gebruiken, die sinds de release van .NET Core 2.1 wordt ondersteund. Als u voor Windows het volledige .NET-framework gebruikt, moet u een Windows Server Core-afbeelding gebruiken (runtime-only afbeelding, zoals *4.7.2-windowsservercore-ltsc2016*). Runtime-only afbeeldingen zijn kleiner, maar ondersteunen geen workloads waarvoor de .NET SDK nodig is.

## <a name="availability-and-quotas"></a>Beschikbaarheid en quota

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Hoeveel cores en geheugen moet ik toewijzen voor mijn containers of de containergroep?

Dit hangt echt af van uw werklast. Begin klein en test prestaties om te zien hoe uw containers het doen. Monitor het gebruik van [CPU- en geheugenbronnen](container-instances-monitor.md)en voeg vervolgens kernen of geheugen toe op basis van het soort processen dat u in de container implementeert. 

Controleer ook de [beschikbaarheid van resources](container-instances-region-availability.md#availability---general) voor de regio waarin u implementeert voor de bovengrenzen van CPU-cores en geheugen die beschikbaar zijn per containergroep. 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Op welke onderliggende infrastructuur draait ACI?

Azure Container Instances is een serverloze containers-on-demand service, dus we willen dat u zich richt op het ontwikkelen van uw containers en zich geen zorgen maakt over de infrastructuur! Voor degenen die nieuwsgierig zijn of willen vergelijkingen te doen op prestaties, ACI draait op sets van Azure VM's van verschillende SKU's, voornamelijk uit de F-en de D-serie. We verwachten dat dit in de toekomst zal veranderen naarmate we de service blijven ontwikkelen en optimaliseren. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Ik wil duizenden cores inzetten op ACI - kan ik mijn quotum verhoogd krijgen?
 
Ja (soms). Zie het [artikel over quota en limieten](container-instances-quotas.md) voor lopende contingenten en welke limieten op verzoek kunnen worden verhoogd.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Kan ik implementeren met meer dan 4 cores en 16 GB RAM?

Nog niet. Momenteel zijn dit de maxima voor een containergroep. Neem contact op met Azure Support met specifieke vereisten of aanvragen. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Wanneer bevindt ACI zich in een specifieke regio?

De huidige beschikbaarheid van de regio wordt [hier](container-instances-region-availability.md#availability---general)gepubliceerd. Als u een vereiste hebt voor een specifieke regio, neemt u contact op met Azure Support.

## <a name="features-and-scenarios"></a>Functies en scenario's

### <a name="how-do-i-scale-a-container-group"></a>Hoe schaal ik een containergroep?

Momenteel is schalen niet beschikbaar voor containers of containergroepen. Als u meer exemplaren wilt uitvoeren, gebruikt u onze API om meer aanvragen voor het maken van containergroepen voor de service te automatiseren en te maken. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Welke functies zijn beschikbaar voor instanties die worden uitgevoerd in een aangepaste VNet?

U [containergroepen implementeren in een virtueel Azure-netwerk](container-instances-vnet.md) naar keuze en privé-IP's delegeren aan de containergroepen om verkeer binnen het VNet over uw Azure-bronnen te leiden. Implementatie van een containergroep in een virtueel netwerk is momenteel beschikbaar voor productieworkloads in een subset van Azure-regio's.

## <a name="pricing"></a>Prijzen

### <a name="when-does-the-meter-start-running"></a>Wanneer begint de meter te lopen?

De duur van de containergroep wordt berekend vanaf het moment dat we de afbeelding van uw eerste container (voor een nieuwe implementatie) beginnen te trekken of dat uw containergroep opnieuw wordt gestart (als deze al is geïmplementeerd), totdat de containergroep is gestopt. Zie details bij [de prijzen van Containerinstances](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Moet ik niet meer in rekening worden gebracht wanneer mijn containers worden gestopt?

Meters stoppen met draaien zodra uw hele containergroep is gestopt. Zolang een container in uw containergroep wordt uitgevoerd, houden we de resources vast voor het geval u de containers opnieuw wilt starten. 

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie](container-instances-overview.md) over Azure Container Instances.
* [Veelvoorkomende problemen](container-instances-troubleshooting.md) in Azure Container Instances oplossen.