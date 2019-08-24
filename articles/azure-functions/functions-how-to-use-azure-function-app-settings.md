---
title: Azure functie-app-instellingen configureren | Microsoft Docs
description: Meer informatie over het configureren van instellingen voor Azure function-apps.
author: ggailey777
manager: gwallace
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 27e56c3bcaa08fee25c362cdfed64c61b92873cb
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982661"
---
# <a name="how-to-manage-a-function-app"></a>Een functie-app beheren

In Azure Functions biedt een functie-app de uitvoerings context voor uw afzonderlijke functies. Het gedrag van functie-apps is van toepassing op alle functies die worden gehost door een bepaalde functie-app. In dit onderwerp wordt beschreven hoe u uw functie-apps kunt configureren en beheren in de Azure Portal.

Als u wilt beginnen, gaat u naar de [Azure Portal](https://portal.azure.com) en meldt u zich aan bij uw Azure-account. Typ de naam van uw functie-app in de zoekbalk boven in de portal en selecteer deze in de lijst. Nadat u de functie-app hebt geselecteerd, ziet u de volgende pagina:

![Overzicht van functie-app in de Azure Portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

U kunt navigeren naar alles wat u nodig hebt om uw functie-app te beheren op de pagina overzicht, met name de **[Toepassings instellingen](#settings)** en **[platform functies](#platform-features)** .

## <a name="settings"></a>Toepassings instellingen

Op het tabblad **Toepassings instellingen** worden instellingen onderhouden die worden gebruikt door de functie-app.

![Functie-app-instellingen in de Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

Deze instellingen worden versleuteld opgeslagen en u moet **waarden weer geven** selecteren om de waarden in de portal weer te geven.

Als u een instelling wilt toevoegen, selecteert u **nieuwe toepassings instelling** en voegt u het nieuwe sleutel/waarde-paar toe.

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Wanneer u een functie-app lokaal ontwikkelt, worden deze waarden bewaard in het bestand local. settings. json project.

## <a name="platform-features"></a>Platformfuncties

![Tabblad functie app platform-functies.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Functie-apps worden uitgevoerd in en worden onderhouden door het Azure App Service-platform. Als zodanig hebben uw functie-apps toegang tot de meeste functies van het hoofd platform voor webhosting van Azure. Op het tabblad **platform functies** krijgt u toegang tot de vele functies van het app service-platform dat u kunt gebruiken in uw functie-apps. 

> [!NOTE]
> Niet alle App Service-functies zijn beschikbaar wanneer een functie-app wordt uitgevoerd op het verbruiks hosting plan.

De rest van dit onderwerp richt zich op de volgende App Service functies in de Azure Portal die nuttig zijn voor functies:

+ [App Service editor](#editor)
+ [Console](#console)
+ [Geavanceerde hulp middelen (kudu)](#kudu)
+ [Implementatie opties](#deployment)
+ [CORS](#cors)
+ [Verificatie](#auth)
+ [API-definitie](#swagger)

Zie [Azure app service-instellingen configureren](../app-service/configure-common.md)voor meer informatie over het werken met app service-instellingen.

### <a name="editor"></a>App Service-editor

| | |
|-|-|
| ![Functie-app App Service-editor.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | De App Service editor is een geavanceerde editor in de portal die u kunt gebruiken om de JSON-configuratie bestanden en code bestanden te wijzigen. Als u deze optie kiest, wordt er een afzonderlijk browser tabblad met een basis editor geopend. Op deze manier kunt u integreren met de Git-opslag plaats, de code voor uitvoering en fout opsporing en de instellingen van de functie-app wijzigen. Deze editor biedt een verbeterde ontwikkel omgeving voor uw functies in vergelijking met de Blade standaard functie-app.    |

![De App Service editor](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="console"></a>Module

| | |
|-|-|
| ![De functie app-console in de Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | De console in de portal is een ideaal ontwikkelaars hulpprogramma wanneer u liever met uw functie-app vanaf de opdracht regel. Algemene opdrachten zijn onder andere het maken en navigeren van mappen en bestanden, en het uitvoeren van batch bestanden en scripts. |

![Console van functie-app](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Geavanceerde hulp middelen (kudu)

| | |
|-|-|
| ![Functie-app kudu in de Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | De geavanceerde hulp middelen voor App Service (ook wel bekend als kudu) bieden toegang tot geavanceerde beheer functies van uw functie-app. Vanuit kudu beheert u systeem informatie, app-instellingen, omgevings variabelen, site-uitbrei dingen, HTTP-headers en Server variabelen. U kunt **kudu** ook starten door te bladeren naar het SCM-eind punt voor uw functie-app, zoals`https://<myfunctionapp>.scm.azurewebsites.net/` |

![Kudu configureren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Implementatie opties

| | |
|-|-|
| ![Opties voor implementatie van de functie voor apps in de Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Met functies kunt u uw functie code op uw lokale machine ontwikkelen. U kunt vervolgens uw lokale functie-app-project uploaden naar Azure. Naast traditionele FTP-uploads kunt u met functies uw functie-app implementeren met behulp van populaire, continue integratie oplossingen, zoals GitHub, Azure DevOps, Dropbox, bitbucket en anderen. Zie [continue implementatie voor Azure functions](functions-continuous-deployment.md)voor meer informatie. Als u hand matig wilt uploaden met FTP of lokaal Git, moet u ook [uw implementatie referenties configureren](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![CORS voor de functie-app in de Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Om te voor komen dat schadelijke code wordt uitgevoerd in uw services, blokkeert App Service oproepen naar uw functie-apps vanuit externe bronnen. Functies ondersteunen het delen van cross-Origin-resources (CORS), zodat u een ' white list ' kunt definiëren van toegestane oorsprongen van waaruit uw functies externe aanvragen kunnen accepteren.  |

![CORS van functie-app configureren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Verificatie

| | |
|-|-|
| ![Functie-app-verificatie in de Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Wanneer functies een HTTP-trigger gebruiken, kunt u vereisen dat aanroepen eerst worden geverifieerd. App Service ondersteunt Azure Active Directory-verificatie en aanmelden met sociale providers, zoals Facebook, micro soft en Twitter. Zie [Azure app service Authentication-overzicht](../app-service/overview-authentication-authorization.md)voor meer informatie over het configureren van specifieke verificatie providers. |

![Verificatie configureren voor een functie-app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>API-definitie

| | |
|-|-|
| ![Functie-API-Swagger-definitie voor apps in de Azure Portal](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Functies ondersteunen Swagger zodat clients eenvoudiger uw door HTTP geactiveerde functies kunnen gebruiken. Voor meer informatie over het maken van API-definities met Swagger gaat u naar hosten van [een rest-API met CORS in azure app service](../app-service/app-service-web-tutorial-rest-api.md). U kunt ook functions-Proxy's gebruiken om één API-Opper vlak voor meerdere functies te definiëren. Zie [werken met Azure functions-proxy's](functions-proxies.md)voor meer informatie. |

![De API van functie-app configureren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Volgende stappen

+ [Azure App Service-instellingen configureren](../app-service/configure-common.md)
+ [Doorlopende implementatie voor Azure Functions](functions-continuous-deployment.md)



