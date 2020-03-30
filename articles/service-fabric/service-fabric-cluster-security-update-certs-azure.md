---
title: Certificaten beheren in een Azure Service Fabric-cluster
description: Beschrijft hoe u nieuwe certificaten, rollovercertificaat en certificaat verwijderen aan of uit een cluster servicestructuur.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: a3c92e1b39261af32085e4d9b6cb2462d5c0eb64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458351"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Certificaten voor een Service Fabric-cluster in Azure toevoegen of verwijderen
Het wordt aanbevolen dat u vertrouwd bent met de manier waarop Service Fabric X.509-certificaten gebruikt en bekend bent met de [clusterbeveiligingsscenario's.](service-fabric-cluster-security.md) U moet begrijpen wat een clustercertificaat is en waarvoor u wordt gebruikt, voordat u verder gaat.

Het standaardcertificaatbelastingsgedrag van Azure Service Fabrics SDK is het implementeren en gebruiken van het gedefinieerde certificaat met de vervaldatum die het verst in de toekomst is; ongeacht de primaire of secundaire configuratiedefinitie. Terugvallen naar het klassieke gedrag is een niet aanbevolen geavanceerde actie en vereist het instellen van `Fabric.Code` de parameterwaarde 'UseSecondaryIfNewer' op false binnen uw configuratie.

Met servicefabric u naast clientcertificaten ook twee clustercertificaten opgeven, een primaire en een secundaire, wanneer u de certificaatbeveiliging configureert tijdens het maken van het cluster. Raadpleeg [het maken van een azure-cluster via portal](service-fabric-cluster-creation-via-portal.md) of [het maken van een azure-cluster via Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) voor meer informatie over het instellen ervan op het moment dat u deze op maakt. Als u slechts één clustercertificaat opgeeft bij het maken van tijd, wordt dat gebruikt als het primaire certificaat. Na het maken van het cluster u een nieuw certificaat toevoegen als secundair.

> [!NOTE]
> Voor een beveiligd cluster hebt u altijd ten minste één geldig (niet ingetrokken en niet verlopen) clustercertificaat (primair of secundair) geïmplementeerd (zo niet, het cluster werkt niet meer). 90 dagen voordat alle geldige certificaten verlopen, genereert het systeem een waarschuwingstrace en ook een waarschuwingsstatusgebeurtenis op het knooppunt. Er is momenteel geen e-mail of een andere melding die Service Fabric stuurt op dit artikel. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Een secundair clustercertificaat toevoegen met behulp van de portal
Secundair clustercertificaat kan niet worden toegevoegd via de Azure-portal, gebruik Azure powershell. Het proces wordt later in dit document beschreven.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Een clustercertificaat verwijderen met de portal
Voor een beveiligd cluster hebt u altijd ten minste één geldig (niet ingetrokken en niet verlopen) certificaat nodig. Het certificaat dat met de verste in de toekomstige vervaldatum wordt geïmplementeerd, wordt in gebruik genomen en als u het verwijderen verwijdert, werkt uw cluster niet meer. zorg ervoor dat alleen het verlopen certificaat wordt verwijderd of een ongebruikt certificaat dat het snelst verloopt.

Als u een ongebruikt clusterbeveiligingscertificaat wilt verwijderen, navigeert u naar de sectie Beveiliging en selecteert u de optie Verwijderen in het contextmenu in het ongebruikte certificaat.

Als het is uw bedoeling om het certificaat te verwijderen dat als primair is gemarkeerd, moet u een secundair certificaat implementeren met een vervaldatum verder in de toekomst dan het primaire certificaat, waardoor het gedrag voor automatische rollover wordt geactiveerd. het primaire certificaat verwijderen nadat de automatische rollover is voltooid.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Een secundair certificaat toevoegen met Resource Manager Powershell
> [!TIP]
> Er is nu een betere en eenvoudigere manier om een secundair certificaat toe te voegen met de [cmdlet Add-AzServiceFabricClusterCertificate.](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) U hoeft de rest van de stappen in deze sectie niet te volgen.  U hebt ook de oorspronkelijk gebruikte sjabloon niet nodig om het cluster te maken en te implementeren wanneer u de cmdlet [Add-AzServiceClusterCertificate](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) gebruikt.

In deze stappen wordt ervan uitgegaan dat u bekend bent met de manier waarop Resource Manager werkt en ten minste één cluster servicestructuur hebt geïmplementeerd met behulp van een resourcemanagersjabloon en dat de sjabloon die u hebt gebruikt om het cluster in te stellen, bij de hand hebt. Er wordt ook aangenomen dat u zich comfortabel voelt met JSON.

> [!NOTE]
> Als u op zoek bent naar een voorbeeldsjabloon en parameters die u gebruiken om mee te volgen of als uitgangspunt, download deze dan van deze [git-repo.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) 
> 
> 

### <a name="edit-your-resource-manager-template"></a>De sjabloon Resourcemanager bewerken

Voor het gemak van het volgen, voorbeeld 5-VM-1-NodeTypes-Secure_Step2.JSON bevat alle bewerkingen die we zullen maken. het monster is beschikbaar op [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample).

**Zorg ervoor dat u alle stappen volgt**

1. Open de resourcemanagersjabloon die u hebt gebruikt om uw cluster te implementeren. (Als u het voorbeeld hebt gedownload van de voorgaande repo, gebruikt u vervolgens 5-VM-1-NodeTypes-Secure_Step1.JSON om een beveiligd cluster te implementeren en die sjabloon vervolgens te openen).

2. Voeg **twee nieuwe parameters** "secCertificateThumbprint" en "secCertificateUrlValue" van het type "string" toe aan de parametersectie van uw sjabloon. U het volgende codefragment kopiëren en toevoegen aan de sjabloon. Afhankelijk van de bron van uw sjabloon, u deze al hebben gedefinieerd, zo ja, ga naar de volgende stap. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. Breng wijzigingen aan in de bron **Microsoft.ServiceFabric/clusters** - Zoek de brondefinitie 'Microsoft.ServiceFabric/clusters' in uw sjabloon. Onder eigenschappen van die definitie vindt u de JSON-tag 'Certificaat', die er ongeveer zo uit moet zien als het volgende JSON-fragment:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Voeg een nieuwe tag "thumbprintSecondary" toe en geef deze een waarde "[parameters('secCertificateThumbprint')".  

    Dus nu de resource definitie moet er uitzien als de volgende (afhankelijk van uw bron van de sjabloon, kan het niet precies zoals het fragment hieronder). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Als u het cert wilt **omrollen,** geeft u het nieuwe cert op als primair en verplaatst u de huidige primaire als secundaire. Dit resulteert in de rollover van uw huidige primaire certificaat naar het nieuwe certificaat in één implementatiestap.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Breng wijzigingen aan **in alle** resourcedefinities **van Microsoft.Compute/virtualMachineScaleSets** - Zoek de resourcedefinitie Microsoft.Compute/virtualMachineScaleSets. Schuif naar de "uitgever": "Microsoft.Azure.ServiceFabric", onder "virtualMachineProfile".

    In de instellingen van de Uitgever servicefabric ziet u zoiets als dit.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Voeg de nieuwe cert-vermeldingen toe
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    De eigenschappen moeten er nu zo uitzien
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Als u het cert wilt **omrollen,** geeft u het nieuwe cert op als primair en verplaatst u de huidige primaire als secundaire. Dit resulteert in de rollover van uw huidige certificaat naar het nieuwe certificaat in één implementatiestap.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    De eigenschappen moeten er nu zo uitzien    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Wijzigingen aanbrengen in **alle** resourcedefinities **van Microsoft.Compute/virtualMachineScaleSets** - Zoek de resourcedefinitie Microsoft.Compute/virtualMachineScaleSets. Scroll naar de "vaultCertificates": , onder "OSProfile". Het zou er ongeveer zo uit moeten zien.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Voeg de secCertificateUrlValue toe. gebruik het volgende fragment:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Nu moet de resulterende Json er ongeveer zo uitzien.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Zorg ervoor dat u stap 4 en 5 hebt herhaald voor alle nodetypes/Microsoft.Compute/virtualMachineScaleSets-brondefinities in uw sjabloon. Als u een van deze mist, wordt het certificaat niet geïnstalleerd op die virtuele machineschaalset en heeft u onvoorspelbare resultaten in uw cluster, inclusief het cluster dat naar beneden gaat (als u geen geldige certificaten krijgt die het cluster voor beveiliging kan gebruiken. Dus dubbel check, alvorens verder te gaan.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Uw sjabloonbestand bewerken om de nieuwe parameters weer te geven die u hierboven hebt toegevoegd
Als u het voorbeeld van de [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) gebruikt om mee te volgen, u wijzigingen aanbrengen in Het voorbeeld 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON 

Bewerk het parameterbestand Resourcemanager-sjabloon en voeg de twee nieuwe parameters voor secCertificateThumbprint en secCertificateUrlValue toe. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>De sjabloon implementeren in Azure

- U bent nu klaar om uw sjabloon te implementeren in Azure. Open een opdrachtprompt voor Azure PS-versie 1+.
- Meld u aan bij uw Azure-account en selecteer het specifieke azure-abonnement. Dit is een belangrijke stap voor mensen die toegang hebben tot meer dan één azure-abonnement.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Test de sjabloon voordat u deze implementeert. Gebruik dezelfde resourcegroep waarop uw cluster momenteel is geïmplementeerd.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Implementeer de sjabloon in uw resourcegroep. Gebruik dezelfde resourcegroep waarop uw cluster momenteel is geïmplementeerd. Voer de opdracht Nieuw-AzResourceGroupDeployment uit. U hoeft de modus niet op te geven, omdat de standaardwaarde **incrementeel**is.

> [!NOTE]
> Als u Modus instelt op Voltooien, u per ongeluk resources verwijderen die niet in uw sjabloon staan. Dus niet gebruiken in dit scenario.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Hier is een ingevuld voorbeeld van dezelfde powershell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Zodra de implementatie is voltooid, maakt u verbinding met uw cluster met het nieuwe certificaat en voert u enkele query's uit. Als je in staat bent om te doen. Vervolgens u het oude certificaat verwijderen. 

Als u een zelfondertekend certificaat gebruikt, vergeet ze dan niet te importeren in uw lokale TrustedPeople-cert-winkel.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Voor snelle referentie is hier de opdracht om verbinding te maken met een beveiligd cluster 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Voor snelle referentie hier is de opdracht om cluster status te krijgen

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Clientcertificaten implementeren in het cluster.

U dezelfde stappen gebruiken als in de voorgaande stappen 5 om de certificaten van een keyvault naar de knooppunten te laten implementeren. Je hoeft alleen maar te definiëren en gebruik maken van verschillende parameters.


## <a name="adding-or-removing-client-certificates"></a>Clientcertificaten toevoegen of verwijderen

Naast de clustercertificaten u clientcertificaten toevoegen om beheerbewerkingen uit te voeren op een Cluster Servicefabric.

U twee soorten clientcertificaten toevoegen: Beheerder of Alleen-lezen. Deze kunnen vervolgens worden gebruikt om de toegang tot de beheerbewerkingen en querybewerkingen op het cluster te beheren. Standaard worden de clustercertificaten toegevoegd aan de lijst met toegestane beheerderscertificaten.

u een willekeurig aantal clientcertificaten opgeven. Elke toevoeging/verwijdering resulteert in een configuratie-update voor het cluster ServiceFabric


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Clientcertificaten toevoegen - Beheerder of Alleen-lezen via portal

1. Navigeer naar de sectie Beveiliging en selecteer de knop '+ verificatie' boven aan de beveiligingssectie.
2. Kies in de sectie 'Verificatie toevoegen' de optie 'Verificatietype' - 'Alleen-lezen client' of 'Beheerclient'
3. Kies nu de methode Autorisatie. Dit geeft aan Service Fabric aan of het dit certificaat moet opzoeken met behulp van de onderwerpnaam of de duimafdruk. In het algemeen is het geen goede beveiligingspraktijk om de autorisatiemethode van de onderwerpnaam te gebruiken. 

![Clientcertificaat toevoegen][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Verwijderen van clientcertificaten - Beheerder of Alleen-lezen via de portal

Als u wilt voorkomen dat een secundair certificaat wordt gebruikt voor clusterbeveiliging, navigeert u naar de sectie Beveiliging en selecteert u de optie 'Verwijderen' in het contextmenu in het specifieke certificaat.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Toepassingscertificaten toevoegen aan een schaalset voor virtuele machines

Zie [dit voorbeeld Powershell-script](scripts/service-fabric-powershell-add-application-certificate.md)als u een certificaat voor uw toepassingen wilt implementeren in uw cluster.

## <a name="next-steps"></a>Volgende stappen
Lees deze artikelen voor meer informatie over clusterbeheer:

* [Upgradeproces van servicefabriccluster en verwachtingen van u](service-fabric-cluster-upgrade.md)
* [Toegang tot rollen instellen voor clients](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


