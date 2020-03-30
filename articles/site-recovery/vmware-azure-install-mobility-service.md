---
title: Bronmachines voorbereiden om de Mobiliteitsservice te installeren via push-installatie voor noodherstel van Vm's en fysieke servers naar Azure | Microsoft Documenten
description: Meer informatie over hoe u uw server voorbereidt op het installeren van een Mobiliteitsagent door middel van push-installatie voor noodherstel van VMware VM's en fysieke servers naar Azure met behulp van de Azure Site Recovery-service.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: a2f4bdb96b8d1ecb23ddcec844726439ec46fff2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186458"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Bereid de bronmachine voor op de push-installatie van mobiliteitsagent

Wanneer u disaster recovery instelt voor Vm's en fysieke servers met VMware-vm's en fysieke servers met [Azure Site Recovery,](site-recovery-overview.md)installeert u de [Service Site Recovery Mobility](vmware-physical-mobility-service-overview.md) op elke on-premises Vm en fysieke server.  De Mobiliteitsservice legt gegevensop de machine vast en stuurt deze door naar de siteherstelprocesserver.

## <a name="install-on-windows-machine"></a>Installeren op Windows-machine

Ga als volgt te werk op elke Windows-machine die u wilt beveiligen:

1. Zorg ervoor dat er netwerkconnectiviteit is tussen de machine en de processerver. Als u geen afzonderlijke processerver hebt ingesteld, wordt deze standaard uitgevoerd op de configuratieserver.
1. Maak een account dat op de processerver kan worden gebruikt voor toegang tot de computer. Het account moet beheerdersrechten hebben, lokaal of domein. Gebruik dit account alleen voor de push-installatie en voor agentupdates.
2. Als u geen domeinaccount gebruikt, schakelt u extern gebruikerstoegangsbesturingselement op de lokale computer als volgt uit:
    - Voeg onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System registry key een nieuwe DWORD: **LocalAccountTokenFilterPolicy**toe. Stel de waarde in op **1**.
    -  Voer de volgende opdracht uit om dit bij een opdrachtprompt uit te voeren:  
   'REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. Selecteer Een app of functie toestaan **via Firewall in**Windows Firewall op de machine die u wilt beveiligen. Bestand **en printerdelen en** Windows Management Instrumentation **(WMI)** inschakelen. Voor computers die tot een domein behoren, u de firewall-instellingen configureren met behulp van een groepsbeleidsobject (GPO).

   ![Firewallinstellingen](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Voeg het account toe dat u hebt gemaakt in CSPSConfigtool. Meld u hiervoor aan bij uw configuratieserver.
5. Open **cspsconfigtool.exe**. Het is beschikbaar als snelkoppeling op het bureaublad en in de map %ProgramData%\ASR\home\svsystems\bin.
6. Selecteer **account** **toevoegen**op het tabblad Accounts beheren .
7. Voeg het account toe dat u hebt gemaakt.
8. Voer de referenties in die u gebruikt wanneer u replicatie voor een computer inschakelt.

## <a name="install-on-linux-machine"></a>Installeren op Linux-machine

Ga op elke Linux-machine die u wilt beschermen het volgende uit:

1. Zorg ervoor dat er netwerkconnectiviteit is tussen de Linux-machine en de processerver.
2. Maak een account dat op de processerver kan worden gebruikt voor toegang tot de computer. Het account moet een **rootgebruiker** zijn op de Linux-bronserver. Gebruik dit account alleen voor de push-installatie en voor updates.
3. Controleer of het bestand /etc/hosts op de Linux-bronserver vermeldingen bevat die de lokale hostnaam toewijzen aan IP-adressen die zijn gekoppeld aan alle netwerkadapters.
4. Installeer de meest recente openssh, openssh-server en openssl-pakketten op de computer die u wilt repliceren.
5. Zorg ervoor dat SSH (Secure Shell) is ingeschakeld en wordt uitgevoerd op poort 22.
4. Schakel het SFTP-subsysteem en wachtwoordverificatie in het sshd_config-bestand in. Log hiervoor in als **root**.
5. Zoek in het bestand **/etc/ssh/sshd_config** de regel die begint met **PasswordAuthentication.**
6. Geef de regel geen commentaar en wijzig de waarde in **ja**.
7. Zoek de regel die begint met **Subsysteem**en geef geen commentaar op de regel.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Start de service **sshd** opnieuw.
9. Voeg het account toe dat u hebt gemaakt in CSPSConfigtool. Meld u hiervoor aan bij uw configuratieserver.
10. Open **cspsconfigtool.exe**. Het is beschikbaar als snelkoppeling op het bureaublad en in de map %ProgramData%\home\svsystems\bin.
11. Selecteer **account** **toevoegen**op het tabblad Accounts beheren .
12. Voeg het account toe dat u hebt gemaakt.
13. Voer de referenties in die u gebruikt wanneer u replicatie voor een computer inschakelt.

## <a name="anti-virus-on-replicated-machines"></a>Anti-virus op gerepliceerde machines

Als machines die u wilt repliceren, actieve antivirussoftware worden uitgevoerd, moet u ervoor zorgen dat u de installatiemap van de Mobiliteitsservice uitsluit van antivirusbewerkingen (*C:\ProgramData\ASR\agent).* Dit zorgt ervoor dat replicatie werkt zoals verwacht.

## <a name="next-steps"></a>Volgende stappen

Nadat de Mobiliteitsservice is geïnstalleerd, selecteert u in de Azure-portal **+ Repliceren** om deze VM's te beveiligen. Meer informatie over het inschakelen van replicatie voor Vm's en [fysieke servers](physical-azure-disaster-recovery.md#enable-replication)van [VMware.](vmware-azure-enable-replication.md)


