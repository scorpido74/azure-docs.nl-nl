---
title: Gegevens laden in Azure Data Lake Storage Gen1
description: Azure Data Factory gebruiken om gegevens te kopiëren naar Azure Data Lake Storage Gen1
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/17/2018
ms.openlocfilehash: 1325910877d1e030b3bf4114e16d0f81ecea8cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443982"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Gegevens laden in Azure Data Lake Storage Gen1 met Azure Data Factory

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (voorheen bekend als Azure Data Lake Store) is een bedrijfsbrede hyper-scale repository voor big data analytische workloads. Met Data Lake Storage Gen1 u gegevens vastleggen van elke grootte, type en innamesnelheid. De gegevens worden op één plaats vastgelegd voor operationele en verkennende analyses.

Azure Data Factory is een volledig beheerde cloudgebaseerde data-integratieservice. U de service gebruiken om het meer te vullen met gegevens uit uw bestaande systeem en tijd te besparen bij het bouwen van uw analyseoplossingen.

Azure Data Factory biedt de volgende voordelen voor het laden van gegevens in Data Lake Storage Gen1:

* **Eenvoudig in te stellen:** Een intuïtieve 5-staps wizard zonder scripting vereist.
* **Rich data store support**: Ingebouwde ondersteuning voor een rijke set on-premises en cloud-gebaseerde datastores. Zie de tabel [met ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een gedetailleerde lijst .
* **Veilig en compliant**: Gegevens worden overgedragen via HTTPS of ExpressRoute. De wereldwijde aanwezigheid van de service zorgt ervoor dat uw gegevens nooit de geografische grens verlaten.
* **Hoge prestaties**: Tot 1 GB/s-laadsnelheid van gegevens in Data Lake Storage Gen1. Zie [Activiteitsprestaties kopiëren](copy-activity-performance.md)voor meer informatie .

In dit artikel ziet u hoe u de tool Gegevenskopiëren gegevens in de gegevensfabriek gebruiken om gegevens van Amazon S3 te _laden in Data Lake Storage Gen1._ U vergelijkbare stappen uitvoeren om gegevens uit andere soorten gegevensarchieven te kopiëren.

> [!NOTE]
> Zie [Gegevens kopiëren naar of van Data Lake Storage Gen1 met Azure Data Factory](connector-azure-data-lake-store.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Data Lake Storage Gen1-account: als u geen Data Lake Storage Gen1-account hebt, raadpleegt u de instructies in [Een Data Lake Storage Gen1-account maken.](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account)
* Amazon S3: Dit artikel laat zien hoe u gegevens van Amazon S3 kopiëren. U andere gegevensarchieven gebruiken door vergelijkbare stappen te volgen.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer links in het menu Een resource > **Analytics** > **Data Factory maken:** **Create a resource**
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geef op de pagina **Nieuwe gegevensfabriek** waarden op voor de velden die in de volgende afbeelding worden weergegeven: 
      
   ![Pagina Nieuwe gegevensfactory](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Naam:** Voer een wereldwijd unieke naam in voor uw Azure-gegevensfabriek. Als u de fout 'Gegevensfabrieksnaam \"LoadADLSG1Demo\" is niet beschikbaar' ontvangt, voert u een andere naam in voor de gegevensfabriek. U bijvoorbeeld de naam _**yourname**_**ADFTutorialDataFactory**gebruiken. Probeer de gegevensfabriek opnieuw te maken. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    * **Abonnement**: Selecteer uw Azure-abonnement waarin u de gegevensfabriek wilt maken. 
    * **Resourcegroep:** selecteer een bestaande resourcegroep in de vervolgkeuzelijst of selecteer de optie **Nieuwe maken** en voer de naam van een resourcegroep in. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
    * **Versie**: Selecteer **V2**.
    * **Locatie**: Selecteer de locatie voor de gegevensfabriek. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensopslag die door gegevensfabriek worden gebruikt, kunnen zich in andere locaties en regio's bevinden. Deze gegevensopslag omvat Azure Data Lake Storage Gen1, Azure Storage, Azure SQL Database, enzovoort.

3. Selecteer **Maken**.
4. Nadat de creatie is voltooid, gaat u naar uw gegevensfabriek. U ziet de startpagina **van Data Factory** zoals weergegeven in de volgende afbeelding: 
   
   ![Startpagina van de gegevensfactory](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Klik op de tegel **Maken en bewaken** om de toepassing Gegevensintegratie in een afzonderlijk tabblad te openen.

## <a name="load-data-into-data-lake-storage-gen1"></a>Gegevens laden in Data Lake Storage Gen1

1. Selecteer **op de** pagina Aan de slag de tegel **Gegevens kopiëren** om het gereedschap Gegevens kopiëren te starten: 

   ![De tegel Copy Data-hulpprogramma](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. Geef op de pagina **Eigenschappen** **CopyFromAmazonS3ToADLS** op voor het veld **Taaknaam** en selecteer **Volgende:**

    ![De pagina Eigenschappen](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. Klik op de pagina **Brongegevensarchief** op **+ Nieuwe verbinding maken:**

    ![De pagina Brongegevensarchief](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Selecteer **Amazon S3**en selecteer **Doorgaan**
    
    ![Pagina Gegevensarchief S3](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Ga op **de pagina Amazon S3-verbinding opgeven** de volgende stappen uit: 
   1. Geef de waarde **van de toegangssleutel-id** op.
   2. Geef de waarde **van de geheime toegangssleutel** op.
   3. Selecteer **Finish**.
   
      ![Amazon S3-account opgeven](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. U ziet een nieuwe verbinding. Selecteer **Volgende**.
   
   ![Amazon S3-account opgeven](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. Blader op de pagina **Het invoerbestand of de invoermap kiezen** naar de map en het bestand dat u wilt kopiëren. Selecteer de map/het bestand, selecteer **Kiezen**en selecteer **Volgende:**

    ![Het invoerbestand of de invoermap kiezen](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Kies het kopieergedrag door de opties **Bestanden opnieuw kopiëren** en Binaire **kopie** (kopiëren bestanden as-is) te selecteren. Selecteer **Volgende**:

    ![Uitvoermap opgeven](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. Klik op de pagina **Doelgegevensarchief** op **+ Nieuwe verbinding maken**en selecteer Azure Data Lake Storage **Gen1**en selecteer **Doorgaan:**

    ![De pagina Doelgegevensarchief](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Ga op de pagina **New Linked Service (Azure Data Lake Storage Gen1)** de volgende stappen uit: 

   1. Selecteer uw Data Lake Storage Gen1-account voor de naam van het **Data Lake Store-account.**
   2. Geef de **tenant**op en selecteer Voltooien.
   3. Selecteer **Volgende**.
   
   > [!IMPORTANT]
   > In deze walkthrough gebruikt u een beheerde identiteit voor Azure-bronnen om uw Data Lake Storage Gen1-account te verifiëren. Zorg ervoor dat u de MSI de juiste machtigingen in Data Lake Storage Gen1 verleent door deze instructies op te [volgen.](connector-azure-data-lake-store.md#managed-identity)
   
   ![Gegevens Lake Storage Gen1-account opgeven](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. Voer op de pagina **Het uitvoerbestand of de map kiezen** de naam **copyfroms3** in als naam van de uitvoermap en selecteer **Volgende:** 

    ![Uitvoermap opgeven](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. Selecteer **op** de pagina Instellingen de optie **Volgende:**

    ![De pagina Instellingen](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. Controleer **op** de pagina Overzicht de instellingen en selecteer **Volgende:**

    ![Overzichtspagina](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. Selecteer **op**de pagina Implementatie de optie **Monitor** om de pijplijn te controleren (taak):

    ![De pagina Implementatie](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. De kolom **Acties** bevat koppelingen om activiteitenuit te voeren en de pijplijn opnieuw uit te voeren:

    ![Pijplijnuitvoeringen controleren](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Als u activiteitsruns wilt weergeven die zijn gekoppeld aan de pijplijnrun, selecteert u de koppeling **Activiteitsuitvoering weergeven** in de kolom **Acties.** Omdat er slechts één activiteit (kopieeractiviteit) in de pijplijn is, ziet u slechts één vermelding in de lijst. Als u wilt teruggaan naar de weergave **pijplijnuitvoeringen,** selecteert u de koppeling Pijplijn en bovenaan. Selecteer **Vernieuwen** om de lijst te vernieuwen. 

    ![Uitvoering van activiteiten controleren](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Als u de uitvoeringsgegevens voor elke kopieeractiviteit wilt controleren, selecteert u de koppeling **Details** onder **Acties** in de weergave activiteitsbewaking. U details controleren, zoals het volume van gegevens die van de bron naar de gootsteen zijn gekopieerd, gegevensdoorvoer, uitvoeringsstappen met bijbehorende duur en gebruikte configuraties:

    ![Details van het uitvoeren van activiteiten controleren](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Controleer of de gegevens worden gekopieerd naar uw Data Lake Storage Gen1-account: 

    ![Gegevens gegevensopslag-gen1-uitvoer controleren](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over ondersteuning voor Data Lake Storage Gen1: 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1-connector](connector-azure-data-lake-store.md)
