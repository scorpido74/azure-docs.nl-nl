---
title: Door de klant beheerde sleutels instellen om gegevens in rust in ISE's te versleutelen
description: Uw eigen versleutelingssleutels maken en beheren om gegevens in rust te beveiligen voor integratieserviceomgevingen (ISEs) in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127648"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Door de klant beheerde sleutels instellen om gegevens in rust te versleutelen voor integratieserviceomgevingen (ISEs) in Azure Logic Apps

Azure Logic Apps vertrouwt op Azure Storage om gegevens in rust op te slaan en automatisch [te versleutelen.](../storage/common/storage-service-encryption.md) Deze versleuteling beschermt uw gegevens en helpt u om te voldoen aan uw organisatorische beveiligings- en nalevingsverplichtingen. Azure Storage gebruikt standaard door Microsoft beheerde sleutels om uw gegevens te versleutelen. Zie [Azure Storage-versleuteling voor gegevens in rust](../storage/common/storage-service-encryption.md) en Azure Data [Encryption-at-Rest](../security/fundamentals/encryption-atrest.md)voor meer informatie over hoe Azure Storage-versleuteling werkt.

Wanneer u een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maakt voor het hosten van uw logische apps en u meer controle wilt over de versleutelingssleutels die door Azure Storage worden gebruikt, u uw eigen sleutel instellen, gebruiken en beheren met [Azure Key Vault.](../key-vault/key-vault-overview.md) Deze mogelijkheid wordt ook wel "Bring Your Own Key" (BYOK) genoemd en uw sleutel wordt een "door de klant beheerde sleutel" genoemd.

In dit onderwerp wordt uitgelegd hoe u uw eigen versleutelingssleutel instelt en opgeeft die u gebruiken wanneer u uw ISE maakt met behulp van de LOGIC Apps REST API. Zie [Een integratieserviceomgeving (ISE) maken met de LOGIC Apps REST API](../logic-apps/create-integration-service-environment-rest-api.md)voor de algemene stappen om een ISE te maken via logic apps rest api.

## <a name="considerations"></a>Overwegingen

* Op dit moment is klantbeheerde sleutelondersteuning voor een ISE alleen beschikbaar in deze Azure-regio's: West US 2, East US en South Central US

* U een door de klant beheerde sleutel alleen opgeven *wanneer u uw ISE maakt,* niet achteraf. U deze sleutel niet uitschakelen nadat uw ISE is gemaakt. Momenteel bestaat er geen ondersteuning voor het roteren van een door de klant beheerde sleutel voor een ISE.

* Om door klanten beheerde sleutels te ondersteunen, vereist uw ISE dat de [door het systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) is ingeschakeld. Met deze identiteit kan de ISE toegang tot bronnen in andere Azure AD-tenants (Azure Directory) verifiëren, zodat u zich niet hoeft aan te melden met uw referenties.

* Om momenteel een ISE te maken die door klanten beheerde sleutels ondersteunt en de door het systeem toegewezen identiteit heeft ingeschakeld, moet u de Logic Apps REST API aanroepen met behulp van een HTTPS PUT-verzoek.

