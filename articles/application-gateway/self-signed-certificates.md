---
title: Zelfondertekend certificaat genereren met een aangepaste basis-CA
titleSuffix: Azure Application Gateway
description: Meer informatie over het genereren van een zelfondertekend certificaat van Azure Application Gateway met een aangepaste basis-CA
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 0447e87fd8685188af8008995ba938092f2b87fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293600"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Een zelfondertekend certificaat van Azure Application Gateway genereren met een aangepaste basis-CA

De Application Gateway v2 SKU introduceert het gebruik van Trusted Root Certificates om backend servers toe te staan. Hiermee worden verificatiecertificaten verwijderd die vereist waren in de v1 SKU. Het *rootcertificaat* is een Basis-64 gecodeerde X.509(. CER) het rootcertificaat opmaken vanaf de backendcertificaatserver. Het identificeert de root certificaat autoriteit (CA) die het servercertificaat heeft uitgegeven en het servercertificaat wordt vervolgens gebruikt voor de SSL-communicatie.

Application Gateway vertrouwt standaard op het certificaat van uw website als deze is ondertekend door een bekende CA (bijvoorbeeld GoDaddy of DigiCert). U hoeft het basiscertificaat in dat geval niet expliciet te uploaden. Zie [Overzicht van SSL-beëindiging en end-to-end SSL met Application Gateway](ssl-overview.md)voor meer informatie. Als u echter een dev/testomgeving hebt en geen geverifieerd CA-ondertekend certificaat wilt kopen, u uw eigen aangepaste CA maken en er een zelfondertekend certificaat mee maken. 

> [!NOTE]
> Zelfondertekende certificaten worden standaard niet vertrouwd en kunnen moeilijk te onderhouden zijn. Ook kunnen ze gebruik maken van verouderde hash en cipher suites die misschien niet sterk zijn. Voor een betere beveiliging, de aankoop van een certificaat ondertekend door een bekende certificaat autoriteit.

In dit artikel leert u hoe u:

- Maak uw eigen aangepaste certificaatautoriteit
- Een zelfondertekend certificaat maken dat is ondertekend door uw aangepaste CA
- Een zelfondertekend rootcertificaat uploaden naar een toepassingsgateway om de backendserver te verifiëren

## <a name="prerequisites"></a>Vereisten

