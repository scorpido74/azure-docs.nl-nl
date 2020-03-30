---
title: Wat is er gebeurd met mijn ASP.NET 5 project (Visual Studio connected services)
description: Beschrijft wat er gebeurt nadat u verbinding maakt met een Azure-opslagaccount in een Visual Studio-ASP.NET 5-project met behulp van visual studio-verbonden services
services: storage
author: ghogen
manager: jillfra
ms.assetid: e7caa9fa-c780-45eb-a546-299fc1c68455
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: be99465a48aaf680834f313e03384a9f0c211502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72300014"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Wat is er gebeurd met mijn ASP.NET 5-project (Visual Studio Azure Storage connected services)?
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

Ook is het NuGet-pakket **Microsoft.Framework.Configuration.Json** toegevoegd.

## <a name="connection-string-for-azure-storage-added"></a>Verbindingstekenreeks voor Azure Storage toegevoegd
In het config.json-bestand van uw project is een element gemaakt met de verbindingstekenreeks en -sleutel van het geselecteerde opslagaccount.

Zie voor meer informatie [ASP.NET 5](https://www.asp.net/vnext).

