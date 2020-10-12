---
title: Azure seriële console | Microsoft Docs
description: Met de seriële console van Azure kunt u verbinding maken met uw virtuele machine wanneer SSH of RDP niet beschikbaar is.
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
ms.openlocfilehash: 28c5a3085d84b25deb7c5ee09a9c9cc4d7a06819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87374062"
---
# <a name="azure-serial-console"></a>Azure-seriële console

De seriële console in de Azure Portal biedt toegang tot een op tekst gebaseerde console voor virtuele machines (Vm's) en installatie kopieën van virtuele-machine schaal sets met Linux of Windows. Deze seriële verbinding maakt verbinding met de seriële ttyS0-of COM1-poort van de virtuele machine of het exemplaar van de VM-schaalset, waardoor toegang onafhankelijk is van de status van het netwerk of het besturings systeem. De seriële console is alleen toegankelijk via de Azure Portal en is alleen toegestaan voor gebruikers met een toegangs rol van Inzender of een hoger niveau voor virtuele-machine schaal sets.

Seriële console werkt op dezelfde manier voor Vm's en exemplaren van virtuele-machine schaal sets. In dit document bevatten alle vermeldingen aan Vm's impliciet instanties voor schaal sets voor virtuele machines, tenzij anders vermeld.

Seriële console is algemeen beschikbaar in de wereld wijde Azure-regio's en in de open bare preview-versie van Azure Government. Het is nog niet beschikbaar in de Azure China-Cloud.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Vereisten voor toegang tot de Azure-seriële console
U hebt het volgende nodig om toegang te krijgen tot de seriële console op uw VM of virtuele-machine Scale set-instantie:

- Diagnostische gegevens over opstarten moeten zijn ingeschakeld voor de virtuele machine
- Er moet een gebruikers account zijn dat gebruikmaakt van wachtwoord verificatie in de virtuele machine. U kunt een gebruiker op basis van een wacht woord maken met de functie [wacht woord opnieuw instellen](../extensions/vmaccess.md#reset-password) van de extensie VM-toegang. Selecteer **wacht woord opnieuw instellen** in het gedeelte **ondersteuning en probleem oplossing** .
- Het Azure-account voor toegang tot de seriële console moet de [rol Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) hebben voor zowel de VM als het opslag account voor [Diagnostische gegevens over opstarten](boot-diagnostics.md)
- Klassieke implementaties worden niet ondersteund. De virtuele machine of het exemplaar van de VM-schaalset moet gebruikmaken van het Azure Resource Manager-implementatie model.

> [!NOTE]
> Seriële console is momenteel niet compatibel met een beheerd opslag account voor diagnostische gegevens over opstarten. Als u een seriële console wilt gebruiken, moet u ervoor zorgen dat u een aangepast opslag account gebruikt.

## <a name="get-started-with-the-serial-console"></a>Aan de slag met de seriële console
De seriële console voor Vm's en schaal sets voor virtuele machines is alleen toegankelijk via de Azure Portal:

### <a name="serial-console-for-virtual-machines"></a>Seriële console voor Virtual Machines
Seriële console voor Vm's is net zo eenvoudig als klikken op **seriële console** in de sectie **ondersteuning en probleem oplossing** in de Azure Portal.
  1. Open [Azure Portal](https://portal.azure.com).

  1. Ga naar **alle resources** en selecteer een virtuele machine. De overzichts pagina voor de VM wordt geopend.

  1. Schuif omlaag naar de sectie **ondersteuning en probleem oplossing** en selecteer **seriële console**. Er wordt een nieuw deel venster met de seriële console geopend en de verbinding wordt gestart.

     ![Venster Linux-seriële console](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Seriële console voor Virtual Machine Scale Sets
Seriële console is beschikbaar voor schaal sets voor virtuele machines, die toegankelijk zijn voor elk exemplaar in de schaalset. U moet naar de afzonderlijke instantie van een schaalset voor virtuele machines gaan voordat u de knop **seriële console** ziet. Als voor de virtuele-machine schaal sets geen diagnostische gegevens over opstarten zijn ingeschakeld, moet u het model voor de schaalset van de virtuele machine bijwerken om diagnostische gegevens over opstarten in te scha kelen en vervolgens alle exemplaren naar het nieuwe model bijwerken om toegang te krijgen tot de seriële console.
  1. Open [Azure Portal](https://portal.azure.com).

  1. Ga naar **alle resources** en selecteer een schaalset voor virtuele machines. De overzichts pagina voor de schaalset voor virtuele machines wordt geopend.

  1. Naar **instanties** navigeren

  1. Een instantie van een schaalset voor virtuele machines selecteren

  1. Selecteer **seriële console**in het gedeelte **ondersteuning en probleem oplossing** . Er wordt een nieuw deel venster met de seriële console geopend en de verbinding wordt gestart.

     ![Seriële console voor virtuele Linux-machine schaal sets](./media/virtual-machines-serial-console/vmss-start-console.gif)


### <a name="tls-12-in-serial-console"></a>TLS 1,2 in seriële console
Seriële console maakt gebruik van TLS 1,2 end-to-end om alle communicatie binnen de service te beveiligen. Seriële console heeft een afhankelijkheid van een door de gebruiker beheerde diagnose voor diagnostische gegevens over opstarten en TLS 1,2 moet afzonderlijk worden geconfigureerd voor het opslag account. Instructies hiervoor vindt u [hier](../../storage/common/transport-layer-security-configure-minimum-version.md).

## <a name="advanced-uses-for-serial-console"></a>Geavanceerd gebruik van seriële console
Afgezien van de toegang tot de console tot uw virtuele machine, kunt u ook de Azure Serial console gebruiken voor het volgende:
* Een [opdracht voor een systeem aanvraag naar uw virtuele machine](./serial-console-nmi-sysrq.md) verzenden
* Een [niet-maskeer bare interrupt naar uw virtuele machine](./serial-console-nmi-sysrq.md) verzenden
* [Uw VM zonder problemen opnieuw opstarten of geforceerd](./serial-console-power-options.md)


## <a name="next-steps"></a>Volgende stappen
Meer documentatie over de seriële console vindt u in de zijbalk.
- Meer informatie is beschikbaar voor [seriële console voor Linux-vm's](./serial-console-linux.md).
- Er is meer informatie beschikbaar voor de [seriële console voor Windows-vm's](./serial-console-windows.md).
