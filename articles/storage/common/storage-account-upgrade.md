---
title: Upgraden naar een V2-opslagaccount voor algemeen gebruik
titleSuffix: Azure Storage
description: Upgrade naar v2-opslagaccounts voor algemene doeleinden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/25/2019
ms.author: tamram
ms.openlocfilehash: 9afbade408d6f95fcd3a61aa1ba65bc09c7a875b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067216"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Upgraden naar een V2-opslagaccount voor algemeen gebruik

V2-opslagaccounts voor algemene doeleinden ondersteunen de nieuwste Azure Storage-functies en bevatten alle functionaliteit van v1- en Blob-opslagaccounts voor algemeen gebruik. V2-accounts voor algemene doeleinden worden aanbevolen voor de meeste opslagscenario's. V2-accounts voor algemene doeleinden bieden de laagste capaciteitsprijzen per gigabyte voor Azure Storage en brancheconcurrerende transactieprijzen. V2-accounts voor algemene doeleinden ondersteunen standaardaccounttoegangslagen van hot of cool- en blobniveau tussen hot, cool of archiveren.

Upgraden naar een v2-opslagaccount voor algemene doeleinden van uw v1- of Blob-opslagaccounts voor algemene doeleinden is eenvoudig. U upgraden met de Azure-portal, PowerShell of Azure CLI. Er is geen downtime of risico op gegevensverlies in verband met een upgrade naar een v2-opslagaccount voor algemene doeleinden. De accountupgrade gebeurt via een eenvoudige Azure Resource Manager-bewerking die het accounttype wijzigt.

