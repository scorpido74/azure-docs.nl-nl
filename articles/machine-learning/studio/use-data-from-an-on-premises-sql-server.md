---
title: On-premises SQL Server
titleSuffix: Azure Machine Learning Studio (classic)
description: Gegevens uit een on-premises SQL Server Data Base gebruiken om geavanceerde analyses uit te voeren met de klassieke versie van Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 78b50ac5f0c39a38815b01ab7510d17eda941425
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492538"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Analyses uitvoeren met Azure Machine Learning Studio (klassiek) met behulp van een on-premises SQL Server Data Base

Bedrijven die werken met on-premises gegevens, willen vaak profiteren van de schaal en flexibiliteit van de Cloud voor hun machine learning werk belastingen. Maar ze willen hun huidige bedrijfs processen en werk stromen niet verstoren door hun on-premises gegevens naar de cloud te verplaatsen. Azure Machine Learning Studio (klassiek) biedt nu ondersteuning voor het lezen van uw gegevens van een on-premises SQL Server-Data Base en vervolgens de training en het scoren van een model met deze gegevens. U hoeft de gegevens niet meer hand matig te kopiëren en te synchroniseren tussen de Cloud en uw on-premises server. In plaats daarvan kunt u de module **gegevens importeren** in de klassieke versie van Azure machine learning Studio nu rechtstreeks lezen vanaf uw on-premises SQL Server-Data Base voor uw trainings-en Score taken.

Dit artikel bevat een overzicht van hoe u on-premises SQL Server-gegevens indeelt in de klassieke versie van Azure Machine Learning Studio. Hierbij wordt ervan uitgegaan dat u bekend bent met de klassieke versie van Studio-concepten zoals werk ruimten, modules, gegevens sets, experimenten, *enzovoort*.

> [!NOTE]
> Deze functie is niet beschikbaar voor gratis werk ruimten. Zie [Azure machine learning prijzen](https://azure.microsoft.com/pricing/details/machine-learning/)voor meer informatie over machine learning prijzen en-lagen.
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Installeer de Data Factory zelf-hostende Integration Runtime
Als u toegang wilt krijgen tot een on-premises SQL Server-data base in de klassieke versie van Azure Machine Learning Studio, moet u de Data Factory zelf-hostende Integration Runtime, voorheen bekend als de Data Management Gateway, downloaden en installeren. Wanneer u de verbinding in Machine Learning Studio (klassiek) configureert, kunt u de Integration Runtime (IR) downloaden en installeren met behulp van het dialoog venster **gegevens gateway downloaden en registreren** dat hieronder wordt beschreven.


U kunt ook de IR van tevoren installeren door het MSI-installatie pakket te downloaden en uit te voeren vanuit het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=39717). Het MSI-bestand kan ook worden gebruikt om een bestaande IR te upgraden naar de nieuwste versie, waarbij alle instellingen behouden blijven.

Het Data Factory zelf-Hostende Integration Runtime heeft de volgende vereisten:

* De Data Factory zelf-Hostende integratie vereist een 64-bits besturings systeem met .NET Framework 4.6.1 of hoger.
* De ondersteunde versies van het Windows-besturings systeem zijn Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* De aanbevolen configuratie voor de IR-computer is ten minste 2 GHz, 4-core CPU, 8 GB RAM-geheugen en 80 GB schijf ruimte.
* Als de hostmachine in de slaap stand wordt gezet, reageert de IR niet op gegevens aanvragen. Configureer daarom een passend energiebeheer schema op de computer voordat u de IR installeert. Als de computer is geconfigureerd voor de slaap stand, wordt een bericht weer gegeven in de IR-installatie.
* Omdat de Kopieer activiteit met een specifieke frequentie plaatsvindt, volgt het resource gebruik (CPU, geheugen) op de machine ook hetzelfde patroon met piek-en niet-actieve tijden. Het resource gebruik is ook afhankelijk van de hoeveelheid gegevens die wordt verplaatst. Wanneer er meerdere Kopieer taken worden uitgevoerd, ziet u dat het resource gebruik tijdens piek tijden niet meer werkt. Hoewel de hierboven vermelde minimum configuratie technisch genoeg is, is het raadzaam om een configuratie met meer bronnen dan de minimale configuratie, afhankelijk van uw specifieke belasting voor het verplaatsen van gegevens.

