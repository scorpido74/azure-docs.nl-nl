---
title: Azure VMware-oplossing door CloudSimple - VCenter instellen op Private Cloud voor vRealize Automation
description: Beschrijft hoe u een VMware vCenter-server instelt op uw CloudSimple Private Cloud als eindpunt voor VMware vRealize Automation
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024837"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>VCenter instellen op uw Private Cloud voor VMware vRealize Automation

U een VMware vCenter-server instellen op uw CloudSimple Private Cloud als eindpunt voor VMware vRealize Automation.

## <a name="before-you-begin"></a>Voordat u begint

Voltooi deze taken voordat u de vCenter-server configureert:

* Configureer een [Site-to-Site VPN-verbinding](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tussen uw on-premises omgeving en uw Private Cloud.
* [Dns-forwarding van on-premises DNS-verzoeken configureren](on-premises-dns-setup.md) naar de DNS-servers voor uw Private Cloud.
* Dien een [ondersteuningsverzoek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) in om een vRealize Automation IaaS-beheergebruiker te maken met de set machtigingen die in de volgende tabel worden vermeld.

| Kenmerkwaarde | Machtiging |
------------ | ------------- |  
| Gegevensarchief |  Ruimte toewijzen <br> Bladeren door Datastore |
| Datastore-cluster | Een Gegevensarchiefcluster configureren |
| Map | Map maken <br>Map verwijderen |
| Wereldwijd |  Aangepaste kenmerken beheren<br>Aangepast kenmerk instellen |
| Netwerk | Netwerk toewijzen |
| Machtigingen | Machtigingen wijzigen |
| Resource | VM toewijzen aan resourcegroep<br>Uitgeschakelde virtuele machine migreren<br>Elektrisch migreren op virtuele machine |
| Inventaris virtuele machine |  Maken op uit bestaande<br>Nieuwe maken<br>Verplaatsen<br>Verwijderen | 
| Virtuele machineinteractie |  CD-media configureren<br>Consoleinteractie<br>Apparaatverbinding<br>Uitschakelen<br>Inschakelen<br>Opnieuw instellen<br>Onderbreken<br>Hulpprogramma's installeren | 
| Configuratie van virtuele machines |  Bestaande schijf toevoegen<br>Nieuwe schijf toevoegen<br>Toevoegen of verwijderen<br>Schijf verwijderen<br>Geavanceerd<br>CPU-aantal wijzigen<br>Bron wijzigen<br>Virtuele schijf uitbreiden<br>Schijfwijzigingstracking<br>Geheugen<br>Apparaatinstellingen wijzigen<br>Naam wijzigen<br>Annotatie instellen (versie 5.0 en hoger)<br>Instellingen<br>Plaatsing voor swapbestanden |
| Inrichten |  Aanpassen<br>Kloonsjabloon<br>Kloon virtuele machine<br>Sjabloon implementeren<br>Lees De specificaties van aanpassen |
| Virtuele machinestatus | Momentopname maken<br>Momentopname verwijderen<br>Terugkeren naar momentopname |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Installeer vRealize Automation in uw on-premises omgeving

1. Meld u aan bij het vRealize Automation IaaS-servertoestel als iaas-beheerder dat CloudSimple Support voor u heeft gemaakt.
2. Implementeer een vSphere-agent voor het eindpunt vRealize Automation.
    1. Ga naar https://*vra-url*:5480/installer, waarbij *vra-url* de URL is die je gebruikt om toegang te krijgen tot de vRealize Automation administration UI.
    2. Klik op de **IaaS Installer** om de installer te downloaden.<br>
    De naamgevingsconventie voor het installatiebestand is setup_*vra-url.*@5480.exe
    3. Voer het installatieprogramma uit. Klik in het scherm Welkom op **Volgende**.
    4. Accepteer de EULA en klik op **Volgende**.
    5. Geef de aanmeldingsgegevens op, klik op **Certificaat accepteren**en klik vervolgens op **Volgende**.
    ![vRA-referenties](media/configure-vra-endpoint-login.png)
    6. Selecteer **Aangepaste installatie-** en **proxyagents** en klik op **Volgende**.
    ![vRA-installatietype](media/configure-vra-endpoint-install-type.png)
    7. Voer de aanmeldingsgegevens van de IaaS-server in en klik op **Volgende**. Als u Active Directory gebruikt, voert u de gebruikersnaam in **domein\gebruikersindeling** in. Gebruik anders **user@domain** de indeling.
    ![vRA-inloggegevens](media/configure-vra-endpoint-account.png)
    8. Voer **vSphere** voor **agenttype**in voor de proxy-instellingen . Voer een naam in voor de agent.
    9. Voer de IaaS-server FQDN in de velden **Manager Service Host** en De **WebServicehost van Model Manager** in. Klik **op Testen** om de verbinding voor elk van de FQDN-waarden te testen. Als de test mislukt, wijzigt u uw DNS-instellingen zodat de hostnaam van de IaaS-server is opgelost.
    10. Voer een naam in voor vCenter-servereindpunt voor de Private Cloud. Neem de naam op voor gebruik later in het configuratieproces.

        ![vRA-installatieproxy](media/configure-vra-endpoint-proxy.png)

    11. Klik op **Volgende**.
    12. Klik **op Installeren**.

## <a name="configure-the-vsphere-agent"></a>De vSphere-agent configureren

1. Ga naar https://*vra-url*/vcac en meld je aan als **ConfigurationAdmin**.
2. Selecteer **Eindpunten** > **voor infrastructuur** > **.**
3. Selecteer **Nieuwe** > **virtuele** > **vSphere**.
4. Voer de vSphere-eindpuntnaam in die u in de vorige procedure hebt opgegeven.
5. Voer **voor Adres**de URL van de Private Cloud vCenter Server in de indeling https://*vcenter-fqdn*/sdk, waarbij *vcenter-fqdn* de naam van de vCenter-server is.
6. Voer de referenties in voor de vRealize Automation IaaS-beheergebruiker die CloudSimple Support voor u heeft gemaakt.
7. Klik **op Verbinding testen** om de gebruikersreferenties te valideren. Als de test mislukt, controleert u de URL, de accountgegevens en [de naam van het eindpunt](#verify-the-endpoint-name) en test u opnieuw.
8. Klik na een geslaagde test op **OK** om het vSphere-eindpunt te maken.
    ![vRA-eindpuntconfig-toegang](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>De naam van het eindpunt verifiëren

Ga als volgt te werk om de juiste vCenter-servereindpuntnaam te identificeren:

1. Open een opdrachtprompt op het IaaS-toestel.
2. Wijzig de map in C:\Program Files (x86)\VMware\vCAC\Agents\agent-name, waarbij *de naam agent-naam* de naam is die u aan het vCenter-servereindpunt hebt toegewezen.
3. Voer de volgende opdracht uit.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

De uitvoer is vergelijkbaar met de volgende. De waarde `managementEndpointName` van het veld is de naam van het eindpunt.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
