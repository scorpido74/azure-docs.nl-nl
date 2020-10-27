---
author: baanders
description: bestand opnemen om lokale verificatie in te stellen voor DefaultAzureCredential in Azure Digital Twins-voorbeelden - zonder intro
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8bf0590b867d37a22706f679bfbeee2140935637
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494637"
---
Met `DefaultAzureCredential` zoekt het voorbeeld naar referenties in uw lokale omgeving, zoals een Azure-login in een lokale [Azure-CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) of in Visual Studio/Visual Studio Code. Dit betekent dat u zich **lokaal moet aanmelden bij Azure** via een van deze mechanismen om referenties in te stellen voor het voorbeeld.

Als u gebruikmaakt van Visual Studio of Visual Studio Code om het codevoorbeeld uit te voeren, moet u ervoor zorgen dat u bent aangemeld bij die editor met dezelfde Azure-referenties die u wilt gebruiken om uw Azure Digital Twins-instantie te openen.

Anders kunt u [de **Azure-CLI** lokaal installeren](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), de opdrachtprompt uitvoeren op uw machine en de opdracht `az login` uitvoeren om u aan te melden bij uw Azure-account. Wanneer u hierna uw codevoorbeeld moet uitvoeren, zou u automatisch aangemeld moeten worden.