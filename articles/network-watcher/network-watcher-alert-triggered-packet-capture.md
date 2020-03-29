---
title: Packet capture gebruiken om proactieve netwerkbewaking uit te voeren met waarschuwingen - Azure-functies
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u een waarschuwing maakt die wordt geactiveerd bij het vastleggen van pakketten met Azure Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: ea506e137d71fc3124a4f93f1e97750a08dd4284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842934"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Packet capture gebruiken voor proactieve netwerkbewaking met waarschuwingen en Azure-functies

Network Watcher packet capture creëert capture sessies om het verkeer in en uit virtuele machines te volgen. Het vastleggen-bestand kan een filter hebben dat is gedefinieerd om alleen het verkeer te volgen dat u wilt controleren. Deze gegevens worden vervolgens opgeslagen in een opslagblob of lokaal op de gastmachine.

Deze mogelijkheid kan op afstand worden gestart vanuit andere automatiseringsscenario's, zoals Azure-functies. Packet capture geeft u de mogelijkheid om proactieve opnames uit te voeren op basis van gedefinieerde netwerkanomalieën. Andere toepassingen zijn het verzamelen van netwerkstatistieken, het verkrijgen van informatie over netwerkinbraken, het debuggen van client-servercommunicatie en meer.

Resources die 24/7 in Azure worden geïmplementeerd, worden uitgevoerd. U en uw medewerkers kunnen de status van alle resources niet 24/7 actief controleren. Wat gebeurt er bijvoorbeeld als er om 02.00 uur een probleem optreedt?

Door Network Watcher, waarschuwingen en functies vanuit het Azure-ecosysteem te gebruiken, u proactief reageren met de gegevens en hulpprogramma's om problemen in uw netwerk op te lossen.

