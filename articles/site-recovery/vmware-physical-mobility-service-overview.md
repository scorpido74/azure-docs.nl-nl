---
title: Informatie over de Mobiliteitsservice voor noodherstel van Vm's en fysieke servers met Azure Site Recovery | Microsoft Documenten
description: Meer informatie over de Mobiliteitsserviceagent voor noodherstel van VMware VM's en fysieke servers naar Azure met behulp van de Azure Site Recovery-service.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: ec4d1cfbe0c76c8245c4beeaa7c044d76d917a7a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259787"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>Over de Mobility-service voor Vm's en fysieke servers van VMware

Wanneer u noodherstel instelt voor Virtuele Vm's (VMware) en fysieke servers met [Azure Site Recovery,](site-recovery-overview.md)installeert u de Service Site Recovery Mobility op elke on-premises Vm en fysieke server. De Mobiliteitsservice legt gegevensop de machine vast en stuurt deze door naar de siteherstelprocesserver. De Mobility-service wordt geïnstalleerd door de Mobility service agent software die u implementeren met behulp van de volgende methoden:

- [Push-installatie:](#push-installation)Wanneer beveiliging is ingeschakeld via de Azure-portal, installeert Site Recovery de Mobiliteitsservice op de server.
- Handmatige installatie: U de Mobiliteitsservice handmatig installeren op elke machine via de [gebruikersinterface (UI)](#install-the-mobility-service-using-ui) of [opdrachtprompt.](#install-the-mobility-service-using-command-prompt)
- [Geautomatiseerde implementatie](vmware-azure-mobility-install-configuration-mgr.md): U de installatie van de Mobility-service automatiseren met software-implementatietools zoals Configuration Manager.

> [!NOTE]
> De Mobility-service gebruikt ongeveer 6%-10% van het geheugen op bronmachines voor Vm's of fysieke machines.

## <a name="antivirus-on-replicated-machines"></a>Antivirus op gerepliceerde machines

Als machines die u wilt repliceren antivirussoftware uitvoeren, sluit u de installatiemap _C:\ProgramData\ASR\agent_ van de mobiliteitsservice uit voor antivirusbewerkingen. Deze uitsluiting zorgt ervoor dat replicatie werkt zoals verwacht.

## <a name="push-installation"></a>Push-installatie

Push-installatie is een integraal onderdeel van de taak die wordt uitgevoerd vanaf de Azure-portal om replicatie in te [schakelen.](vmware-azure-enable-replication.md#enable-replication) Nadat u de set VM's hebt gekozen die u wilt beveiligen en replicatie wilt inschakelen, duwt de configuratieserver de Mobiliteitsserviceagent naar de servers, installeert de agent en voltooit de registratie van de agent met de configuratieserver.

### <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat aan alle [vereisten voor](vmware-azure-install-mobility-service.md) push-installatie wordt voldaan.
- Controleer of alle serverconfiguraties voldoen aan de criteria in de [ondersteuningsmatrix voor noodherstel van Vm's en fysieke servers naar Azure.](vmware-physical-azure-support-matrix.md)

De werkstroom voor push-installatie wordt beschreven in de volgende secties:

### <a name="mobility-service-agent-version-923-and-higher"></a>Mobiliteitsservicemedewerker versie 9.23 en hoger

Zie [Rollup 35 voor Azure Site Recovery voor](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)meer informatie over versie 9.23.

Tijdens een push-installatie van de Mobility service worden de volgende stappen uitgevoerd:

1. De agent wordt naar de bronmachine geduwd. Het kopiëren van de agent naar de bronmachine kan mislukken als gevolg van meerdere milieufouten. Bezoek [onze richtlijnen](vmware-azure-troubleshoot-push-install.md) om push-installatiefouten op te lossen.
1. Nadat de agent naar de server is gekopieerd, wordt een vereistecontrole uitgevoerd op de server.
   - Als aan alle voorwaarden wordt voldaan, begint de installatie.
   - De installatie mislukt als niet aan een of meer van de [voorwaarden](vmware-physical-azure-support-matrix.md) wordt voldaan.
1. Als onderdeel van de installatie van de agent is de VSS-provider (Volume Shadow Copy Service) voor Azure Site Recovery geïnstalleerd. De VSS-provider wordt gebruikt om toepassingsconsistente herstelpunten te genereren. Als de installatie van de VSS-provider mislukt, wordt deze stap overgeslagen en wordt de installatie van de agent voortgezet.
1. Als de installatie van de agent slaagt, maar de installatie van de VSS-provider mislukt, wordt de taakstatus gemarkeerd als **Waarschuwing**. Dit heeft geen invloed op crash-consistent herstelpunt generatie.

    - Als u toepassingsconsistente herstelpunten wilt genereren, raadpleegt u [onze richtlijnen](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) om een handmatige installatie van de SITE Recovery VSS-provider te voltooien.
    - Als u geen toepassingsconsistente herstelpunten wilt genereren, [wijzigt u het replicatiebeleid](vmware-azure-set-up-replication.md#create-a-policy) om toepassingsconsistente herstelpunten uit te schakelen.

### <a name="mobility-service-agent-version-922-and-below"></a>Mobiliteitsservicemedewerker versie 9.22 en lager

1. De agent wordt naar de bronmachine geduwd. Het kopiëren van de agent naar de bronmachine kan mislukken als gevolg van meerdere milieufouten. Raadpleeg [onze richtlijnen](vmware-azure-troubleshoot-push-install.md) om push-installatiefouten op te lossen.
1. Nadat de agent naar de server is gekopieerd, wordt een vereistecontrole uitgevoerd op de server.
   - Als aan alle voorwaarden wordt voldaan, begint de installatie.
   - De installatie mislukt als niet aan een of meer van de [voorwaarden](vmware-physical-azure-support-matrix.md) wordt voldaan.

1. Als onderdeel van de installatie van de agent is de VSS-provider (Volume Shadow Copy Service) voor Azure Site Recovery geïnstalleerd. De VSS-provider wordt gebruikt om toepassingsconsistente herstelpunten te genereren.
   - Als de installatie van de VSS-provider mislukt, mislukt de installatie van de agent. Gebruik [versie 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) of hoger om crashconsistente herstelpunten te genereren en een handmatige installatie van de VSS-provider te maken om te voorkomen dat de installatie van de agent mislukt.

## <a name="install-the-mobility-service-using-ui"></a>De Mobiliteitsservice installeren met behulp van gebruikersinterface

### <a name="prerequisites"></a>Vereisten

- Controleer of alle serverconfiguraties voldoen aan de criteria in de [ondersteuningsmatrix voor noodherstel van Vm's en fysieke servers naar Azure.](vmware-physical-azure-support-matrix.md)
- [Zoek het installatieprogramma](#locate-installer-files) voor het besturingssysteem van de server.

>[!IMPORTANT]
> Gebruik de installatiemethode ui niet als u een IaaS-vm (Azure Infrastructure as a Service) van de ene Azure-regio naar de andere repliceert. Gebruik de [opdrachtpromptinstallatie.](#install-the-mobility-service-using-command-prompt)

1. Kopieer het installatiebestand naar de machine en voer het uit.
1. Selecteer **in installatieoptie**de optie **Mobiliteitsservice installeren**.
1. Kies de installatielocatie en selecteer **Installeren**.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="Optiepagina voor de installatie van mobiliteitsservice.":::

1. Controleer de installatie in **de voortgang van de installatie**. Nadat de installatie is voltooid, selecteert u **Doorgaan naar configuratie** om de service te registreren bij de configuratieserver.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="Registratiepagina van de mobiliteitsservice.":::

1. Geef in **Configuratieservergegevens**het IP-adres en de wachtwoordzin op die u hebt geconfigureerd.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="Registratiepagina van de mobiliteitsservice.":::

1. Selecteer **Registreren** om de registratie te voltooien.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="De registratie van de mobiliteitsdienst laatste pagina.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>De Mobiliteitsservice installeren met opdrachtprompt

### <a name="prerequisites"></a>Vereisten

- Controleer of alle serverconfiguraties voldoen aan de criteria in de [ondersteuningsmatrix voor noodherstel van Vm's en fysieke servers naar Azure.](vmware-physical-azure-support-matrix.md)
- [Zoek het installatieprogramma](#locate-installer-files) voor het besturingssysteem van de server.

### <a name="windows-machine"></a>Windows-machine

- Voer vanuit een opdrachtprompt de volgende opdrachten uit om het installatieprogramma naar een lokale map te kopiëren, zoals _C:\Temp_, op de server die u wilt beveiligen. Vervang de bestandsnaam van het installatieprogramma door de werkelijke bestandsnaam.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA_version_Windows_GA_date_release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- Voer deze opdracht uit om de agent te installeren.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- Voer deze opdrachten uit om de agent te registreren bij de configuratieserver.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Installatie-instellingen

Instelling | Details
--- | ---
Syntaxis | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
Installatielogboeken | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | Verplichte installatieparameter. Hiermee geeft u op of de Mobiliteitsservice (MS) of hoofddoel (MT) moet worden geïnstalleerd.
`/InstallLocation`| Optionele parameter. Hiermee geeft u de installatielocatie van de Mobiliteitsservice (elke map) op.
`/Platform` | Verplicht. Hiermee geeft u het platform op waarop de Mobiliteitsservice is geïnstalleerd: <br/> **VMware** voor VMware VM's/fysieke servers. <br/> **Azure** voor Azure VM's.<br/><br/> Als u Azure VM's als fysieke machines behandelt, geeft u **VMware**op .
`/Silent`| Optioneel. Hiermee geeft u op of het installatieprogramma in de stille modus moet worden uitgevoerd.

#### <a name="registration-settings"></a>Registratie-instellingen

Instelling | Details
--- | ---
Syntaxis | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
Agentconfiguratielogboeken | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | Verplichte parameter. `<CSIP>`hiermee wordt het IP-adres van de configuratieserver opgegeven. Gebruik een geldig IP-adres.
`/PassphraseFilePath` |  Verplicht. Locatie van de wachtwoordzin. Gebruik een geldig UNC- of lokaal bestandspad.

### <a name="linux-machine"></a>Linux machine

1. Kopieer het installatieprogramma vanuit een terminalsessie naar een lokale map, zoals _/tmp_ op de server die u wilt beveiligen. Vervang de bestandsnaam van het installatieprogramma door de werkelijke bestandsnaam van uw Linux-distributie en voer de opdrachten uit.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. Installeer als volgt:

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. Nadat de installatie is voltooid, moet de Mobiliteitsservice zijn geregistreerd op de configuratieserver. Voer de volgende opdracht uit om de Mobiliteitsservice te registreren bij de configuratieserver.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>Installatie-instellingen

Instelling | Details
--- | ---
Syntaxis | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | Verplichte installatieparameter. Hiermee geeft u op of de Mobiliteitsservice (MS) of hoofddoel (MT) moet worden geïnstalleerd.
`-d` | Optionele parameter. Hiermee geeft u de `/usr/local/ASR`installatielocatie van de Mobiliteitsservice op: .
`-v` | Verplicht. Hiermee geeft u het platform op waarop Mobility service is geïnstalleerd. <br/> **VMware** voor VMware VM's/fysieke servers. <br/> **Azure** voor Azure VM's.
`-q` | Optioneel. Hiermee geeft u op of het installatieprogramma in de stille modus moet worden uitgevoerd.

#### <a name="registration-settings"></a>Registratie-instellingen

Instelling | Details
--- | ---
Syntaxis | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | Verplichte parameter. `<CSIP>`hiermee wordt het IP-adres van de configuratieserver opgegeven. Gebruik een geldig IP-adres.
`-P` |  Verplicht. Volledig bestandspad van het bestand waarin de wachtwoordzin wordt opgeslagen. Gebruik een geldige map.

## <a name="azure-virtual-machine-agent"></a>Azure Virtual Machine-agent

- **Windows VM's**: Vanaf versie 9.7.0.0 van de Mobiliteitsservice wordt de [Azure VM-agent](/azure/virtual-machines/extensions/features-windows#azure-vm-agent) geïnstalleerd door de installatiemedewerker van de Mobiliteitsservice. Dit zorgt ervoor dat wanneer de machine niet overgaat naar Azure, de Azure VM voldoet aan de vereiste voor de installatie van een agent voor het gebruik van een VM-extensie.
- **Linux VM's**: De [WALinuxAgent](/azure/virtual-machines/extensions/update-linux-agent) moet handmatig worden geïnstalleerd op de Azure VM na failover.

## <a name="locate-installer-files"></a>Installatiebestanden zoeken

Ga op de configuratieserver naar de map _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_. Controleer op basis van het besturingssysteem welke installateur u nodig hebt. In de volgende tabel worden de installatiebestanden voor elk VMware VM- en fysiek serverbesturingssysteem samengevat. Voordat u begint, u de [ondersteunde besturingssystemen](vmware-physical-azure-support-matrix.md#replicated-machines)bekijken.

> [!NOTE]
> De bestandsnamen gebruiken de syntaxis in de volgende tabel met _versie_ en _datum_ als tijdelijke aanduidingen voor de echte waarden. De werkelijke bestandsnamen zien er hetzelfde uit als deze voorbeelden:
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

Installatiebestand | Besturingssysteem (alleen 64-bits)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> Inclusief SP2 en SP3.
`Microsoft-ASR_UA_version_SLES11-SP3-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6.4 </br> Oracle Enterprise Linux 6.5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16.04 LTS server
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

## <a name="next-steps"></a>Volgende stappen

[Stel push-installatie in voor de Mobility service.](vmware-azure-install-mobility-service.md)
