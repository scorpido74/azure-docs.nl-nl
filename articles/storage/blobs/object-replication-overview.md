---
title: Overzicht van object replicatie
titleSuffix: Azure Storage
description: Met object replicatie worden blok-blobs tussen een bron opslag account en een doel account asynchroon gekopieerd. Gebruik object replicatie om de latentie van Lees aanvragen te minimaliseren, de efficiëntie van reken workloads te verhogen, gegevens distributie te optimaliseren en de kosten te minimaliseren.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4105698198e6fb7f4e3d3526ff9590ebca4898f1
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612163"
---
# <a name="object-replication-for-block-blobs"></a>Object replicatie voor blok-blobs

Met object replicatie worden blok-blobs tussen een bron opslag account en een doel account asynchroon gekopieerd. Enkele scenario's die worden ondersteund door object replicatie zijn onder andere:

- **Latentie minimaliseren.** Object replicatie kan de latentie voor lees aanvragen verminderen door clients in staat te stellen gegevens te gebruiken uit een regio die zich in de buurt van de fysieke nabijheid bevindt.
- **Verhoog de efficiëntie voor reken werkbelastingen.** Met object replicatie kunnen reken werkbelastingen dezelfde sets van blok-blobs in verschillende regio's verwerken.
- **Gegevens distributie optimaliseren.** U kunt gegevens op één locatie verwerken of analyseren en vervolgens alleen de resultaten repliceren naar extra regio's.
- **Kosten optimaliseren.** Nadat uw gegevens zijn gerepliceerd, kunt u de kosten verlagen door deze te verplaatsen naar de laag archief met behulp van het beleid voor het beheer van levens cycli.

In het volgende diagram ziet u hoe object replicatie blok-blobs van een bron opslag account in een bepaalde regio repliceert naar doel accounts in twee verschillende regio's.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagram waarin wordt weer gegeven hoe object replicatie werkt":::

Zie [Configure object Replication](object-replication-configure.md)(Engelstalig) voor meer informatie over het configureren van object replicatie.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>Vereisten voor objectreplicatie

Voor object replicatie moeten de volgende Azure Storage-functies ook zijn ingeschakeld:

- [Change feed](storage-blob-change-feed.md): moet zijn ingeschakeld voor het bron account. Zie [de wijzigings feed inschakelen en uitschakelen](storage-blob-change-feed.md#enable-and-disable-the-change-feed)voor meer informatie over het inschakelen van de feed voor wijzigingen.
- [BLOB-versie beheer](versioning-overview.md): moet zijn ingeschakeld op zowel de bron-als de doel account. Zie [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)voor meer informatie over het inschakelen van versie beheer.

Het inschakelen van wijzigings feed-en BLOB-versie beheer kan extra kosten in beslag nemen. Raadpleeg de [pagina met Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie.

## <a name="object-replication-policies-and-rules"></a>Beleid en regels voor object replicatie

Wanneer u object replicatie configureert, maakt u een replicatie beleid dat het bron opslag account en het doel account opgeeft. Een replicatie beleid bevat een of meer regels die een bron container en een doel container opgeven en aangeven welke blok-blobs in de bron container worden gerepliceerd.

Nadat u object replicatie hebt geconfigureerd, wordt door Azure Storage de wijziging van de feed voor het bron account periodiek gecontroleerd en worden schrijf-en verwijder bewerkingen naar het doel account asynchroon gerepliceerd. Replicatie latentie is afhankelijk van de grootte van de blok-blob die wordt gerepliceerd.

> [!IMPORTANT]
> Omdat blok-BLOB-gegevens asynchroon worden gerepliceerd, worden het bron account en het doel account niet direct gesynchroniseerd. Er is momenteel geen SLA over hoe lang het duurt om gegevens naar het doel account te repliceren.

### <a name="replication-policies"></a>Replicatie beleid

Wanneer u object replicatie configureert, wordt er een replicatie beleid gemaakt voor zowel het bron account als het doel account via de Azure Storage Resource provider. Het replicatie beleid wordt geïdentificeerd door een beleids-ID. Het beleid voor de bron-en doel accounts moet dezelfde beleids-ID hebben zodat de replicatie kan worden uitgevoerd.

Een opslag account kan fungeren als het bron account voor Maxi maal twee doel accounts. De bron-en doel account kunnen zich in dezelfde regio of in verschillende regio's bevinden. Ze kunnen zich ook in verschillende abonnementen en in verschillende Azure Active Directory-tenants (Azure AD) bevinden. Er kan slechts één replicatie beleid worden gemaakt voor elk bron account/doel account paar.

### <a name="replication-rules"></a>Replicatie regels

Met replicatie regels wordt aangegeven hoe Azure Storage blobs van een bron container naar een doel container repliceert. U kunt Maxi maal 10 replicatie regels voor elk replicatie beleid opgeven. Elke replicatie regel definieert één bron-en doel container en elke bron-en doel container kan slechts in één regel worden gebruikt.

Wanneer u een replicatie regel maakt, worden standaard alleen de nieuwe blok-blobs die vervolgens aan de bron container worden toegevoegd, gekopieerd. U kunt opgeven dat zowel nieuwe als bestaande blok-blobs worden gekopieerd, of u kunt een aangepast Kopieer bereik definiëren waarmee blok-blobs die zijn gemaakt van een opgegeven tijd, worden gekopieerd.

U kunt ook een of meer filters opgeven als onderdeel van een replicatie regel voor het filteren van blok-blobs op voor voegsel. Wanneer u een voor voegsel opgeeft, worden alleen blobs die overeenkomen met het voor voegsel in de bron container gekopieerd naar de doel container.

De bron-en doel containers moeten beide bestaan voordat u ze in een regel kunt opgeven. Nadat u het replicatiebeleid hebt gemaakt, wordt de doelcontainer alleen-lezen. Pogingen om naar de doelcontainer te schrijven, mislukken met foutcode 409 (Conflict). U kunt echter de bewerking [BLOB-laag instellen](/rest/api/storageservices/set-blob-tier) aanroepen op een BLOB in de doel container om deze naar de laag archief te verplaatsen. Zie voor meer informatie over de laag archief [Azure Blob-opslag: dynamische, koude en archief toegangs lagen](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="billing"></a>Billing

Met object replicatie worden er extra kosten in rekening gebracht voor lees-en schrijf transacties op basis van de bron-en doel accounts, en worden er uitvoer kosten berekend voor de replicatie van gegevens van het bron account naar het doel account en de Lees kosten voor het verwerken van wijzigings invoer.

## <a name="next-steps"></a>Volgende stappen

- [Objectreplicatie configureren](object-replication-configure.md)
- [Ondersteuning voor feed wijzigen in Azure Blob Storage](storage-blob-change-feed.md)
- [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)