![Scenario][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

* De nieuwste versie van [Azure PowerShell](/powershell/azure/install-Az-ps).
* Een bestaand exemplaar van Network Watcher. Als u er nog geen hebt, [maakt u een exemplaar van Network Watcher](network-watcher-create.md).
* Een bestaande virtuele machine in dezelfde regio als Network Watcher met de [Windows-extensie](../virtual-machines/windows/extensions-nwa.md) of [Linux virtuele machine extensie](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Scenario

In dit voorbeeld verzendt uw VM meer TCP-segmenten dan normaal en wilt u worden gewaarschuwd. TCP-segmenten worden hier als voorbeeld gebruikt, maar u elke waarschuwingsvoorwaarde gebruiken.

Wanneer u wordt gewaarschuwd, wilt u gegevens op pakketniveau ontvangen om te begrijpen waarom de communicatie is toegenomen. Vervolgens u stappen ondernemen om de virtuele machine terug te brengen naar regelmatige communicatie.

In dit scenario wordt ervan uitgegaan dat u een bestaande instantie van Network Watcher en een resourcegroep met een geldige virtuele machine hebt.

De volgende lijst is een overzicht van de werkstroom die plaatsvindt:

1. Er wordt een waarschuwing geactiveerd op uw vm.
1. De waarschuwing roept uw Azure-functie aan via een webhook.
1. Uw Azure-functie verwerkt de waarschuwing en start een network watcher-pakketopnamesessie.
1. De pakketopname wordt uitgevoerd op de VM en verzamelt verkeer.
1. Het pakketopnamebestand wordt geüpload naar een opslagaccount voor beoordeling en diagnose.

Om dit proces te automatiseren, maken en verbinden we een waarschuwing op onze VM om te activeren wanneer het incident zich voordoet. We maken ook een functie om in Te bellen in Network Watcher.

Dit scenario doet het volgende:

* Hiermee maakt u een Azure-functie waarmee een pakketopname wordt gestart.
* Hiermee maakt u een waarschuwingsregel op een virtuele machine en configureert u de waarschuwingsregel om de Azure-functie aan te roepen.

## <a name="create-an-azure-function"></a>Een Azure-functie maken

De eerste stap is het maken van een Azure-functie om de waarschuwing te verwerken en een pakketopname te maken.

1. Selecteer in de [Azure-portal](https://portal.azure.com)de optie **Een resource** > **Compute** > **Function App maken**.

    ![Een functie-app maken][1-1]

2. Voer in het blade van de **functie-app** de volgende waarden in en selecteer **OK** om de app te maken:

    |**Instelling** | **Waarde** | **Details** |
    |---|---|---|
    |**App-naam**|PacketCaptureExample PacketCaptureExample|De naam van de functie-app.|
    |**Abonnement**|[Uw abonnement] Het abonnement waarvoor de functie-app te maken.||
    |**Resourcegroep**|PacketCaptureRG PacketCaptureRG PacketCaptureRG|De resourcegroep die de functie-app bevat.|
    |**Hostingabonnement**|Verbruiksabonnement| Het type abonnement dat uw functie-app gebruikt. Opties zijn Verbruik of Azure App Service-abonnement. |
    |**Locatie**|VS - centraal| De regio waarin de functie-app kan worden gemaakt.|
    |**Opslagaccount**|{autogenerated}| Het opslagaccount dat Azure Functions nodig heeft voor opslag voor algemene doeleinden.|

3. Selecteer in het blade **Van PacketCaptureExample Function Apps** de optie >**+****Functie Aangepast** **functies** > .

4. Selecteer **HttpTrigger-Powershell**en voer de resterende gegevens in. Selecteer ten slotte als u de functie wilt maken, **Maken**.

    |**Instelling** | **Waarde** | **Details** |
    |---|---|---|
    |**Scenario**|Experimenteel|Type scenario|
    |**Een naam voor de functie opgeven**|AlertPacketCapturePowerShell|Naam van de functie|
    |**Autorisatieniveau**|Functie|Machtigingsniveau voor de functie|

![Voorbeeld van functies][functions1]

> [!NOTE]
> De PowerShell-sjabloon is experimenteel en heeft geen volledige ondersteuning.

Aanpassingen zijn vereist voor dit voorbeeld en worden uitgelegd in de volgende stappen.

### <a name="add-modules"></a>Modules toevoegen

Als u Network Watcher PowerShell-cmdlets wilt gebruiken, uploadt u de nieuwste PowerShell-module naar de functie-app.

1. Voer de volgende PowerShell-opdracht uit op uw lokale machine met de nieuwste Azure PowerShell-modules:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    In dit voorbeeld vindt u het lokale pad van uw Azure PowerShell-modules. Deze mappen worden in een latere stap gebruikt. De modules die in dit scenario worden gebruikt zijn:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![PowerShell-mappen][functions5]

1. Selecteer **Instellingen voor functie-apps** > **Ga naar App Service Editor**.

    ![Instellingen voor functie-app][functions2]

1. Klik met de rechtermuisknop op de map **AlertPacketCapturePowershell** en maak vervolgens een map met **azuremodules**. 

4. Maak een submap voor elke module die u nodig hebt.

    ![Map en submappen][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Klik met de rechtermuisknop op de submap **Az.Network** en selecteer **Bestanden uploaden**. 

6. Ga naar uw Azure-modules. Selecteer in de lokale **map Az.Network** alle bestanden in de map. Selecteer vervolgens **OK**. 

7. Herhaal deze stappen voor **Az.Accounts** en **Az.Resources**.

    ![Bestanden uploaden][functions6]

1. Nadat u klaar bent, moet elke map de PowerShell-modulebestanden van uw lokale machine hebben.

    ![PowerShell-bestanden][functions7]

### <a name="authentication"></a>Authentication

Als u de PowerShell-cmdlets wilt gebruiken, moet u zich verifiëren. U configureert verificatie in de functie-app. Als u verificatie wilt configureren, moet u omgevingsvariabelen configureren en een versleuteld sleutelbestand uploaden naar de functie-app.

> [!NOTE]
> Dit scenario bevat slechts één voorbeeld van het implementeren van verificatie met Azure-functies. Er zijn andere manieren om dit te doen.

#### <a name="encrypted-credentials"></a>Versleutelde referenties

Met het volgende PowerShell-script wordt een sleutelbestand gemaakt met de naam **PassEncryptKey.key.** Het biedt ook een versleutelde versie van het wachtwoord dat wordt geleverd. Dit wachtwoord is hetzelfde wachtwoord dat is gedefinieerd voor de Azure Active Directory-toepassing die wordt gebruikt voor verificatie.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

Maak in de App Service Editor van de functie-app een map met de naam **sleutels** onder **AlertPacketCapturePowerShell**. Upload vervolgens het **PassEncryptKey.key-bestand** dat u in het vorige PowerShell-voorbeeld hebt gemaakt.

![Functietoets][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Waarden ophalen voor omgevingsvariabelen

De uiteindelijke vereiste is het instellen van de omgevingsvariabelen die nodig zijn om toegang te krijgen tot de waarden voor verificatie. In de volgende lijst worden de omgevingsvariabelen weergegeven die zijn gemaakt:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

De client-id is de toepassings-id van een toepassing in Azure Active Directory.

1. Als u nog geen toepassing hebt om te gebruiken, voert u het volgende voorbeeld uit om een toepassing te maken.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Het wachtwoord dat u gebruikt bij het maken van de toepassing moet hetzelfde wachtwoord zijn dat u eerder hebt gemaakt bij het opslaan van het sleutelbestand.

1. Selecteer **Abonnementen**in de Azure-portal . Selecteer het te gebruiken abonnement en selecteer **vervolgens Toegangsbeheer (IAM)**.

    ![Functies IAM][functions9]

1. Kies het account dat u wilt gebruiken en selecteer **Eigenschappen**. Kopieer de toepassings-id.

    ![Toepassings-id functies][functions10]

#### <a name="azuretenant"></a>AzureTenant

Verkrijg de tenant-id door het volgende PowerShell-voorbeeld uit te voeren:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

De waarde van de azurecredpassword-omgevingsvariabele is de waarde die u krijgt bij het uitvoeren van het volgende PowerShell-voorbeeld. Dit voorbeeld is hetzelfde voorbeeld dat wordt weergegeven in de vorige sectie **Versleutelde referenties.** De waarde die nodig is, `$Encryptedpassword` is de uitvoer van de variabele.  Dit is het servicehoofdwachtwoord dat u hebt versleuteld met behulp van het PowerShell-script.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>De omgevingsvariabelen opslaan

1. Ga naar de functie-app. Selecteer vervolgens **Instellingen voor functie-apps** > **App configureren**.

    ![App-instellingen configureren][functions11]

1. Voeg de omgevingsvariabelen en hun waarden toe aan de app-instellingen en selecteer **Opslaan**.

    ![Instellingen voor apps][functions12]

### <a name="add-powershell-to-the-function"></a>PowerShell toevoegen aan de functie

Het is nu tijd om te bellen naar Network Watcher vanuit de Azure-functie. Afhankelijk van de vereisten kan de implementatie van deze functie variëren. De algemene stroom van de code is echter als volgt:

1. Invoerparameters verwerken.
2. Bestaande pakketopnamen opvragen om limieten te verifiëren en naamconflicten op te lossen.
3. Maak een pakketopname met de juiste parameters.
4. Poll pakket periodiek vast te leggen totdat het is voltooid.
5. Laat de gebruiker weten dat de pakketopnamesessie is voltooid.

Het volgende voorbeeld is PowerShell-code die in de functie kan worden gebruikt. Er zijn waarden die moeten worden vervangen voor **subscriptionId,** **resourceGroupName**en **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>De functie-URL ophalen 
1. Nadat u de functie hebt gemaakt, configureert u uw waarschuwing om de URL aan te roepen die aan de functie is gekoppeld. Kopieer de functie-URL vanuit uw functie-app om deze waarde te krijgen.

    ![De functie-URL zoeken][functions13]

2. Kopieer de functie-URL voor uw functie-app.

    ![De functie-URL kopiëren][2]

Als u aangepaste eigenschappen nodig hebt in de payload van de webhook POST-aanvraag, raadpleegt u [Een webhook configureren op een Azure-metrische waarschuwing](../azure-monitor/platform/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Een waarschuwing configureren op een virtuele machine

Waarschuwingen kunnen worden geconfigureerd om personen op de hoogte te stellen wanneer een specifieke statistiek een drempel waardestreep overschrijdt die eraan is toegewezen. In dit voorbeeld bevindt de waarschuwing zich op de TCP-segmenten die worden verzonden, maar de waarschuwing kan worden geactiveerd voor veel andere statistieken. In dit voorbeeld wordt een waarschuwing geconfigureerd om een webhook aan te roepen om de functie aan te roepen.

### <a name="create-the-alert-rule"></a>De waarschuwingsregel maken

Ga naar een bestaande virtuele machine en voeg vervolgens een waarschuwingsregel toe. Meer gedetailleerde documentatie over het configureren van waarschuwingen is te vinden bij [Waarschuwingen maken in Azure Monitor for Azure services - Azure portal.](../monitoring-and-diagnostics/insights-alerts-portal.md) Voer de volgende waarden in het regelblad **Waarschuwen** in en selecteer **OK**.

  |**Instelling** | **Waarde** | **Details** |
  |---|---|---|
  |**Naam**|TCP_Segments_Sent_Exceeded|Naam van de waarschuwingsregel.|
  |**Beschrijving**|VERZONDen TCP-segmenten overschreden drempelwaarde|De beschrijving voor de waarschuwingsregel.|
  |**Gegevens**|VERZONDEN TCP-segmenten| De statistiek die moet worden gebruikt om de waarschuwing te activeren. |
  |**Voorwaarde**|Groter dan| De voorwaarde om te gebruiken bij de evaluatie van de statistiek.|
  |**Drempel**|100| De waarde van de statistiek die de waarschuwing activeert. Deze waarde moet worden ingesteld op een geldige waarde voor uw omgeving.|
  |**Periode**|In de laatste vijf minuten| Hiermee bepaalt u de periode waarin u de drempelwaarde op de statistiek moet zoeken.|
  |**Webhook**|[webhook URL van functie-app]| De webhook-URL van de functie-app die in de vorige stappen is gemaakt.|

> [!NOTE]
> De statistiek TCP-segmenten is standaard niet ingeschakeld. Meer informatie over het inschakelen van aanvullende statistieken door naar [Monitoring en diagnostische gegevens inschakelen](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md)te gaan.

## <a name="review-the-results"></a>De resultaten bekijken

Na de criteria voor de waarschuwingstriggers wordt een pakketopname gemaakt. Ga naar Network Watcher en selecteer **Packet capture**. Op deze pagina u de koppeling voor het vastleggen van pakketten selecteren om de packet capture te downloaden.

![Pakketopname weergeven][functions14]

Als het opnamebestand lokaal wordt opgeslagen, u het ophalen door u aan te melden bij de virtuele machine.

Zie Aan de slag met Azure [Blob-opslag met .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)voor instructies over het downloaden van bestanden uit Azure-opslagaccounts. Een ander hulpmiddel dat u gebruiken is [Storage Explorer.](https://storageexplorer.com/)

Nadat uw opname is gedownload, u deze bekijken met behulp van elk hulpmiddel dat een **.cap-bestand** kan lezen. Hieronder volgen links naar twee van deze tools:

- [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
- [Wireshark](https://www.wireshark.org/)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bekijken van uw pakketopnames door [packet capture-analyse met Wireshark te](network-watcher-deep-packet-inspection.md)bezoeken.


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png
