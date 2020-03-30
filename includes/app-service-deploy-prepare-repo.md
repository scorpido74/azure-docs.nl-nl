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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836804"
---
## <a name="prepare-your-repository"></a>Uw opslagplaats voorbereiden

Als u automatische builds wilt krijgen van de Azure App Service Kudu-buildserver, controleert u of uw bronmap de juiste bestanden in uw project heeft.

| Runtime | Hoofdmapbestanden |
|-|-|
| ASP.NET (alleen Windows) | _*.sln_, _*.csproj_of _default.aspx_ |
| ASP.NET Core | _*.sln_ of _*.csproj_ |
| PHP | _Index.php_ |
| Ruby (alleen Linux) | _Gemfile Gemfile_ |
| Node.js | _server.js_, _app.js_of _package.json_ met een startscript |
| Python | .py , _requirements.txt_, of _runtime.txt_ _ \*_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_of _iisstart.htm_ |
| Webtaken | _\<job_name>/run. extensie \<>_ onder _\_App-gegevens/-taken/continu_ voor continue WebJobs of _App-gegevens/taken/geactiveerd\__ voor geactiveerde WebJobs. Zie [Kudu WebJobs-documentatie](https://github.com/projectkudu/kudu/wiki/WebJobs)voor meer informatie. |
| Functions | Zie [Continue implementatie voor Azure-functies](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Als u uw implementatie wilt aanpassen, neemt u een *.deployment-bestand* op in de hoofdmap van de opslagplaats. Zie [Implementaties en](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) Aangepast [implementatiescript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)aanpassen voor meer informatie.

> [!NOTE]
> Als u zich ontwikkelt in Visual Studio, laat [Visual Studio dan een opslagplaats voor u maken.](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio) Het project is direct klaar om te worden geïmplementeerd met behulp van Git.
>

