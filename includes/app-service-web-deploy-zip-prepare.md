---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6b5aa4f409b8c2f5a9125ab01e8587f4ac9c4ee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945170"
---
## <a name="create-a-project-zip-file"></a>Een ZIP-bestand van het project maken

>[!NOTE]
> Als u de bestanden in een ZIP-bestand hebt gedownload, haalt u eerst de bestanden uit. Als u bijvoorbeeld een ZIP-bestand van GitHub hebt gedownload, u dat bestand niet zo implementeren als het is. GitHub voegt extra geneste mappen toe, die niet werken met App Service. 
>

Navigeer in een lokaal terminalvenster naar de hoofdmap van uw app-project. 

Deze map moet het invoerbestand voor uw web-app bevatten, zoals _index.html,_ _index.php_en _app.js_. Het kan ook package management bestanden zoals _project.json_, _composer.json_, _package.json_, _bower.json_, en _requirements.txt_.

Tenzij u wilt dat App Service implementatieautomatisering voor u uitvoert, `npm` `bower`voert `gulp` `composer`u `pip`alle buildtaken uit (bijvoorbeeld , , , en ) en controleert u of u over alle bestanden beschikt die u nodig hebt om de app uit te voeren. Deze stap is vereist als u uw pakket rechtstreeks wilt [uitvoeren.](../articles/app-service/deploy-run-package.md)

Maak een ZIP-archief van alle bestanden in uw project. De volgende opdracht maakt gebruik van het standaardhulpprogramma in de terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

