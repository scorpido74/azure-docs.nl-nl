---
title: Doorlopende implementatie voor Azure Functions
description: Gebruik de doorlopende implementatiefuncties van Azure App Service om uw functies te publiceren.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277022"
---
# <a name="continuous-deployment-for-azure-functions"></a>Doorlopende implementatie voor Azure Functions

U Azure-functies gebruiken om uw code continu te implementeren met behulp van [integratie van bronbeheer.](functions-deployment-technologies.md#source-control) Met de integratie van bronbeheer kan een werkstroom worden geïmplementeerd waarin een code-update implementatie naar Azure activeert. Als u nieuw bent in Azure-functies, gaat u aan de slag door het [overzicht Azure-functies te bekijken.](functions-overview.md)

Continue implementatie is een goede optie voor projecten waarbij u meerdere en frequente bijdragen integreert. Wanneer u continue implementatie gebruikt, behoudt u één enkele bron van waarheid voor uw code, waardoor teams eenvoudig kunnen samenwerken. U continue implementatie configureren in Azure-functies vanaf de volgende broncodelocaties:

* [Azure-opslagplaatsen](https://azure.microsoft.com/services/devops/repos/)
* [GitHub GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

De eenheid van implementatie voor functies in Azure is de functie-app. Alle functies in een functie-app worden tegelijkertijd geïmplementeerd. Nadat u continue implementatie hebt ingeschakeld, wordt de toegang tot functiecode in de Azure-portal geconfigureerd als *alleen-lezen* omdat de bron van de waarheid ergens anders is ingesteld.

## <a name="requirements-for-continuous-deployment"></a>Vereisten voor continue implementatie

Als u de implementatie continu wilt uitvoeren, moet uw mapstructuur compatibel zijn met de basismapstructuur die Azure Functions verwacht.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> Continue implementatie wordt nog niet ondersteund voor Linux-apps die worden uitgevoerd op een verbruiksplan. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Doorlopende implementatie instellen

Voer deze stappen uit om continue implementatie voor een bestaande functie-app te configureren. De stappen tonen integratie met een GitHub-repository aan, maar vergelijkbare stappen zijn van toepassing op Azure Repos of andere broncoderepositories.

1. Selecteer In uw functie-app in de [Azure-portal](https://portal.azure.com)de optie **Platformfuncties** > **Implementatiecentrum**.

    ![Implementatiecentrum openen](./media/functions-continuous-deployment/platform-features.png)

2. Selecteer **In Implementatiecentrum**De optie **GitHub**en selecteer vervolgens **Autoriseren**. Als u GitHub al hebt geautoriseerd, selecteert u **Doorgaan**. 

    ![Azure App Service Deployment Center](./media/functions-continuous-deployment/github.png)

3. Selecteer in GitHub de knop **AzureAppService autoriseren.** 

    ![Azure-appservice autoriseren](./media/functions-continuous-deployment/authorize.png)
    
    Selecteer **Doorgaan in** **Implementatiecentrum** in de Azure-portal .

4. Selecteer een van de volgende buildproviders:

    * **App Service build service:** Het beste als je geen build nodig hebt of als je een generieke build nodig hebt.
    * **Azure Pipelines (Preview)**: Het beste wanneer u meer controle over de build nodig hebt. Deze provider is momenteel in preview.

    ![Een buildprovider selecteren](./media/functions-continuous-deployment/build.png)

5. Configureer informatie die specifiek is voor de optie bronbeheer die u hebt opgegeven. Voor GitHub moet u waarden invoeren of selecteren voor **Organisatie,** **Repository**en **Branch**. De waarden zijn gebaseerd op de locatie van uw code. Selecteer vervolgens **Doorgaan**.

    ![GitHub configureren](./media/functions-continuous-deployment/github-specifics.png)

6. Controleer alle details en selecteer **Voltooien** om uw implementatieconfiguratie te voltooien.

    ![Samenvatting](./media/functions-continuous-deployment/summary.png)

Wanneer het proces is voltooid, wordt alle code van de opgegeven bron geïmplementeerd in uw app. Op dat moment leiden wijzigingen in de implementatiebron tot een implementatie van deze wijzigingen in uw functie-app in Azure.

## <a name="deployment-scenarios"></a>Implementatiescenario's

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Bestaande functies verplaatsen naar continue implementatie

Als u al functies hebt geschreven in de [Azure-portal](https://portal.azure.com) en u de inhoud van uw app wilt downloaden voordat u overschakelt naar continue implementatie, gaat u naar het tabblad **Overzicht** van uw functie-app. Selecteer de knop **Inhoud van de app downloaden.**

![App-inhoud downloaden](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Nadat u continue integratie hebt geconfigureerd, u uw bronbestanden niet meer bewerken in de portal Functies.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor Azure Functions](functions-best-practices.md)
