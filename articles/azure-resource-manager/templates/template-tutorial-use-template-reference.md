---
title: Sjabloonverwijzing gebruiken
description: Gebruik de sjabloonverwijzing Azure Resource Manager om een sjabloon te maken.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b742982121a20a2b057eba4211584b0386dde411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373426"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Zelfstudie: De verwijzing naar de ARM-sjabloon gebruiken

Meer informatie over het zoeken naar de gegevens van het sjabloonschema en de informatie gebruiken om ARM-sjablonen (Azure Resource Manager) te maken.

In deze zelfstudie gebruikt u een basissjabloon uit Azure-snelstartsjablonen. Met behulp van sjabloonreferentiedocumentatie past u de sjabloon aan.

![Naslagwerk voor resourcebeheersjabloon implementeren-opslagaccount](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * Inzicht in de sjabloon
> * De sjabloonverwijzing zoeken
> * De sjabloon bewerken
> * De sjabloon implementeren

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code met de extensie Resource Manager Tools. Zie [Visual Studio Code gebruiken om ARM-sjablonen te maken.](use-vs-code-to-create-template.md)

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

[Azure Quickstart-sjablonen](https://azure.microsoft.com/resources/templates/) is een opslagplaats voor ARM-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De in deze snelstart gebruikte sjabloon wordt [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Standaardopslagaccount maken) genoemd. De sjabloon definieert een Azure Storage-accountresource.

1. Selecteer **Bestand**>**openen bestand**in Visual Studio-code .
1. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Selecteer **Openen** om het bestand te openen.
1. Selecteer **Bestand**>**opslaan als** om het bestand op te slaan als **azuredeploy.json** op uw lokale computer.

## <a name="understand-the-schema"></a>Informatie over het schema

1. Vouw vanuit VS Code de sjabloon samen naar het hoofdniveau. U hebt de eenvoudigste structuur met de volgende elementen:

    ![Eenvoudigste structuur voor Resource Manager-sjabloon](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: geef de locatie van het JSON-schemabestand op dat de versie van de sjabloontaal beschrijft.
    * **contentVersion**: geef een waarde op voor dit element om belangrijke wijzigingen in de sjabloon vast te leggen.
    * **parameters**: geef de waarden op die worden geleverd wanneer de implementatie wordt uitgevoerd om resource-implementatie aan te passen.
    * **variables**: geef de waarden op die worden gebruikt als JSON-fragmenten in de sjabloon voor het vereenvoudigen van sjabloontaalexpressies.
    * **resources**: geef de resourcetypen op die worden geïmplementeerd of bijgewerkt in een resourcegroep.
    * **outputs**: geef de waarden op die worden geretourneerd na de implementatie.

1. Vouw **Resources** uit. Er is een `Microsoft.Storage/storageAccounts`-resource gedefinieerd.

    ![Definitie opslagaccount Resource Manager-sjabloon](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

## <a name="find-the-template-reference"></a>De sjabloonverwijzing zoeken

1. Blader naar [Azure-sjabloonverwijzing](https://docs.microsoft.com/azure/templates/).
1. Voer in het vak **Filteren op titel** **opslagaccounts**in en selecteer de eerste **opslagaccounts** onder **verwijzing > opslag**.

    ![Resource Manager-sjabloon verwijzing opslagaccount](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Een resourceprovider heeft meestal meerdere API-versies:

    ![Naslaggeheugenvoorsjabloonbronaccountversies van resourcebeheer](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Selecteer **Alle bronnen** onder **Opslag** in het linkerdeelvenster. Op deze pagina worden de brontypen en -versies van de opslagbronprovider weergegeven. Het wordt aanbevolen om de nieuwste API-versies te gebruiken voor de resourcetypen die in uw sjabloon zijn gedefinieerd.

    ![Versies van naslaggeheugens van resourcebeheer-sjablonen](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Selecteer de nieuwste versie van het **brontype storageAccount.**  De nieuwste versie is **2019-06-01** wanneer dit artikel is geschreven.

1. Op deze pagina worden de details van het brontype storageAccount weergegeven.  Er worden bijvoorbeeld de toegestane waarden voor het **Sku-object** weergegeven. Er zijn meer skus dan wat wordt vermeld in de quickstart sjabloon die u eerder hebt geopend. U de snelstartsjabloon aanpassen om alle beschikbare opslagtypen op te nemen.

    ![Referentieopslagaccount voor resourcebeheer-sjabloon](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>De sjabloon bewerken

Voeg vanuit Visual Studio Code de extra opslagaccounttypen toe zoals in de volgende schermafbeelding:

![Bronbeheersjabloonopslagaccountbronnen](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Raadpleeg de sectie [De sjabloon implementeren](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) in de snelstartgids van Visual Studio Code voor de implementatieprocedure. Wanneer u de sjabloon implementeert, geeft u de parameter **storageAccountType** op met een nieuwe toegevoegde waarde, **bijvoorbeeld Premium_ZRS**. De deploy zou mislukken als u de oorspronkelijke snelstartsjabloon gebruikt omdat **Premium_ZRS** geen toegestane waarde was.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u met sjabloonverwijzing een bestaande sjabloon kunt aanpassen. Voor informatie over hoe u meerdere exemplaren van een opslagaccount maakt, zie:

> [!div class="nextstepaction"]
> [Meerdere exemplaren maken](./template-tutorial-create-multiple-instances.md)