Houd rekening met het volgende bij het instellen en gebruiken van een Data Factory zelf-hostende Integration Runtime:

* U kunt slechts één exemplaar van IR installeren op één computer.
* U kunt één IR gebruiken voor meerdere on-premises gegevens bronnen.
* U kunt meerdere IRs op verschillende computers verbinden met dezelfde on-premises gegevens bron.
* U configureert een IRs voor slechts één werk ruimte tegelijk. IRs kan momenteel niet worden gedeeld in werk ruimten.
* U kunt meerdere IRs-belasting voor één werk ruimte configureren. U kunt bijvoorbeeld een IR gebruiken die is verbonden met uw test gegevens bronnen tijdens de ontwikkeling en een productie-IR wanneer u klaar bent voor operationeel maken.
* De IR hoeft niet op dezelfde computer als de gegevens bron te zijn. Maar dichter bij de gegevens bron vermindert de tijd voor de gateway om verbinding te maken met de gegevens bron. U wordt aangeraden de IR te installeren op een machine die verschilt van de computer die als host fungeert voor de on-premises gegevens bron, zodat de gateway en de gegevens bron niet kunnen concurreren voor bronnen.
* Als u al een IR op uw computer hebt geïnstalleerd voor Power BI of Azure Data Factory scenario's, installeert u een afzonderlijke IR voor de klassieke versie van Azure Machine Learning Studio op een andere computer.

  > [!NOTE]
  > U kunt Data Factory zelf-hostende Integration Runtime en Power BI Gateway niet uitvoeren op dezelfde computer.
  >
  >
* U moet de Data Factory zelf-hostende Integration Runtime gebruiken voor de klassieke versie van Azure Machine Learning Studio, zelfs als u Azure ExpressRoute gebruikt voor andere gegevens. U moet uw gegevens bron behandelen als een on-premises gegevens bron (die zich achter een firewall bevindt), zelfs wanneer u ExpressRoute gebruikt. Gebruik de Data Factory zelf-hostende Integration Runtime om verbinding te maken tussen Machine Learning en de gegevens bron.

