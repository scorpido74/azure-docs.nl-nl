---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659866"
---
>[!IMPORTANT]
>U kunt de resources die u hebt gemaakt als vereisten voor andere Azure Machine Learning zelf studies en artikelen met instructies gebruiken.

### <a name="delete-everything"></a>Alles verwijderen

Als u niet van plan bent om iets te gebruiken dat u hebt gemaakt, verwijdert u de hele resource groep, zodat u geen kosten in rekening brengt.

1. Selecteer in de Azure Portal **resource groepen** aan de linkerkant van het venster.
 
   ![Een resourcegroep verwijderen in de Azure-portal](./media/aml-ui-cleanup/delete-resources.png)

1. Selecteer in de lijst de resource groep die u hebt gemaakt.

1. Selecteer **Resourcegroep verwijderen**.

Als u de resource groep verwijdert, worden ook alle resources die u in de ontwerp functie hebt gemaakt, verwijderd. 

### <a name="delete-individual-assets"></a>Afzonderlijke assets verwijderen

In de ontwerp functie waar u uw experiment hebt gemaakt, verwijdert u afzonderlijke assets door ze te selecteren en vervolgens de knop **verwijderen** te selecteren.

Het reken doel dat u hier hebt gemaakt, wordt *automatisch geschaald* naar nul knoop punten wanneer het niet wordt gebruikt. Deze actie wordt uitgevoerd om de kosten te minimaliseren. Als u het berekenings doel wilt verwijderen, voert u de volgende stappen uit:

![Assets verwijderen](./media/aml-ui-cleanup/delete-asset.png)

U kunt de registratie van gegevens sets opheffen vanuit uw werk ruimte door elke gegevensset te selecteren en **registratie verwijderen**te selecteren.

![Registratie van gegevensset opheffen](./media/aml-ui-cleanup/unregister-dataset1225.png)

Als u een gegevensset wilt verwijderen, gaat u naar het opslag account met behulp van de Azure Portal of Azure Storage Explorer en verwijdert u deze activa hand matig.


