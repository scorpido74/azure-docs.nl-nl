---
title: Gegevens uit Azure Data Factory kopiëren naar Azure Data Explorer
description: In dit artikel leert u hoe u gegevens in neemt (laden) in Azure Data Explorer met behulp van het kopieerhulpprogramma Azure Data Factory.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 860b1a579d9c8cee6c6e80ae4c4e7fdd7949d5c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71300587"
---
# <a name="copy-data-to-azure-data-explorer-by-using-azure-data-factory"></a>Gegevens kopiëren naar Azure Data Explorer met Azure Data Factory 

Azure Data Explorer is een snelle, volledig beheerde, data-analytics service. Het biedt real-time analyse van grote hoeveelheden gegevens die uit vele bronnen worden gestreamd, zoals toepassingen, websites en IoT-apparaten. Met Azure Data Explorer u eenvoudig gegevens verkennen en patronen en afwijkingen identificeren om producten te verbeteren, klantervaringen te verbeteren, apparaten te controleren en de bewerkingen te verbeteren. Het helpt je nieuwe vragen te verkennen en binnen enkele minuten antwoorden te krijgen. 

Azure Data Factory is een volledig beheerde, cloudgebaseerde, data-integratieservice. U deze gebruiken om uw Azure Data Explorer-database te vullen met gegevens uit uw bestaande systeem. Het kan u helpen tijd te besparen wanneer u analyseoplossingen bouwt.

Wanneer u gegevens laadt in Azure Data Explorer, biedt Data Factory de volgende voordelen:

* **Eenvoudige installatie:** Krijg een intuïtieve wizard in vijf stappen zonder scripting vereist.
* **Rich data store support:** Get built-in support for a rich set of on-premises and cloud-based data stores. Zie de tabel [met ondersteunde gegevensarchieven](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)voor een gedetailleerde lijst .
* **Veilig en compatibel:** gegevens worden overgedragen via HTTPS of Azure ExpressRoute. De wereldwijde aanwezigheid van de service zorgt ervoor dat uw gegevens nooit de geografische grens verlaten.
* **Hoge prestaties**: de snelheid voor het laden van gegevens is maximaal 1 gigabyte per seconde (GBps) in Azure Data Explorer. Zie [Activiteitsprestaties kopiëren](/azure/data-factory/copy-activity-performance)voor meer informatie .

