---
title: Azure HDInsight-clusters aanpassen met behulp van script acties
description: Aangepaste onderdelen toevoegen aan HDInsight-clusters op basis van Linux met behulp van script acties. Script acties zijn bash-scripts die kunnen worden gebruikt voor het aanpassen van de cluster configuratie of het toevoegen van extra services en hulpprogram ma's, zoals tint, solr of R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: 555596ba1040fcbd5c9131869fd275d749e0d734
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934023"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Azure HDInsight-clusters aanpassen met behulp van script acties

Azure HDInsight biedt een configuratie methode met de naam **script acties** waarmee aangepaste scripts worden aangeroepen om het cluster aan te passen. Deze scripts worden gebruikt voor het installeren van extra onderdelen en het wijzigen van configuratie-instellingen. Script acties kunnen worden gebruikt tijdens of na het maken van het cluster.

Script acties kunnen ook worden gepubliceerd naar Azure Marketplace als een HDInsight-toepassing. Zie [een hdinsight-toepassing publiceren in azure Marketplace](hdinsight-apps-publish-applications.md)voor meer informatie over hdinsight-toepassingen.

## <a name="permissions"></a>Machtigingen

Voor een HDInsight-cluster dat is gekoppeld aan een domein, zijn er twee Apache Ambari-machtigingen vereist wanneer u script acties gebruikt met het cluster:

* **AMBARI. Voer\_aangepaste\_opdracht uit**. De Ambari-beheerdersrol heeft deze machtiging standaard.
* **Cluster. Voer\_aangepaste\_opdracht uit**. Zowel de beheerder van het HDInsight-cluster als de Ambari-beheerder hebben standaard deze machtiging.

Zie [hdinsight-clusters beheren met Enterprise Security Package](./domain-joined/apache-domain-joined-manage.md)voor meer informatie over het werken met machtigingen met hdinsight die lid zijn van een domein.

## <a name="access-control"></a>Toegangsbeheer

Als u niet de beheerder of eigenaar van uw Azure-abonnement bent, moet uw account ten minste de Inzender toegang hebben tot de resource groep die het HDInsight-cluster bevat.

