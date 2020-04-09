---
title: Azure AD Connect-synchronisatieservicefuncties en -configuratie | Microsoft Documenten
description: Beschrijft servicefuncties voor Azure AD Connect-synchronisatieservice.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3f6b698922440c6e3e9b488cca93ca8d98d9c59
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983072"
---
# <a name="azure-ad-connect-sync-service-features"></a>Functies van de Azure AD Connect-synchronisatieservice

De synchronisatiefunctie van Azure AD Connect heeft twee componenten:

* De on-premises component met de naam **Azure AD Connect sync**, ook wel **synchronisatieengine**genoemd .
* De service die zich in Azure AD begeeft, ook wel **Azure AD Connect-synchronisatieservice genoemd**

In dit onderwerp wordt uitgelegd hoe de volgende functies van de **Azure AD Connect-synchronisatieservice** werken en hoe u deze configureren met Windows PowerShell.

Deze instellingen zijn geconfigureerd door de [Azure Active Directory Module voor Windows PowerShell.](https://aka.ms/aadposh) Download en installeer het apart van Azure AD Connect. De cmdlets gedocumenteerd in dit onderwerp werden geïntroduceerd in de [2016 maart release (build 9031.1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Als u niet beschikt over de cmdlets gedocumenteerd in dit onderwerp of ze produceren niet hetzelfde resultaat, zorg er dan voor dat u de nieuwste versie.

Voer de configuratie in uw Azure `Get-MsolDirSyncFeatures`AD-map uit om de configuratie in uw Azure AD-map uit te voeren.  
![Get-MsolDirSyncFeatures-resultaat](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Veel van deze instellingen kunnen alleen worden gewijzigd door Azure AD Connect.

De volgende instellingen kunnen `Set-MsolDirSyncFeature`worden geconfigureerd door:

| DirSyncFunctie | Opmerking |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Hiermee kunnen objecten deelnemen aan userPrincipalName, naast het primaire SMTP-adres. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Hiermee kan de synchronisatieengine het kenmerk userPrincipalName bijwerken voor beheerde/gelicentieerde (niet-federatieve) gebruikers. |

Nadat u een functie hebt ingeschakeld, kan deze niet opnieuw worden uitgeschakeld.

> [!NOTE]
> Vanaf 24 augustus 2016 is de functie *Dubbele kenmerktolerantie* standaard ingeschakeld voor nieuwe Azure AD-mappen. Deze functie wordt ook uitgerold en ingeschakeld op mappen die vóór deze datum zijn gemaakt. U ontvangt een e-mailmelding wanneer uw directory op het punt staat deze functie in te schakelen.
> 
> 

De volgende instellingen zijn geconfigureerd door Azure AD `Set-MsolDirSyncFeature`Connect en kunnen niet worden gewijzigd door:

| DirSyncFunctie | Opmerking |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: terugschrijven van apparaten inschakelen](how-to-connect-device-writeback.md) |
| DirectoryExtensies |[Synchronisatie van Azure AD Connect: Directory-extensies](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Hiermee kan een kenmerk in quarantaine worden geplaatst wanneer het een duplicaat van een ander object is in plaats van het hele object tijdens het exporteren te laten mislukken. |
| Wachtwoordhashsynchronisatie |[Synchronisatie van wachtwoordhash implementeren met Azure AD Connect-synchronisatie](how-to-connect-password-hash-synchronization.md) |
|Pass-through-verificatie|[Gebruikersaanmelding met Pass Through-verificatie in Azure Active Directory](how-to-connect-pta.md)|
| UnifiedGroupWriteback |[Voorbeeld: Terugschrijven van groepen](how-to-connect-preview.md#group-writeback) |
| UserWriteback |Momenteel niet ondersteund. |

## <a name="duplicate-attribute-resiliency"></a>Tolerantie voor het dupliceren van kenmerkentie

In plaats van objecten met dubbele UPN's/ proxyAdressen niet in te richten, wordt het gedupliceerde kenmerk "in quarantaine geplaatst" en wordt een tijdelijke waarde toegewezen. Wanneer het conflict is opgelost, wordt de tijdelijke UPN automatisch gewijzigd in de juiste waarde. Zie [Identiteitssynchronisatie en tolerantie voor het dupliceren van kenmerken](how-to-connect-syncservice-duplicate-attribute-resiliency.md)voor meer informatie.

## <a name="userprincipalname-soft-match"></a>Soft Match userPrincipalName

Wanneer deze functie is ingeschakeld, is soft-match ingeschakeld voor UPN naast het [primaire SMTP-adres](https://support.microsoft.com/kb/2641663), dat altijd is ingeschakeld. Soft-match wordt gebruikt om bestaande cloudgebruikers in Azure AD te matchen met on-premises gebruikers.

Als u on-premises AD-accounts moet koppelen aan bestaande accounts die in de cloud zijn gemaakt en u Exchange Online niet gebruikt, is deze functie handig. In dit scenario hebt u over het algemeen geen reden om het SMTP-kenmerk in de cloud in te stellen.

Deze functie is standaard ingeschakeld voor nieuw gemaakte Azure AD-mappen. U zien of deze functie voor u is ingeschakeld door het uitvoeren van:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Als deze functie niet is ingeschakeld voor uw Azure AD-map, u deze inschakelen door het uitvoeren van:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>UserPrincipalName-updates synchroniseren

In het verleden zijn updates van het kenmerk UserPrincipalName met behulp van de synchronisatieservice van on-premises geblokkeerd, tenzij beide voorwaarden waar waren:

* De gebruiker wordt beheerd (niet-gefedereerd).
* Aan de gebruiker is geen licentie toegewezen.

> [!NOTE]
> Vanaf maart 2019 is het synchroniseren van UPN-wijzigingen voor federatieve gebruikersaccounts toegestaan.
> 

Als u deze functie inschakelt, kan de synchronisatieengine de userPrincipalName bijwerken wanneer deze on-premises wordt gewijzigd en u wachtwoordhashsynchronisatie of pass-through-verificatie gebruikt.

Deze functie is standaard ingeschakeld voor nieuw gemaakte Azure AD-mappen. U zien of deze functie voor u is ingeschakeld door het uitvoeren van:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Als deze functie niet is ingeschakeld voor uw Azure AD-map, u deze inschakelen door het uitvoeren van:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Nadat u deze functie hebt ingesloten, blijven de bestaande userPrincipalName-waarden zoals het is. Bij de volgende on-premises wijziging van het kenmerk userPrincipalName wordt de normale deltasynchronisatie voor gebruikers bijgewerkt.  

## <a name="see-also"></a>Zie ook

* [Synchronisatie van Azure AD Connect](how-to-connect-sync-whatis.md)
* [Uw on-premises identiteiten integreren met Azure Active Directory.](whatis-hybrid-identity.md)
