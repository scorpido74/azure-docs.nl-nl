---
title: Power shell gebruiken om Extern bureaublad in te scha kelen voor een rol
titleSuffix: Azure Cloud Services
description: Uw Azure Cloud service-toepassing configureren met behulp van Power shell om extern bureau blad-verbindingen toe te staan
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: e4e8dca6c5359e865e6a17fc47fe47802b0ee9e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75386116"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Verbinding met extern bureaublad inschakelen voor een rol in azure Cloud Services met behulp van Power shell

> [!div class="op_single_selector"]
> * [Azure-portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Met Extern bureaublad kunt u toegang krijgen tot het bureau blad van een rol die wordt uitgevoerd in Azure. U kunt een Extern bureaublad verbinding gebruiken om problemen met uw toepassing op te lossen en te onderzoeken terwijl deze wordt uitgevoerd.

In dit artikel wordt beschreven hoe u extern bureau blad kunt inschakelen voor uw Cloud service rollen met behulp van Power shell. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor de vereiste onderdelen voor dit artikel. Power shell gebruikt de Extern bureaublad extensie zodat u Extern bureaublad kunt inschakelen nadat de toepassing is geïmplementeerd.

## <a name="configure-remote-desktop-from-powershell"></a>Extern bureaublad configureren vanuit Power shell
Met de cmdlet [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) kunt u extern bureaublad inschakelen voor opgegeven rollen of voor alle rollen van de implementatie van de Cloud service. Met de cmdlet kunt u de gebruikers naam en het wacht woord voor de gebruiker van het externe bureau blad opgeven via de para meter *Credential* waarmee een PSCredential-object wordt geaccepteerd.

Als u Power shell interactief gebruikt, kunt u het PSCredential-object eenvoudig instellen door de cmdlet [Get-credentials aan](https://technet.microsoft.com/library/hh849815.aspx) te roepen.

```powershell
$remoteusercredentials = Get-Credential
```

Met deze opdracht wordt een dialoog venster weer gegeven waarin u de gebruikers naam en het wacht woord voor de externe gebruiker op een veilige manier kunt opgeven.

Omdat Power shell in automatiserings scenario's helpt, kunt u het **PSCredential** -object ook zo instellen dat er geen gebruikers interactie is vereist. Eerst moet u een veilig wacht woord instellen. U begint met het opgeven van een wacht woord voor tekst zonder opmaak, converteer het naar een veilige teken reeks met behulp van [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Vervolgens moet u deze beveiligde teken reeks converteren naar een versleutelde standaard teken reeks met behulp van [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Nu kunt u deze versleutelde standaard teken reeks opslaan in een bestand met behulp van [set-content](https://technet.microsoft.com/library/ee176959.aspx).

U kunt ook een beveiligd wachtwoord bestand maken, zodat u niet elke keer het wacht woord hoeft in te voeren. Een beveiligd wachtwoord bestand is ook beter dan een bestand met tekst zonder opmaak. Gebruik de volgende Power shell om een beveiligd wachtwoord bestand te maken:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Zorg er bij het instellen van het wacht woord voor dat u voldoet aan de [complexiteits vereisten](https://technet.microsoft.com/library/cc786468.aspx).

Als u het referentie object wilt maken vanuit het beveiligde wachtwoord bestand, moet u de inhoud van het bestand lezen en terugconverteren naar een veilige teken reeks met behulp van [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

De cmdlet [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) accepteert ook een *verval* parameter, waarmee een **datum/tijd** wordt opgegeven waarop het gebruikers account verloopt. U kunt bijvoorbeeld instellen dat het account een paar dagen vanaf de huidige datum en tijd verloopt.

Dit Power shell-voor beeld laat zien hoe u de Extern bureaublad-extensie kunt instellen voor een Cloud service:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
U kunt desgewenst ook de implementatie site en rollen opgeven waarvoor u extern bureau blad wilt inschakelen. Als deze para meters niet zijn opgegeven, maakt de cmdlet extern bureau blad op alle rollen in de **productie** -implementatie site.

De uitbrei ding Extern bureaublad is gekoppeld aan een implementatie. Als u een nieuwe implementatie voor de service maakt, moet u extern bureau blad inschakelen voor die implementatie. Als u altijd extern bureau blad wilt inschakelen, kunt u overwegen de Power shell-scripts te integreren in de implementatie werk stroom.

## <a name="remote-desktop-into-a-role-instance"></a>Extern bureaublad naar een rolinstantie

De cmdlet [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) wordt gebruikt voor extern bureau blad in een specifieke rolinstantie van uw Cloud service. U kunt de para meter *LocalPath* gebruiken om het RDP-bestand lokaal te downloaden. U kunt ook de para meter *starten* gebruiken om het dialoog venster verbinding met extern bureaublad direct te starten om toegang te krijgen tot de instantie van de Cloud service-rol.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Controleren of Extern bureaublad extensie is ingeschakeld voor een service

De cmdlet [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) geeft aan dat extern bureau blad is ingeschakeld of uitgeschakeld op een service-implementatie. De cmdlet retourneert de gebruikers naam voor de extern bureau blad-gebruiker en de rollen waarvoor de extern bureau blad-uitbrei ding is ingeschakeld. Dit gebeurt standaard op de implementatie site en u kunt in plaats daarvan de faserings sleuf gebruiken.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Extern bureaublad extensie verwijderen van een service

Als u de extern bureau blad-extensie al hebt ingeschakeld in een implementatie en u de instellingen voor extern bureau blad wilt bijwerken, moet u eerst de extensie verwijderen. En schakel deze opnieuw in met de nieuwe instellingen. Als u bijvoorbeeld een nieuw wacht woord wilt instellen voor het externe gebruikers account of het account is verlopen. Dit is vereist voor bestaande implementaties waarvoor de extern bureau blad-extensie is ingeschakeld. Voor nieuwe implementaties kunt u de extensie gewoon direct Toep assen.

Als u de extern bureau blad-extensie wilt verwijderen uit de implementatie, kunt u de cmdlet [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) gebruiken. U kunt desgewenst ook de implementatie site en rol opgeven van waaruit u de extern bureau blad-extensie wilt verwijderen.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Als u de extensie configuratie volledig wilt verwijderen, moet u de cmdlet *Remove* aanroepen met de para meter **UninstallConfiguration** .
>
> Met de para meter **UninstallConfiguration** verwijdert u de extensie configuratie die wordt toegepast op de service. Elke extensie configuratie is gekoppeld aan de service configuratie. Het aanroepen van de *Remove* cmdlet zonder **UninstallConfiguration** koppelt de <mark>implementatie</mark> van de extensie configuratie, waardoor de uitbrei ding daarom effectief wordt verwijderd. De extensie configuratie blijft echter gekoppeld aan de service.

## <a name="additional-resources"></a>Aanvullende bronnen

[Cloud Services configureren](cloud-services-how-to-configure-portal.md)


