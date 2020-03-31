---
title: Uw taken plannen
description: Gebruik taakplanning om uw taken te beheren.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.workload: big-compute
ms.date: 02/20/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 55ea8fb4cc0e65deaa89d718c4a46513716dcf54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672431"
---
# <a name="schedule-jobs-for-efficiency"></a>Taken plannen voor efficiëntie

Met batchtaken u eerst prioriteit geven aan de taken die u wilt uitvoeren, rekening houdend met taken die afhankelijk zijn van andere taken. Door uw taken te plannen, u ervoor zorgen dat u de minste hoeveelheid resources gebruikt. Knooppunten kunnen worden buiten gebruik gesteld wanneer dit niet nodig is, taken die afhankelijk zijn van andere taken worden net op tijd opgebouwd om de werkstromen te optimaliseren. Er wordt maar één taak per keer uitgevoerd. Een nieuwe start pas als de vorige is voltooid. U uw taak automatisch aanvullen. 

## <a name="benefit-of-job-scheduling"></a>Voordeel van taakplanning

Het voordeel van het plannen van taken is dat u een planning voor het maken van banen opgeven. De taken die u plant met taakbeheertaken, zijn gekoppeld aan een taak. De taak voor taakvoor taak maakt taken voor de taak. Hiervoor moet de taak voor taak voor taak voor taak taak worden geverifieerd met het Batch-account. Gebruik het AZ_BATCH_AUTHENTICATION_TOKEN access token. Het token geeft toegang tot de rest van de taak. 

## <a name="use-the-portal-to-schedule-a-job"></a>De portal gebruiken om een taak te plannen

   1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

   2. Selecteer het Batch-account waarin u taken wilt plannen.

   3. Selecteer **Toevoegen** om een nieuw taakschema te maken en vul het **basisformulier in**.



![Een taak plannen][1]

**Taakschema-id**: de unieke id voor dit taakschema.

**Weergavenaam:** De weergavenaam voor de taak hoeft niet uniek te zijn, maar heeft een maximale lengte van 1024 tekens.

**Niet uitvoeren totdat**: Hiermee geeft u de vroegste tijd op dat de taak wordt uitgevoerd. Als u dit niet instelt, wordt het schema klaar om taken onmiddellijk uit te voeren.

**Niet uitvoeren na**: Geen taken worden uitgevoerd na de tijd die u hier instelt. Als u geen tijd opgeeft, maakt u een terugkerend taakschema dat actief blijft totdat u het expliciet beëindigt.

**Herhalingsinterval:** u de hoeveelheid tijd tussen taken opgeven. U slechts één taak tegelijk hebben gepland, dus als het tijd is om een nieuwe taak te maken onder een taakschema, maar de vorige taak wordt nog steeds uitgevoerd, wordt de batchservice pas als de vorige taak is voltooid.  

**Startvenster**: Hier geeft u het tijdsinterval op, vanaf het tijdstip waarop de planning aangeeft dat er een taak moet worden gemaakt, totdat deze moet worden voltooid. Als de huidige taak niet wordt voltooid tijdens het venster, wordt de volgende taak niet gestart.

Onderaan het basisformulier geeft u de pool op waarop u de taak wilt uitvoeren. Als u uw pool-id-gegevens wilt vinden, selecteert u **Bijwerken**. 

![Groep opgeven][2]


**Pool ID:** Identificeer de pool waarop u de taak uitvoert.

**Taakconfiguratietaak**: Selecteer **Bijwerken** om de taak Jobmanager en de taken voor het voorbereiden en vrijgeven van taken voor taak te benoemen als u deze gebruikt.

**Prioriteit**: Geef de baan een prioriteit.

**Maximale wandkloktijd**: Stel de maximale tijd in waarvoor de taak kan worden uitgevoerd. Als de taak niet binnen het tijdsbestek wordt voltooid, beëindigt Batch de taak. Als u dit niet instelt, is er geen tijdslimiet voor de taak.

**Maximaal aantal taken:** geef het aantal keren op dat een taak maximaal vier keer opnieuw kan worden geprobeerd. Dit is niet hetzelfde als het aantal nieuwe pogingen dat een API-aanroep zou kunnen hebben.

**Wanneer alle taken zijn voltooid:** de standaardinstelling is geen actie.

**Wanneer een taak mislukt:** De standaardinstelling is geen actie. Een taak mislukt als het aantal nieuwe gegevens is uitgeput of er een fout is opgetreden bij het starten van de taak. 

Nadat u **Opslaan**hebt geselecteerd, als u naar **Taakschema's** in de linkernavigatie gaat, u de uitvoering van de taak bijhouden door **Uitvoeringsgegevens te**selecteren.


## <a name="for-more-information"></a>Voor meer informatie

Zie het taakschema van de [AZ-batch](https://docs.microsoft.com/cli/azure/batch/job-schedule?view=azure-cli-latest)om een taak te beheren met de Azure CLI.

## <a name="next-steps"></a>Volgende stappen

[Maak taakafhankelijkheden om taken uit te voeren die afhankelijk zijn van andere taken](batch-task-dependencies.md).





[1]: ./media/batch-job-schedule/add_job_schedule-02.png
[2]: ./media/batch-job-schedule/add_job_schedule-03.png


