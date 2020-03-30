---
title: Wat is er gebeurd met mijn cloudserviceproject?
description: Beschrijft wat er gebeurt in een cloudservicesproject nadat u verbinding hebt gemaakt met een Azure-opslagaccount met behulp van visual studio-verbonden services
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298760"
---
# <a name="what-happened-to-my-cloud-services-project-visual-studio-azure-storage-connected-service"></a>Wat is er gebeurd met mijn cloudserviceproject (aan Visual Studio Azure Storage verbonden service)?
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
Elementen zijn gemaakt met de verbindingstekenreeks en de sleutel van het geselecteerde opslagaccount. De volgende dossiers zijn gewijzigd:

* **ServiceDefinition.csdef**
* **ServiceConfiguration.Cloud.cscfg**
* **ServiceConfiguration.Local.cscfg**

