---
title: On-premises SQL Server
titleSuffix: ML Studio (classic) - Azure
description: Gebruik gegevens uit een on-premises SQL Server-database om geavanceerde analyses uit te voeren met Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 648dbdb7e9e9d1b20c55d3fa5b314b7e4657d5e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204179"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Analyses uitvoeren met Azure Machine Learning Studio (klassiek) met behulp van een on-premises SQL Server-database

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Vaak willen bedrijven die werken met on-premises gegevens profiteren van de schaal en flexibiliteit van de cloud voor hun machine learning-workloads. Maar ze willen hun huidige bedrijfsprocessen en workflows niet verstoren door hun on-premises gegevens naar de cloud te verplaatsen. Azure Machine Learning Studio (klassiek) ondersteunt nu het lezen van uw gegevens uit een on-premises SQL Server-database en vervolgens het trainen en scoren van een model met deze gegevens. U hoeft de gegevens niet langer handmatig te kopiëren en te synchroniseren tussen de cloud en uw on-premises server. In plaats daarvan kan de module **Gegevens importeren** in Azure Machine Learning Studio (klassiek) nu rechtstreeks uit uw on-premises SQL Server-database lezen voor uw trainings- en scoringstaken.

In dit artikel vindt u een overzicht van het binnendringen van on-premises SQL-servergegevens in Azure Machine Learning Studio (klassiek). Het gaat ervan uit dat u bekend bent met Studio (klassieke) concepten zoals werkplekken, modules, datasets, experimenten, *enz.*

