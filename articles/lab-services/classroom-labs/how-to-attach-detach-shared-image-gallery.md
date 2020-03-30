---
title: Een gedeelde afbeeldingsgalerie koppelen of loskoppelen in Azure Lab Services | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u een gedeelde afbeeldingsgalerie koppelt aan een klaslokaallab in Azure Lab Services.
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
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: 00dbef7b4453ffcb54020340bde51f55827759a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284315"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Een gedeelde afbeeldingsgalerie koppelen of loskoppelen in Azure Lab Services
Docenten/labbeheerders kunnen een vm-afbeelding van een sjabloon opslaan in een [azure-gedeelde afbeeldingsgalerie](../../virtual-machines/windows/shared-image-galleries.md) om deze door anderen opnieuw te kunnen gebruiken. Als eerste stap koppelt de labbeheerder een bestaande gedeelde afbeeldingsgalerie aan het labaccount. Zodra de gedeelde afbeeldingsgalerie is gekoppeld, kunnen laboratoria die in het labaccount zijn gemaakt, afbeeldingen opslaan in de gedeelde afbeeldingsgalerie. Andere docenten kunnen deze afbeelding selecteren in de gedeelde afbeeldingsgalerie om een sjabloon voor hun klassen te maken. 

Wanneer een afbeelding wordt opgeslagen in een gedeelde afbeeldingsgalerie, repliceert Azure Lab Services de opgeslagen afbeelding naar andere regio's die beschikbaar zijn in dezelfde [geografie.](https://azure.microsoft.com/global-infrastructure/geographies/) Het zorgt ervoor dat de afbeelding beschikbaar is voor laboratoria die zijn gemaakt in andere regio's in dezelfde geografie. Het opslaan van afbeeldingen in een gedeelde afbeeldingsgalerie brengt extra kosten met zich mee, waaronder kosten voor alle gerepliceerde afbeeldingen. Deze kosten staan los van de gebruikskosten van Azure Lab Services. Zie Gedeelde galerie voor afbeeldingen [– Facturering]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)voor meer informatie over de prijzen van gedeelde afbeeldingengalerij.

In dit artikel ziet u hoe u een gedeelde afbeeldingsgalerie aan een labaccount koppelt of loskoppelt. 

> [!NOTE]
> Momenteel ondersteunt Azure Lab Services het maken van sjabloonVM's op basis van alleen **gegeneraliseerde** VM-afbeeldingen (geen gespecialiseerde afbeeldingen) in een gedeelde afbeeldingsgalerie. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>Configureren op het moment van het maken van labaccount
Wanneer u een labaccount maakt, u een gedeelde afbeeldingsgalerie aan het labaccount koppelen. U een bestaande gedeelde afbeeldingsgalerie selecteren in de vervolgkeuzelijst of een nieuwe galerie maken. Als u een gedeelde afbeeldingsgalerie wilt maken en koppelen aan het labaccount, selecteert u **Nieuw maken,** voert u een naam voor de galerie in en voert **u OK**in. 

