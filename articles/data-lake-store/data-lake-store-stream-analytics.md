---
title: Gegevens van Stream Analytics streamen naar Azure Data Lake Storage Gen1 | Microsoft Documenten
description: Azure Stream Analytics gebruiken om gegevens te streamen naar Azure Data Lake Storage Gen1
services: data-lake-store,stream-analytics
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: d3dbacd58b3bda3fbf8ee8ad5f175eccc2cb2a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60194852"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Gegevens van Azure Storage Blob streamen naar Azure Data Lake Storage Gen1 met Azure Stream Analytics
In dit artikel leert u hoe u Azure Data Lake Storage Gen1 gebruiken als uitvoer voor een Azure Stream Analytics-taak. In dit artikel wordt een eenvoudig scenario weergegeven dat gegevens uit een Azure Storage-blob (invoer) leest en de gegevens naar Data Lake Storage Gen1 (uitvoer) schrijft.

## <a name="prerequisites"></a>Vereisten
Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Storage-account**. U gebruikt een blobcontainer uit dit account om gegevens in te voeren voor een Stream Analytics-taak. Ga er voor deze zelfstudie van uit dat u een opslagaccount hebt dat **storageforasa** wordt genoemd en een container binnen het account dat **storageforasacontainer**wordt genoemd. Zodra u de container hebt gemaakt, uploadt u er een voorbeeldgegevensbestand naar. 
  
* **Een Data Lake Storage Gen1-account**. Volg de instructies bij [Aan de slag met Azure Data Lake Storage Gen1 met behulp van de Azure Portal.](data-lake-store-get-started-portal.md) Laten we aannemen dat u een Data Lake Storage Gen1-account hebt genaamd **myadlsg1.** 

## <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken
U begint met het maken van een Stream Analytics-taak met een invoerbron en een uitvoerbestemming. Voor deze zelfstudie is de bron een Azure blob-container en de bestemming is Data Lake Storage Gen1.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

2. Klik in het linkerdeelvenster op **Taken van Stream analytics**en klik vervolgens op **Toevoegen**.

    ![Een streamanalysetaak maken](./media/data-lake-store-stream-analytics/create.job.png "Een Stream Analytics-taak maken")

    > [!NOTE]
    > Zorg ervoor dat u een taak maakt in dezelfde regio als het opslagaccount, anders brengt u extra kosten met zich mee voor het verplaatsen van gegevens tussen regio's.
    >

## <a name="create-a-blob-input-for-the-job"></a>Een Blob-invoer voor de taak maken

1. Open de pagina voor de taak Stream Analytics, klik in het linkerdeelvenster op het tabblad **Invoer** en klik vervolgens op **Toevoegen**.

    ![Een invoer toevoegen aan uw taak](./media/data-lake-store-stream-analytics/create.input.1.png "Een invoer toevoegen aan uw taak")

2. Geef op het **nieuwe invoerblad** de volgende waarden op.

    ![Een invoer toevoegen aan uw taak](./media/data-lake-store-stream-analytics/create.input.2.png "Een invoer toevoegen aan uw taak")

   * Voer **voor Invoeralias**een unieke naam in voor de taakinvoer.
   * Selecteer **Gegevensstroom**voor **Brontype**.
   * Selecteer **Blob-opslag**voor **Bron**.
   * Selecteer **Blob-opslag gebruiken in het huidige abonnement**voor **Abonnement**.
   * Selecteer **voor opslagaccount**het opslagaccount dat u hebt gemaakt als onderdeel van de vereisten. 
   * Selecteer **voor Container**de container die u hebt gemaakt in het geselecteerde opslagaccount.
   * Selecteer **CSV**voor **de indeling voor eventserialisatie**.
   * Selecteer **tabblad**voor **scheidingsteken**.
   * Selecteer **UTF-8**voor **codering**.

     Klik **op Maken**. Het portaal voegt nu de invoer toe en test de verbinding ermee.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Een Data Lake Storage Gen1-uitvoer voor de taak maken

1. Open de pagina voor de taak Stream Analytics, klik op het tabblad **Uitvoer,** klik op **Toevoegen**en selecteer **Data Lake Storage Gen1**.

    ![Een uitvoer toevoegen aan uw taak](./media/data-lake-store-stream-analytics/create.output.1.png "Een uitvoer toevoegen aan uw taak")

