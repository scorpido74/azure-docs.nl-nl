---
title: Logboekregistratie van webservices - Azure Machine Learning Studio (klassiek) | Microsoft Documenten
description: Meer informatie over het inschakelen van logboekregistratie voor Machine Learning Studio (klassieke) webservices. Logging biedt aanvullende informatie om problemen met de API's op te lossen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217848"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Logboekregistratie inschakelen voor Azure Machine Learning Studio (klassieke) webservices

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Dit document bevat informatie over de loggingmogelijkheden van Machine Learning Studio (klassieke) webservices. Logging biedt aanvullende informatie, naast alleen een foutnummer en een bericht, die u kunnen helpen bij het oplossen van uw oproepen naar de Machine Learning Studio (klassieke) API's.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Logboekregistratie inschakelen voor een webservice

U schakelt logboekregistratie in via de [Azure Machine Learning Studio (klassieke) Web Services-portal.](https://services.azureml.net) 

1. Meld u aan bij de Azure Machine Learning [https://services.azureml.net](https://services.azureml.net)Studio (klassieke) Web Services-portal op . Voor een klassieke webservice u ook naar de portal gaan door op **Nieuwe Web Services Experience** te klikken op de Machine Learning Studio (klassieke) Web Services-pagina in Studio (klassiek).

   ![Koppeling Nieuwe Web Services Experience](./media/web-services-logging/new-web-services-experience-link.png)

2. Klik op de bovenste menubalk op **Webservices** voor een nieuwe webservice of klik op **Klassieke webservices** voor een klassieke webservice.

   ![Nieuwe of klassieke webservices selecteren](./media/web-services-logging/select-web-service.png)

3. Klik voor een nieuwe webservice op de naam van de webservice. Klik voor een klassieke webservice op de naam van de webservice en klik vervolgens op de volgende pagina op het juiste eindpunt.

4. Klik op de bovenste menubalk op **Configureren**.

5. Stel de optie **Logboekregistratie inschakelen** in op *Fout* (alleen fouten registreren) of *Alles* (voor volledige logboekregistratie).

   ![Logboekregistratieniveau selecteren](./media/web-services-logging/enable-logging.png)

6. Klik op **Opslaan**.

7. Maak voor Klassieke webservices de **ml-diagnostics-container.**

   Alle webservicelogboeken worden bewaard in een blobcontainer met de naam **ml-diagnostics** in het opslagaccount dat is gekoppeld aan de webservice. Voor nieuwe webservices wordt deze container gemaakt wanneer u voor het eerst toegang hebt tot de webservice. Voor Klassieke webservices moet u de container maken als deze nog niet bestaat. 

   1. Ga in de [Azure-portal](https://portal.azure.com)naar het opslagaccount dat is gekoppeld aan de webservice.

   2. Klik onder **Blob Service** op **Containers**.

   3. Als de **ml-diagnostics** van de container niet bestaat, klikt u op **+Container**, geeft u de container de naam "ml-diagnostics" en selecteert u het **access-type** als 'Blob'. Klik op **OK**.

      ![Een nieuwe container maken om uw diagnostische logboeken op te slaan](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Voor een klassieke webservice heeft het Web Services Dashboard in Machine Learning Studio (klassiek) ook een schakelaar om logboekregistratie mogelijk te maken. Omdat logboekregistratie nu echter wordt beheerd via de Web Services-portal, moet u logboekregistratie via de portal inschakelen zoals beschreven in dit artikel. Als u het inloggen in Studio (klassiek) al hebt ingeschakeld, schakelt u logboekregistratie uit en schakelt u deze opnieuw in.


## <a name="the-effects-of-enabling-logging"></a>De effecten van het inschakelen van houtkap
Wanneer logboekregistratie is ingeschakeld, worden de diagnose en fouten van het eindpunt van de webservice geregistreerd in de **ml-diagnostics blobcontainer** in het Azure Storage-account dat is gekoppeld aan de werkruimte van de gebruiker. Deze container bevat alle diagnostische gegevens voor alle eindpunten van de webservice voor alle werkruimten die aan dit opslagaccount zijn gekoppeld.

De logboeken kunnen worden bekeken met behulp van een van de verschillende tools die beschikbaar zijn om een Azure Storage-account te verkennen. Het gemakkelijkst kan zijn om naar het opslagaccount in de Azure-portal te navigeren, op **Containers**te klikken en vervolgens op de **containerml-diagnostics**te klikken.  

## <a name="log-blob-detail-information"></a>Details van de blob logboekblob
Elke blob in de container bevat de diagnostische informatie voor precies een van de volgende acties:

* Een uitvoering van de batch-execution methode  
* Een uitvoering van de methode Request-Response  
* Initialisatie van een container met aanvraagrespons

De naam van elke blob heeft een voorvoegsel van het volgende formulier: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Waarbij _Logboektype_ een van de volgende waarden is:  

* batch  
* score/aanvragen  
* score/init  