![Gedeelde afbeeldingsgalerie configureren op het moment van het maken van labaccount](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configureren nadat het labaccount is gemaakt
Nadat het labaccount is gemaakt, u de volgende taken uitvoeren:

- Een gedeelde afbeeldingsgalerie maken en eraan koppelen
- Een gedeelde afbeeldingsgalerie toevoegen aan het labaccount
- Een gedeelde afbeeldingsgalerie loskoppelen van het labaccount

## <a name="create-and-attach-a-shared-image-gallery"></a>Een gedeelde afbeeldingsgalerie maken en eraan koppelen
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services**. Selecteer **Lab Services** in de sectie **DEVOPS.** Als u ster`*`( ) naast **Lab Services**selecteert, wordt deze toegevoegd aan de sectie **FAVORIETEN** in het linkermenu. Vanaf de volgende keer selecteert u **Lab Services** onder **FAVORIETEN**.

    ![Alle services -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecteer uw labaccount om de pagina **Lab-account** te bekijken. 
4. Selecteer **Gedeelde afbeeldingsgalerie** in het linkermenu en selecteer **+ Maken** op de werkbalk.  

    ![Knop Gedeelde afbeeldingsgalerie maken](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Voer in het venster **Gedeelde afbeeldingsgalerie maken** een **naam** voor de galerie in en voer **OK**in. 

    ![Venster gedeelde afbeeldingsgalerie maken](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services maakt de gedeelde afbeeldingsgalerie en koppelt deze aan het labaccount. Alle labs die in dit labaccount zijn gemaakt, hebben toegang tot de bijgevoegde gedeelde afbeeldingsgalerie. 

    ![Bijgevoegde afbeeldingsgalerie](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    In het onderste deelvenster ziet u afbeeldingen in de gedeelde afbeeldingsgalerie. In deze nieuwe galerij zijn er geen afbeeldingen. Wanneer u afbeeldingen uploadt naar de galerie, ziet u ze op deze pagina.     

    Alle afbeeldingen in de bijgevoegde gedeelde afbeeldingsgalerie zijn standaard ingeschakeld. U geselecteerde afbeeldingen in- of uitschakelen door ze in de lijst te selecteren en geselecteerde **afbeeldingen inschakelen** of Geselecteerde **afbeeldingen uitschakelen.**

## <a name="attach-an-existing-shared-image-gallery"></a>Een bestaande gedeelde afbeeldingsgalerie toevoegen
In de volgende procedure ziet u hoe u een bestaande gedeelde afbeeldingsgalerie aan een labaccount koppelt. 

1. Selecteer op de pagina **Lab-account** de optie **Gedeelde afbeeldingsgalerie** in het linkermenu en selecteer **Bijvoegen** op de werkbalk. 

    ![Gedeelde afbeeldingsgalerie - knop Toevoegen](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Selecteer op de pagina **Een bestaande gedeelde afbeeldingsgalerie** de optie van uw gedeelde afbeeldingsgalerie en selecteer **OK**.

    ![Een bestaande galerie selecteren](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. U ziet het volgende scherm: 

    ![Mijn galerie in de lijst](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    In dit voorbeeld zijn er nog geen afbeeldingen in de gedeelde afbeeldingsgalerie.

    Azure Lab Services-identiteit wordt toegevoegd als bijdrager aan de gedeelde afbeeldingsgalerie die aan het lab is gekoppeld. Hiermee kunnen docenten/IT-beheerders afbeeldingen van virtuele machines opslaan in de gedeelde afbeeldingsgalerie. Alle labs die in dit labaccount zijn gemaakt, hebben toegang tot de bijgevoegde gedeelde afbeeldingsgalerie. 

    Alle afbeeldingen in de bijgevoegde gedeelde afbeeldingsgalerie zijn standaard ingeschakeld. U geselecteerde afbeeldingen in- of uitschakelen door ze in de lijst te selecteren en geselecteerde **afbeeldingen inschakelen** of Geselecteerde **afbeeldingen uitschakelen.** 

## <a name="detach-a-shared-image-gallery"></a>Een gedeelde afbeeldingsgalerie loskoppelen
Aan een lab kan slechts één gedeelde afbeeldingsgalerie worden gekoppeld. Als u een andere gedeelde afbeeldingsgalerie wilt koppelen, koppelt u de huidige afbeelding los voordat u de nieuwe afbeelding koppelt. Als u een gedeelde afbeeldingsgalerie los maakt van uw lab, selecteert u **Losmaken** op de werkbalk en bevestigt u de losmakende bewerking. 

![De gedeelde afbeeldingsgalerie loskoppelen van het labaccount](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Volgende stappen
Zie Hoe u een galerie met gedeelde afbeeldingen gebruiken voor meer informatie over het opslaan van een labafbeelding in de gedeelde afbeeldingsgalerie of een afbeelding uit de gedeelde afbeeldingsgalerie gebruiken om een virtuele afbeelding [te maken.](how-to-use-shared-image-gallery.md)

Zie [gedeelde afbeeldingsgalerie](../../virtual-machines/windows/shared-image-galleries.md)voor meer informatie over gedeelde galeries in het algemeen.
