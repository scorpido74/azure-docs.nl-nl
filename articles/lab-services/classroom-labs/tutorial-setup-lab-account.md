---
title: Een lab-account met Azure Lab Services instellen | Microsoft Docs
description: Meer informatie over het instellen van een labaccount met Azure Lab Services, het toevoegen van een labmaker en het opgeven van Marketplace-afbeeldingen die moeten worden gebruikt door laboratoria in het labaccount.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: dba6a4c07691f3d7ec88d8b889e68d6ac7116f07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239446"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Zelfstudie: een lab-account instellen met Azure Lab Services
In Azure Lab Services fungeert een lab-account als het centrale account waarin alle labs in uw organisatie worden beheerd. In uw lab-account machtigt u anderen om labs te maken en stelt u beleidsregels in die van toepassing zijn op alle labs in het lab-account. In deze zelfstudie leert u hoe u een labaccount maakt. 

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Een lab-account maken
> * Een gebruiker toevoegen aan de rol Labmaker

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-lab-account"></a>Een lab-account maken
In de volgende stappen ziet u hoe u Azure Portal kunt gebruiken om een lab te maken met Azure Lab Services. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Selecteer **DevOps** uit **categorieën**. Selecteer vervolgens **Lab Services**. Als u ster`*`( ) naast **Lab Services**selecteert, wordt deze toegevoegd aan de sectie **FAVORIETEN** in het linkermenu. Vanaf de volgende keer selecteert u **Lab Services** onder **FAVORIETEN**.

    ![Alle services -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecteer op de pagina **Lab Services** **de** optie Toevoegen op de werkbalk of selecteer de knop **Labaccount maken** op de pagina. 

    ![Selecteer de Toevoegen op de pagina Lab-accounts](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Ga op het tabblad **Basisbeginselen** van de pagina **Een labaccount maken** de volgende acties uit: 
    1. Voer een naam in bij **lab-accountnaam**. 
    2. Selecteer het **Azure-abonnement** waarin u het lab-account wilt maken.
    3. Selecteer **voor resourcegroep**een bestaande resourcegroep of selecteer **Nieuw maken**en voer een naam in voor de resourcegroep.
    4. Selecteer **bij Locatie**een locatie/regio waarin u het labaccount wilt maken. 

        ![Lab-account - basispagina](../media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Selecteer **Controleren + maken**.
    6. Bekijk het overzicht en selecteer **Maken**. 

        ![Bekijken + maken -> maken](../media/tutorial-setup-lab-account/create-button.png)    
5. Wanneer de implementatie is voltooid, vouwt u **Volgende stappen**uit en selecteert u Ga **naar resource**. 

    ![Ga naar de accountpagina van het lab](../media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Controleer of u de pagina **Lab-account ziet.** 

    ![Pagina lab-account](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Een gebruiker toevoegen aan de rol Labmaker
Om een leslokaallab in een labaccount in te kunnen stellen, moet de gebruiker lid zijn van de rol **Labmaker** in het labaccount. Als u docenten de toestemming wilt geven om labs voor hun klassen te maken, voegt u ze toe aan de rol **Lab Creator:**

> [!NOTE]
> Het account dat u hebt gebruikt voor het maken van het lab-account wordt automatisch toegevoegd aan deze rol. Als u van plan bent hetzelfde gebruikersaccount te gebruiken om een klaslokaallab te maken in deze zelfstudie, slaat u deze stap over. 

1. Selecteer op de pagina **Labaccount** de optie **Toegangsbeheer (IAM)**, selecteer **+ Toevoegen** op de werkbalk en klik vervolgens op **+ Roltoewijzing toevoegen** in de werkbalk. 

    ![Access Control -> Knop Roltoewijzing toevoegen](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Selecteer op de pagina **Roltoewijzing toevoegen** de optie **Labmaker** als **Rol**. Selecteer de gebruiker die u wilt toevoegen aan de rol Labmaker en selecteer **Opslaan**. 

    ![Labmaker toevoegen](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een lab-account gemaakt. Ga naar de volgende lesstudie voor meer informatie over het maken van een leslokaallab:

> [!div class="nextstepaction"]
> [Een leslokaallab instellen](tutorial-setup-classroom-lab.md)

