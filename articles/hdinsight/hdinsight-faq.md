---
title: Veelgestelde vragen van Azure HDInsight
description: Veelgestelde vragen over HDInsight
keywords: veelgestelde vragen, veelgestelde vragen
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 8a37e1b9bc4a0b953dc727dbab2813dd938ed576
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652220"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: veelgestelde vragen

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over het uitvoeren van [Azure HDInsight.](https://azure.microsoft.com/services/hdinsight/)

## <a name="creating-or-deleting-hdinsight-clusters"></a>HDInsight-clusters maken of verwijderen

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Hoe kan ik een HDInsight-cluster inrichten?

Zie [Clusters instellen in HDInsight met Apache Hadoop, Apache Spark, Apache Kafka en meer](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)voor het bekijken van de beschikbare typen HDInsight-clusters en de inrichtingsmethoden.

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Hoe verwijder ik een bestaand HDInsight-cluster?

Zie [Een HDInsight-cluster verwijderen](hdinsight-delete-cluster.md)voor meer informatie over het verwijderen van een cluster wanneer het niet meer in gebruik is.

Laat ten minste 30 tot 60 minuten tussen bewerkingen maken en verwijderen. Anders kan de bewerking mislukken met het volgende foutbericht:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Hoe selecteer ik het juiste aantal cores of knooppunten voor mijn werkbelasting?

Het juiste aantal cores en andere configuratie-opties zijn afhankelijk van verschillende factoren.

Zie [Capaciteitsplanning voor HDInsight-clusters voor](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning)meer informatie.

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Wat kan ik doen wanneer het inrichten van een cluster mislukt vanwege een capaciteitsprobleem?

In deze sectie worden fouten met betrekking tot probleemproblemen en mitigatietechnieken voor algemene capaciteitsproblemen aangebracht.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Fout: De implementatie zou het quotum van '800' overschrijden

Azure heeft een quotumlimiet van 800 implementaties per resourcegroep. Er worden verschillende quota toegepast per resourcegroep, abonnement, account of andere scopes. Uw abonnement mag bijvoorbeeld worden geconfigureerd om het aantal kerngeheugens voor een regio te beperken. Als u een virtuele machine probeert te implementeren die meer cores heeft dan het toegestane bedrag, ontvangt u een foutbericht waarin staat dat het quotum is overschreden.

Als u dit probleem wilt oplossen, verwijdert u de implementaties die niet langer nodig zijn met behulp van de Azure-portal, CLI of PowerShell.

Zie voor meer informatie [Solve errors for resource quotas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) (Fouten voor resourcequota oplossen).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Fout: Het maximale knooppunt overschreed de beschikbare kernen in deze regio

Uw abonnement mag worden geconfigureerd om het aantal kerngeheugens voor een regio te beperken. Als u een resource probeert te implementeren die meer cores heeft dan het toegestane bedrag, ontvangt u een foutbericht waarin staat dat het quotum is overschreden.

Voer de volgende stappen uit om een quotumverhoging aan te vragen:

1. Ga naar de [Azure-portal](https://portal.azure.com)en selecteer **Help + ondersteuning**.
   
1. Selecteer **Nieuw ondersteuningsverzoek**.
   
1. Geef op het tabblad **Basisbeginselen** van de pagina **Nieuw ondersteuningsverzoek** de volgende informatie op:
   
   - **Probleemtype:** Selecteer **Service- en abonnementslimieten (quota)**.
   - **Abonnement:** Selecteer het abonnement dat u wilt wijzigen.
   - **Contingenttype:** Selecteer **HDInsight**.

Raadpleeg [Create a support ticket to increase core](hdinsight-capacity-planning.md#quotas) (Een ondersteuningsticket maken om het aantal kerngeheugens te verhogen) voor meer informatie.

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Wat zijn de verschillende typen knooppunten in een HDInsight-cluster?

Azure HDInsight-clusters hebben verschillende typen virtuele machines of knooppunten. Elk knooppunttype speelt een rol bij de werking van het systeem.

Zie [Resourcetypen in Azure HDInsight-clusters](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)voor meer informatie.

## <a name="individual-components"></a>Afzonderlijke onderdelen

### <a name="can-i-install-additional-components-on-my-cluster"></a>Kan ik extra onderdelen op mijn cluster installeren?

Ja. Gebruik het als belangrijkste onderdelen om extra componenten te installeren of de clusterconfiguratie aan te passen:

- Scripts tijdens of na het maken. Scripts worden aangeroepen via [scriptactie](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), een configuratieoptie die u gebruiken via de Azure-portal, HDInsight Windows PowerShell-cmdlets of de HDInsight .NET SDK. Deze configuratieoptie kan worden gebruikt vanaf de Azure-portal, HDInsight Windows PowerShell-cmdlets of de HDInsight .NET SDK.

- [HDInsight Application Platform](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) om ecosysteemtoepassingen te installeren.

Zie Wat zijn de [Apache Hadoop componenten en versies beschikbaar met HDInsight voor](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions) een lijst met ondersteunde componenten?

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Kan ik de afzonderlijke onderdelen upgraden die vooraf op het cluster zijn geïnstalleerd?

Als u ingebouwde onderdelen of toepassingen upgradet die vooraf op uw cluster zijn geïnstalleerd, wordt de resulterende configuratie niet ondersteund door Microsoft. Deze systeemconfiguraties zijn niet getest door Microsoft. Probeer een andere versie van het HDInsight-cluster te gebruiken die mogelijk al de bijgewerkte versie van het onderdeel vooraf heeft geïnstalleerd.

Hive upgraden als afzonderlijke component wordt bijvoorbeeld niet ondersteund. HDInsight is een managed service en veel services zijn geïntegreerd met de Ambari-server en getest. Als u een Hive op zichzelf upgradet, worden de geïndexeerde binaire bestanden van andere componenten gewijzigd en worden er problemen met de integratie van onderdelen in uw cluster veroorzaakt.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Kunnen Spark en Kafka op hetzelfde HDInsight-cluster draaien?

Nee, het is niet mogelijk om Apache Kafka en Apache Spark op hetzelfde HDInsight cluster uit te voeren. Maak afzonderlijke clusters voor Kafka en Spark om problemen met resourceconflicten te voorkomen.

### <a name="how-do-i-change-timezone-in-ambari"></a>Hoe verander ik de tijdzone in Ambari?

1. Open de Ambari-webgebruikersinterface op `https://CLUSTERNAME.azurehdinsight.net`, waar CLUSTERNAME de naam van uw cluster is.
2. Selecteer in de rechterbovenhoek beheerder | Instellingen. 

   ![Ambari-instellingen](media/hdinsight-faq/ambari-settings.png)

3. Selecteer in het venster Gebruikersinstellingen de nieuwe tijdzone in de vervolgkeuzelijst Tijdzone en klik op Opslaan.

   ![Gebruikersinstellingen voor Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Hoe kan ik migreren van de bestaande metastore naar Azure SQL Server? 

Als u wilt migreren van SQL Server naar Azure SQL Server, raadpleegt u [Zelfstudie: SQL Server migreren naar één database of samengevoegde database in Azure SQL Database offline met DMS](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Wordt de Hive metastore verwijderd wanneer het cluster wordt verwijderd?

Het hangt af van het type metastore dat uw cluster is geconfigureerd om te gebruiken.

Voor een standaardmetastore: de standaardmetastore maakt deel uit van de clusterlevenscyclus. Wanneer u een cluster verwijdert, worden ook de bijbehorende metastore en metagegevens verwijderd.

Voor een aangepaste metastore: de levenscyclus van de metastore is niet gekoppeld aan de levenscyclus van een cluster. Daarom u clusters maken en verwijderen zonder metadata te verliezen. Metagegevens zoals uw Hive-schema's blijven bestaan, zelfs nadat u het HDInsight-cluster hebt verwijderd en opnieuw hebt gemaakt.

Zie [Externe metagegevensopslag gebruiken in Azure HDInsight](hdinsight-use-external-metadata-stores.md)voor meer informatie.

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Migreert het migreren van een Hive-metastore ook het standaardbeleid van de Ranger-database?

Nee, de beleidsdefinitie bevindt zich in de Ranger-database, dus het migreren van de Ranger-database zal zijn beleid migreren.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>u een Hive-metastore migreren van een ESP-cluster (Enterprise Security Package) naar een niet-ESP-cluster en vice versa?

Ja, u een Hive-metastore migreren van een ESP naar een niet-ESP-cluster.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hoe kan ik de grootte van een Hive metastore database schatten?

Een Hive-metastore wordt gebruikt om de metagegevens op te slaan voor gegevensbronnen die worden gebruikt door de Hive-server. De groottevereisten zijn deels afhankelijk van het aantal en de complexiteit van uw Hive-gegevensbronnen en kunnen niet vooraf worden geschat. Zoals beschreven in [hive metastore richtlijnen](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines), u beginnen met een S2-laag, die 50 DTU en 250 GB opslag biedt, en als je een knelpunt ziet, u de database opschalen.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Ondersteunt u een andere database dan Azure SQL Database als een externe metastore?

Nee, Microsoft ondersteunt alleen Azure SQL Database als een externe aangepaste metastore.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Kan ik een metastore delen in meerdere clusters?

Ja, u aangepaste metastore delen in meerdere clusters, zolang ze dezelfde versie van HDInsight gebruiken.

## <a name="connectivity-and-virtual-networks"></a>Connectiviteit en virtuele netwerken  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Wat zijn de gevolgen van het blokkeren van poorten 22 en 23 op mijn netwerk?

Als u poorten 22 en poort 23 blokkeert, hebt u geen SSH-toegang tot het cluster. Deze poorten worden niet gebruikt door HDInsight-service.

Raadpleeg de volgende documenten voor meer informatie:

- [Netwerkverkeer beheren](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [Veilig binnenkomend verkeer naar HDInsight-clusters in een virtueel netwerk met privéeindpunt](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [IP-adressen hdinsight-beheer](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Kan ik een extra virtuele machine implementeren binnen hetzelfde subnet als een HDInsight-cluster?

Ja, u een extra virtuele machine implementeren binnen hetzelfde subnet als een HDInsight-cluster. De volgende configuraties zijn mogelijk:

- Randknooppunten: u een ander randknooppunt aan het cluster toevoegen, zoals beschreven in [Lege randknooppunten gebruiken op Apache Hadoop-clusters in HDInsight.](hdinsight-apps-use-edge-node.md)

- Zelfstandige knooppunten: u een zelfstandige virtuele machine toevoegen aan hetzelfde subnet en `https://<CLUSTERNAME>-int.azurehdinsight.net`toegang krijgen tot het cluster vanaf die virtuele machine met behulp van het privéeindpunt. Zie [Netwerkverkeer beheren](hdinsight-plan-virtual-network-deployment.md#networktraffic)voor meer informatie .

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Moet ik gegevens opslaan op de lokale schijf van een randknooppunt?

Nee, het opslaan van gegevens op een lokale schijf is geen goed idee. Als het knooppunt mislukt, gaan alle gegevens die lokaal zijn opgeslagen verloren. We raden u aan gegevens op te slaan in Azure Data Lake Storage Gen2- of Azure Blob-opslag of door een Azure Files-share te monteren voor het opslaan van de gegevens.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Kan ik een bestaand HDInsight-cluster toevoegen aan een ander virtueel netwerk?

Nee, dat kan je niet. Het virtuele netwerk moet worden gespecificeerd op het moment van inlevering. Als er geen virtueel netwerk is opgegeven tijdens de inrichting, maakt de implementatie een intern netwerk dat niet van buitenaf toegankelijk is. Zie [HDInsight toevoegen aan een bestaand virtueel netwerk voor](hdinsight-plan-virtual-network-deployment.md#existingvnet)meer informatie.

## <a name="security-and-certificates"></a>Beveiliging en certificaten

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Wat zijn de aanbevelingen voor malwarebescherming op Azure HDInsight-clusters?

Zie [Microsoft Antimalware voor Azure Cloud Services en virtuele machines voor](../security/fundamentals/antimalware.md)informatie over malwarebescherming.

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Hoe maak ik een keytab voor een HDInsight ESP-cluster?

Maak een Kerberos-toetstabblad voor uw domeingebruikersnaam. U dit tabblad later gebruiken om te verifiëren voor externe clusters die zijn verbonden aan domeinen zonder een wachtwoord in te voeren. De domeinnaam is hoofdletters:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Kan ik een bestaande Azure Active Directory-tenant gebruiken om een HDInsight-cluster te maken met het ESP?

U moet Azure Active Directory Domain Services (Azure AD DS) inschakelen voordat u een HDInsight-cluster met ESP maken. Open-source Hadoop vertrouwt op Kerberos voor Authenticatie (in tegenstelling tot OAuth).

Als u VM's wilt aansluiten bij een domein, moet u een domeincontroller hebben. Azure AD DS is de beheerde domeincontroller en wordt beschouwd als een extensie van Azure Active Directory die alle Kerberos-vereisten biedt om een beveiligd Hadoop-cluster op een beheerde manier te bouwen. HDInsight as a managed service integreert met Azure AD DS om end-to-end beveiliging te bieden.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Kan ik een zelfondertekend certificaat gebruiken in een AAD-DS beveiligde LDAP-installatie en het inrichten van een ESP-cluster?

Het gebruik van een certificaat dat is uitgegeven door een certificaatautoriteit wordt aanbevolen, maar het gebruik van een zelfondertekend certificaat wordt ook ondersteund op ESP. Zie voor meer informatie:

- [Azure Active Directory Domain Services inschakelen](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Zelfstudie: Beveiligde LDAP configureren voor een beheerd Azure Active Directory Domain Services-domein](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Hoe kan ik inlogactiviteiten opvragen die in Ranger worden weergegeven?

Voor controlevereisten raadt Microsoft aan azure monitorlogboeken in te schakelen zoals beschreven in [Azure Monitor-logboeken gebruiken om HDInsight-clusters te controleren.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial)

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Kan ik Clamscan uitschakelen op mijn cluster?

Clamscan is de antivirussoftware die op het HDInsight-cluster wordt uitgevoerd en wordt gebruikt door Azure security (azsecd) om uw clusters te beschermen tegen virusaanvallen. Microsoft raadt gebruikers ten zeerste aan geen wijzigingen aan te brengen in de standaard Clamscan-configuratie.

Dit proces interfereert niet met of neemt geen cycli weg van andere processen. Het zal altijd toegeven aan andere proces. CPU-pieken van Clamscan mogen alleen worden gezien wanneer het systeem niet actief is.  

In scenario's waarin u de planning moet beheren, u de volgende stappen gebruiken:

1. Automatische uitvoering uitschakelen met de volgende opdracht:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Voeg een Cron-taak toe waarop de volgende opdracht als hoofdwordt uitgevoerd:
   
   `/usr/local/bin/azsecd manual -s clamav`

[Zie Hoe stel ik een Cron-taak in](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)voor meer informatie over het instellen en uitvoeren van een cron-taak?

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Waarom is LLAP beschikbaar op Spark ESP-clusters?
Op ESP Spark-clusters is LLAP ingeschakeld om veiligheidsredenen (d.w.z. Apache Ranger), niet om prestaties. U moet grotere node VM's gebruiken om geschikt te maken voor het resourcegebruik van LLAP (bijvoorbeeld minimaal D13V2). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Hoe kan ik extra AAD-groepen toevoegen na het maken van een ESP-cluster?
Er zijn twee manieren om dit te bereiken: 1- U het cluster opnieuw maken en de extra groep toevoegen op het moment van het maken van het cluster. Als u scoped-synchronisatie gebruikt in AAD-DS, moet u ervoor zorgen dat groep B is opgenomen in de scoped synchronisatie.
2- Voeg de groep toe als een geneste subgroep van de vorige groep die is gebruikt om het ESP-cluster te maken. Als u bijvoorbeeld een ESP-cluster `A`met groep hebt gemaakt, u later groep `B` toevoegen als een geneste subgroep van `A` en na ongeveer een uur wordt deze automatisch gesynchroniseerd en beschikbaar in het cluster. 

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Kan ik een Azure Data Lake Storage Gen2 toevoegen aan een bestaand HDInsight-cluster als extra opslagaccount?

Nee, het is momenteel niet mogelijk om een Azure Data Lake Storage Gen2-opslagaccount toe te voegen aan een cluster met blob-opslag als primaire opslag. Zie [Opslagopties vergelijken voor](hdinsight-hadoop-compare-storage-options.md)meer informatie .

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Hoe kan ik de momenteel gekoppelde Service Principal voor een Data Lake-opslagaccount vinden?

U uw instellingen vinden in **Data Lake Storage Gen1-toegang** onder uw clustereigenschappen in de Azure-portal. Zie [Clusterinstellingen verifiëren voor](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)meer informatie .
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Hoe kan ik het gebruik van opslagaccounts en blobcontainers voor mijn HDInsight-clusters berekenen?

Voer een van de volgende handelingen uit:

- [PowerShell gebruiken](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Zoek de grootte van de */user/hive/. Prullenbak/map* op het HDInsight-cluster met de volgende opdrachtregel:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Hoe kan ik controle instellen voor mijn blob-opslagaccount?

Als u blob-opslagaccounts wilt controleren, configureert u de controle met behulp van de procedure bij [Een opslagaccount controleren in de Azure-portal.](../storage/common/storage-monitor-storage-account.md) Een HDFS-auditlogboek bevat alleen controlegegevens voor het lokale HDFS-bestandssysteem (hdfs://mycluster).  Het omvat geen bewerkingen die worden uitgevoerd op externe opslag.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Hoe kan ik bestanden overbrengen tussen een blobcontainer en een HDInsight-hoofdknooppunt?

Voer een script uit dat vergelijkbaar is met het volgende shellscript op uw hoofdknooppunt:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Het bestand *filenames.txt* heeft het absolute pad van de bestanden in de blobcontainers.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Zijn er Ranger-plug-ins voor opslag?

Momenteel bestaat er geen Ranger-plug-in voor blob-opslag en Azure Data Lake Storage Gen1 of Gen2. Voor ESP-clusters moet u Azure Data Lake Storage gebruiken, omdat u op zijn minst handmatig fine-grain-machtigingen instellen op bestandssysteemniveau met HDFS-hulpprogramma's. Wanneer esp-clusters Azure Data Lake Storage gebruiken, worden een deel van het toegangsbeheer voor bestandssystemen ook gebruikt met Azure Active Directory op clusterniveau. 

U met de Azure Storage Explorer beleid voor gegevenstoegang toewijzen aan de beveiligingsgroepen van uw gebruikers. Zie voor meer informatie:

- [Hoe stel ik machtigingen in voor Azure AD-gebruikers om gegevens op te vragen in Data Lake Storage Gen2 met behulp van Hive of andere services?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Machtigingen op bestands- en mapniveau instellen voor Azure Data Lake Storage Gen2 met behulp van Azure Storage Explorer](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Kan ik de HDFS-opslag op een cluster vergroten zonder de schijfgrootte van werknemersknooppunten te vergroten?

Nee, u de schijfgrootte van een werkknooppunt niet vergroten, dus de enige manier om de schijfgrootte te vergroten, is door het cluster te laten vallen en opnieuw te maken met grotere vm's van werknemers. Gebruik HDFS niet voor het opslaan van uw HDInsight-gegevens, omdat de gegevens worden verwijderd als u uw cluster verwijdert. Sla in plaats daarvan uw gegevens op in Azure. Het opschalen van het cluster kan ook extra capaciteit toevoegen aan uw HDInsight-cluster.

## <a name="edge-nodes"></a>Edge-knooppunten

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Kan ik een randknooppunt toevoegen nadat het cluster is gemaakt?

HDInsight-cluster of naar een nieuw cluster bij het maken van het cluster. Zie [Lege Edge-knooppunten op Apache Hadoop-clusters gebruiken in HDInsight](hdinsight-apps-use-edge-node.md) voor meer informatie.

### <a name="how-can-i-connect-to-an-edge-node"></a>Hoe kan ik verbinding maken met een randknooppunt?

Nadat u een randknooppunt hebt gemaakt, u er verbinding mee maken met SSH op poort 22. U de naam van het randknooppunt vinden in de clusterportal. De namen eindigen meestal met *-ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Waarom worden doorgaande scripts niet automatisch uitgevoerd op nieuw gemaakte randknooppunten?

U gebruikt doorgaande scripts om nieuwe werknemersknooppunten aan te passen die aan het cluster zijn toegevoegd door middel van schaalbewerkingen. Doorgaande scripts zijn niet van toepassing op randknooppunten.

## <a name="rest-api"></a>REST-API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Wat zijn de REST API-aanroepen om een Tez-queryweergave uit het cluster te halen?

U de volgende REST-eindpunten gebruiken om de benodigde informatie in JSON-indeling op te halen. Gebruik basisverificatiekoppen om de aanvragen in te voeren.

- Tez Query View: *https:\//\<clusternaam>.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- Tez Dag View: *https:\//\<clusternaam>.azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Hoe haal ik de configuratiegegevens op uit het HDI-cluster met behulp van een Azure Active Directory-gebruiker?

Als u met uw AAD-gebruiker over de juiste verificatietokens wilt onderhandelen, gaat u via de gateway met de volgende indeling:

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Hoe gebruik ik Ambari Restful API om de prestaties van YARN te controleren?

Als u de opdracht Curl aanroept in hetzelfde virtuele netwerk of een virtueel netwerk met peered, is de opdracht:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Als u de opdracht van buiten het virtuele netwerk of vanuit een niet-peered virtueel netwerk aanroept, is de opdrachtindeling:

- Voor een niet-ESP-cluster:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- Voor een ESP-cluster:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Curl zal u om een wachtwoord vragen. U moet een geldig wachtwoord invoeren voor de gebruikersnaam van het inloggen van het cluster.

## <a name="billing"></a>Billing

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Hoeveel kost het implementeren van een HDInsight-cluster?

Zie de pagina [Azure HDInsight Pricing voor](https://azure.microsoft.com/pricing/details/hdinsight/) meer informatie over prijzen en veelgestelde vragen over facturering.

### <a name="when-does-hdinsight-billing-start--stop"></a>Wanneer begint HDInsight-facturering & te stoppen?

De facturering voor het gebruik van HDInsight-clusters begint zodra er een cluster is gemaakt en stopt als een cluster wordt verwijderd. Facturering wordt naar rato per minuut beoordeeld.

### <a name="how-do-i-cancel-my-subscription"></a>Hoe annuleer ik mijn abonnement?

Zie [Uw Azure-abonnement opzeggen](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)voor informatie over het opzeggen van uw abonnement.

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Wat gebeurt er voor betalen per gebruik-abonnementen nadat ik mijn abonnement heb opgezegd?

Zie [Wat gebeurt er nadat ik mijn abonnement heb opgezegd voor](/azure/billing/billing-how-to-cancel-azure-subscription) informatie over uw abonnement?

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Waarom wordt de Hive-versie weergegeven als 1.2.1000 in plaats van 2.1 in de Ambari-gebruikersinterface, ook al heb ik een HDInsight 3.6-cluster?

Hoewel slechts 1.2 verschijnt in de Ambari UI, bevat HDInsight 3.6 zowel Hive 1.2 als Hive 2.1.

## <a name="other-faq"></a>Andere veelgestelde vragen

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Wat biedt HDInsight in termen van real-time stream verwerkingsmogelijkheden?

Zie [Een streamverwerkingstechnologie kiezen in Azure](/azure/architecture/data-guide/technology-choices/stream-processing)voor informatie over de integratiemogelijkheden van streamverwerking in Azure HDInsight.

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Is er een manier om het hoofdknooppunt van het cluster dynamisch te beëindigen wanneer het cluster voor een bepaalde periode niet actief is?

Dit kan niet met HDInsight clusters. U Azure Data Factory voor deze scenario's gebruiken.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Welk compliance-aanbod biedt HDInsight?

Zie het Microsoft [Trust Center](https://www.microsoft.com/trust-center) en het [overzicht van microsoft Azure-compliance](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)voor naleving.
