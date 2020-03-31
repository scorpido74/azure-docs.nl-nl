---
title: Resources implementeren met Azure-portal
description: Gebruik Azure portal en Azure Resource Manage om uw resources te implementeren in een brongroep in uw abonnement.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: ea2faf810b3a5d1b8fa46575201022a501cc7d58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153434"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Resources implementeren met ARM-sjablonen en Azure-portal

Meer informatie over het gebruik van de [Azure-portal](https://portal.azure.com) met [ARM-sjablonen (Azure Resource Manager)](overview.md) om uw Azure-bronnen te implementeren. Zie [Azure-resources beheren met behulp van de Azure-portal](../management/manage-resources-portal.md)voor meer informatie over het beheren van uw resources.

Het implementeren van Azure-resources met behulp van de Azure-portal omvat meestal twee stappen:

- Maak een resourcegroep.
- Resources implementeren in de resourcegroep.

Daarnaast u ook een ARM-sjabloon implementeren om Azure-resources te maken.

In dit artikel worden beide methoden weergegeven.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

1. Als u een nieuwe resourcegroep wilt maken, selecteert u **Resourcegroepen** in de [Azure-portal](https://portal.azure.com).

   ![Resourcegroepen selecteren](./media/deploy-portal/select-resource-groups.png)

1. Selecteer Onder Resourcegroepen de optie **Toevoegen**.

   ![Resourcegroep toevoegen](./media/deploy-portal/add-resource-group.png)

1. Selecteer of voer de volgende eigenschapswaarden in:

    - **Subscription**: selecteer een Azure-abonnement.
    - **Resourcegroep**: Geef de resourcegroep een naam.
    - **Regio**: Geef een Azure-locatie op. Dit is waar de resourcegroep metagegevens over de resources opslaat. Om nalevingsredenen u opgeven waar die metagegevens zijn opgeslagen. In het algemeen raden we u aan een locatie op te geven waar de meeste van uw resources zich bevinden. Het gebruik van dezelfde locatie kan uw sjabloon vereenvoudigen.

   ![Groepswaarden instellen](./media/deploy-portal/set-group-properties.png)

1. Selecteer **Controleren + maken**.
1. de waarden controleren en vervolgens **Maken**selecteren .
1. Selecteer **Vernieuwen** voordat u de nieuwe brongroep in de lijst zien.

## <a name="deploy-resources-to-a-resource-group"></a>Resources implementeren voor een resourcegroep

Nadat u een resourcegroep hebt gemaakt, u resources implementeren voor de groep vanuit de Marketplace. De Marketplace biedt vooraf gedefinieerde oplossingen voor veelvoorkomende scenario's.

1. Als u een implementatie wilt starten, selecteert **u Een resource maken** vanuit de [Azure-portal](https://portal.azure.com).

   ![Nieuwe bron](./media/deploy-portal/new-resources.png)

1. Zoek het type resource dat u wilt implementeren. De resources zijn ingedeeld in categorieën. Als u de specifieke oplossing die u wilt implementeren niet ziet, u ernaar zoeken op marketplace. De volgende schermafbeelding laat zien dat Ubuntu Server is geselecteerd.

   ![Resourcetype selecteren](./media/deploy-portal/select-resource-type.png)

1. Afhankelijk van het type geselecteerde resource, moet u een verzameling relevante eigenschappen instellen voordat u wordt geïmplementeerd. Voor alle typen moet u een doelbrongroep selecteren. In de volgende afbeelding ziet u hoe u een virtuele Linux-machine maakt en implementeert deze in de resourcegroep die u hebt gemaakt.

   ![Een resourcegroep maken](./media/deploy-portal/select-existing-group.png)

   U ook besluiten een resourcegroep te maken bij het implementeren van uw resources. Selecteer **Nieuw maken** en geef de resourcegroep een naam.

1. Je implementatie begint. De implementatie kan enkele minuten duren. Sommige resources nemen langer in beslag dan andere resources. Wanneer de implementatie is voltooid, ziet u een melding. Selecteer **Ga naar resource** om te openen

   ![Melding weergeven](./media/deploy-portal/view-notification.png)

1. Nadat u uw resources hebt geïmplementeerd, u meer resources aan de resourcegroep toevoegen door **Toevoegen te**selecteren.

   ![Bron toevoegen](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Resources implementeren uit aangepaste sjabloon

Als u een implementatie wilt uitvoeren maar geen van de sjablonen in de Marketplace wilt gebruiken, u een aangepaste sjabloon maken die de infrastructuur voor uw oplossing definieert. Zie [De structuur en syntaxis van ARM-sjablonen begrijpen](template-syntax.md)voor meer informatie over het maken van sjablonen.

> [!NOTE]
> De portalinterface ondersteunt geen verwijzing naar een [geheim uit een Key Vault.](key-vault-parameter.md) Gebruik in plaats daarvan [PowerShell](deploy-powershell.md) of [Azure CLI](deploy-cli.md) om uw sjabloon lokaal of vanuit een externe URI te implementeren.

1. Als u een aangepaste sjabloon via de portal wilt implementeren, selecteert u **Een resource**maken , zoeken naar **sjabloon**. en selecteer vervolgens **Sjabloonimplementatie**.

   ![Implementatie van zoeksjabloon](./media/deploy-portal/search-template.png)

1. Selecteer **Maken**.
1. U ziet verschillende opties voor het maken van een sjabloon:

    - **Bouw uw eigen sjabloon in editor:** maak een sjabloon met behulp van de portalsjablooneditor.  De editor kan een resourcesjabloonschema toevoegen.
    - **Algemene sjablonen:** er zijn vier algemene sjablonen voor het maken van een virtuele Linux-machine, een virtuele Windows-machine, een webtoepassing en een Azure SQL-database.
    - **Een GitHub-snelstartsjabloon laden:** gebruik een bestaande [quickstartsjablonen](https://azure.microsoft.com/resources/templates/).

   ![Opties weergeven](./media/deploy-portal/see-options.png)

    Deze zelfstudie geeft de instructie voor het laden van een quickstartsjabloon.

1. Typ of selecteer **onder Een GitHub-snelstartsjabloon**laden , typ of selecteer **101-storage-account-maak**.

    U hebt hiervoor twee opties:

    - **Selecteer sjabloon:** implementeer de sjabloon.
    - **Sjabloon bewerken:** bewerk de snelstartsjabloon voordat u deze implementeert.

1. Selecteer **Sjabloon bewerken** om de portalsjablooneditor te verkennen. De sjabloon wordt geladen in de editor. Let op: er zijn twee parameters: **storageAccountType** en **locatie**.

   ![Sjabloon maken](./media/deploy-portal/show-json.png)

1. Breng een kleine wijziging aan in de sjabloon. Werk bijvoorbeeld de variabele **storageAccountName** bij naar:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Selecteer **Opslaan**. Nu ziet u de interface voor de implementatie van portalsjablonen. Let op de twee parameters die u in de sjabloon hebt gedefinieerd.
1. Voer de eigenschapswaarden in of selecteer deze:

    - **Subscription**: selecteer een Azure-abonnement.
    - **Resourcegroep**: Selecteer **Nieuw maken** en geef een naam.
    - **Locatie:** Selecteer een Azure-locatie.
    - **Type opslagaccount:** gebruik de standaardwaarde.
    - **Locatie:** gebruik de standaardwaarde.
    - **Ik ga akkoord met de bovenstaande voorwaarden**: (selecteren)

1. Selecteer **Aankoop**.

## <a name="next-steps"></a>Volgende stappen

- Zie [Controlebewerkingen met Resourcemanager](../management/view-activity-logs.md)voor het weergeven van controlelogboeken.
- Zie [Implementatiebewerkingen weergeven](deployment-history.md)voor het oplossen van implementatiefouten.
- Zie [ARM-sjablonen exporteren](export-template-portal.md)als u een sjabloon wilt exporteren uit een implementatie- of resourcegroep.
- Zie [Azure Deployment Manager](deployment-manager-overview.md)om uw service veilig uit te rollen in meerdere regio's.
