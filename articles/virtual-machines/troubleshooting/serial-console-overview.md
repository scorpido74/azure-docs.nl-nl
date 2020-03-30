---
title: Azure Serial Console | Microsoft Documenten
description: Met de Azure Serial Console u verbinding maken met uw VM wanneer SSH of RDP niet beschikbaar zijn.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 779bb88d15ea6c52f4399f17223b89916e22653d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267012"
---
# <a name="azure-serial-console"></a>Azure Serial Console

De seriële console in de Azure-portal biedt toegang tot een op tekst gebaseerde console voor virtuele machines (VM's) en exemplaren met een virtuele machineschaal met Linux of Windows. Deze seriële verbinding maakt verbinding met de ttyS0- of COM1-seriële poort van de vm- of virtuele machineschaalsetsetinstantie, die toegang biedt onafhankelijk van de status van het netwerk of het besturingssysteem. De seriële console is alleen toegankelijk via de Azure-portal en is alleen toegestaan voor gebruikers die een toegangsrol van inzender of hoger hebben tot de VM- of virtuele machineschaalset.

Serial Console werkt op dezelfde manier voor VM's en virtuele machineschaalset-instanties. In dit document bevatten alle vermeldingen aan VM's impliciet exemplaren van virtuele machineschaalsets, tenzij anders vermeld.

> [!NOTE]
> De seriële console is algemeen beschikbaar in algemene Azure-regio's en in openbare preview in Azure Government. Het is nog niet beschikbaar in de Azure China-cloud.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Vereisten voor toegang tot de Azure Serial Console
Als u toegang wilt krijgen tot de seriële console op uw vm- of virtuele machineschaalset-instantie, hebt u het volgende nodig:

- Opstartdiagnostiek moet zijn ingeschakeld voor de VM
- Een gebruikersaccount dat wachtwoordverificatie gebruikt, moet binnen de VM bestaan. U een op een wachtwoord gebaseerde gebruiker maken met de [wachtwoordfunctie voor opnieuw instellen](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) van de VM-toegangsextensie. Selecteer **Wachtwoord opnieuw instellen** in de sectie Ondersteuning + **probleemoplossing.**
- Het Azure-account dat toegang krijgt tot serial console moet een rol in [de virtuele machineinzender](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) hebben voor zowel de VM als het opslagaccount [voor opstartdiagnostiek](boot-diagnostics.md)

> [!NOTE]
> Klassieke implementaties worden niet ondersteund. Uw exemplaar van de vm- of virtuele machineschaalset moet het implementatiemodel azure resource beheer gebruiken.

## <a name="get-started-with-the-serial-console"></a>Aan de slag met de seriële console
De seriële console voor VM's en de schade machineschaalset is alleen toegankelijk via de Azure-portal:

### <a name="serial-console-for-virtual-machines"></a>Seriële console voor virtuele machines
Seriële console voor VM's is net zo eenvoudig als klikken op **seriële console** in de sectie **Ondersteuning + probleemoplossing** in de Azure-portal.
  1. Open de [Azure Portal](https://portal.azure.com).

  1. Navigeer naar **Alle bronnen** en selecteer een virtuele machine. De overzichtspagina voor de VM wordt geopend.

  1. Schuif omlaag naar de sectie **Ondersteuning + probleemoplossing** en selecteer **Seriële console**. Een nieuw deelvenster met de seriële console opent en start de verbinding.

     ![Venster Linux Serial Console](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Seriële console voor virtuele machineschaalsets
Serial Console is beschikbaar voor virtuele machineschaalsets, toegankelijk voor elke instantie binnen de schaalset. U moet naar het afzonderlijke exemplaar van een virtuele machineschaal instellen voordat u de **knop Seriële console** ziet. Als uw virtuele machineschaalset geen opstartdiagnose heeft ingeschakeld, moet u ervoor zorgen dat u uw model voor de opstartschaal model bijwerkt om opstartdiagnoses in te schakelen en vervolgens alle exemplaren upgraden naar het nieuwe model om toegang te krijgen tot seriële console.
  1. Open de [Azure Portal](https://portal.azure.com).

  1. Navigeer naar **Alle bronnen** en selecteer een virtuele machineschaalset. De overzichtspagina voor de virtuele machineschaalset wordt geopend.

  1. Navigeren naar **instanties**

  1. Een instantie voor een virtuele machineschaalset selecteren

  1. Selecteer **Seriële console**in de sectie **Ondersteuning + probleemoplossing** . Een nieuw deelvenster met de seriële console opent en start de verbinding.

     ![Linux virtuele machine schaal set Serial Console](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>Geavanceerd gebruik voor seriële console
Naast consoletoegang tot uw vm, u de Azure Serial Console ook gebruiken voor het volgende:
* Een [opdracht voor systeemaanvragen naar uw vm verzenden](./serial-console-nmi-sysrq.md)
* Een [niet-maskerbare onderbreking naar uw VM verzenden](./serial-console-nmi-sysrq.md)
* Gracieus [rebooten of krachtig krachtig power-cycling uw VM](./serial-console-power-options.md)


## <a name="next-steps"></a>Volgende stappen
Aanvullende documentatie over seriële console is beschikbaar in de zijbalk.
- Meer informatie is beschikbaar voor [Serial Console voor Linux VM's.](./serial-console-linux.md)
- Meer informatie is beschikbaar voor [Serial Console voor Windows VM's.](./serial-console-windows.md)
