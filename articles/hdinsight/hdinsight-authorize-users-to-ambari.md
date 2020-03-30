---
title: Gebruikers autoriseren voor Ambari-weergaven - Azure HDInsight
description: Machtigingen voor Ambari-gebruikers- en groepsmachtigingen voor HDInsight-clusters beheren met ESP ingeschakeld.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 3bb58334e96bf5378fb78b70125f9c7994a7c2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435645"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Gebruikers machtigen voor Apache Ambari-weergaven

[HDInsight-clusters (Enterprise Security Package)](./domain-joined/hdinsight-security-overview.md) bieden mogelijkheden op bedrijfsniveau, waaronder Azure Active Directory-gebaseerde verificatie. U [nieuwe gebruikers synchroniseren](hdinsight-sync-aad-users-to-cluster.md) die zijn toegevoegd aan Azure AD-groepen die toegang hebben gekregen tot het cluster, zodat deze specifieke gebruikers bepaalde acties kunnen uitvoeren. Werken met gebruikers, groepen en machtigingen in [Apache Ambari](https://ambari.apache.org/) wordt ondersteund voor zowel ESP HDInsight-clusters als standaard HDInsight-clusters.

Active Directory-gebruikers kunnen zich aanmelden bij de clusterknooppunten met behulp van hun domeinreferenties. Ze kunnen hun domeinreferenties ook gebruiken om clusterinteracties te verifiëren met andere goedgekeurde eindpunten zoals [Hue,](https://gethue.com/)Ambari Views, ODBC, JDBC, PowerShell en REST API's.

> [!WARNING]  
> Wijzig het wachtwoord van de Ambari-waakhond (hdinsightwatchdog) niet op uw Linux-gebaseerde HDInsight-cluster. Als u het wachtwoord wijzigt, u scriptacties gebruiken of schaalbewerkingen uitvoeren met uw cluster.

Als u dit nog niet hebt gedaan, volgt u [deze instructies](./domain-joined/apache-domain-joined-configure.md) om een nieuw ESP-cluster in te richten.

## <a name="access-the-ambari-management-page"></a>Toegang tot de ambari-beheerpagina

Om naar de **Ambari management pagina** op de [Apache Ambari Web UI,](hdinsight-hadoop-manage-ambari.md)blader naar `https://CLUSTERNAME.azurehdinsight.net`. Voer de gebruikersnaam en het wachtwoord van de clusterbeheerder in die u hebt gedefinieerd bij het maken van het cluster. Selecteer vervolgens in het Ambari-dashboard **Ambari beheren** onder het **beheerdersmenu:**

![Apache Ambari dashboard beheren](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="add-users"></a>Gebruikers toevoegen

### <a name="add-users-through-the-portal"></a>Gebruikers toevoegen via de portal

1. Selecteer **Gebruikers**op de beheerpagina .

    ![Gebruikers van Apache Ambari-beheerpagina's](./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png)

1. Selecteer **+ Lokale gebruiker maken**.

1. **Gebruikersnaam** en **wachtwoord opgeven**. Selecteer **Opslaan**.

### <a name="add-users-through-powershell"></a>Gebruikers toevoegen via PowerShell

Bewerk de onderstaande variabelen `CLUSTERNAME` `NEWUSER`door `PASSWORD` , en met de juiste waarden te vervangen.

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>Gebruikers toevoegen via Curl

Bewerk de onderstaande variabelen `CLUSTERNAME` `ADMINPASSWORD`door `NEWUSER`, `USERPASSWORD` , en met de juiste waarden te vervangen. Het script is ontworpen om te worden uitgevoerd met bash. Kleine wijzigingen nodig zou zijn voor een Windows-opdrachtprompt.

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>Machtigingen verlenen aan Apache Hive-weergaven

Ambari komt met view instances voor onder andere [Apache Hive](https://hive.apache.org/) en [Apache TEZ.](https://tez.apache.org/) Als u toegang wilt verlenen tot een of meer hive-weergave-instanties, gaat u naar de **ambari-beheerpagina**.

1. Selecteer op de beheerpagina de koppeling **Weergaven** onder de menukop **Weergaven** aan de linkerkant.

    ![Apache Ambari bekijkt weergavekoppelingen](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. Vouw op de pagina Weergaven de rij **HIVE** uit. Er is één standaardhive-weergave die wordt gemaakt wanneer de Hive-service aan het cluster wordt toegevoegd. U ook meer hive-weergave-exemplaren maken als dat nodig is. Selecteer een Hive-weergave:

    ![HDInsight Views - Apache Hive view HDInsight Views - Apache Hive view HDInsight Views - Apache Hive view HDInsight](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Schuif naar de onderkant van de pagina Weergeven. Onder de sectie *Machtigingen* hebt u twee opties voor het verlenen van machtigingen aan domeingebruikers voor de weergave:

**Geef deze gebruikers** ![toestemming Om deze gebruikers toestemming te verlenen](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Toestemming verlenen aan deze groepen** ![Toestemming verlenen aan deze groepen](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Als u een gebruiker wilt toevoegen, selecteert u de knop **Gebruiker toevoegen.**

   * Begin met het typen van de gebruikersnaam en u ziet een vervolgkeuzelijst met eerder gedefinieerde namen.

     ![Apache Ambari gebruiker automatisch voltooid](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Selecteer of typ het typen, de gebruikersnaam. Als u deze gebruikersnaam als nieuwe gebruiker wilt toevoegen, selecteert u de knop **Nieuw.**

   * Als u de wijzigingen wilt opslaan, schakelt u het **blauwe selectievakje in.**

     ![Apache Ambari verleent gebruikersmachtigingen](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Als u een groep wilt toevoegen, selecteert u de knop **Groep toevoegen.**

   * Begin met het typen van de groepsnaam. Het proces waarbij een bestaande groepsnaam wordt geselecteerd of een nieuwe groep wordt toegevoegd, is hetzelfde als voor het toevoegen van gebruikers.
   * Als u de wijzigingen wilt opslaan, schakelt u het **blauwe selectievakje in.**

     ![Apache Ambari verleent machtigingen](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

Het is handig om gebruikers rechtstreeks aan een weergave toe te voegen als u machtigingen wilt toewijzen aan een gebruiker om die weergave te gebruiken, maar niet wilt dat deze lid is van een groep met extra machtigingen. Als u de hoeveelheid administratieve overhead wilt verminderen, kan het eenvoudiger zijn om machtigingen toe te wijzen aan groepen.

## <a name="grant-permissions-to-apache-tez-views"></a>Machtigingen verlenen aan Apache TEZ-weergaven

Met de instanties van de [Apache TEZ-weergave](https://tez.apache.org/) kunnen de gebruikers alle Tez-taken controleren en debuggen, ingediend door Apache Hive-query's en [Apache Pig-scripts.](https://pig.apache.org/) [Apache Hive](https://hive.apache.org/) Er is één standaardinstantie voor tez-weergave die wordt gemaakt wanneer het cluster is ingericht.

Als u gebruikers en groepen wilt toewijzen aan een tez-weergaveinstantie, vouwt u de **tez-rij** uit op de pagina Weergaven, zoals eerder beschreven.

![HDInsight Views - Apache Tez view HDInsight Views - Apache Tez view HDInsight Views - Apache Tez view HDInsight](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Als u gebruikers of groepen wilt toevoegen, herhaalt u stap 3 - 5 in de vorige sectie.

## <a name="assign-users-to-roles"></a>Gebruikers toewijzen aan rollen

Er zijn vijf beveiligingsrollen voor gebruikers en groepen, weergegeven in volgorde van afnemende toegangsmachtigingen:

* Clusterbeheerder
* Clusteroperator
* Servicebeheerder
* Serviceoperator
* Clustergebruiker

Als u rollen wilt beheren, gaat u naar de **ambari-beheerpagina**en selecteert u de koppeling **Rollen** in de *menugroep Clusters* aan de linkerkant.

![Koppelingen in het menu rollen voor Apache Ambari-rollen](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Als u de lijst met machtigingen voor elke rol wilt bekijken, klikt u op het blauwe vraagteken naast de **koptekst van** de tabel rollen op de pagina Rollen.

![Koppelingenmachtigingen voor Apache Ambari-rollen](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Koppelingenmachtigingen voor Apache Ambari-rollen")

Op deze pagina zijn er twee verschillende weergaven die u gebruiken om rollen voor gebruikers en groepen te beheren: Blokkeren en Aanbieden.

### <a name="block-view"></a>Blokweergave

In de weergave Blok wordt elke rol in de eigen rij weergegeven en worden de **rollen Toewijzen aan deze gebruikers** weergegeven en rollen toewijzen aan deze **groepenopties** zoals eerder beschreven.

![Blokweergave apache Ambari-rollen](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>Lijstweergave

De lijstweergave biedt snelle bewerkingsmogelijkheden in twee categorieën: Gebruikers en groepen.

* In de categorie Gebruikers van de lijstweergave wordt een lijst met alle gebruikers weergegeven, zodat u een rol voor elke gebruiker in de vervolgkeuzelijst selecteren.

    ![Lijstweergave van Apache Ambari-rollen - gebruikers](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

* In de categorie Groepen van de lijstweergave worden alle groepen en de rol weergegeven die aan elke groep is toegewezen. In ons voorbeeld wordt de lijst met groepen gesynchroniseerd vanuit de Azure AD-groepen die zijn opgegeven in de eigenschap **Access-gebruikersgroep** van de domeininstellingen van het cluster. Zie [Een HDInsight-cluster maken met ESP ingeschakeld](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).

    ![Weergave van de lijst met rollen van Apache Ambari - groepen](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    In de afbeelding hierboven krijgt de groep 'hiveusers' de rol *Clustergebruiker* toegewezen. Dit is een alleen-lezen rol waarmee de gebruikers van die groep serviceconfiguraties en clusterstatistieken kunnen bekijken, maar niet kunnen wijzigen.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Log in bij Ambari als alleen-weergavegebruiker

We hebben onze Azure AD-domeingebruiker 'hiveuser1'-machtigingen toegewezen aan Hive- en Tez-weergaven. Wanneer we de Ambari Web UI starten en de domeinreferenties van deze gebruiker invoeren (Azure AD-gebruikersnaam in e-mailindeling en wachtwoord), wordt de gebruiker doorgestuurd naar de ambari-weergavenpagina. Vanaf hier kan de gebruiker elke toegankelijke weergave selecteren. De gebruiker kan geen ander deel van de site bezoeken, waaronder het dashboard, services, hosts, waarschuwingen of beheerderspagina's.

![Apache Ambari-gebruiker met alleen weergaven](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Inloggen bij Ambari als clustergebruiker

We hebben onze Azure AD-domeingebruiker 'hiveuser2' toegewezen aan de rol *Clustergebruiker.* Deze rol heeft toegang tot het dashboard en alle menu-items. Een clustergebruiker heeft minder toegestane opties dan een beheerder. Hiveuser2 kan bijvoorbeeld configuraties voor elk van de services weergeven, maar kan deze niet bewerken.

![Apache Ambari dashboard display](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Volgende stappen

* [Apache Hive-beleid configureren in HDInsight met ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [ESP HDInsight-clusters beheren](./domain-joined/apache-domain-joined-manage.md)
* [Gebruik de Apache Hive View met Apache Hadoop in HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Azure AD-gebruikers synchroniseren met het cluster](hdinsight-sync-aad-users-to-cluster.md)
* [HDInsight-clusters beheren met behulp van de Apache Ambari REST API](./hdinsight-hadoop-manage-ambari-rest-api.md)
