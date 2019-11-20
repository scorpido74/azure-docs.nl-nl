---
title: Architectuur van replicatie-apparaat Azure Migrate
description: Biedt een overzicht van het Azure Migrate replicatie-apparaat voor migratie op basis van een agent.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ba14767bde5d6cdca3a82dbe4e8a115ec25cc911
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186559"
---
# <a name="replication-appliance"></a>Replicatie apparaat

In dit artikel wordt het replicatie apparaat beschreven dat wordt gebruikt door Azure Migrate: Server evaluatie bij het migreren van virtuele VMware-machines, fysieke machines en persoonlijke/open bare Cloud-Vm's naar Azure, met behulp van een migratie op basis van een agent. 

Het hulp programma is beschikbaar in de [Azure migrate](migrate-overview.md) hub. De hub biedt systeem eigen hulpprogram ma's voor evaluatie en migratie, evenals hulpprogram ma's van andere Azure-Services en onafhankelijke software leveranciers (Isv's) van derden.


## <a name="appliance-overview"></a>Overzicht van apparaten

Het replicatie apparaat wordt geïmplementeerd als één on-premises computer, hetzij als een virtuele VMware-machine of een fysieke server. Deze wordt uitgevoerd:
- **Replicatie apparaat**: het replicatie apparaat coördineert communicatie en beheert gegevens replicatie voor on-premises virtuele VMware-machines en fysieke servers die repliceren naar Azure.
- **Proces server**: de proces server, die standaard wordt geïnstalleerd op het replicatie apparaat, en doet het volgende:
    - **Replicatie gateway**: deze fungeert als replicatie gateway. Het ontvangt replicatie gegevens van machines die zijn ingeschakeld voor replicatie. Het optimaliseert replicatie gegevens met caching, compressie en versleuteling, en verzendt deze naar Azure.
    - **Agent installatie programma**: voert een push-installatie van de Mobility-service uit. Deze service moet zijn geïnstalleerd en worden uitgevoerd op elke on-premises machine die u voor migratie wilt repliceren.

## <a name="appliance-deployment"></a>Implementatie van het apparaat

**Implementeren als** | **Gebruikt voor** | **Details**
--- | --- |  ---
VMware-VM | Wordt meestal gebruikt bij het migreren van virtuele VMware-machines met behulp van het hulp programma voor Azure Migrate migratie met migratie op basis van een agent. | U kunt de eicellen-sjabloon downloaden van de Azure Migrate hub en importeren in vCenter Server om de apparaat-VM te maken.
Een fysieke computer | Wordt gebruikt bij het migreren van on-premises fysieke servers als u geen VMware-infra structuur hebt of als u geen virtuele VMware-machine kunt maken met behulp van een eicellen-sjabloon. | U downloadt een software-installatie programma van de Azure Migrate hub en voert het uit om de apparaatapparaat in te stellen.

## <a name="appliance-deployment-requirements"></a>Vereisten voor de implementatie van het apparaat

[Controleer](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) de implementatie vereisten.



## <a name="appliance-license"></a>Toestel licentie
Het apparaat wordt geleverd met een evaluatie licentie voor Windows Server 2016, die voor 180 dagen geldig is. Als de evaluatie periode bijna is verlopen, raden wij aan dat u een nieuw apparaat downloadt en implementeert, of dat u de licentie voor het besturings systeem van de apparaat-VM activeert.

## <a name="replication-process"></a>Replicatieproces

1. Wanneer u replicatie voor een virtuele machine inschakelt, begint de initiële replicatie naar Azure Storage met behulp van het opgegeven replicatie beleid. 
2. Verkeer wordt gerepliceerd naar open bare eind punten van Azure Storage via internet. Het repliceren van verkeer via een site-naar-site virtueel particulier netwerk (VPN) van een on-premises site naar Azure wordt niet ondersteund.
3. Nadat de initiële replicatie is voltooid, begint de replicatie van verschillen. Bijgehouden wijzigingen voor een machine worden vastgelegd.
4. Communicatie gebeurt als volgt:
    - Vm's communiceren met het replicatie apparaat op poort HTTPS 443 inkomend voor replicatie beheer.
    - Het replicatie apparaat organiseert de replicatie met Azure via poort HTTPS 443 uitgaand.
    - Vm's verzenden replicatie gegevens naar de proces server (die wordt uitgevoerd op het replicatie apparaat) op poort HTTPS 9443-binnenkomend. Deze poort kan worden gewijzigd.
    - De proces server ontvangt replicatie gegevens, optimaliseert en versleutelt deze en verzendt deze naar Azure Storage via poort 443 uitgaand.
5. De replicatie gegevens melden het eerste land in een cache-opslag account in Azure. Deze logboeken worden verwerkt en de gegevens worden opgeslagen op een door Azure beheerde schijf.

![Architectuur](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Toestel-upgrades

Het apparaat wordt hand matig geüpgraded vanuit de Azure Migrate hub. U wordt aangeraden altijd de meest recente versie uit te voeren.

1. In Azure Migrate > servers > Azure Migrate: Server evaluatie, infrastructuur servers, klik op **configuratie servers**.
2. In **configuratie servers**wordt een koppeling weer gegeven in de **Agent versie** wanneer er een nieuwe versie van het replicatie apparaat beschikbaar is. 
3. Down load het installatie programma naar de replicatie apparaat computer en installeer de upgrade. Het installatie programma detecteert de versie die momenteel wordt uitgevoerd op het apparaat.
 
## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](tutorial-assess-vmware.md#set-up-the-appliance-vm) het instellen van het apparaat voor VMware.
[Meer informatie over](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) het instellen van het apparaat voor Hyper-V.

