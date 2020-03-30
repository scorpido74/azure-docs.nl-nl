---
title: Tijdelijke verbindingsfouten - Azure Database voor MySQL
description: Meer informatie over het omgaan met tijdelijke verbindingsfouten en efficiënt verbinding maken met Azure Database voor MySQL.
keywords: mysql-verbinding, verbindingstekenreeks, verbindingsproblemen, tijdelijke fout, verbindingsfout, efficiënt verbinden
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 79c5c7e485cc9cb03757b8a981cef92d79b81c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537173"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Tijdelijke fouten verwerken en efficiënt verbinding maken met Azure Database voor MySQL

In dit artikel wordt beschreven hoe u tijdelijke fouten verwerken en efficiënt verbinding maken met Azure Database voor MySQL.

## <a name="transient-errors"></a>Tijdelijke fouten

Een tijdelijke fout, ook wel een tijdelijke fout genoemd, is een fout die zichzelf zal oplossen. Meestal manifesteren deze fouten zich als een verbinding met de databaseserver die wordt verwijderd. Ook kunnen nieuwe verbindingen met een server niet worden geopend. Tijdelijke fouten kunnen bijvoorbeeld optreden wanneer er hardware- of netwerkfouten optreden. Een andere reden zou kunnen zijn een nieuwe versie van een PaaS-service die wordt uitgerold. De meeste van deze gebeurtenissen worden automatisch beperkt door het systeem in minder dan 60 seconden. Een aanbevolen manier voor het ontwerpen en ontwikkelen van applicaties in de cloud is het verwachten van tijdelijke fouten. Stel dat ze kunnen gebeuren in elk onderdeel op elk gewenst moment en om de juiste logica op zijn plaats om deze situaties te behandelen.

## <a name="handling-transient-errors"></a>Tijdelijke fouten verwerken

Tijdelijke fouten moeten worden verwerkt met behulp van logica voor opnieuw proberen. Situaties die in aanmerking moeten worden genomen:

* Er treedt een fout op wanneer u een verbinding probeert te openen
* Een niet-actieve verbinding wordt aan de serverzijde verbroken. Wanneer u een opdracht probeert uit te geven, kan deze niet worden uitgevoerd
* Een actieve verbinding die momenteel een opdracht uitvoert, wordt verbroken.

De eerste en tweede zaak zijn vrij rechttoe rechtaan te behandelen. Probeer de verbinding opnieuw te openen. Wanneer u slaagt, is de tijdelijke fout door het systeem beperkt. U uw Azure Database opnieuw gebruiken voor MySQL. We raden u aan te wachten voordat u de verbinding opnieuw probeert. Terug uit als de eerste pogingen mislukken. Op deze manier kan het systeem alle beschikbare middelen gebruiken om de foutsituatie te overwinnen. Een goed patroon om te volgen is:

* Wacht 5 seconden voordat je eerste nieuwe poging doet.
* Voor elke volgende opnieuw proberen, de verhoging van het wachten exponentieel, tot 60 seconden.
* Stel een maximum aantal nieuwe pogingen in op welk punt de bewerking is mislukt.

Wanneer een verbinding met een actieve transactie mislukt, is het moeilijker om het herstel correct te verwerken. Er zijn twee gevallen: Als de transactie alleen-lezen van aard was, is het veilig om de verbinding te heropenen en de transactie opnieuw te proberen. Als de transactie echter ook naar de database is geschreven, moet u bepalen of de transactie is teruggedraaid of dat deze is geslaagd voordat de tijdelijke fout is uitgevoerd. In dat geval hebt u misschien de commit-bevestiging van de databaseserver niet ontvangen.

Een manier om dit te doen, is het genereren van een unieke ID op de client die wordt gebruikt voor alle pogingen. U geeft deze unieke ID als onderdeel van de transactie door aan de server en slaat deze op in een kolom met een unieke beperking. Op deze manier u de transactie veilig opnieuw proberen. Het zal lukken als de vorige transactie is teruggedraaid en de door de client gegenereerde unieke ID nog niet in het systeem bestaat. Het wordt niet weergegeven als de schending van de dubbele sleutel wordt aangegeven als de unieke id eerder is opgeslagen omdat de vorige transactie is voltooid.

Wanneer uw programma communiceert met Azure Database voor MySQL via middleware van derden, vraagt u de leverancier of de middleware opnieuw proberenlogica bevat voor tijdelijke fouten.

Zorg ervoor dat u de logica opnieuw probeert. Probeer bijvoorbeeld uw code uit te voeren terwijl u de rekenbronnen van uw Azure Database voor MySQL-server opoft. Uw toepassing moet de korte downtime die tijdens deze bewerking wordt ondervonden, zonder problemen verwerken.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Efficiënt verbinding maken met Azure Database voor MySQL

