---
title: Handtekeninghash-algoritme wijzigen voor vertrouwensrelatie met Office 365-relying party - Azure
description: Op deze pagina vindt u richtlijnen voor het wijzigen van sha-algoritme voor federatievertrouwensrelatie met Office 365
keywords: SHA1, SHA256,O365,federatie,aadconnect,adfs,ad fs, verander sha, federatie vertrouwen, vertrouwen partij vertrouwen
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2233b434fda628dcf812a62f06541fc4b0296aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897349"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Handtekeninghash-algoritme wijzigen voor vertrouwensrelatie met Office 365-relying party
## <a name="overview"></a>Overzicht
Active Directory Federation Services (AD FS) ondertekent de tokens bij Microsoft Azure Active Directory om te controleren of er niet mee wordt geknoeid. Deze handtekening kan gebaseerd zijn op SHA1 of SHA256. Azure Active Directory ondersteunt nu tokens die zijn ondertekend met een SHA256-algoritme en we raden u aan het tokenondertekenende algoritme in te stellen op SHA256 voor het hoogste beveiligingsniveau. In dit artikel worden de stappen beschreven die nodig zijn om het token-ondertekeningsalgoritme in te stellen op het veiligere SHA256-niveau.

>[!NOTE]
>Microsoft raadt het gebruik van SHA256 aan als het algoritme voor het ondertekenen van tokens, omdat het veiliger is dan SHA1, maar SHA1 blijft nog steeds een ondersteunde optie.

## <a name="change-the-token-signing-algorithm"></a>Het token-ondertekeningsalgoritme wijzigen
Nadat u het handtekeningalgoritme hebt ingesteld met een van de twee onderstaande processen, ondertekent AD FS de tokens voor Office 365 vertrouwen d.m.v. sha256. U hoeft geen extra configuratiewijzigingen aan te brengen en deze wijziging heeft geen invloed op uw mogelijkheid om toegang te krijgen tot Office 365 of andere Azure AD-toepassingen.

### <a name="ad-fs-management-console"></a>AD FS-beheerconsole
1. Open de AD FS-beheerconsole op de primaire AD FS-server.
2. Vouw het AD FS-knooppunt uit en klik op **Vertrouwensrelaties van de groep**vertrouwen .
3. Klik met de rechtermuisknop op de vertrouwensrelatie tussen uw Office 365/Azure-relying party en selecteer **Eigenschappen**.
4. Selecteer het tabblad **Geavanceerd** en selecteer het beveiligde hash-algoritme SHA256.
5. Klik op **OK**.

![SHA256 ondertekening algoritme - MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>AD FS PowerShell-cmdlets
1. Open PowerShell op elke AD FS-server onder beheerdersbevoegdheden.
2. Stel het beveiligde hash-algoritme in met de **cmdlet Set-AdfsRelyingPartyTrust.**
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Lees ook
* [Office 365-vertrouwensrelatie herstellen met Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

