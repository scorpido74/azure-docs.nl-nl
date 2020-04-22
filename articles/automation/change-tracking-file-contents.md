---
title: Wijzigingen in bestandsinhoud weergeven met Azure Automation
description: Gebruik de functie bestandsinhoudswijziging van Change Tracking om de inhoud van een gewijzigd bestand weer te geven.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 57c3c2c7a0c923921c727ccea7839940457bc1ee
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683003"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Inhoud weergeven van een bestand dat wordt bijgehouden met Change Tracking

Met het bijhouden van bestandsinhoud u de inhoud van een bestand bekijken voor en na een wijziging die wordt bijgehouden met Change Tracking. Om dit te doen, slaat het de inhoud van het bestand op in een opslagaccount nadat elke wijziging plaatsvindt.

## <a name="requirements"></a>Vereisten

* Voor het opslaan van bestandsinhoud is een standaardopslagaccount vereist met het implementatiemodel Resource Manager. Premium- en klassieke opslagaccounts voor implementatiemodellen mogen niet worden gebruikt. Zie [Over Azure-opslagaccounts](../storage/common/storage-create-storage-account.md) voor meer informatie over opslagaccounts

* Het gebruikte opslagaccount kan slechts 1 Automation-account hebben aangesloten.

* [Change Tracking](automation-change-tracking.md) is ingeschakeld in uw Automatiseringsaccount.

## <a name="enable-file-content-tracking"></a>Bijhouden van bestandsinhoud inschakelen

1. Open in de Azure-portal uw Automatiseringsaccount en selecteer **Vervolgens Bijhouden wijzigen**.
2. Selecteer in het bovenste menu **Instellingen bewerken**.
3. Selecteer **Bestandsinhoud** en klik op **Koppeling**. Hiermee wordt het deelvenster **Inhoudslocatie toevoegen voor het bijhouden van wijzigingen** geopend.

   ![inschakelen](./media/change-tracking-file-contents/enable.png)

4. Selecteer het abonnements- en opslagaccount dat u wilt gebruiken om de inhoud van het bestand op te slaan. Als u het bijhouden van bestandsinhoud wilt inschakelen voor alle bestaande bijgehouden bestanden, selecteert u **Aan** voor **het uploaden van bestandsinhoud voor alle instellingen**. U dit voor elk bestandspad achteraf wijzigen.

   ![opslagaccount instellen](./media/change-tracking-file-contents/storage-account.png)

5. Eenmaal ingeschakeld worden het opslagaccount en de SAS Uris weergegeven. De SAS Uris verloopt na 365 dagen en kan opnieuw worden gemaakt door op de knop **Regenereren** te klikken.

   ![accountsleutels lijst](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Een bestand toevoegen

De volgende stappen lopen u door het inschakelen van wijzigingstracking voor een bestand:

1. Selecteer op de pagina Instellingen bewerken van **Bijhouden wijzigen**het **tabblad Windows-bestanden** of **Linux-bestanden** en klik op **Toevoegen**

1. Vul de gegevens voor het bestandspad in en selecteer Waar onder **Bestandsinhoud uploaden voor alle instellingen**. Met deze instelling u alleen bestandsinhoud bijhouden voor dat bestandspad.

   ![een linux-bestand toevoegen](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>De inhoud van een bijgehouden bestand weergeven

1. Zodra een wijziging is gedetecteerd voor het bestand of een bestand in het pad, wordt deze weergegeven in de portal. Selecteer de bestandswijziging in de lijst met wijzigingen. Het deelvenster Details wijzigen wordt weergegeven.

   ![lijstwijzigingen](./media/change-tracking-file-contents/change-list.png)

1. In het deelvenster Details wijzigen ziet u de standaard voor en na bestandsgegevens. Selecteer **Wijzigingen in bestandsinhoud weergeven** om de inhoud van het bestand te bekijken.

   ![details wijzigen](./media/change-tracking-file-contents/change-details.png)

1. Op de nieuwe pagina ziet u de bestandsinhoud in een weergave naast elkaar. U **Inline** ook selecteren om een inline-weergave van de wijzigingen te bekijken.

   ![bestandswijzigingen weergeven](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar de zelfstudie over Change Tracking voor meer informatie over het gebruik van de oplossing:

> [!div class="nextstepaction"]
> [Problemen met wijzigingen in uw omgeving oplossen](automation-tutorial-troubleshoot-changes.md)

* Gebruik [Logboekzoekopdrachten in Azure Monitor-logboeken](../log-analytics/log-analytics-log-searches.md) om gedetailleerde gegevens voor het bijhouden van wijzigingen weer te geven.

