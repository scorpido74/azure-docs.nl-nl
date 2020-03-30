---
title: Wat is er gebeurd met mijn ASP.NET-project?
description: Beschrijft wat er gebeurt nadat azure-opslag is toegevoegd aan een ASP.NET project met behulp van visual studio-verbonden services
services: storage
author: ghogen
manager: jillfra
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4374955d6d51c1ae44c211aaa93d0b5e8930fe5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300036"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Wat is er gebeurd met mijn ASP.NET project (Visual Studio Azure Storage connected service)?
## <a name="references-added"></a>Referenties toegevoegd
Het Azure Storage NuGet-pakket is toegevoegd aan uw Visual Studio-project.  
In dit pakket worden de volgende .NET-verwijzingen toegevoegd:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.data**
* **System.Spatial System.Spatial System.Spatial System.**

## <a name="connection-string-for-azure-storage-added"></a>Verbindingstekenreeks voor Azure Storage toegevoegd
In het web.config-bestand van uw project is een element gemaakt met de verbindingstekenreeks en -sleutel van het geselecteerde opslagaccount.

Zie [ASP.NET](https://www.asp.net)voor meer informatie.

