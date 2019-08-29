---
title: PostgreSQL instellen op een Linux-VM | Microsoft Docs
description: Meer informatie over het installeren en configureren van PostgreSQL op een virtuele Linux-machine in azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 7fc8cb7c07dd27cd42dc4c6a7e0a576f0efe04e0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091724"
---
# <a name="install-and-configure-postgresql-on-azure"></a>PostgreSQL installeren en configureren op Azure
PostgreSQL is een geavanceerde open source-data base, vergelijkbaar met Oracle en DB2. Het bevat bedrijfs klare functies, zoals volwaardige naleving, betrouw bare transactionele verwerking en gelijktijdigheids beheer met meerdere versies. Het biedt ook ondersteuning voor standaarden zoals ANSI SQL en SQL/MED (waaronder Foreign data-wrappers voor Oracle, MySQL, MongoDB en vele andere). Het is zeer uitbreidbaar met ondersteuning voor meer dan 12 procedurele talen, EGINNEN-en concept indexen, ondersteuning voor ruimtelijke gegevens en meerdere NoSQL functies voor JSON of op sleutel waarde gebaseerde toepassingen.

In dit artikel wordt beschreven hoe u PostgreSQL kunt installeren en configureren op een virtuele Azure-machine waarop Linux wordt uitgevoerd.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>PostgreSQL installeren
> [!NOTE]
> U moet al een virtuele Azure-machine met Linux hebben om deze zelf studie te kunnen volt ooien. Als u een virtuele Linux-machine wilt maken en instellen voordat u doorgaat, raadpleegt u de [zelf studie voor Azure Linux VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Gebruik in dit geval poort 1999 als de PostgreSQL-poort.  

Maak verbinding met de virtuele Linux-machine die u hebt gemaakt via PuTTy. Als dit de eerste keer is dat u een virtuele machine van Azure Linux gebruikt, raadpleegt u [SSH gebruiken met Linux op Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor meer informatie over het gebruik van putty om verbinding te maken met een virtuele Linux-machine.

1. Voer de volgende opdracht uit om over te scha kelen naar het hoofd niveau (beheerder):
   
        # sudo su -
2. Voor sommige distributies gelden afhankelijkheden die u moet installeren voordat u PostgreSQL installeert. Controleer of uw distributie in deze lijst voor komen en voer de volgende opdracht uit:
   
   * Red Hat base Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian base Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Down load PostgreSQL naar de hoofdmap en pak het pakket uit:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Hierboven vindt u een voor beeld. U kunt het gedetailleerde Download adres vinden in de [index van/pub/source/](https://ftp.postgresql.org/pub/source/).
4. Voer de volgende opdrachten uit om de build te starten:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Als u alles wilt bouwen dat kan worden gemaakt, met inbegrip van de documentatie (HTML-en man-pagina's) en aanvullende modules (contrib), voert u de volgende opdracht uit:
   
        # gmake install-world
   
    Het volgende bevestigings bericht wordt weer gegeven:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>PostgreSQL configureren
1. Beschrijving Maak een symbolische koppeling om de PostgreSQL-verwijzing zo kort te maken dat het versie nummer niet wordt vermeld:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Maak een map voor de Data Base:
   
        # mkdir -p /opt/pgsql_data
3. Maak een niet-hoofd gebruiker en wijzig het profiel van de gebruiker. Schakel vervolgens over naar deze nieuwe gebruiker (met de naam *post gres* in ons voor beeld):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Uit veiligheids overwegingen gebruikt PostgreSQL een niet-hoofd gebruiker om de data base te initialiseren, te starten of af te sluiten.
   > 
   > 
4. Bewerk het *bash_profile* -bestand door de onderstaande opdrachten in te voeren. Deze regels worden toegevoegd aan het einde van het *bash_profile* -bestand:
   
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
5. Voer het *bash_profile* -bestand uit:
   
        $ source .bash_profile
6. Valideer uw installatie met behulp van de volgende opdracht:
   
        $ which psql
   
    Als uw installatie is voltooid, ziet u het volgende antwoord:
   
        /opt/pgsql/bin/psql
7. U kunt ook de PostgreSQL-versie controleren:
   
        $ psql -V

8. Initialiseer de Data Base:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    De volgende uitvoer wordt weer gegeven:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>PostgreSQL instellen
<!--    [postgres@ test ~]$ exit -->

Voer de volgende opdrachten uit:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Wijzig twee variabelen in het/etc/init.d/postgresql-bestand. Het voor voegsel wordt ingesteld op het installatiepad van PostgreSQL: **/opt/pgsql**. PGDATA wordt ingesteld op het gegevenspad van PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Wijzig het bestand om het uit te voeren:

    # chmod +x /etc/init.d/postgresql

PostgreSQL starten:

    # /etc/init.d/postgresql start

Controleer of het eind punt van PostgreSQL zich op:

    # netstat -tunlp|grep 1999

In dat geval moet de volgende uitvoer worden weergegeven:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Verbinding maken met de post gres-data base
Opnieuw overschakelen naar de post gres-gebruiker:

    # su - postgres

Een post gres-data base maken:

    $ createdb events

Verbinding maken met de data base met gebeurtenissen die u zojuist hebt gemaakt:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Een post gres-tabel maken en verwijderen
Nu u verbinding hebt gemaakt met de data base, kunt u er tabellen in maken.

Maak bijvoorbeeld een nieuwe voorbeeld tabel met post gres met behulp van de volgende opdracht:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

U hebt nu een tabel met vier kolommen met de volgende kolom namen en beperkingen ingesteld:

1. De kolom ' name ' is beperkt door de VARCHAR-opdracht, die minder dan 20 tekens lang is.
2. In de kolom "Food" wordt het voedings punt aangegeven dat elke persoon gaat meenemen. VARCHAR beperkt deze tekst tot 30 tekens.
3. In de kolom bevestigd wordt vastgelegd of de persoon een RSVP-feest heeft. De acceptabele waarden zijn "Y" en "N".
4. In de kolom date wordt weer gegeven wanneer de gebruiker zich heeft geregistreerd voor de gebeurtenis. Post gres vereist dat datums worden geschreven als jjjj-mm-dd.

Als uw tabel is gemaakt, ziet u het volgende:

![image](./media/postgresql-install/no4.png)

U kunt ook de tabel structuur controleren met behulp van de volgende opdracht:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Gegevens toevoegen aan een tabel
Voeg eerst gegevens in een rij in:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

U ziet deze uitvoer:

![image](./media/postgresql-install/no6.png)

U kunt ook een paar personen toevoegen aan de tabel. Hier volgen enkele opties, of u kunt uw eigen instellingen maken:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Tabellen weer geven
Gebruik de volgende opdracht om een tabel weer te geven:

    select * from potluck;

De uitvoer is:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Gegevens in een tabel verwijderen
Gebruik de volgende opdracht om gegevens in een tabel te verwijderen:

    delete from potluck where name=’John’;

Hiermee verwijdert u alle gegevens in de rij ' Johan '. De uitvoer is:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Gegevens in een tabel bijwerken
Gebruik de volgende opdracht om gegevens in een tabel bij te werken. Voor deze versie heeft zand bevestigd dat ze deel nemen, dus zullen we de RSVP wijzigen van ' N ' in ' Y ':

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Meer informatie over PostgreSQL
Nu u de installatie van PostgreSQL in een Azure Linux-VM hebt voltooid, kunt u deze gebruiken in Azure. Ga naar de [postgresql-website](https://www.postgresql.org/)voor meer informatie over postgresql.

