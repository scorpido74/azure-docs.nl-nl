---
title: Regionale nood herstel voor Azure Databricks
description: In dit artikel wordt een aanpak beschreven voor herstel na nood gevallen in Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 599dc950cf286520e3f099966786d836ad05ba94
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84789324"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Regionale nood herstel voor Azure Databricks clusters

In dit artikel wordt een nood herstel architectuur beschreven die nuttig is voor Azure Databricks-clusters en de stappen om dat ontwerp uit te voeren.

## <a name="azure-databricks-architecture"></a>Azure Databricks architectuur

Wanneer u op hoog niveau een Azure Databricks-werk ruimte maakt op basis van de Azure Portal, wordt een [beheerd apparaat](../azure-resource-manager/managed-applications/overview.md) geïmplementeerd als een Azure-resource in uw abonnement, in de gekozen Azure-regio (bijvoorbeeld VS-West). Dit apparaat wordt geïmplementeerd in een [Azure-Virtual Network](../virtual-network/virtual-networks-overview.md) met een [netwerk beveiligings groep](../virtual-network/manage-network-security-group.md) en een Azure Storage account dat beschikbaar is in uw abonnement. Het virtuele netwerk biedt beveiliging op beveiligings niveau voor de Databricks-werk ruimte en wordt beveiligd via de netwerk beveiligings groep. In de werk ruimte kunt u Databricks-clusters maken door de werk nemer en het VM-type van het stuur programma en de Databricks-runtime versie op te geven. De persistente gegevens zijn beschikbaar in uw opslag account. Dit kan zowel Azure Blob Storage als Azure Data Lake Storage zijn. Zodra het cluster is gemaakt, kunt u taken uitvoeren via notebooks, REST Api's, ODBC/JDBC-eind punten door ze te koppelen aan een specifiek cluster.

Het Databricks-besturings vlak beheert en bewaakt de Databricks-werkruimte omgeving. Een beheer bewerking zoals het maken van een cluster wordt gestart vanuit het besturings vlak. Alle meta gegevens, zoals geplande taken, worden opgeslagen in een Azure-data base met geo-replicatie voor fout tolerantie.

![Databricks-architectuur](media/howto-regional-disaster-recovery/databricks-architecture.png)

Een van de voor delen van deze architectuur is dat gebruikers Azure Databricks kunnen verbinden met elke opslag bron in hun account. Een belang rijk voor deel is dat zowel Compute (Azure Databricks) en Storage onafhankelijk van elkaar kunnen worden geschaald.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Een regionale nood herstel topologie maken

Zoals u in de voor gaande architectuur beschrijving ziet, worden er een aantal onderdelen gebruikt voor een Big Data-pijp lijn met Azure Databricks: Azure Storage, Azure data base en andere gegevens bronnen. Azure Databricks is de *reken kracht* voor de pijp lijn Big data. Het is *tijdelijk* van aard, wat betekent dat wanneer uw gegevens nog steeds beschikbaar zijn in azure Storage, de *compute* (Azure Databricks-cluster) kan worden beëindigd, zodat u niet hoeft te betalen voor reken kracht wanneer u deze niet nodig hebt. De *berekenings* (Azure Databricks) en de opslag bronnen moeten zich in dezelfde regio bevinden, zodat de taken geen hoge latentie hebben.  

