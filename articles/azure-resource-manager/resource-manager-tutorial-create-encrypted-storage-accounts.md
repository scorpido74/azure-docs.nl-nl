---
title: Sjabloonverwijzing gebruiken
description: De verwijzing voor Azure Resource Manager-sjablonen gebruiken om een sjabloon te maken voor het implementeren van een versleuteld opslagaccount.
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 99ec64529b90c7a80aea62090f80c55cf4e23510
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326484"
---
# <a name="tutorial-utilize-the-azure-resource-manager-template-reference"></a>Tutorial: Utilize the Azure Resource Manager template reference

Meer informatie over het zoeken van de sjabloonschema-informatie en het gebruiken van deze informatie om Azure Resource Manager-sjablonen te maken.

In deze zelfstudie gebruikt u een basissjabloon uit Azure-snelstartsjablonen. U past de sjabloon aan door middel van referentiemateriaal voor sjablonen om een versleuteld opslagaccount te maken.

![Resource Manager template reference deploy encrypted storage account](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-tutorial-deploy-encrypted-storage-account.png)

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een snelstartsjabloon openen
> * Inzicht in de sjabloon
> * De sjabloonverwijzing zoeken
> * De sjabloon bewerken
> * De sjabloon implementeren

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).

## <a name="open-a-quickstart-template"></a>Een snelstartsjabloon openen

[Azure-snelstartsjablonen](https://azure.microsoft.com/resources/templates/) is een opslagplaats voor Resource Manager-sjablonen. In plaats van een sjabloon helemaal vanaf de basis te maken, kunt u een voorbeeldsjabloon zoeken en aanpassen. De in deze snelstart gebruikte sjabloon wordt [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Standaardopslagaccount maken) genoemd. De sjabloon definieert een Azure Storage-accountresource.

1. Selecteer in Visual Studio Code **Bestand**>**Bestand openen**.
2. Plak de volgende URL in **Bestandsnaam**:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Selecteer **Openen** om het bestand te openen.
4. Selecteer **Bestand**>**Opslaan als** om het bestand op uw lokale computer op te slaan als **azuredeploy.json**.

## <a name="understand-the-schema"></a>Informatie over het schema

1. Vouw vanuit VS Code de sjabloon samen naar het hoofdniveau. U hebt de eenvoudigste structuur met de volgende elementen:

    ![Eenvoudigste structuur voor Resource Manager-sjabloon](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema**: geef de locatie van het JSON-schemabestand op dat de versie van de sjabloontaal beschrijft.
    * **contentVersion**: geef een waarde op voor dit element om belangrijke wijzigingen in de sjabloon vast te leggen.
    * **parameters**: geef de waarden op die worden geleverd wanneer de implementatie wordt uitgevoerd om resource-implementatie aan te passen.
    * **variables**: geef de waarden op die worden gebruikt als JSON-fragmenten in de sjabloon voor het vereenvoudigen van sjabloontaalexpressies.
    * **resources**: geef de resourcetypen op die worden geïmplementeerd of bijgewerkt in een resourcegroep.
    * **outputs**: geef de waarden op die worden geretourneerd na de implementatie.

2. Vouw **Resources** uit. Er is een `Microsoft.Storage/storageAccounts`-resource gedefinieerd. Met de sjabloon maakt u een niet-versleuteld opslagaccount.

    ![Definitie opslagaccount Resource Manager-sjabloon](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>De sjabloonverwijzing zoeken

1. Browse to [Azure Template reference](https://docs.microsoft.com/azure/templates/).
2. In the **Filter by title** box, enter **storage accounts**.
3. Select **Reference/Template reference/Storage/&lt;Version>/Storage Accounts** as shown in the following screenshot:

    ![Resource Manager-sjabloon verwijzing opslagaccount](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    Als u niet weet welke versie u moet kiezen, gebruik dan de nieuwste versie.

4. Zoek de aan versleuteling gerelateerde definitie-informatie.

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    Op dezelfde pagina bevestigt de volgende beschrijving dat het object `encryption` wordt gebruikt om een versleuteld opslagaccount te maken.

    ![Versleuteling opslagaccount Resource Manager-sjabloonverwijzing](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    En er zijn twee manieren voor het beheren van de versleutelingssleutel. U kunt door Microsoft beheerde sleutels gebruiken met Storage Service Encryption of u kunt uw eigen versleutelingssleutels gebruiken. Om deze zelfstudie eenvoudig te houden, gebruikt u de optie `Microsoft.Storage`. U hoeft dus geen Azure Key Vault te maken.

    ![Versleutelingsobject opslagaccount Resource Manager-sjabloonverwijzing](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    Het versleutelingsobject moet er als volgt uitzien:

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>De sjabloon bewerken

Wijzig de sjabloon vanuit Visual Studio Code, zodat het resources-element er als volgt uitziet:

![Versleutelde resources van opslagaccount in Resource Manager-sjabloon](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Raadpleeg de sectie [De sjabloon implementeren](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) in de snelstartgids van Visual Studio Code voor de implementatieprocedure.

In de volgende schermafbeelding ziet u de CLI-opdracht voor het weergeven van het zojuist gemaakte opslagaccount, waarmee wordt aangegeven dat versleuteling is ingeschakeld voor de blobopslag.

![Versleuteld opslagaccount in Azure Resource Manager](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Voer de naam van de resourcegroep in het veld **Filter by name** in.
3. Selecteer de naam van de resourcegroep.  U ziet in totaal zes resources in de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u met sjabloonverwijzing een bestaande sjabloon kunt aanpassen. Voor informatie over hoe u meerdere exemplaren van een opslagaccount maakt, zie:

> [!div class="nextstepaction"]
> [Meerdere exemplaren maken](./resource-manager-tutorial-create-multiple-instances.md)
