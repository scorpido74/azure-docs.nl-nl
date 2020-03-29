---
title: Silent install Azure AD App Proxy connector | Microsoft Documenten
description: Voorziet hoe u een onbeheerde installatie van Azure AD Application Proxy Connector uitvoert om veilige externe toegang tot uw on-premises apps te bieden.
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
ms.date: 01/24/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b43d2de0a366d7e69a025b2e4e2998dccda2038e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756208"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Een onbeheerd installatiescript maken voor de Azure AD Application Proxy-connector

Met dit onderwerp u een Windows PowerShell-script maken waarmee u zonder toezicht installatie en registratie mogelijk maakt voor uw Azure AD Application Proxy-connector.

Deze mogelijkheid is handig wanneer u:

* Installeer de connector op Windows-servers die geen gebruikersinterface hebben ingeschakeld of die u niet openen met Extern bureaublad.
* Installeer en registreer veel connectoren tegelijk.
* Integreer de connectorinstallatie en -registratie als onderdeel van een andere procedure.
* Maak een standaardserverafbeelding die de verbindingsbits bevat, maar niet is geregistreerd.

Als de [toepassingsproxyconnector](application-proxy-connectors.md) werkt, moet deze worden geregistreerd bij uw Azure AD-map met behulp van een toepassingsbeheerder en wachtwoord. Normaal gesproken wordt deze informatie ingevoerd tijdens de installatie van connectoren in een pop-updialoogvenster, maar u PowerShell gebruiken om dit proces in plaats daarvan te automatiseren.

Er zijn twee stappen voor een installatie zonder toezicht. Installeer eerst de connector. Registreer de connector ten tweede bij Azure AD. 

## <a name="install-the-connector"></a>De connector installeren
Gebruik de volgende stappen om de connector te installeren zonder deze te registreren:

1. Open een opdrachtprompt.
2. Voer de volgende opdracht uit, waarbij de /q een stille installatie betekent. Een stille installatie vraagt u niet om de licentieovereenkomst voor eindgebruikers te accepteren.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>De connector registreren met Azure AD
Er zijn twee methoden die u gebruiken om de connector te registreren:

* De connector registreren met een Windows PowerShell-referentieobject
* De connector registreren met een offline token

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>De connector registreren met een Windows PowerShell-referentieobject
1. Maak een Object `$cred` Windows PowerShell-referenties met een beheerdersgebruikersnaam en wachtwoord voor uw directory. Voer de volgende opdracht * \<\> * uit en vervang gebruikersnaam en * \<wachtwoord:\>*
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Ga naar **C:\Program Files\Microsoft AAD App Proxy** Connector `$cred` en voer het volgende script uit met het object dat u hebt gemaakt:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>De connector registreren met een offline token
1. Maak een offline token met de klasse AuthenticationContext met de waarden in dit codefragment of PowerShell-cmdlets hieronder:

    **Using C#:**

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }

    **PowerShell gebruiken:**

        # Locate AzureAD PowerShell Module
        # Change Name of Module to AzureAD after what you have installed
        $AADPoshPath = (Get-InstalledModule -Name AzureAD).InstalledLocation
        # Set Location for ADAL Helper Library
        $ADALPath = $(Get-ChildItem -Path $($AADPoshPath) -Filter Microsoft.IdentityModel.Clients.ActiveDirectory.dll -Recurse ).FullName | Select-Object -Last 1
        
        # Add ADAL Helper Library
        Add-Type -Path $ADALPath
        
        #region constants
        
        # The AAD authentication endpoint uri
        [uri]$AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/token?api-version=1.0/" 
        
        # The application ID of the connector in AAD
        [string]$ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489"
        
        # The reply address of the connector application in AAD
        [uri]$ConnectorRedirectAddress = "urn:ietf:wg:oauth:2.0:oob" 
        
        # The AppIdUri of the registration service in AAD
        [uri]$RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp"
        
        #endregion
        
        #region GetAuthenticationToken
        
        # Set AuthN context
        $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $AadAuthenticationEndpoint
        
        # Build platform parameters
        $promptBehavior = [Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Always
        $platformParam = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList $promptBehavior
        
        # Do AuthN and get token
        $authResult = $authContext.AcquireTokenAsync($RegistrationServiceAppIdUri.AbsoluteUri, $ConnectorAppId, $ConnectorRedirectAddress, $platformParam).Result
        
        # Check AuthN result
        If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId) ) {
        $token = $authResult.AccessToken
        $tenantId = $authResult.TenantId
        }
        Else {
        Write-Output "Authentication result, token or tenant id returned are null"
        }
        
        #endregion

2. Zodra u het token hebt, maakt u een SecureString met behulp van het token:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Voer de volgende opdracht Windows \<PowerShell\> uit en vervang tenant-GUID door uw directory-id:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Volgende stappen 
* [Toepassingen publiceren met uw eigen domeinnaam (Engelstalig artikel)](application-proxy-configure-custom-domain.md)
* [Eenmalige aanmelding inschakelen](application-proxy-configure-single-sign-on-with-kcd.md)
* [Problemen met toepassingsproxy oplossen (Engelstalig artikel)](application-proxy-troubleshoot.md)


