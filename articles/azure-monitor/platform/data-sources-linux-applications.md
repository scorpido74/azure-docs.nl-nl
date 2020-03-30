---
title: Prestaties van Linux-toepassingen verzamelen in Azure Monitor | Microsoft Documenten
description: In dit artikel vindt u informatie over het configureren van de Log Analytics-agent voor Linux om prestatiemeteritems voor MySQL en Apache HTTP Server te verzamelen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: 2fd148dbb85a4fd60fe63d4fb73128bf92dea1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670556"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Prestatiemeteritems voor Linux-toepassingen verzamelen in Azure Monitor 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
In dit artikel vindt u informatie over het configureren van de [Log Analytics-agent voor Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) om prestatiemeteritems voor specifieke toepassingen in Azure Monitor te verzamelen.  De toepassingen in dit artikel zijn:  

- [MySQL](#mysql)
- [Apache HTTP-server](#apache-http-server)

## <a name="mysql"></a>MySQL
Als MySQL Server of MariaDB Server wordt gedetecteerd op de computer wanneer de Log Analytics-agent is geïnstalleerd, wordt automatisch een provider voor prestatiebewaking voor MySQL Server geïnstalleerd. Deze provider maakt verbinding met de lokale MySQL/MariaDB-server om prestatiestatistieken bloot te leggen. MySQL-gebruikersreferenties moeten zo zijn geconfigureerd dat de provider toegang heeft tot de MySQL Server.

### <a name="configure-mysql-credentials"></a>MySQL-referenties configureren
De MySQL OMI-provider vereist een vooraf geconfigureerde MySQL-gebruiker en geïnstalleerde MySQL-clientbibliotheken om de prestatie- en statusgegevens van het MySQL-exemplaar op te vragen.  Deze referenties worden opgeslagen in een verificatiebestand dat is opgeslagen op de Linux-agent.  Het verificatiebestand geeft aan op welk bindingsadres en poort de MySQL-instantie aanluistert en welke referenties moeten worden gebruikt om metrische gegevens te verzamelen.  

Tijdens de installatie van de Log Analytics-agent voor Linux scant de MySQL OMI-provider MySQL mySQL my.cnf-configuratiebestanden (standaardlocaties) op binding-adres en poort en stelt het MySQL OMI-verificatiebestand gedeeltelijk in.

Het MySQL-verificatiebestand `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`wordt opgeslagen op .


### <a name="authentication-file-format"></a>Bestandsindeling voor verificatie
Hieronder volgt de indeling voor het MySQL OMI-verificatiebestand

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

De vermeldingen in het verificatiebestand worden beschreven in de volgende tabel.

| Eigenschap | Beschrijving |
|:--|:--|
| Poort | Vertegenwoordigt de huidige poort waarop de Instantie MySQL luistert. Poort 0 geeft aan dat de volgende eigenschappen worden gebruikt voor de standaardinstantie. |
| Bindingsadres| Huidig MySQL-bindingsadres. |
| gebruikersnaam| MySQL-gebruiker gebruikt om de MySQL-server instantie te controleren. |
| Basis64 gecodeerd wachtwoord| Wachtwoord van de MySQL-bewakingsgebruiker gecodeerd in Base64. |
| Autoupdate| Hiermee geeft u op of u wijzigingen in het my.cnf-bestand opnieuw wilt scannen en het MySQL OMI-verificatiebestand moet overschrijven wanneer de MySQL OMI-provider wordt bijgewerkt. |

### <a name="default-instance"></a>Standaardexemplaar
Het MySQL OMI-verificatiebestand kan een standaardinstantie en poortnummer definiëren om het beheren van meerdere MySQL-exemplaren op één Linux-host eenvoudiger te maken.  De standaardinstantie wordt aangeduid met een instantie met poort 0. Alle extra instanties nemen eigenschappen over die zijn ingesteld van de standaardinstantie, tenzij ze verschillende waarden opgeven. Als bijvoorbeeld MySQL-instantie luisteren op poort '3308' wordt toegevoegd, wordt het bindingsadres, de gebruikersnaam en het gecodeerde wachtwoord van de standaardinstantie gebruikt om te proberen het luisteren van de instantie op 3308 te controleren. Als de instantie op 3308 is gebonden aan een ander adres en dezelfde MySQL-gebruikersnaam en wachtwoordpaar gebruikt, is alleen het bindingsadres nodig en worden de andere eigenschappen overgenomen.

In de volgende tabel heeft u voorbeeldinstellingen 

| Beschrijving | File |
|:--|:--|
| Standaardinstantie en -instantie met poort 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Standaardinstantie en-instantie met poort 3308 en verschillende gebruikersnaam en wachtwoord. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMI-verificatiebestandsprogramma
Inbegrepen bij de installatie van de MySQL OMI-provider is een MySQL OMI-verificatiebestandsprogramma dat kan worden gebruikt om het MySQL OMI-verificatiebestand te bewerken. Het verificatiebestandsprogramma is te vinden op de volgende locatie.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Het bestand met referenties moet leesbaar zijn door het omsagent-account. Het uitvoeren van de mycimprovauth commando als omsgent wordt aanbevolen.

In de volgende tabel vindt u details over de syntaxis voor het gebruik van mycimprovauth.

| Bewerking | Voorbeeld | Beschrijving
|:--|:--|:--|
| automatisch *bijwerken false of true* | mycimprovauth autoupdate false | Hiermee bepaalt u of het verificatiebestand automatisch wordt bijgewerkt bij het opnieuw opstarten of bijwerken. |
| wachtwoord voor *standaardbindingsadres* | mycimprovauth standaard 127.0.0.1 root pwd | Hiermee stelt u de standaardinstantie in het MySQL OMI-verificatiebestand in.<br>Het wachtwoordveld moet in platte tekst worden ingevoerd - het wachtwoord in het MySQL OMI-verificatiebestand is Base 64 gecodeerd. |
| *standaard- of port_num* verwijderen | mycimprovauth 3308 | Hiermee verwijdert u de opgegeven instantie standaard of op poortnummer. |
| Help | mycimprov hulp | Hiermee drukt u een lijst af met opdrachten die u wilt gebruiken. |
| afdrukken | mycimprov print | Hiermee wordt een eenvoudig te lezen MySQL OMI-verificatiebestand afgedrukt. |
| update port_num *wachtwoord voor gebruikersnaam van het bindingsadres* | mycimprov update 3307 127.0.0.1 root pwd | Hiermee wordt de opgegeven instantie bijgewerkt of wordt de instantie toegevoegd als deze niet bestaat. |

In de volgende voorbeeldopdrachten wordt een standaardgebruikersaccount voor de MySQL-server op localhost gedefinieerd.  Het wachtwoordveld moet in platte tekst worden ingevoerd - het wachtwoord in het MySQL OMI-verificatiebestand is Base 64 gecodeerd

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Databasemachtigingen vereist voor MySQL-prestatiemeteritems
De MySQL-gebruiker heeft toegang nodig tot de volgende query's om prestatiegegevens van MySQL Server te verzamelen. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


De MySQL-gebruiker vereist ook SELECT-toegang tot de volgende standaardtabellen.

- information_schema
- Mysql. 

Deze bevoegdheden kunnen worden verleend door de volgende subsidieopdrachten uit te voeren.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Als u machtigingen wilt verlenen aan een MySQL-bewakingsgebruiker, moet de verlenende gebruiker de bevoegdheid 'GRANT-optie' hebben en het bevoegdheid dat wordt verleend.

### <a name="define-performance-counters"></a>Prestatiemeteritems definiëren

Zodra u de Log Analytics-agent voor Linux hebt geconfigureerd om gegevens naar Azure Monitor te verzenden, moet u de prestatiemeteritems configureren die u wilt verzamelen.  Gebruik de procedure in [windows- en Linux-prestatiegegevensbronnen in Azure Monitor](data-sources-performance-counters.md) met de tellers in de volgende tabel.

| Objectnaam | Tellernaam |
|:--|:--|
| MySQL-database | Schijfruimte in bytes |
| MySQL-database | Tabellen |
| MySQL-server | Afgebroken verbinding Pct |
| MySQL-server | Verbinding Gebruiken Pct |
| MySQL-server | Schijfruimtegebruik in bytes |
| MySQL-server | Pct voor volledige tabelscannen |
| MySQL-server | InnoDB Buffer Pool Hit Pct |
| MySQL-server | InnoDB Buffer Pool Gebruik Pct |
| MySQL-server | InnoDB Buffer Pool Gebruik Pct |
| MySQL-server | Hit Pct met sleutelcache |
| MySQL-server | Sleutelcache Pct gebruiken |
| MySQL-server | Schrijven Pct met sleutelcache |
| MySQL-server | Hit Pct voor querycache |
| MySQL-server | Querycache snoeit Pct |
| MySQL-server | Querycache Pct gebruiken |
| MySQL-server | Hit Pct voor tabelcache |
| MySQL-server | Tabelcache Pct gebruiken |
| MySQL-server | TabelvergrendelingStelling Pct |

## <a name="apache-http-server"></a>Apache HTTP-server 
Als Apache HTTP Server wordt gedetecteerd op de computer wanneer de omsagent-bundel is geïnstalleerd, wordt automatisch een provider voor prestatiebewaking voor Apache HTTP Server geïnstalleerd. Deze provider vertrouwt op een Apache-module die in de Apache HTTP-server moet worden geladen om toegang te krijgen tot prestatiegegevens. De module kan worden geladen met de volgende opdracht:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Voer de volgende opdracht uit om de Apache-bewakingsmodule te ontladen:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Prestatiemeteritems definiëren

Zodra u de Log Analytics-agent voor Linux hebt geconfigureerd om gegevens naar Azure Monitor te verzenden, moet u de prestatiemeteritems configureren die u wilt verzamelen.  Gebruik de procedure in [windows- en Linux-prestatiegegevensbronnen in Azure Monitor](data-sources-performance-counters.md) met de tellers in de volgende tabel.

| Objectnaam | Tellernaam |
|:--|:--|
| Apache HTTP-server | Drukke werknemers |
| Apache HTTP-server | Niet-actieve werknemers |
| Apache HTTP-server | Pct Bezig werknemers |
| Apache HTTP-server | Totaal Pct-cpu |
| Virtuele Host apache | Fouten per minuut - Client |
| Virtuele Host apache | Fouten per minuut - Server |
| Virtuele Host apache | KB per aanvraag |
| Virtuele Host apache | Aanvragen KB per seconde |
| Virtuele Host apache | Aanvragen per seconde |



## <a name="next-steps"></a>Volgende stappen
* [Verzamel prestatietellers](data-sources-performance-counters.md) van Linux-agents.
* Meer informatie over [logboekquery's](../log-query/log-query-overview.md) om de gegevens te analyseren die zijn verzameld uit gegevensbronnen en -oplossingen. 
