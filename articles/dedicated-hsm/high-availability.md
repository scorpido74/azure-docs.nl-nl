---
title: Hoge Beschik baarheid-specifieke HSM voor Azure | Microsoft Docs
description: Voor beeld van een hoge Beschik baarheid voor Azure en basis overwegingen
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "70882245"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Hoge Beschik baarheid van exclusieve HSM voor Azure

De specifieke HSM van Azure wordt uitgebreid door de Maxi maal beschik bare data centers van micro soft. Elk Data Center met hoge Beschik baarheid is echter kwetsbaar voor gelokaliseerde storingen en in extreme omstandigheden, regionale niveau fouten. Micro soft implementeert HSM-apparaten in verschillende data centers binnen een regio om ervoor te zorgen dat meerdere apparaten worden ingericht, waardoor niet kan worden bepaald welke apparaten één rek delen. U kunt een hoger niveau voor hoge Beschik baarheid bereiken door deze Hsm's te koppelen aan de data centers in een regio met behulp van de functie Gemalto HA Group. Het is ook mogelijk om apparaten in verschillende regio's te koppelen om de regionale failover in een nood herstel situatie te verhelpen. Met deze configuratie met hoge Beschik baarheid in meerdere lagen wordt een storing in het apparaat automatisch opgelost om toepassingen te laten werken. Alle data centers hebben ook reserve apparaten en onderdelen op locatie, zodat elk mislukt apparaat tijdig kan worden vervangen.

## <a name="high-availability-example"></a>Voor beeld van hoge Beschik baarheid

Informatie over het configureren van HSM-apparaten voor hoge Beschik baarheid op het software niveau vindt u in de Gemalto Luna Network HSM Administration Guide (Engelstalig). Dit document is beschikbaar op de [GEMALTO HSM-pagina](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).

Het volgende diagram toont een Maxi maal beschik bare architectuur. Het gebruikt meerdere apparaten in de regio en meerdere apparaten die zijn gekoppeld aan een afzonderlijke regio. Deze architectuur maakt gebruik van Mini maal vier HSM-apparaten en onderdelen van virtuele netwerken.

![Diagram met hoge Beschik baarheid](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Volgende stappen

Het wordt aanbevolen om alle belang rijke concepten van de service, zoals hoge Beschik baarheid en beveiliging, goed te begrijpen vóór het inrichten van apparaten en het ontwerpen of implementeren van toepassingen.
Meer onderwerpen over het concept niveau:

* [Implementatie architectuur](deployment-architecture.md)
* [Fysieke beveiliging](physical-security.md)
* [Netwerken](networking.md)
* [Ondersteuning](supportability.md)
* [Controle](monitoring.md)

Voor specifieke informatie over het configureren van HSM-apparaten voor maximale Beschik baarheid raadpleegt u de Gemalto-portal voor klanten ondersteuning voor de beheerders handleidingen en raadpleegt u sectie 6.
