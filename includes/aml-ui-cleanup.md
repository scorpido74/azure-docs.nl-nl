---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/16/2019
ms.openlocfilehash: 0071b0df2c2e173eced1722372f88b1de2708afa
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692428"
---
>[!IMPORTANT]
>U kunt de resources die u hebt gemaakt als vereisten voor andere Azure Machine Learning service-zelf studies en procedures voor artikelen gebruiken.

### <a name="delete-everything"></a>Alles verwijderen

Als u niet van plan bent om iets te gebruiken dat u hebt gemaakt, verwijdert u de hele resource groep, zodat u geen kosten meer hebt:

1. Selecteer in de Azure Portal **resource groepen** aan de linkerkant van het venster.
 
   ![Een resourcegroep verwijderen in de Azure-portal](./media/aml-ui-cleanup/delete-resources.png)

1. Selecteer in de lijst de resource groep die u hebt gemaakt.

1. Selecteer de knop met het weglatings teken ( **...** ) aan de rechter kant van het venster.

1. Selecteer **Resourcegroep verwijderen**.

Als u de resource groep verwijdert, worden ook alle resources verwijderd die u hebt gemaakt in de visuele interface.  

### <a name="delete-only-the-compute-target"></a>Alleen het reken doel verwijderen

Het reken doel dat u hier hebt gemaakt, wordt *automatisch geschaald* naar nul knoop punten wanneer het niet wordt gebruikt. Dit is om de kosten te minimaliseren. Als u het berekenings doel wilt verwijderen, voert u de volgende stappen uit:

1. Open uw werk ruimte in de [Azure Portal](https://portal.azure.com).

    ![Het reken doel verwijderen](./media/aml-ui-cleanup/delete-compute-target.png)

1. Selecteer de resource in het gedeelte **berekenen** van uw werk ruimte.

1. Selecteer **Verwijderen**.

### <a name="delete-individual-assets"></a>Afzonderlijke assets verwijderen

In de visuele interface waar u uw experiment hebt gemaakt, verwijdert u afzonderlijke assets door ze te selecteren en vervolgens de knop **verwijderen** te selecteren. U kunt de registratie van gegevens sets ongedaan maken in uw werk ruimte door elke gegevensset te selecteren en **registratie verwijderen**te selecteren.

![Assets verwijderen](./media/aml-ui-cleanup/delete-asset.png)
