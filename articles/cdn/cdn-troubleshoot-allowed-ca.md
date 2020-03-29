---
title: Ca toegestaan voor het inschakelen van aangepaste HTTPS op Azure CDN
description: Als u uw eigen certificaat gebruikt om HTTPS in te schakelen op een aangepast domein, moet u een toegestane certificaatautoriteit (CA) gebruiken om het te maken.
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
ms.openlocfilehash: 7b71611d43bc2d4de4c3e609462906c44fba0443
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919971"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Certificaatautoriteiten toestaan voor het inschakelen van aangepaste HTTPS op Azure CDN

U moet voldoen aan specifieke certificaatvereisten wanneer u [de HTTPS-functie inschakelt met behulp van uw eigen certificaat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) voor een aangepast Azure Content Delivery Network (CDN) domein. De **Azure CDN-standaard van Microsoft-profiel** vereist een certificaat van een van de goedgekeurde certificaatautoriteiten (CA) in de volgende lijst. Als een certificaat van een niet-goedgekeurde CA of als een zelfondertekend certificaat wordt gebruikt, wordt de aanvraag afgewezen. **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon-profielen** accepteren elk geldig certificaat van elke geldige CA.

> [!NOTE]
> De optie om uw eigen certificaat te gebruiken om de aangepaste domein-HTTPS-functie in te schakelen, is *niet* beschikbaar voor **Azure CDN Standard van Akamai-profielen.** 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

