---
title: Windows 7 virtuele machines Windows virtueel bureau blad (klassiek)-Azure
description: Problemen oplossen voor virtuele Windows 7-machines (Vm's) in een Windows-omgeving (virtueel bureau blad).
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e7f433668c34fb5edc35889adcd604023202ada4
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286339"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop-classic"></a>Problemen oplossen met virtuele machines van Windows 7 in het virtuele bureau blad van Windows (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op het virtuele bureau blad van Windows (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager virtueel-bureaublad objecten van Windows.

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