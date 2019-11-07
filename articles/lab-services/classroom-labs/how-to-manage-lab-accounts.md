---
title: Lab-accounts beheren in Azure Lab Services | Microsoft Docs
description: Meer informatie over het maken van een Lab-account, het weer geven van alle Lab-accounts of het verwijderen van een Lab-account in een Azure-abonnement.
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
ms.openlocfilehash: ee64780bca13bf497793dc90ad22d3eaf91949a6
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606334"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Lab-accounts beheren in Azure Lab Services 
In Azure Lab Services is een Lab-account een container voor beheerde Lab-typen zoals klassikale Labs. Een beheerder stelt een Lab-account in met Azure Lab Services en biedt toegang tot Lab-eigen aars die in het account Labs kunnen maken. In dit artikel wordt beschreven hoe u een Lab-account maakt, alle Lab-accounts weergeeft of een Lab-account verwijdert.

## <a name="create-a-lab-account"></a>Een lab-account maken
In de volgende stappen ziet u hoe u Azure Portal kunt gebruiken om een lab te maken met Azure Lab Services. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Selecteer **Lab-accounts** in de sectie **DevOps** . Selecteer de ster (`*`) naast de **Lab-accounts** om er een toe te voegen aan de sectie **FAVORIETEN** in het menu links. Vanaf de volgende keer selecteert u **Lab-accounts** onder **Favorieten**.

    ![Alle Services -> Lab-accounts](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecteer op de pagina **Lab-accounts** **Toevoegen** op de werkbalk. 

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

## <a name="view-lab-accounts"></a>Lab-accounts weer geven
1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Selecteer **alle resources** in het menu. 
3. Selecteer een **Lab-account** voor het **type**. 
    U kunt ook filteren op abonnement, resource groep, locaties en tags. 

    ![Alle resources-> Lab-accounts](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Labs in het lab-account weer geven en beheren

1. Selecteer op de pagina **Lab-account** de optie **Labs** in het menu links.

    ![Labs in het account](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. U ziet een **lijst met Labs** in het account met de volgende informatie: 
    1. De naam van het lab.
    2. De datum waarop het lab is gemaakt. 
    3. Het e-mail adres van de gebruiker die het lab heeft gemaakt. 
    4. Maxi maal aantal gebruikers dat is toegestaan voor het lab. 
    5. De status van het lab. 

## <a name="delete-a-lab-in-the-lab-account"></a>Een Lab verwijderen in het lab-account
Volg de instructies in de vorige sectie om een lijst met de Labs in het lab-account weer te geven.

1. Selecteren **... (weglatings tekens)** en selecteer **verwijderen**. 

    ![Een Lab-knop verwijderen](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Selecteer **Ja** in het waarschuwings bericht. 

    ![Verwijderen van Lab bevestigen](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Een Lab-account verwijderen
Volg de instructies in de vorige sectie waarin Lab-accounts in een lijst worden weer gegeven. Gebruik de volgende instructies om een Lab-account te verwijderen: 

1. Selecteer het **Lab-account** dat u wilt verwijderen. 
2. Selecteer **verwijderen** in de werk balk. 

    ![Lab-accounts-> knop verwijderen](../media/how-to-manage-lab-accounts/delete-button.png)
1. Typ **Ja** ter bevestiging.
1. Selecteer **Verwijderen**. 

    ![Lab-account verwijderen-bevestiging](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> U kunt ook de Power shell-module AZ. LabServices (preview) gebruiken voor het beheren van Lab-accounts. Zie de [Introductie pagina AZ. LabServices op github](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel: [Lab-accounts configureren](how-to-configure-lab-accounts.md).