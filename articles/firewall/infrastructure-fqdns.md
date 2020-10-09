---
title: De FQDN van de infra structuur voor Azure Firewall
description: Azure Firewall bevat een ingebouwde regelverzameling voor infrastructuur-FQDN’s die standaard zijn toegestaan.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74168985"
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