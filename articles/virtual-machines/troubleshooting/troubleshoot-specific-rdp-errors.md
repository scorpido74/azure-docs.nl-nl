---
title: Specifieke RDP-foutberichten voor Azure VM's | Microsoft Documenten
description: Specifieke foutberichten begrijpen die u mogelijk ontvangt wanneer u de verbinding met Extern bureaublad met een virtuele Windows-machine in Azure probeert te gebruiken
keywords: Extern bureaublad-fout, fout in externe bureaubladverbinding, kan geen verbinding maken met VM, probleemoplossing voor extern bureaublad
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 851c5eb4ebfee4e4a4836a07b51578dd2b0c68cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266869"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Specifieke RDP-foutberichten oplossen naar een virtuele Windows-machine in Azure
Mogelijk ontvangt u een specifiek foutbericht wanneer u verbinding met Extern bureaublad gebruikt met een virtuele Windows-machine (VM) in Azure. In dit artikel worden enkele van de meest voorkomende foutberichten beschreven, samen met stappen voor het oplossen van problemen om deze op te lossen. Zie de [handleiding voor probleemoplossing voor Extern bureaublad](troubleshoot-rdp-connection.md)als u problemen ondervindt bij het maken van verbinding met uw vm, maar er geen specifiek foutbericht tegenkomt.

Zie het volgende voor informatie over specifieke foutberichten:

