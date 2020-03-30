---
title: Inhoud synchroniseren vanuit een cloudmap
description: Meer informatie over het implementeren van uw app naar Azure App Service via inhoudssynchronisatie vanuit een cloudmap, waaronder OneDrive of Dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9f18eef607a5f655aecc0b6ebe1c85a239b34192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482969"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Inhoud vanuit een cloudmap synchroniseren met Azure App Service
In dit artikel ziet u hoe u uw inhoud synchroniseert met [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) vanuit Dropbox en OneDrive. 

De on-demand content sync deployment wordt aangedreven door de App Service [Kudu implementatie engine.](https://github.com/projectkudu/kudu/wiki) U met uw app-code en -inhoud werken in een aangewezen cloudmap en vervolgens synchroniseren met App Service met een klik op een knop. Contentsynchronisatie maakt gebruik van de Kudu-buildserver. 

## <a name="enable-content-sync-deployment"></a>Implementatie van inhoudssynchronisatie inschakelen

Als u inhoudssynchronisatie wilt inschakelen, navigeert u naar de app-app-pagina van uw App Service in de [Azure-portal.](https://portal.azure.com)

Klik in het linkermenu op **Implementatiecentrum** > **OneDrive** of **Dropbox** > **Autoriseren**. Volg de autorisatieprompts. 

![](media/app-service-deploy-content-sync/choose-source.png)

Je hoeft slechts één keer een autote autoriseren met OneDrive of Dropbox. Als u al geautoriseerd bent, klikt u op **Doorgaan**. Je het geautoriseerde OneDrive- of Dropbox-account wijzigen door op **Account wijzigen**te klikken.

![](media/app-service-deploy-content-sync/continue.png)

Selecteer **op** de pagina Configureren de map die u wilt synchroniseren. Deze map wordt gemaakt onder het volgende aangewezen inhoudspad in OneDrive of Dropbox. 
   
* **OneDrive**:`Apps\Azure Web Apps`
* **Dropbox**:`Apps\Azure`

Klik op Doorgaan als u klaar **bent.**

Controleer **op** de pagina Overzicht uw opties en klik op **Voltooien**.

## <a name="synchronize-content"></a>Inhoud synchroniseren

Als u inhoud in uw cloudmap wilt synchroniseren met App Service, gaat u terug naar de pagina **Implementatiecentrum** en klikt u op **Synchroniseren**.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Vanwege de onderliggende verschillen in de API's wordt **OneDrive voor Bedrijven** op dit moment niet ondersteund. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Implementatie van inhoudssynchronisatie uitschakelen

Als u inhoudssynchronisatie wilt uitschakelen, navigeert u naar de app-app-pagina van uw App Service in de [Azure-portal.](https://portal.azure.com)

Klik in het linkermenu op **Implementatiecentrum** > **Verbreken**.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Implementeren vanuit lokale Git-repo](deploy-local-git.md)
