---
title: Externe on-premises resources beheren met behulp van Power shell-functies
description: Meer informatie over het configureren van Hybride verbindingen in Azure Relay om een Power shell-functie-app te verbinden met on-premises resources, die vervolgens kan worden gebruikt om de on-premises resource op afstand te beheren.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74226938"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Hybride omgevingen beheren met Power shell in Azure Functions en App Service Hybride verbindingen

Met de functie Azure App Service Hybride verbindingen kunt u toegang krijgen tot bronnen in andere netwerken. Meer informatie over deze mogelijkheid vindt u in de [hybride verbindingen](../app-service/app-service-hybrid-connections.md) -documentatie. In dit artikel wordt beschreven hoe u deze mogelijkheid kunt gebruiken om Power shell-functies uit te voeren die gericht zijn op een on-premises server. Deze server kan vervolgens worden gebruikt voor het beheren van alle resources in de on-premises omgeving vanuit een Azure PowerShell-functie.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Een on-premises server configureren voor externe communicatie met Power shell

Het volgende script schakelt externe communicatie van Power shell in en maakt een nieuwe firewall regel en een WinRM https-listener. Voor test doeleinden wordt een zelfondertekend certificaat gebruikt. In een productie omgeving wordt u aangeraden een ondertekend certificaat te gebruiken.

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

## <a name="create-a-powershell-function-app-in-the-portal"></a>Een Power shell-functie-app maken in de portal

De functie App Service Hybride verbindingen is alleen beschikbaar in de abonnementen Basic, Standard en geïsoleerd. Wanneer u de functie-app met Power Shell maakt, maakt of selecteert u een van deze plannen.

