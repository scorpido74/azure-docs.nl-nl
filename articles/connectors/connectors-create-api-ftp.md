---
title: Verbinding maken met FTP-server-Azure Logic Apps
description: Bestanden maken, bewaken en beheren op een FTP-server met Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: divswa, klam, LADocs
ms.topic: conceptual
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: ac6ae1a3b00a4e7568bd7967105f202fbf2e4f9b
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547495"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>FTP-bestanden maken, controleren en beheren met behulp van Azure Logic Apps

Met Azure Logic Apps en de FTP-connector kunt u geautomatiseerde taken en werk stromen maken om bestanden te maken, controleren, verzenden en ontvangen via uw account op een FTP-server, samen met andere acties, bijvoorbeeld:

* Controleren wanneer bestanden worden toegevoegd of gewijzigd.
* Bestanden downloaden, maken, kopiëren, bijwerken, weer geven en verwijderen.
* Bestands inhoud en meta gegevens ophalen.
* Haal archief naar mappen.

U kunt triggers gebruiken die reacties ontvangen van uw FTP-server en de uitvoer beschikbaar maken voor andere acties. U kunt acties uitvoeren in uw Logic apps gebruiken voor het beheren van bestanden op uw FTP-server. U kunt ook andere acties uitvoeren met de uitvoer van FTP-acties. Als u bijvoorbeeld regel matig bestanden van uw FTP-server ontvangt, kunt u e-mail over deze bestanden en hun inhoud verzenden met behulp van de Office 365 Outlook-Connector of de Outlook.com-connector. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limieten

* De FTP-connector ondersteunt alleen expliciete FTP via SSL (FTPS) en is niet compatibel met impliciete FTPS.

* FTP-acties kunnen standaard bestanden lezen of schrijven die *50 MB of kleiner*zijn. Voor het afhandelen van bestanden die groter zijn dan 50 MB, worden met FTP-bewerkingen [bericht chunks](../logic-apps/logic-apps-handle-large-messages.md)ondersteund. De actie **Bestands inhoud ophalen** maakt impliciet gebruik van Chunking.

* FTP-Triggers bieden geen ondersteuning voor het delen van segmenten. Bij het aanvragen van bestands inhoud selecteren triggers alleen bestanden van 50 MB of kleiner. Als u bestanden groter dan 50 MB wilt ophalen, volgt u dit patroon:

  * Gebruik een FTP-trigger die bestands eigenschappen retourneert, bijvoorbeeld **Wanneer een bestand wordt toegevoegd of gewijzigd (alleen eigenschappen)** .

  * Volg de trigger met de actie **Bestands inhoud** voor FTP ophalen, die het volledige bestand leest en impliciet gebruikmaakt van Chunking.

## <a name="how-ftp-triggers-work"></a>Hoe FTP-triggers werken

FTP-triggers worden uitgevoerd door het FTP-bestands systeem te pollen en te zoeken naar een bestand dat sinds de laatste poll is gewijzigd. Met sommige hulpprogram ma's kunt u de tijds tempel behouden wanneer de bestanden worden gewijzigd. In deze gevallen moet u deze functie uitschakelen zodat de trigger kan werken. Hier volgen enkele algemene instellingen:

| SFTP-client | Actie |
|-------------|--------|
| WinSCP | Ga naar **opties** > **voor keuren** > **overdracht** > **bewerken** > **tijds tempel behouden** > **uitschakelen** |
| FileZilla | Ga naar **overdracht** > de **tijds tempels van overgebrachte bestanden te behouden** > **uitschakelen** |
|||

Wanneer een trigger een nieuw bestand vindt, controleert de trigger of het nieuwe bestand is voltooid en niet gedeeltelijk is geschreven. Een bestand kan bijvoorbeeld wijzigingen in voortgang hebben wanneer de trigger de bestands server controleert. Om te voor komen dat een gedeeltelijk geschreven bestand wordt geretourneerd, wordt door de trigger de tijds tempel voor het bestand met recente wijzigingen weer gegeven, maar wordt dat bestand niet direct geretourneerd. De trigger retourneert het bestand alleen wanneer de server opnieuw wordt gecontroleerd. Dit gedrag kan soms een vertraging veroorzaken die twee maal het polling interval van de trigger is.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Het adres en de account referenties van de FTP-hostserver

  De FTP-connector vereist dat de FTP-server toegankelijk is vanaf internet en in de *passieve* modus kan worden uitgevoerd. Met uw referenties kunt u een verbinding maken met uw logische app en toegang krijgen tot uw FTP-account.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waartoe u toegang wilt krijgen tot uw FTP-account. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een FTP-trigger. Als u een FTP-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="connect-to-ftp"></a>Verbinding maken met FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Voor lege logische apps voert u in het zoekvak ' FTP ' in als uw filter. Selecteer de gewenste trigger onder de lijst met triggers.

   -of-

   Voor bestaande Logic apps selecteert u onder de laatste stap waar u een actie wilt toevoegen de optie **nieuwe stap**en vervolgens **een actie toevoegen**. Voer in het zoekvak ' FTP ' in als uw filter. Selecteer in de lijst acties de gewenste actie.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer **een actie toevoegen**.

