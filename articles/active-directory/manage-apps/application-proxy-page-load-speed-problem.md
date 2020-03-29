---
title: Een toepassing van toepassingsproxy duurt te lang om te laden | Microsoft Documenten
description: Problemen met de prestaties van pagina's oplossen met de Azure AD-toepassingsproxy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac1182d719d7c90129115e1fadf94f4f86a28e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65782649"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Een toepassing van toepassingsproxy duurt te lang om te laden

In dit artikel u begrijpen waarom het laden van een Azure AD Application Proxy-toepassing lang kan duren. Het legt ook uit wat u doen om dit probleem op te lossen.

## <a name="overview"></a>Overzicht
Hoewel uw toepassingen werken, kunnen ze een lange latentie ervaren. Er kunnen netwerktopologie tweaks die u maken om de snelheid te verbeteren. Zie het [netwerkoverwegingsdocument](application-proxy-network-topology.md)voor een evaluatie van verschillende topologieën.

Naast netwerktopologie zijn er momenteel geen verdere aanbevelingen voor performance tuning. Als de Application Proxy-service wordt uitgebreid, kan deze komen tot een datacenter dat fysiek dichterbij is. De nabijheid kan helpen bij latentie. Zie de [latentietestpagina](http://www.azurespeed.com/Azure/Latency)voor een lijst met Azure-datacenters. 

De datacenters met de Application Proxy-service zijn te vinden met het [Connector Ports Test Tool.](https://aadap-portcheck.connectorporttest.msappproxy.net/) 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Feedback op locatie van het datacenter van Application Proxy 
Er kunnen Azure-datacenters zijn die application proxy nog niet bevatten, maar die zouden leiden tot een grote latentieverbetering voor u. Stuur de locatie aadapfeedback@microsoft.comvan het datacenter naar . Microsoft gebruikt uw feedback voor uitbreidingsplannen.

Microsoft werkt aan extra mogelijkheden om de latentie te verbeteren. Zodra deze verbeteringen beschikbaar zijn, wordt de documentatie bijgewerkt.

## <a name="next-steps"></a>Volgende stappen
[Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)
