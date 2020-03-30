---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 034e966d259f1ca5f22eec5935013de32c883b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056575"
---
De projectsjabloon Azure Functions in Visual Studio maakt een project dat u publiceren naar een functie-app in Azure. U een functie-app gebruiken om functies te groeperen als een logische eenheid voor eenvoudiger beheer, implementatie, schalen en delen van resources.

1. Selecteer in Visual Studio in het menu **Bestand** de optie **Nieuw** > **project**.

1. Voer **in Een nieuw project maken** *functies* in het zoekvak in en kies vervolgens de sjabloon **Azure-functies.**

1. Voer in **Uw nieuwe project configureren**een **projectnaam** voor uw project in en selecteer **Vervolgens Maken**. De functie-appnaam moet geldig zijn als een C#-naamruimte. Gebruik dus geen onderstrepingstekens, afbreekstreepjes of andere niet-alfanumerieke tekens.

1. Gebruik voor het **nieuwe project - &lt;uw&gt; projectnaam-instellingen** de waarden in de volgende tabel:

    | Instelling      | Waarde  | Beschrijving                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Runtime van functies** | **Azure-functies <br />v2 (.NET-kern)** | Met deze waarde wordt een functieproject gemaakt dat gebruik maakt van de uitvoering van versie 2.x van Azure-functies, die .NET Core ondersteunt. Azure Functions 1.x ondersteunt .NET Framework. Zie [overzicht van azure functions runtime-versies](../articles/azure-functions/functions-versions.md)voor meer informatie.   |
    | **Functiesjabloon** | **HTTP-trigger** | Met deze waarde wordt een functie geactiveerd door een HTTP-aanvraag. |
    | **Opslagaccount**  | **Opslagemulator** | Omdat een Azure-functie een opslagaccount vereist, wordt er een toegewezen of gemaakt wanneer u uw project publiceert naar Azure. Een HTTP-trigger maakt geen gebruik van een tekenreeks voor azure-opslagaccountverbindingen. alle andere triggertypen vereisen een geldige azure storage-verbindingstekenreeks.  |
    | **Toegangsrechten** | **Anoniem** | De gemaakte functie kan door iedere client worden geactiveerd zonder een sleutel op te geven. Met deze autorisatie-instelling kunt u eenvoudig uw nieuwe functie testen. Zie [Autorisatiesleutels](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) en [HTTP- en webhookbindingen](../articles/azure-functions/functions-bindings-http-webhook.md)voor meer informatie over sleutels en autorisatie. |
    

    
    ![Projectinstellingen voor Azure Functions](./media/functions-vs-tools-create/functions-project-settings.png)

    Zorg ervoor dat u de **toegangsrechten** instelt op **Anoniem**. Als u het standaardfunctieniveau **kiest,** moet u de [functiesleutel](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) weergeven in aanvragen om toegang te krijgen tot het eindpunt van de functie.

1. Selecteer **OK** om het functieproject en de functie HTTP-geactiveerd te maken.
