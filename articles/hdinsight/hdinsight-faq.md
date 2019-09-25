---
title: Veelgestelde vragen over Azure HDInsight
description: Biedt een overzicht van Azure HDInsight
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: v-miegge
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 4accd8c2f58dd89fb5f918ca6e58e71caeaed57f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212753"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Veelgestelde vragen

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over het uitvoeren van [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>HDInsight-clusters maken of verwijderen

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Een HDInsight-cluster Hoe kan ik inrichten?

Zie [clusters instellen in HDInsight met Apache Hadoop, Apache Spark, Apache Kafka en meer voor informatie](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)over de beschik bare typen HDInsight-clusters en de inrichtings methoden.

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Hoe kan ik een bestaand HDInsight-cluster verwijderen?

Voor meer informatie over het verwijderen van een cluster als het niet meer wordt gebruikt, raadpleegt u [een HDInsight-cluster verwijderen met uw browser, Power shell of de Azure cli](https://docs.microsoft.com/azure/hdinsight/hdinsight-delete-cluster). Het is raadzaam dat er ten minste 30 tot 60 minuten tussen de bewerking maken en verwijderen bestaan op één cluster. Anders mislukt de bewerking en wordt het volgende fout bericht weer gegeven:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Hoe kan ik selecteert u het juiste aantal kernen of knoop punten voor mijn werk belasting?

Het juiste aantal kern geheugens en andere configuratie opties zijn afhankelijk van verschillende factoren.

Zie [capaciteits planning voor HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning)voor meer informatie.

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>Wat kan ik doen wanneer het inrichten van een cluster mislukt vanwege een capaciteits probleem?

In deze sectie worden veelvoorkomende problemen met de capaciteit en de oplossings technieken beschreven.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Fout: De implementatie overschrijdt het quotum van ' 800 '

Azure heeft een quotumlimiet van 800 implementaties per resourcegroep. Verschillende quota's worden toegepast per resource groep, abonnement, account of andere scopes. Uw abonnement mag bijvoorbeeld worden geconfigureerd om het aantal kerngeheugens voor een regio te beperken. Als u probeert een virtuele machine te implementeren met meer kern geheugens dan de toegestane hoeveelheid, wordt een fout bericht weer gegeven dat aangeeft dat het quotum is overschreden.

U kunt dit probleem oplossen door de implementaties te verwijderen die niet langer nodig zijn met behulp van de Azure Portal, CLI of Power shell.

Zie voor meer informatie [Solve errors for resource quotas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors) (Fouten voor resourcequota oplossen).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Fout: Het maximale knoop punt heeft de beschik bare kernen in deze regio overschreden

Uw abonnement mag worden geconfigureerd om het aantal kerngeheugens voor een regio te beperken. Als u probeert een bron te implementeren met meer kern geheugens dan de toegestane hoeveelheid, wordt een fout bericht weer gegeven dat aangeeft dat het quotum is overschreden.

Voer de volgende stappen uit om een quotumverhoging aan te vragen:

1. Ga naar de Azure Portal en selecteer Help + ondersteuning.
2. Selecteer een nieuwe ondersteunings aanvraag.
3. Op de pagina nieuwe ondersteunings aanvraag selecteert u de volgende opties op het tabblad basis beginselen:

   * Type probleem: Service- en abonnementslimieten (quota)
   * Abonnement: Het abonnement dat u wilt wijzigen
   * Type quotum: HDInsight

Zie voor meer informatie [een ondersteunings ticket maken om de kernen te verhogen](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Wat zijn de verschillende typen knoop punten in een HDInsight-cluster?

Azure HDInsight-clusters hebben verschillende typen virtuele machines of knoop punten. Elk knooppunt type speelt een rol in de werking van het systeem.

Zie [resource typen in azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-virtual-network-architecture#resource-types-in-azure-hdinsight-clusters)voor meer informatie.

## <a name="individual-components"></a>Afzonderlijke onderdelen

### <a name="can-i-install-additional-components-on-my-cluster"></a>Kan ik extra onderdelen installeren op mijn cluster?

Ja, u kunt extra onderdelen installeren of cluster configuratie aanpassen met behulp van een van de volgende methoden:

* Scripts gebruiken tijdens of na het maken. Dergelijke scripts worden aangeroepen via een [script actie](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux). Deze configuratie optie kan worden gebruikt vanuit de Azure Portal, HDInsight Windows Power shell-cmdlets of de HDInsight .NET SDK. 
* Sudo of andere methoden gebruiken nadat het cluster is ingericht.
* Het [HDInsight-toepassings platform](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) gebruiken om ecosystem-toepassingen te installeren.

Microsoft Ondersteuning teams kunnen echter alleen ondersteuning bieden voor de volgende situaties:

* Problemen of fouten die optreden tijdens het laden van het script. Fouten die optreden tijdens het uitvoeren van aangepaste scripts vallen buiten het bereik van een ondersteunings ticket. 
* Aanvullende toepassingen die deel uitmaken van het proces dat door het cluster wordt gemaakt. 

Zie [Wat zijn de Apache Hadoop onderdelen en versies die beschikbaar zijn in HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions) voor een lijst met ondersteunde onderdelen?

De ondersteuning voor afzonderlijke onderdelen kan ook variëren per clustertype. Spark wordt bijvoorbeeld niet ondersteund in een Kafka-cluster en vice versa.

Neem contact op met de leverancier of service provider voor ondersteuning voor toepassingen of services buiten het proces voor het maken van het cluster. U kunt ook community-sites gebruiken ter ondersteuning van deze acties. Er zijn veel community-sites beschikbaar. Voor beelden zijn [MSDN-forum voor HDInsight](https://social.msdn.microsoft.com/Forums/home?forum=hdinsight) en [stack overflow](https://stackoverflow.com/questions/tagged/hdinsight). Apache-projecten hebben ook project sites op de [Apache-website](https://apache.org/). Een voor beeld is [Hadoop](https://hadoop.apache.org/).

 Lees de [Veelgestelde vragen over ondersteuning van Azure](https://azure.microsoft.com/en-us/support/faq/)voor meer vragen over ondersteuning voor Azure.

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Kan ik een upgrade uitvoeren voor de afzonderlijke onderdelen die vooraf zijn geïnstalleerd op het cluster?

Als u een upgrade uitvoert van ingebouwde onderdelen of toepassingen die vooraf zijn geïnstalleerd op uw cluster, wordt de resulterende configuratie niet ondersteund door micro soft. Deze systeem configuraties zijn niet getest door micro soft. Probeer een andere versie van het HDInsight-cluster te gebruiken waarop mogelijk al de bijgewerkte versie van het onderdeel is geïnstalleerd.

Een upgrade van Hive als een afzonderlijk onderdeel wordt bijvoorbeeld niet ondersteund. HDInsight is een beheerde service en veel services zijn geïntegreerd met Ambari-server en getest. Als u een Hive op zichzelf bijwerkt, worden de geïndexeerde binaire bestanden van andere onderdelen gewijzigd en ontstaan er problemen met de integratie van onderdelen in uw cluster.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Kan Spark en Kafka worden uitgevoerd op hetzelfde HDInsight-cluster?

Nee, het is niet mogelijk om Apache Kafka en Apache Spark uit te voeren op hetzelfde HDInsight-cluster. Gebruikers moeten afzonderlijke clusters maken voor elk om problemen met bron conflicten te voor komen.

### <a name="how-do-i-change-timezone-in-ambari"></a>Hoe kan ik tijd zone wijzigen in Ambari?

1. Open de Ambari-webgebruikersinterface https://CLUSTERNAME.azurehdinsight.net op, waarbij clustername de naam van uw cluster is.
2. Selecteer in de rechter bovenhoek de optie beheer | Instellingen. 

   ![Ambari-instellingen](media/hdinsight-faq/ambari-settings.png)

3. Selecteer in het venster gebruikers instellingen de nieuwe tijd zone in de vervolg keuzelijst tijd zone en klik vervolgens op opslaan.

   ![Gebruikers instellingen Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Meta Store

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Hoe kan ik migreren van de bestaande meta Store naar Azure SQL Server? 

Zie zelf studie voor informatie [over het migreren van SQL Server naar Azure SQL Server: SQL Server naar een enkele data base of gegroepeerde Data base in Azure SQL Database offline](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)migreren met behulp van DMS.

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Wordt het Hive-metastore verwijderd wanneer het cluster wordt verwijderd?

Dit is afhankelijk van het type meta Store dat uw cluster heeft geconfigureerd voor gebruik.

Voor een standaard-META Store: De standaard-META Store maakt deel uit van de cluster levenscyclus. Wanneer u een cluster verwijdert, worden ook de corresponderende meta Store-gegevens verwijderd.

Voor een aangepaste meta Store: De levens cyclus van de meta Store is niet gebonden aan de levens cyclus van een cluster. Daarom kunt u clusters maken en verwijderen zonder dat meta gegevens verloren gaan. Meta gegevens zoals uw Hive-schema's blijven behouden, zelfs nadat u het HDInsight-cluster hebt verwijderd en opnieuw hebt gemaakt.

Zie [externe meta gegevens archieven gebruiken in azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores)voor meer informatie.

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Migreert migratie van een Hive-metastore ook het standaard beleid van de zwerver-data base?

Nee, de beleids definitie bevindt zich in de zwerver-data base. Daarom wordt het beleid niet gemigreerd door de zwerver-data base te migreren.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>Kunt u een Hive-metastore van een Enterprise Security Package (ESP)-cluster migreren naar een niet-ESP-cluster en vice versa?

Ja, u kunt een Hive-metastore migreren van een ESP naar een niet-ESP-cluster.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Hoe kan ik een schatting maken van de omvang van een Hive-metastore-data base?

Een Hive-metastore wordt gebruikt voor het opslaan van de meta gegevens voor gegevens bronnen die door de Hive-server worden gebruikt. Daarom worden de grootte vereisten beïnvloed door het aantal gegevens bronnen dat u mogelijk moet gebruiken voor de Hive en hoe complex de gegevens bronnen zijn. Daarom kan de grootte niet worden geschat. Zoals beschreven in [Hive-metastore aanbevolen procedures](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores#hive-metastore-best-practices), vanaf een S2-laag, biedt 50 DTU en 250 GB opslag ruimte. Als er een knel punt optreedt, kunt u de data base omhoog schalen.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Ondersteunt u andere data bases dan Azure SQL Database als een externe meta Store?

Nee, micro soft ondersteunt alleen Azure SQL Database als een externe, aangepaste meta Store.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Kan ik een meta Store over meerdere clusters delen?

Ja, u kunt een aangepaste meta Store delen in meerdere clusters zolang deze dezelfde versie van HDInsight gebruiken.

## <a name="connectivity-and-virtual-networks"></a>Connectiviteit en virtuele netwerken  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Wat zijn de implicaties van het blok keren van poorten 22 en 23 op mijn netwerk?

Als u poort 22 en poort 23 blokkeert, hebt u geen SSH-toegang tot het cluster. Deze poorten worden niet gebruikt door de HDInsight-service.

Raadpleeg de volgende documenten voor meer informatie:

* [Netwerk verkeer beheren](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)
* [Binnenkomend verkeer naar HDInsight-clusters in een virtueel netwerk met een persoonlijk eind punt beveiligen](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
* [IP-adressen van HDInsight-beheer](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Kan ik een extra virtuele machine implementeren in hetzelfde subnet als een HDInsight-cluster?

Ja, u kunt een extra virtuele machine implementeren binnen hetzelfde subnet als een HDInsight-cluster. De volgende configuraties zijn mogelijk:

* Edge-knoop punten: U kunt nog een Edge-knoop punt toevoegen aan het cluster, zoals beschreven in [lege Edge-knoop punten gebruiken op Apache Hadoop clusters in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node). 
* Zelfstandige knoop punten:  U kunt een zelfstandige virtuele machine toevoegen aan hetzelfde subnet en het cluster vanaf die virtuele machine openen met behulp van het persoonlijke eind `https://<CLUSTERNAME>-int.azurehdinsight.net`punt. Zie [netwerk verkeer beheren](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)voor meer informatie.

### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Kan ik een bestaand HDInsight-cluster toevoegen aan een ander virtueel netwerk?

Nee, u kunt geen bestaand HDInsight-cluster toevoegen aan een ander virtueel netwerk. Het virtuele netwerk moet worden opgegeven op het moment van de inrichting. Als er tijdens het inrichten geen virtueel netwerk is opgegeven, maakt de implementatie een intern netwerk dat niet toegankelijk is vanaf buiten. Zie [HDInsight toevoegen aan een bestaand virtueel netwerk](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#existingvnet)voor meer informatie.

## <a name="security-and-certificates"></a>Beveiliging en certificaten

Wat zijn de aanbevelingen van micro soft voor beveiliging tegen schadelijke software op Azure HD Insight-clusters?

Zie [micro soft antimalware voor Azure Cloud Services en virtual machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)voor informatie over beveiliging tegen schadelijke software.

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Hoe kan ik een keytab voor een HDInsight ESP-cluster maken?

U kunt een Kerberos-keytab maken voor de gebruikers naam van uw domein. U kunt deze keytab later gebruiken om te verifiëren bij clusters die zijn toegevoegd aan een extern domein zonder een wacht woord in te voeren. Let op de hoofd letters in de domein naam:

```
o ktutil 
o ktutil: addent -password -p stXX@ DOMAIN.COM -k 1 -e RC4-HMAC 
o Password for stXX@DOMAIN.COM: provided by lab instructors 
o ktutil: wkt stXX.keytab 
o ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Kan ik een bestaande Azure Active Directory Tenant gebruiken om een HDInsight-cluster te maken dat de ESP heeft?

Het inschakelen van Azure Active Directory Domain Services (AAD-DS) is een vereiste voordat u een HDInsight-cluster kunt maken dat ESP heeft. Open-source Hadoop is afhankelijk van Kerberos voor authenticatie (in plaats van OAuth).

Als u virtuele machines wilt toevoegen aan een domein, is een domein controller vereist. AAD-DS is de beheerde domein controller. Het wordt beschouwd als een uitbrei ding van AAD die alle Kerberos-vereisten biedt om een veilig Hadoop-cluster op een beheerde manier te bouwen. HDInsight als beheerde service kan worden geïntegreerd met AAD-DS om de end-to-end beveiliging te bieden.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Kan ik een zelfondertekend certificaat gebruiken in een beveiligde LDAP-installatie van AAD-DS en een ESP-cluster inrichten?

Hoewel het gebruik van een certificaat dat is uitgegeven door een certificerings instantie (CA) wordt aanbevolen, wordt het gebruik van een zelfondertekend certificaat ook ondersteund op ESP.

Zie voor meer informatie:

* [Azure AD-DS inschakelen](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#enable-azure-ad-ds)
* [Zelfstudie: Een beveiligd LDAP configureren voor een Azure Active Directory Domain Services beheerd domein](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-ldaps)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Hoe kan ik de aanmeldings activiteiten in zwerver weer geven?

Voor controle vereisten raadt micro soft aan Azure Monitor Logboeken in te scha kelen zoals beschreven in [Azure monitor Logboeken gebruiken om HDInsight-clusters te controleren](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Kan ik clamscan uitschakelen op mijn cluster?

Clamscan is de antivirus software die wordt uitgevoerd op het HDInsight-cluster en wordt gebruikt door Azure Security (azsecd) om uw clusters te beschermen tegen virus aanvallen. Micro soft raadt u ten zeerste aan dat gebruikers geen wijzigingen aanbrengen in de standaard clamscan-configuratie.

Dit proces is niet van invloed op een of meer cycli van andere processen. Er wordt altijd naar een ander proces geoogst. CPU-pieken van clamscan moeten alleen worden gezien wanneer het systeem niet actief is.  

In scenario's waarin u het schema moet beheren, kunt u de volgende stappen uitvoeren:

1. Schakel automatische uitvoering uit met behulp van de volgende opdracht:

   ``/usr/local/vbin/azsecd config -s clamav -d Disabled``

2. Maak een cron-taak die deze opdracht uitvoert als root:

   ``/usr/local/bin/azsecd manual -s clamav``

Zie [Hoe kan ik een cron instellen](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)voor meer informatie over het instellen en uitvoeren van een cron-taak?

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Kan ik een Azure Data Lake Storage Gen2 toevoegen aan een bestaand HDInsight-cluster als een extra opslag account?

Nee. Op dit moment kunt u geen Azure Data Lake Storage Gen2 toevoegen aan een bestaand HDInsight-cluster dat Blob-opslag gebruikt als primaire opslag. Zie voor meer informatie [opslag opties vergelijken voor gebruik met Azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options). 

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Hoe kan ik de momenteel gekoppelde Service-Principal vinden voor een Data Lake Storage-account?

U kunt deze instellingen controleren door Data Lake Storage Gen1 toegang te controleren onder de cluster eigenschappen in de portal. Zie [cluster installatie controleren](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal#verify-cluster-set-up)voor meer informatie.
 
Hoe kan ik het gebruik van opslag accounts en BLOB-containers berekenen voor mijn HDInsight-clusters?
U kunt een van de volgende procedures gebruiken:

* [PowerShell gebruiken](https://docs.microsoft.com/azure/storage/scripts/storage-blobs-container-calculate-size-powershell)
* U kunt ook de grootte van de/User/Hive/. vinden Prullen mand/map op de HDI-clusters met behulp van de volgende opdracht:

   ``hdfs dfs -du -h /user/hive/.Trash/``

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Hoe kan ik controle instellen voor mijn Blob Storage-account?

Als u Blob Storage-accounts wilt controleren, moet u de bewaking configureren voor het Blob Storage-account met behulp van de procedure die wordt beschreven [in een opslag account in het Azure Portal bewaken](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account). Een HDFS-audit logboek biedt alleen controle-informatie voor het lokale bestands systeem HDFS (hdfs://mycluster).  Het bevat geen bewerkingen die worden uitgevoerd op externe opslag.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Hoe kan ik bestanden overdragen tussen een BLOB-container en een HDInsight-hoofd knooppunt?

U kunt bestanden overdragen tussen een BLOB-container en een HDInsight-hoofd knooppunt door een shell script uit te voeren op het hoofd knooppunt, vergelijkbaar met het volgende voor beeld:

```
for i in cat filenames.txt

do
         hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Het bestand *filenames. txt* heeft het absolute pad van de bestanden in de BLOB-containers.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Zijn er zwerver-invoeg toepassingen voor opslag?

Momenteel bestaan er geen zwerver-invoeg toepassingen voor Blob Storage, Azure Data Lake Storage (ADLS) gen1 of Azure Data Lake Storage Gen2. Voor ESP-clusters gebruikt u ADLS als best practice en stelt u nauw keurige machtigingen hand matig in op bestandssysteem niveau met behulp van HDFS-hulpprogram ma's. ESP-clusters hebben ook een deel van het toegangs beheer van het bestands systeem met behulp van AAD op cluster niveau wanneer u ADLS gebruikt. 

U moet de Azure Storage Explorer kunnen gebruiken om beleid voor gegevens toegang toe te wijzen aan beveiligings groepen waar uw gebruikers zich bevinden met behulp van de procedures die worden beschreven in de volgende artikelen:

* [Hoe kan ik machtigingen instellen voor Azure AD-gebruikers om gegevens op te vragen in Data Lake Storage Gen2 met behulp van Hive of andere services?](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
* [Machtigingen voor bestanden en mapniveau instellen met behulp van Azure Storage Explorer met Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Kan ik de HDFS-opslag op een cluster verg Roten zonder de schijf grootte van worker-knoop punten te verhogen?

Nee, u kunt de schijf grootte van geen enkel werk knooppunt verg Roten. De enige manier om de schijf grootte te verg Roten, is door het cluster te verwijderen en opnieuw te maken op grotere virtuele worker-machines.

Micro soft raadt u niet aan om HDFS te gebruiken voor het opslaan van uw gegevens in HDInsight, omdat de gegevens worden verwijderd wanneer u het cluster verwijdert. In plaats daarvan raden we u aan om uw gegevens op te slaan in Azure. Door het cluster omhoog te schalen, kunt u ook meer capaciteit toevoegen aan uw HDInsight-cluster.

## <a name="edge-nodes"></a>Edge-knooppunten

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Kan ik een Edge-knoop punt toevoegen nadat het cluster is gemaakt?

Ja, u kunt een leeg Edge-knoop punt toevoegen aan een bestaand HDInsight-cluster of aan een nieuw cluster wanneer u het cluster maakt.

Zie [Lege Edge-knooppunten op Apache Hadoop-clusters gebruiken in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node) voor meer informatie.

### <a name="how-can-i-connect-to-an-edge-node"></a>Hoe kan ik verbinding maken met een Edge-knoop punt?

Nadat u een Edge-knoop punt hebt gemaakt, kunt u verbinding maken met het Edge-knoop punt met behulp van de SSH op poort 22. U kunt de naam van het Edge-knoop punt in de cluster Portal vinden (de namen zijn in het algemeen eindigen op '-Ed ').

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Waarom worden persistente scripts niet automatisch uitgevoerd op nieuw gemaakte Edge-knoop punten?

Persistente scripts worden gebruikt voor het aanpassen van nieuwe werk knooppunten die aan het cluster worden toegevoegd via schaal bewerkingen en die niet van toepassing zijn op Edge-knoop punten.

## <a name="rest-api"></a>REST-API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Wat zijn de REST API-aanroepen om een TEZ-query weergave uit het cluster te halen?

U kunt de volgende REST-eind punten gebruiken om de benodigde informatie te verzamelen waarbij het antwoord in JSON-indeling wordt weer. Basis verificatie headers kunnen worden gebruikt om deze aanvragen uit te voeren.

* ' TEZ-query weer geven –`<cluster name>`https://. azurehdinsight.net/WS/v1/Timeline/HIVE_QUERY_ID/

* "TEZ dag" View- https://`<cluster name>`. azurehdinsight.net/WS/v1/Timeline/TEZ_DAG_ID/

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Hoe kan ik de configuratie gegevens uit het HDI-cluster op te halen met behulp van een Azure Active Directory gebruiker?

Als u de juiste verificatie tokens met uw AAD-gebruiker wilt onderhandelen, gaat u door de gateway via de volgende indeling:

* https://`<cluster dnsname>`. azurehdinsight.net/API/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Hoe kan ik Ambari rest API gebruiken om de prestaties van een garen te controleren?

Als de krul opdracht wordt aangeroepen in hetzelfde VNet of een gelijkwaardige VNet, gebruikt u de volgende opdracht:

``curl -u <cluster login username> -sS -G http://headnodehost:8080/api/v1/clusters/anssenllap/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
Als de opdracht wordt aangeroepen vanuit buiten het VNet of een niet-gelijkwaardig VNet, is de opdracht indeling:
 
* Voor een niet-ESP-cluster:``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
* Voor een ESP-cluster:``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``

> [!NOTE]
> Met krul wordt u om een wacht woord gevraagd. U moet een geldig wacht woord opgeven voor de gebruikers naam van het cluster.

## <a name="billing"></a>Billing

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Hoeveel kost het om een HDInsight-cluster te implementeren?

Zie de pagina met [prijzen voor Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) voor meer informatie over de prijzen en veelgestelde vragen met betrekking tot facturering.

### <a name="how-do-i-cancel-my-subscription"></a>Hoe kan ik mijn abonnement annuleren?

Zie [uw Azure-abonnement annuleren](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)voor meer informatie over het annuleren van uw abonnement.

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Wat gebeurt er nadat ik mijn abonnement heb geannuleerd? voor betalen per gebruik-abonnementen?

Zie [Wat gebeurt er nadat ik mijn abonnement heb geannuleerd?](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription#what-happens-after-i-cancel-my-subscription) voor informatie over uw abonnement nadat dit is geannuleerd?

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Waarom wordt de Hive-versie weer gegeven als 1.2.1000 in plaats van 2,1 in de Ambari-gebruikers interface, zelfs als ik een HDInsight 3,6-cluster uitvoer?

Hoewel alleen 1,2 wordt weer gegeven in de Ambari-gebruikers interface, bevat HDInsight 3,6 zowel Hive 1,2 als hive 2,1.

## <a name="other-faq"></a>Overige veelgestelde vragen

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>Wat biedt HDInsight voor real-time verwerking van stroom mogelijkheden?

Voor informatie over de integratie mogelijkheden van stroom verwerking in azure HDInsight raadpleegt u [een stroom verwerkings technologie kiezen in azure](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Is er een manier om het hoofd knooppunt van het cluster dynamisch te beëindigen wanneer het cluster gedurende een bepaalde periode niet actief is?

Nee, u kunt het hoofd knooppunt van het cluster niet dynamisch beëindigen. U kunt Azure Data Factory voor dit scenario gebruiken.
