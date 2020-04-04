---
title: Windows-computers verbinden met Azure Monitor | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Windows-computers die in andere clouds of on-premises worden gehost, verbinden met Azure Monitor met de agent Log Analytics voor Windows.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 70fa66a96291e0c2a638bf69bdce7da531d32bb7
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637468"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Windows-computers verbinden met Azure Monitor

Als u virtuele machines of fysieke computers in uw lokale datacenter of andere cloudomgeving met Azure Monitor wilt bewaken en beheren, moet u de Log Analytics-agent (ook wel de Microsoft Monitoring Agent (MMA) genoemd implementeren en configureren om te rapporteren aan een of meer Log Analytics-werkruimten. De agent ondersteunt ook de rol Hybride Runbook Worker voor Azure Automation.  

Op een bewaakte Windows-computer wordt de agent vermeld als de Microsoft Monitoring Agent-service. De Microsoft Monitoring Agent-service verzamelt gebeurtenissen uit logboekbestanden en Windows-gebeurtenislogboek, prestatiegegevens en andere telemetrie. Zelfs wanneer de agent niet kan communiceren met Azure Monitor rapporteert het aan, de agent blijft uitvoeren en wachtrijen de verzamelde gegevens op de schijf van de bewaakte computer. Wanneer de verbinding is hersteld, verzendt de Microsoft Monitoring Agent-service verzamelde gegevens naar de service.

Het middel kan worden geïnstalleerd met behulp van een van de volgende methoden. De meeste installaties maken zo nodig gebruik van een combinatie van deze methoden voor het installeren van verschillende sets computers.  Details over het gebruik van elke methode worden later in het artikel verstrekt.

* Handmatige installatie. Setup wordt handmatig uitgevoerd op de computer met behulp van de wizard Setup, vanaf de opdrachtregel of geïmplementeerd met een bestaand softwaredistributieprogramma.
* Azure Automation Desired State Configuration (DSC). DSC gebruiken in Azure Automation met een script voor Windows-computers dat al in uw omgeving is geïmplementeerd.  
* PowerShell-script.
* Resourcemanager-sjabloon voor virtuele machines waarop Windows on-premises wordt uitgevoerd in Azure Stack. 

>[!NOTE]
>Azure Security Center (ASC) is afhankelijk van de Microsoft Monitoring Agent (ook wel de Windows-agent Log Analytics genoemd) en installeert en configureert deze om te rapporteren aan een Log Analytics-werkruimte als onderdeel van de implementatie ervan. ASC bevat een automatische inrichtingsoptie waarmee de Windows-agent Log Analytics automatisch kan worden geïnstalleerd op alle VM's in uw abonnement en deze kan worden geconfigureerd om te rapporteren aan een specifieke werkruimte. Zie Automatische inrichting van [de loganalytics-agent inschakelen](../../security-center/security-center-enable-data-collection.md#auto-provision-mma)voor meer informatie over deze optie .
>

Als u de agent moet configureren om aan meer dan één werkruimte te rapporteren, kan dit niet worden uitgevoerd tijdens de eerste installatie, alleen daarna door de instellingen van het Configuratiescherm of PowerShell bij te werken, zoals beschreven in [Het toevoegen of verwijderen van een werkruimte.](agent-manage.md#adding-or-removing-a-workspace)  

Als u meer wilt weten over de ondersteunde configuratie, kunt u de informatie over [ondersteunde Windows-besturingssystemen](log-analytics-agent.md#supported-windows-operating-systems) en de [ netwerkconfiguratie voor de firewall](log-analytics-agent.md#network-requirements) raadplegen.

## <a name="obtain-workspace-id-and-key"></a>Werkruimte-ID en -sleutel ophalen
Voordat u de Log Analytics-agent voor Windows installeert, hebt u de werkruimte-id en -sleutel nodig voor uw Log Analytics-werkruimte.  Deze informatie is vereist tijdens het instellen van elke installatiemethode om de agent correct te configureren en ervoor te zorgen dat deze met succes kan communiceren met Azure Monitor in de commerciële azure- en amerikaanse overheidscloud. 

1. Zoek en selecteer in de Azure-portal **log Analytics-werkruimten**en selecteer deze.
2. Selecteer in uw lijst met Log Analytics-werkruimten de werkruimte die u wilt configureren bij het configureren van de agent waaraan u wilt rapporteren.
3. Selecteer **Geavanceerde instellingen**.<br><br> ![Geavanceerde instellingen van Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecteer **Verbonden bronnen** en selecteer vervolgens **Windows-servers**.   
5. Kopieer en plak deze naar uw favoriete editor, de **werkruimte-id** en **de primaire sleutel**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Agent configureren om TLS 1.2 te gebruiken
Als u het gebruik van het [TLS 1.2-protocol](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) wilt configureren voor communicatie tussen de Windows-agent en de Log Analytics-service, u de onderstaande stappen volgen om in te schakelen voordat de agent op de virtuele machine of daarna wordt geïnstalleerd.

>[!NOTE]
>Als u een VM met Windows Server 2008 SP2 x64 configureert om TLS 1.2 te gebruiken, moet u eerst de volgende [ondersteuningsupdate voor SHA-2-codeondertekenen](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update) installeren voordat u de onderstaande stappen uitvoert. 
>

1. Zoek de volgende registersubsleutel: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Een subsleutel maken onder **Protocollen** voor TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Maak een **clientsubsleutel** onder de subsleutel TLS 1.2-protocolversie die u eerder hebt gemaakt. **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Maak de volgende DWORD-waarden onder **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client:**

    * **Ingeschakeld** [Waarde = 1]
    * **Standaard uitgeschakeld** [Waarde = 0]  

Configureer .NET Framework 4.6 of hoger om beveiligde cryptografie te ondersteunen, omdat deze standaard is uitgeschakeld. De [sterke cryptografie](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) maakt gebruik van veiligere netwerkprotocollen zoals TLS 1.2 en blokkeert protocollen die niet veilig zijn. 

1. Zoek de volgende registersubsleutel: **\\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft . NETFramework\v4.0.30319**.  
2. Maak de DWORD-waarde **SchUseStrongCrypto** onder deze subsleutel met een waarde van **1**.  
3. Zoek de volgende registersubsleutel: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319**.  
4. Maak de DWORD-waarde **SchUseStrongCrypto** onder deze subsleutel met een waarde van **1**. 
5. Start het systeem opnieuw op om de instellingen van kracht te laten worden. 

## <a name="install-the-agent-using-setup-wizard"></a>De agent installeren met de wizard Setup
De volgende stappen installeren en configureren van de loganalytics-agent in azure- en Azure Government-cloud met behulp van de wizard Setup voor de agent op uw computer. Zie [De agent Operations Manager implementeren met de wizard Agent Setup](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard)als u wilt weten hoe u de agent wilt configureren om deze ook te rapporteren aan een beheergroep van System Center Operations Manager.

1. Selecteer in uw Werkruimte Log Analytics vanaf de pagina **Windows Servers** waarnaar u eerder hebt genavigeerd de juiste **Windows Agent-versie downloaden** die u wilt downloaden, afhankelijk van de processorarchitectuur van het Windows-besturingssysteem.   
2. Voer Setup uit om de agent op de computer te installeren.
2. Klik op de pagina **Welkom** op **Volgende**.
3. Lees de licentie op de pagina **Licentievoorwaarden** en klik op **Akkoord**.
4. Op de pagina **Doelmap** wijzigt u desgewenst de standaardinstallatiemap en klikt u op **Volgende**.
5. Op de pagina **Installatieopties voor agent** kiest u ervoor de agent verbinding te laten maken met Azure Log Analytics en klikt u op **Volgende**.   
6. Ga op de pagina **Azure Log Analytics** als volgt te werk:
   1. Plak de **Werkruimte-id** en **Werkruimtesleutel (primaire sleutel)** die u eerder hebt gekopieerd.  Als u de computer wilt laten rapporteren bij een Log Analytics-werkruimte in de Azure Government-cloud, selecteert u **Azure US Government** in de vervolgkeuzelijst **Azure Cloud**.  
   2. Als de computer met de Log Analytics-service moet communiceren via een proxyserver, klikt u op **Geavanceerd** en geeft u de URL en het poortnummer van de proxyserver op.  Als er voor uw proxyserver verificatie is vereist, voert u de gebruikersnaam en het wachtwoord in voor verificatie met de proxyserver. Klik vervolgens op **Volgende**.  
7. Klik op **Volgende** als u de vereiste configuratie-instellingen hebt voltooid.<br><br> ![Werkruimte-id en primaire sleutel plakken](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Controleer op de pagina **Gereed om te installeren** uw keuzes en klik op **Installeren**.
9. Klik op de pagina **Configuratie voltooid** op **Voltooien**.

Als u klaar bent wordt de **MMA** in het **Configuratiescherm** weergegeven. Als u wilt bevestigen dat het rapporteert aan Log Analytics, controleert [u De connectiviteit van de agent verifiëren voor Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>De agent installeren met de opdrachtregel
Het gedownloade bestand voor de agent is een op zichzelf staand installatiepakket.  Het installatieprogramma voor de agent en de ondersteunende bestanden zijn opgenomen in het pakket en moeten worden geëxtraheerd om correct te kunnen worden geïnstalleerd met behulp van de opdrachtregel die in de volgende voorbeelden wordt weergegeven.    

>[!NOTE]
>Als u een agent wilt upgraden, moet u de Api voor scripting van Log Analytics gebruiken. Zie het onderwerp [Het beheren en onderhouden van de Log Analytics-agent voor Windows en Linux](agent-manage.md) voor meer informatie.

In de volgende tabel worden de specifieke parameters weergegeven die worden ondersteund door de installatie voor de agent, ook wanneer deze wordt geïmplementeerd met Automation DSC.

|MMA-specifieke opties                   |Opmerkingen         |
|---------------------------------------|--------------|
| NOAPM=1                               | Optionele parameter. Installeert de agent zonder .NET Application Performance Monitoring.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = De agent configureren om te rapporteren aan een werkruimte                |
|OPINSIGHTS_WORKSPACE_ID                | Werkruimte-id (guid) voor de werkruimte om toe te voegen                    |
|OPINSIGHTS_WORKSPACE_KEY               | Werkruimtesleutel die wordt gebruikt om in eerste instantie te verifiëren met de werkruimte |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | De cloudomgeving opgeven waar de werkruimte zich bevindt <br> 0 = Azure commercial cloud (standaard) <br> 1 = Azure-overheid |
|OPINSIGHTS_PROXY_URL               | URI voor de proxy te gebruiken |
|OPINSIGHTS_PROXY_USERNAME               | Gebruikersnaam om toegang te krijgen tot een geverifieerde proxy |
|OPINSIGHTS_PROXY_PASSWORD               | Wachtwoord om toegang te krijgen tot een geverifieerde proxy |

1. Als u de installatiebestanden van de agent `MMASetup-<platform>.exe /c` wilt extraheren, wordt u vanuit een opdrachtprompt met verhoogde bevoegdheid verwijderd en wordt u gevraagd om het pad om bestanden naar uit te halen.  U het pad ook opgeven `MMASetup-<platform>.exe /c /t:<Full Path>`door de argumenten door te geven.  
2. Als u de agent in stilte wilt installeren en configureren om te rapporteren aan een werkruimte in de commerciële cloud van Azure, u uit de map die u de installatiebestanden hebt geëxtraheerd om te typen: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   of om de agent te configureren om te rapporteren aan azure us government cloud, typt u het: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >De tekenreekswaarden voor de parameters *OPINSIGHTS_WORKSPACE_ID* en *OPINSIGHTS_WORKSPACE_KEY* moeten worden ingekapseld in dubbele aanhalingstekens om Windows Installer te instrueren om te interprleren als geldige opties voor het pakket. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>De agent installeren met DSC in Azure Automation

U het volgende scriptvoorbeeld gebruiken om de agent te installeren met Azure Automation DSC.   Zie Aan de slag met [Azure Automation](/azure/automation/) om inzicht te krijgen in vereisten en stappen voor het maken van een Automatiseringsaccount dat vereist is voordat u Automation DSC gebruikt.  Als u niet bekend bent met Automation DSC, bekijkt u [Aan de slag met Automation DSC](../../automation/automation-dsc-getting-started.md).

In het volgende voorbeeld wordt de 64-bits `URI` agent geïnstalleerd, die wordt geïdentificeerd door de waarde. U de 32-bits versie ook gebruiken door de URI-waarde te vervangen. De URI's voor beide versies zijn:

- Windows 64-bits agent -https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bits agent -https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Deze procedure en het scriptvoorbeeld ondersteunen geen upgrade van de agent die al is geïmplementeerd op een Windows-computer.

De 32-bits en 64-bits versies van het agentpakket hebben verschillende productcodes en nieuwe versies die zijn uitgebracht, hebben ook een unieke waarde.  De productcode is een GUID die de belangrijkste identificatie is van een toepassing of product en wordt vertegenwoordigd door de eigenschap Windows Installer **ProductCode.**  De `ProductId` waarde in het **MMAgent.ps1-script** moet overeenkomen met de productcode van het 32-bits of 64-bits agent-installatiepakket.

Als u de productcode rechtstreeks uit het installatiepakket van de agent wilt ophalen, u Orca.exe gebruiken uit de [Windows SDK Components for Windows Installer Developers,](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) een onderdeel van de Windows Software Development Kit of powershell gebruiken volgens een [voorbeeldscript](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) dat is geschreven door een Microsoft Valuable Professional (MVP).  Voor beide benaderingen moet u eerst het **MOMagent.msi-bestand** uit het MMASetup-installatiepakket halen.  Dit wordt eerder weergegeven in de eerste stap onder de sectie [Installeer de agent met behulp van de opdrachtregel](#install-the-agent-using-the-command-line).  

1. Importeer de xPSDesiredStateConfiguration DSC-module vanuit [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) Azure Automation.  
2.    Azure Automation-variabele elementen maken voor *OPSINSIGHTS_WS_ID* en *OPSINSIGHTS_WS_KEY*. Stel *OPSINSIGHTS_WS_ID* in op uw Log Analytics-werkruimte-id en stel *OPSINSIGHTS_WS_KEY* in op de primaire sleutel van uw werkruimte.
3.    Kopieer het script en sla het op als MMAgent.ps1.

```powershell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

    Import-DscResource -ModuleName xPSDesiredStateConfiguration
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}

```

4. Werk `ProductId` de waarde in het script bij met de productcode die is geëxtraheerd uit de nieuwste versie van het installatiepakket van de agent met behulp van de eerder aanbevolen methoden. 
5. [Importeer het MMAgent.ps1-configuratiescript](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) in uw Automatiseringsaccount. 
5. [Wijs een Windows-computer of -knooppunt toe](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) aan de configuratie. Binnen 15 minuten controleert het knooppunt de configuratie en wordt de agent naar het knooppunt geduwd.

## <a name="verify-agent-connectivity-to-log-analytics"></a>De verbinding met de agent met Log Analytics verifiëren

Zodra de installatie van de agent is voltooid, kan het verifiëren van deze verbinding met elkaar is aangesloten en kan rapportage op twee manieren worden uitgevoerd.  

Ga in de computer naar **Configuratiescherm** en zoek het item **Microsoft Monitoring Agent**.  Selecteer deze en op het tabblad **Azure Log Analytics** moet de agent een bericht weergeven met de vermelding: De Microsoft Monitoring Agent is met succes verbonden met de Microsoft Operations Management **Suite-service.**<br><br> ![Verbindingsstatus van MMA met Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

U ook een eenvoudige logboekquery uitvoeren in de Azure-portal.  

1. Zoek en selecteer **Monitor**in de Azure-portal.
1. Selecteer **Logboeken** in het menu.
1. In het deelvenster **Logboeken** in het queryveldtype:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

In de geretourneerde zoekresultaten ziet u heartbeatrecords voor de computer die aangeven dat deze is verbonden en rapporteert aan de service.   

## <a name="next-steps"></a>Volgende stappen

- Controleer [het beheren en onderhouden van de Log Analytics-agent voor Windows en Linux](agent-manage.md) voor meer informatie over het opnieuw configureren, upgraden of verwijderen van de agent uit de virtuele machine.

- Controleer [het oplossen van problemen met de Windows-agent](agent-windows-troubleshoot.md) als u problemen ondervindt tijdens het installeren of beheren van de agent.
