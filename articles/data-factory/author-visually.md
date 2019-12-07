---
title: Visuele ontwerpen
description: Meer informatie over het gebruik van visuele ontwerpen in Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 01/09/2019
ms.openlocfilehash: 734a9de3eaa44a149c10d1a268d09024f3ef279d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891623"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Visueel ontwerpen in Azure Data Factory

Met de Azure Data Factory gebruikers interface-ervaring (UX) kunt u resources visueel ontwerpen en implementeren voor uw data factory zonder dat u code hoeft te schrijven. U kunt activiteiten naar een pijplijn doek slepen, test uitvoeringen uitvoeren, iteratief fouten opsporen en uw pijplijn uitvoeringen implementeren en bewaken.

## <a name="authoring-canvas"></a>Canvas ontwerpen

Klik op het potlood pictogram om het **ontwerp canvas**te openen. 

![Canvas ontwerpen](media/author-visually/authoring-canvas.png)

Hier maakt u een ontwerp van de pijp lijnen, activiteiten, gegevens sets, gekoppelde services, gegevens stromen, triggers en Integration Runtimes waaruit uw fabriek bestaat. Zie [gegevens kopiëren met behulp van de Kopieer activiteit](tutorial-copy-data-portal.md)om aan de slag te gaan met het maken van een pijp lijn met behulp van het ontwerp doek. 

De standaard ervaring voor het ontwerpen van visuele elementen werkt rechtstreeks met de Data Factory-service. De integratie van Azure opslag plaatsen git of GitHub wordt ook ondersteund om broncode beheer en samen werking mogelijk te maken voor werk op uw data factory-pijp lijnen. Zie [broncode beheer in azure Data Factory](source-control.md)voor meer informatie over de verschillen tussen deze ontwerp ervaring.

## <a name="expressions-and-functions"></a>Expressies en functies

Expressies en functies kunnen worden gebruikt in plaats van statische waarden om veel eigenschappen in Azure Data Factory op te geven.

Als u een expressie voor een eigenschaps waarde wilt opgeven, selecteert u **dynamische inhoud toevoegen** of klikt u op **Alt + P** bij focus op het veld.

![Dynamische inhoud toevoegen](media/author-visually/dynamic-content-1.png)

Hiermee opent u de **opbouw functie voor Data Factory Expression** , waar u expressies kunt bouwen op basis van ondersteunde systeem variabelen, uitvoer van activiteiten, functies en door de gebruiker opgegeven variabelen of para meters. 

![Opbouwfunctie voor expressies](media/author-visually/dynamic-content-2.png)

Zie [expressies en functies in azure Data Factory](control-flow-expression-language-functions.md)voor meer informatie over de taal van de expressie.

## <a name="provide-feedback"></a>Feedback geven

Selecteer **feedback** om commentaar te geven over functies of om micro soft op de hoogte te stellen over problemen met het hulp programma:

![Feedback](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Volgende stappen

Zie [pijp lijnen bewaken en beheren via een programma](monitor-programmatically.md)voor meer informatie over het controleren en beheren van pijp lijnen.
