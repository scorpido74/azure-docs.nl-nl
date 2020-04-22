---
title: PostgreSQL instellen op een Linux-VM
description: Meer informatie over het installeren en configureren van PostgreSQL op een Virtuele Linux-machine in Azure
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: d86e42dcc16d108cc82c9d245c7919145cef365f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759344"
---
# <a name="install-and-configure-postgresql-on-azure"></a>PostgreSQL installeren en configureren op Azure
PostgreSQL is een geavanceerde open-source database vergelijkbaar met Oracle en DB2. Het bevat bedrijfsklare functies zoals volledige ACID-naleving, betrouwbare transactionele verwerking en gelijktijdige controle van meerdere versies. Het ondersteunt ook standaarden zoals ANSI SQL en SQL/MED (inclusief buitenlandse gegevenswikkels voor Oracle, MySQL, MongoDB en vele anderen). Het is zeer uitbreidbaar met ondersteuning voor meer dan 12 procedurele talen, GIN- en GiST-indexen, ondersteuning voor ruimtelijke gegevens en meerdere NoSQL-achtige functies voor JSON- of key-value-gebaseerde toepassingen.

In dit artikel leert u hoe u PostgreSQL installeert en configureert op een virtuele Azure-machine met Linux.


## <a name="install-postgresql"></a>PostgreSQL installeren
> [!NOTE]
> U moet al een Azure virtuele machine met Linux hebben om deze zelfstudie te voltooien. Zie de [Azure Linux VM-zelfstudie](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)om een Linux-vm te maken en in te stellen voordat u verdergaat.
> 
> 

Gebruik in dit geval poort 1999 als de PostgreSQL-poort.  

Maak verbinding met de Linux-VM die u hebt gemaakt via PuTTY. Als dit de eerste keer is dat u een Azure Linux VM gebruikt, raadpleegt u [Hoe u SSH met Linux op Azure gebruikt](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) om te leren hoe u PuTTY gebruiken om verbinding te maken met een Linux-vm.

1. Voer de volgende opdracht uit om over te schakelen naar de hoofdmap (beheerder):
   
        # sudo su -
2. Sommige distributies hebben afhankelijkheden die u moet installeren voordat u PostgreSQL installeert. Controleer in deze lijst of je distro in deze lijst staat en voer de juiste opdracht uit:
   
   * Red Hat basis Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian basis Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Download PostgreSQL in de hoofdmap en rits het pakket uit:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Het bovenstaande is een voorbeeld. U vindt het meer gedetailleerde downloadadres in de [Index van /pub/source/](https://ftp.postgresql.org/pub/source/).
4. Voer de volgende opdrachten uit om de build te starten:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Als u alles wilt bouwen dat kan worden gebouwd, inclusief de documentatie`contrib`(HTML- en manpagina's) en extra modules ( ) voert u in plaats daarvan de volgende opdracht uit:
   
        # gmake install-world
   
    U ontvangt het volgende bevestigingsbericht:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>PostgreSQL configureren
1. (Optioneel) Maak een symbolische koppeling om de PostgreSQL-verwijzing in te korten om het versienummer niet op te nemen:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Maak een map voor de database:
   
        # mkdir -p /opt/pgsql_data
3. Maak een niet-hoofdgebruiker en wijzig het profiel van die gebruiker. Schakel vervolgens over naar deze nieuwe gebruiker *(postgres* genoemd in ons voorbeeld):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Om veiligheidsredenen gebruikt PostgreSQL een niet-hoofdgebruiker om de database te initialiseren, te starten of af te sluiten.
   > 
   > 
4. Bewerk het *bash_profile* bestand door onderstaande opdrachten in te voeren. Deze regels worden toegevoegd aan het einde van het *bash_profile* bestand:
   
        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF
5. Voer het *bash_profile-bestand* uit:
   
        $ source .bash_profile
6. Valideer uw installatie met de volgende opdracht:
   
        $ which psql
   
    Als uw installatie succesvol is, ziet u het volgende antwoord:
   
        /opt/pgsql/bin/psql
7. U ook de PostgreSQL-versie controleren:
   
        $ psql -V

8. Initialiseer de database:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    U ontvangt de volgende uitvoer:

![installatiekopie](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>PostgreSQL instellen
<!--    [postgres@ test ~]$ exit -->

Voer de volgende opdrachten uit:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Wijzig twee variabelen in het bestand /etc/init.d/postgresql. Het voorvoegsel is ingesteld op het installatiepad van PostgreSQL: **/opt/pgsql**. PGDATA is ingesteld op het gegevensopslagpad van PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![installatiekopie](./media/postgresql-install/no2.png)

Wijzig het bestand om het uitvoerbaar te maken:

    # chmod +x /etc/init.d/postgresql

Start PostgreSQL:

    # /etc/init.d/postgresql start

Controleer of het eindpunt van PostgreSQL is ingeschakeld:

    # netstat -tunlp|grep 1999

In dat geval moet de volgende uitvoer worden weergegeven:

![installatiekopie](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Verbinding maken met de Postgres-database
Schakel opnieuw over naar de postgres-gebruiker:

    # su - postgres

Maak een Postgres-database:

    $ createdb events

Maak verbinding met de gebeurtenissendatabase die u zojuist hebt gemaakt:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Een Postgres-tabel maken en verwijderen
Nu u verbinding hebt gemaakt met de database, u er tabellen in maken.

Maak bijvoorbeeld een nieuw voorbeeld van de postgrestabel met de volgende opdracht:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

U hebt nu een tabel met vier kolommen ingesteld met de volgende kolomnamen en -beperkingen:

1. De "naam" kolom is beperkt door de VARCHAR commando te worden onder 20 tekens lang.
2. De kolom "voedsel" geeft het voedselitem aan dat elke persoon zal brengen. VARCHAR beperkt deze tekst tot minder dan 30 tekens.
3. De "bevestigde" kolom registreert of de persoon RSVP'd aan potluck heeft. De acceptabele waarden zijn "Y" en "N".
4. De kolom 'datum' wordt weergegeven wanneer ze zich hebben aangemeld voor het evenement. Postgres vereist dat data worden geschreven als yyyy-mm-dd.

U ziet het volgende als uw tabel is gemaakt:

![installatiekopie](./media/postgresql-install/no4.png)

U de tabelstructuur ook controleren met de volgende opdracht:

![installatiekopie](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Gegevens toevoegen aan een tabel
Voeg eerst informatie in een rij in:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Deze uitvoer ziet er ongeveer als volgt uit:

![installatiekopie](./media/postgresql-install/no6.png)

U ook nog een paar mensen aan de tafel toevoegen. Hier zijn enkele opties, of u uw eigen:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Tabellen weergeven
Gebruik de volgende opdracht om een tabel weer te geven:

    select * from potluck;

Dit is de uitvoer:

![installatiekopie](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Gegevens in een tabel verwijderen
Gebruik de volgende opdracht om gegevens in een tabel te verwijderen:

    delete from potluck where name=’John’;

Hiermee worden alle informatie in de rij 'John' verwijderd. Dit is de uitvoer:

![installatiekopie](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Gegevens in een tabel bijwerken
Gebruik de volgende opdracht om gegevens in een tabel bij te werken. Voor deze, Sandy heeft bevestigd dat ze aanwezig zijn, dus we zullen de RSVP veranderen van "N" naar "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Meer informatie over PostgreSQL
Nu u de installatie van PostgreSQL in een Azure Linux VM hebt voltooid, u deze graag gebruiken in Azure. Ga voor meer informatie over PostgreSQL naar de [PostgreSQL-website.](https://www.postgresql.org/)

