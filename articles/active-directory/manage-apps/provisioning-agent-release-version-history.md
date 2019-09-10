---
title: 'Azure AD Connect-inrichtings agent: Release geschiedenis van versie | Microsoft Docs'
description: In dit artikel vindt u een overzicht van alle releases van Azure AD Connect-inrichtings agent en worden nieuwe functies en problemen opgelost
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
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: de6ef5dedb0779536cfbc1e6a3440e748ff78524
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862107"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect-inrichtings agent: Releasegeschiedenis van versie
Dit artikel bevat een overzicht van de versies en functies van Azure AD Connect Provisioning agent die is uitgebracht. Het Azure AD-team werkt de inrichtings agent regel matig bij met nieuwe functies en functionaliteit. Inrichtings agenten worden automatisch bijgewerkt wanneer een nieuwe versie wordt uitgebracht. 

U wordt aangeraden automatische updates in te scha kelen voor uw agents om ervoor te zorgen dat u beschikt over de nieuwste functies en oplossingen voor fouten. Micro soft biedt direct ondersteuning voor de nieuwste versie van de agent en één versie voor.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Release status

9 september 2019: Uitgebracht voor automatische updates

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Mogelijkheid om extra tracering en logboek registratie te configureren voor problemen met het inrichten van inrichtings agent
* De mogelijkheid om alleen de AD-kenmerken op te halen die zijn geconfigureerd in de toewijzing om de prestaties van synchronisatie te verbeteren

### <a name="fixed-issues"></a>Problemen opgelost

* Er is een fout opgelost waarbij-in de agent niet meer reageert als er problemen zijn met AD-verbindings fouten
* Er is een fout opgelost die problemen heeft veroorzaakt tijdens het lezen van binaire gegevens uit Active Directory
* Er is een fout opgelost waarbij-in de agent geen vertrouwens relatie kan worden verlengd met de Cloud-Hybrid Identity-service

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Release status

23 januari 2019: Uitgebracht voor downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* De inrichtings agent &-connector architectuur is vernieuwd voor betere prestaties, stabiliteit en betrouw baarheid 
* Vereenvoudigde configuratie van de inrichtings agent met behulp van de door de gebruikers interface gebaseerde installatie wizard 
* Er is ondersteuning toegevoegd voor automatische agent updates

