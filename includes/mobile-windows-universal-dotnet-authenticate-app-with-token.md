---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: d71d52257b6e8cfa243207c9bfdb5c7de7d3dd37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176716"
---
1. Voeg in het MainPage.xaml.cs-projectbestand de volgende **instructies** toe:
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Vervang de **methode AuthenticateAsync** door de volgende code:
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: https://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Sign in with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider, "{url_scheme_of_your_app}");
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must sign in. Sign-In Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    In deze versie van **AuthenticateAsync**probeert de app referenties te gebruiken die zijn opgeslagen in de **PasswordVault** om toegang te krijgen tot de service. Een regelmatige aanmelding wordt ook uitgevoerd wanneer er geen opgeslagen referenties zijn.
   
   > [!NOTE]
   > Een token in de cache kan verlopen en tokenverloop kan ook plaatsvinden na verificatie wanneer de app in gebruik is. Zie [Controleren op verlopen verificatietokens voor](https://aka.ms/jww5vp)meer informatie over hoe u bepalen of een token is verlopen. Zie de [na-caching-tokens in Azure Mobile Services managed SDK](https://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx)voor een oplossing voor het afhandelen van autorisatiefouten in het verlopen van tokens. 
   > 
   > 
3. Start de app twee keer opnieuw.
   
    Merk op dat bij de eerste start-up, aanmelden bij de provider opnieuw verplicht is. Bij de tweede herstart worden de in de cache opgeslagen referenties echter gebruikt en wordt de aanmelding omzeild. 

