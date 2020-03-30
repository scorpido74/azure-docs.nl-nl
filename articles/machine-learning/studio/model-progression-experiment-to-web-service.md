---
title: Hoe een model een webservice wordt
titleSuffix: ML Studio (classic) - Azure
description: Een overzicht van de mechanica van hoe uw Azure Machine Learning Studio (klassiek) model vordert van een ontwikkelingsexperiment naar een webservice.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: e6a8fe7771776a92b3c28a188bc352e3bb105313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217920"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Hoe een Machine Learning Studio (klassiek) model vordert van een experiment naar een webservice

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Azure Machine Learning Studio (klassiek) biedt een interactief canvas waarmee u een ***experiment*** ontwikkelen, uitvoeren, testen en herhalen dat een voorspellend analysemodel vertegenwoordigt. Er zijn een breed scala aan modules beschikbaar die kunnen:

* Gegevens invoeren in uw experiment
* De gegevens manipuleren
* Een model trainen met behulp van machine learning-algoritmen
* Het model scoren
* De resultaten evalueren
* Eindwaarden uitvoer

Zodra u tevreden bent met uw experiment, u het implementeren als een ***klassieke Azure Machine Learning-webservice*** of een ***nieuwe Azure Machine Learning-webservice,*** zodat gebruikers deze nieuwe gegevens kunnen verzenden en resultaten kunnen ontvangen.

In dit artikel geven we een overzicht van de mechanica van hoe uw Machine Learning-model vordert van een ontwikkelingsexperiment naar een geoperationaliseerde webservice.

> [!NOTE]
> Er zijn andere manieren om machine learning-modellen te ontwikkelen en te implementeren, maar dit artikel is gericht op hoe u Machine Learning Studio (klassiek) gebruikt. Zie bijvoorbeeld de blogpost [Build & Deploy Predictive Web Apps using RStudio and Azure Machine Learning studio](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx)om een klassieke voorspellende webservice met R te lezen.
>
>

Hoewel Azure Machine Learning Studio (klassiek) is ontworpen om u te helpen bij het ontwikkelen en implementeren van een *voorspellend analysemodel,* is het mogelijk om Studio (klassiek) te gebruiken om een experiment te ontwikkelen dat geen voorspellend analysemodel bevat. Een experiment kan bijvoorbeeld alleen gegevens invoeren, manipuleren en vervolgens de resultaten uitvoeren. Net als een voorspellend analyse-experiment u dit niet-voorspellende experiment implementeren als een webservice, maar het is een eenvoudiger proces omdat het experiment geen machine learning-model traint of scoort. Hoewel het niet typisch is om Studio (klassiek) op deze manier te gebruiken, nemen we het op in de discussie, zodat we een volledige uitleg kunnen geven over hoe Studio (klassiek) werkt.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Ontwikkelen en implementeren van een voorspellende webservice
Dit zijn de fasen die een typische oplossing volgt wanneer u deze ontwikkelt en implementeert met Machine Learning Studio (klassiek):

