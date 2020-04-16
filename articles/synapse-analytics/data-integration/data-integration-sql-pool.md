---
title: Inname in SQL-pool in Azure Synapse Analytics
description: Meer informatie over het innemen van gegevens in een SQL-groep in Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: fbd8e03b1f8af7802133c35ae4860116aaea0c3c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430563"
---
# <a name="ingesting-data-into-a-sql-pool"></a>Gegevens opnemen in een SQL-groep

In dit artikel leert u hoe u gegevens van een Azure Data Lake Gen 2-opslagaccount inneemt in een SQL-groep met Azure Synapse Analytics.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* **Azure-opslagaccount**: u gebruikt Azure Data Lake Storage Gen 2 als *brongegevensarchief.* Zie [Een Azure Storage-account maken](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor stappen om er een te maken als u geen opslagaccount hebt.
* **Azure Synapse Analytics**: U gebruikt een SQL-groep als *een sink* data store. Zie [Een SQL-groep maken](../../sql-database/sql-database-get-started-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor stappen om er een te maken als u geen Azure Synapse Analytics-exemplaar hebt.

## <a name="create-linked-services"></a>Gekoppelde services maken

In Azure Synapse Analytics definieert u in Azure Synapse Analytics uw verbindingsgegevens met andere services. In deze sectie voegt u een gekoppelde azure Synapse Analytics en Azure Data Lake Storage Gen2-service toe.

1. Open de UX van Azure Synapse Analytics en ga naar het tabblad **Beheren.**
1. Selecteer **Linked Services**onder **Externe verbindingen**.
1. Als u een gekoppelde service wilt toevoegen, klikt u op **Nieuw**.
1. Selecteer de tegel Azure Data Lake Storage Gen2 in de lijst en klik op **Doorgaan**.
1. Voer uw verificatiereferenties in. Accountsleutel, serviceprincipal en beheerde identiteit worden momenteel ondersteunde verificatietypen. Klik op testverbinding om te controleren of uw referenties correct zijn. Klik **op Maken** als u klaar bent.
1. Herhaal stap 3-5, maar in plaats van Azure Data Lake Storage Gen2 selecteert u de tegel Azure Synapse Analytics en voert u de bijbehorende verbindingsreferenties in. Voor Azure Synapse Analytics worden SQL-verificatie, beheerde identiteit en serviceprincipal momenteel ondersteund.

## <a name="create-pipeline"></a>Pijplijn maken

Een pijplijn bevat de logische stroom voor het uitvoeren van een reeks activiteiten. In deze sectie maakt u een pijplijn met een kopieeractiviteit die gegevens van ADLS gen 2 in neemt in een SQL-groep.

1. Ga naar het tabblad **Orkestratie.** Klik op het pluspictogram naast de koptekst pijplijn en selecteer **Pijplijn**.
1. Sleep **onder Verplaatsen en transformeren** in het deelvenster Activiteiten de gegevens **kopiëren** naar het pijplijncanvas.
1. Klik op de kopieeractiviteit en ga naar het tabblad **Bron.** Klik op **Nieuw** om een nieuwe brongegevensset te maken.
1. Selecteer Azure Data Lake Storage gen2 als uw gegevensarchief en klik verder.
1. Selecteer DelimitedText als opmaak en klik op Doorgaan.
1. Selecteer in het deelvenster Seteigenschappen de ADLS-gekoppelde service die u hebt gemaakt. Geef het bestandspad van uw brongegevens op en geef op of de eerste rij een koptekst heeft. U het schema importeren uit het bestandsarchief of een voorbeeldbestand. Klik op OK als u klaar bent.
1. Ga naar het tabblad **Sink.** Klik op **Nieuw** om een nieuwe sinkset te maken.
1. Selecteer Azure Synapse Analytics als uw gegevensarchief en klik verder.
1. Selecteer in het deelvenster Set-eigenschappen de gekoppelde Azure Synapse Analytics-service die u hebt gemaakt. Als u naar een bestaande tabel schrijft, selecteert u deze in de vervolgkeuzelijst. Anders controleert u **Bewerken** en voert u uw nieuwe tabelnaam in. Klik op OK als u klaar bent
1. Als u een tabel maakt, schakelt u **Tabel automatisch maken** in het veld tabeloptie in.

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

Zie het artikel [Gegevens opnemen in Azure Data Lake Storage Gen2](data-integration-data-lake.md) voor meer informatie over gegevensintegratie voor Synapse Analytics.
