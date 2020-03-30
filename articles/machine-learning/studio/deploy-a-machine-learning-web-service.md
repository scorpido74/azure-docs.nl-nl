---
title: Een webservice implementeren
titleSuffix: ML Studio (classic) - Azure
description: Een trainingsexperiment converteren naar een voorspellend experiment, voorbereiden op implementatie en vervolgens implementeren als een Azure Machine Learning Studio (klassieke) webservice.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 6c81e50560de69f7702e852d4602680fde7f01f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218134"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Een Azure Machine Learning Studio (klassieke) webservice implementeren

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klassiek) stelt u in staat om een voorspellende analytische oplossing te bouwen en te testen. Vervolgens u de oplossing implementeren als een webservice.

Machine Learning Studio (klassieke) webservices bieden een interface tussen een applicatie en een Machine Learning Studio (klassiek) workflow scoring model. Een externe toepassing kan in realtime communiceren met een Machine Learning Studio (klassiek) workflow scoremodel. Een oproep naar een Machine Learning Studio (klassieke) webservice retourneert voorspellingsresultaten naar een externe toepassing. Tijdens een aanroep aan een webservice wordt een API-sleutel doorgegeven die is gemaakt tijdens de implementatie van de webservice. Een Machine Learning Studio (klassieke) webservice is gebaseerd op REST, een populaire architectuurkeuze voor webprogrammeringsprojecten.

Azure Machine Learning Studio (klassiek) heeft twee soorten webservices:

* Request-Response Service (RRS): een lage latentie, zeer schaalbare service die één gegevensrecord scoort.
* Batch Execution Service (BES): een asynchrone service die een batch gegevensrecords scoort.

De invoer voor BES is net als de gegevensinvoer die door RRS wordt gebruikt. Het belangrijkste verschil is dat BES een blok records uit diverse bronnen leest, zoals Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (hive query) en HTTP-bronnen.

Vanuit een oogpunt op hoog niveau implementeert u uw model in drie stappen:

* **[Maak een trainingsexperiment]** - In Studio (klassiek) u een voorspellend analysemodel trainen en testen met behulp van trainingsgegevens die u levert, met behulp van een grote set ingebouwde machine learning-algoritmen.
* **[Converteer het naar een voorspellend experiment]** - Zodra uw model is getraind met bestaande gegevens en u klaar bent om het te gebruiken om nieuwe gegevens te scoren, bereidt u uw experiment voor op voorspellingen.
* **Implementeer** het als een **[nieuwe webservice]** of een **[klassieke webservice]** - Wanneer u uw voorspellende experiment implementeert als een Azure-webservice, kunnen gebruikers gegevens naar uw model verzenden en de voorspellingen van uw model ontvangen.

## <a name="create-a-training-experiment"></a>Een trainingsexperiment maken

Om een voorspellend analysemodel te trainen, gebruikt u Azure Machine Learning Studio (klassiek) om een trainingsexperiment te maken waarbij u verschillende modules opneemt om trainingsgegevens te laden, de gegevens zo nodig voor te bereiden, machine learning-algoritmen toe te passen en de Resultaten. U herhalen op een experiment en probeer verschillende machine learning algoritmen te vergelijken en evalueren van de resultaten.

Het proces van het maken en beheren van trainingsexperimenten wordt elders grondiger behandeld. Raadpleeg voor meer informatie de volgende artikelen:

* [Een eenvoudig experiment maken in Azure Machine Learning Studio (klassiek)](create-experiment.md)
* [Een voorspellende oplossing ontwikkelen met Azure Machine Learning Studio (klassiek)](tutorial-part1-credit-risk.md)
* [Uw trainingsgegevens importeren in Azure Machine Learning Studio (klassiek)](import-data.md)
* [Experimentiteraties beheren in Azure Machine Learning Studio (klassiek)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Het trainingsexperiment converteren naar een voorspellend experiment

Zodra u uw model hebt getraind, bent u klaar om uw trainingsexperiment om te zetten in een voorspellend experiment om nieuwe gegevens te scoren.

Door te converteren naar een voorspellend experiment, krijgt u uw getrainde model klaar om te worden geïmplementeerd als een scorende webservice. Gebruikers van de webservice kunnen invoergegevens naar uw model sturen en uw model stuurt de voorspellingsresultaten terug. Houd bij het converteren naar een voorspellend experiment rekening met hoe u verwacht dat uw model door anderen wordt gebruikt.

Als u uw trainingsexperiment wilt converteren naar een voorspellend experiment, **klikt** u onder aan het experimentcanvas uitvoeren, klikt u op **Webservice instellen**en selecteert u Predictive Web **Service**.

![Converteren naar scoringsexperiment](./media/publish-a-machine-learning-web-service/figure-1.png)

Zie [Uw model voorbereiden op implementatie in Azure Machine Learning Studio (klassiek)](convert-training-experiment-to-scoring-experiment.md)voor meer informatie over het uitvoeren van deze conversie.

In de volgende stappen wordt beschreven dat een voorspellend experiment wordt geïmplementeerd als een nieuwe webservice. U het experiment ook implementeren als Classic-webservice.

## <a name="deploy-it-as-a-new-web-service"></a>Implementeren als een nieuwe webservice

Nu het voorspellende experiment is voorbereid, u het implementeren als een nieuwe (Op Resource Manager gebaseerde) Azure-webservice. Met behulp van de webservice kunnen gebruikers gegevens naar uw model verzenden en het model geeft zijn voorspellingen terug.

Als u uw voorspellend experiment wilt implementeren, klikt u onder aan het experimentcanvas op **Uitvoeren.** Nadat het experiment is uitgevoerd, klikt u op **Webservice implementeren** en selecteert **u Webservice [nieuw]implementeren**.  De implementatiepagina van de Machine Learning Studio (klassieke) Web Service portal wordt geopend.

> [!NOTE] 
> Als u een nieuwe webservice wilt implementeren, moet u over voldoende machtigingen beschikken in het abonnement waarvoor u de webservice implementeert. Zie [Een webservice beheren met de Azure Machine Learning Web Services-portal](manage-new-webservice.md)voor meer informatie. 

### <a name="web-service-portal-deploy-experiment-page"></a>Experimentpagina voor webserviceportal implementeren

Voer op de pagina Experiment implementeren een naam in voor de webservice.
Selecteer een prijsplan. Als u een bestaand prijsplan hebt, u het selecteren, anders moet u een nieuw prijsplan voor de service maken.

1. Selecteer in de vervolgkeuzelijst **Prijsplan** een bestaand abonnement of selecteer de optie **Nieuw abonnement selecteren.**
2. Typ **in Plannaam**een naam die het plan op uw factuur identificeert.
3. Selecteer een van de **lagen van het maandelijkse abonnement**. Standaard worden de prijscategorieën voor uw standaardregio gebruikt en de webservice wordt ook in die regio geïmplementeerd.

Klik **op Implementeren** en de pagina **Snelstart** voor uw webservice wordt geopend.

De pagina Quickstart van de webservice geeft u toegang tot en begeleiding over de meest voorkomende taken die u uitvoert na het maken van een webservice. Vanaf hier u eenvoudig toegang krijgen tot zowel de pagina Testen als de pagina Consumeren.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Uw nieuwe webservice testen

Als u uw nieuwe webservice wilt testen, klikt u op **Webservice testen** onder algemene taken. Op de pagina Test u uw webservice testen als een Request-Response Service (RRS) of een Batch Execution-service (BES).

Op de rrs-testpagina worden de ingangen, uitvoer en alle algemene parameters weergegeven die u voor het experiment hebt gedefinieerd. Als u de webservice wilt testen, u handmatig de juiste waarden voor de ingangen invoeren of een CSV-bestand (comma separated value) leveren dat de testwaarden bevat.

Als u rrs wilt testen, voert u in de lijstweergavemodus de juiste waarden in voor de ingangen en klikt u op **Aanvraag-antwoord testen**. De voorspellingsresultaten worden weergegeven in de uitvoerkolom links.

![De juiste waarden invoeren om uw webservice te testen](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Als u uw BES wilt testen, klikt u op **Batch**. Klik op de testpagina Batch op Bladeren onder uw invoer en selecteer een CSV-bestand met de juiste voorbeeldwaarden. Als u geen CSV-bestand hebt en u uw voorspellend experiment hebt gemaakt met Machine Learning Studio (klassiek), u de gegevensset voor uw voorspellende experiment downloaden en gebruiken.

Als u de gegevensset wilt downloaden, opent u Machine Learning Studio (klassiek). Open uw voorspellend experiment en klik met de rechtermuisknop op de invoer voor uw experiment. Selecteer in het contextmenu **de gegevensset** en selecteer **Download**.

![Download uw gegevensset van het Studio (klassieke) canvas](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klik **op Testen**. De status van de taak Batchexecution wordt rechts weergegeven onder **Batchtaken testen.**

![Uw batchuitvoeringstaak testen met de webserviceportal](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Op de pagina **CONFIGURATIE** u de beschrijving, de titel, de opslagaccountsleutel bijwerken en voorbeeldgegevens voor uw webservice inschakelen.

![Uw webservice configureren](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Toegang tot uw nieuwe webservice

Zodra u uw webservice van Machine Learning Studio (klassiek) implementeert, u gegevens naar de service verzenden en programmatisch antwoorden ontvangen.

De pagina **Consumeren** biedt alle informatie die u nodig hebt om toegang te krijgen tot uw webservice. De API-sleutel wordt bijvoorbeeld geleverd om geautoriseerde toegang tot de service mogelijk te maken.

Zie [Een Azure Machine Learning Studio (klassieke) webservice gebruiken](consume-web-services.md)voor meer informatie over toegang tot een webservice voor Machine Learning Studio (klassieke) webservice.

### <a name="manage-your-new-web-service"></a>Uw nieuwe webservice beheren

U uw nieuwe webservices beheren met machine learning studio (klassieke) Web Services-portal. Klik op de [hoofdportalpagina](https://services.azureml.net/)op **Webservices**. Op de webpagina u een service verwijderen of kopiëren. Als u een specifieke service wilt controleren, klikt u op de service en klikt u vervolgens op **Dashboard**. Als u batchtaken wilt controleren die zijn gekoppeld aan de webservice, klikt u op **Batchaanvraaglogboek**.

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a>Uw nieuwe webservice implementeren in meerdere regio's

U een nieuwe webservice eenvoudig implementeren in meerdere regio's zonder dat u meerdere abonnementen of werkruimten nodig hebt.

Prijzen zijn regiospecifiek, dus u moet een factureringsplan definiëren voor elke regio waarin u de webservice implementeert.

#### <a name="create-a-plan-in-another-region"></a>Een plan maken in een andere regio

1. Meld u aan bij [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/).
2. Klik op de **menuoptie Plannen.**
3. Klik op de pagina Plannen over weergave op **Nieuw**.
4. Selecteer **in** de vervolgkeuzelijst Abonnement het abonnement waarin het nieuwe abonnement zich bevindt.
5. Selecteer in de **vervolgkeuzelijst Regio** een regio voor het nieuwe plan. De planopties voor het geselecteerde gebied worden weergegeven in de sectie **Opties plannen** van de pagina.
6. Selecteer in de vervolgkeuzelijst **Resourcegroep** een resourcegroep voor het plan. Zie overzicht azure resource [manager](../../azure-resource-manager/management/overview.md)voor meer informatie over resourcegroepen.
7. Typ **in Plannaam** de naam van het plan.
8. Klik **onder Planopties**op het factureringsniveau voor het nieuwe abonnement.
9. Klik **op Maken**.

#### <a name="deploy-the-web-service-to-another-region"></a>De webservice implementeren naar een andere regio

1. Klik op de pagina Microsoft Azure Machine Learning Web Services op de **menuoptie Webservices.**
2. Selecteer de webservice die u implementeert in een nieuw gebied.
3. Klik **op Kopiëren**.
4. Typ in **WebServicename**een nieuwe naam voor de webservice.
5. Typ in **de beschrijving van webservice**een beschrijving voor de webservice.
6. Selecteer **in** de vervolgkeuzelijst Abonnement het abonnement waarin de nieuwe webservice zich bevindt.
7. Selecteer in de vervolgkeuzelijst **Resourcegroep** een resourcegroep voor de webservice. Zie overzicht azure resource [manager](../../azure-resource-manager/management/overview.md)voor meer informatie over resourcegroepen.
8. Selecteer in de **vervolgkeuzelijst Regio** de regio waarin u de webservice wilt implementeren.
9. Selecteer in de vervolgkeuzelijst **Opslagaccount** een opslagaccount waarin u de webservice wilt opslaan.
10. Selecteer in de vervolgkeuzelijst **Prijsplan** een plan in de regio die u in stap 8 hebt geselecteerd.
11. Klik **op Kopiëren**.

## <a name="deploy-it-as-a-classic-web-service"></a>Implementeren als een klassieke webservice

Nu het voorspellende experiment voldoende is voorbereid, u het implementeren als een Klassieke Azure-webservice. Met behulp van de webservice kunnen gebruikers gegevens naar uw model verzenden en het model geeft zijn voorspellingen terug.

Als u uw voorspellend experiment wilt implementeren, **klikt** u onder aan het experimentcanvas uitvoeren en klikt u vervolgens op **Webservice implementeren**. De webservice is ingesteld en u wordt in het dashboard van de webservice geplaatst.

![Uw webservice implementeren vanuit Studio (klassiek)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Test uw Classic-webservice

U de webservice testen in de Machine Learning Studio (classic) Web Services portal of Machine Learning Studio (klassiek).

Als u de webservice Antwoord aanvragen wilt testen, klikt u op de knop **Testen** in het dashboard van de webservice. Er verschijnt een dialoogvenster waarin u wordt gevraagd naar de invoergegevens voor de service. Dit zijn de kolommen die worden verwacht door het scoringsexperiment. Voer een set gegevens in en klik vervolgens op **OK**. De resultaten van de webservice worden onder aan het dashboard weergegeven.

U op de koppeling **Voorbeeld** testen klikken om uw service te testen in de Azure Machine Learning Studio -portal (klassieke) webservices, zoals eerder wordt weergegeven in de sectie Nieuwe webservice.

Als u de batchuitvoeringsservice wilt testen, klikt u op Koppeling voorbeeld **testen** . Klik op de testpagina Batch op Bladeren onder uw invoer en selecteer een CSV-bestand met de juiste voorbeeldwaarden. Als u geen CSV-bestand hebt en u uw voorspellend experiment hebt gemaakt met Machine Learning Studio (klassiek), u de gegevensset voor uw voorspellende experiment downloaden en gebruiken.

![De webservice testen](./media/publish-a-machine-learning-web-service/figure-3.png)

Op de **pagina CONFIGURATIE** u de weergavenaam van de service wijzigen en deze een beschrijving geven. De naam en beschrijving worden weergegeven in de [Azure-portal](https://portal.azure.com/) waar u uw webservices beheert.

U een beschrijving geven voor uw invoergegevens, uitvoergegevens en webserviceparameters door voor elke kolom een tekenreeks in te voeren onder **INVOERSCHEMA,** **UITVOERSCHEMA**en **Parameter WebSERVICE**. Deze beschrijvingen worden gebruikt in de voorbeeldcodedocumentatie die voor de webservice wordt verstrekt.

U logboekregistratie inschakelen om eventuele fouten te diagnosticeren die u ziet wanneer uw webservice wordt geopend. Zie [Logboekregistratie inschakelen voor Machine Learning Studio (klassieke) webservices voor](web-services-logging.md)meer informatie.

![Logboekregistratie inschakelen voor de portal voor webservices](./media/publish-a-machine-learning-web-service/figure-4.png)

U de eindpunten voor de webservice ook configureren in de Azure Machine Learning Web Services-portal, vergelijkbaar met de procedure die eerder in de sectie Nieuwe webservice werd weergegeven. De opties zijn verschillend, u de servicebeschrijving toevoegen of wijzigen, logboekregistratie inschakelen en voorbeeldgegevens inschakelen voor het testen.

### <a name="access-your-classic-web-service"></a>Toegang tot uw Classic-webservice

Zodra u uw webservice implementeert vanuit Azure Machine Learning Studio (klassiek), u gegevens naar de service verzenden en programmatisch antwoorden ontvangen.

Het dashboard biedt alle informatie die u nodig hebt om toegang te krijgen tot uw webservice. De API-sleutel is bijvoorbeeld aanwezig om geautoriseerde toegang tot de service mogelijk te maken en API-helppagina's worden verstrekt om u te helpen aan de slag te gaan met het schrijven van uw code.

Zie [Een Azure Machine Learning Studio (klassieke) webservice gebruiken](consume-web-services.md)voor meer informatie over toegang tot een webservice voor Machine Learning Studio (klassieke) webservice.

### <a name="manage-your-classic-web-service"></a>Uw klassieke webservice beheren

Er zijn verschillende acties die u uitvoeren om een webservice te controleren. U het bijwerken en verwijderen. U naast het standaardeindpunt dat wordt gemaakt wanneer u deze implementeert, ook extra eindpunten toevoegen aan een Klassieke webservice.

Zie [Een Azure Machine Learning Studio -werkruimte (klassieke) werkruimte beheren](manage-workspace.md) en Een [webservice beheren met behulp van de Azure Machine Learning Studio -portal (klassieke) Web Services.](manage-new-webservice.md)

## <a name="update-the-web-service"></a>De webservice bijwerken
U wijzigingen aanbrengen in uw webservice, zoals het bijwerken van het model met aanvullende trainingsgegevens en het opnieuw implementeren, door de oorspronkelijke webservice te overschrijven.

Als u de webservice wilt bijwerken, opent u het oorspronkelijke voorspellende experiment dat u hebt gebruikt om de webservice te implementeren en een bewerkbare kopie te maken door op **OPSLAAN ALS**te klikken. Breng de wijzigingen aan en klik op **Webservice implementeren**.

Omdat u dit experiment al eerder hebt geïmplementeerd, wordt u gevraagd of u de bestaande service wilt overschrijven (Classic Web Service) of (Nieuwe webservice) wilt bijwerken.Because you've ployed this experiment before, you is sgevraagd if you want to overwrite (Classic Web Service) or update (New web service) the existing service. Als u op **JA** of **Update** klikt, wordt de bestaande webservice gestopt en wordt het nieuwe voorspellende experiment geïmplementeerd.

> [!NOTE]
> Als u bijvoorbeeld configuratiewijzigingen hebt aangebracht in de oorspronkelijke webservice, die een nieuwe weergavenaam of -beschrijving invoert, moet u deze waarden opnieuw invoeren.

Een optie voor het bijwerken van uw webservice is om het model programmatisch om te scholen. Zie [Machine Learning Studio (klassieke) modellen programmatisch omscholen](/azure/machine-learning/studio/retrain-machine-learning-model)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie Hoe een machine learning [studio(klassiek) model van een experiment naar een geoperationaliseerde webservice](model-progression-experiment-to-web-service.md)vordert voor meer technische details over hoe implementatie werkt.

* Zie [Uw model voorbereiden op implementatie in Azure Machine Learning Studio (klassiek).](convert-training-experiment-to-scoring-experiment.md)

* Er zijn verschillende manieren om de REST-API te gebruiken en toegang te krijgen tot de webservice. Zie [hoe u een Azure Machine Learning Studio -webservice (klassieke) webservice gebruiken.](consume-web-services.md)

<!-- internal links -->
[Een trainingsexperiment maken]: #create-a-training-experiment
[Converteren naar een voorspellend experiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Nieuwe webservice]: #deploy-it-as-a-new-web-service
[Klassieke webservice]: #deploy-it-as-a-classic-web-service
[Nieuw]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
