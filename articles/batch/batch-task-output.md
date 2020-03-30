---
title: Taak- en taakuitvoer blijven uitvoeren naar een gegevensarchief - Azure Batch
description: Meer informatie over verschillende opties voor het aanhouden van uitvoergegevens uit batchtaken en -taken. U gegevens blijven gebruiken voor Azure Storage of naar een ander gegevensarchief.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0809a838f1d34491eb4e276ce356eded9b98756e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022831"
---
# <a name="persist-job-and-task-output"></a>Taken persistent maken

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Enkele veelvoorkomende voorbeelden van taakuitvoer zijn:

- Bestanden die zijn gemaakt wanneer de taak gegevens verwerkt.
- Logbestanden die zijn gekoppeld aan taakuitvoering.

In dit artikel worden verschillende opties beschreven voor het uitvoeren van taken.

## <a name="options-for-persisting-output"></a>Opties voor aanhoudende uitvoer

Afhankelijk van uw scenario zijn er een paar verschillende benaderingen die u volgen om de taakuitvoer voort te houden:

- [Gebruik de Batchservice-API](batch-task-output-files.md).  
- [Gebruik de bibliotheek Batchbestandsconventies voor .NET](batch-task-output-file-conventions.md).  
- Implementeer de batchbestandsconventiesstandaard in uw toepassing.
- Implementeer een aangepaste oplossing voor bestandsverplaatsing.

In de volgende secties worden elke benadering en algemene ontwerpoverwegingen voor aanhoudende output kort beschreven.

### <a name="use-the-batch-service-api"></a>De Batchservice-API gebruiken

De batchservice ondersteunt het opgeven van uitvoerbestanden in Azure Storage voor taakgegevens wanneer u [een taak toevoegt aan een taak](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) of een verzameling taken toevoegt aan een [taak.](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job)

Zie [Taakgegevens voor Azure-opslag blijven voor](batch-task-output-files.md)meer informatie over het uitvoeren van taken met de Batchservice-API .

### <a name="use-the-batch-file-conventions-library-for-net"></a>De bibliotheek Batchbestandsconventies gebruiken voor .NET

Batch definieert een optionele set conventies voor het benoemen van taakuitvoerbestanden in Azure Storage. De [batchbestandsconventiesstandaard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) beschrijft deze conventies. De standaard Bestandsconventies bepaalt de namen van de doelcontainer en het blobpad in Azure Storage voor een bepaald uitvoerbestand op basis van de namen van de taak en taak.

Het is aan u of u besluit om de standaard Bestandsconventies te gebruiken voor het benoemen van uw uitvoergegevensbestanden. U ook de naam van de doelcontainer en blob zoals u dat wilt. Als u de standaard Bestandsconventies gebruikt voor het benoemen van uitvoerbestanden, zijn uw uitvoerbestanden beschikbaar om te bekijken in de [Azure-portal.][portal]

Ontwikkelaars die Batch-oplossingen bouwen met C# en .NET, kunnen de [bibliotheek Bestandsconventies voor .NET][nuget_package] gebruiken om taakgegevens voorte houden aan een Azure Storage-account, volgens de [batchbestandsconventiesstandaard](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions). De bibliotheek Bestandsconventies verwerkt het verplaatsen van uitvoerbestanden naar Azure Storage en het benoemen van doelcontainers en blobs op een bekende manier.

Zie [Taak- en taakgegevens voor Azure Storage](batch-task-output-file-conventions.md)blijven voor meer informatie over de uitvoer van taken voor .NET.

### <a name="implement-the-batch-file-conventions-standard"></a>De standaard batchbestandsconventies implementeren

Als u een andere taal dan .NET gebruikt, u de [standaard BatchFile Conventions](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) implementeren in uw eigen toepassing.

U de standaard voor de naamgeving van bestandsconventies zelf implementeren wanneer u een bewezen naamgevingsschema wilt of wanneer u taakuitvoer in de Azure-portal wilt bekijken.

### <a name="implement-a-custom-file-movement-solution"></a>Een aangepaste oplossing voor bestandsverplaatsing implementeren

U ook uw eigen complete oplossing voor bestandsverplaatsing implementeren. Gebruik deze aanpak wanneer:

