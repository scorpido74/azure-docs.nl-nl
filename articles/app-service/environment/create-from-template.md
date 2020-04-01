---
title: Een ASE maken met ARM
description: Meer informatie over het maken van een externe of ILB-appserviceomgeving met behulp van een Azure Resource Manager-sjabloon.
author: ccompy
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e06fcdbac097e85c039e34274c61cb51ee06bcd6
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478317"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Een ASE maken met behulp van een Azure Resource Manager-sjabloon

## <a name="overview"></a>Overzicht

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure App Service-omgevingen (ASEs) kunnen worden gemaakt met een internettoegankelijk eindpunt of een eindpunt op een intern adres in een Virtueel Azure-netwerk (VNet). Wanneer dit is gemaakt met een intern eindpunt, wordt dat eindpunt geleverd door een Azure-component, een iLB (internal load balancer). De ASE op een intern IP-adres wordt een ILB ASE genoemd. De ASE met een openbaar eindpunt wordt een externe ASE genoemd. 

Een ASE kan worden gemaakt met behulp van de Azure-portal of een Azure Resource Manager-sjabloon. In dit artikel worden de stappen en syntaxis doorlopen die u nodig hebt om een externe ASE- of ILB ASE-sjablonen te maken. Zie [Een externe ASE][MakeExternalASE] maken of [Een ILB ASE maken][MakeILBASE]voor meer informatie over het maken van een ASE in de Azure-portal.

Wanneer u een ASE maakt in de Azure-portal, u tegelijkertijd uw VNet maken of een reeds bestaande VNet kiezen om in te implementeren. Wanneer u een ASE maakt op basis van een sjabloon, moet u beginnen met: 

* Een Resource Manager VNet.
* Een subnet in dat VNet. We raden een ASE-subnetgrootte aan van `/24` 256 adressen om tegemoet te komen aan toekomstige groei- en schaalbehoeften. Nadat de ASE is gemaakt, u de grootte niet wijzigen.
* De resource-ID van uw VNet. U deze informatie ophalen via de Azure-portal onder uw virtuele netwerkeigenschappen.
* Het abonnement dat u wilt implementeren.
* De locatie die u wilt implementeren.

Ga als het gaat om het automatiseren van uw ASE-creatie:

1. Maak de ASE op basis van een sjabloon. Als u een externe ASE maakt, bent u na deze stap klaar. Als u een ILB ASE maakt, zijn er nog een paar dingen te doen.

2. Nadat uw ILB ASE is gemaakt, wordt een TLS/SSL-certificaat geüpload dat overeenkomt met uw ILB ASE-domein.

3. Het geüploade TLS/SSL-certificaat wordt aan de ILB ASE toegewezen als het 'standaard' TLS/SSL-certificaat.  Dit certificaat wordt gebruikt voor TLS/SSL-verkeer naar apps op de ILB ASE wanneer ze het `https://someapp.mycustomrootdomain.com`gemeenschappelijke hoofddomein gebruiken dat is toegewezen aan de ASE (bijvoorbeeld).


## <a name="create-the-ase"></a>De ASE maken
Een resourcemanagersjabloon waarmee een ASE wordt gemaakt en het bijbehorende parametersbestand is beschikbaar [in een voorbeeld][quickstartasev2create] op GitHub.

Als u een ILB ASE wilt maken, gebruikt u deze voorbeelden van [resourcebeheersjablonen][quickstartilbasecreate]. Ze richten zich op die use case. De meeste parameters in het bestand *azuredeploy.parameters.json* zijn gemeenschappelijk voor het maken van ILB ASEs en Externe ASE's. In de volgende lijst worden parameters van speciale notitie of die uniek zijn, opgeroepen wanneer u een ILB ASE maakt:

* *internalLoadBalancingMode*: Stel dit in de meeste gevallen in op 3, wat betekent dat zowel HTTP/HTTPS-verkeer op poorten 80/443 als de poorten van het besturings-/datakanaal waarnaar wordt geluisterd door de FTP-service op de ASE, gebonden zijn aan een intern adres met ILB toegewezen virtueel netwerk. Als deze eigenschap is ingesteld op 2, zijn alleen de FTP-servicegerelateerde poorten (zowel besturing als gegevenskanalen) gebonden aan een ILB-adres. Het HTTP/HTTPS-verkeer blijft op de openbare VIP.
* *dnsSuffix*: Deze parameter definieert het standaardhoofddomein dat aan de ASE is toegewezen. In de openbare variant van Azure App Service is het standaardhoofddomein voor alle web-apps *azurewebsites.net.* Omdat een ILB ASE intern is in het virtuele netwerk van een klant, heeft het geen zin om het standaardhoofddomein van de openbare dienst te gebruiken. In plaats daarvan moet een ILB ASE een standaardhoofddomein hebben dat zinvol is voor gebruik binnen het interne virtuele netwerk van een bedrijf. Contoso Corporation kan bijvoorbeeld een standaardhoofddomein van *internal-contoso.com* gebruiken voor apps die alleen binnen het virtuele netwerk van Contoso oplosbaar en toegankelijk zijn. 
* *ipSslAddressCount*: Deze parameter standaard wordt automatisch standaard een waarde van 0 in het *azuredeploy.json-bestand* omdat ILB ASEs slechts één ILB-adres hebben. Er zijn geen expliciete IP-SSL-adressen voor een ILB ASE. Daarom moet de IP-SSL-adresgroep voor een ILB ASE op nul worden ingesteld. Anders treedt een indikfout op. 

