---
title: Best practices voor Azure Service Fabric-beveiliging
description: Aanbevolen procedures en ontwerpoverwegingen voor het beveiligen van Azure Service Fabric-clusters en -toepassingen.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: dcdc338bdcdb2c04f6b8894ccb358bc773b95c07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258926"
---
# <a name="azure-service-fabric-security"></a>Azure Service Fabric-beveiliging 

Bekijk aanbevolen procedures voor [Azure Service Fabric-beveiliging voor](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices) meer informatie over aanbevolen procedures voor Azure [Security,](https://docs.microsoft.com/azure/security/)

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) is de aanbevolen service voor het beheer van geheimen voor Azure Service Fabric-toepassingen en -clusters.
> [!NOTE]
> Als certificaten/geheimen van een Key Vault worden geïmplementeerd in een virtuele machineschaalset als een geheim van de virtuele machineschaal, moeten de key vault en de virtuele machineschaalset naast elkaar worden geplaatst.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Certificaatautoriteit uitgegeven Service Fabric-certificaat maken

Een Azure Key Vault-certificaat kan worden gemaakt of geïmporteerd in een Key Vault. Wanneer een Key Vault-certificaat wordt gemaakt, wordt de privésleutel gemaakt in de Key Vault en nooit blootgesteld aan de eigenaar van het certificaat. Dit zijn de manieren om een certificaat te maken in Key Vault:

- Maak een zelfondertekend certificaat om een publiek-private sleutelpaar te maken en te koppelen aan een certificaat. Het certificaat wordt ondertekend door zijn eigen sleutel. 
- Maak handmatig een nieuw certificaat om een sleutelpaar voor publiek-privé te maken en een x.509-certificaatondertekeningsaanvraag te genereren. De ondertekeningsaanvraag kan worden ondertekend door uw registratieautoriteit of certificeringsinstantie. Het ondertekende x509-certificaat kan worden samengevoegd met het in behandeling zijnde sleutelpaar om het KV-certificaat in Key Vault te voltooien. Hoewel deze methode meer stappen vereist, biedt het u meer beveiliging omdat de privésleutel is gemaakt en beperkt tot Key Vault. Dit wordt uitgelegd in het onderstaande diagram. 

