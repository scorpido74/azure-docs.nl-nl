---
title: Installeer Jupyter lokaal en maak verbinding met Spark in Azure HDInsight
description: Meer informatie over het lokaal installeren van Jupyter-notitieblok op uw computer en het aansluiten op een Apache Spark-cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/02/2020
ms.openlocfilehash: 1d044ddaea0a2c7a1d489523cc9aa4515df0728a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632662"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Installeer Jupyter-laptop op uw computer en maak verbinding met Apache Spark op HDInsight

In dit artikel leert u hoe u Jupyter-laptop installeren met de aangepaste PySpark -kernels (voor Python) en Apache Spark (voor Scala) met Spark-magie. Vervolgens sluit u het notitieblok aan op een HDInsight-cluster.

Er zijn vier belangrijke stappen die betrokken zijn bij het installeren van Jupyter en het aansluiten op Apache Spark op HDInsight.

* Spark-cluster configureren.
* Installeer Jupyter notebook.
* Installeer de PySpark- en Spark-kernels met de Spark-magie.
* Configureer Spark-magie om toegang te krijgen tot spark-cluster op HDInsight.

Zie [Kernels beschikbaar voor Jupyter-laptops met Apache Spark Linux-clusters op HDInsight voor](apache-spark-jupyter-notebook-kernels.md)meer informatie over aangepaste kernels en Spark-magie.

## <a name="prerequisites"></a>Vereisten

* Een Apache Spark-cluster in HDInsight. Zie [Apache Spark-clusters maken in Azure HDInsight](apache-spark-jupyter-spark-sql.md) voor instructies. De lokale notebook maakt verbinding met het HDInsight-cluster.

* Weten hoe u Jupyter Notebook gebruikt met Spark on HDInsight.

## <a name="install-jupyter-notebook-on-your-computer"></a>Jupyter-laptop op uw computer installeren

