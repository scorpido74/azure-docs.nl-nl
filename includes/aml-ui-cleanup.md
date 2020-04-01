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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659866"
---
>[!IMPORTANT]
>U de resources die u hebt gemaakt gebruiken als vereisten voor andere Azure Machine Learning-zelfstudies en instructieartikelen.

### <a name="delete-everything"></a>Alles verwijderen

Als u niet van plan bent iets te gebruiken dat u hebt gemaakt, verwijdert u de hele brongroep, zodat u geen kosten in rekening brengt.

1. Selecteer **resourcegroepen** aan de linkerkant van het venster in de Azure-portal.
 
   ![Een resourcegroep verwijderen in de Azure-portal](./media/aml-ui-cleanup/delete-resources.png)

1. Selecteer in de lijst de resourcegroep die u hebt gemaakt.

1. Selecteer **Resourcegroep verwijderen**.

Als u de brongroep verwijdert, worden ook alle resources verwijderd die u in de ontwerper hebt gemaakt. 

### <a name="delete-individual-assets"></a>Afzonderlijke elementen verwijderen

Verwijder afzonderlijke elementen in de ontwerper waar u uw experiment hebt gemaakt door ze te selecteren en vervolgens de knop **Verwijderen te** selecteren.

Het rekendoel dat u hier hebt *gemaakt, wordt automatisch automatisch geschaald* naar nulknooppunten wanneer het niet wordt gebruikt. Deze actie wordt ondernomen om de kosten te minimaliseren.Als u het rekendoel wilt verwijderen, neemt u de volgende stappen uit:

![Elementen verwijderen](./media/aml-ui-cleanup/delete-asset.png)

U gegevenssets uituw halen uit uw werkruimte door elke gegevensset te selecteren en **Het registreren van u uit te**schakelen.

![Gegevensset uitschrijven](./media/aml-ui-cleanup/unregister-dataset1225.png)

Als u een gegevensset wilt verwijderen, gaat u naar het opslagaccount met behulp van de Azure-portal of Azure Storage Explorer en verwijdert u deze elementen handmatig.


