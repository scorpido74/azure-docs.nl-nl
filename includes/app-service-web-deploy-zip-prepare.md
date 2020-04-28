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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75945170"
---
## <a name="create-a-project-zip-file"></a>Een ZIP-bestand van het project maken

>[!NOTE]
> Als u de bestanden in een ZIP-bestand hebt gedownload, pakt u eerst de bestanden uit. Als u bijvoorbeeld een ZIP-bestand van GitHub hebt gedownload, kunt u dat bestand niet implementeren. GitHub voegt extra geneste directory's toe, die niet samen werken met App Service. 
>

Navigeer in een lokaal Terminal venster naar de hoofdmap van uw app-project. 

Deze map moet het invoer bestand bevatten naar uw web-app, zoals _index. html_, _index. php_en _app. js_. Het kan ook pakket beheer bestanden bevatten zoals _project. json_, _Composer. json_, _package. json_, _Bower. json_en _Requirements. txt_.

Tenzij u app service de implementatie automatisering voor u wilt uitvoeren, voert u alle build-taken uit (bijvoorbeeld `npm` `bower` `gulp` `composer`,,, en `pip`) en zorgt u ervoor dat u alle bestanden hebt die u nodig hebt om de app uit te voeren. Deze stap is vereist als u [uw pakket rechtstreeks wilt uitvoeren](../articles/app-service/deploy-run-package.md).

Maak een ZIP-archief van alle bestanden in uw project. De volgende opdracht maakt gebruik van het standaardhulpprogramma in de terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

