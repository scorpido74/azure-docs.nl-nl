---
title: Verbinding maken met de SFTP-server met SSH-Azure Logic Apps
description: Taken automatiseren voor het bewaken, maken, beheren, verzenden en ontvangen van bestanden voor een SFTP-server met behulp van SSH en Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: 33c6007ebc429bb0d95d702ae9b90f9ac411a88c
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695197"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>SFTP-bestanden bewaken, maken en beheren met SSH en Azure Logic Apps

U kunt met behulp van het SSH-protocol [(Secure Shell)](https://www.ssh.com/ssh/protocol/) voor het automatiseren van taken voor het bewaken, maken, verzenden en ontvangen van bestanden op een [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) -server door het gebruik van Azure Logic apps en de SFTP-SSH te automatiseren connector. SFTP is een netwerk protocol dat toegang biedt tot bestanden, bestands overdracht en bestands beheer via elke betrouw bare gegevens stroom. Hier volgen enkele voor beelden van taken die u kunt automatiseren:

* Controleren wanneer bestanden worden toegevoegd of gewijzigd.
* Bestanden downloaden, maken, kopiëren, een andere naam geven, bijwerken, weer geven en verwijderen.
* Mappen maken.
* Bestands inhoud en meta gegevens ophalen.
* Haal archief naar mappen.

U kunt triggers gebruiken die gebeurtenissen op uw SFTP-server controleren en uitvoer beschikbaar maken voor andere acties. U kunt acties gebruiken waarmee verschillende taken op uw SFTP-server worden uitgevoerd. U kunt ook andere acties in uw logische app gebruiken de uitvoer van SFTP-acties. Als u bijvoorbeeld regel matig bestanden van uw SFTP-server ophaalt, kunt u e-mail waarschuwingen over die bestanden en hun inhoud verzenden met behulp van de Office 365 Outlook Connector of de Outlook.com-connector. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

Zie de sectie [SFTP-SSH versus SFTP vergelijken](#comparison) verderop in dit onderwerp voor verschillen tussen de SFTP-SSH-connector en de SFTP-connector.

## <a name="limits"></a>Limieten

* Met SFTP-SSH-acties kunnen standaard bestanden worden gelezen of geschreven die *1 GB of kleiner* zijn, maar slechts in *15 MB* segmenten tegelijk. Voor het afhandelen van bestanden die groter zijn dan 15 MB, worden met SFTP-SSH-acties [bericht chunks](../logic-apps/logic-apps-handle-large-messages.md)ondersteund, met uitzonde ring van de actie bestand kopiëren, waarmee slechts 15 MB bestanden kunnen worden verwerkt. De actie **Bestands inhoud ophalen** maakt impliciet gebruik van bericht Chunking.

* SFTP-SSH-Triggers bieden geen ondersteuning voor segmentering. Bij het aanvragen van bestands inhoud selecteren triggers alleen bestanden die 15 MB of kleiner zijn. Als u bestanden groter dan 15 MB wilt ophalen, volgt u dit patroon:

  * Gebruik een SFTP-SSH-trigger die bestands eigenschappen retourneert, bijvoorbeeld **Wanneer een bestand wordt toegevoegd of gewijzigd (alleen eigenschappen)** .

  * Volg de trigger met de bewerking voor het **ophalen van bestands inhoud** van SFTP-SSH, die het volledige bestand leest en impliciet gebruikmaakt van het segmenteren van berichten.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH versus SFTP vergelijken

Hier volgen andere belang rijke verschillen tussen de SFTP-SSH-connector en de SFTP-connector waarbij de SFTP-SSH-connector over de volgende mogelijkheden beschikt:

* Maakt gebruik van de [SSH.net-bibliotheek](https://github.com/sshnet/SSH.NET), een open-source SSH-bibliotheek (Secure Shell) die .net ondersteunt.

* Met SFTP-SSH-acties kunnen standaard bestanden worden gelezen of geschreven die *1 GB of kleiner* zijn, maar slechts in *15 MB* segmenten tegelijk. Voor het verwerken van bestanden die groter zijn dan 15 MB, kunnen SFTP-SSH-acties gebruikmaken van [bericht Chunking](../logic-apps/logic-apps-handle-large-messages.md). De actie bestand kopiëren ondersteunt echter slechts 15 MB aan bestanden omdat die actie geen ondersteuning biedt voor het segmenteren van berichten. SFTP-SSH-Triggers bieden geen ondersteuning voor segmentering.

* Biedt de actie **map maken** , waarmee een map op het opgegeven pad op de sftp-server wordt gemaakt.

* Biedt de actie **Bestands naam wijzigen** , waarmee een bestand op de sftp-server wordt hernoemd.

* Hiermee wordt de verbinding met de SFTP-server *Maxi maal 1 uur*in de cache opgeslagen, waardoor de prestaties worden verbeterd en het aantal pogingen om verbinding te maken met de server wordt verminderd. Als u de duur voor dit cache gedrag wilt instellen, bewerkt u de eigenschap [ **' ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) in de SSH-configuratie op de sftp-server.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Uw SFTP-server adres en account referenties, waarmee uw logische app toegang kan krijgen tot uw SFTP-account. U hebt ook toegang nodig tot een persoonlijke SSH-sleutel en het wacht woord voor de persoonlijke SSH-sleutel.

  > [!IMPORTANT]
  >
  > De SFTP-SSH-connector ondersteunt *alleen* deze indelingen, algoritmen en vinger afdrukken van persoonlijke sleutels:
  >
  > * **Indelingen van persoonlijke sleutels**: RSA-sleutels (Rivest Shamir Adleman) en DSA (Digital Signature Algorithm) in zowel de OpenSSH-als de ssh.com-indeling. Als uw persoonlijke sleutel zich in de PuTTy-bestands indeling (. ppk) bevindt, [moet u eerst de sleutel converteren naar de OpenSSH-bestands indeling (. pem)](#convert-to-openssh).
  >
  > * **Versleutelings algoritmen**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC en AES-256-CBC
  >
  > * **Vinger afdruk**: MD5
  >
  > Nadat u de SFTP-SSH-trigger of actie die u wilt toevoegen aan uw logische app hebt toegevoegd, moet u verbindings gegevens voor uw SFTP-server opgeven. Wanneer u uw persoonlijke SSH-sleutel voor deze verbinding opgeeft, ***hoeft u de sleutel niet hand matig in te voeren of te bewerken***. Dit kan ertoe leiden dat de verbinding mislukt. Zorg er in plaats daarvan voor dat u ***de sleutel kopieert*** uit uw persoonlijke SSH-sleutel bestand en ***plak*** die sleutel in de verbindings gegevens. 
  > Zie de sectie [verbinding maken met SFTP met SSH verderop in](#connect) dit artikel voor meer informatie.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt krijgen tot uw SFTP-account. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een SFTP-SSH-trigger. Als u een SFTP-SSH-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="how-sftp-ssh-triggers-work"></a>Hoe SFTP-SSH-triggers werken?

SFTP-SSH-triggers werken door het SFTP-bestands systeem te pollen en te zoeken naar een bestand dat sinds de laatste poll is gewijzigd. Met sommige hulpprogram ma's kunt u de tijds tempel behouden wanneer de bestanden worden gewijzigd. In deze gevallen moet u deze functie uitschakelen zodat de trigger kan werken. Hier volgen enkele algemene instellingen:

| SFTP-client | Action |
|-------------|--------|
| WinSCP | Ga naar **opties** > **voor keuren** > **overdracht** > **bewerken** > **tijds tempel voor behouden** > **uitschakelen** |
| FileZilla | Ga naar **overdracht** > **tijds tempels van overgebrachte bestanden behouden** > **uitschakelen** |
|||

Wanneer een trigger een nieuw bestand vindt, controleert de trigger of het nieuwe bestand is voltooid en niet gedeeltelijk is geschreven. Een bestand kan bijvoorbeeld wijzigingen in voortgang hebben wanneer de trigger de bestands server controleert. Om te voor komen dat een gedeeltelijk geschreven bestand wordt geretourneerd, wordt door de trigger de tijds tempel voor het bestand met recente wijzigingen weer gegeven, maar wordt dat bestand niet direct geretourneerd. De trigger retourneert het bestand alleen wanneer de server opnieuw wordt gecontroleerd. Dit gedrag kan soms een vertraging veroorzaken die twee maal het polling interval van de trigger is.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Op PuTTy gebaseerde sleutel converteren naar OpenSSH

Als uw persoonlijke sleutel zich in de PuTTy-indeling bevindt, waarbij de bestandsnaam extensie. ppk (PuTTy persoonlijke sleutel) wordt gebruikt, moet u eerst de sleutel converteren naar de OpenSSH-indeling. Dit maakt gebruik van de bestandsnaam extensie. pem (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>Op UNIX gebaseerd besturings systeem

1. Als de PuTTy-hulpprogram ma's niet al op uw systeem zijn geïnstalleerd, doet u dat nu bijvoorbeeld:

   `sudo apt-get install -y putty`

1. Voer deze opdracht uit om een bestand te maken dat u met de SFTP-SSH-connector kunt gebruiken:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Bijvoorbeeld:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows-besturingssysteem

1. Als u dit nog niet hebt gedaan, [downloadt u het hulp programma voor de nieuwste putty-Generator (puttygen. exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)en start u het hulp programma.

1. Selecteer **laden**op dit scherm.

   ![Selecteer laden](./media/connectors-sftp-ssh/puttygen-load.png)

1. Blader naar uw persoonlijke sleutel bestand in de PuTTy-indeling en selecteer **openen**.

1. Selecteer in het menu **conversies** de optie **OpenSSH key**.

   ![Selecteer ' OpenSSH-sleutel exporteren '](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Sla het bestand met de persoonlijke sleutel op met de bestandsnaam extensie `.pem`.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Verbinding maken met SFTP met SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Voor lege logische apps voert u in het zoekvak ' sftp SSH ' in als uw filter. Selecteer de gewenste trigger onder de lijst met triggers.

   -of-

   Voor bestaande Logic apps, onder de laatste stap waar u een actie wilt toevoegen, kiest u **nieuwe stap**. Voer in het zoekvak ' sftp SSH ' in als uw filter. Selecteer in de lijst acties de gewenste actie.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Geef de benodigde gegevens voor de verbinding op.

   > [!IMPORTANT]
   >
   > Wanneer u uw persoonlijke SSH-sleutel in de **persoonlijke SSH-sleutel** eigenschap invoert, voert u de volgende aanvullende stappen uit om ervoor te zorgen dat u de volledige en juiste waarde voor deze eigenschap opgeeft. Een ongeldige sleutel zorgt ervoor dat de verbinding mislukt.

   Hoewel u een tekst editor kunt gebruiken, zijn hier voorbeeld stappen die laten zien hoe u de sleutel correct kopieert en plakt met behulp van Notepad. exe als voor beeld.

   1. Open uw persoonlijke SSH-sleutel bestand in een tekst editor. In deze stappen wordt Klad blok als voor beeld gebruikt.

   1. Selecteer in het menu **bewerken** in Klad blok de optie **Alles selecteren**.

   1. Selecteer  > -**exemplaar** **bewerken**.

   1. Plak de *volledige* sleutel die u hebt gekopieerd in de eigenschap van de **persoonlijke SSH-sleutel** , die ondersteuning biedt voor meerdere regels in de SFTP-SSH-trigger of de actie die u hebt toegevoegd.  ***Zorg ervoor dat u*** de sleutel plakt. ***Voer de sleutel niet hand matig in of bewerk deze***.

1. Wanneer u klaar bent met het invoeren van de verbindings gegevens, kiest u **maken**.

1. Geef nu de gegevens op die nodig zijn voor de geselecteerde trigger of actie en blijf de werk stroom van uw logische app bouwen.

## <a name="examples"></a>Voorbeelden

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP-SSH-trigger: Wanneer een bestand wordt toegevoegd of gewijzigd

Deze trigger start een werk stroom voor logische apps wanneer een bestand wordt toegevoegd aan of gewijzigd op een SFTP-server. U kunt bijvoorbeeld een voor waarde toevoegen waarmee de inhoud van het bestand wordt gecontroleerd en de inhoud wordt opgehaald op basis van het feit of de inhoud voldoet aan een opgegeven voor waarde. U kunt vervolgens een actie toevoegen waarmee de inhoud van het bestand wordt opgehaald en die inhoud in een map op de SFTP-server plaatsen.

**Bedrijfs voorbeeld**: U kunt deze trigger gebruiken om een SFTP-map te bewaken voor nieuwe bestanden die klant orders vertegenwoordigen. U kunt vervolgens een SFTP-actie gebruiken, zoals het **ophalen van bestands inhoud** , zodat u de inhoud van de bestelling krijgt voor verdere verwerking en die order in een Data Base orders opslaat.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP-SSH-actie: Inhoud ophalen met behulp van pad

Met deze actie wordt de inhoud van een bestand op een SFTP-server opgehaald. U kunt bijvoorbeeld de trigger uit het vorige voor beeld toevoegen en een voor waarde waaraan de inhoud van het bestand moet worden voldaan. Als de voor waarde waar is, kan de actie die de inhoud ophaalt worden uitgevoerd.

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/sftpconnector/)van de connector voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps](../connectors/apis-list.md) -connectors
