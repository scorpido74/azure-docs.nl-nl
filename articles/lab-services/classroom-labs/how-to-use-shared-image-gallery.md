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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: f438c32deb7e923f08396b0580d807d6e5b5e69a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585030"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Een galerie met gedeelde afbeeldingen gebruiken in Azure Lab Services
In dit artikel wordt uitgelegd hoe docenten/Lab-beheerder een installatie kopie van een virtuele machine van een sjabloon kan opslaan, zodat deze door anderen opnieuw kan worden gebruikt. Deze installatie kopieën worden opgeslagen in een [Galerie met gedeelde installatie kopieën](../../virtual-machines/windows/shared-image-galleries.md)van Azure. Als eerste stap koppelt de testlab-beheerder een bestaande galerie met gedeelde afbeeldingen aan het lab-account. Zodra de galerie met gedeelde installatie kopieën is gekoppeld, kunnen Labs die in het lab-account is gemaakt, afbeeldingen opslaan in de galerie met gedeelde afbeeldingen. Andere docenten kunnen deze afbeelding selecteren in de galerie met gedeelde afbeeldingen om een sjabloon voor hun klassen te maken. 

## <a name="prerequisites"></a>Vereisten
- Maak een galerie met gedeelde installatie kopieën met behulp van [Azure PowerShell](../../virtual-machines/windows/shared-images.md) of [Azure cli](../../virtual-machines/linux/shared-images.md).
- U hebt de galerie met gedeelde afbeeldingen aan het lab-account gekoppeld. Zie de [Galerie gedeelde installatie kopieën koppelen of ontkoppelen](how-to-attach-detach-shared-image-gallery.md)voor stapsgewijze instructies.


## <a name="save-an-image-to-the-shared-image-gallery"></a>Een afbeelding opslaan in de galerie met gedeelde afbeeldingen
Nadat een galerie met gedeelde installatie kopieën is gekoppeld, kan een Lab-account beheerder of een docent een afbeelding opslaan in de galerie met gedeelde afbeeldingen, zodat deze door andere docenten opnieuw kan worden gebruikt. 

1. Op de **sjabloon** pagina voor het lab selecteert **u exporteren naar gedeelde installatie kopie galerie** op de werk balk.

    ![Knop Afbeelding opslaan](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Voer in het dialoog venster **exporteren naar gedeelde afbeeldingen galerie** een **naam in voor de installatie kopie**en selecteer vervolgens **exporteren**. 

    ![Dialoog venster exporteren naar de galerie gedeelde afbeeldingen](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. U kunt de voortgang van deze bewerking zien op de **sjabloon** pagina. Deze bewerking kan enige tijd duren. 

    ![Exporteren wordt uitgevoerd](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Wanneer de export bewerking is voltooid, wordt het volgende bericht weer gegeven:

    ![Exporteren is voltooid](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

U kunt ook een installatie kopie uploaden naar de galerie met gedeelde afbeeldingen buiten de context van een lab. Zie overzicht van de [Galerie met gedeelde afbeeldingen](../../virtual-machines/windows/shared-images.md)voor meer informatie. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Een installatie kopie uit de galerie met gedeelde afbeeldingen gebruiken
Een docent/docent kan een aangepaste afbeelding kiezen die beschikbaar is in de galerie met gedeelde afbeeldingen voor de sjabloon tijdens het maken van een nieuwe lab.

![Installatie kopie van virtuele machine uit de galerie gebruiken](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Volgende stappen
Zie [Galerie met gedeelde afbeeldingen](../../virtual-machines/windows/shared-image-galleries.md)voor meer informatie over gedeelde afbeeldings galerieën.
