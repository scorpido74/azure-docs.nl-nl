---
title: Azure DevTest Labs gebruiken voor training | Microsoft Documenten
description: In dit artikel vindt u gedetailleerde stappen die u volgen om een lab in te stellen voor training in Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 8f0a930d6e3c04548ade71f6d4e3294114eb60a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759974"
---
# <a name="use-azure-devtest-labs-for-training"></a>Azure DevTest Labs gebruiken voor training
Azure DevTest Labs kan worden gebruikt om naast dev/test ook veel belangrijke scenario's te implementeren. Een van die scenario's is het opzetten van een lab voor training. Met Azure DevTest Labs u een lab maken waar u aangepaste sjablonen bieden die elke cursist kan gebruiken om identieke en geïsoleerde omgevingen te maken voor training. U beleid toepassen om ervoor te zorgen dat trainingsomgevingen alleen beschikbaar zijn voor elke cursist wanneer deze nodig zijn en voldoende resources bevatten - zoals virtuele machines - die nodig zijn voor de training. Tot slot u het lab eenvoudig delen met stagiaires, waartoe ze in één klik toegang hebben.

![DevTest Labs gebruiken voor training](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs voldoet aan de volgende vereisten die nodig zijn om training uit te voeren in elke virtuele omgeving: 

* Stagiairs kunnen vm's die door andere stagiairs zijn gemaakt niet zien
* Elke trainingsmachine moet identiek zijn
* Stagiairs kunnen snel hun trainingsomgeving en
* Beheers de kosten door ervoor te zorgen dat stagiairs niet meer VM's kunnen krijgen dan ze nodig hebben voor de training en ook vm's kunnen afsluiten wanneer ze ze niet gebruiken
* Deel het trainingslab eenvoudig met elke cursist
* Hergebruik van het trainingslab steeds weer

In dit artikel vindt u meer informatie over verschillende Azure DevTest Labs-functies die kunnen worden gebruikt om te voldoen aan de eerder beschreven trainingsvereisten en gedetailleerde stappen die u volgen om een lab voor training in te stellen.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Training implementeren met Azure DevTest Labs
1. **Maak het lab** 
   
    Labs zijn het startpunt in Azure DevTest Labs. Zodra u een lab hebt gemaakt, u taken uitvoeren, zoals gebruikers (stagiairs) toevoegen aan het lab, beleid instellen om de kosten te beheersen, VM-afbeeldingen definiëren die snel kunnen maken en meer.   
   
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een lab maken in Azure DevTest Labs](devtest-lab-create-lab.md) |Meer informatie over het maken van een lab in Azure DevTest Labs in de Azure-portal. |
2. **Maak in enkele minuten trainingsVM's met kant-en-klare marketplace-afbeeldingen en aangepaste afbeeldingen** 
   
    U kant-en-klare afbeeldingen kiezen uit een breed scala aan afbeeldingen in de Azure Marketplace en deze beschikbaar maken voor de cursisten in het lab. Als de kant-en-klare afbeeldingen niet aan uw vereisten voldoen, u een aangepaste afbeelding maken door een lab-vm te maken met behulp van een kant-en-klare afbeelding van Azure Marketplace, alle software te installeren die u nodig hebt voor de training en de VM op te slaan als aangepaste afbeelding in het lab. 
   
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Azure Marketplace-installatiekopieën configureren](devtest-lab-configure-marketplace-images.md) |Meer informatie over hoe u Azure Marketplace-afbeeldingen op de witte lijst plaatsen; het beschikbaar stellen voor selectie alleen de afbeeldingen die u wilt voor de training. |
   | [Een aangepaste installatiekopie maken](devtest-lab-create-template.md) |Maak een aangepaste afbeelding door de software die u nodig hebt voor de training vooraf te installeren, zodat cursisten snel een VM kunnen maken met behulp van de aangepaste afbeelding. |
3. **Herbruikbare sjablonen maken voor trainingsmachines** 
   
    Een formule in Azure DevTest Labs is een lijst met standaardeigenschapwaarden die worden gebruikt om een VM te maken. U een formule in het lab maken door een afbeelding, een VM-grootte (een combinatie van CPU en RAM) en een virtueel netwerk te kiezen. Elke cursist kan de formule in het lab zien en deze gebruiken om een VM te maken. 
   
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [DevTest Labs-formules beheren om VM's te maken](devtest-lab-manage-formulas.md) |Ontdek hoe u een formule maken door een afbeelding, VM-grootte (combinatie van CPU en RAM) en een virtueel netwerk op te pikken. |
4. **Beheerskosten**
   
    Met Azure DevTest Labs u een beleid in het lab instellen om het maximum aantal VM's op te geven dat kan worden gemaakt door een cursist in het lab. 
   
    Als u meerdaagse training uitvoert en alle VM's op een bepaald tijdstip van de dag wilt stoppen en deze de volgende dag automatisch opnieuw wilt starten, u dat eenvoudig bereiken door het beleid voor automatisch afsluiten en automatisch starten in het lab in te stellen. 
   
    Ten slotte u, wanneer de training is voltooid, alle VM's tegelijk verwijderen door één PowerShell-script uit te voeren. 
   
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Beleid voor lab maken](devtest-lab-set-lab-policy.md) |Beheer de kosten door beleid in het lab in te stellen. |
   | [Alle lab-VM's verwijderen met behulp van een PowerShell-script](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Verwijder alle labs in één bewerking wanneer de training is voltooid. |
5. **Deel het lab met elke cursist**
   
    Labs zijn rechtstreeks toegankelijk via een link die u deelt met uw cursisten. Uw cursisten hoeven niet eens een Azure-account te hebben, zolang ze maar een [Microsoft-account](devtest-lab-faq.md#what-is-a-microsoft-account)hebben. Stagiairs kunnen vm's die door andere stagiairs zijn gemaakt, niet zien.  
   
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een stagiair toevoegen aan een lab in Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Gebruik de Azure-portal om stagiairs toe te voegen aan uw trainingslab. |
   | [Stagiaires toevoegen aan het lab met behulp van een PowerShell-script](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Gebruik PowerShell om het toevoegen van trainees aan uw trainingslab te automatiseren. |
   | [Krijg een link naar het lab](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Meer informatie over hoe een lab rechtstreeks toegankelijk is via een hyperlink. |
6. **Hergebruik van het lab opnieuw en opnieuw** 
   
    U het maken van laboratoria automatiseren, inclusief aangepaste instellingen, door een Resource Manager-sjabloon te maken en deze steeds opnieuw te gebruiken om identieke labs te maken. 
   
    Meer informatie door op de koppelingen in de volgende tabel te klikken:
   
   | Taak | Wat u leert |
   | --- | --- |
   | [Een lab maken met een resourcemanagersjabloon](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Maak labs in Azure DevTest Labs met behulp van Resource Manager-sjablonen. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

