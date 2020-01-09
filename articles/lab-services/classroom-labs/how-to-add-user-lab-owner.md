---
title: Gebruiker toevoegen als een Lab-eigenaar in Azure Lab Services
description: In dit artikel leest u hoe een beheerder een gebruiker kan toevoegen als eigenaar van een lab.
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
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 9e007ee6c95843b7e8ef9d7a701237034811489e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480851"
---
# <a name="how-to-add-a-user-as-an-owner-of-a-classroom-lab-in-azure-lab-services"></a>Een gebruiker toevoegen als eigenaar van een leslokaal Lab in Azure Lab Services
In dit artikel wordt beschreven hoe u een gebruiker toevoegt als eigenaar van een lab in Azure Lab Services.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Gebruiker toevoegen aan de rol van lezer voor het lab-account
1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Zoek naar **test services**en selecteer deze.
3. Selecteer uw **Lab-account** in de lijst. 
2. Selecteer op de **pagina Lab**-account **Access Control (IAM)** in het menu links. 
2. Selecteer op de pagina **toegangs beheer (IAM)** de optie **toevoegen** op de werk balk en selecteer **functie toewijzing toevoegen**.

    ![Roltoewijzing voor het lab-account ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Voer op de pagina **een roltoewijzing toevoegen** de volgende stappen uit: 
    1. Selecteer **lezer** voor de **rol**. 
    2. Selecteer de gebruiker. 
    3. Selecteer **Opslaan**. 

        ![Gebruiker toevoegen aan de rol van lezer voor het lab-account ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Gebruiker toevoegen aan de rol eigenaar voor het lab

1. Selecteer op de pagina **Lab-account** **alle Labs** in het menu links.
2. Selecteer het **Lab** waaraan u de gebruiker wilt toevoegen als eigenaar. 
    
    ![Het lab selecteren ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. Selecteer op de pagina **Lab** **toegangs beheer (IAM)** in het menu links.
4. Selecteer op de pagina **toegangs beheer (IAM)** de optie **toevoegen** op de werk balk en selecteer **functie toewijzing toevoegen**.
5. Voer op de pagina **een roltoewijzing toevoegen** de volgende stappen uit: 
    1. Selecteer de **eigenaar** voor de **rol**. 
    2. Selecteer de gebruiker. 
    3. Selecteer **Opslaan**. 

## <a name="next-steps"></a>Volgende stappen
Controleer of de gebruiker het lab ziet bij het aanmelden bij de portal voor de [Lab-Services](https://labs.azure.com).