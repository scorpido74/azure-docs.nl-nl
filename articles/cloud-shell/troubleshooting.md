---
title: Problemen met Azure Cloud Shell oplossen | Microsoft Docs
description: Problemen met Azure Cloud Shell oplossen
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
ms.openlocfilehash: 91dc87cd6bda93663fb4b4eae3d498ae56ba4b3e
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169603"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Problemen met & beperkingen van Azure Cloud Shell oplossen

Bekende oplossingen voor het oplossen van problemen in Azure Cloud Shell zijn onder andere:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Algemene probleem oplossing

### <a name="early-timeouts-in-firefox"></a>Vroege time-outs in FireFox

- **Details**: Cloud shell gebruikt een open WebSocket om invoer/uitvoer door te geven aan uw browser. FireFox heeft vooraf ingestelde beleids regels waarmee de WebSocket voor tijdig kan worden gesloten voor vroege time-outs in Cloud Shell.
- **Oplossing**: Open Firefox en navigeer naar "about: config" in het vak URL. Zoek naar ' Network. WebSocket. time-out. ping. request ' en wijzig de waarde van 0 in 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Cloud Shell uitschakelen in een vergrendelde netwerk omgeving

- **Details**: beheerders kunnen de toegang tot Cloud shell voor hun gebruikers uitschakelen. Cloud Shell gebruikt toegang tot het domein `ux.console.azure.com`, dat kan worden geweigerd, waardoor alle toegang tot de entrypoints van Cloud Shell, waaronder portal.azure.com, shell.azure.com, Visual Studio code Azure-account extensie en docs.microsoft.com, wordt gestopt.
- **Oplossing**: Beperk de toegang tot `ux.console.azure.com` via netwerk instellingen tot uw omgeving. Het pictogram Cloud Shell blijft aanwezig in portal.azure.com, maar er wordt geen verbinding gemaakt met de service.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Opslag dialoogvenster-fout: 403 RequestDisallowedByPolicy

- **Details**: wanneer u een opslag account maakt met behulp van Cloud shell, is dit niet mogelijk als gevolg van een Azure-beleid dat door de beheerder is geplaatst. Fout bericht bevat: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Oplossing**: Neem contact op met uw Azure-beheerder om het Azure-beleid dat het maken van opslag weigert te verwijderen of bij te werken.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Opslag dialoogvenster-fout: 400 DisallowedOperation

- **Details**: wanneer u een Azure Active Directory-abonnement gebruikt, kunt u geen opslag ruimte maken.
- **Oplossing**: gebruik een Azure-abonnement dat kan worden gebruikt om opslag resources te maken. Azure AD-abonnementen kunnen geen Azure-resources maken.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal-uitvoer-fout: kan geen verbinding maken met de terminal: WebSocket kan niet tot stand worden gebracht. Druk op `Enter` om opnieuw verbinding te maken.
- **Details**: Cloud shell moet de mogelijkheid hebben om een WebSocket-verbinding tot stand te brengen met Cloud shell-infra structuur.
- **Oplossing**: Controleer of u uw netwerk instellingen hebt geconfigureerd zodat u HTTPS-aanvragen en WebSocket-aanvragen kunt verzenden naar domeinen op *. console.Azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Stel uw Cloud Shell-verbinding in op ondersteuning met behulp van TLS 1,2
 - **Details**: als u de versie van TLS wilt definiëren voor uw verbinding met Cloud shell, moet u browsercompatibele instellingen instellen.
 - **Oplossing**: Navigeer naar de beveiligings instellingen van uw browser en schakel het selectie vakje in naast TLS 1,2 gebruiken.

## <a name="bash-troubleshooting"></a>Bash problemen oplossen

### <a name="cannot-run-the-docker-daemon"></a>Kan de docker-daemon niet uitvoeren

