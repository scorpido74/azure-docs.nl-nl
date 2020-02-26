---
title: 'Zelf studie: data bases configureren in Azure Database for MySQL met behulp van Ansible'
description: Ontdek hoe u Ansible gebruikt om een Azure Database for MySQL-server te maken en configureren
keywords: ansible, azure, devops, bash, playbook, mysql, database
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: f068b3022c94466a20b524240dc293392b1f42ff
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603122"
---
# <a name="tutorial-configure-databases-in-azure-database-for-mysql-using-ansible"></a>Zelf studie: data bases configureren in Azure Database for MySQL met behulp van Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[Azure database for MySQL](/azure/mysql/overview) is een relationele database service op basis van de MySQL Community Edition. Met Azure Database for MySQL kunt u MySQL-data bases in uw web-apps beheren.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Een MySql-server maken
> * Een MySql-data base maken
> * Een firewall regel configureren zodat een externe app verbinding kan maken met uw server
> * Verbinding maken met uw MySql-server vanuit de Azure Cloud shell
> * Een query uitvoeren op uw beschik bare MySQL-servers
> * Alle data bases in de verbonden servers weer geven

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Met de Playbook-code in deze sectie maakt u een Azure-resource groep. Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.  

Sla het volgende playbook op als `rg.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:

* Er wordt een resource groep met de naam `myResourceGroup` gemaakt.
* De resource groep wordt gemaakt op de locatie van de `eastus`:

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook rg.yml
```

## <a name="create-a-mysql-server-and-database"></a>Een MySQL-server en -database maken

Met de Playbook-code in deze sectie maakt u een MySQL-server en een Azure Database for MySQL-exemplaar. De nieuwe MySQL-server is een algemene doel server van generatie 5 met één vCore en heeft de naam `mysqlserveransible`. Het data base-exemplaar heeft de naam `mysqldbansible`.

Zie [Azure database for MySQL prijs categorieën](/azure/mysql/concepts-pricing-tiers)voor meer informatie over prijs categorieën. 

Sla het volgende playbook op als `mysql_create.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus
    mysqlserver_name: mysqlserveransible
    mysqldb_name: mysqldbansible
    admin_username: mysqladmin
    admin_password: <server_admin_password> 
  tasks:
    - name: Create MySQL Server
      azure_rm_mysqlserver:
        resource_group: "{{ resource_group }}"
        name: "{{ mysqlserver_name }}"
        sku:
          name: B_Gen5_1
          tier: Basic
        location: "{{ location }}"
        version: 5.6
        enforce_ssl: True
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        storage_mb: 51200
    - name: Create instance of MySQL Database
      azure_rm_mysqldatabase:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
        name: "{{ mysqldb_name }}"
```

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:

* In de sectie `vars` moet de waarde van `mysqlserver_name` uniek zijn.
* Vervang in het gedeelte `vars` `<server_admin_password>` door een wacht woord.

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook mysql_create.yml
```

## <a name="configure-a-firewall-rule"></a>Een firewallregel configureren

Met een firewall regel op server niveau kan een externe app verbinding maken met uw server via de Azure MySQL-service Firewall. Voor beelden van externe apps zijn het `mysql` opdracht regel programma en de MySQL Workbench.

Met de Playbook-code in deze sectie maakt u een firewall regel met de naam `extenalaccess` die verbindingen van elk extern IP-adres toestaat. 

Sla het volgende playbook op als `mysql_firewall.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
  - name: Open firewall to access MySQL Server from outside
    azure_rm_resource:
      api_version: '2017-12-01'
      resource_group: "{{ resource_group }}"
      provider: dbformysql
      resource_type: servers
      resource_name: "{{ mysqlserver_name }}"
      subresource:
        - type: firewallrules
          name: externalaccess
      body:
        properties:
          startIpAddress: "0.0.0.0"
          endIpAddress: "255.255.255.255"
