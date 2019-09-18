---
title: Gebruikers autoriseren voor Ambari-weer gaven-Azure HDInsight
description: Ambari-gebruikers-en groeps machtigingen voor HDInsight-clusters beheren waarvoor ESP is ingeschakeld.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: hrasheed
ms.openlocfilehash: 533bd750056f2e961ca9239e995fbfc62b2381d0
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076685"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Gebruikers machtigen voor Apache Ambari-weergaven

[Enterprise Security Package (ESP) ingeschakelde HDInsight-clusters](./domain-joined/hdinsight-security-overview.md) bieden mogelijkheden op ondernemings niveau, waaronder op Azure Active Directory gebaseerde verificatie. U kunt [nieuwe gebruikers synchroniseren](hdinsight-sync-aad-users-to-cluster.md) die zijn toegevoegd aan Azure ad-groepen die toegang tot het cluster hebben gekregen, zodat deze specifieke gebruikers bepaalde acties kunnen uitvoeren. Werken met gebruikers, groepen en machtigingen in [Apache Ambari](https://ambari.apache.org/) wordt ondersteund voor zowel ESP HDInsight-clusters als standaard HDInsight-clusters.

Active Directory kunnen gebruikers zich met hun domein referenties aanmelden bij de cluster knooppunten. Ze kunnen ook hun domein referenties gebruiken om cluster interacties te verifiëren met andere goedgekeurde eind punten, zoals [tint](https://gethue.com/), Ambari WEERGAVEN, ODBC, JDBC, Power shell en rest api's.

> [!WARNING]  
> Wijzig het wacht woord van de Ambari-watchdog (hdinsightwatchdog) niet in uw HDInsight-cluster op basis van Linux. Als u het wacht woord wijzigt, is het niet meer mogelijk om script acties te gebruiken of om schaal bewerkingen uit te voeren met uw cluster.

Als u dit nog niet hebt gedaan, volgt u [deze instructies](./domain-joined/apache-domain-joined-configure.md) om een nieuw ESP-cluster in te richten.

## <a name="access-the-ambari-management-page"></a>Toegang tot de Ambari-beheer pagina

Als u naar de **Ambari-beheer pagina** van de [Apache AMBARI-webgebruikersinterface](hdinsight-hadoop-manage-ambari.md)wilt **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** gaan, bladert u naar. Voer de gebruikers naam en het wacht woord in van de Cluster beheerder die u hebt gedefinieerd bij het maken van het cluster. Selecteer vervolgens in het Ambari-dash board de optie **Ambari beheren** onder het menu **beheerder** :

![Apache Ambari-dash board beheren](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Machtigingen verlenen voor het Apache Hive weer gaven

Ambari wordt geleverd met View instances voor [Apache Hive](https://hive.apache.org/) en [Apache TEZ](https://tez.apache.org/), onder andere. Ga naar de **beheer pagina**van het Ambari om toegang te verlenen aan een of meer Hive-exemplaren.

1. Selecteer op de pagina beheer de koppeling **weer gaven** in de kop van het menu **weer gaven** aan de linkerkant.

    ![Koppelingen voor weer gave Apache Ambari-weer gaven](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. Vouw op de pagina weer gaven de **Hive** -rij uit. Er is één standaard Hive-weer gave die wordt gemaakt wanneer de component service aan het cluster wordt toegevoegd. U kunt indien nodig ook meer Hive-weergave-instanties maken. Een Hive-weer gave selecteren:

    ![HDInsight-weer gaven-Apache Hive weer gave](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. Schuif naar de onderkant van de pagina weer geven. In het gedeelte *machtigingen* hebt u twee opties voor het verlenen van domein gebruikers hun machtigingen voor de weer gave:

**Machtigingen verlenen aan deze gebruikers** ![Machtigingen verlenen aan deze gebruikers](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**Machtigingen verlenen aan deze groepen** ![Machtigingen verlenen aan deze groepen](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. Als u een gebruiker wilt toevoegen, selecteert u de knop **gebruiker toevoegen** .

   * Begin met het typen van de gebruikers naam en er wordt een vervolg keuzelijst met eerder gedefinieerde namen weer geven.

     ![Apache Ambari-gebruiker automatisch aanvullen](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * Selecteer of voltooi de gebruikers naam. Selecteer de knop **Nieuw** om deze gebruikers naam toe te voegen als een nieuwe gebruiker.

   * Schakel het **selectie vakje blauw**in om uw wijzigingen op te slaan.

     ![Apache Ambari gebruikers machtigingen verlenen](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. Selecteer de knop **groep toevoegen** om een groep toe te voegen.

   * Begin met het typen van de groeps naam. Het proces van het selecteren van een bestaande groeps naam of het toevoegen van een nieuwe groep is hetzelfde als voor het toevoegen van gebruikers.
   * Schakel het **selectie vakje blauw**in om uw wijzigingen op te slaan.

     ![Apache Ambari Grant-machtigingen](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

Het rechtstreeks toevoegen van gebruikers aan een weer gave is handig wanneer u machtigingen wilt toewijzen aan een gebruiker om die weer gave te gebruiken, maar niet wilt dat ze lid zijn van een groep die extra machtigingen heeft. Om de hoeveelheid administratieve overhead te verminderen, is het eenvoudiger om machtigingen toe te wijzen aan groepen.

## <a name="grant-permissions-to-apache-tez-views"></a>Machtigingen verlenen voor Apache TEZ-weer gaven

Met de [Apache TEZ](https://tez.apache.org/) View-instanties kunnen gebruikers alle TEZ-taken bewaken en fouten opsporen, verzonden door [Apache Hive](https://hive.apache.org/) query's en [Apache Pig](https://pig.apache.org/) -scripts. Er is één standaard TEZ weer gave-exemplaar dat wordt gemaakt wanneer het cluster wordt ingericht.

Als u gebruikers en groepen wilt toewijzen aan een instantie van een TEZ-weer gave, vouwt u de rij **TEZ** op de pagina weer gaven uit, zoals eerder is beschreven.

![HDInsight-weer gaven-Apache TEZ View](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

Herhaal de stappen 3-5 in de vorige sectie om gebruikers of groepen toe te voegen.

## <a name="assign-users-to-roles"></a>Gebruikers toewijzen aan rollen

Er zijn vijf beveiligings rollen voor gebruikers en groepen, die worden vermeld in volg orde van het afnemen van toegangs machtigingen:

* Cluster beheerder
* Cluster operator
* Servicebeheerder
* Service operator
* Cluster gebruiker

Als u rollen wilt beheren, gaat u naar de **pagina beheer van Ambari**en selecteert u vervolgens de koppeling **rollen** in de menu groep *clusters* aan de linkerkant.

![Menu koppelingen voor Apache Ambari-rollen](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

Als u de lijst met machtigingen wilt zien die aan elke rol zijn gegeven, klikt u op het blauwe vraag teken naast de **tabelkop tabel op de pagina rollen.**

![Menu opdracht koppelings machtigingen voor Apache Ambari-rollen](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Menu opdracht koppelings machtigingen voor Apache Ambari-rollen")

Op deze pagina zijn er twee verschillende weer gaven die u kunt gebruiken voor het beheren van rollen voor gebruikers en groepen: Blok en lijst.

### <a name="block-view"></a>Blok weergave

In de blok weergave wordt elke rol in een eigen rij weer gegeven en worden de rollen **toegewezen aan deze gebruikers** , en **kunnen rollen aan deze groepen worden toegewezen** , zoals eerder beschreven.

![Blok weergave van Apache Ambari-rollen](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>Lijst weergave

De lijst weergave biedt snelle bewerkings mogelijkheden in twee categorieën: Gebruikers en groepen.

* De categorie gebruikers van de lijst weergave bevat een lijst met alle gebruikers, zodat u een rol voor elke gebruiker in de vervolg keuzelijst kunt selecteren.

    ![Weer gave Apache Ambari-rollen lijst-gebruikers](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  In de categorie groepen van de lijst weergave worden alle groepen weer gegeven, evenals de rol die aan elke groep is toegewezen. In ons voor beeld wordt de lijst met groepen gesynchroniseerd vanuit de Azure AD-groepen die zijn opgegeven in de eigenschap **gebruikers groep** van het cluster. Zie [een HDInsight-cluster maken waarvoor ESP is ingeschakeld](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Apache Ambari roles list view-groups](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    In de bovenstaande afbeelding is de groep ' hiveusers ' toegewezen aan de gebruikersrol *cluster* . Dit is een alleen-lezen rol waarmee de gebruikers van die groep de service configuraties en cluster metrieken kunnen bekijken, maar niet wijzigen.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Meld u aan bij Ambari als alleen-lezen gebruiker

We hebben onze machtigingen voor de Azure AD-domein gebruiker ' hiveuser1 ' toegewezen aan Hive-en TEZ-weer gaven. Wanneer we de Ambari-webgebruikersinterface starten en de domein referenties van deze gebruiker invoeren (Azure AD-gebruikers naam in e-mail indeling en wacht woord), wordt de gebruiker omgeleid naar de pagina Ambari weergaven. Hier kan de gebruiker elke toegankelijke weer gave selecteren. De gebruiker kan geen ander deel van de site bezoeken, met inbegrip van het dash board, de services, hosts, waarschuwingen of de beheer pagina's.

![Apache Ambari-gebruiker met alleen weer gaven](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Meld u aan bij Ambari als een cluster gebruiker

We hebben onze Azure AD-domein gebruiker ' hiveuser2 ' toegewezen aan de gebruikersrol *cluster* . Deze rol kan toegang krijgen tot het dash board en alle menu-items. Een cluster gebruiker heeft minder toegestane opties dan een beheerder. Hiveuser2 kan bijvoorbeeld configuraties voor elk van de services weer geven, maar niet bewerken.

![Apache Ambari-dashboard weergave](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Volgende stappen

* [Apache Hive beleid in HDInsight configureren met ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [ESP HDInsight-clusters beheren](./domain-joined/apache-domain-joined-manage.md)
* [De weer gave Apache Hive gebruiken met Apache Hadoop in HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Azure AD-gebruikers synchroniseren met het cluster](hdinsight-sync-aad-users-to-cluster.md)
