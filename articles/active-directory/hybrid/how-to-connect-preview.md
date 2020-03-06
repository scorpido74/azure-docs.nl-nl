---
title: 'Azure AD Connect: functies in de preview-versie | Microsoft Docs'
description: In dit onderwerp vindt u meer informatie over de functies in de preview-versie van Azure AD Connect.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375961"
---
# <a name="more-details-about-features-in-preview"></a>Meer informatie over functies in Preview
In dit onderwerp wordt beschreven hoe u functies kunt gebruiken die momenteel als preview-versie beschikbaar zijn.

## <a name="group-writeback"></a>Groep terugschrijven
Met de optie voor het terugschrijven van groepen in optionele functies kunt u **Office 365-groepen** terugschrijven naar een forest waarop Exchange is geïnstalleerd. Dit is een groep die altijd in de Cloud is gemastereerd. Als u Exchange on-premises hebt, kunt u deze groepen terugschrijven naar on-premises, zodat gebruikers met een on-premises Exchange-postvak e-mails van deze groepen kunnen verzenden en ontvangen.

Meer informatie over Office 365-groepen en hoe u deze kunt gebruiken, vindt u [hier](https://aka.ms/O365g).

Een Office 365-groep wordt weer gegeven als een distributie groep in on-premises AD DS. Uw on-premises Exchange-server moet op Exchange 2013 cumulatief update 8 (uitgebracht in maart 2015) of Exchange 2016 zijn om dit nieuwe groeps type te herkennen.

**Notities tijdens de preview**

* Het kenmerk adres boek wordt momenteel niet ingevuld in de preview-versie. Zonder dit kenmerk is de groep niet zichtbaar in de GAL. De eenvoudigste manier om dit kenmerk in te vullen, is door de Exchange Power shell-cmdlet `update-recipient`te gebruiken.
* Alleen forests met het Exchange-schema zijn geldige doelen voor groepen. Als er geen uitwisseling is gedetecteerd, kan groeps terugschrijven niet worden ingeschakeld.
* Er worden momenteel alleen implementaties van Exchange-organisaties met één forest ondersteund. Als u meer dan één Exchange-organisatie on-premises hebt, hebt u een on-premises GALSync-oplossing nodig voor deze groepen om weer te geven in uw andere forests.
* De functie voor het terugschrijven van groepen verwerkt geen beveiligings groepen of distributie groepen.

> [!NOTE]
> Er is een abonnement op Azure AD Premium vereist voor het terugschrijven van de groep.
> 
>

## <a name="user-writeback"></a>Gebruikers terugschrijven
> [!IMPORTANT]
> De preview-functie voor het terugschrijven van gebruikers is in de update van augustus 2015 voor Azure AD Connect verwijderd. Als u het hebt ingeschakeld, moet u deze functie uitschakelen.
>
>

## <a name="next-steps"></a>Volgende stappen
Ga door met de [aangepaste installatie van Azure AD Connect](how-to-connect-install-custom.md).

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory ](whatis-hybrid-identity.md).
