---
title: Toegang tot Azure-beheer beheren met voorwaardelijke toegang in Azure AD
description: Meer informatie over het gebruik van Voorwaardelijke toegang in Azure AD om toegang tot Azure-beheer te beheren.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137413"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Toegang tot Azure-beheer beheren met voorwaardelijke toegang

> [!CAUTION]
> Zorg ervoor dat u begrijpt hoe Voorwaardelijke toegang werkt voordat u een beleid instelt om de toegang tot Azure-beheer te beheren. Zorg ervoor dat u geen voorwaarden maakt die uw eigen toegang tot de portal kunnen blokkeren.

Voorwaardelijke toegang in Azure Active Directory (Azure AD) regelt de toegang tot cloud-apps op basis van specifieke voorwaarden die u opgeeft. Als u toegang wilt verlenen, maakt u beleid voor voorwaardelijke toegang dat toegang toestaat of blokkeert op basis van de vraag of aan de vereisten in het beleid is voldaan. 

Normaal gesproken gebruikt u Voorwaardelijke toegang om de toegang tot uw cloud-apps te beheren. U ook beleidsregels instellen om de toegang tot Azure-beheer te beheren op basis van bepaalde voorwaarden (zoals aanmeldingsrisico, locatie of apparaat) en om vereisten zoals meervoudige verificatie af te dwingen.

Als u een beleid voor Azure-beheer wilt maken, selecteert u **Microsoft Azure Management** onder **Cloud-apps** wanneer u de app kiest waarop u het beleid wilt toepassen.

![Voorwaardelijke toegang voor Azure-beheer](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Het beleid dat u maakt, is van toepassing op alle azure-beheereindpunten, waaronder het volgende:

- Azure Portal
- Azure Resource Manager-provider
- Klassieke API's voor servicebeheer
- Azure PowerShell
- Beheerdersportal voor Visual Studio-abonnementen
- Azure DevOps
- Azure Data Factory-portal

Houd er rekening mee dat het beleid van toepassing is op Azure PowerShell, dat de Azure Resource Manager API aanroept. Het is niet van toepassing op [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), dat Microsoft Graph aanroept.


Zie [Voorwaardelijke toegang in Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md)voor meer informatie over het instellen en gebruiken van voorwaardelijke toegang.
