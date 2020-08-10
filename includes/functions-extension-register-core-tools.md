---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031117"
---
Als u geen uitbreidings bundels kunt gebruiken, kunt u Azure Functions Core Tools lokaal gebruiken om de specifieke extensie pakketten te installeren die vereist zijn voor uw project. 

> [!NOTE]
> Als u de uitbrei dingen hand matig wilt installeren met behulp van kern Hulpprogramma's, moet u de .NET Core 2. x SDK hebben geïnstalleerd.

Wanneer u de extensies expliciet installeert, wordt een .NET-project bestand met de naam Extensions. csproj toegevoegd aan de hoofdmap van uw project. Dit bestand definieert de set NuGet-pakketten die vereist zijn voor uw functies. Hoewel u met de NuGet- [pakket verwijzingen](/nuget/consume-packages/package-references-in-project-files) in dit bestand kunt werken, kunt u met de belangrijkste hulpprogram ma's uitbrei dingen installeren zonder het bestand hand matig te hoeven bewerken.

Er zijn verschillende manieren om de vereiste extensies in uw lokale project te installeren met behulp van kern Hulpprogramma's. 

#### <a name="install-all-extensions"></a>Alle uitbrei dingen installeren 

Gebruik de volgende opdracht om automatisch alle extensie pakketten toe te voegen die worden gebruikt door de bindingen in uw lokale project:

```dotnetcli
func extensions install
```
Met de opdracht leest u de *function.jsin* het bestand om te zien welke pakketten u nodig hebt, installeert u ze en bouwt u het project extensies uit (extensies. csproj). Er worden nieuwe bindingen aan de huidige versie toegevoegd, maar bestaande bindingen worden niet bijgewerkt. Gebruik de `--force` optie om bestaande bindingen bij te werken naar de meest recente versie bij het installeren van nieuwe.

#### <a name="install-a-specific-extension"></a>Een specifieke extensie installeren

Gebruik de volgende opdracht voor het installeren van een specifiek uitbreidings pakket op een specifieke versie, in dit geval de opslag extensie:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```