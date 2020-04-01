---
title: Problemen met Azure Cloud Shell | Microsoft Documenten
description: Azure Cloud Shell oplossen
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 0b1b22095c77344ed71762d3d51b12f19d9f1811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458049"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Problemen oplossen & beperkingen van Azure Cloud Shell

Bekende oplossingen voor het oplossen van problemen in Azure Cloud Shell zijn:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Algemene probleemoplossing

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Fout met AzureAD-cmdlets in PowerShell

- **Details:** Wanneer u AzureAD-cmdlets uitvoert zoals `Get-AzureADUser` in Cloud `You must call the Connect-AzureAD cmdlet before calling any other cmdlets`Shell, ziet u mogelijk een fout: . 
- **Resolutie**: `Connect-AzureAD` Voer de cmdlet uit. Voorheen heeft Cloud Shell deze cmdlet automatisch uitgevoerd tijdens het opstarten van PowerShell. Om de starttijd te versnellen, wordt de cmdlet niet meer automatisch uitgevoerd. U ervoor kiezen om `Connect-AzureAD` het vorige gedrag te herstellen door toe te voegen aan het $PROFILE-bestand in PowerShell.

### <a name="early-timeouts-in-firefox"></a>Vroege time-outs in FireFox

- **Details:** Cloud Shell maakt gebruik van een open websocket om input/output door te geven aan uw browser. FireFox heeft vooraf ingestelde beleidsregels die de websocket voortijdig kunnen sluiten en vroege time-outs in Cloud Shell kunnen veroorzaken.
- **Resolutie**: Open FireFox en navigeer naar "about:config" in het URL-vak. Zoek naar "network.websocket.timeout.ping.request" en wijzig de waarde van 0 naar 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Cloud Shell uitschakelen in een vergrendelde netwerkomgeving

- **Details:** Beheerders kunnen de toegang tot Cloud Shell voor hun gebruikers uitschakelen. Cloud Shell maakt gebruik `ux.console.azure.com` van toegang tot het domein, die kan worden geweigerd, waardoor elke toegang tot de toegangspunten van Cloud Shell wordt gestopt, waaronder portal.azure.com, shell.azure.com, Visual Studio Code Azure Account-extensie en docs.microsoft.com. In de cloud van de `ux.console.azure.us`Amerikaanse regering is het ingangspunt ; er is geen overeenkomstige shell.azure.us.
- **Oplossing:** Beperk `ux.console.azure.com` `ux.console.azure.us` de toegang tot of via netwerkinstellingen tot uw omgeving. Het Pictogram Cloud Shell bestaat nog steeds in de Azure-portal, maar maakt geen verbinding met de service.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Dialoogvenster Opslag - Fout: 403 RequestDisallowedByPolicy

