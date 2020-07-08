---
title: Overzicht van object replicatie (preview-versie)
titleSuffix: Azure Storage
description: Met object replicatie (preview) worden blok-blobs tussen een bron opslag account en een doel account asynchroon gekopieerd. Gebruik object replicatie om de latentie van Lees aanvragen te minimaliseren, de efficiëntie van reken workloads te verhogen, gegevens distributie te optimaliseren en de kosten te minimaliseren.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2d8d4c369cef8bf996628e8c89a424f04dcdbe71
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888070"
---
# <a name="object-replication-for-block-blobs-preview"></a>Object replicatie voor blok-blobs (preview-versie)

Met object replicatie (preview) worden blok-blobs tussen een bron opslag account en een doel account asynchroon gekopieerd. Enkele scenario's die worden ondersteund door object replicatie zijn onder andere:

- **Latentie minimaliseren.** Object replicatie kan de latentie voor lees aanvragen verminderen door clients in staat te stellen gegevens te gebruiken uit een regio die zich in de buurt van de fysieke nabijheid bevindt.
- **Verhoog de efficiëntie voor reken werkbelastingen.** Met object replicatie kunnen reken werkbelastingen dezelfde sets van blok-blobs in verschillende regio's verwerken.
- **Gegevens distributie optimaliseren.** U kunt gegevens op één locatie verwerken of analyseren en vervolgens alleen de resultaten repliceren naar extra regio's.
- **Kosten optimaliseren.** Nadat uw gegevens zijn gerepliceerd, kunt u de kosten verlagen door deze te verplaatsen naar de laag archief met behulp van het beleid voor het beheer van levens cycli.

In het volgende diagram ziet u hoe object replicatie blok-blobs van een bron opslag account in een bepaalde regio repliceert naar doel accounts in twee verschillende regio's.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagram waarin wordt weer gegeven hoe object replicatie werkt":::

Zie [Configure object Replication (preview)](object-replication-configure.md)voor meer informatie over het configureren van object replicatie.

## <a name="object-replication-policies-and-rules"></a>Beleid en regels voor object replicatie

Wanneer u object replicatie configureert, maakt u een replicatie beleid dat het bron opslag account en het doel account opgeeft. Een replicatie beleid bevat een of meer regels die een bron container en een doel container opgeven en aangeven welke blok-blobs in de bron container worden gerepliceerd.

Nadat u object replicatie hebt geconfigureerd, wordt door Azure Storage de wijziging van de feed voor het bron account periodiek gecontroleerd en worden schrijf-en verwijder bewerkingen naar het doel account asynchroon gerepliceerd. Replicatie latentie is afhankelijk van de grootte van de blok-blob die wordt gerepliceerd.

> [!IMPORTANT]
> Omdat blok-BLOB-gegevens asynchroon worden gerepliceerd, worden het bron account en het doel account niet direct gesynchroniseerd. Er is momenteel geen SLA over hoe lang het duurt om gegevens naar het doel account te repliceren.

### <a name="replications-policies"></a>Replicatie beleid

Wanneer u object replicatie configureert, wordt er een replicatie beleid gemaakt voor zowel het bron account als het doel account via de Azure Storage Resource provider. Het replicatie beleid wordt geïdentificeerd door een beleids-ID. Het beleid voor de bron-en doel accounts moet dezelfde beleids-ID hebben zodat de replicatie kan worden uitgevoerd.

Een opslag account kan fungeren als het bron account voor Maxi maal twee doel accounts. En een doel account kan niet meer dan twee bron accounts bevatten. De bron- en doelaccounts kunnen zich allemaal in verschillende regio's bevinden. U kunt afzonderlijke replicatie beleidsregels configureren om gegevens naar elk van de doel accounts te repliceren.

### <a name="replication-rules"></a>Replicatie regels

Met replicatie regels wordt aangegeven hoe Azure Storage blobs van een bron container naar een doel container repliceert. U kunt Maxi maal 10 replicatie regels voor elk replicatie beleid opgeven. Elke regel definieert één bron-en doel container en elke bron-en doel container kan slechts in één regel worden gebruikt.

