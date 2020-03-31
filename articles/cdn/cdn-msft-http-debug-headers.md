---
title: HTTP-headers voor Azure CDN van Microsoft debuggen | Microsoft Documenten
description: Foutopsporingscacheaanvragen bevat aanvullende informatie over het cachebeleid dat is toegepast op het aangevraagde element. Deze headers zijn specifiek voor Azure CDN van Microsoft.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68814068"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Foutopsporing HTTP-header voor Azure CDN van Microsoft
De foutopsporingsantwoordkop `X-Cache`geeft details over welke laag van de CDN-stack de inhoud is aangeboden. Deze header is specifiek voor Azure CDN van Microsoft.

### <a name="response-header-format"></a>Indeling voor antwoordkoppen

Header | Beschrijving
-------|------------
X-Cache: TCP_HIT | Deze koptekst wordt geretourneerd wanneer de inhoud wordt weergegeven vanuit de CDN-randcache. 
X-Cache: TCP_REMOTE_HIT | Deze koptekst wordt geretourneerd wanneer de inhoud wordt weergegeven vanuit de regionale cache van CDN (Origin shield-laag)
X-Cache: TCP_MISS | Deze header wordt geretourneerd wanneer er een cache ontbreekt en de inhoud wordt weergegeven vanuit de Origin. 


