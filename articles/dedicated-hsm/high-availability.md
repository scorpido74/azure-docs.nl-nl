---
title: Hoge beschikbaarheid - Azure Dedicated HSM | Microsoft Documenten
description: Azure Dedicated HSM-voorbeeld voor hoge beschikbaarheid en basisoverwegingen
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 536ef62acad900090924598edfa45450b2a8c951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70882245"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure Dedicated HSM hoge beschikbaarheid

Azure Dedicated HSM wordt ondersteund door de zeer beschikbare datacenters van Microsoft. Elk zeer beschikbaar datacenter is echter kwetsbaar voor gelokaliseerde fouten en in extreme omstandigheden storingen op regionaal niveau. Microsoft implementeert HSM-apparaten in verschillende datacenters binnen een regio om ervoor te zorgen dat het inrichten van meerdere apparaten er niet toe leidt dat apparaten één rack delen. Een ander niveau van hoge beschikbaarheid kan worden bereikt door deze HSM's te koppelen aan de datacenters in een regio met behulp van de Gemalto HA Group-functie. Het is ook mogelijk om apparaten in verschillende regio's te koppelen om regionale failover in een situatie met noodherstel aan te pakken. Met deze configuratie met meerdere lagen hoge beschikbaarheid wordt elke apparaatfout automatisch verholpen om toepassingen te laten werken. Alle datacenters hebben ook reserveapparaten en onderdelen op locatie, zodat elk mislukt apparaat tijdig kan worden vervangen.

## <a name="high-availability-example"></a>Voorbeeld van hoge beschikbaarheid

Informatie over het configureren van HSM-apparaten voor hoge beschikbaarheid op softwareniveau vindt u in de 'Gemalto Luna Network HSM Administration Guide'. Dit document is beschikbaar op de [Gemalto HSM-pagina.](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)

Het volgende diagram toont een zeer beschikbare architectuur. Het maakt gebruik van meerdere apparaten in de regio en meerdere apparaten gekoppeld in een aparte regio. Deze architectuur maakt gebruik van minimaal vier HSM-apparaten en virtuele netwerkcomponenten.

![Diagram met hoge beschikbaarheid](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Volgende stappen

Het wordt aanbevolen dat alle belangrijke concepten van de service, zoals hoge beschikbaarheid en beveiliging, goed worden begrepen voordat apparaatinrichting en toepassingsontwerp of -implementatie worden geïmplementeerd.
Verdere concept niveau onderwerpen:

* [Implementatiearchitectuur](deployment-architecture.md)
* [Fysieke beveiliging](physical-security.md)
* [Networking](networking.md)
* [Ondersteuning](supportability.md)
* [Monitoring](monitoring.md)

Voor specifieke informatie over het configureren van HSM-apparaten voor hoge beschikbaarheid verwijzen wij u naar de Gemalto Customer Support Portal voor de beheerdershandleidingen en zie sectie 6.
