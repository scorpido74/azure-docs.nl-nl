---
title: Een verwijderd dash board herstellen in de Azure Portal | Microsoft Docs
description: Als u een gepubliceerd dash board in de Azure Portal verwijdert, kunt u het dash board herstellen.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ead9dab61e0cb60505aed9db43c61eb73a0e22a9
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760195"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Een verwijderd dash board in het Azure Portal herstellen

Als u een _gepubliceerd_ dash board in de Azure Portal verwijdert, kunt u dat dash board binnen 14 dagen na de verwijdering herstellen. Als het dash board niet is gepubliceerd, kunt u het niet herstellen en moet u het opnieuw bouwen. Zie [dash board publiceren](azure-portal-dashboard-share-access.md#publish-dashboard)voor meer informatie over het publiceren van een dash board. Volg deze stappen om een gepubliceerd dash board te herstellen:

1. Selecteer **resource groepen**in het menu Azure Portal en selecteer vervolgens de resource groep waar u het dash board hebt gepubliceerd (standaard **Dash boards**genoemd).

1. Vouw onder **activiteiten logboek**de bewerking **dash board verwijderen** uit. Selecteer het tabblad **wijzigings overzicht** en selecteer vervolgens **\<verwijderde resource\>** .

    ![Scherm afbeelding van het tabblad wijzigings overzicht](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Selecteer en kopieer de inhoud van het linkerdeel venster en sla deze op in een tekst bestand met de extensie _. json_ . De portal maakt gebruik van het JSON-bestand om het dash board opnieuw te maken.

    ![Scherm afbeelding van het verschil met wijzigings geschiedenis](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Selecteer in het menu Azure Portal de optie **Dash boards**en selecteer vervolgens **uploaden**.

    ![Scherm afbeelding van het uploaden van het dash board](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Selecteer het JSON-bestand dat u hebt opgeslagen. De portal maakt het dash board opnieuw met dezelfde naam en elementen als het verwijderde dash board.

1. Selecteer **delen** om het dash board te publiceren en het juiste toegangs beheer te herstellen.

    ![Scherm opname van de dashboard share](media/recover-shared-deleted-dashboard/dashboard-share.png)
