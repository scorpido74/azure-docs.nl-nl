---
title: Referenties opslaan in Azure Key Vault
description: Meer informatie over het opslaan van referenties voor gegevensopslag die worden gebruikt in een Azure-sleutelkluis die Azure Data Factory automatisch kan ophalen tijdens runtime.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: 1418205843fefc76db4e73832736b308d0cc79a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122607"
---
# <a name="store-credential-in-azure-key-vault"></a>Referenties opslaan in Azure Key Vault

U referenties voor gegevensopslag en rekengegevens opslaan in een [Azure Key Vault.](../key-vault/key-vault-overview.md) Azure Data Factory haalt de referenties op bij het uitvoeren van een activiteit die het gegevensarchief/de gegevensopslag gebruikt.

Momenteel ondersteunen alle activiteitstypen, behalve aangepaste activiteit, deze functie. Controleer specifiek voor de connectorconfiguratie de sectie 'gekoppelde serviceeigenschappen' in [elk connectoronderwerp](copy-activity-overview.md#supported-data-stores-and-formats) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Deze functie is afhankelijk van de door de gegevensfabriek beheerde identiteit. Lees hoe het werkt [vanuit Beheerde identiteit voor gegevensfabriek](data-factory-service-identity.md) en zorg ervoor dat uw gegevensfabriek een bijbehorende gegevensfabriek heeft.

## <a name="steps"></a>Stappen

Als u wilt verwijzen naar een referentie die is opgeslagen in Azure Key Vault, moet u het:

1. **Haal de beheerde identiteit** van de gegevensfabriek op door de waarde van 'Managed Identity Object ID' te kopiëren die samen met uw fabriek is gegenereerd. Als u de gebruikersinterface voor ADF-auteurs gebruikt, wordt de id van beheerde identiteitsobject weergegeven in het venster voor het maken van gekoppelde servicemet azure Key Vault. u het ook ophalen uit azure portal, verwijzen naar [Gegevens fabriek beheerde identiteit ophalen](data-factory-service-identity.md#retrieve-managed-identity).
2. **Geef de beheerde identiteit toegang tot uw Azure Key Vault.** In uw sleutelkluis -> Toegangsbeleid -> Nieuwe > zoeken in deze beheerde identiteit deze beheerde identiteit doorzoeken om **vervolgkeuzestop Met** machtigingen voor het krijgen in geheime machtigingen te verlenen. Het maakt het mogelijk deze aangewezen fabriek om toegang te krijgen tot geheim in de sleutel kluis.
3. **Maak een gekoppelde service die naar uw Azure Key Vault wijst.** Raadpleeg [de gekoppelde Azure Key Vault-service](#azure-key-vault-linked-service).
4. **Maak data store linked service, waarin wordt verwezen naar het bijbehorende geheim dat is opgeslagen in key vault.** Raadpleeg [het referentiegeheim dat is opgeslagen in de sleutelkluis.](#reference-secret-stored-in-key-vault)

## <a name="azure-key-vault-linked-service"></a>Gekoppelde Azure Key Vault-service

De volgende eigenschappen worden ondersteund voor azure key vault-gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet zijn ingesteld op: **AzureKeyVault**. | Ja |
| baseUrl (baseUrl) | Geef de URL van Azure Key Vault op. | Ja |

**UI van het ontwerpen van het ontwerp:**

Klik **op Verbindingen** -> **Gekoppelde Services** -> **+Nieuw->** zoeken naar "Azure Key Vault":

![AKV zoeken](media/store-credentials-in-key-vault/search-akv.png)

Selecteer de ingerichte Azure Key Vault waar uw referenties zijn opgeslagen. U **TestConnection** doen om te zorgen dat uw AKV-verbinding geldig is. 

![AKV configureren](media/store-credentials-in-key-vault/configure-akv.png)

**Json voorbeeld:**

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

## <a name="reference-secret-stored-in-key-vault"></a>Verwijzen naar het geheim dat in Azure Key Vault is opgeslagen

De volgende eigenschappen worden ondersteund wanneer u een veld configureert in gekoppelde service waarin wordt verwezen naar een sleutelkluisgeheim:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van het veld moet zijn ingesteld op: **AzureKeyVaultSecret**. | Ja |
| secretName | De naam van het geheim in Azure Key Vault. | Ja |
| secretVersie | De versie van het geheim in Azure Key Vault.<br/>Indien niet opgegeven, gebruikt het altijd de nieuwste versie van het geheim.<br/>Indien gespecificeerd, dan blijft het aan de gegeven versie plakken.| Nee |
| winkel | Verwijst naar een Azure Key Vault-gekoppelde service die u gebruikt om de referentie op te slaan. | Ja |

**UI van het ontwerpen van het ontwerp:**

Selecteer **Azure Key Vault** voor geheime velden terwijl u de verbinding maakt met uw gegevensarchief/gegevensbestand. Selecteer de ingerichte Azure Key Vault Linked Service en geef de **geheime naam**op. U optioneel ook een geheime versie leveren. 

>[!TIP]
>Voor connectors die verbindingstekenreeks in gekoppelde service gebruiken, zoals SQL Server, Blob-opslag, enz., u ervoor kiezen om alleen het geheime veld op te slaan, bijvoorbeeld wachtwoord in AKV, of om de volledige verbindingstekenreeks in AKV op te slaan. U beide opties vinden op de gebruikersinterface.

![AKV-geheim configureren](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON voorbeeld: (zie de sectie "wachtwoord")**

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
Zie [ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensarchieven die worden ondersteund als bronnen en sinks door de kopieeractiviteit in Azure Data Factory.