In dit artikel gebruikt u het hulpprogramma Gegevenscelkopiegegevens gegevens in de fabriek om gegevens van Amazon Simple Storage Service (S3) in Azure Data Explorer te laden. U een soortgelijk proces volgen om gegevens uit andere gegevensarchieven te kopiëren, zoals:
* [Azure Blob-opslag](/azure/data-factory/connector-azure-blob-storage)
* [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)
* [Azure SQL-gegevensmagazijn](/azure/data-factory/connector-azure-sql-data-warehouse)
* [Google BigQuery](/azure/data-factory/connector-google-bigquery)
* [Oracle](/azure/data-factory/connector-oracle)
* [Bestandssysteem](/azure/data-factory/connector-file-system)

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* [Een Azure Data Explorer-cluster en -database](create-cluster-database-portal.md).
* Een bron van gegevens.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Meld u aan bij [Azure Portal](https://ms.portal.azure.com).

1. Selecteer in het linkerdeelvenster De optie **Een gegevensfabriek voor bronnen** > **analyse** > **maken**.

   ![Een gegevensfabriek maken in de Azure-portal](media/data-factory-load-data/create-adf.png)

1. Geef in het **fabrieksdeelvenster Nieuwe gegevens** waarden op voor de velden in de volgende tabel:

   ![Het deelvenster 'Nieuwe gegevensfabriek'](media/data-factory-load-data/my-new-data-factory.png)  

   | Instelling  | Waarde in te voeren  |
   |---|---|
   | **Naam** | Voer in het vak een wereldwijd unieke naam in voor uw gegevensfabriek. Als u een fout ontvangt, *is de naam \"LoadADXDemo\" van de gegevensfabriek niet beschikbaar,* voert u een andere naam in voor de gegevensfabriek. Zie [Gegevensfabriek naamgevingsregels voor](/azure/data-factory/naming-rules)regels voor het benoemen van Gegevensfabriek .|
   | **Abonnement** | Selecteer in de vervolgkeuzelijst het Azure-abonnement waarin u de gegevensfabriek wilt maken. |
   | **Resourcegroep** | Selecteer **Nieuw maken**en voer de naam van een nieuwe resourcegroep in. Als u al een resourcegroep hebt, selecteert u **Bestaande gebruiken**. |
   | **Versie** | Selecteer **V2**in de vervolgkeuzelijst . |  
   | **Locatie** | Selecteer in de vervolgkeuzelijst de locatie voor de gegevensfabriek. Alleen ondersteunde locaties worden weergegeven in de lijst. De gegevensopslag die door de gegevensfabriek worden gebruikt, kunnen in andere locaties of regio's bestaan. |

1. Selecteer **Maken**.

1. Als u het creatieproces wilt controleren, selecteert u **Meldingen** op de werkbalk. Nadat u de gegevensfabriek hebt gemaakt, selecteert u deze.
   
   Het deelvenster **Gegevensfabriek** wordt geopend.

   ![Het deelvenster Gegevensfabriek](media/data-factory-load-data/data-factory-home-page.png)

1. Als u de toepassing in een apart deelvenster wilt openen, selecteert u de tegel **Auteur & Monitor.**

## <a name="load-data-into-azure-data-explorer"></a>Gegevens laden in Azure Data Explorer

U gegevens uit vele soorten [gegevensarchieven](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) laden in Azure Data Explorer. In dit artikel wordt besproken hoe u gegevens van Amazon S3 laden.

U uw gegevens op een van de volgende manieren laden:

* Selecteer in de gebruikersinterface van Azure Data Factory in het linkerdeelvenster het pictogram **Auteur,** zoals weergegeven in de sectie Een gegevensfabriek maken van [Een gegevensfabriek maken met de gebruikersinterface](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)van Azure Data Factory.
* In het hulpprogramma Gegevens kopiëren van Azure Data Factory, zoals weergegeven in [Het gereedschap Gegevens kopiëren gebruiken om gegevens te kopiëren.](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)

### <a name="copy-data-from-amazon-s3-source"></a>Gegevens van Amazon S3 kopiëren (bron)

1. Open in het deelvenster **Laten we aan de slag** het gereedschap Gegevens kopiëren door Gegevens kopiëren **te**selecteren.

   ![De knop Gegevens kopiëren](media/data-factory-load-data/copy-data-tool-tile.png)

1. Voer in het deelvenster **Eigenschappen** in het vak **Taaknaam** een naam in en selecteer **Volgende**.

    ![Het deelvenster Gegevenseigenschappen kopiëren](media/data-factory-load-data/copy-from-source.png)

1. Selecteer in het deelvenster **Brongegevensarchief** de optie **Nieuwe verbinding maken**.

    ![Het deelvenster Gegevens kopiëren "Brongegevensarchief"](media/data-factory-load-data/source-create-connection.png)

1. Selecteer **Amazon S3**en selecteer **Doorgaan**.

    ![Het deelvenster Nieuwe gekoppelde service](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. Ga in het deelvenster **Nieuwe Gekoppelde Service (Amazon S3)** als volgt te werk:

    ![Amazon S3-gekoppelde service opgeven](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    a. Voer in het vak **Naam** de naam van uw nieuwe gekoppelde service in.

    b. Selecteer de waarde in de vervolgkeuzelijst **Runtime verbinding via integratie.**

    c. Voer in het vak **Toegangssleutel-id** de waarde in.
    
    > [!NOTE]
    > Selecteer in Amazon S3 uw toegangssleutel uw Amazon-gebruikersnaam op de navigatiebalk en selecteer **Mijn beveiligingsreferenties.**
    
    d. Voer in het vak **Geheime toegangssleutel** een waarde in.

    e. Als u de gekoppelde serviceverbinding wilt testen die u hebt gemaakt, selecteert u **Verbinding testen**.

    f. Selecteer **Finish**.
    
      In het deelvenster **Brongegevensarchief** wordt uw nieuwe AmazonS31-verbinding weergegeven. 

1. Selecteer **Volgende**.

   ![Verbinding met brongegevensarchief gemaakt](media/data-factory-load-data/source-data-store-created-connection.png)

1. Ga als volgt te werk in **het invoerbestand of mapvenster kiezen:**

    a. Blader naar het bestand of de map die u wilt kopiëren en selecteer het vervolgens.

    b. Selecteer het gewenste kopieergedrag. Controleer of het selectievakje **Binaire kopie** is gewist.

    c. Selecteer **Volgende**.

    ![Het invoerbestand of de invoermap kiezen](media/data-factory-load-data/source-choose-input-file.png)

1. Selecteer in het deelvenster **Instellingen voor bestandsindeling** de relevante instellingen voor uw bestand. en selecteer **Volgende**.

   ![Het deelvenster Instellingen voor bestandsindeling](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>Gegevens kopiëren naar Azure Data Explorer (doel)

De nieuwe gekoppelde Azure Data Explorer-service is gemaakt om de gegevens te kopiëren naar de azure data explorer-doeltabel (sink) die in deze sectie is opgegeven.

#### <a name="create-the-azure-data-explorer-linked-service"></a>De gekoppelde Azure Data Explorer-service maken

Ga als volgt te werk om de gekoppelde Azure Data Explorer-service te maken.

1. Als u een bestaande verbinding met het gegevensarchief wilt gebruiken of een nieuw gegevensarchief wilt opgeven, selecteert u in het deelvenster **Doelgegevensarchief** de optie **Nieuwe verbinding maken**.

    ![Deelvenster Doelgegevensarchief](media/data-factory-load-data/destination-create-connection.png)

1. Selecteer **Azure Data Explorer**in het deelvenster Nieuwe **gekoppelde service** en selecteer **Doorgaan**.

    ![Het nieuwe gekoppelde servicevenster](media/data-factory-load-data/adx-select-new-linked-service.png)

1. Ga in het deelvenster **Nieuwe Gekoppelde Service (Azure Data Explorer)** als volgt te werk:

    ![Het nieuwe deelvenster Gekoppelde service van Azure Data Explorer](media/data-factory-load-data/adx-new-linked-service.png)

   a. Voer in het vak **Naam** een naam in voor de gekoppelde Azure Data Explorer-service.

   b. Ga **onder accountselectiemethode**een van de volgende handelingen uit: 

    * Selecteer **Uit Azure-abonnement** en selecteer vervolgens in de vervolgkeuzelijsten uw **Azure-abonnement** en uw **cluster**. 

        > [!NOTE]
        > De **vervolgkeuzelijst Cluster** bevat alleen clusters die aan uw abonnement zijn gekoppeld.

    * Selecteer **Handmatig invoeren**en voer vervolgens uw eindpunt **in**.

   c. Voer **in** het vak Tenant de tenantnaam in.

   d. Voer in het vak **Servicehoofd-id** de serviceprincipal-id in.

   e. Selecteer **Hoofdsleutel service** en voer in het vak **Servicehoofdsleutel** de waarde voor de sleutel in.

   f. Selecteer uw databasenaam in de vervolgkeuzelijst **Database.** U ook het selectievakje **Bewerken** inschakelen en de databasenaam invoeren.

   g. Als u de gekoppelde serviceverbinding wilt testen die u hebt gemaakt, selecteert u **Verbinding testen**. Als u verbinding maken met uw gekoppelde service, wordt in het deelvenster een groen vinkje en een geslaagd bericht **verbinding** weergegeven.

   h. Selecteer **Voltooien** om de gekoppelde servicecreatie te voltooien.

    > [!NOTE]
    > De serviceprincipal wordt door Azure Data Factory gebruikt om toegang te krijgen tot de Azure Data Explorer-service. Als u een serviceprincipal wilt maken, gaat u [naar het maken van een Azure Active Directory (Azure AD) serviceprincipal](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal). Gebruik de Azure Key Vault-methode niet.

#### <a name="configure-the-azure-data-explorer-data-connection"></a>De gegevensverbinding van Azure Data Explorer configureren

Nadat u de gekoppelde serviceverbinding hebt gemaakt, wordt het deelvenster **Doelgegevensarchief** geopend en is de verbinding die u hebt gemaakt beschikbaar voor gebruik. Ga als volgt te werk om de verbinding te configureren.

1. Selecteer **Volgende**.

    ![Het deelvenster Azure Data Explorer 'Doelgegevensarchief'](media/data-factory-load-data/destination-data-store.png)

1. Stel in het **tabeltoewijzingsvenster** de naam van de doeltabel in en selecteer **Volgende**.

    ![Het deelvenster Doelgegevensset 'Tabeltoewijzing'](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. In het **kolomtoewijzingsvenster** vinden de volgende toewijzingen plaats:

    a. De eerste toewijzing wordt uitgevoerd door Azure Data Factory volgens de [azure data factory-schematoewijzing.](/azure/data-factory/copy-activity-schema-and-type-mapping) Ga als volgt te werk:

    * Stel de **kolomtoewijzingen** in voor de doeltabel Azure Data Factory. De standaardtoewijzing wordt weergegeven van bron naar de doeltabel Azure Data Factory.

    * Annuleer de selectie van de kolommen die u niet nodig hebt om uw kolomtoewijzing te definiëren.

    b. De tweede toewijzing vindt plaats wanneer deze tabelgegevens worden ingenomen in Azure Data Explorer. Toewijzing wordt uitgevoerd volgens [csv-toewijzingsregels.](/azure/kusto/management/mappings#csv-mapping) Zelfs als de brongegevens niet in CSV-indeling zijn, zet Azure Data Factory de gegevens om in een tabelindeling. Daarom is CSV mapping de enige relevante mapping in dit stadium. Ga als volgt te werk:

    * (Optioneel) Voeg onder **Azure Data Explorer (Kusto) sink-eigenschappen**de relevante **toewijzingsnaam inname** toe, zodat kolomtoewijzing kan worden gebruikt.

    * Als **de naam van de toewijzing van inname** niet is opgegeven, wordt de toewijzingsvolgorde met naam voor *naam* gebruikt die is gedefinieerd in de sectie **Kolomtoewijzingen.** Als *de toewijzing met naamsnaam* mislukt, probeert Azure Data Explorer de gegevens in een *positievolgorde per kolom* in te nemen (dat wil zeggen dat deze wordt toegewezen op positie als standaard).

    * Selecteer **Volgende**.

    ![Het deelvenster Doelgegevensset 'Kolomtoewijzing'](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. Ga als volgt te werk in het deelvenster **Instellingen:**

    a. Voer **onder Fouttolerantieinstellingen**de relevante instellingen in.

    b. Onder **Prestatie-instellingen** **is fasering inschakelen** niet van toepassing en geavanceerde **instellingen** bevatten kostenoverwegingen. Als u geen specifieke vereisten hebt, laat u deze instellingen zoals deze zijn.

    c. Selecteer **Volgende**.

    ![Het deelvenster Gegevens kopiëren "Instellingen"](media/data-factory-load-data/copy-data-settings.png)

1. Controleer in het deelvenster **Overzicht** de instellingen en selecteer **Volgende**.

    ![Het deelvenster Gegevens kopiëren "Overzicht"](media/data-factory-load-data/copy-data-summary.png)

1. Ga als volgt te werk in het **deelvenster Voltooid voltooien:**

    a. Als u wilt overschakelen naar het tabblad **Monitor** en de status van de pijplijn wilt weergeven (dat wil zeggen voortgang, fouten en gegevensstroom), selecteert u **Controleren**.

    b. Als u gekoppelde services, gegevenssets en pijplijnen wilt bewerken, selecteert u **Pijplijn bewerken**.

    c. Selecteer **Voltooien** om de taak voor kopieergegevens te voltooien.

    ![Het deelvenster Voltooid implementeren](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Azure Data Explorer-connector](/azure/data-factory/connector-azure-data-explorer) in Azure Data Factory.
* Meer informatie over het bewerken van gekoppelde services, gegevenssets en pijplijnen in de [gebruikersinterface van datafabriek](/azure/data-factory/quickstart-create-data-factory-portal).
* Meer informatie over [Azure Data Explorer-query's](/azure/data-explorer/web-query-data) voor gegevensquery's.
