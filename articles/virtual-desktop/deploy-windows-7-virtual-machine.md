---
title: Windows 7 virtuele machine Windows Virtual Desktop implementeren - Azure
description: Een virtuele windows 7-machine configureren en implementeren op Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158dd9d6a38cc1aaf7667c0b16518e23b3f7cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366680"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Een virtuele Windows 7-machine implementeren in Windows Virtual Desktop

Het proces voor het implementeren van een Virtuele Windows 7-machine (VM) op Windows Virtual Desktop is iets anders dan voor VM's met latere versies van Windows. In deze handleiding vindt u het implementeren van Windows 7.

## <a name="prerequisites"></a>Vereisten

Volg voordat u begint de instructies in [Een hostgroep maken met PowerShell](create-host-pools-powershell.md) om een hostpool te maken. Volg daarna de instructies in [Hostpools maken in Azure Marketplace](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) om een of meer gebruikers toe te wijzen aan de bureaubladtoepassingsgroep.

## <a name="configure-a-windows-7-virtual-machine"></a>Een virtuele windows 7-machine configureren

Zodra u de vereisten hebt uitgevoerd, u uw Windows 7 VM configureren voor implementatie op Windows Virtual Desktop.

Ga als lid van de Browser naar het instellen van een Windows 7-vm op Windows Virtual Desktop:

1. Meld u aan bij de Azure-portal en zoek naar de Windows 7 Enterprise-afbeelding of upload uw eigen aangepaste Windows 7 Enterprise-afbeelding (x64).  
2. Implementeer een of meerdere virtuele machines met Windows 7 Enterprise als host-besturingssysteem. Zorg ervoor dat de virtuele machines Extern bureaublad-protocol (RDP) (de TCP/3389-poort) toestaan.
3. Maak verbinding met de Windows 7 Enterprise-host via de RDP en verifieer met de referenties die u hebt gedefinieerd tijdens het configureren van uw implementatie. 
4. Voeg het account dat u hebt gebruikt tijdens een verbinding met de host met RDP toe aan de groep 'Extern bureaublad-gebruiker'. Als u dit niet doet, u mogelijk geen verbinding maken met de virtuele machine nadat u lid bent geworden van uw Active Directory-domein.
5. Ga naar Windows Update op uw vm.
6. Installeer alle Windows-updates in de categorie Belangrijk.
7. Installeer alle Windows-updates in de categorie Optioneel (met uitzondering van taalpakketten). Hiermee wordt de Remote Desktop Protocol 8.0-update[(KB2592687)](https://www.microsoft.com/download/details.aspx?id=35387)geïnstalleerd die u nodig hebt om deze instructies in te vullen.
8. Open de externe groepsbeleidseditor en navigeer naar > **computerconfiguratiebeheersjablonen** > **Windows Components** > **Remote Desktop Services** > **Remote Desktop Session Host** > **Remote Session Environment**. **Computer Configuration**
9. Schakel het beleid extern bureaublad-protocol 8.0 in.
10. Sluit je aan bij deze VM in uw Active Directory-domein.
11. Start de virtuele machine opnieuw op door de volgende opdracht uit te voeren:
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. Volg [hier](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) de instructies om een registratietoken te krijgen.
13. [Download de Windows Virtual Desktop Agent voor Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Download de Windows Virtual Desktop Agent Manager voor Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Open het installatieprogramma van Windows Virtual Desktop Agent en volg de instructies. Geef de registratiesleutel die u in stap 12 hebt gemaakt, op wanneer u daarom wordt gevraagd.
16. Open het installatieprogramma van Windows Virtual Desktop en volg de instructies.
17. Blokkeer eventueel de TCP/3389-poort om directe toegang tot extern bureaublad-protocol tot de VM te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Uw Windows Virtual Desktop-implementatie is nu klaar voor gebruik. [Download de nieuwste versie van de Windows Virtual Desktop-client](https://aka.ms/wvd/clients/windows) om aan de slag te gaan.

Zie ons artikel over probleemoplossing bij [Problemen met virtuele apparaten van Windows 7 in Windows Virtual Desktop](troubleshoot-windows-7-vm.md)voor een lijst met bekende problemen en instructies voor het oplossen van problemen voor Windows 7.
