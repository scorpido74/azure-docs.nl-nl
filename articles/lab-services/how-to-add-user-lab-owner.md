---
title: Aanvullende eigen aars toevoegen aan een lab in Azure Lab Services
description: In dit artikel leest u hoe een beheerder een gebruiker kan toevoegen als eigenaar van een lab in Azure Lab Services.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 1438c9c4f33cca45b6c609e90cc2cbe7a762adb0
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897353"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Aanvullende eigen aars toevoegen aan een bestaand Lab in Azure Lab Services
Dit artikel laat u zien hoe u, als beheerder, extra eigen aren kunt toevoegen aan een bestaand Lab.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Gebruiker toevoegen aan de rol van lezer voor het lab-account
Als u een gebruiker wilt toevoegen als een extra eigenaar van een bestaand Lab, moet u eerst de gebruiker **Lees** machtigingen geven voor het lab-account.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Zoek naar **test services**en selecteer deze.
3. Selecteer uw **Lab-account** in de lijst. 
2. Selecteer op de **pagina Lab**-account **Access Control (IAM)** in het menu links. 
2. Selecteer op de pagina **toegangs beheer (IAM)** de optie **toevoegen** op de werk balk en selecteer **functie toewijzing toevoegen**.

    ![Roltoewijzing voor het lab-account ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Voer op de pagina **een roltoewijzing toevoegen** de volgende stappen uit: 
    1. Selecteer **lezer** voor de **rol**. 
    2. Selecteer de gebruiker. 
    3. Selecteer **Opslaan**. 

        ![Gebruiker toevoegen aan de rol van lezer voor het lab-account ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Gebruiker toevoegen aan de rol eigenaar voor het lab

1. Selecteer op de pagina **Lab-account** **alle Labs** in het menu links.
2. Selecteer het **Lab** waaraan u de gebruiker wilt toevoegen als eigenaar. 
    
    ![Het lab selecteren ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. Selecteer op de pagina **Lab** **toegangs beheer (IAM)** in het menu links.
4. Selecteer op de pagina **toegangs beheer (IAM)** de optie **toevoegen** op de werk balk en selecteer **functie toewijzing toevoegen**.
5. Voer op de pagina **een roltoewijzing toevoegen** de volgende stappen uit: 
    1. Selecteer de **eigenaar** voor de **rol**. 
    2. Selecteer de gebruiker. 
    3. Selecteer **Opslaan**. 

## <a name="next-steps"></a>Volgende stappen
Controleer of de gebruiker het lab ziet bij het aanmelden bij de portal voor de [Lab-Services](https://labs.azure.com).