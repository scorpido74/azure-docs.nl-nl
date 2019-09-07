---
title: Een galerie met gedeelde afbeeldingen gebruiken in Azure Lab Services | Microsoft Docs
description: Informatie over het configureren van een Lab-account voor het gebruik van een galerie met gedeelde afbeeldingen zodat een gebruiker een installatie kopie kan delen met andere en een andere gebruiker kan de installatie kopie gebruiken om een sjabloon-VM in het lab te maken.
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
ms.openlocfilehash: 65cc2d9ac2b96822f2c1b740f3180ba1d9eaf98c
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389983"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Een galerie met gedeelde afbeeldingen gebruiken in Azure Lab Services
In dit artikel wordt uitgelegd hoe docenten/Lab-beheerder een installatie kopie van een virtuele machine van een sjabloon kan opslaan, zodat deze door anderen opnieuw kan worden gebruikt. Deze installatie kopieën worden opgeslagen in een [Galerie met gedeelde installatie kopieën](../../virtual-machines/windows/shared-image-galleries.md)van Azure. Als eerste stap koppelt de testlab-beheerder een bestaande galerie met gedeelde afbeeldingen aan het lab-account. Zodra de galerie met gedeelde installatie kopieën is gekoppeld, kunnen Labs die in het lab-account is gemaakt, afbeeldingen opslaan in de galerie met gedeelde afbeeldingen. Andere docenten kunnen deze afbeelding selecteren in de galerie met gedeelde afbeeldingen om een sjabloon voor hun klassen te maken. 

## <a name="prerequisites"></a>Vereisten
- Maak een galerie met gedeelde installatie kopieën met behulp van [Azure PowerShell](../../virtual-machines/windows/shared-images.md) of [Azure cli](../../virtual-machines/linux/shared-images.md).
- U hebt de galerie met gedeelde afbeeldingen aan het lab-account gekoppeld. Zie de [Galerie gedeelde installatie kopieën koppelen of ontkoppelen](how-to-attach-detach-shared-image-gallery.md)voor stapsgewijze instructies.


## <a name="save-an-image-to-the-shared-image-gallery"></a>Een afbeelding opslaan in de galerie met gedeelde afbeeldingen
Nadat een galerie met gedeelde installatie kopieën is gekoppeld, kan een Lab-account beheerder of een docent een afbeelding opslaan in de galerie met gedeelde afbeeldingen, zodat deze door andere docenten opnieuw kan worden gebruikt. 

1. Op de start pagina van het lab selecteert u **afbeelding opslaan** op de tegel in de **sjabloon** sectie.

    ![Knop Afbeelding opslaan](../media/how-to-use-shared-image-gallery/save-image-button.png)
2.  Voer in het venster **installatie kopie van virtuele machine opslaan** een naam in voor de installatie kopie en selecteer **Opslaan**. 

    ![Venster installatie kopie van virtuele machine opslaan](../media/how-to-use-shared-image-gallery/save-virtual-machine-image.png)
3. Controleer de status van de Lab-tegel. 

    ![Status van de bewerking voor het opslaan van de afbeelding](../media/how-to-use-shared-image-gallery/save-image-status.png)
4. Controleer of de bewerking is gelukt.

    ![De bewerking voor het opslaan van de afbeelding is voltooid](../media/how-to-use-shared-image-gallery/save-image-successful.png)

U kunt ook een installatie kopie uploaden naar de galerie met gedeelde afbeeldingen buiten de context van een lab. Zie overzicht van de [Galerie met gedeelde afbeeldingen](../../virtual-machines/windows/shared-images.md)voor meer informatie. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Een installatie kopie uit de galerie met gedeelde afbeeldingen gebruiken
Een docent/docent kan een aangepaste afbeelding kiezen die beschikbaar is in de galerie met gedeelde afbeeldingen voor de sjabloon tijdens het maken van een nieuwe lab.

![Installatie kopie van virtuele machine uit de galerie gebruiken](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Volgende stappen
Zie [Galerie met gedeelde afbeeldingen](../../virtual-machines/windows/shared-image-galleries.md)voor meer informatie over gedeelde afbeeldings galerieën.
