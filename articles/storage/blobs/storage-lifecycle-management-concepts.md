---
title: De levenscyclus van Azure Storage beheren
description: Meer informatie over het maken van levenscyclusbeleidsregels om verouderde gegevens van Hot naar Cool- en Archive-lagen over te brengen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 238c12baf55b525a24107a727d09588ef06a6bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598303"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>De levenscyclus van Azure Blob-opslag beheren

Gegevenssets hebben een unieke levenscyclus. Vroeg in de levenscyclus hebben mensen vaak toegang tot bepaalde gegevens. Maar de behoefte aan toegang daalt drastisch naarmate de gegevens ouder worden. Sommige gegevens blijven inactief in de cloud en worden zelden geopend nadat ze zijn opgeslagen. Sommige gegevens verlopen dagen of maanden na het maken, terwijl andere gegevenssets actief worden gelezen en gewijzigd gedurende hun levensduur. Azure Blob-opslaglevenscyclusbeheer biedt een uitgebreid, op regels gebaseerd beleid voor GPv2- en Blob-opslagaccounts. Gebruik het beleid om uw gegevens over te brengen naar de juiste toegangsniveaus of te verlopen aan het einde van de levenscyclus van de gegevens.

Met het levenscyclusbeheerbeleid u:

- Blobs overzetten naar een koelere opslaglaag (warm tot koel, warm om te archiveren of afkoelen om te archiveren) om te optimaliseren voor prestaties en kosten
- Blobs verwijderen aan het einde van hun levenscyclus
- Regels definiëren die eenmaal per dag op het opslagaccountniveau moeten worden uitgevoerd
- Regels toepassen op containers of een subset van blobs (met voorvoegsels als filters)

Overweeg een scenario waarin gegevens regelmatig toegang krijgen tijdens de vroege stadia van de levenscyclus, maar slechts af en toe na twee weken. Na de eerste maand wordt de gegevensset zelden geopend. In dit scenario is hete opslag het beste in de vroege stadia. Koele opslag is het meest geschikt voor incidentele toegang. Archiefopslag is de beste laagoptie nadat de gegevens ouder zijn dan een maand. Door opslaglagen aan te passen aan de leeftijd van gegevens, u de goedkoopste opslagopties voor uw behoeften ontwerpen. Om deze overgang te bereiken, zijn regels voor levenscyclusbeheer beschikbaar om verouderde gegevens naar koelere lagen te verplaatsen.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Ondersteuning voor opslagaccount

Het levenscyclusbeheerbeleid is beschikbaar met GPv2-accounts (General Purpose v2), Blob-opslagaccounts en Opslagaccounts voor Premium Block Blob. In de Azure-portal u een bestaand GPv1-account (General Purpose) upgraden naar een GPv2-account. Zie overzicht van [Azure-opslagaccounts](../common/storage-account-overview.md)voor meer informatie over opslagaccounts.  

## <a name="pricing"></a>Prijzen