> [!NOTE]
> Deze functie is niet beschikbaar voor gratis werkruimten. Zie [Azure Machine Learning Pricing](https://azure.microsoft.com/pricing/details/machine-learning/)voor meer informatie over prijzen en lagen van Machine Learning.
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>De runtime voor self-hosted integratie van Data Factory installeren
Als u toegang wilt krijgen tot een on-premises SQL Server-database in Azure Machine Learning Studio (klassiek), moet u de Data Factory Self-hosted Integration Runtime downloaden en installeren, voorheen bekend als de Data Management Gateway. Wanneer u de verbinding in Machine Learning Studio (klassiek) configureert, hebt u de mogelijkheid om de Integratieruntime (IR) te downloaden en te installeren via het dialoogvenster **Gegevensgateway downloaden en registreren** dat hieronder wordt beschreven.


U de IR ook van tevoren installeren door het MSI-installatiepakket te downloaden en uit te voeren vanuit het [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=39717) De MSI kan ook worden gebruikt om een bestaande IR te upgraden naar de nieuwste versie, met alle instellingen behouden.

De Runtime voor self-hosted integratie van De Data Factory heeft de volgende voorwaarden:

* De Self-Hosted Integration van De Data Factory vereist een 64-bits besturingssysteem met .NET Framework 4.6.1 of hoger.
* De ondersteunde Windows-versies van het Besturingssysteem zijn Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* De aanbevolen configuratie voor de IR-machine is ten minste 2 GHz, 4 Core CPU, 8GB RAM en 80GB schijf.
* Als de hostmachine overwintert, reageert de IR niet op gegevensverzoeken. Configureer daarom een geschikt energiebeheerschema op de computer voordat u de IR installeert. Als de machine is geconfigureerd om te overwinteren, wordt in de IR-installatie een bericht weergegeven.
* Omdat kopieeractiviteit plaatsvindt op een specifieke frequentie, volgt het resourcegebruik (CPU, geheugen) op de machine ook hetzelfde patroon met piek- en idletijden. Het gebruik van resources is ook sterk afhankelijk van de hoeveelheid gegevens die wordt verplaatst. Wanneer er meerdere kopieertaken worden uitgevoerd, ziet u dat het gebruik van resources tijdens piekuren toert. Hoewel de bovenstaande minimale configuratie technisch voldoende is, wilt u misschien een configuratie hebben met meer resources dan de minimale configuratie, afhankelijk van uw specifieke belasting voor gegevensverplaatsing.

Houd rekening met het volgende bij het instellen en gebruiken van een Self-hosted Integration Runtime van datafactory:

* U slechts één exemplaar van IR op één computer installeren.
* U één IR gebruiken voor meerdere on-premises gegevensbronnen.
* U meerdere IRs op verschillende computers verbinden met dezelfde on-premises gegevensbron.
* U configureert een IR voor slechts één werkruimte tegelijk. Momenteel kunnen IR's niet worden gedeeld op verschillende werkruimten.
* U meerdere IR's configureren voor één werkruimte. U bijvoorbeeld een IR gebruiken die tijdens de ontwikkeling is verbonden met uw testgegevensbronnen en een productie-IR wanneer u klaar bent om te operationaliseren.
* De IR hoeft niet op dezelfde machine te staan als de gegevensbron. Maar dichter bij de gegevensbron blijven, vermindert de tijd voor de gateway om verbinding te maken met de gegevensbron. We raden u aan de IR te installeren op een machine die anders is dan de machine die de on-premises gegevensbron host, zodat de gateway en de gegevensbron niet concurreren om resources.
* Als u al een IR hebt geïnstalleerd op uw computer met Power BI- of Azure Data Factory-scenario's, installeert u een afzonderlijke IR voor Azure Machine Learning Studio (klassiek) op een andere computer.

  > [!NOTE]
  > U Data Factory Self-hosted Integration Runtime en Power BI Gateway niet op dezelfde computer uitvoeren.
  >
  >
* U moet de Self-hosted Integration Runtime van Data Factory gebruiken voor Azure Machine Learning Studio (klassiek), zelfs als u Azure ExpressRoute gebruikt voor andere gegevens. U moet uw gegevensbron behandelen als een on-premises gegevensbron (die zich achter een firewall bevindt) zelfs wanneer u ExpressRoute gebruikt. Gebruik de Runtime Self-hosted Integration-integratie van Data Factory om verbinding te maken tussen Machine Learning en de gegevensbron.

In het artikel [Integratieruntime in Data Factory](../../data-factory/concepts-integration-runtime.md)vindt u gedetailleerde informatie over installatievereisten, installatiestappen en tips voor het oplossen van problemen.

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Gegevens uit uw on-premises SQL Server-database binnendringen in Azure Machine Learning
In deze walkthrough stelt u een Runtime azure Data Factory Integration in een Azure Machine Learning-werkruimte in, configureert u deze en leest u gegevens uit een on-premises SQL Server-database.

> [!TIP]
> Schakel de pop-upblokkering van uw browser `studio.azureml.net`voor . Als u de Google Chrome-browser gebruikt, downloadt en installeert u een van de verschillende plug-ins die beschikbaar zijn in Google Chrome WebStore [Klik op Eenmalige app-extensie](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory Self-hosted Integration Runtime stond voorheen bekend als Data Management Gateway. De stap voor stap tutorial zal blijven verwijzen naar het als een gateway.  

### <a name="step-1-create-a-gateway"></a>Stap 1: Een gateway maken
De eerste stap is het maken en instellen van de gateway om toegang te krijgen tot uw on-premises SQL-database.

1. Meld u aan bij [Azure Machine Learning Studio (klassiek)](https://studio.azureml.net/Home/) en selecteer de werkruimte waarin u wilt werken.
2. Klik op het **pagina-pagina INSTELLINGEN** aan de linkerkant en klik bovenaan op het tabblad **GEGEVENSGATEWAYs.**
3. Klik onder aan het scherm op **NIEUWE GEGEVENSGATEWAY.**

    ![Nieuwe gegevensgateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. Voer in het dialoogvenster **Nieuwe gegevensgateway** de **gatewaynaam** in en voeg eventueel een **beschrijving**toe. Klik op de pijl in de rechterbenedenhoek om naar de volgende stap van de configuratie te gaan.

    ![Voer de naam en beschrijving van de gateway in](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. Kopieer in het dialoogvenster Gegevensgateway downloaden en registreren de SLEUTEL GATEWAYREGISTRATIE naar het klembord.

    ![Gegevensgateway downloaden en registreren](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Als u de Microsoft Data Management Gateway nog niet hebt gedownload en geïnstalleerd, klikt u op **Gegevensbeheergateway downloaden.** Dit brengt u naar het Microsoft Download Center waar u de gatewayversie selecteren die u nodig hebt, deze downloaden en installeren. In de beginsecties van het artikel [Gegevens verplaatsen tussen on-premises bronnen en de cloud met Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md)vindt u gedetailleerde informatie over installatievereisten, installatiestappen en tips voor probleemoplossing.
7. Nadat de gateway is geïnstalleerd, wordt het Configuratiebeheer voor gegevensbeheergateway geopend en wordt het dialoogvenster **Registergateway** weergegeven. Plak de **gatewayregistratiesleutel** die u naar het klembord hebt gekopieerd en klik op **Registreren**.
8. Als u al een gateway hebt geïnstalleerd, voert u het Configuratiebeheer voor gegevensbeheergateway uit. Klik **op De sleutel wijzigen,** plak de **gatewayregistratiesleutel** die u in de vorige stap naar het klembord hebt gekopieerd en klik op **OK**.
9. Wanneer de installatie is voltooid, wordt het dialoogvenster **Gateway registreren** voor Microsoft Data Management Gateway Configuration Manager weergegeven. Plak de GATEWAY-registratiesleutel die u in een vorige stap naar het klembord hebt gekopieerd en klik op **Registreren**.

    ![Gateway registreren](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. De gatewayconfiguratie is voltooid wanneer de volgende waarden zijn ingesteld op het tabblad **Start** in Microsoft Data Management Gateway Configuration Manager:

    * **De naam van de gateway** en de naam van de **instantie** zijn ingesteld op de naam van de gateway.
    * **Registratie** is ingesteld op **Geregistreerd**.
    * **Status** is ingesteld op **Gestart**.
    * De statusbalk onderaan geeft **Verbonden aan Data Management Gateway Cloud Service** weer, samen met een groen vinkje.

      ![Data Management Gateway Manager](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio (klassiek) wordt ook bijgewerkt wanneer de registratie succesvol is.

    ![Gatewayregistratie geslaagd](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. Klik in het dialoogvenster **Gegevensgateway downloaden en registreren** op het vinkje om de installatie te voltooien. Op de pagina **Instellingen** wordt de gatewaystatus weergegeven als 'Online'. In het rechterdeelvenster vindt u status en andere nuttige informatie.

    ![Gateway-instellingen](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. Ga in het configuratiebeheer van microsoft Data Management Gateway over naar het tabblad **Certificaat.** Het certificaat dat op dit tabblad is opgegeven, wordt gebruikt om referenties te versleutelen/decoderen voor het on-premises gegevensarchief dat u in de portal opgeeft. Dit certificaat is het standaardcertificaat. Microsoft raadt aan dit te wijzigen in uw eigen certificaat dat u een back-up maakt in uw certificaatbeheersysteem. Klik in plaats **daarvan op Wijzigen** om uw eigen certificaat te gebruiken.

    ![Gatewaycertificaat wijzigen](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (facultatief) Als u verboselogboeking wilt inschakelen om problemen met de gateway op te lossen, schakelt u in het Microsoft Data Management Gateway Configuration Manager over naar het tabblad **Diagnostische gegevens** en schakelt u de optie **Verbose-logboekregistratie inschakelen in voor probleemoplossingsdoeleinden.** De logboekregistratiegegevens zijn te vinden in de Windows Event Viewer onder het knooppunt **Gegevensbeheergateway** **Voor toepassingen en services.**  - &gt; U ook het tabblad **Diagnostische gegevens** gebruiken om de verbinding met een on-premises gegevensbron te testen met behulp van de gateway.

    ![Uitgebreide logboekregistratie inschakelen](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Hiermee wordt het gateway-installatieproces in Azure Machine Learning Studio (klassiek) voltooid.
U bent nu klaar om uw on-premises gegevens te gebruiken.

U voor elke werkruimte meerdere gateways maken en instellen in Studio (klassiek). U bijvoorbeeld een gateway hebben die u tijdens de ontwikkeling met uw testgegevensbronnen wilt verbinden, en een andere gateway voor uw productiegegevensbronnen. Azure Machine Learning Studio (klassiek) biedt u de flexibiliteit om meerdere gateways in te stellen, afhankelijk van uw bedrijfsomgeving. Momenteel u geen gateway delen tussen werkruimten en kan slechts één gateway op één computer worden geïnstalleerd. Zie [Gegevens verplaatsen tussen on-premises bronnen en cloud met Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md)voor meer informatie.

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Stap 2: De gateway gebruiken om gegevens uit een on-premises gegevensbron te lezen
Nadat u de gateway hebt ingesteld, u een **module Gegevens importeren** toevoegen aan een experiment dat de gegevens uit de on-premises SQL Server-database ingangen.

1. Selecteer in Machine Learning Studio (klassiek) het tabblad **EXPERIMENTEN,** klik op **+NIEUW** in de linkerbenedenhoek en selecteer **Leeg experiment** (of selecteer een van de verschillende beschikbare voorbeeldexperimenten).
2. Zoek en sleep de module **Gegevens importeren** naar het experimentcanvas.
3. Klik **op Opslaan als** onder het canvas. Typ 'Azure Machine Learning Studio (klassieke) on-premises SQL Server-zelfstudie' voor **OK** de naam van het experiment, selecteer de werkruimte en klik op het ok-vinkje.

   ![Experiment opslaan met een nieuwe naam](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Klik **op** de module Gegevens importeren om deze te selecteren en selecteer vervolgens in het deelvenster **Eigenschappen** rechts van het canvas de optie 'On-Premises SQL-database' in de vervolgkeuzelijst **Gegevensbron.**
5. Selecteer de **gegevensgateway die** u hebt geïnstalleerd en geregistreerd. U een andere gateway instellen door (nieuwe gegevensgateway toevoegen...) te selecteren.

   ![Gegevensgateway selecteren voor module Gegevens importeren](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Voer de naam en **de naam van**de SQL **Database-server** in, samen met de SQL **Database-query** die u wilt uitvoeren.
7. Klik **op Waarden invoeren** onder Gebruikersnaam en **wachtwoord** en voer uw databasereferenties in. U Windows Integrated Authentication of SQL Server Authentication gebruiken, afhankelijk van hoe uw on-premises SQL Server is geconfigureerd.

   ![Databasereferenties invoeren](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Het bericht "waarden vereist" verandert in "waarden ingesteld" met een groen vinkje. U hoeft de referenties slechts één keer in te voeren, tenzij de databasegegevens of het wachtwoord worden gewijzigd. Azure Machine Learning Studio (klassiek) gebruikt het certificaat dat u hebt opgegeven toen u de gateway installeerde om de referenties in de cloud te versleutelen. Azure slaat nooit on-premises referenties op zonder versleuteling.

   ![Eigenschappen van de module Gegevens importeren](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Klik **op RUN** om het experiment uit te voeren.

Zodra het experiment is uitgevoerd, u de gegevens die u uit de database hebt geïmporteerd visualiseren door op de uitvoerpoort van de module **Gegevens importeren** te klikken en **Visualiseren**te selecteren.

Zodra u klaar bent met het ontwikkelen van uw experiment, u uw model implementeren en operationaliseren. Met behulp van de Batch Execution Service worden gegevens uit de on-premises SQL Server-database die is geconfigureerd in de module **Gegevens importeren** gelezen en gebruikt om te scoren. Hoewel u de aanvraagresponsservice gebruiken voor het scoren van on-premises gegevens, raadt Microsoft in plaats daarvan aan de [Excel-invoegtoepassing te](excel-add-in-for-web-services.md) gebruiken. Momenteel wordt het schrijven naar een on-premises SQL Server-database via **ExportData** niet ondersteund in uw experimenten of gepubliceerde webservices.
