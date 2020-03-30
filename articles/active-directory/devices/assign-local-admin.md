---
title: Lokale beheerders beheren op azure AD-apparaten
description: Meer informatie over het toewijzen van Azure-rollen aan de lokale beheerdersgroep van een Windows-apparaat.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc1812d955590ec0c7372e1311c9d69f93b9957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128882"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>De groep lokale beheerders beheren op Azure AD-apparaten

Als u een Windows-apparaat wilt beheren, moet u lid zijn van de lokale beheerdersgroep. Als onderdeel van het Azure Active Directory -proces (Azure AD) wordt het lidmaatschap van deze groep bijgewerkt op een apparaat. U de lidmaatschapsupdate aanpassen om aan uw zakelijke vereisten te voldoen. Een lidmaatschapsupdate is bijvoorbeeld handig als u uw helpdeskmedewerkers in staat wilt stellen taken uit te voeren waarvoor beheerdersrechten op een apparaat vereist zijn.

In dit artikel wordt uitgelegd hoe de lidmaatschapsupdate werkt en hoe u deze aanpassen tijdens een Azure AD Join. De inhoud van dit artikel is niet van toepassing op een **hybride** Azure AD-lid.

## <a name="how-it-works"></a>Hoe werkt het?

Wanneer u een Windows-apparaat verbindt met Azure AD via een Azure AD-verbinding, voegt Azure AD de volgende beveiligingsprincipes toe aan de groep lokale beheerders op het apparaat:

- De globale ad-beheerdersrol van Azure AD
- De functie Azure AD-apparaatbeheerder 
- De gebruiker die het Azure AD uitvoert, wordt lid   

Door Azure AD-rollen toe te voegen aan de groep lokale beheerders, u de gebruikers bijwerken die een apparaat op elk gewenst moment in Azure AD kunnen beheren zonder iets op het apparaat te wijzigen. Momenteel u geen groepen toewijzen aan een beheerdersrol.
Azure AD voegt ook de azure AD-apparaatbeheerdersrol toe aan de lokale beheerdersgroep om het principe van de minste bevoegdheden (PoLP) te ondersteunen. Naast de globale beheerders u gebruikers die *alleen* de apparaatbeheerdersrol hebben toegewezen, ook inschakelen om een apparaat te beheren. 

## <a name="manage-the-global-administrators-role"></a>De rol globale beheerders beheren

Zie het lidmaatschap van de globale beheerdersrol om het lidmaatschap van de globale beheerdersrol te bekijken en bij te werken:

- [Alle leden van een beheerdersrol weergeven in Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)
- [Beheerdersrollen toewijzen aan gebruikers in Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>De rol apparaatbeheerder beheren 

In de Azure-portal u de functie apparaatbeheerder beheren op de pagina **Apparaten.** Ga als een opening van de pagina **Apparaten:**

1. Meld u aan bij uw [Azure-portal](https://portal.azure.com) als globale beheerder.
1. Zoek naar *Azure Active Directory* en selecteer deze optie.
1. Klik **in** de sectie Beheren op **Apparaten**.
1. Klik **op** de pagina Apparaten op **Apparaatinstellingen**.

Als u de functie apparaatbeheerder wilt wijzigen, configureert u **extra lokale beheerders op Azure AD-apparaten**.  

![Extra lokale beheerders](./media/assign-local-admin/10.png)

>[!NOTE]
> Voor deze optie is een Azure AD Premium-tenant vereist. 

Apparaatbeheerders worden toegewezen aan alle azure AD-apparaten. U apparaatbeheerders niet naar een specifieke set apparaten richten. Het bijwerken van de apparaatbeheerdersrol heeft niet noodzakelijkerwijs een onmiddellijke impact op de getroffen gebruikers. Op apparaten waar een gebruiker al is aangemeld, vindt de privilege-update plaats wanneer *beide* onderstaande acties plaatsvinden:

- Er zijn 4 uur verstreken voor Azure AD om een nieuw primair vernieuwingstoken uit te geven met de juiste bevoegdheden. 
- Gebruiker meldt zich af en meldt zich weer in, niet vergrendelen / ontgrendelen, om hun profiel te vernieuwen.

## <a name="manage-regular-users"></a>Regelmatige gebruikers beheren

Azure AD voegt standaard de gebruiker toe die de Azure AD-join uitvoert aan de beheerdersgroep op het apparaat. Als u wilt voorkomen dat gewone gebruikers lokale beheerders worden, hebt u de volgende opties:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) - Windows Autopilot biedt u een optie om te voorkomen dat primaire gebruiker die de join uitvoert, een lokale beheerder wordt. U dit bereiken door [een Autopilot-profiel aan te maken.](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)
- [Bulkinschrijving](/intune/windows-bulk-enroll) - Een Azure AD-join die wordt uitgevoerd in de context van een bulkinschrijving, vindt plaats in de context van een automatisch gemaakte gebruiker. Gebruikers die zich aanmelden nadat een apparaat is samengevoegd, worden niet toegevoegd aan de groep beheerders.   

## <a name="manually-elevate-a-user-on-a-device"></a>Een gebruiker handmatig verheffen op een apparaat 

Naast het gebruik van het Azure AD-joinproces u een gewone gebruiker ook handmatig verheffen tot lokale beheerder op één specifiek apparaat. Voor deze stap moet u al lid zijn van de lokale beheerdersgroep. 

Vanaf de **Windows 10 1709-release** u deze taak uitvoeren via **Instellingen -> Accounts -> Andere gebruikers**. Selecteer **Een werk- of schoolgebruiker toevoegen,** voer de UPN van de gebruiker in onder **Gebruikersaccount** en selecteer *Beheerder* onder **Accounttype**  
 
Daarnaast u gebruikers ook toevoegen met behulp van de opdrachtprompt:

- Als uw tenantgebruikers zijn gesynchroniseerd vanuit on-premises Active Directory, gebruikt u `net localgroup administrators /add "Contoso\username"`.
- Als uw tenantgebruikers zijn gemaakt in Azure AD, gebruikt u`net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Overwegingen 

U geen groepen toewijzen aan de rol van apparaatbeheerder, alleen individuele gebruikers zijn toegestaan.

Apparaatbeheerders worden toegewezen aan alle Azure AD Joined-apparaten. Ze kunnen niet worden ingesteld op een specifieke set apparaten.

Wanneer u gebruikers uit de functie apparaatbeheerder verwijdert, hebben ze nog steeds de bevoegdheid voor de lokale beheerder op een apparaat, zolang ze zijn aangemeld bij het apparaat. De bevoegdheid wordt ingetrokken tijdens de volgende aanmelding of na 4 uur wanneer een nieuw primair vernieuwingstoken wordt uitgegeven.

## <a name="next-steps"></a>Volgende stappen

- Zie [Apparaten beheren met behulp van de Azure-portal](device-management-azure-portal.md) voor een overzicht van hoe u apparaten in de Azure-portal kunt beheren.
- Zie Azure [Active Directory-beleid voor voorwaardelijke toegang tot apparaten configureren](../conditional-access/require-managed-devices.md)voor meer informatie over voorwaardelijke toegang op basis van apparaten.
