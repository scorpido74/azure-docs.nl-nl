---
title: Verificatie op basis van certificaten met Azure Cosmos DB en Active Directory
description: Meer informatie over het configureren van een Azure AD-identiteit voor verificatie op basis van certificaten voor toegangssleutels vanuit Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 085280a8064e4d12ac63939ada7cdb296d47dc70
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365779"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Verificatie op basis van certificaten voor een Azure AD-identiteit voor toegang tot sleutels vanuit een Azure Cosmos DB-account

Met verificatie op basis van certificaten kan de clienttoepassing worden geverifieerd door Azure AD (Azure Active Directory) te gebruiken met een clientcertificaat. U kunt verificatie op basis van certificaten uitvoeren op een computer waarvoor u een id nodig hebt, zoals een on-premises computer of virtuele machine in Azure. Uw toepassing kan vervolgens Azure Cosmos DB-sleutels lezen zonder dat de sleutels rechtstreeks in de toepassing staan. In dit artikel wordt beschreven hoe u een voorbeeld van Azure AD-toepassing maakt, deze configureert voor verificatie op basis van certificaten, u aanmeldt bij Azure met de nieuwe toepassingsidentiteit en vervolgens de sleutels ophaalt uit uw Azure Cosmos-account. In dit artikel wordt Azure PowerShell gebruikt om de identiteiten in te stellen en wordt een C#-voorbeeld-app verstrekt die sleutels verifieert en toegang heeft tot sleutels vanuit uw Azure Cosmos-account.  

## <a name="prerequisites"></a>Vereisten

* Installeer de [nieuwste versie](/powershell/azure/install-az-ps) van Azure PowerShell.

