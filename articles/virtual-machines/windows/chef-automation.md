---
title: Implementatie van virtuele Azure-machines met chef | Microsoft Docs
description: Meer informatie over hoe u chef kunt gebruiken voor automatische implementatie en configuratie van virtuele machines op Microsoft Azure
services: virtual-machines-windows
documentationcenter: ''
author: diegoviso
manager: gwallace
tags: azure-service-management,azure-resource-manager
editor: ''
ms.assetid: 0b82ca70-89ed-496d-bb49-c04ae59b4523
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 07/09/2019
ms.author: diviso
ms.openlocfilehash: 5cbf53da5a0af0a511350b9f30153e2fefe72dcf
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080100"
---
# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Implementatie van virtuele Azure-machine automatiseren met Chef

Chef is een uitstekend hulp programma voor het leveren van automatisering en desired state configurations.

Met de nieuwste Cloud API-release biedt chef naadloze integratie met Azure, waardoor u de mogelijkheid krijgt om configuratie statussen in te richten en te implementeren via één opdracht.

In dit artikel stelt u uw chef-omgeving in voor het inrichten van virtuele Azure-machines en het maken van een beleid of ' Cookbook ' en het implementeren van deze Cookbook naar een virtuele machine van Azure.

## <a name="chef-basics"></a>Basis beginselen van chef
Voordat u begint, [controleert u de basis concepten van chef](https://www.chef.io/chef).

In het volgende diagram ziet u de chef-architectuur op hoog niveau.

![][2]

Chef heeft drie belangrijkste architectuur onderdelen: Chef-server, chef-client (node) en chef-werk station.

De chef-server is het beheer punt en er zijn twee opties voor de chef-server: een gehoste oplossing of een on-premises oplossing.

De chef-client (knoop punt) is de agent die zich bevindt op de servers die u beheert.

Het chef-werk station, de naam van het beheer werkstation waar u beleids regels maakt en beheer opdrachten uitvoert en het software pakket van chef-hulpprogram ma's.

Over het algemeen ziet u _uw werk station_ als de locatie waar u acties en _chef werk station_ voor het software pakket uitvoert.
U kunt bijvoorbeeld de opdracht mes downloaden als onderdeel van het _chef-werk station_, maar u kunt ook mes-opdrachten uitvoeren vanaf _uw werk station om de_ infra structuur te beheren.

Chef maakt ook gebruik van de concepten van ' hand leidingen ' en ' recepten ', wat in feite het beleid is dat we definiëren en Toep assen op de-servers.

## <a name="preparing-your-workstation"></a>Uw werk station voorbereiden

Eerst moet u uw werk station voorbereiden door een map te maken voor het opslaan van chef-configuratie bestanden en hand leidingen.

Maak een map met de naam C:\Chef.

Down load en installeer de nieuwste versie van [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) op uw werk station.

## <a name="configure-azure-service-principal"></a>Azure Service Principal configureren

De eenvoudigste van de termen en de Azure-Service-Principal is een service account.   We gaan gebruikmaken van een Service-Principal om ons te helpen bij het maken van Azure-resources van ons chef-werk station.  Om de relevante service-principal te maken met de vereiste machtigingen, moeten we de volgende opdrachten uitvoeren in Power shell:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Noteer uw abonnements-, TenantID-, ClientID-en client geheim (het wacht woord dat u hierboven hebt ingesteld), u hebt dit later nodig. 

## <a name="setup-chef-server"></a>Chef-server instellen

In deze hand leiding wordt ervan uitgegaan dat u zich aanmeldt voor gehoste chef.

Als u nog geen chef-server gebruikt, kunt u het volgende doen:

* Meld u aan voor gehoste [chef](https://manage.chef.io/signup). Dit is de snelste manier om aan de slag te gaan met chef.
* Installeer een zelfstandige chef-server op een Linux-computer, gevolgd door de [installatie-instructies](https://docs.chef.io/install_server.html) van [chef docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Een gehoste chef-account maken

Meld u [hier](https://manage.chef.io/signup)aan voor een gehoste chef-account.

Tijdens het registratie proces wordt u gevraagd om een nieuwe organisatie te maken.

![][3]

Als uw organisatie eenmaal is gemaakt, downloadt u het Start pakket.

![][4]

> [!NOTE]
> Als er een waarschuwing wordt weer gegeven dat de sleutels opnieuw moeten worden ingesteld, is het raadzaam om door te gaan omdat er nog geen bestaande infra structuur is geconfigureerd.
>

Het zip-bestand van de start Kit bevat de configuratie bestanden en gebruikers sleutels `.chef` van uw organisatie in de Directory.

De `organization-validator.pem` moet afzonderlijk worden gedownload, omdat het een persoonlijke sleutel is en persoonlijke sleutels niet op de chef-server mogen worden opgeslagen. In [chef beheren](https://manage.chef.io/), gaat u naar de sectie beheer en selecteert u validatie sleutel opnieuw instellen, die een bestand bevat dat u afzonderlijk kunt downloaden. Sla het bestand op in c:\chef.

### <a name="configuring-your-chef-workstation"></a>Uw chef-werk station configureren

Pak de inhoud van de chef-starter. zip uit naar c:\chef.

Kopieer alle bestanden onder chef-starter\chef-repo\.chef naar de map c:\chef.

Kopieer het `organization-validator.pem` bestand naar c:\chef, als dit is opgeslagen in c:\Downloads

De map moet er nu ongeveer zo uitzien als in het volgende voor beeld.

```powershell
    Directory: C:\Users\username\chef

Mode           LastWriteTime    Length Name
----           -------------    ------ ----
d-----    12/6/2018   6:41 PM           .chef
d-----    12/6/2018   5:40 PM           chef-repo
d-----    12/6/2018   5:38 PM           cookbooks
d-----    12/6/2018   5:38 PM           roles
-a----    12/6/2018   5:38 PM       495 .gitignore
-a----    12/6/2018   5:37 PM      1678 azuredocs-validator.pem
-a----    12/6/2018   5:38 PM      1674 user.pem
-a----    12/6/2018   5:53 PM       414 knife.rb
-a----    12/6/2018   5:38 PM      2341 README.md
```

U hebt nu vijf bestanden en vier mappen (met inbegrip van de lege directory chef-opslag plaats) in de hoofdmap van c:\chef.

### <a name="edit-kniferb"></a>Mes bewerken. rb

De PEM-bestanden bevatten de persoonlijke sleutels van uw organisatie en de beheerders rechten voor communicatie en het bestand mes. rb bevat uw mes-configuratie. Het bestand mes. rb moet worden bewerkt.

Open het bestand mes. rb in de editor van uw keuze. Het ongewijzigde bestand moet er ongeveer als volgt uitzien:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Voeg de volgende gegevens toe aan uw mes. rb:

validation_client_name "myorg-validator"

validation_key "#{current_dir}/myorg.pem"

knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"

knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"

knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"

knife[:azure_client_secret] =     "#1234p$wdchef19"


Deze regels zorgen ervoor dat mes verwijst naar de hand leidingen-map onder c:\chef\cookbooks en maakt ook gebruik van de Azure-service-principal die u hebt gemaakt tijdens Azure-bewerkingen.

Het bestand mes. rb moet er nu uitzien als in het volgende voor beeld:

![][14]

<!--- Giant problem with this section: Chef 12 uses a config.rb instead of knife.rb
// However, the starter kit hasn't been updated
// So, I don't think this will even work on the modern Chef -->

<!--- update image [6] knife.rb -->

```rb
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                "myorg"
client_key               "#{current_dir}/myorg.pem"
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg-validator.pem"
chef_server_url          "https://api.chef.io/organizations/myorg"
cookbook_path            ["#{current_dir}/../cookbooks"]
knife[:azure_tenant_id] = "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] = "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] = "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] = "#1234p$wdchef19"
```

## <a name="install-chef-workstation"></a>Chef-werk station installeren

[Down load en installeer](https://downloads.chef.io/chef-workstation/) vervolgens chef-werk station.
Installeer chef workstation op de standaard locatie. Deze installatie kan enkele minuten duren.

Op het bureau blad ziet u een ' catch Weight Power shell ', een omgeving die is geladen met het hulp programma dat u nodig hebt voor interactie met de chef-producten. Met de Power shell catch Weight worden nieuwe ad hoc-opdrachten `chef-run` beschikbaar gemaakt, zoals traditionele chef cli-opdrachten, `chef`zoals. Bekijk de geïnstalleerde versie van chef Workstation en de chef-hulpprogram `chef -v`ma's met. U kunt ook de versie van uw werk station controleren door ' about chef Workstation ' te selecteren in de chef-werk station-app.

`chef --version`Er moet iets als resultaat worden geretourneerd:

```
Chef Workstation: 0.4.2
  chef-run: 0.3.0
  chef-client: 15.0.300
  delivery-cli: 0.0.52 (9d07501a3b347cc687c902319d23dc32dd5fa621)
  berks: 7.0.8
  test-kitchen: 2.2.5
  inspec: 4.3.2
```

> [!NOTE]
> De volg orde van het pad is belang rijk. Als uw opscode-paden zich niet in de juiste volg orde bevinden, hebt u problemen.
>

Start uw werk station opnieuw op voordat u doorgaat.

### <a name="install-knife-azure"></a>Mes Azure installeren

In deze zelf studie wordt ervan uitgegaan dat u de Azure Resource Manager gebruikt om te communiceren met uw virtuele machine.

Installeer de mes Azure-extensie. Dit biedt mes met de Azure-invoeg toepassing.

Voer de volgende opdracht uit.

    chef gem install knife-azure ––pre

> [!NOTE]
> Met het-pre-argument zorgt u ervoor dat u de nieuwste RC-versie van de mes Azure-invoeg toepassing ontvangt, waarmee u toegang hebt tot de nieuwste set Api's.
>
>

Waarschijnlijk worden er ook tegelijkertijd een aantal afhankelijkheden geïnstalleerd.

![][8]

Voer de volgende opdracht uit om ervoor te zorgen dat alles correct is geconfigureerd.

    knife azurerm server list

Als alles goed is geconfigureerd, ziet u een lijst met beschik bare Azure-afbeeldingen die worden doorzocht.

Gefeliciteerd! Uw werk station is ingesteld.

## <a name="creating-a-cookbook"></a>Een Cookbook maken

Een Cookbook wordt gebruikt door chef om een set opdrachten te definiëren die u wilt uitvoeren op uw beheerde client. Het maken van een Cookbook is eenvoudig. gebruik gewoon de **chef genereren Cookbook** opdracht voor het genereren van de Cookbook-sjabloon. Deze Cookbook is voor een webserver die automatisch IIS implementeert.

Voer de volgende opdracht uit in de directory C:\Chef.

    chef generate cookbook webserver

Met deze opdracht wordt een set bestanden gegenereerd in de map C:\Chef\cookbooks\webserver. Definieer vervolgens de set opdrachten die de chef-client moet uitvoeren op de beheerde virtuele machine.

De opdrachten worden opgeslagen in het bestand default. rb. In dit bestand definieert u een set opdrachten waarmee IIS wordt geïnstalleerd, wordt IIS gestart en wordt een sjabloon bestand naar de map wwwroot gekopieerd.

Wijzig het C:\chef\cookbooks\webserver\recipes\default.rb-bestand en voeg de volgende regels toe.

    powershell_script 'Install IIS' do
         action :run
         code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
         action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
         source 'Default.htm.erb'
         rights :read, 'Everyone'
    end

Sla het bestand op wanneer u klaar bent.

## <a name="creating-a-template"></a>Een sjabloon maken
In deze stap genereert u een sjabloon bestand dat wordt gebruikt als de standaard pagina. html.

Voer de volgende opdracht uit om de sjabloon te genereren:

    chef generate template webserver Default.htm

Navigeer naar het `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` bestand. Bewerk het bestand door een eenvoudige HTML-code voor ' Hallo wereld ' toe te voegen en sla het bestand op.

## <a name="upload-the-cookbook-to-the-chef-server"></a>De Cookbook naar de chef-server uploaden
In deze stap maakt u een kopie van de Cookbook die u hebt gemaakt op de lokale computer en uploadt u deze naar de door chef gehoste server. Nadat het Cookbook is geüpload, wordt het weer gegeven op het tabblad **beleid** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Een virtuele machine implementeren met mes Azure
Implementeer een virtuele Azure-machine en pas de Cookbook ' webserver ' toe waarmee de IIS-webservice en de standaard webpagina worden geïnstalleerd.

Als u dit wilt doen, gebruikt u de opdracht **mes azurerm server Create** .

Vervolgens wordt een voor beeld van de opdracht weer gegeven.

    knife azurerm server create `
    --azure-resource-group-name rg-chefdeployment `
    --azure-storage-account store `
    --azure-vm-name chefvm `
    --azure-vm-size 'Standard_DS2_v2' `
    --azure-service-location 'westus' `
    --azure-image-reference-offer 'WindowsServer' `
    --azure-image-reference-publisher 'MicrosoftWindowsServer' `
    --azure-image-reference-sku '2016-Datacenter' `
    --azure-image-reference-version 'latest' `
    -x myuser -P myPassword123 `
    --tcp-endpoints '80,3389' `
    --chef-daemon-interval 1 `
    -r "recipe[webserver]"


Met het bovenstaande voor beeld maakt u een virtuele machine met Windows Server 2016 die is geïnstalleerd in de regio vs-West. Vervang uw specifieke variabelen door en voer uit.

> [!NOTE]
> Via de opdracht regel Automatiseer ik ook mijn regels voor het eindpunt netwerk met behulp van de para meter – TCP-eind punten. Ik heb poorten 80 en 3389 geopend om toegang te geven tot de webpagina en de RDP-sessie.
>
>

Wanneer u de opdracht hebt uitgevoerd, gaat u naar de Azure Portal om te zien hoe uw machine begint met inrichten.

![][15]

De opdracht prompt wordt vervolgens weer gegeven.

![][16]

Zodra de implementatie is voltooid, wordt het open bare IP-adres van de nieuwe virtuele machine weer gegeven nadat de implementatie is voltooid, kunt u deze kopiëren en in een webbrowser plakken en de website bekijken die u hebt geïmplementeerd. Tijdens de implementatie van de virtuele machine is de poort 80 geopend, zodat deze extern beschikbaar moet zijn.   

![][11]

In dit voor beeld wordt gebruikgemaakt van creatieve HTML-code.

U kunt ook de status van het knoop punt weer geven [chef beheren](https://manage.chef.io/). 

![][17]

Vergeet niet dat u ook via een RDP-sessie verbinding kunt maken via de Azure Portal via poort 3389.

Bedankt. Ga vandaag nog aan de slag met uw infra structuur en start de code met Azure.

<!--Image references-->
[2]: media/chef-automation/2.png
[3]: media/chef-automation/3.png
[4]: media/chef-automation/4.png
[5]: media/chef-automation/5.png
[6]: media/chef-automation/6.png
[7]: media/chef-automation/7.png
[8]: media/chef-automation/8.png
[9]: media/chef-automation/9.png
[10]: media/chef-automation/10.png
[11]: media/chef-automation/11.png
[13]: media/chef-automation/13.png
[14]: media/chef-automation/14.png
[15]: media/chef-automation/15.png
[16]: media/chef-automation/16.png
[17]: media/chef-automation/17.png


<!--Link references-->
