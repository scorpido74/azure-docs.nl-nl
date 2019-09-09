---
title: Gegevens kopiëren van Azure Data Factory naar Azure Data Explorer
description: In dit onderwerp leert u hoe u gegevens opneemt (laadt) in azure Data Explorer met behulp van Azure Data Factory kopiëren
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: b3bd9b800da4f096639d02c78b718216441621a9
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2019
ms.locfileid: "70803981"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>Gegevens kopiëren naar Azure Data Explorer met behulp van Azure Data Factory 

Azure Data Explorer is een snelle, volledig beheerde gegevens analyse service voor realtime-analyse van grote hoeveel heden gegevensstreaming van veel bronnen, zoals toepassingen, websites en IoT-apparaten. Bekijk iteratieve gegevens en Identificeer patronen en afwijkingen om producten te verbeteren, ervaringen van klanten te verbeteren, apparaten te bewaken en de bewerkingen te stimuleren. Ontdek nieuwe vragen en krijg antwoorden binnen enkele minuten. Azure Data Factory is een volledig beheerde service voor gegevens integratie in de Cloud. U kunt de service gebruiken om uw Azure Data Explorer-data base te vullen met gegevens van uw bestaande systeem en tijd besparen bij het bouwen van uw analyse oplossingen.

Azure Data Factory biedt de volgende voor delen voor het laden van gegevens in azure Data Explorer:

* **Eenvoudig in te stellen**: Een intuïtieve vijf stappen wizard zonder scripting vereist.
* **Ondersteuning voor uitgebreide gegevens opslag**: Ingebouwde ondersteuning voor een groot aantal on-premises en cloud-gebaseerde gegevens archieven. Zie de tabel met [ondersteunde gegevens archieven](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)voor een gedetailleerde lijst.
* **Beveiligd en compatibel**: Gegevens worden overgedragen via HTTPS of ExpressRoute. De wereld wijde service aanwezigheid zorgt ervoor dat uw gegevens de geografische grens nooit verlaten.
* **Hoge prestaties**: Maxi maal 1 GB/s gegevens laden in azure Data Explorer. Zie [prestaties van Kopieer activiteit](/azure/data-factory/copy-activity-performance)voor meer informatie.

In dit artikel wordt beschreven hoe u het hulp programma Data Factory Gegevens kopiëren kunt gebruiken om gegevens van Amazon S3 te laden in azure Data Explorer. U kunt vergelijk bare stappen volgen voor het kopiëren van gegevens uit andere gegevens archieven, zoals [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage), [Azure SQL database](/azure/data-factory/connector-azure-sql-database), [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse), [Google BigQuery](/azure/data-factory/connector-google-bigquery),[Oracle](/azure/data-factory/connector-oracle)en [File System](/azure/data-factory/connector-file-system).

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Een Azure Data Explorer-cluster en-data base](create-cluster-database-portal.md)
* Bron van de gegevens.

## <a name="create-a-data-factory"></a>Data factory maken

1. Selecteer de knop **een resource maken** (+) in de linkerbovenhoek van de portal > **Analytics** > **Data Factory**.

   ![Een nieuwe gegevensfactory maken](media/data-factory-load-data/create-adf.png)

1. Geef op de pagina **nieuw Data Factory** waarden op voor de volgende velden en selecteer vervolgens **maken**.

    ![De pagina Nieuwe data factory](media/data-factory-load-data/my-new-data-factory.png)

    **Instelling**  | **Beschrijving van veld**
    |---|---|
    | **Name** | Voer een wereld wijd unieke naam in voor uw data factory. Als u de fout melding *' Data Factory- \"naam\" LoadADXDemo is niet beschikbaar '* ontvangt, voert u een andere naam in voor de Data Factory. Zie [Data Factory naamgevings regels](/azure/data-factory/naming-rules)voor naamgevings regels van Data Factory artefacten.|
    | **Abonnement** | Selecteer uw Azure-abonnement waarin u de data factory wilt maken. |
    | **Resourcegroep** | Selecteer **nieuwe maken** en voer de naam van een nieuwe resource groep in. Selecteer **bestaande gebruiken**als u een bestaande resource groep hebt. |
    | **Versie** | **V2** selecteren |
    | **Location** | Selecteer de locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevens archieven die door data factory worden gebruikt, kunnen zich op andere locaties of regio's bevinden. |
    | | |

1. Selecteer meldingen op de werk balk om het aanmaak proces te bewaken. Nadat het maken is voltooid, gaat u naar de data factory die u hebt gemaakt. De start pagina van **Data Factory** wordt geopend.

   ![Startpagina van de gegevensfactory](media/data-factory-load-data/data-factory-home-page.png)

