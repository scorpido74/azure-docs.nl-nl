---
title: Mobility Service automatiseren voor noodherstel van de installatie in Azure Site Recovery
description: De Mobiliteitsservice voor VMware /physical server disaster recovery automatisch installeren met Azure Site Recovery.
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: f24d321e882024d324435498adf11694037547f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77252224"
---
# <a name="automate-mobility-service-installation"></a>Installatie van Mobility Service automatiseren

In dit artikel wordt beschreven hoe u installatie en updates voor de Mobility Service-agent in [Azure Site Recovery kunt](site-recovery-overview.md)automatiseren.

Wanneer u Site Recovery implementeert voor noodherstel van on-premises Vm's en fysieke servers in Azure, installeert u de Mobility Service-agent op elke machine die u wilt repliceren. De Mobiliteitsservice legt gegevensop de machine vast en stuurt deze door naar de siteherstelprocesserver voor replicatie. U de Mobiliteitsservice op een aantal manieren implementeren:

- **Push-installatie:** Laat Site Recovery de mobiliteitsserviceagent installeren wanneer u replicatie inschakelt voor een machine in de Azure-portal.
- **Handmatige installatie**: Installeer de Mobiliteitsservice handmatig op elke machine. [Meer informatie](vmware-physical-mobility-service-overview.md) over push- en handmatige installatie.
- **Geautomatiseerde implementatie:** Automatiseer de installatie met hulpprogramma's voor softwareimplementatie, zoals Microsoft Endpoint Configuration Manager of hulpprogramma's van derden, zoals JetPatch.

Geautomatiseerde installatie en actualisering biedt een oplossing als:

- Uw organisatie staat geen push-installatie toe op beveiligde servers.
- Uw bedrijfsbeleid vereist dat wachtwoorden periodiek worden gewijzigd. U moet een wachtwoord opgeven voor de push-installatie.
- Uw beveiligingsbeleid staat het toevoegen van firewalluitzonderingen voor specifieke machines niet toe.
- U treedt op als hostingserviceprovider en wilt geen referenties van de klantmachine verstrekken die nodig zijn voor push-installatie met Site Recovery.
- U moet agentinstallaties tegelijkertijd naar veel servers schalen.
- U wilt installaties en upgrades plannen tijdens geplande onderhoudsvensters.

## <a name="prerequisites"></a>Vereisten

Om de installatie te automatiseren, hebt u de volgende items nodig:

- Een geïmplementeerde software-installatieoplossing zoals [Configuration Manager](/configmgr/) of [JetPatch.](https://jetpatch.com/microsoft-azure-site-recovery/)
- Implementatievereisten die zijn geïmplementeerd in [Azure](tutorial-prepare-azure.md) en [on-premises](vmware-azure-tutorial-prepare-on-premises.md) voor VMware-noodherstel of voor herstel van [fysieke servers](physical-azure-disaster-recovery.md) na noodgevallen. Bekijk de [ondersteuningsvereisten](vmware-physical-azure-support-matrix.md) voor herstel na noodgevallen.

## <a name="prepare-for-automated-deployment"></a>Voorbereiden op geautomatiseerde implementatie

In de volgende tabel worden tools en processen voor het automatiseren van de implementatie van Mobility Service samengevat.

**Hulpprogramma** | **Details** | **Instructies**
--- | --- | ---
**Configuratiebeheer** | 1. Controleer of u de hierboven vermelde [voorwaarden](#prerequisites) hebt. <br/><br/> 2. Implementeer disaster recovery door de bronomgeving in te stellen, inclusief het downloaden van een OVA-bestand om de Site Recovery-configuratieserver te implementeren als vmware-vm met behulp van een OVF-sjabloon.<br/><br/> 3. U registreert de configuratieserver met de siteherstelservice, stelt de doelazure-omgeving in en configureert een replicatiebeleid.<br/><br/> 4. Voor geautomatiseerde mobility service-implementatie maakt u een netwerkshare met de configuratieserverwachtwoordzin en installatiebestanden van Mobility Service.<br/><br/> 5. U maakt een Configuration Manager-pakket met de installatie of updates en bereidt u zich voor op de implementatie van Mobility Service.<br/><br/> 6. U vervolgens replicatie naar Azure inschakelen voor de machines waarop de Mobiliteitsservice is geïnstalleerd. | [Automatiseren met Configuratiebeheer](#automate-with-configuration-manager)
**JetPatch (JetPatch)** | 1. Controleer of u de hierboven vermelde [voorwaarden](#prerequisites) hebt. <br/><br/> 2. Implementeer disaster recovery door de bronomgeving in te stellen, inclusief het downloaden en implementeren van JetPatch Agent Manager voor Azure Site Recovery in uw siteherstelomgeving, met behulp van een OVF-sjabloon.<br/><br/> 3. U registreert de configuratieserver met Siterecovery, stelt de doelomgeving van Azure in en configureert een replicatiebeleid.<br/><br/> 4. Voor geautomatiseerde implementatie u de configuratie van JetPatch Agent Manager initialiseren en voltooien.<br/><br/> 5. In JetPatch u een siteherstelbeleid maken om de implementatie en upgrade van de Mobility Service-agent te automatiseren. <br/><br/> 6. U vervolgens replicatie naar Azure inschakelen voor de machines waarop de Mobiliteitsservice is geïnstalleerd. | [Automatiseren met JetPatch Agent Manager](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [Problemen met de installatie van agenten in JetPatch oplossen](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Automatiseren met Configuratiebeheer

### <a name="prepare-the-installation-files"></a>De installatiebestanden voorbereiden

1. Zorg ervoor dat u de voorwaarden op zijn plaats.
1. Maak een beveiligde netwerkbestandsshare (SMB-share) die toegankelijk is voor de machine waarop de configuratieserver wordt uitgevoerd.
1. [Categoriseer in](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections) Configuratiebeheer de servers waarop u de Mobiliteitsservice wilt installeren of bijwerken. Een verzameling moet alle Windows-servers bevatten, de andere alle Linux-servers.
1. Maak in het netwerkdeel een map:

   - Maak voor installatie op Windows-machines een map met de naam _MobSvcWindows_.
   - Maak voor installatie op Linux-machines een map met de naam _MobSvcLinux_.

1. Meld u aan bij de configuratieservermachine.
1. Open op de configuratieservermachine een opdrachtprompt voor beheerders.
1. Voer de volgende opdracht uit om het wachtwoordzinsbestand te genereren:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. Kopieer het bestand _MobSvc.passphrase_ naar de Windows-map en de Linux-map.
1. Voer deze opdracht uit om naar de map met de installatiebestanden te bladeren:

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. Kopieer deze installatiebestanden naar het netwerkshare:

   - Kopieer voor Windows _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ naar _MobSvcWindows_.
   - Kopieer voor Linux de volgende bestanden naar _MobSvcLinux:_
     - _Microsoft-ASR_UARHEL6-64release.tar.gz_
     - _Microsoft-ASR_UARHEL7-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP3-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP4-64release.tar.gz_
     - _Microsoft-ASR_UAOL6-64release.tar.gz_
     - _Microsoft-ASR_UAUBUNTU-14.04-64release.tar.gz_

1. Zoals beschreven in de volgende procedures, kopieert u de code naar de Windows- of Linux-mappen. We gaan ervan uit dat:

   - Het IP-adres van `192.168.3.121`de configuratieserver is .
   - De beveiligde netwerkbestandsshare is `\\ContosoSecureFS\MobilityServiceInstallers`.

### <a name="copy-code-to-the-windows-folder"></a>Code kopiëren naar de Windows-map

Kopieer de volgende code:

- Sla de code op in de _map MobSvcWindows_ als _install.bat_.
- Vervang `[CSIP]` de tijdelijke aanduidingen in dit script door de werkelijke waarden van het IP-adres van uw configuratieserver.
- Het script ondersteunt nieuwe installaties van de Mobility Service-agent en updates voor agents die al zijn geïnstalleerd.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Code kopiëren naar de Linux-map

Kopieer de volgende code:

- Sla de code op in de _Map MobSvcLinux_ als _install_linux.sh_.
- Vervang `[CSIP]` de tijdelijke aanduidingen in dit script door de werkelijke waarden van het IP-adres van uw configuratieserver.
- Het script ondersteunt nieuwe installaties van de Mobility Service-agent en updates voor agents die al zijn geïnstalleerd.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>Een pakket maken

1. Meld u aan bij de console Configuration Manager en ga naar **Software Library** > **Application Management** > **Packages**.
1. Klik met de rechtermuisknop op **Pakketten** > **Maken Pakket**.
1. Geef pakketgegevens op, waaronder een naam, beschrijving, fabrikant, taal en versie.
1. Selecteer **Dit pakket bevat bronbestanden**.
1. Klik **op Bladeren**en selecteer de netwerkshare en map die het relevante installatieprogramma bevat _(MobSvcWindows_ of _MobSvcLinux)._ Selecteer vervolgens **Volgende**.

   ![Schermafbeelding van de wizard Pakket en programma maken](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. Selecteer in **Kies het programmatype dat u wilt maken** de optie Standard **Program** > **Next**.

   ![Schermafbeelding van de wizard Pakket en programma maken](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. Geef **in Informatie opgeven over deze standaardprogrammapagina** de volgende waarden op:

    **Parameter** | **Windows-waarde** | **Linux-waarde**
    --- | --- | ---
    **Naam** | Microsoft Azure Mobility Service installeren (Windows) | Microsoft Azure Mobility Service (Linux) installeren.
    **Opdrachtregel** | Install.bat | ./install_linux.sh
    **Programma kan worden uitgevoerd** | Of een gebruiker is aangemeld | Of een gebruiker is aangemeld
    **Andere parameters** | Standaardinstelling gebruiken | Standaardinstelling gebruiken

   ![Schermafbeelding van de wizard Pakket en programma maken](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. Ga **in De vereisten voor dit standaardprogramma**de volgende taken uitvoeren:

   - Selecteer **Dit programma kan alleen worden uitgevoerd op opgegeven platforms voor**Windows-machines. Selecteer vervolgens de [ondersteunde Windows-besturingssystemen](vmware-physical-azure-support-matrix.md#replicated-machines) en selecteer **Volgende**.
   - Voor Linux-machines selecteert u **Dit programma kan op elk platform worden uitgevoerd.** Selecteer **vervolgens Volgende**.

1. Sluit de wizard af.

### <a name="deploy-the-package"></a>Het pakket implementeren

1. Klik in de console Configuratiebeheer met de rechtermuisknop op het pakket en selecteer **Inhoud distribueren**.

   ![Schermafbeelding van de console Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. Selecteer de distributiepunten waarop de pakketten moeten worden gekopieerd. [Meer informatie](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points).
1. Voltooi de wizard. Het pakket begint vervolgens te repliceren naar de opgegeven distributiepunten.
1. Nadat de pakketdistributie is voltooid, klikt u met de rechtermuisknop op het pakket > **Implementeren**.

   ![Schermafbeelding van de console Configuration Manager](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. Selecteer de Windows- of Linux-apparaatverzameling die u eerder hebt gemaakt.
1. Selecteer **distributiepunten**op de pagina **De inhoudsdoel** opgeven .
1. Stel in **Instellingen opgeven om te bepalen hoe deze software wordt geïmplementeerd,** **stel Doel** in **op Vereist**.

   ![Schermafbeelding van de wizard Software implementeren](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. Stel **in De planning voor deze implementatie**op, stel een planning in. [Meer informatie](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched).

   - De Mobiliteitsservice is geïnstalleerd volgens het door u opgegeven schema.
   - Om onnodige herstart te voorkomen, plant u de installatie van het pakket tijdens uw maandelijkse onderhoudsvenster of het venster met software-updates.

1. Configureer op de pagina **Distributiepunten** de instellingen en voltooi de wizard.
1. De voortgang van de implementatie controleren in de console Configuration Manager. Ga naar **Implementaties** > **Deployments** > _\<controleren\>met de naam van uw pakket._

### <a name="uninstall-the-mobility-service"></a>De Mobiliteitsservice verwijderen

U Configuratiebeheerpakketten maken om de Mobiliteitsservice te verwijderen. In het volgende script wordt bijvoorbeeld de Mobiliteitsservice verwijdert:

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>Volgende stappen

Replicatie voor VM's [inschakelen.](vmware-azure-enable-replication.md)
