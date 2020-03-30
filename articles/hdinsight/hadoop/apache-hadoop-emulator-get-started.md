---
title: Leer een Apache Hadoop sandbox, emulator te gebruiken - Azure HDInsight
description: 'Als u wilt beginnen met het leren over het gebruik van het Apache Hadoop-ecosysteem, u een Hadoop-sandbox van Hortonworks instellen op een virtuele Azure-machine. '
keywords: hadoop emulator, hadoop sandbox
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: 47ee66393e3e1678576b12a70b767f35cb3bc635
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73044771"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Aan de slag met een Apache Hadoop sandbox, een emulator op een virtuele machine

Leer hoe u de Apache Hadoop-sandbox van Hortonworks op een virtuele machine installeert om meer te weten te komen over het Hadoop-ecosysteem. De sandbox biedt een lokale ontwikkelomgeving om meer te weten te komen over Hadoop, Hadoop Distributed File System (HDFS) en taakindiening. Zodra u bekend bent met Hadoop, u Hadoop op Azure gaan gebruiken door een HDInsight-cluster te maken. Zie Aan de slag met [Hadoop op HDInsight](apache-hadoop-linux-tutorial-get-started.md)voor meer informatie over hoe u aan de slag.

## <a name="prerequisites"></a>Vereisten

* [Oracle VirtualBox](https://www.virtualbox.org/). Download en installeer het vanaf [hier.](https://www.virtualbox.org/wiki/Downloads)

## <a name="download-and-install-the-virtual-machine"></a>De virtuele machine downloaden en installeren

1. Blader naar de [Cloudera-downloads.](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html)

1. Klik **op VIRTUALBOX** onder **Installatietype kiezen** om de nieuwste Hortonworks Sandbox op een vm te downloaden. Log in of vul het productinteresseformulier in.

1. Klik op de knop **HDP SANDBOX (LAATSTE)** om de download te starten.

Zie [Sandbox Deployment and Install Guide](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/)voor instructies voor het instellen van de sandbox.

Als u een oudere SANDBOX voor hdp-versies wilt downloaden, raadpleegt u de koppelingen onder **Oudere versies**.

## <a name="start-the-virtual-machine"></a>De virtuele machine starten

1. Open Oracle VM VirtualBox.
1. Klik **in** het menu Bestand op **Toestel importeren**en geef vervolgens de sandbox-afbeelding van Hortonworks op.
1. Selecteer de Sandbox van Hortonworks, klik op **Start**en vervolgens **normaal begin**. Zodra de virtuele machine klaar is met het opstartproces, worden inloginstructies weergegeven.

    ![virtualbox manager normale start](./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png)

1. Open een webbrowser en navigeer naar de `http://127.0.0.1:8888`weergegeven URL (meestal ).

## <a name="set-sandbox-passwords"></a>Sandbox-wachtwoorden instellen

1. Selecteer **Geavanceerde opties weergeven**vanaf de stap aan de **slag** van de Sandbox-pagina van Hortonworks . Gebruik de informatie op deze pagina om in te loggen op de sandbox met Behulp van SSH. Gebruik de opgegeven naam en wachtwoord.

   > [!NOTE]
   > Als u geen SSH-client hebt geïnstalleerd, u de webgebaseerde SSH gebruiken die door de virtuele machine wordt geleverd op **http://localhost:4200/**.

    De eerste keer dat u verbinding maakt met SSH, wordt u gevraagd het wachtwoord voor het hoofdaccount te wijzigen. Voer een nieuw wachtwoord in, dat u gebruikt wanneer u inlogt met SSH.

2. Voer, nadat u bent ingelogd, de volgende opdracht in:

        ambari-admin-password-reset

    Geef desgevraagd een wachtwoord op voor het Ambari-beheerdersaccount. Dit wordt gebruikt wanneer u toegang krijgt tot de Ambari Web UI.

## <a name="use-hive-commands"></a>Hive-opdrachten gebruiken

1. Gebruik de volgende opdracht om de Hive-shell te starten, van een SSH-verbinding naar de sandbox:

        hive
2. Zodra de shell is gestart, gebruikt u het volgende om de tabellen weer te geven die zijn voorzien van de sandbox:

        show tables;
3. Gebruik het volgende om 10 `sample_07` rijen uit de tabel op te halen:

        select * from sample_07 limit 10;

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het gebruik van Visual Studio met de Hortonworks Sandbox](../hdinsight-hadoop-emulator-visual-studio.md)

* [Het leren van de touwen van de Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Hadoop tutorial - Aan de slag met HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
