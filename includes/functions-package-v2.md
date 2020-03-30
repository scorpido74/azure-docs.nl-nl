---
title: bestand opnemen
description: bestand opnemen
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205716"
---
Voeg ondersteuning toe aan de gewenste ontwikkelomgeving met behulp van de volgende methoden.

| Ontwikkelomgeving  | Toepassingstype      | Ondersteuning toevoegen |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C# klassenbibliotheek      | [Het NuGet-pakket installeren](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Gebaseerd op [kerntools](../articles/azure-functions/functions-run-local.md) | [De extensiebundel registreren](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Het installeren van de [Azure Tools-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) wordt aanbevolen. |
| Elke andere editor/IDE     | Gebaseerd op [kerntools](../articles/azure-functions/functions-run-local.md) | [De extensiebundel registreren](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure-portal             | Alleen online in portal | Installeert bij het toevoegen van een binding<br /><br /> Zie [Uw extensies bijwerken](../articles/azure-functions/install-update-binding-extensions-manual.md) om bestaande bindende extensies bij te werken zonder uw functie-app opnieuw te hoeven publiceren. |
