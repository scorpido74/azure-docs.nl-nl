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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77205716"
---
Voeg ondersteuning toe in uw voorkeursontwikkelomgeving met de volgende methoden.

| Ontwikkelomgeving  | Toepassingstype      | Ondersteuning toevoegen |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C#-klassebibliotheek      | [Installeer het NuGet-pakket van](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Op basis van [core-hulpprogramma's](../articles/azure-functions/functions-run-local.md) | [De uitbreidingsbundel registreren](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Het wordt aanbevolen om de [Azure Tools-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) te installeren. |
| Een andere editor/IDE     | Op basis van [core-hulpprogramma's](../articles/azure-functions/functions-run-local.md) | [De uitbreidingsbundel registreren](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure Portal             | Alleen online in portal | Wordt geïnstalleerd wanneer u een binding toevoegt<br /><br /> Raadpleeg [Uw extensies bijwerken](../articles/azure-functions/install-update-binding-extensions-manual.md) om bestaande bindingsextenties uit te breiden zonder dat u uw functie-app opnieuw moet publiceren. |
