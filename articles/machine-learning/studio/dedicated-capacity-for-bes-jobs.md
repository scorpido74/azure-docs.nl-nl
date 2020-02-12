---
title: Azure Batch-service taken
titleSuffix: ML Studio (classic) - Azure
description: Overzicht van Azure Batch Services voor Machine Learning Studio (klassieke) taken. Batchverwerking van toepassingen kunt u maken van groepen waarop u kunt indienen batchtaken.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio (classic) | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: c1a7f44539d07761814f5cb3d7074c9428423154
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153533"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-classic-jobs"></a>Azure Batch-service voor Azure Machine Learning Studio (klassieke) taken

Verwerking van machine Learning Batch-Pool voorziet schalen door de klant beheerde in de Service Azure Machine Learning Batch kan worden uitgevoerd. Klassieke batchverwerking voor machine learning vindt plaats in een omgeving met meerdere tenants, waardoor het aantal gelijktijdige taken die u kunt indienen en taken in de wachtrij op basis van first in first out. Deze onzekerheid betekent dat u kunt geen nauwkeurig te voorspellen wanneer de taak wordt uitgevoerd.

Batchverwerking van toepassingen kunt u maken van groepen waarop u kunt indienen batchtaken. U bepaalt de grootte van de groep en aan welke toepassingen de taak is verzonden. De BES-taak wordt uitgevoerd in een eigen verwerking ruimte bieden voorspelbare verwerkingsprestaties verbeteren en de mogelijkheid om te maken van resourcegroepen die overeenkomen met de verwerkingsbelasting dat u verzendt.

> [!NOTE]
> U moet een nieuwe op Resource Manager gebaseerde Machine Learning-webservice hebben om een groep te maken. Nadat u een BES hebt gemaakt, kunt u elke wille keurige-webservice uitvoeren, zowel op basis van een nieuw Resource Manager als op het klassieke, in de groep.

## <a name="how-to-use-batch-pool-processing"></a>Het gebruik van de verwerking van de Batch-Pool

Configuratie van de verwerking van de Batch-Pool is momenteel niet beschikbaar via de Azure-portal. Voor het gebruik van de verwerking van de Batch-Pool, moet u:

-   Aanroepen van CSS voor het maken van een Batch-Account voor groep van toepassingen en het verkrijgen van de Service-URL van een Pool en een autorisatiesleutel
-   Een nieuwe Resource Manager gebaseerde webservice en een abonnement maken

