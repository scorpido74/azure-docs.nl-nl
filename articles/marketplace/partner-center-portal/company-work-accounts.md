---
title: Bedrijfswerkaccounts en Partnercenter
description: Controleren of uw bedrijf een werkaccount heeft ingesteld bij Microsoft, een nieuw werkaccount maken of meerdere werkaccounts instellen om te gebruiken met Partner Center.
author: dsindona
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: c4e7427d87c5f88d8c686b867ef88ceb05f28286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281422"
---
# <a name="company-work-accounts-and-partner-center"></a>Bedrijfswerkaccounts en Partnercenter

Partnercentrum gebruikt bedrijfswerkaccounts, ook wel AD-tenants (Azure Active Directory) genoemd, om accounttoegang voor meerdere gebruikers te beheren, machtigingen, hostgroepen en toepassingen te beheren en profielgegevens te onderhouden. Door het domein van uw bedrijfse-mailaccount te koppelen aan uw Partner Center-account, kunnen werknemers van uw bedrijf zich aanmelden bij Partner Center om marktplaatsaanbiedingen te beheren met behulp van hun eigen gebruikersnamen en wachtwoorden voor werkaccounts.

## <a name="check-whether-your-company-already-has-a-work-account"></a>Controleren of uw bedrijf al een werkaccount heeft

Als uw bedrijf zich heeft geabonneerd op een Microsoft-cloudservice zoals Azure, Microsoft Intune of Office 365, hebt u al een domein voor werke-mailaccount (ook wel Azure Active Directory-tenant genoemd) dat kan worden gebruikt met Partner Center.

Volg de volgende stappen om te controleren:
1. Meld u aan bij https://portal.azure.comde Azure-beheerportal op .
2. Selecteer **Azure Active Directory** in het menu links-navigatie en selecteer aangepaste **domeinnamen**.
3. Als u al een werkaccount hebt, wordt uw domeinnaam weergegeven.

Als uw bedrijf nog geen werkaccount heeft, wordt er een voor u gemaakt tijdens het inschrijvingsproces van het Partnercenter.

## <a name="set-up-multiple-work-accounts"></a>Meerdere werkaccounts instellen

Voordat u besluit een bestaand werkaccount te gebruiken, moet u overwegen hoeveel gebruikers in het werkaccount toegang moeten krijgen tot Partner Center. Als u gebruikers in het werkaccount hebt die geen toegang hoeven te krijgen tot Partner Center, u overwegen meerdere werkaccounts te maken, zodat alleen gebruikers die toegang moeten krijgen tot partnercentrum, op een bepaald account worden weergegeven.

## <a name="create-a-new-work-account"></a>Een nieuw werkaccount maken

Volg de onderstaande stappen om een nieuw werkaccount voor uw bedrijf aan te maken. Mogelijk moet u hulp aanvragen bij degene die beheerdersmachtigingen heeft voor het Microsoft Azure-account van uw bedrijf.

1. Meld u aan bij de [Microsoft Azure-portal](https://portal.azure.com).
2. Selecteer in het linkernavigatiemenu de **Azure Active Directory** -> **Users**.
3. Selecteer **Nieuwe gebruiker** en maak een nieuw Azure-werkaccount door een naam en e-mailadres in te voeren. Controleer of de **maprol** is ingesteld op **Gebruiker** en schakel het selectievakje **Wachtwoord weergeven** onderaan in om het automatisch gegenereerde wachtwoord weer te geven en te noteren.
4. Selecteer **Maken** om de nieuwe gebruiker op te slaan.

Het e-mailadres van het gebruikersaccount moet een geverifieerde domeinnaam in uw map zijn. U alle geverifieerde domeinen in uw map weergeven door **Azure Active Directory** -> **Custom-domeinnamen** te selecteren in het menu links-navigatie.

Zie [Een domein toevoegen of koppelen in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain)voor meer informatie over het toevoegen van aangepaste domeinen in Azure Active Directory.

## <a name="troubleshoot-work-email-sign-in"></a>Aanmelden voor werke-mail oplossen

Als u zich niet aanmelden bij uw werkaccount (ook wel uw Azure AD-tenant genoemd), vindt u het scenario in het onderstaande diagram dat het beste overeenkomt met uw situatie en volgt u de aanbevolen stappen.

![Diagram voor het aanmelden van werkaccountproblemen](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>Volgende stappen

- [Uw Commerciële Marketplace-account beheren in partnercentrum](./manage-account.md) 
