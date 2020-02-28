---
title: Windows-computers verbinden met Azure Monitor | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Windows-computers Azure Monitor die worden gehost in andere Clouds of on-premises verbindt met de Log Analytics-agent voor Windows.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 21efb16cf519d4bcad520af1c7d8818f36a77218
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668635"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Windows-computers verbinden met Azure Monitor

Als u virtuele machines of fysieke computers in uw lokale Data Center of een andere cloud omgeving wilt bewaken en beheren met Azure Monitor, moet u de Log Analytics-agent (ook wel micro soft Monitoring Agent (MMA) genoemd) implementeren en configureren voor rapporteert aan een of meer Log Analytics-werk ruimten. De agent biedt ook ondersteuning voor de Hybrid Runbook Worker rol voor Azure Automation.  

Op een bewaakte Windows-computer wordt de agent vermeld als de micro soft Monitoring Agent-service. De service micro soft monitoring agent verzamelt gebeurtenissen van logboek bestanden en Windows-gebeurtenis logboeken, prestatie gegevens en andere telemetrie. Zelfs wanneer de agent niet kan communiceren met Azure Monitor it-rapporteert, blijft de agent actief en worden de verzamelde gegevens op de schijf van de bewaakte computer in de wachtrij geplaatst. Wanneer de verbinding wordt hersteld, verzendt de service micro soft Monitoring Agent verzamelde gegevens naar de service.

De agent kan worden geïnstalleerd met behulp van een van de volgende methoden. De meeste installaties gebruiken een combi natie van deze methoden om verschillende sets computers te installeren, indien van toepassing.  Verderop in dit artikel vindt u meer informatie over het gebruik van elke methode.

* Hand matige installatie. Setup wordt hand matig uitgevoerd op de computer met behulp van de installatie wizard, vanaf de opdracht regel of wordt geïmplementeerd met een bestaand hulp programma voor software distributie.
* Azure Automation Desired State Configuration (DSC). DSC gebruiken in Azure Automation met een script voor Windows-computers die al in uw omgeving zijn geïmplementeerd.  
* Power shell-script.
* Resource Manager-sjabloon voor virtuele machines waarop Windows on-premises wordt uitgevoerd in Azure Stack. 

