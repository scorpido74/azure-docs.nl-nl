---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "67836804"
---
## <a name="prepare-your-repository"></a>Uw opslag plaats voorbereiden

Zorg ervoor dat de hoofdmap van uw opslag plaats de juiste bestanden bevat in het project om automatische builds op te halen van Azure App Service kudu-buildserver.

| Runtime | Hoofdmap bestanden |
|-|-|
| ASP.NET (alleen Windows) | _*. SLN_, _*. csproj_of _default. aspx_ |
| ASP.NET Core | _*. SLN_ of _*. csproj_ |
| PHP | _index. php_ |
| Ruby (alleen Linux) | _Gemfile_ |
| Node.js | _server. js_, _app. js_of _package. json_ met een begin script |
| Python | . py, _Requirements. txt_of _runtime. txt_ _ \*_ |
| HTML | _default. htm_, _default. html_, _default. asp_, _index. htm_, _index. html_of _iisstart. htm_ |
| Webtaken | _\<job_name>/run. extensie \<>_ onder _app\_-gegevens/-taken/doorlopend_ voor doorlopende webjobs, of _app\_-gegevens/-taken/geactiveerd_ voor getriggerde webjobs. Zie [kudu](https://github.com/projectkudu/kudu/wiki/WebJobs)voor meer informatie. |
| Functions | Zie [continue implementatie voor Azure functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Als u uw implementatie wilt aanpassen, neemt u een *. Deployment* -bestand op in de hoofdmap van de opslag plaats. Zie [implementaties](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) en [aangepast implementatie script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)aanpassen voor meer informatie.

> [!NOTE]
> Als u in Visual Studio ontwikkelt, kunt [u Visual Studio een opslag plaats laten maken](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Het project kan direct worden geïmplementeerd met behulp van Git.
>