Nadat het bestand *azuredeploy.parameters.json* is ingevuld, maakt u de ASE met behulp van het PowerShell-codefragment. Wijzig de bestandspaden die overeenkomen met de locatie van de resourcemanager-sjabloonbestandslocaties op uw machine. Vergeet niet om uw eigen waarden te leveren voor de implementatienaam Resource Manager en de naam van de resourcegroep:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Het duurt ongeveer een uur voor de ASE wordt gemaakt. Vervolgens verschijnt de ASE in de portal in de lijst met ASE's voor het abonnement dat de implementatie heeft geactiveerd.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Het 'standaard' TLS/SSL-certificaat uploaden en configureren
Een TLS/SSL-certificaat moet aan de ASE worden gekoppeld als het 'standaard' TLS/SSL-certificaat dat wordt gebruikt om TLS-verbindingen met apps tot stand te brengen. Als het standaard DNS-achtervoegsel *internal-contoso.com*van de `https://some-random-app.internal-contoso.com` ASE internal-contoso.com is, vereist een verbinding met een TLS/SSL-certificaat dat geldig is voor **.internal-contoso.com*. 

Verkrijg een geldig TLS/SSL-certificaat door gebruik te maken van interne certificaatautoriteiten, een certificaat te kopen bij een externe uitgever of een zelfondertekend certificaat te gebruiken. Ongeacht de bron van het TLS/SSL-certificaat moeten de volgende certificaatkenmerken correct zijn geconfigureerd:

* **Onderwerp**: Dit kenmerk moet worden ingesteld op **.your-root-domain-here.com*.
* **Alternatieve naam van het onderwerp:** dit kenmerk moet zowel **.your-root-domain-here.com* als **.scm.your-root-domain-here.com*bevatten. TLS-verbindingen met de SCM/Kudu-site die aan elke app zijn gekoppeld, gebruiken een adres van het *formulier your-app-name.scm.your-root-domain-here.com*.

Met een geldig TLS/SSL-certificaat in de hand zijn twee extra voorbereidende stappen nodig. Converteer/sla het TLS/SSL-certificaat op als een .pfx-bestand. Houd er rekening mee dat het .pfx-bestand alle tussenliggende en hoofdcertificaten moet bevatten. Beveilig het bestand met een wachtwoord.

Het .pfx-bestand moet worden geconverteerd naar een base64-tekenreeks omdat het TLS/SSL-certificaat wordt geüpload met behulp van een Resource Manager-sjabloon. Omdat Resource Manager-sjablonen tekstbestanden zijn, moet het PFX-bestand worden geconverteerd naar een base64-tekenreeks. Op deze manier kan het worden opgenomen als een parameter van de sjabloon.

Gebruik het volgende PowerShell-codefragment om:

* Een zelfondertekend certificaat genereren.
* Exporteer het certificaat als een .pfx-bestand.
* Converteer het .pfx-bestand in een door basis64 gecodeerde tekenreeks.
* Sla de tekenreeks met basis64 op in een afzonderlijk bestand. 

Deze PowerShell-code voor base64-codering is aangepast van de [PowerShell-scriptsblog:][examplebase64encoding]

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Nadat het TLS/SSL-certificaat is gegenereerd en geconverteerd naar een door een basis64-gecodeerde tekenreeks, gebruikt u het voorbeeld resourcebeheersjabloon [Configureer het standaard SSL-certificaat][quickstartconfiguressl] op GitHub. 

De parameters in het bestand *azuredeploy.parameters.json* worden hier weergegeven:

