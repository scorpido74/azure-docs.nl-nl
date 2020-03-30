---
title: Beheerde identiteiten configureren met Azure App-configuratie
description: Meer informatie over hoe beheerde identiteiten werken in Azure App-configuratie en hoe u een beheerde identiteit configureert
author: jpconnock
ms.topic: article
ms.date: 02/25/2020
ms.author: jeconnoc
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: fe66466395a100221e6a3cdebdef870bdf195afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623030"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Beheerde identiteiten gebruiken voor Azure App-configuratie

In dit onderwerp ziet u hoe u een beheerde identiteit maakt voor Azure App-configuratie. Met een beheerde identiteit van Azure Active Directory (AAD) kan Azure App-configuratie eenvoudig toegang krijgen tot andere door AAD beveiligde bronnen, zoals Azure Key Vault. De identiteit wordt beheerd door het Azure-platform. Het vereist niet dat u om te verstrekken of te draaien geen geheimen. Zie [Beheerde identiteiten voor Azure-resources voor](../active-directory/managed-identities-azure-resources/overview.md)meer informatie over beheerde identiteiten in AAD.

Uw aanvraag kan worden toegekend twee soorten identiteiten:

- Een **door het systeem toegewezen identiteit** is gekoppeld aan uw configuratiearchief. Het wordt verwijderd als uw configuratiearchief wordt verwijderd. Een configuratiearchief kan slechts één door het systeem toegewezen identiteit hebben.
- Een door de gebruiker toegewezen identiteit is een zelfstandige **Azure-bron** die kan worden toegewezen aan uw configuratiearchief. Een configuratiearchief kan meerdere door de gebruiker toegewezen identiteiten hebben.

## <a name="adding-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen

Voor het maken van een app-configuratiearchief met een door het systeem toegewezen identiteit moet een extra eigenschap in de store worden ingesteld.

### <a name="using-the-azure-cli"></a>Azure CLI gebruiken

Als u een beheerde identiteit wilt instellen met de Azure CLI, gebruikt u de opdracht az [appconfig-identiteit toewijzing] aan een bestaand configuratiearchief. U hebt drie opties voor het uitvoeren van de voorbeelden in deze sectie:

- Gebruik [Azure Cloud Shell](../cloud-shell/overview.md) vanuit de Azure-portal.
- Gebruik de ingesloten Azure Cloud Shell via de knop 'Probeer het' in de rechterbovenhoek van elk codeblok hieronder.
- [Installeer de nieuwste versie van Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.1 of hoger) als u liever een lokale CLI-console gebruikt.

Met de volgende stappen u een app-configuratiearchief maken en een identiteit toewijzen met de CLI:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login]. Gebruik een account dat is gekoppeld aan uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

1. Maak een App Configuration Store met de CLI. Zie Voorbeeld van [app-configuratie CLI](scripts/cli-create-service.md)voor meer voorbeelden van het gebruik van de CLI met Azure App-configuratie:

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Voer de opdracht [az appconfig-identiteit toe] om de door het systeem toegewezen identiteit voor dit configuratiearchief te maken:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit toevoegen

Als u een app-configuratiearchief maakt met een door de gebruiker toegewezen identiteit, moet u de identiteit maken en vervolgens de bron-id aan uw winkel toewijzen.

### <a name="using-the-azure-cli"></a>Azure CLI gebruiken

Als u een beheerde identiteit wilt instellen met de Azure CLI, gebruikt u de opdracht az [appconfig-identiteit toewijzing] aan een bestaand configuratiearchief. U hebt drie opties voor het uitvoeren van de voorbeelden in deze sectie:

- Gebruik [Azure Cloud Shell](../cloud-shell/overview.md) vanuit de Azure-portal.
- Gebruik de ingesloten Azure Cloud Shell via de knop 'Probeer het' in de rechterbovenhoek van elk codeblok hieronder.
- [Installeer de nieuwste versie van Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 of hoger) als u liever een lokale CLI-console gebruikt.

Met de volgende stappen u een door de gebruiker toegewezen identiteit en een app-configuratiearchief maken en vervolgens de identiteit toewijzen aan het archief met behulp van de CLI:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login]. Gebruik een account dat is gekoppeld aan uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

1. Maak een App Configuration Store met de CLI. Zie Voorbeeld van [app-configuratie CLI](scripts/cli-create-service.md)voor meer voorbeelden van het gebruik van de CLI met Azure App-configuratie:

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Maak een door de `myUserAssignedIdentity` gebruiker toegewezen identiteit die wordt aangeroepen met de CLI.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Noteer in de uitvoer van deze `id` opdracht de waarde van de eigenschap.

1. Voer de opdracht [az appconfig-identiteit toe] om de nieuwe door de gebruiker toegewezen identiteit toe te wijzen aan dit configuratiearchief. Gebruik de waarde `id` van de eigenschap die u in de vorige stap hebt opgemerkt.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Een identiteit verwijderen

Een door het systeem toegewezen identiteit kan worden verwijderd door de functie uit te schakelen met de opdracht identiteit [van AZ Appconfig](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) in de Azure CLI. Door de gebruiker toegewezen identiteiten kunnen afzonderlijk worden verwijderd. Als u een door het systeem toegewezen identiteit op deze manier verwijdert, wordt deze ook uit AAD verwijderd. Door het systeem toegewezen identiteiten worden ook automatisch uit AAD verwijderd wanneer de app-bron wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een ASP.NET Core-app maken met Azure-app-configuratie](quickstart-aspnet-core-app.md)

[az appconfig identiteit toewijzen]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