Meer informatie over de installatie vereisten, installatie stappen en tips voor probleem oplossing vindt u in het artikel [Integration runtime in Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Gegevens uit uw on-premises SQL Server data base in Azure Machine Learning
In dit scenario stelt u een Azure Data Factory Integration Runtime in een Azure Machine Learning-werk ruimte in, configureert u deze en leest u vervolgens gegevens van een on-premises SQL Server-Data Base.

> [!TIP]
> Voordat u begint, moet u de pop-upblokkering van uw browser voor `studio.azureml.net`uitschakelen. Als u de Google Chrome-browser gebruikt, downloadt en installeert u een van de verschillende invoeg toepassingen die beschikbaar zijn in Google Chrome-webstore. [Klik eenmaal op app-extensie](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory zelf-hostende Integration Runtime voorheen bekend als Data Management Gateway. In de zelf studie voor stapsgewijze instructies blijft deze als een gateway verwijzen.  

### <a name="step-1-create-a-gateway"></a>Stap 1: een gateway maken
De eerste stap is om de gateway te maken en in te stellen voor toegang tot uw on-premises SQL database.

1. Meld u aan bij [Azure machine learning Studio (klassiek)](https://studio.azureml.net/Home/) en selecteer de werk ruimte waarin u wilt werken.
2. Klik op de Blade **instellingen** aan de linkerkant en klik vervolgens op het tabblad **gegevens gateways** bovenaan.
3. Klik onder aan het scherm op **nieuwe gegevens gateway** .

    ![Nieuwe gegevens gateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. Voer in het dialoog venster **nieuwe gegevens gateway** de **naam** van de gateway in en voeg eventueel een **Beschrijving**toe. Klik op de pijl in de rechter bovenhoek om naar de volgende stap van de configuratie te gaan.

    ![De gateway naam en-beschrijving invoeren](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. In het dialoog venster gegevens gateway downloaden en registreren kopieert u de registratie sleutel voor de GATEWAY naar het klem bord.

    ![Gegevens gateway downloaden en registreren](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Als u de micro soft-Data Management Gateway nog niet hebt gedownload en geïnstalleerd, klikt u op **Data Management Gateway downloaden**. Hiermee gaat u naar het micro soft Download centrum waar u de gateway versie kunt selecteren die u nodig hebt, downloadt u deze en installeert u deze. Meer informatie over de installatie vereisten, installatie stappen en tips voor het oplossen van problemen vindt u in de eerste sectie van het artikel [gegevens verplaatsen tussen on-premises bronnen en Cloud met Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).
7. Nadat de gateway is geïnstalleerd, wordt de Data Management Gateway Configuration Manager geopend en wordt het dialoog venster **Gateway registreren** weer gegeven. Plak de **Gateway registratie sleutel** die u hebt gekopieerd naar het klem bord en klik op **registreren**.
8. Als u al een gateway hebt geïnstalleerd, voert u de Data Management Gateway-Configuration Manager uit. Klik op **sleutel wijzigen**, plak de **Gateway registratie sleutel** die u in de vorige stap naar het klem bord hebt gekopieerd en klik op **OK**.
9. Wanneer de installatie is voltooid, wordt het dialoog venster **Gateway registreren** voor micro soft data management gateway Configuration Manager weer gegeven. Plak de GATEWAY registratie sleutel die u in een vorige stap naar het klem bord hebt gekopieerd en klik op **registreren**.

    ![Gateway registreren](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. De configuratie van de gateway is voltooid wanneer de volgende waarden zijn ingesteld op het tabblad **Start** in micro soft data management gateway Configuration Manager:

    * De naam van de **Gateway** en de naam van het **exemplaar** worden ingesteld op de naam van de gateway.
    * **Registratie** is ingesteld op **geregistreerd**.
    * **Status** is ingesteld op **gestart**.
    * De status balk aan de onderkant wordt weer gegeven met **Data Management Gateway Cloud service** samen met een groen vinkje.

      ![Data Management Gateway Manager](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      De klassieke versie van Azure Machine Learning Studio wordt ook bijgewerkt wanneer de registratie is geslaagd.

    ![Gateway registratie geslaagd](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. Klik in het dialoog venster **gegevens gateway downloaden en registreren** op het vinkje om de installatie te volt ooien. Op de pagina **instellingen** wordt de status van de gateway weer gegeven als ' online '. In het rechterdeel venster vindt u de status en andere nuttige informatie.

    ![Gateway-instellingen](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. Ga in de micro soft Data Management Gateway Configuration Manager naar het tabblad **certificaat** . Het certificaat dat is opgegeven op dit tabblad wordt gebruikt voor het versleutelen/ontsleutelen van referenties voor het on-premises gegevens archief dat u in de portal opgeeft. Dit certificaat is het standaard certificaat. Micro soft raadt aan om dit te wijzigen in uw eigen certificaat waarvan u een back-up hebt gemaakt in uw certificaat beheersysteem. Klik in plaats daarvan op **wijzigen** om uw eigen certificaat te gebruiken.

    ![Gateway certificaat wijzigen](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. Beschrijving Als u uitgebreide logboek registratie wilt inschakelen om problemen met de gateway op te lossen, gaat u in de micro soft Data Management Gateway Configuration Manager naar het tabblad **Diagnostische gegevens** en schakelt u **uitgebreide logboek registratie inschakelen voor het oplossen van problemen** optie. De informatie over logboek registratie vindt u in de Windows-Logboeken onder de **Logboeken toepassingen en Services** -&gt; **Data Management Gateway** knoop punt. U kunt ook het tabblad **diagnostiek** gebruiken om de verbinding met een on-premises gegevens bron te testen met behulp van de gateway.

    ![Uitgebreide logboek registratie inschakelen](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Hiermee voltooit u het installatie proces van de gateway in de klassieke versie van Azure Machine Learning Studio.
U bent nu klaar om uw on-premises gegevens te gebruiken.

U kunt in Studio (klassiek) meerdere gateways maken en instellen voor elke werk ruimte. U kunt bijvoorbeeld een gateway hebben die u tijdens de ontwikkeling verbinding wilt laten maken met uw test gegevens bronnen en een andere gateway voor uw productie gegevens bronnen. De klassieke versie van Azure Machine Learning Studio biedt u de flexibiliteit om meerdere gateways in te stellen, afhankelijk van uw bedrijfs omgeving. U kunt op dit moment geen gateway delen tussen werk ruimten en slechts één gateway kan op één computer worden geïnstalleerd. Zie [gegevens verplaatsen tussen on-premises bronnen en Cloud met Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md)voor meer informatie.

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Stap 2: de gateway gebruiken om gegevens van een on-premises gegevens bron te lezen
Nadat u de gateway hebt ingesteld, kunt u een module voor het **importeren van gegevens** toevoegen aan een experiment dat de gegevens van de on-premises SQL Server Data Base instuurt.

1. In Machine Learning Studio (klassiek) selecteert u het tabblad **experimenten** , klikt u op **+ Nieuw** in de linkerbenedenhoek en selecteert u **leeg experiment** (of selecteert u een van de beschik bare voorbeeld experimenten).
2. Zoek en sleep de module **gegevens importeren** naar het canvas op het experiment.
3. Klik op **Opslaan als** onder het canvas. Voer ' Azure Machine Learning Studio (klassiek) on-premises SQL Server zelf studie ' in voor de naam van het experiment, selecteer de werk ruimte en klik op het vinkje **OK** .

   ![Experiment met een nieuwe naam opslaan](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Klik op de module **gegevens importeren** om deze te selecteren en selecteer in het deel venster **Eigenschappen** rechts van het canvas de optie ' On-premises SQL database ' in de vervolg keuzelijst **gegevens bron** .
5. Selecteer de **gegevens gateway** die u hebt geïnstalleerd en geregistreerd. U kunt een andere gateway instellen door ' (nieuwe gegevens gateway toevoegen...) ' te selecteren.

   ![Gegevens gateway selecteren voor de module gegevens importeren](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Voer de naam van de SQL **Data Base-Server** en de **database naam**in, samen met de SQL **Data Base-query** die u wilt uitvoeren.
7. Klik op **waarden opgeven** onder **gebruikers naam en wacht woord** en voer uw database referenties in. U kunt geïntegreerde Windows-verificatie of SQL Server verificatie gebruiken, afhankelijk van hoe uw on-premises SQL Server zijn geconfigureerd.

   ![Database referenties opgeven](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Het bericht ' waarden vereist ' wordt gewijzigd in ' waarden ingesteld ' met een groen vinkje. U hoeft slechts één keer de referenties in te voeren, tenzij de gegevens van de data base of het wacht woord worden gewijzigd. De klassieke versie van Azure Machine Learning Studio gebruikt het certificaat dat u hebt gegeven tijdens de installatie van de gateway om de referenties in de cloud te versleutelen. Azure slaat nooit on-premises referenties zonder versleuteling op.

   ![Eigenschappen van module gegevens importeren](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Klik op **uitvoeren** om het experiment uit te voeren.

Zodra het experiment is voltooid, kunt u de gegevens die u uit de Data Base hebt geïmporteerd, visualiseren door te klikken op de uitvoer poort van de module **gegevens importeren** en **visualiseren**te selecteren.

Wanneer u klaar bent met het ontwikkelen van uw experiment, kunt u uw model implementeren en operationeel maken. Als u de batch Execution-Service gebruikt, worden de gegevens van de on-premises SQL Server-Data Base die is geconfigureerd in de module **gegevens importeren** gelezen en gebruikt voor scores. Hoewel u de aanvraag respons service kunt gebruiken voor het bepalen van on-premises gegevens, raadt micro soft u aan om in plaats daarvan de [Excel-invoeg toepassing](excel-add-in-for-web-services.md) te gebruiken. Het schrijven naar een on-premises SQL Server Data Base via **export gegevens** wordt momenteel niet ondersteund in uw experimenten of gepubliceerde webservices.
