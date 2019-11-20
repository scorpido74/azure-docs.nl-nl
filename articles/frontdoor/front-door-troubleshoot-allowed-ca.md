---
title: Toegestane CA voor het inschakelen van aangepaste HTTPS op de Azure front-deur service
description: Als u uw eigen certificaat gebruikt om HTTPS in te scha kelen op een aangepast domein, moet u een toegestane certificerings instantie (CA) gebruiken om deze te maken.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 62420889d9a4cb1e9d1c570a0845c704fca56cb3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184588"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Toegestane certificerings instanties voor het inschakelen van aangepaste HTTPS op de Azure front-deur service

Wanneer u [de https-functie inschakelt met behulp van uw eigen certificaat](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), moet u voor een aangepast domein van de Azure front-deur service een toegestane certificerings instantie (CA) gebruiken om uw SSL-certificaat te maken. Anders wordt de aanvraag geweigerd als u een niet-toegestane CA of een zelfondertekend certificaat gebruikt.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
