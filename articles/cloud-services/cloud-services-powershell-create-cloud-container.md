---
title: Een cloudservicecontainer maken met PowerShell | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u een cloudservicecontainer maakt met PowerShell. De container bevat web- en werknemersrollen.
services: cloud-services
documentationcenter: .net
author: cawaMS
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: ef9d3c7c479df9c71a855f0a243b5b9d0da947d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359503"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Een opdracht Azure PowerShell gebruiken om een lege cloudservicecontainer te maken

In dit artikel wordt uitgelegd hoe u snel een Cloud Services-container maakt met Azure PowerShell-cmdlets. Volg de onderstaande stappen:

1. Installeer de Microsoft Azure PowerShell-cmdlet op de pagina [Azure PowerShell-downloads.](https://aka.ms/webpi-azps)
2. Open de opdrachtprompt powershell.
3. Gebruik de [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) om u aan te melden.

   > [!NOTE]
   > Raadpleeg Azure [PowerShell voor](/powershell/azure/overview)meer informatie over het installeren van de Azure PowerShell-cmdlet en het verbinden met uw Azure-abonnement.
   >
   >
4. Gebruik de cmdlet **Nieuw-AzureService** om een lege Azure-cloudservicecontainer te maken.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Volg dit voorbeeld om de cmdlet aan te roepen:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Voer voor meer informatie over het maken van de Azure-cloudservice het als:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Volgende stappen

* Als u de implementatie van de cloudservice wilt beheren, raadpleegt u de opdrachten [Get-AzureService,](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0) [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0)en [Set-AzureService.](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) U ook verwijzen naar [Hoe u cloudservices configureert](cloud-services-how-to-configure-portal.md) voor meer informatie.
* Als u uw cloudserviceproject naar Azure wilt publiceren, raadpleegt u het voorbeeld van de **PublishCloudService.ps1-code** van [de repository van gearchiveerde cloudservices.](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery)