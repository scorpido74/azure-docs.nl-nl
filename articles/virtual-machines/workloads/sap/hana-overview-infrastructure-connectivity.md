---
title: Infrastructuur en connectiviteit met SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Configureer de vereiste connectiviteitsinfrastructuur om SAP HANA op Azure (grote exemplaren) te gebruiken.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cea89087742f1987f693b8bfb627bd71038a0c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616924"
---
# <a name="sap-hana-large-instances-deployment"></a>SAP HANA (grote exemplaren) implementatie 

In dit artikel wordt ervan uitgegaan dat u uw aankoop van SAP HANA op Azure (grote exemplaren) van Microsoft hebt voltooid. Voor het lezen van dit artikel, voor algemene achtergrond, zie [HANA grote gevallen gemeenschappelijke termen](hana-know-terms.md) en [HANA grote exemplaren SKU's](hana-available-skus.md).


Microsoft heeft de volgende informatie nodig om HANA-eenheden voor grote instanties te implementeren:

- Naam van de klant.
- Zakelijke contactgegevens (inclusief e-mailadres en telefoonnummer).
- Technische contactgegevens (inclusief e-mailadres en telefoonnummer).
- Contactgegevens voor technische netwerken (inclusief e-mailadres en telefoonnummer).
- Azure-implementatieregio (bijvoorbeeld West-VS, Australië-Oost of Noord-Europa).
- SAP HANA op Azure (grote exemplaren) SKU (configuratie).
- Voor elke Azure-implementatieregio:
    - Een IP-adresbereik van 29 voor ER-P2P-verbindingen die virtuele Azure-netwerken verbinden met grote exemplaren van HANA.
    - Een /24 CIDR-blok dat wordt gebruikt voor de IP-pool van de HANA-server voor grote exemplaren.
    - Optioneel wanneer u [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) gebruikt om directe routering van on-premises naar HANA-eenheden voor grote instanties of routering tussen HANA-eenheden voor grote instanties in verschillende Azure-regio's in te schakelen, moet u een ander IP-adresbereik van 29 /29 reserveren. Dit specifieke bereik overlapt mogelijk niet met een van de andere IP-adresbereiken die u eerder hebt gedefinieerd.
- De IP-adresbereikwaarden die worden gebruikt in het kenmerk van de virtuele netwerkadresruimte van elk virtueel Azure-netwerk dat verbinding maakt met de grote exemplaren van de HANA.
- Gegevens voor elk HANA-systeem voor grote instanties:
  - Gewenste hostnaam, idealiter met een volledig gekwalificeerde domeinnaam.
  - Gewenst IP-adres voor de HANA-eenheid voor grote instanties buiten het IP-pooladresbereik van de server. (De eerste 30 IP-adressen in het IP-pooladresbereik van de server zijn gereserveerd voor intern gebruik binnen HANA-grote exemplaren.)
  - SAP HANA SID naam voor de SAP HANA instantie (nodig om de nodige SAP HANA-gerelateerde schijfvolumes te creëren). Microsoft heeft de HANA SID nodig voor het maken van de machtigingen voor sidadm op de NFS-volumes. Deze volumes hechten aan de HANA grote instantie eenheid. De HANA SID wordt ook gebruikt als een van de naamcomponenten van de schijfvolumes die worden gemonteerd. Als u meer dan één HANA-exemplaar op het apparaat wilt uitvoeren, moet u meerdere HANA-ID's vermelden. Elk krijgt een aparte set van volumes toegewezen.
  - In het Linux OS heeft de sidadm-gebruiker een groeps-ID. Deze ID is vereist om de benodigde SAP HANA-gerelateerde schijfvolumes te maken. De SAP HANA-installatie creëert meestal de sapsys-groep, met een groeps-ID van 1001. De sidadm gebruiker maakt deel uit van die groep.
  - In het Linux OS heeft de sidadm-gebruiker een gebruikers-ID. Deze ID is vereist om de benodigde SAP HANA-gerelateerde schijfvolumes te maken. Als u meerdere HANA-exemplaren op het apparaat uitvoert, vermeldt u alle sidadm-gebruikers. 
- De Azure-abonnements-id voor het Azure-abonnement waarmee SAP HANA op Azure HANA-grote exemplaren rechtstreeks verbonden zullen zijn. Deze abonnements-ID verwijst naar het Azure-abonnement, dat wordt in rekening gebracht met de HANA-eenheid of -eenheden.

Nadat u de voorgaande informatie hebt verstrekt, voorziet Microsoft SAP HANA op Azure (grote exemplaren). Microsoft stuurt u informatie om uw Virtuele Azure-netwerken te koppelen aan grote exemplaren van HANA. U ook toegang krijgen tot de HANA grote instance units.

Gebruik de volgende reeks om verbinding te maken met de HANA-grote exemplaren nadat Microsoft deze heeft geïmplementeerd:

1. [Azure VM's verbinden met grote exemplaren van HANA](hana-connect-azure-vm-large-instances.md)
2. [Een VNet verbinden met HANA grote exemplaren ExpressRoute](hana-connect-vnet-express-route.md)
3. [Aanvullende netwerkvereisten (optioneel)](hana-additional-network-requirements.md)

