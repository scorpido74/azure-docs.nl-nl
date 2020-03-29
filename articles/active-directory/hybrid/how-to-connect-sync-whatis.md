---
title: 'Azure AD Connect-synchronisatie: synchronisatie begrijpen en aanpassen | Microsoft Documenten'
description: Hier wordt uitgelegd hoe Azure AD Connect-synchronisatie werkt en hoe u deze aanpassen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3b87f40d75d4045155e7dd953dc76ffd9de2b34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60348736"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen
De Azure Active Directory Connect-synchronisatieservices (Azure AD Connect-synchronisatie) is een belangrijk onderdeel van Azure AD Connect. Het zorgt voor alle bewerkingen die verband houden met het synchroniseren van identiteitsgegevens tussen uw on-premises omgeving en Azure AD. Azure AD Connect-synchronisatie is de opvolger van DirSync, Azure AD Sync en Forefront Identity Manager met de Azure Active Directory Connector geconfigureerd.

Dit onderwerp is het thuis voor **Azure AD Connect-synchronisatie** (ook wel **synchronisatieengine**genoemd) en bevat koppelingen naar alle andere onderwerpen die ermee verband houden. Zie [Uw on-premises identiteiten integreren met Azure Active Directory](whatis-hybrid-identity.md)voor koppelingen naar Azure AD Connect.

De synchronisatieservice bestaat uit twee componenten, de on-premises **Azure AD Connect-synchronisatiecomponent** en de servicezijde in Azure AD, **azure AD Connect-synchronisatieservice**genaamd.

## <a name="azure-ad-connect-sync-topics"></a>Synchronisatieonderwerpen voor Azure AD Connect
| Onderwerp | Wat het dekt en wanneer te lezen |
| --- | --- |
| **Synchronisatiefundamenten van Azure AD Connect** | |
| [Inzicht in de architectuur](concept-azure-ad-connect-sync-architecture.md) |Voor degenen onder u die nieuw zijn in de sync engine en willen leren over de architectuur en de gebruikte termen. |
| [Technische concepten](how-to-connect-sync-technical-concepts.md) |Een korte versie van het architectuuronderwerp en legt kort de gebruikte termen uit. |
| [Topologieën voor Azure AD Connect](plan-connect-topologies.md) |Beschrijft de verschillende topologieën en scenario's die de synchronisatieengine ondersteunt. |
| **Aangepaste configuratie** | |
| [De wizard Installatie opnieuw uitvoeren](how-to-connect-installation-wizard.md) |Hier wordt uitgelegd welke opties beschikbaar zijn wanneer u de wizard Azure AD Connect-installatie opnieuw uitvoert. |
| [Inzicht in declaratieve inrichting](concept-azure-ad-connect-sync-declarative-provisioning.md) |Beschrijft het configuratiemodel genaamd declaratieve inrichting. |
| [Inzicht in verklarende inrichtingsexpressies](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Beschrijft de syntaxis voor de expressietaal die wordt gebruikt bij declaratieve inrichting. |
| [Inzicht in de standaardconfiguratie](concept-azure-ad-connect-sync-default-configuration.md) |Beschrijft de out-of-box regels en de standaardconfiguratie. Beschrijft ook hoe de regels samenwerken om de out-of-box scenario's te laten werken. |
| [Gebruikers en contactpersonen begrijpen](concept-azure-ad-connect-sync-user-and-contacts.md) |Gaat verder met het vorige onderwerp en beschrijft hoe de configuratie voor gebruikers en contactpersonen samenwerkt, met name in een omgeving met meerdere bossen. |
| [Een wijziging aanbrengen in de standaardconfiguratie](how-to-connect-sync-change-the-configuration.md) |Hiermee u een algemene configuratiewijziging aanbrengen in kenmerkstromen. |
| [Aanbevolen procedures voor het wijzigen van de standaardconfiguratie](how-to-connect-sync-best-practices-changing-default-configuration.md) |Ondersteuningsbeperkingen en voor het aanbrengen van wijzigingen in de out-of-box configuratie. |
| [Filteren configureren](how-to-connect-sync-configure-filtering.md) |Beschrijft de verschillende opties voor het beperken van welke objecten worden gesynchroniseerd met Azure AD en stap voor stap hoe u deze opties configureert. |
| **Functies en scenario's** | |
| [Onopzettelijke verwijderingen voorkomen](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Beschrijft de functie *voor het per ongeluk verwijderen en* hoe u deze configureren. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |Beschrijft de ingebouwde scheduler, die gegevens importeert, synchroniseert en exporteert. |
| [Synchronisatie van wachtwoordhash implementeren](how-to-connect-password-hash-synchronization.md) |Beschrijft hoe wachtwoordsynchronisatie werkt, hoe u deze implementeert en hoe u werken en problemen oplossen. |
| [Apparaat terugschrijven](how-to-connect-device-writeback.md) |Beschrijft hoe apparaatterugschrijfinformatie werkt in Azure AD Connect. |
| [Uitbreidingen van de directory](how-to-connect-sync-feature-directory-extensions.md) |Beschrijft hoe u het Azure AD-schema uitbreidt met uw eigen aangepaste kenmerken. |
| [Office 365 PreferredDataLocatie](how-to-connect-sync-feature-preferreddatalocation.md) |Beschrijft hoe u de Office 365-bronnen van de gebruiker in dezelfde regio plaatst als de gebruiker. |
| **Synchronisatieservice** | |
| [Functies van de Azure AD Connect-synchronisatieservice](how-to-connect-syncservice-features.md) |Beschrijft de synchronisatieservicekant en hoe u synchronisatie-instellingen in Azure AD wijzigen. |
| [Tolerantie voor het dupliceren van kenmerkentie](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Beschrijft hoe u de tolerantie van **userPrincipalName** en **proxyAddresses** dubbele kenmerken inschakelt. |
| **Bewerkingen en gebruikersinterface** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |Beschrijft de tabbladen Synchronization Service Manager, inclusief [Operations,](how-to-connect-sync-service-manager-ui-operations.md) [Connectors,](how-to-connect-sync-service-manager-ui-connectors.md) [Metaverse Designer](how-to-connect-sync-service-manager-ui-mvdesigner.md)en [Metaverse Search.](how-to-connect-sync-service-manager-ui-mvsearch.md) |
| [Operationele taken en overwegingen](how-to-connect-sync-operations.md) |Beschrijft operationele problemen, zoals disaster recovery. |
| **Procedures...** | |
| [Het Azure AD-account opnieuw instellen](how-to-connect-azureadaccount.md) |De referenties van het serviceaccount dat wordt gebruikt om verbinding te maken vanuit Azure AD Connect-synchronisatie met Azure AD opnieuw instellen. |
| **Meer informatie en referenties** | |
| [Poorten](reference-connect-ports.md) |Geeft aan welke poorten u moet openen tussen de synchronisatieengine en uw on-premises mappen en Azure AD. |
| [Kenmerken gesynchroniseerd naar Azure Active Directory](reference-connect-sync-attributes-synchronized.md) |Hiermee worden alle kenmerken weergegeven die worden gesynchroniseerd tussen on-premises AD en Azure AD. |
| [Functieverwijzing](reference-connect-sync-functions-reference.md) |Bevat alle functies die beschikbaar zijn in declaratieve inrichting. |

## <a name="additional-resources"></a>Aanvullende resources
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
