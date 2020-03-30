---
title: Gegevens migreren naar Azure Cosmos DB SQL API-account met Striim
description: Meer informatie over het gebruik van Striim om gegevens van een Oracle-database te migreren naar een Azure Cosmos DB SQL API-account.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 36ba9e2d3385184f32876a6d067b58f7c21a90bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71003284"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Gegevens migreren naar Azure Cosmos DB SQL API-account met Striim
 
De Striim-afbeelding in de Azure-markt biedt continue realtime gegevensverkeer van datawarehouses en databases naar Azure. Tijdens het verplaatsen van de gegevens u in-line denormalisatie, gegevenstransformatie, real-time analyses en scenario's voor gegevensrapportage uitvoeren. Het is gemakkelijk om aan de slag te gaan met Striim om bedrijfsgegevens continu te verplaatsen naar Azure Cosmos DB SQL API. Azure biedt een marktplaatsaanbod waarmee het eenvoudig is om Striim te implementeren en gegevens te migreren naar Azure Cosmos DB. 

In dit artikel ziet u hoe u Striim gebruikt om gegevens van een **Oracle-database** te migreren naar een **Azure Cosmos DB SQL API-account.**

## <a name="prerequisites"></a>Vereisten

* Als u geen [Azure-abonnement](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

* Een Oracle-database die on-premises wordt uitgevoerd met enkele gegevens erin.

## <a name="deploy-the-striim-marketplace-solution"></a>De Striim-marktoplossing implementeren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer **Een resource maken** en zoeken naar **Striim** in de Azure-marktplaats. Selecteer de eerste optie **en Maak**.

   ![Striim-marktplaatsobject zoeken](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. Voer vervolgens de configuratie-eigenschappen van de instantie Striim in. De Striim-omgeving wordt geïmplementeerd in een virtuele machine. Voer in het deelvenster **Basisbeginselen** de **VM-gebruikersnaam**, **VM-wachtwoord** in (dit wachtwoord wordt gebruikt om SSH in de VM te gebruiken). Selecteer uw **abonnements-,** **resourcegroep-** en **locatiegegevens** waar u Striim wilt implementeren. Zodra u klaar bent, selecteert u **OK**.

   ![Basisinstellingen voor Striim configureren](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)

1. Kies in het deelvenster **Striim-clusterinstellingen** het type Striim-implementatie en de grootte van de virtuele machine.

   |Instelling | Waarde | Beschrijving |
   | ---| ---| ---|
   |Striim-implementatietype |Zelfstandig | Striim kan worden uitgevoerd in een **zelfstandige** of **clusterimplementatietypen.** De zelfstandige modus implementeert de Striim-server op één virtuele machine en u de grootte van de VM's selecteren, afhankelijk van uw gegevensvolume. De clustermodus implementeert de Striim-server op twee of meer VM's met de geselecteerde grootte. Clusteromgevingen met meer dan 2 knooppunten bieden automatische hoge beschikbaarheid en failover.</br></br> In deze zelfstudie u de optie Zelfstandig selecteren. Gebruik de standaard VM van de vm van de Standard_F4s.Use the default "Standard_F4s" size VM.  | 
   | Naam van het Cluster Striim|    <Striim_cluster_Name>|  Naam van het cluster Striim.|
   | Striim-clusterwachtwoord|   <Striim_cluster_password> <|  Wachtwoord voor het cluster.|

   Nadat u het formulier hebt ingevuld, selecteert u **OK** om door te gaan.

1. Configureer in het deelvenster **Striim-toegangsinstellingen** het **openbare IP-adres** (kies de standaardwaarden), **Domeinnaam voor Striim**, **Beheerderswachtwoord** dat u wilt gebruiken om u aan te sluiten bij de Striim-gebruikersinterface. Een VNET en Subnet configureren (kies de standaardwaarden). Nadat u de details hebt ingevuld, selecteert u **OK** om door te gaan.

   ![Striim-toegangsinstellingen](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure valideert de implementatie en zorgt ervoor dat alles er goed uitziet; validatie duurt enkele minuten om te voltooien. Nadat de validatie is voltooid, selecteert u **OK**.
  
1. Bekijk ten slotte de gebruiksvoorwaarden en selecteer **Maken** om uw Striim-exemplaar te maken. 

## <a name="configure-the-source-database"></a>De brondatabase configureren 

In deze sectie configureert u de Oracle-database als bron voor gegevensverplaatsing.  U hebt het [Oracle JDBC-stuurprogramma](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) nodig om verbinding te maken met Oracle. Als u wijzigingen uit uw bron-Oracle-database wilt lezen, u de [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) of de [XStream-API's](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)gebruiken. De Oracle JDBC-driver moet aanwezig zijn op het Java-klassepad van Striim om gegevens uit de Oracle-database te lezen, schrijven of te blijven gebruiken.

Download de [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) driver op uw lokale machine. U installeert het later in het Striim-cluster.

## <a name="configure-the-target-database"></a>De doeldatabase configureren

In deze sectie configureert u het Azure Cosmos DB SQL API-account als het doel voor gegevensverplaatsing.

1. Maak een [Azure Cosmos DB SQL API-account](create-cosmosdb-resources-portal.md) met behulp van de Azure-portal.

1. Navigeer naar het deelvenster **Gegevensverkenner** in uw Azure Cosmos-account. Selecteer **Nieuwe container** om een nieuwe container te maken. Ga ervan uit dat u *producten* en *ordergegevens* migreert van Oracle-database naar Azure Cosmos DB. Maak een nieuwe database met de naam **StriimDemo** met een container met de naam **Orders**. De container inrichten met **1000 RU's** (in dit voorbeeld worden 1000 R's gebruikt, maar u moet de geschatte doorvoer voor uw werkbelasting gebruiken) en **/ORDER_ID** als partitiesleutel. Deze waarden verschillen afhankelijk van uw brongegevens. 

   ![SQL API-account maken](./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Oracle configureren in Azure Cosmos DB-gegevensstroom

1. Laten we terug naar Striim gaan. Voordat u interactie hebt met Striim, installeert u het Oracle JDBC-stuurprogramma dat u eerder hebt gedownload.

1. Navigeer naar het Striim-exemplaar dat u hebt geïmplementeerd in de Azure-portal. Selecteer de knop **Verbinding maken** in de bovenste menubalk en kopieer de URL op het tabblad **SSH** in **Aanmelding met het lokale vm-accountveld.**

   ![De SSH-URL ophalen](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Open een nieuw terminalvenster en voer de SSH-opdracht uit die u hebt gekopieerd van de Azure-portal. Dit artikel maakt gebruik van terminal in een MacOS, u de soortgelijke instructies met behulp van PuTTY of een andere SSH-client op een Windows-machine. Wanneer u wordt gevraagd, typt u **ja** om door te gaan en voert u het **wachtwoord** in dat u in de vorige stap voor de virtuele machine hebt ingesteld.

   ![Verbinding maken met Striim VM](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Open nu een nieuw terminaltabblad om het **ojdbc8.jar-bestand** te kopiëren dat u eerder hebt gedownload. Gebruik de volgende opdracht SCP om het jar-bestand van uw lokale machine te kopiëren naar de tmp-map van de Striim-instantie die in Azure wordt uitgevoerd:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Het Jar-bestand kopiëren van locatiemachine naar Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. Navigeer vervolgens terug naar het venster waar u SSH hebt gedaan naar de Striim-instantie en Log in als sudo. Verplaats het **ojdbc8.jar-bestand** van de **/tmp-map** naar de **libdirectory** van uw Striim-instantie met de volgende opdrachten:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Het jar-bestand verplaatsen naar libmap](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. Start vanuit hetzelfde terminalvenster de Striim-server opnieuw door de volgende opdrachten uit te voeren:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim zal een minuut duren om op te starten. Als u de status wilt zien, voert u de volgende opdracht uit: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Navigeer nu terug naar Azure en kopieer het openbare IP-adres van uw Striim VM. 

   ![Striim VM-IP-adres kopiëren](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Als u naar de webgebruikersinterface van de Striim wilt navigeren, opent u een nieuw tabblad in een browser en kopieert u het openbare IP-adres, gevolgd door: 9080. Meld u aan met de gebruikersnaam van de **beheerder,** samen met het beheerderswachtwoord dat u hebt opgegeven in de Azure-portal.

   ![Log hier in](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Nu kom je op striim's startpagina. Er zijn drie verschillende deelvensters : **Dashboards,** **Apps**en **SourcePreview**. Met het deelvenster Dashboards u gegevens in realtime verplaatsen en visualiseren. Het deelvenster Apps bevat uw pijplijnen voor streaminggegevens of gegevensstromen. Aan de rechterkant van de pagina bevindt zich SourcePreview waar u een voorbeeld van uw gegevens bekijken voordat u deze verplaatst.

1. Selecteer het deelvenster **Apps,** we richten ons voorlopig op dit deelvenster. Er zijn een verscheidenheid van voorbeeldapps die u gebruiken om over Striim te leren, nochtans in dit artikel zult u onze creëren. Selecteer de knop **App toevoegen** in de rechterbovenhoek.

   ![De Striim-app toevoegen](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Er zijn een paar verschillende manieren om Striim-toepassingen te maken. Selecteer **Begin met sjabloon** om te beginnen met een bestaande sjabloon.

   ![De app starten met de sjabloon](./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png)

1. Typ in het veld **Zoeksjablonen** 'Cosmos' en selecteer **Target: Azure Cosmos DB** en selecteer Vervolgens Oracle CDC in Azure Cosmos **DB**.

   ![Selecteer Oracle CDC naar Cosmos DB](./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png)

1. Geef op de volgende pagina een naam aan uw toepassing. U een naam opgeven zoals **oraToCosmosDB** en vervolgens **Opslaan selecteren.**

1. Voer vervolgens de bronconfiguratie van uw bronOracle-exemplaar in. Voer een waarde in voor de **bronnaam**. De bronnaam is slechts een naamgevingsconventie voor de Striim-toepassing, u iets als **src_onPremOracle**gebruiken. Voer waarden in voor de rest van de **URL**van de bronparameters , **Gebruikersnaam**, **Wachtwoord**, kies **LogMiner** als lezer om gegevens van Oracle te lezen. Selecteer **Volgende** om door te gaan.

   ![Bronparameters configureren](./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png)

1. Striim controleert uw omgeving en zorgt ervoor dat deze verbinding kan maken met uw bron-Oracle-instantie, de juiste bevoegdheden heeft en dat CDC correct is geconfigureerd. Zodra alle waarden zijn gevalideerd, selecteert u **Volgende**.

   ![Bronparameters valideren](./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png)

1. Selecteer de tabellen uit de Oracle-database die u wilt migreren. Laten we bijvoorbeeld de tabel Orders kiezen en **Volgende**selecteren. 

   ![Brontabellen selecteren](./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png)

1. Nadat u de brontabel hebt geselecteerd, u complexere bewerkingen uitvoeren, zoals toewijzing en filtering. In dit geval maakt u alleen een replica van uw brontabel in Azure Cosmos DB. Selecteer dus **Volgende** om het doel te configureren

1. Laten we nu het doel configureren:

   * **Doelnaam** - Geef een vriendelijke naam op voor het doel. 
   * **Input From** - Selecteer in de vervolgkeuzelijst de invoerstroom van de invoerstroom die u hebt gemaakt in de bronconfiguratie van Oracle. 
   * **Verzamelingen**: voer de doeleigenschappen van Azure Cosmos DB-configuratie in. De syntaxis van verzamelingen is **SourceSchema.SourceTable, TargetDatabase.TargetContainer**. In dit voorbeeld is de waarde "SYSTEEM. ORDERS, StriimDemo.Orders". 
   * **AccessKey** - De primaire sleutel van uw Azure Cosmos-account.
   * **ServiceEndpoint** - De URI van uw Azure Cosmos-account, ze kunnen worden gevonden onder het gedeelte **Sleutels** van de Azure-portal. 

   Selecteer **Opslaan** en **volgende**.

   ![Doelparameters configureren](./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png)


1. Vervolgens kom je bij de flowdesigner, waar je connectors slepen en uit de doos zetten om je streamingtoepassingen te maken. U zult geen wijzigingen aanbrengen in de stroom op dit punt. Dus ga je gang en implementeer de toepassing door de knop **App implementeren te** selecteren.
 
   ![De app implementeren](./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png)

1. In het implementatievenster u opgeven of u bepaalde delen van uw toepassing wilt uitvoeren op specifieke delen van uw implementatietopologie. Omdat we in een eenvoudige implementatietopologie via Azure worden uitgevoerd, gebruiken we de standaardoptie.

   ![De standaardoptie gebruiken](./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png)

1. Na het implementeren u een voorbeeld van de stream bekijken om gegevens te zien die doorstromen. Selecteer het **golfpictogram** en de oogbol ernaast. Selecteer de **knop Geïmplementeerd** in de bovenste menubalk en selecteer **App starten**.

   ![De app starten](./media/cosmosdb-sql-api-migrate-data-striim/start-app.png)

1. Door het gebruik van een **CDC (Change Data Capture)** lezer, zal Striim pick-up alleen nieuwe wijzigingen in de database. Als u gegevens door uw brontabellen hebt gestroomd, ziet u deze. Aangezien dit echter een demotabel is, is de bron niet verbonden met een toepassing. Als u een voorbeeldgegevensgenerator gebruikt, u een reeks gebeurtenissen in voegen in uw Oracle-database.

1. U ziet gegevens die door het Striim-platform stromen. Striim pikt ook alle metadata op die aan uw tabel zijn gekoppeld, wat handig is om de gegevens te controleren en ervoor te zorgen dat de gegevens op het juiste doel terecht komen.

   ![CDC-pijplijn configureren](./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png)

1. Tot slot melden we ons aan bij Azure en navigeerjenaar uw Azure Cosmos-account. Vernieuw de Gegevensverkenner en u zien dat de gegevens zijn aangekomen.  

   ![Gemigreerde gegevens valideren in Azure](./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png)

Door de Striim-oplossing in Azure te gebruiken, u gegevens voortdurend migreren naar Azure Cosmos DB uit verschillende bronnen zoals Oracle, Cassandra, MongoDB en diverse anderen naar Azure Cosmos DB. Voor meer informatie u terecht op de [Striim website](https://www.striim.com/), [download een gratis 30-daagse trial van Striim](https://go2.striim.com/download-free-trial), en voor eventuele problemen bij het opzetten van het migratiepad met Striim, bestand een [ondersteuningsverzoek.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Volgende stappen

* Als u gegevens migreert naar Azure Cosmos DB SQL API, raadpleegt u [hoe u gegevens migreert naar cassandra-API-account met Striim](cosmosdb-cassandra-api-migrate-data-striim.md)

* [Uw gegevens bewaken en debuggen met Azure Cosmos DB-statistieken](use-metrics.md)
