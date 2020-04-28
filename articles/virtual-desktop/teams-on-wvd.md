---
title: Micro soft teams op virtueel bureau blad van Windows-Azure
description: Micro soft teams gebruiken op het virtuele bureau blad van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a860b005457c6e02187423a3ffbbc63fe7c758b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187525"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Micro soft teams gebruiken op het virtuele bureau blad van Windows

> Van toepassing op: Windows 10 en Windows 10 IoT Enter prise

Gevirtualiseerde omgevingen bieden een unieke reeks uitdagingen voor samenwerkings-apps, zoals micro soft-teams, met verhoogde latentie, intensief CPU-gebruik van de host en slechte algehele audio-en video prestaties. Voor meer informatie over het gebruik van micro soft-teams in VDI-omgevingen raadpleegt u [teams voor gevirtualiseerde desktop infrastructuur](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Vereisten

Voordat u micro soft teams kunt gebruiken op het virtuele bureau blad van Windows, moet u de volgende dingen doen:

- Installeer de [Windows desktop-client](connect-windows-7-and-10.md) op een Windows 10-apparaat dat voldoet aan de [hardwarevereisten](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)voor micro soft-teams.
- Verbinding maken met een Windows 10-of Windows 10 Enter prise virtual machine (VM).
- [Bereid uw netwerk](https://docs.microsoft.com/microsoftteams/prepare-network) voor op micro soft teams.

## <a name="use-unoptimized-microsoft-teams"></a>Niet-geoptimaliseerde micro soft-teams gebruiken

U kunt micro soft-teams gebruiken in uw Windows virtual desktop-omgevingen om gebruik te maken van de functies voor chatten en samen werken met micro soft teams. Windows Virtual Desktop biedt geen ondersteuning voor teams op VDI audio/video-optimalisaties (AV). Aanroepen en vergaderingen worden niet ondersteund. Als het beleid van uw organisatie toestaat, kunt u nog steeds audio gesprekken voeren en vergaderingen met audio doen, maar niet-geoptimaliseerde audio kwaliteit in aanroepen is afhankelijk van de configuratie van uw host en is mogelijk niet betrouwbaar. Voor meer informatie raadpleegt u [teams over VDI-prestatie overwegingen](https://docs.microsoft.com/microsoftteams/teams-for-vdi#teams-on-vdi-performance-considerations).

### <a name="prepare-your-image-for-teams"></a>Uw installatie kopie voorbereiden voor teams

Als u teams per computer wilt installeren, stelt u de volgende register sleutel op de host in:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Micro soft teams installeren

U kunt de teams bureau blad-app implementeren met behulp van een installatie per computer. Micro soft teams installeren in uw virtueel-bureaublad omgeving van Windows:

1. Down load het [MSI-pakket voor teams](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) dat overeenkomt met uw omgeving. Het is raadzaam om het 64-bits installatie programma te gebruiken op een 64-bits besturings systeem.
2. Voer deze opdracht uit om de MSI te installeren op de host-VM.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      Hiermee worden teams op programma bestanden of programma bestanden (x86) geïnstalleerd. De volgende keer dat u zich aanmeldt en teams start, wordt de app gevraagd om uw referenties op te vragen.

      > [!NOTE]
      > Gebruikers en beheerders kunnen de functie voor het automatisch starten van teams tijdens het aanmelden op dit moment niet uitschakelen.

      Als u de MSI wilt verwijderen van de host-VM, voert u de volgende opdracht uit:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Als u teams met de MSI-instelling ALLUSER = 1 installeert, worden automatische updates uitgeschakeld. Het is raadzaam om teams ten minste één keer per maand bij te werken. Voor meer informatie over het implementeren van de bureau blad-app teams, raadpleegt [u de bureau blad teams implementeren op de VM](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Remote Desktop Protocol eigenschappen voor een hostgroep aanpassen
Het aanpassen van de eigenschappen van de Remote Desktop Protocol (RDP) van een hostgroep, zoals de ervaring op meerdere monitors, waarmee u een optimale ervaring kunt bieden aan uw gebruikers op basis van hun behoeften. U kunt RDP-eigenschappen in virtueel bureau blad van Windows aanpassen met behulp van de para meter **-CustomRdpProperty** in de cmdlet **set-RdsHostPool** .
Zie [ondersteunde RDP-Bestands instellingen](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) voor een volledige lijst met ondersteunde eigenschappen en hun standaard waarden.
