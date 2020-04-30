---
title: Voorbereiden op een nood herstel van de VMware-VM met Azure Site Recovery
description: Leer hoe u on-premises VMware-servers kunt voorbereiden op herstel naar Azure na een noodgeval met behulp van de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4969a1f14e53aabf79495e179213f9763d4c8803
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79238858"
---
# <a name="prepare-on-premises-vmware-servers-for-disaster-recovery-to-azure"></a>On-premises VMware-servers voorbereiden op herstel naar Azure na een noodgeval

In dit artikel wordt beschreven hoe u on-premises VMware-servers voorbereidt op herstel na nood geval met behulp van de [Azure site Recovery](site-recovery-overview.md) Services. 

Dit is de tweede zelfstudie in een reeks waarin u ziet hoe u herstel na noodgeval kunt instellen voor Azure voor on-premises VMware-VM’s. In de eerste zelfstudie hebben we de [Azure-onderdelen ingesteld](tutorial-prepare-azure.md) die nodig zijn voor VMware-noodherstel.


In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Bereid een account voor op de vCenter-Server of vSphere ESXi-host om de VM-detectie te automatiseren.
> * Een account voorbereiden voor automatische installatie van de Mobility-service op VMware-Vm's.
> * Bekijk de vereisten en ondersteuning voor VMware-servers en-VM'S.
> * Voorbereiden om verbinding te maken met virtuele Azure-machines na een failover.

> [!NOTE]
> Zelf studies laten u de eenvoudigste implementatie traject voor een scenario zien. Waar mogelijk wordt gebruikgemaakt van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg het artikel in het gedeelte How to van de inhouds opgave van Site Recovery voor gedetailleerde instructies.

## <a name="before-you-start"></a>Voordat u begint

Zorg ervoor dat u Azure hebt voor bereid zoals beschreven in de [eerste zelf studie in deze serie](tutorial-prepare-azure.md).

## <a name="prepare-an-account-for-automatic-discovery"></a>Een account voorbereiden voor automatische detectie

Site Recovery moet toegang hebben tot de VMware-servers om het volgende te kunnen doen:

- Virtuele machines automatisch detecteren. Er is minimaal een alleen-lezen-account vereist.
- Replicatie, failover en failback orkestreren. U hebt een account nodig dat bewerkingen kan uitvoeren als het maken en verwijderen van schijven en het inschakelen van VM’s.

Ga als volgt te werk om het account te maken:

1. Als u een vast account wilt gebruiken, maakt u een rol op vCenter-niveau. Geef de rol een naam zoals **Azure_Site_Recovery**.
2. Wijs de rol de rechten toe die worden samengevat in onderstaande tabel.
3. Maak een gebruiker op de vCenter-server of vSphere-host. Wijs de rol toe aan de gebruiker.

### <a name="vmware-account-permissions"></a>Machtigingen voor VMware-account

