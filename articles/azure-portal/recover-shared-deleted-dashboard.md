---
title: Een verwijderd dashboard herstellen in de Azure-portal | Microsoft Documenten
description: Als u een gepubliceerd dashboard in de Azure-portal verwijdert, u het dashboard herstellen.
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: af0c72f0bc5dd8f3a3cbae7b82b1ac56447cbb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133292"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Een verwijderd dashboard herstellen in de Azure-portal

Als u zich in de openbare Azure-cloud bevindt en u een _gepubliceerd_ dashboard in de Azure-portal verwijdert, u dat dashboard binnen 14 dagen na de verwijdering herstellen. Als u zich in een Azure-overheidscloud bevindt of als het dashboard niet is gepubliceerd, u deze niet herstellen en moet u deze opnieuw opbouwen. Zie [Dashboard publiceren](azure-portal-dashboard-share-access.md#publish-dashboard)voor meer informatie over het publiceren van een dashboard . Volg de volgende stappen om een gepubliceerd dashboard te herstellen:

1. Selecteer **resourcegroepen**in het menu Azure portal en selecteer vervolgens de resourcegroep waar u het dashboard hebt gepubliceerd (standaard worden **dashboards**genoemd).

1. Vouw **onder het logboek Activiteit**de bewerking Dashboard **verwijderen** uit. Selecteer het tabblad **Geschiedenis wijzigen** en selecteer ** \<verwijderde\>bron**.

    ![Schermafbeelding van het tabblad Geschiedenis wijzigen](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Selecteer en kopieer de inhoud van het linkerdeelvenster en sla vervolgens op in een tekstbestand met een _.json-bestandsextensie._ De portal gebruikt het JSON-bestand om het dashboard opnieuw te maken.

    ![Schermafbeelding van de diff wijzigingsgeschiedenis](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Selecteer **dashboards**in het menu Azure portal en selecteer **Uploaden**.

    ![Schermafbeelding van het uploaden van dashboard](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Selecteer het JSON-bestand dat u hebt opgeslagen. De portal maakt het dashboard opnieuw met dezelfde naam en elementen als het verwijderde dashboard.

1. Selecteer **Delen** om het dashboard te publiceren en het juiste toegangscontrolebeheer opnieuw in te stellen.

    ![Schermafbeelding van dashboardshare](media/recover-shared-deleted-dashboard/dashboard-share.png)
