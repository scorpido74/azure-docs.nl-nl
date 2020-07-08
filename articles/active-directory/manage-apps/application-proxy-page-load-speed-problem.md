---
title: Het laden van een toepassings proxy toepassing duurt te lang Microsoft Docs
description: Problemen met de prestaties van pagina's oplossen met de Azure AD-toepassingsproxy
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bff9873742d5d660f563eebcb58ff9bcca940d24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760169"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Het laden van een toepassings proxy toepassing duurt te lang

Dit artikel helpt u te begrijpen waarom het laden van een Azure AD-toepassingsproxy-toepassing veel tijd in beslag kan nemen. Er wordt ook uitgelegd wat u kunt doen om dit probleem op te lossen.

## <a name="overview"></a>Overzicht
Hoewel uw toepassingen werken, kunnen ze een lange latentie ondervinden. Er zijn mogelijk aanpassingen aan de netwerk topologie die u kunt aanbrengen om de snelheid te verbeteren. Raadpleeg het [document over netwerk overwegingen](application-proxy-network-topology.md)voor een evaluatie van verschillende topologieën.

Naast de netwerk topologie zijn er momenteel geen verdere aanbevelingen voor het afstemmen van de prestaties. Wanneer de Application proxy-service uitbreidt, kan deze worden geleverd aan een Data Center dat zich fysiek dichter bevindt. De nauwere nabijheid kan helpen met latentie. Zie de [pagina latentie test](http://www.azurespeed.com/Azure/Latency)voor een lijst met Azure-data centers. 

De data centers met de Application proxy-service kunnen worden gevonden met het [test programma voor connector poorten](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Feedback over de locaties van het Data Center van de toepassings proxy 
Er kunnen Azure-data centers zijn die nog geen toepassings proxy bevatten, maar dit kan leiden tot een geweldige latentie verbetering voor u. De locatie van het Data Center verzenden naar aadapfeedback@microsoft.com . Micro soft gebruikt uw feedback voor uitbreidings abonnementen.

Micro soft werkt aan aanvullende mogelijkheden om de latentie te verbeteren. Zodra deze verbeteringen beschikbaar zijn, wordt de documentatie bijgewerkt.

## <a name="next-steps"></a>Volgende stappen
[Werken met bestaande on-premises proxyservers](application-proxy-configure-connectors-with-proxy-servers.md)
