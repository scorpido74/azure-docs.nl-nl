---
title: LDAP-synchronisatie in Ranger en Apache Ambari in Azure HDInsight
description: Pak de LDAP-synchronisatie aan in Ranger en Ambari en geef algemene richtlijnen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463217"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>LDAP-synchronisatie in Ranger en Apache Ambari in Azure HDInsight

HDInsight Enterprise Security Package (ESP) clusters gebruiken Ranger voor autorisatie. Apache Ambari en Ranger synchroniseren gebruikers en groepen onafhankelijk en werken een beetje anders. Dit artikel is bedoeld om de LDAP-synchronisatie in Ranger en Ambari aan te pakken.

## <a name="general-guidelines"></a>Algemene richtlijnen

* Implementeer altijd clusters met groepen.
* In plaats van groepsfilters te wijzigen in Ambari en Ranger, probeert u deze allemaal te beheren in Azure AD en gebruikt u geneste groepen om de vereiste gebruikers binnen te halen.
* Zodra een gebruiker is gesynchroniseerd, wordt deze niet verwijderd, zelfs niet als de gebruiker geen deel uitmaakt van de groepen.
* Als u de LDAP-filters rechtstreeks moet wijzigen, gebruikt u de gebruikersinterface eerst omdat deze enkele validaties bevat.

## <a name="users-are-synced-separately"></a>Gebruikers worden afzonderlijk gesynchroniseerd

Ambari en Ranger delen de gebruikersdatabase niet omdat ze twee verschillende doeleinden dienen. Als een gebruiker de Ambari-gebruikersinterface moet gebruiken, moet de gebruiker worden gesynchroniseerd met Ambari. Als de gebruiker niet is gesynchroniseerd met Ambari, zal Ambari UI / API het weigeren, maar andere delen van het systeem werken (deze worden bewaakt door Ranger of Resource Manager en niet Ambari). Als u de gebruiker wilt opnemen in een Ranger-beleid, synchroniseert u de gebruiker met Ranger.

Wanneer een beveiligd cluster wordt geïmplementeerd, worden groepsleden transitief gesynchroniseerd (alle subgroepen en hun leden) met zowel Ambari als Ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Ambari-gebruikerssynchronisatie en -configuratie

Vanaf de hoofdknooppunten wordt `/opt/startup_scripts/start_ambari_ldap_sync.py`elk uur een cron-taak uitgevoerd om de gebruikerssynchronisatie te plannen. De cron job roept de Ambari rest API's om de synchronisatie uit te voeren. Het script dient een lijst in met gebruikers en groepen om te synchroniseren (omdat de gebruikers mogelijk niet tot de opgegeven groepen behoren, worden beide afzonderlijk opgegeven). Ambari synchroniseert de sAMAccountName als de gebruikersnaam en alle groepsleden, transitief.

De logs moeten `/var/log/ambari-server/ambari-server.log`in . Zie [Ambari-logboekniveau configureren voor](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)meer informatie .

In de clusters Van Data Lake wordt de haak voor het maken van de postgebruiker gebruikt om de thuismappen voor de gesynchroniseerde gebruikers te maken en worden ze ingesteld als de eigenaren van de thuismappen. Als de gebruiker niet correct is gesynchroniseerd met Ambari, kan de gebruiker te maken krijgen met fouten bij het openen van tijdelijke mappen en andere tijdelijke mappen.

### <a name="update-groups-to-be-synced-to-ambari"></a>Groepen bijwerken die moeten worden gesynchroniseerd met Ambari

Als u groepslidmaatschappen in Azure AD niet beheren, hebt u twee opties:

* Voer een eenmalige synchronisatie uit zoals beter beschreven bij [LDAP-gebruikers en -groepen synchroniseren](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Wanneer het groepslidmaatschap verandert, moet u deze synchronisatie opnieuw doen.

* Schrijf een cron-taak, bel de [Ambari API periodiek](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) met de nieuwe groepen.

## <a name="ranger-user-sync-and-configuration"></a>Synchronisatie en configuratie van ranger-gebruikers

Ranger heeft een ingebouwde sync engine die elk uur draait om de gebruikers te synchroniseren. Het deelt de gebruikersdatabase niet met Ambari. HDInsight configureert het zoekfilter om de beheerdersgebruiker, de watchdog-gebruiker en de leden van de groep te synchroniseren die tijdens het maken van het cluster zijn opgegeven. De groepsleden worden transitief gesynchroniseerd:

* Incrementele synchronisatie uitschakelen.
* Synchronisatiekaart voor gebruikersgroepen inschakelen.
* Geef het zoekfilter op om de tijdelijke groepsleden op te nemen.
* SAMAccountName synchroniseren voor gebruikers en naamkenmerk voor groepen.

### <a name="group-or-incremental-sync"></a>Groeps- of incrementele synchronisatie

Ranger ondersteunt een groepssynchronisatieoptie, maar het werkt als een kruising met gebruikersfilter. Geen unie tussen groepslidmaatschappen en gebruikersfilter. Een typische use case voor groepssynchronisatiefilter in Ranger is - groepsfilter: (dn=clusteradmingroup), gebruikersfilter: (city=seattle).

Incrementele synchronisatie werkt alleen voor de gebruikers die al zijn gesynchroniseerd (de eerste keer). Incrementeel synchroniseert geen nieuwe gebruikers die na de eerste synchronisatie aan de groepen zijn toegevoegd.

### <a name="update-ranger-sync-filter"></a>Synchronisatiefilter Ranger bijwerken

Het LDAP-filter is te vinden in de Ambari-gebruikersinterface, onder de sectie Ranger-gebruikerssynchronisatieconfiguratie. Het bestaande filter bevindt `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`zich in het formulier . Zorg ervoor dat u predicaat aan het `net ads` einde toevoegt en test het filter met behulp van de zoekopdracht of ldp.exe of iets dergelijks.

## <a name="ranger-user-sync-logs"></a>Logboeken voor het synchroniseren van gebruikers van ranger's

Ranger gebruiker sync kan gebeuren uit een van de headnodes. De logs `/var/log/ranger/usersync/usersync.log`zijn in . Ga als volgt te werk om de verbositeit van de logboeken te vergroten:

1. Log in bij Ambari.
1. Ga naar de Ranger configuratie sectie.
1. Ga naar de sectie Geavanceerd **usersync-log4j.**
1. Verander `log4j.rootLogger` het `DEBUG` niveau (Na wijziging `log4j.rootLogger = DEBUG,logFile,FilterLog`moet het eruit zien).
1. Sla de configuratie op en start de ranger opnieuw op.

## <a name="next-steps"></a>Volgende stappen

* [Verificatieproblemen in Azure HDInsight](./domain-joined-authentication-issues.md)
* [Azure AD-gebruikers synchroniseren met een HDInsight-cluster](../hdinsight-sync-aad-users-to-cluster.md)
