---
title: Niet-interactieve verificatie .NET-toepassing - Azure HDInsight
description: Meer informatie over het maken van niet-interactieve verificatie Microsoft .NET-toepassingen in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 5e6a0586bc750f8972586920c15dbb297295aa20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371270"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Een niet-interactieve verificatie .NET HDInsight-toepassing maken

Voer uw Microsoft .NET Azure HDInsight-toepassing uit onder de eigen identiteit van de toepassing (niet-interactief) of onder de identiteit van de aangemelde gebruiker van de toepassing (interactief). In dit artikel ziet u hoe u een niet-interactieve verificatie .NET-toepassing maakt om verbinding te maken met Azure en HDInsight te beheren. Zie [Verbinding maken met Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight)voor een voorbeeld van een interactieve toepassing.

Vanuit uw niet-interactieve .NET-toepassing hebt u het:

* Uw Azure-abonnementstenant-id (ook wel *directory-id*genoemd). Zie [Tenant-id opvragen](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* De Azure AD-toepassingsclient-id (Azure Active Directory). Zie [Een Azure Active Directory-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) en een [toepassings-id opdoen](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* De geheime sleutel voor azure AD-toepassingen. Zie [Verificatiesleutel voor toepassingen ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="prerequisites"></a>Vereisten

Een HDInsight-cluster. Zie de [aan de slag tutorial](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Een rol toewijzen aan de Azure AD-toepassing

Wijs uw Azure AD-toepassing een [rol](../role-based-access-control/built-in-roles.md)toe om deze machtigingen toe te kennen om acties uit te voeren. U het bereik instellen op het niveau van het abonnement, de resourcegroep of de resource. De machtigingen worden overgenomen naar lagere niveaus van bereik. Als u bijvoorbeeld een toepassing toevoegt aan de leesrol voor een resourcegroep, betekent dit dat de toepassing de brongroep en alle bronnen in de groep kan lezen. In dit artikel stelt u het bereik in op het niveau van de resourcegroep. Zie [Roltoewijzingen gebruiken om toegang tot uw Azure-abonnementsbronnen te beheren](../role-based-access-control/role-assignments-portal.md)voor meer informatie.

**De rol Eigenaar toevoegen aan de Azure AD-toepassing**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer naar de brongroep met het HDInsight-cluster waarop u uw Hive-query later in dit artikel uitvoert. Als u een groot aantal resourcegroepen hebt, u het filter gebruiken om de gewenste groep te vinden.
1. Selecteer **Access control (IAM) in**het menu Resourcegroep .
1. Selecteer het tabblad **Roltoewijzingen** om de huidige roltoewijzingen te bekijken.
1. Selecteer boven aan de pagina **+ Toevoegen**.
1. Volg de instructies om de rol Eigenaar toe te voegen aan uw Azure AD-toepassing. Nadat u de rol hebt toegevoegd, wordt de toepassing weergegeven onder de rol Eigenaar.

## <a name="develop-an-hdinsight-client-application"></a>Een HDInsight-clienttoepassing ontwikkelen

1. Maak een C#-consoletoepassing.
2. Voeg de volgende [NuGet-pakketten toe:](https://www.nuget.org/)

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Voer de volgende code uit:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```

## <a name="next-steps"></a>Volgende stappen

* [Maak een Azure Active Directory-toepassing en serviceprincipal in de Azure-portal.](../active-directory/develop/howto-create-service-principal-portal.md)
* Meer informatie over het [verifiëren van een serviceprincipal met Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Meer informatie over [Azure Role-Based Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md).
