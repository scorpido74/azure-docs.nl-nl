---
title: De Extern bureaublad-licentieserver is niet beschikbaar wanneer u verbinding maakt met een Azure VM | Microsoft Documenten
description: Meer informatie over het oplossen van problemen met RDP-failproblemen omdat er geen Extern bureaublad-licentieserver beschikbaar is | Microsoft Documenten
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 154160f9a3fbd485ee6383bf3d5ff1c291520a75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71088524"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Extern bureaublad-licentieserver is niet beschikbaar wanneer u verbinding maakt met een Azure VM

In dit artikel u het probleem oplossen wanneer u geen verbinding maken met een virtuele Azure-machine (VM), omdat er geen Extern bureaublad-licentieserver beschikbaar is om een licentie te verlenen.

## <a name="symptoms"></a>Symptomen

Wanneer u verbinding probeert te maken met een virtuele machine (VM), ervaart u de volgende scenario's:

- De VM screenshot laat zien dat het besturingssysteem volledig is geladen en wachten op referenties.
- U ontvangt de volgende foutmeldingen wanneer u een RDP-verbinding (Microsoft Remote Desktop Protocol) probeert te maken:

  - De externe sessie is verbroken omdat er geen Extern bureaublad-licentieservers beschikbaar zijn om een licentie te verstrekken.

  - Er is geen Extern bureaublad-licentieserver beschikbaar. Extern bureaublad-services werkt niet meer omdat deze computer de respijtperiode heeft doorstaan en geen contact heeft opgenomen met ten minste een geldige Windows Server 2008-licentieserver. Selecteer dit bericht om de configuratie van de Extern bureaublad-sessiehostserver te openen om licentiediagnose te gebruiken.

U echter normaal gesproken verbinding maken met de virtuele machine met behulp van een administratieve sessie:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Oorzaak

Dit probleem treedt op als een Extern bureaublad-licentieserver niet beschikbaar is om een licentie te verlenen om een externe sessie te starten. Dit kan worden veroorzaakt door verschillende scenario's, ook al is er een functie extern bureaublad-sessiehost ingesteld op de VM:

- Er was nooit een Remote Desktop licentierol in het milieu, en de respijtperiode, 180 dagen, is voorbij.
- Er is een Extern bureaublad-licentie in de omgeving geïnstalleerd, maar deze is nooit geactiveerd.
- Een Extern bureaublad-licentie in de omgeving heeft geen CLIENT Access-licenties (CAL's) geïnjecteerd om de verbinding in te stellen.
- Er is een Extern bureaublad-licentie in de omgeving geïnstalleerd. Er zijn beschikbare CAL's, maar ze zijn niet goed geconfigureerd.
- Een Extern bureaublad-licentie heeft CAL's en is geactiveerd. Sommige andere problemen op de Extern bureaublad-licentieserver voorkomen echter dat deze licenties in de omgeving kunnen verstrekken.

## <a name="solution"></a>Oplossing

Als u dit probleem wilt oplossen, [maakt u een back-up van de osschijf](../windows/snapshot-copy-managed-disk.md) en voert u de volgende stappen uit:

1. Maak verbinding met de VM met behulp van een administratieve sessie:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    Als u geen verbinding maken met de VM met behulp van een beheersessie, u de [virtuele machineseriële console op Azure](serial-console-windows.md) gebruiken om als volgt toegang te krijgen tot de VM:

    1. Toegang tot de seriële console door **ondersteuning & seriële** > **console (Preview)** te selecteren. Als de functie is ingeschakeld op de VM, u de vm met succes verbinden.

    2. Maak een nieuw kanaal voor een CMD-instantie. Voer **CMD** in om het kanaal te starten en de kanaalnaam op te halen.

    3. Schakel over naar het kanaal waarop de CMD-instantie wordt uitgevoerd. In dit geval moet het kanaal 1 zijn:

       ```
       ch -si 1
       ```

    4. Selecteer **Opnieuw invoeren** en voer een geldige gebruikersnaam en wachtwoord, lokale of domein-id, in voor de VM.

2. Controleer of de VM een functie Extern bureaublad-sessiehost heeft ingeschakeld. Als de rol is ingeschakeld, moet u ervoor zorgen dat deze naar behoren functioneert. Open een verhoogde CMD-instantie en volg de volgende stappen:

    1. Gebruik de volgende opdracht om de status van de functie Extern bureaublad-sessiehost te controleren:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Als deze opdracht een waarde van 0 retourneert, betekent dit dat de rol is uitgeschakeld en u naar stap 3 gaan.

    2. Gebruik de volgende opdracht om het beleid te controleren en indien nodig opnieuw te configureren:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        Als de waarde **LicensingMode** is ingesteld op een andere waarde dan 4, stelt u de waarde in op 4:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Als de waarde **SpecifiedLicenseServers** niet bestaat of als er onjuiste licentieservergegevens zijn, wijzigt u deze als volgt:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Nadat u wijzigingen in het register hebt aangebracht, start u de vm opnieuw.

    4. Als u geen CAL's hebt, verwijdert u de rol Extern bureaublad-sessiehost. Dan wordt de RDP weer normaal. Het staat slechts twee gelijktijdige RDP-verbindingen met de VM toe:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        Als de VM de licentierol Extern bureaublad heeft en deze niet wordt gebruikt, u die rol ook verwijderen:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. Controleer of de VM verbinding kan maken met de Extern bureaublad-licentieserver. U de verbinding met poort 135 tussen de VM en de licentieserver testen: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Als er geen Extern bureaublad-licentieserver in de omgeving is en u die wilt, u [een functieservice voor extern bureaublad-licenties installeren.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)) [Configureer vervolgens de RDS-licenties](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Als een Extern bureaublad-licentieserver is geconfigureerd en in orde is, controleert u of de Extern bureaublad-licentieserver is geactiveerd met CAL's.

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u nog steeds hulp nodig hebt, [neemt u contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om het probleem op te lossen.