Databaseverbindingen zijn een beperkte bron, dus door effectief gebruik te maken van verbindingspooling om toegang te krijgen tot Azure Database voor MySQL, worden de prestaties geoptimaliseerd. In het onderstaande gedeelte wordt uitgelegd hoe u verbindingspooling of permanente verbindingen gebruikt om beter toegang te krijgen tot Azure Database voor MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Toegang tot databases met behulp van koppelingspooling (aanbevolen)

Het beheren van databaseverbindingen kan een aanzienlijke invloed hebben op de prestaties van de toepassing als geheel. Om de prestaties van uw toepassing te optimaliseren, moet het doel zijn om het aantal keren dat verbindingen worden gemaakt en tijd voor het tot stand brengen van verbindingen in belangrijke codepaden te verminderen. We raden ten zeerste aan om databaseverbindingen te poolen of permanente verbindingen te gebruiken om verbinding te maken met Azure Database voor MySQL. Databaseverbinding pooling verzorgt het maken, beheren en toewijzen van databaseverbindingen. Wanneer een programma een databaseverbinding aanvraagt, geeft het prioriteit aan de toewijzing van bestaande niet-actieve databaseverbindingen, in plaats van het maken van een nieuwe verbinding. Nadat het programma is voltooid met behulp van de databaseverbinding, wordt de verbinding hersteld ter voorbereiding op verder gebruik, in plaats van gewoon te worden gesloten.

Voor een betere illustratie, dit artikel biedt [een stukje van de steekproef code](./sample-scripts-java-connection-pooling.md) die JAVA gebruikt als voorbeeld. Zie [Apache common DBCP voor](https://commons.apache.org/proper/commons-dbcp/)meer informatie.

> [!NOTE]
> De server configureert een time-outmechanisme om een verbinding te sluiten die al enige tijd in een niet-actieve status is om resources vrij te maken. Zorg ervoor dat u het verificatiesysteem instelt om de effectiviteit van permanente verbindingen te garanderen wanneer u ze gebruikt. Zie [Verificatiesystemen configureren aan de clientzijde om de effectiviteit van permanente verbindingen te garanderen voor](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections)meer informatie.

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Toegang tot databases met behulp van permanente verbindingen (aanbevolen)

Het concept van permanente verbindingen is vergelijkbaar met dat van verbinding pooling. Het vervangen van korte verbindingen door permanente verbindingen vereist slechts kleine wijzigingen in de code, maar het heeft een groot effect in termen van het verbeteren van de prestaties in veel typische toepassingsscenario's.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Toegang tot databases met behulp van wacht- en probeermechanisme met korte verbindingen

Als u resourcebeperkingen hebt, raden we u ten zeerste aan databasepooling of permanente verbindingen te gebruiken om toegang te krijgen tot databases. Als uw toepassing korte verbindingen gebruikt en verbindingsfouten ervaart wanneer u de bovengrens voor het aantal gelijktijdige verbindingen nadert, u het mechanisme proberen te wachten en opnieuw te proberen. U een geschikte wachttijd instellen, met een kortere wachttijd na de eerste poging. Daarna u meerdere keren proberen te wachten op gebeurtenissen.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Verificatiemechanismen configureren in clients om de effectiviteit van permanente verbindingen te bevestigen

De server configureert een time-outmechanisme om een verbinding te sluiten die al enige tijd niet actief is om resources vrij te maken. Wanneer de client de database opnieuw opent, is dit gelijk aan het maken van een nieuwe verbindingsaanvraag tussen de client en de server. Om de effectiviteit van verbindingen tijdens het gebruik ervan te garanderen, configureert u een verificatiemechanisme op de client. Zoals in het volgende voorbeeld wordt weergegeven, u Tomcat JDBC-koppelingspooling gebruiken om dit verificatiemechanisme te configureren.

Door de parameter TestOnBorrow in te stellen, controleert de verbindingsgroep bij een nieuwe aanvraag automatisch de effectiviteit van beschikbare niet-actieve verbindingen. Als een dergelijke verbinding effectief is, trekt de direct geretourneerde verbindingspool de verbinding in. De verbindingsgroep maakt vervolgens een nieuwe effectieve verbinding en retourneert deze. Dit proces zorgt ervoor dat de database efficiënt wordt geopend. 

Zie het officiële introductiedocument voor de [JDBC-verbindingsgroep](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes)voor informatie over de specifieke instellingen. U moet voornamelijk de volgende drie parameters instellen: TestOnBorrow (ingesteld op true), ValidationQuery (ingesteld op SELECT 1) en ValidationQueryTimeout (ingesteld op 1). De specifieke voorbeeldcode wordt hieronder weergegeven:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Volgende stappen

* [Verbindingsproblemen met Azure Databases for MySQL oplossen](howto-troubleshoot-common-connection-issues.md)