Als u een HDInsight-cluster maakt, moet iemand met ten minste de Inzender toegang tot het Azure-abonnement eerder de provider voor HDInsight hebben geregistreerd. Registratie van een provider vindt plaats wanneer een gebruiker met toegang tot het abonnement op het niveau van Inzender, voor het eerst een resource maakt onder het abonnement. U kunt dit ook doen zonder een resource te maken als u [een provider registreert met behulp van rest](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Meer informatie over het werken met toegangs beheer:

* [Aan de slag met toegangsbeheer in Azure Portal](../role-based-access-control/overview.md)
* [Roltoewijzingen gebruiken voor het beheer van de toegang tot de resources van uw Azure-abonnement](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Script acties begrijpen

Een script actie is een bash script dat wordt uitgevoerd op de knoop punten in een HDInsight-cluster. Kenmerken en functies van script acties zijn als volgt:

* Moet worden opgeslagen op een URI die toegankelijk is vanuit het HDInsight-cluster. De volgende opslag locaties zijn mogelijk:

    * Voor reguliere clusters:

      * ADLS Gen1: de Service-Principal HDInsight gebruikt om toegang te krijgen tot Data Lake Storage moet lees toegang hebben tot het script. De URI-indeling voor scripts die zijn opgeslagen in Data Lake Storage Gen1 is `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

      * Een BLOB in een Azure Storage-account dat ofwel het primaire of extra opslag account voor het HDInsight-cluster is. Bij het maken van het cluster wordt aan HDInsight toegang verleend tot beide typen opslag accounts.

        > [!IMPORTANT]  
        > Roteer de opslag sleutel niet op dit Azure Storage account, omdat dit ertoe leidt dat volgende script acties worden uitgevoerd met scripts die daar zijn opgeslagen.

      * Een open bare service voor het delen van bestanden via http://-paden. Voor beelden zijn Azure Blob, GitHub, OneDrive.

        Zie [voorbeeld script actie scripts](#example-script-action-scripts)voor voor beelden van uri's.

     * Voor clusters met ESP:

         * De wasb://of wasbs://of http [s]://Uri's worden ondersteund.

* Kan alleen worden uitgevoerd op bepaalde knooppunt typen. Voor beelden zijn hoofd knooppunten of worker-knoop punten.

* Kan persistent of ad hoc zijn.

    Persistente scripts worden gebruikt om nieuwe worker-knoop punten die aan het cluster worden toegevoegd, aan te passen via schaal bewerkingen. Een persistent script kan ook wijzigingen Toep assen op een ander type knoop punt wanneer er schaal bewerkingen worden uitgevoerd. Een voor beeld is een hoofd knooppunt.

  > [!IMPORTANT]  
  > Persistente script acties moeten een unieke naam hebben.

    Ad-hoc scripts zijn niet persistent. Ze worden niet toegepast op worker-knoop punten die zijn toegevoegd aan het cluster nadat het script is uitgevoerd. Vervolgens kunt u een ad-hoc script promo veren naar een persistent script of een persistent script naar een ad-hoc script verlagen.

  > [!IMPORTANT]  
  > Script acties die worden gebruikt tijdens het maken van het cluster, worden automatisch bewaard.
  >
  > Scripts die niet worden bewaard, zelfs niet als u er specifiek voor hebt aangegeven dat ze moeten zijn.

* Kan para meters accepteren die door het script worden gebruikt tijdens de uitvoering.

* Uitvoeren met bevoegdheden op hoofd niveau op de cluster knooppunten.

* Kan worden gebruikt via de Azure Portal, Azure PowerShell, de klassieke Azure-CLI of de HDInsight .NET SDK.

Het cluster houdt een geschiedenis bij van alle scripts die zijn uitgevoerd. De geschiedenis helpt u bij het vinden van de ID van een script voor promotie-of degradatie bewerkingen.

> [!IMPORTANT]  
> Er is geen automatische manier om de wijzigingen die door een script actie zijn aangebracht, ongedaan te maken. U kunt de wijzigingen hand matig ongedaan maken of een script opgeven waarmee ze worden teruggedraaid.

### <a name="script-action-in-the-cluster-creation-process"></a>Script actie in het proces voor het maken van het cluster

Script acties die worden gebruikt tijdens het maken van een cluster, zijn iets anders dan script acties die worden uitgevoerd op een bestaand cluster:

* Het script wordt automatisch persistent gemaakt.

* Een fout in het script kan ertoe leiden dat het proces voor het maken van een cluster mislukt.

Het volgende diagram illustreert wanneer de script actie wordt uitgevoerd tijdens het maken van het proces:

![Aanpassing en fasen van HDInsight-cluster tijdens het maken van het cluster][img-hdi-cluster-states]

Het script wordt uitgevoerd terwijl HDInsight wordt geconfigureerd. Het script wordt parallel uitgevoerd op alle opgegeven knoop punten in het cluster. Het wordt uitgevoerd met hoofd bevoegdheden op de knoop punten.

> [!NOTE]  
> U kunt bewerkingen uitvoeren zoals het stoppen en starten van services, met inbegrip van Apache Hadoop-gerelateerde services. Als u services stopt, moet u ervoor zorgen dat de Ambari-service en andere Hadoop-gerelateerde services worden uitgevoerd voordat het script is voltooid. Deze services zijn vereist om de status en status van het cluster tijdens het maken te bepalen.

Tijdens het maken van het cluster kunt u veel script acties tegelijk gebruiken. Deze scripts worden aangeroepen in de volg orde waarin ze zijn opgegeven.

> [!IMPORTANT]  
> Script acties moeten binnen 60 minuten worden voltooid of er is een time-out opgestaan. Tijdens het inrichten van het cluster wordt het script gelijktijdig uitgevoerd met andere installatie-en configuratie processen. Concurrentie voor bronnen, zoals CPU-tijd of netwerk bandbreedte, kan ertoe leiden dat het script langer duurt dan in uw ontwikkel omgeving is voltooid.
>
> Voor het minimaliseren van de tijd die nodig is om het script uit te voeren, vermijdt u taken zoals het downloaden en compileren van toepassingen vanuit de bron. Toepassingen precompileren en de binaire bestanden opslaan in Azure Storage.

### <a name="script-action-on-a-running-cluster"></a>Script actie op een actief cluster

Als er een fout optreedt in een script dat wordt uitgevoerd op een cluster dat al wordt uitgevoerd, wordt het cluster niet automatisch gewijzigd in een mislukte status. Nadat het script is voltooid, moet het cluster terugkeren naar een actieve status.

> [!IMPORTANT]  
> Zelfs als het cluster een actieve status heeft, heeft het mislukte script mogelijk items gebroken. Een script kan bijvoorbeeld bestanden verwijderen die nodig zijn voor het cluster.
>
> Scripts acties worden uitgevoerd met hoofd bevoegdheden. Zorg ervoor dat u begrijpt wat een script doet voordat u het toepast op uw cluster.

Wanneer u een script toepast op een cluster, wordt de status van het cluster gewijzigd van **actief** in **geaccepteerd**. Vervolgens wordt de **HDInsight-configuratie** gewijzigd en, ten slotte, opnieuw voor het **uitvoeren** van geslaagde scripts. De script status wordt vastgelegd in de geschiedenis van de script actie. Deze informatie geeft aan of het script is geslaagd of mislukt. Met de `Get-AzHDInsightScriptActionHistory` Power shell-cmdlet wordt bijvoorbeeld de status van een script weer gegeven. Het retourneert informatie die lijkt op de volgende tekst:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> Als u de cluster gebruiker, de beheerder, het wacht woord wijzigt nadat het cluster is gemaakt, kunnen script acties op dit cluster mislukken. Als u persistente script acties hebt die doel worker-knoop punten hebben, kunnen deze scripts mislukken wanneer u het cluster schaalt.

## <a name="example-script-action-scripts"></a>Voorbeeld script actie scripts

Script actie scripts kunnen worden gebruikt via de volgende hulpprogram ma's:

* Azure Portal
* Azure PowerShell
* De klassieke Azure-CLI
* An HDInsight .NET SDK

HDInsight biedt scripts voor het installeren van de volgende onderdelen op HDInsight-clusters:

| Name | Script |
| --- | --- |
| Een Azure Storage-account toevoegen |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Zie [extra opslag accounts toevoegen aan HDInsight](hdinsight-hadoop-add-storage.md). |
| Hue installeren |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Zie [kleur Toon installeren en gebruiken op HDInsight Hadoop-clusters](hdinsight-hadoop-hue-linux.md). |
| Hive-bibliotheken vooraf laden |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Zie [aangepaste Apache Hive bibliotheken toevoegen bij het maken van uw HDInsight-cluster](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="use-a-script-action-during-cluster-creation"></a>Een script actie gebruiken tijdens het maken van een cluster

In deze sectie worden de verschillende manieren beschreven waarop u script acties kunt gebruiken wanneer u een HDInsight-cluster maakt.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Een script actie gebruiken tijdens het maken van het cluster vanuit het Azure Portal

1. Begin met het maken van een cluster zoals beschreven in op [Linux gebaseerde clusters in HDInsight maken met behulp van de Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md). Tijdens het maken van het cluster arriveert u bij stap 6, **script acties**. Navigeer naar **optionele** >  **+ nieuwe verzenden**.

    ![Script actie voor Azure Portal cluster](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-classic-script-action.png)

1. Gebruik het __script item selecteren__ om een vooraf gemaakt script te selecteren. Als u een aangepast script wilt gebruiken, selecteert u __aangepast__. Geef vervolgens de __naam__ en de __bash-script-URI__ voor uw script op.

    ![Een script toevoegen in het formulier Select script](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    De volgende tabel beschrijft de elementen op het formulier:

    | Eigenschap | Waarde |
    | --- | --- |
    | Een script selecteren | Selecteer __aangepast__om uw eigen script te gebruiken. Anders selecteert u een van de meegeleverde scripts. |
    | Name |Geef een naam op voor de script actie. |
    | Bash-script-URI |Geef de URI van het script op. |
    | Hoofd/werk-ZooKeeper |Geef de knoop punten op waarop het script wordt uitgevoerd: **Head**, **worker**of **ZooKeeper**. |
    | Parameters |Geef de para meters op, indien vereist door het script. |

    Gebruik de vermelding __Deze script actie persistent__ maken om ervoor te zorgen dat het script wordt toegepast tijdens schaal bewerkingen.

1. Selecteer __maken__ om het script op te slaan. Vervolgens kunt u __+ Nieuw__ gebruiken om een ander script toe te voegen.

    ![Meervoudige HDInsight-script acties](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    Wanneer u klaar bent met het toevoegen van scripts, selecteert u de knop __selecteren__ en vervolgens de knop __volgende__ om door te gaan naar de sectie __cluster samenvatting__ .

1. Als u het cluster wilt maken, selecteert u __maken__ op basis van de selectie van de __cluster samenvatting__ .

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Een script actie uit Azure Resource Manager sjablonen gebruiken

Script acties kunnen worden gebruikt met Azure Resource Manager sjablonen. Zie voor beeld [een HDInsight Linux-cluster maken en een script actie uitvoeren](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/).

In dit voor beeld wordt de script actie toegevoegd met behulp van de volgende code:

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

* [Resources implementeren met Resource Manager-sjablonen en Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Een script actie gebruiken tijdens het maken van het cluster van Azure PowerShell

In deze sectie gebruikt u de cmdlet [add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) om scripts aan te roepen om een cluster aan te passen. Voordat u begint, moet u ervoor zorgen dat u Azure PowerShell installeert en configureert. Als u deze Power shell-opdrachten wilt gebruiken, hebt u de [AZ-module](https://docs.microsoft.com/powershell/azure/overview)nodig.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Het volgende script laat zien hoe u een script actie toepast wanneer u een cluster maakt met behulp van Power shell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Het kan enkele minuten duren voordat het cluster is gemaakt.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Een script actie gebruiken tijdens het maken van het cluster vanuit de HDInsight .NET SDK

De HDInsight .NET SDK bevat client bibliotheken waarmee u gemakkelijker kunt werken met HDInsight vanuit een .NET-toepassing. Zie [script acties](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions)voor een code voorbeeld.

## <a name="apply-a-script-action-to-a-running-cluster"></a>Een script actie Toep assen op een actief cluster

In deze sectie wordt uitgelegd hoe u script acties toepast op een actief cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Een script actie Toep assen op een actief cluster vanuit de Azure Portal

Ga naar de [Azure Portal](https://portal.azure.com):

1. Ga in het menu links naar **alle services** >  **Analytics** - > **HDInsight-clusters**.

1. Selecteer uw cluster in de lijst. Hiermee wordt de standaard weergave geopend.

1. Selecteer in de standaard weergave onder **instellingen**de optie **script acties**.

1. Selecteer boven aan de pagina **script acties** **+ Nieuw verzenden**.

    ![Een script toevoegen aan een actief cluster](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Gebruik het __script item selecteren__ om een vooraf gemaakt script te selecteren. Als u een aangepast script wilt gebruiken, selecteert u __aangepast__. Geef vervolgens de __naam__ en de __bash-script-URI__ voor uw script op.

    ![Een script toevoegen in het formulier Select script](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    De volgende tabel beschrijft de elementen op het formulier:

    | Eigenschap | Waarde |
    | --- | --- |
    | Een script selecteren | Selecteer __aangepast__om uw eigen script te gebruiken. Als dat niet het geval is, selecteert u een gegeven script. |
    | Name |Geef een naam op voor de script actie. |
    | Bash-script-URI |Geef de URI van het script op. |
    | Hoofd/werk-Zookeeper |Geef de knoop punten op waarop het script wordt uitgevoerd: **Head**, **worker**of **ZooKeeper**. |
    | Parameters |Geef de para meters op, indien vereist door het script. |

    Gebruik de vermelding __Deze script actie persistent__ om te controleren of het script wordt toegepast tijdens schaal bewerkingen.

1. Selecteer tot slot de knop **maken** om het script toe te passen op het cluster.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Een script actie Toep assen op een actief cluster vanaf Azure PowerShell

Als u deze Power shell-opdrachten wilt gebruiken, hebt u de [AZ-module](https://docs.microsoft.com/powershell/azure/overview)nodig.

In het volgende voor beeld ziet u hoe u een script actie toepast op een actief cluster:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

Nadat de bewerking is voltooid, ontvangt u informatie die lijkt op de volgende tekst:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Een script actie Toep assen op een actief cluster vanuit de Azure CLI

Voordat u begint, moet u ervoor zorgen dat u de Azure CLI installeert en configureert. Zie [de klassieke Azure-cli installeren](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)voor meer informatie.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Overschakelen naar Azure Resource Manager modus:

    ```bash
    azure config mode arm
    ```

2. Verifiëren bij uw Azure-abonnement:

    ```bash
    azure login
    ```

3. Een script actie Toep assen op een actief cluster:

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    Als u para meters voor deze opdracht weglaat, wordt u gevraagd. Als het script dat u opgeeft met `-u` para meters accepteert, kunt u deze opgeven met behulp van de para meter `-p`.

    Geldige knooppunt typen zijn `headnode`, `workernode`en `zookeeper`. Als het script moet worden toegepast op verschillende knooppunt typen, geeft u de typen op, gescheiden door een punt komma `;`. Bijvoorbeeld `-n headnode;workernode`.

    Als u het script wilt behouden, voegt u `--persistOnSuccess`toe. U kunt het script ook later persistent maken met behulp van `azure hdinsight script-action persisted set`.

    Nadat de taak is voltooid, krijgt u uitvoer zoals de volgende tekst:

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Een script actie Toep assen op een actief cluster met behulp van REST API

Zie [Cluster rest API in azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Een script actie Toep assen op een actief cluster vanuit de HDInsight .NET SDK

Zie [een script actie Toep assen op een op Linux gebaseerd HDInsight-cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)voor een voor beeld van het gebruik van de .NET SDK om scripts toe te passen op een cluster.

## <a name="view-history-and-promote-and-demote-script-actions"></a>De geschiedenis bekijken en script acties promo veren en verlagen

### <a name="the-azure-portal"></a>Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Ga in het menu links naar **alle services** > **Analytics** - > **HDInsight-clusters**.

1. Selecteer uw cluster in de lijst. Hiermee wordt de standaard weergave geopend.

1. Selecteer in de standaard weergave onder **instellingen**de optie **script acties**.

1. Een geschiedenis van scripts voor dit cluster wordt weer gegeven in de sectie script acties. Deze informatie bevat een lijst met persistente scripts. In de volgende scherm afbeelding ziet u dat het script solr is uitgevoerd op dit cluster. In de scherm afbeelding worden geen persistente scripts weer gegeven.

    ![Overzicht van acties voor portal script](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. Selecteer een script in de geschiedenis om de sectie **Eigenschappen** voor dit script weer te geven. Vanaf de bovenkant van het scherm kunt u het script opnieuw uitvoeren of het niveau verhogen.

    ![Eigenschappen van script acties niveau verhogen](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. U kunt ook het weglatings teken, **...** , rechts van de vermeldingen in de sectie script acties selecteren om acties uit te voeren.

    ![Persistente script acties verwijderen](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| cmdlet | Functie |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Informatie over persistente script acties ophalen. |
| `Get-AzHDInsightScriptActionHistory` |Een geschiedenis ophalen van script acties die zijn toegepast op het cluster of Details voor een specifiek script. |
| `Set-AzHDInsightPersistedScriptAction` |Bevorder een ad-hoc script actie naar een persistente script actie. |
| `Remove-AzHDInsightPersistedScriptAction` |Het niveau van een persistente script actie verlagen naar een ad hoc actie. |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction` de acties die door een script worden uitgevoerd, worden niet ongedaan gemaakt. Met deze cmdlet wordt alleen de persistente vlag verwijderd.

In het volgende voorbeeld script ziet u hoe u de-cmdlets gebruikt om een script te promoten en vervolgens te degraderen.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>De klassieke Azure-CLI

| cmdlet | Functie |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |Een lijst met persistente script acties ophalen. |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |Gegevens ophalen voor een specifieke persistente script actie. |
| `azure hdinsight script-action history list <clustername>` |Een geschiedenis ophalen van script acties die zijn toegepast op het cluster. |
| `azure hdinsight script-action history show <clustername> <scriptname>` |Gegevens ophalen voor een specifieke script actie. |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |Bevorder een ad-hoc script actie naar een persistente script actie. |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |Het niveau van een persistente script actie verlagen naar een ad hoc actie. |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` de acties die door een script worden uitgevoerd, worden niet ongedaan gemaakt. Met deze cmdlet wordt alleen de persistente vlag verwijderd.

### <a name="the-hdinsight-net-sdk"></a>De HDInsight .NET SDK

Zie [een script actie Toep assen op een op Linux gebaseerd HDInsight-cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)voor een voor beeld van het gebruik van de .NET SDK voor het ophalen van de script geschiedenis van een cluster, het promo veren of verlagen van scripts.

> [!NOTE]  
> In dit voor beeld wordt ook gedemonstreerd hoe u een HDInsight-toepassing installeert met behulp van de .NET-SDK.

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Ondersteuning voor open-source software die wordt gebruikt in HDInsight-clusters

De Microsoft Azure HDInsight-service gebruikt een ecosysteem van open-source technologieën die zijn gevormd rond Apache Hadoop. Microsoft Azure biedt een algemeen ondersteunings niveau voor open-source technologieën. Zie de sectie **support Scope** van [Veelgestelde vragen over ondersteuning van Azure](https://azure.microsoft.com/support/faq/)voor meer informatie. De HDInsight-service biedt een extra ondersteunings niveau voor ingebouwde componenten.

Er zijn twee typen open source-onderdelen beschikbaar in de HDInsight-service:

* **Ingebouwde onderdelen**. Deze onderdelen zijn vooraf geïnstalleerd op HDInsight-clusters en bieden kern functionaliteit van het cluster. De volgende onderdelen maken deel uit van deze categorie:

  * [Apache HADOOP garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Resource.
  * De [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)van de Hive-query taal.
  * [Apache mahout](https://mahout.apache.org/).

    Er is een volledige lijst met cluster onderdelen beschikbaar in [Wat zijn de Apache Hadoop onderdelen en versies die beschikbaar zijn in HDInsight?](hdinsight-component-versioning.md)

* **Aangepaste onderdelen**. Als gebruiker van het cluster kunt u in uw workload elk onderdeel dat beschikbaar is in de Community installeren of gebruiken, of door u gemaakt.

> [!WARNING]  
> Onderdelen die worden meegeleverd met het HDInsight-cluster, worden volledig ondersteund. Microsoft Ondersteuning helpt bij het isoleren en oplossen van problemen met betrekking tot deze onderdelen.
>
> Aangepaste onderdelen ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Microsoft Ondersteuning kunt het probleem mogelijk oplossen. Het kan ook zijn dat u beschik bare kanalen moet betrekken voor de open source technologieën waar diep gaande expertise voor die technologie wordt gevonden. Veel community-sites kunnen worden gebruikt. Voor beelden zijn [MSDN-forum voor HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) en [stack overflow](https://stackoverflow.com).
>
> Apache-projecten hebben ook project sites op de [Apache-website](https://apache.org). Een voor beeld is [Hadoop](https://hadoop.apache.org/).

De HDInsight-service biedt verschillende manieren om aangepaste onderdelen te gebruiken. Hetzelfde ondersteunings niveau geldt, ongeacht hoe een onderdeel wordt gebruikt of geïnstalleerd op het cluster. In de volgende lijst worden de meest voorkomende manieren beschreven waarop aangepaste onderdelen worden gebruikt in HDInsight-clusters:

1. **Taak wordt verzonden**. Hadoop of andere typen taken die aangepaste onderdelen uitvoeren of gebruiken, kunnen worden verzonden naar het cluster.

2. **Aanpassing**van het cluster. Tijdens het maken van het cluster kunt u aanvullende instellingen en aangepaste onderdelen opgeven die op de cluster knooppunten zijn geïnstalleerd.

3. Voor **beelden**. Voor populaire aangepaste onderdelen kunnen micro soft en andere voor beelden bieden van hoe deze onderdelen kunnen worden gebruikt in HDInsight-clusters. Deze voor beelden worden zonder ondersteuning geboden.

## <a name="troubleshooting"></a>Problemen oplossen

U kunt de Ambari-web-gebruikers interface gebruiken om informatie te bekijken die is vastgelegd door script acties. Als het script mislukt tijdens het maken van het cluster, zijn de logboeken ook beschikbaar in het standaard opslag account dat aan het cluster is gekoppeld. Deze sectie bevat informatie over het ophalen van de logboeken met behulp van beide opties.

### <a name="the-apache-ambari-web-ui"></a>De Apache Ambari-webgebruikersinterface

1. Ga in uw browser naar `https://CLUSTERNAME.azurehdinsight.net`. Vervang **CLUSTERNAME** door de naam van uw HDInsight-cluster.

    Wanneer u hierom wordt gevraagd, voert u de account naam, de **beheerder**en het wacht woord voor het cluster in. Mogelijk moet u de beheerders referenties opnieuw invoeren in een webformulier.

2. Selecteer de **OPS** -vermelding in de balk boven aan de pagina. Er wordt een lijst weer gegeven met de huidige en vorige bewerkingen die op het cluster zijn uitgevoerd via Ambari.

    ![Ambari Web UI-balk met OPS geselecteerd](./media/hdinsight-hadoop-customize-cluster-linux/hdi-apache-ambari-nav.png)

3. Zoek de vermeldingen die zijn **uitgevoerd\_customscriptaction** in de kolom **bewerkingen** . Deze vermeldingen worden gemaakt wanneer de script acties worden uitgevoerd.

    ![Acties voor Apache Ambari-script actie](./media/hdinsight-hadoop-customize-cluster-linux/ambari-script-action.png)

    Als u de **stdout** -en **stderr** -uitvoer wilt weer geven, selecteert u de vermelding **run\customscriptaction** en zoomt u in op de koppelingen. Deze uitvoer wordt gegenereerd wanneer het script wordt uitgevoerd en kan nuttige informatie bevatten.

### <a name="access-logs-from-the-default-storage-account"></a>Toegang tot logboeken vanuit het standaard opslag account

Als het maken van een cluster mislukt vanwege een script fout, worden de logboeken bewaard in het cluster-opslag account.

* De opslag logboeken zijn beschikbaar op `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Script actie logboeken](./media/hdinsight-hadoop-customize-cluster-linux/script-action-logs-in-storage.png)

    In deze map worden de logboeken afzonderlijk geordend voor **hoofd knooppunt**, **worker node**en **Zookeeper node**. Zie de volgende voorbeelden:

    * **Hoofd knooppunt**: `<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Worker-knoop punt**: `<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper-knoop punt**: `<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Alle **stdout** en **stderr** van de bijbehorende host worden geüpload naar het opslag account. Er zijn één **uitvoer-\*. txt** en **fouten-\*. txt** voor elke script actie. Het **output-*. txt** -bestand bevat informatie over de URI van het script dat op de host is uitgevoerd. De volgende tekst is een voor beeld van deze informatie:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* Het is mogelijk dat u herhaaldelijk een script actie cluster met dezelfde naam maakt. In dat geval kunt u de relevante logboeken onderscheiden op basis van de naam van de map **date** . De mapstructuur voor een cluster, **mycluster**, die op verschillende datums wordt gemaakt, lijkt bijvoorbeeld op de volgende logboek vermeldingen:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Als u een script actie cluster met dezelfde naam op dezelfde dag maakt, kunt u het unieke voor voegsel gebruiken om de relevante logboek bestanden te identificeren.

* Als u een cluster maakt in de buurt van 12:00 uur middernacht, is het mogelijk dat de logboek bestanden twee dagen lang zijn. In dat geval ziet u twee verschillende datum mappen voor hetzelfde cluster.

* Het uploaden van logboek bestanden naar de standaard container kan tot vijf minuten duren, met name voor grote clusters. Als u de logboeken wilt openen, moet u het cluster dus niet onmiddellijk verwijderen als een script actie mislukt.

### <a name="ambari-watchdog"></a>Ambari watchdog

> [!WARNING]  
> Wijzig niet het wacht woord voor de Ambari watchdog, hdinsightwatchdog, op uw op Linux gebaseerd HDInsight-cluster. Het wijzigen van het wacht woord voor dit account verbreekt de mogelijkheid om nieuwe script acties uit te voeren op het HDInsight-cluster.

### <a name="cant-import-name-blobservice"></a>Kan de naam BlobService niet importeren

__Symptomen__. De script actie mislukt. Tekst die vergelijkbaar is met de volgende fout wordt weer gegeven wanneer u de bewerking in Ambari bekijkt:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Oorzaak__. Deze fout treedt op als u de python Azure Storage-client bijwerkt die is opgenomen in het HDInsight-cluster. HDInsight verwacht Azure Storage client 0.20.0.

__Oplossing__. Om deze fout op te lossen, moet u hand matig verbinding maken met elk cluster knooppunt met behulp van `ssh`. Voer de volgende opdracht uit om de juiste Storage-client versie opnieuw te installeren:

```bash
sudo pip install azure-storage==0.20.0
```

Zie [verbinding maken met HDInsight (Apache Hadoop) met behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md)voor informatie over het maken van verbinding met het cluster met SSH.

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>In de geschiedenis worden geen scripts weer gegeven die worden gebruikt tijdens het maken van het cluster

Als uw cluster vóór 15 maart 2016 is gemaakt, ziet u mogelijk geen vermelding in de script actie geschiedenis. Het wijzigen van het formaat van het cluster leidt ertoe dat de scripts worden weer gegeven in de geschiedenis van de script actie.

Er zijn twee uitzonderingen:

* Uw cluster is gemaakt vóór 1 september 2015. Deze datum is wanneer script acties zijn geïntroduceerd. Alle clusters die vóór deze datum zijn gemaakt, kunnen geen script acties hebben voor het maken van het cluster.

* U hebt meerdere script acties gebruikt tijdens het maken van het cluster. Of u hebt dezelfde naam gebruikt voor meerdere scripts of dezelfde naam, dezelfde URI, maar verschillende para meters voor meerdere scripts. In deze gevallen wordt de volgende fout weer geven:

    Er kunnen geen nieuwe script acties op dit cluster worden uitgevoerd, omdat er conflicterende script namen in bestaande scripts zijn. Script namen die worden meegeleverd tijdens het maken van het cluster, moeten allemaal uniek zijn. Bestaande scripts worden uitgevoerd op een andere grootte.

## <a name="next-steps"></a>Volgende stappen

* [Script actie scripts voor HDInsight ontwikkelen](hdinsight-hadoop-script-actions-linux.md)
* [Extra opslag ruimte toevoegen aan een HDInsight-cluster](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Fasen tijdens het maken van het cluster"
