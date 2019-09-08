---
title: Externe on-premises resources beheren met Power shell Azure Functions
description: Meer informatie over het configureren van Hybride verbindingen in Azure Relay om een Power shell-functie-app te verbinden met on-premises resources, die vervolgens kan worden gebruikt om de on-premises resource op afstand te beheren.
author: eamono
manager: gailey777
ms.service: azure-functions
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 0acbe0892be50e9e1747e5839101110d4adcebcf
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775414"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Hybride omgevingen beheren met Power shell in Azure Functions en App Service Hybride verbindingen

Met de Azure App Service Hybride verbindingen kunt u toegang krijgen tot bronnen in andere netwerken. Meer informatie over deze mogelijkheid vindt u in de documentatie van [hybride verbindingen](../app-service/app-service-hybrid-connections.md) . In de volgende informatie wordt beschreven hoe u deze mogelijkheid kunt gebruiken om Power shell-functies uit te voeren die gericht zijn op een on-premises server. Deze server kan vervolgens worden gebruikt voor het beheren van alle resources in de on-premises omgeving vanuit een Azure PowerShell-functie.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Een on-premises server configureren voor externe communicatie met Power shell

In het onderstaande script wordt externe communicatie met Power shell ingeschakeld, wordt er een nieuwe firewall regel gemaakt en een HTTPS-listener van WinRM. Voor test doeleinden wordt een zelfondertekend certificaat gebruikt. Het is raadzaam een ondertekend certificaat te gebruiken voor de productie.

