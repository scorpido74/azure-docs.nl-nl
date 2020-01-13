---
title: Versleuteling van veilige assets in Automation
description: Azure Automation beveiligt beveiligde assets met behulp van meerdere coderings niveaus. De versleuteling wordt standaard uitgevoerd met door micro soft beheerde sleutels. Klanten kunnen hun Automation-accounts configureren voor het gebruik van door de klant beheerde sleutels voor versleuteling. In dit artikel worden de details van beide coderings methoden beschreven en wordt uitgelegd hoe u kunt scha kelen tussen de twee.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: fa8ea40d827807565e71d1e790c8c52986b85ec8
ms.sourcegitcommit: d48afd9a09f850b230709826d4a5cd46e57d19fa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75904952"
---
# <a name="secure-assets-in-azure-automation"></a>Assets in Azure Automation beveiligen

Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen bevatten. Deze assets worden beveiligd in Azure Automation met behulp van meerdere coderings niveaus. Op basis van de sleutel op het hoogste niveau die wordt gebruikt voor de versleuteling, zijn er twee modellen voor versleuteling:
-   Door micro soft beheerde sleutels gebruiken
-   Door de klant beheerde sleutels gebruiken

### <a name="microsoft-managed-keys"></a>Door micro soft beheerde sleutels

Uw Azure Automation-account maakt standaard gebruik van door micro soft beheerde sleutels.

Elke beveiligde Asset wordt versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel (gegevens versleutelings sleutel) die voor elk Automation-account wordt gegenereerd. Deze sleutels zijn versleuteld en opgeslagen in Azure Automation met behulp van nog een unieke sleutel die wordt gegenereerd voor elk account dat een account versleutelings sleutel (AEK) wordt genoemd. Deze versleutelings sleutels voor accounts versleutelen en opgeslagen in Azure Automation met behulp van door micro soft beheerde sleutels. 

### <a name="customer-managed-keys-with-key-vault-preview"></a>Door de klant beheerde sleutels met Key Vault (preview-versie)

U kunt versleuteling van beveiligde assets in Azure Automation op het niveau van een Automation-account beheren met uw eigen sleutels. Wanneer u een door de klant beheerde sleutel opgeeft op het niveau van het Automation-account, wordt die sleutel gebruikt voor het beveiligen en beheren van de toegang tot de account versleutelings sleutel voor het Automation-account, dat op zijn beurt wordt gebruikt voor het versleutelen en ontsleutelen van alle beveiligde assets. Door de klant beheerde sleutels bieden meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelings sleutels voor het beveiligen van uw beveiligde assets controleren. 

U moet Azure Key Vault gebruiken om door de klant beheerde sleutels op te slaan. U kunt zelf sleutels maken en deze opslaan in een sleutel kluis, of u kunt de Azure Key Vault-Api's gebruiken om sleutels te genereren.  Zie [Wat is Azure Key Vault?](../key-vault/key-vault-overview.md) voor meer informatie over Azure Key Vault.

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Door de klant beheerde sleutels voor een Automation-account inschakelen

Wanneer u versleuteling inschakelt met door de klant beheerde sleutels voor een Automation-account, Azure Automation verpakt de account versleutelings sleutel met de door de klant beheerde sleutel in de bijbehorende sleutel kluis. Het inschakelen van door de klant beheerde sleutels heeft geen invloed op de prestaties en het account wordt direct met de nieuwe sleutel versleuteld, zonder enige tijd vertraging.

Een nieuw Automation-account is altijd versleuteld met door micro soft beheerde sleutels. Het is niet mogelijk om door de klant beheerde sleutels in te scha kelen op het moment dat het account wordt gemaakt. Door de klant beheerde sleutels worden opgeslagen in Azure Key Vault en de sleutel kluis moet worden ingericht met toegangs beleid waarmee sleutel machtigingen worden verleend aan de beheerde identiteit die is gekoppeld aan het Automation-account. De beheerde identiteit is alleen beschikbaar nadat het opslag account is gemaakt.

Wanneer u de sleutel die wordt gebruikt voor Azure Automation beveiligde Asset Encryption wijzigt door door de klant beheerde sleutels in of uit te scha kelen, de sleutel versie bij te werken of een andere sleutel op te geven, wordt de versleuteling van de hoofd sleutel gewijzigd, maar de beveiligde assets in uw Azure Het Automation-account hoeft niet opnieuw te worden versleuteld.

