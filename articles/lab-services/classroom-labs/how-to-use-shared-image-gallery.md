---
title: Een gedeelde afbeeldingsgalerie gebruiken in Azure Lab Services | Microsoft Documenten
description: Meer informatie over het configureren van een labaccount om een gedeelde afbeeldingsgalerie te gebruiken, zodat een gebruiker een afbeelding met een andere afbeelding kan delen en een andere gebruiker de afbeelding kan gebruiken om een sjabloon-VM in het lab te maken.
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
ms.openlocfilehash: c611ecdb5a2534f7368e533e3e19e6e3f96de57f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190446"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Een gedeelde afbeeldingsgalerie gebruiken in Azure Lab Services
In dit artikel ziet u hoe docenten/labbeheerders een virtuele sjabloonafbeelding kunnen opslaan om door anderen te kunnen worden hergebruikt. Deze afbeeldingen worden opgeslagen in een [azure-galerie met gedeelde afbeeldingen](../../virtual-machines/windows/shared-image-galleries.md). Als eerste stap koppelt de labbeheerder een bestaande gedeelde afbeeldingsgalerie aan het labaccount. Zodra de gedeelde afbeeldingsgalerie is gekoppeld, kunnen laboratoria die in het labaccount zijn gemaakt, afbeeldingen opslaan in de gedeelde afbeeldingsgalerie. Andere docenten kunnen deze afbeelding selecteren in de gedeelde afbeeldingsgalerie om een sjabloon voor hun klassen te maken. 

> [!NOTE]
> Momenteel ondersteunt Azure Lab Services het maken van sjabloonVM's op basis van alleen **gegeneraliseerde** VM-afbeeldingen (geen gespecialiseerde afbeeldingen) in een gedeelde afbeeldingsgalerie. 

## <a name="prerequisites"></a>Vereisten
- Maak een gedeelde afbeeldingsgalerie met [Azure PowerShell](../../virtual-machines/windows/shared-images.md) of [Azure CLI](../../virtual-machines/linux/shared-images.md).
- U hebt de gedeelde afbeeldingsgalerie aan het labaccount gekoppeld. Zie [Gedeelde afbeeldingsgalerie toevoegen of loskoppelen](how-to-attach-detach-shared-image-gallery.md)voor stapsgewijze instructies.


## <a name="save-an-image-to-the-shared-image-gallery"></a>Een afbeelding opslaan in de gedeelde afbeeldingsgalerie
Nadat een gedeelde afbeeldingsgalerie is gekoppeld, kan een labaccountbeheerder of een docent een afbeelding opslaan in de gedeelde afbeeldingsgalerie, zodat deze door andere docenten kan worden hergebruikt. 

1. Selecteer op de pagina **Sjabloon** voor het lab de optie **Exporteren naar gedeelde afbeeldingsgalerie** op de werkbalk.

    ![Knop Afbeelding opslaan](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Voer in het dialoogvenster **Exporteren naar gedeelde afbeeldingsgalerie** een **naam voor de afbeelding**in en selecteer **Vervolgens Exporteren**. 

    ![Dialoogvenster Exporteren naar gedeelde afbeeldingsgalerie](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. U de voortgang van deze bewerking zien op de **sjabloonpagina.** Deze operatie kan een keer duren. 

    ![Export in uitvoering](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Wanneer de exportbewerking is geslaagd, ziet u het volgende bericht:

    ![Export voltooid](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

U ook een afbeelding uploaden naar de gedeelde afbeeldingsgalerie buiten de context van een lab. Zie [Overzicht van gedeelde afbeeldingsgalerie voor](../../virtual-machines/windows/shared-images.md)meer informatie . 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Een afbeelding gebruiken in de gedeelde afbeeldingsgalerie
Een docent/professor kan een aangepaste afbeelding kiezen die beschikbaar is in de gedeelde afbeeldingsgalerie voor de sjabloon tijdens het maken van nieuwe labs.

![Virtuele machineafbeelding gebruiken vanuit de galerie](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Volgende stappen
Zie [gedeelde afbeeldingsgalerie](../../virtual-machines/windows/shared-image-galleries.md)voor meer informatie over gedeelde galeries voor afbeeldingen.
