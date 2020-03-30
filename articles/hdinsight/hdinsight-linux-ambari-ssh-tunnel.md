---
title: SSH-tunneling gebruiken om toegang te krijgen tot Azure HDInsight
description: Meer informatie over het gebruik van een SSH-tunnel om veilig door webbronnen te bladeren die worden gehost op uw HDInsight-knooppunten op Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 6f4efd9a316b92f17f89cea66a7c81e84ac3cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72991352"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>SSH-tunneling gebruiken om toegang te krijgen tot apache Ambari web ui, jobgeschiedenis, namenode, Apache Oozie en andere UI's

HDInsight-clusters bieden toegang tot de Apache Ambari-webgebruikersinterface via internet, maar sommige functies vereisen een SSH-tunnel. De web-gebruikersinterface voor de Apache Oozie-service is bijvoorbeeld niet toegankelijk via internet zonder een SSh-tunnel.

## <a name="why-use-an-ssh-tunnel"></a>Waarom een SSH-tunnel gebruiken

Verschillende menu's in Ambari werken alleen door een SSH-tunnel. Deze menu's zijn afhankelijk van websites en services die worden uitgevoerd op andere knooppunttypen, zoals werknemersknooppunten.

De volgende Web-UI's vereisen een SSH-tunnel:

* JobGeschiedenis
* NameNode
* Threadstacks
* Oozie web Gebruikersinterface
* HBase Master en Logs UI

Als u Script-acties gebruikt om uw cluster aan te passen, vereisen services of hulpprogramma's die u installeert die een webservice blootleggen, een SSH-tunnel. Als u Hue bijvoorbeeld installeert met een Scriptactie, moet u een SSH-tunnel gebruiken om toegang te krijgen tot de Hue-webgebruikersinterface.

> [!IMPORTANT]  
> Als u rechtstreeks toegang hebt tot HDInsight via een virtueel netwerk, hoeft u geen SSH-tunnels te gebruiken. Zie voor een voorbeeld van rechtstreeks toegang tot HDInsight via een virtueel netwerk het [Connect HDInsight naar uw on-premises netwerkdocument.](connect-on-premises-network.md)

## <a name="what-is-an-ssh-tunnel"></a>Wat is een SSH-tunnel

[Secure Shell (SSH) tunneling](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) verbindt een poort op uw lokale machine met een hoofdknooppunt op HDInsight. Verkeer dat naar de lokale haven wordt verzonden, wordt omgeleid via een SSH-verbinding naar het hoofdknooppunt. De aanvraag wordt opgelost alsof deze afkomstig is op het hoofdknooppunt. Het antwoord wordt vervolgens door de tunnel naar uw werkstation gerouteerd.

## <a name="prerequisites"></a>Vereisten

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Een webbrowser die kan worden geconfigureerd om een SOCKS5-proxy te gebruiken.

    > [!WARNING]  
    > De SOCKS-proxy-ondersteuning die is ingebouwd in windows-internetinstellingen ondersteunt GEEN SOCKS5 en werkt niet met de stappen in dit document. De volgende browsers vertrouwen op Windows-proxy-instellingen en werken momenteel niet met de stappen in dit document:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome is ook afhankelijk van de Windows-proxy-instellingen. U echter extensies installeren die SOCKS5 ondersteunen. Wij raden [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Een tunnel maken met de opdracht SSH

Gebruik de volgende opdracht om een `ssh` SSH-tunnel te maken met behulp van de opdracht. Vervang `sshuser` door een SSH-gebruiker voor uw `CLUSTERNAME` HDInsight-cluster en vervang de naam van uw HDInsight-cluster:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

Met deze opdracht wordt een verbinding gemaakt die verkeer naar lokale poort 9876 naar het cluster over SSH leidt. De opties zijn:

* **D 9876** - De lokale haven die het verkeer door de tunnel leidt.
* **C** - Alle gegevens comprimeren, omdat webverkeer meestal tekst is.
* **2** - Forceer SSH om alleen protocolversie 2 te proberen.
* **q** - Stille modus.
* **T** - Schakel pseudo-tty toewijzing, omdat je gewoon het doorsturen van een poort.
* **n** - Voorkom het lezen van SOIN, omdat je gewoon een poort doorstuurt.
* **N** - Voer geen opdracht op afstand uit, omdat u alleen een poort doorstuurt.
* **f** - Ren op de achtergrond.

Zodra de opdracht is voltooid, wordt het verkeer dat naar poort 9876 op de lokale computer wordt verzonden, doorgestuurd naar het clusterhoofdknooppunt.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>Een tunnel maken met PuTTY

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) is een grafische SSH-client voor Windows. Als u niet bekend bent met PuTTY, raadpleegt u de [PuTTY-documentatie.](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html) Gebruik de volgende stappen om een SSH-tunnel te maken met PuTTY:

### <a name="create-or-load-a-session"></a>Een sessie maken of laden

1. Open PuTTY en zorg ervoor dat **Sessie** is geselecteerd in het linkermenu. Als u al een sessie hebt opgeslagen, selecteert u de sessienaam in de lijst **Opgeslagen sessies** en selecteert u **Laden**.

1. Als u nog geen opgeslagen sessie hebt, voert u uw verbindingsgegevens in:

    |Eigenschap |Waarde |
    |---|---|
    |Hostnaam (of IP-adres)|Het SSH-adres voor het HDInsight-cluster. Bijvoorbeeld **mijncluster-ssh.azurehdinsight.net**.|
    |Poort|22|
    |Verbindingstype|SSH|

