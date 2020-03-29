---
title: Bestaande hostgroep uitbreiden met nieuwe sessiehosts - Azure
description: Een bestaande hostgroep uitbreiden met nieuwe sessiehosts in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aee5195fe86fed3e631908a38d3bdb7d5e4883b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365216"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Een bestaande hostgroep uitbreiden met nieuwe sessiehosts

Als u het gebruik in uw hostpool verhoogt, moet u mogelijk uw bestaande hostpool uitbreiden met nieuwe sessiehosts om de nieuwe belasting te verwerken.

In dit artikel wordt uitgelegd hoe u een bestaande hostpool uitbreiden met nieuwe sessiehosts.

## <a name="what-you-need-to-expand-the-host-pool"></a>Wat u nodig hebt om het hostzwembad uit te breiden

Controleer voordat u begint of u een virtuele hostgroep en virtuele vm's (hostvan een host- en sessiehost) hebt gemaakt met een van de volgende methoden:

- [Azure Marketplace-aanbod](./create-host-pools-azure-marketplace.md)
- [GitHub Azure Resource Manager-sjabloon](./create-host-pools-arm-template.md)
- [Een hostpool maken met PowerShell](./create-host-pools-powershell.md)

Je hebt ook de volgende informatie nodig van toen je de vm's voor hostpool en sessiehost hebt gemaakt:

- VM-formaat, afbeeldings- en naamvoorvoegsel
- Domeinjoin- en Windows Virtual Desktop-tenantbeheerdersreferenties
- Naam van het virtuele netwerk en subnetnaam

De volgende drie secties zijn drie methoden die u gebruiken om de hostpool uit te breiden. U dit doen met welke implementatietool u zich ook voelt.

>[!NOTE]
>Tijdens de implementatiefase ziet u foutmeldingen voor de vm-bronnen van de vorige sessiehost als ze momenteel zijn afgesloten. Deze fouten worden uitgevoerd omdat Azure de PowerShell DSC-extensie niet kan uitvoeren om te valideren dat de VM's van de sessiehost correct zijn geregistreerd op uw bestaande hostgroep. U deze fouten veilig negeren of u de fouten voorkomen door alle vm's voor sessiehostin de bestaande hostgroep te starten voordat u met het implementatieproces begint.

## <a name="redeploy-from-azure"></a>Opnieuw implementeren vanuit Azure

Als u al een hostpool- en sessiehostVM's hebt gemaakt met behulp van de [Azure Marketplace-aanbieding](./create-host-pools-azure-marketplace.md) of [gitHub Azure Resource Manager-sjabloon,](./create-host-pools-arm-template.md)u dezelfde sjabloon opnieuw implementeren vanuit de Azure-portal. Als u de sjabloon opnieuw implementeert, worden alle gegevens die u in de oorspronkelijke sjabloon hebt ingevoerd, met uitzondering van wachtwoorden, automatisch opnieuw ingevoerd.

U de sjabloon Azure Resource Manager opnieuw implementeren om een hostgroep uit te vouwen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zoek in de zoekbalk boven aan de Azure-portal naar **Resourcegroepen** en selecteer het item onder **Services**.
3. Zoek en selecteer de resourcegroep die u hebt gemaakt toen u de hostgroep hebt gemaakt.
4. Selecteer **Implementaties**in het deelvenster aan de linkerkant van de browser .
5. Selecteer de juiste implementatie voor het maken van hostpool:
     - Als u de oorspronkelijke hostgroep hebt gemaakt met het Azure Marketplace-aanbod, selecteert u de implementatie die begint met **rds.wvd-provision-host-pool.**
     - Als u de oorspronkelijke hostgroep hebt gemaakt met de sjabloon GitHub Azure Resource Manager, selecteert u de implementatie met de naam **Microsoft.Template**.
6. Selecteer **Opnieuw implementeren**.
     
     >[!NOTE]
     >Als de sjabloon niet automatisch opnieuw wordt geïmplementeerd wanneer u **Opnieuw implementeren**selecteert, selecteert u **Sjabloon** in het deelvenster aan de linkerkant van uw browser en selecteert u **Vervolgens Implementeren**.

