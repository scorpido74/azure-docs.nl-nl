---
title: Een aangepaste azure devTest Labs-afbeelding maken op basis van een VM | Microsoft Documenten
description: Meer informatie over het maken van een aangepaste afbeelding in Azure DevTest Labs op basis van een ingerichte VM met behulp van de Azure-portal
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 07f3b60b9218f74bb3a778daa27f31687c4538b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60868450"
---
# <a name="create-a-custom-image-from-a-vm"></a>Een aangepaste installatiekopie maken vanaf een virtuele machine

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

U een aangepaste afbeelding maken van een ingerichte virtuele machine en die aangepaste afbeelding vervolgens gebruiken om identieke VM's te maken. In de volgende stappen wordt uitgelegd hoe u een aangepaste afbeelding maakt van een virtuele machine:

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met laboratoria het gewenste lab.  

1. Selecteer in het hoofdvenster van het lab **mijn virtuele machines**.
 
1. Selecteer in het deelvenster **Mijn virtuele machines** de VM waaruit u de aangepaste afbeelding wilt maken.

1. Selecteer in het beheervenster van de VM de optie **Aangepaste afbeelding maken (VHD).**

    ![Aangepast afbeeldingsmenu-item maken](./media/devtest-lab-create-template/create-custom-image.png)

1. Voer in het deelvenster **Aangepaste afbeelding** een naam en beschrijving in voor uw aangepaste afbeelding. Deze informatie wordt weergegeven in de lijst met basissen wanneer u een vm maakt. De aangepaste afbeelding bevat de OS-schijf en alle gegevensschijven die aan de virtuele machine zijn gekoppeld.

    ![Aangepast afbeeldingsvenster maken](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Selecteer of sysprep is uitgevoerd op de VM. Als de sysprep niet op de VM is uitgevoerd, geeft u op of u wilt dat sysprep op de VM wordt uitgevoerd wanneer de aangepaste afbeelding wordt gemaakt.

1. Selecteer **OK** wanneer u klaar bent om de aangepaste afbeelding te maken.

Na een paar minuten wordt de aangepaste afbeelding gemaakt en opgeslagen in het opslagaccount van het lab. Wanneer een labgebruiker een nieuwe vm wil maken, is de afbeelding beschikbaar in de lijst met basisafbeeldingen.

![Aangepaste afbeelding beschikbaar in lijst met basisafbeeldingen](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Gerelateerde blogberichten

- [Aangepaste afbeeldingen of formules?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Aangepaste afbeeldingen kopiëren tussen Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Volgende stappen

- [Een vm toevoegen aan uw lab](devtest-lab-add-vm.md)
