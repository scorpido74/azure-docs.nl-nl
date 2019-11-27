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
ms.date: 09/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 092c3b4ac6ce163e9fcf8aaad9e74f398559e9e2
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546324"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Bewerkingen voor de resource provider Azure Resource Manager

Dit artikel bevat een overzicht van de bewerkingen die beschikbaar zijn voor elke Azure Resource Manager resource provider. Deze bewerkingen kunnen worden gebruikt in [aangepaste rollen](custom-roles.md) om nauw keurig op [rollen gebaseerd toegangs beheer (RBAC)](overview.md) te bieden voor bronnen in Azure. Bewerkings reeksen hebben de volgende indeling: `{Company}.{ProviderName}/{resourceType}/{action}`. Zie [overeenkomen met de resource provider voor service](../azure-resource-manager/azure-services-resource-providers.md)voor een lijst met de manier waarop naam ruimten van de resource provider worden toegewezen aan Azure-Services.

De bewerkingen van de resource provider zijn altijd in ontwikkeling. Als u de meest recente bewerkingen wilt downloaden, gebruikt u de [bewerkings lijst](/cli/azure/provider/operation#az-provider-operation-list)van de [AzProviderOperation-](/powershell/module/az.resources/get-azprovideroperation) of AZ-provider.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Micro soft. AAD

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. AAD/domainServices/verwijderen | Domein service verwijderen |
> | Bewerking | Micro soft. AAD/domainServices/oucontainer/verwijderen | OE-container verwijderen |
> | Bewerking | Micro soft. AAD/domainServices/oucontainer/lezen | OE-containers lezen |
> | Bewerking | Micro soft. AAD/domainServices/oucontainer/schrijven | OE-container schrijven |
> | Bewerking | Micro soft. AAD/domainServices/lezen | Domein services lezen |
> | Bewerking | Micro soft. AAD/domainServices/replicaSets/verwijderen | Cluster site verwijderen |
> | Bewerking | Micro soft. AAD/domainServices/replicaSets/lezen | Cluster site lezen |
> | Bewerking | Micro soft. AAD/domainServices/replicaSets/schrijven | Cluster site schrijven |
> | Bewerking | Micro soft. AAD/domainServices/schrijven | Domein service schrijven |
> | Bewerking | Micro soft. AAD/locaties/operationresults/lezen |  |
> | Bewerking | Micro soft. AAD/Operations/lezen |  |
> | Bewerking | Micro soft. AAD/registreren/actie | Domein service registreren |
> | Bewerking | Micro soft. AAD/niet registreren/actie | Registratie van domein service ongedaan maken |

## <a name="microsoftaadiam"></a>micro soft. aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | micro soft. aadiam/diagnosticsettings/verwijderen | Een diagnostische instelling verwijderen |
> | Bewerking | micro soft. aadiam/diagnosticsettings/lezen | Een diagnostische instelling lezen |
> | Bewerking | micro soft. aadiam/diagnosticsettings/schrijven | Een diagnostische instelling schrijven |
> | Bewerking | micro soft. aadiam/diagnosticsettingscategories/lezen | Categorieën voor Diagnostische instellingen lezen |

## <a name="microsoftaddons"></a>Micro soft. Addons

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Addons/Operations/lezen | Hiermee worden ondersteunde RP-bewerkingen opgehaald. |
> | Bewerking | Micro soft. Addons/REGI ster/actie | Het opgegeven abonnement registreren bij micro soft. Addons |
> | Bewerking | Micro soft. Addons/supportProviders/listsupportplaninfo/Action | Een lijst met actuele informatie over het ondersteunings plan voor het opgegeven abonnement. |
> | Bewerking | Micro soft. Addons/supportProviders/supportPlanTypes/verwijderen | Hiermee verwijdert u het opgegeven canonieke ondersteunings plan |
> | Bewerking | Micro soft. Addons/supportProviders/supportPlanTypes/lezen | De opgegeven canonieke ondersteunings plan status ophalen. |
> | Bewerking | Micro soft. Addons/supportProviders/supportPlanTypes/write | Hiermee wordt het type canonieke ondersteunings plan toegevoegd dat is opgegeven. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/actie | Maak een nieuw forest voor de Tenant. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/addomainservicemembers/lezen | Hiermee worden alle servers opgehaald voor de opgegeven service naam. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/Alerts/lezen | Hiermee worden waarschuwings gegevens opgehaald voor het forest zoals alertid, datum van waarschuwing, laatste gedetecteerde waarschuwing, waarschuwing beschrijving, laatst bijgewerkt, waarschuwings niveau, waarschuwings status, koppelingen voor probleem oplossing, enzovoort. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/configuratie/lezen | Hiermee wordt de service configuratie voor het forest opgehaald. Voor beeld: Forestnaam, functionaliteits niveau, domein naamgevings Master FSMO Role, FSMO-functie van schema master, enzovoort. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/verwijderen | Hiermee verwijdert u een service en de bijbehorende servers samen met status gegevens. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/Dimensions/lezen | Hiermee worden de domeinen en site gegevens voor het forest opgehaald. Voor beeld: status, actieve waarschuwingen, omgezette waarschuwingen, eigenschappen zoals het functionele niveau van het domein, forest, infrastructuur master, PDC, RID-master, enzovoort.  |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/features/userpreferencechanged/lezen | Hiermee wordt de voorkeurs instelling van de gebruiker voor het forest opgehaald.<br>Voor beeld: MetricCounterName zoals ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Instellingen voor de gebruikers interface grafieken, enzovoort. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/forestsummary/lezen | Hiermee wordt een forest-samen vatting opgehaald voor het opgegeven forest, zoals de Forestnaam, het aantal domeinen onder dit forest, het aantal site-en site gegevens, enzovoort. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/metricmetadata/lezen | Hiermee wordt de lijst met ondersteunde metrische gegevens voor een bepaalde service opgehaald.<br>Bijvoorbeeld extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen/sec., enzovoort voor de ADFS-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor ADDomainService.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor ADSync-service. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/metrieken/groepen/lezen | Op basis van een service krijgt deze API de metrische gegevens.<br>Deze API kan bijvoorbeeld worden gebruikt voor het ophalen van informatie met betrekking tot: extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen per seconde, enzovoort voor de ADFederation-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor de ADDomain-service.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor synchronisatie service. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/premiumcheck/lezen | Met deze API wordt de lijst met alle onboarded ADDomainServices voor een Premium-Tenant opgehaald. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/lezen | Hiermee worden service Details opgehaald voor de opgegeven service naam. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/replicationdetails/lezen | Hiermee worden de replicatie Details opgehaald voor alle servers voor de opgegeven service naam. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/replicationstatus/lezen | Hiermee wordt het aantal domein controllers en de bijbehorende replicatie fouten opgehaald, indien aanwezig. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/replicationsummary/lezen | Hiermee wordt de volledige lijst met domein controllers en de replicatie Details voor het opgegeven forest opgehaald. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/Servicemembers/Action | Voeg een Server exemplaar toe aan de service. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/Servicemembers/referenties/lezen | Tijdens de registratie van de server van ADDomainService wordt deze API aangeroepen om de referenties op te halen voor de onboarding van nieuwe servers. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/Servicemembers/verwijderen | Hiermee verwijdert u een server voor een bepaalde service en Tenant. |
> | Bewerking | Micro soft. ADHybridHealthService/addsservices/schrijven | Hiermee wordt het ADDomainService-exemplaar voor de Tenant gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ADHybridHealthService/Configuration/Action | Hiermee wordt de Tenant configuratie bijgewerkt. |
> | Bewerking | Micro soft. ADHybridHealthService/configuratie/lezen | Hiermee wordt de Tenant configuratie gelezen. |
> | Bewerking | Micro soft. ADHybridHealthService/configuratie/schrijven | Hiermee maakt u een Tenant configuratie. |
> | Bewerking | Micro soft. ADHybridHealthService/logboeken/inhoud/lezen | Hiermee wordt de inhoud opgehaald van agent installatie-en registratie logboeken die zijn opgeslagen in BLOB. |
> | Bewerking | Micro soft. ADHybridHealthService/logboeken/lezen | Hiermee worden de agent installatie-en registratie logboeken voor de Tenant opgehaald. |
> | Bewerking | Micro soft. ADHybridHealthService/Operations/lezen | Hiermee wordt een lijst opgehaald met bewerkingen die worden ondersteund door het systeem. |
> | Bewerking | Micro soft. ADHybridHealthService/REGI ster/actie | Hiermee wordt de ADHybrid Health Service Resource provider geregistreerd en wordt het maken van ADHybrid Health Service Resource ingeschakeld. |
> | Bewerking | Micro soft. ADHybridHealthService/Reports/availabledeployments/lezen | Hiermee wordt een lijst met beschik bare regio's opgehaald die door DevOps worden gebruikt om klant incidenten te ondersteunen. |
> | Bewerking | Micro soft. ADHybridHealthService/Reports/badpassword/lezen | Hiermee haalt u de lijst met ongeldige wachtwoord pogingen op voor alle gebruikers in Active Directory Federation Service. |
> | Bewerking | Micro soft. ADHybridHealthService/Reports/badpassworduseridipfrequency/lezen | Hiermee wordt de BLOB SAS-URI met de status en het uiteindelijke resultaat van een nieuwe in wachtrij geplaatste rapport taak opgehaald voor frequentie van beschadigde gebruikers namen en wacht woorden per IP-adres per dag voor een bepaalde Tenant. |
> | Bewerking | Micro soft. ADHybridHealthService/Reports/consentedtodevopstenants/lezen | Hiermee wordt de lijst met DevOps-toestemmings tenants opgehaald. Normaal gesp roken gebruikt voor klant ondersteuning. |
> | Bewerking | Micro soft. ADHybridHealthService/Reports/isdevops/lezen | Haalt een waarde op die aangeeft of de Tenant is DevOps of niet. |
> | Bewerking | Micro soft. ADHybridHealthService/Reports/selectdevopstenant/lezen | Update GebruikersID (objectid) voor de geselecteerde dev OPS-Tenant. |
> | Bewerking | Micro soft. ADHybridHealthService/Reports/selecteddeployment/lezen | Hiermee wordt de geselecteerde implementatie voor de opgegeven Tenant opgehaald. |
> | Bewerking | Micro soft. ADHybridHealthService/Reports/tenantassigneddeployment/lezen | Als er een Tenant-id wordt opgegeven, wordt de opslag locatie van de Tenant opgehaald. |
> | Bewerking | Micro soft. ADHybridHealthService/Reports/updateselecteddeployment/lezen | Hiermee haalt u de geografische locatie op waarvan de gegevens worden geopend. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/actie | Hiermee wordt een service-exemplaar in de Tenant bijgewerkt. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/waarschuwingen/lezen | Hiermee worden de waarschuwingen voor een service gelezen. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/waarschuwingen/lezen | Hiermee worden de waarschuwingen voor een service gelezen. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/checkservicefeatureavailibility/lezen | Als er een functie naam wordt gebruikt, wordt gecontroleerd of een service alles heeft wat u nodig hebt om deze functie te gebruiken. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/verwijderen | Hiermee verwijdert u een service-exemplaar in de Tenant. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/exporterrors/lezen | Hiermee worden de export fouten voor een bepaalde synchronisatie service opgehaald. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/exportstatus/lezen | Hiermee wordt de export status voor een bepaalde service opgehaald. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/Feedbacktype/feedback/lezen | Hiermee ontvangt u feedback over waarschuwingen voor een bepaalde service en server. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/metricmetadata/lezen | Hiermee wordt de lijst met ondersteunde metrische gegevens voor een bepaalde service opgehaald.<br>Bijvoorbeeld extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen/sec., enzovoort voor de ADFS-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor ADDomainService.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor ADSync-service. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/metrieken/groepen/gemiddeld/lezen | Op basis van een service krijgt deze API het gemiddelde voor metrische gegevens voor een bepaalde service.<br>Deze API kan bijvoorbeeld worden gebruikt voor het ophalen van informatie met betrekking tot: extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen per seconde, enzovoort voor de ADFederation-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor de ADDomain-service.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor synchronisatie service. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/metrieken/groepen/lezen | Op basis van een service krijgt deze API de metrische gegevens.<br>Deze API kan bijvoorbeeld worden gebruikt voor het ophalen van informatie met betrekking tot: extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen per seconde, enzovoort voor de ADFederation-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor de ADDomain-service.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor synchronisatie service. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/metrieken/groepen/som/lezen | Op basis van een service krijgt deze API de geaggregeerde weer gave voor metrische gegevens voor een bepaalde service.<br>Deze API kan bijvoorbeeld worden gebruikt voor het ophalen van informatie met betrekking tot: extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen per seconde, enzovoort voor de ADFederation-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor de ADDomain-service.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor synchronisatie service. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/monitoringconfiguration/schrijven | Hiermee wordt de bewakings configuratie voor een service toegevoegd of bijgewerkt. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/monitoringconfigurations/lezen | Hiermee worden de bewakings configuraties voor een bepaalde service opgehaald. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/monitoringconfigurations/schrijven | Het toevoegen of bijwerken van bewakings configuraties voor een service. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/premiumcheck/lezen | Met deze API wordt de lijst met alle onboarded Services voor een Premium-Tenant opgehaald. |
> | Bewerking | Micro soft. ADHybridHealthService/services/lezen | Hiermee worden de service-exemplaren in de Tenant gelezen. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/rapporten/blobUris/lezen | Hiermee worden alle Uri's voor Risk ante IP-rapporten opgehaald voor de afgelopen zeven dagen. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/rapporten/details/lezen | Hiermee wordt een rapport van de meeste 50 gebruikers met een onjuist wacht woord van de afgelopen 7 dagen opgehaald |
> | Bewerking | Micro soft. ADHybridHealthService/Services/rapporten/generateBlobUri/actie | Genereert een Risk ante IP-rapport en retourneert een URI die ernaar verwijst. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/Servicemembers/actie | Hiermee maakt u een Server exemplaar in de service. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/Servicemembers/waarschuwingen/lezen | Hiermee worden de waarschuwingen voor een server gelezen. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/Servicemembers/referenties/lezen | Tijdens de registratie van de server wordt deze API aangeroepen om de referenties op te halen voor de onboarding van nieuwe servers. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/Servicemembers/datafreshness/lezen | Voor een bepaalde server krijgt deze API een lijst met gegevens typen die worden geüpload door de servers en de meest recente tijd voor elke upload. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/Servicemembers/verwijderen | Hiermee verwijdert u een Server exemplaar in de service. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/Servicemembers/exportstatus/lezen | Hiermee worden de fout gegevens voor het exporteren van de synchronisatie voor een bepaalde synchronisatie service opgehaald. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/Servicemembers/metrieken/groepen/lezen | Op basis van een service krijgt deze API de metrische gegevens.<br>Deze API kan bijvoorbeeld worden gebruikt voor het ophalen van informatie met betrekking tot: extranet-account vergrendelingen, totaal aantal mislukte aanvragen, openstaande token aanvragen (proxy), token aanvragen per seconde, enzovoort voor de ADFederation-service.<br>NTLM-authenticaties/sec. geslaagde LDAP-bindingen per seconde, LDAP-bindings tijd, LDAP actieve threads, Kerberos-authenticaties/sec, totaal aantal ATQ-threads, enzovoort voor de ADDomain-service.<br>Profiel latentie uitvoeren, TCP-verbindingen die tot stand zijn gebracht, persoonlijke bytes van Insights-agent, Statistieken exporteren naar Azure AD voor synchronisatie service. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/Servicemembers/metrieken/lezen | Hiermee wordt de lijst met connectors opgehaald en worden profiel namen uitgevoerd voor de opgegeven service en het betreffende service-lid. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/Servicemembers/lezen | Hiermee leest u het Server exemplaar in de service. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/Servicemembers/serviceconfiguration/lezen | Hiermee wordt de service configuratie voor een bepaalde Tenant opgehaald. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/tenantwhitelisting/lezen | Hiermee wordt de status van de functie white list voor een bepaalde Tenant opgehaald. |
> | Bewerking | Micro soft. ADHybridHealthService/Services/schrijven | Hiermee maakt u een service-exemplaar in de Tenant. |
> | Bewerking | Micro soft. ADHybridHealthService/registratie/actie | Hiermee wordt de registratie van het abonnement voor ADHybrid Health Service Resource provider ongedaan gemaakt. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Advisor/configuraties/lezen | Configuraties ophalen |
> | Bewerking | Micro soft. Advisor/configuraties/schrijven | Hiermee wordt een configuratie gemaakt/bijgewerkt |
> | Bewerking | Micro soft. Advisor/generateRecommendations/actie | Genereert aanbevelingen |
> | Bewerking | Micro soft. Advisor/generateRecommendations/lezen | Hiermee wordt de status van de aanbevelingen genereren opgehaald |
> | Bewerking | Micro soft. Advisor/meta gegevens/lezen | Meta gegevens ophalen |
> | Bewerking | Micro soft. Advisor/bewerkingen/lezen | Hiermee worden de bewerkingen voor de micro soft Advisor opgehaald |
> | Bewerking | Micro soft. Advisor/aanbevelingen/beschikbaar/actie | Er is nieuwe aanbeveling beschikbaar in micro soft Advisor |
> | Bewerking | Micro soft. Advisor/aanbevelingen/lezen | Aanbevelingen voor lezen |
> | Bewerking | Micro soft. Advisor/aanbevelingen/onderdrukkingen/verwijderen | Onderdrukkingen verwijderen |
> | Bewerking | Micro soft. Advisor/aanbevelingen/onderdrukkingen/lezen | Haalt onderdrukkingen op |
> | Bewerking | Micro soft. Advisor/aanbevelingen/onderdrukkingen/schrijven | Hiermee worden onderdrukkingen gemaakt/bijgewerkt |
> | Bewerking | Micro soft. Advisor/registreren/actie | Hiermee wordt het abonnement voor de micro soft Advisor geregistreerd |
> | Bewerking | Micro soft. Advisor/onderdrukkingen/verwijderen | Onderdrukkingen verwijderen |
> | Bewerking | Micro soft. Advisor/onderdrukkingen/lezen | Haalt onderdrukkingen op |
> | Bewerking | Micro soft. Advisor/onderdrukkingen/schrijven | Hiermee worden onderdrukkingen gemaakt/bijgewerkt |
> | Bewerking | Micro soft. Advisor/registratie/actie | Hiermee wordt de registratie van het abonnement voor micro soft Advisor ongedaan gemaakt |

## <a name="microsoftalertsmanagement"></a>Micro soft. AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. AlertsManagement/actionRules/verwijderen | De actie regel verwijderen in een bepaald abonnement. |
> | Bewerking | Micro soft. AlertsManagement/actionRules/lezen | Alle actie regels voor de invoer filters ophalen. |
> | Bewerking | Micro soft. AlertsManagement/actionRules/schrijven | Een actie regel in een bepaald abonnement maken of bijwerken |
> | Bewerking | Micro soft. AlertsManagement/Alerts/changestate/Action | Wijzig de status van de waarschuwing. |
> | Bewerking | Micro soft. AlertsManagement/waarschuwingen/diagnostische gegevens/lezen | Alle diagnostische gegevens voor de waarschuwing ophalen |
> | Bewerking | Micro soft. AlertsManagement/Alerts/geschiedenis/lezen | Geschiedenis van de waarschuwing ophalen |
> | Bewerking | Micro soft. AlertsManagement/waarschuwingen/lezen | Alle waarschuwingen voor de invoer filters ophalen. |
> | Bewerking | Micro soft. AlertsManagement/alertsList/lezen | Alle waarschuwingen voor de invoer filters voor alle abonnementen ophalen |
> | Bewerking | Micro soft. AlertsManagement/alertsMetaData/lezen | Meta gegevens van waarschuwingen ophalen voor de invoer parameter. |
> | Bewerking | Micro soft. AlertsManagement/alertsSummary/lezen | De samen vatting van waarschuwingen ophalen |
> | Bewerking | Micro soft. AlertsManagement/alertsSummaryList/lezen | De samen vatting van waarschuwingen in abonnementen ophalen |
> | Bewerking | Micro soft. AlertsManagement/Operations/lezen | Hiermee worden de beschik bare bewerkingen gelezen |
> | Bewerking | Micro soft. AlertsManagement/REGI ster/actie | Hiermee wordt het abonnement voor het micro soft-waarschuwings beheer geregistreerd |
> | Bewerking | Micro soft. AlertsManagement/smartDetectorAlertRules/verwijderen | Smart detector-waarschuwings regel in een bepaald abonnement verwijderen |
> | Bewerking | Micro soft. AlertsManagement/smartDetectorAlertRules/lezen | Alle Smart detector-waarschuwings regels voor de invoer filters ophalen |
> | Bewerking | Micro soft. AlertsManagement/smartDetectorAlertRules/schrijven | Smart detector-waarschuwings regel in een bepaald abonnement maken of bijwerken |
> | Bewerking | Micro soft. AlertsManagement/smartGroups/changestate/Action | Wijzig de status van de Smart Group |
> | Bewerking | Micro soft. AlertsManagement/smartGroups/geschiedenis/lezen | Geschiedenis van de slimme groep ophalen |
> | Bewerking | Micro soft. AlertsManagement/smartGroups/lezen | Alle slimme groepen voor de invoer filters ophalen |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. AnalysisServices/locaties/checkNameAvailability/actie | Hiermee wordt gecontroleerd of de opgegeven Analyseserver naam geldig is en niet wordt gebruikt. |
> | Bewerking | Micro soft. AnalysisServices/locaties/operationresults/lezen | Hiermee wordt de informatie opgehaald van het opgegeven bewerkings resultaat. |
> | Bewerking | Micro soft. AnalysisServices/locaties/operationstatuses/lezen | Hiermee wordt de informatie opgehaald van de opgegeven bewerkings status. |
> | Bewerking | Micro soft. AnalysisServices/Operations/lezen | Hiermee wordt de informatie over bewerkingen opgehaald |
> | Bewerking | Micro soft. AnalysisServices/REGI ster/actie | Registreert Analysis Services resource provider. |
> | Bewerking | Micro soft. AnalysisServices/servers/verwijderen | Hiermee verwijdert u de Analyseserver. |
> | Bewerking | Micro soft. AnalysisServices/servers/listGatewayStatus/actie | De status van de gateway die is gekoppeld met de server weer geven. |
> | Bewerking | Micro soft. AnalysisServices/servers/lezen | Hiermee wordt de informatie opgehaald van de opgegeven Analyseserver. |
> | Bewerking | Micro soft. AnalysisServices/servers/hervatten/actie | Hiermee wordt de Analyseserver hervat. |
> | Bewerking | Micro soft. AnalysisServices/servers/sku's/lezen | Beschik bare SKU-gegevens voor de server ophalen |
> | Bewerking | Micro soft. AnalysisServices/servers/onderbreken/actie | Hiermee wordt de Analyseserver onderbroken. |
> | Bewerking | Micro soft. AnalysisServices/servers/schrijven | Hiermee wordt het opgegeven Analyseserver gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. AnalysisServices/sku's/lezen | Hiermee wordt de informatie van Sku's opgehaald |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ApiManagement/checkNameAvailability/lezen | Controleert of de gegeven service naam beschikbaar is |
> | Bewerking | Micro soft. ApiManagement/Operations/lezen | Alle API-bewerkingen lezen die beschikbaar zijn voor micro soft. ApiManagement-resource |
> | Bewerking | Micro soft. ApiManagement/REGI ster/actie | Het abonnement voor de resource provider micro soft. ApiManagement registreren |
> | Bewerking | Micro soft. ApiManagement/rapporten/lezen | Haal rapporten op die zijn geaggregeerd met tijds perioden, een geografische regio, ontwikkel aars, producten, Api's, bewerkingen, abonnementen en byRequest. |
> | Bewerking | Micro soft. ApiManagement/service/api's/verwijderen | Hiermee verwijdert u de opgegeven API van het API Management service-exemplaar. |
> | Bewerking | Micro soft. ApiManagement/service/api's/diagnostiek/verwijderen | Hiermee verwijdert u de opgegeven diagnose vanuit een API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/diagnostiek/lezen | Een lijst met alle diagnostische gegevens van een API. of worden de details van de diagnose opgehaald voor een API die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/api's/diagnostiek/schrijven | Hiermee maakt u een nieuwe diagnose voor een API of wordt een bestaande versie bijgewerkt. of werkt de details bij van de diagnose voor een API die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/api's/problemen/bijlagen/verwijderen | Hiermee verwijdert u de opgegeven opmerking van een probleem. |
> | Bewerking | Micro soft. ApiManagement/service/api's/problemen/bijlagen/lezen | Een lijst met alle bijlagen voor het probleem dat is gekoppeld aan de opgegeven API. of worden de details van de kwestie bijlage opgehaald voor een API die is opgegeven met de id. |
> | Bewerking | Micro soft. ApiManagement/service/api's/problemen/bijlagen/schrijven | Hiermee wordt een nieuwe bijlage gemaakt voor het probleem in een API of wordt een bestaand item bijgewerkt. |
> | Bewerking | Micro soft. ApiManagement/service/api's/problemen/opmerkingen/verwijderen | Hiermee verwijdert u de opgegeven opmerking van een probleem. |
> | Bewerking | Micro soft. ApiManagement/service/api's/issues/comments/lezen | Een lijst met alle opmerkingen voor het probleem dat is gekoppeld aan de opgegeven API. of worden de details van de probleem Opmerking opgehaald voor een API die is opgegeven met de id. |
> | Bewerking | Micro soft. ApiManagement/service/api's/issues/comments/schrijven | Hiermee maakt u een nieuwe opmerking voor het probleem in een API of wordt een bestaand item bijgewerkt. |
> | Bewerking | Micro soft. ApiManagement/service/api's/problemen/verwijderen | Hiermee verwijdert u het opgegeven probleem uit een API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/problemen/lezen | Een lijst met alle problemen die zijn gekoppeld aan de opgegeven API. of worden de details opgehaald van het probleem voor een API die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/api's/problemen/schrijven | Hiermee maakt u een nieuw probleem voor een API of wordt een bestaande versie bijgewerkt. of werkt een bestaand probleem bij voor een API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/Operations/verwijderen | Hiermee verwijdert u de opgegeven bewerking in de API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/Operations/policies/verwijderen | Hiermee verwijdert u de beleids configuratie bij de API-bewerking. |
> | Bewerking | Micro soft. ApiManagement/service/api's/Operations/policies/lezen | De lijst met beleids configuratie op het API-bewerkings niveau ophalen. of u kunt de beleids configuratie ophalen op het niveau van de API-bewerking. |
> | Bewerking | Micro soft. ApiManagement/service/api's/Operations/policies/schrijven | Hiermee wordt een beleids configuratie voor het API-bewerkings niveau gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ApiManagement/service/api's/Operations/beleid/verwijderen | De beleids configuratie verwijderen op het niveau van de bewerking |
> | Bewerking | Micro soft. ApiManagement/service/api's/Operations/Policy/Read | De beleids configuratie ophalen op het niveau van de bewerking |
> | Bewerking | Micro soft. ApiManagement/service/api's/Operations/Policy/write | Beleids configuratie maken op bewerkings niveau |
> | Bewerking | Micro soft. ApiManagement/service/api's/Operations/lezen | Hiermee wordt een verzameling van de bewerkingen voor de opgegeven API weer gegeven. of worden de details opgehaald van de API-bewerking die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/api's/Operations/Tags/verwijderen | Ontkoppel de code van de bewerking. |
> | Bewerking | Micro soft. ApiManagement/service/api's/Operations/tags/lezen | Een lijst met alle labels die aan de bewerking zijn gekoppeld. of Get-label dat is gekoppeld aan de bewerking. |
> | Bewerking | Micro soft. ApiManagement/service/api's/Operations/Tags/schrijven | Wijs een tag toe aan de bewerking. |
> | Bewerking | Micro soft. ApiManagement/service/api's/Operations/write | Hiermee maakt u een nieuwe bewerking in de API of werkt u een bestaande. of werkt de details van de bewerking bij in de API die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/api's/operationsByTags/lezen | Hiermee wordt een verzameling bewerkingen weer gegeven die zijn gekoppeld aan Tags. |
> | Bewerking | Micro soft. ApiManagement/service/api's/policies/verwijderen | Hiermee verwijdert u de beleids configuratie op de API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/policies/lezen | De beleids configuratie ophalen op API-niveau. of de beleids configuratie op het API-niveau ophalen. |
> | Bewerking | Micro soft. ApiManagement/service/api's/beleid/schrijven | Hiermee wordt een beleids configuratie voor de API gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ApiManagement/service/api's/beleid/verwijderen | De beleids configuratie op API-niveau verwijderen |
> | Bewerking | Micro soft. ApiManagement/service/api's/beleid/lezen | De beleids configuratie ophalen op API-niveau |
> | Bewerking | Micro soft. ApiManagement/service/api's/beleid/schrijven | Beleids configuratie maken op API-niveau |
> | Bewerking | Micro soft. ApiManagement/service/api's/producten/lezen | Een lijst met alle producten waarvan de API deel uitmaakt. |
> | Bewerking | Micro soft. ApiManagement/service/api's/lezen | Een lijst met alle Api's van de API Management service-instantie. of worden de details opgehaald van de API die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/api's/releases/verwijderen | Hiermee verwijdert u alle versies van de API of verwijdert u de opgegeven release in de API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/releases/lezen | Een lijst met alle releases van een API.<br>Er wordt een API-versie gemaakt wanneer u een API-revisie actueel maakt.<br>Releases worden ook gebruikt om terug te draaien naar eerdere revisies.<br>De resultaten worden gewisseld en kunnen worden beperkt door de $top en de $skip-para meters.<br>of retourneert de details van een API-release. |
> | Bewerking | Micro soft. ApiManagement/service/api's/releases/schrijven | Hiermee maakt u een nieuwe release voor de API. of werkt de details bij van de release van de API die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/api's/revisies/verwijderen | Hiermee verwijdert u alle revisies van een API |
> | Bewerking | Micro soft. ApiManagement/service/api's/revisies/lezen | Een lijst met alle revisies van een API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/schema's/verwijderen | Hiermee verwijdert u de schema configuratie bij de API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/schema's/lezen | De schema configuratie ophalen op API-niveau. u kunt ook de schema configuratie ophalen op het API-niveau. |
> | Bewerking | Micro soft. ApiManagement/service/api's/schema's/schrijven | Hiermee wordt een schema configuratie voor de API gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ApiManagement/service/api's/tagDescriptions/verwijderen | Verwijder de beschrijving van de tag voor de API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/tagDescriptions/lezen | Een lijst met alle beschrijvingen van tags binnen het bereik van API. Model dat vergelijkbaar is met Swagger-tagDescription is gedefinieerd op API-niveau maar tag kan worden toegewezen aan de bewerkingen of een Get-label beschrijving in het bereik van API |
> | Bewerking | Micro soft. ApiManagement/service/api's/tagDescriptions/write | Beschrijving van een tag maken/bijwerken binnen het bereik van de API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/Tags/verwijderen | Ontkoppel de tag uit de API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/tags/lezen | Een lijst met alle labels die zijn gekoppeld aan de API. of Get-label dat is gekoppeld aan de API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/Tags/schrijven | Wijs tag toe aan de API. |
> | Bewerking | Micro soft. ApiManagement/service/api's/schrijven | Hiermee maakt u een nieuwe of bijgewerkte bestaande API van het API Management service-exemplaar. of werkt de opgegeven API van het API Management service-exemplaar bij. |
> | Bewerking | Micro soft. ApiManagement/service/apisByTags/lezen | Hiermee wordt een verzameling api's weer gegeven die zijn gekoppeld aan Tags. |
> | Bewerking | Micro soft. ApiManagement/service/apiVersionSets/verwijderen | Hiermee verwijdert u een specifieke API-versieset. |
> | Bewerking | Micro soft. ApiManagement/service/apiVersionSets/lezen | Geeft een verzameling van API-versie sets in het opgegeven service-exemplaar. of worden de details opgehaald van de API-versieset die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/apiVersionSets/versies/lezen | Lijst met versie-entiteiten ophalen |
> | Bewerking | Micro soft. ApiManagement/service/apiVersionSets/schrijven | Hiermee wordt een API-versieset gemaakt of bijgewerkt. of werkt de details bij van de API-versieset die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/applynetworkconfigurationupdates/actie | Hiermee worden de micro soft. ApiManagement-resources die worden uitgevoerd in Virtual Network bijgewerkt om bijgewerkte netwerk instellingen te kiezen. |
> | Bewerking | Micro soft. ApiManagement/service/authorizationServers/verwijderen | Hiermee verwijdert u een specifiek exemplaar van de autorisatie server. |
> | Bewerking | Micro soft. ApiManagement/service/authorizationServers/lezen | Hiermee wordt een verzameling autorisatie servers weer gegeven die zijn gedefinieerd binnen een service-exemplaar. of worden de details opgehaald van de autorisatie server die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/authorizationServers/schrijven | Hiermee maakt u een nieuwe autorisatie server of werkt u een bestaande autorisatie server bij. of worden de details van de autorisatie server die is opgegeven door de id, bijgewerkt. |
> | Bewerking | Micro soft. ApiManagement/service/back-ends/verwijderen | Hiermee verwijdert u de opgegeven back-end. |
> | Bewerking | Micro soft. ApiManagement/service/back-end/lezen | Hiermee wordt een verzameling back-endservers in het opgegeven service-exemplaar weer gegeven. of worden de details opgehaald van de back-end die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/back-end/reconnect/Action | Hiermee wordt de APIM-proxy op de hoogte gebracht van een nieuwe verbinding met de back-end na de opgegeven time-out. Als er geen time-out is opgegeven, wordt er een time-out van 2 minuten gebruikt. |
> | Bewerking | Micro soft. ApiManagement/service/back-ends/schrijven | Hiermee wordt een back-end gemaakt of bijgewerkt. of een bestaande back-end bijwerken. |
> | Bewerking | Micro soft. ApiManagement/service/Backup/Action | Backup API Management-service naar de opgegeven container in een door de gebruiker opgegeven opslag account |
> | Bewerking | Micro soft. ApiManagement/service/caches/verwijderen | Hiermee verwijdert u een specifieke cache. |
> | Bewerking | Micro soft. ApiManagement/service/caches/lezen | Hiermee wordt een verzameling van alle externe caches in het opgegeven service-exemplaar weer gegeven. of worden de details opgehaald van de cache die is opgegeven met de id. |
> | Bewerking | Micro soft. ApiManagement/service/caches/schrijven | Hiermee wordt een externe cache gemaakt of bijgewerkt die moet worden gebruikt in het API Management-exemplaar. of werkt de details bij van de cache die is opgegeven met de id. |
> | Bewerking | Micro soft. ApiManagement/service/certificaten/verwijderen | Hiermee verwijdert u een specifiek certificaat. |
> | Bewerking | Micro soft. ApiManagement/service/certificaten/lezen | Hiermee wordt een verzameling van alle certificaten in het opgegeven service-exemplaar weer gegeven. of worden de details opgehaald van het certificaat dat is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/certificaten/schrijven | Hiermee wordt het certificaat dat wordt gebruikt voor verificatie met de back-end, gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ApiManagement/service/Content types/contentItems/Delete | Hiermee verwijdert u het opgegeven inhouds item. |
> | Bewerking | Micro soft. ApiManagement/service/Content types/contentItems/lezen | Hiermee wordt een lijst met inhouds items geretourneerd of Details van het inhouds item geretourneerd |
> | Bewerking | Micro soft. ApiManagement/service/Content types/contentItems/write | Hiermee wordt een nieuw inhouds item gemaakt of een opgegeven inhouds item bijgewerkt |
> | Bewerking | Micro soft. ApiManagement/service/Content types/lezen | Hiermee wordt een lijst met inhouds typen geretourneerd |
> | Bewerking | Micro soft. ApiManagement/service/verwijderen | API Management service-exemplaar verwijderen |
> | Bewerking | Micro soft. ApiManagement/service/diagnostiek/verwijderen | Hiermee verwijdert u de opgegeven diagnose. |
> | Bewerking | Micro soft. ApiManagement/service/diagnostiek/lezen | Een lijst met alle diagnostische gegevens van de API Management service-instantie. of worden de details opgehaald van de diagnose die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/diagnostiek/schrijven | Hiermee wordt een nieuwe diagnose gemaakt of een bestaande bijgewerkt. of de details van de diagnostische gegevens die door de id zijn opgegeven, worden bijgewerkt. |
> | Bewerking | Micro soft. ApiManagement/service/gateways/actie | Hiermee wordt de gateway configuratie opgehaald. of werkt de heartbeat van de gateway bij. |
> | Bewerking | Micro soft. ApiManagement/service/gateways/verwijderen | Hiermee verwijdert u de specifieke gateway. |
> | Bewerking | Micro soft. ApiManagement/service/gateways/sleutels/actie | Hiermee worden gateway sleutels opgehaald. |
> | Bewerking | Micro soft. ApiManagement/service/gateways/lezen | Een lijst met gateways die zijn geregistreerd bij service-exemplaar. of worden de details opgehaald van de gateway die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/gateways/regeneratePrimaryKey/actie | Hiermee genereert u de primaire-gateway sleutel invalidationg alle tokens die ermee zijn gemaakt. |
> | Bewerking | Micro soft. ApiManagement/service/gateways/regenerateSecondaryKey/actie | Hiermee genereert u de secundaire gateway sleutel invalidationg alle tokens die ermee zijn gemaakt. |
> | Bewerking | Micro soft. ApiManagement/service/gateways/token/actie | Hiermee wordt het token voor gedeelde toegangs autorisatie opgehaald voor de gateway. |
> | Bewerking | Micro soft. ApiManagement/service/gateways/schrijven | Hiermee wordt een gateway gemaakt of bijgewerkt die moet worden gebruikt in het API Management-exemplaar. of werkt de details bij van de gateway die is opgegeven met de id. |
> | Bewerking | Micro soft. ApiManagement/service/getssotoken/actie | Hiermee wordt een SSO-token opgehaald dat kan worden gebruikt om zich aan te melden bij API Management service verouderde portal als beheerder |
> | Bewerking | Micro soft. ApiManagement/service/groepen/verwijderen | Hiermee verwijdert u een specifieke groep van het API Management service-exemplaar. |
> | Bewerking | Micro soft. ApiManagement/service/groepen/lezen | Een lijst met groepen die zijn gedefinieerd binnen een service-exemplaar. of worden de details opgehaald van de groep die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/groepen/gebruikers/verwijderen | Bestaande gebruiker uit bestaande groep verwijderen. |
> | Bewerking | Micro soft. ApiManagement/service/groepen/gebruikers/lezen | Hiermee wordt een verzameling gebruikers entiteiten weer gegeven die aan de groep zijn gekoppeld. |
> | Bewerking | Micro soft. ApiManagement/service/groepen/gebruikers/schrijven | Een bestaande gebruiker toevoegen aan een bestaande groep |
> | Bewerking | Micro soft. ApiManagement/service/groepen/schrijven | Hiermee wordt een groep gemaakt of bijgewerkt. of worden de details van de groep die is opgegeven door de id, bijgewerkt. |
> | Bewerking | Micro soft. ApiManagement/service/identityProviders/verwijderen | Hiermee verwijdert u de opgegeven id-provider configuratie. |
> | Bewerking | Micro soft. ApiManagement/service/identityProviders/lezen | Geeft een lijst van een id-provider die is geconfigureerd in het opgegeven service-exemplaar. of worden de configuratie details opgehaald van de ID-provider die is geconfigureerd in het opgegeven service-exemplaar. |
> | Bewerking | Micro soft. ApiManagement/service/identityProviders/schrijven | Hiermee wordt de Identity provider-configuratie gemaakt of bijgewerkt. of werkt een bestaande Identity provider-configuratie bij. |
> | Bewerking | Micro soft. ApiManagement/Service/problemen/lezen | Hiermee wordt een verzameling van problemen in het opgegeven service-exemplaar weer gegeven. of krijgt API Management probleem Details |
> | Bewerking | Micro soft. ApiManagement/service/locaties/networkstatus/lezen | Hiermee wordt de netwerk toegangs status opgehaald van de resources waarvan de service afhankelijk is in de locatie. |
> | Bewerking | Micro soft. ApiManagement/service/logger/verwijderen | Hiermee verwijdert u de opgegeven logboek registratie. |
> | Bewerking | Micro soft. ApiManagement/service/logger/lezen | Hiermee wordt een verzameling Logboeken in het opgegeven service-exemplaar weer gegeven. of worden de details opgehaald van het logboek dat is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/logger/schrijven | Hiermee wordt een logboek registratie gemaakt of bijgewerkt. of werkt een bestaande logger bij. |
> | Bewerking | Micro soft. ApiManagement/service/managedeployments/actie | SKU/eenheden wijzigen, regionale implementaties van API Management service toevoegen/verwijderen |
> | Bewerking | Micro soft. ApiManagement/service/networkstatus/lezen | Hiermee wordt de netwerk toegangs status opgehaald van de resources waarvan de service afhankelijk is. |
> | Bewerking | Micro soft. ApiManagement/service/meldingen/actie | Hiermee wordt een melding verzonden naar een opgegeven gebruiker |
> | Bewerking | Micro soft. ApiManagement/service/meldingen/lezen | Hiermee wordt een verzameling eigenschappen weer gegeven die zijn gedefinieerd binnen een service-exemplaar. of worden de details opgehaald van de melding die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/meldingen/recipientEmails/verwijderen | Hiermee verwijdert u het e-mail bericht uit de lijst met meldingen. |
> | Bewerking | Micro soft. ApiManagement/service/meldingen/recipientEmails/lezen | Hiermee wordt de lijst met e-mail berichten voor meldingen ontvangen die zijn geabonneerd op een melding. |
> | Bewerking | Micro soft. ApiManagement/service/meldingen/recipientEmails/schrijven | Hiermee wordt het e-mail adres toegevoegd aan de lijst met ontvangers voor de melding. |
> | Bewerking | Micro soft. ApiManagement/service/meldingen/recipientUsers/verwijderen | Hiermee verwijdert u de API Management gebruiker uit de lijst met meldingen. |
> | Bewerking | Micro soft. ApiManagement/service/meldingen/recipientUsers/lezen | Hiermee wordt de lijst opgehaald met de ontvanger van de meldings gebruiker die is geabonneerd op de melding. |
> | Bewerking | Micro soft. ApiManagement/service/meldingen/recipientUsers/schrijven | Voegt de API Management gebruiker toe aan de lijst met ontvangers voor de melding. |
> | Bewerking | Micro soft. ApiManagement/service/meldingen/schrijven | Melding van API Management Uitgever maken of bijwerken. |
> | Bewerking | Micro soft. ApiManagement/service/openidConnectProviders/verwijderen | Hiermee verwijdert u een specifieke OpenID Connect Connect-provider van het exemplaar van de API Management service. |
> | Bewerking | Micro soft. ApiManagement/service/openidConnectProviders/lezen | Een lijst met alle OpenID Connect Connect-providers. of haalt een specifieke OpenID Connect Connect-provider op. |
> | Bewerking | Micro soft. ApiManagement/service/openidConnectProviders/schrijven | Hiermee wordt de OpenID Connect Connect-provider gemaakt of bijgewerkt. of werkt de specifieke OpenID Connect Connect-provider bij. |
> | Bewerking | Micro soft. ApiManagement/service/operationresults/lezen | Hiermee wordt de huidige status van een langlopende bewerking opgehaald |
> | Bewerking | Micro soft. ApiManagement/service/beleid/verwijderen | Hiermee verwijdert u de globale beleids configuratie van de API Management-service. |
> | Bewerking | Micro soft. ApiManagement/service/beleid/lezen | Een lijst met alle globale beleids definities van de API Management-service. of de globale beleids definitie van de API Management-service ophalen. |
> | Bewerking | Micro soft. ApiManagement/service/beleid/schrijven | Hiermee wordt de globale beleids configuratie van de API Management-service gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ApiManagement/service/beleid/verwijderen | De beleids configuratie op Tenant niveau verwijderen |
> | Bewerking | Micro soft. ApiManagement/service/beleid/lezen | De beleids configuratie ophalen op Tenant niveau |
> | Bewerking | Micro soft. ApiManagement/service/beleid/schrijven | Beleids configuratie maken op Tenant niveau |
> | Bewerking | Micro soft. ApiManagement/service/policyDescriptions/lezen | Een lijst met alle beleids beschrijvingen. |
> | Bewerking | Micro soft. ApiManagement/service/policySnippets/lezen | Een lijst met alle beleids fragmenten. |
> | Bewerking | Micro soft. ApiManagement/service/portalsettings/lezen | Instellingen voor aanmelden ophalen voor de portal of instellingen voor het aanmelden ophalen voor de portal of voor het ophalen van delegerings instellingen voor de portal. |
> | Bewerking | Micro soft. ApiManagement/service/portalsettings/schrijven | Aanmeldings instellingen bijwerken. of om aanmeldings instellingen te maken of bij te werken. of update de instellingen voor aanmelden of het bijwerken van instellingen voor het aanmelden of het bijwerken van de instellingen voor delegering. of u instellingen voor delegering wilt maken of bijwerken. |
> | Bewerking | Micro soft. ApiManagement/service/producten/api's/verwijderen | Hiermee verwijdert u de opgegeven API uit het opgegeven product. |
> | Bewerking | Micro soft. ApiManagement/service/producten/api's/lezen | Hiermee wordt een verzameling weer gegeven van de Api's die zijn gekoppeld aan een product. |
> | Bewerking | Micro soft. ApiManagement/service/producten/api's/schrijven | Hiermee wordt een API toegevoegd aan het opgegeven product. |
> | Bewerking | Micro soft. ApiManagement/service/producten/verwijderen | Product verwijderen. |
> | Bewerking | Micro soft. ApiManagement/service/producten/groepen/verwijderen | Hiermee verwijdert u de koppeling tussen de opgegeven groep en het product. |
> | Bewerking | Micro soft. ApiManagement/service/producten/groepen/lezen | Hier wordt de verzameling ontwikkelaars groepen weer gegeven die zijn gekoppeld aan het opgegeven product. |
> | Bewerking | Micro soft. ApiManagement/service/producten/groepen/schrijven | Hiermee wordt de koppeling tussen de opgegeven ontwikkel groep en het opgegeven product toegevoegd. |
> | Bewerking | Micro soft. ApiManagement/service/producten/beleid/verwijderen | Hiermee verwijdert u de beleids configuratie van het product. |
> | Bewerking | Micro soft. ApiManagement/service/producten/beleid/lezen | De configuratie van het beleid op product niveau ophalen. of de beleids configuratie op product niveau ophalen. |
> | Bewerking | Micro soft. ApiManagement/service/producten/beleid/schrijven | Hiermee wordt een beleids configuratie voor het product gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ApiManagement/service/producten/beleid/verwijderen | De beleids configuratie op product niveau verwijderen |
> | Bewerking | Micro soft. ApiManagement/service/producten/beleid/lezen | De beleids configuratie ophalen op product niveau |
> | Bewerking | Micro soft. ApiManagement/service/producten/beleid/schrijven | Beleids configuratie maken op product niveau |
> | Bewerking | Micro soft. ApiManagement/service/producten/lezen | Een lijst met producten in het opgegeven service-exemplaar. of worden de details opgehaald van het product dat is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/producten/abonnementen/lezen | Hiermee wordt de verzameling abonnementen op het opgegeven product weer gegeven. |
> | Bewerking | Micro soft. ApiManagement/service/Producten/Tags/verwijderen | Ontkoppel de tag uit het product. |
> | Bewerking | Micro soft. ApiManagement/service/Producten/Tags/lezen | Een lijst met alle labels die aan het product zijn gekoppeld. of Get-label dat is gekoppeld aan het product. |
> | Bewerking | Micro soft. ApiManagement/service/Producten/Tags/schrijven | Wijs tag toe aan het product. |
> | Bewerking | Micro soft. ApiManagement/service/producten/schrijven | Hiermee wordt een product gemaakt of bijgewerkt. of bestaande product gegevens bijwerken. |
> | Bewerking | Micro soft. ApiManagement/service/productsByTags/lezen | Een lijst met producten die aan Tags zijn gekoppeld. |
> | Bewerking | Micro soft. ApiManagement/service/eigenschappen/verwijderen | Hiermee verwijdert u de specifieke eigenschap van het API Management service-exemplaar. |
> | Bewerking | Micro soft. ApiManagement/service/eigenschappen/lezen | Hiermee wordt een verzameling eigenschappen weer gegeven die zijn gedefinieerd binnen een service-exemplaar. of worden de details opgehaald van de eigenschap die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/eigenschappen/schrijven | Hiermee wordt een eigenschap gemaakt of bijgewerkt. of werkt de specifieke eigenschap bij. |
> | Bewerking | Micro soft. ApiManagement/service/quota's/Peri Oden/lezen | Waarde van quotum teller voor periode ophalen |
> | Bewerking | Micro soft. ApiManagement/service/quota's/Peri Oden/schrijven | Huidige waarde van quotum teller instellen |
> | Bewerking | Micro soft. ApiManagement/service/quota's/lezen | Waarden voor quotum ophalen |
> | Bewerking | Micro soft. ApiManagement/service/quota's/schrijven | Huidige waarde van quotum teller instellen |
> | Bewerking | Micro soft. ApiManagement/service/lezen | Meta gegevens voor een API Management service-exemplaar lezen |
> | Bewerking | Micro soft. ApiManagement/service/regio's/lezen | Een lijst met alle Azure-regio's waarin de service bestaat. |
> | Bewerking | Micro soft. ApiManagement/service/rapporten/lezen | Rapport ophalen dat is geaggregeerd met tijds perioden of het rapport ophalen dat is geaggregeerd door de geografische regio of het rapport ophalen dat is geaggregeerd door ontwikkel aars.<br>of haal het rapport op dat is geaggregeerd door producten.<br>of haal het rapport op dat door Api's is geaggregeerd of maak het rapport dat is geaggregeerd door bewerkingen of ontvang het rapport dat is geaggregeerd met een abonnement.<br>of aanvragen rapport gegevens ophalen |
> | Bewerking | Micro soft. ApiManagement/service/Restore/Action | De API Management-service herstellen vanuit de opgegeven container in een opslag account dat door de gebruiker is opgegeven |
> | Bewerking | Micro soft. ApiManagement/service/abonnementen/verwijderen | Hiermee verwijdert u het opgegeven abonnement. |
> | Bewerking | Micro soft. ApiManagement/service/abonnementen/lezen | Een lijst met alle abonnementen van het API Management service-exemplaar. of Hiermee wordt de opgegeven abonnements entiteit opgehaald. |
> | Bewerking | Micro soft. ApiManagement/service/abonnementen/regeneratePrimaryKey/actie | Hiermee wordt de primaire sleutel van het bestaande abonnement van het API Management service-exemplaar opnieuw gegenereerd. |
> | Bewerking | Micro soft. ApiManagement/service/abonnementen/regenerateSecondaryKey/actie | Hiermee wordt de secundaire sleutel van het bestaande abonnement van het exemplaar van de API Management-service opnieuw gegenereerd. |
> | Bewerking | Micro soft. ApiManagement/service/abonnementen/schrijven | Hiermee wordt het abonnement van de opgegeven gebruiker voor het opgegeven product gemaakt of bijgewerkt. of werkt de details bij van een abonnement dat is opgegeven met de id. |
> | Bewerking | Micro soft. ApiManagement/service/tagResources/lezen | Hiermee wordt een verzameling resources die zijn gekoppeld aan Tags weer gegeven. |
> | Bewerking | Micro soft. ApiManagement/service/tags/verwijderen | Hiermee verwijdert u een specifiek label van het API Management service-exemplaar. |
> | Bewerking | Micro soft. ApiManagement/service/tags/lezen | Hiermee wordt een verzameling tags weer gegeven die zijn gedefinieerd binnen een service-exemplaar. of worden de details opgehaald van het label dat door de id is opgegeven. |
> | Bewerking | Micro soft. ApiManagement/service/Tags/schrijven | Hiermee maakt u een tag. of werkt de details bij van het label dat is opgegeven met de id. |
> | Bewerking | Micro soft. ApiManagement/service/templates/verwijderen | Standaard API Management e-mail sjabloon opnieuw instellen |
> | Bewerking | Micro soft. ApiManagement/service/templates/lezen | Hiermee worden alle e-mail sjablonen opgehaald of API Management e-mail sjabloon gegevens opgehaald |
> | Bewerking | Micro soft. ApiManagement/service/templates/schrijven | API Management e-mail sjabloon of updates API Management e-mail sjabloon maken of bijwerken |
> | Bewerking | Micro soft. ApiManagement/service/Tenant/verwijderen | Beleids configuratie voor de Tenant verwijderen |
> | Bewerking | Micro soft. ApiManagement/service/Tenant/implementeren/actie | Voert een implementatie taak uit om wijzigingen van de opgegeven Git-vertakking toe te passen op de configuratie in de data base. |
> | Bewerking | Micro soft. ApiManagement/service/Tenant/operationResults/lezen | Lijst met bewerkings resultaten ophalen of resultaat van een specifieke bewerking ophalen |
> | Bewerking | Micro soft. ApiManagement/service/Tenant/lezen | De globale beleids definitie van de API Management-service ophalen. of Details van Tenant toegangs gegevens ophalen |
> | Bewerking | Micro soft. ApiManagement/service/Tenant/regeneratePrimaryKey/actie | Primaire toegangs sleutel opnieuw genereren |
> | Bewerking | Micro soft. ApiManagement/service/Tenant/regenerateSecondaryKey/actie | Secundaire toegangs sleutel opnieuw genereren |
> | Bewerking | Micro soft. ApiManagement/service/Tenant/opslaan/actie | Maakt door voeren met configuratie momentopname aan de opgegeven vertakking in de opslag plaats |
> | Bewerking | Micro soft. ApiManagement/service/Tenant/syncState/lezen | Status van de laatste Git-synchronisatie ophalen |
> | Bewerking | Micro soft. ApiManagement/service/Tenant/valideren/actie | Hiermee worden wijzigingen van de opgegeven Git-vertakking gecontroleerd |
> | Bewerking | Micro soft. ApiManagement/service/Tenant/schrijven | Beleids configuratie instellen voor de Tenant of Details van Tenant toegangs gegevens bijwerken |
> | Bewerking | Micro soft. ApiManagement/service/updatecertificate/actie | SSL-certificaat voor een API Management-service uploaden |
> | Bewerking | Micro soft. ApiManagement/service/updatehostname/actie | Aangepaste domein namen instellen, bijwerken of verwijderen voor een API Management-service |
> | Bewerking | Micro soft. ApiManagement/Service/gebruikers/actie | Een nieuwe gebruiker registreren |
> | Bewerking | Micro soft. ApiManagement/Service/gebruikers/bevestigingen/verzenden/actie | Bevestiging verzenden |
> | Bewerking | Micro soft. ApiManagement/Service/gebruikers/verwijderen | Hiermee verwijdert u een specifieke gebruiker. |
> | Bewerking | Micro soft. ApiManagement/Service/gebruikers/generateSsoUrl/actie | Hiermee wordt een omleidings-URL met een verificatie token opgehaald voor het ondertekenen van een bepaalde gebruiker in de ontwikkelaars Portal. |
> | Bewerking | Micro soft. ApiManagement/Service/gebruikers/groepen/lezen | Een lijst met alle gebruikers groepen. |
> | Bewerking | Micro soft. ApiManagement/Service/gebruikers/identiteiten/lezen | Lijst met alle gebruikers-id's. |
> | Bewerking | Micro soft. ApiManagement/Service/gebruikers/sleutels/lezen | Sleutels ophalen die aan de gebruiker zijn gekoppeld |
> | Bewerking | Micro soft. ApiManagement/Service/gebruikers/lezen | Hiermee wordt een verzameling geregistreerde gebruikers weer gegeven in het opgegeven service-exemplaar. of worden de details opgehaald van de gebruiker die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/Service/gebruikers/abonnementen/lezen | Hiermee wordt de verzameling abonnementen van de opgegeven gebruiker weer gegeven. |
> | Bewerking | Micro soft. ApiManagement/Service/gebruikers/token/actie | Hiermee haalt u het Shared Access Authorization-token voor de gebruiker. |
> | Bewerking | Micro soft. ApiManagement/Service/gebruikers/schrijven | Hiermee wordt een gebruiker gemaakt of bijgewerkt. of werkt de details bij van de gebruiker die is opgegeven door de id. |
> | Bewerking | Micro soft. ApiManagement/service/schrijven | Een nieuw exemplaar van API Management service maken |
> | Bewerking | Micro soft. ApiManagement/registratie/actie | De registratie van het abonnement voor de resource provider micro soft. ApiManagement is ongedaan gemaakt |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Authorization/classicAdministrators/verwijderen | Hiermee verwijdert u de beheerder van het abonnement. |
> | Bewerking | Micro soft. Authorization/classicAdministrators/operationstatuses/Read | Hiermee haalt u de bewerkings status van de beheerder van het abonnement. |
> | Bewerking | Micro soft. Authorization/classicAdministrators/lezen | Hiermee worden de beheerders voor het abonnement gelezen. |
> | Bewerking | Micro soft. Authorization/classicAdministrators/write | De beheerder toevoegen of wijzigen in een abonnement. |
> | Bewerking | Micro soft. Authorization/denyAssignments/verwijderen | Een deny-toewijzing verwijderen bij het opgegeven bereik. |
> | Bewerking | Micro soft. Authorization/denyAssignments/lezen | Informatie over een weigerings toewijzing ophalen. |
> | Bewerking | Micro soft. Authorization/denyAssignments/write | Een weigerings toewijzing maken bij het opgegeven bereik. |
> | Bewerking | Micro soft. Authorization/elevateAccess/Action | Hiermee wordt de gebruiker toegang tot beheerders verleend in het Tenant bereik |
> | Bewerking | Micro soft. autorisatie/vergren delen/verwijderen | Verwijder de vergren delingen bij het opgegeven bereik. |
> | Bewerking | Micro soft. autorisatie/vergren delen/lezen | Hiermee worden de vergren delingen voor het opgegeven bereik opgehaald. |
> | Bewerking | Micro soft. autorisatie/vergren delen/schrijven | Vergren delingen aan het opgegeven bereik toevoegen. |
> | Bewerking | Micro soft. Authorization/Operations/Read | Hiermee wordt de lijst met bewerkingen opgehaald |
> | Bewerking | Micro soft. Authorization/permissions/Read | Een lijst met alle machtigingen die de aanroeper heeft voor een bepaald bereik. |
> | Bewerking | Micro soft. Authorization/Policies/audit/Action | De actie die wordt ondernomen als gevolg van de evaluatie van Azure Policy met het effect ' controle ' |
> | Bewerking | Micro soft. Authorization/Policies/auditIfNotExists/Action | De actie die wordt ondernomen als gevolg van de evaluatie van Azure Policy met het effect ' auditIfNotExists ' |
> | Bewerking | Micro soft. Authorization/Policies/Deny/Action | De actie die wordt ondernomen als gevolg van de evaluatie van Azure Policy met het effect ' deny ' |
> | Bewerking | Micro soft. Authorization/Policies/deployIfNotExists/Action | De actie die wordt ondernomen als gevolg van de evaluatie van Azure Policy met het effect ' deployIfNotExists ' |
> | Bewerking | Micro soft. Authorization/policyAssignments/verwijderen | Een beleids toewijzing verwijderen bij het opgegeven bereik. |
> | Bewerking | Micro soft. Authorization/policyAssignments/lezen | Informatie over een beleids toewijzing ophalen. |
> | Bewerking | Micro soft. Authorization/policyAssignments/write | Een beleids toewijzing maken voor het opgegeven bereik. |
> | Bewerking | Micro soft. Authorization/policyDefinitions/verwijderen | Een beleids definitie verwijderen. |
> | Bewerking | Micro soft. Authorization/policyDefinitions/lezen | Informatie over een beleids definitie ophalen. |
> | Bewerking | Micro soft. Authorization/policyDefinitions/write | Maak een aangepaste beleids definitie. |
> | Bewerking | Micro soft. Authorization/policySetDefinitions/verwijderen | Een definitie van een beleidsset verwijderen. |
> | Bewerking | Micro soft. Authorization/policySetDefinitions/lezen | Informatie over een definitie van een beleidsset ophalen. |
> | Bewerking | Micro soft. Authorization/policySetDefinitions/write | Maak een aangepaste definitie van een beleidsset. |
> | Bewerking | Micro soft. Authorization/providerOperations/lezen | Get-bewerkingen voor alle resource providers die kunnen worden gebruikt in roldefinities. |
> | Bewerking | Micro soft. Authorization/roleAssignments/verwijderen | Een roltoewijzing verwijderen bij het opgegeven bereik. |
> | Bewerking | Micro soft. Authorization/roleAssignments/lezen | Informatie over een roltoewijzing ophalen. |
> | Bewerking | Microsoft.Authorization/roleAssignments/write | Een roltoewijzing maken bij het opgegeven bereik. |
> | Bewerking | Micro soft. Authorization/roleDefinitions/verwijderen | De opgegeven aangepaste roldefinitie verwijderen. |
> | Bewerking | Micro soft. Authorization/roleDefinitions/lezen | Informatie over een roldefinitie ophalen. |
> | Bewerking | Micro soft. Authorization/roleDefinitions/write | Een aangepaste roldefinitie maken of bijwerken met opgegeven machtigingen en toewijs bare bereiken. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Automation/automationAccounts/agentRegistrationInformation/lezen | De registratie gegevens van een Azure Automation DSC lezen |
> | Bewerking | Micro soft. Automation/automationAccounts/agentRegistrationInformation/regenerateKey/Action | Schrijft een aanvraag om Azure Automation DSC-sleutels opnieuw te genereren |
> | Bewerking | Micro soft. Automation/automationAccounts/certificaten/verwijderen | Hiermee wordt een Azure Automation-certificaat element verwijderd |
> | Bewerking | Micro soft. Automation/automationAccounts/certificates/getCount/Action | Hiermee wordt het aantal certificaten gelezen |
> | Bewerking | Micro soft. Automation/automationAccounts/certificaten/lezen | Hiermee wordt een Azure Automation-certificaat element opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/certificaten/schrijven | Hiermee wordt een Azure Automation certificaat-Asset gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/compilationjobs/lezen | Hiermee wordt de compilatie van een Azure Automation DSC gelezen |
> | Bewerking | Micro soft. Automation/automationAccounts/compilationjobs/lezen | Hiermee wordt de compilatie van een Azure Automation DSC gelezen |
> | Bewerking | Micro soft. Automation/automationAccounts/compilationjobs/write | Hiermee wordt de compilatie van een Azure Automation DSC geschreven |
> | Bewerking | Micro soft. Automation/automationAccounts/compilationjobs/write | Hiermee wordt de compilatie van een Azure Automation DSC geschreven |
> | Bewerking | Micro soft. Automation/automationAccounts/configuraties/inhoud/lezen | Hiermee wordt de inhoud van de configuratie Media gelezen |
> | Bewerking | Micro soft. Automation/automationAccounts/configuraties/verwijderen | Hiermee wordt de inhoud van een Azure Automation DSC verwijderd |
> | Bewerking | Micro soft. Automation/automationAccounts/configurations/getCount/Action | Hiermee wordt het aantal gelezen van een Azure Automation DSC-inhoud |
> | Bewerking | Micro soft. Automation/automationAccounts/configuraties/lezen | Hiermee wordt de inhoud van een Azure Automation DSC opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/configuraties/schrijven | Schrijft de inhoud van een Azure Automation DSC |
> | Bewerking | Micro soft. Automation/automationAccounts/Connections/Delete | Hiermee wordt een Azure Automation verbindings element verwijderd |
> | Bewerking | Micro soft. Automation/automationAccounts/Connections/getCount/Action | Hiermee wordt het aantal verbindingen gelezen |
> | Bewerking | Micro soft. Automation/automationAccounts/Connections/Read | Hiermee wordt een Azure Automation verbindings element opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/Connections/write | Hiermee wordt een Azure Automation verbindings-Asset gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/connectionTypes/verwijderen | Hiermee verwijdert u een Azure Automation verbindings type-Asset |
> | Bewerking | Micro soft. Automation/automationAccounts/connectionTypes/lezen | Hiermee wordt een Azure Automation verbindings type-Asset opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/connectionTypes/write | Hiermee maakt u een Azure Automation verbindings type-Asset |
> | Bewerking | Micro soft. Automation/automationAccounts/referenties/verwijderen | Hiermee wordt een Azure Automation-referentie-element verwijderd |
> | Bewerking | Micro soft. Automation/automationAccounts/credentials/getCount/Action | Hiermee wordt het aantal referenties gelezen |
> | Bewerking | Micro soft. Automation/automationAccounts/referenties/lezen | Hiermee wordt een Azure Automation referentie-element opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/referenties/schrijven | Hiermee wordt een Azure Automation referentie-Asset gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/verwijderen | Hiermee verwijdert u een Azure Automation account |
> | Bewerking | Micro soft. Automation/automationAccounts/hybridRunbookWorkerGroups/verwijderen | Hiermee verwijdert u Hybrid Runbook Worker resources |
> | Bewerking | Micro soft. Automation/automationAccounts/hybridRunbookWorkerGroups/lezen | Hybrid Runbook Worker resources lezen |
> | Bewerking | Micro soft. Automation/automationAccounts/Jobs/uitvoer/lezen | Hiermee wordt de uitvoer van een taak opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/Jobs/lezen | Hiermee wordt een Azure Automation-taak opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/Jobs/resume/Action | Hiermee wordt een Azure Automation taak hervat |
> | Bewerking | Micro soft. Automation/automationAccounts/Jobs/runbookContent/Action | Hiermee wordt de inhoud van het Azure Automation runbook opgehaald op het moment dat de taak wordt uitgevoerd |
> | Bewerking | Micro soft. Automation/automationAccounts/Jobs/stop/actie | Hiermee wordt een Azure Automation taak gestopt |
> | Bewerking | Micro soft. Automation/automationAccounts/Jobs/stromen/lezen | Hiermee wordt een Azure Automation taak stroom opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/Jobs/stromen/lezen | Hiermee wordt een Azure Automation taak stroom opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/Jobs/onderbreken/actie | Hiermee wordt een Azure Automation taak onderbroken |
> | Bewerking | Micro soft. Automation/automationAccounts/Jobs/schrijven | Hiermee maakt u een Azure Automation taak |
> | Bewerking | Micro soft. Automation/automationAccounts/jobSchedules/verwijderen | Hiermee wordt een Azure Automation taak schema verwijderd |
> | Bewerking | Micro soft. Automation/automationAccounts/jobSchedules/lezen | Hiermee wordt een Azure Automation taak planning opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/jobSchedules/write | Hiermee maakt u een Azure Automation taak schema |
> | Bewerking | Micro soft. Automation/automationAccounts/linkedWorkspace/lezen | Hiermee wordt de werk ruimte opgehaald die is gekoppeld aan het Automation-account |
> | Bewerking | Micro soft. Automation/automationAccounts/Listkeys ophalen/Action | Hiermee worden de sleutels voor het Automation-account gelezen |
> | Bewerking | Micro soft. Automation/automationAccounts/modules/activiteiten/lezen | Hiermee worden Azure Automation activiteiten opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/modules/verwijderen | Hiermee wordt een Azure Automation Power shell-module verwijderd |
> | Bewerking | Micro soft. Automation/automationAccounts/modules/getCount/Action | Hiermee wordt het aantal Power shell-modules in het Automation-account opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/modules/lezen | Hiermee wordt een Azure Automation Power shell-module opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/modules/schrijven | Hiermee wordt een Azure Automation Power shell-module gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/nodeConfigurations/verwijderen | Hiermee wordt de knooppunt configuratie van een Azure Automation DSC verwijderd |
> | Bewerking | Micro soft. Automation/automationAccounts/nodeConfigurations/rawContent/Action | Hiermee wordt de inhoud van een Azure Automation DSC-knooppunt configuratie gelezen |
> | Bewerking | Micro soft. Automation/automationAccounts/nodeConfigurations/lezen | Hiermee wordt de knooppunt configuratie van een Azure Automation DSC gelezen |
> | Bewerking | Micro soft. Automation/automationAccounts/nodeConfigurations/write | Hiermee wordt de knooppunt configuratie van een Azure Automation DSC geschreven |
> | Bewerking | Micro soft. Automation/automationAccounts/nodecounts/lezen | Hiermee leest u de samen vatting van het aantal knoop punten voor het opgegeven type |
> | Bewerking | Micro soft. Automation/automationAccounts/nodes/Delete | Verwijdert Azure Automation DSC-knoop punten |
> | Bewerking | Micro soft. Automation/automationAccounts/nodes/Read | Azure Automation DSC-knoop punten lezen |
> | Bewerking | Micro soft. Automation/automationAccounts/knoop punten/rapporten/inhoud/lezen | Leest Azure Automation DSC-rapport inhoud |
> | Bewerking | Micro soft. Automation/automationAccounts/knoop punten/rapporten/lezen | Azure Automation DSC-rapporten lezen |
> | Bewerking | Micro soft. Automation/automationAccounts/nodes/write | Hiermee worden Azure Automation DSC-knoop punten gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/objectDataTypes/Fields/Read | Hiermee wordt Azure Automation TypeFields |
> | Bewerking | Micro soft. Automation/automationAccounts/python2Packages/verwijderen | Hiermee verwijdert u een Azure Automation python 2-pakket |
> | Bewerking | Micro soft. Automation/automationAccounts/python2Packages/lezen | Hiermee wordt een Azure Automation python 2-pakket opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/python2Packages/write | Hiermee wordt een Azure Automation python 2-pakket gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/python3Packages/verwijderen | Hiermee verwijdert u een Azure Automation python 3-pakket |
> | Bewerking | Micro soft. Automation/automationAccounts/python3Packages/lezen | Hiermee wordt een Azure Automation python 3-pakket opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/python3Packages/write | Hiermee wordt een Azure Automation python 3-pakket gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/lezen | Hiermee wordt een Azure Automation-account opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/inhoud/lezen | Hiermee wordt de inhoud van een Azure Automation runbook opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/verwijderen | Hiermee wordt een Azure Automation runbook verwijderd |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/Draft/inhoud/schrijven | Hiermee wordt de inhoud van een Azure Automation-runbookconcept gemaakt |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/Draft/operationResults/lezen | Hiermee worden de resultaten van de concept bewerking Azure Automation runbook opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/Draft/lezen | Hiermee wordt een Azure Automation runbook-concept opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/Draft/testJob/lezen | Hiermee wordt een test taak voor een concept van een Azure Automation runbook opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/Draft/testJob/resume/actie | Hiermee wordt een test taak voor een Azure Automation-runbookconcept hervat |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/Draft/testJob/stop/actie | Hiermee wordt een test taak voor een Azure Automation-runbookconcept gestopt |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/Draft/testJob/Suspend/Action | Hiermee wordt een test taak voor een Azure Automation-runbookconcept onderbroken |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/Draft/testJob/schrijven | Hiermee wordt een test taak voor een concept van een Azure Automation runbook gemaakt |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/Draft/undoEdit/actie | Wijzigingen aan een Azure Automation-runbookconcept ongedaan maken |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/getCount/actie | Hiermee wordt het aantal Azure Automation runbooks opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/publiceren/actie | Hiermee wordt een Azure Automation-runbookconcept gepubliceerd |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/lezen | Hiermee wordt een Azure Automation runbook opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/runbooks/schrijven | Hiermee wordt een Azure Automation runbook gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/planning/verwijderen | Hiermee verwijdert u een Azure Automation schema-Asset |
> | Bewerking | Micro soft. Automation/automationAccounts/schedules/getCount/Action | Hiermee wordt het aantal Azure Automation planningen opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/schedules/lezen | Hiermee wordt een Azure Automation schema-element opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/schedules/write | Hiermee wordt een Azure Automation Schedule-Asset gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/lezen | Hiermee wordt een Azure Automation uitvoeren van de configuratie van de software-update-computer opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/softwareUpdateConfigurationRuns/lezen | Hiermee wordt een Azure Automation uitvoeren van de configuratie van software-updates opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/softwareUpdateConfigurations/verwijderen | Hiermee wordt een Azure Automation Software-update configuratie verwijderd |
> | Bewerking | Micro soft. Automation/automationAccounts/softwareUpdateConfigurations/verwijderen | Hiermee wordt een Azure Automation Software-update configuratie verwijderd |
> | Bewerking | Micro soft. Automation/automationAccounts/softwareUpdateConfigurations/lezen | Hiermee wordt een Azure Automation configuratie van software-updates opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/softwareUpdateConfigurations/lezen | Hiermee wordt een Azure Automation configuratie van software-updates opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/softwareUpdateConfigurations/write | Hiermee wordt Azure Automation Software-update configuratie gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/softwareUpdateConfigurations/write | Hiermee wordt Azure Automation Software-update configuratie gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/statistieken/lezen | Hiermee worden Azure Automation statistieken opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/updateDeploymentMachineRuns/lezen | Een Azure Automation update-implementatie machine ophalen |
> | Bewerking | Micro soft. Automation/automationAccounts/updateManagementPatchJob/lezen | Hiermee wordt een Azure Automation patch taak voor update beheer opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/usages/lezen | Hiermee wordt het Azure Automation gebruik opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/Varia bles/verwijderen | Hiermee wordt een Azure Automation variabel activum verwijderd |
> | Bewerking | Micro soft. Automation/automationAccounts/Varia bles/lezen | Hiermee wordt een Azure Automation variabel activum gelezen |
> | Bewerking | Micro soft. Automation/automationAccounts/Varia bles/-schrijven | Hiermee wordt een Azure Automation variabele-Asset gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/mijnen/verwijderen | Een Azure Automation Watcher-taak verwijderen |
> | Bewerking | Micro soft. Automation/automationAccounts/mijnen/lezen | Hiermee wordt een Azure Automation Watcher-taak opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/mijnen/starten/actie | Een Azure Automation Watcher-taak starten |
> | Bewerking | Micro soft. Automation/automationAccounts/mijnen/stoppen/actie | Een Azure Automation Watcher-taak stoppen |
> | Bewerking | Micro soft. Automation/automationAccounts/mijnen/stromen/lezen | Hiermee wordt een Azure Automation Watcher-taak stroom opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/mijnen/watcherActions/verwijderen | Een Azure Automation Watcher-taak acties verwijderen |
> | Bewerking | Micro soft. Automation/automationAccounts/mijnen/watcherActions/lezen | Hiermee wordt een Azure Automation Watcher-taak acties opgehaald |
> | Bewerking | Micro soft. Automation/automationAccounts/mijnen/watcherActions/schrijven | Een Azure Automation Watcher-taak acties maken |
> | Bewerking | Micro soft. Automation/automationAccounts/mijnen/schrijven | Hiermee wordt een Azure Automation Watcher-taak gemaakt |
> | Bewerking | Micro soft. Automation/automationAccounts/webhooks/actie | Hiermee wordt een URI voor een Azure Automation-webhook gegenereerd |
> | Bewerking | Micro soft. Automation/automationAccounts/webhooks/verwijderen | Hiermee wordt een Azure Automation-webhook verwijderd  |
> | Bewerking | Micro soft. Automation/automationAccounts/webhooks/lezen | Hiermee wordt een Azure Automation-webhook gelezen |
> | Bewerking | Micro soft. Automation/automationAccounts/webhooks/schrijven | Hiermee wordt een Azure Automation-webhook gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/automationAccounts/schrijven | Hiermee wordt een Azure Automation-account gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Automation/Operations/lezen | Hiermee worden beschik bare bewerkingen voor Azure Automation resources opgehaald |
> | Bewerking | Micro soft. Automation/REGI ster/actie | Hiermee wordt het abonnement geregistreerd bij Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. AzureActiveDirectory/b2cDirectories/verwijderen | B2C Directory-resource verwijderen |
> | Bewerking | Micro soft. AzureActiveDirectory/b2cDirectories/lezen | B2C Directory-resource weer geven |
> | Bewerking | Micro soft. AzureActiveDirectory/b2cDirectories/schrijven | B2C-Directory resource maken of bijwerken |
> | Bewerking | Micro soft. AzureActiveDirectory/b2ctenants/lezen | Een lijst met alle B2C-tenants waarbij de gebruiker lid is |
> | Bewerking | Micro soft. AzureActiveDirectory/Operations/lezen | Alle API-bewerkingen lezen die beschikbaar zijn voor de resource provider micro soft. AzureActiveDirectory |
> | Bewerking | Micro soft. AzureActiveDirectory/REGI ster/actie | Het abonnement voor de resource provider micro soft. AzureActiveDirectory registreren |

## <a name="microsoftazurestack"></a>Micro soft. AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. AzureStack/Operations/lezen | Hiermee worden de eigenschappen van een resource provider bewerking opgehaald |
> | Bewerking | Micro soft. AzureStack/REGI ster/actie | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. AzureStack |
> | Bewerking | Micro soft. AzureStack/registraties/customerSubscriptions/verwijderen | Hiermee verwijdert u een Azure Stack klant abonnement |
> | Bewerking | Micro soft. AzureStack/registraties/customerSubscriptions/lezen | Hiermee worden de eigenschappen van een Azure Stack klant abonnement opgehaald |
> | Bewerking | Micro soft. AzureStack/registraties/customerSubscriptions/schrijven | Hiermee wordt een Azure Stack klant abonnement gemaakt of bijgewerkt |
> | Bewerking | Micro soft. AzureStack/registraties/verwijderen | Hiermee wordt een Azure Stack registratie verwijderd |
> | Bewerking | Micro soft. AzureStack/registraties/getActivationKey/actie | Hiermee wordt de nieuwste Azure Stack activerings sleutel opgehaald |
> | Bewerking | Micro soft. AzureStack/registraties/producten/listDetails/actie | Hiermee worden uitgebreide details opgehaald voor een Azure Stack Marketplace-product |
> | Bewerking | Micro soft. AzureStack/registraties/producten/lezen | Hiermee worden de eigenschappen van een Azure Stack Marketplace-product opgehaald |
> | Bewerking | Micro soft. AzureStack/registraties/producten/uploadProductLog/actie | Status en tijds tempel van de product bewerkings Azure Stack van de Marketplace vastleggen |
> | Bewerking | Micro soft. AzureStack/registraties/lezen | Hiermee worden de eigenschappen van een Azure Stack registratie opgehaald |
> | Bewerking | Micro soft. AzureStack/registraties/schrijven | Hiermee wordt een Azure Stack registratie gemaakt of bijgewerkt |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. batch/batchAccounts/toepassingen/verwijderen | Hiermee wordt een toepassing verwijderd |
> | Bewerking | Micro soft. batch/batchAccounts/toepassingen/lezen | Een lijst met toepassingen of de eigenschappen van een toepassing ophalen |
> | Bewerking | Micro soft. batch/batchAccounts/toepassingen/versies/activeren/actie | Hiermee wordt een toepassings pakket geactiveerd |
> | Bewerking | Micro soft. batch/batchAccounts/toepassingen/versies/verwijderen | Hiermee wordt een toepassings pakket verwijderd |
> | Bewerking | Micro soft. batch/batchAccounts/toepassingen/versies/lezen | Hiermee worden de eigenschappen van een toepassings pakket opgehaald |
> | Bewerking | Micro soft. batch/batchAccounts/toepassingen/versies/schrijven | Hiermee wordt een nieuw toepassings pakket gemaakt of een bestaand toepassings pakket bijgewerkt |
> | Bewerking | Micro soft. batch/batchAccounts/toepassingen/schrijven | Hiermee wordt een nieuwe toepassing gemaakt of een bestaande toepassing bijgewerkt |
> | Bewerking | Micro soft. batch/batchAccounts/certificateOperationResults/lezen | Hiermee worden de resultaten opgehaald van een langlopende certificaat bewerking voor een batch-account |
> | Bewerking | Micro soft. batch/batchAccounts/certificaten/cancelDelete/actie | Hiermee wordt het verwijderen van een certificaat voor een batch-account geannuleerd |
> | Bewerking | Micro soft. batch/batchAccounts/certificaten/verwijderen | Hiermee wordt een certificaat uit een batch-account verwijderd |
> | Bewerking | Micro soft. batch/batchAccounts/certificaten/lezen | Een lijst met certificaten voor een batch-account of de eigenschappen van een certificaat ophalen |
> | Bewerking | Micro soft. batch/batchAccounts/certificaten/schrijven | Hiermee maakt u een nieuw certificaat op een batch-account of werkt u een bestaand certificaat bij |
> | Bewerking | Micro soft. batch/batchAccounts/verwijderen | Hiermee verwijdert u een batch-account |
> | DataAction | Micro soft. batch/batchAccounts/Jobs/verwijderen | Hiermee wordt een taak verwijderd uit een batch-account |
> | DataAction | Micro soft. batch/batchAccounts/Jobs/lezen | Een lijst met taken voor een batch-account of de eigenschappen van een taak ophalen |
> | DataAction | Micro soft. batch/batchAccounts/Jobs/schrijven | Hiermee maakt u een nieuwe taak voor een batch-account of werkt u een bestaande taak bij |
> | DataAction | Micro soft. batch/batchAccounts/jobSchedules/verwijderen | Hiermee verwijdert u een taak schema van een batch-account |
> | DataAction | Micro soft. batch/batchAccounts/jobSchedules/lezen | Een lijst met taak planningen voor een batch-account of de eigenschappen van een taak schema ophalen |
> | DataAction | Micro soft. batch/batchAccounts/jobSchedules/schrijven | Hiermee wordt een nieuwe taak planning gemaakt voor een batch-account of wordt een bestaande taak planning bijgewerkt |
> | Bewerking | Micro soft. batch/batchAccounts/listkeys ophalen/actie | Een lijst met toegangs sleutels voor een batch-account |
> | Bewerking | Micro soft. batch/batchAccounts/operationResults/lezen | Hiermee worden de resultaten van een langlopende batch-account bewerking opgehaald |
> | Bewerking | Micro soft. batch/batchAccounts/poolOperationResults/lezen | Hiermee worden de resultaten van een langlopende pool bewerking voor een batch-account opgehaald |
> | Bewerking | Micro soft. batch/batchAccounts/Pools/verwijderen | Hiermee verwijdert u een groep van een batch-account |
> | Bewerking | Micro soft. batch/batchAccounts/Pools/disableAutoscale/actie | Hiermee schakelt u automatisch schalen uit voor een batch-account groep |
> | Bewerking | Micro soft. batch/batchAccounts/Pools/lezen | Een lijst met groepen in een batch-account of de eigenschappen van een groep ophalen |
> | Bewerking | Micro soft. batch/batchAccounts/Pools/stopResize/actie | Hiermee wordt een actieve bewerking voor het wijzigen van de grootte van een batch-account groep gestopt |
> | Bewerking | Micro soft. batch/batchAccounts/Pools/schrijven | Hiermee wordt een nieuwe pool gemaakt op een batch-account of een bestaande groep bijgewerkt |
> | Bewerking | Micro soft. batch/batchAccounts/lezen | Een lijst met batch-accounts of de eigenschappen van een batch-account |
> | Bewerking | Micro soft. batch/batchAccounts/regeneratekeys/actie | Hiermee worden de toegangs sleutels voor een batch-account opnieuw gegenereerd |
> | Bewerking | Micro soft. batch/batchAccounts/syncAutoStorageKeys/actie | Synchroniseert toegangs sleutels voor het automatische opslag account dat is geconfigureerd voor een batch-account |
> | Bewerking | Micro soft. batch/batchAccounts/schrijven | Hiermee maakt u een nieuw batch-account of werkt u een bestaand batch-account bij |
> | Bewerking | Micro soft. batch/locaties/accountOperationResults/lezen | Hiermee worden de resultaten van een langlopende batch-account bewerking opgehaald |
> | Bewerking | Micro soft. batch/locaties/checkNameAvailability/actie | Hiermee wordt gecontroleerd of de account naam geldig is en niet wordt gebruikt. |
> | Bewerking | Micro soft. batch/locaties/quota's/lezen | Hiermee worden de batch quota's van het opgegeven abonnement in de opgegeven Azure-regio opgehaald |
> | Bewerking | Micro soft. batch/Operations/lezen | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn voor de resource provider van micro soft. batch |
> | Bewerking | Micro soft. batch/registreren/actie | Hiermee wordt het abonnement voor de batch-resource provider geregistreerd en wordt het maken van batch-accounts ingeschakeld |
> | Bewerking | Micro soft. batch/registratie/actie | Hiermee maakt u de registratie van het abonnement voor de batch-resource provider ongedaan voor het maken van batch-accounts. |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. facturering/billingAccounts/overeenkomsten/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingPermissions/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingProfiles/billingPermissions/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingProfiles/klanten/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingProfiles/facturen/prijzen overzicht/downloaden/actie |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingProfiles/invoiceSections/billingPermissions/lezen |  |
> | Bewerking | Micro soft. billing/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/Move/Action |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/overdracht/actie |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/actie |  |
> | Bewerking | Micro soft. billing/billingAccounts/billingProfiles/invoiceSections/Products/Move/Action |  |
> | Bewerking | Micro soft. billing/billingAccounts/billingProfiles/invoiceSections/Products/overboeking/actie |  |
> | Bewerking | Micro soft. billing/billingAccounts/billingProfiles/invoiceSections/Products/validateMoveEligibility/Action |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingProfiles/invoiceSections/lezen |  |
> | Bewerking | Micro soft. billing/billingAccounts/billingProfiles/invoiceSections/write |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingProfiles/prijzen overzicht/downloaden/actie |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingProfiles/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingProfiles/schrijven |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingProfiles/schrijven |  |
> | Bewerking | Micro soft. facturering/billingAccounts/billingSubscriptions/lezen |  |
> | Bewerking | Micro soft. billing/billingAccounts/klanten/billingPermissions/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/klanten/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/afdelingen/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/enrollmentAccounts/billingPermissions/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/enrollmentAccounts/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/enrollmentDepartments/billingPermissions/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/actie |  |
> | Bewerking | Micro soft. facturering/billingAccounts/producten/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/lezen |  |
> | Bewerking | Micro soft. facturering/billingAccounts/schrijven |  |
> | Bewerking | Micro soft. facturering/afdelingen/lezen |  |
> | Bewerking | Micro soft. facturering/registreren/actie |  |
> | Bewerking | Micro soft. facturering/validateAddress/actie |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. BingMaps/mapApis/verwijderen | Verwijder bewerking |
> | Bewerking | Micro soft. BingMaps/mapApis/listSecrets/Action | De geheimen weer geven |
> | Bewerking | Micro soft. BingMaps/mapApis/listSingleSignOnToken/Action | Verificatie token voor eenmalige aanmelding voor de resource lezen |
> | Bewerking | Micro soft. BingMaps/mapApis/lezen | Lees bewerking |
> | Bewerking | Micro soft. BingMaps/mapApis/regenerateKey/Action | Hiermee wordt de sleutel opnieuw gegenereerd |
> | Bewerking | Micro soft. BingMaps/mapApis/schrijven | Schrijf bewerking |
> | Bewerking | Micro soft. BingMaps/Operations/lezen | Beschrijving van de bewerking. |

## <a name="microsoftblockchain"></a>Micro soft. Block Chain

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Block Chain/blockchainMembers/verwijderen | Hiermee verwijdert u een bestaand Block Chain-lid. |
> | Bewerking | Micro soft. Block Chain/blockchainMembers/listApiKeys/Action | Hiermee worden bestaande Block Chain member API-sleutels opgehaald of weer gegeven. |
> | Bewerking | Micro soft. Block Chain/blockchainMembers/lezen | Hiermee wordt een lijst met bestaande Block Chain-leden opgehaald of weer gegeven. |
> | DataAction | Micro soft. Block Chain/blockchainMembers/transactionNodes/Connect/Action | Maakt verbinding met een trans actie-knoop punt van het block Chain-lid. |
> | Bewerking | Micro soft. Block Chain/blockchainMembers/transactionNodes/verwijderen | Hiermee verwijdert u een bestaand Block Chain member Trans Action-knoop punt. |
> | Bewerking | Micro soft. Block Chain/blockchainMembers/transactionNodes/listApiKeys/Action | Hiermee worden de bestaande API-sleutels van het block Chain member-trans actie knooppunt opgehaald of weer gegeven. |
> | Bewerking | Micro soft. Block Chain/blockchainMembers/transactionNodes/lezen | Hiermee wordt een lijst met bestaande Block Chain-leden transactie knooppunten opgehaald of weer gegeven. |
> | Bewerking | Micro soft. Block Chain/blockchainMembers/transactionNodes/write | Hiermee wordt een Block Chain-lid-transactie knooppunt gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Block Chain/blockchainMembers/schrijven | Hiermee wordt een Block Chain-lid gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Block Chain/locaties/blockchainMemberOperationResults/lezen | Hiermee haalt u de bewerkings resultaten van Block Chain-leden op. |
> | Bewerking | Micro soft. Block Chain/locaties/checkNameAvailability/actie | Hiermee wordt gecontroleerd of de resource naam geldig is en niet wordt gebruikt. |
> | Bewerking | Micro soft. Block Chain/Operations/lezen | Alle bewerkingen in de resource provider van micro soft Block Chain weer geven. |
> | Bewerking | Micro soft. Block Chain/REGI ster/actie | Hiermee wordt het abonnement voor de Block Chain-resource provider geregistreerd. |

## <a name="microsoftblueprint"></a>Micro soft. blauw druk

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. blauw druk/blueprintAssignments/assignmentOperations/lezen | Alle blauw drukken artefacten lezen |
> | Bewerking | Micro soft. blauw druk/blueprintAssignments/verwijderen | Blauw druk artefacten verwijderen |
> | Bewerking | Micro soft. blauw druk/blueprintAssignments/lezen | Alle blauw drukken artefacten lezen |
> | Bewerking | Micro soft. blauw druk/blueprintAssignments/whoisblueprint/actie | De object-id van de service-principal voor Azure blauw drukken ophalen. |
> | Bewerking | Micro soft. blauw druk/blueprintAssignments/schrijven | Blauw drukken artefacten maken of bijwerken |
> | Bewerking | Micro soft. blauw druk/blauw drukken/artefacten/verwijderen | Blauw druk artefacten verwijderen |
> | Bewerking | Micro soft. blauw druk/blauw drukken/artefacten/lezen | Alle blauw drukken artefacten lezen |
> | Bewerking | Micro soft. blauw druk/blauw drukken/artefacten/schrijven | Blauw drukken artefacten maken of bijwerken |
> | Bewerking | Micro soft. blauw druk/blauw drukken/verwijderen | Blauw drukken verwijderen |
> | Bewerking | Micro soft. blauw druk/blauw drukken/lezen | Alle blauw drukken lezen |
> | Bewerking | Micro soft. blauw druk/blauw drukken/versies/artefacten/lezen | Alle blauw drukken artefacten lezen |
> | Bewerking | Micro soft. blauw druk/blauw drukken/versies/verwijderen | Blauw drukken verwijderen |
> | Bewerking | Micro soft. blauw druk/blauw drukken/versies/lezen | Alle blauw drukken lezen |
> | Bewerking | Micro soft. blauw druk/blauw drukken/versies/schrijven | Blauw drukken maken of bijwerken |
> | Bewerking | Micro soft. blauw druk/blauw drukken/schrijven | Blauw drukken maken of bijwerken |
> | Bewerking | Micro soft. blauw druk/registreren/actie | Hiermee wordt de resource provider voor Azure-blauw drukken geregistreerd |

## <a name="microsoftbotservice"></a>Micro soft. BotService

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. BotService/botServices/kanalen/verwijderen | Een bot-service verwijderen |
> | Bewerking | Micro soft. BotService/botServices/channels/lezen | Een bot-service lezen |
> | Bewerking | Micro soft. BotService/botServices/channels/write | Een bot-service schrijven |
> | Bewerking | Micro soft. BotService/botServices/Connections/Delete | Een bot-service verwijderen |
> | Bewerking | Micro soft. BotService/botServices/Connections/Read | Een bot-service lezen |
> | Bewerking | Micro soft. BotService/botServices/Connections/write | Een bot-service schrijven |
> | Bewerking | Micro soft. BotService/botServices/verwijderen | Een bot-service verwijderen |
> | Bewerking | Micro soft. BotService/botServices/lezen | Een bot-service lezen |
> | Bewerking | Micro soft. BotService/botServices/schrijven | Een bot-service schrijven |
> | Bewerking | Micro soft. BotService/locaties/operationresults/lezen | De status van een asynchrone bewerking lezen |
> | Bewerking | Micro soft. BotService/Operations/lezen | De bewerkingen voor alle resource typen lezen |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. cache/checknameavailability/actie | Hiermee wordt gecontroleerd of een naam beschikbaar is voor gebruik met een nieuwe Redis Cache |
> | Bewerking | Micro soft. cache/locaties/operationresults/lezen | Hiermee wordt het resultaat van een langlopende bewerking opgehaald waarvoor de header ' Location ' eerder naar de client is geretourneerd |
> | Bewerking | Micro soft. cache/Operations/lezen | Geeft een lijst van de bewerkingen die worden ondersteund door de provider micro soft. cache. |
> | Bewerking | Micro soft. cache/redis/verwijderen | De hele Redis Cache verwijderen |
> | Bewerking | Micro soft. cache/redis/exporteren/actie | Redis gegevens exporteren naar voor voegsels voor vaste opslag-blobs in de opgegeven indeling |
> | Bewerking | Micro soft. cache/redis/firewallRules/verwijderen | IP-firewall regels van een Redis Cache verwijderen |
> | Bewerking | Micro soft. cache/redis/firewallRules/lezen | De IP-firewall regels van een Redis Cache ophalen |
> | Bewerking | Micro soft. cache/redis/firewallRules/schrijven | De IP-firewall regels van een Redis Cache bewerken |
> | Bewerking | Micro soft. cache/redis/forceReboot/actie | Een cache-exemplaar geforceerd opnieuw opstarten, mogelijk met gegevens verlies. |
> | Bewerking | Micro soft. cache/redis/importeren/actie | Gegevens van een opgegeven indeling importeren uit meerdere blobs in redis |
> | Bewerking | Micro soft. cache/redis/linkedservers/verwijderen | Een gekoppelde server verwijderen uit een Redis Cache |
> | Bewerking | Micro soft. cache/redis/linkedservers/lezen | Gekoppelde servers ophalen die zijn gekoppeld aan een redis-cache. |
> | Bewerking | Micro soft. cache/redis/linkedservers/schrijven | Een gekoppelde server toevoegen aan een Redis Cache |
> | Bewerking | Micro soft. cache/redis/Listkeys ophalen/actie | De waarde van Redis Cache toegangs sleutels in de beheer portal weer geven |
> | Bewerking | Micro soft. cache/redis/listUpgradeNotifications/lezen | Vermeld de nieuwste upgrade meldingen voor de cache-Tenant. |
> | Bewerking | Micro soft. cache/redis/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor een Redis Cache |
> | Bewerking | Micro soft. cache/redis/patchSchedules/verwijderen | Het patch schema van een Redis Cache verwijderen |
> | Bewerking | Micro soft. cache/redis/patchSchedules/lezen | Hiermee wordt het patch-schema van een Redis Cache opgehaald |
> | Bewerking | Micro soft. cache/redis/patchSchedules/schrijven | Het patch-schema van een Redis Cache wijzigen |
> | Bewerking | Micro soft. cache/redis/lezen | De instellingen en configuratie van het Redis Cache in de beheer portal weer geven |
> | Bewerking | Micro soft. cache/redis/regenerateKey/actie | De waarde van Redis Cache toegangs sleutels in de beheer portal wijzigen |
> | Bewerking | Micro soft. cache/redis/start/actie | Start een cache-exemplaar. |
> | Bewerking | Micro soft. cache/redis/stop/actie | Een cache-exemplaar stoppen. |
> | Bewerking | Micro soft. cache/redis/schrijven | De instellingen en configuratie van de Redis Cache in de beheer portal wijzigen |
> | Bewerking | Micro soft. cache/registreren/actie | Hiermee wordt de resource provider micro soft. cache geregistreerd bij een abonnement |
> | Bewerking | Micro soft. cache/registratie/actie | Hiermee wordt de registratie van de resource provider micro soft. cache bij een abonnement ongedaan gemaakt |

## <a name="microsoftcapacity"></a>Micro soft. capacity

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. capacity/appliedreservations/lezen | Alle reserve ringen lezen |
> | Bewerking | Micro soft. capacity/calculateexchange/actie | Hiermee worden de Exchange-hoeveelheid en de prijs van nieuwe aankoop berekend en worden er beleids fouten geretourneerd. |
> | Bewerking | Micro soft. capacity/calculateprice/actie | De reserverings prijs berekenen |
> | Bewerking | Micro soft. capacity/catalogi/lezen | Catalogus van reserve ring lezen |
> | Bewerking | Micro soft. capacity/checkoffers/actie | Alle abonnements aanbiedingen controleren |
> | Bewerking | Micro soft. capacity/checkscopes/actie | Een abonnement controleren |
> | Bewerking | Micro soft. capacity/commercialreservationorders/lezen | Reserverings orders ophalen die zijn gemaakt in een Tenant |
> | Bewerking | Micro soft. capacity/Exchange/actie | Alle reserve ringen uitwisselen |
> | Bewerking | Micro soft. capacity/Operations/lezen | Een wille keurige bewerking lezen |
> | Bewerking | Micro soft. capacity/REGI ster/actie | Hiermee wordt de resource provider voor capaciteit geregistreerd en wordt het maken van capaciteits resources ingeschakeld. |
> | Bewerking | Micro soft. capacity/reservationorders/actie | Een reserve ring bijwerken |
> | Bewerking | Micro soft. capacity/reservationorders/availablescopes/actie | Beschik bare bereik zoeken |
> | Bewerking | Micro soft. capacity/reservationorders/calculaterefund/actie | Hiermee worden de terugbetalings hoeveelheid en de prijs van nieuwe aankoop berekend en worden er beleids fouten geretourneerd. |
> | Bewerking | Micro soft. capacity/reservationorders/verwijderen | Een reserve ring verwijderen |
> | Bewerking | Micro soft. capacity/reservationorders/samen voegen/actie | Een reserve ring samen voegen |
> | Bewerking | Micro soft. capacity/reservationorders/lezen | Alle reserve ringen lezen |
> | Bewerking | Micro soft. capacity/reservationorders/reserve ringen/actie | Een reserve ring bijwerken |
> | Bewerking | Micro soft. capacity/reservationorders/reserve ringen/availablescopes/actie | Beschik bare bereik zoeken |
> | Bewerking | Micro soft. capacity/reservationorders/reserve ringen/verwijderen | Een reserve ring verwijderen |
> | Bewerking | Micro soft. capacity/reservationorders/reserve ringen/lezen | Alle reserve ringen lezen |
> | Bewerking | Micro soft. capacity/reservationorders/reserve ringen/revisies/lezen | Alle reserve ringen lezen |
> | Bewerking | Micro soft. capacity/reservationorders/reserve ringen/schrijven | Een reserve ring maken |
> | Bewerking | Micro soft. capacity/reservationorders/retouren/actie | Reserve ring retour neren |
> | Bewerking | Micro soft. capacity/reservationorders/Split/actie | Alle reserve ringen splitsen |
> | Bewerking | Micro soft. capacity/reservationorders/swap/Action | Alle reserve ringen omwisselen |
> | Bewerking | Micro soft. capacity/reservationorders/schrijven | Een reserve ring maken |
> | Bewerking | Micro soft. capacity/tenants/registreren/actie | Een Tenant registreren |
> | Bewerking | Micro soft. capacity/registratie/actie | De registratie van een Tenant ongedaan maken |
> | Bewerking | Micro soft. capacity/validatereservationorder/actie | Een reserve ring valideren |

## <a name="microsoftcdn"></a>Micro soft. CDN

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. CDN/CheckNameAvailability/actie |  |
> | Bewerking | Micro soft. CDN/CheckResourceUsage/actie |  |
> | Bewerking | Micro soft. CDN/edgenodes/verwijderen |  |
> | Bewerking | Micro soft. CDN/edgenodes/lezen |  |
> | Bewerking | Micro soft. CDN/edgenodes/schrijven |  |
> | Bewerking | Micro soft. CDN/operationresults/verwijderen |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/CheckResourceUsage/actie |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/verwijderen |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/CheckResourceUsage/Action |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/customdomainresults/verwijderen |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/Action |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/Action |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/customdomainresults/lezen |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/customdomainresults/schrijven |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/verwijderen |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/load/Action |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/originresults/verwijderen |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/originresults/lezen |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/originresults/schrijven |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/opschonen/actie |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/lezen |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/start/Action |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/stop/Action |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/ValidateCustomDomain/Action |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/endpointresults/write |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/GenerateSsoUri/actie |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/GetSupportedOptimizationTypes/actie |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/lezen |  |
> | Bewerking | Micro soft. CDN/operationresults/profileresults/schrijven |  |
> | Bewerking | Micro soft. CDN/operationresults/lezen |  |
> | Bewerking | Micro soft. CDN/operationresults/schrijven |  |
> | Bewerking | Micro soft. CDN/bewerkingen/lezen |  |
> | Bewerking | Micro soft. CDN/profielen/CheckResourceUsage/actie |  |
> | Bewerking | Micro soft. CDN/profielen/verwijderen |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/CheckResourceUsage/actie |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/customdomains/verwijderen |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/customdomains/DisableCustomHttps/actie |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/customdomains/EnableCustomHttps/actie |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/customdomains/lezen |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/customdomains/schrijven |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/verwijderen |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/laden/actie |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/oorsprong/verwijderen |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/oorsprongen/lezen |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/oorsprongen/schrijven |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/opschonen/actie |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/lezen |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/starten/actie |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/stoppen/actie |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/ValidateCustomDomain/actie |  |
> | Bewerking | Micro soft. CDN/profielen/eind punten/schrijven |  |
> | Bewerking | Micro soft. CDN/profielen/GenerateSsoUri/actie |  |
> | Bewerking | Micro soft. CDN/profielen/GetSupportedOptimizationTypes/actie |  |
> | Bewerking | Micro soft. CDN/profielen/lezen |  |
> | Bewerking | Micro soft. CDN/profielen/schrijven |  |
> | Bewerking | Micro soft. CDN/registreren/actie | Hiermee wordt het abonnement voor de CDN-resource provider geregistreerd en wordt het maken van CDN-profielen ingeschakeld. |
> | Bewerking | Micro soft. CDN/ValidateProbe/actie |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/certificaten/verwijderen | Een bestaand certificaat verwijderen |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/certificaten/lezen | De lijst met certificaten ophalen |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/certificates/write | Een nieuw certificaat toevoegen of een bestaande bijwerken |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/verwijderen | Een bestaande AppServiceCertificate verwijderen |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/lezen | De lijst met CertificateOrders ophalen |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/opnieuw uitgeven/actie | Een bestaande certificaat bestelling opnieuw uitgeven |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/renew/actie | Een bestaande certificaat bestelling vernieuwen |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/resendEmail/Action | E-mail van certificaat opnieuw verzenden |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/resendRequestEmails/Action | E-mail met aanvraag opnieuw verzenden naar een ander e-mail adres |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/resendRequestEmails/Action | Site zegel ophalen voor een uitgegeven App Service Certificate |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | De lijst met certificaat acties ophalen |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | E-mail geschiedenis van certificaat ophalen |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Domein eigendom verifiëren |
> | Bewerking | Micro soft. CertificateRegistration/certificateOrders/schrijven | Een nieuwe certificaat bestelling toevoegen of een bestaand item bijwerken |
> | Bewerking | Micro soft. CertificateRegistration/Operations/lezen | Alle bewerkingen van de registratie van het app service-certificaat weer geven |
> | Bewerking | Micro soft. CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/actie | Service-Principal inrichten voor service-app-Principal |
> | Bewerking | Micro soft. CertificateRegistration/REGI ster/actie | De resource provider van micro soft-certificaten registreren voor het abonnement |
> | Bewerking | Micro soft. CertificateRegistration/validateCertificateRegistrationInformation/actie | Het certificaat aankoop object valideren zonder dit te verzenden |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ClassicCompute/capabilities/lezen | Hiermee worden de mogelijkheden |
> | Bewerking | Micro soft. ClassicCompute/checkDomainNameAvailability/actie | Hiermee wordt de beschik baarheid van een bepaalde domein naam gecontroleerd. |
> | Bewerking | Micro soft. ClassicCompute/checkDomainNameAvailability/lezen | Hiermee wordt de beschik baarheid van een bepaalde domein naam opgehaald. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/actief/schrijven | Hiermee stelt u de naam van het actieve domein. |
> | Bewerking | Micro soft. ClassicCompute/domainName/Availability sets/lezen | De beschikbaarheidsset weer geven voor de resource. |
> | Bewerking | Micro soft. ClassicCompute/domainName/capabilities/mogelijkheden/lezen | Hiermee worden de domein naam mogelijkheden weer gegeven |
> | Bewerking | Micro soft. ClassicCompute/domainName/verwijderen | De domein namen voor resources verwijderen. |
> | Bewerking | Micro soft. ClassicCompute/domainName/deploymentslots/lezen | Hiermee worden de implementatie sites weer gegeven. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/deploymentslots/roles/lezen | Rol voor implementatie sleuf van domein naam ophalen |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/deploymentslots/roles/roleinstances/lezen | Rolinstantie ophalen voor rol op implementatie site van domein naam |
> | Bewerking | Micro soft. ClassicCompute/domein naam/deploymentslots/status/lezen | Haal de status van de implementatie site op. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/deploymentslots/status/schrijven | Voeg de status van de implementatie site toe. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/deploymentslots/upgrade Domain/lezen | Upgrade domein ophalen voor implementatie site op domein naam |
> | Bewerking | Micro soft. ClassicCompute/domein-en deploymentslots/upgrade Domain/schrijven | Upgrade domein bijwerken voor implementatie site op domein naam |
> | Bewerking | Micro soft. ClassicCompute/domainName/deploymentslots/schrijven | Hiermee wordt de implementatie gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/uitbrei dingen/verwijderen | Verwijder de domeinnaam extensies. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/uitbrei dingen/operationStatuses/lezen | Hiermee wordt de bewerkings status van de domeinnaam extensies gelezen. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/extensies/uitbrei dingen/lezen | Retourneert de domeinnaam extensies. |
> | Bewerking | Micro soft. ClassicCompute/domein-en uitbrei dingen/extensies schrijven | Voeg de domeinnaam extensies toe. |
> | Bewerking | Micro soft. ClassicCompute/domainName/internalLoadBalancers/verwijderen | Verwijder een nieuwe interne taak verdeling. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/internalLoadBalancers/operationStatuses/lezen | Hiermee wordt de bewerkings status van de interne load balancers van domein namen gelezen. |
> | Bewerking | Micro soft. ClassicCompute/domainName/internalLoadBalancers/lezen | Hiermee worden de interne load balancers opgehaald. |
> | Bewerking | Micro soft. ClassicCompute/domainName/internalLoadBalancers/schrijven | Hiermee maakt u een nieuwe interne taak verdeling. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/loadBalancedEndpointSets/operationStatuses/lezen | Hiermee wordt de bewerkings status van de eindpunt sets met gelijke taak verdeling van domein namen gelezen. |
> | Bewerking | Micro soft. ClassicCompute/domainName/loadBalancedEndpointSets/lezen | De eindpunt sets met gelijke taak verdeling ophalen. |
> | Bewerking | Micro soft. ClassicCompute/domainName/loadBalancedEndpointSets/schrijven | Voeg de eindpuntset met gelijke taak verdeling toe. |
> | Bewerking | Micro soft. ClassicCompute/domainName/operationstatuses/lezen | De bewerkings status van de domein naam ophalen. |
> | Bewerking | Micro soft. ClassicCompute/domainName/operationStatuses/lezen | Hiermee wordt de bewerkings status van de domeinnaam extensies gelezen. |
> | Bewerking | Micro soft. ClassicCompute/domainName/lezen | De domein namen voor resources retour neren. |
> | Bewerking | Micro soft. ClassicCompute/domainName/serviceCertificates/verwijderen | De gebruikte service certificaten verwijderen. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/serviceCertificates/operationStatuses/lezen | Hiermee wordt de bewerkings status van de service certificaten van domein namen gelezen. |
> | Bewerking | Micro soft. ClassicCompute/domainName/serviceCertificates/lezen | Hiermee worden de gebruikte service certificaten geretourneerd. |
> | Bewerking | Micro soft. ClassicCompute/domainName/serviceCertificates/schrijven | De gebruikte service certificaten toevoegen of wijzigen. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/abortMigration/actie | De migratie van een implementatie site afbreekt. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/commitMigration/actie | Hiermee wordt de migratie van een implementatie site doorgevoerd. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/sleuven/verwijderen | Hiermee verwijdert u een opgegeven implementatie sleuf. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/sleuven/operationStatuses/lezen | Hiermee wordt de bewerkings status van de domeinnaam sleuven gelezen. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/prepareMigration/actie | Bereidt de migratie van een implementatie site voor. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/sleuven/lezen | Hiermee worden de implementatie sites weer gegeven. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/rollen/extensionReferences/verwijderen | Verwijder de extensie verwijzing voor de implementatie site-rol. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/rollen/extensionReferences/operationStatuses/lezen | Hiermee wordt de bewerkings status van de extensies voor de extensie rollen van domein namen sleuven gelezen. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/sleuven/rollen/extensionReferences/lezen | Retourneert de extensie verwijzing voor de implementatie site-rol. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/rollen/extensionReferences/schrijven | De extensie verwijzing voor de implementatie site functie toevoegen of wijzigen. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/sleuven/rollen/metricdefinitions/lezen | De metrische definitie van de roldefinitie voor de domein naam ophalen. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/rollen/metrieken/lezen | Haal de metrische gegevens voor de rol voor de domein naam op. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/sleuven/rollen/operationstatuses/lezen | De bewerkings status voor de rol van de domeinnaam sleuf ophalen. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/sleuven/rollen/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/rollen/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/sleuven/rollen/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/sleuven/rollen/lezen | Haal de rol op voor de implementatie site. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/rollen/roleInstances/downloadremotedesktopconnectionfile/actie | Down loadt extern bureau blad-verbindings bestand voor de rolinstantie voor de rol van domein naam sleuf. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/rollen/roleInstances/operationStatuses/lezen | Hiermee wordt de bewerkings status van de rolinstantie voor de rol van de domein naam sleuf opgehaald. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/sleuven/rollen/roleInstances/lezen | Haal de rolinstantie op. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/rollen/roleInstances/opnieuw samen stellen/actie | Maakt de rolinstantie opnieuw. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/rollen/roleInstances/opnieuw installatie kopie/actie | Installatie kopieën van de rolinstantie terugzetten. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/rollen/roleInstances/opnieuw opstarten/actie | Hiermee worden rolinstanties opnieuw gestart. |
> | Bewerking | Micro soft. ClassicCompute/domainNamees/sleuven/rollen/sku's/lezen | Haal de rol-SKU voor de implementatie site op. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/sleuven/rollen/schrijven | Voeg een rol toe voor de implementatie site. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/sleuven/starten/actie | Hiermee wordt een implementatie site gestart. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/sleuven/status/starten/schrijven | Hiermee wordt de status van de implementatie site gewijzigd in gestopt. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/sleuven/status/stoppen/schrijven | Hiermee wordt de status van de implementatie site gewijzigd in gestart. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/sleuven/stoppen/actie | Hiermee wordt de implementatie site onderbroken. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/sleuven/upgrade Domain/schrijven | Voer een upgrade uit voor het domein. |
> | Bewerking | Micro soft. ClassicCompute/Domeins/sleuven/validateMigration/actie | Hiermee wordt de migratie van een implementatie site gevalideerd. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/sleuven/schrijven | Hiermee wordt de implementatie gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ClassicCompute/domein naam/wisselen/actie | De faserings sleuf wordt overgewisseld naar de productie sleuf. |
> | Bewerking | Micro soft. ClassicCompute/domein-en schrijf bewerkingen | De domein namen voor resources toevoegen of wijzigen. |
> | Bewerking | Micro soft. ClassicCompute/moveSubscriptionResources/actie | Verplaats alle klassieke resources naar een ander abonnement. |
> | Bewerking | Micro soft. ClassicCompute/operatingSystemFamilies/lezen | Geeft een lijst van de gast besturingssysteem families die beschikbaar zijn in Microsoft Azure en bevat ook een lijst met de besturingssysteem versies die beschikbaar zijn voor elke familie. |
> | Bewerking | Micro soft. ClassicCompute/operatingSystems/lezen | Hier worden de versies weer gegeven van het gast besturingssysteem dat momenteel beschikbaar is in Microsoft Azure. |
> | Bewerking | Micro soft. ClassicCompute/Operations/lezen | Hiermee wordt de lijst met bewerkingen opgehaald. |
> | Bewerking | Micro soft. ClassicCompute/operationStatuses/lezen | Hiermee wordt de bewerkings status van de resource gelezen. |
> | Bewerking | Micro soft. ClassicCompute/quota's/lezen | Het quotum voor het abonnement ophalen. |
> | Bewerking | Micro soft. ClassicCompute/REGI ster/actie | Registreren bij klassieke compute |
> | Bewerking | Micro soft. ClassicCompute/resourceTypes/sku's/lezen | Hiermee wordt de SKU-lijst opgehaald voor ondersteunde resource typen. |
> | Bewerking | Micro soft. ClassicCompute/validateSubscriptionMoveAvailability/actie | Valideer de beschik baarheid van het abonnement voor een klassieke Verplaats bewerking. |
> | Bewerking | Micro soft. ClassicCompute/informatie/associatedNetworkSecurityGroups/verwijderen | Hiermee verwijdert u de netwerk beveiligings groep die is gekoppeld aan de virtuele machine. |
> | Bewerking | Micro soft. ClassicCompute/informatie/associatedNetworkSecurityGroups/operationStatuses/lezen | Hiermee wordt de bewerkings status gelezen van de virtuele machines die zijn gekoppeld aan netwerk beveiligings groepen. |
> | Bewerking | Micro soft. ClassicCompute/informatie/associatedNetworkSecurityGroups/lezen | Hiermee wordt de netwerk beveiligings groep opgehaald die aan de virtuele machine is gekoppeld. |
> | Bewerking | Micro soft. ClassicCompute/informatie/associatedNetworkSecurityGroups/write | Hiermee wordt een netwerk beveiligings groep toegevoegd die aan de virtuele machine is gekoppeld. |
> | Bewerking | Micro soft. ClassicCompute/informatie/asyncOperations/lezen | Hiermee worden de mogelijke asynchrone bewerkingen opgehaald |
> | Bewerking | Micro soft. ClassicCompute/informatie/attachDisk/Action | Hiermee koppelt u een gegevens schijf aan een virtuele machine. |
> | Bewerking | Micro soft. ClassicCompute/informatie/Capture/actie | Een virtuele machine vastleggen. |
> | Bewerking | Micro soft. ClassicCompute/informatie/verwijderen | Virtuele machines worden verwijderd. |
> | Bewerking | Micro soft. ClassicCompute/informatie/detachDisk/Action | Een gegevens schijf loskoppelen van de virtuele machine. |
> | Bewerking | Micro soft. ClassicCompute/informatie/diagnosticsettings/lezen | Diagnostische instellingen van virtuele machine ophalen. |
> | Bewerking | Micro soft. ClassicCompute/informatie/schijven/lezen | Hiermee wordt een lijst met gegevens schijven opgehaald |
> | Bewerking | Micro soft. ClassicCompute/informatie/downloadRemoteDesktopConnectionFile/Action | Hiermee downloadt u het RDP-bestand voor de virtuele machine. |
> | Bewerking | Micro soft. ClassicCompute/informatie/Extensions/operationStatuses/lezen | Hiermee wordt de bewerkings status van de extensies van de virtuele machines gelezen. |
> | Bewerking | Micro soft. ClassicCompute/informatie/Extensions/lezen | Hiermee wordt de extensie van de virtuele machine opgehaald. |
> | Bewerking | Micro soft. ClassicCompute/informatie/Extensions/schrijven | Hiermee wordt de extensie van de virtuele machine geplaatst. |
> | Bewerking | Micro soft. ClassicCompute/informatie/metricdefinitions/lezen | De metrische definitie van de virtuele machine ophalen. |
> | Bewerking | Micro soft. ClassicCompute/informatie/metrisch/lezen | Hiermee worden de metrische gegevens opgehaald. |
> | Bewerking | Micro soft. ClassicCompute/informatie/networkInterfaces/associatedNetworkSecurityGroups/verwijderen | Hiermee verwijdert u de netwerk beveiligings groep die is gekoppeld aan de netwerk interface. |
> | Bewerking | Micro soft. ClassicCompute/informatie/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/lezen | Hiermee wordt de bewerkings status gelezen van de virtuele machines die zijn gekoppeld aan netwerk beveiligings groepen. |
> | Bewerking | Micro soft. ClassicCompute/informatie/networkInterfaces/associatedNetworkSecurityGroups/lezen | Hiermee wordt de netwerk beveiligings groep opgehaald die aan de netwerk interface is gekoppeld. |
> | Bewerking | Micro soft. ClassicCompute/informatie/networkInterfaces/associatedNetworkSecurityGroups/schrijven | Hiermee wordt een netwerk beveiligings groep toegevoegd die aan de netwerk interface is gekoppeld. |
> | Bewerking | Micro soft. ClassicCompute/informatie/operationStatuses/lezen | Hiermee wordt de bewerkings status van de virtuele machines gelezen. |
> | Bewerking | Micro soft. ClassicCompute/informatie/performMaintenance/Action | Hiermee wordt onderhoud uitgevoerd op de virtuele machine. |
> | Bewerking | Micro soft. ClassicCompute/informatie/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Bewerking | Micro soft. ClassicCompute/informatie/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Bewerking | Micro soft. ClassicCompute/informatie/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Bewerking | Micro soft. ClassicCompute/informatie/lezen | Hiermee wordt een lijst met virtuele machines opgehaald. |
> | Bewerking | Micro soft. ClassicCompute/informatie/opnieuw implementeren/actie | Hiermee wordt de virtuele machine opnieuw geïmplementeerd. |
> | Bewerking | Micro soft. ClassicCompute/informatie/restart/actie | Hiermee worden virtuele machines opnieuw opgestart. |
> | Bewerking | Micro soft. ClassicCompute/informatie/shutdown/actie | Sluit de virtuele machine af. |
> | Bewerking | Micro soft. ClassicCompute/informatie/start/Action | Start de virtuele machine. |
> | Bewerking | Micro soft. ClassicCompute/informatie/stop/Action | Hiermee wordt de virtuele machine gestopt. |
> | Bewerking | Micro soft. ClassicCompute/informatie/schrijven | Virtuele machines toevoegen of wijzigen. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ClassicNetwork/expressroutecrossconnections/operationstatuses/lezen | Haal de status van een snelle route-cross Connection-bewerking op. |
> | Bewerking | Micro soft. ClassicNetwork/expressroutecrossconnections/peering/verwijderen | Verwijder de peering voor Express route-Kruis verbinding. |
> | Bewerking | Micro soft. ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/lezen | De bewerkings status van de peering van een snelle route Kruis verbindings bewerking ophalen. |
> | Bewerking | Micro soft. ClassicNetwork/expressroutecrossconnections/peerings/lezen | Ontvang snelle route Kruis verbinding peering. |
> | Bewerking | Micro soft. ClassicNetwork/expressroutecrossconnections/peering/schrijven | Verbindings peering voor Express route toevoegen. |
> | Bewerking | Micro soft. ClassicNetwork/expressroutecrossconnections/lezen | Ontvang snelle route-cross-verbindingen. |
> | Bewerking | Micro soft. ClassicNetwork/expressroutecrossconnections/schrijven | Snelle route-cross-verbindingen toevoegen. |
> | Bewerking | Micro soft. ClassicNetwork/gatewaySupportedDevices/lezen | Hiermee wordt de lijst met ondersteunde apparaten opgehaald. |
> | Bewerking | Micro soft. ClassicNetwork/networkSecurityGroups/verwijderen | Hiermee verwijdert u de netwerk beveiligings groep. |
> | Bewerking | Micro soft. ClassicNetwork/networkSecurityGroups/operationStatuses/lezen | Hiermee wordt de bewerkings status van de netwerk beveiligings groep gelezen. |
> | Bewerking | Micro soft. ClassicNetwork/networksecuritygroups/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee worden de diagnostische instellingen voor netwerk beveiligings groepen opgehaald |
> | Bewerking | Micro soft. ClassicNetwork/networksecuritygroups/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee worden de diagnostische instellingen voor netwerk beveiligings groepen gemaakt of bijgewerkt, deze bewerking wordt aangevuld door de Insights-resource provider. |
> | Bewerking | Micro soft. ClassicNetwork/networksecuritygroups/providers/micro soft. Insights/logDefinitions/lezen | Hiermee worden de gebeurtenissen voor de netwerk beveiligings groep opgehaald |
> | Bewerking | Micro soft. ClassicNetwork/networkSecurityGroups/lezen | Hiermee wordt de netwerk beveiligings groep opgehaald. |
> | Bewerking | Micro soft. ClassicNetwork/networkSecurityGroups/securityRules/verwijderen | Hiermee verwijdert u de beveiligings regel. |
> | Bewerking | Micro soft. ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/lezen | Hiermee wordt de bewerkings status van de beveiligings regels voor de netwerk beveiligings groep gelezen. |
> | Bewerking | Micro soft. ClassicNetwork/networkSecurityGroups/securityRules/lezen | Hiermee wordt de beveiligings regel opgehaald. |
> | Bewerking | Micro soft. ClassicNetwork/networkSecurityGroups/securityRules/write | Hiermee wordt een beveiligings regel toegevoegd of bijgewerkt. |
> | Bewerking | Micro soft. ClassicNetwork/networkSecurityGroups/schrijven | Hiermee voegt u een nieuwe netwerk beveiligings groep toe. |
> | Bewerking | Micro soft. ClassicNetwork/Operations/lezen | Klassieke netwerk bewerkingen ophalen. |
> | Bewerking | Micro soft. ClassicNetwork/quota's/lezen | Het quotum voor het abonnement ophalen. |
> | Bewerking | Micro soft. ClassicNetwork/REGI ster/actie | Registreren bij klassiek netwerk |
> | Bewerking | Micro soft. ClassicNetwork/reservedIps/verwijderen | Verwijder een gereserveerd IP-adres. |
> | Bewerking | Micro soft. ClassicNetwork/reservedIps/deelname/actie | Een gereserveerd IP-adres toevoegen |
> | Bewerking | Micro soft. ClassicNetwork/reservedIps/link/actie | Een gereserveerd IP-adres koppelen |
> | Bewerking | Micro soft. ClassicNetwork/reservedIps/operationStatuses/lezen | Hiermee wordt de bewerkings status van de gereserveerde ip's gelezen. |
> | Bewerking | Micro soft. ClassicNetwork/reservedIps/lezen | Hiermee worden de gereserveerde Ip's opgehaald |
> | Bewerking | Micro soft. ClassicNetwork/reservedIps/schrijven | Een nieuw gereserveerd IP-adres toevoegen |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/abortMigration/Action | De migratie van een Virtual Network afbreken |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/capabilities/lezen | Hiermee worden de mogelijkheden |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/checkIPAddressAvailability/Action | Controleert de beschik baarheid van een bepaald IP-adres in een virtueel netwerk. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/commitMigration/Action | Hiermee wordt de migratie van een Virtual Network doorgevoerd |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/verwijderen | Hiermee verwijdert u het virtuele netwerk. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/verwijderen | Hiermee wordt de intrekking van een client certificaat ongedaan. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/lezen | Lees de ingetrokken client certificaten. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/schrijven | Hiermee wordt een client certificaat ingetrokken. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/verwijderen | Hiermee verwijdert u het client certificaat van de gateway van het virtuele netwerk. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/downloaden/actie | Hiermee wordt het certificaat gedownload via de vinger afdruk. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/actie | Geeft een lijst van het certificaat pakket voor de virtuele netwerk gateway. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/lezen | Zoek de basis certificaten van de client. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/schrijven | Hiermee wordt een nieuw client basis certificaat geüpload. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/Connections/Connect/Action | Verbindt een site met site gateway verbinding. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/verbindingen/verbinding maken/actie | Verbreekt de verbinding van een site met site gateway verbinding. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/verbindingen/lezen | Hiermee haalt u de lijst met verbindingen op. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/verbindingen/testen/actie | Test een site-naar-site gateway verbinding. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/verwijderen | Hiermee verwijdert u de gateway van het virtuele netwerk. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/actie | Hiermee downloadt u het configuratie script voor het apparaat. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/actie | Hiermee downloadt u de diagnostische gegevens van de gateway. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/actie | Hiermee wordt de circuit service sleutel opgehaald. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/listPackage/actie | Hiermee wordt het gateway pakket voor het virtuele netwerk weer gegeven. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/operationStatuses/lezen | Hiermee wordt de bewerkings status voor de virtuele netwerk gateways gelezen. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/pakketten/lezen | Hiermee wordt het gateway pakket voor het virtuele netwerk opgehaald. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/lezen | Hiermee worden de virtuele netwerk gateways opgehaald. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/startDiagnostics/actie | Diagnostische gegevens voor de gateway van het virtuele netwerk starten. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/actie | Hiermee stopt u de diagnostische gegevens voor de virtuele netwerk gateway. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/gateways/schrijven | Hiermee voegt u een virtuele netwerk gateway toe. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/deelname/actie | Voegt het virtuele netwerk samen. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/operationStatuses/lezen | Hiermee wordt de bewerkings status van de virtuele netwerken gelezen. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/peer/Action | Peert een virtueel netwerk met een ander virtueel netwerk. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/prepareMigration/Action | Bereidt de migratie van een Virtual Network voor |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/lezen | Het virtuele netwerk ophalen. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/verwijderen | Hiermee wordt de proxy voor de peering van het externe virtuele netwerk verwijderd. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/lezen | Hiermee wordt de proxy voor de peering van het externe virtuele netwerk opgehaald. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Hiermee wordt de proxy voor de peering van het externe virtuele netwerk toegevoegd of bijgewerkt. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/verwijderen | Hiermee verwijdert u de netwerk beveiligings groep die aan het subnet is gekoppeld. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/lezen | Hiermee wordt de bewerkings status gelezen van het subnet van het virtuele netwerk dat aan de netwerk beveiligings groep is gekoppeld. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/lezen | Hiermee wordt de netwerk beveiligings groep opgehaald die aan het subnet is gekoppeld. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/subnetten/associatedNetworkSecurityGroups/schrijven | Hiermee wordt een netwerk beveiligings groep toegevoegd die aan het subnet is gekoppeld. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/validateMigration/Action | Valideert de migratie van een Virtual Network |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/virtualNetworkPeerings/lezen | Hiermee wordt de peering van het virtuele netwerk opgehaald. |
> | Bewerking | Micro soft. ClassicNetwork/virtualNetworks/schrijven | Voeg een nieuw virtueel netwerk toe. |

## <a name="microsoftclassicstorage"></a>Micro soft. ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ClassicStorage/capabilities/lezen | Hiermee worden de mogelijkheden |
> | Bewerking | Micro soft. ClassicStorage/checkStorageAccountAvailability/actie | Hiermee wordt gecontroleerd op de beschik baarheid van een opslag account. |
> | Bewerking | Micro soft. ClassicStorage/checkStorageAccountAvailability/lezen | De beschik baarheid van een opslag account ophalen. |
> | Bewerking | Micro soft. ClassicStorage/schijven/lezen | Retourneert de schijf van het opslag account. |
> | Bewerking | Micro soft. ClassicStorage/images/operationstatuses/lezen | Haalt de bewerkings status van de afbeelding op. |
> | Bewerking | Micro soft. ClassicStorage/images/lezen | Hiermee wordt de afbeelding geretourneerd. |
> | Bewerking | Micro soft. ClassicStorage/Operations/lezen | Hiermee worden klassieke opslag bewerkingen opgehaald |
> | Bewerking | Micro soft. ClassicStorage/osImages/lezen | Retourneert de installatie kopie van het besturings systeem. |
> | Bewerking | Micro soft. ClassicStorage/osPlatformImages/lezen | Hiermee wordt de installatie kopie van het besturings systeem opgehaald. |
> | Bewerking | Micro soft. ClassicStorage/publicImages/lezen | Hiermee wordt de installatie kopie van de open bare virtuele machine opgehaald. |
> | Bewerking | Micro soft. ClassicStorage/quota's/lezen | Het quotum voor het abonnement ophalen. |
> | Bewerking | Micro soft. ClassicStorage/REGI ster/actie | Registreren bij klassieke opslag |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/abortMigration/Action | De migratie van een opslag account wordt afgebroken. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/blobServices/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/blobServices/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/blobServices/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/commitMigration/Action | Hiermee wordt de migratie van een opslag account doorgevoerd. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/verwijderen | Verwijder het opslag account. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/schijven/verwijderen | Hiermee verwijdert u een opgegeven opslag account schijf. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/schijven/operationStatuses/lezen | Hiermee wordt de bewerkings status van de resource gelezen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/schijven/lezen | Retourneert de schijf van het opslag account. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/schijven/schrijven | Hiermee voegt u een opslag account schijf toe. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/fileServices/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/fileServices/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/fileServices/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/images/verwijderen | Hiermee verwijdert u een bepaalde installatie kopie van het opslag account. Keur. Micro soft. ClassicStorage/Storage accounts/vmImages gebruiken |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/images/operationstatuses/lezen | Retourneert de bewerkings status van de installatie kopie van het opslag account. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/images/lezen | Hiermee wordt de installatie kopie van het opslag account geretourneerd. Keur. Micro soft. ClassicStorage/Storage accounts/vmImages gebruiken |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/Listkeys ophalen/Action | Hier worden de toegangs sleutels voor de opslag accounts weer gegeven. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/operationStatuses/lezen | Hiermee wordt de bewerkings status van de resource gelezen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/osImages/verwijderen | Hiermee verwijdert u een gegeven installatie kopie van het besturings systeem van het opslag account. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/osImages/lezen | Retourneert de installatie kopie van het besturings systeem van het opslag account. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/osImages/write | Voegt een gegeven installatie kopie van het besturings systeem van het opslag account toe. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/prepareMigration/Action | Bereidt de migratie van een opslag account voor. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/queueServices/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/queueServices/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/queueServices/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/lezen | Retour neer het opslag account met het opgegeven account. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/regenerateKey/Action | Hiermee worden de bestaande toegangs sleutels voor het opslag account opnieuw gegenereerd. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/Services/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/Services/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/Services/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/Services/metrische gegevens/lezen | Hiermee worden de metrische gegevens opgehaald. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/services/lezen | De beschik bare services ophalen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/tableServices/providers/micro soft. Insights/diagnosticSettings/lezen | De diagnostische instellingen ophalen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/tableServices/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/tableServices/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities opgehaald. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/validateMigration/Action | Hiermee wordt de migratie van een opslag account gevalideerd. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/vmImages/verwijderen | Hiermee verwijdert u een bepaalde installatie kopie van een virtuele machine. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/vmImages/operationstatuses/lezen | Hiermee wordt een bepaalde bewerkings status van installatie kopie van virtuele machine opgehaald. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/vmImages/lezen | Hiermee wordt de installatie kopie van de virtuele machine geretourneerd. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/vmImages/write | Hiermee voegt u een bepaalde installatie kopie van een virtuele machine toe. |
> | Bewerking | Micro soft. ClassicStorage/Storage accounts/schrijven | Hiermee voegt u een nieuw opslag account toe. |
> | Bewerking | Micro soft. ClassicStorage/vmImages/lezen | Geeft een lijst van installatie kopieën van virtuele machines. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | DataAction | Micro soft. CognitiveServices/accounts/automatische suggesties/zoeken/actie | Met deze bewerking worden suggesties gegeven voor een bepaalde query of gedeeltelijke query. |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/analyseren/actie | Met deze bewerking wordt een uitgebreide set visuele functies geëxtraheerd op basis van de inhoud van de installatie kopie.  |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/areaofinterest/actie | Met deze bewerking wordt een omsluitend kader geretourneerd rond het belangrijkste gedeelte van de afbeelding. |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/beschrijven/actie | Met deze bewerking wordt een beschrijving van een afbeelding in een door de mens lees bare taal gegenereerd met volledige zinnen.<br> De beschrijving is gebaseerd op een verzameling inhouds Tags, die ook door de bewerking worden geretourneerd.<br>Voor elke afbeelding kan meer dan één beschrijving worden gegenereerd.<br> Beschrijvingen worden geordend op basis van hun betrouwbaarheids Score.<br>Alle beschrijvingen zijn in het Engels. |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/detecteren/actie | Met deze bewerking wordt object detectie uitgevoerd voor de opgegeven installatie kopie.  |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/generatethumbnail/actie | Met deze bewerking wordt een miniatuur afbeelding gegenereerd met de opgegeven breedte en hoogte van de gebruiker.<br> Standaard analyseert de service de installatie kopie, identificeert het rente gebied (ROI) en genereert slimme bijsnijd coördinaten op basis van het ROI.<br> Slim bijsnijden helpt bij het opgeven van een hoogte-breedte verhouding die afwijkt van die van de invoer installatie kopie |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/modellen/analyseren/actie | Met deze bewerking wordt inhoud in een installatie kopie herkend door een domein-specifiek model toe te passen.<br> De lijst met domein-specifieke modellen die worden ondersteund door de Computer Vision-API kan worden opgehaald met behulp van de/models GET-aanvraag.<br> Op dit moment biedt de API de volgende gebruikersspecifieke modellen: beroemdheden, bezienswaardigheden. |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/modellen/lezen | Met deze bewerking wordt de lijst met domein-specifieke modellen geretourneerd die worden ondersteund door de Computer Vision-API.  De API ondersteunt momenteel de volgende gebruikersspecifieke modellen: beroemdheden Recognizer, oriëntatie punt Recognizer. |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/OCR/actie | Optische teken herkenning (OCR) detecteert tekst in een afbeelding en extraheert de herkende tekens in een door een machine bruikbare teken stroom.    |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/recognizetext/actie | Gebruik deze interface om het resultaat van een Tekst herkennen bewerking op te halen. Wanneer u de Tekst herkennen-interface gebruikt, bevat het antwoord een veld met de naam bewerking locatie. Het veld bewerkings locatie bevat de URL die u moet gebruiken voor de bewerking resultaat van het ophalen van Tekst herkennen. |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/tag/actie | Met deze bewerking wordt een lijst met woorden, of tags, gegenereerd die relevant zijn voor de inhoud van de geleverde afbeelding.<br>De Computer Vision-API kan labels retour neren op basis van objecten, in de praktijk, in achtergronden of in afbeeldingen gevonden.<br>In tegens telling tot categorieën worden tags niet ingedeeld op basis van een hiërarchisch classificatie systeem, maar komen ze overeen met de inhoud van de afbeelding.<br>Tags kunnen hints bevatten om Verwar ring te voor komen of om context te bieden, bijvoorbeeld de tag "cello", kan worden gecombineerd met de hint "musical instrument".<br>Alle tags zijn in het Engels. |
> | DataAction | Micro soft. CognitiveServices/accounts/ComputerVision/textoperations/lezen | Deze interface wordt gebruikt om het resultaat van de tekst bewerking te herkennen. De URL naar deze interface moet worden opgehaald uit het veld <b>bewerkings locatie</b> dat is geretourneerd door de tekst herkennen-interface. |
> | Bewerking | Micro soft. CognitiveServices/accounts/verwijderen | Hiermee worden de API-accounts verwijderd |
> | DataAction | Micro soft. CognitiveServices/accounts/EntitySearch/zoeken/actie | Entiteiten ophalen en de resultaten voor een bepaalde query plaatsen. |
> | DataAction | Micro soft. CognitiveServices/accounts/gezicht/detecteren/actie | Detecteren van menselijke gezichten in een afbeelding, gezichts rechthoeken retour neren en optioneel met faceIds, bezienswaardigheden en kenmerken. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/facelists/verwijderen | Een opgegeven gezichts lijst verwijderen. De gerelateerde installatie kopieën in de lijst met het gezichts materiaal worden ook verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/facelists/persistedfaces/verwijderen | Een gezicht verwijderen uit een gezichts lijst door opgegeven faceListId en persisitedFaceId. De afbeelding voor het gerelateerde gezicht wordt ook verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/facelists/persistedfaces/schrijven | Voeg een gezicht toe aan een opgegeven gezichts lijst van Maxi maal 1.000 gezichten. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/facelists/lezen | Haal de faceListId, naam, User Data en gezichten van een gezichts lijst op in de lijst met gezicht. Lijst met gezichts lijsten ' faceListId, name en User Data. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/facelists/schrijven | Maak een lege face-lijst met door de gebruiker opgegeven faceListId, een naam en een optioneel User Data. Maxi maal 64 gezichts lijsten zijn toegestane Update gegevens van een gezichts lijst, inclusief naam en User Data. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/findsimilars/actie | Aan de hand van het voor beeld van een query op faceId kunt u zoeken naar de vergelijk bare gezichten vanuit een faceId-matrix, een gezichts lijst of een grote gezichts lijst. faceId |
> | DataAction | Micro soft. CognitiveServices/accounts/face/groep/actie | Verdeel kandidaten in groepen op basis van vergelijk bare gezicht. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/identificeren/actie | 1-op-veel-identificatie voor het vinden van de dichtstbijzijnde treffers van een persoons groep of een grote persoons groep. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/verwijderen | Een opgegeven grote gezichts lijst verwijderen. De gerelateerde gezichts afbeeldingen in de lijst grote gezicht worden ook verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/persistedfaces/verwijderen | Een gezicht verwijderen uit een grote gezichts lijst door opgegeven largeFaceListId en persisitedFaceId. De afbeelding voor het gerelateerde gezicht wordt ook verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/persistedfaces/lezen | Haal persistente gezicht op in grote gezichts lijst door largeFaceListId en persistedFaceId. Een lijst met gezichten ' persistedFaceId en User Data ' in een opgegeven lijst met grote vlakken. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/persistedfaces/schrijven | Voeg een gezicht toe aan een opgegeven lijst met grote vlakken, tot 1.000.000 gezichten. Het veld User Data van een opgegeven face bijwerken in een lijst met grote vlakken door de persistedFaceId ervan. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/lezen | De largeFaceListId, naam en User Data van een grote lijst ophalen. Lijst met grote gezichts lijsten: informatie over largeFaceListId, name en User Data. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/Train/actie | Een trainings taak voor een grote gezichts lijst verzenden. Training is een cruciale stap die alleen kan worden gebruikt door een getrainde grote gezichts lijst. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/training/lezen | Om te controleren of de trainings status van de grote face List is voltooid of nog steeds actief is. LargeFaceList training is een asynchrone bewerking |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largefacelists/schrijven | Maak een lege lijst met grote gezichts gebruikers met een door de gebruiker opgegeven largeFaceListId, een naam en een optioneel User Data. Update gegevens van een grote gezichts lijst, met inbegrip van naam en User Data. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/verwijderen | Een bestaande groep grote personen met de opgegeven personGroupId verwijderen. Permanente gegevens in deze grote groep worden verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/personen/actie | Een nieuwe persoon maken in een opgegeven grote persoons groep. Als u een gezicht wilt toevoegen aan deze persoon, neemt u contact op met |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/personen/verwijderen | Een bestaande persoon verwijderen uit een grote groep personen. Alle opgeslagen persoons gegevens en gezichts afbeeldingen in de persoons vermelding worden verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/personen/persistedfaces/verwijderen | Een gezicht verwijderen van een persoon in een grote groep personen. Gezichts gegevens en afbeelding die betrekking hebben op deze gezichts vermelding worden ook verwijderd. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/personen/persistedfaces/lezen | Informatie over persoons gezicht ophalen. De permanente persoons zijde wordt opgegeven door de largePersonGroupId, personId en persistedFaceId. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/personen/persistedfaces/schrijven | Voeg een gezichts afbeelding aan een persoon toe aan een grote persoons groep voor gezichts identificatie of-verificatie. Om te omgaan met de afbeelding van het bijwerken van een persoon die het veld User Data van het gezicht heeft behouden. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/personen/lezen | Haal de naam en het User Data van een persoon op en de persistente faceIds die de afbeelding van het Inge schreven persoons gezicht vertegenwoordigt. De gegevens van alle personen in de opgegeven groep grote personen weer geven, waaronder personId, name, User Data and persistedFaceIds. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/personen/schrijven | Naam of User Data van een persoon bijwerken. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/lezen | De gegevens van een grote persoons groep ophalen, inclusief de naam en het User Data. Deze API retourneert een lijst met gegevens van een grote persoon met groepen alle bestaande largePesonGroupId, naam en User Data van de groep. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/Train/actie | Een trainings taak voor een grote persoons groep verzenden. Training is een belang rijke stap dat alleen een getrainde grote persoons groep kan gebruiken. |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/training/lezen | De trainings status van een grote persoons groep controleren is voltooid of nog steeds actief. LargePersonGroup training is een asynchrone bewerking |
> | DataAction | Micro soft. CognitiveServices/accounts/face/largepersongroups/schrijven | Maak een nieuwe groep grote personen met een door de gebruiker opgegeven largePersonGroupId, een naam en een optionele User Data. De naam en het User Data van een bestaande groep van grote personen bijwerken. De eigenschappen blijven ongewijzigd als ze niet in de hoofd tekst van de aanvraag staan. |
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
> | DataAction | Micro soft. CognitiveServices/accounts/gezicht/controleren/actie | Controleer of twee gezichten deel uitmaken van dezelfde persoon of of het ene gezicht deel uitmaakt van een persoon. |
> | DataAction | Micro soft. CognitiveServices/accounts/ImageSearch/Details/actie | Retourneert inzichten over een afbeelding, zoals webpagina's die de afbeelding bevatten. |
> | DataAction | Micro soft. CognitiveServices/accounts/ImageSearch/zoeken/actie | Relevante installatie kopieën voor een bepaalde query ophalen. |
> | DataAction | Micro soft. CognitiveServices/accounts/ImageSearch/trending/actie | Huidige trend afbeeldingen ophalen. |
> | DataAction | Micro soft. CognitiveServices/accounts/ImmersiveReader/getcontentmodelforreader/actie | Hiermee maakt u een insluitende lezer-sessie |
> | DataAction | Micro soft. CognitiveServices/accounts/InkRecognizer/recognize/Action | Op basis van een set lijn gegevens wordt de inhoud geanalyseerd en wordt een lijst gegenereerd met herkende entiteiten, inclusief de herkende tekst. |
> | Bewerking | Micro soft. CognitiveServices/accounts/Listkeys ophalen/actie | Lijst met sleutels |
> | DataAction | Micro soft. CognitiveServices/accounts/LUIS/voors pellen/actie | Hiermee wordt de gepubliceerde eindpunt voorspelling opgehaald voor de opgegeven query. |
> | DataAction | Micro soft. CognitiveServices/accounts/NewsSearch/categorysearch/actie | Hiermee wordt nieuws voor een gegeven categorie geretourneerd. |
> | DataAction | Micro soft. CognitiveServices/accounts/NewsSearch/zoeken/actie | Nieuws artikelen ophalen die relevant zijn voor een bepaalde query. |
> | DataAction | Micro soft. CognitiveServices/accounts/NewsSearch/trendingtopics/actie | Bekijk onderwerpen over trends die door Bing worden geïdentificeerd. Dit zijn dezelfde onderwerpen die worden weer gegeven in de banner aan de onderkant van de Bing-start pagina. |
> | Bewerking | Micro soft. CognitiveServices/accounts/lezen | Hiermee worden API-accounts gelezen. |
> | Bewerking | Micro soft. CognitiveServices/accounts/regenerateKey/actie | Sleutel opnieuw genereren |
> | Bewerking | Micro soft. CognitiveServices/accounts/sku's/lezen | Beschik bare Sku's voor een bestaande resource lezen. |
> | DataAction | Micro soft. CognitiveServices/accounts/spelling/spelling/actie | Resultaat van een spelling controle query ophalen via GET of POST. |
> | DataAction | Micro soft. CognitiveServices/accounts/TextAnalytics/entities/actie | De API retourneert een lijst met bekende entiteiten en algemene benoemde entiteiten (\"persoon\", \"locatie\", \"organisatie\" enzovoort) in een bepaald document. |
> | DataAction | Micro soft. CognitiveServices/accounts/TextAnalytics/woordgroepen/actie | De API retourneert een lijst met tekenreeksen die de belangrijkste gespreksonderwerpen in de invoertekst aanduiden. |
> | DataAction | Micro soft. CognitiveServices/accounts/TextAnalytics/talen/actie | De API retourneert de gedetecteerde taal en een numerieke score tussen 0 en 1. Scores dichtbij 1 geven aan dat het 100% zeker is dat de geïdentificeerde taal waar is. Er worden in totaal 120 talen ondersteund. |
> | DataAction | Micro soft. CognitiveServices/accounts/TextAnalytics/sentiment/actie | De API retourneert een numerieke score tussen 0 en 1.<br>Scores dicht bij 1 geven een positieve sentiment aan, terwijl scores dicht bij 0 duiden op een negatieve sentiment.<br>Een Score van 0,5 geeft het gebrek aan sentiment aan (bijvoorbeeld<br>een factoid-instructie). |
> | Bewerking | Micro soft. CognitiveServices/accounts/gebruik/lezen | Het quotum gebruik voor een bestaande resource ophalen. |
> | DataAction | Micro soft. CognitiveServices/accounts/VideoSearch/Details/actie | Krijg inzichten over een video, zoals verwante Video's. |
> | DataAction | Micro soft. CognitiveServices/accounts/VideoSearch/zoeken/actie | Down load Video's die relevant zijn voor een bepaalde query. |
> | DataAction | Micro soft. CognitiveServices/accounts/VideoSearch/trending/actie | Down load momenteel Video's over trend. |
> | DataAction | Micro soft. CognitiveServices/accounts/VisualSearch/zoeken/actie | Retourneert een lijst met labels die relevant zijn voor de meegeleverde afbeelding |
> | DataAction | Micro soft. CognitiveServices/accounts/WebSearch/Search/Action | Down load web-, afbeeldings-, nieuws-en & Video's resultaten voor een bepaalde query. |
> | Bewerking | Micro soft. CognitiveServices/accounts/schrijven | Schrijft API-accounts. |
> | Bewerking | Micro soft. CognitiveServices/checkDomainAvailability/actie | Beschik bare Sku's voor een abonnement lezen. |
> | Bewerking | Micro soft. CognitiveServices/locaties/checkSkuAvailability/actie | Beschik bare Sku's voor een abonnement lezen. |
> | Bewerking | Micro soft. CognitiveServices/locaties/checkSkuAvailability/actie | Beschik bare Sku's voor een abonnement lezen. |
> | Bewerking | Micro soft. CognitiveServices/locaties/deleteVirtualNetworkOrSubnets/actie | Melding van micro soft. Network van het verwijderen van VirtualNetworks of subnetten. |
> | Bewerking | Micro soft. CognitiveServices/Operations/lezen | Alle beschik bare bewerkingen weer geven |
> | Bewerking | Micro soft. CognitiveServices/REGI ster/actie | Registreert het abonnement voor Cognitive Services |
> | Bewerking | Micro soft. CognitiveServices/REGI ster/actie | Registreert het abonnement voor Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. commerce/RateCard/lezen | Retour neren gegevens van aanbieding, meta gegevens van resource/meter en tarieven voor het gegeven abonnement. |
> | Bewerking | Micro soft. commerce/UsageAggregates/lezen | Hiermee wordt het verbruik van Microsoft Azure door een abonnement opgehaald. Het resultaat bevat statistische gegevens over het gebruik van gegevens, abonnementen en resources die zijn gerelateerd aan een bepaald tijds bereik. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Compute/Availability sets/verwijderen | Hiermee wordt de beschikbaarheidsset verwijderd |
> | Bewerking | Micro soft. Compute/Availability sets/lezen | De eigenschappen van een beschikbaarheidsset ophalen |
> | Bewerking | Micro soft. Compute/Availability sets/toegestane VM/lezen | Beschik bare grootten voor het maken of bijwerken van een virtuele machine in de beschikbaarheidsset weer geven |
> | Bewerking | Micro soft. Compute/Availability sets/schrijven | Hiermee wordt een nieuwe beschikbaarheidsset gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. Compute/diskEncryptionSets/verwijderen | Een schijf versleutelings verwijderen |
> | Bewerking | Micro soft. Compute/diskEncryptionSets/lezen | De eigenschappen van een set schijf versleuteling ophalen |
> | Bewerking | Micro soft. Compute/diskEncryptionSets/schrijven | Een nieuwe schijf versleutelings maken of een bestaande set bijwerken |
> | Bewerking | Micro soft. Compute/schijven/beginGetAccess/actie | De SAS-URI van de schijf ophalen voor BLOB-toegang |
> | Bewerking | Micro soft. Compute/schijven/verwijderen | Hiermee wordt de schijf verwijderd |
> | Bewerking | Micro soft. Compute/schijven/endGetAccess/actie | De SAS-URI van de schijf intrekken |
> | Bewerking | Micro soft. Compute/schijven/lezen | De eigenschappen van een schijf ophalen |
> | Bewerking | Micro soft. Compute/schijven/schrijven | Hiermee wordt een nieuwe schijf gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. Compute/galerieën/verwijderen | Hiermee wordt de galerie verwijderd |
> | Bewerking | Micro soft. Compute/galerieën/afbeeldingen/verwijderen | De galerie afbeelding verwijderen |
> | Bewerking | Micro soft. Compute/galerieën/afbeeldingen/lezen | Hiermee worden de eigenschappen van de galerie afbeelding opgehaald |
> | Bewerking | Micro soft. Compute/galerieën/afbeeldingen/versies/verwijderen | Hiermee wordt de versie van de galerie afbeelding verwijderd |
> | Bewerking | Micro soft. Compute/galerieën/afbeeldingen/versies/lezen | Hiermee worden de eigenschappen van de versie van de galerie afbeelding opgehaald |
> | Bewerking | Micro soft. Compute/galerieën/afbeeldingen/versies/schrijven | Hiermee wordt een nieuwe versie van de galerie afbeelding gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. Compute/galerieën/afbeeldingen/schrijven | Hiermee wordt een nieuwe galerie afbeelding gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. Compute/galerieën/lezen | Hiermee worden de eigenschappen van de galerie opgehaald |
> | Bewerking | Micro soft. Compute/galerieën/schrijven | Hiermee maakt u een nieuwe galerie of werkt u een bestaande. |
> | Bewerking | Micro soft. Compute/hostGroups/verwijderen | Hiermee wordt de hostgroep verwijderd |
> | Bewerking | Micro soft. Compute/hostGroups/hosts/Delete | Hiermee wordt de host verwijderd |
> | Bewerking | Micro soft. Compute/hostGroups/hosts/lezen | De eigenschappen van een host ophalen |
> | Bewerking | Micro soft. Compute/hostGroups/hosts/write | Hiermee wordt een nieuwe host gemaakt of een bestaande host bijgewerkt |
> | Bewerking | Micro soft. Compute/hostGroups/lezen | De eigenschappen van een hostgroep ophalen |
> | Bewerking | Micro soft. Compute/hostGroups/schrijven | Hiermee wordt een nieuwe hostgroep gemaakt of een bestaande hostgroep bijgewerkt |
> | Bewerking | Micro soft. Compute/images/verwijderen | Hiermee verwijdert u de installatie kopie |
> | Bewerking | Micro soft. Compute/images/lezen | De eigenschappen van de installatie kopie ophalen |
> | Bewerking | Micro soft. Compute/images/schrijven | Hiermee wordt een nieuwe installatie kopie gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. Compute/locaties/capsOperations/lezen | Hiermee wordt de status van een asynchrone Cap's-bewerking opgehaald |
> | Bewerking | Micro soft. computes/locaties/onkoperen/lezen | Hiermee wordt de status van een asynchrone schijf bewerking opgehaald |
> | Bewerking | Micro soft. Compute/locaties/logAnalytics/getRequestRateByInterval/Action | Logboeken maken voor het weer geven van totaal aantal aanvragen per tijds interval voor hulp bij het beperken van diagnoses. |
> | Bewerking | Micro soft. Compute/locaties/logAnalytics/getThrottledRequests/Action | Maak Logboeken om aggregaties van vertraagde aanvragen weer te geven, gegroepeerd op ResourceName, Operationname of het toegepaste beperkings beleid. |
> | Bewerking | Micro soft. Compute/locaties/bewerkingen/lezen | Hiermee wordt de status van een asynchrone bewerking opgehaald |
> | Bewerking | Micro soft. Compute/locaties/uitgevers/artifacttypes/aanbiedingen/lezen | De eigenschappen van een platform installatie kopie aanbieding ophalen |
> | Bewerking | Micro soft. Compute/locaties/uitgevers/artifacttypes/aanbiedingen/sku's/lezen | De eigenschappen van een platform installatie kopie-SKU ophalen |
> | Bewerking | Micro soft. Compute/locaties/Publishers/artifacttypes/aanbiedingen/sku's/versies/lezen | De eigenschappen van een platform installatie kopie versie ophalen |
> | Bewerking | Micro soft. Compute/locaties/uitgevers/artifacttypes/typen/lezen | De eigenschappen van een VMExtension-type ophalen |
> | Bewerking | Micro soft. computes/locaties/uitgevers/artifacttypes/typen/versies/lezen | De eigenschappen van een VMExtension-versie ophalen |
> | Bewerking | Micro soft. computes/locaties/uitgevers/lezen | De eigenschappen van een uitgever ophalen |
> | Bewerking | Micro soft. Compute/locaties/runCommand/lezen | Een lijst met beschik bare uitvoerings opdrachten op locatie |
> | Bewerking | Micro soft. computes/locaties/gebruik/lezen | Hiermee worden service limieten en huidige gebruiks hoeveelheden opgehaald voor de reken resources van het abonnement op een locatie |
> | Bewerking | Micro soft. Compute/locaties/toegestane VM/lezen | Een lijst met beschik bare grootten van virtuele machines op een locatie |
> | Bewerking | Micro soft. Compute/Operations/lezen | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn op micro soft. Compute resource provider |
> | Bewerking | Micro soft. Compute/proximityPlacementGroups/verwijderen | Hiermee wordt de plaatsings groep voor nabijheid verwijderd |
> | Bewerking | Micro soft. Compute/proximityPlacementGroups/lezen | De eigenschappen van een proximity-plaatsings groep ophalen |
> | Bewerking | Micro soft. Compute/proximityPlacementGroups/schrijven | Hiermee wordt een nieuwe plaatsings groep voor nabijheid gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. Compute/REGI ster/actie | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. compute |
> | Bewerking | Micro soft. Compute/restorePointCollections/verwijderen | Hiermee verwijdert u de verzameling van herstel punten en de opgenomen herstel punten |
> | Bewerking | Micro soft. Compute/restorePointCollections/lezen | De eigenschappen van een herstel punt verzameling ophalen |
> | Bewerking | Micro soft. Compute/restorePointCollections/restorePoints/verwijderen | Hiermee wordt het herstel punt verwijderd |
> | Bewerking | Micro soft. Compute/restorePointCollections/restorePoints/lezen | De eigenschappen van een herstel punt ophalen |
> | Bewerking | Micro soft. Compute/restorePointCollections/restorePoints/retrieveSasUris/Action | De eigenschappen van een herstel punt samen met Blob SAS-Uri's ophalen |
> | Bewerking | Micro soft. Compute/restorePointCollections/restorePoints/write | Hiermee maakt u een nieuw herstel punt |
> | Bewerking | Micro soft. Compute/restorePointCollections/schrijven | Hiermee maakt u een nieuwe herstel punt verzameling of werkt u een bestaande. |
> | Bewerking | Micro soft. Compute/sharedVMImages/verwijderen | Hiermee worden de SharedVMImage verwijderd |
> | Bewerking | Micro soft. Compute/sharedVMImages/lezen | De eigenschappen van een SharedVMImage ophalen |
> | Bewerking | Micro soft. Compute/sharedVMImages/versa/verwijderen | Een SharedVMImageVersion verwijderen |
> | Bewerking | Micro soft. Compute/sharedVMImages/versies/lezen | De eigenschappen van een SharedVMImageVersion ophalen |
> | Bewerking | Micro soft. Compute/sharedVMImages/versies/repliceren/actie | Een SharedVMImageVersion repliceren naar doel regio's |
> | Bewerking | Micro soft. Compute/sharedVMImages/versies/schrijven | Een nieuwe SharedVMImageVersion maken of een bestaande bijwerken |
> | Bewerking | Micro soft. Compute/sharedVMImages/schrijven | Hiermee wordt een nieuwe SharedVMImage gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. Compute/sku's/lezen | Hiermee wordt de lijst met micro soft. Compute-Sku's die beschikbaar zijn voor uw abonnement opgehaald |
> | Bewerking | Micro soft. Compute/moment opnamen/beginGetAccess/actie | De SAS-URI van de moment opname voor BLOB-toegang ophalen |
> | Bewerking | Micro soft. Compute/moment opnamen/verwijderen | Een moment opname verwijderen |
> | Bewerking | Micro soft. Compute/moment opnamen/endGetAccess/actie | De SAS-URI van de moment opname intrekken |
> | Bewerking | Micro soft. Compute/moment opnamen/lezen | De eigenschappen van een moment opname ophalen |
> | Bewerking | Micro soft. Compute/moment opnamen/schrijven | Een nieuwe moment opname maken of een bestaand abonnement bijwerken |
> | Bewerking | Micro soft. Compute/unregister/actie | Registratie van een abonnement bij micro soft. Compute-resource provider |
> | Bewerking | Micro soft. Compute/informatie/Capture/Action | Hiermee wordt de virtuele machine vastgelegd door virtuele harde schijven te kopiëren en wordt een sjabloon gegenereerd dat kan worden gebruikt voor het maken van vergelijk bare virtuele machines |
> | Bewerking | Micro soft. Compute/informatie/convertToManagedDisks/Action | Hiermee worden de op blobs gebaseerde schijven van de virtuele machine geconverteerd naar beheerde schijven |
> | Bewerking | Micro soft. Compute/informatie/disallocate/Action | Hiermee wordt de virtuele machine uitgeschakeld en worden de reken resources vrijgegeven |
> | Bewerking | Micro soft. Compute/informatie/verwijderen | Hiermee wordt de virtuele machine verwijderd |
> | Bewerking | Micro soft. Compute/informatie/uitbrei dingen/verwijderen | Hiermee wordt de extensie van de virtuele machine verwijderd |
> | Bewerking | Micro soft. Compute/informatie/uitbrei dingen/lezen | De eigenschappen van een extensie van een virtuele machine ophalen |
> | Bewerking | Micro soft. Compute/informatie/Extensions/write | Hiermee wordt een nieuwe extensie van de virtuele machine gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. Compute/informatie/generalize/Action | Hiermee wordt de status van de virtuele machine ingesteld op gegeneraliseerd en wordt de virtuele machine voor bereid voor vastleggen |
> | Bewerking | Micro soft. Compute/informatie/instanceView/lezen | Hiermee wordt de gedetailleerde runtime status van de virtuele machine en de bijbehorende resources opgehaald |
> | DataAction | Micro soft. Compute/informatie/login/Action | Als gewone gebruiker aanmelden bij een virtuele machine |
> | DataAction | Micro soft. Compute/informatie/loginAsAdmin/Action | Aanmelden bij een virtuele machine met Windows-beheerders-of Linux-root gebruikers bevoegdheden |
> | Bewerking | Micro soft. Compute/informatie/performMaintenance/Action | Onderhouds bewerking op de virtuele machine wordt uitgevoerd. |
> | Bewerking | Micro soft. Compute/informatie/uitgeschakeld/Action | Hiermee wordt de virtuele machine uitgeschakeld. Houd er rekening mee dat de virtuele machine nog steeds wordt gefactureerd. |
> | Bewerking | Micro soft. Compute/informatie/lezen | De eigenschappen van een virtuele machine ophalen |
> | Bewerking | Micro soft. Compute/informatie/redeploy/Action | Hiermee wordt de virtuele machine opnieuw geïmplementeerd |
> | Bewerking | Micro soft. Compute/informatie/Reimage/Action | Hiermee wordt de installatie kopie van de virtuele machine die gebruikmaakt van een differentiërende schijf. |
> | Bewerking | Micro soft. Compute/informatie/restart/Action | Hiermee wordt de virtuele machine opnieuw opgestart |
> | Bewerking | Micro soft. Compute/informatie/runCommand/Action | Hiermee wordt een vooraf gedefinieerd script uitgevoerd op de virtuele machine |
> | Bewerking | Micro soft. Compute/informatie/start/Action | De virtuele machine wordt gestart |
> | Bewerking | Micro soft. Compute/informatie/toegestane VM/lezen | Een lijst met beschik bare grootten waarmee de virtuele machine kan worden bijgewerkt |
> | Bewerking | Micro soft. Compute/informatie/schrijven | Hiermee wordt een nieuwe virtuele machine gemaakt of een bestaande virtuele machine bijgewerkt |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/disallocate/Action | Hiermee worden de reken resources voor de instanties van de virtuele-machine Schaalset uitgeschakeld en vrijgegeven  |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/verwijderen | Hiermee wordt de Schaalset voor virtuele machines verwijderd |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/verwijderen/actie | Hiermee verwijdert u de exemplaren van de virtuele-machine Schaalset |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/uitbrei dingen/verwijderen | Hiermee wordt de extensie van de virtuele-machine Schaalset verwijderd |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/uitbrei dingen/lezen | Hiermee worden de eigenschappen van een virtuele-machine Scale set-extensie opgehaald |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/Extensions/write | Hiermee wordt een nieuwe extensie van een virtuele-machine Schaalset gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/Action | De platform update domeinen van een virtuele-machine Schaalset van service Fabric hand matig door lopen om een wachtende update te volt ooien |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/instanceView/lezen | Hiermee wordt de instantie weergave van de virtuele-machine Schaalset opgehaald |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/manualUpgrade/Action | Hiermee worden instanties hand matig bijgewerkt naar het laatste model van de virtuele-machine Schaalset |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/networkInterfaces/lezen | Eigenschappen van alle netwerk interfaces van een Schaalset voor virtuele machines ophalen |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/osRollingUpgrade/Action | Start een rolling upgrade om alle exemplaren van virtuele-machine schaal sets te verplaatsen naar de meest recente versie van het besturings systeem van de platform installatie kopie. |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/osUpgradeHistory/lezen | Hiermee wordt de geschiedenis van besturingssysteem upgrades voor een Schaalset voor virtuele machines opgehaald |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/performMaintenance/Action | Hiermee wordt gepland onderhoud uitgevoerd op de exemplaren van de Schaalset voor virtuele machines |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/uitgeschakeld/Action | Hiermee worden de exemplaren van de Schaalset voor virtuele machines uitgeschakeld |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/publicIPAddresses/lezen | Eigenschappen van alle open bare IP-adressen van een Schaalset voor virtuele machines ophalen |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/lezen | De eigenschappen van een Schaalset voor virtuele machines ophalen |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/redeploy/Action | De exemplaren van de Schaalset voor virtuele machines opnieuw implementeren |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/Reimage/Action | Hiermee wordt de installatie kopie van de exemplaren van de virtuele-machine Schaalset opnieuw ingesteld |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/reimageAll/Action | Hiermee worden alle schijven (besturingssysteem schijf en gegevens schijven) opnieuw ingesteld voor de exemplaren van een Schaalset voor virtuele machines  |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/restart/Action | Hiermee worden de exemplaren van de Schaalset voor virtuele machines opnieuw gestart |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/rollingUpgrades/Cancel/Action | Hiermee wordt de rolling upgrade van een Schaalset voor virtuele machines geannuleerd |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/rollingUpgrades/lezen | De meest recente status van de rolling upgrade ophalen voor een Schaalset voor virtuele machines |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/Scale/Action | Controleren of een bestaande Schaalset voor virtuele machines kan worden geschaald/uitgebreid naar het opgegeven aantal instanties |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/sku's/lezen | Een lijst met geldige Sku's voor een bestaande Schaalset voor virtuele machines |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/start/Action | Hiermee worden de exemplaren van de Schaalset voor virtuele machines gestart |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/disallocate/Action | Hiermee worden de reken resources voor een virtuele machine in een VM-Schaalset uitgeschakeld en vrijgegeven. |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/verwijderen | Een specifieke virtuele machine in een VM-Schaalset verwijderen. |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/instanceView/lezen | Hiermee wordt de instantie weergave opgehaald van een virtuele machine in een VM-Schaalset. |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/networkInterfaces/ipConfigurations/publicIPAddresses/lezen | Haal de eigenschappen van een openbaar IP-adres op dat is gemaakt met de virtuele-machine Schaalset. Virtuele-machine schaal sets kunnen Maxi maal één openbaar IP-adres per ipconfiguration maken (privé IP) |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/networkInterfaces/ipConfigurations/Read | Eigenschappen ophalen van een of alle IP-configuraties van een netwerk interface die is gemaakt met de virtuele-machine Schaalset. IP-configuraties vertegenwoordigen privé Ip's |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/networkInterfaces/lezen | Eigenschappen ophalen van een of alle netwerk interfaces van een virtuele machine die is gemaakt met een Schaalset voor virtuele machines |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/performMaintenance/Action | Hiermee wordt gepland onderhoud uitgevoerd op een exemplaar van een virtuele machine in een Schaalset voor virtuele machines |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/uitgeschakeld/Action | Hiermee wordt een exemplaar van een virtuele machine in een VM-Schaalset afgezet. |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/lezen | Hiermee worden de eigenschappen van een virtuele machine in een VM-Schaalset opgehaald |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/redeploy/Action | Hiermee wordt een exemplaar van een virtuele machine in een Schaalset voor virtuele machines opnieuw geïmplementeerd |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/Reimage/Action | Hiermee wordt de installatie kopie van een exemplaar van een virtuele machine in een Schaalset voor virtuele machines opnieuw ingesteld. |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/reimageAll/Action | Hiermee wordt de installatie kopie van alle schijven (besturingssysteem schijf en gegevens schijven) voor het exemplaar van een virtuele machine in een VM-Schaalset opnieuw ingesteld. |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/restart/Action | Hiermee wordt een exemplaar van een virtuele machine in een VM-Schaalset opnieuw opgestart. |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/runCommand/actie | Hiermee wordt een vooraf gedefinieerd script uitgevoerd op een exemplaar van een virtuele machine in een Schaalset met virtuele machines. |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/start/Action | Hiermee start u een exemplaar van een virtuele machine in een VM-Schaalset. |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/informatie/write | Hiermee worden de eigenschappen van een virtuele machine in een VM-Schaalset bijgewerkt |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/toegestane VM/lezen | Beschik bare grootten voor het maken of bijwerken van een virtuele machine in de Schaalset voor virtuele machines weer geven |
> | Bewerking | Micro soft. Compute/virtualMachineScaleSets/schrijven | Hiermee wordt een nieuwe Schaalset voor virtuele machines gemaakt of een bestaande bijgewerkt |

## <a name="microsoftconsumption"></a>Micro soft. verbruik

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. verbruik/tegoeden/lezen | Het overzicht van het gebruik van een facturerings periode voor een beheer groep weer geven. |
> | Bewerking | Micro soft. verbruik/budgetten/verwijderen | De budgetten van een abonnement of een beheer groep verwijderen. |
> | Bewerking | Micro soft. verbruik/budget/lezen | De budgetten op basis van een abonnement of een beheer groep weer geven. |
> | Bewerking | Micro soft. verbruik/budgetten/schrijven | Hiermee maakt en bijwerkt u de budgetten op basis van een abonnement of een beheer groep. |
> | Bewerking | Micro soft. verbruik/toeslagen/lezen | Lijst met kosten |
> | Bewerking | Micro soft. verbruik/tegoed/lezen | Lijst tegoeden |
> | Bewerking | Micro soft. verbruik/gebeurtenissen/lezen | Gebeurtenissen weer geven |
> | Bewerking | Micro soft. verbruik/prognoses/lezen | Prognoses weer geven |
> | Bewerking | Micro soft. verbruik/loten/lezen | Loten weer geven |
> | Bewerking | Micro soft. verbruik/Marketplace/lezen | Vermeld de details van het resource gebruik van Marketplace voor een bereik voor EA en webdirect-abonnementen. |
> | Bewerking | Micro soft. verbruik/operationresults/lezen | Operationresults weer geven |
> | Bewerking | Micro soft. verbruik/bewerkingen/lezen | Een lijst met alle ondersteunde bewerkingen door de resource provider van micro soft. verbruik. |
> | Bewerking | Micro soft. verbruik/operationstatus/lezen | Operationstatus weer geven |
> | Bewerking | Micro soft. verbruik/pricesheets/lezen | De Pricesheets-gegevens voor een abonnement of een beheer groep weer geven. |
> | Bewerking | Micro soft. verbruik/registreren/actie | Registreren voor gebruik van RP |
> | Bewerking | Micro soft. verbruik/reservationDetails/lezen | Vermeld de gebruiks gegevens voor gereserveerde instanties per reserverings order of beheer groepen. De details van de gegevens zijn per exemplaar per niveau. |
> | Bewerking | Micro soft. verbruik/reservationRecommendations/lezen | Een lijst met enkele of gedeelde aanbevelingen voor gereserveerde instanties voor een abonnement. |
> | Bewerking | Micro soft. verbruik/reservationSummaries/lezen | Vermeld het gebruiks overzicht voor gereserveerde instanties per reserverings order of beheer groepen. De overzichts gegevens zijn maandelijks of dagelijks. |
> | Bewerking | Micro soft. verbruik/reservationTransactions/lezen | Vermeld de transactie geschiedenis voor gereserveerde instanties per beheer groep. |
> | Bewerking | Micro soft. verbruik/tags/lezen | Tags weer geven voor EA en abonnementen. |
> | Bewerking | Micro soft. verbruik/tenants/lezen | Tenants weer geven |
> | Bewerking | Micro soft. verbruik/tenants/registreren/actie | Registreer de actie voor het bereik van micro soft. verbruik door een Tenant. |
> | Bewerking | Micro soft. verbruik/voor waarden/lezen | De voor waarden voor een abonnement of een beheer groep weer geven. |
> | Bewerking | Micro soft. verbruik/usageDetails/lezen | Vermeld de gebruiks gegevens voor een bereik voor EA en webdirect-abonnementen. |

## <a name="microsoftcontainerinstance"></a>Micro soft. ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ContainerInstance/containerGroups/containers/exec/actie | In een specifieke container uitvoeren. |
> | Bewerking | Micro soft. ContainerInstance/containerGroups/containers/logboeken/lezen | Logboeken voor een specifieke container ophalen. |
> | Bewerking | Micro soft. ContainerInstance/containerGroups/verwijderen | De specifieke container groep verwijderen. |
> | Bewerking | Micro soft. ContainerInstance/containerGroups/operationResults/lezen | Resultaat van asynchrone bewerking ophalen |
> | Bewerking | Micro soft. ContainerInstance/containerGroups/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor de container groep opgehaald. |
> | Bewerking | Micro soft. ContainerInstance/containerGroups/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de container groep gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ContainerInstance/containerGroups/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor de container groep. |
> | Bewerking | Micro soft. ContainerInstance/containerGroups/lezen | Alle container groepen ophalen. |
> | Bewerking | Micro soft. ContainerInstance/containerGroups/restart/actie | Hiermee wordt een specifieke container groep opnieuw gestart. |
> | Bewerking | Micro soft. ContainerInstance/containerGroups/start/Action | Hiermee start u een specifieke container groep. |
> | Bewerking | Micro soft. ContainerInstance/containerGroups/stop/Action | Hiermee stopt u een specifieke container groep. De toewijzing van reken resources wordt ongedaan gemaakt en de facturering wordt gestopt. |
> | Bewerking | Micro soft. ContainerInstance/containerGroups/schrijven | Een specifieke container groep maken of bijwerken. |
> | Bewerking | Micro soft. ContainerInstance/locaties/cachedImages/lezen | Hiermee worden de in de cache opgeslagen installatie kopieën voor het abonnement in een regio opgehaald. |
> | Bewerking | Micro soft. ContainerInstance/locaties/mogelijkheden/lezen | De mogelijkheden voor een regio ophalen. |
> | Bewerking | Micro soft. ContainerInstance/locaties/deleteVirtualNetworkOrSubnets/actie | Hiermee wordt aan micro soft. ContainerInstance door gemeld dat het virtuele netwerk of subnet wordt verwijderd. |
> | Bewerking | Micro soft. ContainerInstance/locaties/bewerkingen/lezen | De bewerkingen voor de Azure container instance-service weer geven. |
> | Bewerking | Micro soft. ContainerInstance/locaties/gebruik/lezen | Het gebruik voor een bepaalde regio ophalen. |
> | Bewerking | Micro soft. ContainerInstance/Operations/lezen | De bewerkingen voor de Azure container instance-service weer geven. |
> | Bewerking | Micro soft. ContainerInstance/REGI ster/actie | Registreert het abonnement voor de resource provider van de container instantie en maakt het mogelijk om container groepen te maken. |
> | Bewerking | Micro soft. ContainerInstance/serviceassociationlinks/verwijderen | Verwijder de koppeling van de service koppeling die is gemaakt door de resource provider van het Azure container exemplaar op een subnet. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ContainerRegistry/checkNameAvailability/lezen | Controleert of de container register naam beschikbaar is voor gebruik. |
> | Bewerking | Micro soft. ContainerRegistry/locaties/deleteVirtualNetworkOrSubnets/actie | Hiermee wordt aan micro soft. ContainerRegistry door gemeld dat het virtuele netwerk of subnet wordt verwijderd |
> | Bewerking | Micro soft. ContainerRegistry/locaties/operationResults/lezen | Hiermee wordt een asynchroon bewerking resultaat opgehaald |
> | Bewerking | Micro soft. ContainerRegistry/Operations/lezen | Een lijst met alle beschik bare Azure Container Registry REST API bewerkingen |
> | Bewerking | Micro soft. ContainerRegistry/REGI ster/actie | Hiermee wordt het abonnement voor de resource provider van de container register geregistreerd en wordt het maken van container registers ingeschakeld. |
> | Bewerking | Micro soft. ContainerRegistry/registers/artefacten/verwijderen | Artefact verwijderen in container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/builds/annuleren/actie | Hiermee wordt een bestaande build geannuleerd. |
> | Bewerking | Micro soft. ContainerRegistry/registers/builds/getLogLink/actie | Hiermee wordt een koppeling opgehaald om de build-logboeken te downloaden. |
> | Bewerking | Micro soft. ContainerRegistry/registers/builds/lezen | Hiermee worden de eigenschappen van de opgegeven build opgehaald of worden alle builds voor het opgegeven container register weer gegeven. |
> | Bewerking | Micro soft. ContainerRegistry/registers/builds/schrijven | Hiermee wordt een build voor een container register bijgewerkt met de opgegeven para meters. |
> | Bewerking | Micro soft. ContainerRegistry/registers/buildTasks/verwijderen | Hiermee verwijdert u een bouw taak uit een container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/buildTasks/listSourceRepositoryProperties/Action | Hiermee worden de eigenschappen van broncode beheer voor een build-taak weer gegeven. |
> | Bewerking | Micro soft. ContainerRegistry/registers/buildTasks/lezen | Hiermee worden de eigenschappen van de opgegeven build-taak opgehaald of worden alle build-taken voor het opgegeven container register weer gegeven. |
> | Bewerking | Micro soft. ContainerRegistry/registers/buildTasks/stappen/verwijderen | Hiermee verwijdert u een build-stap van een build-taak. |
> | Bewerking | Micro soft. ContainerRegistry/registers/buildTasks/Steps/listBuildArguments/Action | Hier worden de argumenten voor het maken van een build-stap weer gegeven, inclusief de geheime argumenten. |
> | Bewerking | Micro soft. ContainerRegistry/registers/buildTasks/stappen/lezen | Hiermee worden de eigenschappen van de opgegeven build-stap opgehaald of worden alle stappen voor het maken van de opgegeven build-taak weer gegeven. |
> | Bewerking | Micro soft. ContainerRegistry/registers/buildTasks/Steps/write | Hiermee wordt een build-stap voor een build-taak gemaakt of bijgewerkt met de opgegeven para meters. |
> | Bewerking | Micro soft. ContainerRegistry/registers/buildTasks/schrijven | Hiermee wordt een build-taak voor een container register gemaakt of bijgewerkt met de opgegeven para meters. |
> | Bewerking | Micro soft. ContainerRegistry/registers/verwijderen | Hiermee verwijdert u een container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/eventGridFilters/verwijderen | Hiermee verwijdert u een gebeurtenis raster filter uit een container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/eventGridFilters/lezen | Hiermee worden de eigenschappen van het opgegeven gebeurtenis raster filter opgehaald of worden alle Event grid-filters voor het opgegeven container register weer gegeven. |
> | Bewerking | Micro soft. ContainerRegistry/registers/eventGridFilters/schrijven | Hiermee wordt een gebeurtenis raster filter voor een container register met de opgegeven para meters gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ContainerRegistry/registers/getBuildSourceUploadUrl/actie | Hiermee wordt de upload locatie opgehaald zodat de gebruiker de bron kan uploaden. |
> | Bewerking | Micro soft. ContainerRegistry/registers/importImage/actie | Importeer de installatie kopie naar het container register met de opgegeven para meters. |
> | Bewerking | Micro soft. ContainerRegistry/registers/listBuildSourceUploadUrl/actie | URL-locatie voor uploaden van bron voor een container register ophalen. |
> | Bewerking | Micro soft. ContainerRegistry/registers/listCredentials/actie | Geeft een lijst van de aanmeldings referenties voor het opgegeven container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/listPolicies/lezen | Geeft een lijst van de beleids regels voor het opgegeven container register |
> | Bewerking | Micro soft. ContainerRegistry/registers/listUsages/lezen | Hiermee worden de quota gebruikt voor het opgegeven container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/meta gegevens/lezen | Hiermee worden de meta gegevens van een specifieke opslag plaats voor een container register opgehaald |
> | Bewerking | Micro soft. ContainerRegistry/registers/meta gegevens/schrijven | Hiermee worden de meta gegevens van een opslag plaats bijgewerkt voor een container register |
> | Bewerking | Micro soft. ContainerRegistry/registers/operationStatuses/lezen | Hiermee wordt een asynchrone bewerkings status van het REGI ster opgehaald |
> | Bewerking | Micro soft. ContainerRegistry/registers/pull/lezen | Pull of ophalen van installatie kopieën uit een container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/push/schrijven | Push of schrijf installatie kopieën naar een container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/quarantineRead/lezen | In quarantaine geplaatste installatie kopieën verzamelen of ophalen uit het container register |
> | Bewerking | Micro soft. ContainerRegistry/registers/quarantineWrite/schrijven | De quarantaine status van in quarantaine geplaatste installatie kopieën schrijven/wijzigen |
> | Bewerking | Micro soft. ContainerRegistry/registers/queueBuild/actie | Hiermee maakt u een nieuwe build op basis van de aanvraag parameters en voegt u deze toe aan de build-wachtrij. |
> | Bewerking | Micro soft. ContainerRegistry/registers/lezen | Hiermee worden de eigenschappen van het opgegeven container register opgehaald of worden alle container registers weer gegeven onder de opgegeven resource groep of dit abonnement. |
> | Bewerking | Micro soft. ContainerRegistry/registers/regenerateCredential/actie | Hiermee wordt een van de aanmeldings referenties voor het opgegeven container register opnieuw gegenereerd. |
> | Bewerking | Micro soft. ContainerRegistry/registers/replicatie/verwijderen | Hiermee verwijdert u een replicatie uit een container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/replicaties/operationStatuses/lezen | Hiermee wordt de status van een asynchrone replicatie bewerking opgehaald |
> | Bewerking | Micro soft. ContainerRegistry/registers/replicatie/lezen | Hiermee worden de eigenschappen van de opgegeven replicatie opgehaald of worden alle replicaties voor het opgegeven container register weer gegeven. |
> | Bewerking | Micro soft. ContainerRegistry/registers/replicaties/schrijven | Hiermee wordt een replicatie voor een container register met de opgegeven para meters gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ContainerRegistry/registers/runs/annuleren/actie | Een bestaande uitvoering annuleren. |
> | Bewerking | Micro soft. ContainerRegistry/registers/runs/listLogSasUrl/Action | Hiermee wordt de SAS-URL voor het logboek opgehaald voor een run. |
> | Bewerking | Micro soft. ContainerRegistry/registers/runs/lezen | Hiermee worden de eigenschappen opgehaald van een uitvoering op basis van een container register of lijst runs. |
> | Bewerking | Micro soft. ContainerRegistry/registers/runs/schrijven | Hiermee wordt een run bijgewerkt. |
> | Bewerking | Micro soft. ContainerRegistry/registers/scheduleRun/actie | Een uitvoering plannen op basis van een container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/ondertekenen/schrijven | Meta gegevens van de vertrouwens relatie push/pull voor een container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/taken/verwijderen | Hiermee verwijdert u een taak voor een container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/taken/listDetails/actie | Alle details van een taak voor een container register weer geven. |
> | Bewerking | Micro soft. ContainerRegistry/registers/taken/lezen | Hiermee wordt een taak opgehaald voor een container register of worden alle taken weer gegeven. |
> | Bewerking | Micro soft. ContainerRegistry/registers/taken/schrijven | Hiermee wordt een taak voor een container register gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ContainerRegistry/registers/updatePolicies/schrijven | Hiermee wordt het beleid voor het opgegeven container register bijgewerkt |
> | Bewerking | Micro soft. ContainerRegistry/registers/webhooks/verwijderen | Hiermee verwijdert u een webhook uit een container register. |
> | Bewerking | Micro soft. ContainerRegistry/registers/webhooks/getCallbackConfig/actie | Hiermee wordt de configuratie van de service-URI en aangepaste headers voor de webhook opgehaald. |
> | Bewerking | Micro soft. ContainerRegistry/registers/webhooks/listEvents/actie | Een lijst met recente gebeurtenissen voor de opgegeven webhook. |
> | Bewerking | Micro soft. ContainerRegistry/registers/webhooks/operationStatuses/lezen | Hiermee wordt een async-bewerkings status van webhook opgehaald |
> | Bewerking | Micro soft. ContainerRegistry/registers/webhooks/ping/actie | Hiermee wordt een ping-gebeurtenis geactiveerd die naar de webhook moet worden verzonden. |
> | Bewerking | Micro soft. ContainerRegistry/registers/webhooks/lezen | Hiermee worden de eigenschappen van de opgegeven webhook opgehaald of worden alle webhooks voor het opgegeven container register weer gegeven. |
> | Bewerking | Micro soft. ContainerRegistry/registers/webhooks/schrijven | Hiermee wordt een webhook voor een container register met de opgegeven para meters gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ContainerRegistry/registers/schrijven | Hiermee wordt een container register met de opgegeven para meters gemaakt of bijgewerkt. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. container service/containerServices/verwijderen | Hiermee wordt een container service verwijderd |
> | Bewerking | Micro soft. container service/containerServices/lezen | Een container service ophalen |
> | Bewerking | Micro soft. container service/containerServices/schrijven | Hiermee wordt een nieuwe container service gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. container service/locaties/operationresults/lezen | Hiermee wordt de status van een asynchroon bewerkings resultaat opgehaald |
> | Bewerking | Micro soft. container service/locaties/bewerkingen/lezen | Hiermee wordt de status van een asynchrone bewerking opgehaald |
> | Bewerking | Micro soft. container service/locaties/Orchestrator/lezen | Een lijst met ondersteunde Orchestrator |
> | Bewerking | Micro soft. container service/managedClusters/accessProfiles/listCredential/Action | Een beheerd cluster toegangs profiel verkrijgen met een rolnaam met behulp van de lijst referentie |
> | Bewerking | Micro soft. container service/managedClusters/accessProfiles/lezen | Een beheerd cluster toegangs profiel ophalen op basis van rolnaam |
> | Bewerking | Micro soft. container service/managedClusters/agentPools/verwijderen | Hiermee verwijdert u een agent groep |
> | Bewerking | Micro soft. container service/managedClusters/agentPools/lezen | Hiermee wordt een agent groep opgehaald |
> | Bewerking | Micro soft. container service/managedClusters/agentPools/upgradeProfiles/lezen | Hiermee wordt het upgrade Profiel van de agent groep opgehaald |
> | Bewerking | Micro soft. container service/managedClusters/agentPools/write | Hiermee wordt een nieuwe agent groep gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. container service/managedClusters/verwijderen | Hiermee wordt een beheerd cluster verwijderd |
> | Bewerking | Micro soft. container service/managedClusters/detectors/lezen | Beheerde cluster detectie ophalen |
> | Bewerking | Micro soft. container service/managedClusters/diagnosticsState/lezen | Hiermee wordt de diagnostische status van het cluster opgehaald |
> | Bewerking | Micro soft. container service/managedClusters/listClusterAdminCredential/Action | De clusterAdmin-referentie van een beheerd cluster weer geven |
> | Bewerking | Micro soft. container service/managedClusters/listClusterUserCredential/Action | De clusterUser-referentie van een beheerd cluster weer geven |
> | Bewerking | Micro soft. container service/managedClusters/privateEndpointConnectionsApproval/Action | Hiermee wordt bepaald of de gebruiker een verbinding met een privé-eind punt mag goed keuren |
> | Bewerking | Micro soft. container service/managedClusters/lezen | Een beheerd cluster ophalen |
> | Bewerking | Micro soft. container service/managedClusters/resetAADProfile/Action | Het AAD-Profiel van een beheerd cluster opnieuw instellen |
> | Bewerking | Micro soft. container service/managedClusters/resetServicePrincipalProfile/Action | Het Service-Principal-Profiel van een beheerd cluster opnieuw instellen |
> | Bewerking | Micro soft. container service/managedClusters/upgradeProfiles/lezen | Hiermee wordt het upgrade Profiel van het cluster opgehaald |
> | Bewerking | Micro soft. container service/managedClusters/schrijven | Hiermee maakt u een nieuw beheerd cluster of wordt er een bestaande bijgewerkt |
> | Bewerking | Micro soft. container service/openShiftClusters/verwijderen | Een open-Shift-cluster verwijderen |
> | Bewerking | Micro soft. container service/openShiftClusters/lezen | Een open-Shift-cluster ophalen |
> | Bewerking | Micro soft. container service/openShiftClusters/schrijven | Hiermee maakt u een nieuw open-ploeg cluster of werkt u een bestaand item bij |
> | Bewerking | Micro soft. container service/openShiftManagedClusters/verwijderen | Een door open ploeg beheerd cluster verwijderen |
> | Bewerking | Micro soft. container service/openShiftManagedClusters/lezen | Een open Shift-beheerd cluster ophalen |
> | Bewerking | Micro soft. container service/openShiftManagedClusters/schrijven | Hiermee maakt u een nieuw open-Shift-beheerd cluster of wordt een bestaand item bijgewerkt |
> | Bewerking | Micro soft. container service/operations/lezen | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn voor de resource provider micro soft. container service |
> | Bewerking | Micro soft. container service/REGI ster/actie | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. container service |
> | Bewerking | Micro soft. container service/registratie/actie | Registratie van het abonnement met de resource provider micro soft. container service is ongedaan gemaakt |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ContentModerator/toepassingen/verwijderen | Verwijder bewerking |
> | Bewerking | Micro soft. ContentModerator/Applications/listSecrets/actie | Geheimen vermelden |
> | Bewerking | Micro soft. ContentModerator/Applications/listSingleSignOnToken/actie | Tokens voor eenmalige aanmelding lezen |
> | Bewerking | Micro soft. ContentModerator/toepassingen/lezen | Lees bewerking |
> | Bewerking | Micro soft. ContentModerator/toepassingen/schrijven | Schrijf bewerking |
> | Bewerking | Micro soft. ContentModerator/toepassingen/schrijven | Schrijf bewerking |
> | Bewerking | Micro soft. ContentModerator/listCommunicationPreference/actie | Communicatie voorkeur weer geven |
> | Bewerking | Micro soft. ContentModerator/Operations/lezen | Lees bewerkingen |
> | Bewerking | Micro soft. ContentModerator/updateCommunicationPreference/actie | Communicatie voorkeur bijwerken |

## <a name="microsoftcostmanagement"></a>Micro soft. CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. CostManagement/cloudConnectors/verwijderen | De opgegeven Cloud connector verwijderen. |
> | Bewerking | Micro soft. CostManagement/cloudConnectors/lezen | De cloudConnectors voor de geverifieerde gebruiker weer geven. |
> | Bewerking | Micro soft. CostManagement/cloudConnectors/schrijven | De opgegeven Cloud connector maken of bijwerken. |
> | Bewerking | Micro soft. CostManagement/Dimensions/lezen | Een lijst met alle ondersteunde dimensies op basis van een bereik. |
> | Bewerking | Micro soft. CostManagement/export acties/actie | Voer de opgegeven export uit. |
> | Bewerking | Micro soft. CostManagement/export/Delete | De opgegeven export verwijderen. |
> | Bewerking | Micro soft. CostManagement/export/Read | De exports op basis van het bereik weer geven. |
> | Bewerking | Micro soft. CostManagement/export/run/Action | Uitvoer uitvoeren. |
> | Bewerking | Micro soft. CostManagement/export/write | De opgegeven export maken of bijwerken. |
> | Bewerking | Micro soft. CostManagement/externalBillingAccounts/externalSubscriptions/lezen | De externalSubscriptions in een externalBillingAccount voor de geverifieerde gebruiker weer geven. |
> | Bewerking | Micro soft. CostManagement/externalBillingAccounts/lezen | De externalBillingAccounts voor de geverifieerde gebruiker weer geven. |
> | Bewerking | Micro soft. CostManagement/externalSubscriptions/lezen | De externalSubscriptions voor de geverifieerde gebruiker weer geven. |
> | Bewerking | Micro soft. CostManagement/externalSubscriptions/schrijven | Gekoppelde beheer groep van externalSubscription bijwerken |
> | Bewerking | Micro soft. CostManagement/query/actie | Query's uitvoeren op gebruiks gegevens met een bereik. |
> | Bewerking | Micro soft. CostManagement/query/lezen | Query's uitvoeren op gebruiks gegevens met een bereik. |
> | Bewerking | Micro soft. CostManagement/REGI ster/actie | Registreer de actie voor het bereik van micro soft. CostManagement door een abonnement. |
> | Bewerking | Micro soft. CostManagement/Reports/actie | Plan rapporten over gebruiks gegevens met een bereik. |
> | Bewerking | Micro soft. CostManagement/rapporten/lezen | Plan rapporten over gebruiks gegevens met een bereik. |
> | Bewerking | Micro soft. CostManagement/tenants/registreren/actie | Registreer de actie voor het bereik van micro soft. CostManagement door een Tenant. |

## <a name="microsoftdatabox"></a>Micro soft. DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DataBox/Jobs/bookShipmentPickUp/actie | Hiermee kunt u een ophalen van retour zendingen boeken. |
> | Bewerking | Micro soft. DataBox/Jobs/annuleren/actie | Hiermee wordt een volg orde die wordt uitgevoerd, geannuleerd. |
> | Bewerking | Micro soft. DataBox/Jobs/verwijderen | De orders verwijderen |
> | Bewerking | Micro soft. DataBox/Jobs/listCredentials/actie | Geeft een lijst van de niet-versleutelde referenties die zijn gerelateerd aan de order. |
> | Bewerking | Micro soft. DataBox/Jobs/lezen | De orders weer geven of ophalen |
> | Bewerking | Micro soft. DataBox/Jobs/schrijven | De orders maken of bijwerken |
> | Bewerking | Micro soft. DataBox/locaties/availableSkus/actie | Met deze methode wordt de lijst met beschik bare sku's geretourneerd. |
> | Bewerking | Micro soft. DataBox/locaties/availableSkus/lezen | De beschik bare Sku's weer geven of ophalen |
> | Bewerking | Micro soft. DataBox/locaties/operationResults/lezen | De resultaten van de bewerking weer geven of ophalen |
> | Bewerking | Micro soft. DataBox/locaties/regionConfiguration/actie | Deze methode retourneert de configuraties voor de regio. |
> | Bewerking | Micro soft. DataBox/locaties/validateAddress/actie | Valideert het verzend adres en levert eventueel alternatieve adressen. |
> | Bewerking | Micro soft. DataBox/locaties/validateInputs/actie | Met deze methode worden alle typen validaties ondersteund. |
> | Bewerking | Micro soft. DataBox/Operations/lezen | De bewerkingen weer geven of ophalen |
> | Bewerking | Micro soft. DataBox/REGI ster/actie | Provider micro soft. Databox registreren |
> | Bewerking | Micro soft. DataBox/abonnementen/resourceGroups/moveResources/actie | Met deze methode wordt de resource verplaatsing uitgevoerd. |
> | Bewerking | Micro soft. DataBox/abonnementen/resourceGroups/validateMoveResources/actie | Met deze methode wordt gecontroleerd of het verplaatsen van de resource is toegestaan. |
> | Bewerking | Micro soft. DataBox/registratie/actie | De registratie van de provider micro soft. Databox ongedaan maken |

## <a name="microsoftdataboxedge"></a>Micro soft. DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/Alerts/lezen | De waarschuwingen weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/Alerts/lezen | De waarschuwingen weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/verwijderen | Hiermee worden de bandbreedte schema's verwijderd |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/lezen | Het resultaat van de bewerking weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/lezen | De bandbreedte schema's weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/lezen | De bandbreedte schema's weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Hiermee worden de bandbreedte schema's gemaakt of bijgewerkt |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/verwijderen | Hiermee worden de Data Box Edge-apparaten verwijderd |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/Action | Updates downloaden in het apparaat |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/Action | Hiermee wordt uitgebreide informatie over de resource opgehaald |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/installUpdates/Action | Updates installeren op het apparaat |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/Jobs/lezen | De taken weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/networkSettings/lezen | De netwerk instellingen van het apparaat weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/nodes/Read | Hiermee worden de knoop punten weer gegeven of opgehaald |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/operationResults/lezen | Het resultaat van de bewerking weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/operationsStatus/lezen | De bewerkings status weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/orders/verwijderen | De orders worden verwijderd |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/lezen | Het resultaat van de bewerking weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/orders/lezen | De orders weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/orders/lezen | De orders weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/orders/schrijven | Hiermee worden de orders gemaakt of bijgewerkt |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/lezen | De Data Box Edge-apparaten weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/lezen | De Data Box Edge-apparaten weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/lezen | De Data Box Edge-apparaten weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/roles/verwijderen | Hiermee worden de rollen verwijderd |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/lezen | Het resultaat van de bewerking weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/roles/lezen | Hiermee worden de rollen weer gegeven of opgehaald |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/roles/lezen | Hiermee worden de rollen weer gegeven of opgehaald |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/roles/schrijven | Hiermee worden de rollen gemaakt of bijgewerkt |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/Action | Zoeken naar updates |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/lezen | Het resultaat van de bewerking weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/actie | Beveiligings instellingen bijwerken |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/shares/verwijderen | Hiermee worden de shares verwijderd |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/lezen | Het resultaat van de bewerking weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/shares/lezen | De shares weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/shares/lezen | De shares weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/shares/vernieuwen/actie | De meta gegevens van de share met de gegevens uit de Cloud vernieuwen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/shares/schrijven | Hiermee worden de shares gemaakt of bijgewerkt |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/verwijderen | Hiermee verwijdert u de referenties van het opslag account |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/lezen | Het resultaat van de bewerking weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/lezen | De referenties van het opslag account worden weer gegeven of opgehaald |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/lezen | De referenties van het opslag account worden weer gegeven of opgehaald |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Hiermee worden de referenties van het opslag account gemaakt of bijgewerkt |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/triggers/verwijderen | Hiermee verwijdert u de triggers |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/lezen | Het resultaat van de bewerking weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/triggers/lezen | De triggers worden weer gegeven of opgehaald |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/triggers/lezen | De triggers worden weer gegeven of opgehaald |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/triggers/schrijven | Hiermee worden de triggers gemaakt of bijgewerkt |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/update Summary/lezen | De samen vatting van de update weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/Action | Certificaat voor apparaatregistratie uploaden |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/gebruikers/verwijderen | Hiermee verwijdert u de gebruikers delen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/users/operationResults/lezen | Het resultaat van de bewerking weer geven of ophalen |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/users/lezen | Hiermee worden de gebruikers van de share weer gegeven of opgehaald |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/users/lezen | Hiermee worden de gebruikers van de share weer gegeven of opgehaald |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/gebruikers/schrijven | Hiermee worden de share gebruikers gemaakt of bijgewerkt |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/schrijven | Hiermee worden de Data Box Edge-apparaten gemaakt of bijgewerkt |
> | Bewerking | Micro soft. DataBoxEdge/dataBoxEdgeDevices/schrijven | Hiermee worden de Data Box Edge-apparaten gemaakt of bijgewerkt |

## <a name="microsoftdatabricks"></a>Micro soft. Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Databricks/locaties/getNetworkPolicies/actie | Netwerk intentie beleid voor een subnet ophalen op basis van de locatie die wordt gebruikt door NRP |
> | Bewerking | Micro soft. Databricks/REGI ster/actie | Meld u aan bij Databricks. |
> | Bewerking | Micro soft. Databricks/werk ruimten/verwijderen | Hiermee verwijdert u een Databricks-werk ruimte. |
> | Bewerking | Micro soft. Databricks/werk ruimten/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee stelt u de beschik bare Diagnostische instellingen voor de Databricks-werk ruimte in |
> | Bewerking | Micro soft. Databricks/werk ruimten/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instellingen toevoegen of wijzigen. |
> | Bewerking | Micro soft. Databricks/werk ruimten/providers/micro soft. Insights/logDefinitions/lezen | Hiermee worden de beschik bare logboek definities voor de Databricks-werk ruimte opgehaald |
> | Bewerking | Micro soft. Databricks/werk ruimten/lezen | Hiermee wordt een lijst met Databricks-werk ruimten opgehaald. |
> | Bewerking | Micro soft. Databricks/werk ruimten/schrijven | Hiermee maakt u een Databricks-werk ruimte. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DataCatalog/catalogi/verwijderen | Catalogus bron voor Data Catalog resource provider verwijderen. |
> | Bewerking | Micro soft. DataCatalog/catalogi/lezen | Lees de resource van de catalogus voor de resource provider van Data Catalog. |
> | Bewerking | Micro soft. DataCatalog/catalogi/schrijven | Bron voor het schrijven van catalogi voor Data Catalog resource provider. |
> | Bewerking | Micro soft. DataCatalog/checkNameAvailability/lezen | Controleer de beschik baarheid van de catalogus naam voor de resource provider van Data Catalog. |
> | Bewerking | Micro soft. DataCatalog/datacatalogs/verwijderen | Verwijder de DataCatalog-resource voor de resource provider van Data Catalog. |
> | Bewerking | Micro soft. DataCatalog/datacatalogs/lezen | Lees de DataCatalog-resource voor de resource provider van Data Catalog. |
> | Bewerking | Micro soft. DataCatalog/datacatalogs/schrijven | Schrijf de DataCatalog-resource voor de resource provider van Data Catalog. |
> | Bewerking | Micro soft. DataCatalog/Operations/lezen | Hiermee worden alle beschik bare bewerkingen in Data Catalog resource provider gelezen. |
> | Bewerking | Micro soft. DataCatalog/REGI ster/actie | Het abonnement voor de resource provider van Data Catalog registreren |
> | Bewerking | Micro soft. DataCatalog/registratie/actie | De registratie van het abonnement voor Data Catalog resource provider ongedaan maken |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DataFactory/checkazuredatafactorynameavailability/lezen | Hiermee wordt gecontroleerd of de naam van de Data Factory beschikbaar is voor gebruik. |
> | Bewerking | Micro soft. DataFactory/datafactories/activitywindows/lezen | Hiermee worden activiteiten Vensters in de Data Factory met opgegeven para meters gelezen. |
> | Bewerking | Micro soft. DataFactory/datafactories/datapipelines/activities/activitywindows/lezen | Hiermee worden activiteit Vensters voor de pijplijn activiteit gelezen met de opgegeven para meters. |
> | Bewerking | Micro soft. DataFactory/datafactories/datapipelines/activitywindows/lezen | Hiermee worden activiteiten Vensters voor de pijp lijn met de opgegeven para meters gelezen. |
> | Bewerking | Micro soft. DataFactory/datafactories/datapipelines/verwijderen | Hiermee verwijdert u een pijp lijn. |
> | Bewerking | Micro soft. DataFactory/datafactories/datapipelines/Pause/actie | Hiermee wordt een pijp lijn onderbroken. |
> | Bewerking | Micro soft. DataFactory/datafactories/datapipelines/lezen | Hiermee wordt een pijp lijn gelezen. |
> | Bewerking | Micro soft. DataFactory/datafactories/datapipelines/resume/actie | Hiermee wordt een pijp lijn hervat. |
> | Bewerking | Micro soft. DataFactory/datafactories/datapipelines/update/actie | Hiermee werkt u een pijp lijn bij. |
> | Bewerking | Micro soft. DataFactory/datafactories/datapipelines/schrijven | Hiermee wordt een pijp lijn gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. DataFactory/datafactories/data sets/activitywindows/lezen | Hiermee worden activiteiten Vensters voor de gegevensset met opgegeven para meters gelezen. |
> | Bewerking | Micro soft. DataFactory/datafactories/gegevens sets/verwijderen | Hiermee verwijdert u alle gegevensset. |
> | Bewerking | Micro soft. DataFactory/datafactories/gegevens sets/lezen | Hiermee wordt elke gegevensset gelezen. |
> | Bewerking | Micro soft. DataFactory/datafactories/data sets/sliceruns/lezen | Hiermee wordt de uitvoering van het gegevens segment voor de opgegeven gegevensset gelezen met de opgegeven begin tijd. |
> | Bewerking | Micro soft. DataFactory/datafactories/data sets/slices/lezen | Hiermee worden de gegevens segmenten in de opgegeven periode opgehaald. |
> | Bewerking | Micro soft. DataFactory/datafactories/data sets/slices/write | De status van het gegevens segment bijwerken. |
> | Bewerking | Micro soft. DataFactory/datafactories/data sets/schrijven | Hiermee wordt een gegevensset gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. DataFactory/datafactories/verwijderen | Hiermee verwijdert u de Data Factory. |
> | Bewerking | Micro soft. DataFactory/datafactories/gateways/Connection info/actie | Hiermee worden de verbindings gegevens voor elke gateway gelezen. |
> | Bewerking | Micro soft. DataFactory/datafactories/gateways/verwijderen | Hiermee verwijdert u alle gateways. |
> | Bewerking | Micro soft. DataFactory/datafactories/gateways/listauthkeys/actie | Geeft een lijst van de verificatie sleutels voor elke gateway. |
> | Bewerking | Micro soft. DataFactory/datafactories/gateways/lezen | Alle gateways worden gelezen. |
> | Bewerking | Micro soft. DataFactory/datafactories/gateways/regenerateauthkey/actie | Hiermee worden de verificatie sleutels voor alle gateways opnieuw gegenereerd. |
> | Bewerking | Micro soft. DataFactory/datafactories/gateways/schrijven | Hiermee wordt een gateway gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. DataFactory/datafactories/linkedServices/verwijderen | Hiermee verwijdert u alle gekoppelde services. |
> | Bewerking | Micro soft. DataFactory/datafactories/linkedServices/lezen | Hiermee wordt alle gekoppelde services gelezen. |
> | Bewerking | Micro soft. DataFactory/datafactories/linkedServices/schrijven | Hiermee wordt een gekoppelde service gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. DataFactory/datafactories/lezen | Hiermee wordt de Data Factory gelezen. |
> | Bewerking | Micro soft. DataFactory/datafactories/runs/LogInfo/lezen | Hiermee leest u een SAS-URI naar een BLOB-container die de logboeken bevat. |
> | Bewerking | Micro soft. DataFactory/datafactories/Tables/verwijderen | Hiermee verwijdert u alle gegevensset. |
> | Bewerking | Micro soft. DataFactory/datafactories/Tables/lezen | Hiermee wordt elke gegevensset gelezen. |
> | Bewerking | Micro soft. DataFactory/datafactories/Tables/schrijven | Hiermee wordt een gegevensset gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. DataFactory/datafactories/schrijven | Hiermee wordt de Data Factory gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. DataFactory/factories/cancelpipelinerun/actie | Annuleert de uitvoering van de pijp lijn die is opgegeven door de run-ID. |
> | Bewerking | Micro soft. DataFactory/factories/cancelSandboxPipelineRun/actie | Hiermee wordt een debug-uitvoering voor de pijp lijn geannuleerd. |
> | Bewerking | Micro soft. DataFactory/factories/createdataflowdebugsession/actie | Hiermee maakt u een foutopsporingssessie voor gegevens stromen. |
> | Bewerking | Micro soft. DataFactory/factories/gegevens stromen/verwijderen | Hiermee verwijdert u de gegevens stroom. |
> | Bewerking | Micro soft. DataFactory/fabrieken/gegevens stromen/lezen | Hiermee wordt de gegevens stroom gelezen. |
> | Bewerking | Micro soft. DataFactory/fabrieken/gegevens stromen/schrijven | Gegevens stroom maken of bijwerken |
> | Bewerking | Micro soft. DataFactory/factories/gegevens sets/verwijderen | Hiermee verwijdert u alle gegevensset. |
> | Bewerking | Micro soft. DataFactory/fabrieken/gegevens sets/lezen | Hiermee wordt elke gegevensset gelezen. |
> | Bewerking | Micro soft. DataFactory/fabrieken/gegevens sets/schrijven | Hiermee wordt een gegevensset gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. DataFactory/factories/debugpipelineruns/annuleren/actie | Hiermee wordt een debug-uitvoering voor de pijp lijn geannuleerd. |
> | Bewerking | Micro soft. DataFactory/factories/verwijderen | Hiermee verwijdert u Data Factory. |
> | Bewerking | Micro soft. DataFactory/factories/deletedataflowdebugsession/actie | Hiermee verwijdert u een foutopsporingssessie voor gegevens stromen. |
> | Bewerking | Micro soft. DataFactory/factories/getDataPlaneAccess/actie | Hiermee krijgt u toegang tot de ADF DataPlane-service. |
> | Bewerking | Micro soft. DataFactory/fabrieken/getDataPlaneAccess/lezen | Hiermee leest u de toegang tot de ADF DataPlane-service. |
> | Bewerking | Micro soft. DataFactory/factories/getFeatureValue/actie | De waarde voor de functie voor belichtings controle ophalen voor de specifieke locatie. |
> | Bewerking | Micro soft. DataFactory/fabrieken/getFeatureValue/lezen | Hiermee wordt de waarde voor de belichtings controle functie voor de specifieke locatie gelezen. |
> | Bewerking | Micro soft. DataFactory/factories/getGitHubAccessToken/actie | Hiermee wordt het toegangs token voor GitHub opgehaald. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/verwijderen | Hiermee worden alle Integration Runtime verwijderd. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/getconnectioninfo/lezen | Integration Runtime verbindings gegevens lezen. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/getObjectMetadata/actie | Ontvang SSIS Integration Runtime meta gegevens voor het opgegeven Integration Runtime. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/getStatus/lezen | Hiermee wordt Integration Runtime status gelezen. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/actie | Een gekoppelde Integration Runtime verwijzing maken voor de opgegeven gedeelde Integration Runtime. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/listauthkeys/lezen | Geeft een lijst van de verificatie sleutels voor een Integration Runtime. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/monitoringdata/lezen | Hiermee haalt u de bewakings gegevens voor alle Integration Runtime op. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/knoop punten/verwijderen | Hiermee wordt het knoop punt voor de opgegeven Integration Runtime verwijderd. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/nodes/ipAddress/actie | Retourneert het IP-adres voor het opgegeven knoop punt van de Integration Runtime. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/nodes/lezen | Hiermee leest u het knoop punt voor de opgegeven Integration Runtime. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/nodes/write | Hiermee wordt een zelf-hostend Integration Runtime knoop punt bijgewerkt. |
> | Bewerking | Micro soft. DataFactory/fabrieken/integrationruntimes/lezen | Hiermee worden alle Integration Runtime gelezen. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/refreshObjectMetadata/actie | Integration Runtime-meta gegevens voor SSIS vernieuwen voor het opgegeven Integration Runtime. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/regenerateauthkey/actie | Hiermee worden de verificatie sleutels voor de opgegeven Integration Runtime opnieuw gegenereerd. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/removelinks/actie | Hiermee verwijdert u gekoppelde Integration Runtime verwijzingen van de opgegeven Integration Runtime. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/start/Action | Start alle Integration Runtime. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/stop/actie | Hiermee worden alle Integration Runtime gestopt. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/synccredentials/actie | Synchroniseert de referenties voor de opgegeven Integration Runtime. |
> | Bewerking | Micro soft. DataFactory/factories/integrationruntimes/upgrade/actie | Hiermee wordt de opgegeven Integration Runtime bijgewerkt. |
> | Bewerking | Micro soft. DataFactory/fabrieken/integrationruntimes/schrijven | Hiermee wordt een Integration Runtime gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. DataFactory/factories/linkedServices/verwijderen | Hiermee verwijdert u de gekoppelde service. |
> | Bewerking | Micro soft. DataFactory/fabrieken/linkedServices/lezen | Hiermee wordt de gekoppelde service gelezen. |
> | Bewerking | Micro soft. DataFactory/fabrieken/linkedServices/schrijven | Een gekoppelde service maken of bijwerken |
> | Bewerking | Micro soft. DataFactory/factories/pipelineruns/activityruns/lezen | Hiermee leest u de uitvoering van de activiteit voor de opgegeven ID van de pijplijn uitvoering. |
> | Bewerking | Micro soft. DataFactory/factories/pipelineruns/annuleren/actie | Annuleert de uitvoering van de pijp lijn die is opgegeven door de run-ID. |
> | Bewerking | Micro soft. DataFactory/factories/pipelineruns/queryactivityruns/actie | Query's uitvoeren op de uitvoering van de activiteit voor de opgegeven pijp lijn run ID. |
> | Bewerking | Micro soft. DataFactory/factories/pipelineruns/queryactivityruns/lezen | Hiermee wordt het resultaat van de uitvoering van de query activiteit voor de opgegeven pijplijn run-ID gelezen. |
> | Bewerking | Micro soft. DataFactory/fabrieken/pipelineruns/lezen | De pijplijn uitvoeringen worden gelezen. |
> | Bewerking | Micro soft. DataFactory/factories/pijp lijnen/createrun/actie | Hiermee maakt u een uitvoering voor de pijp lijn. |
> | Bewerking | Micro soft. DataFactory/factories/pijp lijnen/verwijderen | Hiermee verwijdert u de pijp lijn. |
> | Bewerking | Micro soft. DataFactory/fabrieken/pijp lijnen/pipelineruns/activityruns/voortgang/lezen | Hiermee wordt de voortgang van de uitvoering van de activiteit opgehaald. |
> | Bewerking | Micro soft. DataFactory/fabrieken/pijp lijnen/pipelineruns/lezen | Hiermee leest u de pijplijn uitvoering. |
> | Bewerking | Micro soft. DataFactory/fabrieken/pijp lijnen/lezen | Hiermee leest u de pijp lijn. |
> | Bewerking | Micro soft. DataFactory/fabrieken/pijp lijnen/sandbox/actie | Hiermee maakt u een uitvoerings omgeving voor fout opsporing voor de pijp lijn. |
> | Bewerking | Micro soft. DataFactory/fabrieken/pijp lijnen/sandbox/maken/actie | Hiermee maakt u een uitvoerings omgeving voor fout opsporing voor de pijp lijn. |
> | Bewerking | Micro soft. DataFactory/fabrieken/pijp lijnen/sandbox/uitvoeren/actie | Hiermee maakt u een debug-uitvoering voor de pijp lijn. |
> | Bewerking | Micro soft. DataFactory/fabrieken/pijp lijnen/schrijven | Pijp lijn maken of bijwerken |
> | Bewerking | Micro soft. DataFactory/factories/querydebugpipelineruns/actie | Voert een query uit op de uitvoeringen van de pijp lijn. |
> | Bewerking | Micro soft. DataFactory/factories/querypipelineruns/actie | Query's uitvoeren op de pijplijn uitvoeringen. |
> | Bewerking | Micro soft. DataFactory/fabrieken/querypipelineruns/lezen | Hiermee wordt het resultaat van de query pijplijn uitvoeringen gelezen. |
> | Bewerking | Micro soft. DataFactory/factories/querytriggerruns/actie | Query's uitvoeren op de trigger. |
> | Bewerking | Micro soft. DataFactory/fabrieken/querytriggerruns/lezen | Hiermee wordt het resultaat van trigger uitvoeringen gelezen. |
> | Bewerking | Micro soft. DataFactory/fabrieken/lezen | Data Factory lezen. |
> | Bewerking | Micro soft. DataFactory/factories/sandboxpipelineruns/sandboxActivityRuns/lezen | Hiermee worden de uitvoerings gegevens van de fout opsporing voor de activiteit opgehaald. |
> | Bewerking | Micro soft. DataFactory/factories/startdataflowdebugsession/actie | Start een sessie voor fout opsporing van gegevens stromen. |
> | Bewerking | Micro soft. DataFactory/factories/submitDataFlowForPreview/actie | Verzend gegevens stroom om het voor beeld van gegevens op te halen met behulp van een foutopsporingssessie. |
> | Bewerking | Micro soft. DataFactory/fabrieken/triggerruns/lezen | Hiermee wordt de uitvoering van de trigger gelezen. |
> | Bewerking | Micro soft. DataFactory/factories/triggers/verwijderen | Hiermee verwijdert u een trigger. |
> | Bewerking | Micro soft. DataFactory/factories/triggers/lezen | Hiermee wordt elke trigger gelezen. |
> | Bewerking | Micro soft. DataFactory/fabrieken/triggers/starten/actie | Hiermee wordt elke trigger gestart. |
> | Bewerking | Micro soft. DataFactory/fabrieken/Triggers/stoppen/actie | Hiermee stopt u een trigger. |
> | Bewerking | Micro soft. DataFactory/fabrieken/triggers/triggerruns/lezen | Hiermee wordt de uitvoering van de trigger gelezen. |
> | Bewerking | Micro soft. DataFactory/fabrieken/triggers/schrijven | Hiermee wordt een trigger gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. DataFactory/fabrieken/schrijven | Data Factory maken of bijwerken |
> | Bewerking | Micro soft. DataFactory/locaties/configureFactoryRepo/actie | Hiermee configureert u de opslag plaats voor de Factory. |
> | Bewerking | Micro soft. DataFactory/locaties/getFeatureValue/actie | De waarde voor de functie voor belichtings controle ophalen voor de specifieke locatie. |
> | Bewerking | Micro soft. DataFactory/locaties/getFeatureValue/lezen | Hiermee wordt de waarde voor de belichtings controle functie voor de specifieke locatie gelezen. |
> | Bewerking | Micro soft. DataFactory/Operations/lezen | Hiermee worden alle bewerkingen in micro soft Data Factory provider gelezen. |
> | Bewerking | Micro soft. DataFactory/registreren/actie | Hiermee wordt het abonnement voor de Data Factory resource provider geregistreerd. |
> | Bewerking | Micro soft. DataFactory/registratie/actie | Hiermee wordt de registratie van het abonnement voor de resource provider Data Factory verwijderd. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/computePolicies/verwijderen | Een reken beleid verwijderen. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/computePolicies/lezen | Informatie over een reken beleid ophalen. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/computePolicies/schrijven | Een reken beleid maken of bijwerken. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/verwijderen | Een Data Lake Store-account ontkoppelen van een DataLakeAnalytics-account. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/lezen | Informatie over een gekoppeld data Lake Store-account van een DataLakeAnalytics-account ophalen. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/schrijven | Een gekoppeld data Lake Store-account maken of bijwerken van een DataLakeAnalytics-account. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/verwijderen | Een DataLakeAnalytics-account verwijderen. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/firewallRules/verwijderen | Een firewall regel verwijderen. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/firewallRules/lezen | Informatie over een firewall regel ophalen. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/firewallRules/schrijven | Een firewall regel maken of bijwerken. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/operationResults/lezen | Resultaat van een DataLakeAnalytics-account bewerking ophalen. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/lezen | Informatie over een bestaand DataLakeAnalytics-account ophalen. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/Storage accounts/containers/listSasTokens/actie | Vermeld SAS-tokens voor opslag containers van een gekoppeld opslag account van een DataLakeAnalytics-account. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/Storage accounts/containers/lezen | Containers ophalen van een gekoppeld opslag account van een DataLakeAnalytics-account. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/Storage accounts/verwijderen | Een opslag account ontkoppelen van een DataLakeAnalytics-account. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/Storage accounts/lezen | Informatie ophalen over een gekoppeld opslag account van een DataLakeAnalytics-account. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/Storage accounts/schrijven | Een gekoppeld opslag account maken of bijwerken van een DataLakeAnalytics-account. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/TakeOwnership/actie | Machtigingen verlenen om taken te annuleren die door andere gebruikers zijn verzonden. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/transferAnalyticsUnits/actie | SystemMaxAnalyticsUnits over DataLakeAnalytics-accounts overdragen. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/virtualNetworkRules/verwijderen | Verwijder een regel voor het virtuele netwerk. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/virtualNetworkRules/lezen | Informatie over een virtueel netwerk regel ophalen. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/virtualNetworkRules/schrijven | Een regel voor een virtueel netwerk maken of bijwerken. |
> | Bewerking | Micro soft. DataLakeAnalytics/accounts/schrijven | Een DataLakeAnalytics-account maken of bijwerken. |
> | Bewerking | Micro soft. DataLakeAnalytics/locaties/mogelijkheid/lezen | Informatie over de mogelijkheden van een abonnement over het gebruik van DataLakeAnalytics. |
> | Bewerking | Micro soft. DataLakeAnalytics/locaties/checkNameAvailability/actie | Controleer de beschik baarheid van een DataLakeAnalytics-account naam. |
> | Bewerking | Micro soft. DataLakeAnalytics/locaties/operationResults/lezen | Resultaat van een DataLakeAnalytics-account bewerking ophalen. |
> | Bewerking | Micro soft. DataLakeAnalytics/locaties/gebruik/lezen | Gegevens over quotum gebruik ophalen van een abonnement met behulp van DataLakeAnalytics. |
> | Bewerking | Micro soft. DataLakeAnalytics/Operations/lezen | Beschik bare bewerkingen voor DataLakeAnalytics ophalen. |
> | Bewerking | Micro soft. DataLakeAnalytics/REGI ster/actie | Registreer het abonnement op DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. data Lake Store/accounts/verwijderen | Een Data Lake Store-account verwijderen. |
> | Bewerking | Micro soft. data Lake Store/accounts/enableKeyVault/actie | Schakel de sleutel kluis in voor een Data Lake Store-account. |
> | Bewerking | Micro soft. data Lake Store/accounts/eventGridFilters/verwijderen | Een EventGrid-filter verwijderen. |
> | Bewerking | Micro soft. data Lake Store/accounts/eventGridFilters/lezen | Een EventGrid-filter ophalen. |
> | Bewerking | Micro soft. data Lake Store/accounts/eventGridFilters/schrijven | Een EventGrid-filter maken of bijwerken. |
> | Bewerking | Micro soft. data Lake Store/accounts/firewallRules/verwijderen | Een firewall regel verwijderen. |
> | Bewerking | Micro soft. data Lake Store/accounts/firewallRules/lezen | Informatie over een firewall regel ophalen. |
> | Bewerking | Micro soft. data Lake Store/accounts/firewallRules/schrijven | Een firewall regel maken of bijwerken. |
> | Bewerking | Micro soft. data Lake Store/accounts/operationResults/lezen | Resultaat van een Data Lake Store-account bewerking ophalen. |
> | Bewerking | Micro soft. data Lake Store/accounts/lezen | Informatie over een bestaand data Lake Store-account ophalen. |
> | Bewerking | Micro soft. data Lake Store/accounts/shares/verwijderen | Verwijder een share. |
> | Bewerking | Micro soft. data Lake Store/accounts/shares/lezen | Informatie over een share ophalen. |
> | Bewerking | Micro soft. data Lake Store/accounts/shares/schrijven | Een share maken of bijwerken. |
> | Bewerking | Microsoft.DataLakeStore/accounts/Superuser/action | Verleen de beheerder aan Data Lake Store wanneer deze is verleend met micro soft. Authorization/roleAssignments/write. |
> | Bewerking | Micro soft. data Lake Store/accounts/trustedIdProviders/verwijderen | Verwijder een vertrouwde id-provider. |
> | Bewerking | Micro soft. data Lake Store/accounts/trustedIdProviders/lezen | Informatie over een vertrouwde id-provider ophalen. |
> | Bewerking | Micro soft. data Lake Store/accounts/trustedIdProviders/schrijven | Een vertrouwde id-provider maken of bijwerken. |
> | Bewerking | Micro soft. data Lake Store/accounts/virtualNetworkRules/verwijderen | Verwijder een regel voor het virtuele netwerk. |
> | Bewerking | Micro soft. data Lake Store/accounts/virtualNetworkRules/lezen | Informatie over een virtueel netwerk regel ophalen. |
> | Bewerking | Micro soft. data Lake Store/accounts/virtualNetworkRules/schrijven | Een regel voor een virtueel netwerk maken of bijwerken. |
> | Bewerking | Micro soft. data Lake Store/accounts/schrijven | Een Data Lake Store-account maken of bijwerken. |
> | Bewerking | Micro soft. data Lake Store/locaties/mogelijkheid/lezen | Informatie over de mogelijkheden van een abonnement over het gebruik van data Lake Store. |
> | Bewerking | Micro soft. data Lake Store/locaties/checkNameAvailability/actie | Controleer de beschik baarheid van een Data Lake Store-account naam. |
> | Bewerking | Micro soft. data Lake Store/locaties/operationResults/lezen | Resultaat van een Data Lake Store-account bewerking ophalen. |
> | Bewerking | Micro soft. data Lake Store/locaties/gebruik/lezen | Gegevens over quotum gebruik ophalen van een abonnement met behulp van data Lake Store. |
> | Bewerking | Micro soft. data Lake Store/Operations/lezen | Beschik bare bewerkingen voor data Lake Store ophalen. |
> | Bewerking | Micro soft. data Lake Store/REGI ster/actie | Registreer het abonnement op Data Lake Store. |

## <a name="microsoftdatamigration"></a>Micro soft. DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DataMigration/locaties/operationResults/lezen | De status ophalen van een langlopende bewerking gerelateerd aan een 202 geaccepteerde reactie |
> | Bewerking | Micro soft. DataMigration/locaties/operationStatuses/lezen | De status ophalen van een langlopende bewerking gerelateerd aan een 202 geaccepteerde reactie |
> | Bewerking | Micro soft. DataMigration/REGI ster/actie | Hiermee wordt het abonnement geregistreerd bij de Azure Database Migration Service Provider |
> | Bewerking | Micro soft. DataMigration/Services/addWorker/actie | Voegt een DMS-werk nemer toe aan de beschikbaar-werk nemers van de service |
> | Bewerking | Micro soft. DataMigration/Services/checkStatus/actie | Controleren of de service is geïmplementeerd en wordt uitgevoerd |
> | Bewerking | Micro soft. DataMigration/Services/configureWorker/actie | Hiermee wordt een DMS-werk nemer geconfigureerd voor de beschikbaar-werk nemers van de service |
> | Bewerking | Micro soft. DataMigration/Services/verwijderen | Hiermee worden een resource en alle onderliggende items verwijderd |
> | Bewerking | Micro soft. DataMigration/Services/projects/accessArtifacts/actie | Genereer een URL die kan worden gebruikt om project artefacten op te halen of in te stellen |
> | Bewerking | Micro soft. DataMigration/services/projecten/verwijderen | Hiermee worden een resource en alle onderliggende items verwijderd |
> | Bewerking | Micro soft. DataMigration/services/projecten/bestanden/verwijderen | Hiermee worden een resource en alle onderliggende items verwijderd |
> | Bewerking | Micro soft. DataMigration/services/projecten/bestanden/lezen | Informatie over resources lezen |
> | Bewerking | Micro soft. DataMigration/services/projecten/bestanden/lezen/actie | Een URL verkrijgen die kan worden gebruikt om de inhoud van het bestand te lezen |
> | Bewerking | Micro soft. DataMigration/services/projecten/bestanden/readWrite/actie | Een URL verkrijgen die kan worden gebruikt om de inhoud van het bestand te lezen of te schrijven |
> | Bewerking | Micro soft. DataMigration/services/projecten/bestanden/schrijven | Resources en hun eigenschappen maken of bijwerken |
> | Bewerking | Micro soft. DataMigration/services/projecten/lezen | Informatie over resources lezen |
> | Bewerking | Micro soft. DataMigration/services/projecten/taken/annuleren/actie | De taak annuleren als deze momenteel wordt uitgevoerd |
> | Bewerking | Micro soft. DataMigration/services/projecten/taken/verwijderen | Hiermee worden een resource en alle onderliggende items verwijderd |
> | Bewerking | Micro soft. DataMigration/services/projecten/taken/lezen | Informatie over resources lezen |
> | Bewerking | Micro soft. DataMigration/services/projecten/taken/schrijven | Taken Azure Database Migration Service taken uitvoeren |
> | Bewerking | Micro soft. DataMigration/services/projecten/schrijven | Taken Azure Database Migration Service taken uitvoeren |
> | Bewerking | Micro soft. DataMigration/services/lezen | Informatie over resources lezen |
> | Bewerking | Micro soft. DataMigration/Services/removeWorker/actie | Hiermee wordt een DMS-werk nemer verwijderd uit de beschikbaar-werk rollen van de service |
> | Bewerking | Micro soft. DataMigration/Services/serviceTasks/annuleren/actie | De taak annuleren als deze momenteel wordt uitgevoerd |
> | Bewerking | Micro soft. DataMigration/Services/serviceTasks/verwijderen | Hiermee worden een resource en alle onderliggende items verwijderd |
> | Bewerking | Micro soft. DataMigration/Services/serviceTasks/lezen | Informatie over resources lezen |
> | Bewerking | Micro soft. DataMigration/Services/serviceTasks/schrijven | Taken Azure Database Migration Service taken uitvoeren |
> | Bewerking | Micro soft. DataMigration/Services/sleuven/verwijderen | Hiermee worden een resource en alle onderliggende items verwijderd |
> | Bewerking | Micro soft. DataMigration/Services/sleuven/lezen | Informatie over resources lezen |
> | Bewerking | Micro soft. DataMigration/Services/sleuven/schrijven | Resources en hun eigenschappen maken of bijwerken |
> | Bewerking | Micro soft. DataMigration/services/start/actie | Start de DMS-service zodat de migratie opnieuw kan worden uitgevoerd |
> | Bewerking | Micro soft. DataMigration/services/stoppen/actie | De DMS-service stoppen om de kosten te minimaliseren |
> | Bewerking | Micro soft. DataMigration/Services/updateAgentConfig/actie | Hiermee wordt de configuratie van de DMS-agent bijgewerkt met de gegeven waarden. |
> | Bewerking | Micro soft. DataMigration/Services/schrijven | Resources en hun eigenschappen maken of bijwerken |
> | Bewerking | Micro soft. DataMigration/sku's/lezen | Een lijst met Sku's ophalen die worden ondersteund door DMS-resources. |

## <a name="microsoftdbformariadb"></a>Micro soft. DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DBforMariaDB/checkNameAvailability/actie | Controleer of de opgegeven server naam beschikbaar is voor het wereld wijd inrichten van een bepaald abonnement. |
> | Bewerking | Micro soft. DBforMariaDB/locaties/azureAsyncOperation/lezen | Resultaten van MariaDB-server bewerking retour neren |
> | Bewerking | Micro soft. DBforMariaDB/locaties/operationResults/lezen | Resultaten van MariaDB server-bewerking retour neren op basis van ResourceGroup |
> | Bewerking | Micro soft. DBforMariaDB/locaties/operationResults/lezen | Resultaten van MariaDB-server bewerking retour neren |
> | Bewerking | Micro soft. DBforMariaDB/locaties/performanceTiers/lezen | Hiermee wordt de lijst met prestatie lagen weer gegeven die beschikbaar zijn. |
> | Bewerking | Micro soft. DBforMariaDB/locaties/securityAlertPoliciesAzureAsyncOperation/lezen | Retour neer de lijst met resultaten van de server bedreigings detectie. |
> | Bewerking | Micro soft. DBforMariaDB/locaties/securityAlertPoliciesOperationResults/lezen | Retour neer de lijst met resultaten van de server bedreigings detectie. |
> | Bewerking | Micro soft. DBforMariaDB/Operations/lezen | Retour neer de lijst met MariaDB-bewerkingen. |
> | Bewerking | Micro soft. DBforMariaDB/performanceTiers/lezen | Hiermee wordt de lijst met prestatie lagen weer gegeven die beschikbaar zijn. |
> | Bewerking | Micro soft. DBforMariaDB/REGI ster/actie | MariaDB-resource provider registreren |
> | Bewerking | Micro soft. DBforMariaDB/servers/beheerders/verwijderen | Hiermee verwijdert u een bestaande beheerder van de MariaDB-server. |
> | Bewerking | Micro soft. DBforMariaDB/servers/beheerders/lezen | Hiermee haalt u een lijst met MariaDB-Server beheerders op. |
> | Bewerking | Micro soft. DBforMariaDB/servers/beheerders/schrijven | Hiermee wordt de MariaDB-Server beheerder met de opgegeven para meters gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. DBforMariaDB/servers/Advisors/createRecommendedActionSession/actie | Een nieuwe actie sessie voor een aanbeveling maken |
> | Bewerking | Micro soft. DBforMariaDB/servers/Advisors/lezen | De lijst met Advisors retour neren |
> | Bewerking | Micro soft. DBforMariaDB/servers/Advisors/lezen | Een adviseur retour neren |
> | Bewerking | Micro soft. DBforMariaDB/servers/Advisors/recommendedActions/lezen | De lijst met aanbevolen acties retour neren |
> | Bewerking | Micro soft. DBforMariaDB/servers/Advisors/recommendedActions/lezen | De lijst met aanbevolen acties retour neren |
> | Bewerking | Micro soft. DBforMariaDB/servers/Advisors/recommendedActions/lezen | Een aanbevolen actie retour neren |
> | Bewerking | Micro soft. DBforMariaDB/servers/configuraties/lezen | De lijst met configuraties voor een server retour neren of de eigenschappen voor de opgegeven configuratie ophalen. |
> | Bewerking | Micro soft. DBforMariaDB/servers/configuraties/schrijven | De waarde voor de opgegeven configuratie bijwerken |
> | Bewerking | Micro soft. DBforMariaDB/servers/data bases/verwijderen | Hiermee verwijdert u een bestaande MariaDB-data base. |
> | Bewerking | Micro soft. DBforMariaDB/servers/data bases/lezen | De lijst met MariaDB-data bases retour neren of de eigenschappen voor de opgegeven Data Base ophalen. |
> | Bewerking | Micro soft. DBforMariaDB/servers/data bases/schrijven | Hiermee maakt u een MariaDB-data base met de opgegeven para meters of werkt u de eigenschappen voor de opgegeven Data Base bij. |
> | Bewerking | Micro soft. DBforMariaDB/servers/verwijderen | Hiermee verwijdert u een bestaande server. |
> | Bewerking | Micro soft. DBforMariaDB/servers/firewallRules/verwijderen | Hiermee verwijdert u een bestaande firewall regel. |
> | Bewerking | Micro soft. DBforMariaDB/servers/firewallRules/lezen | De lijst met firewall regels voor een server retour neren of de eigenschappen voor de opgegeven firewall regel ophalen. |
> | Bewerking | Micro soft. DBforMariaDB/servers/firewallRules/schrijven | Hiermee maakt u een firewall regel met de opgegeven para meters of werkt u een bestaande regel bij. |
> | Bewerking | Micro soft. DBforMariaDB/servers/logboeken/lezen | De lijst met MariaDB-logboeken weer geven. |
> | Bewerking | Micro soft. DBforMariaDB/servers/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de disagnostic-instelling voor de resource opgehaald |
> | Bewerking | Micro soft. DBforMariaDB/servers/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. DBforMariaDB/servers/providers/micro soft. Insights/logDefinitions/lezen | Hiermee worden de beschik bare logboeken voor MariaDB-servers opgehaald |
> | Bewerking | Micro soft. DBforMariaDB/servers/providers/micro soft. Insights/metricDefinitions/lezen | Typen metrische gegevens die beschikbaar zijn voor data bases retour neren |
> | Bewerking | Micro soft. DBforMariaDB/servers/queryTexts/actie | De tekst voor een lijst met query's retour neren |
> | Bewerking | Micro soft. DBforMariaDB/servers/queryTexts/actie | De tekst van een query retour neren |
> | Bewerking | Micro soft. DBforMariaDB/servers/lezen | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Bewerking | Micro soft. DBforMariaDB/servers/recoverableServers/lezen | De herstel bare MariaDB-Server gegevens retour neren |
> | Bewerking | Micro soft. DBforMariaDB/servers/replica's/lezen | Replica's van een MariaDB-server ophalen |
> | Bewerking | Micro soft. DBforMariaDB/servers/opnieuw opstarten/actie | Hiermee wordt een specifieke server opnieuw gestart. |
> | Bewerking | Micro soft. DBforMariaDB/servers/securityAlertPolicies/lezen | Details ophalen van het server Threat Detection-beleid dat op een bepaalde server is geconfigureerd |
> | Bewerking | Micro soft. DBforMariaDB/servers/securityAlertPolicies/schrijven | Het server Threat Detection-beleid voor een bepaalde server wijzigen |
> | Bewerking | Micro soft. DBforMariaDB/servers/topQueryStatistics/lezen | De lijst met query statistieken voor de meest voorkomende query's retour neren. |
> | Bewerking | Micro soft. DBforMariaDB/servers/topQueryStatistics/lezen | Een query statistieken retour neren |
> | Bewerking | Micro soft. DBforMariaDB/servers/updateConfigurations/actie | Update configuraties voor de opgegeven server |
> | Bewerking | Micro soft. DBforMariaDB/servers/virtualNetworkRules/verwijderen | Hiermee verwijdert u een bestaande Virtual Network regel |
> | Bewerking | Micro soft. DBforMariaDB/servers/virtualNetworkRules/lezen | De lijst met regels voor het virtuele netwerk retour neren of de eigenschappen voor de opgegeven virtuele netwerk regel ophalen. |
> | Bewerking | Micro soft. DBforMariaDB/servers/virtualNetworkRules/schrijven | Hiermee maakt u een regel voor het virtuele netwerk met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven virtuele-netwerk regel bij. |
> | Bewerking | Micro soft. DBforMariaDB/servers/waitStatistics/lezen | Wacht statistieken voor een exemplaar retour neren |
> | Bewerking | Micro soft. DBforMariaDB/servers/waitStatistics/lezen | Een wachtende statistiek retour neren |
> | Bewerking | Micro soft. DBforMariaDB/servers/schrijven | Hiermee maakt u een server met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server bij. |

## <a name="microsoftdbformysql"></a>Micro soft. DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DBforMySQL/checkNameAvailability/actie | Controleer of de opgegeven server naam beschikbaar is voor het wereld wijd inrichten van een bepaald abonnement. |
> | Bewerking | Micro soft. DBforMySQL/locaties/azureAsyncOperation/lezen | Resultaten van MySQL-server bewerking retour neren |
> | Bewerking | Micro soft. DBforMySQL/locaties/operationResults/lezen | Resultaten van de MySQL-server bewerking retour neren op basis van ResourceGroup |
> | Bewerking | Micro soft. DBforMySQL/locaties/operationResults/lezen | Resultaten van MySQL-server bewerking retour neren |
> | Bewerking | Micro soft. DBforMySQL/locaties/performanceTiers/lezen | Hiermee wordt de lijst met prestatie lagen weer gegeven die beschikbaar zijn. |
> | Bewerking | Micro soft. DBforMySQL/locaties/securityAlertPoliciesAzureAsyncOperation/lezen | Retour neer de lijst met resultaten van de server bedreigings detectie. |
> | Bewerking | Micro soft. DBforMySQL/locaties/securityAlertPoliciesOperationResults/lezen | Retour neer de lijst met resultaten van de server bedreigings detectie. |
> | Bewerking | Micro soft. DBforMySQL/Operations/lezen | De lijst met MySQL-bewerkingen retour neren. |
> | Bewerking | Micro soft. DBforMySQL/performanceTiers/lezen | Hiermee wordt de lijst met prestatie lagen weer gegeven die beschikbaar zijn. |
> | Bewerking | Micro soft. DBforMySQL/REGI ster/actie | MySQL-resource provider registreren |
> | Bewerking | Micro soft. DBforMySQL/servers/beheerders/verwijderen | Hiermee verwijdert u een bestaande beheerder van de MySQL-server. |
> | Bewerking | Micro soft. DBforMySQL/servers/beheerders/lezen | Hiermee haalt u een lijst met MySQL-Server beheerders op. |
> | Bewerking | Micro soft. DBforMySQL/servers/beheerders/schrijven | Hiermee wordt de MySQL-Server beheerder met de opgegeven para meters gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. DBforMySQL/servers/Advisors/createRecommendedActionSession/actie | Een nieuwe actie sessie voor een aanbeveling maken |
> | Bewerking | Micro soft. DBforMySQL/servers/Advisors/lezen | De lijst met Advisors retour neren |
> | Bewerking | Micro soft. DBforMySQL/servers/Advisors/lezen | Een adviseur retour neren |
> | Bewerking | Micro soft. DBforMySQL/servers/Advisors/recommendedActions/lezen | De lijst met aanbevolen acties retour neren |
> | Bewerking | Micro soft. DBforMySQL/servers/Advisors/recommendedActions/lezen | De lijst met aanbevolen acties retour neren |
> | Bewerking | Micro soft. DBforMySQL/servers/Advisors/recommendedActions/lezen | Een aanbevolen actie retour neren |
> | Bewerking | Micro soft. DBforMySQL/servers/configuraties/lezen | De lijst met configuraties voor een server retour neren of de eigenschappen voor de opgegeven configuratie ophalen. |
> | Bewerking | Micro soft. DBforMySQL/servers/configuraties/schrijven | De waarde voor de opgegeven configuratie bijwerken |
> | Bewerking | Micro soft. DBforMySQL/servers/data bases/verwijderen | Hiermee verwijdert u een bestaande MySQL-data base. |
> | Bewerking | Micro soft. DBforMySQL/servers/data bases/lezen | De lijst met MySQL-data bases retour neren of de eigenschappen voor de opgegeven Data Base ophalen. |
> | Bewerking | Micro soft. DBforMySQL/servers/data bases/schrijven | Hiermee maakt u een MySQL-data base met de opgegeven para meters of werkt u de eigenschappen voor de opgegeven Data Base bij. |
> | Bewerking | Micro soft. DBforMySQL/servers/verwijderen | Hiermee verwijdert u een bestaande server. |
> | Bewerking | Micro soft. DBforMySQL/servers/firewallRules/verwijderen | Hiermee verwijdert u een bestaande firewall regel. |
> | Bewerking | Micro soft. DBforMySQL/servers/firewallRules/lezen | De lijst met firewall regels voor een server retour neren of de eigenschappen voor de opgegeven firewall regel ophalen. |
> | Bewerking | Micro soft. DBforMySQL/servers/firewallRules/schrijven | Hiermee maakt u een firewall regel met de opgegeven para meters of werkt u een bestaande regel bij. |
> | Bewerking | Micro soft. DBforMySQL/servers/logboeken/lezen | De lijst met PostgreSQL-logboeken weer geven. |
> | Bewerking | Micro soft. DBforMySQL/servers/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de disagnostic-instelling voor de resource opgehaald |
> | Bewerking | Micro soft. DBforMySQL/servers/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. DBforMySQL/servers/providers/micro soft. Insights/logDefinitions/lezen | Hiermee worden de beschik bare logboeken voor MySQL-servers opgehaald |
> | Bewerking | Micro soft. DBforMySQL/servers/providers/micro soft. Insights/metricDefinitions/lezen | Typen metrische gegevens die beschikbaar zijn voor data bases retour neren |
> | Bewerking | Micro soft. DBforMySQL/servers/queryTexts/actie | De tekst voor een lijst met query's retour neren |
> | Bewerking | Micro soft. DBforMySQL/servers/queryTexts/actie | De tekst van een query retour neren |
> | Bewerking | Micro soft. DBforMySQL/servers/lezen | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Bewerking | Micro soft. DBforMySQL/servers/recoverableServers/lezen | De gegevens van de herstel bare MySQL-server retour neren |
> | Bewerking | Micro soft. DBforMySQL/servers/replica's/lezen | Replica's van een MySQL-server ophalen |
> | Bewerking | Micro soft. DBforMySQL/servers/opnieuw opstarten/actie | Hiermee wordt een specifieke server opnieuw gestart. |
> | Bewerking | Micro soft. DBforMySQL/servers/securityAlertPolicies/lezen | Details ophalen van het server Threat Detection-beleid dat op een bepaalde server is geconfigureerd |
> | Bewerking | Micro soft. DBforMySQL/servers/securityAlertPolicies/schrijven | Het server Threat Detection-beleid voor een bepaalde server wijzigen |
> | Bewerking | Micro soft. DBforMySQL/servers/topQueryStatistics/lezen | De lijst met query statistieken voor de meest voorkomende query's retour neren. |
> | Bewerking | Micro soft. DBforMySQL/servers/topQueryStatistics/lezen | Een query statistieken retour neren |
> | Bewerking | Micro soft. DBforMySQL/servers/updateConfigurations/actie | Update configuraties voor de opgegeven server |
> | Bewerking | Micro soft. DBforMySQL/servers/virtualNetworkRules/verwijderen | Hiermee verwijdert u een bestaande Virtual Network regel |
> | Bewerking | Micro soft. DBforMySQL/servers/virtualNetworkRules/lezen | De lijst met regels voor het virtuele netwerk retour neren of de eigenschappen voor de opgegeven virtuele netwerk regel ophalen. |
> | Bewerking | Micro soft. DBforMySQL/servers/virtualNetworkRules/schrijven | Hiermee maakt u een regel voor het virtuele netwerk met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven virtuele-netwerk regel bij. |
> | Bewerking | Micro soft. DBforMySQL/servers/waitStatistics/lezen | Wacht statistieken voor een exemplaar retour neren |
> | Bewerking | Micro soft. DBforMySQL/servers/waitStatistics/lezen | Een wachtende statistiek retour neren |
> | Bewerking | Micro soft. DBforMySQL/servers/schrijven | Hiermee maakt u een server met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server bij. |

## <a name="microsoftdbforpostgresql"></a>Micro soft. DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DBforPostgreSQL/checkNameAvailability/actie | Controleer of de opgegeven server naam beschikbaar is voor het wereld wijd inrichten van een bepaald abonnement. |
> | Bewerking | Micro soft. DBforPostgreSQL/locaties/azureAsyncOperation/lezen | Resultaten van PostgreSQL-server bewerking retour neren |
> | Bewerking | Micro soft. DBforPostgreSQL/locaties/operationResults/lezen | Resultaten van PostgreSQL server-bewerking retour neren op basis van ResourceGroup |
> | Bewerking | Micro soft. DBforPostgreSQL/locaties/operationResults/lezen | Resultaten van PostgreSQL-server bewerking retour neren |
> | Bewerking | Micro soft. DBforPostgreSQL/locaties/performanceTiers/lezen | Hiermee wordt de lijst met prestatie lagen weer gegeven die beschikbaar zijn. |
> | Bewerking | Micro soft. DBforPostgreSQL/locaties/privateEndpointConnectionAzureAsyncOperation/lezen | Hiermee wordt het resultaat voor een verbindings bewerking voor een privé-eind punt opgehaald |
> | Bewerking | Micro soft. DBforPostgreSQL/locaties/privateEndpointConnectionOperationResults/lezen | Hiermee wordt het resultaat voor een verbindings bewerking voor een privé-eind punt opgehaald |
> | Bewerking | Micro soft. DBforPostgreSQL/locaties/privateEndpointConnectionProxyAzureAsyncOperation/lezen | Hiermee wordt het resultaat opgehaald voor een proxy bewerking voor een privé-eindpunt verbinding |
> | Bewerking | Micro soft. DBforPostgreSQL/locaties/privateEndpointConnectionProxyOperationResults/lezen | Hiermee wordt het resultaat opgehaald voor een proxy bewerking voor een privé-eindpunt verbinding |
> | Bewerking | Micro soft. DBforPostgreSQL/locaties/securityAlertPoliciesAzureAsyncOperation/lezen | Retour neer de lijst met resultaten van de server bedreigings detectie. |
> | Bewerking | Micro soft. DBforPostgreSQL/locaties/securityAlertPoliciesOperationResults/lezen | Retour neer de lijst met resultaten van de server bedreigings detectie. |
> | Bewerking | Micro soft. DBforPostgreSQL/Operations/lezen | Retour neer de lijst met PostgreSQL-bewerkingen. |
> | Bewerking | Micro soft. DBforPostgreSQL/performanceTiers/lezen | Hiermee wordt de lijst met prestatie lagen weer gegeven die beschikbaar zijn. |
> | Bewerking | Micro soft. DBforPostgreSQL/REGI ster/actie | PostgreSQL-resource provider registreren |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/beheerders/verwijderen | Hiermee verwijdert u een bestaande beheerder van de PostgreSQL-server. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/beheerders/lezen | Hiermee haalt u een lijst met PostgreSQL-Server beheerders op. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/beheerders/schrijven | Hiermee wordt de PostgreSQL-Server beheerder met de opgegeven para meters gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/Advisors/lezen | De lijst met Advisors retour neren |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/Advisors/recommendedActions/lezen | De lijst met aanbevolen acties retour neren |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/Advisors/recommendedActionSessions/actie | Aanbevelingen doen |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/configuraties/lezen | De lijst met configuraties voor een server retour neren of de eigenschappen voor de opgegeven configuratie ophalen. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/configuraties/schrijven | De waarde voor de opgegeven configuratie bijwerken |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/data bases/verwijderen | Hiermee verwijdert u een bestaande PostgreSQL-data base. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/data bases/lezen | De lijst met PostgreSQL-data bases retour neren of de eigenschappen voor de opgegeven Data Base ophalen. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/data bases/schrijven | Hiermee maakt u een PostgreSQL-data base met de opgegeven para meters of werkt u de eigenschappen voor de opgegeven Data Base bij. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/verwijderen | Hiermee verwijdert u een bestaande server. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/firewallRules/verwijderen | Hiermee verwijdert u een bestaande firewall regel. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/firewallRules/lezen | De lijst met firewall regels voor een server retour neren of de eigenschappen voor de opgegeven firewall regel ophalen. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/firewallRules/schrijven | Hiermee maakt u een firewall regel met de opgegeven para meters of werkt u een bestaande regel bij. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/logboeken/lezen | De lijst met PostgreSQL-logboeken weer geven. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/verwijderen | Hiermee verwijdert u een bestaande verbindings proxy voor een persoonlijk eind punt |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/lezen | Hiermee wordt de lijst met particuliere endpoint-verbindings proxy's geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbinding proxy voor het particuliere eind punt. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/Action | Hiermee wordt een aanroep voor het maken van een privé-eind punt gevalideerd aan de kant van de NRP |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/schrijven | Hiermee maakt u een particuliere endpoint-verbindings proxy met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven verbinding proxy van het particuliere eind punt bij. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/privateEndpointConnections/verwijderen | Hiermee verwijdert u een bestaande persoonlijke eindpunt verbinding |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/privateEndpointConnections/lezen | Hiermee wordt de lijst met privé-eindpunt verbindingen geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbinding met een privé-eind punt. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/privateEndpointConnections/schrijven | Hiermee wordt een bestaande verbinding met een privé-eind punt goedgekeurd of geweigerd |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/privateLinkResources/lezen | De persoonlijke koppelings resources voor de bijbehorende PostgreSQL-server ophalen |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de disagnostic-instelling voor de resource opgehaald |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/providers/micro soft. Insights/logDefinitions/lezen | Hiermee worden de beschik bare logboeken voor post gres-servers opgehaald |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/providers/micro soft. Insights/metricDefinitions/lezen | Typen metrische gegevens die beschikbaar zijn voor data bases retour neren |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/queryTexts/actie | De tekst van een query retour neren |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/queryTexts/lezen | De tekst voor een lijst met query's retour neren |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/lezen | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/recoverableServers/lezen | De herstel bare PostgreSQL-Server gegevens retour neren |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/replica's/lezen | Replica's van een PostgreSQL-server ophalen |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/opnieuw opstarten/actie | Hiermee wordt een specifieke server opnieuw gestart. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/securityAlertPolicies/lezen | Details ophalen van het server Threat Detection-beleid dat op een bepaalde server is geconfigureerd |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/securityAlertPolicies/schrijven | Het server Threat Detection-beleid voor een bepaalde server wijzigen |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/topQueryStatistics/lezen | De lijst met query statistieken voor de meest voorkomende query's retour neren. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/updateConfigurations/actie | Update configuraties voor de opgegeven server |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/virtualNetworkRules/verwijderen | Hiermee verwijdert u een bestaande Virtual Network regel |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/virtualNetworkRules/lezen | De lijst met regels voor het virtuele netwerk retour neren of de eigenschappen voor de opgegeven virtuele netwerk regel ophalen. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/virtualNetworkRules/schrijven | Hiermee maakt u een regel voor het virtuele netwerk met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven virtuele-netwerk regel bij. |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/waitStatistics/lezen | Wacht statistieken voor een exemplaar retour neren |
> | Bewerking | Micro soft. DBforPostgreSQL/servers/schrijven | Hiermee maakt u een server met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server bij. |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/configuraties/lezen | De lijst met configuraties voor een server retour neren of de eigenschappen voor de opgegeven configuratie ophalen. |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/configuraties/schrijven | De waarde voor de opgegeven configuratie bijwerken |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/verwijderen | Hiermee verwijdert u een bestaande server. |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/firewallRules/verwijderen | Hiermee verwijdert u een bestaande firewall regel. |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/firewallRules/lezen | De lijst met firewall regels voor een server retour neren of de eigenschappen voor de opgegeven firewall regel ophalen. |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/firewallRules/write | Hiermee maakt u een firewall regel met de opgegeven para meters of werkt u een bestaande regel bij. |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de disagnostic-instelling voor de resource opgehaald |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/providers/micro soft. Insights/logDefinitions/lezen | Hiermee worden de beschik bare logboeken voor post gres-servers opgehaald |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/providers/micro soft. Insights/metricDefinitions/lezen | Typen metrische gegevens die beschikbaar zijn voor data bases retour neren |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/lezen | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/updateConfigurations/Action | Update configuraties voor de opgegeven server |
> | Bewerking | Micro soft. DBforPostgreSQL/serversv2/schrijven | Hiermee maakt u een server met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server bij. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. apparaten/accounts/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Bewerking | Micro soft. apparaten/accounts/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. apparaten/accounts/logDefinitions/lezen | Hiermee worden de beschik bare logboek definities voor de IotHub-service opgehaald |
> | Bewerking | Micro soft. apparaten/accounts/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor de IotHub-service |
> | Bewerking | Micro soft. apparaten/checkNameAvailability/actie | Controleren of de naam van de IotHub beschikbaar is |
> | Bewerking | Micro soft. apparaten/checkNameAvailability/actie | Controleren of de naam van de IotHub beschikbaar is |
> | Bewerking | Micro soft. apparaten/checkProvisioningServiceNameAvailability/actie | Controleer of de naam van de inrichtings service beschikbaar is |
> | Bewerking | Micro soft. apparaten/checkProvisioningServiceNameAvailability/actie | Controleer of de naam van de inrichtings service beschikbaar is |
> | Bewerking | Micro soft. devices/digitalTwins/verwijderen | Een bestaand Digital Apparaatdubbels-account en alle onderliggende items verwijderen |
> | Bewerking | Micro soft. devices/digitalTwins/operationresults/lezen | De status van een bewerking ophalen voor een Digital Apparaatdubbels-account |
> | Bewerking | Micro soft. devices/digitalTwins/lezen | Hiermee wordt een lijst opgehaald met de Digital Apparaatdubbels-accounts die zijn gekoppeld aan een abonnement |
> | Bewerking | Micro soft. devices/digitalTwins/sku's/lezen | Een lijst met geldige Sku's voor Digital Apparaatdubbels-accounts ophalen |
> | Bewerking | Micro soft. devices/digitalTwins/schrijven | Een nieuw Apparaatdubbels-account maken |
> | Bewerking | Micro soft. devices/ElasticPools/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Bewerking | Micro soft. devices/ElasticPools/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. apparaten/elasticPools/iotHubTenants/certificaten/verwijderen | Certificaat verwijderen |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Verificatie code genereren |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/certificaten/lezen | Hiermee wordt het certificaat opgehaald |
> | Bewerking | Micro soft. apparaten/elasticPools/iotHubTenants/certificaten/controleren/actie | Certificaat bron verifiëren |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/certificates/write | Certificaat maken of bijwerken |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/verwijderen | De IotHub-Tenant resource verwijderen |
> | Bewerking | Micro soft. devices/ElasticPools/IotHubTenants/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Bewerking | Micro soft. devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/verwijderen | EventHub Consumer Group verwijderen |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/lezen | EventHub Consumer-groep (en) ophalen |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/schrijven | EventHub-Consumer groep maken |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/exportDevices/Action | Apparaten exporteren |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/getStats/lezen | Hiermee wordt de resource voor IotHub Tenant statistieken opgehaald |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/importDevices/Action | Apparaten importeren |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/iotHubKeys/listkeys ophalen/Action | Hiermee wordt de IotHub-Tenant sleutel opgehaald |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/Jobs/lezen | Verzonden details van de taak (s) die op de opgegeven IotHub zijn ingediend |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/Listkeys ophalen/Action | Hiermee worden IotHub-Tenant sleutels opgehaald |
> | Bewerking | Micro soft. devices/ElasticPools/IotHubTenants/logDefinitions/lezen | Hiermee worden de beschik bare logboek definities voor de IotHub-service opgehaald |
> | Bewerking | Micro soft. devices/ElasticPools/IotHubTenants/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor de IotHub-service |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/quotaMetrics/lezen | Quotum metrieken ophalen |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/lezen | Hiermee wordt de IotHub-Tenant resource opgehaald |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/Routing/routes/$testall/Action | Een bericht voor alle bestaande routes testen |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/Routing/routes/$testnew/Action | Een bericht testen op basis van een gegeven test route |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/routingEndpointsHealth/lezen | Hiermee wordt de status van alle routerings eindpunten voor een IotHub opgehaald |
> | Bewerking | Micro soft. devices/elasticPools/iotHubTenants/schrijven | De IotHub-Tenant resource maken of bijwerken |
> | Bewerking | Micro soft. devices/ElasticPools/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor de IotHub-service |
> | Bewerking | Micro soft. apparaten/iotHubs/certificaten/verwijderen | Certificaat verwijderen |
> | Bewerking | Micro soft. devices/iotHubs/certificates/generateVerificationCode/Action | Verificatie code genereren |
> | Bewerking | Micro soft. apparaten/iotHubs/certificaten/lezen | Hiermee wordt het certificaat opgehaald |
> | Bewerking | Micro soft. apparaten/iotHubs/certificaten/controleren/actie | Certificaat bron verifiëren |
> | Bewerking | Micro soft. apparaten/iotHubs/certificaten/schrijven | Certificaat maken of bijwerken |
> | Bewerking | Micro soft. devices/iotHubs/verwijderen | IotHub-resource verwijderen |
> | Bewerking | Micro soft. devices/IotHubs/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Bewerking | Micro soft. devices/IotHubs/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. devices/iotHubs/eventGridFilters/verwijderen | Hiermee wordt het Event Grid filter verwijderd |
> | Bewerking | Micro soft. devices/iotHubs/eventGridFilters/lezen | Hiermee wordt het Event Grid filter opgehaald |
> | Bewerking | Micro soft. devices/iotHubs/eventGridFilters/schrijven | Een nieuw Event Grid filter maken of een bestaand item bijwerken |
> | Bewerking | Micro soft. apparaten/iotHubs/eventHubEndpoints/consumerGroups/verwijderen | EventHub Consumer Group verwijderen |
> | Bewerking | Micro soft. devices/iotHubs/eventHubEndpoints/consumerGroups/lezen | EventHub Consumer-groep (en) ophalen |
> | Bewerking | Micro soft. devices/iotHubs/eventHubEndpoints/consumerGroups/write | EventHub-Consumer groep maken |
> | Bewerking | Micro soft. devices/iotHubs/exportDevices/Action | Apparaten exporteren |
> | Bewerking | Micro soft. devices/iotHubs/importDevices/Action | Apparaten importeren |
> | Bewerking | Micro soft. devices/iotHubs/iotHubKeys/listkeys ophalen/Action | De IotHub-sleutel voor de opgegeven naam ophalen |
> | Bewerking | Micro soft. devices/iotHubs/iotHubStats/lezen | IotHub-statistieken ophalen |
> | Bewerking | Micro soft. apparaten/iotHubs/Jobs/lezen | Verzonden details van de taak (s) die op de opgegeven IotHub zijn ingediend |
> | Bewerking | Micro soft. devices/iotHubs/listkeys ophalen/Action | Alle IotHub-sleutels ophalen |
> | Bewerking | Micro soft. devices/IotHubs/logDefinitions/lezen | Hiermee worden de beschik bare logboek definities voor de IotHub-service opgehaald |
> | Bewerking | Micro soft. devices/IotHubs/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor de IotHub-service |
> | Bewerking | Micro soft. devices/iotHubs/operationresults/lezen | Resultaat van bewerking ophalen (verouderde API) |
> | Bewerking | Micro soft. devices/iotHubs/quotaMetrics/lezen | Quotum metrieken ophalen |
> | Bewerking | Micro soft. devices/iotHubs/lezen | Hiermee worden de IotHub-resource (s) opgehaald |
> | Bewerking | Micro soft. devices/iotHubs/route ring/$testall/Action | Een bericht voor alle bestaande routes testen |
> | Bewerking | Micro soft. devices/iotHubs/route ring/$testnew/Action | Een bericht testen op basis van een gegeven test route |
> | Bewerking | Micro soft. devices/iotHubs/routingEndpointsHealth/lezen | Hiermee wordt de status van alle routerings eindpunten voor een IotHub opgehaald |
> | Bewerking | Micro soft. devices/iotHubs/sku's/lezen | Geldige IotHub-Sku's ophalen |
> | Bewerking | Micro soft. devices/iotHubs/schrijven | IotHub-resource maken of bijwerken |
> | Bewerking | Micro soft. apparaten/locaties/operationresults/lezen | Resultaat van op locatie gebaseerde bewerking ophalen |
> | Bewerking | Micro soft. devices/operationresults/lezen | Resultaat van bewerking ophalen |
> | Bewerking | Micro soft. apparaten/bewerkingen/lezen | Alle resource provider-bewerkingen ophalen |
> | Bewerking | Micro soft. apparaten/provisioningServices/certificaten/verwijderen | Certificaat verwijderen |
> | Bewerking | Micro soft. devices/provisioningServices/certificates/generateVerificationCode/Action | Verificatie code genereren |
> | Bewerking | Micro soft. apparaten/provisioningServices/certificaten/lezen | Hiermee wordt het certificaat opgehaald |
> | Bewerking | Micro soft. apparaten/provisioningServices/certificaten/controleren/actie | Certificaat bron verifiëren |
> | Bewerking | Micro soft. apparaten/provisioningServices/certificaten/schrijven | Certificaat maken of bijwerken |
> | Bewerking | Micro soft. devices/provisioningServices/verwijderen | IotDps-resource verwijderen |
> | Bewerking | Micro soft. devices/provisioningServices/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Bewerking | Micro soft. devices/provisioningServices/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. apparaten/provisioningServices/sleutels/listkeys ophalen/actie | IotDps-sleutels voor sleutel naam ophalen |
> | Bewerking | Micro soft. devices/provisioningServices/listkeys ophalen/Action | Alle IotDps-sleutels ophalen |
> | Bewerking | Micro soft. devices/provisioningServices/logDefinitions/lezen | Hiermee worden de beschik bare logboek definities voor de inrichtings service opgehaald |
> | Bewerking | Micro soft. devices/provisioningServices/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor de inrichtings service |
> | Bewerking | Micro soft. devices/provisioningServices/operationresults/lezen | Resultaat van DPS-bewerking ophalen |
> | Bewerking | Micro soft. devices/provisioningServices/lezen | IotDps-resource ophalen |
> | Bewerking | Micro soft. devices/provisioningServices/sku's/lezen | Geldige IotDps-Sku's ophalen |
> | Bewerking | Micro soft. devices/provisioningServices/schrijven | IotDps-resource maken |
> | Bewerking | Micro soft. apparaten/registreren/actie | Het abonnement voor de IotHub-resource provider registreren en het maken van IotHub-resources inschakelen |
> | Bewerking | Micro soft. apparaten/gebruik/lezen | Details van het abonnements gebruik voor deze provider ophalen. |

## <a name="microsoftdevspaces"></a>Micro soft. DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DevSpaces/controllers/verwijderen | Azure dev Spaces-controller en dataplane-Services verwijderen |
> | Bewerking | Micro soft. DevSpaces/controllers/listConnectionDetails/actie | Verbindings details weer geven voor de infra structuur van de Azure dev Space-controller |
> | Bewerking | Micro soft. DevSpaces/controllers/lezen | Eigenschappen van de controller van Azure dev Spaces lezen |
> | Bewerking | Micro soft. DevSpaces/controllers/schrijven | Eigenschappen van een Azure dev Space-controller maken of bijwerken |
> | Bewerking | Micro soft. DevSpaces/locaties/checkContainerHostMapping/actie | Bestaande controller toewijzing voor een container host controleren |
> | Bewerking | Micro soft. DevSpaces/locaties/operationresults/lezen | Status lezen voor een asynchrone bewerking |
> | Bewerking | Micro soft. DevSpaces/REGI ster/actie | Micro soft dev Spaces resource provider registreren bij een abonnement |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DevTestLab/labCenters/verwijderen | Lab Centers verwijderen. |
> | Bewerking | Micro soft. DevTestLab/labCenters/lezen | Lab Centers lezen. |
> | Bewerking | Micro soft. DevTestLab/labCenters/schrijven | Lab Centers toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/artifactSources/armTemplates/lezen | Lees Azure Resource Manager-sjablonen. |
> | Bewerking | Micro soft. DevTestLab/Labs/artifactSources/artefacten/GenerateArmTemplate/actie | Hiermee wordt een ARM-sjabloon gegenereerd voor het opgegeven artefact, worden de vereiste bestanden geüpload naar een opslag account en wordt het gegenereerde artefact gevalideerd. |
> | Bewerking | Micro soft. DevTestLab/Labs/artifactSources/artefacten/lezen | Artefacten lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/artifactSources/verwijderen | Artefact bronnen verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/artifactSources/lezen | Artefact bronnen lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/artifactSources/schrijven | Artefact bronnen toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/ClaimAnyVm/actie | Claim een wille keurig claim bare virtuele machine in het lab. |
> | Bewerking | Micro soft. DevTestLab/Labs/kosten/lezen | Lees kosten. |
> | Bewerking | Micro soft. DevTestLab/Labs/kosten/schrijven | Kosten toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/CreateEnvironment/actie | Maak virtuele machines in een lab. |
> | Bewerking | Micro soft. DevTestLab/Labs/customImages/verwijderen | Aangepaste installatie kopieën verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/customImages/lezen | Aangepaste installatie kopieën lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/customImages/schrijven | Aangepaste installatie kopieën toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/verwijderen | Labs verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/EnsureCurrentUserProfile/actie | Zorg ervoor dat de huidige gebruiker een geldig profiel in het lab heeft. |
> | Bewerking | Micro soft. DevTestLab/Labs/ExportResourceUsage/actie | Hiermee exporteert u het resource gebruik van de test omgeving naar een opslag account |
> | Bewerking | Micro soft. DevTestLab/Labs/formules/verwijderen | Formules verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/formules/lezen | Formules lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/formules/schrijven | Formules toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/galleryImages/lezen | Galerie afbeeldingen lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/GenerateUploadUri/actie | Genereer een URI voor het uploaden van aangepaste schijf installatie kopieën naar een lab. |
> | Bewerking | Micro soft. DevTestLab/Labs/ImportVirtualMachine/actie | Een virtuele machine importeren in een ander lab. |
> | Bewerking | Micro soft. DevTestLab/Labs/ListVhds/actie | Schijf installatie kopieën weer geven die beschikbaar zijn voor het maken van aangepaste installatie kopieën. |
> | Bewerking | Micro soft. DevTestLab/Labs/notificationChannels/verwijderen | Meldings kanalen verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/notificationChannels/notify/Action | Een melding naar het meegeleverde kanaal verzenden. |
> | Bewerking | Micro soft. DevTestLab/Labs/notificationChannels/lezen | Lees meldings kanalen. |
> | Bewerking | Micro soft. DevTestLab/Labs/notificationChannels/schrijven | Meldings kanalen toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/policySets/EvaluatePolicies/actie | Evalueert het lab-beleid. |
> | Bewerking | Micro soft. DevTestLab/Labs/policySets/policies/verwijderen | Beleids regels verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/policySets/polices/lezen | Beleid lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/policySets/beleid/schrijven | Beleids regels toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/policySets/lezen | Beleids sets lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/lezen | Lees Labs. |
> | Bewerking | Micro soft. DevTestLab/Labs/planning/verwijderen | Schema's verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/planningen/uitvoeren/actie | Een schema uitvoeren. |
> | Bewerking | Micro soft. DevTestLab/Labs/planningen/ListApplicable/actie | Een lijst met alle toepasselijke schema's |
> | Bewerking | Micro soft. DevTestLab/Labs/planningen/lezen | Planningen lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/planningen/schrijven | Schema's toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/serviceRunners/verwijderen | Verwijder uitlopers van de service. |
> | Bewerking | Micro soft. DevTestLab/Labs/serviceRunners/lezen | Lees service-uitlopers. |
> | Bewerking | Micro soft. DevTestLab/Labs/serviceRunners/schrijven | Het toevoegen of wijzigen van service lopers. |
> | Bewerking | Micro soft. DevTestLab/Labs/sharedGalleries/verwijderen | Gedeelde galerieën verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/sharedGalleries/lezen | Gedeelde galerieën lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/sharedGalleries/sharedImages/verwijderen | Gedeelde installatie kopieën verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/sharedGalleries/sharedImages/lezen | Gedeelde installatie kopieën lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/sharedGalleries/sharedImages/schrijven | Gedeelde installatie kopieën toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/sharedGalleries/schrijven | Gedeelde galerieën toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/verwijderen | Gebruikers profielen verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/schijven/koppelen/actie | Koppel en maak de lease van de schijf aan de virtuele machine. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/schijven/verwijderen | Schijven verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/schijven/ontkoppelen/actie | Ontkoppel en Verbreek de lease van de schijf die aan de virtuele machine is gekoppeld. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/schijven/lezen | Lees schijven. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/schijven/schrijven | Schijven toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/omgevingen/verwijderen | Omgevingen verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/omgevingen/lezen | Omgevingen lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/omgevingen/schrijven | Omgevingen toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/lezen | Gebruikers profielen lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/geheimen/verwijderen | Verwijder geheimen. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/geheimen/lezen | Lees geheimen. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/geheimen/schrijven | Geheimen toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/users/serviceFabrics/verwijderen | Service fabrics verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/users/serviceFabrics/ListApplicableSchedules/actie | Hier worden de toepasselijke start-en stop schema's vermeld, indien van toepassing. |
> | Bewerking | Micro soft. DevTestLab/Labs/users/serviceFabrics/lezen | Lees service fabrics. |
> | Bewerking | Micro soft. DevTestLab/Labs/users/serviceFabrics/planning/verwijderen | Schema's verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/users/serviceFabrics/planningen/uitvoeren/actie | Een schema uitvoeren. |
> | Bewerking | Micro soft. DevTestLab/Labs/users/serviceFabrics/schedules/lezen | Planningen lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/serviceFabrics/planningen/schrijven | Schema's toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/users/serviceFabrics/start/actie | Start een service Fabric. |
> | Bewerking | Micro soft. DevTestLab/Labs/users/serviceFabrics/stop/actie | Een service Fabric stoppen |
> | Bewerking | Micro soft. DevTestLab/Labs/users/serviceFabrics/schrijven | Service fabrics toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/gebruikers/schrijven | Gebruikers profielen toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/AddDataDisk/actie | Een nieuwe of bestaande gegevens schijf koppelen aan een virtuele machine. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/ApplyArtifacts/actie | Artefacten Toep assen op de virtuele machine. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/claim/actie | Eigenaar worden van een bestaande virtuele machine |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/verwijderen | Virtuele machines verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/DetachDataDisk/actie | Ontkoppel de opgegeven schijf van de virtuele machine. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/GetRdpFileContents/actie | Hiermee wordt een teken reeks opgehaald waarmee de inhoud van het RDP-bestand voor de virtuele machine wordt aangeduid |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/ListApplicableSchedules/actie | Hier worden de toepasselijke start-en stop schema's vermeld, indien van toepassing. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/lezen | Virtuele machines lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/opnieuw implementeren/actie | Een virtuele machine opnieuw implementeren |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/formaat wijzigen/actie | Wijzig de grootte van de virtuele machine. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/restart/actie | Start een virtuele machine opnieuw op. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/planning/verwijderen | Schema's verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/planning/uitvoeren/actie | Een schema uitvoeren. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/planning/lezen | Planningen lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/planningen/schrijven | Schema's toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/start/Action | Start een virtuele machine. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/stop/actie | Een virtuele machine stoppen |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/TransferDisks/actie | Hiermee worden alle gegevens schijven die zijn gekoppeld aan de virtuele machine, overgedragen aan de huidige gebruiker. |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/unclaim/actie | Eigendom van een bestaande virtuele machine vrijgeven |
> | Bewerking | Micro soft. DevTestLab/Labs/informatie/schrijven | Virtuele machines toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/virtualNetworks/bastionHosts/verwijderen | Verwijder bastionhosts. |
> | Bewerking | Micro soft. DevTestLab/Labs/virtualNetworks/bastionHosts/lezen | Lees bastionhosts. |
> | Bewerking | Micro soft. DevTestLab/Labs/virtualNetworks/bastionHosts/schrijven | Bastionhosts toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/virtualNetworks/verwijderen | Virtuele netwerken verwijderen. |
> | Bewerking | Micro soft. DevTestLab/Labs/virtualNetworks/lezen | Virtuele netwerken lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/virtualNetworks/schrijven | Virtuele netwerken toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/vmPools/verwijderen | Verwijder groepen van virtuele machines. |
> | Bewerking | Micro soft. DevTestLab/Labs/vmPools/lezen | Virtuele-machine Pools lezen. |
> | Bewerking | Micro soft. DevTestLab/Labs/vmPools/schrijven | Virtuele-machine Pools toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/Labs/schrijven | Labs toevoegen of wijzigen. |
> | Bewerking | Micro soft. DevTestLab/locaties/bewerkingen/lezen | Lees bewerkingen. |
> | Bewerking | Micro soft. DevTestLab/REGI ster/actie | Hiermee wordt het abonnement geregistreerd |
> | Bewerking | Micro soft. DevTestLab/planningen/verwijderen | Schema's verwijderen. |
> | Bewerking | Micro soft. DevTestLab/planningen/uitvoeren/actie | Een schema uitvoeren. |
> | Bewerking | Micro soft. DevTestLab/planningen/lezen | Planningen lezen. |
> | Bewerking | Micro soft. DevTestLab/planningen/doel/actie | Hiermee wordt de doel resource-id van een planning bijgewerkt. |
> | Bewerking | Micro soft. DevTestLab/planningen/schrijven | Schema's toevoegen of wijzigen. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DocumentDB/databaseAccountNames/lezen | Hiermee wordt gecontroleerd op Beschik baarheid van namen. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/verzamelingen/verwijderen | Een verzameling verwijderen. Alleen van toepassing op API-typen: MongoDb. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/verzamelingen/operationResults/lezen | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: MongoDb. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/verzamelingen/lezen | Een verzameling lezen of alle verzamelingen weer geven. Alleen van toepassing op API-typen: MongoDb. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/Collections/Settings/operationResults/Read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: MongoDb. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/verzamelingen/instellingen/lezen | Lees de door Voer van een verzameling. Alleen van toepassing op API-typen: MongoDb. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/Collections/Settings/write | De door Voer van een verzameling bijwerken. Alleen van toepassing op API-typen: MongoDb. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/verzamelingen/schrijven | Een verzameling maken of bijwerken. Alleen van toepassing op API-typen: MongoDb. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/containers/verwijderen | Een container verwijderen. Alleen van toepassing op API-typen: ' SQL '. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/containers/operationResults/lezen | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: ' SQL '. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/containers/lezen | Een container lezen of alle containers weer geven. Alleen van toepassing op API-typen: ' SQL '. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/containers/instellingen/operationResults/lezen | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: ' SQL '. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/containers/instellingen/lezen | Lees een container doorvoer. Alleen van toepassing op API-typen: ' SQL '. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/containers/instellingen/schrijven | Een container doorvoer bijwerken. Alleen van toepassing op API-typen: ' SQL '. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/containers/schrijven | Een container maken of bijwerken. Alleen van toepassing op API-typen: ' SQL '. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/verwijderen | Een Data Base verwijderen. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/grafieken/verwijderen | Een grafiek verwijderen. Alleen van toepassing op API-typen: gremlin'. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/grafieken/operationResults/lezen | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: gremlin'. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/grafieken/lezen | Een grafiek lezen of alle grafieken weer geven. Alleen van toepassing op API-typen: gremlin'. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/grafieken/instellingen/operationResults/lezen | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: gremlin'. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/grafieken/instellingen/lezen | Lees een grafiek doorvoer. Alleen van toepassing op API-typen: gremlin'. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/grafieken/instellingen/schrijven | Een grafiek doorvoer bijwerken. Alleen van toepassing op API-typen: gremlin'. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/grafieken/schrijven | Een grafiek maken of bijwerken. Alleen van toepassing op API-typen: gremlin'. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/operationResults/lezen | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/lezen | Een Data Base lezen of alle data bases weer geven. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/instellingen/operationResults/lezen | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/instellingen/lezen | Lees de door Voer van een Data Base. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/instellingen/schrijven | Een Data Base-door Voer bijwerken. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/data bases/write | Een database maken. Alleen van toepassing op API-typen: ' SQL ', ' MongoDb ', ' gremlin'. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/verwijderen | Een spatie verwijderen. Alleen van toepassing op API-typen: Cassandra. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/operationResults/lezen | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: Cassandra. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/Keys/lezen | Lees een spatie of geef alle spatie ruimten weer. Alleen van toepassing op API-typen: Cassandra. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/Settings/operationResults/Read | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: Cassandra. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/Settings/lezen | Lees de door Voer van een spatie. Alleen van toepassing op API-typen: Cassandra. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/Settings/write | Een door Voer van een spatie bijwerken. Alleen van toepassing op API-typen: Cassandra. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/Tables/verwijderen | Een tabel verwijderen. Alleen van toepassing op API-typen: Cassandra. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/Tables/operationResults/lezen | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: Cassandra. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/Tables/tabellen/lezen | Een tabel lezen of alle tabellen in een lijst weer geven. Alleen van toepassing op API-typen: Cassandra. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/Tables/Settings/operationResults/lezen | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: Cassandra. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/Tables/Settings/lezen | Een tabel doorvoer lezen. Alleen van toepassing op API-typen: Cassandra. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/Tables/Settings/write | Een tabel doorvoer bijwerken. Alleen van toepassing op API-typen: Cassandra. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/Tables/Table/write | Een tabel maken of bijwerken. Alleen van toepassing op API-typen: Cassandra. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Keys/schrijven | Maak een spatie. Alleen van toepassing op API-typen: Cassandra. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Tables/verwijderen | Een tabel verwijderen. Alleen van toepassing op API-typen: Table. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Tables/operationResults/lezen | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: Table. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Tables/lezen | Een tabel lezen of alle tabellen in een lijst weer geven. Alleen van toepassing op API-typen: Table. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Tables/Settings/operationResults/lezen | Lees de status van de asynchrone bewerking. Alleen van toepassing op API-typen: Table. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Tables/Settings/lezen | Een tabel doorvoer lezen. Alleen van toepassing op API-typen: Table. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Tables/Settings/write | Een tabel doorvoer bijwerken. Alleen van toepassing op API-typen: Table. Alleen van toepassing op de instellings typen: door voer. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/api's/Tables/write | Een tabel maken of bijwerken. Alleen van toepassing op API-typen: Table. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/backup/Action | Een aanvraag indienen voor het configureren van de back-up |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/changeResourceGroup/Action | Resource groep van een database account wijzigen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/data bases/Collections/metricDefinitions/Read | Hiermee worden de metrische definities van de verzameling gelezen. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/data bases/verzamelingen/metrieken/lezen | Hiermee worden de metrische gegevens van de verzameling gelezen. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/data bases/verzamelingen/partitionKeyRangeId/metrieken/lezen | Data Base-account partitie sleutel niveau meet waarden lezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/data bases/verzamelingen/partities/metrieken/lezen | Metrische gegevens van het partitie niveau voor het database account lezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/data bases/verzamelingen/partities/lezen | Database account partities in een verzameling lezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/data bases/verzamelingen/partities/gebruik/lezen | Het gebruik van het database account op partitie niveau lezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/data bases/verzamelingen/gebruik/lezen | Hiermee worden de verzamelings gebruik gelezen. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/data bases/metricDefinitions/lezen | Hiermee worden de metrische definities van de data base gelezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/data bases/metrische gegevens/lezen | Hiermee worden de metrische gegevens van de data base gelezen. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/data bases/gebruik/lezen | Hiermee worden de database gebruik gelezen. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/verwijderen | Hiermee verwijdert u de database accounts. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/failoverPriorityChange/Action | Wijzig de prioriteiten van de failover van regio's van een database account. Dit wordt gebruikt voor het uitvoeren van een hand matige failover-bewerking |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/getBackupPolicy/Action | Het back-upbeleid van het database account ophalen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/listConnectionStrings/Action | De verbindings reeksen ophalen voor een database account |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/Listkeys ophalen/Action | Sleutels van een database account weer geven |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/metricDefinitions/lezen | Hiermee worden de definities van metrische gegevens van het database account gelezen. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/metrisch/lezen | Hiermee worden de metrische gegevens van het database account gelezen. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/offlineRegion/Action | Offline een regio van een database account.  |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/onlineRegion/Action | Online een regio van een database account. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/operationResults/lezen | De status van de asynchrone bewerking lezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/percentiel/metrieken/lezen | Metrische gegevens over latentie lezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/percentiel/lezen | Percentielen van replicatie latentie lezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/percentiel/sourceRegion/targetRegion/metrieken/lezen | Latentie-metrische gegevens lezen voor een specifieke bron-en doel regio |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/percentiel/targetRegion/metrieken/lezen | Metrische gegevens over latentie voor een specifieke doel regio lezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/verwijderen | Een particuliere endpoint-verbindings proxy van het database account verwijderen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/lezen | Een particuliere endpoint-verbindings proxy van het database account lezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/Action | Een particuliere endpoint-verbindings proxy van het database account valideren |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Een particuliere endpoint-verbindings proxy van het database account maken of bijwerken |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/lezen | Hiermee leest u een database account. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/readonlykeys/Action | Hiermee wordt de alleen-lezen sleutels van het database account gelezen. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/readonlykeys/lezen | Hiermee wordt de alleen-lezen sleutels van het database account gelezen. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/regenerateKey/Action | Sleutels van een database account draaien |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/Region/data bases/verzamelingen/metrieken/lezen | Leest de metrische gegevens over de verzameling van regio's. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/Region/data bases/verzamelingen/partitionKeyRangeId/metrieken/lezen | Regionale data base-account partitie sleutel niveau metrieken lezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/Region/data bases/verzamelingen/partities/metrieken/lezen | Regionale data base-account gegevens voor partitie niveau lezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/Region/data bases/verzamelingen/partities/lezen | Regionale database account partities in een verzameling lezen |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/Region/metrisch/lezen | Hiermee worden de metrische gegevens van de regio en het database account gelezen. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/Restore/Action | Een herstel aanvraag verzenden |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/Usage/lezen | Hiermee worden de gebruiks gegevens van het database account gelezen. |
> | Bewerking | Micro soft. DocumentDB/databaseAccounts/schrijven | Een database account bijwerken. |
> | Bewerking | Micro soft. DocumentDB/locaties/deleteVirtualNetworkOrSubnets/actie | Hiermee wordt aan micro soft. DocumentDB door gemeld dat VirtualNetwork of subnet wordt verwijderd |
> | Bewerking | Micro soft. DocumentDB/locaties/deleteVirtualNetworkOrSubnets/operationResults/lezen | Lees status van asynchrone bewerking deleteVirtualNetworkOrSubnets |
> | Bewerking | Micro soft. DocumentDB/locaties/operationsStatus/lezen | Hiermee wordt de status van asynchrone bewerkingen gelezen |
> | Bewerking | Micro soft. DocumentDB/operationResults/lezen | De status van de asynchrone bewerking lezen |
> | Bewerking | Micro soft. DocumentDB/Operations/lezen | Lees bewerkingen die beschikbaar zijn voor de micro soft-DocumentDB  |
> | Bewerking | Micro soft. DocumentDB/REGI ster/actie |  De resource provider van micro soft DocumentDB voor het abonnement registreren |

## <a name="microsoftdomainregistration"></a>Micro soft. DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. DomainRegistration/checkDomainAvailability/actie | Controleren of een domein beschikbaar is voor aankoop |
> | Bewerking | Micro soft. DomainRegistration/domeinen/verwijderen | Een bestaand domein verwijderen. |
> | Bewerking | Micro soft. DomainRegistration/domeinen/domainownershipidentifiers/verwijderen | Eigendoms-id verwijderen |
> | Bewerking | Micro soft. DomainRegistration/domains/domainownershipidentifiers/lezen | Eigendoms-id's weer geven |
> | Bewerking | Micro soft. DomainRegistration/domains/domainownershipidentifiers/lezen | Eigendoms-id ophalen |
> | Bewerking | Micro soft. DomainRegistration/domains/domainownershipidentifiers/write | Id maken of bijwerken |
> | Bewerking | Micro soft. DomainRegistration/domains/operationresults/lezen | Een domein bewerking ophalen |
> | Bewerking | Micro soft. DomainRegistration/domeinen/lezen | De lijst met domeinen ophalen |
> | Bewerking | Micro soft. DomainRegistration/domeinen/lezen | Domein ophalen |
> | Bewerking | Micro soft. DomainRegistration/domeinen/vernieuwen/actie | Een bestaand domein vernieuwen. |
> | Bewerking | Micro soft. DomainRegistration/domeinen/schrijven | Een nieuw domein toevoegen of een bestaande bijwerken |
> | Bewerking | Micro soft. DomainRegistration/generateSsoRequest/actie | Genereer een aanvraag voor het aanmelden bij het Domain Control Center. |
> | Bewerking | Micro soft. DomainRegistration/listDomainRecommendations/actie | De aanbevelingen van het lijst domein ophalen op basis van tref woorden |
> | Bewerking | Micro soft. DomainRegistration/Operations/lezen | Alle bewerkingen van de app service-domein registratie weer geven |
> | Bewerking | Micro soft. DomainRegistration/REGI ster/actie | De resource provider van micro soft-domeinen voor het abonnement registreren |
> | Bewerking | Micro soft. DomainRegistration/topLevelDomains/listAgreements/Action | Actie voor lijst overeenkomst |
> | Bewerking | Micro soft. DomainRegistration/topLevelDomains/lezen | Toplevel-domeinen ophalen |
> | Bewerking | Micro soft. DomainRegistration/topLevelDomains/lezen | Toplevel-domein ophalen |
> | Bewerking | Micro soft. DomainRegistration/validateDomainRegistrationInformation/actie | Het domein aankoop object valideren zonder dit te verzenden |

## <a name="microsofteventgrid"></a>Micro soft. EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. EventGrid/domeinen/verwijderen | Een domein verwijderen |
> | Bewerking | Micro soft. EventGrid/domains/Listkeys ophalen/Action | Sleutels voor een domein weer geven |
> | Bewerking | Micro soft. EventGrid/domains/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens voor domeinen opgehaald |
> | Bewerking | Micro soft. EventGrid/domeinen/lezen | Een domein lezen |
> | Bewerking | Micro soft. EventGrid/domains/regenerateKey/Action | Sleutel voor een domein opnieuw genereren |
> | Bewerking | Micro soft. EventGrid/domeinen/onderwerpen/verwijderen | Een domein onderwerp verwijderen |
> | Bewerking | Micro soft. EventGrid/domeinen/onderwerpen/lezen | Een onderwerp van een domein lezen |
> | Bewerking | Micro soft. EventGrid/domeinen/onderwerpen/schrijven | Een domein onderwerp maken of bijwerken |
> | Bewerking | Micro soft. EventGrid/domeinen/schrijven | Een domein maken of bijwerken |
> | Bewerking | Micro soft. EventGrid/eventSubscriptions/verwijderen | Een eventSubscription verwijderen |
> | Bewerking | Micro soft. EventGrid/eventSubscriptions/getFullUrl/Action | Volledige URL voor het gebeurtenis abonnement ophalen |
> | Bewerking | Micro soft. EventGrid/eventSubscriptions/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor gebeurtenis abonnementen opgehaald |
> | Bewerking | Micro soft. EventGrid/eventSubscriptions/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor gebeurtenis abonnementen gemaakt of bijgewerkt |
> | Bewerking | Micro soft. EventGrid/eventSubscriptions/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor eventSubscriptions |
> | Bewerking | Micro soft. EventGrid/eventSubscriptions/lezen | Een eventSubscription lezen |
> | Bewerking | Micro soft. EventGrid/eventSubscriptions/schrijven | Een eventSubscription maken of bijwerken |
> | Bewerking | Micro soft. EventGrid/extensionTopics/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor onderwerpen opgehaald |
> | Bewerking | Micro soft. EventGrid/extensionTopics/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor onderwerpen gemaakt of bijgewerkt |
> | Bewerking | Micro soft. EventGrid/extensionTopics/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor onderwerpen |
> | Bewerking | Micro soft. EventGrid/extensionTopics/lezen | Lees een extensionTopic. |
> | Bewerking | Micro soft. EventGrid/locaties/eventSubscriptions/lezen | Regionale gebeurtenis abonnementen weer geven |
> | Bewerking | Micro soft. EventGrid/locaties/operationResults/lezen | Het resultaat van een regionale bewerking lezen |
> | Bewerking | Micro soft. EventGrid/locaties/operationsStatus/lezen | De status van een regionale bewerking lezen |
> | Bewerking | Micro soft. EventGrid/locaties/topictypes/eventSubscriptions/lezen | Regionale gebeurtenis abonnementen weer geven op topictype |
> | Bewerking | Micro soft. EventGrid/operationResults/lezen | Het resultaat van een bewerking lezen |
> | Bewerking | Micro soft. EventGrid/Operations/lezen | Lijst met EventGrid-bewerkingen. |
> | Bewerking | Micro soft. EventGrid/operationsStatus/lezen | De status van een bewerking lezen |
> | Bewerking | Micro soft. EventGrid/REGI ster/actie | Hiermee wordt het abonnement voor de EventGrid-resource provider geregistreerd. |
> | Bewerking | Micro soft. EventGrid/onderwerpen/verwijderen | Een onderwerp verwijderen |
> | Bewerking | Micro soft. EventGrid/topics/Listkeys ophalen/Action | Sleutels voor een onderwerp weer geven |
> | Bewerking | Micro soft. EventGrid/topics/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor onderwerpen opgehaald |
> | Bewerking | Micro soft. EventGrid/topics/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor onderwerpen gemaakt of bijgewerkt |
> | Bewerking | Micro soft. EventGrid/topics/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor onderwerpen |
> | Bewerking | Micro soft. EventGrid/topics/lezen | Een onderwerp lezen |
> | Bewerking | Micro soft. EventGrid/topics/regenerateKey/Action | Sleutel voor een onderwerp opnieuw genereren |
> | Bewerking | Micro soft. EventGrid/topics/schrijven | Een onderwerp maken of bijwerken |
> | Bewerking | Micro soft. EventGrid/topictypes/eventSubscriptions/lezen | Globale gebeurtenis abonnementen weer geven op onderwerps type |
> | Bewerking | Micro soft. EventGrid/topictypes/eventtypes/lezen | Lees eventtypes ondersteund door een topictype |
> | Bewerking | Micro soft. EventGrid/topictypes/lezen | Een topictype lezen |
> | Bewerking | Micro soft. EventGrid/registratie/actie | Hiermee wordt de registratie van het abonnement voor de EventGrid-resource provider ongedaan gemaakt. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. EventHub/availableClusterRegions/lezen | Lees bewerking om een lijst te maken met beschik bare vooraf ingerichte clusters per Azure-regio. |
> | Bewerking | Micro soft. EventHub/checkNameAvailability/actie | Controleert de beschik baarheid van de naam ruimte onder het gegeven abonnement. |
> | Bewerking | Micro soft. EventHub/checkNamespaceAvailability/actie | Controleert de beschik baarheid van de naam ruimte onder het gegeven abonnement. Deze API is afgeschaft. gebruik in plaats daarvan CheckNameAvailability. |
> | Bewerking | Micro soft. EventHub/clusters/verwijderen | Hiermee verwijdert u een bestaande cluster bron. |
> | Bewerking | Micro soft. EventHub/clusters/naam ruimten/lezen | Naam ruimte ARM-Id's weer geven voor naam ruimten in een cluster. |
> | Bewerking | Micro soft. EventHub/clusters/operationresults/lezen | De status van een asynchrone cluster bewerking ophalen. |
> | Bewerking | Micro soft. EventHub/clusters/providers/micro soft. Insights/metricDefinitions/lezen | Lijst met resource beschrijvingen voor metrische gegevens van cluster ophalen |
> | Bewerking | Micro soft. EventHub/clusters/lezen | Hiermee wordt de cluster bron beschrijving opgehaald |
> | Bewerking | Micro soft. EventHub/clusters/schrijven | Hiermee maakt of wijzigt u een bestaande cluster bron. |
> | Bewerking | Micro soft. EventHub/locaties/deleteVirtualNetworkOrSubnets/actie | Hiermee worden de VNet-regels in de EventHub-resource provider voor het opgegeven VNet verwijderd |
> | Bewerking | Micro soft. EventHub/naam ruimten/authorizationRules/actie | De autorisatie regel voor de naam ruimte wordt bijgewerkt. Deze API is afgeschaft. Gebruik in plaats daarvan een PUT-aanroep om de naam ruimte autorisatie regel bij te werken. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Bewerking | Micro soft. EventHub/naam ruimten/authorizationRules/verwijderen | Naam ruimte autorisatie regel verwijderen. De standaard regel voor autorisatie van de naam ruimte kan niet worden verwijderd.  |
> | Bewerking | Micro soft. EventHub/naam ruimten/authorizationRules/listkeys ophalen/actie | De verbindings reeks ophalen voor de naam ruimte |
> | Bewerking | Micro soft. EventHub/naam ruimten/authorizationRules/lezen | De lijst met beschrijvingen van autorisatie regels voor naam ruimten ophalen. |
> | Bewerking | Micro soft. EventHub/naam ruimten/authorizationRules/regenerateKeys/actie | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Bewerking | Micro soft. EventHub/naam ruimten/authorizationRules/schrijven | Maak autorisatie regels voor het naam ruimte niveau en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels, de primaire en secundaire sleutels kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. EventHub/naam ruimten/verwijderen | Naam ruimte resource verwijderen |
> | Bewerking | Micro soft. EventHub/naam ruimten/disasterRecoveryConfigs/authorizationRules/listkeys ophalen/actie | Hiermee worden de sleutels voor autorisatie regels opgehaald voor de primaire naam ruimte voor nood herstel |
> | Bewerking | Micro soft. EventHub/naam ruimten/disasterRecoveryConfigs/authorizationRules/lezen | De autorisatie regels van de primaire naam ruimte voor herstel na nood geval ophalen |
> | Bewerking | Micro soft. EventHub/naam ruimten/disasterRecoveryConfigs/breakPairing/actie | Hiermee schakelt u herstel na nood gevallen uit en stopt u het repliceren van wijzigingen van primaire naar secundaire naam ruimten. |
> | Bewerking | Micro soft. EventHub/naam ruimten/disasterrecoveryconfigs/checkNameAvailability/actie | Controleert de beschik baarheid van de naam ruimte alias onder het opgegeven abonnement. |
> | Bewerking | Micro soft. EventHub/naam ruimten/disasterRecoveryConfigs/verwijderen | Hiermee verwijdert u de nood herstel configuratie die is gekoppeld aan de naam ruimte. Deze bewerking kan alleen worden aangeroepen via de primaire naam ruimte. |
> | Bewerking | Micro soft. EventHub/naam ruimten/disasterRecoveryConfigs/failover/actie | Hiermee wordt een GEO DR-failover aangeroepen en wordt de naam ruimte alias zo geconfigureerd dat deze naar de secundaire naam ruimte wijst. |
> | Bewerking | Micro soft. EventHub/naam ruimten/disasterRecoveryConfigs/lezen | Hiermee haalt u de nood herstel configuratie op die is gekoppeld aan de naam ruimte. |
> | Bewerking | Micro soft. EventHub/naam ruimten/disasterRecoveryConfigs/schrijven | Hiermee wordt de nood herstel configuratie die is gekoppeld aan de naam ruimte gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. EventHub/naam ruimten/Event hubs/authorizationRules/actie | Bewerking voor het bijwerken van EventHub. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. Autorisatie regels. Gebruik een aanroep voor het bijwerken van de autorisatie regel. |
> | Bewerking | Micro soft. EventHub/naam ruimten/Event hubs/authorizationRules/verwijderen | Bewerking voor het verwijderen van EventHub-autorisatie regels |
> | Bewerking | Micro soft. EventHub/naam ruimten/Event hubs/authorizationRules/listkeys ophalen/actie | De verbindings reeks ophalen voor EventHub |
> | Bewerking | Micro soft. EventHub/naam ruimten/Event hubs/authorizationRules/lezen |  De lijst met EventHub-autorisatie regels ophalen |
> | Bewerking | Micro soft. EventHub/naam ruimten/Event hubs/authorizationRules/regenerateKeys/actie | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Bewerking | Micro soft. EventHub/naam ruimten/Event hubs/authorizationRules/schrijven | Maak EventHub-autorisatie regels en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. EventHub/naam ruimten/Event hubs/consumergroups/verwijderen | Bewerking voor het verwijderen van de ConsumerGroup-resource |
> | Bewerking | Micro soft. EventHub/naam ruimten/Event hubs/consumergroups/lezen | Lijst met ConsumerGroup-resource beschrijvingen ophalen |
> | Bewerking | Micro soft. EventHub/naam ruimten/Event hubs/consumergroups/schrijven | Eigenschappen van ConsumerGroup maken of bijwerken. |
> | Bewerking | Micro soft. EventHub/naam ruimten/Event hubs/verwijderen | Bewerking voor het verwijderen van de EventHub-resource |
> | Bewerking | Micro soft. EventHub/naam ruimten/Event hubs/lezen | Lijst met EventHub-resource beschrijvingen ophalen |
> | Bewerking | Micro soft. EventHub/naam ruimten/Event hubs/schrijven | Eigenschappen van EventHub maken of bijwerken. |
> | Bewerking | Micro soft. EventHub/naam ruimten/ipFilterRules/verwijderen | IP-filter bron verwijderen |
> | Bewerking | Micro soft. EventHub/naam ruimten/ipFilterRules/lezen | IP-filter bron ophalen |
> | Bewerking | Micro soft. EventHub/naam ruimten/ipFilterRules/schrijven | IP-filter bron maken |
> | DataAction | Micro soft. EventHub/naam ruimten/berichten/ontvangen/actie | Berichten ontvangen |
> | DataAction | Micro soft. EventHub/naam ruimten/berichten/verzenden/actie | Berichten verzenden |
> | Bewerking | Micro soft. EventHub/naam ruimten/messagingPlan/lezen | Hiermee wordt het berichten plan voor een naam ruimte opgehaald.<br>Deze API is afgeschaft.<br>Eigenschappen die via de MessagingPlan-resource beschikbaar worden gemaakt, worden in latere API-versies verplaatst naar de bron naam ruimte (Parent).<br>Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Bewerking | Micro soft. EventHub/naam ruimten/messagingPlan/schrijven | Hiermee wordt het bericht abonnement voor een naam ruimte bijgewerkt.<br>Deze API is afgeschaft.<br>Eigenschappen die via de MessagingPlan-resource beschikbaar worden gemaakt, worden in latere API-versies verplaatst naar de bron naam ruimte (Parent).<br>Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Bewerking | Micro soft. EventHub/naam ruimten/networkruleset/verwijderen | VNET-regel resource verwijderen |
> | Bewerking | Micro soft. EventHub/naam ruimten/networkruleset/lezen | Hiermee wordt de NetworkRuleSet-resource opgehaald |
> | Bewerking | Micro soft. EventHub/naam ruimten/networkruleset/schrijven | VNET-regel resource maken |
> | Bewerking | Micro soft. EventHub/naam ruimten/networkrulesets/verwijderen | VNET-regel resource verwijderen |
> | Bewerking | Micro soft. EventHub/naam ruimten/networkrulesets/lezen | Hiermee wordt de NetworkRuleSet-resource opgehaald |
> | Bewerking | Micro soft. EventHub/naam ruimten/networkrulesets/schrijven | VNET-regel resource maken |
> | Bewerking | Micro soft. EventHub/naam ruimten/operationresults/lezen | De status van de naam ruimte bewerking ophalen |
> | Bewerking | Micro soft. EventHub/naam ruimten/providers/micro soft. Insights/diagnosticSettings/lezen | Lijst met resource beschrijvingen voor Diagnostische instellingen van naam ruimte ophalen |
> | Bewerking | Micro soft. EventHub/naam ruimten/providers/micro soft. Insights/diagnosticSettings/schrijven | Lijst met resource beschrijvingen voor Diagnostische instellingen van naam ruimte ophalen |
> | Bewerking | Micro soft. EventHub/naam ruimten/providers/micro soft. Insights/logDefinitions/lezen | Lijst met naam ruimte logboek bron beschrijvingen ophalen |
> | Bewerking | Micro soft. EventHub/naam ruimten/providers/micro soft. Insights/metricDefinitions/lezen | Lijst met resource beschrijvingen voor metrische gegevens van de naam ruimte ophalen |
> | Bewerking | Micro soft. EventHub/naam ruimten/lezen | De lijst met de resource beschrijving van de naam ruimte ophalen |
> | Bewerking | Micro soft. EventHub/naam ruimten/removeAcsNamepsace/actie | ACS-naam ruimte verwijderen |
> | Bewerking | Micro soft. EventHub/naam ruimten/virtualNetworkRules/verwijderen | VNET-regel resource verwijderen |
> | Bewerking | Micro soft. EventHub/naam ruimten/virtualNetworkRules/lezen | Hiermee wordt VNET-regel Resource opgehaald |
> | Bewerking | Micro soft. EventHub/naam ruimten/virtualNetworkRules/schrijven | VNET-regel resource maken |
> | Bewerking | Micro soft. EventHub/naam ruimten/schrijven | Maak een naam ruimte resource en werk de eigenschappen bij. Tags en capaciteit van de naam ruimte zijn de eigenschappen die kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. EventHub/Operations/lezen | Get-bewerkingen |
> | Bewerking | Micro soft. EventHub/REGI ster/actie | Hiermee wordt het abonnement voor de EventHub-resource provider geregistreerd en wordt het maken van EventHub-resources mogelijk |
> | Bewerking | Micro soft. EventHub/SKU/lezen | Lijst met resource beschrijvingen van SKU ophalen |
> | Bewerking | Micro soft. EventHub/SKU/regio's/lezen | Lijst met SkuRegions-resource beschrijvingen ophalen |
> | Bewerking | Micro soft. EventHub/registratie/actie | Hiermee wordt de EventHub-resource provider geregistreerd |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. features/onderdelen/lezen | Hiermee haalt u de functies van een abonnement op. |
> | Bewerking | Micro soft. features/Operations/lezen | Hiermee wordt de lijst met bewerkingen opgehaald. |
> | Bewerking | Micro soft. features/providers/onderdelen/lezen | Hiermee wordt de functie van een abonnement in een bepaalde resource provider opgehaald. |
> | Bewerking | Micro soft. onderdelen/providers/onderdelen/registreren/actie | Hiermee wordt de functie voor een abonnement in een bepaalde resource provider geregistreerd. |
> | Bewerking | Micro soft. onderdelen/providers/onderdelen/registratie/actie | Hiermee wordt de registratie van de functie voor een abonnement in een bepaalde resource provider ongedaan gemaakt. |
> | Bewerking | Micro soft. onderdelen/registreren/actie | Hiermee wordt de functie van een abonnement geregistreerd. |

## <a name="microsoftguestconfiguration"></a>Micro soft. GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. GuestConfiguration/guestConfigurationAssignments/verwijderen | Toewijzing van gast configuratie verwijderen. |
> | Bewerking | Micro soft. GuestConfiguration/guestConfigurationAssignments/lezen | Toewijzing van gast configuratie ophalen. |
> | Bewerking | Micro soft. GuestConfiguration/guestConfigurationAssignments/Reports/lezen | Toewijzings rapport voor gast configuratie ophalen. |
> | Bewerking | Micro soft. GuestConfiguration/guestConfigurationAssignments/schrijven | Nieuwe gast configuratie toewijzing maken. |
> | Bewerking | Micro soft. GuestConfiguration/Operations/lezen | Hiermee worden de bewerkingen voor de resource provider micro soft. GuestConfiguration opgehaald |
> | Bewerking | Micro soft. GuestConfiguration/REGI ster/actie | Hiermee wordt het abonnement voor de resource provider micro soft. GuestConfiguration geregistreerd. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. HDInsight/clusters/toepassingen/verwijderen | Toepassing voor HDInsight-cluster verwijderen |
> | Bewerking | Micro soft. HDInsight/clusters/toepassingen/lezen | Toepassing voor HDInsight-cluster ophalen |
> | Bewerking | Micro soft. HDInsight/clusters/toepassingen/schrijven | Toepassing voor HDInsight-cluster maken of bijwerken |
> | Bewerking | Micro soft. HDInsight/clusters/changerdpsetting/actie | RDP-instelling voor HDInsight-cluster wijzigen |
> | Bewerking | Micro soft. HDInsight/clusters/configuraties/actie | Configuratie van HDInsight-cluster bijwerken |
> | Bewerking | Micro soft. HDInsight/clusters/configuraties/actie | Configuraties van HDInsight-clusters ophalen |
> | Bewerking | Micro soft. HDInsight/clusters/configuraties/lezen | Configuraties van HDInsight-clusters ophalen |
> | Bewerking | Micro soft. HDInsight/clusters/verwijderen | Een HDInsight-cluster verwijderen |
> | Bewerking | Micro soft. HDInsight/clusters/uitbrei dingen/verwijderen | Cluster uitbreiding voor HDInsight-cluster verwijderen |
> | Bewerking | Micro soft. HDInsight/clusters/uitbrei dingen/lezen | Cluster uitbreiding voor HDInsight-cluster ophalen |
> | Bewerking | Micro soft. HDInsight/clusters/uitbrei dingen/schrijven | Cluster uitbreiding voor HDInsight-cluster maken |
> | Bewerking | Micro soft. HDInsight/clusters/getGatewaySettings/actie | Gateway-instellingen voor HDInsight-cluster ophalen |
> | Bewerking | Micro soft. HDInsight/clusters/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor het bron HDInsight-cluster opgehaald |
> | Bewerking | Micro soft. HDInsight/clusters/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor het bron HDInsight-cluster gemaakt of bijgewerkt |
> | Bewerking | Micro soft. HDInsight/clusters/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor het HDInsight-cluster |
> | Bewerking | Micro soft. HDInsight/clusters/lezen | Details over HDInsight-cluster ophalen |
> | Bewerking | Micro soft. HDInsight/clusters/rollen/formaat wijzigen/actie | Het formaat van een HDInsight-cluster wijzigen |
> | Bewerking | Micro soft. HDInsight/clusters/updateGatewaySettings/actie | Gateway-instellingen voor HDInsight-cluster bijwerken |
> | Bewerking | Micro soft. HDInsight/clusters/schrijven | Een HDInsight-cluster maken of bijwerken |
> | Bewerking | Micro soft. HDInsight/locaties/mogelijkheden/lezen | Abonnements mogelijkheden ophalen |
> | Bewerking | Micro soft. HDInsight/locaties/checkNameAvailability/lezen | Beschik baarheid van naam controleren |
> | Bewerking | Micro soft. HDInsight/registreren/actie | HDInsight-resource provider registreren voor het abonnement |
> | Bewerking | Micro soft. HDInsight/niet registreren/actie | Registratie van HDInsight-resource provider opheffen voor het abonnement |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ImportExport/Jobs/verwijderen | Hiermee verwijdert u een bestaande taak. |
> | Bewerking | Micro soft. ImportExport/Jobs/listBitLockerKeys/actie | Hiermee worden de BitLocker-sleutels voor de opgegeven taak opgehaald. |
> | Bewerking | Micro soft. ImportExport/Jobs/lezen | Hiermee worden de eigenschappen van de opgegeven taak opgehaald of wordt de lijst met taken geretourneerd. |
> | Bewerking | Micro soft. ImportExport/Jobs/schrijven | Hiermee maakt u een taak met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven taak bij. |
> | Bewerking | Micro soft. ImportExport/locaties/lezen | Hiermee worden de eigenschappen opgehaald voor de opgegeven locatie of wordt de lijst met locaties geretourneerd. |
> | Bewerking | Micro soft. ImportExport/Operations/lezen | Hiermee worden de bewerkingen opgehaald die door de resource provider worden ondersteund. |
> | Bewerking | Micro soft. ImportExport/REGI ster/actie | Hiermee wordt het abonnement voor de resource provider import/export geregistreerd en wordt het maken van import/export-taken ingeschakeld. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Insights/ActionGroups/verwijderen | Een actie groep verwijderen |
> | Bewerking | Micro soft. Insights/ActionGroups/lezen | Een actie groep lezen |
> | Bewerking | Micro soft. Insights/ActionGroups/schrijven | Een actie groep maken of bijwerken |
> | Bewerking | Micro soft. Insights/ActivityLogAlerts/geactiveerd/actie | Waarschuwing voor activiteiten logboek geactiveerd |
> | Bewerking | Micro soft. Insights/ActivityLogAlerts/verwijderen | Een waarschuwing voor een activiteiten logboek verwijderen |
> | Bewerking | Micro soft. Insights/ActivityLogAlerts/lezen | Een waarschuwing voor een activiteiten logboek lezen |
> | Bewerking | Micro soft. Insights/ActivityLogAlerts/schrijven | Een waarschuwing voor een activiteiten logboek maken of bijwerken |
> | Bewerking | Micro soft. Insights/AlertRules/geactiveerd/actie | Klassieke waarschuwing voor metrische gegevens geactiveerd |
> | Bewerking | Micro soft. Insights/AlertRules/verwijderen | Een klassieke waarschuwing voor metrische gegevens verwijderen |
> | Bewerking | Micro soft. Insights/AlertRules/incidenten/lezen | Een bericht met een klassiek metrische waarschuwing lezen |
> | Bewerking | Micro soft. Insights/AlertRules/lezen | Een klassieke waarschuwing voor metrische gegevens lezen |
> | Bewerking | Micro soft. Insights/AlertRules/opgelost/actie | Klassieke waarschuwing voor metrische gegevens opgelost |
> | Bewerking | Micro soft. Insights/AlertRules/beperkt/actie | Waarschuwings regel voor klassieke metrische gegevens beperkt |
> | Bewerking | Micro soft. Insights/AlertRules/schrijven | Een klassieke waarschuwing voor metrische gegevens maken of bijwerken |
> | Bewerking | Micro soft. Insights/AutoscaleSettings/verwijderen | Een instelling voor automatisch schalen verwijderen |
> | Bewerking | Micro soft. Insights/AutoscaleSettings/providers/micro soft. Insights/diagnosticSettings/lezen | Een diagnostische instelling voor bronnen lezen |
> | Bewerking | Micro soft. Insights/AutoscaleSettings/providers/micro soft. Insights/diagnosticSettings/schrijven | Een diagnostische instelling voor resources maken of bijwerken |
> | Bewerking | Micro soft. Insights/AutoscaleSettings/providers/micro soft. Insights/logDefinitions/lezen | Logboek definities lezen |
> | Bewerking | Micro soft. Insights/AutoscaleSettings/providers/micro soft. Insights/MetricDefinitions/lezen | Metrische definities lezen |
> | Bewerking | Micro soft. Insights/AutoscaleSettings/lezen | Een instelling voor automatisch schalen lezen |
> | Bewerking | Micro soft. Insights/AutoscaleSettings/Scaledown/actie | Automatisch omlaag schalen gestart |
> | Bewerking | Micro soft. Insights/AutoscaleSettings/ScaledownResult/actie | Automatisch omlaag schalen voltooid |
> | Bewerking | Micro soft. Insights/AutoscaleSettings/Scaleup/actie | Automatisch omhoog schalen gestart |
> | Bewerking | Micro soft. Insights/AutoscaleSettings/ScaleupResult/actie | Automatisch omhoog schalen voltooid |
> | Bewerking | Micro soft. Insights/AutoscaleSettings/schrijven | Een instelling voor automatisch schalen maken of bijwerken |
> | Bewerking | Micro soft. Insights/basis lijn/lezen | Een metrische basis lijn lezen (preview-versie) |
> | Bewerking | Micro soft. Insights/CalculateBaseline/lezen | Basis lijn voor metrische waarden berekenen (preview-versie) |
> | Bewerking | Micro soft. Insights/onderdelen/AnalyticsItems/verwijderen | Een Application Insights Analytics-item verwijderen |
> | Bewerking | Micro soft. Insights/onderdelen/AnalyticsItems/lezen | Een Application Insights Analytics-item lezen |
> | Bewerking | Micro soft. Insights/onderdelen/AnalyticsItems/schrijven | Een Application Insights Analytics-item schrijven |
> | Bewerking | Micro soft. Insights/onderdelen/AnalyticsTables/actie | Application Insights Analytics Table-actie |
> | Bewerking | Micro soft. Insights/onderdelen/AnalyticsTables/verwijderen | Een Application Insights Analytics table-schema verwijderen |
> | Bewerking | Micro soft. Insights/onderdelen/AnalyticsTables/lezen | Een Application Insights Analytics table-schema lezen |
> | Bewerking | Micro soft. Insights/onderdelen/AnalyticsTables/schrijven | Een Application Insights Analytics table-schema schrijven |
> | Bewerking | Micro soft. Insights/onderdelen/aantekeningen/verwijderen | Een Application Insights aantekening verwijderen |
> | Bewerking | Micro soft. Insights/onderdelen/aantekeningen/lezen | Een Application Insights aantekening lezen |
> | Bewerking | Micro soft. Insights/onderdelen/aantekeningen/schrijven | Een Application Insights aantekening schrijven |
> | Bewerking | Micro soft. Insights/onderdelen/API/lezen | API voor Application Insights onderdeel gegevens lezen |
> | Bewerking | Micro soft. Insights/onderdelen/ApiKeys/actie | Een Application Insights-API-sleutel genereren |
> | Bewerking | Micro soft. Insights/onderdelen/ApiKeys/verwijderen | Een Application Insights-API-sleutel verwijderen |
> | Bewerking | Micro soft. Insights/onderdelen/ApiKeys/lezen | Een Application Insights-API-sleutel lezen |
> | Bewerking | Micro soft. Insights/onderdelen/BillingPlanForComponent/lezen | Een facturerings plan voor Application Insights onderdeel lezen |
> | Bewerking | Micro soft. Insights/onderdelen/CurrentBillingFeatures/lezen | De huidige facturerings functies voor Application Insights onderdeel lezen |
> | Bewerking | Micro soft. Insights/onderdelen/CurrentBillingFeatures/schrijven | Huidige facturerings functies voor Application Insights onderdeel schrijven |
> | Bewerking | Micro soft. Insights/onderdelen/DailyCapReached/actie | De dagelijkse limiet voor het Application Insights onderdeel is bereikt |
> | Bewerking | Micro soft. Insights/onderdelen/DailyCapWarningThresholdReached/actie | De waarschuwings drempel voor de dagelijkse limiet voor Application Insights onderdeel is bereikt |
> | Bewerking | Micro soft. Insights/onderdelen/DefaultWorkItemConfig/lezen | Een Application Insights standaard configuratie voor ALM-integratie lezen |
> | Bewerking | Micro soft. Insights/onderdelen/verwijderen | De configuratie van een Application Insights-onderdeel verwijderen |
> | Bewerking | Micro soft. Insights/onderdelen/gebeurtenissen/lezen | Logboeken van Application Insights ophalen met de OData-query-indeling |
> | Bewerking | Micro soft. Insights/onderdelen/ExportConfiguration/actie | Actie Application Insights export instellingen |
> | Bewerking | Micro soft. Insights/onderdelen/ExportConfiguration/verwijderen | Application Insights export instellingen verwijderen |
> | Bewerking | Micro soft. Insights/onderdelen/ExportConfiguration/lezen | Instellingen voor Application Insights exporteren lezen |
> | Bewerking | Micro soft. Insights/onderdelen/ExportConfiguration/schrijven | Instellingen voor Application Insights exporteren schrijven |
> | Bewerking | Micro soft. Insights/onderdelen/ExtendQueries/lezen | Uitgebreide query resultaten van Application Insights onderdeel lezen |
> | Bewerking | Micro soft. Insights/onderdelen/favorieten/verwijderen | Een Application Insights favoriet verwijderen |
> | Bewerking | Micro soft. Insights/onderdelen/Favorieten/lezen | Een Application Insights favoriet lezen |
> | Bewerking | Micro soft. Insights/onderdelen/favorieten/schrijven | Een Application Insights favoriet schrijven |
> | Bewerking | Micro soft. Insights/onderdelen/FeatureCapabilities/lezen | Functie mogelijkheden van Application Insights onderdeel lezen |
> | Bewerking | Micro soft. Insights/onderdelen/GetAvailableBillingFeatures/lezen | Beschik bare facturerings functies van Application Insights onderdeel lezen |
> | Bewerking | Micro soft. Insights/onderdelen/GetToken/lezen | Een token voor een Application Insights onderdeel lezen |
> | Bewerking | Micro soft. Insights/onderdelen/MetricDefinitions/lezen | Metrische definities van Application Insights-onderdeel lezen |
> | Bewerking | Micro soft. Insights/onderdelen/metrische gegevens/lezen | Metrische gegevens van Application Insights-onderdeel lezen |
> | Bewerking | Micro soft. Insights/onderdelen/verplaatsen/actie | Een Application Insights onderdeel verplaatsen naar een andere resource groep of een ander abonnement |
> | Bewerking | Micro soft. Insights/onderdelen/MyAnalyticsItems/verwijderen | Een Application Insights persoonlijk analyse-item verwijderen |
> | Bewerking | Micro soft. Insights/onderdelen/MyAnalyticsItems/lezen | Een Application Insights persoonlijk analyse-item lezen |
> | Bewerking | Micro soft. Insights/onderdelen/MyAnalyticsItems/schrijven | Een Application Insights persoonlijk analyse-item schrijven |
> | Bewerking | Micro soft. Insights/onderdelen/MyFavorites/lezen | Een Application Insights persoonlijke favoriet lezen |
> | Bewerking | Micro soft. Insights/onderdelen/bewerkingen/lezen | Status van langlopende bewerkingen in Application Insights ophalen |
> | Bewerking | Micro soft. Insights/onderdelen/PricingPlans/lezen | Een prijs plan voor een Application Insights onderdeel lezen |
> | Bewerking | Micro soft. Insights/onderdelen/PricingPlans/schrijven | Een prijs plan voor een Application Insights onderdeel schrijven |
> | Bewerking | Micro soft. Insights/onderdelen/ProactiveDetectionConfigs/lezen | Application Insights proactieve detectie configuratie lezen |
> | Bewerking | Micro soft. Insights/onderdelen/ProactiveDetectionConfigs/schrijven | Application Insights proactieve detectie configuratie wordt geschreven |
> | Bewerking | Micro soft. Insights/onderdelen/providers/micro soft. Insights/MetricDefinitions/lezen | Metrische definities lezen |
> | Bewerking | Micro soft. Insights/onderdelen/opschonen/actie | Gegevens uit Application Insights verwijderen |
> | Bewerking | Micro soft. Insights/onderdelen/query/lezen | Query's uitvoeren voor Application Insights-logboeken |
> | Bewerking | Micro soft. Insights/onderdelen/QuotaStatus/lezen | Quota status van Application Insights onderdeel lezen |
> | Bewerking | Micro soft. Insights/onderdelen/lezen | De configuratie van een Application Insights-onderdeel lezen |
> | Bewerking | Micro soft. Insights/onderdelen/SyntheticMonitorLocations/lezen | Application Insights-webtest locaties lezen |
> | Bewerking | Micro soft. Insights/onderdelen/webtesten/lezen | Een configuratie van een webtest lezen |
> | Bewerking | Micro soft. Insights/onderdelen/WorkItemConfigs/verwijderen | Een Application Insights ALM-integratie configuratie verwijderen |
> | Bewerking | Micro soft. Insights/onderdelen/WorkItemConfigs/lezen | Een Application Insights ALM-integratie configuratie lezen |
> | Bewerking | Micro soft. Insights/onderdelen/WorkItemConfigs/schrijven | Een Application Insights ALM-integratie configuratie schrijven |
> | Bewerking | Micro soft. Insights/onderdelen/schrijven | Schrijven naar de configuratie van een Application Insights-onderdeel |
> | Bewerking | Micro soft. Insights/DiagnosticSettings/verwijderen | Een diagnostische instelling voor bronnen verwijderen |
> | Bewerking | Micro soft. Insights/DiagnosticSettings/lezen | Een diagnostische instelling voor bronnen lezen |
> | Bewerking | Micro soft. Insights/DiagnosticSettings/schrijven | Een diagnostische instelling voor resources maken of bijwerken |
> | Bewerking | Micro soft. Insights/EventCategories/lezen | Beschik bare gebeurtenis categorieën voor activiteiten logboek lezen |
> | Bewerking | Micro soft. Insights/eventtypes/digestevents/lezen | Samen vatting van beheer gebeurtenis type lezen |
> | Bewerking | Micro soft. Insights/eventtypes/values/Read | Gebeurtenissen in het activiteiten logboek lezen |
> | Bewerking | Micro soft. Insights/ExtendedDiagnosticSettings/verwijderen | Een diagnostische instelling voor het netwerk stroom logboek verwijderen |
> | Bewerking | Micro soft. Insights/ExtendedDiagnosticSettings/lezen | Een diagnostische instelling voor het netwerk stroom logboek lezen |
> | Bewerking | Micro soft. Insights/ExtendedDiagnosticSettings/schrijven | Een diagnostische instelling voor het netwerk stroom logboek maken of bijwerken |
> | Bewerking | Micro soft. Insights/ListMigrationDate/actie | Migratie datum van abonnement terughalen |
> | Bewerking | Micro soft. Insights/ListMigrationDate/lezen | Migratie datum van abonnement terughalen |
> | Bewerking | Micro soft. Insights/LogDefinitions/lezen | Logboek definities lezen |
> | Bewerking | Micro soft. Insights/LogProfiles/verwijderen | Een logboek profiel voor het activiteiten logboek verwijderen |
> | Bewerking | Micro soft. Insights/LogProfiles/lezen | Een logboek profiel voor het activiteiten logboek lezen |
> | Bewerking | Micro soft. Insights/LogProfiles/schrijven | Een logboek profiel voor het activiteiten logboek maken of bijwerken |
> | Bewerking | Micro soft. Insights/logboeken/ADAssessmentRecommendation/lezen | Gegevens uit de ADAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ADReplicationResult/lezen | Gegevens uit de ADReplicationResult-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ADSecurityAssessmentRecommendation/lezen | Gegevens uit de ADSecurityAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/waarschuwing/lezen | Gegevens uit de tabel waarschuwing lezen |
> | Bewerking | Micro soft. Insights/logboeken/AlertHistory/lezen | Gegevens uit de AlertHistory-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ApplicationInsights/lezen | Gegevens uit de ApplicationInsights-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/AzureActivity/lezen | Gegevens uit de AzureActivity-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/AzureMetrics/lezen | Gegevens uit de AzureMetrics-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/BoundPort/lezen | Gegevens uit de BoundPort-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/CommonSecurityLog/lezen | Gegevens uit de CommonSecurityLog-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ComputerGroup/lezen | Gegevens uit de ComputerGroup-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ConfigurationChange/lezen | Gegevens uit de ConfigurationChange-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ConfigurationData/lezen | Gegevens uit de ConfigurationData-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ContainerImageInventory/lezen | Gegevens uit de ContainerImageInventory-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ContainerInventory/lezen | Gegevens uit de ContainerInventory-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ContainerLog/lezen | Gegevens uit de ContainerLog-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ContainerServiceLog/lezen | Gegevens uit de ContainerServiceLog-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DeviceAppCrash/lezen | Gegevens uit de DeviceAppCrash-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DeviceAppLaunch/lezen | Gegevens uit de DeviceAppLaunch-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DeviceCalendar/lezen | Gegevens uit de DeviceCalendar-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DeviceCleanup/lezen | Gegevens uit de DeviceCleanup-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DeviceConnectSession/lezen | Gegevens uit de DeviceConnectSession-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DeviceEtw/lezen | Gegevens uit de DeviceEtw-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DeviceHardwareHealth/lezen | Gegevens uit de DeviceHardwareHealth-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/Apparaatstatus/lezen | Gegevens uit de apparaatstatus-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DeviceHeartbeat/lezen | Gegevens uit de DeviceHeartbeat-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DeviceSkypeHeartbeat/lezen | Gegevens uit de DeviceSkypeHeartbeat-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DeviceSkypeSignIn/lezen | Gegevens uit de DeviceSkypeSignIn-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DeviceSleepState/lezen | Gegevens uit de DeviceSleepState-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DHAppFailure/lezen | Gegevens uit de DHAppFailure-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DHAppReliability/lezen | Gegevens uit de DHAppReliability-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DHDriverReliability/lezen | Gegevens uit de DHDriverReliability-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DHLogonFailures/lezen | Gegevens uit de DHLogonFailures-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DHLogonMetrics/lezen | Gegevens uit de DHLogonMetrics-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DHOSCrashData/lezen | Gegevens uit de DHOSCrashData-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DHOSReliability/lezen | Gegevens uit de DHOSReliability-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DHWipAppLearning/lezen | Gegevens uit de DHWipAppLearning-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DnsEvents/lezen | Gegevens uit de DnsEvents-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/DnsInventory/lezen | Gegevens uit de DnsInventory-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ETWEvent/lezen | Gegevens uit de ETWEvent-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/gebeurtenis/lezen | Gegevens uit de gebeurtenis tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ExchangeAssessmentRecommendation/lezen | Gegevens uit de ExchangeAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ExchangeOnlineAssessmentRecommendation/lezen | Gegevens uit de ExchangeOnlineAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/heartbeat/lezen | Gegevens uit de heartbeat-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/IISAssessmentRecommendation/lezen | Gegevens uit de IISAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/InboundConnection/lezen | Gegevens uit de InboundConnection-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/KubeNodeInventory/lezen | Gegevens uit de KubeNodeInventory-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/KubePodInventory/lezen | Gegevens uit de KubePodInventory-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/LinuxAuditLog/lezen | Gegevens uit de LinuxAuditLog-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAApplication/lezen | Gegevens uit de MAApplication-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAApplicationHealth/lezen | Gegevens uit de MAApplicationHealth-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAApplicationHealthAlternativeVersions/lezen | Gegevens uit de MAApplicationHealthAlternativeVersions-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAApplicationHealthIssues/lezen | Gegevens uit de MAApplicationHealthIssues-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAApplicationInstance/lezen | Gegevens uit de MAApplicationInstance-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAApplicationInstanceReadiness/lezen | Gegevens uit de MAApplicationInstanceReadiness-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAApplicationReadiness/lezen | Gegevens uit de MAApplicationReadiness-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MADeploymentPlan/lezen | Gegevens uit de MADeploymentPlan-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MADevice/lezen | Gegevens uit de MADevice-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MADevicePnPHealth/lezen | Gegevens uit de MADevicePnPHealth-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MADevicePnPHealthAlternativeVersions/lezen | Gegevens uit de MADevicePnPHealthAlternativeVersions-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MADevicePnPHealthIssues/lezen | Gegevens uit de MADevicePnPHealthIssues-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MADeviceReadiness/lezen | Gegevens uit de MADeviceReadiness-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MADriverInstanceReadiness/lezen | Gegevens uit de MADriverInstanceReadiness-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MADriverReadiness/lezen | Gegevens uit de MADriverReadiness-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeAddin/lezen | Gegevens uit de MAOfficeAddin-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeAddinHealth/lezen | Gegevens uit de MAOfficeAddinHealth-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeAddinHealthIssues/lezen | Gegevens uit de MAOfficeAddinHealthIssues-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeAddinInstance/lezen | Gegevens uit de MAOfficeAddinInstance-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeAddinInstanceReadiness/lezen | Gegevens uit de MAOfficeAddinInstanceReadiness-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeAddinReadiness/lezen | Gegevens uit de MAOfficeAddinReadiness-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeApp/lezen | Gegevens uit de MAOfficeApp-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeAppHealth/lezen | Gegevens uit de MAOfficeAppHealth-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeAppInstance/lezen | Gegevens uit de MAOfficeAppInstance-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeAppReadiness/lezen | Gegevens uit de MAOfficeAppReadiness-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeBuildInfo/lezen | Gegevens uit de MAOfficeBuildInfo-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeCurrencyAssessment/lezen | Gegevens uit de MAOfficeCurrencyAssessment-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeCurrencyAssessmentDailyCounts/lezen | Gegevens uit de MAOfficeCurrencyAssessmentDailyCounts-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeDeploymentStatus/lezen | Gegevens uit de MAOfficeDeploymentStatus-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeMacroHealth/lezen | Gegevens uit de MAOfficeMacroHealth-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeMacroHealthIssues/lezen | Gegevens uit de MAOfficeMacroHealthIssues-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeMacroIssueInstanceReadiness/lezen | Gegevens uit de MAOfficeMacroIssueInstanceReadiness-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeMacroIssueReadiness/lezen | Gegevens uit de MAOfficeMacroIssueReadiness-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeMacroSummary/lezen | Gegevens uit de MAOfficeMacroSummary-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeSuite/lezen | Gegevens uit de MAOfficeSuite-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAOfficeSuiteInstance/lezen | Gegevens uit de MAOfficeSuiteInstance-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAProposedPilotDevices/lezen | Gegevens uit de MAProposedPilotDevices-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAWindowsBuildInfo/lezen | Gegevens uit de MAWindowsBuildInfo-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAWindowsCurrencyAssessment/lezen | Gegevens uit de MAWindowsCurrencyAssessment-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAWindowsCurrencyAssessmentDailyCounts/lezen | Gegevens uit de MAWindowsCurrencyAssessmentDailyCounts-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAWindowsDeploymentStatus/lezen | Gegevens uit de MAWindowsDeploymentStatus-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/MAWindowsSysReqInstanceReadiness/lezen | Gegevens uit de MAWindowsSysReqInstanceReadiness-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/NetworkMonitoring/lezen | Gegevens uit de NetworkMonitoring-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/OfficeActivity/lezen | Gegevens uit de OfficeActivity-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/bewerking/lezen | Gegevens uit de bewerkings tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/OutboundConnection/lezen | Gegevens uit de OutboundConnection-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/perf/lezen | Gegevens uit de prestatie tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ProtectionStatus/lezen | Gegevens uit de ProtectionStatus-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/lezen | Gegevens uit al uw logboeken lezen |
> | Bewerking | Micro soft. Insights/logboeken/ReservedAzureCommonFields/lezen | Gegevens uit de ReservedAzureCommonFields-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ReservedCommonFields/lezen | Gegevens uit de ReservedCommonFields-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/SCCMAssessmentRecommendation/lezen | Gegevens uit de SCCMAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/SCOMAssessmentRecommendation/lezen | Gegevens uit de SCOMAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/SecurityAlert/lezen | Gegevens uit de SecurityAlert-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/Security Baseline Baseline/lezen | Gegevens uit de Security Baseline Baseline-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/Summary/lezen | Gegevens uit de Summary-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/SecurityDetection/lezen | Gegevens uit de SecurityDetection-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/SecurityEvent/lezen | Gegevens uit de SecurityEvent-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ServiceFabricOperationalEvent/lezen | Gegevens uit de ServiceFabricOperationalEvent-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ServiceFabricReliableActorEvent/lezen | Gegevens uit de ServiceFabricReliableActorEvent-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/ServiceFabricReliableServiceEvent/lezen | Gegevens uit de ServiceFabricReliableServiceEvent-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/SfBAssessmentRecommendation/lezen | Gegevens uit de SfBAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/SfBOnlineAssessmentRecommendation/lezen | Gegevens uit de SfBOnlineAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/SharePointOnlineAssessmentRecommendation/lezen | Gegevens uit de SharePointOnlineAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/SPAssessmentRecommendation/lezen | Gegevens uit de SPAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/SQLAssessmentRecommendation/lezen | Gegevens uit de SQLAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/SQLQueryPerformance/lezen | Gegevens uit de SQLQueryPerformance-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/syslog/lezen | Gegevens uit de syslog-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/SysmonEvent/lezen | Gegevens uit de SysmonEvent-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/tabellen. aangepast/lezen | Gegevens uit een aangepast logboek lezen |
> | Bewerking | Micro soft. Insights/logboeken/UAApp/lezen | Gegevens uit de UAApp-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/UAComputer/lezen | Gegevens uit de UAComputer-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/UAComputerRank/lezen | Gegevens uit de UAComputerRank-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/UADriver/lezen | Gegevens uit de UADriver-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/UADriverProblemCodes/lezen | Gegevens uit de UADriverProblemCodes-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/UAFeedback/lezen | Gegevens uit de UAFeedback-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/UAHardwareSecurity/lezen | Gegevens uit de UAHardwareSecurity-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/UAIESiteDiscovery/lezen | Gegevens uit de UAIESiteDiscovery-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/UAOfficeAddIn/lezen | Gegevens uit de UAOfficeAddIn-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/UAProposedActionPlan/lezen | Gegevens uit de UAProposedActionPlan-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/UASysReqIssue/lezen | Gegevens uit de UASysReqIssue-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/UAUpgradedComputer/lezen | Gegevens uit de UAUpgradedComputer-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/bijwerken/lezen | Gegevens uit de update tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/Updaterunprogress Installation/lezen | Gegevens uit de Updaterunprogress Installation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/update Summary/lezen | Gegevens uit de update Summary-tabel lezen |
> | Bewerking | Micro soft. Insights/Logboeken/gebruik/lezen | Gegevens uit de gebruiks tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/W3CIISLog/lezen | Gegevens uit de W3CIISLog-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/WaaSDeploymentStatus/lezen | Gegevens uit de WaaSDeploymentStatus-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/WaaSInsiderStatus/lezen | Gegevens uit de WaaSInsiderStatus-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/WaaSUpdateStatus/lezen | Gegevens uit de WaaSUpdateStatus-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/WDAVStatus/lezen | Gegevens uit de WDAVStatus-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/WDAVThreat/lezen | Gegevens uit de WDAVThreat-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/WindowsClientAssessmentRecommendation/lezen | Gegevens uit de WindowsClientAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/WindowsFirewall/lezen | Gegevens uit de WindowsFirewall-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/WindowsServerAssessmentRecommendation/lezen | Gegevens uit de WindowsServerAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/WireData/lezen | Gegevens uit de WireData-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/WUDOAggregatedStatus/lezen | Gegevens uit de WUDOAggregatedStatus-tabel lezen |
> | Bewerking | Micro soft. Insights/logboeken/WUDOStatus/lezen | Gegevens uit de WUDOStatus-tabel lezen |
> | Bewerking | Micro soft. Insights/MetricAlerts/verwijderen | Een waarschuwing voor metrische gegevens verwijderen |
> | Bewerking | Micro soft. Insights/MetricAlerts/lezen | Een waarschuwing voor metrische gegevens lezen |
> | Bewerking | Micro soft. Insights/MetricAlerts/status/lezen | Status van waarschuwing voor metrische gegevens lezen |
> | Bewerking | Micro soft. Insights/MetricAlerts/schrijven | Een waarschuwing voor metrische gegevens maken of bijwerken |
> | Bewerking | Micro soft. Insights/MetricBaselines/lezen | Metrische basis lijnen lezen |
> | Bewerking | Micro soft. Insights/MetricDefinitions/micro soft. Insights/lezen | Metrische definities lezen |
> | Bewerking | Micro soft. Insights/MetricDefinitions/providers/micro soft. Insights/lezen | Metrische definities lezen |
> | Bewerking | Micro soft. Insights/MetricDefinitions/lezen | Metrische definities lezen |
> | Bewerking | Micro soft. Insights/Metricnamespaces/lezen | Metrische naam ruimten lezen |
> | Bewerking | Micro soft. Insights/statistieken/actie | Metrische actie |
> | Bewerking | Micro soft. Insights/metrische gegevens/micro soft. Insights/lezen | Metrische gegevens lezen |
> | Bewerking | Micro soft. Insights/metrische gegevens/providers/metrieken/lezen | Metrische gegevens lezen |
> | Bewerking | Micro soft. Insights/metrische gegevens/lezen | Metrische gegevens lezen |
> | DataAction | Micro soft. Insights/metrische gegevens/schrijven | Metrische gegevens schrijven |
> | Bewerking | Micro soft. Insights/MigrateToNewpricingModel/actie | Abonnement migreren naar nieuw prijs model |
> | Bewerking | Micro soft. Insights/bewerkingen/lezen | Leesbewerkingen |
> | Bewerking | Micro soft. Insights/registreren/actie | De micro soft Insights-provider registreren |
> | Bewerking | Micro soft. Insights/RollbackToLegacyPricingModel/actie | Abonnement terugdraaien naar een verouderd prijs model |
> | Bewerking | Micro soft. Insights/ScheduledQueryRules/verwijderen | Een geplande query regel verwijderen |
> | Bewerking | Micro soft. Insights/ScheduledQueryRules/lezen | Een geplande query regel lezen |
> | Bewerking | Micro soft. Insights/ScheduledQueryRules/schrijven | Een geplande query regel schrijven |
> | Bewerking | Micro soft. Insights/tenants/registreren/actie | Initialiseert de micro soft Insights-provider |
> | Bewerking | Micro soft. Insights/registratie/actie | De micro soft Insights-provider registreren |
> | Bewerking | Micro soft. Insights/webtestes/verwijderen | Een webtest configuratie verwijderen |
> | Bewerking | Micro soft. Insights/webtests/GetToken/lezen | Een token voor webtest lezen |
> | Bewerking | Micro soft. Insights/webtests/MetricDefinitions/lezen | Metrische definities van webtest lezen |
> | Bewerking | Micro soft. Insights/webtests/metrische gegevens/lezen | De metrische gegevens van een webtest lezen |
> | Bewerking | Micro soft. Insights/webtests/lezen | Een configuratie van een webtest lezen |
> | Bewerking | Micro soft. Insights/webtests/schrijven | Schrijven naar een webtest-configuratie |
> | Bewerking | Micro soft. Insights/werkmappen/verwijderen | Een werkmap verwijderen |
> | Bewerking | Micro soft. Insights/werkmappen/lezen | Een werkmap lezen |
> | Bewerking | Micro soft. Insights/werkmappen/schrijven | Een werkmap maken of bijwerken |

## <a name="microsoftintune"></a>Micro soft. intune

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. intune/diagnosticsettings/verwijderen | Een diagnostische instelling verwijderen |
> | Bewerking | Micro soft. intune/diagnosticsettings/lezen | Een diagnostische instelling lezen |
> | Bewerking | Micro soft. intune/diagnosticsettings/schrijven | Een diagnostische instelling schrijven |
> | Bewerking | Micro soft. intune/diagnosticsettingscategories/lezen | Categorieën voor Diagnostische instellingen lezen |

## <a name="microsoftiotcentral"></a>Micro soft. IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. IoTCentral/appTemplates/actie | Hiermee worden alle beschik bare toepassings sjablonen op Azure IoT Central opgehaald |
> | Bewerking | Micro soft. IoTCentral/checkNameAvailability/actie | Hiermee wordt gecontroleerd of een IoT Central toepassings naam beschikbaar is |
> | Bewerking | Micro soft. IoTCentral/checkSubdomainAvailability/actie | Hiermee wordt gecontroleerd of een IoT Central subdomein van de toepassing beschikbaar is |
> | Bewerking | Micro soft. IoTCentral/IoTApps/verwijderen | Hiermee verwijdert u een IoT Central-toepassing |
> | Bewerking | Micro soft. IoTCentral/IoTApps/lezen | Hiermee wordt een enkele IoT Central-toepassing opgehaald |
> | Bewerking | Micro soft. IoTCentral/IoTApps/schrijven | Hiermee wordt een IoT Central-toepassing gemaakt of bijgewerkt |
> | Bewerking | Micro soft. IoTCentral/Operations/lezen | Hiermee worden alle beschik bare bewerkingen voor IoT Central toepassingen opgehaald |
> | Bewerking | Micro soft. IoTCentral/REGI ster/actie | Het abonnement voor de resource provider van Azure IoT Central registreren |

## <a name="microsoftiotspaces"></a>Micro soft. IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. IoTSpaces/Graph/verwijderen | Hiermee wordt de resource van micro soft. IoTSpaces-grafiek verwijderd |
> | Bewerking | Micro soft. IoTSpaces/Graph/lezen | Hiermee worden de resource (s) van de micro soft. IoTSpaces-grafiek opgehaald |
> | Bewerking | Micro soft. IoTSpaces/Graph/schrijven | Resource van micro soft. IoTSpaces Graph maken |
> | Bewerking | Micro soft. IoTSpaces/REGI ster/actie | Registreer het abonnement voor de resource provider micro soft. IoTSpaces Graph om het maken van resources in te scha kelen |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. checkNameAvailability/lezen | Hiermee wordt gecontroleerd of de naam van een sleutel kluis geldig is en niet wordt gebruikt |
> | Bewerking | Micro soft. deletedVaults/lezen | De eigenschappen van voorlopig verwijderde sleutel kluizen weer geven |
> | Bewerking | Micro soft. hsmPools/verwijderen | Een HSM-groep verwijderen |
> | Bewerking | Micro soft. hsmPools/joinVault/actie | Een sleutel kluis toevoegen aan een HSM-groep |
> | Bewerking | Micro soft. hsmPools/lezen | De eigenschappen van een HSM-groep weer geven |
> | Bewerking | Micro soft. hsmPools/schrijven | Een nieuwe HSM-groep maken om de eigenschappen van een bestaande HSM-groep bij te werken |
> | Bewerking | Micro soft. sleutel kluis/locaties/deletedVaults/opschonen/actie | Een voorlopig verwijderde sleutel kluis leegmaken |
> | Bewerking | Micro soft. de sleutel kluis/locaties/deletedVaults/lezen | De eigenschappen van een voorlopig verwijderde sleutel kluis weer geven |
> | Bewerking | Micro soft. sleutel kluis/locaties/deleteVirtualNetworkOrSubnets/actie | Hiermee wordt aan micro soft.-sleutel kluis gemeld dat een virtueel netwerk of subnet wordt verwijderd |
> | Bewerking | Micro soft. de sleutel kluis/locaties/operationResults/lezen | Het resultaat van een langdurige bewerking controleren |
> | Bewerking | Micro soft. sleutel kluis/bewerkingen/lezen | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn op micro soft. |
> | Bewerking | Micro soft. de sleutel kluis/REGI ster/actie | Hiermee wordt een abonnement geregistreerd |
> | Bewerking | Micro soft. sleutel kluis/registratie/actie | Registratie van een abonnement opheffen |
> | Bewerking | Micro soft. de sleutel kluis/kluizen/accessPolicies/schrijven | Een bestaand toegangs beleid bijwerken door samen te voegen of te vervangen of een nieuw toegangs beleid toe te voegen aan een kluis. |
> | Bewerking | Micro soft.-sleutel kluis/-kluizen/verwijderen | Een sleutelkluis verwijderen |
> | Bewerking | Micro soft.-sleutel kluis/-kluizen/implementeren/actie | Hiermee wordt toegang tot geheimen in een sleutel kluis ingeschakeld bij het implementeren van Azure-resources |
> | Bewerking | Micro soft. sleutel kluis/kluizen/eventGridFilters/verwijderen | Hiermee wordt micro soft. EventGrid een melding verzonden dat een abonnement op Key Vault wordt verwijderd |
> | Bewerking | Micro soft. de sleutel kluis/kluizen/eventGridFilters/lezen | Hiermee wordt micro soft.-sleutel kluis geïnformeerd dat een EventGrid-abonnement voor Key Vault wordt weer gegeven |
> | Bewerking | Micro soft. de sleutel kluis/kluizen/eventGridFilters/schrijven | Hiermee wordt micro soft.-sleutel kluis geïnformeerd dat er een nieuw EventGrid-abonnement voor Key Vault wordt gemaakt |
> | Bewerking | Micro soft.-sleutel kluis/-kluizen/lezen | De eigenschappen van een sleutel kluis weer geven |
> | Bewerking | Micro soft.-sleutel kluis/kluizen/geheimen/lezen | De eigenschappen van een geheim weer geven, maar niet de waarde ervan. |
> | Bewerking | Micro soft.-sleutel kluis/kluizen/geheimen/schrijven | Maak een nieuw geheim of werk de waarde van een bestaand geheim bij. |
> | Bewerking | Micro soft. de sleutel kluis/kluizen/schrijven | Een nieuwe sleutel kluis maken of de eigenschappen van een bestaande sleutel kluis bijwerken |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Kusto/clusters/activeren/actie | Start het cluster. |
> | Bewerking | Micro soft. Kusto/clusters/AttachedDatabaseConfigurations/verwijderen | Hiermee verwijdert u een gekoppelde database configuratie resourceCopy. |
> | Bewerking | Micro soft. Kusto/clusters/AttachedDatabaseConfigurations/lezen | Hiermee leest u een gekoppelde database configuratie resourceCopy. |
> | Bewerking | Micro soft. Kusto/clusters/AttachedDatabaseConfigurations/schrijven | Hiermee wordt een gekoppelde database configuratie resourceCopy geschreven. |
> | Bewerking | Micro soft. Kusto/clusters/CheckNameAvailability/actie | Controleert de beschik baarheid van de cluster naam. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/AddPrincipals/actie | Voegt databaseprincipal toe. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/CheckNameAvailability/actie | Controleert de beschik baarheid van namen voor een bepaald type. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/DataConnections/verwijderen | Hiermee verwijdert u een gegevens verbindingen resourceCopy. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/DataConnections/lezen | Hiermee wordt een gegevens verbindingen-resourceCopy gelezen. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/DataConnections/schrijven | Schrijft een gegevens verbindingen resourceCopy. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/DataConnectionValidation/actie | Valideert de database gegevens verbinding. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/verwijderen | Hiermee verwijdert u een Data Base-resourceCopy. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/EventHubConnections/verwijderen | Hiermee verwijdert u een event hub-verbinding resourceCopy. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/EventHubConnections/lezen | Hiermee leest u een event hub-verbinding resourceCopy. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/EventHubConnections/schrijven | Schrijft een event hub-verbinding resourceCopy. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/EventHubConnectionValidation/actie | Hiermee valideert u de data base Event hub-verbinding. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/ListPrincipals/actie | Een lijst met data base-principals. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/lezen | Hiermee leest u een Data Base-resourceCopy. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/RemovePrincipals/actie | Hiermee verwijdert u databaseprincipal. |
> | Bewerking | Micro soft. Kusto/clusters/data bases/schrijven | Schrijft een Data Base-resourceCopy. |
> | Bewerking | Micro soft. Kusto/clusters/deactiveren/actie | Hiermee wordt het cluster gestopt. |
> | Bewerking | Micro soft. Kusto/clusters/verwijderen | Hiermee verwijdert u een cluster-resourceCopy. |
> | Bewerking | Micro soft. Kusto/clusters/lezen | Hiermee wordt een cluster-resourceCopy gelezen. |
> | Bewerking | Micro soft. Kusto/clusters/Sku's/lezen | Hiermee leest u een cluster-SKU resourceCopy. |
> | Bewerking | Micro soft. Kusto/clusters/start/actie | Start het cluster. |
> | Bewerking | Micro soft. Kusto/clusters/stoppen/actie | Hiermee wordt het cluster gestopt. |
> | Bewerking | Micro soft. Kusto/clusters/schrijven | Schrijft een cluster-resourceCopy. |
> | Bewerking | Micro soft. Kusto/DetachFollowerDatabases/actie | Koppelt de data bases van de opvolger los. |
> | Bewerking | Micro soft. Kusto/ListFollowerDatabases/actie | Een lijst met de data bases van de follow-out. |
> | Bewerking | Micro soft. Kusto/locaties/CheckNameAvailability/actie | Controleert de beschik baarheid van resourceCopy-namen. |
> | Bewerking | Micro soft. Kusto/locaties/operationresults/lezen | Lees bewerkingen resourceCopys |
> | Bewerking | Micro soft. Kusto/Operations/lezen | Lees bewerkingen resourceCopys |
> | Bewerking | Micro soft. Kusto/REGI ster/actie | Registratie actie abonnement |
> | Bewerking | Micro soft. Kusto/REGI ster/actie | Hiermee wordt het abonnement geregistreerd bij de resource provider Kusto. |
> | Bewerking | Micro soft. Kusto/Sku's/lezen | Hiermee leest u een SKU-resourceCopy. |
> | Bewerking | Micro soft. Kusto/registratie/actie | Hiermee wordt de registratie van het abonnement bij de Kusto-resource provider ongedaan gemaakt. |

## <a name="microsoftlabservices"></a>Micro soft. LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. LabServices/labAccounts/CreateLab/Action | Een lab maken in een Lab-account. |
> | Bewerking | Micro soft. LabServices/labAccounts/verwijderen | Lab-accounts verwijderen. |
> | Bewerking | Micro soft. LabServices/labAccounts/galleryImages/verwijderen | Galerie afbeeldingen verwijderen. |
> | Bewerking | Micro soft. LabServices/labAccounts/galleryImages/lezen | Galerie afbeeldingen lezen. |
> | Bewerking | Micro soft. LabServices/labAccounts/galleryImages/write | Galerie afbeeldingen toevoegen of wijzigen. |
> | Bewerking | Micro soft. LabServices/labAccounts/GetRegionalAvailability/Action | Informatie over regionale Beschik baarheid ophalen voor elke grootte categorie die is geconfigureerd met een Lab-account |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/AddUsers/actie | Gebruikers toevoegen aan een Lab |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/verwijderen | Labs verwijderen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/verwijderen | Omgevings instelling verwijderen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/omgevingen/verwijderen | Omgevingen verwijderen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/omgevingen/lezen | Omgevingen lezen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/environments/ResetPassword/Action | Hiermee wordt het gebruikers wachtwoord opnieuw ingesteld op een omgeving |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/omgevingen/start/actie | Start een omgeving door alle resources in de omgeving te starten. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/omgevingen/stoppen/actie | Stopt een omgeving door alle resources in de omgeving te stoppen |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/omgevingen/schrijven | Omgevingen toevoegen of wijzigen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/Publish/Action | Voorziet in het inrichten van vereiste resources voor een omgevings instelling op basis van de huidige status van de instelling lab/environment. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/lezen | Omgevings instelling lezen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/ResetPassword/Action | Hiermee stelt u het wacht woord voor de virtuele machine van de sjabloon opnieuw in. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/SaveImage/Action | Hiermee wordt de huidige sjabloon afbeelding opgeslagen in de gedeelde galerie in het lab-account |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/planning/verwijderen | Schema's verwijderen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/planningen/lezen | Planningen lezen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/planningen/schrijven | Schema's toevoegen of wijzigen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/start/Action | Start een sjabloon door alle resources in de sjabloon te starten. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/stop/actie | Hiermee stopt u een sjabloon door alle resources in de sjabloon te stoppen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/environmentSettings/schrijven | Omgevings instelling toevoegen of wijzigen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/lezen | Lees Labs. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/SendEmail/actie | E-mail met registratie koppeling naar het lab verzenden |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/gebruikers/verwijderen | Gebruikers verwijderen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/gebruikers/lezen | Gebruikers lezen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/gebruikers/schrijven | Gebruikers toevoegen of wijzigen. |
> | Bewerking | Micro soft. LabServices/labAccounts/Labs/schrijven | Labs toevoegen of wijzigen. |
> | Bewerking | Micro soft. LabServices/labAccounts/lezen | Lab-accounts lezen. |
> | Bewerking | Micro soft. LabServices/labAccounts/sharedGalleries/verwijderen | Verwijder sharedgalleries. |
> | Bewerking | Micro soft. LabServices/labAccounts/sharedGalleries/lezen | Lees sharedgalleries. |
> | Bewerking | Micro soft. LabServices/labAccounts/sharedGalleries/write | Sharedgalleries toevoegen of wijzigen. |
> | Bewerking | Micro soft. LabServices/labAccounts/sharedImages/verwijderen | Verwijder sharedimages. |
> | Bewerking | Micro soft. LabServices/labAccounts/sharedImages/lezen | Lees sharedimages. |
> | Bewerking | Micro soft. LabServices/labAccounts/sharedImages/write | Sharedimages toevoegen of wijzigen. |
> | Bewerking | Micro soft. LabServices/labAccounts/schrijven | Lab-accounts toevoegen of wijzigen. |
> | Bewerking | Micro soft. LabServices/locaties/bewerkingen/lezen | Lees bewerkingen. |
> | Bewerking | Micro soft. LabServices/REGI ster/actie | Hiermee wordt het abonnement geregistreerd |
> | Bewerking | Micro soft. LabServices/users/GetOperationBatchStatus/Action | Batch bewerkings status ophalen |
> | Bewerking | Micro soft. LabServices/users/GetOperationStatus/Action | Hiermee wordt de status van een langlopende bewerking opgehaald |
> | Bewerking | Micro soft. LabServices/users/GetPersonalPreferences/Action | Persoonlijke voor keuren voor een gebruiker ophalen |
> | Bewerking | Micro soft. LabServices/users/ListAllEnvironments/Action | Alle omgevingen voor de gebruiker weer geven |
> | Bewerking | Micro soft. LabServices/gebruikers/registreren/actie | Een gebruiker registreren bij een beheerd Lab |
> | Bewerking | Micro soft. LabServices/users/ResetPassword/Action | Hiermee wordt het gebruikers wachtwoord opnieuw ingesteld op een omgeving |
> | Bewerking | Micro soft. LabServices/users/StartEnvironment/Action | Start een omgeving door alle resources in de omgeving te starten. |
> | Bewerking | Micro soft. LabServices/users/StopEnvironment/Action | Stopt een omgeving door alle resources in de omgeving te stoppen |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Logic/integrationAccounts/overeenkomsten/verwijderen | Hiermee verwijdert u de overeenkomst in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/agreements/listContentCallbackUrl/Action | Hiermee wordt de call back-URL voor de overeenkomst inhoud opgehaald in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/overeenkomsten/lezen | Hiermee leest u de overeenkomst in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/overeenkomsten/schrijven | Hiermee wordt de overeenkomst in het integratie account gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Logic/integrationAccounts/assembly's/verwijderen | Hiermee verwijdert u de assembly in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/assemblies/listContentCallbackUrl/actie | Hiermee wordt de call back-URL opgehaald voor assembly-inhoud in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/assembly's/lezen | Hiermee leest u de assembly in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/assembly's/schrijven | Hiermee wordt de assembly in het integratie account gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Logic/integrationAccounts/batchConfigurations/verwijderen | Hiermee verwijdert u de batch configuratie in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/batchConfigurations/lezen | Hiermee leest u de batch configuratie in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/batchConfigurations/schrijven | Hiermee wordt de batch configuratie in het integratie account gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Logic/integrationAccounts/certificaten/verwijderen | Hiermee verwijdert u het certificaat in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/certificaten/lezen | Hiermee leest u het certificaat in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/certificaten/schrijven | Hiermee wordt het certificaat in het integratie account gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Logic/integrationAccounts/verwijderen | Hiermee verwijdert u het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/samen voegen/actie | Koppelt aan het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/listCallbackUrl/Action | Hiermee wordt de call back-URL opgehaald voor het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/listKeyVaultKeys/Action | Hiermee worden de sleutels in de sleutel kluis opgehaald. |
> | Bewerking | Micro soft. Logic/integrationAccounts/logTrackingEvents/Action | Registreert de tracerings gebeurtenissen in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/kaarten/verwijderen | Hiermee verwijdert u de kaart in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/Maps/listContentCallbackUrl/actie | Hiermee wordt de call back-URL opgehaald voor de toewijzing van inhoud in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/Maps/lezen | Hiermee leest u de kaart in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/kaarten/schrijven | Hiermee wordt de kaart in het integratie account gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Logic/integrationAccounts/partners/verwijderen | Hiermee verwijdert u de partner in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/partners/listContentCallbackUrl/actie | Hiermee wordt de call back-URL voor partner inhoud opgehaald in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/partners/lezen | Hiermee leest u de partner in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/partners/schrijven | Hiermee wordt de partner in het integratie account gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Logic/integrationAccounts/providers/micro soft. Insights/logDefinitions/lezen | Hiermee worden de logboek definities van het integratie account gelezen. |
> | Bewerking | Micro soft. Logic/integrationAccounts/lezen | Hiermee leest u het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/regenerateAccessKey/Action | Hiermee worden de toegangs sleutel geheimen opnieuw gegenereerd. |
> | Bewerking | Micro soft. Logic/integrationAccounts/schema's/verwijderen | Hiermee verwijdert u het schema in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/schemas/listContentCallbackUrl/actie | Hiermee wordt de call back-URL opgehaald voor schema-inhoud in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/schemas/lezen | Hiermee leest u het schema in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/schema's/schrijven | Hiermee wordt het schema in het integratie account gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Logic/integrationAccounts/sessies/verwijderen | Hiermee verwijdert u de sessie in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/sessies/lezen | Hiermee leest u de batch configuratie in het integratie account. |
> | Bewerking | Micro soft. Logic/integrationAccounts/sessies/schrijven | Hiermee wordt de sessie in het integratie account gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Logic/integrationAccounts/schrijven | Hiermee wordt het integratie account gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Logic/integrationServiceEnvironments/verwijderen | Hiermee verwijdert u de integratie service omgeving. |
> | Bewerking | Micro soft. Logic/integrationServiceEnvironments/samen voegen/actie | Voegt de Integratieserviceomgeving toe. |
> | Bewerking | Micro soft. Logic/integrationServiceEnvironments/Beheerdeapi's/apiOperations/lezen | Hiermee wordt de beheerde API-bewerking van de Integration service Environment gelezen. |
> | Bewerking | Micro soft. Logic/integrationServiceEnvironments/Beheerdeapi's/lezen | Hiermee wordt de beheerde API van de Integration service Environment gelezen. |
> | Bewerking | Micro soft. Logic/integrationServiceEnvironments/operationStatuses/lezen | Hiermee wordt de bewerkings status van de integratie service omgeving gelezen. |
> | Bewerking | Micro soft. Logic/integrationServiceEnvironments/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities van de integratie service omgeving gelezen. |
> | Bewerking | Micro soft. Logic/integrationServiceEnvironments/lezen | Hiermee wordt de integratie service omgeving gelezen. |
> | Bewerking | Micro soft. Logic/integrationServiceEnvironments/schrijven | Hiermee wordt de integratie service omgeving gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Logic/locaties/werk stromen/recommendOperationGroups/actie | Hiermee haalt u de werk stroom aanbevolen bewerkings groepen. |
> | Bewerking | Micro soft. Logic/locaties/werk stromen/valideren/actie | Valideert de werk stroom. |
> | Bewerking | Micro soft. Logic/Operations/lezen | Hiermee wordt de bewerking opgehaald. |
> | Bewerking | Micro soft. Logic/registreren/actie | Hiermee wordt de resource provider van micro soft. Logic geregistreerd voor een bepaald abonnement. |
> | Bewerking | Micro soft. Logic/werk stromen/accessKey/verwijderen | Hiermee verwijdert u de toegangs sleutel. |
> | Bewerking | Micro soft. Logic/werk stromen/accessKeyen/lijst/actie | Geeft een lijst van de toegangs sleutel geheimen. |
> | Bewerking | Micro soft. Logic/werk stromen/accessKey/lezen | Hiermee wordt de toegangs sleutel gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/accessKeyen/opnieuw genereren/actie | Hiermee worden de toegangs sleutel geheimen opnieuw gegenereerd. |
> | Bewerking | Micro soft. Logic/werk stromen/accessKeys/schrijven | Hiermee wordt de toegangs sleutel gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Logic/werk stromen/verwijderen | Hiermee verwijdert u de werk stroom. |
> | Bewerking | Micro soft. Logic/werk stromen/uitschakelen/actie | Hiermee schakelt u de werk stroom uit. |
> | Bewerking | Micro soft. Logic/werk stromen/inschakelen/actie | Hiermee wordt de werk stroom ingeschakeld. |
> | Bewerking | Micro soft. Logic/werk stromen/listCallbackUrl/actie | Hiermee wordt de call back-URL voor de werk stroom opgehaald. |
> | Bewerking | Micro soft. Logic/werk stromen/listSwagger/actie | Hiermee worden de Swagger-definities van de werk stroom opgehaald. |
> | Bewerking | Micro soft. Logic/werk stromen/verplaatsen/actie | Verplaatst de werk stroom van de bestaande abonnements-id, resource groep en/of naam naar een andere abonnements-id, resource groep en/of naam. |
> | Bewerking | Micro soft. Logic/werk stromen/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee worden de diagnostische instellingen van de werk stroom gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling van de werk stroom gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Logic/werk stromen/providers/micro soft. Insights/logDefinitions/lezen | Hiermee worden de definities van werk stroom logboeken gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de metrische definities van de werk stroom gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/lezen | Hiermee wordt de werk stroom gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/regenerateAccessKey/actie | Hiermee worden de toegangs sleutel geheimen opnieuw gegenereerd. |
> | Bewerking | Micro soft. Logic/werk stromen/uitvoeren/actie | Start een uitvoering van de werk stroom. |
> | Bewerking | Micro soft. Logic/werk stromen/uitvoeringen/acties/listExpressionTraces/actie | Hiermee worden de expressie traceringen voor de uitvoerings actie van de werk stroom opgehaald. |
> | Bewerking | Micro soft. Logic/werk stromen/uitvoeringen/acties/lezen | Hiermee wordt de uitvoerings actie van de werk stroom gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/uitvoeringen/acties/herhalingen/listExpressionTraces/actie | Hiermee worden de expressie traceringen voor de herhaling van de uitvoerings actie van de werk stroom opgehaald. |
> | Bewerking | Micro soft. Logic/werk stromen/uitvoeringen/acties/herhalingen/lezen | Hiermee wordt de herhaling van de uitvoerings actie van de werk stroom gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/uitvoeringen/acties/herhalingen/requestHistories/lezen | Hiermee wordt de aanvraag geschiedenis van de herhalings actie voor het uitvoeren van de werk stroom gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/uitvoeringen/acties/requestHistories/lezen | Hiermee wordt de aanvraag geschiedenis van de actie werk stroom uitvoeren gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/uitvoeringen/acties/scoperepetitions/lezen | Hiermee wordt de herhaling van het uitvoerings actie bereik van de werk stroom gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/uitvoeringen/annuleren/actie | Annuleert de uitvoering van een werk stroom. |
> | Bewerking | Micro soft. Logic/werk stromen/uitvoeringen/verwijderen | Hiermee verwijdert u een uitvoering van een werk stroom. |
> | Bewerking | Micro soft. Logic/werk stromen/uitvoeringen/bewerkingen/lezen | Hiermee wordt de status van de uitvoerings bewerking van de werk stroom gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/uitvoeringen/lezen | Hiermee wordt de uitvoering van de werk stroom gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/onderbreken/actie | Hiermee wordt de werk stroom onderbroken. |
> | Bewerking | Micro soft. Logic/werk stromen/triggers/geschiedenis/lezen | Hiermee leest u de trigger geschiedenis. |
> | Bewerking | Micro soft. Logic/werk stromen/triggers/geschiedenis/opnieuw verzenden/actie | Hiermee wordt de werk stroom trigger opnieuw verzonden. |
> | Bewerking | Micro soft. Logic/werk stromen/triggers/listCallbackUrl/actie | Hiermee wordt de call back-URL opgehaald voor de trigger. |
> | Bewerking | Micro soft. Logic/werk stromen/triggers/lezen | Hiermee leest u de trigger. |
> | Bewerking | Micro soft. Logic/werk stromen/triggers/opnieuw instellen/actie | Hiermee wordt de trigger opnieuw ingesteld. |
> | Bewerking | Micro soft. Logic/werk stromen/triggers/uitvoeren/actie | De trigger wordt uitgevoerd. |
> | Bewerking | Micro soft. Logic/werk stromen/triggers/setState/actie | Hiermee stelt u de status van de trigger in. |
> | Bewerking | Micro soft. Logic/werk stromen/valideren/actie | Valideert de werk stroom. |
> | Bewerking | Micro soft. Logic/werk stromen/versies/lezen | Hiermee wordt de werk stroom versie gelezen. |
> | Bewerking | Micro soft. Logic/werk stromen/versies/triggers/listCallbackUrl/actie | Hiermee wordt de call back-URL opgehaald voor de trigger. |
> | Bewerking | Micro soft. Logic/werk stromen/schrijven | Hiermee wordt de werk stroom gemaakt of bijgewerkt. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. MachineLearning/commitmentPlans/commitmentAssociations/Move/Action | Een Machine Learning Toezeggings plan koppeling verplaatsen |
> | Bewerking | Micro soft. MachineLearning/commitmentPlans/commitmentAssociations/lezen | Een Machine Learning Toezeggings plan koppeling lezen |
> | Bewerking | Micro soft. MachineLearning/commitmentPlans/verwijderen | Een Machine Learning Toezeggings plan verwijderen |
> | Bewerking | Micro soft. MachineLearning/commitmentPlans/deelname/actie | Een Machine Learning Toezeggings plan samen voegen |
> | Bewerking | Micro soft. MachineLearning/commitmentPlans/lezen | Een Machine Learning Toezeggings plan lezen |
> | Bewerking | Micro soft. MachineLearning/commitmentPlans/schrijven | Een Machine Learning Toezeggings plan maken of bijwerken |
> | Bewerking | Micro soft. MachineLearning/locaties/operationresults/lezen | Resultaat van een Machine Learning bewerking ophalen |
> | Bewerking | Micro soft. MachineLearning/locaties/operationsstatus/lezen | De status van een doorlopende Machine Learning bewerking ophalen |
> | Bewerking | Micro soft. MachineLearning/Operations/lezen | Machine Learning-bewerkingen ophalen |
> | Bewerking | Micro soft. MachineLearning/REGI ster/actie | Hiermee wordt het abonnement voor de resource provider van de machine learning-webservice geregistreerd en wordt het maken van webservices ingeschakeld. |
> | Bewerking | Micro soft. MachineLearning/sku's/lezen | Sku's van Machine Learning Toezeggings plan ophalen |
> | Bewerking | Micro soft. MachineLearning/webservices/actie | Eigenschappen van regionale webservice maken voor ondersteunde regio's |
> | Bewerking | Micro soft. MachineLearning/webservices/verwijderen | Alle Machine Learning-webservice verwijderen |
> | Bewerking | Micro soft. MachineLearning/webservices/listkeys ophalen/lezen | Sleutels naar een Machine Learning-webservice ophalen |
> | Bewerking | Micro soft. MachineLearning/webservices/lezen | Alle Machine Learning-webservice lezen |
> | Bewerking | Micro soft. MachineLearning/webservices/schrijven | Een Machine Learning-webservice maken of bijwerken |
> | Bewerking | Micro soft. MachineLearning/werk ruimten/verwijderen | Machine Learning-werkruimte verwijderen |
> | Bewerking | Micro soft. MachineLearning/werk ruimten/listworkspacekeys/actie | Lijst met sleutels voor een Machine Learning-werkruimte |
> | Bewerking | Micro soft. MachineLearning/werk ruimten/lezen | Alle Machine Learning-werkruimte lezen |
> | Bewerking | Micro soft. MachineLearning/werk ruimten/resyncstoragekeys/actie | Sleutels van het opslag account die zijn geconfigureerd voor een Machine Learning-werkruimte, worden opnieuw gesynchroniseerd |
> | Bewerking | Micro soft. MachineLearning/werk ruimten/schrijven | Machine Learning-werkruimte maken of bijwerken |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. MachineLearningServices/locaties/computeoperationsstatus/lezen | Hiermee wordt de status van een bepaalde reken bewerking opgehaald |
> | Bewerking | Micro soft. MachineLearningServices/locaties/gebruik/lezen | Gebruiks rapport voor AML-reken resources in een abonnement |
> | Bewerking | Micro soft. MachineLearningServices/locaties/toegestane VM/lezen | Ondersteunde VM-grootten ophalen |
> | Bewerking | Micro soft. MachineLearningServices/locaties/workspaceOperationsStatus/lezen | Hiermee wordt de status van een bepaalde werkruimte bewerking opgehaald |
> | Bewerking | Micro soft. MachineLearningServices/REGI ster/actie | Hiermee wordt het abonnement voor de Machine Learning Services resource provider geregistreerd |
> | Bewerking | Micro soft. MachineLearningServices/werk ruimten/berekenen/verwijderen | Hiermee worden de reken resources in Machine Learning Services werk ruimte (n) verwijderd |
> | Bewerking | Micro soft. MachineLearningServices/werk ruimten/berekeningen/Listkeys ophalen/actie | Geheimen vermelden voor reken resources in Machine Learning Services werk ruimte |
> | Bewerking | Micro soft. MachineLearningServices/werk ruimten/berekeningen/listNodes/actie | Knoop punten voor Compute-resource in Machine Learning Services werk ruimte weer geven |
> | Bewerking | Micro soft. MachineLearningServices/werk ruimten/reken-en lees bewerkingen | Hiermee worden de reken resources in Machine Learning Services werk ruimte (n) opgehaald |
> | Bewerking | Micro soft. MachineLearningServices/werk ruimten/reken kracht/schrijven | Hiermee worden de reken resources in Machine Learning Services werk ruimte (n) gemaakt of bijgewerkt |
> | Bewerking | Micro soft. MachineLearningServices/werk ruimten/verwijderen | Hiermee verwijdert u de Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/experimenten/verwijderen | Hiermee verwijdert u experimenten in Machine Learning Services werk ruimte (n) |
> | DataAction | Micro soft. MachineLearningServices/werk ruimtes/experimenten/lezen | Hiermee worden experimenten in Machine Learning Services werk ruimte (n) opgehaald |
> | DataAction | Micro soft. MachineLearningServices/werk ruimten/experimenten/schrijven | Hiermee worden experimenten in Machine Learning Services werk ruimte (n) gemaakt of bijgewerkt |
> | Bewerking | Micro soft. MachineLearningServices/werk ruimten/Listkeys ophalen/actie | Geheimen voor een Machine Learning Services-werk ruimte vermelden |
> | Bewerking | Micro soft. MachineLearningServices/werk ruimten/lezen | Hiermee worden de Machine Learning Services werk ruimte (n) opgehaald |
> | Bewerking | Micro soft. MachineLearningServices/werk ruimten/schrijven | Hiermee wordt een Machine Learning Services werk ruimte (n) gemaakt of bijgewerkt |

## <a name="microsoftmanagedidentity"></a>Micro soft. ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ManagedIdentity/Identities/lezen | Hiermee wordt een bestaande systeem-id opgehaald |
> | Bewerking | Micro soft. ManagedIdentity/REGI ster/actie | Hiermee wordt het abonnement voor de resource provider van de beheerde identiteit geregistreerd |
> | Bewerking | Micro soft. ManagedIdentity/userAssignedIdentities/Assign/Action | RBAC-actie voor het toewijzen van een bestaande door de gebruiker toegewezen identiteit aan een resource |
> | Bewerking | Micro soft. ManagedIdentity/userAssignedIdentities/verwijderen | Hiermee wordt een bestaande door de gebruiker toegewezen identiteit verwijderd |
> | Bewerking | Micro soft. ManagedIdentity/userAssignedIdentities/lezen | Hiermee wordt een bestaande door de gebruiker toegewezen identiteit opgehaald |
> | Bewerking | Micro soft. ManagedIdentity/userAssignedIdentities/schrijven | Hiermee wordt een nieuwe door de gebruiker toegewezen identiteit gemaakt of worden de labels bijgewerkt die zijn gekoppeld aan een bestaande door de gebruiker toegewezen identiteit |

## <a name="microsoftmanagedservices"></a>Micro soft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ManagedServices/marketplaceRegistrationDefinitions/lezen | Hiermee haalt u een lijst met definities voor beheerde services registratie op. |
> | Bewerking | Micro soft. ManagedServices/Operations/lezen | Hiermee wordt een lijst met beheerde services-bewerkingen opgehaald. |
> | Bewerking | Micro soft. ManagedServices/operationStatuses/lezen | Hiermee wordt de bewerkings status van de resource gelezen. |
> | Bewerking | Micro soft. ManagedServices/REGI ster/actie | Meld u aan bij beheerde services. |
> | Bewerking | Micro soft. ManagedServices/registrationAssignments/verwijderen | Hiermee wordt de registratie toewijzing van beheerde services verwijderd. |
> | Bewerking | Micro soft. ManagedServices/registrationAssignments/lezen | Hiermee wordt een lijst met beheerde services registratie toewijzingen opgehaald. |
> | Bewerking | Micro soft. ManagedServices/registrationAssignments/schrijven | De registratie toewijzing voor beheerde services toevoegen of wijzigen. |
> | Bewerking | Micro soft. ManagedServices/registrationDefinitions/verwijderen | Hiermee wordt de registratie definitie van beheerde services verwijderd. |
> | Bewerking | Micro soft. ManagedServices/registrationDefinitions/lezen | Hiermee haalt u een lijst met definities voor beheerde services registratie op. |
> | Bewerking | Micro soft. ManagedServices/registrationDefinitions/schrijven | De registratie definitie voor beheerde services toevoegen of wijzigen. |
> | Bewerking | Micro soft. ManagedServices/registratie/actie | Registratie bij beheerde services opheffen. |

## <a name="microsoftmanagement"></a>Micro soft. Management

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Management/checkNameAvailability/actie | Hiermee wordt gecontroleerd of de opgegeven naam van de beheer groep geldig en uniek is. |
> | Bewerking | Micro soft. Management/getEntities/actie | Alle entiteiten (Beheergroepen, abonnementen enz.) weer geven voor de geverifieerde gebruiker. |
> | Bewerking | Micro soft. Management/managementGroups/verwijderen | Beheer groep verwijderen. |
> | Bewerking | Micro soft. Management/managementGroups/lezen | Beheer groepen voor de geverifieerde gebruiker weer geven. |
> | Bewerking | Micro soft. Management/managementGroups/abonnementen/verwijderen | Het abonnement van de beheer groep wordt ontkoppeld. |
> | Bewerking | Micro soft. Management/managementGroups/abonnementen/schrijven | Het bestaande abonnement wordt gekoppeld aan de beheer groep. |
> | Bewerking | Micro soft. Management/managementGroups/schrijven | Een beheer groep maken of bijwerken. |
> | Bewerking | Micro soft. Management/REGI ster/actie | Het opgegeven abonnement registreren bij micro soft. Management |

## <a name="microsoftmaps"></a>Micro soft. Maps

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | DataAction | Micro soft. Maps/accounts/gegevens/lezen | Hiermee wordt het lezen van gegevens toegang verleend aan een Maps-account. |
> | Bewerking | Micro soft. Maps/accounts/verwijderen | Een Maps-account verwijderen. |
> | Bewerking | Micro soft. Maps/accounts/eventGridFilters/verwijderen | Een Event Grid filter verwijderen |
> | Bewerking | Micro soft. Maps/accounts/eventGridFilters/lezen | Een Event Grid filter ophalen |
> | Bewerking | Micro soft. Maps/accounts/eventGridFilters/schrijven | Een Event Grid filter maken of bijwerken |
> | Bewerking | Micro soft. Maps/accounts/Listkeys ophalen/actie | Lijst met account sleutels voor overzichten |
> | Bewerking | Micro soft. Maps/accounts/lezen | Een Maps-account ophalen. |
> | Bewerking | Micro soft. Maps/accounts/regenerateKey/actie | Nieuwe Maps-account primaire of secundaire sleutel genereren |
> | Bewerking | Micro soft. Maps/accounts/schrijven | Een Maps-account maken of bijwerken. |
> | Bewerking | Micro soft. Maps/Operations/lezen | De provider bewerkingen lezen |
> | Bewerking | Micro soft. Maps/REGI ster/actie | De provider registreren |

## <a name="microsoftmarketplace"></a>Micro soft. Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Marketplace/offerTypes/uitgevers/aanbiedingen/abonnementen/overeenkomsten/lezen | Retourneert een overeenkomst. |
> | Bewerking | Micro soft. Marketplace/offerTypes/uitgevers/aanbiedingen/abonnementen/overeenkomsten/schrijven | Accepteert een ondertekende overeenkomst. |
> | Bewerking | Micro soft. Marketplace/offerTypes/uitgevers/aanbiedingen/abonnementen/configuraties/importImage/actie | Hiermee importeert u een installatie kopie naar de ACR van de eind gebruiker. |
> | Bewerking | Micro soft. Marketplace/offerTypes/uitgevers/aanbiedingen/plannen/configuratie/lezen | Retourneert een configuratie. |
> | Bewerking | Micro soft. Marketplace/offerTypes/uitgevers/aanbiedingen/plannen/configuratie/schrijven | Hiermee wordt een configuratie opgeslagen. |
> | Bewerking | Micro soft. Marketplace/registreren/actie | Hiermee wordt de resource provider micro soft. Marketplace geregistreerd in het abonnement. |

## <a name="microsoftmarketplaceapps"></a>Micro soft. MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. MarketplaceApps/ClassicDevServices/verwijderen | Er wordt een Verwijder bewerking uitgevoerd op een klassieke dev service-resource. |
> | Bewerking | Micro soft. MarketplaceApps/ClassicDevServices/listSecrets/Action | Hiermee haalt u een service voor het beheer van bron-en klassieke resources. |
> | Bewerking | Micro soft. MarketplaceApps/ClassicDevServices/listSingleSignOnToken/Action | Hiermee wordt de URL voor eenmalige aanmelding voor een klassieke dev service opgehaald. |
> | Bewerking | Micro soft. MarketplaceApps/ClassicDevServices/lezen | Voert een GET-bewerking uit op een klassieke dev service. |
> | Bewerking | Micro soft. MarketplaceApps/ClassicDevServices/regenerateKey/Action | Hiermee genereert u een klassieke service voor het beheren van resource beheer sleutels. |
> | Bewerking | Micro soft. MarketplaceApps/Operations/lezen | Lees de bewerkingen voor alle resource typen. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. MarketplaceOrdering/overeenkomsten/aanbiedingen/abonnementen/annuleren/actie | Een overeenkomst voor een bepaald Marketplace-item annuleren |
> | Bewerking | Micro soft. MarketplaceOrdering/overeenkomsten/aanbiedingen/plannen/lezen | Een overeenkomst voor een gegeven Marketplace-item retour neren |
> | Bewerking | Micro soft. MarketplaceOrdering/overeenkomsten/aanbiedingen/abonnementen/ondertekening/actie | Een overeenkomst voor een bepaald Marketplace-item ondertekenen |
> | Bewerking | Micro soft. MarketplaceOrdering/overeenkomsten/lezen | Alle overeenkomsten voor het opgegeven abonnement retour neren |
> | Bewerking | Micro soft. MarketplaceOrdering/offertypes/Publishers/aanbiedingen/plannen/overeenkomsten/lezen | Een overeenkomst voor een bepaald item van een virtuele Marketplace-machine verkrijgen |
> | Bewerking | Micro soft. MarketplaceOrdering/offertypes/Publishers/aanbiedingen/abonnementen/overeenkomsten/schrijven | Een overeenkomst voor een bepaald item van een virtuele Marketplace-machine ondertekenen of annuleren |
> | Bewerking | Micro soft. MarketplaceOrdering/Operations/lezen | Alle mogelijke bewerkingen in de API weer geven |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Media/checknameavailability/actie | Hiermee wordt gecontroleerd of een Media Services account naam beschikbaar is |
> | Bewerking | Micro soft. media/locaties/checkNameAvailability/actie | Hiermee wordt gecontroleerd of een Media Services account naam beschikbaar is |
> | Bewerking | Micro soft. Media/Media Services/accountfilters/verwijderen | Een account filter verwijderen |
> | Bewerking | Micro soft. Media/Media Services/accountfilters/lezen | Een account filter lezen |
> | Bewerking | Micro soft. Media/Media Services/accountfilters/schrijven | Een account filter maken of bijwerken |
> | Bewerking | Micro soft. Media/Media Services/assets/assetfilters/verwijderen | Alle activa filters verwijderen |
> | Bewerking | Micro soft. Media/Media Services/assets/assetfilters/lezen | Alle activa filters lezen |
> | Bewerking | Micro soft. Media/Media Services/assets/assetfilters/schrijven | Een Asset-filter maken of bijwerken |
> | Bewerking | Micro soft. Media/Media Services/assets/verwijderen | Alle activa verwijderen |
> | Bewerking | Micro soft. Media/Media Services/assets/getEncryptionKey/actie | Versleutelings sleutel voor activa ophalen |
> | Bewerking | Micro soft. Media/Media Services/assets/listContainerSas/actie | SAS-Url's voor de Asset-container vermelden |
> | Bewerking | Micro soft. Media/Media Services/assets/listStreamingLocators/actie | Streaming-Locators vermelden voor Asset |
> | Bewerking | Micro soft. Media/Media Services/assets/lezen | Alle activa lezen |
> | Bewerking | Micro soft. Media/Media Services/assets/schrijven | Activa maken of bijwerken |
> | Bewerking | Micro soft. Media/Media Services/contentKeyPolicies/verwijderen | Alle beleids regels voor inhouds sleutels verwijderen |
> | Bewerking | Micro soft. Media/Media Services/contentKeyPolicies/getPolicyPropertiesWithSecrets/actie | Beleids eigenschappen ophalen met geheimen |
> | Bewerking | Micro soft. Media/Media Services/contentKeyPolicies/lezen | Alle beleids regels voor inhouds sleutels lezen |
> | Bewerking | Micro soft. Media/Media Services/contentKeyPolicies/schrijven | Beleid voor inhouds sleutels maken of bijwerken |
> | Bewerking | Micro soft. Media/Media Services/verwijderen | Een Media Services account verwijderen |
> | Bewerking | Micro soft. Media/Media Services/eventGridFilters/verwijderen | Event Grid filter verwijderen |
> | Bewerking | Micro soft. Media/Media Services/eventGridFilters/lezen | Een Event Grid filter lezen |
> | Bewerking | Micro soft. Media/Media Services/eventGridFilters/schrijven | Event Grid filter maken of bijwerken |
> | Bewerking | Micro soft. Media/Media Services/liveEventOperations/lezen | Een live gebeurtenis bewerking lezen |
> | Bewerking | Micro soft. Media/Media Services/liveEvents/verwijderen | Een live gebeurtenis verwijderen |
> | Bewerking | Micro soft. Media/Media Services/liveEvents/liveOutputs/verwijderen | Een live uitvoer verwijderen |
> | Bewerking | Micro soft. Media/Media Services/liveEvents/liveOutputs/lezen | Een wille keurige live-uitvoer lezen |
> | Bewerking | Micro soft. Media/Media Services/liveEvents/liveOutputs/schrijven | Een live-uitvoer maken of bijwerken |
> | Bewerking | Micro soft. Media/Media Services/liveEvents/lezen | Een live gebeurtenis lezen |
> | Bewerking | Micro soft. Media/Media Services/liveEvents/reset/Action | Een live gebeurtenis bewerking opnieuw instellen |
> | Bewerking | Micro soft. Media/Media Services/liveEvents/start/actie | Een live gebeurtenis bewerking starten |
> | Bewerking | Micro soft. Media/Media Services/liveEvents/stop/actie | Een live gebeurtenis bewerking stoppen |
> | Bewerking | Micro soft. Media/Media Services/liveEvents/schrijven | Een live gebeurtenis maken of bijwerken |
> | Bewerking | Micro soft. Media/Media Services/liveOutputOperations/lezen | Een live uitvoer bewerking lezen |
> | Bewerking | Micro soft. Media/Media Services/lezen | Een Media Services account lezen |
> | Bewerking | Micro soft. Media/Media Services/streamingEndpointOperations/lezen | Een streaming-eindpunt bewerking lezen |
> | Bewerking | Micro soft. Media/Media Services/streamingEndpoints/verwijderen | Een streaming-eind punt verwijderen |
> | Bewerking | Micro soft. Media/Media Services/streamingEndpoints/lezen | Een streaming-eind punt lezen |
> | Bewerking | Micro soft. Media/Media Services/streamingEndpoints/schalen/actie | Een streaming-eindpunt bewerking schalen |
> | Bewerking | Micro soft. Media/Media Services/streamingEndpoints/start/actie | Een streaming-eindpunt bewerking starten |
> | Bewerking | Micro soft. Media/Media Services/streamingEndpoints/stop/actie | Een streaming-eindpunt bewerking stoppen |
> | Bewerking | Micro soft. Media/Media Services/streamingEndpoints/schrijven | Een streaming-eind punt maken of bijwerken |
> | Bewerking | Micro soft. Media/Media Services/streamingLocators/verwijderen | Een streaming-Locator verwijderen |
> | Bewerking | Micro soft. Media/Media Services/streamingLocators/listContentKeys/actie | Inhouds sleutels weer geven |
> | Bewerking | Micro soft. Media/Media Services/streamingLocators/listPaths/actie | Paden weer geven |
> | Bewerking | Micro soft. Media/Media Services/streamingLocators/lezen | Een streaming-Locator lezen |
> | Bewerking | Micro soft. Media/Media Services/streamingLocators/schrijven | Een streaming-Locator maken of bijwerken |
> | Bewerking | Micro soft. Media/Media Services/streamingPolicies/verwijderen | Een streaming-beleid verwijderen |
> | Bewerking | Micro soft. Media/Media Services/streamingPolicies/lezen | Elk streaming-beleid lezen |
> | Bewerking | Micro soft. Media/Media Services/streamingPolicies/schrijven | Een streaming-beleid maken of bijwerken |
> | Bewerking | Micro soft. Media/Media Services/syncStorageKeys/actie | De opslag sleutels voor een gekoppeld Azure Storage account synchroniseren |
> | Bewerking | Micro soft. Media/Media Services/transformaties/verwijderen | Trans formatie verwijderen |
> | Bewerking | Micro soft. Media/Media Services/transformaties/Jobs/cancelJob/actie | Taak annuleren |
> | Bewerking | Micro soft. Media/Media Services/trans formaties/Jobs/verwijderen | Taak verwijderen |
> | Bewerking | Micro soft. Media/Media Services/transformaties/taken/lezen | Een wille keurige taak lezen |
> | Bewerking | Micro soft. Media/Media Services/transformaties/taken/schrijven | Een taak maken of bijwerken |
> | Bewerking | Micro soft. Media/Media Services/transformaties/lezen | Een trans formatie lezen |
> | Bewerking | Micro soft. Media/Media Services/transformaties/schrijven | Een trans formatie maken of bijwerken |
> | Bewerking | Micro soft. Media/Media Services/schrijven | Een Media Services-account maken of bijwerken |
> | Bewerking | Micro soft. Media/Operations/lezen | Beschik bare bewerkingen ophalen |
> | Bewerking | Micro soft. Media/REGI ster/actie | Hiermee wordt het abonnement voor de Media Services resource provider geregistreerd en wordt het maken van Media Services accounts mogelijk |
> | Bewerking | Micro soft. media/niet registreren/actie | Hiermee wordt de registratie van het abonnement voor de Media Services resource provider ongedaan gemaakt |

## <a name="microsoftmigrate"></a>Micro soft. migrate

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. migrate/assessmentprojects/assessmentOptions/Read | Hiermee worden de evaluatie opties opgehaald die beschikbaar zijn op de opgegeven locatie |
> | Bewerking | Micro soft. migrate/assessmentprojects/beoordelingen/lezen | Een lijst met evaluaties binnen een project |
> | Bewerking | Micro soft. migrate/assessmentprojects/Delete | Hiermee wordt het evaluatie project verwijderd |
> | Bewerking | Micro soft. migrate/assessmentprojects/groups/beoordelingen/assessedmachines/lezen | De eigenschappen van een geschatte machine ophalen |
> | Bewerking | Micro soft. migrate/assessmentprojects/groepen/Beoordelingen/verwijderen | Hiermee wordt een evaluatie verwijderd |
> | Bewerking | Micro soft. migrate/assessmentprojects/groepen/beoordelingen/DownloadURL/actie | Hiermee wordt de URL van een evaluatie rapport gedownload |
> | Bewerking | Micro soft. migrate/assessmentprojects/groepen/beoordelingen/lezen | Hiermee worden de eigenschappen van een evaluatie opgehaald |
> | Bewerking | Micro soft. migrate/assessmentprojects/groepen/beoordelingen/schrijven | Hiermee wordt een nieuwe evaluatie gemaakt of een bestaande evaluatie bijgewerkt |
> | Bewerking | Micro soft. migrate/assessmentprojects/groepen/verwijderen | Hiermee verwijdert u een groep |
> | Bewerking | Micro soft. migrate/assessmentprojects/groepen/lezen | De eigenschappen van een groep ophalen |
> | Bewerking | Micro soft. migrate/assessmentprojects/groups/updateMachines/Action | Groep bijwerken door computers toe te voegen of te verwijderen |
> | Bewerking | Micro soft. migrate/assessmentprojects/groups/write | Hiermee wordt een nieuwe groep gemaakt of een bestaande groep bijgewerkt |
> | Bewerking | Micro soft. migrate/assessmentprojects/hypervcollectors/Delete | Hiermee verwijdert u de Hyper-Collector |
> | Bewerking | Micro soft. migrate/assessmentprojects/hypervcollectors/Read | Hiermee worden de eigenschappen van Hyper-Collector opgehaald |
> | Bewerking | Micro soft. migrate/assessmentprojects/hypervcollectors/write | Hiermee wordt een nieuwe Hyper-Collector gemaakt of een bestaande Hyper-Collector bijgewerkt |
> | Bewerking | Micro soft. migrate/assessmentprojects/machines/lezen | Hiermee worden de eigenschappen van een machine opgehaald |
> | Bewerking | Micro soft. migrate/assessmentprojects/lezen | Hiermee worden de eigenschappen van het evaluatie project opgehaald |
> | Bewerking | Micro soft. migrate/assessmentprojects/vmwarecollectors/Delete | VMware Collector verwijderen |
> | Bewerking | Micro soft. migrate/assessmentprojects/vmwarecollectors/Read | Hiermee worden de eigenschappen van VMware Collector opgehaald |
> | Bewerking | Micro soft. migrate/assessmentprojects/vmwarecollectors/write | Hiermee wordt een nieuwe VMware Collector gemaakt of een bestaande VMware Collector bijgewerkt |
> | Bewerking | Micro soft. migrate/assessmentprojects/write | Hiermee maakt u een nieuw evaluatie project of werkt u een bestaand evaluatie project bij |
> | Bewerking | Micro soft. migrate/locaties/assessmentOptions/lezen | Hiermee worden de evaluatie opties opgehaald die beschikbaar zijn op de opgegeven locatie |
> | Bewerking | Micro soft. migrate/locations/checknameavailability/Action | Controleert de beschik baarheid van de resource naam voor het gegeven abonnement op de opgegeven locatie |
> | Bewerking | Micro soft. migrate/migrateprojects/DatabaseInstances/Read | Hiermee worden de eigenschappen van een Data Base-exemplaar opgehaald |
> | Bewerking | Micro soft. migrate/migrateprojects/data bases/lezen | Hiermee worden de eigenschappen van een Data Base opgehaald |
> | Bewerking | Micro soft. migrate/migrateprojects/Delete | Hiermee wordt een migratie project verwijderd |
> | Bewerking | Micro soft. migrate/migrateprojects/machines/lezen | Hiermee worden de eigenschappen van een machine opgehaald |
> | Bewerking | Micro soft. migrate/migrateprojects/MigrateEvents/Delete | Hiermee wordt een migratie gebeurtenis verwijderd |
> | Bewerking | Micro soft. migrate/migrateprojects/MigrateEvents/Read | Hiermee worden de eigenschappen van een migratie gebeurtenis opgehaald. |
> | Bewerking | Micro soft. migrate/migrateprojects/lezen | Hiermee worden de eigenschappen van het migratie project opgehaald |
> | Bewerking | Micro soft. migrate/migrateprojects/RefreshSummary/Action | Hiermee wordt het samen vatting van het migratie project vernieuwd |
> | Bewerking | Micro soft. migrate/migrateprojects/registerTool/Action | Hulp programma registreren bij een migratie project |
> | Bewerking | Micro soft. migrate/migrateprojects/Solutions/cleanupData/Action | De oplossings gegevens van het migratie project opschonen |
> | Bewerking | Micro soft. migrate/migrateprojects/Solutions/Delete | Hiermee verwijdert u een migratie project oplossing |
> | Bewerking | Micro soft. migrate/migrateprojects/Solutions/getconfig/Action | Hiermee wordt de configuratie van de project oplossing voor migratie opgehaald |
> | Bewerking | Micro soft. migrate/migrateprojects/Solutions/Read | Hiermee worden de eigenschappen van de project-oplossing migreren opgehaald |
> | Bewerking | Micro soft. migrate/migrateprojects/Solutions/write | Hiermee maakt u een nieuwe oplossing voor het migreren van een project of werkt u een bestaande migratie project oplossing bij |
> | Bewerking | Micro soft. migrate/migrateprojects/write | Hiermee maakt u een nieuw migratie project of werkt u een bestaand migratie project bij |
> | Bewerking | Micro soft. migrate/Operations/Read | Hiermee worden bewerkingen weer gegeven die beschikbaar zijn op micro soft. resource provider migreren |
> | Bewerking | Micro soft. migrate/projects/beoordelingen/lezen | Een lijst met evaluaties binnen een project |
> | Bewerking | Micro soft. migrate/projects/verwijderen | Hiermee wordt het project verwijderd |
> | Bewerking | Micro soft. migrate/projects/groepen/beoordelingen/assessedmachines/lezen | De eigenschappen van een geschatte machine ophalen |
> | Bewerking | Micro soft. migrate/projecten/groepen/Beoordelingen/verwijderen | Hiermee wordt een evaluatie verwijderd |
> | Bewerking | Micro soft. migrate/projects/groepen/beoordelingen/DownloadURL/actie | Hiermee wordt de URL van een evaluatie rapport gedownload |
> | Bewerking | Micro soft. migrate/projects/groepen/beoordelingen/lezen | Hiermee worden de eigenschappen van een evaluatie opgehaald |
> | Bewerking | Micro soft. migrate/projects/groepen/beoordelingen/schrijven | Hiermee wordt een nieuwe evaluatie gemaakt of een bestaande evaluatie bijgewerkt |
> | Bewerking | Micro soft. migrate/projecten/groepen/verwijderen | Hiermee verwijdert u een groep |
> | Bewerking | Micro soft. migrate/project/groepen/lezen | De eigenschappen van een groep ophalen |
> | Bewerking | Micro soft. migrate/projects/groepen/schrijven | Hiermee wordt een nieuwe groep gemaakt of een bestaande groep bijgewerkt |
> | Bewerking | Micro soft. migrate/projects/sleutels/actie | Hiermee worden gedeelde sleutels voor het project opgehaald |
> | Bewerking | Micro soft. migrate/projectes/machines/lezen | Hiermee worden de eigenschappen van een machine opgehaald |
> | Bewerking | Micro soft. migrate/projects/lezen | Hiermee worden de eigenschappen van een project opgehaald |
> | Bewerking | Micro soft. migrate/projecten/schrijven | Hiermee wordt een nieuw project gemaakt of een bestaand project bijgewerkt |
> | Bewerking | Micro soft. migrate/REGI ster/actie | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. migrate |

## <a name="microsoftmixedreality"></a>Micro soft. MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. MixedReality/REGI ster/actie | Registreert een abonnement voor de resource provider Mixed Reality. |
> | DataAction | Micro soft. MixedReality/SpatialAnchorsAccounts/Create/Action | Ruimtelijke ankers maken |
> | DataAction | Micro soft. MixedReality/SpatialAnchorsAccounts/verwijderen | Ruimtelijke ankers verwijderen |
> | DataAction | Micro soft. MixedReality/SpatialAnchorsAccounts/detectie/lezen | Ruimtelijke beankeringen in de buurt detecteren |
> | DataAction | Micro soft. MixedReality/SpatialAnchorsAccounts/eigenschappen/lezen | Eigenschappen van ruimtelijke ankers ophalen |
> | Bewerking | Micro soft. MixedReality/spatialAnchorsAccounts/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor micro soft. MixedReality/spatialAnchorsAccounts opgehaald |
> | Bewerking | Micro soft. MixedReality/spatialAnchorsAccounts/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor micro soft. MixedReality/spatialAnchorsAccounts gemaakt of bijgewerkt |
> | Bewerking | Micro soft. MixedReality/spatialAnchorsAccounts/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor micro soft. MixedReality/spatialAnchorsAccounts |
> | DataAction | Micro soft. MixedReality/SpatialAnchorsAccounts/query/lezen | Ruimtelijke ankers zoeken |
> | DataAction | Micro soft. MixedReality/SpatialAnchorsAccounts/submitdiag/lezen | Diagnostische gegevens verzenden om de kwaliteit van de Azure spatiale ankers-service te verbeteren |
> | DataAction | Micro soft. MixedReality/SpatialAnchorsAccounts/schrijven | Eigenschappen van ruimtelijke ankers bijwerken |

## <a name="microsoftnetapp"></a>Micro soft. NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. NetApp/locaties/checkfilepathavailability/actie | Controleren of het bestandspad beschikbaar is |
> | Bewerking | Micro soft. NetApp/locaties/checknameavailability/actie | Controleren of de resource naam beschikbaar is |
> | Bewerking | Micro soft. NetApp/locaties/operationresults/lezen | Hiermee wordt een resource van een bewerkings resultaat gelezen. |
> | Bewerking | Micro soft. NetApp/locaties/lezen | Hiermee wordt een resource voor beschikbaarheids controle gelezen. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/capacityPools/verwijderen | Hiermee verwijdert u een groeps resource. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/capacityPools/lezen | Hiermee wordt een pool resource gelezen. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/verwijderen | Hiermee verwijdert u een volume resource. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/mountTargets/lezen | Hiermee wordt een bron van het doel van een koppeling gelezen. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/lezen | Hiermee wordt een volume bron gelezen. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/moment opnamen/verwijderen | Hiermee verwijdert u een momentopname resource. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/moment opnamen/lezen | Hiermee wordt een momentopname resource gelezen. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/moment opnamen/schrijven | Schrijft een momentopname resource. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/capacityPools/volumes/schrijven | Schrijft een volume resource. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/capacityPools/write | Schrijft een pool resource. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/verwijderen | Hiermee verwijdert u een account bron. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/lezen | Hiermee wordt een account resource gelezen. |
> | Bewerking | Micro soft. NetApp/netAppAccounts/schrijven | Schrijft een account bron. |
> | Bewerking | Micro soft. NetApp/Operations/lezen | Hiermee worden een bewerkings resources gelezen. |
> | Bewerking | Micro soft. NetApp/REGI ster/actie | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. NetApp |
> | Bewerking | Micro soft. NetApp/registratie/actie | Registratie van het abonnement met de resource provider micro soft. NetApp is ongedaan gemaakt |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Network/applicationGatewayAvailableRequestHeaders/lezen | Application Gateway beschik bare aanvraag headers ophalen |
> | Bewerking | Micro soft. Network/applicationGatewayAvailableResponseHeaders/lezen | Application Gateway beschik bare reactie header ophalen |
> | Bewerking | Micro soft. Network/applicationGatewayAvailableServerVariables/lezen | Application Gateway beschik bare Server variabelen ophalen |
> | Bewerking | Micro soft. Network/applicationGatewayAvailableSslOptions/predefinedPolicies/lezen | Application Gateway vooraf gedefinieerd SSL-beleid |
> | Bewerking | Micro soft. Network/applicationGatewayAvailableSslOptions/lezen | Beschik bare SSL-opties Application Gateway |
> | Bewerking | Micro soft. Network/applicationGatewayAvailableWafRuleSets/lezen | Hiermee worden Application Gateway beschik bare WAF-regel sets opgehaald |
> | Bewerking | Micro soft. Network/applicationGateways/backendAddressPools/samen voegen/actie | Voegt een back-end-adres groep van een toepassings gateway samen. Niet Alertable. |
> | Bewerking | Micro soft. Network/applicationGateways/backendhealth/Action | Hiermee wordt een back-upstatus van de toepassings gateway opgehaald |
> | Bewerking | Micro soft. Network/applicationGateways/verwijderen | Hiermee verwijdert u een toepassings gateway |
> | Bewerking | Micro soft. Network/applicationGateways/getBackendHealthOnDemand/Action | Hiermee wordt een back-end van de toepassings gateway opgehaald voor de opgegeven http-instelling en back-end-pool |
> | Bewerking | Micro soft. Network/applicationGateways/lezen | Hiermee wordt een toepassings gateway opgehaald |
> | Bewerking | Micro soft. Network/applicationGateways/start/Action | Start een toepassings gateway |
> | Bewerking | Micro soft. Network/applicationGateways/stop/Action | Stopt een toepassings gateway |
> | Bewerking | Micro soft. Network/applicationGateways/schrijven | Hiermee wordt een toepassings gateway gemaakt of een toepassings gateway bijgewerkt |
> | Bewerking | Micro soft. Network/ApplicationGatewayWebApplicationFirewallPolicies/verwijderen | Hiermee wordt een Application Gateway WAF-beleid verwijderd |
> | Bewerking | Micro soft. Network/ApplicationGatewayWebApplicationFirewallPolicies/lezen | Hiermee wordt een Application Gateway WAF-beleid opgehaald |
> | Bewerking | Micro soft. Network/ApplicationGatewayWebApplicationFirewallPolicies/schrijven | Hiermee maakt u een Application Gateway WAF-beleid of werkt u een Application Gateway WAF-beleid bij |
> | Bewerking | Micro soft. Network/applicationSecurityGroups/verwijderen | Hiermee verwijdert u een toepassings beveiligings groep |
> | Bewerking | Micro soft. Network/applicationSecurityGroups/joinIpConfiguration/Action | Voegt een IP-configuratie toe aan toepassings beveiligings groepen. Niet alertable. |
> | Bewerking | Micro soft. Network/applicationSecurityGroups/joinNetworkSecurityRule/Action | Er wordt een beveiligings regel toegevoegd aan toepassings beveiligings groepen. Niet alertable. |
> | Bewerking | Micro soft. Network/applicationSecurityGroups/listIpConfigurations/Action | Een lijst met IP-configuraties in de ApplicationSecurityGroup |
> | Bewerking | Micro soft. Network/applicationSecurityGroups/lezen | Hiermee wordt een toepassings beveiligings groep-ID opgehaald. |
> | Bewerking | Micro soft. Network/applicationSecurityGroups/schrijven | Hiermee wordt een toepassings beveiligings groep gemaakt of een bestaande toepassings beveiligings groep bijgewerkt. |
> | Bewerking | Micro soft. Network/azureFirewallFqdnTags/lezen | Hiermee worden Azure Firewall FQDN-Tags opgehaald |
> | Bewerking | Micro soft. Network/azurefirewalls/verwijderen | Azure Firewall verwijderen |
> | Bewerking | Micro soft. Network/azurefirewalls/lezen | Azure Firewall ophalen |
> | Bewerking | Micro soft. Network/azurefirewalls/schrijven | Hiermee wordt een Azure Firewall gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/bastionHosts/verwijderen | Hiermee wordt een bastion-host verwijderd |
> | Bewerking | Micro soft. Network/bastionHosts/lezen | Hiermee wordt een bastion-host opgehaald |
> | Bewerking | Micro soft. Network/bastionHosts/schrijven | Een bastion-host maken of bijwerken |
> | Bewerking | Micro soft. Network/bgpServiceCommunities/lezen | BGP-service Community's ophalen |
> | Bewerking | Micro soft. Network/checkFrontDoorNameAvailability/actie | Hiermee wordt gecontroleerd of de naam van de voor deur beschikbaar is |
> | Bewerking | Micro soft. Network/checkTrafficManagerNameAvailability/actie | Controleert de beschik baarheid van een Traffic Manager relatieve DNS-naam. |
> | Bewerking | Micro soft. netwerk/verbindingen/verwijderen | Hiermee worden VirtualNetworkGatewayConnection verwijderd |
> | Bewerking | Micro soft. netwerk/verbindingen/lezen | Hiermee wordt VirtualNetworkGatewayConnection opgehaald |
> | Bewerking | Micro soft. netwerk/verbindingen/intrekken/actie | Hiermee wordt de verbindings status van een Express-route gemarkeerd als ingetrokken |
> | Bewerking | Micro soft. netwerk/verbindingen/sharedkey/actie | VirtualNetworkGatewayConnection SharedKey ophalen |
> | Bewerking | Micro soft. netwerk/verbindingen/sharedKey/lezen | Hiermee wordt VirtualNetworkGatewayConnection SharedKey opgehaald |
> | Bewerking | Micro soft. Network/Connections/sharedKey/write | Hiermee wordt een bestaande VirtualNetworkGatewayConnection-SharedKey gemaakt of bijgewerkt |
> | Bewerking | Micro soft. netwerk/verbindingen/vpndeviceconfigurationscript/actie | Hiermee wordt de configuratie van het VPN-apparaat van VirtualNetworkGatewayConnection |
> | Bewerking | Micro soft. netwerk/verbindingen/schrijven | Hiermee wordt een bestaande VirtualNetworkGatewayConnection gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/ddosCustomPolicies/verwijderen | Hiermee wordt een aangepast DDoS-beleid verwijderd |
> | Bewerking | Micro soft. Network/ddosCustomPolicies/lezen | Hiermee wordt een DDoS-aangepaste definitie van de beleids definitie opgehaald |
> | Bewerking | Micro soft. Network/ddosCustomPolicies/schrijven | Hiermee maakt u een aangepast DDoS-beleid of werkt u een bestaand aangepast DDoS-beleid bij |
> | Bewerking | Micro soft. Network/ddosProtectionPlans/verwijderen | Hiermee verwijdert u een DDoS Protection Plan |
> | Bewerking | Micro soft. Network/ddosProtectionPlans/samen voegen/actie | Voegt een DDoS Protection Plan samen. Niet alertable. |
> | Bewerking | Micro soft. Network/ddosProtectionPlans/lezen | Hiermee wordt een DDoS Protection Plan opgehaald |
> | Bewerking | Micro soft. Network/ddosProtectionPlans/schrijven | Hiermee maakt u een DDoS Protection Plan of werkt u een DDoS Protection-abonnement bij  |
> | Bewerking | Micro soft. Network/dnsoperationresults/lezen | Hiermee worden de resultaten van een DNS-bewerking opgehaald |
> | Bewerking | Micro soft. Network/dnsoperationstatuses/lezen | Hiermee wordt de status van een DNS-bewerking opgehaald  |
> | Bewerking | Micro soft. Network/dnszones/A/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' A ' van een DNS-zone. |
> | Bewerking | Micro soft. Network/dnszones/A/lezen | De recordset van het type ' A ' ophalen in de JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/dnszones/A/schrijven | Een recordset van het type ' A ' maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/dnszones/AAAA/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' AAAA ' uit een DNS-zone. |
> | Bewerking | Micro soft. Network/dnszones/AAAA/lezen | Haal de recordset van het type ' AAAA ' op in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/dnszones/AAAA/schrijven | Een recordset van het type ' AAAA ' maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/dnszones/alle/lezen | Hiermee worden DNS-record sets in verschillende typen opgehaald |
> | Bewerking | Micro soft. Network/dnszones/CAA/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' CAA ' uit een DNS-zone. |
> | Bewerking | Micro soft. Network/dnszones/CAA/lezen | De recordset ophalen van het type ' CAA ', in JSON-indeling. De recordset bevat de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/dnszones/CAA/schrijven | Een recordset van het type ' CAA ' maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/dnszones/CNAME/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' CNAME ' uit een DNS-zone. |
> | Bewerking | Micro soft. Network/dnszones/CNAME/lezen | De recordset van het type CNAME ophalen in de JSON-indeling. De recordset bevat de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/dnszones/CNAME/schrijven | Een recordset van het type CNAME maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/dnszones/verwijderen | Verwijder de DNS-zone in JSON-indeling. De zone-eigenschappen zijn onder andere tags, ETAG, numberOfRecordSets en maxNumberOfRecordSets. |
> | Bewerking | Micro soft. Network/dnszones/MX/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' MX ' uit een DNS-zone. |
> | Bewerking | Micro soft. Network/dnszones/MX/lezen | Haal de recordset van het type ' MX ' op in de JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/dnszones/MX/schrijven | Een recordset van het type ' MX ' maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/dnszones/NS/verwijderen | Hiermee wordt de DNS-recordset van het type NS verwijderd |
> | Bewerking | Micro soft. Network/dnszones/NS/lezen | Hiermee wordt een DNS-recordset van het type NS opgehaald |
> | Bewerking | Micro soft. Network/dnszones/NS/schrijven | Hiermee wordt een DNS-recordset van het type NS gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/dnszones/PTR/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' PTR ' van een DNS-zone. |
> | Bewerking | Micro soft. Network/dnszones/PTR/lezen | Haal de recordset van het type ' PTR ' op in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/dnszones/PTR/schrijven | Een recordset van het type ' PTR ' maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/dnszones/lezen | Haal de DNS-zone op in JSON-indeling. De zone-eigenschappen zijn onder andere tags, ETAG, numberOfRecordSets en maxNumberOfRecordSets. Houd er rekening mee dat met deze opdracht de record sets in de zone niet worden opgehaald. |
> | Bewerking | Micro soft. Network/dnszones/record sets/lezen | Hiermee worden DNS-record sets in verschillende typen opgehaald |
> | Bewerking | Micro soft. Network/dnszones/SOA/lezen | Hiermee wordt een DNS-recordset van het type SOA opgehaald |
> | Bewerking | Micro soft. Network/dnszones/SOA/schrijven | Hiermee wordt een DNS-recordset van het type SOA gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/dnszones/SRV/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' SRV ' uit een DNS-zone. |
> | Bewerking | Micro soft. Network/dnszones/SRV/lezen | De recordset van het type SRV ophalen in de JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/dnszones/SRV/schrijven | Een recordset van het type SRV maken of bijwerken |
> | Bewerking | Micro soft. Network/dnszones/TXT/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' TXT ' uit een DNS-zone. |
> | Bewerking | Micro soft. Network/dnszones/TXT/lezen | Haal de recordset van het type ' TXT ' op in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/dnszones/TXT/schrijven | Een recordset van het type ' TXT ' maken of bijwerken binnen een DNS-zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/dnszones/schrijven | Een DNS-zone binnen een resource groep maken of bijwerken.  Wordt gebruikt om de tags op een DNS-zone resource bij te werken. Houd er rekening mee dat deze opdracht niet kan worden gebruikt om record sets in de zone te maken of bij te werken. |
> | Bewerking | Micro soft. netwerk/expressRouteCircuits/autorisaties/verwijderen | Hiermee verwijdert u een Expressroute-autorisatie |
> | Bewerking | Micro soft. Network/expressRouteCircuits/Authorization/Read | Hiermee wordt een Expressroute-autorisatie opgehaald |
> | Bewerking | Micro soft. Network/expressRouteCircuits/Authorization/write | Hiermee wordt een bestaande Expressroute-autorisatie gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/expressRouteCircuits/verwijderen | Hiermee wordt een Expressroute verwijderd |
> | Bewerking | Micro soft. Network/expressRouteCircuits/samen voegen/actie | Voegt een Express route-Circuit samen. Niet alertable. |
> | Bewerking | Micro soft. Network/expressRouteCircuits/peering/arpTables/lezen | Hiermee wordt een Expressroute-peering-ArpTable opgehaald |
> | Bewerking | Micro soft. Network/expressRouteCircuits/peeringen/verbindingen/verwijderen | Hiermee verwijdert u een Expressroute-verbinding |
> | Bewerking | Micro soft. Network/expressRouteCircuits/peeringen/verbindingen/lezen | Hiermee wordt een Expressroute-verbinding opgehaald |
> | Bewerking | Micro soft. Network/expressRouteCircuits/peering/Connections/write | Hiermee wordt een bestaande Expressroute-verbindings resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/expressRouteCircuits/peering/verwijderen | Hiermee wordt een Expressroute-peering verwijderd |
> | Bewerking | Micro soft. Network/expressRouteCircuits/peering/peerConnections/lezen | Hiermee wordt een verbinding met een peer Express route-Circuit opgehaald |
> | Bewerking | Micro soft. Network/expressRouteCircuits/peering/lezen | Hiermee wordt een Expressroute-peering opgehaald |
> | Bewerking | Micro soft. Network/expressRouteCircuits/peering/routeTables/lezen | Hiermee wordt een Expressroute-peering-RouteTable opgehaald |
> | Bewerking | Micro soft. Network/expressRouteCircuits/peering/routeTablesSummary/lezen | Hiermee wordt een Expressroute-peering RouteTable-samen vatting opgehaald |
> | Bewerking | Micro soft. Network/expressRouteCircuits/peeringen/statistieken/lezen | Hiermee wordt een Expressroute-peering-stat opgehaald |
> | Bewerking | Micro soft. Network/expressRouteCircuits/peering/schrijven | Hiermee wordt een bestaande Expressroute-peering gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/expressRouteCircuits/lezen | Een Expressroute ophalen |
> | Bewerking | Micro soft. Network/expressRouteCircuits/statistieken/lezen | Hiermee wordt een Expressroute-stat opgehaald |
> | Bewerking | Micro soft. Network/expressRouteCircuits/schrijven | Hiermee wordt een bestaande Expressroute gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/expressRouteCrossConnections/samen voegen/actie | Voegt een snelle route-cross-verbinding samen. Niet alertable. |
> | Bewerking | Micro soft. Network/expressRouteCrossConnections/peering/arpTables/lezen | Hiermee wordt een ARP-tabel van een snelle route Kruis verbinding gemaakt |
> | Bewerking | Micro soft. Network/expressRouteCrossConnections/peering/verwijderen | Hiermee wordt een snelle route Kruis verbindings peering verwijderd |
> | Bewerking | Micro soft. Network/expressRouteCrossConnections/peering/lezen | Hiermee wordt een snelle route Kruis verbindings peering opgehaald |
> | Bewerking | Micro soft. Network/expressRouteCrossConnections/peering/routeTables/lezen | Hiermee wordt een route tabel voor de peering van een snelle route Kruis verbinding opgehaald |
> | Bewerking | Micro soft. Network/expressRouteCrossConnections/peering/routeTableSummary/lezen | Hiermee wordt een samen vatting van een routerings tabel met snelle route Kruis verbinding opgehaald |
> | Bewerking | Micro soft. Network/expressRouteCrossConnections/peering/schrijven | Hiermee maakt u een snelle route Kruis verbinding-peering of werkt u een bestaande Express route Kruis verbindings peering bij |
> | Bewerking | Micro soft. Network/expressRouteCrossConnections/lezen | Snelle route-Kruis verbinding verkrijgen |
> | Bewerking | Micro soft. Network/expressRouteGateways/expressRouteConnections/verwijderen | Hiermee verwijdert u een snelle route verbinding |
> | Bewerking | Micro soft. Network/expressRouteGateways/expressRouteConnections/lezen | Hiermee wordt een snelle route verbinding opgehaald |
> | Bewerking | Micro soft. Network/expressRouteGateways/expressRouteConnections/schrijven | Hiermee maakt u een snelle route verbinding of werkt u een bestaande Express route-verbinding bij |
> | Bewerking | Micro soft. Network/expressRouteGateways/samen voegen/actie | Voegt een Express route-gateway samen. Niet alertable. |
> | Bewerking | Micro soft. Network/expressRouteGateways/lezen | Express route-gateway ophalen |
> | Bewerking | Micro soft. Network/expressRoutePorts/verwijderen | Hiermee worden ExpressRoutePorts verwijderd |
> | Bewerking | Micro soft. Network/expressRoutePorts/samen voegen/actie | Snelle route poorten worden toegevoegd. Niet alertable. |
> | Bewerking | Micro soft. Network/expressRoutePorts/links/lezen | Hiermee wordt ExpressRouteLink opgehaald |
> | Bewerking | Micro soft. Network/expressRoutePorts/lezen | Hiermee wordt ExpressRoutePorts opgehaald |
> | Bewerking | Micro soft. Network/expressRoutePorts/schrijven | ExpressRoutePorts maken of bijwerken |
> | Bewerking | Micro soft. Network/expressRoutePortsLocations/lezen | Locaties voor Express route poorten ophalen |
> | Bewerking | Micro soft. Network/expressRouteServiceProviders/lezen | Hiermee worden snelle Route Service providers opgehaald |
> | Bewerking | Micro soft. Network/firewallPolicies/verwijderen | Hiermee wordt een firewall beleid verwijderd |
> | Bewerking | Micro soft. Network/firewallPolicies/samen voegen/actie | Er wordt lid van een firewall-beleid. Niet alertable. |
> | Bewerking | Micro soft. Network/firewallPolicies/lezen | Hiermee wordt een firewall beleid opgehaald |
> | Bewerking | Micro soft. Network/firewallPolicies/ruleGroups/verwijderen | Hiermee wordt een firewall beleidsregel groep verwijderd |
> | Bewerking | Micro soft. Network/firewallPolicies/ruleGroups/lezen | Hiermee wordt een firewall beleidsregel groep opgehaald |
> | Bewerking | Micro soft. Network/firewallPolicies/ruleGroups/schrijven | Hiermee wordt een firewall beleidsregel groep gemaakt of een bestaande firewall beleids regel groep bijgewerkt |
> | Bewerking | Micro soft. Network/firewallPolicies/schrijven | Hiermee wordt een firewall beleid gemaakt of een bestaand firewall beleid bijgewerkt |
> | Bewerking | Micro soft. Network/frontDoors/backendPools/verwijderen | Hiermee verwijdert u een back-end-groep |
> | Bewerking | Micro soft. Network/frontDoors/backendPools/lezen | Hiermee wordt een back-end-pool opgehaald |
> | Bewerking | Micro soft. Network/frontDoors/backendPools/schrijven | Hiermee wordt een back-end-groep gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/frontDoors/verwijderen | Hiermee verwijdert u een voor deur |
> | Bewerking | Micro soft. Network/frontDoors/frontendEndpoints/verwijderen | Hiermee wordt een frontend-eind punt verwijderd |
> | Bewerking | Micro soft. Network/frontDoors/frontendEndpoints/disableHttps/Action | Hiermee wordt HTTPS uitgeschakeld op een frontend-eind punt |
> | Bewerking | Micro soft. Network/frontDoors/frontendEndpoints/enableHttps/Action | HTTPS inschakelen op een frontend-eind punt |
> | Bewerking | Micro soft. Network/frontDoors/frontendEndpoints/lezen | Hiermee wordt een frontend-eind punt opgehaald |
> | Bewerking | Micro soft. Network/frontDoors/frontendEndpoints/schrijven | Hiermee wordt een frontend-eind punt gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/frontDoors/healthProbeSettings/verwijderen | Hiermee worden de instellingen voor de status test verwijderd |
> | Bewerking | Micro soft. Network/frontDoors/healthProbeSettings/lezen | Hiermee worden de status test instellingen opgehaald |
> | Bewerking | Micro soft. Network/frontDoors/healthProbeSettings/schrijven | Hiermee worden instellingen voor de status test gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/frontDoors/loadBalancingSettings/verwijderen | Hiermee worden instellingen voor taak verdeling gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/frontDoors/loadBalancingSettings/lezen | Hiermee worden de instellingen voor taak verdeling opgehaald |
> | Bewerking | Micro soft. Network/frontDoors/loadBalancingSettings/schrijven | Hiermee worden instellingen voor taak verdeling gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/frontDoors/verwijderen/actie | Inhoud in cache verwijderen van een front deur |
> | Bewerking | Micro soft. Network/frontDoors/lezen | Hiermee wordt een voor deur opgehaald |
> | Bewerking | Micro soft. Network/frontDoors/routingRules/verwijderen | Hiermee verwijdert u een routerings regel |
> | Bewerking | Micro soft. Network/frontDoors/routingRules/lezen | Hiermee wordt een routerings regel opgehaald |
> | Bewerking | Micro soft. Network/frontDoors/routingRules/schrijven | Hiermee wordt een routerings regel gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/frontDoors/validateCustomDomain/Action | Hiermee wordt een frontend-eind punt gevalideerd voor een front-deur |
> | Bewerking | Micro soft. Network/frontDoors/schrijven | Hiermee wordt een voor deur gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/frontDoorWebApplicationFirewallManagedRuleSets/lezen | Hiermee worden beheerde regel sets voor Web Application firewall opgehaald |
> | Bewerking | Micro soft. Network/frontDoorWebApplicationFirewallPolicies/verwijderen | Hiermee wordt een firewall beleid voor webtoepassingen verwijderd |
> | Bewerking | Micro soft. Network/frontDoorWebApplicationFirewallPolicies/lezen | Hiermee wordt een Web Application firewall-beleid opgehaald |
> | Bewerking | Micro soft. Network/frontDoorWebApplicationFirewallPolicies/schrijven | Hiermee wordt een firewall beleid voor webtoepassingen gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/loadBalancers/backendAddressPools/samen voegen/actie | Voegt een load balancer back-end-adres groep samen. Niet Alertable. |
> | Bewerking | Micro soft. Network/loadBalancers/backendAddressPools/lezen | Hiermee wordt een load balancer back-end-adres groep definitie opgehaald |
> | Bewerking | Micro soft. Network/loadBalancers/verwijderen | Hiermee verwijdert u een load balancer |
> | Bewerking | Micro soft. Network/loadBalancers/frontendIPConfigurations/samen voegen/actie | Voegt een Load Balancer frontend-IP-configuratie. Niet alertable. |
> | Bewerking | Micro soft. Network/loadBalancers/frontendIPConfigurations/lezen | Hiermee wordt een load balancer frontend-IP-configuratie definitie opgehaald |
> | Bewerking | Micro soft. Network/loadBalancers/inboundNatPools/samen voegen/actie | Voegt een load balancer binnenkomende NAT-pool samen. Niet alertable. |
> | Bewerking | Micro soft. Network/loadBalancers/inboundNatPools/lezen | Hiermee wordt een load balancer binnenkomende NAT-groeps definitie opgehaald |
> | Bewerking | Micro soft. Network/loadBalancers/inboundNatRules/verwijderen | Hiermee wordt een load balancer binnenkomende NAT-regel verwijderd |
> | Bewerking | Micro soft. Network/loadBalancers/inboundNatRules/samen voegen/actie | Voegt een load balancer binnenkomende NAT-regel. Niet Alertable. |
> | Bewerking | Micro soft. Network/loadBalancers/inboundNatRules/lezen | Hiermee wordt een load balancer binnenkomende NAT-regel definitie opgehaald |
> | Bewerking | Micro soft. Network/loadBalancers/inboundNatRules/schrijven | Hiermee maakt u een load balancer binnenkomende NAT-regel of werkt u een bestaande load balancer binnenkomende NAT-regel bij |
> | Bewerking | Micro soft. Network/loadBalancers/loadBalancingRules/lezen | Hiermee wordt een load balancer de definitie van de taakverdelings regel opgehaald |
> | Bewerking | Micro soft. Network/loadBalancers/networkInterfaces/lezen | Hiermee wordt verwezen naar alle netwerk interfaces onder een load balancer |
> | Bewerking | Micro soft. Network/loadBalancers/outboundRules/lezen | Hiermee wordt een load balancer uitgaande regel definitie opgehaald |
> | Bewerking | Micro soft. Network/loadBalancers/tests/samen voegen/actie | Staat het gebruik van tests van een load balancer toe. Met deze machtiging healthProbe eigenschap van de VM-schaalset kan bijvoorbeeld naar de test worden verwezen. Niet alertable. |
> | Bewerking | Micro soft. Network/loadBalancers/tests/lezen | Hiermee wordt een load balancer-test opgehaald |
> | Bewerking | Micro soft. Network/loadBalancers/lezen | Hiermee wordt een load balancer definitie opgehaald |
> | Bewerking | Micro soft. Network/loadBalancers/informatie/lezen | Hiermee worden verwijzingen naar alle virtuele machines onder een load balancer opgehaald |
> | Bewerking | Micro soft. Network/loadBalancers/schrijven | Hiermee maakt u een load balancer of werkt u een bestaande load balancer bij |
> | Bewerking | Micro soft. Network/localnetworkgateways/verwijderen | Hiermee worden LocalNetworkGateway verwijderd |
> | Bewerking | Micro soft. Network/localnetworkgateways/lezen | Hiermee wordt LocalNetworkGateway opgehaald |
> | Bewerking | Micro soft. Network/localnetworkgateways/schrijven | Hiermee wordt een bestaande LocalNetworkGateway gemaakt of bijgewerkt |
> | Bewerking | Micro soft. netwerk/locaties/autoApprovedPrivateLinkServices/lezen | Hiermee worden automatisch goedgekeurde persoonlijke koppelings services opgehaald |
> | Bewerking | Micro soft. netwerk/locaties/availableDelegations/lezen | Beschik bare delegaties ophalen |
> | Bewerking | Micro soft. netwerk/locaties/availablePrivateEndpointTypes/lezen | Beschik bare persoonlijke eindpunt resources ophalen |
> | Bewerking | Micro soft. netwerk/locaties/availableServiceAliases/lezen | Beschik bare service aliassen ophalen |
> | Bewerking | Micro soft. netwerk/locaties/bareMetalTenants/actie | Hiermee wordt een bare metal-Tenant toegewezen of gevalideerd |
> | Bewerking | Micro soft. netwerk/locaties/checkAcceleratedNetworkingSupport/actie | Hiermee wordt de ondersteuning voor versnelde netwerken gecontroleerd |
> | Bewerking | Micro soft. netwerk/locaties/checkDnsNameAvailability/lezen | Controleert of het DNS-label beschikbaar is op de opgegeven locatie |
> | Bewerking | Micro soft. netwerk/locaties/checkPrivateLinkServiceVisibility/actie | Controleert de zicht baarheid van de persoonlijke koppelings service |
> | Bewerking | Micro soft. netwerk/locaties/operationResults/lezen | Hiermee wordt het bewerkings resultaat van een asynchrone bericht-of verwijder bewerking opgehaald |
> | Bewerking | Micro soft. netwerk/locaties/bewerkingen/lezen | Hiermee wordt de bewerkings resource opgehaald die de status van een asynchrone bewerking vertegenwoordigt |
> | Bewerking | Micro soft. netwerk/locaties/serviceTags/lezen | Service Tags ophalen |
> | Bewerking | Micro soft. netwerk/locaties/supportedVirtualMachineSizes/lezen | Hiermee worden de ondersteunde grootten van virtuele machines opgehaald |
> | Bewerking | Micro soft. netwerk/locaties/gebruik/lezen | Hiermee worden de metrische gegevens over het bronnen gebruik opgehaald |
> | Bewerking | Micro soft. netwerk/locaties/virtualNetworkAvailableEndpointServices/lezen | Hiermee wordt een lijst met beschik bare Virtual Network endpoint services opgehaald |
> | Bewerking | Micro soft. Network/networkIntentPolicies/verwijderen | Hiermee wordt een beleid voor netwerk intentie verwijderd |
> | Bewerking | Micro soft. Network/networkIntentPolicies/lezen | Hiermee wordt een beschrijving van het beleid voor netwerk intentie opgehaald |
> | Bewerking | Micro soft. Network/networkIntentPolicies/schrijven | Hiermee wordt een beleid voor netwerk intentie gemaakt of een bestaand netwerk intentie beleid bijgewerkt |
> | Bewerking | Micro soft. Network/networkInterfaces/verwijderen | Hiermee wordt een netwerk interface verwijderd |
> | Bewerking | Micro soft. Network/networkInterfaces/effectiveNetworkSecurityGroups/Action | Netwerk beveiligings groepen die zijn geconfigureerd op de netwerk interface van de virtuele machine ophalen |
> | Bewerking | Micro soft. Network/networkInterfaces/effectiveRouteTable/Action | Route tabel ophalen die is geconfigureerd op de netwerk interface van de virtuele machine |
> | Bewerking | Micro soft. Network/networkInterfaces/ipconfigurations/samen voegen/actie | Voegt een IP-configuratie van een netwerk interface toe. Niet alertable. |
> | Bewerking | Micro soft. Network/networkInterfaces/ipconfigurations/lezen | Hiermee wordt een IP-configuratie definitie van de netwerk interface opgehaald.  |
> | Bewerking | Micro soft. Network/networkInterfaces/samen voegen/actie | Voegt een virtuele machine toe aan een netwerk interface. Niet Alertable. |
> | Bewerking | Micro soft. Network/networkInterfaces/loadBalancers/lezen | Hiermee worden alle load balancers opgehaald waarvan de netwerk interface deel uitmaakt |
> | Bewerking | Micro soft. Network/networkInterfaces/lezen | Hiermee haalt u een definitie van een netwerk interface.  |
> | Bewerking | Micro soft. Network/networkInterfaces/tapConfigurations/verwijderen | Hiermee verwijdert u een netwerk interface tikt u op configuratie. |
> | Bewerking | Micro soft. Network/networkInterfaces/tapConfigurations/lezen | Hiermee wordt een netwerk interface op configuratie opgehaald. |
> | Bewerking | Micro soft. Network/networkInterfaces/tapConfigurations/schrijven | Hiermee maakt u een netwerk interface tikt u op configuratie of werkt u een bestaande netwerk interface op configuratie tikken. |
> | Bewerking | Micro soft. Network/networkInterfaces/schrijven | Hiermee maakt u een netwerk interface of werkt u een bestaande netwerk interface bij.  |
> | Bewerking | Micro soft. Network/networkProfiles/verwijderen | Hiermee wordt een netwerk profiel verwijderd |
> | Bewerking | Micro soft. Network/networkProfiles/lezen | Hiermee wordt een netwerk profiel opgehaald |
> | Bewerking | Micro soft. Network/networkProfiles/removeContainers/Action | Containers verwijderen |
> | Bewerking | Micro soft. Network/networkProfiles/setContainers/Action | Containers instellen |
> | Bewerking | Micro soft. Network/networkProfiles/setNetworkInterfaces/Action | Netwerk interfaces voor containers instellen |
> | Bewerking | Micro soft. Network/networkProfiles/schrijven | Hiermee wordt een netwerk profiel gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/networkSecurityGroups/defaultSecurityRules/lezen | Hiermee wordt een standaard beveiligings regel definitie opgehaald |
> | Bewerking | Micro soft. Network/networkSecurityGroups/verwijderen | Hiermee wordt een netwerk beveiligings groep verwijderd |
> | Bewerking | Micro soft. Network/networkSecurityGroups/samen voegen/actie | Er wordt lid van een netwerk beveiligings groep. Niet Alertable. |
> | Bewerking | Micro soft. Network/networkSecurityGroups/lezen | Hiermee wordt een definitie van een netwerk beveiligings groep opgehaald |
> | Bewerking | Micro soft. Network/networkSecurityGroups/securityRules/verwijderen | Hiermee verwijdert u een beveiligings regel |
> | Bewerking | Micro soft. Network/networkSecurityGroups/securityRules/lezen | Hiermee wordt een definitie van een beveiligings regel opgehaald |
> | Bewerking | Micro soft. Network/networkSecurityGroups/securityRules/schrijven | Hiermee maakt u een beveiligings regel of werkt u een bestaande beveiligings regel bij |
> | Bewerking | Micro soft. Network/networkSecurityGroups/schrijven | Hiermee wordt een netwerk beveiligings groep gemaakt of een bestaande netwerk beveiligings groep bijgewerkt |
> | Bewerking | Micro soft. Network/networkWatchers/availableProvidersList/Action | Retourneert alle beschik bare Internet serviceproviders voor een opgegeven Azure-regio. |
> | Bewerking | Micro soft. Network/networkWatchers/azureReachabilityReport/Action | Retourneert de relatieve latentie score voor Internet serviceproviders van een opgegeven locatie naar Azure-regio's. |
> | Bewerking | Micro soft. Network/networkWatchers/configureFlowLog/Action | Hiermee configureert u de logboek registratie van de stroom voor een doel bron. |
> | Bewerking | Micro soft. Network/networkWatchers/connectionMonitors/verwijderen | Hiermee wordt een verbindings monitor verwijderd |
> | Bewerking | Micro soft. Network/networkWatchers/connectionMonitors/query/actie | Query bewakings connectiviteit tussen opgegeven eind punten |
> | Bewerking | Micro soft. Network/networkWatchers/connectionMonitors/lezen | Details van de verbindings monitor ophalen |
> | Bewerking | Micro soft. Network/networkWatchers/connectionMonitors/start/Action | Bewakings verbinding tussen opgegeven eind punten starten |
> | Bewerking | Micro soft. Network/networkWatchers/connectionMonitors/stop/Action | Controle connectiviteit tussen opgegeven eind punten stoppen/onderbreken |
> | Bewerking | Micro soft. Network/networkWatchers/connectionMonitors/schrijven | Hiermee maakt u een verbindings monitor |
> | Bewerking | Micro soft. Network/networkWatchers/connectivityCheck/Action | Hiermee wordt de mogelijkheid voor het tot stand brengen van een directe TCP-verbinding vanaf een virtuele machine naar een bepaald eind punt, inclusief een andere VM of een wille keurige externe server, gecontroleerd. |
> | Bewerking | Micro soft. Network/networkWatchers/verwijderen | Hiermee wordt een netwerk-Watcher verwijderd |
> | Bewerking | Micro soft. Network/networkWatchers/ipFlowVerify/Action | Retourneert of het pakket wordt toegestaan of geweigerd van een bepaalde bestemming. |
> | Bewerking | Micro soft. Network/networkWatchers/lenzen/verwijderen | Hiermee wordt een lens verwijderd |
> | Bewerking | Micro soft. Network/networkWatchers/lenzen/query/actie | Query's bewaken van netwerk verkeer op een opgegeven eind punt |
> | Bewerking | Micro soft. Network/networkWatchers/lenzen/lezen | Lens Details ophalen |
> | Bewerking | Micro soft. Network/networkWatchers/lenzen/start/actie | Bewaken van netwerk verkeer op een opgegeven eind punt starten |
> | Bewerking | Micro soft. Network/networkWatchers/lenzen/stoppen/actie | Bewaken van netwerk verkeer op een opgegeven eind punt stoppen/onderbreken |
> | Bewerking | Micro soft. Network/networkWatchers/lenzen/schrijven | Hiermee maakt u een lens |
> | Bewerking | Micro soft. Network/networkWatchers/networkConfigurationDiagnostic/Action | Diagnose van de netwerk configuratie. |
> | Bewerking | Micro soft. Network/networkWatchers/nextHop/actie | Voor een opgegeven doel-en doel-IP-adres retourneert het type van de volgende hop en de volgende hoop IP-adres. |
> | Bewerking | Micro soft. Network/networkWatchers/packetCaptures/verwijderen | Hiermee wordt een pakket opname verwijderd |
> | Bewerking | Micro soft. Network/networkWatchers/packetCaptures/queryStatus/Action | Haalt informatie op over de eigenschappen en status van een pakket opname bron. |
> | Bewerking | Micro soft. Network/networkWatchers/packetCaptures/lezen | De definitie voor pakket opname ophalen |
> | Bewerking | Micro soft. Network/networkWatchers/packetCaptures/stop/Action | Stop de actieve sessie voor het vastleggen van pakketten. |
> | Bewerking | Micro soft. Network/networkWatchers/packetCaptures/schrijven | Hiermee maakt u een pakket opname |
> | Bewerking | Micro soft. Network/networkWatchers/pingMeshes/verwijderen | Hiermee wordt een PingMesh verwijderd |
> | Bewerking | Micro soft. Network/networkWatchers/pingMeshes/lezen | Details van PingMesh ophalen |
> | Bewerking | Micro soft. Network/networkWatchers/pingMeshes/start/Action | PingMesh tussen opgegeven Vm's starten |
> | Bewerking | Micro soft. Network/networkWatchers/pingMeshes/stop/Action | PingMesh tussen opgegeven Vm's stoppen |
> | Bewerking | Micro soft. Network/networkWatchers/pingMeshes/schrijven | Hiermee maakt u een PingMesh |
> | Bewerking | Micro soft. Network/networkWatchers/queryConnectionMonitors/Action | Controle van de connectiviteit voor batch query's tussen opgegeven eind punten |
> | Bewerking | Micro soft. Network/networkWatchers/queryFlowLogStatus/Action | Hiermee wordt de status van de logboek registratie van de stroom op een resource opgehaald. |
> | Bewerking | Micro soft. Network/networkWatchers/queryTroubleshootResult/Action | Hiermee wordt het probleem voor het oplossen van problemen met de eerder uitgevoerde of actieve probleemoplossings bewerking opgehaald. |
> | Bewerking | Micro soft. Network/networkWatchers/lezen | De Network Watcher-definitie ophalen |
> | Bewerking | Micro soft. Network/networkWatchers/securityGroupView/Action | De geconfigureerde en efficiënte regels voor netwerk beveiligings groepen weer geven die zijn toegepast op een virtuele machine. |
> | Bewerking | Micro soft. Network/networkWatchers/Topology/actie | Hiermee wordt een weer gave op netwerk niveau van resources en de bijbehorende relaties in een resource groep opgehaald. |
> | Bewerking | Micro soft. netwerk/networkWatchers/problemen oplossen/actie | Hiermee wordt het oplossen van problemen met een netwerk bron in azure gestart. |
> | Bewerking | Micro soft. Network/networkWatchers/schrijven | Hiermee wordt een netwerk-Watcher gemaakt of een bestaande netwerk-Watcher bijgewerkt |
> | Bewerking | Micro soft. Network/Operations/lezen | Beschik bare bewerkingen ophalen |
> | Bewerking | Micro soft. Network/p2sVpnGateways/verwijderen | Hiermee verwijdert u een P2SVpnGateway. |
> | Bewerking | Micro soft. Network/p2sVpnGateways/generatevpnprofile/Action | VPN-profiel voor P2SVpnGateway genereren |
> | Bewerking | Micro soft. Network/p2sVpnGateways/getp2svpnconnectionhealth/Action | Hiermee wordt een P2S VPN-verbindings status opgehaald voor P2SVpnGateway |
> | Bewerking | Micro soft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/Action | Hiermee wordt een P2S VPN-verbindings status voor P2SVpnGateway opgehaald |
> | Bewerking | Micro soft. Network/p2sVpnGateways/lezen | Hiermee wordt een P2SVpnGateway opgehaald. |
> | Bewerking | Micro soft. Network/p2sVpnGateways/schrijven | Hiermee wordt een P2SVpnGateway geplaatst. |
> | Bewerking | Micro soft. Network/privateDnsOperationResults/lezen | Hiermee worden de resultaten van een Privé-DNS bewerking opgehaald |
> | Bewerking | Micro soft. Network/privateDnsOperationStatuses/lezen | Hiermee wordt de status van een Privé-DNS bewerking opgehaald |
> | Bewerking | Micro soft. Network/privateDnsZones/A/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' A ' in een Privé-DNS zone. |
> | Bewerking | Micro soft. Network/privateDnsZones/A/lezen | De recordset van het type ' A ' ophalen binnen een Privé-DNS zone, in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/privateDnsZones/A/schrijven | Een recordset van het type ' A ' in een Privé-DNS zone maken of bijwerken. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/privateDnsZones/AAAA/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' AAAA ' uit een Privé-DNS zone. |
> | Bewerking | Micro soft. Network/privateDnsZones/AAAA/lezen | Haal de recordset van het type ' AAAA ' op in een Privé-DNS zone, in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/privateDnsZones/AAAA/schrijven | Een recordset van het type ' AAAA ' maken of bijwerken binnen een Privé-DNS zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/privateDnsZones/alle/lezen | Hiermee worden Privé-DNS record sets in verschillende typen opgehaald |
> | Bewerking | Micro soft. Network/privateDnsZones/CNAME/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' CNAME ' uit een Privé-DNS zone. |
> | Bewerking | Micro soft. Network/privateDnsZones/CNAME/lezen | De recordset van het type CNAME ophalen binnen een Privé-DNS zone, in JSON-indeling. |
> | Bewerking | Micro soft. Network/privateDnsZones/CNAME/schrijven | Een recordset van het type CNAME maken of bijwerken binnen een Privé-DNS zone. |
> | Bewerking | Micro soft. Network/privateDnsZones/verwijderen | Een Privé-DNS zone verwijderen. |
> | Bewerking | Micro soft. Network/privateDnsZones/MX/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' MX ' uit een Privé-DNS zone. |
> | Bewerking | Micro soft. Network/privateDnsZones/MX/lezen | Haal de recordset van het type ' MX ' op in een Privé-DNS zone, in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/privateDnsZones/MX/schrijven | Een recordset van het type ' MX ' maken of bijwerken binnen een Privé-DNS zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/privateDnsZones/PTR/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' PTR ' uit een Privé-DNS zone. |
> | Bewerking | Micro soft. Network/privateDnsZones/PTR/lezen | Haal de recordset van het type ' PTR ' op in een Privé-DNS zone, in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/privateDnsZones/PTR/schrijven | Een recordset van het type ' PTR ' maken of bijwerken binnen een Privé-DNS zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/privateDnsZones/lezen | Haal de eigenschappen van de Privé-DNS-zone op in JSON-indeling. Houd er rekening mee dat met deze opdracht de virtuele netwerken waaraan de Privé-DNS zone is gekoppeld of de record sets in de zone, niet worden opgehaald. |
> | Bewerking | Micro soft. Network/privateDnsZones/record sets/lezen | Hiermee worden Privé-DNS record sets in verschillende typen opgehaald |
> | Bewerking | Micro soft. Network/privateDnsZones/SOA/lezen | Haal de recordset van het type ' SOA ' op in een Privé-DNS zone, in JSON-indeling. |
> | Bewerking | Micro soft. Network/privateDnsZones/SOA/schrijven | Een recordset van het type ' SOA ' in een Privé-DNS zone bijwerken. |
> | Bewerking | Micro soft. Network/privateDnsZones/SRV/verwijderen | De recordset van een bepaalde naam en type SRV verwijderen uit een Privé-DNS zone. |
> | Bewerking | Micro soft. Network/privateDnsZones/SRV/lezen | De recordset van het type SRV ophalen binnen een Privé-DNS zone, in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/privateDnsZones/SRV/schrijven | Een recordset van het type SRV maken of bijwerken binnen een Privé-DNS zone. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/privateDnsZones/TXT/verwijderen | Verwijder de recordset van een bepaalde naam en typ ' TXT ' uit een Privé-DNS zone. |
> | Bewerking | Micro soft. Network/privateDnsZones/TXT/lezen | Haal de recordset van het type ' TXT ' op in een Privé-DNS zone, in JSON-indeling. De recordset bevat een lijst met records en de TTL, tags en ETag. |
> | Bewerking | Micro soft. Network/privateDnsZones/TXT/schrijven | Een recordset van het type ' TXT ' in een Privé-DNS zone maken of bijwerken. Met de opgegeven records worden de huidige records in de recordset vervangen. |
> | Bewerking | Micro soft. Network/privateDnsZones/virtualNetworkLinks/verwijderen | Verwijder een Privé-DNS zone koppeling naar het virtuele netwerk. |
> | Bewerking | Micro soft. Network/privateDnsZones/virtualNetworkLinks/lezen | Haal de koppeling naar de Privé-DNS zone op in JSON-indeling naar eigenschappen van virtueel netwerk. |
> | Bewerking | Micro soft. Network/privateDnsZones/virtualNetworkLinks/schrijven | Een Privé-DNS zone koppeling naar een virtueel netwerk maken of bijwerken. |
> | Bewerking | Micro soft. Network/privateDnsZones/schrijven | Een Privé-DNS zone in een resource groep maken of bijwerken. Houd er rekening mee dat deze opdracht niet kan worden gebruikt voor het maken of bijwerken van koppelingen met virtuele netwerken of record sets in de zone. |
> | Bewerking | Micro soft. Network/privateEndpointRedirectMaps/lezen | Hiermee wordt een persoonlijk eind punt RedirectMap |
> | Bewerking | Micro soft. Network/privateEndpointRedirectMaps/schrijven | Hiermee maakt u een persoonlijk eind punt RedirectMap of werkt u een bestaand persoonlijk eind punt RedirectMap |
> | Bewerking | Micro soft. Network/privateEndpoints/verwijderen | Hiermee verwijdert u een persoonlijke eindpunt resource. |
> | Bewerking | Micro soft. Network/privateEndpoints/lezen | Hiermee wordt een persoonlijke eindpunt resource opgehaald. |
> | Bewerking | Micro soft. Network/privateEndpoints/schrijven | Hiermee maakt u een nieuw persoonlijk eind punt of werkt u een bestaand persoonlijk eind punt bij. |
> | Bewerking | Micro soft. Network/privateLinkServices/verwijderen | Hiermee wordt een resource van een persoonlijke koppelings service verwijderd. |
> | Bewerking | Micro soft. Network/privateLinkServices/privateEndpointConnections/verwijderen | Hiermee verwijdert u een verbinding met een privé-eind punt. |
> | Bewerking | Micro soft. Network/privateLinkServices/privateEndpointConnections/lezen | Hiermee wordt een definitie van een particuliere endpoint-verbinding opgehaald. |
> | Bewerking | Micro soft. Network/privateLinkServices/privateEndpointConnections/schrijven | Hiermee maakt u een nieuwe verbinding voor een persoonlijk eind punt of werkt u een bestaande verbinding met een privé-eind punt bij. |
> | Bewerking | Micro soft. Network/privateLinkServices/lezen | Hiermee wordt een bron van een persoonlijke koppelings service opgehaald. |
> | Bewerking | Micro soft. Network/privateLinkServices/schrijven | Hiermee maakt u een nieuwe privé koppelings service of werkt u een bestaande persoonlijke koppelings service bij. |
> | Bewerking | Micro soft. Network/publicIPAddresses/verwijderen | Hiermee verwijdert u een openbaar IP-adres. |
> | Bewerking | Micro soft. Network/publicIPAddresses/samen voegen/actie | Er wordt verbinding gemaakt met een openbaar IP-adres. Niet Alertable. |
> | Bewerking | Micro soft. Network/publicIPAddresses/lezen | Hiermee wordt een definitie van een openbaar IP-adres opgehaald. |
> | Bewerking | Micro soft. Network/publicIPAddresses/schrijven | Hiermee maakt u een openbaar IP-adres of werkt u een bestaand openbaar IP-adres bij.  |
> | Bewerking | Micro soft. Network/publicIPPrefixes/verwijderen | Hiermee wordt een openbaar IP-voor voegsel verwijderd |
> | Bewerking | Micro soft. Network/publicIPPrefixes/samen voegen/actie | Er wordt een PublicIPPrefix toegevoegd. Niet alertable. |
> | Bewerking | Micro soft. Network/publicIPPrefixes/lezen | Hiermee wordt een definitie van een openbaar IP-voor voegsel opgehaald |
> | Bewerking | Micro soft. Network/publicIPPrefixes/schrijven | Hiermee wordt een openbaar IP-voor voegsel gemaakt of een bestaand openbaar IP-voor voegsel bijgewerkt |
> | Bewerking | Micro soft. Network/REGI ster/actie | Hiermee wordt het abonnement geregistreerd |
> | Bewerking | Micro soft. Network/routeFilters/verwijderen | Hiermee wordt een definitie van een route filter verwijderd |
> | Bewerking | Micro soft. Network/routeFilters/samen voegen/actie | Er wordt verbinding gemaakt met een route filter. Niet Alertable. |
> | Bewerking | Micro soft. Network/routeFilters/lezen | Hiermee wordt een route filter definitie opgehaald |
> | Bewerking | Micro soft. Network/routeFilters/routeFilterRules/verwijderen | Hiermee wordt een regel definitie voor een route filter verwijderd |
> | Bewerking | Micro soft. Network/routeFilters/routeFilterRules/lezen | Hiermee wordt een regel definitie voor een route filter opgehaald |
> | Bewerking | Micro soft. Network/routeFilters/routeFilterRules/schrijven | Hiermee maakt u een regel voor route filtering of werkt u een bestaande route filter regel bij |
> | Bewerking | Micro soft. Network/routeFilters/schrijven | Hiermee maakt u een route filter of werkt u een bestaand route filter bij |
> | Bewerking | Micro soft. Network/routeTables/verwijderen | Hiermee wordt een definitie van een route tabel verwijderd |
> | Bewerking | Micro soft. Network/routeTables/samen voegen/actie | Voegt een route tabel samen. Niet Alertable. |
> | Bewerking | Micro soft. Network/routeTables/lezen | Hiermee wordt een definitie van een route tabel opgehaald |
> | Bewerking | Micro soft. Network/routeTables/routes/verwijderen | Hiermee verwijdert u een route definitie |
> | Bewerking | Micro soft. Network/routeTables/routes/lezen | Hiermee wordt een route definitie opgehaald |
> | Bewerking | Micro soft. Network/routeTables/routes/schrijven | Hiermee maakt u een route of werkt u een bestaande route bij |
> | Bewerking | Micro soft. Network/routeTables/schrijven | Hiermee maakt u een route tabel of werkt u een bestaande route tabel bij |
> | Bewerking | Micro soft. Network/securegateways/verwijderen | Beveiligde gateway verwijderen |
> | Bewerking | Micro soft. Network/securegateways/lezen | Beveiligde gateway ophalen |
> | Bewerking | Micro soft. Network/securegateways/schrijven | Hiermee wordt een beveiligde gateway gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/serviceEndpointPolicies/verwijderen | Hiermee wordt een service-eindpunt beleid verwijderd |
> | Bewerking | Micro soft. Network/serviceEndpointPolicies/samen voegen/actie | Voegt een service-eindpunt beleid samen. Niet alertable. |
> | Bewerking | Micro soft. Network/serviceEndpointPolicies/joinSubnet/Action | Voegt een subnet toe aan service Endpoint-beleids regels. Niet alertable. |
> | Bewerking | Micro soft. Network/serviceEndpointPolicies/lezen | Hiermee wordt een beschrijving van het service-eindpunt beleid opgehaald |
> | Bewerking | Micro soft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/verwijderen | Hiermee wordt een beleids definitie voor service-eind punten verwijderd |
> | Bewerking | Micro soft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/lezen | Hiermee wordt een beschrijving van de definitie van het service-eindpunt beleid opgehaald |
> | Bewerking | Micro soft. Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/schrijven | Hiermee wordt een beleids definitie voor service-eind punten gemaakt of een bestaande definitie van een service-eindpunt beleid bijgewerkt |
> | Bewerking | Micro soft. Network/serviceEndpointPolicies/schrijven | Hiermee wordt een service-eindpunt beleid gemaakt of een bestaand service-eindpunt beleid bijgewerkt |
> | Bewerking | Micro soft. Network/trafficManagerGeographicHierarchies/lezen | Hiermee wordt de Traffic Manager geografische hiërarchie opgehaald die regio's bevat die kunnen worden gebruikt met de geografische routerings methode voor verkeer |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/azureEndpoints/verwijderen | Hiermee verwijdert u een Azure-eindpunt uit een bestaand Traffic Manager-profiel. Traffic Manager wordt het routeren van verkeer naar de verwijderde Azure-eindpunt gestopt. |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/azureEndpoints/lezen | Hiermee haalt u een Azure-eindpunt die tot een Traffic Manager profiel behoort, inclusief alle eigenschappen van die Azure-eindpunt. |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/azureEndpoints/schrijven | Voeg een nieuwe Azure-eindpunt toe aan een bestaand Traffic Manager profiel of werk de eigenschappen van een bestaande Azure-eindpunt in dat Traffic Manager profiel bij. |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/verwijderen | Verwijder het Traffic Manager profiel. Alle instellingen die aan het Traffic Manager profiel zijn gekoppeld, gaan verloren en het profiel kan niet meer worden gebruikt voor het routeren van verkeer. |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/externalEndpoints/verwijderen | Hiermee verwijdert u een extern eind punt uit een bestaand Traffic Manager profiel. Traffic Manager wordt het routeren van verkeer naar het verwijderde externe eind punt gestopt. |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/externalEndpoints/lezen | Hiermee haalt u een extern eind punt dat hoort bij een Traffic Manager profiel, inclusief alle eigenschappen van het externe eind punt. |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/externalEndpoints/schrijven | Voeg een nieuw extern eind punt toe aan een bestaand Traffic Manager profiel of werk de eigenschappen van een bestaand extern eind punt in dat Traffic Manager profiel bij. |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/heatMaps/lezen | Hiermee haalt u de Traffic Manager heatmap voor het gegeven Traffic Manager profiel dat query aantallen en latentie gegevens bevat op locatie en bron-IP. |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/nestedEndpoints/verwijderen | Hiermee verwijdert u een genest eind punt uit een bestaand Traffic Manager profiel. Traffic Manager wordt het routerings verkeer naar het verwijderde geneste eind punt gestopt. |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/nestedEndpoints/lezen | Hiermee wordt een genest eind punt opgehaald dat tot een Traffic Manager profiel behoort, inclusief alle eigenschappen van dat geneste eind punt. |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/nestedEndpoints/schrijven | Een nieuw genest eind punt toevoegen aan een bestaand Traffic Manager profiel of de eigenschappen van een bestaand genest eind punt in dat Traffic Manager profiel bijwerken. |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/lezen | De configuratie van het Traffic Manager-profiel ophalen. Dit zijn onder andere DNS-instellingen, instellingen voor verkeers routering, instellingen voor eindpunt bewaking en de lijst met eind punten die door dit Traffic Manager profiel worden gerouteerd. |
> | Bewerking | Micro soft. Network/trafficManagerProfiles/schrijven | Maak een Traffic Manager profiel of wijzig de configuratie van een bestaand Traffic Manager-profiel.<br>Dit omvat het in-of uitschakelen van een profiel en het wijzigen van DNS-instellingen, instellingen voor verkeers routering of instellingen voor het controleren van eind punten.<br>Eind punten die door het Traffic Manager profiel worden doorgestuurd, kunnen worden toegevoegd, verwijderd, ingeschakeld of uitgeschakeld. |
> | Bewerking | Micro soft. Network/trafficManagerUserMetricsKeys/verwijderen | Hiermee verwijdert u de sleutel op abonnements niveau die wordt gebruikt voor de verzameling van metrische gegevens voor de realtime gebruiker. |
> | Bewerking | Micro soft. Network/trafficManagerUserMetricsKeys/lezen | Hiermee wordt de sleutel op abonnements niveau opgehaald die wordt gebruikt voor de verzameling van metrische gegevens voor de realtime gebruiker. |
> | Bewerking | Micro soft. Network/trafficManagerUserMetricsKeys/schrijven | Hiermee wordt een nieuwe sleutel op abonnements niveau gemaakt die moet worden gebruikt voor de verzameling van metrische gegevens voor de realtime gebruiker. |
> | Bewerking | Micro soft. netwerk/registratie/actie | Hiermee wordt de registratie van het abonnement ongedaan gemaakt |
> | Bewerking | Micro soft. Network/virtualHubs/verwijderen | Hiermee wordt een virtuele hub verwijderd |
> | Bewerking | Micro soft. Network/virtualHubs/hubVirtualNetworkConnections/verwijderen | Hiermee wordt een HubVirtualNetworkConnection verwijderd |
> | Bewerking | Micro soft. Network/virtualHubs/hubVirtualNetworkConnections/lezen | Een HubVirtualNetworkConnection ophalen |
> | Bewerking | Micro soft. Network/virtualHubs/hubVirtualNetworkConnections/schrijven | Een HubVirtualNetworkConnection maken of bijwerken |
> | Bewerking | Micro soft. Network/virtualHubs/lezen | Een virtuele hub ophalen |
> | Bewerking | Micro soft. Network/virtualHubs/routeTables/verwijderen | Een VirtualHubRouteTableV2 verwijderen |
> | Bewerking | Micro soft. Network/virtualHubs/routeTables/lezen | Een VirtualHubRouteTableV2 ophalen |
> | Bewerking | Micro soft. Network/virtualHubs/routeTables/schrijven | Een VirtualHubRouteTableV2 maken of bijwerken |
> | Bewerking | Micro soft. Network/virtualHubs/schrijven | Een virtuele hub maken of bijwerken |
> | Bewerking | micro soft. Network/virtualnetworkgateways/Connections/lezen | VirtualNetworkGatewayConnection ophalen |
> | Bewerking | Micro soft. Network/virtualNetworkGateways/verwijderen | Hiermee wordt een virtualNetworkGateway verwijderd |
> | Bewerking | micro soft. Network/virtualnetworkgateways/generatevpnclientpackage/Action | VpnClient-pakket genereren voor virtualNetworkGateway |
> | Bewerking | micro soft. Network/virtualnetworkgateways/generatevpnprofile/Action | VpnProfile-pakket genereren voor VirtualNetworkGateway |
> | Bewerking | micro soft. Network/virtualnetworkgateways/getadvertisedroutes/Action | Hiermee worden virtualNetworkGateway geadverteerde routes opgehaald |
> | Bewerking | micro soft. Network/virtualnetworkgateways/getbgppeerstatus/Action | Hiermee wordt de BGP-peer status virtualNetworkGateway opgehaald |
> | Bewerking | micro soft. Network/virtualnetworkgateways/getlearnedroutes/Action | Hiermee worden virtualnetworkgateway geleerde routes opgehaald |
> | Bewerking | micro soft. Network/virtualnetworkgateways/getvpnclientconnectionhealth/Action | Per VPN-client verbindings status voor VirtualNetworkGateway ophalen |
> | Bewerking | micro soft. Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | Vpnclient IPSec-para meters voor VirtualNetworkGateway P2S-client ophalen. |
> | Bewerking | micro soft. Network/virtualnetworkgateways/getvpnprofilepackageurl/Action | Hiermee wordt de URL van een vooraf gegenereerd VPN-client profiel pakket opgehaald |
> | Bewerking | Micro soft. Network/virtualNetworkGateways/lezen | Hiermee wordt een VirtualNetworkGateway opgehaald |
> | Bewerking | micro soft. Network/virtualnetworkgateways/reset/Action | Hiermee wordt een virtualNetworkGateway opnieuw ingesteld |
> | Bewerking | micro soft. Network/virtualnetworkgateways/resetvpnclientsharedkey/Action | Stel de gedeelde sleutel vpnclient voor de VirtualNetworkGateway P2S-client opnieuw in. |
> | Bewerking | micro soft. Network/virtualnetworkgateways/setvpnclientipsecparameters/Action | Stel vpnclient IPSec-para meters in voor VirtualNetworkGateway P2S-client. |
> | Bewerking | Micro soft. Network/virtualnetworkgateways/supportedvpndevices/Action | Een lijst met ondersteunde VPN-apparaten |
> | Bewerking | Micro soft. Network/virtualNetworkGateways/schrijven | Hiermee wordt een VirtualNetworkGateway gemaakt of bijgewerkt |
> | Bewerking | Micro soft. Network/virtualNetworks/BastionHosts/Action | Hiermee worden Bastion opgehaald in een Virtual Network. |
> | Bewerking | Micro soft. Network/virtualNetworks/bastionHosts/default/action | Hiermee worden Bastion opgehaald in een Virtual Network. |
> | Bewerking | Micro soft. Network/virtualNetworks/checkIpAddressAvailability/lezen | Controleer of het IP-adres beschikbaar is op het opgegeven virtuele netwerk |
> | Bewerking | Micro soft. Network/virtualNetworks/verwijderen | Hiermee verwijdert u een virtueel netwerk |
> | Bewerking | Micro soft. Network/virtualNetworks/samen voegen/actie | Voegt een virtueel netwerk samen. Niet Alertable. |
> | Bewerking | Micro soft. Network/virtualNetworks/peer/Action | Peering van een virtueel netwerk met een ander virtueel netwerk |
> | Bewerking | Micro soft. Network/virtualNetworks/lezen | De virtuele-netwerk definitie ophalen |
> | Bewerking | Micro soft. Network/virtualNetworks/subnetten/contextualServiceEndpointPolicies/verwijderen | Hiermee wordt een beleid voor contextuele service-eind punten verwijderd |
> | Bewerking | Micro soft. Network/virtualNetworks/subnetten/contextualServiceEndpointPolicies/lezen | Hiermee wordt beleid voor contextuele service-eind punten opgehaald |
> | Bewerking | Micro soft. Network/virtualNetworks/subnetten/contextualServiceEndpointPolicies/schrijven | Hiermee wordt een beleid voor contextuele service-eind punten gemaakt of een bestaand beleid voor contextuele service-eind punten bijgewerkt |
> | Bewerking | Micro soft. Network/virtualNetworks/subnetten/verwijderen | Hiermee verwijdert u een subnet van een virtueel netwerk |
> | Bewerking | Micro soft. Network/virtualNetworks/subnetten/toevoegen/actie | Voegt een virtueel netwerk samen. Niet Alertable. |
> | Bewerking | Micro soft. netwerk/virtualNetworks/subnetten/joinViaServiceEndpoint/actie | Voegt een resource, zoals een opslag account of SQL database, toe aan een subnet. Niet alertable. |
> | Bewerking | Micro soft. netwerk/virtualNetworks/subnetten/prepareNetworkPolicies/actie | Bereidt een subnet voor door het benodigde netwerk beleid toe te passen |
> | Bewerking | Micro soft. Network/virtualNetworks/subnetten/lezen | Hiermee wordt een subnet definitie van een virtueel netwerk opgehaald |
> | Bewerking | Micro soft. netwerk/virtualNetworks/subnetten/unprepareNetworkPolicies/actie | De voor bereiding van een subnet ongedaan maken door het toegepaste netwerk beleid te verwijderen |
> | Bewerking | Micro soft. Network/virtualNetworks/subnetten/informatie/lezen | Hiermee worden verwijzingen naar alle virtuele machines in een subnet van een virtueel netwerk opgehaald |
> | Bewerking | Micro soft. Network/virtualNetworks/subnetten/schrijven | Hiermee wordt een subnet van een virtueel netwerk gemaakt of een bestaand subnet van een virtueel netwerk bijgewerkt |
> | Bewerking | Micro soft. netwerk/virtualNetworks/gebruik/lezen | De IP-gebruik voor elk subnet van het virtuele netwerk ophalen |
> | Bewerking | Micro soft. Network/virtualNetworks/informatie/lezen | Hiermee worden verwijzingen naar alle virtuele machines in een virtueel netwerk opgehaald |
> | Bewerking | Micro soft. Network/virtualNetworks/virtualNetworkPeerings/verwijderen | Hiermee wordt een virtueel netwerk peering verwijderd |
> | Bewerking | Micro soft. Network/virtualNetworks/virtualNetworkPeerings/lezen | Hiermee wordt een definitie van een virtuele netwerk-peering opgehaald |
> | Bewerking | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Hiermee maakt u een virtueel netwerk of werkt u een bestaande virtuele netwerk peering bij |
> | Bewerking | Micro soft. Network/virtualNetworks/schrijven | Hiermee wordt een virtueel netwerk gemaakt of een bestaand virtueel netwerk bijgewerkt |
> | Bewerking | Micro soft. Network/virtualNetworkTaps/verwijderen | Virtual Network tikken verwijderen |
> | Bewerking | Micro soft. Network/virtualNetworkTaps/samen voegen/actie | Tik op een virtueel netwerk tikken. Niet Alertable. |
> | Bewerking | Micro soft. Network/virtualNetworkTaps/lezen | Virtual Network tikken |
> | Bewerking | Micro soft. Network/virtualNetworkTaps/schrijven | Virtual Network tikken maken of bijwerken |
> | Bewerking | Micro soft. Network/virtualRouters/verwijderen | Hiermee wordt een VirtualRouter verwijderd |
> | Bewerking | Micro soft. Network/virtualRouters/samen voegen/actie | Er wordt een VirtualRouter toegevoegd. Niet alertable. |
> | Bewerking | Micro soft. Network/virtualRouters/lezen | Hiermee wordt een VirtualRouter opgehaald |
> | Bewerking | Micro soft. Network/virtualRouters/virtualRouterPeerings/verwijderen | Hiermee wordt een VirtualRouterPeering verwijderd |
> | Bewerking | Micro soft. Network/virtualRouters/virtualRouterPeerings/lezen | Hiermee wordt een VirtualRouterPeering opgehaald |
> | Bewerking | Micro soft. Network/virtualRouters/virtualRouterPeerings/schrijven | Hiermee maakt u een VirtualRouterPeering of werkt u een bestaande VirtualRouterPeering bij |
> | Bewerking | Micro soft. Network/virtualRouters/schrijven | Hiermee maakt u een VirtualRouter of werkt u een bestaande VirtualRouter bij |
> | Bewerking | Micro soft. Network/virtualWans/verwijderen | Hiermee verwijdert u een virtueel WAN |
> | Bewerking | Micro soft. Network/virtualwans/generateVpnProfile/Action | VirtualWanVpnServerConfiguration VpnProfile genereren |
> | Bewerking | Micro soft. Network/virtualWans/lezen | Een virtueel WAN ophalen |
> | Bewerking | Micro soft. Network/virtualwans/supportedSecurityProviders/lezen | Hiermee worden ondersteunde VirtualWan-beveiligings providers opgehaald. |
> | Bewerking | Micro soft. Network/virtualWans/virtualHubs/lezen | Hiermee worden alle virtuele hubs opgehaald die naar een virtueel WAN verwijzen. |
> | Bewerking | Micro soft. Network/virtualwans/vpnconfiguration/Action | Hiermee wordt een VPN-configuratie opgehaald |
> | Bewerking | Micro soft. Network/virtualwans/vpnServerConfigurations/Action | VirtualWanVpnServerConfigurations ophalen |
> | Bewerking | Micro soft. Network/virtualWans/vpnSites/lezen | Hiermee worden alle VPN-sites opgehaald die naar een virtueel WAN verwijzen. |
> | Bewerking | Micro soft. Network/virtualWans/schrijven | Een virtueel WAN maken of bijwerken |
> | Bewerking | Micro soft. Network/vpnGateways/verwijderen | Hiermee verwijdert u een VpnGateway. |
> | Bewerking | micro soft. Network/vpngateways/listvpnconnectionshealth/Action | Hiermee wordt de status van de verbinding voor alle of een subset van verbindingen op een VpnGateway |
> | Bewerking | Micro soft. Network/vpnGateways/lezen | Hiermee wordt een VpnGateway opgehaald. |
> | Bewerking | micro soft. Network/vpngateways/reset/Action | Hiermee wordt een VpnGateway opnieuw ingesteld |
> | Bewerking | micro soft. Network/vpnGateways/vpnConnections/verwijderen | Hiermee verwijdert u een VpnConnection. |
> | Bewerking | micro soft. Network/vpnGateways/vpnConnections/lezen | Hiermee wordt een VpnConnection opgehaald. |
> | Bewerking | micro soft. Network/vpnGateways/vpnConnections/vpnLinkConnections/lezen | Hiermee wordt een VPN-koppelings verbinding opgehaald |
> | Bewerking | micro soft. Network/vpnGateways/vpnConnections/schrijven | Hiermee wordt een VpnConnection geplaatst. |
> | Bewerking | Micro soft. Network/vpnGateways/schrijven | Hiermee wordt een VpnGateway geplaatst. |
> | Bewerking | Micro soft. Network/vpnServerConfigurations/verwijderen | VpnServerConfiguration verwijderen |
> | Bewerking | Micro soft. Network/vpnServerConfigurations/lezen | VpnServerConfiguration ophalen |
> | Bewerking | Micro soft. Network/vpnServerConfigurations/schrijven | VpnServerConfiguration maken of bijwerken |
> | Bewerking | Micro soft. Network/vpnsites/verwijderen | Hiermee verwijdert u een bron van een VPN-site. |
> | Bewerking | Micro soft. Network/vpnsites/lezen | Hiermee wordt een bron van een VPN-site opgehaald. |
> | Bewerking | micro soft. Network/vpnSites/vpnSiteLinks/lezen | Hiermee wordt een VPN-site koppeling opgehaald |
> | Bewerking | Micro soft. Network/vpnsites/schrijven | Hiermee wordt een bron van een VPN-site gemaakt of bijgewerkt. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. notification hubs/CheckNamespaceAvailability/actie | Hiermee wordt gecontroleerd of een opgegeven naam ruimte resource beschikbaar is in de NotificationHub-service. |
> | Bewerking | Micro soft. notification hubs/naam ruimten/authorizationRules/actie | De lijst met beschrijvingen van autorisatie regels voor naam ruimten ophalen. |
> | Bewerking | Micro soft. notification hubs/naam ruimten/authorizationRules/verwijderen | Naam ruimte autorisatie regel verwijderen. De standaard regel voor autorisatie van de naam ruimte kan niet worden verwijderd.  |
> | Bewerking | Micro soft. notification hubs/naam ruimten/authorizationRules/listkeys ophalen/actie | De verbindings reeks ophalen voor de naam ruimte |
> | Bewerking | Micro soft. notification hubs/naam ruimten/authorizationRules/lezen | De lijst met beschrijvingen van autorisatie regels voor naam ruimten ophalen. |
> | Bewerking | Micro soft. notification hubs/naam ruimten/authorizationRules/regenerateKeys/actie | Regel voor naam ruimte autorisatie primaire/secundaire sleutel opnieuw genereren, de sleutel opgeven die opnieuw moet worden gegenereerd |
> | Bewerking | Micro soft. notification hubs/naam ruimten/authorizationRules/schrijven | Maak autorisatie regels voor het naam ruimte niveau en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels, de primaire en secundaire sleutels kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. notification hubs/naam ruimten/CheckNotificationHubAvailability/actie | Hiermee wordt gecontroleerd of een opgegeven NotificationHub-naam beschikbaar is in een naam ruimte. |
> | Bewerking | Micro soft. notification hubs/naam ruimten/verwijderen | Naam ruimte resource verwijderen |
> | Bewerking | Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules/actie | De lijst met autorisatie regels voor notification hub ophalen |
> | Bewerking | Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules/verwijderen | Autorisatie regels voor notification hub verwijderen |
> | Bewerking | Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules/listkeys ophalen/actie | De verbindings reeks ophalen voor de notification hub |
> | Bewerking | Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules/lezen | De lijst met autorisatie regels voor notification hub ophalen |
> | Bewerking | Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules/regenerateKeys/actie | Verificatie regel voor notification hub opnieuw genereren primair/secundaire sleutel, de sleutel opgeven die opnieuw moet worden gegenereerd |
> | Bewerking | Micro soft. notification hubs/naam ruimten/notification hubs/authorizationRules/schrijven | Maak autorisatie regels voor de notification hub en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels, de primaire en secundaire sleutels kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. notification hubs/naam ruimten/notification hubs/debugSend/actie | Een test push melding verzenden. |
> | Bewerking | Micro soft. notification hubs/naam ruimten/notification hubs/verwijderen | Resource voor notification hub verwijderen |
> | Bewerking | Micro soft. notification hubs/naam ruimten/notification hubs/metricDefinitions/lezen | Lijst met resource beschrijvingen voor metrische gegevens van de naam ruimte ophalen |
> | Bewerking | Micro soft. notification hubs/naam ruimten/notification hubs/pnsCredentials/actie | Alle PNS-referenties voor notification hub ophalen. Dit omvat, WNS, MPNS, APNS-, GCM-en Baidu-referenties |
> | Bewerking | Micro soft. notification hubs/naam ruimten/notification hubs/lezen | Lijst met resource beschrijvingen voor notification hub ophalen |
> | Bewerking | Micro soft. notification hubs/naam ruimten/notification hubs/schrijven | Een notification hub maken en de eigenschappen ervan bijwerken. De eigenschappen omvatten voornamelijk PNS-referenties. Autorisatie regels en TTL |
> | Bewerking | Micro soft. notification hubs/naam ruimten/lezen | De lijst met de resource beschrijving van de naam ruimte ophalen |
> | Bewerking | Micro soft. notification hubs/naam ruimten/schrijven | Maak een naam ruimte resource en werk de eigenschappen bij. Tags en capaciteit van de naam ruimte zijn de eigenschappen die kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. notification hubs/operationResults/lezen | Hiermee worden de bewerkings resultaten voor Notification Hubs provider geretourneerd |
> | Bewerking | Micro soft. notification hubs/Operations/lezen | Hiermee wordt een lijst met ondersteunde bewerkingen voor Notification Hubs provider geretourneerd |
> | Bewerking | Micro soft. notification hubs/REGI ster/actie | Hiermee wordt het abonnement voor de notification hubs-resource provider geregistreerd en wordt het maken van naam ruimten en notification hubs |
> | Bewerking | Micro soft. notification hubs/registratie/actie | Hiermee wordt de registratie van het abonnement voor de notification hubs-resource provider ongedaan gemaakt en wordt het maken van naam ruimten en notification hubs |

## <a name="microsoftoffazure"></a>Micro soft. OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. OffAzure/HyperVSites/clusters/lezen | Hiermee worden de eigenschappen van een Hyper-V-cluster opgehaald |
> | Bewerking | Micro soft. OffAzure/HyperVSites/clusters/schrijven | Hiermee wordt het Hyper-V-cluster gemaakt of bijgewerkt |
> | Bewerking | Micro soft. OffAzure/HyperVSites/verwijderen | Hiermee verwijdert u de Hyper-V-site |
> | Bewerking | Micro soft. OffAzure/HyperVSites/healthsummary/lezen | Hiermee wordt het status overzicht voor Hyper-V-resource opgehaald |
> | Bewerking | Micro soft. OffAzure/HyperVSites/hosts/lezen | Hiermee worden de eigenschappen van een Hyper-V-host opgehaald |
> | Bewerking | Micro soft. OffAzure/HyperVSites/hosts/write | Hiermee wordt de Hyper-V-host gemaakt of bijgewerkt |
> | Bewerking | Micro soft. OffAzure/HyperVSites/Jobs/lezen | Hiermee worden de eigenschappen van een Hyper-V-taak opgehaald |
> | Bewerking | Micro soft. OffAzure/HyperVSites/machines/lezen | Hiermee worden de eigenschappen van een Hyper-V-computer opgehaald |
> | Bewerking | Micro soft. OffAzure/HyperVSites/operationsstatus/lezen | Hiermee worden de eigenschappen van de status van een Hyper-V-bewerking opgehaald |
> | Bewerking | Micro soft. OffAzure/HyperVSites/lezen | Hiermee worden de eigenschappen van een Hyper-V-site opgehaald |
> | Bewerking | Micro soft. OffAzure/HyperVSites/Refresh/actie | Hiermee worden de objecten op een Hyper-V-site vernieuwd |
> | Bewerking | Micro soft. OffAzure/HyperVSites/runasaccounts/lezen | Hiermee worden de eigenschappen van een Hyper-V run as-accounts opgehaald |
> | Bewerking | Micro soft. OffAzure/HyperVSites/gebruik/lezen | Hiermee worden de gebruiks handelingen van een Hyper-V-site opgehaald |
> | Bewerking | Micro soft. OffAzure/HyperVSites/schrijven | Hiermee wordt de Hyper-V-site gemaakt of bijgewerkt |
> | Bewerking | Micro soft. OffAzure/Operations/lezen | Hiermee worden de beschik bare bewerkingen gelezen |
> | Bewerking | Micro soft. OffAzure/REGI ster/actie | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. OffAzure |
> | Bewerking | Micro soft. OffAzure/ServerSites/Jobs/lezen | Hiermee worden de eigenschappen van een server taak opgehaald |
> | Bewerking | Micro soft. OffAzure/ServerSites/machines/lezen | Hiermee worden de eigenschappen van een Server computer opgehaald |
> | Bewerking | Micro soft. OffAzure/ServerSites/machines/schrijven | De eigenschappen van een Server computer schrijven |
> | Bewerking | Micro soft. OffAzure/ServerSites/operationsstatus/lezen | Hiermee worden de eigenschappen van de bewerkings status van de server opgehaald |
> | Bewerking | Micro soft. OffAzure/ServerSites/lezen | Hiermee worden de eigenschappen van een server site opgehaald |
> | Bewerking | Micro soft. OffAzure/ServerSites/Refresh/actie | Hiermee worden de objecten in een server site vernieuwd |
> | Bewerking | Micro soft. OffAzure/ServerSites/runasaccounts/lezen | Hiermee worden de eigenschappen van een run as-account van een server opgehaald |
> | Bewerking | Micro soft. OffAzure/ServerSites/gebruik/lezen | Hiermee worden de gebruiks locaties van een server site opgehaald |
> | Bewerking | Micro soft. OffAzure/ServerSites/schrijven | Hiermee wordt de server site gemaakt of bijgewerkt |
> | Bewerking | Micro soft. OffAzure/VMwareSites/verwijderen | Hiermee verwijdert u de VMware-site |
> | Bewerking | Micro soft. OffAzure/VMwareSites/healthsummary/lezen | Hiermee wordt het status overzicht voor VMware-resource opgehaald |
> | Bewerking | Micro soft. OffAzure/VMwareSites/Jobs/lezen | Hiermee worden de eigenschappen van een VMware-taak opgehaald |
> | Bewerking | Micro soft. OffAzure/VMwareSites/machines/lezen | Hiermee worden de eigenschappen van een VMware-machine opgehaald |
> | Bewerking | Micro soft. OffAzure/VMwareSites/machines/start/actie | VMware-machines starten |
> | Bewerking | Micro soft. OffAzure/VMwareSites/machines/stoppen/actie | Stopt de VMware-machines |
> | Bewerking | Micro soft. OffAzure/VMwareSites/operationsstatus/lezen | Hiermee worden de eigenschappen van een VMware-bewerkings status opgehaald |
> | Bewerking | Micro soft. OffAzure/VMwareSites/lezen | Hiermee worden de eigenschappen van een VMware-site opgehaald |
> | Bewerking | Micro soft. OffAzure/VMwareSites/Refresh/actie | Hiermee worden de objecten in een VMware-site vernieuwd |
> | Bewerking | Micro soft. OffAzure/VMwareSites/runasaccounts/lezen | Hiermee worden de eigenschappen van een VMware run as-accounts opgehaald |
> | Bewerking | Micro soft. OffAzure/VMwareSites/gebruik/lezen | Hiermee worden de gebruiks handelingen van een VMware-site opgehaald |
> | Bewerking | Micro soft. OffAzure/VMwareSites/vCenter/lezen | Hiermee worden de eigenschappen van een VMware vCenter opgehaald |
> | Bewerking | Micro soft. OffAzure/VMwareSites/vCenter/write | Hiermee wordt de VMware vCenter gemaakt of bijgewerkt |
> | Bewerking | Micro soft. OffAzure/VMwareSites/schrijven | Hiermee wordt de VMware-site gemaakt of bijgewerkt |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. OperationalInsights/linkTargets/lezen | Een lijst met bestaande accounts die niet zijn gekoppeld aan een Azure-abonnement. Als u dit Azure-abonnement aan een werk ruimte wilt koppelen, gebruikt u een klant-id die door deze bewerking is geretourneerd in de eigenschap Customer ID van de bewerking werk ruimte maken. |
> | Bewerking | micro soft. operationalinsights/Operations/lezen | Een lijst met alle beschik bare OperationalInsights rest API-bewerkingen. |
> | Bewerking | micro soft. operationalinsights/REGI ster/actie | Rergisters het abonnement. |
> | Bewerking | Micro soft. OperationalInsights/REGI ster/actie | Een abonnement bij een resource provider registreren. |
> | Bewerking | micro soft. operationalinsights/registratie/actie | Hiermee maakt u de registratie van het abonnement ongedaan. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/Analytics/query/actie | Zoek met een nieuwe engine. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/analyse/query/schema/lezen | Zoek schema v2 ophalen. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/API/query/actie | Zoek met een nieuwe engine. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/API/query/schema/lezen | Zoek schema v2 ophalen. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/configurationScopes/verwijderen | Configuratie bereik verwijderen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/configurationScopes/lezen | Configuratie bereik ophalen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/configurationScopes/schrijven | Configuratie bereik instellen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/gegevens bronnen/verwijderen | Gegevens bronnen onder een werk ruimte verwijderen. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/gegevens bronnen/lezen | Gegevens bronnen onder een werk ruimte ophalen. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/gegevens bronnen/schrijven | Gegevens bronnen onder een werk ruimte maken/bijwerken. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/verwijderen | Hiermee verwijdert u een werk ruimte. Als de werk ruimte is gekoppeld aan een bestaande werk ruimte tijdens de aanmaak tijd, wordt de werk ruimte waaraan deze is gekoppeld, niet verwijderd. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/gateways/verwijderen | Hiermee verwijdert u een gateway die is geconfigureerd voor de werk ruimte. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/generateregistrationcertificate/actie | Hiermee wordt een registratie certificaat voor de werk ruimte gegenereerd. Dit certificaat wordt gebruikt om micro soft System Center Operations Manager te verbinden met de werk ruimte. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/Intelligence packs/uitschakelen/actie | Hiermee wordt een Intelligence Pack voor een opgegeven werk ruimte uitgeschakeld. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/Intelligence packs/inschakelen/actie | Hiermee wordt een Intelligence Pack voor een opgegeven werk ruimte ingeschakeld. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/Intelligence packs/lezen | Hiermee worden alle Intelligence packs weer gegeven die zichtbaar zijn voor een bepaalde werk ruimte en wordt ook vermeld of het pakket is ingeschakeld of uitgeschakeld voor die werk ruimte. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/linkedServices/verwijderen | Gekoppelde services onder de opgegeven werk ruimte verwijderen. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/linkedServices/lezen | Gekoppelde services onder de opgegeven werk ruimte ophalen. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/linkedServices/schrijven | Gekoppelde services onder de opgegeven werk ruimte maken/bijwerken. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/Listkeys ophalen/actie | Hiermee haalt u de lijst sleutels voor de werk ruimte op. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/Listkeys ophalen/lezen | Hiermee haalt u de lijst sleutels voor de werk ruimte op. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/managementGroups/lezen | Hiermee worden de namen en meta gegevens opgehaald voor System Center Operations Manager-beheer groepen die met deze werk ruimte zijn verbonden. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/metricDefinitions/lezen | Metrische definities ophalen onder werk ruimte |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/notificationSettings/verwijderen | De instellingen voor meldingen voor de gebruiker verwijderen voor de werk ruimte. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/notificationSettings/lezen | De instellingen voor meldingen voor de gebruiker ophalen voor de werk ruimte. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/notificationSettings/schrijven | De instellingen voor meldingen voor de gebruiker instellen voor de werk ruimte. |
> | Bewerking | micro soft. operationalinsights/werk ruimten/bewerkingen/lezen | Hiermee wordt de status van een OperationalInsights-werkruimte bewerking opgehaald. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/leegmaken/actie | Opgegeven gegevens uit de werk ruimte verwijderen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AADDomainServicesAccountLogon/lezen | Gegevens uit de AADDomainServicesAccountLogon-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AADDomainServicesAccountManagement/lezen | Gegevens uit de AADDomainServicesAccountManagement-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AADDomainServicesDirectoryServiceAccess/lezen | Gegevens uit de AADDomainServicesDirectoryServiceAccess-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AADDomainServicesLogonLogoff/lezen | Gegevens uit de AADDomainServicesLogonLogoff-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AADDomainServicesPolicyChange/lezen | Gegevens uit de AADDomainServicesPolicyChange-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AADDomainServicesPrivilegeUse/lezen | Gegevens uit de AADDomainServicesPrivilegeUse-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AADDomainServicesSystemSecurity/lezen | Gegevens uit de AADDomainServicesSystemSecurity-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ADAssessmentRecommendation/lezen | Gegevens uit de ADAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AddonAzureBackupAlerts/lezen | Gegevens uit de AddonAzureBackupAlerts-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AddonAzureBackupJobs/lezen | Gegevens uit de AddonAzureBackupJobs-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AddonAzureBackupPolicy/lezen | Gegevens uit de AddonAzureBackupPolicy-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AddonAzureBackupProtectedInstance/lezen | Gegevens uit de AddonAzureBackupProtectedInstance-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AddonAzureBackupStorage/lezen | Gegevens uit de AddonAzureBackupStorage-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ADFActivityRun/lezen | Gegevens uit de ADFActivityRun-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ADFPipelineRun/lezen | Gegevens uit de ADFPipelineRun-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ADFTriggerRun/lezen | Gegevens uit de ADFTriggerRun-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ADReplicationResult/lezen | Gegevens uit de ADReplicationResult-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ADSecurityAssessmentRecommendation/lezen | Gegevens uit de ADSecurityAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/waarschuwing/lezen | Gegevens uit de tabel waarschuwing lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AlertHistory/lezen | Gegevens uit de AlertHistory-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AmlComputeClusterEvent/lezen | Gegevens uit de AmlComputeClusterEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AmlComputeClusterNodeEvent/lezen | Gegevens uit de AmlComputeClusterNodeEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AmlComputeJobEvent/lezen | Gegevens uit de AmlComputeJobEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ApiManagementGatewayLogs/lezen | Gegevens uit de ApiManagementGatewayLogs-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AppCenterError/lezen | Gegevens uit de AppCenterError-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ApplicationInsights/lezen | Gegevens uit de ApplicationInsights-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AppPlatformLogsforSpring/lezen | Gegevens uit de AppPlatformLogsforSpring-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AppServiceEnvironmentPlatformLogs/lezen | Gegevens uit de AppServiceEnvironmentPlatformLogs-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/audit logs bevat/lezen | Gegevens uit de audit logs bevat-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AutoscaleEvaluationsLog/lezen | Gegevens uit de AutoscaleEvaluationsLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AutoscaleScaleActionsLog/lezen | Gegevens uit de AutoscaleScaleActionsLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AWSCloudTrail/lezen | Gegevens uit de AWSCloudTrail-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AzureActivity/lezen | Gegevens uit de AzureActivity-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AzureAssessmentRecommendation/lezen | Gegevens uit de AzureAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/AzureMetrics/lezen | Gegevens uit de AzureMetrics-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/BaiClusterEvent/lezen | Gegevens uit de BaiClusterEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/BaiClusterNodeEvent/lezen | Gegevens uit de BaiClusterNodeEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/BaiJobEvent/lezen | Gegevens uit de BaiJobEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/BlockchainApplicationLog/lezen | Gegevens uit de BlockchainApplicationLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/BlockchainProxyLog/lezen | Gegevens uit de BlockchainProxyLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/BoundPort/lezen | Gegevens uit de BoundPort-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/CommonSecurityLog/lezen | Gegevens uit de CommonSecurityLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ComputerGroup/lezen | Gegevens uit de ComputerGroup-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ConfigurationChange/lezen | Gegevens uit de ConfigurationChange-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ConfigurationData/lezen | Gegevens uit de ConfigurationData-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ContainerImageInventory/lezen | Gegevens uit de ContainerImageInventory-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ContainerInventory/lezen | Gegevens uit de ContainerInventory-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ContainerLog/lezen | Gegevens uit de ContainerLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ContainerNodeInventory/lezen | Gegevens uit de ContainerNodeInventory-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ContainerRegistryLoginEvents/lezen | Gegevens uit de ContainerRegistryLoginEvents-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ContainerRegistryRepositoryEvents/lezen | Gegevens uit de ContainerRegistryRepositoryEvents-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ContainerServiceLog/lezen | Gegevens uit de ContainerServiceLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/CoreAzureBackup/lezen | Gegevens uit de CoreAzureBackup-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DatabricksAccounts/lezen | Gegevens uit de DatabricksAccounts-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DatabricksClusters/lezen | Gegevens uit de DatabricksClusters-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DatabricksDBFS/lezen | Gegevens uit de DatabricksDBFS-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DatabricksInstancePools/lezen | Gegevens uit de DatabricksInstancePools-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DatabricksJobs/lezen | Gegevens uit de DatabricksJobs-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DatabricksNotebook/lezen | Gegevens uit de DatabricksNotebook-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DatabricksSecrets/lezen | Gegevens uit de DatabricksSecrets-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DatabricksSQLPermissions/lezen | Gegevens uit de DatabricksSQLPermissions-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DatabricksSSH/lezen | Gegevens uit de DatabricksSSH-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DatabricksTables/lezen | Gegevens uit de DatabricksTables-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DatabricksWorkspace/lezen | Gegevens uit de DatabricksWorkspace-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DeviceAppCrash/lezen | Gegevens uit de DeviceAppCrash-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DeviceAppLaunch/lezen | Gegevens uit de DeviceAppLaunch-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DeviceCalendar/lezen | Gegevens uit de DeviceCalendar-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DeviceCleanup/lezen | Gegevens uit de DeviceCleanup-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DeviceConnectSession/lezen | Gegevens uit de DeviceConnectSession-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DeviceEtw/lezen | Gegevens uit de DeviceEtw-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DeviceHardwareHealth/lezen | Gegevens uit de DeviceHardwareHealth-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/Apparaatstatus/lezen | Gegevens uit de apparaatstatus-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DeviceHeartbeat/lezen | Gegevens uit de DeviceHeartbeat-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DeviceSkypeHeartbeat/lezen | Gegevens uit de DeviceSkypeHeartbeat-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DeviceSkypeSignIn/lezen | Gegevens uit de DeviceSkypeSignIn-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DeviceSleepState/lezen | Gegevens uit de DeviceSleepState-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DHAppFailure/lezen | Gegevens uit de DHAppFailure-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DHAppReliability/lezen | Gegevens uit de DHAppReliability-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DHDriverReliability/lezen | Gegevens uit de DHDriverReliability-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DHLogonFailures/lezen | Gegevens uit de DHLogonFailures-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DHLogonMetrics/lezen | Gegevens uit de DHLogonMetrics-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DHOSCrashData/lezen | Gegevens uit de DHOSCrashData-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DHOSReliability/lezen | Gegevens uit de DHOSReliability-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DHWipAppLearning/lezen | Gegevens uit de DHWipAppLearning-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DnsEvents/lezen | Gegevens uit de DnsEvents-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/DnsInventory/lezen | Gegevens uit de DnsInventory-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ETWEvent/lezen | Gegevens uit de ETWEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/gebeurtenis/lezen | Gegevens uit de gebeurtenis tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ExchangeAssessmentRecommendation/lezen | Gegevens uit de ExchangeAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ExchangeOnlineAssessmentRecommendation/lezen | Gegevens uit de ExchangeOnlineAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/FailedIngestion/lezen | Gegevens uit de FailedIngestion-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/heartbeat/lezen | Gegevens uit de heartbeat-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/HuntingBookmark/lezen | Gegevens uit de HuntingBookmark-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/IISAssessmentRecommendation/lezen | Gegevens uit de IISAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/InboundConnection/lezen | Gegevens uit de InboundConnection-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/InsightsMetrics/lezen | Gegevens uit de InsightsMetrics-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/IntuneAuditLogs/lezen | Gegevens uit de IntuneAuditLogs-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/IntuneOperationalLogs/lezen | Gegevens uit de IntuneOperationalLogs-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/KubeEvents/lezen | Gegevens uit de KubeEvents-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/KubeHealth/lezen | Gegevens uit de KubeHealth-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/KubeNodeInventory/lezen | Gegevens uit de KubeNodeInventory-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/KubePodInventory/lezen | Gegevens uit de KubePodInventory-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/KubeServices/lezen | Gegevens uit de KubeServices-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/LinuxAuditLog/lezen | Gegevens uit de LinuxAuditLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAApplication/lezen | Gegevens uit de MAApplication-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAApplicationHealth/lezen | Gegevens uit de MAApplicationHealth-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAApplicationHealthAlternativeVersions/lezen | Gegevens uit de MAApplicationHealthAlternativeVersions-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAApplicationHealthIssues/lezen | Gegevens uit de MAApplicationHealthIssues-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAApplicationInstance/lezen | Gegevens uit de MAApplicationInstance-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAApplicationInstanceReadiness/lezen | Gegevens uit de MAApplicationInstanceReadiness-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAApplicationReadiness/lezen | Gegevens uit de MAApplicationReadiness-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MADeploymentPlan/lezen | Gegevens uit de MADeploymentPlan-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MADevice/lezen | Gegevens uit de MADevice-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MADeviceNotEnrolled/lezen | Gegevens uit de MADeviceNotEnrolled-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MADeviceNRT/lezen | Gegevens uit de MADeviceNRT-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MADevicePnPHealth/lezen | Gegevens uit de MADevicePnPHealth-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MADevicePnPHealthAlternativeVersions/lezen | Gegevens uit de MADevicePnPHealthAlternativeVersions-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MADevicePnPHealthIssues/lezen | Gegevens uit de MADevicePnPHealthIssues-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MADeviceReadiness/lezen | Gegevens uit de MADeviceReadiness-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MADriverInstanceReadiness/lezen | Gegevens uit de MADriverInstanceReadiness-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MADriverReadiness/lezen | Gegevens uit de MADriverReadiness-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAddin/lezen | Gegevens uit de MAOfficeAddin-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAddinEntityHealth/lezen | Gegevens uit de MAOfficeAddinEntityHealth-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAddinHealth/lezen | Gegevens uit de MAOfficeAddinHealth-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAddinHealthEventNRT/lezen | Gegevens uit de MAOfficeAddinHealthEventNRT-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAddinHealthIssues/lezen | Gegevens uit de MAOfficeAddinHealthIssues-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAddinInstance/lezen | Gegevens uit de MAOfficeAddinInstance-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAddinInstanceReadiness/lezen | Gegevens uit de MAOfficeAddinInstanceReadiness-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAddinReadiness/lezen | Gegevens uit de MAOfficeAddinReadiness-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeApp/lezen | Gegevens uit de MAOfficeApp-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAppCrashesNRT/lezen | Gegevens uit de MAOfficeAppCrashesNRT-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAppHealth/lezen | Gegevens uit de MAOfficeAppHealth-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAppInstance/lezen | Gegevens uit de MAOfficeAppInstance-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAppInstanceHealth/lezen | Gegevens uit de MAOfficeAppInstanceHealth-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAppReadiness/lezen | Gegevens uit de MAOfficeAppReadiness-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeAppSessionsNRT/lezen | Gegevens uit de MAOfficeAppSessionsNRT-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeBuildInfo/lezen | Gegevens uit de MAOfficeBuildInfo-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeCurrencyAssessment/lezen | Gegevens uit de MAOfficeCurrencyAssessment-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeCurrencyAssessmentDailyCounts/lezen | Gegevens uit de MAOfficeCurrencyAssessmentDailyCounts-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeDeploymentStatus/lezen | Gegevens uit de MAOfficeDeploymentStatus-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeDeploymentStatusNRT/lezen | Gegevens uit de MAOfficeDeploymentStatusNRT-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeMacroErrorNRT/lezen | Gegevens uit de MAOfficeMacroErrorNRT-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeMacroGlobalHealth/lezen | Gegevens uit de MAOfficeMacroGlobalHealth-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeMacroHealth/lezen | Gegevens uit de MAOfficeMacroHealth-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeMacroHealthIssues/lezen | Gegevens uit de MAOfficeMacroHealthIssues-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeMacroIssueInstanceReadiness/lezen | Gegevens uit de MAOfficeMacroIssueInstanceReadiness-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeMacroIssueReadiness/lezen | Gegevens uit de MAOfficeMacroIssueReadiness-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeMacroSummary/lezen | Gegevens uit de MAOfficeMacroSummary-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeSuite/lezen | Gegevens uit de MAOfficeSuite-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAOfficeSuiteInstance/lezen | Gegevens uit de MAOfficeSuiteInstance-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAProposedPilotDevices/lezen | Gegevens uit de MAProposedPilotDevices-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAWindowsBuildInfo/lezen | Gegevens uit de MAWindowsBuildInfo-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAWindowsCurrencyAssessment/lezen | Gegevens uit de MAWindowsCurrencyAssessment-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAWindowsCurrencyAssessmentDailyCounts/lezen | Gegevens uit de MAWindowsCurrencyAssessmentDailyCounts-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAWindowsDeploymentStatus/lezen | Gegevens uit de MAWindowsDeploymentStatus-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAWindowsDeploymentStatusNRT/lezen | Gegevens uit de MAWindowsDeploymentStatusNRT-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MAWindowsSysReqInstanceReadiness/lezen | Gegevens uit de MAWindowsSysReqInstanceReadiness-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/McasShadowItReporting/lezen | Gegevens uit de McasShadowItReporting-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftDataShareShareLog/lezen | Gegevens uit de MicrosoftDataShareShareLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftHealthcareApisAuditLogs/lezen | Gegevens uit de MicrosoftHealthcareApisAuditLogs-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftInsightsAzureActivityLog/lezen | Gegevens uit de MicrosoftInsightsAzureActivityLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftWebApplicationLog/lezen | Gegevens uit de MicrosoftWebApplicationLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftWebFunctionExecutionLogs/lezen | Gegevens uit de MicrosoftWebFunctionExecutionLogs-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftWebStdOutStdErrLog/lezen | Gegevens uit de MicrosoftWebStdOutStdErrLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/MicrosoftWebW3CLog/lezen | Gegevens uit de MicrosoftWebW3CLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/NetworkMonitoring/lezen | Gegevens uit de NetworkMonitoring-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/OfficeActivity/lezen | Gegevens uit de OfficeActivity-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/bewerking/lezen | Gegevens uit de bewerkings tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/OutboundConnection/lezen | Gegevens uit de OutboundConnection-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/perf/lezen | Gegevens uit de prestatie tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ProtectionStatus/lezen | Gegevens uit de ProtectionStatus-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/lezen | Query's uitvoeren voor de gegevens in de werk ruimte |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ReservedAzureCommonFields/lezen | Gegevens uit de ReservedAzureCommonFields-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ReservedCommonFields/lezen | Gegevens uit de ReservedCommonFields-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SCCMAssessmentRecommendation/lezen | Gegevens uit de SCCMAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SCOMAssessmentRecommendation/lezen | Gegevens uit de SCOMAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SecurityAlert/lezen | Gegevens uit de SecurityAlert-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/Security Baseline Baseline/lezen | Gegevens uit de Security Baseline Baseline-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/Summary/lezen | Gegevens uit de Summary-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SecurityDetection/lezen | Gegevens uit de SecurityDetection-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SecurityEvent/lezen | Gegevens uit de SecurityEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SecurityIoTRawEvent/lezen | Gegevens uit de SecurityIoTRawEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SecurityRecommendation/lezen | Gegevens uit de SecurityRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ServiceFabricOperationalEvent/lezen | Gegevens uit de ServiceFabricOperationalEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ServiceFabricReliableActorEvent/lezen | Gegevens uit de ServiceFabricReliableActorEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ServiceFabricReliableServiceEvent/lezen | Gegevens uit de ServiceFabricReliableServiceEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SfBAssessmentRecommendation/lezen | Gegevens uit de SfBAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SfBOnlineAssessmentRecommendation/lezen | Gegevens uit de SfBOnlineAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SharePointOnlineAssessmentRecommendation/lezen | Gegevens uit de SharePointOnlineAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SigninLogs/lezen | Gegevens uit de SigninLogs-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SPAssessmentRecommendation/lezen | Gegevens uit de SPAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SQLAssessmentRecommendation/lezen | Gegevens uit de SQLAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SQLQueryPerformance/lezen | Gegevens uit de SQLQueryPerformance-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SqlThreatProtectionLoginAudits/lezen | Gegevens uit de SqlThreatProtectionLoginAudits-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SqlVulnerabilityAssessmentResult/lezen | Gegevens uit de SqlVulnerabilityAssessmentResult-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SucceededIngestion/lezen | Gegevens uit de SucceededIngestion-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/syslog/lezen | Gegevens uit de syslog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/SysmonEvent/lezen | Gegevens uit de SysmonEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/tabellen. aangepast/lezen | Gegevens uit een aangepast logboek lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/ThreatIntelligenceIndicator/lezen | Gegevens uit de ThreatIntelligenceIndicator-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/UAApp/lezen | Gegevens uit de UAApp-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/UAComputer/lezen | Gegevens uit de UAComputer-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/UAComputerRank/lezen | Gegevens uit de UAComputerRank-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/UADriver/lezen | Gegevens uit de UADriver-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/UADriverProblemCodes/lezen | Gegevens uit de UADriverProblemCodes-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/UAFeedback/lezen | Gegevens uit de UAFeedback-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/UAHardwareSecurity/lezen | Gegevens uit de UAHardwareSecurity-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/UAIESiteDiscovery/lezen | Gegevens uit de UAIESiteDiscovery-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/UAOfficeAddIn/lezen | Gegevens uit de UAOfficeAddIn-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/UAProposedActionPlan/lezen | Gegevens uit de UAProposedActionPlan-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/UASysReqIssue/lezen | Gegevens uit de UASysReqIssue-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/UAUpgradedComputer/lezen | Gegevens uit de UAUpgradedComputer-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/update/lezen | Gegevens uit de update tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/Updaterunprogress Installation/lezen | Gegevens uit de Updaterunprogress Installation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/update Summary/lezen | Gegevens uit de update Summary-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/gebruik/lezen | Gegevens uit de gebruiks tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/VMBoundPort/lezen | Gegevens uit de VMBoundPort-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/VMConnection/lezen | Gegevens uit de VMConnection-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/W3CIISLog/lezen | Gegevens uit de W3CIISLog-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WaaSDeploymentStatus/lezen | Gegevens uit de WaaSDeploymentStatus-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WaaSInsiderStatus/lezen | Gegevens uit de WaaSInsiderStatus-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WaaSUpdateStatus/lezen | Gegevens uit de WaaSUpdateStatus-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WDAVStatus/lezen | Gegevens uit de WDAVStatus-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WDAVThreat/lezen | Gegevens uit de WDAVThreat-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WindowsClientAssessmentRecommendation/lezen | Gegevens uit de WindowsClientAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WindowsEvent/lezen | Gegevens uit de WindowsEvent-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WindowsFirewall/lezen | Gegevens uit de WindowsFirewall-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WindowsServerAssessmentRecommendation/lezen | Gegevens uit de WindowsServerAssessmentRecommendation-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WireData/lezen | Gegevens uit de WireData-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WorkloadMonitoringPerf/lezen | Gegevens uit de WorkloadMonitoringPerf-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WUDOAggregatedStatus/lezen | Gegevens uit de WUDOAggregatedStatus-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/query/WUDOStatus/lezen | Gegevens uit de WUDOStatus-tabel lezen |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/lezen | Hiermee wordt een bestaande werk ruimte opgehaald |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/regeneratesharedkey/actie | Hiermee wordt de opgegeven gedeelde sleutel voor de werk ruimte opnieuw gegenereerd |
> | Bewerking | micro soft. operationalinsights/werk ruimten/regels/lezen | Alle waarschuwings regels ophalen. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/savedSearches/verwijderen | Hiermee wordt een opgeslagen Zoek query verwijderd |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/savedSearches/lezen | Hiermee wordt een opgeslagen Zoek query opgehaald |
> | Bewerking | micro soft. operationalinsights/werk ruimten/savedsearches/resultaten/lezen | Resultaten voor opgeslagen Zoek opdrachten ophalen. Afgeschaft |
> | Bewerking | micro soft. operationalinsights/werk ruimten/savedsearches/planning/acties/verwijderen | Geplande zoek acties verwijderen. |
> | Bewerking | micro soft. operationalinsights/werk ruimten/savedsearches/planning/acties/lezen | Geplande zoek acties ophalen. |
> | Bewerking | micro soft. operationalinsights/werk ruimten/savedsearches/planning/acties/schrijven | Geplande zoek acties maken of bijwerken. |
> | Bewerking | micro soft. operationalinsights/werk ruimten/savedsearches/planning/verwijderen | Geplande Zoek opdrachten verwijderen. |
> | Bewerking | micro soft. operationalinsights/werk ruimten/savedsearches/planning/lezen | Geplande Zoek opdrachten ophalen. |
> | Bewerking | micro soft. operationalinsights/werk ruimten/savedsearches/planningen/schrijven | Geplande Zoek opdrachten maken of bijwerken. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/savedSearches/schrijven | Hiermee wordt een opgeslagen Zoek query gemaakt |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/schema/lezen | Hiermee wordt het zoek schema voor de werk ruimte opgehaald.  Zoek schema bevat de weer gegeven velden en de bijbehorende typen. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/zoeken/actie | Hiermee wordt een zoek query uitgevoerd |
> | Bewerking | micro soft. operationalinsights/werk ruimten/zoeken/lezen | Zoek resultaten ophalen. Keur. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/sharedKeys/actie | Hiermee worden de gedeelde sleutels voor de werk ruimte opgehaald. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/sharedKeys/lezen | Hiermee worden de gedeelde sleutels voor de werk ruimte opgehaald. Deze sleutels worden gebruikt om micro soft Operational Insights-agents te verbinden met de werk ruimte. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/storageinsightconfigs/verwijderen | Hiermee verwijdert u een opslag configuratie. Hiermee wordt voor komen dat micro soft Operational Insights het lezen van gegevens uit het opslag account. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/storageinsightconfigs/lezen | Hiermee wordt een opslag configuratie opgehaald. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/storageinsightconfigs/schrijven | Hiermee maakt u een nieuwe opslag configuratie. Deze configuraties worden gebruikt voor het ophalen van gegevens van een locatie in een bestaand opslag account. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/upgradetranslationfailures/lezen | Fout logboek voor de upgrade van de zoek actie ophalen voor de werk ruimte |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/gebruik/lezen | Hiermee worden gebruiks gegevens voor een werk ruimte opgehaald, inclusief de hoeveelheid gegevens die door de werk ruimte wordt gelezen. |
> | Bewerking | Micro soft. OperationalInsights/werk ruimten/schrijven | Hiermee maakt u een nieuwe werk ruimte of koppelingen naar een bestaande werk ruimte door de klant-id van de bestaande werk ruimte op te geven. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. OperationsManagement/managementAssociations/verwijderen | Bestaande beheer koppeling verwijderen |
> | Bewerking | Micro soft. OperationsManagement/managementAssociations/lezen | Bestaande beheer koppeling ophalen |
> | Bewerking | Micro soft. OperationsManagement/managementAssociations/schrijven | Een nieuwe beheer koppeling maken |
> | Bewerking | Micro soft. OperationsManagement/managementConfigurations/verwijderen | Bestaande beheer configuratie verwijderen |
> | Bewerking | Micro soft. OperationsManagement/managementConfigurations/lezen | Bestaande beheer configuratie ophalen |
> | Bewerking | Micro soft. OperationsManagement/managementConfigurations/schrijven | Een nieuwe beheer configuratie maken |
> | Bewerking | Micro soft. OperationsManagement/REGI ster/actie | Een abonnement bij een resource provider registreren. |
> | Bewerking | Micro soft. OperationsManagement/oplossingen/verwijderen | Bestaande OMS-oplossing verwijderen |
> | Bewerking | Micro soft. OperationsManagement/Solutions/lezen | De OMS-oplossing ophalen |
> | Bewerking | Micro soft. OperationsManagement/oplossingen/schrijven | Nieuwe OMS-oplossing maken |

## <a name="microsoftpolicyinsights"></a>Micro soft. PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. PolicyInsights/asyncOperationResults/lezen | Hiermee wordt het resultaat van de asynchrone bewerking opgehaald. |
> | DataAction | Micro soft. PolicyInsights/checkDataPolicyCompliance/actie | Controleer de nalevings status van een bepaald onderdeel met gegevens beleid. |
> | Bewerking | Micro soft. PolicyInsights/Operations/lezen | Hiermee worden ondersteunde bewerkingen voor de micro soft. PolicyInsights-naam ruimte opgehaald |
> | DataAction | Micro soft. PolicyInsights/policyEvents/logDataEvents/Action | De gebeurtenissen van het bron onderdeel beleid registreren. |
> | Bewerking | Micro soft. PolicyInsights/policyEvents/queryResults/Action | Informatie over beleids gebeurtenissen opvragen. |
> | Bewerking | Micro soft. PolicyInsights/policyEvents/queryResults/lezen | Informatie over beleids gebeurtenissen opvragen. |
> | Bewerking | Micro soft. PolicyInsights/policyStates/queryResults/Action | Informatie over beleids statussen opvragen. |
> | Bewerking | Micro soft. PolicyInsights/policyStates/queryResults/lezen | Informatie over beleids statussen opvragen. |
> | Bewerking | Micro soft. PolicyInsights/policyStates/samen vatting/actie | Query samenvattings informatie over de meest recente beleids status. |
> | Bewerking | Micro soft. PolicyInsights/policyStates/samenvatten/lezen | Query samenvattings informatie over de meest recente beleids status. |
> | Bewerking | Micro soft. PolicyInsights/policyStates/triggerEvaluation/Action | Hiermee wordt een nieuwe compatibiliteits evaluatie geactiveerd voor het geselecteerde bereik. |
> | Bewerking | Micro soft. PolicyInsights/policyTrackedResources/queryResults/lezen | Query gegevens over de resources die vereist zijn voor DeployIfNotExists-beleid. |
> | Bewerking | Micro soft. PolicyInsights/REGI ster/actie | Hiermee wordt de micro soft Policy Insights-resource provider geregistreerd en worden er acties op uitgevoerd. |
> | Bewerking | Micro soft. PolicyInsights/herstel bewerkingen/annuleren/actie | Hiermee wordt de uitvoering van micro soft-beleids herstel geannuleerd. |
> | Bewerking | Micro soft. PolicyInsights/herstel bewerkingen/verwijderen | Verwijder beleids herstel. |
> | Bewerking | Micro soft. PolicyInsights/herbemiddelingen/listDeployments/lezen | Geeft een lijst van de implementaties die vereist zijn voor het door voeren van een beleid. |
> | Bewerking | Micro soft. PolicyInsights/herbemiddelingen/lezen | Beleids herstel ophalen. |
> | Bewerking | Micro soft. PolicyInsights/herstel bewerkingen/schrijven | Herstel bewerkingen van micro soft-beleid maken of bijwerken. |
> | Bewerking | Micro soft. PolicyInsights/registratie/actie | Hiermee wordt de registratie van de micro soft Policy Insights-resource provider ongedaan gemaakt. |

## <a name="microsoftportal"></a>Micro soft. Portal

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Portal/consoles/verwijderen | Hiermee verwijdert u het Cloud Shell-exemplaar. |
> | Bewerking | Micro soft. Portal/consoles/lezen | Hiermee wordt het Cloud Shell-exemplaar gelezen. |
> | Bewerking | Micro soft. Portal/consoles/schrijven | Een Cloud Shell-exemplaar maken of bijwerken. |
> | Bewerking | Micro soft. Portal/Dash boards/verwijderen | Hiermee verwijdert u het dash board uit het abonnement. |
> | Bewerking | Micro soft. Portal/Dash boards/lezen | Hiermee worden de Dash boards voor het abonnement gelezen. |
> | Bewerking | Micro soft. Portal/Dash boards/schrijven | Dash board toevoegen aan of wijzigen in een abonnement. |
> | Bewerking | Micro soft. Portal/registreren/actie | Registreren bij de portal |
> | Bewerking | Micro soft. Portal/usersettings/verwijderen | Hiermee verwijdert u de Cloud Shell gebruikers instellingen. |
> | Bewerking | Micro soft. Portal/usersettings/lezen | Hiermee worden de Cloud Shell gebruikers instellingen gelezen. |
> | Bewerking | Micro soft. Portal/usersettings/schrijven | Cloud Shell gebruikers instelling maken of bijwerken. |

## <a name="microsoftpowerbidedicated"></a>Micro soft. PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. PowerBIDedicated/capaciteiten/verwijderen | Hiermee wordt de toegewezen capaciteit van Power BI verwijderd. |
> | Bewerking | Micro soft. PowerBIDedicated/capaciteiten/lezen | Hiermee wordt de informatie opgehaald van de opgegeven Power BI toegewezen capaciteit. |
> | Bewerking | Micro soft. PowerBIDedicated/capaciteiten/hervatten/actie | Hiermee wordt de capaciteit hervat. |
> | Bewerking | Micro soft. PowerBIDedicated/capaciteiten/sku's/lezen | Beschik bare SKU-gegevens voor de capaciteit ophalen |
> | Bewerking | Micro soft. PowerBIDedicated/capaciteiten/onderbreken/actie | Hiermee wordt de capaciteit onderbroken. |
> | Bewerking | Micro soft. PowerBIDedicated/capaciteiten/schrijven | Hiermee wordt de opgegeven Power BI toegewezen capaciteit gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. PowerBIDedicated/locaties/checkNameAvailability/actie | Controleert of de naam van Power BI toegewezen capaciteit geldig is en niet wordt gebruikt. |
> | Bewerking | Micro soft. PowerBIDedicated/locaties/operationresults/lezen | Hiermee wordt de informatie opgehaald van het opgegeven bewerkings resultaat. |
> | Bewerking | Micro soft. PowerBIDedicated/locaties/operationstatuses/lezen | Hiermee wordt de informatie opgehaald van de opgegeven bewerkings status. |
> | Bewerking | Micro soft. PowerBIDedicated/Operations/lezen | Hiermee wordt de informatie over bewerkingen opgehaald |
> | Bewerking | Micro soft. PowerBIDedicated/REGI ster/actie | Registreert Power BI toegewezen resource provider. |
> | Bewerking | Micro soft. PowerBIDedicated/sku's/lezen | Hiermee wordt de informatie van Sku's opgehaald |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Recovery Services/locaties/allocatedStamp/lezen | GetAllocatedStamp is een interne bewerking die wordt gebruikt door de service |
> | Bewerking | Micro soft. Recovery Services/locaties/Allo Cate Stamp/actie | Allo Cate Stamp is een interne bewerking die wordt gebruikt door de service |
> | Bewerking | Micro soft. Recovery Services/locaties/backupPreValidateProtection/actie |  |
> | Bewerking | Micro soft. Recovery Services/locaties/backupProtectedItem/schrijven | Een beveiligd back-upitem maken |
> | Bewerking | Micro soft. Recovery Services/locaties/backupProtectedItems/lezen | Hiermee wordt de lijst met alle beveiligde items geretourneerd. |
> | Bewerking | Micro soft. Recovery Services/locaties/backupStatus/actie | De back-upstatus voor Recovery Services kluizen controleren |
> | Bewerking | Micro soft. Recovery Services/locaties/backupValidateFeatures/actie | Functies valideren |
> | Bewerking | Micro soft. Recovery Services/locaties/checkNameAvailability/actie | De beschik baarheid van resource namen controleren is een API om te controleren of de resource naam beschikbaar is |
> | Bewerking | Micro soft. Recovery Services/locaties/operationStatus/lezen | Hiermee wordt de bewerkings status voor een bepaalde bewerking opgehaald |
> | Bewerking | Micro soft. Recovery Services/Operations/lezen | Met deze bewerking wordt de lijst met bewerkingen voor een resource provider geretourneerd |
> | Bewerking | Micro soft. Recovery Services/REGI ster/actie | Hiermee wordt een abonnement geregistreerd voor een bepaalde resource provider |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupconfig/lezen | Hiermee wordt de configuratie voor Recovery Services kluis geretourneerd. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupconfig/schrijven | Hiermee wordt de configuratie van Recovery Services-kluis bijgewerkt. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupEngines/lezen | Retourneert alle back-upbeheerser vers die zijn geregistreerd bij de kluis. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/backupProtectionIntent/verwijderen | Een opzet voor een back-upbeveiliging verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/backupProtectionIntent/lezen | Een doel voor back-upbeveiliging verkrijgen |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/backupProtectionIntent/schrijven | Een doel voor back-upbeveiliging maken |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/operationResults/lezen | Hiermee wordt de status van de bewerking geretourneerd |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/operationsStatus/lezen | Hiermee wordt de status van de bewerking geretourneerd |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectableContainers/lezen | Alle Beveilig bare containers ophalen |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/verwijderen | Hiermee wordt de geregistreerde container verwijderd |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/query/Action | Een query uitvoeren voor werk belastingen binnen een container |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/items/lezen | Alle items in een container ophalen |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/operationResults/lezen | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op de beveiligings container opgehaald. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/operationsStatus/lezen | Hiermee wordt de status opgehaald van de bewerking die is uitgevoerd op de beveiligings container. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/backup/Action | Maakt een back-up voor het beveiligde item. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/verwijderen | Hiermee wordt het beveiligde item verwijderd |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/operationResults/lezen | Hiermee wordt het resultaat van de bewerking die is uitgevoerd op beveiligde items opgehaald. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/operationsStatus/lezen | Hiermee wordt de status geretourneerd van de bewerking die is uitgevoerd op beveiligde items. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/lezen | Hiermee worden object gegevens van het beveiligde item geretourneerd |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/Action | Direct-item herstel inrichten voor beveiligd item |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/recoveryPoints/lezen | Herstel punten voor beveiligde items ophalen. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | Herstel punten voor beveiligde items herstellen. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/Action | Herstel van onmiddellijke items intrekken voor beveiligd item |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/protectedItems/write | Een beveiligd back-upitem maken |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/lezen | Hiermee worden alle geregistreerde containers geretourneerd |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/protectionContainers/schrijven | Hiermee maakt u een geregistreerde container |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupFabrics/refreshContainers/actie | Hiermee vernieuwt u de container lijst |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupJobs/annuleren/actie | De taak annuleren |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupJobs/operationResults/lezen | Hiermee wordt het resultaat van de taak bewerking geretourneerd. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupJobs/operationsStatus/lezen | Hiermee wordt de status van de taak bewerking geretourneerd. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupJobs/lezen | Hiermee worden alle taak objecten geretourneerd |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupJobsExport/actie | Taken exporteren |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupOperationResults/lezen | Retourneert een back-upbewerkings resultaat voor Recovery Services kluis. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupOperations/lezen | Hiermee wordt de status van de back-upbewerking voor Recovery Services kluis geretourneerd. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupPolicies/verwijderen | Een beveiligings beleid verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupPolicies/operationResults/lezen | Resultaten van beleids bewerking ophalen. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupPolicies/Operations/lezen | Status van beleids bewerking ophalen. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupPolicies/lezen | Hiermee worden alle beveiligings beleidsregels geretourneerd |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupPolicies/schrijven | Hiermee wordt een beveiligings beleid gemaakt |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupProtectableItems/lezen | Hiermee wordt een lijst met alle Beveilig bare items geretourneerd. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupProtectedItems/lezen | Hiermee wordt de lijst met alle beveiligde items geretourneerd. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupProtectionContainers/lezen | Hiermee worden alle containers van het abonnement geretourneerd |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupProtectionIntents/lezen | Alle intenties van de back-upbeveiliging weer geven |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupSecurityPIN/actie | Retourneert informatie over de beveiligings pincode voor Recovery Services kluis. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupstorageconfig/lezen | Hiermee wordt de opslag configuratie voor Recovery Services kluis geretourneerd. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupstorageconfig/schrijven | Hiermee wordt de opslag configuratie voor Recovery Services kluis bijgewerkt. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupUsageSummaries/lezen | Retourneert samen vattingen voor beveiligde items en beveiligde servers voor een Recovery Services. |
> | Bewerking | Micro soft. Recovery Services/kluizen/backupValidateOperation/actie | Bewerking voor beveiligd item valideren |
> | Bewerking | Micro soft. Recovery Services/kluizen/certificaten/schrijven | Met de bewerking resource certificaat bijwerken wordt het resource/kluis-referentie certificaat bijgewerkt. |
> | Bewerking | Micro soft. Recovery Services/kluizen/verwijderen | Met de bewerking kluis verwijderen wordt de opgegeven Azure-resource van het type kluis verwijderd |
> | Bewerking | Micro soft. Recovery Services/kluizen/extendedInformation/verwijderen | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Bewerking | Micro soft. Recovery Services/kluizen/extendedInformation/lezen | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Bewerking | Micro soft. Recovery Services/kluizen/extendedInformation/schrijven | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Bewerking | Micro soft. Recovery Services/kluizen/monitoringAlerts/lezen | Hiermee worden de waarschuwingen voor de Recovery Services-kluis opgehaald. |
> | Bewerking | Micro soft. Recovery Services/kluizen/monitoringAlerts/schrijven | Hiermee wordt de waarschuwing opgelost. |
> | Bewerking | Micro soft. Recovery Services/kluizen/monitoringConfigurations/lezen | Hiermee wordt de configuratie van de Recovery Services-kluis melding opgehaald. |
> | Bewerking | Micro soft. Recovery Services/kluizen/monitoringConfigurations/schrijven | Hiermee configureert u e-mail meldingen voor de Recovery Services-kluis. |
> | Bewerking | Micro soft. Recovery Services/kluizen/lezen | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Bewerking | Micro soft. Recovery Services/kluizen/registeredIdentities/verwijderen | De registratie van de container unregister kan worden gebruikt om de registratie van een container op te heffen. |
> | Bewerking | Micro soft. Recovery Services/kluizen/registeredIdentities/operationResults/lezen | De bewerking resultaten van de bewerking ophalen kan worden gebruikt om de bewerkings status en het resultaat van de asynchroon ingediende bewerking op te halen |
> | Bewerking | Micro soft. Recovery Services/kluizen/registeredIdentities/lezen | De bewerking containers ophalen kan worden gebruikt om de containers op te halen die voor een resource zijn geregistreerd. |
> | Bewerking | Micro soft. Recovery Services/kluizen/registeredIdentities/schrijven | De bewerking service container registreren kan worden gebruikt om een container te registreren bij de Recovery-service. |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationAlertSettings/lezen | Waarschuwings instellingen lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationAlertSettings/schrijven | Waarschuwings instellingen maken of bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationEvents/lezen | Gebeurtenissen lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/checkConsistency/actie | Hiermee wordt de consistentie van de infra structuur gecontroleerd |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/verwijderen | Infrastructuur resources verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/deployProcessServerImage/actie | Installatie kopie van de proces server implementeren |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/migratetoaad/actie | Fabric migreren naar AAD |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/operationresults/lezen | De resultaten van een asynchrone bewerking op de bron-fabrics volgen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/lezen | Alle infra structuren lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/reassociateGateway/actie | Gateway opnieuw koppelen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/verwijderen/actie | Infra structuur verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/renewcertificate/actie | Certificaat voor Fabric vernieuwen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationLogicalNetworks/lezen | Alle logische netwerken lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationNetworks/lezen | Alle netwerken lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationNetworks/replicationNetworkMappings/verwijderen | Netwerk toewijzingen verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationNetworks/replicationNetworkMappings/lezen | Netwerk toewijzingen lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Netwerk toewijzingen maken of bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/actie | Beveiligbaar item detecteren |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/operationresults/lezen | De resultaten van een asynchrone bewerking op de bron beveiligings containers volgen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/lezen | Beveiligings containers lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/Remove/Action | Beveiligings container verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/verwijderen | Migratie-items verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/Action | Item migreren |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/lezen | Alle migratie herstel punten lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/lezen | De resultaten van een asynchrone bewerking op de bron migratie-items bijhouden |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/lezen | Alle migratie-items lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/Action | Migratie testen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/Action | Migratie opschonen testen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Migratie-items maken of bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/lezen | Beveilig bare items lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/Action | Schijven toevoegen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/Action | Herstel punt Toep assen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/verwijderen | Alle beveiligde items verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/Action | Failover door voeren |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/lezen | De resultaten van een asynchrone bewerking volgen op de bron beveiligde items |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/Action | Geplande failover |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/lezen | Alle beveiligde items lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/lezen | Alle replicatie herstel punten lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/verwijderen/actie | Beveiligd item verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/Action | Schijven verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/Action | Replicatie herstellen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/opnieuw beveiligen/actie | Beveiligd item opnieuw beveiligen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/Action |  |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/Action | Feedback verzenden |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/lezen | Alle doel berekenings grootten lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/Action | Failover testen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/Action | Failovertest opschonen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/Action | Failover |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/Action | Mobility-service bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Beveiligde items maken of bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/verwijderen | Alle beveiligings container toewijzingen verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/lezen | De resultaten van een asynchrone bewerking op de resource Protection-container toewijzingen bijhouden |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/lezen | Alle beveiligings container toewijzingen lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/verwijderen/actie | Toewijzing van beveiligings container verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Beveiligings container toewijzingen maken of bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/switchprotection/actie | Beveiligings container overschakelen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationProtectionContainers/schrijven | Beveiligings containers maken of bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationRecoveryServicesProviders/verwijderen | Alle Recovery Services Providers verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationRecoveryServicesProviders/operationresults/lezen | De resultaten van een asynchrone bewerking op de resource Recovery Services Providers bijhouden |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationRecoveryServicesProviders/lezen | Recovery Services Providers lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/actie | Provider vernieuwen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationRecoveryServicesProviders/Remove/Action | Recovery Services provider verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationRecoveryServicesProviders/schrijven | Recovery Services Providers maken of bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/lezen | Alle opslag classificaties lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/verwijderen | Alle opslag classificatie toewijzingen verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/lezen | De resultaten van een asynchrone bewerking op de resource Storage-classificatie toewijzingen bijhouden |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/lezen | Alle opslag classificatie toewijzingen lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Opslag classificatie toewijzingen maken of bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationvCenters/verwijderen | Alle vCenter verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationvCenters/operationresults/lezen | De resultaten van een asynchrone bewerking op de resource-vCenter bijhouden |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationvCenters/lezen | Alle vCenter lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/replicationvCenters/schrijven | VCenter maken of bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationFabrics/schrijven | Infrastructuur resources maken of bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationJobs/annuleren/actie | Taak annuleren |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationJobs/operationresults/lezen | De resultaten van een asynchrone bewerking op de resource taken bijhouden |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationJobs/lezen | Alle taken lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationJobs/restart/actie | Taak opnieuw starten |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationJobs/resume/actie | Taak hervatten |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationMigrationItems/lezen | Alle migratie-items lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationNetworkMappings/lezen | Netwerk toewijzingen lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationNetworks/lezen | Alle netwerken lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationPolicies/verwijderen | Beleids regels verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationPolicies/operationresults/lezen | De resultaten van een asynchrone bewerking op de bron beleidsregels bijhouden |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationPolicies/lezen | Alle beleids regels lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationPolicies/schrijven | Beleid maken of bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationProtectedItems/lezen | Alle beveiligde items lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationProtectionContainerMappings/lezen | Alle beveiligings container toewijzingen lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationProtectionContainers/lezen | Beveiligings containers lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/verwijderen | Herstel plannen verwijderen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/failoverCommit/actie | Herstel plan voor failover door voeren |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/operationresults/lezen | De resultaten van een asynchrone bewerking op de resource recovery-plannen bijhouden |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/plannedFailover/actie | Herstel plan voor geplande failover |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/lezen | Herstel plannen lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/opnieuw beveiligen/actie | Herstel plan opnieuw beveiligen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/testFailover/actie | Herstel plan voor failover testen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/testFailoverCleanup/actie | Herstel plan voor het opschonen van de Failovertest |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/unplannedFailover/actie | Herstel plan voor failover |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationRecoveryPlans/schrijven | Herstel plannen maken of bijwerken |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationRecoveryServicesProviders/lezen | Recovery Services Providers lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationStorageClassificationMappings/lezen | Alle opslag classificatie toewijzingen lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationStorageClassifications/lezen | Alle opslag classificaties lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationSupportedOperatingSystems/lezen | Alle items lezen  |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationUsages/lezen | Een kluis replicatie gebruik lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationVaultHealth/operationresults/lezen | De resultaten van een asynchrone bewerking op de status van de resource-kluis replicatie bijhouden |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationVaultHealth/lezen | Status van de kluis replicatie lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationVaultHealth/Refresh/actie | Status van de kluis vernieuwen |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationVaultSettings/lezen | Alle items lezen  |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationVaultSettings/schrijven | Maken of bijwerken  |
> | Bewerking | Micro soft. Recovery Services/kluizen/replicationvCenters/lezen | Alle vCenter lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/gebruik/lezen | Een kluis gebruik lezen |
> | Bewerking | Micro soft. Recovery Services/kluizen/gebruik/lezen | Hiermee worden gebruiks gegevens voor een Recovery Services kluis geretourneerd. |
> | Bewerking | Micro soft. Recovery Services/kluizen/vaultTokens/lezen | De bewerking kluis token kan worden gebruikt om het kluis token voor back-upbewerkingen op kluis niveau op te halen. |
> | Bewerking | Micro soft. Recovery Services/kluizen/schrijven | Met een kluis bewerking maken wordt een Azure-resource van het type ' kluis ' gemaakt. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. relay/checkNameAvailability/actie | Controleert de beschik baarheid van de naam ruimte onder het gegeven abonnement. |
> | Bewerking | Micro soft. relay/checkNamespaceAvailability/actie | Controleert de beschik baarheid van de naam ruimte onder het gegeven abonnement. Deze API is afgeschaft. gebruik in plaats daarvan CheckNameAvailability. |
> | Bewerking | Micro soft. relay/naam ruimten/authorizationRules/actie | De autorisatie regel voor de naam ruimte wordt bijgewerkt. Deze API is afgeschaft. Gebruik in plaats daarvan een PUT-aanroep om de naam ruimte autorisatie regel bij te werken. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Bewerking | Micro soft. relay/naam ruimten/authorizationRules/verwijderen | Naam ruimte autorisatie regel verwijderen. De standaard regel voor autorisatie van de naam ruimte kan niet worden verwijderd.  |
> | Bewerking | Micro soft. relay/naam ruimten/authorizationRules/listkeys ophalen/actie | De verbindings reeks ophalen voor de naam ruimte |
> | Bewerking | Micro soft. relay/naam ruimten/authorizationRules/lezen | De lijst met beschrijvingen van autorisatie regels voor naam ruimten ophalen. |
> | Bewerking | Micro soft. relay/naam ruimten/authorizationRules/regenerateKeys/actie | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Bewerking | Micro soft. relay/naam ruimten/authorizationRules/schrijven | Maak autorisatie regels voor het naam ruimte niveau en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels, de primaire en secundaire sleutels kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. relay/naam ruimten/verwijderen | Naam ruimte resource verwijderen |
> | Bewerking | Micro soft. relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys ophalen/Action | Hiermee worden de sleutels voor autorisatie regels opgehaald voor de primaire naam ruimte voor nood herstel |
> | Bewerking | Micro soft. relay/naam ruimten/disasterRecoveryConfigs/authorizationRules/lezen | De autorisatie regels van de primaire naam ruimte voor herstel na nood geval ophalen |
> | Bewerking | Micro soft. relay/naam ruimten/disasterRecoveryConfigs/breakPairing/actie | Hiermee schakelt u herstel na nood gevallen uit en stopt u het repliceren van wijzigingen van primaire naar secundaire naam ruimten. |
> | Bewerking | Micro soft. relay/naam ruimten/disasterrecoveryconfigs/checkNameAvailability/actie | Controleert de beschik baarheid van de naam ruimte alias onder het opgegeven abonnement. |
> | Bewerking | Micro soft. relay/naam ruimten/disasterRecoveryConfigs/verwijderen | Hiermee verwijdert u de nood herstel configuratie die is gekoppeld aan de naam ruimte. Deze bewerking kan alleen worden aangeroepen via de primaire naam ruimte. |
> | Bewerking | Micro soft. relay/naam ruimten/disasterRecoveryConfigs/failover/Action | Hiermee wordt een GEO DR-failover aangeroepen en wordt de naam ruimte alias zo geconfigureerd dat deze naar de secundaire naam ruimte wijst. |
> | Bewerking | Micro soft. relay/naam ruimten/disasterRecoveryConfigs/lezen | Hiermee haalt u de nood herstel configuratie op die is gekoppeld aan de naam ruimte. |
> | Bewerking | Micro soft. relay/naam ruimten/disasterRecoveryConfigs/schrijven | Hiermee wordt de nood herstel configuratie die is gekoppeld aan de naam ruimte gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. relay/naam ruimten/HybridConnections/authorizationRules/actie | Bewerking voor het bijwerken van HybridConnection. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. Autorisatie regels. Gebruik een aanroep voor het bijwerken van de autorisatie regel. |
> | Bewerking | Micro soft. relay/naam ruimten/HybridConnections/authorizationRules/verwijderen | Bewerking voor het verwijderen van HybridConnection-autorisatie regels |
> | Bewerking | Micro soft. relay/namespaces/HybridConnections/authorizationRules/listkeys ophalen/Action | De verbindings reeks ophalen voor HybridConnection |
> | Bewerking | Micro soft. relay/naam ruimten/HybridConnections/authorizationRules/lezen |  De lijst met HybridConnection-autorisatie regels ophalen |
> | Bewerking | Micro soft. relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/Action | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Bewerking | Micro soft. relay/naam ruimten/HybridConnections/authorizationRules/schrijven | Maak HybridConnection-autorisatie regels en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. relay/naam ruimten/HybridConnections/verwijderen | Bewerking voor het verwijderen van de HybridConnection-resource |
> | Bewerking | Micro soft. relay/naam ruimten/HybridConnections/lezen | Lijst met HybridConnection-resource beschrijvingen ophalen |
> | Bewerking | Micro soft. relay/naam ruimten/HybridConnections/schrijven | Eigenschappen van HybridConnection maken of bijwerken. |
> | Bewerking | Micro soft. relay/naam ruimten/messagingPlan/lezen | Hiermee wordt het berichten plan voor een naam ruimte opgehaald.<br>Deze API is afgeschaft.<br>Eigenschappen die via de MessagingPlan-resource beschikbaar worden gemaakt, worden in latere API-versies verplaatst naar de bron naam ruimte (Parent).<br>Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Bewerking | Micro soft. relay/naam ruimten/messagingPlan/schrijven | Hiermee wordt het bericht abonnement voor een naam ruimte bijgewerkt.<br>Deze API is afgeschaft.<br>Eigenschappen die via de MessagingPlan-resource beschikbaar worden gemaakt, worden in latere API-versies verplaatst naar de bron naam ruimte (Parent).<br>Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Bewerking | Micro soft. relay/naam ruimten/operationresults/lezen | De status van de naam ruimte bewerking ophalen |
> | Bewerking | Micro soft. relay/naam ruimten/providers/micro soft. Insights/diagnosticSettings/lezen | Lijst met resource beschrijvingen voor Diagnostische instellingen van naam ruimte ophalen |
> | Bewerking | Micro soft. relay/naam ruimten/providers/micro soft. Insights/diagnosticSettings/schrijven | Lijst met resource beschrijvingen voor Diagnostische instellingen van naam ruimte ophalen |
> | Bewerking | Micro soft. relay/naam ruimten/providers/micro soft. Insights/logDefinitions/lezen | Lijst met naam ruimte logboek bron beschrijvingen ophalen |
> | Bewerking | Micro soft. relay/naam ruimten/providers/micro soft. Insights/metricDefinitions/lezen | Lijst met resource beschrijvingen voor metrische gegevens van de naam ruimte ophalen |
> | Bewerking | Micro soft. relay/naam ruimten/lezen | De lijst met de resource beschrijving van de naam ruimte ophalen |
> | Bewerking | Micro soft. relay/naam ruimten/removeAcsNamepsace/actie | ACS-naam ruimte verwijderen |
> | Bewerking | Micro soft. relay/naam ruimten/WcfRelays/authorizationRules/actie | Bewerking voor het bijwerken van WcfRelay. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. Autorisatie regels. Gebruik een aanroep voor het bijwerken van de autorisatie regel. |
> | Bewerking | Micro soft. relay/naam ruimten/WcfRelays/authorizationRules/verwijderen | Bewerking voor het verwijderen van WcfRelay-autorisatie regels |
> | Bewerking | Micro soft. relay/namespaces/WcfRelays/authorizationRules/listkeys ophalen/Action | De verbindings reeks ophalen voor WcfRelay |
> | Bewerking | Micro soft. relay/naam ruimten/WcfRelays/authorizationRules/lezen |  De lijst met WcfRelay-autorisatie regels ophalen |
> | Bewerking | Micro soft. relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/Action | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Bewerking | Micro soft. relay/naam ruimten/WcfRelays/authorizationRules/schrijven | Maak WcfRelay-autorisatie regels en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. relay/naam ruimten/WcfRelays/verwijderen | Bewerking voor het verwijderen van de WcfRelay-resource |
> | Bewerking | Micro soft. relay/naam ruimten/WcfRelays/lezen | Lijst met WcfRelay-resource beschrijvingen ophalen |
> | Bewerking | Micro soft. relay/naam ruimten/WcfRelays/schrijven | Eigenschappen van WcfRelay maken of bijwerken. |
> | Bewerking | Micro soft. relay/naam ruimten/schrijven | Maak een naam ruimte resource en werk de eigenschappen bij. Tags en capaciteit van de naam ruimte zijn de eigenschappen die kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. relay/Operations/lezen | Get-bewerkingen |
> | Bewerking | Micro soft. relay/registreren/actie | Hiermee wordt het abonnement voor de relay-resource provider geregistreerd en wordt het maken van relay-resources ingeschakeld |
> | Bewerking | Micro soft. relay/registratie/actie | Hiermee wordt het abonnement voor de relay-resource provider geregistreerd en wordt het maken van relay-resources ingeschakeld |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ResourceHealth/AvailabilityStatuses/actueel/lezen | Hiermee wordt de beschikbaarheids status opgehaald voor de opgegeven resource |
> | Bewerking | Micro soft. ResourceHealth/AvailabilityStatuses/lezen | Hiermee worden de beschikbaarheids status waarden opgehaald voor alle resources in het opgegeven bereik |
> | Bewerking | Micro soft. ResourceHealth/Events/lezen | Service Health gebeurtenissen voor een gegeven abonnement ophalen |
> | Bewerking | Micro soft. Resourcehealth/healthevent/actie | Hiermee wordt de wijziging in de status van de opgegeven resource aangeduid |
> | Bewerking | Micro soft. Resourcehealth/healthevent/activated/actie | Hiermee wordt de wijziging in de status van de opgegeven resource aangeduid |
> | Bewerking | Micro soft. Resourcehealth/healthevent/InProgress/actie | Hiermee wordt de wijziging in de status van de opgegeven resource aangeduid |
> | Bewerking | Micro soft. Resourcehealth/healthevent/pending/Action | Hiermee wordt de wijziging in de status van de opgegeven resource aangeduid |
> | Bewerking | Micro soft. Resourcehealth/healthevent/opgelost/actie | Hiermee wordt de wijziging in de status van de opgegeven resource aangeduid |
> | Bewerking | Micro soft. Resourcehealth/healthevent/Updated/actie | Hiermee wordt de wijziging in de status van de opgegeven resource aangeduid |
> | Bewerking | Micro soft. ResourceHealth/impactedResources/lezen | Betrokken resources voor een gegeven abonnement ophalen |
> | Bewerking | Micro soft. ResourceHealth/meta gegevens/lezen | Haalt meta gegevens op |
> | Bewerking | Micro soft. ResourceHealth/Operations/lezen | De beschik bare bewerkingen voor de micro soft-ResourceHealth ophalen |
> | Bewerking | Micro soft. ResourceHealth/REGI ster/actie | Hiermee wordt het abonnement voor de micro soft-ResourceHealth geregistreerd |
> | Bewerking | Micro soft. ResourceHealth/registratie/actie | Hiermee wordt de registratie van het abonnement voor de micro soft-ResourceHealth ongedaan gemaakt |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. resources/calculateTemplateHash/actie | De hash van de gegeven sjabloon berekenen. |
> | Bewerking | Micro soft. resources/checkPolicyCompliance/actie | Controleer de nalevings status van een bepaalde bron op basis van bron beleid. |
> | Bewerking | Micro soft. resources/checkResourceName/actie | Controleer de naam van de resource op geldigheid. |
> | Bewerking | Micro soft. resources/implementaties/annuleren/actie | Hiermee wordt een implementatie geannuleerd. |
> | Bewerking | Micro soft. resources/implementaties/verwijderen | Hiermee verwijdert u een implementatie. |
> | Bewerking | Micro soft. resources/implementaties/exportTemplate/actie | Sjabloon exporteren voor een implementatie |
> | Bewerking | Micro soft. resources/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Bewerking | Micro soft. resources/implementaties/operationstatuses/lezen | Hiermee worden de statussen van de implementatie bewerking opgehaald of weer gegeven. |
> | Bewerking | Micro soft. resources/implementaties/lezen | Hiermee wordt een lijst met implementaties opgehaald of weer gegeven. |
> | Bewerking | Micro soft. resources/implementaties/valideren/actie | Hiermee wordt een implementatie gevalideerd. |
> | Bewerking | Micro soft. resources/implementaties/whatIf/actie | Voor spelt wijzigingen in de sjabloon implementatie. |
> | Bewerking | Micro soft. resources/implementaties/schrijven | Hiermee wordt een implementatie gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. resources/koppelingen/verwijderen | Hiermee verwijdert u een resource koppeling. |
> | Bewerking | Micro soft. resources/koppelingen/lezen | Hiermee worden resource koppelingen opgehaald of weer gegeven. |
> | Bewerking | Micro soft. resources/koppelingen/schrijven | Hiermee wordt een resource koppeling gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. resources/Marketplace/aankoop/actie | Een resource aanschaffen via Marketplace. |
> | Bewerking | Micro soft. resources/providers/lezen | De lijst met providers ophalen. |
> | Bewerking | Micro soft. resources/resources/lezen | De lijst met resources ophalen op basis van filters. |
> | Bewerking | Micro soft. resources/abonnementen/locaties/lezen | Hiermee haalt u de lijst met ondersteunde locaties op. |
> | Bewerking | Micro soft. resources/abonnementen/operationresults/lezen | De resultaten van de abonnements bewerking ophalen. |
> | Bewerking | Micro soft. resources/abonnementen/providers/lezen | Hiermee worden resource providers opgehaald of weer gegeven. |
> | Bewerking | Micro soft. resources/abonnementen/lezen | Hiermee wordt de lijst met abonnementen opgehaald. |
> | Bewerking | Micro soft. resources/abonnementen/resourceGroups/verwijderen | Hiermee verwijdert u een resource groep en alle bijbehorende resources. |
> | Bewerking | Micro soft. resources/abonnementen/ResourceGroups/implementaties/bewerkingen/lezen | Hiermee worden implementatie bewerkingen opgehaald of weer gegeven. |
> | Bewerking | Micro soft. resources/abonnementen/ResourceGroups/implementaties/operationstatuses/lezen | Hiermee worden de statussen van de implementatie bewerking opgehaald of weer gegeven. |
> | Bewerking | Micro soft. resources/abonnementen/ResourceGroups/implementaties/lezen | Hiermee wordt een lijst met implementaties opgehaald of weer gegeven. |
> | Bewerking | Micro soft. resources/abonnementen/ResourceGroups/implementaties/schrijven | Hiermee wordt een implementatie gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. resources/abonnementen/resourceGroups/moveResources/actie | Hiermee verplaatst u resources van de ene resource groep naar een andere. |
> | Bewerking | Micro soft. resources/abonnementen/resourceGroups/lezen | Hiermee worden resource groepen opgehaald of weer gegeven. |
> | Bewerking | Micro soft. resources/abonnementen/ResourceGroups/resources/lezen | Hiermee haalt u de resources voor de resource groep op. |
> | Bewerking | Micro soft. resources/abonnementen/resourceGroups/validateMoveResources/actie | Verplaatsing van resources van de ene resource groep naar de andere valideren. |
> | Bewerking | Micro soft. resources/abonnementen/resourceGroups/schrijven | Hiermee wordt een resource groep gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. resources/abonnementen/resources/lezen | Hiermee haalt u de resources van een abonnement op. |
> | Bewerking | Micro soft. resources/abonnementen/tagName/verwijderen | Hiermee verwijdert u een abonnements label. |
> | Bewerking | Micro soft. resources/abonnementen/tagName/lezen | Hiermee worden abonnements Tags opgehaald of weer gegeven. |
> | Bewerking | Micro soft. resources/abonnementen/tagNames/tagValues/verwijderen | Hiermee verwijdert u een abonnements label waarde. |
> | Bewerking | Micro soft. resources/abonnementen/tagNames/tagValues/lezen | Hiermee worden de abonnements label waarden opgehaald of weer gegeven. |
> | Bewerking | Micro soft. resources/abonnementen/tagNames/tagValues/schrijven | Hiermee wordt een abonnements label waarde toegevoegd. |
> | Bewerking | Micro soft. resources/abonnementen/tagName/schrijven | Hiermee voegt u een abonnements label toe. |
> | Bewerking | Micro soft. resources/Tags/verwijderen | Hiermee verwijdert u alle tags van een resource. |
> | Bewerking | Micro soft. resources/tags/lezen | Hiermee worden alle labels op een resource opgehaald. |
> | Bewerking | Micro soft. resources/Tags/schrijven | Hiermee worden de tags op een resource bijgewerkt door bestaande tags te vervangen of samen te voegen met een nieuwe set tags of door bestaande tags te verwijderen. |
> | Bewerking | Micro soft. resources/tenants/lezen | Hiermee wordt de lijst met tenants opgehaald. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. scheduler/jobcollections/verwijderen | Hiermee verwijdert u de taak verzameling. |
> | Bewerking | Micro soft. scheduler/jobcollections/uitschakelen/actie | Hiermee schakelt u het verzamelen van taken uit. |
> | Bewerking | Micro soft. scheduler/jobcollections/Enable/Action | Hiermee schakelt u de taak verzameling in. |
> | Bewerking | Micro soft. scheduler/jobcollections/Jobs/verwijderen | Taak verwijderen. |
> | Bewerking | Micro soft. scheduler/jobcollections/Jobs/generateLogicAppDefinition/actie | Hiermee wordt een definitie van een logische app gegenereerd op basis van een planner taak. |
> | Bewerking | Micro soft. scheduler/jobcollections/Jobs/jobhistories/lezen | Hiermee wordt de taak geschiedenis opgehaald. |
> | Bewerking | Micro soft. scheduler/jobcollections/Jobs/lezen | Hiermee wordt een taak opgehaald. |
> | Bewerking | Micro soft. scheduler/jobcollections/Jobs/uitvoeren/actie | Taak wordt uitgevoerd. |
> | Bewerking | Micro soft. scheduler/jobcollections/Jobs/schrijven | Hiermee wordt een taak gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. scheduler/jobcollections/lezen | Taak verzameling ophalen |
> | Bewerking | Micro soft. scheduler/jobcollections/schrijven | Hiermee wordt een taak verzameling gemaakt of bijgewerkt. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Search/checkNameAvailability/Action | Controleert de beschik baarheid van de service naam. |
> | Bewerking | Micro soft. Search/Operations/lezen | Een lijst met alle beschik bare bewerkingen van de provider micro soft. Search. |
> | Bewerking | Micro soft. zoeken/registreren/actie | Hiermee wordt het abonnement voor de zoek resource provider geregistreerd en wordt het maken van zoek Services ingeschakeld. |
> | Bewerking | Micro soft. Search/searchServices/createQueryKey/Action | Hiermee maakt u de query sleutel. |
> | Bewerking | Micro soft. Search/searchServices/Delete | Hiermee verwijdert u de zoek service. |
> | Bewerking | Micro soft. Search/searchServices/deleteQueryKey/verwijderen | Hiermee verwijdert u de query sleutel. |
> | Bewerking | Micro soft. Search/searchServices/listAdminKeys/Action | Hiermee worden de beheer sleutels gelezen. |
> | Bewerking | Micro soft. Search/searchServices/listQueryKeys/Action | Retourneert de lijst met query-API-sleutels voor de gegeven Azure Cognitive Search-service. |
> | Bewerking | Micro soft. Search/searchServices/listQueryKeys/lezen | Retourneert de lijst met query-API-sleutels voor de gegeven Azure Cognitive Search-service. |
> | Bewerking | Micro soft. Search/searchServices/lezen | Hiermee wordt de zoek service gelezen. |
> | Bewerking | Micro soft. Search/searchServices/regenerateAdminKey/Action | Hiermee wordt de beheerders sleutel opnieuw gegenereerd. |
> | Bewerking | Micro soft. Search/searchServices/start/Action | Hiermee wordt de zoek service gestart. |
> | Bewerking | Micro soft. Search/searchServices/stop/actie | Hiermee stopt u de zoek service. |
> | Bewerking | Micro soft. Search/searchServices/write | Hiermee wordt de zoek service gemaakt of bijgewerkt. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Security/adaptiveNetworkHardenings/Force/Action | Hiermee worden de opgegeven regels voor het beveiligen van verkeer afgedwongen door het maken van overeenkomende beveiligings regels voor de opgegeven netwerk beveiligings groep (en) |
> | Bewerking | Micro soft. Security/adaptiveNetworkHardenings/lezen | Hiermee worden aanbevelingen voor adaptieve netwerk beveiliging van een met Azure beveiligde resource opgehaald |
> | Bewerking | Micro soft. Security/advancedThreatProtectionSettings/lezen | Hiermee worden de geavanceerde instellingen voor bedreigings beveiliging voor de resource opgehaald |
> | Bewerking | Micro soft. Security/advancedThreatProtectionSettings/schrijven | Hiermee worden de geavanceerde instellingen voor bedreigings beveiliging voor de resource bijgewerkt |
> | Bewerking | Micro soft. Security/Alerts/lezen | Hiermee worden alle beschik bare beveiligings waarschuwingen opgehaald |
> | Bewerking | Micro soft. Security/applicationWhitelistings/lezen | Hiermee wordt de toepassing whitelistings |
> | Bewerking | Micro soft. Security/applicationWhitelistings/schrijven | Hiermee maakt u een nieuwe white list of werkt u een bestaande toepassing bij. |
> | Bewerking | Micro soft. Security/assessmentMetadata/lezen | Beschik bare meta gegevens van beveiligings beoordeling voor uw abonnement ophalen |
> | Bewerking | Micro soft. Security/assessmentMetadata/schrijven | Meta gegevens van een beveiligings beoordeling maken of bijwerken |
> | Bewerking | Micro soft. Security/beoordelingen/lezen | Beveiligings beoordelingen ontvangen voor uw abonnement |
> | Bewerking | Micro soft. Security/beoordelingen/schrijven | Beveiligings beoordelingen maken of bijwerken voor uw abonnement |
> | Bewerking | Micro soft. Security/complianceResults/lezen | Hiermee worden de compliantie resultaten voor de resource opgehaald |
> | Bewerking | Micro soft. Security/informationProtectionPolicies/lezen | Hiermee wordt het Information Protection-beleid voor de resource opgehaald |
> | Bewerking | Micro soft. Security/informationProtectionPolicies/schrijven | Hiermee wordt het Information Protection-beleid voor de resource bijgewerkt |
> | Bewerking | Micro soft. beveiliging/locaties/waarschuwingen/activeren/actie | Een beveiligings waarschuwing activeren |
> | Bewerking | Micro soft. beveiliging/locaties/waarschuwingen/sluiten/actie | Een beveiligings waarschuwing negeren |
> | Bewerking | Micro soft. Security/locaties/waarschuwingen/lezen | Hiermee worden alle beschik bare beveiligings waarschuwingen opgehaald |
> | Bewerking | Micro soft. Security/locaties/jitNetworkAccessPolicies/verwijderen | Hiermee wordt het just-in-time-netwerk toegangs beleid verwijderd |
> | Bewerking | Micro soft. Security/locations/jitNetworkAccessPolicies/initiate/Action | Initieert een just-in-time-aanvraag voor netwerk toegangs beleid |
> | Bewerking | Micro soft. Security/locaties/jitNetworkAccessPolicies/lezen | Hiermee wordt het just-in-time-netwerk toegangs beleid opgehaald |
> | Bewerking | Micro soft. Security/locations/jitNetworkAccessPolicies/write | Hiermee maakt u een nieuw just-in-time-netwerk toegangsbeleid of wordt een bestaand beleid bijgewerkt |
> | Bewerking | Micro soft. Security/locaties/lezen | Hiermee wordt de locatie van de beveiligings gegevens opgehaald |
> | Bewerking | Micro soft. beveiliging/locaties/taken/activeren/actie | Een beveiligings aanbeveling activeren |
> | Bewerking | Micro soft. beveiliging/locaties/taken/sluiten/actie | Een beveiligings aanbeveling negeren |
> | Bewerking | Micro soft. Security/locaties/taken/lezen | Hiermee worden alle beschik bare beveiligings aanbevelingen opgehaald |
> | Bewerking | Micro soft. beveiliging/locaties/taken/oplossen/actie | Een beveiligings aanbeveling oplossen |
> | Bewerking | Micro soft. Security/locations/tasks/start/Action | Een beveiligings aanbeveling starten |
> | Bewerking | Micro soft. Security/Policies/lezen | Hiermee wordt het beveiligings beleid opgehaald |
> | Bewerking | Micro soft. Security/Policies/schrijven | Hiermee wordt het beveiligings beleid bijgewerkt |
> | Bewerking | Micro soft. Security/prijzen/verwijderen | Hiermee worden de prijs instellingen voor de scope verwijderd |
> | Bewerking | Micro soft. Security/prijzen/lezen | Hiermee worden de prijs instellingen voor de scope opgehaald |
> | Bewerking | Micro soft. Security/prijzen/schrijven | Hiermee worden de prijs instellingen voor de scope bijgewerkt |
> | Bewerking | Micro soft. Security/REGI ster/actie | Hiermee wordt het abonnement voor Azure Security Center geregistreerd |
> | Bewerking | Micro soft. Security/securityContacts/verwijderen | Hiermee wordt de contact persoon beveiliging verwijderd |
> | Bewerking | Micro soft. Security/securityContacts/lezen | Hiermee wordt de Security-contact persoon opgehaald |
> | Bewerking | Micro soft. Security/securityContacts/schrijven | Hiermee wordt de beveiligings contactpersoon bijgewerkt |
> | Bewerking | Micro soft. Security/securitySolutions/verwijderen | Hiermee verwijdert u een beveiligings oplossing |
> | Bewerking | Micro soft. Security/securitySolutions/lezen | Hiermee worden de beveiligings oplossingen opgehaald |
> | Bewerking | Micro soft. Security/securitySolutions/schrijven | Hiermee wordt een nieuwe beveiligings oplossing gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. Security/securitySolutionsReferenceData/lezen | Hiermee worden de referentie gegevens van beveiligings oplossingen opgehaald |
> | Bewerking | Micro soft. Security/securityStatuses/lezen | Hiermee worden de beveiligings statussen voor Azure-resources opgehaald |
> | Bewerking | Micro soft. Security/securityStatusesSummaries/lezen | Hiermee worden de samen vattingen van de beveiligings status van het bereik opgehaald |
> | Bewerking | Micro soft. Security/Settings/lezen | Hiermee worden de instellingen voor het bereik opgehaald |
> | Bewerking | Micro soft. Security/Settings/write | Hiermee worden de instellingen voor de scope bijgewerkt |
> | Bewerking | Micro soft. Security/tasks/lezen | Hiermee worden alle beschik bare beveiligings aanbevelingen opgehaald |
> | Bewerking | Micro soft. beveiliging/opheffen/actie | Hiermee wordt de registratie van het abonnement bij Azure Security Center ongedaan gemaakt |
> | Bewerking | Micro soft. Security/webApplicationFirewalls/verwijderen | Hiermee verwijdert u een Web Application Firewall |
> | Bewerking | Micro soft. Security/webApplicationFirewalls/lezen | Hiermee worden de firewalls voor webtoepassingen opgehaald |
> | Bewerking | Micro soft. Security/webApplicationFirewalls/schrijven | Hiermee wordt een nieuwe Web Application Firewall gemaakt of een bestaande bijgewerkt |
> | Bewerking | Micro soft. Security/workspaceSettings/Connect/Action | Instellingen voor opnieuw verbinden van werk ruimte-instellingen wijzigen |
> | Bewerking | Micro soft. Security/workspaceSettings/verwijderen | Hiermee verwijdert u de werk ruimte-instellingen |
> | Bewerking | Micro soft. Security/workspaceSettings/lezen | Hiermee worden de werkruimte instellingen opgehaald |
> | Bewerking | Micro soft. Security/workspaceSettings/schrijven | Hiermee werkt u de werk ruimte-instellingen bij |

## <a name="microsoftsecuritygraph"></a>Micro soft. SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. SecurityGraph/diagnosticsettings/verwijderen | Een diagnostische instelling verwijderen |
> | Bewerking | Micro soft. SecurityGraph/diagnosticsettings/lezen | Een diagnostische instelling lezen |
> | Bewerking | Micro soft. SecurityGraph/diagnosticsettings/schrijven | Een diagnostische instelling schrijven |
> | Bewerking | Micro soft. SecurityGraph/diagnosticsettingscategories/lezen | Categorieën voor Diagnostische instellingen lezen |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ServiceBus/checkNameAvailability/actie | Controleert de beschik baarheid van de naam ruimte onder het gegeven abonnement. |
> | Bewerking | Micro soft. ServiceBus/checkNamespaceAvailability/actie | Controleert de beschik baarheid van de naam ruimte onder het gegeven abonnement. Deze API is afgeschaft. gebruik in plaats daarvan CheckNameAvailability. |
> | Bewerking | Micro soft. ServiceBus/locaties/deleteVirtualNetworkOrSubnets/actie | Hiermee worden de VNet-regels in de ServiceBus-resource provider voor het opgegeven VNet verwijderd |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/authorizationRules/actie | De autorisatie regel voor de naam ruimte wordt bijgewerkt. Deze API is afgeschaft. Gebruik in plaats daarvan een PUT-aanroep om de naam ruimte autorisatie regel bij te werken. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/authorizationRules/verwijderen | Naam ruimte autorisatie regel verwijderen. De standaard regel voor autorisatie van de naam ruimte kan niet worden verwijderd.  |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/authorizationRules/listkeys ophalen/actie | De verbindings reeks ophalen voor de naam ruimte |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/authorizationRules/lezen | De lijst met beschrijvingen van autorisatie regels voor naam ruimten ophalen. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/authorizationRules/regenerateKeys/actie | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/authorizationRules/schrijven | Maak autorisatie regels voor het naam ruimte niveau en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels, de primaire en secundaire sleutels kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/verwijderen | Naam ruimte resource verwijderen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/disasterRecoveryConfigs/authorizationRules/listkeys ophalen/actie | Hiermee worden de sleutels voor autorisatie regels opgehaald voor de primaire naam ruimte voor nood herstel |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/disasterRecoveryConfigs/authorizationRules/lezen | De autorisatie regels van de primaire naam ruimte voor herstel na nood geval ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/disasterRecoveryConfigs/breakPairing/actie | Hiermee schakelt u herstel na nood gevallen uit en stopt u het repliceren van wijzigingen van primaire naar secundaire naam ruimten. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/disasterrecoveryconfigs/checkNameAvailability/actie | Controleert de beschik baarheid van de naam ruimte alias onder het opgegeven abonnement. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/disasterRecoveryConfigs/verwijderen | Hiermee verwijdert u de nood herstel configuratie die is gekoppeld aan de naam ruimte. Deze bewerking kan alleen worden aangeroepen via de primaire naam ruimte. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/disasterRecoveryConfigs/failover/actie | Hiermee wordt een GEO DR-failover aangeroepen en wordt de naam ruimte alias zo geconfigureerd dat deze naar de secundaire naam ruimte wijst. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/disasterRecoveryConfigs/lezen | Hiermee haalt u de nood herstel configuratie op die is gekoppeld aan de naam ruimte. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/disasterRecoveryConfigs/schrijven | Hiermee wordt de nood herstel configuratie die is gekoppeld aan de naam ruimte gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/eventGridFilters/verwijderen | Hiermee verwijdert u het Event Grid filter dat aan de naam ruimte is gekoppeld. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/eventGridFilters/lezen | Hiermee wordt het Event Grid filter opgehaald dat aan de naam ruimte is gekoppeld. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/eventGridFilters/schrijven | Hiermee wordt het Event Grid filter dat aan de naam ruimte is gekoppeld, gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/Event hubs/lezen | Lijst met EventHub-resource beschrijvingen ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/ipFilterRules/verwijderen | IP-filter bron verwijderen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/ipFilterRules/lezen | IP-filter bron ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/ipFilterRules/schrijven | IP-filter bron maken |
> | DataAction | Micro soft. ServiceBus/naam ruimten/berichten/ontvangen/actie | Berichten ontvangen |
> | DataAction | Micro soft. ServiceBus/naam ruimten/berichten/verzenden/actie | Berichten verzenden |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/messagingPlan/lezen | Hiermee wordt het berichten plan voor een naam ruimte opgehaald.<br>Deze API is afgeschaft.<br>Eigenschappen die via de MessagingPlan-resource beschikbaar worden gemaakt, worden in latere API-versies verplaatst naar de bron naam ruimte (Parent).<br>Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/messagingPlan/schrijven | Hiermee wordt het bericht abonnement voor een naam ruimte bijgewerkt.<br>Deze API is afgeschaft.<br>Eigenschappen die via de MessagingPlan-resource beschikbaar worden gemaakt, worden in latere API-versies verplaatst naar de bron naam ruimte (Parent).<br>Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/migreren/actie | Naam ruimte bewerking migreren |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/migrationConfigurations/verwijderen | Hiermee verwijdert u de migratie configuratie. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/migrationConfigurations/lezen | Hiermee wordt de migratie configuratie opgehaald, waarmee de status van de migratie en de replicatie bewerkingen in behandeling worden aangegeven |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/migrationConfigurations/Revert/Action | Hiermee wordt de standaard naar Premium-naam ruimte migratie hersteld |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/migrationConfigurations/upgrade/actie | Wijst de DNS-server die is gekoppeld aan de standaard naam ruimte toe aan de Premium-naam ruimte waarmee de migratie wordt voltooid en stopt de synchronisatie van resources van Standard-naar-Premium-naam ruimte |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/migrationConfigurations/schrijven | Hiermee wordt de migratie configuratie gemaakt of bijgewerkt. Hiermee wordt begonnen met het synchroniseren van resources van de standaard naar de Premium-naam ruimte |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/networkruleset/verwijderen | VNET-regel resource verwijderen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/networkruleset/lezen | Hiermee wordt de NetworkRuleSet-resource opgehaald |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/networkruleset/schrijven | VNET-regel resource maken |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/networkrulesets/verwijderen | VNET-regel resource verwijderen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/networkrulesets/lezen | Hiermee wordt de NetworkRuleSet-resource opgehaald |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/networkrulesets/schrijven | VNET-regel resource maken |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/operationresults/lezen | De status van de naam ruimte bewerking ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/providers/micro soft. Insights/diagnosticSettings/lezen | Lijst met resource beschrijvingen voor Diagnostische instellingen van naam ruimte ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/providers/micro soft. Insights/diagnosticSettings/schrijven | Lijst met resource beschrijvingen voor Diagnostische instellingen van naam ruimte ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/providers/micro soft. Insights/logDefinitions/lezen | Lijst met naam ruimte logboek bron beschrijvingen ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/providers/micro soft. Insights/metricDefinitions/lezen | Lijst met resource beschrijvingen voor metrische gegevens van de naam ruimte ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/wacht rijen/authorizationRules/actie | Bewerking voor het bijwerken van de wachtrij. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. Autorisatie regels. Gebruik een aanroep voor het bijwerken van de autorisatie regel. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/wacht rijen/authorizationRules/verwijderen | Bewerking voor het verwijderen van autorisatie regels voor de wachtrij |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/queues/authorizationRules/listkeys ophalen/Action | De verbindings reeks naar de wachtrij ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/wacht rijen/authorizationRules/lezen |  De lijst met autorisatie regels voor de wachtrij ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/queues/authorizationRules/regenerateKeys/Action | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/wacht rijen/authorizationRules/schrijven | Maak autorisatie regels voor de wachtrij en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/wacht rijen/verwijderen | Bewerking voor het verwijderen van de wachtrij resource |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/wacht rijen/lezen | Lijst met bron beschrijvingen van de wachtrij ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/wacht rijen/schrijven | Wachtrij-eigenschappen maken of bijwerken. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/lezen | De lijst met de resource beschrijving van de naam ruimte ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/removeAcsNamepsace/actie | ACS-naam ruimte verwijderen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/authorizationRules/actie | Bewerking voor het bijwerken van het onderwerp. Deze bewerking wordt niet ondersteund op API-versie 2017-04-01. Autorisatie regels. Gebruik een aanroep voor het bijwerken van de autorisatie regel. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/authorizationRules/verwijderen | Bewerking voor het verwijderen van de autorisatie regels voor het onderwerp |
> | Bewerking | Micro soft. ServiceBus/namespaces/topics/authorizationRules/listkeys ophalen/Action | De verbindings reeks naar het onderwerp ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/authorizationRules/lezen |  De lijst met autorisatie regels voor het onderwerp ophalen |
> | Bewerking | Micro soft. ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/Action | De primaire of secundaire sleutel opnieuw genereren voor de resource |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/authorizationRules/schrijven | Maak autorisatie regels voor het onderwerp en werk de eigenschappen bij. De toegangs rechten voor de autorisatie regels kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/verwijderen | Bewerking voor het verwijderen van de Onderwerps resource |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/lezen | Lijst met beschrijvingen van Onderwerps bronnen ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/abonnementen/verwijderen | Bewerking voor het verwijderen van de TopicSubscription-resource |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/abonnementen/lezen | Lijst met TopicSubscription-resource beschrijvingen ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/abonnementen/regels/verwijderen | Bewerking voor het verwijderen van de regel Resource |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/abonnementen/regels/lezen | Lijst met beschrijvingen van regel resources ophalen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/abonnementen/regels/schrijven | Regel eigenschappen maken of bijwerken. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/abonnementen/schrijven | Eigenschappen van TopicSubscription maken of bijwerken. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/onderwerpen/schrijven | Eigenschappen van een onderwerp maken of bijwerken. |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/virtualNetworkRules/verwijderen | VNET-regel resource verwijderen |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/virtualNetworkRules/lezen | Hiermee wordt VNET-regel Resource opgehaald |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/virtualNetworkRules/schrijven | VNET-regel resource maken |
> | Bewerking | Micro soft. ServiceBus/naam ruimten/schrijven | Maak een naam ruimte resource en werk de eigenschappen bij. Tags en capaciteit van de naam ruimte zijn de eigenschappen die kunnen worden bijgewerkt. |
> | Bewerking | Micro soft. ServiceBus/Operations/lezen | Get-bewerkingen |
> | Bewerking | Micro soft. ServiceBus/REGI ster/actie | Hiermee wordt het abonnement voor de ServiceBus-resource provider geregistreerd en wordt het maken van ServiceBus-resources mogelijk |
> | Bewerking | Micro soft. ServiceBus/SKU/lezen | Lijst met resource beschrijvingen van SKU ophalen |
> | Bewerking | Micro soft. ServiceBus/SKU/regio's/lezen | Lijst met SkuRegions-resource beschrijvingen ophalen |
> | Bewerking | Micro soft. ServiceBus/registratie/actie | Hiermee wordt het abonnement voor de ServiceBus-resource provider geregistreerd en wordt het maken van ServiceBus-resources mogelijk |

## <a name="microsoftservicefabric"></a>Micro soft. ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. ServiceFabric/clusters/toepassingen/verwijderen | Alle toepassingen verwijderen |
> | Bewerking | Micro soft. ServiceFabric/clusters/toepassingen/lezen | Alle toepassingen lezen |
> | Bewerking | Micro soft. ServiceFabric/clusters/toepassingen/services/verwijderen | Alle services verwijderen |
> | Bewerking | Micro soft. ServiceFabric/clusters/toepassingen/services/partities/lezen | Een wille keurige partitie lezen |
> | Bewerking | Micro soft. ServiceFabric/clusters/toepassingen/services/partities/replica's/lezen | Een replica lezen |
> | Bewerking | Micro soft. ServiceFabric/clusters/toepassingen/services/lezen | Alle services lezen |
> | Bewerking | Micro soft. ServiceFabric/clusters/toepassingen/services/statussen/lezen | Een service status lezen |
> | Bewerking | Micro soft. ServiceFabric/clusters/toepassingen/services/schrijven | Een service maken of bijwerken |
> | Bewerking | Micro soft. ServiceFabric/clusters/toepassingen/schrijven | Een toepassing maken of bijwerken |
> | Bewerking | Micro soft. ServiceFabric/clusters/applicationTypes/verwijderen | Een toepassings type verwijderen |
> | Bewerking | Micro soft. ServiceFabric/clusters/applicationTypes/lezen | Een toepassings type lezen |
> | Bewerking | Micro soft. ServiceFabric/clusters/applicationTypes/versies/verwijderen | Een versie van een toepassings type verwijderen |
> | Bewerking | Micro soft. ServiceFabric/clusters/applicationTypes/versies/lezen | Een versie van een toepassings type lezen |
> | Bewerking | Micro soft. ServiceFabric/clusters/applicationTypes/versies/schrijven | Een versie van een toepassings type maken of bijwerken |
> | Bewerking | Micro soft. ServiceFabric/clusters/applicationTypes/schrijven | Een toepassings type maken of bijwerken |
> | Bewerking | Micro soft. ServiceFabric/clusters/verwijderen | Een cluster verwijderen |
> | Bewerking | Micro soft. ServiceFabric/clusters/knoop punten/lezen | Een wille keurig knoop punt lezen |
> | Bewerking | Micro soft. ServiceFabric/clusters/lezen | Een cluster lezen |
> | Bewerking | Micro soft. ServiceFabric/clusters/statussen/lezen | Alle cluster status lezen |
> | Bewerking | Micro soft. ServiceFabric/clusters/schrijven | Een cluster maken of bijwerken |
> | Bewerking | Micro soft. ServiceFabric/locaties/clusterVersions/lezen | Een cluster versie lezen |
> | Bewerking | Micro soft. ServiceFabric/locaties/omgevingen/clusterVersions/lezen | Een cluster versie voor een specifieke omgeving lezen |
> | Bewerking | Micro soft. ServiceFabric/locaties/operationresults/lezen | Alle bewerkings resultaten lezen |
> | Bewerking | Micro soft. ServiceFabric/locaties/bewerkingen/lezen | Alle bewerkingen op locatie lezen |
> | Bewerking | Micro soft. ServiceFabric/Operations/lezen | Alle beschik bare bewerkingen lezen |
> | Bewerking | Micro soft. ServiceFabric/REGI ster/actie | Elke actie registreren |

## <a name="microsoftsignalrservice"></a>Micro soft. SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. SignalRService/locaties/checknameavailability/actie | Hiermee wordt gecontroleerd of een naam beschikbaar is voor gebruik met een nieuwe signalerings service |
> | Bewerking | Micro soft. SignalRService/locations/operationresults/Signaler/Read | De status van een asynchrone bewerking opvragen |
> | Bewerking | Micro soft. SignalRService/locaties/operationStatuses/operationId/lezen | De status van een asynchrone bewerking opvragen |
> | Bewerking | Micro soft. SignalRService/locaties/gebruik/lezen | Het quotum gebruik voor de Azure signalerings service ophalen |
> | Bewerking | Micro soft. SignalRService/operationresults/lezen | De status van een asynchrone bewerking opvragen |
> | Bewerking | Micro soft. SignalRService/Operations/lezen | De bewerkingen voor de Azure signalerings service weer geven. |
> | Bewerking | Micro soft. SignalRService/operationstatus/lezen | De status van een asynchrone bewerking opvragen |
> | Bewerking | Micro soft. SignalRService/REGI ster/actie | Hiermee wordt de resource provider micro soft. SignalRService geregistreerd bij een abonnement |
> | Bewerking | Micro soft. SignalRService/signalering/verwijderen | De volledige signalerings service verwijderen |
> | Bewerking | Micro soft. SignalRService/seingevings/eventGridFilters/verwijderen | Een gebeurtenis raster filter verwijderen van een Signaaler. |
> | Bewerking | Micro soft. SignalRService/seingevings/eventGridFilters/lezen | Hiermee worden de eigenschappen van het opgegeven gebeurtenis raster filter opgehaald of worden alle gebeurtenis raster filters voor de opgegeven Signa lering weer gegeven. |
> | Bewerking | Micro soft. SignalRService/seingevings/eventGridFilters/schrijven | Een gebeurtenis raster filter voor een signaal sterkte met de opgegeven para meters maken of bijwerken. |
> | Bewerking | Micro soft. SignalRService/seingevings/listkeys ophalen/Action | De waarde van de toegangs sleutels voor de Signa lering in de beheer portal of via API weer geven |
> | Bewerking | Micro soft. SignalRService/Signa lering/lezen | De instellingen en configuraties van de signaal sterkte weer geven in de beheer portal of via API |
> | Bewerking | Micro soft. SignalRService/seingevings/regeneratekey/Action | Wijzig de waarde van de toegangs sleutels voor de Signa lering in de beheer portal of via API |
> | Bewerking | Micro soft. SignalRService/seingevings/restart/actie | Een Azure signalerings service opnieuw starten in de beheer portal of via de API. Er is een bepaalde uitval tijd. |
> | Bewerking | Micro soft. SignalRService/seingevings/schrijven | Wijzig de instellingen en configuraties van de signaal sterkte in de beheer portal of via API |
> | Bewerking | Micro soft. SignalRService/registratie/actie | Hiermee wordt de registratie van de resource provider micro soft. SignalRService met een abonnement ongedaan gemaakt |

## <a name="microsoftsolutions"></a>Micro soft. Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Solutions/applicationDefinitions/applicationArtifacts/lezen | Een lijst met toepassings artefacten van de toepassings definitie. |
> | Bewerking | Micro soft. Solutions/applicationDefinitions/verwijderen | Hiermee verwijdert u een toepassings definitie. |
> | Bewerking | Micro soft. Solutions/applicationDefinitions/lezen | Hiermee wordt een lijst met toepassings definities opgehaald. |
> | Bewerking | Micro soft. Solutions/applicationDefinitions/write | Een toepassings definitie toevoegen of wijzigen. |
> | Bewerking | Micro soft. Solutions/Applications/applicationArtifacts/lezen | Een lijst met toepassings artefacten. |
> | Bewerking | Micro soft. Solutions/toepassingen/verwijderen | Hiermee verwijdert u een toepassing. |
> | Bewerking | Micro soft. Solutions/toepassingen/lezen | Hiermee haalt u een lijst met toepassingen op. |
> | Bewerking | Micro soft. Solutions/Applications/refreshPermissions/actie | Hiermee vernieuwt u de toepassings machtiging (en). |
> | Bewerking | Micro soft. Solutions/Applications/updateAccess/actie | Hiermee werkt u de toegang tot toepassingen bij. |
> | Bewerking | Micro soft. Solutions/toepassingen/schrijven | Hiermee wordt een toepassing gemaakt. |
> | Bewerking | Micro soft. Solutions/jitRequests/verwijderen | Een JitRequest verwijderen |
> | Bewerking | Micro soft. Solutions/jitRequests/lezen | Hiermee wordt een lijst met JitRequests opgehaald |
> | Bewerking | Micro soft. Solutions/jitRequests/write | Hiermee maakt u een JitRequest |
> | Bewerking | Micro soft. Solutions/locaties/operationStatuses/lezen | Hiermee wordt de bewerkings status van de resource gelezen. |
> | Bewerking | Micro soft. Solutions/REGI ster/actie | Registreren bij oplossingen. |
> | Bewerking | Micro soft. Solutions/registratie/actie | Hiermee wordt de registratie van oplossingen ongedaan gemaakt. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. SQL/checkNameAvailability/actie | Controleer of de opgegeven server naam beschikbaar is voor het wereld wijd inrichten van een bepaald abonnement. |
> | Bewerking | Micro soft. SQL/instancePools/verwijderen | Hiermee wordt een exemplaar groep verwijderd |
> | Bewerking | Micro soft. SQL/instancePools/lezen | Hiermee wordt een exemplaar groep opgehaald |
> | Bewerking | Micro soft. SQL/instancePools/gebruik/lezen | Hiermee worden de gebruiks gegevens van een exemplaar groep opgehaald |
> | Bewerking | Micro soft. SQL/instancePools/schrijven | Hiermee wordt een exemplaar groep gemaakt of bijgewerkt |
> | Bewerking | Micro soft. SQL/locaties/auditingSettingsAzureAsyncOperation/lezen | Resultaat van de set-bewerking voor het controle beleid voor de uitgebreide server-BLOB ophalen |
> | Bewerking | Micro soft. SQL/locaties/auditingSettingsOperationResults/lezen | Resultaat ophalen van de set-bewerking voor het controle beleid voor Server-blobs |
> | Bewerking | Micro soft. SQL/locaties/mogelijkheden/lezen | Hiermee worden de mogelijkheden voor dit abonnement op een bepaalde locatie opgehaald |
> | Bewerking | Micro soft. SQL/locaties/databaseAzureAsyncOperation/lezen | Hiermee wordt de status van een database bewerking opgehaald. |
> | Bewerking | Micro soft. SQL/locaties/databaseOperationResults/lezen | Hiermee wordt de status van een database bewerking opgehaald. |
> | Bewerking | Micro soft. SQL/locaties/deletedServerAsyncOperation/lezen | Hiermee worden bewerkingen uitgevoerd op de verwijderde server |
> | Bewerking | Micro soft. SQL/locaties/deletedServerOperationResults/lezen | Hiermee worden bewerkingen uitgevoerd op de verwijderde server |
> | Bewerking | Micro soft. SQL/locaties/deletedServers/lezen | De lijst met verwijderde servers retour neren of de eigenschappen voor de opgegeven verwijderde server ophalen. |
> | Bewerking | Micro soft. SQL/locaties/deletedServers/herstellen/actie | Een verwijderde server herstellen |
> | Bewerking | Micro soft. SQL/locaties/elasticPoolAzureAsyncOperation/lezen | Hiermee wordt de Azure async-bewerking voor een async-bewerking van een elastische groep opgehaald |
> | Bewerking | Micro soft. SQL/locaties/elasticPoolOperationResults/lezen | Hiermee wordt het resultaat van een elastische groeps bewerking opgehaald. |
> | Bewerking | Micro soft. SQL/locaties/encryptionProtectorAzureAsyncOperation/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante versleutelings beveiliging voor gegevens versleuteling |
> | Bewerking | Micro soft. SQL/locaties/encryptionProtectorOperationResults/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante versleutelings beveiliging voor gegevens versleuteling |
> | Bewerking | Micro soft. SQL/locaties/extendedAuditingSettingsAzureAsyncOperation/lezen | Resultaat van de set-bewerking voor het controle beleid voor de uitgebreide server-BLOB ophalen |
> | Bewerking | Micro soft. SQL/locaties/extendedAuditingSettingsOperationResults/lezen | Resultaat van de set-bewerking voor het controle beleid voor de uitgebreide server-BLOB ophalen |
> | Bewerking | Micro soft. SQL/locaties/firewallRulesAzureAsyncOperation/lezen | Hiermee wordt de status van een firewall regel bewerking opgehaald. |
> | Bewerking | Micro soft. SQL/locaties/firewallRulesOperationResults/lezen | Hiermee wordt de status van een firewall regel bewerking opgehaald. |
> | Bewerking | Micro soft. SQL/locaties/instanceFailoverGroups/verwijderen | Hiermee verwijdert u een bestaande failovergroep voor een exemplaar. |
> | Bewerking | Micro soft. SQL/locaties/instanceFailoverGroups/failover/actie | Voert een geplande failover uit in een bestaande failovergroep voor een exemplaar. |
> | Bewerking | Micro soft. SQL/locaties/instanceFailoverGroups/forceFailoverAllowDataLoss/actie | Hiermee wordt geforceerde failover uitgevoerd in een bestaande failovergroep voor een exemplaar. |
> | Bewerking | Micro soft. SQL/locaties/instanceFailoverGroups/lezen | Hiermee wordt de lijst met failover-groepen voor instanties geretourneerd of worden de eigenschappen opgehaald voor de opgegeven failovergroep voor het exemplaar. |
> | Bewerking | Micro soft. SQL/locaties/instanceFailoverGroups/schrijven | Hiermee maakt u een failovergroep voor een exemplaar met de opgegeven para meters of werkt u de eigenschappen of labels bij voor de opgegeven failovergroep voor het exemplaar. |
> | Bewerking | Micro soft. SQL/locaties/instancePoolAzureAsyncOperation/lezen | Hiermee wordt de status van een exemplaar groep-bewerking opgehaald |
> | Bewerking | Micro soft. SQL/locaties/instancePoolOperationResults/lezen | Hiermee wordt het resultaat voor een bewerking van een exemplaar groep opgehaald |
> | Bewerking | Micro soft. SQL/locaties/interfaceEndpointProfileAzureAsyncOperation/lezen | Hiermee worden de details van een specifiek interface-eind punt Azure async-bewerking geretourneerd |
> | Bewerking | Micro soft. SQL/locaties/interfaceEndpointProfileOperationResults/lezen | Retourneert de details van de opgegeven bewerking voor het eindpunt Profiel van de interface |
> | Bewerking | Micro soft. SQL/locaties/jobAgentAzureAsyncOperation/lezen | Hiermee wordt de status van een taak agent bewerking opgehaald. |
> | Bewerking | Micro soft. SQL/locaties/jobAgentOperationResults/lezen | Hiermee wordt het resultaat van een taak agent bewerking opgehaald. |
> | Bewerking | Micro soft. SQL/locaties/longTermRetentionBackups/lezen | Een lijst met de back-ups voor lange termijn retentie voor elke Data Base op elke server op een locatie |
> | Bewerking | Micro soft. SQL/locaties/longTermRetentionServers/longTermRetentionBackups/lezen | Een lijst met de back-ups voor lange termijn retentie voor elke Data Base op een server |
> | Bewerking | Micro soft. SQL/locaties/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/verwijderen | Hiermee wordt een back-up voor de lange termijn verwijderd |
> | Bewerking | Micro soft. SQL/locaties/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/lezen | Een lijst met de back-ups voor lange termijn retentie voor een Data Base |
> | Bewerking | Micro soft. SQL/locaties/managedDatabaseRestoreAzureAsyncOperation/completeRestore/actie | De herstel bewerking voor de beheerde data base volt ooien |
> | Bewerking | Micro soft. SQL/locaties/managedInstanceEncryptionProtectorAzureAsyncOperation/lezen | Hiermee worden bewerkingen uitgevoerd op transparante gegevens versleuteling beheerde instantie versleuteling beveiliging |
> | Bewerking | Micro soft. SQL/locaties/managedInstanceEncryptionProtectorOperationResults/lezen | Hiermee worden bewerkingen uitgevoerd op transparante gegevens versleuteling beheerde instantie versleuteling beveiliging |
> | Bewerking | Micro soft. SQL/locaties/managedInstanceKeyAzureAsyncOperation/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante instantie sleutels van beheerde exemplaren van gegevens versleuteling |
> | Bewerking | Micro soft. SQL/locaties/managedInstanceKeyOperationResults/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante instantie sleutels van beheerde exemplaren van gegevens versleuteling |
> | Bewerking | Micro soft. SQL/locaties/managedTransparentDataEncryptionAzureAsyncOperation/lezen | Hiermee worden bewerkingen uitgevoerd voor de transparante gegevens versleuteling van de beheerde data base |
> | Bewerking | Micro soft. SQL/locaties/managedTransparentDataEncryptionOperationResults/lezen | Hiermee worden bewerkingen uitgevoerd voor de transparante gegevens versleuteling van de beheerde data base |
> | Bewerking | Micro soft. SQL/locaties/privateEndpointConnectionAzureAsyncOperation/lezen | Hiermee wordt het resultaat voor een verbindings bewerking voor een privé-eind punt opgehaald |
> | Bewerking | Micro soft. SQL/locaties/privateEndpointConnectionOperationResults/lezen | Hiermee wordt het resultaat voor een verbindings bewerking voor een privé-eind punt opgehaald |
> | Bewerking | Micro soft. SQL/locaties/privateEndpointConnectionProxyAzureAsyncOperation/lezen | Hiermee wordt het resultaat opgehaald voor een proxy bewerking voor een privé-eindpunt verbinding |
> | Bewerking | Micro soft. SQL/locaties/privateEndpointConnectionProxyOperationResults/lezen | Hiermee wordt het resultaat opgehaald voor een proxy bewerking voor een privé-eindpunt verbinding |
> | Bewerking | Micro soft. SQL/locaties/lezen | Hiermee worden de beschik bare locaties voor een gegeven abonnement opgehaald |
> | Bewerking | Micro soft. SQL/locaties/serverKeyAzureAsyncOperation/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante server sleutels voor gegevens versleuteling |
> | Bewerking | Micro soft. SQL/locaties/serverKeyOperationResults/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante server sleutels voor gegevens versleuteling |
> | Bewerking | Micro soft. SQL/locaties/syncAgentOperationResults/lezen | Resultaat van de resource bewerking van de synchronisatie agent ophalen |
> | Bewerking | Micro soft. SQL/locaties/syncDatabaseIds/lezen | De data base-id's van de synchronisatie voor een bepaalde regio en een abonnement ophalen |
> | Bewerking | Micro soft. SQL/locaties/syncGroupOperationResults/lezen | Resultaat van de resource bewerking synchronisatie groep ophalen |
> | Bewerking | Micro soft. SQL/locaties/syncMemberOperationResults/lezen | Resultaat van de bewerking voor het synchroniseren van leden bronnen ophalen |
> | Bewerking | Micro soft. SQL/locaties/gebruik/lezen | Hiermee wordt een verzameling metrische gegevens over gebruik voor dit abonnement opgehaald op een locatie |
> | Bewerking | Micro soft. SQL/locaties/virtualNetworkRulesAzureAsyncOperation/lezen | Retourneert de details van de opgegeven virtuele netwerk regels Azure async-bewerking  |
> | Bewerking | Micro soft. SQL/locaties/virtualNetworkRulesOperationResults/lezen | Hiermee worden de details van de opgegeven bewerking voor virtuele netwerk regels geretourneerd  |
> | Bewerking | Micro soft. SQL/managedInstances/beheerders/verwijderen | Hiermee verwijdert u een bestaande beheerder van het beheerde exemplaar. |
> | Bewerking | Micro soft. SQL/managedInstances/beheerders/lezen | Hiermee wordt een lijst met beheerders van beheerde instanties opgehaald. |
> | Bewerking | Micro soft. SQL/managedInstances/beheerders/schrijven | Hiermee wordt een beheerde exemplaar beheerder met de opgegeven para meters gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/backupShortTermRetentionPolicies/lezen | Hiermee wordt een Bewaar beleid voor de korte termijn voor een beheerde data base opgehaald |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/backupShortTermRetentionPolicies/schrijven | Hiermee wordt een Bewaar beleid voor de korte termijn voor een beheerde data base bijgewerkt |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/kolommen/lezen | Een lijst met kolommen voor een beheerde data base retour neren |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/currentSensitivityLabels/lezen | Gevoeligheids labels van een bepaalde Data Base weer geven |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/currentSensitivityLabels/schrijven | Gevoeligheids labels batch-update |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/verwijderen | Hiermee verwijdert u een bestaande beheerde data base |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/providers/micro soft. Insights/logDefinitions/lezen | Hiermee worden de beschik bare logboeken voor beheerde exemplaar databases opgehaald |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/lezen | Hiermee wordt een bestaande beheerde data base opgehaald |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/recommendedSensitivityLabels/lezen | Gevoeligheids labels van een bepaalde Data Base weer geven |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/recommendedSensitivityLabels/schrijven | Aanbevolen gevoeligheids labels voor batch update |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/restoreDetails/lezen | Retourneert Details over het herstellen van de beheerde data base terwijl het terugzetten wordt uitgevoerd. |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/schema's/lezen | Een beheerd database schema ophalen. |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/schema's/tabellen/kolommen/lezen | Een kolom met beheerde data base ophalen |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/schema's/tabellen/kolommen/sensitivityLabels/verwijderen | Het gevoeligheids label van een bepaalde kolom verwijderen |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/schema's/tabellen/kolommen/sensitivityLabels/uitschakelen/actie | Gevoeligheids aanbevelingen voor een bepaalde kolom uitschakelen |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/schema's/Tables/columns/sensitivityLabels/Enable/Action | Gevoeligheids aanbevelingen voor een bepaalde kolom inschakelen |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/schema's/Tables/columns/sensitivityLabels/lezen | Het gevoeligheids label van een bepaalde kolom ophalen |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/schema's/tabellen/kolommen/sensitivityLabels/schrijven | Het gevoeligheids label van een bepaalde kolom maken of bijwerken |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/schema's/tabellen/lezen | Een beheerde database tabel ophalen |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/securityAlertPolicies/lezen | Een lijst met beleids regels voor detectie van het beleid voor beheerde data bases die zijn geconfigureerd voor een bepaalde server ophalen |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/securityAlertPolicies/schrijven | Het beleid voor het detecteren van de Data Base voor een bepaalde beheerde data base wijzigen |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/securityEvents/lezen | Hiermee worden de beveiligings gebeurtenissen van de beheerde data base opgehaald |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/sensitivityLabels/lezen | Gevoeligheids labels van een bepaalde Data Base weer geven |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/transparentDataEncryption/lezen | Details van de data base ophalen Transparent Data Encryption op een bepaalde beheerde data base |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/transparentDataEncryption/schrijven | De data base-Transparent Data Encryption voor een bepaalde beheerde data base wijzigen |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/verwijderen | De evaluatie van beveiligings problemen voor een bepaalde data base verwijderen |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/lezen | Het beleid voor evaluatie van beveiligings problemen ophalen voor een givendatabase |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/regels/basis lijnen/verwijderen | De regel basislijn voor evaluatie van beveiligings problemen verwijderen voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/regels/basis lijnen/lezen | De regel basislijn voor evaluatie van beveiligings problemen ophalen voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/regels/basis lijnen/schrijven | De regel voor de evaluatie van beveiligings problemen wijzigen voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/scans/exporteren/actie | Een bestaand scan resultaat converteren naar een lees bare indeling. Als er al bestaat, gebeurt er niets |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/scans/initiateScan/Action | Voer de database scan voor evaluatie van beveiligings problemen uit. |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/scans/lezen | Retour neer de lijst met evaluatie records van het beveiligings probleem voor de data base of haal de scan record op voor de opgegeven scan-ID. |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/vulnerabilityAssessments/schrijven | De evaluatie van beveiligings problemen voor een bepaalde data base wijzigen |
> | Bewerking | Micro soft. SQL/managedInstances/data bases/schrijven | Hiermee maakt u een nieuwe data base of werkt u een bestaande data base bij. |
> | Bewerking | Micro soft. SQL/managedInstances/verwijderen | Hiermee verwijdert u een bestaand beheerd exemplaar. |
> | Bewerking | Micro soft. SQL/managedInstances/encryptionProtector/lezen | Hiermee wordt een lijst met server versleutelings beveiligingen geretourneerd of worden de eigenschappen opgehaald voor de opgegeven server versleutelings beveiliging. |
> | Bewerking | Micro soft. SQL/managedInstances/encryptionProtector/revalidate/Action | Werk de eigenschappen voor de opgegeven server versleutelings beveiliging bij. |
> | Bewerking | Micro soft. SQL/managedInstances/encryptionProtector/schrijven | Werk de eigenschappen voor de opgegeven server versleutelings beveiliging bij. |
> | Bewerking | Micro soft. SQL/managedInstances/sleutels/verwijderen | Hiermee verwijdert u een bestaande Azure SQL Managed instance-sleutel. |
> | Bewerking | Micro soft. SQL/managedInstances/sleutels/lezen | Hiermee wordt de lijst met beheerde exemplaar sleutels geretourneerd of worden de eigenschappen opgehaald voor de opgegeven sleutel van het beheerde exemplaar. |
> | Bewerking | Micro soft. SQL/managedInstances/sleutels/schrijven | Hiermee maakt u een sleutel met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven sleutel van het beheerde exemplaar bij. |
> | Bewerking | Micro soft. SQL/managedInstances/metricDefinitions/lezen | Metrische definities voor beheerde instanties ophalen |
> | Bewerking | Micro soft. SQL/managedInstances/metrieken/lezen | Metrische gegevens van beheerde instanties ophalen |
> | Bewerking | Micro soft. SQL/managedInstances/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Bewerking | Micro soft. SQL/managedInstances/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. SQL/managedInstances/providers/micro soft. Insights/logDefinitions/lezen | Hiermee worden de beschik bare logboeken voor beheerde instanties opgehaald |
> | Bewerking | Micro soft. SQL/managedInstances/providers/micro soft. Insights/metricDefinitions/lezen | Typen metrische gegevens retour neren die beschikbaar zijn voor beheerde instanties |
> | Bewerking | Micro soft. SQL/managedInstances/lezen | De lijst met beheerde exemplaren retour neren of de eigenschappen van het opgegeven beheerde exemplaar ophalen. |
> | Bewerking | Micro soft. SQL/managedInstances/recoverableDatabases/lezen | Hiermee wordt een lijst met herstel bare beheerde data bases geretourneerd |
> | Bewerking | Micro soft. SQL/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/lezen | Hiermee wordt een Bewaar beleid voor de korte termijn opgehaald voor een verwijderde beheerde data base |
> | Bewerking | Micro soft. SQL/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/schrijven | Hiermee wordt een Bewaar beleid voor de korte termijn bijgewerkt voor een verwijderde beheerde data base |
> | Bewerking | Micro soft. SQL/managedInstances/restorableDroppedDatabases/lezen | Retourneert een lijst met restorable verwijderde beheerde data bases. |
> | Bewerking | Micro soft. SQL/managedInstances/securityAlertPolicies/lezen | Een lijst met beleids regels voor detectie van begeleide server dreigingen ophalen die zijn geconfigureerd voor een bepaalde server |
> | Bewerking | Micro soft. SQL/managedInstances/securityAlertPolicies/schrijven | Het beleid voor detectie van begeleide server dreigingen voor een beheerde server wijzigen |
> | Bewerking | Micro soft. SQL/managedInstances/tdeCertificates/actie | TDE-certificaat maken/bijwerken |
> | Bewerking | Micro soft. SQL/managedInstances/vulnerabilityAssessments/verwijderen | De evaluatie van beveiligings problemen voor een bepaald beheerd exemplaar verwijderen |
> | Bewerking | Micro soft. SQL/managedInstances/vulnerabilityAssessments/lezen | Het beleid voor evaluatie van beveiligings problemen voor een gegeven beheerd exemplaar ophalen |
> | Bewerking | Micro soft. SQL/managedInstances/vulnerabilityAssessments/schrijven | De evaluatie van beveiligings problemen voor een bepaald beheerd exemplaar wijzigen |
> | Bewerking | Micro soft. SQL/managedInstances/schrijven | Hiermee maakt u een beheerd exemplaar met de opgegeven para meters of werkt u de eigenschappen of labels voor het opgegeven beheerde exemplaar bij. |
> | Bewerking | Micro soft. SQL/Operations/lezen | Beschik bare REST bewerkingen ophalen |
> | Bewerking | Micro soft. SQL/privateEndpointConnectionsApproval/actie | Hiermee wordt bepaald of de gebruiker een verbinding met een privé-eind punt mag goed keuren |
> | Bewerking | Micro soft. SQL/REGI ster/actie | Hiermee wordt het abonnement voor de resource provider van micro soft SQL Database geregistreerd en wordt het maken van micro soft SQL-data bases mogelijk. |
> | Bewerking | Micro soft. SQL/servers/beheerders/verwijderen | Hiermee verwijdert u een specifiek Azure Active Directory-beheerder object |
> | Bewerking | Micro soft. SQL/servers/beheerders/lezen | Hiermee wordt een specifiek Azure Active Directory-beheer object opgehaald |
> | Bewerking | Micro soft. SQL/servers/beheerders/schrijven | Hiermee wordt een specifiek Azure Active Directory-beheerder object toegevoegd of bijgewerkt |
> | Bewerking | Micro soft. SQL/servers/Advisor/lezen | Hiermee wordt een lijst met beschik bare Advisor voor de server geretourneerd |
> | Bewerking | Micro soft. SQL/servers/Advisors/recommendedActions/lezen | Hiermee wordt een lijst met aanbevolen acties van opgegeven Advisor voor de server geretourneerd |
> | Bewerking | Micro soft. SQL/servers/Advisors/recommendedActions/schrijven | De aanbevolen actie Toep assen op de server |
> | Bewerking | Micro soft. SQL/servers/Advisors/schrijven | Hiermee wordt de status voor automatisch uitvoeren van een Advisor op server niveau bijgewerkt. |
> | Bewerking | Micro soft. SQL/servers/auditingPolicies/lezen | Details ophalen van het standaard controle beleid voor Server tabellen dat op een bepaalde server is geconfigureerd |
> | Bewerking | Micro soft. SQL/servers/auditingPolicies/schrijven | De standaard controle van de Server tabel voor een bepaalde server wijzigen |
> | Bewerking | Micro soft. SQL/servers/auditingSettings/operationResults/lezen | Resultaat ophalen van de set-bewerking voor het controle beleid voor Server-blobs |
> | Bewerking | Micro soft. SQL/servers/auditingSettings/lezen | Details ophalen van het controle beleid voor de server-BLOB dat op een bepaalde server is geconfigureerd |
> | Bewerking | Micro soft. SQL/servers/auditingSettings/schrijven | De controle van de server-BLOB voor een bepaalde server wijzigen |
> | Bewerking | Micro soft. SQL/servers/automaticTuning/lezen | Retourneert instellingen voor automatisch afstemmen voor de server |
> | Bewerking | Micro soft. SQL/servers/automaticTuning/schrijven | Hiermee worden de instellingen voor automatisch afstemmen van de server bijgewerkt en worden bijgewerkte instellingen geretourneerd |
> | Bewerking | Micro soft. SQL/servers/backupLongTermRetentionVaults/verwijderen | Hiermee verwijdert u een bestaande back-uparchief kluis. |
> | Bewerking | Micro soft. SQL/servers/backupLongTermRetentionVaults/lezen | Deze bewerking wordt gebruikt om een back-upkluis voor lange termijn retentie te verkrijgen. Hiermee wordt informatie geretourneerd over de kluis die op deze server is geregistreerd |
> | Bewerking | Micro soft. SQL/servers/backupLongTermRetentionVaults/schrijven | Met deze bewerking wordt een back-upkluis voor lange termijn registratie op een server geregistreerd |
> | Bewerking | Micro soft. SQL/servers/communicationLinks/verwijderen | Hiermee verwijdert u een bestaande server communicatie koppeling. |
> | Bewerking | Micro soft. SQL/servers/communicationLinks/lezen | Hiermee wordt de lijst met communicatie koppelingen van een opgegeven server geretourneerd. |
> | Bewerking | Micro soft. SQL/servers/communicationLinks/schrijven | Een server communicatie koppeling maken of bijwerken. |
> | Bewerking | Micro soft. SQL/servers/connectionPolicies/lezen | De lijst met server verbindings beleid van een opgegeven server retour neren. |
> | Bewerking | Micro soft. SQL/servers/connectionPolicies/schrijven | Een server verbindings beleid maken of bijwerken. |
> | Bewerking | Micro soft. SQL/servers/data bases/advisores/lezen | Hiermee wordt een lijst geretourneerd met de Advisor die beschikbaar zijn voor de data base |
> | Bewerking | Micro soft. SQL/servers/data bases/advisores/recommendedActions/lezen | Hiermee wordt een lijst met aanbevolen acties van de opgegeven Advisor voor de data base geretourneerd |
> | Bewerking | Micro soft. SQL/servers/data bases/adviseurs/recommendedActions/schrijven | De aanbevolen actie Toep assen op de data base |
> | Bewerking | Micro soft. SQL/servers/data bases/advisores/schrijven | Update status automatisch uitvoeren van een Advisor op database niveau. |
> | Bewerking | Micro soft. SQL/servers/data bases/auditingPolicies/lezen | Details ophalen van het controle beleid voor tabellen dat is geconfigureerd voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/servers/data bases/auditingPolicies/schrijven | Het controle beleid voor tabellen voor een bepaalde data base wijzigen |
> | Bewerking | Micro soft. SQL/servers/data bases/auditingSettings/lezen | Details ophalen van het BLOB-controle beleid dat is geconfigureerd voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/servers/data bases/auditingSettings/schrijven | Het BLOB-controle beleid voor een bepaalde data base wijzigen |
> | Bewerking | Micro soft. SQL/servers/data bases/auditRecords/lezen | De data base-BLOB-controle records ophalen |
> | Bewerking | Micro soft. SQL/servers/data bases/automaticTuning/lezen | Retourneert instellingen voor automatisch afstemmen voor een Data Base |
> | Bewerking | Micro soft. SQL/servers/data bases/automaticTuning/schrijven | Hiermee worden de instellingen voor automatisch afstemmen voor een Data Base bijgewerkt en worden bijgewerkte instellingen geretourneerd |
> | Bewerking | Micro soft. SQL/servers/data bases/azureAsyncOperation/lezen | Hiermee wordt de status van een database bewerking opgehaald. |
> | Bewerking | Micro soft. SQL/servers/data bases/backupLongTermRetentionPolicies/lezen | De lijst met back-uparchiverbeleid van een opgegeven Data Base retour neren. |
> | Bewerking | Micro soft. SQL/servers/data bases/backupLongTermRetentionPolicies/schrijven | Een archief beleid voor back-ups van de data base maken of bijwerken. |
> | Bewerking | Micro soft. SQL/servers/data bases/backupShortTermRetentionPolicies/lezen | Hiermee wordt een Bewaar beleid voor de korte termijn voor een Data Base opgehaald |
> | Bewerking | Micro soft. SQL/servers/data bases/backupShortTermRetentionPolicies/schrijven | Hiermee wordt een Bewaar beleid voor de korte termijn voor een Data Base bijgewerkt |
> | Bewerking | Micro soft. SQL/servers/data bases/kolommen/lezen | Een lijst met kolommen voor een Data Base retour neren |
> | Bewerking | Micro soft. SQL/servers/data bases/connectionPolicies/lezen | Details ophalen van het verbindings beleid dat is geconfigureerd voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/servers/data bases/connectionPolicies/schrijven | Het verbindings beleid voor een bepaalde data base wijzigen |
> | Bewerking | Micro soft. SQL/servers/data bases/currentSensitivityLabels/lezen | Gevoeligheids labels van een bepaalde Data Base weer geven |
> | Bewerking | Micro soft. SQL/servers/data bases/currentSensitivityLabels/schrijven | Gevoeligheids labels batch-update |
> | Bewerking | Micro soft. SQL/servers/data bases/dataMaskingPolicies/lezen | Retour neer de lijst met beleids regels voor database gegevens maskering. |
> | Bewerking | Micro soft. SQL/servers/data bases/dataMaskingPolicies/Rules/Delete | Beleids regel voor gegevens maskering voor een bepaalde data base verwijderen |
> | Bewerking | Micro soft. SQL/servers/data bases/dataMaskingPolicies/Rules/Read | Details ophalen van de beleids regel voor gegevens maskering die is geconfigureerd voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/servers/data bases/dataMaskingPolicies/Rules/write | Beleids regel voor gegevens maskering wijzigen voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/servers/data bases/dataMaskingPolicies/schrijven | Gegevens maskerings beleid voor een bepaalde data base wijzigen |
> | Bewerking | Micro soft. SQL/servers/data bases/dataWarehouseQueries/dataWarehouseQuerySteps/lezen | Retourneert de stap gegevens van de gedistribueerde query van de Data Warehouse-query voor de geselecteerde stap-ID |
> | Bewerking | Micro soft. SQL/servers/data bases/dataWarehouseQueries/lezen | Retourneert de gegevens van de distributie query van het Data Warehouse voor de geselecteerde query-ID |
> | Bewerking | Micro soft. SQL/servers/data bases/dataWarehouseUserActivities/lezen | Hiermee worden de gebruikers activiteiten opgehaald van een SQL Data Warehouse-exemplaar dat actieve en onderbroken query's bevat |
> | Bewerking | Micro soft. SQL/servers/data bases/verwijderen | Hiermee verwijdert u een bestaande data base. |
> | Bewerking | Micro soft. SQL/servers/data bases/exporteren/actie | Azure SQL Database exporteren |
> | Bewerking | Micro soft. SQL/servers/data bases/extendedAuditingSettings/lezen | Details ophalen van het uitgebreide BLOB-controle beleid dat is geconfigureerd voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/servers/data bases/extendedAuditingSettings/schrijven | Het uitgebreide BLOB-controle beleid voor een bepaalde data base wijzigen |
> | Bewerking | Micro soft. SQL/servers/data bases/uitbrei dingen/lezen | Hiermee wordt een verzameling extensies opgehaald voor de data base. |
> | Bewerking | Micro soft. SQL/servers/data bases/extensies/schrijven | De extensie voor een bepaalde data base wijzigen |
> | Bewerking | Micro soft. SQL/servers/data bases/failover/actie | Door klant geïnitieerde data base-failover. |
> | Bewerking | Micro soft. SQL/servers/data bases/geoBackupPolicies/lezen | Geografisch back-upbeleid voor een bepaalde data base ophalen |
> | Bewerking | Micro soft. SQL/servers/data bases/geoBackupPolicies/schrijven | Een beleid voor geoback-ups van de data base maken of bijwerken |
> | Bewerking | Micro soft. SQL/servers/data bases/importExportOperationResults/lezen | Hiermee worden bewerkingen voor importeren/exporteren uitgevoerd |
> | Bewerking | Micro soft. SQL/servers/data bases/maintenanceWindowOptions/lezen | Hiermee wordt een lijst met beschik bare onderhouds Vensters voor een geselecteerde data base opgehaald. |
> | Bewerking | Micro soft. SQL/servers/data bases/maintenanceWindows/lezen | Hiermee worden de instellingen voor onderhouds Vensters voor een geselecteerde data base opgehaald. |
> | Bewerking | Micro soft. SQL/servers/data bases/maintenanceWindows/schrijven | Hiermee stelt u de Windows-instellingen voor onderhoud in voor een geselecteerde data base. |
> | Bewerking | Micro soft. SQL/servers/data bases/metricDefinitions/lezen | Typen metrische gegevens die beschikbaar zijn voor data bases retour neren |
> | Bewerking | Micro soft. SQL/servers/data bases/metrische gegevens/lezen | Metrische gegevens voor data bases retour neren |
> | Bewerking | Micro soft. SQL/servers/data bases/verplaatsen/actie | De naam van een bestaande data base wijzigen. |
> | Bewerking | Micro soft. SQL/servers/data bases/operationResults/lezen | Hiermee wordt de status van een database bewerking opgehaald. |
> | Bewerking | Micro soft. SQL/servers/data bases/bewerkingen/annuleren/actie | Annuleert Azure SQL Database asynchrone bewerking in behandeling die nog niet is voltooid. |
> | Bewerking | Micro soft. SQL/servers/data bases/Operations/lezen | De lijst met bewerkingen die worden uitgevoerd op de data base retour neren |
> | Bewerking | Micro soft. SQL/servers/data bases/Pause/actie | Azure SQL Data Warehouse database onderbreken |
> | Bewerking | Micro soft. SQL/servers/data bases/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Bewerking | Micro soft. SQL/servers/data bases/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. SQL/servers/data bases/providers/micro soft. Insights/logDefinitions/lezen | Hiermee worden de beschik bare logboeken voor data bases opgehaald |
> | Bewerking | Micro soft. SQL/servers/data bases/providers/micro soft. Insights/metricDefinitions/lezen | Typen metrische gegevens die beschikbaar zijn voor data bases retour neren |
> | Bewerking | Micro soft. SQL/servers/data bases/queryStore/queryTexts/lezen | Retourneert de verzameling query teksten die overeenkomen met de opgegeven para meters. |
> | Bewerking | Micro soft. SQL/servers/data bases/queryStore/lezen | Retourneert de huidige waarden van de query Store-instellingen voor de data base. |
> | Bewerking | Micro soft. SQL/servers/data bases/queryStore/schrijven | Query Store-instelling voor de data base bijwerken |
> | Bewerking | Micro soft. SQL/servers/data bases/lezen | De lijst met data bases retour neren of de eigenschappen voor de opgegeven Data Base ophalen. |
> | Bewerking | Micro soft. SQL/servers/data bases/recommendedSensitivityLabels/lezen | Gevoeligheids labels van een bepaalde Data Base weer geven |
> | Bewerking | Micro soft. SQL/servers/data bases/recommendedSensitivityLabels/schrijven | Aanbevolen gevoeligheids labels voor batch update |
> | Bewerking | Micro soft. SQL/servers/data bases/replicationLinks/verwijderen | De replicatie relatie geforceerd en met mogelijk gegevens verlies beëindigen |
> | Bewerking | Micro soft. SQL/servers/data bases/replicationLinks/failover/actie | Failover na het synchroniseren van alle wijzigingen van de primaire, waardoor deze data base in de replicatie relationship\u0027s primair en de externe primaire in een secundaire |
> | Bewerking | Micro soft. SQL/servers/data bases/replicationLinks/forceFailoverAllowDataLoss/actie | Failover direct met mogelijk gegevens verlies, waardoor deze data base in de replicatie relationship\u0027s primair en de externe primaire in een secundaire |
> | Bewerking | Micro soft. SQL/servers/data bases/replicationLinks/lezen | De lijst met replicatie koppelingen retour neren of de eigenschappen ophalen voor de opgegeven replicatie koppelingen. |
> | Bewerking | Micro soft. SQL/servers/data bases/replicationLinks/ontkoppelen/actie | De replicatie relatie geforceerd of na synchronisatie met de partner beëindigen |
> | Bewerking | Micro soft. SQL/servers/data bases/replicationLinks/updateReplicationMode/actie | Replicatie modus bijwerken voor koppeling naar synchrone of asynchrone modus |
> | Bewerking | Micro soft. SQL/servers/data bases/restorePoints/actie | Hiermee maakt u een nieuw herstel punt |
> | Bewerking | Micro soft. SQL/servers/data bases/restorePoints/verwijderen | Hiermee verwijdert u een herstel punt voor de data base. |
> | Bewerking | Micro soft. SQL/servers/data bases/restorePoints/lezen | Retourneert herstel punten voor de data base. |
> | Bewerking | Micro soft. SQL/servers/data bases/hervatten/actie | Azure SQL Data Warehouse database hervatten |
> | Bewerking | Micro soft. SQL/servers/data bases/schema's/lezen | Een database schema ophalen. |
> | Bewerking | Micro soft. SQL/servers/data bases/schema's/tabellen/kolommen/lezen | Een database kolom ophalen. |
> | Bewerking | Micro soft. SQL/servers/data bases/schema's/tabellen/kolommen/sensitivityLabels/verwijderen | Het gevoeligheids label van een bepaalde kolom verwijderen |
> | Bewerking | Micro soft. SQL/servers/data bases/schema's/tabellen/kolommen/sensitivityLabels/uitschakelen/actie | Gevoeligheids aanbevelingen voor een bepaalde kolom uitschakelen |
> | Bewerking | Micro soft. SQL/servers/data bases/schema's/tabellen/kolommen/sensitivityLabels/inschakelen/actie | Gevoeligheids aanbevelingen voor een bepaalde kolom inschakelen |
> | Bewerking | Micro soft. SQL/servers/data bases/schema's/tabellen/kolommen/sensitivityLabels/lezen | Het gevoeligheids label van een bepaalde kolom ophalen |
> | Bewerking | Micro soft. SQL/servers/data bases/schema's/tabellen/kolommen/sensitivityLabels/schrijven | Het gevoeligheids label van een bepaalde kolom maken of bijwerken |
> | Bewerking | Micro soft. SQL/servers/data bases/schema's/tabellen/lezen | Een database tabel ophalen. |
> | Bewerking | Micro soft. SQL/servers/data bases/schema's/Tables/recommendedIndexes/lezen | Lijst met index aanbevelingen voor een Data Base ophalen |
> | Bewerking | Micro soft. SQL/servers/data bases/schema's/tabellen/recommendedIndexes/schrijven | Aanbeveling index Toep assen |
> | Bewerking | Micro soft. SQL/servers/data bases/securityAlertPolicies/lezen | Een lijst ophalen met beleids regels voor het detecteren van de database bedreigingen die zijn geconfigureerd voor een bepaalde server |
> | Bewerking | Micro soft. SQL/servers/data bases/securityAlertPolicies/schrijven | Het beleid voor het detecteren van de beleids regels voor een bepaalde data base wijzigen |
> | Bewerking | Micro soft. SQL/servers/data bases/securityMetrics/lezen | Hiermee wordt een verzameling van metrische gegevens voor database beveiliging opgehaald |
> | Bewerking | Micro soft. SQL/servers/data bases/sensitivityLabels/lezen | Gevoeligheids labels van een bepaalde Data Base weer geven |
> | Bewerking | Micro soft. SQL/servers/data bases/serviceTierAdvisors/lezen | Retour suggestie over het omhoog of omlaag schalen van de Data Base op basis van statistieken voor query-uitvoering om prestaties te verbeteren of kosten te verlagen |
> | Bewerking | Micro soft. SQL/servers/data bases/sku's/lezen | Hiermee wordt een verzameling van sku's opgehaald die beschikbaar zijn voor een Data Base |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/cancelSync/actie | Synchronisatie groep annuleren |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/verwijderen | Hiermee verwijdert u een bestaande synchronisatie groep. |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/hubSchemas/lezen | De lijst met data base-schema's voor de synchronisatie hub retour neren |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/logboeken/lezen | De lijst met Logboeken voor synchronisatie groepen retour neren |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/lezen | De lijst met synchronisatie groepen retour neren of de eigenschappen voor de opgegeven synchronisatie groep ophalen. |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/refreshHubSchema/actie | Data base-schema voor synchronisatie-hub vernieuwen |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/refreshHubSchemaOperationResults/lezen | Resultaat van de bewerking synchronisatie hub-schema vernieuwen ophalen |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/syncMembers/verwijderen | Hiermee verwijdert u een bestaand synchronisatie-lid. |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/syncMembers/lezen | De lijst met synchronisatie leden retour neren of de eigenschappen van het opgegeven Sync-lid ophalen. |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/syncMembers/refreshSchema/Action | Synchronisatie leden schema vernieuwen |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/syncMembers/refreshSchemaOperationResults/lezen | Resultaat ophalen van de vernieuwings bewerking voor het synchronisatie leden schema |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/syncMembers/schema's/lezen | De lijst met database schema's van het synchronisatie-lid retour neren |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/syncMembers/schrijven | Hiermee maakt u een Sync-lid met de opgegeven para meters of werkt u de eigenschappen voor het opgegeven Sync-lid bij. |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/triggerSync/actie | Synchronisatie van synchronisatie groep activeren |
> | Bewerking | Micro soft. SQL/servers/data bases/syncGroups/schrijven | Hiermee maakt u een synchronisatie groep met de opgegeven para meters of werkt u de eigenschappen voor de opgegeven synchronisatie groep bij. |
> | Bewerking | Micro soft. SQL/servers/data bases/topQueries/queryText/actie | Hiermee wordt de Transact-SQL-tekst geretourneerd voor de geselecteerde query-ID |
> | Bewerking | Micro soft. SQL/servers/data bases/topQueries/lezen | Hiermee worden geaggregeerde runtime statistieken geretourneerd voor de geselecteerde query in de geselecteerde tijds periode |
> | Bewerking | Micro soft. SQL/servers/data bases/topQueries/statistieken/lezen | Hiermee worden geaggregeerde runtime statistieken geretourneerd voor de geselecteerde query in de geselecteerde tijds periode |
> | Bewerking | Micro soft. SQL/servers/data bases/transparentDataEncryption/operationResults/lezen | Hiermee worden bewerkingen uitgevoerd op de transparante gegevens versleuteling |
> | Bewerking | Micro soft. SQL/servers/data bases/transparentDataEncryption/lezen | De status en Details van de functie voor transparante gegevens versleuteling ophalen voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/servers/data bases/transparentDataEncryption/schrijven | Transparante gegevens versleutelings status wijzigen |
> | Bewerking | Micro soft. SQL/servers/data bases/upgradeDataWarehouse/actie | Een upgrade uitvoeren voor Azure SQL Data Warehouse database |
> | Bewerking | Micro soft. SQL/servers/data bases/gebruik/lezen | Hiermee worden de gegevens van het Azure SQL Database gebruik opgehaald |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/verwijderen | De evaluatie van beveiligings problemen voor een bepaalde data base verwijderen |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/lezen | Het beleid voor evaluatie van beveiligings problemen ophalen voor een givendatabase |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/regels/basis lijnen/verwijderen | De regel basislijn voor evaluatie van beveiligings problemen verwijderen voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/regels/basis lijnen/lezen | De regel basislijn voor evaluatie van beveiligings problemen ophalen voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/regels/basis lijnen/schrijven | De regel voor de evaluatie van beveiligings problemen wijzigen voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/scans/exporteren/actie | Een bestaand scan resultaat converteren naar een lees bare indeling. Als er al bestaat, gebeurt er niets |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/scans/initiateScan/Action | Voer de database scan voor evaluatie van beveiligings problemen uit. |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/scans/lezen | Retour neer de lijst met evaluatie records van het beveiligings probleem voor de data base of haal de scan record op voor de opgegeven scan-ID. |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessments/schrijven | De evaluatie van beveiligings problemen voor een bepaalde data base wijzigen |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessmentScans/actie | Voer de database scan voor evaluatie van beveiligings problemen uit. |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessmentScans/operationResults/lezen | Ophalen van het resultaat van de data base-evaluatie van het beveiligings probleem beoordelings bewerking |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessmentSettings/lezen | Details ophalen van de evaluatie van beveiligings problemen die is geconfigureerd voor een bepaalde data base |
> | Bewerking | Micro soft. SQL/servers/data bases/vulnerabilityAssessmentSettings/schrijven | De evaluatie van beveiligings problemen voor een bepaalde data base wijzigen |
> | Bewerking | Micro soft. SQL/servers/data bases/schrijven | Hiermee maakt u een Data Base met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven Data Base bij. |
> | Bewerking | Micro soft. SQL/servers/verwijderen | Hiermee verwijdert u een bestaande server. |
> | Bewerking | Micro soft. SQL/servers/disasterRecoveryConfiguration/verwijderen | Hiermee verwijdert u een bestaande configuratie voor herstel na nood gevallen voor een bepaalde server |
> | Bewerking | Micro soft. SQL/servers/disasterRecoveryConfiguration/failover/actie | Een DisasterRecoveryConfiguration failover |
> | Bewerking | Micro soft. SQL/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/actie | Failover van een DisasterRecoveryConfiguration forceren |
> | Bewerking | Micro soft. SQL/servers/disasterRecoveryConfiguration/lezen | Hiermee wordt een verzameling configuraties voor herstel na nood gevallen opgehaald die deze server bevatten |
> | Bewerking | Micro soft. SQL/servers/disasterRecoveryConfiguration/schrijven | Configuratie voor nood herstel van de server wijzigen |
> | Bewerking | Micro soft. SQL/servers/elasticPoolEstimates/lezen | Hiermee wordt een lijst geretourneerd met schattingen van elastische groepen die al zijn gemaakt voor deze server |
> | Bewerking | Micro soft. SQL/servers/elasticPoolEstimates/schrijven | Hiermee maakt u een nieuwe schatting voor een elastische pool voor een lijst met data bases |
> | Bewerking | Micro soft. SQL/servers/elasticPools/advisores/lezen | Hiermee wordt een lijst met beschik bare Advisor voor de elastische pool geretourneerd |
> | Bewerking | Micro soft. SQL/servers/elasticPools/Advisors/recommendedActions/lezen | Hiermee wordt een lijst met aanbevolen acties van opgegeven Advisor voor de elastische pool geretourneerd |
> | Bewerking | Micro soft. SQL/servers/elasticPools/Advisors/recommendedActions/schrijven | De aanbevolen actie Toep assen op de elastische pool |
> | Bewerking | Micro soft. SQL/servers/elasticPools/advisores/schrijven | Update status automatisch uitvoeren van een adviseur op het niveau van de elastische groep. |
> | Bewerking | Micro soft. SQL/servers/elasticPools/data bases/lezen | Hiermee wordt een lijst met data bases voor een elastische pool opgehaald |
> | Bewerking | Micro soft. SQL/servers/elasticPools/verwijderen | Bestaande elastische pool verwijderen |
> | Bewerking | Micro soft. SQL/servers/elasticPools/elasticPoolActivity/lezen | Activiteiten en Details ophalen voor een bepaalde pool voor Elastic data base |
> | Bewerking | Micro soft. SQL/servers/elasticPools/elasticPoolDatabaseActivity/lezen | Activiteiten en Details ophalen voor een bepaalde data base die deel uitmaakt van een pool voor elastische data bases |
> | Bewerking | Micro soft. SQL/servers/elasticPools/failover/actie | Door klant geïnitieerde failover van elastische pool. |
> | Bewerking | Micro soft. SQL/servers/elasticPools/metricDefinitions/lezen | Typen metrische gegevens retour neren die beschikbaar zijn voor Pools voor elastische data bases |
> | Bewerking | Micro soft. SQL/servers/elasticPools/metrieken/lezen | Metrische gegevens retour neren voor Pools voor elastische data bases |
> | Bewerking | Micro soft. SQL/servers/elasticPools/bewerkingen/annuleren/actie | Hiermee wordt een asynchrone bewerking die nog niet is voltooid, geannuleerd in Azure SQL elastische pool. |
> | Bewerking | Micro soft. SQL/servers/elasticPools/Operations/lezen | De lijst met uitgevoerde bewerkingen voor de elastische pool retour neren |
> | Bewerking | Micro soft. SQL/servers/elasticPools/providers/micro soft. Insights/diagnosticSettings/lezen | Hiermee wordt de diagnostische instelling voor de resource opgehaald |
> | Bewerking | Micro soft. SQL/servers/elasticPools/providers/micro soft. Insights/diagnosticSettings/schrijven | Hiermee wordt de diagnostische instelling voor de resource gemaakt of bijgewerkt |
> | Bewerking | Micro soft. SQL/servers/elasticPools/providers/micro soft. Insights/metricDefinitions/lezen | Typen metrische gegevens retour neren die beschikbaar zijn voor Pools voor elastische data bases |
> | Bewerking | Micro soft. SQL/servers/elasticPools/lezen | Details van elastische pool op een bepaalde server ophalen |
> | Bewerking | Micro soft. SQL/servers/elasticPools/sku's/lezen | Hiermee wordt een verzameling sku's opgehaald die beschikbaar zijn voor een elastische pool |
> | Bewerking | Micro soft. SQL/servers/elasticPools/schrijven | Een nieuwe of gewijzigde eigenschappen van een bestaande elastische pool maken |
> | Bewerking | Micro soft. SQL/servers/encryptionProtector/lezen | Hiermee wordt een lijst met server versleutelings beveiligingen geretourneerd of worden de eigenschappen opgehaald voor de opgegeven server versleutelings beveiliging. |
> | Bewerking | Micro soft. SQL/servers/encryptionProtector/revalidate/Action | Werk de eigenschappen voor de opgegeven server versleutelings beveiliging bij. |
> | Bewerking | Micro soft. SQL/servers/encryptionProtector/schrijven | Werk de eigenschappen voor de opgegeven server versleutelings beveiliging bij. |
> | Bewerking | Micro soft. SQL/servers/extendedAuditingSettings/lezen | Details ophalen van het uitgebreide-server-BLOB-controle beleid dat op een bepaalde server is geconfigureerd |
> | Bewerking | Micro soft. SQL/servers/extendedAuditingSettings/schrijven | De controle van de uitgebreide server-BLOB voor een bepaalde server wijzigen |
> | Bewerking | Micro soft. SQL/servers/failoverGroups/verwijderen | Hiermee verwijdert u een bestaande failovergroep. |
> | Bewerking | Micro soft. SQL/servers/failoverGroups/failover/actie | Voert een geplande failover uit in een bestaande failovergroep. |
> | Bewerking | Micro soft. SQL/servers/failoverGroups/forceFailoverAllowDataLoss/actie | Hiermee wordt geforceerde failover uitgevoerd in een bestaande failovergroep. |
> | Bewerking | Micro soft. SQL/servers/failoverGroups/lezen | Retourneert de lijst met failover-groepen of haalt de eigenschappen voor de opgegeven failovergroep op. |
> | Bewerking | Micro soft. SQL/servers/failoverGroups/schrijven | Hiermee maakt u een failovergroep met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven failovergroep bij. |
> | Bewerking | Micro soft. SQL/servers/firewallRules/verwijderen | Hiermee verwijdert u een bestaande server firewall regel. |
> | Bewerking | Micro soft. SQL/servers/firewallRules/lezen | De lijst met Server firewall regels retour neren of de eigenschappen voor de opgegeven server firewall regel ophalen. |
> | Bewerking | Micro soft. SQL/servers/firewallRules/schrijven | Hiermee maakt u een server firewall regel met de opgegeven para meters, werkt u de eigenschappen voor de opgegeven regel bij of overschrijft u alle bestaande regels met een nieuwe server firewall regel (s). |
> | Bewerking | Micro soft. SQL/servers/importeren/actie | Een nieuwe Data Base op de server maken en schema's en gegevens uit een DacPac-pakket implementeren |
> | Bewerking | Micro soft. SQL/servers/importExportOperationResults/lezen | Hiermee worden bewerkingen voor importeren/exporteren uitgevoerd |
> | Bewerking | Micro soft. SQL/servers/interfaceEndpointProfiles/verwijderen | Hiermee wordt het opgegeven interface-eindpunt profiel verwijderd |
> | Bewerking | Micro soft. SQL/servers/interfaceEndpointProfiles/lezen | Hiermee worden de eigenschappen van het opgegeven interface-eindpunt profiel geretourneerd |
> | Bewerking | Micro soft. SQL/servers/interfaceEndpointProfiles/schrijven | Hiermee maakt u een interface-eindpunt profiel met de opgegeven para meters of werkt u de eigenschappen of labels voor het opgegeven interface-eind punt bij |
> | Bewerking | Micro soft. SQL/servers/jobAgents/verwijderen | Hiermee verwijdert u een Azure SQL DB-taak agent |
> | Bewerking | Micro soft. SQL/servers/jobAgents/lezen | Hiermee wordt een Azure SQL DB-taak agent opgehaald |
> | Bewerking | Micro soft. SQL/servers/jobAgents/schrijven | Hiermee wordt een Azure SQL DB-taak agent gemaakt of bijgewerkt |
> | Bewerking | Micro soft. SQL/servers/sleutels/verwijderen | Hiermee verwijdert u een bestaande server sleutel. |
> | Bewerking | Micro soft. SQL/servers/sleutels/lezen | De lijst met Server sleutels retour neren of de eigenschappen voor de opgegeven server sleutel ophalen. |
> | Bewerking | Micro soft. SQL/servers/sleutels/schrijven | Hiermee maakt u een sleutel met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server sleutel bij. |
> | Bewerking | Micro soft. SQL/servers/operationResults/lezen | Hiermee worden Server bewerkingen uitgevoerd |
> | Bewerking | Micro soft. SQL/servers/privateEndpointConnectionProxies/verwijderen | Hiermee verwijdert u een bestaande verbindings proxy voor een persoonlijk eind punt |
> | Bewerking | Micro soft. SQL/servers/privateEndpointConnectionProxies/lezen | Hiermee wordt de lijst met particuliere endpoint-verbindings proxy's geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbinding proxy voor het particuliere eind punt. |
> | Bewerking | Micro soft. SQL/servers/privateEndpointConnectionProxies/valideren/actie | Hiermee wordt een aanroep voor het maken van een privé-eind punt gevalideerd aan de kant van de NRP |
> | Bewerking | Micro soft. SQL/servers/privateEndpointConnectionProxies/schrijven | Hiermee maakt u een particuliere endpoint-verbindings proxy met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven verbinding proxy van het particuliere eind punt bij. |
> | Bewerking | Micro soft. SQL/servers/privateEndpointConnections/verwijderen | Hiermee verwijdert u een bestaande persoonlijke eindpunt verbinding |
> | Bewerking | Micro soft. SQL/servers/privateEndpointConnections/lezen | Hiermee wordt de lijst met privé-eindpunt verbindingen geretourneerd of worden de eigenschappen opgehaald voor de opgegeven verbinding met een privé-eind punt. |
> | Bewerking | Micro soft. SQL/servers/privateEndpointConnections/schrijven | Hiermee wordt een bestaande verbinding met een privé-eind punt goedgekeurd of geweigerd |
> | Bewerking | Micro soft. SQL/servers/privateEndpointConnectionsApproval/actie | Hiermee wordt bepaald of de gebruiker een verbinding met een privé-eind punt mag goed keuren |
> | Bewerking | Micro soft. SQL/servers/privateLinkResources/lezen | De persoonlijke koppelings resources voor de bijbehorende SQL Server ophalen |
> | Bewerking | Micro soft. SQL/servers/providers/micro soft. Insights/metricDefinitions/lezen | Typen metrische gegevens retour neren die beschikbaar zijn voor servers |
> | Bewerking | Micro soft. SQL/servers/lezen | De lijst met servers retour neren of de eigenschappen voor de opgegeven server ophalen. |
> | Bewerking | Micro soft. SQL/servers/recommendedElasticPools/data bases/lezen | Metrische gegevens ophalen voor Aanbevolen Pools voor elastische data bases voor een bepaalde server |
> | Bewerking | Micro soft. SQL/servers/recommendedElasticPools/lezen | Haal aanbevelingen op voor Pools voor elastische data bases om de kosten te verlagen of de prestaties te verbeteren op basis van historisch resource gebruik |
> | Bewerking | Micro soft. SQL/servers/recoverableDatabases/lezen | Deze bewerking wordt gebruikt voor nood herstel van live data base om de data base terug te zetten naar het laatst bekende goede back-uppunt. Het retourneert informatie over de laatste goede back-up, maar doesn\u0027t de data base daad werkelijk te herstellen. |
> | Bewerking | Micro soft. SQL/servers/replicationLinks/lezen | De lijst met replicatie koppelingen retour neren of de eigenschappen ophalen voor de opgegeven replicatie koppelingen. |
> | Bewerking | Micro soft. SQL/servers/restorableDroppedDatabases/lezen | Een lijst ophalen met data bases die zijn verwijderd op een bepaalde server en nog steeds binnen het Bewaar beleid vallen. |
> | Bewerking | Micro soft. SQL/servers/securityAlertPolicies/operationResults/lezen | De resultaten van de Write-bewerking van het server bedreigings detectie beleid ophalen |
> | Bewerking | Micro soft. SQL/servers/securityAlertPolicies/lezen | Een lijst met beleids regels voor Server bedreigings detectie ophalen die zijn geconfigureerd voor een bepaalde server |
> | Bewerking | Micro soft. SQL/servers/securityAlertPolicies/schrijven | Het server Threat Detection-beleid voor een bepaalde server wijzigen |
> | Bewerking | Micro soft. SQL/servers/serviceObjectives/lezen | Lijst met serviceniveau doelstellingen ophalen (ook wel prestatie lagen genoemd) die beschikbaar zijn op een bepaalde server |
> | Bewerking | Micro soft. SQL/servers/syncAgents/verwijderen | Hiermee verwijdert u een bestaande synchronisatie agent. |
> | Bewerking | Micro soft. SQL/servers/syncAgents/generateKey/actie | Registratie sleutel synchronisatie agent genereren |
> | Bewerking | Micro soft. SQL/servers/syncAgents/linkedDatabases/lezen | De lijst met gekoppelde data bases van de gesynchroniseerde agent retour neren |
> | Bewerking | Micro soft. SQL/servers/syncAgents/lezen | De lijst met synchronisatie agenten retour neren of de eigenschappen voor de opgegeven synchronisatie agent ophalen. |
> | Bewerking | Micro soft. SQL/servers/syncAgents/schrijven | Hiermee maakt u een synchronisatie agent met de opgegeven para meters of werkt u de eigenschappen voor de opgegeven synchronisatie agent bij. |
> | Bewerking | Micro soft. SQL/servers/tdeCertificates/actie | TDE-certificaat maken/bijwerken |
> | Bewerking | Micro soft. SQL/servers/gebruik/lezen | Het DTU-quotum van de server en het huidige DTU-verbruik voor alle data bases op de server retour neren |
> | Bewerking | Micro soft. SQL/servers/virtualNetworkRules/verwijderen | Hiermee verwijdert u een bestaande Virtual Network regel |
> | Bewerking | Micro soft. SQL/servers/virtualNetworkRules/lezen | De lijst met regels voor het virtuele netwerk retour neren of de eigenschappen voor de opgegeven virtuele netwerk regel ophalen. |
> | Bewerking | Micro soft. SQL/servers/virtualNetworkRules/schrijven | Hiermee maakt u een regel voor het virtuele netwerk met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven virtuele-netwerk regel bij. |
> | Bewerking | Micro soft. SQL/servers/vulnerabilityAssessments/verwijderen | De evaluatie van beveiligings problemen voor een bepaalde server verwijderen |
> | Bewerking | Micro soft. SQL/servers/vulnerabilityAssessments/lezen | Het beleid voor evaluatie van beveiligings problemen op een bepaalde server ophalen |
> | Bewerking | Micro soft. SQL/servers/vulnerabilityAssessments/schrijven | De evaluatie van beveiligings problemen voor een bepaalde server wijzigen |
> | Bewerking | Micro soft. SQL/servers/schrijven | Hiermee maakt u een server met de opgegeven para meters of werkt u de eigenschappen of labels voor de opgegeven server bij. |
> | Bewerking | Micro soft. SQL/niet registreren/actie | Hiermee wordt de registratie van het abonnement voor de resource provider van micro soft SQL Database ongedaan gemaakt en wordt het maken van micro soft SQL-data bases ingeschakeld. |
> | Bewerking | Micro soft. SQL/virtualClusters/verwijderen | Hiermee verwijdert u een bestaand virtueel cluster. |
> | Bewerking | Micro soft. SQL/virtualClusters/lezen | De lijst met virtuele clusters retour neren of de eigenschappen van het opgegeven virtuele cluster ophalen. |
> | Bewerking | Micro soft. SQL/virtualClusters/schrijven | Hiermee werkt u virtuele-cluster Tags bij. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Storage/checknameavailability/lezen | Controleert of de account naam geldig is en niet wordt gebruikt. |
> | Bewerking | Micro soft. Storage/locaties/checknameavailability/lezen | Controleert of de account naam geldig is en niet wordt gebruikt. |
> | Bewerking | Micro soft. Storage/locaties/deleteVirtualNetworkOrSubnets/actie | Hiermee wordt aan micro soft. Storage door gemeld dat het virtuele netwerk of subnet wordt verwijderd |
> | Bewerking | Micro soft. Storage/vestigingen/gebruik/lezen | Retourneert de limiet en het huidige gebruiks aantal voor resources in het opgegeven abonnement |
> | Bewerking | Micro soft. Storage/Operations/lezen | Controleert de status van een asynchrone bewerking. |
> | Bewerking | Micro soft. Storage/REGI ster/actie | Hiermee wordt het abonnement voor de opslag Resource provider geregistreerd en wordt het maken van opslag accounts mogelijk. |
> | Bewerking | Micro soft. Storage/sku's/lezen | Een lijst met de Sku's die worden ondersteund door micro soft. storage. |
> | DataAction | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/toevoegen/actie | Retourneert het resultaat van het toevoegen van blob-inhoud |
> | DataAction | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/verwijderen | Hiermee wordt het resultaat van het verwijderen van een BLOB geretourneerd |
> | DataAction | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/deleteAutomaticSnapshot/Action | Retourneert het resultaat van het verwijderen van een automatische moment opname |
> | DataAction | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/filter/actie | Retourneert de lijst met blobs onder een account met het filter overeenkomende labels |
> | DataAction | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/lezen | Retourneert een BLOB of een lijst met blobs |
> | DataAction | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/runAsSuperUser/Action | Retourneert het resultaat van de BLOB-opdracht |
> | DataAction | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/tags/lezen | Retourneert het resultaat van het lezen van BLOB-Tags |
> | DataAction | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/Tags/schrijven | Retourneert het resultaat van het schrijven van BLOB-Tags |
> | DataAction | Micro soft. Storage/Storage accounts/blobServices/containers/blobs/schrijven | Hiermee wordt het resultaat van het schrijven van een BLOB geretourneerd |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/clearLegalHold/actie | Juridische bewaring van BLOB-container wissen |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/verwijderen | Retourneert het resultaat van het verwijderen van een container |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/immutabilityPolicies/verwijderen | Onveranderbaarheid-beleid van BLOB-container verwijderen |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/immutabilityPolicies/Extend/Action | Onveranderbaarheid-beleid van BLOB-container uitbreiden |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/immutabilityPolicies/Lock/Action | Onveranderbaarheid-beleid voor BLOB-container vergren delen |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/immutabilityPolicies/lezen | Onveranderbaarheid-beleid voor BLOB-container ophalen |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/immutabilityPolicies/schrijven | Onveranderbaarheid-beleid voor BLOB-container plaatsen |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/lease/actie | Retourneert het resultaat van de lease-BLOB-container |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/lezen | Retourneert een container |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/lezen | Hiermee wordt een lijst met containers opgehaald |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/setLegalHold/actie | Juridische bewaring van BLOB-container instellen |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/schrijven | Retourneert het resultaat van een patch BLOB-container |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/containers/schrijven | Retourneert het resultaat van de put-BLOB-container |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey/actie | Retourneert een gebruikers delegering sleutel voor de BLOB-service |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/lezen |  |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/lezen | Hiermee worden de eigenschappen of statistieken van de BLOB-service geretourneerd |
> | Bewerking | Micro soft. Storage/Storage accounts/blobServices/schrijven | Retourneert het resultaat van de eigenschappen van de put BLOB-service |
> | Bewerking | Micro soft. Storage/Storage accounts/verwijderen | Hiermee verwijdert u een bestaand opslag account. |
> | Bewerking | Micro soft. Storage/Storage accounts/encryptionScopes/lezen |  |
> | Bewerking | Micro soft. Storage/Storage accounts/encryptionScopes/schrijven |  |
> | Bewerking | Micro soft. Storage/Storage accounts/failover/actie | De klant kan de failover beheren in geval van beschikbaarheids problemen |
> | DataAction | Micro soft. Storage/Storage accounts/fileServices/bestands shares/files/actassuperuser/Action | Machtigingen voor bestands beheerders ophalen |
> | DataAction | Micro soft. Storage/Storage accounts/fileServices/bestands shares/bestanden/verwijderen | Retourneert het resultaat van het verwijderen van een bestand/map |
> | DataAction | Micro soft. Storage/Storage accounts/fileServices/bestands shares/files/modifypermissions/Action | Hiermee wordt het resultaat van het wijzigen van de machtiging voor een bestand/map geretourneerd |
> | DataAction | Micro soft. Storage/Storage accounts/fileServices/bestands shares/bestanden/lezen | Retourneert een bestand/map of een lijst met bestanden/mappen |
> | DataAction | Micro soft. Storage/Storage accounts/fileServices/bestands shares/bestanden/schrijven | Retourneert het resultaat van het schrijven van een bestand of het maken van een map |
> | Bewerking | Micro soft. Storage/Storage accounts/fileServices/lezen |  |
> | Bewerking | Micro soft. Storage/Storage accounts/fileServices/lezen | Eigenschappen van bestands service ophalen |
> | Bewerking | Micro soft. Storage/Storage accounts/fileServices/shares/verwijderen |  |
> | Bewerking | Micro soft. Storage/Storage accounts/fileServices/shares/lezen |  |
> | Bewerking | Micro soft. Storage/Storage accounts/fileServices/shares/lezen |  |
> | Bewerking | Micro soft. Storage/Storage accounts/fileServices/shares/schrijven |  |
> | Bewerking | Micro soft. Storage/Storage accounts/fileServices/schrijven |  |
> | Bewerking | Micro soft. Storage/Storage accounts/listAccountSas/Action | Hiermee wordt het SAS-token van het account voor het opgegeven opslag account geretourneerd. |
> | Bewerking | Micro soft. Storage/Storage accounts/listkeys ophalen/Action | Retourneert de toegangs sleutels voor het opgegeven opslag account. |
> | Bewerking | Micro soft. Storage/Storage accounts/listServiceSas/Action | Hiermee wordt het SAS-token van de service voor het opgegeven opslag account geretourneerd. |
> | Bewerking | Micro soft. Storage/Storage accounts/managementPolicies/verwijderen | Beheer beleid voor opslag accounts verwijderen |
> | Bewerking | Micro soft. Storage/Storage accounts/managementPolicies/lezen | Beleid voor opslag beheer accounts ophalen |
> | Bewerking | Micro soft. Storage/Storage accounts/managementPolicies/schrijven | Opslag account beheer beleid plaatsen |
> | Bewerking | Micro soft. Storage/Storage accounts/objectReplicationPolicies/verwijderen |  |
> | Bewerking | Micro soft. Storage/Storage accounts/objectReplicationPolicies/lezen |  |
> | Bewerking | Micro soft. Storage/Storage accounts/objectReplicationPolicies/schrijven |  |
> | Bewerking | Micro soft. Storage/Storage accounts/privateEndpointConnectionProxies/verwijderen | Particuliere endpoint-verbindings Proxy's verwijderen |
> | Bewerking | Micro soft. Storage/Storage accounts/privateEndpointConnectionProxies/lezen | Proxy voor verbinding met privé-eind punt ophalen |
> | Bewerking | Micro soft. Storage/Storage accounts/privateEndpointConnectionProxies/schrijven | Connect-Proxy's voor privé-eind punt plaatsen |
> | Bewerking | Micro soft. Storage/Storage accounts/privateEndpointConnections/verwijderen | Verbinding voor privé-eind punt verwijderen |
> | Bewerking | Micro soft. Storage/Storage accounts/privateEndpointConnections/lezen | Verbinding voor privé-eind punt ophalen |
> | Bewerking | Micro soft. Storage/Storage accounts/privateEndpointConnections/schrijven | Verbinding voor privé-eind punt plaatsen |
> | Bewerking | Micro soft. Storage/Storage accounts/PrivateEndpointConnectionsApproval/Action | Privé-eindpunt verbindingen goed keuren |
> | Bewerking | Micro soft. Storage/Storage accounts/privateLinkResources/lezen | Storage account groupids ophalen |
> | Bewerking | Micro soft. Storage/Storage accounts/queueServices/queues/verwijderen | Retourneert het resultaat van het verwijderen van een wachtrij |
> | DataAction | Micro soft. Storage/Storage accounts/queueServices/queues/berichten/toevoegen/actie | Retourneert het resultaat van het toevoegen van een bericht |
> | DataAction | Micro soft. Storage/Storage accounts/queueServices/queues/berichten/verwijderen | Retourneert het resultaat van het verwijderen van een bericht |
> | DataAction | Micro soft. Storage/Storage accounts/queueServices/queues/berichten/proces/actie | Retourneert het resultaat van de verwerking van een bericht |
> | DataAction | Micro soft. Storage/Storage accounts/queueServices/queues/berichten/lezen | Retourneert een bericht |
> | DataAction | Micro soft. Storage/Storage accounts/queueServices/queues/berichten/schrijven | Hiermee wordt het resultaat van het schrijven van een bericht geretourneerd |
> | Bewerking | Micro soft. Storage/Storage accounts/queueServices/queues/Read | Hiermee wordt een wachtrij of een lijst met wacht rijen geretourneerd. |
> | Bewerking | Micro soft. Storage/Storage accounts/queueServices/queues/write | Retourneert het resultaat van het schrijven van een wachtrij |
> | Bewerking | Micro soft. Storage/Storage accounts/queueServices/lezen | Queue-service eigenschappen ophalen |
> | Bewerking | Micro soft. Storage/Storage accounts/queueServices/lezen | Retourneert eigenschappen of statistieken van de wachtrij service. |
> | Bewerking | Micro soft. Storage/Storage accounts/queueServices/schrijven | Retourneert het resultaat van het instellen van de eigenschappen van de wachtrij service |
> | Bewerking | Micro soft. Storage/Storage accounts/lezen | Retourneert de lijst met opslag accounts of haalt de eigenschappen voor het opgegeven opslag account op. |
> | Bewerking | Micro soft. Storage/Storage accounts/regeneratekey/Action | Hiermee worden de toegangs sleutels voor het opgegeven opslag account opnieuw gegenereerd. |
> | Bewerking | Micro soft. Storage/Storage accounts/restoreBlobRanges/Action | BLOB-bereiken herstellen naar de status van de opgegeven tijd |
> | Bewerking | Micro soft. Storage/Storage accounts/revokeUserDelegationKeys/Action | Hiermee worden alle sleutels voor gebruikers overdracht voor het opgegeven opslag account ingetrokken. |
> | Bewerking | Micro soft. Storage/Storage accounts/Services/diagnosticSettings/schrijven | Diagnostische instellingen van opslag account maken/bijwerken. |
> | Bewerking | Micro soft. Storage/Storage accounts/tableServices/lezen | Table service eigenschappen ophalen |
> | Bewerking | Micro soft. Storage/Storage accounts/schrijven | Hiermee maakt u een opslag account met de opgegeven para meters of werkt u de eigenschappen of labels bij of voegt u een aangepast domein toe voor het opgegeven opslag account. |
> | Bewerking | Micro soft. opslag/gebruik/lezen | Retourneert de limiet en het huidige gebruiks aantal voor resources in het opgegeven abonnement |

## <a name="microsoftstoragesync"></a>micro soft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | micro soft. storagesync/locaties/checkNameAvailability/actie | Controleert of de naam van de opslag synchronisatie service geldig is en niet wordt gebruikt. |
> | Bewerking | micro soft. storagesync/locaties/werk stromen/bewerkingen/lezen | Hiermee wordt de status van een asynchrone bewerking opgehaald |
> | Bewerking | micro soft. storagesync/Operations/lezen | Hiermee wordt een lijst met ondersteunde bewerkingen opgehaald |
> | Bewerking | micro soft. storagesync/REGI ster/actie | Hiermee wordt het abonnement voor de opslag synchronisatie provider geregistreerd |
> | Bewerking | micro soft. storagesync/storageSyncServices/verwijderen | Opslag synchronisatie Services verwijderen |
> | Bewerking | micro soft. storagesync/storageSyncServices/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor opslag synchronisatie Services |
> | Bewerking | micro soft. storagesync/storageSyncServices/lezen | Alle opslag synchronisatie services lezen |
> | Bewerking | micro soft. storagesync/storageSyncServices/registeredServer/verwijderen | Alle geregistreerde servers verwijderen |
> | Bewerking | micro soft. storagesync/storageSyncServices/registeredServers/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor de geregistreerde server |
> | Bewerking | micro soft. storagesync/storageSyncServices/registeredServer/lezen | Een geregistreerde server lezen |
> | Bewerking | micro soft. storagesync/storageSyncServices/registeredServer/write | Een geregistreerde server maken of bijwerken |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/verwijderen | Alle Cloud eindpunten verwijderen |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/lezen | Hiermee wordt de status van een asynchrone back-up-of herstel bewerking opgehaald |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/aanroep/actie | Deze actie aanroepen na een back-up |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/actie | Deze actie aanroepen na het herstellen |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/Action | Deze actie aanroepen voordat u een back-up maakt |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/prevorigformaat/actie | Deze actie aanroepen voordat deze wordt hersteld |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/lezen | Alle Cloud eindpunten lezen |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/actie | Heartbeat herstellen |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/schrijven | Alle Cloud eindpunten maken of bijwerken |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/verwijderen | Alle synchronisatie groepen verwijderen |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor synchronisatie groepen |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/lezen | Alle synchronisatie groepen lezen |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints/verwijderen | Server eindpunten verwijderen |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor Server-eind punten |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints/lezen | Server eindpunten lezen |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/actie | Deze actie aanroepen om bestanden op een server op te halen |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints/schrijven | Server eindpunten maken of bijwerken |
> | Bewerking | micro soft. storagesync/storageSyncServices/syncGroups/write | Synchronisatie groepen maken of bijwerken |
> | Bewerking | micro soft. storagesync/storageSyncServices/workflows/operationresults/lezen | Hiermee wordt de status van een asynchrone bewerking opgehaald |
> | Bewerking | micro soft. storagesync/storageSyncServices/workflows/Operations/lezen | Hiermee wordt de status van een asynchrone bewerking opgehaald |
> | Bewerking | micro soft. storagesync/storageSyncServices/workflows/lezen | Werk stromen lezen |
> | Bewerking | micro soft. storagesync/storageSyncServices/schrijven | Opslag synchronisatie Services maken of bijwerken |
> | Bewerking | micro soft. storagesync/registratie/actie | Hiermee wordt de registratie van het abonnement voor de opslag synchronisatie provider ongedaan gemaakt |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. StorSimple/managers/accessControlRecords/verwijderen | Hiermee verwijdert u de Access Control records |
> | Bewerking | Micro soft. StorSimple/managers/accessControlRecords/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/accessControlRecords/lezen | De Access Control records weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/accessControlRecords/schrijven | De Access Control records maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/waarschuwingen/lezen | De waarschuwingen weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/back-ups/lezen | De back-upset weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/bandwidthSettings/verwijderen | Hiermee verwijdert u een bestaande band breedte-instelling (alleen 8000 Series) |
> | Bewerking | Micro soft. StorSimple/managers/bandwidthSettings/operationResults/lezen | De bewerkings resultaten weer geven |
> | Bewerking | Micro soft. StorSimple/managers/bandwidthSettings/lezen | De band breedte-instellingen weer geven (alleen 8000 Series) |
> | Bewerking | Micro soft. StorSimple/managers/bandwidthSettings/schrijven | Hiermee maakt u een nieuwe of bijgewerkte band breedte-instellingen (alleen 8000 Series) |
> | Bewerking | Micro soft. StorSimple/managers/certificaten/schrijven | De certificaten maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/certificaten/schrijven | Met de bewerking resource certificaat bijwerken wordt het resource/kluis-referentie certificaat bijgewerkt. |
> | Bewerking | Micro soft. StorSimple/managers/clearAlerts/actie | Wis alle waarschuwingen die zijn gekoppeld aan Apparaatbeheer. |
> | Bewerking | Micro soft. StorSimple/managers/cloudApplianceConfigurations/lezen | De ondersteunde configuraties van het Cloud apparaat weer geven |
> | Bewerking | Micro soft. StorSimple/managers/configureDevice/actie | Hiermee configureert u een apparaat |
> | Bewerking | Micro soft. StorSimple/managers/verwijderen | Hiermee worden de Apparaatbeheer verwijderd |
> | Bewerking | Micro soft. StorSimple/managers/verwijderen | Met de bewerking kluis verwijderen wordt de opgegeven Azure-resource van het type kluis verwijderd |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/alertSettings/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/alertSettings/lezen | De instellingen van de waarschuwing weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/alertSettings/schrijven | De instellingen voor waarschuwingen maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/authorizeForServiceEncryptionKeyRollover/actie | Machtigen voor overschakeling van service-versleutelings sleutel van apparaten |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupPolicies/back-up/actie | Maak een hand matige back-up om een back-up op aanvraag te maken van alle volumes die worden beveiligd door het beleid. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupPolicies/verwijderen | Hiermee verwijdert u een bestaande back-upbeleid (alleen 8000 Series) |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupPolicies/operationResults/lezen | De bewerkings resultaten weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupPolicies/lezen | Het back-upbeleid (alleen 8000 Series) weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupPolicies/planning/verwijderen | Hiermee worden bestaande Schema's verwijderd |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupPolicies/planningen/operationResults/lezen | De bewerkings resultaten weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupPolicies/planningen/lezen | De planningen weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupPolicies/planningen/schrijven | Hiermee maakt u een nieuwe of bijgewerkte planningen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupPolicies/schrijven | Hiermee maakt u een nieuw of bijgewerkt back-upbeleid (alleen 8000 Series) |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/back-ups/verwijderen | Hiermee verwijdert u de back-upset |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/back-ups/elementen/klonen/actie | Een share of volume klonen met behulp van een back-upelement. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/back-ups/elementen/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/back-ups/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/back-ups/lezen | De back-upset weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/back-ups/herstellen/actie | Herstel alle volumes uit een back-upset. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupScheduleGroups/verwijderen | Hiermee verwijdert u de back-upschema groepen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupScheduleGroups/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupScheduleGroups/lezen | De back-upschema groepen worden weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/backupScheduleGroups/schrijven | De back-upschema groepen maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/chapSettings/verwijderen | Hiermee worden de CHAP-instellingen verwijderd |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/chapSettings/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/chapSettings/lezen | De CHAP-instellingen weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/chapSettings/schrijven | De CHAP-instellingen maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/deactiveren/actie | Hiermee wordt een apparaat deactiveren. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/verwijderen | Hiermee worden de apparaten verwijderd |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/schijven/lezen | De schijven weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/downloaden/actie | Updates downloaden voor een apparaat. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/failover/actie | Failover van het apparaat. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/failover/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/failoverTargets/lezen | De failover-doelen van de apparaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/back-up/actie | Maak een back-up van een bestands server. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/verwijderen | Hiermee verwijdert u de bestands servers |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/metrieken/lezen | Hiermee worden de metrische gegevens weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/metricsDefinitions/lezen | Hiermee worden de metrische definities weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/lezen | De bestands servers weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/shares/verwijderen | Hiermee worden de shares verwijderd |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/shares/metrieken/lezen | Hiermee worden de metrische gegevens weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/shares/metricsDefinitions/lezen | Hiermee worden de metrische definities weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/shares/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/shares/lezen | De shares weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/shares/schrijven | De shares maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/fileservers/schrijven | Bestands servers maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/hardwareComponentGroups/changeControllerPowerState/actie | De energie status van de controller wijzigen van hardwarecomponenten |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/hardwareComponentGroups/operationResults/lezen | De bewerkings resultaten weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/hardwareComponentGroups/lezen | De hardware-onderdeel groepen weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/installeren/actie | Installeer updates op een apparaat. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/installUpdates/actie | Installeert updates op de apparaten (alleen 8000 Series). |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/back-up/actie | Maak een back-up van een iSCSI-server. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/verwijderen | De iSCSI-servers worden verwijderd |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/schijven/verwijderen | De schijven worden verwijderd |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/schijven/metrieken/lezen | Hiermee worden de metrische gegevens weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/schijven/metricsDefinitions/lezen | Hiermee worden de metrische definities weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/schijven/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/schijven/lezen | De schijven weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/schijven/schrijven | De schijven maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/metrieken/lezen | Hiermee worden de metrische gegevens weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/metricsDefinitions/lezen | Hiermee worden de metrische definities weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/lezen | De iSCSI-servers weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/iscsiservers/schrijven | De iSCSI-servers maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/taken/annuleren/actie | Een actieve taak annuleren |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/Jobs/operationResults/lezen | De bewerkings resultaten weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/taken/lezen | De taken weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/listFailoverSets/actie | De failover-sets voor een bestaand apparaat weer geven (alleen 8000 Series). |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/listFailoverTargets/actie | Failover-doelen van de apparaten weer geven (alleen 8000 Series). |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/metrieken/lezen | Hiermee worden de metrische gegevens weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/metricsDefinitions/lezen | Hiermee worden de metrische definities weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/confirmMigration/actie | Hiermee bevestigt u een geslaagde migratie en voert u deze door. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/confirmMigrationStatus/lezen | De status van de migratie bevestigen weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/fetchConfirmMigrationStatus/actie | De Bevestig status van de migratie ophalen. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/fetchMigrationEstimate/actie | Haal de status van de taak schatting voor migratie op. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/fetchMigrationStatus/actie | De status voor de migratie ophalen. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/importeren/actie | Bron configuraties voor migratie importeren |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/migrationEstimate/lezen | De schatting van de migratie weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/migrationStatus/lezen | De migratie status weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/operationResults/lezen | De bewerkings resultaten weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/startMigration/actie | Migratie starten met behulp van bron configuraties |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/migrationSourceConfigurations/startMigrationEstimate/actie | Start een taak om de duur van het migratie proces te schatten. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/networkSettings/operationResults/lezen | De bewerkings resultaten weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/networkSettings/lezen | Hiermee worden de netwerk instellingen weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/networkSettings/schrijven | Hiermee maakt u een nieuw of bijgewerkt netwerk instellingen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/publicEncryptionKey/actie | Open bare versleutelings sleutel van Apparaatbeheer weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/publishSupportPackage/actie | Publiceer het ondersteunings pakket voor een bestaand apparaat. Een StorSimple-ondersteunings pakket is een eenvoudig te gebruiken mechanisme dat alle relevante logboeken verzamelt om Microsoft Ondersteuning te helpen bij het oplossen van problemen met StorSimple-apparaten. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/lezen | De apparaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/scanForUpdates/actie | Zoeken naar updates op een apparaat. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/securitySettings/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/securitySettings/lezen | De beveiligings instellingen weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/securitySettings/syncRemoteManagementCertificate/actie | Het certificaat voor extern beheer voor een apparaat synchroniseren. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/securitySettings/update/actie | De beveiligings instellingen bijwerken. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/securitySettings/schrijven | Hiermee maakt u een nieuwe of bijgewerkte beveiligings instellingen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/sendTestAlertEmail/actie | Verzend een e-mail met een test waarschuwing naar geconfigureerde e-mail ontvangers. |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/shares/lezen | De shares weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/timeSettings/operationResults/lezen | De bewerkings resultaten weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/timeSettings/lezen | De tijd instellingen weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/timeSettings/schrijven | Hiermee maakt u een nieuwe of bijgewerkte tijd instellingen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/updates/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/update Summary/lezen | De samen vatting van de update weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumeContainers/verwijderen | Hiermee verwijdert u een bestaande volume container (alleen 8000 Series) |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumeContainers/metrieken/lezen | De metrische gegevens weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumeContainers/metricsDefinitions/lezen | De definities voor metrische gegevens weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumeContainers/operationResults/lezen | De bewerkings resultaten weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumeContainers/lezen | De volume containers weer geven (alleen 8000 Series) |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumeContainers/volumes/verwijderen | Hiermee verwijdert u een bestaand volume |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumeContainers/volumes/metrieken/lezen | De metrische gegevens weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumeContainers/volumes/metricsDefinitions/lezen | De definities voor metrische gegevens weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumeContainers/volumes/operationResults/lezen | De bewerkings resultaten weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumeContainers/volumes/lezen | De volumes weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumeContainers/volumes/schrijven | Hiermee maakt u een nieuw of bijgewerkt volume |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumeContainers/schrijven | Hiermee maakt u een nieuwe of bijgewerkte volume containers (alleen 8000 Series) |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/volumes/lezen | De volumes weer geven |
> | Bewerking | Micro soft. StorSimple/managers/apparaten/schrijven | De apparaten maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/encryptionSettings/lezen | De versleutelings instellingen weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/extendedInformation/verwijderen | Hiermee verwijdert u de uitgebreide kluis gegevens |
> | Bewerking | Micro soft. StorSimple/managers/extendedInformation/verwijderen | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Bewerking | Micro soft. StorSimple/managers/extendedInformation/lezen | De uitgebreide kluis gegevens weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/extendedInformation/lezen | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Bewerking | Micro soft. StorSimple/managers/extendedInformation/schrijven | De uitgebreide kluis gegevens maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/extendedInformation/schrijven | Met de bewerking uitgebreide informatie ophalen wordt de uitgebreide informatie opgehaald van een object dat de Azure-resource van het type? kluis vertegenwoordigt? |
> | Bewerking | Micro soft. StorSimple/managers/onderdelen/lezen | De functies weer geven |
> | Bewerking | Micro soft. StorSimple/managers/fileservers/lezen | De bestands servers weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/getEncryptionKey/actie | De versleutelings sleutel voor Apparaatbeheer ophalen. |
> | Bewerking | Micro soft. StorSimple/managers/iscsiservers/lezen | De iSCSI-servers weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/Jobs/lezen | De taken weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/listActivationKey/actie | Hiermee wordt de activerings sleutel van de StorSimple-Apparaatbeheer opgehaald. |
> | Bewerking | Micro soft. StorSimple/managers/listPublicEncryptionKey/actie | Open bare versleutelings sleutels van een StorSimple-Apparaatbeheer weer geven. |
> | Bewerking | Micro soft. StorSimple/managers/metrische gegevens/lezen | Hiermee worden de metrische gegevens weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/metricsDefinitions/lezen | Hiermee worden de metrische definities weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/migrateClassicToResourceManager/actie | Klassiek migreren naar Resource Manager van managers |
> | Bewerking | Micro soft. StorSimple/managers/migrationSourceConfigurations/lezen | De migratie bron configuraties weer geven (alleen 8000 Series) |
> | Bewerking | Micro soft. StorSimple/managers/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/provisionCloudAppliance/actie | Maak een nieuw Cloud apparaat. |
> | Bewerking | Micro soft. StorSimple/managers/lezen | Hiermee worden de Apparaatbeheer weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/lezen | Met de bewerking kluis ophalen wordt een object opgehaald dat de Azure-resource van het type ' kluis ' vertegenwoordigt. |
> | Bewerking | Micro soft. StorSimple/managers/regenerateActivationKey/actie | Genereer de activerings sleutel opnieuw voor een bestaande StorSimple-Apparaatbeheer. |
> | Bewerking | Micro soft. StorSimple/managers/storageAccountCredentials/verwijderen | Hiermee verwijdert u de referenties van het opslag account |
> | Bewerking | Micro soft. StorSimple/managers/storageAccountCredentials/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/storageAccountCredentials/lezen | De referenties van het opslag account worden weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/storageAccountCredentials/schrijven | De referenties van het opslag account maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/storageDomains/verwijderen | Hiermee worden de opslag domeinen verwijderd |
> | Bewerking | Micro soft. StorSimple/managers/storageDomains/operationResults/lezen | De bewerkings resultaten weer geven of ophalen |
> | Bewerking | Micro soft. StorSimple/managers/storageDomains/lezen | De opslag domeinen worden weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/managers/storageDomains/schrijven | De opslag domeinen maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/schrijven | Apparaatbeheer maken of bijwerken |
> | Bewerking | Micro soft. StorSimple/managers/schrijven | Met een kluis bewerking maken wordt een Azure-resource van het type ' kluis ' gemaakt. |
> | Bewerking | Micro soft. StorSimple/Operations/lezen | Hiermee worden de bewerkingen weer gegeven of opgehaald |
> | Bewerking | Micro soft. StorSimple/REGI ster/actie | Provider micro soft. StorSimple registreren |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. StreamAnalytics/locaties/quota's/lezen | Stream Analytics abonnements quotum lezen |
> | Bewerking | Micro soft. StreamAnalytics/Operations/lezen | Stream Analytics bewerkingen lezen |
> | Bewerking | Micro soft. StreamAnalytics/REGI ster/actie | Een abonnement bij Stream Analytics resource provider registreren |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/verwijderen | Stream Analytics taak verwijderen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/functions/verwijderen | Stream Analytics taak functie verwijderen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/functions/operationresults/lezen | Resultaten van de bewerking lezen voor Stream Analytics taak functie |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/functions/lezen | Stream Analytics taak functie lezen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/Action | De standaard definitie van een Stream Analytics taak functie ophalen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/functions/test/Action | Stream Analytics taak functie testen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/functions/write | Functie Write Stream Analytics-taak |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/invoer/verwijderen | Stream Analytics taak invoer verwijderen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/invoer/operationresults/lezen | Resultaten van de bewerking lezen voor Stream Analytics taak invoer |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/invoer/lezen | Stream Analytics taak invoer lezen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/invoer/voor beeld/actie | Voor beeld van Stream Analytics taak invoer |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/invoer/test/actie | Stream Analytics taak invoer testen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/invoer/schrijven | Invoer van Stream Analytics-taak schrijven |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/metricdefinitions/lezen | Metrische definities lezen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/operationresults/lezen | Resultaten van de bewerking lezen voor Stream Analytics taak |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/outputs/verwijderen | Stream Analytics taak uitvoer verwijderen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/outputs/operationresults/lezen | Resultaten van de bewerking lezen voor Stream Analytics taak uitvoer |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/outputs/lezen | Stream Analytics taak uitvoer lezen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/outputs/test/Action | Stream Analytics taak uitvoer testen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/outputs/schrijven | Stream Analytics taak uitvoer schrijven |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/providers/micro soft. Insights/diagnosticSettings/lezen | Diagnostische instelling lezen. |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/providers/micro soft. Insights/diagnosticSettings/schrijven | Diagnostische instelling schrijven. |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/providers/micro soft. Insights/logDefinitions/lezen | Hiermee worden de beschik bare logboeken voor streamingjobs opgehaald |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/providers/micro soft. Insights/metricDefinitions/lezen | Hiermee worden de beschik bare metrische gegevens opgehaald voor streamingjobs |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/lezen | Stream Analytics taak lezen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/start/Action | Stream Analytics taak starten |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/stop/Action | Stream Analytics taak stoppen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/trans formaties/verwijderen | Stream Analytics taak transformatie verwijderen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/trans formaties/lezen | Stream Analytics taak transformatie lezen |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/trans formaties/schrijven | Trans formatie van Stream Analytics-taak schrijven |
> | Bewerking | Micro soft. StreamAnalytics/streamingjobs/schrijven | Stream Analytics-taak schrijven |

## <a name="microsoftsubscription"></a>Micro soft. Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. abonnement/annuleren/actie | Hiermee wordt het abonnement geannuleerd |
> | Bewerking | Micro soft. Subscription/CreateSubscription/actie | Maak een Azure-abonnement |
> | Bewerking | Micro soft. abonnement/registreren/actie | Hiermee wordt een abonnement geregistreerd bij de resource provider micro soft. Subscription |
> | Bewerking | Micro soft. Subscription/naam wijzigen/actie | De naam van het abonnement wijzigen |
> | Bewerking | Micro soft. Subscription/SubscriptionDefinitions/lezen | Een definitie van een Azure-abonnement in een beheer groep ophalen. |
> | Bewerking | Micro soft. Subscription/SubscriptionDefinitions/schrijven | Een definitie van een Azure-abonnement maken |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Microsoft.Support/register/action | Hiermee wordt het item geregistreerd bij de resourceprovider voor ondersteuning |
> | Bewerking | Microsoft.Support/supportTickets/read | Hiermee worden gegevens over het ondersteuningsticket opgehaald (inclusief status, ernst, contactgegevens en communicatie) of wordt de lijst met ondersteuningstickets voor de verschillende abonnementen opgehaald. |
> | Bewerking | Microsoft.Support/supportTickets/write | Hiermee wordt een ondersteuningsticket gemaakt of bijgewerkt. U kunt een ondersteuningsticket maken voor problemen van technische aard of op het gebied van facturering, quota's of beheer van abonnementen. U kunt voor bestaande ondersteuningstickets de ernst, de contactgegevens en de communicatiemethoden aanpassen. |

## <a name="microsofttimeseriesinsights"></a>Micro soft. TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/accesspolicies/verwijderen | Hiermee verwijdert u het toegangs beleid. |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/accesspolicies/lezen | De eigenschappen van een toegangs beleid ophalen. |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/accesspolicies/schrijven | Hiermee maakt u een nieuw toegangs beleid voor een omgeving of werkt u een bestaand toegangs beleid bij. |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/verwijderen | Hiermee verwijdert u de omgeving. |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/eventsources/verwijderen | Hiermee verwijdert u de bron van de gebeurtenis. |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/eventsources/lezen | De eigenschappen van een gebeurtenis bron ophalen. |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/eventsources/schrijven | Hiermee maakt u een nieuwe gebeurtenis bron voor een omgeving of werkt u een bestaande gebeurtenis bron bij. |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/lezen | De eigenschappen van een omgeving ophalen. |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/referencedatasets/verwijderen | Hiermee verwijdert u de set met referentie gegevens. |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/referencedatasets/lezen | De eigenschappen van een referentie gegevensset ophalen. |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/referencedatasets/schrijven | Hiermee maakt u een nieuwe referentie gegevensverzameling voor een omgeving of werkt u een bestaande referentie gegevensset bij. |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/status/lezen | Haal de status van de omgeving op, evenals de status van de bijbehorende bewerkingen als ingangs activiteiten. |
> | Bewerking | Micro soft. TimeSeriesInsights/omgevingen/schrijven | Hiermee maakt u een nieuwe omgeving of werkt u een bestaande omgeving bij. |
> | Bewerking | Micro soft. TimeSeriesInsights/REGI ster/actie | Hiermee wordt het abonnement voor de Time Series Insights resource provider geregistreerd en wordt het maken van Time Series Insights-omgevingen mogelijk gemaakt. |

## <a name="microsoftvisualstudio"></a>Micro soft. Visual Studio

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. Visual Studio/account/verwijderen | Account verwijderen |
> | Bewerking | Micro soft. Visual Studio/account/extension/Read | Account/extensie lezen |
> | Bewerking | Micro soft. Visual Studio/account/project/lezen | Account/project lezen |
> | Bewerking | Micro soft. Visual Studio/account/project/schrijven | Account/project instellen |
> | Bewerking | Micro soft. Visual Studio/account/lezen | Account lezen |
> | Bewerking | Micro soft. Visual Studio/account/schrijven | Account instellen |
> | Bewerking | Micro soft. Visual Studio/uitbrei ding/verwijderen | Extensie verwijderen |
> | Bewerking | Micro soft. Visual Studio/extension/Read | Uitbrei ding lezen |
> | Bewerking | Micro soft. Visual Studio/extension/write | Extensie instellen |
> | Bewerking | Micro soft. Visual Studio/project/verwijderen | Project verwijderen |
> | Bewerking | Micro soft. Visual Studio/project/lezen | Project lezen |
> | Bewerking | Micro soft. Visual Studio/project/schrijven | Project instellen |
> | Bewerking | Micro soft. Visual Studio/REGI ster/actie | Azure-abonnement registreren bij micro soft. Visual Studio-provider |

## <a name="microsoftweb"></a>micro soft. Web

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | micro soft. Web/apimanagementaccounts/apiacls/lezen | Apiacls van API management-accounts ophalen. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/apiacls/verwijderen | Api's voor API management-accounts verwijderen Apiacls. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/apiacls/lezen | Api's voor API management-accounts ophalen Apiacls. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/apiacls/schrijven | Api's voor API management-accounts bijwerken Apiacls. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/connectionacls/lezen | Api's voor API management-accounts ophalen Connectionacls. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/Connections/confirmconsentcode/Action | Bevestig de toestemming code API management accounts Api's Connections. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/Connections/connectionacls/verwijderen | API-beheer accounts verwijderen Api's-verbindingen Connectionacls. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/Connections/connectionacls/lezen | Get API management accounts Api's Connections Connectionacls. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/Connections/connectionacls/write | Update API management accounts Api's Connections Connectionacls. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/verbindingen/verwijderen | Api's-verbindingen van API management-accounts verwijderen. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/Connections/getconsentlinks/Action | Vraag koppelingen naar toestemming voor API management-accounts Api's-verbindingen op. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/Connections/listconnectionkeys/Action | Lijst met verbindings sleutels API-beheer accounts Api's-verbindingen. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/Connections/listsecrets/Action | Lijst met geheimen API management accounts Api's-verbindingen. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/verbindingen/lezen | Api's-verbindingen van API management-accounts ophalen. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/verbindingen/schrijven | Api's-verbindingen van API management-accounts bijwerken. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/verwijderen | Api's voor API management-accounts verwijderen. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/localizeddefinitions/verwijderen | API management-accounts verwijderen Api's gelokaliseerde definities. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/localizeddefinitions/lezen | API management accounts Api's gelokaliseerde definities ophalen. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/localizeddefinitions/schrijven | Update API management accounts Api's gelokaliseerde definities. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/lezen | Api's voor API management-accounts ophalen. |
> | Bewerking | micro soft. Web/apimanagementaccounts/api's/schrijven | Api's voor API management-accounts bijwerken. |
> | Bewerking | micro soft. Web/apimanagementaccounts/connectionacls/lezen | Connectionacls van API management-accounts ophalen. |
> | Bewerking | micro soft. Web/availablestacks/lezen | Beschik bare stacks ophalen. |
> | Bewerking | Micro soft. web/certificaten/verwijderen | Een bestaand certificaat verwijderen. |
> | Bewerking | Micro soft. web/certificaten/lezen | De lijst met certificaten ophalen. |
> | Bewerking | Micro soft. web/certificaten/schrijven | Een nieuw certificaat toevoegen of een bestaande bijwerken. |
> | Bewerking | micro soft. Web/checknameavailability/lezen | Controleer of de resource naam beschikbaar is. |
> | Bewerking | micro soft. Web/classicmobileservices/lezen | Klassieke Mobile Services ophalen. |
> | Bewerking | Micro soft. Web/connectionGateways/verwijderen | Hiermee verwijdert u een verbindings gateway. |
> | Bewerking | Micro soft. Web/connectionGateways/samen voegen/actie | Er wordt lid van een verbindings gateway. |
> | Bewerking | Micro soft. Web/connectionGateways/ListStatus/actie | Hiermee wordt de status van een verbindings gateway weer gegeven. |
> | Bewerking | Micro soft. Web/connectionGateways/verplaatsen/actie | Hiermee verplaatst u een verbindings gateway. |
> | Bewerking | Micro soft. Web/connectionGateways/lezen | De lijst met verbindings gateways ophalen. |
> | Bewerking | Micro soft. Web/connectionGateways/schrijven | Hiermee wordt een verbindings gateway gemaakt of bijgewerkt. |
> | Bewerking | micro soft. Web/verbindingen/confirmconsentcode/actie | Bevestig de code voor de toestemming van de verbinding. |
> | Bewerking | Micro soft. Web/verbindingen/verwijderen | Hiermee verwijdert u een verbinding. |
> | Bewerking | Micro soft. Web/verbindingen/samen voegen/actie | Er wordt een verbinding toegevoegd. |
> | Bewerking | micro soft. Web/verbindingen/listconsentlinks/actie | Lijst met toestemmings koppelingen voor verbindingen. |
> | Bewerking | Micro soft. Web/verbindingen/verplaatsen/actie | Hiermee verplaatst u een verbinding. |
> | Bewerking | Micro soft. Web/verbindingen/lezen | De lijst met verbindingen ophalen. |
> | Bewerking | Micro soft. Web/verbindingen/schrijven | Hiermee wordt een verbinding gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Web/customApis/verwijderen | Hiermee verwijdert u een aangepaste API. |
> | Bewerking | Micro soft. Web/customApis/extractApiDefinitionFromWsdl/actie | Hiermee wordt de API-definitie geëxtraheerd uit een WSDL. |
> | Bewerking | Micro soft. Web/customApis/samen voegen/actie | Er wordt verbinding gemaakt met een aangepaste API. |
> | Bewerking | Micro soft. Web/customApis/listWsdlInterfaces/actie | Geeft een lijst van de WSDL-interfaces voor een aangepaste API. |
> | Bewerking | Micro soft. Web/customApis/verplaatsen/actie | Hiermee verplaatst u een aangepaste API. |
> | Bewerking | Micro soft. Web/customApis/lezen | De lijst met aangepaste API ophalen. |
> | Bewerking | Micro soft. Web/customApis/schrijven | Hiermee wordt een aangepaste API gemaakt of bijgewerkt. |
> | Bewerking | Micro soft. Web/deletedSites/lezen | De eigenschappen van een verwijderde web-app ophalen |
> | Bewerking | micro soft. Web/deploymentlocations/lezen | Implementatie locaties ophalen. |
> | Bewerking | Micro soft. Web/georegio's/lezen | De lijst met geo-regio's ophalen. |
> | Bewerking | micro soft. Web/hostingenvironments/capaciteiten/lezen | De capaciteit van hosting omgevingen ophalen. |
> | Bewerking | Micro soft. Web/hostingEnvironments/verwijderen | Een App Service Environment verwijderen |
> | Bewerking | micro soft. Web/hostingenvironments/detectors/lezen | Detecties voor hosting omgevingen ophalen. |
> | Bewerking | micro soft. Web/hostingenvironments/diagnostiek/lezen | Diagnostische gegevens over hosting omgevingen ophalen. |
> | Bewerking | micro soft. Web/hostingenvironments/inboundnetworkdependenciesendpoints/lezen | De netwerk eindpunten van alle inkomende afhankelijkheden ophalen. |
> | Bewerking | Micro soft. Web/hostingEnvironments/samen voegen/actie | Voegt een App Service Environment |
> | Bewerking | micro soft. Web/hostingenvironments/metricdefinitions/lezen | Metrische definities voor hosting omgevingen ophalen. |
> | Bewerking | micro soft. Web/hostingenvironments/multirolepools/metricdefinitions/lezen | Host-omgevingen met Multirole-groepen ophalen metrische definities. |
> | Bewerking | micro soft. Web/hostingenvironments/multirolepools/metrisch/lezen | De metrische gegevens van de Pools voor hosting omgevingen ophalen. |
> | Bewerking | Micro soft. Web/hostingEnvironments/multiRolePools/lezen | De eigenschappen van een front-end groep ophalen in een App Service Environment |
> | Bewerking | micro soft. Web/hostingenvironments/multirolepools/sku's/lezen | Haal de Sku's van de Multirole-Pools voor hosting omgevingen op. |
> | Bewerking | micro soft. Web/hostingenvironments/multirolepools/gebruik/lezen | Haal hosting omgevingen op met het gebruik van Multirole-Pools. |
> | Bewerking | Micro soft. Web/hostingEnvironments/multiRolePools/schrijven | Een nieuwe front-endwebserver maken in een App Service Environment of een bestaande groep bijwerken |
> | Bewerking | micro soft. Web/hostingenvironments/Operations/lezen | Bewerkingen voor hosting omgevingen ophalen. |
> | Bewerking | micro soft. Web/hostingenvironments/outboundnetworkdependenciesendpoints/lezen | De netwerk eindpunten van alle uitgaande afhankelijkheden ophalen. |
> | Bewerking | Micro soft. Web/hostingEnvironments/PrivateEndpointConnectionsApproval/actie | Privé-eindpunt verbindingen goed keuren |
> | Bewerking | Micro soft. Web/hostingEnvironments/lezen | De eigenschappen van een App Service Environment ophalen |
> | Bewerking | Micro soft. Web/hostingEnvironments/opnieuw opstarten/actie | Alle machines in een App Service Environment opnieuw opstarten |
> | Bewerking | micro soft. Web/hostingenvironments/CV/actie | Hosting omgevingen hervatten. |
> | Bewerking | micro soft. Web/hostingenvironments/server farms/lezen | Haal hosting omgevingen op App Service plannen. |
> | Bewerking | micro soft. Web/hostingenvironments/sites/lezen | Haal Web Apps hosting omgevingen op. |
> | Bewerking | micro soft. Web/hostingenvironments/Pause/actie | Hosting omgevingen blok keren. |
> | Bewerking | micro soft. Web/hostingenvironments/gebruik/lezen | Het gebruik van hosting omgevingen ophalen. |
> | Bewerking | micro soft. Web/hostingenvironments/workerpools/metricdefinitions/lezen | Workerpools metrische definities voor hosting omgevingen ophalen. |
> | Bewerking | micro soft. Web/hostingenvironments/workerpools/metrisch/lezen | Haal Workerpools-metrische gegevens voor hosting omgevingen op. |
> | Bewerking | Micro soft. Web/hostingEnvironments/workerPools/lezen | De eigenschappen van een werk groep in een App Service Environment ophalen |
> | Bewerking | micro soft. Web/hostingenvironments/workerpools/sku's/lezen | Haal hosting omgevingen Workerpools Sku's op. |
> | Bewerking | micro soft. Web/hostingenvironments/workerpools/gebruik/lezen | Haal Workerpools-gebruik op in hosting omgevingen. |
> | Bewerking | Micro soft. Web/hostingEnvironments/workerPools/schrijven | Een nieuwe werk groep maken in een App Service Environment of een bestaande pool bijwerken |
> | Bewerking | Micro soft. Web/hostingEnvironments/schrijven | Een nieuwe App Service Environment maken of bestaande bijwerken |
> | Bewerking | micro soft. Web/ishostingenvironmentnameavailable/lezen | Ophalen als de hosting omgevings naam beschikbaar is. |
> | Bewerking | micro soft. Web/ishostnameavailable/lezen | Controleer of de hostnaam beschikbaar is. |
> | Bewerking | micro soft. Web/isusernameavailable/lezen | Controleer of de gebruikers naam beschikbaar is. |
> | Bewerking | Micro soft. Web/listSitesAssignedToHostName/lezen | Namen ophalen van sites die zijn toegewezen aan de hostnaam. |
> | Bewerking | micro soft. Web/locaties/apioperations/lezen | Get locations API Operations. |
> | Bewerking | micro soft. Web/locaties/connectiongatewayinstallations/lezen | Get locations Connection gateway Installations. |
> | Bewerking | micro soft. Web/locaties/deleteVirtualNetworkOrSubnets/actie | Melding over het verwijderen van Vnet of subnet voor locaties. |
> | Bewerking | micro soft. Web/locaties/extractapidefinitionfromwsdl/actie | De API-definitie van WSDL voor locaties ophalen. |
> | Bewerking | micro soft. Web/locaties/listwsdlinterfaces/actie | Lijst van WSDL-interfaces voor locaties. |
> | Bewerking | micro soft. Web/locaties/beheerdeapi's/apioperations/lezen | Ophalen van locaties Managed API-bewerkingen. |
> | Bewerking | Micro soft. Web/locaties/beheerdeapi's/samen voegen/actie | Voegt een beheerde API samen. |
> | Bewerking | micro soft. Web/locaties/beheerdeapi's/lezen | Beheerde Api's voor locaties ophalen. |
> | Bewerking | micro soft. Web/locaties/operationResults/lezen | Get-bewerkingen. |
> | Bewerking | micro soft. Web/locaties/bewerkingen/lezen | Get-bewerkingen. |
> | Bewerking | micro soft. Web/Operations/lezen | Get-bewerkingen. |
> | Bewerking | micro soft. Web/publishingusers/lezen | Publiceer gebruikers. |
> | Bewerking | micro soft. Web/publishingusers/schrijven | Publicatie gebruikers bijwerken. |
> | Bewerking | Micro soft. Web/aanbevelingen/lezen | De lijst met aanbevelingen voor abonnementen ophalen. |
> | Bewerking | micro soft. Web/registreren/actie | Registreer de resource provider van micro soft. web voor het abonnement. |
> | Bewerking | micro soft. Web/resourcehealthmetadata/lezen | Resource Health meta gegevens ophalen. |
> | Bewerking | micro soft. web/server farms/capabilities/lezen | Profiteer van de mogelijkheden van App Service-abonnementen. |
> | Bewerking | Micro soft. web/server farms/verwijderen | Een bestaand App Service plan verwijderen |
> | Bewerking | Micro soft. web/server farms/eventGridFilters/verwijderen | Verwijder Event Grid filter op de server farm. |
> | Bewerking | Micro soft. web/server farms/eventGridFilters/lezen | Event Grid filter op de server farm ophalen. |
> | Bewerking | Micro soft. web/server farms/eventGridFilters/schrijven | Plaats Event Grid filter op de server farm. |
> | Bewerking | micro soft. web/server farms/firstpartyapps/instellingen/verwijderen | De instellingen van de app voor de eerste partij verwijderen App Service plannen. |
> | Bewerking | micro soft. web/server farms/firstpartyapps/Settings/lezen | Ontvang App Service-abonnementen voor de eerste partij-apps. |
> | Bewerking | micro soft. web/server farms/firstpartyapps/Settings/write | De instellingen van de app voor de eerste partij bijwerken App Service plannen. |
> | Bewerking | micro soft. web/server farms/hybridconnectionnamespaces/relays/verwijderen | App Service-abonnementen van hybride verbindings ruimten voor het verwijderen worden door gegeven. |
> | Bewerking | micro soft. web/server farms/hybridconnectionnamespaces/relays/lezen | Get App Service-abonnementen hybride verbindings naam ruimten relays. |
> | Bewerking | micro soft. web/server farms/hybridconnectionnamespaces/relays/sites/lezen | Get App Service-abonnementen hybride verbindings naam ruimten relays Web Apps. |
> | Bewerking | micro soft. web/server farms/hybridconnectionplanlimits/lezen | Ontvang de limieten voor hybride verbindings plannen voor App Service abonnementen. |
> | Bewerking | micro soft. web/server farms/hybridconnectionrelays/lezen | Ontvang App Service abonnementen hybride verbindings relays. |
> | Bewerking | micro soft. web/server farms/metricdefinitions/lezen | Definities van metrische gegevens voor App Service plannen ophalen. |
> | Bewerking | micro soft. web/server farms/metrisch/lezen | Statistieken over het App Service plan ophalen. |
> | Bewerking | micro soft. web/server farms/operationresults/lezen | Resultaten van de bewerking App Service plannen ophalen. |
> | Bewerking | Micro soft. web/server farms/lezen | De eigenschappen van een App Service plan ophalen |
> | Bewerking | Micro soft. web/server farms/restartSites/actie | Alle Web Apps opnieuw opstarten in een App Service plan |
> | Bewerking | micro soft. web/server farms/sites/lezen | Ontvang App Service plannen Web Apps. |
> | Bewerking | micro soft. web/server farms/sku's/lezen | Down load de Sku's van App Service-abonnementen. |
> | Bewerking | micro soft. web/server farms/gebruik/lezen | Maak gebruik van App Service plannen. |
> | Bewerking | micro soft. web/server farms/virtualnetworkconnections/gateways/schrijven | Werk App Service plannen Virtual Network verbindingen gateways. |
> | Bewerking | micro soft. web/server farms/virtualnetworkconnections/lezen | App Service plannen Virtual Network verbindingen ophalen. |
> | Bewerking | micro soft. web/server farms/virtualnetworkconnections/routes/verwijderen | App Service plannen Virtual Network verbindings routes verwijderen. |
> | Bewerking | micro soft. web/server farms/virtualnetworkconnections/routes/lezen | App Service plannen Virtual Network verbindings routes ophalen. |
> | Bewerking | micro soft. web/server farms/virtualnetworkconnections/routes/schrijven | App Service plannen Virtual Network verbindings routes bijwerken. |
> | Bewerking | micro soft. web/server farms/werk nemers/opnieuw opstarten/actie | Start App Service plannen werk nemers. |
> | Bewerking | Micro soft. web/server farms/schrijven | Een nieuw App Service plan maken of een bestaand abonnement bijwerken |
> | Bewerking | micro soft. web/sites/analyzecustomhostname/lezen | Aangepaste hostnaam analyseren. |
> | Bewerking | Micro soft. web/sites/applySlotConfig/actie | De sleuf configuratie van de web-app Toep assen vanaf de doel site tot de huidige web-app |
> | Bewerking | Micro soft. web/sites/back-up/actie | Een nieuwe web-app-back-up maken |
> | Bewerking | micro soft. web/sites/back-up/lezen | Web Apps back-up ophalen. |
> | Bewerking | micro soft. web/sites/back-up/schrijven | Web Apps back-up bijwerken. |
> | Bewerking | micro soft. web/sites/back-ups/actie | Hiermee wordt een bestaande app-back-up gedetecteerd die kan worden hersteld vanuit een BLOB in azure Storage. |
> | Bewerking | micro soft. web/sites/back-ups/verwijderen | Verwijder Web Apps back-ups. |
> | Bewerking | micro soft. web/sites/back-ups/lijst/actie | Lijst Web Apps back-ups. |
> | Bewerking | Micro soft. web/sites/back-ups/lezen | De eigenschappen van de back-up van een web-app ophalen |
> | Bewerking | micro soft. web/sites/back-ups/herstellen/actie | Herstel Web Apps back-ups. |
> | Bewerking | micro soft. web/sites/back-ups/schrijven | Web Apps back-ups bijwerken. |
> | Bewerking | micro soft. web/sites/config/verwijderen | Web Apps configuratie verwijderen. |
> | Bewerking | Micro soft. web/sites/config/lijst/actie | De gevoelige beveiligings instellingen van de web-app weer geven, zoals publicatie referenties, app-instellingen en verbindings reeksen |
> | Bewerking | Micro soft. web/sites/config/lezen | Configuratie-instellingen voor web-apps ophalen |
> | Bewerking | micro soft. web/sites/config/moment opnamen/lezen | Web Apps-configuratie momentopnamen ophalen. |
> | Bewerking | Micro soft. web/sites/config/schrijven | De configuratie-instellingen van de web-app bijwerken |
> | Bewerking | micro soft. web/sites/containerlogs/actie | Container-logboeken ophalen voor web-app. |
> | Bewerking | micro soft. web/sites/containerlogs/downloaden/actie | Down load Web Apps container Logboeken. |
> | Bewerking | micro soft. web/sites/continuouswebjobs/verwijderen | Verwijder Web Apps doorlopende webtaken. |
> | Bewerking | micro soft. web/sites/continuouswebjobs/lezen | Ontvang Web Apps doorlopende webtaken. |
> | Bewerking | micro soft. web/sites/continuouswebjobs/start/actie | Start Web Apps doorlopende webtaken. |
> | Bewerking | micro soft. web/sites/continuouswebjobs/stoppen/actie | Stop Web Apps doorlopende webtaken. |
> | Bewerking | Micro soft. web/sites/verwijderen | Een bestaande web-app verwijderen |
> | Bewerking | micro soft. web/sites/implementaties/verwijderen | Web Apps-implementaties verwijderen. |
> | Bewerking | micro soft. web/sites/implementaties/logboek/lezen | Web Apps-implementaties logboek ophalen. |
> | Bewerking | micro soft. web/sites/implementaties/lezen | Web Apps-implementaties ophalen. |
> | Bewerking | micro soft. web/sites/implementaties/schrijven | Web Apps-implementaties bijwerken. |
> | Bewerking | micro soft. web/sites/detectoren/lezen | Web Apps detectors ophalen. |
> | Bewerking | micro soft. web/sites/diagnostische gegevens/analyses/uitvoeren/actie | Voer Web Apps diagnostische gegevens over de analyse uit. |
> | Bewerking | micro soft. web/sites/diagnostiek/analyses/lezen | Down load de analyse van Web Apps diagnose. |
> | Bewerking | micro soft. web/sites/diagnostiek/aspnetcore/lezen | Web Apps diagnostische gegevens voor ASP.NET Core-app ophalen. |
> | Bewerking | micro soft. web/sites/diagnostiek/automatisch herstellen/lezen | Ontvang Web Apps diagnose automatisch herstellen. |
> | Bewerking | micro soft. web/sites/diagnostische gegevens/implementatie/lezen | Down load de implementatie van Web Apps diagnose. |
> | Bewerking | micro soft. web/sites/diagnostische gegevens/implementaties/lezen | Web Apps diagnostische implementaties ophalen. |
> | Bewerking | micro soft. web/sites/diagnostische gegevens/detectoren/uitvoeren/actie | Voer Web Apps diagnostische gegevens detectie uit. |
> | Bewerking | micro soft. web/sites/diagnostische gegevens/detectoren/lezen | Down load de detectie van Web Apps diagnose. |
> | Bewerking | micro soft. web/sites/diagnostiek/failedrequestsperuri/lezen | Ophalen van mislukte aanvragen voor Web Apps diagnostische gegevens per URI. |
> | Bewerking | micro soft. web/sites/diagnostiek/frebanalysis/lezen | Web Apps diagnostische FREB-analyse ophalen. |
> | Bewerking | micro soft. web/sites/diagnostiek/LogAnalyzer/lezen | Lees de logboek analyse van Web Apps Diagnostics. |
> | Bewerking | micro soft. web/sites/diagnostiek/lezen | Web Apps diagnostische categorieën ophalen. |
> | Bewerking | micro soft. web/sites/diagnostiek/runtimeavailability/lezen | Down load de beschik baarheid van Web Apps diagnostische gegevens. |
> | Bewerking | micro soft. web/sites/diagnostiek/servicehealth/lezen | Web Apps diagnostische Service Health ophalen. |
> | Bewerking | micro soft. web/sites/diagnostiek/sitecpuanalysis/lezen | Web Apps diagnostische gegevens van de site CPU ophalen. |
> | Bewerking | micro soft. web/sites/diagnostiek/sitecrashes/lezen | Ontvang Web Apps diagnostische gegevens van de site. |
> | Bewerking | micro soft. web/sites/diagnostiek/sitelatency/lezen | Site latentie van Web Apps diagnostische gegevens ophalen. |
> | Bewerking | micro soft. web/sites/diagnostiek/sitememoryanalysis/lezen | Down load de analyse van de site geheugen van Web Apps diagnose. |
> | Bewerking | micro soft. web/sites/diagnostiek/siterestartsettingupdate/lezen | Web Apps diagnostische gegevens van de site ophalen update opnieuw instellen. |
> | Bewerking | micro soft. web/sites/diagnostiek/siterestartuserinitiated/lezen | Web Apps diagnostische gegevens van de site voor het opnieuw starten van de gebruiker ophalen. |
> | Bewerking | micro soft. web/sites/diagnostiek/siteswap/lezen | Ontvang Web Apps diagnostische gegevens van site swap. |
> | Bewerking | micro soft. web/sites/diagnostiek/threadcount/lezen | Web Apps diagnostische-thread aantal ophalen. |
> | Bewerking | micro soft. web/sites/diagnostiek/workeravailability/lezen | Web Apps diagnostische Workeravailability-gegevens ophalen. |
> | Bewerking | micro soft. web/sites/diagnostiek/workerprocessrecycle/lezen | Web Apps diagnostische gegevens van werk processen ophalen. |
> | Bewerking | micro soft. web/sites/domainownershipidentifiers/lezen | Web Apps eigendoms-Id's van het domein ophalen. |
> | Bewerking | micro soft. web/sites/domainownershipidentifiers/schrijven | De eigendoms-Id's van Web Apps domein bijwerken. |
> | Bewerking | Micro soft. web/sites/eventGridFilters/verwijderen | Event Grid filter verwijderen voor web-app. |
> | Bewerking | Micro soft. web/sites/eventGridFilters/lezen | Event Grid filter voor web-app ophalen. |
> | Bewerking | Micro soft. web/sites/eventGridFilters/schrijven | Event Grid filteren op Web-app. |
> | Bewerking | micro soft. web/sites/uitbrei dingen/verwijderen | Verwijder Web Apps site-extensies. |
> | Bewerking | micro soft. web/sites/uitbrei dingen/lezen | Web Apps site-extensies ophalen. |
> | Bewerking | micro soft. web/sites/extensies/schrijven | Web Apps site-extensies bijwerken. |
> | Bewerking | micro soft. web/sites/functies/actie | Functions Web Apps. |
> | Bewerking | micro soft. web/sites/functies/verwijderen | Web Apps functies verwijderen. |
> | Bewerking | micro soft. web/sites/functies/sleutels/verwijderen | Functie sleutels verwijderen. |
> | Bewerking | micro soft. web/sites/functies/sleutels/schrijven | Functie sleutels bijwerken. |
> | Bewerking | micro soft. web/sites/functies/listkeys ophalen/actie | Functie sleutels weer geven. |
> | Bewerking | micro soft. web/sites/functies/listsecrets/actie | Lijst met functie geheimen. |
> | Bewerking | micro soft. web/sites/functies/hoofd sleutel/lezen | Web Apps functies hoofd sleutel ophalen. |
> | Bewerking | micro soft. web/sites/functies/eigenschappen/lezen | Eigenschappen van Web Apps functies lezen. |
> | Bewerking | micro soft. web/sites/functies/eigenschappen/schrijven | Eigenschappen van Web Apps functies bijwerken. |
> | Bewerking | micro soft. web/sites/functies/lezen | Web Apps-functies ophalen. |
> | Bewerking | micro soft. web/sites/functies/-token/lezen | Web Apps functions-Token ophalen. |
> | Bewerking | micro soft. web/sites/functies/schrijven | Web Apps-functies bijwerken. |
> | Bewerking | micro soft. web/sites/host/functionkeys/verwijderen | Functie sleutels host-functies verwijderen. |
> | Bewerking | micro soft. web/sites/host/functionkeys/schrijven | Functie sleutels van host-functies bijwerken. |
> | Bewerking | micro soft. web/sites/host/listkeys ophalen/actie | Host-sleutels van functies weer geven. |
> | Bewerking | micro soft. web/sites/host/listsyncstatus/actie | Status van triggers voor synchronisatie functie voor lijst. |
> | Bewerking | micro soft. web/sites/host/eigenschappen/lezen | Eigenschappen van de host van Web Apps functies lezen. |
> | Bewerking | micro soft. web/sites/host/synchroniseren/actie | Synchronisatie functie Triggers. |
> | Bewerking | micro soft. web/sites/host/systemkeys/verwijderen | Functies verwijderen host systeem sleutels. |
> | Bewerking | micro soft. web/sites/host/systemkeys/schrijven | Update functies host systeem sleutels. |
> | Bewerking | micro soft. web/sites/hostnamebindings/verwijderen | Web Apps hostnaam-bindingen verwijderen. |
> | Bewerking | micro soft. web/sites/hostnamebindings/lezen | Web Apps hostnaam-bindingen ophalen. |
> | Bewerking | micro soft. web/sites/hostnamebindings/schrijven | Web Apps hostnaam-bindingen bijwerken. |
> | Bewerking | micro soft. web/sites/hostruntime/functies/sleutels/lezen | Web Apps sleutels voor Hostruntime-functies ophalen. |
> | Bewerking | Micro soft. web/sites/hostruntime/host/_master/Read | De hoofd sleutel van functie-app voor beheer bewerkingen ophalen |
> | Bewerking | Micro soft. web/sites/hostruntime/host/actie | Voer functie-app runtime-actie uit zoals synchronisatie triggers, functies toevoegen, functies aanroepen, functies verwijderen, enzovoort. |
> | Bewerking | micro soft. web/sites/hostruntime/host/lezen | Web Apps Hostruntime-host ophalen. |
> | Bewerking | micro soft. web/sites/hybridconnection/verwijderen | Web Apps hybride verbinding verwijderen. |
> | Bewerking | micro soft. web/sites/hybridconnection/lezen | Web Apps hybride verbinding ophalen. |
> | Bewerking | micro soft. web/sites/hybridconnection/schrijven | Web Apps hybride verbinding bijwerken. |
> | Bewerking | micro soft. web/sites/hybridconnectionnamespaces/relays/verwijderen | Web Apps relays-naam ruimten voor hybride verbindingen verwijderen. |
> | Bewerking | micro soft. web/sites/hybridconnectionnamespaces/relays/listkeys ophalen/actie | Lijst met sleutels Web Apps relays-naam ruimten voor hybride verbindingen. |
> | Bewerking | micro soft. web/sites/hybridconnectionnamespaces/relays/lezen | Down loads van Web Apps Hybrid Connection-naam ruimten. |
> | Bewerking | micro soft. web/sites/hybridconnectionnamespaces/relays/schrijven | Web Apps relays-naam ruimten voor hybride verbindingen bijwerken. |
> | Bewerking | micro soft. web/sites/hybridconnectionrelays/lezen | Ontvang Web Apps hybride verbindings relays. |
> | Bewerking | micro soft. web/sites/exemplaren/implementaties/verwijderen | Implementaties van Web Apps instanties verwijderen. |
> | Bewerking | micro soft. web/sites/exemplaren/implementaties/lezen | Implementaties van Web Apps-instanties ophalen. |
> | Bewerking | micro soft. web/sites/instanties/extensies/logboek/lezen | Het logboek voor extensies van Web Apps-exemplaren ophalen. |
> | Bewerking | micro soft. web/sites/instanties/uitbrei dingen/processen/lezen | Web Apps-exemplaren van extensies-processen ophalen. |
> | Bewerking | micro soft. web/sites/instanties/extensies/lezen | Uitbrei dingen van Web Apps-instanties ophalen. |
> | Bewerking | micro soft. web/sites/instanties/processen/verwijderen | Verwijder de processen van Web Apps exemplaren. |
> | Bewerking | micro soft. web/sites/exemplaren/processen/modules/lezen | Get Web Apps instances-modules. |
> | Bewerking | micro soft. web/sites/instanties/processen/lezen | Get Web Apps instances-processen. |
> | Bewerking | micro soft. web/sites/exemplaren/processen/threads/lezen | Web Apps-exemplaren ophalen threads verwerken. |
> | Bewerking | micro soft. web/sites/exemplaren/lezen | Web Apps-instanties ophalen. |
> | Bewerking | micro soft. web/sites/listsyncfunctiontriggerstatus/actie | Trigger status van synchronisatie functie voor lijst. |
> | Bewerking | micro soft. web/sites/metricdefinitions/lezen | Web Apps metrische definities ophalen. |
> | Bewerking | micro soft. web/sites/metrische gegevens/lezen | Web Apps metrische gegevens ophalen. |
> | Bewerking | micro soft. web/sites/metricsdefinitions/lezen | Web Apps metrische definities ophalen. |
> | Bewerking | micro soft. web/sites/migratemysql/actie | MySql-Web Apps migreren. |
> | Bewerking | micro soft. web/sites/migratemysql/lezen | Ga Web Apps om MySql te migreren. |
> | Bewerking | micro soft. web/sites/networktrace/actie | Web Apps netwerk tracering. |
> | Bewerking | micro soft. web/sites/networktraces/operationresults/lezen | Resultaten van Web Apps netwerk tracerings bewerking ophalen. |
> | Bewerking | micro soft. web/sites/NieuwWachtwoord/actie | NieuwWachtwoord Web Apps. |
> | Bewerking | micro soft. web/sites/operationresults/lezen | Resultaten van Web Apps-bewerking ophalen. |
> | Bewerking | micro soft. web/sites/bewerkingen/lezen | Ontvang Web Apps bewerkingen. |
> | Bewerking | micro soft. web/sites/perfcounters/lezen | Prestatie meter items voor Web Apps ophalen. |
> | Bewerking | micro soft. web/sites/premieraddons/verwijderen | Verwijder Web Apps Premier-invoeg toepassingen. |
> | Bewerking | micro soft. web/sites/premieraddons/lezen | Ontvang Web Apps Premier-invoeg toepassingen. |
> | Bewerking | micro soft. web/sites/premieraddons/schrijven | Werk Web Apps Premier-invoeg toepassingen bij. |
> | Bewerking | micro soft. web/sites/privateaccess/lezen | Ontvang gegevens over toegang tot de persoonlijke locatie en geautoriseerde virtuele netwerken die toegang hebben tot de site. |
> | Bewerking | Micro soft. web/sites/PrivateEndpointConnectionsApproval/actie | Privé-eindpunt verbindingen goed keuren |
> | Bewerking | micro soft. web/sites/processen/modules/lezen | Web Apps-proces modules ophalen. |
> | Bewerking | micro soft. web/sites/processen/lezen | Web Apps-processen ophalen. |
> | Bewerking | micro soft. web/sites/processen/threads/lezen | Ontvang Web Apps process-threads. |
> | Bewerking | micro soft. web/sites/publiccertificates/verwijderen | Web Apps open bare certificaten verwijderen. |
> | Bewerking | micro soft. web/sites/publiccertificates/lezen | Ontvang Web Apps open bare certificaten. |
> | Bewerking | micro soft. web/sites/publiccertificates/schrijven | Web Apps open bare certificaten bijwerken. |
> | Bewerking | Micro soft. web/sites/publiceren/actie | Een web-app publiceren |
> | Bewerking | Micro soft. web/sites/publishxml/actie | XML voor publicatie profielen ophalen voor een web-app |
> | Bewerking | micro soft. web/sites/publishxml/lezen | Web Apps Publishing XML ophalen. |
> | Bewerking | Micro soft. web/sites/lezen | De eigenschappen van een web-app ophalen |
> | Bewerking | micro soft. web/sites/recommendationhistory/lezen | Geschiedenis van Web Apps aanbeveling ophalen. |
> | Bewerking | micro soft. web/sites/aanbevelingen/uitschakelen/actie | Schakel Web Apps aanbevelingen uit. |
> | Bewerking | Micro soft. web/sites/aanbevelingen/lezen | De lijst met aanbevelingen voor web-app ophalen. |
> | Bewerking | micro soft. web/sites/herstellen/actie | Web Apps herstellen. |
> | Bewerking | Micro soft. web/sites/resetSlotConfig/actie | De configuratie van de web-app opnieuw instellen |
> | Bewerking | micro soft. web/sites/resourcehealthmetadata/lezen | Meta gegevens van Web Apps Resource Health ophalen. |
> | Bewerking | Micro soft. web/sites/opnieuw opstarten/actie | Een web-app opnieuw starten |
> | Bewerking | micro soft. web/sites/herstellen/lezen | Web Apps herstel ophalen. |
> | Bewerking | micro soft. web/sites/herstellen/schrijven | Web Apps herstellen. |
> | Bewerking | micro soft. web/sites/restorefrombackupblob/actie | De web-app terugzetten vanuit een back-upblob. |
> | Bewerking | micro soft. web/sites/restorefromdeletedapp/actie | Web Apps van verwijderde app herstellen. |
> | Bewerking | micro soft. web/sites/restoresnapshot/actie | Herstel Web Apps moment opnamen. |
> | Bewerking | micro soft. web/sites/siteextensions/verwijderen | Verwijder Web Apps site-extensies. |
> | Bewerking | micro soft. web/sites/siteextensions/lezen | Web Apps site-extensies ophalen. |
> | Bewerking | micro soft. web/sites/siteextensions/schrijven | Web Apps site-extensies bijwerken. |
> | Bewerking | micro soft. web/sites/sleuven/analyzecustomhostname/lezen | Web Apps sleuven ophalen aangepaste hostnaam analyseren. |
> | Bewerking | Micro soft. web/sites/sleuven/applySlotConfig/actie | De sleuf configuratie van de web-app Toep assen op de huidige sleuf van de doel sleuf. |
> | Bewerking | Micro soft. web/sites/sleuven/back-up/actie | Nieuwe web app-sleuf back-up maken. |
> | Bewerking | micro soft. web/sites/sleuven/back-up/lezen | Back-ups van Web Apps sleuven ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/back-up/schrijven | Web Apps-sleuven voor back-ups bijwerken. |
> | Bewerking | micro soft. web/sites/sleuven/back-ups/actie | Detecteer Web Apps sleuven back-ups. |
> | Bewerking | micro soft. web/sites/sleuven/back-ups/verwijderen | Verwijder Web Apps-sleuven back-ups. |
> | Bewerking | micro soft. web/sites/sleuven/back-ups/lijst/actie | Lijst met Web Apps sleuven back-ups. |
> | Bewerking | Micro soft. web/sites/sleuven/back-ups/lezen | De eigenschappen van de back-up van een web-app-sleuf ophalen |
> | Bewerking | micro soft. web/sites/sleuven/back-ups/herstellen/actie | Back-ups van Web Apps sleuven herstellen. |
> | Bewerking | micro soft. web/sites/sleuven/configuratie/verwijderen | Web Apps sleuf configuratie verwijderen. |
> | Bewerking | Micro soft. web/sites/sleuven/configuratie/lijst/actie | Lijst met gevoelige instellingen voor beveiliging van de web-app-sleuf, zoals publicatie referenties, app-instellingen en verbindings reeksen |
> | Bewerking | Micro soft. web/sites/sleuven/config/lezen | Configuratie-instellingen van de web-app-sleuf ophalen |
> | Bewerking | Micro soft. web/sites/sleuven/config/schrijven | Configuratie-instellingen van de web-app-sleuf bijwerken |
> | Bewerking | micro soft. web/sites/sleuven/containerlogs/actie | Bewaar container logboeken voor de web-app-sleuf. |
> | Bewerking | micro soft. web/sites/sleuven/containerlogs/downloaden/actie | Down load Web Apps-sleuf container Logboeken. |
> | Bewerking | micro soft. web/sites/sleuven/continuouswebjobs/verwijderen | Verwijder Web Apps-sleuven doorlopende webtaken. |
> | Bewerking | micro soft. web/sites/sleuven/continuouswebjobs/lezen | Ontvang Web Apps sleuven doorlopende webtaken. |
> | Bewerking | micro soft. web/sites/sleuven/continuouswebjobs/start/actie | Start Web Apps-sleuven doorlopende webtaken. |
> | Bewerking | micro soft. web/sites/sleuven/continuouswebjobs/stoppen/actie | Stop Web Apps-sleuven van doorlopende webtaken. |
> | Bewerking | Micro soft. web/sites/sleuven/verwijderen | Een bestaande sleuf voor een web-app verwijderen |
> | Bewerking | micro soft. web/sites/sleuven/implementaties/verwijderen | Web Apps sleuf-implementaties verwijderen. |
> | Bewerking | micro soft. web/sites/sleuven/implementaties/logboek/lezen | Haal het logboek voor de implementatie van Web Apps sleuven op. |
> | Bewerking | micro soft. web/sites/sleuven/implementaties/lezen | Haal implementaties van Web Apps sleuven op. |
> | Bewerking | micro soft. web/sites/sleuven/implementaties/schrijven | Implementaties van Web Apps sleuven bijwerken. |
> | Bewerking | micro soft. web/sites/sleuven/detectoren/lezen | Ontvang Web Apps sleuf detecties. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostische gegevens/analyses/uitvoeren/actie | Voer de diagnostische analyse van Web Apps sleuven uit. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostische gegevens/analyses/lezen | Haal analyse van de diagnostische gegevens van Web Apps-sleuven op. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/aspnetcore/lezen | Down load de diagnostische gegevens van Web Apps sleuven voor ASP.NET Core app. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostische gegevens/automatisch herstellen/lezen | Automatisch herstellen van Web Apps-sleuven automatisch herstellen. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostische gegevens/implementatie/lezen | Down load de diagnostische implementatie van Web Apps sleuven. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostische gegevens/implementaties/lezen | Down loads van Web Apps sleuven diagnostische implementaties. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostische gegevens/detectoren/uitvoeren/actie | Voer de diagnostische detector van Web Apps sleuven uit. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostische gegevens/detectoren/lezen | Down load de detectie van Web Apps sleuven Diagnostics. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/frebanalysis/lezen | Ontvang Web Apps sleuven diagnostische FREB-analyse. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/LogAnalyzer/lezen | Ontvang Web Apps sleuven Diagnostische logboeken Log Analyzer. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostische gegevens/lezen | Ontvang Web Apps sleuf diagnostiek. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/runtimeavailability/lezen | Down load de beschik baarheid van de diagnostische runtime van Web Apps-sleuven. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/servicehealth/lezen | Web Apps-sleuven diagnostische gegevens ophalen Service Health. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/sitecpuanalysis/lezen | Ontvang Web Apps sleuven diagnostische gegevens van de site CPU. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/sitecrashes/lezen | Ontvang Web Apps sleuven diagnostische gegevens van de site. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/sitelatency/lezen | Down load Web Apps-sleuven site latentie diagnose. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/sitememoryanalysis/lezen | Ontvang Web Apps sleuven diagnostische gegevens van het site geheugen. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/siterestartsettingupdate/lezen | Ophalen van de instelling update voor de diagnostische gegevens van Web Apps sites |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/siterestartuserinitiated/lezen | Ophalen van Web Apps-sleuven site Diagnostics-gebruiker gestart. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/siteswap/lezen | Down load Web Apps-sleuven site swap. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/threadcount/lezen | Web Apps-sleuven van het aantal diagnostische gegevens ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/workeravailability/lezen | Ontvang Web Apps sleuven diagnostische gegevens Workeravailability. |
> | Bewerking | micro soft. web/sites/sleuven/diagnostiek/workerprocessrecycle/lezen | Web Apps-sleuven ophalen werk proces wordt herhaald. |
> | Bewerking | micro soft. web/sites/sleuven/domainownershipidentifiers/lezen | Domein eigendoms-Id's van Web Apps-sleuven ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/functies/listsecrets/actie | Lijst met geheimen Web Apps sleuven-functies. |
> | Bewerking | micro soft. web/sites/sleuven/functies/lezen | Web Apps-sleuven-functies ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/hostnamebindings/verwijderen | Verwijder Web Apps sleuven hostnamen bindings. |
> | Bewerking | micro soft. web/sites/sleuven/hostnamebindings/lezen | Haal Web Apps sleuven hostname-bindingen op. |
> | Bewerking | micro soft. web/sites/sleuven/hostnamebindings/schrijven | Web Apps sleuven hostnamen bindingen bijwerken. |
> | Bewerking | micro soft. web/sites/sleuven/hybridconnection/verwijderen | Web Apps-sleuven hybride verbinding verwijderen. |
> | Bewerking | micro soft. web/sites/sleuven/hybridconnection/lezen | Web Apps-sleuven hybride verbinding ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/hybridconnection/schrijven | Web Apps-sleuven hybride verbinding bijwerken. |
> | Bewerking | micro soft. web/sites/sleuven/hybridconnectionnamespaces/Relais/verwijderen | Web Apps-sleuven verwijderen sites voor naam ruimten hybride verbinding. |
> | Bewerking | micro soft. web/sites/sleuven/hybridconnectionnamespaces/relays/schrijven | Web Apps-sleuven voor het bijwerken van de sites met hybride verbindings ruimten door geven. |
> | Bewerking | micro soft. web/sites/sleuven/hybridconnectionrelays/lezen | Haal Web Apps-sleuven hybride verbindings relays door. |
> | Bewerking | micro soft. web/sites/sleuven/exemplaren/implementaties/lezen | Implementaties van Web Apps sleuf-instanties ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/instanties/processen/verwijderen | Verwijder de processen van Web Apps-sleuf. |
> | Bewerking | micro soft. web/sites/sleuven/instanties/processen/lezen | Web Apps-exemplaren van sleuf-processen ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/exemplaren/lezen | Web Apps sleuf-instanties ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/metricdefinitions/lezen | Metrische definities van Web Apps-sleuven ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/metrieken/lezen | Gegevens over Web Apps sleuven ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/migratemysql/lezen | Web Apps sleuven ophalen MySql migreren. |
> | Bewerking | micro soft. web/sites/sleuven/networktrace/actie | Web Apps sleuven voor netwerk tracering. |
> | Bewerking | micro soft. web/sites/sleuven/networktraces/operationresults/lezen | Resultaten van de netwerk tracerings bewerking van Web Apps sleuven ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/NieuwWachtwoord/actie | NieuwWachtwoord Web Apps-sleuven. |
> | Bewerking | micro soft. web/sites/sleuven/operationresults/lezen | Resultaten van de bewerking Web Apps sleuven ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/bewerkingen/lezen | Web Apps sleuf bewerkingen ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/perfcounters/lezen | Prestatie meter items voor Web Apps sleuven ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/phplogging/lezen | Web Apps Phplogging-sleuven ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/premieraddons/verwijderen | Web Apps-sleuven verwijderen premier-invoeg toepassingen. |
> | Bewerking | micro soft. web/sites/sleuven/premieraddons/lezen | Ontvang Web Apps-sleuven premier-invoeg toepassingen. |
> | Bewerking | micro soft. web/sites/sleuven/premieraddons/schrijven | Web Apps-sleuven voor premier-invoeg toepassingen bijwerken. |
> | Bewerking | micro soft. web/sites/sleuven/processen/lezen | Web Apps sleuf processen ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/publiccertificates/verwijderen | Open bare certificaten Web Apps-sleuven verwijderen. |
> | Bewerking | micro soft. web/sites/sleuven/publiccertificates/lezen | Ontvang Web Apps-sleuven open bare certificaten. |
> | Bewerking | micro soft. web/sites/sleuven/publiccertificates/schrijven | Open bare certificaten van Web Apps sleuven maken of bijwerken. |
> | Bewerking | Micro soft. web/sites/sleuven/publiceren/actie | Een web app-sleuf publiceren |
> | Bewerking | Micro soft. web/sites/sleuven/publishxml/actie | Publicatie profiel-XML ophalen voor web app-sleuf |
> | Bewerking | Micro soft. web/sites/sleuven/lezen | De eigenschappen van een web-app-implementatie sleuf ophalen |
> | Bewerking | micro soft. web/sites/sleuven/herstellen/actie | Web Apps sleuven herstellen. |
> | Bewerking | Micro soft. web/sites/sleuven/resetSlotConfig/actie | Configuratie van sleuf voor web-app opnieuw instellen |
> | Bewerking | micro soft. web/sites/sleuven/resourcehealthmetadata/lezen | Web Apps-sleuven ophalen Resource Health meta gegevens. |
> | Bewerking | Micro soft. web/sites/sleuven/opnieuw starten/actie | Een web-app-sleuf opnieuw opstarten |
> | Bewerking | micro soft. web/sites/sleuven/herstellen/lezen | Herstel van Web Apps sleuven ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/herstellen/schrijven | Web Apps-sleuven herstellen. |
> | Bewerking | micro soft. web/sites/sleuven/restorefrombackupblob/actie | Web Apps sleuf herstellen vanaf een back-upblob. |
> | Bewerking | micro soft. web/sites/sleuven/restorefromdeletedapp/actie | Web-app-sleuven van verwijderde app herstellen. |
> | Bewerking | micro soft. web/sites/sleuven/restoresnapshot/actie | Herstel Web Apps sleuf momentopnamen. |
> | Bewerking | micro soft. web/sites/sleuven/siteextensions/verwijderen | Site-extensies Web Apps sleuven verwijderen. |
> | Bewerking | micro soft. web/sites/sleuven/siteextensions/lezen | Site-extensies van Web Apps-sleuven ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/siteextensions/schrijven | Site-extensies Web Apps sleuven bijwerken. |
> | Bewerking | Micro soft. web/sites/sleuven/slotsdiffs/actie | Verschillen in de configuratie van de web-app en-sleuven |
> | Bewerking | Micro soft. web/sites/sleuven/slotsswap/actie | Implementatie sleuven voor web-apps wisselen |
> | Bewerking | micro soft. web/sites/sleuven/moment opnamen/lezen | Moment opnamen van Web Apps sleuven ophalen. |
> | Bewerking | Micro soft. web/sites/sleuven/sourcecontrols/verwijderen | Configuratie-instellingen voor het bron beheer van de web-app-sleuf verwijderen |
> | Bewerking | Micro soft. web/sites/sleuven/sourcecontrols/lezen | Configuratie-instellingen voor het bron beheer van de web-app-sleuf ophalen |
> | Bewerking | Micro soft. web/sites/sleuven/sourcecontrols/schrijven | Configuratie-instellingen voor bron beheer van de web-app-sleuf bijwerken |
> | Bewerking | Micro soft. web/sites/sleuven/starten/actie | Een web-app-sleuf starten |
> | Bewerking | Micro soft. web/sites/sleuven/stoppen/actie | Een web-app-sleuf stoppen |
> | Bewerking | micro soft. web/sites/sleuven/synchroniseren/actie | Web Apps-sleuven synchroniseren. |
> | Bewerking | micro soft. web/sites/sleuven/triggeredwebjobs/verwijderen | Web Apps-sleuven die webjobs hebben geactiveerd, verwijderen. |
> | Bewerking | micro soft. web/sites/sleuven/triggeredwebjobs/lezen | Haal Web Apps-sleuven op die webjobs hebben geactiveerd. |
> | Bewerking | micro soft. web/sites/sleuven/triggeredwebjobs/uitvoeren/actie | Voer Web Apps-sleuven uit die webjobs hebben geactiveerd. |
> | Bewerking | micro soft. web/sites/sleuven/gebruik/lezen | Web Apps sleuven gebruiken. |
> | Bewerking | micro soft. web/sites/sleuven/virtualnetworkconnections/verwijderen | Web Apps-sleuven verwijderen Virtual Network verbindingen. |
> | Bewerking | micro soft. web/sites/sleuven/virtualnetworkconnections/gateways/schrijven | Web Apps-sleuven bijwerken Virtual Network verbindingen gateways. |
> | Bewerking | micro soft. web/sites/sleuven/virtualnetworkconnections/lezen | Web Apps sleuven Virtual Network verbindingen ophalen. |
> | Bewerking | micro soft. web/sites/sleuven/virtualnetworkconnections/schrijven | Web Apps-sleuven Virtual Network verbindingen bijwerken. |
> | Bewerking | micro soft. web/sites/sleuven/webjobs/lezen | Web Apps-sleuven voor webjobs ophalen. |
> | Bewerking | Micro soft. web/sites/sleuven/schrijven | Een nieuwe web-app-sleuf maken of een bestaande site bijwerken |
> | Bewerking | Micro soft. web/sites/slotsdiffs/actie | Verschillen in de configuratie van de web-app en-sleuven |
> | Bewerking | Micro soft. web/sites/slotsswap/actie | Implementatie sleuven voor web-apps wisselen |
> | Bewerking | micro soft. web/sites/moment opnamen/lezen | Web Apps moment opnamen ophalen. |
> | Bewerking | Micro soft. web/sites/sourcecontrols/verwijderen | Configuratie-instellingen voor broncode beheer van web-app verwijderen |
> | Bewerking | Micro soft. web/sites/sourcecontrols/lezen | Configuratie-instellingen voor broncode beheer van web-app ophalen |
> | Bewerking | Micro soft. web/sites/sourcecontrols/schrijven | Configuratie-instellingen voor broncode beheer van web-app bijwerken |
> | Bewerking | Micro soft. web/sites/starten/actie | Een web-app starten |
> | Bewerking | Micro soft. web/sites/stoppen/actie | Een web-app stoppen |
> | Bewerking | micro soft. web/sites/synchroniseren/actie | Web Apps synchroniseren. |
> | Bewerking | micro soft. web/sites/syncfunctiontriggers/actie | Synchronisatie functie Triggers. |
> | Bewerking | micro soft. web/sites/triggeredwebjobs/verwijderen | Web Apps geactiveerde webjobs verwijderen. |
> | Bewerking | micro soft. web/sites/triggeredwebjobs/geschiedenis/lezen | Web Apps geactiveerde webjobs-geschiedenis ophalen. |
> | Bewerking | micro soft. web/sites/triggeredwebjobs/lezen | Web Apps geactiveerde webjobs ophalen. |
> | Bewerking | micro soft. web/sites/triggeredwebjobs/uitvoeren/actie | Web Apps geactiveerde webjobs worden uitgevoerd. |
> | Bewerking | micro soft. web/sites/gebruik/lezen | Ontvang Web Apps gebruik. |
> | Bewerking | micro soft. web/sites/virtualnetworkconnections/verwijderen | Web Apps Virtual Network verbindingen verwijderen. |
> | Bewerking | micro soft. web/sites/virtualnetworkconnections/gateways/lezen | Web Apps gateways voor Virtual Network verbindingen ophalen. |
> | Bewerking | micro soft. web/sites/virtualnetworkconnections/gateways/schrijven | Web Apps Virtual Network gateways voor verbindingen bijwerken. |
> | Bewerking | micro soft. web/sites/virtualnetworkconnections/lezen | Web Apps Virtual Network verbindingen ophalen. |
> | Bewerking | micro soft. web/sites/virtualnetworkconnections/schrijven | Web Apps Virtual Network verbindingen bijwerken. |
> | Bewerking | micro soft. web/sites/webjobs/lezen | Web Apps webjobs ophalen. |
> | Bewerking | Micro soft. web/sites/schrijven | Een nieuwe web-app maken of een bestaande bijwerken |
> | Bewerking | micro soft. Web/sku's/lezen | Down load Sku's. |
> | Bewerking | micro soft. Web/sourcecontrols/lezen | Bron besturings elementen ophalen. |
> | Bewerking | micro soft. Web/sourcecontrols/schrijven | Bron besturings elementen bijwerken. |
> | Bewerking | micro soft. Web/registratie/actie | De registratie van de resource provider micro soft. web voor het abonnement ongedaan maken. |
> | Bewerking | micro soft. Web/valideren/actie | Subelementid. |
> | Bewerking | micro soft. Web/verifyhostingenvironmentvnet/actie | Controleer het Vnet van de hosting omgeving. |

## <a name="microsoftworkloadmonitor"></a>Micro soft. WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Actie type | Bewerking | Beschrijving |
> | --- | --- | --- |
> | Bewerking | Micro soft. WorkloadMonitor/onderdelen/lezen | Hiermee worden onderdelen voor de resource opgehaald |
> | Bewerking | Micro soft. WorkloadMonitor/componentsSummary/lezen | Hiermee wordt een overzicht van onderdelen opgehaald |
> | Bewerking | Micro soft. WorkloadMonitor/monitorInstances/lezen | Hiermee worden exemplaren van monitors voor de resource opgehaald |
> | Bewerking | Micro soft. WorkloadMonitor/monitorInstancesSummary/lezen | Hiermee wordt een overzicht van monitor exemplaren opgehaald |
> | Bewerking | Micro soft. WorkloadMonitor/monitors/lezen | Hiermee worden monitors opgehaald voor de resource |
> | Bewerking | Micro soft. WorkloadMonitor/monitors/schrijven | Monitor configureren voor de resource |
> | Bewerking | Micro soft. WorkloadMonitor/notificationSettings/lezen | Hiermee worden meldings instellingen voor de resource opgehaald |
> | Bewerking | Micro soft. WorkloadMonitor/notificationSettings/schrijven | Instellingen voor meldingen voor de resource configureren |
> | Bewerking | Micro soft. WorkloadMonitor/Operations/lezen | Hiermee worden de ondersteunde bewerkingen opgehaald |

## <a name="next-steps"></a>Volgende stappen

- [Overeenkomende resource provider voor service](../azure-resource-manager/azure-services-resource-providers.md)
- [Aangepaste rollen voor Azure-resources](custom-roles.md)
- [Ingebouwde rollen voor Azure-resources](built-in-roles.md)
