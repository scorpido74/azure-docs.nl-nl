---
title: De levens cyclus van Azure Storage beheren
description: Meer informatie over het maken van levenscyclus beleids regels voor het overstappen van verouderde gegevens van dynamische naar coole en archief lagen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 255e440586af2a5c9115023f45fbf02e25c57ab6
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692129"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>De levenscyclus van Azure Blob-opslag beheren

Gegevens sets hebben een unieke levens cyclus. In de loop van de levens cyclus hebben mensen vaak toegang tot bepaalde gegevens. Maar de nood zaak van de toegang tot de gegevens valt aanzienlijk. Sommige gegevens blijven niet actief in de Cloud en worden zelden gebruikt wanneer deze zijn opgeslagen. Sommige gegevens verlopen dagen of maanden na het maken, terwijl andere gegevens sets actief worden gelezen en gewijzigd gedurende hun levens duur. Levenscyclus beheer van Azure Blob-opslag biedt een rijk, op regels gebaseerd beleid voor GPv2-en Blob Storage-accounts. Gebruik het beleid om uw gegevens over te zetten naar de juiste toegangs lagen of verloopt aan het einde van de levens cyclus van de gegevens.

Met het levenscyclus beheer beleid kunt u:

- Overgangs-blobs naar een koele opslaglaag (warm naar koud, Hot-to-Archive of koud naar archief) om te optimaliseren voor prestaties en kosten
- Blobs verwijderen aan het einde van de levens cycli
- Regels definiëren die één keer per dag moeten worden uitgevoerd op het niveau van het opslag account
- Regels Toep assen op containers of een subset van blobs (met behulp van naam voorvoegsels of [BLOB-index Tags](storage-manage-find-blobs.md) als filters)

Houd rekening met een scenario waarbij gegevens veelvuldig toegankelijk zijn tijdens de vroege fase van de levens cyclus, maar af en toe slechts af en toe na twee weken. Na de eerste maand wordt de gegevensset zelden geopend. In dit scenario is hot Storage het beste tijdens de eerste fasen. Cool Storage is het meest geschikt voor incidentele toegang. Archief opslag is de beste laag optie nadat de gegevens gedurende een maand zijn verouderd. Door opslag lagen aan te passen ten opzichte van de leeftijd van gegevens, kunt u de minst dure opslag opties voor uw behoeften ontwerpen. Voor deze overgang zijn levenscyclus beheer beleids regels beschikbaar om verouderde gegevens naar koele lagen te verplaatsen.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Ondersteuning voor opslag accounts

Het levenscyclus beheer beleid is beschikbaar met Algemeen v2-accounts (GPv2), Blob Storage-accounts en Premium Block Blob Storage-accounts. In de Azure Portal kunt u een bestaand Algemeen-account (GPv1) upgraden naar een GPv2-account. Zie [overzicht van Azure Storage-account](../common/storage-account-overview.md)voor meer informatie over opslag accounts.  

## <a name="pricing"></a>Prijzen

