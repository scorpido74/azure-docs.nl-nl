---
title: Beveiligingsmaatregelen
description: Zoek een checklist met beveiligingsbesturingselementen voor het evalueren van Azure App Service voor uw organisatie.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671454"
---
# <a name="security-controls-for-azure-app-service"></a>Beveiligingsbesturingselementen voor Azure App Service

In dit artikel worden de beveiligingsbesturingselementen die zijn ingebouwd in Azure App Service, document.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie
|---|---|--|
| Ondersteuning voor serviceeindpunten| Ja | Beschikbaar voor App Service.| [Beperkingen voor azure-servicetoegang](app-service-ip-restrictions.md)
| Ondersteuning voor VNet-injectie| Ja | App Service Omgevingen zijn privé-implementaties van App Service gewijd aan een enkele klant geïnjecteerd in het virtuele netwerk van een klant. | [Inleiding tot Azure App Service Environments](environment/intro.md)
| Ondersteuning voor Netwerkisolatie en Firewalling| Ja | Voor de openbare variant van app-service met meerdere tenant's kunnen klanten netwerkaCL's (IP-beperkingen) configureren om toegestaan binnenkomend verkeer af te sluiten.  App Service Environments worden direct geïmplementeerd in virtuele netwerken en kunnen dus worden beveiligd met NSG's. | [Beperkingen voor azure-servicetoegang](app-service-ip-restrictions.md)
| Ondersteuning voor gedwongen tunneling| Ja | App-serviceomgevingen kunnen worden geïmplementeerd in het virtuele netwerk van een klant waar gedwongen tunneling is geconfigureerd. | [De Azure App Service-omgeving configureren met geforceerde tunnels](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Controle & logboekregistratie

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie
|---|---|--|
| Azure-bewakingsondersteuning (logboekanalyses, app-inzichten, enz.)| Ja | App Service integreert met Application Insights voor talen die Application Insights ondersteunen (Full .NET Framework, .NET Core, Java en Node.JS).  Zie [Azure App Service-prestaties controleren](../azure-monitor/app/azure-web-apps.md). App Service stuurt ook toepassingsstatistieken naar Azure Monitor. | [Apps controleren in Azure App Service](web-sites-monitor.md)
| Logboekregistratie en audit van het controle- en beheervlak| Ja | Alle beheerbewerkingen die worden uitgevoerd op App Service-objecten, vinden plaats via [Azure Resource Manager.](../azure-resource-manager/index.yml) Historische logboeken van deze bewerkingen zijn zowel in het portaal als via de CLI beschikbaar. | [Azure Resource Manager resource provider operations](../role-based-access-control/resource-provider-operations.md#microsoftweb), az monitor [activity-log](/cli/azure/monitor/activity-log)
| Logboekregistratie en -audit van gegevensvliegtuigen | Nee | Het gegevensvlak voor App-service is een externe bestandsshare met de geïmplementeerde website-inhoud van een klant.  Er is geen controle van de externe bestandsshare. |

## <a name="identity"></a>Identiteit

| Beveiligingscontrole | Ja/Nee | Opmerkingen |  Documentatie
|---|---|--|
| Authentication| Ja | Klanten kunnen applicaties bouwen op App Service die automatisch worden geïntegreerd met [Azure Active Directory (Azure AD)](../active-directory/index.yml) en andere OAuth-compatibele identiteitsproviders Voor beheertoegang tot App Service-assets wordt alle toegang beheerd door een combinatie van Azure AD-geverifieerde principal- en Azure Resource Manager RBAC-rollen. | [Verificatie en autorisatie in Azure App Service](overview-authentication-authorization.md)
| Autorisatie| Ja | Voor beheertoegang tot App Service-assets wordt alle toegang beheerd door een combinatie van Azure AD-geverifieerde principal- en Azure Resource Manager RBAC-rollen.  | [Verificatie en autorisatie in Azure App Service](overview-authentication-authorization.md)

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie
|---|---|--|
| Server-side encryptie in rust: door Microsoft beheerde sleutels | Ja | Inhoud van het webbestand wordt opgeslagen in Azure Storage, waardoor de inhoud in rust automatisch wordt versleuteld. <br><br>Door de klant geleverde geheimen worden in rust versleuteld. De geheimen worden in rust versleuteld terwijl ze worden opgeslagen in app-configuratiedatabases.<br><br>Lokaal aangesloten schijven kunnen optioneel worden gebruikt als tijdelijke opslag door websites (D:\local en %TMP%). Lokaal aangesloten schijven worden niet versleuteld in rust. | [Azure Storage-versleuteling voor gegevens in rust](../storage/common/storage-service-encryption.md)
| Server-side encryptie in rust: door de klant beheerde sleutels (BYOK) | Ja | Klanten kunnen ervoor kiezen om toepassingsgeheimen op te slaan in Key Vault en ze op te halen tijdens runtime. | [Key Vault-verwijzingen gebruiken voor App Service en Azure-functies (voorbeeld)](app-service-key-vault-references.md)
| Versleuteling op kolomniveau (Azure Data Services)| N.v.t. | |
| Versleuteling tijdens het transport (zoals ExpressRoute-versleuteling, vnet-versleuteling en VNet-VNet-versleuteling)| Ja | Klanten kunnen websites configureren om HTTPS te vereisen en te gebruiken voor binnenkomend verkeer.  | [Alleen https van Azure App Service maken](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (blogbericht)
| API-aanroepen versleuteld| Ja | Managementcalls om App Service te configureren vinden plaats via [Azure Resource Manager-oproepen](../azure-resource-manager/index.yml) via HTTPS. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligingscontrole | Ja/Nee | Opmerkingen | Documentatie
|---|---|--|
| Ondersteuning voor configuratiebeheer (versiebeheer van configuratie, enz.)| Ja | Voor beheerbewerkingen kan de status van een App Service-configuratie worden geëxporteerd als een Azure Resource Manager-sjabloon en in de loop van de tijd worden geversioned. Voor runtime-bewerkingen kunnen klanten meerdere verschillende liveversies van een toepassing onderhouden met de functie Implementatiesleuven voor app-service. | 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligingsbesturingselementen voor Azure-services](../security/fundamentals/security-controls.md).