1. Selecteer de tegel **auteur & monitor** om de toepassing te openen op een afzonderlijk tabblad.

## <a name="load-data-into-azure-data-explorer"></a>Gegevens laden in azure Data Explorer

Gegevens kunnen worden geladen van veel soorten [gegevens archieven](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) naar Azure Data Explorer. In dit onderwerp vindt u informatie over het laden van gegevens van Amazon S3.

Er zijn twee manieren om gegevens in azure Data Explorer te laden met behulp van Azure Data Factory:

* Azure Data Factory gebruikers interface: [tabblad **Auteur**](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Azure Data Factory **gegevens kopiëren** hulp programma](/azure/data-factory/quickstart-create-data-factory-copy-data-tool) dat in dit artikel wordt gebruikt.

### <a name="copy-data-from-amazon-s3-source"></a>Gegevens kopiëren van Amazon S3 (bron)

1. Selecteer op de pagina **aan de slag** de **gegevens kopiëren** tegel om het gegevens kopiëren-hulp programma te starten.

   ![De tegel gegevens kopiëren](media/data-factory-load-data/copy-data-tool-tile.png)

1. Geef op de pagina **Eigenschappen** de **taak naam** op en selecteer **volgende**.

    ![Kopiëren van bron eigenschappen](media/data-factory-load-data/copy-from-source.png)

1. Klik op de pagina **brongegevens archief** op **+ nieuwe verbinding maken**.

    ![Bron gegevens verbinding maken](media/data-factory-load-data/source-create-connection.png)

