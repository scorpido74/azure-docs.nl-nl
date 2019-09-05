---
title: Een galerie met gedeelde afbeeldingen koppelen of ontkoppelen in Azure Lab Services | Microsoft Docs
description: Meer informatie over het koppelen van een galerie met gedeelde afbeeldingen aan een lab in Azure Lab Services.
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 18a14981c97af8e9d90480f7b04d50fc6df6b01d
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382715"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Een galerie met gedeelde afbeeldingen koppelen of ontkoppelen in Azure Lab Services
Docenten/Lab-beheerder kunnen een sjabloon-VM-installatie kopie opslaan in een [Galerie met gedeelde installatie kopieën](../../virtual-machines/windows/shared-image-galleries.md) van Azure, zodat deze door anderen opnieuw kan worden gebruikt. Als eerste stap koppelt de testlab-beheerder een bestaande galerie met gedeelde afbeeldingen aan het lab-account. Zodra de galerie met gedeelde installatie kopieën is gekoppeld, kunnen Labs die in het lab-account is gemaakt, afbeeldingen opslaan in de galerie met gedeelde afbeeldingen. Andere docenten kunnen deze afbeelding selecteren in de galerie met gedeelde afbeeldingen om een sjabloon voor hun klassen te maken. 

In dit artikel wordt beschreven hoe u een galerie met gedeelde afbeeldingen koppelt aan of koppelt aan een Lab-account. 

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configureren op het moment dat het lab-account wordt gemaakt
Wanneer u een Lab-account maakt, kunt u een galerie met gedeelde afbeeldingen koppelen aan het lab-account. U kunt een bestaande galerie met gedeelde afbeeldingen selecteren in de vervolg keuzelijst of een nieuwe maken. Als u een galerie met gedeelde afbeeldingen wilt maken en koppelen aan het lab-account, selecteert u **Nieuw maken**, voert u een naam in voor de galerie en voert u **OK**in. 

![Galerie met gedeelde afbeeldingen configureren op het moment dat het account wordt gemaakt](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configureren nadat het lab-account is gemaakt
Nadat het lab-account is gemaakt, kunt u de volgende taken uitvoeren:

- Een galerie met gedeelde afbeeldingen maken en koppelen
- Een galerie met gedeelde afbeeldingen koppelen aan het lab-account
- Een galerie met gedeelde afbeeldingen loskoppelen van het lab-account

## <a name="create-and-attach-a-shared-image-gallery"></a>Een galerie met gedeelde afbeeldingen maken en koppelen
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Selecteer **Lab Services** in de sectie **DEVOPS** . Als u ster (`*`) naast Lab- **Services**selecteert, wordt deze toegevoegd aan de sectie **Favorieten** in het menu links. Vanaf de volgende keer kunt u **Lab-Services** selecteren onder **Favorieten**.

    ![Alle services-> Lab-Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecteer uw Lab-account om de pagina **Lab-account** weer te geven. 
4. Selecteer in het menu links **gedeelde installatie kopie galerie** en selecteer **+ maken** op de werk balk.  

    ![Knop gedeelde installatie kopie galerie maken](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Voer in het venster **gedeelde installatie kopie galerie maken** een **naam** in voor de galerie en voer **OK**in. 

    ![Venster gedeelde installatie kopie galerie maken](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services maakt de galerie met gedeelde afbeeldingen en koppelt deze aan het lab-account. Alle Labs die in dit lab-account is gemaakt, hebben toegang tot de gekoppelde gedeelde afbeeldingen galerie. 

    ![Galerie met bijgevoegde afbeeldingen](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    In het onderste deel venster ziet u afbeeldingen in de galerie met gedeelde afbeeldingen. In deze nieuwe galerie zijn er geen installatie kopieën. Wanneer u afbeeldingen naar de galerie uploadt, worden deze op deze pagina weer geven.     

    Alle installatie kopieën in de gekoppelde gedeelde installatie kopie galerie zijn standaard ingeschakeld. U kunt geselecteerde installatie kopieën in-of uitschakelen door ze te selecteren in de lijst en de knop geselecteerde installatie kopieën **inschakelen** of **geselecteerde afbeeldingen uitschakelen** te gebruiken.

## <a name="attach-an-existing-shared-image-gallery"></a>Een bestaande galerie met gedeelde afbeeldingen koppelen
De volgende procedure laat zien hoe u een bestaande galerie met gedeelde afbeeldingen koppelt aan een Lab-account. 

1. Selecteer op de pagina **Lab-account** de optie **Galerie met gedeelde afbeeldingen** in het linkermenu en selecteer vervolgens **bijvoegen** op de werk balk. 

    ![Galerie gedeelde afbeeldingen-knop toevoegen](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Selecteer op de pagina **een bestaande gedeelde installatie kopie galerie koppelen** de galerie met gedeelde afbeeldingen en selecteer **OK**.

    ![Een bestaande galerie selecteren](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Het volgende scherm wordt weer gegeven: 

    ![Mijn galerie in de lijst](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    In dit voor beeld zijn er nog geen installatie kopieën in de galerie met gedeelde afbeeldingen.

    Azure Lab Services identiteit wordt toegevoegd als een bijdrager aan de galerie met gedeelde afbeeldingen die is gekoppeld aan het lab. Hiermee kunnen docenten/IT-beheerder installatie kopieën van virtuele machines opslaan in de galerie met gedeelde installatie kopieën. Alle Labs die in dit lab-account is gemaakt, hebben toegang tot de gekoppelde gedeelde afbeeldingen galerie. 

    Alle installatie kopieën in de gekoppelde gedeelde installatie kopie galerie zijn standaard ingeschakeld. U kunt geselecteerde installatie kopieën in-of uitschakelen door ze te selecteren in de lijst en de knop geselecteerde installatie kopieën **inschakelen** of **geselecteerde afbeeldingen uitschakelen** te gebruiken. 

## <a name="detach-a-shared-image-gallery"></a>Een galerie met gedeelde afbeeldingen loskoppelen
Er kan slechts één galerie met gedeelde afbeeldingen worden gekoppeld aan een lab. Als u een andere galerie met gedeelde afbeeldingen wilt koppelen, moet u het huidige loskoppelen voordat u het nieuwe toevoegt. Als u een galerie met gedeelde installatie kopieën wilt loskoppelen van uw Lab, selecteert u **loskoppelen** op de werk balk en bevestigt u de koppelings bewerking. 

![De galerie met gedeelde afbeeldingen loskoppelen van het lab-account](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Volgende stappen
Zie de [Galerie met gedeelde afbeeldingen gebruiken](how-to-use-shared-image-gallery.md)voor meer informatie over het opslaan van een Lab-afbeelding in de galerie met gedeelde afbeeldingen of het gebruik van een afbeelding uit de galerie met gedeelde afbeeldingen om een virtuele machine te maken.

Zie [Galerie met gedeelde afbeeldingen](../../virtual-machines/windows/shared-image-galleries.md)voor meer informatie over gedeelde afbeeldings galerieën in het algemeen.
