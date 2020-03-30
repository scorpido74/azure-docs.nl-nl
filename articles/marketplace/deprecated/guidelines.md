---
title: Richtlijnen voor Azure Marketplace- en AppSource-uitgever | Azure
description: Richtlijnen voor Azure Marketplace en AppSource voor app- en service-uitgevers
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: dsindona
ms.openlocfilehash: 9156126ff2a3dbe936dc426c6f5097c6bba7b7b7
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387599"
---
# <a name="guidelines"></a>Richtlijnen  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Richtlijnen voor Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Richtlijnen voor het maken van een Microsoft ID voor het beheren van een marktplaatsaccount  
Als meer dan één persoon toegang nodig heeft tot dezelfde Microsoft-id die wordt gebruikt om uw marketplace-account te maken, moet u deze richtlijnen volgen om u te helpen een bedrijfsaccount aan te maken. 

>[!IMPORTANT]
>Als u meerdere gebruikers wilt autoriseren om toegang te krijgen tot uw Microsoft Developer Center-account (Dev Center), raadt Microsoft u aan Azure Active Directory (Azure AD) te gebruiken om rollen toe te wijzen aan individuele gebruikers. Elke gebruiker moet toegang krijgen tot het account door zich aan te melden met afzonderlijke Azure AD-referenties. Maak uw Microsoft-id met behulp van een e-mailadres in een domein dat is geregistreerd bij uw bedrijf Microsoft stelt voor om de e-mail niet aan een persoon te toegewezen. Een voorbeeld is `windowsapps@fabrikam.com`.  
>*   Ga voor meer informatie naar Issue: Microsoft ID in een azure [AD-sectie voor een federatief domein.](#issue-microsoft-id-in-an-azure-ad-federated-domain)  

*   Beperk de toegang tot de Microsoft ID tot een zo klein mogelijk aantal ontwikkelaars. 
*   Stel een zakelijke e-maildistributielijst (DL) in met iedereen die toegang moet krijgen tot uw Dev Center-account. Voeg het DL-e-mailadres toe aan uw beveiligingsgegevens. Met de DL kunnen alle werknemers in de lijst desgevraagd beveiligingscodes ontvangen en de beveiligingsgegevens voor uw Microsoft ID beheren. Als het opzetten van een distributielijst niet haalbaar is, moet de eigenaar van het afzonderlijke e-mailaccount beschikbaar zijn om de beveiligingscode te openen en te delen wanneer daarom wordt gevraagd.  
    *   De eigenaar wordt bijvoorbeeld gevraagd wanneer nieuwe beveiligingsgegevens worden toegevoegd aan de Microsoft-id of wanneer de Microsoft ID wordt geopend vanaf een nieuw apparaat.  
*   Voeg een bedrijfstelefoonnummer toe waarvoor geen extensie nodig is en toegankelijk is voor belangrijke teamleden.  
*   In het algemeen moet u vereisen dat ontwikkelaars vertrouwde apparaten gebruiken om zich aan te melden bij uw Dev Center-account. Alle belangrijke teamleden moeten toegang hebben tot de vertrouwde apparaten. Het gebruik van vertrouwde apparaten om toegang te krijgen vermindert de vereiste voor het verzenden van beveiligingscodes wanneer iemand toegang heeft tot het Dev Center-account.  
*   Als u toegang moet verlenen tot het Dev Center-account vanaf een niet-vertrouwde computer, moet u de toegang beperken tot niet meer dan vijf ontwikkelaars. Idealiter moeten uw ontwikkelaars toegang krijgen tot het account vanaf computers die dezelfde geografische en netwerklocatie hebben.  
*   Controleer en verifieer regelmatig uw beveiligingsgegevens.  
    *   Als u uw beveiligingsgegevens wilt bekijken, gaat u naar de pagina Beveiligingsinstellingen op [account.live.com/proofs/Manage.](https://account.live.com/proofs/Manage)

Uw Dev Center-account moet voornamelijk worden geopend vanaf vertrouwde computers. Het is van cruciaal belang dat u toegang hebt vanaf vertrouwde computers, omdat er een limiet is aan het aantal codes dat per Dev Center-account per week wordt gegenereerd. Het gebruik van vertrouwde computers maakt ook de meest veilige en consistente aanmeldingservaring mogelijk. 
*   Ga voor meer informatie over aanvullende dev Center-accountrichtlijnen en -beveiliging naar de pagina Een ontwikkelaarsaccount openen op [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Probleem: Microsoft ID in een Azure AD-federatief domein  
Uw bedrijfsaccount kan worden gefedereerd via Azure Active Directory (Azure AD). Als u een Microsoft ID probeert te maken met een bedrijfse-mailadres dat is gefedereerd met Azure AD, ontvangt u een foutmelding. Als u een fout ontvangt, moet u contact opnemen met uw IT-team om te bevestigen dat uw account wordt gefedereerd via Azure AD. Azure AD federatieve e-mail is een bekend probleem en Microsoft werkt aan het oplossen van het.  
*   Ga voor meer informatie over Azure AD naar de pagina Azure Active Directory Documentation op [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Microsoft raadt een tijdelijke oplossing aan. Volg deze stappen om een nieuw `outlook.com` e-mailadres in het domein te maken en maak een regel om uw communicatie door te sturen.  
1.  Ga naar de pagina Account maken en klik op de koppeling Een nieuw e-mailadres ontvangen. 
    *   Als u zich wilt aanmelden voor uw Microsoft ID, gaat u naar de pagina Account maken op [signup.live.com/signup](https://signup.live.com/signup).  
2.  Maak het nieuwe e-mailadres en voer een wachtwoord in. Er wordt een nieuwe Microsoft `outlook.com` ID en een e-mailpostvak in het domein gemaakt. Ga door met het registratieproces totdat het account is gemaakt.  

    >[!IMPORTANT]
    >U moet een e-mailadres of distributielijst gebruiken die is geregistreerd als Microsoft-id om u te registreren in Dev Center. Microsoft raadt u aan een distributielijst te gebruiken om afhankelijkheid van personen te verwijderen. Als uw e-mailadres of distributielijst niet is geregistreerd, moet u zich nu registreren.    

    >[!Important]
    >Als uw e-mailadres zich `Microsoft` in het bedrijfsdomein bevindt, u het niet gebruiken voor registratie in HetV Center.  

3.  Nadat u de Microsoft-id met het Outlook-e-mailadres hebt gemaakt, meldt u zich aan bij uw Outlook-postvak. Maak een regel voor het doorsturen van e-mail. De regel voor e-maildoorsturen moet alle e-mails die in het Outlook-postvak zijn ontvangen, verplaatsen naar het e-mailadres in uw domein dat u hebt gemaakt om uw marketplace-account te beheren.  
    *   Als u zich wilt aanmelden bij uw Outlook-postvak, gaat u naar de Outlook-pagina op [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Ga voor meer informatie over doorstuurregels naar de regels gebruiken in Outlook Web App om berichten automatisch door te sturen naar een andere accountpagina op [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  De doorstuurregel stuurt alle e-mail en communicatie die in het E-mailaccount van Outlook zijn ontvangen naar het e-mailadres in een domein dat is geregistreerd bij uw bedrijf. Uw `outlook.com` e-mailadres moet worden gebruikt om te verifiëren in zowel Dev Center als Cloud Partner Portal.  

## <a name="next-steps"></a>Volgende stappen

*   Ga naar de pagina [Azure Marketplace en AppSource Publisher Guide.](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) 
 
---
