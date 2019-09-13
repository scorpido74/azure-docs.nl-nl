---
title: Voor beeld-CIS Microsoft Azure Stichtings basis-benchmark test blauw druk-Aanbevelings toewijzing
description: Aanbevelings toewijzing van het CIS-voor beeld van het DIS Microsoft Azure fundament voor de basis voor Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/09/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f048262003a3567175c40ebf4ee744c41e11b5f9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918695"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Aanbevelings toewijzing van het CIS-voor beeld van het DIS Microsoft Azure fundament-benchmarks

Het volgende artikel bevat informatie over de manier waarop de Azure-blauw drukken CIS Microsoft Azure Stichtings-blauw voor beeld wordt toegewezen aan de aanbevelingen voor de CIS-Microsoft Azure Stichting. Zie voor meer informatie over de aanbevelingen [CIS Microsoft Azure Stichting-benchmarks](https://www.cisecurity.org/benchmark/azure/).

De volgende toewijzingen zijn de aanbevelingen van het **CIS-Microsoft Azure basis benchmark v-1.1.0** . Gebruik de navigatie aan de rechter kant om rechtstreeks naar een specifieke aanbevelings toewijzing te gaan.
Veel van de toegewezen aanbevelingen worden geïmplementeerd met een [Azure Policy](../../../policy/overview.md) -initiatief. Als u het complete initiatief wilt bekijken, opent u **beleid** in het Azure Portal en selecteert u de pagina **definities** . Zoek en selecteer vervolgens de  **\[preview\] -controle CIS Microsoft Azure basis Bench Mark v 1.1.0 aanbevelingen en implementeer specifieke VM-extensies ter ondersteuning** van de ingebouwde beleids initiatieven voor controle vereisten.

> [!IMPORTANT]
> Elk besturings element hieronder is gekoppeld aan een of meer [Azure Policy](../../../policy/overview.md) definities. Met deze beleids regels kunt u de naleving van het besturings element [beoordelen](../../../policy/how-to/get-compliance-data.md) . Er is echter vaak geen 1:1-of volledige overeenkomst tussen een besturings element en een of meer beleids regels. Als zodanig is de **naleving** in azure Policy alleen bedoeld voor het beleid zelf. Dit garandeert niet dat u volledig compatibel bent met alle vereisten van een besturings element. Daarnaast bevat de nalevings standaard besturings elementen die niet worden behandeld door Azure Policy definities op dit moment. Daarom is naleving in Azure Policy slechts een gedeeltelijke weer gave van uw algemene nalevings status. De koppelingen tussen de besturings elementen en Azure Policy definities voor dit voor beeld van deze naleving blauw druk kunnen na verloop van tijd veranderen. Als u de wijzigings geschiedenis wilt weer geven, raadpleegt u de [github commit-geschiedenis](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 Controleer of multi-factor Authentication is ingeschakeld voor alle bevoegde gebruikers

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe die zijn afgestemd op deze CIS-aanbeveling.

- MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement
- MFA moet zijn ingeschakeld voor accounts met schrijf machtigingen voor uw abonnement

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 Controleer of multi-factor Authentication is ingeschakeld voor alle gebruikers zonder privileged

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 Zorg ervoor dat er geen gast gebruikers zijn

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe die zijn afgestemd op deze CIS-aanbeveling.

- Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement
- Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 Controleer of de standaard beleids instelling voor ASC ' systeem updates controleren ' niet is uitgeschakeld '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Systeem updates moeten worden geïnstalleerd op uw computers

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 Controleer of de standaard beleids instelling voor het controleren van de beveiligings lekken van het besturings systeem niet is uitgeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 Controleer of de standaard beleids instelling voor ASC ' monitor Endpoint Protection ' niet is uitgeschakeld '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Ontbrekende Endpoint Protection in Azure Security Center controleren

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 Controleer of de ASC-standaard beleids instelling schijf versleuteling controleren niet is uitgeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Schijf versleuteling moet worden toegepast op virtuele machines

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2,8 Controleer of de standaard beleids instelling voor het controleren van de Web Application firewall is uitgeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- De Nsg's-regels voor webtoepassingen op IaaS moeten een harde verbinding hebben

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 zorg ervoor dat de standaard beleids instelling voor het controleren van het beveiligings niveau van de ASC niet is uitgeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 Zorg ervoor dat ASC-standaard beleids instelling ' JIT-netwerk toegang controleren ' niet is uitgeschakeld '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Just-in-time-netwerk toegangs beheer moet worden toegepast op virtuele machines

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 Controleer of de standaard beleids instelling voor de ASC ' SQL-versleuteling controleren ' niet is uitgeschakeld '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 Controleer of ' beveiligde overdracht vereist ' is ingesteld op ingeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 Controleer of de standaard regel voor netwerk toegang voor opslag accounts is ingesteld op weigeren

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Onbeperkte netwerk toegang tot opslag accounts controleren

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 Controleer of ' controle ' is ingesteld op ' aan '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Controle moet worden ingeschakeld voor geavanceerde instellingen voor gegevens beveiliging op SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 Zorg ervoor dat ' AuditActionGroups ' in het beleid voor een SQL Server correct is ingesteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Voor de SQL-controle-instellingen moeten actie-groepen zijn geconfigureerd voor het vastleggen van kritieke activiteiten

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 Controleer of de Bewaar periode van de controle groter is dan 90 dagen

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- SQL-servers moeten worden geconfigureerd met controle dagen van meer dan 90 dagen.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 Controleer of ' geavanceerde gegevens beveiliging ' op een SQL-Server is ingesteld op on

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Geavanceerde gegevensbeveiliging moet zijn ingeschakeld op uw SQL-servers

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 zorg ervoor dat ' bedreigingen detectie typen ' is ingesteld op ' all '

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe die zijn afgestemd op deze CIS-aanbeveling.

- De geavanceerde beveiligings typen voor bedreigingen moeten worden ingesteld op ' all ' in de instellingen voor geavanceerde gegevens beveiliging van SQL Server
- De geavanceerde beveiligings typen voor bedreigingen moeten worden ingesteld op ' all ' in de instellingen voor geavanceerde gegevens beveiliging van SQL Managed instance

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 Controleer of ' waarschuwingen verzenden naar ' is ingesteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Geavanceerde instellingen voor gegevens beveiliging voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van beveiligings waarschuwingen

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 Controleer of ' e-mail service en mede beheerders ' is ingeschakeld '

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Geavanceerde instellingen voor gegevens beveiliging voor het beheerde exemplaar van SQL moeten een e-mail adres bevatten voor het ontvangen van beveiligings waarschuwingen

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 Zorg ervoor dat Azure Active Directory beheerder is geconfigureerd

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Een Azure Active Directory beheerder moet worden ingericht voor SQL-servers

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 Controleer of ' gegevens versleuteling ' is ingesteld op on ' op een SQL Database

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 Controleer of de TDE-beveiliging van SQL Server is versleuteld met BYOK (gebruik uw eigen sleutel)

Deze blauw druk wijst [Azure Policy](../../../policy/overview.md) definities toe die zijn afgestemd op deze CIS-aanbeveling.

- De TDE-beveiliging van de SQL-server moet zijn versleuteld met uw eigen sleutel
- TDE-beveiliging van SQL Managed instance moet worden versleuteld met uw eigen sleutel

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 zorgt ervoor dat logboek registratie voor Azure-sleutel kluis is ingeschakeld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Diagnostische logboeken in Key Vault moeten worden ingeschakeld

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 Zorg ervoor dat de besturingssysteem schijf is versleuteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Schijf versleuteling moet worden toegepast op virtuele machines

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 Controleer of de gegevens schijven zijn versleuteld

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Schijf versleuteling moet worden toegepast op virtuele machines

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 zorg ervoor dat de meest recente besturingssysteem patches voor alle Virtual Machines zijn toegepast

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Systeem updates moeten worden geïnstalleerd op uw computers

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 Controleer of Endpoint Protection voor alle Virtual Machines is geïnstalleerd

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Ontbrekende Endpoint Protection in Azure Security Center controleren

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 op rollen gebaseerd toegangs beheer (RBAC) in azure Kubernetes Services inschakelen

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- \[Voor\]beeld: Op rollen gebaseerde Access Control (RBAC) moet worden gebruikt voor Kubernetes-Services

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 Zorg ervoor dat web-apps alle HTTP-verkeer omleidt naar HTTPS in Azure App Service

Deze blauw druk wijst een [Azure Policy](../../../policy/overview.md) definitie toe die wordt uitgelijnd met deze CIS-aanbeveling.

- Web-App moet alleen toegankelijk zijn via HTTPS

## <a name="next-steps"></a>Volgende stappen

Nu u de toewijzing van het besturings element van CIS Microsoft Azure Stichtings-benchmarks hebt bekeken, gaat u naar het volgende artikel voor meer informatie over de blauw druk of gaat u naar Azure Policy in de Azure Portal om het initiatief toe te wijzen:

> [!div class="nextstepaction"]
> [CIS Microsoft Azure basis benchmarks van de Stichting-overzicht](./index.md)
> [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).