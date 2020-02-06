---
title: 'Azure AD Connect-inrichtings agent: release geschiedenis van versie | Microsoft Docs'
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
ms.date: 02/04/2020
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: cac9eac2d17fa0a4f1db487d4c7f8beb67de24a9
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030664"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect-inrichtings agent: release geschiedenis van versie
Dit artikel bevat een overzicht van de versies en functies van Azure Active Directory Connect Provisioning agent die is uitgebracht. Het Azure AD-team werkt de inrichtings agent regel matig bij met nieuwe functies en functionaliteit. De inrichtings agent wordt automatisch bijgewerkt wanneer er een nieuwe versie wordt uitgebracht. 

U wordt aangeraden automatische updates in te scha kelen voor uw agents om ervoor te zorgen dat u beschikt over de nieuwste functies en oplossingen voor fouten. Micro soft biedt direct ondersteuning voor de nieuwste versie van de agent en één versie voor.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Release status

4 december 2019: uitgebracht voor downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Biedt ondersteuning voor [Azure AD Connect Cloud inrichting](../cloud-provisioning/what-is-cloud-provisioning.md) voor het synchroniseren van gebruikers, het contact opnemen en groeperen van gegevens van on-premises Active Directory naar Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Release status

9 september 2019: uitgebracht voor automatische updates

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Mogelijkheid om extra tracering en logboek registratie te configureren voor problemen met het inrichten van inrichtings agent
* De mogelijkheid om alleen de Azure AD-kenmerken op te halen die zijn geconfigureerd in de toewijzing om de prestaties van synchronisatie te verbeteren

### <a name="fixed-issues"></a>Problemen opgelost

* Er is een fout opgelost waarbij de agent niet meer reageert als er problemen zijn met Azure AD-verbindings fouten
* Er is een fout opgelost die problemen heeft veroorzaakt tijdens het lezen van binaire gegevens uit Azure Active Directory
* Er is een fout opgelost waarbij de agent geen vertrouwens relatie kan vernieuwen met de Cloud-Hybrid Identity-service

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Release status

23 januari 2019: uitgebracht voor downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* De inrichtings agent en connector architectuur zijn vernieuwd voor betere prestaties, stabiliteit en betrouw baarheid 
* Vereenvoudigde configuratie van de inrichtings agent met behulp van de wizard voor de installatie van de gebruikers interface 
* Er is ondersteuning toegevoegd voor automatische agent updates

