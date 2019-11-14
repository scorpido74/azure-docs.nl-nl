---
title: Beveiligings controles voor Azure App Service
description: Een controle lijst met beveiligings controles voor het evalueren van Azure App Service
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: app-service
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a1889def8d177c312618f12b3fa0480cc4b849b3
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74046869"
---
# <a name="security-controls-for-azure-app-service"></a>Beveiligings controles voor Azure App Service

In dit artikel worden de beveiligings besturings elementen gedocumenteerd die zijn ingebouwd in Azure App Service.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Netwerk

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie
|---|---|--|
| Ondersteuning voor service-eind punten| Ja | Beschikbaar voor App Service.| [Toegangs beperkingen Azure App Service](app-service-ip-restrictions.md)
| Ondersteuning voor VNet-injectie| Ja | App Service omgevingen zijn persoonlijke implementaties van App Service toegewezen aan één klant die is geïnjecteerd in het virtuele netwerk van een klant. | [Inleiding tot de App Service omgevingen](environment/intro.md)
| Ondersteuning voor netwerk isolatie en firewalling| Ja | Voor de open bare multi tenant-variatie van App Service kunnen klanten netwerk-Acl's (IP-beperkingen) configureren om toegestaan binnenkomend verkeer te vergren delen.  App Service omgevingen worden rechtstreeks geïmplementeerd in virtuele netwerken en kunnen daarom worden beveiligd met Nsg's. | [Toegangs beperkingen Azure App Service](app-service-ip-restrictions.md)
| Ondersteuning voor geforceerde tunneling| Ja | App Service omgevingen kunnen worden geïmplementeerd in het virtuele netwerk van een klant waar geforceerde tunneling is geconfigureerd. | [De Azure App Service-omgeving configureren met geforceerde tunneling](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>& Logboek registratie controleren

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie
|---|---|--|
| Ondersteuning voor Azure-bewaking (log Analytics, app Insights, enz.)| Ja | App Service integreert met Application Insights voor talen die Application Insights ondersteunen (volledige .NET Framework, .NET core, Java en node. JS).  Zie [Azure app service prestaties bewaken](../azure-monitor/app/azure-web-apps.md). Met App Service worden metrische gegevens van toepassingen ook naar Azure Monitor verzonden. | [Apps in Azure App Service bewaken](web-sites-monitor.md)
| Logboek registratie en controle op het vlak van controle en beheer| Ja | Alle beheer bewerkingen die op App Service objecten worden uitgevoerd, vindt plaats via [Azure Resource Manager](../azure-resource-manager/index.yml). Historische logboeken van deze bewerkingen zijn beschikbaar in de portal en via de CLI. | [Azure Resource Manager resource provider bewerkingen](../role-based-access-control/resource-provider-operations.md#microsoftweb), [AZ monitor Activity-Log](/cli/azure/monitor/activity-log)
| Logboek registratie en controle van het gegevens vlak | Nee | Het gegevens vlak voor App Service is een externe bestands share met de inhoud van de geïmplementeerde website van de klant.  De externe bestands share kan niet worden gecontroleerd. |

## <a name="identity"></a>Identiteit

| Beveiligings beheer | Ja/Nee | Opmerkingen |  Documentatie
|---|---|--|
| Authentication| Ja | Klanten kunnen toepassingen bouwen op App Service die automatisch worden geïntegreerd met [Azure Active Directory (Azure AD)](../active-directory/index.yml) en andere OAuth-compatibele id-providers voor beheer toegang tot app service-assets, alle toegang wordt bepaald door een combi natie van door Azure AD geverifieerde principal en Azure Resource Manager RBAC-rollen. | [Verificatie en autorisatie in Azure App Service](overview-authentication-authorization.md)
| Autorisatie| Ja | Voor beheer toegang tot App Service assets wordt alle toegang bepaald door een combi natie van door Azure AD geverifieerde Principal en Azure Resource Manager RBAC-rollen.  | [Verificatie en autorisatie in Azure App Service](overview-authentication-authorization.md)

## <a name="data-protection"></a>Gegevensbeveiliging

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie
|---|---|--|
| Versleuteling aan server zijde op rest: door micro soft beheerde sleutels | Ja | Inhoud van website bestand wordt opgeslagen in Azure Storage, die de inhoud automatisch versleutelt. <br><br>Door de klant verstrekte geheimen worden op rest versleuteld. De geheimen worden op rest versleuteld terwijl ze zijn opgeslagen in App Service configuratie databases.<br><br>Lokaal gekoppelde schijven kunnen eventueel worden gebruikt als tijdelijke opslag door websites (D:\Local en% TMP%). Lokaal gekoppelde schijven zijn niet versleuteld op rest. | [Azure Storage versleuteling voor Data-at-rest](../storage/common/storage-service-encryption.md)
| Versleuteling aan server zijde op rest: door de klant beheerde sleutels (BYOK) | Ja | Klanten kunnen ervoor kiezen om toepassings geheimen op te slaan in Key Vault en deze op te halen tijdens runtime. | [Key Vault verwijzingen gebruiken voor App Service en Azure Functions (preview-versie)](app-service-key-vault-references.md)
| Versleuteling op kolom niveau (Azure Data Services)| N.v.t. | |
| Versleuteling in transit (zoals ExpressRoute-versleuteling, in VNet-versleuteling en VNet-VNet-versleuteling)| Ja | Klanten kunnen websites zo configureren dat ze HTTPS vereisen en gebruiken voor inkomend verkeer.  | [Een Azure app service alleen https maken](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (blog bericht)
| Versleutelde API-aanroepen| Ja | Beheer aanroepen om App Service te configureren, worden uitgevoerd via [Azure Resource Manager](../azure-resource-manager/index.yml) -aanroepen via https. |

## <a name="configuration-management"></a>Configuratiebeheer

| Beveiligings beheer | Ja/Nee | Opmerkingen | Documentatie
|---|---|--|
| Ondersteuning voor configuratie beheer (versie van configuratie, enz.)| Ja | Voor beheer bewerkingen kan de status van een App Service configuratie worden geëxporteerd als een Azure Resource Manager sjabloon en de versie in de loop van de tijd. Voor runtime bewerkingen kunnen klanten meerdere verschillende live versies van een toepassing onderhouden met behulp van de functie App Service implementatie sleuven. | 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ingebouwde beveiligings controles in Azure-Services](../security/fundamentals/security-controls.md).
