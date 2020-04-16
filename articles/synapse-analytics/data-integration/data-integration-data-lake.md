---
title: Inname in Azure Data Lake Storage Gen2 in Azure Synapse Analytics
description: Meer informatie over het innemen van gegevens in Azure Data Lake Storage Gen2 in Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 4d7d7be523749797e5dbce0e50c307fc682974f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430576"
---
# <a name="ingesting-data-into-azure-data-lake-storage-gen2"></a>Gegevens opnemen in Azure Data Lake Storage Gen2 

In dit artikel leert u hoe u gegevens van de ene locatie naar de andere innemen in een Azure Data Lake Gen 2-opslagaccount (Azure Data Lake Gen 2) met Azure Synapse Analytics.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* **Azure Storage-account**: U gebruikt Azure Data Lake Gen 2 als *brongegevensarchief.* Zie [Een Azure Storage-account maken](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor stappen om er een te maken als u geen opslagaccount hebt.

## <a name="create-linked-services"></a>Gekoppelde services maken

In Azure Synapse Analytics definieert u in Azure Synapse Analytics uw verbindingsgegevens met andere services. In deze sectie voegt u Azure Synapse Analytics en Azure Data Lake Gen 2 toe als gekoppelde services.

1. Open de UX van Azure Synapse Analytics en ga naar het tabblad **Beheren.**
1. Selecteer **Linked Services**onder **Externe verbindingen**.
1. Als u een gekoppelde service wilt toevoegen, klikt u op **Nieuw**.
1. Selecteer de tegel Azure Data Lake Storage Gen2 in de lijst en klik op **Doorgaan**.
1. Voer uw verificatiereferenties in. Accountsleutel, serviceprincipal en beheerde identiteit worden momenteel ondersteunde verificatietypen. Klik op testverbinding om te controleren of uw referenties correct zijn. 
1. Klik **op Maken** als u klaar bent.

## <a name="create-pipeline"></a>Pijplijn maken

Een pijplijn bevat de logische stroom voor het uitvoeren van een reeks activiteiten. In deze sectie maakt u een pijplijn met een kopieeractiviteit die gegevens uit Azure Data Lake Gen 2 in neemt in een SQL-groep.

1. Ga naar het tabblad **Orkestratie.** Klik op het pluspictogram naast de koptekst pijplijn en selecteer **Pijplijn**.
1. Sleep **onder Verplaatsen en transformeren** in het deelvenster Activiteiten de gegevens **kopiëren** naar het pijplijncanvas.
1. Klik op de kopieeractiviteit en ga naar het tabblad **Bron.** Klik op **Nieuw** om een nieuwe brongegevensset te maken.
1. Selecteer Azure Data Lake Storage Gen2 als uw gegevensarchief en klik verder.
1. Selecteer DelimitedText als opmaak en klik op Doorgaan.
1. Selecteer in het deelvenster Seteigenschappen de ADLS-gekoppelde service die u hebt gemaakt. Geef het bestandspad van uw brongegevens op en geef op of de eerste rij een koptekst heeft. U het schema importeren uit het bestandsarchief of een voorbeeldbestand. Klik op OK als u klaar bent.
1. Ga naar het tabblad **Sink.** Klik op **Nieuw** om een nieuwe sinkset te maken.
1. Selecteer Azure Data Lake Storage gen2 als uw gegevensarchief en klik verder.
1. Selecteer DelimitedText als opmaak en klik op Doorgaan.
1. Selecteer in het deelvenster Seteigenschappen de ADLS-gekoppelde service die u hebt gemaakt. Geef het pad op van de map waar u gegevens wilt schrijven. Klik op OK als u klaar bent.

## <a name="debug-and-publish-pipeline"></a>Pijplijn opsporen en publiceren

Zodra u klaar bent met het configureren van uw pijplijn, u een foutopsporingsrun uitvoeren voordat u uw artefacten publiceert om te controleren of alles correct is.

1. Selecteer **Fouten opsporen** om fouten op te sporen in de pijplijn. De status van de pijplijnuitvoering wordt weergegeven op het tabblad **Uitvoer** onder in het venster. 
1. Zodra de pijplijn succesvol kan worden uitgevoerd, selecteert u op de bovenste werkbalk **Alles publiceren**. Met deze actie worden entiteiten (gegevenssets en pijplijnen) die u hebt gemaakt, gepubliceerd voor de Synapse Analytics-service.
1. Wacht tot u het bericht **Publiceren gelukt** ziet. Als u meldingsberichten wilt zien, klikt u rechtsboven op de belknop. 


## <a name="trigger-and-monitor-the-pipeline"></a>De pijplijn activeren en bewaken

In deze stap activeert u handmatig de pijplijn die in de vorige stap is gepubliceerd. 

1. Selecteer **Trigger toevoegen** op de werkbalk en selecteer Nu **activeren**. Klik op de pagina **Pijplijnuitvoering** op **Voltooien**.  
1. Ga naar het tabblad **Monitor** in de linkerzijbalk. U ziet een pijplijn die wordt geactiveerd door een handmatige trigger. U kunt via de links in de kolom **Acties** details van de activiteiten bekijken en de pijplijn opnieuw uitvoeren.
1. Selecteer de link **Uitvoeringen van activiteit weergeven** in de kolom **Acties** om de activiteituitvoeringen te zien die zijn gekoppeld aan de pijplijnuitvoering. In dit voorbeeld is er slechts één activiteit, dus u ziet slechts één vermelding in de lijst. Selecteer de link **Details** (pictogram van een bril) in de kolom **Acties** om details over de kopieerbewerking te zien. Selecteer **Pijplijnuitvoeringen** bovenaan om terug te gaan naar de weergave Pijplijnuitvoeringen. Selecteer **Vernieuwen** om de weergave te vernieuwen.
1. Controleer of uw gegevens correct zijn geschreven in de SQL-groep.


## <a name="next-steps"></a>Volgende stappen

Zie de [ingesting-gegevens in een SQL-poolartikel](data-integration-sql-pool.md) voor meer informatie over gegevensintegratie voor Synapse Analytics.