- U wilt taakgegevens blijven gebruiken voor een ander gegevensarchief dan Azure Storage. Als u bestanden wilt uploaden naar een gegevensarchief zoals Azure SQL of Azure DataLake, u een aangepast script of uitvoerbaar uploaden naar die locatie. U deze vervolgens aanroepen op de opdrachtregel nadat u uw primaire uitvoerbare hebt uitgevoerd. Op een Windows-knooppunt u bijvoorbeeld de volgende twee opdrachten aanroepen:`doMyWork.exe && uploadMyFilesToSql.exe`
- U wilt het controleren of vroegtijdig uploaden van de eerste resultaten uitvoeren.
- U wilt de gedetailleerde controle over foutafhandeling behouden. U bijvoorbeeld uw eigen oplossing implementeren als u taakafhankelijkheidsacties wilt gebruiken om bepaalde uploadacties uit te voeren op basis van specifieke taakexitcodes. Zie [Taakafhankelijkheden maken om taken uit te voeren die afhankelijk zijn van andere taken](batch-task-dependencies.md)voor meer informatie over taakafhankelijkheidsacties.

## <a name="design-considerations-for-persisting-output"></a>Ontwerpoverwegingen voor aanhoudende uitvoer

Houd bij het ontwerpen van uw batchoplossing rekening met de volgende factoren die verband houden met taak- en taakuitvoer.

- **Levensduur van het compute-knooppunt**: Compute-knooppunten zijn vaak van voorbijgaande aard, vooral in groepen met automatische schaal. Uitvoer van een taak die wordt uitgevoerd op een knooppunt is alleen beschikbaar zolang het knooppunt bestaat en alleen binnen de bestandsbewaarperiode die u voor de taak hebt ingesteld. Als een taak uitvoer produceert die nodig kan zijn nadat de taak is voltooid, moet de taak de uitvoerbestanden uploaden naar een duurzaam archief zoals Azure Storage.

- **Uitvoeropslag:** Azure Storage wordt aanbevolen als gegevensarchief voor taakuitvoer, maar u elke duurzame opslag gebruiken. Het uitvoeren van taken naar Azure Storage is geïntegreerd in de Batch service API. Als u een andere vorm van duurzame opslag gebruikt, moet u de toepassingslogica schrijven om de taakuitvoer zelf voort te houden.

- **Uitvoer ophalen:** u taakuitvoer rechtstreeks ophalen via de compute nodes in uw groep of uit Azure Storage of een ander gegevensarchief als u taakuitvoer hebt volgehouden. Als u de uitvoer van een taak rechtstreeks uit een compute node wilt ophalen, hebt u de bestandsnaam en de uitvoerlocatie op het knooppunt nodig. Als u taakuitvoer naar Azure Storage blijft uitvoeren, hebt u het volledige pad naar het bestand in Azure Storage nodig om de uitvoerbestanden met de Azure Storage SDK te downloaden.

- **Uitvoer weergeven:** Wanneer u naar een batchtaak in de Azure-portal navigeert en **Bestanden op knooppunt**selecteert, krijgt u alle bestanden te zien die aan de taak zijn gekoppeld, niet alleen de uitvoerbestanden waarin u geïnteresseerd bent. Nogmaals, bestanden op compute nodes zijn alleen beschikbaar zolang het knooppunt bestaat en alleen binnen de bestandsbewaartijd die u voor de taak hebt ingesteld. Als u taakuitvoer wilt weergeven die u hebt volgehouden in Azure Storage, u de Azure-portal of een Azure Storage-clienttoepassing gebruiken, zoals de [Azure Storage Explorer.][storage_explorer] Als u uitvoergegevens in Azure Storage wilt weergeven met de portal of een ander hulpprogramma, moet u de locatie van het bestand kennen en er rechtstreeks naar navigeren.

## <a name="next-steps"></a>Volgende stappen

- Ontdek de nieuwe functies in de Batch service API gebruiken om taakgegevens in [Persist-taakgegevens voor Azure Storage te](batch-task-output-files.md)blijven gebruiken met de Batch service API.
- Meer informatie over het gebruik van de bibliotheek BatchFile Conventions voor .NET in [Persist-taak- en taakgegevens naar Azure Storage met de bibliotheek BatchFile Conventions voor .NET](batch-task-output-file-conventions.md).
- Zie het voorbeeldproject [PersistOutputs][github_persistoutputs] op GitHub, dat laat zien hoe u zowel de batchclientbibliotheek voor .NET als de bibliotheek Bestandsconventies gebruiken voor .NET om taakuitvoer voor duurzame opslag voort te zetten.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
