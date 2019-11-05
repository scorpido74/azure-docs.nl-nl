---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/22/2019
ms.openlocfilehash: 5a66212122745d0f4426e48e9487e9d674cec53f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489982"
---
>[!IMPORTANT]
>U kunt de resources die u hebt gemaakt als vereisten voor andere Azure Machine Learning zelf studies en artikelen met instructies gebruiken.

### <a name="delete-everything"></a>Alles verwijderen

Als u niet van plan bent om iets te gebruiken dat u hebt gemaakt, verwijdert u de hele resource groep, zodat u geen kosten meer hebt:

1. Selecteer in de Azure Portal **resource groepen** aan de linkerkant van het venster.
 
   ![Een resourcegroep verwijderen in de Azure-portal](./media/aml-ui-cleanup/delete-resources.png)

1. Selecteer in de lijst de resource groep die u hebt gemaakt.

1. Selecteer de knop met het weglatings teken ( **...** ) aan de rechter kant van het venster.

1. Selecteer **Resourcegroep verwijderen**.

Als u de resource groep verwijdert, worden ook alle resources die u in de ontwerp functie hebt gemaakt, verwijderd.  

### <a name="delete-only-the-compute-target"></a>Alleen het reken doel verwijderen

Het reken doel dat u hier hebt gemaakt, wordt *automatisch geschaald* naar nul knoop punten wanneer het niet wordt gebruikt. Dit is om de kosten te minimaliseren. Als u het berekenings doel wilt verwijderen, voert u de volgende stappen uit:

1. Open uw werk ruimte in de [Azure Portal](https://portal.azure.com).

    ![Het reken doel verwijderen](./media/aml-ui-cleanup/delete-compute-target.png)

1. Selecteer de resource in het gedeelte **berekenen** van uw werk ruimte.

1. Selecteer **Verwijderen**.

### <a name="delete-individual-assets"></a>Afzonderlijke assets verwijderen

In de ontwerp functie waar u uw experiment hebt gemaakt, verwijdert u afzonderlijke assets door ze te selecteren en vervolgens de knop **verwijderen** te selecteren.

![Assets verwijderen](./media/aml-ui-cleanup/delete-asset.png)

U kunt de registratie van gegevens sets ongedaan maken in uw werk ruimte door elke gegevensset te selecteren en **registratie verwijderen**te selecteren.

![Registratie van gegevensset opheffen](./media/aml-ui-cleanup/unregister-dataset.png)


