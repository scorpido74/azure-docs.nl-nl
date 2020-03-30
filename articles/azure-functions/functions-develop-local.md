---
title: Azure-functies lokaal ontwikkelen en uitvoeren
description: Lees hoe u Azure-functies op uw lokale computer codeen en testen voordat u deze uitvoert op Azure-functies.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74230636"
---
# <a name="code-and-test-azure-functions-locally"></a>Azure-functies lokaal programmeren en testen

Hoewel u Azure-functies ontwikkelen en testen in de [Azure-portal,]geven veel ontwikkelaars de voorkeur aan een lokale ontwikkelingservaring. Functies maakt het gemakkelijk om uw favoriete codeeditor en ontwikkelingstools te gebruiken om functies op uw lokale computer te maken en te testen. Uw lokale functies kunnen verbinding maken met live Azure-services en u ze debuggen op uw lokale computer met behulp van de volledige runtime van functies.

## <a name="local-development-environments"></a>Lokale ontwikkelingsomgevingen

De manier waarop u functies ontwikkelt op uw lokale computer is afhankelijk van uw [taal-](supported-languages.md) en toolingvoorkeuren. De omgevingen in de volgende tabel ondersteunen lokale ontwikkeling:

|Omgeving                              |Talen         |Beschrijving|
|-----------------------------------------|------------|---|
|[Visual Studio-code](functions-develop-vs-code.md)| [C# (klassenbibliotheek),](functions-dotnet-class-library.md) [C#-script (.csx),](functions-reference-csharp.md) [JavaScript](functions-reference-node.md), [PowerShell](functions-create-first-function-powershell.md), [Python](functions-reference-python.md) | De [Azure Functions-extensie voor VS-code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voegt ondersteuning voor functies toe aan VS-code. Vereist de Core Tools. Ondersteunt ontwikkeling op Linux, MacOS en Windows bij het gebruik van versie 2.x van de Core Tools. Zie [Uw eerste functie maken met Visual Studio Code](functions-create-first-function-vs-code.md)voor meer informatie. |
| [Opdrachtprompt of terminal](functions-run-local.md) | [C# (klassenbibliotheek),](functions-dotnet-class-library.md) [C#-script (.csx),](functions-reference-csharp.md) [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Azure Functions Core Tools] biedt de kernruntime en sjablonen voor het maken van functies, die lokale ontwikkeling mogelijk maken. Versie 2.x ondersteunt ontwikkeling op Linux, MacOS en Windows. Alle omgevingen vertrouwen op Core Tools voor de lokale functies runtime. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (klassebibliotheek)](functions-dotnet-class-library.md) | De azure-functies zijn opgenomen in de **Azure-ontwikkelingsworkload** van [Visual Studio 2019](https://www.visualstudio.com/vs/) en latere versies. Hiermee u functies in een klassenbibliotheek compileren en de .dll naar Azure publiceren. Bevat de core tools voor lokale tests. Zie [Azure-functies ontwikkelen met Visual Studio](functions-develop-vs.md)voor meer informatie. |
| [Maven](functions-create-first-java-maven.md) (diverse) | [Java](functions-reference-java.md) | Integreert met Core Tools om de ontwikkeling van Java-functies mogelijk te maken. Versie 2.x ondersteunt ontwikkeling op Linux, MacOS en Windows. Zie [Uw eerste functie maken met Java en Maven](functions-create-first-java-maven.md)voor meer informatie. Ondersteunt ook ontwikkeling [met](functions-create-maven-eclipse.md) Eclipse en [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Met elk van deze lokale ontwikkelomgevingen u functie-app-projecten maken en vooraf gedefinieerde functiessjablonen gebruiken om nieuwe functies te maken. Elk maakt gebruik van de Core Tools, zodat u testen en debuggen uw functies tegen de echte functies runtime op uw eigen machine net als je zou elke andere app. U uw functie-app-project ook publiceren vanuit een van deze omgevingen naar Azure.  

## <a name="next-steps"></a>Volgende stappen

+ Zie [Azure-functies ontwikkelen met Visual Studio](functions-develop-vs.md)voor meer informatie over lokale ontwikkeling van gecompileerde C#-functies met Visual Studio 2019.
+ Zie [Azure-functies implementeren van VS-code](/azure/javascript/tutorial-vscode-serverless-node-01)voor meer informatie over lokale ontwikkeling van functies met VS-code op een Mac-, Linux- of Windows-computer.
+ Zie [Werken met Azure Functions Core Tools](functions-run-local.md)voor meer informatie over het ontwikkelen van functies via de opdrachtprompt of terminal.

<!-- LINKS -->

[Core-hulpprogramma's voor Azure-functies]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
