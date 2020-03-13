---
title: "Zelf studie 3: een model voor krediet Risico's implementeren"
titleSuffix: Azure Machine Learning Studio (classic)
description: Een gedetailleerde zelf studie waarin wordt getoond hoe u een predictive analytics oplossing kunt maken voor een beoordeling van een credit risico in Azure Machine Learning Studio (klassiek). Deze zelfstudie is deel drie van een driedelige serie. U ontdek hierin hoe u een model implementeert als webservice.
keywords: credit risk, predictive analytics solution,risk assessment, deploy, web service
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 9fb0b59374edf322e5e2221b90e912ee2c665bac
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204150"
---
# <a name="tutorial-3-deploy-credit-risk-model---azure-machine-learning-studio-classic"></a>Zelf studie 3: een model voor krediet Risico's implementeren-Azure Machine Learning Studio (klassiek)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

In deze zelfstudie wordt uitgebreid ingegaan op het ontwikkelingsproces van een predictive analytics-oplossing. U ontwikkelt een eenvoudig model in Machine Learning Studio (klassiek).  Vervolgens implementeert u het model als een Azure Machine Learning-webservice.  Dit geïmplementeerde model kan voorspellingen doen op basis van nieuwe gegevens. Deze zelfstudie is **deel drie van een driedelige serie**.

Stel dat u iemands kredietrisico moet voorspellen op basis van de gegevens die deze persoon in een kredietaanvraag heeft ingevuld.  

Kredietrisicobeoordeling is een complex probleem, maar in deze zelfstudie wordt het enigszins vereenvoudigd. U gebruikt deze als voor beeld van hoe u een predictive analytics oplossing kunt maken met behulp van Microsoft Azure Machine Learning Studio (klassiek). U gebruikt Azure Machine Learning Studio (klassiek) en een Machine Learning-webservice voor deze oplossing. 

In deze driedelige zelfstudie begint u met openbaar beschikbare kredietrisicogegevens.  Vervolgens ontwikkelt en traint u een voorspellend model.  En ten slotte implementeert u het model als een webservice.

In [deel één van de zelf studie](tutorial-part1-credit-risk.md)hebt u een machine learning Studio (klassieke) werk ruimte gemaakt, gegevens geüpload en een experiment gemaakt.

In [deel twee van de zelfstudie](tutorial-part2-credit-risk-train.md) hebt u modellen getraind en geëvalueerd.

In dit deel van de zelfstudie gaat u het volgende doen:

> [!div class="checklist"]
> * Implementatie voorbereiden
> * De webservice implementeren
> * De webservice testen
> * De webservice beheren
> * De webservice openen

## <a name="prerequisites"></a>Vereisten

Voltooi [deel twee van de zelfstudie](tutorial-part2-credit-risk-train.md).

## <a name="prepare-for-deployment"></a>Implementatie voorbereiden
Als u anderen de mogelijkheid wilt bieden om het voorspellende model te gebruiken dat u in deze zelfstudie hebt ontwikkeld, kunt u het als webservice implementeren in Azure.

Tot nu hebt u geëxperimenteerd met het trainen van het model. Met de geïmplementeerde service wordt echter niet meer getraind - hij wordt gebruikt om nieuwe voorspellingen te genereren door de invoer van de gebruiker te beoordelen op basis van het model. We gaan dus nu voorbereidingen treffen om dit experiment van een ***trainingsexperiment*** om te zetten in een ***voorspellend*** experiment. 

Het voorbereiden op de implementatie bestaat uit drie stappen:  

1. Een van de modellen verwijderen
1. Het *trainingsexperiment* dat u hebt gemaakt, converteren naar een *voorspellend experiment*
1. Het voorspellende experiment implementeren als webservice

### <a name="remove-one-of-the-models"></a>Een van de modellen verwijderen

Om te beginnen met u dit experiment beperken. Momenteel bestaat het experiment over twee verschillende modellen, maar u moet slechts één model gebruiken wanneer u het experiment implementeert als webservice.  

