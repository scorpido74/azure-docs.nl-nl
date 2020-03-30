---
title: Verbinding maken met bestandssystemen op locatie
description: Taken en werkstromen automatiseren die verbinding maken met on-premises bestandssystemen met de bestandssysteemconnector via de on-premises gegevensgateway in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: 2a00405a2100c3e565ca4f8ea4149540a5199b43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651403"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Verbinding maken met on-premises bestandssystemen met Azure Logic Apps

Met Azure Logic Apps en de connector Voor bestandssysteem u geautomatiseerde taken en werkstromen maken die bestanden maken en beheren op een on-premises bestandsshare, bijvoorbeeld:

- Bestanden maken, downloaden, toevoegen, bijwerken en verwijderen.
- Lijst bestanden in mappen of hoofdmappen.
- Krijg bestandsinhoud en metagegevens.

In dit artikel ziet u hoe u verbinding maken met een on-premises bestandssysteem zoals beschreven in dit voorbeeldscenario: kopieer een bestand dat naar Dropbox is geüpload naar een bestandsshare en stuur vervolgens een e-mail. Om veilig verbinding te maken en toegang te krijgen tot on-premises systemen, gebruiken logische apps de [on-premises datagateway.](../logic-apps/logic-apps-gateway-connection.md) Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Zie de [verbindingsverwijzing voor bestandssysteemoplossingen](/connectors/filesystem/)voor technische informatie over connectoren voor connectoren .

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Voordat u logische apps koppelen aan on-premises systemen zoals uw bestandssysteemserver, moet u [een on-premises gegevensgateway installeren en instellen.](../logic-apps/logic-apps-gateway-install.md) Op die manier u opgeven om uw gateway-installatie te gebruiken wanneer u de verbinding met het bestandssysteem maakt vanuit uw logische app.

* Een [Dropbox-account](https://www.dropbox.com/), waar je je gratis bij aanmelden. Je accountgegevens zijn nodig voor het maken van een verbinding tussen je logica-app en je Dropbox-account.

* Toegang tot de computer met het bestandssysteem dat u wilt gebruiken. Als u bijvoorbeeld de gegevensgateway op dezelfde computer als uw bestandssysteem installeert, hebt u de accountreferenties voor die computer nodig.

* Een e-mailaccount van een provider die wordt ondersteund door Logic Apps, zoals Office 365 Outlook, Outlook.com of Gmail. Voor andere providers [kunt u hier de lijst met connectors bekijken](https://docs.microsoft.com/connectors/). Deze logische app maakt gebruik van een Office 365 Outlook-account. Als u een ander e-mailaccount gebruikt, zijn de algemene stappen hetzelfde, maar ziet de gebruikersinterface er misschien iets anders uit.

* Basiskennis over [het maken van logische apps.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Voor dit voorbeeld hebt u een lege logische app nodig.

## <a name="add-trigger"></a>Trigger toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Voer in het zoekvak 'dropbox' in als filter. Selecteer deze trigger in de lijst triggers: **Wanneer een bestand wordt gemaakt**

   ![Dropbox-trigger selecteren](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Meld u aan met uw Dropbox-accountreferenties en vernieuw de toegang tot uw Dropbox-gegevens voor Azure Logic Apps.

1. Geef de vereiste informatie voor uw trigger.

   ![Dropbox-trigger](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Acties toevoegen

1. Kies Volgende **stap**onder de trigger . Voer in het zoekvak 'bestandssysteem' in als filter. Selecteer in de lijst met acties de optie: **Bestand maken**

   ![Connector voor bestandssysteem zoeken](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Als u nog geen verbinding met uw bestandssysteem hebt, wordt u gevraagd een verbinding te maken.

   ![Verbinding maken](media/logic-apps-using-file-connector/file-system-connection.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   | -------- | -------- | ----- | ----------- |
   | **Verbindingsnaam** | Ja | <*verbindingsnaam*> | De naam die u wilt voor uw verbinding |
   | **Hoofdmap** | Ja | <*rootmap-naam*> | De hoofdmap voor uw bestandssysteem, bijvoorbeeld als u uw on-premises gegevensgateway hebt geïnstalleerd, zoals een lokale map op de computer waar de on-premises gegevensgateway is geïnstalleerd, of de map voor een netwerkshare waartoe de computer toegang heeft. <p>Bijvoorbeeld: `\\PublicShare\\DropboxFiles` <p>De hoofdmap is de hoofdmap van de bovenliggende map, die wordt gebruikt voor relatieve paden voor alle bestandsgerelateerde acties. |
   | **Verificatietype** | Nee | <*auth-type*> | Het type verificatie dat uw bestandssysteem gebruikt, bijvoorbeeld **Windows** |
   | **Gebruikersnaam** | Ja | <*domain*>\\domeingebruikersnaam<*username*> | De gebruikersnaam voor de computer waar u uw bestandssysteem hebt |
   | **Wachtwoord** | Ja | <*uw wachtwoord*> | Het wachtwoord voor de computer waar u uw bestandssysteem hebt |
   | **Gateway** | Ja | <*installed-gateway-name*> | De naam voor uw eerder geïnstalleerde gateway |
   |||||

1. Wanneer u klaar bent, kiest u **Maken**.

   Logic Apps configureert en test uw verbinding, zodat de verbinding goed werkt. Als de verbinding correct is ingesteld, worden er opties weergegeven voor de actie die u eerder hebt geselecteerd.

1. Geef in de actie **Bestand maken** de details op voor het kopiëren van bestanden van Dropbox naar de hoofdmap in uw on-premises bestandsshare. Als u uitvoer uit eerdere stappen wilt toevoegen, klikt u in de vakken en selecteert u in beschikbare velden wanneer de lijst met dynamische inhoud wordt weergegeven.

   ![Bestandsactie maken](media/logic-apps-using-file-connector/create-file-filled.png)

1. Voeg nu een Outlook-actie toe die een e-mail verzendt, zodat de juiste gebruikers op de hoogte zijn van het nieuwe bestand. Voer de geadresseerden, de titel en de hoofdtekst van de e-mail in. Voor het testen u uw eigen e-mailadres gebruiken.

   ![Actie E-mail verzenden](media/logic-apps-using-file-connector/send-email.png)

1. Sla uw logische app op. Test je app door een bestand te uploaden naar Dropbox.

   Uw logica-app moet het bestand kopiëren naar uw on-premises bestandsshare en de ontvangers een e-mail sturen over het gekopieerde bestand.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/fileconnector/)de connector voor meer technische details over deze connector, zoals triggers, acties en limieten zoals beschreven in het Swagger-bestand van de connector.

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over hoe u [verbinding maken met on-premises gegevens](../logic-apps/logic-apps-gateway-connection.md) 
* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
