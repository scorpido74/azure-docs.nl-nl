---
title: Doorlopende implementatie voor Azure Functions
description: Gebruik de continue implementatie functies van Azure App Service om uw functies te publiceren.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230889"
---
# <a name="continuous-deployment-for-azure-functions"></a>Doorlopende implementatie voor Azure Functions

U kunt Azure Functions gebruiken om uw code continu te implementeren met behulp van [broncode beheer integratie](functions-deployment-technologies.md#source-control). Met broncode beheer integratie kunt u een werk stroom maken waarin de implementatie van een code-update wordt geactiveerd naar Azure. Als u niet bekend bent met Azure Functions, kunt u aan de slag met het [overzicht van Azure functions](functions-overview.md).

Continue implementatie is een goede optie voor projecten waarbij u meerdere en frequente bijdragen integreert. Wanneer u doorlopende implementatie gebruikt, houdt u één bron van waarheid voor uw code bij, zodat teams eenvoudig kunnen samen werken. U kunt continue implementatie in Azure Functions configureren vanaf de volgende bron code locaties:

* [Azure-opslag plaatsen](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

De implementatie-eenheid voor functies in Azure is de functie-app. Alle functies in een functie-app worden op hetzelfde moment geïmplementeerd. Nadat u doorlopende implementatie hebt ingeschakeld, is de toegang tot de functie code in de Azure Portal geconfigureerd als *alleen-lezen* omdat de bron van waarheid is ingesteld op elders.

## <a name="requirements-for-continuous-deployment"></a>Vereisten voor continue implementatie

Voor een continue implementatie moet uw mapstructuur compatibel zijn met de basis mapstructuur die Azure Functions verwacht.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> Continue implementatie wordt nog niet ondersteund voor Linux-apps die worden uitgevoerd op een verbruiks abonnement. 

## <a name="credentials"></a>Continue implementatie instellen

Voer de volgende stappen uit om een continue implementatie voor een bestaande functie-app te configureren. De stappen illustreren de integratie met een GitHub-opslag plaats, maar soort gelijke stappen zijn van toepassing voor Azure opslag plaatsen of andere opslag plaatsen voor bron codes.

1. Selecteer in de functie-app [](https://portal.azure.com)in de Azure Portal **platform functies** > **Deployment Center**.

    ![Implementatie centrum openen](./media/functions-continuous-deployment/platform-features.png)

2. Selecteer **github**in het **implementatie centrum**en selecteer vervolgens **autoriseren**. Als u GitHub al hebt geautoriseerd, selecteert u **door gaan**. 

    ![Azure App Service implementatie centrum](./media/functions-continuous-deployment/github.png)

3. Selecteer in GitHub de knop **AzureAppService autoriseren** . 

    ![Azure App Service autoriseren](./media/functions-continuous-deployment/authorize.png)
    
    Selecteer **door gaan**in **implementatie centrum** in de Azure Portal.

4. Selecteer een van de volgende Build-providers:

    * **App service build-service**: het beste wanneer u geen build nodig hebt of als u een generieke build nodig hebt.
    * **Azure-pijp lijnen (preview)** : het beste wanneer u meer controle nodig hebt over de build. Deze provider is momenteel beschikbaar als preview-versie.

    ![Een build-provider selecteren](./media/functions-continuous-deployment/build.png)

5. Configureer informatie die specifiek is voor de bron beheer optie die u hebt opgegeven. Voor GitHub moet u waarden voor **organisatie**, **opslag plaats**en **vertakking**invoeren of selecteren. De waarden zijn gebaseerd op de locatie van uw code. Selecteer vervolgens **door gaan**.

    ![GitHub configureren](./media/functions-continuous-deployment/github-specifics.png)

6. Bekijk alle gegevens en selecteer vervolgens **volt ooien** om de configuratie van de implementatie te volt ooien.

    ![Samenvatting](./media/functions-continuous-deployment/summary.png)

Wanneer het proces is voltooid, wordt alle code uit de opgegeven bron in uw app geïmplementeerd. Op dat moment activeren wijzigingen in de implementatie bron een implementatie van die wijzigingen in de functie-app in Azure.

## <a name="deployment-scenarios"></a>Implementatiescenario’s

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Bestaande functies verplaatsen naar doorlopende implementatie

Als u al functies hebt geschreven in de [Azure Portal](https://portal.azure.com) en u de inhoud van uw app wilt downloaden voordat u overschakelt naar doorlopende implementatie, gaat u naar het tabblad **overzicht** van de functie-app. Selecteer de knop **app-inhoud downloaden** .

![App-inhoud downloaden](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Nadat u doorlopende integratie hebt geconfigureerd, kunt u de bron bestanden niet meer bewerken in de functions-Portal.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
