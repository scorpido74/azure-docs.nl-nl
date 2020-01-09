---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 37917e0ed663675677f1d0452b5796120ca2694e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468764"
---
### <a name="azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service
Met de **Azure Storage gekoppelde service** kunt u een Azure Storage-account koppelen aan een azure-Data Factory met behulp van de **account sleutel**, waarmee u de Data Factory met wereld wijde toegang tot de Azure Storage. In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor Azure Storage gekoppelde service.

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type |De eigenschap type moet worden ingesteld op: **opslag** |Ja |
| connectionString |Geef de gegevens op die nodig zijn om verbinding te maken met Azure Storage voor de Connections Tring-eigenschap. |Ja |

Zie [toegangs sleutels voor opslag accounts beheren](../articles/storage/common/storage-account-keys-manage.md)voor meer informatie over het ophalen van toegangs sleutels voor opslag accounts.

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

### <a name="azure-storage-sas-linked-service"></a>Azure Storage SAS-gekoppelde service
Een Shared Access Signature (SAS) biedt gedelegeerde toegang tot resources in uw opslag account. U kunt een client beperkte machtigingen verlenen voor objecten in uw opslag account voor een bepaalde periode en met een opgegeven set machtigingen, zonder dat u de toegangs sleutels van uw account hoeft te delen. De SAS is een URI die in de query parameters omvat alle informatie die nodig is voor geverifieerde toegang tot een opslag resource. Om toegang te krijgen tot opslag bronnen met de SAS, hoeft de client alleen de SA'S door te geven aan de juiste constructor of methode. Zie voor meer informatie over SA'S [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../articles/storage/common/storage-sas-overview.md).

> [!IMPORTANT]
> Azure Data Factory ondersteunt nu alleen **service-sa's** maar geen Sa's van accounts. Let op dat de SAS-URL kan worden afgeleid van Azure Portal of Storage Explorer een account-SA'S is, wat niet wordt ondersteund.

> [!TIP]
> U kunt de volgende Power shell-opdrachten uitvoeren om een service-SAS te genereren voor uw opslag account (Vervang de plaats houders en Ken de benodigde machtiging toe): `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Met de gekoppelde Azure Storage SAS-service kunt u een Azure Storage account koppelen aan een Azure-data factory met behulp van een Shared Access Signature (SAS). Het biedt de data factory met beperkte/gebonden toegang tot alle/specifieke resources (BLOB/container) in de opslag. In de volgende tabel vindt u een beschrijving van de JSON-elementen die specifiek zijn voor Azure Storage gekoppelde SAS-service. 

| Eigenschap | Beschrijving | Verplicht |
|:--- |:--- |:--- |
| type |De eigenschap type moet worden ingesteld op: **azurestoragesas zijn** |Ja |
| sasUri |Geef Shared Access Signature-URI op voor de Azure Storage resources, zoals blob, container of table.  |Ja |

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

Wanneer u een **SAS-URI**maakt, overweeg dan het volgende:  

* Stel de juiste lees-en schrijf **machtigingen** voor objecten in op basis van de manier waarop de gekoppelde service (lezen, schrijven, lezen/schrijven) wordt gebruikt in uw Data Factory.
* Stel **verlooptijd** op de juiste wijze. Zorg ervoor dat de toegang tot Azure Storage objecten niet verloopt binnen de actieve periode van de pijp lijn.
* URI moet worden gemaakt in de juiste container/BLOB of op basis van de benodigde tabel niveau. Met een SAS-URI naar een Azure-Blob kan de Data Factory-service toegang krijgen tot die specifieke blob. Met een SAS-URI naar een Azure-Blob-container kan de Data Factory-Service blobs in die container door lopen. Als u later toegang wilt bieden tot meer of minder objecten of als u de SAS-URI wilt bijwerken, moet u de gekoppelde service bijwerken met de nieuwe URI.   

