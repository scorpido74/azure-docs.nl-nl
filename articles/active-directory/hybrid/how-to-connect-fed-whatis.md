---
title: Azure AD Connect en federatie | Microsoft Documenten
description: Deze pagina is de centrale locatie voor alle documentatie met betrekking tot AD FS-bewerkingen die Azure AD Connect gebruiken.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0518c58abf156c718ee083ffadb0ef8e0a590252
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331530"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect en federatie
Met Azure Active Directory (Azure AD) Connect u de federatie configureren met on-premises Active Directory Federation Services (AD FS) en Azure AD. Met federatieaanmelding u gebruikers in staat stellen zich aan te melden bij azure AD-services met hun on-premises wachtwoorden - en terwijl u zich in het bedrijfsnetwerk bevindt, zonder dat ze hun wachtwoorden opnieuw hoeven in te voeren. Door de federatieoptie met AD FS te gebruiken, u een nieuwe installatie van AD FS implementeren of u een bestaande installatie opgeven in een Windows Server 2012 R2-farm.

Dit onderwerp is de thuisbasis voor informatie over federatiegerelateerde functionaliteiten voor Azure AD Connect. Het bevat links naar alle gerelateerde onderwerpen. Zie [Uw on-premises identiteiten integreren met Azure Active Directory](whatis-hybrid-identity.md)voor koppelingen naar Azure AD Connect.

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: federatieonderwerpen
| Onderwerp | Wat het dekt en wanneer het te lezen |
|:--- |:--- |
| **Aanmeldingsopties voor Azure AD Connect-gebruikers** | |
| [Gebruikersaanmeldingsopties begrijpen](plan-connect-user-signin.md) |Meer informatie over verschillende aanmeldingsopties voor gebruikers en hoe deze van invloed zijn op de gebruikerservaring van Azure-aanmelding. |
| **AD FS installeren met Azure AD Connect** | |
| [Vereisten](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |Bekijk de vereisten voor een succesvolle AD FS-installatie via Azure AD Connect. |
| [Een AD FS-farm configureren](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Installeer een nieuwe AD FS-farm met Azure AD Connect. |
| [Federate met Azure AD met alternatieve inlog-id](how-to-connect-fed-management.md#alternateid) | Federatie configureren met alternatieve aanmeldings-id  |
| **De AD FS-configuratie wijzigen** | |
| [Herstel het vertrouwen](how-to-connect-fed-management.md#repairthetrust) |Herstel de huidige vertrouwensrelatie tussen on-premises AD FS en Office 365/Azure. |
| [Een nieuwe AD FS-server toevoegen](how-to-connect-fed-management.md#addadfsserver) |Een AD FS-farm uitbreiden met een extra AD FS-server na de eerste installatie. |
| [Een nieuwe WAP-server van AD FS toevoegen](how-to-connect-fed-management.md#addwapserver) |Een AD FS-farm uitbreiden met een extra WAP-server (Web Application Proxy) na de eerste installatie. |
| [Een nieuw federatief domein toevoegen](how-to-connect-fed-management.md#addfeddomain) |Voeg een ander domein toe dat moet worden gefedereerd met Azure AD. |
| [Het TLS/SSL-certificaat bijwerken](how-to-connect-fed-ssl-update.md)| Werk het TLS/SSL-certificaat voor een AD FS-farm bij. |
| [Federatiecertificaten voor Office 365 en Azure AD verlengen](how-to-connect-fed-o365-certs.md)|Uw O365-certificaat vernieuwen met Azure AD.|
| **Andere federatieconfiguratie** | |
| [Meerdere exemplaren van Azure AD federeren met één exemplaar van AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Federen meerdere Azure AD met enkele AD FS-farm| 
| [Een aangepast bedrijfslogo/-illustratie toevoegen](how-to-connect-fed-management.md#customlogo) |Wijzig de aanmeldingservaring door het aangepaste logo op te geven dat wordt weergegeven op de ad fs-aanmeldingspagina. |
| [Een aanmeldingsbeschrijving toevoegen](how-to-connect-fed-management.md#addsignindescription) |Wijzig de aanmeldingsbeschrijving op de aanmeldingspagina van AD FS. |
| [Regels voor AD FS-claimwijzigen](how-to-connect-fed-management.md#modclaims) |Wijzig of voeg claimregels toe in AD FS die overeenkomen met de synchronisatieconfiguratie van Azure AD Connect. |


## <a name="additional-resources"></a>Aanvullende bronnen
* [Twee Azure AD-berichten met één AD FS federating](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [AD FS-implementatie in Azure](how-to-connect-fed-azure-adfs.md)
* [Cross-geografische AD FS-implementatie met hoge beschikbaarheid in Azure met Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