7. Selecteer de resourcegroep die de huidige VM's voor sessiehost bevat in de bestaande hostgroep.
     
     >[!NOTE]
     >Als u een fout ziet die u aangeeft een andere resourcegroep te selecteren, ook al is de groep die u hebt ingevoerd juist, selecteert u een andere resourcegroep en selecteert u vervolgens de oorspronkelijke resourcegroep.

8. Voer de volgende URL in voor de *_artifactsLocation:*`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Voer het nieuwe totale aantal sessiehosts in dat u wilt in *Rdsh Aantal exemplaren*. Als u bijvoorbeeld uw hostpool uitbreidt van vijf sessiehosts naar acht, voert u **8**in.
10. Voer hetzelfde bestaande domeinwachtwoord in dat u voor het bestaande domein UPN hebt gebruikt. Wijzig de gebruikersnaam niet, want dat zal een fout veroorzaken wanneer u de sjabloon uitvoert.
11. Voer hetzelfde tenantbeheerderswachtwoord in dat u hebt gebruikt voor de gebruiker of toepassings-id die u hebt ingevoerd voor *tenantbeheerder Upn of toepassingsid.* Nogmaals, wijzig de gebruikersnaam niet.
12. Voltooi de inzending om je hostpool uit te breiden.

## <a name="run-the-azure-marketplace-offering"></a>De Azure Marketplace-aanbieding uitvoeren

Volg de instructies in [Een hostgroep maken met behulp van de Azure Marketplace](./create-host-pools-azure-marketplace.md) totdat u De Azure Marketplace-aanbieding hebt uitgevoerd om een nieuwe [hostgroep in te richten.](./create-host-pools-azure-marketplace.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool) Wanneer u op dat punt bent, moet u voor elk tabblad de volgende gegevens invoeren:

### <a name="basics"></a>Basisbeginselen

Alle waarden in deze sectie moeten overeenkomen met wat u hebt opgegeven toen u de vm's voor hostpool en sessiehost voor het eerst maakte, met uitzondering *van standaarddesktopgebruikers:*

1.    Selecteer *bij Abonnement*het abonnement waarbij u de hostgroep voor het eerst hebt gemaakt.
2.    Selecteer *voor resourcegroep*dezelfde resourcegroep waarin de bestaande hostgroephostVM's zich bevinden.
3.    Selecteer *voor Regio*dezelfde regio waar de bestaande hostpoolsessiehost VM's zich bevinden.
4.    Voer *voor hostpoolnaam*de naam van de bestaande hostgroep in.
5.    Selecteer *voor bureaubladtype*het bureaubladtype dat overeenkomt met de bestaande hostgroep.
6.    Voer voor *standaarddesktopgebruikers*een door komma's gescheiden lijst in met eventuele extra gebruikers die u wilt aanmelden bij de Windows Virtual Desktop-clients en toegang te krijgen tot een bureaublad nadat het Azure Marketplace-aanbod is voltooid. Als u bijvoorbeeld wilt user3@contoso.com toewijzen user4@contoso.com en user3@contoso.comopenen, voert u ,user4@contoso.com.
7.    Selecteer **Volgende: Virtuele machine configureren**.

>[!NOTE]
>Met uitzondering *van standaardbureaubladgebruikers*moeten alle velden exact overeenkomen met wat is geconfigureerd in de bestaande hostgroep. Als er een mismatch is die zal resulteren in een nieuwe hostpool.

### <a name="configure-virtual-machines"></a>Virtuele machines configureren

Alle parameterwaarden in deze sectie moeten overeenkomen met wat u hebt opgegeven toen u de vm's voor hostpool en sessiehost voor het eerst maakte, met uitzondering van het totale aantal VM's. Het aantal VM's dat u invoert, is het aantal VM's in uw uitgebreide hostpool:

1. Selecteer de VM-grootte die overeenkomt met de bestaande VM's voor sessiehost.
    
    >[!NOTE]
    >Als de specifieke VM-grootte die u zoekt niet wordt weergegeven in de vm-groottekiezer, is dat omdat we deze nog niet hebben aangesloten bij de Azure Marketplace-tool. Als u een VM-grootte wilt aanvragen, maakt u een verzoek of stemt u een bestaand verzoek in het [Windows Virtual Desktop UserVoice-forum](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

2. Pas het *gebruiksprofiel*, *totaal aantal gebruikers*en het aantal parameters voor virtuele *machines* aan om het totale aantal sessiehosts te selecteren dat u in uw hostgroep wilt hebben. Als u bijvoorbeeld uw hostgroep uitbreidt van vijf sessiehosts naar acht, configureert u deze opties om naar 8 virtuele machines te gaan.
3. Voer een voorvoegsel in voor de namen van de virtuele machines. Als u bijvoorbeeld de naam 'voorvoegsel' invoert, worden de virtuele machines 'voorfix-0', 'voorvoegsel-1' genoemd, enzovoort.
4. Selecteer **Volgende: Instellingen voor virtuele machines**.

### <a name="virtual-machine-settings"></a>Instellingen voor virtuele machines

Alle parameterwaarden in deze sectie moeten overeenkomen met wat u hebt opgegeven toen u de vm's voor hostpool en sessiehost voor het eerst maakte:

1. Voer *voor de afbeeldingsbron* en *de image-besturingssysteemversie*dezelfde informatie in die u hebt opgegeven toen u de hostgroep voor het eerst maakte.
2. Voer voor *HET AD-domein lid worden van UPN* en de bijbehorende wachtwoorden dezelfde informatie in die u hebt verstrekt toen u de hostgroep voor het eerst maakte om lid te worden van de VM's in het Active Directory-domein. Deze referenties worden gebruikt om een lokaal account op uw virtuele machines te maken. U deze lokale accounts opnieuw instellen om hun referenties later te wijzigen.
3. Selecteer voor de virtuele netwerkinformatie hetzelfde virtuele netwerk en subnet voor de plaats waar uw bestaande hostpoolsessiehost VM's zich bevinden.
4. Selecteer **Volgende: Windows Virtual Desktop-gegevens configureren**.

### <a name="windows-virtual-desktop-information"></a>Windows Virtual Desktop-gegevens

Alle parameterwaarden in deze sectie moeten overeenkomen met wat u hebt opgegeven toen u de vm's voor hostpool en sessiehost voor het eerst maakte:

1. Voer voor de naam van de *tenantgroep*van Windows Virtual Desktop de naam in voor de tenantgroep die uw tenant bevat. Laat het als de standaard, tenzij u een specifieke tenantgroepnaam hebt opgegeven.
2. Voer voor *de tenantnaam van Windows Virtual Desktop*de naam in van de tenant waar u deze hostgroep maakt.
3. Geef dezelfde referenties op die u hebt gebruikt toen u de vm's voor hostpool en sessiehost voor het eerst maakte. Als u een serviceprincipal gebruikt, voert u de id in van het Azure Active Directory-exemplaar waarin de serviceprincipal zich bevindt.
4. Selecteer **Volgende : Controleren + maken**.

## <a name="run-the-github-azure-resource-manager-template"></a>De sjabloon GitHub Azure Resource Manager uitvoeren

Volg de instructies in [De sjabloon Azure Resource Manager uitvoeren voor het inrichten](./create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) van een nieuwe hostgroep en geef dezelfde parameterwaarden op, behalve het *Rdsh-aantal exemplaren*. Voer het gewenste aantal vm's voor sessiehost in de hostgroep in nadat u de sjabloon hebt uitgevoerd. Als u bijvoorbeeld uw hostpool uitbreidt van vijf sessiehosts naar acht, voert u **8**in.

## <a name="next-steps"></a>Volgende stappen

Nu u uw bestaande hostgroep hebt uitgebreid, u zich aanmelden bij een Windows Virtual Desktop-client om deze te testen als onderdeel van een gebruikerssessie. U verbinding maken met een sessie met een van de volgende clients:

- [Verbinding maken met de Windows Desktop-client](./connect-windows-7-and-10.md)
- [Verbinding maken met de webclient](./connect-web.md)
- [Verbinding maken met de Android-client](./connect-android.md)
- [Verbinding maken met de macOS-client](./connect-macos.md)
- [Verbinding maken met de iOS-client](./connect-ios.md)
