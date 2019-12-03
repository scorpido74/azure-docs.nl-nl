---
title: Voor beeld-besturings elementen in CIS Microsoft Azure Stichtings basis benchmarks
description: Aanbevelings toewijzing van het CIS-voor beeld van het DIS Microsoft Azure fundament voor de basis voor Azure Policy.
ms.date: 11/04/2019
ms.topic: sample
ms.openlocfilehash: 34d38f34dcd4233706f9b4578bc2dc2a644e4c2c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707416"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Aanbevelings toewijzing van het CIS-voor beeld van het DIS Microsoft Azure fundament-benchmarks

Het volgende artikel bevat informatie over de manier waarop de Azure-blauw drukken CIS Microsoft Azure Stichtings-blauw voor beeld wordt toegewezen aan de aanbevelingen voor de CIS-Microsoft Azure Stichting. Zie voor meer informatie over de aanbevelingen [CIS Microsoft Azure Stichting-benchmarks](https://www.cisecurity.org/benchmark/azure/).

De volgende toewijzingen zijn de aanbevelingen van het **CIS-Microsoft Azure basis benchmark v-1.1.0** . Gebruik de navigatie aan de rechter kant om rechtstreeks naar een specifieke aanbevelings toewijzing te gaan.
Veel van de toegewezen aanbevelingen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md) -initiatief. Als u het complete initiatief wilt bekijken, opent u **beleid** in het Azure Portal en selecteert u de pagina **definities** . Zoek en selecteer vervolgens de **\[Preview\] controle CIS Microsoft Azure fundering Bench Mark v 1.1.0 en implementeer specifieke VM-extensies ter ondersteuning** van de ingebouwde beleids initiatieven voor controle vereisten.

