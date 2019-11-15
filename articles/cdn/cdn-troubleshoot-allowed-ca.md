---
title: Toegestane CA voor het inschakelen van aangepaste HTTPS op Azure CDN
description: Als u uw eigen certificaat gebruikt om HTTPS in te scha kelen op een aangepast domein, moet u een toegestane certificerings instantie (CA) gebruiken om deze te maken.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5462502514a3e327913122fe99fd699856891216
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083108"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Toegestane certificerings instanties voor het inschakelen van aangepaste HTTPS op Azure CDN

Wanneer u [de https-functie inschakelt met behulp van uw eigen certificaat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), moet u een aangepaste certificerings instantie (CA) voor het maken van uw SSL-certificaat gebruiken voor een aangepast Azure Content Delivery Network (CDN)-domein op een **Azure CDN standaard van micro soft** endpoint. Anders wordt de aanvraag geweigerd als u een niet-toegestane CA of een zelfondertekend certificaat gebruikt.

> [!NOTE]
> De optie voor het gebruik van uw eigen certificaat om aangepaste HTTPS in te scha kelen is alleen beschikbaar voor **Azure CDN standaard van micro soft** -profielen. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

