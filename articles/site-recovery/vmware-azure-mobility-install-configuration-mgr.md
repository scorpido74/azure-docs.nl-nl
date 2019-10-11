---
title: Automatische installatie van de Azure Site Recovery Mobility-service voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar Azure met behulp van System Center Configuration Manager | Microsoft Docs
description: Dit artikel helpt u bij het automatiseren van de installatie van de Mobility-service met System Center Configuration Manager, voor herstel na nood gevallen van virtuele VMware-machines en fysieke servers naar Azure met behulp van Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: ee92ad6e0687018f69044bf3edde76b9f98cee52
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255584"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>De installatie van de Mobility-service automatiseren met System Center Configuration Manager

De Mobility-service is geïnstalleerd op virtuele VMware-machines en fysieke servers die u naar Azure wilt repliceren met [Azure site Recovery](site-recovery-overview.md)

In dit artikel vindt u een voor beeld van hoe u System Center Configuration Manager kunt gebruiken om de Azure Site Recovery Mobility-service op een virtuele VMware-machine te implementeren. Het gebruik van een hulp programma voor software-implementatie zoals Configuration Manager heeft de volgende voor delen:

* Nieuwe installaties en upgrades plannen tijdens het geplande onderhouds venster voor software-updates
* Schaal implementatie naar honderden servers tegelijk

In dit artikel wordt gebruikgemaakt van System Center Configuration Manager 2012 R2 om de implementatie activiteit te demonstreren. We gaan ervan uit dat u versie **9.9.4510.1** of hoger van de Mobility-service gebruikt.

U kunt de installatie van de Mobility-service ook automatiseren met [Azure Automation DSC](vmware-azure-mobility-deploy-automation-dsc.md).

## <a name="prerequisites"></a>Vereisten

