---
title: Azure Batch-service taken
titleSuffix: ML Studio (classic) Azure
description: Overzicht van Azure Batch Services voor Machine Learning Studio (klassieke) taken. Met de verwerking van batch groepen kunt u groepen maken waarop u batch-taken kunt verzenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio (classic) | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: a0e829f61117df25f8643301a0acdadeafd7c267
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684812"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-classic-jobs"></a>Azure Batch-service voor Azure Machine Learning Studio (klassieke) taken

Machine Learning verwerking van batch-Pools biedt door de klant beheerde schaal voor de Azure Machine Learning batch-uitvoerings service. Klassieke batch verwerking voor machine learning vindt plaats in een omgeving met meerdere tenants, waarmee het aantal gelijktijdige taken dat u kunt indienen, wordt beperkt en taken in de wachtrij worden geplaatst op basis van de eerste in-first-out. Deze onzekerheid houdt in dat u niet nauw keurig kunt voors pellen wanneer uw taak wordt uitgevoerd.

Met de verwerking van batch groepen kunt u groepen maken waarop u batch-taken kunt verzenden. U bepaalt de grootte van de pool en aan welke pool de taak wordt verzonden. Uw BES-taak wordt uitgevoerd in een eigen verwerkings ruimte en biedt voorspel bare verwerkings prestaties en de mogelijkheid om resource groepen te maken die overeenkomen met de verwerkings belasting die u verzendt.

> [!NOTE]
> U moet een nieuwe op Resource Manager gebaseerde Machine Learning-webservice hebben om een groep te maken. Nadat u een BES hebt gemaakt, kunt u elke wille keurige-webservice uitvoeren, zowel op basis van een nieuw Resource Manager als op het klassieke, in de groep.

## <a name="how-to-use-batch-pool-processing"></a>Verwerking van batch groepen gebruiken

De configuratie van de batch-pool verwerking is momenteel niet beschikbaar via de Azure Portal. Als u de verwerking van batch-groepen wilt gebruiken, moet u het volgende doen:

-   CSS aanroepen om een batch-pool account te maken en een groeps service-URL en een autorisatie sleutel te verkrijgen
-   Een nieuwe op Resource Manager gebaseerde webservice en een facturerings plan maken

Neem contact op met de klanten service en ondersteuning van micro soft (CSS) en geef uw abonnements-ID op om uw account te maken. CSS werkt samen met u om te bepalen wat de juiste capaciteit is voor uw scenario. CSS configureert uw account vervolgens met het maximum aantal Pools dat u kunt maken en het maximum aantal virtuele machines (Vm's) dat u in elke pool kunt plaatsen. Zodra uw account is geconfigureerd, hebt u de URL van uw groeps service en een autorisatie sleutel opgegeven.

Nadat uw account is gemaakt, gebruikt u de groeps service-URL en de autorisatie sleutel voor het uitvoeren van groeps beheer bewerkingen in de batch-pool.

![Service architectuur van de batch-pool.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

U maakt Pools door de bewerking groep maken aan te roepen op de URL van de groeps service die u aan u hebt door gegeven. Wanneer u een pool maakt, geeft u het aantal Vm's en de URL op van de Swagger. json van een nieuwe op Resource Manager gebaseerde Machine Learning-webservice. Deze webservice is bedoeld om de facturerings koppeling tot stand te brengen. De batch-pool service maakt gebruik van Swagger. json om de groep te koppelen aan een facturerings plan. U kunt elke wille keurige BES-webservice uitvoeren, zowel op basis van een nieuw Resource Manager als op het klassieke, in de groep.

U kunt elke nieuwe op Resource Manager gebaseerde webservice gebruiken, maar houd er rekening mee dat de facturering voor de taken wordt gefactureerd op basis van het facturerings plan dat aan die service is gekoppeld. U kunt het beste een webservice en een nieuw abonnement maken speciaal voor het uitvoeren van batch pool taken.

Zie [een Azure machine learning-webservice implementeren](deploy-a-machine-learning-web-service.md)voor meer informatie over het maken van webservices.

Zodra u een groep hebt gemaakt, verzendt u de BES-taak met de batch-aanvraag-URL voor de webservice. U kunt ervoor kiezen om deze naar een groep of naar klassieke batch verwerking te verzenden. Als u een taak wilt verzenden naar batch pool verwerking, voegt u de volgende para meter toe aan de hoofd tekst van de opdracht indienen:

"AzureBatchPoolId": "&lt;groeps-ID&gt;"

Als u de para meter niet toevoegt, wordt de taak uitgevoerd in de klassieke batch proces omgeving. Als de pool beschik bare resources heeft, wordt de taak onmiddellijk gestart. Als de pool geen vrije resources heeft, wordt uw taak in de wachtrij geplaatst totdat een resource beschikbaar is.

Als u merkt dat u de capaciteit van uw Pools regel matig bereikt en u behoefte hebt aan een verhoogde capaciteit, kunt u CSS aanroepen en samen werken met een vertegenwoordiger om uw quota te verg Roten.

Voorbeeld aanvraag:

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

## <a name="considerations-when-using-batch-pool-processing"></a>Overwegingen bij het gebruik van batch-pool verwerking

De verwerking van batch-groepen is een service die altijd wordt gefactureerd en die vereist dat u deze koppelt aan een op Resource Manager gebaseerd facturerings plan. Er worden alleen kosten in rekening gebracht voor het aantal reken uren dat de pool uitvoert. ongeacht het aantal taken dat tijdens die tijd groep wordt uitgevoerd. Als u een pool maakt, wordt u gefactureerd voor de reken uren van elke virtuele machine in de groep totdat de groep is verwijderd, zelfs als er geen batch-taken worden uitgevoerd in de groep. Facturering voor de virtuele machines begint wanneer de inrichting is voltooid en stopt wanneer ze zijn verwijderd. U kunt elk van de plannen gebruiken die zijn gevonden op de [pagina met machine learning prijzen](https://azure.microsoft.com/pricing/details/machine-learning/).

Voor beeld van facturering:

Als u een batch-pool met twee virtuele machines maakt en deze verwijdert na 24 uur, wordt uw abonnement gefactureerd 48 reken uren. ongeacht het aantal taken dat tijdens die periode is uitgevoerd.

Als u een batch-pool met vier virtuele machines maakt en deze na 12 uur verwijdert, wordt uw abonnement ook 48 reken uren gedebiteerd.

U wordt aangeraden de taak status te controleren om te bepalen wanneer taken zijn voltooid. Wanneer alle taken zijn voltooid, roept u de bewerking voor het wijzigen van de groep aan om het aantal virtuele machines in de groep in te stellen op nul. Als u gepaard gaat met groeps resources en u een nieuwe groep wilt maken, bijvoorbeeld om een ander abonnement te betalen, kunt u in plaats daarvan de pool verwijderen wanneer alle taken zijn voltooid.


| **Verwerking van batch-pool gebruiken wanneer**    | **Gebruik klassieke batch verwerking wanneer**  |
|---|---|
|U moet een groot aantal taken uitvoeren<br>of<br/>U moet weten dat uw taken onmiddellijk worden uitgevoerd<br/>of<br/>U hebt gegarandeerde door voer. U moet bijvoorbeeld een aantal taken in een bepaald tijds bestek uitvoeren en uw reken resources zo schalen dat ze aan uw behoeften voldoen.    | U voert slechts een paar taken uit<br/>And<br/> U hebt de taken niet nodig om direct te worden uitgevoerd |
