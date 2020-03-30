---
title: Checklist voor operationele beveiliging van Azure| Microsoft Documenten
description: In dit artikel vindt u een checklist voor de operationele beveiliging van Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: fb940857a0f88590cb9bbbf56b9e6a791299309f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980947"
---
# <a name="azure-operational-security-checklist"></a>Checklist voor operationele beveiliging van Azure
Het implementeren van een toepassing op Azure is snel, eenvoudig en kosteneffectief. Voordat u cloudtoepassingen in productie implementeert die nuttig zijn om een checklist te hebben om uw toepassing te evalueren aan de hand van een lijst met essentiële en aanbevolen operationele beveiligingsacties die u overwegen.

## <a name="introduction"></a>Inleiding

Azure biedt een reeks infrastructuurservices die u gebruiken om uw toepassingen te implementeren. Azure Operational Security verwijst naar de services, besturingselementen en functies die beschikbaar zijn voor gebruikers voor het beschermen van hun gegevens, toepassingen en andere elementen in Microsoft Azure.

-   Om het maximale voordeel uit het cloudplatform te halen, raden we u aan Azure-services te gebruiken en de checklist te volgen.
-   Organisaties die tijd en middelen investeren bij het beoordelen van de operationele paraatheid van hun toepassingen voor de lancering hebben een veel hoger percentage van tevredenheid dan degenen die dat niet doen. Bij het uitvoeren van dit werk kunnen checklists een waardevol mechanisme zijn om ervoor te zorgen dat toepassingen consistent en holistisch worden geëvalueerd.
-   Het niveau van de operationele beoordeling is afhankelijk van het cloudvolwassenheidsniveau van de organisatie en de ontwikkelingsfase, beschikbaarheidsbehoeften en vereisten voor gegevensgevoeligheid van de toepassing.

## <a name="checklist"></a>Controlelijst

Deze checklist is bedoeld om bedrijven te helpen bij het bedenken van verschillende operationele beveiligingsoverwegingen bij het implementeren van geavanceerde bedrijfstoepassingen op Azure. Het kan ook worden gebruikt om u te helpen een veilige cloudmigratie- en operatiestrategie voor uw organisatie op te bouwen.

