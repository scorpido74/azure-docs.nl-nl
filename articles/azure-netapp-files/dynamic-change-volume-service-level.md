---
title: Het service niveau van een volume voor Azure NetApp Files dynamisch wijzigen | Microsoft Docs
description: Hierin wordt beschreven hoe u het service niveau van een volume dynamisch kunt wijzigen.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/12/2020
ms.author: b-juche
ms.openlocfilehash: e5219e1c87221ade8da68c21209f41b4d6139be2
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579076"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Het serviceniveau van een volume dynamisch wijzigen

> [!IMPORTANT] 
> * De registratie van de open bare Preview voor deze functie is in de wacht stand gezet tot verdere kennisgeving. 
> * Dynamisch wijzigen van het service niveau van een replicatie doel volume wordt momenteel niet ondersteund.

U kunt het service niveau van een bestaand volume wijzigen door het volume te verplaatsen naar een andere capaciteits groep die gebruikmaakt van het gewenste [service niveau](azure-netapp-files-service-levels.md) voor het volume. Voor deze in-place wijziging van het serviceniveau voor het volume is niet vereist dat u gegevens migreert. Het heeft ook geen invloed op de toegang tot het volume.  

Met deze functionaliteit kunt u op aanvraag voldoen aan de behoeften van uw werk belasting.  Als u de prestaties wilt verbeteren, kunt u een bestaand volume zodanig wijzigen dat het een hoger serviceniveau kan gebruiken. Als u de kosten wilt optimaliseren, kunt u het volume aanpassen om een lager serviceniveau te gebruiken. Als het volume zich op dit moment bevindt in een capaciteits groep die gebruikmaakt van het *standaard* service niveau en u wilt dat het volume het *Premium* -service niveau gebruikt, kunt u het volume dynamisch verplaatsen naar een capaciteits groep die gebruikmaakt van het *Premium* -service niveau.  

De capaciteits groep waarnaar u het volume wilt verplaatsen, moet al bestaan. De capaciteits pool kan andere volumes bevatten.  Als u het volume wilt verplaatsen naar een merk-nieuwe capaciteits groep, moet u [de capaciteits groep maken](azure-netapp-files-set-up-capacity-pool.md) voordat u het volume verplaatst.  

## <a name="considerations"></a>Overwegingen

* Nadat het volume is verplaatst naar een andere capaciteits groep, hebt u geen toegang meer tot de vorige volume activiteiten logboeken en de metrische gegevens van het volume. Het volume wordt gestart met nieuwe activiteiten logboeken en metrische gegevens onder de nieuwe capaciteits groep.

* Als u een volume verplaatst naar een capaciteits groep van een hoger service niveau (bijvoorbeeld van *Standard* naar *Premium* of *Ultra* service niveau), moet u Mini maal zeven dagen wachten voordat u dat volume *opnieuw* kunt verplaatsen naar een capaciteits groep van een lager service niveau (bijvoorbeeld verplaatsen van *Ultra* naar *Premium* of *Standard* ).  
<!-- 
## Register the feature

The feature to move a volume to another capacity pool is currently in preview. If you are using this feature for the first time, you need to register the feature first.

1. Register the feature: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Check the status of the feature registration: 

    > [!NOTE]
    > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is **Registered** before continuing.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
--> 
## <a name="move-a-volume-to-another-capacity-pool"></a>Een volume verplaatsen naar een andere capaciteits groep

1.  Klik op de pagina volumes met de rechter muisknop op het volume waarvan u het service niveau wilt wijzigen. Selecteer **groep wijzigen**.

    ![Klik met de rechter muisknop op volume](../media/azure-netapp-files/right-click-volume.png)

2. Selecteer in het venster groep wijzigen de capaciteits groep waarnaar u het volume wilt verplaatsen. 

    ![Groep wijzigen](../media/azure-netapp-files/change-pool.png)

3.  Klik op **OK**.


## <a name="next-steps"></a>Volgende stappen  

* [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
