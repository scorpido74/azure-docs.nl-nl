---
title: On-premises SQL Server
titleSuffix: ML Studio (classic) - Azure
description: Gegevens uit een on-premises SQL Server Data Base gebruiken om geavanceerde analyses uit te voeren met Azure Machine Learning Studio (klassiek).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 648dbdb7e9e9d1b20c55d3fa5b314b7e4657d5e7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204179"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Analyses uitvoeren met Azure Machine Learning Studio (klassiek) met behulp van een on-premises SQL Server Data Base

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Vaak ondernemingen die met on-premises gegevens werken wilt profiteren van de schaal en flexibiliteit van de cloud voor de machine learning-werkbelastingen uitvoeren. Maar ze niet willen hun huidige bedrijfsprocessen en werkstromen worden onderbroken door het verplaatsen van hun on-premises gegevens naar de cloud. Azure Machine Learning Studio (klassiek) biedt nu ondersteuning voor het lezen van uw gegevens van een on-premises SQL Server-Data Base en vervolgens de training en het scoren van een model met deze gegevens. U hebt niet meer handmatig kopiëren en synchroniseren van gegevens tussen de cloud en uw on-premises server. In plaats daarvan kan de module **gegevens importeren** in azure machine learning Studio (klassiek) nu rechtstreeks worden gelezen vanaf uw on-premises SQL Server-Data Base voor uw trainings-en Score taken.

Dit artikel bevat een overzicht van hoe u on-premises SQL Server-gegevens indeelt in Azure Machine Learning Studio (klassiek). Hierbij wordt ervan uitgegaan dat u bekend bent met de concepten van Studio (klassiek) zoals werk ruimten, modules, gegevens sets, experimenten, *enzovoort*.

> [!NOTE]
> Deze functie is niet beschikbaar voor gratis werkruimten. Zie [Azure machine learning prijzen](https://azure.microsoft.com/pricing/details/machine-learning/)voor meer informatie over machine learning prijzen en-lagen.
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>De Data Factory zelf-hostende Integratieruntime installeren
Als u toegang wilt krijgen tot een on-premises SQL Server-data base in Azure Machine Learning Studio (klassiek), moet u de Data Factory zelf-hostende Integration Runtime, voorheen bekend als de Data Management Gateway, downloaden en installeren. Wanneer u de verbinding in Machine Learning Studio (klassiek) configureert, kunt u de Integration Runtime (IR) downloaden en installeren met behulp van het dialoog venster **gegevens gateway downloaden en registreren** dat hieronder wordt beschreven.


U kunt ook de IR van tevoren installeren door het MSI-installatie pakket te downloaden en uit te voeren vanuit het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=39717). Het MSI-bestand kan ook worden gebruikt om een bestaande IR te upgraden naar de nieuwste versie, waarbij alle instellingen behouden blijven.

De Data Factory zelfgehoste Integration Runtime heeft de volgende vereisten:

* De Data Factory zelfgehoste Integration vereist een 64-bits besturingssysteem met .NET Framework 4.6.1 of hoger.
* De ondersteunde versies van de Windows-besturingssysteem zijn Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* De aanbevolen configuratie voor de machine IR is ten minste 2 GHz, 4-Core CPU, 8GB RAM-geheugen en 80GB-schijf.
* Als de hostmachine in de slaap stand wordt gezet, reageert de IR niet op gegevens aanvragen. Daarom een juiste energiebeheerschema configureren op de computer voordat u installeert de IR. Als de machine is geconfigureerd voor de slaapstand, wordt de installatie van de IR een bericht weergegeven.
* Omdat de kopieeractiviteit wordt uitgevoerd op een specifieke frequentie, volgt het Resourcegebruik (CPU, geheugen) op de computer ook hetzelfde patroon met piek- en niet-actieve tijden. Gebruik van resources is ook afhankelijk sterk de hoeveelheid gegevens die wordt verplaatst. Wanneer meerdere kopie-taken uitgevoerd worden, moet u Resourcegebruik tijdens piektijden omhoog gaan zien. De minimale configuratie bovenstaande technisch voldoende is, kunt u een configuratie met meer bronnen dan de minimale configuratie zijn afhankelijk van uw specifieke werkbelasting voor verplaatsing van gegevens.

