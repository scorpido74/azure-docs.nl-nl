---
title: Azure functions lokaal ontwikkelen en uitvoeren | Microsoft Docs
description: Meer informatie over het coderen en testen van Azure functions op uw lokale computer voordat u deze uitvoert op Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 7c2e727ecb080d1db212e8b45a2c48bac81a3949
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709317"
---
# <a name="code-and-test-azure-functions-locally"></a>Code-en test Azure Functions lokaal

U kunt Azure Functions in het [Azure-portal]ontwikkelen en testen, maar veel ontwikkel aars geven de voor keur aan een lokale ontwikkel ervaring. Met functies kunt u eenvoudig uw favoriete code-editor en ontwikkel hulpprogramma's gebruiken om functies op uw lokale computer te maken en te testen. Uw lokale functies kunnen verbinding maken met Live Azure-Services en u kunt fouten opsporen op uw lokale computer met behulp van de volledige functions-runtime.

## <a name="local-development-environments"></a>Lokale ontwikkel omgevingen

De manier waarop u functies op uw lokale computer ontwikkelt, is afhankelijk van uw [taal](supported-languages.md) en programma voorkeuren. De omgevingen in de volgende tabel ondersteunen lokale ontwikkeling:

|Omgeving                              |Languages         |Description|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [](functions-reference-node.md) [](functions-reference-python.md) [](functions-create-first-function-powershell.md) [(klassen bibliotheek), script (. CSX), Java C# ](functions-dotnet-class-library.md)script, Power shell, python [ C# ](functions-reference-csharp.md) | De [uitbrei ding van de Azure functions voor VS code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voegt functies ondersteuning toe aan VS code. Vereist de kern Hulpprogramma's. Ondersteunt de ontwikkeling van Linux, MacOS en Windows bij gebruik van versie 2. x van de kern Hulpprogramma's. Zie [uw eerste functie maken met Visual Studio code](functions-create-first-function-vs-code.md)voor meer informatie. |
| [Opdracht prompt of Terminal](functions-run-local.md) | [](functions-reference-node.md) [](functions-reference-python.md) [](functions-reference-powershell.md) [(klassen bibliotheek), script (. CSX), Java C# ](functions-dotnet-class-library.md)script, Power shell, python [ C# ](functions-reference-csharp.md) | [Azure functions core tools] biedt de kern runtime en sjablonen voor het maken van functies, waarmee lokale ontwikkeling mogelijk wordt. Versie 2. x ondersteunt de ontwikkeling van Linux, MacOS en Windows. Alle omgevingen zijn afhankelijk van de belangrijkste Hulpprogram Ma's voor de runtime van lokale functies. |
| [Visual Studio 2019](functions-develop-vs.md) | [C#(klassen bibliotheek)](functions-dotnet-class-library.md) | De Azure Functions-hulpprogram ma's zijn opgenomen in de **Azure Development** -werk belasting van [Visual Studio 2019](https://www.visualstudio.com/vs/) en latere versies. Hiermee kunt u functies in een klassen bibliotheek compileren en het dll-bestand publiceren naar Azure. Bevat de belangrijkste Hulpprogram Ma's voor lokale tests. Zie [Azure functions ontwikkelen met Visual Studio](functions-develop-vs.md)voor meer informatie. |
| [Maven](functions-create-first-java-maven.md) Sommige | [Java](functions-reference-java.md) | Kan worden geïntegreerd met kern Hulpprogramma's om de ontwikkeling van Java-functies mogelijk te maken. Versie 2. x ondersteunt de ontwikkeling van Linux, MacOS en Windows. Zie [uw eerste functie maken met Java en Maven](functions-create-first-java-maven.md)voor meer informatie. Biedt ook ondersteuning voor ontwikkeling met het idee van [eclips](functions-create-maven-eclipse.md) en [IntelliJ](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Met elk van deze lokale ontwikkel omgevingen kunt u functie-app-projecten maken en vooraf gedefinieerde functie sjablonen gebruiken om nieuwe functies te maken. Elk maakt gebruik van de kern Hulpprogramma's zodat u uw functies kunt testen en fout opsporing uitvoert op de echte functions-runtime op uw eigen computer, net zoals elke andere app. U kunt uw functie-app-project ook publiceren vanuit een van deze omgevingen naar Azure.  

## <a name="next-steps"></a>Volgende stappen

+ Zie [Azure functions ontwikkelen met Visual Studio](functions-develop-vs.md)voor meer C# informatie over de lokale ontwikkeling van gecompileerde functies met behulp van Visual Studio 2019.
+ Zie [Azure functions van VS code implementeren](/azure/javascript/tutorial-vscode-serverless-node-01)voor meer informatie over de lokale ontwikkeling van functies met behulp van VS code op een Mac-, Linux-of Windows-computer.
+ Zie [werken met Azure functions core tools](functions-run-local.md)voor meer informatie over het ontwikkelen van functies vanaf de opdracht prompt of Terminal.

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
