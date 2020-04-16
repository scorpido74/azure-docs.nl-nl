---
title: Gegevens laden in Azure Data Lake Storage Gen2
description: Azure Data Factory gebruiken om gegevens te kopiëren naar Azure Data Lake Storage Gen2
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: 96674f059e9cbc21c5c8c64eff8c94c810c4aa32
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417773"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Gegevens laden in Azure Data Lake Storage Gen2 met Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Lake Storage Gen2 is een reeks mogelijkheden die zijn toegelegd op big data-analyses, ingebouwd in [Azure Blob-opslag.](../storage/blobs/storage-blobs-introduction.md) Hiermee u communiceren met uw gegevens met behulp van zowel bestandssysteem en object opslag paradigma's.

Azure Data Factory (ADF) is een volledig beheerde cloudgebaseerde data-integratieservice. U de service gebruiken om het meer te vullen met gegevens uit een uitgebreide reeks on-premises en cloudgebaseerde gegevensopslag en tijd te besparen bij het bouwen van uw analyseoplossingen. Zie de tabel [met ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een gedetailleerde lijst met ondersteunde connectors.

Azure Data Factory biedt een scale-out, beheerde oplossing voor gegevensverplaatsing. Dankzij de scale-out architectuur van ADF kan het gegevens opnemen bij een hoge doorvoer. Zie [Activiteitsprestaties kopiëren](copy-activity-performance.md)voor meer informatie .

In dit artikel ziet u hoe u het hulpprogramma Gegevensfabriekskopiegegevens gebruiken om gegevens van _de Amazon Web Services S3-service_ te laden in Azure Data Lake Storage _Gen2._ U vergelijkbare stappen uitvoeren om gegevens uit andere soorten gegevensarchieven te kopiëren.

>[!TIP]
>Raadpleeg [deze specifieke walkthrough](load-azure-data-lake-storage-gen2-from-gen1.md)voor het kopiëren van gegevens uit Azure Data Lake Storage Gen1 naar Gen2.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Azure Storage-account met Data Lake Storage Gen2 ingeschakeld: als u geen opslagaccount hebt, [maakt u een account](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* AWS-account met een S3-bucket die gegevens bevat: In dit artikel wordt weergegeven hoe u gegevens van Amazon S3 kopiëren. U andere gegevensarchieven gebruiken door vergelijkbare stappen te volgen.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer links in het menu **Een brongegevens** > **maken + Analytics** > **Data Factory:**
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geef op de pagina **Nieuwe gegevensfabriek** waarden op voor de velden die in de volgende afbeelding worden weergegeven: 
      
   ![Pagina Nieuwe gegevensfactory](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Naam:** Voer een wereldwijd unieke naam in voor uw Azure-gegevensfabriek. Als u de fout 'Gegevensfabrieksnaam \"LoadADLSDemo\" is niet beschikbaar' ontvangt, voert u een andere naam in voor de gegevensfabriek. U bijvoorbeeld de naam _**yourname**_**ADFTutorialDataFactory**gebruiken. Probeer de gegevensfabriek opnieuw te maken. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    * **Abonnement**: Selecteer uw Azure-abonnement waarin u de gegevensfabriek wilt maken. 
    * **Resourcegroep:** selecteer een bestaande resourcegroep in de vervolgkeuzelijst of selecteer de optie **Nieuwe maken** en voer de naam van een resourcegroep in. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
    * **Versie**: Selecteer **V2**.
    * **Locatie**: Selecteer de locatie voor de gegevensfabriek. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensopslag die door gegevensfabriek worden gebruikt, kunnen zich in andere locaties en regio's bevinden. 

3. Selecteer **Maken**.
4. Nadat de creatie is voltooid, gaat u naar uw gegevensfabriek. U ziet de startpagina **van Data Factory** zoals weergegeven in de volgende afbeelding: 
   
   ![Startpagina van de gegevensfactory](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Klik op de tegel **Maken en bewaken** om de toepassing Gegevensintegratie in een afzonderlijk tabblad te openen.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Gegevens laden in Azure Data Lake Storage Gen2

1. Selecteer **op de** pagina Aan de slag de tegel **Gegevens kopiëren** om het gereedschap Gegevens kopiëren te starten: 

   ![De tegel Copy Data-hulpprogramma](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Geef op de pagina **Eigenschappen** **CopyFromAmazonS3ToADLS** op voor het veld **Taaknaam** en selecteer **Volgende:**

    ![De pagina Eigenschappen](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Klik op de pagina **Brongegevensarchief** op **+ Nieuwe verbinding maken:**

    ![De pagina Brongegevensarchief](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Selecteer **Amazon S3** in de connectorgalerie en selecteer **Doorgaan**
    
    ![Pagina Gegevensarchief S3](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Ga op **de pagina Amazon S3-verbinding opgeven** de volgende stappen uit:

   1. Geef de waarde **van de toegangssleutel-id** op.
   2. Geef de waarde **van de geheime toegangssleutel** op.
   3. Klik op **Verbinding testen** om de instellingen te valideren en selecteer vervolgens **Voltooien**.
   4. U ziet dat er een nieuwe verbinding wordt gemaakt. Selecteer **Next**.
   
      ![Amazon S3-account opgeven](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. Blader op de pagina **Het invoerbestand of de invoermap kiezen** naar de map en het bestand dat u wilt kopiëren. Selecteer de map/het bestand en selecteer **Kiezen:**

    ![Het invoerbestand of de invoermap kiezen](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Geef het kopieergedrag op door de opties **Bestanden recursief kopiëren** en **binaire kopie** in te schakelen. Selecteer **Volgende**:

    ![Uitvoermap opgeven](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Klik op de pagina **Doelgegevensarchief** op **+ Nieuwe verbinding maken**en selecteer Azure Data Lake Storage **Gen2**en selecteer **Doorgaan:**

    ![De pagina Doelgegevensarchief](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Ga op de **pagina Azure Data Lake Storage-verbindingspagina opgeven:**

   1. Selecteer uw Account met Data Lake Storage Gen2 in de vervolgkeuzelijst 'Accountnaam opslagaccount'.
   2. Selecteer **Voltooien** om de verbinding te maken. Selecteer **vervolgens Volgende**.
   
   ![Azure Data Lake Storage Gen2-account opgeven](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Voer op de pagina **Het uitvoerbestand of de map kiezen** de naam **copyfroms3** in als naam van de uitvoermap en selecteer **Volgende**. ADF maakt het bijbehorende ADLS Gen2-bestandssysteem en submappen tijdens het kopiëren als deze niet bestaat.

    ![Uitvoermap opgeven](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Selecteer **op** de pagina Instellingen de optie **Volgende** om de standaardinstellingen te gebruiken:

    ![De pagina Instellingen](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Controleer **op** de pagina Overzicht de instellingen en selecteer **Volgende:**

    ![Overzichtspagina](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Selecteer **op**de pagina Implementatie de optie **Monitor** om de pijplijn te controleren:

    ![De pagina Implementatie](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. De kolom **Acties** bevat koppelingen om activiteitenuit te voeren en de pijplijn opnieuw uit te voeren:

    ![Pijplijnuitvoeringen controleren](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Als u activiteitsruns wilt weergeven die zijn gekoppeld aan de pijplijnrun, selecteert u de koppeling **Activiteitsuitvoering weergeven** in de kolom **Acties.** Omdat er slechts één activiteit (kopieeractiviteit) in de pijplijn is, ziet u slechts één vermelding in de lijst. Als u wilt teruggaan naar de weergave **pijplijnuitvoeringen,** selecteert u de koppeling Pijplijn en bovenaan. Selecteer **Vernieuwen** om de lijst te vernieuwen. 

    ![Uitvoering van activiteiten controleren](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Als u de uitvoeringsgegevens voor elke kopieeractiviteit wilt controleren, selecteert u de koppeling **Details** (afbeelding van de bril) onder **Acties** in de weergave activiteitsbewaking. U details controleren, zoals het volume van gegevens die van de bron naar de gootsteen zijn gekopieerd, gegevensdoorvoer, uitvoeringsstappen met bijbehorende duur en gebruikte configuraties:

    ![Details van het uitvoeren van activiteiten controleren](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Controleer of de gegevens worden gekopieerd naar uw Data Lake Storage Gen2-account.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van activiteit kopiëren](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-connector](connector-azure-data-lake-storage.md)
