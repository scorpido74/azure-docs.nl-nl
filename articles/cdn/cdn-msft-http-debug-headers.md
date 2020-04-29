---
title: Fout opsporing voor HTTP-headers voor Azure CDN van micro soft | Microsoft Docs
description: Debug cache-aanvraag headers bieden aanvullende informatie over het cache beleid dat wordt toegepast op de aangevraagde Asset. Deze headers zijn specifiek voor Azure CDN van micro soft.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81260408"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Fout opsporing voor de HTTP-header voor Azure CDN van micro soft
De antwoord header `X-Cache`voor fout opsporing bevat details over de laag van de CDN-stack waaruit de inhoud afkomstig is. Deze header is specifiek voor Azure CDN van micro soft.

### <a name="response-header-format"></a>Indeling van de antwoord header

Header | Beschrijving
-------|------------
X-cache: TCP_HIT | Deze header wordt geretourneerd wanneer de inhoud wordt geleverd vanuit de cache van de CDN-rand. 
X-cache: TCP_REMOTE_HIT | Deze header wordt geretourneerd wanneer de inhoud wordt geleverd vanuit de regionale cache van de CDN (oorspronkelijke afschermings laag)
X-cache: TCP_MISS | Deze header wordt geretourneerd als er een cache-Missing is en de inhoud van de oorsprong wordt geleverd. 