Stel dat u hebt besloten dat het Boosted Tree-model beter presteerde dan het dan de SVM-model. Het eerste wat u moet doen, is het verwijderen van de [twee klasse-ondersteunings vector machine][two-class-support-vector-machine] module en de modules die werden gebruikt voor het trainen van IT. Het is een goed idee om eerst een kopie van het experiment te maken door op **Opslaan als** te klikken aan de onderzijde van het experimentcanvas.

U moet de volgende modules verwijderen:  

* [Vector computer met twee klassen ondersteuning][two-class-support-vector-machine]
* [Model][train-model] -en [score model][score-model] -modules die hieraan zijn gekoppeld
* [Gegevens normaliseren][normalize-data] (beide)
* [Model evalueren][evaluate-model] (omdat de evaluatie van de modellen is voltooid)

Selecteer de modules en druk op Delete, of klik met de rechtermuisknop op de modules en selecteer **Verwijderen**. 

![Markeren welke modules moeten worden verwijderd om het computer model van de ondersteunings vector te verwijderen](./media/tutorial-part3-credit-risk-deploy/publish3a.png)

Het model zou er nu ongeveer zo uit moeten zien:

![Het resulterende experiment wanneer het machine model van de ondersteunings vector wordt verwijderd](./media/tutorial-part3-credit-risk-deploy/publish3.png)

Nu kunt u dit model implementeren met behulp van de [Geboostte beslissings structuur met twee klassen][two-class-boosted-decision-tree].

### <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Het trainingsexperiment converteren naar een voorspellend experiment

Om het model voor te bereiden voor implementatie moet u het trainingsexperiment omzetten in een voorspellend experiment. Dit omvat drie stappen:

1. Sla het model dat u hebt getraind op en vervang de trainingsmodules
1. Werk het experiment bij door modules te verwijderen die alleen voor de training nodig waren
1. Bepaal waar de webservice invoer accepteert en waar de uitvoer wordt gegenereerd

U kunt dit handmatig doen, maar gelukkig kunnen alle drie de stappen worden uitgevoerd door onder in het experimentcanvas te klikken op **Set Up Web Service** (Webservice configureren). Selecteer dan de optie **Predictive Web Service** (Voorspellende webservice).

> [!TIP]
> Als u meer informatie wilt over wat er gebeurt wanneer u een trainings experiment converteert naar een voorspellend experiment, raadpleegt u [hoe u uw model voorbereidt op implementatie in azure machine learning Studio (klassiek)](convert-training-experiment-to-scoring-experiment.md).

Wanneer u op **Set Up Web Service** klikt, gebeuren er verschillende zaken:

* Het getrainde model wordt omgezet in één module met een **getraind model**. De module wordt opgeslagen in het modulepalet links van het experimentcanvas. (Terug te vinden bij de **getrainde modellen**)
* Modules die zijn gebruikt voor de training, worden verwijderd. Specifieker gezegd:
  * [Geboostte beslissings structuur met twee klassen][two-class-boosted-decision-tree]
  * [Model trainen][train-model]
  * [Gegevens splitsen][split]
  * de tweede [R-script][execute-r-script] module voor uitvoering die is gebruikt voor test gegevens
* Het opgeslagen getrainde model wordt weer toegevoegd aan het experiment
* De modules **Webservice-invoer** en **Webservice-uitvoer** worden toegevoegd (deze bepalen waar de gegevens van de gebruiker worden ingevoerd in het model, welke gegevens worden geretourneerd en wanneer de webservice wordt geopend)

> [!NOTE]
> U kunt zien dat het experiment in twee delen wordt opgeslagen op tabbladen die boven aan het experimentcanvas zijn toegevoegd. Het oorspronkelijke trainingsexperiment staat op het tabblad **Trainingsexperiment** en het zojuist gemaakte voorspellende experiment staat bij **Voorspellend experiment**. U gaat het voorspellende experiment implementeren als webservice.