> [!IMPORTANT]
> Het upgraden van een v1- of Blob-opslagaccount voor algemene doeleinden naar v2 voor algemene doeleinden is permanent en kan niet ongedaan worden gemaakt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar uw opslagaccount.
3. Klik **in** de sectie Instellingen op **Configuratie**.
4. Klik onder **Soort account** op **Upgrade**.
5. Typ bij **Upgrade bevestigen** de naam van uw account.
6. Klik op **Upgrade** onder aan het blad.

    ![Accounttype bijwerken](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u een v1-account voor algemene doeleinden wilt upgraden naar een v2-account voor algemene doeleinden met PowerShell, werkt u PowerShell eerst bij om de nieuwste versie van de **Az.Storage-module** te gebruiken. Zie [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) (Azure PowerShell installeren en configureren) voor meer informatie over het installeren van PowerShell.

Roep vervolgens de volgende opdracht aan om het account te upgraden, waarbij de naam van uw brongroep, de naam van het opslagaccount en de gewenste accounttoegangslaag worden vervangen.

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2 -AccessTier <Hot/Cool>
```
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een v1-account voor algemene doeleinden wilt upgraden naar een v2-account voor algemene doeleinden met Azure CLI, installeert u eerst de nieuwste versie van Azure CLI. Zie [Azure CLI 2.0 installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) voor meer informatie over het installeren van de CLI.

Roep vervolgens de volgende opdracht aan om het account te upgraden, waarbij de naam van uw brongroep, de naam van het opslagaccount en de gewenste accounttoegangslaag worden vervangen.

```azurecli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2 --access-tier=<Hot/Cool>
```

---

## <a name="specify-an-access-tier-for-blob-data"></a>Een toegangslaag opgeven voor blobgegevens

V2-accounts voor algemene doeleinden ondersteunen alle Azure-opslagservices en gegevensobjecten, maar toegangslagen zijn alleen beschikbaar voor het blokkeren van blobs binnen Blob-opslag. Wanneer u een upgrade uitvoert naar een v2-opslagaccount voor algemene doeleinden, u een standaardaccounttoegangslaag van hot of cool opgeven, wat aangeeft dat de standaardlaag uw blobgegevens worden geüpload alsof de parameter afzonderlijke blobtoegangslaag niet is opgegeven.

Met blob-toegangslagen u de meest kosteneffectieve opslag kiezen op basis van uw verwachte gebruikspatronen. Blokblobs kunnen worden opgeslagen in een hete, koele of archieflagen. Zie [Azure Blob-opslaglagen: Hot, Cool en Archive storage lagen](../blobs/storage-blob-storage-tiers.md)voor meer informatie over toegangslagen.

Standaard wordt een nieuw opslagaccount gemaakt in de hot access-laag en kan een v1-opslagaccount voor algemene doeleinden worden geüpgraded naar de hot- of cool-accountlaag. Als een accounttoegangslaag niet is opgegeven bij een upgrade, wordt deze standaard geüpgraded naar hot. Als u onderzoekt welke toegangslaag u moet gebruiken voor uw upgrade, moet u rekening houden met uw huidige scenario voor gegevensgebruik. Er zijn twee typische gebruikersscenario's voor het migreren naar een v2-account voor algemene doeleinden:

* U hebt een bestaand v1-opslagaccount voor algemene doeleinden en wilt een upgrade naar een v2-opslagaccount voor algemene doeleinden evalueren, met de juiste opslagtoegangslaag voor blobgegevens.
* U hebt besloten om een v2-opslagaccount voor algemene doeleinden te gebruiken of er al een te hebben en u wilt evalueren of u de hot- of cool-opslagtoegangslaag moet gebruiken voor blobgegevens.

In beide gevallen is de eerste prioriteit het schatten van de kosten van het opslaan, openen en werken op uw gegevens die zijn opgeslagen in een v2-opslagaccount voor algemene doeleinden en deze te vergelijken met uw huidige kosten.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Het upgraden van een v1-opslagaccount naar een v2-account voor algemene doeleinden is gratis. U de gewenste accountlaag opgeven tijdens het upgradeproces. Als een accountlaag niet is opgegeven bij een upgrade, `Hot`wordt de standaardaccountlaag van het bijgewerkte account . Als u de opslagtoegangslaag na de upgrade wijzigt, kan dit echter leiden tot wijzigingen in uw factuur, dus het wordt aanbevolen om de nieuwe accountlaag op te geven tijdens de upgrade.

Alle opslagaccounts maken gebruik van een prijsmodel voor het opslaan van blobs op basis van laag van elke blob. Als u een opslagaccount gebruikt, zijn de volgende factureringsvoorwaarden van toepassing:

* **Opslagkosten**: Naast de hoeveelheid opgeslagen gegevens, variëren de kosten voor het opslaan van gegevens afhankelijk van de opslagtoegangslaag. De kosten per GB nemen af als de laag minder dynamisch ('cooler') wordt.

* **Kosten van gegevenstoegang**: de kosten voor gegevenstoegang nemen toe als de laag minder dynamisch ('cooler') wordt. Voor gegevens in de toegangslaag voor koele en archiefopslag, wordt een gegevenstoegangstoeslag per gigabyte in rekening gebracht voor reads.

* **Transactiekosten**: er gelden kosten per transactie voor alle lagen. Deze kosten nemen toe als de laag minder dynamisch wordt.

* **Kosten voor gegevensoverdracht met geo-replicatie**: deze kosten zijn alleen van toepassing op accounts waarvoor geo-replicatie is geconfigureerd, inclusief GRS en RA-GRS. Kosten voor gegevensoverdracht met geo-replicatie worden in rekening gebracht per GB.

* **Kosten voor uitgaande gegevensoverdracht**: uitgaande gegevensoverdracht (gegevens die buiten een Azure-regio worden overgedragen) worden gefactureerd voor bandbreedtegebruik per GB, net zoals bij opslagaccounts voor algemeen gebruik.

* **De opslagtoegangslaag wijzigen:** Als u de toegangslaag voor accountopslag wijzigt van koel naar heet, worden kosten in rekening gebracht die gelijk zijn aan het lezen van alle gegevens die in het opslagaccount aanwezig zijn. Als u echter de accounttoegangslaag wijzigt van hot naar cool, worden alle gegevens in de koellaag (alleen GPv2-accounts) in rekening gebracht.

> [!NOTE]
> Zie de pagina [Prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie over het prijsmodel voor opslagaccounts. Zie de pagina [Prijsinformatie voor bandbreedte](https://azure.microsoft.com/pricing/details/data-transfers/) voor meer informatie over de kosten voor uitgaande gegevensoverdracht.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Kosten voor uw huidige gebruikspatronen schatten

Als u de kosten wilt schatten van het opslaan en openen van blobgegevens in een v2-opslagaccount voor algemene doeleinden in een bepaalde laag, evalueert u uw bestaande gebruikspatroon of benadert u uw verwachte gebruikspatroon. Doorgaans zijn de volgende gegevens hiervoor van belang:

* Uw Blob-opslagverbruik, in gigabytes, waaronder:
  * Hoeveel gegevens worden opgeslagen in het opslagaccount?
  * Hoe verandert het gegevensvolume op maandbasis; worden oude gegevens voortdurend vervangen door nieuwe gegevens?

* Het primaire toegangspatroon voor uw Blob-opslaggegevens, waaronder:
  * Hoeveel gegevens worden er gelezen en naar het opslagaccount geschreven?
  * Hoeveel leesbewerkingen versus schrijfbewerkingen vinden er plaats op de gegevens in het opslagaccount?

Als u wilt bepalen wat de beste toegangslaag voor uw behoeften is, kan het handig zijn om de capaciteit van uw blobgegevens te bepalen en hoe die gegevens worden gebruikt. Dit kan het beste worden gedaan door te kijken naar de monitoring statistieken voor uw account.

### <a name="monitoring-existing-storage-accounts"></a>Bewaking van bestaande opslagaccounts

Voor het bewaken van uw bestaande opslagaccounts en het verzamelen van deze gegevens, kunt u gebruikmaken van Azure Opslaganalyse, dat logboekregistratie uitvoert en metrische gegevens biedt voor een opslagaccount. Opslaganalyse kan metrische gegevens opslaan die samengevoegde transactiestatistieken en capaciteitsgegevens bevat over serviceaanvragen voor GPv1- en GPv2-opslagaccounts en Blob Storage-accounts. Deze gegevens worden opgeslagen in bekende tabellen in hetzelfde opslagaccount.

Zie [About Storage Analytics Metrics](https://msdn.microsoft.com/library/azure/hh343258.aspx) (Metrische gegevens in opslaganalyse) en [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx) (Tabelschema van metrische gegevens in opslaganalyse) voor meer informatie

> [!NOTE]
> Blob Storage-accounts geven het service-eindpunt van de tabel alleen weer voor het opslaan en openen van de metrische gegevens voor dat account.

Voor het controleren van het opslagverbruik voor Blob Storage moet u de metrische gegevens over capaciteit inschakelen.
Als dit is ingeschakeld, worden de capaciteitsgegevens van een Blob Storage-serviceaccount dagelijks geregistreerd en vastgelegd als een tabelvermelding die naar de *$MetricsCapacityBlob*-tabel binnen hetzelfde opslagaccount wordt geschreven.

Voor het controleren van gegevenstoegangspatronen voor Blob Storage, moet u de metrische gegevens die per uur worden verzameld voor de transactie inschakelen vanaf de API. Als deze methode is ingeschakeld, worden er elk uur per-API-transacties verzameld en geregistreerd als een tabelvermelding die is naar de *$MetricsHourPrimaryTransactionsBlob*-tabel binnen hetzelfde opslagaccount wordt geschreven. De *$MetricsHourSecondaryTransactionsBlob*-tabel registreert de transacties naar het secundaire eindpunt bij gebruik van RA-GRS-opslagaccounts.

> [!NOTE]
> Als u een algemeen opslagaccount hebt waarin u pagina-blobs en virtuele-machineschijven of wachtrijen, bestanden of tabellen, hebt opgeslagen naast blok- en toevoegblobgegevens, is dit schattingsproces niet van toepassing. De capaciteitsgegevens maken geen onderscheid tussen blok-blobs en andere typen en geven geen capaciteitsgegevens voor andere gegevenstypen. Als u deze typen gebruikt, is een alternatieve methode om te kijken naar de hoeveelheden op uw meest recente factuur.

Als u een goede schatting wilt maken van uw gegevensverbruik en toegangspatroon, raden we u aan voor de metrische gegevens een retentieperiode te kiezen die een goede afspiegeling is van uw normale gebruik en dat als uitgangspunt te nemen. Een optie is de metrische gegevens zeven dagen te bewaren en de gegevens elke week te verzamelen en aan het einde van de maand te analyseren. Een andere optie is de metrische gegevens van de afgelopen 30 dagen te bewaren en deze gegevens aan het einde van deze periode van 30 dagen te verzamelen en te analyseren.

Zie Statistieken voor [opslaganalyses](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie over het in- en verzamelen en weergeven van metrische gegevens.

> [!NOTE]
> Het opslaan, openen en downloaden van analytische gegevens wordt op dezelfde manier in rekening gebracht als normale gebruikersgegevens.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Metrische gegevens over het gebruik inzetten voor het schatten van de kosten

#### <a name="capacity-costs"></a>Capaciteitskosten

De meest recente vermelding in de metrische gegevenstabel voor capaciteit *$MetricsCapacityBlob* met de rijsleutel *'data'* toont de opslagcapaciteit die wordt gebruikt door gebruikersgegevens. De meest recente vermelding in de metrische gegevenstabel voor capaciteit *$MetricsCapacityBlob* met de rijsleutel *'analytics'* toont de opslagcapaciteit die wordt gebruikt door de analyselogboeken.

Deze totale capaciteit die wordt verbruikt door zowel gebruikersgegevens en analyselogboeken (indien ingeschakeld) kunnen vervolgens worden gebruikt om de opslagkosten te schatten voor gegevens in het opslagaccount. Deze manier kan ook worden gebruikt voor het schatten van de opslagkosten in GPv1-opslagaccounts.

#### <a name="transaction-costs"></a>Transactiekosten

Het totaal van *'TotalBillableRequests'*, in alle items voor een API in de metrische gegevenstabel voor transactie, geeft het totale aantal transacties voor die bepaalde API weer. *Bijvoorbeeld*: het totale aantal *GetBlob*-transacties in een bepaalde periode kan worden berekend door het totaal te nemen van het totale aantal factureerbare aanvragen voor alle items met de rijsleutel *user;GetBlob*.

Voor het schatten van de transactiekosten van Blob Storage-accounts moet u de transacties in drie groepen opdelen omdat ze verschillend zijn geprijsd.

* Schrijftransacties zoals *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* en *'CopyBlob'*.
* Verwijdertransacties zoals *'DeleteBlob'* en *'DeleteContainer'*.
* Alle andere transacties.

Voor het schatten van de transactiekosten voor GPv1-opslagaccounts moet u alle transacties verzamelen ongeacht de bewerking/API.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Gegevenstoegang en overdrachtskosten voor geo-replicatiegegevens

Opslaganalyse biedt geen informatie over de hoeveelheid gegevens die zijn gelezen en geschreven van en naar een opslagaccount, maar deze hoeveelheid kan min of meer worden geschat door te kijken naar de metrische gegevenstabel voor transacties. Het totaal van *'TotalIngress'* in alle items voor een API in de metrische gegevenstabel voor transacties, geeft de totale hoeveelheid inkomende gegevens in bytes voor die bepaalde API weer. Op dezelfde manier geeft het totaal van *'TotalEgress'* de totale hoeveelheid uitgaande gegevens in bytes weer.

Voor het schatten van de kosten voor het openen van gegevens in Blob Storage-accounts moet u de transacties in twee groepen opdelen.

* De hoeveelheid gegevens die is opgehaald van het opslagaccount, kan worden geschat door te kijken naar het totaal van *'TotalEgress'* voor met name de *'GetBlob'*- en *'CopyBlob'*-bewerkingen.

* De hoeveelheid gegevens die wordt geschreven naar het opslagaccount kan worden geschat door te kijken naar het totaal van *'TotalIngress'* voor met name de *'PutBlob'*-, *'PutBlock'*-, *'CopyBlob'*- en *'AppendBlock'*-bewerkingen.

De overdrachtskosten van geo-replicatiegegevens voor Blob Storage-accounts kan ook worden berekend met behulp van de schatting voor de hoeveelheid gegevens die wordt geschreven bij gebruik van een GRS- of RA-GRS-opslagaccount.

> [!NOTE]
> Voor een meer gedetailleerd voorbeeld over het berekenen van de kosten voor het gebruik van de hot of cool storage access tier, neem een kijkje op de FAQ getiteld *'Wat zijn Hot en Cool toegangslagen en hoe moet ik bepalen welke te gebruiken?'* op de [pagina met prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="next-steps"></a>Volgende stappen

* [Een opslagaccount maken](storage-account-create.md)
