---
title: Beheerde identiteit voor Data Factory
description: Meer informatie over beheerde identiteit voor Azure Data Factory.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: 45699680ad2003c034bce588857f8b102a0b6d26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261123"
---
# <a name="managed-identity-for-data-factory"></a>Beheerde identiteit voor Data Factory

Dit artikel helpt u te begrijpen wat wordt beheerd identiteit voor Data Factory (voorheen bekend als Managed Service Identity / MSI) en hoe het werkt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht

Bij het maken van een gegevensfabriek kan een beheerde identiteit worden gecreëerd, samen met het maken van fabrieken. De beheerde identiteit is een beheerde toepassing die is geregistreerd bij Azure Active Directory en vertegenwoordigt deze specifieke gegevensfabriek.

Beheerde identiteit voor Data Factory komt ten goede aan de volgende functies:

- [Bewaar referenties in Azure Key Vault,](store-credentials-in-key-vault.md)in welk geval de beheerde identiteit van de gegevensfabriek wordt gebruikt voor Azure Key Vault-verificatie.
- Connectors zoals [Azure Blob-opslag,](connector-azure-blob-storage.md) [Azure Data Lake Storage Gen1,](connector-azure-data-lake-store.md) [Azure Data Lake Storage Gen2,](connector-azure-data-lake-storage.md) [Azure SQL Database](connector-azure-sql-database.md)en Azure SQL [Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Webactiviteit](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Beheerde identiteit genereren

Beheerde identiteit voor Data Factory wordt als volgt gegenereerd:

- Bij het maken van gegevensfabriek via **Azure portal of PowerShell**wordt beheerde identiteit altijd automatisch gemaakt.
- Bij het maken van gegevensfabriek via **SDK**wordt de beheerde identiteit alleen gemaakt als u 'Identiteit = nieuwe FactoryIdentity()' opgeeft in het fabrieksobject voor creatie. Zie voorbeeld in [.NET quickstart - gegevensfabriek maken](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Bij het maken van gegevensfabriek via **REST API**wordt de beheerde identiteit alleen gemaakt als u de sectie 'identiteit' opgeeft in de aanvraaginstantie. Zie voorbeeld in [REST quickstart - datafactory maken](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Als u merkt dat uw gegevensfabriek geen beheerde identiteit heeft die is gekoppeld aan de [instructies voor beheerde identiteits ophalen,](#retrieve-managed-identity) u er expliciet een genereren door de gegevensfabriek programmatisch bij te werken met de initiator van de identiteit:

- [Beheerde identiteit genereren met PowerShell](#generate-managed-identity-using-powershell)
- [Beheerde identiteit genereren met REST API](#generate-managed-identity-using-rest-api)
- [Beheerde identiteit genereren met een Azure Resource Manager-sjabloon](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Beheerde identiteit genereren met SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Beheerde identiteit kan niet worden gewijzigd. Het bijwerken van een gegevensfabriek die al een beheerde identiteit heeft, heeft geen impact, de beheerde identiteit blijft ongewijzigd.
>- Als u een gegevensfabriek bijwerkt die al een beheerde identiteit heeft zonder de parameter "identiteit" op te geven in het fabrieksobject of zonder de sectie 'identiteit' op te geven in de aanvraaginstantie REST, krijgt u een foutmelding.
>- Wanneer u een gegevensfabriek verwijdert, wordt de bijbehorende beheerde identiteit verwijderd.

### <a name="generate-managed-identity-using-powershell"></a>Beheerde identiteit genereren met PowerShell

Call **Set-AzDataFactoryV2** opdracht opnieuw, dan zie je "Identiteit" velden worden nieuw gegenereerd:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Beheerde identiteit genereren met REST API

Call below API with "identity" sectie in de aanvraaginstantie:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Aanvraag:** voeg "identiteit" toe: { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Antwoord**: beheerde identiteit wordt automatisch gemaakt en de sectie 'identiteit' wordt dienovereenkomstig ingevuld.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Beheerde identiteit genereren met een Azure Resource Manager-sjabloon

**Sjabloon**: voeg "identiteit" toe: { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Beheerde identiteit genereren met SDK

Bel de gegevensfabriek create_or_update functie met Identity=new FactoryIdentity(). Voorbeeldcode met .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Beheerde identiteit ophalen

U de beheerde identiteit ophalen uit azure portal of programmatisch. De volgende secties tonen enkele voorbeelden.

>[!TIP]
> Als u de beheerde identiteit niet ziet, [genereert u beheerde identiteit](#generate-managed-identity) door uw fabriek bij te werken.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Beheerde identiteit ophalen met Azure-portal

U de beheerde identiteitsgegevens vinden van Azure portal -> uw gegevensfabriek -> Eigenschappen.

- Beheerde identiteitsobject-id
- Beheerde identiteittenant
- Beheerde identiteittoepassings-id

De beheerde identiteitsgegevens worden ook weergegeven wanneer u gekoppelde service maakt die beheerde identiteitsverificatie ondersteunt, zoals Azure Blob, Azure Data Lake Storage, Azure Key Vault, enz.

Wanneer u toestemming verleent, gebruikt u object-id of de naam van de gegevensfabriek (als beheerde identiteitsnaam) om deze identiteit te vinden.

### <a name="retrieve-managed-identity-using-powershell"></a>Beheerde identiteit ophalen met PowerShell

De beheerde identiteits-id en tenant-id worden als volgt geretourneerd wanneer u een specifieke gegevensfabriek ontvangt. Gebruik de **PrincipalId** om toegang te verlenen:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

U de toepassings-id verkrijgen door bovenstaande hoofd-id te kopiëren en vervolgens onder de opdracht Azure Active Directory met hoofd-id als parameter uit te voeren.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende onderwerpen die introduceren wanneer en hoe de beheerde identiteit van de datafabriek te gebruiken:

- [Referenties opslaan in Azure Key Vault](store-credentials-in-key-vault.md)
- [Gegevens kopiëren van/naar Azure Data Lake Store met beheerde identiteiten voor Azure-bronverificatie](connector-azure-data-lake-store.md)

Zie [Overzicht beheerde identiteiten voor Azure-resources](/azure/active-directory/managed-identities-azure-resources/overview) voor meer achtergrondinformatie over beheerde identiteiten voor Azure-resources, waarop de beheerde identiteit van de gegevensfabriek is gebaseerd. 
