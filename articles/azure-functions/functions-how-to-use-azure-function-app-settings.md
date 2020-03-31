---
title: Functie-app-instellingen configureren in Azure
description: Meer informatie over het configureren van instellingen voor Azure-functie-apps.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276944"
---
# <a name="manage-your-function-app"></a>Uw functie-app beheren 

In Azure Functions biedt een functie-app de uitvoeringscontext voor uw afzonderlijke functies. Functie-app gedrag is van toepassing op alle functies gehost door een bepaalde functie app. Alle functies in een functie-app moeten van dezelfde [taal](supported-languages.md)zijn. 

Individuele functies in een functie-app worden samen geïmplementeerd en samen geschaald. Alle functies in dezelfde functie-app delen resources per exemplaar als de functie-app schalen. 

Verbindingstekenreeksen, omgevingsvariabelen en andere toepassingsinstellingen worden afzonderlijk gedefinieerd voor elke functie-app. Alle gegevens die moeten worden gedeeld tussen functie-apps, moeten extern worden opgeslagen in een opgeslagen opslag.

In dit artikel wordt beschreven hoe u uw functie-apps configureert en beheert. 

> [!TIP]  
> Veel configuratieopties kunnen ook worden beheerd met behulp van de [Azure CLI.] 

## <a name="get-started-in-the-azure-portal"></a>Aan de slag in de Azure-portal

Ga om te beginnen naar de [Azure-portal] en meld u aan bij uw Azure-account. Typ de naam van uw functie-app in de zoekbalk boven in de portal en selecteer deze in de lijst. Nadat u de functie-app hebt geselecteerd, ziet u de volgende pagina:

