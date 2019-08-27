---
title: Verbinding maken met Dropbox-Azure Logic Apps
description: Bestanden uploaden en beheren met Dropbox REST Api's en Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 4e0689454ec074348fcbc775373a48d6825cfac4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050989"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Bestanden in Dropbox uploaden en beheren met behulp van Azure Logic Apps

Met de Dropbox-connector en Azure Logic Apps kunt u geautomatiseerde werk stromen maken voor het uploaden en beheren van bestanden in uw Dropbox-account. 

In dit artikel wordt beschreven hoe u verbinding maakt met Dropbox vanuit uw logische app en vervolgens de Dropbox toevoegt **Wanneer een bestand wordt gemaakt** en de Dropbox **Bestands inhoud ophalen met behulp van het pad** .

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [Dropbox-account](https://www.dropbox.com/), dat u gratis kunt registreren. Uw account referenties zijn nodig voor het maken van een verbinding tussen uw logische app en uw Dropbox-account.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Voor dit voor beeld hebt u een lege logische app nodig.

## <a name="add-trigger"></a>Trigger toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Kies **Alle** onder het zoekvak. Voer in het zoekvak ' Dropbox ' in als uw filter.
Selecteer in de lijst triggers deze trigger: **Wanneer een bestand wordt gemaakt**

   ![Dropbox-trigger selecteren](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Meld u aan met de referenties van uw Dropbox-account en machtig toegang tot uw Dropbox-gegevens voor Azure Logic Apps.

1. Geef de vereiste informatie op voor de trigger. 

   In dit voor beeld selecteert u de map waarnaar u het maken van bestanden wilt bijhouden. Als u wilt bladeren door uw mappen, kiest u het mappictogram naast het vak **map** .

## <a name="add-action"></a>Actie toevoegen

Voeg nu een actie toe waarmee de inhoud wordt opgehaald uit een nieuw bestand.

1. Kies **volgende stap**onder de trigger. 

1. Kies **Alle** onder het zoekvak. Voer in het zoekvak ' Dropbox ' in als uw filter.
Selecteer in de lijst acties deze actie: **Bestands inhoud ophalen met behulp van pad**

1. Als u Azure Logic Apps nog geen toegang hebt tot Dropbox, moet u de toegang nu toestaan.

1. Als u wilt bladeren naar het bestandspad dat u wilt gebruiken, naast het vak **pad naar bestand** , kiest u de knop met weglatings tekens ( **...** ). 

   U kunt ook in het vak bestandspad klikken en in de lijst met dynamische inhoud de optie **bestandspad**selecteren, waarvan de waarde beschikbaar is als uitvoer van de trigger die u in de vorige sectie hebt toegevoegd.

1. Wanneer u klaar bent, slaat u de logische app op.

1. Maak een nieuw bestand in Dropbox om uw logische app te activeren.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentie pagina van de connector](/connectors/dropbox/)voor technische details, zoals triggers, acties en limieten, zoals beschreven in het OpenAPI (voorheen Swagger)-bestand van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors
