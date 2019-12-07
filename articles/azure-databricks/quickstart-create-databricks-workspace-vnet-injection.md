---
title: Een Azure Databricks-werk ruimte maken in uw eigen Virtual Network Snelstartgids
description: In dit artikel wordt beschreven hoe u Azure Databricks implementeert in uw virtuele netwerk.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 404aa1d345bb1e30a0fe55d033d49d7dd8ba5a1b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893412"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Quick Start: een Azure Databricks-werk ruimte maken in uw eigen Virtual Network

De standaard implementatie van Azure Databricks maakt een nieuw virtueel netwerk dat wordt beheerd door Databricks. In deze Quick start ziet u hoe u in plaats daarvan een Azure Databricks werkruimte maakt in uw eigen virtuele netwerk. U maakt ook een Apache Spark cluster binnen die werk ruimte. 

Zie [Deploy Azure Databricks in uw Azure Virtual Network (VNet-injectie)] (/databricks/Administration-Guide/Cloud-configurations/Azure/vnet-Inject) voor meer informatie over het maken van een Azure Databricks-werk ruimte in uw eigen virtuele netwerk.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/databricks/) aan.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

> [!Note]
> Deze zelf studie kan niet worden uitgevoerd met een **gratis proef abonnement van Azure**.
> Als u een gratis account hebt, gaat u naar uw profiel en wijzigt u uw abonnement in **betalen per gebruik**. Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie. Vervolgens [verwijdert u de bestedings limiet](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)en [vraagt u een quotum toename](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) aan voor vcpu's in uw regio. Wanneer u uw Azure Databricks-werk ruimte maakt, kunt u de prijs categorie **Trial (Premium-14-dagen gratis dbu's)** selecteren om de werk ruimte gedurende 14 dagen toegang te geven tot gratis premium Azure Databricks dbu's.

## <a name="create-a-virtual-network"></a>Maak een virtueel netwerk

