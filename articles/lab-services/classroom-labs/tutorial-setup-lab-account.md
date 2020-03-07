---
title: Een lab-account met Azure Lab Services instellen | Microsoft Docs
description: Meer informatie over het instellen van een Lab-account met Azure Lab Services, het toevoegen van een Lab-maker en het opgeven van Marketplace-installatie kopieën die door Labs in het lab-account moeten worden gebruikt.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361049"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Zelfstudie: een lab-account instellen met Azure Lab Services
In Azure Lab Services fungeert een lab-account als het centrale account waarin alle labs in uw organisatie worden beheerd. In uw lab-account machtigt u anderen om labs te maken en stelt u beleidsregels in die van toepassing zijn op alle labs in het lab-account. In deze zelf studie leert u hoe u een Lab-account maakt. 

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Een lab-account maken
> * Een gebruiker toevoegen aan de rol Labmaker

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-a-lab-account"></a>Een lab-account maken
In de volgende stappen ziet u hoe u Azure Portal kunt gebruiken om een lab te maken met Azure Lab Services. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Selecteer **DevOps** uit **Categorieën**. Selecteer vervolgens **Lab-Services**. Als u ster (`*`) selecteert naast **Lab-Services**, wordt deze toegevoegd aan de sectie **Favorieten** in het menu links. Vanaf de volgende keer kunt u **Lab-Services** selecteren onder **Favorieten**.

    ![Alle services-> Lab-Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Op de pagina **Lab-Services** selecteert u **toevoegen** op de werk balk of selecteert u de knop **Lab-account maken** op de pagina. 

    ![Selecteer de Toevoegen op de pagina Lab-accounts](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Voer op het tabblad **basis** van de pagina **een Lab-account maken** de volgende acties uit: 
    1. Voer een naam in bij **lab-accountnaam**. 
    2. Selecteer het **Azure-abonnement** waarin u het lab-account wilt maken.
    3. Selecteer bij **resource groep**een bestaande resource groep of selecteer **nieuwe maken**en voer een naam in voor de resource groep.
    4. Selecteer bij **locatie**een locatie/regio waarin u het lab-account wilt maken. 

        ![Lab-account-basis pagina](../media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. Selecteer **Controleren + maken**.
    6. Controleer de samen vatting en selecteer **maken**. 

        ![Controleren + maken-> maken](../media/tutorial-setup-lab-account/create-button.png)    
5. Wanneer de implementatie is voltooid, vouwt u **volgende stappen**uit en selecteert **u Ga naar resource**. 

    ![Ga naar de pagina Lab-account](../media/tutorial-setup-lab-account/go-to-lab-account.png)
6. Controleer of de pagina **Lab-account** wordt weer geven. 

    ![Pagina lab-account](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Een gebruiker toevoegen aan de rol Labmaker
Om een leslokaallab in een labaccount in te kunnen stellen, moet de gebruiker lid zijn van de rol **Labmaker** in het labaccount. Als u machtigingen wilt toekennen aan docenten om labs te maken voor hun lessen, voegt u hen toe aan de rol **Labmaker**:

> [!NOTE]
> Het account dat u hebt gebruikt voor het maken van het lab-account wordt automatisch toegevoegd aan deze rol. Als u van plan bent om hetzelfde gebruikers account te gebruiken om in deze zelf studie een leslokaal Lab te maken, slaat u deze stap over. 

1. Selecteer op de pagina **Labaccount** de optie **Toegangsbeheer (IAM)** , selecteer **+ Toevoegen** op de werkbalk en klik vervolgens op **+ Roltoewijzing toevoegen** in de werkbalk. 

    ![Access Control -> Knop Roltoewijzing toevoegen](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Selecteer op de pagina **Roltoewijzing toevoegen** de optie **Labmaker** als **Rol**. Selecteer de gebruiker die u wilt toevoegen aan de rol Labmaker en selecteer **Opslaan**. 

    ![Labmaker toevoegen](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een lab-account gemaakt. Ga verder met de volgende zelf studie voor meer informatie over het maken van een leslokaal Lab als docent:

> [!div class="nextstepaction"]
> [Een leslokaal-lab instellen](tutorial-setup-classroom-lab.md)

