---
title: Veelgestelde vragen over de MSIX-app van Windows virtueel bureau blad-Azure
description: Veelgestelde vragen over MSIX app attach voor Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556270"
---
# <a name="msix-app-attach-faq"></a>Veelgestelde vragen over het koppelen van MSIX-apps

In dit artikel vindt u antwoorden op veelgestelde vragen over MSIX app attach voor Windows virtueel bureau blad.

## <a name="does-msix-app-attach-use-fslogix"></a>Maakt MSIX-app gebruik van FSLogix?

MSIX app attach maakt geen gebruik van FSLogix. App attach en FSLogix zijn echter ontworpen om samen te werken om een naadloze gebruikers ervaring te bieden.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Kan ik MSIX app-koppeling buiten het virtuele bureau blad van Windows gebruiken?

Ja, MSIX app attach is een functie die deel uitmaakt van Windows 10 Enter prise en kan worden gebruikt buiten het virtuele bureau blad van Windows. Er is echter geen beheer vlak voor de MSIX-app die zich buiten het virtuele bureau blad van Windows bevindt.

## <a name="how-do-i-get-an-msix-package"></a>Hoe kan ik een MSIX-pakket ophalen?

Uw software leverancier geeft u een MSIX-pakket. U kunt ook niet-MSIX-pakketten converteren naar MSIX. Meer informatie over het [verplaatsen van uw bestaande installatie Programma's naar MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Welke besturings systemen ondersteunen MSIX-app koppelen?

Windows 10 Enter prise en Windows 10 Enter prise multi-session.

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over het toevoegen van MSIX-apps, raadpleegt u onze [overzichts](what-is-app-attach.md) [woordenlijst](app-attach-glossary.md). U kunt ook aan de slag met het [instellen van een app-koppeling](app-attach.md).