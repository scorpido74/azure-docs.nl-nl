---
title: Een aangepaste Azure DevTest Labs-afbeelding maken op basis van een VHD-bestand | Microsoft Documenten
description: Meer informatie over het maken van een aangepaste afbeelding in Azure DevTest Labs vanuit een VHD-bestand met behulp van de Azure-portal
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 853c138c8cf73b41b0cebb6c1d349865e18eab6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61295949"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Een aangepaste afbeelding maken op basis van een VHD-bestand

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

In de volgende stappen u een aangepaste afbeelding maken van een VHD-bestand met behulp van de Azure-portal:

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met laboratoria het gewenste lab.  

1. Selecteer **Configuratie en beleid**in het hoofdvenster van het lab. 

1. Selecteer **aangepaste afbeeldingen**in het deelvenster Configuratie **en beleid** .

1. Selecteer in het deelvenster **Aangepaste afbeeldingen** de optie **+Toevoegen**.

    ![Aangepaste afbeelding toevoegen](./media/devtest-lab-create-template/add-custom-image.png)

1. Voer de naam van de aangepaste afbeelding in. Deze naam wordt weergegeven in de lijst met basisafbeeldingen bij het maken van een vm.

1. Voer de beschrijving van de aangepaste afbeelding in. Deze beschrijving wordt weergegeven in de lijst met basisafbeeldingen bij het maken van een vm.

1. Selecteer **Windows** of **Linux**voor het **type BE.**

    - Als u **Windows**inschakelt, geeft u via het selectievakje op of *sysprep* op de machine is uitgevoerd. 
    - Als u **Linux**inschakelt, geeft u via het selectievakje op of *deprovision* op de machine is uitgevoerd. 

1. Selecteer een **VHD** in het vervolgkeuzemenu. Dit is de VHD die zal worden gebruikt om de nieuwe aangepaste afbeelding te maken. Selecteer indien nodig om een VHD te **uploaden met PowerShell**.

1. U ook een plannaam invoeren, aanbieding plannen en uitgever plannen als de afbeelding die wordt gebruikt om de aangepaste afbeelding te maken, geen gelicentieerde afbeelding is (gepubliceerd door Microsoft).

   - **Naam van het plan:** Voer de naam in van de Marketplace-afbeelding (SKU) waaruit deze aangepaste afbeelding is gemaakt 
   - **Plan aanbieding:** Voer het product (aanbieding) in van de Marketplace-afbeelding waaruit deze aangepaste afbeelding is gemaakt 
   - **Uitgever van abonnement:** Voer de uitgever van de Marketplace-afbeelding in waaruit deze aangepaste afbeelding is gemaakt

   > [!NOTE]
   > Als de afbeelding die u gebruikt om een aangepaste afbeelding te maken **geen** gelicentieerde afbeelding is, zijn deze velden leeg en kunnen deze worden ingevuld als u dat wilt. Als de afbeelding een gelicentieerde afbeelding **is,** worden de velden automatisch gevuld met de plangegevens. Als u ze in dit geval probeert te wijzigen, wordt een waarschuwingsbericht weergegeven.
   >
   >

1. Selecteer **OK** om de aangepaste afbeelding te maken.

Na een paar minuten wordt de aangepaste afbeelding gemaakt en opgeslagen in het opslagaccount van het lab. Wanneer een labgebruiker een nieuwe vm wil maken, is de afbeelding beschikbaar in de lijst met basisafbeeldingen.

![Aangepaste afbeelding beschikbaar in lijst met basisafbeeldingen](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Gerelateerde blogberichten

- [Aangepaste afbeeldingen of formules?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Aangepaste afbeeldingen kopiëren tussen Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Volgende stappen

- [Een vm toevoegen aan uw lab](./devtest-lab-add-vm.md)
