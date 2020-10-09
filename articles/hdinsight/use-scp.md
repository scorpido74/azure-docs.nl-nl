---
title: SCP met Apache Hadoop in azure HDInsight gebruiken
description: Dit document bevat informatie over het maken van verbinding met HDInsight met behulp van de SSH-en SCP-opdrachten.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 44c87a3415a236f614510aa3163ad2e9cbd1f64f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856174"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>SCP met Apache Hadoop in azure HDInsight gebruiken

Dit artikel bevat informatie over het veilig overdragen van bestanden met uw HDInsight-cluster.

## <a name="copy-files"></a>Bestanden kopiëren

Het hulpprogramma `scp` kan worden gebruikt om bestanden te kopiëren naar en van afzonderlijke knooppunten in het cluster. De volgende opdracht kopieert bijvoorbeeld de map `test.txt` uit het lokale systeem naar het primaire hoofdknooppunt:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Omdat er geen pad is opgegeven na de `:`, wordt het bestand geplaatst in de basismap `sshuser`.

In het volgende voorbeeld wordt het bestand `test.txt` uit de basismap `sshuser` op het primaire hoofdknooppunt gekopieerd naar het lokale systeem:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

`scp` heeft alleen toegang tot het bestandssysteem van afzonderlijke knooppunten in het cluster. Het kan niet worden gebruikt om toegang te krijgen tot gegevens in de opslag die compatibel is met HDFS voor het cluster.

Gebruik `scp` wanneer u een resource moet uploaden voor gebruik in een SSH-sessie. Upload bijvoorbeeld van een Python-script en voer het script uit in een SSH-sessie.

Zie de volgende documenten voor informatie over het rechtstreeks laden van gegevens in de Hadoop Distributed File System-compatibele opslag:

* [HDInsight op basis van Azure Storage](hdinsight-hadoop-use-blob-storage.md).
* [HDInsight met behulp van Azure data Lake Storage gen1](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md).

## <a name="next-steps"></a>Volgende stappen

* [SSH gebruiken met HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Edge-knooppunten gebruiken in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
