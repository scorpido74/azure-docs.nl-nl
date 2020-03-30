---
title: Veilig verbinding maken met een Azure Service Fabric-cluster
description: Beschrijft hoe u de toegang van klanten tot een Service Fabric-cluster verifiëren en hoe u de communicatie tussen clients en een cluster beveiligen.
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: a1f4abbabe428a09492efefca4a8da9801b9f68d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258575"
---
# <a name="connect-to-a-secure-cluster"></a>Verbinding maken met een beveiligd cluster

Wanneer een client verbinding maakt met een clusterknooppunt van Service Fabric, kan de client worden geverifieerd en kan de communicatie worden beveiligd met behulp van certificaatbeveiliging of Azure Active Directory (AAD). Deze verificatie zorgt ervoor dat alleen geautoriseerde gebruikers toegang hebben tot het cluster en geïmplementeerde toepassingen en beheertaken kunnen uitvoeren.  Certificaat- of AAD-beveiliging moet eerder zijn ingeschakeld in het cluster toen het cluster werd gemaakt.  Zie [Clusterbeveiliging](service-fabric-cluster-security.md)voor meer informatie over clusterbeveiligingsscenario's. Als u verbinding maakt met een cluster dat is beveiligd met certificaten, [stelt u het clientcertificaat in](service-fabric-connect-to-secure-cluster.md#connectsecureclustersetupclientcert) op de computer die verbinding maakt met het cluster. 

<a id="connectsecureclustercli"></a> 

## <a name="connect-to-a-secure-cluster-using-azure-service-fabric-cli-sfctl"></a>Verbinding maken met een beveiligd cluster met Azure Service Fabric CLI (sfctl)