1. Selecteer in het menu Azure Portal de optie **een resource maken**. Selecteer vervolgens **netwerk > virtueel netwerk**.

    ![Een virtueel netwerk maken op Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. Pas onder **virtueel netwerk maken**de volgende instellingen toe: 

    |Instelling|Voorgestelde waarde|Beschrijving|
    |-------|---------------|-----------|
    |Naam|databricks-Quick Start|Selecteer een naam voor het virtuele netwerk.|
    |Adresruimte|10.1.0.0/16|Het adres bereik van het virtuele netwerk in CIDR-notatie. Het CIDR-bereik moet tussen/16 en/24 zijn|
    |Abonnement|\<Uw abonnement\>|Selecteer het Azure-abonnement dat u wilt gebruiken.|
    |Resourcegroep|databricks-Quick Start|Selecteer **nieuwe maken** en voer een nieuwe resource groeps naam in voor uw account.|
    |Locatie|\<Selecteer de regio die het dichtst bij uw gebruikers is gelegen\>|Selecteer een geografische locatie waar u uw virtuele netwerk kunt hosten. Gebruik de locatie die het dichtst bij uw gebruikers is.|
    |Subnetnaam|standaardinstelling|Selecteer een naam voor het standaard subnet in het virtuele netwerk.|
    |Subnetadresbereik|10.1.0.0/24|Het adresbereik van het subnet in CIDR-notatie. Het moet deel uitmaken van de adres ruimte van het virtuele netwerk. Het adres bereik van een subnet dat wordt gebruikt, kan niet worden bewerkt.|

    ![Een virtueel netwerk maken op Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Zodra de implementatie is voltooid, gaat u naar het virtuele netwerk en selecteert u **adres ruimte** onder **instellingen**. In het vak met de tekst *extra adres bereik toevoegen voegt*u `10.179.0.0/16` in en selecteert u **Opslaan**.

    ![Adres ruimte van het virtuele Azure-netwerk](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Een Azure Databricks-werkruimte maken

1. Selecteer in het menu Azure Portal de optie **een resource maken**. Selecteer vervolgens **Analytics > Databricks**.

    ![Een Azure Databricks-werk ruimte maken op Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. Pas onder **Azure Databricks-service**de volgende instellingen toe:

    |Instelling|Voorgestelde waarde|Beschrijving|
    |-------|---------------|-----------|
    |Naam van de werkruimte|databricks-Quick Start|Selecteer een naam voor uw Azure Databricks-werk ruimte.|
    |Abonnement|\<Uw abonnement\>|Selecteer het Azure-abonnement dat u wilt gebruiken.|
    |Resourcegroep|databricks-Quick Start|Selecteer dezelfde resource groep die u hebt gebruikt voor het virtuele netwerk.|
    |Locatie|\<Selecteer de regio die het dichtst bij uw gebruikers is gelegen\>|Kies dezelfde locatie als het virtuele netwerk.|
    |Prijsniveau|Kies tussen Standard of Premium.|Zie de [pagina met prijzen voor Databricks](https://azure.microsoft.com/pricing/details/databricks/)voor meer informatie over prijs categorieën.|
    |Azure Databricks-werk ruimte in uw Virtual Network (VNet) implementeren|Ja|Met deze instelling kunt u een Azure Databricks-werk ruimte in uw virtuele netwerk implementeren.|
    |Virtueel netwerk|databricks-Quick Start|Selecteer het virtuele netwerk dat u in de vorige sectie hebt gemaakt.|
    |Naam van openbaar subnet|openbaar subnet|Gebruik de standaard naam van het open bare subnet.|
    |CIDR-bereik openbaar subnet|10.179.64.0/18|Gebruik een CIDR-bereik tot en met/26.|
    |Naam van particulier subnet|persoonlijk subnet|De standaard naam van het particuliere subnet gebruiken.|
    |CIDR-bereik voor persoonlijk subnet|10.179.0.0/18|Gebruik een CIDR-bereik tot en met/26.|

    ![Een Azure Databricks-werk ruimte maken op Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Wanneer de implementatie is voltooid, gaat u naar de Azure Databricks-resource. U ziet dat de peering van het virtuele netwerk is uitgeschakeld. U ziet ook de resource groep en de beheerde resource groep op de pagina overzicht. 

    ![Azure Databricks overzicht in Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    De beheerde resource groep kan niet worden gewijzigd en wordt niet gebruikt voor het maken van virtuele machines. U kunt alleen virtuele machines maken in de resource groep die u beheert.

    ![Beheerde resource groep Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Een cluster maken

> [!NOTE]
> Als u een gratis account wilt gebruiken om het Azure Databricks-cluster te maken, gaat u voordat het cluster is gemaakt naar uw profiel en wijzigt u uw abonnement in **betalen per gebruik**. Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie.

1. Ga terug naar uw Azure Databricks-service en selecteer **werk ruimte starten** op de pagina **overzicht** .

2. Selecteer **Clusters** >  **+ cluster maken**. Maak vervolgens een cluster naam, zoals *databricks-Quick Start-cluster*, en accepteer de overige standaard instellingen. Selecteer **Cluster maken**.

    ![Azure Databricks cluster maken](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Zodra het cluster actief is, keert u terug naar de beheerde resource groep in de Azure Portal. Let op de nieuwe virtuele machines, schijven, IP-adres en netwerk interfaces. Er wordt een netwerk interface gemaakt in elk van de open bare en particuliere subnetten met IP-adressen.  

    ![Azure Databricks beheerde resource groep na het maken van het cluster](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Ga terug naar uw Azure Databricks-werk ruimte en selecteer het cluster dat u hebt gemaakt. Ga vervolgens naar het tabblad **uitvoerende Webuitvoeringen** op de pagina **Spark-gebruikers interface** . U ziet dat de adressen voor het stuur programma en de uitvoerders zich in het particuliere subnet-bereik bevinden. In dit voor beeld zijn het stuur programma 10.179.0.6 en de uitvoerende 10.179.0.4s en 10.179.0.5. Uw IP-adressen kunnen verschillen.

    ![Azure Databricks Spark UI-uitvoerendeers](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u het artikel hebt doorgenomen, kunt u het cluster beëindigen. Dit doet u door vanuit de Azure Databricks-werkruimte in het linkerdeelvenster **Clusters** te selecteren. Voor het cluster dat u wilt beëindigen, plaatst u de cursor op het weglatingsteken onder de kolom **Acties** en selecteert u het **beëindigingspictogram**. Hiermee wordt het cluster gestopt.

Als u het cluster niet handmatig beëindigt, stopt het cluster automatisch, op voorwaarde dat het selectievakje **Beëindigen na \_\_ minuten inactiviteit** is ingeschakeld tijdens het maken van het cluster. In dat geval stopt het cluster automatisch als het gedurende de opgegeven tijd inactief is geweest.

Als u het cluster niet opnieuw wilt gebruiken, kunt u de resource groep die u hebt gemaakt in de Azure Portal verwijderen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Spark-cluster gemaakt in Azure Databricks dat u hebt geïmplementeerd in een virtueel netwerk. Ga naar het volgende artikel voor meer informatie over het opvragen van een SQL Server Linux-docker-container in het virtuele netwerk met behulp van JDBC vanuit een Azure Databricks-notebook.  

> [!div class="nextstepaction"]
>[Een SQL Server Linux docker-container in een virtueel netwerk doorzoeken van een Azure Databricks notebook](vnet-injection-sql-server.md)