![Implementatiestroom](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Figuur 1 - Stadia van een typisch voorspellend analysemodel*

### <a name="the-training-experiment"></a>Het trainingsexperiment
Het ***trainingsexperiment*** is de eerste fase van het ontwikkelen van uw webservice in Machine Learning Studio (klassiek). Het doel van het trainingsexperiment is om u een plek te geven om een machine learning-model te ontwikkelen, te testen, te herhalen en uiteindelijk te trainen. U zelfs meerdere modellen tegelijk trainen terwijl u op zoek bent naar de beste oplossing, maar zodra u klaar bent met experimenteren, selecteert u één getraind model en elimineert u de rest uit het experiment. Zie [Een voorspellende analyseoplossing ontwikkelen voor kredietrisicobeoordeling ontwikkelen in Azure Machine Learning Studio (klassiek)](tutorial-part1-credit-risk.md)voor een voorbeeld van het ontwikkelen van een voorspellend analyse-experiment.

### <a name="the-predictive-experiment"></a>Het voorspellende experiment
Zodra u een getraind model in uw trainingsexperiment hebt, klikt u op **Webservice instellen** en selecteert u **Predictive Web Service** in Machine Learning Studio (klassiek) om het proces te starten om uw trainingsexperiment om te zetten in een ***voorspellend experiment.*** Het doel van het voorspellende experiment is om uw getrainde model te gebruiken om nieuwe gegevens te scoren, met als doel uiteindelijk geoperationaliseerd te worden als Een Azure Web Service.

Deze conversie wordt voor u uitgevoerd via de volgende stappen:

* Zet de set modules die worden gebruikt voor training om in één module en sla deze op als een getraind model
* Elimineer vreemde modules die niet gerelateerd zijn aan scoren
* Invoer- en uitvoerpoorten toevoegen die de uiteindelijke webservice zal gebruiken

Er kunnen meer wijzigingen zijn die u wilt aanbrengen om uw voorspellende experiment klaar te maken om te worden geïmplementeerd als webservice. Als u bijvoorbeeld wilt dat de webservice slechts een subset van resultaten uitvoert, u een filtermodule toevoegen vóór de uitvoerpoort.

In dit conversieproces wordt het trainingsexperiment niet verwijderd. Wanneer het proces is voltooid, hebt u twee tabbladen in Studio (klassiek): een voor het trainingsexperiment en een voor het voorspellende experiment. Op deze manier u wijzigingen aanbrengen in het trainingsexperiment voordat u uw webservice implementeert en het voorspellende experiment opnieuw opbouwen. Of u een kopie van het trainingsexperiment opslaan om een andere lijn van experimenten te starten.

> [!NOTE]
> Wanneer u op **Predictive Web Service** klikt, start u een automatisch proces om uw trainingsexperiment om te zetten in een voorspellend experiment, en dit werkt in de meeste gevallen goed. Als uw trainingsexperiment complex is (u hebt bijvoorbeeld meerdere trainingspaden die u samenbrengt), u deze conversie handmatig uitvoeren. Zie [Uw model voorbereiden op implementatie in Azure Machine Learning Studio (klassiek) voor](convert-training-experiment-to-scoring-experiment.md)meer informatie.
>
>

### <a name="the-web-service"></a>De webservice
Zodra u ervan overtuigd bent dat uw voorspellende experiment klaar is, u uw service implementeren als een klassieke webservice of een nieuwe webservice op basis van Azure Resource Manager. Als u uw model wilt operationaliseren door het te implementeren als een *klassieke machine learning-webservice,* klikt u op **Webservice implementeren** en selecteert u **Webservice implementeren [Klassiek]**. Als u wilt implementeren als *nieuwe machine learning-webservice,* klikt u op **Webservice implementeren** en selecteert u **Webservice implementeren [Nieuw].** Gebruikers kunnen nu gegevens naar uw model verzenden met behulp van de REST API van de webservice en de resultaten terugkrijgen. Zie [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Azure Machine Learning-webservice gebruiken) voor meer informatie.

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Het niet-typische geval: een niet-voorspellende webservice maken
Als uw experiment geen voorspellend analysemodel traint, hoeft u niet zowel een trainingsexperiment als een scoreexperiment te maken - er is slechts één experiment en u het implementeren als een webservice. Machine Learning Studio (klassiek) detecteert of uw experiment een voorspellend model bevat door de modules te analyseren die u hebt gebruikt.

Nadat u hebt herhaald op uw experiment en zijn tevreden mee:

1. Klik **op Webservice instellen** en selecteer **Webservice omscholen** - invoer- en uitvoerknooppunten worden automatisch toegevoegd
2. Klik **op Uitvoeren**
3. Klik op **Webservice implementeren** en selecteer **Webservice implementeren [Klassiek]** of **Webservice implementeren [Nieuw]** afhankelijk van de omgeving waarop u wilt implementeren.

Uw webservice is nu geïmplementeerd en u deze openen en beheren, net als een voorspellende webservice.

## <a name="updating-your-web-service"></a>Uw webservice bijwerken
Nu u uw experiment als webservice hebt geïmplementeerd, wat als u het moet bijwerken?

Dat hangt af van wat je nodig hebt om bij te werken:

**U wilt de invoer of uitvoer wijzigen of de manier wijzigen waarop de webservice gegevens manipuleert**

Als u het model niet wijzigt, maar alleen de manier wijzigt waarop de webservice gegevens verwerkt, u het voorspellende experiment bewerken en vervolgens op **Webservice implementeren** klikken en **Webservice [Klassiek]** implementeren selecteren of **Webservice [Nieuw] opnieuw implementeren.** De webservice is gestopt, het bijgewerkte voorspellende experiment wordt geïmplementeerd en de webservice wordt opnieuw gestart.

Hier is een voorbeeld: Stel dat uw voorspellende experiment de hele rij invoergegevens retourneert met het voorspelde resultaat. U besluiten dat u wilt dat de webservice alleen het resultaat retourneert. U dus een **projectkolommenmodule** toevoegen aan het voorspellende experiment, vlak voor de uitvoerpoort, om andere kolommen dan het resultaat uit te sluiten. Wanneer u op **Webservice implementeren** klikt en **Webservice [Klassiek] implementeren** selecteert of **Webservice [Nieuw]** opnieuw implementeren, wordt de webservice bijgewerkt.

**U wilt het model omscholen met nieuwe gegevens**

Als u uw machine learning-model wilt behouden, maar u wilt het omscholen met nieuwe gegevens, hebt u twee keuzes:

1. **Train het model om terwijl de webservice wordt uitgevoerd** - Als u uw model wilt omscholen terwijl de voorspellende webservice wordt uitgevoerd, u dit doen door een paar wijzigingen aan te brengen in het trainingsexperiment om er een ***omscholingsexperiment van***te maken, dan u het implementeren als een ** *omscholingswebservice.* ** Zie [Machine Learning-modellen programmatisch omscholen](/azure/machine-learning/studio/retrain-machine-learning-model)voor instructies over hoe u dit doen.
2. **Ga terug naar het oorspronkelijke trainingsexperiment en gebruik verschillende trainingsgegevens om uw model te ontwikkelen** - Uw voorspellende experiment is gekoppeld aan de webservice, maar het trainingsexperiment is niet direct gekoppeld op deze manier. Als u het oorspronkelijke trainingsexperiment wijzigt en op **Webservice instellen**klikt, wordt een *nieuw* voorspellend experiment gemaakt dat, wanneer deze wordt geïmplementeerd, een *nieuwe* webservice maakt. Het werkt niet alleen de oorspronkelijke webservice bij.

   Als u het trainingsexperiment wilt wijzigen, opent u het en klikt u op **Opslaan als** om een kopie te maken. Hierdoor blijft het oorspronkelijke trainingsexperiment, het voorspellende experiment en de webservice intact. U nu een nieuwe webservice maken met uw wijzigingen. Zodra u de nieuwe webservice hebt geïmplementeerd, u beslissen of u de vorige webservice wilt stoppen of naast de nieuwe service wilt laten werken.

**U wilt een ander model trainen**

Als u wijzigingen wilt aanbrengen in uw oorspronkelijke voorspellende experiment, zoals het selecteren van een ander machine learning-algoritme, het uitproberen van een andere trainingsmethode, enz., moet u de tweede hierboven beschreven procedure voor het omscholen van uw model volgen: open het trainingsexperiment, klik op **Opslaan als** om een kopie te maken en begin vervolgens het nieuwe pad af om uw model te ontwikkelen, het voorspellende experiment te maken en de webservice te implementeren. Hierdoor wordt een nieuwe webservice gemaakt die niets met de oorspronkelijke service te maken heeft - u beslissen welke, of beide, blijven draaien.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over het proces van ontwikkelen en experimenteren:

* het experiment converteren - [Uw model voorbereiden op implementatie in Azure Machine Learning Studio (klassiek)](convert-training-experiment-to-scoring-experiment.md)
* De webservice implementeren - [Een Azure Machine Learning-webservice implementeren](deploy-a-machine-learning-web-service.md)
* omscholing van het model - [Machine Learning-modellen programmatisch omscholen](/azure/machine-learning/studio/retrain-machine-learning-model)

Zie voor voorbeelden van het hele proces:

* [Machine learning-zelfstudie: maak uw eerste experiment in Azure Machine Learning Studio (klassiek)](create-experiment.md)
* [Walkthrough: Ontwikkel een predictive analytics-oplossing voor kredietrisicobeoordeling in Azure Machine Learning](tutorial-part1-credit-risk.md)

