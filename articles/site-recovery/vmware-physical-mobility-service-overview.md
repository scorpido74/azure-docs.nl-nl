---
title: Informatie over de Mobiliteitsservice voor noodherstel van Vm's en fysieke servers met Azure Site Recovery | Microsoft Documenten
description: Meer informatie over de Mobility Service-agent voor noodherstel van VMware VM's en fysieke servers naar Azure met behulp van de Azure Site Recovery-service.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: c5acc9637fe5afe8f7dd32d23fbdbb80373b4f61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256833"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Over de Mobility-service voor Vm's en fysieke servers van VMware

Wanneer u disaster recovery instelt voor Vm's en fysieke servers met VMware-vm's en fysieke servers met [Azure Site Recovery,](site-recovery-overview.md)installeert u de Service Site Recovery Mobility op elke on-premises Vm en fysieke server.  De Mobiliteitsservice legt gegevensop de machine vast en stuurt deze door naar de siteherstelprocesserver. U de Mobiliteitsservice implementeren met de volgende methoden:

- [Push-installatie](#push-installation): Site Recovery installeert mobiliteitsagent op de server wanneer de beveiliging is ingeschakeld via Azure portal.
- Handmatig installeren: u de Mobiliteitsservice handmatig installeren op elke machine via [de gebruikersinterface](#install-mobility-agent-through-ui) of [opdrachtprompt.](#install-mobility-agent-through-command-prompt)
- [Geautomatiseerde implementatie](vmware-azure-mobility-install-configuration-mgr.md): U de installatie automatiseren met software-implementatietools zoals Configuration Manager.

> [!NOTE]
> De Mobiliteitsagent gebruikt ongeveer 6%-10% van het geheugen op bronmachines voor Vm's of fysieke machines.

## <a name="anti-virus-on-replicated-machines"></a>Anti-virus op gerepliceerde machines

Als machines die u wilt repliceren, actieve antivirussoftware worden uitgevoerd, moet u ervoor zorgen dat u de installatiemap van de Mobiliteitsservice uitsluit van antivirusbewerkingen (*C:\ProgramData\ASR\agent).* Dit zorgt ervoor dat replicatie werkt zoals verwacht.

## <a name="push-installation"></a>Push-installatie

Push-installatie is een integraal onderdeel van de taak "[Replicatie inschakelen"](vmware-azure-enable-replication.md#enable-replication)die in de portal wordt geactiveerd. Na het kiezen van de set virtuele machines die u wilt beschermen en activeren "Replicatie inschakelen", configuratieserver duwt mobiliteitsagent op de servers, installeert de agent en volledige registratie van agent met configuratieserver. Voor een succesvolle voltooiing van deze operatie,

- Zorg ervoor dat aan alle [vereisten voor](vmware-azure-install-mobility-service.md) push-installatie wordt voldaan.
- Controleer of alle configuraties van servers onder [de ondersteuningsmatrix van VMware naar Azure DR-scenario](vmware-physical-azure-support-matrix.md)vallen.

Details van de push-installatieworkflow zijn beschreven in de volgende secties.

### <a name="from-923-version-onwards"></a>Vanaf [9.23 versie](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)

Tijdens de push-installatie van mobiliteitsagent worden de volgende stappen

1. Duwt agent op de bronmachine. Het kopiëren van de agent op de bronmachine kan mislukken als gevolg van meerdere milieufouten. Bezoek [onze richtlijnen](vmware-azure-troubleshoot-push-install.md) om push-installatiefouten op te lossen.
2. Nadat de agent met succes is gekopieerd naar de serververeistecontroles worden uitgevoerd op de server. De installatie mislukt als niet aan een of meer van de [voorwaarden](vmware-physical-azure-support-matrix.md) wordt voldaan. Als aan alle voorwaarden wordt voldaan, wordt de installatie geactiveerd.
3. Azure Site Recovery VSS-provider is geïnstalleerd op de server als onderdeel van de installatie van de Mobiliteitsagent. Deze provider wordt gebruikt om consistente toepassingspunten te genereren. Als de installatie van de VSS-provider mislukt, wordt deze stap overgeslagen en wordt de installatie van de agent voortgezet.
4. Als de installatie van een agent slaagt, maar de installatie van de VSS-provider mislukt, wordt de taakstatus gemarkeerd als 'Waarschuwing'. Dit heeft geen invloed op crash consistentie punten generatie.

    a. Om consistente toepassingspunten te genereren, raadpleegt u [onze richtlijnen](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) om de installatie van Site Recovery VSS-provider handmatig te voltooien. </br>
    b.  Als u niet wilt dat consistente toepassingspunten worden gegenereerd, [wijzigt u het replicatiebeleid](vmware-azure-set-up-replication.md#create-a-policy) om consistente toepassingspunten uit te schakelen.

### <a name="before-922-versions"></a>Vóór 9.22 versies

1. Duwt agent op de bronmachine. Het kopiëren van de agent op de bronmachine kan mislukken als gevolg van meerdere milieufouten. Bezoek [onze richtlijnen](vmware-azure-troubleshoot-push-install.md) om push-installatiefouten op te lossen.
2. Nadat de agent met succes is gekopieerd naar de serververeistecontroles worden uitgevoerd op de server. De installatie mislukt als niet aan een of meer van de [voorwaarden](vmware-physical-azure-support-matrix.md) wordt voldaan. Als aan alle voorwaarden wordt voldaan, wordt de installatie geactiveerd.
3. Azure Site Recovery VSS-provider is geïnstalleerd op de server als onderdeel van de installatie van de Mobiliteitsagent. Deze provider wordt gebruikt om consistente toepassingspunten te genereren. Als de installatie van de VSS-provider mislukt, mislukt de installatie van de agent. Gebruik [9.23 versie](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) of hoger om crashconsistent points te genereren en VSS-provider handmatig te installeren om te voorkomen dat de installatie van mobiliteitsagenten mislukt.

## <a name="install-mobility-agent-through-ui"></a>Mobiliteitsagent installeren via gebruikersinterface

### <a name="prerequisite"></a>Vereiste

- Controleer of alle configuraties van servers onder [de ondersteuningsmatrix van VMware naar Azure DR-scenario](vmware-physical-azure-support-matrix.md)vallen.
- [Zoek het installatieprogramma](#locate-installer-files) op basis van het besturingssysteem van de server.

>[!IMPORTANT]
> Als u Azure IaaS VM van de ene Azure-regio naar de andere repliceert, gebruikt u deze methode niet. Gebruik in plaats daarvan de op opdrachtlijn gebaseerde installatiemethode.

1. Kopieer het installatiebestand naar de machine en voer het uit.
2. Selecteer **in installatieoptie**de optie **Mobiliteitsservice installeren**.
3. Selecteer de installatielocatie > **installeren**.

    ![Optiepagina voor de installatie van Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Controleer de installatie in **de voortgang van de installatie**. Nadat de installatie is voltooid, selecteert u **Doorgaan naar configuratie** om de service te registreren bij de configuratieserver.

    ![Registratiepagina van Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. Geef in **Configuratieservergegevens**het IP-adres en de wachtwoordzin op die u hebt geconfigureerd.

    ![Registratiepagina van Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Selecteer **Registreren** om de registratie te voltooien.

    ![Definitieve pagina voor mobility service-registratie](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Mobiliteitsagent installeren via opdrachtprompt

### <a name="prerequisite"></a>Vereiste

- Controleer of alle configuraties van servers onder [de ondersteuningsmatrix van VMware naar Azure DR-scenario](vmware-physical-azure-support-matrix.md)vallen.
- [Zoek het installatieprogramma](#locate-installer-files) op basis van het besturingssysteem van de server.

### <a name="on-a-windows-machine"></a>Op een Windows-machine

- Kopieer het installatieprogramma naar een lokale map (bijvoorbeeld C:\Temp) op de server die u wilt beveiligen.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Installeer als volgt:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Registreer de agent bij de configuratieserver.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Installatie-instellingen
**Instelling** | **Details**
--- | ---
Gebruik | UnifiedAgent.exe /Role \<MS/MT> \</InstallLocation InstallLocation Install Location> /Platform "VmWare" /Silent
Installatielogboeken | Onder %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Rol | Verplichte installatieparameter. Hiermee geeft u op of de Mobiliteitsservice (MS) of hoofddoel (MT) moet worden geïnstalleerd.
/InstallLocation| Optionele parameter. Hiermee geeft u de installatielocatie van de Mobiliteitsservice (elke map) op.
/Platform | Verplicht. Hiermee geeft u het platform op waarop Mobility Service is geïnstalleerd. **VMware** voor VMware VM's/fysieke servers; **Azure** voor Azure VM's.<br/><br/> Als u Azure VM's als fysieke machines behandelt, geeft u **VMware**op .
/Silent| Optioneel. Hiermee geeft u op of het installatieprogramma in de stille modus moet worden uitgevoerd.

#### <a name="registration-settings"></a>Registratie-instellingen
**Instelling** | **Details**
--- | ---
Gebruik | UnifiedAgentConfigurator.exe /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>
Agentconfiguratielogboeken | Onder %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Verplichte parameter. Hiermee geeft u het IP-adres van de configuratieserver op. Gebruik een geldig IP-adres.
/PassphraseFilePath |  Verplicht. Locatie van de wachtwoordzin. Gebruik een geldig UNC- of lokaal bestandspad.

### <a name="on-a-linux-machine"></a>Op een Linux machine

1. Kopieer het installatieprogramma naar een lokale map (bijvoorbeeld /tmp) op de server die u wilt beveiligen. Voer in een terminal de volgende opdrachten uit:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Installeer als volgt:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Nadat de installatie is voltooid, moet Mobility Service zijn geregistreerd op de configuratieserver. Voer de volgende opdracht uit om Mobility Service te registreren bij de configuratieserver:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Installatie-instellingen
**Instelling** | **Details**
--- | ---
Gebruik | ./install -d \<Install Location \<> -r MS/MT> -v VmWare -q
-r | Verplichte installatieparameter. Hiermee geeft u op of de Mobiliteitsservice (MS) of hoofddoel (MT) moet worden geïnstalleerd.
-d | Optionele parameter. Hiermee geeft u de installatielocatie van de Mobiliteitsservice op: /usr/local/ASR.
-v | Verplicht. Hiermee geeft u het platform op waarop Mobility Service is geïnstalleerd. **VMware** voor VMware VM's/fysieke servers; **Azure** voor Azure VM's.
-q | Optioneel. Hiermee geeft u op of het installatieprogramma in de stille modus moet worden uitgevoerd.

#### <a name="registration-settings"></a>Registratie-instellingen
**Instelling** | **Details**
--- | ---
Gebruik | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP \<> -P PassphraseFilePath>
-i | Verplichte parameter. Hiermee geeft u het IP-adres van de configuratieserver op. Gebruik een geldig IP-adres.
-P |  Verplicht. Volledig bestandspad van het bestand waarin de wachtwoordzin wordt opgeslagen. Gebruik een geldige map.

## <a name="azure-virtual-machine-agent"></a>Azure Virtual Machine-agent

- **Windows VM's**: Vanaf versie 9.7.0.0 van de Mobiliteitsservice wordt de [Azure VM-agent](../virtual-machines/extensions/features-windows.md#azure-vm-agent) geïnstalleerd door de installatiemedewerker van de Mobiliteitsservice. Dit zorgt ervoor dat wanneer de machine niet overgaat naar Azure, de Azure VM voldoet aan de vereiste voor de installatie van een agent voor het gebruik van een Vm-extensie.
- **Linux VM's**: De [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) moet handmatig worden geïnstalleerd op de Azure VM na failover.

## <a name="locate-installer-files"></a>Installatiebestanden zoeken

Ga naar %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository folder op configuratieserver. Controleer op basis van het besturingssysteem welke installateur u nodig hebt. In de volgende tabel worden de installatiebestanden voor elk VMware VM- en fysiek serverbesturingssysteem samengevat. U [ondersteunde besturingssystemen](vmware-physical-azure-support-matrix.md#replicated-machines) bekijken voordat u begint.

**Installatiebestand** | **Besturingssysteem (alleen 64-bits)**
--- | ---
Microsoft-ASR\_\*UA\*Windows release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1,SP2,SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS server
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Volgende stappen

[Stel push-installatie in voor de Mobility service.](vmware-azure-install-mobility-service.md)
