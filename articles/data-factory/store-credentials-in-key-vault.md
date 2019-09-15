---
title: Referenties opslaan in Azure Key Vault | Microsoft Docs
description: Meer informatie over het opslaan van referenties voor gegevens archieven die worden gebruikt in een Azure-sleutel kluis die Azure Data Factory automatisch kan ophalen tijdens runtime.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: 3f46c54edff2bc765e75742848f83d30e7aa7c09
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003396"
---
# <a name="store-credential-in-azure-key-vault"></a>Referenties opslaan in Azure Key Vault

U kunt referenties voor gegevens archieven en berekeningen opslaan in een [Azure Key Vault](../key-vault/key-vault-overview.md). Azure Data Factory haalt de referenties op bij het uitvoeren van een activiteit die gebruikmaakt van de gegevens opslag/compute.

Momenteel ondersteunen alle activiteitstypen behalve aangepaste activiteiten deze functie. Voor de configuratie van de connector raadpleegt u de sectie ' eigenschappen van gekoppelde service ' in [elk onderwerp](copy-activity-overview.md#supported-data-stores-and-formats) van de connector voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Deze functie is afhankelijk van de data factory beheerde identiteit. Meer informatie over hoe het werkt vanuit de [beheerde identiteit voor Data Factory](data-factory-service-identity.md) en om ervoor te zorgen dat uw Data Factory een koppeling hebben.

## <a name="steps"></a>Stappen

Als u wilt verwijzen naar een referentie die is opgeslagen in Azure Key Vault, moet u het volgende doen:

1. **Haal Data Factory beheerde identiteit** op door de waarde van de beheerde ID-toepassings-id die samen met uw fabriek is gegenereerd te kopiëren. Als u de gebruikers interface voor ADF-ontwerpen gebruikt, wordt de ID van de beheerde identiteits toepassing weer gegeven in het venster Azure Key Vault gekoppelde service maken. u kunt deze ook ophalen uit Azure Portal. Raadpleeg [Data Factory beheerde identiteit ophalen](data-factory-service-identity.md#retrieve-managed-identity).
2. **Verleen de beheerde identiteit toegang tot uw Azure Key Vault.** In uw sleutel kluis-> toegangs beleid-> Voeg nieuwe toe > Zoek deze toepassings-ID van de beheerde identiteit in **om toestemming te geven in** de vervolg keuzelijst geheim machtigingen. Hiermee kan deze toegewezen Factory toegang krijgen tot het geheim in de sleutel kluis.
3. **Een gekoppelde service maken die verwijst naar uw Azure Key Vault.** Raadpleeg [Azure Key Vault gekoppelde service](#azure-key-vault-linked-service).
4. **Maak een gekoppelde Data Store-service, waarin wordt verwezen naar het corresponderende geheim dat is opgeslagen in de sleutel kluis.** Raadpleeg het [referentie geheim dat is opgeslagen in de sleutel kluis](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault gekoppelde service

De volgende eigenschappen worden ondersteund voor Azure Key Vault gekoppelde service:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type moet worden ingesteld op: **AzureKeyVault**. | Ja |
| baseUrl | Geef de Azure Key Vault URL op. | Ja |

**Gebruikers interface voor ontwerpen gebruiken:**

Klik op **verbindingen** -> **gekoppelde services** ->  **+ Nieuw** -> zoek naar ' Azure Key Vault ':

![Azure zoeken](media/store-credentials-in-key-vault/search-akv.png)

Selecteer de ingerichte Azure Key Vault waar uw referenties worden opgeslagen. U kunt de **verbinding testen** om er zeker van te zijn dat uw Azure-verbinding geldig is. 

![Azure configureren](media/store-credentials-in-key-vault/configure-akv.png)

**JSON-voor beeld:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Referentie geheim opgeslagen in de sleutel kluis

De volgende eigenschappen worden ondersteund wanneer u een veld configureert in een gekoppelde service die naar een sleutel kluis geheim verwijst:

| Eigenschap | Description | Vereist |
|:--- |:--- |:--- |
| Type | De eigenschap type van het veld moet worden ingesteld op: **AzureKeyVaultSecret**. | Ja |
| secretName | De naam van het geheim in Azure Key Vault. | Ja |
| secretVersion | De versie van het geheim in Azure Key Vault.<br/>Als u niets opgeeft, wordt altijd de meest recente versie van het geheim gebruikt.<br/>Als deze is opgegeven, wordt de opgegeven versie weer gegeven.| Nee |
| store | Verwijst naar een Azure Key Vault gekoppelde service die u gebruikt om de referentie op te slaan. | Ja |

**Gebruikers interface voor ontwerpen gebruiken:**

Selecteer **Azure Key Vault** voor geheime velden tijdens het maken van de verbinding met uw gegevens archief/compute. Selecteer de ingerichte Azure Key Vault gekoppelde service en geef de **geheime naam**op. U kunt eventueel ook een geheime versie opgeven. 

>[!TIP]
>Voor connectors die gebruikmaken van connection string in gekoppelde service, zoals SQL Server, Blob Storage, enzovoort, kunt u kiezen om alleen het geheime veld op te slaan, bijvoorbeeld een wacht woord in azure, of om de volledige connection string op te slaan in Azure. U kunt beide opties vinden in de gebruikers interface.

![Azure-geheim configureren](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON-voor beeld: (Zie de sectie wacht woord)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
