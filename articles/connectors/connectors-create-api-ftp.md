---
title: Verbinding maken met FTP-server
description: Werk- en werkstromen automatiseren die bestanden op een FTP-server maken, bewaken en beheren met Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: 94ca609d9dc070e6e2b4dc878ecd8dfaf9331ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648169"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>FTP-bestanden maken, bewaken en beheren met Azure Logic Apps

Met Azure Logic Apps en de FTP-connector u geautomatiseerde taken en werkstromen maken die bestanden maken, bewaken, verzenden en ontvangen via uw account op een FTP-server, samen met andere acties, bijvoorbeeld:

* Controleer wanneer bestanden worden toegevoegd of gewijzigd.
* Bestanden downloaden, maken, kopiëren, bijwerken, aanbieden en verwijderen.
* Krijg bestandsinhoud en metagegevens.
* Haal archieven naar mappen.

U triggers gebruiken die reacties van uw FTP-server ontvangen en de uitvoer beschikbaar maken voor andere acties. U uitvoeren van acties in uw logische apps gebruiken voor het beheren van bestanden op uw FTP-server. U ook andere acties de uitvoer van FTP-acties laten gebruiken. Als u bijvoorbeeld regelmatig bestanden van uw FTP-server krijgt, u e-mail verzenden over die bestanden en hun inhoud met behulp van de Office 365 Outlook-connector of Outlook.com connector. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Beperkingen

* De FTP-connector ondersteunt alleen expliciete FTP via SSL (FTPS) en is niet compatibel met impliciete FTPS.

* Ftp-acties kunnen standaard bestanden lezen of schrijven die *50 MB of kleiner*zijn. Als u bestanden van meer dan 50 MB wilt verwerken, ondersteunen FTP-acties [berichtchunking](../logic-apps/logic-apps-handle-large-messages.md). Met de actie **Bestandsinhoud downloaden** wordt impliciet gebruik genomen van chunking.

* FTP-triggers ondersteunen geen chunking. Wanneer u bestandsinhoud opvraagt, selecteren triggers alleen bestanden die 50 MB of kleiner zijn. Volg dit patroon om bestanden van meer dan 50 MB te krijgen:

  * Gebruik een FTP-trigger die bestandseigenschappen retourneert, zoals **Wanneer een bestand wordt toegevoegd of gewijzigd (alleen eigenschappen).**

  * Volg de trigger met de actie FTP **Get-bestandsinhoud,** die het volledige bestand leest en impliciet chunking gebruikt.

* Als u een on-premises FTP-server hebt, u overwegen een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) te maken of [hybride azure-verbindingen te](../app-service/app-service-hybrid-connections.md)gebruiken, waarmee u beide on-premises gegevensbronnen openen zonder een on-premises gegevensgateway te gebruiken.

## <a name="how-ftp-triggers-work"></a>Hoe FTP-triggers werken

FTP-triggers werken door het FTP-bestandssysteem te peilen en te zoeken naar een bestand dat is gewijzigd sinds de laatste poll. Met sommige hulpprogramma's u de tijdstempel behouden wanneer de bestanden worden gewijzigd. In deze gevallen moet u deze functie uitschakelen, zodat uw trigger kan werken. Hier zijn enkele veelvoorkomende instellingen:

| SFTP-client | Actie |
|-------------|--------|
| Winscp | Ga > naar **Optiesvoorkeuren** > **Preferences** > **Bewerken** > **Tijdstempel behouden** > **Preserve timestamp****uitschakelen** |
| Filezilla | Ga naar > **Timestampen overzetten van overgedragen bestanden** > **uitschakelen** **Transfer** |
|||

