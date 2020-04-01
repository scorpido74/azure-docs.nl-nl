---
title: CIS Microsoft Azure Foundations Benchmark blueprint sample besturingselementen
description: Aanbevelingstoewijzing van het blauwdrukvoorbeeld van de CIS Microsoft Azure Foundations Benchmark-blauwdruk voor Azure Policy.
ms.date: 11/04/2019
ms.topic: sample
ms.openlocfilehash: 34d38f34dcd4233706f9b4578bc2dc2a644e4c2c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74707416"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Aanbevelingstoewijzing van het blauwdrukvoorbeeld van de CIS Microsoft Azure Foundations Benchmark

In het volgende artikel wordt beschreven hoe de azure blueprints CIS Microsoft Azure Foundations Benchmark-blauwdrukvoorbeeld wordt toegewezen aan de aanbevelingen van De Microsoft Azure Foundations Benchmark van Het CIS. Zie [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/)voor meer informatie over de aanbevelingen.

De volgende toewijzingen zijn aan de **aanbevelingen van De Microsoft Azure Foundations Benchmark v1.1.0 van Het CIS.** Gebruik de navigatie aan de rechterkant om direct naar een specifieke aanbevelingstoewijzing te springen.
Veel van de toegewezen aanbevelingen worden geïmplementeerd met een [Azure Policy-initiatief.](../../../policy/overview.md) Als u het volledige initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities.** Zoek en selecteer vervolgens de ** \[aanbevelingen\] Preview Audit CIS Microsoft Azure Foundations Benchmark v1.1.0 en implementeer specifieke VM-extensies ter ondersteuning van ingebouwde** beleidsinitiatieven voor auditvereisten.

> [!IMPORTANT]
> Elk besturingselement hieronder is gekoppeld aan een of meer [Azure Policy-definities.](../../../policy/overview.md) Met dit beleid u [beoordelen of de](../../../policy/how-to/get-compliance-data.md) controle is nageleefd; Er is echter vaak geen 1:1 of volledige overeenkomst tussen een besturingselement en een of meer beleidsregels. **Compliant** in Azure Policy verwijst daarom alleen naar het beleid zelf. Dit zorgt er niet voor dat u volledig voldoet aan alle vereisten van een controle. Bovendien bevat de nalevingsstandaard besturingselementen die op dit moment niet worden behandeld door azure-beleidsdefinities. Naleving in Azure Policy is daarom slechts een gedeeltelijke weergave van uw algemene nalevingsstatus. De koppelingen tussen besturingselementen en Azure Policy-definities voor dit voorbeeld van nalevingsblauwdrukken kunnen in de loop van de tijd veranderen. Zie de [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md)om de wijzigingsgeschiedenis te bekijken.

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Ervoor zorgen dat meervoudige verificatie is ingeschakeld voor alle bevoorrechte gebruikers

In deze blauwdruk worden [Azure-beleidsdefinities](../../../policy/overview.md) toegerekend waarmee u controleren wanneer meervoudige verificatie niet is ingeschakeld op geprivilegieerde Azure Active Directory-accounts.

- MFA moet zijn ingeschakeld voor accounts met eigenaarmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Ervoor zorgen dat meervoudige verificatie is ingeschakeld voor alle niet-bevoorrechte gebruikers

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegerekend waarmee u controleren wanneer meervoudige verificatie niet is ingeschakeld op niet-geprivilegieerde Azure Active Directory-accounts.

- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Zorg ervoor dat er geen gastgebruikers zijn

In deze blauwdruk worden [Azure-beleidsdefinities](../../../policy/overview.md) toegetrokken waarmee u controleren op gastaccounts die mogelijk moeten worden verwijderd.

- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met leesmachtigingen moeten uit uw abonnement worden verwijderd
- Externe accounts met schrijfmachtigingen moeten uit uw abonnement worden verwijderd

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 Zorg ervoor dat er geen aangepaste eigenaarrollen voor abonnementen worden gemaakt

