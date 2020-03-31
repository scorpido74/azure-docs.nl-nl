---
title: Verbinding maken met Dropbox
description: Werk- en werkstromen automatiseren die bestanden uploaden en beheren in Dropbox met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 8f54f832884b172761f62b16db29d2f0abd0dd46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665748"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Bestanden uploaden en beheren in Dropbox met Azure Logic Apps

Met de Dropbox-connector en Azure Logic Apps u geautomatiseerde werkstromen maken die bestanden uploaden en beheren in uw Dropbox-account. 

In dit artikel ziet u hoe u verbinding maakt met Dropbox vanuit uw logica-app en voegt u de Dropbox toe **Wanneer een bestand is gemaakt** trigger en de Dropbox Get **bestandsinhoud met behulp van padactie.**

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [Dropbox-account](https://www.dropbox.com/), waar je je gratis bij aanmelden. Je accountgegevens zijn nodig voor het maken van een verbinding tussen je logica-app en je Dropbox-account.

* Basiskennis over [het maken van logische apps.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Voor dit voorbeeld hebt u een lege logische app nodig.

## <a name="add-trigger"></a>Trigger toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Kies **Alle** onder het zoekvak. Voer in het zoekvak 'dropbox' in als filter.
Selecteer deze trigger in de lijst triggers: **Wanneer een bestand wordt gemaakt**

   ![Dropbox-trigger selecteren](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Meld u aan met uw Dropbox-accountreferenties en vernieuw de toegang tot uw Dropbox-gegevens voor Azure Logic Apps.

1. Geef de vereiste informatie voor uw trigger. 

   Selecteer in dit voorbeeld de map waarin u het maken van bestanden wilt bijhouden. Als u door uw mappen wilt bladeren, kiest u het mappictogram naast het **vak Map.**

## <a name="add-action"></a>Actie toevoegen

Voeg nu een actie toe die de inhoud uit een nieuw bestand haalt.

1. Kies Volgende **stap**onder de trigger . 

1. Kies **Alle** onder het zoekvak. Voer in het zoekvak 'dropbox' in als filter.
Selecteer deze actie in de lijst met acties: **Bestandsinhoud ophalen via pad**

1. Als je Azure Logic Apps nog niet hebt geautoriseerd om toegang te krijgen tot Dropbox, moet je nu de toegang autoriseren.

1. Als u naar het bestandspad wilt bladeren dat u wilt gebruiken, kiest u naast het vak **Bestandspad** de knop ellips (**... ).** 

   U ook in het vak **Bestandspad** klikken en in de lijst met dynamische inhoud de optie **Bestandspad**selecteren, waarvan de waarde beschikbaar is als uitvoer van de trigger die u in de vorige sectie hebt toegevoegd.

1. Wanneer u klaar bent, slaat u uw logica-app op.

1. Als u uw logica-app wilt activeren, maakt u een nieuw bestand in Dropbox.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](/connectors/dropbox/)de connector voor technische details, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