Als u uw eigen regionale nood herstel topologie wilt maken, volgt u deze vereisten:

   1. Richt meerdere Azure Databricks-werk ruimten in afzonderlijke Azure-regio's in. Maak bijvoorbeeld de primaire Azure Databricks-werk ruimte in Oost-VS2. Maak een tweede Azure Databricks werk ruimte voor herstel na nood gevallen in een afzonderlijke regio, zoals vs-West.

   2. [Geografisch redundante opslag](../storage/common/storage-redundancy.md)gebruiken. De gegevens die zijn gekoppeld aan Azure Databricks, worden standaard opgeslagen in Azure Storage. De resultaten van Databricks-taken worden ook opgeslagen in Azure Blob Storage, zodat de verwerkte gegevens duurzaam zijn en die Maxi maal beschikbaar blijven nadat het cluster is beëindigd. Omdat de opslag-en het Databricks-cluster zich op dezelfde locatie bevinden, moet u geo-redundante opslag gebruiken zodat gegevens kunnen worden geopend in een secundaire regio als de primaire regio niet meer toegankelijk is.

   3. Zodra de secundaire regio is gemaakt, moet u de gebruikers, gebruikers mappen, notebooks, cluster configuratie, taken configuratie, Bibliotheken, opslag, init-scripts en toegangs beheer opnieuw configureren. Meer informatie vindt u in de volgende sectie.

## <a name="detailed-migration-steps"></a>Gedetailleerde migratiestappen

1. **De Databricks-opdracht regel interface op uw computer instellen**

   Dit artikel bevat een aantal code voorbeelden die gebruikmaken van de opdracht regel interface voor de meeste automatische stappen, omdat het een gebruiks vriendelijke wrapper is dan Azure Databricks REST API.

   Voordat u een migratie stap uitvoert, installeert u de databricks-CLI op uw desktop computer of een virtuele machine waarop u het werk wilt uitvoeren. Zie [install DATABRICKS cli](/azure/databricks/dev-tools/databricks-cli) (Engelstalig) voor meer informatie.

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Alle python-scripts die in dit artikel worden vermeld, worden verwacht te werken met python 2.7 + < 3. x.

2. **Configureer twee profielen.**

   Configureer een voor de primaire werk ruimte en een andere voor de secundaire werk ruimte:

   ```bash
   databricks configure --profile primary --token
   databricks configure --profile secondary --token
   ```

   De code blokken in dit artikel scha kelen tussen de profielen in elke volgende stap met behulp van de bijbehorende werk ruimte opdracht. Zorg ervoor dat de namen van de profielen die u maakt, worden vervangen door elk code blok.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   U kunt indien nodig hand matig overschakelen op de opdracht regel:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Azure Active Directory gebruikers migreren**

   Voeg dezelfde Azure Active Directory gebruikers hand matig toe aan de secundaire werk ruimte die voor komt in de primaire werk ruimte.

4. **De gebruikers mappen en-notitie blokken migreren**

   Gebruik de volgende python-code voor het migreren van de sandbox-gebruikers omgevingen, waaronder de geneste mappen structuur en notitie blokken per gebruiker.

   > [!NOTE]
   > Bibliotheken worden niet in deze stap gekopieerd, omdat de onderliggende API'S deze niet ondersteunt.

   Kopieer en sla het volgende python-script op in een bestand en voer het uit in de Databricks-opdracht regel. Bijvoorbeeld `python scriptname.py`.

   ```python
   import sys
   import os
   import subprocess
   from subprocess import call, check_output
   
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   
   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = (user_list_out.decode(encoding="utf-8")).splitlines()
   
   print (user_list)
   
   # Export sandboxed environment(folders, notebooks) for each user and import into new workspace.
   #Libraries are not included with these APIs / commands.
   
   for user in user_list:
     #print("Trying to migrate workspace for user ".decode() + user)
     print (("Trying to migrate workspace for user ") + user)
   
     subprocess.call(str("mkdir -p ") + str(user), shell = True)
     export_exit_status = call("databricks workspace export_dir /Users/" + str(user) + " ./" + str(user) + " --profile " + EXPORT_PROFILE, shell = True)
   
     if export_exit_status==0:
       print ("Export Success")
       import_exit_status = call("databricks workspace import_dir ./" + str(user) + " /Users/" + str(user) + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print ("Import Success")
       else:
         print ("Import Failure")
     else:
       print ("Export Failure")
   print ("All done")
   ```

