---
title: SMT-server instellen voor SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: SMT-server instellen voor SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 699a8a69621642d07d3547c07bb20c0d32ca7686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616997"
---
# <a name="set-up-smt-server-for-suse-linux"></a>SMT-server instellen voor SUSE Linux
Grote exemplaren van SAP HANA hebben geen directe verbinding met het internet. Het is geen eenvoudig proces om een dergelijke eenheid te registreren bij de besturingssysteemprovider en om updates te downloaden en toe te passen. Een oplossing voor SUSE Linux is het opzetten van een SMT-server in een Azure virtuele machine. Host de virtuele machine in een virtueel Azure-netwerk, dat is verbonden met de HANA Large Instance. Met zo'n SMT-server kan de HANA Large Instance-eenheid updates registreren en downloaden. 

Zie hun [Tool voor abonnementsbeheer voor SLES 12 SP2 voor](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf)meer documentatie over SUSE. 

Vereisten voor het installeren van een SMT-server die voldoet aan de taak voor HANA Large Instances zijn:

- Een Virtueel Azure-netwerk dat is verbonden met het HANA Large Instance ExpressRoute-circuit.
- Een SUSE-account dat is gekoppeld aan een organisatie. De organisatie moet een geldig SUSE-abonnement hebben.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>SMT-server installeren op een virtuele Azure-machine

