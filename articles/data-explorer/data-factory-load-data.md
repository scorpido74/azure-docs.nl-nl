---
title: Gegevens kopiëren van Azure Data Factory naar Azure Data Explorer
description: In dit artikel leert u hoe u gegevens kunt opnemen in azure Data Explorer met behulp van het hulp programma Azure Data Factory kopiëren.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300587"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Gegevens kopiëren naar Azure Data Explorer met behulp van Azure Data Factory 

Azure Data Explorer is een snelle, volledig beheerde service voor gegevens analyse. Het biedt real-time analyse van grote hoeveel heden gegevens die streamen van veel bronnen, zoals toepassingen, websites en IoT-apparaten. Met Azure Data Explorer kunt u gegevens iteratief verkennen en patronen en afwijkingen identificeren om producten te verbeteren, ervaringen van klanten verbeteren, apparaten bewaken en bewerkingen stimuleren. Het helpt u nieuwe vragen te verkennen en binnen enkele minuten antwoorden te krijgen. 

Azure Data Factory is een volledig beheerde, gegevens integratie service op basis van de Cloud. U kunt deze gebruiken om uw Azure Data Explorer-data base te vullen met gegevens van uw bestaande systeem. Het helpt u tijd te besparen wanneer u analyse oplossingen bouwt.

Wanneer u gegevens laadt in azure Data Explorer, biedt Data Factory de volgende voor delen:

* **Eenvoudige installatie**: Maak een intuïtieve, vijf stappen wizard zonder scripting vereist.
* **Ondersteuning voor uitgebreide gegevens opslag**: Krijg ingebouwde ondersteuning voor een groot aantal on-premises en cloud-gebaseerde gegevens archieven. Zie de tabel met [ondersteunde gegevens archieven](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)voor een gedetailleerde lijst.
* **Beveiligd en compatibel**: Gegevens worden overgedragen via HTTPS of Azure ExpressRoute. De wereld wijde service aanwezigheid zorgt ervoor dat uw gegevens de geografische grens nooit verlaten.
* **Hoge prestaties**: De snelheid voor het laden van gegevens is Maxi maal 1 gigabyte per seconde (GBps) in azure Data Explorer. Zie de prestaties van de [Kopieer activiteit](/azure/data-factory/copy-activity-performance)voor meer informatie.

In dit artikel gebruikt u het hulp programma Data Factory Gegevens kopiëren om gegevens van de Amazon Simple Storage-service (S3) te laden in azure Data Explorer. U kunt een vergelijkbaar proces volgen voor het kopiëren van gegevens uit andere gegevens archieven, zoals:
* [Azure Blob Storage](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL Data Warehouse](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Bestandssysteem](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Een Azure Data Explorer-cluster en -database](create-cluster-database-portal.md).
* Een gegevens bron.

## <a name="create-a-data-factory"></a>Data factory maken

