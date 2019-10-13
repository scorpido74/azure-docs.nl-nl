---
title: Wat is er gebeurd met mijn Cloud service project?
description: Beschrijft wat er gebeurt in een Cloud Services-project nadat u verbinding hebt gemaakt met een Azure-opslag account met behulp van Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: ca0ea68d-f417-4ce8-9413-40d76f69cdea
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ac5a635b687e51ddd34899717497548296c2fc23
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298760"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Wat is er gebeurd met mijn Cloud Services-project (Visual Studio Azure Storage Connected service)?
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

## <a name="connection-string-for-azure-storage-added"></a>Verbindings reeks voor toegevoegde Azure Storage
Elementen zijn gemaakt met de connection string en sleutel van het geselecteerde opslag account. Er zijn wijzigingen aangebracht in de volgende bestanden:

* **ServiceDefinition. csdef**
* **ServiceConfiguration. Cloud. cscfg**
* **ServiceConfiguration. local. cscfg**

