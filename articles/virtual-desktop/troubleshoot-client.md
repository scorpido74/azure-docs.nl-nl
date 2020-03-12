---
title: Problemen met Extern bureaublad client Windows virtueel bureau blad oplossen-Azure
description: Problemen oplossen bij het instellen van client verbindingen in een Windows Virtual Desktop-Tenant omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: e3a240901ffca2c126e2b61eaee0cf287cc31d6e
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127506"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Problemen met de Extern bureaublad-client oplossen

In dit artikel worden veelvoorkomende problemen met de Extern bureaublad-client beschreven en hoe u deze kunt oplossen.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Extern bureaublad-client voor Windows 7 of Windows 10 reageert niet meer of kan niet worden geopend

Gebruik de volgende Power shell-cmdlets om out-of-band-client registers (OOB) op te schonen.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Ga naar **%AppData%\RdClientRadc** en verwijder alle inhoud.

Verwijder Extern bureaublad-client voor Windows 7 en Windows 10 en installeer deze opnieuw.

## <a name="web-client-wont-open"></a>WebClient kan niet worden geopend

Test eerst uw Internet verbinding door een andere website in uw browser te openen. bijvoorbeeld [www.Bing.com](https://www.bing.com).

**Nslookup** gebruiken om te controleren of DNS de FQDN kan omzetten:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Probeer verbinding te maken met een andere client, bijvoorbeeld Extern bureaublad-client voor Windows 7 of Windows 10, en controleer of u de webclient kunt openen.

### <a name="opening-another-site-fails"></a>Het openen van een andere site mislukt

Dit wordt meestal veroorzaakt door problemen met de netwerk verbinding of door een netwerk storing. U wordt aangeraden contact op te nemen met netwerk ondersteuning.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup kan de naam niet omzetten

Dit wordt meestal veroorzaakt door problemen met de netwerk verbinding of door een netwerk storing. U wordt aangeraden contact op te nemen met netwerk ondersteuning.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>De client kan geen verbinding maken, maar andere clients in uw netwerk kunnen verbinding maken

Als uw browser wordt gestart of niet meer werkt terwijl u de webclient gebruikt, volgt u deze instructies om het probleem op te lossen:

1. Start de browser opnieuw.
2. Wis browser cookies. Zie [cookie bestanden verwijderen in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Wis de browsercache. Zie [browser cache wissen voor uw browser](https://binged.it/2RKyfdU).
4. Open de browser in de persoonlijke modus.

## <a name="web-client-stops-responding-or-disconnects"></a>Webclient reageert niet meer of de verbinding wordt verbroken

Probeer verbinding te maken met behulp van een andere browser of client.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Andere browsers en clients kunnen ook niet worden geopend

Als er problemen blijven optreden nadat u de browser hebt geswitcheerd, is het probleem mogelijk niet in uw browser, maar met uw netwerk. U wordt aangeraden contact op te nemen met netwerk ondersteuning.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webclient vraagt om referenties

Als de webclient om referenties wordt gevraagd, volgt u deze instructies:

1. Controleer of de URL van de webclient juist is.
2. Controleer of de referenties die u gebruikt voor de Windows Virtual Desktop-omgeving zijn gekoppeld aan de URL.
3. Wis browser cookies. Zie [cookie bestanden verwijderen in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)voor meer informatie.
4. Wis de browsercache. Zie [browser cache voor uw browser wissen](https://binged.it/2RKyfdU)voor meer informatie.
5. Open uw browser in de persoonlijke modus.

## <a name="next-steps"></a>Volgende stappen

- Zie [probleemoplossings overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van het oplossen van problemen met het virtuele bureau blad van Windows en de escalatie trajecten.
- Zie [Tenant en hostgroep maken](troubleshoot-set-up-issues.md)voor informatie over het oplossen van problemen bij het maken van een Tenant en een hostgroep in een virtueel-bureaublad omgeving van Windows.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)om de zelf studie voor problemen oplossen op te lossen.