---
title: Regionaal herstel na noodgevallen voor Azure Databricks
description: In dit artikel wordt een benadering beschreven voor het doen van noodherstel in Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 2604d5b357feacce3493b4a4ded971144262611d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161933"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Regionaal herstel na noodgevallen voor Azure Databricks-clusters

In dit artikel wordt een noodherstelarchitectuur beschreven die nuttig is voor Azure Databricks-clusters en de stappen om dat ontwerp uit te voeren.

## <a name="azure-databricks-architecture"></a>Azure Databricks-architectuur

Wanneer u op hoog niveau een Azure Databricks-werkruimte maakt vanuit de Azure-portal, wordt een [beheerd toestel](../azure-resource-manager/managed-applications/overview.md) geïmplementeerd als azure-bron in uw abonnement, in de gekozen Azure-regio (bijvoorbeeld West-US). Dit toestel wordt geïmplementeerd in een [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) met een [netwerkbeveiligingsgroep](../virtual-network/manage-network-security-group.md) en een Azure Storage-account, beschikbaar in uw abonnement. Het virtuele netwerk biedt perimeterniveaubeveiliging aan de Databricks-werkruimte en wordt beveiligd via de netwerkbeveiligingsgroep. Binnen de werkruimte u Databricks-clusters maken door het type werknemer en stuurprogramma vm en de runtime-versie van Databricks op te geven. De aanhoudende gegevens zijn beschikbaar in uw opslagaccount, dat azure blob-opslag of Azure Data Lake Storage kan zijn. Zodra het cluster is gemaakt, u taken uitvoeren via notitieblokken, REST API's, ODBC/JDBC-eindpunten door ze aan een specifiek cluster te koppelen.

Het controlevlak Databricks beheert en bewaakt de werkruimteomgeving van Databricks. Elke beheerbewerking, zoals het maken van een cluster, wordt gestart vanuit het controlevlak. Alle metagegevens, zoals geplande taken, worden opgeslagen in een Azure-database met geo-replicatie voor fouttolerantie.

![Databricks-architectuur](media/howto-regional-disaster-recovery/databricks-architecture.png)

Een van de voordelen van deze architectuur is dat gebruikers Azure Databricks kunnen verbinden met elke opslagbron in hun account. Een belangrijk voordeel is dat zowel compute (Azure Databricks) als opslag onafhankelijk van elkaar kunnen worden geschaald.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Hoe maak je een regionale disaster recovery topologie

Zoals u in de vorige architectuurbeschrijving opmerkt, zijn er een aantal onderdelen die worden gebruikt voor een Big Data-pijplijn met Azure Databricks: Azure Storage, Azure Database en andere gegevensbronnen. Azure Databricks is de *compute* voor de Big Data-pijplijn. Het is *vluchtig* van aard, wat betekent dat terwijl uw gegevens nog steeds beschikbaar zijn in Azure Storage, de *compute* (Azure Databricks-cluster) kan worden beëindigd, zodat u niet hoeft te betalen voor compute wanneer u deze niet nodig hebt. De *compute* -gegevensbronnen (Azure Databricks) en opslagbronnen moeten zich in dezelfde regio bevinden, zodat taken geen hoge latentie ervaren.  

Volg de volgende vereisten om uw eigen regionale topologie voor noodherstel te maken:

   1. Inrichten van meerdere Azure Databricks-werkruimten in afzonderlijke Azure-regio's. Maak bijvoorbeeld de primaire Azure Databricks-werkruimte in East US2. Maak de secundaire Azure Databricks-werkruimte voor noodherstel in een afzonderlijke regio, zoals West-US.

   2. Gebruik [georedundante opslag](../storage/common/storage-redundancy.md). De gekoppelde Azure Databricks met gegevens worden standaard opgeslagen in Azure Storage. De resultaten van Databricks-taken worden ook opgeslagen in Azure Blob Storage, zodat de verwerkte gegevens duurzaam zijn en zeer beschikbaar blijven nadat het cluster is beëindigd. Aangezien het cluster Opslag en Gegevensstenen zich naast elkaar bevindt, moet u georedundante opslag gebruiken, zodat gegevens in het secundaire gebied kunnen worden geopend als het primaire gebied niet langer toegankelijk is.

   3. Zodra het secundaire gebied is gemaakt, moet u de gebruikers, gebruikersmappen, notitieblokken, clusterconfiguratie, takenconfiguratie, bibliotheken, opslag, init-scripts en toegangsbeheer opnieuw configureren. Aanvullende details worden beschreven in de volgende sectie.

