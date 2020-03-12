---
title: Windows 7 virtuele machines Windows virtueel bureau blad-Azure
description: Problemen oplossen voor virtuele Windows 7-machines (Vm's) in een virtueel-bureaublad omgeving van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a2ff3f6fa9896e45ecd6ab40d40d46a046edf1cb
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127384"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Problemen met virtuele Windows 7-machines in Windows Virtual Desktop oplossen

Gebruik dit artikel voor het oplossen van problemen die zich voordoen bij het configureren van de virtuele machines (Vm's) voor virtuele bureau blad-sessies van Windows.

## <a name="known-issues"></a>Bekende problemen

Windows 7 op virtuele Windows-Bureau bladen biedt geen ondersteuning voor de volgende functies:

- Gevirtualiseerde toepassingen (RemoteApps)
- Omleiding van tijd zone
- Automatische DPI-schaling

Virtueel bureau blad van Windows kan alleen volledige Bureau bladen voor Windows 7 virtualiseren.

Automatische DPI-schaling wordt niet ondersteund. u kunt de resolutie op uw virtuele machine hand matig wijzigen door met de rechter muisknop op het pictogram in de Extern bureaublad-client te klikken en **oplossing**te selecteren.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Fout: geen toegang tot de Extern bureaublad gebruikers groep

Als Windows virtueel bureau blad u of de referenties van uw gebruikers in de Extern bureaublad gebruikers groep niet kan vinden, ziet u mogelijk een van de volgende fout berichten:

- "Deze gebruiker is geen lid van de Extern bureaublad gebruikers groep"
- "U moet zijn gemachtigd om u aan te melden via Extern bureaublad-services"

U kunt deze fout oplossen door de gebruiker toe te voegen aan de Extern bureaublad-gebruikers groep:

1. Open Azure Portal.
2. Selecteer de virtuele machine waarop u het fout bericht hebt gezien.
3. Selecteer **een opdracht uitvoeren**.
4. Voer de volgende opdracht uit met `<username>` vervangen door de naam van de gebruiker die u wilt toevoegen:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```