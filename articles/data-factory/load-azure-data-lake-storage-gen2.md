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
ms.openlocfilehash: 90573f77c77d614923f882053145d2f84598953d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440239"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Gegevens laden in Azure Data Lake Storage Gen2 met Azure Data Factory

Azure Data Lake Storage Gen2 is een set mogelijkheden die wordt toegewezen aan big data Analytics, ingebouwd in [Azure Blob-opslag](../storage/blobs/storage-blobs-introduction.md). Hiermee kunt u samenwerken met uw gegevens met behulp van beide beschermingsparadigma in het systeem en de object-opslag.

Azure Data Factory (ADF) is een volledig beheerde service voor gegevens integratie in de Cloud. U kunt de service gebruiken om de Lake te vullen met gegevens uit een uitgebreide set on-premises en cloud-gebaseerde gegevens opslag en tijd besparen bij het bouwen van uw analyse oplossingen. Zie de tabel met [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een gedetailleerde lijst met ondersteunde connectors.

Azure Data Factory biedt een oplossing voor beheerde gegevens verplaatsing met scale-out. Als gevolg van de scale-out-architectuur van ADF kunnen gegevens worden opgenomen met een hoge door voer. Zie [prestaties van Kopieer activiteit](copy-activity-performance.md)voor meer informatie.

Dit artikel laat u zien hoe u met het hulp programma Data Factory Gegevens kopiëren gegevens kunt laden van _Amazon Web Services S3-service_ in _Azure data Lake Storage Gen2_. U kunt vergelijk bare stappen volgen om gegevens te kopiëren van andere typen gegevens archieven.

>[!TIP]
>Raadpleeg [deze specifieke stapsgewijze instructies voor het](load-azure-data-lake-storage-gen2-from-gen1.md)kopiëren van gegevens van Azure data Lake Storage gen1 naar Gen2.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Azure Storage account met Data Lake Storage Gen2 ingeschakeld: als u geen opslag account hebt, [maakt u een account](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* AWS-account met een S3-Bucket die gegevens bevat: in dit artikel wordt beschreven hoe u gegevens kopieert vanuit Amazon S3. U kunt andere gegevens archieven gebruiken door vergelijk bare stappen te volgen.

## <a name="create-a-data-factory"></a>Een data factory maken

1. Selecteer in het menu links de optie **een resource maken** > **gegevens en analyses** > **Data Factory**:
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geef op de pagina **nieuw Data Factory** waarden op voor de velden die worden weer gegeven in de volgende afbeelding: 
      
   ![De pagina Nieuwe data factory](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Naam**: Voer een wereld wijd unieke naam in voor uw Azure-Data Factory. Als het fout bericht ' Data Factory-naam \"LoadADLSDemo\" niet beschikbaar is, geeft u een andere naam op voor de data factory. U kunt bijvoorbeeld _**de naam**_ **ADFTutorialDataFactory**. Probeer de data factory opnieuw te maken. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    * **Abonnement**: Selecteer het Azure-abonnement waarin u de Data Factory wilt maken. 
    * **Resource groep**: Selecteer een bestaande resource groep in de vervolg keuzelijst of selecteer de optie **nieuwe maken** en voer de naam van een resource groep in. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
    * **Versie**: Selecteer **v2**.
    * **Locatie**: Selecteer de locatie voor de Data Factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevens archieven die door data factory worden gebruikt, kunnen zich op andere locaties en regio's bevinden. 

3. Selecteer **Maken**.
4. Nadat het maken is voltooid, gaat u naar uw data factory. U ziet de **Data Factory** start pagina zoals wordt weer gegeven in de volgende afbeelding: 
   
   ![Startpagina van de gegevensfactory](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Klik op de tegel **Maken en bewaken** om de toepassing Gegevensintegratie in een afzonderlijk tabblad te openen.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Gegevens laden in Azure Data Lake Storage Gen2

1. Selecteer op de pagina **aan de slag** de **gegevens kopiëren** tegel om het gegevens kopiëren-hulp programma te starten: 

   ![De tegel Copy Data-hulpprogramma](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Geef op de pagina **Eigenschappen** **CopyFromAmazonS3ToADLS** op voor het veld **taak naam** en selecteer **volgende**:

    ![De pagina Eigenschappen](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Klik op de pagina **brongegevens archief** op **+ nieuwe verbinding maken**:

    ![De pagina Brongegevensarchief](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Selecteer **Amazon S3** in de galerie connector en selecteer **door gaan**
    
    ![S3-pagina van brongegevens archief](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Voer op de pagina **Amazon S3-verbinding opgeven** de volgende stappen uit:

   1. Geef de waarde voor de **toegangs sleutel-id** op.
   2. Geef de waarde voor de **geheime toegangs sleutel** op.
   3. Klik op **Verbinding testen** om de instellingen te valideren en selecteer vervolgens **Voltooien**.
   4. U ziet dat er een nieuwe verbinding wordt gemaakt. Selecteer **Next**.
   
      ![Amazon S3-account opgeven](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. Blader op de pagina **Het invoerbestand of de invoermap kiezen** naar de map en het bestand dat u wilt kopiëren. Selecteer de map/het bestand, selecteer **kiezen**:

    ![Het invoerbestand of de invoermap kiezen](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Geef het kopieergedrag op door de opties **Bestanden recursief kopiëren** en **binaire kopie** in te schakelen. Selecteer **Volgende**:

    ![Uitvoermap opgeven](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Klik op de pagina **doel gegevens archief** op **+ nieuwe verbinding maken**en selecteer vervolgens **Azure data Lake Storage Gen2**en selecteer **door gaan**:

    ![De pagina Doelgegevensarchief](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Voer de volgende stappen uit op de pagina **Azure data Lake Storage verbinding opgeven** :

   1. Selecteer uw Data Lake Storage Gen2 geschikte account in de vervolg keuzelijst opslag account naam.
   2. Selecteer **Voltooien** om de verbinding te maken. Selecteer vervolgens **Volgende**.
   
   ![Azure Data Lake Storage Gen2 account opgeven](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Voer op de pagina **het uitvoer bestand of de map kiezen** **copyfroms3** in als de naam van de uitvoermap en selecteer **volgende**. Bij ADF worden de bijbehorende ADLS Gen2 bestands systeem en submappen tijdens het kopiëren gemaakt als deze niet bestaat.

    ![Uitvoermap opgeven](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Selecteer op de pagina **instellingen** de optie **volgende** om de standaard instellingen te gebruiken:

    ![De pagina Instellingen](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Controleer de instellingen op de pagina **samen vatting** en selecteer **volgende**:

    ![Overzichtspagina](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Selecteer op de **pagina implementatie**de optie **monitor** om de pijp lijn te bewaken:

    ![De pagina Implementatie](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. De kolom **acties** bevat koppelingen om de details van de activiteit weer te geven en de pijp lijn opnieuw uit te voeren:

    ![Pijplijnuitvoeringen controleren](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Selecteer de koppeling **uitvoeringen van activiteit weer** geven in de kolom **acties** om de uitvoering van activiteiten te bekijken die zijn gekoppeld aan de pijplijn uitvoering. Omdat er slechts één activiteit (kopieeractiviteit) in de pijplijn is, ziet u slechts één vermelding in de lijst. Als u wilt terugkeren naar de weer gave pijplijn uitvoeringen, selecteert u de koppeling **pijp lijnen** bovenaan. Selecteer **Vernieuwen** om de lijst te vernieuwen. 

    ![Uitvoering van activiteiten controleren](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Als u de uitvoerings Details voor elke Kopieer activiteit wilt bewaken, selecteert u de **Details** koppeling (bril-afbeelding) onder **acties** in de weer gave activiteiten bewaking. U kunt details, zoals het volume van de gegevens die uit de bron zijn gekopieerd, bewaken naar de sink, gegevens doorvoer, uitvoerings stappen met de overeenkomstige duur en de gebruikte configuraties:

    ![Details van uitvoering van activiteit bewaken](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Controleer of de gegevens zijn gekopieerd naar uw Data Lake Storage Gen2-account.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht kopieeractiviteit](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-connector](connector-azure-data-lake-storage.md)
