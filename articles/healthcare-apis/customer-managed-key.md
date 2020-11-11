---
title: Door de klant beheerde sleutels configureren voor Azure API for FHIR
description: De functie voor het meenemen van uw eigen sleutel wordt ondersteund in Azure API for FHIR via Cosmos DB
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 05c208ba3c9005d38b8924037748764f8d112e3a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398178"
---
# <a name="configure-customer-managed-keys"></a>Door de klant beheerde sleutels configureren

Wanneer u een nieuwe Azure API for FHIR-account maakt, worden uw gegevens standaard versleuteld met door Microsoft beheerde sleutels. U kunt nu een tweede laag versleuteling voor de gegevens toevoegen met behulp van uw eigen sleutel die u zelf kiest en beheert.

In Azure wordt dit doorgaans gerealiseerd met behulp van een versleutelingssleutel in de Azure Key Vault van de klant. Azure SQL, Azure Storage en Cosmos DB zijn enkele voorbeelden die deze mogelijkheid bieden. Azure API for FHIR maakt gebruik van deze ondersteuning van Cosmos DB. Wanneer u een account maakt, hebt u de optie om een Azure Key Vault-sleutel-URI op te geven. Deze sleutel wordt doorgegeven aan Cosmos DB wanneer het DB-account is ingericht. Wanneer er een FHIR-aanvraag wordt gedaan, haalt Cosmos DB uw sleutel op en gebruikt deze om de gegevens te versleutelen/ontsleutelen. Om aan de slag te gaan, kunt u de volgende koppelingen raadplegen:

- [De Azure Cosmos DB-resourceprovider registreren voor uw Azure-abonnement](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Uw Azure Key Vault-exemplaar configureren](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
-  [Een toegangsbeleid toevoegen aan uw Azure Key Vault-exemplaar](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Een sleutel genereren in Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

Nadat u uw Azure-API for FHIR-account hebt gemaakt op Azure Portal, ziet u een configuratieoptie 'Gegevensversleuteling' onder de 'Database-instellingen' op het tabblad 'aanvullende instellingen'. Standaard wordt de optie voor de door de service beheerde sleutel gekozen. U kunt hier uw Azure Key Vault-sleutel opgeven door de optie 'Door de klant beheerde sleutel' te selecteren. U kunt hier de gekopieerde sleutel-URI invoeren.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Azure API for FHIR maken":::

U kunt ook uw sleutel kiezen uit de Sleutelkiezer:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Sleutelkiezer":::

Voor bestaande FHIR-accounts kunt u de sleutelversleutelingskeuze (door service of door de klant beheerde sleutel) bekijken in de blade 'Data base', zoals hieronder wordt weergegeven. De configuratieoptie kan niet worden gewijzigd wanneer deze eenmaal is gekozen. U kunt uw sleutel echter wel wijzigen en bijwerken.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Database":::

Daarnaast kunt u een nieuwe versie van de opgegeven sleutel maken, waarna uw gegevens worden versleuteld met de nieuwe versie zonder onderbreking van de service. U kunt ook toegang tot de sleutel verwijderen om de toegang tot de gegevens te verwijderen.