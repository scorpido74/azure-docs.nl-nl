---
title: 'Azure AD Connect: problemen met bronanker tijdens de installatie oplossen | Microsoft Documenten'
description: In dit onderwerp worden stappen gezet voor het oplossen van problemen met het bronanker tijdens de installatie.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: fac33a01afc2efc1ab06c4783c11f7a089bb6208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62114151"
---
# <a name="troubleshooting-source-anchor-issues-during-installation"></a>Problemen met bronanker oplossen tijdens de installatie
In dit artikel worden de verschillende bronankergerelateerde problemen uitgelegd die tijdens de installatie kunnen optreden en worden manieren gevonden om deze problemen op te lossen.

## <a name="invalid-source-anchor-in-azure-active-directory"></a>Ongeldig bronanker in Azure Active Directory

### <a name="custom-installation"></a>Aangepaste installatie

Tijdens de aangepaste installatie leest Azure AD Connect het bronankerbeleid uit Azure Active Directory. Als het beleid bestaat in Azure Active Directory, past Azure AD Connect hetzelfde beleid toe, tenzij het wordt overschreven door de klant. De wizard informeert u welk kenmerk is gelezen. Bovendien waarschuwt de wizard als u het bronankerbeleid probeert te overschrijven.

Tijdens deze leesbewerking is het mogelijk dat het bronankerbeleid in Azure Active Directory onverwacht is. In dit geval weet Azure AD Connect niet wat het bronanker moet gebruiken en moet handmatig worden overschreven.</br>
![Onverwachte](media/tshoot-connect-source-anchor/source1.png)

Als u dit probleem wilt oplossen, u het bronanker handmatig overschrijven door een specifiek kenmerk te selecteren. Ga verder met deze optie als en alleen als u zeker bent van welk kenmerk te selecteren. Als u dit niet zeker weet, neemt u contact op met [microsoft-ondersteuning](https://support.microsoft.com/contactus/) voor richtlijnen. Als u het bronankerbeleid wijzigt, kan de koppeling tussen uw on-premises gebruikers en hun bijbehorende Azure-bronnen worden afgebroken.</br>
![Onverwachte](media/tshoot-connect-source-anchor/source2.png)

### <a name="express-installation"></a>Express-installatie
Tijdens de expresinstallatie leest Azure AD Connect het bronankerbeleid van Azure Active Directory. Als het beleid bestaat in Azure Active Directory, past Azure AD Connect hetzelfde beleid toe. Er is geen optie om handmatig te overschrijven.

Tijdens deze leesbewerking is het mogelijk dat het bronankerbeleid in Azure Active Directory onverwacht is. In dit geval weet Azure AD Connect niet wat het bronanker moet zijn.</br>
![Onverwachte](media/tshoot-connect-source-anchor/source3.png)

Als u dit probleem wilt oplossen, moet u opnieuw installeren met de aangepaste modus en het bronanker handmatig overschrijven door een specifiek kenmerk te selecteren. Ga verder met deze optie als en alleen als u zeker bent van welk kenmerk te selecteren. Als u dit niet zeker weet, neemt u contact op met [microsoft-ondersteuning](https://support.microsoft.com/contactus/) voor richtlijnen. Als u het bronankerbeleid wijzigt, kan de koppeling tussen uw on-premises gebruikers en hun bijbehorende Azure-bronnen worden afgebroken.

### <a name="invalid-source-anchor-in-sync-engine"></a>Ongeldig bronanker in synchronisatieengine
Tijdens de installatie is het mogelijk dat Azure AD Connect probeert de synchronisatieengine te configureren met behulp van een ongeldig bronanker. Deze bewerking is waarschijnlijk een productprobleem en de installatie van Azure AD Connect mislukt. Neem contact op met [Microsoft-ondersteuning](https://support.microsoft.com/contactus/) als u dit probleem uitvoert.</br>
![Onverwachte](media/tshoot-connect-source-anchor/source4.png)


## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).