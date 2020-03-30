---
title: Wat is er gebeurd met mijn WebJob-project (Visual Studio Azure Storage)?
description: Beschrijft wat er is gebeurd in een Azure WebJob-project nadat u verbinding hebt gemaakt met een opslagaccount met behulp van visual studio-verbonden services
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 17861b7c25dfaf9bc9399e5261cdf2a5b43caf21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298727"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Wat is er gebeurd met mijn WebJob-project (Visual Studio Azure Storage connected service)?
## <a name="references-added"></a>Referenties toegevoegd
Het Azure Storage NuGet-pakket is toegevoegd aan of bijgewerkt in uw Visual Studio-project.  
In dit pakket worden de volgende .NET-verwijzingen toegevoegd:

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.data**
* **System.Spatial System.Spatial System.Spatial System.**

## <a name="connection-string-for-azure-storage-added"></a>Verbindingstekenreeks voor Azure Storage toegevoegd
In het app.config-bestand van uw project zijn de **azurewebjobsstorage-** en **AzureWebJobsDashboard-vermeldingen** bijgewerkt met de verbindingstekenreeks en -sleutel van het geselecteerde opslagaccount.

Zie [Documentatiebronnen voor Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226)voor meer informatie .

