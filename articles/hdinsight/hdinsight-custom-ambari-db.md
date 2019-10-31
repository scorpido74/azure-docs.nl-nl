---
title: Aangepaste Apache Ambari-Data Base op Azure HDInsight
description: Meer informatie over het maken van HDInsight-clusters met uw eigen aangepaste Apache Ambari-data base.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: 8064fd5369e55ea223a697d30d7643ff5407cf76
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73066001"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>HDInsight-clusters met een aangepaste Ambari-data base instellen

Apache Ambari vereenvoudigt het beheer en de bewaking van een Apache Hadoop cluster. Ambari biedt een gebruiks vriendelijke webgebruikersinterface en REST API. Ambari is opgenomen in HDInsight-clusters en wordt gebruikt om het cluster te controleren en configuratie wijzigingen aan te brengen.

In een normale cluster maken, zoals beschreven in andere artikelen, zoals het [instellen van clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md), wordt Ambari geïmplementeerd in een [S0 Azure-SQL database](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) die wordt beheerd door HDInsight en niet toegankelijk is voor gebruikers.

Met de functie Custom Ambari DB kunt u een nieuw cluster en installatie-Ambari implementeren in een externe data base die u beheert. De implementatie wordt uitgevoerd met een Azure Resource Manager sjabloon. Deze functie biedt de volgende voor delen:

- Aanpassing: u kiest de grootte en de verwerkings capaciteit van de data base. Als er grote clusters zijn die intensieve workloads verwerken, kan een Ambari-data base met lagere specificaties een bottleneck vormen voor beheer bewerkingen.
- Flexibiliteit: u kunt de Data Base naar wens schalen voor uw behoeften.
- Beheer: u kunt back-ups en beveiliging voor uw Data Base op een manier beheren die past bij uw organisatie vereisten.

In de rest van dit artikel komen de volgende punten aan bod:

- vereisten voor het gebruik van de functie Custom Ambari DB
- de stappen die nodig zijn om HDInsight-clusters in te richten met uw eigen externe data base voor Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Aangepaste Ambari DB-vereisten

U kunt een aangepaste Ambari-data base implementeren met alle cluster typen en versies. Meerdere clusters kunnen niet dezelfde Ambari-Data Base gebruiken.

De aangepaste Ambari DB heeft de volgende vereisten:

- U moet beschikken over een bestaande Azure SQL DB-server en-data base.
- De data base die u voor de installatie van Ambari opgeeft, moet leeg zijn. Er mogen geen tabellen zijn in het standaard dbo-schema.
- De gebruiker die wordt gebruikt om verbinding te maken met de data base moet machtigingen voor selecteren, CREATE TABLE en invoegen hebben voor de data base.
- Schakel de optie in om [toegang tot Azure-Services toe te staan](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) op de Azure SQL-server waarop u Ambari wilt hosten.
- IP-adressen van beheer van HDInsight-service moeten worden toegestaan in de SQL Server. Zie [IP-adressen van HDInsight-beheer](hdinsight-management-ip-addresses.md) voor een lijst met IP-adressen die moeten worden toegevoegd aan de SQL Server-firewall.

Wanneer u uw Apache Ambari DB host in een externe data base, moet u rekening houden met de volgende punten:

- U bent verantwoordelijk voor de extra kosten van de Azure SQL-data base die Ambari bevat.
- Maak regel matig een back-up van uw aangepaste Ambari DB. Azure SQL Database worden automatisch back-ups gegenereerd, maar de Bewaar periode voor back-ups is afhankelijk van. Zie [informatie over automatische SQL database back-ups](../sql-database/sql-database-automated-backups.md)voor meer informatie.

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Clusters implementeren met een aangepaste Ambari-data base

Als u een HDInsight-cluster wilt maken dat gebruikmaakt van uw eigen externe Ambari-data base, gebruikt u de [aangepaste AMBARI DB Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)start-sjabloon.

Bewerk de para meters in de `azuredeploy.parameters.json` om informatie op te geven over het nieuwe cluster en de data base die Ambari zal bevatten.

U kunt de implementatie starten met behulp van de Azure CLI. Vervang `<RESOURCEGROUPNAME>` door de resource groep waar u uw cluster wilt implementeren.

```azure-cli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Volgende stappen

- [Externe metagegevensopslag gebruiken in Azure HDInsight](hdinsight-use-external-metadata-stores.md)