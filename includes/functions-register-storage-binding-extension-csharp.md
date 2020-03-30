---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201946"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Binding-extensies registreren

Met uitzondering van HTTP- en timertriggers worden bindingen geïmplementeerd als uitbreidingspakketten. Voer de volgende [opdracht voor dotnetadd-pakketten uit](/dotnet/core/tools/dotnet-add-package) in het terminalvenster om het uitbreidingspakket Opslag aan uw project toe te voegen.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

U nu de binding van de opslaguitvoer toevoegen aan uw project.  
::: zone-end  