U moet nog een extra stap uitvoeren bij dit specifieke experiment.
u hebt twee [R-script][execute-r-script] modules voor uitvoeren toegevoegd om een wegings functie voor de gegevens op te geven. Dat trucje had u nodig voor trainingen en tests, dus u kunt ze uit het uiteindelijke model verwijderen.
Machine Learning Studio (klassiek) heeft de module voor het uitvoeren van een [R-script][execute-r-script] verwijderd wanneer de [Splits][split] module werd verwijderd. U kunt nu de andere en [meta gegevens editor][metadata-editor] rechtstreeks aan het [score model][score-model]verwijderen.    

Het experiment ziet er nu als volgt uit:  

![Het getrainde model beoordelen](./media/tutorial-part3-credit-risk-deploy/publish4.png)


> [!NOTE]
> U vraagt zich misschien af waarom u de gegevensset UCI German Credit Card niet hebt meegenomen uit het voorspellende experiment. De service gaat de gegevens van de gebruiker beoordelen (niet de oorspronkelijke gegevensset), dus waarom zou u de oorspronkelijke gegevensset in het model laten staan?
> 
> De service heeft de oorspronkelijke creditcardgegevens niet nodig. Het schema van de gegevens is echter wel nodig. Dit bevat informatie over het aantal kolommen dat er is en welke kolommen numeriek zijn. Deze schemagegevens zijn nodig om de gegevens van de gebruiker te kunnen interpreteren. U laat deze onderdelen verbonden zodat de beoordelingsmodule beschikt over het gegevenssetschema wanneer de service actief is. De gegevens worden niet gebruikt, maar het schema wel.  
> 
>Het is belangrijk om te weten dat als uw oorspronkelijke gegevensset het label bevatte, het verwachte schema van de webinvoer ook een kolom met dat label verwacht! U kunt dit oplossen door het label te verwijderen in combinatie met andere gegevens die in de gegevensset van de training zaten, maar die niet in de webinvoer staan. Doe dit voordat u de webinvoer en trainingsgegevensset koppelt aan een algemene module. 
> 

Voer het experiment nog een keer uit (Klik op **uitvoeren**). Als u wilt controleren of het model nog steeds werkt, klikt u op de uitvoer van de module [score model][score-model] en selecteert u **resultaten weer geven**. U kunt zien dat de oorspronkelijke gegevens worden weergegeven, samen met de kredietrisicowaarde ('beoordeelde labels') en de waarschijnlijkheidswaarde van de beoordeling ('beoordeelde waarschijnlijkheden'). 

## <a name="deploy-the-web-service"></a>De webservice implementeren
U kunt het experiment als klassieke webservice implementeren of als nieuwe webservice (op basis van Azure Resource Manager).

### <a name="deploy-as-a-classic-web-service"></a>Als een klassieke webservice implementeren
Als u op basis van het experiment een klassieke webservice wilt implementeren, klikt u op **Webservice implementeren** onder het canvas en selecteert u **Webservice implementeren (klassiek)** . Machine Learning Studio (klassiek) implementeert het experiment als een webservice en gaat u naar het dash board voor die webservice. Op deze pagina kunt u terugkeren naar het experiment (**Momentopname weergeven** of **Nieuwste weergeven**) en een eenvoudige test uitvoeren voor de webservice (zie **De webservice testen** hieronder). Er staat hier ook informatie over het maken van toepassingen die toegang kunnen verkrijgen tot de webservice. In de volgende stap van deze zelfstudie komt u daar meer over te weten.

![Dashboard van de webservice](./media/tutorial-part3-credit-risk-deploy/publish6.png)


U kunt de service configureren door te klikken op het tabblad **configuratie** . Hier kunt u de service naam wijzigen (deze wordt standaard de naam van het experiment gegeven) en een beschrijving geven. U kunt ook meer beschrijvende labels opgeven voor de invoer- en uitvoergegevens.  

![De webservice configureren](./media/tutorial-part3-credit-risk-deploy/publish5.png)


### <a name="deploy-as-a-new-web-service"></a>Als een nieuwe webservice implementeren

> [!NOTE] 
> Voor het implementeren van een nieuwe webservice moet u over voldoende machtigingen beschikken in het abonnement waarvoor u de webservice implementeert. Zie [Manage a web service using the Azure Machine Learning Web Services portal](manage-new-webservice.md) (Een webservice beheren met de Azure Machine Learning-portal voor webservices) voor meer informatie. 

