---
title: Problemen met de proxy van toepassingen oplossen | Microsoft Documenten
description: Voorziet in het oplossen van fouten in Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017; it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7be9a17bed2a39d16f813332c2d6effc03393264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244223"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Problemen en foutmeldingen van toepassingsproxy oplossen

Wanneer u problemen met toepassingsproxy oplost, raden we u aan te beginnen met het controleren van de probleemoplossingsstroom, [problemen met de proxyconnector voor de foutopsporing van toepassingen,](application-proxy-debug-connectors.md)om te bepalen of toepassingsproxyconnectors correct zijn geconfigureerd. Als u nog steeds problemen ondervindt bij het maken van verbinding met de toepassing, volgt u de stroom voor het oplossen van problemen in problemen met de [toepassingsproblemen van de foutopsporingstoepassing.](application-proxy-debug-apps.md)

Als er fouten optreden bij de toegang tot een gepubliceerde toepassing of in publicatietoepassingen, controleert u de volgende opties om te zien of Microsoft Azure AD-toepassingsproxy correct werkt:

* Open de Windows Services-console. Controleer of de **Microsoft AAD Application Proxy Connector-service** is ingeschakeld en uitgevoerd. U ook de pagina Eigenschappen van de application proxy-service bekijken, zoals in de volgende afbeelding wordt weergegeven:  
  ![Schermafbeelding van het venster Eigenschappen van Microsoft AAD Application Proxy Connector Properties](./media/application-proxy-troubleshoot/connectorproperties.png)
* Open Logboeken en zoek naar toepassingsproxyconnectorgebeurtenissen in **toepassingen en serviceslogboeken** > **Microsoft** > **AadApplicationProxy** > **Connector** > **Admin**.
* Indien nodig zijn er meer gedetailleerde logboeken beschikbaar door [de sessielogboeken van de toepassingsproxy-connector in te schakelen.](application-proxy-connectors.md#under-the-hood)

## <a name="the-page-is-not-rendered-correctly"></a>De pagina wordt niet correct weergegeven
U problemen hebben met het renderen van uw toepassing of onjuist functioneren zonder specifieke foutmeldingen te ontvangen. Dit kan gebeuren als u het artikelpad hebt gepubliceerd, maar de toepassing vereist inhoud die buiten dat pad bestaat.

Als u bijvoorbeeld het `https://yourapp/app` pad publiceert, maar `https://yourapp/media`de toepassing afbeeldingen oproept, worden deze niet weergegeven. Zorg ervoor dat u de toepassing publiceert met behulp van het pad op het hoogste niveau dat u nodig hebt om alle relevante inhoud op te nemen. In dit voorbeeld zou `http://yourapp/`het zijn .

Als u uw pad wijzigt om inhoud waarnaar wordt verwezen, maar gebruikers nog steeds op een diepere koppeling in het pad moeten landen, raadpleegt u het blogbericht [De juiste koppeling instellen voor toepassingsproxytoepassingen in het Azure AD-toegangspaneel en het startprogramma voor Office 365-apps](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Verbindingsfouten

Als de registratie mislukt tijdens de installatie van de wizard Connector, zijn er twee manieren om de reden voor de fout weer te geven. Kijk in het gebeurtenislogboek onder **Logboeken voor toepassingen en services\Microsoft\AadApplicationProxy\Connector\Admin**of voer de volgende Opdracht Windows PowerShell uit:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Zodra u de connectorfout in het gebeurtenislogboek hebt gevonden, gebruikt u deze tabel met veelvoorkomende fouten om het probleem op te lossen:

| Fout | Aanbevolen stappen |
| ----- | ----------------- |
| Connectorregistratie is mislukt: zorg ervoor dat u toepassingsproxy hebt ingeschakeld in de Azure Management Portal en dat u de gebruikersnaam en het wachtwoord van Active Directory correct hebt ingevoerd. Fout: 'Er zijn een of meer fouten opgetreden.' | Als u het registratievenster hebt gesloten zonder u aan te melden bij Azure AD, voert u de wizard Connector opnieuw uit en registreert u de connector. <br><br> Als het registratievenster wordt geopend en vervolgens onmiddellijk wordt gesloten zonder dat u zich aanmelden, krijgt u deze fout waarschijnlijk. Deze fout treedt op wanneer er een netwerkfout op uw systeem optreedt. Zorg ervoor dat het mogelijk is om verbinding te maken vanuit een browser met een openbare website en dat de poorten zijn geopend zoals gespecificeerd in [vereisten voor application proxy.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) |
| Duidelijke fout wordt weergegeven in het registratievenster. Kan niet doorgaan | Als u deze fout ziet en vervolgens het venster sluit, hebt u de verkeerde gebruikersnaam of het verkeerde wachtwoord ingevoerd. &Opnieuw. |
| Connectorregistratie is mislukt: zorg ervoor dat u toepassingsproxy hebt ingeschakeld in de Azure Management Portal en dat u de gebruikersnaam en het wachtwoord van Active Directory correct hebt ingevoerd. Fout: 'AADSTS50059: Er is geen tenant-identificerende informatie gevonden in de aanvraag of geïmpliceerd door een verstrekte referenties en zoeken door serviceprincipal URI is mislukt. | U probeert u aan te melden met een Microsoft-account en niet met een domein dat deel uitmaakt van de organisatie-id van de map die u probeert te openen. Zorg ervoor dat de beheerder deel uitmaakt van dezelfde domeinnaam als het tenantdomein, bijvoorbeeld als admin@contoso.comhet Azure AD-domein contoso.com is, moet de beheerder . |
| Kan het huidige uitvoeringsbeleid voor het uitvoeren van PowerShell-scripts niet ophalen. | Als de connector-installatie mislukt, controleert u of het uitvoeringsbeleid van PowerShell niet is uitgeschakeld. <br><br>1. Open de groepsbeleidseditor.<br>2. Ga naar **computerconfiguratiebeheersjablonen** > **Administrative Templates** > **Windows-onderdelen** > **Windows PowerShell** en dubbelklik op **Script-uitvoering inschakelen**.<br>3. Het uitvoeringsbeleid kan worden ingesteld op **Niet geconfigureerd** of **ingeschakeld**. Als u bent ingesteld op **Ingeschakeld,** moet u ervoor zorgen dat onder Opties het uitvoeringsbeleid is ingesteld op **Toestaan van lokale scripts en extern ondertekende scripts** of op Toestaan van alle **scripts**. |
| Connector kan de configuratie niet downloaden. | Het clientcertificaat van de connector, dat wordt gebruikt voor verificatie, is verlopen. Dit kan ook gebeuren als u de connector achter een proxy hebt geïnstalleerd. In dit geval heeft de connector geen toegang tot internet en kan deze geen toepassingen leveren aan externe gebruikers. Vernieuw de vertrouwensrelatie handmatig met de `Register-AppProxyConnector` cmdlet in Windows PowerShell. Als uw connector zich achter een proxy bevindt, is het noodzakelijk om internettoegang te verlenen tot de 'netwerkservices' en 'lokaal systeem' van de Connector-accounts. Dit kan worden bereikt door hen toegang te verlenen tot de proxy of door ze in te stellen om de proxy te omzeilen. |
| Connectorregistratie is mislukt: controleer of u een toepassingsbeheerder van uw Active Directory bent om de connector te registreren. Fout: 'Het registratieverzoek is afgewezen.' | De alias waarmee u zich probeert in te loggen, is geen beheerder van dit domein. Uw connector wordt altijd geïnstalleerd voor de map die eigenaar is van het domein van de gebruiker. Zorg ervoor dat het beheerdersaccount waarmee u zich probeert aan te melden ten minste machtigingen voor toepassingsbeheerder heeft voor de Azure AD-tenant. |
| De connector kan geen verbinding maken met de service vanwege netwerkproblemen. De connector probeerde toegang te krijgen tot de volgende URL. | De connector kan geen verbinding maken met de cloudservice Application Proxy. Dit kan gebeuren als u een firewallregel hebt die de verbinding blokkeert. Zorg ervoor dat u toegang hebt gegeven tot de juiste poorten en URL's die worden vermeld in [vereisten voor toepassingsproxy.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) |

## <a name="kerberos-errors"></a>Kerberos fouten

Deze tabel behandelt de meest voorkomende fouten die afkomstig zijn van Kerberos setup en configuratie, en bevat suggesties voor oplossing.

| Fout | Aanbevolen stappen |
| ----- | ----------------- |
| Kan het huidige uitvoeringsbeleid voor het uitvoeren van PowerShell-scripts niet ophalen. | Als de connector-installatie mislukt, controleert u of het uitvoeringsbeleid van PowerShell niet is uitgeschakeld.<br><br>1. Open de groepsbeleidseditor.<br>2. Ga naar **computerconfiguratiebeheersjablonen** > **Administrative Templates** > **Windows-onderdelen** > **Windows PowerShell** en dubbelklik op **Script-uitvoering inschakelen**.<br>3. Het uitvoeringsbeleid kan worden ingesteld op **Niet geconfigureerd** of **ingeschakeld**. Als u bent ingesteld op **Ingeschakeld,** moet u ervoor zorgen dat onder Opties het uitvoeringsbeleid is ingesteld op **Toestaan van lokale scripts en extern ondertekende scripts** of op Toestaan van alle **scripts**. |
| 12008 - Azure AD heeft het maximum aantal toegestane Kerberos-verificatiepogingen naar de backendserver overschreden. | Deze fout kan duiden op een onjuiste configuratie tussen Azure AD en de backend-toepassingsserver of een probleem in de tijd- en datumconfiguratie op beide machines. De backendserver heeft het Kerberos-ticket van Azure AD geweigerd. Controleer of Azure AD en de backend-toepassingsserver correct zijn geconfigureerd. Zorg ervoor dat de tijd- en datumconfiguratie op het Azure AD en de backend-toepassingsserver worden gesynchroniseerd. |
| 13016 - Azure AD kan namens de gebruiker geen Kerberos-ticket ophalen omdat er geen UPN in het randtoken of in de toegangscookie zit. | Er is een probleem met de STS-configuratie. Fix the UPN claim configuration in the STS. |
| 13019 - Azure AD kan een Kerberos-ticket niet namens de gebruiker ophalen vanwege de volgende algemene API-fout. | Deze gebeurtenis kan duiden op een onjuiste configuratie tussen Azure AD en de server van de domeincontroller of op een tijd- en datumconfiguratie op beide machines. De domeincontroller heeft het Kerberos-ticket dat is gemaakt door Azure AD geweigerd. Controleer of Azure AD en de backend-toepassingsserver correct zijn geconfigureerd, met name de SPN-configuratie. Controleer of het Azure AD-domein is gekoppeld aan hetzelfde domein als de domeincontroller om ervoor te zorgen dat de domeincontroller vertrouwensrelaties met Azure AD instelt. Zorg ervoor dat de tijd- en datumconfiguratie op het Azure AD en de domeincontroller zijn gesynchroniseerd. |
| 13020 - Azure AD kan namens de gebruiker geen Kerberos-ticket ophalen omdat de backendserver SPN niet is gedefinieerd. | Deze gebeurtenis kan duiden op een onjuiste configuratie tussen Azure AD en de server van de domeincontroller of op een tijd- en datumconfiguratie op beide machines. De domeincontroller heeft het Kerberos-ticket dat is gemaakt door Azure AD geweigerd. Controleer of Azure AD en de backend-toepassingsserver correct zijn geconfigureerd, met name de SPN-configuratie. Controleer of het Azure AD-domein is gekoppeld aan hetzelfde domein als de domeincontroller om ervoor te zorgen dat de domeincontroller vertrouwensrelaties met Azure AD instelt. Zorg ervoor dat de tijd- en datumconfiguratie op het Azure AD en de domeincontroller zijn gesynchroniseerd. |
| 13022 - Azure AD kan de gebruiker niet verifiëren omdat de backendserver reageert op Kerberos-verificatiepogingen met een HTTP 401-fout. | Deze gebeurtenis kan duiden op een onjuiste configuratie tussen Azure AD en de backend-toepassingsserver of een probleem in de tijd- en datumconfiguratie op beide machines. De backendserver heeft het Kerberos-ticket van Azure AD geweigerd. Controleer of Azure AD en de backend-toepassingsserver correct zijn geconfigureerd. Zorg ervoor dat de tijd- en datumconfiguratie op het Azure AD en de backend-toepassingsserver worden gesynchroniseerd. Zie [Problemen met kerberos constrained delegation configurations for Application Proxy voor](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)meer informatie.  |

## <a name="end-user-errors"></a>Fouten van eindgebruikers

Deze lijst bevat fouten die uw eindgebruikers kunnen tegenkomen wanneer ze de app proberen te openen en mislukken. 

| Fout | Aanbevolen stappen |
| ----- | ----------------- |
| De website kan de pagina niet weergeven. | Uw gebruiker kan deze fout krijgen wanneer hij toegang probeert te krijgen tot de app die u hebt gepubliceerd als de toepassing een IWA-toepassing is. De gedefinieerde SPN voor deze toepassing kan onjuist zijn. Voor IWA-apps moet u ervoor zorgen dat de SPN die voor deze toepassing is geconfigureerd, correct is. |
| De website kan de pagina niet weergeven. | Uw gebruiker kan deze fout krijgen wanneer hij toegang probeert te krijgen tot de app die u hebt gepubliceerd als de toepassing een OWA-toepassing is. Dit kan worden veroorzaakt door een van de volgende:<br><li>De gedefinieerde SPN voor deze toepassing is onjuist. Controleer of de SPN die voor deze toepassing is geconfigureerd, correct is.</li><li>De gebruiker die toegang heeft tot de toepassing gebruikt een Microsoft-account in plaats van het juiste bedrijfsaccount om zich aan te melden, of de gebruiker is een gastgebruiker. Zorg ervoor dat de gebruiker zich aanmeldt met zijn bedrijfsaccount dat overeenkomt met het domein van de gepubliceerde toepassing. Gebruikers en gasten van Microsoft-account hebben geen toegang tot IWA-toepassingen.</li><li>De gebruiker die heeft geprobeerd om toegang te krijgen tot de toepassing is niet goed gedefinieerd voor deze toepassing op de on-premises kant. Zorg ervoor dat deze gebruiker de juiste machtigingen heeft zoals gedefinieerd voor deze backend-toepassing op de on-premises machine. |
| Deze bedrijfsapp is niet toegankelijk. U bent niet bevoegd om toegang te krijgen tot deze toepassing. De autorisatie is mislukt. Zorg ervoor dat u de gebruiker met toegang tot deze toepassing toewijst. | Uw gebruiker kan deze fout krijgen wanneer hij toegang probeert te krijgen tot de app die u hebt gepubliceerd als hij of zij Microsoft-accounts gebruikt in plaats van zijn bedrijfsaccount om zich aan te melden. Gastgebruikers kunnen deze fout ook krijgen. Gebruikers en gasten van het Microsoft-account hebben geen toegang tot IWA-toepassingen. Zorg ervoor dat de gebruiker zich aanmeldt met zijn bedrijfsaccount dat overeenkomt met het domein van de gepubliceerde toepassing.<br><br>Het is mogelijk dat u de gebruiker niet voor deze toepassing hebt toegewezen. Ga naar het tabblad **Toepassing** en wijs deze gebruikers- of gebruikersgroep onder **Gebruikers en groepen**toe aan deze toepassing. |
| Deze bedrijfsapp is op dit moment niet toegankelijk. Probeer het later opnieuw... Er is een time-out ophet moment dat de connector is vergetik. | Uw gebruiker kan deze fout krijgen wanneer hij toegang probeert te krijgen tot de app die u hebt gepubliceerd als deze niet goed is gedefinieerd voor deze toepassing aan de on-premises kant. Zorg ervoor dat uw gebruikers de juiste machtigingen hebben zoals gedefinieerd voor deze backend-toepassing op de on-premises machine. |
| Deze bedrijfsapp is niet toegankelijk. U bent niet bevoegd om toegang te krijgen tot deze toepassing. De autorisatie is mislukt. Controleer of de gebruiker een licentie heeft voor Azure Active Directory Premium. | Uw gebruiker kan deze fout krijgen wanneer hij toegang probeert te krijgen tot de app die u hebt gepubliceerd als deze niet expliciet is toegewezen met een Premium-licentie door de beheerder van de abonnee. Ga naar het tabblad Active **Directory-licenties van** de abonnee en controleer of aan deze gebruikersgroep een Premium-licentie is toegewezen. |
| Er kan geen server met de opgegeven hostnaam worden gevonden. | Uw gebruiker kan deze fout krijgen wanneer hij toegang probeert te krijgen tot de app die u hebt gepubliceerd als het aangepaste domein van de toepassing niet correct is geconfigureerd. Controleer of u een certificaat voor het domein hebt geüpload en de DNS-record correct hebt geconfigureerd door de stappen te volgen in [Werken met aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md) |

## <a name="my-error-wasnt-listed-here"></a>Mijn fout is hier niet vermeld

Als u een fout of probleem ondervindt met Azure AD Application Proxy die niet wordt vermeld in deze handleiding voor probleemoplossing, horen we dit graag. Stuur een e-mail naar ons [feedbackteam](mailto:aadapfeedback@microsoft.com) met de details van de fout die u tegenkwam.

## <a name="see-also"></a>Zie ook
* [Toepassingsproxy inschakelen voor Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Toepassingen publiceren met toepassingsproxy (Engelstalig artikel)](application-proxy-add-on-premises-application.md)
* [Eén aanmelding inschakelen](application-proxy-configure-single-sign-on-with-kcd.md)
* [Voorwaardelijke toegang inschakelen](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
