---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176497"
---
Afhankelijk van het besturingssysteem van de client zijn de procedures om op afstand verbinding te maken met het apparaat verschillend.

### <a name="remotely-connect-from-a-windows-client"></a>Verbinding op afstand maken vanuit een Windows-client

Controleer voordat u begint of uw Windows-client Windows PowerShell 5.0 of hoger draait.

Volg deze stappen om op afstand verbinding te maken vanuit een Windows-client.

1. Voer een Windows PowerShell-sessie uit als beheerder.
2. Controleer of de Windows Remote Management-service op uw client wordt uitgevoerd. Typ het volgende na de opdrachtprompt:

    `winrm quickconfig`

3. Wijs een variabele toe aan het IP-adres van het apparaat.

    $ip = "<device_ip>"

    Vervang `<device_ip>` door het IP-adres van uw apparaat.

4. Als u het IP-adres van uw apparaat wilt toevoegen aan de lijst met vertrouwde hosts van de client, typt u de volgende opdracht:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Start een Windows PowerShell-sessie op het apparaat:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Geef het wachtwoord op wanneer daarom wordt gevraagd. Gebruik hetzelfde wachtwoord dat wordt gebruikt om u aan te melden bij de lokale webgebruikersinterface. Het standaard wachtwoord voor de lokale web-gebruikersinterface is *Wachtwoord1*. Wanneer u met externe PowerShell verbinding maakt met het apparaat, ziet u de volgende voorbeelduitvoer:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Verbinding op afstand maken vanaf een Linux-client

Op de Linux-client die u gebruikt om verbinding te maken:

- [Installeer de nieuwste PowerShell Core voor Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) van GitHub om de SSH remoting-functie te krijgen. 
- [Installeer alleen `gss-ntlmssp` het pakket van de NTLM-module.](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md) Voor Ubuntu-clients gebruikt u de volgende opdracht:
    - `sudo apt-get install gss-ntlmssp`

Ga voor meer informatie naar [PowerShell remoting over SSH.](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6)

Volg deze stappen om op afstand verbinding te maken vanuit een NFS-client.

1. Als u de PowerShell-sessie wilt openen, typt u het:

    `sudo pwsh`
 
2. Typ:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Geef het wachtwoord op dat wordt gebruikt om u aan te melden bij uw apparaat wanneer u daarom wordt gevraagd.
 
> [!NOTE]
> Deze procedure werkt niet op Mac OS.
