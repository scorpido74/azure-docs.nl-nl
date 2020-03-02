---
title: Een mapstructuur toewijzen aan een Azure File Sync-topologie
description: Wijs een bestaande bestands-en mapstructuur toe aan Azure-bestands shares voor gebruik met Azure File Sync. Een gemeen schappelijk tekst blok, gedeeld tussen migratie documenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 44fb95de88cc86c802e5ba72f0b5379b8708c506
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209556"
---
In deze stap evalueert u hoeveel Azure-bestands shares u nodig hebt. Eén Windows-Server (of-cluster) kan Maxi maal 30 Azure-bestands shares synchroniseren.

Mogelijk hebt u meer mappen in uw StorSimple die u momenteel lokaal deelt als SMB-shares voor uw gebruikers en apps. Het is de eenvoudigste manier om een on-premises share te gebruiken om 1:1 te koppelen aan een Azure-bestands share. Als dit aantal een beheerbare laag is, wat lager is dan 30 voor één Windows-Server, of als u van plan bent twee Windows-servers (60) te hebben, kunt u het beste een 1:1-toewijzing.

Als u meer shares dan 30 hebt, is het vaak niet nodig om een on-premises share 1:1 toe te wijzen aan een Azure-bestands share.
Houd rekening met de volgende opties:

#### <a name="share-grouping"></a>Groepering delen

Als uw HR-afdeling bijvoorbeeld een totaal van 15 shares heeft, kunt u overwegen om alle HR-gegevens op te slaan in één Azure-bestands share. Als u meerdere on-premises shares in een Azure-bestands share opslaat, is het niet mogelijk om de gebruikelijke 15 SMB-shares op uw lokale Windows-Server te maken. Dit betekent alleen dat u de hoofd mappen van deze 15 shares als submappen in een gemeen schappelijke map ordent. Vervolgens synchroniseert u deze algemene map naar een Azure-bestands share. Op die manier is slechts één Azure-bestands share in de Cloud nodig voor deze groep lokale shares.

#### <a name="volume-sync"></a>Volume synchronisatie

Azure File Sync ondersteunt het synchroniseren van de hoofdmap van een volume naar een Azure-bestands share.
Als u de hoofdmap synchroniseert, worden alle submappen en bestanden in dezelfde Azure-bestands share weer beëindigd.

#### <a name="other-best-practices-to-consider"></a>Andere aanbevolen procedures om rekening mee te houden

Met uitzonde ring van de 30 Azure file share-synchronisatie limiet per server is de effectiviteit van de synchronisatie.

Wanneer er meerdere shares op uw server worden gesynchroniseerd met hun eigen Azure-bestands share, kan de synchronisatie voor al deze items parallel functioneren. De schaal vector is niet de grootte van alle bestanden in een synchronisatie bereik. Het is het aantal items (bestanden en mappen) dat moet worden verwerkt.

Eén Azure-bestands share kan Maxi maal 100 TiB bevatten.
Azure File Sync ondersteunt het synchroniseren van Maxi maal 100.000.000 items per Azure-bestands share.

Het synchroniseren van het basis volume is niet altijd het beste antwoord. Er zijn voor delen in het synchroniseren van meerdere locaties, waardoor het aantal items lager per synchronisatie bereik kan worden bewaard. Het instellen van Azure file sync met een lager aantal items is niet alleen belang rijk voor synchronisatie, maar ook voor delen van back-ups in de Cloud en de snelheid van herstel na nood gevallen, voor het geval u uw server kwijtraakt en een nieuwe hebt ingericht die verbinding maakt met dezelfde Azure-fil e-shares.

Gebruik een combi natie van de bovenstaande concepten om te helpen bepalen hoeveel Azure-bestands shares u nodig hebt, en welke delen van uw bestaande StorSimple-gegevens in de Azure-bestands share moeten eindigen.

Maak een lijst waarin uw ideeën worden vastgelegd, zodat u deze in de volgende stap kunt raadplegen. Het is belang rijk dat u de gegevens van uw toewijzings plan gemakkelijk kunt verliezen bij het inrichten van een groot aantal resources tegelijk.