Wanneer u een replicatie regel maakt, worden standaard alleen de nieuwe blok-blobs die vervolgens aan de bron container worden toegevoegd, gekopieerd. U kunt ook opgeven dat zowel nieuwe als bestaande blok-blobs worden gekopieerd, of u kunt een aangepast Kopieer bereik definiëren waarmee blok-blobs die zijn gemaakt van een opgegeven tijd, worden gekopieerd.

U kunt ook een of meer filters opgeven als onderdeel van een replicatie regel voor het filteren van blok-blobs op voor voegsel. Wanneer u een voor voegsel opgeeft, worden alleen blobs die overeenkomen met het voor voegsel in de bron container gekopieerd naar de doel container.

De bron-en doel containers moeten beide bestaan voordat u ze in een regel kunt opgeven. Nadat u het replicatiebeleid hebt gemaakt, wordt de doelcontainer alleen-lezen. Pogingen om naar de doelcontainer te schrijven, mislukken met foutcode 409 (Conflict). U kunt echter de bewerking [BLOB-laag instellen](/rest/api/storageservices/set-blob-tier) aanroepen op een BLOB in de doel container om deze naar de laag archief te verplaatsen. Zie voor meer informatie over de laag archief [Azure Blob-opslag: dynamische, koude en archief toegangs lagen](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="about-the-preview"></a>Over de preview-versie

Object replicatie wordt alleen ondersteund voor v2-opslag accounts voor algemeen gebruik. Object replicatie is beschikbaar in de volgende regio's in de preview-versie:

- Frankrijk - centraal
- Canada - oost
- Canada - midden

De bron-en doel account moeten zich in een van deze regio's bevinden om object replicatie te kunnen gebruiken. De accounts kunnen zich in twee verschillende regio's bevindt.

Tijdens de preview zijn er geen extra kosten verbonden aan het repliceren van gegevens tussen opslag accounts.

> [!IMPORTANT]
> De preview van object replicatie is alleen bedoeld voor gebruik in niet-productie. Service Level Agreements (Sla's) op het niveau van de productie zijn momenteel niet beschikbaar.

### <a name="prerequisites-for-object-replication"></a>Vereisten voor objectreplicatie

Voor object replicatie moeten de volgende Azure Storage-functies zijn ingeschakeld: 
- [Feed wijzigen](storage-blob-change-feed.md)
- [Versiebeheer](versioning-overview.md)

Voordat u object replicatie configureert, moet u de vereiste onderdelen inschakelen. De wijzigings feed moet zijn ingeschakeld voor het bron account en BLOB-versie beheer moet zijn ingeschakeld op zowel het bron-als doel account. Zie de volgende artikelen voor meer informatie over het inschakelen van deze functies:

- [De wijzigings feed in-en uitschakelen](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)

Zorg ervoor dat u zich registreert voor de voor beelden van de wijzigings feed en BLOB-versie voordat u deze inschakelt.

Het inschakelen van wijzigings feed-en BLOB-versie beheer kan extra kosten in beslag nemen. Raadpleeg de [pagina met Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie.

### <a name="register-for-the-preview"></a>Registreren voor de preview-versie

U kunt registreren voor de preview-versie van object replicatie met behulp van Power shell of Azure CLI. Zorg ervoor dat u zich ook registreert voor de voor beelden van de wijzigings feed en BLOB-versie als u dat nog niet hebt gedaan.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u zich wilt registreren voor de preview met Power shell, voert u de volgende opdrachten uit:

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer de volgende opdrachten uit om u te registreren voor de preview-versie met Azure CLI:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Registratie status controleren

U kunt de status van uw registratie aanvragen controleren met behulp van Power shell of Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Voer de volgende opdrachten uit om de status van uw registratie aanvragen te controleren met behulp van Power shell:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer de volgende opdrachten uit om de status van uw registratie aanvragen te controleren met behulp van Azure CLI:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>Vragen stellen of feedback geven

Neem contact op met micro soft op om vragen te stellen over de preview van object replicatie of feedback te geven AzureStorageFeedback@microsoft.com . Ideeën en suggesties over Azure Storage worden altijd in het [Azure Storage feedback forum](https://feedback.azure.com/forums/217298-storage)gewelkomeerd.

## <a name="next-steps"></a>Volgende stappen

- [Object replicatie configureren (preview-versie)](object-replication-configure.md)
- [Ondersteuning voor feed wijzigen in Azure Blob Storage (preview-versie)](storage-blob-change-feed.md)
- [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)
