---
title: Inhoud vanuit een Cloud-map synchroniseren-Azure App Service
description: Meer informatie over het implementeren van uw app naar Azure App Service via de inhouds synchronisatie vanuit een map in de Cloud.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 033a9adfd81dc52858ab58e3170e69af6f56c323
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070634"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Inhoud vanuit een Cloud-map synchroniseren met Azure App Service
In dit artikel wordt beschreven hoe u uw inhoud synchroniseert met [Azure app service](https://go.microsoft.com/fwlink/?LinkId=529714) vanuit Dropbox en OneDrive. 

De implementatie van inhoud op aanvraag wordt mogelijk gemaakt door de App Service [kudu-implementatie-engine](https://github.com/projectkudu/kudu/wiki). U kunt werken met uw app-code en-inhoud in een aangewezen Cloud-map en vervolgens synchroniseren met App Service met één klik op een knop. Inhouds synchronisatie maakt gebruik van de kudu-buildserver. 

## <a name="enable-content-sync-deployment"></a>Implementatie van inhouds synchronisatie inschakelen

Als u inhouds synchronisatie wilt inschakelen, navigeert u naar de pagina App Service app in de [Azure Portal](https://portal.azure.com).

Klik in het menu links op **Implementatiecentrum** > **OneDrive** of **Dropbox** > **Autoriseren**. Volg de autorisatie prompts. 

![](media/app-service-deploy-content-sync/choose-source.png)

U hoeft slechts één keer per OneDrive of Dropbox te autoriseren. Als u al bent gemachtigd, klikt u op **door gaan**. U kunt het geautoriseerde OneDrive-of Dropbox-account wijzigen door te klikken op **account wijzigen**.

![](media/app-service-deploy-content-sync/continue.png)

Selecteer op de pagina **configureren** de map die u wilt synchroniseren. Deze map wordt gemaakt onder het volgende toegewezen inhoudsdistributiepad in OneDrive of Dropbox. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**:`Apps\Azure`

Wanneer u klaar bent, klikt u op **Doorgaan**.

In de **Samenvatting** pagina, controleer uw opties en klik op **Voltooien**.

## <a name="synchronize-content"></a>Inhoud synchroniseren

Als u inhoud in de map Cloud met App Service wilt synchroniseren, gaat u terug naar de pagina van het **implementatie centrum** en klikt u op **synchroniseren**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Vanwege onderliggende verschillen in de API's wordt **OneDrive voor bedrijven** niet ondersteund op dit moment. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Implementatie van inhouds synchronisatie uitschakelen

Als u inhouds synchronisatie wilt uitschakelen, gaat u naar de pagina App Service app in de [Azure Portal](https://portal.azure.com).

Klik in het linkermenu op het verbreken van het **implementatie centrum** > .

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Implementeren vanuit lokale Git-opslag plaats](deploy-local-git.md)
