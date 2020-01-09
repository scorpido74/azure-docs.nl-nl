---
title: 'Zelf studie: Azure-app configuratie gebruiken om functie vlaggen te beheren'
titleSuffix: Azure App Configuration
description: In deze zelf studie leert u hoe u functie vlaggen onafhankelijk van uw toepassing kunt beheren met behulp van Azure-app configuratie.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 620cd6e1712a89e13bf876310c0d3248c4a4d1e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75414014"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Zelf studie: functie vlaggen in Azure-app configuratie beheren

U kunt alle functie vlaggen opslaan in Azure-app configuratie en deze beheren vanaf één locatie. App-configuratie heeft een portal voor gebruikers met de naam **feature Manager** die specifiek is ontworpen voor functie vlaggen. App-configuratie biedt ook systeem eigen ondersteuning voor het gegevens schema .NET Core-functie-Flag.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Functie vlaggen definiëren en beheren in app-configuratie.
> * U hebt toegang tot functie vlaggen vanuit uw toepassing.

## <a name="create-feature-flags"></a>Functie vlaggen maken

De functie beheerder in de Azure Portal voor app-configuratie biedt een gebruikers interface voor het maken en beheren van de functie vlaggen die u in uw toepassingen gebruikt.

Een nieuwe functie vlag toevoegen:

1. Selecteer **functie beheer** >  **+ toevoegen** om een functie vlag toe te voegen.

    ![Lijst met functie vlaggen](./media/azure-app-configuration-feature-flags.png)

1. Voer een unieke sleutel naam in voor de functie vlag. U hebt deze naam nodig om te verwijzen naar de markering in uw code.

1. Als u wilt, geeft u de functie een beschrijving.

1. De begin status van de functie vlag instellen. Deze status is doorgaans *uitgeschakeld* of *ingeschakeld*. De status *bij* is gewijzigd in *voorwaardelijk* als u een filter aan de functie vlag toevoegt.

    ![Functie vlag maken](./media/azure-app-configuration-feature-flag-create.png)

1. Wanneer de status is *ingeschakeld*, selecteert u **+ filter toevoegen** om eventuele aanvullende voor waarden op te geven voor het kwalificeren van de status. Voer een ingebouwde of aangepaste filter sleutel in en selecteer **+ para meter toevoegen** om een of meer para meters aan het filter te koppelen. Ingebouwde filters zijn onder andere:

    | Sleutel | JSON-para meters |
    |---|---|
    | Microsoft.Percentage | {"Waarde": 0-100 procent} |
    | Microsoft.TimeWindow | {"Start": UTC-tijd, "end": UTC-tijd} |

    ![Functie vlag filter](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Status vlaggen voor onderdelen bijwerken

De status waarde van een functie vlag wijzigen:

1. Selecteer **functie beheer**.

1. Selecteer het beletsel teken ( **...** ) aan de rechter kant van een functie vlag die u wilt wijzigen en selecteer vervolgens **bewerken**.

1. Stel een nieuwe status in voor de functie vlag.

## <a name="access-feature-flags"></a>Functie vlaggen voor toegang

Functie vlaggen die zijn gemaakt met functie beheer worden opgeslagen en opgehaald als normale sleutel waarden. Ze worden bewaard onder een speciaal naam ruimte voorvoegsel `.appconfig.featureflag`. Als u de onderliggende sleutel waarden wilt weer geven, gebruikt u de configuratie Verkenner. Uw toepassing kan deze waarden ophalen met behulp van de configuratie providers van de configuratie van de app, Sdk's, opdracht regel uitbreidingen en REST Api's.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u functie vlaggen en hun statussen beheert met behulp van app-configuratie. Zie het volgende artikel voor meer informatie over de ondersteuning voor functie beheer in app-configuratie en ASP.NET Core:

* [Functie vlaggen gebruiken in een ASP.NET Core-app](./use-feature-flags-dotnet-core.md)
