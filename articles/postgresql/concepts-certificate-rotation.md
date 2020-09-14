---
title: De draaiing van het certificaat voor Azure Database for PostgreSQL één server
description: Meer informatie over de aanstaande wijzigingen van basis certificaat wijzigingen die van invloed zijn op Azure Database for PostgreSQL één server
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: fcd27e84f479fa71d204e7d5a210ed16642d202b
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057208"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>Informatie over de wijzigingen in de basis-CA-wijziging voor Azure Database for PostgreSQL één server

Azure Database for PostgreSQL het basis certificaat voor de client toepassing/het stuur programma dat is ingeschakeld met SSL wijzigt, wordt gebruikt om [verbinding te maken met de database server](concepts-connectivity-architecture.md). Het basis certificaat dat momenteel beschikbaar is, is ingesteld op het verlopen van 26 oktober 2020 (10/26/2020) als onderdeel van de aanbevolen procedures voor standaard onderhoud en beveiliging. In dit artikel vindt u meer informatie over de aanstaande wijzigingen, de bronnen die worden beïnvloed en de stappen die nodig zijn om ervoor te zorgen dat uw toepassing verbinding met uw database server onderhoudt.

## <a name="what-update-is-going-to-happen"></a>Wat gebeurt er met de update?

In sommige gevallen gebruiken toepassingen een lokaal certificaat bestand dat is gegenereerd op basis van een certificaat bestand van een vertrouwde certificerings instantie (CA) om veilig verbinding te maken. Momenteel kunnen klanten alleen het vooraf gedefinieerde certificaat gebruiken om verbinding te maken met een Azure Database for PostgreSQL-server, die [hier](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)zich bevindt. Het [browser forum van Certificate Authority (CA)](https://cabforum.org/) heeft echter onlangs rapporten gepubliceerd van meerdere certificaten die zijn uitgegeven door leveranciers van de certificerings instantie om niet-compatibel te zijn.

Conform de nalevings vereisten van de branche begon de leveranciers van de certificerings instantie CA-certificaten voor niet-compatibele Ca's te intrekken, waardoor het voor servers vereist dat certificaten worden gebruikt die zijn uitgegeven door compatibele Ca's en ondertekend zijn door CA-certificaten van die compatibele certificerings instanties. Omdat Azure Database for PostgreSQL momenteel een van deze niet-compatibele certificaten gebruikt, die client toepassingen gebruiken om hun SSL-verbindingen te valideren, moeten we ervoor zorgen dat de juiste acties worden ondernomen (hieronder beschreven) om de potentiële impact op uw PostgreSQL-servers te minimaliseren.

Het nieuwe certificaat wordt vanaf 26 oktober 2020 (10/26/2020) gebruikt. Als u een CA-validatie of volledige validatie van het server certificaat gebruikt wanneer u verbinding maakt vanaf een PostgreSQL-client (sslmode = verifiëren-CA of sslmode = check-Full), moet u de configuratie van de toepassing bijwerken vóór 26 oktober 2020 (10/26/2020).

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Hoe kan ik weet of mijn Data Base wordt beïnvloed?

Alle toepassingen die gebruikmaken van SSL/TLS en controleren of het basis certificaat het basis certificaat moet bijwerken om verbinding te maken met Azure Database for PostgreSQL. Als u momenteel geen gebruikmaakt van SSL/TLS (sslmode = uitschakelen) of als u het server certificaat niet verifieert (sslmode = toestaan, sslmode = voor keur of sslmode = vereist), is er geen invloed op de beschik baarheid van uw toepassing. U kunt controleren of uw client toepassing de SSL-modus probeert te gebruiken met de vooraf gedefinieerde vertrouwde certificerings instantie ( [CA).](concepts-ssl-connection-security.md#enforcing-tls-connections)

Als u wilt voor komen dat de beschik baarheid van uw toepassing wordt onderbroken omdat de certificaten onverwacht zijn ingetrokken, of als u een certificaat wilt bijwerken dat is ingetrokken, raadpleegt u de sectie [**' wat heb ik nodig om de verbinding te behouden '**](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) .

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Wat heb ik nodig om de connectiviteit in stand te houden

Volg de onderstaande stappen om te voor komen dat de beschik baarheid van uw toepassing wordt onderbroken omdat de certificaten onverwacht zijn ingetrokken, of om een certificaat bij te werken dat is ingetrokken:

*   Down load BaltimoreCyberTrustRoot & DigiCertGlobalRootG2-basis certificerings instantie via onderstaande koppelingen:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Genereer een gecombineerd CA-certificaat archief met zowel **BaltimoreCyberTrustRoot** als **DigiCertGlobalRootG2** -certificaten.
    *   Voor Java-gebruikers (PostgreSQL JDBC) die gebruikmaken van DefaultJavaSSLFactory, voert u de volgende handelingen uit:

          ```azurecli-interactive
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```azurecli-interactive
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Vervang vervolgens het bestand van de oorspronkelijke opslag plaats door de nieuwe, gegenereerde versie:
        *   System. setProperty ("javax. net. SSL. trustStore", "path_to_truststore_file"); 
        *   System. setProperty ("javax. net. SSL. trustStorePassword", "wacht woord");
        
    *   Voor .NET (Npgsql)-gebruikers in Windows, moet u ervoor zorgen dat **Baltimore Cyber Trust Root** en **DigiCert Global root G2** beide aanwezig zijn in Windows-certificaat archief, vertrouwde basis certificerings instanties. Als er geen certificaten bestaan, importeert u het ontbrekende certificaat.

        ![Azure Database for PostgreSQL .net-certificaat](media/overview/netconnecter-cert.png)

    *   Voor .NET (Npgsql)-gebruikers op Linux met SSL_CERT_DIR, moet u ervoor zorgen dat **BaltimoreCyberTrustRoot** en **DigiCertGlobalRootG2** beide bestaan in de map die wordt aangegeven door SSL_CERT_DIR. Als er geen certificaten bestaan, maakt u het ontbrekende certificaat bestand.

    *   Voor andere PostgreSQL-client gebruikers kunt u twee CA-certificaat bestanden als onderstaande indeling samen voegen

        </br>-----BEGIN CERTIFICAAT-----
 </br>(Root CA1: BaltimoreCyberTrustRoot. CRT. pem)
 </br>-----EIND CERTIFICAAT-----
 </br>-----BEGIN CERTIFICAAT-----
 </br>(Root CA2: DigiCertGlobalRootG2. CRT. pem)
 </br>-----EIND CERTIFICAAT-----

*   Vervang het oorspronkelijke PEM-bestand van de basis-CA door het gecombineerde basis-CA-bestand en start de toepassing/client opnieuw.
*   Nadat het nieuwe certificaat op de server is geïmplementeerd, kunt u in de toekomst het PEM-bestand van de CA wijzigen in DigiCertGlobalRootG2. CRT. pem.

## <a name="what-can-be-the-impact"></a>Wat kan de impact hebben?
Als u het basis certificaat Baltimore Cyber Trust gebruikt om de SSL-verbinding met Azure Database for PostgreSQL te controleren, zoals hier wordt beschreven, kan de beschik baarheid van uw toepassing worden onderbroken omdat de data base niet bereikbaar is. Afhankelijk van uw toepassing kunnen er diverse fout berichten worden weer gegeven, inclusief, maar niet beperkt tot:
*   Ongeldig certificaat/ingetrokken certificaat
*   Time-out opgetreden voor verbinding
*   Fout, indien van toepassing

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. als ik SSL/TLS niet gebruik, moet ik dan nog steeds de basis-CA bijwerken?
Er zijn geen acties vereist als u SSL/TLS niet gebruikt. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. als ik SSL/TLS gebruik, moet ik mijn database server opnieuw starten om de basis-CA bij te werken?
Nee, u hoeft de database server niet opnieuw op te starten om het nieuwe certificaat te gebruiken. Dit is een wijziging aan de client zijde en de binnenkomende client verbindingen moeten het nieuwe certificaat gebruiken om ervoor te zorgen dat ze verbinding kunnen maken met de database server.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020-10262020"></a>3. Wat gebeurt er als ik het basis certificaat niet bijwerk vóór 26 oktober 2020 (10/26/2020)?
Als u het basis certificaat niet vóór 30 november 2020 bijwerkt, zullen uw toepassingen die verbinding maken via SSL/TLS en verificatie voor het basis certificaat niet kunnen communiceren met de PostgreSQL-database server en de toepassing verbindings problemen ondervindt met uw PostgreSQL-database server.

### <a name="4-do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>4. Ik moet een downtime voor onderhoud voor deze wijziging plannen?<BR>
Nee. Aangezien de wijziging hier alleen aan de client zijde wordt weer gegeven om verbinding te maken met de database server, is er hier geen onderbrekings tijd nodig voor deze wijziging.

### <a name="5--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020-10262020"></a>5. Wat moet ik doen als ik vóór 26 oktober 2020 (10/26/2020) geen geplande downtime voor deze wijziging krijg?
Omdat de clients die zijn gebruikt voor het maken van een verbinding met de server, de certificaat gegevens moeten bijwerken zoals beschreven in de sectie oplossing [hier](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity), hoeven we in dit geval geen downtime voor de server te gebruiken.

###  <a name="6-if-i-create-a-new-server-after-nov-30th-will-i-be-impacted"></a>6. als ik na 30 november een nieuwe server maak, geldt dit?
Voor servers die zijn gemaakt na 26 oktober 2020 (10/26/2020), kunt u het zojuist uitgegeven certificaat voor uw toepassingen gebruiken om verbinding te maken via SSL.

### <a name="7-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>7. hoe vaak werkt micro soft hun certificaten bij of wat is het verloop beleid?
Deze certificaten die worden gebruikt door Azure Database for PostgreSQL worden door vertrouwde certificerings instanties (CA) verschaft. Daarom is de ondersteuning van deze certificaten op Azure Database for PostgreSQL gekoppeld aan de ondersteuning van deze certificaten per CA. In dit geval kunnen er echter onvoorziene fouten voor komen in deze vooraf gedefinieerde certificaten die op het eerst moeten worden opgelost.

### <a name="8-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>8. als ik lees replica's gebruik, moet ik deze update alleen uitvoeren op de hoofd server of op alle Lees replica's?
Omdat deze update een wijziging aan de client zijde is, moeten we ook de wijzigingen voor deze clients Toep assen als de client gebruikt voor het lezen van gegevens van de replica server. 

### <a name="9-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>9. hebben we een query aan server zijde om te controleren of SSL wordt gebruikt?
Raadpleeg [SSL-verificatie](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity)om te controleren of u SSL-verbinding gebruikt om verbinding te maken met de server.

### <a name="10-what-if-i-have-further-questions"></a>10. Wat moet ik doen als ik meer vragen heb?
Als u vragen hebt, kunt u antwoorden krijgen van experts van community's in [micro soft Q&A](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com). Als u een ondersteunings abonnement hebt en technische hulp nodig hebt, kunt u [contact met ons](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com) opnemen