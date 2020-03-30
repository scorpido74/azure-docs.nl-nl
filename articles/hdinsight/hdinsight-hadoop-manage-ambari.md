---
title: Azure HDInsight bewaken en beheren met ambari webgebruikersinterface
description: Meer informatie over het gebruik van Ambari om HDInsight-clusters op basis van Linux te monitoren en te beheren. In dit document leert u hoe u de Ambari Web UI gebruikt die is opgenomen in HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: bf780897317d41c7da85140f64313546cf5c31d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064684"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>HDInsight-clusters beheren met behulp van de Apache Ambari-webinterface

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari vereenvoudigt het beheer en de monitoring van een Apache Hadoop-cluster door een gebruiksvriendelijke web-gebruikersinterface en REST API te bieden. Ambari is opgenomen in HDInsight-clusters en wordt gebruikt om het cluster te bewaken en configuratiewijzigingen aan te brengen.

In dit document leert u hoe u de Ambari Web UI gebruiken met een HDInsight-cluster.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>Wat is Apache Ambari?

[Apache Ambari](https://ambari.apache.org) vereenvoudigt hadoop beheer door het verstrekken van een eenvoudig te gebruiken web-ui. U Ambari gebruiken om Hadoop-clusters te beheren en te controleren. Ontwikkelaars kunnen deze mogelijkheden integreren in hun toepassingen met behulp van de [Ambari REST API's.](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

## <a name="connectivity"></a>Connectiviteit

De Ambari Web UI is beschikbaar `https://CLUSTERNAME.azurehdinsight.net`op `CLUSTERNAME` uw HDInsight-cluster op , waar is de naam van uw cluster.

> [!IMPORTANT]  
> Verbinding maken met Ambari op HDInsight vereist HTTPS. Wanneer u om verificatie wordt gevraagd, gebruikt u de naam en het wachtwoord van het beheerdersaccount dat u hebt opgegeven toen het cluster werd gemaakt. Als u niet om referenties wordt gevraagd, controleert u uw netwerkinstellingen om te controleren of er geen verbindingsprobleem is tussen de client en de Azure HDInsight-clusters.

## <a name="ssh-tunnel-proxy"></a>SSH-tunnel (proxy)

Hoewel Ambari voor uw cluster rechtstreeks via internet toegankelijk is, worden sommige links van de Ambari Web UI (zoals naar de JobTracker) niet op internet weergegeven. Om toegang te krijgen tot deze services, moet u een SSH-tunnel maken. Zie [SSH Tunneling gebruiken met HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)voor meer informatie.

## <a name="ambari-web-ui"></a>Ambari Web UI

> [!WARNING]  
> Niet alle functies van de Ambari Web UI worden ondersteund op HDInsight. Zie het gedeelte [Niet-ondersteunde bewerkingen](#unsupported-operations) van dit document voor meer informatie.

Wanneer u verbinding maakt met de Ambari Web UI, wordt u gevraagd zich te verifiëren op de pagina. Gebruik de clusterbeheerdergebruiker (standaardbeheerder) en het wachtwoord dat u tijdens het maken van het cluster hebt gebruikt.

Wanneer de pagina wordt geopend, noteer t.a.v. de balk bovenaan. Deze balk bevat de volgende informatie en besturingselementen:

![Overzicht van apache Ambari-dashboard](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Item |Beschrijving |
|---|---|
|Ambari-logo|Hiermee opent u het dashboard, dat kan worden gebruikt om het cluster te bewaken.|
|Clusternaam # ops|Hiermee wordt het aantal lopende Ambari-bewerkingen weergegeven. Als u de clusternaam of **#ops** selecteert, wordt een lijst met achtergrondbewerkingen weergegeven.|
|Waarschuwingen met #|Geeft eventuele waarschuwingen of kritieke waarschuwingen weer voor het cluster.|
|Dashboard|Hiermee geeft u het dashboard weer.|
|Services|Informatie- en configuratie-instellingen voor de services in het cluster.|
|Hosts|Informatie- en configuratie-instellingen voor de knooppunten in het cluster.|
|Waarschuwingen|Een logboek met informatie, waarschuwingen en kritieke waarschuwingen.|
|Beheerder|Softwarestack/services die zijn geïnstalleerd op het cluster, serviceaccountgegevens en Kerberos-beveiliging.|
|Knop Beheerder|Ambari-beheer, gebruikersinstellingen en afmelden.|

## <a name="monitoring"></a>Bewaking

### <a name="alerts"></a>Waarschuwingen

De volgende lijst bevat de algemene waarschuwingsstatussen die ambari gebruikt:

* **OK**
* **Waarschuwing**
* **Kritische**
* **Onbekende**

Waarschuwingen anders dan **OK** zorgen ervoor dat de vermelding **#waarschuwingen** boven aan de pagina het aantal waarschuwingen weergeeft. Als u dit item selecteert, worden de waarschuwingen en hun status weergegeven.

Waarschuwingen worden georganiseerd in verschillende standaardgroepen, die kunnen worden bekeken vanaf de pagina **Waarschuwingen.**

![Apache Ambari waarschuwt paginaoverzicht](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

U de groepen beheren door het menu **Acties te** gebruiken en **Waarschuwingsgroepen beheren te**selecteren.

![Apache Ambari beheert waarschuwingsgroepen](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

U ook waarschuwingsmethoden beheren en waarschuwingsmeldingen maken in het menu **Acties** door __Meldingen beheren te__selecteren. Alle huidige meldingen worden weergegeven. U ook meldingen maken vanaf hier. Meldingen kunnen worden verzonden via **e-mail** of **SNMP** wanneer specifieke waarschuwing / ernst combinaties optreden. U bijvoorbeeld een e-mailbericht verzenden wanneer een van de waarschuwingen in de groep **YARN-standaard** is ingesteld op **Kritiek**.

![Apache Ambari maken waarschuwingsmelding](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Als u ten slotte __Waarschuwingsinstellingen__ beheren selecteert in het menu __Acties,__ u instellen hoe vaak een waarschuwing moet plaatsvinden voordat een melding wordt verzonden. Deze instelling kan worden gebruikt om meldingen voor tijdelijke fouten te voorkomen.

Zie [E-mailmeldingen van Apache Ambari configureren in Azure HDInsight](./apache-ambari-email.md)voor een zelfstudie van een melding met een gratis [SendGrid-account.](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email)

### <a name="cluster"></a>Cluster

Het tabblad **Statistieken** van het dashboard bevat een reeks widgets waarmee u de status van uw cluster in één oogopslag controleren. Verschillende widgets, zoals **CPU-gebruik,** bieden aanvullende informatie wanneer erop wordt geklikt.

![Apache Ambari dashboard met statistieken](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

Op het tabblad **Heatmaps** worden statistieken weergegeven als gekleurde heatmaps, gaande van groen naar rood.

![Apache Ambari dashboard met heatmaps](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

Selecteer **Hosts**voor meer informatie over de knooppunten in het cluster. Selecteer vervolgens het specifieke knooppunt waarin u geïnteresseerd bent.

![Apache Ambari host overzichtsdetails](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Services

De **zijbalk van Services** op het dashboard geeft snel inzicht in de status van de services die op het cluster worden uitgevoerd. Verschillende pictogrammen worden gebruikt om de status of acties die moeten worden uitgevoerd aan te geven. Er wordt bijvoorbeeld een geel recyclesymbool weergegeven als een service moet worden gerecycled.

![Apache Ambari diensten zijbalk](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> De weergegeven services verschillen tussen HDInsight-clustertypen en -versies. De hier weergegeven services kunnen anders zijn dan de services die voor uw cluster worden weergegeven.

Als u een service selecteert, wordt meer gedetailleerde informatie over de service weergegeven.

![Informatie over apache Ambari-service](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Snelkoppelingen

Sommige services geven boven aan de pagina een koppeling **Snelle koppelingen** weer. Dit kan worden gebruikt om toegang te krijgen tot servicespecifieke web-UI's, zoals:

* **Taakgeschiedenis** - KaartVerlaag de taakgeschiedenis.
* **Resource Manager** - YARN ResourceManager UI.
* **NameNode** - Hadoop Distributed File System (HDFS) NameNode UI.
* **Oozie Web UI** - Oozie UI.

Als u een van deze koppelingen selecteert, wordt een nieuw tabblad in uw browser geopend, waarin de geselecteerde pagina wordt weergegeven.

> [!NOTE]  
> Als u het item **Snelle koppelingen** voor een service selecteert, kan een fout 'server niet gevonden' worden weergegeven. Als u deze fout tegenkomt, moet u een SSH-tunnel gebruiken wanneer u de vermelding **Snelle koppelingen** voor deze service gebruikt. Zie [SSH-tunneling gebruiken met HDInsight voor](hdinsight-linux-ambari-ssh-tunnel.md) meer informatie

## <a name="management"></a>Beheer

### <a name="ambari-users-groups-and-permissions"></a>Ambari-gebruikers, groepen en machtigingen

Werken met gebruikers, groepen en machtigingen wordt ondersteund bij het gebruik van een [domein dat is gekoppeld aan](./domain-joined/hdinsight-security-overview.md) hdinsight-cluster. Zie [HdInsight-clusters](./domain-joined/hdinsight-security-overview.md)beheren van domeinen voor informatie over het gebruik van de gebruikersinterface van Ambari Management op een cluster met domein.

> [!WARNING]  
> Wijzig het wachtwoord van de Ambari-waakhond (hdinsightwatchdog) niet op uw Linux-gebaseerde HDInsight-cluster. Als u het wachtwoord wijzigt, u scriptacties gebruiken of schaalbewerkingen uitvoeren met uw cluster.

### <a name="hosts"></a>Hosts

Op de pagina **Hosts** worden alle hosts in het cluster weergegeven. Voer deze stappen uit om hosts te beheren.

![Apache Ambari host paginaoverzicht](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> Het toevoegen, ontmantelen en opnieuw in bedrijf stellen van een host mag niet worden gebruikt met HDInsight-clusters.

1. Selecteer de host die u wilt beheren.

2. Gebruik het menu **Acties** om de actie te selecteren die u wilt uitvoeren:

    |Item |Beschrijving |
    |---|---|
    |Alle onderdelen starten|Start alle onderdelen op de host.|
    |Alle onderdelen stoppen|Stop alle onderdelen op de host.|
    |Alle onderdelen opnieuw starten|Stop en start alle onderdelen op de host.|
    |Onderhoudsmodus inschakelen|Onderdrukt waarschuwingen voor de host. Deze modus moet worden ingeschakeld als u acties uitvoert die waarschuwingen genereren. Bijvoorbeeld stoppen en starten van een service.|
    |Onderhoudsmodus uitschakelen|Geeft als resultaat de host bij normale waarschuwingen.|
    |Stoppen|Hiermee stopt DataNode of NodeManagers op de host.|
    |Starten|Hiermee start u DataNode of NodeManagers op de host.|
    |Opnieuw starten|Stopt en start DataNode of NodeManagers op de host.|
    |Ontmantelen|Hiermee verwijdert u een host uit het cluster. **Gebruik deze actie niet op HDInsight-clusters.**|
    |Herinbedrijfstelling|Hiermee voegt u een eerder buiten bedrijf gesteld host toe aan het cluster. **Gebruik deze actie niet op HDInsight-clusters.**|

### <a name="services"></a><a id="service"></a>Diensten

Gebruik op de pagina **Dashboard** of **Services** de knop **Acties** onder aan de lijst met services om alle services te stoppen en te starten.

![Lijst met Apache Ambari-serviceacties](./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png)

> [!WARNING]  
> Hoewel **Add Service** in dit menu wordt vermeld, mag deze niet worden gebruikt om services toe te voegen aan het HDInsight-cluster. Nieuwe services moeten worden toegevoegd met behulp van een scriptactie tijdens het inrichten van het cluster. Zie [HDInsight-clusters aanpassen met Scriptacties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over het gebruik van Script-acties.

Hoewel de knop **Acties** alle services opnieuw kan starten, wilt u vaak een specifieke service starten, stoppen of opnieuw starten. Gebruik de volgende stappen om acties uit te voeren op een afzonderlijke service:

1. Selecteer een service op de pagina **Dashboard** of **Services.**

2. Gebruik boven aan het tabblad **Overzicht** de knop **Serviceacties** en selecteer de actie die u wilt uitvoeren. Hiermee wordt de service opnieuw gestart op alle knooppunten.

    ![Individuele serviceacties apache Ambari](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Als u sommige services opnieuw start terwijl het cluster wordt uitgevoerd, kunnen waarschuwingen worden gegenereerd. Om waarschuwingen te voorkomen, u de knop **Serviceacties** gebruiken om de **onderhoudsmodus** voor de service in te schakelen voordat u de herstart uitvoert.

3. Zodra een actie is geselecteerd, wordt het **op-item # op** boven aan de paginastappen weergegeven om aan te geven dat er een achtergrondbewerking plaatsvindt. Als deze is geconfigureerd om weer te geven, wordt de lijst met achtergrondbewerkingen weergegeven.

   > [!NOTE]  
   > Als u **de onderhoudsmodus** voor de service hebt ingeschakeld, moet u deze uitschakelen door de knop **Serviceacties** te gebruiken zodra de bewerking is voltooid.

Als u een service wilt configureren, gebruikt u de volgende stappen:

1. Selecteer een service op de pagina **Dashboard** of **Services.**

2. Selecteer het tabblad **Configs.** De huidige configuratie wordt weergegeven. Er wordt ook een lijst met eerdere configuraties weergegeven.

    ![Apache Ambari-serviceconfiguratie](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. Gebruik de weergegeven velden om de configuratie te wijzigen en selecteer **Opslaan**. Of selecteer een vorige configuratie en selecteer **Vervolgens Huidig maken** om terug te keren naar de vorige instellingen.

## <a name="ambari-views"></a>Ambari-weergaven

Met Ambari-weergaven kunnen ontwikkelaars ui-elementen aansluiten op de Gebruikersinterface van Ambari met behulp van het [Apache Ambari Views Framework](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight biedt de volgende weergaven met Hadoop-clustertypen:

* Hive View: Met de Hive View u Hive-query's rechtstreeks vanuit uw webbrowser uitvoeren. U query's opslaan, resultaten bekijken, resultaten opslaan in de clusteropslag of resultaten downloaden naar uw lokale systeem. Zie [Apache Hive-weergaven gebruiken met HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)voor meer informatie over het gebruik van Hive-weergaven.

* Tez View: Met de Tez View u taken beter begrijpen en optimaliseren. U informatie bekijken over hoe Tez-taken worden uitgevoerd en welke resources worden gebruikt.

## <a name="unsupported-operations"></a>Niet-ondersteunde bewerkingen

De volgende Ambari-bewerkingen worden niet ondersteund op HDInsight:

* __De service Metrics Collector verplaatsen__. Wanneer u informatie bekijkt over de service Metrics Collector, is een van de acties die beschikbaar zijn in het menu Serviceacties __het verzamelprogramma voor statistieken verplaatsen.__ Dit wordt niet ondersteund met HDInsight.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) met HDInsight.
