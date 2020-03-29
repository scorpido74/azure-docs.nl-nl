---
title: Het wachtwoord van het Azure AD Connector-account wijzigen | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u het Azure AD Connector-account herstellen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ea151ee79fccd66f1d9422744d8f57829677ec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204537"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Het wachtwoord voor het Azure AD Connector-account wijzigen
Het Azure AD Connector-account moet servicevrij zijn. Als u de referenties opnieuw moet instellen, dan is dit onderwerp iets voor u. Als een globale beheerder bijvoorbeeld per ongeluk het wachtwoord van het account opnieuw heeft ingesteld met PowerShell.

## <a name="reset-the-credentials"></a>De referenties opnieuw instellen
Als het Azure AD Connector-account geen contact kan opnemen met Azure AD vanwege verificatieproblemen, kan het wachtwoord opnieuw worden ingesteld.

1. Meld u aan bij de synchronisatieserver van Azure AD Connect en start PowerShell.
2. Voer `Add-ADSyncAADServiceAccount` uit.  
   ![PowerShell-account voor cmdlet addadsyncaadservice](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Geef Azure AD Global-beheerdersreferenties op.

Deze cmdlet reset het wachtwoord voor het serviceaccount en werkt het bij zowel in Azure AD als in de synchronisatieengine.

## <a name="known-issues-these-steps-can-solve"></a>Bekende problemen die deze stappen kunnen oplossen
Deze sectie is een lijst met fouten die zijn gerapporteerd door klanten die zijn opgelost door een reset van referenties op het Azure AD Connector-account.

---
Evenement 6900  
De server heeft een onverwachte fout opgelopen tijdens het verwerken van een melding van wachtwoordwijziging:  
AADSTS70002: Fout bij het valideren van referenties. AADSTS50054: Oud wachtwoord wordt gebruikt voor verificatie.

---
Evenement 659  
Fout bij het ophalen van de synchronisatieconfiguratie van het wachtwoordbeleid. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: Fout bij het valideren van referenties. AADSTS50054: Oud wachtwoord wordt gebruikt voor verificatie.

## <a name="next-steps"></a>Volgende stappen
**Overzichtsonderwerpen**

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)

