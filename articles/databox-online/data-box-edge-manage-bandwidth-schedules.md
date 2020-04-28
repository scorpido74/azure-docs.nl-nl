---
title: Bandbreedte schema's Azure Data Box Edge beheren | Microsoft Docs
description: Hierin wordt beschreven hoe u de Azure Portal gebruikt voor het beheren van bandbreedte schema's op uw Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f7b762d5502986c306de240519688aa639f58445
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "60756799"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Gebruik de Azure Portal om bandbreedte schema's op uw Azure Data Box Edge te beheren  

In dit artikel wordt beschreven hoe u gebruikers beheert op uw Azure Data Box Edge. Met bandbreedteschema's kunt u het gebruik van netwerkbandbreedte beheren over schema's voor meerdere tijdstippen. Deze schema's kunnen worden toegepast op upload- en downloadbewerkingen van uw apparaat naar de cloud.

U kunt de bandbreedte schema's voor uw Data Box Edge toevoegen, wijzigen of verwijderen via de Azure Portal.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een schema toevoegen
> * Een schema wijzigen
> * Een schema verwijderen


## <a name="add-a-schedule"></a>Een schema toevoegen

Voer de volgende stappen uit in de Azure Portal om een schema toe te voegen.

1. Ga in de Azure Portal voor uw Data Box Edge-resource naar **band breedte**.
2. Selecteer **+ schema toevoegen**in het rechterdeel venster.

    ![Band breedte selecteren](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. Doe het volgende in **Schema toevoegen**: 

   1. Geef de **Eerste dag**, **Laatste dag**, **Begintijd** en **Eindtijd** van de planning op.
   2. Controleer de optie **alle dagen** als dit schema de hele dag moet worden uitgevoerd.
   3. **Bandbreedtesnelheid** is de bandbreedte in Megabits per seconde (Mbps) die door uw apparaat wordt gebruikt bij bewerkingen die betrekking hebben op de cloud (uploaden en downloaden). Geef voor dit veld een getal tussen 20 en 1.000.000.007 op.
   4. Schakel **Onbeperkte** bandbreedte in als u de datumupload en -download niet wilt regelen.
   5. Selecteer **Toevoegen**.

      ![Schema toevoegen](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Er wordt een schema gemaakt met de opgegeven parameters. Dit schema wordt vervolgens weergegeven in de lijst van bandbreedteschema's in de portal.

    ![Lijst met bandbreedte schema's bijgewerkt](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Schema bewerken

Voer de volgende stappen uit als u een bandbreedteschema wilt bewerken.

1. Ga in het Azure Portal naar uw Data Box Edge resource en ga vervolgens naar **band breedte**. 
2. Selecteer en selecteer een schema dat u wilt wijzigen in de lijst met bandbreedte schema's.
    ![Bandbreedte planning selecteren](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Breng de gewenste wijzigingen aan en sla de wijzigingen op.

    ![Gebruiker wijzigen](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Wanneer het schema is gewijzigd, wordt de lijst met schema's bijgewerkt met het gewijzigde schema.

    ![Gebruiker wijzigen](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Een schema verwijderen

Voer de volgende stappen uit om een bandbreedte schema te verwijderen dat is gekoppeld aan uw Data Box Edge apparaat.

1. Ga in het Azure Portal naar uw Data Box Edge resource en ga vervolgens naar **band breedte**.  

2. Selecteer in de lijst met bandbreedteschema's een schema dat u wilt verwijderen. Selecteer **verwijderen**in het **schema bewerken**. Selecteer **Ja**als u om bevestiging wordt gevraagd.

   ![Een gebruiker verwijderen](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Wanneer de planning is verwijderd, wordt de lijst met schema's bijgewerkt.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [beheren van shares](data-box-edge-manage-shares.md).
