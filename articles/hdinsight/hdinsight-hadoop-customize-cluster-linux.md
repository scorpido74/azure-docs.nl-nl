---
title: Azure HDInsight-clusters aanpassen met behulp van scriptacties
description: Voeg aangepaste componenten toe aan HDInsight-clusters met behulp van scriptacties. Scriptacties zijn Bash-scripts die kunnen worden gebruikt om de clusterconfiguratie aan te passen of extra services en hulpprogramma's toe te voegen, zoals Hue, Solr of R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 12e6892930afe8ba9c7bad9b05fd39eeaf8835fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272498"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Azure HDInsight-clusters aanpassen met behulp van scriptacties

Azure HDInsight biedt een configuratiemethode genaamd **scriptacties** die aangepaste scripts aanroepen om het cluster aan te passen. Deze scripts worden gebruikt om extra componenten te installeren en configuratie-instellingen te wijzigen. Scriptacties kunnen worden gebruikt tijdens of na het maken van het cluster.

Scriptacties kunnen ook worden gepubliceerd naar de Azure Marketplace als een HDInsight-toepassing. Zie [Een HDInsight-toepassing publiceren in de Azure Marketplace](hdinsight-apps-publish-applications.md)voor meer informatie over HDInsight-toepassingen.

## <a name="permissions"></a>Machtigingen

Voor een HDInsight-cluster met domeinzijn er twee Apache Ambari-machtigingen vereist wanneer u scriptacties met het cluster gebruikt:

* **AMBARI, ik weet het niet. \_AANGEPASTE\_OPDRACHT UITVOEREN**. De functie Ambari Administrator heeft deze machtiging standaard.
* **CLUSTER. \_AANGEPASTE\_OPDRACHT UITVOEREN**. Zowel de HDInsight Cluster Administrator als ambari administrator hebben deze standaard toestemming.

Zie [HDInsight-clusters](./domain-joined/apache-domain-joined-manage.md)beheren met Enterprise Security Package voor meer informatie over het werken met machtigingen met hdInsight met domeinlid.

## <a name="access-control"></a>Toegangsbeheer

Als u niet de beheerder of eigenaar van uw Azure-abonnement bent, moet uw account ten minste inzendertoegang hebben tot de brongroep die het HDInsight-cluster bevat.