1. Selecteer **Amazon S3**en selecteer **door gaan**

    ![Nieuwe gekoppelde service](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Voer de volgende stappen uit op de pagina **nieuwe gekoppelde service (Amazon S3)** :

    ![Een gekoppelde service van Amazon S3 opgeven](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * Geef de **naam** op van de nieuwe gekoppelde service.
    * Selecteer **verbinden via Integration runtime** -waarde in de vervolg keuzelijst.
    * Geef de waarde voor de **toegangs sleutel-id** op.
    * Geef de waarde voor de **geheime toegangs sleutel** op.
    * Selecteer **verbinding testen** om de gekoppelde service verbinding te testen die u hebt gemaakt.
    * Selecteer **Finish**.

1. Op de pagina **brongegevens archief** ziet u de nieuwe AmazonS31-verbinding. Selecteer **Volgende**.

   ![De verbinding is gemaakt met het bron gegevens archief](media/data-factory-load-data/source-data-store-created-connection.png)

1. Klik op de pagina **het invoer bestand of de map kiezen** :

    1. Blader naar de map of het bestand dat u wilt kopiëren. Selecteer de map of het bestand.
    1. Selecteer het Kopieer gedrag zoals vereist. Bewaar **binaire kopie** uitgeschakeld.
    1. Selecteer **Volgende**.

    ![Het invoerbestand of de invoermap kiezen](media/data-factory-load-data/source-choose-input-file.png)

1. Selecteer op de pagina **instellingen voor bestands indelingen** de relevante instellingen voor het bestand en klik op **volgende**.

   ![Het invoerbestand of de invoermap kiezen](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Gegevens kopiëren naar Azure Data Explorer (bestemming)

Azure Data Explorer de nieuwe gekoppelde service is gemaakt om de gegevens te kopiëren naar de Azure Data Explorer doel tabel (Sink) die hieronder is opgegeven.

#### <a name="create-the-azure-data-explorer-linked-service"></a>De gekoppelde Azure Data Explorer-service maken

1. Op de pagina **doel gegevens archief** kunt u een bestaande gegevens opslag verbinding gebruiken of een nieuw gegevens archief opgeven door te klikken op **+ nieuwe verbinding maken**.

    ![De pagina Doelgegevensarchief](media/data-factory-load-data/destination-create-connection.png)

1. Selecteer op de pagina **nieuwe gekoppelde service** de optie **Azure Data Explorer**en selecteer vervolgens **door gaan** .

    ![Selecteer Azure Data Explorer-nieuwe gekoppelde service](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Voer de volgende stappen uit op de pagina **nieuwe gekoppelde service (Azure Data Explorer)** :

    ![ADX nieuwe gekoppelde service](media/data-factory-load-data/adx-new-linked-service.png)

   * Selecteer een **naam** voor de gekoppelde Azure Data Explorer-service.
   * Methode voor het **selecteren van accounts**: 
        * Selecteer het keuze rondje **van Azure-abonnement** en selecteer uw **Azure-abonnements** account. Selecteer vervolgens uw **cluster**. Opmerking in de vervolg keuzelijst worden alleen clusters vermeld die bij de gebruiker horen.
        * U kunt ook **hand matig** keuze rondje opgeven selecteren en uw **eind punt**invoeren.
    * Geef de **Tenant**op.
    * Voer de ID van de **Service-Principal**in.
    * Selecteer de knop **Service Principal Key** en voer de sleutel van de **Service-Principal**in.
    * Selecteer uw **Data Base** in de vervolg keuzelijst. U kunt ook het selectie vakje **bewerken** selecteren en de naam van uw data base invoeren.
    * Selecteer **verbinding testen** om de gekoppelde service verbinding te testen die u hebt gemaakt. Als u verbinding kunt maken met uw installatie **, wordt een** groen vinkje weer gegeven.
    * Selecteer **volt ooien** om de gekoppelde service te maken.

    > [!NOTE]
    > De service-principal wordt gebruikt door Azure Data Factory om toegang te krijgen tot de Azure Data Explorer-service. Voor de Service-Principal [maakt u een service-principal voor Azure Active Directory (Azure AD)](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Gebruik niet de **Azure Key Vault** methode.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>De Azure Data Explorer-gegevens verbinding configureren

1. Het **doel gegevens archief** wordt geopend. De Azure Data Explorer-gegevens verbinding die u hebt gemaakt, is beschikbaar voor gebruik. Selecteer **volgende** om de verbinding te configureren.

    ![ADX doel gegevens archief](media/data-factory-load-data/destination-data-store.png)

1. Stel in **tabel toewijzing**de naam van de doel tabel in en selecteer **volgende**.

    ![Tabel toewijzing doel gegevensset](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. Op de pagina **kolom toewijzing** :
    * De eerste toewijzing wordt uitgevoerd door ADF volgens de [schema toewijzing van ADF](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * Stel de **kolom toewijzingen** voor de Azure Data Factory doel tabel in. De standaard toewijzing wordt weer gegeven van bron naar ADF-doel tabel.
        * Deselecteer de kolommen die u niet nodig hebt voor het definiëren van de kolom toewijzing.
    * De tweede toewijzing vindt plaats wanneer deze tabellaire gegevens worden opgenomen in azure Data Explorer. Toewijzing wordt uitgevoerd op basis van [regels voor CSV-toewijzing](/azure/kusto/management/mappings#csv-mapping). Houd er rekening mee dat zelfs als de bron gegevens geen CSV-indeling hebben, ADF de gegevens heeft geconverteerd naar een tabel indeling, daarom is CSV-toewijzing de enige relevante toewijzing in deze fase.
        * Onder de eigenschappen van de **sink van Azure Data Explorer (Kusto)** wordt de relevante **opname toewijzings naam** (optioneel) toegevoegd, zodat de kolom toewijzing kan worden gebruikt.
        * Als de naam van de **opname toewijzing** niet is opgegeven, wordt de toewijzings volgorde "bij naam" die is gedefinieerd in de sectie **kolom toewijzingen** , uitgevoerd. Als de toewijzing ' bij naam ' mislukt, probeert Azure Data Explorer de gegevens op te nemen in de volg orde ' op kolom ' (toegewezen als standaard).
    * Selecteer **Volgende**

    ![Kolom toewijzing van de doel gegevensset](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Op de pagina **Instellingen** doet u het volgende:
    * Stel de relevante **fout tolerantie-instellingen**in.
    * **Prestatie-instellingen**: Het inschakelen van staging is niet van toepassing. **Geavanceerde instellingen** zijn onder andere kosten overwegingen. Als er geen specifieke behoeften zijn, blijft de instelling ongewijzigd.
    * Selecteer **Volgende**.

    ![Instellingen voor kopiëren van gegevens](media/data-factory-load-data/copy-data-settings.png)

1. Controleer de instellingen in **samen vatting**en selecteer **volgende**.

    ![Gegevens overzicht kopiëren](media/data-factory-load-data/copy-data-summary.png)

1. Op de **pagina implementatie**:
    * Selecteer **monitor** om over te scha kelen naar het tabblad **monitor** en Bekijk de status van de pijp lijn (voortgang, fouten en gegevens stroom).
    * Selecteer **pijp lijn bewerken** om gekoppelde services, gegevens sets en pijp lijnen te bewerken.
    * Selecteer **volt ooien** om de taak gegevens kopiëren te volt ooien

    ![De pagina Implementatie](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Azure Data Explorer-connector](/azure/data-factory/connector-azure-data-explorer) in azure Data Factory.

* Meer informatie over het bewerken van gekoppelde services, gegevens sets en pijp lijnen in de [Data Factory-gebruikers interface](/azure/data-factory/quickstart-create-data-factory-portal).

* Meer informatie over [Azure Data Explorer query's](/azure/data-explorer/web-query-data) voor het uitvoeren van gegevens query's.
