---
title: Wijzigingen in bestands inhoud weer geven met Azure Automation
description: Gebruik de functie voor het wijzigen van de bestands inhoud van het bijhouden van wijzigingen om de inhoud weer te geven van een bestand dat is gewijzigd.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4ab88aa2dc604172f00d875353dabba61fd101af
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850581"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Inhoud weer geven van een bestand dat wordt bijgehouden met Wijzigingen bijhouden

Met het bijhouden van bestands inhoud kunt u de inhoud van een bestand weer geven voor en na een wijziging die wordt bijgehouden met Wijzigingen bijhouden. Hiervoor wordt de bestands inhoud opgeslagen in een opslag account nadat elke wijziging is doorgevoerd.

## <a name="requirements"></a>Vereisten

* Een standaard opslag account met het Resource Manager-implementatie model is vereist voor het opslaan van bestands inhoud. Premium-en klassieke implementatie model opslag accounts mogen niet worden gebruikt. Zie [over Azure Storage-accounts](../storage/common/storage-create-storage-account.md) voor meer informatie over opslag accounts.

* Voor het gebruikte opslag account kan slechts één Automation-account zijn verbonden.

* [Wijzigingen bijhouden](automation-change-tracking.md) is ingeschakeld in uw Automation-account.

## <a name="enable-file-content-tracking"></a>Tracering van bestands inhoud inschakelen

1. Open uw Automation-account in het Azure Portal en selecteer vervolgens **Wijzigingen bijhouden**.
2. Selecteer in het bovenste menu **Instellingen bewerken**.
3. Selecteer **Bestands inhoud** en klik op **koppelen**. Hiermee opent u het deel venster **locatie van inhoud toevoegen voor wijzigingen bijhouden** .

   ![Inschakelen](./media/change-tracking-file-contents/enable.png)

4. Selecteer het abonnement en het opslag account dat moet worden gebruikt voor het opslaan van de bestands inhoud. Als u het bijhouden van bestands inhoud voor alle bestaande bijgehouden bestanden wilt inschakelen, selecteert u **aan** voor **uploaden van bestands inhoud voor alle instellingen**. U kunt dit later voor elk bestandspad wijzigen.

   ![opslag account instellen](./media/change-tracking-file-contents/storage-account.png)

5. Als het opslag account en de SAS-Uri's eenmaal zijn ingeschakeld, worden deze weer gegeven. De SAS-Uri's verlopen na 365 dagen en kunnen opnieuw worden gemaakt door te klikken op de knop **opnieuw genereren** .

   ![lijst met account sleutels](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Een bestand toevoegen

De volgende stappen helpen u bij het inschakelen van het bijhouden van wijzigingen voor een bestand:

1. Op de pagina **Instellingen bewerken** van **Wijzigingen bijhouden**selecteert u het tabblad **Windows-bestanden** of Linux- **bestanden** en klikt u op **toevoegen**

1. Vul de informatie voor het bestandspad in en selecteer **waar** onder **Bestands inhoud uploaden voor alle instellingen**. Met deze instelling wordt het bijhouden van bestands inhoud alleen voor dat bestandspad ingeschakeld.

   ![een Linux-bestand toevoegen](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>De inhoud van een bijgehouden bestand weer geven

1. Als er een wijziging is gedetecteerd voor het bestand of een bestand in het pad, wordt het weer gegeven in de portal. Selecteer de wijziging van het bestand in de lijst met wijzigingen. Het deel venster **Details wijzigen** wordt weer gegeven.

   ![wijzigingen weer geven](./media/change-tracking-file-contents/change-list.png)

1. Op de pagina **Details wijzigen** ziet u de standaard voor-en na-bestands informatie. Klik linksboven op **Bestands inhoud weer geven wijzigingen** om de inhoud van het bestand te bekijken.

   ![Details wijzigen](./media/change-tracking-file-contents/change-details.png)

1. Op de pagina Nieuw wordt de inhoud van het bestand weer gegeven in een side-by-side-weer gave. U kunt ook **inline** selecteren om een inline weer gave van de wijzigingen weer te geven.

   ![bestands wijzigingen weer geven](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar de zelf studie over Wijzigingen bijhouden voor meer informatie over het gebruik van de oplossing:

> [!div class="nextstepaction"]
> [Problemen met wijzigingen in uw omgeving oplossen](automation-tutorial-troubleshoot-changes.md)

* [Zoek opdrachten in Logboeken in azure monitor logboeken](../log-analytics/log-analytics-log-searches.md) gebruiken om gedetailleerde gegevens voor het bijhouden van wijzigingen weer te geven.

