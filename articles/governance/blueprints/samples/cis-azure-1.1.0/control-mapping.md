---
title: Beheeropties van blauwdrukvoorbeeld voor CIS Microsoft Azure Foundations Benchmark
description: Toewijzing van aanbevelingen uit het blauwdrukvoorbeeld CIS Microsoft Azure Foundations Benchmark aan Azure Policy.
ms.date: 05/12/2020
ms.topic: sample
ms.openlocfilehash: b6029e147af49cfb91078c6228615c32ad2db5fe
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84167227"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Toewijzing van aanbevelingen uit het blauwdrukvoorbeeld CIS Microsoft Azure Foundations Benchmark

In het volgende artikel ziet u hoe het blauwdrukvoorbeeld CIS Microsoft Azure Foundations Benchmark van Azure Blueprints is gekoppeld aan de aanbevelingen uit de CIS Microsoft Azure Foundations Benchmark (Engelstalig). Zie [CIS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/) voor meer informatie over de aanbevelingen.

Hieronder ziet u de toewijzingen naar de aanbevelingen in **CIS Microsoft Azure Foundations Benchmark v1.1.0**. Gebruik de navigatie aan de rechterkant om rechtstreeks naar een specifieke toewijzing van een aanbeveling te gaan.
Veel van de toegewezen aanbevelingen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md)-initiatief. Als u het complete initiatief wilt bekijken, opent u **Beleid** in de Azure-portal en selecteert u de pagina **Definities**. Zoek en selecteer vervolgens het ingebouwde beleidsinitiatief **\[Voorbeeld\] Aanbevelingen voor CIS Microsoft Azure Foundations Benchmark 1.1.0 controleren en specifieke ondersteunde VM-extensies implementeren**.

> [!IMPORTANT]
> Elke beheeroptie hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md)-definities. Met deze beleidsregels kunt u de [compliance beoordelen](../../../policy/how-to/get-compliance-data.md) met de beheeroptie. Er is echter vaak geen één-op-één- of volledige overeenkomst tussen een beheeroptie en een of meer beleidsregels. Als zodanig verwijst de term **Conform** in Azure Policy alleen naar de beleidsregels zelf. Dit garandeert niet dat u volledig conform bent met alle vereisten van een beheeroptie. Daarnaast bevat de compliance standaard beheeropties die op dit moment nog niet worden beschreven door Azure Policy-definities. Daarom is compliance in Azure Policy slechts een gedeeltelijke weergave van uw algemene compliancestatus. De koppelingen tussen de beheeropties en Azure Policy definities voor dit blauwdrukvoorbeeld voor compliance kunnen na verloop van tijd veranderen. Als u de wijzigingsgeschiedenis wilt bekijken, raadpleegt u de [GitHub Commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Ensure that multi-factor authentication is enabled for all privileged users

Met deze blauwdruk worden definities van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u controles kunt uitvoeren wanneer meervoudige verificatie niet is ingeschakeld voor bevoegde Azure Active Directory-accounts.

- MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Ensure that multi-factor authentication is enabled for all non-privileged users

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u controles kunt uitvoeren wanneer meervoudige verificatie niet is ingeschakeld voor niet-bevoegde Azure Active Directory-accounts.

- MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Ensure that there are no guest users

Met deze blauwdruk worden definities van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u kunt controleren op gastaccounts die mogelijk moeten worden verwijderd.

- Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 Ensure that no custom subscription owner roles are created

Met deze blauwdruk worden definities van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u kunt controleren op aangepaste rollen voor abonnementseigenaren die mogelijk moeten worden verwijderd.

- Aangepaste rollen voor abonnementseigenaren mogen niet bestaan

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 Ensure that standard pricing tier is selected

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u netwerken en virtuele machines kunt controleren waarop de prijscategorie Standaard voor Security Center niet is ingeschakeld.

- De prijscategorie Standard voor Security Center moet zijn geselecteerd

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 Ensure that 'Automatic provisioning of monitoring agent' is set to 'On'

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat automatisch inrichten van de Log Analytics-agent is ingeschakeld.

- Automatisch inrichten van de Log Analytics-agent voor controle moet zijn ingeschakeld voor uw abonnement

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Ensure ASC Default policy setting "Monitor System Updates" is not "Disabled"

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat systeemupdates worden geïnstalleerd op virtuele machines.

- Er moeten systeemupdates op uw computers worden geïnstalleerd

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Ensure ASC Default policy setting "Monitor OS Vulnerabilities" is not "Disabled"

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u kunt controleren op beveiligingsproblemen op virtuele machines die nog niet zijn opgelost.

- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Ensure ASC Default policy setting "Monitor Endpoint Protection" is not "Disabled"

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat eindpuntbeveiliging wordt ingeschakeld op virtuele machines.

- Ontbrekende Endpoint Protection bewaken in Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Ensure ASC Default policy setting "Monitor Disk Encryption" is not "Disabled"

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat schijven van virtuele machines worden versleuteld.

- Schijfversleuteling moet worden toegepast op virtuele machines

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 Ensure ASC Default policy setting "Monitor Network Security Groups" is not "Disabled"

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u internetgerichte virtuele machines kunt beveiligen.

- Aanbevelingen voor Adaptieve netwerkbeveiliging moeten worden toegepast op op internet gerichte virtuele machines

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 Ensure ASC Default policy setting "Enable Next Generation Firewall(NGFW) Monitoring" is not "Disabled"

Met deze blauwdruk worden definities van [Azure Policy](../../../policy/overview.md) toegewezen waarmee subnetten en virtuele machines worden beveiligd tegen bedreigingen door de toegang te beperken. Het Security Center-beleid waarnaar wordt verwezen door deze aanbeveling van CIS Microsoft Azure Foundations Benchmark is vervangen door twee nieuwe aanbevelingen. De onderstaande beleidsregels zijn gekoppeld aan de nieuwe aanbevelingen.

- Subnetten moeten worden gekoppeld aan een netwerkbeveiligingsgroep
- Op internet gerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Ensure ASC Default policy setting "Monitor Vulnerability Assessment" is not "Disabled"

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat beveiligingsproblemen worden gedetecteerd en opgelost.

- Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 Ensure ASC Default policy setting "Monitor Storage Blob Encryption" is not "Disabled"

Azure Storage-versleuteling is ingeschakeld voor alle nieuwe en bestaande opslagaccounts en kan niet worden uitgeschakeld. (Dit is een standaardfunctie van Azure; er is geen beleidstoewijzing.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Ensure ASC Default policy setting "Monitor JIT Network Access" is not "Disabled"

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u de toegang tot virtuele machines kunt beheren.

- Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 Ensure ASC Default policy setting "Monitor Adaptive Application Whitelisting" is not "Disabled"

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat besturingselementen voor adaptieve toepassingen zijn ingeschakeld op virtuele machines.

- Besturingselementen voor adaptieve toepassingen om veilige toepassingen in de toegestane lijst op te nemen, moeten worden ingeschakeld op uw computers

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 Ensure ASC Default policy setting "Monitor SQL Auditing" is not "Disabled"

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat controle op SQL-servers is ingeschakeld.

- Controle op SQL-servers moet zijn ingeschakeld

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Ensure ASC Default policy setting "Monitor SQL Encryption" is not "Disabled"

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat Transparent Data Encryption wordt ingeschakeld voor SQL-databases.

- Transparent Data Encryption in SQL-databases moet zijn ingeschakeld

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 Ensure that 'Security contact emails' is set

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat beveiligingsmeldingen correct zijn ingeschakeld.

- Er moet een e-mailadres van de contactpersoon voor beveiliging worden opgeven voor uw abonnement

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 Ensure that security contact 'Phone number' is set

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat beveiligingsmeldingen correct zijn ingeschakeld.

- Er moet een telefoonnummer van de contactpersoon voor beveiliging worden opgeven voor uw abonnement

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 Ensure that 'Send email notification for high severity alerts' is set to 'On'

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat beveiligingsmeldingen correct zijn ingeschakeld.

- E-mailmelding voor waarschuwingen met hoge urgentie moet zijn ingeschakeld

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 Ensure that 'Send email also to subscription owners' is set to 'On'

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat beveiligingsmeldingen correct zijn ingeschakeld.

- E-mailmelding aan abonnementseigenaar voor waarschuwingen met hoge urgentie moet zijn ingeschakeld

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Ensure that 'Secure transfer required' is set to 'Enabled'

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u opslagaccounts kunt controleren die onveilige verbindingen toestaan.

- Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Ensure default network access rule for Storage Accounts is set to deny

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u opslagaccounts kunt controleren die onbeperkte toegang toestaan.

- Netwerktoegang tot opslagaccounts moet zijn beperkt

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 Ensure 'Trusted Microsoft Services' is enabled for Storage Account access

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u opslagaccounts kunt controleren die geen toegang uit vertrouwde Microsoft-services toestaan.

- Opslagaccounts moeten toegang uit vertrouwde Microsoft-services toestaan

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Ensure that 'Auditing' is set to 'On'

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat controle op SQL-servers is ingeschakeld. 

- Controle op SQL-servers moet zijn ingeschakeld

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Ensure that 'AuditActionGroups' in 'auditing' policy for a SQL server is set properly

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat controle op SQL-servers juist is geconfigureerd.

- In de instellingen van SQL-controle moeten actiegroepen zijn geconfigureerd om kritieke activiteiten te kunnen vastleggen

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Ensure that 'Auditing' Retention is 'greater than 90 days'

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat SQL-serverlogboeken ten minste 90 dagen worden bewaard.

- SQL-servers moeten worden geconfigureerd met een bewaarperiode voor controleren van meer dan negentig dagen.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Ensure that 'Advanced Data Security' on a SQL server is set to 'On'

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat advanced data security is ingeschakeld op SQL Database- en SQL Managed Instance servers.

- Advanced Data Security moet zijn ingeschakeld op uw SQL-servers
- Advanced Data Security moet zijn ingeschakeld voor SQL Managed Instance

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 Ensure that 'Threat Detection types' is set to 'All'

Met deze blauwdruk worden definities van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat Advanced Threat Protection correct is geconfigureerd op SQL Database- en SQL Managed Instance-servers.

- Advanced Threat Protection-typen moeten worden ingesteld op All in de Advanced Data Security-instellingen van SQL Server
- Advanced Threat Protection-typen moeten ingesteld zijn op Alles in de Advanced Data Security-instellingen

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 Ensure that 'Send alerts to' is set

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat meldingen van Advanced Data Security correct zijn ingeschakeld.

- De Advanced Data Security-instellingen voor SQL Server moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- De Advanced Data Security-instellingen voor SQL Managed Instance moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Ensure that 'Email service and co-administrators' is 'Enabled'

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat meldingen van Advanced Data Security correct zijn ingeschakeld.

- E-mailmeldingen aan beheerders en abonnementseigenaren moeten zijn ingeschakeld in de Advanced Data Security-instellingen voor SQL Server
- E-mailmeldingen naar beheerders en abonnementseigenaren moeten zijn ingeschakeld in de Advanced Data Security-instellingen voor SQL Managed Instance

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Ensure that Azure Active Directory Admin is configured

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat er een Azure Active Directory-beheerder wordt ingericht voor SQL-servers.

- Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Ensure that 'Data encryption' is set to 'On' on a SQL Database

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat Transparent Data Encryption wordt ingeschakeld voor SQL-databases.

- Transparent Data Encryption in SQL-databases moet zijn ingeschakeld

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Ensure SQL server's TDE protector is encrypted with BYOK (Use your own key)

Met deze blauwdruk worden definities van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat de TDE-beveiliging voor SQL Database- en SQL Managed Instance-servers met uw eigen sleutel wordt versleuteld.

- TDE-beveiliging van de SQL-server moet worden versleuteld met uw eigen sleutel
- TDE-beveiliging van SQL Managed Instance moet worden versleuteld met uw eigen sleutel

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 Ensure 'Enforce SSL connection' is set to 'ENABLED' for MySQL Database Server

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat MySQL-databaseservers TLS/SSL-verbindingen afdwingen.

- SSL-verbinding afdwingen moet worden ingeschakeld voor MySQL-databaseservers

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 Ensure server parameter 'log_checkpoints' is set to 'ON' for PostgreSQL Database Server

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat PostgreSQL-databaseservers controlepunten vastleggen.

- Logboekcontrolepunten moeten zijn ingeschakeld voor PostgreSQL-databaseservers

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 Ensure 'Enforce SSL connection' is set to 'ENABLED' for PostgreSQL Database Server

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat PostgreSQL-databaseservers TLS/SSL-verbindingen afdwingen.

- SSL-verbinding afdwingen moet worden ingeschakeld voor PostgreSQL-databaseservers

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 Ensure server parameter 'log_connections' is set to 'ON' for PostgreSQL Database Server

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat PostgreSQL-databaseservers verbindingen vastleggen.

- Logboekverbindingen moeten zijn ingeschakeld voor PostgreSQL-databaseservers

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 Ensure server parameter 'log_disconnections' is set to 'ON' for PostgreSQL Database Server

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat PostgreSQL-databaseservers verbroken verbindingen vastleggen.

- Verbroken verbindingen moeten worden geregistreerd voor PostgreSQL-databaseservers.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 Ensure server parameter 'log_duration' is set to 'ON' for PostgreSQL Database Server

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat PostgreSQL-databaseservers de duur van voltooide instructies vastleggen.

- Logboekduur moet zijn ingeschakeld voor PostgreSQL-databaseservers

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 Ensure server parameter 'connection_throttling' is set to 'ON' for PostgreSQL Database Server

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat brute force-aanvallen op PostgreSQL-databaseservers worden beperkt.

- Verbinding beperken moet worden ingeschakeld voor PostgreSQL-databaseservers

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Ensure that Azure Active Directory Admin is configured

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat er een Azure Active Directory-beheerder wordt ingericht voor SQL-servers. De CIS Microsoft Azure Foundations Benchmark bevat deze aanbeveling, maar dit is echter een duplicaat van [aanbeveling 4.8](#48-ensure-that-azure-active-directory-admin-is-configured).

- Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Ensure that a Log Profile exists

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat er een logboekprofiel bestaat voor alle Azure-abonnementen. 

- Azure-abonnementen moeten een logboekprofiel voor het activiteitenlogboek hebben

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Ensure that Activity Log Retention is set 365 days or greater

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat activiteitenlogboeken ten minste één jaar worden bewaard.

- Het activiteitenlogboek moet ten minste één jaar worden bewaard

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Ensure audit profile captures all the activities

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat het logboekprofiel juist is geconfigureerd.

- Met het Azure Monitor-logboekprofiel moeten logboeken worden verzameld voor de categorieën schrijven, verwijderen en actie

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Ensure the log profile captures activity logs for all regions including global

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat het logboekprofiel juist is geconfigureerd.

- Azure Monitor moet activiteitenlogboeken uit alle regio's verzamelen

## <a name="516-ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>5.1.6 Ensure the storage account containing the container with activity logs is encrypted with BYOK (Use Your Own Key)

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat opslagaccounts die activiteitenlogboeken bevatten, worden versleuteld met BYOK.

- Het opslagaccount met de container met activiteitenlogboeken moet worden versleuteld met BYOK

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Ensure that logging for Azure KeyVault is 'Enabled'

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat diagnostische logboeken worden ingeschakeld voor kluizen.

- Diagnostische logboeken in Key Vault moeten zijn ingeschakeld

## <a name="521-ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>5.2.1 Ensure that Activity Log Alert exists for Create Policy Assignment

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat specifieke waarschuwingen worden vastgelegd in activiteitenlogboeken.

- Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beleidsbewerkingen

## <a name="522-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>5.2.2 Ensure that Activity Log Alert exists for Create or Update Network Security Group

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat specifieke waarschuwingen worden vastgelegd in activiteitenlogboeken.

- Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beheerbewerkingen

## <a name="523-ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>5.2.3 Ensure that Activity Log Alert exists for Delete Network Security Group

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat specifieke waarschuwingen worden vastgelegd in activiteitenlogboeken.

- Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beheerbewerkingen

## <a name="524-ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>5.2.4 Ensure that Activity Log Alert exists for Create or Update Network Security Group Rule

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat specifieke waarschuwingen worden vastgelegd in activiteitenlogboeken.

- Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beheerbewerkingen

## <a name="525-ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>5.2.5 Ensure that activity log alert exists for the Delete Network Security Group Rule

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat specifieke waarschuwingen worden vastgelegd in activiteitenlogboeken.

- Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beheerbewerkingen

## <a name="526-ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>5.2.6 Ensure that Activity Log Alert exists for Create or Update Security Solution

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat specifieke waarschuwingen worden vastgelegd in activiteitenlogboeken.

- Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beveiligingsbewerkingen

## <a name="527-ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>5.2.7 Ensure that Activity Log Alert exists for Delete Security Solution

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat specifieke waarschuwingen worden vastgelegd in activiteitenlogboeken.

- Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beveiligingsbewerkingen

## <a name="528-ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>5.2.8 Ensure that Activity Log Alert exists for Create or Update or Delete SQL Server Firewall Rule

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat specifieke waarschuwingen worden vastgelegd in activiteitenlogboeken.

- Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beheerbewerkingen

## <a name="529-ensure-that-activity-log-alert-exists-for-update-security-policy"></a>5.2.9 Ensure that Activity Log Alert exists for Update Security Policy

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat specifieke waarschuwingen worden vastgelegd in activiteitenlogboeken.

- Er moet een waarschuwing voor activiteitenlogboeken bestaan voor specifieke beveiligingsbewerkingen

## <a name="61-ensure-that-rdp-access-is-restricted-from-the-internet"></a>6.1 Ensure that RDP access is restricted from the internet

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat RDP-toegang is beperkt.

- RDP-toegang via internet moet worden geblokkeerd

## <a name="62-ensure-that-ssh-access-is-restricted-from-the-internet"></a>6.2 Ensure that SSH access is restricted from the internet

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat SSH-toegang is beperkt.

- SSH-toegang via internet moet worden geblokkeerd

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Ensure that Network Watcher is 'Enabled'

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat Network Watcher is ingeschakeld voor alle regio's waar resources worden geïmplementeerd. Voor dit beleid is een parametermatrix vereist die alle toepasselijke regio's opgeeft. De standaardwaarde in deze beleidsinitiatiefdefinitie is 'eastus'.

- Network Watcher moet zijn ingeschakeld

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Ensure that 'OS disk' are encrypted

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat schijfversleuteling wordt ingeschakeld op virtuele machines.

- Schijfversleuteling moet worden toegepast op virtuele machines

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Ensure that 'Data disks' are encrypted

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat schijfversleuteling wordt ingeschakeld op virtuele machines.

- Schijfversleuteling moet worden toegepast op virtuele machines

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 Ensure that 'Unattached disks' are encrypted

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat niet-gekoppelde schijven worden versleuteld.

- Niet-gekoppelde schijven moeten worden versleuteld

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 Ensure that only approved extensions are installed

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat alleen goedgekeurde extensies van virtuele machines worden geïnstalleerd. Voor dit beleid is een parametermatrix vereist die alle goedgekeurde extensies van virtuele machines opgeeft. Deze beleidsinitiatiefdefinitie bevat voorgestelde standaardwaarden die klanten moeten valideren. 

- Alleen goedgekeurde VM-extensies mogen worden geïnstalleerd

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Ensure that the latest OS Patches for all Virtual Machines are applied

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat systeemupdates worden geïnstalleerd op virtuele machines.

- Er moeten systeemupdates op uw computers worden geïnstalleerd

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Ensure that the endpoint protection for all Virtual Machines is installed

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat eindpuntbeveiliging wordt ingeschakeld op virtuele machines.

- Ontbrekende Endpoint Protection bewaken in Azure Security Center

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 Ensure the key vault is recoverable

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat kluisobjecten kunnen worden hersteld in het geval van onbedoeld verwijderen.

- Key Vault-objecten moeten herstelbaar zijn

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Enable role-based access control (RBAC) within Azure Kubernetes Services

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat op rollen gebaseerd toegangsbeheer wordt gebruikt voor beheerde machtigingen in clusters van de Kubernetes-service.

- Op rollen gebaseerd toegangsbeheer (RBAC) moet worden gebruikt voor Kubernetes Services

## <a name="91-ensure-app-service-authentication-is-set-on-azure-app-service"></a>9.1 Ensure App Service Authentication is set on Azure App Service

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat aanvragen voor App Service-apps worden geverifieerd.

- De verificatie moet worden ingeschakeld in uw API-app
- De verificatie moet worden ingeschakeld in uw Function-app
- De verificatie moet worden ingeschakeld in uw web-app

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Ensure web app redirects all HTTP traffic to HTTPS in Azure App Service

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat webtoepassingen alleen toegankelijk zijn via beveiligde verbindingen.

- Webtoepassing mag alleen toegankelijk zijn via HTTPS

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 Ensure web app is using the latest version of TLS encryption

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat web-apps de nieuwste versie van TLS gebruiken.

- De nieuwste TLS-versie moet worden gebruikt in uw API-app
- De nieuwste TLS-versie moet worden gebruikt in uw functie-app
- De nieuwste TLS-versie moet worden gebruikt in uw web-app

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 Ensure the web app has 'Client Certificates (Incoming client certificates)' set to 'On'

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat alleen clients met geldige certificaten een web-app kunnen bereiken.

- Controleren of de API-app client-certificaten (inkomende client-certificaten) heeft ingesteld op Aan
- Controleren of de clientcertificaten (inkomende clientcertificaten) zijn ingesteld op Aan voor de functie-app
- Controleren of clientcertificaten (inkomende clientcertificaten) zijn ingesteld op Aan voor de web-app

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 Ensure that Register with Azure Active Directory is enabled on App Service

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat web-apps een beheerde identiteit gebruiken.

- Controleren of registratie in Azure Active Directory is ingeschakeld voor de API-app
- Controleren of registratie in de actieve map Azure is ingeschakeld in de functie-app
- Controleren of registratie in de active map Azure is ingeschakeld in de web-app

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 Ensure that '.Net Framework' version is the latest, if used as a part of the web app

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat web-apps de nieuwste versie van .NET Framework gebruiken.

- Controleren of de versie van .NET Framework de meest recente is als deze wordt gebruikt als onderdeel van de API-app
- Controleren of de versie van .NET Framework de meest recente is als deze wordt gebruikt als onderdeel van de functie-app
- Controleren of de versie van .NET Framework de meest recente is als deze wordt gebruikt als onderdeel van de web-app

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 Ensure that 'PHP version' is the latest, if used to run the web app

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat web-apps de nieuwste versie van PHP gebruiken.

- Controleren of de PHP-versie de meest recente is als deze wordt gebruikt als onderdeel van de API-app
- Controleren of de PHP-versie de meest recente is als deze wordt gebruikt als onderdeel van de functie-app
- Controleren of de PHP-versie de meest recente is als deze wordt gebruikt als onderdeel van de web-app

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 Ensure that 'Python version' is the latest, if used to run the web app

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat web-apps de nieuwste versie van Python gebruiken.

- Controleren of de Python-versie de meest recente is als deze wordt gebruikt als onderdeel van de API-app
- Controleren of de Python-versie de meest recente is als deze wordt gebruikt als onderdeel van de functie-app
- Controleren of de Python-versie de meest recente is als deze wordt gebruikt als onderdeel van de web-app

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 Ensure that 'Java version' is the latest, if used to run the web app

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat web-apps de nieuwste versie van Java gebruiken.

- Controleren of de Java-versie de meest recente is als deze wordt gebruikt als onderdeel van de API-app
- Zorg ervoor dat de nieuwste versie van Java wordt gebruikt als onderdeel van de functie-app
- Controleren of de Java-versie de meest recente is als deze wordt gebruikt als onderdeel van de web-app

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 Ensure that 'HTTP Version' is the latest, if used to run the web app

Met deze blauwdruk wordt een definitie van [Azure Policy](../../../policy/overview.md) toegewezen waarmee u ervoor kunt zorgen dat web-apps de nieuwste versie van HTTP gebruiken.

- Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de API-app te openen
- Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de functie-app te openen
- Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de web-app te openen

## <a name="next-steps"></a>Volgende stappen

Nu u de toewijzing van beheeropties van de blauwdruk CIS Microsoft Azure Foundations Benchmark hebt bekeken, kunt u de volgende artikelen lezen voor meer informatie over de blauwdruk. Ga naar Azure Policy in de Azure-portal om het initiatief toe te wijzen:

> [!div class="nextstepaction"]
> [Blauwdruk CIS Microsoft Azure Foundations Benchmark: overzicht](./index.md)
> [Blauwdruk CIS Microsoft Azure Foundations Benchmark: implementatiestappen](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).