Installeer Python voordat u Jupyter-notitieblokken installeert. De [Anaconda-distributie](https://www.anaconda.com/download/) installeert zowel Python als Jupyter Notebook.

Download de [Anaconda installer](https://www.anaconda.com/download/) voor uw platform en voer de setup uit. Controleer tijdens het uitvoeren van de wizard Setup of u de optie selecteert om Anaconda toe te voegen aan de VARIABELE PATH.  Zie ook, [Het installeren van Jupyter met behulp van Anaconda](https://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-spark-magic"></a>Spark-magie installeren

1. Voer een van de onderstaande opdrachten in om Spark-magie te installeren. Zie ook, [sparkmagic documentatie](https://github.com/jupyter-incubator/sparkmagic#installation).

    |Clusterversie | Installatie, opdracht |
    |---|---|
    |v3.6 en v3.5 |`pip install sparkmagic==0.13.1`|
    |v3.4|`pip install sparkmagic==0.2.3`|

1. Zorg `ipywidgets` ervoor dat de volgende opdracht correct is geïnstalleerd:

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>PySpark- en Spark-kernels installeren

1. Bepaal `sparkmagic` waar de installatie is geïnstalleerd door de volgende opdracht in te voeren:

    ```cmd
    pip show sparkmagic
    ```

    Wijzig vervolgens uw werkmap in de **locatie** die met de bovenstaande opdracht is geïdentificeerd.

1. Voer in uw nieuwe werkmap een of meer van de onderstaande opdrachten in om de gewenste kernel(s) te installeren:

    |Kernel | Opdracht |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR (SparkR)|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark (PySpark)|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3 (PySpark3)|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. Optioneel. Voer de onderstaande opdracht in om de serverextensie in te schakelen:

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Spark-magie configureren om verbinding te maken met hdInsight Spark-cluster

In deze sectie configureert u de Spark-magie die u eerder hebt geïnstalleerd om verbinding te maken met een Apache Spark-cluster.

1. Start de python-shell met de volgende opdracht:

    ```cmd
    python
    ```

2. De Jupyter-configuratie-informatie wordt meestal opgeslagen in de gebruikershomedirectory. Voer de volgende opdracht in om de startmap te identificeren en maak een map met de naam ** \.sparkmagic**.  Het volledige pad wordt uitgevoerd.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Maak in `.sparkmagic`de map een bestand genaamd **config.json** en voeg het volgende JSON-fragment erin toe.  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. Breng de volgende bewerkingen uit in het bestand:

    |Sjabloonwaarde | Nieuwe waarde |
    |---|---|
    |{GEBRUIKERSNAAM}|Cluster login, `admin`standaard is .|
    |{CLUSTERDNSNAME}|Clusternaam|
    |{BASE64ENCODEDPASSWORD}|Een basis64 gecodeerd wachtwoord voor uw werkelijke wachtwoord.  U een base64-wachtwoord genereren op [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Blijf bij `sparkmagic 0.12.7` gebruik (clusters v3.5 en v3.6).  Bij `sparkmagic 0.2.3` gebruik (clusters v3.4) vervangen door `"should_heartbeat": true`.|

    U een volledig voorbeeldbestand zien op [voorbeeldconfig.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > Heartbeats worden verzonden om ervoor te zorgen dat sessies niet worden gelekt. Wanneer een computer in de slaapstand gaat of wordt uitgeschakeld, wordt de hartslag niet verzonden, waardoor de sessie wordt opgeschoond. Voor clusters v3.4 u, als u dit gedrag wilt `livy.server.interactive.heartbeat.timeout` `0` uitschakelen, de Livy-config instellen op de Ambari-gebruikersinterface. Voor clusters v3.5 wordt de sessie niet verwijderd als u de bovenstaande 3.5-configuratie niet instelt.

5. Start Jupyter. Gebruik de volgende opdracht in de opdrachtprompt.

    ```cmd
    jupyter notebook
    ```

6. Controleer of u de Spark-magie gebruiken die beschikbaar is met de kernels. Voltooi de volgende stappen.

    a. Maak een nieuwe notebook. **Selecteer**Nieuw in de rechterhoek. U ziet de standaardkernel **Python 2** of **Python 3** en de kernels die u hebt geïnstalleerd. De werkelijke waarden kunnen variëren afhankelijk van uw installatiekeuzes.  Selecteer **PySpark**.

    ![Beschikbare kernels in Jupyter-laptop](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Kernels in Jupyter-laptop")

    > [!IMPORTANT]  
    > Na het selecteren **van Nieuw** controleer je shell op eventuele fouten.  Als u de `TypeError: __init__() got an unexpected keyword argument 'io_loop'` fout ziet, u een bekend probleem ondervinden met bepaalde versies van Tornado.  Als dat het zo is, stop je de `pip install tornado==4.5.3`kernel en degradeer je de Tornado-installatie met de volgende opdracht: .

    b. Voer het volgende codefragment uit.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Als u de uitvoer met succes ophalen, wordt uw verbinding met het HDInsight-cluster getest.

    Als u de configuratie van het notitieblok wilt bijwerken om verbinding te maken met een ander cluster, werkt u de config.json bij met de nieuwe set waarden, zoals weergegeven in stap 3 hierboven.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Waarom zou ik Jupyter op mijn computer installeren?

Redenen om Jupyter op uw computer te installeren en deze vervolgens aan te sluiten op een Apache Spark-cluster op HDInsight:

* Biedt u de mogelijkheid om uw notitieblokken lokaal te maken, uw toepassing te testen op een lopend cluster en vervolgens de notitieblokken naar het cluster te uploaden. Als u de notitieblokken naar het cluster wilt uploaden, u ze uploaden met `/HdiNotebooks` het Jupyter-notitieblok dat wordt uitgevoerd of het cluster, of ze opslaan in de map in het opslagaccount dat aan het cluster is gekoppeld. Zie [Waar worden Jupyter-notitieblokken opgeslagen](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)voor meer informatie over hoe notitieblokken op het cluster worden opgeslagen?
* Met de notitieblokken die lokaal beschikbaar zijn, u verbinding maken met verschillende Spark-clusters op basis van uw toepassingsvereisten.
* U GitHub gebruiken om een bronbesturingssysteem te implementeren en versiebeheer voor de notitieblokken te hebben. U ook een samenwerkingsomgeving hebben waar meerdere gebruikers met hetzelfde notitieblok kunnen werken.
* U lokaal met notitieblokken werken zonder zelfs maar een cluster te hebben. U hebt alleen een cluster nodig om uw notitieblokken tegen te testen, niet om uw notitieblokken of een ontwikkelomgeving handmatig te beheren.
* Het is misschien eenvoudiger om uw eigen lokale ontwikkelomgeving te configureren dan om de Jupyter-installatie op het cluster te configureren.  U profiteren van alle software die u lokaal hebt geïnstalleerd zonder een of meer externe clusters te configureren.

> [!WARNING]  
> Als Jupyter op uw lokale computer is geïnstalleerd, kunnen meerdere gebruikers hetzelfde notitieblok tegelijkertijd op hetzelfde Spark-cluster uitvoeren. In een dergelijke situatie worden meerdere Livy-sessies gemaakt. Als u een probleem tegenkomt en dat wilt debuggen, is het een complexe taak om bij te houden welke Livy-sessie bij welke gebruiker is.  

## <a name="next-steps"></a>Volgende stappen

* [Overzicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Apache Spark met BI: Apache Spark-gegevens analyseren met Power BI in HDInsight](apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)