Een nieuwe webservice implementeren op basis van het experiment:

1. Klik op **Webservice implementeren** onder het canvas en selecteer **Webservice implementeren [nieuw]** . Machine Learning Studio (klassiek) brengt u over naar de pagina Azure Machine Learning webservices **implementeren** .

1. Voer een naam in voor de webservice. 

1. Bij **Prijsplan** kunt u een bestaand prijsplan selecteren of u selecteert Nieuwe maken. Geef het nieuwe plan dan een naam en selecteer de maandelijkse optie. Standaard worden de prijscategorieën voor uw standaardregio gebruikt en de webservice wordt ook in die regio geïmplementeerd.

1. Klik op **Implementeren**.

Na een paar minuten wordt de pagina **Quickstart** van uw webservice geopend.

U kunt de service configureren door op het tabblad **configureren** te klikken. Hier kunt u de service titel wijzigen en een beschrijving geven. 

Als u de webservice wilt testen, klikt u op het tabblad **Testen** (zie **De webservice testen** hieronder). Voor informatie over het maken van toepassingen die toegang hebben tot de webservice, klikt u op het tabblad **Verbruiken** (in de volgende stap van deze zelfstudie krijgt u hier meer informatie over).

> [!TIP]
> U kunt de webservice na het implementeren bijwerken. Als u bijvoorbeeld uw model wilt wijzigen, bewerkt u het trainingsexperiment, verbetert u de modelparameters en klikt u op **Webservice implementeren**. Selecteer dan **Webservice implementeren (klassiek)** of **Webservice implementeren (nieuw)** . Wanneer u het experiment opnieuw implementeert, wordt de webservice vervangen. Er wordt dan gebruikgemaakt van het bijgewerkte model.  
> 
> 

## <a name="test-the-web-service"></a>De webservice testen

Wanneer de webservice wordt geopend, worden de gegevens van de gebruiker door gegeven in de module voor de invoer van de **webservice** , waar deze wordt doorgestuurd naar de module [score model][score-model] en deze heeft beoordeeld. Met deze manier waarop u het voorspellende experiment hebt ingesteld, verwacht het model gegevens in dezelfde indeling als die van de oorspronkelijke kredietrisicogegevensset.
De resultaten worden aan de gebruiker geretourneerd vanuit de webservice, via de module **Webservice-uitvoer**.

> [!TIP]
> Op de manier waarop u het voorspellende experiment hebt geconfigureerd, worden de volledige resultaten van de module [score model][score-model] geretourneerd. Deze omvatten alle invoergegevens, plus de kredietrisicowaarde en de beoordelingswaarschijnlijkheid. U kunt echter ook iets anders retourneren als u wilt; u zou bijvoorbeeld alleen de kredietrisicowaarde kunnen retourneren. Als u dit wilt doen, voegt u een module [select columns][select-columns] in tussen [score model][score-model] en de **webservice-uitvoer** om kolommen te elimineren die u niet door de webservice wilt laten retour neren. 
> 
> 

U kunt een klassieke webservice testen in **machine learning Studio (klassiek)** of in de **Azure machine learning** -portal van webservices.
U kunt nieuwe webservices alleen testen in de portal voor **Machine Learning-webservices**.

> [!TIP]
> Wanneer u een test uitvoert in de portal voor Azure Machine Learning-webservices, kunt u de portal voorbeeldgegevens laten maken. Deze kunt u vervolgens gebruiken voor het testen van de aanvraag/antwoord-service. Op de pagina **Configureren** selecteert u Ja bij **Voorbeeldgegevens inschakelen?** . Wanneer u het tabblad Aanvraag/antwoord opent op de pagina **Testen**, vult de portal de voorbeeldgegevens in van de oorspronkelijke kredietrisicogegevensset.

### <a name="test-a-classic-web-service"></a>Een klassieke webservice testen

U kunt een klassieke webservice testen in Machine Learning Studio (klassiek) of in het Machine Learning Web Services-portal. 

