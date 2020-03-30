---
title: Gegevenssets toevoegen aan een bestaand Azure-gegevensaandeel
description: Meer informatie over het toevoegen van gegevenssets aan een bestaand gegevensaandeel in Azure-gegevensdelen en delen met dezelfde ontvangers.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490550"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Gegevenssets toevoegen aan een bestaand aandeel in Azure Data Share

In dit artikel wordt uitgelegd hoe u gegevenssets toevoegt aan een reeds bestaand gegevensaandeel met Azure Data Share. Hierdoor u meer gegevens delen met dezelfde ontvangers zonder dat u een nieuw aandeel hoeft te maken.

Zie de zelfstudie [Gegevens delen](share-your-data.md) voor informatie over het toevoegen van gegevenssets bij het maken van een share.

## <a name="navigate-to-a-sent-data-share"></a>Navigeren naar een verzonden gegevensaandeel

Navigeer in Azure Data Share naar uw verzonden aandeel en selecteer het tabblad **Gegevenssets.** Klik **op + Sets toevoegen** om meer gegevenssets toe te voegen.

![Gegevenssets toevoegen](./media/how-to/how-to-add-datasets/add-datasets.png)

Selecteer in het deelvenster aan de rechterkant het gegevenssettype dat u wilt toevoegen en klik op **Volgende**. Selecteer het abonnement en de brongroep van de gegevens die u wilt toevoegen. Zoek met de vervolgkeuzepijlen en schakel vervolgens het selectievakje in naast de gegevens die u wilt toevoegen.

![Gegevenssets toevoegen](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Zodra u op **Gegevenssets toevoegen**klikt, worden de gegevenssets aan uw aandeel toegevoegd. Opmerking: een momentopname moet worden geactiveerd door uw consumenten om de nieuwe gegevenssets te kunnen zien. Als er momentopnameinstellingen zijn geconfigureerd, zien consumenten de nieuwe gegevenssets zodra de volgende geplande momentopname is voltooid. Zonder dat de momentopnameinstellingen zijn geconfigureerd, moet de consument handmatig een volledige of incrementele kopie van de gegevens activeren om de updates te ontvangen. Zie [Momentopnamen](terminology.md)voor meer informatie over momentopnamen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [toevoegen van geadresseerden aan een bestaande gegevensshare](how-to-add-recipients.md).