## <a name="detailed-migration-steps"></a>Gedetailleerde migratiestappen

1. **De opdrachtregelinterface Databricks instellen op uw computer**

   In dit artikel worden een aantal codevoorbeelden weergegeven die de command-line-interface voor de meeste geautomatiseerde stappen gebruiken, omdat het een eenvoudig te gebruiken wrapper is via Azure Databricks REST API.

   Installeer de databricks-cli op uw desktopcomputer of een virtuele machine waar u het werk wilt doen voordat u een migratiestap uitvoert. Zie [Databricks CLI installeren voor](/azure/databricks/dev-tools/databricks-cli) meer informatie

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Python-scripts in dit artikel werken naar verwachting met Python 2.7+ < 3.x.

2. **Configureer twee profielen.**

   Configureer een voor de primaire werkruimte en een andere voor de secundaire werkruimte:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   De codeblokken in dit artikel schakelen tussen profielen in elke volgende stap met de bijbehorende werkruimteopdracht. Zorg ervoor dat de namen van de profielen die u maakt worden vervangen door elk codeblok.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   U indien nodig handmatig schakelen op de opdrachtregel:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Azure Active Directory-gebruikers migreren**

   Voeg handmatig dezelfde Azure Active Directory-gebruikers toe aan de secundaire werkruimte die in de primaire werkruimte bestaat.

4. **De gebruikersmappen en notitieblokken migreren**

   Gebruik de volgende python-code om de sandbox-gebruikersomgevingen te migreren, waaronder de geneste mapstructuur en notitieblokken per gebruiker.

   > [!NOTE]
   > Bibliotheken worden in deze stap niet gekopieerd, omdat de onderliggende API deze niet ondersteunt.

   Kopieer en sla het volgende python-script op in een bestand en voer het uit in de opdrachtregel Databricks. Bijvoorbeeld `python scriptname.py`.

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **De clusterconfiguraties migreren**

   Zodra notitieblokken zijn gemigreerd, u de clusterconfiguraties optioneel migreren naar de nieuwe werkruimte. Het is bijna een volledig geautomatiseerde stap met behulp van databricks-cli, tenzij u selectieve cluster config migratie te doen in plaats van voor iedereen.

   > [!NOTE]
   > Helaas is er geen cluster config eindpunt te maken, en dit script probeert om elk cluster te maken meteen. Als er niet genoeg cores beschikbaar zijn in uw abonnement, kan het maken van het cluster mislukken. De fout kan worden genegeerd, zolang de configuratie wordt overgedragen.

   In het volgende script wordt een toewijzing van oude naar nieuwe cluster-------iD's afgedrukt, die later kunnen worden gebruikt voor taakmigratie (voor taken die zijn geconfigureerd om bestaande clusters te gebruiken).

   Kopieer en sla het volgende python-script op in een bestand en voer het uit in de opdrachtregel Databricks. Bijvoorbeeld `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json, os

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE])
   clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids
   clusters_list = []
   ##for cluster_info in clusters_info_list: clusters_list.append(cluster_info.split(None, 1)[0])

   for cluster_info in clusters_info_list: 
      if cluster_info != '':
         clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf","node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","spark_env_vars","autotermination_minutes","enable_elastic_disk"]

   print(str(len(clusters_list)) + " clusters found in the primary site" )

   print ("---------------------------------------------------------")
   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {}
   i = 0
   for cluster in clusters_list:
      i += 1
      print("Checking cluster " + str(i) + "/" + str(len(clusters_list)) + " : " + cluster)
      cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
      print ("Got cluster config from old workspace")

       # Remove extra content from the config, as we need to build create request with allowed elements only
      cluster_req_json = json.loads(cluster_get_out)
      cluster_json_keys = cluster_req_json.keys()

      #Don't migrate Job clusters
      if cluster_req_json['cluster_source'] == u'JOB' : 
         print ("Skipping this cluster as it is a Job cluster : " + cluster_req_json['cluster_id'] )
         print ("---------------------------------------------------------")
         continue

      for key in cluster_json_keys:
         if key not in cluster_req_elems:
            cluster_req_json.pop(key, None)

      # Create the cluster, and store the mapping from old to new cluster ids

      #Create a temp file to store the current cluster info as JSON
      strCurrentClusterFile = "tmp_cluster_info.json" 

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

      fClusterJSONtmp = open(strCurrentClusterFile,"w+")
      fClusterJSONtmp.write(json.dumps(cluster_req_json))
      fClusterJSONtmp.close()

      #cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE])
      cluster_create_out = check_output(["databricks", "clusters", "create", "--json-file", strCurrentClusterFile , "--profile", IMPORT_PROFILE])
      cluster_create_out_json = json.loads(cluster_create_out)
      cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

      print ("Cluster create request sent to secondary site workspace successfully")
      print ("---------------------------------------------------------")

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) : 
         os.remove(strCurrentClusterFile)

   print ("Cluster mappings: " + json.dumps(cluster_old_new_mappings))
   print ("All done")
   print ("P.S. : Please note that all the new clusters in your secondary site are being started now!")
   print ("       If you won't use those new clusters at the moment, please don't forget terminating your new clusters to avoid charges")
   ```