In deze blauwdruk worden [Azure-beleidsdefinities](../../../policy/overview.md) toegetrokken waarmee u controleren op aangepaste rollen voor abonnementseigenaren die mogelijk moeten worden verwijderd.

- Aangepaste eigenaarrollen voor abonnementen mogen niet bestaan

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 Ervoor zorgen dat de standaardprijslaag is geselecteerd

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u netwerken en virtuele machines controleren waar de standaardlaag beveiligingscentrum niet is ingeschakeld.

 - De standaardprijslaag van het Beveiligingscentrum moet worden geselecteerd

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 Ervoor zorgen dat "Automatische inrichting van de controleagent" wordt ingesteld op "Aan"

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u garanderen dat de log-analyse-agent automatisch wordt ingericht.

- Automatische inrichting van de Monitoringagent Log Analytics moet worden ingeschakeld op uw abonnement

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Ervoor zorgen dat ASC-standaardbeleidsinstelling 'Systeemupdates controleren' niet is uitgeschakeld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u ervoor zorgen dat systeemupdates op virtuele machines worden geïnstalleerd.

- Er moeten systeemupdates op uw computers zijn geïnstalleerd

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Ervoor zorgen dat ASC-standaardbeleid "Monitor OS Vulnerabilities" niet is uitgeschakeld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u controleren op niet-herstelde kwetsbaarheden in virtuele machines.

- Kwetsbaarheden in beveiligingsconfiguratie op uw machines moeten worden verholpen

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Ervoor zorgen dat ASC-standaardbeleid "Monitorendpoint Protection" niet is uitgeschakeld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u ervoor zorgen dat endpoint-beveiliging is ingeschakeld op virtuele machines.

- Ontbrekende endpointbeveiliging controleren in Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Ervoor zorgen dat ASC Standaardbeleidsinstelling 'Schijfversleuteling controleren' niet is uitgeschakeld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegekend waarmee u ervoor zorgen dat schijven van virtuele machines worden versleuteld.

- Schijfversleuteling moet worden toegepast op virtuele machines

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 Ervoor zorgen dat ASC-standaardbeleidsinstelling 'Netwerkbeveiligingsgroepen controleren' niet is uitgeschakeld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u virtuele machines met internetgericht gezinde apparaten beschermen.

- Netwerkbeveiligingsgroepregels voor internet waarmee virtuele machines worden geconfronteerd, moeten worden verhard

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 Ervoor zorgen dat ASC-standaardbeleidsinstelling "Monitor Web Application Firewall" niet is uitgeschakeld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u virtuele machines met webtoepassingen beschermen.

- De NSGs-regels voor webapplicaties op IaaS moeten worden verhard

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 Zorgen voor ASC-standaardbeleidsinstelling "Next Generation Firewall (NGFW) Monitoring inschakelen is niet "Uitgeschakeld"

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toekent waarmee subnetten en virtuele machines worden beschermd tegen bedreigingen door de toegang te beperken. Het Beveiligingscentrum-beleid waarnaar wordt verwezen door deze CIS Microsoft Azure Foundations Benchmark-aanbeveling is vervangen door twee nieuwe aanbevelingen. Het onderstaande beleid gaat over de nieuwe aanbevelingen.

- Subnetten moeten worden gekoppeld aan een netwerkbeveiligingsgroep
- Virtuele machines moeten worden gekoppeld aan een netwerkbeveiligingsgroep

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Ervoor zorgen dat ASC-standaardbeleid "Monitor Vulnerability Assessment" niet is uitgeschakeld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u ervoor zorgen dat kwetsbaarheden worden gedetecteerd en verholpen.

- Kwetsbaarheden moeten worden verholpen door een oplossing voor kwetsbaarheidsbeoordeling

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 Ervoor zorgen dat ASC Standaardbeleidsinstelling 'Monitoropslagblobversleuteling' niet is uitgeschakeld