* Binnen *30 minuten* nadat u het HTTPS PUT-verzoek hebt verzonden waarmee uw ISE is gemaakt, moet u [de sleutelkluis toegang geven tot de door het SYSTEEM toegewezen identiteit van uw ISE.](#identity-access-to-key-vault) Anders mislukt ISE-creatie en wordt er een fout met machtigingen gegenereerd.

## <a name="prerequisites"></a>Vereisten

* Dezelfde [voorwaarden](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) en [vereisten om toegang voor uw ISE in te schakelen](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) als wanneer u een ISE maakt in de Azure-portal

* Een Azure-sleutelkluis met de eigenschappen **Soft Delete** en Do **Not Purge** ingeschakeld

  Zie Overzicht van [Azure Key Vault soft-delete](../key-vault/key-vault-ovw-soft-delete.md) van Azure Key Vault en Configureren van door de klant [beheerde sleutels met Azure Key Vault](../storage/common/storage-encryption-keys-portal.md)voor meer informatie over het inschakelen van deze eigenschappen. Als u nieuw bent in Azure Key Vault, leest u hoe u [een sleutelkluis maakt](../key-vault/quick-create-portal.md#create-a-vault) met behulp van de Azure-portal of met de opdracht Azure PowerShell, [New-AzKeyVault.](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault)

* In uw sleutelkluis wordt een sleutel gemaakt met deze eigenschapswaarden:

  | Eigenschap | Waarde |
  |----------|-------|
  | **Sleuteltype** | RSA |
  | **RSA-sleutelgrootte** | 2048 |
  | **Ingeschakeld** | Ja |
  |||

  ![Uw door de klant beheerde versleutelingssleutel maken](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Zie [Door de klant beheerde sleutels configureren met Azure Key Vault](../storage/common/storage-encryption-keys-portal.md) of de opdracht Azure PowerShell, [Add-AzKeyVaultKey.](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey)

* Een tool die u gebruiken om uw ISE te maken door de Logic Apps REST API aan te roepen met een HTTPS PUT-aanvraag. U bijvoorbeeld [Postman](https://www.getpostman.com/downloads/)gebruiken of een logische app maken die deze taak uitvoert.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>ISE maken met sleutelkluis en beheerde identiteitsondersteuning

Als u uw ISE wilt maken door de LOGIC Apps REST API aan te roepen, voert u dit HTTPS PUT-verzoek in:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> De Logic Apps REST API 2019-05-01 versie vereist dat u uw eigen HTTP PUT-aanvraag voor ISE-connectors maakt.

Implementatie duurt meestal binnen twee uur om te voltooien. Af en toe kan de implementatie tot vier uur duren. Als u de implementatiestatus wilt controleren, selecteert u in de [Azure-portal](https://portal.azure.com)op uw Azure-werkbalk het pictogram meldingen, waarmee het deelvenster meldingen wordt geopend.

> [!NOTE]
> Als de implementatie mislukt of als u uw ISE verwijdert, kan het tot een uur duren voordat azure uw subnetten vrijgeeft. Deze vertraging betekent dat u mogelijk moet wachten voordat u deze subnetten in een andere ISE opnieuw gebruikt.
>
> Als u uw virtuele netwerk verwijdert, duurt het over het algemeen maximaal twee uur voordat azure uw subnetten vrijgeeft, maar deze bewerking kan langer duren. 
> Controleer bij het verwijderen van virtuele netwerken of er nog steeds geen bronnen zijn verbonden. 
> Zie [Virtueel netwerk verwijderen](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Aanvraagheader

Neem in de koptekst van de aanvraag de volgende eigenschappen op:

* `Content-type`: Stel deze `application/json`eigenschapswaarde in op .

* `Authorization`: Stel deze eigenschapswaarde in op het token aan toonder voor de klant die toegang heeft tot het Azure-abonnement of de brongroep die u wilt gebruiken.

### <a name="request-body"></a>Aanvraagbody

Schakel in de aanvraaginstantie ondersteuning in voor deze aanvullende items door hun informatie in uw ISE-definitie te verstrekken:

* De door het systeem toegewezen beheerde identiteit die uw ISE gebruikt om toegang te krijgen tot uw sleutelkluis
* Uw sleutelkluis en de door de klant beheerde sleutel die u wilt gebruiken

#### <a name="request-body-syntax"></a>Syntaxis van de hoofdtekst van het verzoek

Hier is de syntaxis van de aanvraaginstantie, waarin de eigenschappen worden beschreven die moeten worden gebruikt wanneer u uw ISE maakt:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Voorbeeld van het hoofdvoorbeeld van aanvraag

In dit voorbeeldaanvraaghoofd worden de voorbeeldwaarden weergegeven:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Geef toegang tot uw sleutelkluis

Binnen *30 minuten* nadat u het HTTP PUT-verzoek hebt verzonden om uw ISE te maken, moet u een toegangsbeleid toevoegen aan uw sleutelkluis voor de door het SYSTEEM toegewezen identiteit van uw ISE. Anders mislukt het maken van uw ISE en krijgt u een fout met machtigingen. 

Voor deze taak u de opdracht Azure PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) gebruiken of deze stappen volgen in de Azure-portal:

1. Open uw Azure-sleutelkluis in de [Azure-portal.](https://portal.azure.com)

1. Selecteer **access-beleid** > **Toegangsbeleid toevoegen**in het menu Sleutelkluis, bijvoorbeeld:

   ![Toegangsbeleid toevoegen voor beheerde identiteit met systeemtoegewezen identiteit](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Voer de volgende stappen uit nadat het deelvenster **Toegangsbeleid toevoegen** is geopend:

   1. Selecteer de volgende opties:

      | Instelling | Waarden |
      |---------|--------|
      | **De lijst configureren vanuit sjabloon (optioneel)** | Sleutelbeheer |
      | **Belangrijkste machtigingen** | - **Key Management Operations:** Get, List <p><p>- **Cryptografische bewerkingen:** Toets uitpakken, wrap-toets |
      |||

      ![Selecteer 'Sleutelbeheer' > 'Belangrijke machtigingen'](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Selecteer Geen **geselecteerd** **voor Selecteren voor Hoofd selecteren**. Nadat het **hoofdvenster** is geopend, zoekt en selecteert u in het zoekvak uw ISE. Wanneer u klaar bent, kiest **u Toevoegen** > **selecteren**.

      ![Selecteer uw ISE om te gebruiken als hoofd](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Wanneer u klaar bent met het deelvenster **Toegangsbeleid,** selecteert u **Opslaan**.

Zie [Key Vault-verificatie met een beheerde identiteit opgeven](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Key Vault](../key-vault/key-vault-overview.md)