* Als u geen [Azure-abonnement](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

## <a name="register-an-app-in-azure-ad"></a>Een app bij Azure AD registreren

In deze stap registreert u een voorbeeldwebtoepassing in uw Azure AD-account. Deze toepassing wordt later gebruikt om de sleutels van uw Azure Cosmos DB-account te lezen. Gebruik de volgende stappen om een toepassing te registreren: 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Open het deelvenster Azure **Active Directory,** ga naar het deelvenster **App-registraties** en selecteer **Nieuwe registratie**. 

   ![Nieuwe toepassingsregistratie in Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Vul het **aanvraagformulier register** in met de volgende gegevens:  

   * **Naam** – Geef een naam op voor uw toepassing, het kan elke naam zijn, zoals "sampleApp".
   * **Ondersteunde accounttypen** : kies **alleen Accounts in deze organisatiemap (Standaardmap)** zodat resources in uw huidige map toegang krijgen tot deze toepassing. 
   * **URL omleiden** : kies toepassing van het type **Web** en geef een URL op waar uw toepassing wordt gehost, het kan elke URL zijn. In dit voorbeeld u een `https://sampleApp.com` test-URL opgeven, zoals het goed is, zelfs als de app niet bestaat.

   ![Een voorbeeldwebtoepassing registreren](./media/certificate-based-authentication/register-sample-web-app.png)

1. Selecteer **Registreren** nadat u het formulier hebt ingevuld.

1. Nadat de app is geregistreerd, maak een notitie van de **Applicatie(client) ID** en **Object ID**, zult u deze gegevens gebruiken in de volgende stappen. 

   ![De toepassings- en object--geïdentificeerde gegevens ophalen](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>De AzureAD-module installeren

In deze stap installeert u de Azure AD PowerShell-module. Deze module is vereist om de id van de toepassing die u in de vorige stap hebt geregistreerd, te verkrijgen en een zelfondertekend certificaat aan die toepassing te koppelen. 

1. Open Windows PowerShell ISE met beheerdersrechten. Als u dit nog niet hebt gedaan, installeert u de AZ PowerShell-module en maakt u verbinding met uw abonnement. Als u meerdere abonnementen hebt, u de context van het huidige abonnement instellen zoals weergegeven in de volgende opdrachten:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. De [AzureAD-module](/powershell/module/azuread/?view=azureadps-2.0) installeren en importeren

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Aanmelden bij uw Azure-advertentie

Meld u aan bij uw Azure AD waar u de toepassing hebt geregistreerd. Gebruik de opdracht Connect-AzureAD om u aan te melden bij uw account en voer uw Azure-accountreferenties in het pop-upvenster in. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Open een ander exemplaar van Windows PowerShell ISE en voer de volgende opdrachten uit om een zelfondertekend certificaat te maken en de sleutel te lezen die aan het certificaat is gekoppeld:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>De op certificaten gebaseerde referentie maken 

Voer vervolgens de volgende opdrachten uit om de object-id van uw toepassing op te halen en de op certificaten gebaseerde referenties te maken. In dit voorbeeld stellen we dat het certificaat na een jaar verloopt, u het instellen op elke vereiste einddatum.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

De bovenstaande opdracht resulteert in de uitvoer vergelijkbaar met de screenshot hieronder:

![Op certificaten gebaseerde uitvoer van referenties](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Uw Azure Cosmos-account configureren om de nieuwe identiteit te gebruiken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Als u naar uw Azure Cosmos-account navigeert, opent u het **IAM-blad (Access Control).**

1. Selecteer **Roltoewijzing toevoegen** en **toevoegen**. Voeg de voorbeeldapp toe die u in de vorige stap hebt gemaakt met de rol **Inzender** zoals weergegeven in de volgende schermafbeelding:

   ![Azure Cosmos-account configureren om de nieuwe identiteit te gebruiken](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Selecteer **Opslaan** nadat u het formulier hebt ingevuld

## <a name="register-your-certificate-with-azure-ad"></a>Uw certificaat registreren bij Azure AD

U de op certificaten gebaseerde referenties koppelen aan de clienttoepassing in Azure AD vanuit de Azure-portal. Als u de referentie wilt koppelen, moet u het certificaatbestand uploaden met de volgende stappen:

In de Azure-appregistratie voor de clienttoepassing:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Open het deelvenster Azure **Active Directory,** ga naar het deelvenster **App-registraties** en open de voorbeeld-app die u in de vorige stap hebt gemaakt. 

1. Selecteer **Certificaten & geheimen** en upload vervolgens het **certificaat**. Blader door het certificaatbestand dat u in de vorige stap hebt gemaakt om te uploaden.

1. Selecteer **Toevoegen**. Nadat het certificaat is geüpload, worden de duimafdruk, begindatum en vervaldatumwaarden weergegeven.

## <a name="access-the-keys-from-powershell"></a>Toegang tot de sleutels vanuit PowerShell

In deze stap meldt u zich aan bij Azure met behulp van de toepassing en het certificaat dat u hebt gemaakt en toegang tot de sleutels van uw Azure Cosmos-account. 

1. Maak in eerste instantie de referenties van het Azure-account wissen die u hebt gebruikt om u aan te melden bij uw account. U referenties wissen met behulp van de volgende opdracht:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Vervolgens u valideren dat u zich aanmelden bij azure portal met behulp van de referenties van de toepassing en toegang krijgen tot de Azure Cosmos DB-sleutels:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

In de vorige opdracht worden de primaire en secundaire hoofdsleutels van uw Azure Cosmos-account weergegeven. U het activiteitenlogboek van uw Azure Cosmos-account bekijken om te valideren dat het verzoek om sleutels ophalen is geslaagd en dat de gebeurtenis wordt gestart door de toepassing 'voorbeeldapp'.

![Het aanroepen van ophaalsleutels valideren in het Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)

## <a name="access-the-keys-from-a-c-application"></a>Toegang tot de sleutels vanuit een C#-toepassing 

U dit scenario ook valideren door toegang te krijgen tot sleutels vanuit een C#-toepassing. De volgende C#-consoletoepassing, die toegang heeft tot Azure Cosmos DB-sleutels met behulp van de app die is geregistreerd in Active Directory. Zorg ervoor dat u de tenantId, clientID, certName, resourcegroepnaam, abonnements-ID, Azure Cosmos-accountnaamgegevens bijwerkt voordat u de code uitvoert. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

Met dit script worden de primaire en secundaire hoofdtoetsen uitgevoerd, zoals in de volgende schermafbeelding wordt weergegeven:

![csharp-toepassingsuitvoer](./media/certificate-based-authentication/csharp-application-output.png)

Net als in de vorige sectie u het activiteitenlogboek van uw Azure Cosmos-account bekijken om te valideren dat de gebeurtenis aanvragen voor ophalen sleutels wordt gestart door de toepassing 'voorbeeldapp'. 


## <a name="next-steps"></a>Volgende stappen

* [Azure Cosmos-sleutels beveiligen met Azure Key Vault](access-secrets-from-keyvault.md)

* [Beveiligingsbasislijn voor Azure Cosmos DB](security-baseline.md)
