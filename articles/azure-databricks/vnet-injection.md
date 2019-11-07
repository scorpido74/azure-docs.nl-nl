---
title: Azure Databricks implementeren in uw virtuele netwerk
description: In dit artikel wordt beschreven hoe u Azure Databricks implementeert in uw virtuele netwerk, ook wel bekend als VNet-injectie.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 5eded3217e96ccc45951acae004d1424e16cb098
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605669"
---
# <a name="deploy-azure-databricks-in-your-virtual-network"></a>Azure Databricks implementeren in uw virtuele netwerk

De standaard implementatie van Azure Databricks is een volledig beheerde service in Azure: alle gegevens vlak resources, waaronder een virtueel netwerk (VNet), worden geïmplementeerd naar een vergrendelde resource groep. Als u netwerk aanpassing nodig hebt, kunt u echter Azure Databricks-resources in uw eigen virtuele netwerk implementeren (ook wel VNet-injectie genoemd), wanneer u het volgende kunt doen:

* Verbind Azure Databricks met andere Azure-Services (zoals Azure Storage) op een veiligere manier met behulp van service-eind punten.
* Maak verbinding met on-premises gegevens bronnen voor gebruik met Azure Databricks en profiteer van door de gebruiker gedefinieerde routes.
* Azure Databricks verbinding maken met een virtueel netwerk apparaat om al het uitgaande verkeer te controleren en acties uit te voeren op basis van regels voor toestaan en weigeren.
* Configureer Azure Databricks voor het gebruik van aangepaste DNS.
* Configureer NSG-regels (netwerk beveiligings groep) om beperkingen voor uitgaand verkeer op te geven.
* Implementeer Azure Databricks clusters in uw bestaande virtuele netwerk.