Versleuteling door Azure Storage wordt ingeschakeld voor alle nieuwe en bestaande opslagaccounts en kan niet worden uitgeschakeld. (Dit is een standaardAzure-mogelijkheid; er is geen beleidstoewijzing.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Ervoor zorgen dat ASC Standaardbeleid instelling "Monitor JIT Network Access" is niet "Uitgeschakeld"

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u de toegang tot virtuele machines beheren.

- Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 Ervoor zorgen dat ASC Standaardbeleid instelling "Monitor Adaptive Application Whitelisting" is niet "Uitgeschakeld"

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegepast waarmee u garanderen dat Adaptieve toepassingsbesturingselementen zijn ingeschakeld op virtuele machines.

- Adaptieve toepassingsbesturingselementen moeten worden ingeschakeld op virtuele machines

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 Ervoor zorgen dat ASC-standaardbeleidsinstelling 'Monitor SQL Auditing' niet is uitgeschakeld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee sql-servercontrole is ingeschakeld.

- Controle moet worden ingeschakeld op geavanceerde instellingen voor gegevensbeveiliging op SQL Server

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Ervoor zorgen dat ASC-standaardbeleidsinstelling 'SQL-versleuteling controleren' niet is uitgeschakeld

Met deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegevoegd waarmee u garanderen dat transparante gegevensversleuteling is ingeschakeld in SQL-databases.

- Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 Ervoor zorgen dat 'E-mails met beveiligingscontact' worden ingesteld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) weergegeven waarmee u ervoor zorgen dat beveiligingsmeldingen correct zijn ingeschakeld

- Voor uw abonnement moet een e-mailadres voor beveiligingscontact worden opgegeven

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 Ervoor zorgen dat het beveiligingscontact 'Telefoonnummer' is ingesteld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) weergegeven waarmee u ervoor zorgen dat beveiligingsmeldingen correct zijn ingeschakeld

- Voor uw abonnement moet een telefoonnummer van een beveiligingscontactpersoon worden opgegeven

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 Zorg ervoor dat 'E-mailmelding verzenden voor waarschuwingen met hoge ernst' is ingesteld op 'Aan'

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) weergegeven waarmee u ervoor zorgen dat beveiligingsmeldingen correct zijn ingeschakeld

- E-mailmelding voor waarschuwingen met hoge ernst moet worden ingeschakeld

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 Zorg ervoor dat 'E-mail ook naar abonnementseigenaren' is ingesteld op 'Aan'

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) weergegeven waarmee u ervoor zorgen dat beveiligingsmeldingen correct zijn ingeschakeld

- E-mailmelding aan de eigenaar van een abonnement voor waarschuwingen met hoge ernst moet worden ingeschakeld

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Ervoor zorgen dat "Vereiste veilige overdracht" is ingesteld op "Ingeschakeld"

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u opslagaccounts controleren waarmee onveilige verbindingen mogelijk zijn.

- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Ervoor zorgen dat de standaardregel voor netwerktoegang voor opslagaccounts wordt ontzeggen

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u opslagaccounts controleren die onbeperkte toegang mogelijk maken.

- Onbeperkte netwerktoegang tot opslagaccounts controleren

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 Zorg ervoor dat 'Vertrouwde Microsoft Services' is ingeschakeld voor toegang tot een opslagaccount

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u opslagaccounts controleren die geen toegang toestaan vanuit vertrouwde Microsoft-services.

- Opslagaccounts moeten toegang bieden vanuit vertrouwde Microsoft-services

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Ervoor zorgen dat "Auditing" is ingesteld op "Aan"

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee sql-servercontrole is ingeschakeld. 

- Controle moet worden ingeschakeld op geavanceerde instellingen voor gegevensbeveiliging op SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Ervoor zorgen dat 'AuditActionGroups' in het 'auditing'-beleid voor een SQL-server naar behoren is ingesteld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegepast waarmee u ervoor zorgen dat sql-servercontrole correct is geconfigureerd.

- SQL-controle-instellingen moeten actiegroepen hebben geconfigureerd om kritieke activiteiten vast te leggen

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Ervoor zorgen dat de retentie van "auditing" "meer dan 90 dagen" bedraagt

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegekend waarmee u ervoor zorgen dat SQL-serverlogboeken ten minste 90 dagen worden bewaard.