1. Geef de benodigde gegevens voor de verbinding op en kies vervolgens **maken**.

1. Geef de benodigde gegevens op voor de geselecteerde trigger of actie en ga door met het bouwen van de werk stroom van uw logische app.

## <a name="examples"></a>Voorbeelden

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>FTP-trigger: wanneer een bestand wordt toegevoegd of gewijzigd

Deze trigger start een werk stroom voor logische apps wanneer de trigger detecteert wanneer een bestand wordt toegevoegd of gewijzigd op een FTP-server. U kunt bijvoorbeeld een voor waarde toevoegen waarmee de inhoud van het bestand wordt gecontroleerd en beslist of deze inhoud wordt opgehaald, op basis van de vraag of die inhoud voldoet aan een opgegeven voor waarde. Ten slotte kunt u een actie toevoegen waarmee de inhoud van het bestand wordt opgehaald en die inhoud in een map op de SFTP-server plaatsen.

**Enter prise-voor beeld**: u kunt deze trigger gebruiken om een FTP-map te bewaken voor nieuwe bestanden die klant orders beschrijven. U kunt vervolgens een FTP-actie gebruiken, zoals het **ophalen van bestands inhoud**, zodat u de inhoud van de bestelling kunt ophalen voor verdere verwerking en die order opslaan in een Data Base voor orders.

Hier volgt een voor beeld waarin deze trigger wordt weer gegeven: **Wanneer een bestand wordt toegevoegd of gewijzigd**

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Voor lege logische apps voert u in het zoekvak ' FTP ' in als uw filter. Selecteer in de lijst met triggers deze trigger: **Wanneer een ingediend wordt toegevoegd of gewijzigd-FTP**

   ![FTP-trigger zoeken en selecteren](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Geef de benodigde gegevens voor de verbinding op en kies vervolgens **maken**.

   Deze connector brengt standaard bestanden over in een tekst indeling. Als u bestanden wilt overdragen in binaire indeling, bijvoorbeeld wanneer en wanneer code ring wordt gebruikt, selecteert u **binair Trans Port**.

   ![FTP-server verbinding maken](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Klik naast het vak **map** op het mappictogram zodat er een lijst wordt weer gegeven. Als u de map wilt vinden die u wilt bewaken voor nieuwe of bewerkte bestanden, selecteert u de pijl-rechts ( **>** ), bladert u naar de map en selecteert u de map.

   ![Zoeken en selecteren welke map u wilt bewaken](./media/connectors-create-api-ftp/select-folder.png)  

   De geselecteerde map wordt weer gegeven in het vak **map** .

   ![Geselecteerde map](./media/connectors-create-api-ftp/selected-folder.png)  

Nu de logische app een trigger heeft, voegt u de acties toe die u wilt uitvoeren wanneer de logische app een nieuw of bewerkt bestand vindt. Voor dit voor beeld kunt u een FTP-actie toevoegen die de nieuwe of bijgewerkte inhoud ontvangt.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>FTP-actie: inhoud ophalen

Met deze actie wordt de inhoud van een bestand op een FTP-server opgehaald wanneer dat bestand wordt toegevoegd of bijgewerkt. U kunt bijvoorbeeld de trigger uit het vorige voor beeld toevoegen en een actie waarmee de inhoud van het bestand wordt opgehaald nadat het bestand is toegevoegd of bewerkt.

Hier volgt een voor beeld waarin deze actie wordt weer gegeven: **inhoud ophalen**

1. Kies **nieuwe stap**onder de trigger of een andere actie.

1. Voer in het zoekvak ' FTP ' in als uw filter. Selecteer in de lijst acties deze actie: **Bestands inhoud ophalen-FTP**

   ![FTP-actie selecteren](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Als u al een verbinding met uw FTP-server en account hebt, gaat u naar de volgende stap. Geef anders de benodigde gegevens op voor die verbinding en kies vervolgens **maken**.

   ![FTP-server verbinding maken](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Nadat de actie **Bestands inhoud ophalen** wordt geopend, klikt u in het vak **bestand** zodat de lijst met dynamische inhoud wordt weer gegeven. U kunt nu eigenschappen selecteren voor de uitvoer van de vorige stappen. Selecteer in de lijst met dynamische inhoud de eigenschap **Bestands inhoud** , die de inhoud bevat voor het toegevoegde of bijgewerkte bestand.  

   ![Bestand zoeken en selecteren](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   De eigenschap **Bestands inhoud** wordt nu weer gegeven in het vak **bestand** .

   ![Geselecteerde eigenschap bestands inhoud](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Sla uw logische app op. Als u uw werk stroom wilt testen, voegt u een bestand toe aan de FTP-map die nu wordt bewaakt door uw logische app.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina van de connector](/connectors/ftpconnector/)voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)