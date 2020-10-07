---
title: VMware HCX-netwerksegmenten
description: Er zijn vier netwerken vereist voor VMware HCX.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: f01aba13d688dfed573acfc8c4344511fbfd9fa8
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578741"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Er zijn vier netwerken vereist voor VMware HCX:

- **SDN-beheernetwerk:** Meestal is dit hetzelfde beheernetwerk dat wordt gebruikt voor het vSphere-cluster.  Identificeer ten minste twee IP-adressen op dit netwerksegment voor VMware-HCX (er kunnen meer nummers nodig zijn, afhankelijk van uw implementatie).

- **vMotion-netwerk:** Meestal is dit hetzelfde netwerk dat wordt gebruikt voor vMotion in de vSphere-cluster.  Identificeer ten minste twee IP-adressen op dit netwerksegment voor VMware-HCX (er kunnen meer nummers nodig zijn, afhankelijk van uw implementatie).  

   Het netwerk van vMotion moet zichtbaar zijn op een gedistribueerde virtuele switch of vSwitch0. Als dat niet het geval is, wijzig dan de omgeving.

   > [!NOTE]
   > Als dit netwerk niet gerouteerd (privé) is, vormt dat geen probleem.

- **Uplinknetwerk:** Maak een nieuw netwerk voor VMware HCX Uplink en breidt dit naar uw vSphere-cluster uit via een poortgroep.  Identificeer ten minste twee IP-adressen op dit netwerksegment voor VMware-HCX (er kunnen meer nummers nodig zijn, afhankelijk van uw implementatie).  

   > [!NOTE]
   > De aanbevolen methode is om een /29-netwerk te maken, maar elke netwerkgrootte volstaat.

- **Replicatienetwerk:** Maak een nieuw netwerk voor VMware HCX Replication en breidt dit naar uw vSphere-cluster uit via een poortgroep.  Identificeer ten minste twee IP-adressen op dit netwerksegment voor VMware-HCX (er kunnen meer nummers nodig zijn, afhankelijk van uw implementatie).

   > [!NOTE]
   > De aanbevolen methode is om een /29-netwerk te maken, maar elke netwerkgrootte volstaat.