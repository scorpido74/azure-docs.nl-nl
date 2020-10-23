---
title: SSH-verificatie met Azure Active Directory
description: Architectuur richtlijnen voor het realiseren van SSH-integratie met Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb766150339820f9356fe94311cd1ff33dda5480
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462882"
---
# <a name="ssh"></a>SSH  

Secure Shell (SSH) is een netwerk protocol dat versleuteling biedt voor het veilig uitvoeren van netwerk services via een niet-beveiligd netwerk. SSH biedt ook een opdracht regel aanmelden, voert externe opdrachten uit en brengt bestanden veilig over. Dit wordt vaak gebruikt in UNIX-systemen zoals Linux®. SSH vervangt het Telnet-protocol, dat geen versleuteling biedt in een niet-beveiligd netwerk. 

Azure Active Directory (Azure AD) biedt een extensie van een virtuele machine (VM) voor Linux-® systemen die worden uitgevoerd op Azure. 

## <a name="use-when"></a>Wanneer gebruiken 

* Werken met op Linux® gebaseerde Vm's waarvoor externe aanmelding is vereist

* Externe opdrachten uitvoeren in Linux®-gebaseerde systemen

* Bestanden in een niet-beveiligd netwerk veilig overdragen

![diagram van Azure AD met SSH-protocol](./media/authentication-patterns/ssh-auth.png)

SSH met Azure AD

## <a name="components-of-system"></a>Onderdelen van systeem 

* **Gebruiker**: start SSH-client om een verbinding in te stellen met de Linux® vm's en biedt referenties voor verificatie.

* **Webbrowser**: het onderdeel waarmee de gebruiker communiceert. De service communiceert met de ID-provider (Azure AD) om de gebruiker veilig te verifiëren en te autoriseren.

* **SSH-client**: verstuurt het installatie proces van de verbinding.

* **Azure AD**: verifieert de identiteit van de gebruiker met behulp van de apparaats stroom en geeft een token uit aan de Linux-vm's.

* **Virtuele Linux-machine**: Hiermee wordt token geaccepteerd en is de verbinding geslaagd.

## <a name="implement-ssh-with-azure-ad"></a>SSH implementeren met Azure AD 

* [Meld u aan bij een Linux® VM met Azure Active Directory referenties-Azure Virtual Machines ](https://docs.microsoft.com/azure/virtual-machines/linux/login-using-aad) 

* [Code Ring OAuth 2,0-apparaat-micro soft Identity-platform ](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)

* [Integreren met Azure Active Directory (akamai.com)](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

 
