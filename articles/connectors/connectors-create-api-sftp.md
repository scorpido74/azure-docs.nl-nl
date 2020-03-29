---
title: Verbinding maken met SFTP-account
description: Werk en processen automatiseren die bestanden voor een SFTP-server via SSH bewaken, maken, beheren, verzenden en ontvangen met Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: divswa, klam, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: d0da98070fa8da5403677e1a67bda75456c74d80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789270"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>SFTP-bestanden bewaken, maken en beheren met behulp van Azure Logic Apps

> [!IMPORTANT]
> Gebruik de [SFTP-SSH-connector](../connectors/connectors-sftp-ssh.md) omdat de SFTP-connector is afgeschaft. U sftp-triggers en acties niet meer selecteren in de Logic App Designer.

Als u taken wilt automatiseren die bestanden bewaken, maken, verzenden en ontvangen op een [SFTP-server (Secure File Transfer Protocol),](https://www.ssh.com/ssh/sftp/) u integratiewerkstromen bouwen en automatiseren met Azure Logic Apps en de SFTP-connector. SFTP is een netwerkprotocol dat bestandstoegang, bestandsoverdracht en bestandsbeheer mogelijk maakt via elke betrouwbare gegevensstroom. Hier volgen enkele voorbeeldtaken die u automatiseren:

* Controleer wanneer bestanden worden toegevoegd of gewijzigd.
* Bestanden downloaden, maken, kopiëren, bijwerken, aanbieden en verwijderen.
* Krijg bestandsinhoud en metagegevens.
* Haal archieven naar mappen.

U triggers gebruiken die gebeurtenissen op uw SFTP-server controleren en uitvoer beschikbaar maken voor andere acties. U acties gebruiken die verschillende taken uitvoeren op uw SFTP-server. U ook andere acties in uw logica-app gebruiken voor de uitvoer van SFTP-acties. Als u bijvoorbeeld regelmatig bestanden ophaalt van uw SFTP-server, u e-mailwaarschuwingen over die bestanden en de inhoud ervan verzenden met behulp van de Office 365 Outlook-connector of Outlook.com connector. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limieten

De SFTP-connector verwerkt alleen bestanden die *50 MB of kleiner* zijn en geen ondersteuning bieden voor [berichtchunking.](../logic-apps/logic-apps-handle-large-messages.md) Voor grotere bestanden gebruikt u de [SFTP-SSH-connector.](../connectors/connectors-sftp-ssh.md) Voor verschillen tussen de SFTP-connector en de SFTP-SSH-connector, bekijkt u [SFTP-SSH versus SFTP](../connectors/connectors-sftp-ssh.md#comparison) in het SFTP-SSH-artikel.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Uw SFTP-serveradres en accountreferenties, waarmee uw logische app toegang heeft tot uw SFTP-account. Om het [Secure Shell (SSH)-protocol te](https://www.ssh.com/ssh/protocol/) gebruiken, hebt u ook toegang nodig tot een SSH-privésleutel en het SSH-wachtwoord voor privésleutels.

  > [!NOTE]
  >
  > De SFTP-connector ondersteunt deze private key-indelingen: OpenSSH, ssh.com en PuTTY
  >
  > Wanneer u uw logica-app maakt, moet u verbindingsgegevens voor uw SFTP-server verstrekken nadat u de gewenste SFTP-trigger of actie hebt toegevoegd. 
  > Als u een SSH-privésleutel gebruikt, controleert u of u de sleutel uit uw SSH-privésleutelbestand ***kopieert*** en ***plakt*** u die sleutel in de verbindingsgegevens, ***voert u de sleutel niet handmatig in of bewerkt***deze niet , waardoor de verbinding kan mislukken. 
  > Zie de latere stappen in dit artikel voor meer informatie.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt hebben tot uw SFTP-account. Als u wilt beginnen met een SFTP-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u een SFTP-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="how-sftp-triggers-work"></a>Hoe SFTP-triggers werken

SFTP triggers werken door polling het SFTP-bestandssysteem en op zoek naar een bestand dat is gewijzigd sinds de laatste poll. Met sommige hulpprogramma's u de tijdstempel behouden wanneer de bestanden worden gewijzigd. In deze gevallen moet u deze functie uitschakelen, zodat uw trigger kan werken. Hier zijn enkele veelvoorkomende instellingen:

| SFTP-client | Actie |
|-------------|--------|
| Winscp | Ga > naar **Optiesvoorkeuren** > **Preferences** > **Bewerken** > **Tijdstempel behouden** > **Preserve timestamp****uitschakelen** |
| Filezilla | Ga naar > **Timestampen overzetten van overgedragen bestanden** > **uitschakelen** **Transfer** |
|||

Wanneer een trigger een nieuw bestand vindt, controleert de trigger of het nieuwe bestand is voltooid en niet gedeeltelijk is geschreven. Een bestand kan bijvoorbeeld wijzigingen in uitvoering hebben wanneer de trigger de bestandsserver controleert. Om te voorkomen dat een gedeeltelijk geschreven bestand wordt teruggegeven, wordt in de trigger de tijdstempel voor het bestand met recente wijzigingen nota's aangebracht, maar wordt dat bestand niet onmiddellijk teruggegeven. De trigger retourneert het bestand alleen wanneer de server opnieuw wordt gepeild. Soms kan dit gedrag leiden tot een vertraging die tot twee keer het steminterval van de trigger is.

## <a name="connect-to-sftp"></a>Verbinding maken met SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Voer in het zoekvak 'sftp' in als filter. Selecteer onder de lijst triggers de gewenste trigger.

   -of-

   Kies **Nieuwe stap**voor bestaande logische apps onder de laatste stap waarin u een actie wilt toevoegen. Voer in het zoekvak 'sftp' in als filter. Selecteer onder de lijst met acties de gewenste actie.

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen de stappen. Kies het plusteken (**+**) dat wordt weergegeven en selecteer Vervolgens Een actie **toevoegen**.

1. Geef de nodige details voor uw verbinding.

   > [!IMPORTANT]
   >
   > Wanneer u uw SSH-privésleutel invoert in de **privésleuteleigenschap van SSH,** volgt u deze aanvullende stappen, die ervoor zorgen dat u de volledige en juiste waarde voor deze accommodatie geeft. 
   > Een ongeldige sleutel zorgt ervoor dat de verbinding mislukt.

   Hoewel u elke teksteditor gebruiken, zijn hier voorbeeldstappen die laten zien hoe u uw sleutel correct kopiëren en plakken met Behulp van Notepad.exe als voorbeeld.

   1. Open uw SSH-privésleutelbestand in een teksteditor. Deze stappen gebruiken Kladblok als voorbeeld.

   1. Selecteer **Alles selecteren**in het menu **Kladblok bewerken** .

   1. Selecteer**Kopiëren** **bewerken** > .

   1. Plak in de SFTP-trigger of actie die u hebt toegevoegd de *volledige* sleutel die u hebt gekopieerd in de **eigenschap SSH-privésleutel,** die meerdere regels ondersteunt. Zorg ervoor dat je de sleutel ***plakt.*** ***Voer de sleutel niet handmatig in of bewerk deze niet***handmatig.

1. Wanneer u klaar bent met het invoeren van de verbindingsgegevens, kiest u **Maken**.

1. Geef de benodigde details voor de geselecteerde trigger of actie en ga verder met het bouwen van de workflow van uw logische app.

## <a name="examples"></a>Voorbeelden

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP-trigger: wanneer een bestand wordt toegevoegd of gewijzigd

Met deze trigger wordt een werkstroom voor logische apps gestart wanneer een bestand wordt toegevoegd of gewijzigd op een SFTP-server. U bijvoorbeeld een voorwaarde toevoegen die de inhoud van het bestand controleert en de inhoud krijgt op basis van of de inhoud voldoet aan een bepaalde voorwaarde. U vervolgens een actie toevoegen die de inhoud van het bestand krijgt en die inhoud in een map op de SFTP-server plaatsen.

**Ondernemingsvoorbeeld:** U deze trigger gebruiken om een SFTP-map te controleren op nieuwe bestanden die klantorders vertegenwoordigen. U vervolgens een SFTP-actie gebruiken, zoals **Bestandsinhoud downloaden,** zodat u de inhoud van de bestelling krijgt voor verdere verwerking en die bestelling in een orderdatabase opslaat.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>SFTP-actie: inhoud opdoen

Met deze actie wordt de inhoud van een bestand op een SFTP-server opgehaald. U bijvoorbeeld de trigger uit het vorige voorbeeld toevoegen en een voorwaarde waaraan de inhoud van het bestand moet voldoen. Als de voorwaarde waar is, kan de actie waarin de inhoud wordt uitgevoerd.

## <a name="connector-reference"></a>Connector-verwijzing

Voor technische details over triggers, acties en limieten, die worden beschreven door de OpenAPI-beschrijving (voorheen Swagger) van de connector, raadpleegt u de [referentiepagina](/connectors/sftpconnector/)van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
