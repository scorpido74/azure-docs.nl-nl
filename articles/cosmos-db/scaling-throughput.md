---
title: Door Voer in Azure Cosmos DB schalen
description: In dit artikel wordt beschreven hoe Azure Cosmos DB door Voer wordt geschaald in verschillende regio's waarin het Azure Cosmos-account is ingericht.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873502"
---
# <a name="globally-scale-provisioned-throughput"></a>Wereldwijd schalen van ingerichte doorvoer 

In Azure Cosmos DB wordt ingerichte door Voer weer gegeven als aanvraag eenheden per seconde (RU/s of de meervouds vorm RUs). RUs meet de kosten van zowel lees-als schrijf bewerkingen voor uw Cosmos-container, zoals wordt weer gegeven in de volgende afbeelding:

![Aanvraageenheden](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

U kunt RUs inrichten op een Cosmos-container of een Cosmos-data base. RUs dat is ingericht voor een container is uitsluitend beschikbaar voor de bewerkingen die in die container worden uitgevoerd. RUs dat is ingericht voor een Data Base, wordt gedeeld tussen alle containers in die data base (met uitzonde ring van containers met uitsluitend toegewezen RUs).

U kunt de ingerichte RU/s op elk gewenst moment verg Roten of verkleinen om de ingerichte door Voer in te schalen. Zie [How-to-Provisioning inrichten](set-throughput.md) en Cosmos-containers en-data bases elastisch schalen voor meer informatie. Voor een wereld wijde schaal aanpassing kunt u op elk gewenst moment regio's toevoegen aan of verwijderen uit uw Cosmos-account. Zie [regio's toevoegen aan/verwijderen uit uw database account](how-to-manage-database-account.md#addremove-regions-from-your-database-account)voor meer informatie. Het koppelen van meerdere regio's aan een Cosmos-account is belang rijk in veel scenario's: voor een lage latentie en een [hoge Beschik baarheid](high-availability.md) over de hele wereld.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Hoe ingerichte door Voer wordt gedistribueerd in verschillende regio's

Als u *' r '* Rus inricht in een Cosmos-container (of-data base), zorgt Cosmos DB ervoor dat *' r '* Rus beschikbaar is in *elke* regio die aan uw Cosmos-account is gekoppeld. Telkens wanneer u een nieuwe regio aan uw account toevoegt, Cosmos DB automatisch *' R '* in de zojuist toegevoegde regio. De bewerkingen die worden uitgevoerd voor uw Cosmos-container, worden gegarandeerd *' R '* RUs in elke regio. U kunt RUs niet selectief toewijzen aan een bepaalde regio. Het RUs-aanbod dat is ingericht voor een Cosmos-container (of-data base) wordt ingericht in alle regio's die zijn gekoppeld aan uw Cosmos-account.

Ervan uitgaande dat een Cosmos-container is geconfigureerd met *' R '* RUs en dat er *N* -regio's zijn gekoppeld aan het Cosmos-account, geldt het volgende:

- Als het Cosmos-account is geconfigureerd met één schrijf regio, is het totale RUs algemeen beschikbaar in de container = *R* x *N*.

- Als het Cosmos-account is geconfigureerd met meerdere schrijf regio's, is het totale RUs algemeen beschikbaar in de container = *R* x (*N*+ 1). Het aanvullende *R* RUs wordt automatisch ingericht om update conflicten en antientropie verkeer over de regio's te verwerken.

Uw keuze van [consistentie model](consistency-levels.md) is ook van invloed op de door voer. U kunt ongeveer 2x Lees doorvoer bereiken voor de minder consistente consistentie niveaus (zoals een *sessie*, *consistent voor voegsel* en *uiteindelijke* consistentie) in vergelijking met sterkere consistentie niveaus (bijvoorbeeld *gebonden veroudering* of *sterke* consistentie).

## <a name="next-steps"></a>Volgende stappen

Hierna vindt u informatie over het configureren van door Voer voor een container of Data Base:

* [De door Voer voor containers en data bases ophalen en instellen](set-throughput.md) 

