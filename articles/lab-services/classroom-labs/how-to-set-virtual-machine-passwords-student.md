---
title: Wachtwoorden voor vm's in klassikale labin Azure Lab Services opnieuw instellen | Microsoft Documenten
description: Meer informatie over het opnieuw instellen van wachtwoorden voor virtuele machines (VM's) in klaslabs van Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 7c757ef8508f9364a46116e6ddf19207f23a4b6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73583659"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-students"></a>Wachtwoord instellen of opnieuw instellen voor virtuele machines in klaslokalen (studenten)
In dit artikel ziet u hoe studenten het wachtwoord voor hun VM's kunnen instellen/resetten. 

## <a name="enable-resetting-of-passwords"></a>Opnieuw instellen van wachtwoorden inschakelen
Op het moment van het maken van het lab kan de eigenaar van het lab het **zelfde wachtwoord gebruiken voor alle virtuele machines**in- of uitschakelen. Als deze optie is ingeschakeld, kunnen studenten het wachtwoord niet opnieuw instellen. Alle VM's in de labs hebben hetzelfde wachtwoord dat is ingesteld door de instructeur. 

Als deze optie is uitgeschakeld, moeten gebruikers een wachtwoord instellen wanneer ze voor de eerste keer verbinding proberen te maken met de vm. Studenten kunnen het wachtwoord ook later opnieuw instellen, zoals in het laatste gedeelte van dit artikel wordt weergegeven. 

## <a name="reset-password-for-the-first-time"></a>Wachtwoord voor de eerste keer opnieuw instellen
Als de optie **Hetzelfde wachtwoord gebruiken voor alle virtuele machines** is uitgeschakeld, ziet de gebruiker het volgende dialoogvenster om het wachtwoord voor de vm in te stellen wanneer gebruikers (studenten) de knop **Verbinding** maken op de labtegel op de pagina Mijn **virtuele machines** selecteren: 

![Wachtwoord opnieuw instellen voor de student](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Wachtwoord later opnieuw instellen
Student kan ook het wachtwoord instellen door te klikken op het overloopmenu **(verticale drie stippen)** op de labtegel en **door wachtwoord opnieuw instellen**te selecteren. 

![Wachtwoord later opnieuw instellen](../media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel: [Studentengebruik configureren](how-to-configure-student-usage.md)voor meer informatie over andere opties voor het gebruik van studenten die een labeigenaar kan configureren.
