---
title: Externe on-premises resources beheren met PowerShell-functies
description: Meer informatie over het configureren van hybride verbindingen in Azure Relay om een PowerShell-functie-app te verbinden met on-premises resources, die vervolgens kunnen worden gebruikt om de on-premises bron op afstand te beheren.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226938"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Hybride omgevingen beheren met PowerShell in Azure-functies en hybride verbindingen van App Service

Met de functie Hybride verbindingen van Azure App Service u toegang krijgen tot bronnen in andere netwerken. Meer informatie over deze mogelijkheid vindt u in de documentatie [Hybride verbindingen.](../app-service/app-service-hybrid-connections.md) In dit artikel wordt beschreven hoe u deze mogelijkheid gebruiken om PowerShell-functies uit te voeren die gericht zijn op een on-premises server. Deze server kan vervolgens worden gebruikt om alle resources in de on-premises omgeving te beheren vanuit een Azure PowerShell-functie.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Een on-premises server configureren voor PowerShell-remoren

Het volgende script maakt PowerShell remoting mogelijk en het maakt een nieuwe firewallregel en een WinRM-https-listener. Voor testdoeleinden wordt een zelfondertekend certificaat gebruikt. In een productieomgeving raden we u aan een ondertekend certificaat te gebruiken.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Een PowerShell-functie-app maken in de portal

De functie Hybride verbindingen van App-service is alleen beschikbaar in basis-, standaard- en geïsoleerde prijsplannen. Wanneer u de functie-app maakt met PowerShell, maakt of selecteert u een van deze abonnementen.

