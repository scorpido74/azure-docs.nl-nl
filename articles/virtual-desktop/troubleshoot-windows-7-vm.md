---
title: Problemen met virtuele Windows 7-machines in virtueel bureau blad van Windows oplossen-Azure
description: Problemen oplossen voor virtuele Windows 7-machines (Vm's) in een virtueel-bureaublad omgeving van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
ms.openlocfilehash: 7fdb46ac7cffc4eb6a791304c7a6b58378806296
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679891"
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

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Fout: Geen toegang tot de Extern bureaublad gebruikers groep

Als Windows virtueel bureau blad u of de referenties van uw gebruikers in de Extern bureaublad gebruikers groep niet kan vinden, ziet u mogelijk een van de volgende fout berichten:

- "Deze gebruiker is geen lid van de Extern bureaublad gebruikers groep"
- "U moet zijn gemachtigd om u aan te melden via Extern bureaublad-services"

U kunt deze fout oplossen door de gebruiker toe te voegen aan de Extern bureaublad-gebruikers groep:

1. Open Azure Portal.
2. Selecteer de virtuele machine waarop u het fout bericht hebt gezien.
3. Selecteer **een opdracht uitvoeren**.
4. Voer de volgende opdracht uit met `<username>`, vervangen door de naam van de gebruiker die u wilt toevoegen:
   
   ```cmd
   localgroup "Remote Desktop Users" <username> /add
   ```