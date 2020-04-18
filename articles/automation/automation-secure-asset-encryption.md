---
title: Versleuteling van beveiligde activa in Azure Automation
description: Azure Automation beschermt beveiligde assets met meerdere niveaus van versleuteling. Standaard wordt de versleuteling uitgevoerd met door Microsoft beheerde sleutels. Klanten kunnen hun automatiseringsaccounts configureren om door de klant beheerde sleutels te gebruiken voor versleuteling. In dit artikel worden de details van beide versleutelingsmodi beschreven en hoe u schakelen tussen de twee.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 594bac257c2b9739f1ece276c881348b35d2f704
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604820"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>Versleuteling van beveiligde activa in Azure Automation

Beveiligde elementen in Azure Automation omvatten referenties, certificaten, verbindingen en versleutelde variabelen. Deze elementen worden beveiligd in Azure Automation met behulp van meerdere niveaus van versleuteling. Op basis van de bovenste toets die wordt gebruikt voor de versleuteling, zijn er twee modellen voor versleuteling:
-    Door Microsoft beheerde sleutels gebruiken
-    Sleutels met behulp van klanten

## <a name="microsoft-managed-keys"></a>Door Microsoft beheerde sleutels

Standaard gebruikt uw Azure Automation-account door Microsoft beheerde sleutels.

Elk beveiligde element wordt versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel (Gegevensversleutelingssleutel) die wordt gegenereerd voor elk automatiseringsaccount. Deze sleutels zelf worden versleuteld en opgeslagen in Azure Automation met behulp van nog een andere unieke sleutel die wordt gegenereerd voor elk account genaamd een Account Encryption Key (AEK). Deze accountversleutelingssleutels zijn versleuteld en opgeslagen in Azure Automation met behulp van door Microsoft beheerde sleutels. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Door de klant beheerde sleutels met Key Vault (voorbeeld)

U versleuteling van beveiligde assets voor uw Automation-account beheren met uw eigen sleutels. Wanneer u een door de klant beheerde sleutel opgeeft op het niveau van het Automatiseringsaccount, wordt die sleutel gebruikt om de toegang tot de accountversleutelingssleutel voor het Automatiseringsaccount te beschermen en te beheren. Dit op zijn beurt wordt gebruikt om te versleutelen en te decoderen van alle beveiligde activa. Door de klant beheerde sleutels bieden meer flexibiliteit om toegangsbesturingselementen te maken, te roteren, uit te schakelen en in te trekken. U ook de versleutelingssleutels controleren die worden gebruikt om uw beveiligde activa te beschermen.

Gebruik Azure Key Vault om door klanten beheerde sleutels op te slaan. U uw eigen sleutels maken en opslaan in een sleutelkluis, of u de Azure Key Vault API's gebruiken om sleutels te genereren.  Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Door de klant beheerde sleutels inschakelen voor een Automatiseringsaccount

Wanneer u versleuteling inschakelt met door de klant beheerde sleutels voor een Automatiseringsaccount, verpakt Azure Automation de accountversleutelingssleutel met de door de klant beheerde sleutel in de bijbehorende sleutelkluis. Het inschakelen van door de klant beheerde sleutels heeft geen invloed op de prestaties en het account wordt onmiddellijk, zonder vertraging, versleuteld met de nieuwe sleutel.

Een nieuw Automation-account wordt altijd versleuteld met door Microsoft beheerde sleutels. Het is niet mogelijk om door de klant beheerde sleutels in te schakelen op het moment dat het account wordt gemaakt. Door de klant beheerde sleutels worden opgeslagen in Azure Key Vault en de sleutelkluis moet worden ingericht met toegangsbeleid waarmee belangrijke machtigingen worden verleend aan de beheerde identiteit die is gekoppeld aan het automatiseringsaccount. De beheerde identiteit is alleen beschikbaar nadat het opslagaccount is gemaakt.

Wanneer u de sleutel wijzigt die wordt gebruikt voor beveiligde assetversleuteling van Azure Automation, door door klanten beheerde sleutels in te schakelen of uit te schakelen, de sleutelversie bij te werken of een andere sleutel op te geven, hoeft de versleuteling van de accountversleutelingssleutel te worden gewijzigd, maar hoeven de beveiligde elementen in uw Azure Automation-account niet opnieuw te worden versleuteld.

In de volgende drie secties worden de mechanica beschreven van het inschakelen van door de klant beheerde sleutels voor een Automation-account. 

