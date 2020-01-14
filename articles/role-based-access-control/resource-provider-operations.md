---
title: Bewerkingen voor de resource provider Azure Resource Manager | Microsoft Docs
description: Een lijst met de bewerkingen die beschikbaar zijn voor de resource providers van de Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9d4b4134fa26fd2cb904a862ac16544873bf8bcb
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934462"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Bewerkingen voor de resource provider Azure Resource Manager

Dit artikel bevat een overzicht van de bewerkingen die beschikbaar zijn voor elke Azure Resource Manager resource provider. Deze bewerkingen kunnen worden gebruikt in [aangepaste rollen](custom-roles.md) om nauw keurig op [rollen gebaseerd toegangs beheer (RBAC)](overview.md) te bieden voor bronnen in Azure. Bewerkings reeksen hebben de volgende indeling: `{Company}.{ProviderName}/{resourceType}/{action}`. Zie [overeenkomen met de resource provider voor service](../azure-resource-manager/azure-services-resource-providers.md)voor een lijst met de manier waarop naam ruimten van de resource provider worden toegewezen aan Azure-Services.

De bewerkingen van de resource provider zijn altijd in ontwikkeling. Als u de meest recente bewerkingen wilt downloaden, gebruikt u de [bewerkings lijst](/cli/azure/provider/operation#az-provider-operation-list)van de [AzProviderOperation-](/powershell/module/az.resources/get-azprovideroperation) of AZ-provider.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.AAD/domainServices/delete | Domein service verwijderen |
> | Actie | Microsoft.AAD/domainServices/oucontainer/delete | OE-container verwijderen |
> | Actie | Microsoft.AAD/domainServices/oucontainer/read | OE-containers lezen |
> | Actie | Microsoft.AAD/domainServices/oucontainer/write | OE-container schrijven |
> | Actie | Microsoft.AAD/domainServices/read | Domein services lezen |
> | Actie | Microsoft.AAD/domainServices/write | Domein service schrijven |
> | Actie | Microsoft.AAD/locations/operationresults/read |  |
> | Actie | Microsoft.AAD/Operations/read |  |
> | Actie | Microsoft.AAD/register/action | Domein service registreren |
> | Actie | Microsoft.AAD/unregister/action | Registratie van domein service ongedaan maken |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | microsoft.aadiam/diagnosticsettings/delete | Een diagnostische instelling verwijderen |
> | Actie | microsoft.aadiam/diagnosticsettings/read | Een diagnostische instelling lezen |
> | Actie | microsoft.aadiam/diagnosticsettings/write | Een diagnostische instelling schrijven |
> | Actie | microsoft.aadiam/diagnosticsettingscategories/read | Categorieën voor Diagnostische instellingen lezen |
> | Actie | micro soft. aadiam/metricDefinitions/lezen | Metrische definities voor Tenant niveau lezen |
> | Actie | micro soft. aadiam/metrieken/lezen | Metrische gegevens van Tenant niveau lezen |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Addons/operations/read | Hiermee worden ondersteunde RP-bewerkingen opgehaald. |
> | Actie | Micro soft. Addons/REGI ster/actie | Het opgegeven abonnement registreren bij micro soft. Addons |
> | Actie | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Een lijst met actuele informatie over het ondersteunings plan voor het opgegeven abonnement. |
> | Actie | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Hiermee verwijdert u het opgegeven canonieke ondersteunings plan |
> | Actie | Microsoft.Addons/supportProviders/supportPlanTypes/read | De opgegeven canonieke ondersteunings plan status ophalen. |
> | Actie | Microsoft.Addons/supportProviders/supportPlanTypes/write | Hiermee wordt het type canonieke ondersteunings plan toegevoegd dat is opgegeven. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.ADHybridHealthService/addsservices/action | Maak een nieuw forest voor de Tenant. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Hiermee worden alle servers opgehaald voor de opgegeven service naam. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/alerts/read | Hiermee worden waarschuwings gegevens opgehaald voor het forest zoals alertid, datum van waarschuwing, laatste gedetecteerde waarschuwing, waarschuwing beschrijving, laatst bijgewerkt, waarschuwings niveau, waarschuwings status, koppelingen voor probleem oplossing, enzovoort. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/configuration/read | Hiermee wordt de service configuratie voor het forest opgehaald. Voor beeld: Forestnaam, functionaliteits niveau, domein naamgevings Master FSMO Role, FSMO-functie van schema master, enzovoort. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/delete | Hiermee verwijdert u een service en de bijbehorende servers samen met status gegevens. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Hiermee worden de domeinen en site gegevens voor het forest opgehaald. Voor beeld: status, actieve waarschuwingen, omgezette waarschuwingen, eigenschappen zoals het functionele niveau van het domein, forest, infrastructuur master, PDC, RID-master, enzovoort.  |
> | Actie | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Hiermee wordt de voorkeurs instelling van de gebruiker voor het forest opgehaald.<br>Voor beeld: MetricCounterName zoals ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Instellingen voor de gebruikers interface grafieken, enzovoort. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Hiermee wordt een forest-samen vatting opgehaald voor het opgegeven forest, zoals de Forestnaam, het aantal domeinen onder dit forest, het aantal site-en site gegevens, enzovoort. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Hiermee wordt de lijst met ondersteunde metrische gegevens voor een bepaalde service opgehaald.<br>Bijvoorbeeld extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen/sec., enzovoort voor de ADFS-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor ADDomainService.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor ADSync-service. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Op basis van een service krijgt deze API de metrische gegevens.<br>Deze API kan bijvoorbeeld worden gebruikt voor het ophalen van informatie met betrekking tot: extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen per seconde, enzovoort voor de ADFederation-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor de ADDomain-service.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor synchronisatie service. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Met deze API wordt de lijst met alle onboarded ADDomainServices voor een Premium-Tenant opgehaald. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/read | Hiermee worden service Details opgehaald voor de opgegeven service naam. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Hiermee worden de replicatie Details opgehaald voor alle servers voor de opgegeven service naam. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Hiermee wordt het aantal domein controllers en de bijbehorende replicatie fouten opgehaald, indien aanwezig. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Hiermee wordt de volledige lijst met domein controllers en de replicatie Details voor het opgegeven forest opgehaald. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Voeg een Server exemplaar toe aan de service. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Tijdens de registratie van de server van ADDomainService wordt deze API aangeroepen om de referenties op te halen voor de onboarding van nieuwe servers. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Hiermee verwijdert u een server voor een bepaalde service en Tenant. |
> | Actie | Microsoft.ADHybridHealthService/addsservices/write | Hiermee wordt het ADDomainService-exemplaar voor de Tenant gemaakt of bijgewerkt. |
> | Actie | Microsoft.ADHybridHealthService/configuration/action | Hiermee wordt de Tenant configuratie bijgewerkt. |
> | Actie | Microsoft.ADHybridHealthService/configuration/read | Hiermee wordt de Tenant configuratie gelezen. |
> | Actie | Microsoft.ADHybridHealthService/configuration/write | Hiermee maakt u een Tenant configuratie. |
> | Actie | Microsoft.ADHybridHealthService/logs/contents/read | Hiermee wordt de inhoud opgehaald van agent installatie-en registratie logboeken die zijn opgeslagen in BLOB. |
> | Actie | Microsoft.ADHybridHealthService/logs/read | Hiermee worden de agent installatie-en registratie logboeken voor de Tenant opgehaald. |
> | Actie | Microsoft.ADHybridHealthService/operations/read | Hiermee wordt een lijst opgehaald met bewerkingen die worden ondersteund door het systeem. |
> | Actie | Microsoft.ADHybridHealthService/register/action | Hiermee wordt de ADHybrid Health Service Resource provider geregistreerd en wordt het maken van ADHybrid Health Service Resource ingeschakeld. |
> | Actie | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Hiermee wordt een lijst met beschik bare regio's opgehaald die door DevOps worden gebruikt om klant incidenten te ondersteunen. |
> | Actie | Microsoft.ADHybridHealthService/reports/badpassword/read | Hiermee haalt u de lijst met ongeldige wachtwoord pogingen op voor alle gebruikers in Active Directory Federation Service. |
> | Actie | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Hiermee wordt de BLOB SAS-URI met de status en het uiteindelijke resultaat van een nieuwe in wachtrij geplaatste rapport taak opgehaald voor frequentie van beschadigde gebruikers namen en wacht woorden per IP-adres per dag voor een bepaalde Tenant. |
> | Actie | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Hiermee wordt de lijst met DevOps-toestemmings tenants opgehaald. Normaal gesp roken gebruikt voor klant ondersteuning. |
> | Actie | Microsoft.ADHybridHealthService/reports/isdevops/read | Haalt een waarde op die aangeeft of de Tenant is DevOps of niet. |
> | Actie | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Update GebruikersID (objectid) voor de geselecteerde dev OPS-Tenant. |
> | Actie | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Hiermee wordt de geselecteerde implementatie voor de opgegeven Tenant opgehaald. |
> | Actie | Micro soft. ADHybridHealthService/Reports/tenantassigneddeployment/lezen | Als er een Tenant-id wordt opgegeven, wordt de opslag locatie van de Tenant opgehaald. |
> | Actie | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Hiermee haalt u de geografische locatie op waarvan de gegevens worden geopend. |
> | Actie | Microsoft.ADHybridHealthService/services/action | Hiermee wordt een service-exemplaar in de Tenant bijgewerkt. |
> | Actie | Microsoft.ADHybridHealthService/services/alerts/read | Hiermee worden de waarschuwingen voor een service gelezen. |
> | Actie | Microsoft.ADHybridHealthService/services/alerts/read | Hiermee worden de waarschuwingen voor een service gelezen. |
> | Actie | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Als er een functie naam wordt gebruikt, wordt gecontroleerd of een service alles heeft wat u nodig hebt om deze functie te gebruiken. |
> | Actie | Microsoft.ADHybridHealthService/services/delete | Hiermee verwijdert u een service-exemplaar in de Tenant. |
> | Actie | Microsoft.ADHybridHealthService/services/exporterrors/read | Hiermee worden de export fouten voor een bepaalde synchronisatie service opgehaald. |
> | Actie | Microsoft.ADHybridHealthService/services/exportstatus/read | Hiermee wordt de export status voor een bepaalde service opgehaald. |
> | Actie | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Hiermee ontvangt u feedback over waarschuwingen voor een bepaalde service en server. |
> | Actie | Microsoft.ADHybridHealthService/services/metricmetadata/read | Hiermee wordt de lijst met ondersteunde metrische gegevens voor een bepaalde service opgehaald.<br>Bijvoorbeeld extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen/sec., enzovoort voor de ADFS-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor ADDomainService.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor ADSync-service. |
> | Actie | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Op basis van een service krijgt deze API het gemiddelde voor metrische gegevens voor een bepaalde service.<br>Deze API kan bijvoorbeeld worden gebruikt voor het ophalen van informatie met betrekking tot: extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen per seconde, enzovoort voor de ADFederation-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor de ADDomain-service.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor synchronisatie service. |
> | Actie | Microsoft.ADHybridHealthService/services/metrics/groups/read | Op basis van een service krijgt deze API de metrische gegevens.<br>Deze API kan bijvoorbeeld worden gebruikt voor het ophalen van informatie met betrekking tot: extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen per seconde, enzovoort voor de ADFederation-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor de ADDomain-service.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor synchronisatie service. |
> | Actie | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Op basis van een service krijgt deze API de geaggregeerde weer gave voor metrische gegevens voor een bepaalde service.<br>Deze API kan bijvoorbeeld worden gebruikt voor het ophalen van informatie met betrekking tot: extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen per seconde, enzovoort voor de ADFederation-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor de ADDomain-service.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor synchronisatie service. |
> | Actie | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Hiermee wordt de bewakings configuratie voor een service toegevoegd of bijgewerkt. |
> | Actie | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Hiermee worden de bewakings configuraties voor een bepaalde service opgehaald. |
> | Actie | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Het toevoegen of bijwerken van bewakings configuraties voor een service. |
> | Actie | Microsoft.ADHybridHealthService/services/premiumcheck/read | Met deze API wordt de lijst met alle onboarded Services voor een Premium-Tenant opgehaald. |
> | Actie | Microsoft.ADHybridHealthService/services/read | Hiermee worden de service-exemplaren in de Tenant gelezen. |
> | Actie | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Hiermee worden alle Uri's voor Risk ante IP-rapporten opgehaald voor de afgelopen zeven dagen. |
> | Actie | Microsoft.ADHybridHealthService/services/reports/details/read | Hiermee wordt een rapport van de meeste 50 gebruikers met een onjuist wacht woord van de afgelopen 7 dagen opgehaald |
> | Actie | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Genereert een Risk ante IP-rapport en retourneert een URI die ernaar verwijst. |
> | Actie | Microsoft.ADHybridHealthService/services/servicemembers/action | Hiermee maakt u een Server exemplaar in de service. |
> | Actie | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Hiermee worden de waarschuwingen voor een server gelezen. |
> | Actie | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Tijdens de registratie van de server wordt deze API aangeroepen om de referenties op te halen voor de onboarding van nieuwe servers. |
> | Actie | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | Voor een bepaalde server krijgt deze API een lijst met gegevens typen die worden geüpload door de servers en de meest recente tijd voor elke upload. |
> | Actie | Microsoft.ADHybridHealthService/services/servicemembers/delete | Hiermee verwijdert u een Server exemplaar in de service. |
> | Actie | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Hiermee worden de fout gegevens voor het exporteren van de synchronisatie voor een bepaalde synchronisatie service opgehaald. |
> | Actie | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Op basis van een service krijgt deze API de metrische gegevens.<br>Deze API kan bijvoorbeeld worden gebruikt voor het ophalen van informatie met betrekking tot: extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen per seconde, enzovoort voor de ADFederation-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor de ADDomain-service.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor synchronisatie service. |
> | Actie | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Hiermee wordt de lijst met connectors opgehaald en worden profiel namen uitgevoerd voor de opgegeven service en het betreffende service-lid. |
> | Actie | Microsoft.ADHybridHealthService/services/servicemembers/read | Hiermee leest u het Server exemplaar in de service. |
> | Actie | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Hiermee wordt de service configuratie voor een bepaalde Tenant opgehaald. |
> | Actie | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Hiermee wordt de status van de functie white list voor een bepaalde Tenant opgehaald. |
> | Actie | Microsoft.ADHybridHealthService/services/write | Hiermee maakt u een service-exemplaar in de Tenant. |
> | Actie | Microsoft.ADHybridHealthService/unregister/action | Hiermee wordt de registratie van het abonnement voor ADHybrid Health Service Resource provider ongedaan gemaakt. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. Advisor/configuraties/lezen | Configuraties ophalen |
> | Actie | Microsoft.Advisor/configurations/write | Hiermee wordt een configuratie gemaakt/bijgewerkt |
> | Actie | Microsoft.Advisor/generateRecommendations/action | Hiermee wordt de status van de aanbevelingen genereren opgehaald |
> | Actie | Micro soft. Advisor/generateRecommendations/lezen | Hiermee wordt de status van de aanbevelingen genereren opgehaald |
> | Actie | Microsoft.Advisor/metadata/read | Meta gegevens ophalen |
> | Actie | Micro soft. Advisor/bewerkingen/lezen | Hiermee worden de bewerkingen voor de micro soft Advisor opgehaald |
> | Actie | Micro soft. Advisor/aanbevelingen/beschikbaar/actie | Er is nieuwe aanbeveling beschikbaar in micro soft Advisor |
> | Actie | Micro soft. Advisor/aanbevelingen/lezen | Aanbevelingen voor lezen |
> | Actie | Micro soft. Advisor/aanbevelingen/onderdrukkingen/verwijderen | Onderdrukkingen verwijderen |
> | Actie | Micro soft. Advisor/aanbevelingen/onderdrukkingen/lezen | Haalt onderdrukkingen op |
> | Actie | Micro soft. Advisor/aanbevelingen/onderdrukkingen/schrijven | Hiermee worden onderdrukkingen gemaakt/bijgewerkt |
> | Actie | Micro soft. Advisor/registreren/actie | Hiermee wordt het abonnement voor de micro soft Advisor geregistreerd |
> | Actie | Micro soft. Advisor/onderdrukkingen/verwijderen | Onderdrukkingen verwijderen |
> | Actie | Micro soft. Advisor/onderdrukkingen/lezen | Haalt onderdrukkingen op |
> | Actie | Micro soft. Advisor/onderdrukkingen/schrijven | Hiermee worden onderdrukkingen gemaakt/bijgewerkt |
> | Actie | Micro soft. Advisor/registratie/actie | Hiermee wordt de registratie van het abonnement voor micro soft Advisor ongedaan gemaakt |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.AlertsManagement/actionRules/delete | De actie regel verwijderen in een bepaald abonnement. |
> | Actie | Microsoft.AlertsManagement/actionRules/read | Alle actie regels voor de invoer filters ophalen. |
> | Actie | Micro soft. AlertsManagement/actionRules/schrijven | Een actie regel in een bepaald abonnement maken of bijwerken |
> | Actie | Micro soft. AlertsManagement/Alerts/changestate/Action | Wijzig de status van de waarschuwing. |
> | Actie | Microsoft.AlertsManagement/alerts/diagnostics/read | Alle diagnostische gegevens voor de waarschuwing ophalen |
> | Actie | Microsoft.AlertsManagement/alerts/history/read | Geschiedenis van de waarschuwing ophalen |
> | Actie | Microsoft.AlertsManagement/alerts/read | Alle waarschuwingen voor de invoer filters ophalen. |
> | Actie | Microsoft.AlertsManagement/alertsList/read | Alle waarschuwingen voor de invoer filters voor alle abonnementen ophalen |
> | Actie | Micro soft. AlertsManagement/alertsMetaData/lezen | Meta gegevens van waarschuwingen ophalen voor de invoer parameter. |
> | Actie | Microsoft.AlertsManagement/alertsSummary/read | De samen vatting van waarschuwingen ophalen |
> | Actie | Microsoft.AlertsManagement/alertsSummaryList/read | De samen vatting van waarschuwingen in abonnementen ophalen |
> | Actie | Micro soft. AlertsManagement/Operations/lezen | Hiermee worden de beschik bare bewerkingen gelezen |
> | Actie | Microsoft.AlertsManagement/register/action | Hiermee wordt het abonnement voor het micro soft-waarschuwings beheer geregistreerd |
> | Actie | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Smart detector-waarschuwings regel in een bepaald abonnement verwijderen |
> | Actie | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Alle Smart detector-waarschuwings regels voor de invoer filters ophalen |
> | Actie | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Smart detector-waarschuwings regel in een bepaald abonnement maken of bijwerken |
> | Actie | Micro soft. AlertsManagement/smartGroups/changestate/Action | Wijzig de status van de Smart Group |
> | Actie | Microsoft.AlertsManagement/smartGroups/history/read | Geschiedenis van de slimme groep ophalen |
> | Actie | Micro soft. AlertsManagement/smartGroups/lezen | Alle slimme groepen voor de invoer filters ophalen |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Hiermee wordt gecontroleerd of de opgegeven Analyseserver naam geldig is en niet wordt gebruikt. |
> | Actie | Microsoft.AnalysisServices/locations/operationresults/read | Hiermee wordt de informatie opgehaald van het opgegeven bewerkings resultaat. |
> | Actie | Microsoft.AnalysisServices/locations/operationstatuses/read | Hiermee wordt de informatie opgehaald van de opgegeven bewerkings status. |
> | Actie | Microsoft.AnalysisServices/operations/read | Hiermee wordt de informatie over bewerkingen opgehaald |
> | Actie | Microsoft.AnalysisServices/register/action | Registreert Analysis Services resource provider. |
> | Actie | Microsoft.AnalysisServices/servers/delete | Hiermee verwijdert u de Analyseserver. |
> | Actie | Microsoft.AnalysisServices/servers/listGatewayStatus/action | De status van de gateway die is gekoppeld met de server weer geven. |
> | Actie | Microsoft.AnalysisServices/servers/read | Hiermee wordt de informatie opgehaald van de opgegeven Analyseserver. |
> | Actie | Microsoft.AnalysisServices/servers/resume/action | Hiermee wordt de Analyseserver hervat. |
> | Actie | Microsoft.AnalysisServices/servers/skus/read | Beschik bare SKU-gegevens voor de server ophalen |
> | Actie | Microsoft.AnalysisServices/servers/suspend/action | Hiermee wordt de Analyseserver onderbroken. |
> | Actie | Microsoft.AnalysisServices/servers/write | Hiermee wordt het opgegeven Analyseserver gemaakt of bijgewerkt. |
> | Actie | Microsoft.AnalysisServices/skus/read | Hiermee wordt de informatie van Sku's opgehaald |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.ApiManagement/checkNameAvailability/read | Controleert of de gegeven service naam beschikbaar is |
> | Actie | Microsoft.ApiManagement/operations/read | Alle API-bewerkingen lezen die beschikbaar zijn voor micro soft. ApiManagement-resource |
> | Actie | Microsoft.ApiManagement/register/action | Het abonnement voor de resource provider micro soft. ApiManagement registreren |
> | Actie | Micro soft. ApiManagement/rapporten/lezen | Haal rapporten op die zijn geaggregeerd met tijds perioden, een geografische regio, ontwikkel aars, producten, Api's, bewerkingen, abonnementen en byRequest. |
> | Actie | Microsoft.ApiManagement/service/apis/delete | Hiermee verwijdert u de opgegeven API van het API Management service-exemplaar. |
> | Actie | Microsoft.ApiManagement/service/apis/diagnostics/delete | Hiermee verwijdert u de opgegeven diagnose vanuit een API. |
> | Actie | Microsoft.ApiManagement/service/apis/diagnostics/read | Een lijst met alle diagnostische gegevens van een API. of worden de details van de diagnose opgehaald voor een API die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/apis/diagnostics/write | Hiermee maakt u een nieuwe diagnose voor een API of wordt een bestaande versie bijgewerkt. of werkt de details bij van de diagnose voor een API die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Hiermee verwijdert u de opgegeven opmerking van een probleem. |
> | Actie | Microsoft.ApiManagement/service/apis/issues/attachments/read | Een lijst met alle bijlagen voor het probleem dat is gekoppeld aan de opgegeven API. of worden de details van de kwestie bijlage opgehaald voor een API die is opgegeven met de id. |
> | Actie | Micro soft. ApiManagement/service/api's/problemen/bijlagen/schrijven | Hiermee wordt een nieuwe bijlage gemaakt voor het probleem in een API of wordt een bestaand item bijgewerkt. |
> | Actie | Microsoft.ApiManagement/service/apis/issues/comments/delete | Hiermee verwijdert u de opgegeven opmerking van een probleem. |
> | Actie | Microsoft.ApiManagement/service/apis/issues/comments/read | Een lijst met alle opmerkingen voor het probleem dat is gekoppeld aan de opgegeven API. of worden de details van de probleem Opmerking opgehaald voor een API die is opgegeven met de id. |
> | Actie | Microsoft.ApiManagement/service/apis/issues/comments/write | Hiermee maakt u een nieuwe opmerking voor het probleem in een API of wordt een bestaand item bijgewerkt. |
> | Actie | Microsoft.ApiManagement/service/apis/issues/delete | Hiermee verwijdert u het opgegeven probleem uit een API. |
> | Actie | Microsoft.ApiManagement/service/apis/issues/read | Een lijst met alle problemen die zijn gekoppeld aan de opgegeven API. of worden de details opgehaald van het probleem voor een API die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/apis/issues/write | Hiermee maakt u een nieuw probleem voor een API of wordt een bestaande versie bijgewerkt. of werkt een bestaand probleem bij voor een API. |
> | Actie | Microsoft.ApiManagement/service/apis/operations/delete | Hiermee verwijdert u de opgegeven bewerking in de API. |
> | Actie | Microsoft.ApiManagement/service/apis/operations/policies/delete | Hiermee verwijdert u de beleids configuratie bij de API-bewerking. |
> | Actie | Microsoft.ApiManagement/service/apis/operations/policies/read | De lijst met beleids configuratie op het API-bewerkings niveau ophalen. of u kunt de beleids configuratie ophalen op het niveau van de API-bewerking. |
> | Actie | Microsoft.ApiManagement/service/apis/operations/policies/write | Hiermee wordt een beleids configuratie voor het API-bewerkings niveau gemaakt of bijgewerkt. |
> | Actie | Microsoft.ApiManagement/service/apis/operations/policy/delete | De beleids configuratie verwijderen op het niveau van de bewerking |
> | Actie | Microsoft.ApiManagement/service/apis/operations/policy/read | De beleids configuratie ophalen op het niveau van de bewerking |
> | Actie | Microsoft.ApiManagement/service/apis/operations/policy/write | Beleids configuratie maken op bewerkings niveau |
> | Actie | Microsoft.ApiManagement/service/apis/operations/read | Hiermee wordt een verzameling van de bewerkingen voor de opgegeven API weer gegeven. of worden de details opgehaald van de API-bewerking die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/apis/operations/tags/delete | Ontkoppel de code van de bewerking. |
> | Actie | Microsoft.ApiManagement/service/apis/operations/tags/read | Een lijst met alle labels die aan de bewerking zijn gekoppeld. of Get-label dat is gekoppeld aan de bewerking. |
> | Actie | Micro soft. ApiManagement/service/api's/Operations/Tags/schrijven | Wijs een tag toe aan de bewerking. |
> | Actie | Microsoft.ApiManagement/service/apis/operations/write | Hiermee maakt u een nieuwe bewerking in de API of werkt u een bestaande. of werkt de details van de bewerking bij in de API die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/apis/operationsByTags/read | Hiermee wordt een verzameling bewerkingen weer gegeven die zijn gekoppeld aan Tags. |
> | Actie | Microsoft.ApiManagement/service/apis/policies/delete | Hiermee verwijdert u de beleids configuratie op de API. |
> | Actie | Microsoft.ApiManagement/service/apis/policies/read | De beleids configuratie ophalen op API-niveau. of de beleids configuratie op het API-niveau ophalen. |
> | Actie | Micro soft. ApiManagement/service/api's/beleid/schrijven | Hiermee wordt een beleids configuratie voor de API gemaakt of bijgewerkt. |
> | Actie | Microsoft.ApiManagement/service/apis/policy/delete | De beleids configuratie op API-niveau verwijderen |
> | Actie | Microsoft.ApiManagement/service/apis/policy/read | De beleids configuratie ophalen op API-niveau |
> | Actie | Micro soft. ApiManagement/service/api's/beleid/schrijven | Beleids configuratie maken op API-niveau |
> | Actie | Microsoft.ApiManagement/service/apis/products/read | Een lijst met alle producten waarvan de API deel uitmaakt. |
> | Actie | Microsoft.ApiManagement/service/apis/read | Een lijst met alle Api's van de API Management service-instantie. of worden de details opgehaald van de API die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/apis/releases/delete | Hiermee verwijdert u alle versies van de API of verwijdert u de opgegeven release in de API. |
> | Actie | Microsoft.ApiManagement/service/apis/releases/read | Een lijst met alle releases van een API.<br>Er wordt een API-versie gemaakt wanneer u een API-revisie actueel maakt.<br>Releases worden ook gebruikt om terug te draaien naar eerdere revisies.<br>De resultaten worden gewisseld en kunnen worden beperkt door de $top en de $skip-para meters.<br>of retourneert de details van een API-release. |
> | Actie | Micro soft. ApiManagement/service/api's/releases/schrijven | Hiermee maakt u een nieuwe release voor de API. of werkt de details bij van de release van de API die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/apis/revisions/delete | Hiermee verwijdert u alle revisies van een API |
> | Actie | Microsoft.ApiManagement/service/apis/revisions/read | Een lijst met alle revisies van een API. |
> | Actie | Microsoft.ApiManagement/service/apis/schemas/delete | Hiermee verwijdert u de schema configuratie bij de API. |
> | Actie | Microsoft.ApiManagement/service/apis/schemas/read | De schema configuratie ophalen op API-niveau. u kunt ook de schema configuratie ophalen op het API-niveau. |
> | Actie | Micro soft. ApiManagement/service/api's/schema's/schrijven | Hiermee wordt een schema configuratie voor de API gemaakt of bijgewerkt. |
> | Actie | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Verwijder de beschrijving van de tag voor de API. |
> | Actie | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Een lijst met alle beschrijvingen van tags binnen het bereik van API. Model dat vergelijkbaar is met Swagger-tagDescription is gedefinieerd op API-niveau maar tag kan worden toegewezen aan de bewerkingen of een Get-label beschrijving in het bereik van API |
> | Actie | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Beschrijving van een tag maken/bijwerken binnen het bereik van de API. |
> | Actie | Microsoft.ApiManagement/service/apis/tags/delete | Ontkoppel de tag uit de API. |
> | Actie | Microsoft.ApiManagement/service/apis/tags/read | Een lijst met alle labels die zijn gekoppeld aan de API. of Get-label dat is gekoppeld aan de API. |
> | Actie | Micro soft. ApiManagement/service/api's/Tags/schrijven | Wijs tag toe aan de API. |
> | Actie | Microsoft.ApiManagement/service/apis/write | Hiermee maakt u een nieuwe of bijgewerkte bestaande API van het API Management service-exemplaar. of werkt de opgegeven API van het API Management service-exemplaar bij. |
> | Actie | Microsoft.ApiManagement/service/apisByTags/read | Hiermee wordt een verzameling api's weer gegeven die zijn gekoppeld aan Tags. |
> | Actie | Microsoft.ApiManagement/service/apiVersionSets/delete | Hiermee verwijdert u een specifieke API-versieset. |
> | Actie | Microsoft.ApiManagement/service/apiVersionSets/read | Geeft een verzameling van API-versie sets in het opgegeven service-exemplaar. of worden de details opgehaald van de API-versieset die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Lijst met versie-entiteiten ophalen |
> | Actie | Microsoft.ApiManagement/service/apiVersionSets/write | Hiermee wordt een API-versieset gemaakt of bijgewerkt. of werkt de details bij van de API-versieset die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Hiermee worden de micro soft. ApiManagement-resources die worden uitgevoerd in Virtual Network bijgewerkt om bijgewerkte netwerk instellingen te kiezen. |
> | Actie | Microsoft.ApiManagement/service/authorizationServers/delete | Hiermee verwijdert u een specifiek exemplaar van de autorisatie server. |
> | Actie | Micro soft. ApiManagement/service/authorizationServers/listSecrets/actie | Hiermee worden geheimen voor de autorisatie server opgehaald. |
> | Actie | Microsoft.ApiManagement/service/authorizationServers/read | Hiermee wordt een verzameling autorisatie servers weer gegeven die zijn gedefinieerd binnen een service-exemplaar. of worden de details van de autorisatie server zonder geheimen opgehaald. |
> | Actie | Microsoft.ApiManagement/service/authorizationServers/write | Hiermee maakt u een nieuwe autorisatie server of werkt u een bestaande autorisatie server bij. of worden de details van de autorisatie server die is opgegeven door de id, bijgewerkt. |
> | Actie | Microsoft.ApiManagement/service/backends/delete | Hiermee verwijdert u de opgegeven back-end. |
> | Actie | Microsoft.ApiManagement/service/backends/read | Hiermee wordt een verzameling back-endservers in het opgegeven service-exemplaar weer gegeven. of worden de details opgehaald van de back-end die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/backends/reconnect/action | Hiermee wordt de APIM-proxy op de hoogte gebracht van een nieuwe verbinding met de back-end na de opgegeven time-out. Als er geen time-out is opgegeven, wordt er een time-out van 2 minuten gebruikt. |
> | Actie | Microsoft.ApiManagement/service/backends/write | Hiermee wordt een back-end gemaakt of bijgewerkt. of een bestaande back-end bijwerken. |
> | Actie | Microsoft.ApiManagement/service/backup/action | Backup API Management-service naar de opgegeven container in een door de gebruiker opgegeven opslag account |
> | Actie | Microsoft.ApiManagement/service/caches/delete | Hiermee verwijdert u een specifieke cache. |
> | Actie | Microsoft.ApiManagement/service/caches/read | Hiermee wordt een verzameling van alle externe caches in het opgegeven service-exemplaar weer gegeven. of worden de details opgehaald van de cache die is opgegeven met de id. |
> | Actie | Microsoft.ApiManagement/service/caches/write | Hiermee wordt een externe cache gemaakt of bijgewerkt die moet worden gebruikt in het API Management-exemplaar. of werkt de details bij van de cache die is opgegeven met de id. |
> | Actie | Microsoft.ApiManagement/service/certificates/delete | Hiermee verwijdert u een specifiek certificaat. |
> | Actie | Microsoft.ApiManagement/service/certificates/read | Hiermee wordt een verzameling van alle certificaten in het opgegeven service-exemplaar weer gegeven. of worden de details opgehaald van het certificaat dat is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/certificates/write | Hiermee wordt het certificaat dat wordt gebruikt voor verificatie met de back-end, gemaakt of bijgewerkt. |
> | Actie | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Hiermee verwijdert u het opgegeven inhouds item. |
> | Actie | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Hiermee wordt een lijst met inhouds items geretourneerd of Details van het inhouds item geretourneerd |
> | Actie | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Hiermee wordt een nieuw inhouds item gemaakt of een opgegeven inhouds item bijgewerkt |
> | Actie | Microsoft.ApiManagement/service/contentTypes/read | Hiermee wordt een lijst met inhouds typen geretourneerd |
> | Actie | Microsoft.ApiManagement/service/delete | API Management service-exemplaar verwijderen |
> | Actie | Microsoft.ApiManagement/service/diagnostics/delete | Hiermee verwijdert u de opgegeven diagnose. |
> | Actie | Microsoft.ApiManagement/service/diagnostics/read | Een lijst met alle diagnostische gegevens van de API Management service-instantie. of worden de details opgehaald van de diagnose die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/diagnostics/write | Hiermee wordt een nieuwe diagnose gemaakt of een bestaande bijgewerkt. of de details van de diagnostische gegevens die door de id zijn opgegeven, worden bijgewerkt. |
> | Actie | Micro soft. ApiManagement/service/gateways/actie | Hiermee wordt de gateway configuratie opgehaald. of werkt de heartbeat van de gateway bij. |
> | Actie | Micro soft. ApiManagement/service/gateways/api's/verwijderen | Hiermee verwijdert u de opgegeven API van de opgegeven gateway. |
> | Actie | Micro soft. ApiManagement/service/gateways/api's/lezen | Hiermee wordt een verzameling weer gegeven van de Api's die zijn gekoppeld aan een gateway. |
> | Actie | Micro soft. ApiManagement/service/gateways/api's/schrijven | Hiermee voegt u een API toe aan de opgegeven gateway. |
> | Actie | Micro soft. ApiManagement/service/gateways/verwijderen | Hiermee verwijdert u de specifieke gateway. |
> | Actie | Micro soft. ApiManagement/service/gateways/hostnameConfigurations/lezen | Hiermee wordt de verzameling hostname-configuraties voor de opgegeven gateway weer gegeven. |
> | Actie | Micro soft. ApiManagement/service/gateways/sleutels/actie | Hiermee worden gateway sleutels opgehaald. |
> | Actie | Micro soft. ApiManagement/service/gateways/lezen | Een lijst met gateways die zijn geregistreerd bij service-exemplaar. of worden de details opgehaald van de gateway die is opgegeven door de id. |
> | Actie | Micro soft. ApiManagement/service/gateways/regeneratePrimaryKey/actie | Hiermee genereert u de primaire-gateway sleutel invalidationg alle tokens die ermee zijn gemaakt. |
> | Actie | Micro soft. ApiManagement/service/gateways/regenerateSecondaryKey/actie | Hiermee genereert u de secundaire gateway sleutel invalidationg alle tokens die ermee zijn gemaakt. |
> | Actie | Micro soft. ApiManagement/service/gateways/token/actie | Hiermee wordt het token voor gedeelde toegangs autorisatie opgehaald voor de gateway. |
> | Actie | Micro soft. ApiManagement/service/gateways/schrijven | Hiermee wordt een gateway gemaakt of bijgewerkt die moet worden gebruikt in het API Management-exemplaar. of werkt de details bij van de gateway die is opgegeven met de id. |
> | Actie | Microsoft.ApiManagement/service/getssotoken/action | Hiermee wordt een SSO-token opgehaald dat kan worden gebruikt om zich aan te melden bij API Management service verouderde portal als beheerder |
> | Actie | Microsoft.ApiManagement/service/groups/delete | Hiermee verwijdert u een specifieke groep van het API Management service-exemplaar. |
> | Actie | Microsoft.ApiManagement/service/groups/read | Een lijst met groepen die zijn gedefinieerd binnen een service-exemplaar. of worden de details opgehaald van de groep die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/groups/users/delete | Bestaande gebruiker uit bestaande groep verwijderen. |
> | Actie | Microsoft.ApiManagement/service/groups/users/read | Hiermee wordt een verzameling gebruikers entiteiten weer gegeven die aan de groep zijn gekoppeld. |
> | Actie | Micro soft. ApiManagement/service/groepen/gebruikers/schrijven | Een bestaande gebruiker toevoegen aan een bestaande groep |
> | Actie | Microsoft.ApiManagement/service/groups/write | Hiermee wordt een groep gemaakt of bijgewerkt. of worden de details van de groep die is opgegeven door de id, bijgewerkt. |
> | Actie | Microsoft.ApiManagement/service/identityProviders/delete | Hiermee verwijdert u de opgegeven id-provider configuratie. |
> | Actie | Micro soft. ApiManagement/service/identityProviders/listSecrets/actie | Hiermee worden geheimen van de identiteits provider opgehaald. |
> | Actie | Microsoft.ApiManagement/service/identityProviders/read | Geeft een lijst van een id-provider die is geconfigureerd in het opgegeven service-exemplaar. of worden de configuratie details van de ID-provider zonder geheimen opgehaald. |
> | Actie | Microsoft.ApiManagement/service/identityProviders/write | Hiermee wordt de Identity provider-configuratie gemaakt of bijgewerkt. of werkt een bestaande Identity provider-configuratie bij. |
> | Actie | Microsoft.ApiManagement/service/issues/read | Hiermee wordt een verzameling van problemen in het opgegeven service-exemplaar weer gegeven. of krijgt API Management probleem Details |
> | Actie | Microsoft.ApiManagement/service/locations/networkstatus/read | Hiermee wordt de netwerk toegangs status opgehaald van de resources waarvan de service afhankelijk is in de locatie. |
> | Actie | Microsoft.ApiManagement/service/loggers/delete | Hiermee verwijdert u de opgegeven logboek registratie. |
> | Actie | Microsoft.ApiManagement/service/loggers/read | Hiermee wordt een verzameling Logboeken in het opgegeven service-exemplaar weer gegeven. of worden de details opgehaald van het logboek dat is opgegeven door de id. |
> | Actie | Micro soft. ApiManagement/service/logger/schrijven | Hiermee wordt een logboek registratie gemaakt of bijgewerkt. of werkt een bestaande logger bij. |
> | Actie | Microsoft.ApiManagement/service/managedeployments/action | SKU/eenheden wijzigen, regionale implementaties van API Management service toevoegen/verwijderen |
> | Actie | Micro soft. ApiManagement/service/namedValues/verwijderen | Hiermee verwijdert u een specifieke benoemde waarde uit het API Management service-exemplaar. |
> | Actie | Micro soft. ApiManagement/service/namedValues/listSecrets/actie | Hiermee worden de geheimen opgehaald van de benoemde waarde die is opgegeven door de id. |
> | Actie | Micro soft. ApiManagement/service/namedValues/lezen | Hiermee wordt een verzameling benoemde waarden weer gegeven die zijn gedefinieerd binnen een service-exemplaar. of worden de details opgehaald van de benoemde waarde die is opgegeven door de id. |
> | Actie | Micro soft. ApiManagement/service/namedValues/schrijven | Hiermee wordt een benoemde waarde gemaakt of bijgewerkt. of werkt de specifieke benoemde waarde bij. |
> | Actie | Microsoft.ApiManagement/service/networkstatus/read | Hiermee wordt de netwerk toegangs status opgehaald van de resources waarvan de service afhankelijk is. |
> | Actie | Microsoft.ApiManagement/service/notifications/action | Hiermee wordt een melding verzonden naar een opgegeven gebruiker |
> | Actie | Microsoft.ApiManagement/service/notifications/read | Hiermee wordt een verzameling eigenschappen weer gegeven die zijn gedefinieerd binnen een service-exemplaar. of worden de details opgehaald van de melding die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Hiermee verwijdert u het e-mail bericht uit de lijst met meldingen. |
> | Actie | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Hiermee wordt de lijst met e-mail berichten voor meldingen ontvangen die zijn geabonneerd op een melding. |
> | Actie | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Hiermee wordt het e-mail adres toegevoegd aan de lijst met ontvangers voor de melding. |
> | Actie | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Hiermee verwijdert u de API Management gebruiker uit de lijst met meldingen. |
> | Actie | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Hiermee wordt de lijst opgehaald met de ontvanger van de meldings gebruiker die is geabonneerd op de melding. |
> | Actie | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Voegt de API Management gebruiker toe aan de lijst met ontvangers voor de melding. |
> | Actie | Microsoft.ApiManagement/service/notifications/write | Melding van API Management Uitgever maken of bijwerken. |
> | Actie | Microsoft.ApiManagement/service/openidConnectProviders/delete | Hiermee verwijdert u een specifieke OpenID Connect Connect-provider van het exemplaar van de API Management service. |
> | Actie | Micro soft. ApiManagement/service/openidConnectProviders/listSecrets/actie | Haalt specifieke geheimen voor OpenID Connect Connect provider op. |
> | Actie | Microsoft.ApiManagement/service/openidConnectProviders/read | Een lijst met alle OpenID Connect Connect-providers. of haalt een specifieke OpenID Connect Connect-provider zonder geheimen. |
> | Actie | Microsoft.ApiManagement/service/openidConnectProviders/write | Hiermee wordt de OpenID Connect Connect-provider gemaakt of bijgewerkt. of werkt de specifieke OpenID Connect Connect-provider bij. |
> | Actie | Micro soft. ApiManagement/service/operationresults/lezen | Hiermee wordt de huidige status van een langlopende bewerking opgehaald |
> | Actie | Microsoft.ApiManagement/service/policies/delete | Hiermee verwijdert u de globale beleids configuratie van de API Management-service. |
> | Actie | Micro soft. ApiManagement/service/beleid/lezen | Een lijst met alle globale beleids definities van de API Management-service. of de globale beleids definitie van de API Management-service ophalen. |
> | Actie | Micro soft. ApiManagement/service/beleid/schrijven | Hiermee wordt de globale beleids configuratie van de API Management-service gemaakt of bijgewerkt. |
> | Actie | Microsoft.ApiManagement/service/policy/delete | De beleids configuratie op Tenant niveau verwijderen |
> | Actie | Micro soft. ApiManagement/service/beleid/lezen | De beleids configuratie ophalen op Tenant niveau |
> | Actie | Micro soft. ApiManagement/service/beleid/schrijven | Beleids configuratie maken op Tenant niveau |
> | Actie | Micro soft. ApiManagement/service/policyDescriptions/lezen | Een lijst met alle beleids beschrijvingen. |
> | Actie | Microsoft.ApiManagement/service/policySnippets/read | Een lijst met alle beleids fragmenten. |
> | Actie | Microsoft.ApiManagement/service/portalsettings/read | Instellingen voor aanmelden ophalen voor de portal of instellingen voor het aanmelden ophalen voor de portal of voor het ophalen van delegerings instellingen voor de portal. |
> | Actie | Micro soft. ApiManagement/service/portalsettings/schrijven | Aanmeldings instellingen bijwerken. of om aanmeldings instellingen te maken of bij te werken. of update de instellingen voor aanmelden of het bijwerken van instellingen voor het aanmelden of het bijwerken van de instellingen voor delegering. of u instellingen voor delegering wilt maken of bijwerken. |
> | Actie | Microsoft.ApiManagement/service/products/apis/delete | Hiermee verwijdert u de opgegeven API uit het opgegeven product. |
> | Actie | Micro soft. ApiManagement/service/producten/api's/lezen | Hiermee wordt een verzameling weer gegeven van de Api's die zijn gekoppeld aan een product. |
> | Actie | Micro soft. ApiManagement/service/producten/api's/schrijven | Hiermee wordt een API toegevoegd aan het opgegeven product. |
> | Actie | Microsoft.ApiManagement/service/products/delete | Product verwijderen. |
> | Actie | Microsoft.ApiManagement/service/products/groups/delete | Hiermee verwijdert u de koppeling tussen de opgegeven groep en het product. |
> | Actie | Microsoft.ApiManagement/service/products/groups/read | Hier wordt de verzameling ontwikkelaars groepen weer gegeven die zijn gekoppeld aan het opgegeven product. |
> | Actie | Microsoft.ApiManagement/service/products/groups/write | Hiermee wordt de koppeling tussen de opgegeven ontwikkel groep en het opgegeven product toegevoegd. |
> | Actie | Microsoft.ApiManagement/service/products/policies/delete | Hiermee verwijdert u de beleids configuratie van het product. |
> | Actie | Microsoft.ApiManagement/service/products/policies/read | De configuratie van het beleid op product niveau ophalen. of de beleids configuratie op product niveau ophalen. |
> | Actie | Micro soft. ApiManagement/service/producten/beleid/schrijven | Hiermee wordt een beleids configuratie voor het product gemaakt of bijgewerkt. |
> | Actie | Microsoft.ApiManagement/service/products/policy/delete | De beleids configuratie op product niveau verwijderen |
> | Actie | Micro soft. ApiManagement/service/producten/beleid/lezen | De beleids configuratie ophalen op product niveau |
> | Actie | Micro soft. ApiManagement/service/producten/beleid/schrijven | Beleids configuratie maken op product niveau |
> | Actie | Microsoft.ApiManagement/service/products/read | Een lijst met producten in het opgegeven service-exemplaar. of worden de details opgehaald van het product dat is opgegeven door de id. |
> | Actie | Micro soft. ApiManagement/service/producten/abonnementen/lezen | Hiermee wordt de verzameling abonnementen op het opgegeven product weer gegeven. |
> | Actie | Microsoft.ApiManagement/service/products/tags/delete | Ontkoppel de tag uit het product. |
> | Actie | Micro soft. ApiManagement/service/Producten/Tags/lezen | Een lijst met alle labels die aan het product zijn gekoppeld. of Get-label dat is gekoppeld aan het product. |
> | Actie | Micro soft. ApiManagement/service/Producten/Tags/schrijven | Wijs tag toe aan het product. |
> | Actie | Micro soft. ApiManagement/service/producten/schrijven | Hiermee wordt een product gemaakt of bijgewerkt. of bestaande product gegevens bijwerken. |
> | Actie | Micro soft. ApiManagement/service/productsByTags/lezen | Een lijst met producten die aan Tags zijn gekoppeld. |
> | Actie | Microsoft.ApiManagement/service/properties/delete | Hiermee verwijdert u de specifieke eigenschap van het API Management service-exemplaar. |
> | Actie | Micro soft. ApiManagement/Service/Properties/listSecrets/Action | Hiermee worden de geheimen opgehaald van de eigenschap die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/properties/read | Hiermee wordt een verzameling eigenschappen weer gegeven die zijn gedefinieerd binnen een service-exemplaar. of worden de details opgehaald van de eigenschap die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/properties/write | Hiermee wordt een eigenschap gemaakt of bijgewerkt. of werkt de specifieke eigenschap bij. |
> | Actie | Microsoft.ApiManagement/service/quotas/periods/read | Waarde van quotum teller voor periode ophalen |
> | Actie | Microsoft.ApiManagement/service/quotas/periods/write | Huidige waarde van quotum teller instellen |
> | Actie | Microsoft.ApiManagement/service/quotas/read | Waarden voor quotum ophalen |
> | Actie | Microsoft.ApiManagement/service/quotas/write | Huidige waarde van quotum teller instellen |
> | Actie | Microsoft.ApiManagement/service/read | Meta gegevens voor een API Management service-exemplaar lezen |
> | Actie | Microsoft.ApiManagement/service/regions/read | Een lijst met alle Azure-regio's waarin de service bestaat. |
> | Actie | Microsoft.ApiManagement/service/reports/read | Rapport ophalen dat is geaggregeerd met tijds perioden of het rapport ophalen dat is geaggregeerd door de geografische regio of het rapport ophalen dat is geaggregeerd door ontwikkel aars.<br>of haal het rapport op dat is geaggregeerd door producten.<br>of haal het rapport op dat door Api's is geaggregeerd of maak het rapport dat is geaggregeerd door bewerkingen of ontvang het rapport dat is geaggregeerd met een abonnement.<br>of aanvragen rapport gegevens ophalen |
> | Actie | Microsoft.ApiManagement/service/restore/action | De API Management-service herstellen vanuit de opgegeven container in een opslag account dat door de gebruiker is opgegeven |
> | Actie | Microsoft.ApiManagement/service/subscriptions/delete | Hiermee verwijdert u het opgegeven abonnement. |
> | Actie | Micro soft. ApiManagement/service/abonnementen/listSecrets/actie | Hiermee worden de opgegeven abonnements sleutels opgehaald. |
> | Actie | Microsoft.ApiManagement/service/subscriptions/read | Een lijst met alle abonnementen van het API Management service-exemplaar. of Hiermee wordt de opgegeven abonnements entiteit (zonder sleutels) opgehaald. |
> | Actie | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Hiermee wordt de primaire sleutel van het bestaande abonnement van het API Management service-exemplaar opnieuw gegenereerd. |
> | Actie | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Hiermee wordt de secundaire sleutel van het bestaande abonnement van het exemplaar van de API Management-service opnieuw gegenereerd. |
> | Actie | Microsoft.ApiManagement/service/subscriptions/write | Hiermee wordt het abonnement van de opgegeven gebruiker voor het opgegeven product gemaakt of bijgewerkt. of werkt de details bij van een abonnement dat is opgegeven met de id. |
> | Actie | Microsoft.ApiManagement/service/tagResources/read | Hiermee wordt een verzameling resources die zijn gekoppeld aan Tags weer gegeven. |
> | Actie | Microsoft.ApiManagement/service/tags/delete | Hiermee verwijdert u een specifiek label van het API Management service-exemplaar. |
> | Actie | Microsoft.ApiManagement/service/tags/read | Hiermee wordt een verzameling tags weer gegeven die zijn gedefinieerd binnen een service-exemplaar. of worden de details opgehaald van het label dat door de id is opgegeven. |
> | Actie | Micro soft. ApiManagement/service/Tags/schrijven | Hiermee maakt u een tag. of werkt de details bij van het label dat is opgegeven met de id. |
> | Actie | Microsoft.ApiManagement/service/templates/delete | Standaard API Management e-mail sjabloon opnieuw instellen |
> | Actie | Microsoft.ApiManagement/service/templates/read | Hiermee worden alle e-mail sjablonen opgehaald of API Management e-mail sjabloon gegevens opgehaald |
> | Actie | Micro soft. ApiManagement/service/templates/schrijven | API Management e-mail sjabloon of updates API Management e-mail sjabloon maken of bijwerken |
> | Actie | Microsoft.ApiManagement/service/tenant/delete | Beleids configuratie voor de Tenant verwijderen |
> | Actie | Microsoft.ApiManagement/service/tenant/deploy/action | Voert een implementatie taak uit om wijzigingen van de opgegeven Git-vertakking toe te passen op de configuratie in de data base. |
> | Actie | Micro soft. ApiManagement/service/Tenant/listSecrets/actie | Details van Tenant toegangs gegevens ophalen |
> | Actie | Microsoft.ApiManagement/service/tenant/operationResults/read | Lijst met bewerkings resultaten ophalen of resultaat van een specifieke bewerking ophalen |
> | Actie | Microsoft.ApiManagement/service/tenant/read | De globale beleids definitie van de API Management-service ophalen. of Details van Tenant toegangs gegevens ophalen |
> | Actie | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Primaire toegangs sleutel opnieuw genereren |
> | Actie | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Secundaire toegangs sleutel opnieuw genereren |
> | Actie | Microsoft.ApiManagement/service/tenant/save/action | Maakt door voeren met configuratie momentopname aan de opgegeven vertakking in de opslag plaats |
> | Actie | Microsoft.ApiManagement/service/tenant/syncState/read | Status van de laatste Git-synchronisatie ophalen |
> | Actie | Microsoft.ApiManagement/service/tenant/validate/action | Hiermee worden wijzigingen van de opgegeven Git-vertakking gecontroleerd |
> | Actie | Microsoft.ApiManagement/service/tenant/write | Beleids configuratie instellen voor de Tenant of Details van Tenant toegangs gegevens bijwerken |
> | Actie | Microsoft.ApiManagement/service/updatecertificate/action | SSL-certificaat voor een API Management-service uploaden |
> | Actie | Microsoft.ApiManagement/service/updatehostname/action | Aangepaste domein namen instellen, bijwerken of verwijderen voor een API Management-service |
> | Actie | Microsoft.ApiManagement/service/users/action | Een nieuwe gebruiker registreren |
> | Actie | Microsoft.ApiManagement/service/users/confirmations/send/action | Bevestiging verzenden |
> | Actie | Microsoft.ApiManagement/service/users/delete | Hiermee verwijdert u een specifieke gebruiker. |
> | Actie | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Hiermee wordt een omleidings-URL met een verificatie token opgehaald voor het ondertekenen van een bepaalde gebruiker in de ontwikkelaars Portal. |
> | Actie | Microsoft.ApiManagement/service/users/groups/read | Een lijst met alle gebruikers groepen. |
> | Actie | Microsoft.ApiManagement/service/users/identities/read | Lijst met alle gebruikers-id's. |
> | Actie | Microsoft.ApiManagement/service/users/keys/read | Sleutels ophalen die aan de gebruiker zijn gekoppeld |
> | Actie | Microsoft.ApiManagement/service/users/read | Hiermee wordt een verzameling geregistreerde gebruikers weer gegeven in het opgegeven service-exemplaar. of worden de details opgehaald van de gebruiker die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/users/subscriptions/read | Hiermee wordt de verzameling abonnementen van de opgegeven gebruiker weer gegeven. |
> | Actie | Microsoft.ApiManagement/service/users/token/action | Hiermee haalt u het Shared Access Authorization-token voor de gebruiker. |
> | Actie | Microsoft.ApiManagement/service/users/write | Hiermee wordt een gebruiker gemaakt of bijgewerkt. of werkt de details bij van de gebruiker die is opgegeven door de id. |
> | Actie | Microsoft.ApiManagement/service/write | Een nieuw exemplaar van API Management service maken |
> | Actie | Micro soft. ApiManagement/registratie/actie | De registratie van het abonnement voor de resource provider micro soft. ApiManagement is ongedaan gemaakt |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. Authorization/classicAdministrators/verwijderen | Hiermee verwijdert u de beheerder van het abonnement. |
> | Actie | Micro soft. Authorization/classicAdministrators/operationstatuses/Read | Hiermee haalt u de bewerkings status van de beheerder van het abonnement. |
> | Actie | Microsoft.Authorization/classicAdministrators/read | Hiermee worden de beheerders voor het abonnement gelezen. |
> | Actie | Microsoft.Authorization/classicAdministrators/write | De beheerder toevoegen of wijzigen in een abonnement. |
> | Actie | Microsoft.Authorization/denyAssignments/delete | Een deny-toewijzing verwijderen bij het opgegeven bereik. |
> | Actie | Microsoft.Authorization/denyAssignments/read | Informatie over een weigerings toewijzing ophalen. |
> | Actie | Microsoft.Authorization/denyAssignments/write | Een weigerings toewijzing maken bij het opgegeven bereik. |
> | Actie | Microsoft.Authorization/elevateAccess/action | Hiermee krijgt de Gebruikerstoegangbeheerder toegang op tenantniveau |
> | Actie | Micro soft. autorisatie/vergren delen/verwijderen | Verwijder de vergren delingen bij het opgegeven bereik. |
> | Actie | Micro soft. autorisatie/vergren delen/lezen | Hiermee worden de vergren delingen voor het opgegeven bereik opgehaald. |
> | Actie | Microsoft.Authorization/locks/write | Vergren delingen aan het opgegeven bereik toevoegen. |
> | Actie | Micro soft. Authorization/Operations/Read | Hiermee wordt de lijst met bewerkingen opgehaald |
> | Actie | Micro soft. Authorization/permissions/Read | Een lijst met alle machtigingen die de aanroeper heeft voor een bepaald bereik. |
> | Actie | Micro soft. Authorization/Policies/audit/Action | De actie die wordt ondernomen als gevolg van de evaluatie van Azure Policy met het effect ' controle ' |
> | Actie | Micro soft. Authorization/Policies/auditIfNotExists/Action | De actie die wordt ondernomen als gevolg van de evaluatie van Azure Policy met het effect ' auditIfNotExists ' |
> | Actie | Micro soft. Authorization/Policies/Deny/Action | De actie die wordt ondernomen als gevolg van de evaluatie van Azure Policy met het effect ' deny ' |
> | Actie | Micro soft. Authorization/Policies/deployIfNotExists/Action | De actie die wordt ondernomen als gevolg van de evaluatie van Azure Policy met het effect ' deployIfNotExists ' |
> | Actie | Microsoft.Authorization/policyAssignments/delete | Een beleids toewijzing verwijderen bij het opgegeven bereik. |
> | Actie | Microsoft.Authorization/policyAssignments/read | Informatie over een beleids toewijzing ophalen. |
> | Actie | Microsoft.Authorization/policyAssignments/write | Een beleids toewijzing maken voor het opgegeven bereik. |
> | Actie | Micro soft. Authorization/policyDefinitions/verwijderen | Een beleids definitie verwijderen. |
> | Actie | Micro soft. Authorization/policyDefinitions/lezen | Informatie over een beleids definitie ophalen. |
> | Actie | Microsoft.Authorization/policyDefinitions/write | Maak een aangepaste beleids definitie. |
> | Actie | Microsoft.Authorization/policySetDefinitions/delete | Een definitie van een beleidsset verwijderen. |
> | Actie | Micro soft. Authorization/policySetDefinitions/lezen | Informatie over een definitie van een beleidsset ophalen. |
> | Actie | Microsoft.Authorization/policySetDefinitions/write | Maak een aangepaste definitie van een beleidsset. |
> | Actie | Microsoft.Authorization/providerOperations/read | Get-bewerkingen voor alle resource providers die kunnen worden gebruikt in roldefinities. |
> | Actie | Microsoft.Authorization/roleAssignments/delete | Een roltoewijzing verwijderen bij het opgegeven bereik. |
> | Actie | Micro soft. Authorization/roleAssignments/lezen | Informatie over een roltoewijzing ophalen. |
> | Actie | Microsoft.Authorization/roleAssignments/write | Een roltoewijzing maken bij het opgegeven bereik. |
> | Actie | Micro soft. Authorization/roleDefinitions/verwijderen | De opgegeven aangepaste roldefinitie verwijderen. |
> | Actie | Micro soft. Authorization/roleDefinitions/lezen | Informatie over een roldefinitie ophalen. |
> | Actie | Micro soft. Authorization/roleDefinitions/write | Een aangepaste roldefinitie maken of bijwerken met opgegeven machtigingen en toewijs bare bereiken. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | De registratie gegevens van een Azure Automation DSC lezen |
> | Actie | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Schrijft een aanvraag om Azure Automation DSC-sleutels opnieuw te genereren |
> | Actie | Microsoft.Automation/automationAccounts/certificates/delete | Hiermee wordt een Azure Automation-certificaat element verwijderd |
> | Actie | Microsoft.Automation/automationAccounts/certificates/getCount/action | Hiermee wordt het aantal certificaten gelezen |
> | Actie | Microsoft.Automation/automationAccounts/certificates/read | Hiermee wordt een Azure Automation-certificaat element opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/certificates/write | Hiermee wordt een Azure Automation certificaat-Asset gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/automationAccounts/compilationjobs/read | Hiermee wordt de compilatie van een Azure Automation DSC gelezen |
> | Actie | Microsoft.Automation/automationAccounts/compilationjobs/read | Hiermee wordt de compilatie van een Azure Automation DSC gelezen |
> | Actie | Microsoft.Automation/automationAccounts/compilationjobs/write | Hiermee wordt de compilatie van een Azure Automation DSC geschreven |
> | Actie | Microsoft.Automation/automationAccounts/compilationjobs/write | Hiermee wordt de compilatie van een Azure Automation DSC geschreven |
> | Actie | Microsoft.Automation/automationAccounts/configurations/content/read | Hiermee wordt de inhoud van de configuratie Media gelezen |
> | Actie | Microsoft.Automation/automationAccounts/configurations/delete | Hiermee wordt de inhoud van een Azure Automation DSC verwijderd |
> | Actie | Microsoft.Automation/automationAccounts/configurations/getCount/action | Hiermee wordt het aantal gelezen van een Azure Automation DSC-inhoud |
> | Actie | Microsoft.Automation/automationAccounts/configurations/read | Hiermee wordt de inhoud van een Azure Automation DSC opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/configurations/write | Schrijft de inhoud van een Azure Automation DSC |
> | Actie | Microsoft.Automation/automationAccounts/connections/delete | Hiermee wordt een Azure Automation verbindings element verwijderd |
> | Actie | Microsoft.Automation/automationAccounts/connections/getCount/action | Hiermee wordt het aantal verbindingen gelezen |
> | Actie | Microsoft.Automation/automationAccounts/connections/read | Hiermee wordt een Azure Automation verbindings element opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/connections/write | Hiermee wordt een Azure Automation verbindings-Asset gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/automationAccounts/connectionTypes/delete | Hiermee verwijdert u een Azure Automation verbindings type-Asset |
> | Actie | Microsoft.Automation/automationAccounts/connectionTypes/read | Hiermee wordt een Azure Automation verbindings type-Asset opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/connectionTypes/write | Hiermee maakt u een Azure Automation verbindings type-Asset |
> | Actie | Microsoft.Automation/automationAccounts/credentials/delete | Hiermee wordt een Azure Automation-referentie-element verwijderd |
> | Actie | Microsoft.Automation/automationAccounts/credentials/getCount/action | Hiermee wordt het aantal referenties gelezen |
> | Actie | Microsoft.Automation/automationAccounts/credentials/read | Hiermee wordt een Azure Automation referentie-element opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/credentials/write | Hiermee wordt een Azure Automation referentie-Asset gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/automationAccounts/delete | Hiermee verwijdert u een Azure Automation account |
> | Actie | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Hiermee verwijdert u Hybrid Runbook Worker resources |
> | Actie | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hybrid Runbook Worker resources lezen |
> | Actie | Microsoft.Automation/automationAccounts/jobs/output/read | Hiermee wordt de uitvoer van een taak opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/jobs/read | Hiermee wordt een Azure Automation-taak opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/jobs/resume/action | Hiermee wordt een Azure Automation taak hervat |
> | Actie | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Hiermee wordt de inhoud van het Azure Automation runbook opgehaald op het moment dat de taak wordt uitgevoerd |
> | Actie | Microsoft.Automation/automationAccounts/jobs/stop/action | Hiermee wordt een Azure Automation taak gestopt |
> | Actie | Microsoft.Automation/automationAccounts/jobs/streams/read | Hiermee wordt een Azure Automation taak stroom opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/jobs/streams/read | Hiermee wordt een Azure Automation taak stroom opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/jobs/suspend/action | Hiermee wordt een Azure Automation taak onderbroken |
> | Actie | Microsoft.Automation/automationAccounts/jobs/write | Hiermee maakt u een Azure Automation taak |
> | Actie | Microsoft.Automation/automationAccounts/jobSchedules/delete | Hiermee wordt een Azure Automation taak schema verwijderd |
> | Actie | Microsoft.Automation/automationAccounts/jobSchedules/read | Hiermee wordt een Azure Automation taak planning opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/jobSchedules/write | Hiermee maakt u een Azure Automation taak schema |
> | Actie | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Hiermee wordt de werk ruimte opgehaald die is gekoppeld aan het Automation-account |
> | Actie | Microsoft.Automation/automationAccounts/listKeys/action | Hiermee worden de sleutels voor het Automation-account gelezen |
> | Actie | Microsoft.Automation/automationAccounts/modules/activities/read | Hiermee worden Azure Automation activiteiten opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/modules/delete | Hiermee wordt een Azure Automation Power shell-module verwijderd |
> | Actie | Microsoft.Automation/automationAccounts/modules/getCount/action | Hiermee wordt het aantal Power shell-modules in het Automation-account opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/modules/read | Hiermee wordt een Azure Automation Power shell-module opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/modules/write | Hiermee wordt een Azure Automation Power shell-module gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Hiermee wordt de knooppunt configuratie van een Azure Automation DSC verwijderd |
> | Actie | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Hiermee wordt de inhoud van een Azure Automation DSC-knooppunt configuratie gelezen |
> | Actie | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Hiermee wordt de knooppunt configuratie van een Azure Automation DSC gelezen |
> | Actie | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Hiermee wordt de knooppunt configuratie van een Azure Automation DSC geschreven |
> | Actie | Microsoft.Automation/automationAccounts/nodecounts/read | Hiermee leest u de samen vatting van het aantal knoop punten voor het opgegeven type |
> | Actie | Microsoft.Automation/automationAccounts/nodes/delete | Verwijdert Azure Automation DSC-knoop punten |
> | Actie | Microsoft.Automation/automationAccounts/nodes/read | Azure Automation DSC-knoop punten lezen |
> | Actie | Micro soft. Automation/automationAccounts/knoop punten/rapporten/inhoud/lezen | Leest Azure Automation DSC-rapport inhoud |
> | Actie | Micro soft. Automation/automationAccounts/knoop punten/rapporten/lezen | Azure Automation DSC-rapporten lezen |
> | Actie | Microsoft.Automation/automationAccounts/nodes/write | Hiermee worden Azure Automation DSC-knoop punten gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Hiermee wordt Azure Automation TypeFields |
> | Actie | Microsoft.Automation/automationAccounts/python2Packages/delete | Hiermee verwijdert u een Azure Automation python 2-pakket |
> | Actie | Microsoft.Automation/automationAccounts/python2Packages/read | Hiermee wordt een Azure Automation python 2-pakket opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/python2Packages/write | Hiermee wordt een Azure Automation python 2-pakket gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/automationAccounts/python3Packages/delete | Hiermee verwijdert u een Azure Automation python 3-pakket |
> | Actie | Microsoft.Automation/automationAccounts/python3Packages/read | Hiermee wordt een Azure Automation python 3-pakket opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/python3Packages/write | Hiermee wordt een Azure Automation python 3-pakket gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/automationAccounts/read | Hiermee wordt een Azure Automation-account opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/content/read | Hiermee wordt de inhoud van een Azure Automation runbook opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/delete | Hiermee wordt een Azure Automation runbook verwijderd |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Hiermee wordt de inhoud van een Azure Automation-runbookconcept gemaakt |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Hiermee worden de resultaten van de concept bewerking Azure Automation runbook opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/draft/read | Hiermee wordt een Azure Automation runbook-concept opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Hiermee wordt een test taak voor een concept van een Azure Automation runbook opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Hiermee wordt een test taak voor een Azure Automation-runbookconcept hervat |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Hiermee wordt een test taak voor een Azure Automation-runbookconcept gestopt |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Hiermee wordt een test taak voor een Azure Automation-runbookconcept onderbroken |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Hiermee wordt een test taak voor een concept van een Azure Automation runbook gemaakt |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Wijzigingen aan een Azure Automation-runbookconcept ongedaan maken |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Hiermee wordt het aantal Azure Automation runbooks opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/publish/action | Hiermee wordt een Azure Automation-runbookconcept gepubliceerd |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/read | Hiermee wordt een Azure Automation runbook opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/runbooks/write | Hiermee wordt een Azure Automation runbook gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/automationAccounts/schedules/delete | Hiermee verwijdert u een Azure Automation schema-Asset |
> | Actie | Microsoft.Automation/automationAccounts/schedules/getCount/action | Hiermee wordt het aantal Azure Automation planningen opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/schedules/read | Hiermee wordt een Azure Automation schema-element opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/schedules/write | Hiermee wordt een Azure Automation Schedule-Asset gemaakt of bijgewerkt |
> | Actie | Micro soft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/lezen | Hiermee wordt een Azure Automation uitvoeren van de configuratie van de software-update-computer opgehaald |
> | Actie | Micro soft. Automation/automationAccounts/softwareUpdateConfigurationRuns/lezen | Hiermee wordt een Azure Automation uitvoeren van de configuratie van software-updates opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Hiermee wordt een Azure Automation Software-update configuratie verwijderd |
> | Actie | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Hiermee wordt een Azure Automation Software-update configuratie verwijderd |
> | Actie | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Hiermee wordt een Azure Automation configuratie van software-updates opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Hiermee wordt een Azure Automation configuratie van software-updates opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Hiermee wordt Azure Automation Software-update configuratie gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Hiermee wordt Azure Automation Software-update configuratie gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/automationAccounts/statistics/read | Hiermee worden Azure Automation statistieken opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Een Azure Automation update-implementatie machine ophalen |
> | Actie | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Hiermee wordt een Azure Automation patch taak voor update beheer opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/usages/read | Hiermee wordt het Azure Automation gebruik opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/variables/delete | Hiermee wordt een Azure Automation variabel activum verwijderd |
> | Actie | Microsoft.Automation/automationAccounts/variables/read | Hiermee wordt een Azure Automation variabel activum gelezen |
> | Actie | Microsoft.Automation/automationAccounts/variables/write | Hiermee wordt een Azure Automation variabele-Asset gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/automationAccounts/watchers/delete | Een Azure Automation Watcher-taak verwijderen |
> | Actie | Microsoft.Automation/automationAccounts/watchers/read | Hiermee wordt een Azure Automation Watcher-taak opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/watchers/start/action | Een Azure Automation Watcher-taak starten |
> | Actie | Microsoft.Automation/automationAccounts/watchers/stop/action | Een Azure Automation Watcher-taak stoppen |
> | Actie | Micro soft. Automation/automationAccounts/mijnen/stromen/lezen | Hiermee wordt een Azure Automation Watcher-taak stroom opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Een Azure Automation Watcher-taak acties verwijderen |
> | Actie | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Hiermee wordt een Azure Automation Watcher-taak acties opgehaald |
> | Actie | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Een Azure Automation Watcher-taak acties maken |
> | Actie | Microsoft.Automation/automationAccounts/watchers/write | Hiermee wordt een Azure Automation Watcher-taak gemaakt |
> | Actie | Microsoft.Automation/automationAccounts/webhooks/action | Hiermee wordt een URI voor een Azure Automation-webhook gegenereerd |
> | Actie | Microsoft.Automation/automationAccounts/webhooks/delete | Hiermee wordt een Azure Automation-webhook verwijderd  |
> | Actie | Microsoft.Automation/automationAccounts/webhooks/read | Hiermee wordt een Azure Automation-webhook gelezen |
> | Actie | Microsoft.Automation/automationAccounts/webhooks/write | Hiermee wordt een Azure Automation-webhook gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/automationAccounts/write | Hiermee wordt een Azure Automation-account gemaakt of bijgewerkt |
> | Actie | Microsoft.Automation/operations/read | Hiermee worden beschik bare bewerkingen voor Azure Automation resources opgehaald |
> | Actie | Microsoft.Automation/register/action | Hiermee wordt het abonnement geregistreerd bij Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.AzureActiveDirectory/b2cDirectories/delete | B2C Directory-resource verwijderen |
> | Actie | Microsoft.AzureActiveDirectory/b2cDirectories/read | B2C Directory-resource weer geven |
> | Actie | Microsoft.AzureActiveDirectory/b2cDirectories/write | B2C-Directory resource maken of bijwerken |
> | Actie | Microsoft.AzureActiveDirectory/b2ctenants/read | Een lijst met alle B2C-tenants waarbij de gebruiker lid is |
> | Actie | Microsoft.AzureActiveDirectory/operations/read | Alle API-bewerkingen lezen die beschikbaar zijn voor de resource provider micro soft. AzureActiveDirectory |
> | Actie | Microsoft.AzureActiveDirectory/register/action | Het abonnement voor de resource provider micro soft. AzureActiveDirectory registreren |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.AzureStack/Operations/read | Hiermee worden de eigenschappen van een resource provider bewerking opgehaald |
> | Actie | Microsoft.AzureStack/register/action | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. AzureStack |
> | Actie | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Hiermee verwijdert u een Azure Stack klant abonnement |
> | Actie | Microsoft.AzureStack/registrations/customerSubscriptions/read | Hiermee worden de eigenschappen van een Azure Stack klant abonnement opgehaald |
> | Actie | Microsoft.AzureStack/registrations/customerSubscriptions/write | Hiermee wordt een Azure Stack klant abonnement gemaakt of bijgewerkt |
> | Actie | Microsoft.AzureStack/registrations/delete | Hiermee wordt een Azure Stack registratie verwijderd |
> | Actie | Microsoft.AzureStack/registrations/getActivationKey/action | Hiermee wordt de nieuwste Azure Stack activerings sleutel opgehaald |
> | Actie | Micro soft. AzureStack/registraties/producten/getProduct/actie | Hiermee wordt Azure Stack Marketplace-product opgehaald |
> | Actie | Micro soft. AzureStack/registraties/producten/getProducts/actie | Hiermee wordt een lijst met Azure Stack Marketplace-Producten opgehaald |
> | Actie | Microsoft.AzureStack/registrations/products/listDetails/action | Hiermee worden uitgebreide details opgehaald voor een Azure Stack Marketplace-product |
> | Actie | Microsoft.AzureStack/registrations/products/read | Hiermee worden de eigenschappen van een Azure Stack Marketplace-product opgehaald |
> | Actie | Micro soft. AzureStack/registraties/producten/uploadProductLog/actie | Status en tijds tempel van de product bewerkings Azure Stack van de Marketplace vastleggen |
> | Actie | Microsoft.AzureStack/registrations/read | Hiermee worden de eigenschappen van een Azure Stack registratie opgehaald |
> | Actie | Microsoft.AzureStack/registrations/write | Hiermee wordt een Azure Stack registratie gemaakt of bijgewerkt |
> | Actie | Micro soft. AzureStack/verificationKeys/getCurrentKey/Action | Hiermee wordt de huidige versie van de open bare sleutel voor Azure Stack ondertekenen opgehaald |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Batch/batchAccounts/applications/delete | Hiermee wordt een toepassing verwijderd |
> | Actie | Microsoft.Batch/batchAccounts/applications/read | Een lijst met toepassingen of de eigenschappen van een toepassing ophalen |
> | Actie | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Hiermee wordt een toepassings pakket geactiveerd |
> | Actie | Microsoft.Batch/batchAccounts/applications/versions/delete | Hiermee wordt een toepassings pakket verwijderd |
> | Actie | Microsoft.Batch/batchAccounts/applications/versions/read | Hiermee worden de eigenschappen van een toepassings pakket opgehaald |
> | Actie | Microsoft.Batch/batchAccounts/applications/versions/write | Hiermee wordt een nieuw toepassings pakket gemaakt of een bestaand toepassings pakket bijgewerkt |
> | Actie | Microsoft.Batch/batchAccounts/applications/write | Hiermee wordt een nieuwe toepassing gemaakt of een bestaande toepassing bijgewerkt |
> | Actie | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Hiermee worden de resultaten opgehaald van een langlopende certificaat bewerking voor een batch-account |
> | Actie | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Hiermee wordt het verwijderen van een certificaat voor een batch-account geannuleerd |
> | Actie | Microsoft.Batch/batchAccounts/certificates/delete | Hiermee wordt een certificaat uit een batch-account verwijderd |
> | Actie | Microsoft.Batch/batchAccounts/certificates/read | Een lijst met certificaten voor een batch-account of de eigenschappen van een certificaat ophalen |
> | Actie | Microsoft.Batch/batchAccounts/certificates/write | Hiermee maakt u een nieuw certificaat op een batch-account of werkt u een bestaand certificaat bij |
> | Actie | Microsoft.Batch/batchAccounts/delete | Hiermee verwijdert u een batch-account |
> | DataAction | Micro soft. batch/batchAccounts/Jobs/verwijderen | Hiermee wordt een taak verwijderd uit een batch-account |
> | DataAction | Micro soft. batch/batchAccounts/Jobs/lezen | Een lijst met taken voor een batch-account of de eigenschappen van een taak ophalen |
> | DataAction | Micro soft. batch/batchAccounts/Jobs/schrijven | Hiermee maakt u een nieuwe taak voor een batch-account of werkt u een bestaande taak bij |
> | DataAction | Micro soft. batch/batchAccounts/jobSchedules/verwijderen | Hiermee verwijdert u een taak schema van een batch-account |
> | DataAction | Micro soft. batch/batchAccounts/jobSchedules/lezen | Een lijst met taak planningen voor een batch-account of de eigenschappen van een taak schema ophalen |
> | DataAction | Micro soft. batch/batchAccounts/jobSchedules/schrijven | Hiermee wordt een nieuwe taak planning gemaakt voor een batch-account of wordt een bestaande taak planning bijgewerkt |
> | Actie | Microsoft.Batch/batchAccounts/listkeys/action | Een lijst met toegangs sleutels voor een batch-account |
> | Actie | Microsoft.Batch/batchAccounts/operationResults/read | Hiermee worden de resultaten van een langlopende batch-account bewerking opgehaald |
> | Actie | Microsoft.Batch/batchAccounts/poolOperationResults/read | Hiermee worden de resultaten van een langlopende pool bewerking voor een batch-account opgehaald |
> | Actie | Microsoft.Batch/batchAccounts/pools/delete | Hiermee verwijdert u een groep van een batch-account |
> | Actie | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Hiermee schakelt u automatisch schalen uit voor een batch-account groep |
> | Actie | Microsoft.Batch/batchAccounts/pools/read | Een lijst met groepen in een batch-account of de eigenschappen van een groep ophalen |
> | Actie | Microsoft.Batch/batchAccounts/pools/stopResize/action | Hiermee wordt een actieve bewerking voor het wijzigen van de grootte van een batch-account groep gestopt |
> | Actie | Microsoft.Batch/batchAccounts/pools/write | Hiermee wordt een nieuwe pool gemaakt op een batch-account of een bestaande groep bijgewerkt |
> | Actie | Microsoft.Batch/batchAccounts/read | Een lijst met batch-accounts of de eigenschappen van een batch-account |
> | Actie | Microsoft.Batch/batchAccounts/regeneratekeys/action | Hiermee worden de toegangs sleutels voor een batch-account opnieuw gegenereerd |
> | Actie | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synchroniseert toegangs sleutels voor het automatische opslag account dat is geconfigureerd voor een batch-account |
> | Actie | Microsoft.Batch/batchAccounts/write | Hiermee maakt u een nieuw batch-account of werkt u een bestaand batch-account bij |
> | Actie | Microsoft.Batch/locations/accountOperationResults/read | Hiermee worden de resultaten van een langlopende batch-account bewerking opgehaald |
> | Actie | Microsoft.Batch/locations/checkNameAvailability/action | Hiermee wordt gecontroleerd of de account naam geldig is en niet wordt gebruikt. |
> | Actie | Microsoft.Batch/locations/quotas/read | Hiermee worden de batch quota's van het opgegeven abonnement in de opgegeven Azure-regio opgehaald |
> | Actie | Microsoft.Batch/operations/read | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn voor de resource provider van micro soft. batch |
> | Actie | Microsoft.Batch/register/action | Hiermee wordt het abonnement voor de batch-resource provider geregistreerd en wordt het maken van batch-accounts ingeschakeld |
> | Actie | Microsoft.Batch/unregister/action | Hiermee maakt u de registratie van het abonnement voor de batch-resource provider ongedaan voor het maken van batch-accounts. |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. facturering/billingAccounts/overeenkomsten/lezen |  |
> | Actie | Micro soft. facturering/billingAccounts/billingPermissions/lezen |  |
> | Actie | Micro soft. facturering/billingAccounts/billingProfiles/billingPermissions/lezen |  |
> | Actie | Micro soft. facturering/billingAccounts/billingProfiles/klanten/lezen |  |
> | Actie | Micro soft. facturering/billingAccounts/billingProfiles/facturen/prijzen overzicht/downloaden/actie |  |
> | Actie | Micro soft. facturering/billingAccounts/billingProfiles/invoiceSections/billingPermissions/lezen |  |
> | Actie | Micro soft. billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/Move/Action |  |
> | Actie | Micro soft. facturering/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/overdracht/actie |  |
> | Actie | Micro soft. facturering/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/actie |  |
> | Actie | Micro soft. billing/billingAccounts/billingProfiles/invoiceSections/Products/Move/Action |  |
> | Actie | Micro soft. billing/billingAccounts/billingProfiles/invoiceSections/Products/overboeking/actie |  |
> | Actie | Micro soft. billing/billingAccounts/billingProfiles/invoiceSections/Products/validateMoveEligibility/Action |  |
> | Actie | Micro soft. facturering/billingAccounts/billingProfiles/invoiceSections/lezen |  |
> | Actie | Micro soft. billing/billingAccounts/billingProfiles/invoiceSections/write |  |
> | Actie | Micro soft. facturering/billingAccounts/billingProfiles/prijzen overzicht/downloaden/actie |  |
> | Actie | Micro soft. facturering/billingAccounts/billingProfiles/lezen |  |
> | Actie | Micro soft. facturering/billingAccounts/billingProfiles/schrijven |  |
> | Actie | Micro soft. facturering/billingAccounts/billingProfiles/schrijven |  |
> | Actie | Micro soft. facturering/billingAccounts/billingSubscriptions/lezen |  |
> | Actie | Micro soft. billing/billingAccounts/klanten/billingPermissions/lezen |  |
> | Actie | Micro soft. facturering/billingAccounts/klanten/lezen |  |
> | Actie | Microsoft.Billing/billingAccounts/departments/read |  |
> | Actie | Micro soft. facturering/billingAccounts/enrollmentAccounts/billingPermissions/lezen |  |
> | Actie | Microsoft.Billing/billingAccounts/enrollmentAccounts/read |  |
> | Actie | Micro soft. facturering/billingAccounts/enrollmentDepartments/billingPermissions/lezen |  |
> | Actie | Micro soft. facturering/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/actie |  |
> | Actie | Micro soft. facturering/billingAccounts/producten/lezen |  |
> | Actie | Microsoft.Billing/billingAccounts/read |  |
> | Actie | Micro soft. facturering/billingAccounts/schrijven |  |
> | Actie | Microsoft.Billing/departments/read |  |
> | Actie | Micro soft. facturering/facturen/downloaden/actie | Factuur downloaden met Download koppeling uit lijst |
> | Actie | Microsoft.Billing/invoices/read |  |
> | Actie | Microsoft.Billing/register/action |  |
> | Actie | Micro soft. facturering/validateAddress/actie |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.BingMaps/mapApis/Delete | Verwijder bewerking |
> | Actie | Microsoft.BingMaps/mapApis/listSecrets/action | De geheimen weer geven |
> | Actie | Micro soft. BingMaps/mapApis/listSingleSignOnToken/Action | Verificatie token voor eenmalige aanmelding voor de resource lezen |
> | Actie | Microsoft.BingMaps/mapApis/Read | Lees bewerking |
> | Actie | Microsoft.BingMaps/mapApis/regenerateKey/action | Hiermee wordt de sleutel opnieuw gegenereerd |
> | Actie | Microsoft.BingMaps/mapApis/Write | Schrijf bewerking |
> | Actie | Microsoft.BingMaps/Operations/read | Beschrijving van de bewerking. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Blockchain/blockchainMembers/delete | Hiermee verwijdert u een bestaand Block Chain-lid. |
> | Actie | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | Hiermee worden bestaande Block Chain member API-sleutels opgehaald of weer gegeven. |
> | Actie | Microsoft.Blockchain/blockchainMembers/read | Hiermee wordt een lijst met bestaande Block Chain-leden opgehaald of weer gegeven. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Maakt verbinding met een trans actie-knoop punt van het block Chain-lid. |
> | Actie | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Hiermee verwijdert u een bestaand Block Chain member Trans Action-knoop punt. |
> | Actie | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Hiermee worden de bestaande API-sleutels van het block Chain member-trans actie knooppunt opgehaald of weer gegeven. |
> | Actie | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Hiermee wordt een lijst met bestaande Block Chain-leden transactie knooppunten opgehaald of weer gegeven. |
> | Actie | Micro soft. Block Chain/blockchainMembers/transactionNodes/write | Hiermee wordt een Block Chain-lid-transactie knooppunt gemaakt of bijgewerkt. |
> | Actie | Microsoft.Blockchain/blockchainMembers/write | Hiermee wordt een Block Chain-lid gemaakt of bijgewerkt. |
> | Actie | Micro soft. Block Chain/cordaMembers/verwijderen | Hiermee verwijdert u een bestaand Block Chain-koord. |
> | Actie | Micro soft. Block Chain/cordaMembers/lezen | Hiermee wordt een lijst met bestaande Block Chain-leden (en) opgehaald of weer gegeven. |
> | Actie | Micro soft. Block Chain/cordaMembers/schrijven | Hiermee wordt een Block Chain-koord-lid gemaakt of bijgewerkt. |
> | Actie | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Hiermee haalt u de bewerkings resultaten van Block Chain-leden op. |
> | Actie | Microsoft.Blockchain/locations/checkNameAvailability/action | Hiermee wordt gecontroleerd of de resource naam geldig is en niet wordt gebruikt. |
> | Actie | Microsoft.Blockchain/operations/read | Alle bewerkingen in de resource provider van micro soft Block Chain weer geven. |
> | Actie | Microsoft.Blockchain/register/action | Hiermee wordt het abonnement voor de Block Chain-resource provider geregistreerd. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Alle blauw drukken artefacten lezen |
> | Actie | Microsoft.Blueprint/blueprintAssignments/delete | Alle blauwdrukartefacten verwijderen |
> | Actie | Microsoft.Blueprint/blueprintAssignments/read | Alle blauw drukken artefacten lezen |
> | Actie | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | De object-id van de service-principal voor Azure blauw drukken ophalen. |
> | Actie | Microsoft.Blueprint/blueprintAssignments/write | Alle blauwdrukartefacten maken of bijwerken |
> | Actie | Microsoft.Blueprint/blueprints/artifacts/delete | Alle blauwdrukartefacten verwijderen |
> | Actie | Microsoft.Blueprint/blueprints/artifacts/read | Alle blauw drukken artefacten lezen |
> | Actie | Microsoft.Blueprint/blueprints/artifacts/write | Alle blauwdrukartefacten maken of bijwerken |
> | Actie | Microsoft.Blueprint/blueprints/delete | Blauw drukken verwijderen |
> | Actie | Microsoft.Blueprint/blueprints/read | Alle blauw drukken lezen |
> | Actie | Microsoft.Blueprint/blueprints/versions/artifacts/read | Alle blauw drukken artefacten lezen |
> | Actie | Microsoft.Blueprint/blueprints/versions/delete | Blauw drukken verwijderen |
> | Actie | Microsoft.Blueprint/blueprints/versions/read | Alle blauw drukken lezen |
> | Actie | Microsoft.Blueprint/blueprints/versions/write | Blauw drukken maken of bijwerken |
> | Actie | Microsoft.Blueprint/blueprints/write | Blauw drukken maken of bijwerken |
> | Actie | Microsoft.Blueprint/register/action | Hiermee wordt de resource provider voor Azure-blauw drukken geregistreerd |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.BotService/botServices/channels/delete | Een bot-service verwijderen |
> | Actie | Microsoft.BotService/botServices/channels/read | Een bot-service lezen |
> | Actie | Microsoft.BotService/botServices/channels/write | Een bot-service schrijven |
> | Actie | Microsoft.BotService/botServices/connections/delete | Een bot-service verwijderen |
> | Actie | Microsoft.BotService/botServices/connections/read | Een bot-service lezen |
> | Actie | Microsoft.BotService/botServices/connections/write | Een bot-service schrijven |
> | Actie | Microsoft.BotService/botServices/delete | Een bot-service verwijderen |
> | Actie | Microsoft.BotService/botServices/read | Een bot-service lezen |
> | Actie | Microsoft.BotService/botServices/write | Een bot-service schrijven |
> | Actie | Microsoft.BotService/locations/operationresults/read | De status van een asynchrone bewerking lezen |
> | Actie | Microsoft.BotService/Operations/read | De bewerkingen voor alle resource typen lezen |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Cache/checknameavailability/action | Hiermee wordt gecontroleerd of een naam beschikbaar is voor gebruik met een nieuwe Redis Cache |
> | Actie | Microsoft.Cache/locations/operationresults/read | Hiermee wordt het resultaat van een langlopende bewerking opgehaald waarvoor de header ' Location ' eerder naar de client is geretourneerd |
> | Actie | Microsoft.Cache/operations/read | Geeft een lijst van de bewerkingen die worden ondersteund door de provider micro soft. cache. |
> | Actie | Microsoft.Cache/redis/delete | De hele Redis Cache verwijderen |
> | Actie | Microsoft.Cache/redis/export/action | Redis gegevens exporteren naar voor voegsels voor vaste opslag-blobs in de opgegeven indeling |
> | Actie | Microsoft.Cache/redis/firewallRules/delete | IP-firewall regels van een Redis Cache verwijderen |
> | Actie | Microsoft.Cache/redis/firewallRules/read | De IP-firewall regels van een Redis Cache ophalen |
> | Actie | Microsoft.Cache/redis/firewallRules/write | De IP-firewall regels van een Redis Cache bewerken |
> | Actie | Microsoft.Cache/redis/forceReboot/action | Een cache-exemplaar geforceerd opnieuw opstarten, mogelijk met gegevens verlies. |
> | Actie | Microsoft.Cache/redis/import/action | Gegevens van een opgegeven indeling importeren uit meerdere blobs in redis |
> | Actie | Microsoft.Cache/redis/linkedservers/delete | Een gekoppelde server verwijderen uit een Redis Cache |
> | Actie | Microsoft.Cache/redis/linkedservers/read | Gekoppelde servers ophalen die zijn gekoppeld aan een redis-cache. |
> | Actie | Microsoft.Cache/redis/linkedservers/write | Een gekoppelde server toevoegen aan een Redis Cache |
> | Actie | Microsoft.Cache/redis/listKeys/action | De waarde van Redis Cache toegangs sleutels in de beheer portal weer geven |
> | Actie | Microsoft.Cache/redis/listUpgradeNotifications/read | Vermeld de nieuwste upgrade meldingen voor de cache-Tenant. |
> | Actie | Microsoft.Cache/redis/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor een Redis Cache |
> | Actie | Microsoft.Cache/redis/patchSchedules/delete | Het patch schema van een Redis Cache verwijderen |
> | Actie | Microsoft.Cache/redis/patchSchedules/read | Hiermee wordt het patch-schema van een Redis Cache opgehaald |
> | Actie | Microsoft.Cache/redis/patchSchedules/write | Het patch-schema van een Redis Cache wijzigen |
> | Actie | Microsoft.Cache/redis/read | De instellingen en configuratie van het Redis Cache in de beheer portal weer geven |
> | Actie | Microsoft.Cache/redis/regenerateKey/action | De waarde van Redis Cache toegangs sleutels in de beheer portal wijzigen |
> | Actie | Microsoft.Cache/redis/start/action | Start een cache-exemplaar. |
> | Actie | Microsoft.Cache/redis/stop/action | Een cache-exemplaar stoppen. |
> | Actie | Microsoft.Cache/redis/write | De instellingen en configuratie van de Redis Cache in de beheer portal wijzigen |
> | Actie | Microsoft.Cache/register/action | Hiermee wordt de resource provider micro soft. cache geregistreerd bij een abonnement |
> | Actie | Microsoft.Cache/unregister/action | Hiermee wordt de registratie van de resource provider micro soft. cache bij een abonnement ongedaan gemaakt |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Capacity/appliedreservations/read | Alle reserve ringen lezen |
> | Actie | Microsoft.Capacity/calculateexchange/action | Hiermee worden de Exchange-hoeveelheid en de prijs van nieuwe aankoop berekend en worden er beleids fouten geretourneerd. |
> | Actie | Microsoft.Capacity/calculateprice/action | De reserverings prijs berekenen |
> | Actie | Micro soft. capacity/catalogi/lezen | Catalogus van reserve ring lezen |
> | Actie | Microsoft.Capacity/checkoffers/action | Alle abonnements aanbiedingen controleren |
> | Actie | Microsoft.Capacity/checkscopes/action | Een abonnement controleren |
> | Actie | Microsoft.Capacity/commercialreservationorders/read | Reserverings orders ophalen die zijn gemaakt in een Tenant |
> | Actie | Microsoft.Capacity/exchange/action | Alle reserve ringen uitwisselen |
> | Actie | Microsoft.Capacity/operations/read | Een wille keurige bewerking lezen |
> | Actie | Micro soft. capacity/REGI ster/actie | Hiermee wordt de resource provider voor capaciteit geregistreerd en wordt het maken van capaciteits resources ingeschakeld. |
> | Actie | Microsoft.Capacity/reservationorders/action | Een reserve ring bijwerken |
> | Actie | Microsoft.Capacity/reservationorders/availablescopes/action | Beschik bare bereik zoeken |
> | Actie | Microsoft.Capacity/reservationorders/calculaterefund/action | Hiermee worden de terugbetalings hoeveelheid en de prijs van nieuwe aankoop berekend en worden er beleids fouten geretourneerd. |
> | Actie | Microsoft.Capacity/reservationorders/delete | Een reserve ring verwijderen |
> | Actie | Microsoft.Capacity/reservationorders/merge/action | Een reserve ring samen voegen |
> | Actie | Microsoft.Capacity/reservationorders/read | Alle reserve ringen lezen |
> | Actie | Microsoft.Capacity/reservationorders/reservations/action | Een reserve ring bijwerken |
> | Actie | Micro soft. capacity/reservationorders/reserve ringen/availablescopes/actie | Beschik bare bereik zoeken |
> | Actie | Microsoft.Capacity/reservationorders/reservations/delete | Een reserve ring verwijderen |
> | Actie | Microsoft.Capacity/reservationorders/reservations/read | Alle reserve ringen lezen |
> | Actie | Microsoft.Capacity/reservationorders/reservations/revisions/read | Alle reserve ringen lezen |
> | Actie | Microsoft.Capacity/reservationorders/reservations/write | Een reserve ring maken |
> | Actie | Microsoft.Capacity/reservationorders/return/action | Reserve ring retour neren |
> | Actie | Microsoft.Capacity/reservationorders/split/action | Alle reserve ringen splitsen |
> | Actie | Microsoft.Capacity/reservationorders/swap/action | Alle reserve ringen omwisselen |
> | Actie | Microsoft.Capacity/reservationorders/write | Een reserve ring maken |
> | Actie | Microsoft.Capacity/tenants/register/action | Een Tenant registreren |
> | Actie | Micro soft. capacity/registratie/actie | De registratie van een Tenant ongedaan maken |
> | Actie | Microsoft.Capacity/validatereservationorder/action | Een reserve ring valideren |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. CDN/cdnwebapplicationfirewallmanagedrulesets/verwijderen |  |
> | Actie | Micro soft. CDN/cdnwebapplicationfirewallmanagedrulesets/lezen |  |
> | Actie | Micro soft. CDN/cdnwebapplicationfirewallmanagedrulesets/schrijven |  |
> | Actie | Micro soft. CDN/cdnwebapplicationfirewallpolicies/verwijderen |  |
> | Actie | Micro soft. CDN/cdnwebapplicationfirewallpolicies/lezen |  |
> | Actie | Micro soft. CDN/cdnwebapplicationfirewallpolicies/schrijven |  |
> | Actie | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Actie | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Actie | Micro soft. CDN/edgenodes/verwijderen |  |
> | Actie | Microsoft.Cdn/edgenodes/read |  |
> | Actie | Micro soft. CDN/edgenodes/schrijven |  |
> | Actie | Micro soft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/verwijderen |  |
> | Actie | Micro soft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/lezen |  |
> | Actie | Micro soft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/schrijven |  |
> | Actie | Microsoft.Cdn/operationresults/delete |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Actie | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Actie | Microsoft.Cdn/operationresults/read |  |
> | Actie | Microsoft.Cdn/operationresults/write |  |
> | Actie | Microsoft.Cdn/operations/read |  |
> | Actie | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Actie | Microsoft.Cdn/profiles/delete |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/read |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Actie | Microsoft.Cdn/profiles/endpoints/write |  |
> | Actie | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Actie | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Actie | Microsoft.Cdn/profiles/read |  |
> | Actie | Microsoft.Cdn/profiles/write |  |
> | Actie | Microsoft.Cdn/register/action | Hiermee wordt het abonnement voor de CDN-resource provider geregistreerd en wordt het maken van CDN-profielen ingeschakeld. |
> | Actie | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Een bestaand certificaat verwijderen |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | De lijst met certificaten ophalen |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Een nieuw certificaat toevoegen of een bestaande bijwerken |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/Delete | Een bestaande AppServiceCertificate verwijderen |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/Read | De lijst met CertificateOrders ophalen |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Een bestaande certificaat bestelling opnieuw uitgeven |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Een bestaande certificaat bestelling vernieuwen |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | E-mail van certificaat opnieuw verzenden |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | E-mail met aanvraag opnieuw verzenden naar een ander e-mail adres |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Site zegel ophalen voor een uitgegeven App Service Certificate |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | De lijst met certificaat acties ophalen |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | E-mail geschiedenis van certificaat ophalen |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Domein eigendom verifiëren |
> | Actie | Microsoft.CertificateRegistration/certificateOrders/Write | Een nieuwe certificaat bestelling toevoegen of een bestaand item bijwerken |
> | Actie | Microsoft.CertificateRegistration/operations/Read | Alle bewerkingen van de registratie van het app service-certificaat weer geven |
> | Actie | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Service-Principal inrichten voor service-app-Principal |
> | Actie | Microsoft.CertificateRegistration/register/action | De resource provider van micro soft-certificaten registreren voor het abonnement |
> | Actie | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Het certificaat aankoop object valideren zonder dit te verzenden |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.ClassicCompute/capabilities/read | Hiermee worden de mogelijkheden |
> | Actie | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Hiermee wordt de beschik baarheid van een bepaalde domein naam gecontroleerd. |
> | Actie | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Hiermee wordt de beschik baarheid van een bepaalde domein naam opgehaald. |
> | Actie | Microsoft.ClassicCompute/domainNames/active/write | Hiermee stelt u de naam van het actieve domein. |
> | Actie | Microsoft.ClassicCompute/domainNames/availabilitySets/read | De beschikbaarheidsset weer geven voor de resource. |
> | Actie | Microsoft.ClassicCompute/domainNames/capabilities/read | Hiermee worden de domein naam mogelijkheden weer gegeven |
> | Actie | Microsoft.ClassicCompute/domainNames/delete | De domein namen voor resources verwijderen. |
> | Actie | Micro soft. ClassicCompute/domainName/deploymentslots/lezen | Hiermee worden de implementatie sites weer gegeven. |
> | Actie | Micro soft. ClassicCompute/domainNamees/deploymentslots/roles/lezen | Rol voor implementatie sleuf van domein naam ophalen |
> | Actie | Micro soft. ClassicCompute/domainNamees/deploymentslots/roles/roleinstances/lezen | Rolinstantie ophalen voor rol op implementatie site van domein naam |
> | Actie | Micro soft. ClassicCompute/domein naam/deploymentslots/status/lezen | Haal de status van de implementatie site op. |
> | Actie | Micro soft. ClassicCompute/domein naam/deploymentslots/status/schrijven | Voeg de status van de implementatie site toe. |
> | Actie | Micro soft. ClassicCompute/domainNamees/deploymentslots/upgrade Domain/lezen | Upgrade domein ophalen voor implementatie site op domein naam |
> | Actie | Micro soft. ClassicCompute/domein-en deploymentslots/upgrade Domain/schrijven | Upgrade domein bijwerken voor implementatie site op domein naam |
> | Actie | Micro soft. ClassicCompute/domainName/deploymentslots/schrijven | Hiermee wordt de implementatie gemaakt of bijgewerkt. |
> | Actie | Microsoft.ClassicCompute/domainNames/extensions/delete | Verwijder de domeinnaam extensies. |
> | Actie | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Hiermee wordt de bewerkings status van de domeinnaam extensies gelezen. |
> | Actie | Micro soft. ClassicCompute/domein naam/extensies/uitbrei dingen/lezen | Retourneert de domeinnaam extensies. |
> | Actie | Microsoft.ClassicCompute/domainNames/extensions/write | Voeg de domeinnaam extensies toe. |
> | Actie | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Verwijder een nieuwe interne taak verdeling. |
> | Actie | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Hiermee wordt de bewerkings status van de interne load balancers van domein namen gelezen. |
> | Actie | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Hiermee worden de interne load balancers opgehaald. |
> | Actie | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Hiermee maakt u een nieuwe interne taak verdeling. |
> | Actie | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Hiermee wordt de bewerkings status van de eindpunt sets met gelijke taak verdeling van domein namen gelezen. |
> | Actie | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | De eindpunt sets met gelijke taak verdeling ophalen. |
> | Actie | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Voeg de eindpuntset met gelijke taak verdeling toe. |
> | Actie | Micro soft. ClassicCompute/domainName/operationstatuses/lezen | De bewerkings status van de domein naam ophalen. |
> | Actie | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Hiermee wordt de bewerkings status van de domeinnaam extensies gelezen. |
> | Actie | Microsoft.ClassicCompute/domainNames/read | De domein namen voor resources retour neren. |
> | Actie | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | De gebruikte service certificaten verwijderen. |
> | Actie | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Hiermee wordt de bewerkings status van de service certificaten van domein namen gelezen. |
> | Actie | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Hiermee worden de gebruikte service certificaten geretourneerd. |
> | Actie | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | De gebruikte service certificaten toevoegen of wijzigen. |
> | Actie | Micro soft. ClassicCompute/Domeins/sleuven/abortMigration/actie | De migratie van een implementatie site afbreekt. |
> | Actie | Micro soft. ClassicCompute/Domeins/sleuven/commitMigration/actie | Hiermee wordt de migratie van een implementatie site doorgevoerd. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/delete | Hiermee verwijdert u een opgegeven implementatie sleuf. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Hiermee wordt de bewerkings status van de domeinnaam sleuven gelezen. |
> | Actie | Micro soft. ClassicCompute/Domeins/sleuven/prepareMigration/actie | Bereidt de migratie van een implementatie site voor. |
> | Actie | Micro soft. ClassicCompute/domein naam/sleuven/lezen | Hiermee worden de implementatie sites weer gegeven. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Verwijder de extensie verwijzing voor de implementatie site-rol. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Hiermee wordt de bewerkings status van de extensies voor de extensie rollen van domein namen sleuven gelezen. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Retourneert de extensie verwijzing voor de implementatie site-rol. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | De extensie verwijzing voor de implementatie site functie toevoegen of wijzigen. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | De metrische definitie van de roldefinitie voor de domein naam ophalen. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Haal de metrische gegevens voor de rol voor de domein naam op. |
> | Actie | Micro soft. ClassicCompute/domainNamees/sleuven/rollen/operationstatuses/lezen | De bewerkings status voor de rol van de domeinnaam sleuf ophalen. |
> | Actie | Micro soft. ClassicCompute/domainNamees/sleuven/rollen/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Actie | Micro soft. ClassicCompute/Domeins/sleuven/rollen/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de metrische definities opgehaald. |
> | Actie | Micro soft. ClassicCompute/domein naam/sleuven/rollen/lezen | Haal de rol op voor de implementatie site. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Down loadt extern bureau blad-verbindings bestand voor de rolinstantie voor de rol van domein naam sleuf. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Hiermee wordt de bewerkings status van de rolinstantie voor de rol van de domein naam sleuf opgehaald. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Haal de rolinstantie op. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Maakt de rolinstantie opnieuw. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Installatie kopieën van de rolinstantie terugzetten. |
> | Actie | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Hiermee worden rolinstanties opnieuw gestart. |
> | Actie | Micro soft. ClassicCompute/domainNamees/sleuven/rollen/sku's/lezen | Haal de rol-SKU voor de implementatie site op. |
> | Actie | Micro soft. ClassicCompute/domein naam/sleuven/rollen/schrijven | Voeg een rol toe voor de implementatie site. |
> | Actie | Micro soft. ClassicCompute/domein naam/sleuven/starten/actie | Hiermee wordt een implementatie site gestart. |
> | Actie | Micro soft. ClassicCompute/domein naam/sleuven/status/starten/schrijven | Hiermee wordt de status van de implementatie site gewijzigd in gestopt. |
> | Actie | Micro soft. ClassicCompute/domein naam/sleuven/status/stoppen/schrijven | Hiermee wordt de status van de implementatie site gewijzigd in gestart. |
> | Actie | Micro soft. ClassicCompute/domein naam/sleuven/stoppen/actie | Hiermee wordt de implementatie site onderbroken. |
> | Actie | Micro soft. ClassicCompute/domein naam/sleuven/upgrade Domain/schrijven | Voer een upgrade uit voor het domein. |
> | Actie | Micro soft. ClassicCompute/Domeins/sleuven/validateMigration/actie | Hiermee wordt de migratie van een implementatie site gevalideerd. |
> | Actie | Micro soft. ClassicCompute/domein naam/sleuven/schrijven | Hiermee wordt de implementatie gemaakt of bijgewerkt. |
> | Actie | Microsoft.ClassicCompute/domainNames/swap/action | De faserings sleuf wordt overgewisseld naar de productie sleuf. |
> | Actie | Microsoft.ClassicCompute/domainNames/write | De domein namen voor resources toevoegen of wijzigen. |
> | Actie | Microsoft.ClassicCompute/moveSubscriptionResources/action | Verplaats alle klassieke resources naar een ander abonnement. |
> | Actie | Microsoft.ClassicCompute/operatingSystemFamilies/read | Geeft een lijst van de gast besturingssysteem families die beschikbaar zijn in Microsoft Azure en bevat ook een lijst met de besturingssysteem versies die beschikbaar zijn voor elke familie. |
> | Actie | Microsoft.ClassicCompute/operatingSystems/read | Hier worden de versies weer gegeven van het gast besturingssysteem dat momenteel beschikbaar is in Microsoft Azure. |
> | Actie | Microsoft.ClassicCompute/operations/read | Hiermee wordt de lijst met bewerkingen opgehaald. |
> | Actie | Microsoft.ClassicCompute/operationStatuses/read | Hiermee wordt de bewerkings status van de resource gelezen. |
> | Actie | Microsoft.ClassicCompute/quotas/read | Het quotum voor het abonnement ophalen. |
> | Actie | Microsoft.ClassicCompute/register/action | Registreren bij klassieke compute |
> | Actie | Microsoft.ClassicCompute/resourceTypes/skus/read | Hiermee wordt de SKU-lijst opgehaald voor ondersteunde resource typen. |
> | Actie | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Valideer de beschik baarheid van het abonnement voor een klassieke Verplaats bewerking. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Hiermee verwijdert u de netwerk beveiligings groep die is gekoppeld aan de virtuele machine. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Hiermee wordt de bewerkings status gelezen van de virtuele machines die zijn gekoppeld aan netwerk beveiligings groepen. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Hiermee wordt de netwerk beveiligings groep opgehaald die aan de virtuele machine is gekoppeld. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Hiermee wordt een netwerk beveiligings groep toegevoegd die aan de virtuele machine is gekoppeld. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Hiermee worden de mogelijke asynchrone bewerkingen opgehaald |
> | Actie | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Hiermee koppelt u een gegevens schijf aan een virtuele machine. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/capture/action | Een virtuele machine vastleggen. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/delete | Virtuele machines worden verwijderd. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Een gegevens schijf loskoppelen van de virtuele machine. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Diagnostische instellingen van virtuele machine ophalen. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/disks/read | Hiermee wordt een lijst met gegevens schijven opgehaald |
> | Actie | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Hiermee downloadt u het RDP-bestand voor de virtuele machine. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Hiermee wordt de bewerkings status van de extensies van de virtuele machines gelezen. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/extensions/read | Hiermee wordt de extensie van de virtuele machine opgehaald. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/extensions/write | Hiermee wordt de extensie van de virtuele machine geplaatst. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | De metrische definitie van de virtuele machine ophalen. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/metrics/read | Hiermee worden de metrische gegevens opgehaald. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Hiermee verwijdert u de netwerk beveiligings groep die is gekoppeld aan de netwerk interface. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Hiermee wordt de bewerkings status gelezen van de virtuele machines die zijn gekoppeld aan netwerk beveiligings groepen. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Hiermee wordt de netwerk beveiligings groep opgehaald die aan de netwerk interface is gekoppeld. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Hiermee wordt een netwerk beveiligings groep toegevoegd die aan de netwerk interface is gekoppeld. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Hiermee wordt de bewerkings status van de virtuele machines gelezen. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Hiermee wordt onderhoud uitgevoerd op de virtuele machine. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | De diagnostische instellingen ophalen. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Diagnostische instellingen toevoegen of wijzigen. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de metrische definities opgehaald. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/read | Hiermee wordt een lijst met virtuele machines opgehaald. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Hiermee wordt de virtuele machine opnieuw geïmplementeerd. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/restart/action | Hiermee worden virtuele machines opnieuw opgestart. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Sluit de virtuele machine af. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/start/action | Start de virtuele machine. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/stop/action | Hiermee wordt de virtuele machine gestopt. |
> | Actie | Microsoft.ClassicCompute/virtualMachines/write | Virtuele machines toevoegen of wijzigen. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Haal de status van een snelle route-cross Connection-bewerking op. |
> | Actie | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Verwijder de peering voor Express route-Kruis verbinding. |
> | Actie | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | De bewerkings status van de peering van een snelle route Kruis verbindings bewerking ophalen. |
> | Actie | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Ontvang snelle route Kruis verbinding peering. |
> | Actie | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Verbindings peering voor Express route toevoegen. |
> | Actie | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Ontvang snelle route-cross-verbindingen. |
> | Actie | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Snelle route-cross-verbindingen toevoegen. |
> | Actie | Micro soft. ClassicNetwork/gatewaySupportedDevices/lezen | Hiermee wordt de lijst met ondersteunde apparaten opgehaald. |
> | Actie | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Hiermee verwijdert u de netwerk beveiligings groep. |
> | Actie | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Hiermee wordt de bewerkings status van de netwerk beveiligings groep gelezen. |
> | Actie | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee worden de diagnostische instellingen voor netwerk beveiligings groepen opgehaald |
> | Actie | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee worden de diagnostische instellingen voor netwerk beveiligings groepen gemaakt of bijgewerkt, deze bewerking wordt aangevuld door de Insights-resource provider. |
> | Actie | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Hiermee worden de gebeurtenissen voor de netwerk beveiligings groep opgehaald |
> | Actie | Microsoft.ClassicNetwork/networkSecurityGroups/read | Hiermee wordt de netwerk beveiligings groep opgehaald. |
> | Actie | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Hiermee verwijdert u de beveiligings regel. |
> | Actie | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Hiermee wordt de bewerkings status van de beveiligings regels voor de netwerk beveiligings groep gelezen. |
> | Actie | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Hiermee wordt de beveiligings regel opgehaald. |
> | Actie | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Hiermee wordt een beveiligings regel toegevoegd of bijgewerkt. |
> | Actie | Microsoft.ClassicNetwork/networkSecurityGroups/write | Hiermee voegt u een nieuwe netwerk beveiligings groep toe. |
> | Actie | Microsoft.ClassicNetwork/operations/read | Klassieke netwerk bewerkingen ophalen. |
> | Actie | Microsoft.ClassicNetwork/quotas/read | Het quotum voor het abonnement ophalen. |
> | Actie | Microsoft.ClassicNetwork/register/action | Registreren bij klassiek netwerk |
> | Actie | Microsoft.ClassicNetwork/reservedIps/delete | Verwijder een gereserveerd IP-adres. |
> | Actie | Microsoft.ClassicNetwork/reservedIps/join/action | Een gereserveerd IP-adres toevoegen |
> | Actie | Microsoft.ClassicNetwork/reservedIps/link/action | Een gereserveerd IP-adres koppelen |
> | Actie | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Hiermee wordt de bewerkings status van de gereserveerde ip's gelezen. |
> | Actie | Microsoft.ClassicNetwork/reservedIps/read | Hiermee worden de gereserveerde Ip's opgehaald |
> | Actie | Microsoft.ClassicNetwork/reservedIps/write | Een nieuw gereserveerd IP-adres toevoegen |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | De migratie van een Virtual Network afbreken |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/capabilities/lezen | Hiermee worden de mogelijkheden |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Controleert de beschik baarheid van een bepaald IP-adres in een virtueel netwerk. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Hiermee wordt de migratie van een Virtual Network doorgevoerd |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/verwijderen | Hiermee verwijdert u het virtuele netwerk. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/verwijderen | Hiermee wordt de intrekking van een client certificaat ongedaan. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/lezen | Lees de ingetrokken client certificaten. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/schrijven | Hiermee wordt een client certificaat ingetrokken. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Hiermee verwijdert u het client certificaat van de gateway van het virtuele netwerk. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/downloaden/actie | Hiermee wordt het certificaat gedownload via de vinger afdruk. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Geeft een lijst van het certificaat pakket voor de virtuele netwerk gateway. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Zoek de basis certificaten van de client. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Hiermee wordt een nieuw client basis certificaat geüpload. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/Connections/Connect/Action | Verbindt een site met site gateway verbinding. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Verbreekt de verbinding van een site met site gateway verbinding. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/verbindingen/lezen | Hiermee haalt u de lijst met verbindingen op. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/verbindingen/testen/actie | Test een site-naar-site gateway verbinding. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/verwijderen | Hiermee verwijdert u de gateway van het virtuele netwerk. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/actie | Hiermee downloadt u het configuratie script voor het apparaat. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/actie | Hiermee downloadt u de diagnostische gegevens van de gateway. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/actie | Hiermee wordt de circuit service sleutel opgehaald. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/listPackage/actie | Hiermee wordt het gateway pakket voor het virtuele netwerk weer gegeven. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Hiermee wordt de bewerkings status voor de virtuele netwerk gateways gelezen. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/pakketten/lezen | Hiermee wordt het gateway pakket voor het virtuele netwerk opgehaald. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/lezen | Hiermee worden de virtuele netwerk gateways opgehaald. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/startDiagnostics/actie | Diagnostische gegevens voor de gateway van het virtuele netwerk starten. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/actie | Hiermee stopt u de diagnostische gegevens voor de virtuele netwerk gateway. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/gateways/schrijven | Hiermee voegt u een virtuele netwerk gateway toe. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/deelname/actie | Voegt het virtuele netwerk samen. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Hiermee wordt de bewerkings status van de virtuele netwerken gelezen. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/peer/Action | Peert een virtueel netwerk met een ander virtueel netwerk. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/prepareMigration/Action | Bereidt de migratie van een Virtual Network voor |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/lezen | Het virtuele netwerk ophalen. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Hiermee wordt de proxy voor de peering van het externe virtuele netwerk verwijderd. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Hiermee wordt de proxy voor de peering van het externe virtuele netwerk opgehaald. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Hiermee wordt de proxy voor de peering van het externe virtuele netwerk toegevoegd of bijgewerkt. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Hiermee verwijdert u de netwerk beveiligings groep die aan het subnet is gekoppeld. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Hiermee wordt de bewerkings status gelezen van het subnet van het virtuele netwerk dat aan de netwerk beveiligings groep is gekoppeld. |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Hiermee wordt de netwerk beveiligings groep opgehaald die aan het subnet is gekoppeld. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/subnetten/associatedNetworkSecurityGroups/schrijven | Hiermee wordt een netwerk beveiligings groep toegevoegd die aan het subnet is gekoppeld. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/validateMigration/Action | Valideert de migratie van een Virtual Network |
> | Actie | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Hiermee wordt de peering van het virtuele netwerk opgehaald. |
> | Actie | Micro soft. ClassicNetwork/virtualNetworks/schrijven | Voeg een nieuw virtueel netwerk toe. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. ClassicStorage/capabilities/lezen | Hiermee worden de mogelijkheden |
> | Actie | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Hiermee wordt gecontroleerd op de beschik baarheid van een opslag account. |
> | Actie | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | De beschik baarheid van een opslag account ophalen. |
> | Actie | Microsoft.ClassicStorage/disks/read | Retourneert de schijf van het opslag account. |
> | Actie | Micro soft. ClassicStorage/images/operationstatuses/lezen | Haalt de bewerkings status van de afbeelding op. |
> | Actie | Microsoft.ClassicStorage/images/read | Hiermee wordt de afbeelding geretourneerd. |
> | Actie | Microsoft.ClassicStorage/operations/read | Hiermee worden klassieke opslag bewerkingen opgehaald |
> | Actie | Microsoft.ClassicStorage/osImages/read | Retourneert de installatie kopie van het besturings systeem. |
> | Actie | Microsoft.ClassicStorage/osPlatformImages/read | Hiermee wordt de installatie kopie van het besturings systeem opgehaald. |
> | Actie | Microsoft.ClassicStorage/publicImages/read | Hiermee wordt de installatie kopie van de open bare virtuele machine opgehaald. |
> | Actie | Micro soft. ClassicStorage/quota's/lezen | Het quotum voor het abonnement ophalen. |
> | Actie | Micro soft. ClassicStorage/REGI ster/actie | Registreren bij klassieke opslag |
> | Actie | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | De migratie van een opslag account wordt afgebroken. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/blobServices/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/blobServices/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/blobServices/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Hiermee wordt de migratie van een opslag account doorgevoerd. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/delete | Verwijder het opslag account. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/disks/delete | Hiermee verwijdert u een opgegeven opslag account schijf. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Hiermee wordt de bewerkings status van de resource gelezen. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/disks/read | Retourneert de schijf van het opslag account. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/disks/write | Hiermee voegt u een opslag account schijf toe. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/fileServices/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/fileServices/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/fileServices/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/images/delete | Hiermee verwijdert u een bepaalde installatie kopie van het opslag account. Keur. Micro soft. ClassicStorage/Storage accounts/vmImages gebruiken |
> | Actie | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Retourneert de bewerkings status van de installatie kopie van het opslag account. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/images/read | Hiermee wordt de installatie kopie van het opslag account geretourneerd. Keur. Micro soft. ClassicStorage/Storage accounts/vmImages gebruiken |
> | Actie | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Hiermee wordt de bewerkings status van de resource gelezen. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Hiermee verwijdert u een gegeven installatie kopie van het besturings systeem van het opslag account. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/osImages/read | Retourneert de installatie kopie van het besturings systeem van het opslag account. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/osImages/write | Voegt een gegeven installatie kopie van het besturings systeem van het opslag account toe. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Bereidt de migratie van een opslag account voor. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/queueServices/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/queueServices/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/queueServices/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/read | Retour neer het opslag account met het opgegeven account. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Hiermee worden de bestaande toegangs sleutels voor het opslag account opnieuw gegenereerd. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | De diagnostische instellingen ophalen. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/Services/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Hiermee worden de metrische definities opgehaald. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Hiermee worden de metrische gegevens opgehaald. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/services/read | De beschik bare services ophalen. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/tableServices/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/tableServices/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Actie | Micro soft. ClassicStorage/Storage accounts/tableServices/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Hiermee wordt de migratie van een opslag account gevalideerd. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Hiermee verwijdert u een bepaalde installatie kopie van een virtuele machine. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Hiermee wordt een bepaalde bewerkings status van installatie kopie van virtuele machine opgehaald. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Hiermee wordt de installatie kopie van de virtuele machine geretourneerd. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Hiermee voegt u een bepaalde installatie kopie van een virtuele machine toe. |
> | Actie | Microsoft.ClassicStorage/storageAccounts/write | Hiermee voegt u een nieuw opslag account toe. |
> | Actie | Microsoft.ClassicStorage/vmImages/read | Geeft een lijst van installatie kopieën van virtuele machines. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | DataAction | Micro soft. CognitiveServices/accounts/AnomalyDetector/tijds Erie/complete/detecteren/actie | Met deze bewerking wordt een model gegenereerd met behulp van een volledige serie, waarbij elk punt wordt gedetecteerd met hetzelfde model.<br>Met deze methode worden punten voor en na een bepaald punt gebruikt om te bepalen of het een afwijkend is.<br>De volledige detectie kan de gebruiker een algemene status van de tijd reeks geven. |
> | DataAction | Micro soft. CognitiveServices/accounts/AnomalyDetector/tijds Erie/last/detect/Action | Met deze bewerking wordt een model gegenereerd met behulp van punten voor de laatste. Met deze methode worden alleen historische punten gebruikt om te bepalen of het doel punt een afwijkend is. Het meest recente punt detectie komt overeen met het scenario van real-time bewaking van Bedrijfs gegevens. |
> | DataAction | Micro soft. CognitiveServices/accounts/automatische suggesties/zoeken/actie | Met deze bewerking worden suggesties gegeven voor een bepaalde query of gedeeltelijke query. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | Met deze bewerking wordt een uitgebreide set visuele functies geëxtraheerd op basis van de inhoud van de installatie kopie.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | Met deze bewerking wordt een omsluitend kader geretourneerd rond het belangrijkste gedeelte van de afbeelding. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | Met deze bewerking wordt een beschrijving van een afbeelding in een door de mens lees bare taal gegenereerd met volledige zinnen.<br> De beschrijving is gebaseerd op een verzameling inhouds Tags, die ook door de bewerking worden geretourneerd.<br>Voor elke afbeelding kan meer dan één beschrijving worden gegenereerd.<br> Beschrijvingen worden geordend op basis van hun betrouwbaarheids Score.<br>Alle beschrijvingen zijn in het Engels. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | Met deze bewerking wordt object detectie uitgevoerd voor de opgegeven installatie kopie.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Met deze bewerking wordt een miniatuur afbeelding gegenereerd met de opgegeven breedte en hoogte van de gebruiker.<br> Standaard wordt met de service de afbeelding geanalyseerd, het interessegebied bepaald en op basis van het interessegebied coördinaten voor slim bijsnijden gegenereerd.<br> Slim bijsnijden helpt bij het opgeven van een hoogte-breedte verhouding die afwijkt van die van de invoer installatie kopie |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Met deze bewerking wordt inhoud in een installatie kopie herkend door een domein-specifiek model toe te passen.<br> De lijst met domein-specifieke modellen die worden ondersteund door de Computer Vision-API kan worden opgehaald met behulp van de/models GET-aanvraag.<br> Op dit moment biedt de API de volgende gebruikersspecifieke modellen: beroemdheden, bezienswaardigheden. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | Met deze bewerking wordt de lijst met domein-specifieke modellen geretourneerd die worden ondersteund door de Computer Vision-API.  De API ondersteunt momenteel de volgende gebruikersspecifieke modellen: beroemdheden Recognizer, oriëntatie punt Recognizer. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | Optische teken herkenning (OCR) detecteert tekst in een afbeelding en extraheert de herkende tekens in een door een machine bruikbare teken stroom.    |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/lezen/analyseren/actie | Gebruik deze interface om een lees bewerking uit te voeren, waarbij u gebruikmaakt van de geavanceerde optische teken herkenning-algoritmen (OCR) die zijn geoptimaliseerd voor tekst zware documenten.<br>Het kan handgeschreven, gedrukte of gecombineerde documenten verwerken.<br>Wanneer u de Lees interface gebruikt, bevat het antwoord een header met de naam Operation-location.<br>De header bewerkings locatie bevat de URL die u moet gebruiken voor de bewerking Lees resultaat ophalen voor toegang tot OCR-resultaten. |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/lezen/analyzeresults/lezen | Gebruik deze interface om de status en OCR-resultaat van een lees bewerking op te halen.  De URL die de ' operationId ' bevat, wordt geretourneerd in de reactie header van de Lees bewerking ' Operation-location '. |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/lezen/core/asyncbatchanalyze/actie | Gebruik deze interface om het resultaat van een bewerking voor het lezen van een batch-bestand op te halen, waarbij het geavanceerde optische teken wordt gebruikt |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/lezen/bewerkingen/lezen | Deze interface wordt gebruikt voor het ophalen van OCR-resultaten van een lees bewerking. De URL naar deze interface moet worden opgehaald uit het veld <b>bewerkings locatie</b> dat is geretourneerd door de batch read file-interface. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Gebruik deze interface om het resultaat van een Tekst herkennen bewerking op te halen. Wanneer u de Tekst herkennen-interface gebruikt, bevat het antwoord een veld met de naam bewerking locatie. Het veld bewerkings locatie bevat de URL die u moet gebruiken voor de bewerking resultaat van het ophalen van Tekst herkennen. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | Met deze bewerking wordt een lijst met woorden, of tags, gegenereerd die relevant zijn voor de inhoud van de geleverde afbeelding.<br>De Computer Vision-API kan labels retour neren op basis van objecten, in de praktijk, in achtergronden of in afbeeldingen gevonden.<br>In tegens telling tot categorieën worden tags niet ingedeeld op basis van een hiërarchisch classificatie systeem, maar komen ze overeen met de inhoud van de afbeelding.<br>Tags kunnen hints bevatten om Verwar ring te voor komen of om context te bieden, bijvoorbeeld de tag "cello", kan worden gecombineerd met de hint "musical instrument".<br>Alle tags zijn in het Engels. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | Deze interface wordt gebruikt om het resultaat van de tekst bewerking te herkennen. De URL naar deze interface moet worden opgehaald uit het veld <b>bewerkings locatie</b> dat is geretourneerd door de tekst herkennen-interface. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/image lists/actie | Lijst met installatie kopieën maken. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/image lists/verwijderen | Lijst met installatie kopieën-verwijderen |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/image lists/installatie kopieën/verwijderen | Verwijder een afbeelding uit de lijst met installatie kopieën. De lijst met installatie kopieën kan worden gebruikt om treffers te vinden die overeenkomen met andere installatie kopieën wanneer u de API afbeelding/overeenkomst gebruikt. Verwijder alle installatie kopieën uit de lijst. De lijst met installatie kopieën kan worden gebruikt om treffers te vinden die overeenkomen met andere installatie kopieën wanneer u de API afbeelding/match gebruikt. * |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/image-installatie kopieën/afbeeldingen/lezen | Afbeelding: alle afbeeldings-Id's ophalen |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/image-installatie kopieën/afbeeldingen/schrijven | Een afbeelding toevoegen aan de lijst met installatie kopieën. De lijst met installatie kopieën kan worden gebruikt om treffers te vinden die overeenkomen met andere installatie kopieën wanneer u de API afbeelding/overeenkomst gebruikt. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/image lists/lezen | Lijst met installatie kopieën-Details ophalen-lijst met installatie kopieën-alles ophalen |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/image lists/refreshindex/Action | Lijst met installatie kopieën-zoek index vernieuwen |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/image lists/write | Lijst met installatie kopieën-Details van de update |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/processimage/evaluate/actie | Retourneert waarschijnlijkheid van de installatie kopie met ongepaste of inhoud voor volwassenen. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/processimage/findfaces/actie | Vind gezichten in afbeeldingen. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/processimage/match/Action | Fuzzily komt overeen met een afbeelding voor een van uw aangepaste afbeeldings lijsten.<br>Met deze API kunt u uw aangepaste installatie kopie lijsten maken en beheren.<br> |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/processimage/OCR/Action | Retourneert tekst die in de afbeelding is gevonden voor de opgegeven taal. Als er geen taal in de invoer is opgegeven, wordt de detectie standaard ingesteld op Engels. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/processtext/DetectLanguage/actie | Met deze bewerking wordt de taal van de gegeven invoer inhoud gedetecteerd.<br>Retourneert de ISO 639-3-code voor de meest voorkomende taal die de verzonden tekst omvat.<br>Meer dan 110 talen worden ondersteund. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/processtext/scherm/actie | De bewerking detecteert grof taalgebruik in meer dan 100 talen en vergelijkt woorden op basis van aangepaste en gedeelde zwarte lijsten. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/taken/actie | Er wordt een taak-id geretourneerd voor de installatie kopie-inhoud die op dit eind punt is geplaatst.  |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/Jobs/lezen | De taak Details voor een taak-id ophalen. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/Recensies/accessKey/lezen | De toegangs sleutel voor inhoud controleren voor uw team ophalen. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/beoordelingen/actie | De gemaakte beoordelingen worden weer gegeven voor revisoren van uw team. Als revisoren de beoordeling volt ooien, worden de resultaten van de controle gepost (d.w.z. HTTP POST) op de opgegeven CallBackEndpoint. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/beoordelingen/frames/lezen | *NotDefined* |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/beoordelingen/frames/schrijven | Gebruik deze methode om frames toe te voegen voor een video beoordeling. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/Beoordelingen/publiceren/actie | Video beoordelingen worden in eerste instantie gemaakt in een niet-gepubliceerde status, wat betekent dat deze niet beschikbaar is voor revisoren die uw team nog moet beoordelen. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/beoordelingen/lezen | Retourneert beoordelings gegevens voor de door gegeven beoordelings-id. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/beoordelingen/transcriptie/actie | Deze API voegt een transcript bestand (tekst versie van alle woorden die in een video worden gesp roken) toe aan een video beoordeling. Het bestand moet een geldige WebVTT-indeling zijn. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/beoordelingen/transcriptmoderationresult/actie | Deze API voegt een bestand met tekst resultaten voor een transcriptie toe voor een video beoordeling. Het resultaat bestand voor tekst van het transcript scherm is het resultaat van een scherm tekst-API. Als u een bestand met tekst resultaten wilt genereren, moet er een transcript bestand worden weer gegeven met de tekst-API op het scherm. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/instellingen/sjablonen/verwijderen | Een sjabloon in uw team verwijderen |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/instellingen/sjablonen/lezen | Retourneert een matrix met de controle sjablonen die zijn ingericht voor dit team. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/instellingen/sjablonen/schrijven | Hiermee wordt de opgegeven sjabloon gemaakt of bijgewerkt |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/werk stromen/lezen | De details van een specifieke werk stroom in uw team ophalen. Alle beschik bare werk stromen voor uw team ophalen * |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/teams/werk stromen/schrijven | Een nieuwe werk stroom maken of een bestaande workflow bijwerken. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/termlists/actie | Maak een lijst met termen. |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/termlists/Bulkupdate/actie | Termen lijsten-bulk update |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/termlists/verwijderen | Termen lijsten-verwijderen |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/termlists/lezen | Lijst met termen: alle lijsten ophalen-Details ophalen |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/termlists/refreshindex/actie | Termen lijsten-zoek index vernieuwen |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/termlists/voor waarden/verwijderen | Term-delete-term-alle voor waarden verwijderen |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/termlists/voor waarden/lezen | Term: alle voor waarden ophalen |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/termlists/voor waarden/schrijven | Term-term toevoegen |
> | DataAction | Micro soft. CognitiveServices/accounts/ContentModerator/termlists/schrijven | Termen lijsten-Details van de update |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/classificering/herhalingen/afbeelding/actie | Een afbeelding classificeren en het resultaat opslaan. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/classificatie/herhalingen/afbeelding/niets opslaan/actie | Een installatie kopie classificeren zonder het resultaat op te slaan. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/classificatie/iteraties/URL/actie | Hiermee wordt een afbeeldings-URL geclassificeerd en wordt het resultaat opgeslagen. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/classificatie/herhalingen/URL/niets opslaan/actie | Een afbeeldings-URL classificeren zonder het resultaat op te slaan. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/detectie/herhalingen/afbeelding/actie | Objecten in een installatie kopie detecteren en het resultaat opslaan. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/detectie/herhalingen/afbeelding/niets opslaan/actie | Objecten in een installatie kopie detecteren zonder het resultaat op te slaan. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/detectie/herhalingen/URL/actie | Objecten detecteren in een afbeeldings-URL en het resultaat opslaan. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/detectie/herhalingen/URL/geen opslaan/actie | Objecten in een afbeeldings-URL detecteren zonder het resultaat op te slaan. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/domeinen/lezen | Informatie over een specifiek domein ophalen. Een lijst met beschik bare domeinen ophalen. * |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/labelproposals/instelling/actie | Stel de groeps grootte van het label voorstel in. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/labelproposals/instellen/lezen | De groeps grootte van het label voorstel voor dit project ophalen. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/project/migratie/actie | *NotDefined* |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/actie | Een project maken. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/verwijderen | Een specifiek project verwijderen. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/actie | Deze API accepteert hoofdtekst inhoud als meerdelige/form-data en application/octet-stream. Bij gebruik van meerdelige |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/verwijderen | Verwijder installatie kopieën uit de set met trainings afbeeldingen. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/bestanden/actie | Deze API accepteert een batch bestanden en optioneel Tags om installatie kopieën te maken. Er is een limiet van 64 afbeeldingen en 20 tags. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/id/lezen | Deze API retourneert een set installatie kopieën voor de opgegeven labels en optioneel iteratie. Als er geen herhaling is opgegeven, wordt de |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/voor spellingen/actie | Deze API maakt een batch met installatie kopieën van voor spelling opgegeven installatie kopieën. Er is een limiet van 64 afbeeldingen en 20 tags. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/regionproposals/actie | Met deze API worden regio voorstellen voor een installatie kopie weer geven samen met vertrouwens gebieden voor de regio. Als er geen Voorst Ellen worden gevonden, wordt er een lege matrix geretourneerd. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/regio's/actie | Deze API accepteert een batch met afbeeldings regio's en optioneel Tags om bestaande installatie kopieën met regio gegevens bij te werken. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/regio's/verwijderen | Een set afbeeldings regio's verwijderen. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/aanbevolen/actie | Met deze API worden niet-gelabelde installatie kopieën opgehaald die worden gefilterd op voorgestelde Tags-Id's. Er wordt een lege matrix geretourneerd als er geen installatie kopieën worden gevonden. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/aanbevolen/aantal/actie | Deze API gaat in tagIds om het aantal niet-gelabelde afbeeldingen per voorgestelde Tags voor een bepaalde drempel waarde op te halen. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/gelabeld/aantal/lezen | Het filteren bevindt zich op een en/of een relatie. Bijvoorbeeld, als de gegeven label-id's voor de ' hond ' zijn en |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/gelabeld/lezen | Deze API ondersteunt de selectie van batches en het bereik. Standaard worden alleen eerste 50 installatie kopieën geretourneerd die overeenkomen met installatie kopieën. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/Tags/actie | Een set installatie kopieën koppelen aan een set met tags. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/Tags/verwijderen | Een set tags verwijderen uit een set met installatie kopieën. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/ongecodeerd/aantal/lezen | Deze API retourneert de afbeeldingen die geen labels voor een bepaald project en eventueel een herhaling hebben. Als er geen herhaling is opgegeven, wordt de |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/niet-gecodeerd/lezen | Deze API ondersteunt de selectie van batches en het bereik. Standaard worden alleen eerste 50 installatie kopieën geretourneerd die overeenkomen met installatie kopieën. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/afbeeldingen/url's/actie | Deze API accepteert een batch-URL en optioneel Tags om installatie kopieën te maken. Er is een limiet van 64 afbeeldingen en 20 tags. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/herhalingen/verwijderen | Een specifieke herhaling van een project verwijderen. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/herhalingen/exporteren/actie | Een getrainde iteratie exporteren. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/herhalingen/exporteren/lezen | De lijst met exports voor een specifieke herhaling ophalen. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/herhalingen/prestaties/installatie kopieën/aantal/lezen | Het filteren bevindt zich op een en/of een relatie. Bijvoorbeeld, als de gegeven label-id's voor de ' hond ' zijn en |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/herhalingen/prestaties/installatie kopieën/lezen | Deze API ondersteunt de selectie van batches en het bereik. Standaard worden alleen eerste 50 installatie kopieën geretourneerd die overeenkomen met installatie kopieën. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/herhalingen/prestaties/lezen | Gedetailleerde informatie over de prestaties van een herhaling ophalen. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/herhalingen/publiceren/actie | Een specifieke herhaling publiceren. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/herhalingen/publiceren/verwijderen | Publicatie van een specifieke herhaling ongedaan maken. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/herhalingen/lezen | Een specifieke herhaling ophalen. Vind iteraties voor het project. * |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/herhalingen/schrijven | Een specifieke herhaling bijwerken. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/voor spellingen/verwijderen | Verwijder een aantal voorspelde afbeeldingen en de bijbehorende Voorspellings resultaten. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/project/voor spellingen/query/actie | Down load installatie kopieën die naar uw Voorspellings eindpunt zijn verzonden. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. prediction/projects/QuickTest/image/Action | Een afbeelding snel testen. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/QuickTest/URL/actie | Een afbeeldings-URL snel testen. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/lezen | Een specifiek project ophalen. Uw projecten ophalen. * |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/Tags/actie | Maak een tag voor het project. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/Tags/verwijderen | Een tag uit het project verwijderen. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/tags/lezen | Informatie over een specifieke tag ophalen. De labels voor een bepaald project en herhaling ophalen. * |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/Tags/schrijven | Een tag bijwerken. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/tagsandregions/suggesties/actie | Met deze API worden voorgestelde Tags en regio's voor een matrix/batch met niet-gecodeerde installatie kopieën en vertrouwens elementen voor de Tags opgehaald. Er wordt een lege matrix geretourneerd als er geen tags worden gevonden. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/training/actie | Wacht rijen project voor training. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/projecten/schrijven | Een specifiek project bijwerken. |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/quotum/actie | *NotDefined* |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/quotum/verwijderen | *NotDefined* |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/quotum/vernieuwen/schrijven | *NotDefined* |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/gebruik/voor spelling/gebruiker/lezen | Gebruik ophalen voor Voorspellings resource voor Oxford-gebruiker |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/gebruik/training/resource/laag/lezen | Gebruik voor trainings resource voor Azure-gebruiker ophalen |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/gebruik/training/gebruiker/lezen | Gebruik ophalen voor trainings resource voor Oxford-gebruiker |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/gebruiker/actie | *NotDefined* |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/gebruiker/verwijderen | *NotDefined* |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/gebruiker/status/schrijven | Gebruikers status bijwerken |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/gebruiker/laag/schrijven | *NotDefined* |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/gebruikers/lezen | *NotDefined* |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/White List/verwijderen | Hiermee wordt een white list-gebruiker met specifieke functionaliteit verwijderd |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/white list/lezen | Hiermee wordt een lijst met white list-gebruikers met specifieke functionaliteit opgehaald |
> | DataAction | Micro soft. CognitiveServices/accounts/CustomVision. voor spelling/white list/schrijven | Hiermee wordt een gebruiker in de white list bijgewerkt of gemaakt met een specifieke mogelijkheid |
> | Actie | Microsoft.CognitiveServices/accounts/delete | Hiermee worden de API-accounts verwijderd |
> | DataAction | Micro soft. CognitiveServices/accounts/EntitySearch/zoeken/actie | Entiteiten ophalen en de resultaten voor een bepaalde query plaatsen. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/detect/action | Detecteren van menselijke gezichten in een afbeelding, gezichts rechthoeken retour neren en optioneel met faceIds, bezienswaardigheden en kenmerken. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Een opgegeven gezichts lijst verwijderen. De gerelateerde installatie kopieën in de lijst met het gezichts materiaal worden ook verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/facelists/persistedfaces/verwijderen | Een gezicht verwijderen uit een gezichts lijst door opgegeven faceListId en persisitedFaceId. De afbeelding voor het gerelateerde gezicht wordt ook verwijderd. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Voeg een gezicht toe aan een opgegeven gezichts lijst van Maxi maal 1.000 gezichten. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Haal de faceListId, naam, User Data en gezichten van een gezichts lijst op in de lijst met gezicht. Lijst met gezichts lijsten ' faceListId, name en User Data. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Maak een lege face-lijst met door de gebruiker opgegeven faceListId, een naam en een optioneel User Data. Maxi maal 64 gezichts lijsten zijn toegestane Update gegevens van een gezichts lijst, inclusief naam en User Data. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Aan de hand van het voor beeld van een query op faceId kunt u zoeken naar de vergelijk bare gezichten vanuit een faceId-matrix, een gezichts lijst of een grote gezichts lijst. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Verdeel kandidaten in groepen op basis van vergelijk bare gezicht. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | 1-op-veel-identificatie voor het vinden van de dichtstbijzijnde treffers van een persoons groep of een grote persoons groep. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/verwijderen | Een opgegeven grote gezichts lijst verwijderen. De gerelateerde gezichts afbeeldingen in de lijst grote gezicht worden ook verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/persistedfaces/verwijderen | Een gezicht verwijderen uit een grote gezichts lijst door opgegeven largeFaceListId en persisitedFaceId. De afbeelding voor het gerelateerde gezicht wordt ook verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/persistedfaces/lezen | Haal persistente gezicht op in grote gezichts lijst door largeFaceListId en persistedFaceId. Een lijst met gezichten ' persistedFaceId en User Data ' in een opgegeven lijst met grote vlakken. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/persistedfaces/schrijven | Voeg een gezicht toe aan een opgegeven lijst met grote vlakken, tot 1.000.000 gezichten. Het veld User Data van een opgegeven face bijwerken in een lijst met grote vlakken door de persistedFaceId ervan. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/lezen | De largeFaceListId, naam en User Data van een grote lijst ophalen. Lijst met grote gezichts lijsten: informatie over largeFaceListId, name en User Data. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/Train/actie | Een trainings taak voor een grote gezichts lijst verzenden. Training is een cruciale stap die alleen kan worden gebruikt door een getrainde grote gezichts lijst. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/training/lezen | Om te controleren of de trainings status van de grote face List is voltooid of nog steeds actief is. LargeFaceList training is een asynchrone bewerking |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/schrijven | Maak een lege lijst met grote gezichts gebruikers met een door de gebruiker opgegeven largeFaceListId, een naam en een optioneel User Data. Update gegevens van een grote gezichts lijst, met inbegrip van naam en User Data. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Een bestaande groep grote personen met de opgegeven personGroupId verwijderen. Permanente gegevens in deze grote groep worden verwijderd. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Een nieuwe persoon maken in een opgegeven grote persoons groep. Als u een gezicht wilt toevoegen aan deze persoon, neemt u contact op met |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Een bestaande persoon verwijderen uit een grote groep personen. Alle opgeslagen persoons gegevens en gezichts afbeeldingen in de persoons vermelding worden verwijderd. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Een gezicht verwijderen van een persoon in een grote groep personen. Gezichts gegevens en afbeelding die betrekking hebben op deze gezichts vermelding worden ook verwijderd. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Informatie over persoons gezicht ophalen. De permanente persoons zijde wordt opgegeven door de largePersonGroupId, personId en persistedFaceId. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/personen/persistedfaces/schrijven | Voeg een gezichts afbeelding aan een persoon toe aan een grote persoons groep voor gezichts identificatie of-verificatie. Om te omgaan met de afbeelding van het bijwerken van een persoon die het veld User Data van het gezicht heeft behouden. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Haal de naam en het User Data van een persoon op en de persistente faceIds die de afbeelding van het Inge schreven persoons gezicht vertegenwoordigt. De gegevens van alle personen in de opgegeven groep grote personen weer geven, waaronder personId, name, User Data and persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Naam of User Data van een persoon bijwerken. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | De gegevens van een grote persoons groep ophalen, inclusief de naam en het User Data. Deze API retourneert een lijst met gegevens van een grote persoon met groepen alle bestaande largePesonGroupId, naam en User Data van de groep. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Een trainings taak voor een grote persoons groep verzenden. Training is een belang rijke stap dat alleen een getrainde grote persoons groep kan gebruiken. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | De trainings status van een grote persoons groep controleren is voltooid of nog steeds actief. LargePersonGroup training is een asynchrone bewerking |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Maak een nieuwe groep grote personen met een door de gebruiker opgegeven largePersonGroupId, een naam en een optionele User Data. De naam en het User Data van een bestaande groep van grote personen bijwerken. De eigenschappen blijven ongewijzigd als ze niet in de hoofd tekst van de aanvraag staan. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/persongroups/verwijderen | Een bestaande persoons groep met opgegeven personGroupId verwijderen. De persistente gegevens in deze persoons groep worden verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/persongroups/personen/actie | Een nieuwe persoon maken in een opgegeven persoons groep. Als u een gezicht wilt toevoegen aan deze persoon, neemt u contact op met |
> | DataAction | Micro soft. CognitiveServices/accounts/face/persongroups/personen/verwijderen | Een bestaande persoon uit een persoons groep verwijderen. Alle opgeslagen persoons gegevens en gezichts afbeeldingen in de persoons vermelding worden verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/persongroups/personen/persistedfaces/verwijderen | Een gezicht verwijderen van een persoon in een persoons groep. Gezichts gegevens en afbeelding die betrekking hebben op deze gezichts vermelding worden ook verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/persongroups/personen/persistedfaces/lezen | Informatie over persoons gezicht ophalen. De permanente persoons zijde wordt opgegeven door de personGroupId, personId en persistedFaceId. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/persongroups/personen/persistedfaces/schrijven | Voeg een gezichts afbeelding aan een persoon toe aan een persoons groep voor gezichts identificatie of-verificatie. Om te omgaan met de afbeelding van meerdere updates van een persoon die het veld User Data van het gezicht heeft behouden. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/persongroups/personen/lezen | Haal de naam en het User Data van een persoon op en de persistente faceIds die de afbeelding van het Inge schreven persoons gezicht vertegenwoordigt. De gegevens van alle personen in de opgegeven persoons groep weer geven, met inbegrip van personId, name, User Data en persistedFaceIds van Registered. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/persongroups/personen/schrijven | Naam of User Data van een persoon bijwerken. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/persongroups/lezen | Naam van persoons groep ophalen en User Data. Als u persoons gegevens wilt ophalen onder deze personGroup, gebruikt u de pesonGroupId, naam en User Data van de lijst met personen. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/persongroups/Train/actie | Een trainings taak voor een persoons groep verzenden. Training is een cruciale stap die alleen kan worden gebruikt door een getrainde persoons groep. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/persongroups/training/lezen | Om te controleren of de trainings status van de persoons groep is voltooid of nog steeds actief is. PersonGroup-training is een asynchrone bewerking die wordt geactiveerd |
> | DataAction | Micro soft. CognitiveServices/accounts/face/persongroups/schrijven | Maak een nieuwe persoons groep met opgegeven personGroupId, naam en door de gebruiker opgegeven Data User. De naam en het User Data van een bestaande persoons groep bijwerken. De eigenschappen blijven ongewijzigd als ze niet in de hoofd tekst van de aanvraag staan. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | Controleer of twee gezichten deel uitmaken van dezelfde persoon of of het ene gezicht deel uitmaakt van een persoon. |
> | DataAction | Micro soft. CognitiveServices/accounts/FormRecognizer/aangepast/modellen/analyseren/actie | Sleutel-waardeparen extra heren uit een bepaald document. Het invoer document moet een van de ondersteunde inhouds typen-' Application/PDF ', ' Image/JPEG ' of ' image/png ' zijn. Er wordt een reactie van succes geretourneerd in JSON. |
> | DataAction | Micro soft. CognitiveServices/accounts/FormRecognizer/aangepast/modellen/verwijderen | Model artefacten verwijderen. |
> | DataAction | Micro soft. CognitiveServices/accounts/FormRecognizer/Custom/modellen/sleutels/lezen | De sleutels voor het model ophalen. |
> | DataAction | Micro soft. CognitiveServices/accounts/FormRecognizer/aangepast/modellen/lezen | Informatie over een model ophalen. Informatie over alle getrainde aangepaste modellen weer geven * |
> | DataAction | Micro soft. CognitiveServices/accounts/FormRecognizer/Custom/Train/actie | Een aangepast model maken en trainen.<br>De aanvraag voor de trein moet een bron parameter bevatten die een extern toegankelijke Azure Storage BLOB-container URI (bij voor keur een Shared Access Signature URI) of een geldig pad naar een gegevensmap in een lokaal gekoppeld station is.<br>Wanneer lokale paden zijn opgegeven, moeten ze de indeling voor Linux/Unix-paden volgen en een absoluut pad zijn dat is geroot naar de configuratie van de invoer koppeling |
> | DataAction | Micro soft. CognitiveServices/accounts/FormRecognizer/prebuild/Receipt/asyncbatchanalyze/Action | Veld tekst en semantische waarden uit een bepaald ontvangst document extra heren. Het document met de invoer afbeelding moet een van de ondersteunde inhouds typen zijn: JPEG, PNG, BMP, PDF of TIFF. Een succes respons is een JSON met een veld met de naam Operation-location, dat de URL bevat voor de bewerking ontvangst resultaat ophalen om de resultaten asynchroon op te halen. |
> | DataAction | Micro soft. CognitiveServices/accounts/FormRecognizer/prebuild/Receipt/Operations/Action | Query's uitvoeren op de status en het resultaat ophalen van een ontvangst bewerking analyseren. De URL naar deze interface kan worden verkregen van de header ' Operation-location ' in het antwoord van de ontvangst bevestiging. |
> | DataAction | Micro soft. CognitiveServices/accounts/ImageSearch/Details/actie | Retourneert inzichten over een afbeelding, zoals webpagina's die de afbeelding bevatten. |
> | DataAction | Micro soft. CognitiveServices/accounts/ImageSearch/zoeken/actie | Relevante installatie kopieën voor een bepaalde query ophalen. |
> | DataAction | Micro soft. CognitiveServices/accounts/ImageSearch/trending/actie | Huidige trend afbeeldingen ophalen. |
> | DataAction | Micro soft. CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/actie | Hiermee maakt u een insluitende lezer-sessie |
> | DataAction | Micro soft. CognitiveServices/accounts/InkRecognizer/recognize/Action | Op basis van een set lijn gegevens wordt de inhoud geanalyseerd en wordt een lijst gegenereerd met herkende entiteiten, inclusief de herkende tekst. |
> | Actie | Microsoft.CognitiveServices/accounts/listKeys/action | Lijst met sleutels |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Hiermee wordt de gepubliceerde eindpunt voorspelling opgehaald voor de opgegeven query. |
> | DataAction | Micro soft. CognitiveServices/accounts/NewsSearch/categorysearch/actie | Hiermee wordt nieuws voor een gegeven categorie geretourneerd. |
> | DataAction | Micro soft. CognitiveServices/accounts/NewsSearch/zoeken/actie | Nieuws artikelen ophalen die relevant zijn voor een bepaalde query. |
> | DataAction | Micro soft. CognitiveServices/accounts/NewsSearch/trendingtopics/actie | Bekijk onderwerpen over trends die door Bing worden geïdentificeerd. Dit zijn dezelfde onderwerpen die worden weer gegeven in de banner aan de onderkant van de Bing-start pagina. |
> | Actie | Microsoft.CognitiveServices/accounts/read | Hiermee worden API-accounts gelezen. |
> | Actie | Microsoft.CognitiveServices/accounts/regenerateKey/action | Sleutel opnieuw genereren |
> | Actie | Microsoft.CognitiveServices/accounts/skus/read | Beschik bare Sku's voor een bestaande resource lezen. |
> | DataAction | Micro soft. CognitiveServices/accounts/spelling/spelling/actie | Resultaat van een spelling controle query ophalen via GET of POST. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | De API retourneert een lijst met bekende entiteiten en algemene benoemde entiteiten (\"persoon\", \"locatie\", \"organisatie\" enzovoort) in een bepaald document. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | De API retourneert een lijst met tekenreeksen die de belangrijkste gespreksonderwerpen in de invoertekst aanduiden. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | De API retourneert de gedetecteerde taal en een numerieke score tussen 0 en 1. Scores dichtbij 1 geven aan dat het 100% zeker is dat de geïdentificeerde taal waar is. Er worden in totaal 120 talen ondersteund. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | De API retourneert een numerieke score tussen 0 en 1.<br>Scores dicht bij 1 wijzen op een positief gevoel, terwijl scores dicht bij 0 een negatief gevoel aangeven.<br>Een Score van 0,5 geeft het gebrek aan sentiment aan (bijvoorbeeld<br>een factoid-instructie). |
> | Actie | Microsoft.CognitiveServices/accounts/usages/read | Het quotum gebruik voor een bestaande resource ophalen. |
> | DataAction | Micro soft. CognitiveServices/accounts/VideoSearch/Details/actie | Krijg inzichten over een video, zoals verwante Video's. |
> | DataAction | Micro soft. CognitiveServices/accounts/VideoSearch/zoeken/actie | Down load Video's die relevant zijn voor een bepaalde query. |
> | DataAction | Micro soft. CognitiveServices/accounts/VideoSearch/trending/actie | Down load momenteel Video's over trend. |
> | DataAction | Micro soft. CognitiveServices/accounts/VisualSearch/zoeken/actie | Retourneert een lijst met labels die relevant zijn voor de meegeleverde afbeelding |
> | DataAction | Micro soft. CognitiveServices/accounts/WebSearch/Search/Action | Down load web-, afbeeldings-, nieuws-en & Video's resultaten voor een bepaalde query. |
> | Actie | Microsoft.CognitiveServices/accounts/write | Schrijft API-accounts. |
> | Actie | Microsoft.CognitiveServices/checkDomainAvailability/action | Beschik bare Sku's voor een abonnement lezen. |
> | Actie | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Beschik bare Sku's voor een abonnement lezen. |
> | Actie | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Beschik bare Sku's voor een abonnement lezen. |
> | Actie | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Melding van micro soft. Network van het verwijderen van VirtualNetworks of subnetten. |
> | Actie | Micro soft. CognitiveServices/locaties/operationresults/lezen | Lees de status van een asynchrone bewerking. |
> | Actie | Microsoft.CognitiveServices/Operations/read | Alle beschik bare bewerkingen weer geven |
> | Actie | Microsoft.CognitiveServices/register/action | Registreert het abonnement voor Cognitive Services |
> | Actie | Microsoft.CognitiveServices/register/action | Registreert het abonnement voor Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. commerce/RateCard/lezen | Retour neren gegevens van aanbieding, meta gegevens van resource/meter en tarieven voor het gegeven abonnement. |
> | Actie | Micro soft. commerce/registreren/actie | Abonnement voor micro soft commerce UsageAggregate registreren |
> | Actie | Micro soft. commerce/registratie/actie | Registratie van abonnement voor micro soft commerce UsageAggregate opheffen |
> | Actie | Microsoft.Commerce/UsageAggregates/read | Hiermee wordt het verbruik van Microsoft Azure door een abonnement opgehaald. Het resultaat bevat statistische gegevens over het gebruik van gegevens, abonnementen en resources die zijn gerelateerd aan een bepaald tijds bereik. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Compute/availabilitySets/delete | Hiermee wordt de beschikbaarheidsset verwijderd |
> | Actie | Microsoft.Compute/availabilitySets/read | De eigenschappen van een beschikbaarheidsset ophalen |
> | Actie | Microsoft.Compute/availabilitySets/vmSizes/read | Beschik bare grootten voor het maken of bijwerken van een virtuele machine in de beschikbaarheidsset weer geven |
> | Actie | Microsoft.Compute/availabilitySets/write | Hiermee wordt een nieuwe beschikbaarheidsset gemaakt of een bestaande bijgewerkt |
> | Actie | Micro soft. Compute/diskEncryptionSets/verwijderen | Een schijf versleutelings verwijderen |
> | Actie | Micro soft. Compute/diskEncryptionSets/lezen | De eigenschappen van een set schijf versleuteling ophalen |
> | Actie | Micro soft. Compute/diskEncryptionSets/schrijven | Een nieuwe schijf versleutelings maken of een bestaande set bijwerken |
> | Actie | Microsoft.Compute/disks/beginGetAccess/action | De SAS-URI van de schijf ophalen voor BLOB-toegang |
> | Actie | Microsoft.Compute/disks/delete | Hiermee wordt de schijf verwijderd |
> | Actie | Microsoft.Compute/disks/endGetAccess/action | De SAS-URI van de schijf intrekken |
> | Actie | Microsoft.Compute/disks/read | De eigenschappen van een schijf ophalen |
> | Actie | Microsoft.Compute/disks/write | Hiermee wordt een nieuwe schijf gemaakt of een bestaande bijgewerkt |
> | Actie | Micro soft. Compute/galerieën/toepassingen/verwijderen | Hiermee wordt de galerie toepassing verwijderd |
> | Actie | Micro soft. Compute/galerieën/toepassingen/lezen | Hiermee worden de eigenschappen van de galerie toepassing opgehaald |
> | Actie | Micro soft. Compute/galerieën/toepassingen/versies/verwijderen | Hiermee wordt de versie van de galerie toepassing verwijderd |
> | Actie | Micro soft. Compute/galerieën/toepassingen/versies/lezen | Hiermee worden de eigenschappen van de versie van de galerie toepassing opgehaald |
> | Actie | Micro soft. Compute/galerieën/toepassingen/versies/schrijven | Hiermee wordt een nieuwe versie van de galerie toepassing gemaakt of een bestaande bijgewerkt |
> | Actie | Micro soft. Compute/galerieën/toepassingen/schrijven | Hiermee wordt een nieuwe galerie toepassing gemaakt of een bestaande bijgewerkt |
> | Actie | Microsoft.Compute/galleries/delete | Hiermee wordt de galerie verwijderd |
> | Actie | Microsoft.Compute/galleries/images/delete | De galerie afbeelding verwijderen |
> | Actie | Microsoft.Compute/galleries/images/read | Hiermee worden de eigenschappen van de galerie afbeelding opgehaald |
> | Actie | Microsoft.Compute/galleries/images/versions/delete | Hiermee wordt de versie van de galerie afbeelding verwijderd |
> | Actie | Microsoft.Compute/galleries/images/versions/read | Hiermee worden de eigenschappen van de versie van de galerie afbeelding opgehaald |
> | Actie | Microsoft.Compute/galleries/images/versions/write | Hiermee wordt een nieuwe versie van de galerie afbeelding gemaakt of een bestaande bijgewerkt |
> | Actie | Microsoft.Compute/galleries/images/write | Hiermee wordt een nieuwe galerie afbeelding gemaakt of een bestaande bijgewerkt |
> | Actie | Microsoft.Compute/galleries/read | Hiermee worden de eigenschappen van de galerie opgehaald |
> | Actie | Microsoft.Compute/galleries/write | Hiermee maakt u een nieuwe galerie of werkt u een bestaande. |
> | Actie | Microsoft.Compute/hostGroups/delete | Hiermee wordt de hostgroep verwijderd |
> | Actie | Micro soft. Compute/hostGroups/hosts/Delete | Hiermee wordt de host verwijderd |
> | Actie | Micro soft. Compute/hostGroups/hosts/lezen | De eigenschappen van een host ophalen |
> | Actie | Micro soft. Compute/hostGroups/hosts/write | Hiermee wordt een nieuwe host gemaakt of een bestaande host bijgewerkt |
> | Actie | Micro soft. Compute/hostGroups/lezen | De eigenschappen van een hostgroep ophalen |
> | Actie | Micro soft. Compute/hostGroups/schrijven | Hiermee wordt een nieuwe hostgroep gemaakt of een bestaande hostgroep bijgewerkt |
> | Actie | Microsoft.Compute/images/delete | Hiermee verwijdert u de installatie kopie |
> | Actie | Microsoft.Compute/images/read | De eigenschappen van de installatie kopie ophalen |
> | Actie | Microsoft.Compute/images/write | Hiermee wordt een nieuwe installatie kopie gemaakt of een bestaande bijgewerkt |
> | Actie | Microsoft.Compute/locations/capsOperations/read | Hiermee wordt de status van een asynchrone Cap's-bewerking opgehaald |
> | Actie | Microsoft.Compute/locations/diskOperations/read | Hiermee wordt de status van een asynchrone schijf bewerking opgehaald |
> | Actie | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Logboeken maken voor het weer geven van totaal aantal aanvragen per tijds interval voor hulp bij het beperken van diagnoses. |
> | Actie | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Maak Logboeken om aggregaties van vertraagde aanvragen weer te geven, gegroepeerd op ResourceName, Operationname of het toegepaste beperkings beleid. |
> | Actie | Microsoft.Compute/locations/operations/read | Hiermee wordt de status van een asynchrone bewerking opgehaald |
> | Actie | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | De eigenschappen van een platform installatie kopie aanbieding ophalen |
> | Actie | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | De eigenschappen van een platform installatie kopie-SKU ophalen |
> | Actie | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | De eigenschappen van een platform installatie kopie versie ophalen |
> | Actie | Microsoft.Compute/locations/publishers/artifacttypes/types/read | De eigenschappen van een VMExtension-type ophalen |
> | Actie | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | De eigenschappen van een VMExtension-versie ophalen |
> | Actie | Microsoft.Compute/locations/publishers/read | De eigenschappen van een uitgever ophalen |
> | Actie | Microsoft.Compute/locations/runCommands/read | Een lijst met beschik bare uitvoerings opdrachten op locatie |
> | Actie | Microsoft.Compute/locations/usages/read | Hiermee worden service limieten en huidige gebruiks hoeveelheden opgehaald voor de reken resources van het abonnement op een locatie |
> | Actie | Microsoft.Compute/locations/vmSizes/read | Een lijst met beschik bare grootten van virtuele machines op een locatie |
> | Actie | Micro soft. Compute/locaties/vsmOperations/lezen | Hiermee wordt de status van een asynchrone bewerking voor virtuele-machine schaal sets met de extensie van de virtual machine runtime-service opgehaald |
> | Actie | Microsoft.Compute/operations/read | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn op micro soft. Compute resource provider |
> | Actie | Microsoft.Compute/proximityPlacementGroups/delete | Hiermee wordt de plaatsings groep voor nabijheid verwijderd |
> | Actie | Microsoft.Compute/proximityPlacementGroups/read | De eigenschappen van een proximity-plaatsings groep ophalen |
> | Actie | Microsoft.Compute/proximityPlacementGroups/write | Hiermee wordt een nieuwe plaatsings groep voor nabijheid gemaakt of een bestaande bijgewerkt |
> | Actie | Microsoft.Compute/register/action | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. compute |
> | Actie | Microsoft.Compute/restorePointCollections/delete | Hiermee verwijdert u de verzameling van herstel punten en de opgenomen herstel punten |
> | Actie | Microsoft.Compute/restorePointCollections/read | De eigenschappen van een herstel punt verzameling ophalen |
> | Actie | Microsoft.Compute/restorePointCollections/restorePoints/delete | Hiermee wordt het herstel punt verwijderd |
> | Actie | Microsoft.Compute/restorePointCollections/restorePoints/read | De eigenschappen van een herstel punt ophalen |
> | Actie | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | De eigenschappen van een herstel punt samen met Blob SAS-Uri's ophalen |
> | Actie | Microsoft.Compute/restorePointCollections/restorePoints/write | Hiermee maakt u een nieuw herstel punt |
> | Actie | Microsoft.Compute/restorePointCollections/write | Hiermee maakt u een nieuwe herstel punt verzameling of werkt u een bestaande. |
> | Actie | Microsoft.Compute/sharedVMImages/delete | Hiermee worden de SharedVMImage verwijderd |
> | Actie | Microsoft.Compute/sharedVMImages/read | De eigenschappen van een SharedVMImage ophalen |
> | Actie | Microsoft.Compute/sharedVMImages/versions/delete | Een SharedVMImageVersion verwijderen |
> | Actie | Microsoft.Compute/sharedVMImages/versions/read | De eigenschappen van een SharedVMImageVersion ophalen |
> | Actie | Microsoft.Compute/sharedVMImages/versions/replicate/action | Een SharedVMImageVersion repliceren naar doel regio's |
> | Actie | Microsoft.Compute/sharedVMImages/versions/write | Een nieuwe SharedVMImageVersion maken of een bestaande bijwerken |
> | Actie | Microsoft.Compute/sharedVMImages/write | Hiermee wordt een nieuwe SharedVMImage gemaakt of een bestaande bijgewerkt |
> | Actie | Microsoft.Compute/skus/read | Hiermee wordt de lijst met micro soft. Compute-Sku's die beschikbaar zijn voor uw abonnement opgehaald |
> | Actie | Microsoft.Compute/snapshots/beginGetAccess/action | De SAS-URI van de moment opname voor BLOB-toegang ophalen |
> | Actie | Microsoft.Compute/snapshots/delete | Een moment opname verwijderen |
> | Actie | Microsoft.Compute/snapshots/endGetAccess/action | De SAS-URI van de moment opname intrekken |
> | Actie | Microsoft.Compute/snapshots/read | De eigenschappen van een moment opname ophalen |
> | Actie | Microsoft.Compute/snapshots/write | Een nieuwe moment opname maken of een bestaand abonnement bijwerken |
> | Actie | Microsoft.Compute/unregister/action | Registratie van een abonnement bij micro soft. Compute-resource provider |
> | Actie | Microsoft.Compute/virtualMachines/capture/action | Hiermee wordt de virtuele machine vastgelegd door virtuele harde schijven te kopiëren en wordt een sjabloon gegenereerd dat kan worden gebruikt voor het maken van vergelijk bare virtuele machines |
> | Actie | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Hiermee worden de op blobs gebaseerde schijven van de virtuele machine geconverteerd naar beheerde schijven |
> | Actie | Microsoft.Compute/virtualMachines/deallocate/action | Hiermee wordt de virtuele machine uitgeschakeld en worden de reken resources vrijgegeven |
> | Actie | Microsoft.Compute/virtualMachines/delete | Hiermee wordt de virtuele machine verwijderd |
> | Actie | Microsoft.Compute/virtualMachines/extensions/delete | Hiermee wordt de extensie van de virtuele machine verwijderd |
> | Actie | Microsoft.Compute/virtualMachines/extensions/read | De eigenschappen van een extensie van een virtuele machine ophalen |
> | Actie | Microsoft.Compute/virtualMachines/extensions/write | Hiermee wordt een nieuwe extensie van de virtuele machine gemaakt of een bestaande bijgewerkt |
> | Actie | Microsoft.Compute/virtualMachines/generalize/action | Hiermee wordt de status van de virtuele machine ingesteld op gegeneraliseerd en wordt de virtuele machine voor bereid voor vastleggen |
> | Actie | Microsoft.Compute/virtualMachines/instanceView/read | Hiermee wordt de gedetailleerde runtime status van de virtuele machine en de bijbehorende resources opgehaald |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Als gewone gebruiker aanmelden bij een virtuele machine |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Aanmelden bij een virtuele machine met Windows-beheerders-of Linux-root gebruikers bevoegdheden |
> | Actie | Microsoft.Compute/virtualMachines/performMaintenance/action | Onderhouds bewerking op de virtuele machine wordt uitgevoerd. |
> | Actie | Microsoft.Compute/virtualMachines/powerOff/action | Hiermee wordt de virtuele machine uitgeschakeld. Houd er rekening mee dat de virtuele machine nog steeds wordt gefactureerd. |
> | Actie | Microsoft.Compute/virtualMachines/read | De eigenschappen van een virtuele machine ophalen |
> | Actie | Microsoft.Compute/virtualMachines/redeploy/action | Hiermee wordt de virtuele machine opnieuw geïmplementeerd |
> | Actie | Microsoft.Compute/virtualMachines/reimage/action | Hiermee wordt de installatie kopie van de virtuele machine die gebruikmaakt van een differentiërende schijf. |
> | Actie | Microsoft.Compute/virtualMachines/restart/action | Hiermee wordt de virtuele machine opnieuw opgestart |
> | Actie | Microsoft.Compute/virtualMachines/runCommand/action | Hiermee wordt een vooraf gedefinieerd script uitgevoerd op de virtuele machine |
> | Actie | Microsoft.Compute/virtualMachines/start/action | De virtuele machine wordt gestart |
> | Actie | Microsoft.Compute/virtualMachines/vmSizes/read | Een lijst met beschik bare grootten waarmee de virtuele machine kan worden bijgewerkt |
> | Actie | Microsoft.Compute/virtualMachines/write | Hiermee wordt een nieuwe virtuele machine gemaakt of een bestaande virtuele machine bijgewerkt |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Hiermee worden de reken resources voor de instanties van de virtuele-machine Schaalset uitgeschakeld en vrijgegeven  |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/delete | Hiermee wordt de Schaalset voor virtuele machines verwijderd |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/delete/action | Hiermee verwijdert u de exemplaren van de virtuele-machine Schaalset |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Hiermee wordt de extensie van de virtuele-machine Schaalset verwijderd |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Hiermee worden de eigenschappen van een virtuele-machine Scale set-extensie opgehaald |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Hiermee wordt een nieuwe extensie van een virtuele-machine Schaalset gemaakt of een bestaande bijgewerkt |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | De platform update domeinen van een virtuele-machine Schaalset van service Fabric hand matig door lopen om een wachtende update te volt ooien |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Hiermee wordt de instantie weergave van de virtuele-machine Schaalset opgehaald |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Hiermee worden instanties hand matig bijgewerkt naar het laatste model van de virtuele-machine Schaalset |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Eigenschappen van alle netwerk interfaces van een Schaalset voor virtuele machines ophalen |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Start een rolling upgrade om alle exemplaren van virtuele-machine schaal sets te verplaatsen naar de meest recente versie van het besturings systeem van de platform installatie kopie. |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Hiermee wordt de geschiedenis van besturingssysteem upgrades voor een Schaalset voor virtuele machines opgehaald |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Hiermee wordt gepland onderhoud uitgevoerd op de exemplaren van de Schaalset voor virtuele machines |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Hiermee worden de exemplaren van de Schaalset voor virtuele machines uitgeschakeld |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Eigenschappen van alle open bare IP-adressen van een Schaalset voor virtuele machines ophalen |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/read | De eigenschappen van een Schaalset voor virtuele machines ophalen |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | De exemplaren van de Schaalset voor virtuele machines opnieuw implementeren |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Hiermee wordt de installatie kopie van de exemplaren van de virtuele-machine Schaalset opnieuw ingesteld |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Hiermee worden alle schijven (besturingssysteem schijf en gegevens schijven) opnieuw ingesteld voor de exemplaren van een Schaalset voor virtuele machines  |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/restart/action | Hiermee worden de exemplaren van de Schaalset voor virtuele machines opnieuw gestart |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Hiermee wordt de rolling upgrade van een Schaalset voor virtuele machines geannuleerd |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | De meest recente status van de rolling upgrade ophalen voor een Schaalset voor virtuele machines |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/scale/action | Controleren of een bestaande Schaalset voor virtuele machines kan worden geschaald/uitgebreid naar het opgegeven aantal instanties |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/skus/read | Een lijst met geldige Sku's voor een bestaande Schaalset voor virtuele machines |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/start/action | Hiermee worden de exemplaren van de Schaalset voor virtuele machines gestart |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Hiermee worden de reken resources voor een virtuele machine in een VM-Schaalset uitgeschakeld en vrijgegeven. |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Een specifieke virtuele machine in een VM-Schaalset verwijderen. |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Hiermee wordt de instantie weergave opgehaald van een virtuele machine in een VM-Schaalset. |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Haal de eigenschappen van een openbaar IP-adres op dat is gemaakt met de virtuele-machine Schaalset. Virtuele-machine schaal sets kunnen Maxi maal één openbaar IP-adres per ipconfiguration maken (privé IP) |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Eigenschappen ophalen van een of alle IP-configuraties van een netwerk interface die is gemaakt met de virtuele-machine Schaalset. IP-configuraties vertegenwoordigen privé Ip's |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Eigenschappen ophalen van een of alle netwerk interfaces van een virtuele machine die is gemaakt met een Schaalset voor virtuele machines |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Hiermee wordt gepland onderhoud uitgevoerd op een exemplaar van een virtuele machine in een Schaalset voor virtuele machines |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Hiermee wordt een exemplaar van een virtuele machine in een VM-Schaalset afgezet. |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Hiermee worden de eigenschappen van een virtuele machine in een VM-Schaalset opgehaald |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Hiermee wordt een exemplaar van een virtuele machine in een Schaalset voor virtuele machines opnieuw geïmplementeerd |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Hiermee wordt de installatie kopie van een exemplaar van een virtuele machine in een Schaalset voor virtuele machines opnieuw ingesteld. |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Hiermee wordt de installatie kopie van alle schijven (besturingssysteem schijf en gegevens schijven) voor het exemplaar van een virtuele machine in een VM-Schaalset opnieuw ingesteld. |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Hiermee wordt een exemplaar van een virtuele machine in een VM-Schaalset opnieuw opgestart. |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Hiermee wordt een vooraf gedefinieerd script uitgevoerd op een exemplaar van een virtuele machine in een Schaalset met virtuele machines. |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Hiermee start u een exemplaar van een virtuele machine in een VM-Schaalset. |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Hiermee worden de eigenschappen van een virtuele machine in een VM-Schaalset bijgewerkt |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | Beschik bare grootten voor het maken of bijwerken van een virtuele machine in de Schaalset voor virtuele machines weer geven |
> | Actie | Microsoft.Compute/virtualMachineScaleSets/write | Hiermee wordt een nieuwe Schaalset voor virtuele machines gemaakt of een bestaande bijgewerkt |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. verbruik/aggregatedcost/lezen | Lijst AggregatedCost voor beheer groep. |
> | Actie | Microsoft.Consumption/balances/read | Het overzicht van het gebruik van een facturerings periode voor een beheer groep weer geven. |
> | Actie | Microsoft.Consumption/budgets/delete | De budgetten van een abonnement of een beheer groep verwijderen. |
> | Actie | Microsoft.Consumption/budgets/read | De budgetten op basis van een abonnement of een beheer groep weer geven. |
> | Actie | Microsoft.Consumption/budgets/write | Hiermee maakt en bijwerkt u de budgetten op basis van een abonnement of een beheer groep. |
> | Actie | Micro soft. verbruik/toeslagen/lezen | Lijst met kosten |
> | Actie | Micro soft. verbruik/tegoed/lezen | Lijst tegoeden |
> | Actie | Micro soft. verbruik/gebeurtenissen/lezen | Gebeurtenissen weer geven |
> | Actie | Micro soft. verbruik/externalBillingAccounts/tags/lezen | Tags weer geven voor EA en abonnementen. |
> | Actie | Micro soft. verbruik/externalSubscriptions/tags/lezen | Tags weer geven voor EA en abonnementen. |
> | Actie | Micro soft. verbruik/prognoses/lezen | Prognoses weer geven |
> | Actie | Micro soft. verbruik/loten/lezen | Loten weer geven |
> | Actie | Microsoft.Consumption/marketplaces/read | Vermeld de details van het resource gebruik van Marketplace voor een bereik voor EA en webdirect-abonnementen. |
> | Actie | Micro soft. verbruik/operationresults/lezen | Operationresults weer geven |
> | Actie | Micro soft. verbruik/bewerkingen/lezen | Een lijst met alle ondersteunde bewerkingen door de resource provider van micro soft. verbruik. |
> | Actie | Micro soft. verbruik/operationstatus/lezen | Operationstatus weer geven |
> | Actie | Microsoft.Consumption/pricesheets/read | De Pricesheets-gegevens voor een abonnement of een beheer groep weer geven. |
> | Actie | Micro soft. verbruik/registreren/actie | Registreren voor gebruik van RP |
> | Actie | Micro soft. verbruik/reservationDetails/lezen | Vermeld de gebruiks gegevens voor gereserveerde instanties per reserverings order of beheer groepen. De details van de gegevens zijn per exemplaar per niveau. |
> | Actie | Micro soft. verbruik/reservationRecommendations/lezen | Een lijst met enkele of gedeelde aanbevelingen voor gereserveerde instanties voor een abonnement. |
> | Actie | Microsoft.Consumption/reservationSummaries/read | Vermeld het gebruiks overzicht voor gereserveerde instanties per reserverings order of beheer groepen. De overzichts gegevens zijn maandelijks of dagelijks. |
> | Actie | Microsoft.Consumption/reservationTransactions/read | Vermeld de transactie geschiedenis voor gereserveerde instanties per beheer groep. |
> | Actie | Micro soft. verbruik/tags/lezen | Tags weer geven voor EA en abonnementen. |
> | Actie | Micro soft. verbruik/tenants/lezen | Tenants weer geven |
> | Actie | Micro soft. verbruik/tenants/registreren/actie | Registreer de actie voor het bereik van micro soft. verbruik door een Tenant. |
> | Actie | Micro soft. verbruik/voor waarden/lezen | De voor waarden voor een abonnement of een beheer groep weer geven. |
> | Actie | Microsoft.Consumption/usageDetails/read | Vermeld de gebruiks gegevens voor een bereik voor EA en webdirect-abonnementen. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. ContainerInstance/containerGroups/containers/buildlogs/lezen | Bouw logboeken voor een specifieke container ophalen. |
> | Actie | Microsoft.ContainerInstance/containerGroups/containers/exec/action | In een specifieke container uitvoeren. |
> | Actie | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Logboeken voor een specifieke container ophalen. |
> | Actie | Microsoft.ContainerInstance/containerGroups/delete | De specifieke container groep verwijderen. |
> | Actie | Micro soft. ContainerInstance/containerGroups/operationResults/lezen | Resultaat van asynchrone bewerking ophalen |
> | Actie | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee wordt de diagnostische instelling voor de container groep opgehaald. |
> | Actie | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de container groep gemaakt of bijgewerkt. |
> | Actie | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor de container groep. |
> | Actie | Microsoft.ContainerInstance/containerGroups/read | Alle container groepen ophalen. |
> | Actie | Microsoft.ContainerInstance/containerGroups/restart/action | Hiermee wordt een specifieke container groep opnieuw gestart. |
> | Actie | Microsoft.ContainerInstance/containerGroups/start/action | Hiermee start u een specifieke container groep. |
> | Actie | Microsoft.ContainerInstance/containerGroups/stop/action | Hiermee stopt u een specifieke container groep. De toewijzing van reken resources wordt ongedaan gemaakt en de facturering wordt gestopt. |
> | Actie | Microsoft.ContainerInstance/containerGroups/write | Een specifieke container groep maken of bijwerken. |
> | Actie | Microsoft.ContainerInstance/locations/cachedImages/read | Hiermee worden de in de cache opgeslagen installatie kopieën voor het abonnement in een regio opgehaald. |
> | Actie | Microsoft.ContainerInstance/locations/capabilities/read | De mogelijkheden voor een regio ophalen. |
> | Actie | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Hiermee wordt aan micro soft. ContainerInstance door gemeld dat het virtuele netwerk of subnet wordt verwijderd. |
> | Actie | Micro soft. ContainerInstance/locaties/bewerkingen/lezen | De bewerkingen voor de Azure container instance-service weer geven. |
> | Actie | Microsoft.ContainerInstance/locations/usages/read | Het gebruik voor een bepaalde regio ophalen. |
> | Actie | Micro soft. ContainerInstance/Operations/lezen | De bewerkingen voor de Azure container instance-service weer geven. |
> | Actie | Microsoft.ContainerInstance/register/action | Registreert het abonnement voor de resource provider van de container instantie en maakt het mogelijk om container groepen te maken. |
> | Actie | Micro soft. ContainerInstance/serviceassociationlinks/verwijderen | Verwijder de koppeling van de service koppeling die is gemaakt door de resource provider van het Azure container exemplaar op een subnet. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.ContainerRegistry/checkNameAvailability/read | Controleert of de container register naam beschikbaar is voor gebruik. |
> | Actie | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Hiermee wordt aan micro soft. ContainerRegistry door gemeld dat het virtuele netwerk of subnet wordt verwijderd |
> | Actie | Microsoft.ContainerRegistry/locations/operationResults/read | Hiermee wordt een asynchroon bewerking resultaat opgehaald |
> | Actie | Microsoft.ContainerRegistry/operations/read | Een lijst met alle beschik bare Azure Container Registry REST API bewerkingen |
> | Actie | Microsoft.ContainerRegistry/register/action | Hiermee wordt het abonnement voor de resource provider van de container register geregistreerd en wordt het maken van container registers ingeschakeld. |
> | Actie | Microsoft.ContainerRegistry/registries/artifacts/delete | Artefact verwijderen in container register. |
> | Actie | Microsoft.ContainerRegistry/registries/builds/cancel/action | Hiermee wordt een bestaande build geannuleerd. |
> | Actie | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Hiermee wordt een koppeling opgehaald om de build-logboeken te downloaden. |
> | Actie | Microsoft.ContainerRegistry/registries/builds/read | Hiermee worden de eigenschappen van de opgegeven build opgehaald of worden alle builds voor het opgegeven container register weer gegeven. |
> | Actie | Micro soft. ContainerRegistry/registers/builds/schrijven | Hiermee wordt een build voor een container register bijgewerkt met de opgegeven para meters. |
> | Actie | Microsoft.ContainerRegistry/registries/buildTasks/delete | Hiermee verwijdert u een bouw taak uit een container register. |
> | Actie | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Hiermee worden de eigenschappen van broncode beheer voor een build-taak weer gegeven. |
> | Actie | Microsoft.ContainerRegistry/registries/buildTasks/read | Hiermee worden de eigenschappen van de opgegeven build-taak opgehaald of worden alle build-taken voor het opgegeven container register weer gegeven. |
> | Actie | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Hiermee verwijdert u een build-stap van een build-taak. |
> | Actie | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Hier worden de argumenten voor het maken van een build-stap weer gegeven, inclusief de geheime argumenten. |
> | Actie | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Hiermee worden de eigenschappen van de opgegeven build-stap opgehaald of worden alle stappen voor het maken van de opgegeven build-taak weer gegeven. |
> | Actie | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Hiermee wordt een build-stap voor een build-taak gemaakt of bijgewerkt met de opgegeven para meters. |
> | Actie | Microsoft.ContainerRegistry/registries/buildTasks/write | Hiermee wordt een build-taak voor een container register gemaakt of bijgewerkt met de opgegeven para meters. |
> | Actie | Microsoft.ContainerRegistry/registries/delete | Hiermee verwijdert u een container register. |
> | Actie | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Hiermee verwijdert u een gebeurtenis raster filter uit een container register. |
> | Actie | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Hiermee worden de eigenschappen van het opgegeven gebeurtenis raster filter opgehaald of worden alle Event grid-filters voor het opgegeven container register weer gegeven. |
> | Actie | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Hiermee wordt een gebeurtenis raster filter voor een container register met de opgegeven para meters gemaakt of bijgewerkt. |
> | Actie | Micro soft. ContainerRegistry/registers/generateCredentials/actie | Sleutels genereren voor een token van een opgegeven container register. |
> | Actie | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Hiermee wordt de upload locatie opgehaald zodat de gebruiker de bron kan uploaden. |
> | Actie | Microsoft.ContainerRegistry/registries/importImage/action | Importeer de installatie kopie naar het container register met de opgegeven para meters. |
> | Actie | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | URL-locatie voor uploaden van bron voor een container register ophalen. |
> | Actie | Microsoft.ContainerRegistry/registries/listCredentials/action | Geeft een lijst van de aanmeldings referenties voor het opgegeven container register. |
> | Actie | Microsoft.ContainerRegistry/registries/listPolicies/read | Geeft een lijst van de beleids regels voor het opgegeven container register |
> | Actie | Microsoft.ContainerRegistry/registries/listUsages/read | Hiermee worden de quota gebruikt voor het opgegeven container register. |
> | Actie | Microsoft.ContainerRegistry/registries/metadata/read | Hiermee worden de meta gegevens van een specifieke opslag plaats voor een container register opgehaald |
> | Actie | Microsoft.ContainerRegistry/registries/metadata/write | Hiermee worden de meta gegevens van een opslag plaats bijgewerkt voor een container register |
> | Actie | Microsoft.ContainerRegistry/registries/operationStatuses/read | Hiermee wordt een asynchrone bewerkings status van het REGI ster opgehaald |
> | Actie | Microsoft.ContainerRegistry/registries/pull/read | Pull of ophalen van installatie kopieën uit een container register. |
> | Actie | Microsoft.ContainerRegistry/registries/push/write | Push of schrijf installatie kopieën naar een container register. |
> | Actie | Micro soft. ContainerRegistry/registers/quarantaine/lezen | In quarantaine geplaatste installatie kopieën verzamelen of ophalen uit het container register |
> | Actie | Micro soft. ContainerRegistry/registers/quarantaine/schrijven | De quarantaine status van in quarantaine geplaatste installatie kopieën schrijven/wijzigen |
> | Actie | Microsoft.ContainerRegistry/registries/queueBuild/action | Hiermee maakt u een nieuwe build op basis van de aanvraag parameters en voegt u deze toe aan de build-wachtrij. |
> | Actie | Microsoft.ContainerRegistry/registries/read | Hiermee worden de eigenschappen van het opgegeven container register opgehaald of worden alle container registers weer gegeven onder de opgegeven resource groep of dit abonnement. |
> | Actie | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Hiermee wordt een van de aanmeldings referenties voor het opgegeven container register opnieuw gegenereerd. |
> | Actie | Microsoft.ContainerRegistry/registries/replications/delete | Hiermee verwijdert u een replicatie uit een container register. |
> | Actie | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Hiermee wordt de status van een asynchrone replicatie bewerking opgehaald |
> | Actie | Microsoft.ContainerRegistry/registries/replications/read | Hiermee worden de eigenschappen van de opgegeven replicatie opgehaald of worden alle replicaties voor het opgegeven container register weer gegeven. |
> | Actie | Microsoft.ContainerRegistry/registries/replications/write | Hiermee wordt een replicatie voor een container register met de opgegeven para meters gemaakt of bijgewerkt. |
> | Actie | Microsoft.ContainerRegistry/registries/runs/cancel/action | Een bestaande uitvoering annuleren. |
> | Actie | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Hiermee wordt de SAS-URL voor het logboek opgehaald voor een run. |
> | Actie | Microsoft.ContainerRegistry/registries/runs/read | Hiermee worden de eigenschappen opgehaald van een uitvoering op basis van een container register of lijst runs. |
> | Actie | Microsoft.ContainerRegistry/registries/runs/write | Hiermee wordt een run bijgewerkt. |
> | Actie | Microsoft.ContainerRegistry/registries/scheduleRun/action | Een uitvoering plannen op basis van een container register. |
> | Actie | Micro soft. ContainerRegistry/registers/scopeMaps/verwijderen | Hiermee verwijdert u een scope toewijzing uit een container register. |
> | Actie | Micro soft. ContainerRegistry/registers/scopeMaps/operationStatuses/lezen | Hiermee wordt een asynchrone bewerkings status van een scope toewijzing opgehaald. |
> | Actie | Micro soft. ContainerRegistry/registers/scopeMaps/lezen | Hiermee worden de eigenschappen van de opgegeven Scope toewijzing opgehaald of worden alle scope toewijzingen voor het opgegeven container register weer gegeven. |
> | Actie | Micro soft. ContainerRegistry/registers/scopeMaps/schrijven | Hiermee wordt een scope toewijzing voor een container register met de opgegeven para meters gemaakt of bijgewerkt. |
> | Actie | Micro soft. ContainerRegistry/registers/ondertekenen/schrijven | Meta gegevens van de vertrouwens relatie push/pull voor een container register. |
> | Actie | Microsoft.ContainerRegistry/registries/tasks/delete | Hiermee verwijdert u een taak voor een container register. |
> | Actie | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Alle details van een taak voor een container register weer geven. |
> | Actie | Microsoft.ContainerRegistry/registries/tasks/read | Hiermee wordt een taak opgehaald voor een container register of worden alle taken weer gegeven. |
> | Actie | Microsoft.ContainerRegistry/registries/tasks/write | Hiermee wordt een taak voor een container register gemaakt of bijgewerkt. |
> | Actie | Micro soft. ContainerRegistry/registers/tokens/verwijderen | Hiermee verwijdert u een token uit een container register. |
> | Actie | Micro soft. ContainerRegistry/registers/tokens/operationStatuses/lezen | Hiermee wordt de status van een token async-bewerking opgehaald. |
> | Actie | Micro soft. ContainerRegistry/registers/tokens/lezen | Hiermee worden de eigenschappen van het opgegeven token opgehaald of worden alle tokens voor het opgegeven container register weer gegeven. |
> | Actie | Micro soft. ContainerRegistry/registers/tokens/schrijven | Hiermee wordt een token voor een container register met de opgegeven para meters gemaakt of bijgewerkt. |
> | Actie | Microsoft.ContainerRegistry/registries/updatePolicies/write | Hiermee wordt het beleid voor het opgegeven container register bijgewerkt |
> | Actie | Micro soft. ContainerRegistry/registers/webhooks/verwijderen | Hiermee verwijdert u een webhook uit een container register. |
> | Actie | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Hiermee wordt de configuratie van de service-URI en aangepaste headers voor de webhook opgehaald. |
> | Actie | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Een lijst met recente gebeurtenissen voor de opgegeven webhook. |
> | Actie | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Hiermee wordt een async-bewerkings status van webhook opgehaald |
> | Actie | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Hiermee wordt een ping-gebeurtenis geactiveerd die naar de webhook moet worden verzonden. |
> | Actie | Microsoft.ContainerRegistry/registries/webhooks/read | Hiermee worden de eigenschappen van de opgegeven webhook opgehaald of worden alle webhooks voor het opgegeven container register weer gegeven. |
> | Actie | Micro soft. ContainerRegistry/registers/webhooks/schrijven | Hiermee wordt een webhook voor een container register met de opgegeven para meters gemaakt of bijgewerkt. |
> | Actie | Micro soft. ContainerRegistry/registers/schrijven | Hiermee wordt een container register met de opgegeven para meters gemaakt of bijgewerkt. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.ContainerService/containerServices/delete | Hiermee wordt een container service verwijderd |
> | Actie | Microsoft.ContainerService/containerServices/read | Een container service ophalen |
> | Actie | Microsoft.ContainerService/containerServices/write | Hiermee wordt een nieuwe container service gemaakt of een bestaande bijgewerkt |
> | Actie | Microsoft.ContainerService/locations/operationresults/read | Hiermee wordt de status van een asynchroon bewerkings resultaat opgehaald |
> | Actie | Microsoft.ContainerService/locations/operations/read | Hiermee wordt de status van een asynchrone bewerking opgehaald |
> | Actie | Microsoft.ContainerService/locations/orchestrators/read | Een lijst met ondersteunde Orchestrator |
> | Actie | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Een beheerd cluster toegangs profiel verkrijgen met een rolnaam met behulp van de lijst referentie |
> | Actie | Microsoft.ContainerService/managedClusters/accessProfiles/read | Een beheerd cluster toegangs profiel ophalen op basis van rolnaam |
> | Actie | Microsoft.ContainerService/managedClusters/agentPools/delete | Hiermee verwijdert u een agent groep |
> | Actie | Microsoft.ContainerService/managedClusters/agentPools/read | Hiermee wordt een agent groep opgehaald |
> | Actie | Micro soft. container service/managedClusters/agentPools/upgradeProfiles/lezen | Hiermee wordt het upgrade Profiel van de agent groep opgehaald |
> | Actie | Microsoft.ContainerService/managedClusters/agentPools/write | Hiermee wordt een nieuwe agent groep gemaakt of een bestaande bijgewerkt |
> | Actie | Micro soft. container service/managedClusters/availableAgentPoolVersions/lezen | Hiermee worden de beschik bare versies van de agent groep van het cluster opgehaald |
> | Actie | Microsoft.ContainerService/managedClusters/delete | Hiermee wordt een beheerd cluster verwijderd |
> | Actie | Micro soft. container service/managedClusters/detectors/lezen | Beheerde cluster detectie ophalen |
> | Actie | Micro soft. container service/managedClusters/diagnosticsState/lezen | Hiermee wordt de diagnostische status van het cluster opgehaald |
> | Actie | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | De clusterAdmin-referentie van een beheerd cluster weer geven |
> | Actie | Micro soft. container service/managedClusters/listClusterMonitoringUserCredential/Action | De clusterMonitoringUser-referentie van een beheerd cluster weer geven |
> | Actie | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | De clusterUser-referentie van een beheerd cluster weer geven |
> | Actie | Micro soft. container service/managedClusters/privateEndpointConnectionsApproval/Action | Hiermee wordt bepaald of de gebruiker een verbinding met een privé-eind punt mag goed keuren |
> | Actie | Microsoft.ContainerService/managedClusters/read | Een beheerd cluster ophalen |
> | Actie | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Het AAD-Profiel van een beheerd cluster opnieuw instellen |
> | Actie | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Het Service-Principal-Profiel van een beheerd cluster opnieuw instellen |
> | Actie | Micro soft. container service/managedClusters/rotateClusterCertificates/Action | Certificaten van een beheerd cluster draaien |
> | Actie | Micro soft. container service/managedClusters/upgradeProfiles/lezen | Hiermee wordt het upgrade Profiel van het cluster opgehaald |
> | Actie | Microsoft.ContainerService/managedClusters/write | Hiermee maakt u een nieuw beheerd cluster of wordt er een bestaande bijgewerkt |
> | Actie | Microsoft.ContainerService/openShiftClusters/delete | Een open-Shift-cluster verwijderen |
> | Actie | Microsoft.ContainerService/openShiftClusters/read | Een open-Shift-cluster ophalen |
> | Actie | Microsoft.ContainerService/openShiftClusters/write | Hiermee maakt u een nieuw open-ploeg cluster of werkt u een bestaand item bij |
> | Actie | Microsoft.ContainerService/openShiftManagedClusters/delete | Een door open ploeg beheerd cluster verwijderen |
> | Actie | Microsoft.ContainerService/openShiftManagedClusters/read | Een open Shift-beheerd cluster ophalen |
> | Actie | Microsoft.ContainerService/openShiftManagedClusters/write | Hiermee maakt u een nieuw open-Shift-beheerd cluster of wordt een bestaand item bijgewerkt |
> | Actie | Microsoft.ContainerService/operations/read | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn voor de resource provider micro soft. container service |
> | Actie | Microsoft.ContainerService/register/action | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. container service |
> | Actie | Microsoft.ContainerService/unregister/action | Registratie van het abonnement met de resource provider micro soft. container service is ongedaan gemaakt |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. CostManagement/waarschuwingen/schrijven | Waarschuwingen bijwerken. |
> | Actie | Microsoft.CostManagement/cloudConnectors/delete | De opgegeven Cloud connector verwijderen. |
> | Actie | Microsoft.CostManagement/cloudConnectors/read | De cloudConnectors voor de geverifieerde gebruiker weer geven. |
> | Actie | Microsoft.CostManagement/cloudConnectors/write | De opgegeven Cloud connector maken of bijwerken. |
> | Actie | Microsoft.CostManagement/dimensions/read | Een lijst met alle ondersteunde dimensies op basis van een bereik. |
> | Actie | Microsoft.CostManagement/exports/action | Voer de opgegeven export uit. |
> | Actie | Microsoft.CostManagement/exports/delete | De opgegeven export verwijderen. |
> | Actie | Microsoft.CostManagement/exports/read | De exports op basis van het bereik weer geven. |
> | Actie | Microsoft.CostManagement/exports/run/action | Uitvoer uitvoeren. |
> | Actie | Micro soft. CostManagement/export/write | De opgegeven export maken of bijwerken. |
> | Actie | Micro soft. CostManagement/externalBillingAccounts/Dimension/lezen | Alle ondersteunde dimensies voor externe BillingAccounts weer geven. |
> | Actie | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | De externalSubscriptions in een externalBillingAccount voor de geverifieerde gebruiker weer geven. |
> | Actie | Micro soft. CostManagement/externalBillingAccounts/Forecast/Action | Prognose van gebruiks gegevens voor externe BillingAccounts. |
> | Actie | Micro soft. CostManagement/externalBillingAccounts/Forecast/lezen | Prognose van gebruiks gegevens voor externe BillingAccounts. |
> | Actie | Micro soft. CostManagement/externalBillingAccounts/query/actie | Query gegevens over het gebruik van externe BillingAccounts. |
> | Actie | Micro soft. CostManagement/externalBillingAccounts/query/lezen | Query gegevens over het gebruik van externe BillingAccounts. |
> | Actie | Microsoft.CostManagement/externalBillingAccounts/read | De externalBillingAccounts voor de geverifieerde gebruiker weer geven. |
> | Actie | Micro soft. CostManagement/externalSubscriptions/Dimensions/lezen | Alle ondersteunde dimensies voor het externe abonnement weer geven. |
> | Actie | Micro soft. CostManagement/externalSubscriptions/Forecast/Action | Prognose van gebruiks gegevens voor externe BillingAccounts. |
> | Actie | Micro soft. CostManagement/externalSubscriptions/Forecast/lezen | Prognose van gebruiks gegevens voor externe BillingAccounts. |
> | Actie | Micro soft. CostManagement/externalSubscriptions/query/actie | Query gegevens over het gebruik van een extern abonnement. |
> | Actie | Micro soft. CostManagement/externalSubscriptions/query/lezen | Query gegevens over het gebruik van een extern abonnement. |
> | Actie | Microsoft.CostManagement/externalSubscriptions/read | De externalSubscriptions voor de geverifieerde gebruiker weer geven. |
> | Actie | Microsoft.CostManagement/externalSubscriptions/write | Gekoppelde beheer groep van externalSubscription bijwerken |
> | Actie | Micro soft. CostManagement/Forecast/Action | Prognose van gebruiks gegevens op basis van een bereik. |
> | Actie | Micro soft. CostManagement/Forecast/lezen | Prognose van gebruiks gegevens op basis van een bereik. |
> | Actie | Micro soft. CostManagement/Operations/lezen | Alle ondersteunde bewerkingen van de resource provider micro soft. CostManagement weer geven. |
> | Actie | Microsoft.CostManagement/query/action | Query's uitvoeren op gebruiks gegevens met een bereik. |
> | Actie | Microsoft.CostManagement/query/read | Query's uitvoeren op gebruiks gegevens met een bereik. |
> | Actie | Microsoft.CostManagement/register/action | Registreer de actie voor het bereik van micro soft. CostManagement door een abonnement. |
> | Actie | Microsoft.CostManagement/reports/action | Plan rapporten over gebruiks gegevens met een bereik. |
> | Actie | Micro soft. CostManagement/rapporten/lezen | Plan rapporten over gebruiks gegevens met een bereik. |
> | Actie | Microsoft.CostManagement/tenants/register/action | Registreer de actie voor het bereik van micro soft. CostManagement door een Tenant. |
> | Actie | Micro soft. CostManagement/weer gaven/actie | Weer gave maken. |
> | Actie | Micro soft. CostManagement/weer gaven/verwijderen | Opgeslagen weer gaven verwijderen. |
> | Actie | Micro soft. CostManagement/views/lezen | Lijst met alle opgeslagen weer gaven. |
> | Actie | Micro soft. CostManagement/weer gaven/schrijven | Update weergave. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Hiermee kunt u een ophalen van retour zendingen boeken. |
> | Actie | Microsoft.DataBox/jobs/cancel/action | Hiermee wordt een volg orde die wordt uitgevoerd, geannuleerd. |
> | Actie | Microsoft.DataBox/jobs/delete | De orders verwijderen |
> | Actie | Microsoft.DataBox/jobs/listCredentials/action | Geeft een lijst van de niet-versleutelde referenties die zijn gerelateerd aan de order. |
> | Actie | Microsoft.DataBox/jobs/read | De orders weer geven of ophalen |
> | Actie | Microsoft.DataBox/jobs/write | De orders maken of bijwerken |
> | Actie | Microsoft.DataBox/locations/availableSkus/action | Met deze methode wordt de lijst met beschik bare sku's geretourneerd. |
> | Actie | Micro soft. DataBox/locaties/availableSkus/lezen | De beschik bare Sku's weer geven of ophalen |
> | Actie | Microsoft.DataBox/locations/operationResults/read | De resultaten van de bewerking weer geven of ophalen |
> | Actie | Micro soft. DataBox/locaties/regionConfiguration/actie | Deze methode retourneert de configuraties voor de regio. |
> | Actie | Microsoft.DataBox/locations/validateAddress/action | Valideert het verzend adres en levert eventueel alternatieve adressen. |
> | Actie | Micro soft. DataBox/locaties/validateInputs/actie | Met deze methode worden alle typen validaties ondersteund. |
> | Actie | Micro soft. DataBox/Operations/lezen | De bewerkingen weer geven of ophalen |
> | Actie | Microsoft.DataBox/register/action | Provider micro soft. Databox registreren |
> | Actie | Micro soft. DataBox/abonnementen/resourceGroups/moveResources/actie | Met deze methode wordt de resource verplaatsing uitgevoerd. |
> | Actie | Micro soft. DataBox/abonnementen/resourceGroups/validateMoveResources/actie | Met deze methode wordt gecontroleerd of het verplaatsen van de resource is toegestaan. |
> | Actie | Micro soft. DataBox/registratie/actie | De registratie van de provider micro soft. Databox ongedaan maken |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | De waarschuwingen weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Hiermee worden de bandbreedte schema's verwijderd |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Het resultaat van de bewerking weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | De bandbreedte schema's weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | De bandbreedte schema's weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Hiermee worden de bandbreedte schema's gemaakt of bijgewerkt |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Hiermee worden de Data Box Edge-apparaten verwijderd |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Updates downloaden in het apparaat |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Hiermee wordt uitgebreide informatie over de resource opgehaald |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Updates installeren op het apparaat |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | De taken weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | De netwerk instellingen van het apparaat weer geven of ophalen |
> | Actie | Micro soft. DataBoxEdge/dataBoxEdgeDevices/nodes/Read | Hiermee worden de knoop punten weer gegeven of opgehaald |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Het resultaat van de bewerking weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | De bewerkings status weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | De orders worden verwijderd |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Het resultaat van de bewerking weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | De orders weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | De orders weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Hiermee worden de orders gemaakt of bijgewerkt |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | De Data Box Edge-apparaten weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | De Data Box Edge-apparaten weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | De Data Box Edge-apparaten weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Hiermee worden de rollen verwijderd |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Het resultaat van de bewerking weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Hiermee worden de rollen weer gegeven of opgehaald |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Hiermee worden de rollen weer gegeven of opgehaald |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Hiermee worden de rollen gemaakt of bijgewerkt |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Zoeken naar updates |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Het resultaat van de bewerking weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Beveiligings instellingen bijwerken |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Hiermee worden de shares verwijderd |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Het resultaat van de bewerking weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | De shares weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | De shares weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | De meta gegevens van de share met de gegevens uit de Cloud vernieuwen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Hiermee worden de shares gemaakt of bijgewerkt |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Hiermee verwijdert u de referenties van het opslag account |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Het resultaat van de bewerking weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | De referenties van het opslag account worden weer gegeven of opgehaald |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | De referenties van het opslag account worden weer gegeven of opgehaald |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Hiermee worden de referenties van het opslag account gemaakt of bijgewerkt |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Hiermee verwijdert u de triggers |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Het resultaat van de bewerking weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | De triggers worden weer gegeven of opgehaald |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | De triggers worden weer gegeven of opgehaald |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Hiermee worden de triggers gemaakt of bijgewerkt |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | De samen vatting van de update weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Certificaat voor apparaatregistratie uploaden |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Hiermee verwijdert u de gebruikers delen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Het resultaat van de bewerking weer geven of ophalen |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Hiermee worden de gebruikers van de share weer gegeven of opgehaald |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Hiermee worden de gebruikers van de share weer gegeven of opgehaald |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Hiermee worden de share gebruikers gemaakt of bijgewerkt |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Hiermee worden de Data Box Edge-apparaten gemaakt of bijgewerkt |
> | Actie | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Hiermee worden de Data Box Edge-apparaten gemaakt of bijgewerkt |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Databricks/locations/getNetworkPolicies/action | Netwerk intentie beleid voor een subnet ophalen op basis van de locatie die wordt gebruikt door NRP |
> | Actie | Micro soft. Databricks/locaties/operationstatuses/lezen | Hiermee wordt de bewerkings status van de resource gelezen. |
> | Actie | Micro soft. Databricks/Operations/lezen | Hiermee wordt de lijst met bewerkingen opgehaald. |
> | Actie | Microsoft.Databricks/register/action | Meld u aan bij Databricks. |
> | Actie | Microsoft.Databricks/workspaces/delete | Hiermee verwijdert u een Databricks-werk ruimte. |
> | Actie | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee stelt u de beschik bare Diagnostische instellingen voor de Databricks-werk ruimte in |
> | Actie | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Diagnostische instellingen toevoegen of wijzigen. |
> | Actie | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Hiermee worden de beschik bare logboek definities voor de Databricks-werk ruimte opgehaald |
> | Actie | Microsoft.Databricks/workspaces/read | Hiermee wordt een lijst met Databricks-werk ruimten opgehaald. |
> | Actie | Micro soft. Databricks/werk ruimten/refreshPermissions/actie | Machtigingen voor een werk ruimte vernieuwen |
> | Actie | Micro soft. Databricks/werk ruimten/updateDenyAssignment/actie | Geen acties voor het weigeren van toewijzingen bijwerken voor een beheerde resource groep van een werk ruimte |
> | Actie | Micro soft. Databricks/werk ruimten/virtualNetworkPeerings/verwijderen | Hiermee wordt een virtueel netwerk peering verwijderd |
> | Actie | Micro soft. Databricks/werk ruimten/virtualNetworkPeerings/lezen | Hiermee wordt de peering van het virtuele netwerk opgehaald. |
> | Actie | Micro soft. Databricks/werk ruimten/virtualNetworkPeerings/schrijven | Peering voor het virtuele netwerk toevoegen of wijzigen |
> | Actie | Microsoft.Databricks/workspaces/write | Hiermee maakt u een Databricks-werk ruimte. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DataCatalog/catalogs/delete | Catalogus bron voor Data Catalog resource provider verwijderen. |
> | Actie | Microsoft.DataCatalog/catalogs/read | Lees de resource van de catalogus voor de resource provider van Data Catalog. |
> | Actie | Microsoft.DataCatalog/catalogs/write | Bron voor het schrijven van catalogi voor Data Catalog resource provider. |
> | Actie | Microsoft.DataCatalog/checkNameAvailability/read | Controleer de beschik baarheid van de catalogus naam voor de resource provider van Data Catalog. |
> | Actie | Microsoft.DataCatalog/datacatalogs/delete | Verwijder de DataCatalog-resource voor de resource provider van Data Catalog. |
> | Actie | Microsoft.DataCatalog/datacatalogs/read | Lees de DataCatalog-resource voor de resource provider van Data Catalog. |
> | Actie | Microsoft.DataCatalog/datacatalogs/write | Schrijf de DataCatalog-resource voor de resource provider van Data Catalog. |
> | Actie | Microsoft.DataCatalog/operations/read | Hiermee worden alle beschik bare bewerkingen in Data Catalog resource provider gelezen. |
> | Actie | Microsoft.DataCatalog/register/action | Het abonnement voor de resource provider van Data Catalog registreren |
> | Actie | Micro soft. DataCatalog/registratie/actie | De registratie van het abonnement voor Data Catalog resource provider ongedaan maken |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Hiermee wordt gecontroleerd of de naam van de Data Factory beschikbaar is voor gebruik. |
> | Actie | Microsoft.DataFactory/datafactories/activitywindows/read | Hiermee worden activiteiten Vensters in de Data Factory met opgegeven para meters gelezen. |
> | Actie | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Hiermee worden activiteit Vensters voor de pijplijn activiteit gelezen met de opgegeven para meters. |
> | Actie | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Hiermee worden activiteiten Vensters voor de pijp lijn met de opgegeven para meters gelezen. |
> | Actie | Microsoft.DataFactory/datafactories/datapipelines/delete | Hiermee verwijdert u een pijp lijn. |
> | Actie | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Hiermee wordt een pijp lijn onderbroken. |
> | Actie | Microsoft.DataFactory/datafactories/datapipelines/read | Hiermee wordt een pijp lijn gelezen. |
> | Actie | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Hiermee wordt een pijp lijn hervat. |
> | Actie | Microsoft.DataFactory/datafactories/datapipelines/update/action | Hiermee werkt u een pijp lijn bij. |
> | Actie | Microsoft.DataFactory/datafactories/datapipelines/write | Hiermee wordt een pijp lijn gemaakt of bijgewerkt. |
> | Actie | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Hiermee worden activiteiten Vensters voor de gegevensset met opgegeven para meters gelezen. |
> | Actie | Microsoft.DataFactory/datafactories/datasets/delete | Hiermee verwijdert u alle gegevensset. |
> | Actie | Microsoft.DataFactory/datafactories/datasets/read | Hiermee wordt elke gegevensset gelezen. |
> | Actie | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Hiermee wordt de uitvoering van het gegevens segment voor de opgegeven gegevensset gelezen met de opgegeven begin tijd. |
> | Actie | Microsoft.DataFactory/datafactories/datasets/slices/read | Hiermee worden de gegevens segmenten in de opgegeven periode opgehaald. |
> | Actie | Microsoft.DataFactory/datafactories/datasets/slices/write | De status van het gegevens segment bijwerken. |
> | Actie | Microsoft.DataFactory/datafactories/datasets/write | Hiermee wordt een gegevensset gemaakt of bijgewerkt. |
> | Actie | Microsoft.DataFactory/datafactories/delete | Hiermee verwijdert u de Data Factory. |
> | Actie | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Hiermee worden de verbindings gegevens voor elke gateway gelezen. |
> | Actie | Microsoft.DataFactory/datafactories/gateways/delete | Hiermee verwijdert u alle gateways. |
> | Actie | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Geeft een lijst van de verificatie sleutels voor elke gateway. |
> | Actie | Microsoft.DataFactory/datafactories/gateways/read | Alle gateways worden gelezen. |
> | Actie | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Hiermee worden de verificatie sleutels voor alle gateways opnieuw gegenereerd. |
> | Actie | Microsoft.DataFactory/datafactories/gateways/write | Hiermee wordt een gateway gemaakt of bijgewerkt. |
> | Actie | Microsoft.DataFactory/datafactories/linkedServices/delete | Hiermee verwijdert u alle gekoppelde services. |
> | Actie | Microsoft.DataFactory/datafactories/linkedServices/read | Hiermee wordt alle gekoppelde services gelezen. |
> | Actie | Microsoft.DataFactory/datafactories/linkedServices/write | Hiermee wordt een gekoppelde service gemaakt of bijgewerkt. |
> | Actie | Microsoft.DataFactory/datafactories/read | Hiermee wordt de Data Factory gelezen. |
> | Actie | Microsoft.DataFactory/datafactories/runs/loginfo/read | Hiermee leest u een SAS-URI naar een BLOB-container die de logboeken bevat. |
> | Actie | Microsoft.DataFactory/datafactories/tables/delete | Hiermee verwijdert u alle gegevensset. |
> | Actie | Microsoft.DataFactory/datafactories/tables/read | Hiermee wordt elke gegevensset gelezen. |
> | Actie | Microsoft.DataFactory/datafactories/tables/write | Hiermee wordt een gegevensset gemaakt of bijgewerkt. |
> | Actie | Microsoft.DataFactory/datafactories/write | Hiermee wordt de Data Factory gemaakt of bijgewerkt. |
> | Actie | Micro soft. DataFactory/factories/addDataFlowToDebugSession/actie | Voeg gegevens stroom toe aan foutopsporingssessie voor preview. |
> | Actie | Microsoft.DataFactory/factories/cancelpipelinerun/action | Annuleert de uitvoering van de pijp lijn die is opgegeven door de run-ID. |
> | Actie | Micro soft. DataFactory/factories/cancelSandboxPipelineRun/actie | Hiermee wordt een debug-uitvoering voor de pijp lijn geannuleerd. |
> | Actie | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Hiermee maakt u een foutopsporingssessie voor gegevens stromen. |
> | Actie | Microsoft.DataFactory/factories/dataflows/delete | Hiermee verwijdert u de gegevens stroom. |
> | Actie | Microsoft.DataFactory/factories/dataflows/read | Hiermee wordt de gegevens stroom gelezen. |
> | Actie | Microsoft.DataFactory/factories/dataflows/write | Gegevens stroom maken of bijwerken |
> | Actie | Microsoft.DataFactory/factories/datasets/delete | Hiermee verwijdert u alle gegevensset. |
> | Actie | Microsoft.DataFactory/factories/datasets/read | Hiermee wordt elke gegevensset gelezen. |
> | Actie | Microsoft.DataFactory/factories/datasets/write | Hiermee wordt een gegevensset gemaakt of bijgewerkt. |
> | Actie | Micro soft. DataFactory/factories/debugpipelineruns/annuleren/actie | Hiermee wordt een debug-uitvoering voor de pijp lijn geannuleerd. |
> | Actie | Microsoft.DataFactory/factories/delete | Hiermee verwijdert u Data Factory. |
> | Actie | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Hiermee verwijdert u een foutopsporingssessie voor gegevens stromen. |
> | Actie | Micro soft. DataFactory/factories/executeDataFlowDebugCommand/actie | Opdracht voor fout opsporing van gegevens stromen uitvoeren. |
> | Actie | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Hiermee krijgt u toegang tot de ADF DataPlane-service. |
> | Actie | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Hiermee leest u de toegang tot de ADF DataPlane-service. |
> | Actie | Microsoft.DataFactory/factories/getFeatureValue/action | De waarde voor de functie voor belichtings controle ophalen voor de specifieke locatie. |
> | Actie | Microsoft.DataFactory/factories/getFeatureValue/read | Hiermee wordt de waarde voor de belichtings controle functie voor de specifieke locatie gelezen. |
> | Actie | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Hiermee wordt het toegangs token voor GitHub opgehaald. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/delete | Hiermee worden alle Integration Runtime verwijderd. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Integration Runtime verbindings gegevens lezen. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Ontvang SSIS Integration Runtime meta gegevens voor het opgegeven Integration Runtime. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Hiermee wordt Integration Runtime status gelezen. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Een gekoppelde Integration Runtime verwijzing maken voor de opgegeven gedeelde Integration Runtime. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Geeft een lijst van de verificatie sleutels voor een Integration Runtime. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Hiermee haalt u de bewakings gegevens voor alle Integration Runtime op. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Hiermee wordt het knoop punt voor de opgegeven Integration Runtime verwijderd. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Retourneert het IP-adres voor het opgegeven knoop punt van de Integration Runtime. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Hiermee leest u het knoop punt voor de opgegeven Integration Runtime. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Hiermee wordt een zelf-hostend Integration Runtime knoop punt bijgewerkt. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/read | Hiermee worden alle Integration Runtime gelezen. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Integration Runtime-meta gegevens voor SSIS vernieuwen voor het opgegeven Integration Runtime. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Hiermee worden de verificatie sleutels voor de opgegeven Integration Runtime opnieuw gegenereerd. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Hiermee verwijdert u gekoppelde Integration Runtime verwijzingen van de opgegeven Integration Runtime. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/start/action | Start alle Integration Runtime. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Hiermee worden alle Integration Runtime gestopt. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Synchroniseert de referenties voor de opgegeven Integration Runtime. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Hiermee wordt de opgegeven Integration Runtime bijgewerkt. |
> | Actie | Microsoft.DataFactory/factories/integrationruntimes/write | Hiermee wordt een Integration Runtime gemaakt of bijgewerkt. |
> | Actie | Microsoft.DataFactory/factories/linkedServices/delete | Hiermee verwijdert u de gekoppelde service. |
> | Actie | Microsoft.DataFactory/factories/linkedServices/read | Hiermee wordt de gekoppelde service gelezen. |
> | Actie | Microsoft.DataFactory/factories/linkedServices/write | Een gekoppelde service maken of bijwerken |
> | Actie | Micro soft. DataFactory/fabrieken/operationResults/lezen | Hiermee worden de resultaten van de bewerking opgehaald. |
> | Actie | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Hiermee leest u de uitvoering van de activiteit voor de opgegeven ID van de pijplijn uitvoering. |
> | Actie | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Annuleert de uitvoering van de pijp lijn die is opgegeven door de run-ID. |
> | Actie | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Query's uitvoeren op de uitvoering van de activiteit voor de opgegeven pijp lijn run ID. |
> | Actie | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Hiermee wordt het resultaat van de uitvoering van de query activiteit voor de opgegeven pijplijn run-ID gelezen. |
> | Actie | Microsoft.DataFactory/factories/pipelineruns/read | De pijplijn uitvoeringen worden gelezen. |
> | Actie | Microsoft.DataFactory/factories/pipelines/createrun/action | Hiermee maakt u een uitvoering voor de pijp lijn. |
> | Actie | Microsoft.DataFactory/factories/pipelines/delete | Hiermee verwijdert u de pijp lijn. |
> | Actie | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Hiermee wordt de voortgang van de uitvoering van de activiteit opgehaald. |
> | Actie | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Hiermee leest u de pijplijn uitvoering. |
> | Actie | Microsoft.DataFactory/factories/pipelines/read | Hiermee leest u de pijp lijn. |
> | Actie | Micro soft. DataFactory/fabrieken/pijp lijnen/sandbox/actie | Hiermee maakt u een uitvoerings omgeving voor fout opsporing voor de pijp lijn. |
> | Actie | Micro soft. DataFactory/fabrieken/pijp lijnen/sandbox/maken/actie | Hiermee maakt u een uitvoerings omgeving voor fout opsporing voor de pijp lijn. |
> | Actie | Micro soft. DataFactory/fabrieken/pijp lijnen/sandbox/uitvoeren/actie | Hiermee maakt u een debug-uitvoering voor de pijp lijn. |
> | Actie | Microsoft.DataFactory/factories/pipelines/write | Pijp lijn maken of bijwerken |
> | Actie | Micro soft. DataFactory/factories/querydataflowdebugsessions/actie | Query's uitvoeren op een Data flow debug-sessie. |
> | Actie | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Voert een query uit op de uitvoeringen van de pijp lijn. |
> | Actie | Microsoft.DataFactory/factories/querypipelineruns/action | Query's uitvoeren op de pijplijn uitvoeringen. |
> | Actie | Microsoft.DataFactory/factories/querypipelineruns/read | Hiermee wordt het resultaat van de query pijplijn uitvoeringen gelezen. |
> | Actie | Microsoft.DataFactory/factories/querytriggerruns/action | Query's uitvoeren op de trigger. |
> | Actie | Microsoft.DataFactory/factories/querytriggerruns/read | Hiermee wordt het resultaat van trigger uitvoeringen gelezen. |
> | Actie | Microsoft.DataFactory/factories/read | Data Factory lezen. |
> | Actie | Micro soft. DataFactory/factories/sandboxpipelineruns/actie | Voert een query uit op de uitvoeringen van de pijp lijn. |
> | Actie | Micro soft. DataFactory/fabrieken/sandboxpipelineruns/lezen | Hiermee haalt u de uitvoerings gegevens voor de fout opsporing voor de pijp lijn op. |
> | Actie | Micro soft. DataFactory/factories/sandboxpipelineruns/sandboxActivityRuns/lezen | Hiermee worden de uitvoerings gegevens van de fout opsporing voor de activiteit opgehaald. |
> | Actie | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Start een sessie voor fout opsporing van gegevens stromen. |
> | Actie | Microsoft.DataFactory/factories/triggerruns/read | Hiermee wordt de uitvoering van de trigger gelezen. |
> | Actie | Microsoft.DataFactory/factories/triggers/delete | Hiermee verwijdert u een trigger. |
> | Actie | Micro soft. DataFactory/factories/triggers/geteventsubscriptionstatus/actie | Status van gebeurtenis abonnement. |
> | Actie | Microsoft.DataFactory/factories/triggers/read | Hiermee wordt elke trigger gelezen. |
> | Actie | Microsoft.DataFactory/factories/triggers/start/action | Hiermee wordt elke trigger gestart. |
> | Actie | Microsoft.DataFactory/factories/triggers/stop/action | Hiermee stopt u een trigger. |
> | Actie | Micro soft. DataFactory/factories/triggers/subscribetoevents/actie | Abonneren op gebeurtenissen. |
> | Actie | Microsoft.DataFactory/factories/triggers/triggerruns/read | Hiermee wordt de uitvoering van de trigger gelezen. |
> | Actie | Micro soft. DataFactory/factories/triggers/unsubscribefromevents/actie | Afmelden bij gebeurtenissen. |
> | Actie | Microsoft.DataFactory/factories/triggers/write | Hiermee wordt een trigger gemaakt of bijgewerkt. |
> | Actie | Microsoft.DataFactory/factories/write | Data Factory maken of bijwerken |
> | Actie | Microsoft.DataFactory/locations/configureFactoryRepo/action | Hiermee configureert u de opslag plaats voor de Factory. |
> | Actie | Microsoft.DataFactory/locations/getFeatureValue/action | De waarde voor de functie voor belichtings controle ophalen voor de specifieke locatie. |
> | Actie | Microsoft.DataFactory/locations/getFeatureValue/read | Hiermee wordt de waarde voor de belichtings controle functie voor de specifieke locatie gelezen. |
> | Actie | Microsoft.DataFactory/operations/read | Hiermee worden alle bewerkingen in micro soft Data Factory provider gelezen. |
> | Actie | Microsoft.DataFactory/register/action | Hiermee wordt het abonnement voor de Data Factory resource provider geregistreerd. |
> | Actie | Microsoft.DataFactory/unregister/action | Hiermee wordt de registratie van het abonnement voor de resource provider Data Factory verwijderd. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Een reken beleid verwijderen. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Informatie over een reken beleid ophalen. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Een reken beleid maken of bijwerken. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Een Data Lake Store-account ontkoppelen van een DataLakeAnalytics-account. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Informatie over een gekoppeld data Lake Store-account van een DataLakeAnalytics-account ophalen. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Een gekoppeld data Lake Store-account maken of bijwerken van een DataLakeAnalytics-account. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/delete | Een DataLakeAnalytics-account verwijderen. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Een firewall regel verwijderen. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Informatie over een firewall regel ophalen. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Een firewall regel maken of bijwerken. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Resultaat van een DataLakeAnalytics-account bewerking ophalen. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/read | Informatie over een bestaand DataLakeAnalytics-account ophalen. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Vermeld SAS-tokens voor opslag containers van een gekoppeld opslag account van een DataLakeAnalytics-account. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Containers ophalen van een gekoppeld opslag account van een DataLakeAnalytics-account. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Een opslag account ontkoppelen van een DataLakeAnalytics-account. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Informatie ophalen over een gekoppeld opslag account van een DataLakeAnalytics-account. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Een gekoppeld opslag account maken of bijwerken van een DataLakeAnalytics-account. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Machtigingen verlenen om taken te annuleren die door andere gebruikers zijn verzonden. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | SystemMaxAnalyticsUnits over DataLakeAnalytics-accounts overdragen. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Verwijder een regel voor het virtuele netwerk. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Informatie over een virtueel netwerk regel ophalen. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Een regel voor een virtueel netwerk maken of bijwerken. |
> | Actie | Microsoft.DataLakeAnalytics/accounts/write | Een DataLakeAnalytics-account maken of bijwerken. |
> | Actie | Microsoft.DataLakeAnalytics/locations/capability/read | Informatie over de mogelijkheden van een abonnement over het gebruik van DataLakeAnalytics. |
> | Actie | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Controleer de beschik baarheid van een DataLakeAnalytics-account naam. |
> | Actie | Microsoft.DataLakeAnalytics/locations/operationResults/read | Resultaat van een DataLakeAnalytics-account bewerking ophalen. |
> | Actie | Microsoft.DataLakeAnalytics/locations/usages/read | Gegevens over quotum gebruik ophalen van een abonnement met behulp van DataLakeAnalytics. |
> | Actie | Microsoft.DataLakeAnalytics/operations/read | Beschik bare bewerkingen voor DataLakeAnalytics ophalen. |
> | Actie | Microsoft.DataLakeAnalytics/register/action | Registreer het abonnement op DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DataLakeStore/accounts/delete | Een Data Lake Store-account verwijderen. |
> | Actie | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Schakel de sleutel kluis in voor een Data Lake Store-account. |
> | Actie | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Een EventGrid-filter verwijderen. |
> | Actie | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Een EventGrid-filter ophalen. |
> | Actie | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Een EventGrid-filter maken of bijwerken. |
> | Actie | Microsoft.DataLakeStore/accounts/firewallRules/delete | Een firewall regel verwijderen. |
> | Actie | Microsoft.DataLakeStore/accounts/firewallRules/read | Informatie over een firewall regel ophalen. |
> | Actie | Microsoft.DataLakeStore/accounts/firewallRules/write | Een firewall regel maken of bijwerken. |
> | Actie | Microsoft.DataLakeStore/accounts/operationResults/read | Resultaat van een Data Lake Store-account bewerking ophalen. |
> | Actie | Microsoft.DataLakeStore/accounts/read | Informatie over een bestaand data Lake Store-account ophalen. |
> | Actie | Micro soft. data Lake Store/accounts/shares/verwijderen | Verwijder een share. |
> | Actie | Micro soft. data Lake Store/accounts/shares/lezen | Informatie over een share ophalen. |
> | Actie | Micro soft. data Lake Store/accounts/shares/schrijven | Een share maken of bijwerken. |
> | Actie | Microsoft.DataLakeStore/accounts/Superuser/action | Verleen de beheerder aan Data Lake Store wanneer deze is verleend met micro soft. Authorization/roleAssignments/write. |
> | Actie | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Verwijder een vertrouwde id-provider. |
> | Actie | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Informatie over een vertrouwde id-provider ophalen. |
> | Actie | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Een vertrouwde id-provider maken of bijwerken. |
> | Actie | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Verwijder een regel voor het virtuele netwerk. |
> | Actie | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Informatie over een virtueel netwerk regel ophalen. |
> | Actie | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Een regel voor een virtueel netwerk maken of bijwerken. |
> | Actie | Microsoft.DataLakeStore/accounts/write | Een Data Lake Store-account maken of bijwerken. |
> | Actie | Microsoft.DataLakeStore/locations/capability/read | Informatie over de mogelijkheden van een abonnement over het gebruik van data Lake Store. |
> | Actie | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Controleer de beschik baarheid van een Data Lake Store-account naam. |
> | Actie | Microsoft.DataLakeStore/locations/operationResults/read | Resultaat van een Data Lake Store-account bewerking ophalen. |
> | Actie | Microsoft.DataLakeStore/locations/usages/read | Gegevens over quotum gebruik ophalen van een abonnement met behulp van data Lake Store. |
> | Actie | Microsoft.DataLakeStore/operations/read | Beschik bare bewerkingen voor data Lake Store ophalen. |
> | Actie | Microsoft.DataLakeStore/register/action | Registreer het abonnement op Data Lake Store. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DataMigration/locations/operationResults/read | De status ophalen van een langlopende bewerking gerelateerd aan een 202 geaccepteerde reactie |
> | Actie | Microsoft.DataMigration/locations/operationStatuses/read | De status ophalen van een langlopende bewerking gerelateerd aan een 202 geaccepteerde reactie |
> | Actie | Microsoft.DataMigration/register/action | Hiermee wordt het abonnement geregistreerd bij de Azure Database Migration Service Provider |
> | Actie | Microsoft.DataMigration/services/addWorker/action | Voegt een DMS-werk nemer toe aan de beschikbaar-werk nemers van de service |
> | Actie | Microsoft.DataMigration/services/checkStatus/action | Controleren of de service is geïmplementeerd en wordt uitgevoerd |
> | Actie | Microsoft.DataMigration/services/configureWorker/action | Hiermee wordt een DMS-werk nemer geconfigureerd voor de beschikbaar-werk nemers van de service |
> | Actie | Microsoft.DataMigration/services/delete | Hiermee worden een resource en alle onderliggende items verwijderd |
> | Actie | Microsoft.DataMigration/services/projects/accessArtifacts/action | Genereer een URL die kan worden gebruikt om project artefacten op te halen of in te stellen |
> | Actie | Microsoft.DataMigration/services/projects/delete | Hiermee worden een resource en alle onderliggende items verwijderd |
> | Actie | Microsoft.DataMigration/services/projects/files/delete | Hiermee worden een resource en alle onderliggende items verwijderd |
> | Actie | Microsoft.DataMigration/services/projects/files/read | Informatie over resources lezen |
> | Actie | Microsoft.DataMigration/services/projects/files/read/action | Een URL verkrijgen die kan worden gebruikt om de inhoud van het bestand te lezen |
> | Actie | Microsoft.DataMigration/services/projects/files/readWrite/action | Een URL verkrijgen die kan worden gebruikt om de inhoud van het bestand te lezen of te schrijven |
> | Actie | Microsoft.DataMigration/services/projects/files/write | Resources en hun eigenschappen maken of bijwerken |
> | Actie | Microsoft.DataMigration/services/projects/read | Informatie over resources lezen |
> | Actie | Microsoft.DataMigration/services/projects/tasks/cancel/action | De taak annuleren als deze momenteel wordt uitgevoerd |
> | Actie | Microsoft.DataMigration/services/projects/tasks/delete | Hiermee worden een resource en alle onderliggende items verwijderd |
> | Actie | Microsoft.DataMigration/services/projects/tasks/read | Informatie over resources lezen |
> | Actie | Microsoft.DataMigration/services/projects/tasks/write | Taken Azure Database Migration Service taken uitvoeren |
> | Actie | Microsoft.DataMigration/services/projects/write | Taken Azure Database Migration Service taken uitvoeren |
> | Actie | Microsoft.DataMigration/services/read | Informatie over resources lezen |
> | Actie | Microsoft.DataMigration/services/removeWorker/action | Hiermee wordt een DMS-werk nemer verwijderd uit de beschikbaar-werk rollen van de service |
> | Actie | Microsoft.DataMigration/services/serviceTasks/cancel/action | De taak annuleren als deze momenteel wordt uitgevoerd |
> | Actie | Microsoft.DataMigration/services/serviceTasks/delete | Hiermee worden een resource en alle onderliggende items verwijderd |
> | Actie | Microsoft.DataMigration/services/serviceTasks/read | Informatie over resources lezen |
> | Actie | Microsoft.DataMigration/services/serviceTasks/write | Taken Azure Database Migration Service taken uitvoeren |
> | Actie | Microsoft.DataMigration/services/slots/delete | Hiermee worden een resource en alle onderliggende items verwijderd |
> | Actie | Microsoft.DataMigration/services/slots/read | Informatie over resources lezen |
> | Actie | Microsoft.DataMigration/services/slots/write | Resources en hun eigenschappen maken of bijwerken |
> | Actie | Microsoft.DataMigration/services/start/action | Start de DMS-service zodat de migratie opnieuw kan worden uitgevoerd |
> | Actie | Microsoft.DataMigration/services/stop/action | De DMS-service stoppen om de kosten te minimaliseren |
> | Actie | Microsoft.DataMigration/services/updateAgentConfig/action | Hiermee wordt de configuratie van de DMS-agent bijgewerkt met de gegeven waarden. |
> | Actie | Microsoft.DataMigration/services/write | Resources en hun eigenschappen maken of bijwerken |
> | Actie | Microsoft.DataMigration/skus/read | Een lijst met Sku's ophalen die worden ondersteund door DMS-resources. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DBforMariaDB/checkNameAvailability/action | Controleer of de opgegeven server naam beschikbaar is voor het wereld wijd inrichten van een bepaald abonnement. |
> | Actie | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Resultaten van MariaDB-server bewerking retour neren |
> | Actie | Microsoft.DBforMariaDB/locations/operationResults/read | Resultaten van MariaDB server-bewerking retour neren op basis van ResourceGroup |
> | Actie | Microsoft.DBforMariaDB/locations/operationResults/read | Resultaten van MariaDB-server bewerking retour neren |
> | Actie | Microsoft.DBforMariaDB/locations/performanceTiers/read | Hiermee wordt de lijst met prestatie lagen weer gegeven die beschikbaar zijn. |
> | Actie | Micro soft. DBforMariaDB/locaties/privateEndpointConnectionAzureAsyncOperation/lezen | Hiermee wordt het resultaat voor een verbindings bewerking voor een privé-eind punt opgehaald |
> | Actie | Micro soft. DBforMariaDB/locaties/privateEndpointConnectionOperationResults/lezen | Hiermee wordt het resultaat voor een verbindings bewerking voor een privé-eind punt opgehaald |
> | Actie | Micro soft. DBforMariaDB/locaties/privateEndpointConnectionProxyAzureAsyncOperation/lezen | Hiermee wordt het resultaat opgehaald voor een proxy bewerking voor een privé-eindpunt verbinding |
> | Actie | Micro soft. DBforMariaDB/locaties/privateEndpointConnectionProxyOperationResults/lezen | Hiermee wordt het resultaat opgehaald voor een proxy bewerking voor een privé-eindpunt verbinding |
> | Actie | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Retour neer de lijst met resultaten van de server bedreigings detectie. |
> | Actie | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Retour neer de lijst met resultaten van de server bedreigings detectie. |
> | Actie | Micro soft. DBforMariaDB/locaties/serverKeyAzureAsyncOperation/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante server sleutels voor gegevens versleuteling |
> | Actie | Micro soft. DBforMariaDB/locaties/serverKeyOperationResults/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante server sleutels voor gegevens versleuteling |
> | Actie | Microsoft.DBforMariaDB/operations/read | Retour neer de lijst met MariaDB-bewerkingen. |
> | Actie | Microsoft.DBforMariaDB/performanceTiers/read | Hiermee wordt de lijst met prestatie lagen weer gegeven die beschikbaar zijn. |
> | Actie | Microsoft.DBforMariaDB/register/action | MariaDB-resource provider registreren |
> | Actie | Microsoft.DBforMariaDB/servers/administrators/delete | Hiermee verwijdert u een bestaande beheerder van de MariaDB-server. |
> | Actie | Microsoft.DBforMariaDB/servers/administrators/read | Hiermee haalt u een lijst met MariaDB-Server beheerders op. |
> | Actie | Micro soft. DBforMariaDB/servers/beheerders/schrijven | Hiermee wordt de MariaDB-Server beheerder met de opgegeven para meters gemaakt of bijgewerkt. |
> | Actie | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | Een nieuwe actie sessie voor een aanbeveling maken |
> | Actie | Microsoft.DBforMariaDB/servers/advisors/read | De lijst met Advisors retour neren |
> | Actie | Microsoft.DBforMariaDB/servers/advisors/read | Een adviseur retour neren |
> | Actie | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | De lijst met aanbevolen acties retour neren |
> | Actie | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | De lijst met aanbevolen acties retour neren |
> | Actie | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Een aanbevolen actie retour neren |
> | Actie | Microsoft.DBforMariaDB/servers/configurations/read | De lijst met configuraties voor een server retour neren of de eigenschappen voor de opgegeven configuratie ophalen. |
> | Actie | Microsoft.DBforMariaDB/servers/configurations/write | De waarde voor de opgegeven configuratie bijwerken |
> | Actie | Microsoft.DBforMariaDB/servers/databases/delete | Hiermee verwijdert u een bestaande MariaDB-data base. |
> | Actie | Microsoft.DBforMariaDB/servers/databases/read | De lijst met MariaDB-data bases retour neren of de eigenschappen voor de opgegeven Data Base ophalen. |
> | Actie | Microsoft.DBforMariaDB/servers/databases/write | Hiermee maakt u een MariaDB-data base met de opgegeven para meters of werkt u de eigenschappen voor de opgegeven Data Base bij. |
> | Actie | Microsoft.DBforMariaDB/servers/delete | Hiermee verwijdert u een bestaande server. |
> | Actie | Microsoft.DBforMariaDB/servers/firewallRules/delete | Hiermee verwijdert u een bestaande firewall regel. |
> | Actie | Microsoft.DBforMariaDB/servers/firewallRules/read | De lijst met firewall regels voor een server retour neren of de eigenschappen voor de opgegeven firewall regel ophalen. |
> | Actie | Micro soft. DBforMariaDB/servers/firewallRules/schrijven | Hiermee maakt u een firewall regel met de opgegeven para meters of werkt u een bestaande regel bij. |
> | Actie | Micro soft. DBforMariaDB/servers/sleutels/verwijderen | Hiermee verwijdert u een bestaande server sleutel. |
> | Actie | Micro soft. DBforMariaDB/servers/sleutels/lezen | De lijst met Server sleutels retour neren of de eigenschappen voor de opgegeven server sleutel ophalen. |
> | Actie | Micro soft. DBforMariaDB/servers/sleutels/schrijven | Hiermee maakt u een sleutel met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server sleutel bij. |
> | Actie | Microsoft.DBforMariaDB/servers/logFiles/read | De lijst met MariaDB-logboeken weer geven. |
> | Actie | Micro soft. DBforMariaDB/servers/privateEndpointConnectionProxies/verwijderen | Hiermee verwijdert u een bestaande verbindings proxy voor een persoonlijk eind punt |
> | Actie | Micro soft. DBforMariaDB/servers/privateEndpointConnectionProxies/lezen | Hiermee wordt de lijst met particuliere endpoint-verbindings proxy's geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbinding proxy voor het particuliere eind punt. |
> | Actie | Micro soft. DBforMariaDB/servers/privateEndpointConnectionProxies/validate/Action | Hiermee wordt een aanroep voor het maken van een privé-eind punt gevalideerd aan de kant van de NRP |
> | Actie | Micro soft. DBforMariaDB/servers/privateEndpointConnectionProxies/schrijven | Hiermee maakt u een particuliere endpoint-verbindings proxy met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven verbinding proxy van het particuliere eind punt bij. |
> | Actie | Micro soft. DBforMariaDB/servers/privateEndpointConnections/verwijderen | Hiermee verwijdert u een bestaande persoonlijke eindpunt verbinding |
> | Actie | Micro soft. DBforMariaDB/servers/privateEndpointConnections/lezen | Hiermee wordt de lijst met privé-eindpunt verbindingen geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbinding met een privé-eind punt. |
> | Actie | Micro soft. DBforMariaDB/servers/privateEndpointConnections/schrijven | Hiermee wordt een bestaande verbinding met een privé-eind punt goedgekeurd of geweigerd |
> | Actie | Micro soft. DBforMariaDB/servers/privateLinkResources/lezen | De persoonlijke koppelings resources voor de bijbehorende MariaDB-server ophalen |
> | Actie | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee wordt de disagnostic-instelling voor de resource opgehaald |
> | Actie | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Hiermee worden de beschik bare logboeken voor MariaDB-servers opgehaald |
> | Actie | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Typen metrische gegevens die beschikbaar zijn voor data bases retour neren |
> | Actie | Microsoft.DBforMariaDB/servers/queryTexts/action | De tekst voor een lijst met query's retour neren |
> | Actie | Microsoft.DBforMariaDB/servers/queryTexts/action | De tekst van een query retour neren |
> | Actie | Microsoft.DBforMariaDB/servers/read | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Actie | Microsoft.DBforMariaDB/servers/recoverableServers/read | De herstel bare MariaDB-Server gegevens retour neren |
> | Actie | Microsoft.DBforMariaDB/servers/replicas/read | Replica's van een MariaDB-server ophalen |
> | Actie | Microsoft.DBforMariaDB/servers/restart/action | Hiermee wordt een specifieke server opnieuw gestart. |
> | Actie | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Details ophalen van het server Threat Detection-beleid dat op een bepaalde server is geconfigureerd |
> | Actie | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Het server Threat Detection-beleid voor een bepaalde server wijzigen |
> | Actie | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | De lijst met query statistieken voor de meest voorkomende query's retour neren. |
> | Actie | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Een query statistieken retour neren |
> | Actie | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Update configuraties voor de opgegeven server |
> | Actie | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Hiermee verwijdert u een bestaande Virtual Network regel |
> | Actie | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | De lijst met regels voor het virtuele netwerk retour neren of de eigenschappen voor de opgegeven virtuele netwerk regel ophalen. |
> | Actie | Micro soft. DBforMariaDB/servers/virtualNetworkRules/schrijven | Hiermee maakt u een regel voor het virtuele netwerk met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven virtuele-netwerk regel bij. |
> | Actie | Microsoft.DBforMariaDB/servers/waitStatistics/read | Wacht statistieken voor een exemplaar retour neren |
> | Actie | Microsoft.DBforMariaDB/servers/waitStatistics/read | Een wachtende statistiek retour neren |
> | Actie | Microsoft.DBforMariaDB/servers/write | Hiermee maakt u een server met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server bij. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DBforMySQL/checkNameAvailability/action | Controleer of de opgegeven server naam beschikbaar is voor het wereld wijd inrichten van een bepaald abonnement. |
> | Actie | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Resultaten van MySQL-server bewerking retour neren |
> | Actie | Microsoft.DBforMySQL/locations/operationResults/read | Resultaten van de MySQL-server bewerking retour neren op basis van ResourceGroup |
> | Actie | Microsoft.DBforMySQL/locations/operationResults/read | Resultaten van MySQL-server bewerking retour neren |
> | Actie | Microsoft.DBforMySQL/locations/performanceTiers/read | Hiermee wordt de lijst met prestatie lagen weer gegeven die beschikbaar zijn. |
> | Actie | Micro soft. DBforMySQL/locaties/privateEndpointConnectionAzureAsyncOperation/lezen | Hiermee wordt het resultaat voor een verbindings bewerking voor een privé-eind punt opgehaald |
> | Actie | Micro soft. DBforMySQL/locaties/privateEndpointConnectionOperationResults/lezen | Hiermee wordt het resultaat voor een verbindings bewerking voor een privé-eind punt opgehaald |
> | Actie | Micro soft. DBforMySQL/locaties/privateEndpointConnectionProxyAzureAsyncOperation/lezen | Hiermee wordt het resultaat opgehaald voor een proxy bewerking voor een privé-eindpunt verbinding |
> | Actie | Micro soft. DBforMySQL/locaties/privateEndpointConnectionProxyOperationResults/lezen | Hiermee wordt het resultaat opgehaald voor een proxy bewerking voor een privé-eindpunt verbinding |
> | Actie | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Retour neer de lijst met resultaten van de server bedreigings detectie. |
> | Actie | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Retour neer de lijst met resultaten van de server bedreigings detectie. |
> | Actie | Micro soft. DBforMySQL/locaties/serverKeyAzureAsyncOperation/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante server sleutels voor gegevens versleuteling |
> | Actie | Micro soft. DBforMySQL/locaties/serverKeyOperationResults/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante server sleutels voor gegevens versleuteling |
> | Actie | Microsoft.DBforMySQL/operations/read | De lijst met MySQL-bewerkingen retour neren. |
> | Actie | Microsoft.DBforMySQL/performanceTiers/read | Hiermee wordt de lijst met prestatie lagen weer gegeven die beschikbaar zijn. |
> | Actie | Microsoft.DBforMySQL/register/action | MySQL-resource provider registreren |
> | Actie | Microsoft.DBforMySQL/servers/administrators/delete | Hiermee verwijdert u een bestaande beheerder van de MySQL-server. |
> | Actie | Microsoft.DBforMySQL/servers/administrators/read | Hiermee haalt u een lijst met MySQL-Server beheerders op. |
> | Actie | Microsoft.DBforMySQL/servers/administrators/write | Hiermee wordt de MySQL-Server beheerder met de opgegeven para meters gemaakt of bijgewerkt. |
> | Actie | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | Een nieuwe actie sessie voor een aanbeveling maken |
> | Actie | Microsoft.DBforMySQL/servers/advisors/read | De lijst met Advisors retour neren |
> | Actie | Microsoft.DBforMySQL/servers/advisors/read | Een adviseur retour neren |
> | Actie | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | De lijst met aanbevolen acties retour neren |
> | Actie | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | De lijst met aanbevolen acties retour neren |
> | Actie | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Een aanbevolen actie retour neren |
> | Actie | Microsoft.DBforMySQL/servers/configurations/read | De lijst met configuraties voor een server retour neren of de eigenschappen voor de opgegeven configuratie ophalen. |
> | Actie | Microsoft.DBforMySQL/servers/configurations/write | De waarde voor de opgegeven configuratie bijwerken |
> | Actie | Microsoft.DBforMySQL/servers/databases/delete | Hiermee verwijdert u een bestaande MySQL-data base. |
> | Actie | Microsoft.DBforMySQL/servers/databases/read | De lijst met MySQL-data bases retour neren of de eigenschappen voor de opgegeven Data Base ophalen. |
> | Actie | Microsoft.DBforMySQL/servers/databases/write | Hiermee maakt u een MySQL-data base met de opgegeven para meters of werkt u de eigenschappen voor de opgegeven Data Base bij. |
> | Actie | Microsoft.DBforMySQL/servers/delete | Hiermee verwijdert u een bestaande server. |
> | Actie | Microsoft.DBforMySQL/servers/firewallRules/delete | Hiermee verwijdert u een bestaande firewall regel. |
> | Actie | Microsoft.DBforMySQL/servers/firewallRules/read | De lijst met firewall regels voor een server retour neren of de eigenschappen voor de opgegeven firewall regel ophalen. |
> | Actie | Microsoft.DBforMySQL/servers/firewallRules/write | Hiermee maakt u een firewall regel met de opgegeven para meters of werkt u een bestaande regel bij. |
> | Actie | Micro soft. DBforMySQL/servers/sleutels/verwijderen | Hiermee verwijdert u een bestaande server sleutel. |
> | Actie | Micro soft. DBforMySQL/servers/sleutels/lezen | De lijst met Server sleutels retour neren of de eigenschappen voor de opgegeven server sleutel ophalen. |
> | Actie | Micro soft. DBforMySQL/servers/sleutels/schrijven | Hiermee maakt u een sleutel met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server sleutel bij. |
> | Actie | Microsoft.DBforMySQL/servers/logFiles/read | Retour neer de lijst met MySQL-Logboeken. |
> | Actie | Micro soft. DBforMySQL/servers/privateEndpointConnectionProxies/verwijderen | Hiermee verwijdert u een bestaande verbindings proxy voor een persoonlijk eind punt |
> | Actie | Micro soft. DBforMySQL/servers/privateEndpointConnectionProxies/lezen | Hiermee wordt de lijst met particuliere endpoint-verbindings proxy's geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbinding proxy voor het particuliere eind punt. |
> | Actie | Micro soft. DBforMySQL/servers/privateEndpointConnectionProxies/validate/Action | Hiermee wordt een aanroep voor het maken van een privé-eind punt gevalideerd aan de kant van de NRP |
> | Actie | Micro soft. DBforMySQL/servers/privateEndpointConnectionProxies/schrijven | Hiermee maakt u een particuliere endpoint-verbindings proxy met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven verbinding proxy van het particuliere eind punt bij. |
> | Actie | Micro soft. DBforMySQL/servers/privateEndpointConnections/verwijderen | Hiermee verwijdert u een bestaande persoonlijke eindpunt verbinding |
> | Actie | Micro soft. DBforMySQL/servers/privateEndpointConnections/lezen | Hiermee wordt de lijst met privé-eindpunt verbindingen geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbinding met een privé-eind punt. |
> | Actie | Micro soft. DBforMySQL/servers/privateEndpointConnections/schrijven | Hiermee wordt een bestaande verbinding met een privé-eind punt goedgekeurd of geweigerd |
> | Actie | Micro soft. DBforMySQL/servers/privateLinkResources/lezen | De persoonlijke koppelings resources voor de bijbehorende MySQL-server ophalen |
> | Actie | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee wordt de disagnostic-instelling voor de resource opgehaald |
> | Actie | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Hiermee worden de beschik bare logboeken voor MySQL-servers opgehaald |
> | Actie | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Typen metrische gegevens die beschikbaar zijn voor data bases retour neren |
> | Actie | Microsoft.DBforMySQL/servers/queryTexts/action | De tekst voor een lijst met query's retour neren |
> | Actie | Microsoft.DBforMySQL/servers/queryTexts/action | De tekst van een query retour neren |
> | Actie | Microsoft.DBforMySQL/servers/read | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Actie | Microsoft.DBforMySQL/servers/recoverableServers/read | De gegevens van de herstel bare MySQL-server retour neren |
> | Actie | Microsoft.DBforMySQL/servers/replicas/read | Replica's van een MySQL-server ophalen |
> | Actie | Microsoft.DBforMySQL/servers/restart/action | Hiermee wordt een specifieke server opnieuw gestart. |
> | Actie | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Details ophalen van het server Threat Detection-beleid dat op een bepaalde server is geconfigureerd |
> | Actie | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Het server Threat Detection-beleid voor een bepaalde server wijzigen |
> | Actie | Microsoft.DBforMySQL/servers/topQueryStatistics/read | De lijst met query statistieken voor de meest voorkomende query's retour neren. |
> | Actie | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Een query statistieken retour neren |
> | Actie | Microsoft.DBforMySQL/servers/updateConfigurations/action | Update configuraties voor de opgegeven server |
> | Actie | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Hiermee verwijdert u een bestaande Virtual Network regel |
> | Actie | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | De lijst met regels voor het virtuele netwerk retour neren of de eigenschappen voor de opgegeven virtuele netwerk regel ophalen. |
> | Actie | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Hiermee maakt u een regel voor het virtuele netwerk met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven virtuele-netwerk regel bij. |
> | Actie | Microsoft.DBforMySQL/servers/waitStatistics/read | Wacht statistieken voor een exemplaar retour neren |
> | Actie | Microsoft.DBforMySQL/servers/waitStatistics/read | Een wachtende statistiek retour neren |
> | Actie | Microsoft.DBforMySQL/servers/write | Hiermee maakt u een server met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server bij. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DBforPostgreSQL/checkNameAvailability/action | Controleer of de opgegeven server naam beschikbaar is voor het wereld wijd inrichten van een bepaald abonnement. |
> | Actie | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Resultaten van PostgreSQL-server bewerking retour neren |
> | Actie | Microsoft.DBforPostgreSQL/locations/operationResults/read | Resultaten van PostgreSQL server-bewerking retour neren op basis van ResourceGroup |
> | Actie | Microsoft.DBforPostgreSQL/locations/operationResults/read | Resultaten van PostgreSQL-server bewerking retour neren |
> | Actie | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Hiermee wordt de lijst met prestatie lagen weer gegeven die beschikbaar zijn. |
> | Actie | Micro soft. DBforPostgreSQL/locaties/privateEndpointConnectionAzureAsyncOperation/lezen | Hiermee wordt het resultaat voor een verbindings bewerking voor een privé-eind punt opgehaald |
> | Actie | Micro soft. DBforPostgreSQL/locaties/privateEndpointConnectionOperationResults/lezen | Hiermee wordt het resultaat voor een verbindings bewerking voor een privé-eind punt opgehaald |
> | Actie | Micro soft. DBforPostgreSQL/locaties/privateEndpointConnectionProxyAzureAsyncOperation/lezen | Hiermee wordt het resultaat opgehaald voor een proxy bewerking voor een privé-eindpunt verbinding |
> | Actie | Micro soft. DBforPostgreSQL/locaties/privateEndpointConnectionProxyOperationResults/lezen | Hiermee wordt het resultaat opgehaald voor een proxy bewerking voor een privé-eindpunt verbinding |
> | Actie | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Retour neer de lijst met resultaten van de server bedreigings detectie. |
> | Actie | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Retour neer de lijst met resultaten van de server bedreigings detectie. |
> | Actie | Micro soft. DBforPostgreSQL/locaties/serverKeyAzureAsyncOperation/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante server sleutels voor gegevens versleuteling |
> | Actie | Micro soft. DBforPostgreSQL/locaties/serverKeyOperationResults/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante server sleutels voor gegevens versleuteling |
> | Actie | Microsoft.DBforPostgreSQL/operations/read | Retour neer de lijst met PostgreSQL-bewerkingen. |
> | Actie | Microsoft.DBforPostgreSQL/performanceTiers/read | Hiermee wordt de lijst met prestatie lagen weer gegeven die beschikbaar zijn. |
> | Actie | Microsoft.DBforPostgreSQL/register/action | PostgreSQL-resource provider registreren |
> | Actie | Microsoft.DBforPostgreSQL/servers/administrators/delete | Hiermee verwijdert u een bestaande beheerder van de PostgreSQL-server. |
> | Actie | Microsoft.DBforPostgreSQL/servers/administrators/read | Hiermee haalt u een lijst met PostgreSQL-Server beheerders op. |
> | Actie | Microsoft.DBforPostgreSQL/servers/administrators/write | Hiermee wordt de PostgreSQL-Server beheerder met de opgegeven para meters gemaakt of bijgewerkt. |
> | Actie | Microsoft.DBforPostgreSQL/servers/advisors/read | De lijst met Advisors retour neren |
> | Actie | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | De lijst met aanbevolen acties retour neren |
> | Actie | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Aanbevelingen doen |
> | Actie | Microsoft.DBforPostgreSQL/servers/configurations/read | De lijst met configuraties voor een server retour neren of de eigenschappen voor de opgegeven configuratie ophalen. |
> | Actie | Microsoft.DBforPostgreSQL/servers/configurations/write | De waarde voor de opgegeven configuratie bijwerken |
> | Actie | Microsoft.DBforPostgreSQL/servers/databases/delete | Hiermee verwijdert u een bestaande PostgreSQL-data base. |
> | Actie | Microsoft.DBforPostgreSQL/servers/databases/read | De lijst met PostgreSQL-data bases retour neren of de eigenschappen voor de opgegeven Data Base ophalen. |
> | Actie | Microsoft.DBforPostgreSQL/servers/databases/write | Hiermee maakt u een PostgreSQL-data base met de opgegeven para meters of werkt u de eigenschappen voor de opgegeven Data Base bij. |
> | Actie | Microsoft.DBforPostgreSQL/servers/delete | Hiermee verwijdert u een bestaande server. |
> | Actie | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Hiermee verwijdert u een bestaande firewall regel. |
> | Actie | Microsoft.DBforPostgreSQL/servers/firewallRules/read | De lijst met firewall regels voor een server retour neren of de eigenschappen voor de opgegeven firewall regel ophalen. |
> | Actie | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Hiermee maakt u een firewall regel met de opgegeven para meters of werkt u een bestaande regel bij. |
> | Actie | Micro soft. DBforPostgreSQL/servers/sleutels/verwijderen | Hiermee verwijdert u een bestaande server sleutel. |
> | Actie | Micro soft. DBforPostgreSQL/servers/sleutels/lezen | De lijst met Server sleutels retour neren of de eigenschappen voor de opgegeven server sleutel ophalen. |
> | Actie | Micro soft. DBforPostgreSQL/servers/sleutels/schrijven | Hiermee maakt u een sleutel met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server sleutel bij. |
> | Actie | Microsoft.DBforPostgreSQL/servers/logFiles/read | De lijst met PostgreSQL-logboeken weer geven. |
> | Actie | Micro soft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/verwijderen | Hiermee verwijdert u een bestaande verbindings proxy voor een persoonlijk eind punt |
> | Actie | Micro soft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/lezen | Hiermee wordt de lijst met particuliere endpoint-verbindings proxy's geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbinding proxy voor het particuliere eind punt. |
> | Actie | Micro soft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/Action | Hiermee wordt een aanroep voor het maken van een privé-eind punt gevalideerd aan de kant van de NRP |
> | Actie | Micro soft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/schrijven | Hiermee maakt u een particuliere endpoint-verbindings proxy met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven verbinding proxy van het particuliere eind punt bij. |
> | Actie | Micro soft. DBforPostgreSQL/servers/privateEndpointConnections/verwijderen | Hiermee verwijdert u een bestaande persoonlijke eindpunt verbinding |
> | Actie | Micro soft. DBforPostgreSQL/servers/privateEndpointConnections/lezen | Hiermee wordt de lijst met privé-eindpunt verbindingen geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbinding met een privé-eind punt. |
> | Actie | Micro soft. DBforPostgreSQL/servers/privateEndpointConnections/schrijven | Hiermee wordt een bestaande verbinding met een privé-eind punt goedgekeurd of geweigerd |
> | Actie | Micro soft. DBforPostgreSQL/servers/privateLinkResources/lezen | De persoonlijke koppelings resources voor de bijbehorende PostgreSQL-server ophalen |
> | Actie | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee wordt de disagnostic-instelling voor de resource opgehaald |
> | Actie | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Hiermee worden de beschik bare logboeken voor post gres-servers opgehaald |
> | Actie | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Typen metrische gegevens die beschikbaar zijn voor data bases retour neren |
> | Actie | Microsoft.DBforPostgreSQL/servers/queryTexts/action | De tekst van een query retour neren |
> | Actie | Microsoft.DBforPostgreSQL/servers/queryTexts/read | De tekst voor een lijst met query's retour neren |
> | Actie | Microsoft.DBforPostgreSQL/servers/read | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Actie | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | De herstel bare PostgreSQL-Server gegevens retour neren |
> | Actie | Microsoft.DBforPostgreSQL/servers/replicas/read | Replica's van een PostgreSQL-server ophalen |
> | Actie | Microsoft.DBforPostgreSQL/servers/restart/action | Hiermee wordt een specifieke server opnieuw gestart. |
> | Actie | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Details ophalen van het server Threat Detection-beleid dat op een bepaalde server is geconfigureerd |
> | Actie | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Het server Threat Detection-beleid voor een bepaalde server wijzigen |
> | Actie | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | De lijst met query statistieken voor de meest voorkomende query's retour neren. |
> | Actie | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Update configuraties voor de opgegeven server |
> | Actie | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Hiermee verwijdert u een bestaande Virtual Network regel |
> | Actie | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | De lijst met regels voor het virtuele netwerk retour neren of de eigenschappen voor de opgegeven virtuele netwerk regel ophalen. |
> | Actie | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Hiermee maakt u een regel voor het virtuele netwerk met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven virtuele-netwerk regel bij. |
> | Actie | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Wacht statistieken voor een exemplaar retour neren |
> | Actie | Microsoft.DBforPostgreSQL/servers/write | Hiermee maakt u een server met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server bij. |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/configurations/read | De lijst met configuraties voor een server retour neren of de eigenschappen voor de opgegeven configuratie ophalen. |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/configurations/write | De waarde voor de opgegeven configuratie bijwerken |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/delete | Hiermee verwijdert u een bestaande server. |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Hiermee verwijdert u een bestaande firewall regel. |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | De lijst met firewall regels voor een server retour neren of de eigenschappen voor de opgegeven firewall regel ophalen. |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Hiermee maakt u een firewall regel met de opgegeven para meters of werkt u een bestaande regel bij. |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee wordt de disagnostic-instelling voor de resource opgehaald |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Hiermee worden de beschik bare logboeken voor post gres-servers opgehaald |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Typen metrische gegevens die beschikbaar zijn voor data bases retour neren |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/read | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Update configuraties voor de opgegeven server |
> | Actie | Microsoft.DBforPostgreSQL/serversv2/write | Hiermee maakt u een server met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server bij. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. apparaten/accounts/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Actie | Micro soft. apparaten/accounts/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Micro soft. apparaten/accounts/logDefinitions/lezen | Hiermee worden de beschik bare logboek definities voor de IotHub-service opgehaald |
> | Actie | Micro soft. apparaten/accounts/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor de IotHub-service |
> | Actie | Microsoft.Devices/checkNameAvailability/Action | Controleren of de naam van de IotHub beschikbaar is |
> | Actie | Microsoft.Devices/checkNameAvailability/Action | Controleren of de naam van de IotHub beschikbaar is |
> | Actie | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Controleer of de naam van de inrichtings service beschikbaar is |
> | Actie | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Controleer of de naam van de inrichtings service beschikbaar is |
> | Actie | Micro soft. devices/digitalTwins/verwijderen | Een bestaand Digital Apparaatdubbels-account en alle onderliggende items verwijderen |
> | Actie | Micro soft. devices/digitalTwins/operationresults/lezen | De status van een bewerking ophalen voor een Digital Apparaatdubbels-account |
> | Actie | Micro soft. devices/digitalTwins/lezen | Hiermee wordt een lijst opgehaald met de Digital Apparaatdubbels-accounts die zijn gekoppeld aan een abonnement |
> | Actie | Micro soft. devices/digitalTwins/sku's/lezen | Een lijst met geldige Sku's voor Digital Apparaatdubbels-accounts ophalen |
> | Actie | Micro soft. devices/digitalTwins/schrijven | Een nieuw Apparaatdubbels-account maken |
> | Actie | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Actie | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Micro soft. devices/elasticPools/eventGridFilters/verwijderen | Hiermee wordt de Elastische pool Event Grid filter verwijderd |
> | Actie | Micro soft. devices/elasticPools/eventGridFilters/lezen | Hiermee wordt de Elastische pool Event Grid filter opgehaald |
> | Actie | Micro soft. devices/elasticPools/eventGridFilters/schrijven | Nieuwe Event Grid filter bestaande Elastische pool maken of bijwerken |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Certificaat verwijderen |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Verificatie code genereren |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Hiermee wordt het certificaat opgehaald |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Certificaat bron verifiëren |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Certificaat maken of bijwerken |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/Delete | De IotHub-Tenant resource verwijderen |
> | Actie | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Actie | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | EventHub Consumer Group verwijderen |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | EventHub Consumer-groep (en) ophalen |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | EventHub-Consumer groep maken |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Apparaten exporteren |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Hiermee wordt de resource voor IotHub Tenant statistieken opgehaald |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Apparaten importeren |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Hiermee wordt de IotHub-Tenant sleutel opgehaald |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Verzonden details van de taak (s) die op de opgegeven IotHub zijn ingediend |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Hiermee worden IotHub-Tenant sleutels opgehaald |
> | Actie | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Hiermee worden de beschik bare logboek definities voor de IotHub-service opgehaald |
> | Actie | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor de IotHub-service |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Quotum metrieken ophalen |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/Read | Hiermee wordt de IotHub-Tenant resource opgehaald |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Een bericht voor alle bestaande routes testen |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Een bericht testen op basis van een gegeven test route |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Hiermee wordt de status van alle routerings eindpunten voor een IotHub opgehaald |
> | Actie | Micro soft. devices/elasticPools/iotHubTenants/securitySettings/operationResults/lezen | Het resultaat van de asynchrone put-bewerking voor IOT Tenant hub SecuritySettings ophalen |
> | Actie | Micro soft. devices/elasticPools/iotHubTenants/securitySettings/lezen | De Azure Security Center-instellingen ophalen voor IOT-Tenant-hub |
> | Actie | Micro soft. devices/elasticPools/iotHubTenants/securitySettings/write | De Azure Security Center-instellingen op IOT-Tenant-hub bijwerken |
> | Actie | Microsoft.Devices/elasticPools/iotHubTenants/Write | De IotHub-Tenant resource maken of bijwerken |
> | Actie | Microsoft.Devices/ElasticPools/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor de IotHub-service |
> | Actie | Microsoft.Devices/iotHubs/certificates/Delete | Certificaat verwijderen |
> | Actie | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Verificatie code genereren |
> | Actie | Microsoft.Devices/iotHubs/certificates/Read | Hiermee wordt het certificaat opgehaald |
> | Actie | Microsoft.Devices/iotHubs/certificates/verify/Action | Certificaat bron verifiëren |
> | Actie | Microsoft.Devices/iotHubs/certificates/Write | Certificaat maken of bijwerken |
> | Actie | Microsoft.Devices/iotHubs/Delete | IotHub-resource verwijderen |
> | Actie | Microsoft.Devices/IotHubs/diagnosticSettings/read | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Actie | Microsoft.Devices/IotHubs/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Hiermee wordt het Event Grid filter verwijderd |
> | Actie | Microsoft.Devices/iotHubs/eventGridFilters/Read | Hiermee wordt het Event Grid filter opgehaald |
> | Actie | Microsoft.Devices/iotHubs/eventGridFilters/Write | Een nieuw Event Grid filter maken of een bestaand item bijwerken |
> | Actie | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | EventHub Consumer Group verwijderen |
> | Actie | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | EventHub Consumer-groep (en) ophalen |
> | Actie | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | EventHub-Consumer groep maken |
> | Actie | Microsoft.Devices/iotHubs/exportDevices/Action | Apparaten exporteren |
> | Actie | Microsoft.Devices/iotHubs/importDevices/Action | Apparaten importeren |
> | Actie | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | De IotHub-sleutel voor de opgegeven naam ophalen |
> | Actie | Microsoft.Devices/iotHubs/iotHubStats/Read | IotHub-statistieken ophalen |
> | Actie | Microsoft.Devices/iotHubs/jobs/Read | Verzonden details van de taak (s) die op de opgegeven IotHub zijn ingediend |
> | Actie | Microsoft.Devices/iotHubs/listkeys/Action | Alle IotHub-sleutels ophalen |
> | Actie | Microsoft.Devices/IotHubs/logDefinitions/read | Hiermee worden de beschik bare logboek definities voor de IotHub-service opgehaald |
> | Actie | Microsoft.Devices/IotHubs/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor de IotHub-service |
> | Actie | Microsoft.Devices/iotHubs/operationresults/Read | Resultaat van bewerking ophalen (verouderde API) |
> | Actie | Microsoft.Devices/iotHubs/quotaMetrics/Read | Quotum metrieken ophalen |
> | Actie | Microsoft.Devices/iotHubs/Read | Hiermee worden de IotHub-resource (s) opgehaald |
> | Actie | Microsoft.Devices/iotHubs/routing/$testall/Action | Een bericht voor alle bestaande routes testen |
> | Actie | Microsoft.Devices/iotHubs/routing/$testnew/Action | Een bericht testen op basis van een gegeven test route |
> | Actie | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Hiermee wordt de status van alle routerings eindpunten voor een IotHub opgehaald |
> | Actie | Micro soft. devices/iotHubs/securitySettings/operationResults/lezen | Het resultaat van de asynchrone put-bewerking voor IOT hub-SecuritySettings ophalen |
> | Actie | Micro soft. devices/iotHubs/securitySettings/lezen | De Azure Security Center-instellingen voor IOT hub ophalen |
> | Actie | Micro soft. devices/iotHubs/securitySettings/schrijven | De Azure Security Center-instellingen in IOT hub bijwerken |
> | Actie | Microsoft.Devices/iotHubs/skus/Read | Geldige IotHub-Sku's ophalen |
> | Actie | Microsoft.Devices/iotHubs/Write | IotHub-resource maken of bijwerken |
> | Actie | Micro soft. apparaten/locaties/operationresults/lezen | Resultaat van op locatie gebaseerde bewerking ophalen |
> | Actie | Micro soft. devices/operationresults/lezen | Resultaat van bewerking ophalen |
> | Actie | Microsoft.Devices/operations/Read | Alle resource provider-bewerkingen ophalen |
> | Actie | Microsoft.Devices/provisioningServices/certificates/Delete | Certificaat verwijderen |
> | Actie | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Verificatie code genereren |
> | Actie | Microsoft.Devices/provisioningServices/certificates/Read | Hiermee wordt het certificaat opgehaald |
> | Actie | Microsoft.Devices/provisioningServices/certificates/verify/Action | Certificaat bron verifiëren |
> | Actie | Microsoft.Devices/provisioningServices/certificates/Write | Certificaat maken of bijwerken |
> | Actie | Microsoft.Devices/provisioningServices/Delete | IotDps-resource verwijderen |
> | Actie | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Actie | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Microsoft.Devices/provisioningServices/keys/listkeys/Action | IotDps-sleutels voor sleutel naam ophalen |
> | Actie | Microsoft.Devices/provisioningServices/listkeys/Action | Alle IotDps-sleutels ophalen |
> | Actie | Microsoft.Devices/provisioningServices/logDefinitions/read | Hiermee worden de beschik bare logboek definities voor de inrichtings service opgehaald |
> | Actie | Microsoft.Devices/provisioningServices/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor de inrichtings service |
> | Actie | Microsoft.Devices/provisioningServices/operationresults/Read | Resultaat van DPS-bewerking ophalen |
> | Actie | Microsoft.Devices/provisioningServices/Read | IotDps-resource ophalen |
> | Actie | Microsoft.Devices/provisioningServices/skus/Read | Geldige IotDps-Sku's ophalen |
> | Actie | Microsoft.Devices/provisioningServices/Write | IotDps-resource maken |
> | Actie | Microsoft.Devices/register/action | Het abonnement voor de IotHub-resource provider registreren en het maken van IotHub-resources inschakelen |
> | Actie | Microsoft.Devices/usages/Read | Details van het abonnements gebruik voor deze provider ophalen. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DevSpaces/controllers/delete | Azure dev Spaces-controller en dataplane-Services verwijderen |
> | Actie | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Verbindings details weer geven voor de infra structuur van de Azure dev Space-controller |
> | Actie | Microsoft.DevSpaces/controllers/read | Eigenschappen van de controller van Azure dev Spaces lezen |
> | Actie | Microsoft.DevSpaces/controllers/write | Eigenschappen van een Azure dev Space-controller maken of bijwerken |
> | Actie | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Bestaande controller toewijzing voor een container host controleren |
> | Actie | Microsoft.DevSpaces/locations/operationresults/read | Status lezen voor een asynchrone bewerking |
> | Actie | Microsoft.DevSpaces/register/action | Micro soft dev Spaces resource provider registreren bij een abonnement |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DevTestLab/labCenters/delete | Lab Centers verwijderen. |
> | Actie | Microsoft.DevTestLab/labCenters/read | Lab Centers lezen. |
> | Actie | Microsoft.DevTestLab/labCenters/write | Lab Centers toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Lees Azure Resource Manager-sjablonen. |
> | Actie | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Hiermee wordt een Azure Resource Manager-sjabloon gegenereerd voor het opgegeven artefact, worden de vereiste bestanden geüpload naar een opslag account en wordt het gegenereerde artefact gevalideerd. |
> | Actie | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Artefacten lezen. |
> | Actie | Microsoft.DevTestLab/labs/artifactSources/delete | Artefact bronnen verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/artifactSources/read | Artefact bronnen lezen. |
> | Actie | Microsoft.DevTestLab/labs/artifactSources/write | Artefact bronnen toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Claim een wille keurig claim bare virtuele machine in het lab. |
> | Actie | Microsoft.DevTestLab/labs/costs/read | Lees kosten. |
> | Actie | Microsoft.DevTestLab/labs/costs/write | Kosten toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/CreateEnvironment/action | Maak virtuele machines in een lab. |
> | Actie | Microsoft.DevTestLab/labs/customImages/delete | Aangepaste installatie kopieën verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/customImages/read | Aangepaste installatie kopieën lezen. |
> | Actie | Microsoft.DevTestLab/labs/customImages/write | Aangepaste installatie kopieën toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/delete | Labs verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Zorg ervoor dat de huidige gebruiker een geldig profiel in het lab heeft. |
> | Actie | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Hiermee exporteert u het resource gebruik van de test omgeving naar een opslag account |
> | Actie | Microsoft.DevTestLab/labs/formulas/delete | Formules verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/formulas/read | Formules lezen. |
> | Actie | Microsoft.DevTestLab/labs/formulas/write | Formules toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/galleryImages/read | Galerie afbeeldingen lezen. |
> | Actie | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Genereer een URI voor het uploaden van aangepaste schijf installatie kopieën naar een lab. |
> | Actie | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Een virtuele machine importeren in een ander lab. |
> | Actie | Microsoft.DevTestLab/labs/ListVhds/action | Schijf installatie kopieën weer geven die beschikbaar zijn voor het maken van aangepaste installatie kopieën. |
> | Actie | Microsoft.DevTestLab/labs/notificationChannels/delete | Meldings kanalen verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Een melding naar het meegeleverde kanaal verzenden. |
> | Actie | Microsoft.DevTestLab/labs/notificationChannels/read | Lees meldings kanalen. |
> | Actie | Microsoft.DevTestLab/labs/notificationChannels/write | Meldings kanalen toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Evalueert het lab-beleid. |
> | Actie | Microsoft.DevTestLab/labs/policySets/policies/delete | Beleids regels verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/policySets/policies/read | Beleid lezen. |
> | Actie | Microsoft.DevTestLab/labs/policySets/policies/write | Beleids regels toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/policySets/read | Beleids sets lezen. |
> | Actie | Microsoft.DevTestLab/labs/read | Lees Labs. |
> | Actie | Microsoft.DevTestLab/labs/schedules/delete | Schema's verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/schedules/Execute/action | Een schema uitvoeren. |
> | Actie | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Een lijst met alle toepasselijke schema's |
> | Actie | Microsoft.DevTestLab/labs/schedules/read | Planningen lezen. |
> | Actie | Microsoft.DevTestLab/labs/schedules/write | Schema's toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/serviceRunners/delete | Verwijder uitlopers van de service. |
> | Actie | Microsoft.DevTestLab/labs/serviceRunners/read | Lees service-uitlopers. |
> | Actie | Microsoft.DevTestLab/labs/serviceRunners/write | Het toevoegen of wijzigen van service lopers. |
> | Actie | Microsoft.DevTestLab/labs/sharedGalleries/delete | Gedeelde galerieën verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/sharedGalleries/read | Gedeelde galerieën lezen. |
> | Actie | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Gedeelde installatie kopieën verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Gedeelde installatie kopieën lezen. |
> | Actie | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Gedeelde installatie kopieën toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/sharedGalleries/write | Gedeelde galerieën toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/users/delete | Gebruikers profielen verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/users/disks/Attach/action | Koppel en maak de lease van de schijf aan de virtuele machine. |
> | Actie | Microsoft.DevTestLab/labs/users/disks/delete | Schijven verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/users/disks/Detach/action | Ontkoppel en Verbreek de lease van de schijf die aan de virtuele machine is gekoppeld. |
> | Actie | Microsoft.DevTestLab/labs/users/disks/read | Lees schijven. |
> | Actie | Microsoft.DevTestLab/labs/users/disks/write | Schijven toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/users/environments/delete | Omgevingen verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/users/environments/read | Omgevingen lezen. |
> | Actie | Microsoft.DevTestLab/labs/users/environments/write | Omgevingen toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/users/read | Gebruikers profielen lezen. |
> | Actie | Microsoft.DevTestLab/labs/users/secrets/delete | Verwijder geheimen. |
> | Actie | Microsoft.DevTestLab/labs/users/secrets/read | Lees geheimen. |
> | Actie | Microsoft.DevTestLab/labs/users/secrets/write | Geheimen toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Service fabrics verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Hier worden de toepasselijke start-en stop schema's vermeld, indien van toepassing. |
> | Actie | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Lees service fabrics. |
> | Actie | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Schema's verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Een schema uitvoeren. |
> | Actie | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Planningen lezen. |
> | Actie | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Schema's toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Start een service Fabric. |
> | Actie | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Een service Fabric stoppen |
> | Actie | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Service fabrics toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/users/write | Gebruikers profielen toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Een nieuwe of bestaande gegevens schijf koppelen aan een virtuele machine. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Artefacten Toep assen op de virtuele machine. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Eigenaar worden van een bestaande virtuele machine |
> | Actie | Micro soft. DevTestLab/Labs/informatie/ClearArtifactResults/actie | Hiermee wist u de artefact resultaten van de virtuele machine. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/delete | Virtuele machines verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Ontkoppel de opgegeven schijf van de virtuele machine. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Hiermee wordt een teken reeks opgehaald waarmee de inhoud van het RDP-bestand voor de virtuele machine wordt aangeduid |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Hier worden de toepasselijke start-en stop schema's vermeld, indien van toepassing. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/read | Virtuele machines lezen. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Een virtuele machine opnieuw implementeren |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Wijzig de grootte van de virtuele machine. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Start een virtuele machine opnieuw op. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Schema's verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Een schema uitvoeren. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Planningen lezen. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Schema's toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Start een virtuele machine. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Een virtuele machine stoppen |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Hiermee worden alle gegevens schijven die zijn gekoppeld aan de virtuele machine, overgedragen aan de huidige gebruiker. |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Eigendom van een bestaande virtuele machine vrijgeven |
> | Actie | Microsoft.DevTestLab/labs/virtualMachines/write | Virtuele machines toevoegen of wijzigen. |
> | Actie | Micro soft. DevTestLab/Labs/virtualNetworks/bastionHosts/verwijderen | Verwijder bastionhosts. |
> | Actie | Micro soft. DevTestLab/Labs/virtualNetworks/bastionHosts/lezen | Lees bastionhosts. |
> | Actie | Micro soft. DevTestLab/Labs/virtualNetworks/bastionHosts/schrijven | Bastionhosts toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/virtualNetworks/delete | Virtuele netwerken verwijderen. |
> | Actie | Microsoft.DevTestLab/labs/virtualNetworks/read | Virtuele netwerken lezen. |
> | Actie | Microsoft.DevTestLab/labs/virtualNetworks/write | Virtuele netwerken toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/vmPools/delete | Verwijder groepen van virtuele machines. |
> | Actie | Microsoft.DevTestLab/labs/vmPools/read | Virtuele-machine Pools lezen. |
> | Actie | Microsoft.DevTestLab/labs/vmPools/write | Virtuele-machine Pools toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/labs/write | Labs toevoegen of wijzigen. |
> | Actie | Microsoft.DevTestLab/locations/operations/read | Lees bewerkingen. |
> | Actie | Microsoft.DevTestLab/register/action | Hiermee wordt het abonnement geregistreerd |
> | Actie | Microsoft.DevTestLab/schedules/delete | Schema's verwijderen. |
> | Actie | Microsoft.DevTestLab/schedules/Execute/action | Een schema uitvoeren. |
> | Actie | Microsoft.DevTestLab/schedules/read | Planningen lezen. |
> | Actie | Microsoft.DevTestLab/schedules/Retarget/action | Hiermee wordt de doel resource-id van een planning bijgewerkt. |
> | Actie | Microsoft.DevTestLab/schedules/write | Schema's toevoegen of wijzigen. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DocumentDB/databaseAccountNames/read | Hiermee wordt gecontroleerd op Beschik baarheid van namen. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Een verzameling verwijderen. Alleen van toepassing op API-typen: MongoDb. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: MongoDb. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Een verzameling lezen of alle verzamelingen weer geven. Alleen van toepassing op API-typen: MongoDb. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: MongoDb. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | Lees de door Voer van een verzameling. Alleen van toepassing op API-typen: MongoDb. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | De door Voer van een verzameling bijwerken. Alleen van toepassing op API-typen: MongoDb. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Een verzameling maken of bijwerken. Alleen van toepassing op API-typen: MongoDb. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Een container verwijderen. Alleen van toepassing op API-typen: ' SQL '. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: ' SQL '. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Een container lezen of alle containers weer geven. Alleen van toepassing op API-typen: ' SQL '. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: ' SQL '. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | Lees een container doorvoer. Alleen van toepassing op API-typen: ' SQL '. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Een container doorvoer bijwerken. Alleen van toepassing op API-typen: ' SQL '. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Een container maken of bijwerken. Alleen van toepassing op API-typen: ' SQL '. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Een Data Base verwijderen. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Een grafiek verwijderen. Alleen van toepassing op API-typen: gremlin'. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: gremlin'. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Een grafiek lezen of alle grafieken weer geven. Alleen van toepassing op API-typen: gremlin'. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: gremlin'. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Lees een grafiek doorvoer. Alleen van toepassing op API-typen: gremlin'. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Een grafiek doorvoer bijwerken. Alleen van toepassing op API-typen: gremlin'. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Een grafiek maken of bijwerken. Alleen van toepassing op API-typen: gremlin'. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Een Data Base lezen of alle data bases weer geven. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Lees de door Voer van een Data Base. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Een Data Base-door Voer bijwerken. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Een database maken. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Een spatie verwijderen. Alleen van toepassing op API-typen: Cassandra. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: Cassandra. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Lees een spatie of geef alle spatie ruimten weer. Alleen van toepassing op API-typen: Cassandra. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: Cassandra. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Lees de door Voer van een spatie. Alleen van toepassing op API-typen: Cassandra. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Een door Voer van een spatie bijwerken. Alleen van toepassing op API-typen: Cassandra. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Een tabel verwijderen. Alleen van toepassing op API-typen: Cassandra. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: Cassandra. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Een tabel lezen of alle tabellen in een lijst weer geven. Alleen van toepassing op API-typen: Cassandra. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: Cassandra. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Een tabel doorvoer lezen. Alleen van toepassing op API-typen: Cassandra. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Een tabel doorvoer bijwerken. Alleen van toepassing op API-typen: Cassandra. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Een tabel maken of bijwerken. Alleen van toepassing op API-typen: Cassandra. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Maak een spatie. Alleen van toepassing op API-typen: Cassandra. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Een tabel verwijderen. Alleen van toepassing op API-typen: Table. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: Table. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Een tabel lezen of alle tabellen in een lijst weer geven. Alleen van toepassing op API-typen: Table. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: Table. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Een tabel doorvoer lezen. Alleen van toepassing op API-typen: Table. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Een tabel doorvoer bijwerken. Alleen van toepassing op API-typen: Table. Alleen van toepassing op de instellings typen: door voer. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Een tabel maken of bijwerken. Alleen van toepassing op API-typen: Table. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/backup/action | Een aanvraag indienen voor het configureren van de back-up |
> | Actie | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Resource groep van een database account wijzigen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Hiermee worden de metrische definities van de verzameling gelezen. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Hiermee worden de metrische gegevens van de verzameling gelezen. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Data Base-account partitie sleutel niveau meet waarden lezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Metrische gegevens van het partitie niveau voor het database account lezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Database account partities in een verzameling lezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Het gebruik van het database account op partitie niveau lezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Hiermee worden de verzamelings gebruik gelezen. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Hiermee worden de metrische definities van de data base gelezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Hiermee worden de metrische gegevens van de data base gelezen. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Hiermee worden de database gebruik gelezen. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/delete | Hiermee verwijdert u de database accounts. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Wijzig de prioriteiten van de failover van regio's van een database account. Dit wordt gebruikt voor het uitvoeren van een hand matige failover-bewerking |
> | Actie | Micro soft. DocumentDB/databaseAccounts/getBackupPolicy/Action | Het back-upbeleid van het database account ophalen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | De verbindings reeksen ophalen voor een database account |
> | Actie | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Sleutels van een database account weer geven |
> | Actie | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Hiermee worden de definities van metrische gegevens van het database account gelezen. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/metrics/read | Hiermee worden de metrische gegevens van het database account gelezen. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Offline een regio van een database account.  |
> | Actie | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online een regio van een database account. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/operationResults/read | De status van de asynchrone bewerking lezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Metrische gegevens over latentie lezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/percentile/read | Percentielen van replicatie latentie lezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Latentie-metrische gegevens lezen voor een specifieke bron-en doel regio |
> | Actie | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Metrische gegevens over latentie voor een specifieke doel regio lezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Een particuliere endpoint-verbindings proxy van het database account verwijderen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/read | Lees status van asynchrone bewerking voor verbindings proxy van privé-eind punt |
> | Actie | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Een particuliere endpoint-verbindings proxy van het database account lezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Een particuliere endpoint-verbindings proxy van het database account valideren |
> | Actie | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Een particuliere endpoint-verbindings proxy van het database account maken of bijwerken |
> | Actie | Micro soft. DocumentDB/databaseAccounts/privateEndpointConnections/verwijderen | Een persoonlijke eindpunt verbinding van een database account verwijderen |
> | Actie | Micro soft. DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/lezen | Lees status van asynchrone bewerking privateEndpointConnenctions |
> | Actie | Micro soft. DocumentDB/databaseAccounts/privateEndpointConnections/lezen | Een verbinding met een privé-eind punt lezen of alle verbindingen met het privé-eind punt van een database account weer geven |
> | Actie | Micro soft. DocumentDB/databaseAccounts/privateEndpointConnections/write | Een persoonlijke eindpunt verbinding van een database account maken of bijwerken |
> | Actie | Micro soft. DocumentDB/databaseAccounts/privateLinkResources/lezen | Een persoonlijke koppelings bron lezen of alle persoonlijke koppelings resources van een database account weer geven |
> | Actie | Microsoft.DocumentDB/databaseAccounts/read | Hiermee leest u een database account. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Hiermee wordt de alleen-lezen sleutels van het database account gelezen. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Hiermee wordt de alleen-lezen sleutels van het database account gelezen. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Sleutels van een database account draaien |
> | Actie | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Leest de metrische gegevens over de verzameling van regio's. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Regionale data base-account partitie sleutel niveau metrieken lezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Regionale data base-account gegevens voor partitie niveau lezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Regionale database account partities in een verzameling lezen |
> | Actie | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Hiermee worden de metrische gegevens van de regio en het database account gelezen. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/restore/action | Een herstel aanvraag verzenden |
> | Actie | Microsoft.DocumentDB/databaseAccounts/usages/read | Hiermee worden de gebruiks gegevens van het database account gelezen. |
> | Actie | Microsoft.DocumentDB/databaseAccounts/write | Een database account bijwerken. |
> | Actie | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Hiermee wordt aan micro soft. DocumentDB door gemeld dat VirtualNetwork of subnet wordt verwijderd |
> | Actie | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Lees status van asynchrone bewerking deleteVirtualNetworkOrSubnets |
> | Actie | Microsoft.DocumentDB/locations/operationsStatus/read | Hiermee wordt de status van asynchrone bewerkingen gelezen |
> | Actie | Microsoft.DocumentDB/operationResults/read | De status van de asynchrone bewerking lezen |
> | Actie | Microsoft.DocumentDB/operations/read | Lees bewerkingen die beschikbaar zijn voor de micro soft-DocumentDB  |
> | Actie | Microsoft.DocumentDB/register/action |  De resource provider van micro soft DocumentDB voor het abonnement registreren |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.DomainRegistration/checkDomainAvailability/Action | Controleren of een domein beschikbaar is voor aankoop |
> | Actie | Microsoft.DomainRegistration/domains/Delete | Een bestaand domein verwijderen. |
> | Actie | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Eigendoms-id verwijderen |
> | Actie | Micro soft. DomainRegistration/domains/domainownershipidentifiers/lezen | Eigendoms-id's weer geven |
> | Actie | Micro soft. DomainRegistration/domains/domainownershipidentifiers/lezen | Eigendoms-id ophalen |
> | Actie | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Id maken of bijwerken |
> | Actie | Microsoft.DomainRegistration/domains/operationresults/Read | Een domein bewerking ophalen |
> | Actie | Microsoft.DomainRegistration/domains/Read | De lijst met domeinen ophalen |
> | Actie | Microsoft.DomainRegistration/domains/Read | Domein ophalen |
> | Actie | Microsoft.DomainRegistration/domains/renew/Action | Een bestaand domein vernieuwen. |
> | Actie | Microsoft.DomainRegistration/domains/Write | Een nieuw domein toevoegen of een bestaande bijwerken |
> | Actie | Microsoft.DomainRegistration/generateSsoRequest/Action | Genereer een aanvraag voor het aanmelden bij het Domain Control Center. |
> | Actie | Microsoft.DomainRegistration/listDomainRecommendations/Action | De aanbevelingen van het lijst domein ophalen op basis van tref woorden |
> | Actie | Microsoft.DomainRegistration/operations/Read | Alle bewerkingen van de app service-domein registratie weer geven |
> | Actie | Microsoft.DomainRegistration/register/action | De resource provider van micro soft-domeinen voor het abonnement registreren |
> | Actie | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Actie voor lijst overeenkomst |
> | Actie | Microsoft.DomainRegistration/topLevelDomains/Read | Toplevel-domeinen ophalen |
> | Actie | Microsoft.DomainRegistration/topLevelDomains/Read | Toplevel-domein ophalen |
> | Actie | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Het domein aankoop object valideren zonder dit te verzenden |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.EventGrid/domains/delete | Een domein verwijderen |
> | Actie | Microsoft.EventGrid/domains/listKeys/action | Sleutels voor een domein weer geven |
> | Actie | Micro soft. EventGrid/domains/providers/micro soft. Insights/logDefinitions/lezen | Toegang tot Diagnostische logboeken toestaan |
> | Actie | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens voor domeinen opgehaald |
> | Actie | Microsoft.EventGrid/domains/read | Een domein lezen |
> | Actie | Microsoft.EventGrid/domains/regenerateKey/action | Sleutel voor een domein opnieuw genereren |
> | Actie | Micro soft. EventGrid/domeinen/onderwerpen/verwijderen | Een domein onderwerp verwijderen |
> | Actie | Microsoft.EventGrid/domains/topics/read | Een onderwerp van een domein lezen |
> | Actie | Micro soft. EventGrid/domeinen/onderwerpen/schrijven | Een domein onderwerp maken of bijwerken |
> | Actie | Microsoft.EventGrid/domains/write | Een domein maken of bijwerken |
> | Actie | Microsoft.EventGrid/eventSubscriptions/delete | Een eventSubscription verwijderen |
> | Actie | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Volledige URL voor het gebeurtenis abonnement ophalen |
> | Actie | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee wordt de diagnostische instelling voor gebeurtenis abonnementen opgehaald |
> | Actie | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor gebeurtenis abonnementen gemaakt of bijgewerkt |
> | Actie | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor eventSubscriptions |
> | Actie | Microsoft.EventGrid/eventSubscriptions/read | Een eventSubscription lezen |
> | Actie | Microsoft.EventGrid/eventSubscriptions/write | Een eventSubscription maken of bijwerken |
> | Actie | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee wordt de diagnostische instelling voor onderwerpen opgehaald |
> | Actie | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor onderwerpen gemaakt of bijgewerkt |
> | Actie | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor onderwerpen |
> | Actie | Microsoft.EventGrid/extensionTopics/read | Lees een extensionTopic. |
> | Actie | Microsoft.EventGrid/locations/eventSubscriptions/read | Regionale gebeurtenis abonnementen weer geven |
> | Actie | Microsoft.EventGrid/locations/operationResults/read | Het resultaat van een regionale bewerking lezen |
> | Actie | Microsoft.EventGrid/locations/operationsStatus/read | De status van een regionale bewerking lezen |
> | Actie | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Regionale gebeurtenis abonnementen weer geven op topictype |
> | Actie | Microsoft.EventGrid/operationResults/read | Het resultaat van een bewerking lezen |
> | Actie | Microsoft.EventGrid/operations/read | Lijst met EventGrid-bewerkingen. |
> | Actie | Microsoft.EventGrid/operationsStatus/read | De status van een bewerking lezen |
> | Actie | Microsoft.EventGrid/register/action | Hiermee wordt het abonnement voor de EventGrid-resource provider geregistreerd. |
> | Actie | Microsoft.EventGrid/topics/delete | Een onderwerp verwijderen |
> | Actie | Microsoft.EventGrid/topics/listKeys/action | Sleutels voor een onderwerp weer geven |
> | Actie | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee wordt de diagnostische instelling voor onderwerpen opgehaald |
> | Actie | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor onderwerpen gemaakt of bijgewerkt |
> | Actie | Micro soft. EventGrid/topics/providers/micro soft. Insights/logDefinitions/lezen | Toegang tot Diagnostische logboeken toestaan |
> | Actie | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor onderwerpen |
> | Actie | Microsoft.EventGrid/topics/read | Een onderwerp lezen |
> | Actie | Microsoft.EventGrid/topics/regenerateKey/action | Sleutel voor een onderwerp opnieuw genereren |
> | Actie | Microsoft.EventGrid/topics/write | Een onderwerp maken of bijwerken |
> | Actie | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Globale gebeurtenis abonnementen weer geven op onderwerps type |
> | Actie | Microsoft.EventGrid/topictypes/eventtypes/read | Lees eventtypes ondersteund door een topictype |
> | Actie | Microsoft.EventGrid/topictypes/read | Een topictype lezen |
> | Actie | Micro soft. EventGrid/registratie/actie | Hiermee wordt de registratie van het abonnement voor de EventGrid-resource provider ongedaan gemaakt. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. EventHub/availableClusterRegions/lezen | Lees bewerking om een lijst te maken met beschik bare vooraf ingerichte clusters per Azure-regio. |
> | Actie | Microsoft.EventHub/checkNameAvailability/action | Controleert de beschik baarheid van de naam ruimte onder het gegeven abonnement. |
> | Actie | Microsoft.EventHub/checkNamespaceAvailability/action | Controleert de beschik baarheid van de naam ruimte onder het gegeven abonnement. Deze API is afgeschaft. gebruik in plaats daarvan CheckNameAvailability. |
> | Actie | Microsoft.EventHub/clusters/delete | Hiermee verwijdert u een bestaande cluster bron. |
> | Actie | Microsoft.EventHub/clusters/namespaces/read | Naam ruimte Azure Resource Manager-Id's voor naam ruimten binnen een cluster weer geven. |
> | Actie | Microsoft.EventHub/clusters/operationresults/read | De status van een asynchrone cluster bewerking ophalen. |
> | Actie | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Lijst met resource beschrijvingen voor metrische gegevens van cluster ophalen |
> | Actie | Microsoft.EventHub/clusters/read | Hiermee wordt de cluster bron beschrijving opgehaald |
> | Actie | Microsoft.EventHub/clusters/write | Hiermee maakt of wijzigt u een bestaande cluster bron. |
> | Actie | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Hiermee worden de VNet-regels in de EventHub-resource provider voor het opgegeven VNet verwijderd |
> | Actie | Microsoft.EventHub/namespaces/authorizationRules/action | De autorisatie regel voor de naam ruimte wordt bijgewerkt. Deze API is afgeschaft. Gebruik in plaats daarvan een PUT-aanroep om de naam ruimte autorisatie regel bij te werken. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Actie | Microsoft.EventHub/namespaces/authorizationRules/delete | Naam ruimte autorisatie regel verwijderen. De standaard regel voor autorisatie van de naam ruimte kan niet worden verwijderd.  |
> | Actie | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | De verbindings reeks ophalen voor de naam ruimte |
> | Actie | Microsoft.EventHub/namespaces/authorizationRules/read | De lijst met beschrijvingen van autorisatie regels voor naam ruimten ophalen. |
> | Actie | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Actie | Microsoft.EventHub/namespaces/authorizationRules/write | Maak autorisatie regels voor het naam ruimte niveau en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels, de primaire en secundaire sleutels kunnen worden bijgewerkt. |
> | Actie | Microsoft.EventHub/namespaces/Delete | Naam ruimte resource verwijderen |
> | Actie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hiermee worden de sleutels voor autorisatie regels opgehaald voor de primaire naam ruimte voor nood herstel |
> | Actie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | De autorisatie regels van de primaire naam ruimte voor herstel na nood geval ophalen |
> | Actie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Hiermee schakelt u herstel na nood gevallen uit en stopt u het repliceren van wijzigingen van primaire naar secundaire naam ruimten. |
> | Actie | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Controleert de beschik baarheid van de naam ruimte alias onder het opgegeven abonnement. |
> | Actie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Hiermee verwijdert u de nood herstel configuratie die is gekoppeld aan de naam ruimte. Deze bewerking kan alleen worden aangeroepen via de primaire naam ruimte. |
> | Actie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Hiermee wordt een GEO DR-failover aangeroepen en wordt de naam ruimte alias zo geconfigureerd dat deze naar de secundaire naam ruimte wijst. |
> | Actie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Hiermee haalt u de nood herstel configuratie op die is gekoppeld aan de naam ruimte. |
> | Actie | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Hiermee wordt de nood herstel configuratie die is gekoppeld aan de naam ruimte gemaakt of bijgewerkt. |
> | Actie | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Bewerking voor het bijwerken van EventHub. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. Autorisatie regels. Gebruik een aanroep voor het bijwerken van de autorisatie regel. |
> | Actie | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Bewerking voor het verwijderen van EventHub-autorisatie regels |
> | Actie | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | De verbindings reeks ophalen voor EventHub |
> | Actie | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  De lijst met EventHub-autorisatie regels ophalen |
> | Actie | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Actie | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Maak EventHub-autorisatie regels en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels kunnen worden bijgewerkt. |
> | Actie | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Bewerking voor het verwijderen van de ConsumerGroup-resource |
> | Actie | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Lijst met ConsumerGroup-resource beschrijvingen ophalen |
> | Actie | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Eigenschappen van ConsumerGroup maken of bijwerken. |
> | Actie | Microsoft.EventHub/namespaces/eventhubs/Delete | Bewerking voor het verwijderen van de EventHub-resource |
> | Actie | Microsoft.EventHub/namespaces/eventhubs/read | Lijst met EventHub-resource beschrijvingen ophalen |
> | Actie | Microsoft.EventHub/namespaces/eventhubs/write | Eigenschappen van EventHub maken of bijwerken. |
> | Actie | Microsoft.EventHub/namespaces/ipFilterRules/delete | IP-filter bron verwijderen |
> | Actie | Microsoft.EventHub/namespaces/ipFilterRules/read | IP-filter bron ophalen |
> | Actie | Microsoft.EventHub/namespaces/ipFilterRules/write | IP-filter bron maken |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Berichten ontvangen |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | Berichten verzenden |
> | Actie | Microsoft.EventHub/namespaces/messagingPlan/read | Hiermee wordt het berichten plan voor een naam ruimte opgehaald.<br>Deze API is afgeschaft.<br>Eigenschappen die via de MessagingPlan-resource beschikbaar worden gemaakt, worden in latere API-versies verplaatst naar de bron naam ruimte (Parent).<br>Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Actie | Microsoft.EventHub/namespaces/messagingPlan/write | Hiermee wordt het bericht abonnement voor een naam ruimte bijgewerkt.<br>Deze API is afgeschaft.<br>Eigenschappen die via de MessagingPlan-resource beschikbaar worden gemaakt, worden in latere API-versies verplaatst naar de bron naam ruimte (Parent).<br>Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Actie | Micro soft. EventHub/naam ruimten/networkruleset/verwijderen | VNET-regel resource verwijderen |
> | Actie | Micro soft. EventHub/naam ruimten/networkruleset/lezen | Hiermee wordt de NetworkRuleSet-resource opgehaald |
> | Actie | Micro soft. EventHub/naam ruimten/networkruleset/schrijven | VNET-regel resource maken |
> | Actie | Microsoft.EventHub/namespaces/networkrulesets/delete | VNET-regel resource verwijderen |
> | Actie | Microsoft.EventHub/namespaces/networkrulesets/read | Hiermee wordt de NetworkRuleSet-resource opgehaald |
> | Actie | Microsoft.EventHub/namespaces/networkrulesets/write | VNET-regel resource maken |
> | Actie | Microsoft.EventHub/namespaces/operationresults/read | De status van de naam ruimte bewerking ophalen |
> | Actie | Micro soft. EventHub/naam ruimten/providers/micro soft. Insights/diagnosticSettings/lezen | Lijst met resource beschrijvingen voor Diagnostische instellingen van naam ruimte ophalen |
> | Actie | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Lijst met resource beschrijvingen voor Diagnostische instellingen van naam ruimte ophalen |
> | Actie | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Lijst met naam ruimte logboek bron beschrijvingen ophalen |
> | Actie | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Lijst met resource beschrijvingen voor metrische gegevens van de naam ruimte ophalen |
> | Actie | Microsoft.EventHub/namespaces/read | De lijst met de resource beschrijving van de naam ruimte ophalen |
> | Actie | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | ACS-naam ruimte verwijderen |
> | Actie | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | VNET-regel resource verwijderen |
> | Actie | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Hiermee wordt VNET-regel Resource opgehaald |
> | Actie | Microsoft.EventHub/namespaces/virtualNetworkRules/write | VNET-regel resource maken |
> | Actie | Microsoft.EventHub/namespaces/write | Maak een naam ruimte resource en werk de eigenschappen bij. Tags en capaciteit van de naam ruimte zijn de eigenschappen die kunnen worden bijgewerkt. |
> | Actie | Microsoft.EventHub/operations/read | Get-bewerkingen |
> | Actie | Microsoft.EventHub/register/action | Hiermee wordt het abonnement voor de EventHub-resource provider geregistreerd en wordt het maken van EventHub-resources mogelijk |
> | Actie | Microsoft.EventHub/sku/read | Lijst met resource beschrijvingen van SKU ophalen |
> | Actie | Microsoft.EventHub/sku/regions/read | Lijst met SkuRegions-resource beschrijvingen ophalen |
> | Actie | Microsoft.EventHub/unregister/action | Hiermee wordt de EventHub-resource provider geregistreerd |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. features/onderdelen/lezen | Hiermee haalt u de functies van een abonnement op. |
> | Actie | Micro soft. features/Operations/lezen | Hiermee wordt de lijst met bewerkingen opgehaald. |
> | Actie | Microsoft.Features/providers/features/read | Hiermee wordt de functie van een abonnement in een bepaalde resource provider opgehaald. |
> | Actie | Microsoft.Features/providers/features/register/action | Hiermee wordt de functie voor een abonnement in een bepaalde resource provider geregistreerd. |
> | Actie | Microsoft.Features/providers/features/unregister/action | Hiermee wordt de registratie van de functie voor een abonnement in een bepaalde resource provider ongedaan gemaakt. |
> | Actie | Microsoft.Features/register/action | Hiermee wordt de functie van een abonnement geregistreerd. |

## <a name="microsoftguestconfiguration"></a>Micro soft. GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Toewijzing van gast configuratie verwijderen. |
> | Actie | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Toewijzing van gast configuratie ophalen. |
> | Actie | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Toewijzings rapport voor gast configuratie ophalen. |
> | Actie | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Nieuwe gast configuratie toewijzing maken. |
> | Actie | Micro soft. GuestConfiguration/Operations/lezen | Hiermee worden de bewerkingen voor de resource provider micro soft. GuestConfiguration opgehaald |
> | Actie | Microsoft.GuestConfiguration/register/action | Hiermee wordt het abonnement voor de resource provider micro soft. GuestConfiguration geregistreerd. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.HDInsight/clusters/applications/delete | Toepassing voor HDInsight-cluster verwijderen |
> | Actie | Microsoft.HDInsight/clusters/applications/read | Toepassing voor HDInsight-cluster ophalen |
> | Actie | Microsoft.HDInsight/clusters/applications/write | Toepassing voor HDInsight-cluster maken of bijwerken |
> | Actie | Micro soft. HDInsight/clusters/changerdpsetting/actie | RDP-instelling voor HDInsight-cluster wijzigen |
> | Actie | Microsoft.HDInsight/clusters/configurations/action | Configuratie van HDInsight-cluster bijwerken |
> | Actie | Microsoft.HDInsight/clusters/configurations/action | Configuraties van HDInsight-clusters ophalen |
> | Actie | Microsoft.HDInsight/clusters/configurations/read | Configuraties van HDInsight-clusters ophalen |
> | Actie | Microsoft.HDInsight/clusters/delete | Een HDInsight-cluster verwijderen |
> | Actie | Micro soft. HDInsight/clusters/uitbrei dingen/verwijderen | Cluster uitbreiding voor HDInsight-cluster verwijderen |
> | Actie | Micro soft. HDInsight/clusters/uitbrei dingen/lezen | Cluster uitbreiding voor HDInsight-cluster ophalen |
> | Actie | Micro soft. HDInsight/clusters/uitbrei dingen/schrijven | Cluster uitbreiding voor HDInsight-cluster maken |
> | Actie | Microsoft.HDInsight/clusters/getGatewaySettings/action | Gateway-instellingen voor HDInsight-cluster ophalen |
> | Actie | Micro soft. HDInsight/clusters/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor het bron HDInsight-cluster opgehaald |
> | Actie | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor het bron HDInsight-cluster gemaakt of bijgewerkt |
> | Actie | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor het HDInsight-cluster |
> | Actie | Microsoft.HDInsight/clusters/read | Details over HDInsight-cluster ophalen |
> | Actie | Microsoft.HDInsight/clusters/roles/resize/action | Het formaat van een HDInsight-cluster wijzigen |
> | Actie | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Gateway-instellingen voor HDInsight-cluster bijwerken |
> | Actie | Microsoft.HDInsight/clusters/write | Een HDInsight-cluster maken of bijwerken |
> | Actie | Microsoft.HDInsight/locations/capabilities/read | Abonnements mogelijkheden ophalen |
> | Actie | Microsoft.HDInsight/locations/checkNameAvailability/read | Beschik baarheid van naam controleren |
> | Actie | Micro soft. HDInsight/registreren/actie | HDInsight-resource provider registreren voor het abonnement |
> | Actie | Micro soft. HDInsight/niet registreren/actie | Registratie van HDInsight-resource provider opheffen voor het abonnement |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. ImportExport/Jobs/verwijderen | Hiermee verwijdert u een bestaande taak. |
> | Actie | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Hiermee worden de BitLocker-sleutels voor de opgegeven taak opgehaald. |
> | Actie | Microsoft.ImportExport/jobs/read | Hiermee worden de eigenschappen van de opgegeven taak opgehaald of wordt de lijst met taken geretourneerd. |
> | Actie | Microsoft.ImportExport/jobs/write | Hiermee maakt u een taak met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven taak bij. |
> | Actie | Micro soft. ImportExport/locaties/lezen | Hiermee worden de eigenschappen opgehaald voor de opgegeven locatie of wordt de lijst met locaties geretourneerd. |
> | Actie | Micro soft. ImportExport/Operations/lezen | Hiermee worden de bewerkingen opgehaald die door de resource provider worden ondersteund. |
> | Actie | Micro soft. ImportExport/REGI ster/actie | Hiermee wordt het abonnement voor de resource provider import/export geregistreerd en wordt het maken van import/export-taken ingeschakeld. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Insights/ActionGroups/Delete | Een actie groep verwijderen |
> | Actie | Microsoft.Insights/ActionGroups/Read | Een actie groep lezen |
> | Actie | Microsoft.Insights/ActionGroups/Write | Een actie groep maken of bijwerken |
> | Actie | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Waarschuwing voor activiteiten logboek geactiveerd |
> | Actie | Microsoft.Insights/ActivityLogAlerts/Delete | Een waarschuwing voor een activiteiten logboek verwijderen |
> | Actie | Microsoft.Insights/ActivityLogAlerts/Read | Een waarschuwing voor een activiteiten logboek lezen |
> | Actie | Microsoft.Insights/ActivityLogAlerts/Write | Een waarschuwing voor een activiteiten logboek maken of bijwerken |
> | Actie | Microsoft.Insights/AlertRules/Activated/Action | Klassieke waarschuwing voor metrische gegevens geactiveerd |
> | Actie | Microsoft.Insights/AlertRules/Delete | Een klassieke waarschuwing voor metrische gegevens verwijderen |
> | Actie | Microsoft.Insights/AlertRules/Incidents/Read | Een bericht met een klassiek metrische waarschuwing lezen |
> | Actie | Microsoft.Insights/AlertRules/Read | Een klassieke waarschuwing voor metrische gegevens lezen |
> | Actie | Microsoft.Insights/AlertRules/Resolved/Action | Klassieke waarschuwing voor metrische gegevens opgelost |
> | Actie | Microsoft.Insights/AlertRules/Throttled/Action | Waarschuwings regel voor klassieke metrische gegevens beperkt |
> | Actie | Microsoft.Insights/AlertRules/Write | Een klassieke waarschuwing voor metrische gegevens maken of bijwerken |
> | Actie | Microsoft.Insights/AutoscaleSettings/Delete | Een instelling voor automatisch schalen verwijderen |
> | Actie | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Een diagnostische instelling voor bronnen lezen |
> | Actie | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Een diagnostische instelling voor resources maken of bijwerken |
> | Actie | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Logboek definities lezen |
> | Actie | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Metrische definities lezen |
> | Actie | Microsoft.Insights/AutoscaleSettings/Read | Een instelling voor automatisch schalen lezen |
> | Actie | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Automatisch omlaag schalen gestart |
> | Actie | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Automatisch omlaag schalen voltooid |
> | Actie | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Automatisch omhoog schalen gestart |
> | Actie | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Automatisch omhoog schalen voltooid |
> | Actie | Microsoft.Insights/AutoscaleSettings/Write | Een instelling voor automatisch schalen maken of bijwerken |
> | Actie | Microsoft.Insights/Baseline/Read | Een metrische basis lijn lezen (preview-versie) |
> | Actie | Microsoft.Insights/CalculateBaseline/Read | Basis lijn voor metrische waarden berekenen (preview-versie) |
> | Actie | Microsoft.Insights/Components/AnalyticsItems/Delete | Een Application Insights Analytics-item verwijderen |
> | Actie | Microsoft.Insights/Components/AnalyticsItems/Read | Een Application Insights Analytics-item lezen |
> | Actie | Microsoft.Insights/Components/AnalyticsItems/Write | Een Application Insights Analytics-item schrijven |
> | Actie | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights Analytics Table-actie |
> | Actie | Microsoft.Insights/Components/AnalyticsTables/Delete | Een Application Insights Analytics table-schema verwijderen |
> | Actie | Microsoft.Insights/Components/AnalyticsTables/Read | Een Application Insights Analytics table-schema lezen |
> | Actie | Microsoft.Insights/Components/AnalyticsTables/Write | Een Application Insights Analytics table-schema schrijven |
> | Actie | Microsoft.Insights/Components/Annotations/Delete | Een Application Insights aantekening verwijderen |
> | Actie | Microsoft.Insights/Components/Annotations/Read | Een Application Insights aantekening lezen |
> | Actie | Microsoft.Insights/Components/Annotations/Write | Een Application Insights aantekening schrijven |
> | Actie | Microsoft.Insights/Components/Api/Read | API voor Application Insights onderdeel gegevens lezen |
> | Actie | Microsoft.Insights/Components/ApiKeys/Action | Een Application Insights-API-sleutel genereren |
> | Actie | Microsoft.Insights/Components/ApiKeys/Delete | Een Application Insights-API-sleutel verwijderen |
> | Actie | Microsoft.Insights/Components/ApiKeys/Read | Een Application Insights-API-sleutel lezen |
> | Actie | Microsoft.Insights/Components/BillingPlanForComponent/Read | Een facturerings plan voor Application Insights onderdeel lezen |
> | Actie | Micro soft. Insights/onderdelen/CurrentBillingFeatures/lezen | De huidige facturerings functies voor Application Insights onderdeel lezen |
> | Actie | Micro soft. Insights/onderdelen/CurrentBillingFeatures/schrijven | Huidige facturerings functies voor Application Insights onderdeel schrijven |
> | Actie | Micro soft. Insights/onderdelen/DailyCapReached/actie | De dagelijkse limiet voor het Application Insights onderdeel is bereikt |
> | Actie | Micro soft. Insights/onderdelen/DailyCapWarningThresholdReached/actie | De waarschuwings drempel voor de dagelijkse limiet voor Application Insights onderdeel is bereikt |
> | Actie | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Een Application Insights standaard configuratie voor ALM-integratie lezen |
> | Actie | Microsoft.Insights/Components/Delete | De configuratie van een Application Insights-onderdeel verwijderen |
> | Actie | Microsoft.Insights/Components/Events/Read | Logboeken van Application Insights ophalen met de OData-query-indeling |
> | Actie | Microsoft.Insights/Components/ExportConfiguration/Action | Actie Application Insights export instellingen |
> | Actie | Microsoft.Insights/Components/ExportConfiguration/Delete | Application Insights export instellingen verwijderen |
> | Actie | Microsoft.Insights/Components/ExportConfiguration/Read | Instellingen voor Application Insights exporteren lezen |
> | Actie | Microsoft.Insights/Components/ExportConfiguration/Write | Instellingen voor Application Insights exporteren schrijven |
> | Actie | Microsoft.Insights/Components/ExtendQueries/Read | Uitgebreide query resultaten van Application Insights onderdeel lezen |
> | Actie | Microsoft.Insights/Components/Favorites/Delete | Een Application Insights favoriet verwijderen |
> | Actie | Micro soft. Insights/onderdelen/Favorieten/lezen | Een Application Insights favoriet lezen |
> | Actie | Microsoft.Insights/Components/Favorites/Write | Een Application Insights favoriet schrijven |
> | Actie | Micro soft. Insights/onderdelen/FeatureCapabilities/lezen | Functie mogelijkheden van Application Insights onderdeel lezen |
> | Actie | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Beschik bare facturerings functies van Application Insights onderdeel lezen |
> | Actie | Micro soft. Insights/onderdelen/GetToken/lezen | Een token voor een Application Insights onderdeel lezen |
> | Actie | Micro soft. Insights/onderdelen/MetricDefinitions/lezen | Metrische definities van Application Insights-onderdeel lezen |
> | Actie | Micro soft. Insights/onderdelen/metrische gegevens/lezen | Metrische gegevens van Application Insights-onderdeel lezen |
> | Actie | Microsoft.Insights/Components/Move/Action | Een Application Insights onderdeel verplaatsen naar een andere resource groep of een ander abonnement |
> | Actie | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Een Application Insights persoonlijk analyse-item verwijderen |
> | Actie | Microsoft.Insights/Components/MyAnalyticsItems/Read | Een Application Insights persoonlijk analyse-item lezen |
> | Actie | Microsoft.Insights/Components/MyAnalyticsItems/Write | Een Application Insights persoonlijk analyse-item schrijven |
> | Actie | Microsoft.Insights/Components/MyFavorites/Read | Een Application Insights persoonlijke favoriet lezen |
> | Actie | Microsoft.Insights/Components/Operations/Read | Status van langlopende bewerkingen in Application Insights ophalen |
> | Actie | Micro soft. Insights/onderdelen/PricingPlans/lezen | Een prijs plan voor een Application Insights onderdeel lezen |
> | Actie | Microsoft.Insights/Components/PricingPlans/Write | Een prijs plan voor een Application Insights onderdeel schrijven |
> | Actie | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Application Insights proactieve detectie configuratie lezen |
> | Actie | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Application Insights proactieve detectie configuratie wordt geschreven |
> | Actie | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Metrische definities lezen |
> | Actie | Microsoft.Insights/Components/Purge/Action | Gegevens uit Application Insights verwijderen |
> | Actie | Micro soft. Insights/onderdelen/query/lezen | Query's uitvoeren voor Application Insights-logboeken |
> | Actie | Micro soft. Insights/onderdelen/QuotaStatus/lezen | Quota status van Application Insights onderdeel lezen |
> | Actie | Micro soft. Insights/onderdelen/lezen | De configuratie van een Application Insights-onderdeel lezen |
> | Actie | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Application Insights-webtest locaties lezen |
> | Actie | Micro soft. Insights/onderdelen/webtesten/lezen | Een configuratie van een webtest lezen |
> | Actie | Microsoft.Insights/Components/WorkItemConfigs/Delete | Een Application Insights ALM-integratie configuratie verwijderen |
> | Actie | Microsoft.Insights/Components/WorkItemConfigs/Read | Een Application Insights ALM-integratie configuratie lezen |
> | Actie | Microsoft.Insights/Components/WorkItemConfigs/Write | Een Application Insights ALM-integratie configuratie schrijven |
> | Actie | Microsoft.Insights/Components/Write | Schrijven naar de configuratie van een Application Insights-onderdeel |
> | Actie | Micro soft. Insights/DataCollectionRuleAssociations/verwijderen | De koppeling van een resource met een gegevens verzamelings regel verwijderen |
> | Actie | Micro soft. Insights/DataCollectionRuleAssociations/lezen | De koppeling van een resource met een gegevens verzamelings regel lezen |
> | Actie | Micro soft. Insights/DataCollectionRuleAssociations/schrijven | De koppeling van een resource met een gegevens verzamelings regel maken of bijwerken |
> | DataAction | Micro soft. Insights/DataCollectionRules/gegevens/schrijven | Gegevens verzenden naar een regel voor gegevens verzameling |
> | Actie | Micro soft. Insights/DataCollectionRules/verwijderen | Een regel voor het verzamelen van gegevens verwijderen |
> | Actie | Micro soft. Insights/DataCollectionRules/lezen | Een regel voor het verzamelen van gegevens lezen |
> | Actie | Micro soft. Insights/DataCollectionRules/schrijven | Een regel voor het verzamelen van gegevens maken of bijwerken |
> | Actie | Microsoft.Insights/DiagnosticSettings/Delete | Een diagnostische instelling voor bronnen verwijderen |
> | Actie | Microsoft.Insights/DiagnosticSettings/Read | Een diagnostische instelling voor bronnen lezen |
> | Actie | Microsoft.Insights/DiagnosticSettings/Write | Een diagnostische instelling voor resources maken of bijwerken |
> | Actie | Micro soft. Insights/EventCategories/lezen | Beschik bare gebeurtenis categorieën voor activiteiten logboek lezen |
> | Actie | Microsoft.Insights/eventtypes/digestevents/Read | Samen vatting van beheer gebeurtenis type lezen |
> | Actie | Microsoft.Insights/eventtypes/values/Read | Gebeurtenissen in het activiteiten logboek lezen |
> | Actie | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Een diagnostische instelling voor het netwerk stroom logboek verwijderen |
> | Actie | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Een diagnostische instelling voor het netwerk stroom logboek lezen |
> | Actie | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Een diagnostische instelling voor het netwerk stroom logboek maken of bijwerken |
> | Actie | Microsoft.Insights/ListMigrationDate/Action | Migratie datum van abonnement terughalen |
> | Actie | Microsoft.Insights/ListMigrationDate/Read | Migratie datum van abonnement terughalen |
> | Actie | Micro soft. Insights/LogDefinitions/lezen | Logboek definities lezen |
> | Actie | Microsoft.Insights/LogProfiles/Delete | Een logboek profiel voor het activiteiten logboek verwijderen |
> | Actie | Microsoft.Insights/LogProfiles/Read | Een logboek profiel voor het activiteiten logboek lezen |
> | Actie | Microsoft.Insights/LogProfiles/Write | Een logboek profiel voor het activiteiten logboek maken of bijwerken |
> | Actie | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Gegevens uit de ADAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ADReplicationResult/Read | Gegevens uit de ADReplicationResult-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Gegevens uit de ADSecurityAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/Alert/Read | Gegevens uit de tabel waarschuwing lezen |
> | Actie | Microsoft.Insights/Logs/AlertHistory/Read | Gegevens uit de AlertHistory-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ApplicationInsights/Read | Gegevens uit de ApplicationInsights-tabel lezen |
> | Actie | Microsoft.Insights/Logs/AzureActivity/Read | Gegevens uit de AzureActivity-tabel lezen |
> | Actie | Microsoft.Insights/Logs/AzureMetrics/Read | Gegevens uit de AzureMetrics-tabel lezen |
> | Actie | Microsoft.Insights/Logs/BoundPort/Read | Gegevens uit de BoundPort-tabel lezen |
> | Actie | Microsoft.Insights/Logs/CommonSecurityLog/Read | Gegevens uit de CommonSecurityLog-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/ComputerGroup/lezen | Gegevens uit de ComputerGroup-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/ConfigurationChange/lezen | Gegevens uit de ConfigurationChange-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ConfigurationData/Read | Gegevens uit de ConfigurationData-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ContainerImageInventory/Read | Gegevens uit de ContainerImageInventory-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ContainerInventory/Read | Gegevens uit de ContainerInventory-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ContainerLog/Read | Gegevens uit de ContainerLog-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ContainerServiceLog/Read | Gegevens uit de ContainerServiceLog-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DeviceAppCrash/Read | Gegevens uit de DeviceAppCrash-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Gegevens uit de DeviceAppLaunch-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/DeviceCalendar/lezen | Gegevens uit de DeviceCalendar-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DeviceCleanup/Read | Gegevens uit de DeviceCleanup-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DeviceConnectSession/Read | Gegevens uit de DeviceConnectSession-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DeviceEtw/Read | Gegevens uit de DeviceEtw-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Gegevens uit de DeviceHardwareHealth-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DeviceHealth/Read | Gegevens uit de apparaatstatus-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Gegevens uit de DeviceHeartbeat-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Gegevens uit de DeviceSkypeHeartbeat-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Gegevens uit de DeviceSkypeSignIn-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DeviceSleepState/Read | Gegevens uit de DeviceSleepState-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DHAppFailure/Read | Gegevens uit de DHAppFailure-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/DHAppReliability/lezen | Gegevens uit de DHAppReliability-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/DHDriverReliability/lezen | Gegevens uit de DHDriverReliability-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DHLogonFailures/Read | Gegevens uit de DHLogonFailures-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DHLogonMetrics/Read | Gegevens uit de DHLogonMetrics-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DHOSCrashData/Read | Gegevens uit de DHOSCrashData-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DHOSReliability/Read | Gegevens uit de DHOSReliability-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/DHWipAppLearning/lezen | Gegevens uit de DHWipAppLearning-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/DnsEvents/lezen | Gegevens uit de DnsEvents-tabel lezen |
> | Actie | Microsoft.Insights/Logs/DnsInventory/Read | Gegevens uit de DnsInventory-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/ETWEvent/lezen | Gegevens uit de ETWEvent-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/gebeurtenis/lezen | Gegevens uit de gebeurtenis tabel lezen |
> | Actie | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Gegevens uit de ExchangeAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Gegevens uit de ExchangeOnlineAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/Heartbeat/Read | Gegevens uit de heartbeat-tabel lezen |
> | Actie | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Gegevens uit de IISAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/InboundConnection/Read | Gegevens uit de InboundConnection-tabel lezen |
> | Actie | Microsoft.Insights/Logs/KubeNodeInventory/Read | Gegevens uit de KubeNodeInventory-tabel lezen |
> | Actie | Microsoft.Insights/Logs/KubePodInventory/Read | Gegevens uit de KubePodInventory-tabel lezen |
> | Actie | Microsoft.Insights/Logs/LinuxAuditLog/Read | Gegevens uit de LinuxAuditLog-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/MAApplication/lezen | Gegevens uit de MAApplication-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/MAApplicationHealth/lezen | Gegevens uit de MAApplicationHealth-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Gegevens uit de MAApplicationHealthAlternativeVersions-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/MAApplicationHealthIssues/lezen | Gegevens uit de MAApplicationHealthIssues-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAApplicationInstance/Read | Gegevens uit de MAApplicationInstance-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Gegevens uit de MAApplicationInstanceReadiness-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Gegevens uit de MAApplicationReadiness-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/MADeploymentPlan/lezen | Gegevens uit de MADeploymentPlan-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/MADevice/lezen | Gegevens uit de MADevice-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Gegevens uit de MADevicePnPHealth-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Gegevens uit de MADevicePnPHealthAlternativeVersions-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Gegevens uit de MADevicePnPHealthIssues-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MADeviceReadiness/Read | Gegevens uit de MADeviceReadiness-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Gegevens uit de MADriverInstanceReadiness-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MADriverReadiness/Read | Gegevens uit de MADriverReadiness-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeAddin/Read | Gegevens uit de MAOfficeAddin-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Gegevens uit de MAOfficeAddinHealth-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Gegevens uit de MAOfficeAddinHealthIssues-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Gegevens uit de MAOfficeAddinInstance-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Gegevens uit de MAOfficeAddinInstanceReadiness-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Gegevens uit de MAOfficeAddinReadiness-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeApp/Read | Gegevens uit de MAOfficeApp-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Gegevens uit de MAOfficeAppHealth-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Gegevens uit de MAOfficeAppInstance-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Gegevens uit de MAOfficeAppReadiness-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Gegevens uit de MAOfficeBuildInfo-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Gegevens uit de MAOfficeCurrencyAssessment-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Gegevens uit de MAOfficeCurrencyAssessmentDailyCounts-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Gegevens uit de MAOfficeDeploymentStatus-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Gegevens uit de MAOfficeMacroHealth-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Gegevens uit de MAOfficeMacroHealthIssues-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Gegevens uit de MAOfficeMacroIssueInstanceReadiness-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Gegevens uit de MAOfficeMacroIssueReadiness-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Gegevens uit de MAOfficeMacroSummary-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeSuite/Read | Gegevens uit de MAOfficeSuite-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Gegevens uit de MAOfficeSuiteInstance-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Gegevens uit de MAProposedPilotDevices-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Gegevens uit de MAWindowsBuildInfo-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Gegevens uit de MAWindowsCurrencyAssessment-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Gegevens uit de MAWindowsCurrencyAssessmentDailyCounts-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Gegevens uit de MAWindowsDeploymentStatus-tabel lezen |
> | Actie | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Gegevens uit de MAWindowsSysReqInstanceReadiness-tabel lezen |
> | Actie | Microsoft.Insights/Logs/NetworkMonitoring/Read | Gegevens uit de NetworkMonitoring-tabel lezen |
> | Actie | Microsoft.Insights/Logs/OfficeActivity/Read | Gegevens uit de OfficeActivity-tabel lezen |
> | Actie | Microsoft.Insights/Logs/Operation/Read | Gegevens uit de bewerkings tabel lezen |
> | Actie | Microsoft.Insights/Logs/OutboundConnection/Read | Gegevens uit de OutboundConnection-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/perf/lezen | Gegevens uit de prestatie tabel lezen |
> | Actie | Microsoft.Insights/Logs/ProtectionStatus/Read | Gegevens uit de ProtectionStatus-tabel lezen |
> | Actie | Microsoft.Insights/Logs/Read | Gegevens uit al uw logboeken lezen |
> | Actie | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Gegevens uit de ReservedAzureCommonFields-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ReservedCommonFields/Read | Gegevens uit de ReservedCommonFields-tabel lezen |
> | Actie | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Gegevens uit de SCCMAssessmentRecommendation-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/SCOMAssessmentRecommendation/lezen | Gegevens uit de SCOMAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/SecurityAlert/Read | Gegevens uit de SecurityAlert-tabel lezen |
> | Actie | Microsoft.Insights/Logs/SecurityBaseline/Read | Gegevens uit de Security Baseline Baseline-tabel lezen |
> | Actie | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Gegevens uit de Summary-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/SecurityDetection/lezen | Gegevens uit de SecurityDetection-tabel lezen |
> | Actie | Microsoft.Insights/Logs/SecurityEvent/Read | Gegevens uit de SecurityEvent-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Gegevens uit de ServiceFabricOperationalEvent-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Gegevens uit de ServiceFabricReliableActorEvent-tabel lezen |
> | Actie | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Gegevens uit de ServiceFabricReliableServiceEvent-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/SfBAssessmentRecommendation/lezen | Gegevens uit de SfBAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Gegevens uit de SfBOnlineAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Gegevens uit de SharePointOnlineAssessmentRecommendation-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/SPAssessmentRecommendation/lezen | Gegevens uit de SPAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Gegevens uit de SQLAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Gegevens uit de SQLQueryPerformance-tabel lezen |
> | Actie | Microsoft.Insights/Logs/Syslog/Read | Gegevens uit de syslog-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/SysmonEvent/lezen | Gegevens uit de SysmonEvent-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/tabellen. aangepast/lezen | Gegevens uit een aangepast logboek lezen |
> | Actie | Micro soft. Insights/logboeken/UAApp/lezen | Gegevens uit de UAApp-tabel lezen |
> | Actie | Microsoft.Insights/Logs/UAComputer/Read | Gegevens uit de UAComputer-tabel lezen |
> | Actie | Microsoft.Insights/Logs/UAComputerRank/Read | Gegevens uit de UAComputerRank-tabel lezen |
> | Actie | Microsoft.Insights/Logs/UADriver/Read | Gegevens uit de UADriver-tabel lezen |
> | Actie | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Gegevens uit de UADriverProblemCodes-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/UAFeedback/lezen | Gegevens uit de UAFeedback-tabel lezen |
> | Actie | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Gegevens uit de UAHardwareSecurity-tabel lezen |
> | Actie | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Gegevens uit de UAIESiteDiscovery-tabel lezen |
> | Actie | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Gegevens uit de UAOfficeAddIn-tabel lezen |
> | Actie | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Gegevens uit de UAProposedActionPlan-tabel lezen |
> | Actie | Microsoft.Insights/Logs/UASysReqIssue/Read | Gegevens uit de UASysReqIssue-tabel lezen |
> | Actie | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Gegevens uit de UAUpgradedComputer-tabel lezen |
> | Actie | Micro soft. Insights/logboeken/bijwerken/lezen | Gegevens uit de update tabel lezen |
> | Actie | Microsoft.Insights/Logs/UpdateRunProgress/Read | Gegevens uit de Updaterunprogress Installation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/UpdateSummary/Read | Gegevens uit de update Summary-tabel lezen |
> | Actie | Microsoft.Insights/Logs/Usage/Read | Gegevens uit de gebruiks tabel lezen |
> | Actie | Microsoft.Insights/Logs/W3CIISLog/Read | Gegevens uit de W3CIISLog-tabel lezen |
> | Actie | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Gegevens uit de WaaSDeploymentStatus-tabel lezen |
> | Actie | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Gegevens uit de WaaSInsiderStatus-tabel lezen |
> | Actie | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Gegevens uit de WaaSUpdateStatus-tabel lezen |
> | Actie | Microsoft.Insights/Logs/WDAVStatus/Read | Gegevens uit de WDAVStatus-tabel lezen |
> | Actie | Microsoft.Insights/Logs/WDAVThreat/Read | Gegevens uit de WDAVThreat-tabel lezen |
> | Actie | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Gegevens uit de WindowsClientAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/WindowsFirewall/Read | Gegevens uit de WindowsFirewall-tabel lezen |
> | Actie | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Gegevens uit de WindowsServerAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.Insights/Logs/WireData/Read | Gegevens uit de WireData-tabel lezen |
> | Actie | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Gegevens uit de WUDOAggregatedStatus-tabel lezen |
> | Actie | Microsoft.Insights/Logs/WUDOStatus/Read | Gegevens uit de WUDOStatus-tabel lezen |
> | Actie | Microsoft.Insights/MetricAlerts/Delete | Een waarschuwing voor metrische gegevens verwijderen |
> | Actie | Microsoft.Insights/MetricAlerts/Read | Een waarschuwing voor metrische gegevens lezen |
> | Actie | Micro soft. Insights/MetricAlerts/status/lezen | Status van waarschuwing voor metrische gegevens lezen |
> | Actie | Micro soft. Insights/MetricAlerts/schrijven | Een waarschuwing voor metrische gegevens maken of bijwerken |
> | Actie | Microsoft.Insights/MetricBaselines/Read | Metrische basis lijnen lezen |
> | Actie | Micro soft. Insights/MetricDefinitions/micro soft. Insights/lezen | Metrische definities lezen |
> | Actie | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Metrische definities lezen |
> | Actie | Microsoft.Insights/MetricDefinitions/Read | Metrische definities lezen |
> | Actie | Micro soft. Insights/Metricnamespaces/lezen | Metrische naam ruimten lezen |
> | Actie | Microsoft.Insights/Metrics/Action | Metrische actie |
> | Actie | Micro soft. Insights/metrische gegevens/micro soft. Insights/lezen | Metrische gegevens lezen |
> | Actie | Microsoft.Insights/Metrics/providers/Metrics/Read | Metrische gegevens lezen |
> | Actie | Microsoft.Insights/Metrics/Read | Metrische gegevens lezen |
> | DataAction | Microsoft.Insights/Metrics/Write | Metrische gegevens schrijven |
> | Actie | Microsoft.Insights/MigrateToNewpricingModel/Action | Abonnement migreren naar nieuw prijs model |
> | Actie | Microsoft.Insights/Operations/Read | Leesbewerkingen |
> | Actie | Microsoft.Insights/Register/Action | De micro soft Insights-provider registreren |
> | Actie | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Abonnement terugdraaien naar een verouderd prijs model |
> | Actie | Microsoft.Insights/ScheduledQueryRules/Delete | Een geplande query regel verwijderen |
> | Actie | Microsoft.Insights/ScheduledQueryRules/Read | Een geplande query regel lezen |
> | Actie | Microsoft.Insights/ScheduledQueryRules/Write | Een geplande query regel schrijven |
> | Actie | Microsoft.Insights/Tenants/Register/Action | Initialiseert de micro soft Insights-provider |
> | Actie | Microsoft.Insights/Unregister/Action | De micro soft Insights-provider registreren |
> | Actie | Microsoft.Insights/Webtests/Delete | Een webtest configuratie verwijderen |
> | Actie | Microsoft.Insights/Webtests/GetToken/Read | Een token voor webtest lezen |
> | Actie | Micro soft. Insights/webtests/MetricDefinitions/lezen | Metrische definities van webtest lezen |
> | Actie | Micro soft. Insights/webtests/metrische gegevens/lezen | De metrische gegevens van een webtest lezen |
> | Actie | Microsoft.Insights/Webtests/Read | Een configuratie van een webtest lezen |
> | Actie | Microsoft.Insights/Webtests/Write | Schrijven naar een webtest-configuratie |
> | Actie | Micro soft. Insights/werkmappen/verwijderen | Een werkmap verwijderen |
> | Actie | Micro soft. Insights/werkmappen/lezen | Een werkmap lezen |
> | Actie | Micro soft. Insights/werkmappen/schrijven | Een werkmap maken of bijwerken |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. intune/diagnosticsettings/verwijderen | Een diagnostische instelling verwijderen |
> | Actie | Micro soft. intune/diagnosticsettings/lezen | Een diagnostische instelling lezen |
> | Actie | Micro soft. intune/diagnosticsettings/schrijven | Een diagnostische instelling schrijven |
> | Actie | Micro soft. intune/diagnosticsettingscategories/lezen | Categorieën voor Diagnostische instellingen lezen |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.IoTCentral/appTemplates/action | Hiermee worden alle beschik bare toepassings sjablonen op Azure IoT Central opgehaald |
> | Actie | Microsoft.IoTCentral/checkNameAvailability/action | Hiermee wordt gecontroleerd of een IoT Central toepassings naam beschikbaar is |
> | Actie | Microsoft.IoTCentral/checkSubdomainAvailability/action | Hiermee wordt gecontroleerd of een IoT Central subdomein van de toepassing beschikbaar is |
> | Actie | Microsoft.IoTCentral/IoTApps/delete | Hiermee verwijdert u een IoT Central-toepassing |
> | Actie | Microsoft.IoTCentral/IoTApps/read | Hiermee wordt een enkele IoT Central-toepassing opgehaald |
> | Actie | Microsoft.IoTCentral/IoTApps/write | Hiermee wordt een IoT Central-toepassing gemaakt of bijgewerkt |
> | Actie | Microsoft.IoTCentral/operations/read | Hiermee worden alle beschik bare bewerkingen voor IoT Central toepassingen opgehaald |
> | Actie | Microsoft.IoTCentral/register/action | Het abonnement voor de resource provider van Azure IoT Central registreren |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.IoTSpaces/Graph/delete | Hiermee wordt de resource van micro soft. IoTSpaces-grafiek verwijderd |
> | Actie | Microsoft.IoTSpaces/Graph/read | Hiermee worden de resource (s) van de micro soft. IoTSpaces-grafiek opgehaald |
> | Actie | Microsoft.IoTSpaces/Graph/write | Resource van micro soft. IoTSpaces Graph maken |
> | Actie | Microsoft.IoTSpaces/register/action | Registreer het abonnement voor de resource provider micro soft. IoTSpaces Graph om het maken van resources in te scha kelen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.KeyVault/checkNameAvailability/read | Hiermee wordt gecontroleerd of de naam van een sleutel kluis geldig is en niet wordt gebruikt |
> | Actie | Microsoft.KeyVault/deletedVaults/read | De eigenschappen van voorlopig verwijderde sleutel kluizen weer geven |
> | Actie | Microsoft.KeyVault/hsmPools/delete | Een HSM-groep verwijderen |
> | Actie | Microsoft.KeyVault/hsmPools/joinVault/action | Een sleutel kluis toevoegen aan een HSM-groep |
> | Actie | Microsoft.KeyVault/hsmPools/read | De eigenschappen van een HSM-groep weer geven |
> | Actie | Microsoft.KeyVault/hsmPools/write | Een nieuwe HSM-groep maken om de eigenschappen van een bestaande HSM-groep bij te werken |
> | Actie | Microsoft.KeyVault/locations/deletedVaults/purge/action | Een voorlopig verwijderde sleutel kluis leegmaken |
> | Actie | Microsoft.KeyVault/locations/deletedVaults/read | De eigenschappen van een voorlopig verwijderde sleutel kluis weer geven |
> | Actie | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Hiermee wordt aan micro soft.-sleutel kluis gemeld dat een virtueel netwerk of subnet wordt verwijderd |
> | Actie | Microsoft.KeyVault/locations/operationResults/read | Het resultaat van een langdurige bewerking controleren |
> | Actie | Microsoft.KeyVault/operations/read | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn op micro soft. |
> | Actie | Microsoft.KeyVault/register/action | Hiermee wordt een abonnement geregistreerd |
> | Actie | Microsoft.KeyVault/unregister/action | Registratie van een abonnement opheffen |
> | Actie | Microsoft.KeyVault/vaults/accessPolicies/write | Een bestaand toegangs beleid bijwerken door samen te voegen of te vervangen of een nieuw toegangs beleid toe te voegen aan een kluis. |
> | Actie | Microsoft.KeyVault/vaults/delete | Een sleutelkluis verwijderen |
> | Actie | Microsoft.KeyVault/vaults/deploy/action | Hiermee wordt toegang tot geheimen in een sleutel kluis ingeschakeld bij het implementeren van Azure-resources |
> | Actie | Microsoft.KeyVault/vaults/eventGridFilters/delete | Hiermee wordt micro soft. EventGrid een melding verzonden dat een abonnement op Key Vault wordt verwijderd |
> | Actie | Microsoft.KeyVault/vaults/eventGridFilters/read | Hiermee wordt micro soft.-sleutel kluis geïnformeerd dat een EventGrid-abonnement voor Key Vault wordt weer gegeven |
> | Actie | Microsoft.KeyVault/vaults/eventGridFilters/write | Hiermee wordt micro soft.-sleutel kluis geïnformeerd dat er een nieuw EventGrid-abonnement voor Key Vault wordt gemaakt |
> | Actie | Microsoft.KeyVault/vaults/read | De eigenschappen van een sleutel kluis weer geven |
> | Actie | Microsoft.KeyVault/vaults/secrets/read | De eigenschappen van een geheim weer geven, maar niet de waarde ervan. |
> | Actie | Microsoft.KeyVault/vaults/secrets/write | Maak een nieuw geheim of werk de waarde van een bestaand geheim bij. |
> | Actie | Microsoft.KeyVault/vaults/write | Een nieuwe sleutel kluis maken of de eigenschappen van een bestaande sleutel kluis bijwerken |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Kusto/Clusters/Activate/action | Start het cluster. |
> | Actie | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Hiermee verwijdert u een gekoppelde database configuratie resourceCopy. |
> | Actie | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Hiermee leest u een gekoppelde database configuratie resourceCopy. |
> | Actie | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Hiermee wordt een gekoppelde database configuratie resourceCopy geschreven. |
> | Actie | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Controleert de beschik baarheid van de cluster naam. |
> | Actie | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Voegt databaseprincipal toe. |
> | Actie | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Controleert de beschik baarheid van namen voor een bepaald type. |
> | Actie | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Hiermee verwijdert u een gegevens verbindingen resourceCopy. |
> | Actie | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Hiermee wordt een gegevens verbindingen-resourceCopy gelezen. |
> | Actie | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Schrijft een gegevens verbindingen resourceCopy. |
> | Actie | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Valideert de database gegevens verbinding. |
> | Actie | Microsoft.Kusto/Clusters/Databases/delete | Hiermee verwijdert u een Data Base-resourceCopy. |
> | Actie | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Hiermee verwijdert u een event hub-verbinding resourceCopy. |
> | Actie | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Hiermee leest u een event hub-verbinding resourceCopy. |
> | Actie | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Schrijft een event hub-verbinding resourceCopy. |
> | Actie | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Hiermee valideert u de data base Event hub-verbinding. |
> | Actie | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Een lijst met data base-principals. |
> | Actie | Micro soft. Kusto/clusters/data bases/PrincipalAssignments/verwijderen | Hiermee verwijdert u een Data Base-Principal-toewijzing resourceCopy. |
> | Actie | Micro soft. Kusto/clusters/data bases/PrincipalAssignments/lezen | Leest een Data Base-Principal-toewijzing resourceCopy. |
> | Actie | Micro soft. Kusto/clusters/data bases/PrincipalAssignments/schrijven | Schrijft een Data Base-Principal-toewijzing resourceCopy. |
> | Actie | Microsoft.Kusto/Clusters/Databases/read | Hiermee leest u een Data Base-resourceCopy. |
> | Actie | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Hiermee verwijdert u databaseprincipal. |
> | Actie | Microsoft.Kusto/Clusters/Databases/write | Schrijft een Data Base-resourceCopy. |
> | Actie | Microsoft.Kusto/Clusters/Deactivate/action | Hiermee wordt het cluster gestopt. |
> | Actie | Microsoft.Kusto/Clusters/delete | Hiermee verwijdert u een cluster-resourceCopy. |
> | Actie | Micro soft. Kusto/clusters/DetachFollowerDatabases/actie | Koppelt de data bases van de opvolger los. |
> | Actie | Micro soft. Kusto/clusters/DiagnoseVirtualNetwork/actie | Diagnosticeert de status van de netwerk verbinding voor externe bronnen waarop de service is depedent. |
> | Actie | Micro soft. Kusto/clusters/ListFollowerDatabases/actie | Een lijst met de data bases van de follow-out. |
> | Actie | Microsoft.Kusto/Clusters/read | Hiermee wordt een cluster-resourceCopy gelezen. |
> | Actie | Microsoft.Kusto/Clusters/SKUs/read | Hiermee leest u een cluster-SKU resourceCopy. |
> | Actie | Microsoft.Kusto/Clusters/Start/action | Start het cluster. |
> | Actie | Microsoft.Kusto/Clusters/Stop/action | Hiermee wordt het cluster gestopt. |
> | Actie | Microsoft.Kusto/Clusters/write | Schrijft een cluster-resourceCopy. |
> | Actie | Microsoft.Kusto/Locations/CheckNameAvailability/action | Controleert de beschik baarheid van resourceCopy-namen. |
> | Actie | Micro soft. Kusto/locaties/GetNetworkPolicies/actie | Hiermee wordt het netwerk beleid voor intenties opgehaald |
> | Actie | Microsoft.Kusto/locations/operationresults/read | Lees bewerkingen resourceCopys |
> | Actie | Micro soft. Kusto/Operations/lezen | Lees bewerkingen resourceCopys |
> | Actie | Micro soft. Kusto/REGI ster/actie | Registratie actie abonnement |
> | Actie | Microsoft.Kusto/Register/action | Hiermee wordt het abonnement geregistreerd bij de resource provider Kusto. |
> | Actie | Micro soft. Kusto/Sku's/lezen | Hiermee leest u een SKU-resourceCopy. |
> | Actie | Micro soft. Kusto/registratie/actie | Hiermee wordt de registratie van het abonnement bij de Kusto-resource provider ongedaan gemaakt. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.LabServices/labAccounts/CreateLab/action | Een lab maken in een Lab-account. |
> | Actie | Microsoft.LabServices/labAccounts/delete | Lab-accounts verwijderen. |
> | Actie | Microsoft.LabServices/labAccounts/galleryImages/delete | Galerie afbeeldingen verwijderen. |
> | Actie | Microsoft.LabServices/labAccounts/galleryImages/read | Galerie afbeeldingen lezen. |
> | Actie | Microsoft.LabServices/labAccounts/galleryImages/write | Galerie afbeeldingen toevoegen of wijzigen. |
> | Actie | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Informatie over regionale Beschik baarheid ophalen voor elke grootte categorie die is geconfigureerd met een Lab-account |
> | Actie | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Gebruikers toevoegen aan een Lab |
> | Actie | Microsoft.LabServices/labAccounts/labs/delete | Labs verwijderen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Omgevings instelling verwijderen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Omgevingen verwijderen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Omgevingen lezen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Hiermee wordt het gebruikers wachtwoord opnieuw ingesteld op een omgeving |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Start een omgeving door alle resources in de omgeving te starten. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Stopt een omgeving door alle resources in de omgeving te stoppen |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Omgevingen toevoegen of wijzigen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Voorziet in het inrichten van vereiste resources voor een omgevings instelling op basis van de huidige status van de instelling lab/environment. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Omgevings instelling lezen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Hiermee stelt u het wacht woord voor de virtuele machine van de sjabloon opnieuw in. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Hiermee wordt de huidige sjabloon afbeelding opgeslagen in de gedeelde galerie in het lab-account |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Schema's verwijderen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Planningen lezen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Schema's toevoegen of wijzigen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Start een sjabloon door alle resources in de sjabloon te starten. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Hiermee stopt u een sjabloon door alle resources in de sjabloon te stoppen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Omgevings instelling toevoegen of wijzigen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/read | Lees Labs. |
> | Actie | Microsoft.LabServices/labAccounts/labs/SendEmail/action | E-mail met registratie koppeling naar het lab verzenden |
> | Actie | Microsoft.LabServices/labAccounts/labs/users/delete | Gebruikers verwijderen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/users/read | Gebruikers lezen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/users/write | Gebruikers toevoegen of wijzigen. |
> | Actie | Microsoft.LabServices/labAccounts/labs/write | Labs toevoegen of wijzigen. |
> | Actie | Microsoft.LabServices/labAccounts/read | Lab-accounts lezen. |
> | Actie | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Verwijder sharedgalleries. |
> | Actie | Microsoft.LabServices/labAccounts/sharedGalleries/read | Lees sharedgalleries. |
> | Actie | Microsoft.LabServices/labAccounts/sharedGalleries/write | Sharedgalleries toevoegen of wijzigen. |
> | Actie | Microsoft.LabServices/labAccounts/sharedImages/delete | Verwijder sharedimages. |
> | Actie | Microsoft.LabServices/labAccounts/sharedImages/read | Lees sharedimages. |
> | Actie | Microsoft.LabServices/labAccounts/sharedImages/write | Sharedimages toevoegen of wijzigen. |
> | Actie | Microsoft.LabServices/labAccounts/write | Lab-accounts toevoegen of wijzigen. |
> | Actie | Microsoft.LabServices/locations/operations/read | Lees bewerkingen. |
> | Actie | Microsoft.LabServices/register/action | Hiermee wordt het abonnement geregistreerd |
> | Actie | Microsoft.LabServices/users/ListAllEnvironments/action | Alle omgevingen voor de gebruiker weer geven |
> | Actie | Microsoft.LabServices/users/Register/action | Een gebruiker registreren bij een beheerd Lab |
> | Actie | Microsoft.LabServices/users/ResetPassword/action | Hiermee wordt het gebruikers wachtwoord opnieuw ingesteld op een omgeving |
> | Actie | Microsoft.LabServices/users/StartEnvironment/action | Start een omgeving door alle resources in de omgeving te starten. |
> | Actie | Microsoft.LabServices/users/StopEnvironment/action | Stopt een omgeving door alle resources in de omgeving te stoppen |
> | Actie | Micro soft. LabServices/users/UserSettings/Action | Hiermee worden persoonlijke gebruikers instellingen bijgewerkt en geretourneerd. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Logic/integrationAccounts/agreements/delete | Hiermee verwijdert u de overeenkomst in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Hiermee wordt de call back-URL voor de overeenkomst inhoud opgehaald in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/agreements/read | Hiermee leest u de overeenkomst in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/agreements/write | Hiermee wordt de overeenkomst in het integratie account gemaakt of bijgewerkt. |
> | Actie | Microsoft.Logic/integrationAccounts/assemblies/delete | Hiermee verwijdert u de assembly in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Hiermee wordt de call back-URL opgehaald voor assembly-inhoud in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/assemblies/read | Hiermee leest u de assembly in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/assemblies/write | Hiermee wordt de assembly in het integratie account gemaakt of bijgewerkt. |
> | Actie | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Hiermee verwijdert u de batch configuratie in het integratie account. |
> | Actie | Micro soft. Logic/integrationAccounts/batchConfigurations/lezen | Hiermee leest u de batch configuratie in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Hiermee wordt de batch configuratie in het integratie account gemaakt of bijgewerkt. |
> | Actie | Microsoft.Logic/integrationAccounts/certificates/delete | Hiermee verwijdert u het certificaat in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/certificates/read | Hiermee leest u het certificaat in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/certificates/write | Hiermee wordt het certificaat in het integratie account gemaakt of bijgewerkt. |
> | Actie | Microsoft.Logic/integrationAccounts/delete | Hiermee verwijdert u het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/join/action | Koppelt aan het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Hiermee wordt de call back-URL opgehaald voor het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Hiermee worden de sleutels in de sleutel kluis opgehaald. |
> | Actie | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Registreert de tracerings gebeurtenissen in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/maps/delete | Hiermee verwijdert u de kaart in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Hiermee wordt de call back-URL opgehaald voor de toewijzing van inhoud in het integratie account. |
> | Actie | Micro soft. Logic/integrationAccounts/Maps/lezen | Hiermee leest u de kaart in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/maps/write | Hiermee wordt de kaart in het integratie account gemaakt of bijgewerkt. |
> | Actie | Microsoft.Logic/integrationAccounts/partners/delete | Hiermee verwijdert u de partner in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Hiermee wordt de call back-URL voor partner inhoud opgehaald in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/partners/read | Hiermee leest u de partner in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/partners/write | Hiermee wordt de partner in het integratie account gemaakt of bijgewerkt. |
> | Actie | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Hiermee worden de logboek definities van het integratie account gelezen. |
> | Actie | Microsoft.Logic/integrationAccounts/read | Hiermee leest u het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Hiermee worden de toegangs sleutel geheimen opnieuw gegenereerd. |
> | Actie | Micro soft. Logic/integrationAccounts/rosettaNetProcessConfigurations/verwijderen | Hiermee verwijdert u de RosettaNet-proces configuratie in het integratie account. |
> | Actie | Micro soft. Logic/integrationAccounts/rosettaNetProcessConfigurations/lezen | Hiermee leest u de RosettaNet-proces configuratie in het integratie account. |
> | Actie | Micro soft. Logic/integrationAccounts/rosettaNetProcessConfigurations/schrijven | Hiermee wordt de RosettaNet-proces configuratie in het integratie account gemaakt of bijgewerkt. |
> | Actie | Microsoft.Logic/integrationAccounts/schemas/delete | Hiermee verwijdert u het schema in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Hiermee wordt de call back-URL opgehaald voor schema-inhoud in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/schemas/read | Hiermee leest u het schema in het integratie account. |
> | Actie | Microsoft.Logic/integrationAccounts/schemas/write | Hiermee wordt het schema in het integratie account gemaakt of bijgewerkt. |
> | Actie | Microsoft.Logic/integrationAccounts/sessions/delete | Hiermee verwijdert u de sessie in het integratie account. |
> | Actie | Micro soft. Logic/integrationAccounts/sessies/lezen | Hiermee leest u de batch configuratie in het integratie account. |
> | Actie | Micro soft. Logic/integrationAccounts/sessies/schrijven | Hiermee wordt de sessie in het integratie account gemaakt of bijgewerkt. |
> | Actie | Microsoft.Logic/integrationAccounts/write | Hiermee wordt het integratie account gemaakt of bijgewerkt. |
> | Actie | Micro soft. Logic/integrationServiceEnvironments/availableManagedApis/lezen | Hiermee wordt de beschik bare beheerde Api's van de integratie service omgeving gelezen. |
> | Actie | Microsoft.Logic/integrationServiceEnvironments/delete | Hiermee verwijdert u de integratie service omgeving. |
> | Actie | Microsoft.Logic/integrationServiceEnvironments/join/action | Voegt de Integratieserviceomgeving toe. |
> | Actie | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Hiermee wordt de beheerde API-bewerking van de Integration service Environment gelezen. |
> | Actie | Micro soft. Logic/integrationServiceEnvironments/Beheerdeapi's/samen voegen/actie | Voegt de Integratieserviceomgeving Managed API toe. |
> | Actie | Micro soft. Logic/integrationServiceEnvironments/Beheerdeapi's/operationStatuses/lezen | Hiermee wordt de bewerkings status van de Managed API van de integratie service omgeving gelezen. |
> | Actie | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Hiermee wordt de beheerde API van de Integration service Environment gelezen. |
> | Actie | Micro soft. Logic/integrationServiceEnvironments/Beheerdeapi's/schrijven | Hiermee wordt de beheerde API voor de integratie service omgeving gemaakt of bijgewerkt. |
> | Actie | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Hiermee wordt de bewerkings status van de integratie service omgeving gelezen. |
> | Actie | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de metrische definities van de integratie service omgeving gelezen. |
> | Actie | Microsoft.Logic/integrationServiceEnvironments/read | Hiermee wordt de integratie service omgeving gelezen. |
> | Actie | Microsoft.Logic/integrationServiceEnvironments/write | Hiermee wordt de integratie service omgeving gemaakt of bijgewerkt. |
> | Actie | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Hiermee haalt u de werk stroom aanbevolen bewerkings groepen. |
> | Actie | Microsoft.Logic/locations/workflows/validate/action | Valideert de werk stroom. |
> | Actie | Microsoft.Logic/operations/read | Hiermee wordt de bewerking opgehaald. |
> | Actie | Microsoft.Logic/register/action | Hiermee wordt de resource provider van micro soft. Logic geregistreerd voor een bepaald abonnement. |
> | Actie | Microsoft.Logic/workflows/accessKeys/delete | Hiermee verwijdert u de toegangs sleutel. |
> | Actie | Microsoft.Logic/workflows/accessKeys/list/action | Geeft een lijst van de toegangs sleutel geheimen. |
> | Actie | Microsoft.Logic/workflows/accessKeys/read | Hiermee wordt de toegangs sleutel gelezen. |
> | Actie | Microsoft.Logic/workflows/accessKeys/regenerate/action | Hiermee worden de toegangs sleutel geheimen opnieuw gegenereerd. |
> | Actie | Microsoft.Logic/workflows/accessKeys/write | Hiermee wordt de toegangs sleutel gemaakt of bijgewerkt. |
> | Actie | Micro soft. Logic/werk stromen/verwijderen | Hiermee verwijdert u de werk stroom. |
> | Actie | Micro soft. Logic/werk stromen/detectoren/lezen | Hiermee wordt de werk stroom detectie gelezen. |
> | Actie | Microsoft.Logic/workflows/disable/action | Hiermee schakelt u de werk stroom uit. |
> | Actie | Microsoft.Logic/workflows/enable/action | Hiermee wordt de werkstroom ingeschakeld. |
> | Actie | Microsoft.Logic/workflows/listCallbackUrl/action | Hiermee wordt de call back-URL voor de werk stroom opgehaald. |
> | Actie | Microsoft.Logic/workflows/listSwagger/action | Hiermee worden de Swagger-definities van de werk stroom opgehaald. |
> | Actie | Microsoft.Logic/workflows/move/action | Verplaatst de werk stroom van de bestaande abonnements-id, resource groep en/of naam naar een andere abonnements-id, resource groep en/of naam. |
> | Actie | Micro soft. Logic/werk stromen/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee worden de diagnostische instellingen van de werk stroom gelezen. |
> | Actie | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling van de werk stroom gemaakt of bijgewerkt. |
> | Actie | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Hiermee worden de definities van werk stroom logboeken gelezen. |
> | Actie | Micro soft. Logic/werk stromen/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities van de werk stroom gelezen. |
> | Actie | Microsoft.Logic/workflows/read | Hiermee wordt de werk stroom gelezen. |
> | Actie | Microsoft.Logic/workflows/regenerateAccessKey/action | Hiermee worden de toegangs sleutel geheimen opnieuw gegenereerd. |
> | Actie | Microsoft.Logic/workflows/run/action | Start een uitvoering van de werk stroom. |
> | Actie | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Hiermee worden de expressie traceringen voor de uitvoerings actie van de werk stroom opgehaald. |
> | Actie | Microsoft.Logic/workflows/runs/actions/read | Hiermee wordt de uitvoerings actie van de werk stroom gelezen. |
> | Actie | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Hiermee worden de expressie traceringen voor de herhaling van de uitvoerings actie van de werk stroom opgehaald. |
> | Actie | Microsoft.Logic/workflows/runs/actions/repetitions/read | Hiermee wordt de herhaling van de uitvoerings actie van de werk stroom gelezen. |
> | Actie | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Hiermee wordt de aanvraag geschiedenis van de herhalings actie voor het uitvoeren van de werk stroom gelezen. |
> | Actie | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Hiermee wordt de aanvraag geschiedenis van de actie werk stroom uitvoeren gelezen. |
> | Actie | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Hiermee wordt de herhaling van het uitvoerings actie bereik van de werk stroom gelezen. |
> | Actie | Microsoft.Logic/workflows/runs/cancel/action | Annuleert de uitvoering van een werk stroom. |
> | Actie | Micro soft. Logic/werk stromen/uitvoeringen/verwijderen | Hiermee verwijdert u een uitvoering van een werk stroom. |
> | Actie | Micro soft. Logic/werk stromen/uitvoeringen/bewerkingen/lezen | Hiermee wordt de status van de uitvoerings bewerking van de werk stroom gelezen. |
> | Actie | Micro soft. Logic/werk stromen/uitvoeringen/lezen | Hiermee wordt de uitvoering van de werk stroom gelezen. |
> | Actie | Microsoft.Logic/workflows/suspend/action | Hiermee wordt de werk stroom onderbroken. |
> | Actie | Microsoft.Logic/workflows/triggers/histories/read | Hiermee leest u de trigger geschiedenis. |
> | Actie | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Hiermee wordt de werk stroom trigger opnieuw verzonden. |
> | Actie | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Hiermee wordt de call back-URL opgehaald voor de trigger. |
> | Actie | Microsoft.Logic/workflows/triggers/read | Hiermee leest u de trigger. |
> | Actie | Microsoft.Logic/workflows/triggers/reset/action | Hiermee wordt de trigger opnieuw ingesteld. |
> | Actie | Microsoft.Logic/workflows/triggers/run/action | De trigger wordt uitgevoerd. |
> | Actie | Microsoft.Logic/workflows/triggers/setState/action | Hiermee stelt u de status van de trigger in. |
> | Actie | Microsoft.Logic/workflows/validate/action | Valideert de werk stroom. |
> | Actie | Micro soft. Logic/werk stromen/versies/lezen | Hiermee wordt de werk stroom versie gelezen. |
> | Actie | Micro soft. Logic/werk stromen/versies/triggers/listCallbackUrl/actie | Hiermee wordt de call back-URL opgehaald voor de trigger. |
> | Actie | Micro soft. Logic/werk stromen/schrijven | Hiermee wordt de werk stroom gemaakt of bijgewerkt. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Een Machine Learning Toezeggings plan koppeling verplaatsen |
> | Actie | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Een Machine Learning Toezeggings plan koppeling lezen |
> | Actie | Microsoft.MachineLearning/commitmentPlans/delete | Een Machine Learning Toezeggings plan verwijderen |
> | Actie | Microsoft.MachineLearning/commitmentPlans/join/action | Een Machine Learning Toezeggings plan samen voegen |
> | Actie | Microsoft.MachineLearning/commitmentPlans/read | Een Machine Learning Toezeggings plan lezen |
> | Actie | Microsoft.MachineLearning/commitmentPlans/write | Een Machine Learning Toezeggings plan maken of bijwerken |
> | Actie | Microsoft.MachineLearning/locations/operationresults/read | Resultaat van een Machine Learning bewerking ophalen |
> | Actie | Microsoft.MachineLearning/locations/operationsstatus/read | De status van een doorlopende Machine Learning bewerking ophalen |
> | Actie | Microsoft.MachineLearning/operations/read | Machine Learning-bewerkingen ophalen |
> | Actie | Microsoft.MachineLearning/register/action | Hiermee wordt het abonnement voor de resource provider van de machine learning-webservice geregistreerd en wordt het maken van webservices ingeschakeld. |
> | Actie | Microsoft.MachineLearning/skus/read | Sku's van Machine Learning Toezeggings plan ophalen |
> | Actie | Microsoft.MachineLearning/webServices/action | Eigenschappen van regionale webservice maken voor ondersteunde regio's |
> | Actie | Microsoft.MachineLearning/webServices/delete | Alle Machine Learning-webservice verwijderen |
> | Actie | Microsoft.MachineLearning/webServices/listkeys/read | Sleutels naar een Machine Learning-webservice ophalen |
> | Actie | Microsoft.MachineLearning/webServices/read | Alle Machine Learning-webservice lezen |
> | Actie | Microsoft.MachineLearning/webServices/write | Een Machine Learning-webservice maken of bijwerken |
> | Actie | Microsoft.MachineLearning/Workspaces/delete | Machine Learning-werkruimte verwijderen |
> | Actie | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Lijst met sleutels voor een Machine Learning-werkruimte |
> | Actie | Microsoft.MachineLearning/Workspaces/read | Alle Machine Learning-werkruimte lezen |
> | Actie | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Sleutels van het opslag account die zijn geconfigureerd voor een Machine Learning-werkruimte, worden opnieuw gesynchroniseerd |
> | Actie | Microsoft.MachineLearning/Workspaces/write | Machine Learning-werkruimte maken of bijwerken |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Hiermee wordt de status van een bepaalde reken bewerking opgehaald |
> | Actie | Micro soft. MachineLearningServices/locaties/quota's/lezen | Hiermee worden de momenteel toegewezen werkruimte quota opgehaald op basis van VMFamily. |
> | Actie | Micro soft. MachineLearningServices/locaties/updateQuotas/actie | Update quota voor elke VM-serie in de werk ruimte. |
> | Actie | Microsoft.MachineLearningServices/locations/usages/read | Gebruiks rapport voor AML-reken resources in een abonnement |
> | Actie | Microsoft.MachineLearningServices/locations/vmsizes/read | Ondersteunde VM-grootten ophalen |
> | Actie | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Hiermee wordt de status van een bepaalde werkruimte bewerking opgehaald |
> | Actie | Microsoft.MachineLearningServices/register/action | Hiermee wordt het abonnement voor de Machine Learning Services resource provider geregistreerd |
> | Actie | Microsoft.MachineLearningServices/workspaces/computes/delete | Hiermee worden de reken resources in Machine Learning Services werk ruimte (n) verwijderd |
> | Actie | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Geheimen vermelden voor reken resources in Machine Learning Services werk ruimte |
> | Actie | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Knoop punten voor Compute-resource in Machine Learning Services werk ruimte weer geven |
> | Actie | Microsoft.MachineLearningServices/workspaces/computes/read | Hiermee worden de reken resources in Machine Learning Services werk ruimte (n) opgehaald |
> | Actie | Microsoft.MachineLearningServices/workspaces/computes/write | Hiermee worden de reken resources in Machine Learning Services werk ruimte (n) gemaakt of bijgewerkt |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/datadriftdetectors/lezen | Hiermee worden gegevens drift-detectoren opgehaald in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/datadriftdetectors/schrijven | Hiermee worden gegevens drift gedetecteerd of bijgewerkt in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens sets/geregistreerd/verwijderen | Hiermee verwijdert u geregistreerde gegevens sets in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens sets/geregistreerd/preview/lezen | Hiermee wordt een voor beeld van de gegevensset voor geregistreerde gegevens sets in Machine Learning Services werk ruimte (n) opgehaald |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens sets/geregistreerd/Profiel/lezen | Hiermee worden de gegevensset-profielen voor geregistreerde gegevens sets in Machine Learning Services werk ruimte (n) opgehaald |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens sets/geregistreerd/profiel/schrijven | Hiermee worden de profielen van de gegevensset in Machine Learning Services werk ruimte (n) gemaakt of bijgewerkt |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens sets/geregistreerd/lezen | Hiermee haalt u geregistreerde gegevens sets op in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens sets/geregistreerd/schrijven | Hiermee worden geregistreerde gegevens sets gemaakt of bijgewerkt in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens sets/niet-geregistreerd/verwijderen | Verwijdert niet-geregistreerde gegevens sets in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens sets/niet-geregistreerd/voor beeld/lezen | Hiermee wordt een voor beeld van de gegevensset opgehaald voor niet-geregistreerde gegevens sets in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens sets/niet-geregistreerd/Profiel/lezen | Hiermee worden dataset-profielen opgehaald voor niet-geregistreerde gegevens sets in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens sets/niet geregistreerd/lezen | Hiermee worden niet-geregistreerde gegevens sets opgehaald in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens sets/niet-geregistreerd/schrijven | Hiermee worden niet-geregistreerde gegevens sets gemaakt of bijgewerkt in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens opslag/verwijderen | Hiermee verwijdert u gegevens opslag in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens opslag/lezen | Haalt gegevens opslag op in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/gegevens opslag/schrijven | Maakt of werkt gegevens opslag in Machine Learning Services werk ruimte (n) |
> | Actie | Microsoft.MachineLearningServices/workspaces/delete | Hiermee verwijdert u de Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/eind punten/-pijp lijnen/lezen | Hiermee worden gepubliceerde pijp lijnen en pijplijn eindpunten in Machine Learning Services werk ruimte (n) opgehaald |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/eind punten/-pijp lijnen/schrijven | Hiermee worden gepubliceerde pijp lijnen en pijplijn eindpunten gemaakt of bijgewerkt in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/omgevingen/samen stellen/actie | Bouwt omgevingen in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/omgevingen/lezen | Hiermee worden omgevingen in Machine Learning Services werk ruimte (n) opgehaald |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/omgevingen/readSecrets/actie | Haalt omgevingen op met geheimen in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/omgevingen/schrijven | Hiermee worden omgevingen gemaakt of bijgewerkt in Machine Learning Services werk ruimte (n) |
> | Actie | Micro soft. MachineLearningServices/werk ruimten/eventGridFilters/lezen | Een Event Grid filter voor een bepaalde werk ruimte ophalen |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/experimenten/verwijderen | Hiermee verwijdert u experimenten in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimtes/experimenten/lezen | Hiermee worden experimenten in Machine Learning Services werk ruimte (n) opgehaald |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/experimenten/uitvoeringen/lezen | Wordt uitgevoerd in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/experimenten/runs/scriptRun/Submit/actie | Maakt of update script uitvoeringen in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/experimenten/uitvoeringen/schrijven | Maakt of updates wordt uitgevoerd in Machine Learning Services werk ruimte (n) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Hiermee worden experimenten in Machine Learning Services werk ruimte (n) gemaakt of bijgewerkt |
> | Actie | Microsoft.MachineLearningServices/workspaces/listKeys/action | Geheimen voor een Machine Learning Services-werk ruimte vermelden |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/meta gegevens/artefacten/verwijderen | Artefacten in Machine Learning Services werk ruimte (n) verwijderen |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/meta gegevens/artefacten/lezen | Hiermee worden artefacten in Machine Learning Services werk ruimte (n) opgehaald |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/meta gegevens/artefacten/schrijven | Maakt of werkt artefacten in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/meta gegevens/moment opnamen/verwijderen | Moment opnamen in Machine Learning Services werk ruimte (n) verwijderen |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/meta gegevens/moment opnamen/lezen | Hiermee worden moment opnamen in Machine Learning Services werk ruimte (n) opgehaald |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/meta gegevens/moment opnamen/schrijven | Maakt moment opnamen of werkt deze bij in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/modellen/verwijderen | Hiermee verwijdert u modellen in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/modellen/downloaden/actie | Hiermee worden modellen gedownload in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/modellen/pakket/actie | Modeleert modellen in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/modellen/lezen | Hiermee worden modellen in Machine Learning Services werk ruimte (n) opgehaald |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/modellen/schrijven | Hiermee worden modellen gemaakt of bijgewerkt in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/modules/lezen | Hiermee worden modules opgehaald in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/modules/schrijven | Hiermee wordt een module gemaakt of bijgewerkt in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/pipelinedrafts/verwijderen | Hiermee verwijdert u de concepten van de pijp lijn in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/pipelinedrafts/lezen | Hiermee worden de concepten van de pijp lijn in Machine Learning Services werk ruimte (n) opgehaald |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/pipelinedrafts/schrijven | Maakt of bijwerkt pijplijn concepten in Machine Learning Services werkruimte (s) |
> | Actie | Microsoft.MachineLearningServices/workspaces/read | Hiermee worden de Machine Learning Services werk ruimte (n) opgehaald |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/Services/ACI/verwijderen | Hiermee worden ACI-services verwijderd uit Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/Services/ACI/listkeys ophalen/actie | Lijst met sleutels voor ACI-Services in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/Services/ACI/schrijven | Hiermee worden ACI-Services gemaakt of bijgewerkt in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/Services/AKS/devtest/verwijderen | Hiermee verwijdert u devtest AKS-Services in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/Services/AKS/devtest/listkeys ophalen/actie | Lijst met sleutels voor devtest AKS Services in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/Services/AKS/devtest/Score/actie | Scores devtest AKS Services in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/Services/AKS/devtest/schrijven | Hiermee worden devtest AKS-Services gemaakt of bijgewerkt in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/Services/AKS/Prod/verwijderen | Hiermee verwijdert u de Prod AKS Services in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/Services/AKS/Prod/listkeys ophalen/actie | Lijst met sleutels voor Prod AKS Services in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/Services/AKS/Prod/Score/actie | Scores voor Prod AKS Services in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/Services/AKS/Prod/schrijven | Hiermee worden Prod AKS Services gemaakt of bijgewerkt in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/services/lezen | Hiermee worden services in Machine Learning Services werk ruimte (n) opgehaald |
> | Actie | Microsoft.MachineLearningServices/workspaces/write | Hiermee wordt een Machine Learning Services werk ruimte (n) gemaakt of bijgewerkt |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. ManagedIdentity/Identities/lezen | Hiermee wordt een bestaande systeem-id opgehaald |
> | Actie | Micro soft. ManagedIdentity/Operations/lezen | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn voor de resource provider micro soft. ManagedIdentity |
> | Actie | Microsoft.ManagedIdentity/register/action | Hiermee wordt het abonnement voor de resource provider van de beheerde identiteit geregistreerd |
> | Actie | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | RBAC-actie voor het toewijzen van een bestaande door de gebruiker toegewezen identiteit aan een resource |
> | Actie | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Hiermee wordt een bestaande door de gebruiker toegewezen identiteit verwijderd |
> | Actie | Microsoft.ManagedIdentity/userAssignedIdentities/read | Hiermee wordt een bestaande door de gebruiker toegewezen identiteit opgehaald |
> | Actie | Microsoft.ManagedIdentity/userAssignedIdentities/write | Hiermee wordt een nieuwe door de gebruiker toegewezen identiteit gemaakt of worden de labels bijgewerkt die zijn gekoppeld aan een bestaande door de gebruiker toegewezen identiteit |

## <a name="microsoftmanagedservices"></a>Micro soft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. ManagedServices/marketplaceRegistrationDefinitions/lezen | Hiermee haalt u een lijst met definities voor beheerde services registratie op. |
> | Actie | Micro soft. ManagedServices/Operations/lezen | Hiermee wordt een lijst met beheerde services-bewerkingen opgehaald. |
> | Actie | Micro soft. ManagedServices/operationStatuses/lezen | Hiermee wordt de bewerkings status van de resource gelezen. |
> | Actie | Micro soft. ManagedServices/REGI ster/actie | Meld u aan bij beheerde services. |
> | Actie | Micro soft. ManagedServices/registrationAssignments/verwijderen | Hiermee wordt de registratie toewijzing van beheerde services verwijderd. |
> | Actie | Micro soft. ManagedServices/registrationAssignments/lezen | Hiermee wordt een lijst met beheerde services registratie toewijzingen opgehaald. |
> | Actie | Micro soft. ManagedServices/registrationAssignments/schrijven | De registratie toewijzing voor beheerde services toevoegen of wijzigen. |
> | Actie | Micro soft. ManagedServices/registrationDefinitions/verwijderen | Hiermee wordt de registratie definitie van beheerde services verwijderd. |
> | Actie | Micro soft. ManagedServices/registrationDefinitions/lezen | Hiermee haalt u een lijst met definities voor beheerde services registratie op. |
> | Actie | Micro soft. ManagedServices/registrationDefinitions/schrijven | De registratie definitie voor beheerde services toevoegen of wijzigen. |
> | Actie | Micro soft. ManagedServices/registratie/actie | Registratie bij beheerde services opheffen. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Management/checkNameAvailability/action | Hiermee wordt gecontroleerd of de opgegeven naam van de beheer groep geldig en uniek is. |
> | Actie | Microsoft.Management/getEntities/action | Alle entiteiten (Beheergroepen, abonnementen enz.) weer geven voor de geverifieerde gebruiker. |
> | Actie | Microsoft.Management/managementGroups/delete | Beheer groep verwijderen. |
> | Actie | Micro soft. Management/managementGroups/afstammelingen/lezen | Hiermee worden alle descendanten (Beheergroepen, abonnementen) van een beheer groep opgehaald. |
> | Actie | Microsoft.Management/managementGroups/read | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | Actie | Microsoft.Management/managementGroups/subscriptions/delete | Het abonnement van de beheer groep wordt ontkoppeld. |
> | Actie | Microsoft.Management/managementGroups/subscriptions/write | Het bestaande abonnement wordt gekoppeld aan de beheer groep. |
> | Actie | Microsoft.Management/managementGroups/write | Een beheer groep maken of bijwerken. |
> | Actie | Microsoft.Management/register/action | Het opgegeven abonnement registreren bij micro soft. Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Hiermee wordt het lezen van gegevens toegang verleend aan een Maps-account. |
> | Actie | Microsoft.Maps/accounts/delete | Een Maps-account verwijderen. |
> | Actie | Microsoft.Maps/accounts/eventGridFilters/delete | Een Event Grid filter verwijderen |
> | Actie | Microsoft.Maps/accounts/eventGridFilters/read | Een Event Grid filter ophalen |
> | Actie | Microsoft.Maps/accounts/eventGridFilters/write | Een Event Grid filter maken of bijwerken |
> | Actie | Microsoft.Maps/accounts/listKeys/action | Lijst met account sleutels voor overzichten |
> | Actie | Microsoft.Maps/accounts/read | Een Maps-account ophalen. |
> | Actie | Microsoft.Maps/accounts/regenerateKey/action | Nieuwe Maps-account primaire of secundaire sleutel genereren |
> | Actie | Microsoft.Maps/accounts/write | Een Maps-account maken of bijwerken. |
> | Actie | Microsoft.Maps/operations/read | De provider bewerkingen lezen |
> | Actie | Microsoft.Maps/register/action | De provider registreren |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Retourneert een overeenkomst. |
> | Actie | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Accepteert een ondertekende overeenkomst. |
> | Actie | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Hiermee importeert u een installatie kopie naar de ACR van de eind gebruiker. |
> | Actie | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Retourneert een configuratie. |
> | Actie | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Hiermee wordt een configuratie opgeslagen. |
> | Actie | Microsoft.Marketplace/register/action | Hiermee wordt de resource provider micro soft. Marketplace geregistreerd in het abonnement. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.MarketplaceApps/ClassicDevServices/delete | Er wordt een Verwijder bewerking uitgevoerd op een klassieke dev service-resource. |
> | Actie | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Hiermee haalt u een service voor het beheer van bron-en klassieke resources. |
> | Actie | Micro soft. MarketplaceApps/ClassicDevServices/listSingleSignOnToken/Action | Hiermee wordt de URL voor eenmalige aanmelding voor een klassieke dev service opgehaald. |
> | Actie | Microsoft.MarketplaceApps/ClassicDevServices/read | Voert een GET-bewerking uit op een klassieke dev service. |
> | Actie | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Hiermee genereert u een klassieke service voor het beheren van resource beheer sleutels. |
> | Actie | Microsoft.MarketplaceApps/Operations/read | Lees de bewerkingen voor alle resource typen. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Een overeenkomst voor een bepaald Marketplace-item annuleren |
> | Actie | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Een overeenkomst voor een gegeven Marketplace-item retour neren |
> | Actie | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Een overeenkomst voor een bepaald Marketplace-item ondertekenen |
> | Actie | Microsoft.MarketplaceOrdering/agreements/read | Alle overeenkomsten voor het opgegeven abonnement retour neren |
> | Actie | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Een overeenkomst voor een bepaald item van een virtuele Marketplace-machine verkrijgen |
> | Actie | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Een overeenkomst voor een bepaald item van een virtuele Marketplace-machine ondertekenen of annuleren |
> | Actie | Microsoft.MarketplaceOrdering/operations/read | Alle mogelijke bewerkingen in de API weer geven |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Media/checknameavailability/action | Hiermee wordt gecontroleerd of een Media Services account naam beschikbaar is |
> | Actie | Microsoft.Media/mediaservices/accountfilters/delete | Een account filter verwijderen |
> | Actie | Microsoft.Media/mediaservices/accountfilters/read | Een account filter lezen |
> | Actie | Microsoft.Media/mediaservices/accountfilters/write | Een account filter maken of bijwerken |
> | Actie | Microsoft.Media/mediaservices/assets/assetfilters/delete | Alle activa filters verwijderen |
> | Actie | Microsoft.Media/mediaservices/assets/assetfilters/read | Alle activa filters lezen |
> | Actie | Microsoft.Media/mediaservices/assets/assetfilters/write | Een Asset-filter maken of bijwerken |
> | Actie | Microsoft.Media/mediaservices/assets/delete | Alle activa verwijderen |
> | Actie | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Versleutelings sleutel voor activa ophalen |
> | Actie | Microsoft.Media/mediaservices/assets/listContainerSas/action | SAS-Url's voor de Asset-container vermelden |
> | Actie | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Streaming-Locators vermelden voor Asset |
> | Actie | Microsoft.Media/mediaservices/assets/read | Alle activa lezen |
> | Actie | Microsoft.Media/mediaservices/assets/write | Activa maken of bijwerken |
> | Actie | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Alle beleids regels voor inhouds sleutels verwijderen |
> | Actie | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Beleids eigenschappen ophalen met geheimen |
> | Actie | Microsoft.Media/mediaservices/contentKeyPolicies/read | Alle beleids regels voor inhouds sleutels lezen |
> | Actie | Microsoft.Media/mediaservices/contentKeyPolicies/write | Beleid voor inhouds sleutels maken of bijwerken |
> | Actie | Microsoft.Media/mediaservices/delete | Een Media Services account verwijderen |
> | Actie | Microsoft.Media/mediaservices/eventGridFilters/delete | Event Grid filter verwijderen |
> | Actie | Microsoft.Media/mediaservices/eventGridFilters/read | Een Event Grid filter lezen |
> | Actie | Microsoft.Media/mediaservices/eventGridFilters/write | Event Grid filter maken of bijwerken |
> | Actie | Microsoft.Media/mediaservices/liveEventOperations/read | Een live gebeurtenis bewerking lezen |
> | Actie | Microsoft.Media/mediaservices/liveEvents/delete | Een live gebeurtenis verwijderen |
> | Actie | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Een live uitvoer verwijderen |
> | Actie | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Een wille keurige live-uitvoer lezen |
> | Actie | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Een live-uitvoer maken of bijwerken |
> | Actie | Microsoft.Media/mediaservices/liveEvents/read | Een live gebeurtenis lezen |
> | Actie | Microsoft.Media/mediaservices/liveEvents/reset/action | Een live gebeurtenis bewerking opnieuw instellen |
> | Actie | Microsoft.Media/mediaservices/liveEvents/start/action | Een live gebeurtenis bewerking starten |
> | Actie | Microsoft.Media/mediaservices/liveEvents/stop/action | Een live gebeurtenis bewerking stoppen |
> | Actie | Microsoft.Media/mediaservices/liveEvents/write | Een live gebeurtenis maken of bijwerken |
> | Actie | Microsoft.Media/mediaservices/liveOutputOperations/read | Een live uitvoer bewerking lezen |
> | Actie | Microsoft.Media/mediaservices/read | Een Media Services account lezen |
> | Actie | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Een streaming-eindpunt bewerking lezen |
> | Actie | Microsoft.Media/mediaservices/streamingEndpoints/delete | Een streaming-eind punt verwijderen |
> | Actie | Microsoft.Media/mediaservices/streamingEndpoints/read | Een streaming-eind punt lezen |
> | Actie | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Een streaming-eindpunt bewerking schalen |
> | Actie | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Een streaming-eindpunt bewerking starten |
> | Actie | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Een streaming-eindpunt bewerking stoppen |
> | Actie | Microsoft.Media/mediaservices/streamingEndpoints/write | Een streaming-eind punt maken of bijwerken |
> | Actie | Microsoft.Media/mediaservices/streamingLocators/delete | Een streaming-Locator verwijderen |
> | Actie | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Inhouds sleutels weer geven |
> | Actie | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Paden weer geven |
> | Actie | Microsoft.Media/mediaservices/streamingLocators/read | Een streaming-Locator lezen |
> | Actie | Microsoft.Media/mediaservices/streamingLocators/write | Een streaming-Locator maken of bijwerken |
> | Actie | Microsoft.Media/mediaservices/streamingPolicies/delete | Een streaming-beleid verwijderen |
> | Actie | Microsoft.Media/mediaservices/streamingPolicies/read | Elk streaming-beleid lezen |
> | Actie | Microsoft.Media/mediaservices/streamingPolicies/write | Een streaming-beleid maken of bijwerken |
> | Actie | Microsoft.Media/mediaservices/syncStorageKeys/action | De opslag sleutels voor een gekoppeld Azure Storage account synchroniseren |
> | Actie | Microsoft.Media/mediaservices/transforms/delete | Trans formatie verwijderen |
> | Actie | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Taak annuleren |
> | Actie | Microsoft.Media/mediaservices/transforms/jobs/delete | Taak verwijderen |
> | Actie | Microsoft.Media/mediaservices/transforms/jobs/read | Een wille keurige taak lezen |
> | Actie | Microsoft.Media/mediaservices/transforms/jobs/write | Een taak maken of bijwerken |
> | Actie | Microsoft.Media/mediaservices/transforms/read | Een trans formatie lezen |
> | Actie | Microsoft.Media/mediaservices/transforms/write | Een trans formatie maken of bijwerken |
> | Actie | Microsoft.Media/mediaservices/write | Een Media Services-account maken of bijwerken |
> | Actie | Microsoft.Media/operations/read | Beschik bare bewerkingen ophalen |
> | Actie | Microsoft.Media/register/action | Hiermee wordt het abonnement voor de Media Services resource provider geregistreerd en wordt het maken van Media Services accounts mogelijk |
> | Actie | Microsoft.Media/unregister/action | Hiermee wordt de registratie van het abonnement voor de Media Services resource provider ongedaan gemaakt |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. migrate/assessmentprojects/assessmentOptions/Read | Hiermee worden de evaluatie opties opgehaald die beschikbaar zijn op de opgegeven locatie |
> | Actie | Microsoft.Migrate/assessmentprojects/assessments/read | Een lijst met evaluaties binnen een project |
> | Actie | Microsoft.Migrate/assessmentprojects/delete | Hiermee wordt het evaluatie project verwijderd |
> | Actie | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | De eigenschappen van een geschatte machine ophalen |
> | Actie | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | Hiermee wordt een evaluatie verwijderd |
> | Actie | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | Hiermee wordt de URL van een evaluatie rapport gedownload |
> | Actie | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Hiermee worden de eigenschappen van een evaluatie opgehaald |
> | Actie | Microsoft.Migrate/assessmentprojects/groups/assessments/write | Hiermee wordt een nieuwe evaluatie gemaakt of een bestaande evaluatie bijgewerkt |
> | Actie | Microsoft.Migrate/assessmentprojects/groups/delete | Hiermee verwijdert u een groep |
> | Actie | Microsoft.Migrate/assessmentprojects/groups/read | De eigenschappen van een groep ophalen |
> | Actie | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Groep bijwerken door computers toe te voegen of te verwijderen |
> | Actie | Microsoft.Migrate/assessmentprojects/groups/write | Hiermee wordt een nieuwe groep gemaakt of een bestaande groep bijgewerkt |
> | Actie | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Hiermee verwijdert u de Hyper-Collector |
> | Actie | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Hiermee worden de eigenschappen van Hyper-Collector opgehaald |
> | Actie | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Hiermee wordt een nieuwe Hyper-Collector gemaakt of een bestaande Hyper-Collector bijgewerkt |
> | Actie | Micro soft. migrate/assessmentprojects/importcollectors/Delete | Hiermee wordt de import verzamelaar verwijderd |
> | Actie | Micro soft. migrate/assessmentprojects/importcollectors/Read | Hiermee worden de eigenschappen van de import Collector opgehaald |
> | Actie | Micro soft. migrate/assessmentprojects/importcollectors/write | Hiermee maakt u een nieuwe import verzamelaar of werkt u een bestaande import Collector bij |
> | Actie | Micro soft. migrate/assessmentprojects/machines/lezen | Hiermee worden de eigenschappen van een machine opgehaald |
> | Actie | Microsoft.Migrate/assessmentprojects/read | Hiermee worden de eigenschappen van het evaluatie project opgehaald |
> | Actie | Micro soft. migrate/assessmentprojects/servercollectors/Read | Hiermee worden de eigenschappen van de server verzamelaar opgehaald |
> | Actie | Micro soft. migrate/assessmentprojects/servercollectors/write | Hiermee maakt u een nieuwe server verzamelaar of werkt u een bestaande server verzamelaar bij |
> | Actie | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | VMware Collector verwijderen |
> | Actie | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Hiermee worden de eigenschappen van VMware Collector opgehaald |
> | Actie | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Hiermee wordt een nieuwe VMware Collector gemaakt of een bestaande VMware Collector bijgewerkt |
> | Actie | Microsoft.Migrate/assessmentprojects/write | Hiermee maakt u een nieuw evaluatie project of werkt u een bestaand evaluatie project bij |
> | Actie | Microsoft.Migrate/locations/assessmentOptions/read | Hiermee worden de evaluatie opties opgehaald die beschikbaar zijn op de opgegeven locatie |
> | Actie | Microsoft.Migrate/locations/checknameavailability/action | Controleert de beschik baarheid van de resource naam voor het gegeven abonnement op de opgegeven locatie |
> | Actie | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Hiermee worden de eigenschappen van een Data Base-exemplaar opgehaald |
> | Actie | Microsoft.Migrate/migrateprojects/Databases/read | Hiermee worden de eigenschappen van een Data Base opgehaald |
> | Actie | Microsoft.Migrate/migrateprojects/delete | Hiermee wordt een migratie project verwijderd |
> | Actie | Microsoft.Migrate/migrateprojects/machines/read | Hiermee worden de eigenschappen van een machine opgehaald |
> | Actie | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | Hiermee wordt een migratie gebeurtenis verwijderd |
> | Actie | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Hiermee worden de eigenschappen van een migratie gebeurtenis opgehaald. |
> | Actie | Microsoft.Migrate/migrateprojects/read | Hiermee worden de eigenschappen van het migratie project opgehaald |
> | Actie | Micro soft. migrate/migrateprojects/RefreshSummary/Action | Hiermee wordt het samen vatting van het migratie project vernieuwd |
> | Actie | Microsoft.Migrate/migrateprojects/registerTool/action | Hulp programma registreren bij een migratie project |
> | Actie | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | De oplossings gegevens van het migratie project opschonen |
> | Actie | Micro soft. migrate/migrateprojects/Solutions/Delete | Hiermee verwijdert u een migratie project oplossing |
> | Actie | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Hiermee wordt de configuratie van de project oplossing voor migratie opgehaald |
> | Actie | Micro soft. migrate/migrateprojects/Solutions/Read | Hiermee worden de eigenschappen van de project-oplossing migreren opgehaald |
> | Actie | Microsoft.Migrate/migrateprojects/solutions/write | Hiermee maakt u een nieuwe oplossing voor het migreren van een project of werkt u een bestaande migratie project oplossing bij |
> | Actie | Microsoft.Migrate/migrateprojects/write | Hiermee maakt u een nieuw migratie project of werkt u een bestaand migratie project bij |
> | Actie | Micro soft. migrate/Operations/Read | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn op micro soft. resource provider migreren |
> | Actie | Microsoft.Migrate/projects/assessments/read | Een lijst met evaluaties binnen een project |
> | Actie | Microsoft.Migrate/projects/delete | Hiermee wordt het project verwijderd |
> | Actie | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | De eigenschappen van een geschatte machine ophalen |
> | Actie | Microsoft.Migrate/projects/groups/assessments/delete | Hiermee wordt een evaluatie verwijderd |
> | Actie | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Hiermee wordt de URL van een evaluatie rapport gedownload |
> | Actie | Microsoft.Migrate/projects/groups/assessments/read | Hiermee worden de eigenschappen van een evaluatie opgehaald |
> | Actie | Microsoft.Migrate/projects/groups/assessments/write | Hiermee wordt een nieuwe evaluatie gemaakt of een bestaande evaluatie bijgewerkt |
> | Actie | Microsoft.Migrate/projects/groups/delete | Hiermee verwijdert u een groep |
> | Actie | Microsoft.Migrate/projects/groups/read | De eigenschappen van een groep ophalen |
> | Actie | Microsoft.Migrate/projects/groups/write | Hiermee wordt een nieuwe groep gemaakt of een bestaande groep bijgewerkt |
> | Actie | Microsoft.Migrate/projects/keys/action | Hiermee worden gedeelde sleutels voor het project opgehaald |
> | Actie | Micro soft. migrate/projectes/machines/lezen | Hiermee worden de eigenschappen van een machine opgehaald |
> | Actie | Microsoft.Migrate/projects/read | Hiermee worden de eigenschappen van een project opgehaald |
> | Actie | Microsoft.Migrate/projects/write | Hiermee wordt een nieuw project gemaakt of een bestaand project bijgewerkt |
> | Actie | Microsoft.Migrate/register/action | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. migrate |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.MixedReality/register/action | Registreert een abonnement voor de resource provider Mixed Reality. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Ruimtelijke ankers maken |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Ruimtelijke ankers verwijderen |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Ruimtelijke beankeringen in de buurt detecteren |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Eigenschappen van ruimtelijke ankers ophalen |
> | Actie | Micro soft. MixedReality/spatialAnchorsAccounts/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor micro soft. MixedReality/spatialAnchorsAccounts opgehaald |
> | Actie | Micro soft. MixedReality/spatialAnchorsAccounts/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor micro soft. MixedReality/spatialAnchorsAccounts gemaakt of bijgewerkt |
> | Actie | Micro soft. MixedReality/spatialAnchorsAccounts/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor micro soft. MixedReality/spatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Ruimtelijke ankers zoeken |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnostische gegevens verzenden om de kwaliteit van de Azure spatiale ankers-service te verbeteren |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Eigenschappen van ruimtelijke ankers bijwerken |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. NetApp/locaties/checkfilepathavailability/actie | Controleren of het bestandspad beschikbaar is |
> | Actie | Micro soft. NetApp/locaties/checknameavailability/actie | Controleren of de resource naam beschikbaar is |
> | Actie | Microsoft.NetApp/locations/operationresults/read | Hiermee wordt een resource van een bewerkings resultaat gelezen. |
> | Actie | Microsoft.NetApp/locations/read | Hiermee wordt een resource voor beschikbaarheids controle gelezen. |
> | Actie | Micro soft. NetApp/netAppAccounts/backupPolicies/verwijderen | Hiermee verwijdert u een bron voor een back-upbeleid. |
> | Actie | Micro soft. NetApp/netAppAccounts/backupPolicies/lezen | Hiermee wordt een bron voor een back-upbeleid gelezen. |
> | Actie | Micro soft. NetApp/netAppAccounts/backupPolicies/write | Schrijft een bron voor een back-upbeleid. |
> | Actie | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Hiermee verwijdert u een groeps resource. |
> | Actie | Microsoft.NetApp/netAppAccounts/capacityPools/read | Hiermee wordt een pool resource gelezen. |
> | Actie | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/back-ups/verwijderen | Hiermee verwijdert u een back-upbron. |
> | Actie | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/back-ups/lezen | Hiermee wordt een back-upbron gelezen. |
> | Actie | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/back-ups/schrijven | Schrijft een back-upbron. |
> | Actie | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/onderbreken/actie | Relaties met volume replicatie verbreekt |
> | Actie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Hiermee verwijdert u een volume resource. |
> | Actie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Hiermee wordt een bron van het doel van een koppeling gelezen. |
> | Actie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Hiermee wordt een volume bron gelezen. |
> | Actie | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/ReplicationStatus/actie | Hiermee wordt de status van de volume replicatie gelezen. |
> | Actie | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/Revert/actie | Volume naar specifieke moment opname herstellen |
> | Actie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Hiermee verwijdert u een momentopname resource. |
> | Actie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Hiermee wordt een momentopname resource gelezen. |
> | Actie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Schrijft een momentopname resource. |
> | Actie | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Schrijft een volume resource. |
> | Actie | Microsoft.NetApp/netAppAccounts/capacityPools/write | Schrijft een pool resource. |
> | Actie | Microsoft.NetApp/netAppAccounts/delete | Hiermee verwijdert u een account bron. |
> | Actie | Microsoft.NetApp/netAppAccounts/read | Hiermee wordt een account resource gelezen. |
> | Actie | Micro soft. NetApp/netAppAccounts/kluizen/lezen | Hiermee leest u een kluis bron. |
> | Actie | Microsoft.NetApp/netAppAccounts/write | Schrijft een account bron. |
> | Actie | Microsoft.NetApp/Operations/read | Hiermee worden een bewerkings resources gelezen. |
> | Actie | Micro soft. NetApp/REGI ster/actie | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. NetApp |
> | Actie | Micro soft. NetApp/registratie/actie | Registratie van het abonnement met de resource provider micro soft. NetApp is ongedaan gemaakt |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Application Gateway beschik bare aanvraag headers ophalen |
> | Actie | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Application Gateway beschik bare reactie header ophalen |
> | Actie | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Application Gateway beschik bare Server variabelen ophalen |
> | Actie | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway vooraf gedefinieerd SSL-beleid |
> | Actie | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Beschik bare SSL-opties Application Gateway |
> | Actie | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Hiermee worden Application Gateway beschik bare WAF-regel sets opgehaald |
> | Actie | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Voegt een back-end-adres groep van een toepassings gateway samen. Niet Alertable. |
> | Actie | Microsoft.Network/applicationGateways/backendhealth/action | Hiermee wordt een back-upstatus van de toepassings gateway opgehaald |
> | Actie | Microsoft.Network/applicationGateways/delete | Hiermee verwijdert u een toepassings gateway |
> | Actie | Micro soft. Network/applicationGateways/getBackendHealthOnDemand/Action | Hiermee wordt een back-end van de toepassings gateway opgehaald voor de opgegeven http-instelling en back-end-pool |
> | Actie | Microsoft.Network/applicationGateways/read | Hiermee wordt een toepassings gateway opgehaald |
> | Actie | Microsoft.Network/applicationGateways/start/action | Start een toepassings gateway |
> | Actie | Microsoft.Network/applicationGateways/stop/action | Stopt een toepassings gateway |
> | Actie | Microsoft.Network/applicationGateways/write | Hiermee wordt een toepassings gateway gemaakt of een toepassings gateway bijgewerkt |
> | Actie | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Hiermee wordt een Application Gateway WAF-beleid verwijderd |
> | Actie | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Hiermee wordt een Application Gateway WAF-beleid opgehaald |
> | Actie | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Hiermee maakt u een Application Gateway WAF-beleid of werkt u een Application Gateway WAF-beleid bij |
> | Actie | Microsoft.Network/applicationSecurityGroups/delete | Hiermee verwijdert u een toepassings beveiligings groep |
> | Actie | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Voegt een IP-configuratie toe aan toepassings beveiligings groepen. Niet alertable. |
> | Actie | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Er wordt een beveiligings regel toegevoegd aan toepassings beveiligings groepen. Niet alertable. |
> | Actie | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Een lijst met IP-configuraties in de ApplicationSecurityGroup |
> | Actie | Microsoft.Network/applicationSecurityGroups/read | Hiermee wordt een toepassings beveiligings groep-ID opgehaald. |
> | Actie | Microsoft.Network/applicationSecurityGroups/write | Hiermee wordt een toepassings beveiligings groep gemaakt of een bestaande toepassings beveiligings groep bijgewerkt. |
> | Actie | Microsoft.Network/azureFirewallFqdnTags/read | Hiermee worden Azure Firewall FQDN-Tags opgehaald |
> | Actie | Microsoft.Network/azurefirewalls/delete | Azure Firewall verwijderen |
> | Actie | Microsoft.Network/azurefirewalls/read | Azure Firewall ophalen |
> | Actie | Microsoft.Network/azurefirewalls/write | Hiermee wordt een Azure Firewall gemaakt of bijgewerkt |
> | Actie | Micro soft. Network/bastionHosts/createbsl/Action | Maakt deel bare url's voor de Vm's onder een Bastion en retourneert de url's |
> | Actie | Microsoft.Network/bastionHosts/delete | Hiermee wordt een bastion-host verwijderd |
> | Actie | Micro soft. Network/bastionHosts/deletebsl/Action | Hiermee worden deel bare url's voor de beschik bare virtuele machines in een bastion verwijderd |
> | Actie | Micro soft. Network/bastionHosts/disconnectactivesessions/Action | Verbinding met verleende actieve sessies op de bastion-host |
> | Actie | Micro soft. Network/bastionHosts/getactivesessions/Action | Actieve sessies op de bastion-host ophalen |
> | Actie | Micro soft. Network/bastionHosts/getbsl/Action | Retourneert de shares bare url's voor de opgegeven Vm's in een bastion-subnet waarin hun url's zijn gemaakt |
> | Actie | Microsoft.Network/bastionHosts/read | Hiermee wordt een bastion-host opgehaald |
> | Actie | Microsoft.Network/bastionHosts/write | Een bastion-host maken of bijwerken |
> | Actie | Microsoft.Network/bgpServiceCommunities/read | BGP-service Community's ophalen |
> | Actie | Microsoft.Network/checkFrontDoorNameAvailability/action | Hiermee wordt gecontroleerd of de naam van de voor deur beschikbaar is |
> | Actie | Microsoft.Network/checkTrafficManagerNameAvailability/action | Controleert de beschik baarheid van een Traffic Manager relatieve DNS-naam. |
> | Actie | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | Actie | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Actie | Microsoft.Network/connections/revoke/action | Hiermee wordt de verbindings status van een Express-route gemarkeerd als ingetrokken |
> | Actie | Microsoft.Network/connections/sharedkey/action | VirtualNetworkGatewayConnection SharedKey ophalen |
> | Actie | Microsoft.Network/connections/sharedKey/read | Hiermee wordt VirtualNetworkGatewayConnection SharedKey opgehaald |
> | Actie | Microsoft.Network/connections/sharedKey/write | Hiermee wordt een bestaande VirtualNetworkGatewayConnection-SharedKey gemaakt of bijgewerkt |
> | Actie | Micro soft. netwerk/verbindingen/startpacketcapture/actie | Start een Virtual Network gateway-verbindings pakket vastleggen. |
> | Actie | Micro soft. netwerk/verbindingen/stoppacketcapture/actie | Hiermee stopt u het vastleggen van een Virtual Network gateway-verbindings pakket. |
> | Actie | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Hiermee wordt de configuratie van het VPN-apparaat van VirtualNetworkGatewayConnection |
> | Actie | Microsoft.Network/connections/write | Hiermee wordt een bestaande VirtualNetworkGatewayConnection gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/ddosCustomPolicies/delete | Hiermee wordt een aangepast DDoS-beleid verwijderd |
> | Actie | Microsoft.Network/ddosCustomPolicies/read | Hiermee wordt een DDoS-aangepaste definitie van de beleids definitie opgehaald |
> | Actie | Microsoft.Network/ddosCustomPolicies/write | Hiermee maakt u een aangepast DDoS-beleid of werkt u een bestaand aangepast DDoS-beleid bij |
> | Actie | Microsoft.Network/ddosProtectionPlans/delete | Hiermee verwijdert u een DDoS Protection Plan |
> | Actie | Microsoft.Network/ddosProtectionPlans/join/action | Voegt een DDoS Protection Plan samen. Niet alertable. |
> | Actie | Microsoft.Network/ddosProtectionPlans/read | Hiermee wordt een DDoS Protection Plan opgehaald |
> | Actie | Microsoft.Network/ddosProtectionPlans/write | Hiermee maakt u een DDoS Protection Plan of werkt u een DDoS Protection-abonnement bij  |
> | Actie | Microsoft.Network/dnsoperationresults/read | Hiermee worden de resultaten van een DNS-bewerking opgehaald |
> | Actie | Microsoft.Network/dnsoperationstatuses/read | Hiermee wordt de status van een DNS-bewerking opgehaald  |
> | Actie | Microsoft.Network/dnszones/A/delete | Verwijder de recordset van een bepaalde naam en typ ' A ' van een DNS-zone. |
> | Actie | Microsoft.Network/dnszones/A/read | De recordset van het type ' A ' ophalen in de JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Actie | Microsoft.Network/dnszones/A/write | Een recordset van het type ' A ' maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/dnszones/AAAA/delete | Verwijder de recordset van een bepaalde naam en typ ' AAAA ' uit een DNS-zone. |
> | Actie | Microsoft.Network/dnszones/AAAA/read | Haal de recordset van het type ' AAAA ' op in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Actie | Microsoft.Network/dnszones/AAAA/write | Een recordset van het type ' AAAA ' maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/dnszones/all/read | Hiermee worden DNS-record sets in verschillende typen opgehaald |
> | Actie | Microsoft.Network/dnszones/CAA/delete | Verwijder de recordset van een bepaalde naam en typ ' CAA ' uit een DNS-zone. |
> | Actie | Microsoft.Network/dnszones/CAA/read | De recordset ophalen van het type ' CAA ', in JSON-indeling. De recordset bevat de TTL, tags en ETag. |
> | Actie | Microsoft.Network/dnszones/CAA/write | Een recordset van het type ' CAA ' maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/dnszones/CNAME/delete | Verwijder de recordset van een bepaalde naam en typ ' CNAME ' uit een DNS-zone. |
> | Actie | Microsoft.Network/dnszones/CNAME/read | De recordset van het type CNAME ophalen in de JSON-indeling. De recordset bevat de TTL, tags en ETag. |
> | Actie | Microsoft.Network/dnszones/CNAME/write | Een recordset van het type CNAME maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/dnszones/delete | Verwijder de DNS-zone in JSON-indeling. De zone-eigenschappen zijn onder andere tags, ETAG, numberOfRecordSets en maxNumberOfRecordSets. |
> | Actie | Microsoft.Network/dnszones/MX/delete | Verwijder de recordset van een bepaalde naam en typ ' MX ' uit een DNS-zone. |
> | Actie | Microsoft.Network/dnszones/MX/read | Haal de recordset van het type ' MX ' op in de JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Actie | Microsoft.Network/dnszones/MX/write | Een recordset van het type ' MX ' maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/dnszones/NS/delete | Hiermee wordt de DNS-recordset van het type NS verwijderd |
> | Actie | Microsoft.Network/dnszones/NS/read | Hiermee wordt een DNS-recordset van het type NS opgehaald |
> | Actie | Microsoft.Network/dnszones/NS/write | Hiermee wordt een DNS-recordset van het type NS gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/dnszones/PTR/delete | Verwijder de recordset van een bepaalde naam en typ ' PTR ' van een DNS-zone. |
> | Actie | Microsoft.Network/dnszones/PTR/read | Haal de recordset van het type ' PTR ' op in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Actie | Microsoft.Network/dnszones/PTR/write | Een recordset van het type ' PTR ' maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/dnszones/read | Haal de DNS-zone op in JSON-indeling. De zone-eigenschappen zijn onder andere tags, ETAG, numberOfRecordSets en maxNumberOfRecordSets. Houd er rekening mee dat met deze opdracht de record sets in de zone niet worden opgehaald. |
> | Actie | Microsoft.Network/dnszones/recordsets/read | Hiermee worden DNS-record sets in verschillende typen opgehaald |
> | Actie | Microsoft.Network/dnszones/SOA/read | Hiermee wordt een DNS-recordset van het type SOA opgehaald |
> | Actie | Microsoft.Network/dnszones/SOA/write | Hiermee wordt een DNS-recordset van het type SOA gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/dnszones/SRV/delete | Verwijder de recordset van een bepaalde naam en typ ' SRV ' uit een DNS-zone. |
> | Actie | Microsoft.Network/dnszones/SRV/read | De recordset van het type SRV ophalen in de JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Actie | Microsoft.Network/dnszones/SRV/write | Een recordset van het type SRV maken of bijwerken |
> | Actie | Microsoft.Network/dnszones/TXT/delete | Verwijder de recordset van een bepaalde naam en typ ' TXT ' uit een DNS-zone. |
> | Actie | Microsoft.Network/dnszones/TXT/read | Haal de recordset van het type ' TXT ' op in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Actie | Microsoft.Network/dnszones/TXT/write | Een recordset van het type ' TXT ' maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/dnszones/write | Een DNS-zone binnen een resource groep maken of bijwerken.  Wordt gebruikt om de tags op een DNS-zone resource bij te werken. Houd er rekening mee dat deze opdracht niet kan worden gebruikt om record sets in de zone te maken of bij te werken. |
> | Actie | Microsoft.Network/expressRouteCircuits/authorizations/delete | Hiermee verwijdert u een Expressroute-autorisatie |
> | Actie | Microsoft.Network/expressRouteCircuits/authorizations/read | Hiermee wordt een Expressroute-autorisatie opgehaald |
> | Actie | Microsoft.Network/expressRouteCircuits/authorizations/write | Hiermee wordt een bestaande Expressroute-autorisatie gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/expressRouteCircuits/delete | Hiermee wordt een Expressroute verwijderd |
> | Actie | Microsoft.Network/expressRouteCircuits/join/action | Voegt een Express route-Circuit samen. Niet alertable. |
> | Actie | Micro soft. Network/expressRouteCircuits/peering/arpTables/lezen | Hiermee wordt een Expressroute-peering-ArpTable opgehaald |
> | Actie | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Hiermee verwijdert u een Expressroute-verbinding |
> | Actie | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Hiermee wordt een Expressroute-verbinding opgehaald |
> | Actie | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Hiermee wordt een bestaande Expressroute-verbindings resource gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/expressRouteCircuits/peerings/delete | Hiermee wordt een Expressroute-peering verwijderd |
> | Actie | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Hiermee wordt een verbinding met een peer Express route-Circuit opgehaald |
> | Actie | Microsoft.Network/expressRouteCircuits/peerings/read | Hiermee wordt een Expressroute-peering opgehaald |
> | Actie | Micro soft. Network/expressRouteCircuits/peering/routeTables/lezen | Hiermee wordt een Expressroute-peering-RouteTable opgehaald |
> | Actie | Micro soft. Network/expressRouteCircuits/peering/routeTablesSummary/lezen | Hiermee wordt een Expressroute-peering RouteTable-samen vatting opgehaald |
> | Actie | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Hiermee wordt een Expressroute-peering-stat opgehaald |
> | Actie | Microsoft.Network/expressRouteCircuits/peerings/write | Hiermee wordt een bestaande Expressroute-peering gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/expressRouteCircuits/read | Een Expressroute ophalen |
> | Actie | Microsoft.Network/expressRouteCircuits/stats/read | Hiermee wordt een Expressroute-stat opgehaald |
> | Actie | Microsoft.Network/expressRouteCircuits/write | Hiermee wordt een bestaande Expressroute gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/expressRouteCrossConnections/join/action | Voegt een snelle route-cross-verbinding samen. Niet alertable. |
> | Actie | Micro soft. Network/expressRouteCrossConnections/peering/arpTables/lezen | Hiermee wordt een ARP-tabel van een snelle route Kruis verbinding gemaakt |
> | Actie | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Hiermee wordt een snelle route Kruis verbindings peering verwijderd |
> | Actie | Microsoft.Network/expressRouteCrossConnections/peerings/read | Hiermee wordt een snelle route Kruis verbindings peering opgehaald |
> | Actie | Micro soft. Network/expressRouteCrossConnections/peering/routeTables/lezen | Hiermee wordt een route tabel voor de peering van een snelle route Kruis verbinding opgehaald |
> | Actie | Micro soft. Network/expressRouteCrossConnections/peering/routeTableSummary/lezen | Hiermee wordt een samen vatting van een routerings tabel met snelle route Kruis verbinding opgehaald |
> | Actie | Microsoft.Network/expressRouteCrossConnections/peerings/write | Hiermee maakt u een snelle route Kruis verbinding-peering of werkt u een bestaande Express route Kruis verbindings peering bij |
> | Actie | Microsoft.Network/expressRouteCrossConnections/read | Snelle route-Kruis verbinding verkrijgen |
> | Actie | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Hiermee verwijdert u een snelle route verbinding |
> | Actie | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Hiermee wordt een snelle route verbinding opgehaald |
> | Actie | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Hiermee maakt u een snelle route verbinding of werkt u een bestaande Express route-verbinding bij |
> | Actie | Microsoft.Network/expressRouteGateways/join/action | Voegt een Express route-gateway samen. Niet alertable. |
> | Actie | Microsoft.Network/expressRouteGateways/read | Express route-gateway ophalen |
> | Actie | Microsoft.Network/expressRoutePorts/delete | Hiermee worden ExpressRoutePorts verwijderd |
> | Actie | Microsoft.Network/expressRoutePorts/join/action | Snelle route poorten worden toegevoegd. Niet alertable. |
> | Actie | Microsoft.Network/expressRoutePorts/links/read | Gets ExpressRouteLink |
> | Actie | Microsoft.Network/expressRoutePorts/read | Hiermee wordt ExpressRoutePorts opgehaald |
> | Actie | Microsoft.Network/expressRoutePorts/write | ExpressRoutePorts maken of bijwerken |
> | Actie | Microsoft.Network/expressRoutePortsLocations/read | Locaties voor Express route poorten ophalen |
> | Actie | Microsoft.Network/expressRouteServiceProviders/read | Hiermee worden snelle Route Service providers opgehaald |
> | Actie | Micro soft. Network/firewallPolicies/verwijderen | Hiermee wordt een firewall beleid verwijderd |
> | Actie | Micro soft. Network/firewallPolicies/samen voegen/actie | Er wordt lid van een firewall-beleid. Niet alertable. |
> | Actie | Micro soft. Network/firewallPolicies/lezen | Hiermee wordt een firewall beleid opgehaald |
> | Actie | Micro soft. Network/firewallPolicies/ruleGroups/verwijderen | Hiermee wordt een firewall beleidsregel groep verwijderd |
> | Actie | Micro soft. Network/firewallPolicies/ruleGroups/lezen | Hiermee wordt een firewall beleidsregel groep opgehaald |
> | Actie | Micro soft. Network/firewallPolicies/ruleGroups/schrijven | Hiermee wordt een firewall beleidsregel groep gemaakt of een bestaande firewall beleids regel groep bijgewerkt |
> | Actie | Micro soft. Network/firewallPolicies/schrijven | Hiermee wordt een firewall beleid gemaakt of een bestaand firewall beleid bijgewerkt |
> | Actie | Microsoft.Network/frontDoors/backendPools/delete | Hiermee verwijdert u een back-end-groep |
> | Actie | Microsoft.Network/frontDoors/backendPools/read | Hiermee wordt een back-end-pool opgehaald |
> | Actie | Microsoft.Network/frontDoors/backendPools/write | Hiermee wordt een back-end-groep gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/frontDoors/delete | Hiermee verwijdert u een voor deur |
> | Actie | Microsoft.Network/frontDoors/frontendEndpoints/delete | Hiermee wordt een frontend-eind punt verwijderd |
> | Actie | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Hiermee wordt HTTPS uitgeschakeld op een frontend-eind punt |
> | Actie | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | HTTPS inschakelen op een frontend-eind punt |
> | Actie | Microsoft.Network/frontDoors/frontendEndpoints/read | Hiermee wordt een frontend-eind punt opgehaald |
> | Actie | Microsoft.Network/frontDoors/frontendEndpoints/write | Hiermee wordt een frontend-eind punt gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/frontDoors/healthProbeSettings/delete | Hiermee worden de instellingen voor de status test verwijderd |
> | Actie | Microsoft.Network/frontDoors/healthProbeSettings/read | Hiermee worden de status test instellingen opgehaald |
> | Actie | Microsoft.Network/frontDoors/healthProbeSettings/write | Hiermee worden instellingen voor de status test gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Hiermee worden instellingen voor taak verdeling gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/frontDoors/loadBalancingSettings/read | Hiermee worden de instellingen voor taak verdeling opgehaald |
> | Actie | Microsoft.Network/frontDoors/loadBalancingSettings/write | Hiermee worden instellingen voor taak verdeling gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/frontDoors/purge/action | Inhoud in cache verwijderen van een front deur |
> | Actie | Microsoft.Network/frontDoors/read | Hiermee wordt een voor deur opgehaald |
> | Actie | Microsoft.Network/frontDoors/routingRules/delete | Hiermee verwijdert u een routerings regel |
> | Actie | Microsoft.Network/frontDoors/routingRules/read | Hiermee wordt een routerings regel opgehaald |
> | Actie | Microsoft.Network/frontDoors/routingRules/write | Hiermee wordt een routerings regel gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/frontDoors/validateCustomDomain/action | Hiermee wordt een frontend-eind punt gevalideerd voor een front-deur |
> | Actie | Microsoft.Network/frontDoors/write | Hiermee wordt een voor deur gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Hiermee worden beheerde regel sets voor Web Application firewall opgehaald |
> | Actie | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Hiermee wordt een firewall beleid voor webtoepassingen verwijderd |
> | Actie | Micro soft. Network/frontDoorWebApplicationFirewallPolicies/samen voegen/actie | Voegt een firewall beleid voor webtoepassingen toe. Niet Alertable. |
> | Actie | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Hiermee wordt een Web Application firewall-beleid opgehaald |
> | Actie | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Hiermee wordt een firewall beleid voor webtoepassingen gemaakt of bijgewerkt |
> | Actie | Micro soft. Network/ipGroups/verwijderen | Hiermee wordt een IpGroup verwijderd |
> | Actie | Micro soft. Network/ipGroups/samen voegen/actie | Er wordt een IpGroup toegevoegd. Niet alertable. |
> | Actie | Micro soft. Network/ipGroups/lezen | Hiermee wordt een IpGroup opgehaald |
> | Actie | Micro soft. Network/ipGroups/schrijven | Hiermee wordt een IpGroup gemaakt of een bestaande IpGroups bijgewerkt |
> | Actie | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Voegt een load balancer back-end-adres groep samen. Niet Alertable. |
> | Actie | Microsoft.Network/loadBalancers/backendAddressPools/read | Hiermee wordt een load balancer back-end-adres groep definitie opgehaald |
> | Actie | Microsoft.Network/loadBalancers/delete | Hiermee verwijdert u een load balancer |
> | Actie | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Voegt een Load Balancer frontend-IP-configuratie. Niet alertable. |
> | Actie | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Hiermee wordt een load balancer frontend-IP-configuratie definitie opgehaald |
> | Actie | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Voegt een load balancer binnenkomende NAT-pool samen. Niet alertable. |
> | Actie | Microsoft.Network/loadBalancers/inboundNatPools/read | Hiermee wordt een load balancer binnenkomende NAT-groeps definitie opgehaald |
> | Actie | Microsoft.Network/loadBalancers/inboundNatRules/delete | Hiermee wordt een load balancer binnenkomende NAT-regel verwijderd |
> | Actie | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Voegt een load balancer binnenkomende NAT-regel. Niet Alertable. |
> | Actie | Microsoft.Network/loadBalancers/inboundNatRules/read | Hiermee wordt een load balancer binnenkomende NAT-regel definitie opgehaald |
> | Actie | Microsoft.Network/loadBalancers/inboundNatRules/write | Hiermee maakt u een load balancer binnenkomende NAT-regel of werkt u een bestaande load balancer binnenkomende NAT-regel bij |
> | Actie | Microsoft.Network/loadBalancers/loadBalancingRules/read | Hiermee wordt een load balancer de definitie van de taakverdelings regel opgehaald |
> | Actie | Microsoft.Network/loadBalancers/networkInterfaces/read | Hiermee wordt verwezen naar alle netwerk interfaces onder een load balancer |
> | Actie | Microsoft.Network/loadBalancers/outboundRules/read | Hiermee wordt een load balancer uitgaande regel definitie opgehaald |
> | Actie | Microsoft.Network/loadBalancers/probes/join/action | Staat het gebruik van tests van een load balancer toe. Met deze machtiging healthProbe eigenschap van de VM-schaalset kan bijvoorbeeld naar de test worden verwezen. Niet alertable. |
> | Actie | Microsoft.Network/loadBalancers/probes/read | Hiermee wordt een load balancer-test opgehaald |
> | Actie | Microsoft.Network/loadBalancers/read | Hiermee wordt een load balancer definitie opgehaald |
> | Actie | Microsoft.Network/loadBalancers/virtualMachines/read | Hiermee worden verwijzingen naar alle virtuele machines onder een load balancer opgehaald |
> | Actie | Microsoft.Network/loadBalancers/write | Hiermee maakt u een load balancer of werkt u een bestaande load balancer bij |
> | Actie | Microsoft.Network/localnetworkgateways/delete | Deletes LocalNetworkGateway |
> | Actie | Microsoft.Network/localnetworkgateways/read | Gets LocalNetworkGateway |
> | Actie | Microsoft.Network/localnetworkgateways/write | Hiermee wordt een bestaande LocalNetworkGateway gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Hiermee worden automatisch goedgekeurde persoonlijke koppelings services opgehaald |
> | Actie | Microsoft.Network/locations/availableDelegations/read | Beschik bare delegaties ophalen |
> | Actie | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Beschik bare persoonlijke eindpunt resources ophalen |
> | Actie | Micro soft. netwerk/locaties/availableServiceAliases/lezen | Beschik bare service aliassen ophalen |
> | Actie | Microsoft.Network/locations/bareMetalTenants/action | Hiermee wordt een bare metal-Tenant toegewezen of gevalideerd |
> | Actie | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Hiermee wordt de ondersteuning voor versnelde netwerken gecontroleerd |
> | Actie | Microsoft.Network/locations/checkDnsNameAvailability/read | Controleert of het DNS-label beschikbaar is op de opgegeven locatie |
> | Actie | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Controleert de zicht baarheid van de persoonlijke koppelings service |
> | Actie | Microsoft.Network/locations/operationResults/read | Hiermee wordt het bewerkings resultaat van een asynchrone bericht-of verwijder bewerking opgehaald |
> | Actie | Microsoft.Network/locations/operations/read | Hiermee wordt de bewerkings resource opgehaald die de status van een asynchrone bewerking vertegenwoordigt |
> | Actie | Microsoft.Network/locations/serviceTags/read | Service Tags ophalen |
> | Actie | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Hiermee worden de ondersteunde grootten van virtuele machines opgehaald |
> | Actie | Microsoft.Network/locations/usages/read | Hiermee worden de metrische gegevens over het bronnen gebruik opgehaald |
> | Actie | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Hiermee wordt een lijst met beschik bare Virtual Network endpoint services opgehaald |
> | Actie | Microsoft.Network/networkIntentPolicies/delete | Hiermee wordt een beleid voor netwerk intentie verwijderd |
> | Actie | Microsoft.Network/networkIntentPolicies/read | Hiermee wordt een beschrijving van het beleid voor netwerk intentie opgehaald |
> | Actie | Microsoft.Network/networkIntentPolicies/write | Hiermee wordt een beleid voor netwerk intentie gemaakt of een bestaand netwerk intentie beleid bijgewerkt |
> | Actie | Microsoft.Network/networkInterfaces/delete | Hiermee wordt een netwerk interface verwijderd |
> | Actie | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Netwerk beveiligings groepen die zijn geconfigureerd op de netwerk interface van de virtuele machine ophalen |
> | Actie | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Route tabel ophalen die is geconfigureerd op de netwerk interface van de virtuele machine |
> | Actie | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Voegt een IP-configuratie van een netwerk interface toe. Niet alertable. |
> | Actie | Microsoft.Network/networkInterfaces/ipconfigurations/read | Hiermee wordt een IP-configuratie definitie van de netwerk interface opgehaald.  |
> | Actie | Microsoft.Network/networkInterfaces/join/action | Voegt een virtuele machine toe aan een netwerk interface. Niet Alertable. |
> | Actie | Microsoft.Network/networkInterfaces/loadBalancers/read | Hiermee worden alle load balancers opgehaald waarvan de netwerk interface deel uitmaakt |
> | Actie | Microsoft.Network/networkInterfaces/read | Hiermee haalt u een definitie van een netwerk interface.  |
> | Actie | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Hiermee verwijdert u een netwerk interface tikt u op configuratie. |
> | Actie | Microsoft.Network/networkInterfaces/tapConfigurations/read | Hiermee wordt een netwerk interface op configuratie opgehaald. |
> | Actie | Microsoft.Network/networkInterfaces/tapConfigurations/write | Hiermee maakt u een netwerk interface tikt u op configuratie of werkt u een bestaande netwerk interface op configuratie tikken. |
> | Actie | Microsoft.Network/networkInterfaces/write | Hiermee maakt u een netwerk interface of werkt u een bestaande netwerk interface bij.  |
> | Actie | Microsoft.Network/networkProfiles/delete | Hiermee wordt een netwerk profiel verwijderd |
> | Actie | Microsoft.Network/networkProfiles/read | Hiermee wordt een netwerk profiel opgehaald |
> | Actie | Microsoft.Network/networkProfiles/removeContainers/action | Containers verwijderen |
> | Actie | Microsoft.Network/networkProfiles/setContainers/action | Containers instellen |
> | Actie | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Netwerk interfaces voor containers instellen |
> | Actie | Microsoft.Network/networkProfiles/write | Hiermee wordt een netwerk profiel gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Hiermee wordt een standaard beveiligings regel definitie opgehaald |
> | Actie | Microsoft.Network/networkSecurityGroups/delete | Hiermee wordt een netwerk beveiligings groep verwijderd |
> | Actie | Microsoft.Network/networkSecurityGroups/join/action | Er wordt lid van een netwerk beveiligings groep. Niet Alertable. |
> | Actie | Microsoft.Network/networkSecurityGroups/read | Hiermee wordt een definitie van een netwerk beveiligings groep opgehaald |
> | Actie | Microsoft.Network/networkSecurityGroups/securityRules/delete | Hiermee verwijdert u een beveiligings regel |
> | Actie | Microsoft.Network/networkSecurityGroups/securityRules/read | Hiermee wordt een definitie van een beveiligings regel opgehaald |
> | Actie | Microsoft.Network/networkSecurityGroups/securityRules/write | Hiermee maakt u een beveiligings regel of werkt u een bestaande beveiligings regel bij |
> | Actie | Microsoft.Network/networkSecurityGroups/write | Hiermee wordt een netwerk beveiligings groep gemaakt of een bestaande netwerk beveiligings groep bijgewerkt |
> | Actie | Microsoft.Network/networkWatchers/availableProvidersList/action | Retourneert alle beschik bare Internet serviceproviders voor een opgegeven Azure-regio. |
> | Actie | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Retourneert de relatieve latentie score voor Internet serviceproviders van een opgegeven locatie naar Azure-regio's. |
> | Actie | Microsoft.Network/networkWatchers/configureFlowLog/action | Hiermee configureert u de logboek registratie van de stroom voor een doel bron. |
> | Actie | Microsoft.Network/networkWatchers/connectionMonitors/delete | Hiermee wordt een verbindings monitor verwijderd |
> | Actie | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Query bewakings connectiviteit tussen opgegeven eind punten |
> | Actie | Microsoft.Network/networkWatchers/connectionMonitors/read | Details van de verbindings monitor ophalen |
> | Actie | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Bewakings verbinding tussen opgegeven eind punten starten |
> | Actie | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Controle connectiviteit tussen opgegeven eind punten stoppen/onderbreken |
> | Actie | Microsoft.Network/networkWatchers/connectionMonitors/write | Hiermee maakt u een verbindings monitor |
> | Actie | Microsoft.Network/networkWatchers/connectivityCheck/action | Hiermee wordt de mogelijkheid voor het tot stand brengen van een directe TCP-verbinding vanaf een virtuele machine naar een bepaald eind punt, inclusief een andere VM of een wille keurige externe server, gecontroleerd. |
> | Actie | Microsoft.Network/networkWatchers/delete | Hiermee wordt een netwerk-Watcher verwijderd |
> | Actie | Micro soft. Network/networkWatchers/flowLogs/verwijderen | Hiermee verwijdert u een stroom logboek |
> | Actie | Micro soft. Network/networkWatchers/flowLogs/lezen | Details van stroom logboek ophalen |
> | Actie | Micro soft. Network/networkWatchers/flowLogs/schrijven | Hiermee maakt u een stroom logboek |
> | Actie | Microsoft.Network/networkWatchers/ipFlowVerify/action | Retourneert of het pakket wordt toegestaan of geweigerd van een bepaalde bestemming. |
> | Actie | Microsoft.Network/networkWatchers/lenses/delete | Hiermee wordt een lens verwijderd |
> | Actie | Microsoft.Network/networkWatchers/lenses/query/action | Query's bewaken van netwerk verkeer op een opgegeven eind punt |
> | Actie | Microsoft.Network/networkWatchers/lenses/read | Lens Details ophalen |
> | Actie | Microsoft.Network/networkWatchers/lenses/start/action | Bewaken van netwerk verkeer op een opgegeven eind punt starten |
> | Actie | Microsoft.Network/networkWatchers/lenses/stop/action | Bewaken van netwerk verkeer op een opgegeven eind punt stoppen/onderbreken |
> | Actie | Microsoft.Network/networkWatchers/lenses/write | Hiermee maakt u een lens |
> | Actie | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnose van de netwerk configuratie. |
> | Actie | Microsoft.Network/networkWatchers/nextHop/action | Voor een opgegeven doel-en doel-IP-adres retourneert het type van de volgende hop en de volgende hoop IP-adres. |
> | Actie | Microsoft.Network/networkWatchers/packetCaptures/delete | Hiermee wordt een pakket opname verwijderd |
> | Actie | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Haalt informatie op over de eigenschappen en status van een pakket opname bron. |
> | Actie | Microsoft.Network/networkWatchers/packetCaptures/read | De definitie voor pakket opname ophalen |
> | Actie | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Stop de actieve sessie voor het vastleggen van pakketten. |
> | Actie | Microsoft.Network/networkWatchers/packetCaptures/write | Hiermee maakt u een pakket opname |
> | Actie | Microsoft.Network/networkWatchers/pingMeshes/delete | Hiermee wordt een PingMesh verwijderd |
> | Actie | Microsoft.Network/networkWatchers/pingMeshes/read | Details van PingMesh ophalen |
> | Actie | Microsoft.Network/networkWatchers/pingMeshes/start/action | PingMesh tussen opgegeven Vm's starten |
> | Actie | Microsoft.Network/networkWatchers/pingMeshes/stop/action | PingMesh tussen opgegeven Vm's stoppen |
> | Actie | Microsoft.Network/networkWatchers/pingMeshes/write | Hiermee maakt u een PingMesh |
> | Actie | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Controle van de connectiviteit voor batch query's tussen opgegeven eind punten |
> | Actie | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Hiermee wordt de status van de logboek registratie van de stroom op een resource opgehaald. |
> | Actie | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Hiermee wordt het probleem voor het oplossen van problemen met de eerder uitgevoerde of actieve probleemoplossings bewerking opgehaald. |
> | Actie | Microsoft.Network/networkWatchers/read | De Network Watcher-definitie ophalen |
> | Actie | Microsoft.Network/networkWatchers/securityGroupView/action | De geconfigureerde en efficiënte regels voor netwerk beveiligings groepen weer geven die zijn toegepast op een virtuele machine. |
> | Actie | Microsoft.Network/networkWatchers/topology/action | Hiermee wordt een weer gave op netwerk niveau van resources en de bijbehorende relaties in een resource groep opgehaald. |
> | Actie | Microsoft.Network/networkWatchers/troubleshoot/action | Hiermee wordt het oplossen van problemen met een netwerk bron in azure gestart. |
> | Actie | Microsoft.Network/networkWatchers/write | Hiermee wordt een netwerk-Watcher gemaakt of een bestaande netwerk-Watcher bijgewerkt |
> | Actie | Microsoft.Network/operations/read | Beschik bare bewerkingen ophalen |
> | Actie | Microsoft.Network/p2sVpnGateways/delete | Hiermee verwijdert u een P2SVpnGateway. |
> | Actie | Micro soft. Network/p2sVpnGateways/disconnectp2svpnconnections/Action | P2s VPN-verbindingen verbreken |
> | Actie | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | VPN-profiel voor P2SVpnGateway genereren |
> | Actie | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Hiermee wordt een P2S VPN-verbindings status opgehaald voor P2SVpnGateway |
> | Actie | Micro soft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/Action | Hiermee wordt een P2S VPN-verbindings status voor P2SVpnGateway opgehaald |
> | Actie | Microsoft.Network/p2sVpnGateways/read | Hiermee wordt een P2SVpnGateway opgehaald. |
> | Actie | Microsoft.Network/p2sVpnGateways/write | Hiermee wordt een P2SVpnGateway geplaatst. |
> | Actie | Microsoft.Network/privateDnsOperationResults/read | Hiermee worden de resultaten van een Privé-DNS bewerking opgehaald |
> | Actie | Microsoft.Network/privateDnsOperationStatuses/read | Hiermee wordt de status van een Privé-DNS bewerking opgehaald |
> | Actie | Microsoft.Network/privateDnsZones/A/delete | Verwijder de recordset van een bepaalde naam en typ ' A ' in een Privé-DNS zone. |
> | Actie | Microsoft.Network/privateDnsZones/A/read | De recordset van het type ' A ' ophalen binnen een Privé-DNS zone, in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Actie | Microsoft.Network/privateDnsZones/A/write | Een recordset van het type ' A ' in een Privé-DNS zone maken of bijwerken. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/privateDnsZones/AAAA/delete | Verwijder de recordset van een bepaalde naam en typ ' AAAA ' uit een Privé-DNS zone. |
> | Actie | Microsoft.Network/privateDnsZones/AAAA/read | Haal de recordset van het type ' AAAA ' op in een Privé-DNS zone, in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Actie | Microsoft.Network/privateDnsZones/AAAA/write | Een recordset van het type ' AAAA ' maken of bijwerken binnen een Privé-DNS zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/privateDnsZones/ALL/read | Hiermee worden Privé-DNS record sets in verschillende typen opgehaald |
> | Actie | Microsoft.Network/privateDnsZones/CNAME/delete | Verwijder de recordset van een bepaalde naam en typ ' CNAME ' uit een Privé-DNS zone. |
> | Actie | Microsoft.Network/privateDnsZones/CNAME/read | De recordset van het type CNAME ophalen binnen een Privé-DNS zone, in JSON-indeling. |
> | Actie | Microsoft.Network/privateDnsZones/CNAME/write | Een recordset van het type CNAME maken of bijwerken binnen een Privé-DNS zone. |
> | Actie | Microsoft.Network/privateDnsZones/delete | Een Privé-DNS zone verwijderen. |
> | Actie | Microsoft.Network/privateDnsZones/MX/delete | Verwijder de recordset van een bepaalde naam en typ ' MX ' uit een Privé-DNS zone. |
> | Actie | Microsoft.Network/privateDnsZones/MX/read | Haal de recordset van het type ' MX ' op in een Privé-DNS zone, in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Actie | Microsoft.Network/privateDnsZones/MX/write | Een recordset van het type ' MX ' maken of bijwerken binnen een Privé-DNS zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/privateDnsZones/PTR/delete | Verwijder de recordset van een bepaalde naam en typ ' PTR ' uit een Privé-DNS zone. |
> | Actie | Microsoft.Network/privateDnsZones/PTR/read | Haal de recordset van het type ' PTR ' op in een Privé-DNS zone, in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Actie | Microsoft.Network/privateDnsZones/PTR/write | Een recordset van het type ' PTR ' maken of bijwerken binnen een Privé-DNS zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/privateDnsZones/read | Haal de eigenschappen van de Privé-DNS-zone op in JSON-indeling. Houd er rekening mee dat met deze opdracht de virtuele netwerken waaraan de Privé-DNS zone is gekoppeld of de record sets in de zone, niet worden opgehaald. |
> | Actie | Microsoft.Network/privateDnsZones/recordsets/read | Hiermee worden Privé-DNS record sets in verschillende typen opgehaald |
> | Actie | Microsoft.Network/privateDnsZones/SOA/read | Haal de recordset van het type ' SOA ' op in een Privé-DNS zone, in JSON-indeling. |
> | Actie | Microsoft.Network/privateDnsZones/SOA/write | Een recordset van het type ' SOA ' in een Privé-DNS zone bijwerken. |
> | Actie | Microsoft.Network/privateDnsZones/SRV/delete | De recordset van een bepaalde naam en type SRV verwijderen uit een Privé-DNS zone. |
> | Actie | Microsoft.Network/privateDnsZones/SRV/read | De recordset van het type SRV ophalen binnen een Privé-DNS zone, in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Actie | Microsoft.Network/privateDnsZones/SRV/write | Een recordset van het type SRV maken of bijwerken binnen een Privé-DNS zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/privateDnsZones/TXT/delete | Verwijder de recordset van een bepaalde naam en typ ' TXT ' uit een Privé-DNS zone. |
> | Actie | Microsoft.Network/privateDnsZones/TXT/read | Haal de recordset van het type ' TXT ' op in een Privé-DNS zone, in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Actie | Microsoft.Network/privateDnsZones/TXT/write | Een recordset van het type ' TXT ' in een Privé-DNS zone maken of bijwerken. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Actie | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Verwijder een Privé-DNS zone koppeling naar het virtuele netwerk. |
> | Actie | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Haal de koppeling naar de Privé-DNS zone op in JSON-indeling naar eigenschappen van virtueel netwerk. |
> | Actie | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Een Privé-DNS zone koppeling naar een virtueel netwerk maken of bijwerken. |
> | Actie | Microsoft.Network/privateDnsZones/write | Een Privé-DNS zone in een resource groep maken of bijwerken. Houd er rekening mee dat deze opdracht niet kan worden gebruikt voor het maken of bijwerken van koppelingen met virtuele netwerken of record sets in de zone. |
> | Actie | Micro soft. Network/privateEndpointRedirectMaps/lezen | Hiermee wordt een persoonlijk eind punt RedirectMap |
> | Actie | Micro soft. Network/privateEndpointRedirectMaps/schrijven | Hiermee maakt u een persoonlijk eind punt RedirectMap of werkt u een bestaand persoonlijk eind punt RedirectMap |
> | Actie | Microsoft.Network/privateEndpoints/delete | Hiermee verwijdert u een persoonlijke eindpunt resource. |
> | Actie | Microsoft.Network/privateEndpoints/read | Hiermee wordt een persoonlijke eindpunt resource opgehaald. |
> | Actie | Microsoft.Network/privateEndpoints/write | Hiermee maakt u een nieuw persoonlijk eind punt of werkt u een bestaand persoonlijk eind punt bij. |
> | Actie | Microsoft.Network/privateLinkServices/delete | Hiermee wordt een resource van een persoonlijke koppelings service verwijderd. |
> | Actie | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Hiermee verwijdert u een verbinding met een privé-eind punt. |
> | Actie | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Hiermee wordt een definitie van een particuliere endpoint-verbinding opgehaald. |
> | Actie | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Hiermee maakt u een nieuwe verbinding voor een persoonlijk eind punt of werkt u een bestaande verbinding met een privé-eind punt bij. |
> | Actie | Microsoft.Network/privateLinkServices/read | Hiermee wordt een bron van een persoonlijke koppelings service opgehaald. |
> | Actie | Microsoft.Network/privateLinkServices/write | Hiermee maakt u een nieuwe privé koppelings service of werkt u een bestaande persoonlijke koppelings service bij. |
> | Actie | Microsoft.Network/publicIPAddresses/delete | Hiermee verwijdert u een openbaar IP-adres. |
> | Actie | Microsoft.Network/publicIPAddresses/join/action | Er wordt verbinding gemaakt met een openbaar IP-adres. Niet Alertable. |
> | Actie | Microsoft.Network/publicIPAddresses/read | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | Actie | Microsoft.Network/publicIPAddresses/write | Hiermee maakt u een openbaar IP-adres of werkt u een bestaand openbaar IP-adres bij.  |
> | Actie | Microsoft.Network/publicIPPrefixes/delete | Hiermee wordt een openbaar IP-voor voegsel verwijderd |
> | Actie | Microsoft.Network/publicIPPrefixes/join/action | Er wordt een PublicIPPrefix toegevoegd. Niet alertable. |
> | Actie | Microsoft.Network/publicIPPrefixes/read | Hiermee wordt een definitie van een openbaar IP-voor voegsel opgehaald |
> | Actie | Microsoft.Network/publicIPPrefixes/write | Hiermee wordt een openbaar IP-voor voegsel gemaakt of een bestaand openbaar IP-voor voegsel bijgewerkt |
> | Actie | Microsoft.Network/register/action | Hiermee wordt het abonnement geregistreerd |
> | Actie | Microsoft.Network/routeFilters/delete | Hiermee wordt een definitie van een route filter verwijderd |
> | Actie | Microsoft.Network/routeFilters/join/action | Er wordt verbinding gemaakt met een route filter. Niet Alertable. |
> | Actie | Microsoft.Network/routeFilters/read | Hiermee wordt een route filter definitie opgehaald |
> | Actie | Microsoft.Network/routeFilters/routeFilterRules/delete | Hiermee wordt een regel definitie voor een route filter verwijderd |
> | Actie | Microsoft.Network/routeFilters/routeFilterRules/read | Hiermee wordt een regel definitie voor een route filter opgehaald |
> | Actie | Microsoft.Network/routeFilters/routeFilterRules/write | Hiermee maakt u een regel voor route filtering of werkt u een bestaande route filter regel bij |
> | Actie | Microsoft.Network/routeFilters/write | Hiermee maakt u een route filter of werkt u een bestaand route filter bij |
> | Actie | Microsoft.Network/routeTables/delete | Hiermee wordt een definitie van een route tabel verwijderd |
> | Actie | Microsoft.Network/routeTables/join/action | Voegt een route tabel samen. Niet Alertable. |
> | Actie | Microsoft.Network/routeTables/read | Hiermee wordt een definitie van een route tabel opgehaald |
> | Actie | Microsoft.Network/routeTables/routes/delete | Hiermee verwijdert u een route definitie |
> | Actie | Microsoft.Network/routeTables/routes/read | Hiermee wordt een route definitie opgehaald |
> | Actie | Microsoft.Network/routeTables/routes/write | Hiermee maakt u een route of werkt u een bestaande route bij |
> | Actie | Microsoft.Network/routeTables/write | Hiermee maakt u een route tabel of werkt u een bestaande route tabel bij |
> | Actie | Microsoft.Network/serviceEndpointPolicies/delete | Hiermee wordt een service-eindpunt beleid verwijderd |
> | Actie | Microsoft.Network/serviceEndpointPolicies/join/action | Voegt een service-eindpunt beleid samen. Niet alertable. |
> | Actie | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Voegt een subnet toe aan service Endpoint-beleids regels. Niet alertable. |
> | Actie | Microsoft.Network/serviceEndpointPolicies/read | Hiermee wordt een beschrijving van het service-eindpunt beleid opgehaald |
> | Actie | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Hiermee wordt een beleids definitie voor service-eind punten verwijderd |
> | Actie | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Hiermee wordt een beschrijving van de definitie van het service-eindpunt beleid opgehaald |
> | Actie | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Hiermee wordt een beleids definitie voor service-eind punten gemaakt of een bestaande definitie van een service-eindpunt beleid bijgewerkt |
> | Actie | Microsoft.Network/serviceEndpointPolicies/write | Hiermee wordt een service-eindpunt beleid gemaakt of een bestaand service-eindpunt beleid bijgewerkt |
> | Actie | Microsoft.Network/trafficManagerGeographicHierarchies/read | Hiermee wordt de Traffic Manager geografische hiërarchie opgehaald die regio's bevat die kunnen worden gebruikt met de geografische routerings methode voor verkeer |
> | Actie | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Hiermee verwijdert u een Azure-eindpunt uit een bestaand Traffic Manager-profiel. Traffic Manager wordt het routeren van verkeer naar de verwijderde Azure-eindpunt gestopt. |
> | Actie | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Hiermee haalt u een Azure-eindpunt die tot een Traffic Manager profiel behoort, inclusief alle eigenschappen van die Azure-eindpunt. |
> | Actie | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Voeg een nieuwe Azure-eindpunt toe aan een bestaand Traffic Manager profiel of werk de eigenschappen van een bestaande Azure-eindpunt in dat Traffic Manager profiel bij. |
> | Actie | Microsoft.Network/trafficManagerProfiles/delete | Verwijder het Traffic Manager profiel. Alle instellingen die aan het Traffic Manager profiel zijn gekoppeld, gaan verloren en het profiel kan niet meer worden gebruikt voor het routeren van verkeer. |
> | Actie | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Hiermee verwijdert u een extern eind punt uit een bestaand Traffic Manager profiel. Traffic Manager wordt het routeren van verkeer naar het verwijderde externe eind punt gestopt. |
> | Actie | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Hiermee haalt u een extern eind punt dat hoort bij een Traffic Manager profiel, inclusief alle eigenschappen van het externe eind punt. |
> | Actie | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Voeg een nieuw extern eind punt toe aan een bestaand Traffic Manager profiel of werk de eigenschappen van een bestaand extern eind punt in dat Traffic Manager profiel bij. |
> | Actie | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Hiermee haalt u de Traffic Manager heatmap voor het gegeven Traffic Manager profiel dat query aantallen en latentie gegevens bevat op locatie en bron-IP. |
> | Actie | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Hiermee verwijdert u een genest eind punt uit een bestaand Traffic Manager profiel. Traffic Manager wordt het routerings verkeer naar het verwijderde geneste eind punt gestopt. |
> | Actie | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Hiermee wordt een genest eind punt opgehaald dat tot een Traffic Manager profiel behoort, inclusief alle eigenschappen van dat geneste eind punt. |
> | Actie | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Een nieuw genest eind punt toevoegen aan een bestaand Traffic Manager profiel of de eigenschappen van een bestaand genest eind punt in dat Traffic Manager profiel bijwerken. |
> | Actie | Microsoft.Network/trafficManagerProfiles/read | De configuratie van het Traffic Manager-profiel ophalen. Dit zijn onder andere DNS-instellingen, instellingen voor verkeers routering, instellingen voor eindpunt bewaking en de lijst met eind punten die door dit Traffic Manager profiel worden gerouteerd. |
> | Actie | Microsoft.Network/trafficManagerProfiles/write | Maak een Traffic Manager profiel of wijzig de configuratie van een bestaand Traffic Manager-profiel.<br>Dit omvat het in-of uitschakelen van een profiel en het wijzigen van DNS-instellingen, instellingen voor verkeers routering of instellingen voor het controleren van eind punten.<br>Eind punten die door het Traffic Manager profiel worden doorgestuurd, kunnen worden toegevoegd, verwijderd, ingeschakeld of uitgeschakeld. |
> | Actie | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Hiermee verwijdert u de sleutel op abonnements niveau die wordt gebruikt voor de verzameling van metrische gegevens voor de realtime gebruiker. |
> | Actie | Microsoft.Network/trafficManagerUserMetricsKeys/read | Hiermee wordt de sleutel op abonnements niveau opgehaald die wordt gebruikt voor de verzameling van metrische gegevens voor de realtime gebruiker. |
> | Actie | Microsoft.Network/trafficManagerUserMetricsKeys/write | Hiermee wordt een nieuwe sleutel op abonnements niveau gemaakt die moet worden gebruikt voor de verzameling van metrische gegevens voor de realtime gebruiker. |
> | Actie | Microsoft.Network/unregister/action | Hiermee wordt de registratie van het abonnement ongedaan gemaakt |
> | Actie | Microsoft.Network/virtualHubs/delete | Hiermee wordt een virtuele hub verwijderd |
> | Actie | Micro soft. Network/virtualHubs/effectiveRoutes/Action | Hiermee wordt een efficiënte route opgehaald die is geconfigureerd op de virtuele hub |
> | Actie | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Hiermee wordt een HubVirtualNetworkConnection verwijderd |
> | Actie | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Een HubVirtualNetworkConnection ophalen |
> | Actie | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Een HubVirtualNetworkConnection maken of bijwerken |
> | Actie | Microsoft.Network/virtualHubs/read | Een virtuele hub ophalen |
> | Actie | Micro soft. Network/virtualHubs/routeTables/verwijderen | Een VirtualHubRouteTableV2 verwijderen |
> | Actie | Micro soft. Network/virtualHubs/routeTables/lezen | Een VirtualHubRouteTableV2 ophalen |
> | Actie | Micro soft. Network/virtualHubs/routeTables/schrijven | Een VirtualHubRouteTableV2 maken of bijwerken |
> | Actie | Microsoft.Network/virtualHubs/write | Een virtuele hub maken of bijwerken |
> | Actie | microsoft.network/virtualnetworkgateways/connections/read | VirtualNetworkGatewayConnection ophalen |
> | Actie | Microsoft.Network/virtualNetworkGateways/delete | Hiermee wordt een virtualNetworkGateway verwijderd |
> | Actie | micro soft. Network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/Action | VPN-verbindingen met virtuele netwerk gateway verbreken |
> | Actie | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | VpnClient-pakket genereren voor virtualNetworkGateway |
> | Actie | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | VpnProfile-pakket genereren voor VirtualNetworkGateway |
> | Actie | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Hiermee worden virtualNetworkGateway geadverteerde routes opgehaald |
> | Actie | micro soft. Network/virtualnetworkgateways/getbgppeerstatus/Action | Hiermee wordt de BGP-peer status virtualNetworkGateway opgehaald |
> | Actie | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Hiermee worden virtualnetworkgateway geleerde routes opgehaald |
> | Actie | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Per VPN-client verbindings status voor VirtualNetworkGateway ophalen |
> | Actie | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Vpnclient IPSec-para meters voor VirtualNetworkGateway P2S-client ophalen. |
> | Actie | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Hiermee wordt de URL van een vooraf gegenereerd VPN-client profiel pakket opgehaald |
> | Actie | Microsoft.Network/virtualNetworkGateways/read | Hiermee wordt een VirtualNetworkGateway opgehaald |
> | Actie | microsoft.network/virtualnetworkgateways/reset/action | Hiermee wordt een virtualNetworkGateway opnieuw ingesteld |
> | Actie | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Stel de gedeelde sleutel vpnclient voor de VirtualNetworkGateway P2S-client opnieuw in. |
> | Actie | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Stel vpnclient IPSec-para meters in voor VirtualNetworkGateway P2S-client. |
> | Actie | micro soft. Network/virtualnetworkgateways/startpacketcapture/Action | Start een Virtual Network gateway-pakket vastleggen. |
> | Actie | micro soft. Network/virtualnetworkgateways/stoppacketcapture/Action | Hiermee stopt u het vastleggen van een Virtual Network gateway pakket. |
> | Actie | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Een lijst met ondersteunde VPN-apparaten |
> | Actie | Microsoft.Network/virtualNetworkGateways/write | Hiermee wordt een VirtualNetworkGateway gemaakt of bijgewerkt |
> | Actie | Microsoft.Network/virtualNetworks/BastionHosts/action | Hiermee worden Bastion opgehaald in een Virtual Network. |
> | Actie | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Hiermee worden Bastion opgehaald in een Virtual Network. |
> | Actie | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Controleer of het IP-adres beschikbaar is op het opgegeven virtuele netwerk |
> | Actie | Microsoft.Network/virtualNetworks/delete | Hiermee verwijdert u een virtueel netwerk |
> | Actie | Micro soft. Network/virtualNetworks/samen voegen/actie | Voegt een virtueel netwerk samen. Niet Alertable. |
> | Actie | Microsoft.Network/virtualNetworks/peer/action | Peering van een virtueel netwerk met een ander virtueel netwerk |
> | Actie | Microsoft.Network/virtualNetworks/read | De virtuele-netwerk definitie ophalen |
> | Actie | Micro soft. Network/virtualNetworks/subnetten/contextualServiceEndpointPolicies/verwijderen | Hiermee wordt een beleid voor contextuele service-eind punten verwijderd |
> | Actie | Micro soft. Network/virtualNetworks/subnetten/contextualServiceEndpointPolicies/lezen | Hiermee wordt beleid voor contextuele service-eind punten opgehaald |
> | Actie | Micro soft. Network/virtualNetworks/subnetten/contextualServiceEndpointPolicies/schrijven | Hiermee wordt een beleid voor contextuele service-eind punten gemaakt of een bestaand beleid voor contextuele service-eind punten bijgewerkt |
> | Actie | Microsoft.Network/virtualNetworks/subnets/delete | Hiermee verwijdert u een subnet van een virtueel netwerk |
> | Actie | Microsoft.Network/virtualNetworks/subnets/join/action | Voegt een virtueel netwerk samen. Niet Alertable. |
> | Actie | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | Actie | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Bereidt een subnet voor door het benodigde netwerk beleid toe te passen |
> | Actie | Microsoft.Network/virtualNetworks/subnets/read | Hiermee wordt een subnet definitie van een virtueel netwerk opgehaald |
> | Actie | Micro soft. netwerk/virtualNetworks/subnetten/unprepareNetworkPolicies/actie | De voor bereiding van een subnet ongedaan maken door het toegepaste netwerk beleid te verwijderen |
> | Actie | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Hiermee worden verwijzingen naar alle virtuele machines in een subnet van een virtueel netwerk opgehaald |
> | Actie | Microsoft.Network/virtualNetworks/subnets/write | Hiermee wordt een subnet van een virtueel netwerk gemaakt of een bestaand subnet van een virtueel netwerk bijgewerkt |
> | Actie | Microsoft.Network/virtualNetworks/usages/read | De IP-gebruik voor elk subnet van het virtuele netwerk ophalen |
> | Actie | Microsoft.Network/virtualNetworks/virtualMachines/read | Hiermee worden verwijzingen naar alle virtuele machines in een virtueel netwerk opgehaald |
> | Actie | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Hiermee wordt een virtueel netwerk peering verwijderd |
> | Actie | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Hiermee wordt een definitie van een virtuele netwerk-peering opgehaald |
> | Actie | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Hiermee maakt u een virtueel netwerk of werkt u een bestaande virtuele netwerk peering bij |
> | Actie | Microsoft.Network/virtualNetworks/write | Hiermee wordt een virtueel netwerk gemaakt of een bestaand virtueel netwerk bijgewerkt |
> | Actie | Microsoft.Network/virtualNetworkTaps/delete | Virtual Network tikken verwijderen |
> | Actie | Microsoft.Network/virtualNetworkTaps/join/action | Tik op een virtueel netwerk tikken. Niet Alertable. |
> | Actie | Microsoft.Network/virtualNetworkTaps/read | Virtual Network tikken |
> | Actie | Microsoft.Network/virtualNetworkTaps/write | Virtual Network tikken maken of bijwerken |
> | Actie | Micro soft. Network/virtualRouters/verwijderen | Hiermee wordt een VirtualRouter verwijderd |
> | Actie | Micro soft. Network/virtualRouters/samen voegen/actie | Er wordt een VirtualRouter toegevoegd. Niet alertable. |
> | Actie | Micro soft. Network/virtualRouters/peering/verwijderen | Hiermee wordt een VirtualRouterPeering verwijderd |
> | Actie | Micro soft. Network/virtualRouters/peering/lezen | Hiermee wordt een VirtualRouterPeering opgehaald |
> | Actie | Micro soft. Network/virtualRouters/peering/schrijven | Hiermee maakt u een VirtualRouterPeering of werkt u een bestaande VirtualRouterPeering bij |
> | Actie | Micro soft. Network/virtualRouters/lezen | Hiermee wordt een VirtualRouter opgehaald |
> | Actie | Micro soft. Network/virtualRouters/schrijven | Hiermee maakt u een VirtualRouter of werkt u een bestaande VirtualRouter bij |
> | Actie | Microsoft.Network/virtualWans/delete | Hiermee verwijdert u een virtueel WAN |
> | Actie | Micro soft. Network/virtualwans/generateVpnProfile/Action | VirtualWanVpnServerConfiguration VpnProfile genereren |
> | Actie | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Hiermee verwijdert u een virtueel WAN-P2SVpnServerConfiguration |
> | Actie | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Hiermee wordt een virtuele WAN-P2SVpnServerConfiguration opgehaald |
> | Actie | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Hiermee wordt een virtuele WAN-P2SVpnServerConfiguration gemaakt of een bestaande virtuele WAN-P2SVpnServerConfiguration bijgewerkt |
> | Actie | Microsoft.Network/virtualWans/read | Een virtueel WAN ophalen |
> | Actie | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Hiermee worden ondersteunde VirtualWan-beveiligings providers opgehaald. |
> | Actie | Microsoft.Network/virtualWans/virtualHubs/read | Hiermee worden alle virtuele hubs opgehaald die naar een virtueel WAN verwijzen. |
> | Actie | Microsoft.Network/virtualwans/vpnconfiguration/action | Hiermee wordt een VPN-configuratie opgehaald |
> | Actie | Micro soft. Network/virtualwans/vpnServerConfigurations/Action | VirtualWanVpnServerConfigurations ophalen |
> | Actie | Microsoft.Network/virtualWans/vpnSites/read | Hiermee worden alle VPN-sites opgehaald die naar een virtueel WAN verwijzen. |
> | Actie | Microsoft.Network/virtualWans/write | Een virtueel WAN maken of bijwerken |
> | Actie | Microsoft.Network/vpnGateways/delete | Hiermee verwijdert u een VpnGateway. |
> | Actie | microsoft.network/vpngateways/listvpnconnectionshealth/action | Hiermee wordt de status van de verbinding voor alle of een subset van verbindingen op een VpnGateway |
> | Actie | Microsoft.Network/vpnGateways/read | Hiermee wordt een VpnGateway opgehaald. |
> | Actie | microsoft.network/vpngateways/reset/action | Hiermee wordt een VpnGateway opnieuw ingesteld |
> | Actie | microsoft.network/vpnGateways/vpnConnections/delete | Hiermee verwijdert u een VpnConnection. |
> | Actie | microsoft.network/vpnGateways/vpnConnections/read | Hiermee wordt een VpnConnection opgehaald. |
> | Actie | micro soft. Network/vpnGateways/vpnConnections/vpnLinkConnections/lezen | Hiermee wordt een VPN-koppelings verbinding opgehaald |
> | Actie | microsoft.network/vpnGateways/vpnConnections/write | Hiermee wordt een VpnConnection geplaatst. |
> | Actie | Microsoft.Network/vpnGateways/write | Hiermee wordt een VpnGateway geplaatst. |
> | Actie | Micro soft. Network/vpnServerConfigurations/verwijderen | VpnServerConfiguration verwijderen |
> | Actie | Micro soft. Network/vpnServerConfigurations/lezen | VpnServerConfiguration ophalen |
> | Actie | Micro soft. Network/vpnServerConfigurations/schrijven | VpnServerConfiguration maken of bijwerken |
> | Actie | Microsoft.Network/vpnsites/delete | Hiermee verwijdert u een bron van een VPN-site. |
> | Actie | Microsoft.Network/vpnsites/read | Hiermee wordt een bron van een VPN-site opgehaald. |
> | Actie | micro soft. Network/vpnSites/vpnSiteLinks/lezen | Hiermee wordt een VPN-site koppeling opgehaald |
> | Actie | Microsoft.Network/vpnsites/write | Hiermee wordt een bron van een VPN-site gemaakt of bijgewerkt. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Hiermee wordt gecontroleerd of een opgegeven naam ruimte resource beschikbaar is in de NotificationHub-service. |
> | Actie | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | De lijst met beschrijvingen van autorisatie regels voor naam ruimten ophalen. |
> | Actie | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Naam ruimte autorisatie regel verwijderen. De standaard regel voor autorisatie van de naam ruimte kan niet worden verwijderd.  |
> | Actie | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | De verbindings reeks ophalen voor de naam ruimte |
> | Actie | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | De lijst met beschrijvingen van autorisatie regels voor naam ruimten ophalen. |
> | Actie | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Regel voor naam ruimte autorisatie primaire/secundaire sleutel opnieuw genereren, de sleutel opgeven die opnieuw moet worden gegenereerd |
> | Actie | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Maak autorisatie regels voor het naam ruimte niveau en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels, de primaire en secundaire sleutels kunnen worden bijgewerkt. |
> | Actie | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Hiermee wordt gecontroleerd of een opgegeven NotificationHub-naam beschikbaar is in een naam ruimte. |
> | Actie | Microsoft.NotificationHubs/Namespaces/Delete | Naam ruimte resource verwijderen |
> | Actie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | De lijst met autorisatie regels voor notification hub ophalen |
> | Actie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Autorisatie regels voor notification hub verwijderen |
> | Actie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | De verbindings reeks ophalen voor de notification hub |
> | Actie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | De lijst met autorisatie regels voor notification hub ophalen |
> | Actie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Verificatie regel voor notification hub opnieuw genereren primair/secundaire sleutel, de sleutel opgeven die opnieuw moet worden gegenereerd |
> | Actie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Maak autorisatie regels voor de notification hub en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels, de primaire en secundaire sleutels kunnen worden bijgewerkt. |
> | Actie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Een test push melding verzenden. |
> | Actie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Resource voor notification hub verwijderen |
> | Actie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Lijst met resource beschrijvingen voor metrische gegevens van de naam ruimte ophalen |
> | Actie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Alle PNS-referenties voor notification hub ophalen. Dit omvat, WNS, MPNS, APNS-, GCM-en Baidu-referenties |
> | Actie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Lijst met resource beschrijvingen voor notification hub ophalen |
> | Actie | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Een notification hub maken en de eigenschappen ervan bijwerken. De eigenschappen omvatten voornamelijk PNS-referenties. Autorisatie regels en TTL |
> | Actie | Microsoft.NotificationHubs/Namespaces/read | De lijst met de resource beschrijving van de naam ruimte ophalen |
> | Actie | Microsoft.NotificationHubs/Namespaces/write | Maak een naam ruimte resource en werk de eigenschappen bij. Tags en capaciteit van de naam ruimte zijn de eigenschappen die kunnen worden bijgewerkt. |
> | Actie | Microsoft.NotificationHubs/operationResults/read | Hiermee worden de bewerkings resultaten voor Notification Hubs provider geretourneerd |
> | Actie | Microsoft.NotificationHubs/operations/read | Hiermee wordt een lijst met ondersteunde bewerkingen voor Notification Hubs provider geretourneerd |
> | Actie | Microsoft.NotificationHubs/register/action | Hiermee wordt het abonnement voor de notification hubs-resource provider geregistreerd en wordt het maken van naam ruimten en notification hubs |
> | Actie | Microsoft.NotificationHubs/unregister/action | Hiermee wordt de registratie van het abonnement voor de notification hubs-resource provider ongedaan gemaakt en wordt het maken van naam ruimten en notification hubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.OffAzure/HyperVSites/clusters/read | Hiermee worden de eigenschappen van een Hyper-V-cluster opgehaald |
> | Actie | Microsoft.OffAzure/HyperVSites/clusters/write | Hiermee wordt het Hyper-V-cluster gemaakt of bijgewerkt |
> | Actie | Microsoft.OffAzure/HyperVSites/delete | Hiermee verwijdert u de Hyper-V-site |
> | Actie | Micro soft. OffAzure/HyperVSites/healthsummary/lezen | Hiermee wordt het status overzicht voor Hyper-V-resource opgehaald |
> | Actie | Microsoft.OffAzure/HyperVSites/hosts/read | Hiermee worden de eigenschappen van een Hyper-V-host opgehaald |
> | Actie | Microsoft.OffAzure/HyperVSites/hosts/write | Hiermee wordt de Hyper-V-host gemaakt of bijgewerkt |
> | Actie | Microsoft.OffAzure/HyperVSites/jobs/read | Hiermee worden de eigenschappen van een Hyper-V-taak opgehaald |
> | Actie | Microsoft.OffAzure/HyperVSites/machines/read | Hiermee worden de eigenschappen van een Hyper-V-computer opgehaald |
> | Actie | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hiermee worden de eigenschappen van de status van een Hyper-V-bewerking opgehaald |
> | Actie | Microsoft.OffAzure/HyperVSites/read | Hiermee worden de eigenschappen van een Hyper-V-site opgehaald |
> | Actie | Microsoft.OffAzure/HyperVSites/refresh/action | Hiermee worden de objecten op een Hyper-V-site vernieuwd |
> | Actie | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hiermee worden de eigenschappen van een Hyper-V run as-accounts opgehaald |
> | Actie | Microsoft.OffAzure/HyperVSites/usage/read | Hiermee worden de gebruiks handelingen van een Hyper-V-site opgehaald |
> | Actie | Microsoft.OffAzure/HyperVSites/write | Hiermee wordt de Hyper-V-site gemaakt of bijgewerkt |
> | Actie | Microsoft.OffAzure/Operations/read | Hiermee worden de beschik bare bewerkingen gelezen |
> | Actie | Microsoft.OffAzure/register/action | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. OffAzure |
> | Actie | Micro soft. OffAzure/ServerSites/Jobs/lezen | Hiermee worden de eigenschappen van een server taak opgehaald |
> | Actie | Micro soft. OffAzure/ServerSites/machines/lezen | Hiermee worden de eigenschappen van een Server computer opgehaald |
> | Actie | Micro soft. OffAzure/ServerSites/machines/schrijven | De eigenschappen van een Server computer schrijven |
> | Actie | Micro soft. OffAzure/ServerSites/operationsstatus/lezen | Hiermee worden de eigenschappen van de bewerkings status van de server opgehaald |
> | Actie | Micro soft. OffAzure/ServerSites/lezen | Hiermee worden de eigenschappen van een server site opgehaald |
> | Actie | Micro soft. OffAzure/ServerSites/Refresh/actie | Hiermee worden de objecten in een server site vernieuwd |
> | Actie | Micro soft. OffAzure/ServerSites/runasaccounts/lezen | Hiermee worden de eigenschappen van een run as-account van een server opgehaald |
> | Actie | Micro soft. OffAzure/ServerSites/gebruik/lezen | Hiermee worden de gebruiks locaties van een server site opgehaald |
> | Actie | Micro soft. OffAzure/ServerSites/schrijven | Hiermee wordt de server site gemaakt of bijgewerkt |
> | Actie | Microsoft.OffAzure/VMwareSites/delete | Hiermee verwijdert u de VMware-site |
> | Actie | Micro soft. OffAzure/VMwareSites/healthsummary/lezen | Hiermee wordt het status overzicht voor VMware-resource opgehaald |
> | Actie | Microsoft.OffAzure/VMwareSites/jobs/read | Hiermee worden de eigenschappen van een VMware-taak opgehaald |
> | Actie | Microsoft.OffAzure/VMwareSites/machines/read | Hiermee worden de eigenschappen van een VMware-machine opgehaald |
> | Actie | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware-machines starten |
> | Actie | Microsoft.OffAzure/VMwareSites/machines/stop/action | Stopt de VMware-machines |
> | Actie | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Hiermee worden de eigenschappen van een VMware-bewerkings status opgehaald |
> | Actie | Microsoft.OffAzure/VMwareSites/read | Hiermee worden de eigenschappen van een VMware-site opgehaald |
> | Actie | Microsoft.OffAzure/VMwareSites/refresh/action | Hiermee worden de objecten in een VMware-site vernieuwd |
> | Actie | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Hiermee worden de eigenschappen van een VMware run as-accounts opgehaald |
> | Actie | Microsoft.OffAzure/VMwareSites/usage/read | Hiermee worden de gebruiks handelingen van een VMware-site opgehaald |
> | Actie | Microsoft.OffAzure/VMwareSites/vcenters/read | Hiermee worden de eigenschappen van een VMware vCenter opgehaald |
> | Actie | Microsoft.OffAzure/VMwareSites/vcenters/write | Hiermee wordt de VMware vCenter gemaakt of bijgewerkt |
> | Actie | Microsoft.OffAzure/VMwareSites/write | Hiermee wordt de VMware-site gemaakt of bijgewerkt |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.OperationalInsights/linkTargets/read | Een lijst met bestaande accounts die niet zijn gekoppeld aan een Azure-abonnement. Als u dit Azure-abonnement aan een werk ruimte wilt koppelen, gebruikt u een klant-id die door deze bewerking is geretourneerd in de eigenschap Customer ID van de bewerking werk ruimte maken. |
> | Actie | microsoft.operationalinsights/operations/read | Een lijst met alle beschik bare OperationalInsights rest API-bewerkingen. |
> | Actie | microsoft.operationalinsights/register/action | Rergisters het abonnement. |
> | Actie | Microsoft.OperationalInsights/register/action | Een abonnement bij een resource provider registreren. |
> | Actie | microsoft.operationalinsights/unregister/action | Hiermee maakt u de registratie van het abonnement ongedaan. |
> | Actie | Microsoft.OperationalInsights/workspaces/analytics/query/action | Zoek met een nieuwe engine. |
> | Actie | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Zoek schema v2 ophalen. |
> | Actie | Microsoft.OperationalInsights/workspaces/api/query/action | Zoek met een nieuwe engine. |
> | Actie | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Zoek schema v2 ophalen. |
> | Actie | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Configuratie bereik verwijderen |
> | Actie | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Configuratie bereik ophalen |
> | Actie | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Configuratie bereik instellen |
> | Actie | Microsoft.OperationalInsights/workspaces/datasources/delete | Gegevens bronnen onder een werk ruimte verwijderen. |
> | Actie | Microsoft.OperationalInsights/workspaces/datasources/read | Gegevens bronnen onder een werk ruimte ophalen. |
> | Actie | Microsoft.OperationalInsights/workspaces/datasources/write | Gegevens bronnen onder een werk ruimte maken/bijwerken. |
> | Actie | Microsoft.OperationalInsights/workspaces/delete | Hiermee verwijdert u een werk ruimte. Als de werk ruimte is gekoppeld aan een bestaande werk ruimte tijdens de aanmaak tijd, wordt de werk ruimte waaraan deze is gekoppeld, niet verwijderd. |
> | Actie | Microsoft.OperationalInsights/workspaces/gateways/delete | Hiermee verwijdert u een gateway die is geconfigureerd voor de werk ruimte. |
> | Actie | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Hiermee wordt een registratie certificaat voor de werk ruimte gegenereerd. Dit certificaat wordt gebruikt om micro soft System Center Operations Manager te verbinden met de werk ruimte. |
> | Actie | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Hiermee wordt een Intelligence Pack voor een opgegeven werk ruimte uitgeschakeld. |
> | Actie | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Hiermee wordt een Intelligence Pack voor een opgegeven werk ruimte ingeschakeld. |
> | Actie | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Hiermee worden alle Intelligence packs weer gegeven die zichtbaar zijn voor een bepaalde werk ruimte en wordt ook vermeld of het pakket is ingeschakeld of uitgeschakeld voor die werk ruimte. |
> | Actie | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Gekoppelde services onder de opgegeven werk ruimte verwijderen. |
> | Actie | Microsoft.OperationalInsights/workspaces/linkedServices/read | Gekoppelde services onder de opgegeven werk ruimte ophalen. |
> | Actie | Microsoft.OperationalInsights/workspaces/linkedServices/write | Gekoppelde services onder de opgegeven werk ruimte maken/bijwerken. |
> | Actie | Microsoft.OperationalInsights/workspaces/listKeys/action | Hiermee haalt u de lijst sleutels voor de werk ruimte op. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | Actie | Microsoft.OperationalInsights/workspaces/listKeys/read | Hiermee haalt u de lijst sleutels voor de werk ruimte op. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | Actie | Microsoft.OperationalInsights/workspaces/managementGroups/read | Hiermee worden de namen en meta gegevens opgehaald voor System Center Operations Manager-beheer groepen die met deze werk ruimte zijn verbonden. |
> | Actie | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Metrische definities ophalen onder werk ruimte |
> | Actie | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | De instellingen voor meldingen voor de gebruiker verwijderen voor de werk ruimte. |
> | Actie | Microsoft.OperationalInsights/workspaces/notificationSettings/read | De instellingen voor meldingen voor de gebruiker ophalen voor de werk ruimte. |
> | Actie | Microsoft.OperationalInsights/workspaces/notificationSettings/write | De instellingen voor meldingen voor de gebruiker instellen voor de werk ruimte. |
> | Actie | microsoft.operationalinsights/workspaces/operations/read | Hiermee wordt de status van een OperationalInsights-werkruimte bewerking opgehaald. |
> | Actie | Microsoft.OperationalInsights/workspaces/purge/action | Opgegeven gegevens uit de werk ruimte verwijderen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Gegevens uit de AADDomainServicesAccountLogon-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Gegevens uit de AADDomainServicesAccountManagement-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Gegevens uit de AADDomainServicesDirectoryServiceAccess-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Gegevens uit de AADDomainServicesLogonLogoff-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | Gegevens uit de AADDomainServicesPolicyChange-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Gegevens uit de AADDomainServicesPrivilegeUse-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Gegevens uit de AADDomainServicesSystemSecurity-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Gegevens uit de ADAssessmentRecommendation-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AddonAzureBackupAlerts/lezen | Gegevens uit de AddonAzureBackupAlerts-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AddonAzureBackupJobs/lezen | Gegevens uit de AddonAzureBackupJobs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AddonAzureBackupPolicy/lezen | Gegevens uit de AddonAzureBackupPolicy-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AddonAzureBackupProtectedInstance/lezen | Gegevens uit de AddonAzureBackupProtectedInstance-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AddonAzureBackupStorage/lezen | Gegevens uit de AddonAzureBackupStorage-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Gegevens uit de ADFActivityRun-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Gegevens uit de ADFPipelineRun-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Gegevens uit de ADFTriggerRun-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Gegevens uit de ADReplicationResult-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Gegevens uit de ADSecurityAssessmentRecommendation-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AegDeliveryFailureLogs/lezen | Gegevens uit de AegDeliveryFailureLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AegPublishFailureLogs/lezen | Gegevens uit de AegPublishFailureLogs-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/Alert/read | Gegevens uit de tabel waarschuwing lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Gegevens uit de AlertHistory-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AmlComputeClusterEvent/lezen | Gegevens uit de AmlComputeClusterEvent-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AmlComputeClusterNodeEvent/lezen | Gegevens uit de AmlComputeClusterNodeEvent-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AmlComputeJobEvent/lezen | Gegevens uit de AmlComputeJobEvent-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/ApiManagementGatewayLogs/lezen | Gegevens uit de ApiManagementGatewayLogs-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Gegevens uit de AppCenterError-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Gegevens uit de ApplicationInsights-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AppPlatformLogsforSpring/lezen | Gegevens uit de AppPlatformLogsforSpring-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AppPlatformSystemLogs/lezen | Gegevens uit de AppPlatformSystemLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AppServiceAppLogs/lezen | Gegevens uit de AppServiceAppLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AppServiceAuditLogs/lezen | Gegevens uit de AppServiceAuditLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AppServiceConsoleLogs/lezen | Gegevens uit de AppServiceConsoleLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AppServiceEnvironmentPlatformLogs/lezen | Gegevens uit de AppServiceEnvironmentPlatformLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/AppServiceHTTPLogs/lezen | Gegevens uit de AppServiceHTTPLogs-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Gegevens uit de audit logs bevat-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Gegevens uit de AutoscaleEvaluationsLog-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Gegevens uit de AutoscaleScaleActionsLog-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Gegevens uit de AWSCloudTrail-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Gegevens uit de AzureActivity-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Gegevens uit de AzureAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Gegevens uit de AzureMetrics-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/BaiClusterEvent/lezen | Gegevens uit de BaiClusterEvent-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/BaiClusterNodeEvent/lezen | Gegevens uit de BaiClusterNodeEvent-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/BaiJobEvent/lezen | Gegevens uit de BaiJobEvent-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | Gegevens uit de BlockchainApplicationLog-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | Gegevens uit de BlockchainProxyLog-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Gegevens uit de BoundPort-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Gegevens uit de CommonSecurityLog-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Gegevens uit de ComputerGroup-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Gegevens uit de ConfigurationChange-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Gegevens uit de ConfigurationData-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Gegevens uit de ContainerImageInventory-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Gegevens uit de ContainerInventory-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Gegevens uit de ContainerLog-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Gegevens uit de ContainerNodeInventory-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/ContainerRegistryLoginEvents/lezen | Gegevens uit de ContainerRegistryLoginEvents-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/ContainerRegistryRepositoryEvents/lezen | Gegevens uit de ContainerRegistryRepositoryEvents-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Gegevens uit de ContainerServiceLog-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/CoreAzureBackup/lezen | Gegevens uit de CoreAzureBackup-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Gegevens uit de DatabricksAccounts-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Gegevens uit de DatabricksClusters-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Gegevens uit de DatabricksDBFS-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/DatabricksInstancePools/lezen | Gegevens uit de DatabricksInstancePools-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Gegevens uit de DatabricksJobs-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Gegevens uit de DatabricksNotebook-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Gegevens uit de DatabricksSecrets-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Gegevens uit de DatabricksSQLPermissions-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Gegevens uit de DatabricksSSH-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Gegevens uit de DatabricksTables-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Gegevens uit de DatabricksWorkspace-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Gegevens uit de DeviceAppCrash-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Gegevens uit de DeviceAppLaunch-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Gegevens uit de DeviceCalendar-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Gegevens uit de DeviceCleanup-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Gegevens uit de DeviceConnectSession-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Gegevens uit de DeviceEtw-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Gegevens uit de DeviceHardwareHealth-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Gegevens uit de apparaatstatus-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Gegevens uit de DeviceHeartbeat-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Gegevens uit de DeviceSkypeHeartbeat-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Gegevens uit de DeviceSkypeSignIn-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Gegevens uit de DeviceSleepState-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Gegevens uit de DHAppFailure-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Gegevens uit de DHAppReliability-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/DHCPActivity/lezen | Gegevens uit de DHCPActivity-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Gegevens uit de DHDriverReliability-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Gegevens uit de DHLogonFailures-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Gegevens uit de DHLogonMetrics-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Gegevens uit de DHOSCrashData-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Gegevens uit de DHOSReliability-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Gegevens uit de DHWipAppLearning-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Gegevens uit de DnsEvents-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Gegevens uit de DnsInventory-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Gegevens uit de ETWEvent-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/Event/read | Gegevens uit de gebeurtenis tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Gegevens uit de ExchangeAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Gegevens uit de ExchangeOnlineAssessmentRecommendation-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/FailedIngestion/lezen | Gegevens uit de FailedIngestion-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/FunctionAppLogs/lezen | Gegevens uit de FunctionAppLogs-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Gegevens uit de heartbeat-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Gegevens uit de HuntingBookmark-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Gegevens uit de IISAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Gegevens uit de InboundConnection-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Gegevens uit de InsightsMetrics-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Gegevens uit de IntuneAuditLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/IntuneDeviceComplianceOrg/lezen | Gegevens uit de IntuneDeviceComplianceOrg-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Gegevens uit de IntuneOperationalLogs-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Gegevens uit de KubeEvents-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/KubeHealth/lezen | Gegevens uit de KubeHealth-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/KubeMonAgentEvents/lezen | Gegevens uit de KubeMonAgentEvents-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Gegevens uit de KubeNodeInventory-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Gegevens uit de KubePodInventory-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Gegevens uit de KubeServices-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Gegevens uit de LinuxAuditLog-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Gegevens uit de MAApplication-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Gegevens uit de MAApplicationHealth-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Gegevens uit de MAApplicationHealthAlternativeVersions-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Gegevens uit de MAApplicationHealthIssues-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Gegevens uit de MAApplicationInstance-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Gegevens uit de MAApplicationInstanceReadiness-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Gegevens uit de MAApplicationReadiness-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Gegevens uit de MADeploymentPlan-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Gegevens uit de MADevice-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Gegevens uit de MADeviceNotEnrolled-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Gegevens uit de MADeviceNRT-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Gegevens uit de MADevicePnPHealth-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Gegevens uit de MADevicePnPHealthAlternativeVersions-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Gegevens uit de MADevicePnPHealthIssues-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Gegevens uit de MADeviceReadiness-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Gegevens uit de MADriverInstanceReadiness-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Gegevens uit de MADriverReadiness-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Gegevens uit de MAOfficeAddin-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Gegevens uit de MAOfficeAddinEntityHealth-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Gegevens uit de MAOfficeAddinHealth-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Gegevens uit de MAOfficeAddinHealthEventNRT-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Gegevens uit de MAOfficeAddinHealthIssues-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Gegevens uit de MAOfficeAddinInstance-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Gegevens uit de MAOfficeAddinInstanceReadiness-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Gegevens uit de MAOfficeAddinReadiness-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Gegevens uit de MAOfficeApp-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Gegevens uit de MAOfficeAppCrashesNRT-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Gegevens uit de MAOfficeAppHealth-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Gegevens uit de MAOfficeAppInstance-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Gegevens uit de MAOfficeAppInstanceHealth-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Gegevens uit de MAOfficeAppReadiness-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Gegevens uit de MAOfficeAppSessionsNRT-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Gegevens uit de MAOfficeBuildInfo-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Gegevens uit de MAOfficeCurrencyAssessment-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Gegevens uit de MAOfficeCurrencyAssessmentDailyCounts-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Gegevens uit de MAOfficeDeploymentStatus-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Gegevens uit de MAOfficeDeploymentStatusNRT-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Gegevens uit de MAOfficeMacroErrorNRT-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Gegevens uit de MAOfficeMacroGlobalHealth-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Gegevens uit de MAOfficeMacroHealth-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Gegevens uit de MAOfficeMacroHealthIssues-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Gegevens uit de MAOfficeMacroIssueInstanceReadiness-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Gegevens uit de MAOfficeMacroIssueReadiness-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Gegevens uit de MAOfficeMacroSummary-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Gegevens uit de MAOfficeSuite-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Gegevens uit de MAOfficeSuiteInstance-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Gegevens uit de MAProposedPilotDevices-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Gegevens uit de MAWindowsBuildInfo-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Gegevens uit de MAWindowsCurrencyAssessment-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Gegevens uit de MAWindowsCurrencyAssessmentDailyCounts-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Gegevens uit de MAWindowsDeploymentStatus-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Gegevens uit de MAWindowsDeploymentStatusNRT-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Gegevens uit de MAWindowsSysReqInstanceReadiness-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/McasShadowItReporting/lezen | Gegevens uit de McasShadowItReporting-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftAzureBastionAuditLogs/lezen | Gegevens uit de MicrosoftAzureBastionAuditLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftDataShareReceivedSnapshotLog/lezen | Gegevens uit de MicrosoftDataShareReceivedSnapshotLog-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftDataShareSentSnapshotLog/lezen | Gegevens uit de MicrosoftDataShareSentSnapshotLog-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftDataShareShareLog/lezen | Gegevens uit de MicrosoftDataShareShareLog-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftDynamicsTelemetryPerformanceLogs/lezen | Gegevens uit de MicrosoftDynamicsTelemetryPerformanceLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftDynamicsTelemetrySystemMetricsLogs/lezen | Gegevens uit de MicrosoftDynamicsTelemetrySystemMetricsLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftHealthcareApisAuditLogs/lezen | Gegevens uit de MicrosoftHealthcareApisAuditLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftInsightsAzureActivityLog/lezen | Gegevens uit de MicrosoftInsightsAzureActivityLog-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Gegevens uit de NetworkMonitoring-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Gegevens uit de OfficeActivity-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/Operation/read | Gegevens uit de bewerkings tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Gegevens uit de OutboundConnection-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/Perf/read | Gegevens uit de prestatie tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Gegevens uit de ProtectionStatus-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/read | Query's uitvoeren voor de gegevens in de werk ruimte |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Gegevens uit de ReservedCommonFields-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Gegevens uit de SCCMAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Gegevens uit de SCOMAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Gegevens uit de SecurityAlert-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Gegevens uit de Security Baseline Baseline-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Gegevens uit de Summary-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Gegevens uit de SecurityDetection-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Gegevens uit de SecurityEvent-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Gegevens uit de SecurityIoTRawEvent-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Gegevens uit de SecurityRecommendation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Gegevens uit de ServiceFabricOperationalEvent-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Gegevens uit de ServiceFabricReliableActorEvent-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Gegevens uit de ServiceFabricReliableServiceEvent-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Gegevens uit de SfBAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Gegevens uit de SfBOnlineAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Gegevens uit de SharePointOnlineAssessmentRecommendation-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/SignalRServiceDiagnosticLogs/lezen | Gegevens uit de SignalRServiceDiagnosticLogs-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Gegevens uit de SigninLogs-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Gegevens uit de SPAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Gegevens uit de SQLAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Gegevens uit de SQLQueryPerformance-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Gegevens uit de SqlThreatProtectionLoginAudits-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Gegevens uit de SqlVulnerabilityAssessmentResult-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/StorageBlobLogs/lezen | Gegevens uit de StorageBlobLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/StorageFileLogs/lezen | Gegevens uit de StorageFileLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/StorageQueueLogs/lezen | Gegevens uit de StorageQueueLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/StorageTableLogs/lezen | Gegevens uit de StorageTableLogs-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/SucceededIngestion/lezen | Gegevens uit de SucceededIngestion-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Gegevens uit de syslog-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Gegevens uit de SysmonEvent-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Gegevens uit een aangepast logboek lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Gegevens uit de ThreatIntelligenceIndicator-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Gegevens uit de UAApp-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Gegevens uit de UAComputer-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Gegevens uit de UAComputerRank-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Gegevens uit de UADriver-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Gegevens uit de UADriverProblemCodes-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Gegevens uit de UAFeedback-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Gegevens uit de UAHardwareSecurity-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Gegevens uit de UAIESiteDiscovery-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Gegevens uit de UAOfficeAddIn-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Gegevens uit de UAProposedActionPlan-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Gegevens uit de UASysReqIssue-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Gegevens uit de UAUpgradedComputer-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/Update/read | Gegevens uit de update tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Gegevens uit de Updaterunprogress Installation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Gegevens uit de update Summary-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/Usage/read | Gegevens uit de gebruiks tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Gegevens uit de VMBoundPort-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/VMComputer/lezen | Gegevens uit de VMComputer-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Gegevens uit de VMConnection-tabel lezen |
> | Actie | Micro soft. OperationalInsights/werk ruimten/query/VMProcess/lezen | Gegevens uit de VMProcess-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Gegevens uit de W3CIISLog-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Gegevens uit de WaaSDeploymentStatus-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Gegevens uit de WaaSInsiderStatus-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Gegevens uit de WaaSUpdateStatus-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Gegevens uit de WDAVStatus-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Gegevens uit de WDAVThreat-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Gegevens uit de WindowsClientAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Gegevens uit de WindowsEvent-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Gegevens uit de WindowsFirewall-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Gegevens uit de WindowsServerAssessmentRecommendation-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WireData/read | Gegevens uit de WireData-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Gegevens uit de WorkloadMonitoringPerf-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Gegevens uit de WUDOAggregatedStatus-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Gegevens uit de WUDOStatus-tabel lezen |
> | Actie | Microsoft.OperationalInsights/workspaces/read | Hiermee wordt een bestaande werk ruimte opgehaald |
> | Actie | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Hiermee wordt de opgegeven gedeelde sleutel voor de werk ruimte opnieuw gegenereerd |
> | Actie | microsoft.operationalinsights/workspaces/rules/read | Alle waarschuwings regels ophalen. |
> | Actie | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Hiermee wordt een opgeslagen Zoek query verwijderd |
> | Actie | Microsoft.OperationalInsights/workspaces/savedSearches/read | Hiermee wordt een opgeslagen Zoek query opgehaald |
> | Actie | microsoft.operationalinsights/workspaces/savedsearches/results/read | Resultaten voor opgeslagen Zoek opdrachten ophalen. Afgeschaft |
> | Actie | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Geplande zoek acties verwijderen. |
> | Actie | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Geplande zoek acties ophalen. |
> | Actie | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Geplande zoek acties maken of bijwerken. |
> | Actie | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Geplande Zoek opdrachten verwijderen. |
> | Actie | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Geplande Zoek opdrachten ophalen. |
> | Actie | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Geplande Zoek opdrachten maken of bijwerken. |
> | Actie | Microsoft.OperationalInsights/workspaces/savedSearches/write | Hiermee wordt een opgeslagen Zoek query gemaakt |
> | Actie | Microsoft.OperationalInsights/workspaces/schema/read | Hiermee wordt het zoek schema voor de werk ruimte opgehaald.  Zoek schema bevat de weer gegeven velden en de bijbehorende typen. |
> | Actie | Microsoft.OperationalInsights/workspaces/search/action | Hiermee wordt een zoek query uitgevoerd |
> | Actie | microsoft.operationalinsights/workspaces/search/read | Zoek resultaten ophalen. Afgeschaft. |
> | Actie | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Hiermee worden de gedeelde sleutels voor de werk ruimte opgehaald. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | Actie | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Hiermee worden de gedeelde sleutels voor de werk ruimte opgehaald. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | Actie | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Hiermee verwijdert u een opslag configuratie. Hiermee wordt voor komen dat micro soft Operational Insights het lezen van gegevens uit het opslag account. |
> | Actie | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Hiermee wordt een opslag configuratie opgehaald. |
> | Actie | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Hiermee maakt u een nieuwe opslag configuratie. Deze configuraties worden gebruikt voor het ophalen van gegevens van een locatie in een bestaand opslag account. |
> | Actie | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Fout logboek voor de upgrade van de zoek actie ophalen voor de werk ruimte |
> | Actie | Microsoft.OperationalInsights/workspaces/usages/read | Hiermee worden gebruiks gegevens voor een werk ruimte opgehaald, inclusief de hoeveelheid gegevens die door de werk ruimte wordt gelezen. |
> | Actie | Microsoft.OperationalInsights/workspaces/write | Hiermee maakt u een nieuwe werk ruimte of koppelingen naar een bestaande werk ruimte door de klant-id van de bestaande werk ruimte op te geven. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.OperationsManagement/managementAssociations/delete | Bestaande beheer koppeling verwijderen |
> | Actie | Microsoft.OperationsManagement/managementAssociations/read | Bestaande beheer koppeling ophalen |
> | Actie | Microsoft.OperationsManagement/managementAssociations/write | Een nieuwe beheer koppeling maken |
> | Actie | Microsoft.OperationsManagement/managementConfigurations/delete | Bestaande beheer configuratie verwijderen |
> | Actie | Microsoft.OperationsManagement/managementConfigurations/read | Bestaande beheer configuratie ophalen |
> | Actie | Microsoft.OperationsManagement/managementConfigurations/write | Een nieuwe beheer configuratie maken |
> | Actie | Microsoft.OperationsManagement/register/action | Een abonnement bij een resource provider registreren. |
> | Actie | Microsoft.OperationsManagement/solutions/delete | Bestaande OMS-oplossing verwijderen |
> | Actie | Microsoft.OperationsManagement/solutions/read | De OMS-oplossing ophalen |
> | Actie | Microsoft.OperationsManagement/solutions/write | Nieuwe OMS-oplossing maken |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.PolicyInsights/asyncOperationResults/read | Hiermee wordt het resultaat van de asynchrone bewerking opgehaald. |
> | DataAction | Micro soft. PolicyInsights/checkDataPolicyCompliance/actie | Controleer de nalevings status van een bepaald onderdeel met gegevens beleid. |
> | Actie | Microsoft.PolicyInsights/operations/read | Hiermee worden ondersteunde bewerkingen voor de micro soft. PolicyInsights-naam ruimte opgehaald |
> | DataAction | Micro soft. PolicyInsights/policyEvents/logDataEvents/Action | De gebeurtenissen van het bron onderdeel beleid registreren. |
> | Actie | Microsoft.PolicyInsights/policyEvents/queryResults/action | Informatie over beleids gebeurtenissen opvragen. |
> | Actie | Microsoft.PolicyInsights/policyEvents/queryResults/read | Informatie over beleids gebeurtenissen opvragen. |
> | Actie | Micro soft. PolicyInsights/policyMetadata/lezen | Meta gegevens van het beleid ophalen. |
> | Actie | Microsoft.PolicyInsights/policyStates/queryResults/action | Informatie over beleids statussen opvragen. |
> | Actie | Microsoft.PolicyInsights/policyStates/queryResults/read | Informatie over beleids statussen opvragen. |
> | Actie | Microsoft.PolicyInsights/policyStates/summarize/action | Query samenvattings informatie over de meest recente beleids status. |
> | Actie | Microsoft.PolicyInsights/policyStates/summarize/read | Query samenvattings informatie over de meest recente beleids status. |
> | Actie | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Hiermee wordt een nieuwe compatibiliteits evaluatie geactiveerd voor het geselecteerde bereik. |
> | Actie | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Query gegevens over de resources die vereist zijn voor DeployIfNotExists-beleid. |
> | Actie | Microsoft.PolicyInsights/register/action | Hiermee wordt de micro soft Policy Insights-resource provider geregistreerd en worden er acties op uitgevoerd. |
> | Actie | Microsoft.PolicyInsights/remediations/cancel/action | Hiermee wordt de uitvoering van micro soft-beleids herstel geannuleerd. |
> | Actie | Microsoft.PolicyInsights/remediations/delete | Verwijder beleids herstel. |
> | Actie | Microsoft.PolicyInsights/remediations/listDeployments/read | Geeft een lijst van de implementaties die vereist zijn voor het door voeren van een beleid. |
> | Actie | Microsoft.PolicyInsights/remediations/read | Beleids herstel ophalen. |
> | Actie | Microsoft.PolicyInsights/remediations/write | Herstel bewerkingen van micro soft-beleid maken of bijwerken. |
> | Actie | Microsoft.PolicyInsights/unregister/action | Hiermee wordt de registratie van de micro soft Policy Insights-resource provider ongedaan gemaakt. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Portal/consoles/delete | Hiermee verwijdert u het Cloud Shell-exemplaar. |
> | Actie | Microsoft.Portal/consoles/read | Hiermee wordt het Cloud Shell-exemplaar gelezen. |
> | Actie | Microsoft.Portal/consoles/write | Een Cloud Shell-exemplaar maken of bijwerken. |
> | Actie | Microsoft.Portal/dashboards/delete | Hiermee verwijdert u het dash board uit het abonnement. |
> | Actie | Microsoft.Portal/dashboards/read | Hiermee worden de Dash boards voor het abonnement gelezen. |
> | Actie | Microsoft.Portal/dashboards/write | Dash board toevoegen aan of wijzigen in een abonnement. |
> | Actie | Microsoft.Portal/register/action | Registreren bij de portal |
> | Actie | Microsoft.Portal/usersettings/delete | Hiermee verwijdert u de Cloud Shell gebruikers instellingen. |
> | Actie | Microsoft.Portal/usersettings/read | Hiermee worden de Cloud Shell gebruikers instellingen gelezen. |
> | Actie | Microsoft.Portal/usersettings/write | Cloud Shell gebruikers instelling maken of bijwerken. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. PowerBIDedicated/capaciteiten/verwijderen | Hiermee wordt de toegewezen capaciteit van Power BI verwijderd. |
> | Actie | Micro soft. PowerBIDedicated/capaciteiten/lezen | Hiermee wordt de informatie opgehaald van de opgegeven Power BI toegewezen capaciteit. |
> | Actie | Microsoft.PowerBIDedicated/capacities/resume/action | Hiermee wordt de capaciteit hervat. |
> | Actie | Micro soft. PowerBIDedicated/capaciteiten/sku's/lezen | Beschik bare SKU-gegevens voor de capaciteit ophalen |
> | Actie | Micro soft. PowerBIDedicated/capaciteiten/onderbreken/actie | Hiermee wordt de capaciteit onderbroken. |
> | Actie | Micro soft. PowerBIDedicated/capaciteiten/schrijven | Hiermee wordt de opgegeven Power BI toegewezen capaciteit gemaakt of bijgewerkt. |
> | Actie | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Controleert of de naam van Power BI toegewezen capaciteit geldig is en niet wordt gebruikt. |
> | Actie | Microsoft.PowerBIDedicated/locations/operationresults/read | Hiermee wordt de informatie opgehaald van het opgegeven bewerkings resultaat. |
> | Actie | Micro soft. PowerBIDedicated/locaties/operationstatuses/lezen | Hiermee wordt de informatie opgehaald van de opgegeven bewerkings status. |
> | Actie | Microsoft.PowerBIDedicated/operations/read | Hiermee wordt de informatie over bewerkingen opgehaald |
> | Actie | Microsoft.PowerBIDedicated/register/action | Registreert Power BI toegewezen resource provider. |
> | Actie | Microsoft.PowerBIDedicated/skus/read | Hiermee wordt de informatie van Sku's opgehaald |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Actie | Microsoft.RecoveryServices/locations/allocateStamp/action | Allo Cate Stamp is een interne bewerking die wordt gebruikt door de service |
> | Actie | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Actie | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Een beveiligd back-upitem maken |
> | Actie | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Hiermee wordt de lijst met alle beveiligde items geretourneerd. |
> | Actie | Microsoft.RecoveryServices/Locations/backupStatus/action | De back-upstatus voor Recovery Services kluizen controleren |
> | Actie | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Functies valideren |
> | Actie | Microsoft.RecoveryServices/locations/checkNameAvailability/action | De beschik baarheid van resource namen controleren is een API om te controleren of de resource naam beschikbaar is |
> | Actie | Microsoft.RecoveryServices/locations/operationStatus/read | Hiermee wordt de bewerkings status voor een bepaalde bewerking opgehaald |
> | Actie | Microsoft.RecoveryServices/operations/read | Met deze bewerking wordt de lijst met bewerkingen voor een resource provider geretourneerd |
> | Actie | Microsoft.RecoveryServices/register/action | Hiermee wordt een abonnement geregistreerd voor een bepaalde resource provider |
> | Actie | Microsoft.RecoveryServices/Vaults/backupconfig/read | Hiermee wordt de configuratie voor Recovery Services kluis geretourneerd. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupconfig/write | Hiermee wordt de configuratie van Recovery Services-kluis bijgewerkt. |
> | Actie | Micro soft. Recovery Services/kluizen/backupEncryptionConfigs/lezen | Hiermee wordt de versleutelings configuratie voor back-ups opgehaald. |
> | Actie | Micro soft. Recovery Services/kluizen/backupEncryptionConfigs/schrijven | Configuratie van back-upbron versleuteling |
> | Actie | Microsoft.RecoveryServices/Vaults/backupEngines/read | Retourneert alle back-upbeheerser vers die zijn geregistreerd bij de kluis. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Een opzet voor een back-upbeveiliging verwijderen |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Een doel voor back-upbeveiliging verkrijgen |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Een doel voor back-upbeveiliging maken |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Hiermee wordt de status van de bewerking geretourneerd |
> | Actie | Micro soft. Recovery Services/kluizen/backupFabrics/operationsStatus/lezen | Hiermee wordt de status van de bewerking geretourneerd |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Alle Beveilig bare containers ophalen |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Hiermee wordt de geregistreerde container verwijderd |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Een query uitvoeren voor werk belastingen binnen een container |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Alle items in een container ophalen |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op de beveiligings container opgehaald. |
> | Actie | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/operationsStatus/lezen | Hiermee wordt de status opgehaald van de bewerking die is uitgevoerd op de beveiligings container. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Maakt een back-up voor het beveiligde item. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Hiermee wordt het beveiligde item verwijderd |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op beveiligde items opgehaald. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Hiermee wordt de status geretourneerd van de bewerking die is uitgevoerd op beveiligde items. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Direct-item herstel inrichten voor beveiligd item |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Herstel punten voor beveiligde items ophalen. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Herstel punten voor beveiligde items herstellen. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Herstel van onmiddellijke items intrekken voor beveiligd item |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Hiermee worden alle geregistreerde containers geretourneerd |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Hiermee maakt u een geregistreerde container |
> | Actie | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Hiermee vernieuwt u de container lijst |
> | Actie | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | De taak annuleren |
> | Actie | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Hiermee wordt het resultaat van de taak bewerking geretourneerd. |
> | Actie | Micro soft. Recovery Services/kluizen/backupJobs/operationsStatus/lezen | Hiermee wordt de status van de taak bewerking geretourneerd. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupJobs/read | Hiermee worden alle taak objecten geretourneerd |
> | Actie | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Taken exporteren |
> | Actie | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Retourneert een back-upbewerkings resultaat voor Recovery Services kluis. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupOperations/read | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Een beveiligings beleid verwijderen |
> | Actie | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Resultaten van beleids bewerking ophalen. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Status van beleids bewerking ophalen. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | Actie | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Hiermee wordt een beveiligings beleid gemaakt |
> | Actie | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Hiermee wordt een lijst met alle Beveilig bare items geretourneerd. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Hiermee wordt de lijst met alle beveiligde items geretourneerd. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Hiermee worden alle containers van het abonnement geretourneerd |
> | Actie | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Alle intenties van de back-upbeveiliging weer geven |
> | Actie | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Retourneert informatie over de beveiligings pincode voor Recovery Services kluis. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Hiermee wordt de opslag configuratie voor Recovery Services kluis geretourneerd. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Hiermee wordt de opslag configuratie voor Recovery Services kluis bijgewerkt. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | Actie | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Bewerking voor beveiligd item valideren |
> | Actie | Microsoft.RecoveryServices/Vaults/certificates/write | Met de bewerking resource certificaat bijwerken wordt het resource/kluis-referentie certificaat bijgewerkt. |
> | Actie | Microsoft.RecoveryServices/Vaults/delete | Met de bewerking kluis verwijderen wordt de opgegeven Azure-resource van het type kluis verwijderd |
> | Actie | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Actie | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Actie | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Actie | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | Actie | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Hiermee wordt de waarschuwing opgelost. |
> | Actie | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Hiermee wordt de configuratie van de Recovery Services-kluis melding opgehaald. |
> | Actie | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Hiermee configureert u e-mail meldingen voor de Recovery Services-kluis. |
> | Actie | Microsoft.RecoveryServices/Vaults/read | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Actie | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | De registratie van de container unregister kan worden gebruikt om de registratie van een container op te heffen. |
> | Actie | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Actie | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Actie | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | De bewerking service container registreren kan worden gebruikt om een container te registreren bij de Recovery-service. |
> | Actie | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Waarschuwings instellingen lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Waarschuwings instellingen maken of bijwerken |
> | Actie | Microsoft.RecoveryServices/vaults/replicationEvents/read | Gebeurtenissen lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Hiermee wordt de consistentie van de infra structuur gecontroleerd |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Infrastructuur resources verwijderen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Installatie kopie van de proces server implementeren |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Fabric migreren naar AAD |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/operationresults/lezen | De resultaten van een asynchrone bewerking op de bron-fabrics volgen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/lezen | Alle infra structuren lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Gateway opnieuw koppelen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Infra structuur verwijderen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Certificaat voor Fabric vernieuwen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Alle logische netwerken lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Alle netwerken lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Netwerk toewijzingen verwijderen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Netwerk toewijzingen lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Netwerk toewijzingen maken of bijwerken |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Beveiligbaar item detecteren |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/operationresults/lezen | De resultaten van een asynchrone bewerking op de bron beveiligings containers volgen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Beveiligings containers lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Beveiligings container verwijderen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/verwijderen | Migratie-items verwijderen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Item migreren |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Alle migratie herstel punten lezen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/lezen | De resultaten van een asynchrone bewerking op de bron migratie-items bijhouden |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/lezen | Alle migratie-items lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Migratie testen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Migratie opschonen testen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Migratie-items maken of bijwerken |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/lezen | Beveilig bare items lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Schijven toevoegen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Herstel punt Toep assen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Alle beveiligde items verwijderen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failover door voeren |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/lezen | De resultaten van een asynchrone bewerking volgen op de bron beveiligde items |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Geplande failover |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Alle beveiligde items lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Alle replicatie herstel punten lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Beveiligd item verwijderen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Schijven verwijderen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Replicatie herstellen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Beveiligd item opnieuw beveiligen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Feedback indienen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Alle doel berekenings grootten lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Failover testen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Failovertest opschonen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Failover |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mobility-service bijwerken |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Beveiligde items maken of bijwerken |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Alle beveiligings container toewijzingen verwijderen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/lezen | De resultaten van een asynchrone bewerking op de resource Protection-container toewijzingen bijhouden |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Alle beveiligings container toewijzingen lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Toewijzing van beveiligings container verwijderen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Beveiligings container toewijzingen maken of bijwerken |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Beveiligings container overschakelen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Beveiligings containers maken of bijwerken |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Alle Recovery Services Providers verwijderen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationRecoveryServicesProviders/operationresults/lezen | De resultaten van een asynchrone bewerking op de resource Recovery Services Providers bijhouden |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services Providers lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Provider vernieuwen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Recovery Services provider verwijderen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Recovery Services Providers maken of bijwerken |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/lezen | Alle opslag classificaties lezen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/verwijderen | Alle opslag classificatie toewijzingen verwijderen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/lezen | De resultaten van een asynchrone bewerking op de resource Storage-classificatie toewijzingen bijhouden |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/lezen | Alle opslag classificatie toewijzingen lezen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Opslag classificatie toewijzingen maken of bijwerken |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Alle vCenter verwijderen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationvCenters/operationresults/lezen | De resultaten van een asynchrone bewerking op de resource-vCenter bijhouden |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Alle vCenter lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | VCenter maken of bijwerken |
> | Actie | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Infrastructuur resources maken of bijwerken |
> | Actie | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Taak annuleren |
> | Actie | Micro soft. Recovery Services/kluizen/replicationJobs/operationresults/lezen | De resultaten van een asynchrone bewerking op de resource taken bijhouden |
> | Actie | Microsoft.RecoveryServices/vaults/replicationJobs/read | Alle taken lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Taak opnieuw starten |
> | Actie | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Taak hervatten |
> | Actie | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Alle migratie-items lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Netwerk toewijzingen lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Alle netwerken lezen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationOperationStatus/lezen | De status van de kluis replicatie bewerking lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Beleids regels verwijderen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationPolicies/operationresults/lezen | De resultaten van een asynchrone bewerking op de bron beleidsregels bijhouden |
> | Actie | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Alle beleids regels lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Beleid maken of bijwerken |
> | Actie | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Alle beveiligde items lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Alle beveiligings container toewijzingen lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Beveiligings containers lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Herstel plannen verwijderen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Herstel plan voor failover door voeren |
> | Actie | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/operationresults/lezen | De resultaten van een asynchrone bewerking op de resource recovery-plannen bijhouden |
> | Actie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Herstel plan voor geplande failover |
> | Actie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Herstel plannen lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Herstel plan opnieuw beveiligen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Herstel plan voor failover testen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Herstel plan voor het opschonen van de Failovertest |
> | Actie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Herstel plan voor failover |
> | Actie | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Herstel plannen maken of bijwerken |
> | Actie | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Recovery Services Providers lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Alle opslag classificatie toewijzingen lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Alle opslag classificaties lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Alle items lezen  |
> | Actie | Microsoft.RecoveryServices/vaults/replicationUsages/read | Een kluis replicatie gebruik lezen |
> | Actie | Micro soft. Recovery Services/kluizen/replicationVaultHealth/operationresults/lezen | De resultaten van een asynchrone bewerking op de status van de resource-kluis replicatie bijhouden |
> | Actie | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Status van de kluis replicatie lezen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Status van de kluis vernieuwen |
> | Actie | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Alle items lezen  |
> | Actie | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Maken of bijwerken  |
> | Actie | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Alle vCenter lezen |
> | Actie | Microsoft.RecoveryServices/vaults/usages/read | Een kluis gebruik lezen |
> | Actie | Microsoft.RecoveryServices/Vaults/usages/read | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | Actie | Microsoft.RecoveryServices/Vaults/vaultTokens/read | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | Actie | Microsoft.RecoveryServices/Vaults/write | Met een kluis bewerking maken wordt een Azure-resource van het type ' kluis ' gemaakt. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Relay/checkNameAvailability/action | Controleert de beschik baarheid van de naam ruimte onder het gegeven abonnement. |
> | Actie | Microsoft.Relay/checkNamespaceAvailability/action | Controleert de beschik baarheid van de naam ruimte onder het gegeven abonnement. Deze API is afgeschaft. gebruik in plaats daarvan CheckNameAvailability. |
> | Actie | Micro soft. relay/naam ruimten/authorizationRules/actie | De autorisatie regel voor de naam ruimte wordt bijgewerkt. Deze API is afgeschaft. Gebruik in plaats daarvan een PUT-aanroep om de naam ruimte autorisatie regel bij te werken. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Actie | Micro soft. relay/naam ruimten/authorizationRules/verwijderen | Naam ruimte autorisatie regel verwijderen. De standaard regel voor autorisatie van de naam ruimte kan niet worden verwijderd.  |
> | Actie | Micro soft. relay/naam ruimten/authorizationRules/listkeys ophalen/actie | De verbindings reeks ophalen voor de naam ruimte |
> | Actie | Micro soft. relay/naam ruimten/authorizationRules/lezen | De lijst met beschrijvingen van autorisatie regels voor naam ruimten ophalen. |
> | Actie | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Actie | Micro soft. relay/naam ruimten/authorizationRules/schrijven | Maak autorisatie regels voor het naam ruimte niveau en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels, de primaire en secundaire sleutels kunnen worden bijgewerkt. |
> | Actie | Micro soft. relay/naam ruimten/verwijderen | Naam ruimte resource verwijderen |
> | Actie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hiermee worden de sleutels voor autorisatie regels opgehaald voor de primaire naam ruimte voor nood herstel |
> | Actie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | De autorisatie regels van de primaire naam ruimte voor herstel na nood geval ophalen |
> | Actie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Hiermee schakelt u herstel na nood gevallen uit en stopt u het repliceren van wijzigingen van primaire naar secundaire naam ruimten. |
> | Actie | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Controleert de beschik baarheid van de naam ruimte alias onder het opgegeven abonnement. |
> | Actie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Hiermee verwijdert u de nood herstel configuratie die is gekoppeld aan de naam ruimte. Deze bewerking kan alleen worden aangeroepen via de primaire naam ruimte. |
> | Actie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Hiermee wordt een GEO DR-failover aangeroepen en wordt de naam ruimte alias zo geconfigureerd dat deze naar de secundaire naam ruimte wijst. |
> | Actie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Hiermee haalt u de nood herstel configuratie op die is gekoppeld aan de naam ruimte. |
> | Actie | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Hiermee wordt de nood herstel configuratie die is gekoppeld aan de naam ruimte gemaakt of bijgewerkt. |
> | Actie | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Bewerking voor het bijwerken van HybridConnection. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. Autorisatie regels. Gebruik een aanroep voor het bijwerken van de autorisatie regel. |
> | Actie | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Bewerking voor het verwijderen van HybridConnection-autorisatie regels |
> | Actie | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | De verbindings reeks ophalen voor HybridConnection |
> | Actie | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  De lijst met HybridConnection-autorisatie regels ophalen |
> | Actie | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Actie | Micro soft. relay/naam ruimten/HybridConnections/authorizationRules/schrijven | Maak HybridConnection-autorisatie regels en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels kunnen worden bijgewerkt. |
> | Actie | Micro soft. relay/naam ruimten/HybridConnections/verwijderen | Bewerking voor het verwijderen van de HybridConnection-resource |
> | Actie | Micro soft. relay/naam ruimten/HybridConnections/lezen | Lijst met HybridConnection-resource beschrijvingen ophalen |
> | Actie | Micro soft. relay/naam ruimten/HybridConnections/schrijven | Eigenschappen van HybridConnection maken of bijwerken. |
> | Actie | Microsoft.Relay/namespaces/messagingPlan/read | Hiermee wordt het berichten plan voor een naam ruimte opgehaald.<br>Deze API is afgeschaft.<br>Eigenschappen die via de MessagingPlan-resource beschikbaar worden gemaakt, worden in latere API-versies verplaatst naar de bron naam ruimte (Parent).<br>Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Actie | Micro soft. relay/naam ruimten/messagingPlan/schrijven | Hiermee wordt het bericht abonnement voor een naam ruimte bijgewerkt.<br>Deze API is afgeschaft.<br>Eigenschappen die via de MessagingPlan-resource beschikbaar worden gemaakt, worden in latere API-versies verplaatst naar de bron naam ruimte (Parent).<br>Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Actie | Micro soft. relay/naam ruimten/operationresults/lezen | De status van de naam ruimte bewerking ophalen |
> | Actie | Micro soft. relay/naam ruimten/providers/micro soft. Insights/diagnosticSettings/lezen | Lijst met resource beschrijvingen voor Diagnostische instellingen van naam ruimte ophalen |
> | Actie | Micro soft. relay/naam ruimten/providers/micro soft. Insights/diagnosticSettings/schrijven | Lijst met resource beschrijvingen voor Diagnostische instellingen van naam ruimte ophalen |
> | Actie | Micro soft. relay/naam ruimten/providers/micro soft. Insights/logDefinitions/lezen | Lijst met naam ruimte logboek bron beschrijvingen ophalen |
> | Actie | Micro soft. relay/naam ruimten/providers/micro soft. Insights/metricDefinitions/lezen | Lijst met resource beschrijvingen voor metrische gegevens van de naam ruimte ophalen |
> | Actie | Micro soft. relay/naam ruimten/lezen | De lijst met de resource beschrijving van de naam ruimte ophalen |
> | Actie | Microsoft.Relay/namespaces/removeAcsNamepsace/action | ACS-naam ruimte verwijderen |
> | Actie | Micro soft. relay/naam ruimten/WcfRelays/authorizationRules/actie | Bewerking voor het bijwerken van WcfRelay. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. Autorisatie regels. Gebruik een aanroep voor het bijwerken van de autorisatie regel. |
> | Actie | Micro soft. relay/naam ruimten/WcfRelays/authorizationRules/verwijderen | Bewerking voor het verwijderen van WcfRelay-autorisatie regels |
> | Actie | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | De verbindings reeks ophalen voor WcfRelay |
> | Actie | Micro soft. relay/naam ruimten/WcfRelays/authorizationRules/lezen |  De lijst met WcfRelay-autorisatie regels ophalen |
> | Actie | Micro soft. relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/Action | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Actie | Micro soft. relay/naam ruimten/WcfRelays/authorizationRules/schrijven | Maak WcfRelay-autorisatie regels en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels kunnen worden bijgewerkt. |
> | Actie | Micro soft. relay/naam ruimten/WcfRelays/verwijderen | Bewerking voor het verwijderen van de WcfRelay-resource |
> | Actie | Micro soft. relay/naam ruimten/WcfRelays/lezen | Lijst met WcfRelay-resource beschrijvingen ophalen |
> | Actie | Micro soft. relay/naam ruimten/WcfRelays/schrijven | Eigenschappen van WcfRelay maken of bijwerken. |
> | Actie | Micro soft. relay/naam ruimten/schrijven | Maak een naam ruimte resource en werk de eigenschappen bij. Tags en capaciteit van de naam ruimte zijn de eigenschappen die kunnen worden bijgewerkt. |
> | Actie | Microsoft.Relay/operations/read | Get-bewerkingen |
> | Actie | Microsoft.Relay/register/action | Hiermee wordt het abonnement voor de relay-resource provider geregistreerd en wordt het maken van relay-resources ingeschakeld |
> | Actie | Microsoft.Relay/unregister/action | Hiermee wordt het abonnement voor de relay-resource provider geregistreerd en wordt het maken van relay-resources ingeschakeld |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. ResourceHealth/AvailabilityStatuses/actueel/lezen | Hiermee wordt de beschikbaarheids status opgehaald voor de opgegeven resource |
> | Actie | Microsoft.ResourceHealth/AvailabilityStatuses/read | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Actie | Microsoft.ResourceHealth/events/read | Service Health gebeurtenissen voor een gegeven abonnement ophalen |
> | Actie | Microsoft.Resourcehealth/healthevent/action | Hiermee wordt de wijziging in de status van de opgegeven resource aangeduid |
> | Actie | Microsoft.Resourcehealth/healthevent/Activated/action | Hiermee wordt de wijziging in de status van de opgegeven resource aangeduid |
> | Actie | Microsoft.Resourcehealth/healthevent/InProgress/action | Hiermee wordt de wijziging in de status van de opgegeven resource aangeduid |
> | Actie | Microsoft.Resourcehealth/healthevent/Pending/action | Hiermee wordt de wijziging in de status van de opgegeven resource aangeduid |
> | Actie | Microsoft.Resourcehealth/healthevent/Resolved/action | Hiermee wordt de wijziging in de status van de opgegeven resource aangeduid |
> | Actie | Microsoft.Resourcehealth/healthevent/Updated/action | Hiermee wordt de wijziging in de status van de opgegeven resource aangeduid |
> | Actie | Microsoft.ResourceHealth/impactedResources/read | Betrokken resources voor een gegeven abonnement ophalen |
> | Actie | Microsoft.ResourceHealth/metadata/read | Haalt meta gegevens op |
> | Actie | Micro soft. ResourceHealth/meldingen/lezen | Ontvangt Azure Resource Manager meldingen |
> | Actie | Microsoft.ResourceHealth/Operations/read | De beschik bare bewerkingen voor de micro soft-ResourceHealth ophalen |
> | Actie | Microsoft.ResourceHealth/register/action | Hiermee wordt het abonnement voor de micro soft-ResourceHealth geregistreerd |
> | Actie | Microsoft.ResourceHealth/unregister/action | Hiermee wordt de registratie van het abonnement voor de micro soft-ResourceHealth ongedaan gemaakt |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. resources/calculateTemplateHash/actie | De hash van de gegeven sjabloon berekenen. |
> | Actie | Micro soft. resources/checkPolicyCompliance/lezen | Controleer de nalevings status van een bepaalde bron op basis van bron beleid. |
> | Actie | Microsoft.Resources/checkResourceName/action | Controleer de naam van de resource op geldigheid. |
> | Actie | Microsoft.Resources/deployments/cancel/action | Hiermee wordt een implementatie geannuleerd. |
> | Actie | Microsoft.Resources/deployments/delete | Hiermee verwijdert u een implementatie. |
> | Actie | Micro soft. resources/implementaties/exportTemplate/actie | Sjabloon exporteren voor een implementatie |
> | Actie | Micro soft. resources/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Actie | Micro soft. resources/implementaties/operationstatuses/lezen | Hiermee worden de statussen van de implementatie bewerking opgehaald of weer gegeven. |
> | Actie | Microsoft.Resources/deployments/read | Hiermee wordt een lijst met implementaties opgehaald of weer gegeven. |
> | Actie | Microsoft.Resources/deployments/validate/action | Hiermee wordt een implementatie gevalideerd. |
> | Actie | Microsoft.Resources/deployments/whatIf/action | Voor spelt wijzigingen in de sjabloon implementatie. |
> | Actie | Microsoft.Resources/deployments/write | Hiermee wordt een implementatie gemaakt of bijgewerkt. |
> | Actie | Micro soft. resources/deploymentScripts/verwijderen | Hiermee wordt een implementatie script verwijderd |
> | Actie | Micro soft. resources/deploymentScripts/logboeken/lezen | Hiermee worden de script logboeken van de implementatie opgehaald of weer gegeven |
> | Actie | Micro soft. resources/deploymentScripts/lezen | Hiermee worden de implementatie scripts opgehaald of weer gegeven |
> | Actie | Micro soft. resources/deploymentScripts/schrijven | Hiermee wordt een implementatie script gemaakt of bijgewerkt |
> | Actie | Microsoft.Resources/links/delete | Hiermee verwijdert u een resource koppeling. |
> | Actie | Microsoft.Resources/links/read | Hiermee worden resource koppelingen opgehaald of weer gegeven. |
> | Actie | Microsoft.Resources/links/write | Hiermee wordt een resource koppeling gemaakt of bijgewerkt. |
> | Actie | Microsoft.Resources/marketplace/purchase/action | Een resource aanschaffen via Marketplace. |
> | Actie | Microsoft.Resources/providers/read | De lijst met providers ophalen. |
> | Actie | Microsoft.Resources/resources/read | De lijst met resources ophalen op basis van filters. |
> | Actie | Microsoft.Resources/subscriptions/locations/read | Hiermee haalt u de lijst met ondersteunde locaties op. |
> | Actie | Microsoft.Resources/subscriptions/operationresults/read | De resultaten van de abonnements bewerking ophalen. |
> | Actie | Microsoft.Resources/subscriptions/providers/read | Hiermee worden resource providers opgehaald of weer gegeven. |
> | Actie | Microsoft.Resources/subscriptions/read | Hiermee wordt de lijst met abonnementen opgehaald. |
> | Actie | Microsoft.Resources/subscriptions/resourceGroups/delete | Hiermee verwijdert u een resource groep en alle bijbehorende resources. |
> | Actie | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Actie | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Hiermee worden de statussen van de implementatie bewerking opgehaald of weer gegeven. |
> | Actie | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Hiermee wordt een lijst met implementaties opgehaald of weer gegeven. |
> | Actie | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Hiermee wordt een implementatie gemaakt of bijgewerkt. |
> | Actie | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Hiermee verplaatst u resources van de ene resource groep naar een andere. |
> | Actie | Microsoft.Resources/subscriptions/resourceGroups/read | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Actie | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Hiermee haalt u de resources voor de resource groep op. |
> | Actie | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Verplaatsing van resources van de ene resource groep naar de andere valideren. |
> | Actie | Microsoft.Resources/subscriptions/resourceGroups/write | Hiermee wordt een resource groep gemaakt of bijgewerkt. |
> | Actie | Microsoft.Resources/subscriptions/resources/read | Hiermee haalt u de resources van een abonnement op. |
> | Actie | Microsoft.Resources/subscriptions/tagNames/delete | Hiermee verwijdert u een abonnements label. |
> | Actie | Microsoft.Resources/subscriptions/tagNames/read | Hiermee worden abonnements Tags opgehaald of weer gegeven. |
> | Actie | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Hiermee verwijdert u een abonnements label waarde. |
> | Actie | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Hiermee worden de abonnements label waarden opgehaald of weer gegeven. |
> | Actie | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Hiermee wordt een abonnements label waarde toegevoegd. |
> | Actie | Microsoft.Resources/subscriptions/tagNames/write | Hiermee voegt u een abonnements label toe. |
> | Actie | Microsoft.Resources/tags/delete | Hiermee verwijdert u alle tags van een resource. |
> | Actie | Microsoft.Resources/tags/read | Hiermee worden alle labels op een resource opgehaald. |
> | Actie | Microsoft.Resources/tags/write | Hiermee worden de tags op een resource bijgewerkt door bestaande tags te vervangen of samen te voegen met een nieuwe set tags of door bestaande tags te verwijderen. |
> | Actie | Microsoft.Resources/tenants/read | Hiermee wordt de lijst met tenants opgehaald. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Scheduler/jobcollections/delete | Hiermee verwijdert u de taak verzameling. |
> | Actie | Microsoft.Scheduler/jobcollections/disable/action | Hiermee schakelt u het verzamelen van taken uit. |
> | Actie | Microsoft.Scheduler/jobcollections/enable/action | Hiermee schakelt u de taak verzameling in. |
> | Actie | Microsoft.Scheduler/jobcollections/jobs/delete | Taak verwijderen. |
> | Actie | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Hiermee wordt een definitie van een logische app gegenereerd op basis van een planner taak. |
> | Actie | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Hiermee wordt de taak geschiedenis opgehaald. |
> | Actie | Microsoft.Scheduler/jobcollections/jobs/read | Hiermee wordt een taak opgehaald. |
> | Actie | Microsoft.Scheduler/jobcollections/jobs/run/action | Taak wordt uitgevoerd. |
> | Actie | Micro soft. scheduler/jobcollections/Jobs/schrijven | Hiermee wordt een taak gemaakt of bijgewerkt. |
> | Actie | Micro soft. scheduler/jobcollections/lezen | Taak verzameling ophalen |
> | Actie | Microsoft.Scheduler/jobcollections/write | Hiermee wordt een taak verzameling gemaakt of bijgewerkt. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Search/checkNameAvailability/action | Controleert de beschik baarheid van de service naam. |
> | Actie | Micro soft. Search/Operations/lezen | Een lijst met alle beschik bare bewerkingen van de provider micro soft. Search. |
> | Actie | Microsoft.Search/register/action | Hiermee wordt het abonnement voor de zoek resource provider geregistreerd en wordt het maken van zoek Services ingeschakeld. |
> | Actie | Microsoft.Search/searchServices/createQueryKey/action | Hiermee maakt u de query sleutel. |
> | Actie | Microsoft.Search/searchServices/delete | Hiermee verwijdert u de zoek service. |
> | Actie | Microsoft.Search/searchServices/deleteQueryKey/delete | Hiermee verwijdert u de query sleutel. |
> | Actie | Microsoft.Search/searchServices/listAdminKeys/action | Hiermee worden de beheer sleutels gelezen. |
> | Actie | Micro soft. Search/searchServices/listQueryKeys/Action | Retourneert de lijst met query-API-sleutels voor de gegeven Azure Search service. |
> | Actie | Microsoft.Search/searchServices/listQueryKeys/read | Retourneert de lijst met query-API-sleutels voor de gegeven Azure Search service. |
> | Actie | Micro soft. Search/searchServices/privateEndpointConnectionProxies/verwijderen | Hiermee verwijdert u een bestaande verbindings proxy voor een persoonlijk eind punt |
> | Actie | Micro soft. Search/searchServices/privateEndpointConnectionProxies/lezen | Hiermee wordt de lijst met Connection-proxy's voor het particuliere eind punt geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbindings proxy van het particuliere eind punt |
> | Actie | Micro soft. Search/searchServices/privateEndpointConnectionProxies/validate/Action | Hiermee wordt een aanroep voor het maken van een privé-eind punt gevalideerd aan de kant van de NRP |
> | Actie | Micro soft. Search/searchServices/privateEndpointConnectionProxies/write | Hiermee maakt u een particuliere endpoint-verbindings proxy met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven verbinding proxy voor het particuliere eind punt bij |
> | Actie | Microsoft.Search/searchServices/read | Hiermee wordt de zoek service gelezen. |
> | Actie | Microsoft.Search/searchServices/regenerateAdminKey/action | Hiermee wordt de beheerders sleutel opnieuw gegenereerd. |
> | Actie | Microsoft.Search/searchServices/start/action | Hiermee wordt de zoek service gestart. |
> | Actie | Microsoft.Search/searchServices/stop/action | Hiermee stopt u de zoek service. |
> | Actie | Microsoft.Search/searchServices/write | Hiermee wordt de zoek service gemaakt of bijgewerkt. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Hiermee worden de opgegeven regels voor het beveiligen van verkeer afgedwongen door het maken van overeenkomende beveiligings regels voor de opgegeven netwerk beveiligings groep (en) |
> | Actie | Microsoft.Security/adaptiveNetworkHardenings/read | Hiermee worden aanbevelingen voor adaptieve netwerk beveiliging van een met Azure beveiligde resource opgehaald |
> | Actie | Microsoft.Security/advancedThreatProtectionSettings/read | Hiermee worden de geavanceerde instellingen voor bedreigings beveiliging voor de resource opgehaald |
> | Actie | Microsoft.Security/advancedThreatProtectionSettings/write | Hiermee worden de geavanceerde instellingen voor bedreigings beveiliging voor de resource bijgewerkt |
> | Actie | Microsoft.Security/alerts/read | Hiermee worden alle beschik bare beveiligings waarschuwingen opgehaald |
> | Actie | Microsoft.Security/applicationWhitelistings/read | Hiermee wordt de toepassing whitelistings |
> | Actie | Microsoft.Security/applicationWhitelistings/write | Hiermee maakt u een nieuwe white list of werkt u een bestaande toepassing bij. |
> | Actie | Micro soft. Security/assessmentMetadata/lezen | Beschik bare meta gegevens van beveiligings beoordeling voor uw abonnement ophalen |
> | Actie | Micro soft. Security/assessmentMetadata/schrijven | Meta gegevens van een beveiligings beoordeling maken of bijwerken |
> | Actie | Micro soft. Security/beoordelingen/lezen | Beveiligings beoordelingen ontvangen voor uw abonnement |
> | Actie | Micro soft. Security/beoordelingen/schrijven | Beveiligings beoordelingen maken of bijwerken voor uw abonnement |
> | Actie | Micro soft. Security/complianceResults/lezen | Hiermee worden de compliantie resultaten voor de resource opgehaald |
> | Actie | Microsoft.Security/informationProtectionPolicies/read | Hiermee wordt het Information Protection-beleid voor de resource opgehaald |
> | Actie | Microsoft.Security/informationProtectionPolicies/write | Hiermee wordt het Information Protection-beleid voor de resource bijgewerkt |
> | Actie | Microsoft.Security/locations/alerts/activate/action | Een beveiligings waarschuwing activeren |
> | Actie | Microsoft.Security/locations/alerts/dismiss/action | Een beveiligings waarschuwing negeren |
> | Actie | Microsoft.Security/locations/alerts/read | Hiermee worden alle beschik bare beveiligings waarschuwingen opgehaald |
> | Actie | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Hiermee wordt het just-in-time-netwerk toegangs beleid verwijderd |
> | Actie | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Initieert een just-in-time-aanvraag voor netwerk toegangs beleid |
> | Actie | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Hiermee wordt het just-in-time-netwerk toegangs beleid opgehaald |
> | Actie | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Hiermee maakt u een nieuw just-in-time-netwerk toegangsbeleid of wordt een bestaand beleid bijgewerkt |
> | Actie | Microsoft.Security/locations/read | Hiermee wordt de locatie van de beveiligings gegevens opgehaald |
> | Actie | Microsoft.Security/locations/tasks/activate/action | Een beveiligings aanbeveling activeren |
> | Actie | Microsoft.Security/locations/tasks/dismiss/action | Een beveiligings aanbeveling negeren |
> | Actie | Microsoft.Security/locations/tasks/read | Hiermee worden alle beschik bare beveiligings aanbevelingen opgehaald |
> | Actie | Microsoft.Security/locations/tasks/resolve/action | Een beveiligings aanbeveling oplossen |
> | Actie | Microsoft.Security/locations/tasks/start/action | Een beveiligings aanbeveling starten |
> | Actie | Microsoft.Security/policies/read | Hiermee wordt het beveiligings beleid opgehaald |
> | Actie | Microsoft.Security/policies/write | Hiermee wordt het beveiligings beleid bijgewerkt |
> | Actie | Micro soft. Security/prijzen/verwijderen | Hiermee worden de prijs instellingen voor de scope verwijderd |
> | Actie | Microsoft.Security/pricings/read | Hiermee worden de prijs instellingen voor de scope opgehaald |
> | Actie | Micro soft. Security/prijzen/schrijven | Hiermee worden de prijs instellingen voor de scope bijgewerkt |
> | Actie | Microsoft.Security/register/action | Hiermee wordt het abonnement voor Azure Security Center geregistreerd |
> | Actie | Micro soft. Security/securityContacts/verwijderen | Hiermee wordt de contact persoon beveiliging verwijderd |
> | Actie | Microsoft.Security/securityContacts/read | Hiermee wordt de Security-contact persoon opgehaald |
> | Actie | Microsoft.Security/securityContacts/write | Hiermee wordt de beveiligings contactpersoon bijgewerkt |
> | Actie | Microsoft.Security/securitySolutions/delete | Hiermee verwijdert u een beveiligings oplossing |
> | Actie | Microsoft.Security/securitySolutions/read | Hiermee worden de beveiligings oplossingen opgehaald |
> | Actie | Microsoft.Security/securitySolutions/write | Hiermee wordt een nieuwe beveiligings oplossing gemaakt of een bestaande bijgewerkt |
> | Actie | Microsoft.Security/securitySolutionsReferenceData/read | Hiermee worden de referentie gegevens van beveiligings oplossingen opgehaald |
> | Actie | Micro soft. Security/securityStatuses/lezen | Hiermee worden de beveiligings statussen voor Azure-resources opgehaald |
> | Actie | Micro soft. Security/securityStatusesSummaries/lezen | Hiermee worden de samen vattingen van de beveiligings status van het bereik opgehaald |
> | Actie | Microsoft.Security/settings/read | Hiermee worden de instellingen voor het bereik opgehaald |
> | Actie | Microsoft.Security/settings/write | Hiermee worden de instellingen voor de scope bijgewerkt |
> | Actie | Microsoft.Security/tasks/read | Hiermee worden alle beschik bare beveiligings aanbevelingen opgehaald |
> | Actie | Micro soft. beveiliging/opheffen/actie | Hiermee wordt de registratie van het abonnement bij Azure Security Center ongedaan gemaakt |
> | Actie | Micro soft. Security/webApplicationFirewalls/verwijderen | Hiermee verwijdert u een Web Application Firewall |
> | Actie | Micro soft. Security/webApplicationFirewalls/lezen | Hiermee worden de firewalls voor webtoepassingen opgehaald |
> | Actie | Micro soft. Security/webApplicationFirewalls/schrijven | Hiermee wordt een nieuwe Web Application Firewall gemaakt of een bestaande bijgewerkt |
> | Actie | Microsoft.Security/workspaceSettings/connect/action | Instellingen voor opnieuw verbinden van werk ruimte-instellingen wijzigen |
> | Actie | Microsoft.Security/workspaceSettings/delete | Hiermee verwijdert u de werk ruimte-instellingen |
> | Actie | Microsoft.Security/workspaceSettings/read | Hiermee worden de werkruimte instellingen opgehaald |
> | Actie | Microsoft.Security/workspaceSettings/write | Hiermee werkt u de werk ruimte-instellingen bij |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.SecurityGraph/diagnosticsettings/delete | Een diagnostische instelling verwijderen |
> | Actie | Micro soft. SecurityGraph/diagnosticsettings/lezen | Een diagnostische instelling lezen |
> | Actie | Micro soft. SecurityGraph/diagnosticsettings/schrijven | Een diagnostische instelling schrijven |
> | Actie | Micro soft. SecurityGraph/diagnosticsettingscategories/lezen | Categorieën voor Diagnostische instellingen lezen |

## <a name="microsoftsecurityinsights"></a>Micro soft. SecurityInsights

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. SecurityInsights/aggregaties/lezen | Hiermee worden geaggregeerde gegevens opgehaald |
> | Actie | Micro soft. SecurityInsights/alertRules/acties/verwijderen | Hiermee verwijdert u de reactie acties van een waarschuwings regel |
> | Actie | Micro soft. SecurityInsights/alertRules/acties/lezen | Hiermee worden de reactie acties van een waarschuwings regel opgehaald |
> | Actie | Micro soft. SecurityInsights/alertRules/acties/schrijven | Hiermee worden de reactie acties van een waarschuwings regel bijgewerkt |
> | Actie | Micro soft. SecurityInsights/alertRules/verwijderen | Hiermee verwijdert u waarschuwings regels |
> | Actie | Micro soft. SecurityInsights/alertRules/lezen | Hiermee worden de waarschuwings regels opgehaald |
> | Actie | Micro soft. SecurityInsights/alertRules/schrijven | Waarschuwings regels voor updates |
> | Actie | Micro soft. SecurityInsights/blad wijzers/verwijderen | Blad wijzers verwijderen |
> | Actie | Micro soft. SecurityInsights/blad wijzers/uitvouwen/actie | Hiermee worden de gerelateerde entiteiten van een entiteit door een specifieke uitbrei ding opgehaald |
> | Actie | Micro soft. SecurityInsights/blad wijzers/lezen | Hiermee worden blad wijzers opgehaald |
> | Actie | Micro soft. SecurityInsights/blad wijzers/schrijven | Blad wijzers bijwerken |
> | Actie | Micro soft. SecurityInsights/cases/opmerkingen/lezen | Hiermee worden de case-opmerkingen opgehaald |
> | Actie | Micro soft. SecurityInsights/cases/comments/schrijven | Hiermee worden de case opmerkingen gemaakt |
> | Actie | Micro soft. SecurityInsights/cases/verwijderen | Hiermee wordt een aanvraag verwijderd |
> | Actie | Micro soft. SecurityInsights/cases/onderzoeken/lezen | Hiermee worden de case onderzoeken opgehaald |
> | Actie | Micro soft. SecurityInsights/cases/onderzoeken/schrijven | Hiermee worden de meta gegevens van een case bijgewerkt |
> | Actie | Micro soft. SecurityInsights/cases/lezen | Hiermee wordt een case opgehaald |
> | Actie | Micro soft. SecurityInsights/cases/schrijven | Hiermee wordt een case bijgewerkt |
> | Actie | Micro soft. SecurityInsights/dataConnectors/verwijderen | Hiermee verwijdert u een gegevens connector |
> | Actie | Micro soft. SecurityInsights/dataConnectors/lezen | Hiermee worden de gegevens connectors opgehaald |
> | Actie | Micro soft. SecurityInsights/dataConnectors/schrijven | Hiermee wordt een gegevens connector bijgewerkt |
> | Actie | Micro soft. SecurityInsights/REGI ster/actie | Hiermee wordt het abonnement geregistreerd bij de Azure-Sentinel |
> | Actie | Micro soft. SecurityInsights/Settings/lezen | Hiermee worden instellingen opgehaald |
> | Actie | Micro soft. SecurityInsights/Settings/write | Instellingen voor updates |
> | Actie | Micro soft. SecurityInsights/registratie/actie | Hiermee wordt de registratie van het abonnement bij Azure-Sentinel ongedaan gemaakt |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.ServiceBus/checkNameAvailability/action | Controleert de beschik baarheid van de naam ruimte onder het gegeven abonnement. |
> | Actie | Microsoft.ServiceBus/checkNamespaceAvailability/action | Controleert de beschik baarheid van de naam ruimte onder het gegeven abonnement. Deze API is afgeschaft. gebruik in plaats daarvan CheckNameAvailability. |
> | Actie | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Hiermee worden de VNet-regels in de ServiceBus-resource provider voor het opgegeven VNet verwijderd |
> | Actie | Microsoft.ServiceBus/namespaces/authorizationRules/action | De autorisatie regel voor de naam ruimte wordt bijgewerkt. Deze API is afgeschaft. Gebruik in plaats daarvan een PUT-aanroep om de naam ruimte autorisatie regel bij te werken. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Actie | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Naam ruimte autorisatie regel verwijderen. De standaard regel voor autorisatie van de naam ruimte kan niet worden verwijderd.  |
> | Actie | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | De verbindings reeks ophalen voor de naam ruimte |
> | Actie | Microsoft.ServiceBus/namespaces/authorizationRules/read | De lijst met beschrijvingen van autorisatie regels voor naam ruimten ophalen. |
> | Actie | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Actie | Microsoft.ServiceBus/namespaces/authorizationRules/write | Maak autorisatie regels voor het naam ruimte niveau en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels, de primaire en secundaire sleutels kunnen worden bijgewerkt. |
> | Actie | Microsoft.ServiceBus/namespaces/Delete | Naam ruimte resource verwijderen |
> | Actie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hiermee worden de sleutels voor autorisatie regels opgehaald voor de primaire naam ruimte voor nood herstel |
> | Actie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | De autorisatie regels van de primaire naam ruimte voor herstel na nood geval ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Hiermee schakelt u herstel na nood gevallen uit en stopt u het repliceren van wijzigingen van primaire naar secundaire naam ruimten. |
> | Actie | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Controleert de beschik baarheid van de naam ruimte alias onder het opgegeven abonnement. |
> | Actie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Hiermee verwijdert u de nood herstel configuratie die is gekoppeld aan de naam ruimte. Deze bewerking kan alleen worden aangeroepen via de primaire naam ruimte. |
> | Actie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Hiermee wordt een GEO DR-failover aangeroepen en wordt de naam ruimte alias zo geconfigureerd dat deze naar de secundaire naam ruimte wijst. |
> | Actie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Hiermee haalt u de nood herstel configuratie op die is gekoppeld aan de naam ruimte. |
> | Actie | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Hiermee wordt de nood herstel configuratie die is gekoppeld aan de naam ruimte gemaakt of bijgewerkt. |
> | Actie | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Hiermee verwijdert u het Event Grid filter dat aan de naam ruimte is gekoppeld. |
> | Actie | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Hiermee wordt het Event Grid filter opgehaald dat aan de naam ruimte is gekoppeld. |
> | Actie | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Hiermee wordt het Event Grid filter dat aan de naam ruimte is gekoppeld, gemaakt of bijgewerkt. |
> | Actie | Microsoft.ServiceBus/namespaces/eventhubs/read | Lijst met EventHub-resource beschrijvingen ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | IP-filter bron verwijderen |
> | Actie | Microsoft.ServiceBus/namespaces/ipFilterRules/read | IP-filter bron ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/ipFilterRules/write | IP-filter bron maken |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Berichten ontvangen |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Berichten verzenden |
> | Actie | Microsoft.ServiceBus/namespaces/messagingPlan/read | Hiermee wordt het berichten plan voor een naam ruimte opgehaald.<br>Deze API is afgeschaft.<br>Eigenschappen die via de MessagingPlan-resource beschikbaar worden gemaakt, worden in latere API-versies verplaatst naar de bron naam ruimte (Parent).<br>Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Actie | Microsoft.ServiceBus/namespaces/messagingPlan/write | Hiermee wordt het bericht abonnement voor een naam ruimte bijgewerkt.<br>Deze API is afgeschaft.<br>Eigenschappen die via de MessagingPlan-resource beschikbaar worden gemaakt, worden in latere API-versies verplaatst naar de bron naam ruimte (Parent).<br>Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Actie | Microsoft.ServiceBus/namespaces/migrate/action | Naam ruimte bewerking migreren |
> | Actie | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Hiermee verwijdert u de migratie configuratie. |
> | Actie | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Hiermee wordt de migratie configuratie opgehaald, waarmee de status van de migratie en de replicatie bewerkingen in behandeling worden aangegeven |
> | Actie | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Hiermee wordt de standaard naar Premium-naam ruimte migratie hersteld |
> | Actie | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Wijst de DNS-server die is gekoppeld aan de standaard naam ruimte toe aan de Premium-naam ruimte waarmee de migratie wordt voltooid en stopt de synchronisatie van resources van Standard-naar-Premium-naam ruimte |
> | Actie | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Hiermee wordt de migratie configuratie gemaakt of bijgewerkt. Hiermee wordt begonnen met het synchroniseren van resources van de standaard naar de Premium-naam ruimte |
> | Actie | Micro soft. ServiceBus/naam ruimten/networkruleset/verwijderen | VNET-regel resource verwijderen |
> | Actie | Micro soft. ServiceBus/naam ruimten/networkruleset/lezen | Hiermee wordt de NetworkRuleSet-resource opgehaald |
> | Actie | Micro soft. ServiceBus/naam ruimten/networkruleset/schrijven | VNET-regel resource maken |
> | Actie | Microsoft.ServiceBus/namespaces/networkrulesets/delete | VNET-regel resource verwijderen |
> | Actie | Microsoft.ServiceBus/namespaces/networkrulesets/read | Hiermee wordt de NetworkRuleSet-resource opgehaald |
> | Actie | Microsoft.ServiceBus/namespaces/networkrulesets/write | VNET-regel resource maken |
> | Actie | Microsoft.ServiceBus/namespaces/operationresults/read | De status van de naam ruimte bewerking ophalen |
> | Actie | Micro soft. ServiceBus/naam ruimten/providers/micro soft. Insights/diagnosticSettings/lezen | Lijst met resource beschrijvingen voor Diagnostische instellingen van naam ruimte ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Lijst met resource beschrijvingen voor Diagnostische instellingen van naam ruimte ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Lijst met naam ruimte logboek bron beschrijvingen ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Lijst met resource beschrijvingen voor metrische gegevens van de naam ruimte ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Bewerking voor het bijwerken van de wachtrij. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. Autorisatie regels. Gebruik een aanroep voor het bijwerken van de autorisatie regel. |
> | Actie | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Bewerking voor het verwijderen van autorisatie regels voor de wachtrij |
> | Actie | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | De verbindings reeks naar de wachtrij ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  De lijst met autorisatie regels voor de wachtrij ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Actie | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Maak autorisatie regels voor de wachtrij en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels kunnen worden bijgewerkt. |
> | Actie | Microsoft.ServiceBus/namespaces/queues/Delete | Bewerking voor het verwijderen van de wachtrij resource |
> | Actie | Microsoft.ServiceBus/namespaces/queues/read | Lijst met bron beschrijvingen van de wachtrij ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/queues/write | Wachtrij-eigenschappen maken of bijwerken. |
> | Actie | Microsoft.ServiceBus/namespaces/read | De lijst met de resource beschrijving van de naam ruimte ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | ACS-naam ruimte verwijderen |
> | Actie | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Bewerking voor het bijwerken van het onderwerp. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. Autorisatie regels. Gebruik een aanroep voor het bijwerken van de autorisatie regel. |
> | Actie | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Bewerking voor het verwijderen van de autorisatie regels voor het onderwerp |
> | Actie | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | De verbindings reeks naar het onderwerp ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  De lijst met autorisatie regels voor het onderwerp ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Actie | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Maak autorisatie regels voor het onderwerp en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels kunnen worden bijgewerkt. |
> | Actie | Microsoft.ServiceBus/namespaces/topics/Delete | Bewerking voor het verwijderen van de Onderwerps resource |
> | Actie | Microsoft.ServiceBus/namespaces/topics/read | Lijst met beschrijvingen van Onderwerps bronnen ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Bewerking voor het verwijderen van de TopicSubscription-resource |
> | Actie | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Lijst met TopicSubscription-resource beschrijvingen ophalen |
> | Actie | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Bewerking voor het verwijderen van de regel Resource |
> | Actie | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Lijst met beschrijvingen van regel resources ophalen |
> | Actie | Micro soft. ServiceBus/naam ruimten/onderwerpen/abonnementen/regels/schrijven | Regel eigenschappen maken of bijwerken. |
> | Actie | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Eigenschappen van TopicSubscription maken of bijwerken. |
> | Actie | Microsoft.ServiceBus/namespaces/topics/write | Eigenschappen van een onderwerp maken of bijwerken. |
> | Actie | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | VNET-regel resource verwijderen |
> | Actie | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Hiermee wordt VNET-regel Resource opgehaald |
> | Actie | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | VNET-regel resource maken |
> | Actie | Microsoft.ServiceBus/namespaces/write | Maak een naam ruimte resource en werk de eigenschappen bij. Tags en capaciteit van de naam ruimte zijn de eigenschappen die kunnen worden bijgewerkt. |
> | Actie | Microsoft.ServiceBus/operations/read | Get-bewerkingen |
> | Actie | Microsoft.ServiceBus/register/action | Hiermee wordt het abonnement voor de ServiceBus-resource provider geregistreerd en wordt het maken van ServiceBus-resources mogelijk |
> | Actie | Microsoft.ServiceBus/sku/read | Lijst met resource beschrijvingen van SKU ophalen |
> | Actie | Microsoft.ServiceBus/sku/regions/read | Lijst met SkuRegions-resource beschrijvingen ophalen |
> | Actie | Microsoft.ServiceBus/unregister/action | Hiermee wordt het abonnement voor de ServiceBus-resource provider geregistreerd en wordt het maken van ServiceBus-resources mogelijk |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. ServiceFabric/clusters/toepassingen/verwijderen | Alle toepassingen verwijderen |
> | Actie | Micro soft. ServiceFabric/clusters/toepassingen/lezen | Alle toepassingen lezen |
> | Actie | Micro soft. ServiceFabric/clusters/toepassingen/services/verwijderen | Alle services verwijderen |
> | Actie | Micro soft. ServiceFabric/clusters/toepassingen/services/partities/lezen | Een wille keurige partitie lezen |
> | Actie | Micro soft. ServiceFabric/clusters/toepassingen/services/partities/replica's/lezen | Een replica lezen |
> | Actie | Micro soft. ServiceFabric/clusters/toepassingen/services/lezen | Alle services lezen |
> | Actie | Micro soft. ServiceFabric/clusters/toepassingen/services/statussen/lezen | Een service status lezen |
> | Actie | Micro soft. ServiceFabric/clusters/toepassingen/services/schrijven | Een service maken of bijwerken |
> | Actie | Micro soft. ServiceFabric/clusters/toepassingen/schrijven | Een toepassing maken of bijwerken |
> | Actie | Micro soft. ServiceFabric/clusters/applicationTypes/verwijderen | Een toepassings type verwijderen |
> | Actie | Micro soft. ServiceFabric/clusters/applicationTypes/lezen | Een toepassings type lezen |
> | Actie | Micro soft. ServiceFabric/clusters/applicationTypes/versies/verwijderen | Een versie van een toepassings type verwijderen |
> | Actie | Micro soft. ServiceFabric/clusters/applicationTypes/versies/lezen | Een versie van een toepassings type lezen |
> | Actie | Micro soft. ServiceFabric/clusters/applicationTypes/versies/schrijven | Een versie van een toepassings type maken of bijwerken |
> | Actie | Micro soft. ServiceFabric/clusters/applicationTypes/schrijven | Een toepassings type maken of bijwerken |
> | Actie | Micro soft. ServiceFabric/clusters/verwijderen | Een cluster verwijderen |
> | Actie | Micro soft. ServiceFabric/clusters/knoop punten/lezen | Een wille keurig knoop punt lezen |
> | Actie | Micro soft. ServiceFabric/clusters/lezen | Een cluster lezen |
> | Actie | Micro soft. ServiceFabric/clusters/statussen/lezen | Alle cluster status lezen |
> | Actie | Micro soft. ServiceFabric/clusters/schrijven | Een cluster maken of bijwerken |
> | Actie | Microsoft.ServiceFabric/locations/clusterVersions/read | Een cluster versie lezen |
> | Actie | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Een cluster versie voor een specifieke omgeving lezen |
> | Actie | Micro soft. ServiceFabric/locaties/operationresults/lezen | Alle bewerkings resultaten lezen |
> | Actie | Micro soft. ServiceFabric/locaties/bewerkingen/lezen | Alle bewerkingen op locatie lezen |
> | Actie | Micro soft. ServiceFabric/Operations/lezen | Alle beschik bare bewerkingen lezen |
> | Actie | Micro soft. ServiceFabric/REGI ster/actie | Elke actie registreren |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.SignalRService/locations/checknameavailability/action | Hiermee wordt gecontroleerd of een naam beschikbaar is voor gebruik met een nieuwe signalerings service |
> | Actie | Micro soft. SignalRService/locations/operationresults/Signaler/Read | De status van een asynchrone bewerking opvragen |
> | Actie | Microsoft.SignalRService/locations/operationStatuses/operationId/read | De status van een asynchrone bewerking opvragen |
> | Actie | Microsoft.SignalRService/locations/usages/read | Het quotum gebruik voor de Azure signalerings service ophalen |
> | Actie | Micro soft. SignalRService/operationresults/lezen | De status van een asynchrone bewerking opvragen |
> | Actie | Micro soft. SignalRService/Operations/lezen | De bewerkingen voor de Azure signalerings service weer geven. |
> | Actie | Micro soft. SignalRService/operationstatus/lezen | De status van een asynchrone bewerking opvragen |
> | Actie | Microsoft.SignalRService/register/action | Hiermee wordt de resource provider micro soft. SignalRService geregistreerd bij een abonnement |
> | Actie | Micro soft. SignalRService/signalering/verwijderen | De volledige signalerings service verwijderen |
> | Actie | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Een gebeurtenis raster filter verwijderen van een Signaaler. |
> | Actie | Microsoft.SignalRService/SignalR/eventGridFilters/read | Hiermee worden de eigenschappen van het opgegeven gebeurtenis raster filter opgehaald of worden alle gebeurtenis raster filters voor de opgegeven Signa lering weer gegeven. |
> | Actie | Microsoft.SignalRService/SignalR/eventGridFilters/write | Een gebeurtenis raster filter voor een signaal sterkte met de opgegeven para meters maken of bijwerken. |
> | Actie | Microsoft.SignalRService/SignalR/listkeys/action | De waarde van de toegangs sleutels voor de Signa lering in de beheer portal of via API weer geven |
> | Actie | Micro soft. SignalRService/seingevings/privateEndpointConnectionProxies/verwijderen | Een particuliere endpoint-verbindings proxy verwijderen |
> | Actie | Micro soft. SignalRService/seingevings/privateEndpointConnectionProxies/lezen | Een Connetion proxy voor een persoonlijk eind punt lezen |
> | Actie | Micro soft. SignalRService/seingevings/privateEndpointConnectionProxies/validate/Action | Een particuliere endpoint-verbindings proxy valideren |
> | Actie | Micro soft. SignalRService/seingevings/privateEndpointConnectionProxies/schrijven | Een particuliere endpoint-verbindings proxy maken |
> | Actie | Micro soft. SignalRService/seingevings/privateEndpointConnections/lezen | Een verbinding met een privé-eind punt lezen |
> | Actie | Micro soft. SignalRService/seingevings/privateEndpointConnections/schrijven | Een persoonlijke eindpunt verbinding goed keuren of afwijzen |
> | Actie | Micro soft. SignalRService/seingevings/privateLinkResources/lezen | Alle persoonlijke koppelings resources van de seingevings lijst weer geven |
> | Actie | Microsoft.SignalRService/SignalR/read | De instellingen en configuraties van de signaal sterkte weer geven in de beheer portal of via API |
> | Actie | Microsoft.SignalRService/SignalR/regeneratekey/action | Wijzig de waarde van de toegangs sleutels voor de Signa lering in de beheer portal of via API |
> | Actie | Microsoft.SignalRService/SignalR/restart/action | Een Azure signalerings service opnieuw starten in de beheer portal of via de API. Er is een bepaalde uitval tijd. |
> | Actie | Micro soft. SignalRService/seingevings/schrijven | Wijzig de instellingen en configuraties van de signaal sterkte in de beheer portal of via API |
> | Actie | Microsoft.SignalRService/unregister/action | Hiermee wordt de registratie van de resource provider micro soft. SignalRService met een abonnement ongedaan gemaakt |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. Solutions/applicationDefinitions/applicationArtifacts/lezen | Een lijst met toepassings artefacten van de toepassings definitie. |
> | Actie | Microsoft.Solutions/applicationDefinitions/delete | Hiermee verwijdert u een toepassings definitie. |
> | Actie | Microsoft.Solutions/applicationDefinitions/read | Hiermee wordt een lijst met toepassings definities opgehaald. |
> | Actie | Microsoft.Solutions/applicationDefinitions/write | Een toepassings definitie toevoegen of wijzigen. |
> | Actie | Microsoft.Solutions/applications/applicationArtifacts/read | Een lijst met toepassings artefacten. |
> | Actie | Microsoft.Solutions/applications/delete | Hiermee verwijdert u een toepassing. |
> | Actie | Microsoft.Solutions/applications/read | Hiermee haalt u een lijst met toepassingen op. |
> | Actie | Microsoft.Solutions/applications/refreshPermissions/action | Hiermee vernieuwt u de toepassings machtiging (en). |
> | Actie | Microsoft.Solutions/applications/updateAccess/action | Hiermee werkt u de toegang tot toepassingen bij. |
> | Actie | Microsoft.Solutions/applications/write | Hiermee wordt een toepassing gemaakt. |
> | Actie | Microsoft.Solutions/jitRequests/delete | Een JitRequest verwijderen |
> | Actie | Microsoft.Solutions/jitRequests/read | Hiermee wordt een lijst met JitRequests opgehaald |
> | Actie | Micro soft. Solutions/jitRequests/write | Hiermee maakt u een JitRequest |
> | Actie | Microsoft.Solutions/locations/operationStatuses/read | Hiermee wordt de bewerkings status van de resource gelezen. |
> | Actie | Micro soft. Solutions/Operations/lezen | Hiermee wordt de lijst met bewerkingen opgehaald. |
> | Actie | Micro soft. Solutions/REGI ster/actie | Registreren bij oplossingen. |
> | Actie | Micro soft. Solutions/registratie/actie | Hiermee wordt de registratie van oplossingen ongedaan gemaakt. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Sql/checkNameAvailability/action | Controleer of de opgegeven server naam beschikbaar is voor het wereld wijd inrichten van een bepaald abonnement. |
> | Actie | Microsoft.Sql/instancePools/delete | Hiermee wordt een exemplaar groep verwijderd |
> | Actie | Microsoft.Sql/instancePools/read | Hiermee wordt een exemplaar groep opgehaald |
> | Actie | Microsoft.Sql/instancePools/usages/read | Hiermee worden de gebruiks gegevens van een exemplaar groep opgehaald |
> | Actie | Microsoft.Sql/instancePools/write | Hiermee wordt een exemplaar groep gemaakt of bijgewerkt |
> | Actie | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Resultaat van de set-bewerking voor het controle beleid voor de uitgebreide server-BLOB ophalen |
> | Actie | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Resultaat ophalen van de set-bewerking voor het controle beleid voor Server-blobs |
> | Actie | Micro soft. SQL/locaties/mogelijkheden/lezen | Hiermee worden de mogelijkheden voor dit abonnement op een bepaalde locatie opgehaald |
> | Actie | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Hiermee wordt de status van een database bewerking opgehaald. |
> | Actie | Microsoft.Sql/locations/databaseOperationResults/read | Hiermee wordt de status van een database bewerking opgehaald. |
> | Actie | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Hiermee worden bewerkingen uitgevoerd op de verwijderde server |
> | Actie | Microsoft.Sql/locations/deletedServerOperationResults/read | Hiermee worden bewerkingen uitgevoerd op de verwijderde server |
> | Actie | Microsoft.Sql/locations/deletedServers/read | De lijst met verwijderde servers retour neren of de eigenschappen voor de opgegeven verwijderde server ophalen. |
> | Actie | Microsoft.Sql/locations/deletedServers/recover/action | Een verwijderde server herstellen |
> | Actie | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Hiermee wordt de Azure async-bewerking voor een async-bewerking van een elastische groep opgehaald |
> | Actie | Microsoft.Sql/locations/elasticPoolOperationResults/read | Hiermee wordt het resultaat van een elastische groeps bewerking opgehaald. |
> | Actie | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Hiermee worden bewerkingen uitgevoerd op de transparante versleutelings beveiliging voor gegevens versleuteling |
> | Actie | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Hiermee worden bewerkingen uitgevoerd op de transparante versleutelings beveiliging voor gegevens versleuteling |
> | Actie | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Resultaat van de set-bewerking voor het controle beleid voor de uitgebreide server-BLOB ophalen |
> | Actie | Micro soft. SQL/locaties/extendedAuditingSettingsOperationResults/lezen | Resultaat van de set-bewerking voor het controle beleid voor de uitgebreide server-BLOB ophalen |
> | Actie | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Hiermee wordt de status van een firewall regel bewerking opgehaald. |
> | Actie | Micro soft. SQL/locaties/firewallRulesOperationResults/lezen | Hiermee wordt de status van een firewall regel bewerking opgehaald. |
> | Actie | Microsoft.Sql/locations/instanceFailoverGroups/delete | Hiermee verwijdert u een bestaande failovergroep voor een exemplaar. |
> | Actie | Micro soft. SQL/locaties/instanceFailoverGroups/failover/actie | Voert een geplande failover uit in een bestaande failovergroep voor een exemplaar. |
> | Actie | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Hiermee wordt geforceerde failover uitgevoerd in een bestaande failovergroep voor een exemplaar. |
> | Actie | Micro soft. SQL/locaties/instanceFailoverGroups/lezen | Hiermee wordt de lijst met failover-groepen voor instanties geretourneerd of worden de eigenschappen opgehaald voor de opgegeven failovergroep voor het exemplaar. |
> | Actie | Microsoft.Sql/locations/instanceFailoverGroups/write | Hiermee maakt u een failovergroep voor een exemplaar met de opgegeven para meters of werkt u de eigenschappen of labels bij voor de opgegeven failovergroep voor het exemplaar. |
> | Actie | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Hiermee wordt de status van een exemplaar groep-bewerking opgehaald |
> | Actie | Microsoft.Sql/locations/instancePoolOperationResults/read | Hiermee wordt het resultaat voor een bewerking van een exemplaar groep opgehaald |
> | Actie | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Hiermee worden de details van een specifiek interface-eind punt Azure async-bewerking geretourneerd |
> | Actie | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Retourneert de details van de opgegeven bewerking voor het eindpunt Profiel van de interface |
> | Actie | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Hiermee wordt de status van een taak agent bewerking opgehaald. |
> | Actie | Microsoft.Sql/locations/jobAgentOperationResults/read | Hiermee wordt het resultaat van een taak agent bewerking opgehaald. |
> | Actie | Microsoft.Sql/locations/longTermRetentionBackups/read | Een lijst met de back-ups voor lange termijn retentie voor elke Data Base op elke server op een locatie |
> | Actie | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Een lijst met de back-ups voor lange termijn retentie voor elke Data Base op een server |
> | Actie | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Hiermee wordt een back-up voor de lange termijn verwijderd |
> | Actie | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Een lijst met de back-ups voor lange termijn retentie voor een Data Base |
> | Actie | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | De herstel bewerking voor de beheerde data base volt ooien |
> | Actie | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Hiermee worden bewerkingen uitgevoerd op transparante gegevens versleuteling beheerde instantie versleuteling beveiliging |
> | Actie | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Hiermee worden bewerkingen uitgevoerd op transparante gegevens versleuteling beheerde instantie versleuteling beveiliging |
> | Actie | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Hiermee worden bewerkingen uitgevoerd op de transparante instantie sleutels van beheerde exemplaren van gegevens versleuteling |
> | Actie | Micro soft. SQL/locaties/managedInstanceKeyOperationResults/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante instantie sleutels van beheerde exemplaren van gegevens versleuteling |
> | Actie | Micro soft. SQL/locaties/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/lezen | Hiermee wordt de status opgehaald van een beleids bewerking voor lange termijn retentie voor een beheerde data base |
> | Actie | Micro soft. SQL/locaties/managedInstanceLongTermRetentionPolicyOperationResults/lezen | Hiermee wordt de status opgehaald van een beleids bewerking voor lange termijn retentie voor een beheerde data base |
> | Actie | Micro soft. SQL/locaties/managedShortTermRetentionPolicyOperationResults/lezen | Hiermee wordt de status van een bewerking voor het bewaren van een korte termijn opgehaald |
> | Actie | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Hiermee worden bewerkingen uitgevoerd voor de transparante gegevens versleuteling van de beheerde data base |
> | Actie | Micro soft. SQL/locaties/managedTransparentDataEncryptionOperationResults/lezen | Hiermee worden bewerkingen uitgevoerd voor de transparante gegevens versleuteling van de beheerde data base |
> | Actie | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Hiermee wordt het resultaat voor een verbindings bewerking voor een privé-eind punt opgehaald |
> | Actie | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Hiermee wordt het resultaat voor een verbindings bewerking voor een privé-eind punt opgehaald |
> | Actie | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Hiermee wordt het resultaat opgehaald voor een proxy bewerking voor een privé-eindpunt verbinding |
> | Actie | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Hiermee wordt het resultaat opgehaald voor een proxy bewerking voor een privé-eindpunt verbinding |
> | Actie | Micro soft. SQL/locaties/lezen | Hiermee worden de beschik bare locaties voor een gegeven abonnement opgehaald |
> | Actie | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Hiermee worden bewerkingen uitgevoerd op de transparante server sleutels voor gegevens versleuteling |
> | Actie | Micro soft. SQL/locaties/serverKeyOperationResults/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante server sleutels voor gegevens versleuteling |
> | Actie | Micro soft. SQL/locaties/shortTermRetentionPolicyOperationResults/lezen | Hiermee wordt de status van een bewerking voor het bewaren van een korte termijn opgehaald |
> | Actie | Microsoft.Sql/locations/syncAgentOperationResults/read | Resultaat van de resource bewerking van de synchronisatie agent ophalen |
> | Actie | Microsoft.Sql/locations/syncDatabaseIds/read | De data base-id's van de synchronisatie voor een bepaalde regio en een abonnement ophalen |
> | Actie | Microsoft.Sql/locations/syncGroupOperationResults/read | Resultaat van de resource bewerking synchronisatie groep ophalen |
> | Actie | Microsoft.Sql/locations/syncMemberOperationResults/read | Resultaat van de bewerking voor het synchroniseren van leden bronnen ophalen |
> | Actie | Micro soft. SQL/locaties/gebruik/lezen | Hiermee wordt een verzameling metrische gegevens over gebruik voor dit abonnement opgehaald op een locatie |
> | Actie | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Retourneert de details van de opgegeven virtuele netwerk regels Azure async-bewerking  |
> | Actie | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Hiermee worden de details van de opgegeven bewerking voor virtuele netwerk regels geretourneerd  |
> | Actie | Microsoft.Sql/managedInstances/administrators/delete | Hiermee verwijdert u een bestaande beheerder van het beheerde exemplaar. |
> | Actie | Microsoft.Sql/managedInstances/administrators/read | Hiermee wordt een lijst met beheerders van beheerde instanties opgehaald. |
> | Actie | Micro soft. SQL/managedInstances/beheerders/schrijven | Hiermee wordt een beheerde exemplaar beheerder met de opgegeven para meters gemaakt of bijgewerkt. |
> | Actie | Micro soft. SQL/managedInstances/data bases/backupLongTermRetentionPolicies/lezen | Hiermee wordt een Bewaar beleid voor lange termijn voor een beheerde data base opgehaald |
> | Actie | Micro soft. SQL/managedInstances/data bases/backupLongTermRetentionPolicies/schrijven | Hiermee wordt een Bewaar beleid voor lange termijn voor een beheerde data base bijgewerkt |
> | Actie | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Hiermee wordt een Bewaar beleid voor de korte termijn voor een beheerde data base opgehaald |
> | Actie | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Hiermee wordt een Bewaar beleid voor de korte termijn voor een beheerde data base bijgewerkt |
> | Actie | Micro soft. SQL/managedInstances/data bases/kolommen/lezen | Een lijst met kolommen voor een beheerde data base retour neren |
> | Actie | Micro soft. SQL/managedInstances/data bases/completeRestore/actie | De herstel bewerking voor de beheerde data base volt ooien |
> | Actie | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Gevoeligheids labels van een bepaalde Data Base weer geven |
> | Actie | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Gevoeligheids labels batch-update |
> | Actie | Microsoft.Sql/managedInstances/databases/delete | Hiermee verwijdert u een bestaande beheerde data base |
> | Actie | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Actie | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Hiermee worden de beschik bare logboeken voor beheerde exemplaar databases opgehaald |
> | Actie | Microsoft.Sql/managedInstances/databases/read | Hiermee wordt een bestaande beheerde data base opgehaald |
> | Actie | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Gevoeligheids labels van een bepaalde Data Base weer geven |
> | Actie | Micro soft. SQL/managedInstances/data bases/recommendedSensitivityLabels/schrijven | Aanbevolen gevoeligheids labels voor batch update |
> | Actie | Micro soft. SQL/managedInstances/data bases/restoreDetails/lezen | Retourneert Details over het herstellen van de beheerde data base terwijl het terugzetten wordt uitgevoerd. |
> | Actie | Microsoft.Sql/managedInstances/databases/schemas/read | Een beheerd database schema ophalen. (alleen schema) |
> | Actie | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Een kolom met beheerde data base ophalen (alleen schema) |
> | Actie | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Het gevoeligheids label van een bepaalde kolom verwijderen |
> | Actie | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Gevoeligheids aanbevelingen voor een bepaalde kolom uitschakelen |
> | Actie | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Gevoeligheids aanbevelingen voor een bepaalde kolom inschakelen |
> | Actie | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Het gevoeligheids label van een bepaalde kolom ophalen |
> | Actie | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Het gevoeligheids label van een bepaalde kolom maken of bijwerken |
> | Actie | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Een beheerde database tabel ophalen (alleen schema) |
> | Actie | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Een lijst met beleids regels voor detectie van het beleid voor beheerde data bases die zijn geconfigureerd voor een bepaalde server ophalen |
> | Actie | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Het beleid voor het detecteren van de Data Base voor een bepaalde beheerde data base wijzigen |
> | Actie | Microsoft.Sql/managedInstances/databases/securityEvents/read | Hiermee worden de beveiligings gebeurtenissen van de beheerde data base opgehaald |
> | Actie | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Gevoeligheids labels van een bepaalde Data Base weer geven |
> | Actie | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Details van de data base ophalen Transparent Data Encryption op een bepaalde beheerde data base |
> | Actie | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | De data base-Transparent Data Encryption voor een bepaalde beheerde data base wijzigen |
> | Actie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | De evaluatie van beveiligings problemen voor een bepaalde data base verwijderen |
> | Actie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Het beleid voor evaluatie van beveiligings problemen ophalen voor een givendatabase |
> | Actie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | De regel basislijn voor evaluatie van beveiligings problemen verwijderen voor een bepaalde data base |
> | Actie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | De regel basislijn voor evaluatie van beveiligings problemen ophalen voor een bepaalde data base |
> | Actie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | De regel voor de evaluatie van beveiligings problemen wijzigen voor een bepaalde data base |
> | Actie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Een bestaand scan resultaat converteren naar een lees bare indeling. Als er al bestaat, gebeurt er niets |
> | Actie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Voer de database scan voor evaluatie van beveiligings problemen uit. |
> | Actie | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/scans/lezen | Retour neer de lijst met evaluatie records van het beveiligings probleem voor de data base of haal de scan record op voor de opgegeven scan-ID. |
> | Actie | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | De evaluatie van beveiligings problemen voor een bepaalde data base wijzigen |
> | Actie | Microsoft.Sql/managedInstances/databases/write | Hiermee maakt u een nieuwe data base of werkt u een bestaande data base bij. |
> | Actie | Micro soft. SQL/managedInstances/verwijderen | Hiermee verwijdert u een bestaand beheerd exemplaar. |
> | Actie | Microsoft.Sql/managedInstances/encryptionProtector/read | Hiermee wordt een lijst met server versleutelings beveiligingen geretourneerd of worden de eigenschappen opgehaald voor de opgegeven server versleutelings beveiliging. |
> | Actie | Micro soft. SQL/managedInstances/encryptionProtector/revalidate/Action | Werk de eigenschappen voor de opgegeven server versleutelings beveiliging bij. |
> | Actie | Microsoft.Sql/managedInstances/encryptionProtector/write | Werk de eigenschappen voor de opgegeven server versleutelings beveiliging bij. |
> | Actie | Micro soft. SQL/managedInstances/inaccessibleManagedDatabases/lezen | Hiermee wordt een lijst met niet-toegankelijke beheerde data bases in een beheerd exemplaar opgehaald |
> | Actie | Micro soft. SQL/managedInstances/sleutels/verwijderen | Hiermee verwijdert u een bestaande Azure SQL Managed instance-sleutel. |
> | Actie | Micro soft. SQL/managedInstances/sleutels/lezen | Hiermee wordt de lijst met beheerde exemplaar sleutels geretourneerd of worden de eigenschappen opgehaald voor de opgegeven sleutel van het beheerde exemplaar. |
> | Actie | Microsoft.Sql/managedInstances/keys/write | Hiermee maakt u een sleutel met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven sleutel van het beheerde exemplaar bij. |
> | Actie | Micro soft. SQL/managedInstances/metricDefinitions/lezen | Metrische definities voor beheerde instanties ophalen |
> | Actie | Micro soft. SQL/managedInstances/metrieken/lezen | Metrische gegevens van beheerde instanties ophalen |
> | Actie | Micro soft. SQL/managedInstances/Operations/Cancel/Action | Hiermee wordt een asynchrone bewerking die nog niet is voltooid, geannuleerd in Azure SQL Managed instance. |
> | Actie | Micro soft. SQL/managedInstances/Operations/Read | Bewerkingen voor beheerde instanties ophalen |
> | Actie | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Actie | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Hiermee worden de beschik bare logboeken voor beheerde instanties opgehaald |
> | Actie | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Typen metrische gegevens retour neren die beschikbaar zijn voor beheerde instanties |
> | Actie | Microsoft.Sql/managedInstances/read | De lijst met beheerde exemplaren retour neren of de eigenschappen van het opgegeven beheerde exemplaar ophalen. |
> | Actie | Micro soft. SQL/managedInstances/recoverableDatabases/lezen | Hiermee wordt een lijst met herstel bare beheerde data bases geretourneerd |
> | Actie | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Hiermee wordt een Bewaar beleid voor de korte termijn opgehaald voor een verwijderde beheerde data base |
> | Actie | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Hiermee wordt een Bewaar beleid voor de korte termijn bijgewerkt voor een verwijderde beheerde data base |
> | Actie | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Retourneert een lijst met restorable verwijderde beheerde data bases. |
> | Actie | Micro soft. SQL/managedInstances/securityAlertPolicies/lezen | Een lijst met beleids regels voor detectie van begeleide server dreigingen ophalen die zijn geconfigureerd voor een bepaalde server |
> | Actie | Micro soft. SQL/managedInstances/securityAlertPolicies/schrijven | Het beleid voor detectie van begeleide server dreigingen voor een beheerde server wijzigen |
> | Actie | Microsoft.Sql/managedInstances/tdeCertificates/action | TDE-certificaat maken/bijwerken |
> | Actie | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | De evaluatie van beveiligings problemen voor een bepaald beheerd exemplaar verwijderen |
> | Actie | Micro soft. SQL/managedInstances/vulnerabilityAssessments/lezen | Het beleid voor evaluatie van beveiligings problemen voor een gegeven beheerd exemplaar ophalen |
> | Actie | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | De evaluatie van beveiligings problemen voor een bepaald beheerd exemplaar wijzigen |
> | Actie | Micro soft. SQL/managedInstances/schrijven | Hiermee maakt u een beheerd exemplaar met de opgegeven para meters of werkt u de eigenschappen of labels voor het opgegeven beheerde exemplaar bij. |
> | Actie | Micro soft. SQL/Operations/lezen | Beschik bare REST bewerkingen ophalen |
> | Actie | Micro soft. SQL/privateEndpointConnectionsApproval/actie | Hiermee wordt bepaald of de gebruiker een verbinding met een privé-eind punt mag goed keuren |
> | Actie | Micro soft. SQL/REGI ster/actie | Hiermee wordt het abonnement voor de resource provider van micro soft SQL Database geregistreerd en wordt het maken van micro soft SQL-data bases mogelijk. |
> | Actie | Micro soft. SQL/servers/beheerders/verwijderen | Hiermee verwijdert u een specifiek Azure Active Directory-beheerder object |
> | Actie | Micro soft. SQL/servers/beheerders/lezen | Hiermee wordt een specifiek Azure Active Directory-beheer object opgehaald |
> | Actie | Micro soft. SQL/servers/beheerders/schrijven | Hiermee wordt een specifiek Azure Active Directory-beheerder object toegevoegd of bijgewerkt |
> | Actie | Micro soft. SQL/servers/Advisor/lezen | Hiermee wordt een lijst met beschik bare Advisor voor de server geretourneerd |
> | Actie | Micro soft. SQL/servers/Advisors/recommendedActions/lezen | Hiermee wordt een lijst met aanbevolen acties van opgegeven Advisor voor de server geretourneerd |
> | Actie | Micro soft. SQL/servers/Advisors/recommendedActions/schrijven | De aanbevolen actie Toep assen op de server |
> | Actie | Micro soft. SQL/servers/Advisors/schrijven | Hiermee wordt de status voor automatisch uitvoeren van een Advisor op server niveau bijgewerkt. |
> | Actie | Micro soft. SQL/servers/auditingPolicies/lezen | Details ophalen van het standaard controle beleid voor Server tabellen dat op een bepaalde server is geconfigureerd |
> | Actie | Micro soft. SQL/servers/auditingPolicies/schrijven | De standaard controle van de Server tabel voor een bepaalde server wijzigen |
> | Actie | Micro soft. SQL/servers/auditingSettings/operationResults/lezen | Resultaat ophalen van de set-bewerking voor het controle beleid voor Server-blobs |
> | Actie | Micro soft. SQL/servers/auditingSettings/lezen | Details ophalen van het controle beleid voor de server-BLOB dat op een bepaalde server is geconfigureerd |
> | Actie | Micro soft. SQL/servers/auditingSettings/schrijven | De controle van de server-BLOB voor een bepaalde server wijzigen |
> | Actie | Microsoft.Sql/servers/automaticTuning/read | Retourneert instellingen voor automatisch afstemmen voor de server |
> | Actie | Micro soft. SQL/servers/automaticTuning/schrijven | Hiermee worden de instellingen voor automatisch afstemmen van de server bijgewerkt en worden bijgewerkte instellingen geretourneerd |
> | Actie | Micro soft. SQL/servers/backupLongTermRetentionVaults/verwijderen | Hiermee verwijdert u een bestaande back-uparchief kluis. |
> | Actie | Micro soft. SQL/servers/backupLongTermRetentionVaults/lezen | Deze bewerking wordt gebruikt om een back-upkluis voor lange termijn retentie te verkrijgen. Hiermee wordt informatie geretourneerd over de kluis die op deze server is geregistreerd |
> | Actie | Micro soft. SQL/servers/backupLongTermRetentionVaults/schrijven | Met deze bewerking wordt een back-upkluis voor lange termijn registratie op een server geregistreerd |
> | Actie | Microsoft.Sql/servers/communicationLinks/delete | Hiermee verwijdert u een bestaande server communicatie koppeling. |
> | Actie | Microsoft.Sql/servers/communicationLinks/read | Hiermee wordt de lijst met communicatie koppelingen van een opgegeven server geretourneerd. |
> | Actie | Microsoft.Sql/servers/communicationLinks/write | Een server communicatie koppeling maken of bijwerken. |
> | Actie | Micro soft. SQL/servers/connectionPolicies/lezen | De lijst met server verbindings beleid van een opgegeven server retour neren. |
> | Actie | Micro soft. SQL/servers/connectionPolicies/schrijven | Een server verbindings beleid maken of bijwerken. |
> | Actie | Micro soft. SQL/servers/data bases/advisores/lezen | Hiermee wordt een lijst geretourneerd met de Advisor die beschikbaar zijn voor de data base |
> | Actie | Micro soft. SQL/servers/data bases/advisores/recommendedActions/lezen | Hiermee wordt een lijst met aanbevolen acties van de opgegeven Advisor voor de data base geretourneerd |
> | Actie | Micro soft. SQL/servers/data bases/adviseurs/recommendedActions/schrijven | De aanbevolen actie Toep assen op de data base |
> | Actie | Micro soft. SQL/servers/data bases/advisores/schrijven | Update status automatisch uitvoeren van een Advisor op database niveau. |
> | Actie | Microsoft.Sql/servers/databases/auditingPolicies/read | Details ophalen van het controle beleid voor tabellen dat is geconfigureerd voor een bepaalde data base |
> | Actie | Micro soft. SQL/servers/data bases/auditingPolicies/schrijven | Het controle beleid voor tabellen voor een bepaalde data base wijzigen |
> | Actie | Micro soft. SQL/servers/data bases/auditingSettings/lezen | Details ophalen van het BLOB-controle beleid dat is geconfigureerd voor een bepaalde data base |
> | Actie | Micro soft. SQL/servers/data bases/auditingSettings/schrijven | Het BLOB-controle beleid voor een bepaalde data base wijzigen |
> | Actie | Microsoft.Sql/servers/databases/auditRecords/read | De data base-BLOB-controle records ophalen |
> | Actie | Micro soft. SQL/servers/data bases/automaticTuning/lezen | Retourneert instellingen voor automatisch afstemmen voor een Data Base |
> | Actie | Micro soft. SQL/servers/data bases/automaticTuning/schrijven | Hiermee worden de instellingen voor automatisch afstemmen voor een Data Base bijgewerkt en worden bijgewerkte instellingen geretourneerd |
> | Actie | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Hiermee wordt de status van een database bewerking opgehaald. |
> | Actie | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | De lijst met back-uparchiverbeleid van een opgegeven Data Base retour neren. |
> | Actie | Micro soft. SQL/servers/data bases/backupLongTermRetentionPolicies/schrijven | Een archief beleid voor back-ups van de data base maken of bijwerken. |
> | Actie | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Hiermee wordt een Bewaar beleid voor de korte termijn voor een Data Base opgehaald |
> | Actie | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Hiermee wordt een Bewaar beleid voor de korte termijn voor een Data Base bijgewerkt |
> | Actie | Micro soft. SQL/servers/data bases/kolommen/lezen | Een lijst met kolommen voor een Data Base retour neren |
> | Actie | Microsoft.Sql/servers/databases/connectionPolicies/read | Details ophalen van het verbindings beleid dat is geconfigureerd voor een bepaalde data base |
> | Actie | Micro soft. SQL/servers/data bases/connectionPolicies/schrijven | Het verbindings beleid voor een bepaalde data base wijzigen |
> | Actie | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Gevoeligheids labels van een bepaalde Data Base weer geven |
> | Actie | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Gevoeligheids labels batch-update |
> | Actie | Micro soft. SQL/servers/data bases/dataMaskingPolicies/lezen | Retour neer de lijst met beleids regels voor database gegevens maskering. |
> | Actie | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Beleids regel voor gegevens maskering voor een bepaalde data base verwijderen |
> | Actie | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Details ophalen van de beleids regel voor gegevens maskering die is geconfigureerd voor een bepaalde data base |
> | Actie | Micro soft. SQL/servers/data bases/dataMaskingPolicies/Rules/write | Beleids regel voor gegevens maskering wijzigen voor een bepaalde data base |
> | Actie | Micro soft. SQL/servers/data bases/dataMaskingPolicies/schrijven | Gegevens maskerings beleid voor een bepaalde data base wijzigen |
> | Actie | Micro soft. SQL/servers/data bases/dataWarehouseQueries/dataWarehouseQuerySteps/lezen | Retourneert de stap gegevens van de gedistribueerde query van de Data Warehouse-query voor de geselecteerde stap-ID |
> | Actie | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Retourneert de gegevens van de distributie query van het Data Warehouse voor de geselecteerde query-ID |
> | Actie | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Hiermee worden de gebruikers activiteiten opgehaald van een SQL Data Warehouse-exemplaar dat actieve en onderbroken query's bevat |
> | Actie | Micro soft. SQL/servers/data bases/verwijderen | Hiermee verwijdert u een bestaande data base. |
> | Actie | Microsoft.Sql/servers/databases/export/action | Azure SQL Database exporteren |
> | Actie | Micro soft. SQL/servers/data bases/extendedAuditingSettings/lezen | Details ophalen van het uitgebreide BLOB-controle beleid dat is geconfigureerd voor een bepaalde data base |
> | Actie | Micro soft. SQL/servers/data bases/extendedAuditingSettings/schrijven | Het uitgebreide BLOB-controle beleid voor een bepaalde data base wijzigen |
> | Actie | Micro soft. SQL/servers/data bases/uitbrei dingen/lezen | Hiermee wordt een verzameling extensies opgehaald voor de data base. |
> | Actie | Micro soft. SQL/servers/data bases/extensies/schrijven | De extensie voor een bepaalde data base wijzigen |
> | Actie | Micro soft. SQL/servers/data bases/failover/actie | Door klant geïnitieerde data base-failover. |
> | Actie | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Geografisch back-upbeleid voor een bepaalde data base ophalen |
> | Actie | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Een beleid voor geoback-ups van de data base maken of bijwerken |
> | Actie | Microsoft.Sql/servers/databases/importExportOperationResults/read | Hiermee worden bewerkingen voor importeren/exporteren uitgevoerd |
> | Actie | Micro soft. SQL/servers/data bases/maintenanceWindowOptions/lezen | Hiermee wordt een lijst met beschik bare onderhouds Vensters voor een geselecteerde data base opgehaald. |
> | Actie | Micro soft. SQL/servers/data bases/maintenanceWindows/lezen | Hiermee worden de instellingen voor onderhouds Vensters voor een geselecteerde data base opgehaald. |
> | Actie | Micro soft. SQL/servers/data bases/maintenanceWindows/schrijven | Hiermee stelt u de Windows-instellingen voor onderhoud in voor een geselecteerde data base. |
> | Actie | Micro soft. SQL/servers/data bases/metricDefinitions/lezen | Typen metrische gegevens die beschikbaar zijn voor data bases retour neren |
> | Actie | Micro soft. SQL/servers/data bases/metrische gegevens/lezen | Metrische gegevens voor data bases retour neren |
> | Actie | Microsoft.Sql/servers/databases/move/action | De naam van een bestaande data base wijzigen. |
> | Actie | Micro soft. SQL/servers/data bases/operationResults/lezen | Hiermee wordt de status van een database bewerking opgehaald. |
> | Actie | Microsoft.Sql/servers/databases/operations/cancel/action | Annuleert Azure SQL Database asynchrone bewerking in behandeling die nog niet is voltooid. |
> | Actie | Micro soft. SQL/servers/data bases/Operations/lezen | De lijst met bewerkingen die worden uitgevoerd op de data base retour neren |
> | Actie | Microsoft.Sql/servers/databases/pause/action | Azure SQL Data Warehouse database onderbreken |
> | Actie | Micro soft. SQL/servers/data bases/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Actie | Micro soft. SQL/servers/data bases/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Hiermee worden de beschik bare logboeken voor data bases opgehaald |
> | Actie | Micro soft. SQL/servers/data bases/providers/micro soft. Insights/metricDefinitions/lezen | Typen metrische gegevens die beschikbaar zijn voor data bases retour neren |
> | Actie | Micro soft. SQL/servers/data bases/queryStore/queryTexts/lezen | Retourneert de verzameling query teksten die overeenkomen met de opgegeven para meters. |
> | Actie | Micro soft. SQL/servers/data bases/queryStore/lezen | Retourneert de huidige waarden van de query Store-instellingen voor de data base. |
> | Actie | Micro soft. SQL/servers/data bases/queryStore/schrijven | Query Store-instelling voor de data base bijwerken |
> | Actie | Micro soft. SQL/servers/data bases/lezen | De lijst met data bases retour neren of de eigenschappen voor de opgegeven Data Base ophalen. |
> | Actie | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Gevoeligheids labels van een bepaalde Data Base weer geven |
> | Actie | Micro soft. SQL/servers/data bases/recommendedSensitivityLabels/schrijven | Aanbevolen gevoeligheids labels voor batch update |
> | Actie | Microsoft.Sql/servers/databases/replicationLinks/delete | De replicatie relatie geforceerd en met mogelijk gegevens verlies beëindigen |
> | Actie | Micro soft. SQL/servers/data bases/replicationLinks/failover/actie | Failover na het synchroniseren van alle wijzigingen van de primaire, waardoor deze data base in de replicatie relationship\u0027s primair en de externe primaire in een secundaire |
> | Actie | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Failover direct met mogelijk gegevens verlies, waardoor deze data base in de replicatie relationship\u0027s primair en de externe primaire in een secundaire |
> | Actie | Micro soft. SQL/servers/data bases/replicationLinks/lezen | De lijst met replicatie koppelingen retour neren of de eigenschappen ophalen voor de opgegeven replicatie koppelingen. |
> | Actie | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | De replicatie relatie geforceerd of na synchronisatie met de partner beëindigen |
> | Actie | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Replicatie modus bijwerken voor koppeling naar synchrone of asynchrone modus |
> | Actie | Microsoft.Sql/servers/databases/restorePoints/action | Hiermee maakt u een nieuw herstel punt |
> | Actie | Microsoft.Sql/servers/databases/restorePoints/delete | Hiermee verwijdert u een herstel punt voor de data base. |
> | Actie | Microsoft.Sql/servers/databases/restorePoints/read | Retourneert herstel punten voor de data base. |
> | Actie | Microsoft.Sql/servers/databases/resume/action | Azure SQL Data Warehouse database hervatten |
> | Actie | Micro soft. SQL/servers/data bases/schema's/lezen | Een database schema ophalen (alleen schema). |
> | Actie | Micro soft. SQL/servers/data bases/schema's/tabellen/kolommen/lezen | Een database kolom ophalen (alleen schema). |
> | Actie | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Het gevoeligheids label van een bepaalde kolom verwijderen |
> | Actie | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Gevoeligheids aanbevelingen voor een bepaalde kolom uitschakelen |
> | Actie | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Gevoeligheids aanbevelingen voor een bepaalde kolom inschakelen |
> | Actie | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Het gevoeligheids label van een bepaalde kolom ophalen |
> | Actie | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Het gevoeligheids label van een bepaalde kolom maken of bijwerken |
> | Actie | Micro soft. SQL/servers/data bases/schema's/tabellen/lezen | Een database tabel ophalen (alleen schema). |
> | Actie | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Lijst met index aanbevelingen voor een Data Base ophalen |
> | Actie | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Aanbeveling index Toep assen |
> | Actie | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Een lijst ophalen met beleids regels voor het detecteren van de database bedreigingen die zijn geconfigureerd voor een bepaalde server |
> | Actie | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Het beleid voor het detecteren van de beleids regels voor een bepaalde data base wijzigen |
> | Actie | Micro soft. SQL/servers/data bases/securityMetrics/lezen | Hiermee wordt een verzameling van metrische gegevens voor database beveiliging opgehaald |
> | Actie | Microsoft.Sql/servers/databases/sensitivityLabels/read | Gevoeligheids labels van een bepaalde Data Base weer geven |
> | Actie | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Retour suggestie over het omhoog of omlaag schalen van de Data Base op basis van statistieken voor query-uitvoering om prestaties te verbeteren of kosten te verlagen |
> | Actie | Microsoft.Sql/servers/databases/skus/read | Hiermee wordt een verzameling van sku's opgehaald die beschikbaar zijn voor een Data Base |
> | Actie | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Synchronisatie groep annuleren |
> | Actie | Microsoft.Sql/servers/databases/syncGroups/delete | Hiermee verwijdert u een bestaande synchronisatie groep. |
> | Actie | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | De lijst met data base-schema's voor de synchronisatie hub retour neren |
> | Actie | Micro soft. SQL/servers/data bases/syncGroups/logboeken/lezen | De lijst met Logboeken voor synchronisatie groepen retour neren |
> | Actie | Micro soft. SQL/servers/data bases/syncGroups/lezen | De lijst met synchronisatie groepen retour neren of de eigenschappen voor de opgegeven synchronisatie groep ophalen. |
> | Actie | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Data base-schema voor synchronisatie-hub vernieuwen |
> | Actie | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Resultaat van de bewerking synchronisatie hub-schema vernieuwen ophalen |
> | Actie | Micro soft. SQL/servers/data bases/syncGroups/syncMembers/verwijderen | Hiermee verwijdert u een bestaand synchronisatie-lid. |
> | Actie | Micro soft. SQL/servers/data bases/syncGroups/syncMembers/lezen | De lijst met synchronisatie leden retour neren of de eigenschappen van het opgegeven Sync-lid ophalen. |
> | Actie | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Synchronisatie leden schema vernieuwen |
> | Actie | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Resultaat ophalen van de vernieuwings bewerking voor het synchronisatie leden schema |
> | Actie | Micro soft. SQL/servers/data bases/syncGroups/syncMembers/schema's/lezen | De lijst met database schema's van het synchronisatie-lid retour neren |
> | Actie | Micro soft. SQL/servers/data bases/syncGroups/syncMembers/schrijven | Hiermee maakt u een Sync-lid met de opgegeven para meters of werkt u de eigenschappen voor het opgegeven Sync-lid bij. |
> | Actie | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Synchronisatie van synchronisatie groep activeren |
> | Actie | Micro soft. SQL/servers/data bases/syncGroups/schrijven | Hiermee maakt u een synchronisatie groep met de opgegeven para meters of werkt u de eigenschappen voor de opgegeven synchronisatie groep bij. |
> | Actie | Microsoft.Sql/servers/databases/topQueries/queryText/action | Hiermee wordt de Transact-SQL-tekst geretourneerd voor de geselecteerde query-ID |
> | Actie | Micro soft. SQL/servers/data bases/topQueries/lezen | Hiermee worden geaggregeerde runtime statistieken geretourneerd voor de geselecteerde query in de geselecteerde tijds periode |
> | Actie | Micro soft. SQL/servers/data bases/topQueries/statistieken/lezen | Hiermee worden geaggregeerde runtime statistieken geretourneerd voor de geselecteerde query in de geselecteerde tijds periode |
> | Actie | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Hiermee worden bewerkingen uitgevoerd op de transparante gegevens versleuteling |
> | Actie | Microsoft.Sql/servers/databases/transparentDataEncryption/read | De status en Details van de functie voor transparante gegevens versleuteling ophalen voor een bepaalde data base |
> | Actie | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Transparante gegevens versleutelings status wijzigen |
> | Actie | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Een upgrade uitvoeren voor Azure SQL Data Warehouse database |
> | Actie | Microsoft.Sql/servers/databases/usages/read | Hiermee worden de gegevens van het Azure SQL Database gebruik opgehaald |
> | Actie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | De evaluatie van beveiligings problemen voor een bepaalde data base verwijderen |
> | Actie | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/lezen | Het beleid voor evaluatie van beveiligings problemen ophalen voor een givendatabase |
> | Actie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | De regel basislijn voor evaluatie van beveiligings problemen verwijderen voor een bepaalde data base |
> | Actie | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/regels/basis lijnen/lezen | De regel basislijn voor evaluatie van beveiligings problemen ophalen voor een bepaalde data base |
> | Actie | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/regels/basis lijnen/schrijven | De regel voor de evaluatie van beveiligings problemen wijzigen voor een bepaalde data base |
> | Actie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Een bestaand scan resultaat converteren naar een lees bare indeling. Als er al bestaat, gebeurt er niets |
> | Actie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Voer de database scan voor evaluatie van beveiligings problemen uit. |
> | Actie | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/scans/lezen | Retour neer de lijst met evaluatie records van het beveiligings probleem voor de data base of haal de scan record op voor de opgegeven scan-ID. |
> | Actie | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | De evaluatie van beveiligings problemen voor een bepaalde data base wijzigen |
> | Actie | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Voer de database scan voor evaluatie van beveiligings problemen uit. |
> | Actie | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Ophalen van het resultaat van de data base-evaluatie van het beveiligings probleem beoordelings bewerking |
> | Actie | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Details ophalen van de evaluatie van beveiligings problemen die is geconfigureerd voor een bepaalde data base |
> | Actie | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | De evaluatie van beveiligings problemen voor een bepaalde data base wijzigen |
> | Actie | Micro soft. SQL/servers/data bases/workloadGroups/verwijderen | Een specifieke werkbelasting groep wordt neergezet. |
> | Actie | Micro soft. SQL/servers/data bases/workloadGroups/lezen | Geeft een lijst van de werkbelasting groepen voor een geselecteerde data base. |
> | Actie | Micro soft. SQL/servers/data bases/workloadGroups/workloadClassifiers/verwijderen | De classificatie van een specifiek werk belasting wordt neergezet. |
> | Actie | Micro soft. SQL/servers/data bases/workloadGroups/workloadClassifiers/lezen | Geeft een lijst van de classificaties van werk belastingen voor een geselecteerde data base. |
> | Actie | Micro soft. SQL/servers/data bases/workloadGroups/workloadClassifiers/schrijven | Hiermee stelt u de eigenschappen voor een specifieke classificatie van werk belastingen. |
> | Actie | Micro soft. SQL/servers/data bases/workloadGroups/schrijven | Hiermee stelt u de eigenschappen voor een specifieke werkbelasting groep. |
> | Actie | Micro soft. SQL/servers/data bases/schrijven | Hiermee maakt u een Data Base met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven Data Base bij. |
> | Actie | Micro soft. SQL/servers/verwijderen | Hiermee verwijdert u een bestaande server. |
> | Actie | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Hiermee verwijdert u een bestaande configuratie voor herstel na nood gevallen voor een bepaalde server |
> | Actie | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Een DisasterRecoveryConfiguration failover |
> | Actie | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Failover van een DisasterRecoveryConfiguration forceren |
> | Actie | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Hiermee wordt een verzameling configuraties voor herstel na nood gevallen opgehaald die deze server bevatten |
> | Actie | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Configuratie voor nood herstel van de server wijzigen |
> | Actie | Microsoft.Sql/servers/elasticPoolEstimates/read | Hiermee wordt een lijst geretourneerd met schattingen van elastische groepen die al zijn gemaakt voor deze server |
> | Actie | Microsoft.Sql/servers/elasticPoolEstimates/write | Hiermee maakt u een nieuwe schatting voor een elastische pool voor een lijst met data bases |
> | Actie | Microsoft.Sql/servers/elasticPools/advisors/read | Hiermee wordt een lijst met beschik bare Advisor voor de elastische pool geretourneerd |
> | Actie | Micro soft. SQL/servers/elasticPools/Advisors/recommendedActions/lezen | Hiermee wordt een lijst met aanbevolen acties van opgegeven Advisor voor de elastische pool geretourneerd |
> | Actie | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | De aanbevolen actie Toep assen op de elastische pool |
> | Actie | Micro soft. SQL/servers/elasticPools/advisores/schrijven | Update status automatisch uitvoeren van een adviseur op het niveau van de elastische groep. |
> | Actie | Microsoft.Sql/servers/elasticPools/databases/read | Hiermee wordt een lijst met data bases voor een elastische pool opgehaald |
> | Actie | Microsoft.Sql/servers/elasticPools/delete | Bestaande elastische pool verwijderen |
> | Actie | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Activiteiten en Details ophalen voor een bepaalde pool voor Elastic data base |
> | Actie | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Activiteiten en Details ophalen voor een bepaalde data base die deel uitmaakt van een pool voor elastische data bases |
> | Actie | Micro soft. SQL/servers/elasticPools/failover/actie | Door klant geïnitieerde failover van elastische pool. |
> | Actie | Micro soft. SQL/servers/elasticPools/metricDefinitions/lezen | Typen metrische gegevens retour neren die beschikbaar zijn voor Pools voor elastische data bases |
> | Actie | Micro soft. SQL/servers/elasticPools/metrieken/lezen | Metrische gegevens retour neren voor Pools voor elastische data bases |
> | Actie | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Hiermee wordt een asynchrone bewerking die nog niet is voltooid, geannuleerd in Azure SQL elastische pool. |
> | Actie | Microsoft.Sql/servers/elasticPools/operations/read | De lijst met uitgevoerde bewerkingen voor de elastische pool retour neren |
> | Actie | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Actie | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Actie | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Typen metrische gegevens retour neren die beschikbaar zijn voor Pools voor elastische data bases |
> | Actie | Microsoft.Sql/servers/elasticPools/read | Details van elastische pool op een bepaalde server ophalen |
> | Actie | Microsoft.Sql/servers/elasticPools/skus/read | Hiermee wordt een verzameling sku's opgehaald die beschikbaar zijn voor een elastische pool |
> | Actie | Micro soft. SQL/servers/elasticPools/schrijven | Een nieuwe of gewijzigde eigenschappen van een bestaande elastische pool maken |
> | Actie | Microsoft.Sql/servers/encryptionProtector/read | Hiermee wordt een lijst met server versleutelings beveiligingen geretourneerd of worden de eigenschappen opgehaald voor de opgegeven server versleutelings beveiliging. |
> | Actie | Micro soft. SQL/servers/encryptionProtector/revalidate/Action | Werk de eigenschappen voor de opgegeven server versleutelings beveiliging bij. |
> | Actie | Micro soft. SQL/servers/encryptionProtector/schrijven | Werk de eigenschappen voor de opgegeven server versleutelings beveiliging bij. |
> | Actie | Micro soft. SQL/servers/extendedAuditingSettings/lezen | Details ophalen van het uitgebreide-server-BLOB-controle beleid dat op een bepaalde server is geconfigureerd |
> | Actie | Micro soft. SQL/servers/extendedAuditingSettings/schrijven | De controle van de uitgebreide server-BLOB voor een bepaalde server wijzigen |
> | Actie | Micro soft. SQL/servers/failoverGroups/verwijderen | Hiermee verwijdert u een bestaande failovergroep. |
> | Actie | Micro soft. SQL/servers/failoverGroups/failover/actie | Voert een geplande failover uit in een bestaande failovergroep. |
> | Actie | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Hiermee wordt geforceerde failover uitgevoerd in een bestaande failovergroep. |
> | Actie | Micro soft. SQL/servers/failoverGroups/lezen | Retourneert de lijst met failover-groepen of haalt de eigenschappen voor de opgegeven failovergroep op. |
> | Actie | Micro soft. SQL/servers/failoverGroups/schrijven | Hiermee maakt u een failovergroep met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven failovergroep bij. |
> | Actie | Micro soft. SQL/servers/firewallRules/verwijderen | Hiermee verwijdert u een bestaande server firewall regel. |
> | Actie | Micro soft. SQL/servers/firewallRules/lezen | De lijst met Server firewall regels retour neren of de eigenschappen voor de opgegeven server firewall regel ophalen. |
> | Actie | Micro soft. SQL/servers/firewallRules/schrijven | Hiermee maakt u een server firewall regel met de opgegeven para meters, werkt u de eigenschappen voor de opgegeven regel bij of overschrijft u alle bestaande regels met een nieuwe server firewall regel (s). |
> | Actie | Micro soft. SQL/servers/importeren/actie | Een nieuwe Data Base op de server maken en schema's en gegevens uit een DacPac-pakket implementeren |
> | Actie | Microsoft.Sql/servers/importExportOperationResults/read | Hiermee worden bewerkingen voor importeren/exporteren uitgevoerd |
> | Actie | Micro soft. SQL/servers/inaccessibleDatabases/lezen | Een lijst met niet-toegankelijke data base (s) retour neren in een logische server. |
> | Actie | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Hiermee wordt het opgegeven interface-eindpunt profiel verwijderd |
> | Actie | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Hiermee worden de eigenschappen van het opgegeven interface-eindpunt profiel geretourneerd |
> | Actie | Micro soft. SQL/servers/interfaceEndpointProfiles/schrijven | Hiermee maakt u een interface-eindpunt profiel met de opgegeven para meters of werkt u de eigenschappen of labels voor het opgegeven interface-eind punt bij |
> | Actie | Micro soft. SQL/servers/jobAgents/verwijderen | Hiermee verwijdert u een Azure SQL DB-taak agent |
> | Actie | Micro soft. SQL/servers/jobAgents/lezen | Hiermee wordt een Azure SQL DB-taak agent opgehaald |
> | Actie | Micro soft. SQL/servers/jobAgents/schrijven | Hiermee wordt een Azure SQL DB-taak agent gemaakt of bijgewerkt |
> | Actie | Micro soft. SQL/servers/sleutels/verwijderen | Hiermee verwijdert u een bestaande server sleutel. |
> | Actie | Micro soft. SQL/servers/sleutels/lezen | De lijst met Server sleutels retour neren of de eigenschappen voor de opgegeven server sleutel ophalen. |
> | Actie | Micro soft. SQL/servers/sleutels/schrijven | Hiermee maakt u een sleutel met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server sleutel bij. |
> | Actie | Micro soft. SQL/servers/operationResults/lezen | Hiermee worden Server bewerkingen uitgevoerd |
> | Actie | Micro soft. SQL/servers/bewerkingen/lezen | De lijst met bewerkingen die op de server zijn uitgevoerd, retour neren |
> | Actie | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Hiermee verwijdert u een bestaande verbindings proxy voor een persoonlijk eind punt |
> | Actie | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Hiermee wordt de lijst met particuliere endpoint-verbindings proxy's geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbinding proxy voor het particuliere eind punt. |
> | Actie | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Hiermee wordt een aanroep voor het maken van een privé-eind punt gevalideerd aan de kant van de NRP |
> | Actie | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Hiermee maakt u een particuliere endpoint-verbindings proxy met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven verbinding proxy van het particuliere eind punt bij. |
> | Actie | Microsoft.Sql/servers/privateEndpointConnections/delete | Hiermee verwijdert u een bestaande persoonlijke eindpunt verbinding |
> | Actie | Microsoft.Sql/servers/privateEndpointConnections/read | Hiermee wordt de lijst met privé-eindpunt verbindingen geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbinding met een privé-eind punt. |
> | Actie | Microsoft.Sql/servers/privateEndpointConnections/write | Hiermee wordt een bestaande verbinding met een privé-eind punt goedgekeurd of geweigerd |
> | Actie | Micro soft. SQL/servers/privateEndpointConnectionsApproval/actie | Hiermee wordt bepaald of de gebruiker een verbinding met een privé-eind punt mag goed keuren |
> | Actie | Microsoft.Sql/servers/privateLinkResources/read | De persoonlijke koppelings resources voor de bijbehorende SQL Server ophalen |
> | Actie | Micro soft. SQL/servers/providers/micro soft. Insights/metricDefinitions/lezen | Typen metrische gegevens retour neren die beschikbaar zijn voor servers |
> | Actie | Microsoft.Sql/servers/read | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Actie | Micro soft. SQL/servers/recommendedElasticPools/data bases/lezen | Metrische gegevens ophalen voor Aanbevolen Pools voor elastische data bases voor een bepaalde server |
> | Actie | Micro soft. SQL/servers/recommendedElasticPools/lezen | Haal aanbevelingen op voor Pools voor elastische data bases om de kosten te verlagen of de prestaties te verbeteren op basis van historisch resource gebruik |
> | Actie | Microsoft.Sql/servers/recoverableDatabases/read | Deze bewerking wordt gebruikt voor nood herstel van live data base om de data base terug te zetten naar het laatst bekende goede back-uppunt. Het retourneert informatie over de laatste goede back-up, maar doesn\u0027t de data base daad werkelijk te herstellen. |
> | Actie | Micro soft. SQL/servers/replicationLinks/lezen | De lijst met replicatie koppelingen retour neren of de eigenschappen ophalen voor de opgegeven replicatie koppelingen. |
> | Actie | Microsoft.Sql/servers/restorableDroppedDatabases/read | Een lijst ophalen met data bases die zijn verwijderd op een bepaalde server en nog steeds binnen het Bewaar beleid vallen. |
> | Actie | Micro soft. SQL/servers/securityAlertPolicies/operationResults/lezen | De resultaten van de Write-bewerking van het server bedreigings detectie beleid ophalen |
> | Actie | Micro soft. SQL/servers/securityAlertPolicies/lezen | Een lijst met beleids regels voor Server bedreigings detectie ophalen die zijn geconfigureerd voor een bepaalde server |
> | Actie | Micro soft. SQL/servers/securityAlertPolicies/schrijven | Het server Threat Detection-beleid voor een bepaalde server wijzigen |
> | Actie | Micro soft. SQL/servers/serviceObjectives/lezen | Lijst met serviceniveau doelstellingen ophalen (ook wel prestatie lagen genoemd) die beschikbaar zijn op een bepaalde server |
> | Actie | Micro soft. SQL/servers/syncAgents/verwijderen | Hiermee verwijdert u een bestaande synchronisatie agent. |
> | Actie | Microsoft.Sql/servers/syncAgents/generateKey/action | Registratie sleutel synchronisatie agent genereren |
> | Actie | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | De lijst met gekoppelde data bases van de gesynchroniseerde agent retour neren |
> | Actie | Micro soft. SQL/servers/syncAgents/lezen | De lijst met synchronisatie agenten retour neren of de eigenschappen voor de opgegeven synchronisatie agent ophalen. |
> | Actie | Micro soft. SQL/servers/syncAgents/schrijven | Hiermee maakt u een synchronisatie agent met de opgegeven para meters of werkt u de eigenschappen voor de opgegeven synchronisatie agent bij. |
> | Actie | Micro soft. SQL/servers/tdeCertificates/actie | TDE-certificaat maken/bijwerken |
> | Actie | Micro soft. SQL/servers/gebruik/lezen | Het DTU-quotum van de server en het huidige DTU-verbruik voor alle data bases op de server retour neren |
> | Actie | Micro soft. SQL/servers/virtualNetworkRules/verwijderen | Hiermee verwijdert u een bestaande Virtual Network regel |
> | Actie | Micro soft. SQL/servers/virtualNetworkRules/lezen | De lijst met regels voor het virtuele netwerk retour neren of de eigenschappen voor de opgegeven virtuele netwerk regel ophalen. |
> | Actie | Micro soft. SQL/servers/virtualNetworkRules/schrijven | Hiermee maakt u een regel voor het virtuele netwerk met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven virtuele-netwerk regel bij. |
> | Actie | Micro soft. SQL/servers/vulnerabilityAssessments/verwijderen | De evaluatie van beveiligings problemen voor een bepaalde server verwijderen |
> | Actie | Micro soft. SQL/servers/vulnerabilityAssessments/lezen | Het beleid voor evaluatie van beveiligings problemen op een bepaalde server ophalen |
> | Actie | Microsoft.Sql/servers/vulnerabilityAssessments/write | De evaluatie van beveiligings problemen voor een bepaalde server wijzigen |
> | Actie | Micro soft. SQL/servers/schrijven | Hiermee maakt u een server met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server bij. |
> | Actie | Micro soft. SQL/niet registreren/actie | Hiermee wordt de registratie van het abonnement voor de resource provider van micro soft SQL Database ongedaan gemaakt en wordt het maken van micro soft SQL-data bases ingeschakeld. |
> | Actie | Micro soft. SQL/virtualClusters/verwijderen | Hiermee verwijdert u een bestaand virtueel cluster. |
> | Actie | Micro soft. SQL/virtualClusters/lezen | De lijst met virtuele clusters retour neren of de eigenschappen van het opgegeven virtuele cluster ophalen. |
> | Actie | Micro soft. SQL/virtualClusters/schrijven | Hiermee werkt u virtuele-cluster Tags bij. |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. SqlVirtualMachine/locaties/availabilityGroupListenerOperationResults/lezen | Resultaat van een listener-bewerking van een beschikbaarheids groep ophalen |
> | Actie | Micro soft. SqlVirtualMachine/locaties/sqlVirtualMachineGroupOperationResults/lezen | Resultaat van een bewerking van een virtuele SQL-machine groep ophalen |
> | Actie | Micro soft. SqlVirtualMachine/locaties/sqlVirtualMachineOperationResults/lezen | Het resultaat van de bewerking van de virtuele SQL-machine ophalen |
> | Actie | Micro soft. SqlVirtualMachine/Operations/lezen |  |
> | Actie | Micro soft. SqlVirtualMachine/REGI ster/actie | Een abonnement bij micro soft. SqlVirtualMachine-resource provider registreren |
> | Actie | Micro soft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/verwijderen | Bestaande listener van beschikbaarheids groep verwijderen |
> | Actie | Micro soft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/lezen | Details ophalen van de SQL-beschikbaarheids groep-listener voor een bepaalde virtuele SQL-machine groep |
> | Actie | Micro soft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/write | Nieuwe of gewijzigde eigenschappen van de bestaande listener van de SQL-beschikbaarheids groep maken |
> | Actie | Micro soft. SqlVirtualMachine/sqlVirtualMachineGroups/verwijderen | Bestaande virtuele SQL-machine groep verwijderen |
> | Actie | Micro soft. SqlVirtualMachine/sqlVirtualMachineGroups/lezen | Details van de virtuele SQL-machine groep ophalen |
> | Actie | Micro soft. SqlVirtualMachine/sqlVirtualMachineGroups/sqlVirtualMachines/lezen | Virtuele SQL-machines weer geven op basis van een bepaalde virtuele SQL-machine groep |
> | Actie | Micro soft. SqlVirtualMachine/sqlVirtualMachineGroups/schrijven | Nieuwe eigenschappen van een bestaande virtuele SQL-machine groep maken of wijzigen |
> | Actie | Micro soft. SqlVirtualMachine/sqlVirtualMachines/verwijderen | Bestaande virtuele SQL-machine verwijderen |
> | Actie | Micro soft. SqlVirtualMachine/sqlVirtualMachines/lezen | Details van de virtuele SQL-machine ophalen |
> | Actie | Micro soft. SqlVirtualMachine/sqlVirtualMachines/schrijven | Nieuwe eigenschappen van een bestaande virtuele SQL-machine maken of wijzigen |
> | Actie | Micro soft. SqlVirtualMachine/registratie/actie | Registratie van abonnement bij micro soft. SqlVirtualMachine-resource provider opheffen |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. Storage/checknameavailability/lezen | Controleert of de account naam geldig is en niet wordt gebruikt. |
> | Actie | Micro soft. Storage/locaties/checknameavailability/lezen | Controleert of de account naam geldig is en niet wordt gebruikt. |
> | Actie | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Hiermee wordt aan micro soft. Storage door gemeld dat het virtuele netwerk of subnet wordt verwijderd |
> | Actie | Microsoft.Storage/locations/usages/read | Retourneert de limiet en het huidige gebruiks aantal voor resources in het opgegeven abonnement |
> | Actie | Microsoft.Storage/operations/read | Controleert de status van een asynchrone bewerking. |
> | Actie | Micro soft. Storage/REGI ster/actie | Hiermee wordt het abonnement voor de opslag Resource provider geregistreerd en wordt het maken van opslag accounts mogelijk. |
> | Actie | Microsoft.Storage/skus/read | Een lijst met de Sku's die worden ondersteund door micro soft. storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Retourneert het resultaat van het toevoegen van blob-inhoud |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Hiermee wordt het resultaat van het verwijderen van een BLOB geretourneerd |
> | DataAction | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/deleteBlobVersion/Action | Retourneert het resultaat van het verwijderen van een BLOB-versie |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Retourneert de lijst met blobs onder een account met het filter overeenkomende labels |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Retourneert een BLOB of een lijst met blobs |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | Retourneert het resultaat van de BLOB-opdracht |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Retourneert het resultaat van het lezen van BLOB-Tags |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Retourneert het resultaat van het schrijven van BLOB-Tags |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Hiermee wordt het resultaat van het schrijven van een BLOB geretourneerd |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Juridische bewaring van BLOB-container wissen |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Retourneert het resultaat van het verwijderen van een container |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Onveranderbaarheid-beleid van BLOB-container verwijderen |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Onveranderbaarheid-beleid van BLOB-container uitbreiden |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Onveranderbaarheid-beleid voor BLOB-container vergren delen |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Onveranderbaarheid-beleid voor BLOB-container ophalen |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Onveranderbaarheid-beleid voor BLOB-container plaatsen |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Retourneert het resultaat van de lease-BLOB-container |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/read | Retourneert een container |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/read | Hiermee wordt een lijst met containers opgehaald |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Juridische bewaring van BLOB-container instellen |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retourneert het resultaat van een patch BLOB-container |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/containers/write | Retourneert het resultaat van de put-BLOB-container |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Retourneert een gebruikers delegering sleutel voor de BLOB-service |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/read | Hiermee worden de eigenschappen of statistieken van de BLOB-service geretourneerd |
> | Actie | Microsoft.Storage/storageAccounts/blobServices/write | Retourneert het resultaat van de eigenschappen van de put BLOB-service |
> | Actie | Microsoft.Storage/storageAccounts/delete | Hiermee verwijdert u een bestaand opslag account. |
> | Actie | Micro soft. Storage/Storage accounts/encryptionScopes/lezen |  |
> | Actie | Micro soft. Storage/Storage accounts/encryptionScopes/lezen |  |
> | Actie | Micro soft. Storage/Storage accounts/encryptionScopes/schrijven |  |
> | Actie | Micro soft. Storage/Storage accounts/encryptionScopes/schrijven |  |
> | Actie | Microsoft.Storage/storageAccounts/failover/action | De klant kan de failover beheren in geval van beschikbaarheids problemen |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action | Machtigingen voor bestands beheerders ophalen |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Retourneert het resultaat van het verwijderen van een bestand/map |
> | DataAction | Micro soft. Storage/Storage accounts/fileServices/bestands shares/files/modifypermissions/Action | Hiermee wordt het resultaat van het wijzigen van de machtiging voor een bestand/map geretourneerd |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Retourneert een bestand/map of een lijst met bestanden/mappen |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Retourneert het resultaat van het schrijven van een bestand of het maken van een map |
> | Actie | Microsoft.Storage/storageAccounts/fileServices/read |  |
> | Actie | Microsoft.Storage/storageAccounts/fileServices/read | Eigenschappen van bestands service ophalen |
> | Actie | Micro soft. Storage/Storage accounts/fileServices/shares/verwijderen |  |
> | Actie | Micro soft. Storage/Storage accounts/fileServices/shares/lezen |  |
> | Actie | Micro soft. Storage/Storage accounts/fileServices/shares/lezen |  |
> | Actie | Micro soft. Storage/Storage accounts/fileServices/shares/schrijven |  |
> | Actie | Micro soft. Storage/Storage accounts/fileServices/schrijven |  |
> | Actie | Microsoft.Storage/storageAccounts/listAccountSas/action | Hiermee wordt het SAS-token van het account voor het opgegeven opslag account geretourneerd. |
> | Actie | Microsoft.Storage/storageAccounts/listkeys/action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Actie | Microsoft.Storage/storageAccounts/listServiceSas/action | Hiermee wordt het SAS-token van de service voor het opgegeven opslag account geretourneerd. |
> | Actie | Microsoft.Storage/storageAccounts/managementPolicies/delete | Beheer beleid voor opslag accounts verwijderen |
> | Actie | Microsoft.Storage/storageAccounts/managementPolicies/read | Beleid voor opslag beheer accounts ophalen |
> | Actie | Microsoft.Storage/storageAccounts/managementPolicies/write | Opslag account beheer beleid plaatsen |
> | Actie | Micro soft. Storage/Storage accounts/objectReplicationPolicies/verwijderen |  |
> | Actie | Micro soft. Storage/Storage accounts/objectReplicationPolicies/lezen |  |
> | Actie | Micro soft. Storage/Storage accounts/objectReplicationPolicies/schrijven |  |
> | Actie | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Particuliere endpoint-verbindings Proxy's verwijderen |
> | Actie | Micro soft. Storage/Storage accounts/privateEndpointConnectionProxies/lezen | Proxy voor verbinding met privé-eind punt ophalen |
> | Actie | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Connect-Proxy's voor privé-eind punt plaatsen |
> | Actie | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Verbinding voor privé-eind punt verwijderen |
> | Actie | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Verbinding voor privé-eind punt ophalen |
> | Actie | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Verbinding voor privé-eind punt plaatsen |
> | Actie | Micro soft. Storage/Storage accounts/PrivateEndpointConnectionsApproval/Action | Privé-eindpunt verbindingen goed keuren |
> | Actie | Microsoft.Storage/storageAccounts/privateLinkResources/read | Storage account groupids ophalen |
> | Actie | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Retourneert het resultaat van het verwijderen van een wachtrij |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Retourneert het resultaat van het toevoegen van een bericht |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Retourneert het resultaat van het verwijderen van een bericht |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Retourneert het resultaat van de verwerking van een bericht |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Retourneert een bericht |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Hiermee wordt het resultaat van het schrijven van een bericht geretourneerd |
> | Actie | Microsoft.Storage/storageAccounts/queueServices/queues/read | Hiermee wordt een wachtrij of een lijst met wacht rijen geretourneerd. |
> | Actie | Microsoft.Storage/storageAccounts/queueServices/queues/write | Retourneert het resultaat van het schrijven van een wachtrij |
> | Actie | Microsoft.Storage/storageAccounts/queueServices/read | Queue-service eigenschappen ophalen |
> | Actie | Microsoft.Storage/storageAccounts/queueServices/read | Retourneert eigenschappen of statistieken van de wachtrij service. |
> | Actie | Microsoft.Storage/storageAccounts/queueServices/write | Retourneert het resultaat van het instellen van de eigenschappen van de wachtrij service |
> | Actie | Microsoft.Storage/storageAccounts/read | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Actie | Microsoft.Storage/storageAccounts/regeneratekey/action | Hiermee worden de toegangs sleutels voor het opgegeven opslag account opnieuw gegenereerd. |
> | Actie | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | BLOB-bereiken herstellen naar de status van de opgegeven tijd |
> | Actie | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Hiermee worden alle sleutels voor gebruikers overdracht voor het opgegeven opslag account ingetrokken. |
> | Actie | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Diagnostische instellingen van opslag account maken/bijwerken. |
> | Actie | Microsoft.Storage/storageAccounts/tableServices/read | Table service eigenschappen ophalen |
> | Actie | Microsoft.Storage/storageAccounts/write | Hiermee maakt u een opslag account met de opgegeven para meters of werkt u de eigenschappen of labels bij of voegt u een aangepast domein toe voor het opgegeven opslag account. |
> | Actie | Microsoft.Storage/usages/read | Retourneert de limiet en het huidige gebruiks aantal voor resources in het opgegeven abonnement |

## <a name="microsoftstoragesync"></a>micro soft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | microsoft.storagesync/locations/checkNameAvailability/action | Controleert of de naam van de opslag synchronisatie service geldig is en niet wordt gebruikt. |
> | Actie | micro soft. storagesync/locaties/werk stromen/bewerkingen/lezen | Hiermee wordt de status van een asynchrone bewerking opgehaald |
> | Actie | micro soft. storagesync/Operations/lezen | Hiermee wordt een lijst met ondersteunde bewerkingen opgehaald |
> | Actie | micro soft. storagesync/REGI ster/actie | Hiermee wordt het abonnement voor de opslag synchronisatie provider geregistreerd |
> | Actie | microsoft.storagesync/storageSyncServices/delete | Opslag synchronisatie Services verwijderen |
> | Actie | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor opslag synchronisatie Services |
> | Actie | microsoft.storagesync/storageSyncServices/read | Alle opslag synchronisatie services lezen |
> | Actie | microsoft.storagesync/storageSyncServices/registeredServers/delete | Alle geregistreerde servers verwijderen |
> | Actie | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor de geregistreerde server |
> | Actie | microsoft.storagesync/storageSyncServices/registeredServers/read | Een geregistreerde server lezen |
> | Actie | microsoft.storagesync/storageSyncServices/registeredServers/write | Een geregistreerde server maken of bijwerken |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Alle Cloud eindpunten verwijderen |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Hiermee wordt de status van een asynchrone back-up-of herstel bewerking opgehaald |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Deze actie aanroepen na een back-up |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Deze actie aanroepen na het herstellen |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Deze actie aanroepen voordat u een back-up maakt |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Deze actie aanroepen voordat deze wordt hersteld |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Alle Cloud eindpunten lezen |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Heartbeat herstellen |
> | Actie | micro soft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/actie | Deze actie aanroepen om de detectie van wijzigingen op de bestands share van een Cloud eindpunt te activeren |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Alle Cloud eindpunten maken of bijwerken |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/delete | Alle synchronisatie groepen verwijderen |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor synchronisatie groepen |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/read | Alle synchronisatie groepen lezen |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Server eindpunten verwijderen |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor Server-eind punten |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Server eindpunten lezen |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Deze actie aanroepen om bestanden op een server op te halen |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Server eindpunten maken of bijwerken |
> | Actie | microsoft.storagesync/storageSyncServices/syncGroups/write | Synchronisatie groepen maken of bijwerken |
> | Actie | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Hiermee wordt de status van een asynchrone bewerking opgehaald |
> | Actie | microsoft.storagesync/storageSyncServices/workflows/operations/read | Hiermee wordt de status van een asynchrone bewerking opgehaald |
> | Actie | microsoft.storagesync/storageSyncServices/workflows/read | Werk stromen lezen |
> | Actie | microsoft.storagesync/storageSyncServices/write | Opslag synchronisatie Services maken of bijwerken |
> | Actie | micro soft. storagesync/registratie/actie | Hiermee wordt de registratie van het abonnement voor de opslag synchronisatie provider ongedaan gemaakt |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.StorSimple/managers/accessControlRecords/delete | Hiermee verwijdert u de Access Control records |
> | Actie | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | De bewerkings resultaten weer geven of ophalen |
> | Actie | Microsoft.StorSimple/managers/accessControlRecords/read | De Access Control records weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/accessControlRecords/schrijven | De Access Control records maken of bijwerken |
> | Actie | Micro soft. StorSimple/managers/waarschuwingen/lezen | De waarschuwingen weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/back-ups/lezen | De back-upset weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/bandwidthSettings/verwijderen | Hiermee verwijdert u een bestaande band breedte-instelling (alleen 8000 Series) |
> | Actie | Micro soft. StorSimple/managers/bandwidthSettings/operationResults/lezen | De bewerkings resultaten weer geven |
> | Actie | Micro soft. StorSimple/managers/bandwidthSettings/lezen | De band breedte-instellingen weer geven (alleen 8000 Series) |
> | Actie | Micro soft. StorSimple/managers/bandwidthSettings/schrijven | Hiermee maakt u een nieuwe of bijgewerkte band breedte-instellingen (alleen 8000 Series) |
> | Actie | Micro soft. StorSimple/managers/certificaten/schrijven | De certificaten maken of bijwerken |
> | Actie | Micro soft. StorSimple/managers/certificaten/schrijven | Met de bewerking resource certificaat bijwerken wordt het resource/kluis-referentie certificaat bijgewerkt. |
> | Actie | Micro soft. StorSimple/managers/clearAlerts/actie | Wis alle waarschuwingen die zijn gekoppeld aan Apparaatbeheer. |
> | Actie | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | De ondersteunde configuraties van het Cloud apparaat weer geven |
> | Actie | Micro soft. StorSimple/managers/configureDevice/actie | Hiermee configureert u een apparaat |
> | Actie | Micro soft. StorSimple/managers/verwijderen | Hiermee worden de Apparaatbeheer verwijderd |
> | Actie | Micro soft. StorSimple/managers/verwijderen | Met de bewerking kluis verwijderen wordt de opgegeven Azure-resource van het type kluis verwijderd |
> | Actie | Micro soft. StorSimple/managers/apparaten/alertSettings/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/alertSettings/lezen | De instellingen van de waarschuwing weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/alertSettings/schrijven | De instellingen voor waarschuwingen maken of bijwerken |
> | Actie | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Machtigen voor overschakeling van service-versleutelings sleutel van apparaten |
> | Actie | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Maak een hand matige back-up om een back-up op aanvraag te maken van alle volumes die worden beveiligd door het beleid. |
> | Actie | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Hiermee verwijdert u een bestaande back-upbeleid (alleen 8000 Series) |
> | Actie | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | De bewerkings resultaten weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/backupPolicies/read | Het back-upbeleid (alleen 8000 Series) weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Hiermee worden bestaande Schema's verwijderd |
> | Actie | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | De bewerkings resultaten weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | De planningen weer geven |
> | Actie | Micro soft. StorSimple/managers/apparaten/backupPolicies/planningen/schrijven | Hiermee maakt u een nieuwe of bijgewerkte planningen |
> | Actie | Microsoft.StorSimple/managers/devices/backupPolicies/write | Hiermee maakt u een nieuw of bijgewerkt back-upbeleid (alleen 8000 Series) |
> | Actie | Micro soft. StorSimple/managers/apparaten/back-ups/verwijderen | Hiermee verwijdert u de back-upset |
> | Actie | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Een share of volume klonen met behulp van een back-upelement. |
> | Actie | Micro soft. StorSimple/managers/apparaten/back-ups/elementen/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/back-ups/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/back-ups/lezen | De back-upset weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/back-ups/herstellen/actie | Herstel alle volumes uit een back-upset. |
> | Actie | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Hiermee verwijdert u de back-upschema groepen |
> | Actie | Micro soft. StorSimple/managers/apparaten/backupScheduleGroups/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/backupScheduleGroups/lezen | De back-upschema groepen worden weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/managers/apparaten/backupScheduleGroups/schrijven | De back-upschema groepen maken of bijwerken |
> | Actie | Microsoft.StorSimple/managers/devices/chapSettings/delete | Hiermee worden de CHAP-instellingen verwijderd |
> | Actie | Micro soft. StorSimple/managers/apparaten/chapSettings/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/chapSettings/lezen | De CHAP-instellingen weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/chapSettings/schrijven | De CHAP-instellingen maken of bijwerken |
> | Actie | Microsoft.StorSimple/managers/devices/deactivate/action | Hiermee wordt een apparaat deactiveren. |
> | Actie | Microsoft.StorSimple/managers/devices/delete | Hiermee worden de apparaten verwijderd |
> | Actie | Micro soft. StorSimple/managers/apparaten/schijven/lezen | De schijven weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/downloaden/actie | Updates downloaden voor een apparaat. |
> | Actie | Micro soft. StorSimple/managers/apparaten/failover/actie | Failover van het apparaat. |
> | Actie | Micro soft. StorSimple/managers/apparaten/failover/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/failoverTargets/lezen | De failover-doelen van de apparaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/back-up/actie | Maak een back-up van een bestands server. |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/verwijderen | Hiermee verwijdert u de bestands servers |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/metrieken/lezen | Hiermee worden de metrische gegevens weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/metricsDefinitions/lezen | Hiermee worden de metrische definities weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/lezen | De bestands servers weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/shares/verwijderen | Hiermee worden de shares verwijderd |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/shares/metrieken/lezen | Hiermee worden de metrische gegevens weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/shares/metricsDefinitions/lezen | Hiermee worden de metrische definities weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/shares/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/shares/lezen | De shares weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/shares/schrijven | De shares maken of bijwerken |
> | Actie | Micro soft. StorSimple/managers/apparaten/fileservers/schrijven | Bestands servers maken of bijwerken |
> | Actie | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | De energie status van de controller wijzigen van hardwarecomponenten |
> | Actie | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | De bewerkings resultaten weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | De hardware-onderdeel groepen weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/install/action | Installeer updates op een apparaat. |
> | Actie | Microsoft.StorSimple/managers/devices/installUpdates/action | Installeert updates op de apparaten (alleen 8000 Series). |
> | Actie | Micro soft. StorSimple/managers/apparaten/iscsiservers/back-up/actie | Maak een back-up van een iSCSI-server. |
> | Actie | Micro soft. StorSimple/managers/apparaten/iscsiservers/verwijderen | De iSCSI-servers worden verwijderd |
> | Actie | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | De schijven worden verwijderd |
> | Actie | Micro soft. StorSimple/managers/apparaten/iscsiservers/schijven/metrieken/lezen | Hiermee worden de metrische gegevens weer gegeven of opgehaald |
> | Actie | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Hiermee worden de metrische definities weer gegeven of opgehaald |
> | Actie | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | De bewerkings resultaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/iscsiservers/schijven/lezen | De schijven weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/iscsiservers/schijven/schrijven | De schijven maken of bijwerken |
> | Actie | Micro soft. StorSimple/managers/apparaten/iscsiservers/metrieken/lezen | Hiermee worden de metrische gegevens weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/managers/apparaten/iscsiservers/metricsDefinitions/lezen | Hiermee worden de metrische definities weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/managers/apparaten/iscsiservers/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/iscsiservers/lezen | De iSCSI-servers weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/iscsiservers/schrijven | De iSCSI-servers maken of bijwerken |
> | Actie | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Een actieve taak annuleren |
> | Actie | Micro soft. StorSimple/managers/apparaten/Jobs/operationResults/lezen | De bewerkings resultaten weer geven |
> | Actie | Micro soft. StorSimple/managers/apparaten/taken/lezen | De taken weer geven of ophalen |
> | Actie | Microsoft.StorSimple/managers/devices/listFailoverSets/action | De failover-sets voor een bestaand apparaat weer geven (alleen 8000 Series). |
> | Actie | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Failover-doelen van de apparaten weer geven (alleen 8000 Series). |
> | Actie | Micro soft. StorSimple/managers/apparaten/metrieken/lezen | Hiermee worden de metrische gegevens weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/managers/apparaten/metricsDefinitions/lezen | Hiermee worden de metrische definities weer gegeven of opgehaald |
> | Actie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Hiermee bevestigt u een geslaagde migratie en voert u deze door. |
> | Actie | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/confirmMigrationStatus/lezen | De status van de migratie bevestigen weer geven |
> | Actie | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/fetchConfirmMigrationStatus/actie | De Bevestig status van de migratie ophalen. |
> | Actie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Haal de status van de taak schatting voor migratie op. |
> | Actie | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/fetchMigrationStatus/actie | De status voor de migratie ophalen. |
> | Actie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Bron configuraties voor migratie importeren |
> | Actie | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/migrationEstimate/lezen | De schatting van de migratie weer geven |
> | Actie | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/migrationStatus/lezen | De migratie status weer geven |
> | Actie | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/operationResults/lezen | De bewerkings resultaten weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Migratie starten met behulp van bron configuraties |
> | Actie | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Start een taak om de duur van het migratie proces te schatten. |
> | Actie | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | De bewerkings resultaten weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/networkSettings/read | Hiermee worden de netwerk instellingen weer gegeven of opgehaald |
> | Actie | Microsoft.StorSimple/managers/devices/networkSettings/write | Hiermee maakt u een nieuw of bijgewerkt netwerk instellingen |
> | Actie | Micro soft. StorSimple/managers/apparaten/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Open bare versleutelings sleutel van Apparaatbeheer weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publiceer het ondersteunings pakket voor een bestaand apparaat. Een StorSimple-ondersteunings pakket is een eenvoudig te gebruiken mechanisme dat alle relevante logboeken verzamelt om Microsoft Ondersteuning te helpen bij het oplossen van problemen met StorSimple-apparaten. |
> | Actie | Micro soft. StorSimple/managers/apparaten/lezen | De apparaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/scanForUpdates/actie | Zoeken naar updates op een apparaat. |
> | Actie | Micro soft. StorSimple/managers/apparaten/securitySettings/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/securitySettings/lezen | De beveiligings instellingen weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Het certificaat voor extern beheer voor een apparaat synchroniseren. |
> | Actie | Microsoft.StorSimple/managers/devices/securitySettings/update/action | De beveiligings instellingen bijwerken. |
> | Actie | Micro soft. StorSimple/managers/apparaten/securitySettings/schrijven | Hiermee maakt u een nieuwe of bijgewerkte beveiligings instellingen |
> | Actie | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Verzend een e-mail met een test waarschuwing naar geconfigureerde e-mail ontvangers. |
> | Actie | Micro soft. StorSimple/managers/apparaten/shares/lezen | De shares weer geven of ophalen |
> | Actie | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | De bewerkings resultaten weer geven |
> | Actie | Micro soft. StorSimple/managers/apparaten/timeSettings/lezen | De tijd instellingen weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/apparaten/timeSettings/schrijven | Hiermee maakt u een nieuwe of bijgewerkte tijd instellingen |
> | Actie | Micro soft. StorSimple/managers/apparaten/updates/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Microsoft.StorSimple/managers/devices/updateSummary/read | De samen vatting van de update weer geven of ophalen |
> | Actie | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Hiermee verwijdert u een bestaande volume container (alleen 8000 Series) |
> | Actie | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | De metrische gegevens weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | De definities voor metrische gegevens weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | De bewerkings resultaten weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/volumeContainers/read | De volume containers weer geven (alleen 8000 Series) |
> | Actie | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Hiermee verwijdert u een bestaand volume |
> | Actie | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | De metrische gegevens weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | De definities voor metrische gegevens weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | De bewerkings resultaten weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | De volumes weer geven |
> | Actie | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Hiermee maakt u een nieuw of bijgewerkt volume |
> | Actie | Microsoft.StorSimple/managers/devices/volumeContainers/write | Hiermee maakt u een nieuwe of bijgewerkte volume containers (alleen 8000 Series) |
> | Actie | Microsoft.StorSimple/managers/devices/volumes/read | De volumes weer geven |
> | Actie | Micro soft. StorSimple/managers/apparaten/schrijven | De apparaten maken of bijwerken |
> | Actie | Micro soft. StorSimple/managers/encryptionSettings/lezen | De versleutelings instellingen weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/extendedInformation/verwijderen | Hiermee verwijdert u de uitgebreide kluis gegevens |
> | Actie | Micro soft. StorSimple/managers/extendedInformation/verwijderen | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Actie | Micro soft. StorSimple/managers/extendedInformation/lezen | De uitgebreide kluis gegevens weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/extendedInformation/lezen | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Actie | Micro soft. StorSimple/managers/extendedInformation/schrijven | De uitgebreide kluis gegevens maken of bijwerken |
> | Actie | Micro soft. StorSimple/managers/extendedInformation/schrijven | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Actie | Micro soft. StorSimple/managers/onderdelen/lezen | De functies weer geven |
> | Actie | Micro soft. StorSimple/managers/fileservers/lezen | De bestands servers weer geven of ophalen |
> | Actie | Microsoft.StorSimple/managers/getEncryptionKey/action | De versleutelings sleutel voor Apparaatbeheer ophalen. |
> | Actie | Micro soft. StorSimple/managers/iscsiservers/lezen | De iSCSI-servers weer geven of ophalen |
> | Actie | Micro soft. StorSimple/managers/Jobs/lezen | De taken weer geven of ophalen |
> | Actie | Microsoft.StorSimple/managers/listActivationKey/action | Hiermee wordt de activerings sleutel van de StorSimple-Apparaatbeheer opgehaald. |
> | Actie | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Open bare versleutelings sleutels van een StorSimple-Apparaatbeheer weer geven. |
> | Actie | Micro soft. StorSimple/managers/metrische gegevens/lezen | Hiermee worden de metrische gegevens weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/managers/metricsDefinitions/lezen | Hiermee worden de metrische definities weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/managers/migrateClassicToResourceManager/actie | Migreren van klassiek naar Resource Manager |
> | Actie | Micro soft. StorSimple/managers/migrationSourceConfigurations/lezen | De migratie bron configuraties weer geven (alleen 8000 Series) |
> | Actie | Micro soft. StorSimple/managers/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Actie | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Maak een nieuw Cloud apparaat. |
> | Actie | Micro soft. StorSimple/managers/lezen | Hiermee worden de Apparaatbeheer weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/managers/lezen | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Actie | Microsoft.StorSimple/managers/regenerateActivationKey/action | Genereer de activerings sleutel opnieuw voor een bestaande StorSimple-Apparaatbeheer. |
> | Actie | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Hiermee verwijdert u de referenties van het opslag account |
> | Actie | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | De bewerkings resultaten weer geven of ophalen |
> | Actie | Microsoft.StorSimple/managers/storageAccountCredentials/read | De referenties van het opslag account worden weer gegeven of opgehaald |
> | Actie | Microsoft.StorSimple/managers/storageAccountCredentials/write | De referenties van het opslag account maken of bijwerken |
> | Actie | Microsoft.StorSimple/managers/storageDomains/delete | Hiermee worden de opslag domeinen verwijderd |
> | Actie | Microsoft.StorSimple/managers/storageDomains/operationResults/read | De bewerkings resultaten weer geven of ophalen |
> | Actie | Microsoft.StorSimple/managers/storageDomains/read | De opslag domeinen worden weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/managers/storageDomains/schrijven | De opslag domeinen maken of bijwerken |
> | Actie | Micro soft. StorSimple/managers/schrijven | Apparaatbeheer maken of bijwerken |
> | Actie | Micro soft. StorSimple/managers/schrijven | Met een kluis bewerking maken wordt een Azure-resource van het type ' kluis ' gemaakt. |
> | Actie | Micro soft. StorSimple/Operations/lezen | Hiermee worden de bewerkingen weer gegeven of opgehaald |
> | Actie | Micro soft. StorSimple/REGI ster/actie | Provider micro soft. StorSimple registreren |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.StreamAnalytics/locations/quotas/Read | Stream Analytics abonnements quotum lezen |
> | Actie | Microsoft.StreamAnalytics/operations/Read | Stream Analytics bewerkingen lezen |
> | Actie | Microsoft.StreamAnalytics/Register/action | Een abonnement bij Stream Analytics resource provider registreren |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/Delete | Stream Analytics taak verwijderen |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Stream Analytics taak functie verwijderen |
> | Actie | Micro soft. StreamAnalytics/streamingjobs/functions/operationresults/lezen | Resultaten van de bewerking lezen voor Stream Analytics taak functie |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Stream Analytics taak functie lezen |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | De standaard definitie van een Stream Analytics taak functie ophalen |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Stream Analytics taak functie testen |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Functie Write Stream Analytics-taak |
> | Actie | Micro soft. StreamAnalytics/streamingjobs/invoer/verwijderen | Stream Analytics taak invoer verwijderen |
> | Actie | Micro soft. StreamAnalytics/streamingjobs/invoer/operationresults/lezen | Resultaten van de bewerking lezen voor Stream Analytics taak invoer |
> | Actie | Micro soft. StreamAnalytics/streamingjobs/invoer/lezen | Stream Analytics taak invoer lezen |
> | Actie | Micro soft. StreamAnalytics/streamingjobs/invoer/voor beeld/actie | Voor beeld van Stream Analytics taak invoer |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Stream Analytics taak invoer testen |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Invoer van Stream Analytics-taak schrijven |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Metrische definities lezen |
> | Actie | Micro soft. StreamAnalytics/streamingjobs/operationresults/lezen | Resultaten van de bewerking lezen voor Stream Analytics taak |
> | Actie | Micro soft. StreamAnalytics/streamingjobs/outputs/verwijderen | Stream Analytics taak uitvoer verwijderen |
> | Actie | Micro soft. StreamAnalytics/streamingjobs/outputs/operationresults/lezen | Resultaten van de bewerking lezen voor Stream Analytics taak uitvoer |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Stream Analytics taak uitvoer lezen |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Stream Analytics taak uitvoer testen |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Stream Analytics taak uitvoer schrijven |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Diagnostische instelling lezen. |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Diagnostische instelling schrijven. |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Hiermee worden de beschik bare logboeken voor streamingjobs opgehaald |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Hiermee worden de beschik bare metrische gegevens opgehaald voor streamingjobs |
> | Actie | Micro soft. StreamAnalytics/streamingjobs/PublishEdgePackage/Action | Edge-pakket publiceren voor Stream Analytics taak |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/Read | Stream Analytics taak lezen |
> | Actie | Micro soft. StreamAnalytics/streamingjobs/Scale/Action | Stream Analytics taak schalen |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/Start/action | Stream Analytics-taak starten |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stream Analytics taak stoppen |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Stream Analytics taak transformatie verwijderen |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Stream Analytics taak transformatie lezen |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Trans formatie van Stream Analytics-taak schrijven |
> | Actie | Microsoft.StreamAnalytics/streamingjobs/Write | Stream Analytics-taak schrijven |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Micro soft. abonnement/annuleren/actie | Hiermee wordt het abonnement geannuleerd |
> | Actie | Microsoft.Subscription/CreateSubscription/action | Maak een Azure-abonnement |
> | Actie | Microsoft.Subscription/register/action | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. Subscription |
> | Actie | Micro soft. Subscription/naam wijzigen/actie | De naam van het abonnement wijzigen |
> | Actie | Micro soft. Subscription/SubscriptionDefinitions/lezen | Een definitie van een Azure-abonnement in een beheer groep ophalen. |
> | Actie | Microsoft.Subscription/SubscriptionDefinitions/write | Een definitie van een Azure-abonnement maken |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.Support/register/action | Hiermee wordt het item geregistreerd bij de resourceprovider voor ondersteuning |
> | Actie | Microsoft.Support/supportTickets/read | Hiermee worden gegevens over het ondersteuningsticket opgehaald (inclusief status, ernst, contactgegevens en communicatie) of wordt de lijst met ondersteuningstickets voor de verschillende abonnementen opgehaald. |
> | Actie | Microsoft.Support/supportTickets/write | Hiermee wordt een ondersteuningsticket gemaakt of bijgewerkt. U kunt een ondersteuningsticket maken voor problemen van technische aard of op het gebied van facturering, quota's of beheer van abonnementen. U kunt voor bestaande ondersteuningstickets de ernst, de contactgegevens en de communicatiemethoden aanpassen. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Hiermee verwijdert u het toegangs beleid. |
> | Actie | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | De eigenschappen van een toegangs beleid ophalen. |
> | Actie | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Hiermee maakt u een nieuw toegangs beleid voor een omgeving of werkt u een bestaand toegangs beleid bij. |
> | Actie | Microsoft.TimeSeriesInsights/environments/delete | Hiermee verwijdert u de omgeving. |
> | Actie | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Hiermee verwijdert u de bron van de gebeurtenis. |
> | Actie | Microsoft.TimeSeriesInsights/environments/eventsources/read | De eigenschappen van een gebeurtenis bron ophalen. |
> | Actie | Microsoft.TimeSeriesInsights/environments/eventsources/write | Hiermee maakt u een nieuwe gebeurtenis bron voor een omgeving of werkt u een bestaande gebeurtenis bron bij. |
> | Actie | Microsoft.TimeSeriesInsights/environments/read | De eigenschappen van een omgeving ophalen. |
> | Actie | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Hiermee verwijdert u de set met referentie gegevens. |
> | Actie | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | De eigenschappen van een referentie gegevensset ophalen. |
> | Actie | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Hiermee maakt u een nieuwe referentie gegevensverzameling voor een omgeving of werkt u een bestaande referentie gegevensset bij. |
> | Actie | Microsoft.TimeSeriesInsights/environments/status/read | Haal de status van de omgeving op, evenals de status van de bijbehorende bewerkingen als ingangs activiteiten. |
> | Actie | Microsoft.TimeSeriesInsights/environments/write | Hiermee maakt u een nieuwe omgeving of werkt u een bestaande omgeving bij. |
> | Actie | Microsoft.TimeSeriesInsights/register/action | Hiermee wordt het abonnement voor de Time Series Insights resource provider geregistreerd en wordt het maken van Time Series Insights-omgevingen mogelijk gemaakt. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.VisualStudio/Account/Delete | Account verwijderen |
> | Actie | Microsoft.VisualStudio/Account/Extension/Read | Account/extensie lezen |
> | Actie | Microsoft.VisualStudio/Account/Project/Read | Account/project lezen |
> | Actie | Microsoft.VisualStudio/Account/Project/Write | Account/project instellen |
> | Actie | Microsoft.VisualStudio/Account/Read | Account lezen |
> | Actie | Microsoft.VisualStudio/Account/Write | Account instellen |
> | Actie | Microsoft.VisualStudio/Extension/Delete | Extensie verwijderen |
> | Actie | Microsoft.VisualStudio/Extension/Read | Uitbrei ding lezen |
> | Actie | Microsoft.VisualStudio/Extension/Write | Extensie instellen |
> | Actie | Microsoft.VisualStudio/Project/Delete | Project verwijderen |
> | Actie | Microsoft.VisualStudio/Project/Read | Project lezen |
> | Actie | Microsoft.VisualStudio/Project/Write | Project instellen |
> | Actie | Microsoft.VisualStudio/Register/Action | Azure-abonnement registreren bij micro soft. Visual Studio-provider |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | microsoft.web/apimanagementaccounts/apiacls/read | Apiacls van API management-accounts ophalen. |
> | Actie | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Api's voor API management-accounts verwijderen Apiacls. |
> | Actie | microsoft.web/apimanagementaccounts/apis/apiacls/read | Api's voor API management-accounts ophalen Apiacls. |
> | Actie | microsoft.web/apimanagementaccounts/apis/apiacls/write | Api's voor API management-accounts bijwerken Apiacls. |
> | Actie | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Api's voor API management-accounts ophalen Connectionacls. |
> | Actie | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Bevestig de toestemming code API management accounts Api's Connections. |
> | Actie | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | API-beheer accounts verwijderen Api's-verbindingen Connectionacls. |
> | Actie | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Get API management accounts Api's Connections Connectionacls. |
> | Actie | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Update API management accounts Api's Connections Connectionacls. |
> | Actie | microsoft.web/apimanagementaccounts/apis/connections/delete | Api's-verbindingen van API management-accounts verwijderen. |
> | Actie | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Vraag koppelingen naar toestemming voor API management-accounts Api's-verbindingen op. |
> | Actie | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Lijst met verbindings sleutels API-beheer accounts Api's-verbindingen. |
> | Actie | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Lijst met geheimen API management accounts Api's-verbindingen. |
> | Actie | microsoft.web/apimanagementaccounts/apis/connections/read | Api's-verbindingen van API management-accounts ophalen. |
> | Actie | microsoft.web/apimanagementaccounts/apis/connections/write | Api's-verbindingen van API management-accounts bijwerken. |
> | Actie | microsoft.web/apimanagementaccounts/apis/delete | Api's voor API management-accounts verwijderen. |
> | Actie | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | API management-accounts verwijderen Api's gelokaliseerde definities. |
> | Actie | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | API management accounts Api's gelokaliseerde definities ophalen. |
> | Actie | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Update API management accounts Api's gelokaliseerde definities. |
> | Actie | microsoft.web/apimanagementaccounts/apis/read | Api's voor API management-accounts ophalen. |
> | Actie | microsoft.web/apimanagementaccounts/apis/write | Api's voor API management-accounts bijwerken. |
> | Actie | microsoft.web/apimanagementaccounts/connectionacls/read | Connectionacls van API management-accounts ophalen. |
> | Actie | microsoft.web/availablestacks/read | Beschik bare stacks ophalen. |
> | Actie | Micro soft. web/certificaten/verwijderen | Een bestaand certificaat verwijderen. |
> | Actie | Microsoft.Web/certificates/Read | De lijst met certificaten ophalen. |
> | Actie | Microsoft.Web/certificates/Write | Een nieuw certificaat toevoegen of een bestaande bijwerken. |
> | Actie | micro soft. Web/checknameavailability/lezen | Controleer of de resource naam beschikbaar is. |
> | Actie | microsoft.web/classicmobileservices/read | Klassieke Mobile Services ophalen. |
> | Actie | Microsoft.Web/connectionGateways/Delete | Hiermee verwijdert u een verbindings gateway. |
> | Actie | Microsoft.Web/connectionGateways/Join/Action | Er wordt lid van een verbindings gateway. |
> | Actie | Microsoft.Web/connectionGateways/ListStatus/Action | Hiermee wordt de status van een verbindings gateway weer gegeven. |
> | Actie | Microsoft.Web/connectionGateways/Move/Action | Hiermee verplaatst u een verbindings gateway. |
> | Actie | Microsoft.Web/connectionGateways/Read | De lijst met verbindings gateways ophalen. |
> | Actie | Microsoft.Web/connectionGateways/Write | Hiermee wordt een verbindings gateway gemaakt of bijgewerkt. |
> | Actie | microsoft.web/connections/confirmconsentcode/action | Bevestig de code voor de toestemming van de verbinding. |
> | Actie | Microsoft.Web/connections/Delete | Hiermee verwijdert u een verbinding. |
> | Actie | Microsoft.Web/connections/Join/Action | Er wordt een verbinding toegevoegd. |
> | Actie | microsoft.web/connections/listconsentlinks/action | Lijst met toestemmings koppelingen voor verbindingen. |
> | Actie | Microsoft.Web/connections/Move/Action | Hiermee verplaatst u een verbinding. |
> | Actie | Microsoft.Web/connections/Read | De lijst met verbindingen ophalen. |
> | Actie | Microsoft.Web/connections/Write | Hiermee wordt een verbinding gemaakt of bijgewerkt. |
> | Actie | Microsoft.Web/customApis/Delete | Hiermee verwijdert u een aangepaste API. |
> | Actie | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Hiermee wordt de API-definitie geëxtraheerd uit een WSDL. |
> | Actie | Microsoft.Web/customApis/Join/Action | Er wordt verbinding gemaakt met een aangepaste API. |
> | Actie | Microsoft.Web/customApis/listWsdlInterfaces/Action | Geeft een lijst van de WSDL-interfaces voor een aangepaste API. |
> | Actie | Microsoft.Web/customApis/Move/Action | Hiermee verplaatst u een aangepaste API. |
> | Actie | Microsoft.Web/customApis/Read | De lijst met aangepaste API ophalen. |
> | Actie | Microsoft.Web/customApis/Write | Hiermee wordt een aangepaste API gemaakt of bijgewerkt. |
> | Actie | Microsoft.Web/deletedSites/Read | De eigenschappen van een verwijderde web-app ophalen |
> | Actie | microsoft.web/deploymentlocations/read | Implementatie locaties ophalen. |
> | Actie | Microsoft.Web/geoRegions/Read | De lijst met geo-regio's ophalen. |
> | Actie | micro soft. Web/hostingenvironments/capaciteiten/lezen | De capaciteit van hosting omgevingen ophalen. |
> | Actie | Micro soft. Web/hostingEnvironments/verwijderen | Een App Service Environment verwijderen |
> | Actie | microsoft.web/hostingenvironments/detectors/read | Detecties voor hosting omgevingen ophalen. |
> | Actie | micro soft. Web/hostingenvironments/diagnostiek/lezen | Diagnostische gegevens over hosting omgevingen ophalen. |
> | Actie | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | De netwerk eindpunten van alle inkomende afhankelijkheden ophalen. |
> | Actie | Micro soft. Web/hostingEnvironments/samen voegen/actie | Voegt een App Service Environment |
> | Actie | microsoft.web/hostingenvironments/metricdefinitions/read | Metrische definities voor hosting omgevingen ophalen. |
> | Actie | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Host-omgevingen met Multirole-groepen ophalen metrische definities. |
> | Actie | microsoft.web/hostingenvironments/multirolepools/metrics/read | De metrische gegevens van de Pools voor hosting omgevingen ophalen. |
> | Actie | Microsoft.Web/hostingEnvironments/multiRolePools/Read | De eigenschappen van een front-end groep ophalen in een App Service Environment |
> | Actie | microsoft.web/hostingenvironments/multirolepools/skus/read | Haal de Sku's van de Multirole-Pools voor hosting omgevingen op. |
> | Actie | microsoft.web/hostingenvironments/multirolepools/usages/read | Haal hosting omgevingen op met het gebruik van Multirole-Pools. |
> | Actie | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Een nieuwe front-endwebserver maken in een App Service Environment of een bestaande groep bijwerken |
> | Actie | microsoft.web/hostingenvironments/operations/read | Bewerkingen voor hosting omgevingen ophalen. |
> | Actie | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | De netwerk eindpunten van alle uitgaande afhankelijkheden ophalen. |
> | Actie | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Privé-eindpunt verbindingen goed keuren |
> | Actie | Micro soft. Web/hostingEnvironments/lezen | De eigenschappen van een App Service Environment ophalen |
> | Actie | Microsoft.Web/hostingEnvironments/reboot/Action | Alle machines in een App Service Environment opnieuw opstarten |
> | Actie | microsoft.web/hostingenvironments/resume/action | Hosting omgevingen hervatten. |
> | Actie | micro soft. Web/hostingenvironments/server farms/lezen | Haal hosting omgevingen op App Service plannen. |
> | Actie | microsoft.web/hostingenvironments/sites/read | Haal Web Apps hosting omgevingen op. |
> | Actie | microsoft.web/hostingenvironments/suspend/action | Hosting omgevingen blok keren. |
> | Actie | microsoft.web/hostingenvironments/usages/read | Het gebruik van hosting omgevingen ophalen. |
> | Actie | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Workerpools metrische definities voor hosting omgevingen ophalen. |
> | Actie | microsoft.web/hostingenvironments/workerpools/metrics/read | Haal Workerpools-metrische gegevens voor hosting omgevingen op. |
> | Actie | Microsoft.Web/hostingEnvironments/workerPools/Read | De eigenschappen van een werk groep in een App Service Environment ophalen |
> | Actie | microsoft.web/hostingenvironments/workerpools/skus/read | Haal hosting omgevingen Workerpools Sku's op. |
> | Actie | microsoft.web/hostingenvironments/workerpools/usages/read | Haal Workerpools-gebruik op in hosting omgevingen. |
> | Actie | Microsoft.Web/hostingEnvironments/workerPools/Write | Een nieuwe werk groep maken in een App Service Environment of een bestaande pool bijwerken |
> | Actie | Micro soft. Web/hostingEnvironments/schrijven | Een nieuwe App Service Environment maken of bestaande bijwerken |
> | Actie | micro soft. Web/ishostingenvironmentnameavailable/lezen | Ophalen als de hosting omgevings naam beschikbaar is. |
> | Actie | micro soft. Web/ishostnameavailable/lezen | Controleer of de hostnaam beschikbaar is. |
> | Actie | micro soft. Web/isusernameavailable/lezen | Controleer of de gebruikers naam beschikbaar is. |
> | Actie | Microsoft.Web/listSitesAssignedToHostName/Read | Namen ophalen van sites die zijn toegewezen aan de hostnaam. |
> | Actie | microsoft.web/locations/apioperations/read | Get locations API Operations. |
> | Actie | microsoft.web/locations/connectiongatewayinstallations/read | Get locations Connection gateway Installations. |
> | Actie | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Melding over het verwijderen van Vnet of subnet voor locaties. |
> | Actie | microsoft.web/locations/extractapidefinitionfromwsdl/action | De API-definitie van WSDL voor locaties ophalen. |
> | Actie | microsoft.web/locations/listwsdlinterfaces/action | Lijst van WSDL-interfaces voor locaties. |
> | Actie | microsoft.web/locations/managedapis/apioperations/read | Ophalen van locaties Managed API-bewerkingen. |
> | Actie | Micro soft. Web/locaties/beheerdeapi's/samen voegen/actie | Voegt een beheerde API samen. |
> | Actie | microsoft.web/locations/managedapis/read | Beheerde Api's voor locaties ophalen. |
> | Actie | micro soft. Web/locaties/operationResults/lezen | Get-bewerkingen. |
> | Actie | micro soft. Web/locaties/bewerkingen/lezen | Get-bewerkingen. |
> | Actie | microsoft.web/operations/read | Get-bewerkingen. |
> | Actie | microsoft.web/publishingusers/read | Publiceer gebruikers. |
> | Actie | microsoft.web/publishingusers/write | Publicatie gebruikers bijwerken. |
> | Actie | Micro soft. Web/aanbevelingen/lezen | De lijst met aanbevelingen voor abonnementen ophalen. |
> | Actie | microsoft.web/register/action | Registreer de resource provider van micro soft. web voor het abonnement. |
> | Actie | microsoft.web/resourcehealthmetadata/read | Resource Health meta gegevens ophalen. |
> | Actie | micro soft. web/server farms/capabilities/lezen | Profiteer van de mogelijkheden van App Service-abonnementen. |
> | Actie | Microsoft.Web/serverfarms/Delete | Een bestaand App Service plan verwijderen |
> | Actie | Micro soft. web/server farms/eventGridFilters/verwijderen | Verwijder Event Grid filter op de server farm. |
> | Actie | Micro soft. web/server farms/eventGridFilters/lezen | Event Grid filter op de server farm ophalen. |
> | Actie | Micro soft. web/server farms/eventGridFilters/schrijven | Plaats Event Grid filter op de server farm. |
> | Actie | micro soft. web/server farms/firstpartyapps/instellingen/verwijderen | De instellingen van de app voor de eerste partij verwijderen App Service plannen. |
> | Actie | micro soft. web/server farms/firstpartyapps/Settings/lezen | Ontvang App Service-abonnementen voor de eerste partij-apps. |
> | Actie | micro soft. web/server farms/firstpartyapps/Settings/write | De instellingen van de app voor de eerste partij bijwerken App Service plannen. |
> | Actie | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | App Service-abonnementen van hybride verbindings ruimten voor het verwijderen worden door gegeven. |
> | Actie | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Get App Service-abonnementen hybride verbindings naam ruimten relays. |
> | Actie | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Get App Service-abonnementen hybride verbindings naam ruimten relays Web Apps. |
> | Actie | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Ontvang de limieten voor hybride verbindings plannen voor App Service abonnementen. |
> | Actie | microsoft.web/serverfarms/hybridconnectionrelays/read | Ontvang App Service abonnementen hybride verbindings relays. |
> | Actie | microsoft.web/serverfarms/metricdefinitions/read | Definities van metrische gegevens voor App Service plannen ophalen. |
> | Actie | microsoft.web/serverfarms/metrics/read | Statistieken over het App Service plan ophalen. |
> | Actie | micro soft. web/server farms/operationresults/lezen | Resultaten van de bewerking App Service plannen ophalen. |
> | Actie | Microsoft.Web/serverfarms/Read | De eigenschappen van een App Service plan ophalen |
> | Actie | Microsoft.Web/serverfarms/restartSites/Action | Alle Web Apps opnieuw opstarten in een App Service plan |
> | Actie | microsoft.web/serverfarms/sites/read | Ontvang App Service plannen Web Apps. |
> | Actie | microsoft.web/serverfarms/skus/read | Down load de Sku's van App Service-abonnementen. |
> | Actie | microsoft.web/serverfarms/usages/read | Maak gebruik van App Service plannen. |
> | Actie | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Werk App Service plannen Virtual Network verbindingen gateways. |
> | Actie | microsoft.web/serverfarms/virtualnetworkconnections/read | App Service plannen Virtual Network verbindingen ophalen. |
> | Actie | micro soft. web/server farms/virtualnetworkconnections/routes/verwijderen | App Service plannen Virtual Network verbindings routes verwijderen. |
> | Actie | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | App Service plannen Virtual Network verbindings routes ophalen. |
> | Actie | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | App Service plannen Virtual Network verbindings routes bijwerken. |
> | Actie | microsoft.web/serverfarms/workers/reboot/action | Start App Service plannen werk nemers. |
> | Actie | Microsoft.Web/serverfarms/Write | Een nieuw App Service plan maken of een bestaand abonnement bijwerken |
> | Actie | microsoft.web/sites/analyzecustomhostname/read | Aangepaste hostnaam analyseren. |
> | Actie | Microsoft.Web/sites/applySlotConfig/Action | De sleuf configuratie van de web-app Toep assen vanaf de doel site tot de huidige web-app |
> | Actie | Microsoft.Web/sites/backup/Action | Een nieuwe web-app-back-up maken |
> | Actie | microsoft.web/sites/backup/read | Web Apps back-up ophalen. |
> | Actie | microsoft.web/sites/backup/write | Web Apps back-up bijwerken. |
> | Actie | microsoft.web/sites/backups/action | Hiermee wordt een bestaande app-back-up gedetecteerd die kan worden hersteld vanuit een BLOB in azure Storage. |
> | Actie | microsoft.web/sites/backups/delete | Verwijder Web Apps back-ups. |
> | Actie | microsoft.web/sites/backups/list/action | Lijst Web Apps back-ups. |
> | Actie | Microsoft.Web/sites/backups/Read | De eigenschappen van de back-up van een web-app ophalen |
> | Actie | microsoft.web/sites/backups/restore/action | Herstel Web Apps back-ups. |
> | Actie | microsoft.web/sites/backups/write | Web Apps back-ups bijwerken. |
> | Actie | microsoft.web/sites/config/delete | Web Apps configuratie verwijderen. |
> | Actie | Microsoft.Web/sites/config/list/Action | De gevoelige beveiligings instellingen van de web-app weer geven, zoals publicatie referenties, app-instellingen en verbindings reeksen |
> | Actie | Microsoft.Web/sites/config/Read | Configuratie-instellingen voor web-apps ophalen |
> | Actie | microsoft.web/sites/config/snapshots/read | Web Apps-configuratie momentopnamen ophalen. |
> | Actie | Microsoft.Web/sites/config/Write | De configuratie-instellingen van de web-app bijwerken |
> | Actie | microsoft.web/sites/containerlogs/action | Container-logboeken ophalen voor web-app. |
> | Actie | micro soft. web/sites/containerlogs/downloaden/actie | Down load Web Apps container Logboeken. |
> | Actie | microsoft.web/sites/continuouswebjobs/delete | Verwijder Web Apps doorlopende webtaken. |
> | Actie | microsoft.web/sites/continuouswebjobs/read | Ontvang Web Apps doorlopende webtaken. |
> | Actie | microsoft.web/sites/continuouswebjobs/start/action | Start Web Apps doorlopende webtaken. |
> | Actie | microsoft.web/sites/continuouswebjobs/stop/action | Stop Web Apps doorlopende webtaken. |
> | Actie | Microsoft.Web/sites/Delete | Een bestaande web-app verwijderen |
> | Actie | microsoft.web/sites/deployments/delete | Web Apps-implementaties verwijderen. |
> | Actie | microsoft.web/sites/deployments/log/read | Web Apps-implementaties logboek ophalen. |
> | Actie | microsoft.web/sites/deployments/read | Web Apps-implementaties ophalen. |
> | Actie | microsoft.web/sites/deployments/write | Web Apps-implementaties bijwerken. |
> | Actie | microsoft.web/sites/detectors/read | Web Apps detectors ophalen. |
> | Actie | microsoft.web/sites/diagnostics/analyses/execute/Action | Voer Web Apps diagnostische gegevens over de analyse uit. |
> | Actie | microsoft.web/sites/diagnostics/analyses/read | Down load de analyse van Web Apps diagnose. |
> | Actie | microsoft.web/sites/diagnostics/aspnetcore/read | Web Apps diagnostische gegevens voor ASP.NET Core-app ophalen. |
> | Actie | microsoft.web/sites/diagnostics/autoheal/read | Ontvang Web Apps diagnose automatisch herstellen. |
> | Actie | micro soft. web/sites/diagnostische gegevens/implementatie/lezen | Down load de implementatie van Web Apps diagnose. |
> | Actie | microsoft.web/sites/diagnostics/deployments/read | Web Apps diagnostische implementaties ophalen. |
> | Actie | microsoft.web/sites/diagnostics/detectors/execute/Action | Voer Web Apps diagnostische gegevens detectie uit. |
> | Actie | microsoft.web/sites/diagnostics/detectors/read | Down load de detectie van Web Apps diagnose. |
> | Actie | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Ophalen van mislukte aanvragen voor Web Apps diagnostische gegevens per URI. |
> | Actie | micro soft. web/sites/diagnostiek/frebanalysis/lezen | Web Apps diagnostische FREB-analyse ophalen. |
> | Actie | microsoft.web/sites/diagnostics/loganalyzer/read | Lees de logboek analyse van Web Apps Diagnostics. |
> | Actie | micro soft. web/sites/diagnostiek/lezen | Web Apps diagnostische categorieën ophalen. |
> | Actie | microsoft.web/sites/diagnostics/runtimeavailability/read | Down load de beschik baarheid van Web Apps diagnostische gegevens. |
> | Actie | microsoft.web/sites/diagnostics/servicehealth/read | Web Apps diagnostische Service Health ophalen. |
> | Actie | micro soft. web/sites/diagnostiek/sitecpuanalysis/lezen | Web Apps diagnostische gegevens van de site CPU ophalen. |
> | Actie | microsoft.web/sites/diagnostics/sitecrashes/read | Ontvang Web Apps diagnostische gegevens van de site. |
> | Actie | microsoft.web/sites/diagnostics/sitelatency/read | Site latentie van Web Apps diagnostische gegevens ophalen. |
> | Actie | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Down load de analyse van de site geheugen van Web Apps diagnose. |
> | Actie | micro soft. web/sites/diagnostiek/siterestartsettingupdate/lezen | Web Apps diagnostische gegevens van de site ophalen update opnieuw instellen. |
> | Actie | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Web Apps diagnostische gegevens van de site voor het opnieuw starten van de gebruiker ophalen. |
> | Actie | micro soft. web/sites/diagnostiek/siteswap/lezen | Ontvang Web Apps diagnostische gegevens van site swap. |
> | Actie | micro soft. web/sites/diagnostiek/threadcount/lezen | Web Apps diagnostische-thread aantal ophalen. |
> | Actie | microsoft.web/sites/diagnostics/workeravailability/read | Web Apps diagnostische Workeravailability-gegevens ophalen. |
> | Actie | micro soft. web/sites/diagnostiek/workerprocessrecycle/lezen | Web Apps diagnostische gegevens van werk processen ophalen. |
> | Actie | microsoft.web/sites/domainownershipidentifiers/read | Web Apps eigendoms-Id's van het domein ophalen. |
> | Actie | microsoft.web/sites/domainownershipidentifiers/write | De eigendoms-Id's van Web Apps domein bijwerken. |
> | Actie | Micro soft. web/sites/eventGridFilters/verwijderen | Event Grid filter verwijderen voor web-app. |
> | Actie | Micro soft. web/sites/eventGridFilters/lezen | Event Grid filter voor web-app ophalen. |
> | Actie | Micro soft. web/sites/eventGridFilters/schrijven | Event Grid filteren op Web-app. |
> | Actie | microsoft.web/sites/extensions/delete | Verwijder Web Apps site-extensies. |
> | Actie | microsoft.web/sites/extensions/read | Web Apps site-extensies ophalen. |
> | Actie | microsoft.web/sites/extensions/write | Web Apps site-extensies bijwerken. |
> | Actie | microsoft.web/sites/functions/action | Functions Web Apps. |
> | Actie | micro soft. web/sites/functies/verwijderen | Web Apps functies verwijderen. |
> | Actie | micro soft. web/sites/functies/sleutels/verwijderen | Functie sleutels verwijderen. |
> | Actie | micro soft. web/sites/functies/sleutels/schrijven | Functie sleutels bijwerken. |
> | Actie | micro soft. web/sites/functies/listkeys ophalen/actie | Functie sleutels weer geven. |
> | Actie | microsoft.web/sites/functions/listsecrets/action | Lijst met functie geheimen. |
> | Actie | microsoft.web/sites/functions/masterkey/read | Web Apps functies hoofd sleutel ophalen. |
> | Actie | micro soft. web/sites/functies/eigenschappen/lezen | Eigenschappen van Web Apps functies lezen. |
> | Actie | micro soft. web/sites/functies/eigenschappen/schrijven | Eigenschappen van Web Apps functies bijwerken. |
> | Actie | micro soft. web/sites/functies/lezen | Web Apps-functies ophalen. |
> | Actie | micro soft. web/sites/functies/-token/lezen | Web Apps functions-Token ophalen. |
> | Actie | microsoft.web/sites/functions/write | Web Apps-functies bijwerken. |
> | Actie | micro soft. web/sites/host/functionkeys/verwijderen | Functie sleutels host-functies verwijderen. |
> | Actie | micro soft. web/sites/host/functionkeys/schrijven | Functie sleutels van host-functies bijwerken. |
> | Actie | micro soft. web/sites/host/listkeys ophalen/actie | Host-sleutels van functies weer geven. |
> | Actie | micro soft. web/sites/host/listsyncstatus/actie | Status van triggers voor synchronisatie functie voor lijst. |
> | Actie | micro soft. web/sites/host/eigenschappen/lezen | Eigenschappen van de host van Web Apps functies lezen. |
> | Actie | micro soft. web/sites/host/synchroniseren/actie | Synchronisatie functie Triggers. |
> | Actie | micro soft. web/sites/host/systemkeys/verwijderen | Functies verwijderen host systeem sleutels. |
> | Actie | micro soft. web/sites/host/systemkeys/schrijven | Update functies host systeem sleutels. |
> | Actie | micro soft. web/sites/hostnamebindings/verwijderen | Web Apps hostnaam-bindingen verwijderen. |
> | Actie | micro soft. web/sites/hostnamebindings/lezen | Web Apps hostnaam-bindingen ophalen. |
> | Actie | micro soft. web/sites/hostnamebindings/schrijven | Web Apps hostnaam-bindingen bijwerken. |
> | Actie | microsoft.web/sites/hostruntime/functions/keys/read | Web Apps sleutels voor Hostruntime-functies ophalen. |
> | Actie | Microsoft.Web/sites/hostruntime/host/_master/read | De hoofd sleutel van functie-app voor beheer bewerkingen ophalen |
> | Actie | Microsoft.Web/sites/hostruntime/host/action | Voer functie-app runtime-actie uit zoals synchronisatie triggers, functies toevoegen, functies aanroepen, functies verwijderen, enzovoort. |
> | Actie | microsoft.web/sites/hostruntime/host/read | Web Apps Hostruntime-host ophalen. |
> | Actie | microsoft.web/sites/hybridconnection/delete | Web Apps hybride verbinding verwijderen. |
> | Actie | microsoft.web/sites/hybridconnection/read | Web Apps hybride verbinding ophalen. |
> | Actie | microsoft.web/sites/hybridconnection/write | Web Apps hybride verbinding bijwerken. |
> | Actie | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Web Apps relays-naam ruimten voor hybride verbindingen verwijderen. |
> | Actie | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Lijst met sleutels Web Apps relays-naam ruimten voor hybride verbindingen. |
> | Actie | microsoft.web/sites/hybridconnectionnamespaces/relays/read | Down loads van Web Apps Hybrid Connection-naam ruimten. |
> | Actie | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Web Apps relays-naam ruimten voor hybride verbindingen bijwerken. |
> | Actie | microsoft.web/sites/hybridconnectionrelays/read | Ontvang Web Apps hybride verbindings relays. |
> | Actie | micro soft. web/sites/exemplaren/implementaties/verwijderen | Implementaties van Web Apps instanties verwijderen. |
> | Actie | microsoft.web/sites/instances/deployments/read | Implementaties van Web Apps-instanties ophalen. |
> | Actie | microsoft.web/sites/instances/extensions/log/read | Het logboek voor extensies van Web Apps-exemplaren ophalen. |
> | Actie | microsoft.web/sites/instances/extensions/processes/read | Web Apps-exemplaren van extensies-processen ophalen. |
> | Actie | microsoft.web/sites/instances/extensions/read | Uitbrei dingen van Web Apps-instanties ophalen. |
> | Actie | microsoft.web/sites/instances/processes/delete | Verwijder de processen van Web Apps exemplaren. |
> | Actie | microsoft.web/sites/instances/processes/modules/read | Get Web Apps instances-modules. |
> | Actie | microsoft.web/sites/instances/processes/read | Get Web Apps instances-processen. |
> | Actie | microsoft.web/sites/instances/processes/threads/read | Web Apps-exemplaren ophalen threads verwerken. |
> | Actie | microsoft.web/sites/instances/read | Web Apps-instanties ophalen. |
> | Actie | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Trigger status van synchronisatie functie voor lijst. |
> | Actie | microsoft.web/sites/metricdefinitions/read | Web Apps metrische definities ophalen. |
> | Actie | microsoft.web/sites/metrics/read | Web Apps metrische gegevens ophalen. |
> | Actie | microsoft.web/sites/metricsdefinitions/read | Web Apps metrische definities ophalen. |
> | Actie | microsoft.web/sites/migratemysql/action | MySql-Web Apps migreren. |
> | Actie | microsoft.web/sites/migratemysql/read | Ga Web Apps om MySql te migreren. |
> | Actie | microsoft.web/sites/networktrace/action | Web Apps netwerk tracering. |
> | Actie | microsoft.web/sites/networktraces/operationresults/read | Resultaten van Web Apps netwerk tracerings bewerking ophalen. |
> | Actie | microsoft.web/sites/newpassword/action | NieuwWachtwoord Web Apps. |
> | Actie | microsoft.web/sites/operationresults/read | Resultaten van Web Apps-bewerking ophalen. |
> | Actie | microsoft.web/sites/operations/read | Ontvang Web Apps bewerkingen. |
> | Actie | microsoft.web/sites/perfcounters/read | Prestatie meter items voor Web Apps ophalen. |
> | Actie | microsoft.web/sites/premieraddons/delete | Verwijder Web Apps Premier-invoeg toepassingen. |
> | Actie | microsoft.web/sites/premieraddons/read | Ontvang Web Apps Premier-invoeg toepassingen. |
> | Actie | microsoft.web/sites/premieraddons/write | Werk Web Apps Premier-invoeg toepassingen bij. |
> | Actie | microsoft.web/sites/privateaccess/read | Ontvang gegevens over toegang tot de persoonlijke locatie en geautoriseerde virtuele netwerken die toegang hebben tot de site. |
> | Actie | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Privé-eindpunt verbindingen goed keuren |
> | Actie | microsoft.web/sites/processes/modules/read | Web Apps-proces modules ophalen. |
> | Actie | microsoft.web/sites/processes/read | Web Apps-processen ophalen. |
> | Actie | microsoft.web/sites/processes/threads/read | Ontvang Web Apps process-threads. |
> | Actie | microsoft.web/sites/publiccertificates/delete | Web Apps open bare certificaten verwijderen. |
> | Actie | microsoft.web/sites/publiccertificates/read | Ontvang Web Apps open bare certificaten. |
> | Actie | microsoft.web/sites/publiccertificates/write | Web Apps open bare certificaten bijwerken. |
> | Actie | Microsoft.Web/sites/publish/Action | Een web-app publiceren |
> | Actie | Microsoft.Web/sites/publishxml/Action | XML voor publicatie profielen ophalen voor een web-app |
> | Actie | microsoft.web/sites/publishxml/read | Web Apps Publishing XML ophalen. |
> | Actie | Microsoft.Web/sites/Read | De eigenschappen van een web-app ophalen |
> | Actie | microsoft.web/sites/recommendationhistory/read | Geschiedenis van Web Apps aanbeveling ophalen. |
> | Actie | microsoft.web/sites/recommendations/disable/action | Schakel Web Apps aanbevelingen uit. |
> | Actie | Micro soft. web/sites/aanbevelingen/lezen | De lijst met aanbevelingen voor web-app ophalen. |
> | Actie | microsoft.web/sites/recover/action | Web Apps herstellen. |
> | Actie | Microsoft.Web/sites/resetSlotConfig/Action | De configuratie van de web-app opnieuw instellen |
> | Actie | microsoft.web/sites/resourcehealthmetadata/read | Meta gegevens van Web Apps Resource Health ophalen. |
> | Actie | Microsoft.Web/sites/restart/Action | Een web-app opnieuw starten |
> | Actie | microsoft.web/sites/restore/read | Web Apps herstel ophalen. |
> | Actie | microsoft.web/sites/restore/write | Web Apps herstellen. |
> | Actie | microsoft.web/sites/restorefrombackupblob/action | De web-app terugzetten vanuit een back-upblob. |
> | Actie | micro soft. web/sites/restorefromdeletedapp/actie | Web Apps van verwijderde app herstellen. |
> | Actie | microsoft.web/sites/restoresnapshot/action | Herstel Web Apps moment opnamen. |
> | Actie | microsoft.web/sites/siteextensions/delete | Verwijder Web Apps site-extensies. |
> | Actie | microsoft.web/sites/siteextensions/read | Web Apps site-extensies ophalen. |
> | Actie | microsoft.web/sites/siteextensions/write | Web Apps site-extensies bijwerken. |
> | Actie | microsoft.web/sites/slots/analyzecustomhostname/read | Web Apps sleuven ophalen aangepaste hostnaam analyseren. |
> | Actie | Microsoft.Web/sites/slots/applySlotConfig/Action | De sleuf configuratie van de web-app Toep assen op de huidige sleuf van de doel sleuf. |
> | Actie | Microsoft.Web/sites/slots/backup/Action | Nieuwe web app-sleuf back-up maken. |
> | Actie | microsoft.web/sites/slots/backup/read | Back-ups van Web Apps sleuven ophalen. |
> | Actie | microsoft.web/sites/slots/backup/write | Web Apps-sleuven voor back-ups bijwerken. |
> | Actie | microsoft.web/sites/slots/backups/action | Detecteer Web Apps sleuven back-ups. |
> | Actie | microsoft.web/sites/slots/backups/delete | Verwijder Web Apps-sleuven back-ups. |
> | Actie | microsoft.web/sites/slots/backups/list/action | Lijst met Web Apps sleuven back-ups. |
> | Actie | Microsoft.Web/sites/slots/backups/Read | De eigenschappen van de back-up van een web-app-sleuf ophalen |
> | Actie | microsoft.web/sites/slots/backups/restore/action | Back-ups van Web Apps sleuven herstellen. |
> | Actie | microsoft.web/sites/slots/config/delete | Web Apps sleuf configuratie verwijderen. |
> | Actie | Microsoft.Web/sites/slots/config/list/Action | Lijst met gevoelige instellingen voor beveiliging van de web-app-sleuf, zoals publicatie referenties, app-instellingen en verbindings reeksen |
> | Actie | Microsoft.Web/sites/slots/config/Read | Configuratie-instellingen van de web-app-sleuf ophalen |
> | Actie | Microsoft.Web/sites/slots/config/Write | Configuratie-instellingen van de web-app-sleuf bijwerken |
> | Actie | microsoft.web/sites/slots/containerlogs/action | Bewaar container logboeken voor de web-app-sleuf. |
> | Actie | microsoft.web/sites/slots/containerlogs/download/action | Down load Web Apps-sleuf container Logboeken. |
> | Actie | microsoft.web/sites/slots/continuouswebjobs/delete | Verwijder Web Apps-sleuven doorlopende webtaken. |
> | Actie | microsoft.web/sites/slots/continuouswebjobs/read | Ontvang Web Apps sleuven doorlopende webtaken. |
> | Actie | microsoft.web/sites/slots/continuouswebjobs/start/action | Start Web Apps-sleuven doorlopende webtaken. |
> | Actie | microsoft.web/sites/slots/continuouswebjobs/stop/action | Stop Web Apps-sleuven van doorlopende webtaken. |
> | Actie | Microsoft.Web/sites/slots/Delete | Een bestaande sleuf voor een web-app verwijderen |
> | Actie | microsoft.web/sites/slots/deployments/delete | Web Apps sleuf-implementaties verwijderen. |
> | Actie | microsoft.web/sites/slots/deployments/log/read | Haal het logboek voor de implementatie van Web Apps sleuven op. |
> | Actie | microsoft.web/sites/slots/deployments/read | Haal implementaties van Web Apps sleuven op. |
> | Actie | microsoft.web/sites/slots/deployments/write | Implementaties van Web Apps sleuven bijwerken. |
> | Actie | microsoft.web/sites/slots/detectors/read | Ontvang Web Apps sleuf detecties. |
> | Actie | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Voer de diagnostische analyse van Web Apps sleuven uit. |
> | Actie | microsoft.web/sites/slots/diagnostics/analyses/read | Haal analyse van de diagnostische gegevens van Web Apps-sleuven op. |
> | Actie | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Down load de diagnostische gegevens van Web Apps sleuven voor ASP.NET Core app. |
> | Actie | microsoft.web/sites/slots/diagnostics/autoheal/read | Automatisch herstellen van Web Apps-sleuven automatisch herstellen. |
> | Actie | micro soft. web/sites/sleuven/diagnostische gegevens/implementatie/lezen | Down load de diagnostische implementatie van Web Apps sleuven. |
> | Actie | micro soft. web/sites/sleuven/diagnostische gegevens/implementaties/lezen | Down loads van Web Apps sleuven diagnostische implementaties. |
> | Actie | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Voer de diagnostische detector van Web Apps sleuven uit. |
> | Actie | microsoft.web/sites/slots/diagnostics/detectors/read | Down load de detectie van Web Apps sleuven Diagnostics. |
> | Actie | micro soft. web/sites/sleuven/diagnostiek/frebanalysis/lezen | Ontvang Web Apps sleuven diagnostische FREB-analyse. |
> | Actie | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Ontvang Web Apps sleuven Diagnostische logboeken Log Analyzer. |
> | Actie | micro soft. web/sites/sleuven/diagnostische gegevens/lezen | Ontvang Web Apps sleuf diagnostiek. |
> | Actie | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Down load de beschik baarheid van de diagnostische runtime van Web Apps-sleuven. |
> | Actie | microsoft.web/sites/slots/diagnostics/servicehealth/read | Web Apps-sleuven diagnostische gegevens ophalen Service Health. |
> | Actie | micro soft. web/sites/sleuven/diagnostiek/sitecpuanalysis/lezen | Ontvang Web Apps sleuven diagnostische gegevens van de site CPU. |
> | Actie | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Ontvang Web Apps sleuven diagnostische gegevens van de site. |
> | Actie | microsoft.web/sites/slots/diagnostics/sitelatency/read | Down load Web Apps-sleuven site latentie diagnose. |
> | Actie | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Ontvang Web Apps sleuven diagnostische gegevens van het site geheugen. |
> | Actie | micro soft. web/sites/sleuven/diagnostiek/siterestartsettingupdate/lezen | Ophalen van de instelling update voor de diagnostische gegevens van Web Apps sites |
> | Actie | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Ophalen van Web Apps-sleuven site Diagnostics-gebruiker gestart. |
> | Actie | micro soft. web/sites/sleuven/diagnostiek/siteswap/lezen | Down load Web Apps-sleuven site swap. |
> | Actie | microsoft.web/sites/slots/diagnostics/threadcount/read | Web Apps-sleuven van het aantal diagnostische gegevens ophalen. |
> | Actie | microsoft.web/sites/slots/diagnostics/workeravailability/read | Ontvang Web Apps sleuven diagnostische gegevens Workeravailability. |
> | Actie | micro soft. web/sites/sleuven/diagnostiek/workerprocessrecycle/lezen | Web Apps-sleuven ophalen werk proces wordt herhaald. |
> | Actie | microsoft.web/sites/slots/domainownershipidentifiers/read | Domein eigendoms-Id's van Web Apps-sleuven ophalen. |
> | Actie | microsoft.web/sites/slots/functions/listsecrets/action | Lijst met geheimen Web Apps sleuven-functies. |
> | Actie | micro soft. web/sites/sleuven/functies/lezen | Web Apps-sleuven-functies ophalen. |
> | Actie | micro soft. web/sites/sleuven/hostnamebindings/verwijderen | Verwijder Web Apps sleuven hostnamen bindings. |
> | Actie | microsoft.web/sites/slots/hostnamebindings/read | Haal Web Apps sleuven hostname-bindingen op. |
> | Actie | micro soft. web/sites/sleuven/hostnamebindings/schrijven | Web Apps sleuven hostnamen bindingen bijwerken. |
> | Actie | microsoft.web/sites/slots/hybridconnection/delete | Web Apps-sleuven hybride verbinding verwijderen. |
> | Actie | microsoft.web/sites/slots/hybridconnection/read | Web Apps-sleuven hybride verbinding ophalen. |
> | Actie | microsoft.web/sites/slots/hybridconnection/write | Web Apps-sleuven hybride verbinding bijwerken. |
> | Actie | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Web Apps-sleuven verwijderen sites voor naam ruimten hybride verbinding. |
> | Actie | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Web Apps-sleuven voor het bijwerken van de sites met hybride verbindings ruimten door geven. |
> | Actie | microsoft.web/sites/slots/hybridconnectionrelays/read | Haal Web Apps-sleuven hybride verbindings relays door. |
> | Actie | microsoft.web/sites/slots/instances/deployments/read | Implementaties van Web Apps sleuf-instanties ophalen. |
> | Actie | microsoft.web/sites/slots/instances/processes/delete | Verwijder de processen van Web Apps-sleuf. |
> | Actie | microsoft.web/sites/slots/instances/processes/read | Web Apps-exemplaren van sleuf-processen ophalen. |
> | Actie | microsoft.web/sites/slots/instances/read | Web Apps sleuf-instanties ophalen. |
> | Actie | microsoft.web/sites/slots/metricdefinitions/read | Metrische definities van Web Apps-sleuven ophalen. |
> | Actie | microsoft.web/sites/slots/metrics/read | Gegevens over Web Apps sleuven ophalen. |
> | Actie | microsoft.web/sites/slots/migratemysql/read | Web Apps sleuven ophalen MySql migreren. |
> | Actie | microsoft.web/sites/slots/networktrace/action | Web Apps sleuven voor netwerk tracering. |
> | Actie | microsoft.web/sites/slots/networktraces/operationresults/read | Resultaten van de netwerk tracerings bewerking van Web Apps sleuven ophalen. |
> | Actie | microsoft.web/sites/slots/newpassword/action | NieuwWachtwoord Web Apps-sleuven. |
> | Actie | microsoft.web/sites/slots/operationresults/read | Resultaten van de bewerking Web Apps sleuven ophalen. |
> | Actie | microsoft.web/sites/slots/operations/read | Web Apps sleuf bewerkingen ophalen. |
> | Actie | microsoft.web/sites/slots/perfcounters/read | Prestatie meter items voor Web Apps sleuven ophalen. |
> | Actie | microsoft.web/sites/slots/phplogging/read | Web Apps Phplogging-sleuven ophalen. |
> | Actie | microsoft.web/sites/slots/premieraddons/delete | Web Apps-sleuven verwijderen premier-invoeg toepassingen. |
> | Actie | microsoft.web/sites/slots/premieraddons/read | Ontvang Web Apps-sleuven premier-invoeg toepassingen. |
> | Actie | microsoft.web/sites/slots/premieraddons/write | Web Apps-sleuven voor premier-invoeg toepassingen bijwerken. |
> | Actie | microsoft.web/sites/slots/processes/read | Web Apps sleuf processen ophalen. |
> | Actie | microsoft.web/sites/slots/publiccertificates/delete | Open bare certificaten Web Apps-sleuven verwijderen. |
> | Actie | microsoft.web/sites/slots/publiccertificates/read | Ontvang Web Apps-sleuven open bare certificaten. |
> | Actie | microsoft.web/sites/slots/publiccertificates/write | Open bare certificaten van Web Apps sleuven maken of bijwerken. |
> | Actie | Microsoft.Web/sites/slots/publish/Action | Een web app-sleuf publiceren |
> | Actie | Microsoft.Web/sites/slots/publishxml/Action | Publicatie profiel-XML ophalen voor web app-sleuf |
> | Actie | Microsoft.Web/sites/slots/Read | De eigenschappen van een web-app-implementatie sleuf ophalen |
> | Actie | microsoft.web/sites/slots/recover/action | Web Apps sleuven herstellen. |
> | Actie | Microsoft.Web/sites/slots/resetSlotConfig/Action | Configuratie van sleuf voor web-app opnieuw instellen |
> | Actie | microsoft.web/sites/slots/resourcehealthmetadata/read | Web Apps-sleuven ophalen Resource Health meta gegevens. |
> | Actie | Microsoft.Web/sites/slots/restart/Action | Een web-app-sleuf opnieuw opstarten |
> | Actie | microsoft.web/sites/slots/restore/read | Herstel van Web Apps sleuven ophalen. |
> | Actie | micro soft. web/sites/sleuven/herstellen/schrijven | Web Apps-sleuven herstellen. |
> | Actie | microsoft.web/sites/slots/restorefrombackupblob/action | Web Apps sleuf herstellen vanaf een back-upblob. |
> | Actie | micro soft. web/sites/sleuven/restorefromdeletedapp/actie | Web-app-sleuven van verwijderde app herstellen. |
> | Actie | microsoft.web/sites/slots/restoresnapshot/action | Herstel Web Apps sleuf momentopnamen. |
> | Actie | microsoft.web/sites/slots/siteextensions/delete | Site-extensies Web Apps sleuven verwijderen. |
> | Actie | microsoft.web/sites/slots/siteextensions/read | Site-extensies van Web Apps-sleuven ophalen. |
> | Actie | microsoft.web/sites/slots/siteextensions/write | Site-extensies Web Apps sleuven bijwerken. |
> | Actie | Microsoft.Web/sites/slots/slotsdiffs/Action | Verschillen in de configuratie van de web-app en-sleuven |
> | Actie | Microsoft.Web/sites/slots/slotsswap/Action | Implementatie sleuven voor web-apps wisselen |
> | Actie | microsoft.web/sites/slots/snapshots/read | Moment opnamen van Web Apps sleuven ophalen. |
> | Actie | Microsoft.Web/sites/slots/sourcecontrols/Delete | Configuratie-instellingen voor het bron beheer van de web-app-sleuf verwijderen |
> | Actie | Microsoft.Web/sites/slots/sourcecontrols/Read | Configuratie-instellingen voor het bron beheer van de web-app-sleuf ophalen |
> | Actie | Microsoft.Web/sites/slots/sourcecontrols/Write | Configuratie-instellingen voor bron beheer van de web-app-sleuf bijwerken |
> | Actie | Microsoft.Web/sites/slots/start/Action | Een web-app-sleuf starten |
> | Actie | Microsoft.Web/sites/slots/stop/Action | Een web-app-sleuf stoppen |
> | Actie | microsoft.web/sites/slots/sync/action | Web Apps-sleuven synchroniseren. |
> | Actie | microsoft.web/sites/slots/triggeredwebjobs/delete | Web Apps-sleuven die webjobs hebben geactiveerd, verwijderen. |
> | Actie | microsoft.web/sites/slots/triggeredwebjobs/read | Haal Web Apps-sleuven op die webjobs hebben geactiveerd. |
> | Actie | microsoft.web/sites/slots/triggeredwebjobs/run/action | Voer Web Apps-sleuven uit die webjobs hebben geactiveerd. |
> | Actie | microsoft.web/sites/slots/usages/read | Web Apps sleuven gebruiken. |
> | Actie | microsoft.web/sites/slots/virtualnetworkconnections/delete | Web Apps-sleuven verwijderen Virtual Network verbindingen. |
> | Actie | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Web Apps-sleuven bijwerken Virtual Network verbindingen gateways. |
> | Actie | microsoft.web/sites/slots/virtualnetworkconnections/read | Web Apps sleuven Virtual Network verbindingen ophalen. |
> | Actie | microsoft.web/sites/slots/virtualnetworkconnections/write | Web Apps-sleuven Virtual Network verbindingen bijwerken. |
> | Actie | microsoft.web/sites/slots/webjobs/read | Web Apps-sleuven voor webjobs ophalen. |
> | Actie | Micro soft. web/sites/sleuven/schrijven | Een nieuwe web-app-sleuf maken of een bestaande site bijwerken |
> | Actie | Microsoft.Web/sites/slotsdiffs/Action | Verschillen in de configuratie van de web-app en-sleuven |
> | Actie | Microsoft.Web/sites/slotsswap/Action | Implementatie sleuven voor web-apps wisselen |
> | Actie | microsoft.web/sites/snapshots/read | Web Apps moment opnamen ophalen. |
> | Actie | Microsoft.Web/sites/sourcecontrols/Delete | Configuratie-instellingen voor broncode beheer van web-app verwijderen |
> | Actie | Microsoft.Web/sites/sourcecontrols/Read | Configuratie-instellingen voor broncode beheer van web-app ophalen |
> | Actie | Microsoft.Web/sites/sourcecontrols/Write | Configuratie-instellingen voor broncode beheer van web-app bijwerken |
> | Actie | Microsoft.Web/sites/start/Action | Een web-app starten |
> | Actie | Microsoft.Web/sites/stop/Action | Een web-app stoppen |
> | Actie | microsoft.web/sites/sync/action | Web Apps synchroniseren. |
> | Actie | microsoft.web/sites/syncfunctiontriggers/action | Synchronisatie functie Triggers. |
> | Actie | microsoft.web/sites/triggeredwebjobs/delete | Web Apps geactiveerde webjobs verwijderen. |
> | Actie | microsoft.web/sites/triggeredwebjobs/history/read | Web Apps geactiveerde webjobs-geschiedenis ophalen. |
> | Actie | microsoft.web/sites/triggeredwebjobs/read | Web Apps geactiveerde webjobs ophalen. |
> | Actie | microsoft.web/sites/triggeredwebjobs/run/action | Web Apps geactiveerde webjobs worden uitgevoerd. |
> | Actie | microsoft.web/sites/usages/read | Ontvang Web Apps gebruik. |
> | Actie | microsoft.web/sites/virtualnetworkconnections/delete | Web Apps Virtual Network verbindingen verwijderen. |
> | Actie | microsoft.web/sites/virtualnetworkconnections/gateways/read | Web Apps gateways voor Virtual Network verbindingen ophalen. |
> | Actie | microsoft.web/sites/virtualnetworkconnections/gateways/write | Web Apps Virtual Network gateways voor verbindingen bijwerken. |
> | Actie | microsoft.web/sites/virtualnetworkconnections/read | Web Apps Virtual Network verbindingen ophalen. |
> | Actie | microsoft.web/sites/virtualnetworkconnections/write | Web Apps Virtual Network verbindingen bijwerken. |
> | Actie | microsoft.web/sites/webjobs/read | Web Apps webjobs ophalen. |
> | Actie | Microsoft.Web/sites/Write | Een nieuwe web-app maken of een bestaande bijwerken |
> | Actie | microsoft.web/skus/read | Down load Sku's. |
> | Actie | microsoft.web/sourcecontrols/read | Bron besturings elementen ophalen. |
> | Actie | microsoft.web/sourcecontrols/write | Bron besturings elementen bijwerken. |
> | Actie | microsoft.web/unregister/action | De registratie van de resource provider micro soft. web voor het abonnement ongedaan maken. |
> | Actie | microsoft.web/validate/action | Subelementid. |
> | Actie | microsoft.web/verifyhostingenvironmentvnet/action | Controleer het Vnet van de hosting omgeving. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Actietype | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Actie | Microsoft.WorkloadMonitor/components/read | Hiermee worden onderdelen voor de resource opgehaald |
> | Actie | Micro soft. WorkloadMonitor/componentsSummary/lezen | Hiermee wordt een overzicht van onderdelen opgehaald |
> | Actie | Microsoft.WorkloadMonitor/monitorInstances/read | Hiermee worden exemplaren van monitors voor de resource opgehaald |
> | Actie | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Hiermee wordt een overzicht van monitor exemplaren opgehaald |
> | Actie | Micro soft. WorkloadMonitor/monitors/lezen | Hiermee worden monitors opgehaald voor de resource |
> | Actie | Micro soft. WorkloadMonitor/monitors/schrijven | Monitor configureren voor de resource |
> | Actie | Microsoft.WorkloadMonitor/notificationSettings/read | Hiermee worden meldings instellingen voor de resource opgehaald |
> | Actie | Microsoft.WorkloadMonitor/notificationSettings/write | Instellingen voor meldingen voor de resource configureren |
> | Actie | Microsoft.WorkloadMonitor/operations/read | Hiermee worden de ondersteunde bewerkingen opgehaald |

## <a name="next-steps"></a>Volgende stappen

- [Overeenkomende resource provider voor service](../azure-resource-manager/azure-services-resource-providers.md)
- [Aangepaste rollen voor Azure-resources](custom-roles.md)
- [Ingebouwde rollen voor Azure-resources](built-in-roles.md)
