---
title: Patchplanningsschema voor besturingssysteem configureren voor Azure HDInsight-clusters
description: Meer informatie over het configureren van het patchschema voor het patchen van het besturingssysteem voor HDInsight-clusters op basis van Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: f8e694f658d6e9de04c92001214ecd5c32ff7753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206857"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Het patchschema voor het besturingssysteem configureren voor HDInsight-clusters op basis van Linux

> [!IMPORTANT]
> Ubuntu-afbeeldingen worden binnen drie maanden na publicatie beschikbaar voor het maken van nieuwe Azure HDInsight-cluster. Vanaf januari 2019 worden de lopende clusters niet automatisch gepatcht. Klanten moeten scriptacties of andere mechanismen gebruiken om een lopend cluster te patchen. Nieuw gemaakte clusters hebben altijd de nieuwste beschikbare updates, inclusief de meest recente beveiligingspatches.

HDInsight biedt ondersteuning voor u om veelvoorkomende taken op uw cluster uit te voeren, zoals het installeren van OS-patches, beveiligingsupdates en het opnieuw opstarten van knooppunten. Deze taken worden uitgevoerd met behulp van de volgende twee scripts die kunnen worden uitgevoerd als [scriptacties](hdinsight-hadoop-customize-cluster-linux.md)en geconfigureerd met parameters:

- `schedule-reboots.sh`- Maak een onmiddellijke herstart of plan een herstart op de clusterknooppunten.
- `install-updates-schedule-reboots.sh`- Installeer alle updates, alleen kernel + beveiligingsupdates of alleen kernelupdates.

> [!NOTE]  
> Scriptacties passen niet automatisch updates toe voor alle toekomstige updatecycli. Voer de scripts elke keer uit wanneer nieuwe updates moeten worden toegepast om de updates te installeren en start de VM opnieuw.

## <a name="preparation"></a>Voorbereiding

Patch op een representatieve niet-productieomgeving voordat deze wordt geïmplementeerd in productie. Ontwikkel een plan om uw systeem adequaat te testen voorafgaand aan uw eigenlijke patching.

Van tijd tot tijd, vanuit een ssh-sessie met uw cluster, u een bericht ontvangen dat er een upgrade beschikbaar is. Het bericht ziet er mogelijk uit als:

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

Patchen is optioneel en naar eigen inzicht.

## <a name="restart-nodes"></a>Knooppunten opnieuw starten
  
Het [scriptschema-reboots](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh), stelt het type reboot in dat wordt uitgevoerd op de machines in het cluster. Stel bij het indienen van de scriptactie de actie in op alle drie knooppunttypen: hoofdknooppunt, werknemersknooppunt en zookeeper. Als het script niet is toegepast op een knooppunttype, worden de VM's voor dat knooppunttype niet bijgewerkt of opnieuw gestart.

De `schedule-reboots script` accepteert één numerieke parameter:

| Parameter | Geaccepteerde waarden | Definitie |
| --- | --- | --- |
| Type opnieuw opstarten om uit te voeren | 1 of 2 | Een waarde van 1 maakt het opnieuw opstarten van de planning mogelijk (gepland in 12-24 uur). Een waarde van 2 maakt het mogelijk om direct opnieuw op te starten (in 5 minuten). Als er geen parameter wordt gegeven, is de standaardwaarde 1. |  

## <a name="install-updates-and-restart-nodes"></a>Updates installeren en knooppunten opnieuw opstarten

Het script [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) biedt opties om verschillende soorten updates te installeren en de VM opnieuw te starten.

Het `install-updates-schedule-reboots` script accepteert twee numerieke parameters, zoals beschreven in de volgende tabel:

| Parameter | Geaccepteerde waarden | Definitie |
| --- | --- | --- |
| Type updates om te installeren | 0, 1 of 2 | Een waarde van 0 installeert alleen kernel updates. Een waarde van 1 installeert alle updates, en 2 installeert alleen kernel + beveiligingsupdates. Als er geen parameter is opgegeven, is de standaardinstelling 0. |
| Type opnieuw opstarten om uit te voeren | 0, 1 of 2 | Een waarde van 0 schakelt opnieuw opstarten uit. Een waarde van 1 maakt het opnieuw opstarten van de planning mogelijk en 2 maakt het mogelijk om onmiddellijk opnieuw op te starten. Als er geen parameter is opgegeven, is de standaardinstelling 0. De gebruiker moet invoerparameter 1 wijzigen in invoerparameter 2. |

> [!NOTE]
> U moet een script markeren als volhard nadat u het hebt toegepast op een bestaand cluster. Anders worden alle nieuwe knooppunten die via schaalbewerkingen zijn gemaakt, het standaardpatchschema gebruikt. Als u het script toepast als onderdeel van het proces voor het maken van het cluster, blijft het automatisch bestaan.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende secties in [Op Linux gebaseerde HDInsight-clusters aanpassen met scriptactie](hdinsight-hadoop-customize-cluster-linux.md)voor specifieke stappen over het gebruik van scriptacties:

- [Een scriptactie gebruiken tijdens het maken van een cluster](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Een scriptactie toepassen op een lopend cluster](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
