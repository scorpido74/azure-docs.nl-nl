---
title: Gegevens uit Office 365 laden met Azure Data Factory
description: Azure Data Factory gebruiken om gegevens uit Office 365 te kopiëren
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: cd2b530375beb3e8fa1f79e004f4f1ac7fd4d0bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443912"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Gegevens uit Office 365 laden met Azure Data Factory

In dit artikel ziet u hoe u de _gegevensverzamelingsgegevens van Office 365 gebruiken in Azure Blob-opslag._ U vergelijkbare stappen uitvoeren om gegevens naar Azure Data Lake Gen1 of Gen2 te kopiëren. Raadpleeg het artikel over [de Office 365-connector](connector-office-365.md) over het kopiëren van gegevens uit Office 365 in het algemeen.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer links in het menu Een resource > **Analytics** > **Data Factory maken:** **Create a resource** 
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geef op de pagina **Nieuwe gegevensfabriek** waarden op voor de velden die in de volgende afbeelding worden weergegeven:
      
   ![Pagina Nieuwe gegevensfactory](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Naam:** Voer een wereldwijd unieke naam in voor uw Azure-gegevensfabriek. Als u de fout 'Gegevensfabrieksnaam *LoadFromOffice365Demo'* ontvangt, voert u een andere naam in voor de gegevensfabriek. U bijvoorbeeld de naam _**yourname**_**LoadFromOffice365Demo**gebruiken. Probeer de gegevensfabriek opnieuw te maken. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    * **Abonnement**: Selecteer uw Azure-abonnement waarin u de gegevensfabriek wilt maken. 
    * **Resourcegroep:** selecteer een bestaande resourcegroep in de vervolgkeuzelijst of selecteer de optie **Nieuwe maken** en voer de naam van een resourcegroep in. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
    * **Versie**: Selecteer **V2**.
    * **Locatie**: Selecteer de locatie voor de gegevensfabriek. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensopslag die door gegevensfabriek worden gebruikt, kunnen zich in andere locaties en regio's bevinden. Deze gegevensopslag omvat Azure Data Lake Store, Azure Storage, Azure SQL Database, enzovoort.

3. Selecteer **Maken**.
4. Nadat de creatie is voltooid, gaat u naar uw gegevensfabriek. U ziet de startpagina **van Data Factory** zoals weergegeven in de volgende afbeelding:
   
   ![Startpagina van de gegevensfactory](./media/load-office-365-data/data-factory-home-page.png)

5. Klik op de tegel **Maken en bewaken** om de toepassing Gegevensintegratie in een afzonderlijk tabblad te openen.

## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Selecteer op de pagina 'Laten we aan de slag' de optie **Pijplijn maken**.
 
    ![Pijplijn maken](./media/load-office-365-data/create-pipeline-entry.png)

2. Typ **op het tabblad Algemeen** voor de pijplijn 'CopyPipeline' voor **Naam** van de pijplijn.

3. In het gereedschap Activiteitenvak > categorie & transformeren > de **activiteit Kopiëren** van het gereedschapsvak naar het oppervlak van het pijplijnontwerp slepen en neerzetten. Geef 'CopyFromOffice365ToBlob' op als activiteitsnaam.

### <a name="configure-source"></a>Bron configureren

1. Ga naar het tabblad pijplijn > **Bron**, klik op **+ Nieuw** om een brongegevensset te maken. 

2. Selecteer **Office 365**in het venster Nieuwe gegevensset en selecteer **Doorgaan**.
 
3. U bevindt zich nu op het tabblad configuratie van kopieeractiviteiten. Klik op de knop **Bewerken** naast de Office 365-gegevensset om de gegevensconfiguratie voort te zetten.

    ![Config Office 365-gegevensset algemeen](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. U ziet een nieuw tabblad geopend voor Office 365-gegevensset. Typ op **het tabblad Algemeen** onder aan het venster Eigenschappen 'SourceOffice365Dataset' voor Naam.
 
5. Ga naar het **tabblad Verbinding** van het venster Eigenschappen. Klik naast het tekstvak Gekoppelde service op **+ Nieuw**.

6. Typ in het venster Nieuwe gekoppelde service 'Office365LinkedService' als naam, voer de hoofd-id en servicehoofdsleutel van de service in, test de verbinding en selecteer **Maken** om de gekoppelde service te implementeren.

    ![Nieuwe office 365-gekoppelde service](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Nadat de gekoppelde service is gemaakt, gaat u terug naar de gegevenssetinstellingen. Kies naast **Tabel**de pijl-omlaag om de lijst met beschikbare Office 365-gegevenssets uit te vouwen en kies 'BasicDataSet_v0. Message_v0" uit de vervolgkeuzelijst:

    ![Gegevensset config Office 365](./media/load-office-365-data/edit-dataset.png)

8. Ga nu terug **pipeline** > naar het**tabblad Bron van** pijplijn om extra eigenschappen voor Office 365-gegevensextractie te blijven configureren.  Gebruikersbereik en gebruikersbereikfilter zijn optionele predicaten die u definiëren om de gegevens die u uit Office 365 wilt extraheren, te beperken. Zie sectie [Office 365-gegevensseteigenschappen](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties) voor het configureren van deze instellingen.

9. U moet een van de datumfilters kiezen en de begin- en eindtijdwaarden opgeven.

10. Klik op het tabblad **Schema importeren** om het schema voor de gegevensset Bericht te importeren.

    ![Config Office 365-gegevenssetschema](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Sink configureren

1. Ga naar de pijplijn > **het tabblad Sink**en selecteer + **Nieuw** om een sink-gegevensset te maken.
 
2. Houd er in het venster Nieuwe gegevensset op dat alleen de ondersteunde bestemmingen worden geselecteerd bij het kopiëren vanuit Office 365. Selecteer **Azure Blob Storage,** selecteer Binaire indeling en selecteer **Doorgaan**.  In deze zelfstudie kopieert u Office 365-gegevens naar een Azure Blob-opslag.

3. Klik op de knop **Bewerken** naast de azure blob-opslaggegevensset om de gegevensconfiguratie voort te zetten.

4. Voer **op** het tabblad Algemeen van het venster Eigenschappen in Naam de enter "OutputBlobDataset".

5. Ga naar het **tabblad Verbinding** van het venster Eigenschappen. Klik naast het tekstvak Gekoppelde service op **+Nieuw**.

6. Typ in het venster Nieuwe gekoppelde service 'AzureStorageLinkedService' als naam, selecteer 'Serviceprincipal' in de vervolgkeuzelijst met verificatiemethoden, vul het serviceeindpunt, tenant, servicehoofd-id en servicehoofdsleutel in en selecteer Opslaan in de gekoppelde service implementeren.  Raadpleeg [hier](connector-azure-blob-storage.md#service-principal-authentication) voor het instellen van serviceprincipal-verificatie voor Azure Blob Storage.

    ![Nieuwe Blob-gekoppelde service](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>De pijplijn valideren

Selecteer in de werkbalk **Valideren** om de pijplijn te valideren.

U ook de JSON-code zien die aan de pijplijn is gekoppeld door rechtsboven op Code te klikken.

## <a name="publish-the-pipeline"></a>De pijplijn publiceren

Selecteer **alles publiceren**op de bovenste werkbalk . Met deze actie publiceert u entiteiten (gegevenssets en pijplijnen) die u hebt gemaakt met Data Factory.

![Wijzigingen publiceren](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>De pijplijn handmatig activeren

Selecteer **Trigger toevoegen** op de werkbalk en selecteer Nu **activeren**. Klik op de pagina Pijplijnuitvoering op **Voltooien**. 

## <a name="monitor-the-pipeline"></a>De pijplijn bewaken

Ga naar het tabblad **Controleren** aan de linkerkant. U ziet een pijplijn die wordt geactiveerd door een handmatige trigger. U kunt via de links in de kolom **Acties** details van de activiteiten bekijken en de pijplijn opnieuw uitvoeren.

![De pijplijn bewaken](./media/load-office-365-data/pipeline-status.png) 

Selecteer de link **Uitvoeringen van activiteit weergeven** in de kolom Acties om de activiteituitvoeringen te zien die zijn gekoppeld aan de pijplijnuitvoering. Omdat er in dit voorbeeld slechts één activiteit in de pijplijn is, ziet u slechts één vermelding in de lijst. Selecteer de link **Details** (pictogram van een bril) in de kolom Acties om details over de kopieerbewerking te zien.

![Activiteit controleren](./media/load-office-365-data/activity-status.png) 

Als dit de eerste keer is dat u gegevens opvraagt voor deze context (een combinatie van welke gegevenstabel wordt geopend, in welk doelaccount de gegevens worden geladen en in welke gebruikersidentiteit de aanvraag voor gegevenstoegang wordt ingediend), ziet u de status van kopieeractiviteit als **In uitvoering**en alleen wanneer u klikt op de link Details onder Acties ziet u de status als **RequesetingConsent**.  Een lid van de groep voor gegevenstoegangsmoet de aanvraag in het Privileged Access Management goedkeuren voordat de gegevensextractie kan worden voortgezet.

_Status als het aanvragen van toestemming:_
![Activiteit uitvoeringsgegevens - toestemming vragen](./media/load-office-365-data/activity-details-request-consent.png) 

_Status als het extraheren van gegevens:_

![Details activiteitsuitvoering - gegevens extraheren](./media/load-office-365-data/activity-details-extract-data.png) 

Zodra de toestemming is verstrekt, zal de gegevensextractie worden voortgezet en na enige tijd wordt de pijplijnrun weergegeven als geslaagd.

![Pijplijn controleren - geslaagd](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Ga nu naar de bestemming Azure Blob Storage en controleer of Office 365-gegevens zijn geëxtraheerd in binaire indeling.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over Azure SQL Data Warehouse-ondersteuning: 

> [!div class="nextstepaction"]
>[Office 365-connector](connector-office-365.md)