- SQL-servers moeten worden geconfigureerd met controleretentiedagen van meer dan 90 dagen.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Zorg ervoor dat 'Advanced Data Security' op een SQL-server is ingesteld op 'Aan'

In deze blauwdruk wordt een [Azure Policy-definitie](../../../policy/overview.md) weergegeven waarmee u zorgen dat geavanceerde gegevensbeveiliging is ingeschakeld op SQL-servers en SQL-beheerde exemplaren.

- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-beheerde exemplaren
- Geavanceerde gegevensbeveiliging moet worden ingeschakeld op uw SQL-servers

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 Zorg ervoor dat "typen bedreigingsdetectie" zijn ingesteld op "Alles"

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toekent waarmee u zorgen dat geavanceerde bedreigingsbeveiliging correct is geconfigureerd op SQL-servers en SQL-beheerde exemplaren.

- Geavanceerde typen bedreigingsbeveiliging moeten worden ingesteld op 'Alles' in sql-server geavanceerde gegevensbeveiligingsinstellingen
- Geavanceerde typen bedreigingsbeveiliging moeten worden ingesteld op 'Alles' in SQL-beheerde instantie Advanced Data Security-instellingen

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 Ervoor zorgen dat 'Waarschuwingen verzenden naar' is ingesteld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegepast waarmee u ervoor zorgen dat geavanceerde meldingen voor gegevensbeveiliging naar behoren zijn ingeschakeld.

- Geavanceerde instellingen voor gegevensbeveiliging voor SQL-beheerde instantie moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- Geavanceerde instellingen voor gegevensbeveiliging voor SQL-server moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Zorg ervoor dat 'E-mailservice en medebeheerders' 'Ingeschakeld' is

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegepast waarmee u ervoor zorgen dat geavanceerde meldingen voor gegevensbeveiliging naar behoren zijn ingeschakeld.

- E-mailmeldingen aan beheerders en eigenaren van abonnementen moeten worden ingeschakeld in sql-beheerde instellingen voor geavanceerde gegevensbeveiliging
- E-mailmeldingen aan beheerders en eigenaren van abonnementen moeten worden ingeschakeld in geavanceerde gegevensbeveiligingsinstellingen van SQL-server

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Ervoor zorgen dat Azure Active Directory Admin is geconfigureerd

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegekend waarmee u garanderen dat een Azure Active Directory-beheerder is ingericht voor SQL-servers.

- Een Azure Active Directory-beheerder moet zijn ingericht voor SQL-servers

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Zorg ervoor dat 'Gegevensversleuteling' is ingesteld op 'Aan' in een SQL-database

Met deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegevoegd waarmee u garanderen dat transparante gegevensversleuteling is ingeschakeld in SQL-databases.

- Transparante gegevensversleuteling in SQL-databases moet worden ingeschakeld

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Zorg ervoor dat de TDE-beveiliging van SQL-server is versleuteld met BYOK (Gebruik uw eigen sleutel)

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toegekend waarmee u garanderen dat de transparante gegevensversleutelde beveiliging voor SQL-servers en SQL-beheerde exemplaren is versleuteld met uw eigen sleutel.

- SQL managed instance TDE protector moet worden versleuteld met uw eigen sleutel
- SQL server TDE protector moet worden versleuteld met uw eigen sleutel

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 Ervoor zorgen dat 'SSL-verbinding afdwingen' is ingesteld op 'ENABLED' voor MySQL Database Server

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u ervoor zorgen dat MySQL-databaseservers SSL-verbindingen afdwingen.

- SSL-verbinding afdwingen moet zijn ingeschakeld voor MySQL-databaseservers

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 Ervoor zorgen dat serverparameter 'log_checkpoints' is ingesteld op 'AAN' voor PostgreSQL Database Server

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u zorgen voor controlepunten voor PostgreSQL-databaseservers.

- Logcontrolepunten moeten zijn ingeschakeld voor PostgreSQL-databaseservers

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 Ervoor zorgen dat 'SSL-verbinding afdwingen' is ingesteld op 'ENABLED' voor PostgreSQL Database Server

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u ervoor zorgen dat PostgreSQL-databaseservers SSL-verbindingen afdwingen.

- SSL-verbinding afdwingen moet zijn ingeschakeld voor PostgreSQL-databaseservers

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 Ervoor zorgen dat serverparameter 'log_connections' is ingesteld op 'AAN' voor PostgreSQL Database Server

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u zorgen voor PostgreSQL-databaseservers logboekverbindingen.

- Logverbindingen moeten zijn ingeschakeld voor PostgreSQL-databaseservers

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 Ervoor zorgen dat serverparameter 'log_disconnections' is ingesteld op 'AAN' voor PostgreSQL Database Server

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u ervoor zorgen dat PostgreSQL-databaseservers verbroken verbindingen registreren.

- Ontkoppelingen moeten worden geregistreerd voor PostgreSQL-databaseservers.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 Ervoor zorgen dat serverparameter 'log_duration' is ingesteld op 'AAN' voor PostgreSQL Database Server

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u ervoor zorgen dat PostgreSQL-databaseservers de duur van voltooide instructies registreren.

- Logduur moet zijn ingeschakeld voor PostgreSQL-databaseservers

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 Ervoor zorgen dat serverparameter 'connection_throttling' is ingesteld op 'AAN' voor PostgreSQL Database Server

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) opgenomen waarmee u brute force-aanvallen op PostgreSQL-databaseservers beperken.

- Verbindingsbeperking moet worden ingeschakeld voor PostgreSQL-databaseservers

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Ervoor zorgen dat Azure Active Directory-beheerder is geconfigureerd

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegekend waarmee u garanderen dat een Azure Active Directory-beheerder is ingericht voor SQL-servers. CIS Microsoft Azure Foundations Benchmark bevat deze aanbeveling; Het is echter een duplicaat van [aanbeveling 4.8](#48-ensure-that-azure-active-directory-admin-is-configured).

- Een Azure Active Directory-beheerder moet zijn ingericht voor SQL-servers

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Ervoor zorgen dat er een logboekprofiel bestaat

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegewezen waarmee u ervoor zorgen dat er een logboekprofiel bestaat voor alle Azure-abonnementen. 

- Azure-abonnementen moeten een logboekprofiel hebben voor activiteitenlogboek

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Zorg ervoor dat het bewaren van activiteitenlogboeken 365 dagen of meer wordt ingesteld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegekend waarmee u ervoor zorgen dat activiteitslogboeken ten minste één jaar worden bewaard.

- Activiteitenlogboek moet ten minste één jaar worden bewaard

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Zorg ervoor dat het auditprofiel alle activiteiten vastlegt

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegepast waarmee u ervoor zorgen dat het logboekprofiel correct is geconfigureerd.

- Azure Monitor-logboekprofiel moet logboeken verzamelen voor categorieën 'schrijven', 'verwijderen' en 'actie'

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Zorg ervoor dat het logboekprofiel activiteitslogboeken vastlegt voor alle regio's, inclusief

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegepast waarmee u ervoor zorgen dat het logboekprofiel correct is geconfigureerd.

- Azure Monitor moet activiteitslogboeken uit alle regio's verzamelen

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Ervoor zorgen dat logboekregistratie voor Azure KeyVault 'ingeschakeld' is

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u controleren of diagnostische logboeken zijn ingeschakeld voor sleutelkluizen.

- Diagnostische logboeken in Key Vault moeten zijn ingeschakeld

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Zorg ervoor dat Network Watcher 'ingeschakeld' is

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegewezen waarmee u ervoor zorgen dat Network Watcher is ingeschakeld voor alle regio's waar resources worden geïmplementeerd. Voor dit beleid is een parameterarray vereist die alle toepasselijke regio's opgeeft. De standaardwaarde in deze beleidsinitiatiefdefinitie is 'eastus'.

- Network Watcher moet ingeschakeld zijn

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Zorg ervoor dat 'OS-schijf' versleuteld is

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegevoegd waarmee u ervoor zorgen dat schijfversleuteling is ingeschakeld op virtuele machines.

- Schijfversleuteling moet worden toegepast op virtuele machines

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Zorg ervoor dat 'Gegevensschijven' versleuteld zijn

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegevoegd waarmee u ervoor zorgen dat schijfversleuteling is ingeschakeld op virtuele machines.

- Schijfversleuteling moet worden toegepast op virtuele machines

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 Zorg ervoor dat 'Niet-gekoppelde schijven' versleuteld zijn

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegekend waarmee u ervoor zorgen dat niet-gekoppelde schijven worden versleuteld.

- Niet-bevestigde schijven moeten worden versleuteld

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 Zorg ervoor dat alleen goedgekeurde extensies worden geïnstalleerd

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegekend waarmee u ervoor zorgen dat alleen goedgekeurde virtuele machine-extensies worden geïnstalleerd. Dit beleid vereist een parameterarray die alle goedgekeurde virtuele machine-extensies opgeeft. Deze beleidsinitiatiefdefinitie bevat voorgestelde standaardwaarden die klanten moeten valideren. 

 - Alleen goedgekeurde VM-extensies moeten worden geïnstalleerd

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Zorg ervoor dat de nieuwste OS Patches voor alle virtuele machines worden toegepast

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u ervoor zorgen dat systeemupdates op virtuele machines worden geïnstalleerd.

- Er moeten systeemupdates op uw computers zijn geïnstalleerd

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Zorg ervoor dat de endpoint-beveiliging voor alle virtuele machines is geïnstalleerd

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u ervoor zorgen dat endpoint-beveiliging is ingeschakeld op virtuele machines.

- Ontbrekende endpointbeveiliging controleren in Azure Security Center

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 Zorg ervoor dat de sleutelkluis kan worden hersteld

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegewezen waarmee u ervoor zorgen dat belangrijke vault-objecten kunnen worden hersteld in het geval van onbedoelde verwijdering.

- Key Vault-objecten moeten kunnen worden hersteld

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 RBAC (Role-based access control) inschakelen binnen Azure Kubernetes Services

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) weergegeven waarmee u garanderen dat toegangsbeheer op basis van rollen wordt gebruikt voor beheerde machtigingen in Kubernetes-serviceclusters

- \[Preview\]: Role-Based Access Control (RBAC) moet worden gebruikt op Kubernetes Services

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Ervoor zorgen dat de web-app al het HTTP-verkeer doorstuurt naar HTTPS in Azure App Service

In deze blauwdruk wordt een [Azure-beleidsdefinitie](../../../policy/overview.md) toegegeven waarmee u ervoor zorgen dat webtoepassingen alleen toegankelijk zijn via beveiligde verbindingen.

- Webtoepassing mag alleen toegankelijk zijn via HTTPS

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 Zorg ervoor dat web-app de nieuwste versie van TLS-versleuteling gebruikt

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toekent waarmee u ervoor zorgen dat web-apps de nieuwste TLS-versie gebruiken.

- Nieuwste TLS-versie moet worden gebruikt in uw API-app
- Nieuwste TLS-versie moet worden gebruikt in uw functie-app
- Nieuwste TLS-versie moet worden gebruikt in uw web-app

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 Zorg ervoor dat de web-app 'Client Certificates (Incoming client certificates)' heeft ingesteld op 'Aan'

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toegevoegd waarmee u ervoor zorgen dat alleen clients met geldige certificaten een web-app kunnen bereiken.

- Ervoor zorgen dat API-app 'Clientcertificaten (Inkomende clientcertificaten)' heeft ingesteld op 'Aan'
- Zorgen voor functie-app heeft 'Client certificaten (Inkomende client certificaten)' ingesteld op 'Aan'
- Ervoor zorgen dat web-app 'Clientcertificates (Inkomende clientcertificaten)' is ingesteld op 'Aan'

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 Ervoor zorgen dat Registreren bij Azure Active Directory is ingeschakeld in De App-service

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toekent waarmee u ervoor zorgen dat web-apps een beheerde identiteit gebruiken.

- Ervoor zorgen dat Registreren met Azure Active Directory is ingeschakeld in de API-app
- Ervoor zorgen dat Registreren met Azure Active Directory is ingeschakeld in functie-app
- Controleren of Registreren met Azure Active Directory is ingeschakeld in WEB App

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 Zorg ervoor dat de versie van '.Net Framework' de nieuwste versie is, indien gebruikt als onderdeel van de web-app

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toekent waarmee u ervoor zorgen dat web-apps de nieuwste versie van het .Net Framework gebruiken.

- Ervoor zorgen dat de versie '.Net Framework' de nieuwste versie is, indien gebruikt als onderdeel van de API-app
- Ervoor zorgen dat de versie '.Net Framework' de nieuwste versie is, indien gebruikt als onderdeel van de functie-app
- Ervoor zorgen dat de versie '.Net Framework' de nieuwste versie is, indien gebruikt als onderdeel van de web-app

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 Zorg ervoor dat 'PHP-versie' de nieuwste is, indien gebruikt om de web-app uit te voeren

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toekent waarmee u ervoor zorgen dat web-apps de nieuwste versie van PHP gebruiken.

- Zorg ervoor dat 'PHP-versie' de nieuwste is, indien gebruikt als onderdeel van de Api-app
- Zorg ervoor dat 'PHP-versie' de nieuwste is, indien gebruikt als onderdeel van de functie-app
- Zorg ervoor dat 'PHP-versie' de nieuwste is, indien gebruikt als onderdeel van de WEB-app

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 Zorg ervoor dat 'Python-versie' de nieuwste is, indien gebruikt om de web-app uit te voeren

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toekent waarmee u ervoor zorgen dat web-apps de nieuwste versie van Python gebruiken.

- Zorg ervoor dat 'Python-versie' de nieuwste is, indien gebruikt als onderdeel van de Api-app
- Ervoor zorgen dat 'Python-versie' de nieuwste is, indien gebruikt als onderdeel van de functie-app
- Zorg ervoor dat 'Python-versie' de nieuwste is, indien gebruikt als onderdeel van de web-app

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 Zorg ervoor dat 'Java-versie' de nieuwste is, indien gebruikt om de web-app uit te voeren

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toegevoegd waarmee u ervoor zorgen dat web-apps de nieuwste versie van Java gebruiken.

- Zorg ervoor dat 'Java-versie' de nieuwste is, indien gebruikt als onderdeel van de Api-app
- Zorg ervoor dat 'Java-versie' de nieuwste is, indien gebruikt als onderdeel van de Funtion-app
- Zorg ervoor dat 'Java-versie' de nieuwste is, indien gebruikt als onderdeel van de web-app

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 Zorg ervoor dat 'HTTP-versie' de nieuwste is, indien gebruikt om de web-app uit te voeren

In deze blauwdruk worden [Azure Policy-definities](../../../policy/overview.md) toekent waarmee u ervoor zorgen dat web-apps de nieuwste versie van HTTP gebruiken.

- Zorg ervoor dat 'HTTP-versie' de nieuwste is, indien gebruikt om de Api-app uit te voeren
- Zorg ervoor dat 'HTTP-versie' de laatste is, indien gebruikt om de functie-app uit te voeren
- Ervoor zorgen dat 'HTTP-versie' de laatste is, indien gebruikt om de web-app uit te voeren


## <a name="next-steps"></a>Volgende stappen

Nu u de beheertoewijzing van de blauwdruk van de CIS Microsoft Azure Foundations Benchmark hebt beoordeeld, gaat u naar de volgende artikelen voor meer informatie over de blauwdruk of bezoekt u Azure Policy in de Azure-portal om het initiatief toe te wijzen:

> [!div class="nextstepaction"]
> [CIS Microsoft Azure Foundations Benchmark blauwdruk - Overzicht](./index.md)
> [CIS Microsoft Azure Foundations Benchmark blauwdruk - Deploy stappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van de blauwdruk](../../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../../concepts/resource-locking.md)
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).