Log dan eerst in bij het [SUSE Customer Center.](https://scc.suse.com/)

Ga naar **referenties van organisatieorganisatie** > **Organization Credentials**. In die sectie moet u de referenties vinden die nodig zijn om de SMT-server in te stellen.

Installeer vervolgens een SUSE Linux VM in het virtuele Azure-netwerk. Als u de virtuele machine wilt implementeren, neemt u een SLES 12 SP2-galerijafbeelding van Azure (selecteer BYOS SUSE-afbeelding). Definieer in het implementatieproces geen DNS-naam en gebruik geen statische IP-adressen.

![Schermafbeelding van de implementatie van virtuele machines voor SMT-server](./media/hana-installation/image3_vm_deployment.png)

De geïmplementeerde virtuele machine is kleiner en heeft het interne IP-adres in het virtuele Azure-netwerk van 10.34.1.4. De naam van de virtuele machine is *smtserver*. Na de installatie wordt de verbinding met de HANA Large Instance-eenheid of -eenheden gecontroleerd. Afhankelijk van hoe u naamomzetting hebt georganiseerd, moet u mogelijk de resolutie configureren van de HANA-eenheden voor grote instanties in etc/hosts van de virtuele Azure-machine. 

Voeg een schijf toe aan de virtuele machine. U gebruikt deze schijf om de updates vast te houden en de opstartschijf zelf kan te klein zijn. Hier, de schijf werd gemonteerd op / srv / www / htdocs, zoals weergegeven in de volgende screenshot. Een schijf van 100 GB moet volstaan.

![Schermafbeelding van de implementatie van virtuele machines voor SMT-server](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Meld u aan bij de EENHEID of eenheden van de HANA Large Instance, onderhoud /etc/hosts en controleer of u de virtuele Azure-machine bereiken die de SMT-server via het netwerk moet uitvoeren.

Meld u na deze controle aan bij de virtuele Azure-machine die de SMT-server moet uitvoeren. Als u stopverf gebruikt om u aan te melden bij de virtuele machine, voert u deze reeks opdrachten uit in uw bash-venster:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Start je bash opnieuw op om de instellingen te activeren. Start dan YAST.

Sluit uw VM (smtserver) aan op de SUSE-site.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Nadat de virtuele machine is aangesloten op de SUSE-site, installeert u de smt-pakketten. Gebruik de volgende stopverfopdracht om de smt-pakketten te installeren.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


U de YAST-tool ook gebruiken om de smt-pakketten te installeren. Ga in YAST naar **Software Maintenance**en zoek naar smt. Selecteer **smt**, die automatisch overschakelt naar yast2-smt.

![Schermafbeelding van SMT in YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Accepteer de selectie voor installatie op de smtserver. Nadat de installatie is voltooid, gaat u naar de configuratie van de SMT-server. Voer de organisatorische referenties in van het SUSE-klantencentrum dat u eerder hebt opgehaald. Voer ook de hostnaam van uw Azure-host voor virtuele machines in als de URL van de SMT-server. In deze demonstratie is het\/https: /smtserver.

![Schermafbeelding van smt-serverconfiguratie](./media/hana-installation/image6_configuration_of_smtserver1.png)

Test nu of de verbinding met het SUSE Customer Center werkt. Zoals u ziet in de volgende screenshot, in dit demonstratiegeval, werkte het wel.

![Schermafbeelding van het testen van de verbinding met het SUSE-klantencentrum](./media/hana-installation/image7_test_connect.png)

Nadat de SMT-installatie is gestart, geeft u een databasewachtwoord op. Omdat het een nieuwe installatie is, moet u dat wachtwoord definiëren zoals weergegeven in de volgende schermafbeelding.

![Schermafbeelding van het definiëren van wachtwoord voor database](./media/hana-installation/image8_define_db_passwd.PNG)

De volgende stap is het maken van een certificaat.

![Schermafbeelding van het maken van een certificaat voor SMT-server](./media/hana-installation/image9_certificate_creation.PNG)

Aan het einde van de configuratie kan het enkele minuten duren voordat de synchronisatiecontrole is uitgevoerd. Na de installatie en configuratie van de SMT-server, moet u de directory repo vinden onder het mount point /srv/www/htdocs/. Er zijn ook enkele subdirectories onder repo. 

Start de SMT-server en de bijbehorende services opnieuw met deze opdrachten.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Pakketten downloaden op SMT-server

Nadat alle services opnieuw zijn opgestart, selecteert u de juiste pakketten in SMT-beheer met behulp van YAST. De selectie van het pakket is afhankelijk van de afbeelding van het besturingssysteem van de HANA Large Instance-server. De selectie van het pakket is niet afhankelijk van de SLES-release of -versie van de virtuele machine waarop de SMT-server wordt uitgevoerd. De volgende schermafbeelding toont een voorbeeld van het selectiescherm.

![Schermafbeelding van het selecteren van pakketten](./media/hana-installation/image10_select_packages.PNG)

Start vervolgens de eerste kopie van de geselecteerde pakketten naar de SMT-server die u hebt ingesteld. Deze kopie wordt geactiveerd in de shell met behulp van de opdracht smt-mirror.

![Schermafbeelding van het downloaden van pakketten naar smt-server](./media/hana-installation/image11_download_packages.PNG)

De pakketten moeten worden gekopieerd naar de mappen gemaakt onder de mount point / srv / www / htdocs. Dit proces kan een uur of langer duren, afhankelijk van het aantal pakketten dat u selecteert. Als dit proces is voltooid, gaat u over op de smt-clientinstelling. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>De SMT-client instellen op HANA Large Instance-eenheden

De klant of klanten in dit geval zijn de HANA Large Instance units. De SMT-serversetup kopieerde het script clientSetup4SMT.sh naar de virtuele Azure-machine. Kopieer dat script naar de HANA Large Instance-eenheid die u wilt verbinden met uw SMT-server. Start het script met de optie -h en geef de naam van uw SMT-server als parameter. In dit voorbeeld is de naam *smtserver*.

![Schermafbeelding van het configureren van de SMT-client](./media/hana-installation/image12_configure_client.PNG)

Het is mogelijk dat de belasting van het certificaat van de server door de client slaagt, maar de registratie mislukt, zoals wordt weergegeven in de volgende screenshot.

![Schermafbeelding van clientregistratiefout](./media/hana-installation/image13_registration_failed.PNG)

Als de registratie mislukt, raadpleegt u [SUSE-ondersteuningsdocument](https://www.suse.com/de-de/support/kb/doc/?id=7006024)en voert u de daar beschreven stappen uit.

> [!IMPORTANT] 
> Geef voor de servernaam de naam van de virtuele machine op (in dit geval *smtserver),* zonder de volledig gekwalificeerde domeinnaam. 

Voer na het uitvoeren van deze stappen de volgende opdracht uit op de eenheid GROTE Instantie van DE HANA:

```
SUSEConnect –cleanup
```

> [!Note] 
> Wacht een paar minuten na die stap. Als u clientSetup4SMT.sh onmiddellijk uitvoert, u een foutmelding krijgen.

Als u een probleem ondervindt dat u moet oplossen op basis van de stappen van het SUSE-artikel, start u clientSetup4SMT.sh opnieuw op de eenheid GROTE Instantie van HANA. Nu moet het met succes eindigen.

![Schermafbeelding van het succes van de clientregistratie](./media/hana-installation/image14_finish_client_config.PNG)

U hebt de SMT-client van de HANA Large Instance-eenheid geconfigureerd om verbinding te maken met de SMT-server die u in de virtuele Azure-machine hebt geïnstalleerd. U nu 'zypper up' of 'zypper in' om het besturingssysteem updates te installeren aan HANA Large Instances, of installeer extra pakketten. U alleen updates ontvangen die u eerder hebt gedownload op de SMT-server.

## <a name="next-steps"></a>Volgende stappen
- [HANA-installatie op HLI](hana-example-installation.md).