|Checklistcategorie| Beschrijving|
| ------------ | -------- |
| [<br>Beveiligingsrollen & Toegangsbesturingselementen](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Gebruik [RBAC (Role based access control)](../../role-based-access-control/role-assignments-portal.md) om gebruikersspecifiek te maken die wordt gebruikt om machtigingen toe te wijzen aan gebruikers, groepen en toepassingen op een bepaald bereik.</li></ul> |
| [<br>Gegevensverzameling & opslag](../../storage/blobs/security-recommendations.md)|<ul><li>Gebruik Beheervliegtuigbeveiliging om uw opslagaccount te beveiligen met [behulp van RBAC (Role-Based Access Control).](../../role-based-access-control/role-assignments-portal.md)</li><li>Beveiliging van gegevensvliegtuigen voor het beveiligen van toegang tot uw gegevens met behulp van [SAS (Shared Access Signatures)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) en Stored Access Policies.</li><li>Versleuteling op transportniveau gebruiken : gebruik HTTPS en de versleuteling die wordt gebruikt door [SMB (Server message block protocols) 3.0](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) voor [Azure File Shares](../../storage/files/storage-dotnet-how-to-use-files.md).</li><li>Gebruik [versleuteling aan de clientzijde](../../storage/common/storage-client-side-encryption.md) om gegevens te beveiligen die u naar opslagaccounts verzendt wanneer u de uitsluitende controle over versleutelingssleutels nodig hebt. </li><li>Gebruik [Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md) om automatisch gegevens in Azure Storage en Azure Disk [Encryption](../azure-security-disk-encryption-overview.md) te versleutelen om schijfbestanden voor virtuele machines voor het besturingssysteem en de gegevensschijven te versleutelen.</li><li>Azure [Storage Analytics gebruiken](https://docs.microsoft.com/rest/api/storageservices/storage-analytics) om het autorisatietype te controleren; Net als bij Blob Storage u zien of gebruikers een gedeelde toegangshandtekening of de opslagaccountsleutels hebben gebruikt.</li><li>Gebruik [CorS (Cross-Origin Resource Sharing)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) om toegang te krijgen tot opslagbronnen van verschillende domeinen.</li></ul> |
|[<br>Veiligheidsbeleid & aanbevelingen](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Gebruik [Azure Security Center](../../security-center/security-center-install-endpoint-protection.md) om endpoint-oplossingen te implementeren.</li><li>Voeg een [webapplicatiefirewall (WAF)](../../application-gateway/waf-overview.md) toe om webtoepassingen te beveiligen.</li><li>   Gebruik een [firewall](../../sentinel/connect-data-sources.md) van een Microsoft-partner om uw beveiligingsbeveiligingen te verhogen. </li><li>Beveiligingscontactgegevens toepassen voor uw Azure-abonnement; dit neemt contact op met het [Microsoft Security Response Center (MSRC)](https://technet.microsoft.com/security/dn528958.aspx) als wordt ontdekt dat uw klantgegevens zijn geopend door een onwettige of onbevoegde partij.</li></ul> |
| [<br>Identiteits& Toegangsbeheer](identity-management-best-practices.md)|<ul><li>[Synchroniseer uw on-premises directory met uw cloudmap met Azure AD.](../../active-directory/hybrid/whatis-hybrid-identity.md)</li><li>Gebruik [Single Sign-On](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/) om gebruikers toegang te geven tot hun SaaS-toepassingen op basis van hun organisatie-account in Azure AD.</li><li>Gebruik het rapport [Registratieactiviteit voor het opnieuw instellen van wachtwoorden](../../active-directory/active-directory-passwords-reporting.md) om de gebruikers te controleren die zich registreren.</li><li>Schakel [multi-factor authenticatie (MFA)](../../active-directory/authentication/multi-factor-authentication.md) in voor gebruikers.</li><li>Ontwikkelaars gebruiken veilige identiteitsmogelijkheden voor apps zoals [Microsoft Security Development Lifecycle (SDL).](https://www.microsoft.com/download/details.aspx?id=12379)</li><li>Controleer actief op verdachte activiteiten met Azure AD Premium-anomalierapporten en [azure AD-identiteitsbeveiligingsmogelijkheden](../../active-directory/identity-protection/overview.md).</li></ul> |
|[<br>Continue beveiligingsbewaking](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Gebruik Malware Assessment Solution [Azure Monitor-logboeken](../../log-analytics/log-analytics-queries.md) om te rapporteren over de status van antimalwarebeveiliging in uw infrastructuur.</li><li>Gebruik [updatebeoordeling](../../automation/automation-update-management.md) om de algemene blootstelling aan potentiële beveiligingsproblemen te bepalen en of en hoe belangrijk deze updates zijn voor uw omgeving.</li><li>De [identiteit en toegang](../../security-center/security-center-monitoring.md) bieden u een overzicht van de gebruiker </li><ul><li>status van de identiteit van de gebruiker,</li><li>aantal mislukte pogingen om in te loggen,</li><li> het account van de gebruiker dat tijdens die pogingen werd gebruikt, accounts die zijn vergrendeld</li> <li>accounts met gewijzigd of opnieuw ingesteld wachtwoord </li><li>Momenteel aantal accounts die zijn aangemeld.</li></ul></ul> |
| [<br>Detectiemogelijkheden van Azure Security Center](../../security-center/security-center-alerts-overview.md#detect-threats)|<ul><li>Gebruik [detectiemogelijkheden](../../security-center/security-center-alerts-overview.md#detect-threats)om actieve bedreigingen te identificeren die zijn gericht op uw Microsoft Azure-bronnen.</li><li>Gebruik [geïntegreerde bedreigingsinformatie](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/19/get-threat-intelligence-reports-with-azure-security-center/) die op zoek is naar bekende slechte actoren door gebruik te maken van wereldwijde bedreigingsinformatie van Microsoft-producten en -services, de [Microsoft Digital Crimes Unit (DCU),](https://www.microsoft.com/trustcenter/security/cybercrime)het Microsoft Security Response Center [(MSRC)](response-center.md)en externe feeds.</li><li>Gebruik [gedragsanalyses](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/) die bekende patronen toepassen om kwaadaardig gedrag te ontdekken. </li><li>Gebruik [anomaliedetectie](https://msdn.microsoft.com/library/azure/dn913096.aspx) die statistische profilering gebruikt om een historische basislijn te maken.</li></ul> |
| [<br>Developer Operations (DevOps)](https://docs.microsoft.com/azure/architecture/checklist/dev-ops)|<ul><li>[Infrastructure as Code (IaC)](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) is een praktijk die het mogelijk maakt de automatisering en validatie van het creëren en afbreken van netwerken en virtuele machines om te helpen bij het leveren van veilige, stabiele applicatie hosting platforms.</li><li>[Continue integratie en implementatie](/visualstudio/containers/overview#continuous-delivery-and-continuous-integration-cicd) stimuleren het voortdurend samenvoegen en testen van code, wat leidt tot het vroegtijdig vinden van defecten. </li><li>[Releasebeheer](https://msdn.microsoft.com/library/vs/alm/release/overview) Beheer geautomatiseerde implementaties in elke fase van uw pijplijn.</li><li>[App Performance Monitoring](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) van lopende toepassingen, waaronder productieomgevingen voor applicatiestatus en klantgebruik, helpen organisaties een hypothese te vormen en strategieën snel te valideren of te weerleggen.</li><li>Met [behulp van Load Testing & Auto-Scale](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) kunnen we prestatieproblemen in onze app vinden om de implementatiekwaliteit te verbeteren en ervoor te zorgen dat onze app altijd up- of beschikbaar is om aan de zakelijke behoeften te voldoen.</li></ul> |


## <a name="conclusion"></a>Conclusie
Veel organisaties hebben hun cloudtoepassingen met succes geïmplementeerd en beheerd op Azure. De meegeleverde checklists benadrukken verschillende checklists die essentieel zijn en helpen u om de kans op succesvolle implementaties en frustratievrije bewerkingen te vergroten. We raden deze operationele en strategische overwegingen ten zeerste aan voor uw bestaande en nieuwe toepassingsimplementaties op Azure.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over Beveiliging:

- [Ontwerp en operationele beveiliging.](https://www.microsoft.com/trustcenter/security/designopsecurity)
- [Planning en bewerkingen van azure security center](../../security-center/security-center-planning-and-operations-guide.md).
