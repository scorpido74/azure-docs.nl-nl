---
title: Het patch schema voor het besturings systeem configureren voor HDInsight-clusters op basis van Linux-Azure
description: Meer informatie over het configureren van het patch schema voor het besturings systeem voor HDInsight-clusters op basis van Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 06111ec35a127cf17fdcc77ff717de7a4bc7299f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076858"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Het patch schema voor het besturings systeem configureren voor HDInsight-clusters op basis van Linux 

> [!IMPORTANT]
> Er worden binnen drie maanden na publicatie een Ubuntu-installatie kopie beschikbaar voor het maken van nieuwe Azure HDInsight-clusters. Vanaf januari 2019 worden er geen automatische patches uitgevoerd voor clusters. Klanten moeten script acties of andere mechanismen gebruiken om een actief cluster te patchen. Nieuwe clusters hebben altijd de meest recente beschik bare updates, inclusief de meest recente beveiligings patches.

Af en toe moet u de virtuele machines (Vm's) in een HDInsight-cluster opnieuw opstarten om belang rijke beveiligings patches te installeren.

Met behulp van de script acties die in dit artikel worden beschreven, kunt u de planning voor het patchen van het besturings systeem als volgt wijzigen:

1. Installeer alle updates of installeer alleen kernel + beveiligings updates of kernel-updates.
2. Direct opnieuw opstarten of een herstart plannen op de VM.

> [!NOTE]  
> De script acties die in dit artikel worden beschreven, werken alleen met HDInsight-clusters op basis van Linux die na 1 augustus 2016 zijn gemaakt. Patches zijn alleen effectief na het opnieuw opstarten van Vm's.
> Met script acties worden updates voor alle toekomstige update cycli niet automatisch toegepast. Voer de scripts telkens uit wanneer er nieuwe updates moeten worden toegepast om de updates te installeren en start de virtuele machine vervolgens opnieuw op.

## <a name="add-information-to-the-script"></a>Informatie toevoegen aan het script

Voor het gebruik van een script is de volgende informatie vereist:

- Met de installatie-updates-Schedule-de locatie van het https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh script opnieuw opstarten:.
    
   HDInsight gebruikt deze URI om het script te zoeken en uit te voeren op alle virtuele machines in het cluster. Dit script bevat opties voor het installeren van updates en het opnieuw opstarten van de virtuele machine.
  
- Schedule-de locatie van het script opnieuw https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh opstarten:.
    
   HDInsight gebruikt deze URI om het script te zoeken en uit te voeren op alle virtuele machines in het cluster. Met dit script wordt de VM opnieuw opgestart.
  
- De cluster knooppunt typen waarop het script wordt toegepast, zijn hoofd knooppunt, workernode en Zookeeper. Pas het script toe op alle knooppunt typen in het cluster. Als het script niet wordt toegepast op een knooppunt type, worden de Vm's voor dat knooppunt type niet bijgewerkt of opnieuw gestart.

- Het script install-updates-Schedule-Starting accepteert twee numerieke para meters:

    | Parameter | Definitie |
    | --- | --- |
    | Kernel-updates alleen installeren/alle updates installeren/kernel + beveiligings updates alleen installeren|0, 1 of 2. Met de waarde 0 worden alleen kernel-updates geïnstalleerd. Met de waarde 1 worden alle updates geïnstalleerd en 2 worden alleen kernel + beveiligings updates geïnstalleerd. Als er geen para meter wordt gegeven, is de standaard waarde 0. |
    | Niet opnieuw opstarten/opnieuw opstarten plannen/direct opnieuw opstarten inschakelen |0, 1 of 2. Met de waarde 0 wordt opnieuw opstarten uitgeschakeld. Met de waarde 1 wordt het opnieuw starten van de planning ingeschakeld en 2 wordt direct opnieuw opgestart. Als er geen para meter wordt gegeven, is de standaard waarde 0. De gebruiker moet invoer parameter 1 wijzigen in invoer parameter 2. |
   
 - In het schema-opnieuw opstarten wordt een numerieke para meter geaccepteerd:

    | Parameter | Definitie |
    | --- | --- |
    | Plannen van opnieuw opstarten inschakelen/direct opnieuw opstarten inschakelen |1 of 2. Met de waarde 1 wordt het opnieuw starten van de planning ingeschakeld (gepland over 12-24 uur). Een waarde van 2 maakt direct opnieuw opstarten mogelijk (in 5 minuten). Als er geen para meter wordt opgegeven, is de standaard waarde 1. |  

> [!NOTE]
> U moet een script markeren als bewaard nadat u het toepast op een bestaand cluster. Anders wordt de standaard planning voor het patchen gebruikt voor nieuwe knoop punten die zijn gemaakt via schaal bewerkingen. Als u het script toepast als onderdeel van het proces voor het maken van het cluster, wordt het automatisch gehandhaafd.


## <a name="next-steps"></a>Volgende stappen

Zie de volgende secties in de op [Linux gebaseerde HDInsight-clusters aanpassen met behulp van script actie](hdinsight-hadoop-customize-cluster-linux.md)voor specifieke stappen voor het gebruik van script acties:

* [Een script actie gebruiken tijdens het maken van een cluster](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Een script actie Toep assen op een actief cluster](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
