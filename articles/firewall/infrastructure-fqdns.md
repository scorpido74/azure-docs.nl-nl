---
title: De FQDN van de infra structuur voor Azure Firewall
description: Meer informatie over infrastructuur FQDN-Azure Firewall
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: 5b134058e1924bae030338411226a9a6aff46557
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130208"
---
# <a name="infrastructure-fqdns"></a>FQDN's voor infrastructuur

Azure Firewall bevat een ingebouwde regelverzameling voor infrastructuur-FQDN’s die standaard zijn toegestaan. Deze FQDN’s zijn specifiek voor het platform en kunnen niet voor andere doeleinden worden gebruikt. 

De volgende services zijn opgenomen in de ingebouwde regel verzameling:

- Berekenings toegang tot opslag platform installatie kopie opslagplaats (PIR)
- Toegang tot de status van beheerde schijven
- Azure Diagnostics en logboek registratie (MDS)

## <a name="overriding"></a>Overschrijven 

U kunt deze ingebouwde infrastructuur regel verzameling overschrijven door het maken van een alle toepassings regel verzameling weigeren die het laatst is verwerkt. Deze wordt altijd vóór de infrastructuurregelverzameling verwerkt. Alles wat niet in de infrastructuurregelverzameling staat, wordt standaard geweigerd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren en configureren van een Azure firewall](tutorial-firewall-deploy-portal.md).