Wanneer een trigger een nieuw bestand vindt, controleert de trigger of het nieuwe bestand is voltooid en niet gedeeltelijk is geschreven. Een bestand kan bijvoorbeeld wijzigingen in uitvoering hebben wanneer de trigger de bestandsserver controleert. Om te voorkomen dat een gedeeltelijk geschreven bestand wordt teruggegeven, wordt in de trigger de tijdstempel voor het bestand met recente wijzigingen nota's aangebracht, maar wordt dat bestand niet onmiddellijk teruggegeven. De trigger retourneert het bestand alleen wanneer de server opnieuw wordt gepeild. Soms kan dit gedrag leiden tot een vertraging die tot twee keer het steminterval van de trigger is.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Uw FTP-hostserveradres en accountreferenties

  De FTP-connector vereist dat uw FTP-server toegankelijk is vanaf het internet en is ingesteld om in *passieve* modus te werken. Met uw referenties kan uw logische app een verbinding maken en toegang krijgen tot uw FTP-account.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt hebben tot uw FTP-account. Als u wilt beginnen met een FTP-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u een FTP-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="connect-to-ftp"></a>Verbinding maken met FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer.

1. Voer in het zoekvak in `ftp` als filter in het zoekvak in. Selecteer in de lijst **Triggers** de gewenste trigger.

   -of-

   Selecteer voor bestaande logische apps onder de laatste stap waarin u een actie wilt toevoegen, De optie **Nieuwe stap**en selecteer Vervolgens Een **actie toevoegen**. Voer in het `ftp` zoekvak in als filter. Selecteer **in** de lijst Acties de gewenste actie.

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen de stappen. Selecteer het plusteken (**+**) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen**.

1. Geef uw verbindingsgegevens op en selecteer **Maken**.

1. Geef de informatie op voor de geselecteerde trigger of actie en ga door met het bouwen van de workflow van uw logische app.

## <a name="examples"></a>Voorbeelden

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>FTP-trigger toevoegen

De **trigger Wanneer een bestand wordt toegevoegd of gewijzigd (alleen eigenschappen)** start een werkstroom van een logische app wanneer de trigger detecteert dat een bestand wordt toegevoegd of gewijzigd op een FTP-server. U bijvoorbeeld een voorwaarde toevoegen die de inhoud van het bestand controleert en beslist of die inhoud wordt verkregen, op basis van of die inhoud voldoet aan een bepaalde voorwaarde. Ten slotte u een actie toevoegen die de inhoud van het bestand krijgt en die inhoud in een andere map op de SFTP-server plaatsen.

U deze trigger bijvoorbeeld gebruiken om een FTP-map te controleren op nieuwe bestanden die klantorders beschrijven. U vervolgens een FTP-actie gebruiken, zoals **Metagegevens ophalen** om de eigenschappen voor dat nieuwe bestand op te halen en vervolgens **Bestandsinhoud** ophalen gebruiken om de inhoud uit dat bestand te halen voor verdere verwerking en die order op te slaan in een orderdatabase.