```

Voor het uitvoeren van de Playbook raadpleegt u de volgende opmerkingen:

* Vervang `startIpAddress` en `endIpAddress`in het gedeelte variabelen. Gebruik het bereik van IP-adressen die overeenkomen met het bereik van waaruit u verbinding wilt maken.
* Verbindingen met Azure Database voor MySQL communiceren via poort 3306. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 3306 mogelijk niet toegestaan. In dat geval kunt u alleen verbinding maken met uw server als uw IT-afdeling poort 3306 openstelt.
* De Playbook maakt gebruik van de module `azure_rm_resource`, waarmee de REST API direct kan worden gebruikt.

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook mysql_firewall.yml
```

## <a name="connect-to-the-server"></a>Verbinding maken met de server

In deze sectie gebruikt u de Azure Cloud shell om verbinding te maken met de server die u eerder hebt gemaakt.

1. Selecteer de knop **try it** in de volgende code:

    ```azurecli-interactive
    mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    ```

1. Voer bij de prompt de volgende opdracht in om een query uit te voeren op de server status:

    ```sql
    mysql> status
    ```
    
    Als alles goed gaat, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:
    
    ```
    demo@Azure:~$ mysql -h mysqlserveransible.mysql.database.azure.com -u mysqladmin@mysqlserveransible -p
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65233
    Server version: 5.6.39.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.
    
    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64) using  EditLine wrapper
    
    Connection id:          65233
    Current database:
    Current user:           mysqladmin@13.76.42.93
    SSL:                    Cipher in use is AES256-SHA
    Current pager:          stdout
    Using outfile:          ''
    Using delimiter:        ;
    Server version:         5.6.39.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mysqlserveransible.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 36 min 21 sec
    
    Threads: 5  Questions: 559  Slow queries: 0  Opens: 96  Flush tables: 3  Open tables: 10  Queries per second avg: 0.256
    --------------
    ```
    
## <a name="query-mysql-servers"></a>Een query uitvoeren op MySQL-servers

Met de Playbook-code in deze sectie wordt een query uitgevoerd op MySQL-servers in `myResourceGroup` en worden de data bases op de gevonden servers weer gegeven.

Sla het volgende playbook op als `mysql_query.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    mysqlserver_name: mysqlserveransible
  tasks:
    - name: Query MySQL Servers in current resource group
      azure_rm_mysqlserver_facts:
        resource_group: "{{ resource_group }}"
      register: mysqlserverfacts

    - name: Dump MySQL Server facts
      debug:
        var: mysqlserverfacts

    - name: Query MySQL Databases
      azure_rm_mysqldatabase_facts:
        resource_group: "{{ resource_group }}"
        server_name: "{{ mysqlserver_name }}"
      register: mysqldatabasefacts

    - name: Dump MySQL Database Facts
      debug:
        var: mysqldatabasefacts
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook mysql_query.yml
```

Nadat de Playbook is uitgevoerd, ziet u uitvoer die vergelijkbaar is met de volgende resultaten:

```json
"servers": [
    {
        "admin_username": "mysqladmin",
        "enforce_ssl": false,
        "fully_qualified_domain_name": "mysqlserveransible.mysql.database.azure.com",
        "id": "/subscriptions/685ba005-af8d-4b04-8f16-a7bf38b2eb5a/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/mysqlserveransible",
        "location": "eastus",
        "name": "mysqlserveransible",
        "resource_group": "myResourceGroup",
        "sku": {
            "capacity": 1,
            "family": "Gen5",
            "name": "B_Gen5_1",
            "tier": "Basic"
        },
        "storage_mb": 5120,
        "user_visible_state": "Ready",
        "version": "5.6"
    }
]
```

U ziet ook de volgende uitvoer voor de MySQL-Data Base:

```json
"databases": [
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "information_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysql",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransibler"
    },
    {
        "charset": "latin1",
        "collation": "latin1_swedish_ci",
        "name": "mysqldbansible",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    },
    {
        "charset": "utf8",
        "collation": "utf8_general_ci",
        "name": "performance_schema",
        "resource_group": "myResourceGroup",
        "server_name": "mysqlserveransible"
    }
]
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resources die u in dit artikel hebt gemaakt. 

Sla het volgende playbook op als `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Voer de Playbook uit met de opdracht `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Ansible in Azure](/azure/ansible/)