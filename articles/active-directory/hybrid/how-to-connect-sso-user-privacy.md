---
title: Gebruikersprivacy en Azure AD Naadloze enkele aanmelding | Microsoft Documenten
description: In dit artikel wordt ingehouden op Azure Active Directory (Azure AD) Seamless SSO en GDPR compliance.
services: active-directory
keywords: wat is Azure AD Connect, GDPR, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9311c1060b953e87f163cb482db14cdd43f50d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60242104"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Gebruikersprivacy en naadloze eenmalige aanmelding van Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Overzicht


Azure AD Seamless SSO maakt het volgende logboektype, dat persoonlijke gegevens kan bevatten: 

- Azure AD Connect tracelogbestanden.

Verbeter de privacy van gebruikers voor Seamless SSO op twee manieren:

1.  Haal op verzoek gegevens voor een persoon en verwijder gegevens van die persoon uit de installaties.
2.  Zorg ervoor dat gegevens niet langer dan 48 uur worden bewaard.

We raden de tweede optie ten zeerste aan, omdat het gemakkelijker te implementeren en te onderhouden is. Zie de volgende instructies voor elk logboektype:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Azure AD Connect-traceringslogboekbestanden verwijderen

Controleer de inhoud van **de map %ProgramData%\AADConnect** en verwijder de inhoud van het tracelog **(trace-\*.log-bestanden)** van deze map binnen 48 uur na het installeren of upgraden van Azure AD Connect of het wijzigen van de Seamless SSO-configuratie, omdat deze actie gegevens kan maken die onder de AVG vallen.

>[!IMPORTANT]
>Verwijder het bestand **PersistedState.xml** niet in deze map, omdat dit bestand wordt gebruikt om de status van de vorige installatie van Azure AD Connect te behouden en wordt gebruikt wanneer een upgrade-installatie is uitgevoerd. Dit bestand bevat nooit gegevens over een persoon en mag nooit worden verwijderd.

U deze traceerlogboekbestanden controleren en verwijderen met Windows Verkenner of u het volgende PowerShell-script gebruiken om de nodige acties uit te voeren:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Sla het script op in een bestand met de ". PS1" extensie. Voer dit script uit als dat nodig is.

Zie [dit artikel](reference-connect-user-privacy.md)voor meer informatie over gerelateerde AZURE AD Connect GDPR-vereisten.

### <a name="note-about-domain-controller-logs"></a>Opmerking over logboeken van domeincontroller

Als controlelogboekregistratie is ingeschakeld, kan dit product beveiligingslogboeken genereren voor uw domeincontrollers. Lees dit [artikel](https://technet.microsoft.com/library/dd277403.aspx)voor meer informatie over het configureren van controlebeleid.

## <a name="next-steps"></a>Volgende stappen

* [Het Privacybeleid van Microsoft controleren op het vertrouwenscentrum](https://www.microsoft.com/trustcenter)
  - [**Problemen oplossen**](tshoot-connect-sso.md) : meer informatie over het oplossen van veelvoorkomende problemen met de functie.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Voor het indienen van nieuwe functieaanvragen.