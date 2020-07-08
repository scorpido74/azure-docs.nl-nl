---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 9e9f9c71701ceb1c76bc162f22e166b4565e731b
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86062697"
---
## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ De [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) versie 2.7.1846 of een latere 2.x-versie.
::: zone-end  
::: zone pivot="programming-language-python"
+ De Azure Functions Core Tools-versie die overeenkomt met de geïnstalleerde Python-versie:

   | Python-versie | Core Tools-versie |
   | -------------- | ------------------ |
   | Python 3.8     | [versie 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [Versie 2.7.1846 of een latere versie](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) versie 2.4 of hoger. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS-versies (8.11.1 en 10.14.1 aanbevolen).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64-bits)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64-bits)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-bit)](https://www.python.org/downloads/release/python-368/), die door Azure Functions worden ondersteund. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ De [.NET Core-SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks), versie 8

+ [Apache Maven](https://maven.apache.org), versie 3.0 of hoger.

> [!IMPORTANT]
> De omgevingsvariabele JAVA_HOME moet zijn ingesteld op de installatielocatie van de JDK om deze quickstart te kunnen voltooien.
::: zone-end