1. Selecteer in de [Azure Portal](https://portal.azure.com) **+ een resource maken** in het menu aan de linkerkant en selecteer vervolgens functie- **app**.

1. Voor het **hosting plan**selecteert u **app service plan**en selecteert u vervolgens **app service plan/locatie**.

1. Selecteer **Nieuw maken**, typ een naam voor het **app service plan** , kies een **locatie** in een [regio](https://azure.microsoft.com/regions/) bij u in de buurt of in de buurt van andere services die uw functies gebruiken en selecteer vervolgens **prijs categorie**.

1. Kies het standaard abonnement S1 en selecteer vervolgens **Toep assen**.

1. Selecteer **OK** om het plan te maken en configureer vervolgens de resterende **functie-app** -instellingen zoals opgegeven in de tabel direct na de volgende scherm afbeelding:

    ![Power shell Core-functie-app](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Instelling      | Voorgestelde waarde  | Beschrijving                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **App-naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens zijn `a-z`, `0-9` en `-`.  | 
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **Resource groep** |  myResourceGroup | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. U kunt ook de voorgestelde waarde gebruiken. |
    | **Besturingssysteem** | Voorkeurs besturingssysteem | selecteer Windows. |
    | **Runtimestack** | Voorkeurstaal | Kies Power shell core. |
    | **Storage** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslag accounts moeten tussen de 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U kunt ook een bestaand account gebruiken.
    | **Application Insights** | Standaard | Hiermee maakt u een Application Insights bron van dezelfde *app-naam* in de dichtstbijzijnde ondersteunde regio. Door deze instelling uit te breiden, kunt u de **nieuwe resource naam** wijzigen of een andere **locatie** kiezen in een regio van [Azure](https://azure.microsoft.com/global-infrastructure/geographies/) waar u uw gegevens wilt opslaan. |

1. Nadat uw instellingen zijn gevalideerd, selecteert u **maken**.

1. Selecteer het **meldings** pictogram in de rechter bovenhoek van de portal en wacht op het bericht implementatie voltooid.

1. Selecteer **Naar de resource gaan** om uw nieuwe functie-app te bekijken. U kunt ook **vastmaken aan dash board**selecteren. Vastmaken maakt het gemakkelijker om terug te gaan naar deze functie-app-resource vanuit uw dash board.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Een hybride verbinding maken voor de functie-app

Hybride verbindingen worden geconfigureerd via het gedeelte netwerken van de functie-app:

1. Selecteer het tabblad **platform functies** in de functie-app en selecteer vervolgens **netwerken**. 
   ![App-overzicht voor platform netwerken](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Selecteer **de eind punten voor uw hybride verbindingen configureren**.
   ![Netwerken](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Selecteer **hybride verbinding toevoegen**.
   ![Hybride verbinding](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Voer informatie in over de hybride verbinding zoals deze wordt weer gegeven na de volgende scherm afbeelding. U hebt de mogelijkheid om de instelling **endpoint host** te laten overeenkomen met de hostnaam van de on-premises server, zodat u de server gemakkelijker later kunt onthouden wanneer u externe opdrachten uitvoert. De poort komt overeen met de standaard Windows Remote Management-service poort die eerder op de server is gedefinieerd.
  ![Hybride verbinding toevoegen](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Naam van hybride verbinding**: ContosoHybridOnPremisesServer
    
    **Eindpunt host**: finance1
    
    **Eindpunt poort**: 5986
    
    **Servicebus naam ruimte**: nieuwe maken
    
    **Locatie**: Kies een beschik bare locatie
    
    **Naam**: contosopowershellhybrid

5. Selecteer **OK** om de hybride verbinding te maken.

## <a name="download-and-install-the-hybrid-connection"></a>De hybride verbinding downloaden en installeren

1. Selecteer **verbindings beheer downloaden** om het MSI-bestand lokaal op uw computer op te slaan.
![Installatie programma downloaden](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Kopieer het MSI-bestand van uw lokale computer naar de on-premises server.
1. Voer het Hybrid Connection Manager-installatie programma uit om de service op de on-premises server te installeren.
![Hybride verbinding installeren](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. Open de hybride verbinding vanuit de portal en kopieer de gateway connection string naar het klem bord.
![Hybride connection string kopiëren](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Open de Hybrid Connection Manager gebruikers interface op de on-premises server.
![Gebruikers interface voor hybride verbinding openen](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Selecteer de knop **hand matig invoeren** en plak de Connection String van het klem bord.
![Verbinding plakken](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Start de Hybrid Connection Manager van Power shell als deze niet wordt weer gegeven als verbonden.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Een app-instelling maken voor het wacht woord van een beheerders account

1. Selecteer het tabblad **platform functies** in de functie-app.
1. Onder **algemene instellingen**selecteert u **configuratie**.
![Platform configuratie selecteren](./media/functions-hybrid-powershell/select-configuration.png)  
1. Vouw de **instelling nieuwe toepassing** uit om een nieuwe instelling voor het wacht woord te maken.
1. Noem de instelling _ContosoUserPassword_en voer het wacht woord in.
1. Selecteer **OK** en vervolgens opslaan om het wacht woord op te slaan in de functie toepassing.
![App-instelling voor wacht woord toevoegen](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Een http-trigger functie maken om te testen

1. Maak een nieuwe HTTP-trigger functie vanuit de functie-app.
![Nieuwe HTTP-trigger maken](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Vervang de Power shell-code uit de sjabloon door de volgende code:

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

## <a name="managing-other-systems-on-premises"></a>On-premises andere systemen beheren

U kunt de verbonden on-premises server gebruiken om verbinding te maken met andere servers en beheer systemen in de lokale omgeving. Hiermee kunt u uw Data Center-bewerkingen beheren vanuit Azure met behulp van uw Power shell-functies. Met het volgende script wordt een Power shell-configuratie sessie geregistreerd die wordt uitgevoerd onder de gegeven referenties. Deze referenties moeten voor een beheerder op de externe servers zijn. U kunt deze configuratie vervolgens gebruiken voor toegang tot andere eind punten op de lokale server of het Data Center.

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

In de twee voor gaande scenario's kunt u verbinding maken met uw on-premises omgevingen en deze beheren met behulp van Power shell in Azure Functions en Hybride verbindingen. We raden u aan meer te weten te komen over [hybride verbindingen](../app-service/app-service-hybrid-connections.md) en [Power shell in functions](./functions-reference-powershell.md).

U kunt ook Azure [Virtual Networks](./functions-create-vnet.md) gebruiken om verbinding te maken met uw on-premises omgeving via Azure functions.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Meer informatie over het werken met Power shell-functies](functions-reference-powershell.md)
