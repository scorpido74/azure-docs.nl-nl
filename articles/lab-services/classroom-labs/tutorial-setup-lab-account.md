---
title: Een lab-account met Azure Lab Services instellen | Microsoft Docs
description: In deze zelfstudie stelt u een lab-account in met Azure Lab Services.
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
ms.date: 04/24/2019
ms.author: spelluru
ms.openlocfilehash: 8c252870a82a60a561f12fab9d728c028458212a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75562099"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Zelfstudie: een lab-account instellen met Azure Lab Services
In Azure Lab Services fungeert een lab-account als het centrale account waarin alle labs in uw organisatie worden beheerd. In uw lab-account machtigt u anderen om labs te maken en stelt u beleidsregels in die van toepassing zijn op alle labs in het lab-account. In deze zelfstudie leert u hoe u een lab-account maakt als lab-beheerder. 

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Maak een lab-account
> * Een gebruiker toevoegen aan de rol Labmaker
> * Microsoft Azure Marketplace-installatiekopieën die beschikbaar zijn voor eigenaars van een lab opgeven

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-a-lab-account"></a>Maak een lab-account
In de volgende stappen ziet u hoe u Azure Portal kunt gebruiken om een lab te maken met Azure Lab Services. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Selecteer **Lab Services** in de sectie **DEVOPS** . Als u ster (`*`) selecteert naast **Lab-Services**, wordt deze toegevoegd aan de sectie **Favorieten** in het menu links. Vanaf de volgende keer kunt u **Lab-Services** selecteren onder **Favorieten**.

    ![Alle services-> Lab-Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Op de pagina **Lab-Services** selecteert u **toevoegen** op de werk balk. 

    ![Selecteer de Toevoegen op de pagina Lab-accounts](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Voer op de pagina **Lab-account** de volgende acties uit: 
    1. Voer een naam in bij **lab-accountnaam**. 
    2. Selecteer het **Azure-abonnement** waarin u het lab-account wilt maken.
    3. Selecteer voor **Resourcegroep** de optie **Nieuwe maken** en voer een naam in voor de resourcegroep.
    4. Selecteer voor **Locatie** een locatie/regio waarin het lab-account moet worden gemaakt. 
    5. Selecteer een bestaande **Galerie met gedeelde afbeeldingen** of maak er een. U kunt de sjabloon-VM in de galerie met gedeelde afbeeldingen opslaan, zodat deze door anderen opnieuw kan worden gebruikt. Zie [een galerie met gedeelde afbeeldingen gebruiken in Azure Lab Services](how-to-use-shared-image-gallery.md)voor meer informatie over de galerieën met gedeelde afbeeldingen. 
    6. Selecteer voor het **virtuele netwerk**van de peer een virtueel netwerk (VNet) voor het lab-netwerk. Labs die in dit account is gemaakt, zijn verbonden met het geselecteerde VNet en hebben toegang tot de resources in het geselecteerde VNet. 
    7. Geef een **adres bereik** voor vm's op in het lab. Het adres bereik moet in de CIDR-notatie (Classless Inter-Domain Routing) staan (voor beeld: 10.20.0.0/23). Virtuele machines in het lab worden gemaakt in dit adres bereik. Zie [een adres bereik voor vm's in het lab opgeven](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab)voor meer informatie.
    8. Voor het veld **Labmaker toestaan om lablocatie te kiezen** geeft u op of u wilt dat labmakers een locatie voor het lab kunnen selecteren. De optie is standaard uitgeschakeld. Wanneer deze is uitgeschakeld, kunnen labmakers geen locatie opgeven voor het lab dat ze maken. De labs worden gemaakt in de dichtstbijzijnde geografische locatie in het labaccount. Wanneer deze is ingeschakeld, kan de labmaker een locatie selecteren wanneer hij een lab maakt. 
    9. Selecteer **Maken**. 

        ![Venster Een lab-account maken](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Selecteer het **Klokpictogram** op de werkbalk (**Meldingen**), controleer of de implementatie is voltooid en selecteer vervolgens **Naar de resource gaan**. 

    U kunt ook **Vernieuwen** selecteren op de **Lab-accounts**-pagina en het lab-account selecteren dat u hebt gemaakt. 

    ![Venster Een lab-account maken](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. U ziet de volgende pagina **lab-account**:

    ![Pagina lab-account](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Een gebruiker toevoegen aan de rol Labmaker
Om een leslokaallab in een labaccount in te kunnen stellen, moet de gebruiker lid zijn van de rol **Labmaker** in het labaccount. Het account dat u hebt gebruikt voor het maken van het lab-account wordt automatisch toegevoegd aan deze rol. Als u van plan bent een leslokaallab te maken met hetzelfde gebruikersaccount, kunt u deze stap overslaan. Als u een ander gebruikersaccount wilt gebruiken om een leslokaallab te maken, voert u de volgende stappen uit: 

Als u machtigingen wilt toekennen aan docenten om labs te maken voor hun lessen, voegt u hen toe aan de rol **Labmaker**:

1. Selecteer op de pagina **Labaccount** de optie **Toegangsbeheer (IAM)** , selecteer **+ Toevoegen** op de werkbalk en klik vervolgens op **+ Roltoewijzing toevoegen** in de werkbalk. 

    ![Access Control -> Knop Roltoewijzing toevoegen](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Selecteer op de pagina **Roltoewijzing toevoegen** de optie **Labmaker** als **Rol**. Selecteer de gebruiker die u wilt toevoegen aan de rol Labmaker en selecteer **Opslaan**. 

    ![Labmaker toevoegen](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Microsoft Azure Marketplace-installatiekopieën opgeven die beschikbaar zijn voor labmakers
Als eigenaar van een labaccount kunt u de Marketplace-installatiekopieën opgeven die labmakers kunnen gebruiken in het labaccount. 

1. Selecteer **Marketplace-installatiekopieën** in het menu aan de linkerkant. Standaard ziet u de volledige lijst met installatiekopieën (zowel ingeschakelde als uitgeschakelde). U kunt de lijst filteren om alleen ingeschakelde/uitgeschakelde installatiekopieën te bekijken door de optie **Alleen ingeschakeld**/**Alleen uitgeschakeld** in de vervolgkeuzelijst bovenaan te selecteren. 
    
    ![Pagina Microsoft Azure Marketplace-installatiekopieën](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    De Marketplace-installatiekopieën die worden weergegeven in de lijst, zijn de enige die voldoen aan de volgende voorwaarden:
        
    - Hiermee wordt een enkele VM gemaakt.
    - Maakt gebruik van Azure Resource Manager om VM’s in te richten
    - Hiervoor hoeft u geen extra licentieabonnement aan te schaffen
2. Als u een Microsoft Azure Marketplace-installatiekopie die is ingeschakeld wilt **uitschakelen**, voert u een van de volgende acties uit: 
    1. Selecteer **... (beletselteken)**  in de laatste kolom en selecteer **Installatiekopie uitschakelen**. 

        ![Een installatiekopie uitschakelen](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Selecteer een of meer installatiekopieën in de lijst door de selectievakjes bij de namen van de installatiekopieën in de lijst te selecteren en **Geselecteerde installatiekopieën uitschakelen** te selecteren. 

        ![Meerdere installatiekopieën uitschakelen](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Als u een Microsoft Azure Marketplace-installatiekopie wilt **inschakelen**, voert u een van de volgende acties uit: 
    1. Selecteer **... (beletselteken)**  in de laatste kolom en selecteer **Installatiekopie inschakelen**. 
    2. Selecteer een of meer installatiekopieën in de lijst door de selectievakjes bij de namen van de installatiekopieën in de lijst te selecteren en **Geselecteerde installatiekopieën inschakelen** te selecteren. 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een lab-account gemaakt. Ga verder met de volgende zelf studie voor meer informatie over het maken van een leslokaal Lab als docent:

> [!div class="nextstepaction"]
> [Een leslokaallab instellen](tutorial-setup-classroom-lab.md)