De functie voor levenscyclusbeheer is gratis. Klanten betalen de normale bedrijfskosten voor de [lijstblobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) en Blob Tier API-aanroepen [instellen.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) De bewerking verwijderen is gratis. Zie [Blob-prijzen blokkeren](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over prijzen.

## <a name="regional-availability"></a>Regionale beschikbaarheid

De functie voor levenscyclusbeheer is beschikbaar in alle Azure-regio's.

## <a name="add-or-remove-a-policy"></a>Een beleid toevoegen of verwijderen

U een beleid toevoegen, bewerken of verwijderen met behulp van een van de volgende methoden:

* [Azure-portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure-CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API's](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Een beleid kan volledig worden gelezen of geschreven. Gedeeltelijke updates worden niet ondersteund. 

> [!NOTE]
> Als u firewallregels inschakelt voor uw opslagaccount, kunnen aanvragen voor levenscyclusbeheer worden geblokkeerd. U deze aanvragen deblokkeren door uitzonderingen op te geven voor vertrouwde Microsoft-services. Zie de sectie Uitzonderingen in [Firewalls en virtuele netwerken configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)voor meer informatie.

In dit artikel ziet u hoe u beleid beheert met behulp van de portal- en PowerShell-methoden.  

# <a name="portal"></a>[Portal](#tab/azure-portal)

Er zijn twee manieren om een beleid toe te voegen via de Azure-portal. 

* [Weergave Lijst met Azure-portal](#azure-portal-list-view)
* [Azure-portalcodeweergave](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Weergave Lijst met Azure-portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek en selecteer uw opslagaccount in de Azure-portal. 

3. Selecteer **onder Blob Service**de optie **Levenscyclusbeheer** om uw regels weer te geven of te wijzigen.

4. Selecteer het tabblad **Lijstweergave.**

5. Selecteer **Regel toevoegen** en vul vervolgens de formuliervelden **actieset in.** In het volgende voorbeeld worden blobs verplaatst naar koele opslag als ze 30 dagen niet zijn gewijzigd.

   ![Actiesetpagina voor levenscyclusbeheer in Azure-portal](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Selecteer **Filterset** om een optioneel filter toe te voegen. Selecteer vervolgens **Bladeren** om een container en map op te geven waarmee u wilt filteren.

   ![Pagina voor het filteren set van bestandenbeheer in Azure-portal](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Selecteer **Controleren + toevoegen** om de beleidsinstellingen te controleren.

9. Selecteer **Toevoegen** om het nieuwe beleid toe te voegen.

#### <a name="azure-portal-code-view"></a>Azure-portalcodeweergave
1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek en selecteer uw opslagaccount in de Azure-portal.

3. Selecteer **onder Blob Service**de optie **Levenscyclusbeheer** om uw beleid weer te geven of te wijzigen.

4. De volgende JSON is een voorbeeld van een beleid dat kan worden geplakt op het tabblad **Codeweergave.**

   ```json
   {
     "rules": [
       {
         "name": "ruleFoo",
         "enabled": true,
         "type": "Lifecycle",
         "definition": {
           "filters": {
             "blobTypes": [ "blockBlob" ],
             "prefixMatch": [ "container1/foo" ]
           },
           "actions": {
             "baseBlob": {
               "tierToCool": { "daysAfterModificationGreaterThan": 30 },
               "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
               "delete": { "daysAfterModificationGreaterThan": 2555 }
             },
             "snapshot": {
               "delete": { "daysAfterCreationGreaterThan": 90 }
             }
           }
         }
       }
     ]
   }
   ```

5. Selecteer **Opslaan**.

6. Zie de secties [Beleid](#policy) en [Regels](#rules) voor meer informatie over dit JSON-voorbeeld.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Het volgende PowerShell-script kan worden gebruikt om een beleid toe te voegen aan uw opslagaccount. De `$rgname` variabele moet worden geïnitialiseerd met de naam van uw resourcegroep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslagaccount.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[Sjabloon](#tab/template)

U levenscyclusbeheer definiëren met Azure Resource Manager-sjablonen. Hier vindt u een voorbeeldsjabloon voor het implementeren van een RA-GRS GPv2-opslagaccount met een levenscyclusbeheerbeleid.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>Beleid

Een levenscyclusbeheerbeleid is een verzameling regels in een JSON-document:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Een beleid is een verzameling regels:

| Parameternaam | Parametertype | Opmerkingen |
|----------------|----------------|-------|
| `rules`        | Een array met regelobjecten | Ten minste één regel is vereist in een beleid. U maximaal 100 regels definiëren in een beleid.|

Elke regel binnen het beleid heeft verschillende parameters:

| Parameternaam | Parametertype | Opmerkingen | Vereist |
|----------------|----------------|-------|----------|
| `name`         | Tekenreeks |Een regelnaam kan maximaal 256 alfanumerieke tekens bevatten. Regelnaam is hoofdlettergevoelig.  Het moet uniek zijn binnen een beleid. | True |
| `enabled`      | Booleaans | Een optionele booleaan om een regel tijdelijk uitgeschakeld te laten zijn. Standaardwaarde is waar als deze niet is ingesteld. | False | 
| `type`         | Een enumwaarde | Het huidige geldige `Lifecycle`type is . | True |
| `definition`   | Een object dat de levenscyclusregel definieert | Elke definitie bestaat uit een filterset en een actieset. | True |

## <a name="rules"></a>Regels

Elke regeldefinitie bevat een filterset en een actieset. De [filterset](#rule-filters) beperkt regelacties tot een bepaalde set objecten in een container of objectennamen. De [actieset](#rule-actions) past de laag toe of verwijdert acties op de gefilterde set objecten.

### <a name="sample-rule"></a>Voorbeeldregel

Met de volgende voorbeeldregel filtert u het `container1` account om `foo`de acties uit te voeren voor objecten die binnen in en begin met .  

>[!NOTE]
>Lifecycle management ondersteunt alleen blokblobtype.  

- Tier blob om laag 30 dagen na laatste wijziging af te koelen
- Tier blob naar archieflaag 90 dagen na laatste wijziging
- Blob 2555 dagen (zeven jaar) verwijderen na laatste wijziging
- Blob-momentopnamen 90 dagen na het maken van momentopnamen verwijderen

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Regelfilters

Filters beperken regelacties tot een subset van blobs in het opslagaccount. Als er meer dan één filter `AND` is gedefinieerd, wordt een logische uitvoering uitgevoerd op alle filters.

Filters omvatten:

| Bestandsnaam | Filtertype | Opmerkingen | Is vereist |
|-------------|-------------|-------|-------------|
| blobtypen   | Een array met vooraf gedefinieerde enumwaarden. | De huidige `blockBlob`release ondersteunt . | Ja |
| prefixMatch | Een reeks tekenreeksen voor voorvoegsels die overeenkomen. Elke regel kan maximaal 10 voorvoegsels definiëren. Een voorvoegseltekenreeks moet beginnen met een containernaam. Als u bijvoorbeeld alle blobs onder `https://myaccount.blob.core.windows.net/container1/foo/...` voor een regel wilt `container1/foo`matchen, is de voorvoegselMatch . | Als u prefixMatch niet definieert, is de regel van toepassing op alle blobs binnen het opslagaccount.  | Nee |

### <a name="rule-actions"></a>Regelacties

Acties worden toegepast op de gefilterde blobs wanneer aan de runvoorwaarde is voldaan.

Lifecycle management ondersteunt gelaagdheid en verwijdering van blobs en verwijdering van blobsnapshots. Definieer ten minste één actie voor elke regel op blobs of blobmomentopnamen.

| Actie        | Basisblob                                   | Momentopname      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Blobs ondersteunen die momenteel op hot tier staan         | Niet ondersteund |
| tierToArchiveer | Blobs ondersteunen die momenteel op het e-hot- of cool-laag staan | Niet ondersteund |
| delete        | Ondersteund                                   | Ondersteund     |

>[!NOTE]
>Als u meer dan één actie op dezelfde blob definieert, past levenscyclusbeheer de goedkoopste actie toe op de blob. Actie `delete` is bijvoorbeeld goedkoper `tierToArchive`dan actie. Actie `tierToArchive` is goedkoper `tierToCool`dan actie.

De run voorwaarden zijn gebaseerd op leeftijd. Basisblobs gebruiken de laatst gewijzigde tijd om de leeftijd bij te houden en blobmomentopnamen gebruiken de tijd voor het maken van momentopnamen om de leeftijd bij te houden.

| Voorwaarde voor actievoer             | Voorwaardewaarde                          | Beschrijving                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| dagenAfterModificationGreaterThan | Gehele waarde die de leeftijd in dagen aangeeft | De voorwaarde voor basisblobacties     |
| daysAfterCreationGreaterThan     | Gehele waarde die de leeftijd in dagen aangeeft | De voorwaarde voor blobmomentopnameacties |

## <a name="examples"></a>Voorbeelden

In de volgende voorbeelden wordt uitgelegd hoe u veelvoorkomende scenario's aanpakken met levenscyclusbeleidsregels.

### <a name="move-aging-data-to-a-cooler-tier"></a>Verouderde gegevens verplaatsen naar een koeler niveau

In dit voorbeeld ziet u hoe `container1/foo` blobs die vooraf zijn gekoppeld aan of `container2/bar`. De beleidsovergangen blobs die niet zijn gewijzigd in meer dan 30 dagen om opslag te koelen, en blobs niet gewijzigd in 90 dagen naar de archieflaag:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-after-ingest"></a>Archiefgegevens na inname

Sommige gegevens blijven inactief in de cloud en worden zelden of nooit geopend nadat ze zijn opgeslagen. Het volgende levenscyclusbeleid is geconfigureerd om gegevens te archiveren kort nadat deze zijn ingenomen. In dit voorbeeld worden blobs in `archivecontainer` het opslagaccount in de container naar een archieflaag overgezet. De overgang wordt bereikt door te handelen op blobs 0 dagen na de laatst gewijzigde tijd:

> [!NOTE] 
> Het wordt aanbevolen om uw blobs direct de archieflaag te uploaden om efficiënter te zijn. U de header x-ms-acess-tier gebruiken voor [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) of [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) met REST-versie 2018-11-09 en nieuwere of onze nieuwste blob-opslagclientbibliotheken. 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Gegevens verlopen op basis van leeftijd

Sommige gegevens verlopen naar verwachting dagen of maanden na het maken. U een levenscyclusbeheerbeleid configureren om gegevens te laten verlopen door verwijdering op basis van de leeftijd van gegevens. In het volgende voorbeeld wordt een beleid weergegeven dat alle blokblobs verwijdert die ouder zijn dan 365 dagen.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>Oude momentopnamen verwijderen

Voor gegevens die gedurende de levensduur regelmatig worden gewijzigd en geopend, worden momentopnamen vaak gebruikt om oudere versies van de gegevens bij te houden. U een beleid maken dat oude momentopnamen verwijdert op basis van de leeftijd van momentopnamen. De momentopnameleeftijd wordt bepaald door de tijd voor het maken van momentopnamen te evalueren. Met deze beleidsregel worden blokblobmomentopnamen in de container `activedata` verwijderd die 90 dagen of ouder zijn na het maken van een momentopname.

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>Veelgestelde vragen

**Ik heb een nieuw beleid gemaakt, waarom worden de acties niet onmiddellijk uitgevoerd?**  
Het platform voert het levenscyclusbeleid één keer per dag uit. Zodra u een beleid hebt geconfigureerd, kan het tot 24 uur duren voordat sommige acties voor de eerste keer worden uitgevoerd.  

**Hoe lang duurt het voordat de acties worden uitgevoerd als ik een bestaand beleid bijwerk?**  
Het duurt maximaal 24 uur voordat het bijgewerkte beleid in werking is getreden. Zodra het beleid van kracht is, kan het tot 24 uur duren voordat de acties worden uitgevoerd. Daarom kunnen de beleidsacties tot 48 uur in beslag nemen.   

**Ik heb een gearchiveerde blob handmatig gerehydrateerd, hoe voorkom ik dat deze tijdelijk wordt verplaatst naar de archieflaag?**  
Wanneer een blob van de ene toegangslaag naar de andere wordt verplaatst, verandert de laatste wijzigingstijd niet. Als u een gearchiveerde blob handmatig rehydrateert naar hot tier, wordt deze door de levenscyclusbeheerengine teruggezet naar de archieflaag. Schakel de regel die van invloed is op deze blob tijdelijk uit om te voorkomen dat deze opnieuw wordt gearchiveerd. Schakel de regel opnieuw in wanneer de blob veilig kan worden verplaatst naar de archieflaag. U de blob ook naar een andere locatie kopiëren als deze permanent in de warme of koele laag moet blijven.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het herstellen van gegevens na het per ongeluk verwijderen:

- [Voorlopig verwijderen voor Azure Storage-blobs](../blobs/storage-blob-soft-delete.md)