1. Selecteer **Opslaan**

    ![HDInsight creëert stopverfsessie](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. Vouw in de sectie **Categorie** links van het dialoogvenster **Verbinding**uit, vouw **SSH**uit en selecteer **vervolgens Tunnels**.

1. Geef de volgende informatie over het **formulier Opties voor het doorsturen van ssh-poort:**

    |Eigenschap |Waarde |
    |---|---|
    |Bronpoort|De poort op de klant die u wilt doorsturen. Bijvoorbeeld **9876**.|
    |Doel|Het SSH-adres voor het HDInsight-cluster. Bijvoorbeeld **mijncluster-ssh.azurehdinsight.net**.|
    |Dynamisch|Maakt dynamische SOCKS proxy routing mogelijk.|

    ![Tunnelopties voor PuTTY-configuratie](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. Selecteer **Toevoegen** om de instellingen toe te voegen en selecteer **Openen** om een SSH-verbinding te openen.

1. Meld u aan bij de server wanneer u daarom wordt gevraagd.

## <a name="use-the-tunnel-from-your-browser"></a>Gebruik de tunnel vanuit uw browser

> [!IMPORTANT]  
> De stappen in deze sectie maken gebruik van de Mozilla FireFox-browser, omdat deze op alle platforms dezelfde proxy-instellingen biedt. Andere moderne browsers, zoals Google Chrome, vereisen mogelijk een extensie zoals FoxyProxy om met de tunnel te werken.

1. Configureer de browser om **localhost** en de poort te gebruiken die u hebt gebruikt bij het maken van de tunnel als een **SOCKS v5-proxy.** Zo zien de Firefox-instellingen eruit. Als u een andere poort dan 9876 hebt gebruikt, wijzigt u de poort in de poort die u hebt gebruikt:

    ![proxy-instellingen voor Firefox-browser](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > Als **u DNS (Remote DNS)** selecteert, worden DNS-aanvragen (Domain Name System) opgelost met behulp van het HDInsight-cluster. Met deze instelling wordt DNS opgelost met behulp van het hoofdknooppunt van het cluster.

2. Controleer of de tunnel werkt door [https://www.whatismyip.com/](https://www.whatismyip.com/)een site te bezoeken, zoals . Het geretourneerde IP-adres moet worden gebruikt door het Microsoft Azure-datacenter.

## <a name="verify-with-ambari-web-ui"></a>Verifiëren met de gebruikersinterface van het Ambari-web

Zodra het cluster is ingesteld, gebruikt u de volgende stappen om te controleren of u toegang hebt tot serviceweb-UI's vanaf het Ambari-web:

1. Ga in uw browser naar `http://headnodehost:8080`. Het `headnodehost` adres wordt over de tunnel naar het cluster verzonden en naar het hoofdknooppunt opgegaan waarop Ambari wordt uitgevoerd. Voer desgevraagd de gebruikersnaam (beheerder) en het wachtwoord voor uw cluster in. U een tweede keer worden gevraagd door de Gebruikersinterface van het Ambari-web. Voer de informatie opnieuw in.

   > [!NOTE]  
   > Wanneer u `http://headnodehost:8080` het adres gebruikt om verbinding te maken met het cluster, maakt u verbinding via de tunnel. Communicatie wordt beveiligd via de SSH-tunnel in plaats van HTTPS. Gebruik `https://clustername.azurehdinsight.net`, waar `clustername` is de naam van het cluster om verbinding te maken via het internet via het internet.

2. Selecteer HDFS in de lijst links van de pagina in de gebruikersinterface van Ambari.

    ![Apache Ambari hdfs-service geselecteerd](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. Wanneer de HDFS-servicegegevens worden weergegeven, selecteert u **Snelle koppelingen**. Er wordt een lijst met de clusterkopknooppunten weergegeven. Selecteer een van de hoofdknooppunten en selecteer **vervolgens namenode gebruikersinterface**.

    ![Afbeelding met het menu QuickLinks uitgebreid](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > Wanneer u __Snelle koppelingen selecteert,__ u een wachtindicator krijgen. Deze voorwaarde kan optreden als u een trage internetverbinding hebt. Wacht een minuut of twee tot de gegevens van de server zijn ontvangen en probeer de lijst opnieuw.
    >
    > Sommige vermeldingen in het menu **Snelle koppelingen** kunnen aan de rechterkant van het scherm worden afgesneden. Als dat het zo is, vouw je het menu uit met de muis en gebruik je de pijl-rechtsom het scherm naar rechts te scrollen om de rest van het menu te zien.

4. Er wordt een pagina weergegeven die lijkt op de volgende afbeelding:

    ![Afbeelding van de Gebruikersinterface van Hadoop NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Let op de URL voor deze pagina; het moet vergelijkbaar `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`zijn met . Deze URI gebruikt de interne volledig gekwalificeerde domeinnaam (FQDN) van het knooppunt en is alleen toegankelijk bij gebruik van een SSH-tunnel.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een SSH-tunnel maken en gebruiken, raadpleegt u het volgende document voor andere manieren om Ambari te gebruiken:

* [HDInsight-clusters beheren met Apache Ambari](hdinsight-hadoop-manage-ambari.md)
