---
title: Problemen met Extern bureaublad-client Windows Virtual Desktop oplossen - Azure
description: Problemen oplossen wanneer u clientverbindingen instelt in een Windows Virtual Desktop-tenantomgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 595762e6e8f22dddff30f1cff8c4bb79e89624b1
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473847"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Problemen met de Extern bureaublad-client oplossen

In dit artikel worden veelvoorkomende problemen met de Extern bureaublad-client beschreven en hoe u deze oplossen.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Extern bureaublad-client voor Windows 7 of Windows 10 reageert niet meer of kan niet worden geopend

Vanaf versie 1.2.790 u de gebruikersgegevens opnieuw instellen vanaf de pagina Info of met een opdracht.

Gebruik de volgende opdracht om uw gebruikersgegevens te verwijderen, de standaardinstellingen te herstellen en u af te melden voor alle werkruimten.

```cmd
msrdcw.exe /reset [/f]
```

Als u een eerdere versie van de Extern bureaublad-client gebruikt, raden we u aan de client te verwijderen en opnieuw te installeren.

## <a name="web-client-wont-open"></a>Webclient wordt niet geopend

Test eerst uw internetverbinding door een andere website in uw browser te openen; [www.bing.com](https://www.bing.com)bijvoorbeeld.

Gebruik **nslookup** om te bevestigen dat DNS de FQDN kan oplossen:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Probeer verbinding te maken met een andere client, zoals Extern bureaublad-client voor Windows 7 of Windows 10, en controleer of u de webclient openen.

### <a name="opening-another-site-fails"></a>Het openen van een andere site mislukt

Dit wordt meestal veroorzaakt door problemen met de netwerkverbinding of een netwerkstoring. We raden u aan contact op te nemen met netwerkondersteuning.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup kan de naam niet oplossen

Dit wordt meestal veroorzaakt door problemen met de netwerkverbinding of een netwerkstoring. We raden u aan contact op te nemen met netwerkondersteuning.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Uw klant kan geen verbinding maken, maar andere clients in uw netwerk kunnen verbinding maken

Als uw browser begint te werken of niet meer werkt terwijl u de webclient gebruikt, volgt u de volgende instructies om dit op te lossen:

1. Start de browser opnieuw.
2. Browsercookies wissen. Zie [Cookiesbestanden verwijderen in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Wis de browsercache. Zie [duidelijke browsercache voor uw browser.](https://binged.it/2RKyfdU)
4. Open browser in de privémodus.

## <a name="web-client-stops-responding-or-disconnects"></a>Webclient reageert niet meer of verbreekt de verbinding

Probeer verbinding te maken via een andere browser of client.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Andere browsers en clients ook storing of niet te openen

Als er problemen blijven bestaan, zelfs nadat u van browser bent gewisseld, is het probleem mogelijk niet bij uw browser, maar bij uw netwerk. We raden u aan contact op te nemen met netwerkondersteuning.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webclient blijft vragen om referenties

Als de webclient blijft vragen om referenties, volgt u de volgende instructies:

1. Controleer of de URL van de webclient juist is.
2. Controleer of de referenties die u gebruikt, zijn voor de Windows Virtual Desktop-omgeving die is gekoppeld aan de URL.
3. Browsercookies wissen. Zie [Cookiebestanden verwijderen in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)voor meer informatie.
4. Wis de browsercache. Zie [Browsercache voor uw browser wissen voor](https://binged.it/2RKyfdU)meer informatie.
5. Open uw browser in de privémodus.

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van probleemoplossing voor het oplossen van problemen met Windows Virtual Desktop en de escalatietracks.
- Zie [Tenant en hostpool maken](troubleshoot-set-up-issues.md)als u problemen wilt oplossen tijdens het maken van een tenant en hostpool in een Windows Virtual Desktop-omgeving.
- Zie Configuratie van de [virtuele machine sessiehost](troubleshoot-vm-configuration.md)voor het oplossen van problemen tijdens het configureren van een virtuele machine (VM) in Windows Virtual Desktop.
- Zie [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)voor het oplossen van problemen bij het gebruik van PowerShell met Windows Virtual Desktop.
- Zie [Zelfstudie: Problemen met resourcebeheersjabloonimplementaties](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)oplossen.