De functie levenscyclus beheer is gratis. Klanten betalen de normale bewerkings kosten voor de [lijst-blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) en stellen API-aanroepen voor de [BLOB-laag](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) in. De verwijderings bewerking is gratis. Zie [prijzen voor blok-BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over prijzen.

## <a name="regional-availability"></a>Regionale beschikbaarheid

De functie levenscyclus beheer is beschikbaar in alle Azure-regio's.

## <a name="add-or-remove-a-policy"></a>Een beleid toevoegen of verwijderen

U kunt een beleid toevoegen, bewerken of verwijderen met een van de volgende methoden:

* [Azure-portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure-CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST-API’s](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Een beleid kan volledig worden gelezen of geschreven. Gedeeltelijke updates worden niet ondersteund. 

> [!NOTE]
> Als u firewall regels inschakelt voor uw opslag account, kunnen aanvragen voor levenscyclus beheer worden geblokkeerd. U kunt deze aanvragen deblokkeren door uitzonde ringen op te geven voor vertrouwde micro soft-Services. Zie de sectie uitzonde ringen in [firewalls en virtuele netwerken configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)voor meer informatie.

In dit artikel wordt uitgelegd hoe u beleid beheert met behulp van de portal-en Power shell-methoden.  

# <a name="portal"></a>[Portal](#tab/azure-portal)

Er zijn twee manieren om een beleid toe te voegen via de Azure Portal. 

* [Lijst weergave Azure Portal](#azure-portal-list-view)
* [Code weergave Azure Portal](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Lijst weergave Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek en selecteer uw opslag account in de Azure Portal. 

3. Selecteer **levenscyclus beheer** onder **BLOB-service**om uw regels te bekijken of te wijzigen.

4. Selecteer het tabblad **lijst weergave** .

5. Selecteer **regel toevoegen** en vul vervolgens de formulier velden voor **actie sets** in. In het volgende voor beeld worden blobs verplaatst naar koude opslag als deze 30 dagen niet zijn gewijzigd.

   ![Pagina levenscyclus beheer actie instellen in Azure Portal](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Selecteer **filter sets** om een optioneel filter toe te voegen. Selecteer vervolgens **Bladeren** om een container en de map op te geven die u wilt filteren.

   ![Pagina met levenscyclus beheer filters instellen in Azure Portal](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Selecteer **controleren en toevoegen** om de beleids instellingen te controleren.

9. Selecteer **toevoegen** om het nieuwe beleid toe te voegen.

#### <a name="azure-portal-code-view"></a>Code weergave Azure Portal
1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek en selecteer uw opslag account in de Azure Portal.

3. Selecteer onder **BLOB**-service **levenscyclus beheer** om uw beleid weer te geven of te wijzigen.

4. De volgende JSON is een voor beeld van een beleid dat op het tabblad **code weergave** kan worden geplakt.

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

6. Zie de secties [beleid](#policy) en [regels](#rules) voor meer informatie over dit JSON-voor beeld.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Het volgende Power shell-script kan worden gebruikt om een beleid toe te voegen aan uw opslag account. De `$rgname` variabele moet worden geïnitialiseerd met de naam van de resource groep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslag account.

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

U kunt levenscyclus beheer definiëren met behulp van Azure Resource Manager sjablonen. Hier volgt een voorbeeld sjabloon voor het implementeren van een RA-GRS GPv2-opslag account met een levenscyclus beheer beleid.

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

Een levenscyclus beheer beleid is een verzameling regels in een JSON-document:

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

| Parameternaam | Parameter type | Opmerkingen |
|----------------|----------------|-------|
| `rules`        | Een matrix van regel objecten | Er is ten minste één regel vereist in een beleid. U kunt Maxi maal 100 regels definiëren in een beleid.|

Elke regel in het beleid heeft verschillende para meters:

| Parameternaam | Parameter type | Opmerkingen | Vereist |
|----------------|----------------|-------|----------|
| `name`         | Tekenreeks |De naam van een regel kan Maxi maal 256 alfanumerieke tekens bevatten. De regel naam is hoofdletter gevoelig.  Het moet uniek zijn binnen een beleid. | True |
| `enabled`      | Boolean-waarde | Een optionele Booleaanse waarde waarmee een regel tijdelijk kan worden uitgeschakeld. De standaard waarde is True als deze niet is ingesteld. | False | 
| `type`         | Een Enum-waarde | Het huidige geldige type is `Lifecycle`. | True |
| `definition`   | Een object dat de levenscyclus regel definieert | Elke definitie bestaat uit een set filters en een Actieset. | True |

## <a name="rules"></a>Regels

Elke regel definitie bevat een set filters en een Actieset. Met de [Filterset](#rule-filters) worden regel acties beperkt tot een bepaalde set objecten binnen een container of object namen. Met de [Actieset](#rule-actions) worden de lagen of verwijder acties toegepast op de gefilterde set met objecten.

### <a name="sample-rule"></a>Voorbeeld regel

Met de volgende voorbeeld regel filtert u het account om de acties uit te voeren `container1` op objecten die `foo`zich in en beginnen met.  

>[!NOTE]
>Levenscyclus beheer ondersteunt alleen het type blok-blob.  

- Laag-BLOB naar cool laag 30 dagen na laatste wijziging
- Laag-BLOB naar archief laag 90 dagen na laatste wijziging
- BLOB 2.555 dagen verwijderen (zeven jaar) na laatste wijziging
- BLOB-moment opnamen 90 dagen na het maken van de moment opname verwijderen

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

### <a name="rule-filters"></a>Regel filters

Filters beperken regel acties voor een subset van blobs binnen het opslag account. Als er meer dan één filter is gedefinieerd, is `AND` dit een logische uitvoering op alle filters.

Filters omvatten:

| Bestandsnaam | Filtertype | Opmerkingen | Is vereist |
|-------------|-------------|-------|-------------|
| blobTypes   | Een matrix met vooraf gedefinieerde Enum-waarden. | De huidige versie ondersteunt `blockBlob`. | Ja |
| prefixMatch | Een matrix met teken reeksen voor voor voegsels die moeten worden vergeleken. Elke regel kan Maxi maal 10 voor voegsels definiëren. Een voorvoegsel teken reeks moet beginnen met een container naam. Als u bijvoorbeeld wilt zoeken naar alle blobs onder `https://myaccount.blob.core.windows.net/container1/foo/...` een regel, is `container1/foo`de prefixMatch. | Als u prefixMatch niet definieert, is de regel van toepassing op alle blobs in het opslag account.  | Nee |
| blobIndexMatch | Een matrix met woordenlijst waarden die bestaan uit BLOB-index Tags sleutel en waarden die moeten worden vergeleken. Elke regel kan Maxi maal 10 BLOB-index code voorwaarde definiëren. Als u bijvoorbeeld alle blobs wilt vergelijken met `Project = Contoso` onder `https://myaccount.blob.core.windows.net/` voor een regel, is `{"name": "Project","op": "==","value": "Contoso"}`de blobIndexMatch. | Als u blobIndexMatch niet definieert, is de regel van toepassing op alle blobs in het opslag account. | Nee |

> [!NOTE]
> BLOB-index bevindt zich in de open bare preview en is beschikbaar in de regio's **Frankrijk-centraal** en **Frankrijk-Zuid** . Zie voor meer informatie over deze functie, samen met bekende problemen en beperkingen, [gegevens beheren en zoeken op Azure Blob Storage met Blob-index (preview)](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Regel acties

Acties worden toegepast op de gefilterde blobs wanneer wordt voldaan aan de voor waarde run.

Levenscyclus beheer ondersteunt het trapsgewijs en verwijderen van blobs en het verwijderen van BLOB-moment opnamen. Definieer ten minste één actie voor elke regel op blobs of BLOB-moment opnamen.

| Actie        | Basis-BLOB                                   | Momentopname      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Ondersteuning voor blobs momenteel op warme laag         | Niet ondersteund |
| tierToArchive | Ondersteuning voor blobs momenteel op warme of koud niveau | Niet ondersteund |
| delete        | Ondersteund                                   | Ondersteund     |

>[!NOTE]
>Als u meer dan één actie op dezelfde BLOB definieert, past levenscyclus beheer de minst dure actie toe op de blob. Actie `delete` is bijvoorbeeld goed koper dan actie `tierToArchive`. Actie `tierToArchive` is goed koper dan `tierToCool`actie.

De uitvoerings voorwaarden zijn gebaseerd op leeftijd. Basis-blobs gebruiken het tijdstip waarop het laatst is gewijzigd voor het bijhouden van leeftijd en de BLOB-moment opnamen maken gebruik van de moment opname voor het bijhouden van leeftijd.

| Voor waarde voor actie uitvoeren             | Waarde voor waarde                          | Beschrijving                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Geheel getal dat de leeftijd in dagen aangeeft | De voor waarde voor basis-BLOB-acties     |
| daysAfterCreationGreaterThan     | Geheel getal dat de leeftijd in dagen aangeeft | De voor waarde voor BLOB-momentopname acties |

## <a name="examples"></a>Voorbeelden

De volgende voor beelden laten zien hoe u veelvoorkomende scenario's met levenscyclus beleids regels kunt aanpakken.

### <a name="move-aging-data-to-a-cooler-tier"></a>Verouderde gegevens naar een koele laag verplaatsen

In dit voor beeld ziet u hoe blok-blobs worden `container1/foo` voorafgegaan `container2/bar`door of. Het beleid overschakelt blobs die gedurende meer dan 30 dagen niet zijn gewijzigd naar koude opslag en blobs die niet zijn gewijzigd in 90 dagen naar de laag van het archief:

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

### <a name="archive-data-after-ingest"></a>Gegevens archiveren na opname

Sommige gegevens blijven niet actief in de Cloud en worden zelden, indien ooit, geopend. Het volgende levenscyclus beleid is geconfigureerd om gegevens te archiveren zodra deze zijn opgenomen. In dit voor beeld worden blok-blobs in het opslag account `archivecontainer` in de container omgezet in een Archive-laag. De overgang wordt uitgevoerd door op blobs 0 dagen na de laatste wijzigings tijd te handelen:

> [!NOTE] 
> Het is raadzaam om uw blobs rechtstreeks naar de archief laag te uploaden om efficiënter te zijn. U kunt de header x-MS-ace's-laag gebruiken voor [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) of [putblock List](https://docs.microsoft.com/rest/api/storageservices/put-block-list) met rest versie 2018-11-09 en nieuwere of onze meest recente client bibliotheken voor Blob Storage. 

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

### <a name="expire-data-based-on-age"></a>Gegevens laten verlopen op basis van leeftijd

Sommige gegevens worden verwacht dagen of maanden na het maken. U kunt een levenscyclus beheer beleid configureren om gegevens te laten verlopen door te verwijderen op basis van de gegevens leeftijd. In het volgende voor beeld ziet u een beleid waarmee alle blok-blobs die ouder zijn dan 365 dagen worden verwijderd.

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

### <a name="delete-data-with-blob-index-tags"></a>Gegevens verwijderen met Blob-index Tags
Sommige gegevens mogen alleen worden verlopen als deze expliciet zijn gemarkeerd voor verwijdering. U kunt een levenscyclus beheer beleid configureren om gegevens te laten verlopen die zijn gelabeld met kenmerken sleutel/waarde van BLOB-index. In het volgende voor beeld ziet u een beleid waarmee alle blok-blobs worden verwijderd die zijn gelabeld met `Project = Contoso`. Zie voor meer informatie over de BLOB-index [gegevens beheren en zoeken op Azure Blob Storage met Blob-index (preview)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="delete-old-snapshots"></a>Oude moment opnamen verwijderen

Voor gegevens die regel matig worden gewijzigd en geopend gedurende de levens duur, worden moment opnamen vaak gebruikt voor het bijhouden van oudere versies van de gegevens. U kunt een beleid maken waarmee oude moment opnamen worden verwijderd op basis van de leeftijd van de moment opname. De leeftijd van de moment opname wordt bepaald door de aanmaak tijd van de moment opname te evalueren. Met deze beleids regel worden blok-BLOB-moment `activedata` opnamen binnen een container verwijderd die 90 dagen of ouder zijn nadat het maken van een moment opname is gemaakt.

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
Het platform voert het levenscyclus beleid eenmaal per dag uit. Nadat u een beleid hebt geconfigureerd, kan het tot 24 uur duren voordat bepaalde acties voor de eerste keer worden uitgevoerd.  

**Hoe lang duurt het om de acties uit te voeren als ik een bestaand beleid bijwerk?**  
Het bijgewerkte beleid duurt Maxi maal 24 uur. Zodra het beleid van kracht is, kan het tot 24 uur duren voordat de acties zijn uitgevoerd. Daarom kan het tot 48 uur duren voordat de beleids acties zijn voltooid.   

**Ik heb een gearchiveerde BLOB hand matig opnieuw gehydrateerd, hoe voorkom ik dat deze tijdelijk weer naar de laag van het archief wordt verplaatst?**  
Wanneer een BLOB wordt verplaatst van de ene toegangs laag naar een andere, verandert de tijd van de laatste wijziging niet. Als u een gearchiveerde BLOB hand matig opnieuw hebt gehydrateerd naar een warme laag, zou deze terug worden verplaatst naar de archief laag door de levenscyclus beheer-engine. Schakel de regel die van invloed is op deze BLOB tijdelijk uit om te voor komen dat deze opnieuw wordt gearchiveerd. Schakel de regel opnieuw in wanneer de BLOB veilig kan worden verplaatst naar de laag van het archief. U kunt ook de BLOB naar een andere locatie kopiëren als deze permanent of koud laag moet blijven.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het herstellen van gegevens na onbedoeld verwijderen:

- [Voorlopig verwijderen voor Azure Storage-blobs](../blobs/storage-blob-soft-delete.md)

Meer informatie over het beheren en zoeken van gegevens met Blob-index:

- [Gegevens in Azure Blob Storage beheren en zoeken met Blob-index](storage-manage-find-blobs.md)
