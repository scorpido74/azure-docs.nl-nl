---
title: Probleem met het installeren van de application proxy agent connector | Microsoft Documenten
description: Problemen oplossen waarmee u te maken krijgen bij het installeren van de Application Proxy Agent Connector
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d773e6302edf0b799e6dfccc702750a9cc74f60
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406690"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Probleem bij het installeren van de connector voor de toepassingsproxyagent

Microsoft AAD Application Proxy Connector is een interne domeincomponent die uitgaande verbindingen gebruikt om de verbinding van het beschikbare eindpunt van de cloud tot het interne domein vast te stellen.

## <a name="general-problem-areas-with-connector-installation"></a>Algemene probleemgebieden met connectorinstallatie

Wanneer de installatie van een connector mislukt, is de hoofdoorzaak meestal een van de volgende gebieden:

1.  **Connectiviteit** – om een succesvolle installatie te voltooien, moet de nieuwe connector toekomstige vertrouwenseigenschappen registreren en vaststellen. Dit wordt gedaan door verbinding te maken met de AAD Application Proxy cloudservice.

2.  **Trust Establishment** – de nieuwe connector maakt een zelfondertekend cert en registreert zich bij de cloudservice.

3.  **Verificatie van de beheerder** : tijdens de installatie moet de gebruiker beheerdersreferenties opgeven om de connector-installatie te voltooien.

> [!NOTE]
> De connectorinstallatielogboeken zijn te vinden in de map %TEMP%en kunnen u helpen aanvullende informatie te verstrekken over wat een installatiefout veroorzaakt.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>De verbinding met de proxyservice voor cloudtoepassingen en de pagina Microsoft Login verifiëren

**Doelstelling:** Controleer of de connectormachine verbinding kan maken met het eindpunt voor de registratie van AAD Application Proxy en de aanmeldingspagina van Microsoft.

1.  Voer op de connectorserver een poorttest uit met [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) of een ander poorttestprogramma om te controleren of poorten 443 en 80 zijn geopend.

2.  Als een van deze poorten niet succesvol is, controleert u of de firewall of backendproxy toegang heeft tot de vereiste domeinen en poorten, [uw on-premises omgeving voorbereiden.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)

3.  Open een browser (apart tabblad) en ga `https://login.microsoftonline.com`naar de volgende webpagina:, zorg ervoor dat u inloggen op die pagina.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>Ondersteuning voor machine- en backendcomponenten controleren voor het vertrouwenscertificaat voor toepassingsproxy

**Doelstelling:** Controleer of de connectormachine, de backendproxy en de firewall het certificaat kunnen ondersteunen dat door de connector is gemaakt voor toekomstige vertrouwensrelatie en of het certificaat geldig is.

>[!NOTE]
>De connector probeert een SHA512-cert te maken dat wordt ondersteund door TLS1.2. Als de machine of de backend firewall en proxy TLS1.2 niet ondersteunen, mislukt de installatie.
>
>

**Bekijk de vereiste vereisten:**

1.  Controleer of de machine TLS1.2 ondersteunt – Alle Windows-versies na 2012 R2 moeten TLS 1.2 ondersteunen. Als uw connectormachine afkomstig is van een versie van 2012 R2 of eerder, controleert u of de volgende KB's op de machine zijn geïnstalleerd:<https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Neem contact op met uw netwerkbeheerder en vraag om te controleren of de backendproxy en firewall SHA512 niet blokkeren voor uitgaand verkeer.

**Ga als volgt te werk om het clientcertificaat te verifiëren:**

Controleer de duimafdruk van het huidige clientcertificaat. Het certificaatarchief is te vinden in %ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

Hier zijn de mogelijke **IsInUserStore** waarden en betekenissen:

- **false** - Het clientcertificaat is gemaakt tijdens de installatie of registratie die is gestart door de opdracht Register-AppProxyConnector. Het wordt opgeslagen in de persoonlijke container in de certificaatopslag van de lokale machine. 

Volg de stappen om het certificaat te verifiëren:

1. Run **certlm.msc**
2. Vouw in de beheerconsole de persoonlijke container uit en klik op Certificaten
3. Het certificaat zoeken dat is afgegeven door **connectorregistrationca.msappproxy.net**

- **true** - Het automatisch vernieuwde certificaat wordt opgeslagen in de persoonlijke container in het gebruikerscertificaatarchief van de netwerkservice. 

Volg de stappen om het certificaat te verifiëren:

1. Download [PsTools.zip](https://docs.microsoft.com/sysinternals/downloads/pstools)
2. Haal [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) uit het pakket en voer **psexec -i -u "nt authority\network service" cmd.exe** uit een verhoogde opdrachtprompt.
3. Run **certmgr.msc** in de nieuw verschenen opdrachtprompt
2. Vouw in de beheerconsole de persoonlijke container uit en klik op Certificaten
3. Zoek het certificaat afgegeven door **connectorregistrationca.msappproxy.ne

**Ga als gevolg van het vernieuwen van het clientcertificaat:**

Als een connector gedurende enkele maanden niet op de service is aangesloten, zijn de certificaten mogelijk verouderd. Het mislukken van de certificaatverlenging leidt tot een verlopen certificaat. Dit maakt de connectorservice om te stoppen met werken. De gebeurtenis 1000 wordt opgenomen in het beheerderslogboek van de connector:

"Opnieuw registreren van de connector is mislukt: het vertrouwenscertificaat connector is verlopen. Voer de PowerShell-cmdlet Register-AppProxyConnector uit op de computer waarop de connector wordt uitgevoerd om uw connector opnieuw te registreren."

In dit geval verwijdert en installeer t/m de connector om de registratie te activeren of u de volgende PowerShell-opdrachten uitvoeren:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Zie [Een onbeheerd installatiescript maken voor de Azure AD Application Proxy-connector](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-register-connector-powershell) voor meer informatie over de opdracht Register-AppProxyConnector.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Controleren of de beheerder wordt gebruikt om de connector te installeren

**Doelstelling:** Controleer of de gebruiker die de connector probeert te installeren een beheerder is met de juiste referenties. Momenteel moet de gebruiker ten minste een toepassingsbeheerder zijn om de installatie te laten slagen.

**Ga als volgt te werk om te controleren of de referenties correct zijn:**

Maak `https://login.microsoftonline.com` verbinding met en gebruik dezelfde referenties. Zorg ervoor dat de login succesvol is. U de gebruikersrol controleren door naar - &gt; Azure - &gt; Active **Directory-gebruikers en groepen** alle **gebruikers**te gaan. **Azure Active Directory** 

Selecteer uw gebruikersaccount en vervolgens 'Maprol' in het resulterende menu. Controleer of de geselecteerde rol 'Toepassingsbeheerder' is. Als u geen toegang hebt tot een van de pagina's langs deze stappen, hebt u niet de vereiste rol.

## <a name="next-steps"></a>Volgende stappen
[Informatie over Azure AD-toepassingsproxyconnectors](application-proxy-connectors.md)
