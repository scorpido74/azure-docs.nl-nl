---
title: Snelstart - Een virtuele Windows-machine configureren in Azure met Chef
description: In deze quickstart leert u hoe u Chef gebruiken om een virtuele Windows-machine in Azure te implementeren en configureren
keywords: chef-kok, azuurblauw, devops, virtuele machine
ms.topic: tutorial
ms.service: chef
author: tomarchermsft
ms.author: tarcher
ms.date: 02/22/2020
ms.openlocfilehash: 824e4df7662ee67c3f0786877053c39a8d952d49
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77590067"
---
# <a name="quickstart---configure-a-windows-virtual-machine-in-azure-using-chef"></a>Snelstart - Een virtuele Windows-machine configureren in Azure met Chef

Chef stelt u in staat om automatisering en gewenste staatconfiguraties te leveren.

Met de nieuwste cloud-API-release biedt Chef naadloze integratie met Azure, zodat u configuratiestatussen inrichten en implementeren via één opdracht.

In dit artikel stelt u uw Chef-omgeving in om virtuele Azure-machines in te richten en een beleids- of kookboek te maken en vervolgens dit kookboek te implementeren op een virtuele Azure-machine.

## <a name="chef-basics"></a>Chef-kok basics

Voordat u begint met dit artikel, [bekijk de basisconcepten van Chef](https://www.chef.io/chef).

Het volgende diagram toont de chef-architectuur op hoog niveau.

![Chef-architectuur](media/chef-automation/chef-architecure.png)

Chef heeft drie belangrijke architectonische componenten: 
- Chef Server - Het beheerpunt en er zijn twee opties voor de Chef Server: een gehoste oplossing of een on-premises oplossing.
- Chef-klant (knooppunt) - De agent die op de servers zit die u beheert.
- Chef Workstation - De naam voor zowel het beheerwerkstation (waar u beleid maakt en beheeropdrachten uitvoert) als het softwarepakket van Chef-tools.

Over het algemeen ziet u **uw werkstation** als de locatie waar u opdrachten en **Chef Workstation** voor het softwarepakket uitvoert.

U downloadt bijvoorbeeld de mesopdracht als onderdeel van het **Chef Workstation,** maar u voert mesopdrachten uit vanaf **uw werkstation** om de infrastructuur te beheren.

Chef-kok maakt ook gebruik van de concepten van *kookboeken* en *recepten.* Deze termen zijn respectievelijk het beleid dat wordt gedefinieerd en toegepast op de servers.

## <a name="preparing-your-workstation"></a>Uw werkstation voorbereiden

Bereid eerst uw werkstation voor door een map te maken om chef-configuratiebestanden en kookboeken op te slaan.

Maak een map met de naam `C:\Chef`.

Download en installeer de nieuwste [Azure CLI-versie](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) op uw werkstation.

## <a name="configure-azure-service-principal"></a>Azure Service Principal configureren

We gebruiken een Service Principal om ons te helpen Azure-bronnen te maken vanuit ons Chef Workstation.  Voer de volgende opdrachten uit in PowerShell als u de relevante serviceprincipal wilt maken met de vereiste machtigingen:
 
```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<yourSubscriptionName>"
$myApplication = New-AzureRmADApplication -DisplayName "automation-app" -HomePage "https://chef-automation-test.com" -IdentifierUris "https://chef-automation-test.com" -Password "#1234p$wdchef19"
New-AzureRmADServicePrincipal -ApplicationId $myApplication.ApplicationId
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $myApplication.ApplicationId
```

Let op uw SubscriptionID, TenantID, ClientID en Client Secret (het wachtwoord dat u eerder in deze zelfstudie hebt ingesteld) omdat u deze waarden nodig hebt. 

## <a name="setup-chef-server"></a>Setup Chef-server

Deze gids gaat ervan uit dat u zich aanmeldt voor Hosted Chef.

Als u nog geen Chef Server gebruikt, u het alsnog het afgelopen zijn:

* Meld je aan voor [Hosted Chef,](https://manage.chef.io/signup)dat is de snelste manier om aan de slag te gaan met Chef.
* Installeer een standalone Chef Server op linux-gebaseerde machine, volgens de [installatie-instructies](https://docs.chef.io/install_server.html) van [Chef Docs](https://docs.chef.io/).

### <a name="creating-a-hosted-chef-account"></a>Een Hosted Chef-account maken

Meld je [hier](https://manage.chef.io/signup)aan voor een Hosted Chef-account.

Tijdens het aanmeldingsproces wordt u gevraagd een nieuwe organisatie te maken.

![Organisatievenster maken](media/chef-automation/create-organization.png)

Zodra uw organisatie is gemaakt, downloadt u de starterkit.

![Chef configureren](media/chef-automation/configure-chef.png)

> [!NOTE]
> Als u een promptwaarschuwing ontvangt dat uw sleutels worden gereset, is het goed om door te gaan omdat er nog geen bestaande infrastructuur is geconfigureerd.
>

Dit zip-bestand van de starterkit bevat `.chef` uw organisatieconfiguratiebestanden en gebruikerssleutel in de map.

Het `organization-validator.pem` moet apart worden gedownload, omdat het een privésleutel is en privésleutels niet op de Chef Server mogen worden opgeslagen. Ga vanuit [Chef Manage](https://manage.chef.io/)naar de sectie Beheer en selecteer 'Validatiesleutel opnieuw instellen', waarmee u een bestand afzonderlijk downloaden. Sla het bestand op c:\chef.Nl.

### <a name="configuring-your-chef-workstation"></a>Uw Chef-workstation configureren

Haal de inhoud `chef-starter.zip` `c:\chef`van de naar .

Kopieer alle `chef-starter\chef-repo\.chef` bestanden `c:\chef` onder naar uw directory.

Kopieer `organization-validator.pem` het `c:\chef`bestand naar , als `c:\Downloads`het is opgeslagen in .

Uw directory moet er nu ongeveer zo uitzien als het volgende voorbeeld.

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

Je moet nu vijf bestanden en vier mappen (met inbegrip van de lege chef-repo directory) in de wortel van c:\chef-kok.

### <a name="edit-kniferb"></a>Edit knife.rb

De PEM-bestanden bevatten uw organisatie en administratieve privésleutels voor communicatie en het knife.rb-bestand bevat uw mesconfiguratie. We moeten het mes.rb dossier bewerken.

Open het knife.rb bestand in de editor van uw keuze. Het ongewijzigde bestand moet er ongeveer uitzien als:

```rb
current_dir = File.dirname(__FILE__)
log_level           :info
log_location        STDOUT
node_name           "mynode"
client_key          "#{current_dir}/user.pem"
chef_server_url     "https://api.chef.io/organizations/myorg"
cookbook_path       ["#{current_dir}/cookbooks"]
```

Voeg de volgende informatie toe aan uw knife.rb en vervang de tijdelijke aanduidingen door uw gegevens:

```rb
validation_client_name   "myorg-validator"
validation_key           "#{current_dir}/myorg.pem"
knife[:azure_tenant_id] =         "0000000-1111-aaaa-bbbb-222222222222"
knife[:azure_subscription_id] =   "11111111-bbbbb-cccc-1111-222222222222"
knife[:azure_client_id] =         "11111111-bbbbb-cccc-1111-2222222222222"
knife[:azure_client_secret] =     "#1234p$wdchef19"
```

Deze regels zullen ervoor zorgen dat Mes verwijzingen naar de kookboeken directory onder `c:\chef\cookbooks`.

Uw `knife.rb` bestand moet er nu hetzelfde uitzien als het volgende voorbeeld:

![Voorbeeld van mesbestand](./media/chef-automation/knife-file-example.png)

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

## <a name="install-chef-workstation"></a>Chef-workstation installeren

Download vervolgens [het Chef Workstation en installeer deze.](https://downloads.chef.io/chef-workstation/)

Installeer Chef Workstation op de standaardlocatie.

Op het bureaublad zie je een CW PowerShell. Deze tool wordt gebruikt om te communiceren met Chef-producten. De CW PowerShell stelt nieuwe commando's beschikbaar, zoals `chef-run` `chef`en Chef CLI commando's (zoals). Bekijk uw geïnstalleerde versie van Chef `chef -v`Workstation en de Chef-tools met . U uw Workstation-versie ook controleren door **About Chef Workstation** te selecteren in de Chef Workstation-app.

`chef --version`moet terugkeren iets als:

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
> De volgorde van het pad is belangrijk! Als uw opscode-paden niet in de juiste volgorde staan, ontstaan er problemen.
>

Start uw werkstation opnieuw op voordat u verdergaat.

### <a name="install-knife-azure"></a>Mes Azure installeren

In deze zelfstudie wordt ervan uitgegaan dat u Azure Resource Manager gebruikt om te communiceren met uw virtuele machine.

Installeer de Azure-extensie Knife, inclusief de Azure-plug-in.

Voer de volgende opdracht uit.

    chef gem install knife-azure ––pre

> [!NOTE]
> Het `–-pre` argument zorgt ervoor dat u de nieuwste RC-versie van de Knife Azure-plug-in ontvangt die toegang biedt tot de nieuwste set API's.
>
>

Het is waarschijnlijk dat een aantal afhankelijkheden ook tegelijkertijd worden geïnstalleerd.

![Uitvoer van het installeren van mes-azure](./media/chef-automation/install-knife-azure.png)

Voer de volgende opdracht uit om ervoor te zorgen dat alles correct is geconfigureerd.

    knife azurerm server list

Als alles correct is geconfigureerd, ziet u een lijst met beschikbare Azure-afbeeldingen doorbladeren.

Gefeliciteerd! Uw werkstation is ingesteld!

## <a name="creating-a-cookbook"></a>Een kookboek maken

Een kookboek wordt gebruikt door Chef om een set van commando's die u wilt draaien op uw beheerde klant te definiëren. Het maken van een kookboek `chef generate cookbook` is eenvoudig, gebruik gewoon de opdracht om de kookboek sjabloon te genereren. Dit kookboek is voor een webserver die Automatisch IIS implementeert.

Voer `C:\Chef directory`onder uw , voer de volgende opdracht uit.

    chef generate cookbook webserver

Met deze opdracht genereert u een set bestanden onder de map C:\Chef\kookboeken\webserver. Definieer vervolgens de set opdrachten voor de Chef-client om op de beheerde virtuele machine uit te voeren.

De opdrachten worden opgeslagen in het bestand default.rb. Definieer in dit bestand een set opdrachten waarmee IIS wordt geïnstalleerd, IIS `wwwroot` wordt gestart en een sjabloonbestand naar de map wordt kopieert.

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

Sla het bestand op zodra u klaar bent.

## <a name="creating-a-template"></a>Een sjabloon maken

In deze stap genereert u een sjabloonbestand `default.html` dat u als pagina wilt gebruiken.

Voer de volgende opdracht uit om de sjabloon te genereren:

    chef generate template webserver Default.htm

Navigeer naar `C:\chef\cookbooks\webserver\templates\default\Default.htm.erb` het bestand. Bewerk het bestand door een eenvoudige *Hello World* HTML-code toe te voegen en sla het bestand op.

## <a name="upload-the-cookbook-to-the-chef-server"></a>Upload het kookboek naar de Chef Server

In deze stap maakt u een kopie van het kookboek dat u op de lokale machine hebt gemaakt en uploadt u deze naar de chef-server. Eenmaal geüpload wordt het kookboek weergegeven onder het tabblad **Beleid.**

    knife cookbook upload webserver

![Resultaten van het installeren van kookboek aan de Chef Server](./media/chef-automation/cookbook-installation-under-policy-tab.png)

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Een virtuele machine implementeren met Knife Azure

Implementeer een virtuele Azure-machine en pas het `Webserver` kookboek toe met de `knife` opdracht.

De `knife` opdracht installeert ook de IIS-webservice en de standaardwebpagina.

```bash
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
```

In `knife` het opdrachtvoorbeeld wordt een *Standard_DS2_v2* virtuele machine gemaakt waarin Windows Server 2016 is geïnstalleerd in de regio West-VS. Wijzig deze waarden volgens uw app-behoeften.

Nadat u de opdracht hebt uitgevoerd, bladert u naar de Azure-portal om te zien hoe uw machine begint te worden ingericht.

![Virtuele machine wordt ingericht](./media/chef-automation/virtual-machine-being-provisioned.png)

De opdrachtprompt wordt vervolgens weergegeven.

![Mes uitvoer bij het maken van virtuele machine](./media/chef-automation/knife-output-when-creating-vm.png)

Zodra de implementatie is voltooid, wordt het openbare IP-adres van de nieuwe virtuele machine weergegeven. Plak deze waarde in een webbrowser om de nieuwe website te bekijken. Toen we de virtuele machine hebben geïmplementeerd, openden we poort 80, zodat deze extern beschikbaar moet zijn.   

![Het testen van de virtuele machine](./media/chef-automation/testing-the-virtual-machine.png)

In dit voorbeeld wordt gebruik gemaakt van creatieve HTML-code.

U ook de status [chef-kok beheren](https://manage.chef.io/)van het knooppunt bekijken. 

![De status van het knooppunt weergeven](./media/chef-automation/viewing-node-status.png)

Vergeet niet dat u ook verbinding maken via een RDP-sessie vanuit de Azure-portal via poort 3389.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Chef op Azure](/azure/chef/)