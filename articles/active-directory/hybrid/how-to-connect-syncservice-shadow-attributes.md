---
title: Schaduwkenmerken van Azure AD Connect-synchronisatieservice | Microsoft Documenten
description: Beschrijft hoe schaduwkenmerken werken in de synchronisatieservice van Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a4078f49abbdf431f42c6cde7cf882112e5848
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60384699"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Schaduwkenmerken van Azure AD Connect-synchronisatieservice
De meeste kenmerken worden in Azure AD op dezelfde manier weergegeven als in uw on-premises Active Directory. Sommige kenmerken hebben echter een speciale verwerking en de kenmerkwaarde in Azure AD kan anders zijn dan wat Azure AD Connect synchroniseert.

## <a name="introducing-shadow-attributes"></a>Schaduwkenmerken introduceren
Sommige kenmerken hebben twee weergaven in Azure AD. Zowel de on-premises waarde als een berekende waarde worden opgeslagen. Deze extra kenmerken worden schaduwkenmerken genoemd. De twee meest voorkomende kenmerken waarbij u dit gedrag ziet, zijn **userPrincipalName** en **proxyAddress**. De wijziging in kenmerkwaarden gebeurt wanneer er waarden in deze kenmerken staan die niet-geverifieerde domeinen vertegenwoordigen. Maar de synchronisatie-engine in Connect leest de waarde in het schaduwattribuut, dus vanuit het perspectief is het kenmerk bevestigd door Azure AD.

U de schaduwkenmerken niet zien via de Azure-portal of met PowerShell. Maar het begrijpen van het concept helpt u om bepaalde scenario's op te lossen waarbij het kenmerk verschillende waarden on-premises en in de cloud heeft.

Om het gedrag beter te begrijpen, kijk naar dit voorbeeld van Fabrikam:  
![Domeinen](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Ze hebben meerdere UPN-achtervoegsels in hun on-premises Active Directory, maar ze hebben er slechts één geverifieerd.

### <a name="userprincipalname"></a>userPrincipalName
Een gebruiker heeft de volgende kenmerkwaarden in een niet-geverifieerd domein:

| Kenmerk | Waarde |
| --- | --- |
| on-premises userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD-shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD-userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

Het kenmerk userPrincipalName is de waarde die u ziet bij het gebruik van PowerShell.

Aangezien de werkelijke on-premises kenmerkwaarde wordt opgeslagen in Azure AD, wordt Azure AD, wanneer u het fabrikam.com-domein verifiëren, het kenmerk userPrincipalName bijgewerkt met de waarde van de shadowUserPrincipalName. U hoeft geen wijzigingen van Azure AD Connect te synchroniseren om deze waarden te kunnen bijwerken.

### <a name="proxyaddresses"></a>proxyAddresses
Hetzelfde proces voor alleen het opnemen van geverifieerde domeinen vindt ook plaats voor proxyAdressen, maar met een aantal extra logica. De controle op geverifieerde domeinen gebeurt alleen voor postvakgebruikers. Een gebruiker of contactpersoon met e-mail vertegenwoordigt een gebruiker in een andere Exchange-organisatie en u alle waarden in proxyAdressen aan deze objecten toevoegen.

Voor een postvakgebruiker, on-premises of in Exchange Online, worden alleen waarden voor geverifieerde domeinen weergegeven. Het kan er zo uitzien:

| Kenmerk | Waarde |
| --- | --- |
| on-premises proxyAdressen | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online-proxyAdressen | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

In dit geval **is smtp:abbie.spencer\@fabrikam.com** is verwijderd omdat dat domein niet is geverifieerd. Maar Exchange voegde ook **SIP:abbie.spencer\@fabrikamonline.com**. Fabrikam heeft lync/Skype niet on-premises gebruikt, maar Azure AD en Exchange Online bereiden zich erop voor.

Deze logica voor proxyAdressen wordt **proxycalc genoemd.** ProxyCalc wordt aangeroepen bij elke wijziging op een gebruiker wanneer:

- Aan de gebruiker is een serviceplan toegewezen dat Exchange Online bevat, zelfs als de gebruiker geen licentie heeft voor Exchange. Als de gebruiker bijvoorbeeld de Office E3 SKU toegewezen krijgt, maar alleen SharePoint Online is toegewezen. Dit geldt zelfs als uw mailbox nog on-premises is.
- Het kenmerk msExchRecipientTypeDetails heeft een waarde.
- U wijzigt proxyAdressen of userPrincipalName.

Het kan enige tijd duren voordat ProxyCalc een wijziging voor een gebruiker verwerkt en is niet synchroon met het Azure AD Connect-exportproces.

> [!NOTE]
> De ProxyCalc-logica heeft een aantal aanvullende gedragingen voor geavanceerde scenario's die niet zijn gedocumenteerd in dit onderwerp. Dit onderwerp is bedoeld om het gedrag te begrijpen en niet alle interne logica te documenteren.

### <a name="quarantined-attribute-values"></a>In quarantaine geplaatste kenmerkwaarden
Schaduwkenmerken worden ook gebruikt wanneer er dubbele attribuutwaarden zijn. Zie [tolerantie voor dubbele kenmerken voor](how-to-connect-syncservice-duplicate-attribute-resiliency.md)meer informatie .

## <a name="see-also"></a>Zie ook
* [Synchronisatie van Azure AD Connect](how-to-connect-sync-whatis.md)
* [Uw on-premises identiteiten integreren met Azure Active Directory.](whatis-hybrid-identity.md)
