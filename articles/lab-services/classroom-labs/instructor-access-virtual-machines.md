---
title: Instructeur die toegang heeft tot Vm's in Azure Lab Services
description: In dit artikel wordt uitgelegd hoe docenten toegang hebben tot de Vm's van studenten vanuit de docent. Een onderwijs assistent kan bijvoorbeeld een docent zijn voor één klasse, maar een student voor andere klassen.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641938"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Toegang tot virtuele machines als student vanuit de docenten weergave
In dit artikel wordt uitgelegd hoe docenten toegang hebben tot hun Vm's voor klassen die ze als studenten bijwonen. 

Hier volgt een scenario waarin deze functie helpt. Een onderwijs assistent is een docent voor één klasse, maar een student in andere klassen. En de docent wil de student-Vm's bekijken en openen vanuit de docenten weergave waarin de laboratoria zijn vermeld. 

## <a name="access-vms-from-instructor-view"></a>Toegang tot Vm's vanuit de weer gave docent

1. Meld u aan bij de [Azure Lab Services-website](https://labs.azure.com). U ziet de Labs waarvan u de eigenaar bent. Deze labs kunnen Labs zijn die u zelf hebt gemaakt of de laboratoria die de beheerder aan u heeft toegewezen als eigenaar. Zie [aanvullende eigen aren toevoegen aan een bestaand Lab](how-to-add-user-lab-owner.md) voor meer informatie.
2. Als u toegang wilt krijgen tot Vm's voor klassen die u als student bijwoont, selecteert u het pictogram computer in de rechter bovenhoek. Controleer of u de Vm's ziet die u als student kunt gebruiken. In het volgende voor beeld is de gebruiker een onderwijs assistent voor het python Lab, maar een student van het Java-Lab. De gebruiker ziet de VM dus in de vervolg keuzelijst van het Java-Lab. De gebruiker kan de virtuele machine starten en er verbinding mee maken. 
    
    ![Toegang tot Vm's van studenten](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Verbinding maken met een VM](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Verbinding maken met een virtuele machine met behulp van RDP op een Mac](connect-virtual-machine-mac-rdp.md)
- [Extern bureau blad voor virtuele Linux-machines gebruiken](how-to-use-remote-desktop-linux-student.md)
