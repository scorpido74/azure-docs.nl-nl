---
title: Azure Active Directory gebruikers synchroniseren met een cluster-Azure HDInsight
description: Synchroniseer geverifieerde gebruikers van Azure Active Directory naar een HDInsight-cluster.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f58c847f512f2db72fdca823637192c3b638b1ae
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879386"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Azure Active Directory-gebruikers synchroniseren met een HDInsight-cluster

[HDInsight-clusters met Enterprise Security Package (ESP)](hdinsight-domain-joined-introduction.md) kunnen gebruikmaken van sterke verificatie met Azure Active Directory-gebruikers (Azure AD), maar ook *op rollen gebaseerd toegangs beheer* (RBAC)-beleid gebruiken. Wanneer u gebruikers en groepen aan Azure AD toevoegt, kunt u de gebruikers synchroniseren die toegang nodig hebben tot uw cluster.

## <a name="prerequisites"></a>Vereisten

Als u dit nog niet hebt gedaan, [maakt u een HDInsight-cluster met Enterprise Security Package](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Nieuwe Azure AD-gebruikers toevoegen

Als u uw hosts wilt weer geven, opent u de Ambari-webgebruikersinterface. Elk knoop punt wordt bijgewerkt met de nieuwe instellingen voor de installatie zonder toezicht.

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de Azure AD-adres lijst die is gekoppeld aan uw ESP-cluster.

2. Selecteer **alle gebruikers** in het menu aan de linkerkant en selecteer vervolgens **nieuwe gebruiker**.

    ![Deel venster alle gebruikers](./media/hdinsight-sync-aad-users-to-cluster/aad-users.png)

3. Vul het nieuwe gebruikers formulier in. Selecteer groepen die u hebt gemaakt voor het toewijzen van machtigingen op basis van het cluster. In dit voor beeld maakt u een groep met de naam ' HiveUsers ', waaraan u nieuwe gebruikers kunt toewijzen. De [voorbeeld instructies](hdinsight-domain-joined-configure.md) voor het maken van een ESP-cluster zijn onder `HiveUsers` andere `AAD DC Administrators`het toevoegen van twee groepen, en.

    ![Deel venster nieuwe gebruiker](./media/hdinsight-sync-aad-users-to-cluster/aad-new-user.png)

4. Selecteer **Maken**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>De Apache Ambari-REST API gebruiken om gebruikers te synchroniseren

Gebruikers groepen die zijn opgegeven tijdens het proces voor het maken van het cluster, worden op dat moment gesynchroniseerd. Gebruikers synchronisatie vindt automatisch één keer per uur plaats. Als u de gebruikers onmiddellijk wilt synchroniseren of als u een andere groep wilt synchroniseren dan de groepen die zijn opgegeven tijdens het maken van het cluster, gebruikt u de Ambari-REST API.

De volgende methode maakt gebruik van POST met de Ambari-REST API. Zie [HDInsight-clusters beheren met behulp van de Apache Ambari rest API](hdinsight-hadoop-manage-ambari-rest-api.md)voor meer informatie.

1. [Maak verbinding met uw cluster via SSH](hdinsight-hadoop-linux-use-ssh-unix.md). Selecteer in het deel venster Overzicht voor uw cluster in de Azure Portal de knop **Secure Shell (SSH)** .

    ![Secure Shell (SSH)](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-secure-shell.png)

2. Kopieer de weer `ssh` gegeven opdracht en plak deze in de SSH-client. Voer het SSH-gebruikers wachtwoord in wanneer u hierom wordt gevraagd.

3. Nadat de verificatie is uitgevoerd, voert u de volgende opdracht in:

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    Het antwoord moet er als volgt uitzien:

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. Als u de synchronisatie status wilt bekijken, voert `curl` u een nieuwe opdracht uit:

    ```bash
    curl -u admin:<YOUR PASSWORD> https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```
    
    Het antwoord moet er als volgt uitzien:
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
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

5. Dit geeft aan dat de status is **voltooid**, er een nieuwe gebruiker is gemaakt en er een lidmaatschap aan de gebruiker is toegewezen. In dit voor beeld wordt de gebruiker toegewezen aan de gesynchroniseerde LDAP-groep ' HiveUsers ', omdat de gebruiker is toegevoegd aan dezelfde groep in azure AD.

> [!NOTE]  
> De vorige methode synchroniseert alleen de Azure AD-groepen die zijn opgegeven in de eigenschap **gebruikers groep** van het domein instellingen tijdens het maken van het cluster. Zie [een HDInsight-cluster maken](domain-joined/apache-domain-joined-configure.md)voor meer informatie.

## <a name="verify-the-newly-added-azure-ad-user"></a>De zojuist toegevoegde Azure AD-gebruiker verifiëren

Open de [Web-UI van Apache Ambari](hdinsight-hadoop-manage-ambari.md) om te controleren of de nieuwe Azure AD-gebruiker is toegevoegd. Toegang tot de Ambari-Webgebruikersinterface door te bladeren naar **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** . Voer de gebruikers naam en het wacht woord van de Cluster beheerder in.

1. Selecteer in het Ambari-dash board **Ambari beheren** onder het menu **beheer** .

    ![Ambari beheren](./media/hdinsight-sync-aad-users-to-cluster/manage-ambari.png)

2. Selecteer **gebruikers** onder de menu groep **gebruikers-en groeps beheer** aan de linkerkant van de pagina.

    ![Menu-item gebruikers](./media/hdinsight-sync-aad-users-to-cluster/users-link.png)

3. De nieuwe gebruiker moet worden weer gegeven in de tabel gebruikers. Het type wordt ingesteld op `LDAP` `Local`in plaats van.

    ![Pagina voor Gebruikers](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Meld u aan bij Ambari als de nieuwe gebruiker

Wanneer de nieuwe gebruiker (of een andere domein gebruiker) zich aanmeldt bij Ambari, gebruiken ze hun volledige gebruikers naam en domein referenties voor Azure AD.  In Ambari wordt een gebruikers alias weer gegeven. Dit is de weergave naam van de gebruiker in azure AD. De nieuwe voorbeeld gebruiker heeft de gebruikers naam `hiveuser3@contoso.com`. In Ambari wordt deze nieuwe gebruiker weer gegeven als `hiveuser3` , maar de gebruiker meldt zich `hiveuser3@contoso.com`aan bij Ambari als.

## <a name="see-also"></a>Zie ook

* [Apache Hive beleid in HDInsight configureren met ESP](hdinsight-domain-joined-run-hive.md)
* [HDInsight-clusters beheren met ESP](hdinsight-domain-joined-manage.md)
* [Gebruikers autoriseren voor Apache Ambari](hdinsight-authorize-users-to-ambari.md)