* *appServiceEnvironmentName:* de naam van de ILB ASE die is geconfigureerd.
* *existingAseLocation*: Teksttekenreeks met het Azure-gebied waar de ILB ASE is geïmplementeerd.  Bijvoorbeeld: "South Central US".
* *pfxBlobString:* de op basis 64 gecodeerde tekenreeksweergave van het PFX-bestand. Gebruik het eerder getoonde codefragment en kopieer de tekenreeks in "exportedcert.pfx.b64". Plak het in als de waarde van het *kenmerk pfxBlobString.*
* *wachtwoord*: Het wachtwoord dat wordt gebruikt om het .pfx-bestand te beveiligen.
* *certificateThumbprint*: De duimafdruk van het certificaat. Als u deze waarde ophaalt uit PowerShell (bijvoorbeeld *$certificate. Duimafdruk* van het eerdere codefragment), u de waarde gebruiken zoals het is. Als u de waarde uit het dialoogvenster Windows-certificaat kopieert, moet u de externe ruimten niet uitdeinen. De *certificaatThumbprint* moet er ongeveer uitzien als AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName:* een vriendelijke tekenreeks-id van uw eigen keuze die wordt gebruikt om het certificaat te identificeren. De naam wordt gebruikt als onderdeel van de unieke Resource Manager-id voor de entiteit *Microsoft.Web/certificaten* die het TLS/SSL-certificaat vertegenwoordigt. De naam *moet* eindigen met \_het volgende achtervoegsel: yourASENameHere_InternalLoadBalancingASE. De Azure-portal gebruikt dit achtervoegsel als indicator dat het certificaat wordt gebruikt om een ASE met ILB-poort te beveiligen.

Hier wordt een verkort voorbeeld van *azuredeploy.parameters.json* weergegeven:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

Nadat het *bestand azuredeploy.parameters.json* is ingevuld, configureert u het standaard TLS/SSL-certificaat met behulp van het PowerShell-codefragment. Wijzig de bestandspaden die overeenkomen met de plaats waar de sjabloonbestanden van Resource Manager zich op uw machine bevinden. Vergeet niet om uw eigen waarden te leveren voor de implementatienaam Resource Manager en de naam van de resourcegroep:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Het duurt ongeveer 40 minuten per ASE front-end om de wijziging toe te passen. Voor een ASE op standaardformaat die twee fronteinden gebruikt, duurt het bijvoorbeeld ongeveer een uur en 20 minuten voordat de sjabloon is voltooid. Terwijl de sjabloon wordt uitgevoerd, kan de ASE niet schalen.  

Nadat de sjabloon is voltooid, zijn apps op de ILB ASE toegankelijk via HTTPS. De verbindingen worden beveiligd met behulp van het standaard TLS/SSL-certificaat. Het standaard TLS/SSL-certificaat wordt gebruikt wanneer apps op de ILB ASE worden geadresseerd met behulp van een combinatie van de toepassingsnaam plus de standaardhostnaam. `https://mycustomapp.internal-contoso.com` Gebruikt bijvoorbeeld het standaard TLS/SSL-certificaat voor **.internal-contoso.com*.

Net als apps die op de openbare multitenant-service worden uitgevoerd, kunnen ontwikkelaars echter aangepaste hostnamen configureren voor afzonderlijke apps. Ze kunnen ook unieke SNI TLS/SSL-certificaatbindingen configureren voor afzonderlijke apps.

## <a name="app-service-environment-v1"></a>App Service-omgeving v1 ##
App Service Environment heeft twee versies: ASEv1 en ASEv2. De voorgaande informatie is gebaseerd op ASEv2. In deze sectie leest u wat de verschillen zijn tussen ASEv1 en ASEv2.

In ASEv1 beheert u alle resources handmatig. Dit geldt ook voor de front-ends, werkrollen en IP-adressen die worden gebruikt voor op IP gebaseerd SSL. Voordat u uw App Service-abonnement uitschalen, moet u de werknemersgroep die u wilt hosten, uitschalen.

ASEv1 maakt gebruik van een ander prijsmodel dan ASEv2. In ASEv1 betaalt u voor elke toegewezen vCPU. Dat geldt ook voor vCPU's die worden gebruikt voor front-ends of werknemers die geen workloads hosten. In ASEv1 is de maximale schaalgrootte voor een AS-omgeving standaard 55 hosts (in totaal). Dit is inclusief werkrollen en front-ends. Een voordeel van ASEv1 is dat deze versie kan worden geïmplementeerd in een klassiek virtueel netwerk en een virtueel netwerk van Resource Manager. Zie [Inleiding tot App Service Environment v1][ASEv1Intro] voor meer informatie over ASEv1.

Zie [Een ILB ASE v1 maken met een resourcemanagersjabloon][ILBASEv1Template]als u een ASEv1 wilt maken met een sjabloon Resourcemanager.


<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/documentation/templates/201-web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
