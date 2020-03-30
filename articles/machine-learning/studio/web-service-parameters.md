---
title: Webserviceparameters - Azure Machine Learning Studio (klassiek) | Microsoft Documenten
description: Azure Machine Learning Web Service Parameters gebruiken om het gedrag van uw model te wijzigen wanneer de webservice wordt geopend.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/12/2017
ms.openlocfilehash: d6ddd9603f22bd3820d18be020b9c620cf06aa42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204406"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Parameters voor Azure Machine Learning Studio (klassieke) webservice gebruiken

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Een Azure Machine Learning-webservice wordt gemaakt door een experiment te publiceren dat modules bevat met configureerbare parameters. In sommige gevallen u het gedrag van de module wijzigen terwijl de webservice wordt uitgevoerd. *Met WebService-parameters* u deze taak uitvoeren. 

Een veelvoorkomend voorbeeld is het instellen van de module [Gegevens importeren,][reader] zodat de gebruiker van de gepubliceerde webservice een andere gegevensbron kan opgeven wanneer de webservice wordt geopend. Of configureer de module [Gegevens exporteren][writer] zodat een andere bestemming kan worden opgegeven. Enkele andere voorbeelden zijn het wijzigen van het aantal bits voor de [Feature Hashing-module][feature-hashing] of het aantal gewenste functies voor de module [Filtergebaseerde functieselectie.][filter-based-feature-selection] 

U Webserviceparameters instellen en deze koppelen aan een of meer moduleparameters in uw experiment en u opgeven of deze vereist of optioneel zijn. De gebruiker van de webservice kan vervolgens waarden voor deze parameters opgeven wanneer hij de webservice aanroept. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Webserviceparameters instellen en gebruiken
U definieert een webserviceparameter door op het pictogram naast de parameter voor een module te klikken en 'Instellen als webserviceparameter' te selecteren. Hierdoor wordt een nieuwe parameter voor webservice gemaakt en wordt deze verbonden met die parameter. Wanneer de webservice wordt geopend, kan de gebruiker vervolgens een waarde opgeven voor de parameter Webservice en deze wordt toegepast op de parameter module.

Zodra u een webserviceparameter hebt gedefinieerd, is deze beschikbaar voor elke andere parameter in het experiment. Als u een webserviceparameter definieert die is gekoppeld aan een parameter voor één module, u diezelfde webserviceparameter voor elke andere module gebruiken, zolang de parameter hetzelfde type waarde verwacht. Als de parameter Webservice bijvoorbeeld een numerieke waarde is, kan deze alleen worden gebruikt voor moduleparameters die een numerieke waarde verwachten. Wanneer de gebruiker een waarde instelt voor de parameter Webservice, wordt deze toegepast op alle bijbehorende moduleparameters.

U beslissen of u een standaardwaarde wilt opgeven voor de parameter Webservice. Als u dit doet, is de parameter optioneel voor de gebruiker van de webservice. Als u geen standaardwaarde opgeeft, moet de gebruiker een waarde invoeren wanneer de webservice wordt geopend.

De API-documentatie voor de webservice bevat informatie voor de webservicegebruiker over het programmatisch opgeven van de parameter Webservice bij het openen van de webservice.

> [!NOTE]
> De API-documentatie voor een klassieke webservice wordt geleverd via de **API Help-paginakoppeling** in het **dashboard** van de webservice in Machine Learning Studio (klassiek). De API-documentatie voor een nieuwe webservice wordt geleverd via de [Azure Machine Learning Web Services-portal](https://services.azureml.net/Quickstart) op de **API-pagina's Consumeren** en **Branieer** voor uw webservice.
> 
> 

## <a name="example"></a>Voorbeeld
Laten we er bijvoorbeeld van uitgaan dat we een experiment hebben met een [module Gegevens exporteren][writer] die informatie naar Azure blob-opslag verzendt. We definiëren een parameter voor webservice met de naam 'Blob-pad' waarmee de webservicegebruiker het pad naar de blobopslag kan wijzigen wanneer de service wordt geopend.

1. Klik in Machine Learning Studio (klassiek) op de module [Gegevens exporteren][writer] om deze te selecteren. De eigenschappen worden weergegeven in het deelvenster Eigenschappen rechts van het experimentcanvas.
2. Geef het opslagtype op:
   
   * Selecteer **onder Opgeven van de gegevensbestemming**de optie 'Azure Blob Storage'.
   * Selecteer **onder Het verificatietype opgeven**de optie 'Account'.
   * Voer de accountgegevens in voor de Azure Blob-opslag. 

3. Klik op het pictogram rechts van het **pad naar blob, te beginnen met de parameter container**. Dit ziet er als volgt uit:
   
   ![Pictogram Parameter voor webservice](./media/web-service-parameters/icon.png)
   
   Selecteer 'Instellen als parameter voor webservice'.
   
   Onder **Webserviceparameters** onder aan het deelvenster Eigenschappen wordt een item toegevoegd met de naam 'Pad naar blob begin met container'. Dit is de parameter Webservice die nu is gekoppeld aan deze parameter [exportgegevens.][writer]
4. Als u de naam van de parameter Webservice wilt wijzigen, klikt u op de naam, voert u 'Blobpad' in en drukt u op **Enter.** 
5. Als u een standaardwaarde voor de parameter Webservice wilt opgeven, klikt u op het pictogram rechts van de naam, selecteert u 'Standaardwaarde opgeven', voert u een waarde in (bijvoorbeeld 'container1/output1.csv') en drukt u op **enter.**
   
   ![Parameter Webservice](./media/web-service-parameters/parameter.png)
6. Klik **op Uitvoeren**. 
7. Klik **op Webservice implementeren** en selecteer **Webservice implementeren [Klassiek]** of **Webservice implementeren [Nieuw]** om de webservice te implementeren.

> [!NOTE] 
> Als u een nieuwe webservice wilt implementeren, moet u over voldoende machtigingen beschikken in het abonnement waarvoor u de webservice implementeert. Zie [Een webservice beheren met de Azure Machine Learning Web Services-portal](manage-new-webservice.md)voor meer informatie. 

De gebruiker van de webservice kan nu een nieuwe bestemming opgeven voor de module [Gegevens exporteren][writer] wanneer hij de webservice ingaat.

## <a name="more-information"></a>Meer informatie
Zie het item [Webserviceparameters](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) in het [machine learning-blog](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx)voor een gedetailleerder voorbeeld.

Zie [Een Azure Machine Learning-webservice gebruiken](consume-web-services.md)voor meer informatie over het openen van een Machine Learning-webservice.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