**Taak** | **Rol/machtigingen** | **Nadere**
--- | --- | ---
**VM-detectie** | Ten minste een alleen-lezen-gebruiker<br/><br/> Datacentrumobject –> doorgeven naar onderliggend object, rol=Alleen-lezen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **geen toegang** met het object **door geven naar onderliggend** item toe aan de onderliggende objecten (vSphere-hosts, gegevens opslag, vm's en netwerken).
**Volledige replicatie, failover en failback** |  Maak een rol (Azure_Site_Recovery) met de vereiste machtigingen en wijs de rol toe aan een VMware-gebruiker of -groep<br/><br/> Datacentrumobject –> Doorgeven naar onderliggend object, rol=Azure_Site_Recovery<br/><br/> Gegevensopslag –> Ruimte toewijzen, browsen in de gegevensopslag, bestandsbewerkingen op laag niveau, bestand verwijderen, bestanden van virtuele machines bijwerken<br/><br/> Netwerk –> Netwerk toewijzen<br/><br/> Resource –> VM toewijzen aan resourcegroep, uitgeschakelde VM migreren, ingeschakelde VM migreren<br/><br/> Taken –> Taak maken, taak bijwerken<br/><br/> Virtuele machine –> Configuratie<br/><br/> Virtuele machine –> Interactie –> vraag beantwoorden, apparaatverbinding, CD's configureren, diskettes configureren, uitschakelen, inschakelen, VMware-hulpprogramma's installeren<br/><br/> Virtuele machine -> Inventaris –> Maken, registreren, registratie opheffen<br/><br/> Virtuele machine –> Inrichten –> Downloaden van virtuele machine toestaan, toestaan dat bestanden van virtuele machines worden geüpload<br/><br/> Virtuele machine –> Momentopnamen -> Momentopnamen verwijderen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **geen toegang** met het object **door geven naar onderliggend** item toe aan de onderliggende objecten (vSphere-hosts, gegevens opslag, vm's en netwerken).

## <a name="prepare-an-account-for-mobility-service-installation"></a>Een account voorbereiden voor installatie van de Mobility-service

De Mobility-service moet zijn geïnstalleerd op de machines die u wilt repliceren. Met Site Recovery kunt u een push-installatie van deze service uitvoeren wanneer u replicatie voor een machine inschakelt. U kunt deze ook handmatig installeren of een installatieprogramma gebruiken.

- In deze zelfstudie gaat u de Mobility-service installeren met een push-installatie.
- Voor deze push-installatie moet u een account voorbereiden dat door Site Recovery kan worden gebruikt om toegang te krijgen tot de VM. U geeft dit account op wanneer u herstel na noodgevallen instelt in de Azure-console.

U bereidt het account als volgt voor:

Bereid een domein of lokaal account met machtigingen voor om op de VM te installeren.

- **Windows-VM’s**: als u wilt installeren op Windows-VM's en geen domeinaccount gebruikt, moet u toegangsbeheer voor externe gebruikers uitschakelen op de lokale computer. U doet dit door in het register > **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** de DWORD-vermelding **LocalAccountTokenFilterPolicy** met een waarde van 1 toe te voegen.
- **Linux-VM's**: voor installatie op Linux-VM’s bereidt u een superuser voor op de Linux-server van de broncomputer.


## <a name="check-vmware-requirements"></a>Vereisten voor VMware controleren

Controleer of de VMware-servers en VM's aan de vereisten voldoen.

1. [Verifieer](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) de vereisten voor VMware-servers.
2. Voor Linux-VM's [controleert](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) u het bestandssysteem en de opslagvereisten. 
3. Controleer de ondersteuning voor het on-premises [netwerk](vmware-physical-azure-support-matrix.md#network) en de [opslag](vmware-physical-azure-support-matrix.md#storage). 
4. Controleer wat er na failover wordt ondersteund voor [Azure-netwerken](vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover), [-opslag](vmware-physical-azure-support-matrix.md#azure-storage) en [-rekenkracht](vmware-physical-azure-support-matrix.md#azure-compute).
5. De on-premises VM's die u voor Azure repliceert, moeten overeenstemmen met [Azure VM-vereisten](vmware-physical-azure-support-matrix.md#azure-vm-requirements).
6. Op een virtuele Linux-machine moet de naam van het apparaat of koppelpunt uniek zijn. Zorg ervoor dat twee apparaten/koppelpunten nooit dezelfde naam hebben. Houd er rekening mee dat de naam niet hoofdletter gevoelig is. Zo is het bijvoorbeeld niet toegestaan om twee apparaten voor dezelfde VM _device1_ en _Device1_ te noemen.


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Voorbereiden op het verbinden met virtuele Azure-machines na een failover

Na een failover wilt u misschien vanaf uw on-premises netwerk verbinding maken met de Azure VM's.

Als u na een failover verbinding wilt maken met Windows-VM’s met behulp van RDP, doet u het volgende:

- **Internet toegang**. Schakel vóór een failover RDP in op de on-premises VM. Zorg dat TCP- en UDP-regels zijn toegevoegd voor het profiel **Openbaar** en dat RDP is toegestaan in **Windows Firewall** > **Toegestane apps** voor alle profielen.
- **Toegang tot site-site-VPN's**:
    - Schakel vóór een failover RDP in op de on-premises VM.
    - RDP moet worden toegestaan in de **Windows Firewall** -> **toegestane apps en functies** voor **domein-en particuliere** netwerken.
    - Controleer of het SAN-beleid van het besturingssysteem is ingesteld op **OnlineAll**. [Meer informatie](https://support.microsoft.com/kb/3031135).
- Er mogen geen Windows-updates in behandeling zijn op de VM wanneer u een failover activeert. Als dat het geval is, kunt u zich niet aanmelden bij de virtuele machine totdat de update is voltooid.
- Controleer na een failover **Diagnostische gegevens over opstarten** op de Windows Azure VM om een schermopname van de VM weer te geven. Als u geen verbinding kunt maken, controleer dan of de VM actief is en bekijk deze [tips voor het oplossen van problemen](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Als u na een failover verbinding wilt maken met Linux-VM’s met behulp van SSH, doet u het volgende:

- Controleer vóór de failover op de on-premises computer of de Secure Shell-service is ingesteld om automatisch te starten bij het opstarten van het systeem.
- Controleer of SSH-verbindingen zijn toegestaan door de firewallregels.
- Sta na de failover op de Azure VM inkomende verbindingen toe met de SSH-poort voor de netwerkbeveiligingsgroepsregels voor de VM waarvoor een failover is uitgevoerd en voor het Azure-subnet waarmee deze is verbonden.
- [Voeg een openbaar IP-adres toe](site-recovery-monitoring-and-troubleshooting.md) voor de VM.
- U kunt de **Diagnostische gegevens over opstarten** controleren om een schermopname van de VM weer te geven.


## <a name="failback-requirements"></a>Vereisten voor failback
Als u van plan bent om terug te gaan naar uw on-premises site, zijn er een aantal [vereisten voor failback](vmware-azure-reprotect.md#before-you-begin). U kunt deze nu voorbereiden, maar u hoeft dit niet te doen. U kunt voorbereiden nadat u een failover hebt uitgevoerd naar Azure.



## <a name="next-steps"></a>Volgende stappen

Herstel na nood gevallen instellen. Als u meerdere virtuele machines repliceert, plan capaciteit.
> [!div class="nextstepaction"]
> [Herstel na nood geval instellen op Azure voor VMware-vm's](vmware-azure-tutorial.md)
> [capaciteits planning uitvoeren](site-recovery-deployment-planner.md).
