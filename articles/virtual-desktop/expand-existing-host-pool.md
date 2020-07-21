---
title: Bestaande hostgroep uitbreiden met nieuwe sessie hosts lente 2020-Azure
description: Een bestaande hostgroep uitbreiden met nieuwe sessie-hosts in virtueel bureau blad van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b23cee72af7f8262fdd552c2d4c26c608b68a1e4
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527605"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Een bestaande hostgroep uitbreiden met nieuwe sessie-hosts

>[!IMPORTANT]
>Deze inhoud is van toepassing op de update uit het voorjaar van 2020 met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md) als u de release van Windows Virtual Desktop uit het najaar van 2019 zonder Azure Resource Manager-objecten gebruikt.
>
> De update van Windows Virtual Desktop uit het voorjaar van 2020 is momenteel beschikbaar als openbare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Wanneer u het gebruik in uw hostgroep wilt verbeteren, moet u mogelijk uw bestaande hostgroep uitbreiden met nieuwe sessie-hosts om de nieuwe belasting af te handelen.

In dit artikel wordt uitgelegd hoe u een bestaande hostgroep kunt uitbreiden met nieuwe sessie-hosts.

## <a name="what-you-need-to-expand-the-host-pool"></a>Wat u nodig hebt om de hostgroep uit te breiden

Voordat u begint, moet u ervoor zorgen dat u een hostgroep en virtuele machines (Vm's) voor de host hebt gemaakt met behulp van een van de volgende methoden:

- [Azure-portal](./create-host-pools-azure-marketplace.md)
- [Een hostpool maken met PowerShell](./create-host-pools-powershell.md)

U hebt ook de volgende informatie nodig bij het maken van de hostgroep en de Vm's voor de sessiehost:

- VM-grootte, installatie kopie en naam voorvoegsel
- Referenties voor domein toevoegen aan beheerder
- Naam van het virtuele netwerk en het subnet

## <a name="add-virtual-machines-with-the-azure-portal"></a>Virtuele machines met de Azure Portal toevoegen

U kunt als volgt uw hostgroep uitbreiden door virtuele machines toe te voegen:

1. Meld u aan bij Azure Portal.

2. Zoek en selecteer **Windows virtueel bureau blad**.

3. Selecteer in het menu aan de linkerkant van het scherm de optie **hostgroepen**en selecteer vervolgens de naam van de hostgroep waaraan u virtuele machines wilt toevoegen.

4. Selecteer **sessie-hosts** in het menu aan de linkerkant van het scherm.

5. Selecteer **+ toevoegen** om te beginnen met het maken van de hostgroep.

6. Negeer het tabblad basis informatie en selecteer in plaats daarvan het tabblad **VM-Details** . Hier kunt u de details weer geven en bewerken van de virtuele machine (VM) die u wilt toevoegen aan de hostgroep.

7. Selecteer de resource groep waarvoor u de virtuele machines wilt maken en selecteer vervolgens de regio. U kunt kiezen welke huidige regio u gebruikt of een nieuwe regio.

8. Voer het aantal sessie-hosts in dat u wilt toevoegen aan uw hostgroep in **aantal vm's**. Als u uw hostgroep bijvoorbeeld met vijf hosts wilt uitbreiden, voert u **5**in.

    >[!NOTE]
    >U kunt de grootte of afbeelding van de Vm's niet bewerken omdat het belang rijk is om ervoor te zorgen dat alle virtuele machines in de hostgroep dezelfde grootte hebben.

9. Selecteer voor de gegevens van het **virtuele netwerk**het virtuele netwerk en het subnet waaraan u de virtuele machines wilt koppelen. U kunt hetzelfde virtuele netwerk selecteren als de bestaande machines die momenteel worden gebruikt, of een andere naam kiezen die geschikter is voor de regio die u in stap 7 hebt geselecteerd.

10. Voer voor het **beheerders account**de Active Directory domein gebruikers naam en het wacht woord in die zijn gekoppeld aan het virtuele netwerk dat u hebt geselecteerd. Deze referenties worden gebruikt om de virtuele machines lid te maken van het virtuele netwerk.

      >[!NOTE]
      >Zorg ervoor dat de namen van uw beheerders overeenkomen met de informatie die hier wordt gegeven. En dat er geen MFA is ingeschakeld voor het account.

11. Selecteer het tabblad **tag** als u labels hebt waarop u de virtuele machines wilt groeperen. Als dat niet het geval is, slaat u dit tabblad over.

12. Selecteer het tabblad **controleren + maken** . Controleer uw keuzes en klik op **maken**als alles er goed uitziet.

## <a name="next-steps"></a>Volgende stappen

Nu u uw bestaande hostgroep hebt uitgebreid, kunt u zich aanmelden bij een virtueel-bureaubladclient van Windows om ze te testen als onderdeel van een gebruikers sessie. U kunt verbinding maken met een sessie met een van de volgende clients:

- [Verbinding maken met de Windows Desktop-client](./connect-windows-7-10.md)
- [Verbinding maken met de webclient](./connect-web.md)
- [Verbinding maken met de Android-client](./connect-android.md)
- [Verbinding maken met de macOS-client](./connect-macos.md)
- [Verbinding maken met de iOS-client](./connect-ios.md)
