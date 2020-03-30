---
title: Een VM toevoegen met een gedeelde afbeelding in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het toevoegen van een virtuele machine (VM) met behulp van een afbeelding uit de bijgevoegde gedeelde afbeeldingsgalerie in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68775589"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Een vm toevoegen met een afbeelding uit de bijgevoegde gedeelde afbeeldingsgalerie
Met Azure DevTest Labs u een gedeelde afbeeldingsgalerie aan uw lab koppelen en vervolgens afbeeldingen in de galerie gebruiken als basis voor de VM's die u in het lab maakt. Zie [Gedeelde afbeeldingsgalerie configureren](configure-shared-image-gallery.md)voor meer informatie over het koppelen van een gedeelde afbeeldingsgalerie aan uw lab. In dit artikel ziet u hoe u een vm aan uw lab toevoegt met behulp van een afbeelding uit de bijgevoegde gedeelde afbeeldingsgalerie als basis. 

## <a name="azure-portal"></a>Azure Portal
In deze sectie leert u hoe u de Azure-portal gebruiken om een VM aan uw lab toe te voegen op basis van een afbeelding uit de bijgevoegde gedeelde afbeeldingsgalerie. In deze sectie worden geen gedetailleerde stapsgewijze instructies gegeven voor het maken van een vm met behulp van de Azure-portal. Zie [Een VM - Azure-portal maken](devtest-lab-add-vm.md)voor deze details. Hiermee worden alleen de stappen weergegeven waarin u een afbeelding selecteert in de bijgevoegde gedeelde afbeeldingsgalerie en een versie van de afbeelding selecteren die u wilt gebruiken. 

Wanneer u een vm aan uw lab toevoegt, u een afbeelding selecteren in de bijgevoegde gedeelde afbeeldingsgalerie als basisafbeelding: 

![Een gedeelde afbeelding voor de basis selecteren](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Vervolgens u op het tabblad **Geavanceerde instellingen** van de pagina **Labresource maken** de versie van de afbeelding selecteren die u als basisafbeelding wilt gebruiken:

![Afbeeldingsversie selecteren](./media/add-vm-use-shared-image/select-version-shared-image.png)

U overschakelen naar het gebruik van een andere versie van de afbeelding nadat de virtuele machine is gemaakt. 

## <a name="resource-manager-template"></a>Resource Manager-sjabloon
Als u een Sjabloon Azure Resource Manager gebruikt om een virtuele machine te maken met behulp van een gedeelde afbeelding sgallery, geeft u een waarde op voor de **gedeelde ImageId** in de sectie **Eigenschappen.** Zie het volgende voorbeeld: 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

Zie Een virtuele machine maken met een voorbeeld [van een gedeelde afbeeldingsgalerij](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) in onze GitHub-opslagplaats voor een volledig voorbeeld van resourcebeheersjablonen. 

## <a name="rest-api"></a>REST API

1. Eerst moet u de id van de afbeelding in de gedeelde afbeeldingsgalerie krijgen. Een manier is om alle afbeeldingen in de bijgevoegde gedeelde afbeeldingsgalerij weer te geven met behulp van de volgende opdracht GET. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Roep de PUT-methode op virtuele machines door de id van de `properties.SharedImageId`gedeelde afbeelding door te geven die u van de vorige oproep hebt ontvangen, aan de .

## <a name="next-steps"></a>Volgende stappen
Zie [Gedeelde afbeeldingsgalerie configureren](configure-shared-image-gallery.md)voor meer informatie over het koppelen van een gedeelde afbeeldingsgalerie aan een lab en deze configureren.