---
title: Microsoft Teams op Windows Virtual Desktop - Azure
description: Microsoft Teams gebruiken op Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 702df1bfaf0701a40e514f8a47151f96917adf38
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811598"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Microsoft Teams gebruiken op Windows Virtual-bureaublad

> Van toepassing op: Windows 10 en Windows 10 IoT Enterprise

Gevirtualiseerde omgevingen bieden een unieke reeks uitdagingen voor samenwerkings-apps zoals Microsoft Teams, waaronder verhoogde latentie, hoog gebruik van host-CPU's en slechte algehele audio- en videoprestaties. Ga voor meer informatie naar Teams voor [gevirtualiseerde desktopinfrastructuur voor](https://docs.microsoft.com/microsoftteams/teams-for-vdi)meer informatie over het gebruik van Microsoft Teams in VDI-omgevingen.

## <a name="prerequisites"></a>Vereisten

Voordat u Microsoft Teams op Windows Virtual Desktop gebruiken, moet u de volgende dingen doen:

- Installeer [Windows Desktop-client](connect-windows-7-and-10.md) op een Windows 10-apparaat dat voldoet aan de [hardwarevereisten van](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)Microsoft Teams.
- Maak verbinding met een Windows 10 Multi-session of Windows 10 Enterprise virtuele machine (VM).
- [Bereid uw netwerk](https://docs.microsoft.com/microsoftteams/prepare-network) voor op Microsoft Teams.

## <a name="use-unoptimized-microsoft-teams"></a>Niet-geoptimaliseerde Microsoft Teams gebruiken

U niet-geoptimaliseerde Microsoft Teams in uw Windows Virtual Desktop-omgevingen gebruiken om gebruik te maken van de volledige chat- en samenwerkingsfuncties van Microsoft Teams en audiobellen. De audiokwaliteit in gesprekken varieert afhankelijk van uw hostconfiguratie, omdat niet-geoptimaliseerde oproepen meer van uw host-CPU gebruiken.

### <a name="prepare-your-image-for-teams"></a>Uw afbeelding voorbereiden op Teams

Als u de installatie van Teams per machine wilt inschakelen, stelt u de volgende registersleutel in op de host:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 0x1
```

### <a name="install-microsoft-teams"></a>Microsoft Teams installeren

U de Bureaublad-app Teams implementeren met een installatie per machine. Ga als verkenner met het installeren van Microsoft Teams in uw Windows Virtual Desktop-omgeving:

1. Download het [Teams MSI-pakket](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) dat bij uw omgeving past. We raden u aan de 64-bits installer te gebruiken op een 64-bits besturingssysteem.
2. Voer deze opdracht uit om de MSI op de host-VM te installeren.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      Hiermee worden Teams geïnstalleerd op programbestanden of programmabestanden (x86). De volgende keer dat u zich aanmeldt en Teams start, vraagt de app om uw referenties.

      > [!NOTE]
      > Gebruikers en beheerders kunnen de automatische lancering voor Teams tijdens het aanmelden op dit moment niet uitschakelen.

      Voer de opdracht uit om de MSI van de host-VM te verwijderen:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Als u Teams installeert met de MSI-instelling ALLUSERS=1, worden automatische updates uitgeschakeld. We raden je aan om Teams minstens één keer per maand bij te werken.
