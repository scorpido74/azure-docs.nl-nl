---
title: On-premises API's openen met Azure AD-toepassingsproxy
description: Met de toepassingsproxy van Azure Active Directory kunnen native apps veilig toegang krijgen tot API's en bedrijfslogica die u on-premises of op cloud-VM's host.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77166012"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Beveiligde toegang tot on-premises API's met Azure AD-toepassingsproxy

Mogelijk worden API's voor bedrijfslogica on-premises uitgevoerd of gehost op virtuele machines in de cloud. Uw eigen Android-, iOS-, Mac- of Windows-apps moeten communiceren met de API-eindpunten om gegevens te gebruiken of gebruikersinteractie te bieden. Azure AD Application Proxy en de [Azure Active Directory Authentication Libraries (ADAL)](/azure/active-directory/develop/active-directory-authentication-libraries) geven uw eigen apps veilig toegang tot uw on-premises API's. Azure Active Directory Application Proxy is een snellere en veiligere oplossing dan het openen van firewallpoorten en het beheren van verificatie en autorisatie op de app-laag. 

In dit artikel u een Azure AD Application Proxy-oplossing instellen voor het hosten van een web-API-service waartoe native apps toegang hebben. 

## <a name="overview"></a>Overzicht

Het volgende diagram toont een traditionele manier om on-premises API's te publiceren. Deze aanpak vereist het openen van inkomende havens 80 en 443.

