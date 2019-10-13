---
title: Wat is er gebeurd met mijn ASP.NET 5-project (Visual Studio Connected Services)
description: Beschrijft wat er gebeurt nadat u verbinding hebt gemaakt met een Azure-opslag account in een Visual Studio ASP.NET 5-project met Visual Studio Connected Services
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
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300014"
---
# <a name="what-happened-to-my-aspnet-5-project-visual-studio-azure-storage-connected-services"></a>Wat is er gebeurd met mijn ASP.NET 5-project (Visual Studio Azure Storage Connected Services)?
## <a name="references-added"></a>Toegevoegde verwijzingen
Het NuGet-pakket van Azure Storage is toegevoegd aan uw Visual Studio-project.  
Dit pakket voegt de volgende .NET-verwijzingen toe:

* **Micro soft. data. EDM**
* **Micro soft. data. OData**
* **Micro soft. data. Services. client**
* **Micro soft. WindowsAzure. Configuration**
* **Micro soft. WindowsAzure. Storage**
* **Newton soft. json**
* **System. data**
* **Systeem. ruimtelijk**

Daarnaast is het NuGet-pakket **micro soft. Framework. Configuration. json** toegevoegd.

## <a name="connection-string-for-azure-storage-added"></a>Verbindings reeks voor toegevoegde Azure Storage
In het bestand config. json van uw project is een element gemaakt met de connection string en sleutel van het geselecteerde opslag account.

Zie [ASP.net 5](https://www.asp.net/vnext)voor meer informatie.