6. **De configuratie van taken migreren**

   Als u in de vorige stap clusterconfiguraties hebt gemigreerd, u ervoor kiezen taakconfiguraties te migreren naar de nieuwe werkruimte. Het is een volledig geautomatiseerde stap met behulp van databricks-cli, tenzij u selectief werk config migratie te doen in plaats van het te doen voor alle banen.

   > [!NOTE]
   > De configuratie voor een geplande taak bevat ook de 'planningsinformatie', dus standaard werkt dat per geconfigureerde timing zodra deze is gemigreerd. Daarom verwijdert het volgende codeblok alle planningsgegevens tijdens de migratie (om dubbele uitvoeringen in oude en nieuwe werkruimten te voorkomen). Configureer de schema's voor dergelijke taken zodra u klaar bent voor cutover.

   De taakconfiguratie vereist instellingen voor een nieuw of bestaand cluster. Als u het bestaande cluster gebruikt, wordt in het onderstaande script/code geprobeerd de oude cluster-id te vervangen door een nieuwe cluster-id.

   Kopieer en sla het volgende python-script op in een bestand. Vervang de `old_cluster_id` waarde `new_cluster_id`voor en , met de uitvoer van clustermigratie in vorige stap. Voer het uit in uw databricks-cli-opdrachtregel, bijvoorbeeld . `python scriptname.py`

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **Bibliotheken migreren**

   Er is momenteel geen eenvoudige manier om bibliotheken van de ene werkruimte naar de andere te migreren. Installeer deze bibliotheken in plaats daarvan handmatig opnieuw in de nieuwe werkruimte. Het is mogelijk om te automatiseren met behulp van een combinatie van [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) om aangepaste bibliotheken te uploaden naar de werkruimte en [bibliotheken CLI.](https://github.com/databricks/databricks-cli#libraries-cli)

8. **Azure blob-opslag en Azure Data Lake Storage-bevestigingen migreren**

   Monteer alle [Azure Blob-opslag-](/azure/databricks/data/data-sources/azure/azure-storage) en [Azure Data Lake Storage-opslagpunten (Gen 2)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) handmatig met behulp van een op een laptop gebaseerde oplossing. De opslagbronnen zouden zijn gemonteerd in de primaire werkruimte en dat moet worden herhaald in de secundaire werkruimte. Er is geen externe API voor mounts.

9. **Clusterinit-scripts migreren**

   Alle clusterinitialisatiescripts kunnen worden gemigreerd van oude naar nieuwe werkruimte met behulp van de [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Kopieer eerst de benodigde scripts van `dbfs:/dat abricks/init/..` naar uw lokale bureaublad of virtuele machine. Kopieer deze scripts vervolgens naar de nieuwe werkruimte op hetzelfde pad.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Het toegangscontrolehandmatig opnieuw configureren en opnieuw toepassen.**

    Als uw bestaande primaire werkruimte is geconfigureerd om de Premium-laag (SKU) te gebruiken, gebruikt u waarschijnlijk ook de [functie Toegangsbeheer](/azure/databricks/administration-guide/access-control/index).

    Als u de functie Toegangsbeheer wel gebruikt, past u het toegangscontrolebeheer handmatig opnieuw toe op de bronnen (notitieblokken, clusters, taken, tabellen).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Herstel na noodgevallen voor uw Azure-ecosysteem

Als u andere Azure-services gebruikt, moet u ook best practices voor noodherstel voor die services implementeren. Als u bijvoorbeeld kiest voor een extern Hive-metastore-exemplaar, moet u disaster recovery overwegen voor [Azure SQL Server,](../sql-database/sql-database-disaster-recovery.md) [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)en/of [Azure Database voor MySQL.](../mysql/concepts-business-continuity.md) Zie Herstel na noodgevallen [voor Azure-toepassingen voor](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications)algemene informatie over herstel na noodgevallen.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Databricks-documentatie](index.yml)voor meer informatie.