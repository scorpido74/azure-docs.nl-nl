---
title: Uw Azure-functies uitvoeren vanuit een pakket
description: Laat de runtime van Azure Functions uw functies uitvoeren door een implementatiepakketbestand te monteren dat projectbestanden van uw functie-app bevat.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: d40896d6a4659945dbeda9ca965366f0b2ca4bd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365268"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Uw Azure-functies uitvoeren vanuit een pakketbestand

In Azure u uw functies rechtstreeks uitvoeren vanuit een implementatiepakketbestand in uw functie-app. De andere optie is het `d:\home\site\wwwroot` implementeren van uw bestanden in de map van uw functie-app.

In dit artikel worden de voordelen beschreven van het uitvoeren van uw functies vanuit een pakket. Het laat ook zien hoe u deze functionaliteit in uw functie-app inschakelen.

> [!IMPORTANT]
> Wanneer u uw functies implementeert in een Linux-functie-app in een [Premium-abonnement,](functions-scale.md#premium-plan)moet u altijd vanuit het pakketbestand worden uitgevoerd en [uw app publiceren met behulp van de Azure Functions Core Tools.](functions-run-local.md#project-file-deployment)

## <a name="benefits-of-running-from-a-package-file"></a>Voordelen van het uitvoeren van een pakketbestand
  
Er zijn verschillende voordelen aan het uitvoeren van een pakketbestand:

+ Vermindert het risico op problemen met het vergrendelen van bestandskopieën.
+ Kan worden geïmplementeerd in een productie-app (met opnieuw opstarten).
+ U bepaalde bestanden zijn die in uw app worden uitgevoerd.
+ Verbetert de prestaties van [Azure Resource Manager-implementaties.](functions-infrastructure-as-code.md)
+ Kan koude starttijden verminderen, met name voor JavaScript-functies met grote npm-pakketbomen.

Zie voor meer informatie [deze aankondiging.](https://github.com/Azure/app-service-announcements/issues/84)

## <a name="enabling-functions-to-run-from-a-package"></a>Functies inschakelen om vanuit een pakket uit te voeren

Als u wilt dat uw functie-app vanuit `WEBSITE_RUN_FROM_PACKAGE` een pakket wordt uitgevoerd, voegt u alleen een instelling toe aan de instellingen van uw functie-app. De `WEBSITE_RUN_FROM_PACKAGE` instelling kan een van de volgende waarden hebben:

| Waarde  | Beschrijving  |
|---------|---------|
| **`1`**  | Aanbevolen voor functie-apps die op Windows worden uitgevoerd. Uitvoeren vanuit een pakketbestand in de `d:\home\data\SitePackages` map van uw functie-app. Als deze optie niet [wordt geïmplementeerd met zip-implementatie,](#integration-with-zip-deployment) `packagename.txt`moet de map ook een bestand hebben met de naam . Dit bestand bevat alleen de naam van het pakketbestand in de map, zonder witruimte. |
|**`<URL>`**  | Locatie van een specifiek pakketbestand dat u wilt uitvoeren. Wanneer u Blob-opslag gebruikt, moet u een privécontainer met een [SAS (Shared Access Signature)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) gebruiken om de runtime van functies toegang te geven tot het pakket. U de [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) gebruiken om pakketbestanden te uploaden naar uw Blob-opslagaccount. Wanneer u een URL opgeeft, moet u [ook triggers synchroniseren](functions-deployment-technologies.md#trigger-syncing) nadat u een bijgewerkt pakket hebt gepubliceerd. |

> [!CAUTION]
> Wanneer u een functie-app uitvoert op Windows, levert de externe URL-optie slechtere prestaties bij het starten van koude. Wanneer u uw functie-app implementeert `WEBSITE_RUN_FROM_PACKAGE` `1` in Windows, moet u instellen op en publiceren met zip-implementatie.

Als volgt ziet u een functie-app die is geconfigureerd om uit te voeren vanaf een .zip-bestand dat wordt gehost in Azure Blob-opslag:

![WEBSITE_RUN_FROM_ZIP-app-instelling](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Momenteel worden alleen .zip-pakketbestanden ondersteund.

## <a name="integration-with-zip-deployment"></a>Integratie met zip-implementatie

[Zip-implementatie][Zip deployment for Azure Functions] is een functie van Azure App Service waarmee `wwwroot` u uw functie-app-project implementeren in de map. Het project is verpakt als een .zip-implementatiebestand. Dezelfde API's kunnen worden gebruikt om `d:\home\data\SitePackages` uw pakket in de map te implementeren. Met `WEBSITE_RUN_FROM_PACKAGE` de waarde `1`van de app-instelling van , `d:\home\data\SitePackages` kopiëren de ZIP-implementatie-API's uw pakket naar de map in plaats van de bestanden te extraheren naar `d:\home\site\wwwroot`. Het maakt `packagename.txt` ook het bestand. Na een herstart wordt het `wwwroot` pakket gemonteerd als een alleen-lezen bestandssysteem. Zie [Zip-implementatie voor Azure-functies voor](deployment-zip-push.md)meer informatie over zip-implementatie.

## <a name="adding-the-website_run_from_package-setting"></a>De instelling WEBSITE_RUN_FROM_PACKAGE toevoegen

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Problemen oplossen

- Run From `wwwroot` Package maakt alleen-lezen, zodat u een foutmelding ontvangt bij het schrijven van bestanden naar deze map.
- Tar- en gzip-formaten worden niet ondersteund.
- Deze functie bestaat niet samen met de lokale cache.
- Voor verbeterde prestaties bij koude start gebruikt`WEBSITE_RUN_FROM_PACKAGE`u de lokale zip-optie (=1).
- Run From Package is niet compatibel`SCM_DO_BUILD_DURING_DEPLOYMENT=true`met de optie voor implementatieaanpassing ( ), de buildstap wordt genegeerd tijdens de implementatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Doorlopende implementatie voor Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
