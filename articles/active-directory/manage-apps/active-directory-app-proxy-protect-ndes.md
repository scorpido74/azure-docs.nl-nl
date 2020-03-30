---
title: Integreren met AD-toepassingsproxy op een NDES-server
titleSuffix: Azure Active Directory
description: Richtlijnen voor het implementeren van een Azure Active Directory Application Proxy om uw NDES-server te beschermen.
services: active-directory
author: CelesteDG
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: baselden
ms.reviewer: mimart
ms.openlocfilehash: 4ccd8834671725ace72497391090f81eb197ad6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77032255"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integratie met Azure AD Application Proxy op een NDES-server (Network Device Enrollment Service)

Met Azure Active Directory (AD) Application Proxy u toepassingen in uw netwerk publiceren. Deze toepassingen zijn toepassingen zoals SharePoint-sites, Microsoft Outlook Web App en andere webtoepassingen. Het biedt ook veilige toegang tot gebruikers buiten uw netwerk via Azure.

Zie [Rastoegang tot on-premises toepassingen via Azure AD Application Proxy](application-proxy.md)als u nieuw bent in Azure AD Application Proxy en meer wilt weten.

Azure AD Application Proxy is gebouwd op Azure. Het geeft u een enorme hoeveelheid netwerkbandbreedte en serverinfrastructuur voor een betere bescherming tegen distributed denial-of-service (DDOS) aanvallen en uitstekende beschikbaarheid. Bovendien is het niet nodig om externe firewallpoorten te openen voor uw on-premises netwerk en is er geen DMZ-server vereist. Al het verkeer is inkomende ontstaan. Zie [Zelfstudie: Een on-premises toepassing toevoegen voor externe toegang via Application Proxy in Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)een volledige lijst met uitgaande poorten.

