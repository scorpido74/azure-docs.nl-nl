---
title: Verbinding maken met SFTP-server met SSH
description: Automatiseer taken die bestanden voor een SFTP-server bewaken, maken, beheren, verzenden en ontvangen met Behulp van SSH en Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 04/13/2020
tags: connectors
ms.openlocfilehash: d7fafdd5830ec2825771d4d611a5f4bd5d87260a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393638"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>SFTP-bestanden bewaken, maken en beheren met SSH en Azure Logic Apps

Als u taken wilt automatiseren die bestanden op een [SFTP-server (Secure File Transfer Protocol)](https://www.ssh.com/ssh/sftp/) bewaken, maken, verzenden en ontvangen met behulp van het [Secure Shell-protocol (SSH),](https://www.ssh.com/ssh/protocol/) u integratieworkflows bouwen en automatiseren met Azure Logic Apps en de SFTP-SSH-connector. SFTP is een netwerkprotocol dat bestandstoegang, bestandsoverdracht en bestandsbeheer mogelijk maakt via elke betrouwbare gegevensstroom. Hier volgen enkele voorbeeldtaken die u automatiseren:

* Controleer wanneer bestanden worden toegevoegd of gewijzigd.
* Bestanden indownloaden, maken, kopiëren, hernoemen, bijwerken, aanbieden en verwijderen.
* Mappen maken.
* Krijg bestandsinhoud en metagegevens.
* Haal archieven naar mappen.

U triggers gebruiken die gebeurtenissen op uw SFTP-server controleren en uitvoer beschikbaar maken voor andere acties. U acties gebruiken die verschillende taken uitvoeren op uw SFTP-server. U ook andere acties in uw logica-app gebruiken voor de uitvoer van SFTP-acties. Als u bijvoorbeeld regelmatig bestanden ophaalt van uw SFTP-server, u e-mailwaarschuwingen over die bestanden en de inhoud ervan verzenden met behulp van de Office 365 Outlook-connector of Outlook.com connector. Als u nieuw bent in logische apps, controleert u [Wat is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Voor verschillen tussen de SFTP-SSH-connector en de SFTP-connector bekijk je de sectie [SFTP-SSH vergelijken versus SFTP](#comparison) later in dit onderwerp.

## <a name="limits"></a>Limieten

* SFTP-SSH-acties die [chunking](../logic-apps/logic-apps-handle-large-messages.md) ondersteunen, kunnen bestanden tot 1 GB verwerken, terwijl SFTP-SSH-acties die geen ondersteuning bieden voor chunking bestanden tot 50 MB kunnen verwerken. Hoewel de standaardchunkgrootte 15 MB is, kan deze grootte dynamisch veranderen, vanaf 5 MB en geleidelijk toenementot het maximum van 50 MB, op basis van factoren zoals netwerklatentie, responstijd van de server, enzovoort.

  > [!NOTE]
  > Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

  U dit adaptieve gedrag overschrijven wanneer u [een constante chunk-grootte opgeeft](#change-chunk-size) die u in plaats daarvan wilt gebruiken. Deze grootte kan variëren van 5 MB tot 50 MB. Stel dat u een bestand van 45 MB hebt en een netwerk dat die bestandsgrootte zonder latentie kan ondersteunen. Adaptieve chunking resulteert in meerdere gesprekken, in plaats van dat ene gesprek. Als u het aantal gesprekken wilt verminderen, u proberen een stukgrootte van 50 MB in te stellen. In een ander scenario u proberen de grootte te verkleinen tot 5 MB als uw logica-app bijvoorbeeld een time-out heeft.

  Chunk size is gekoppeld aan een verbinding, wat betekent dat u dezelfde verbinding gebruiken voor acties die chunking ondersteunen en vervolgens voor acties die geen ondersteuning bieden voor chunking. In dit geval varieert de grootte van de chunk voor acties die geen ondersteuning bieden voor chunking van 5 MB tot 50 MB. In deze tabel ziet u welke SFTP-SSH-acties chunking ondersteunen:

  | Actie | Ondersteuning voor chunking | Ondersteuning voor chunk size overschrijven |
  |--------|------------------|-----------------------------|
  | **Bestand kopiëren** | Nee | Niet van toepassing |
  | **Bestand maken** | Ja | Ja |
  | **Map maken** | Niet van toepassing | Niet van toepassing |
  | **Bestand verwijderen** | Niet van toepassing | Niet van toepassing |
  | **Archief naar map extraheren** | Niet van toepassing | Niet van toepassing |
  | **Bestandsinhoud downloaden** | Ja | Ja |
  | **Bestandsinhoud downloaden via pad** | Ja | Ja |
  | **Metagegevens van bestanden downloaden** | Niet van toepassing | Niet van toepassing |
  | **Bestandsmetagegevens downloaden via pad** | Niet van toepassing | Niet van toepassing |
  | **Bestanden in map weergeven** | Niet van toepassing | Niet van toepassing |
  | **Naamvan het bestand wijzigen** | Niet van toepassing | Niet van toepassing |
  | **Bestand bijwerken** | Nee | Niet van toepassing |
  ||||

* SFTP-SSH-triggers ondersteunen geen berichtenchunking. Wanneer u bestandsinhoud opvraagt, selecteren triggers alleen bestanden die 15 MB of kleiner zijn. Volg dit patroon om bestanden van meer dan 15 MB te krijgen:

  1. Gebruik een SFTP-SSH-trigger die alleen bestandseigenschappen retourneert, zoals **Wanneer een bestand wordt toegevoegd of gewijzigd (alleen eigenschappen).**

  1. Volg de trigger met de actie SFTP-SSH **Get file content,** die het volledige bestand leest en impliciet berichtchunking gebruikt.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Vergelijk SFTP-SSH versus SFTP

Hier volgen andere belangrijke verschillen tussen de SFTP-SSH-connector en de SFTP-connector waar de SFTP-SSH-connector deze mogelijkheden heeft:

* Maakt gebruik van de [SSH.NET-bibliotheek](https://github.com/sshnet/SSH.NET), een open-source Secure Shell -bibliotheek (SSH) die .NET ondersteunt.

* Hiermee wordt de actie **Map maken** weergegeven, waarmee een map wordt gemaakt op het opgegeven pad op de SFTP-server.

* Hiermee biedt u de bestandsactie **Hernoemen,** waarbij de naam van een bestand op de SFTP-server wordt gewijzigd.

* Hiermee caches de verbinding met de SFTP-server *tot 1 uur,* wat de prestaties verbetert en het aantal pogingen om verbinding te maken met de server vermindert. Als u de duur van dit cachinggedrag wilt instellen, bewerkt u de eigenschap [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) in de SSH-configuratie op uw SFTP-server.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Uw SFTP-serveradres en accountreferenties, waarmee uw logische app toegang heeft tot uw SFTP-account. U hebt ook toegang nodig tot een SSH-privésleutel en het ssh-wachtwoord voor privésleutels. Als u chunking wilt gebruiken bij het uploaden van grote bestanden, hebt u zowel lees- als schrijfmachtigingen nodig voor de hoofdmap op uw SFTP-server. Anders krijg je een foutmelding '401 Ongeautoriseerd'.

  > [!IMPORTANT]
  >
  > De SFTP-SSH-connector ondersteunt *alleen* deze privésleutelindelingen, algoritmen en vingerafdrukken:
  >
  > * **Private key formaten**: RSA (Rivest Shamir Adleman) en DSA (Digital Signature Algorithm) toetsen in zowel OpenSSH als ssh.com formaten. Als uw privésleutel zich in de bestandsindeling PuTTY (.ppk) bevindt, [converteert u eerst de sleutel naar de bestandsindeling OpenSSH (.pem).](#convert-to-openssh)
  >
  > * **Encryptie-algoritmen**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC en AES-256-CBC
  >
  > * **Vingerafdruk**: MD5
  >
  > Nadat u de SFTP-SSH-trigger of actie die u wilt toevoegen aan uw logische app, moet u verbindingsgegevens voor uw SFTP-server verstrekken. Wanneer u uw SSH-privésleutel voor deze verbinding opgeeft, ***voert u de sleutel niet handmatig in of bewerkt***deze deze, waardoor de verbinding kan mislukken. Zorg er in plaats daarvan voor dat u de sleutel uit uw SSH-privésleutelbestand ***kopieert*** en ***plakt*** deze sleutel in de verbindingsgegevens. 
  > Zie de sectie [Verbinding maken met SFTP met SSH](#connect) later dit artikel voor meer informatie.

* Basiskennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waar u toegang wilt hebben tot uw SFTP-account. Als u wilt beginnen met een SFTP-SSH-trigger, [maakt u een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u een SFTP-SSH-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de **recidieftrigger.**

## <a name="how-sftp-ssh-triggers-work"></a>Hoe SFTP-SSH-triggers werken

SFTP-SSH triggers werken door polling het SFTP-bestandssysteem en op zoek naar een bestand dat is gewijzigd sinds de laatste poll. Met sommige hulpprogramma's u de tijdstempel behouden wanneer de bestanden worden gewijzigd. In deze gevallen moet u deze functie uitschakelen, zodat uw trigger kan werken. Hier zijn enkele veelvoorkomende instellingen:

| SFTP-client | Actie |
|-------------|--------|
| Winscp | Ga > naar **Optiesvoorkeuren** > **Preferences** > **Bewerken** > **Tijdstempel behouden** > **Preserve timestamp****uitschakelen** |
| Filezilla | Ga naar > **Timestampen overzetten van overgedragen bestanden** > **uitschakelen** **Transfer** |
|||

Wanneer een trigger een nieuw bestand vindt, controleert de trigger of het nieuwe bestand is voltooid en niet gedeeltelijk is geschreven. Een bestand kan bijvoorbeeld wijzigingen in uitvoering hebben wanneer de trigger de bestandsserver controleert. Om te voorkomen dat een gedeeltelijk geschreven bestand wordt teruggegeven, wordt in de trigger de tijdstempel voor het bestand met recente wijzigingen nota's aangebracht, maar wordt dat bestand niet onmiddellijk teruggegeven. De trigger retourneert het bestand alleen wanneer de server opnieuw wordt gepeild. Soms kan dit gedrag leiden tot een vertraging die tot twee keer het steminterval van de trigger is.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>PuTTY-toets converteren naar OpenSSH

Als uw privésleutel in putty-indeling is, waarbij gebruik wordt gemaakt van de bestandsnaamextensie .ppk (PuTTY Private Key), converteert u eerst de sleutel naar de OpenSSH-indeling, die de extensie .pem (Privacy Enhanced Mail) gebruikt.

### <a name="unix-based-os"></a>Unix-gebaseerd besturingssysteem

1. Als de PuTTY-hulpprogramma's nog niet op uw systeem zijn geïnstalleerd, moet u dat nu doen, bijvoorbeeld:

   `sudo apt-get install -y putty`

1. Voer deze opdracht uit, waarmee een bestand wordt gemaakt dat u gebruiken met de SFTP-SSH-connector:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Bijvoorbeeld:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows OS

1. Als u dit nog niet hebt gedaan, [download dan de nieuwste PuTTY Generator (puttygen.exe) tool](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)en start de tool.

1. Selecteer **Laden**op dit scherm .

   ![Selecteer 'Laden'](./media/connectors-sftp-ssh/puttygen-load.png)

1. Blader naar uw privésleutelbestand in de PuTTY-indeling en selecteer **Openen**.

1. Selecteer **in** het menu Conversies de optie **OpenSSH-toets exporteren**.

   ![Selecteer 'OpenSSH-toets exporteren'](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Sla het privésleutelbestand `.pem` op met de bestandsnaamextensie.

## <a name="considerations"></a>Overwegingen

In deze sectie worden overwegingen beschreven die moeten worden gecontroleerd op de triggers en acties van deze connector.

<a name="create-file"></a>

### <a name="create-file"></a>Bestand maken

Als u een bestand op uw SFTP-server wilt maken, u de **bestandsactie** SFTP-SSH Maken gebruiken. Wanneer met deze actie het bestand wordt gestart, roept de Logic Apps-service ook automatisch uw SFTP-server aan om de metagegevens van het bestand te krijgen. Als u het nieuw gemaakte bestand echter verplaatst voordat de Logic Apps-service `404` kan bellen `'A reference was made to a file or folder which does not exist'`om de metagegevens op te halen, krijgt u een foutmelding, . Als u het lezen van de metagegevens van het bestand na het maken van bestanden wilt overslaan, voert u de stappen uit om [de eigenschap **Alle bestandsmetagegevens downloaden** in te stellen op **Nee**](#file-does-not-exist).

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Verbinding maken met SFTP met SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open uw logische app in Logic App Designer, als deze nog niet is geopend.

1. Voer in het zoekvak in `sftp ssh` als filter in het zoekvak in. Selecteer onder de lijst triggers de gewenste trigger.

   -of-

   Selecteer **Nieuwe stap**voor bestaande logische apps onder de laatste stap waarin u een actie wilt toevoegen . Voer in het `sftp ssh` zoekvak in als filter. Selecteer onder de lijst met acties de gewenste actie.

   Als u een actie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl tussen de stappen. Selecteer het plusteken (**+**) dat wordt weergegeven en selecteer vervolgens Een actie **toevoegen**.

1. Geef de nodige details voor uw verbinding.

   > [!IMPORTANT]
   >
   > Wanneer u uw SSH-privésleutel invoert in de **privésleuteleigenschap van SSH,** volgt u deze aanvullende stappen, die ervoor zorgen dat u de volledige en juiste waarde voor deze accommodatie geeft. Een ongeldige sleutel zorgt ervoor dat de verbinding mislukt.

   Hoewel u elke teksteditor gebruiken, zijn hier voorbeeldstappen die laten zien hoe u uw sleutel correct kopiëren en plakken met Behulp van Notepad.exe als voorbeeld.

   1. Open uw SSH-privésleutelbestand in een teksteditor. Deze stappen gebruiken Kladblok als voorbeeld.

   1. Selecteer **Alles selecteren**in het menu **Kladblok bewerken** .

   1. Selecteer**Kopiëren** **bewerken** > .

   1. Plak in de SFTP-SSH-trigger of actie die u hebt toegevoegd de *volledige* sleutel die u hebt gekopieerd in de **eigenschap SSH private key,** die meerdere regels ondersteunt.  Zorg ervoor dat je de sleutel ***plakt.*** ***Voer de sleutel niet handmatig in of bewerk deze niet***handmatig.

1. Wanneer u klaar bent met het invoeren van de verbindingsgegevens, selecteert u **Maken**.

1. Geef nu de nodige details voor de geselecteerde trigger of actie en ga verder met het bouwen van de workflow van uw logische app.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Chunk-grootte overschrijven

Als u het standaard adaptieve gedrag wilt overschrijven dat chunking gebruikt, u een constante chunk-grootte opgeven van 5 MB tot 50 MB.

1. Selecteer in de rechterbovenhoek van de actie de knop ellipsen (**... )** en selecteer **Vervolgens Instellingen**.

   ![SFTP-SSH-instellingen openen](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. Voer onder **Inhoudsoverdracht**in de eigenschap **Chunk-grootte** een gehele waarde in van `5` : `50` 

   ![Chunk-grootte opgeven die u wilt gebruiken](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Wanneer u klaar bent, selecteert u **Gereed**.

## <a name="examples"></a>Voorbeelden

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH-trigger: wanneer een bestand wordt toegevoegd of gewijzigd

Met deze trigger wordt een werkstroom voor logische apps gestart wanneer een bestand wordt toegevoegd of gewijzigd op een SFTP-server. U bijvoorbeeld een voorwaarde toevoegen die de inhoud van het bestand controleert en de inhoud krijgt op basis van of de inhoud voldoet aan een bepaalde voorwaarde. U vervolgens een actie toevoegen die de inhoud van het bestand krijgt en die inhoud in een map op de SFTP-server plaatsen.

**Ondernemingsvoorbeeld:** U deze trigger gebruiken om een SFTP-map te controleren op nieuwe bestanden die klantorders vertegenwoordigen. U vervolgens een SFTP-actie gebruiken, zoals **Bestandsinhoud downloaden,** zodat u de inhoud van de bestelling krijgt voor verdere verwerking en die bestelling in een orderdatabase opslaat.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP - SSH-actie: bestandsinhoud downloaden via pad

Met deze actie wordt de inhoud uit een bestand op een SFTP-server opgehaald door het bestandspad op te geven. U bijvoorbeeld de trigger uit het vorige voorbeeld toevoegen en een voorwaarde waaraan de inhoud van het bestand moet voldoen. Als de voorwaarde waar is, kan de actie waarin de inhoud wordt uitgevoerd.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-errors"></a>Problemen oplossen

In deze sectie worden mogelijke oplossingen voor veelvoorkomende fouten of problemen beschreven.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404-fout: "Er is verwezen naar een bestand of map die niet bestaat"

Deze fout kan optreden wanneer uw logica-app een nieuw bestand op uw SFTP-server maakt via de bestandsactie SFTP-SSH **Maken,** maar het nieuw gemaakte bestand wordt vervolgens onmiddellijk verplaatst voordat de Logic Apps-service de metagegevens van het bestand kan krijgen. Wanneer uw logische app de **actie Bestand maken** uitvoert, roept de Logic Apps-service ook automatisch uw SFTP-server op om de metagegevens van het bestand op te halen. Als het bestand echter wordt verplaatst, kan de Logic Apps-service `404` het bestand niet meer vinden, zodat u het foutbericht ontvangt.

Als u het verplaatsen van het bestand niet voorkomen of vertragen, u het lezen van de metagegevens van het bestand na het maken van bestanden overslaan door de volgende stappen te volgen:

1. Open in de actie **Bestand maken** de lijst Nieuwe **parameter toevoegen,** selecteer de eigenschap **Alle bestandsmetagegevens downloaden** en stel de waarde in op **Nee**.

1. Als u deze bestandsmetagegevens later nodig hebt, u de actie **Metagegevens voor bestanden downloaden** gebruiken.

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentiepagina van](https://docs.microsoft.com/connectors/sftpwithssh/)de connector voor meer technische details over deze connector, zoals triggers, acties en limieten zoals beschreven in het Swagger-bestand van de connector.

> [!NOTE]
> Voor logische apps in een [integratieserviceomgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)gebruikt de ISE-versie met HET LABEL ISE in plaats daarvan de [ISE-berichtlimieten.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic Apps-connectors](../connectors/apis-list.md)
