---
title: Het patch schema voor het besturings systeem voor Azure HDInsight-clusters configureren
description: Meer informatie over het configureren van het patch schema voor het besturings systeem voor HDInsight-clusters op basis van Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a97a03f7ef20ae56cec04341fe76b79ee657547b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748473"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Het patch schema voor het besturings systeem configureren voor HDInsight-clusters op basis van Linux

> [!IMPORTANT]
> Er worden binnen drie maanden na publicatie een Ubuntu-installatie kopie beschikbaar voor het maken van nieuwe Azure HDInsight-clusters. Vanaf januari 2019 worden er geen automatische patches uitgevoerd voor clusters. Klanten moeten script acties of andere mechanismen gebruiken om een actief cluster te patchen. Nieuwe clusters hebben altijd de meest recente beschik bare updates, inclusief de meest recente beveiligings patches.

HDInsight biedt ondersteuning voor u om algemene taken uit te voeren op uw cluster, zoals het installeren van besturingssysteem patches, beveiligings updates en het opnieuw opstarten van knoop punten. Deze taken worden uitgevoerd met behulp van de volgende twee scripts die kunnen worden uitgevoerd als [script acties](hdinsight-hadoop-customize-cluster-linux.md), en geconfigureerd met para meters:

- `schedule-reboots.sh`-direct opnieuw opstarten of een herstart plannen op de cluster knooppunten.
- `install-updates-schedule-reboots.sh`: Installeer alle updates, alleen kernel-en beveiligings updates, of alleen kernel-updates.

> [!NOTE]  
> Met script acties worden updates voor alle toekomstige update cycli niet automatisch toegepast. Voer de scripts telkens uit wanneer er nieuwe updates moeten worden toegepast om de updates te installeren en start de virtuele machine vervolgens opnieuw op.

## <a name="restart-nodes"></a>Knoop punten opnieuw starten
  
Het script [schema-opnieuw opstarten:](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)Hiermee stelt u het type opnieuw opstarten in dat wordt uitgevoerd op de computers in het cluster. Bij het indienen van de script actie stelt u deze in op alle drie knooppunt typen: hoofd knooppunt, Worker-knoop punt en Zookeeper. Als het script niet wordt toegepast op een knooppunt type, worden de Vm's voor dat knooppunt type niet bijgewerkt of opnieuw gestart.

De `schedule-reboots script` accepteert één numerieke para meter:

| Parameter | Geaccepteerde waarden | Definitie |
| --- | --- | --- |
| Type opnieuw opstarten om uit te voeren | 1 of 2 | Met de waarde 1 wordt het opnieuw starten van de planning ingeschakeld (gepland over 12-24 uur). Een waarde van 2 maakt direct opnieuw opstarten mogelijk (in 5 minuten). Als er geen para meter wordt opgegeven, is de standaard waarde 1. |  

## <a name="install-updates-and-restart-nodes"></a>Updates installeren en knoop punten opnieuw starten

De script [install-updates-Schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) biedt opties voor het installeren van verschillende soorten updates en het opnieuw opstarten van de virtuele machine.

Het `install-updates-schedule-reboots` script accepteert twee numerieke para meters, zoals wordt beschreven in de volgende tabel:

| Parameter | Geaccepteerde waarden | Definitie |
| --- | --- | --- |
| Type updates die moeten worden geïnstalleerd | 0, 1 of 2 | Met de waarde 0 worden alleen kernel-updates geïnstalleerd. Met de waarde 1 worden alle updates geïnstalleerd en 2 worden alleen kernel + beveiligings updates geïnstalleerd. Als er geen para meter wordt gegeven, is de standaard waarde 0. |
| Type opnieuw opstarten om uit te voeren | 0, 1 of 2 | Met de waarde 0 wordt opnieuw opstarten uitgeschakeld. Met de waarde 1 wordt het opnieuw starten van de planning ingeschakeld en 2 wordt direct opnieuw opgestart. Als er geen para meter wordt gegeven, is de standaard waarde 0. De gebruiker moet invoer parameter 1 wijzigen in invoer parameter 2. |

> [!NOTE]
> U moet een script markeren als bewaard nadat u het toepast op een bestaand cluster. Anders wordt de standaard planning voor het patchen gebruikt voor nieuwe knoop punten die zijn gemaakt via schaal bewerkingen. Als u het script toepast als onderdeel van het proces voor het maken van het cluster, wordt het automatisch gehandhaafd.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende secties in de op [Linux gebaseerde HDInsight-clusters aanpassen met behulp van script actie](hdinsight-hadoop-customize-cluster-linux.md)voor specifieke stappen voor het gebruik van script acties:

* [Een script actie gebruiken tijdens het maken van een cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Een script actie Toep assen op een actief cluster](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
