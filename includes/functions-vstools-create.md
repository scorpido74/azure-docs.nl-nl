---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a27d44a7684b4ad3d39d7fba50ca52e08e932971
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769762"
---
De Azure Functions-projectsjabloon in Visual Studio maakt een project dat kan worden gepubliceerd in een functie-app in Azure. U kunt een functie-app gebruiken om functies te groeperen als een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen.

1. Selecteer in Visual Studio in het menu **File** de optie **New** > **project**.

1. Zoek in het dialoog venster **een nieuw project maken** naar `functions`, kies de **Azure functions** sjabloon en selecteer **volgende**.

1. Voer een naam in voor het project en selecteer **maken**. De functie-appnaam moet geldig zijn als een C#-naamruimte. Gebruik dus geen onderstrepingstekens, afbreekstreepjes of andere niet-alfanumerieke tekens.

1. Gebruik in **een nieuwe Azure functions-toepassing maken**de volgende opties:

    + **Azure Functions v2 (.NET core)**
    + **HTTP-trigger**
    + **Opslag account**: **opslag-emulator**
    + **Autorisatie niveau**: **anoniem** 

    | Optie      | Voorgestelde waarde  | Beschrijving                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Functions runtime** | **Azure Functions 2. x <br />(.NET core)** | Met deze instelling maakt u een functie project dat gebruikmaakt van versie 2. x runtime van Azure Functions, dat .NET core ondersteunt. Azure Functions 1.x ondersteunt .NET Framework. Zie [doel-Azure functions runtime-versie](../articles/azure-functions/functions-versions.md)voor meer informatie.   |
    | **Functie sjabloon** | **HTTP-trigger** | Met deze instelling maakt u een functie die wordt geactiveerd door een HTTP-aanvraag. |
    | **Opslagaccount**  | **Opslag emulator** | Een HTTP-trigger maakt geen gebruik van de Azure Storage-account verbinding. Voor alle andere triggertypen is er een geldige verbindingsreeks voor het opslagaccount nodig. Omdat voor functies een opslag account vereist is, wordt er een toegewezen of gemaakt wanneer u uw project naar Azure publiceert. |
    | **Verificatieniveau** | **Toegang** | De gemaakte functie kan door iedere client worden geactiveerd zonder een sleutel op te geven. Met deze autorisatie-instelling kunt u eenvoudig uw nieuwe functie testen. Zie [Autorisatiesleutels](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) in de [HTTP- en webhookbindingen](../articles/azure-functions/functions-bindings-http-webhook.md) voor meer informatie over sleutels en autorisatie. |
    
    > [!NOTE]
    > Zorg ervoor dat u het **autorisatie niveau** instelt op `Anonymous`. Als u het standaard niveau van `Function`kiest, moet u de [functie toets](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) in aanvragen voor toegang tot uw functie-eind punt presen teren.
    
4. Selecteer **maken** om het functie project en de http-geactiveerde functie te maken.
