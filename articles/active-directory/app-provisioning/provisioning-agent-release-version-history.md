---
title: 'Azure AD Connect Provisioning Agent: Releasegeschiedenis versie | Microsoft Documenten'
description: In dit artikel worden alle releases van Azure AD Connect Provisioning Agent weergegeven en worden nieuwe functies en vaste problemen beschreven
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183241"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect Provisioning Agent: Releasegeschiedenis versie
In dit artikel worden de versies en functies van Azure Active Directory Connect Provisioning Agent weergegeven die zijn uitgebracht. Het Azure AD-team werkt de provisioning agent regelmatig bij met nieuwe functies en functionaliteit. De inrichtingsagent wordt automatisch bijgewerkt wanneer een nieuwe versie wordt uitgebracht. 

Microsoft biedt directe ondersteuning voor de nieuwste agentversie en één versie.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Status van de release

4 december 2019: Vrij gegeven om te downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Inclusief ondersteuning voor [Azure AD Connect-cloudinrichting](../cloud-provisioning/what-is-cloud-provisioning.md) voor het synchroniseren van gebruikers-, contact- en groepsgegevens van on-premises Active Directory naar Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Status van de release

9 september 2019: Uitgebracht voor automatische update

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Mogelijkheid om aanvullende tracering en logboekregistratie te configureren voor problemen met het opsporen van provisioning agent
* Mogelijkheid om alleen de Azure AD-kenmerken op te halen die zijn geconfigureerd in de toewijzing om de prestaties van synchronisatie te verbeteren

### <a name="fixed-issues"></a>Problemen opgelost

* Een bug verholpen waarbij de agent niet reageerde als er problemen waren met Azure AD-verbindingsfouten
* Een bug opgelost die problemen veroorzaakte toen binaire gegevens werden gelezen uit Azure Active Directory
* Een bug opgelost waarbij de agent het vertrouwen met de hybride cloud-identiteitsservice niet heeft verlengd

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Status van de release

23 januari 2019: Vrij gegeven om te downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Vernieuwde inrichtingsagent- en connectorarchitectuur voor betere prestaties, stabiliteit en betrouwbaarheid 
* De configuratie van provisioning agent vereenvoudigd met de wizard UI-gestuurde installatie 
* Ondersteuning toegevoegd voor automatische agentupdates