* [De externe sessie is verbroken omdat er geen Extern bureaublad-licentieservers beschikbaar zijn om een licentie te verstrekken.](#rdplicense)
* [Extern bureaublad kan de naam van de computer niet vinden.](#rdpname)
* [Er is een verificatiefout opgetreden. Er kan geen contact worden opgenomen met de lokale veiligheidsautoriteit.](#rdpauth)
* [Windows-beveiligingsfout: uw referenties hebben niet gewerkt.](#wincred)
* [Deze computer kan geen verbinding maken met de externe computer.](#rdpconnect)

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>De externe sessie is verbroken omdat er geen Extern bureaublad-licentieservers beschikbaar zijn om een licentie te verstrekken.
Oorzaak: de 120-dagen licentierespijtperiode voor de functie Extern bureaublad-server is verlopen en u moet licenties installeren.

Sla als tijdelijke oplossing een lokale kopie van het RDP-bestand op van de portal en voer deze opdracht uit op een PowerShell-opdrachtprompt om verbinding te maken. Met deze stap schakelt u licenties voor alleen die verbinding uit:

        mstsc <File name>.RDP /admin

Als u niet echt meer dan twee gelijktijdige Extern bureaublad-verbindingen met de vm nodig hebt, u Serverbeheer gebruiken om de rol Extern bureaublad-server te verwijderen.

Zie voor meer informatie het blogbericht [dat Azure VM mislukt met 'Geen Extern bureaublad-licentieservers beschikbaar'.](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/)

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Extern bureaublad kan de naam van de computer niet vinden.
Oorzaak: de Extern bureaublad-client op uw computer kan de naam van de computer niet oplossen in de instellingen van het RDP-bestand.

Mogelijke oplossingen:

* Als u zich op het intranet van een organisatie bevindt, moet u ervoor zorgen dat uw computer toegang heeft tot de proxyserver en https-verkeer naar deze organisatie kan verzenden.
* Als u een lokaal opgeslagen RDP-bestand gebruikt, probeert u het bestand dat door de portal wordt gegenereerd. Deze stap zorgt ervoor dat u de juiste DNS-naam hebt voor de virtuele machine, of de cloudservice en de eindpuntpoort van de VM. Hier is een voorbeeld RDP-bestand gegenereerd door de portal:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Het adresgedeelte van dit RDP-bestand heeft:

* De volledig gekwalificeerde domeinnaam van de cloudservice die de VM bevat ('tailspin-azdatatier.cloudapp.net' in dit voorbeeld).
* De externe TCP-poort van het eindpunt voor Extern bureaublad-verkeer (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Er is een verificatiefout opgetreden. Er kan geen contact worden opgenomen met de lokale veiligheidsautoriteit.
Oorzaak: de doel-VM kan de beveiligingsinstantie niet vinden in het gedeelte van de gebruikersnaam van uw referenties.

Wanneer uw gebruikersnaam zich in het formulier *UserName*\\van de Beveiligingsautoriteit*bevindt* (bijvoorbeeld: CORP\User1), is het gedeelte *SecurityAuthority* de computernaam van de VM (voor de lokale beveiligingsinstantie) of een Active Directory-domeinnaam.

Mogelijke oplossingen:

* Als het account lokaal is voor de VM, controleert u of de VM-naam correct is gespeld.
* Als het account zich in een Active Directory-domein bevindt, controleert u de spelling van de domeinnaam.
* Als het een Active Directory-domeinaccount is en de domeinnaam correct is gespeld, controleert u of er een domeincontroller beschikbaar is in dat domein. Het is een veelvoorkomend probleem in virtuele Azure-netwerken die domeincontrollers bevatten dat een domeincontroller niet beschikbaar is omdat deze niet is gestart. Als tijdelijke oplossing u een lokaal beheerdersaccount gebruiken in plaats van een domeinaccount.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows-beveiligingsfout: uw referenties hebben niet gewerkt.
Oorzaak: de doel-vm kan uw accountnaam en wachtwoord niet valideren.

Een windows-computer kan de referenties van een lokaal account of een domeinaccount valideren.

* Voor lokale accounts gebruikt u de syntaxis *van de gebruikersnaam van computernaam*\\*UserName* (bijvoorbeeld SQL1\Admin4798).
* Voor domeinaccounts gebruikt u de syntaxis *van DomainName*\\*UserName* (voorbeeld: CONTOSO\peterodman).

Als u uw VM hebt gepromoot naar een domeincontroller in een nieuw Active Directory-forest, wordt het lokale beheerdersaccount waarmee u bent aangemeld geconverteerd naar een gelijkwaardig account met hetzelfde wachtwoord in het nieuwe forest en domein. Het lokale account wordt vervolgens verwijderd.

Als u zich bijvoorbeeld hebt aangemeld bij het lokale account DC1\DCAdmin en vervolgens de virtuele machine hebt gepromoot als domeincontroller in een nieuw forest voor het corp.contoso.com-domein, wordt het dc1\DCAdmin-lokale account verwijderd en wordt een nieuw domeinaccount (CORP\DCAdmin) gemaakt met hetzelfde wachtwoord.

Zorg ervoor dat de accountnaam een naam is die de virtuele machine kan verifiëren als een geldig account en dat het wachtwoord juist is.

Zie [Een wachtwoord of de extern bureaublad-service voor virtuele windows-apparaten opnieuw instellen](reset-rdp.md)als u het wachtwoord van het lokale beheerdersaccount opnieuw wilt instellen.

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Deze computer kan geen verbinding maken met de externe computer.
Oorzaak: het account dat wordt gebruikt om verbinding te maken, heeft geen aanmeldingsrechten voor extern bureaublad.

Elke Windows-computer heeft een lokale groep externe gebruikers van extern bureaublad-gebruikers, die de accounts en groepen bevat die zich op afstand kunnen aanmelden. Leden van de lokale beheerdersgroep hebben ook toegang, ook al worden deze accounts niet vermeld in de lokale groep extern bureaublad-gebruikers. Voor met domeinen verbonden machines bevat de groep lokale beheerders ook de domeinbeheerders voor het domein.

Zorg ervoor dat het account waarmee u verbinding maakt, aanmeldingsrechten voor Extern bureaublad heeft. Gebruik als tijdelijke oplossing een domein- of lokaal beheerdersaccount om verbinding te maken via Extern bureaublad. Als u het gewenste account wilt toevoegen aan de lokale groep Extern bureaublad-gebruikers, gebruikt u de module Microsoft Management Console **(Systeemhulpprogramma's > lokale gebruikers en groepen > groepen > Extern bureaublad-gebruikers).**

## <a name="next-steps"></a>Volgende stappen
Als er geen van deze fouten is opgetreden en u een onbekend probleem hebt met het verbinden via RDP, raadpleegt u de [handleiding voor probleemoplossing voor Extern bureaublad.](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* Zie [Toegang tot een toepassing die op](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)een Azure VM wordt uitgevoerd voor het oplossen van stappen voor het openen van toepassingen die op een vm worden uitgevoerd.
* Zie Problemen met [SSH-verbindingen met een Linux-vm in Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)oplossen als u problemen ondervindt met Secure Shell (SSH) om verbinding te maken met een Linux-vm in Azure.