- **Details**: Cloud shell gebruikt een container voor het hosten van uw shell-omgeving, omdat het uitvoeren van de daemon niet is toegestaan.
- **Oplossing**: gebruik [docker-machine](https://docs.docker.com/machine/overview/), die standaard wordt geïnstalleerd, om docker-containers van een externe docker-host te beheren.

## <a name="powershell-troubleshooting"></a>Problemen met Power shell oplossen

### <a name="gui-applications-are-not-supported"></a>GUI-toepassingen worden niet ondersteund

- **Details**: als een gebruiker een GUI-toepassing start, wordt er geen prompt weer gegeven. Als er bijvoorbeeld een kloon is van een persoonlijke GitHub-opslag plaats waarvoor twee ledige verificatie is ingeschakeld, wordt er een dialoog venster weer gegeven voor het volt ooien van de twee ledige verificatie.
- **Oplossing**: Sluit de shell en open deze opnieuw.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Problemen met extern beheer van virtuele Azure-machines oplossen
> [!NOTE]
> Virtuele Azure-machines moeten een openbaar gericht IP-adres hebben.

- **Details**: vanwege de standaard Windows Firewall-instellingen voor WinRM, kan de gebruiker de volgende fout zien: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Oplossing**: Voer `Enable-AzVMPSRemoting` uit om alle aspecten van externe communicatie van Power shell op de doel computer in te scha kelen.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` werkt het resultaat niet bij in azure drive

- **Details**: standaard worden de resultaten van `dir` in de cache van Azure drive opgeslagen om te worden geoptimaliseerd voor gebruikers ervaring.
- **Oplossing**: nadat u een Azure-resource hebt gemaakt, bijgewerkt of verwijderd, voert u `dir -force` uit om de resultaten in het Azure-station bij te werken.

## <a name="general-limitations"></a>Algemene beperkingen

Azure Cloud Shell heeft de volgende bekende beperkingen:

### <a name="system-state-and-persistence"></a>Systeem status en persistentie

De computer die uw Cloud Shell-sessie levert, is tijdelijk en wordt gerecycled nadat uw sessie 20 minuten niet actief is. Cloud Shell moet een Azure-bestands share worden gekoppeld. Als gevolg hiervan moet uw abonnement opslag bronnen kunnen instellen voor toegang tot Cloud Shell. Andere overwegingen zijn onder andere:

- Bij gekoppelde opslag worden alleen wijzigingen in de map @no__t 0 bewaard. In bash wordt de map @no__t 0 ook bewaard.
- Azure-bestands shares kunnen alleen worden gekoppeld vanuit uw [toegewezen regio](persisting-shell-storage.md#mount-a-new-clouddrive).
  - Voer `env` uit in bash om uw regio in te stellen als `ACC_LOCATION`.
- Azure Files ondersteunt alleen lokaal redundante opslag en geografisch redundante opslag accounts.

### <a name="browser-support"></a>Browser ondersteuning

Cloud Shell ondersteunt de nieuwste versies van de volgende browsers:

- Microsoft Edge
- Micro soft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari in de privé modus wordt niet ondersteund.

### <a name="copy-and-paste"></a>Kopiëren en plakken

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Gebruiksbeperkingen

Cloud Shell is bedoeld voor interactieve use-cases. Als gevolg hiervan worden langlopende niet-interactieve sessies beëindigd zonder dat dit wordt gewaarschuwd.

### <a name="user-permissions"></a>Gebruikersmachtigingen

Machtigingen worden ingesteld als gewone gebruikers zonder sudo-toegang. Een installatie buiten uw `$Home`-map wordt niet bewaard.

## <a name="bash-limitations"></a>Bash-beperkingen

### <a name="editing-bashrc"></a>Bewerken. bashrc

Wees voorzichtig bij het bewerken van. bashrc. Dit kan leiden tot onverwachte fouten in Cloud Shell.

## <a name="powershell-limitations"></a>Power shell-beperkingen

### <a name="preview-version-of-azuread-module"></a>Preview-versie van de AzureAD-module

Momenteel is `AzureAD.Standard.Preview`, een preview-versie van de .NET Standard-based module, beschikbaar. Deze module biedt dezelfde functionaliteit als `AzureAD`.

### <a name="sqlserver-module-functionality"></a>functionaliteit van de module `SqlServer`

De module `SqlServer` die is opgenomen in Cloud Shell heeft alleen ondersteuning voor de Prerelease van Power shell core. Met name `Invoke-SqlCmd` is nog niet beschikbaar.

### <a name="default-file-location-when-created-from-azure-drive"></a>Standaard bestands locatie wanneer deze wordt gemaakt vanuit een Azure-station

Met behulp van Power shell-cmdlets kunnen gebruikers geen bestanden maken onder het Azure-station. Wanneer gebruikers nieuwe bestanden maken met andere hulpprogram ma's, zoals vim of nano, worden de bestanden standaard opgeslagen in de `$HOME`.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Door tabs volt ooien kan uitzonde ring genereren PSReadline

Als de PSReadline-EditMode van de gebruiker is ingesteld op emacs, probeert de gebruiker alle mogelijkheden weer te geven via het tabblad voltooiing en is het venster te klein om alle mogelijkheden weer te geven, PSReadline wordt een onverwerkte uitzonde ring gegenereerd.

### <a name="large-gap-after-displaying-progress-bar"></a>Grote tussen ruimte na het weer geven van de voortgangs balk

Als een opdracht of gebruikers actie een voortgangs balk weergeeft, zoals een tab die in het station `Azure:` is voltooid, is het mogelijk dat de cursor niet op de juiste wijze is ingesteld en dat er een lege ruimte wordt weer gegeven waarin de voortgangs balk zich eerder bevond.

### <a name="random-characters-appear-inline"></a>Wille keurige tekens worden inline weer gegeven

De cursor positie reeks codes, bijvoorbeeld `5;13R`, kunnen worden weer gegeven in de gebruikers invoer. De tekens kunnen hand matig worden verwijderd.

## <a name="personal-data-in-cloud-shell"></a>Persoonlijke gegevens in Cloud Shell

Azure Cloud Shell uw persoonlijke gegevens ernstig neemt, worden de gegevens die zijn vastgelegd en opgeslagen door de Azure Cloud Shell-service gebruikt om standaard waarden te bieden voor uw ervaring, zoals de meest recent gebruikte shell, de voorkeurs teken grootte, het voorkeurs lettertype en de bestands share gegevens dat is een back-upcloud-schijf. Als u deze gegevens wilt exporteren of verwijderen, gebruikt u de volgende instructies.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exporteren
Voer de volgende opdrachten uit om de gebruikers instellingen te **exporteren** Cloud shell voor u opslaat, zoals de voorkeurs shell, de teken grootte en het letter type.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)
2. Voer de volgende opdrachten uit in bash of Power shell:

Bash

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
Voer de volgende opdrachten uit om uw gebruikers instellingen te **verwijderen** Cloud shell voor u opslaat, zoals de voorkeurs shell, de teken grootte en het letter type. De volgende keer dat u Cloud Shell start, wordt u gevraagd om een bestands share opnieuw op te heffen. 

>[!Note]
> Als u uw gebruikers instellingen verwijdert, wordt de daad werkelijke Azure Files share niet verwijderd. Ga naar uw Azure Files om deze actie te volt ooien.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)
2. Voer de volgende opdrachten uit in bash of Power shell:

Bash

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Azure Government beperkingen
Azure Cloud Shell in Azure Government is alleen toegankelijk via de Azure Portal.
