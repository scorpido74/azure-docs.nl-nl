---
title: Gegevens migreren naar Azure Cosmos DB Cassandra-API-account met behulp van Realtimeplatform
description: Meer informatie over het gebruik van Realtimeplatform voor het migreren van gegevens van een Oracle-Data Base naar een Azure Cosmos DB Cassandra-API-account.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 39427ac12dc6214630d6c3e5ace62692b1ea30b6
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003073"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Gegevens migreren naar Azure Cosmos DB Cassandra-API-account met behulp van Realtimeplatform

De Realtimeplatform-installatie kopie in de Azure Marketplace biedt continue realtime gegevens verplaatsing van data warehouses en data bases naar Azure. Tijdens het verplaatsen van de gegevens kunt u in-line denormalisatie, gegevens transformatie, realtime analyses en scenario's voor gegevens rapportage uitvoeren. Het is eenvoudig om aan de slag te gaan met Realtimeplatform om de Bedrijfs gegevens voortdurend te verplaatsen naar Azure Cosmos DB Cassandra-API. Azure biedt een Marketplace-aanbieding die het eenvoudig maakt om Realtimeplatform te implementeren en gegevens te migreren naar Azure Cosmos DB. 

In dit artikel wordt beschreven hoe u Realtimeplatform kunt gebruiken om gegevens uit een **Oracle-data base** te migreren naar een **Azure Cosmos DB Cassandra-API-account**.

## <a name="prerequisites"></a>Vereisten

* Als u geen [Azure-abonnement](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing) hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

* Een Oracle-data base die on-premises wordt uitgevoerd met een aantal gegevens.

