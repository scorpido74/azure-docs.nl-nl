---
title: Richt lijnen voor Azure Marketplace en AppSource Publisher | Azure
description: Richt lijnen voor Azure Marketplace en AppSource voor app-en service-uitgevers
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: dsindona
ms.openlocfilehash: 9156126ff2a3dbe936dc426c6f5097c6bba7b7b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80387599"
---
# <a name="guidelines"></a>Richtlijnen  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Richt lijnen voor Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Richt lijnen voor het maken van een micro soft-ID voor het beheren van een Marketplace-account  
Als meer dan één persoon toegang vereist tot dezelfde micro soft-ID die is gebruikt voor het maken van uw Marketplace-account, moet u deze richt lijnen volgen om u te helpen bij het maken van een bedrijfs account. 

>[!IMPORTANT]
>Als u meerdere gebruikers toegang wilt verlenen tot uw micro soft Developer Center-account (dev Center), raadt micro soft u aan Azure Active Directory (Azure AD) te gebruiken om rollen toe te wijzen aan afzonderlijke gebruikers. Elke gebruiker moet toegang hebben tot het account door zich aan te melden met de afzonderlijke Azure AD-referenties. Maak uw micro soft-ID met behulp van een e-mail adres in een domein dat is geregistreerd bij uw bedrijf micro soft raadt aan dat het e-mail bericht niet aan een persoon is toegewezen. Een voorbeeld is `windowsapps@fabrikam.com`.  
>*   Ga voor meer informatie naar het onderwerp [: micro soft-id in een federatieve AD-domein](#issue-microsoft-id-in-an-azure-ad-federated-domain) sectie.  

*   Beperk de toegang tot de micro soft-ID tot het kleinst mogelijke aantal ontwikkel aars. 
*   Stel een zakelijke e-mail distributie lijst (DL) in die alle gebruikers bevat die toegang moeten hebben tot uw ontwikkelaars centrum-account. Voeg het e-mail adres voor de DL toe aan uw beveiligings gegevens. Met de DL kunnen alle werk nemers in de lijst beveiligings codes ontvangen wanneer daarom wordt gevraagd en de beveiligings gegevens voor uw micro soft-ID worden beheerd. Als het instellen van een distributie lijst niet haalbaar is, moet de eigenaar van het afzonderlijke e-mail account beschikbaar zijn voor toegang tot en delen van de beveiligings code wanneer u hierom wordt gevraagd.  
    *   De eigenaar wordt bijvoorbeeld gevraagd wanneer nieuwe beveiligings gegevens worden toegevoegd aan de micro soft-ID of wanneer de micro soft-ID wordt geopend vanaf een nieuw apparaat.  
*   Voeg een telefoon nummer van het bedrijf toe waarvoor geen extensie is vereist en dat toegankelijk is voor belang rijke team leden.  
*   Over het algemeen moet u ontwikkel aars verplichten om te gebruiken van vertrouwde apparaten om zich aan te melden bij uw ontwikkelaars centrum-account. Alle belang rijke team leden moeten toegang hebben tot de vertrouwde apparaten. Het gebruik van vertrouwde apparaten voor toegang vermindert de vereiste voor het verzenden van beveiligings codes wanneer iemand toegang heeft tot het ontwikkelaars centrum-account.  
*   Als u verplicht bent om toegang te verlenen tot het ontwikkelaars centrum-account van een niet-vertrouwde computer, moet u de toegang tot Maxi maal vijf ontwikkel aars beperken. In het ideale geval moeten uw ontwikkel aars toegang hebben tot het account van computers die dezelfde geografische en netwerk locatie delen.  
*   Controleer uw beveiligings gegevens regel matig en controleer deze.  
    *   Als u uw beveiligings gegevens wilt bekijken, gaat u naar de pagina beveiligings instellingen op [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Uw ontwikkelaars centrum-account moet voornamelijk worden geopend vanaf vertrouwde computers. Het is essentieel dat u toegang krijgt vanaf vertrouwde computers, omdat er een limiet is voor het aantal gegenereerde codes per dev Center-account per week. Als u gebruikmaakt van vertrouwde computers, wordt de veiligste en consistente aanmeldings ervaring ook ingeschakeld. 
*   Ga voor meer informatie over extra ontwikkelaars centrum-account richtlijnen en beveiliging naar de pagina een ontwikkelaars account openen op [docs.Microsoft.com/Windows/UWP/Publish/opening-a-Developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Probleem: micro soft-ID in een federatief Azure AD-domein  
Uw bedrijfs account kan worden federatieve door Azure Active Directory (Azure AD). Als u probeert een micro soft-ID te maken op basis van een zakelijk e-mail adres dat is federatieve met Azure AD, ontvangt u een fout melding. Als u een fout bericht ontvangt, moet u bij uw IT-team controleren of uw account is federatieve via Azure AD. Federatieve e-mail van Azure AD is een bekend probleem en micro soft werkt eraan om dit op te lossen.  
*   Ga voor meer informatie over Azure AD naar de Azure Active Directory-documentatie pagina bevindt zich op [docs.Microsoft.com/azure/Active-Directory](https://docs.microsoft.com/azure/active-directory).

Micro soft raadt u aan om een tijdelijke oplossing. Volg deze stappen voor het maken van een nieuw e- `outlook.com` mail adres in het domein en maak een regel om uw communicatie door te sturen.  
1.  Ga naar de pagina account maken en klik op de koppeling een nieuw e-mail adres ophalen. 
    *   Als u zich wilt aanmelden voor uw micro soft-ID, gaat u naar de pagina account maken op [signup.live.com/signup](https://signup.live.com/signup).  
2.  Maak het nieuwe e-mail adres en voer een wacht woord in. Er wordt een nieuwe micro soft-ID en een `outlook.com` e-mail postvak in het domein gemaakt. Ga door met het registratie proces totdat het account is gemaakt.  

    >[!IMPORTANT]
    >U moet een e-mail adres of distributie lijst gebruiken die is geregistreerd als een micro soft-ID om te registreren in het ontwikkelaars centrum. Micro soft raadt u aan om een distributie lijst te gebruiken voor het verwijderen van de afhankelijkheid van personen. Als uw e-mail adres of distributie lijst niet is geregistreerd, moet u zich nu registreren.    

    >[!Important]
    >Als uw e-mail adres zich in het `Microsoft` bedrijfs domein bevindt, kunt u dit niet gebruiken voor registratie in het ontwikkelaars centrum.  

3.  Nadat u de micro soft-ID met het e-mail adres van Outlook hebt gemaakt, meldt u zich aan bij uw Outlook-postvak. Een regel voor het door sturen van e-mail maken. De regel voor het door sturen van e-mail moet alle e-mail berichten die in het Outlook-postvak worden ontvangen, verplaatsen naar het e-mail adres in uw domein dat u hebt gemaakt voor het beheren van uw Marketplace-account.  
    *   Ga naar de Outlook-pagina op [Outlook.live.com/OWA](https://outlook.live.com/owa)om u aan te melden bij uw Outlook-postvak.  
    *   Ga voor meer informatie over regels voor door sturen naar de regels in Outlook Web app gebruiken om berichten automatisch door te sturen naar een andere account pagina op [support.Office.com/article/use-rules-in-Outlook-Web-app-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  De doorstuur regel verzendt alle e-mail en communicaties die zijn ontvangen in het Outlook-e-mail account naar het e-mail adres in een domein dat bij uw bedrijf is geregistreerd. Uw `outlook.com` e-mail adres moet worden gebruikt voor verificatie in zowel het ontwikkelaars centrum als Cloud Partner-Portal.  

## <a name="next-steps"></a>Volgende stappen

*   Ga naar de pagina [met Publisher-hand leiding voor Azure Marketplace en AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) . 
 
---
