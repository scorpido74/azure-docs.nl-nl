---
title: Key Vault gebruiken bij het implementeren van een beheerde app
description: Laat zien hoe u toegangs geheimen in Azure Key Vault kunt gebruiken bij het implementeren van beheerde toepassingen
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: d82e5aed6318e112a0daabf581aec61c8ed5fcbc
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650641"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Key Vault geheim openen bij het implementeren van Azure Managed Applications

Wanneer u tijdens de implementatie een beveiligde waarde (zoals een wacht woord) wilt door geven, kunt u de waarde van een [Azure Key Vault](../../key-vault/key-vault-overview.md)ophalen. Als u toegang wilt krijgen tot de Key Vault bij het implementeren van beheerde toepassingen, moet u toegang verlenen tot de service-principal van het **toestel bron provider** . De service Managed Applications gebruikt deze identiteit voor het uitvoeren van bewerkingen. De Service-Principal moet toegang hebben tot de Key Vault om een waarde op te halen uit een Key Vault tijdens de implementatie.

In dit artikel wordt beschreven hoe u de Key Vault configureert voor gebruik met beheerde toepassingen.

## <a name="enable-template-deployment"></a>Sjabloon implementatie inschakelen

1. Selecteer uw Key Vault in de portal.

1. Selecteer **Toegangsbeleid**.   

   ![Toegangs beleid selecteren](./media/key-vault-access/select-access-policies.png)

1. Selecteer **Klik hierop om geavanceerde beleidsregels weer te geven**.

   ![Geavanceerd toegangs beleid weer geven](./media/key-vault-access/advanced.png)

1. Selecteer **toegang tot Azure Resource Manager inschakelen voor sjabloon implementatie**. Selecteer vervolgens **Opslaan**.

   ![Sjabloon implementatie inschakelen](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Service als Inzender toevoegen

1. Klik op **Toegangsbeheer (IAM)** .

   ![Toegangs beheer selecteren](./media/key-vault-access/access-control.png)

1. Selecteer **roltoewijzing toevoegen**.

   ![Toevoegen selecteren](./media/key-vault-access/add-access-control.png)

1. Selecteer **Inzender** voor de rol. Zoek naar de **resource provider** van het apparaat en selecteer deze in de beschik bare opties.

   ![Provider zoeken](./media/key-vault-access/search-provider.png)

1. Selecteer **Opslaan**.

## <a name="reference-key-vault-secret"></a>Referentie Key Vault geheim

Als u een geheim wilt door geven van een Key Vault naar een sjabloon in uw beheerde toepassing, moet u een [gekoppelde sjabloon](../templates/linked-templates.md) gebruiken en verwijzen naar de Key Vault in de para meters voor de gekoppelde sjabloon. Geef de resource-ID van de Key Vault en de naam van het geheim op.

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>Volgende stappen

U hebt uw Key Vault zodanig geconfigureerd dat deze toegankelijk is tijdens de implementatie van een beheerde toepassing.

* Zie voor informatie over het door geven van een waarde van een Key Vault als een sjabloon parameter [Azure Key Vault gebruiken om een veilige parameter waarde door te geven tijdens de implementatie](../templates/key-vault-parameter.md).
* Zie [voorbeeld projecten voor door Azure beheerde toepassingen](sample-projects.md)voor voor beelden van beheerde toepassingen.
* Zie [Aan de slag met CreateUiDefinition](create-uidefinition-overview.md) voor meer informatie over het maken van een UI-definitiebestand voor een beheerde toepassing.