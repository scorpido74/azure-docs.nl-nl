---
title: Azure Active Directory-gebruikers synchroniseren met HDInsight-cluster
description: Gesynchroniseerde geverifieerde gebruikers vanuit Azure Active Directory naar een HDInsight-cluster.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 299d242c38152db6a471159d1f3d2803598c1832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75744856"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Azure Active Directory-gebruikers synchroniseren met een HDInsight-cluster

[HDInsight-clusters met Enterprise Security Package (ESP)](hdinsight-domain-joined-introduction.md) kunnen sterke verificatie gebruiken met Azure Active Directory-gebruikers (Azure AD) en *rbac-beleid (Role-based access control)* gebruiken. Terwijl u gebruikers en groepen toevoegt aan Azure AD, u de gebruikers synchroniseren die toegang tot uw cluster nodig hebben.

## <a name="prerequisites"></a>Vereisten

Als u dit nog niet hebt gedaan, [maakt u een HDInsight-cluster met enterprise security-pakket](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Nieuwe Azure AD-gebruikers toevoegen

Als u uw hosts wilt bekijken, opent u de gebruikersinterface van Ambari Web. Elk knooppunt wordt bijgewerkt met nieuwe instellingen voor onbeheerde upgrade.

1. Navigeer vanuit de [Azure-portal](https://portal.azure.com)naar de Azure AD-map die is gekoppeld aan uw ESP-cluster.

2. Selecteer **Alle gebruikers** in het linkermenu en selecteer Vervolgens Nieuwe **gebruiker**.

    ![Gebruikers en groepen van Azure-portalen](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Vul het nieuwe gebruikersformulier in. Selecteer groepen die u hebt gemaakt voor het toewijzen van op een cluster gebaseerde machtigingen. Maak in dit voorbeeld een groep met de naam 'HiveUsers', waaraan u nieuwe gebruikers toewijzen. De [voorbeeldinstructies](hdinsight-domain-joined-configure.md) voor het maken van `HiveUsers` een `AAD DC Administrators`ESP-cluster omvatten het toevoegen van twee groepen en .

    ![Het gebruikersvenster van Azure-portalen selecteert groepen](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Selecteer **Maken**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>De Apache Ambari REST API gebruiken om gebruikers te synchroniseren

Gebruikersgroepen die zijn opgegeven tijdens het maken van het cluster, worden op dat moment gesynchroniseerd. Synchronisatie van gebruikers vindt automatisch één keer per uur plaats. Als u de gebruikers onmiddellijk wilt synchroniseren of een andere groep wilt synchroniseren dan de groepen die tijdens het maken van het cluster zijn opgegeven, gebruikt u de Ambari REST API.

De volgende methode gebruikt POST met de Ambari REST API. Zie CLUSTERS beheren van HDInsight voor meer informatie [met behulp van de Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

1. Gebruik [de ssh-opdracht](hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande `CLUSTERNAME` opdracht door de naam van uw cluster te vervangen en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Voer na verificatie de volgende opdracht in:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    Het antwoord moet er als volgt uitzien:

    ```json
    {
      "resources" : [
        {
          "href" : "http://<ACTIVE-HEADNODE-NAME>.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. Als u de synchronisatiestatus wilt `curl` bekijken, voert u een nieuwe opdracht uit:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    Het antwoord moet er als volgt uitzien:

    ```json
    {
      "href" : "http://<ACTIVE-HEADNODE-NAME>.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. Dit resultaat geeft aan dat de status **VOLTOOID**is, één nieuwe gebruiker is gemaakt en dat aan de gebruiker een lidmaatschap is toegewezen. In dit voorbeeld wordt de gebruiker toegewezen aan de gesynchroniseerde LDAP-groep 'HiveUsers', omdat de gebruiker aan dezelfde groep in Azure AD is toegevoegd.

    > [!NOTE]  
    > De vorige methode synchroniseert alleen de Azure AD-groepen die zijn opgegeven in de eigenschap **Access-gebruikersgroep** van de domeininstellingen tijdens het maken van het cluster. Zie [een HDInsight-cluster maken voor](domain-joined/apache-domain-joined-configure.md)meer informatie.

## <a name="verify-the-newly-added-azure-ad-user"></a>De nieuw toegevoegde Azure AD-gebruiker verifiëren

Open de [Gebruikersinterface van Apache Ambari-web](hdinsight-hadoop-manage-ambari.md) om te controleren of de nieuwe Azure AD-gebruiker is toegevoegd. Toegang tot de Ambari Web **`https://CLUSTERNAME.azurehdinsight.net`** UI door te bladeren naar . Voer de gebruikersnaam en het wachtwoord van de clusterbeheerder in.

1. Selecteer **Ambari beheren** in het menu Beheer in het Ambari-dashboard. **admin**

    ![Apache Ambari dashboard Manage Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Selecteer **Gebruikers** onder de menugroep **Gebruikers + Groepsbeheer** aan de linkerkant van de pagina.

    ![HDInsight-gebruikers- en groepenmenu](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. De nieuwe gebruiker moet worden vermeld in de tabel Gebruikers. Het type is `LDAP` ingesteld `Local`op in plaats van .

    ![Overzicht van hdInsight aad-gebruikers](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Log in bij Ambari als de nieuwe gebruiker

Wanneer de nieuwe gebruiker (of een andere domeingebruiker) zich aanmeldt bij Ambari, gebruiken ze hun volledige Azure AD-gebruikersnaam en -referenties.  Ambari geeft een gebruikersalias weer, de weergavenaam van de gebruiker in Azure AD.
De nieuwe voorbeeldgebruiker heeft `hiveuser3@contoso.com`de gebruikersnaam. In Ambari, deze nieuwe `hiveuser3` gebruiker verschijnt als maar de `hiveuser3@contoso.com`gebruiker zich aanmeldt bij Ambari als .

## <a name="see-also"></a>Zie ook

* [Apache Hive-beleid configureren in HDInsight met ESP](hdinsight-domain-joined-run-hive.md)
* [HDInsight-clusters beheren met ESP](hdinsight-domain-joined-manage.md)
* [Gebruikers autoriseren voor Apache Ambari](hdinsight-authorize-users-to-ambari.md)