## <a name="deploy-the-striim-marketplace-solution"></a>De Realtimeplatform Marketplace-oplossing implementeren

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. Selecteer **een resource maken** en zoek naar **realtimeplatform** in azure Marketplace. Selecteer de eerste optie en **Maak**deze.

   ![Realtimeplatform Marketplace-item zoeken](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Voer vervolgens de configuratie-eigenschappen van de Realtimeplatform-instantie in. De Realtimeplatform-omgeving wordt geïmplementeerd op een virtuele machine. Voer in het deel venster **basis beginselen** de **VM-gebruikers naam**, het **VM-wacht woord** in (dit wacht woord wordt gebruikt voor SSH naar de virtuele machine). Selecteer uw **abonnement**, **resource groep**en **locatie gegevens** waar u realtimeplatform wilt implementeren. Wanneer u klaar bent, selecteert u **OK**.

   ![Basis instellingen voor Realtimeplatform configureren](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. Kies in het deel venster **Realtimeplatform cluster instellingen** het type van de realtimeplatform-implementatie en de grootte van de virtuele machine.

   |Instelling | Value | Description |
   | ---| ---| ---|
   |Implementatie type realtimeplatform |Zelfstandig | Realtimeplatform kan worden uitgevoerd in een **zelfstandige** of **cluster** implementatie type. In de zelfstandige modus wordt de Realtimeplatform-server op één virtuele machine geïmplementeerd en kunt u de grootte van de virtuele machines selecteren, afhankelijk van uw gegevens volume. De cluster modus implementeert de Realtimeplatform-server op twee of meer virtuele machines met de geselecteerde grootte. Cluster omgevingen met meer dan twee knoop punten bieden automatische hoge Beschik baarheid en failover.</br></br> In deze zelf studie kunt u de optie zelfstandige selecteren. Gebruik de standaard VM met de grootte ' Standard_F4s '. | 
   | Naam van het Realtimeplatform-cluster|    < Striim_cluster_Name >|  De naam van het Realtimeplatform-cluster.|
   | Realtimeplatform-cluster wachtwoord|   < Striim_cluster_password >|  Het wacht woord voor het cluster.|

   Nadat u het formulier hebt ingevuld, selecteert u **OK** om door te gaan.

1. Configureer in het deel venster **realtimeplatform toegangs instellingen** het **open bare IP-adres** (Kies de standaard waarden), de **domein naam voor realtimeplatform**, het **beheerders wachtwoord** dat u wilt gebruiken om u aan te melden bij de realtimeplatform-gebruikers interface. Een VNET en subnet configureren (Kies de standaard waarden). Nadat u de details hebt ingevuld, selecteert u **OK** om door te gaan.

   ![Realtimeplatform-toegangs instellingen](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. De implementatie wordt door Azure gevalideerd en er wordt gecontroleerd of alles goed werkt. het duurt enkele minuten voordat de validatie is voltooid. Nadat de validatie is voltooid, selecteert u **OK**.
  
1. Controleer ten slotte de gebruiks voorwaarden en selecteer **maken** om uw realtimeplatform-exemplaar te maken. 

## <a name="configure-the-source-database"></a>De bron database configureren

In deze sectie configureert u de Oracle-Data Base als bron voor het verplaatsen van gegevens.  U hebt het [Oracle JDBC-stuur programma](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) nodig om verbinding te maken met Oracle. Als u wijzigingen wilt lezen vanuit de Oracle-bron database, kunt u de [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) of de [XStream-api's](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)gebruiken. Het Oracle JDBC-stuur programma moet aanwezig zijn in het Java-klassenpad van Realtimeplatform om gegevens van Oracle Data Base te lezen, schrijven of persistent te maken.

Down load het [ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) -stuur programma op uw lokale computer. U installeert deze later in het Realtimeplatform-cluster.

## <a name="configure-target-database"></a>Doel database configureren

In deze sectie configureert u de Azure Cosmos DB Cassandra-API account als doel voor het verplaatsen van gegevens.

1. Een [Azure Cosmos DB Cassandra-API-account](create-cassandra-dotnet.md#create-a-database-account) maken met behulp van de Azure Portal.

1. Ga naar het **Data Explorer** deel venster in uw Azure Cosmos-account. Selecteer **nieuwe tabel** om een nieuwe container te maken. Stel dat u *producten* migreert en gegevens van Oracle Data Base *rangschikt* naar Azure Cosmos db. Maak een nieuwe sleutel ruimte met de naam **StriimDemo** met een container orders. De container inrichten met **1000 RUs**(in dit voor beeld wordt 1000 RUs gebruikt, maar u moet de door Voer geschatte voor uw werk belasting gebruiken) en **/ORDER_ID** als de primaire sleutel. Deze waarden variëren afhankelijk van de bron gegevens. 

   ![Cassandra-API account maken](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Oracle configureren voor Azure Cosmos DB gegevens stroom

1. Nu gaan we terug naar Realtimeplatform. Voordat u met Realtimeplatform werkt, installeert u het Oracle JDBC-stuur programma dat u eerder hebt gedownload.

1. Navigeer naar het Realtimeplatform-exemplaar dat u hebt geïmplementeerd in de Azure Portal. Selecteer de knop **verbinden** in de bovenste menu balk en klik op het tabblad **SSH** , kopieer het veld URL in **Aanmelden met een lokaal VM-account** .

   ![De SSH-URL ophalen](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Open een nieuw terminal venster en voer de SSH-opdracht uit die u hebt gekopieerd uit de Azure Portal. In dit artikel wordt gebruikgemaakt van een terminal in een MacOS, kunt u de vergelijk bare instructies volgen met behulp van PuTTy of een andere SSH-client op een Windows-computer. Wanneer u hierom wordt gevraagd, typt u **Ja** om door te gaan en voert u het **wacht woord** in dat u in de vorige stap hebt ingesteld voor de virtuele machine.

   ![Verbinding maken met Realtimeplatform VM](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Open nu een nieuw tabblad Terminal om het bestand **ojdbc8. jar** te kopiëren dat u eerder hebt gedownload. Gebruik de volgende SCP-opdracht om het jar-bestand van uw lokale computer te kopiëren naar de map tmp van het Realtimeplatform-exemplaar dat wordt uitgevoerd in Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Kopieer het jar-bestand van de locatie machine naar Realtimeplatform](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Ga vervolgens terug naar het venster waar u SSH hebt naar het Realtimeplatform-exemplaar en meld u aan als sudo. Verplaats het bestand **ojdbc8. jar** van de **map/tmp** -map naar de map **lib** van uw realtimeplatform-exemplaar met de volgende opdrachten:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Het jar-bestand naar de map lib verplaatsen](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Start vanuit hetzelfde Terminal venster de Realtimeplatform-server door de volgende opdrachten uit te voeren:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Het duurt enkele minuten voordat realtimeplatform is opgestart. Als u de status wilt zien, voert u de volgende opdracht uit: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Ga nu terug naar Azure en kopieer het open bare IP-adres van uw Realtimeplatform-VM. 

   ![IP-adres van Realtimeplatform-VM kopiëren](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Als u naar de Web-UI van de Realtimeplatform wilt gaan, opent u een nieuw tabblad in een browser en kopieert u het open bare IP-adres, gevolgd door: 9080. Meld u aan met de gebruikers naam van de **beheerder** , samen met het beheerders wachtwoord dat u hebt opgegeven in de Azure Portal.

   ![Aanmelden bij Realtimeplatform](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. U ontvangt nu de start pagina van Realtimeplatform. Er zijn drie verschillende deel Vensters: **Dash boards**, **apps**en **SourcePreview**. In het deel venster Dash boards kunt u gegevens in realtime verplaatsen en visualiseren. Het deel venster apps bevat uw gegevens pijplijnen voor streamen of gegevens stromen. Aan de rechter kant van de pagina is SourcePreview waar u een voor beeld van uw gegevens kunt bekijken voordat u deze verplaatst.

1. Het deel venster **apps** selecteren. we richten zich nu op dit deel venster. Er zijn diverse voor beeld-apps die u kunt gebruiken om meer te weten te komen over Realtimeplatform, maar in dit artikel maakt u zelf een eigen sjabloon. Selecteer de knop **app toevoegen** in de rechter bovenhoek.

   ![De Realtimeplatform-app toevoegen](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Er zijn een aantal verschillende manieren om Realtimeplatform-toepassingen te maken. Selecteer **beginnen met een nieuwe begin datum** voor dit scenario.

   ![De app helemaal opnieuw starten](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. Geef een beschrijvende naam op voor uw toepassing, iets zoals **oraToCosmosDB** en selecteer **Opslaan**.

   ![Een nieuwe toepassing maken](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. U ontvangt de flow Designer, waar u de box-connectors kunt slepen en neerzetten om uw streaming-toepassingen te maken. Typ **Oracle** in de zoek balk, sleep en zet de **Oracle CDC** -bron neer op het canvas van de app.  

   ![Oracle CDC-bron](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. Voer de bron configuratie-eigenschappen van uw Oracle-exemplaar in. De bron naam is slechts een naamgevings Conventie voor de Realtimeplatform-toepassing, u kunt een naam gebruiken zoals **src_onPremOracle**. Geef ook andere gegevens op, zoals het adapter type, de verbindings-URL, de gebruikers naam, het wacht woord en de tabel naam. Selecteer **Opslaan** om door te gaan.

   ![Bron parameters configureren](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. Klik nu op het pictogram Wave van de stroom om de doel Azure Cosmos DB instantie te verbinden. 

   ![Verbinding maken met doel](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. Voordat u het doel configureert, moet u ervoor zorgen dat u een [Baltimore-basis certificaat hebt toegevoegd aan de Java-omgeving van realtimeplatform](/java/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store).

1. Voer de configuratie-eigenschappen van uw doel Azure Cosmos DB exemplaar in en selecteer **Opslaan** om door te gaan. Hier volgen de belangrijkste para meters die u moet weten:

   * **Adapter** : gebruik **DatabaseWriter**. Wanneer u naar Azure Cosmos DB Cassandra-API schrijft, is DatabaseWriter vereist. Het Cassandra-stuur programma 3.6.0 is gebundeld met Realtimeplatform. Als de DatabaseWriter groter is dan het aantal dat is ingericht in de Azure Cosmos-container, zal de toepassing vastlopen.

   * **Gebruikers naam** : Geef de naam van uw Azure Cosmos-account op.
   
   * **Wacht woord** : Geef de primaire sleutel van uw Azure Cosmos-account op.

   * **Tabellen** -doel tabellen moeten primaire sleutels hebben en primaire sleutels kunnen niet worden bijgewerkt.

   ![Doel eigenschappen configureren](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![Doel eigenschappen configureren](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. Nu gaan we de Realtimeplatform-toepassing uitvoeren. Selecteer in de bovenste menu balk **gemaakt**en implementeer vervolgens de **app**. In het venster implementatie kunt u opgeven of u bepaalde onderdelen van uw toepassing wilt uitvoeren op specifieke delen van uw implementatie topologie. Omdat we in een eenvoudige implementatie topologie worden uitgevoerd via Azure, gebruiken we de standaard optie.

   ![De app implementeren](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. Nu gaan we een voor beeld van de stroom bekijken om gegevens te zien die via de Realtimeplatform zijn getransporteerd. Klik op het pictogram Wave en klik op het oogpictogram ernaast. Na de implementatie kunt u een voor beeld van de stroom bekijken om de gegevens stroom te bekijken. Selecteer het pictogram **Wave** en de **Eyeball** ernaast. Selecteer de **geïmplementeerde** knop in de bovenste menu balk en selecteer **app starten**.

   ![De app starten](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. Met behulp van een **CDC-lezer (Change Data Capture)** worden door realtimeplatform alleen nieuwe wijzigingen in de data base opgehaald. Als u gegevens in uw bron tabellen doorloopt, wordt deze weer gegeven. Omdat dit echter een voorbeeld tabel is, is dit de bron die niet is verbonden met een toepassing. Als u een voor beeld van een gegevens generator gebruikt, kunt u een keten van gebeurtenissen invoegen in uw Oracle-data base.

1. U ziet gegevens stromen via het Realtimeplatform-platform. Realtimeplatform haalt ook alle meta gegevens op die zijn gekoppeld aan uw tabel. Dit is handig om de gegevens te controleren en ervoor te zorgen dat de gegevens worden gelandd op het juiste doel.

   ![De CDC-pijp lijn instellen](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. Tot slot gaan we aanmelden bij Azure en naar uw Azure Cosmos-account gaan. Vernieuw het Data Explorer en u kunt zien dat de gegevens zijn aangekomen. 

Door gebruik te maken van de Realtimeplatform-oplossing in azure, kunt u voortdurend gegevens migreren naar Azure Cosmos DB van verschillende bronnen, zoals Oracle, Cassandra, MongoDB en diverse andere, naar Azure Cosmos DB. Ga voor meer informatie naar de [realtimeplatform-website](https://www.striim.com/), [down load een gratis proef versie van 30 dagen van realtimeplatform](https://go2.striim.com/download-free-trial)en voor eventuele problemen bij het instellen van het migratie traject met realtimeplatform, een [ondersteunings aanvraag indienen.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Volgende stappen

* Als u gegevens migreert naar Azure Cosmos DB SQL-API, raadpleegt u [gegevens migreren naar Cassandra-API account met behulp van realtimeplatform](cosmosdb-sql-api-migrate-data-striim.md)

* [Uw gegevens controleren en fouten opsporen met Azure Cosmos DB meet waarden](use-metrics.md)
