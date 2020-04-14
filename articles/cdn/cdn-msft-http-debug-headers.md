---
title: HTTP-headers voor Azure CDN van Microsoft debuggen | Microsoft Documenten
description: Foutopsporingscacheaanvragen bevat aanvullende informatie over het cachebeleid dat is toegepast op het aangevraagde element. Deze headers zijn specifiek voor Azure CDN van Microsoft.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: allensu
ms.openlocfilehash: 2475bdce3ab8f153cc837601964bf4a2e90a470c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260408"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Foutopsporing HTTP-header voor Azure CDN van Microsoft
De foutopsporingsantwoordkop `X-Cache`geeft details over welke laag van de CDN-stack de inhoud is aangeboden. Deze header is specifiek voor Azure CDN van Microsoft.

### <a name="response-header-format"></a>Indeling voor antwoordkoppen

Header | Beschrijving
-------|------------
X-Cache: TCP_HIT | Deze koptekst wordt geretourneerd wanneer de inhoud wordt weergegeven vanuit de CDN-randcache. 
X-Cache: TCP_REMOTE_HIT | Deze koptekst wordt geretourneerd wanneer de inhoud wordt weergegeven vanuit de regionale cache van CDN (Origin shield-laag)
X-Cache: TCP_MISS | Deze header wordt geretourneerd wanneer er een cache ontbreekt en de inhoud wordt weergegeven vanuit de Origin. 