1. Meld u aan bij [Azure Portal](https://ms.portal.azure.com).

1. Selecteer in het linkerdeel venster **een resource** > **Analytics** > -**Data Factory**maken.

   ![Maak een data factory in het Azure Portal](media/data-factory-load-data/create-adf.png)

1. Geef in het deel venster **nieuwe Data Factory** waarden op voor de velden in de volgende tabel:

   ![Het deel venster ' nieuwe data factory '](media/data-factory-load-data/my-new-data-factory.png)  

   | Instelling  | Waarde die moet worden ingevoerd  |
   |---|---|
   | **Name** | Voer in het vak een globaal unieke naam in voor uw data factory. Als er een fout bericht wordt weer gegeven, *is\" de Data Factory-naam \"LoadADXDemo niet beschikbaar*. Voer een andere naam in voor de Data Factory. Zie [Data Factory naamgevings regels](/azure/data-factory/naming-rules)voor regels over het benoemen van Data Factory artefacten.|
   | **Abonnement** | Selecteer in de vervolg keuzelijst het Azure-abonnement waarin u de data factory wilt maken. |
   | **Resourcegroep** | Selecteer **nieuwe maken**en voer vervolgens de naam van een nieuwe resource groep in. Als u al een resource groep hebt, selecteert u **bestaande gebruiken**. |
   | **Versie** | Selecteer **v2**in de vervolg keuzelijst. |  
   | **Location** | Selecteer in de vervolg keuzelijst de locatie voor de data factory. Alleen ondersteunde locaties worden weer gegeven in de lijst. De gegevens archieven die worden gebruikt door de data factory kunnen bestaan in andere locaties of regio's. |

1. Selecteer **Maken**.

1. Selecteer **meldingen** op de werk balk om het aanmaak proces te bewaken. Nadat u de data factory hebt gemaakt, selecteert u deze.
   
   Het deel venster **Data Factory** wordt geopend.

   ![Het Data Factory venster](media/data-factory-load-data/data-factory-home-page.png)

1. Als u de toepassing in een apart deel venster wilt openen, selecteert u de tegel **auteur & monitor** .

## <a name="load-data-into-azure-data-explorer"></a>Gegevens laden in azure Data Explorer

U kunt gegevens uit veel soorten [gegevens archieven](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) laden in azure Data Explorer. In dit artikel wordt beschreven hoe u gegevens laadt vanuit Amazon S3.

U kunt uw gegevens op een van de volgende manieren laden:

* Selecteer in de gebruikers interface van Azure Data Factory in het linkerdeel venster het pictogram **Auteur** , zoals wordt weer gegeven in de sectie ' een Data Factory maken ' van [een Data Factory maken met behulp van de Azure Data Factory-gebruikers interface](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory).
* In het hulp programma Azure Data Factory Gegevens kopiëren, zoals wordt weer gegeven in [de gegevens kopiëren tool gebruiken om gegevens te kopiëren](/azure/data-factory/quickstart-create-data-factory-copy-data-tool).

### <a name="copy-data-from-amazon-s3-source"></a>Gegevens kopiëren van Amazon S3 (bron)

1. Open in het deel venster **aan de slag** het gegevens kopiëren-hulp programma door **gegevens kopiëren**te selecteren.

   ![De knop Gegevens kopiëren-hulp programma](media/data-factory-load-data/copy-data-tool-tile.png)

1. Voer in het deel venster **Eigenschappen** in het vak **taak naam** een naam in en selecteer **volgende**.

    ![Het deel venster met Gegevens kopiëren eigenschappen](media/data-factory-load-data/copy-from-source.png)

1. Selecteer in het deel venster **brongegevens archief** de optie **nieuwe verbinding maken**.

    ![Het Gegevens kopiëren deel venster brongegevens archief](media/data-factory-load-data/source-create-connection.png)

1. Selecteer **Amazon S3**en selecteer vervolgens **door gaan**.

    ![Het deel venster nieuwe gekoppelde service](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Ga als volgt te werk in het deel venster **nieuwe gekoppelde service (Amazon S3)** :

    ![Een gekoppelde service van Amazon S3 opgeven](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. Voer in het vak **naam** de naam in van de nieuwe gekoppelde service.

    b. Selecteer de waarde in de vervolg keuzelijst **verbinding maken via Integration runtime** .

    c. Voer in het vak **toegangs sleutel-id** de waarde in.
    
    > [!NOTE]
    > In Amazon S3 selecteert u uw Amazon-gebruikers naam op de navigatie balk en selecteert u vervolgens **mijn beveiligings referenties**om uw toegangs sleutel te vinden.
    
    d. Voer in het vak **geheime toegangs sleutel** een waarde in.

    e. Als u de gekoppelde service verbinding die u hebt gemaakt, wilt testen, selecteert u **verbinding testen**.

    f. Selecteer **Finish**.
    
      In het deel venster **brongegevens archief** wordt de nieuwe AmazonS31-verbinding weer gegeven. 

1. Selecteer **Volgende**.

   ![De verbinding is gemaakt met het bron gegevens archief](media/data-factory-load-data/source-data-store-created-connection.png)

1. Ga als volgt te werk in het deel venster **het invoer bestand of de map kiezen** :

    a. Blader naar het bestand dat of de map die u wilt kopiëren en selecteer deze.

    b. Selecteer het gewenste Kopieer gedrag. Zorg ervoor dat het selectie vakje voor **binaire kopieën** is uitgeschakeld.

    c. Selecteer **Volgende**.

    ![Het invoerbestand of de invoermap kiezen](media/data-factory-load-data/source-choose-input-file.png)

1. Selecteer in het deel venster **instellingen voor bestands indeling** de relevante instellingen voor het bestand. en selecteer **volgende**.

   ![Het deel venster instellingen voor bestands indeling](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Gegevens kopiëren naar Azure Data Explorer (bestemming)

De nieuwe gekoppelde Azure Data Explorer-service wordt gemaakt om de gegevens te kopiëren naar de Azure Data Explorer-doel tabel (Sink) die is opgegeven in deze sectie.

#### <a name="create-the-azure-data-explorer-linked-service"></a>De gekoppelde Azure Data Explorer-service maken

Als u de gekoppelde Azure Data Explorer-service wilt maken, gaat u als volgt te werk.

1. Als u een bestaande gegevens opslag verbinding wilt gebruiken of een nieuw gegevens archief wilt opgeven, selecteert u **nieuwe verbinding maken**in het deel venster **doel gegevens** archief.

    ![Deel venster doel gegevens archief](media/data-factory-load-data/destination-create-connection.png)

1. Selecteer in het deel venster **nieuwe gekoppelde service** de optie **Azure Data Explorer**en selecteer vervolgens **door gaan**.

    ![Het deel venster nieuwe gekoppelde service](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Ga als volgt te werk in het deel venster **nieuwe gekoppelde service (Azure Data Explorer)** :

    ![Het deel venster Data Explorer nieuwe gekoppelde service in azure](media/data-factory-load-data/adx-new-linked-service.png)

   a. Voer in het vak **naam** een naam in voor de gekoppelde Azure Data Explorer-service.

   b. Voer een van de volgende handelingen uit bij methode voor het **selecteren van accounts**: 

    * Selecteer een **Azure-abonnement** en selecteer vervolgens in de vervolg keuzelijst uw Azure- **abonnement** en uw **cluster**. 

        > [!NOTE]
        > In het besturings element **cluster** vervolg keuzelijst worden alleen clusters vermeld die zijn gekoppeld aan uw abonnement.

    * Selecteer **hand matig invoeren**en voer vervolgens uw **eind punt**in.

   c. Voer in het vak **Tenant** de naam van de Tenant in.

   d. Voer in het vak **Service Principal id** de id van de Service-Principal in.

   e. Selecteer **Service Principal Key** en voer vervolgens in het vak **Service Principal Key** de waarde voor de sleutel in.

   f. Selecteer in de vervolg keuzelijst **Data Base** de naam van uw data base. U kunt ook het selectie vakje **bewerken** selecteren en vervolgens de naam van de data base invoeren.

   g. Als u de gekoppelde service verbinding die u hebt gemaakt, wilt testen, selecteert u **verbinding testen**. Als u verbinding kunt maken met uw gekoppelde service, wordt er een groen vinkje weer gegeven in het deel venster en is er een bericht over een **geslaagde verbinding** .

   h. Selecteer **volt ooien** om het maken van de gekoppelde service te volt ooien.

    > [!NOTE]
    > De service-principal wordt gebruikt door Azure Data Factory om toegang te krijgen tot de Azure Data Explorer-service. Als u een Service-Principal wilt maken, gaat u naar [een service-principal voor Azure Active Directory (Azure AD) maken](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Gebruik niet de Azure Key Vault methode.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>De Azure Data Explorer-gegevens verbinding configureren

Nadat u de gekoppelde service verbinding hebt gemaakt, wordt het deel venster **doel gegevens archief** geopend en is de verbinding die u hebt gemaakt, beschikbaar voor gebruik. Ga als volgt te werk om de verbinding te configureren.

1. Selecteer **Volgende**.

    ![Het deel venster doel gegevens archief van Azure Data Explorer](media/data-factory-load-data/destination-data-store.png)

1. Stel in het deel venster **tabel toewijzing** de naam van de doel tabel in en selecteer **volgende**.

    ![Het deel venster met de doel gegevensset "tabel toewijzing"](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. In het deel venster **kolom toewijzing** worden de volgende toewijzingen uitgevoerd:

    a. De eerste toewijzing wordt uitgevoerd door Azure Data Factory volgens de [Azure Data Factory schema toewijzing](/azure/data-factory/copy-activity-schema-and-type-mapping). Ga als volgt te werk:

    * Stel de **kolom toewijzingen** voor de Azure Data Factory doel tabel in. De standaard toewijzing wordt weer gegeven van de bron in de Azure Data Factory doel tabel.

    * Annuleer de selectie van de kolommen die u niet nodig hebt voor het definiëren van de kolom toewijzing.

    b. De tweede toewijzing vindt plaats wanneer deze tabellaire gegevens worden opgenomen in azure Data Explorer. Toewijzing wordt uitgevoerd op basis van [regels voor CSV-toewijzing](/azure/kusto/management/mappings#csv-mapping). Zelfs als de bron gegevens geen CSV-indeling hebben, Azure Data Factory de gegevens naar een tabel indeling geconverteerd. Daarom is CSV-toewijzing de enige relevante toewijzing in deze fase. Ga als volgt te werk:

    * Beschrijving Voeg onder **Azure Data Explorer (Kusto) Sink-eigenschappen**de relevante **naam van de opname toewijzing** toe, zodat de kolom toewijzing kan worden gebruikt.

    * Als de naam van de **opname toewijzing** niet is opgegeven, wordt de volg orde *op naam* toewijzing gebruikt die in de sectie **kolom toewijzingen** is gedefinieerd. Als een toewijzing *via een naam* mislukt, probeert Azure Data Explorer de gegevens op te nemen in een *positie* volgorde (dat wil zeggen, wordt toegewezen door de standaard instelling).

    * Selecteer **Volgende**.

    ![Het deel venster met de doel-gegevensset kolom toewijzing](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Ga als volgt te werk in het deel venster **instellingen** :

    a. Geef bij **instellingen voor fout tolerantie**de relevante instellingen op.

    b. Onder **prestatie-instellingen**is **fase ring inschakelen** niet van toepassing en **Geavanceerde instellingen** omvatten kosten overwegingen. Als u geen specifieke vereisten hebt, laat u deze instellingen ongewijzigd.

    c. Selecteer **Volgende**.

    ![Het deel venster instellingen voor kopiëren van gegevens](media/data-factory-load-data/copy-data-settings.png)

1. Controleer de instellingen in het deel venster **samen vatting** en selecteer vervolgens **volgende**.

    ![Het deel venster samen vatting van Kopieer gegevens](media/data-factory-load-data/copy-data-summary.png)

1. Ga als volgt te werk in het deel venster **implementatie voltooid** :

    a. Als u wilt overschakelen naar het tabblad **monitor** en de status van de pijp lijn (dat wil zeggen, voortgang, fouten en gegevens stroom) wilt weer geven, selecteert u **monitor**.

    b. Als u gekoppelde services, gegevens sets en pijp lijnen wilt bewerken, selecteert u **pijp lijn bewerken**.

    c. Selecteer **volt ooien** om de taak gegevens kopiëren te volt ooien.

    ![Het deel venster implementatie voltooid](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Azure Data Explorer-connector](/azure/data-factory/connector-azure-data-explorer) in azure Data Factory.
* Meer informatie over het bewerken van gekoppelde services, gegevens sets en pijp lijnen in de [Data Factory-gebruikers interface](/azure/data-factory/quickstart-create-data-factory-portal).
* Meer informatie over [Azure Data Explorer query's](/azure/data-explorer/web-query-data) voor het uitvoeren van gegevens query's.
