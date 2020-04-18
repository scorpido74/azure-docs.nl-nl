---
title: Cursusleider die toegang krijgt tot VM's in Azure Lab Services
description: In dit artikel ziet u hoe cursusleiders toegang hebben tot hun vm's voor studenten vanuit de weergave van de cursusleider. Een onderwijsassistent kan bijvoorbeeld een instructeur zijn voor één klas, maar een leerling voor andere klassen.
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
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641938"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Toegang tot virtuele machines als student vanuit de cursusleider
In dit artikel ziet u hoe cursusleiders toegang hebben tot hun VM's voor lessen die ze als studenten bijwonen. 

Hier is een scenario waarin deze functie zal helpen. Een onderwijsassistent is een instructeur voor één klas, maar een leerling in andere klassen. En, de onderwijsinstructeur wil de student VM's bekijken en openen vanuit de instructeurweergave die de labs laat zien die ze bezitten. 

## <a name="access-vms-from-instructor-view"></a>Toegang tot VM's vanuit de weergave van de cursusleider

1. Meld u aan bij de [azure lab services-website.](https://labs.azure.com) Je ziet de labs die je bezit. Deze labs kunnen labs zijn die u zelf hebt gemaakt of de laboratoria die u als eigenaar hebben toegewezen. Zie [Extra eigenaren toevoegen aan een bestaand lab voor](how-to-add-user-lab-owner.md) meer informatie
2. Als u toegang wilt krijgen tot VM's voor lessen die u als student volgt, selecteert u het computerpictogram in de rechterbovenhoek. Controleer of je VM's ziet die je als student openen. In het volgende voorbeeld is de gebruiker een onderwijsassistent voor het Python-lab, maar een student van het Java-lab. Dus, de gebruiker ziet de VM uit het Java-lab in de vervolgkeuzelijst. De gebruiker kan de VM starten en er verbinding mee maken. 
    
    ![Toegang tot vm's voor studenten](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Verbinding maken met een VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Verbinding maken met een vm met RDP op een Mac](connect-virtual-machine-mac-rdp.md)
- [Extern bureaublad gebruiken voor virtuele Linux-machines](how-to-use-remote-desktop-linux-student.md)
