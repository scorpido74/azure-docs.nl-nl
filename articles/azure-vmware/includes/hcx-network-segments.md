---
title: VMware HCX-netwerksegmenten
description: Er zijn vier netwerken vereist voor VMware HCX.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 8137b4383d2a243d53db317db6f5a78b3bc68e67
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173626"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

Er zijn vier netwerken vereist voor VMware HCX:

- **Beheernetwerk:** Meestal is dit hetzelfde beheernetwerk dat wordt gebruikt voor het vSphere-cluster. Identificeer minimaal twee IP-adressen in dit netwerksegment voor VMware-HCX. (Wellicht hebt u er meer nodig, afhankelijk van uw implementatie.)

- **vMotion-netwerk:** Meestal is dit hetzelfde netwerk dat wordt gebruikt voor vMotion in de vSphere-cluster.  Identificeer minimaal twee IP-adressen in dit netwerksegment voor VMware-HCX. (Wellicht hebt u er meer nodig, afhankelijk van uw implementatie.)  

   Het netwerk van vMotion moet zichtbaar zijn op een gedistribueerde virtuele switch of vSwitch0. Als dat niet het geval is, wijzig dan de omgeving.

   > [!NOTE]
   > Als dit netwerk niet gerouteerd (privé) is, vormt dat geen probleem.

- **Uplinknetwerk:** Maak een nieuw netwerk voor VMware HCX Uplink en breidt dit naar uw vSphere-cluster uit via een poortgroep. Identificeer minimaal twee IP-adressen in dit netwerksegment voor VMware-HCX. (Wellicht hebt u er meer nodig, afhankelijk van uw implementatie.)  

   > [!NOTE]
   > De aanbevolen methode is om een /29-netwerk te maken, maar elke netwerkgrootte volstaat.

- **Replicatienetwerk:** Maak een nieuw netwerk voor VMware HCX Replication en breidt dit naar uw vSphere-cluster uit via een poortgroep. Identificeer minimaal twee IP-adressen in dit netwerksegment voor VMware-HCX. (Wellicht hebt u er meer nodig, afhankelijk van uw implementatie.)

   > [!NOTE]
   > De aanbevolen methode is om een /29-netwerk te maken, maar elke netwerkgrootte volstaat.