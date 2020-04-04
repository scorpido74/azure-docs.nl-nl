---
title: Veelgestelde vragen over meerdere sessies van Windows 10 Enterprise - Azure
description: Veelgestelde vragen en aanbevolen procedures voor het gebruik van Windows 10 Enterprise multisessie voor Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d7ea99e2ee8e2882c211ee17acec70222dc058a8
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637088"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Veelgestelde vragen over meervoudige Windows 10 Enterprise-sessies

In dit artikel worden veelgestelde vragen beantwoord en worden aanbevolen procedures voor Windows 10 Enterprise-sessies uitgelegd.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Wat is Windows 10 Enterprise-multisessie?

Windows 10 Enterprise multi-session, voorheen bekend als Windows 10 Enterprise for Virtual Desktops (EVD), is een nieuwe Remote Desktop Session Host die meerdere gelijktijdige interactieve sessies mogelijk maakt. Voorheen kon alleen Windows Server dit doen. Deze mogelijkheid biedt gebruikers een vertrouwde Windows 10-ervaring, terwijl IT kan profiteren van de kostenvoordelen van multi-sessie en bestaande Windows-licenties per gebruiker kunnen gebruiken in plaats van RDS Client Access-licenties (CAL's). Zie [Windows Virtual Desktop-prijzen](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor meer informatie over licenties en prijzen. 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Hoeveel gebruikers kunnen tegelijkertijd een interactieve sessie hebben op Windows 10 Enterprise multisessie?

Hoeveel interactieve sessies die tegelijkertijd actief kunnen zijn, zijn afhankelijk van de hardwarebronnen van uw systeem (vCPU, geheugen, schijf en vGPU), hoe uw gebruikers hun apps gebruiken terwijl ze zijn aangemeld bij een sessie en hoe zwaar de werkbelasting van uw systeem is. We raden u aan de prestaties van uw systeem te valideren om te begrijpen hoeveel gebruikers u hebben op Windows 10 Enterprise-multisessie. Zie [Windows Virtual Desktop-prijzen](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor meer informatie. 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Waarom rapporteert mijn toepassing Windows 10 Enterprise-multisessie als een serverbesturingssysteem?

Windows 10 Enterprise multi-session is een virtuele editie van Windows 10 Enterprise. Een van de verschillen is dat dit besturingssysteem (OS) meldt dat de [ProductType](/windows/win32/cimwin32prov/win32-operatingsystem) een waarde heeft van 3, dezelfde waarde als Windows Server. Deze eigenschap houdt het besturingssysteem compatibel met bestaande RDSH-beheertooling, RDSH-toepassingen met meerdere sessies en meestal optimalisaties voor systeemprestaties op laag niveau voor RDSH-omgevingen. Sommige installatieprogramma's kunnen de installatie op Windows 10-sessies blokkeren, afhankelijk van of ze detecteren of de ProductType is ingesteld op Client. Als uw app niet wordt geïnstalleerd, neemt u contact op met de leverancier van de toepassing voor een bijgewerkte versie. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Kan ik Windows 10 Enterprise-multisessie on-premises uitvoeren?

Windows 10 Enterprise-sessies kunnen niet worden uitgevoerd in on-premises productieomgevingen omdat deze is geoptimaliseerd voor de Windows Virtual Desktop-service voor Azure. Het is in strijd met de licentieovereenkomst voor het uitvoeren van Windows 10 Enterprise-multisessie buiten Azure voor productiedoeleinden. Windows 10 Enterprise-multisessie wordt niet geactiveerd tegen on-premises Key Management Services (KMS).
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Hoe pas ik de windows 10 Enterprise-afbeelding voor meerdere sessies aan voor mijn organisatie?

U een virtuele machine (VM) starten in Azure met Windows 10 Windows 10 Enterprise-multisessie en deze aanpassen door LOB-toepassingen te installeren, sysprep/generaliseren en vervolgens een afbeelding te maken met behulp van de Azure-portal.  
 
Maak om aan de slag te gaan een VM in Azure met Windows 10 Enterprise-multisessie. In plaats van de VM in Azure te starten, u de VHD rechtstreeks downloaden. Daarna u de VHD die u hebt gedownload gebruiken om een nieuwe Generatie 1 VM te maken op een Windows 10-pc met Hyper-V ingeschakeld.

Pas de afbeelding aan uw behoeften aan door LOB-toepassingen te installeren en de afbeelding te sysprepen. Wanneer u klaar bent met aanpassen, uploadt u de afbeelding naar Azure met de VHD erin. Haal daarna Windows Virtual Desktop uit de Azure Marketplace en gebruik deze om een nieuwe hostpool met de aangepaste afbeelding te implementeren.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Hoe beheer ik windows 10 Enterprise-multisessie na implementatie?

U elk ondersteund configuratiehulpprogramma gebruiken, maar we raden Configuratiebeheer versie 1906 aan omdat het Windows 10 Enterprise-multisessie ondersteunt. We werken momenteel aan Microsoft Intune-ondersteuning.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Kan Windows 10 Enterprise meerdere sessies worden Azure Active Directory (AD)-toegetreden?

Windows 10 Enterprise-multisessie wordt momenteel ondersteund om hybride Azure AD-lid te zijn. Nadat windows 10 Enterprise-multisessie is samengevoegd met een domein, gebruikt u het bestaande groepsbeleidsobject om Azure AD-registratie in te schakelen. Zie [Uw hybride Azure Active Directory join-implementatie plannen](../active-directory/devices/hybrid-azuread-join-plan.md)voor meer informatie.
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Waar vind ik de windows 10 Enterprise multi-session afbeelding?

Windows 10 Enterprise-sessie bevindt zich in de Azure-galerie. Als u deze wilt vinden, navigeert u naar de Azure-portal en zoekt u naar de release van Windows 10 Enterprise voor virtuele desktops. Ga voor een afbeelding die is geïntegreerd met Office Pro Plus naar de Azure-portal en zoek naar Microsoft Windows 10 + Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Welke Windows 10 Enterprise-afbeelding met meerdere sessies moet ik gebruiken?

De Azure-galerie heeft verschillende releases, waaronder Windows 10 Enterprise-multisessie, versie 1809 en Windows 10 Enterprise- multisessieversie versie 1903. We raden u aan de nieuwste versie te gebruiken voor betere prestaties en betrouwbaarheid.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Welke Windows 10 Enterprise multi-session versies worden ondersteund?

Windows 10 Enterprise-sessies, versies 1809 en hoger worden ondersteund en zijn beschikbaar in de Azure-galerie. Deze releases volgen dezelfde ondersteuning levenscyclus beleid als Windows 10 Enterprise, wat betekent dat de lente release wordt ondersteund voor 18 maanden en de herfst release voor 30 maanden.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Welke profielbeheeroplossing moet ik gebruiken voor Windows 10 Enterprise-multisessie?

We raden u aan FSLogix-profielcontainers te gebruiken wanneer u Windows 10 Enterprise configureert in niet-permanente omgevingen of andere scenario's die een centraal opgeslagen profiel nodig hebben. FSLogix zorgt ervoor dat het gebruikersprofiel beschikbaar en up-to-date is voor elke gebruikerssessie. We raden u ook aan uw FSLogix-profielcontainer te gebruiken om een gebruikersprofiel op te slaan in een SMB-share met de juiste machtigingen, maar u gebruikersprofielen opslaan in Azure-paginablobopslag indien nodig. Windows Virtual Desktop-gebruikers kunnen FSLogix zonder extra kosten gebruiken.
 
Zie [De profielcontainer FSLogix configureren](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)voor meer informatie over het configureren van een FSLogix-profielcontainer.  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Welke licentie heb ik nodig om toegang te krijgen tot Windows 10 Enterprise multisessie?

Zie [Windows Virtual Desktop-prijzen](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor een volledige lijst met toepasselijke licenties.

## <a name="why-do-my-apps-disappear-after-i-sign-out"></a>Waarom verdwijnen mijn apps nadat ik me heb afmelden?

Dit gebeurt omdat u Windows 10 Enterprise multisessie gebruikt met een oplossing voor profielbeheer zoals FSLogix. Uw beheerder of profieloplossing configureerde uw systeem om gebruikersprofielen te verwijderen wanneer gebruikers zich afmelden. Deze configuratie betekent dat wanneer uw systeem uw gebruikersprofiel verwijdert nadat u zich hebt afmelden, het ook alle apps verwijdert die u tijdens uw sessie hebt geïnstalleerd. Als u de apps die u hebt geïnstalleerd wilt behouden, moet u uw beheerder vragen deze apps in te richten voor alle gebruikers in uw Windows Virtual Desktop-omgeving.

## <a name="how-do-i-make-sure-apps-dont-disappear-when-users-sign-out"></a>Hoe zorg ik ervoor dat apps niet verdwijnen wanneer gebruikers zich afmelden?

De meeste gevirtualiseerde omgevingen zijn standaard geconfigureerd om te voorkomen dat gebruikers extra apps installeren op hun profielen. Als u ervoor wilt zorgen dat een app niet verdwijnt wanneer uw gebruiker zich afmeldt bij Windows Virtual Desktop, moet u die app inrichten voor alle gebruikersprofielen in uw omgeving. Raadpleeg de volgende bronnen voor meer informatie over het inrichten van apps:

- [Ingebouwde apps publiceren in Windows Virtual Desktop](publish-apps.md)
- [Opties voor het onderhouden van opdrachtregelopties voor DISM-apps](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-app-package--appx-or-appxbundle--servicing-command-line-options)
- [Add-AppxProvisionedPackage](https://docs.microsoft.com/powershell/module/dism/add-appxprovisionedpackage?view=win10-ps)

## <a name="how-do-i-make-sure-users-dont-download-and-install-apps-from-the-microsoft-store"></a>Hoe zorg ik ervoor dat gebruikers geen apps downloaden en installeren vanuit de Microsoft Store?

U de Microsoft Store-app uitschakelen om ervoor te zorgen dat gebruikers geen extra apps downloaden dan de apps die u al voor hen hebt ingericht.

Ga als een andere eigenaar van de Store-app:

1. Maak een nieuw groepsbeleid.
2. Selecteer**Systeembeheersjablonen voor** >  **computerconfiguratie** > **.**
3. Selecteer **Winkel**.
4. Selecteer **Winkeltoepassing**.
5. Selecteer **Uitgeschakeld**en selecteer **OK**.
6. Selecteer **Toepassen**.
 
## <a name="next-steps"></a>Volgende stappen

Ga als verkenner naar meer informatie over Windows Virtual Desktop en Windows 10 Enterprise:

- Lees onze [Windows Virtual Desktop Preview-documentatie](overview.md)
- Bezoek onze [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Uw Windows Virtual Desktop-implementatie instellen met de [zelfstudies van Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