5. **De cluster configuraties migreren**

   Wanneer notebooks zijn gemigreerd, kunt u eventueel de cluster configuraties migreren naar de nieuwe werk ruimte. Het is bijna een volledig automatische stap met behulp van databricks-CLI, tenzij u selectieve cluster configuratie wilt migreren in plaats van alle.

   > [!NOTE]
   > Er is geen cluster configuratie-eind punt gemaakt en dit script probeert elk cluster direct te maken. Als er onvoldoende kern geheugens beschikbaar zijn in uw abonnement, kan het maken van het cluster mislukken. De fout kan worden genegeerd, zolang de configuratie is overgedragen.

   Met het volgende script dat is opgegeven, wordt een toewijzing van oud naar nieuwe cluster-Id's afgedrukt, die later kunnen worden gebruikt voor taak migratie (voor taken die zijn geconfigureerd voor het gebruik van bestaande clusters).

   Kopieer en sla het volgende python-script op in een bestand en voer het uit in de Databricks-opdracht regel. Bijvoorbeeld `python scriptname.py`.

   ```python
   import sys
   import os
   import subprocess
   import json
   from subprocess import call, check_output
   
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   
   # Get all clusters info from old workspace
   clusters_out = check_output(["databricks", "clusters", "list",    "--profile", EXPORT_PROFILE])
   clusters_info_list = str(clusters_out.decode(encoding="utf-8")).   splitlines()
   print("Printting Cluster info List")
   print(clusters_info_list)
   
   # Create a list of all cluster ids
   clusters_list = []
   ##for cluster_info in clusters_info_list: clusters_list.append   (cluster_info.split(None, 1)[0])
   
   for cluster_info in clusters_info_list:
      if cluster_info != '':
         clusters_list.append(cluster_info.split(None, 1)[0])
   
   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf","node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","spark_env_vars","autotermination_minutes","enable_elastic_disk"]
   print("Printing Cluster element List")
   print (cluster_req_elems)
   print(str(len(clusters_list)) + " clusters found in the primary site" )
   
   print ("---------------------------------------------------------")
   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {}
   i = 0
   for cluster in clusters_list:
      i += 1
      print("Checking cluster " + str(i) + "/" + str(len(clusters_list)) + " : " +str(cluster))
      cluster_get_out_f = check_output(["databricks", "clusters", "get", "--cluster-id", str(cluster), "--profile", EXPORT_PROFILE])
      cluster_get_out=str(cluster_get_out_f.decode(encoding="utf-8"))
      print ("Got cluster config from old workspace")
      print (cluster_get_out)
       # Remove extra content from the config, as we need to build create request with allowed elements only
      cluster_req_json = json.loads(cluster_get_out)
      cluster_json_keys = cluster_req_json.keys()

      #Don't migrate Job clusters
      if cluster_req_json['cluster_source'] == u'JOB' :
         print ("Skipping this cluster as it is a Job cluster : " + cluster_req_json['cluster_id'] )
         print ("---------------------------------------------------------")
         continue

         #cluster_req_json.pop(key, None)
         for key in cluster_json_keys:
           if key not in cluster_req_elems:
            print (cluster_req_json)
            #cluster_del_item=cluster_json_keys .keys()
            cluster_req_json.popitem(key, None)
  
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
   
6. **De taak configuratie migreren**

   Als u cluster configuraties in de vorige stap hebt gemigreerd, kunt u ervoor kiezen om de taak configuraties naar de nieuwe werk ruimte te migreren. Het is een volledig geautomatiseerde stap met databricks-CLI, tenzij u een selectieve taak configuratie wilt migreren in plaats van deze te gebruiken voor alle taken.

   > [!NOTE]
   > De configuratie voor een geplande taak bevat ook de plannings gegevens, zodat deze standaard worden gestart volgens de geconfigureerde timing zodra deze wordt gemigreerd. Het volgende code blok verwijdert daarom alle plannings gegevens tijdens de migratie (om te voor komen dat dubbele uitvoeringen worden uitgevoerd op oude en nieuwe werk ruimten). Configureer de planningen voor dergelijke taken zodra u klaar bent voor cutover.

   De taak configuratie vereist instellingen voor een nieuw of een bestaand cluster. Als u een bestaand cluster gebruikt, probeert het script/code hieronder de oude cluster-ID te vervangen door de nieuwe cluster-ID.

   Kopieer het volgende python-script en sla het op in een bestand. Vervang de waarde voor `old_cluster_id` en door `new_cluster_id` de uitvoer van de cluster migratie die in de vorige stap is uitgevoerd. Voer deze uit in de databricks-cli-opdracht regel, bijvoorbeeld `python scriptname.py` .

   ```python
   import sys
   import os
   import subprocess
   import json
   from subprocess import call, check_output
   
   
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   
   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"0227-120427-tryst214": "0229-032632-paper88"}
   
   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print("No jobs to migrate")
     sys.exit(0)
   
   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])
   
   # Optionally filter job ids out manually, so as to create only required ones in new workspace
   
   # Create each job in the new workspace based on corresponding settings in the old workspace
   
   for job in jobs_list:
     print("Trying to migrate ") + job
   
     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print("Got job config from old workspace")
   
     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']
   
     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)
   
     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print("Mapping not available for old cluster id ") + job_req_settings_json['existing_cluster_id']
         continue
   
     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print("Sent job create request to new workspace successfully")
   
   print("All done")
   ```

7. **Bibliotheken migreren**

   Er is momenteel geen eenvoudige manier om bibliotheken te migreren van de ene werk ruimte naar een andere. In plaats daarvan installeert u deze bibliotheken hand matig in de nieuwe werk ruimte. Het is mogelijk om een combi natie van [DBFS cli](https://github.com/databricks/databricks-cli#dbfs-cli-examples) te automatiseren voor het uploaden van aangepaste bibliotheken naar de werk ruimte en [bibliotheken cli](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Azure Blob-opslag en Azure Data Lake Storage-koppelingen migreren**

   Koppel alle [Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage) -en [Azure data Lake Storage (Gen 2)-](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) koppel punten hand matig opnieuw met een oplossing op basis van een notitie blok. De opslag resources zouden zijn gekoppeld in de primaire werk ruimte en moeten worden herhaald in de secundaire werk ruimte. Er is geen externe API voor koppels.

9. **Scripts voor het initialiseren van het cluster migreren**

   U kunt de scripts voor het initialiseren van clusters migreren van een oude naar een nieuwe werk ruimte met behulp van de [DBFS cli](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Kopieer eerst de benodigde scripts van `dbfs:/dat abricks/init/..` naar uw lokale bureau blad of virtuele machine. Kopieer vervolgens deze scripts naar de nieuwe werk ruimte op hetzelfde pad.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Toegangs beheer hand matig opnieuw configureren en Toep assen.**

    Als uw bestaande primaire werk ruimte is geconfigureerd voor het gebruik van de Premium-laag (SKU), is het waarschijnlijk dat u ook de [Access Control functie](/azure/databricks/administration-guide/access-control/index)gebruikt.

    Als u de functie Access Control wilt gebruiken, moet u het toegangs beheer hand matig opnieuw Toep assen op de resources (notebooks, clusters, Jobs, tabellen).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Herstel na nood geval voor uw Azure-ecosysteem

Als u andere Azure-Services gebruikt, moet u er ook voor zorgen dat u aanbevolen procedures voor herstel na nood gevallen voor die Services implementeert. Als u bijvoorbeeld een exemplaar van een externe Hive-metastore wilt gebruiken, moet u rekening houden met nood herstel voor [Azure SQL database](../azure-sql/database/disaster-recovery-guidance.md), [Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)en/of [Azure database for MySQL](../mysql/concepts-business-continuity.md). Zie [herstel na nood gevallen voor Azure-toepassingen](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications)voor algemene informatie over herstel na nood gevallen.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Databricks-documentatie](index.yml)voor meer informatie.