- **Details:** Wanneer u een opslagaccount maakt via Cloud Shell, is dit niet gelukt vanwege een Azure-beleid dat door uw beheerder is geplaatst. Foutbericht bevat:`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Oplossing:** neem contact op met uw Azure-beheerder om het Azure-beleid te verwijderen of bij te werken waarin het maken van opslag wordt ontkend.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Dialoogvenster Opslag - Fout: 400 niet-toegestane bewerking

- **Details**: Wanneer u een Azure Active Directory-abonnement gebruikt, u geen opslagruimte maken.
- **Oplossing:** gebruik een Azure-abonnement dat opslagbronnen kan maken. Azure AD-abonnementen kunnen geen Azure-bronnen maken.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminaloutput - Fout: kan geen terminal aansluiten: websocket kan niet worden ingesteld. Druk `Enter` op om opnieuw verbinding te maken.
- **Details**: Cloud Shell vereist de mogelijkheid om een websocketverbinding met cloud shell-infrastructuur tot stand te brengen.
- **Oplossing:** Controleer of u uw netwerkinstellingen hebt geconfigureerd om het verzenden van https-verzoeken en websocketaanvragen naar domeinen op *.console.azure.com in te schakelen.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Uw Cloud Shell-verbinding instellen op ondersteuning via TLS 1.2
 - **Details:** Als u de versie van TLS voor uw verbinding met Cloud Shell wilt definiëren, moet u browserspecifieke instellingen instellen.
 - **Resolutie:** Navigeer naar de beveiligingsinstellingen van uw browser en schakel het selectievakje in naast 'TLS 1.2 gebruiken'.

## <a name="bash-troubleshooting"></a>Problemen met bashen

### <a name="cannot-run-the-docker-daemon"></a>Kan de docker daemon niet uitvoeren

- **Details**: Cloud Shell gebruikt een container om uw shell-omgeving te hosten, waardoor het uitvoeren van de daemon is toegestaan.
- **Resolutie**: Gebruik [docker-machine](https://docs.docker.com/machine/overview/), die standaard is geïnstalleerd, om dockercontainers te beheren vanaf een externe Docker-host.

## <a name="powershell-troubleshooting"></a>PowerShell-probleemoplossing

### <a name="gui-applications-are-not-supported"></a>GUI-toepassingen worden niet ondersteund

- **Details:** Als een gebruiker een GUI-toepassing start, wordt de prompt niet teruggegeven. Wanneer bijvoorbeeld een persoonlijke GitHub-repo met tweefactorauthenticatie is ingeschakeld, wordt een dialoogvenster weergegeven voor het voltooien van de tweefactorverificatie.
- **Resolutie**: Sluit de shell en heropen deze opnieuw.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Problemen met extern beheer van Azure VM's oplossen
> [!NOTE]
> Azure VM's moeten een IP-adres hebben dat voor een openbaar gezicht is gericht.

- **Details:** Vanwege de standaardInstellingen voor Windows Firewall voor WinRM kan de gebruiker de volgende fout zien:`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Resolutie:** `Enable-AzVMPSRemoting` Voer uit om alle aspecten van PowerShell-remoting op de doelmachine mogelijk te maken.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir`werkt het resultaat niet bij in Azure-station

- **Details:** Om te optimaliseren voor de gebruikerservaring worden de resultaten van `dir` de cache standaard opgeslagen in Azure-station.
- **Oplossing:** Nadat u een Azure-bron hebt `dir -force` gemaakt, bijgewerkt of verwijderd, voert u de resultaten in het Azure-station bij.

## <a name="general-limitations"></a>Algemene beperkingen

Azure Cloud Shell heeft de volgende bekende beperkingen:

### <a name="quota-limitations"></a>Quotabeperkingen

Azure Cloud Shell heeft een limiet van 20 gelijktijdige gebruikers per tenant per regio. Als u meer gelijktijdige sessies dan de limiet probeert te openen, ziet u een fout 'Tenant User Over Quota'. Als u een legitieme behoefte hebt om meer sessies open te hebben dan dit (bijvoorbeeld voor trainingssessies), neemt u contact op met ondersteuning voordat uw verwachte gebruik wordt aangevraagd om een quotumverhoging aan te vragen.

Cloud Shell wordt geleverd als een gratis service en is ontworpen om te worden gebruikt om uw Azure-omgeving te configureren, niet als een computerplatform voor algemene doeleinden. Overmatig geautomatiseerd gebruik kan worden beschouwd als in strijd met de Azure-servicevoorwaarden en kan ertoe leiden dat de toegang tot Cloud Shell wordt geblokkeerd.

### <a name="system-state-and-persistence"></a>Systeemstatus en persistentie

De machine die uw Cloud Shell-sessie biedt, is tijdelijk en wordt gerecycled nadat uw sessie gedurende 20 minuten inactief is. Cloud Shell vereist dat een Azure-bestandsshare wordt gemonteerd. Als gevolg hiervan moet uw abonnement opslagbronnen kunnen instellen om toegang te krijgen tot Cloud Shell. Andere overwegingen zijn:

- Met gemonteerde opslag blijven alleen `clouddrive` wijzigingen in de map bestaan. In Bash `$HOME` wordt uw directory ook gehandhaafd.
- Azure-bestandsshares kunnen alleen worden gestart vanuit uw [toegewezen regio.](persisting-shell-storage.md#mount-a-new-clouddrive)
  - Voer in `env` Bash uit om `ACC_LOCATION`je regio te vinden die is ingesteld als .
- Azure Files ondersteunt alleen lokaal redundante opslag- en georedundante opslagaccounts.

### <a name="browser-support"></a>Browserondersteuning

Cloud Shell ondersteunt de nieuwste versies van de volgende browsers:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari in privémodus wordt niet ondersteund.

### <a name="copy-and-paste"></a>Kopiëren en plakken

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Gebruikslimieten

Cloud Shell is bedoeld voor interactieve use cases. Als gevolg hiervan worden langlopende niet-interactieve sessies zonder waarschuwing beëindigd.

### <a name="user-permissions"></a>Gebruikersmachtigingen

Machtigingen worden ingesteld als gewone gebruikers zonder sudo-toegang. Elke installatie `$Home` buiten uw directory wordt niet gehandhaafd.

## <a name="bash-limitations"></a>Bash beperkingen

### <a name="editing-bashrc"></a>Bewerken .bashrc

Wees voorzichtig bij het bewerken van .bashrc, dit kan onverwachte fouten veroorzaken in Cloud Shell.

## <a name="powershell-limitations"></a>PowerShell-beperkingen

### <a name="preview-version-of-azuread-module"></a>Voorbeeldversie van AzureAD-module

Momenteel `AzureAD.Standard.Preview`is een preview-versie van .NET Standard-gebaseerde module beschikbaar. Deze module biedt dezelfde `AzureAD`functionaliteit als .

### <a name="sqlserver-module-functionality"></a>`SqlServer`modulefunctionaliteit

De `SqlServer` module in Cloud Shell heeft alleen prerelease-ondersteuning voor PowerShell Core. In het `Invoke-SqlCmd` bijzonder, is nog niet beschikbaar.

### <a name="default-file-location-when-created-from-azure-drive"></a>Standaardbestandslocatie wanneer deze is gemaakt vanuit Azure-station

Met PowerShell-cmdlets kunnen gebruikers geen bestanden maken onder het Azure-station. Wanneer gebruikers nieuwe bestanden maken met andere tools, zoals vim `$HOME` of nano, worden de bestanden standaard opgeslagen in de standaardbestanden.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Tab voltooiing kan psreadline uitzondering gooien

Als de PSReadline EditMode van de gebruiker is ingesteld op Emacs, probeert de gebruiker alle mogelijkheden weer te geven via tabvoltooiing en is de venstergrootte te klein om alle mogelijkheden weer te geven, psreadline gooit onverwerkte uitzondering.

### <a name="large-gap-after-displaying-progress-bar"></a>Grote kloof na het weergeven van de voortgangsbalk

Als een opdracht of gebruikersactie een voortgangsbalk weergeeft, zoals een tabblad dat in het `Azure:` station wordt voltooid, is het mogelijk dat de cursor niet goed is ingesteld en er een gat wordt weergegeven waar de voortgangsbalk voorheen was.

### <a name="random-characters-appear-inline"></a>Willekeurige tekens verschijnen inline

De cursorpositievolgordecodes kunnen `5;13R`bijvoorbeeld worden weergegeven in de gebruikersinvoer. De tekens kunnen handmatig worden verwijderd.

## <a name="personal-data-in-cloud-shell"></a>Persoonsgegevens in Cloud Shell

Azure Cloud Shell neemt uw persoonlijke gegevens serieus, de gegevens die zijn vastgelegd en opgeslagen door de Azure Cloud Shell-service worden gebruikt om standaardinstellingen voor uw ervaring te bieden, zoals uw meest recent gebruikte shell, de gewenste tekengrootte, het voorkeurslettertype en bestandssharedetails die teruggaan in de cloud. Als u deze gegevens wilt exporteren of verwijderen, gebruikt u de volgende instructies.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exporteren
Als u de gebruikersinstellingen wilt **exporteren,** worden de volgende opdrachten voor cloud shell voor u opgeslagen, zoals de voorkeursshell, de tekengrootte en het lettertypetype.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Voer de volgende opdrachten uit in Bash of PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Verwijderen
Als u uw gebruikersinstellingen wilt **verwijderen,** worden de volgende opdrachten voor Cloud Shell voor u opgeslagen, zoals de voorkeursshell, de tekengrootte en het lettertype. De volgende keer dat u Cloud Shell start, wordt u gevraagd om opnieuw een bestandsshare aan boord te nemen. 

>[!Note]
> Als u uw gebruikersinstellingen verwijdert, wordt het werkelijke delen van Azure-bestanden niet verwijderd. Ga naar uw Azure-bestanden om die actie te voltooien.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Voer de volgende opdrachten uit in Bash of PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Azure-overheidsbeperkingen
Azure Cloud Shell in Azure Government is alleen toegankelijk via de Azure-portal.

>[!Note]
> Verbinding maken met GCC-High of DoD Clouds van de overheid voor Exchange Online wordt momenteel niet ondersteund.