Hier is een voorbeeld dat laat zien hoe u de **trigger Wanneer een bestand wordt toegevoegd of gewijzigd (alleen eigenschappen)** gebruikt.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Voer in het zoekvak in `ftp` als filter in het zoekvak in. Selecteer onder de lijst triggers deze trigger: **Wanneer een ingediende wordt toegevoegd of gewijzigd (alleen eigenschappen)**

   ![De FTP-trigger zoeken en selecteren](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Geef de benodigde details op voor uw verbinding en selecteer **Vervolgens Maken**.

   Standaard worden bestanden in tekstvorm overgedragen met deze connector. Als u bestanden in binaire indeling wilt overbrengen, bijvoorbeeld waar en wanneer codering wordt gebruikt, selecteert u **Binair transport**.

   ![Verbinding maken met FTP-server](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. Selecteer **in** het vak Map het mappictogram zodat er een lijst wordt weergegeven. Als u de map wilt vinden die u wilt controleren voor**>** nieuwe of bewerkte bestanden, selecteert u de pijl met de juiste hoek ( ), bladert u naar die map en selecteert u de map.

   ![Map zoeken en selecteren om te controleren](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   De geselecteerde map wordt weergegeven in het vak **Map.**

   ![Geselecteerde map wordt weergegeven in de eigenschap 'Map'.](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Sla uw logische app op. Selecteer op de werkbalk van de ontwerper de optie **Opslaan**.

Nu uw logische app een trigger heeft, voegt u de acties toe die u wilt uitvoeren wanneer uw logische app een nieuw of bewerkt bestand vindt. In dit voorbeeld u een FTP-actie toevoegen die de nieuwe of bijgewerkte inhoud krijgt.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>FTP-actie toevoegen

Met de actie **Metagegevens voor bestanden downloaden** worden de eigenschappen op uw FTP-server geplaatst en met de actie **Bestandsinhoud downloaden** wordt de bestandsinhoud op basis van de informatie over dat bestand op uw FTP-server weergegeven. U bijvoorbeeld de trigger uit het vorige voorbeeld toevoegen en deze acties om de inhoud van het bestand te krijgen nadat dat bestand is toegevoegd of bewerkt.

1. Selecteer Nieuwe **stap**onder de trigger of andere acties .

1. Voer in het `ftp` zoekvak in als filter. Selecteer onder de lijst met acties de optie: **Metagegevens van bestanden downloaden**

   ![Selecteer de actie 'Metagegevens van bestanden downloaden'](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Als u al een verbinding hebt met uw FTP-server en -account, gaat u naar de volgende stap. Geef anders de benodigde details voor die verbinding op en selecteer **Vervolgens Maken**.

   ![FTP-serververbinding maken](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Nadat de actie **Metagegevens voor bestanden downloaden** is weergegeven, klikt u in het vak **Bestand** zodat de lijst met dynamische inhoud wordt weergegeven. U nu eigenschappen selecteren voor de uitvoer uit eerdere stappen. Selecteer in de lijst dynamische inhoud onder **Metagegevens van bestanden ophalen**de eigenschap **Lijst met bestanden-id,** die verwijst naar de verzameling waar het bestand is toegevoegd of bijgewerkt.

   ![De eigenschap 'Lijst met bestanden-id' zoeken en selecteren](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   De eigenschap **Lijst met bestanden-id** wordt nu weergegeven in het vak **Bestand.**

   ![Geselecteerde eigenschap 'Lijst met bestanden-id'](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Voeg nu deze FTP-actie toe: **Bestandsinhoud downloaden**

   ![De actie 'Bestandsinhoud downloaden' zoeken en selecteren](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Nadat de actie **Bestandsinhoud downloaden** is weergegeven, klikt u in het vak **Bestand** zodat de lijst met dynamische inhoud wordt weergegeven. U nu eigenschappen selecteren voor de uitvoer uit eerdere stappen. Selecteer in de lijst dynamische inhoud onder **Metagegevens van bestanden downloaden**de eigenschap **Id,** die verwijst naar het bestand dat is toegevoegd of bijgewerkt.

   ![De eigenschap 'Id' zoeken en selecteren](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   De eigenschap **Id** wordt nu weergegeven in het vak **Bestand.**

   ![Geselecteerde eigenschap 'Id'](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Sla uw logische app op.

## <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt controleren of uw werkstroom de inhoud retourneert die u verwacht, voegt u een andere actie toe die u de inhoud van het geüploade of bijgewerkte bestand verzendt.

1. Voeg onder de actie **Bestandsinhoud downloaden** een actie toe waarmee u de inhoud van het bestand kan verzenden. In dit voorbeeld wordt de actie **Een e-mail verzenden** voor Office 365 Outlook toegevoegd.

   ![Een actie toevoegen voor het verzenden van e-mail](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Geef de informatie op nadat de actie is weergegeven en de eigenschappen die u wilt testen. Voeg bijvoorbeeld de eigenschap **Bestandsinhoud** toe, die wordt weergegeven in de lijst met dynamische inhoud nadat u **Meer weergeven** hebt geselecteerd in de sectie **Bestandsinhoud downloaden.**

   ![Informatie geven over e-mailactie](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Sla uw logische app op. Als u de logische app wilt uitvoeren en activeren, selecteert u **Uitvoeren**en voegt u vervolgens een bestand toe aan de FTP-map die uw logische app nu monitort.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/ftpconnector/)de connector voor meer technische details over deze connector, zoals triggers, acties en limieten zoals beschreven in het Swagger-bestand van de connector.

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
