---
title: Logboek registratie van webservice-Azure Machine Learning Studio (klassiek) | Microsoft Docs
description: Meer informatie over het inschakelen van logboek registratie voor Machine Learning Studio (klassieke) webservices. Logboekregistratie bevat aanvullende informatie over het oplossen van de API's.
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
ms.date: 06/15/2017
ms.openlocfilehash: 90e7692fe0e254074d8176d719d0ca9abad54a9b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217848"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Logboek registratie inschakelen voor Azure Machine Learning Studio (klassieke) webservices

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Dit document bevat informatie over de logboek registratie van Machine Learning Studio (klassieke) webservices. Logboek registratie biedt aanvullende informatie, behalve een fout nummer en een bericht, waarmee u uw aanroepen naar de Machine Learning Studio (klassieke) Api's kunt oplossen.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Het inschakelen van logboekregistratie voor een webservice

U schakelt logboek registratie in via de [Web Services-portal van Azure machine learning Studio (klassiek)](https://services.azureml.net) . 

1. Meld u aan bij de Web Services-portal van Azure Machine Learning Studio (Classic) op [https://services.azureml.net](https://services.azureml.net). Voor een klassieke webservice kunt u ook naar de portal gaan door te klikken op **nieuwe Web Services-ervaring** op de pagina machine learning Studio (klassiek) Web Services in Studio (klassiek).

   ![Nieuwe Web Services-ervaring-koppeling](./media/web-services-logging/new-web-services-experience-link.png)

2. Klik in de bovenste menu balk op **webservices** voor een nieuwe webservice, of klik op **klassieke** webservices voor een klassieke webservice.

   ![Selecteer Nieuw of klassieke webservices](./media/web-services-logging/select-web-service.png)

3. Klik op de naam van de webservice voor een nieuwe webservice. Voor een klassieke webservice, klikt u op de naam van de webservice en klik vervolgens op de volgende pagina op het juiste eindpunt.

4. Klik op de bovenste menu balk op **configureren**.

5. Stel de optie **logboek registratie inschakelen** in op *fouten (alleen* logboeken vastleggen) of *alle* (voor volledige logboek registratie).

   ![Selecteer het logboekregistratieniveau van](./media/web-services-logging/enable-logging.png)

6. Klik op **Opslaan**.

7. Voor klassieke webservices maakt u de container **met ml-diagnose** .

   Alle logboeken van de webservice worden bewaard in een BLOB-container met de naam **ml-diagnostische gegevens** in het opslag account dat is gekoppeld aan de webservice. Deze container wordt de eerste keer dat u toegang de webservice tot gemaakt voor nieuwe webservices. Voor klassieke webservices moet u de container maken als deze nog niet bestaat. 

   1. Ga in het [Azure Portal](https://portal.azure.com)naar het opslag account dat is gekoppeld aan de webservice.

   2. Klik onder **Blob Service** op **Containers**.

   3. Als de container **ml-diagnostische gegevens** niet aanwezig is, klikt u op **+ container**, geeft u de container de naam ' ml-Diagnostics ' en selecteert u het **toegangs type** als ' BLOB '. Klik op **OK**.

      ![Een nieuwe container maken om uw Diagnostische logboeken op te slaan](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Voor een klassieke webservice heeft het dash board webservices in Machine Learning Studio (klassiek) ook een schakel optie om logboek registratie in te scha kelen. Aangezien logboekregistratie wordt nu beheerd via het Web Services-portal, moet u Schakel logboekregistratie in via de portal zoals wordt beschreven in dit artikel. Als u logboek registratie al hebt ingeschakeld in Studio (klassiek), schakelt u in de portal voor webservices logboek registratie uit en schakelt u het opnieuw in.


## <a name="the-effects-of-enabling-logging"></a>De gevolgen van logboekregistratie inschakelen
Wanneer logboek registratie is ingeschakeld, worden de diagnostische gegevens en fouten van het eind punt van de webservice vastgelegd in de BLOB-container voor **milliliters diagnostiek** in het Azure Storage account dat is gekoppeld aan de werk ruimte van de gebruiker. Deze container bevat de diagnostische gegevens voor alle de web service-eindpunten voor alle werkruimten die zijn gekoppeld aan dit storage-account.

De logboeken kunnen worden weergegeven met behulp van een van de verschillende hulpprogramma's die beschikbaar zijn voor het verkennen van een Azure Storage-Account. Het eenvoudigste kan zijn om naar het opslag account in de Azure Portal te gaan, op **containers**te klikken en vervolgens op de container **ml-diagnostische gegevens**te klikken.  

## <a name="log-blob-detail-information"></a>Logboekgegevens voor blob
Elke blob in de container bevat de diagnostische gegevens voor exact één van de volgende acties:

* Een uitvoering van de Batch Execution-methode  
* Een uitvoering van de Request Response-methode  
* De initialisatie van een Request Response-container

De naam van elke blob heeft een voorvoegsel van de volgende notatie: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Waarbij _logboek type_ een van de volgende waarden is:  

* batch  
* score/aanvragen  
* score/init  

