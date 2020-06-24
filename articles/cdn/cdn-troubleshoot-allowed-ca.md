---
title: Toegestane CA voor het inschakelen van aangepaste HTTPS op Azure CDN
description: Als u uw eigen certificaat gebruikt om HTTPS in te scha kelen op een aangepast domein, moet u een toegestane certificerings instantie (CA) gebruiken om deze te maken.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 29b6cb25e021e86ce6663b4db5c89217aaf70a37
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84887404"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Toegestane certificerings instanties voor het inschakelen van aangepaste HTTPS op Azure CDN

U moet voldoen aan specifieke certificaat vereisten wanneer u [de https-functie inschakelt met behulp van uw eigen certificaat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) voor een aangepast domein van Azure Content Delivery Network (CDN). Voor de **Azure CDN standaard van micro soft** -profiel is een certificaat van een van de goedgekeurde certificerings instanties (CA) in de volgende lijst vereist. Als een certificaat van een niet-goedgekeurde certificerings instantie of een zelfondertekend certificaat wordt gebruikt, wordt de aanvraag geweigerd. **Azure CDN standaard van Verizon** en **Azure CDN Premium van Verizon** -profielen accepteren een geldig certificaat van een geldige certificerings instantie.

> [!NOTE]
> De optie voor het gebruik van uw eigen certificaat om de HTTPS-functie van het aangepaste domein in te scha kelen is *niet* beschikbaar voor **Azure CDN standaard van Akamai** -profielen. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