Overweeg het volgende bij het instellen en gebruiken van een Data Factory zelfgehoste Integration Runtime:

* U kunt slechts één exemplaar van de IR installeren op een enkele computer.
* U kunt een enkel IR gebruiken voor meerdere on-premises gegevensbronnen.
* U kunt meerdere IRs op verschillende computers verbinding maken met dezelfde on-premises gegevensbron.
* U configureert een IRs voor slechts één werk ruimte tegelijk. IRs kan momenteel niet worden gedeeld in werk ruimten.
* U kunt meerdere IRs voor één werkruimte configureren. U kunt bijvoorbeeld een IR gebruiken die is verbonden met uw test gegevens bronnen tijdens de ontwikkeling en een productie-IR wanneer u klaar bent voor operationeel maken.
* De IR hoeft niet te worden op dezelfde computer als de gegevensbron. Maar dichter bij de gegevensbron blijft verkort de termijn voor de gateway verbinding maken met de gegevensbron. Het is raadzaam dat u de IR op een computer die verschilt van de map die als host fungeert voor de on-premises gegevensbron installeren zodat de gateway en de gegevensbron niet concurreren om bronnen.
* Als u al een IR op uw computer hebt geïnstalleerd voor Power BI of Azure Data Factory scenario's, installeert u een afzonderlijke IR voor Azure Machine Learning Studio (klassiek) op een andere computer.

  > [!NOTE]
  > U kunt Data Factory zelfgehoste Integration Runtime en Power BI Gateway niet uitvoeren op dezelfde computer.
  >
  >
* U moet de Data Factory zelf-hostende Integration Runtime voor Azure Machine Learning Studio (klassiek) gebruiken, zelfs als u Azure ExpressRoute gebruikt voor andere gegevens. U moet uw gegevensbron behandelen als een on-premises gegevensbron (die zich achter een firewall bevindt), zelfs als u ExpressRoute gebruikt. De Data Factory zelfgehoste Integration Runtime gebruiken om verbinding tussen de Machine Learning en de gegevensbron te maken.