In de volgende drie secties worden de mechanismen beschreven voor het inschakelen van door de klant beheerde sleutels voor een Automation-account. 

> [!NOTE] 
> Als u door de klant beheerde sleutels wilt inschakelen, moet u op dit moment Azure Automation REST-Api's maken met API versie 2020-01-13-preview

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Vereisten voor het gebruik van door de klant beheerde sleutels in Azure Automation

Voordat u door de klant beheerde sleutels voor een Automation-account inschakelt, moet u ervoor zorgen dat aan de volgende vereisten wordt voldaan

 - De beheerd-sleutel van de klant wordt opgeslagen in een Azure Key Vault. 
 - U moet de instellingen **voorlopig verwijderen** en **niet wissen** in de sleutel kluis inschakelen. Deze functies zijn vereist om sleutels te herstellen in geval van onbedoeld verwijderen.
 - Alleen RSA-sleutels worden ondersteund met Azure Automation versleuteling. Zie [informatie over Azure Key Vault sleutels, geheimen en certificaten](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)voor meer informatie over sleutels.
- Het Automation-account en de sleutel kluis kunnen zich in verschillende abonnementen bevindt, maar moeten zich in dezelfde Azure Active Directory Tenant.

### <a name="assign-an-identity-to-the-automation-account"></a>Een identiteit toewijzen aan het Automation-account

Als u door de klant beheerde sleutels wilt gebruiken met een Automation-account, moet uw Automation-account worden geverifieerd op basis van de sleutel kluis die door de klant beheerde sleutels opslaat. Azure Automation maakt gebruik van door het systeem toegewezen beheerde identiteiten om het account te verifiëren met Key Vault. Zie [Wat is beheerde identiteiten voor Azure-resources?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie over beheerde identiteiten.

Een door het systeem toegewezen beheerde identiteit configureren voor het Automation-account met behulp van de volgende REST API-aanroep

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```
Aanvraagbody
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```    

De door het systeem toegewezen identiteit voor het Automation-account wordt geretourneerd in het antwoord

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configure-the-key-vault-access-policy"></a>Het Key Vault-toegangs beleid configureren

Zodra een beheerde identiteit is toegewezen aan het Automation-account, configureert u de toegang tot de Key Vault de door de klant beheerde sleutels op te slaan. Azure Automation vereist **Get**, **rerecover**, **wrapKey**, **sleutel uitpakken** op de door de klant beheerde sleutels.

Een dergelijk toegangs beleid kan worden ingesteld met behulp van de volgende REST API aanroep.

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```
Aanvraagbody

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE] 
> De velden tenantId en objectId moeten worden voorzien van de waarden identiteit. tenantId en Identity. principalId van het antwoord van de beheerde identiteit voor het Automation-account.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>De configuratie van het Automation-account wijzigen voor het gebruik van de door de klant beheerde sleutel

Ten slotte kunt u uw Automation-account overschakelen van door micro soft beheerde sleutels naar door de klant beheerde sleutels, met behulp van de volgende REST API-aanroep.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```
Aanvraagbody

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```
Voorbeeldantwoord

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="manage-customer-managed-keys-lifecycle"></a>Door de klant beheerde sleutels beheren levenscyclus

### <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels draaien

U kunt een door de klant beheerde sleutel in Azure Key Vault draaien volgens uw nalevings beleid. Wanneer de sleutel wordt geroteerd, moet u het Automation-account bijwerken om de nieuwe sleutel-URI te gebruiken. 

Als u de sleutel roteert, wordt het opnieuw versleutelen van gegevens in het opslag account niet geactiveerd. Er is geen verdere actie vereist van de gebruiker.

## <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u Power shell of Azure CLI. Zie [Azure Key Vault Power shell](https://docs.microsoft.com/powershell/module/az.keyvault/) of [Azure Key Vault cli](https://docs.microsoft.com/cli/azure/keyvault)(Engelstalig) voor meer informatie. Als u toegang intrekt, wordt de toegang tot alle gegevens in het opslag account effectief geblokkeerd, omdat de versleutelings sleutel niet toegankelijk is voor Azure Storage.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Key Vault?](../key-vault/key-vault-overview.md) 
- [Verbindingsassets in Azure Automation](shared-resources/certificates.md)
- [Verbindingsreferentie in Azure Automation](shared-resources/credentials.md)
- [Variabele-assets in Azure Automation](shared-resources/variables.md)