2. Geef op het **nieuwe uitvoerblad** de volgende waarden op.

    ![Een uitvoer toevoegen aan uw taak](./media/data-lake-store-stream-analytics/create.output.2.png "Een uitvoer toevoegen aan uw taak")

    * Voer **voor Uitvoeralias**een unieke naam in voor de taakuitvoer. Dit is een vriendelijke naam die wordt gebruikt in query's om de queryuitvoer naar dit Data Lake Storage Gen1-account te leiden.
    * U wordt gevraagd om toegang te verlenen tot het Data Lake Storage Gen1-account. Klik **op Autoriseren**.

3. Blijf op het **nieuwe uitvoerblad** de volgende waarden opgeven.

    ![Een uitvoer toevoegen aan uw taak](./media/data-lake-store-stream-analytics/create.output.3.png "Een uitvoer toevoegen aan uw taak")

   * Selecteer **voor Accountnaam**het Data Lake Storage Gen1-account dat u al hebt gemaakt waar u de taakuitvoer naartoe wilt sturen.
   * Voer **voor padvoorvoegingspatroon**een bestandspad in dat wordt gebruikt om uw bestanden te schrijven binnen het opgegeven Data Lake Storage Gen1-account.
   * Als u voor **Datumnotatie**een datumtoken in het voorvoegselpad hebt gebruikt, u de datumnotatie selecteren waarin uw bestanden zijn ingedeeld.
   * Als u voor **tijdnotatie**een tijdstokteken in het voorvoegselpad hebt gebruikt, geeft u de tijdindeling op waarin uw bestanden zijn ingedeeld.
   * Selecteer **CSV**voor **de indeling voor eventserialisatie**.
   * Selecteer **tabblad**voor **scheidingsteken**.
   * Selecteer **UTF-8**voor **codering**.
    
     Klik **op Maken**. Het portaal voegt nu de uitvoer toe en test de verbinding eraan.
    
## <a name="run-the-stream-analytics-job"></a>Voer de Stream Analytics-taak uit

1. Als u een streamanalytics-taak wilt uitvoeren, moet u een query uitvoeren op het tabblad **Query.** Voor deze zelfstudie u de voorbeeldquery uitvoeren door de tijdelijke aanduidingen te vervangen door de taakinvoer- en uitvoeraliassen, zoals weergegeven in de onderstaande schermopname.

    ![Query uitvoeren](./media/data-lake-store-stream-analytics/run.query.png "Query uitvoeren")

2. Klik boven aan het scherm **op Opslaan** en klik vervolgens op het tabblad **Overzicht** op **Start**. Selecteer in het dialoogvenster **Aangepaste tijd**en stel de huidige datum en tijd in.

    ![Jobtijd instellen](./media/data-lake-store-stream-analytics/run.query.2.png "Jobtijd instellen")

    Klik **op Start** om de taak te starten. Het kan tot een paar minuten duren om de klus te klaren.

3. Als u de taak wilt activeren om de gegevens uit de blob te kiezen, kopieert u een voorbeeldgegevensbestand naar de blobcontainer. U een voorbeeldgegevensbestand ophalen uit de [Azure Data Lake Git Repository.](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt) Voor deze zelfstudie kopiëren we het bestand **vehicle1_09142014.csv**. U verschillende clients, zoals [Azure Storage Explorer,](https://storageexplorer.com/)gebruiken om gegevens naar een blobcontainer te uploaden.

4. Bekijk op het tabblad **Overzicht** onder **Controleren**hoe de gegevens zijn verwerkt.

    ![Taak controleren](./media/data-lake-store-stream-analytics/run.query.3.png "Taak controleren")

5. Ten slotte u controleren of de taakuitvoergegevens beschikbaar zijn in het Data Lake Storage Gen1-account. 

    ![Uitvoer controleren](./media/data-lake-store-stream-analytics/run.query.4.png "Uitvoer controleren")

    Houd in het deelvenster Gegevensverkenner de melding dat de uitvoer wordt geschreven naar een`streamanalytics/job/output/{date}/{time}`mappad zoals opgegeven in de uitvoerinstellingen van Data Lake Storage Gen1 ( ).  

## <a name="see-also"></a>Zie ook
* [Een HDInsight-cluster maken om Data Lake Storage Gen1 te gebruiken](data-lake-store-hdinsight-hadoop-use-portal.md)