> Azure AD Application Proxy is een functie die alleen beschikbaar is als u de Premium- of Basic-edities van Azure Active Directory gebruikt. Zie [Azure Active Directory-prijzen](https://azure.microsoft.com/pricing/details/active-directory/)voor meer informatie . 
> Als u ems-licenties (Enterprise Mobility Suite) hebt, komt u in aanmerking voor het gebruik van deze oplossing.
> De Azure AD Application Proxy-connector wordt alleen geïnstalleerd op Windows Server 2012 R2 of hoger. Dit is ook een vereiste van de NDES-server.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>De connector installeren en registreren op de NDES-server

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als een toepassingsbeheerder van de map die gebruikmaakt van Application Proxy. Als het domein van de tenant bijvoorbeeld contoso.com is, moet de beheerder admin@contoso.com of een andere beheerdersalias in dat domein zijn.
1. Selecteer uw gebruikersnaam in de rechterbovenhoek. Controleer of u bent aangemeld bij een map met toepassingsproxy. Als u mappen wilt wijzigen, selecteert u **De map Schakelen** en kiest u een map met toepassingsproxy.
1. Selecteer in het linkernavigatiedeelvenster De optie **Azure Active Directory**.
1. Selecteer Onder **Beheren**de optie **Toepassingsproxy**.
1. Selecteer **Connectorservice downloaden**.

    ![Connectorservice downloaden om de servicevoorwaarden te bekijken](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Lees de servicevoorwaarden. Wanneer u klaar bent, selecteert u **Termen accepteren & downloaden**.
1. Kopieer het installatiebestand voor azure AD-toepassingsproxy-connector naar uw NDES-server. 
   > U de connector installeren op elke server binnen uw bedrijfsnetwerk met toegang tot NDES. U hoeft het niet op de NDES-server zelf te installeren.
1. Voer het installatiebestand uit, zoals *AADApplicationProxyConnectorInstaller.exe*. Accepteer de licentievoorwaarden voor software.
1. Tijdens de installatie wordt u gevraagd de connector te registreren met de toepassingsproxy in uw Azure AD-map.
   * Geef de referenties op voor een globale of toepassingsbeheerder in uw Azure AD-map. De algemene azure AD-referenties of toepassingsbeheerders kunnen afwijken van uw Azure-referenties in de portal.

        > [!NOTE]
        > Het globale account of het account voor toepassingsbeheerder dat wordt gebruikt om de connector te registreren, moet deel uitmaken van dezelfde map waar u de application proxy-service inschakelt.
        >
        > Als het Azure AD-domein bijvoorbeeld *contoso.com*is, moet `admin@contoso.com` de globale/toepassingsbeheerder of een andere geldige alias op dat domein zijn.

   * Als de verbeterde beveiligingsconfiguratie van Internet Explorer is ingeschakeld voor de server waar u de connector installeert, wordt het registratiescherm mogelijk geblokkeerd. Als u toegang wilt verlenen, volgt u de instructies in het foutbericht of schakelt u verbeterde beveiliging van Internet Explorer uit tijdens het installatieproces.
   * Zie [Alleengebeurtenisproxy oplossen](application-proxy-troubleshoot.md)als de connectorregistratie mislukt.
1. Aan het einde van de installatie wordt een notitie weergegeven voor omgevingen met een uitgaande proxy. Als u de Azure AD Application Proxy-connector wilt configureren om via `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`de uitgaande proxy te werken, voert u het meegeleverde script uit, zoals .
1. Op de pagina Toepassingsproxy in de Azure-portal wordt de nieuwe connector weergegeven met de status *Actief*, zoals in het volgende voorbeeld wordt weergegeven:

    ![De nieuwe Azure AD Application Proxy-connector die wordt weergegeven als actief in de Azure-portal](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Als u een hoge beschikbaarheid wilt bieden voor toepassingen die zijn geauthenticeren via de Azure AD-toepassingsproxy, u connectors op meerdere VM's installeren. Herhaal dezelfde stappen in de vorige sectie om de connector te installeren op andere servers die zijn aangesloten bij het beheerde Azure AD DS-domein.

1. Ga na een succesvolle installatie terug naar de Azure-portal.

1. Selecteer **Enterprise-toepassingen**.

   ![ervoor zorgen dat u de juiste belanghebbenden indienst](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Selecteer **+Nieuwe toepassing**en selecteer **Vervolgens On-premises toepassing**. 

1. Configureer de volgende velden in de **on-premises toepassing Toevoegen:**

   * **Naam:** Voer een naam in voor de toepassing.
   * **Interne url:** voer de interne URL/FQDN in van uw NDES-server waarop u de connector hebt geïnstalleerd.
   * **Pre-verificatie:** **Selecteer Passthrough**. Het is niet mogelijk om enige vorm van pre-authenticatie te gebruiken. Het protocol dat wordt gebruikt voor certificaataanvragen (SCEP) biedt geen dergelijke optie.
   * Kopieer de opgegeven **externe URL** naar uw klembord.

1. Selecteer **+Toevoegen** om uw toepassing op te slaan.

1. Test of u uw NDES-server openen via de azure AD-toepassingsproxy door de koppeling die u in stap 10 hebt gekopieerd, in stap 10 in een browser te plakken. U ziet een standaard IIS-welkomstpagina.

1. Voeg als laatste test het *mscep.dll-pad* toe aan de bestaande URL die u in de vorige stap hebt geplakt:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. U ziet een **HTTP-fout 403 – Verboden** antwoord.

1. Wijzig de NDES-URL die (via Microsoft Intune) wordt verstrekt in apparaten, deze wijziging kan zich bevinden in microsoft Endpoint Configuration Center of in Intune Cloud.

   * Ga voor configuratiecentrum naar het Certificaatregistratiepunt (CRP) en pas de URL aan. Deze URL is wat apparaten roepen om en presenteren hun uitdaging.
   * Voor Alleen Intune Cloud, ook wel Intune Standalone genoemd, bewerkt of maakt u een nieuw SCEP-beleid en voegt u de nieuwe URL toe.

## <a name="next-steps"></a>Volgende stappen

Met de Azure AD Application Proxy geïntegreerd met NDES, publiceer toepassingen voor gebruikers om toegang te krijgen. Zie [Toepassingen publiceren met Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)voor meer informatie.
