---
title: PowerShell gebruiken om Extern bureaublad in te schakelen voor een rol
titleSuffix: Azure Cloud Services
description: Uw azure cloud service-toepassing configureren met PowerShell om externe bureaubladverbindingen toe te staan
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: e4e8dca6c5359e865e6a17fc47fe47802b0ee9e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386116"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Verbinding met extern bureaublad inschakelen voor een rol in Azure Cloud Services met PowerShell

> [!div class="op_single_selector"]
> * [Azure-portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Powershell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Met Extern bureaublad heeft u toegang tot het bureaublad van een rol die in Azure wordt uitgevoerd. U een verbinding met Extern bureaublad gebruiken om problemen met uw toepassing op te lossen en te diagnosticeren terwijl deze wordt uitgevoerd.

In dit artikel wordt beschreven hoe u extern bureaublad in schakelt in uw Cloud Service Rollen met PowerShell. Zie [Hoe u Azure PowerShell installeert en configureert](/powershell/azure/overview) voor de vereisten die nodig zijn voor dit artikel. PowerShell maakt gebruik van de Extensie Extern bureaublad, zodat u Extern bureaublad inschakelen nadat de toepassing is geïmplementeerd.

## <a name="configure-remote-desktop-from-powershell"></a>Extern bureaublad configureren vanuit PowerShell
Met de cmdlet [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) u Extern bureaublad inschakelen voor bepaalde rollen of alle rollen van uw implementatie van uw cloudservice. Met de cmdlet u de gebruikersnaam en het wachtwoord voor de externe desktopgebruiker opgeven via de parameter *Credential* die een PSCredential-object accepteert.

Als u PowerShell interactief gebruikt, u het PSCredential-object eenvoudig instellen door de cmdlet [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) aan te roepen.

```powershell
$remoteusercredentials = Get-Credential
```

Met deze opdracht wordt een dialoogvenster weergegeven waarmee u de gebruikersnaam en het wachtwoord voor de externe gebruiker op een veilige manier invoeren.

Aangezien PowerShell helpt bij automatiseringsscenario's, u het **PSCredential-object** ook zo instellen dat gebruikersinteractie niet vereist is. Eerst moet u een veilig wachtwoord instellen. U begint met het opgeven van een wachtwoord met platte tekst om te zetten naar een beveiligde tekenreeks met [behulp van ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Vervolgens moet u deze beveiligde tekenreeks omzetten in een versleutelde standaardtekenreeks met [ConvertFrom-SecureString.](https://technet.microsoft.com/library/hh849814.aspx) Nu u deze versleutelde standaardtekenreeks opslaan in een bestand met [set-inhoud](https://technet.microsoft.com/library/ee176959.aspx).

U ook een veilig wachtwoordbestand maken, zodat u het wachtwoord niet elke keer hoeft in te voeren. Ook een veilig wachtwoordbestand is beter dan een plain text-bestand. Gebruik de volgende PowerShell om een veilig wachtwoordbestand te maken:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Controleer bij het instellen van het wachtwoord of u voldoet aan de [complexiteitsvereisten.](https://technet.microsoft.com/library/cc786468.aspx)

Als u het referentieobject wilt maken uit het beveiligde wachtwoordbestand, moet u de inhoud van het bestand lezen en deze converteren naar een beveiligde tekenreeks met [ConvertTo-SecureString.](https://technet.microsoft.com/library/hh849818.aspx)

De cmdlet [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) accepteert ook een parameter *Expiratie,* die een **Datumtijd** aangeeft waarop het gebruikersaccount verloopt. U bijvoorbeeld instellen dat het account een paar dagen na de huidige datum en tijd verloopt.

In dit PowerShell-voorbeeld ziet u hoe u de Extern bureaublad-extensie instelt op een cloudservice:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
U ook optioneel de implementatiesleuf en rollen opgeven waarop u extern bureaublad wilt inschakelen. Als deze parameters niet zijn opgegeven, schakelt de cmdlet extern bureaublad in op alle rollen in de **implementatiesleuf Productie.**

De extensie Extern bureaublad is gekoppeld aan een implementatie. Als u een nieuwe implementatie voor de service maakt, moet u extern bureaublad inschakelen bij die implementatie. Als u altijd extern bureaublad wilt hebben ingeschakeld, moet u overwegen de PowerShell-scripts in uw implementatieworkflow te integreren.

## <a name="remote-desktop-into-a-role-instance"></a>Extern bureaublad in een rolinstantie

De [cmdlet Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) wordt gebruikt om extern bureaublad te gebruiken in een specifieke rolinstantie van uw cloudservice. U de parameter *LocalPath* gebruiken om het RDP-bestand lokaal te downloaden. U de parameter *Starten* gebruiken om het dialoogvenster Verbinding met extern bureaublad direct te starten om toegang te krijgen tot het rolexemplaar van de cloudservice.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Controleren of de extensie Extern bureaublad is ingeschakeld voor een service

De cmdlet [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) geeft aan dat extern bureaublad is ingeschakeld of uitgeschakeld bij een service-implementatie. De cmdlet retourneert de gebruikersnaam voor de extern bureaublad-gebruiker en de rollen waarvoor de externe bureaubladextensie is ingeschakeld. Dit gebeurt standaard in de implementatiesleuf en u ervoor kiezen om de faseringssleuf te gebruiken.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Extern bureaublad-extensie uit een service verwijderen

Als u de externe bureaubladextensie al hebt ingeschakeld bij een implementatie en de instellingen voor extern bureaublad moet bijwerken, verwijdert u eerst de extensie. En schakel het opnieuw in met de nieuwe instellingen. Als u bijvoorbeeld een nieuw wachtwoord wilt instellen voor het externe gebruikersaccount of als het account is verlopen. Dit is vereist voor bestaande implementaties waarbij de externe bureaubladextensie is ingeschakeld. Voor nieuwe implementaties u de extensie eenvoudig direct toepassen.

Als u de externe bureaubladextensie uit de implementatie wilt verwijderen, u de cmdlet [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) gebruiken. U ook optioneel de implementatiesleuf en -rol opgeven waaruit u de extensie voor extern bureaublad wilt verwijderen.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Als u de extensieconfiguratie volledig wilt verwijderen, moet u *de* verwijdercmdlet aanroepen met de parameter **UninstallConfiguration.**
>
> Met de parameter **UninstallConfiguration** wordt elke extensieconfiguratie die op de service wordt toegepast, verwijdert. Elke extensieconfiguratie is gekoppeld aan de serviceconfiguratie. Het *aanroepen van* de verwijdercmdlet zonder **VerwijderenConfiguratie** distantieert de <mark>implementatie</mark> van de extensieconfiguratie, waardoor de extensie effectief wordt verwijderd. De extensieconfiguratie blijft echter gekoppeld aan de service.

## <a name="additional-resources"></a>Aanvullende bronnen

[Cloud Services configureren](cloud-services-how-to-configure-portal.md)


