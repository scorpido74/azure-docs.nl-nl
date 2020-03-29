---
title: Azure HDInsight-toepassingen publiceren
description: Meer informatie over het maken van een HDInsight-toepassing en deze vervolgens publiceren in de Azure Marketplace.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: e64bf253a73df3a2f8170109dc1dfb9a59613733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64685330"
---
# <a name="publish-an-hdinsight-application-in-the-azure-marketplace"></a>Een HDInsight-toepassing publiceren in Azure Marketplace
U een Azure HDInsight-toepassing installeren op een HDInsight-cluster op Basis van Linux. In dit artikel leest u hoe u een HDInsight-toepassing publiceert in de Azure Marketplace. Zie Een aanbieding publiceren in de Azure Marketplace voor algemene informatie over publiceren in de [Azure Marketplace.](../marketplace/marketplace-publishers-guide.md)

HDInsight-toepassingen maken gebruik van het *BYOL-model (Bring Your Own License).* In een BYOL-scenario is een toepassingsprovider verantwoordelijk voor het verlenen van licenties voor de toepassing aan app-gebruikers. App-gebruikers worden alleen in rekening gebracht voor de Azure-resources die ze maken, zoals het HDInsight-cluster en de VM's en knooppunten van het cluster. Momenteel vindt facturering voor de toepassing zelf niet plaats in Azure.

Zie deze HDInsight-toepassingsgerelateerde artikelen voor meer informatie:

* [HDInsight-toepassingen installeren.](hdinsight-apps-install-applications.md) Meer informatie over het installeren van een HDInsight-toepassing op uw clusters.
* [Installeer aangepaste HDInsight-toepassingen.](hdinsight-apps-install-custom-applications.md) Meer informatie over het installeren en testen van aangepaste HDInsight-toepassingen.

## <a name="prerequisites"></a>Vereisten
Als u uw aangepaste toepassing wilt indienen in de Marketplace, [maakt en test u eerst uw aangepaste toepassing.](hdinsight-apps-install-custom-applications.md)

Je moet ook je ontwikkelaarsaccount registreren. Zie [Een aanbieding publiceren in de Azure Marketplace](../marketplace/marketplace-publishers-guide.md) en Een Microsoft [Developer-account maken](../marketplace/marketplace-publishers-guide.md)voor meer informatie.

## <a name="define-the-application"></a>De toepassing definiëren
Twee stappen zijn betrokken bij het publiceren van applicaties in de Marketplace. Definieer eerst een *bestand createUiDef.json.* Het bestand createUiDef.json geeft aan met welke clusters uw toepassing compatibel is. Publiceer vervolgens de sjabloon vanuit de Azure-portal. Hier is een voorbeeld van uiDef.json-bestand:

```json
{
    "handler": "Microsoft.HDInsight",
    "version": "0.0.1-preview",
    "clusterFilters": {
        "types": ["Hadoop", "HBase", "Storm", "Spark"],
        "versions": ["3.6"]
    }
}
```

| Veld | Beschrijving | Mogelijke waarden |
| --- | --- | --- |
| typen |De clustertypen waarmee de toepassing compatibel is. |Hadoop, HBase, Storm, Spark (of een combinatie van deze) |
| versies |De HDInsight-clustertypen waarmee de toepassing compatibel is. |3.4 |