![Overzicht van de functie-app in de Azure-portal](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

U naar alles navigeren wat u nodig hebt om uw functie-app te beheren vanaf de overzichtspagina, met name de **[toepassingsinstellingen](#settings)** en **[de functies van het platform.](#platform-features)**

## <a name="application-settings"></a><a name="settings"></a>Toepassingsinstellingen

Op het tabblad **Toepassingsinstellingen** worden instellingen onderhouden die door uw functie-app worden gebruikt. Deze instellingen worden versleuteld opgeslagen en u moet **Waarden weergeven** selecteren om de waarden in de portal weer te geven. U hebt ook toegang tot toepassingsinstellingen met azure cli.

### <a name="portal"></a>Portal

Als u een instelling in de portal wilt toevoegen, selecteert u **Nieuwe toepassingsinstelling** en voegt u het nieuwe sleutelwaardepaar toe.

![Functie-app-instellingen in de Azure-portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure-CLI

De [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) opdracht retourneert de bestaande toepassingsinstellingen, zoals in het volgende voorbeeld:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

De [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) opdracht voegt een toepassingsinstelling toe of werkt deze bij. In het volgende voorbeeld wordt `CUSTOM_FUNCTION_APP_SETTING` een instelling `12345`met een sleutel met de naam en een waarde van:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Toepassingsinstellingen gebruiken

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Wanneer u een functie-app lokaal ontwikkelt, moet u lokale kopieën van deze waarden bijhouden in het projectbestand local.settings.json. Zie [Bestand Lokale instellingen](functions-run-local.md#local-settings-file)voor meer informatie.

## <a name="platform-features"></a>Platformfuncties

![Functie app platform functies tabblad.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Functie-apps worden uitgevoerd in en worden onderhouden door het Azure App Service-platform. Als zodanig hebben uw functie-apps toegang tot de meeste functies van azure's belangrijkste webhostingplatform. Op het tabblad **Platformfuncties** hebt u toegang tot de vele functies van het App Service-platform die u gebruiken in uw functie-apps. 

> [!NOTE]
> Niet alle app-servicefuncties zijn beschikbaar wanneer een functie-app wordt uitgevoerd op het hostingplan Voor verbruik.

De rest van dit artikel richt zich op de volgende App Service-functies in de Azure-portal die nuttig zijn voor functies:

+ [App-serviceeditor](#editor)
+ [Console](#console)
+ [Geavanceerde tools (Kudu)](#kudu)
+ [Implementatieopties](#deployment)
+ [CORS](#cors)
+ [Verificatie](#auth)

Zie [Instellingen voor Azure App-service configureren](../app-service/configure-common.md)voor meer informatie over het werken met instellingen voor App-service.

### <a name="app-service-editor"></a><a name="editor"></a>App-serviceeditor

![De app-serviceeditor](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

De App Service-editor is een geavanceerde in-portal-editor die u gebruiken om JSON-configuratiebestanden en codebestanden te wijzigen. Als u deze optie kiest, wordt een apart browsertabblad met een basiseditor gestart. Hiermee u integreren met de Git-repository, run- en debugcode en de instellingen van de functie-app wijzigen. Deze editor biedt een verbeterde ontwikkelomgeving voor uw functies in vergelijking met de ingebouwde functie-editor.  

We raden u aan om te overwegen uw functies op uw lokale computer te ontwikkelen. Wanneer u lokaal ontwikkelt en publiceert naar Azure, worden uw projectbestanden alleen-lezen in de portal. Zie [Azure-functies lokaal coderen en testen.](functions-develop-local.md)

### <a name="console"></a><a name="console"></a>Console

![Functie-app-console](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

De in-portal console is een ideale developer tool wanneer u liever interactie met uw functie app van de opdrachtregel. Veelvoorkomende opdrachten zijn directory- en bestandscreatie en navigatie, evenals het uitvoeren van batchbestanden en scripts. 

Bij het lokaal ontwikkelen raden we aan de [Azure Functions Core Tools](functions-run-local.md) en de Azure [CLI]te gebruiken.

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Geavanceerde tools (Kudu)

![Kudu configureren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

De geavanceerde tools voor App Service (ook bekend als Kudu) bieden toegang tot geavanceerde administratieve functies van uw functie-app. Vanuit Kudu beheert u systeeminformatie, app-instellingen, omgevingsvariabelen, site-extensies, HTTP-headers en servervariabelen. U **Kudu** ook starten door te bladeren naar het SCM-eindpunt voor uw functie-app, zoals`https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Implementatiecenter

Wanneer u een oplossing voor bronbeheer gebruikt om uw functiescode te ontwikkelen en te onderhouden, u met Deployment Center bouwen en implementeren vanuit bronbeheer. Uw project wordt gebouwd en geïmplementeerd in Azure wanneer u updates maakt. Zie [Implementatietechnologieën in Azure-functies](functions-deployment-technologies.md)voor meer informatie.

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Cross-origin-resources delen

Om kwaadaardige code-uitvoering op de client te voorkomen, blokkeren moderne browsers aanvragen van webtoepassingen naar resources die in een afzonderlijk domein worden uitgevoerd. [Met Cross-origin resource sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) kan een `Access-Control-Allow-Origin` koptekst aangeven welke oorsprongen eindpunten in uw functie-app mogen aanroepen.

#### <a name="portal"></a>Portal

Wanneer u de lijst Toegestane oorsprong voor uw `Access-Control-Allow-Origin` functie-app configureert, wordt de koptekst automatisch toegevoegd aan alle reacties van HTTP-eindpunten in uw functie-app. **Allowed origins** 

![Cors-lijst van de functie-app configureren](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Wanneer de`*`wildcard ( ) wordt gebruikt, worden alle andere domeinen genegeerd. 

Gebruik [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) de opdracht om een domein toe te voegen aan de lijst met toegestane oorsprong. In het volgende voorbeeld wordt het contoso.com-domein toegevoegd:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Gebruik [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) de opdracht om de huidige toegestane oorsprong weer te geven.

### <a name="authentication"></a><a name="auth"></a>Verificatie

![Verificatie configureren voor een functie-app](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Wanneer functies een HTTP-trigger gebruiken, u vereisen dat oproepen eerst worden geverifieerd. App Service ondersteunt Azure Active Directory-verificatie en aanmelding bij sociale providers, zoals Facebook, Microsoft en Twitter. Zie Overzicht van Azure App [Service-verificatie](../app-service/overview-authentication-authorization.md)voor meer informatie over het configureren van specifieke verificatieproviders. 


## <a name="next-steps"></a>Volgende stappen

+ [Azure App-serviceinstellingen configureren](../app-service/configure-common.md)
+ [Doorlopende implementatie voor Azure Functions](functions-continuous-deployment.md)

[Azure-CLI]: /cli/azure/
[Azure-portal]: https://portal.azure.com
