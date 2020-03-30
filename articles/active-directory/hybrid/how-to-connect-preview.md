---
title: 'Azure AD Connect: functies in preview | Microsoft Documenten'
description: In dit onderwerp worden in meer detail functies beschreven die in preview in Azure AD Connect staan.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7def733a80aea1be77825bb9069217f5f43e003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261279"
---
# <a name="more-details-about-features-in-preview"></a>Meer details over functies in preview
In dit onderwerp wordt beschreven hoe u functies gebruikt die momenteel in een voorbeeld functie zijn.

## <a name="group-writeback"></a>Groep terugschrijven
Met de optie voor groepsterugschrijven in optionele functies u **Office 365-groepen** terugschrijven naar een forest waarop Exchange is geïnstalleerd. Dit is een groep die altijd wordt beheerst in de cloud. Als u Exchange on-premises hebt, u deze groepen terugschrijven naar on-premises, zodat gebruikers met een on-premises Exchange-postvak e-mails van deze groepen kunnen verzenden en ontvangen.

Meer informatie over Office 365-groepen en hoe u deze gebruiken, vindt u [hier](https://aka.ms/O365g).

Een Office 365-groep wordt weergegeven als een distributiegroep in on-premises AD DS. Uw on-premises Exchange-server moet zich op Exchange 2013 cumulatieve update 8 (uitgebracht in maart 2015) of Exchange 2016 bevinden om dit nieuwe groepstype te herkennen.

**Opmerkingen tijdens de preview**

* Het kenmerk adresboek wordt momenteel niet ingevuld in de preview. Zonder dit kenmerk is de groep niet zichtbaar in de GAL. De eenvoudigste manier om dit kenmerk in te vullen, `update-recipient`is door de Exchange PowerShell-cmdlet te gebruiken.
* Alleen forests met het Exchange-schema zijn geldige doelen voor groepen. Als er geen Exchange is gedetecteerd, is groepsafschrijving niet mogelijk om in te schakelen.
* Alleen implementaties van Exchange-organisaties met één forest worden momenteel ondersteund. Als u meer dan één Exchange-organisatie on-premises hebt, hebt u een on-premises GALSync-oplossing nodig om deze groepen in uw andere forests weer te geven.
* De functie Terugschrijffunctie groeperen verwerkt geen beveiligingsgroepen of distributiegroepen.

> [!NOTE]
> Een abonnement op Azure AD Premium is vereist voor het terugschrijven van groepen.
> 
>

## <a name="user-writeback"></a>Terugschrijven van gebruikers
> [!IMPORTANT]
> De preview-functie voor gebruikerswriteback is verwijderd in de update van augustus 2015 voor Azure AD Connect. Als u deze hebt ingeschakeld, moet u deze functie uitschakelen.
>
>

## <a name="next-steps"></a>Volgende stappen
Doorgaan [met](how-to-connect-install-custom.md)de aangepaste installatie van Azure AD Connect .

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
