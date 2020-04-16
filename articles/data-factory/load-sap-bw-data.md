---
title: Gegevens laden van SAP Business Warehouse
description: Azure Data Factory gebruiken om gegevens uit SAP Business Warehouse (BW) te kopiëren
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 96b23696164514ad2f16de72f0f76aa237ffce2e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415843"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Gegevens uit SAP Business Warehouse kopiëren met Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel ziet u hoe u Azure Data Factory gebruikt om gegevens uit SAP Business Warehouse (BW) te kopiëren via Open Hub naar Azure Data Lake Storage Gen2. U een vergelijkbaar proces gebruiken om gegevens naar andere [ondersteunde sink-gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats)te kopiëren.

> [!TIP]
> Zie [Gegevens van SAP Business Warehouse kopiëren via Open Hub met Azure Data Factory](connector-sap-business-warehouse-open-hub.md)voor algemene informatie over het kopiëren van gegevens van SAP BW, waaronder SAP BW Open Hub-integratie en deltaextractiestroom.

## <a name="prerequisites"></a>Vereisten

- **Azure Data Factory:** Als u er geen hebt, volgt u de stappen om [een gegevensfabriek](quickstart-create-data-factory-portal.md#create-a-data-factory)te maken.

- **SAP BW Open Hub Destination (OHD) met bestemmingstype "Database tabel":** Om een OHD te maken of om te controleren of uw OHD correct is geconfigureerd voor Data Factory integratie, zie de [SAP BW Open Hub Destination configuraties](#sap-bw-open-hub-destination-configurations) sectie van dit artikel.

- **De SAP BW-gebruiker heeft de volgende machtigingen nodig:**

  - Autorisatie voor Remote Function Calls (RFC) en SAP BW.
  - Machtigingen voor de activiteit Uitvoeren van het **S_SDSAUTH** autorisatieobject.

- **Een [self-hosted integration runtime (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) met SAP .NET connector 3.0**. Volg de volgende installatiestappen:

  1. Installeer en registreer de zelf gehoste ingebruikloop van integratie, versie 3.13 of hoger. (Dit proces wordt later in dit artikel beschreven.)

  2. Download de [64-bits SAP Connector voor Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) van de website van SAP en installeer deze op dezelfde computer als de zelf gehoste IR. Controleer tijdens de installatie of u **Vergaderingen installeren in GAC** selecteert in het dialoogvenster **Optionele installatiestappen,** zoals in de volgende afbeelding wordt weergegeven:

     ![Dialoogvenster SAP .NET-connector instellen](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Doe een volledig exemplaar van SAP BW Open Hub

Ga in Azure Portal naar uw data factory. Selecteer **Auteur &-monitor** om de gebruikersinterface van gegevensfabriek op een afzonderlijk tabblad te openen.

1. Selecteer **op** de pagina Laten aan de slag de optie **Gegevens kopiëren** om het gereedschap Gegevens kopiëren te openen.

2. Geef op de pagina **Eigenschappen** een **taaknaam**op en selecteer **Volgende**.

3. Selecteer op de pagina **Brongegevensarchief** de optie **+Nieuwe verbinding maken**. Selecteer **SAP BW Open Hub** in de connectorgalerie en selecteer **Doorgaan**. Als u de connectors wilt filteren, u **SAP** typen in het zoekvak.

4. Voer op de pagina **SAP BW Open Hub-verbinding opgeven** deze stappen uit om een nieuwe verbinding te maken.

   ![Sap BW Open Hub-servicepagina maken](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Selecteer in de **runtime-lijst Verbinding via integratie** een bestaande zelfgehoste IR. Of kies ervoor om er een te maken als je er nog geen hebt.

      Als u een nieuwe zelf gehoste IR wilt maken, selecteert u **+Nieuw**en selecteert u **Vervolgens Zelf gehost**. Voer een **naam**in en selecteer **Volgende**. Selecteer **Express-installatie die** u op de huidige computer wilt installeren of volg de opgegeven **handmatige installatiestappen.**

      Zoals vermeld in [Voorwaarden,](#prerequisites)zorg ervoor dat u SAP Connector voor Microsoft .NET 3.0 geïnstalleerd op dezelfde computer waar de zelf gehoste IR wordt uitgevoerd.

   2. Vul de naam SAP **BW-server**, **systeemnummer**, **client-id,** **taal** (indien anders dan **NL**), **Gebruikersnaam**en **Wachtwoord**in .

   3. Selecteer **Verbinding testen** om de instellingen te valideren en selecteer **Voltooien**.

   4. Er wordt een nieuwe verbinding gemaakt. Selecteer **Next**.

5. Blader op de pagina **Open hubbestemmingen** selecteren door de Open Hub-bestemmingen die beschikbaar zijn in uw SAP BW. Selecteer de OHD om gegevens uit te kopiëren en selecteer **Volgende**.

   ![De tabel SAP BW Open Hub Destination selecteren](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Geef een filter op als u er een nodig hebt. Als uw OHD alleen gegevens bevat van één DTP-uitvoering (Data-Transfer Process) met één aanvraag-id, of als u zeker weet dat uw DTP klaar is en u de gegevens wilt kopiëren, schakelt u het selectievakje **Laatste aanvraag uitsluiten** uit.

   Meer informatie over deze instellingen vindt u in het gedeelte [SAP BW Open Hub Destination-configuraties](#sap-bw-open-hub-destination-configurations) van dit artikel. Selecteer **Valideren** om te controleren welke gegevens worden geretourneerd. Selecteer **vervolgens Volgende**.

   ![SAP BW Open Hub-filter configureren](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Selecteer op de pagina **Bestemmingsgegevensarchief** **+Nieuwe verbinding** > maken**Azure Data Lake Storage Gen2** > **Continue**.

8. Voer op de pagina **Azure Data Lake Storage-verbindingspagina** azure data lake storage uitvoeren deze stappen uit om een verbinding te maken.

   ![Een aDLS Gen2 gekoppelde servicepagina maken](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Selecteer uw Account met Data Lake Storage Gen2 in de vervolgkeuzelijst **Naam.**
   2. Selecteer **Voltooien** om de verbinding te maken. Selecteer **vervolgens Volgende**.

9. Voer op de pagina **Het uitvoerbestand of de map kiezen** tekst in als naam van **de** uitvoermap. Selecteer **vervolgens Volgende**.

   ![Pagina uitvoermap kiezen](media/load-sap-bw-data/choose-output-folder.png)

10. Selecteer **op** de pagina **Bestandsindeling** volgende om de standaardinstellingen te gebruiken.

    ![Pagina sink-indeling opgeven](media/load-sap-bw-data/specify-sink-format.png)

11. Vouw op de pagina **Instellingen** **prestatie-instellingen**uit. Voer een waarde in voor **Mate van kopieerparallellisme,** zoals 5 om parallel te laden van SAP BW. Selecteer **vervolgens Volgende**.

    ![Kopieerinstellingen configureren](media/load-sap-bw-data/configure-copy-settings.png)

12. Bekijk de instellingen op de pagina **Samenvatting** . Selecteer **vervolgens Volgende**.

13. Selecteer **op** de pagina Implementatie de optie **Monitor** om de pijplijn te controleren.

    ![De pagina Implementatie](media/load-sap-bw-data/deployment.png)

14. Het tabblad **Monitor** aan de linkerkant van de pagina wordt automatisch geselecteerd. De kolom **Acties** bevat koppelingen om activiteiten-rundetails weer te geven en de pijplijn opnieuw uit te voeren.

    ![Weergave pijplijnbewaking](media/load-sap-bw-data/pipeline-monitoring.png)

15. Als u activiteitsruns wilt weergeven die zijn gekoppeld aan de pijplijnrun, selecteert u **Activiteitsuitvoeringweergeven** in de kolom **Acties.** Omdat er slechts één activiteit (kopieeractiviteit) in de pijplijn is, ziet u slechts één vermelding in de lijst. Als u wilt teruggaan naar de weergave **pijplijnuitvoeringen,** selecteert u de koppeling Pijplijnen bovenaan. Selecteer **Vernieuwen** om de lijst te vernieuwen.

    ![Beeldscherm activiteitsbewaking](media/load-sap-bw-data/activity-monitoring.png)

16. Als u de uitvoeringsgegevens voor elke kopieeractiviteit wilt controleren, selecteert u de koppeling **Details,** een pictogram voor een bril onder **Acties** in de weergave activiteitsbewaking. Beschikbare details omvatten het gegevensvolume dat van de bron naar de gootsteen wordt gekopieerd, gegevensdoorvoer, uitvoeringsstappen en duur en gebruikte configuraties.

    ![Details over activiteitenbewaking](media/load-sap-bw-data/activity-monitoring-details.png)

17. Als u de **maximale aanvraag-id wilt**weergeven, gaat u terug naar de activiteitsbewakingsweergave en selecteert **u Uitvoer** onder **Acties**.

    ![Scherm activiteitsuitvoer](media/load-sap-bw-data/activity-output.png)

    ![Weergave activiteitsuitvoerdetails](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Incrementele kopie van SAP BW Open Hub

> [!TIP]
> Zie [sap BW Open Hub-connectordeltaextractiestroom](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) om te leren hoe de SAP BW Open Hub-connector in Data Factory incrementele gegevens van SAP BW kopieert. Dit artikel kan u ook helpen inzicht te krijgen in de basisconfiguratie van de connector.

Laten we nu doorgaan met het configureren van incrementele kopie van SAP BW Open Hub.

Incrementele kopie maakt gebruik van een "hoogwatermerk" mechanisme dat is gebaseerd op de **aanvraag-ID**. Die ID wordt automatisch gegenereerd in SAP BW Open Hub Destination door de DTP. In het volgende diagram wordt deze werkstroom weergegeven:

![Grafiek van incrementele werkstroom voor kopiëren](media/load-sap-bw-data/incremental-copy-workflow.png)

Selecteer in de gegevensfabriek **Laten we aan de slag gaan** de optie Pijplijn maken van **sjabloon** om de ingebouwde sjabloon te gebruiken.

1. Zoek naar **SAP BW** om de **incrementele kopie van SAP BW naar Azure Data Lake Storage Gen2-sjabloon te** zoeken en te selecteren. Met deze sjabloon worden gegevens overgekopieerd naar Azure Data Lake Storage Gen2. U een vergelijkbare werkstroom gebruiken om naar andere gootsteentypen te kopiëren.

2. Selecteer of maak op de hoofdpagina van de sjabloon de volgende drie verbindingen en selecteer **Deze sjabloon gebruiken** in de rechterbenedenhoek van het venster.

   - **Azure Blob-opslag:** in deze walkthrough gebruiken we Azure Blob-opslag om het hoge watermerk op te slaan, de *max gekopieerde aanvraag-id*.
   - **SAP BW Open Hub**: Dit is de bron om gegevens van te kopiëren. Raadpleeg de vorige full-copy walkthrough voor gedetailleerde configuratie.
   - **Azure Data Lake Storage Gen2**: Dit is de gootsteen om gegevens naar te kopiëren. Raadpleeg de vorige full-copy walkthrough voor gedetailleerde configuratie.

   ![Incrementele kopie van SAP BW-sjabloon](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Deze sjabloon genereert een pijplijn met de volgende drie activiteiten en maakt ze geketend on-succes: *Lookup,* *Copy Data*, en *Web*.

   Ga naar het tabblad **Parameters voor** pijplijn. U ziet alle configuraties die u moet leveren.

   ![Incrementele kopie van SAP BW-configuratie](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName:** geef de naam van de open hub-tabel op om gegevens van te kopiëren.

   - **Data_Destination_Container:** Geef de doellocatie op naar de Azure Data Lake Storage Gen2-container om gegevens naar te kopiëren. Als de container niet bestaat, maakt de kopieeractiviteit Gegevensfabriek er een tijdens de uitvoering.
  
   - **Data_Destination_Directory:** Geef het mappad op onder de Azure Data Lake Storage Gen2-container waarnaar u gegevens wilt kopiëren. Als het pad niet bestaat, maakt de kopieeractiviteit Gegevensfabriek een pad tijdens de uitvoering.
  
   - **HighWatermarkBlobContainer**: Geef de container op om de hoogwatermerkwaarde op te slaan.

   - **HighWatermarkBlobDirectory:** Geef het mappad onder de container op om de hoogwatermerkwaarde op te slaan.

   - **HighWatermarkBlobName:** geef de blobnaam op om de `requestIdCache.txt`hoge watermerkwaarde op te slaan, zoals . Ga in Blob-opslag naar het bijbehorende pad van HighWatermarkBlobContainer+HighWatermarkBlobDirectory+HighWatermarkBlobName, zoals *container/pad/requestIdCache.txt*. Maak een blob met inhoud 0.

      ![Blobinhoud](media/load-sap-bw-data/blob.png)

   - **LogicAppURL:** in deze sjabloon gebruiken we WebActiviteit om Azure Logic Apps aan te roepen om de hoogwatermerkwaarde in Blob-opslag in te stellen. U azure SQL-database ook gebruiken om deze op te slaan. Gebruik een opgeslagen procedureactiviteit om de waarde bij te werken.

      U moet eerst een logische app maken, zoals de volgende afbeelding laat zien. Plak vervolgens in de **HTTP-POST-URL**.

      ![Configuratie van logische app](media/load-sap-bw-data/logic-app-config.png)

      1. Ga naar Azure Portal. Selecteer een nieuwe **Logic Apps-service.** Selecteer **+Blank Logic App** om naar Logic Apps **Designer**te gaan .

      2. Maak een trigger van **Wanneer een HTTP-aanvraag wordt ontvangen**. Geef de HTTP-aanvraaginstantie als volgt op:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Voeg een **blobactie maken** toe. Voor **Mappad** en **Blob-naam**gebruikt u dezelfde waarden die u eerder hebt geconfigureerd in *HighWatermarkBlobContainer+HighWatermarkBlobDirectory* en *HighWatermarkBlobName*.

      4. Selecteer **Opslaan**. Kopieer vervolgens de waarde van **de HTTP POST-URL** die u wilt gebruiken in de pijplijn Gegevensfabriek.

4. Nadat u de pijplijnparameters gegevensfabriek hebt opgemaakt, selecteert u **Foutopsporing** > **voltooien** om een run aan te roepen om de configuratie te valideren. Of selecteer **Publiceren** om alle wijzigingen te publiceren en selecteer **Trigger toevoegen** om een uitvoering uit te voeren.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW Open Hub Destination-configuraties

In deze sectie wordt de configuratie van de SAP BW-zijde geïntroduceerd om de SAP BW Open Hub-connector in Data Factory te gebruiken om gegevens te kopiëren.

### <a name="configure-delta-extraction-in-sap-bw"></a>Deltaextractie configureren in SAP BW

Als u zowel historische kopie als incrementele kopie of alleen incrementele kopie nodig hebt, configureert u deltaextractie in SAP BW.

1. Maak de open hubbestemming. U de OHD maken in SAP Transaction RSA1, die automatisch het vereiste transformatie- en gegevensoverdrachtproces maakt. Gebruik de volgende instellingen:

   - **ObjectType:** U elk objecttype gebruiken. Hier gebruiken we **InfoCube** als voorbeeld.
   - **Doeltype**: Selecteer **databasetabel**.
   - **Sleutel van de tabel**: Selecteer **technische sleutel**.
   - **Extractie**: Selecteer **Gegevens bijhouden en Records invoegen in tabel**.

   ![Dialoogvenster SAP BW OHD-deltaextractie maken](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Dialoogvenster SAP BW OHD delta2-extractie maken](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   U het aantal parallellopende SAP-werkprocessen voor de DTP verhogen:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Plan de DTP in procesketens.

   Een delta DTP voor een kubus werkt alleen als de benodigde rijen niet zijn gecomprimeerd. Zorg ervoor dat de BW-kubuscompressie niet wordt uitgevoerd voordat de DTP naar de open hub-tabel gaat. De eenvoudigste manier om dit te doen is door de DTP te integreren in uw bestaande procesketens. In het volgende voorbeeld wordt de DTP (naar de OHD) ingevoegd in de procesketen tussen de stappen *Rollup Aanpassen* (aggregaat) en *Samenvouwen* (kubuscompressie).

   ![SAP BW-procesketenstroomdiagram maken](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Volledige extractie configureren in SAP BW

Naast delta extractie, wilt u misschien een volledige extractie van dezelfde SAP BW InfoProvider. Dit geldt meestal als u volledige kopie wilt doen, maar niet incrementeel, of als u deltaextractie opnieuw wilt [synchroniseren.](#resync-delta-extraction)

Je niet meer dan één DTP hebben voor dezelfde OHD. Dus, moet u een extra OHD voor delta extractie.

![Sap BW OHD vol maken](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Kies voor een volledige lading OHD andere opties dan deltaextractie:

- In OHD: Stel de optie **Extractie** in **op Gegevens verwijderen en Records invoegen**. Anders worden gegevens vaak geëxtraheerd wanneer u de DTP in een BW-procesketen herhaalt.

- In de DTP: **Stel extractiemodus** **in op Volledig**. U moet de automatisch gemaakte DTP direct na het maken van de OHD van **Delta** naar **Volledig** wijzigen, zoals in deze afbeelding wordt weergegeven:

   ![Dialoogvenster SAP BW OHD maken geconfigureerd voor 'Volledige' extractie](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- In de BW Open Hub-connector van Data Factory: **Laatste aanvraag uitsluiten**uitschakelen . Anders wordt er niets gewonnen.

U voert de volledige DTP meestal handmatig uit. U ook een procesketen maken voor de volledige DTP. Het is meestal een aparte keten die onafhankelijk is van uw bestaande procesketens. Zorg er in beide gevallen *voor dat de DTP klaar is voordat u de extractie start met het gebruik van Data Factory-kopie.* Anders worden alleen gedeeltelijke gegevens gekopieerd.

### <a name="run-delta-extraction-the-first-time"></a>Voer deltaextractie de eerste keer uit

De eerste deltawinning is technisch gezien een *volledige extractie.* Standaard sluit de SAP BW Open Hub-connector het laatste verzoek uit wanneer deze gegevens kopieert. Voor de eerste deltaextractie worden geen gegevens geëxtraheerd door de kopieeractiviteit Gegevensfabriek totdat een volgende DTP deltagegevens genereert in de tabel met een afzonderlijke aanvraag-ID. Er zijn twee manieren om dit scenario te vermijden:

- Schakel de optie **Laatste aanvraag uitsluiten** voor de eerste deltaextractie uit. Zorg ervoor dat de eerste delta DTP klaar is voordat u de deltaextractie de eerste keer start.
-  Gebruik de procedure voor het opnieuw synchroniseren van de deltaextractie, zoals beschreven in het volgende gedeelte.

### <a name="resync-delta-extraction"></a>Delta-extractie opnieuw synchroniseren

De volgende scenario's wijzigen de gegevens in SAP BW-kubussen, maar worden niet in aanmerking genomen door de delta DTP:

- SAP BW selectieve verwijdering (van rijen met behulp van een filter voorwaarde)
- SAP BW-verzoek verwijdering (van defecte verzoeken)

Een SAP Open Hub Destination is geen data-mart-controlled data target (in alle SAP BW support pakketten sinds 2015). U gegevens dus uit een kubus verwijderen zonder de gegevens in de OHD te wijzigen. Vervolgens moet u de gegevens van de kubus opnieuw synchroniseren met Gegevensfabriek:

1. Voer een volledige extractie uit in Data Factory (met behulp van een volledige DTP in SAP).
2. Verwijder alle rijen in de open hub-tabel voor de delta DTP.
3. Stel de status van de delta DTP in **op Opgehaald**.

Daarna werken alle volgende delta DTP's en Data Factory delta-extracties zoals verwacht.

Als u de status van de delta DTP wilt instellen op **Ophalen,** u de volgende optie gebruiken om de delta DTP handmatig uit te voeren:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Volgende stappen

Meer informatie over ondersteuning voor SAP BW Open Hub-connectoren:

> [!div class="nextstepaction"]
>[SAP Business Warehouse Open Hub-connector](connector-sap-business-warehouse-open-hub.md)