- **[OpenSSL](https://www.openssl.org/) op een computer met Windows of Linux** 

   Hoewel er andere hulpprogramma's beschikbaar kunnen zijn voor certificaatbeheer, maakt deze zelfstudie gebruik van OpenSSL. Je OpenSSL gebundeld vinden met veel Linux-distributies, zoals Ubuntu.
- **Een webserver**

   Bijvoorbeeld Apache, IIS of NGINX om de certificaten te testen.

- **Een Application Gateway v2 SKU**
   
  Zie [Quickstart: Direct webverkeer met Azure Application Gateway - Azure portal](quick-create-portal.md)als u geen bestaande toepassingsgateway hebt.

## <a name="create-a-root-ca-certificate"></a>Een basis-CA-certificaat maken

Maak uw basis-CA-certificaat met OpenSSL.

### <a name="create-the-root-key"></a>De hoofdtoets maken

1. Meld u aan bij uw computer waar OpenSSL is geïnstalleerd en voer de volgende opdracht uit. Hierdoor wordt een met een wachtwoord beveiligde sleutel aanmaakt.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. Typ bij de prompt een sterk wachtwoord. Bijvoorbeeld ten minste negen tekens, met hoofdletters, kleine letters, getallen en symbolen.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Een rootcertificaat maken en het zelf ondertekenen

1. Gebruik de volgende opdrachten om de csr en het certificaat te genereren.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Met de vorige opdrachten wordt het basiscertificaat gemaakt. U gebruikt dit om uw servercertificaat te ondertekenen.

1. Wanneer u daarom wordt gevraagd, typt u het wachtwoord voor de hoofdsleutel en de organisatiegegevens voor de aangepaste CA, zoals Land, Staat, Org, OU en de volledig gekwalificeerde domeinnaam (dit is het domein van de uitgever).

   ![rootcertificaat maken](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Een servercertificaat maken

Vervolgens maakt u een servercertificaat met OpenSSL.

### <a name="create-the-certificates-key"></a>De sleutel van het certificaat maken

Gebruik de volgende opdracht om de sleutel voor het servercertificaat te genereren.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>De CSR maken (certificaatondertekeningsaanvraag)

De CSR is een openbare sleutel die aan een CA wordt gegeven bij het aanvragen van een certificaat. De CA geeft het certificaat voor deze specifieke aanvraag uit.

> [!NOTE]
> De CN (Algemene naam) voor het servercertificaat moet afwijken van het domein van de uitgever. In dit geval is `www.contoso.com` de GN voor de uitgever bijvoorbeeld en is `www.fabrikam.com`het CN van het servercertificaat .


1. Gebruik de volgende opdracht om de CSR te genereren:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Wanneer u daarom wordt gevraagd, typt u het wachtwoord voor de hoofdsleutel en de organisatiegegevens voor de aangepaste CA: Land, Staat, Org, OU en de volledig gekwalificeerde domeinnaam. Dit is het domein van de website en het moet anders zijn dan de uitgever.

   ![Servercertificaat](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Het certificaat genereren met de CSR en de sleutel en ondertekenen met de hoofdsleutel van de CA

1. Gebruik de volgende opdracht om het certificaat te maken:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Het nieuw gemaakte certificaat verifiëren

1. Gebruik de volgende opdracht om de uitvoer van het CRT-bestand af te drukken en de inhoud ervan te verifiëren:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Certificaatverificatie](media/self-signed-certificates/verify-cert.png)

1. Controleer de bestanden in uw map en zorg ervoor dat u de volgende bestanden hebt:

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>Het certificaat configureren in de SSL-instellingen van uw webserver

Configureer SSL op uw webserver met de fabrikam.crt- en fabrikam.key-bestanden. Als uw webserver geen twee bestanden kan verwerken, u deze combineren tot één .pem- of .pfx-bestand met OpenSSL-opdrachten.

### <a name="iis"></a>IIS

Zie [Hoe u geïmporteerde certificaten op een webserver in Windows Server 2003 installeert](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)voor instructies over het importeren van certificaten en het uploaden als servercertificaat op IIS.

Zie SSL instellen [op IIS 7 voor](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)SSL-bindende instructies.

### <a name="apache"></a>Apache

De volgende configuratie is een voorbeeld [van virtuele host die is geconfigureerd voor SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) in Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

De volgende configuratie is een voorbeeld [NGINX-serverblok](https://nginx.org/docs/http/configuring_https_servers.html) met SSL-configuratie:

![NGINX met SSL](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Toegang tot de server om de configuratie te verifiëren

1. Voeg het basiscertificaat toe aan de vertrouwde hoofdmap van uw machine. Wanneer u de website bezoekt, moet u ervoor zorgen dat de volledige certificaatketen in de browser wordt weergegeven.

   ![Vertrouwde basiscertificaten](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Er wordt aangenomen dat DNS is geconfigureerd om de naam van de webserver (in dit voorbeeld www.fabrikam.com) naar het IP-adres van uw webserver te richten. Als dit niet het probleem is, u het [hosts-bestand](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) bewerken om de naam op te lossen.
1. Blader naar uw website en klik op het vergrendelingspictogram in het adresvak van uw browser om de site- en certificaatgegevens te verifiëren.

## <a name="verify-the-configuration-with-openssl"></a>De configuratie verifiëren met OpenSSL

U OpenSSL ook gebruiken om het certificaat te verifiëren.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Verificatie van OpenSSL-certificaat](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Het hoofdcertificaat uploaden naar de HTTP-instellingen van application gateway

Als u het certificaat wilt uploaden in Application Gateway, moet u het .crt-certificaat exporteren naar een .cer-indeling Base-64 gecodeerd. Aangezien .crt al de openbare sleutel bevat in de basis-64 gecodeerde indeling, wijzigt u de bestandsextensie van .crt naar .cer. 

### <a name="azure-portal"></a>Azure Portal

Als u het vertrouwde basiscertificaat vanuit de portal wilt uploaden, selecteert u de **HTTP-instellingen** en kiest u het **HTTPS-protocol.**

![Een certificaat toevoegen via de portal](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

U azure CLI of Azure PowerShell ook gebruiken om het hoofdcertificaat te uploaden. De volgende code is een Azure PowerShell-voorbeeld.

> [!NOTE]
> In het volgende voorbeeld wordt een vertrouwd rootcertificaat aan de toepassingsgateway toegevoegd, wordt een nieuwe HTTP-instelling gemaakt en wordt een nieuwe regel toegevoegd, ervan uitgaande dat de backendpool en de listener al bestaan.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that SSL handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```
### <a name="verify-the-application-gateway-backend-health"></a>De backendstatus van de toepassingsgateway verifiëren

1. Klik op de **backendstatusweergave** van de toepassingsgateway om te controleren of de sonde in orde is.
1.    U moet zien dat de status **in orde** is voor de HTTPS-sonde.

    ![HTTPS-sonde](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Overzicht van SSL-beëindiging en end-to-end SSL met Application Gateway](ssl-overview.md)voor meer informatie over SSL\TLS in Application Gateway.