Als u een HDInsight-cluster maakt, moet iemand met ten minste inzendertoegang tot het Azure-abonnement de provider eerder hebben geregistreerd voor HDInsight. Registratie van een provider vindt plaats wanneer een gebruiker met toegang tot het abonnement op het niveau van Inzender, voor het eerst een resource maakt onder het abonnement. Het kan ook worden gedaan zonder het creëren van een bron als u [een provider registreert met behulp van REST](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Meer informatie over het werken met toegangsbeheer:

* [Aan de slag met toegangsbeheer in de Azure-portal](../role-based-access-control/overview.md)
* [Roltoewijzingen gebruiken voor het beheer van de toegang tot de resources van uw Azure-abonnement](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Scriptacties begrijpen

Een scriptactie is Bash-script dat wordt uitgevoerd op de knooppunten in een HDInsight-cluster. Kenmerken en kenmerken van scriptacties zijn als volgt:

* Moet worden opgeslagen op een URI die toegankelijk is vanaf het HDInsight-cluster. De volgende zijn mogelijke opslaglocaties:

    * Voor reguliere clusters:

      * ADLS Gen1: De serviceprincipal die HDInsight gebruikt om toegang te krijgen tot Data Lake Storage moet leestoegang tot het script hebben. De URI-indeling voor scripts die zijn `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`opgeslagen in Data Lake Storage Gen1 is .

      * Een blob in een Azure Storage-account dat het primaire of aanvullende opslagaccount is voor het HDInsight-cluster. HDInsight krijgt toegang tot beide typen opslagaccounts tijdens het maken van clusteraccounts.

        > [!IMPORTANT]  
        > Draai de opslagsleutel niet op dit Azure Storage-account, omdat hierdoor volgende scriptacties met scripts die daar zijn opgeslagen, mislukken.

      * Een openbare service voor het delen van bestanden die toegankelijk is via http:// paden. Voorbeelden zijn Azure Blob, GitHub, OneDrive. Zie Bijvoorbeeld URI's: [Voorbeeld scriptactiescripts](#example-script-action-scripts).

     * Voor clusters met ESP worden de wasb:// of wasbs:// of http[s]:// URI's ondersteund.

* Kan worden beperkt tot het uitvoeren van alleen bepaalde knooppunttypen. Voorbeelden zijn hoofdknooppunten of werkknooppunten.

* Kan worden volgehouden of ad hoc.

    Aanhoudende scriptacties moeten een unieke naam hebben. Volblijvende scripts worden gebruikt om nieuwe werknemersknooppunten aan te passen die aan het cluster zijn toegevoegd door middel van schaalbewerkingen. Een blijvend script kan ook wijzigingen toepassen op een ander knooppunttype wanneer er schaalbewerkingen plaatsvinden. Een voorbeeld is een hoofdknooppunt.

    Ad hoc scripts worden niet volgehouden. Scriptacties die worden gebruikt tijdens het maken van het cluster, worden automatisch gehandhaafd. Ze worden niet toegepast op werkknooppunten die aan het cluster zijn toegevoegd nadat het script is uitgevoerd. Vervolgens u een ad hoc-script promoten in een langdurend script of een blijvend script degraderen tot een ad hoc-script. Scripts die mislukken, worden niet volgehouden, zelfs als u specifiek aangeeft dat ze zouden moeten zijn.

* Kan parameters accepteren die door het script worden gebruikt tijdens de uitvoering.

* Uitvoeren met bevoegdheden op rootniveau op de clusterknooppunten.

* Kan worden gebruikt via de Azure-portal, Azure PowerShell, Azure CLI of HDInsight .NET SDK.

Het cluster houdt een geschiedenis bij van alle scripts die zijn uitgevoerd. De geschiedenis helpt wanneer u de ID van een script voor promotie- of degradatiebewerkingen moet vinden.

> [!IMPORTANT]  
> Er is geen automatische manier om de wijzigingen van een scriptactie ongedaan te maken. Draai de wijzigingen handmatig om of geef een script op dat ze omkeert.

### <a name="script-action-in-the-cluster-creation-process"></a>Scriptactie in het clustercreatieproces

Scriptacties die worden gebruikt tijdens het maken van het cluster, verschillen enigszins van scriptacties die op een bestaand cluster worden uitgevoerd:

* Het script wordt automatisch gehandhaafd.

* Een fout in het script kan ertoe leiden dat het proces voor het maken van het clusterproces mislukt.

In het volgende diagram wordt uitgelegd wanneer scriptactie wordt uitgevoerd tijdens het creatieproces:

![HDInsight-clusteraanpassing en -fasen tijdens het maken van clusteren][img-hdi-cluster-states]

Het script wordt uitgevoerd terwijl HDInsight wordt geconfigureerd. Het script loopt parallel op alle opgegeven knooppunten in het cluster. Het wordt uitgevoerd met rootrechten op de knooppunten.

U bewerkingen uitvoeren zoals het stoppen en starten van services, waaronder Apache Hadoop-gerelateerde services. Als u services stopt, moet u ervoor zorgen dat de Ambari-service en andere Hadoop-gerelateerde services worden uitgevoerd voordat het script is voltooid. Deze services zijn vereist om de status en status van het cluster te bepalen terwijl het wordt gemaakt.

Tijdens het maken van het cluster u veel scriptacties tegelijk gebruiken. Deze scripts worden aangeroepen in de volgorde waarin ze zijn opgegeven.

> [!IMPORTANT]  
> Scriptacties moeten binnen 60 minuten zijn voltooid of een time-out hebben. Tijdens het inrichten van het cluster wordt het script gelijktijdig uitgevoerd met andere installatie- en configuratieprocessen. Concurrentie om resources zoals CPU-tijd of netwerkbandbreedte kan ertoe leiden dat het script langer duurt dan in uw ontwikkelomgeving.
>
> Om de tijd die nodig is om het script uit te voeren te minimaliseren, vermijdt u taken zoals het downloaden en compileren van toepassingen van de bron. Precompileer toepassingen en sla de binaire op in Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Scriptactie op een lopend cluster

Door een fout in een script dat wordt uitgevoerd op een al actief cluster, wordt het cluster niet automatisch gewijzigd in een mislukte status. Nadat een script is voltooid, moet het cluster terugkeren naar een hardloopstatus. Zelfs als het cluster een lopende status heeft, kan het mislukte script dingen hebben verbroken. Een script kan bijvoorbeeld bestanden verwijderen die nodig zijn voor het cluster.

Scriptsacties worden uitgevoerd met hoofdbevoegdheden. Zorg ervoor dat u begrijpt wat een script doet voordat u het toepast op uw cluster.

Wanneer u een script toepast op een cluster, verandert de clusterstatus van **Uitvoeren** naar **Geaccepteerd**. Dan verandert het in **HDInsight configuratie** en, ten slotte, terug naar **Running** voor succesvolle scripts. De scriptstatus is vastgelegd in de scriptactiegeschiedenis. Deze informatie geeft aan of het script is geslaagd of mislukt. De `Get-AzHDInsightScriptActionHistory` PowerShell-cmdlet toont bijvoorbeeld de status van een script. Het retourneert informatie die vergelijkbaar is met de volgende tekst:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Als u de clustergebruiker, beheerder, wachtwoord nadat het cluster is gemaakt, wijzigt, kunnen scriptacties die tegen dit cluster worden uitgevoerd, mislukken. Als u nog steeds scriptacties hebt die de knooppunten van de werknemer targeten, mislukken deze scripts mogelijk wanneer u het cluster schaalt.

## <a name="example-script-action-scripts"></a>Voorbeeldscriptactiescripts

Scriptactiescripts kunnen worden gebruikt via de volgende hulpprogramma's:

* Azure Portal
* Azure PowerShell
* Azure-CLI
* HDInsight .NET SDK

HDInsight biedt scripts om de volgende componenten op HDInsight-clusters te installeren:

| Name | Script |
| --- | --- |
| Een Azure Storage-account toevoegen |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Zie [Extra opslagaccounts toevoegen aan HDInsight](hdinsight-hadoop-add-storage.md). |
| Hue installeren |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Zie [Hue installeren en gebruiken op HDInsight Hadoop-clusters.](hdinsight-hadoop-hue-linux.md) |
| Hive-bibliotheken vooraf laden |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Zie [Aangepaste Apache Hive-bibliotheken toevoegen bij het maken van uw HDInsight-cluster](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Scriptactie tijdens het maken van clusteren

In deze sectie worden de verschillende manieren uitgelegd waarop u scriptacties gebruiken wanneer u een HDInsight-cluster maakt.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Een scriptactie gebruiken tijdens het maken van een cluster vanuit de Azure-portal

1. Begin met het maken van een cluster zoals beschreven in [Clusters op basis van Linux maken in HDInsight met behulp van de Azure-portal.](hdinsight-hadoop-create-linux-clusters-portal.md) Selecteer **op** het tabblad Configuratie + prijzen de optie **+ Scriptactie toevoegen**.

    ![Scriptactie azure-portalcluster](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Gebruik de __vermelding Een scriptitem selecteren__ om een vooraf gemaakt script te selecteren. Als u een aangepast script wilt gebruiken, selecteert u __Aangepast__. Geef vervolgens het __Name and__ __Bash-script URI__ op voor uw script.

    ![Een script toevoegen in het geselecteerde scriptformulier](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    In de volgende tabel worden de elementen op het formulier beschreven:

    | Eigenschap | Waarde |
    | --- | --- |
    | Een script selecteren | Als u uw eigen script wilt gebruiken, selecteert u __Aangepast__. Selecteer anders een van de meegeleverde scripts. |
    | Name |Geef een naam op voor de scriptactie. |
    | Bash script URI |Geef de URI van het script op. |
    | Hoofd/Arbeider/ZooKeeper |Geef de knooppunten op waarop het script wordt uitgevoerd: **Hoofd,** **Werknemer**of **ZooKeeper**. |
    | Parameters |Geef de parameters op, indien vereist door het script. |

    Gebruik de __actievermelding Voorduren__ van dit script om ervoor te zorgen dat het script wordt toegepast tijdens schaalbewerkingen.

1. Selecteer __Maken__ om het script op te slaan. Vervolgens u __+ Nieuw verzenden__ gebruiken om een ander script toe te voegen.

    ![HDInsight meerdere scriptacties](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Wanneer u klaar bent met het toevoegen van scripts, keert u terug naar het tabblad **Configuratie + prijzen.**

1. Voer de resterende stappen voor het maken van clusters zoals gewoonlijk uit.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Een scriptactie gebruiken vanuit Azure Resource Manager-sjablonen

Scriptacties kunnen worden gebruikt met Azure Resource Manager-sjablonen. Zie bijvoorbeeld [HDInsight Linux Cluster maken en voer een scriptactie uit.](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/)

In dit voorbeeld wordt de scriptactie toegevoegd met behulp van de volgende code:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Meer informatie over het implementeren van een sjabloon:

* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Resources implementeren met Resource Manager-sjablonen en de Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Een scriptactie gebruiken tijdens het maken van een cluster vanuit Azure PowerShell

In deze sectie gebruikt u de cmdlet [Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) om scripts aan te roepen om een cluster aan te passen. Controleer voordat u begint of u Azure PowerShell installeert en configureert. Om deze PowerShell-opdrachten te gebruiken, hebt u de [AZ Module](https://docs.microsoft.com/powershell/azure/overview)nodig.

In het volgende script ziet u hoe u een scriptactie toepast wanneer u een cluster maakt met PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Het kan enkele minuten duren voordat het cluster wordt gemaakt.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Een scriptactie gebruiken tijdens het maken van clusteren vanuit de HDInsight .NET SDK

De HDInsight .NET SDK biedt clientbibliotheken die het eenvoudiger maken om met HDInsight te werken vanuit een .NET-toepassing. Zie [Scriptacties](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions)voor een codevoorbeeld .

## <a name="script-action-to-a-running-cluster"></a>Scriptactie naar een lopend cluster

In deze sectie wordt uitgelegd hoe u scriptacties toepast op een lopend cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Een scriptactie toepassen op een lopend cluster vanuit de Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en zoek uw cluster.

1. Selecteer **scriptacties**in de standaardweergave onder **Instellingen**.

1. Selecteer boven aan de pagina **Scriptacties** de optie **+ Nieuw verzenden**.

    ![Een script toevoegen aan een lopend cluster](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Gebruik de __vermelding Een scriptitem selecteren__ om een vooraf gemaakt script te selecteren. Als u een aangepast script wilt gebruiken, selecteert u __Aangepast__. Geef vervolgens het __Name and__ __Bash-script URI__ op voor uw script.

    ![Een script toevoegen in het geselecteerde scriptformulier](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    In de volgende tabel worden de elementen op het formulier beschreven:

    | Eigenschap | Waarde |
    | --- | --- |
    | Een script selecteren | Als u uw eigen script wilt gebruiken, selecteert u __aangepast__. Selecteer anders een opgegeven script. |
    | Name |Geef een naam op voor de scriptactie. |
    | Bash script URI |Geef de URI van het script op. |
    | Hoofd/Arbeider/Dierenverzorger |Geef de knooppunten op waarop het script wordt uitgevoerd: **Hoofd,** **Werknemer**of **ZooKeeper**. |
    | Parameters |Geef de parameters op, indien vereist door het script. |

    Gebruik de __actievermelding Voorduren__ van dit script om ervoor te zorgen dat het script wordt toegepast tijdens schaalbewerkingen.

1. Selecteer ten slotte de knop **Maken** om het script op het cluster toe te passen.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Een scriptactie toepassen op een lopend cluster vanuit Azure PowerShell

Om deze PowerShell-opdrachten te gebruiken, hebt u de [AZ Module](https://docs.microsoft.com/powershell/azure/overview)nodig. In het volgende voorbeeld ziet u hoe u een scriptactie toepast op een lopend cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Nadat de bewerking is voltooid, ontvangt u informatie die vergelijkbaar is met de volgende tekst:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Een scriptactie toepassen op een lopend cluster vanuit azure cli

Controleer voordat u begint of u de Azure CLI installeert en configureert. Zorg ervoor dat u de nieuwste versie hebt. Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)voor meer informatie.

1. Authenticeer naar uw Azure-abonnement:

    ```azurecli
    az login
    ```

1. Een scriptactie toepassen op een lopend cluster:

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Geldige rollen `headnode` `workernode`zijn `zookeepernode` `edgenode`, , , . Als het script op meerdere knooppunttypen moet worden toegepast, scheidt u de rollen door een spatie. Bijvoorbeeld `--roles headnode workernode`.

    Als u het `--persist-on-success`script wilt blijven uitvoeren, voegt u . U het script later `az hdinsight script-action promote`ook blijven uitvoeren met behulp van.

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Een scriptactie toepassen op een lopend cluster met rest-API

Zie [Cluster REST API in Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Een scriptactie toepassen op een lopend cluster van de HDInsight .NET SDK

Zie [Een scriptactie toepassen tegen een op Linux gebaseerd HDInsight-cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)voor een voorbeeld van het gebruik van de .NET SDK om scripts op een cluster toe te passen.

## <a name="view-history-and-promote-and-demote-script-actions"></a>Geschiedenis bekijken en scriptacties promoten en degraderen

### <a name="the-azure-portal"></a>Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en zoek uw cluster.

1. Selecteer **scriptacties**in de standaardweergave onder **Instellingen**.

1. Een geschiedenis van scripts voor dit cluster wordt weergegeven in de sectie scriptacties. Deze informatie bevat een lijst met volgehouden scripts. De volgende screenshot laat zien dat het Solr-script op dit cluster is uitgevoerd. De screenshot toont geen aanhoudende scripts.

    ![Portalscriptacties dienen geschiedenis in](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Selecteer een script uit de geschiedenis om de sectie **Eigenschappen** voor dit script weer te geven. Vanaf de bovenkant van het scherm u het script opnieuw uitvoeren of promoten.

    ![Eigenschappen van scriptacties promoten](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. U ook de ellips, **...**, rechts van de items op het script acties sectie om acties uit te voeren.

    ![Persisted script acties verwijderen](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Functie |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Informatie ophalen over aanhoudende scriptacties. Deze cmdlet maakt de acties van een script niet ongedaan, het verwijdert alleen de aanhoudende vlag.|
| `Get-AzHDInsightScriptActionHistory` |Een geschiedenis ophalen van scriptacties die zijn toegepast op het cluster of details voor een specifiek script. |
| `Set-AzHDInsightPersistedScriptAction` |Een ad-hocscriptactie promoten in een aanhoudende scriptactie. |
| `Remove-AzHDInsightPersistedScriptAction` |Degradeer een aanhoudende scriptactie op een ad-hocactie. |

In het volgende voorbeeldscript wordt aangetoond dat de cmdlets worden gebruikt om een script te promoten en vervolgens te degraderen.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure-CLI

| Opdracht | Beschrijving |
| --- | --- |
| [az hdinsight script-actie verwijderen](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Hiermee verwijdert u een opgegeven aanhoudende scriptactie van het cluster. Met deze opdracht worden de acties van een script niet ongedaan gemaakt, alleen wordt de aanhoudende vlag verwijderd.|
|[az hdinsight script-actie uit te voeren](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Scriptacties uitvoeren op het opgegeven HDInsight-cluster.|
| [az hdinsight script-action lijst](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |Hiermee worden alle aanhoudende scriptacties voor het opgegeven cluster weergegeven. |
|[az hdinsight script-action list-execution-history](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Hiermee worden de uitvoeringsgeschiedenis van alle scripts voor het opgegeven cluster weergegeven.|
|[az hdinsight script-actie te bevorderen](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|Bevordert de opgegeven ad-hocscriptuitvoering in een blijvend script.|
|[az hdinsight script-action show-execution-details](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Hier wordt de detail van de uitvoering van het script voor de opgegeven scriptuitvoerings-ID weergegeven.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

Zie [Een scriptactie toepassen tegen een op Linux gebaseerd HDInsight-cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)voor een voorbeeld van het gebruik van de .NET SDK om scriptgeschiedenis uit een cluster op te halen, scripts te promoten of te degraderen.

> [!NOTE]  
> In dit voorbeeld wordt ook uitgelegd hoe u een HDInsight-toepassing installeert met behulp van de .NET SDK.

## <a name="support-for-open-source-software"></a>Ondersteuning voor open-source software

De Microsoft Azure HDInsight-service maakt gebruik van een ecosysteem van open-source technologieën die zijn gevormd rond Apache Hadoop. Microsoft Azure biedt een algemeen ondersteuningsniveau voor open-sourcetechnologieën. Zie het gedeelte **Ondersteuningsbereik** van [veelgestelde vragen](https://azure.microsoft.com/support/faq/)over het bereik van Azure Support voor meer informatie. De HDInsight-service biedt een extra ondersteuningsniveau voor ingebouwde componenten.

Er zijn twee soorten opensourcecomponenten beschikbaar in de HDInsight-service:

* **Ingebouwde componenten**. Deze componenten zijn vooraf geïnstalleerd op HDInsight-clusters en bieden kernfunctionaliteit van het cluster. De volgende onderdelen behoren tot deze categorie:

  * [Apache Hadoop GAREN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager.
  * De Hive-querytaal [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
  * [Apache Mahout.](https://mahout.apache.org/)

    Een volledige lijst van clustercomponenten is beschikbaar in [Wat zijn de Apache Hadoop componenten en versies beschikbaar met HDInsight?](hdinsight-component-versioning.md)

* **Aangepaste componenten**. Als gebruiker van het cluster u elk onderdeel dat beschikbaar is in de community of door u is gemaakt, in uw werkbelasting installeren of gebruiken.

> [!WARNING]  
> Onderdelen die bij het HDInsight-cluster worden geleverd, worden volledig ondersteund. Microsoft Support helpt bij het isoleren en oplossen van problemen met betrekking tot deze onderdelen.
>
> Aangepaste componenten ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Microsoft Support kan het probleem mogelijk oplossen. Of ze kunnen u vragen om beschikbare kanalen in te schakelen voor de open-source technologieën waar diepgaande expertise voor die technologie wordt gevonden. Veel community sites kunnen worden gebruikt. Voorbeelden zijn [MSDN forum voor HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) en [Stack Overflow](https://stackoverflow.com).
>
> Apache projecten hebben ook project sites op de [Apache website](https://apache.org). Een voorbeeld is [Hadoop.](https://hadoop.apache.org/)

De HDInsight-service biedt verschillende manieren om aangepaste componenten te gebruiken. Hetzelfde ondersteuningsniveau is van toepassing, ongeacht hoe een component wordt gebruikt of geïnstalleerd op het cluster. In de volgende lijst worden de meest voorkomende manieren beschreven waarop aangepaste componenten worden gebruikt op HDInsight-clusters:

1. **Vacature .** Hadoop of andere typen taken die aangepaste componenten uitvoeren of gebruiken, kunnen in het cluster worden ingediend.

2. **Clusteraanpassing**. Tijdens het maken van het cluster u aanvullende instellingen en aangepaste componenten opgeven die op de clusterknooppunten zijn geïnstalleerd.

3. **Monsters**. Voor populaire aangepaste componenten kunnen Microsoft en anderen voorbeelden geven van hoe deze componenten kunnen worden gebruikt op HDInsight-clusters. Deze monsters worden geleverd zonder ondersteuning.

## <a name="troubleshooting"></a>Problemen oplossen

U de gebruikersinterface van het Ambari-web gebruiken om informatie te bekijken die is vastgelegd door scriptacties. Als het script mislukt tijdens het maken van het cluster, zijn de logboeken ook beschikbaar in het standaardopslagaccount dat aan het cluster is gekoppeld. In deze sectie vindt u informatie over het ophalen van de logboeken met behulp van beide opties.

### <a name="the-apache-ambari-web-ui"></a>De Apache Ambari web UI

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net`naar `CLUSTERNAME` , waar is de naam van uw cluster.

1. Selecteer boven aan de pagina het **ops-item.** In een lijst worden huidige en eerdere bewerkingen weergegeven die via Ambari op het cluster zijn uitgevoerd.

    ![Ambari web-gebruikersinterfacebalk met geselecteerde ops](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

1. Zoek de items die **aangepaste\_scriptactie** hebben uitgevoerd in de kolom **Operations.** Deze vermeldingen worden gemaakt wanneer de scriptacties worden uitgevoerd.

    ![Apache Ambari scriptactiebewerkingen](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Als u de **uitvoer van STDOUT** en **STDERR** wilt weergeven, selecteert u de **run\customscriptaction-vermelding** en zoomt u in via de koppelingen. Deze uitvoer wordt gegenereerd wanneer het script wordt uitgevoerd en mogelijk nuttige informatie heeft.

### <a name="access-logs-from-the-default-storage-account"></a>Logboeken openen vanaf het standaardopslagaccount

Als clustercreatie mislukt vanwege een scriptfout, worden de logboeken bewaard in het clusteropslagaccount.

* De opslaglogboeken `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`zijn beschikbaar op .

    ![Scriptactielogboeken](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    Onder deze map worden de logboeken afzonderlijk georganiseerd voor **headnode,** **werknemersknooppunt**en **zookeeper-knooppunt**. Zie de volgende voorbeelden:

    * **Headnode**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Werknemersknooppunt**:`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper knooppunt**:`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Alle **stdout** en **stderr** van de bijbehorende host worden geüpload naar het opslagaccount. Er is één **uitvoer-\*.txt** en **fouten-\*.txt** voor elke scriptactie. Het **bestand uitvoer-*.txt** bevat informatie over de URI van het script dat op de host is uitgevoerd. De volgende tekst is een voorbeeld van deze informatie:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Het is mogelijk dat u herhaaldelijk een scriptactiecluster met dezelfde naam maakt. In dat geval u de relevante logboeken onderscheiden op basis van de naam van de **datummap.** De mapstructuur voor een cluster, **mycluster,** die op verschillende datums is gemaakt, lijkt bijvoorbeeld op de volgende logboekvermeldingen:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Als u op dezelfde dag een scriptactiecluster met dezelfde naam maakt, u het unieke voorvoegsel gebruiken om de relevante logboekbestanden te identificeren.

* Als u een cluster maakt in de buurt van 12:00 uur, middernacht, is het mogelijk dat de logboekbestanden zich over twee dagen uitstrekken. In dat geval ziet u twee verschillende datummappen voor hetzelfde cluster.

* Het uploaden van logbestanden naar de standaardcontainer kan tot vijf minuten duren, vooral voor grote clusters. Dus als u toegang wilt tot de logboeken, moet u het cluster niet onmiddellijk verwijderen als een scriptactie mislukt.

### <a name="ambari-watchdog"></a>Ambari waakhond

> [!WARNING]  
> Verander het wachtwoord voor de Ambari waakhond, hdinsightwatchdog, niet op uw Linux-gebaseerde HDInsight-cluster. Als u het wachtwoord voor dit account wijzigt, wordt de mogelijkheid om nieuwe scriptacties uit te voeren op het HDInsight-cluster afgebroken.

### <a name="cant-import-name-blobservice"></a>Kan naam BlobService niet importeren

__Symptomen.__ De scriptactie mislukt. Tekst die lijkt op de volgende fout wordt weergegeven wanneer u de bewerking in Ambari bekijkt:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Oorzaak__. Deze fout treedt op als u de Python Azure Storage-client upgradet die is opgenomen in het HDInsight-cluster. HDInsight verwacht Azure Storage-client 0.20.0.

__Resolutie__. Als u deze fout wilt oplossen, maakt `ssh`u handmatig verbinding met elk clusterknooppunt met behulp van . Voer de volgende opdracht uit om de juiste opslagclientversie opnieuw te installeren:

```bash
sudo pip install azure-storage==0.20.0
```

Zie Verbinding maken met [HDInsight (Apache Hadoop) met Behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md)voor informatie over het verbinden met het cluster met SSH.

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>Geschiedenis toont niet de scripts die worden gebruikt tijdens het maken van het cluster

Als uw cluster vóór 15 maart 2016 is gemaakt, ziet u mogelijk geen vermelding in de scriptactiegeschiedenis. Als u het formaat van het cluster wijzigen, worden de scripts weergegeven in de scriptactiegeschiedenis.

Er zijn twee uitzonderingen:

* Uw cluster is gemaakt vóór 1 september 2015. Deze datum is wanneer scriptacties werden geïntroduceerd. Een cluster dat vóór deze datum is gemaakt, kan geen scriptacties hebben gebruikt voor het maken van een cluster.

* U hebt meerdere scriptacties gebruikt tijdens het maken van het cluster. Of u gebruikte dezelfde naam voor meerdere scripts of dezelfde naam, dezelfde URI, maar verschillende parameters voor meerdere scripts. In deze gevallen krijgt u de volgende fout:

    Er kunnen geen nieuwe scriptacties worden uitgevoerd op dit cluster vanwege conflicterende scriptnamen in bestaande scripts. Scriptnamen die bij het maken van het cluster worden opgegeven, moeten allemaal uniek zijn. Bestaande scripts worden uitgevoerd op het formaat van het formaat.

## <a name="next-steps"></a>Volgende stappen

* [Scriptactiescripts ontwikkelen voor HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Extra opslag toevoegen aan een HDInsight-cluster](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Fasen tijdens het maken van clusteren"