1. Selecteer in de [Azure-portal](https://portal.azure.com) **+ Een resource maken** in het menu aan de linkerkant en selecteer vervolgens De app **Functie**.

1. Selecteer **app-serviceplan**voor **Hostingen**en selecteer **vervolgens het App-serviceplan/Locatie**.

1. Selecteer **Nieuw maken,** typ een naam van een **App-serviceplan,** kies een **locatie** in een [gebied](https://azure.microsoft.com/regions/) bij u in de buurt of in de buurt van andere services waartoe uw functies worden geopend en selecteer vervolgens **De prijslaag**.

1. Kies het S1-standaardabonnement en selecteer **Toepassen**.

1. Selecteer **OK** om het abonnement te maken en configureer vervolgens de resterende **functie-app-instellingen** zoals die in de tabel zijn opgegeven direct na de volgende schermafbeelding:

    ![PowerShell Core-functie-app](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **App-naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z`, `0-9` en `-`.  | 
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **Resourcegroep** |  myResourceGroup | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. U ook de voorgestelde waarde gebruiken. |
    | **Besturingssysteem** | Voorkeursbesturingssysteem | selecteer Windows. |
    | **Runtimestack** | Voorkeurstaal | Kies powershell-kern. |
    | **Opslag** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. De namen van het opslagaccount moeten 3 tot 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken.
    | **Application Insights** | Standaard | Hiermee maakt u een application insights-bron met dezelfde *app-naam* in het dichtstbijzijnde ondersteunde gebied. Door deze instelling uit te breiden, u de **naam van** de nieuwe bron wijzigen of een andere **locatie** kiezen in een [azure-regio](https://azure.microsoft.com/global-infrastructure/geographies/) waar u uw gegevens wilt opslaan. |

1. Nadat uw instellingen zijn gevalideerd, selecteert u **Maken**.

1. Selecteer het **pictogram Melding** in de rechterbovenhoek van de portal en wacht op het bericht 'Geslaagd e-werk'.

1. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken. U ook **Vastmaken aan het dashboard**selecteren. Vastmaken maakt het eenvoudiger om vanuit je dashboard terug te keren naar deze functie-appbron.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Een hybride verbinding maken voor de functie-app

Hybride verbindingen worden geconfigureerd vanuit het netwerkgedeelte van de functie-app:

1. Selecteer het tabblad **Platformfuncties** in de functie-app en selecteer **Netwerken**. 
   ![App-overzicht voor platformnetwerken](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Selecteer **Eindpunten voor hybride verbindingen configureren**.
   ![Netwerken](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Selecteer **Hybride verbinding toevoegen**.
   ![Hybride verbinding](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Voer informatie in over de hybride verbinding zoals direct na de volgende schermafbeelding wordt weergegeven. U ervoor zorgen dat de instelling **Endpoint Host** overeenkomt met de hostnaam van de on-premises server, waardoor het gemakkelijker wordt om de server later te onthouden wanneer u externe opdrachten uitvoert. De poort komt overeen met de standaardservicepoort voor extern beheer van Windows die eerder op de server is gedefinieerd.
  ![Hybride verbinding toevoegen](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Hybride verbindingsnaam**: ContosoHybridOnPremisesServer
    
    **Endpoint Host**: financiën1
    
    **Eindpuntpoort**: 5986
    
    **Servicebus naamruimte:** Nieuw maken
    
    **Locatie**: Kies een beschikbare locatie
    
    **Naam**: contosopowershellhybrid

5. Selecteer **OK** om de hybride verbinding te maken.

## <a name="download-and-install-the-hybrid-connection"></a>De hybride verbinding downloaden en installeren

1. Selecteer **Verbindingsbeheer downloaden** om het .msi-bestand lokaal op uw computer op te slaan.
![Installatieprogramma downloaden](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Kopieer het .msi-bestand van uw lokale computer naar de on-premises server.
1. Voer het installatieprogramma Hybrid Connection Manager uit om de service op de on-premises server te installeren.
![Hybride verbinding installeren](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. Open vanuit de portal de hybride verbinding en kopieer de tekenreeks voor de gatewayverbinding naar het klembord.
![Hybride verbindingstekenreeks kopiëren](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Open de gebruikersinterface van Hybrid Connection Manager op de on-premises server.
![Gebruikersinterface voor hybride verbinding openen](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Selecteer de knop **Handmatig invoeren** en plak de verbindingstekenreeks op het klembord.
![Verbinding plakken](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Start de Hybride Verbindingsbeheer opnieuw van PowerShell als deze niet wordt weergegeven als verbonden.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Een app-instelling maken voor het wachtwoord van een beheerdersaccount

1. Selecteer het tabblad **Platformfuncties** in de functie-app.
1. Selecteer **Configuratie**onder **Algemene instellingen**.
![Platformconfiguratie selecteren](./media/functions-hybrid-powershell/select-configuration.png)  
1. Nieuwe **toepassingsinstelling uitvouwen** om een nieuwe instelling voor het wachtwoord te maken.
1. Geef de instelling _ContosoUserPassword_een naam en voer het wachtwoord in.
1. Selecteer **OK** en sla op om het wachtwoord op te slaan in de functietoepassing.
![App-instelling toevoegen voor wachtwoord](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Een functie http-trigger maken om te testen

1. Maak een nieuwe HTTP-triggerfunctie vanuit de functie-app.
![Nieuwe HTTP-trigger maken](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Vervang de PowerShell-code uit de sjabloon door de volgende code:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

3. Selecteer **Opslaan** en **uitvoeren** om de functie te testen.
![De functie-app testen](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>Beheer van andere systemen on-premises

U de aangesloten on-premises server gebruiken om verbinding te maken met andere servers en beheersystemen in de lokale omgeving. Hiermee u uw datacenterbewerkingen vanuit Azure beheren met behulp van uw PowerShell-functies. In het volgende script wordt een PowerShell-configuratiesessie geregistreerd die wordt uitgevoerd onder de opgegeven referenties. Deze referenties moeten zijn voor een beheerder op de externe servers. U deze configuratie vervolgens gebruiken om toegang te krijgen tot andere eindpunten op de lokale server of het datacenter.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Vervang de volgende variabelen in dit script door de toepasselijke waarden uit uw omgeving:
* $HybridEndpoint
* $RemoteServer

In de twee voorgaande scenario's u uw on-premises omgevingen verbinden en beheren met PowerShell in Azure-functies en hybride verbindingen. We raden u aan meer te weten te komen over [hybride verbindingen](../app-service/app-service-hybrid-connections.md) en [PowerShell in functies.](./functions-reference-powershell.md)

U ook [virtuele Azure-netwerken](./functions-create-vnet.md) gebruiken om verbinding te maken met uw on-premises omgeving via Azure-functies.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het werken met PowerShell-functies](functions-reference-powershell.md)
