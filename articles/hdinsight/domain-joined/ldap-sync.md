---
title: LDAP-synchronisatie in zwerver en Apache Ambari in azure HDInsight
description: Adresseer de LDAP-synchronisatie in zwerver en Ambari en geef algemene richt lijnen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77463217"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>LDAP-synchronisatie in zwerver en Apache Ambari in azure HDInsight

HDInsight-clusters (ESP-Enterprise Security Package) gebruiken zwerver voor autorisatie. Apache Ambari en zwerver synchroniseren gebruikers en groepen onafhankelijk en werken iets anders. Dit artikel is bedoeld om de LDAP-synchronisatie in zwerver en Ambari te verhelpen.

## <a name="general-guidelines"></a>Algemene richtlijnen

* Implementeer altijd clusters met groepen.
* In plaats van groeps filters in Ambari en zwerver te wijzigen, kunt u deze allemaal beheren in azure AD en geneste groepen gebruiken om de vereiste gebruikers mee te nemen.
* Zodra een gebruiker is gesynchroniseerd, wordt deze niet verwijderd, zelfs niet als de gebruiker geen deel uitmaakt van de groepen.
* Als u de LDAP-filters rechtstreeks wilt wijzigen, moet u eerst de gebruikers interface gebruiken, aangezien deze een aantal validaties bevat.

## <a name="users-are-synced-separately"></a>Gebruikers worden afzonderlijk gesynchroniseerd

Ambari en zwerver delen de gebruikers database niet omdat ze twee verschillende doel einden hebben. Als een gebruiker de Ambari-gebruikers interface moet gebruiken, moet de gebruiker worden gesynchroniseerd met Ambari. Als de gebruiker niet is gesynchroniseerd met Ambari, wordt deze door Ambari UI/API geweigerd, maar andere onderdelen van het systeem werken (deze worden beschermd door zwerver of Resource Manager en niet Ambari). Als u de gebruiker wilt toevoegen in een zwerver-beleid, synchroniseert u de gebruiker vervolgens naar zwerver.

Wanneer een beveiligd cluster wordt geïmplementeerd, worden groeps leden in transitief (alle subgroepen en hun leden) gesynchroniseerd naar zowel Ambari als zwerver. 

## <a name="ambari-user-sync-and-configuration"></a>Gebruikers synchronisatie en-configuratie Ambari

Vanuit de hoofd knooppunten wordt een cron-taak, `/opt/startup_scripts/start_ambari_ldap_sync.py` , elke uur uitgevoerd om de gebruikers synchronisatie te plannen. De cron-taak roept de Ambari rest Api's aan om de synchronisatie uit te voeren. Het script verzendt een lijst met gebruikers en groepen die moeten worden gesynchroniseerd (aangezien de gebruikers geen deel uitmaken van de opgegeven groepen, worden beide afzonderlijk opgegeven.) Ambari synchroniseert de sAMAccountName als de gebruikers naam en alle groeps leden, transitief.

De logboeken moeten zijn in `/var/log/ambari-server/ambari-server.log` . Zie [Configure Ambari Logging Level](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)(Engelstalig) voor meer informatie.

In Data Lake clusters wordt de hook voor het maken van de gebruiker post gebruikt voor het maken van de basis mappen voor de gesynchroniseerde gebruikers en ze zijn ingesteld als eigen aren van de basis mappen. Als de gebruiker niet correct is gesynchroniseerd met Ambari, kan de gebruiker storingen in de toegang tot tijdelijke bestanden en andere tijdelijke mappen oplossen.

### <a name="update-groups-to-be-synced-to-ambari"></a>Update groepen die moeten worden gesynchroniseerd met Ambari

Als u de lidmaatschappen van groepen in azure AD niet kunt beheren, hebt u twee opties:

* Voer een eenmalige synchronisatie uit, zoals wordt beschreven om [LDAP-gebruikers en-groepen te synchroniseren](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Wanneer het groepslid maatschap wordt gewijzigd, moet u deze synchronisatie opnieuw uitvoeren.

* Schrijf een cron-taak en roep de [Ambari-API regel matig](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) aan met de nieuwe groepen.

## <a name="ranger-user-sync-and-configuration"></a>Zwerver-gebruikers synchronisatie en-configuratie

Zwerver heeft een ingebouwde synchronisatie-engine die elk uur wordt uitgevoerd om de gebruikers te synchroniseren. De gebruikers database wordt niet gedeeld met Ambari. HDInsight configureert het zoek filter om de gebruiker met beheerders rechten, de watchdog-gebruiker en de leden van de groep die is opgegeven tijdens het maken van het cluster te synchroniseren. De groeps leden worden transitief gesynchroniseerd:

* Incrementele synchronisatie uitschakelen.
* Synchronisatie toewijzing van gebruikers groep inschakelen.
* Geef het zoek filter op waarmee de leden van de transitieve groep moeten worden toegevoegd.
* Synchroniseer sAMAccountName voor gebruikers en naam kenmerk voor groepen.

### <a name="group-or-incremental-sync"></a>Groep of incrementele synchronisatie

Zwerver ondersteunt een optie voor groeps synchronisatie, maar dit werkt als een snij punt met gebruikers filter. Geen samen voeging tussen groepslid maatschappen en gebruikers filter. Een typisch gebruiks voorbeeld voor een groeps synchronisatie filter in zwerver is-groeps filter: (DN = clusteradmingroup), gebruikers filter: (city = Seattle).

Incrementele synchronisatie werkt alleen voor gebruikers die al zijn gesynchroniseerd (de eerste keer). Met incrementeel worden nieuwe gebruikers die zijn toegevoegd aan de groepen, niet gesynchroniseerd na de initiële synchronisatie.

### <a name="update-ranger-sync-filter"></a>Synchronisatie filter zwerver bijwerken

U vindt het LDAP-filter in de Ambari-gebruikers interface, in de sectie zwerver-gebruikers synchronisatie configureren. Het bestaande filter wordt in het formulier weer `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . Zorg ervoor dat u aan het einde een predikaat toevoegt en test het filter met behulp van de `net ads` Zoek opdracht of het ldp.exe of iets dergelijks.

## <a name="ranger-user-sync-logs"></a>Zwerver gebruikers synchronisatie logboeken

Zwerver-gebruikers synchronisatie kan worden uitgevoerd op een van de hoofd knooppunten. De logboeken zijn in `/var/log/ranger/usersync/usersync.log` . Voer de volgende stappen uit om de uitgebreider van de logboeken te verg Roten:

1. Meld u aan bij Ambari.
1. Ga naar de configuratie sectie zwerver.
1. Ga naar de sectie Geavanceerde **usersync-log4j** .
1. Wijzig de `log4j.rootLogger` waarde in op `DEBUG` niveau (na wijziging moet er als volgt uitzien `log4j.rootLogger = DEBUG,logFile,FilterLog` ).
1. Sla de configuratie op en start zwerver opnieuw op.

## <a name="next-steps"></a>Volgende stappen

* [Verificatie problemen in azure HDInsight](./domain-joined-authentication-issues.md)
* [Azure AD-gebruikers synchroniseren met een HDInsight-cluster](../hdinsight-sync-aad-users-to-cluster.md)
