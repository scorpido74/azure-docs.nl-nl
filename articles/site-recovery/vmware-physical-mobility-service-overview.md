---
title: Over de Mobility-service voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers met Azure Site Recovery | Microsoft Docs
description: Meer informatie over de Mobility Service-agent voor nood herstel van virtuele VMware-machines en fysieke servers naar Azure met behulp van de Azure Site Recovery-service.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: b2c59fd6ee925d531a5a5ff3bb26fdebea025b83
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513555"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Over de Mobility-service voor VMware-Vm's en fysieke servers

Wanneer u herstel na nood gevallen instelt voor virtuele VMware-machines en fysieke servers met behulp van [Azure site Recovery](site-recovery-overview.md), installeert u de site Recovery Mobility-service op elke on-premises VMware-VM en fysieke server.  De Mobility-service legt gegevens op de computer vast en stuurt deze door naar de Site Recovery-proces server. U kunt de Mobility-service implementeren met behulp van de volgende methoden:

- [Push-installatie](#push-installation): site Recovery de mobiliteits agent op de server installeert wanneer de beveiliging is ingeschakeld via Azure Portal.
- Hand matig installeren: u kunt de Mobility-service hand matig op elke computer installeren via de [gebruikers interface](#install-mobility-agent-through-ui) of de [opdracht prompt](#install-mobility-agent-through-command-prompt).
- [Geautomatiseerde implementatie](vmware-azure-mobility-install-configuration-mgr.md): u kunt de installatie automatiseren met hulpprogram ma's voor software-implementatie, zoals Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Anti-virus op gerepliceerde computers

Als de computers die u wilt repliceren, actieve antivirus software hebben, moet u de installatiemap van de Mobility-service uitsluiten van anti-virus bewerkingen (*C:\ProgramData\ASR\agent*). Dit zorgt ervoor dat de replicatie werkt zoals verwacht.

## <a name="push-installation"></a>Push-installatie

De push-installatie is een integraal onderdeel van de taak[replicatie inschakelen](vmware-azure-enable-replication.md#enable-replication)die is geactiveerd in de portal. Na het kiezen van de set virtuele machines die u wilt beveiligen en het activeren van replicatie inschakelen, pusht configuratie server Mobility-agent op de servers, installeert u de agent en voltooit u de registratie van de agent met de configuratie server. Als de bewerking is voltooid,

- Zorg ervoor dat aan alle [vereisten](vmware-azure-install-mobility-service.md) voor de push-installatie is voldaan.
- Zorg ervoor dat alle configuraties van servers onder de [ondersteunings matrix van VMware naar Azure Dr-scenario](vmware-physical-azure-support-matrix.md)vallen.

Details van de werk stroom voor de push-installatie zijn beschreven in de volgende secties.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>Van [9,23-versie](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) en hoger

Tijdens de push-installatie van de Mobility-agent worden de volgende stappen uitgevoerd

1. De agent wordt op de bron machine gepusht. Het kopiëren van de agent op naar de bron machine kan mislukken vanwege meerdere omgevings fouten. Ga naar [onze richt lijnen](vmware-azure-troubleshoot-push-install.md) voor het oplossen van problemen met push-installatie.
2. Nadat de agent is gekopieerd naar de server vereisten controles, worden uitgevoerd op de server. De installatie mislukt als aan een of meer van de [vereisten](vmware-physical-azure-support-matrix.md) niet wordt voldaan. Als aan alle vereisten wordt voldaan, wordt de installatie geactiveerd.
3. Azure Site Recovery VSS-provider is geïnstalleerd op de server als onderdeel van de installatie van de Mobility-agent. Deze provider wordt gebruikt om toepassings consistente punten te genereren. Als de installatie van de VSS-provider is mislukt, wordt deze stap overgeslagen en wordt de agent installatie voortgezet.
4. Als de installatie van de agent slaagt maar de installatie van de VSS-provider mislukt, wordt de status van de taak als ' waarschuwing ' gemarkeerd. Dit is niet van invloed op het genereren van crash-consistentie punten.

    a. Als u toepassings consistente punten wilt genereren, raadpleegt u [onze richt lijnen](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) om de installatie van site Recovery VSS-provider hand matig te volt ooien. </br>
    b.  Als u niet wilt dat toepassings consistente punten worden gegenereerd, [wijzigt u het replicatie beleid](vmware-azure-set-up-replication.md#create-a-policy) om toepassings consistente punten uit te scha kelen.

### <a name="before-922-versions"></a>Vóór 9,22 versies

1. De agent wordt op de bron machine gepusht. Het kopiëren van de agent op naar de bron machine kan mislukken vanwege meerdere omgevings fouten. Ga naar [onze richt lijnen](vmware-azure-troubleshoot-push-install.md) voor het oplossen van problemen met push-installatie.
2. Nadat de agent is gekopieerd naar de server vereisten controles, worden uitgevoerd op de server. De installatie mislukt als aan een of meer van de [vereisten](vmware-physical-azure-support-matrix.md) niet wordt voldaan. Als aan alle vereisten wordt voldaan, wordt de installatie geactiveerd.
3. Azure Site Recovery VSS-provider is geïnstalleerd op de server als onderdeel van de installatie van de Mobility-agent. Deze provider wordt gebruikt om toepassings consistente punten te genereren. Als de installatie van de VSS-provider is mislukt, mislukt de installatie van de agent. Om te voor komen dat de installatie van de Mobility-agent mislukt, gebruikt u [9,23-versie](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) of hoger om crash consistente punten te genereren en VSS-provider hand matig te installeren.

## <a name="install-mobility-agent-through-ui"></a>Mobility agent installeren via de gebruikers interface

### <a name="prerequisite"></a>Vereisten

- Zorg ervoor dat alle configuraties van servers onder de [ondersteunings matrix van VMware naar Azure Dr-scenario](vmware-physical-azure-support-matrix.md)vallen.
- [Zoek het installatie programma](#locate-installer-files) op basis van het besturings systeem van de server.

>[!IMPORTANT]
> Als u Azure IaaS VM van de ene Azure-regio naar de andere repliceert, gebruikt u deze methode niet. Gebruik in plaats daarvan de installatie methode op basis van de opdracht regel.

1. Kopieer het installatie bestand naar de computer en voer dit uit.
2. Selecteer in **installatie optie** **Mobility service installeren**.
3. Selecteer de installatie locatie > **installeren**.

    ![Pagina installatie optie voor Mobility-service](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Controleer de installatie in **voortgang**van de installatie. Nadat de installatie is voltooid, selecteert u **door gaan naar configuratie** om de service te registreren bij de configuratie server.

    ![Pagina registratie van Mobility-service](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. Geef in details van de **Configuratie server**het IP-adres en de wachtwoordzin op die u hebt geconfigureerd.  

    ![Pagina registratie van Mobility-service](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Selecteer **registreren** om de registratie te volt ooien.

    ![Laatste pagina Registratie Mobility-service](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Mobility agent installeren via opdracht prompt

### <a name="prerequisite"></a>Vereisten

- Zorg ervoor dat alle configuraties van servers onder de [ondersteunings matrix van VMware naar Azure Dr-scenario](vmware-physical-azure-support-matrix.md)vallen.
- [Zoek het installatie programma](#locate-installer-files) op basis van het besturings systeem van de server.

### <a name="on-a-windows-machine"></a>Op een Windows-computer

- Kopieer het installatie programma naar een lokale map (bijvoorbeeld C:\Temp) op de server die u wilt beveiligen.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Installeer dit als volgt:

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Registreer de agent bij de configuratie server.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Installatie-instellingen
**Instelling** | **Details**
--- | ---
Gebruik | UnifiedAgent. exe/Role \<MS/MT >/InstallLocation \<installatie locatie >/platform "VmWare"/Silent
Installatielogboeken | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Verplichte installatie parameter. Hiermee geeft u op of de Mobility-service (MS) of het hoofd doel (MT) moet worden geïnstalleerd.
/InstallLocation| Optionele parameter. Hiermee geeft u de installatie locatie van de Mobility-service (een map).
/Platform | Verplicht. Hiermee geeft u het platform op waarop de Mobility-service is geïnstalleerd. **VMware** voor VMware-vm's/fysieke servers; **Azure** voor Azure-vm's.<br/><br/> Als u Azure-Vm's als fysieke machines behandelt, moet u **VMware**opgeven.
/Silent| Optioneel. Hiermee geeft u op of het installatie programma moet worden uitgevoerd in de Stille modus.

#### <a name="registration-settings"></a>Registratie-instellingen
**Instelling** | **Details**
--- | ---
Gebruik | UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>
Agent configuratie logboeken | Under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Verplichte para meter. Hiermee geeft u het IP-adres van de configuratie server. Gebruik een geldig IP-adres.
/PassphraseFilePath |  Verplicht. Locatie van de wachtwoordzin. Gebruik een geldig UNC-pad of een lokaal bestandspad.

### <a name="on-a-linux-machine"></a>Op een Linux-computer

1. Kopieer het installatie programma naar een lokale map (bijvoorbeeld map/tmp) op de server die u wilt beveiligen. Voer de volgende opdrachten uit in een Terminal:

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
    ```

2. Installeer dit als volgt:

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. Nadat de installatie is voltooid, moet de Mobility-service zijn geregistreerd bij de configuratie server. Voer de volgende opdracht uit om Mobility service te registreren bij de configuratie server:

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>Installatie-instellingen
**Instelling** | **Details**
--- | ---
Gebruik | ./install-d \<installatie locatie >-r \<MS/MT->-v VmWare-q
-r | Verplichte installatie parameter. Hiermee geeft u op of de Mobility-service (MS) of het hoofd doel (MT) moet worden geïnstalleerd.
-d | Optionele parameter. Hiermee geeft u de installatie locatie van de Mobility-service op:/usr/local/ASR
-v | Verplicht. Hiermee geeft u het platform op waarop de Mobility-service is geïnstalleerd. **VMware** voor VMware-vm's/fysieke servers; **Azure** voor Azure-vm's.
-q | Optioneel. Hiermee geeft u op of het installatie programma moet worden uitgevoerd in de Stille modus.

#### <a name="registration-settings"></a>Registratie-instellingen
**Instelling** | **Details**
--- | ---
Gebruik | cd-/usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh-i \<CSIP >-P \<PassphraseFilePath >
-i | Verplichte para meter. Hiermee geeft u het IP-adres van de configuratie server. Gebruik een geldig IP-adres.
-P |  Verplicht. Volledig bestandspad van het bestand waarin de wachtwoordzin wordt opgeslagen. Gebruik een geldige map.

## <a name="azure-virtual-machine-agent"></a>Agent van de virtuele machine van Azure

- **Windows-vm's**: van versie 9.7.0.0 van de Mobility-service wordt de [Azure VM-agent](../virtual-machines/extensions/features-windows.md#azure-vm-agent) geïnstalleerd door het installatie programma van de Mobility-service. Op deze manier zorgt u ervoor dat wanneer de computer failover naar Azure wordt uitgevoerd, de Azure VM voldoet aan de installatie vereisten van de agent voor het gebruik van een VM-extensie.
- **Virtuele Linux-machines**: de [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) moet hand matig worden geïnstalleerd op de virtuele Azure-machine na een failover.

## <a name="locate-installer-files"></a>Installatie bestanden zoeken

Ga naar de map%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository op de configuratie server. Controleer welk installatie programma u nodig hebt op basis van het besturings systeem. De volgende tabel bevat een overzicht van de installatie bestanden voor elke VMware-VM en een fysiek Server besturingssysteem. U kunt [ondersteunde besturings systemen](vmware-physical-azure-support-matrix.md#replicated-machines) bekijken voordat u begint.

**Installatie bestand** | **Besturings systeem (alleen 64-bits)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6. * </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7. * </br> CentOS 7. *
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enter prise Linux 6,4, 6,5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14,04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16,04 LTS-server
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Volgende stappen

[Stel een push-installatie in voor de Mobility-service](vmware-azure-install-mobility-service.md).
