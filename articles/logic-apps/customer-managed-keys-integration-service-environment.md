---
title: Door de klant beheerde sleutels instellen voor het versleutelen van gegevens in rust in ISEs
description: Maak en beheer uw eigen coderings sleutels voor het beveiligen van gegevens op rest voor integratie service omgevingen (ISEs) in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 6f4e0744aad5f053cdda0a52b382ad3c86982c2f
ms.sourcegitcommit: d48afd9a09f850b230709826d4a5cd46e57d19fa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75904978"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Door de klant beheerde sleutels instellen om gegevens in rust te versleutelen voor integratie service omgevingen (ISEs) in Azure Logic Apps

Azure Logic Apps is afhankelijk van Azure Storage om gegevens in rust op te slaan en automatisch te [versleutelen](../storage/common/storage-service-encryption.md). Deze versleuteling beveiligt uw gegevens en helpt u te voldoen aan de verplichtingen voor beveiliging en naleving van uw organisatie. Azure Storage maakt standaard gebruik van door micro soft beheerde sleutels om uw gegevens te versleutelen. Zie voor meer informatie over de werking van Azure Storage versleuteling [Azure Storage versleuteling voor Data-at-rest](../storage/common/storage-service-encryption.md) en [Azure Data Encryption-at-rest](../security/fundamentals/encryption-atrest.md).

Wanneer u een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) maakt voor het hosten van uw logische apps en u meer controle wilt hebben over de versleutelings sleutels die worden gebruikt door Azure Storage, kunt u uw eigen sleutel instellen, gebruiken en beheren met behulp van [Azure Key Vault](../key-vault/key-vault-overview.md). Deze mogelijkheid is ook bekend als ' Bring Your Own Key ' (BYOK) en uw sleutel wordt een ' door de klant beheerde sleutel ' genoemd.

In dit onderwerp ziet u hoe u uw eigen versleutelings sleutel kunt instellen en opgeven voor het maken van uw ISE. 

## <a name="considerations"></a>Overwegingen

* Op dit moment is de door de klant beheerde sleutel ondersteuning voor een ISE alleen beschikbaar in de volgende Azure-regio's: VS-West 2, VS-Oost en Zuid-Centraal VS

* U kunt alleen een door de klant beheerde sleutel opgeven *Wanneer u uw ISE maakt*, niet daarna. U kunt deze sleutel niet uitschakelen nadat de ISE is gemaakt. Momenteel bestaat er geen ondersteuning voor het draaien van een door de klant beheerde sleutel voor een ISE.

* Ter ondersteuning van door de klant beheerde sleutels vereist uw ISE dat de door het [systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) is ingeschakeld. Met deze identiteit kan de ISE toegang tot resources in andere Azure Active Directory-tenants (Azure AD) verifiëren, zodat u zich niet hoeft aan te melden met uw referenties.

* Als u momenteel een ISE wilt maken die door de klant beheerde sleutels ondersteunt en de door het systeem toegewezen identiteit is ingeschakeld, moet u de Logic Apps REST API aanroepen met behulp van een HTTPS PUT-aanvraag.