```powershell
# For configuration of WinRM, please see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management

# Enable PowerShell remoting
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986 
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Een Power shell-functie-app maken in de portal

App Service Hybride verbindingen zijn alleen beschikbaar in de basis-, standaard-en geïsoleerde prijs plannen. Wanneer u de functie-app met Power Shell maakt, maakt of selecteert u een van deze plannen.

1. Ga naar de [Azure Portal](https://portal.azure.com).

1. Selecteer **+ een resource maken** aan de linkerkant en kies vervolgens functie- **app**.

1. Voor het **hosting plan**kiest u **app service plan**en selecteert u vervolgens **app service plan/locatie**.

1. Selecteer **Nieuw maken**, typ een naam voor het **app service plan** , kies een **locatie** in een [regio](https://azure.microsoft.com/regions/) bij u in de buurt of in de buurt van andere services die uw functies gebruiken en selecteer vervolgens **prijs categorie**.

1. Kies het standaard abonnement S1 en selecteer vervolgens **Toep assen**.

1. Selecteer **OK** om het plan te maken en gebruik vervolgens de resterende functie-app-instellingen, zoals opgegeven in de tabel onder de afbeelding. 

    ![Power shell Core-functie-app](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Instelling      | Voorgestelde waarde  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Naam van app** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z`, `0-9` en `-`.  | 
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **Resourcegroep** |  myResourceGroup | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. U kunt ook de voorgestelde waarde gebruiken. |
    | **Besturingssysteem** | Voorkeurs besturingssysteem | Selecteer Windows. |
    | **Runtimestack** | Voorkeurstaal | Kies Power shell core. |
    | **Storage** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken.
    | **Application Insights** | Standaard | Hiermee maakt u een Application Insights bron van dezelfde *app-naam* in de dichtstbijzijnde ondersteunde regio. Door deze instelling uit te breiden, kunt u de **nieuwe resource naam** wijzigen of een andere **locatie** in een [Azure-regio](https://azure.microsoft.com/global-infrastructure/geographies/) kiezen waar u uw gegevens wilt opslaan. |

1. Nadat uw instellingen zijn gevalideerd, selecteert u **maken**.

1. Selecteer het meldingspictogram in de rechterbovenhoek van de portal en zoek het bericht **Implementatie voltooid**.

1. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken. U kunt ook **vastmaken aan dash board**selecteren. Vastmaken maakt het gemakkelijker om terug te gaan naar deze functie-app-resource vanuit uw dash board.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Een hybride verbinding maken voor de functie-app

Hybride verbindingen worden geconfigureerd vanuit het gedeelte netwerken van de functie-app.

1. Selecteer het tabblad platform van de functie-app en selecteer netwerken.
![App-overzicht voor platform netwerken](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Selecteer de eind punten voor uw hybride verbindingen configureren.
![Netwerken](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Selecteer hybride verbinding toevoegen.
![Hybride verbinding](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Voer informatie in over de hybride verbinding zoals hieronder wordt weer gegeven. De eindpunt host kan eventueel overeenkomen met de hostnaam van de on-premises server, zodat het gemakkelijker wordt om de server te onthouden wanneer externe opdrachten worden uitgevoerd. De poort komt overeen met de standaard poort voor de Windows Remote Management-service die eerder op de server is gedefinieerd.
![Hybride verbinding toevoegen](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Naam van hybride verbinding** ContosoHybridOnPremisesServer
    
    Finance1 van **eind punt**
    
    **Eindpunt poort** 5986
    
    **Servicebus-naam ruimte** Nieuwe maken
    
    **Locatie** Een beschik bare locatie kiezen
    
    **Naam** contosopowershellhybrid

5. Klik op OK om de hybride verbinding te maken

## <a name="download-and-install-the-hybrid-connection"></a>De hybride verbinding downloaden en installeren

1. Selecteer het pictogram verbindings beheer downloaden om het. msi-bestand lokaal op uw computer op te slaan.
![Installatie programma downloaden](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Kopieer het. msi-bestand van uw lokale computer naar de on-premises server.
1. Voer het installatie programma voor hybride verbindingen uit om de service op de on-premises server te installeren.
![Hybride verbinding installeren](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. Open de hybride verbinding vanuit de portal en kopieer de gateway connection string naar het klem bord.
![Hybride connection string kopiëren](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Open de Hybrid Connection Manager gebruikers interface op de on-premises server.
![Gebruikers interface voor hybride verbinding openen](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Selecteer de knop hand matig invoeren en plak de connection string van het klem bord.
![Verbinding plakken](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Start de Hybrid Connection Manager vanuit Power shell als deze niet is verbonden.
```powershell
Restart-Service HybridConnectionManager
```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Een app-instelling maken voor het wacht woord van een beheerders account

1. Selecteer het tabblad platform in de functie toepassing
1. Selecteer de configuratie in de sectie ![algemene instellingen platform configuratie selecteren](./media/functions-hybrid-powershell/select-configuration.png)  
1. Selecteer nieuwe toepassings instelling om een nieuwe instelling voor het wacht woord te maken
1. Geef een naam op voor de instelling ContosoUserPassword en voer het wacht woord in
1. Selecteer OK en vervolgens opslaan om het wacht woord op te slaan in ![de functie toepassing app-instelling voor wacht woord toevoegen](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Een http-trigger functie maken om te testen

1. Een nieuwe http-trigger functie maken vanuit de functie ![-app nieuwe http-trigger maken](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Vervang de Power shell-code uit de sjabloon door de volgende code:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword
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

3. Klik op opslaan en uitvoeren om de functie ![-app testen te testen](./media/functions-hybrid-powershell/test-function-hybrid.png)  

## <a name="managing-other-systems-on-premises"></a>On-premises andere systemen beheren

U kunt de verbonden on-premises server gebruiken om verbinding te maken met andere servers en beheer systemen in de lokale omgeving. Hiermee kunt u uw Data Center-bewerkingen beheren vanuit Azure met behulp van uw Power shell-functies. Met het volgende script wordt een Power shell-configuratie sessie geregistreerd die wordt uitgevoerd onder de opgegeven referenties. Deze referenties moeten een beheerder zijn op de externe servers. U kunt deze configuratie vervolgens gebruiken voor toegang tot andere eind punten in de lokale server of het Data Center.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be conected to run remote PowerShell commands on
$RemoteServer = "finance2"

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

# Script to run on the jump box to run against the second machine
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server
        hostname
        # Write out the hostname of the remote server
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

Vervang deze variabelen in het bovenstaande script door de waarden die u in uw omgeving hebt:
* $HybridEndpoint
* $RemoteServer

Met de bovenstaande twee scenario's kunt u verbinding maken met uw on-premises omgevingen en deze beheren met Power shell in Azure Functions en Hybride verbindingen. Meer informatie over [hybride verbindingen](../app-service/app-service-hybrid-connections.md) of [Power shell vindt u in functies](./functions-reference-powershell.md) in de documentatie.

U kunt ook Azure [Virtual Networks](./functions-create-vnet.md) gebruiken om verbinding te maken met uw on-premises omgeving met behulp van Azure functions.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het werken met Power shell-functies](functions-reference-powershell.md)
