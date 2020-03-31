---
title: Extra eigenaren toevoegen aan een lab in Azure Lab Services
description: In dit artikel ziet u hoe een beheerder een gebruiker als eigenaar kan toevoegen aan een lab in Azure Lab Services.
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
ms.openlocfilehash: 9302cadf0b900c9faac382bad86b51cea7d828fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443513"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Extra eigenaren toevoegen aan een bestaand lab in Azure Lab Services
In dit artikel ziet u hoe u als beheerder extra eigenaren toevoegen aan een bestaand lab.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>Gebruiker toevoegen aan de lezersrol voor het labaccount
Als u een gebruiker als extra eigenaar wilt toevoegen aan een bestaand lab, moet u de gebruiker eerst **leesmachtigingen** voor het labaccount geven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Zoek naar **Lab Services**en selecteer deze.
3. Selecteer uw **labaccount** in de lijst. 
2. Selecteer **access control (IAM)** op de **pagina Lab-account**in het linkermenu. 
2. Selecteer op de pagina **Toegangsbesturingselement (IAM)** de optie **Toevoegen** op de werkbalk en selecteer **Roltoewijzing toevoegen**.

    ![Roltoewijzing voor het labaccount ](../media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. Ga op de pagina **Een roltoewijzing toevoegen** de volgende stappen uit: 
    1. Selecteer **Reader** voor de **rol**. 
    2. Selecteer de gebruiker. 
    3. Selecteer **Opslaan**. 

        ![Gebruiker toevoegen aan de lezersrol voor het labaccount ](../media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>Gebruiker toevoegen aan de eigenaarrol voor het lab

1. Selecteer Alle **labs** op het linkermenu terug op de pagina **Lab-account.**
2. Selecteer het **lab** waaraan u als eigenaar gebruiker wilt toevoegen. 
    
    ![Het lab selecteren ](../media/how-to-add-user-lab-owner/select-lab.png)    
3. Selecteer op de pagina **Lab** de optie **Toegangsbeheer (IAM)** in het linkermenu.
4. Selecteer op de pagina **Toegangsbesturingselement (IAM)** de optie **Toevoegen** op de werkbalk en selecteer **Roltoewijzing toevoegen**.
5. Ga op de pagina **Een roltoewijzing toevoegen** de volgende stappen uit: 
    1. Selecteer **Eigenaar** voor de **rol**. 
    2. Selecteer de gebruiker. 
    3. Selecteer **Opslaan**. 

## <a name="next-steps"></a>Volgende stappen
Controleer of de gebruiker het lab ziet bij het inloggen op de [Lab Services-portal.](https://labs.azure.com)