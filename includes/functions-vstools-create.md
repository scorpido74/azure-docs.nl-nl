---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 164620bdcee7ac546468354f999dcb3ad96ecf4b
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84730990"
---
Met de sjabloon Azure Functions project in Visual Studio maakt u een project dat u kunt publiceren naar een functie-app in Azure. U kunt een functie-app gebruiken om functies te groeperen als een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen.

1. Selecteer in het Visual Studio-menu de optie **bestand**  >  **Nieuw**  >  **project**.

1. In **een nieuw project maken**voert u *functies* in het zoekvak in, kiest u de sjabloon **Azure functions** en selecteert u vervolgens **volgende**.

1. Voer in **uw nieuwe project configureren**een **project naam** in voor uw project en selecteer vervolgens **maken**. De functie-appnaam moet geldig zijn als een C#-naamruimte. Gebruik dus geen onderstrepingstekens, afbreekstreepjes of andere niet-alfanumerieke tekens.

1. Gebruik de waarden in de volgende tabel voor het **maken van een nieuwe Azure functions toepassings** instellingen:

    | Instelling      | Waarde  | Beschrijving                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Functions runtime** | **Azure Functions v3 <br /> (.net core)** | Met deze waarde wordt een functie project gemaakt dat gebruikmaakt van versie 3. x runtime van Azure Functions, dat .NET Core 3. x ondersteunt. Azure Functions 1.x ondersteunt .NET Framework. Zie [Azure functions runtime versies Overview](../articles/azure-functions/functions-versions.md)(Engelstalig) voor meer informatie.   |
    | **Functie sjabloon** | **HTTP-trigger** | Met deze waarde wordt een functie gemaakt die wordt geactiveerd door een HTTP-aanvraag. |
    | **Opslag account (AzureWebJobsStorage)**  | **Opslag emulator** | Omdat een Azure-functie een opslag account vereist, wordt er een toegewezen of gemaakt wanneer u uw project naar Azure publiceert. Een HTTP-trigger gebruikt geen Azure Storage-account connection string; voor alle andere trigger typen is een geldig Azure Storage account vereist connection string.  |
    | **Autorisatieniveau** | **Anoniem** | De gemaakte functie kan door iedere client worden geactiveerd zonder een sleutel op te geven. Met deze autorisatie-instelling kunt u eenvoudig uw nieuwe functie testen. Zie [autorisatie sleutels](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) en [bindingen voor http en webhook](../articles/azure-functions/functions-bindings-http-webhook.md)voor meer informatie over sleutels en autorisatie. |
    
    
    ![Azure Functions project instellingen](./media/functions-vs-tools-create/functions-project-settings.png)

    Zorg ervoor dat u het **autorisatie niveau** instelt op **anoniem**. Als u het standaard niveau van de **functie**kiest, moet u de [functie sleutel](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) in aanvragen voor toegang tot uw functie-eind punt presen teren.

1. Selecteer **maken** om het functie project en de http-trigger functie te maken.