## <a name="application-installation-script"></a>Installatiescript voor toepassingen
Wanneer een toepassing op een cluster is geïnstalleerd (op een bestaand cluster of op een nieuw cluster), wordt een randknooppunt gemaakt. Het toepassingsinstallatiescript wordt uitgevoerd op het randknooppunt.

  > [!IMPORTANT]  
  > De naam van het toepassingsinstallatiescript moet uniek zijn voor een specifiek cluster. De scriptnaam moet de volgende indeling hebben:
  > 
  > "naam": "[concat('hue-install-v0','-' ,uniquestring('applicationName')]"
  > 
  > De naam van het script bestaat uit drie delen:
  > 
  > * Een scriptnaamvoorvoegsel, dat de toepassingsnaam of een naam moet bevatten die relevant is voor de toepassing.
  > * Een koppelteken, voor leesbaarheid.
  > * Een unieke tekenreeksfunctie, met de naam van de toepassing als parameter.
  > 
  > In de lijst met aanhoudende scriptactie wordt het voorgaande voorbeeld weergegeven als **hue-install-v0-4wkahss55hlas**. Zie een [monster JSON payload](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 

Het installatiescript moet de volgende kenmerken hebben:
* Het script is idempotent. Meerdere aanroepen naar het script leveren hetzelfde resultaat op.
* Het script is goed geversioned. Gebruik een andere locatie voor het script wanneer u wijzigingen upgradet of test. Dit zorgt ervoor dat klanten die de toepassing installeren geen last hebben van uw updates of tests. 
* Het script heeft voldoende logging op elk punt. Meestal zijn scriptlogboeken de enige manier om problemen met de installatie van toepassingen te debuggen.
* Oproepen naar externe services of resources hebben voldoende nieuwe pogingen, zodat de installatie niet wordt beïnvloed door tijdelijke netwerkproblemen.
* Als uw script services op de knooppunten start, worden services gecontroleerd en geconfigureerd om automatisch te starten als er een opnieuw opstarten van een knooppunt plaatsvindt.

## <a name="package-the-application"></a>De toepassing verpakken
Maak een .zip-bestand dat alle bestanden bevat die nodig zijn om uw HDInsight-toepassing te installeren. U gebruikt het .zip-bestand om de toepassing te publiceren. Het .zip-bestand bevat de volgende bestanden:

* createUiDefinition.json
* mainTemplate.json (Zie Aangepaste [HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md)voor een voorbeeld .)
* Alle vereiste scripts

> [!NOTE]  
> U de toepassingsbestanden (inclusief alle web-app-bestanden) hosten op een openbaar toegankelijk eindpunt.

## <a name="publish-the-application"></a>De toepassing publiceren
Ga als een HDInsight-toepassing uit:

1. Meld u aan bij [Azure Publishing](https://publish.windowsazure.com/).
2. Selecteer **oplossingssjablonen**in het linkermenu .
3. Voer een titel in en selecteer **Een nieuwe oplossingssjabloon maken**.
4. Als u uw organisatie nog niet hebt geregistreerd, selecteert u **Dev Center-account maken en lid worden van het Azure-programma**.  Zie [Een Microsoft Developer-account maken](../marketplace/marketplace-publishers-guide.md)voor meer informatie .
5. Selecteer **Sommige topologieën definiëren om aan de slag te gaan**. Een oplossingssjabloon is een "ouder" voor al zijn topologieën. U meerdere topologieën definiëren in één aanbiedings- of oplossingssjabloon. Wanneer een aanbieding naar enscenering wordt geduwd, wordt het met al zijn topologieën geduwd. 
6. Voer een topologienaam in **+** en selecteer .
7. Voer een nieuwe versie **+** in en selecteer .
8. Upload het .zip-bestand dat u hebt gemaakt toen u de toepassing hebt verpakt.  
9. Selecteer **Certificering aanvragen**. Het Certificeringsteam van Microsoft controleert de bestanden en certificeert de topologie.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [installeren van HDInsight-toepassingen](hdinsight-apps-install-applications.md) in uw clusters.
* Meer informatie over het [installeren van aangepaste HDInsight-toepassingen](hdinsight-apps-install-custom-applications.md) en het implementeren van een niet-gepubliceerde HDInsight-toepassing op HDInsight.
* Meer informatie over het [gebruik van Script Action om HDInsight-clusters op basis van Linux aan te passen](hdinsight-hadoop-customize-cluster-linux.md) en meer toepassingen toe te voegen. 
* Meer informatie over het [maken van Apache Hadoop-clusters op basis van Linux in HDInsight met Azure Resource Manager-sjablonen.](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* Meer informatie over het [gebruik van een leeg randknooppunt in HDInsight](hdinsight-apps-use-edge-node.md) om toegang te krijgen tot HDInsight-clusters, HDInsight-toepassingen te testen en HDInsight-toepassingen te hosten.

