---
title: 'Hybride identiteit: vergelijking van hulpprogramma’s voor directory-integratie | Microsoft Docs'
description: Deze pagina bevat een uitgebreide tabel waarin de verschillende hulpprogramma's voor directory-integratie worden vergeleken.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 052d99a819aee415d5e7ad6dc00b8c786af0f636
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811090"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Vergelijking van hulpprogramma’s voor directory-integratie voor hybride identiteit
In de afgelopen jaren hebben de hulpprogramma's voor directory-integratie zich enorm ontwikkeld.  


- [FIM](https://docs.microsoft.com/previous-versions/windows/desktop/forefront-2010/ff182370%28v%3dvs.100%29) en [MIM](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) worden nog steeds ondersteund en maken vooral synchronisatie tussen on-premises systemen mogelijk.   De [FIM Windows Azure AD Connector](https://docs.microsoft.com/previous-versions/mim/dn511001(v=ws.10)?redirectedfrom=MSDN) wordt ondersteund in zowel FIM als MIM, maar wordt niet aanbevolen voor nieuwe implementaties - klanten met on-premises bronnen zoals Notes of SAP HCM moeten MIM gebruiken om Active Directory Domain Services (AD DS) te vullen en vervolgens ook Azure AD Connect-synchronisatie of Azure AD Connect-cloudinrichting gebruiken om te synchroniseren van AD DS naar Azure AD.
- [Azure AD Connect-synchronisatie](how-to-connect-sync-whatis.md) bevat de componenten en functionaliteit die eerder zijn uitgebracht in DirSync en Azure AD Sync, voor synchronisatie tussen AD DS-forests en Azure AD.  
- [Azure AD Connect cloud provisioning](../cloud-provisioning/what-is-cloud-provisioning.md) is een nieuwe Microsoft-agent voor het synchroniseren van AD DS naar Azure AD, handig voor scenario's zoals fusie en overname waarbij de AD-forests van het overgenomen bedrijf geïsoleerd zijn van de AD-forests van het moederbedrijf.

Zie het artikel [Wat is Azure AD Connect-cloudinrichting voor](../cloud-provisioning/what-is-cloud-provisioning.md) meer informatie over de verschillen tussen Azure AD Connect-synchronisatie en Azure AD Connect-cloudprovisioning?

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).

