---
title: Azure Data Lake Storage Gen1 migratie over verschillende regio's | Microsoft Documenten
description: Meer informatie over migratie tussen regio's voor Azure Data Lake Storage Gen1.
services: data-lake-store
documentationcenter: ''
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: 0bf0843314f38c0de28820c82e95b7921297bf40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60518471"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Azure Data Lake Storage Gen1 migreren naar verschillende regio's

Aangezien Azure Data Lake Storage Gen1 beschikbaar komt in nieuwe regio's, u ervoor kiezen om een eenmalige migratie uit te voeren om te profiteren van de nieuwe regio. Meer informatie over wat u moet overwegen wanneer u de migratie plant en voltooit.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Vandaag nog uw gratis Azure-account maken](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie.
* **Een Data Lake Storage Gen1-account in twee verschillende regio's**. Zie [Aan de slag met Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)voor meer informatie.
* **Azure-gegevensfabriek**. Zie voor meer informatie [Inleiding tot Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Overwegingen bij migraties

Identificeer eerst de migratiestrategie die het beste werkt voor uw toepassing die gegevens schrijft, leest of verwerkt in Data Lake Storage Gen1. Wanneer u een strategie kiest, moet u rekening houden met de beschikbaarheidsvereisten van uw toepassing en de downtime die optreedt tijdens een migratie. Uw eenvoudigste aanpak kan bijvoorbeeld zijn om het cloudmigratiemodel 'lift-and-shift'.For example, your simplest approach might use the "lift-and-shift" cloud migration model. In deze benadering pauzeert u de toepassing in uw bestaande regio terwijl al uw gegevens naar de nieuwe regio worden gekopieerd. Wanneer het kopieerproces is voltooid, hervat u uw aanvraag in de nieuwe regio en verwijdert u het oude Data Lake Storage Gen1-account. Downtime tijdens de migratie is vereist.

Om downtime te verminderen, u meteen beginnen met het opnemen van nieuwe gegevens in de nieuwe regio. Wanneer u over de minimale gegevens beschikt die nodig zijn, voert u uw toepassing uit in de nieuwe regio. Op de achtergrond u oudere gegevens van het bestaande Data Lake Storage Gen1-account blijven kopiëren naar het nieuwe Data Lake Storage Gen1-account in de nieuwe regio. Door deze aanpak te gebruiken, u de overstap maken naar de nieuwe regio met weinig downtime. Wanneer alle oudere gegevens zijn gekopieerd, verwijdert u het oude Data Lake Storage Gen1-account.

Andere belangrijke details om rekening mee te houden bij het plannen van uw migratie zijn:

* **Gegevensvolume**. Het volume van de gegevens (in gigabytes, het aantal bestanden en mappen, enzovoort) is van invloed op de tijd en middelen die u nodig hebt voor de migratie.

* **Gegevens Lake Storage Gen1 accountnaam**. De nieuwe accountnaam in de nieuwe regio moet wereldwijd uniek zijn. De naam van uw oude Data Lake Storage Gen1-account in Oost-VS 2 is bijvoorbeeld mogelijk contosoeastus2.azuredatalakestore.net. U uw nieuwe Data Lake Storage Gen1-account in Noord-EU-contosonortheu.azuredatalakestore.net.

* **Gereedschap**. We raden u aan de [kopieeractiviteit van Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) te gebruiken om Gegevens Lake Storage Gen1-bestanden te kopiëren. Data Factory ondersteunt dataverkeer met hoge prestaties en betrouwbaarheid. Houd er rekening mee dat Data Factory alleen de maphiërarchie en inhoud van de bestanden kopieert. U moet handmatig alle toegangscontrolelijsten (ACL's) die u in het oude account gebruikt, handmatig toepassen op het nieuwe account. Zie de handleiding Voor de [prestaties en afstemming van de kopieeractiviteit](../data-factory/copy-activity-performance.md)voor meer informatie, inclusief prestatiedoelen voor de beste scenario's. Als u wilt dat gegevens sneller worden gekopieerd, moet u mogelijk extra cloudgegevensbewegingseenheden gebruiken. Sommige andere hulpprogramma's, zoals AdlCopy, ondersteunen geen kopiëren van gegevens tussen regio's.  

* **Bandbreedtekosten**. [Bandbreedtekosten](https://azure.microsoft.com/pricing/details/bandwidth/) zijn van toepassing omdat gegevens worden overgedragen vanuit een Azure-gebied.

* **ACL's op uw gegevens**. Beveilig uw gegevens in de nieuwe regio door ACL's toe te passen op bestanden en mappen. Zie [Gegevens beveiligen die zijn opgeslagen in Azure Data Lake Storage Gen1](data-lake-store-secure-data.md)voor meer informatie. We raden u aan de migratie te gebruiken om uw ACL's bij te werken en aan te passen. Misschien wilt u instellingen gebruiken die vergelijkbaar zijn met uw huidige instellingen. U de ACL's bekijken die op elk bestand worden toegepast met behulp van de Azure-portal, [PowerShell-cmdlets](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission)of SDK's.  

* **Locatie van analyseservices**. Voor de beste prestaties moeten uw analyseservices, zoals Azure Data Lake Analytics of Azure HDInsight, zich in dezelfde regio bevinden als uw gegevens.  

## <a name="next-steps"></a>Volgende stappen
* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