> [!NOTE] 
> Als u door de klant beheerde sleutels wilt inschakelen, moet u Azure Automation REST API-aanroepen maken met api-versie 2020-01-13-preview

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Vereisten voor het gebruik van door de klant beheerde sleutels in Azure Automation

Voordat u door de klant beheerde sleutels inschakelt voor een Automation-account, moet u ervoor zorgen dat aan de volgende vereisten wordt voldaan:

 - De door de klant bemande sleutel wordt opgeslagen in een Azure Key Vault. 
 - Schakel zowel de eigenschappen **Soft Delete** als Do **Not Purge** in op de sleutelkluis. Deze functies zijn vereist om het herstel van sleutels mogelijk te maken in geval van onbedoelde verwijdering.
 - Alleen RSA-sleutels worden ondersteund met Azure Automation-versleuteling. Zie [Over Azure Key Vault-sleutels, -geheimen en -certificaten](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)voor meer informatie over sleutels.
- Het automatiseringsaccount en de sleutelkluis kunnen in verschillende abonnementen zijn, maar moeten zich in dezelfde Azure Active Directory-tenant bevinden.

### <a name="assign-an-identity-to-the-automation-account"></a>Een identiteit toewijzen aan het automatiseringsaccount

Als u door de klant beheerde sleutels wilt gebruiken met een Automatiseringsaccount, moet uw Automation-account zich verifiëren tegen de sleutelkluis die door de klant beheerde sleutels opslaat. Azure Automation gebruikt beheerde identiteiten met systeemtoegewezen identiteiten om het account te verifiëren met Azure Key Vault. Zie [Wat zijn beheerde identiteiten voor Azure-bronnen voor](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) meer informatie over beheerde identiteiten?

Een beheerde identiteit met een systeem configureren aan het automatiseringsaccount met behulp van de volgende REST API-aanroep:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Aanvraagtekst:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

De door het systeem toegewezen identiteit voor het automatiseringsaccount wordt geretourneerd in een antwoord dat vergelijkbaar is met het volgende:

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

### <a name="configure-the-key-vault-access-policy"></a>Het toegangsbeleid voor key vault configureren

Zodra een beheerde identiteit is toegewezen aan het Automatiseringsaccount, configureert u de toegang tot de sleutelkluis die door de klant beheerde sleutels opslaat. Azure Automation vereist **ophalen,** **herstellen,** **wrapKey**, **UnwrapKey** op de door de klant beheerde sleutels.

Een dergelijk toegangsbeleid kan worden ingesteld met behulp van de volgende REST API-aanroep:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Aanvraagtekst:

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
> De **velden tenantId** en **objectId** moeten worden voorzien van waarden van **identity.tenantId** en **identity.principalId** respectievelijk uit de reactie van de beheerde identiteit voor het Automation-account.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>De configuratie van het Automation-account wijzigen om de door de klant beheerde sleutel te gebruiken

Ten slotte u uw Automation-account overschakelen van door Microsoft beheerde sleutels naar door de klant beheerde sleutels, met behulp van de volgende REST API-aanroep:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Aanvraagtekst:

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

## <a name="manage-customer-managed-keys-lifecycle"></a>Levenscyclus van door de klant beheerde sleutels beheren

### <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels roteren

U een door de klant beheerde sleutel roteren in Azure Key Vault volgens uw nalevingsbeleid. Wanneer de sleutel is gedraaid, moet u het automatiseringsaccount bijwerken om de nieuwe sleutel URI te gebruiken.

Het roteren van de sleutel leidt niet tot herversleuteling van beveiligde elementen in het Automatiseringsaccount. Er is geen verdere actie vereist.

### <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u PowerShell of de Azure CLI. Zie [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) of Azure Key Vault [CLI](https://docs.microsoft.com/cli/azure/keyvault)voor meer informatie. Als u de toegang intrekt, wordt de toegang tot alle beveiligde elementen in het automatiseringsaccount effectief verbroken, omdat de versleutelingssleutel niet toegankelijk is voor Azure Automation.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Sleutelkluis?](../key-vault/general/overview.md)
- [Verbindingsassets in Azure Automation](shared-resources/certificates.md)
- [Verbindingsreferentie in Azure Automation](shared-resources/credentials.md)
- [Variabele-assets in Azure Automation](shared-resources/variables.md)
