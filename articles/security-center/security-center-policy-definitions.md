---
title: Azure-beleids definities die in Azure Security Center worden bewaakt | Microsoft Docs
description: Azure-beleids definities die in Azure Security Center worden bewaakt.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c89cb1aa-74e8-4ed1-980a-02a7a25c1a2f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/22/2019
ms.author: v-mohabe
ms.openlocfilehash: 56e6285501529f26e3622e2f773dbd373c169c10
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990494"
---
# <a name="azure-security-policies-monitored-by-security-center"></a>Azure-beveiligings beleid bewaakt door Security Center
Dit artikel bevat een lijst met Azure Policy definities die u in Azure Security Center kunt bewaken. Zie [werken met beveiligings beleid](tutorial-security-policy.md)voor meer informatie over beveiligings beleidsregels.

## <a name="available-security-policies"></a>Beschik bare beveiligings beleidsregels

Zie de volgende tabel voor meer informatie over het ingebouwde beleid dat wordt bewaakt door Security Center:

| Beleid | Betekenis van het beleid |
| --- | --- |
|Diagnostische logboeken in Virtual Machine Scale Sets moeten worden ingeschakeld|Het is raadzaam om Logboeken in te scha kelen, zodat er na een incident of inbreuk een activiteiten spoor beschikbaar is voor onderzoek.|
|Alle autorisatie regels behalve RootManageSharedAccessKey moeten worden verwijderd uit de Event hub-naam ruimte|Azure Event Hubs-clients mogen geen toegangs beleid op naam ruimte niveau gebruiken dat toegang biedt tot alle wacht rijen en onderwerpen in een naam ruimte. Als u wilt uitlijnen met het beveiligings model met minimale bevoegdheden, maakt u toegangs beleid op het niveau van de entiteit voor wacht rijen en onderwerpen om alleen toegang te verlenen aan de specifieke entiteit.|
|Autorisatie regels voor de Event hub-entiteit moeten worden gedefinieerd|Controleer het bestaan van autorisatie regels voor Event Hubs entiteiten om toegang met minimale bevoegdheden toe te kennen.|
|Toegang tot opslag accounts met firewall-en virtuele-netwerk configuraties moet worden beperkt|Controleer onbeperkte netwerk toegang in de firewall-instellingen van uw opslag account. Configureer netwerk regels zodat alleen toepassingen van toegestane netwerken toegang hebben tot het opslag account. Als u verbindingen van specifieke internet-of lokale clients wilt toestaan, kunt u toegang verlenen aan verkeer van specifieke virtuele Azure-netwerken of op open bare IP-adresbereiken voor Internet.|
|Gebruik van aangepaste RBAC-regels controleren|Ingebouwde rollen controleren, zoals ' eigenaar, Inzender, lezer ' in plaats van aangepaste functies op basis van op rollen gebaseerde toegangs beheer (RBAC), die gevoelig zijn voor fouten. Het gebruik van aangepaste rollen wordt behandeld als een uitzonde ring en vereist strikte beoordelings-en bedreigings modellen.|
|Diagnostische logboeken in Azure Stream Analytics moeten worden ingeschakeld|Controleren of Logboeken worden ingeschakeld en bewaard gedurende een jaar. Hiermee maakt u een activiteiten spoor voor onderzoek wanneer er een beveiligings incident optreedt of uw netwerk is aangetast.|
|Beveiligde overdracht naar opslag accounts moet zijn ingeschakeld|Controleer de vereisten van beveiligde overdracht in uw opslag account. Beveiligde overdracht is een optie die ervoor zorgt dat uw opslag account alleen aanvragen van beveiligde verbindingen (HTTPS) accepteert. Het gebruik van HTTPS zorgt voor verificatie tussen de server en de service. Het beschermt ook gegevens die onderweg zijn via netwerklaag aanvallen, zoals man-in-the-Middle, inkomend, afluis teren en sessie overname.|
|Azure AD-beheerder voor SQL Server moet worden ingericht|Het inrichten van een Azure Active Directory-beheerder (Azure AD) controleren voor SQL Server om Azure AD-verificatie in te scha kelen. Azure AD-verificatie ondersteunt vereenvoudigd beheer van machtigingen en gecentraliseerd identiteits beheer van database gebruikers en andere micro soft-Services.|
|Alle autorisatie regels behalve RootManageSharedAccessKey moeten worden verwijderd uit Service Bus naam ruimte|Azure Service Bus-clients mogen geen toegangs beleid op naam ruimte niveau gebruiken dat toegang biedt tot alle wacht rijen en onderwerpen in een naam ruimte. Als u wilt uitlijnen met het beveiligings model met minimale bevoegdheden, maakt u toegangs beleid op het niveau van de entiteit voor wacht rijen en onderwerpen om alleen toegang te verlenen aan de specifieke entiteit.|
|Diagnostische logboeken in Service Bus moeten worden ingeschakeld|Controleer het inschakelen van Logboeken en bewaar deze gedurende een jaar. Hiermee maakt u een activiteiten spoor voor onderzoek wanneer er een beveiligings incident optreedt of uw netwerk is aangetast.|
|De eigenschap ClusterProtectionLevel van EncryptAndSign in Service Fabric moet worden ingesteld|Service Fabric biedt drie beveiligings niveaus voor communicatie tussen knoop punten die gebruikmaken van een primair cluster certificaat: Geen, Sign en EncryptAndSign. Stel het beveiligingsniveau om ervoor te zorgen dat alle berichten van knooppunt-naar-knooppunt zijn versleuteld en digitaal zijn ondertekend.|
|Client verificatie moet Azure Active Directory gebruiken|Controleer het gebruik van client verificatie alleen via Azure AD in Service Fabric.|
|Diagnostische logboeken in Search Services moeten worden ingeschakeld|Controleren of Logboeken worden ingeschakeld en bewaard gedurende een jaar. Hiermee maakt u een activiteiten spoor voor onderzoek wanneer er een beveiligings incident optreedt of uw netwerk is aangetast.|
|Alleen beveiligde verbindingen met uw Redis Cache moeten worden ingeschakeld|Controleer het inschakelen van alleen verbindingen via SSL naar Azure cache voor redis. Gebruik van beveiligde verbindingen zorgt voor verificatie tussen de server en de service. Het beschermt ook gegevens die onderweg zijn via netwerklaag aanvallen, zoals man-in-the-Middle, inkomend, afluis teren en sessie overname.|
|Diagnostische logboeken in Logic Apps moeten worden ingeschakeld|Controleren of Logboeken worden ingeschakeld en bewaard gedurende een jaar. Hiermee maakt u een activiteiten spoor voor onderzoek wanneer er een beveiligings incident optreedt of uw netwerk is aangetast.|
|Diagnostische logboeken in Key Vault moeten worden ingeschakeld|Controleren of Logboeken worden ingeschakeld en bewaard gedurende een jaar. Hiermee maakt u een activiteiten spoor voor onderzoek wanneer er een beveiligings incident optreedt of uw netwerk is aangetast.|
|Diagnostische logboeken in Event hub moeten worden ingeschakeld|Controleren of Logboeken worden ingeschakeld en bewaard gedurende een jaar. Hiermee maakt u een activiteiten spoor voor onderzoek wanneer er een beveiligings incident optreedt of uw netwerk is aangetast.|
|Diagnostische logboeken in Azure Data Lake Store moeten worden ingeschakeld|Controleren of Logboeken worden ingeschakeld en bewaard tot een jaar. Hiermee maakt u een activiteiten spoor voor onderzoek wanneer er een beveiligings incident optreedt of uw netwerk is aangetast.|
|Diagnostische logboeken in Data Lake Analytics moeten worden ingeschakeld|Controleren of Logboeken worden ingeschakeld en bewaard gedurende een jaar. Hiermee maakt u een activiteiten spoor voor onderzoek wanneer er een beveiligings incident optreedt of uw netwerk is aangetast.|
|Opslag accounts moeten worden gemigreerd naar nieuwe AzureRM-resources|Gebruik Azure Resource Manager voor uw opslag accounts om beveiligings uitbreidingen te bieden. Deze omvatten: <br>-Sterker toegangs beheer (RBAC)<br>-Betere controle<br>-Implementatie en governance op basis van Azure Resource Manager<br>-Toegang tot beheerde identiteiten<br>-Toegang tot Azure Key Vault voor geheimen<br>-Verificatie op basis van Azure AD<br>-Ondersteuning voor Tags en resource groepen voor eenvoudiger beveiligings beheer|
|Virtuele machines moeten worden gemigreerd naar nieuwe AzureRM-resources|Gebruik Azure Resource Manager voor uw virtuele machines om beveiligings uitbreidingen te bieden.  Deze omvatten: <br>-Sterker toegangs beheer (RBAC)<br>-Betere controle<br>-Implementatie en governance op basis van Azure Resource Manager<br>-Toegang tot beheerde identiteiten<br>-Toegang tot Azure Key Vault voor geheimen<br>-Verificatie op basis van Azure AD<br>-Ondersteuning voor Tags en resource groepen voor eenvoudiger beveiligings beheer|
|Metrische waarschuwings regels moeten worden geconfigureerd voor batch-accounts|Controleer de configuratie van metrische waarschuwings regels op Azure Batch accounts om de vereiste metriek in te scha kelen.|
|Diagnostische logboeken in batch-accounts moeten worden ingeschakeld|Controleren of Logboeken worden ingeschakeld en bewaard gedurende een jaar. Hiermee maakt u een activiteiten spoor voor onderzoek wanneer er een beveiligings incident optreedt of uw netwerk is aangetast.|
|Versleuteling moet zijn ingeschakeld voor de variabelen van het Automation-account|Het is belang rijk om versleuteling van de variabele activa van Azure Automation-account in te scha kelen wanneer u gevoelige gegevens opslaat.|
|Diagnostische logboeken in App Services moeten worden ingeschakeld|Controle op het inschakelen van Diagnostische logboeken in de app. Hiermee maakt u een activiteiten spoor voor onderzoek wanneer er een beveiligings incident optreedt of uw netwerk is aangetast.|
|Transparent Data Encryption voor SQL-data bases moet zijn ingeschakeld|Controleer de transparante status van gegevens versleuteling voor SQL-data bases.|
|SQL Server-controle moet zijn ingeschakeld|Controleer het bestaan van SQL-controle op server niveau.|
|\[Preview-versie]: Niet-versleutelde SQL database in Azure Security Center bewaken|Azure Security Center bewaakt niet-versleutelde SQL-servers of-data bases zoals aanbevolen.|
|\[Preview-versie]: Ongecontroleerde SQL database in Azure Security Center bewaken|Azure Security Center bewaakt SQL-servers en-data bases waarvoor geen SQL-controle is ingeschakeld.|
|\[Preview-versie]: Systeem updates moeten worden geïnstalleerd op uw computers|Azure Security Center bewaakt updates van het beveiligings systeem op uw servers zoals aanbevolen.|
|\[Preview-versie]: Ontbrekende BLOB-versleuteling voor opslag accounts controleren|Controleer opslag accounts die geen blob-versleuteling gebruiken. Dit geldt alleen voor micro soft Storage-Resource typen, niet voor opslag van andere providers. Azure Security Center bewaakt mogelijke netwerk-just-in-time-toegang zoals aanbevolen.|
|\[Preview-versie]: Just-in-time-netwerk toegangs beheer moet worden toegepast op virtuele machines|Azure Security Center bewaakt mogelijke netwerk-just-in-time-toegang zoals aanbevolen.|
|\[Preview-versie]: Adaptieve toepassings besturings elementen moeten worden ingeschakeld op virtuele machines|Azure Security Center beschik bare configuratie van de white list-toepassing.|
|\[Preview-versie]: Ontbrekende netwerk beveiligings groepen voor virtuele machines moeten worden geconfigureerd|Azure Security Center bewaakt netwerk beveiligings groepen die te niet-strikte regels bevatten, zoals aanbevolen.|
|\[Preview-versie]: Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld|Azure Security Center bewaakt servers die niet voldoen aan de geconfigureerde basis lijn zoals aanbevolen.| 
|\[Preview-versie]: Endpoint Protection moet worden geïnstalleerd op virtuele machines|Azure Security Center bewaakt servers waarop geen micro soft System Center Endpoint Protection-agent is geïnstalleerd zoals aanbevolen.|
|\[Preview-versie]: Schijf versleuteling moet worden toegepast op virtuele machines|Azure Security Center bewaakt virtuele machines waarvoor geen schijf versleuteling is ingeschakeld.|
|\[Preview-versie]: Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken|Bewaak beveiligings problemen die worden gedetecteerd door de oplossing voor de evaluatie van de beveiligings problemen en Vm's die geen oplossing voor de evaluatie van beveiligings problemen hebben in Azure Security Center zoals aanbevolen.|
|\[Preview-versie]: Niet-beveiligde webtoepassing in Azure Security Center bewaken|Azure Security Center bewaakt webtoepassingen die geen Web Application Firewall bescherming zoals aanbevolen zijn.|
|\[Preview-versie]: Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele machines|Azure Security Center bewaakt netwerk eindpunten die niet de volgende generatie firewall beveiliging hebben zoals aanbevolen.|
|\[Preview-versie]: Beveiligings problemen voor uw SQL-data bases moeten worden hersteld|Controle resultaten voor beoordeling van beveiligings problemen bewaken en raden u aan om database problemen op te lossen.|
|\[Preview-versie]: Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement|U wordt aangeraden Maxi maal drie abonnements eigenaren aan te wijzen om de kans op schending door een aangetast eigenaar te verminderen.|
|\[Preview-versie]: Er moet meer dan één eigenaar aan uw abonnement zijn toegewezen|We raden u aan meer dan één abonnements eigenaar aan te wijzen om de beheerders toegang te verzekeren.|
|\[Preview-versie]: MFA moet zijn ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement |Multi-factor Authentication (MFA) moet zijn ingeschakeld voor alle abonnements accounts die eigenaars machtigingen hebben om een schending van accounts of bronnen te voor komen.|
|\[Preview-versie]: MFA moet zijn ingeschakeld voor uw abonnements accounts met schrijf machtigingen|Multi-factor Authentication moet zijn ingeschakeld voor alle abonnements accounts met schrijf machtigingen om schending van accounts of bronnen te voor komen.|
|\[Preview-versie]: MFA moet zijn ingeschakeld voor uw abonnements accounts met lees machtigingen|Multi-factor Authentication moet zijn ingeschakeld voor alle abonnements accounts met lees machtigingen om schending van accounts of bronnen te voor komen.|
|\[Preview-versie]: Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement|Afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement. Bij het aanmelden is de registratie van afgeschafte accounts geblokkeerd.|
|\[Preview-versie]: Afgeschafte accounts moeten worden verwijderd uit uw abonnement|Afgeschafte accounts moeten uit uw abonnementen worden verwijderd. Bij het aanmelden is de registratie van afgeschafte accounts geblokkeerd.|
|\[Preview-versie]: Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement|Externe accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement om toegang tot machtigingen te voor komen.|
|\[Preview-versie]: Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement|Externe accounts met schrijf machtigingen moeten worden verwijderd uit uw abonnement om niet-bewaakte toegang te voor komen.|
|\[Preview-versie]: Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement|Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement om niet-bewaakte toegang te voor komen.|




## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u kunnen lezen hoe u het beveiligingsbeleid in Security Center configureert. Raadpleeg de volgende artikelen voor meer informatie over Security Center.

* [Azure Security Center plannings-en bedienings handleiding](security-center-planning-and-operations-guide.md): Meer informatie over het plannen en begrijpen van ontwerp overwegingen in Azure Security Center.
* [Beveiligings status controleren in azure Security Center](security-center-monitoring.md): Meer informatie over het controleren van de status van uw Azure-resources.
* [Beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md): Informatie over het beheren van en reageren op beveiligingswaarschuwingen.
* [Partner oplossingen bewaken met Azure Security Center](security-center-partner-solutions.md): Meer informatie over het bewaken van de status van uw partneroplossingen.
* [Azure Security Center Veelgestelde vragen](security-center-faq.md): Krijg antwoorden op veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligings blog](https://blogs.msdn.com/b/azuresecurity/): Raadpleeg de blogberichten over beveiliging en naleving in Azure.

Zie [Wat is Azure Policy?](../governance/policy/overview.md)voor meer informatie over Azure Policy.
