---
title: Infrastructuur FQDN voor Azure Firewall
description: Azure Firewall bevat een ingebouwde regelverzameling voor infrastructuur-FQDN’s die standaard zijn toegestaan.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168985"
---
# <a name="infrastructure-fqdns"></a>FQDN's voor infrastructuur

Azure Firewall bevat een ingebouwde regelverzameling voor infrastructuur-FQDN’s die standaard zijn toegestaan. Deze FQDN’s zijn specifiek voor het platform en kunnen niet voor andere doeleinden worden gebruikt. 

De volgende services zijn opgenomen in de ingebouwde regelverzameling:

- Compute access to storage Platform Image Repository (PIR)
- Statusopslagbeheer beheerde schijven
- Azure Diagnostics and Logging (MDS)

## <a name="overriding"></a>Dwingende 

U deze ingebouwde infrastructuurregelverzameling overschrijven door een volledige verzameling van toepassingsregels te maken die als laatste is verwerkt. Deze wordt altijd vóór de infrastructuurregelverzameling verwerkt. Alles wat niet in de infrastructuurregelverzameling staat, wordt standaard geweigerd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren en configureren van een Azure Firewall](tutorial-firewall-deploy-portal.md).