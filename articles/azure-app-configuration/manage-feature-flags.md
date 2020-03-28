---
title: 'Zelfstudie: Azure-app-configuratie gebruiken om functievlaggen te beheren'
titleSuffix: Azure App Configuration
description: In deze zelfstudie leert u hoe u functievlaggen afzonderlijk van uw toepassing beheert met Azure App Configuration.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: ccab8014000f9f684249bf2c1f800f74c92e7369
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76899363"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Zelfstudie: Functievlaggen beheren in Azure App-configuratie

U alle functievlaggen opslaan in Azure App-configuratie en deze vanaf één plaats beheren. App-configuratie heeft een portal-gebruikersinterface met de naam **Feature Manager** die speciaal is ontworpen voor functievlaggen. App-configuratie ondersteunt ook native het .NET Core-functie-flag-gegevensschema.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Functievlaggen definiëren en beheren in app-configuratie.
> * Toegang functie vlaggen van uw toepassing.

## <a name="create-feature-flags"></a>Functievlaggen maken

De functiebeheer in de Azure-portal voor app-configuratie biedt een gebruikersinterface voor het maken en beheren van de functievlaggen die u in uw toepassingen gebruikt.

Ga als lid van het volgende over een nieuwe functievlag:

1. Selecteer **Functiebeheer** > **+Toevoegen** om een functievlag toe te voegen.

    ![Lijst met functievlaggen](./media/azure-app-configuration-feature-flags.png)

1. Voer een unieke sleutelnaam in voor de functievlag. Je hebt deze naam nodig om naar de vlag in je code te verwijzen.

1. Geef defunctievlag destijds een beschrijving.

1. Stel de beginstatus van de functievlag in. Deze toestand is meestal *uitgeschakeld* of *aan.* De status *Aan* verandert in *Voorwaardelijk* als u een filter toevoegt aan de functievlag.

    ![Feature flag aanmaken](./media/azure-app-configuration-feature-flag-create.png)

1. Wanneer de status *is ingeschakeld,* selecteert u **het filter +Toevoegen** om eventuele aanvullende voorwaarden op te geven om in aanmerking te komen voor de status. Voer een ingebouwde of aangepaste filtertoets in en selecteer **+Parameter toevoegen** om een of meer parameters aan het filter te koppelen. Ingebouwde filters zijn:

    | Sleutel | JSON-parameters |
    |---|---|
    | Microsoft.Percentage | {"Waarde": 0-100 procent} |
    | Microsoft.TimeWindow | {"Start": UTC-tijd, "Einde": UTC-tijd} |

    ![Functievlagfilter](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Functievlagstatussen bijwerken

Ga als lid van het nieuwe bedrijf over de statuswaarde van een functievlag:

1. Selecteer **Functiebeheer**.

1. Rechts van een functievlag die u wilt wijzigen, selecteert u de ellips (**... )** en selecteert u **Bewerken**.

1. Stel een nieuwe status in voor de functievlag.

## <a name="access-feature-flags"></a>Access-functievlaggen

Functievlaggen die door de functiebeheer zijn gemaakt, worden opgeslagen en opgehaald als normale sleutelwaarden. Ze worden bewaard onder een speciaal `.appconfig.featureflag`naamruimtevoorvoegsel. Als u de onderliggende sleutelwaarden wilt weergeven, gebruikt u de Configuratieverkenner. Uw toepassing kan deze waarden ophalen met behulp van de configuratieproviders voor app-configuratie, SDK's, opdrachtregelextensies en REST-API's.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u functievlaggen en hun statussen beheren met behulp van app-configuratie. Zie het volgende artikel voor meer informatie over ondersteuning voor functiebeheer in app-configuratie en ASP.NET Core:

* [Functievlaggen gebruiken in een ASP.NET Core-app](./use-feature-flags-dotnet-core.md)
