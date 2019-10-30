---
title: Veelgestelde vragen over Azure HDInsight
description: Veelgestelde vragen over HDInsight
keywords: Veelgestelde vragen
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 5b9011e2a95dc1bdb86311111123db3c994f3aee
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044938"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: veelgestelde vragen

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over het uitvoeren van [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>HDInsight-clusters maken of verwijderen

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Een HDInsight-cluster Hoe kan ik inrichten?

Zie [clusters instellen in HDInsight met Apache Hadoop, Apache Spark, Apache Kafka en meer voor informatie](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)over de beschik bare typen HDInsight-clusters en de inrichtings methoden.

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Hoe kan ik een bestaand HDInsight-cluster verwijderen?

Zie [een HDInsight-cluster verwijderen](hdinsight-delete-cluster.md)voor meer informatie over het verwijderen van een cluster dat niet meer in gebruik is.

Zorg ervoor dat er ten minste 30 tot 60 minuten tussen Create-en delete-bewerkingen staan. Anders kan de bewerking mislukken met het volgende fout bericht:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Hoe kan ik selecteert u het juiste aantal kernen of knoop punten voor mijn werk belasting?

Het juiste aantal kern geheugens en andere configuratie opties zijn afhankelijk van verschillende factoren.

Zie [capaciteits planning voor HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning)voor meer informatie.

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Wat kan ik doen wanneer het inrichten van een cluster mislukt vanwege een capaciteits probleem?

In deze sectie worden veelvoorkomende problemen met de capaciteit en de oplossings technieken beschreven.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Fout: de implementatie zou het quotum van ' 800 ' overschrijden

Azure heeft een quotumlimiet van 800 implementaties per resourcegroep. Verschillende quota's worden toegepast per resource groep, abonnement, account of andere scopes. Uw abonnement mag bijvoorbeeld worden geconfigureerd om het aantal kerngeheugens voor een regio te beperken. Als u probeert een virtuele machine te implementeren met meer kern geheugens dan de toegestane hoeveelheid, wordt een fout bericht weer gegeven dat aangeeft dat het quotum is overschreden.

U kunt dit probleem oplossen door de implementaties te verwijderen die niet langer nodig zijn met behulp van de Azure Portal, CLI of Power shell.

Zie voor meer informatie [Solve errors for resource quotas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) (Fouten voor resourcequota oplossen).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Fout: het maximale knoop punt heeft de beschik bare kernen in deze regio overschreden

Uw abonnement mag worden geconfigureerd om het aantal kerngeheugens voor een regio te beperken. Als u probeert een bron te implementeren met meer kern geheugens dan de toegestane hoeveelheid, wordt een fout bericht weer gegeven dat aangeeft dat het quotum is overschreden.

Voer de volgende stappen uit om een quotumverhoging aan te vragen:

1. Ga naar de [Azure Portal](https://portal.azure.com)en selecteer **Help + ondersteuning**.
   
1. Selecteer **Nieuwe ondersteuningsaanvraag**.
   
1. Op het tabblad **basis principes** van de pagina **nieuwe ondersteunings aanvraag** geeft u de volgende informatie op:
   
   - **Type probleem:** **Service-en abonnements limieten (quota's)** selecteren.
   - **Abonnement:** Selecteer het abonnement dat u wilt wijzigen.
   - **Quotum type:** Selecteer **HDInsight**.

Raadpleeg [Create a support ticket to increase core](hdinsight-capacity-planning.md#quotas) (Een ondersteuningsticket maken om het aantal kerngeheugens te verhogen) voor meer informatie.

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Wat zijn de verschillende typen knoop punten in een HDInsight-cluster?

Azure HDInsight-clusters hebben verschillende typen virtuele machines of knoop punten. Elk knooppunt type speelt een rol in de werking van het systeem.

Zie [resource typen in azure HDInsight-clusters](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)voor meer informatie.

## <a name="individual-components"></a>Afzonderlijke onderdelen

### <a name="can-i-install-additional-components-on-my-cluster"></a>Kan ik extra onderdelen installeren op mijn cluster?

Ja. Als u extra onderdelen wilt installeren of cluster configuratie wilt aanpassen, gebruikt u:

- Scripts tijdens of na het maken. Scripts worden aangeroepen via [script actie](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), een configuratie optie die u kunt gebruiken vanuit de Azure Portal, Hdinsight Windows Power shell-cmdlets of de HDINSIGHT .NET SDK. Deze configuratie optie kan worden gebruikt vanuit de Azure Portal, HDInsight Windows Power shell-cmdlets of de HDInsight .NET SDK.

- `sudo` of andere methoden nadat u het cluster hebt ingericht.
  
- [HDInsight-toepassings platform](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) voor het installeren van ecosysteem toepassingen.

Microsoft Ondersteuning teams kunnen echter alleen ondersteuning bieden voor de volgende situaties:

- Problemen of fouten die optreden tijdens het laden van het script. Fouten die optreden tijdens het uitvoeren van aangepaste scripts vallen buiten het bereik van een ondersteunings ticket.

- Aanvullende toepassingen die deel uitmaken van het proces dat door het cluster wordt gemaakt. 

Zie [Wat zijn de Apache Hadoop onderdelen en versies die beschikbaar zijn in HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions) voor een lijst met ondersteunde onderdelen?

De ondersteuning voor afzonderlijke onderdelen kan ook variëren per clustertype. Spark wordt bijvoorbeeld niet ondersteund in een Kafka-cluster en vice versa.

Neem voor toepassingen en services buiten het proces voor het maken van het cluster contact op met de leverancier of service provider voor ondersteuning. U kunt ook veel Community-ondersteunings sites gebruiken. Voor beelden zijn het [MSDN-forum voor HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) en [stack overflow](https://stackoverflow.com/). Apache-projecten hebben ook project sites op de [Apache-website](https://apache.org/). Een voor beeld is [Hadoop](https://hadoop.apache.org/). 

Zie de [Veelgestelde vragen over ondersteuning voor Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over ondersteuning voor Azure.

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Kan ik een upgrade uitvoeren voor de afzonderlijke onderdelen die vooraf zijn geïnstalleerd op het cluster?

Als u een upgrade uitvoert van ingebouwde onderdelen of toepassingen die vooraf zijn geïnstalleerd op uw cluster, wordt de resulterende configuratie niet ondersteund door micro soft. Deze systeem configuraties zijn niet getest door micro soft. Probeer een andere versie van het HDInsight-cluster te gebruiken waarop mogelijk al de bijgewerkte versie van het onderdeel is geïnstalleerd.

Een upgrade van Hive als een afzonderlijk onderdeel wordt bijvoorbeeld niet ondersteund. HDInsight is een beheerde service en veel services zijn geïntegreerd met Ambari-server en getest. Als u een Hive op zichzelf bijwerkt, worden de geïndexeerde binaire bestanden van andere onderdelen gewijzigd en ontstaan er problemen met de integratie van onderdelen in uw cluster.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Kan Spark en Kafka worden uitgevoerd op hetzelfde HDInsight-cluster?

Nee, het is niet mogelijk om Apache Kafka en Apache Spark uit te voeren op hetzelfde HDInsight-cluster. Maak afzonderlijke clusters voor Kafka en Spark om problemen met bron conflicten te voor komen.

### <a name="how-do-i-change-timezone-in-ambari"></a>Hoe kan ik tijd zone wijzigen in Ambari?

1. Open de Ambari-webgebruikersinterface op https://CLUSTERNAME.azurehdinsight.net, waarbij CLUSTERNAME de naam van uw cluster is.
2. Selecteer in de rechter bovenhoek de optie beheer | Instellingen. 

   ![Ambari-instellingen](media/hdinsight-faq/ambari-settings.png)

3. Selecteer in het venster gebruikers instellingen de nieuwe tijd zone in de vervolg keuzelijst tijd zone en klik vervolgens op opslaan.

   ![Gebruikers instellingen Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Meta Store

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Hoe kan ik migreren van de bestaande meta Store naar Azure SQL Server? 

Zie [zelf studie: SQL Server migreren naar een enkele data base of gegroepeerde Data base in Azure SQL database offline met behulp van DMS](../dms/tutorial-sql-server-to-azure-sql.md)als u wilt migreren van SQL Server naar Azure SQL Server.

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Wordt het Hive-metastore verwijderd wanneer het cluster wordt verwijderd?

Dit is afhankelijk van het type meta Store dat uw cluster heeft geconfigureerd voor gebruik.

Voor een standaard-META Store: de standaard-META Store maakt deel uit van de cluster levenscyclus. Wanneer u een cluster verwijdert, worden ook de corresponderende meta Store-gegevens verwijderd.

Voor een aangepaste meta Store: de levens cyclus van de meta Store is niet gebonden aan de levens cyclus van een cluster. Daarom kunt u clusters maken en verwijderen zonder dat meta gegevens verloren gaan. Meta gegevens zoals uw Hive-schema's blijven behouden, zelfs nadat u het HDInsight-cluster hebt verwijderd en opnieuw hebt gemaakt.

Zie [externe meta gegevens archieven gebruiken in azure HDInsight](hdinsight-use-external-metadata-stores.md)voor meer informatie.

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Migreert migratie van een Hive-metastore ook het standaard beleid van de zwerver-data base?

Nee, de beleids definitie bevindt zich in de zwerver-data base, dus migreert de zwerver-data base het beleid.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Kunt u een Hive-metastore van een Enterprise Security Package (ESP)-cluster migreren naar een niet-ESP-cluster en vice versa?

Ja, u kunt een Hive-metastore migreren van een ESP naar een niet-ESP-cluster.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hoe kan ik een schatting maken van de omvang van een Hive-metastore-data base?

Een Hive-metastore wordt gebruikt voor het opslaan van de meta gegevens voor gegevens bronnen die door de Hive-server worden gebruikt. De grootte vereisten zijn deels afhankelijk van het aantal en de complexiteit van uw Hive-gegevens bronnen en kunnen niet worden geschat aan de voor grond. Zoals beschreven in [Hive-metastore aanbevolen procedures](hdinsight-use-external-metadata-stores.md#hive-metastore-best-practices), kunt u beginnen met een S2-laag, die 50 DTU en 250 GB opslag biedt, en als u een knel punt ziet, kunt u de data base omhoog schalen.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Ondersteunt u andere data bases dan Azure SQL Database als een externe meta Store?

Nee, micro soft ondersteunt alleen Azure SQL Database als een externe, aangepaste meta Store.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Kan ik een meta Store over meerdere clusters delen?

Ja, u kunt een aangepaste meta Store delen in meerdere clusters zolang deze dezelfde versie van HDInsight gebruiken.

## <a name="connectivity-and-virtual-networks"></a>Connectiviteit en virtuele netwerken  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Wat zijn de implicaties van het blok keren van poorten 22 en 23 op mijn netwerk?

Als u poort 22 en poort 23 blokkeert, hebt u geen SSH-toegang tot het cluster. Deze poorten worden niet gebruikt door de HDInsight-service.

Raadpleeg de volgende documenten voor meer informatie:

- [Netwerk verkeer beheren](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [Binnenkomend verkeer naar HDInsight-clusters in een virtueel netwerk met een persoonlijk eind punt beveiligen](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [IP-adressen van HDInsight-beheer](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Kan ik een extra virtuele machine implementeren in hetzelfde subnet als een HDInsight-cluster?

Ja, u kunt een extra virtuele machine implementeren binnen hetzelfde subnet als een HDInsight-cluster. De volgende configuraties zijn mogelijk:

- Edge-knoop punten: u kunt een ander Edge-knoop punt toevoegen aan het cluster, zoals wordt beschreven in [lege Edge-knoop punten gebruiken op Apache Hadoop clusters in HDInsight](hdinsight-apps-use-edge-node.md).

- Zelfstandige knoop punten: u kunt een zelfstandige virtuele machine toevoegen aan hetzelfde subnet en vanaf die virtuele machine toegang krijgen tot het cluster met behulp van het persoonlijke eind punt `https://<CLUSTERNAME>-int.azurehdinsight.net`. Zie [netwerk verkeer beheren](hdinsight-plan-virtual-network-deployment.md#networktraffic)voor meer informatie.

### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Kan ik een bestaand HDInsight-cluster toevoegen aan een ander virtueel netwerk?

Nee, dat kan niet. Het virtuele netwerk moet worden opgegeven op het moment van de inrichting. Als er tijdens het inrichten geen virtueel netwerk is opgegeven, maakt de implementatie een intern netwerk dat niet toegankelijk is vanaf buiten. Zie [HDInsight toevoegen aan een bestaand virtueel netwerk](hdinsight-plan-virtual-network-deployment.md#existingvnet)voor meer informatie.

## <a name="security-and-certificates"></a>Beveiliging en certificaten

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Wat zijn de aanbevelingen voor beveiliging tegen schadelijke software in azure HDInsight-clusters?

Zie [micro soft antimalware voor Azure Cloud Services en virtual machines](../security/fundamentals/antimalware.md)voor informatie over beveiliging tegen schadelijke software.

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Hoe kan ik een keytab voor een HDInsight ESP-cluster maken?

Maak een Kerberos-keytab voor de gebruikers naam van uw domein. U kunt deze keytab later gebruiken om te verifiëren bij clusters die zijn toegevoegd aan een extern domein zonder een wacht woord in te voeren. De domein naam is hoofdletter:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Kan ik een bestaande Azure Active Directory Tenant gebruiken om een HDInsight-cluster te maken dat de ESP heeft?

U moet Azure Active Directory Domain Services (Azure AD DS) inschakelen voordat u een HDInsight-cluster met ESP kunt maken. Open-source Hadoop is afhankelijk van Kerberos voor authenticatie (in plaats van OAuth).

Als u Vm's wilt toevoegen aan een domein, moet u een domein controller hebben. Azure AD DS is de beheerde domein controller en wordt beschouwd als een uitbrei ding van Azure Active Directory die alle Kerberos-vereisten biedt om een veilig Hadoop-cluster op een beheerde manier te bouwen. HDInsight als een beheerde service kan worden geïntegreerd met Azure AD DS om end-to-end beveiliging te bieden.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Kan ik een zelfondertekend certificaat gebruiken in een beveiligde LDAP-installatie van AAD-DS en een ESP-cluster inrichten?

Het gebruik van een certificaat dat is uitgegeven door een certificerings instantie wordt aanbevolen, maar het gebruik van een zelfondertekend certificaat wordt ook ondersteund op ESP. Zie voor meer informatie:

- [Azure Active Directory Domain Services inschakelen](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Zelf studie: secure LDAP configureren voor een Azure Active Directory Domain Services beheerd domein](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Hoe kan ik de aanmeldings activiteiten in zwerver weer geven?

Voor controle vereisten raadt micro soft aan Azure Monitor Logboeken in te scha kelen zoals beschreven in [Azure monitor Logboeken gebruiken om HDInsight-clusters te controleren](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Kan ik clamscan uitschakelen op mijn cluster?

Clamscan is de antivirus software die wordt uitgevoerd op het HDInsight-cluster en wordt gebruikt door Azure Security (azsecd) om uw clusters te beschermen tegen virus aanvallen. Micro soft raadt u ten zeerste aan dat gebruikers geen wijzigingen aanbrengen in de standaard clamscan-configuratie.

Dit proces is niet van invloed op een of meer cycli van andere processen. Er wordt altijd naar een ander proces geoogst. CPU-pieken van clamscan moeten alleen worden gezien wanneer het systeem niet actief is.  

In scenario's waarin u het schema moet beheren, kunt u de volgende stappen uitvoeren:

1. Schakel automatische uitvoering uit met de volgende opdracht:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Voeg een cron-taak toe die de volgende opdracht als root uitvoert:
   
   `/usr/local/bin/azsecd manual -s clamav`

Zie [Hoe kan ik een cron instellen](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)voor meer informatie over het instellen en uitvoeren van een cron-taak?

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Waarom is LLAP beschikbaar in Spark ESP-clusters?
Op ESP Spark-clusters is LLAP ingeschakeld om beveiligings redenen (dat wil zeggen Apache zwerver), niet voor prestaties. U moet grotere Vm's voor knoop punten gebruiken voor het resource gebruik van LLAP (bijvoorbeeld minimale D13V2). 

### <a name="how-can-i-add-addional-aad-groups-after-creating-an-esp-cluster"></a>Hoe kan ik aanvullende AAD-groepen toevoegen nadat ik een ESP-cluster heb gemaakt?
Er zijn twee manieren om dit te doen: 1. u kunt het cluster opnieuw maken en de extra groep toevoegen op het moment dat het cluster wordt gemaakt. Als u scoped Synchronization in AAD-DS gebruikt, moet u ervoor zorgen dat groep B is opgenomen in de synchronisatie met het bereik.
2: Voeg de groep toe als een geneste subgroep van de vorige groep die is gebruikt voor het maken van het ESP-cluster. Als u bijvoorbeeld een ESP-cluster met groeps `A`hebt gemaakt, kunt u later op groep toevoegen `B` als een geneste subgroep van `A` en na ongeveer één uur dat deze automatisch wordt gesynchroniseerd en beschikbaar is in het cluster. 

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Kan ik een Azure Data Lake Storage Gen2 toevoegen aan een bestaand HDInsight-cluster als een extra opslag account?

Nee, het is momenteel niet mogelijk om een Azure Data Lake Storage Gen2 Storage-account toe te voegen aan een cluster met Blob Storage als primaire opslag. Zie voor meer informatie [opslag opties vergelijken](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Hoe kan ik de momenteel gekoppelde Service-Principal vinden voor een Data Lake Storage-account?

U kunt uw instellingen vinden in **Data Lake Storage gen1 toegang** onder de cluster eigenschappen in de Azure Portal. Zie [cluster installatie controleren](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)voor meer informatie.
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Hoe kan ik het gebruik van opslag accounts en BLOB-containers berekenen voor mijn HDInsight-clusters?

Voer een van de volgende handelingen uit:

- [PowerShell gebruiken](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- De grootte van de */User/Hive/. zoeken Prullen mand/* map op het HDInsight-cluster met behulp van de volgende opdracht regel:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Hoe kan ik controle instellen voor mijn Blob Storage-account?

Als u Blob Storage-accounts wilt controleren, configureert u bewaking met behulp van de procedure bij het [controleren van een opslag account in de Azure Portal](../storage/common/storage-monitor-storage-account.md). Een HDFS-audit logboek biedt alleen controle-informatie voor het lokale bestands systeem HDFS (hdfs://mycluster).  Het bevat geen bewerkingen die worden uitgevoerd op externe opslag.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Hoe kan ik bestanden overdragen tussen een BLOB-container en een HDInsight-hoofd knooppunt?

Voer een script uit dat vergelijkbaar is met het volgende shell script op het hoofd knooppunt:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Het bestand *filenames. txt* heeft het absolute pad van de bestanden in de BLOB-containers.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Zijn er zwerver-invoeg toepassingen voor opslag?

Momenteel bestaat er geen zwerver-invoeg toepassing voor Blob Storage en Azure Data Lake Storage Gen1 of Gen2. Voor ESP-clusters moet u Azure Data Lake Storage gebruiken, omdat u Mini maal nauw keurige machtigingen hand matig op het bestandssysteem niveau kunt instellen met behulp van HDFS-hulpprogram ma's. Bij het gebruik van Azure Data Lake Storage worden ESP-clusters een deel van het toegangs beheer voor bestands systemen met behulp van Azure Active Directory op cluster niveau. 

U kunt beleid voor gegevens toegang toewijzen aan de beveiligings groepen van uw gebruikers met behulp van de Azure Storage Explorer. Zie voor meer informatie:

- [Hoe kan ik machtigingen instellen voor Azure AD-gebruikers om gegevens op te vragen in Data Lake Storage Gen2 met behulp van Hive of andere services?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Machtigingen voor bestanden en mapniveau instellen met behulp van Azure Storage Explorer met Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Kan ik de HDFS-opslag op een cluster verg Roten zonder de schijf grootte van worker-knoop punten te verhogen?

Nee, u kunt de schijf grootte van een worker-knoop punt niet verhogen. de enige manier om de schijf grootte te verg Roten, is het verwijderen van het cluster en het opnieuw maken met grotere werk-Vm's. Gebruik geen HDFS om uw HDInsight-gegevens op te slaan, omdat de gegevens worden verwijderd als u het cluster verwijdert. In plaats daarvan slaat u uw gegevens op in Azure. Door het cluster omhoog te schalen, kunt u ook extra capaciteit toevoegen aan uw HDInsight-cluster.

## <a name="edge-nodes"></a>Edge-knoop punten

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Kan ik een Edge-knoop punt toevoegen nadat het cluster is gemaakt?

HDInsight-cluster of een nieuw cluster bij het maken van het cluster. Zie [Lege Edge-knooppunten op Apache Hadoop-clusters gebruiken in HDInsight](hdinsight-apps-use-edge-node.md) voor meer informatie.

### <a name="how-can-i-connect-to-an-edge-node"></a>Hoe kan ik verbinding maken met een Edge-knoop punt?

Nadat u een Edge-knoop punt hebt gemaakt, kunt u er verbinding mee maken via SSH op poort 22. U kunt de naam van het Edge-knoop punt vinden in de cluster Portal. De namen eindigen meestal met *-Ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Waarom worden persistente scripts niet automatisch uitgevoerd op nieuw gemaakte Edge-knoop punten?

U gebruikt persistente scripts om nieuwe worker-knoop punten die aan het cluster worden toegevoegd, aan te passen via schaal bewerkingen. Persistente scripts zijn niet van toepassing op Edge-knoop punten.

## <a name="rest-api"></a>REST-API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Wat zijn de REST API-aanroepen om een TEZ-query weergave uit het cluster te halen?

U kunt de volgende REST-eind punten gebruiken om de benodigde gegevens in JSON-indeling te halen. Basis verificatie headers gebruiken om de aanvragen uit te voeren.

- TEZ-query weergave: *https:\//\<cluster name >. azurehdinsight. net/WS/v1/Timeline/HIVE_QUERY_ID/*
- TEZ dag weer gave: *https:\//\<cluster name >. azurehdinsight. net/WS/v1/Timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Hoe kan ik de configuratie gegevens uit het HDI-cluster op te halen met behulp van een Azure Active Directory gebruiker?

Als u de juiste verificatie tokens met uw AAD-gebruiker wilt onderhandelen, gaat u door de gateway via de volgende indeling:

* https://`<cluster dnsname>`. azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Hoe kan ik Ambari rest API gebruiken om de prestaties van een garen te controleren?

Als u de krul opdracht aanroept in hetzelfde virtuele netwerk of in een gekoppeld virtueel netwerk, is de opdracht:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Als u de opdracht van buiten het virtuele netwerk of vanuit een niet-gekoppeld virtueel netwerk aanroept, is de opdracht indeling:

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
> Met krul wordt u om een wacht woord gevraagd. U moet een geldig wacht woord opgeven voor de gebruikers naam van het cluster.

## <a name="billing"></a>Billing

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Hoeveel kost het om een HDInsight-cluster te implementeren?

Zie de pagina met [prijzen voor Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) voor meer informatie over de prijzen en veelgestelde vragen met betrekking tot facturering.

### <a name="when-does-hdinsight-billing-start--stop"></a>Wanneer wordt de facturering van HDInsight gestart & stoppen?

De facturering voor het gebruik van HDInsight-clusters begint zodra er een cluster is gemaakt en stopt als een cluster wordt verwijderd. De facturering is Pro-beoordeeld per minuut.

### <a name="how-do-i-cancel-my-subscription"></a>Hoe kan ik mijn abonnement annuleren?

Zie [uw Azure-abonnement annuleren](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)voor meer informatie over het annuleren van uw abonnement.

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Wat gebeurt er nadat ik mijn abonnement heb geannuleerd? voor betalen per gebruik-abonnementen?

Zie [Wat gebeurt er nadat ik mijn abonnement heb geannuleerd?](/azure/billing/billing-how-to-cancel-azure-subscription) voor informatie over uw abonnement nadat dit is geannuleerd?

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Waarom wordt de Hive-versie weer gegeven als 1.2.1000 in plaats van 2,1 in de Ambari-gebruikers interface, zelfs als ik een HDInsight 3,6-cluster uitvoer?

Hoewel alleen 1,2 wordt weer gegeven in de Ambari-gebruikers interface, bevat HDInsight 3,6 zowel Hive 1,2 als hive 2,1.

## <a name="other-faq"></a>Overige veelgestelde vragen

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Wat biedt HDInsight voor real-time verwerking van stroom mogelijkheden?

Voor informatie over de integratie mogelijkheden van stroom verwerking in azure HDInsight raadpleegt u [een stroom verwerkings technologie kiezen in azure](/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Is er een manier om het hoofd knooppunt van het cluster dynamisch te beëindigen wanneer het cluster gedurende een bepaalde periode niet actief is?

U kunt dit niet doen met HDInsight-clusters. U kunt Azure Data Factory voor deze scenario's gebruiken.
