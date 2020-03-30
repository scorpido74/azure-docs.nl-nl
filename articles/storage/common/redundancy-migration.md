---
title: De manier wijzigen waarop een opslagaccount wordt gerepliceerd
titleSuffix: Azure Storage
description: Meer informatie over het wijzigen van de manier waarop gegevens in een bestaand opslagaccount worden gerepliceerd.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 5c37dbdc34138faab8adae6ad18252c18a75cad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337081"
---
# <a name="change-how-a-storage-account-is-replicated"></a>De manier wijzigen waarop een opslagaccount wordt gerepliceerd

Azure Storage slaat altijd meerdere kopieën van uw gegevens op, zodat deze worden beschermd tegen geplande en ongeplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk- of stroomuitval en enorme natuurrampen. Redundantie zorgt ervoor dat uw opslagaccount voldoet aan de [SLA (Service-Level Agreement) voor Azure Storage,](https://azure.microsoft.com/support/legal/sla/storage/) zelfs in het licht van storingen.

Azure Storage biedt de volgende typen replicatie:

- Lokaal redundante opslag (LRS)
- Zone-redundante opslag (ZRS)
- Georedundante opslag (GRS) of georedundante opslag voor leestoegang (RA-GRS)
- Geo-zoneredundante opslag (GZRS) of geread-access geozoneredundante opslag (RA-GZRS) (voorbeeld)

Zie [Redundantie](storage-redundancy.md)azure storage voor een overzicht van elk van deze opties.

## <a name="switch-between-types-of-replication"></a>Schakelen tussen typen replicatie

U een opslagaccount overschakelen van het ene type replicatie naar een ander type, maar sommige scenario's zijn eenvoudiger dan andere. Als u georeplicatie of leestoegang tot het secundaire gebied wilt toevoegen of verwijderen, u de Azure-portal, PowerShell of Azure CLI gebruiken om de replicatieinstelling bij te werken. Als u echter wilt wijzigen hoe gegevens worden gerepliceerd in de primaire regio, door over te stappen van LRS naar ZRS of vice versa, moet u een handmatige migratie uitvoeren.

In de volgende tabel vindt u een overzicht van hoe u overschakelen van elk type replicatie naar het andere:

| Schakelen | ... aan LRS | ... aan GRS/RA-GRS | ... aan ZRS | ... aan GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>... van LRS</b> | N.v.t. | Azure-portal, PowerShell of CLI gebruiken om de replicatieinstelling<sup>1</sup> te wijzigen | Een handmatige migratie uitvoeren <br /><br />Een live migratie aanvragen | Een handmatige migratie uitvoeren <br /><br /> OF <br /><br /> Schakel eerst over naar GRS/RA-GRS en vraag vervolgens een live migratie aan<sup>1</sup> |
| <b>... van GRS/RA-GRS</b> | Azure-portal, PowerShell of CLI gebruiken om de replicatieinstelling te wijzigen | N.v.t. | Een handmatige migratie uitvoeren <br /><br /> OF <br /><br /> Eerst overschakelen naar LRS en vervolgens een live migratie aanvragen | Een handmatige migratie uitvoeren <br /><br /> Een live migratie aanvragen |
| <b>... van ZRS</b> | Een handmatige migratie uitvoeren | Een handmatige migratie uitvoeren | N.v.t. | Azure-portal, PowerShell of CLI gebruiken om de replicatieinstelling<sup>1</sup> te wijzigen |
| <b>... van GZRS/RA-GZRS</b> | Een handmatige migratie uitvoeren | Een handmatige migratie uitvoeren | Azure-portal, PowerShell of CLI gebruiken om de replicatieinstelling te wijzigen | N.v.t. |

<sup>1</sup> Maakt een eenmalige uitwedloop.

> [!CAUTION]
> Als u een [failoveraccount](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance) hebt uitgevoerd voor uw (RA-)GRS- of (RA-)GZRS-account, is deze geconfigureerd als lokaal redundant in de nieuwe primaire regio. Live migratie naar ZRS of GZRS voor dergelijke LRS-accounts wordt niet ondersteund. U moet [handmatige migratie](https://docs.microsoft.com/azure/storage/common/redundancy-migration#perform-a-manual-migration-to-zrs)uitvoeren.

## <a name="change-the-replication-setting"></a>De replicatieinstelling wijzigen

U de Azure-portal, PowerShell of Azure CLI gebruiken om de replicatieinstelling voor een opslagaccount te wijzigen, zolang u de manier waarop gegevens worden gerepliceerd in het primaire gebied niet wijzigt. Als u migreert van LRS in de primaire regio naar ZRS in de primaire regio of vice versa, moet u een [handmatige migratie](#perform-a-manual-migration-to-zrs) of een [live migratie](#request-a-live-migration-to-zrs)uitvoeren.

Als u de manier waarop uw opslagaccount wordt gerepliceerd wijzigt, wordt geen tijd voor uw toepassingen vertraagd.

# <a name="portal"></a>[Portal](#tab/portal)

Voer de volgende stappen uit om de redundantieoptie voor uw opslagaccount in de Azure-portal te wijzigen:

1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer de **configuratie-instelling.**
1. De **replicatieinstelling** bijwerken.

![Schermafbeelding van de optie replicatie wijzigen in de portal](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Als u de redundantieoptie voor uw opslagaccount met PowerShell wilt `-SkuName` wijzigen, roept u de opdracht [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aan en geeft u de parameter op:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de redundantieoptie voor uw opslagaccount met Azure CLI `--sku` wilt wijzigen, roept u de opdracht voor het bijwerken van [az-opslagaccount](/cli/azure/storage/account#az-storage-account-update) aan en geeft u de parameter op:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs"></a>Een handmatige migratie naar ZRS uitvoeren

Als u wilt wijzigen hoe gegevens in uw opslagaccount worden gerepliceerd in de primaire regio, door over te stappen van LRS naar ZRS of vice versa, u ervoor kiezen om een handmatige migratie uit te voeren. Een handmatige migratie biedt meer flexibiliteit dan een livemigratie. U bepaalt de timing van een handmatige migratie, dus gebruik deze optie als u de migratie op een bepaalde datum moet voltooien.

Wanneer u een handmatige migratie uitvoert van LRS naar ZRS in de primaire regio of vice versa, kan het doelopslagaccount georedundant zijn en ook worden geconfigureerd voor leestoegang tot de secundaire regio. U bijvoorbeeld in één stap een LRS-account migreren naar een GZRS- of RA-GZRS-account.

Een handmatige migratie kan leiden tot uitvaltijd van toepassingen. Als voor uw toepassing hoge beschikbaarheid is vereist, biedt Microsoft u de mogelijkheid tot het uitvoeren van een livemigratie. Een livemigratie is een in-place migratie zonder uitvaltijd.

Met een handmatige migratie kopieert u de gegevens van uw bestaande opslagaccount naar een nieuw opslagaccount dat ZRS in de primaire regio gebruikt. Als u een handmatige migratie wilt uitvoeren, u een van de volgende opties gebruiken:

- Kopieer gegevens met behulp van een bestaand hulpprogramma zoals AzCopy, een van de Azure Storage-clientbibliotheken of een betrouwbaar hulpprogramma van derden.
- Als u bekend bent met Hadoop of HDInsight, u zowel het bronopslagaccount als het account voor doelopslag aan uw cluster koppelen. Parallelliseer vervolgens het proces voor gegevenskopie met een tool als DistCp.

## <a name="request-a-live-migration-to-zrs"></a>Een live migratie naar ZRS aanvragen

Als u uw opslagaccount wilt migreren van LRS of GRS naar ZRS in de primaire regio zonder onderbreking van de toepassing, u een live migratie van Microsoft aanvragen. Tijdens een live migratie hebt u toegang tot gegevens in uw opslagaccount en zonder verlies van duurzaamheid of beschikbaarheid. De SLA azure storage wordt tijdens het migratieproces onderhouden. Er is geen gegevensverlies in verband met een live migratie. Serviceeindpunten, toegangssleutels, gedeelde toegangshandtekeningen en andere accountopties blijven na de migratie ongewijzigd.

ZRS ondersteunt alleen v2-accounts voor algemene doeleinden, dus zorg ervoor dat u uw opslagaccount upgradet voordat u een verzoek voor een live migratie naar ZRS indient. Zie [Upgraden naar een v2-opslagaccount voor algemene doeleinden voor](storage-account-upgrade.md)meer informatie. Een opslagaccount moet gegevens bevatten die via live migratie moeten worden gemigreerd.

Live migratie wordt alleen ondersteund voor opslagaccounts die LRS- of GRS-replicatie gebruiken. Als uw account RA-GRS gebruikt, moet u eerst het replicatietype van uw account wijzigen in LRS of GRS voordat u verdergaat. Met deze tussenstap wordt het secundaire alleen-lezeneindpunt van RA-GRS vóór migratie verwijderd.

Microsoft verwerkt uw aanvraag voor livemigratie onmiddellijk, maar er is geen garantie wanneer een livemigratie wordt voltooid. Als u uw gegevens vóór een bepaalde datum naar ZRS wilt migreren, wordt u aangeraden een handmatige migratie uit te voeren. In het algemeen geldt dat hoe meer gegevens u voor uw account hebt, hoe langer het duurt om die gegevens te migreren.

U moet een handmatige migratie uitvoeren als:

- U wilt uw gegevens migreren naar een ZRS-opslagaccount dat zich in een andere regio bevindt dan het bronaccount.
- Uw opslagaccount is een blob- of blokkeerblobaccount met een premium pagina.
- U wilt gegevens migreren van ZRS naar LRS, GRS of RA-GRS.
- Uw opslagaccount bevat gegevens in de archieflaag.

U live migratie aanvragen via de [Azure Support-portal.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) Selecteer in de portal het opslagaccount dat u wilt converteren naar ZRS.

1. **Nieuwe ondersteuningsaanvraag selecteren**
2. Vul de **basisbeginselen** in op basis van uw accountgegevens. Selecteer **in** de sectie Service de optie **Opslagaccountbeheer** en de resource die u wilt converteren naar ZRS.
3. Selecteer **Volgende**.
4. Geef de volgende waarden op in de sectie **Probleem:**
    - **Ernst**: Laat de standaardwaarde als-is.
    - **Probleemtype:** **Gegevensmigratie selecteren**.
    - **Categorie**: Selecteer **Migreren naar ZRS**.
    - **Titel**: Typ een beschrijvende titel, bijvoorbeeld **ZRS-accountmigratie**.
    - **Details**: Typ aanvullende details in het vak **Details,** bijvoorbeeld, ik wil migreren naar ZRS van [LRS, GRS] in de \_ \_ regio.
5. Selecteer **Volgende**.
6. Controleer of de contactgegevens correct zijn op het blad **Contactgegevens.**
7. Selecteer **Maken**.

Een ondersteunend persoon zal contact met u opnemen en alle hulp bieden die u nodig hebt.

> [!NOTE]
> Live migratie wordt momenteel niet ondersteund voor premium bestandsshares. Alleen het handmatig kopiëren of verplaatsen van gegevens wordt momenteel ondersteund.
>
> GZRS-opslagaccounts ondersteunen momenteel de archieflaag niet. Zie [Azure Blob-opslag: hot-, cool- en archieftoegangslagen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) voor meer informatie.
>
> Beheerde schijven zijn alleen beschikbaar voor LRS en kunnen niet worden gemigreerd naar ZRS. U snapshots en afbeeldingen opslaan voor standaard SSD-beheerde schijven op standaard HDD-opslag en [kiezen tussen LRS- en ZRS-opties.](https://azure.microsoft.com/pricing/details/managed-disks/) Zie Inleiding tot door Azure [beheerde schijven voor](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets)informatie over integratie met beschikbaarheidssets.

## <a name="switch-from-zrs-classic"></a>Overschakelen van ZRS Classic

> [!IMPORTANT]
> Microsoft zal zrs classic-accounts op 31 maart 2021 deprecate en migreren. Meer details zullen worden verstrekt aan ZRS Classic klanten voor de afschaffing.
>
> Nadat ZRS algemeen beschikbaar is in een bepaalde regio, kunnen klanten geen ZRS Classic-accounts meer maken vanuit de Azure-portal in die regio. Het gebruik van Microsoft PowerShell en Azure CLI om ZRS Classic-accounts te maken is een optie totdat ZRS Classic is afgeschaft. Zie [Redundantie azure storage](storage-redundancy.md)voor informatie over waar ZRS beschikbaar is.

ZRS Classic repliceert op asynchrone gegevens in datacenters binnen één tot twee regio's. Gerepliceerde gegevens zijn mogelijk niet beschikbaar, tenzij Microsoft failover naar het secundaire initieert. Een ZRS Classic-account kan niet worden geconverteerd naar of van LRS, GRS of RA-GRS. ZRS Classic-accounts bieden ook geen ondersteuning voor statistieken of logboekregistratie.

ZRS Classic is alleen beschikbaar voor **blokblobs** in GPv1-opslagaccounts (GPv1) voor algemene doeleinden. Zie overzicht van [Azure-opslagaccounts](storage-account-overview.md)voor meer informatie over opslagaccounts.

Als u ZRS-accountgegevens handmatig wilt migreren naar of van een LRS-, GRS-, RA-GRS- of ZRS Classic-account, gebruikt u een van de volgende hulpprogramma's: AzCopy, Azure Storage Explorer, PowerShell of Azure CLI. U ook uw eigen migratieoplossing bouwen met een van de Azure Storage-clientbibliotheken.

U uw ZRS Classic-opslagaccount ook upgraden naar ZRS met behulp van de Azure-portal, PowerShell of Azure CLI in regio's waar ZRS beschikbaar is.

# <a name="portal"></a>[Portal](#tab/portal)

Als u wilt upgraden naar ZRS in de Azure-portal, navigeert u naar de **configuratie-instellingen** van het account en kiest **u Upgrade:**

![Upgrade ZRS Classic naar ZRS in de Portal](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

Als u wilt upgraden naar ZRS met PowerShell, voert u de volgende opdracht aan:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u wilt upgraden naar ZRS met Azure CLI, voert u de volgende opdracht aan:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Kosten in verband met het wijzigen van de manier waarop gegevens worden gerepliceerd

De kosten die verbonden zijn aan het wijzigen van de manier waarop gegevens worden gerepliceerd, zijn afhankelijk van uw conversiepad. Het bestellen van het goedkoopste, Azure Storage redundantieaanbod omvat LRS, ZRS, GRS, RA-GRS, GZRS en RA-GZRS.

Bijvoorbeeld, *gaande van* LRS naar een ander type replicatie zal extra kosten met zich meebrengen omdat u naar een meer geavanceerde redundantie niveau. Als u *migreert naar* GRS of RA-GRS, worden bandbreedtekosten in rekening gebracht omdat uw gegevens (in uw primaire regio) worden gerepliceerd naar uw secundaire regio op afstand. Deze kosten zijn een eenmalige kosten bij de eerste installatie. Nadat de gegevens zijn gekopieerd, zijn er geen migratiekosten meer. Zie [de pagina Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over bandbreedtekosten.

Als u uw opslagaccount migreert van GRS naar LRS, zijn er geen extra kosten, maar worden uw gerepliceerde gegevens verwijderd van de secundaire locatie.

> [!IMPORTANT]
> Als u uw opslagaccount migreert van RA-GRS naar GRS of LRS, wordt dat account 30 dagen extra gefactureerd dan de datum waarop het is geconverteerd.

## <a name="see-also"></a>Zie ook

- [Redundantie azure-opslag](storage-redundancy.md)
- [De eigenschap Laatste synchronisatietijd controleren voor een opslagaccount](last-sync-time-get.md)
- [Ontwerpen van zeer beschikbare toepassingen met behulp van georedundante opslag met leestoegang](storage-designing-ha-apps-with-ragrs.md)
