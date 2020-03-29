---
title: Azure Data Box Edge beheerbandbreedteschema's | Microsoft Documenten
description: Beschrijft hoe u de Azure-portal gebruiken om bandbreedteschema's te beheren op uw Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f7b762d5502986c306de240519688aa639f58445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60756799"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>De Azure-portal gebruiken om bandbreedteschema's te beheren op uw Azure Data Box Edge  

In dit artikel wordt beschreven hoe u gebruikers beheert op uw Azure Data Box Edge. Met bandbreedteschema's kunt u het gebruik van netwerkbandbreedte beheren over schema's voor meerdere tijdstippen. Deze schema's kunnen worden toegepast op upload- en downloadbewerkingen van uw apparaat naar de cloud.

U de bandbreedteschema's voor uw Data Box Edge toevoegen, wijzigen of verwijderen via de Azure-portal.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een schema toevoegen
> * Een schema wijzigen
> * Een schema verwijderen


## <a name="add-a-schedule"></a>Een schema toevoegen

Volg de volgende stappen in de Azure-portal om een planning toe te voegen.

1. Ga in de Azure-portal voor uw Data Box Edge-bron naar **Bandbreedte**.
2. Selecteer in het rechterdeelvenster **+ Schema toevoegen**.

    ![Bandbreedte selecteren](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. Doe het volgende in **Schema toevoegen**: 

   1. Geef de **Eerste dag**, **Laatste dag**, **Begintijd** en **Eindtijd** van de planning op.
   2. Controleer de optie **Hele dag** als dit schema de hele dag moet worden uitgevoerd.
   3. **Bandbreedtesnelheid** is de bandbreedte in Megabits per seconde (Mbps) die door uw apparaat wordt gebruikt bij bewerkingen die betrekking hebben op de cloud (uploaden en downloaden). Lever een getal tussen 20 en 1.000.000.007 voor dit veld.
   4. Schakel **Onbeperkte** bandbreedte in als u de datumupload en -download niet wilt regelen.
   5. Selecteer **Toevoegen**.

      ![Schema toevoegen](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Er wordt een schema gemaakt met de opgegeven parameters. Dit schema wordt vervolgens weergegeven in de lijst van bandbreedteschema's in de portal.

    ![Bijgewerkte lijst met bandbreedteschema's](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Schema bewerken

Voer de volgende stappen uit als u een bandbreedteschema wilt bewerken.

1. Ga in de Azure-portal naar uw Data Box Edge-bron en ga vervolgens naar **Bandbreedte**. 
2. Selecteer in de lijst met bandbreedteschema's een schema dat u wilt wijzigen.
    ![Bandbreedteschema selecteren](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Breng de gewenste wijzigingen aan en sla de wijzigingen op.

    ![Gebruiker wijzigen](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Wanneer het schema is gewijzigd, wordt de lijst met schema's bijgewerkt met het gewijzigde schema.

    ![Gebruiker wijzigen](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Een schema verwijderen

Volg de volgende stappen om een bandbreedteschema te verwijderen dat is gekoppeld aan uw Data Box Edge-apparaat.

1. Ga in de Azure-portal naar uw Data Box Edge-bron en ga vervolgens naar **Bandbreedte**.  

2. Selecteer in de lijst met bandbreedteschema's een schema dat u wilt verwijderen. Selecteer **Verwijderen**in het **bewerkingsschema**. Wanneer u om bevestiging wordt gevraagd, selecteert u **Ja**.

   ![Een gebruiker verwijderen](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Wanneer de planning is verwijderd, wordt de lijst met schema's bijgewerkt.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [beheren van aandelen](data-box-edge-manage-shares.md).
