---
title: Veelgestelde vragen over Windows 10 Enter prise multi-session-Azure
description: Veelgestelde vragen en aanbevolen procedures voor het gebruik van Windows 10 Enter prise multi-session voor virtueel bureau blad van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: helohr
ms.openlocfilehash: e2fa30772082f4d2f7c02add61412432233e3f04
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470569"
---
# <a name="windows-10-enterprise-multi-session-faq"></a>Veelgestelde vragen over meervoudige Windows 10 Enterprise-sessies

In dit artikel worden veelgestelde vragen beantwoord en best practices voor Windows 10 Enter prise multi-session besproken.
 
## <a name="what-is-windows-10-enterprise-multi-session"></a>Wat is Windows 10 Enter prise multi-session? 

Windows 10 Enter prise multi-session, voorheen bekend als Windows 10 Enter prise voor Virtual desktops (EVD), is een nieuwe Extern bureaublad-sessiehost waarmee meerdere gelijktijdige interactieve sessies kunnen worden uitgevoerd. Met deze mogelijkheid hebben gebruikers een bekende Windows 10-ervaring, terwijl ze kunnen profiteren van de kosten voordelen van meerdere sessies en bestaande Windows-licenties per gebruiker gebruiken in plaats van RDS-licenties voor client toegang. Zie [prijzen voor Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor meer informatie over licenties en prijzen. 
 
## <a name="how-many-users-can-simultaneously-have-an-interactive-session-on-windows-10-enterprise-multi-session"></a>Hoeveel gebruikers kunnen tegelijkertijd een interactieve sessie op Windows 10 Enter prise Multi-Session hebben?

Hoeveel interactieve sessies tegelijk actief kunnen zijn, is afhankelijk van de hardwarebronnen van uw systeem (vCPU, geheugen, schijf en vGPU), hoe uw gebruikers hun apps gebruiken terwijl ze zich aanmelden bij een sessie en hoe zwaar de werk belasting van uw systeem is. We raden u aan om de prestaties van uw systeem te valideren om inzicht te krijgen in het aantal gebruikers dat u kunt hebben met Windows 10 Enter prise multi-session. Zie [prijzen voor Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor meer informatie. 
 
## <a name="why-does-my-application-report-windows-10-enterprise-multi-session-as-a-server-operating-system"></a>Waarom rapporteert mijn toepassing Windows 10 Enter prise multi-session als een server besturingssysteem?

Windows 10 Enter prise multi-session is een virtuele editie van Windows 10 Enter prise. Een van de verschillen is dat dit besturings systeem (OS) de [product type](/windows/win32/cimwin32prov/win32-operatingsystem) rapporteert als een waarde van 3, dezelfde waarde als Windows Server. Met deze eigenschap blijft het besturings systeem compatibel met bestaande hulpprogram ma's voor het uitvoeren van RDSH-beheer, toepassingen met meerdere sessies op basis van RDSH en voornamelijk systeem prestatie optimalisaties op laag niveau voor RDSH-omgevingen. Sommige installatie Programma's van toepassingen kunnen de installatie van Windows 10 multi-session blok keren, afhankelijk van of ze de product type detecteren, is ingesteld op client. Als uw app niet kan worden geïnstalleerd, neemt u contact op met de leverancier van de toepassing voor een bijgewerkte versie. 
 
## <a name="can-i-run-windows-10-enterprise-multi-session-on-premises"></a>Kan ik on-premises Windows 10 Enter prise multi-session uitvoeren?

Windows 10 Enter prise multi-session kan niet worden uitgevoerd in een on-premises productie omgeving omdat het is geoptimaliseerd voor de virtueel-bureaublad service van Windows voor Azure. U kunt de gebruiksrecht overeenkomst gebruiken om een multi-sessie van Windows 10 Enter prise uit te voeren buiten Azure voor productie doeleinden. Windows 10 Enter prise multi-session kan niet worden geactiveerd op basis van on-premises Key Management Services (KMS).
 
## <a name="how-do-i-customize-the-windows-10-enterprise-multi-session-image-for-my-organization"></a>Hoe kan ik de Windows 10 Enter prise-installatie kopie voor meerdere sessies aanpassen voor mijn organisatie?

U kunt een virtuele machine (VM) in azure starten met Windows 10 Windows 10 Enter prise Multi-Session en deze aanpassen door LOB-toepassingen, sysprep/generalize te installeren en vervolgens een installatie kopie te maken met behulp van de Azure Portal.  
 
Als u aan de slag wilt gaan, maakt u een virtuele machine in azure met Windows 10 Enter prise multi-session. In plaats van de virtuele machine in azure te starten, kunt u de VHD rechtstreeks downloaden. Daarna kunt u de VHD die u hebt gedownload gebruiken om een nieuwe generatie 1 VM te maken op een Windows 10-computer waarop Hyper-V is ingeschakeld.

Pas de installatie kopie aan uw behoeften aan door LOB-toepassingen te installeren en Sysprep uit te voeren op de installatie kopie. Wanneer u klaar bent met aanpassen, uploadt u de installatie kopie naar Azure met de VHD in. Daarna kunt u Windows virtueel bureau blad ophalen van Azure Marketplace en gebruiken om een nieuwe hostgroep te implementeren met de aangepaste installatie kopie.
 
## <a name="how-do-i-manage-windows-10-enterprise-multi-session-after-deployment"></a>Hoe kan ik Windows 10 Enter prise multi-session beheren na de implementatie?

U kunt elk ondersteund configuratie programma gebruiken, maar we raden Configuration Manager versie 1906 aan omdat deze Windows 10 Enter prise multi-session ondersteunt. Er wordt momenteel gewerkt aan Microsoft Intune ondersteuning.
 
## <a name="can-windows-10-enterprise-multi-session-be-azure-active-directory-ad-joined"></a>Kan Windows 10 Enter prise Multi-Session worden Azure Active Directory (AD)-gekoppeld?

Windows 10 Enter prise multi-session wordt momenteel ondersteund als hybride Azure AD-join. Nadat Windows 10 Enter prise multi-session is toegevoegd aan een domein, gebruikt u het bestaande groepsbeleid-object om Azure AD-registratie in te scha kelen. Zie voor meer informatie [de implementatie van hybride Azure Active Directory-deelname plannen](../active-directory/devices/hybrid-azuread-join-plan.md).
 
## <a name="where-can-i-find-the-windows-10-enterprise-multi-session-image"></a>Waar vind ik de Windows 10 Enter prise-installatie kopie voor meerdere sessies?

Windows 10 Enter prise multi-session bevindt zich in de Azure Gallery. Om het te vinden, gaat u naar de Azure Portal en zoekt u naar de release van Windows 10 Enter prise voor virtuele Bureau bladen. Voor een installatie kopie die is geïntegreerd met Office Pro Plus, gaat u naar de Azure Portal en zoekt u naar micro soft Windows 10 + Office 365 ProPlus.

## <a name="which-windows-10-enterprise-multi-session-image-should-i-use"></a>Welke Windows 10 Enter prise-afbeelding voor meerdere sessies moet ik gebruiken?

De Azure Gallery bevat verschillende versies, waaronder Windows 10 Enter prise multi-session, versie 1809 en Windows 10 Enter prise multi-session, versie 1903. U kunt het beste de meest recente versie gebruiken voor verbeterde prestaties en betrouw baarheid.
 
## <a name="which-windows-10-enterprise-multi-session-versions-are-supported"></a>Welke versies van Windows 10 Enter prise met meerdere sessies worden ondersteund?

Windows 10 Enter prise meerdere sessies, versie 1809 en hoger worden ondersteund en zijn beschikbaar in de Azure Gallery. Deze releases volgen hetzelfde levenscyclus beleid voor de levens cyclus als Windows 10 Enter prise. Dit betekent dat de lente release gedurende 18 maanden en de herfst-release 30 maanden wordt ondersteund.
 
## <a name="which-profile-management-solution-should-i-use-for-windows-10-enterprise-multi-session"></a>Welke oplossing voor Profiel beheer moet ik gebruiken voor meerdere sessies van Windows 10 Enter prise?

We raden u aan om FSLogix-profiel containers te gebruiken wanneer u Windows 10 Enter prise configureert in niet-permanente omgevingen of in andere scenario's waarvoor een centraal opgeslagen profiel nodig is. FSLogix zorgt ervoor dat het gebruikers profiel beschikbaar is en up-to-date is voor elke gebruikers sessie. We raden u ook aan uw FSLogix-profiel container te gebruiken om een gebruikers profiel op te slaan in een SMB-share met de juiste machtigingen, maar u kunt indien nodig gebruikers profielen opslaan in een Azure-pagina-Blob-opslag. Windows-gebruikers van virtueel bureau blad kunnen FSLogix zonder extra kosten gebruiken.
 
Zie [Configure the FSLogix profile container](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)(Engelstalig) voor meer informatie over het configureren van een FSLogix-profiel container.  

## <a name="which-license-do-i-need-to-access-windows-10-enterprise-multi-session"></a>Welke licentie heb ik nodig om toegang te krijgen tot de multi-sessie van Windows 10 Enter prise?

Zie [prijzen voor Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor een volledige lijst van toepasselijke licenties.
 
## <a name="next-steps"></a>Volgende stappen

Meer informatie over Windows Virtual Desktop en Windows 10 Enter prise multi-session:

- Lees de [documentatie voor Windows Virtual Desktop Preview](overview.md)
- Bezoek onze [Windows-TechCommunity voor virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Uw Windows-implementatie voor virtueel bureau blad instellen met de [zelf studies voor virtueel Windows-bureau blad](tenant-setup-azure-active-directory.md)
