---
title: Webservice-para meters-Azure Machine Learning Studio (klassiek) | Microsoft Docs
description: Het gebruik van Azure Machine Learning Web Service Parameters aan het gedrag van uw model wijzigen wanneer de web-service wordt geopend.
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
ms.openlocfilehash: 984d2e02ff75df459275fd10e313a4950c8d79c0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432180"
---
# <a name="use-azure-machine-learning-studio-classic-web-service-parameters"></a>Para meters voor de webservice Azure Machine Learning Studio (klassiek) gebruiken
Een Azure Machine Learning-webservice wordt gemaakt door het publiceren van een experiment met modules met configureerbare parameters. In sommige gevallen kunt u het gedrag van de module niet wijzigen terwijl de webservice wordt uitgevoerd. *Web-Parameters van de Service* zodat u deze taak. 

Een voor beeld hiervan is het instellen van de module [import data][reader] , zodat de gebruiker van de gepubliceerde webservice een andere gegevens bron kan opgeven wanneer de webservice wordt geopend. Of configureer de module [gegevens exporteren][writer] zodat er een andere bestemming kan worden opgegeven. Enkele andere voor beelden zijn het wijzigen van het aantal bits voor de [functie-hash][feature-hashing] -module of het aantal gewenste functies voor de [functie selectie module op basis van filter][filter-based-feature-selection] . 

U kunt Parameters van de Web Service en deze koppelen aan een of meer moduleparameters in uw experiment, en kunt u opgeven of ze verplicht of optioneel zijn. De gebruiker van de webservice kan vervolgens waarden opgeven voor deze parameters wanneer ze de webservice aanroept. 



## <a name="how-to-set-and-use-web-service-parameters"></a>Het instellen en Webserviceparameters gebruiken
U definieert een Parameter van Web Service door te klikken op het pictogram naast de parameter voor een module en selecteer 'Instellen als web Serviceparameter'. Hiermee maakt u een nieuwe Web Service-Parameter en verbindt u deze met deze module-parameter. Als de webservice wordt geopend, vervolgens de gebruiker kan een waarde opgeven voor de Web Service-Parameter en wordt toegepast op de module-parameter.

Wanneer u een Web Service-Parameter definiëren, is het beschikbaar voor een andere module-parameter in het experiment. Als u een Web-Service-Parameter die is gekoppeld aan een parameter voor één module definiëren, kunt u die dezelfde Web Service-Parameter voor een andere module, zolang de parameter wordt verwacht de hetzelfde type waarde dat. Bijvoorbeeld, als de Web Service-Parameter een numerieke waarde is, kan klikt u vervolgens deze alleen worden gebruikt voor de parameters van de module die een numerieke waarde verwacht. Wanneer de gebruiker wordt een waarde voor de Web Service-Parameter ingesteld, wordt deze worden toegepast op alle bijbehorende module-parameters.

U kunt vervolgens beslissen of een standaardwaarde opgeven voor de Web Service-Parameter. Als u dit doet, klikt u vervolgens is de parameter optioneel voor de gebruiker van de webservice. Als u een standaardwaarde niet opgeeft, is de gebruiker vereist om een waarde wanneer de web-service wordt geopend.

De API-documentatie voor de webservice bevat informatie voor de gebruiker van de web-service voor het opgeven van de Web Service-Parameter is het via een programma bij het openen van de webservice.

> [!NOTE]
> De API-documentatie voor een klassieke webservice wordt geleverd via de koppeling **API Help pagina** in het **dash board** van de webservice in machine learning Studio (klassiek). De API-documentatie voor een nieuwe webservice wordt geleverd via de [Azure Machine Learning-webservices](https://services.azureml.net/Quickstart) portal op de **verbruiken** en **Swagger API** pagina's voor uw web- de service.
> 
> 

## <a name="example"></a>Voorbeeld
Als voor beeld gaan we ervan uit dat we een experiment hebben met een [export gegevens][writer] module die informatie naar Azure Blob-opslag verzendt. Definiëren we een Web-Service-Parameter met de naam 'Blob-pad' waarmee de gebruiker van de web service het pad naar de blob-opslag wijzigen wanneer de service wordt geopend.

1. Klik in Machine Learning Studio (klassiek) op de module [gegevens exporteren][writer] om deze te selecteren. De eigenschappen worden weergegeven in het deelvenster met eigenschappen aan de rechterkant van het experimentcanvas.
2. Geef het opslagtype:
   
   * Onder **Geef bestemming**, selecteert u 'Azure Blob-opslag.
   * Onder **Geef verificatietype**, selecteert u 'Account'.
   * Voer de accountgegevens voor de Azure blob-opslag. 

3. Klik op het pictogram aan de rechterkant van de **pad naar de blob die begint met de parameter container**. Het ziet er als volgt uit:
   
   ![Web Service Parameter-pictogram](./media/web-service-parameters/icon.png)
   
   Selecteer 'Instellen als web Serviceparameter'.
   
   Een vermelding wordt toegevoegd onder **Webserviceparameters** onderaan in het deelvenster met eigenschappen met de naam 'Pad naar de blob die begint met de container'. Dit is de para meter van de webservice die nu is gekoppeld aan deze module parameter voor [export gegevens][writer] .
4. Wijzig de naam van de Web Service-Parameter, klikt u op de naam, typt u 'blobpad', en druk op de **Enter** sleutel. 
5. Als u wilt een standaardwaarde opgeven voor de Web Service-Parameter, klik op het pictogram aan de rechterkant van de naam, selecteert u "Geef standaardwaarde", voer een waarde (bijvoorbeeld ' container1/output1.csv'), en druk op de **Enter** sleutel.
   
   ![Web Service-Parameter](./media/web-service-parameters/parameter.png)
6. Klik op **Run**. 
7. Klik op **webservice implementeren** en selecteer **webservice implementeren [klassieke]** of **Web Service implementeren [Nieuw]** om de webservice te implementeren.

> [!NOTE] 
> Voor het implementeren van een nieuwe webservice moet u voldoende machtigingen hebben in het abonnement waarvoor u de webservice implementeert. Zie voor meer informatie, [beheren van een webservice met behulp van de Azure Machine Learning-webserviceportal](manage-new-webservice.md). 

De gebruiker van de webservice kan nu een nieuwe bestemming opgeven voor de module [gegevens exporteren][writer] bij het openen van de webservice.

## <a name="more-information"></a>Meer informatie
Zie voor een uitgebreider voorbeeld de [Webserviceparameters](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) vermelding in de [Machine Learning Blog](https://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Zie voor meer informatie over het verkrijgen van toegang tot een Machine Learning-webservice [hoe u een Azure Machine Learning-webservice gebruiken](consume-web-services.md).

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/

