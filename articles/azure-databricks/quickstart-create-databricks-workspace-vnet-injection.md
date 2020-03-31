---
title: Een Azure Databricks-werkruimte maken in uw eigen virtuele netwerk snelstart
description: In dit artikel wordt beschreven hoe u Azure Databricks implementeert in uw virtuele netwerk.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132663"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Snelstart: een Azure Databricks-werkruimte maken in uw eigen virtuele netwerk

De standaardimplementatie van Azure Databricks maakt een nieuw virtueel netwerk dat wordt beheerd door Databricks. In deze quickstart ziet u hoe u in plaats daarvan een Azure Databricks-werkruimte in uw eigen virtuele netwerk maakt. U maakt ook een Apache Spark-cluster binnen die werkruimte. 

Zie [Azure Databricks implementeren in uw Azure Virtual Network (VNet Injection)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)voor meer informatie over waarom u ervoor kiezen een Azure Databricks-werkruimte in uw eigen virtuele netwerk te maken.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/databricks/)aan.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

> [!Note]
> Deze zelfstudie kan niet worden uitgevoerd met **azure free trial subscription**.
> Als je een gratis account hebt, ga je naar je profiel en wijzig je je abonnement naar **betalen per gebruik.** Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie. Verwijder vervolgens [de bestedingslimiet](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)en [vraag een quotumverhoging aan](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) voor vCPU's in uw regio. Wanneer u uw Azure Databricks-werkruimte maakt, u de prijscategorie **Proefkeuzeprijzen (Premium - 14-dagen gratis DU's)** selecteren om de werkruimte gedurende 14 dagen toegang te geven tot gratis Premium Azure Databricks DU's.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer in het menu azure portal de optie **Een resource maken**. Selecteer vervolgens **Netwerken > virtueel netwerk**.

    ![Een virtueel netwerk maken op Azure-portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. Ga **onder Virtueel netwerk maken**de volgende instellingen toe: 

    |Instelling|Voorgestelde waarde|Beschrijving|
    |-------|---------------|-----------|
    |Abonnement|\<Uw abonnement\>|Selecteer het Azure-abonnement dat u wilt gebruiken.|
    |Resourcegroep|databricks-quickstart|Selecteer **Nieuw maken** en voer een nieuwe naam van de resourcegroep in voor uw account.|
    |Name|databricks-quickstart|Selecteer een naam voor uw virtuele netwerk.|
    |Regio|\<Selecteer de regio die het dichtst bij uw gebruikers is gelegen\>|Selecteer een geografische locatie waar u uw virtuele netwerk hosten. Gebruik de locatie die het dichtst bij uw gebruikers is.|

    ![Basisbeginselen voor een virtueel netwerk op Azure-portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Selecteer **Volgende: IP-adressen >** en pas de volgende instellingen toe. Selecteer vervolgens **Controleren + maken**.
    
    |Instelling|Voorgestelde waarde|Beschrijving|
    |-------|---------------|-----------|
    |IPv4-adresruimte|10.2.0.0/16|Het adresbereik van het virtuele netwerk in CIDR-notatie. Het CIDR-bereik moet tussen de /16 en /24 liggen|
    |Subnetnaam|standaardinstelling|Selecteer een naam voor het standaardsubnet in uw virtuele netwerk.|
    |Subnetadresbereik|10.2.0.0/24|Het adresbereik van het subnet in CIDR-notatie. Het moet worden opgenomen door de adresruimte van het virtuele netwerk. Het adresbereik van een subnet dat in gebruik is, kan niet worden bewerkt.|

    ![IP-configuraties instellen voor een virtueel netwerk op Azure-portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. Selecteer op het tabblad **Controleren + maken** de optie **Maken** om het virtuele netwerk te implementeren. Zodra de implementatie is voltooid, navigeert u naar uw virtuele netwerk en selecteert u **Adresruimte** onder **Instellingen**. Voeg in het vak met de `10.179.0.0/16` tekst Extra *adresbereik toevoegen*in en selecteer **Opslaan**.

    ![Azure virtual network address space Azure virtual network address space Azure virtual network address space Azure virtual](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Een Azure Databricks-werkruimte maken

1. Selecteer in het menu azure portal de optie **Een resource maken**. Selecteer vervolgens **Analytics > Databricks**.

    ![Een Azure Databricks-werkruimte maken op Azure-portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. Pas onder **Azure Databricks Service**de volgende instellingen toe:

    |Instelling|Voorgestelde waarde|Beschrijving|
    |-------|---------------|-----------|
    |Naam van de werkruimte|databricks-quickstart|Selecteer een naam voor uw Azure Databricks-werkruimte.|
    |Abonnement|\<Uw abonnement\>|Selecteer het Azure-abonnement dat u wilt gebruiken.|
    |Resourcegroep|databricks-quickstart|Selecteer dezelfde resourcegroep die u voor het virtuele netwerk hebt gebruikt.|
    |Locatie|\<Selecteer de regio die het dichtst bij uw gebruikers is gelegen\>|Kies dezelfde locatie als uw virtuele netwerk.|
    |Prijscategorie|U kunt kiezen tussen Standard en Premium.|Zie de [prijspagina databricks](https://azure.microsoft.com/pricing/details/databricks/)voor meer informatie over prijsniveaus.|

    ![Basisbeginselen van een Azure Databricks-werkruimte maken](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Zodra u klaar bent met het invoeren van instellingen op de pagina **Basisbeginselen,** selecteert u **Volgende: netwerk>** en past u de volgende instellingen toe:

    |Instelling|Voorgestelde waarde|Beschrijving|
    |-------|---------------|-----------|
    |Azure Databricks-werkruimte implementeren in uw Virtuele Netwerk (VNet)|Ja|Met deze instelling u een Azure Databricks-werkruimte implementeren in uw virtuele netwerk.|
    |Virtual Network|databricks-quickstart|Selecteer het virtuele netwerk dat u in de vorige sectie hebt gemaakt.|
    |Naam openbaar subnet|openbaar subnet|Gebruik de standaardnaam van het openbare subnet.|
    |Cidr-bereik openbaar subnet|10.179.64.0/18|Gebruik een CIDR-bereik tot en met /26.|
    |Eigen subnetnaam|privé-subnet|Gebruik de standaardnaam privé-subnet.|
    |Private Subnet CIDR-bereik|10.179.0.0/18|Gebruik een CIDR-bereik tot en met /26.|

    ![VNet-informatie toevoegen aan Azure Databricks-werkruimte op Azure-portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. Zodra de implementatie is voltooid, navigeert u naar de Azure Databricks-bron. Merk op dat virtuele netwerkpeering is uitgeschakeld. Let ook op de resourcegroep en de beheerde resourcegroep op de overzichtspagina. 

    ![Azure Databricks-overzicht in Azure-portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    De beheerde resourcegroep is niet aanpasbaar en wordt niet gebruikt om virtuele machines te maken. U alleen virtuele machines maken in de resourcegroep die u beheert.

    ![Beheerde brongroep azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    Wanneer een implementatie van een werkruimte mislukt, wordt de werkruimte nog steeds gemaakt in een mislukte status. Verwijder de mislukte werkruimte en maak een nieuwe werkruimte die de implementatiefouten oplost. Wanneer u de mislukte werkruimte verwijdert, worden ook de beheerde resourcegroep en eventuele met succes geïmplementeerde resources verwijderd.

## <a name="create-a-cluster"></a>Een cluster maken

> [!NOTE]
> Als u een gratis account wilt gebruiken om het Azure Databricks-cluster te maken, gaat u voordat het cluster is gemaakt naar uw profiel en wijzigt u uw abonnement in **betalen per gebruik**. Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie.

1. Ga terug naar uw Azure Databricks-service en selecteer **Werkruimte starten** op de pagina **Overzicht.**

2. Selecteer **Clusters** > **+ Cluster maken**. Maak vervolgens een clusternaam, zoals *databricks-quickstart-cluster,* en accepteer de resterende standaardinstellingen. Selecteer **Cluster maken**.

    ![Azure Databricks-cluster maken](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Zodra het cluster wordt uitgevoerd, keert u terug naar de beheerde brongroep in de Azure-portal. Let op de nieuwe virtuele machines, schijven, IP-adres en netwerkinterfaces. Er wordt een netwerkinterface gemaakt in elk van de openbare en private subnetten met IP-adressen.  

    ![Beheerde brongroep Azure Databricks na het maken van een cluster](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Ga terug naar uw Azure Databricks-werkruimte en selecteer het cluster dat u hebt gemaakt. Navigeer vervolgens naar het tabblad **Uitvoerders** op de **pagina Spark UI.** Merk op dat de adressen voor de bestuurder en de uitvoerders zich in het privé subnetbereik bevinden. In dit voorbeeld is de bestuurder 10.179.0.6 en uitvoerders 10.179.0.4 en 10.179.0.5. Uw IP-adressen kunnen anders zijn.

    ![Azure Databricks Spark UI-uitvoerders](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u het artikel hebt doorgenomen, kunt u het cluster beëindigen. Dit doet u door vanuit de Azure Databricks-werkruimte in het linkerdeelvenster **Clusters** te selecteren. Voor het cluster dat u wilt beëindigen, plaatst u de cursor op het weglatingsteken onder de kolom **Acties** en selecteert u het **beëindigingspictogram**. Dit stopt het cluster.

Als u het cluster niet handmatig beëindigt, stopt het automatisch, op voorwaarde dat u het selectievakje **Beëindigen na \_ \_ minuten van inactiviteit** hebt geselecteerd tijdens het maken van het cluster. In dat geval stopt het cluster automatisch als het gedurende de opgegeven tijd inactief is geweest.

Als u het cluster niet opnieuw wilt gebruiken, u de brongroep verwijderen die u in de Azure-portal hebt gemaakt.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Spark-cluster gemaakt in Azure Databricks dat u hebt geïmplementeerd in een virtueel netwerk. Ga naar het volgende artikel voor meer informatie over het opvragen van een SQL Server Linux Docker-container in het virtuele netwerk met JDBC vanuit een Azure Databricks-notitieblok.  

> [!div class="nextstepaction"]
>[Een query uitvoeren op een SQL Server Linux Docker-container in een virtueel netwerk vanuit een Azure Databricks-notebook](vnet-injection-sql-server.md)