![Traditionele API-toegang](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

In het volgende diagram ziet u hoe u Azure AD Application Proxy gebruiken om API's veilig te publiceren zonder inkomende poorten te openen:

![Azure AD Application Proxy API-toegang](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

De Azure AD Application Proxy vormt de ruggengraat van de oplossing, werkt als een openbaar eindpunt voor API-toegang en biedt verificatie en autorisatie. U hebt toegang tot uw API's vanaf een breed scala aan platforms via de [ADAL-bibliotheken.](/azure/active-directory/develop/active-directory-authentication-libraries) 

Aangezien azure AD-toepassingsproxyverificatie en -autorisatie bovenop Azure AD zijn gebouwd, u voorwaardelijke toegang voor Azure AD gebruiken om ervoor te zorgen dat alleen vertrouwde apparaten toegang hebben tot API's die zijn gepubliceerd via Application Proxy. Gebruik Azure AD Join of Azure AD Hybrid Joined voor desktops en Intune Beheerd voor apparaten. U ook profiteren van Azure Active Directory Premium-functies zoals Azure Multi-Factor Authentication en de door machine learning gesteunde beveiliging van [Azure Identity Protection.](/azure/active-directory/active-directory-identityprotection)

## <a name="prerequisites"></a>Vereisten

Om deze walkthrough te volgen, heb je het volgende nodig:

- Beheerderstoegang tot een Azure-map, met een account waarmee apps kunnen worden gemaakt en geregistreerd
- De voorbeeldweb-API en native client-apps van[https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 

## <a name="publish-the-api-through-application-proxy"></a>De API publiceren via application proxy

Als u een API buiten uw intranet wilt publiceren via Application Proxy, volgt u hetzelfde patroon als voor het publiceren van web-apps. Zie [Zelfstudie: Een on-premises toepassing voor externe toegang toevoegen via Application Proxy in Azure Active Directory](application-proxy-add-on-premises-application.md).

Ga als een te werk om de SecretAPI-web-API te publiceren via Application Proxy:

1. Bouw en publiceer het voorbeeld SecretAPI-project als een ASP.NET web-app op uw lokale computer of intranet. Zorg ervoor dat u lokaal toegang hebt tot de web-app. 
   
1. Selecteer Azure Active Directory in de [Azure-portal](https://portal.azure.com). **Azure Active Directory** Selecteer vervolgens **Enterprise-toepassingen**.
   
1. Boven aan de **enterprise-toepassingen - Alle toepassingen pagina,** selecteert **US nieuwe toepassing**.
   
1. Selecteer op de pagina **Een toepassingstoevoegen** de optie **On-premises toepassingen**. De **pagina Uw eigen on-premises toepassings** toevoegen wordt weergegeven.
   
1. Als u geen Application Proxy Connector hebt geïnstalleerd, wordt u gevraagd deze te installeren. Selecteer **Application Proxy Connector downloaden** om de connector te downloaden en te installeren. 
   
1. Zodra u de application proxy connector hebt geïnstalleerd, op de pagina **Uw eigen on-premises toepassings** toevoegen:
   
   1. Voer naast **Naam** *SecretAPI*in .
      
   1. Voer naast **Interne url**de URL in die u gebruikt om toegang te krijgen tot de API vanuit uw intranet.
      
   1. Controleer of **Pre-Authentication** is ingesteld op **Azure Active Directory**. 
      
   1. Selecteer **Toevoegen** boven aan de pagina en wacht tot de app is gemaakt.
   
   ![API-app toevoegen](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. Selecteer op de pagina **Enterprise-toepassingen - Alle toepassingen** de **SecretAPI-app.** 
   
1. Selecteer **eigenschappen** in de linkernavigatie op de pagina **SecretAPI - Overzicht.**
   
1. U wilt niet dat API's beschikbaar zijn voor eindgebruikers in het deelvenster **MyApps,** dus stel **Zichtbaar in voor gebruikers** op **Nee** onder aan de pagina **Eigenschappen** en selecteer **Vervolgens Opslaan**.
   
   ![Niet zichtbaar voor gebruikers](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
U hebt uw web-API gepubliceerd via Azure AD Application Proxy. Voeg nu gebruikers toe die toegang hebben tot de app. 

1. Selecteer op de pagina **SecretAPI - Overzicht** de optie **Gebruikers en groepen** in de linkernavigatie.
   
1. Selecteer gebruiker **toevoegen**op de pagina **Gebruikers en groepen** .  
   
1. Selecteer op de pagina **Toewijzing toevoegen** de optie Gebruikers **en groepen**. 
   
1. Zoek **op** de pagina Gebruikers en groepen naar en selecteer gebruikers die toegang hebben tot de app, ook uzelf. Nadat u alle gebruikers hebt geselecteerd, selecteert **u Selecteren**. 
   
   ![Gebruiker selecteren en toewijzen](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. Selecteer Toewijzing toewijzen op de pagina **Toewijzing** **toevoegen**. 

> [!NOTE]
> API's die geïntegreerde Windows-verificatie gebruiken, vereisen mogelijk [extra stappen.](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>De native app registreren en toegang verlenen tot de API

Native apps zijn programma's die zijn ontwikkeld om te gebruiken op een bepaald platform of apparaat. Voordat uw native app verbinding kan maken en toegang heeft tot een API, moet u deze registreren in Azure AD. In de volgende stappen wordt uitgelegd hoe u een native app registreert en deze toegang geeft tot de web-API die u hebt gepubliceerd via Application Proxy.

Ga als volgende over de native app AppProxyNativeAppSample:

1. Selecteer op de pagina Azure Active **Directory-overzicht** de optie **App-registraties**en selecteer boven aan het deelvenster **App-registraties** de optie **Nieuwe registratie**.
   
1. Ga op de pagina **Registreer een aanvraag:**
   
   1. Voer **onder Naam** *AppProxyNativeAppSample in*. 
      
   1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**. 
      
   1. Onder **URL omleiden,** devalert en selecteer **Openbare client (mobile & desktop)** en voer vervolgens *https in:\//appproxynativeapp*. 
      
   1. Selecteer **Registreren**en wacht tot de app is geregistreerd. 
      
      ![Nieuwe toepassing registreren](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
U hebt nu de AppProxyNativeAppSample-app geregistreerd in Azure Active Directory. Ga als u uw eigen app toegang geven tot de SecretAPI-web-API:

1. Selecteer op de pagina Azure Active Directory **Overview** > **App Registrations** de app **AppProxyNativeAppSample.** 
   
1. Selecteer **api-machtigingen** in de linkernavigatie op de pagina **AppProxyNativeAppSample.** 
   
1. Selecteer op de pagina **API-machtigingen** de optie **Een machtiging toevoegen**.
   
1. Selecteer op de pagina **Api-machtigingen** voor aanvragen de **API's die mijn organisatie gebruikt** en zoek en selecteer Vervolgens **SecretAPI**. 
   
1. Schakel op de pagina Volgende **api-machtigingen aanvragen** het selectievakje naast **user_impersonation**in en selecteer **machtigingen toevoegen**. 
   
    ![Een API selecteren](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. Terug op de pagina **API-machtigingen** u **toestemming voor de beheerder verlenen voor Contoso** selecteren om te voorkomen dat andere gebruikers individueel toestemming moeten geven voor de app. 

## <a name="configure-the-native-app-code"></a>De native app-code configureren

De laatste stap is het configureren van de native app. Het volgende fragment uit het *Form1.cs-bestand* in de NativeClient-voorbeeld-app zorgt ervoor dat de ADAL-bibliotheek het token voor het aanvragen van de API-aanroep verwerft en als drager aan de app-header wordt gekoppeld. 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
Als u de native app wilt configureren om verbinding te maken met Azure Active Directory en de PROXY van de API-app aan te roepen, werkt u de tijdelijke aanduidingswaarden in het *app.config-bestand* van de voorbeeldapp NativeClient bij met waarden uit Azure AD: 

- Plak de **map (tenant) id** in het `<add key="ida:Tenant" value="" />` veld. U deze waarde (een GUID) vinden en kopiëren op de pagina **Overzicht** van een van uw apps. 
  
- Plak de **appProxyNativeAppSample-toepassings-id (client)** in het `<add key="ida:ClientId" value="" />` veld. U deze waarde (een GUID) vinden en kopiëren via de **pagina** Overzicht van AppProxyNativeAppSample.
  
- Plak de AppProxyNativeAppSample Redirect `<add key="ida:RedirectUri" value="" />` **URI** in het veld. U deze waarde (een URI) vinden en kopiëren via de pagina AppProxyNativeAppSample **Authentication.** 
  
- Plak de URI van de `<add key="todo:TodoListResourceId" value="" />` SecretAPI-toepassings-id in het veld. **Application ID URI** U deze waarde (een URI) vinden en kopiëren vanuit de SecretAPI **Expose an API-pagina.**
  
- Plak de **URL van** de `<add key="todo:TodoListBaseAddress" value="" />` startpagina secretapi in het veld. U deze waarde (een URL) vinden en kopiëren vanaf de pagina SecretAPI **Branding.**

Nadat u de parameters hebt geconfigureerd, bouwt en voert u de native app uit. Wanneer u de **knop Aanmelden** selecteert, u zich met de app aanmelden en vervolgens een successcherm weergeven om te bevestigen dat deze met de SecretAPI is verbonden.

![Geslaagd](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Een on-premises toepassing voor externe toegang toevoegen via toepassingsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md)
- [Snelstart: een clienttoepassing configureren om toegang te krijgen tot web-API's](../develop/quickstart-configure-app-access-web-apis.md)
- [Native clienttoepassingen inschakelen om te communiceren met proxytoepassingen](application-proxy-configure-native-client-application.md)
