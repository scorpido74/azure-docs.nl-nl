---
title: Aangepaste Apache Ambari-database op Azure HDInsight
description: Meer informatie over het maken van HDInsight-clusters met uw eigen aangepaste Apache Ambari-database.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: e7351e2f39c7e4eed84f4a47e3eeb2214a062a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240160"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>HDInsight-clusters instellen met een aangepaste Ambari DB

Apache Ambari vereenvoudigt het beheer en de monitoring van een Apache Hadoop cluster. Ambari biedt een eenvoudig te gebruiken web-gebruikersinterface en REST API. Ambari is opgenomen in HDInsight-clusters en wordt gebruikt om het cluster te bewaken en configuratiewijzigingen aan te brengen.

In normale clustercreatie, zoals beschreven in andere artikelen, zoals [Clusters instellen in HDInsight,](hdinsight-hadoop-provision-linux-clusters.md)wordt Ambari geïmplementeerd in een [S0 Azure SQL-database](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) die wordt beheerd door HDInsight en niet toegankelijk is voor gebruikers.

Met de aangepaste Ambari DB-functie u een nieuw cluster implementeren en Ambari instellen in een externe database die u beheert. De implementatie gebeurt met een Azure Resource Manager-sjabloon. Deze functie heeft de volgende voordelen:

- Aanpassing - u kiest de grootte en verwerkingscapaciteit van de database. Als u grote clusters hebt die intensieve workloads verwerken, kan een Ambari-database met lagere specificaties een knelpunt worden voor beheerbewerkingen.
- Flexibiliteit - u de database zo nodig schalen om aan uw eisen te voldoen.
- Controle - u back-ups en beveiliging voor uw database beheren op een manier die past bij de vereisten van uw organisatie.

De rest van dit artikel bespreekt de volgende punten:

- vereisten voor het gebruik van de aangepaste Ambari DB-functie
- de stappen die nodig zijn om HDInsight-clusters in te richten met behulp van uw eigen externe database voor Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Aangepaste Ambari DB-vereisten

U een aangepaste Ambari DB implementeren met alle clustertypen en -versies. Meerdere clusters kunnen niet dezelfde Ambari DB gebruiken.

De aangepaste Ambari DB heeft de volgende andere eisen:

- U moet beschikken over een bestaande Azure SQL DB-server en -database.
- De database die u voor ambari-instellingen opgeeft, moet leeg zijn. Er mogen geen tabellen in het standaard dbo-schema staan.
- De gebruiker die wordt gebruikt om verbinding te maken met de database moet select- en maaktabel- en invoegmachtigingen voor de database hebben.
- Schakel de optie in om [toegang tot Azure-services toe te staan](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) op de Azure SQL-server waar u Ambari host.
- Beheer-IP-adressen van de HDInsight-service moeten worden toegestaan in de SQL-server. Zie [IP-adressen van HDInsight-beheer](hdinsight-management-ip-addresses.md) voor een lijst met DE IP-adressen die aan de SQL-serverfirewall moeten worden toegevoegd.

Wanneer u uw Apache Ambari DB host in een externe database, onthoudt u de volgende punten:

- U bent verantwoordelijk voor de extra kosten van de Azure SQL DB die Ambari bevat.
- Maak regelmatig een back-up van uw aangepaste Ambari DB. Azure SQL Database genereert automatisch back-ups, maar de tijdsperiode voor back-upretentie varieert. Zie [Meer informatie over automatische SQL Database-back-ups](../sql-database/sql-database-automated-backups.md)voor meer informatie.

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Clusters implementeren met een aangepaste Ambari DB

Als u een HDInsight-cluster wilt maken dat uw eigen externe Ambari-database gebruikt, gebruikt u de [aangepaste Ambari DB Quickstart-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Bewerk de parameters `azuredeploy.parameters.json` in het om informatie op te geven over uw nieuwe cluster en de database die Ambari bevat.

U de implementatie starten met de Azure CLI. Vervang `<RESOURCEGROUPNAME>` de brongroep waar u uw cluster wilt implementeren.

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Volgende stappen

- [Externe metagegevensopslag gebruiken in Azure HDInsight](hdinsight-use-external-metadata-stores.md)