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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "72298760"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Wat is er gebeurd met mijn cloudserviceproject (aan Visual Studio Azure Storage verbonden service)?
## <a name="references-added"></a>Toegevoegde verwijzingen
Het NuGet-pakket van Azure Storage is toegevoegd aan uw Visual Studio-project.  
Dit pakket voegt de volgende .NET-verwijzingen toe:

* **Micro soft. data. EDM**
* **Micro soft. data. OData**
* **Micro soft. data. Services. client**
* **Micro soft. WindowsAzure. Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System. data**
* **Systeem. ruimtelijk**

## <a name="connection-string-for-azure-storage-added"></a>Verbindings reeks voor toegevoegde Azure Storage
Elementen zijn gemaakt met de connection string en sleutel van het geselecteerde opslag account. Er zijn wijzigingen aangebracht in de volgende bestanden:

* **ServiceDefinition. csdef**
* **ServiceConfiguration. Cloud. cscfg**
* **ServiceConfiguration. local. cscfg**