Als u Azure Databricks-resources in uw eigen virtuele netwerk implementeert, kunt u ook gebruikmaken van flexibele CIDR-bereiken (overal tussen/16-/24 voor het virtuele netwerk en tussen/18-/26 voor de subnetten).

  > [!NOTE]
  > U kunt het virtuele netwerk voor een bestaande werk ruimte niet vervangen. Als uw huidige werk ruimte niet het vereiste aantal actieve cluster knooppunten mag bevatten, maakt u een andere werk ruimte in een groter virtueel netwerk. Volg [deze gedetailleerde migratie stappen](howto-regional-disaster-recovery.md#detailed-migration-steps) voor het kopiëren van resources (notebooks, cluster configuraties, Jobs) van de oude naar de nieuwe werk ruimte.

## <a name="virtual-network-requirements"></a>Vereisten voor virtuele netwerken

U kunt de Azure Databricks werkruimte implementatie-interface in de Azure Portal gebruiken om automatisch een bestaand virtueel netwerk te configureren met de vereiste subnetten, netwerk beveiligings groep en white list-instellingen, of u kunt Azure Resource Manager gebruiken sjablonen voor het configureren van uw virtuele netwerk en het implementeren van uw werk ruimte.

Het virtuele netwerk waarvoor u uw Azure Databricks-werk ruimte implementeert, moet voldoen aan de volgende vereisten:

### <a name="location"></a>Locatie

Het virtuele netwerk moet zich op dezelfde locatie bevinden als de Azure Databricks-werk ruimte.

### <a name="subnets"></a>Subnetten

Het virtuele netwerk moet twee subnetten bevatten die zijn toegewezen aan Azure Databricks:

   1. Een privé subnet met een geconfigureerde netwerk beveiligings groep die cluster-interne communicatie toestaat

   2. Een openbaar subnet met een geconfigureerde netwerk beveiligings groep waarmee communicatie met het Azure Databricks besturings vlak is toegestaan.

### <a name="address-space"></a>Adresruimte

Een CIDR-blok tussen/16-/24 voor het virtuele netwerk en een CIDR-blok tussen/18-/26 voor de privé-en open bare subnetten.

### <a name="whitelisting"></a>Goedkeuring

Alle uitgaand en binnenkomend verkeer tussen de subnetten en het Azure Databricks besturings vlak moet white list zijn.

## <a name="create-an-azure-databricks-workspace"></a>Een Azure Databricks-werkruimte maken

In deze sectie wordt beschreven hoe u een Azure Databricks werkruimte maakt in de Azure Portal en deze implementeert in uw eigen bestaande virtuele netwerk. Azure Databricks werkt het virtuele netwerk bij met twee nieuwe subnetten en netwerk beveiligings groepen met behulp van CIDR-bereiken die door u zijn meegeleverd, whitelists binnenkomend en uitgaand subnet verkeer en implementeert de werk ruimte in het bijgewerkte virtuele netwerk.

## <a name="prerequisites"></a>Vereisten

U moet een virtueel netwerk hebben waarop u de Azure Databricks-werk ruimte gaat implementeren. U kunt een bestaand virtueel netwerk gebruiken of een nieuwe maken, maar het virtuele netwerk moet zich in dezelfde regio bevinden als de Azure Databricks werk ruimte die u wilt maken. Een CIDR-bereik tussen/16-/24 is vereist voor het virtuele netwerk.

  > [!Warning]
  > Een werk ruimte met een kleiner virtueel netwerk (dat wil zeggen, een lager CIDR-bereik) kan sneller IP-adressen (netwerk ruimte) uitvoeren dan een werk ruimte met een groter virtueel netwerk. Een werk ruimte met een/24 virtuele netwerk en/26 subnetten kan bijvoorbeeld Maxi maal 64 knoop punten actief zijn, terwijl een werk ruimte met een/20 virtueel netwerk en/22 subnetten Maxi maal 1024 knoop punten kan hebben.

  Uw subnetten worden automatisch gemaakt wanneer u uw werk ruimte configureert en u krijgt de mogelijkheid om het CIDR-bereik voor de subnetten tijdens de configuratie op te geven.

## <a name="configure-the-virtual-network"></a>Het virtuele netwerk configureren

1. Selecteer in de Azure Portal **+ een resource maken > Analytics > Azure Databricks** om het dialoog venster Azure Databricks service te openen.

2. Volg de configuratie stappen die worden beschreven in stap 2: een Azure Databricks-werk ruimte maken in de aan de slag-hand leiding en selecteer de werk ruimte Azure Databricks implementeren in uw Virtual Network optie.

   ![Azure Databricks-service maken](./media/vnet-injection/create-databricks-service.png)

3. Selecteer het virtuele netwerk dat u wilt gebruiken.

   ![Opties voor het virtuele netwerk](./media/vnet-injection/select-vnet.png)

4. Geef CIDR-bereiken op in een blok tussen/18-/26 voor twee subnetten, toegewezen aan Azure Databricks:

   * Een openbaar subnet wordt gemaakt met een gekoppelde netwerk beveiligings groep waarmee communicatie met het Azure Databricks besturings vlak is toegestaan.
   * Er wordt een persoonlijk subnet gemaakt met een bijbehorende netwerk beveiligings groep die interne communicatie in het cluster toestaat.

5. Klik op **maken** om de Azure Databricks-werk ruimte te implementeren in het virtuele netwerk.

## <a name="advanced-resource-manager-configurations"></a>Geavanceerde configuratie van Resource Manager

Als u meer controle wilt over de configuratie van het virtuele netwerk, bijvoorbeeld dat u bestaande subnetten wilt gebruiken, bestaande netwerk beveiligings groepen wilt gebruiken of uw eigen beveiligings regels wilt maken, kunt u de volgende Azure Resource Manager-sjablonen gebruiken in plaats van de configuratie van virtuele netwerk Portal en werk ruimte-implementatie.

### <a name="all-in-one"></a>Alles in één

Als u een virtueel netwerk, netwerk beveiligings groepen en Azure Databricks werk ruimte wilt maken, gebruikt u de [sjabloon alles-in-één voor Databricks VNet geïnjecteerde werk ruimten](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Wanneer u deze sjabloon gebruikt, hoeft u geen hand matige white list van het subnet verkeer uit te voeren.

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen

Als u netwerk beveiligings groepen met de vereiste regels voor een bestaand virtueel netwerk wilt maken, gebruikt u de [sjabloon netwerk beveiligings groep voor Databricks VNet-injectie](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/).

Wanneer u deze sjabloon gebruikt, hoeft u geen hand matige white list van het subnet verkeer uit te voeren.

### <a name="virtual-network"></a>Virtueel netwerk

Als u een virtueel netwerk met de juiste open bare en privé-subnetten wilt maken, gebruikt u de [Virtual Network sjabloon voor Databricks VNet-injectie](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection).

Als u deze sjabloon gebruikt zonder ook de netwerk beveiligings groepen sjabloon te gebruiken, moet u hand matig white list-regels toevoegen aan de netwerk beveiligings groepen die u gebruikt met het virtuele netwerk.

### <a name="azure-databricks-workspace"></a>Azure Databricks werk ruimte

Als u een Azure Databricks-werk ruimte wilt implementeren in een bestaand virtueel netwerk met open bare en particuliere subnetten en correct geconfigureerde netwerk beveiligings groepen, gebruikt u de [werkruimte sjabloon voor Databricks VNet-injectie](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection).

Als u deze sjabloon gebruikt zonder ook de netwerk beveiligings groepen sjabloon te gebruiken, moet u hand matig white list-regels toevoegen aan de netwerk beveiligings groepen die u gebruikt met het virtuele netwerk.

## <a name="whitelisting-subnet-traffic"></a>White list-subnet verkeer

Als u de [Azure Portal](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject#vnet-inject-portal) -of [Azure Resource Manager sjablonen](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject.html#vnet-inject-advanced) niet gebruikt om uw netwerk beveiligings groepen te maken, moet u het volgende verkeer hand matig White List op uw subnetten.

|Richting|Protocol|Bron|Bronpoort|Doel|Doelpoort|
|---------|--------|------|-----------|-----------|----------------|
|Inkomend|\*|VirtualNetwork|\*|\*|\*|
|Inkomend|\*|Beheer vlak NAT IP|\*|\*|22|
|Inkomend|\*|Beheer vlak NAT IP|\*|\*|5557|
|Uitgaand|\*|\*|\*|Webapp IP|\*|
|Uitgaand|\*|\*|\*|SQL (servicetag)|\*|
|Uitgaand|\*|\*|\*|Opslag (servicetag)|\*|
|Uitgaand|\*|\*|\*|VirtualNetwork|\*|

White list-subnet verkeer met de volgende IP-adressen. Voor SQL (meta Store) en opslag (artefact-en logboek opslag) moet u de SQL-en Storage- [service Tags](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)gebruiken.

|Azure Databricks regio|Service|Openbare IP|
|-----------------------|-------|---------|
|US - oost|Control-vlak NAT </br></br>Ding|23.101.152.95/32 </br></br>40.70.58.221/32|
|US - oost 2|Control-vlak NAT </br></br>Ding|23.101.152.95/32 </br></br>40.70.58.221/32|
|US - noord-centraal|Control-vlak NAT </br></br>Ding|23.101.152.95/32 </br></br>40.70.58.221/32|
|US - centraal|Control-vlak NAT </br></br>Ding|23.101.152.95/32 </br></br>40.70.58.221/32|
|US - zuid-centraal|Control-vlak NAT </br></br>Ding|40.83.178.242/32 </br></br>40.118.174.12/32|
|US - west|Control-vlak NAT </br></br>Ding|40.83.178.242/32 </br></br>40.118.174.12/32|
|US - west 2|Control-vlak NAT </br></br>Ding|40.83.178.242/32 </br></br>40.118.174.12/32|
|Canada - midden|Control-vlak NAT </br></br>Ding|40.85.223.25/32 </br></br>13.71.184.74/32|
|Canada - oost|Control-vlak NAT </br></br>Ding|40.85.223.25/32 </br></br>13.71.184.74/32|
|Verenigd Koninkrijk West|Control-vlak NAT </br></br>Ding|51.140.203.27/32 </br></br>51.140.204.4/32|
|VK - zuid|Control-vlak NAT </br></br>Ding|51.140.203.27/32 </br></br>51.140.204.4/32|
|Europa -west|Control-vlak NAT </br></br>Ding|23.100.0.135/32 </br></br>52.232.19.246/32|
|Europa - noord|Control-vlak NAT </br></br>Ding|23.100.0.135/32 </br></br>52.232.19.246/32|
|India - centraal|Control-vlak NAT </br></br>Ding|104.211.89.81/32 </br></br>104.211.101.14/32|
|India - zuid|Control-vlak NAT </br></br>Ding|104.211.89.81/32 </br></br>104.211.101.14/32|
|India - west|Control-vlak NAT </br></br>Ding|104.211.89.81/32 </br></br>104.211.101.14/32|
|Azië - zuidoost|Control-vlak NAT </br></br>Ding|52.187.0.85/32 </br></br>52.187.145.107/32|
|Azië - oost|Control-vlak NAT </br></br>Ding|52.187.0.85/32 </br></br>52.187.145.107/32|
|Australië - oost|Control-vlak NAT </br></br>Ding|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australië - zuidoost|Control-vlak NAT </br></br>Ding|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australië - centraal|Control-vlak NAT </br></br>Ding|13.70.105.50/32 </br></br>13.75.218.172/32|
|Australië - centraal 2|Control-vlak NAT </br></br>Ding|13.70.105.50/32 </br></br>13.75.218.172/32|
|Japan - oost|Control-vlak NAT </br></br>Ding|13.78.19.235/32 </br></br>52.246.160.72/32|
|Japan - west|Control-vlak NAT </br></br>Ding|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="workspace-launch-errors"></a>Fouten bij het starten van de werk ruimte

Het starten van een werk ruimte in een aangepast virtueel netwerk mislukt op het Azure Databricks aanmeldings scherm met de volgende fout: **' er is een fout opgetreden bij het maken van de werk ruimte. Controleer of de aangepaste netwerk configuratie juist is en probeer het opnieuw. "**

Deze fout wordt veroorzaakt door een netwerk configuratie die niet aan de vereisten voldoet. Controleer of u de instructies in dit onderwerp hebt gevolgd tijdens het maken van de werk ruimte.

### <a name="cluster-creation-errors"></a>Fouten bij het maken van het cluster

**Onbereikbare instanties: bronnen zijn niet bereikbaar via SSH.**

Mogelijke oorzaak: verkeer van het controle vlak naar werk nemers wordt geblokkeerd. Corrigeer door ervoor te zorgen dat de regels voor inkomende beveiliging voldoen aan de vereisten. Als u implementeert in een bestaand virtueel netwerk dat is verbonden met uw on-premises netwerk, controleert u de instellingen met behulp van de informatie in uw Azure Databricks-werk ruimte koppelen aan uw on-premises netwerk.

**Onverwachte opstart fout: er is een onverwachte fout opgetreden tijdens het instellen van het cluster. Probeer het opnieuw en neem contact op met Azure Databricks als het probleem zich blijft voordoen. Intern fout bericht: time-out tijdens het plaatsen van het knoop punt.**

Mogelijke oorzaak: verkeer van werk nemers naar Azure Storage-eind punten wordt geblokkeerd. Los het probleem op door ervoor te zorgen dat uitgaande beveiligings regels voldoen aan de vereisten. Als u aangepaste DNS-servers gebruikt, controleert u ook de status van de DNS-servers in uw virtuele netwerk.

**Het starten van de Cloud provider is mislukt: er is een fout in de Cloud provider opgetreden tijdens het instellen van het cluster. Raadpleeg de Azure Databricks gids voor meer informatie. Azure-fout code: AuthorizationFailed/InvalidResourceReference.**

Mogelijke oorzaak: het virtuele netwerk of de subnetten bestaan niet meer. Zorg ervoor dat het virtuele netwerk en de subnetten bestaan.

**Het cluster is beëindigd. Reden: Spark-opstart fout: Spark kan niet op tijd worden gestart. Dit probleem kan worden veroorzaakt door een niet-werkende Hive-metastore, ongeldige Spark-configuraties of het initialiseren van init-scripts. Raadpleeg de logboeken van de Spark-Stuur Programma's om dit probleem op te lossen en neem contact op met Databricks als het probleem zich blijft voordoen. Intern fout bericht: kan Spark niet starten: het stuur programma is niet gestart in de tijd.**

Mogelijke oorzaak: de container kan niet communiceren met het hosting-exemplaar of het DBFS-opslag account. Los dit probleem op door een aangepaste route toe te voegen aan de subnetten voor het opslag account DBFS met de volgende hop Internet.

### <a name="notebook-command-errors"></a>Opdracht fouten van notebook

**Opdracht reageert niet**

Mogelijke oorzaak: de communicatie tussen werk nemers en werk nemers wordt geblokkeerd. Corrigeer door ervoor te zorgen dat de regels voor inkomende beveiliging voldoen aan de vereisten.

**Werk stroom van notitie blok mislukt met uitzonde ring: com. databricks. WorkflowException: org. apache. http. verbindingen. ConnectTimeoutException**

Mogelijke oorzaak: verkeer van werk nemers naar Azure Databricks webapp wordt geblokkeerd. Corrigeer door ervoor te zorgen dat de uitgaande beveiligings regels voldoen aan de vereisten.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens uitpakken, transformeren en laden met Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