* Binnen *30 minuten* nadat u de https put-aanvraag hebt verzonden die uw ISE maakt, moet u toegang tot de [sleutel kluis verlenen aan de door het systeem toegewezen identiteit van uw ISE](#identity-access-to-key-vault). Anders mislukt het maken van ISE en wordt er een machtigings fout gegenereerd.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een Azure-sleutel kluis met de opties **voorlopig verwijderen** en **niet opschonen** ingeschakeld

  Zie [Azure Key Vault overzicht van voorlopig verwijderen](../key-vault/key-vault-ovw-soft-delete.md) en [door de klant beheerde sleutels configureren met Azure Key Vault](../storage/common/storage-encryption-keys-portal.md)voor meer informatie over het inschakelen van deze eigenschappen. Als u niet bekend bent met Azure Key Vault, kunt [u leren hoe u een sleutel kluis maakt](../key-vault/quick-create-portal.md#create-a-vault) met behulp van de Azure portal of met behulp van de Azure PowerShell opdracht [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault).

* In uw sleutel kluis is dit een sleutel die is gemaakt met de volgende eigenschaps waarden:

  | Eigenschap | Waarde |
  |----------|-------|
  | **Sleutel type** | RSA |
  | **RSA-sleutel grootte** | 2048 |
  | **Ingeschakeld** | Ja |
  |||

  ![Uw door de klant beheerde versleutelings sleutel maken](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Zie voor meer informatie door de [klant beheerde sleutels configureren met Azure Key Vault](../storage/common/storage-encryption-keys-portal.md) of de Azure PowerShell opdracht, [add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey).

* Een hulp programma dat u kunt gebruiken om uw ISE te maken door de Logic Apps REST API aan te roepen met een HTTPS-aanvraag. U kunt bijvoorbeeld [postman](https://www.getpostman.com/downloads/)gebruiken of u kunt een logische app maken die deze taak uitvoert.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>ISE maken met sleutel kluis en beheerde identiteits ondersteuning

Als u uw ISE wilt maken door de Logic Apps REST API aan te roepen, maakt u deze HTTPS-aanvraag:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Voor de versie Logic Apps REST API 2019-05-01 moet u uw eigen HTTP PUT-aanvraag voor ISE-connectors maken.

### <a name="request-header"></a>Aanvraagheader

Neem de volgende eigenschappen op in de aanvraag header:

* `Content-type`: Stel de waarde van deze eigenschap in op `application/json`.

* `Authorization`: Stel de waarde van deze eigenschap in op het Bearer-token voor de klant die toegang heeft tot het Azure-abonnement of de resource groep die u wilt gebruiken.

### <a name="request-body"></a>Aanvraagbody

Schakel in de hoofd tekst van de aanvraag ondersteuning in voor deze aanvullende items door hun gegevens op te geven in uw ISE-definitie:

* De door het systeem toegewezen beheerde identiteit die uw ISE gebruikt om toegang te krijgen tot uw sleutel kluis
* Uw sleutel kluis en de door de klant beheerde sleutel die u wilt gebruiken

#### <a name="request-body-syntax"></a>Syntaxis van aanvraag tekst

Hier volgt de syntaxis van de hoofd tekst van de aanvraag, waarin de eigenschappen worden beschreven die moeten worden gebruikt bij het maken van uw ISE:

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

#### <a name="request-body-example"></a>Voor beeld van aanvraag tekst

In dit voor beeld van de aanvraag tekst worden de voorbeeld waarden weer gegeven:

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

## <a name="grant-access-to-your-key-vault"></a>Toegang verlenen tot uw sleutel kluis

Binnen *30 minuten* nadat u de HTTP put-aanvraag voor het maken van uw ISE hebt verzonden, moet u een toegangs beleid toevoegen aan de sleutel kluis voor de door het systeem toegewezen identiteit van uw ISE. Als u dit niet doet, mislukt het maken van uw ISE en krijgt u een machtigings fout. 

Voor deze taak kunt u de Azure PowerShell [set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) gebruiken of kunt u deze stappen volgen in de Azure portal:

1. Open uw Azure-sleutel kluis in de [Azure Portal](https://portal.azure.com).

1. Selecteer in uw sleutel kluis menu **toegangs beleid** > **toegangs beleid toe te voegen**, bijvoorbeeld:

   ![Toegangs beleid voor door het systeem toegewezen beheerde identiteit toevoegen](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Wanneer het deel venster **toegangs beleid toevoegen** wordt geopend, voert u de volgende stappen uit:

   1. Selecteer deze opties:

      | Instelling | Waarden |
      |---------|--------|
      | **Van sjabloon configureren (optioneel)** | Sleutel beheer |
      | **Sleutel machtigingen** | - **bewerkingen voor sleutel beheer**: ophalen, lijst <p><p>- **cryptografische bewerkingen**: uitpakken van sleutel, terugloop sleutel |
      |||

      ![Selecteer sleutel beheer > sleutel machtigingen](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Selecteer voor **Select Principal** **geen geselecteerd**. Nadat het **hoofd** venster is geopend, zoekt en selecteert u uw ISE in het zoekvak. Wanneer u klaar bent, kiest u > **toevoegen** **selecteren** .

      ![Selecteer de ISE die u als Principal wilt gebruiken](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Wanneer u klaar bent met het deel venster **toegangs beleid** , selecteert u **Opslaan**.

Zie [Key Vault-verificatie bieden met een beheerde identiteit](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Key Vault](../key-vault/key-vault-overview.md)