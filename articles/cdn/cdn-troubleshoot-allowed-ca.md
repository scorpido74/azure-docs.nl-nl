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
ms.openlocfilehash: 81d2209c4b76db685e5a8d2625c84469d5c3dc43
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985844"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Certificaatautoriteiten toestaan voor het inschakelen van aangepaste HTTPS op Azure CDN

U moet voldoen aan specifieke certificaatvereisten wanneer u [de HTTPS-functie inschakelt met behulp van uw eigen certificaat](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) voor een aangepast Azure Content Delivery Network (CDN) domein. De **Azure CDN-standaard van Microsoft-profiel** vereist een certificaat van een van de goedgekeurde certificaatautoriteiten (CA) in de volgende lijst. Als een certificaat van een niet-goedgekeurde CA of als een zelfondertekend certificaat wordt gebruikt, wordt de aanvraag afgewezen. **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon-profielen** accepteren elk geldig certificaat van elke geldige CA.

> [!NOTE]
> De optie om uw eigen certificaat te gebruiken om de aangepaste domein-HTTPS-functie in te schakelen, is *niet* beschikbaar voor **Azure CDN Standard van Akamai-profielen.** 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

