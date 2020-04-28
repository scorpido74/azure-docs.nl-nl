---
title: Wat is er gebeurd met mijn Webtaak-project (Visual Studio Azure Storage)?
description: Beschrijft wat er is gebeurd in een Azure-webproject nadat verbinding is gemaakt met een opslag account met behulp van Visual Studio Connected Services
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "72298727"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Wat is er gebeurd met mijn Webtaak-project (Visual Studio Azure Storage Connected service)?
## <a name="references-added"></a>Toegevoegde verwijzingen
Het NuGet-pakket van Azure Storage is toegevoegd aan of bijgewerkt in uw Visual Studio-project.  
Dit pakket voegt de volgende .NET-verwijzingen toe:

* **Micro soft. data. EDM**
* **Micro soft. data. OData**
* **Micro soft. data. Services. client**
* **Micro soft. WindowsAzure. ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System. data**
* **Systeem. ruimtelijk**

## <a name="connection-string-for-azure-storage-added"></a>Verbindings reeks voor toegevoegde Azure Storage
In het bestand app. config van uw project zijn de **AzureWebJobsStorage** -en **AzureWebJobsDashboard** -vermeldingen bijgewerkt met de Connection String en sleutel van het geselecteerde opslag account.

Zie [Azure WebJobs documentation resources](https://go.microsoft.com/fwlink/?linkid=390226)(Engelstalig) voor meer informatie.