Controleer [methoden voor het maken van Azure Keyvault-certificaatsvoor](https://docs.microsoft.com/azure/key-vault/create-certificate) meer informatie.

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Key Vault-certificaten implementeren voor virtuele machineschaalsets van Service Fabric-cluster

Als u certificaten wilt implementeren van een co-locatie keyvault naar een virtuele machineschaalset, gebruikt u [OsProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile)van de virtuele machineschaalset . De volgende eigenschappen van de resourcemanager-sjabloon zijn:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> De kluis moet zijn ingeschakeld voor de implementatie van resourcebeheersjablonen.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Een Toegangscontrolelijst (ACL) toepassen op uw certificaat voor uw cluster servicestructuur

Uitgever Microsoft.Azure.ServiceFabric [met virtuele machineschaalset-extensies](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) wordt gebruikt om de beveiliging van uw knooppunten te configureren.
Als u een ACL wilt toepassen op uw certificaten voor uw servicestructuurclusterprocessen, gebruikt u de volgende eigenschappen van de resourcemanager-sjabloon:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Een clustercertificaat voor servicestructuur beveiligen op algemene naam

Als u uw cluster `Common Name`ServiceFabric per certificaat wilt beveiligen, gebruikt u het [eigenschappencertificaat Resourcebeheer Als](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames)volgt:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> Service fabricclusters gebruiken het eerste geldige certificaat dat wordt gevonden in het certificaatarchief van uw host. Op Windows is dit het certificaat met de laatste vervaldatum die overeenkomt met uw gemeenschappelijke naam en duimafdruk van de uitgever.

Azure-domeinen, zoals\<*\>UW SUBDOMEIN .cloudapp.azure.com of \<UW SUBDOMEIN\>.trafficmanager.net, zijn eigendom van Microsoft. Certificaatautoriteiten geven geen certificaten voor domeinen af aan onbevoegde gebruikers. De meeste gebruikers moeten een domein kopen bij een registrar of een geautoriseerde domeinbeheerder zijn, zodat een certificaatautoriteit u een certificaat met die gemeenschappelijke naam moet uitgeven.

Voor meer informatie over het configureren van DNS-service om uw domein op te lossen naar een Microsoft IP-adres, raadpleegt u hoe u Azure DNS configureert [om uw domein te hosten.](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)

> [!NOTE]
> Nadat u de naamservers van uw domeinen hebt delegeren aan uw Azure DNS-zonenaamservers, voegt u de volgende twee records toe aan uw DNS-zone:
> - Een 'A'-record voor domein `Alias record set` APEX die NIET een naar alle IP-adressen is, wordt uw aangepaste domein opgelost.
> - Een 'C'-record voor Microsoft-subdomeinen die `Alias record set`u hebt ingericht en die GEEN . U bijvoorbeeld de DNS-naam van uw Traffic Manager of Load Balancer gebruiken.

Als u uw portal wilt bijwerken om een `"managementEndpoint"`aangepaste DNS-naam voor uw cluster van servicestructuur weer te geven, werkt u de eigenschappen van de sjabloon Follow Service Fabric Cluster Resource Manager bij:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Geheime waarden voor versleutelingsstoffen voor fabric

Veelvoorkomende waarden die zijn versleuteld in Service Fabric-pakketten zijn ACR-referenties (Azure Container Registry), omgevingsvariabelen, instellingen en Azure Volume-opslagaccountsleutels voor plug-in-inhoud.

Ga [als u een versleutelingscertificaat instelt en geheimen versleutelt op Windows-clusters:](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows)

Een zelfondertekend certificaat genereren voor het versleutelen van uw geheim:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Gebruik de instructies in [De virtuele machineschaalsets van De Sleutelkluis implementeren voor servicefabriccluster](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) om Key Vault-certificaten te implementeren in de virtuele machineschaalsets van uw Service Fabric-cluster.

Versleutel uw geheim met de volgende PowerShell-opdracht en werk vervolgens het manifest van uw Service Fabric-toepassing bij met de gecodeerde waarde:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Een [versleutelingscertificaat instellen en geheimen versleutelen op Linux-clusters:](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux)

Genereer een zelfondertekend certificaat voor het versleutelen van uw geheimen:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Gebruik de instructies in [De sleutelkluiscertificaten implementeren voor virtuele machineschaalsets van het cluster Fabric](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) voor servicefabric voor de virtuele machineschaalsets van uw Service Fabric-cluster.

Versleutel uw geheim met behulp van de volgende opdrachten en werk vervolgens het Service Fabric Application Manifest bij met de gecodeerde waarde:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Nadat u uw beveiligde waarden hebt versleuteld, [geeft u versleutelde geheimen op in servicefabric-toepassing](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application)en [ontsleutelt u versleutelde geheimen van servicecode.](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code)

## <a name="include-certificate-in-service-fabric-applications"></a>Certificaat opnemen in Service Fabric-toepassingen

Om uw toepassing toegang te geven tot geheimen, neemt u het certificaat op door een **SecretsCertificate-element** toe te voegen aan het toepassingsmanifest.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Servicefabric-toepassingen verifiëren voor Azure Resources met behulp van Managed Service Identity (MSI)

Zie [Wat zijn beheerde identiteiten voor Azure-resources voor](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)meer informatie over beheerde identiteiten voor Azure-resources?
Azure Service Fabric-clusters worden gehost op virtuele machineschaalsets, die [Managed Service Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources)ondersteunen.
Zie Azure Services die Azure Active Directory Authentication [ondersteunen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication)als u een lijst met services wilt krijgen waarmee MSI kan worden geverifieerd.


Als u de beheerde identiteit van het systeem wilt inschakelen tijdens het maken `"Microsoft.Compute/virtualMachinesScaleSets"` van een schaalset voor virtuele machines of een bestaande schaalset voor virtuele machines, geeft u de volgende eigenschap aan:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Zie [Wat is beheerde identiteiten voor Azure-bronnen?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity)

Als u een [door de gebruiker toegewezen beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity)hebt gemaakt, verklaart u de volgende bron in uw sjabloon om deze toe te wijzen aan uw virtuele machineschaalset. Vervang `\<USERASSIGNEDIDENTITYNAME\>` de naam van de door de gebruiker toegewezen beheerde identiteit die u hebt gemaakt:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Voordat uw Service Fabric-toepassing gebruik kan maken van een beheerde identiteit, moeten machtigingen worden verleend aan de Azure Resources waarmee deze moet worden geverifieerd.
Met de volgende opdrachten wordt toegang tot een Azure-bron toegekend:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

Verkrijg in uw Service Fabric-toepassingscode [een toegangstoken](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) voor Azure Resource Manager door een REST te maken die allemaal vergelijkbaar is met het volgende:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

Uw Service Fabric-app kan vervolgens het toegangstoken gebruiken om te verifiëren naar Azure Resources die Active Directory ondersteunen.
In het volgende voorbeeld ziet u hoe u dit doet voor Cosmos DB-bron:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Windows-beveiligingsbasislijnen
[We raden u aan een branchestandaardconfiguratie te implementeren die algemeen bekend en goed is getest, zoals microsoft-beveiligingsbasislijnen, in plaats van zelf een basislijn te maken;](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines) een optie voor het inrichten van deze op uw Virtuele Machine Schaal sets is het gebruik van Azure Desired State Configuration (DSC) extensie handler, om de VM's te configureren als ze online komen, zodat ze de productiesoftware draaien.

## <a name="azure-firewall"></a>Azure Firewall
[Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-bronnen beschermt. Het is een volledig stateful firewall als een service met ingebouwde hoge beschikbaarheid en onbeperkte schaalbaarheid van de cloud.](https://docs.microsoft.com/azure/firewall/overview); Dit maakt de mogelijkheid om uitgaand HTTP/S-verkeer te beperken tot een opgegeven lijst met volledig gekwalificeerde domeinnamen (FQDN) inclusief wild cards. Deze functie vereist geen SSL-beëindiging. Het wordt aanbevolen om [Azure Firewall FQDN-tags](https://docs.microsoft.com/azure/firewall/fqdn-tags) voor Windows-updates te gebruiken en om netwerkverkeer naar Microsoft Windows Update-eindpunten in te schakelen, door uw firewall te stromen. [Azure Firewall implementeren met behulp van een sjabloon](https://docs.microsoft.com/azure/firewall/deploy-template) biedt een voorbeeld voor microsoft.Network/azureFirewalls resource template definition. Firewallregels die gemeenschappelijk zijn voor Service Fabric-toepassingen, is om het volgende toe te staan voor uw virtuele clustersnetwerk:

- *download.microsoft.com
- *servicefabric.azure.com
- *.core.windows.net

Deze firewallregels vormen een aanvulling op uw toegestane uitgaande netwerkbeveiligingsgroepen, waaronder ServiceFabric en Storage, zoals toegestaan bestemmingen van uw virtuele netwerk.

## <a name="tls-12"></a>TLS 1.2
[Tsg](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

Standaard is Windows Defender-antivirus geïnstalleerd op Windows Server 2016. Zie Windows [Defender Antivirus op Windows Server 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)voor meer informatie. De gebruikersinterface wordt standaard geïnstalleerd op een aantal SKU's, maar is niet vereist. Als u processen en paden voor opensourcesoftware wilt uitsluiten als u de impact op prestaties en het verbruik van resources door Windows Defender wilt verminderen en als u met uw beveiligingsbeleid processen en paden voor opensourcesoftware uitsluiten, geeft u de volgende extensiebron voor de bestandsgrootte van virtuele machines aan Beheer sjablooneigenschappen om uw cluster van Servicefabric uit te sluiten van scans:


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Raadpleeg uw antimalwaredocumentatie voor configuratieregels als u Geen Windows Defender gebruikt. Windows Defender wordt niet ondersteund op Linux.

## <a name="platform-isolation"></a>Platform isolatie
Standaard krijgen Service Fabric-toepassingen toegang tot de Runtime van De Service Fabric zelf, die zich in verschillende vormen manifesteert: [omgevingsvariabelen](service-fabric-environment-variables-reference.md) die verwijzen naar bestandspaden op de host die overeenkomen met toepassings- en fabricbestanden, een communicatieeindpunt tussen processen dat toepassingsspecifieke aanvragen accepteert, en het clientcertificaat waarvan Fabric verwacht dat de toepassing wordt gebruikt om zichzelf te verifiëren. In het geval dat de service zichzelf niet-vertrouwde code host, is het raadzaam om deze toegang tot de SF-runtime uit te schakelen - tenzij dit uitdrukkelijk nodig is. De toegang tot de runtime wordt verwijderd met de volgende verklaring in de sectie Beleid van het toepassingsmanifest: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Volgende stappen

* Maak een cluster op VM's of computers met Windows Server: [Service Fabric-clustercreatie voor Windows Server.](service-fabric-cluster-creation-for-windows-server.md)
* Een cluster maken op VM's of computers met Linux: [een Linux-cluster maken.](service-fabric-cluster-creation-via-portal.md)
* Meer informatie over [ondersteuningsopties voor Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