Voor het maken van uw account, aanroepen van Microsoft Customer Service and Support (CSS) en geef uw abonnement-ID. CSS geschikt is voor u om de juiste capaciteit voor uw scenario te bepalen. CSS configureert vervolgens uw account met het maximum aantal groepen die u kunt maken en het maximum aantal virtuele machines (VM's) die u kunt in elke groep van toepassingen plaatsen. Nadat uw account is geconfigureerd, kunt u de URL van de Pool-Service en een autorisatiesleutel vindt.

Nadat uw account is gemaakt, kunt u de URL van groep van toepassingen en -autorisatie-sleutel gebruiken om uit te voeren van de beheerbewerkingen van toepassingen in de Batch-Pool.

![Batch-pool servicearchitectuur.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

U maken groepen door de bewerking-adresgroep maken op de URL van de groep van toepassingen die CSS aan u verstrekt aan te roepen. Wanneer u een pool maakt, geeft u het aantal virtuele machines en de URL van de swagger.json van een nieuwe Resource Manager op basis van Machine Learning-webservice. Deze webservice wordt geboden tot stand brengen van de koppeling met de facturering. De service Batch-Pool gebruikt de swagger.json naar deze pool koppelen aan een abonnement. U kunt elke wille keurige BES-webservice uitvoeren, zowel op basis van een nieuw Resource Manager als op het klassieke, in de groep.

U kunt een nieuwe Resource Manager gebaseerde webservice gebruiken, maar houd er rekening mee dat de facturering voor de taken worden in rekening gebracht op basis van het abonnement dat is gekoppeld aan die service. U wilt maken van een web-service en een nieuw abonnement specifiek voor het uitvoeren van Batch-Pool-jobs.

Zie [een Azure machine learning-webservice implementeren](deploy-a-machine-learning-web-service.md)voor meer informatie over het maken van webservices.

Als u een groep hebt gemaakt, kunt u de BES-taak met behulp van de URL van de Batch-aanvragen voor de webservice verzenden. U kunt verzenden naar een pool of klassieke batchverwerking. Als u wilt een taak voor het verwerken van Batch-Pool, kunt u de volgende parameter toevoegen aan de hoofdtekst van de taak indienen aanvraag:

"AzureBatchPoolId": "&lt;groeps-ID&gt;"

Als u de parameter niet toevoegt, wordt de taak wordt uitgevoerd in de klassieke batch proces-omgeving. Als de groep voldoende bronnen beschikbaar heeft, wordt de taak wordt onmiddellijk uitgevoerd. Als de groep heeft geen gratis resources, uw taak is in de wachtrij geplaatst totdat er een resource beschikbaar is.

Als u merkt dat u de capaciteit van uw pools regelmatig bereikt en u meer capaciteit nodig hebt, kunt u CSS aanroepen en werken met een medewerker van uw quotums verhogen.

Van de Voorbeeldaanvraag:

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Overwegingen bij het gebruik van de verwerking van de Batch-Pool

Verwerking van de batch-Pool is een factureerbare service altijd beschikbaar en die vereist dat u deze koppelen aan een abonnement op basis van Resource Manager. U wordt alleen gefactureerd voor het aantal uren van die de groep wordt uitgevoerd. ongeacht het aantal taken uitgevoerd gedurende die tijd van toepassingen. Als u een pool maakt, wordt u gefactureerd voor de rekenuren van elke virtuele machine in de groep totdat de pool wordt verwijderd, zelfs als er geen batchtaken worden uitgevoerd in de groep. De facturering voor de virtuele machines wordt gestart wanneer ze klaar zijn met het inrichten en stopt wanneer ze zijn verwijderd. U kunt elk van de plannen gebruiken die zijn gevonden op de [pagina met machine learning prijzen](https://azure.microsoft.com/pricing/details/machine-learning/).

Facturering-voorbeeld:

Als u een Batch-Pool met 2 virtuele machines maken en het na 24 uur verwijdert is uw abonnement gedebiteerd 48 rekenuren; ongeacht hoeveel taken zijn uitgevoerd tijdens die periode.

Als u een Batch-Pool met 4 virtuele machines maken en deze na 12 uur verwijdert, wordt uw abonnement is ook gedebiteerd 48-rekenuren.

Het is raadzaam dat u de status van de taak om te bepalen wanneer taken zijn voltooid opvragen. Wanneer alle taken zijn voltooid, moet u de bewerking van de Pool vergroten of verkleinen om in te stellen van het aantal virtuele machines in de pool op nul aanroepen. Als u op de resources in de korte en u wilt maken van een nieuwe groep, bijvoorbeeld om aan te brengen kosten in rekening op basis van een ander abonnement kiest, kunt u de pool in plaats daarvan verwijderen wanneer alle taken zijn voltooid.


| **Verwerking van batch-pool gebruiken wanneer**    | **Gebruik klassieke batch verwerking wanneer**  |
|---|---|
|U moet een groot aantal taken uitvoeren<br>of<br/>U moet weten dat uw taken onmiddellijk worden uitgevoerd<br/>of<br/>U moet een gegarandeerde doorvoer. Bijvoorbeeld, moet u een aantal taken uitvoeren in een bepaalde periode en wilt uitbreiden van uw rekenresources om te voldoen aan uw behoeften.    | U worden hoeft slechts een paar taken uitgevoerd<br/>And<br/> U hoeft niet de taak direct uitvoeren |
