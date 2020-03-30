---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75468764"
---
### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Met de **gekoppelde Azure Storage-service** u een Azure-opslagaccount koppelen aan een Azure-gegevensfabriek met behulp van de **accountsleutel**, waarmee de gegevensfabriek algemene toegang heeft tot de Azure Storage. In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de gekoppelde azure-opslagservice.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |De eigenschap type moet zijn ingesteld op: **AzureStorage** |Ja |
| Connectionstring |Geef informatie op die nodig is om verbinding te maken met Azure-opslag voor de eigenschap connectionString. |Ja |

Zie [Toegangssleutels voor opslagaccount beheren](../articles/storage/common/storage-account-keys-manage.md)voor informatie over het ophalen van de toegangssleutels voor opslagaccount.

**Voorbeeld:**  

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="azure-storage-sas-linked-service"></a>Azure Storage Sas Linked Service
Een SAS (Shared Access Signature; handtekening voor gedeelde toegang) biedt gedelegeerde toegang tot bronnen in uw opslagaccount. Hiermee u een client beperkte machtigingen verlenen aan objecten in uw opslagaccount voor een bepaalde periode en met een bepaalde set machtigingen, zonder dat u uw accounttoegangssleutels hoeft te delen. De SAS is een URI die in de queryparameters alle informatie omvat die nodig is voor geverifieerde toegang tot een opslagbron. Om toegang te krijgen tot opslagbronnen met de SAS, hoeft de client alleen in de SAS door te geven aan de juiste constructor of methode. Zie Beperkte toegang tot [Azure Storage-bronnen verlenen met behulp van gedeelde toegangshandtekeningen (SAS)](../articles/storage/common/storage-sas-overview.md)voor meer informatie over SAS.

> [!IMPORTANT]
> Azure Data Factory ondersteunt nu alleen **Service SAS,** maar niet account SAS. Let op: de SAS URL die kan worden gemaakt vanuit Azure portal of Storage Explorer is een Account SAS, die niet wordt ondersteund.

> [!TIP]
> U hieronder PowerShell-opdrachten uitvoeren om een Service SAS voor uw opslagaccount te genereren (vervang de plaatshouders en geef de benodigde toestemming):`$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Met de gekoppelde Azure Storage SAS-service u een Azure Storage-account koppelen aan een Azure-gegevensfabriek met behulp van een SAS (Shared Access Signature). Het biedt de gegevensfabriek beperkte/tijdgebonden toegang tot alle/specifieke resources (blob/container) in de opslag. In de volgende tabel vindt u een beschrijving voor JSON-elementen die specifiek zijn voor de gekoppelde Azure Storage SAS-service. 

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |De eigenschap type moet zijn ingesteld op: **AzureStorageSas** |Ja |
| sasUri sasUri |Geef URI voor gedeelde toegangshandtekening op de Azure Storage-bronnen zoals blob, container of tabel.  |Ja |

**Voorbeeld:**

```json
{
    "name": "StorageSasLinkedService",
    "properties": {
        "type": "AzureStorageSas",
        "typeProperties": {
            "sasUri": "<Specify SAS URI of the Azure Storage resource>"
        }
    }
}
```

Bij het maken van een **SAS URI,** rekening houdend met het volgende:  

* Stel de juiste **permissions** lees-/schrijfmachtigingen in op objecten op basis van hoe de gekoppelde service (lezen, schrijven, lezen/schrijven) wordt gebruikt in uw gegevensfabriek.
* Stel **de vervaldatum** op de juiste manier in. Zorg ervoor dat de toegang tot Azure Storage-objecten niet verloopt binnen de actieve periode van de pijplijn.
* Uri moet worden gemaakt op het juiste container/blob- of tabelniveau op basis van de noodzaak. Met een SAS Uri naar een Azure-blob heeft de Service Gegevensfabriek toegang tot die specifieke blob. Met een SAS Uri naar een Azure blob-container kan de service Data Factory blobs in die container herhalen. Als u later meer/minder objecten moet bieden of de SAS URI moet bijwerken, moet u de gekoppelde service bijwerken met de nieuwe URI.   

