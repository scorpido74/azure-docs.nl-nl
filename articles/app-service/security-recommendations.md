---
title: Aanbevelingen voor beveiliging
description: Implementeer de beveiligingsaanbevelingen om te helpen voldoen aan uw beveiligingsverplichtingen zoals vermeld in ons gedeelde verantwoordelijkheidsmodel. Verbeter de beveiliging van uw app.
author: barclayn
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: e3f901fd78436b42729607d38f0ddb533f31cd30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684115"
---
# <a name="security-recommendations-for-app-service"></a>Beveiligingsaanbevelingen voor App-service

Dit artikel bevat beveiligingsaanbevelingen voor Azure App Service. Door deze aanbevelingen uit te voeren, u voldoen aan uw beveiligingsverplichtingen zoals beschreven in ons gedeelde verantwoordelijkheidsmodel en wordt de algehele beveiliging voor uw Web App-oplossingen verbeterd. Lees [Azure-infrastructuurbeveiliging](../security/fundamentals/infrastructure.md)voor meer informatie over wat Microsoft doet om de verantwoordelijkheden van de serviceprovider te vervullen.

## <a name="general"></a>Algemeen

| Aanbeveling | Opmerkingen |
|-|-|----|
| Blijf up-to-date | Gebruik de nieuwste versies van ondersteunde platforms, programmeertalen, protocollen en frameworks. |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

| Aanbeveling | Opmerkingen |
|-|----|
| Anonieme toegang uitschakelen | Tenzij u anonieme verzoeken moet ondersteunen, schakelt u anonieme toegang uit. Zie [Verificatie en autorisatie in Azure App Service](overview-authentication-authorization.md)voor meer informatie over verificatieopties voor Azure App Service.|
| Verificatie vereisen | Gebruik waar mogelijk de verificatiemodule van App Service in plaats van code te schrijven om verificatie en autorisatie te verwerken. Zie [Verificatie en autorisatie in Azure App Service](overview-authentication-authorization.md). |
| Back-endresources beveiligen met geverifieerde toegang | U de identiteit van de gebruiker gebruiken of een toepassingsidentiteit gebruiken om te verifiëren naar een back-endbron. Wanneer u ervoor kiest om een toepassingsidentiteit te gebruiken, gebruikt u een [beheerde identiteit.](overview-managed-identity.md)
| Verificatie van clientcertificaat vereisen | Clientcertificaatverificatie verbetert de beveiliging door alleen verbindingen van clients toe te staan die kunnen verifiëren met certificaten die u verstrekt. |

## <a name="data-protection"></a>Gegevensbeveiliging

| Aanbeveling | Opmerkingen |
|-|-|
| HTTP omleiden naar HTTP's | Clients kunnen standaard verbinding maken met webapps via zowel HTTP als HTTPS. We raden u aan HTTP om te leiden naar HTTP's omdat HTTPS het SSL/TLS-protocol gebruikt om een beveiligde verbinding te bieden, die zowel versleuteld als geverifieerd is. |
| Communicatie versleutelen naar Azure-bronnen | Wanneer uw app verbinding maakt met [Azure-bronnen,](https://azure.microsoft.com/services/sql-database/) zoals SQL Database of [Azure Storage,](/azure/storage/)blijft de verbinding in Azure. Aangezien de verbinding via de gedeelde netwerken in Azure verloopt, moet u altijd alle communicatie versleutelen. |
| De nieuwste TLS-versie vereisen mogelijk | Sinds 2018 maken nieuwe Azure App Service-apps gebruik van TLS 1.2. Nieuwere versies van TLS bevatten beveiligingsverbeteringen ten opzichte van oudere protocolversies. |
| FTPS gebruiken | App Service ondersteunt zowel FTP als FTPS voor het implementeren van uw bestanden. Gebruik FTPS in plaats van FTP indien mogelijk. Wanneer een of beide protocollen niet in gebruik zijn, moet u [ze uitschakelen.](deploy-ftp.md#enforce-ftps) |
| Toepassingsgegevens beveiligen | Sla toepassingsgeheimen, zoals databasereferenties, API-tokens of privésleutels, niet op in uw code of configuratiebestanden. De algemeen aanvaarde aanpak is om toegang te krijgen tot hen als [omgevingsvariabelen](https://wikipedia.org/wiki/Environment_variable) met behulp van het standaardpatroon in uw taal van keuze. In Azure App Service u omgevingsvariabelen definiëren via [app-instellingen](web-sites-configure.md) en [verbindingstekenreeksen.](web-sites-configure.md) App-instellingen en verbindingstekenreeksen worden versleuteld opgeslagen in Azure. De app-instellingen worden alleen gedecodeerd voordat ze in het procesgeheugen van uw app worden geïnjecteerd wanneer de app wordt gestart. De encryptiesleutels worden regelmatig gedraaid. U ook uw Azure App Service-app integreren met [Azure Key Vault](/azure/key-vault/) voor geavanceerd geheimenbeheer. Door [toegang te krijgen tot de Key Vault met een beheerde identiteit,](../key-vault/tutorial-web-application-keyvault.md)heeft uw App Service-app veilig toegang tot de geheimen die u nodig hebt. |

## <a name="networking"></a>Netwerken

| Aanbeveling | Opmerkingen |
|-|-|
| Statische IP-beperkingen gebruiken | Met Azure App Service op Windows u een lijst met IP-adressen definiëren die toegang hebben tot uw app. De toegestane lijst kan afzonderlijke IP-adressen of een reeks IP-adressen bevatten die zijn gedefinieerd door een subnetmasker. Zie [Statische IP-beperkingen van Azure App Service](app-service-ip-restrictions.md)voor meer informatie .  |
| De geïsoleerde prijslaag gebruiken | Behalve de geïsoleerde prijscategorie worden uw apps in alle lagen uitgevoerd op de gedeelde netwerkinfrastructuur in Azure App Service. De geïsoleerde laag geeft u volledige netwerkisolatie door uw apps uit te voeren in een speciale [App Service-omgeving.](environment/intro.md) Een App Service-omgeving wordt uitgevoerd in uw eigen exemplaar van [Azure Virtual Network.](/azure/virtual-network/)|
| Veilige verbindingen gebruiken bij toegang tot on-premises bronnen | U [hybride verbindingen,](app-service-hybrid-connections.md) [integratie van virtuele netwerken](web-sites-integrate-with-vnet.md)of [app-serviceomgevingen](environment/intro.md) gebruiken om verbinding te maken met on-premises bronnen. |
| Blootstelling aan binnenkomend netwerkverkeer beperken | Met netwerkbeveiligingsgroepen u de toegang tot het netwerk beperken en het aantal blootgestelde eindpunten beheren. Zie [Inkomend verkeer naar een app-serviceomgeving beheren voor](environment/app-service-app-service-environment-control-inbound-traffic.md)meer informatie. |

## <a name="monitoring"></a>Bewaking

| Aanbeveling | Opmerkingen |
|-|-|
|Standaardlaag Azure Security Center gebruiken | [Azure Security Center](../security-center/security-center-app-services.md) is native geïntegreerd met Azure App Service. Het kan beoordelingen uitvoeren en beveiligingsaanbevelingen geven. |

## <a name="next-steps"></a>Volgende stappen

Neem contact op met uw toepassingsprovider om te zien of er aanvullende beveiligingsvereisten zijn. Zie [Beveiligde ontwikkelingsdocumentatie](../security/fundamentals/abstract-develop-secure-apps.md)voor meer informatie over het ontwikkelen van veilige toepassingen.
