---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a84f0a92703d1b626710cfc4dcfa2820bc58bda6
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673131"
---
## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ De [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) versie 2.7.1846 of een latere 2.x versie.
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 en 3.7 vereisen [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) versie 2.7.1846 of een latere 2.x-versie. Python 3.8 vereist [versie 3.x](../articles/azure-functions/functions-run-local.md#v2) van de Core Tools.
::: zone-end

+ De [Azure CLI-versie](/cli/azure/install-azure-cli) 2.0.76 of hoger. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS versies (8.11.1 en 10.14.1 aanbevolen).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64-bits),](https://www.python.org/downloads/release/python-382/) [Python 3.7 (64-bits),](https://www.python.org/downloads/release/python-375/) [Python 3.6 (64-bits)](https://www.python.org/downloads/release/python-368/), die worden ondersteund door Azure-functies. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ De [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ De [Java Developer Kit](https://aka.ms/azure-jdks), versie 8.

+ [Apache Maven](https://maven.apache.org), versie 3.0 of hoger.

> [!IMPORTANT]
> De omgevingsvariabele JAVA_HOME moet zijn ingesteld op de installatielocatie van de JDK om deze quickstart te kunnen voltooien.
::: zone-end