1. Een hulp programma voor software-implementatie, zoals Configuration Manager, dat al in uw omgeving is geïmplementeerd.
2. U moet twee [verzamelingen apparaten](https://technet.microsoft.com/library/gg682169.aspx)maken, één voor alle **Windows-servers**en een voor alle **Linux-servers**die u wilt beveiligen met behulp van site Recovery.
3. Een configuratie server die al is geregistreerd in de Recovery Services kluis.
4. Een beveiligde netwerk bestands share (SMB-share) die toegankelijk is voor de Configuration Manager-computer.

## <a name="deploy-on-windows-machines"></a>Implementeren op Windows-computers
> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat het IP-adres van de configuratie server 192.168.3.121 is en dat de beveiligde netwerk bestands share \\ \ ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Voorbereiden op implementatie
1. Maak een map op de netwerk share en noem deze **MobSvcWindows**.
2. Meld u aan bij uw configuratie server en open een opdracht prompt met beheerders rechten.
3. Voer de volgende opdrachten uit om een wachtwoordzinbestand te genereren:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Kopieer het bestand **MobSvc. wachtwoordzin** naar de map **MobSvcWindows** op de netwerk share.
5. Blader naar de opslag plaats van het installatie programma op de configuratie server door de volgende opdracht uit te voeren:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Kopieer de **micro soft-ASR @ no__t-1UA @ no__t-2-*versie*\_Windows @ no__t-5GA @ no__t-6*datum*@no__t -8release. exe** naar de map **MobSvcWindows** op uw netwerk share.
7. Kopieer de volgende code en sla deze op als **install. bat** in de map **MobSvcWindows** .

   > [!NOTE]
   > Vervang de tijdelijke aanduidingen [CSIP] in dit script door de werkelijke waarden van het IP-adres van de configuratie server.

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

### <a name="create-a-package"></a>Een pakket maken

1. Meld u aan bij uw Configuration Manager-console.
2. Blader naar **software bibliotheek** > **toepassings beheer** > -**pakketten**.
3. Klik met de rechter muisknop op **pakketten**en selecteer **pakket maken**.
4. Geef waarden op voor de naam, de beschrijving, de fabrikant, de taal en de versie.
5. Selecteer het selectie vakje **Dit pakket bevat bron bestanden** .
6. Klik op **Bladeren**en selecteer de netwerk share waar het installatie programma is opgeslagen (\\ \ ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

   ![Scherm opname van de wizard pakket en programma maken](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. Selecteer op de pagina **het programma type kiezen dat u wilt maken** **standaard programma**en klik op **volgende**.

   ![Scherm opname van de wizard pakket en programma maken](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Geef op de pagina **informatie over deze standaard programma opgeven** de volgende invoer op en klik op **volgende**. (De andere invoer kunnen de standaard waarden gebruiken.)

   | **Parameternaam** | **Waarde** |
   |--|--|
   | Naam | Microsoft Azure Mobility-service installeren (Windows) |
   | Opdrachtregel | install. bat |
   | Het programma kan worden uitgevoerd | Ongeacht of er een gebruiker is aangemeld of niet |

   ![Scherm opname van de wizard pakket en programma maken](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. Selecteer op de volgende pagina de doel besturingssystemen. 
10. Klik twee keer op **volgende** om de wizard te volt ooien.


> [!NOTE]
> Het script ondersteunt zowel nieuwe installaties van Mobility-Service-agents als updates voor agents die al zijn geïnstalleerd.

### <a name="deploy-the-package"></a>Het pakket implementeren
1. Klik in de Configuration Manager-console met de rechter muisknop op uw pakket en selecteer **inhoud distribueren**.
   ![Screenshot van Configuration Manager console @ no__t-1
2. Selecteer de **[distributie punten](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** waarop de pakketten moeten worden gekopieerd.
3. Voltooi de wizard. Het pakket begint vervolgens met het repliceren naar de opgegeven distributie punten.
4. Nadat de pakket distributie is voltooid, klikt u met de rechter muisknop op het pakket en selecteert u **implementeren**.
   ![Screenshot van Configuration Manager console @ no__t-1
5. Selecteer de Windows Server-apparaat-verzameling die u hebt gemaakt in de sectie vereisten als de doel verzameling voor de implementatie.

   ![Scherm opname van de wizard software implementeren](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. Selecteer op de pagina **de doel inhoud opgeven** uw **distributie punten**.
7. Zorg ervoor dat het doel **vereist**is op de pagina **instellingen opgeven om te bepalen hoe deze software is geïmplementeerd** .

   ![Scherm opname van de wizard software implementeren](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Geef op de pagina **de planning voor deze implementatie opgeven** een planning op. Zie [pakketten plannen](https://technet.microsoft.com/library/gg682178.aspx)voor meer informatie.
9. Configureer op de pagina **distributie punten** de eigenschappen op basis van de behoeften van uw Data Center. Voltooi vervolgens de wizard.

> [!TIP]
> Om te voor komen dat het systeem onnodig opnieuw wordt opgestart, moet u de installatie van het pakket plannen tijdens het maandelijkse onderhouds venster of het venster software-updates

U kunt de voortgang van de implementatie bewaken met behulp van de Configuration Manager-console. Ga naar **bewaking** > **implementaties** >  *[uw pakket naam]* .

  ![Scherm afbeelding van Configuration Manager optie voor het bewaken van implementaties](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Implementeren op Linux-machines
> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat het IP-adres van de configuratie server 192.168.3.121 is en dat de beveiligde netwerk bestands share \\ \ ContosoSecureFS\MobilityServiceInstallers.

### <a name="prepare-for-deployment"></a>Voorbereiden op implementatie
1. Maak een map op de netwerk share en geef deze de naam **MobSvcLinux**.
2. Meld u aan bij uw configuratie server en open een opdracht prompt met beheerders rechten.
3. Voer de volgende opdrachten uit om een wachtwoordzinbestand te genereren:

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. Kopieer het bestand **MobSvc. wachtwoordzin** naar de map **MobSvcLinux** op de netwerk share.
5. Blader naar de opslag plaats van het installatie programma op de configuratie server door de opdracht uit te voeren:

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. Kopieer de volgende bestanden naar de map **MobSvcLinux** op de netwerk share:
   * Micro soft-ASR @ no__t-0UA @ no__t-1RHEL6-64 * release. tar. gz
   * Micro soft-ASR @ no__t-0UA @ no__t-1RHEL7-64\*release.tar.gz
   * Micro soft-ASR @ no__t-0UA @ no__t-1SLES11-SP3-64\*release.tar.gz
   * Micro soft-ASR @ no__t-0UA @ no__t-1SLES11-SP4-64\*release.tar.gz
   * Micro soft-ASR @ no__t-0UA @ no__t-1OL6-64\*release.tar.gz
   * Micro soft-ASR @ no__t-0UA @ no__t-1UBUNTU-14.04-64\*release.tar.gz


7. Kopieer de volgende code en sla deze op als **install_linux. sh** in de map **MobSvcLinux** .
   > [!NOTE]
   > Vervang de tijdelijke aanduidingen [CSIP] in dit script door de werkelijke waarden van het IP-adres van de configuratie server.

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

1. Meld u aan bij uw Configuration Manager-console.
2. Blader naar **software bibliotheek** > **toepassings beheer** > -**pakketten**.
3. Klik met de rechter muisknop op **pakketten**en selecteer **pakket maken**.
4. Geef waarden op voor de naam, de beschrijving, de fabrikant, de taal en de versie.
5. Selecteer het selectie vakje **Dit pakket bevat bron bestanden** .
6. Klik op **Bladeren**en selecteer de netwerk share waar het installatie programma is opgeslagen (\\ \ ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

   ![Scherm opname van de wizard pakket en programma maken](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. Selecteer op de pagina **het programma type kiezen dat u wilt maken** **standaard programma**en klik op **volgende**.

   ![Scherm opname van de wizard pakket en programma maken](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. Geef op de pagina **informatie over deze standaard programma opgeven** de volgende invoer op en klik op **volgende**. (De andere invoer kunnen de standaard waarden gebruiken.)

    | **Parameternaam** | **Waarde** |
   |--|--|
   | Naam | Microsoft Azure Mobility-service (Linux) installeren |
   | Opdrachtregel | ./install_linux.sh |
   | Het programma kan worden uitgevoerd | Ongeacht of er een gebruiker is aangemeld of niet |

   ![Scherm opname van de wizard pakket en programma maken](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. Selecteer op de volgende pagina **dit programma kan op elk platform worden uitgevoerd**.
   ![Screenshot van de wizard pakket en programma maken @ no__t-1

10. Klik twee keer op **volgende** om de wizard te volt ooien.

> [!NOTE]
> Het script ondersteunt zowel nieuwe installaties van Mobility-Service-agents als updates voor agents die al zijn geïnstalleerd.

### <a name="deploy-the-package"></a>Het pakket implementeren
1. Klik in de Configuration Manager-console met de rechter muisknop op uw pakket en selecteer **inhoud distribueren**.
   ![Screenshot van Configuration Manager console @ no__t-1
2. Selecteer de **[distributie punten](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** waarop de pakketten moeten worden gekopieerd.
3. Voltooi de wizard. Het pakket begint vervolgens met het repliceren naar de opgegeven distributie punten.
4. Nadat de pakket distributie is voltooid, klikt u met de rechter muisknop op het pakket en selecteert u **implementeren**.
   ![Screenshot van Configuration Manager console @ no__t-1
5. Selecteer de apparaat-verzameling van de Linux-server die u hebt gemaakt in de sectie vereisten als de doel verzameling voor de implementatie.

   ![Scherm opname van de wizard software implementeren](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. Selecteer op de pagina **de doel inhoud opgeven** uw **distributie punten**.
7. Zorg ervoor dat het doel **vereist**is op de pagina **instellingen opgeven om te bepalen hoe deze software is geïmplementeerd** .

   ![Scherm opname van de wizard software implementeren](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. Geef op de pagina **de planning voor deze implementatie opgeven** een planning op. Zie [pakketten plannen](https://technet.microsoft.com/library/gg682178.aspx)voor meer informatie.
9. Configureer op de pagina **distributie punten** de eigenschappen op basis van de behoeften van uw Data Center. Voltooi vervolgens de wizard.

Mobility service wordt geïnstalleerd op het apparaat van de Linux-server, volgens het schema dat u hebt geconfigureerd.


## <a name="uninstall-the-mobility-service"></a>De Mobility-service verwijderen
U kunt Configuration Manager-pakketten maken voor het verwijderen van de Mobility-service. Gebruik het volgende script om dit te doen:

```
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
U bent nu klaar om de beveiliging van uw virtuele machines [in te scha kelen](vmware-azure-enable-replication.md) .
