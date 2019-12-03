---
title: Inhoud vanuit een Cloud-map synchroniseren
description: Meer informatie over het implementeren van uw app naar Azure App Service via inhouds synchronisatie vanuit een Cloud-map, waaronder OneDrive of Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c55894bff9501d3ffb9aa843a9eaa240a213180e
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671738"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Inhoud vanuit een Cloud-map synchroniseren met Azure App Service
In dit artikel wordt beschreven hoe u uw inhoud synchroniseert met [Azure app service](https://go.microsoft.com/fwlink/?LinkId=529714) vanuit Dropbox en OneDrive. 

De implementatie van inhoud op aanvraag wordt mogelijk gemaakt door de App Service [kudu-implementatie-engine](https://github.com/projectkudu/kudu/wiki). U kunt werken met uw app-code en-inhoud in een aangewezen Cloud-map en vervolgens synchroniseren met App Service met één klik op een knop. Inhouds synchronisatie maakt gebruik van de kudu-buildserver. 

## <a name="enable-content-sync-deployment"></a>Implementatie van inhouds synchronisatie inschakelen

Als u inhouds synchronisatie wilt inschakelen, navigeert u naar de pagina App Service app in de [Azure Portal](https://portal.azure.com).

Klik in het linkermenu op **Deployment Center** > **OneDrive** of **Dropbox** > **autoriseren**. Volg de autorisatie prompts. 

![](media/app-service-deploy-content-sync/choose-source.png)

U hoeft slechts één keer per OneDrive of Dropbox te autoriseren. Als u al bent gemachtigd, klikt u op **door gaan**. U kunt het geautoriseerde OneDrive-of Dropbox-account wijzigen door te klikken op **account wijzigen**.

![](media/app-service-deploy-content-sync/continue.png)

Selecteer op de pagina **configureren** de map die u wilt synchroniseren. Deze map wordt gemaakt onder het volgende toegewezen inhoudsdistributiepad in OneDrive of Dropbox. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Wanneer u klaar bent, klikt u op **door gaan**.

Controleer de opties op de pagina **samen vatting** en klik op **volt ooien**.

## <a name="synchronize-content"></a>Inhoud synchroniseren

Als u inhoud in de map Cloud met App Service wilt synchroniseren, gaat u terug naar de pagina van het **implementatie centrum** en klikt u op **synchroniseren**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Vanwege onderliggende verschillen in de Api's, wordt **OneDrive voor bedrijven** op dit moment niet ondersteund. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Implementatie van inhouds synchronisatie uitschakelen

Als u inhouds synchronisatie wilt uitschakelen, gaat u naar de pagina App Service app in de [Azure Portal](https://portal.azure.com).

Klik in het linkermenu op **Deployment Center** > **verbinding verbreken**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Implementeren vanuit lokale Git-opslag plaats](deploy-local-git.md)