> [!IMPORTANT]
> Elk besturings element hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md) definities. Met deze beleids regels kunt u de naleving van het besturings element [beoordelen](../../../policy/how-to/get-compliance-data.md) . Er is echter vaak geen 1:1-of volledige overeenkomst tussen een besturings element en een of meer beleids regels. Als zodanig is de **naleving** in azure Policy alleen bedoeld voor het beleid zelf. Dit garandeert niet dat u volledig compatibel bent met alle vereisten van een besturings element. Daarnaast bevat de nalevings standaard besturings elementen die niet worden behandeld door Azure Policy definities op dit moment. Daarom is naleving in Azure Policy slechts een gedeeltelijke weer gave van uw algemene nalevings status. De koppelingen tussen de besturings elementen en Azure Policy definities voor dit voor beeld van deze naleving blauw druk kunnen na verloop van tijd veranderen. Als u de wijzigings geschiedenis wilt weer geven, raadpleegt u de [github commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 Controleer of multi-factor Authentication is ingeschakeld voor alle bevoegde gebruikers

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee u kunt controleren wanneer multi-factor Authentication niet is ingeschakeld voor bevoegde Azure Active Directory accounts.

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 Controleer of multi-factor Authentication is ingeschakeld voor alle gebruikers zonder privileged

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u kunt controleren wanneer multi-factor Authentication niet is ingeschakeld voor niet-geautoriseerde Azure Active Directory-accounts.

- MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 Zorg ervoor dat er geen gast gebruikers zijn

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe die u helpen bij het bewaken van Gast-accounts die mogelijk moeten worden verwijderd.

- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1,23 zorg ervoor dat er geen rollen voor aangepaste abonnements eigenaren worden gemaakt

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe die u helpen bij het bewaken van rollen van aangepaste abonnements eigenaren die mogelijk moeten worden verwijderd.

- De rollen van het aangepaste abonnements eigenaar mogen niet bestaan

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2,1 Controleer of de prijs categorie Standard is geselecteerd

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u netwerken en virtuele machines kunt bewaken waarop de laag Security Center standaard niet is ingeschakeld.

 - Security Center prijs categorie moet worden geselecteerd

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2,2 Controleer of ' automatische inrichting van monitoring agent ' is ingesteld op on

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat automatisch inrichten van de log Analytics agent is ingeschakeld.

- Het automatisch inrichten van de Log Analytics monitoring agent moet zijn ingeschakeld voor uw abonnement

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 Controleer of de standaard beleids instelling voor ASC ' systeem updates controleren ' niet is uitgeschakeld '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat systeem updates worden geïnstalleerd op virtuele machines.

- Systeem updates moeten worden geïnstalleerd op uw computers

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 Controleer of de standaard beleids instelling voor het controleren van de beveiligings lekken van het besturings systeem niet is uitgeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die u helpt bij het bewaken van beveiligings problemen met virtuele unremediated-machines.

- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 Controleer of de standaard beleids instelling voor ASC ' monitor Endpoint Protection ' niet is uitgeschakeld '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat Endpoint Protection is ingeschakeld op virtuele machines.

- Ontbrekende Endpoint Protection in Azure Security Center controleren

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 Controleer of de ASC-standaard beleids instelling schijf versleuteling controleren niet is uitgeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat de schijven van de virtuele machine zijn versleuteld.

- Schijf versleuteling moet worden toegepast op virtuele machines

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2,7 Controleer of de standaard beleids instelling voor ASC ' netwerk beveiligings groepen controleren ' niet is uitgeschakeld '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u Internet gerichte virtuele machines kunt beveiligen.

- De regels voor de netwerk beveiligings groep voor virtuele machines die zijn gericht op internet, moeten worden gehard

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2,8 Controleer of de standaard beleids instelling voor het controleren van de Web Application firewall is uitgeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die u helpt bij het beveiligen van virtuele machines waarop webtoepassingen worden uitgevoerd.

- De Nsg's-regels voor webtoepassingen op IaaS moeten een harde verbinding hebben

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2,9 Controleer of de standaard beleids instelling voor het inschakelen van de NGFW-bewaking (Next Generation firewall) is uitgeschakeld

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee subnetten en virtuele machines worden beschermd tegen bedreigingen door de toegang te beperken. Het Security Center beleid waarnaar wordt verwezen door deze CIS-Microsoft Azure fundament-benchmark aanbeveling is vervangen door twee nieuwe aanbevelingen. De onderstaande beleids regels verwijzen naar de nieuwe aanbevelingen.

- Subnetten moeten worden gekoppeld aan een netwerk beveiligings groep
- Virtuele machines moeten worden gekoppeld aan een netwerk beveiligings groep

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 zorg ervoor dat de standaard beleids instelling voor het controleren van het beveiligings niveau van de ASC niet is uitgeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u kunt voor komen dat beveiligings problemen worden gedetecteerd en hersteld.

- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2,11 Controleer of de standaard beleids instelling voor het bewaken van de opslag-BLOB-versleuteling niet is uitgeschakeld

Azure Storage versleuteling is ingeschakeld voor alle nieuwe en bestaande opslag accounts en kan niet worden uitgeschakeld. (Dit is een standaard Azure-functie; er is geen beleids toewijzing.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 Zorg ervoor dat ASC-standaard beleids instelling ' JIT-netwerk toegang controleren ' niet is uitgeschakeld '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u de toegang tot virtuele machines kunt beheren.

- Just-in-time-netwerk toegangs beheer moet worden toegepast op virtuele machines

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2,13 Controleer of de standaard beleids instelling voor het controleren van adaptieve toepassing white list niet is uitgeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat de besturings elementen voor adaptieve toepassingen worden ingeschakeld op virtuele machines.

- Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2,14 Controleer of de standaard beleids instelling voor de ASC ' controle van SQL-controle ' is niet ' uitgeschakeld '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die ervoor zorgt dat SQL Server auditing is ingeschakeld.

- Controle moet worden ingeschakeld voor geavanceerde instellingen voor gegevens beveiliging op SQL Server

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 Controleer of de standaard beleids instelling voor de ASC ' SQL-versleuteling controleren ' niet is uitgeschakeld '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat transparent Data Encryption is ingeschakeld voor SQL-data bases.

- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2,16 Controleer of ' beveiliging contact e-mails ' is ingesteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat beveiligings meldingen correct zijn ingeschakeld

- Voor uw abonnement moet een e-mail adres voor een beveiligings contact worden gegeven

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2,17 Controleer of het telefoon nummer van de contact persoon voor beveiliging is ingesteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat beveiligings meldingen correct zijn ingeschakeld

- Voor uw abonnement moet een telefoon nummer voor de contact persoon voor beveiliging worden gegeven

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2,18 Controleer of ' e-mail melding verzenden voor waarschuwingen met hoge urgentie ' is ingesteld op ' aan '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat beveiligings meldingen correct zijn ingeschakeld

- E-mail meldingen voor waarschuwingen met hoge urgentie moeten worden ingeschakeld

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2,19 zorg ervoor dat ' e-mail adres ook verzenden naar eigen aars van het abonnement ' is ingesteld op ' aan '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat beveiligings meldingen correct zijn ingeschakeld

- E-mail melding voor abonnements eigenaar voor waarschuwingen met hoge urgentie moet worden ingeschakeld

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 Controleer of ' beveiligde overdracht vereist ' is ingesteld op ingeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u opslag accounts kunt bewaken die niet-beveiligde verbindingen toestaan.

- Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 Controleer of de standaard regel voor netwerk toegang voor opslag accounts is ingesteld op weigeren

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u opslag accounts kunt bewaken die onbeperkte toegang toestaan.

- Onbeperkte netwerk toegang tot opslag accounts controleren

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3,8 Controleer of ' vertrouwde micro soft-Services ' is ingeschakeld voor toegang tot het opslag account

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u opslag accounts kunt bewaken die geen toegang mogen krijgen tot vertrouwde micro soft-Services.

- Opslag accounts moeten toegang toestaan vanuit vertrouwde micro soft-Services

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 Controleer of ' controle ' is ingesteld op ' aan '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die ervoor zorgt dat SQL Server auditing is ingeschakeld. 

- Controle moet worden ingeschakeld voor geavanceerde instellingen voor gegevens beveiliging op SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 Zorg ervoor dat ' AuditActionGroups ' in het beleid voor een SQL Server correct is ingesteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat de controle van SQL Server op de juiste wijze is geconfigureerd.

- Voor de SQL-controle-instellingen moeten actie-groepen zijn geconfigureerd voor het vastleggen van kritieke activiteiten

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 Controleer of de Bewaar periode van de controle groter is dan 90 dagen

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat SQL Server-logboeken ten minste 90 dagen worden bewaard.

- SQL-servers moeten worden geconfigureerd met controle dagen van meer dan 90 dagen.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 Controleer of ' geavanceerde gegevens beveiliging ' op een SQL-Server is ingesteld op on

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat geavanceerde gegevens beveiliging is ingeschakeld op SQL-servers en SQL Managed instances.

- Geavanceerde gegevens beveiliging moet zijn ingeschakeld voor uw door SQL beheerde instanties
- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 zorg ervoor dat ' bedreigingen detectie typen ' is ingesteld op ' all '

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee u ervoor kunt zorgen dat geavanceerde beveiliging tegen bedreigingen op de juiste wijze is geconfigureerd op SQL-servers en SQL Managed instances.

- De geavanceerde beveiligings typen voor bedreigingen moeten worden ingesteld op ' all ' in de instellingen voor geavanceerde gegevens beveiliging van SQL Server
- De geavanceerde beveiligings typen voor bedreigingen moeten worden ingesteld op ' all ' in de instellingen voor geavanceerde gegevens beveiliging van SQL Managed instance

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 Controleer of ' waarschuwingen verzenden naar ' is ingesteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat geavanceerde gegevens beveiligings meldingen correct zijn ingeschakeld.

- Geavanceerde instellingen voor gegevens beveiliging voor het beheerde exemplaar van SQL moeten een e-mail adres bevatten voor het ontvangen van beveiligings waarschuwingen
- Geavanceerde instellingen voor gegevens beveiliging voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van beveiligings waarschuwingen

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 Controleer of ' e-mail service en mede beheerders ' is ingeschakeld '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat geavanceerde gegevens beveiligings meldingen correct zijn ingeschakeld.

- E-mail meldingen voor beheerders en abonnements eigenaren moeten zijn ingeschakeld in de beveiligings instellingen voor geavanceerde gegevens van SQL Managed instance
- E-mailmeldingen voor beheerders en abonnementseigenaren moeten zijn ingeschakeld in de instellingen voor geavanceerde gegevensbeveiliging van de SQL-server

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 Zorg ervoor dat Azure Active Directory beheerder is geconfigureerd

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat een Azure Active Directory beheerder wordt ingericht voor SQL-servers.

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 Controleer of ' gegevens versleuteling ' is ingesteld op on ' op een SQL Database

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat transparent Data Encryption is ingeschakeld voor SQL-data bases.

- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 Controleer of de TDE-beveiliging van SQL Server is versleuteld met BYOK (gebruik uw eigen sleutel)

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee u ervoor kunt zorgen dat de transparante, gecodeerde gegevens beveiliging voor SQL-servers en SQL Managed instances met uw eigen sleutel worden versleuteld.

- TDE-beveiliging van SQL Managed instance moet worden versleuteld met uw eigen sleutel
- De TDE-beveiliging van de SQL-server moet zijn versleuteld met uw eigen sleutel

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4,11 Controleer of ' SSL-verbinding afdwingen ' is ingesteld op ingeschakeld voor MySQL-database server

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat MySQL-database servers SSL-verbindingen afdwingen.

- SSL-verbinding afdwingen moet zijn ingeschakeld voor MySQL-database servers

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4,12 Controleer of de server parameter log_checkpoints is ingesteld op ON voor de PostgreSQL-database server

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat PostgreSQL database servers logboek controlepunten registreren.

- Logboek controlepunten moeten zijn ingeschakeld voor PostgreSQL-database servers

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4,13 Controleer of ' SSL-verbinding afdwingen ' is ingesteld op ingeschakeld voor PostgreSQL-database server

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat PostgreSQL database servers SSL-verbindingen afdwingen.

- SSL-verbinding afdwingen moet zijn ingeschakeld voor PostgreSQL-database servers

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4,14 Controleer of de server parameter log_connections is ingesteld op ON voor de PostgreSQL-database server

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat PostgreSQL database servers verbinding maken met het logboek.

- Logboek verbindingen moeten zijn ingeschakeld voor PostgreSQL-database servers

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4,15 Controleer of de server parameter log_disconnections is ingesteld op ON voor de PostgreSQL-database server

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat PostgreSQL database servers verbinding met het logboek verbreken.

- Verbroken verbindingen moeten worden vastgelegd voor PostgreSQL-database servers.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4,16 Controleer of de server parameter log_duration is ingesteld op ON voor de PostgreSQL-database server

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat PostgreSQL database servers de duur van voltooide instructies registreren.

- De duur van het logboek moet zijn ingeschakeld voor PostgreSQL-database servers

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4,17 Controleer of de server parameter connection_throttling is ingesteld op ON voor de PostgreSQL-database server

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u beveiligings aanvallen op PostgreSQL-database servers kunt oplossen.

- Verbindings beperking moet zijn ingeschakeld voor PostgreSQL-database servers

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4,19 zorg ervoor dat Azure Active Directory beheerder is geconfigureerd

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat een Azure Active Directory beheerder wordt ingericht voor SQL-servers. CIS Microsoft Azure Stichtings benchmark bevat deze aanbeveling. het is echter een duplicaat van [aanbeveling 4,8](#48-ensure-that-azure-active-directory-admin-is-configured).

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 controleren of er een logboek profiel bestaat

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat er een logboek profiel bestaat voor alle Azure-abonnementen. 

- Azure-abonnementen moeten een logboek profiel voor het activiteiten logboek hebben

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Zorg ervoor dat de Bewaar periode van het activiteiten logboek 365 dagen of groter is ingesteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat activiteiten logboeken voor ten minste één jaar worden bewaard.

- Het activiteiten logboek moet ten minste één jaar worden bewaard

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 controleren of het controle profiel alle activiteiten vastlegt

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat het logboek profiel correct is geconfigureerd.

- Azure Monitor logboek profiel moet Logboeken verzamelen voor de categorieën schrijven, verwijderen, en actie

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Controleer of het logboek profiel activiteiten logboeken vastlegt voor alle regio's, waaronder globaal

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat het logboek profiel correct is geconfigureerd.

- Azure Monitor moet activiteiten logboeken van alle regio's verzamelen

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 zorgt ervoor dat logboek registratie voor Azure-sleutel kluis is ingeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat Diagnostische logboeken zijn ingeschakeld voor sleutel kluizen.

- Diagnostische logboeken in Key Vault moeten worden ingeschakeld

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6,5 Controleer of Network Watcher is ingeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat Network Watcher is ingeschakeld voor alle regio's waar resources worden geïmplementeerd. Voor dit beleid is een parameter matrix vereist die alle toepasselijke regio's opgeeft. De standaard waarde in deze beleids initiatief definitie is ' oostus '.

- Network Watcher moet zijn ingeschakeld

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 Zorg ervoor dat de besturingssysteem schijf is versleuteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat schijf versleuteling is ingeschakeld op virtuele machines.

- Schijf versleuteling moet worden toegepast op virtuele machines

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 Controleer of de gegevens schijven zijn versleuteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat schijf versleuteling is ingeschakeld op virtuele machines.

- Schijf versleuteling moet worden toegepast op virtuele machines

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7,3 Controleer of ' niet-gekoppelde schijven ' zijn versleuteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat niet-gekoppelde schijven worden versleuteld.

- Niet-gekoppelde schijven moeten worden versleuteld

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7,4 zorg ervoor dat alleen goedgekeurde uitbrei dingen zijn geïnstalleerd

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat alleen goedgekeurde extensies van de virtuele machine zijn geïnstalleerd. Voor dit beleid is een parameter matrix vereist waarmee alle goedgekeurde virtuele-machine uitbreidingen worden opgegeven. Deze beleids initiatief-definitie bevat voorgestelde standaard waarden die klanten moeten valideren. 

 - Alleen goedgekeurde VM-uitbrei dingen moeten worden geïnstalleerd

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 zorg ervoor dat de meest recente besturingssysteem patches voor alle Virtual Machines zijn toegepast

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat systeem updates worden geïnstalleerd op virtuele machines.

- Systeem updates moeten worden geïnstalleerd op uw computers

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 Controleer of Endpoint Protection voor alle Virtual Machines is geïnstalleerd

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat Endpoint Protection is ingeschakeld op virtuele machines.

- Ontbrekende Endpoint Protection in Azure Security Center controleren

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8,4 Controleer of de sleutel kluis kan worden hersteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat belang rijke kluis objecten kunnen worden hersteld in het geval van onbedoeld verwijderen.

- Key Vault objecten moeten worden hersteld

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 op rollen gebaseerd toegangs beheer (RBAC) in azure Kubernetes Services inschakelen

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat op rollen gebaseerd toegangs beheer wordt gebruikt voor beheerde machtigingen in Kubernetes-Service clusters

- \[preview\]: op rollen gebaseerde Access Control (RBAC) moet worden gebruikt voor Kubernetes-Services

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 Zorg ervoor dat web-apps alle HTTP-verkeer omleidt naar HTTPS in Azure App Service

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe waarmee u ervoor kunt zorgen dat webtoepassingen alleen toegankelijk zijn via beveiligde verbindingen.

- Webtoepassing mag alleen toegankelijk zijn via HTTPS

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9,3 Zorg ervoor dat web-apps de meest recente versie van TLS-code ring gebruikt

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee u ervoor kunt zorgen dat web-apps de meest recente TLS-versie gebruiken.

- De laatste TLS-versie moet worden gebruikt in uw API-app
- De meest recente versie van TLS moet worden gebruikt in uw functie-app
- De laatste TLS-versie moet worden gebruikt in uw web-app

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9,4 Controleer of de web-app ' client certificaten (binnenkomende client certificaten) ' is ingesteld op ' on '

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee u ervoor kunt zorgen dat alleen clients met geldige certificaten een web-app kunnen bereiken.

- Zorg ervoor dat de API-app ' client certificaten (binnenkomende client certificaten) ' is ingesteld op ' on '
- Zorg ervoor dat voor de functie-app client certificaten (binnenkomende client certificaten) zijn ingesteld op aan
- Zorg ervoor dat de WEB-app client certificaten (binnenkomende client certificaten) heeft ingesteld op on

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9,5 zorg ervoor dat registreren bij Azure Active Directory is ingeschakeld op App Service

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee u ervoor kunt zorgen dat web-apps een beheerde identiteit gebruiken.

- Zorg ervoor dat registreren met Azure Active Directory is ingeschakeld op API-app
- Zorg ervoor dat registreren met Azure Active Directory is ingeschakeld op functie-app
- Zorg ervoor dat registreren bij Azure Active Directory is ingeschakeld voor WEB-app

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9,6 Controleer of de versie van .NET Framework het meest recent is, als deze wordt gebruikt als onderdeel van de web-app

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee u ervoor kunt zorgen dat web-apps de nieuwste versie van .NET Framework gebruiken.

- Zorg ervoor dat .NET Framework versie het meest recent is, als deze wordt gebruikt als onderdeel van de API-app
- Zorg ervoor dat .NET Framework versie het meest recent is, als u deze gebruikt als onderdeel van de functie-app
- Zorg ervoor dat .NET Framework versie het meest recent is, als deze wordt gebruikt als onderdeel van de web-app

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9,7 Controleer of ' PHP-versie ' het meest recent is, als deze wordt gebruikt om de web-app uit te voeren

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee u ervoor kunt zorgen dat web-apps de nieuwste versie van PHP gebruiken.

- Zorg ervoor dat ' PHP-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de API-app
- Zorg ervoor dat ' PHP-versie ' de meest recente is, als deze wordt gebruikt als onderdeel van de functie-app
- Zorg ervoor dat ' PHP-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de WEB-app

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9,8 Controleer of ' python-versie ' het meest recent is, als deze wordt gebruikt om de web-app uit te voeren

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee u ervoor kunt zorgen dat web-apps de meest recente versie van python gebruiken.

- Controleer of ' python-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de API-app
- Controleer of ' python-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de functie-app
- Controleer of ' python-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de web-app

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9,9 Controleer of ' Java version ' de meest recente is, indien gebruikt voor het uitvoeren van de web-app

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee u ervoor kunt zorgen dat web-apps de meest recente versie van Java gebruiken.

- Zorg ervoor dat ' Java-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de API-app
- Zorg ervoor dat ' Java-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de funtion-app
- Zorg ervoor dat ' Java-versie ' het meest recent is, als deze wordt gebruikt als onderdeel van de web-app

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9,10 Controleer of ' HTTP-versie ' het meest recent is, als deze wordt gebruikt om de web-app uit te voeren

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe waarmee u ervoor kunt zorgen dat web-apps de nieuwste versie van http gebruiken.

- Zorg ervoor dat ' HTTP-versie ' het meest recent is, indien gebruikt voor het uitvoeren van de API-app
- Zorg ervoor dat HTTP-versie de meest recente is, als deze wordt gebruikt om de functie-app uit te voeren
- Zorg ervoor dat HTTP-versie de meest recente is, als deze wordt gebruikt om de web-app uit te voeren


## <a name="next-steps"></a>Volgende stappen

Nu u de beheer toewijzing van de CIS Microsoft Azure Stichtings-benchmark blauw drukken hebt bekeken, gaat u naar de volgende artikelen voor meer informatie over de blauw druk of gaat u naar Azure Policy in de Azure Portal om het initiatief toe te wijzen:

> [!div class="nextstepaction"]
> [Cis Microsoft Azure stichtings-benchmark blauw druk-overzicht](./index.md)
> [CIS Microsoft Azure Stichtings bebenchmarks blauw druk-stappen implementeren](./deploy.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).