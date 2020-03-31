---
title: Hybride identiteitsontwerp - vereisten voor meervoudige verificatie Azure | Microsoft Documenten
description: Met Conditional Access-beheer controleert Azure Active Directory de specifieke voorwaarden die u kiest bij het verifiëren van de gebruiker en voordat u toegang tot de toepassing toestaat. Zodra aan deze voorwaarden is voldaan, wordt de gebruiker geverifieerd en toegang tot de toepassing toegestaan.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4743195fc79d43571ec79a13b8518edc7e81379b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109297"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Bepaal vereisten voor meervoudige verificatie voor uw hybride identiteitsoplossing
In deze wereld van mobiliteit, met gebruikers toegang tot gegevens en toepassingen in de cloud en vanaf elk apparaat, is het beveiligen van deze informatie van het grootste belang geworden.  Elke dag is er een nieuwe kop over een inbreuk op de beveiliging.  Hoewel er geen garantie is tegen dergelijke inbreuken, biedt multi-factor authenticatie een extra beveiligingslaag om deze inbreuken te voorkomen.
Begin met het evalueren van de vereisten van organisaties voor multi-factor authenticatie. Dat wil zeggen, wat is de organisatie probeert te beveiligen.  Deze evaluatie is belangrijk om de technische vereisten voor het opzetten en inschakelen van de organisaties gebruikers voor multi-factor authenticatie te definiëren.

Zorg ervoor dat u het volgende antwoord geeft:

* Probeert uw bedrijf Microsoft-apps te beveiligen? 
* Hoe worden deze apps gepubliceerd?
* Biedt uw bedrijf op afstand toegang om werknemers toegang te geven tot on-premises apps?

Zo ja, wat voor soort toegang op afstand? U moet ook evalueren waar de gebruikers die toegang hebben tot deze toepassingen zich zullen bevinden. Deze evaluatie is een andere belangrijke stap om de juiste multi-factor authenticatie strategie te definiëren. Zorg ervoor dat u de volgende vragen beantwoordt:

* Waar bevinden de gebruikers zich?
* Kunnen ze ergens worden gevestigd?
* Wil uw bedrijf beperkingen instellen op basis van de locatie van de gebruiker?

Zodra u deze vereisten begrijpt, is het belangrijk om ook de vereisten van de gebruiker voor multi-factor authenticatie te evalueren. Deze evaluatie is belangrijk omdat het de vereisten voor het uitrollen van multi-factor authenticatie zal definiëren. Zorg ervoor dat u de volgende vragen beantwoordt:

* Zijn de gebruikers bekend met multi-factor authenticatie?
* Zijn sommige toepassingen vereist om extra verificatie te bieden?  
  * Zo ja, de hele tijd, wanneer afkomstig van externe netwerken, of toegang tot specifieke toepassingen, of onder andere omstandigheden?
* Hebben de gebruikers training nodig over het instellen en implementeren van multi-factor authenticatie?
* Wat zijn de belangrijkste scenario's die uw bedrijf wil multi-factor authenticatie in te schakelen voor hun gebruikers?

Na het beantwoorden van de vorige vragen, u begrijpen of er multi-factor authenticatie al on-premises geïmplementeerd. Deze evaluatie is belangrijk om de technische vereisten voor het opzetten en inschakelen van de organisaties gebruikers voor multi-factor authenticatie te definiëren. Zorg ervoor dat u de volgende vragen beantwoordt:

* Moet uw bedrijf bevoorrechte accounts beschermen met MFA?
* Moet uw bedrijf MFA inschakelen voor bepaalde toepassingen om nalevingsredenen?
* Moet uw bedrijf MFA inschakelen voor alle in aanmerking komende gebruikers van deze toepassing of alleen beheerders?
* Heeft u MFA altijd ingeschakeld of alleen wanneer de gebruikers buiten uw bedrijfsnetwerk zijn aangemeld?

## <a name="next-steps"></a>Volgende stappen
[Een strategie voor hybride identiteitsacceptatie definiëren](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Zie ook
[Overzicht ontwerpoverwegingen](plan-hybrid-identity-design-considerations-overview.md)