Meer informatie over de installatie vereisten, installatie stappen en tips voor probleem oplossing vindt u in het artikel [Integration runtime in Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Gegevens uit uw on-premises SQL Server data base in Azure Machine Learning
In dit scenario stelt u een Azure Data Factory Integration Runtime in een Azure Machine Learning-werk ruimte in, configureert u deze en leest u vervolgens gegevens van een on-premises SQL Server-Data Base.

> [!TIP]
> Voordat u begint, moet u de pop-upblokkering van uw browser voor `studio.azureml.net`uitschakelen. Als u de Google Chrome-browser gebruikt, downloadt en installeert u een van de verschillende invoeg toepassingen die beschikbaar zijn in Google Chrome-webstore. [Klik eenmaal op app-extensie](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory zelfgehoste Integration Runtime was voorheen bekend als Data Management Gateway. De stapsgewijze zelfstudie blijven om te verwijzen naar deze als een gateway.  

### <a name="step-1-create-a-gateway"></a>Stap 1: Een gateway maken
De eerste stap is het maken en de gateway instellen voor toegang tot uw on-premises SQL-database.

1. Meld u aan bij [Azure machine learning Studio (klassiek)](https://studio.azureml.net/Home/) en selecteer de werk ruimte waarin u wilt werken.
2. Klik op de Blade **instellingen** aan de linkerkant en klik vervolgens op het tabblad **gegevens gateways** bovenaan.
3. Klik onder aan het scherm op **nieuwe gegevens gateway** .

    ![Nieuwe gegevensgateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. Voer in het dialoog venster **nieuwe gegevens gateway** de **naam** van de gateway in en voeg eventueel een **Beschrijving**toe. Klik op de pijl in de rechterbenedenhoek om naar de volgende stap van de configuratie te gaan.

    ![Gatewaynaam en beschrijving invoeren](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. Download en registreer data gateway in het dialoogvenster in, de REGISTRATIESLEUTEL voor de GATEWAY naar het Klembord te kopiëren.

    ![Download en data gateway registreren](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Als u de micro soft-Data Management Gateway nog niet hebt gedownload en geïnstalleerd, klikt u op **Data Management Gateway downloaden**. Hiermee gaat u naar het Microsoft Download Center waar u kunt de gatewayversie die u nodig hebt, downloadt u deze selecteren en installeren. Meer informatie over de installatie vereisten, installatie stappen en tips voor het oplossen van problemen vindt u in de eerste sectie van het artikel [gegevens verplaatsen tussen on-premises bronnen en Cloud met Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).
7. Nadat de gateway is geïnstalleerd, wordt de Data Management Gateway Configuration Manager geopend en wordt het dialoog venster **Gateway registreren** weer gegeven. Plak de **Gateway registratie sleutel** die u hebt gekopieerd naar het klem bord en klik op **registreren**.
8. Als u al een gateway is geïnstalleerd, voert u Data Management Gateway Configuratiebeheer. Klik op **sleutel wijzigen**, plak de **Gateway registratie sleutel** die u in de vorige stap naar het klem bord hebt gekopieerd en klik op **OK**.
9. Wanneer de installatie is voltooid, wordt het dialoog venster **Gateway registreren** voor micro soft data management gateway Configuration Manager weer gegeven. Plak de GATEWAY registratie sleutel die u in een vorige stap naar het klem bord hebt gekopieerd en klik op **registreren**.

    ![Gateway registreren](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. De configuratie van de gateway is voltooid wanneer de volgende waarden zijn ingesteld op het tabblad **Start** in micro soft data management gateway Configuration Manager:

    * De naam van de **Gateway** en de naam van het **exemplaar** worden ingesteld op de naam van de gateway.
    * **Registratie** is ingesteld op **geregistreerd**.
    * **Status** is ingesteld op **gestart**.
    * De status balk aan de onderkant wordt weer gegeven met **Data Management Gateway Cloud service** samen met een groen vinkje.

      ![Data Management Gateway Manager](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio (klassiek) wordt ook bijgewerkt wanneer de registratie slaagt.

    ![Gateway-registratie is voltooid](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. Klik in het dialoog venster **gegevens gateway downloaden en registreren** op het vinkje om de installatie te volt ooien. Op de pagina **instellingen** wordt de status van de gateway weer gegeven als ' online '. In het rechter deelvenster vindt u de status en andere nuttige informatie.

    ![Gateway-instellingen](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. Ga in de micro soft Data Management Gateway Configuration Manager naar het tabblad **certificaat** . Het certificaat dat is opgegeven op dit tabblad wordt gebruikt voor het versleutelen/ontsleutelen van referenties voor het on-premises gegevens archief dat u in de portal opgeeft. Dit certificaat is het standaard-certificaat. Microsoft raadt aan wijzigen in uw eigen certificaat dat u back-up in uw certificate management-systeem. Klik in plaats daarvan op **wijzigen** om uw eigen certificaat te gebruiken.

    ![Certificaat van de gateway wijzigen](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. Beschrijving Als u uitgebreide logboek registratie wilt inschakelen om problemen met de gateway op te lossen, gaat u in de micro soft Data Management Gateway Configuration Manager naar het tabblad **Diagnostische gegevens** en schakelt u de optie **uitgebreide logboek registratie inschakelen voor probleem oplossing** . De informatie over logboek registratie vindt u in de Windows-Logboeken onder de **Logboeken toepassingen en Services** -&gt; **Data Management Gateway** knoop punt. U kunt ook het tabblad **diagnostiek** gebruiken om de verbinding met een on-premises gegevens bron te testen met behulp van de gateway.

    ![Uitgebreide logboekregistratie inschakelen](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Hiermee voltooit u het installatie proces van de gateway in Azure Machine Learning Studio (klassiek).
U bent nu klaar voor gebruik van uw on-premises gegevens.

U kunt in Studio (klassiek) meerdere gateways maken en instellen voor elke werk ruimte. Bijvoorbeeld, u mogelijk een gateway die u wilt verbinding maken met uw gegevensbronnen test tijdens de ontwikkeling en een andere gateway voor uw productie-gegevensbronnen. Azure Machine Learning Studio (klassiek) biedt u de flexibiliteit om meerdere gateways in te stellen, afhankelijk van uw bedrijfs omgeving. U kunt op dit moment geen gateway delen tussen werk ruimten en slechts één gateway kan op één computer worden geïnstalleerd. Zie [gegevens verplaatsen tussen on-premises bronnen en Cloud met Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md)voor meer informatie.

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Stap 2: De gateway gebruiken om te lezen van gegevens van een on-premises gegevensbron
Nadat u de gateway hebt ingesteld, kunt u een module voor het **importeren van gegevens** toevoegen aan een experiment dat de gegevens van de on-premises SQL Server Data Base instuurt.

1. In Machine Learning Studio (klassiek) selecteert u het tabblad **experimenten** , klikt u op **+ Nieuw** in de linkerbenedenhoek en selecteert u **leeg experiment** (of selecteert u een van de beschik bare voorbeeld experimenten).
2. Zoek en sleep de module **gegevens importeren** naar het canvas op het experiment.
3. Klik op **Opslaan als** onder het canvas. Voer ' Azure Machine Learning Studio (klassiek) on-premises SQL Server zelf studie ' in voor de naam van het experiment, selecteer de werk ruimte en klik op het vinkje **OK** .

   ![Experiment met een nieuwe naam opslaan](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Klik op de module **gegevens importeren** om deze te selecteren en selecteer in het deel venster **Eigenschappen** rechts van het canvas de optie ' On-premises SQL database ' in de vervolg keuzelijst **gegevens bron** .
5. Selecteer de **gegevens gateway** die u hebt geïnstalleerd en geregistreerd. U kunt een andere gateway instellen door te selecteren '(toevoegen van nieuwe gegevensgateway...)'.

   ![Selecteer de gegevensgateway voor de module gegevens importeren](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Voer de naam van de SQL **Data Base-Server** en de **database naam**in, samen met de SQL **Data Base-query** die u wilt uitvoeren.
7. Klik op **waarden opgeven** onder **gebruikers naam en wacht woord** en voer uw database referenties in. U kunt Windows geïntegreerde verificatie of SQL Server-verificatie, afhankelijk van hoe uw on-premises SQL Server is geconfigureerd.

   ![Voer de referenties van database](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Het bericht 'values vereist' wordt overgeschakeld naar 'set waarden' met een groen vinkje. U moet slechts één keer de referenties invoert, tenzij de database-informatie of het wachtwoord wordt gewijzigd. Azure Machine Learning Studio (klassiek) gebruikt het certificaat dat u hebt gegeven tijdens de installatie van de gateway om de referenties in de cloud te versleutelen. Azure slaat nooit on-premises referenties zonder versleuteling.

   ![Eigenschappen van de module gegevens importeren](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Klik op **uitvoeren** om het experiment uit te voeren.

Zodra het experiment is voltooid, kunt u de gegevens die u uit de Data Base hebt geïmporteerd, visualiseren door te klikken op de uitvoer poort van de module **gegevens importeren** en **visualiseren**te selecteren.

Wanneer u klaar bent met het ontwikkelen van uw experiment, kunt u deze kunt implementeren en operationeel maken van uw model. Als u de batch Execution-Service gebruikt, worden de gegevens van de on-premises SQL Server-Data Base die is geconfigureerd in de module **gegevens importeren** gelezen en gebruikt voor scores. Hoewel u de aanvraag respons service kunt gebruiken voor het bepalen van on-premises gegevens, raadt micro soft u aan om in plaats daarvan de [Excel-invoeg toepassing](excel-add-in-for-web-services.md) te gebruiken. Het schrijven naar een on-premises SQL Server Data Base via **export gegevens** wordt momenteel niet ondersteund in uw experimenten of gepubliceerde webservices.