>[!NOTE]
>Azure Security Center (ASC) is afhankelijk van de micro soft Monitoring Agent (ook wel de Log Analytics Windows-agent genoemd) en zal deze installeren en configureren om te rapporteren aan een Log Analytics-werk ruimte als onderdeel van de implementatie. ASC bevat een automatische inrichtings optie waarmee de Log Analytics Windows-agent automatisch kan worden geïnstalleerd op alle virtuele machines in uw abonnement en deze configureert om te rapporteren aan een specifieke werk ruimte. Zie [automatische inrichting van log Analytics-agent inschakelen](../../security-center/security-center-enable-data-collection.md#auto-provision-mma)voor meer informatie over deze optie.
>

Als u de agent moet configureren om te rapporteren aan meer dan één werk ruimte, kan deze niet worden uitgevoerd tijdens de eerste installatie, maar u kunt de instellingen ook bijwerken via configuratie scherm of Power shell, zoals beschreven in [een werk ruimte toevoegen of verwijderen](agent-manage.md#adding-or-removing-a-workspace).  

Als u meer wilt weten over de ondersteunde configuratie, kunt u de informatie over [ondersteunde Windows-besturingssystemen](log-analytics-agent.md#supported-windows-operating-systems) en de [ netwerkconfiguratie voor de firewall](log-analytics-agent.md#network-firewall-requirements) raadplegen.

## <a name="obtain-workspace-id-and-key"></a>Werkruimte-ID en -sleutel ophalen
Voordat u de Log Analytics-agent voor Windows installeert, hebt u de werk ruimte-ID en-sleutel voor uw Log Analytics-werk ruimte nodig.  Deze informatie is vereist tijdens de installatie van elke installatie methode om de agent correct te configureren en ervoor te zorgen dat deze kan communiceren met Azure Monitor in azure commerciële en Amerikaanse overheids Cloud. 

1. Zoek in het Azure Portal naar en selecteer **log Analytics-werk ruimten**.
2. Selecteer in de lijst met Log Analytics-werk ruimten de werk ruimte die u wilt configureren voor het rapporteren van de agent.
3. Selecteer **Geavanceerde instellingen**.<br><br> ![Geavanceerde instellingen van Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecteer **Verbonden bronnen** en selecteer vervolgens **Windows-servers**.   
5. Kopieer en plak in uw favoriete editor, de **werk ruimte-id** en de **primaire sleutel**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Agent configureren voor het gebruik van TLS 1,2
Als u het [TLS 1,2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) -protocol wilt configureren voor communicatie tussen de Windows-agent en de log Analytics-service, kunt u de onderstaande stappen volgen om in te scha kelen voordat de agent op de virtuele machine is geïnstalleerd of daarna.

>[!NOTE]
>Als u een virtuele machine met Windows Server 2008 SP2 x64 wilt configureren voor het gebruik van TLS 1,2, moet u eerst de volgende [SHA-2-ondersteunings update voor ondertekening van code](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update) installeren voordat u de onderstaande stappen uitvoert. 
>

1. Ga naar de volgende registersubsleutel: **HKEY_LOCAL_MACHINE \system\currentcontrolset\control\securityproviders\schannel\protocols**
2. Een subsleutel maken onder **protocollen** voor TLS 1,2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1,2**
3. Maak een subsleutel van de **client** onder de subsleutel TLS 1,2-Protocol versie die u eerder hebt gemaakt. Bijvoorbeeld **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client**.
4. Maak de volgende DWORD-waarden onder **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client**:

    * **Ingeschakeld** [waarde = 1]
    * **DisabledByDefault** [waarde = 0]  

Configureer .NET Framework 4,6 of hoger voor de ondersteuning van beveiligde crypto grafie, omdat deze standaard is uitgeschakeld. De [sterke crypto grafie](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) maakt gebruik van veiliger netwerk protocollen zoals TLS 1,2 en blokkeert protocollen die niet beveiligd zijn. 

1. Ga naar de volgende registersubsleutel: **HKEY_LOCAL_MACHINE \software\microsoft\\. NETFramework\v4.0.30319**.  
2. Maak de DWORD-waarde **schusestrongcrypto toe** onder deze subsleutel met de waarde **1**.  
3. Ga naar de volgende registersubsleutel: **HKEY_LOCAL_MACHINE \software\wow6432node\microsoft\\. NETFramework\v4.0.30319**.  
4. Maak de DWORD-waarde **schusestrongcrypto toe** onder deze subsleutel met de waarde **1**. 
5. Start het systeem opnieuw op om de instellingen van kracht te laten worden. 

## <a name="install-the-agent-using-setup-wizard"></a>De agent installeren met de wizard Setup
Met de volgende stappen wordt de Log Analytics agent in Azure en Azure Government Cloud geïnstalleerd en geconfigureerd met behulp van de installatie wizard voor de agent op uw computer. Als u wilt weten hoe u de agent kunt configureren om ook te rapporteren aan een System Center Operations Manager-beheer groep, raadpleegt u [de Operations Manager-agent implementeren met de wizard Setup van agent](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. Selecteer in uw Log Analytics-werk ruimte op de pagina **Windows-servers** waar u eerder naar hebt genavigeerd, de juiste versie van de **Windows-agent downloaden** om te downloaden, afhankelijk van de processor architectuur van het Windows-besturings systeem.   
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

Als u klaar bent wordt de **MMA** in het **Configuratiescherm** weergegeven. Als u wilt bevestigen dat het rapport wordt gerapporteerd aan Log Analytics, raadpleegt u de [agent connectiviteit controleren op Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>De agent installeren met behulp van de opdracht regel
Het gedownloade bestand voor de agent is een op zichzelf staand installatie pakket.  Het installatie programma voor de agent en de ondersteunende bestanden bevinden zich in het pakket en moet worden uitgepakt om correct te kunnen installeren met behulp van de opdracht regel die wordt weer gegeven in de volgende voor beelden.    

>[!NOTE]
>Als u een agent wilt bijwerken, moet u de Log Analytics Scripting API gebruiken. Zie het onderwerp [beheer en onderhoud van de log Analytics-agent voor Windows en Linux](agent-manage.md) voor meer informatie.

De volgende tabel bevat de specifieke para meters die worden ondersteund door de installatie voor de agent, waaronder wanneer geïmplementeerd met Automation DSC.

|MMA-specifieke opties                   |Opmerkingen         |
|---------------------------------------|--------------|
| NOAPM=1                               | Optionele para meter. Hiermee wordt de agent geïnstalleerd zonder bewaking van .NET-toepassings prestaties.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = de agent configureren om te rapporteren aan een werk ruimte                |
|OPINSIGHTS_WORKSPACE_ID                | Werk ruimte-ID (GUID) voor de toe te voegen werk ruimte                    |
|OPINSIGHTS_WORKSPACE_KEY               | Werkruimte sleutel die in eerste instantie wordt gebruikt voor verificatie met de werk ruimte |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | De cloud omgeving opgeven waarin de werk ruimte zich bevindt <br> 0 = Azure-commerciële Cloud (standaard) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | URI voor de te gebruiken proxy |
|OPINSIGHTS_PROXY_USERNAME               | Gebruikers naam voor toegang tot een geverifieerde proxy |
|OPINSIGHTS_PROXY_PASSWORD               | Wacht woord voor toegang tot een geverifieerde proxy |

1. Als u de installatie bestanden van de agent wilt extra heren, voert u een opdracht prompt met verhoogde bevoegdheid uit `MMASetup-<platform>.exe /c` en wordt u gevraagd om het pad naar het bestand te extra heren.  U kunt ook het pad opgeven door de argumenten `MMASetup-<platform>.exe /c /t:<Full Path>`door te geven.  
2. Als u de agent op de achtergrond wilt installeren en configureren om te rapporteren aan een werk ruimte in de commerciële cloud van Azure, vanuit de map die u de installatie bestanden hebt uitgepakt, typt u: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   of om de agent te configureren om te rapporteren aan de Azure-Cloud voor de Amerikaanse overheid, typt u: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >De teken reeks waarden voor de para meters *OPINSIGHTS_WORKSPACE_ID* en *OPINSIGHTS_WORKSPACE_KEY* moeten worden ingekapseld in dubbele aanhalings tekens om te instrueren dat Windows Installer interprit als geldige opties voor het pakket. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>De agent installeren met behulp van DSC in Azure Automation

U kunt het volgende script voorbeeld gebruiken om de agent te installeren met behulp van Azure Automation DSC.   Als u geen Automation-account hebt, raadpleegt u aan de [slag met Azure Automation](/azure/automation/) om inzicht te krijgen in de vereisten en stappen voor het maken van een Automation-account dat is vereist voor het gebruik van Automation DSC.  Als u niet bekend bent met Automation DSC, raadpleegt u [aan de slag met Automation DSC](../../automation/automation-dsc-getting-started.md).

In het volgende voor beeld wordt de 64-bits agent geïnstalleerd, geïdentificeerd door de waarde van `URI`. U kunt ook de 32-bits versie gebruiken door de URI-waarde te vervangen. De Uri's voor beide versies zijn:

- Windows 64-bits agent- https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bits agent- https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Deze procedure en het script voorbeeld bieden geen ondersteuning voor de upgrade van de agent die al is geïmplementeerd op een Windows-computer.

De 32-bits en 64-bits versies van het agent pakket hebben verschillende product codes en nieuwe versies die zijn uitgebracht, hebben ook een unieke waarde.  De product code is een GUID die de principal-identificatie vormt van een toepassing of product en wordt vertegenwoordigd door de **eigenschap van** de Windows Installer.  De `ProductId` waarde in het script **MMAgent. ps1** moet overeenkomen met de product code van het 32-bits-of 64-bits agent-installatie pakket.

Als u de product code rechtstreeks uit het installatie pakket van de agent wilt ophalen, kunt u Orca. exe gebruiken uit de [Windows SDK onderdelen voor Windows Installer ontwikkel aars](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) die een onderdeel zijn van de Windows Software Development Kit of met behulp van Power shell volgens een [voorbeeld script](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) dat is geschreven door een micro soft Valuable Professional (MVP).  Voor beide benaderingen moet u eerst het **MOMagent. msi** -bestand extra heren uit het MMASetup-installatie pakket.  Dit wordt eerder weer gegeven in de eerste stap onder de sectie de [agent installeren met de opdracht regel](#install-the-agent-using-the-command-line).  

1. Importeer de xPSDesiredStateConfiguration DSC-module van [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) naar Azure Automation.  
2.  Maak Azure Automation variabele assets voor *OPSINSIGHTS_WS_ID* en *OPSINSIGHTS_WS_KEY*. Stel *OPSINSIGHTS_WS_ID* in op uw log Analytics werk ruimte-id en stel *OPSINSIGHTS_WS_KEY* in op de primaire sleutel van uw werk ruimte.
3.  Kopieer het script en sla het op als MMAgent. ps1.

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

4. Werk de `ProductId` waarde in het script bij met de product code die is geëxtraheerd uit de nieuwste versie van het installatie pakket van de agent met behulp van de methoden die eerder worden aanbevolen. 
5. [Importeer het configuratie script MMAgent. ps1](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) in uw Automation-account. 
5. [Wijs een Windows-computer of-knoop punt](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) toe aan de configuratie. Binnen 15 minuten controleert het knoop punt de configuratie en wordt de agent naar het knoop punt gepusht.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Agent connectiviteit met Log Analytics controleren

Nadat de installatie van de agent is voltooid, controleert u of deze is verbonden en de rapportage kan op twee manieren worden uitgevoerd.  

Ga in de computer naar **Configuratiescherm** en zoek het item **Microsoft Monitoring Agent**.  Selecteer de agent en op het tabblad **Azure log Analytics** een bericht met de melding: **micro soft monitoring agent is verbonden met de Microsoft Operations Management Suite-Service.**<br><br> ![Verbindingsstatus van MMA met Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

U kunt ook een eenvoudige logboek query uitvoeren in de Azure Portal.  

1. Zoek in het Azure Portal naar en selecteer **monitor**.
1. Selecteer **Logboeken** in het menu.
1. In het deel venster **Logboeken** in het query veld Type:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

In de zoek resultaten worden de heartbeat-records weer gegeven voor de computer die aangeeft dat deze is verbonden met en rapportage aan de service.   

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [de log Analytics-agent voor Windows en Linux beheren en onderhouden voor](agent-manage.md) meer informatie over het opnieuw configureren, bijwerken of verwijderen van de agent van de virtuele machine.

- Raadpleeg [problemen met de Windows-agent oplossen](agent-windows-troubleshoot.md) als u problemen ondervindt tijdens het installeren of beheren van de agent.