#### <a name="test-in-machine-learning-studio-classic"></a>Testen in Machine Learning Studio (klassiek)

1. Op de pagina **DASHBOARD** van de webservice klikt u op de knop **Testen** onder het **standaardeindpunt**. Er wordt een dialoogvenster weergegeven en u wordt om de invoergegevens voor de service gevraagd. Dit zijn dezelfde kolommen die zijn weergegeven in de oorspronkelijke kredietrisicogegevensset.  

1. Voer een set gegevens in en klik vervolgens op **OK**. 

#### <a name="test-in-the-machine-learning-web-services-portal"></a>Testen in de Machine Learning-webserviceportal

1. Op de pagina **DASHBOARD** van de webservice klikt u op de koppeling **Voorbeeld testen** onder het **standaardeindpunt**. De testpagina in de portal voor Azure Machine Learning-webservices wordt geopend voor het webservice-eindpunt. U wordt gevraagd om de invoergegevens voor de service. Dit zijn dezelfde kolommen die zijn weergegeven in de oorspronkelijke kredietrisicogegevensset.

2. Klik op **Aanvraag/antwoord testen**. 

### <a name="test-a-new-web-service"></a>Een nieuwe webservice testen

U kunt nieuwe webservices alleen testen in de portal voor Machine Learning-webservices.

1. In de portal voor [Azure Machine Learning-webservices](https://services.azureml.net/quickstart) klikt u boven aan de pagina op **Testen**. De pagina **Testen** wordt geopend. Hier kunt u gegevens voor de service invoeren. De invoervelden die worden weergegeven, komen overeen met de kolommen die werden weergegeven in de oorspronkelijke kredietrisicogegevensset. 

1. Voer een set gegevens in en klik vervolgens op **Test Request-Response** (Aanvraag/antwoord testen).

De resultaten van de test worden aan de rechterkant van de pagina weergegeven, in de uitvoerkolom. 


## <a name="manage-the-web-service"></a>De webservice beheren

Wanneer u uw webservice hebt geïmplementeerd (klassiek of nieuw), kunt u deze beheren via de [Microsoft Azure Machine Learning-webservice](https://services.azureml.net/quickstart)portal.

De prestaties van uw webservice bewaken:

1. Meld u aan bij de [Microsoft Azure Machine Learning-webservices](https://services.azureml.net/quickstart)portal
1. Klik op **Webservices**
1. Klik op uw webservice
1. Klik op het **dashboard**

## <a name="access-the-web-service"></a>De webservice openen

In de vorige stap van deze zelfstudie hebt u een webservice geïmplementeerd die gebruikmaakt voorspellingsmodel voor kredietrisico. Gebruikers kunnen er nu gegevens naar verzenden en resultaten ontvangen. 

De webservice is een Azure-webservice waarmee gegevens kunnen worden ontvangen en geretourneerd. Hiervoor wordt op één van de volgende manieren gebruikgemaakt van REST API's:  

* **Aanvraag/antwoord**: de gebruiker stuurt een of meer rijen kredietgegevens naar de service aan de hand van een HTTP-protocol. De service reageert met een of meer resultatensets.
* **Batchuitvoering**: de gebruiker slaat een of meer rijen kredietgegevens op in een Azure-blob en stuurt vervolgens de bloblocatie naar de service. De service beoordeelt de rijen met gegevens in de invoerblob, slaat de resultaten op in een andere blob en retourneert de URL van de container.  

Zie [een Azure machine learning-webservice met een web-app-sjabloon gebruiken](/azure/machine-learning/studio/consume-web-services)voor meer informatie over het openen en gebruiken van de webservice.



## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de volgende stappen voltooid:

> [!div class="checklist"]
> * Implementatie voorbereiden
> * De webservice implementeren
> * De webservice testen
> * De webservice beheren
> * De webservice openen

U kunt ook een aangepaste toepassing ontwikkelen voor toegang tot de webservice. Gebruik hiervoor de beginnerscode die wordt aangeboden in de programmeertalen R, C# en Python.

> [!div class="nextstepaction"]
> [Een Azure Machine Learning-webservice gebruiken](consume-web-services.md)

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