Er zijn een paar verschillende manieren om verbinding te maken met een beveiligd cluster met behulp van de Service Fabric CLI (sfctl). Als u ter verificatie een clientcertificaat gebruikt, moeten de certificaatgegevens overeenkomen met een certificaat dat is geïmplementeerd in de clusterknooppunten. Als uw certificaat certificaatcertificaatautoriteiten (CA's) heeft, moet u bovendien de vertrouwde CV's opgeven.

U verbinding maken `sfctl cluster select` met een cluster met de opdracht.

Clientcertificaten kunnen op twee verschillende manieren worden opgegeven, als cert en sleutelpaar, of als één PFX-bestand. Voor met een wachtwoord beveiligde PEM-bestanden wordt u automatisch gevraagd het wachtwoord in te voeren. Als u het clientcertificaat als PFX-bestand hebt verkregen, converteert u het PFX-bestand eerst naar een PEM-bestand met de volgende opdracht. 

```shell
openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
```

Als uw .pfx-bestand niet met een wachtwoord is beveiligd, gebruikt u -passin-pas: voor de laatste parameter.

Als u het clientcertificaat als pembestand wilt `--pem` opgeven, geeft u het bestandspad in het argument op. Bijvoorbeeld:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

Met een wachtwoord beveiligde pem-bestanden wordt om een wachtwoord gevraagd voordat u een opdracht uitvoert.

Als u een cert wilt `--cert` `--key` opgeven, gebruikt sleutelpaar de argumenten en argumenten om de bestandspaden voor elk desbetreffend bestand op te geven.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --cert ./client.crt --key ./keyfile.key
```

Soms slagen certificaten die worden gebruikt om test- of dev-clusters te beveiligen, als certificaatvalidatie. Als u de verificatie `--no-verify` van het certificaat wilt omzeilen, geeft u de optie op. Bijvoorbeeld:

> [!WARNING]
> Gebruik de `no-verify` optie niet wanneer u verbinding maakt met productieservicefabricclusters.

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --no-verify
```

Daarnaast u paden opgeven naar mappen van vertrouwde CA-certs of afzonderlijke certs. Als u deze paden `--ca` wilt opgeven, gebruikt u het argument. Bijvoorbeeld:

```shell
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem --ca ./trusted_ca
```

Nadat u verbinding hebt gemaakt, moet u [andere sfctl-opdrachten](service-fabric-cli.md) kunnen uitvoeren om met het cluster te communiceren.

<a id="connectsecurecluster"></a>

## <a name="connect-to-a-cluster-using-powershell"></a>Verbinding maken met een cluster met PowerShell
Voordat u bewerkingen uitvoert op een cluster via PowerShell, maakt u eerst een verbinding met het cluster. De clusterverbinding wordt gebruikt voor alle volgende opdrachten in de opgegeven PowerShell-sessie.

### <a name="connect-to-an-unsecure-cluster"></a>Verbinding maken met een onveilig cluster

Als u verbinding wilt maken met een onveilig cluster, geeft u het eindpuntadres van het clusteradres op naar de opdracht **Connect-ServiceFabricCluster:**

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 
```

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Verbinding maken met een beveiligd cluster met Azure Active Directory

Als u verbinding wilt maken met een beveiligd cluster dat Azure Active Directory gebruikt om toegang tot clusterbeheerders te autoriseren, geeft u de duimafdruk van het clustercertificaat op en gebruikt u de *AzureActiveDirectory-vlag.*  

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
-ServerCertThumbprint <Server Certificate Thumbprint> `
-AzureActiveDirectory
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Verbinding maken met een beveiligd cluster met behulp van een clientcertificaat
Voer de volgende PowerShell-opdracht uit om verbinding te maken met een beveiligd cluster dat clientcertificaten gebruikt om beheerderstoegang te autoriseren. 

#### <a name="connect-using-certificate-common-name"></a>Verbinding maken met de algemene naam van het certificaat
Geef de algemene naam van het clustercertificaat op en de algemene naam van het clientcertificaat waarvoor machtigingen zijn verleend voor clusterbeheer. De certificaatgegevens moeten overeenkomen met een certificaat op de clusterknooppunten.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName <certificate common name>  `
    -FindType FindBySubjectName `
    -FindValue <certificate common name> `
    -StoreLocation CurrentUser `
    -StoreName My 
```
*ServerCommonName* is de algemene naam van het servercertificaat dat op de clusterknooppunten is geïnstalleerd. *FindValue* is de algemene naam van het beheerdersclientcertificaat. Wanneer de parameters zijn ingevuld, ziet de opdracht eruit als het volgende voorbeeld:
```powershell
$ClusterName= "sf-commonnametest-scus.southcentralus.cloudapp.azure.com:19000"
$certCN = "sfrpe2eetest.southcentralus.cloudapp.azure.com"

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCommonName $certCN  `
    -FindType FindBySubjectName `
    -FindValue $certCN `
    -StoreLocation CurrentUser `
    -StoreName My 
```

#### <a name="connect-using-certificate-thumbprint"></a>Verbinding maken met de duimafdruk van het certificaat
Geef de duimafdruk van het clustercertificaat en de duimafdruk op van het clientcertificaat waarvoor machtigingen zijn verleend voor clusterbeheer. De certificaatgegevens moeten overeenkomen met een certificaat op de clusterknooppunten.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `  
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `  
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* is de duimafdruk van het servercertificaat dat op de clusterknooppunten is geïnstalleerd. *FindValue* is de duimafdruk van het beheerdersclientcertificaat.  Wanneer de parameters zijn ingevuld, ziet de opdracht eruit als het volgende voorbeeld:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `  
          -KeepAliveIntervalInSec 10 `  
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `  
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `  
          -StoreLocation CurrentUser -StoreName My 
```

### <a name="connect-to-a-secure-cluster-using-windows-active-directory"></a>Verbinding maken met een beveiligd cluster met Windows Active Directory
Als uw zelfstandige cluster is geïmplementeerd met AD-beveiliging, maakt u verbinding met het cluster door de switch 'WindowsCredential' toe te beelden.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -WindowsCredential
```

<a id="connectsecureclusterfabricclient"></a>

## <a name="connect-to-a-cluster-using-the-fabricclient-apis"></a>Verbinding maken met een cluster met de FabricClient API's
De Service Fabric SDK biedt de [FabricClient-klasse](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) voor clusterbeheer. Als u de FabricClient API's wilt gebruiken, krijgt u het Microsoft.ServiceFabric NuGet-pakket.

### <a name="connect-to-an-unsecure-cluster"></a>Verbinding maken met een onveilig cluster

Als u verbinding wilt maken met een extern onbeveiligd cluster, maakt u een FabricClient-instantie en geeft u het clusteradres op:

```csharp
FabricClient fabricClient = new FabricClient("clustername.westus.cloudapp.azure.com:19000");
```

Voor code die wordt uitgevoerd vanuit een cluster, bijvoorbeeld in een betrouwbare service, maakt u een FabricClient *zonder* het clusteradres op te geven. FabricClient maakt verbinding met de lokale beheergateway op het knooppunt waarop de code momenteel wordt uitgevoerd, waardoor een extra netwerkhop wordt vermeden.

```csharp
FabricClient fabricClient = new FabricClient();
```

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Verbinding maken met een beveiligd cluster met behulp van een clientcertificaat

De knooppunten in het cluster moeten beschikken over geldige certificaten waarvan de algemene naam of DNS-naam in SAN wordt weergegeven in de [eigenschap RemoteCommonNames](https://docs.microsoft.com/dotnet/api/system.fabric.x509credentials) op [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient). Het volgen van dit proces maakt wederzijdse verificatie tussen de client en de clusterknooppunten mogelijk.

```csharp
using System.Fabric;
using System.Security.Cryptography.X509Certificates;

string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
var fc = new FabricClient(xc, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "My";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = clientCertThumb;
    xc.RemoteCommonNames.Add(name);
    xc.RemoteCertThumbprints.Add(serverCertThumb);
    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```

### <a name="connect-to-a-secure-cluster-interactively-using-azure-active-directory"></a>Op interactieve wijze verbinding maken met een beveiligd cluster met Azure Active Directory

In het volgende voorbeeld wordt Azure Active Directory gebruikt voor clientidentiteit en servercertificaat voor serveridentiteit.

Er verschijnt automatisch een dialoogvenstervenster voor interactieve aanmelding bij het maken van verbinding met het cluster.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}
```

### <a name="connect-to-a-secure-cluster-non-interactively-using-azure-active-directory"></a>Verbinding maken met een beveiligd cluster dat niet interactief is met Azure Active Directory

Het volgende voorbeeld is gebaseerd op Microsoft.IdentityModel.Clients.ActiveDirectory, Versie: 2.19.208020213.

Zie [Microsoft.IdentityModel.Clients.ActiveDirectory](https://msdn.microsoft.com/library/microsoft.identitymodel.clients.activedirectory.aspx)voor meer informatie over het werven van AAD-token.

```csharp
string tenantId = "C15CFCEA-02C1-40DC-8466-FBD0EE0B05D2";
string clientApplicationId = "118473C2-7619-46E3-A8E4-6DA8D5F56E12";
string webApplicationId = "53E6948C-0897-4DA6-B26A-EE2A38A690B4";

string token = GetAccessToken(
    tenantId,
    webApplicationId,
    clientApplicationId,
    "urn:ietf:wg:oauth:2.0:oob");

string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);
claimsCredentials.LocalClaims = token;

var fc = new FabricClient(claimsCredentials, connection);

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(
    string tenantId,
    string resource,
    string clientId,
    string redirectUri)
{
    string authorityFormat = @"https://login.microsoftonline.com/{0}";
    string authority = string.Format(CultureInfo.InvariantCulture, authorityFormat, tenantId);
    var authContext = new AuthenticationContext(authority);

    var authResult = authContext.AcquireToken(
        resource,
        clientId,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

### <a name="connect-to-a-secure-cluster-without-prior-metadata-knowledge-using-azure-active-directory"></a>Verbinding maken met een beveiligd cluster zonder kennis van voorafgaande metagegevens met Azure Active Directory

In het volgende voorbeeld wordt gebruik gemaakt van niet-interactieve tokenacquisitie, maar dezelfde benadering kan worden gebruikt om een aangepaste interactieve tokenacquisitie-ervaring op te bouwen. De Azure Active Directory-metagegevens die nodig zijn voor tokenacquisitie, worden gelezen uit clusterconfiguratie.

```csharp
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string connection = "clustername.westus.cloudapp.azure.com:19000";

var claimsCredentials = new ClaimsCredentials();
claimsCredentials.ServerThumbprints.Add(serverCertThumb);

var fc = new FabricClient(claimsCredentials, connection);

fc.ClaimsRetrieval += (o, e) =>
{
    return GetAccessToken(e.AzureActiveDirectoryMetadata);
};

try
{
    var ret = fc.ClusterManager.GetClusterManifestAsync().Result;
    Console.WriteLine(ret.ToString());
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static string GetAccessToken(AzureActiveDirectoryMetadata aad)
{
    var authContext = new AuthenticationContext(aad.Authority);

    var authResult = authContext.AcquireToken(
        aad.ClusterApplication,
        aad.ClientApplication,
        new UserCredential("TestAdmin@clustenametenant.onmicrosoft.com", "TestPassword"));
    return authResult.AccessToken;
}

```

<a id="connectsecureclustersfx"></a>

## <a name="connect-to-a-secure-cluster-using-service-fabric-explorer"></a>Verbinding maken met een beveiligd cluster met Service Fabric Explorer
Als u [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) voor een bepaald cluster wilt bereiken, wijst u uw browser aan:

`http://<your-cluster-endpoint>:19080/Explorer`

De volledige URL is ook beschikbaar in het deelvenster clusterbenodigdheden van de Azure-portal.

Als u verbinding wilt maken met een beveiligd cluster op Windows of OS X via een browser, u het clientcertificaat importeren en vraagt de browser u om het certificaat te gebruiken voor verbinding maken met het cluster.  Op Linux-machines moet het certificaat worden geïmporteerd met behulp van geavanceerde browserinstellingen (elke browser heeft verschillende mechanismen) en het naar de certificaatlocatie op schijf wijzen. Lees [Een clientcertificaat instellen](#connectsecureclustersetupclientcert) voor meer informatie.

### <a name="connect-to-a-secure-cluster-using-azure-active-directory"></a>Verbinding maken met een beveiligd cluster met Azure Active Directory

Als u verbinding wilt maken met een cluster dat is beveiligd met AAD, wijst u uw browser aan:

`https://<your-cluster-endpoint>:19080/Explorer`

U wordt automatisch gevraagd om u aan te melden bij AAD.

### <a name="connect-to-a-secure-cluster-using-a-client-certificate"></a>Verbinding maken met een beveiligd cluster met behulp van een clientcertificaat

Als u verbinding wilt maken met een cluster dat is beveiligd met certificaten, wijst u uw browser aan:

`https://<your-cluster-endpoint>:19080/Explorer`

U wordt automatisch gevraagd om een clientcertificaat te selecteren.

<a id="connectsecureclustersetupclientcert"></a>

## <a name="set-up-a-client-certificate-on-the-remote-computer"></a>Een clientcertificaat instellen op de externe computer

Ten minste twee certificaten moeten worden gebruikt voor het beveiligen van het cluster, een voor het cluster- en servercertificaat en een certificaat voor clienttoegang.  We raden u aan ook aanvullende secundaire certificaten en clienttoegangscertificaten te gebruiken.  Om de communicatie tussen een client en een clusterknooppunt te beveiligen met behulp van certificaatbeveiliging, moet u eerst het clientcertificaat verkrijgen en installeren. Het certificaat kan worden geïnstalleerd in de persoonlijke (Mijn) winkel van de lokale computer of de huidige gebruiker.  U hebt ook de duimafdruk van het servercertificaat nodig, zodat de client het cluster kan verifiëren.

* Voor Windows: dubbelklik op het PFX-bestand en volg de aanwijzingen om het certificaat in uw persoonlijke archief, `Certificates - Current User\Personal\Certificates`, te installeren. U ook de opdracht PowerShell gebruiken:

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

    Als het een zelfondertekend certificaat is, moet u het importeren in het winkel 'vertrouwde mensen' van uw machine voordat u dit certificaat gebruiken om verbinding te maken met een beveiligd cluster.

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
    -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
    -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

* Voor Mac: dubbelklik op het PFX-bestand en volg de aanwijzingen om het certificaat in uw Sleutelhanger te installeren.

## <a name="next-steps"></a>Volgende stappen

* [Upgradeproces van servicefabriccluster en verwachtingen van u](service-fabric-cluster-upgrade.md)
* [Uw Service Fabric-toepassingen beheren in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Introductie van servicefabrichealthmodel](service-fabric-health-introduction.md)
* [Toepassingsbeveiliging en runas](service-fabric-application-runas-security.md)
* [Aan de slag met de Service Fabric-CLI](service-fabric-cli.md)
