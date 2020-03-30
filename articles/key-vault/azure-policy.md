---
title: Azure Key Vault integreren met Azure-beleid
description: Meer informatie over het integreren van Azure Key Vault met Azure Policy
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 8c49b53cae08415633e1405317742a8a5d4e64b0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78196889"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Azure Key Vault integreren met Azure-beleid

[Azure Policy](../governance/policy/index.yml) is een governancetool waarmee gebruikers hun Azure-omgeving op schaal kunnen controleren en beheren. Azure Policy biedt de mogelijkheid om vangrails op Azure-resources te plaatsen om ervoor te zorgen dat ze voldoen aan toegewezen beleidsregels. Hiermee kunnen gebruikers audit, real-time handhaving en herstel van hun Azure-omgeving uitvoeren. De resultaten van audits die door het beleid worden uitgevoerd, zullen beschikbaar zijn voor gebruikers in een compliance-dashboard waar ze een drilldown kunnen zien van welke resources en componenten compatibel zijn en welke niet.  Zie het overzicht [van de Azure-beleidsservice](../governance/policy/overview.md)voor meer informatie .

Voorbeeldgebruiksscenario's:

- U wilt de beveiligingshouding van uw bedrijf verbeteren door vereisten te implementeren rond minimale sleutelgroottes en maximale geldigheidsperioden van certificaten in de belangrijkste kluizen van uw bedrijf, maar u weet niet welke teams compliant zijn en welke niet. 
- U hebt momenteel geen oplossing om een audit in uw organisatie uit te voeren, of u voert handmatige audits uit van uw omgeving door individuele teams binnen uw organisatie te vragen hun naleving te melden. U bent op zoek naar een manier om deze taak te automatiseren, audits in real time uit te voeren en de nauwkeurigheid van de audit te garanderen.
- U wilt uw bedrijfsbeveiligingsbeleid afdwingen en voorkomen dat personen zelfondertekende certificaten maken, maar u hebt geen geautomatiseerde manier om hun creatie te blokkeren. 
- U wilt een aantal vereisten voor uw testteams versoepelen, maar u wilt strenge controle houden over uw productieomgeving. U hebt een eenvoudige geautomatiseerde manier nodig om de handhaving van uw resources te scheiden. 
- U wilt er zeker van zijn dat u de handhaving van nieuw beleid terugdraaien in het geval van een probleem met een live-site. U hebt een oplossing met één klik nodig om de handhaving van het beleid uit te schakelen. 
- U vertrouwt op een oplossing van derden voor het controleren van uw omgeving en u wilt een intern Microsoft-aanbod gebruiken. 

## <a name="types-of-policy-effects-and-guidance"></a>Soorten beleidseffecten en richtsnoeren

**Controle:** Wanneer het effect van een beleid is ingesteld op controle, zal het beleid geen baanbrekende wijzigingen in uw omgeving veroorzaken. Het waarschuwt u alleen voor onderdelen zoals certificaten die niet voldoen aan de beleidsdefinities binnen een bepaald bereik, door deze componenten als niet-compatibel te markeren in het dashboard voor naleving van het beleid. Audit is standaard als er geen beleidseffect is geselecteerd. 

**Weigeren:** Wanneer het effect van een beleid is ingesteld om te weigeren, blokkeert het beleid het maken van nieuwe componenten zoals certificaten en blokkeert het nieuwe versies van bestaande componenten die niet voldoen aan de beleidsdefinitie. Bestaande niet-conforme resources in een sleutelkluis worden niet beïnvloed. De "audit"-mogelijkheden zullen blijven functioneren.

## <a name="available-built-in-policy-definitions"></a>Beschikbare beleidsdefinities voor "ingebouwde"

Key Vault heeft een set beleidsregels gemaakt die u toewijzen voor veelvoorkomende scenario's om certificaten te beheren. Dit beleid is 'Ingebouwde', wat betekent dat u geen aangepaste JSON hoeft te schrijven om ze in te schakelen en ze zijn beschikbaar in de Azure-portal die u toewijzen. U bepaalde parameters nog steeds aanpassen aan de behoeften van uw organisatie. 

De acht preview-beleidsregels zijn als volgt.

### <a name="manage-certificate-validity-period-preview"></a>Certificaatgeldigheidsperiode beheren (voorbeeld)

Met dit beleid u de maximale geldigheidsperiode beheren van uw certificaten die zijn opgeslagen in de sleutelkluis. Het is een goede beveiligingspraktijk om de maximale geldigheidsperiode van uw certificaten te beperken. Als een privésleutel van uw certificaat zonder detectie wordt gecompromitteerd, minimaliseert het gebruik van certificaten van korte duur het tijdsbestek voor voortdurende schade en vermindert de waarde van het certificaat voor een aanvaller. 

### <a name="manage-allowed-certificate-key-types-preview"></a>Toegestane certificaatsleuteltypen beheren (voorbeeld)
Met dit beleid u het type certificaten beperken dat zich in uw sleutelkluis kan begeven. U dit beleid gebruiken om ervoor te zorgen dat uw privésleutels van uw certificaat RSA, ECC of HSM-backs zijn. U kiezen uit de volgende lijst welke certificaattypen zijn toegestaan.
- RSA
- RSA - HSM
- Ecc 
- ECC - HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>Actietriggers voor de levensduur van certificaten beheren (voorbeeld)

Met dit beleid u de levenslange actie beheren die is opgegeven voor certificaten die zich binnen een bepaald aantal dagen na hun vervaldatum bevinden of een bepaald percentage van hun gebruiksduur hebben bereikt. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Certificaten beheren die zijn uitgegeven door een geïntegreerde CA (voorbeeld)

Als u een geïntegreerde certificaatinstantie van Key Vault (Digicert of GlobalSign) gebruikt en u wilt dat gebruikers een of een van deze providers gebruiken, u dit beleid gebruiken om uw selectie te controleren of af te dwingen. Dit beleid kan ook worden gebruikt om het maken van zelfondertekende certificaten in sleutelkluis te controleren of te weigeren. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Certificaten beheren die zijn uitgegeven door een geïntegreerde CA (voorbeeld)

Als u een interne certificaatinstantie of een certificaatinstantie gebruikt die niet is geïntegreerd met sleutelkluis en u wilt dat gebruikers een certificaatautoriteit gebruiken uit een lijst die u opgeeft, u dit beleid gebruiken om een toegestane lijst met certificaatautoriteiten te maken op naam van de uitgever. Dit beleid kan ook worden gebruikt om het maken van zelfondertekende certificaten in sleutelkluis te controleren of te weigeren. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Toegestane curvenamen voor certificaten voor elliptische curvecryptografie beheren (voorbeeld)
Als u elliptische curvecryptografie- of ECC-certificaten gebruikt, u een toegestane lijst met curvenamen in de onderstaande lijst aanpassen. Met de standaardoptie staan alle volgende curvenamen toe. 
- P-256
- P-256K
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>Minimale sleutelgrootte voor RSA-certificaten beheren (voorbeeld)
Als u RSA-certificaten gebruikt, u een minimale sleutelgrootte kiezen die uw certificaten moeten hebben. U een optie selecteren in de onderstaande lijst. 
- 2048-bits
- 3072 bits
- 4096 bits

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Certificaten beheren die zich binnen een bepaald aantal dagen na het verstrijken bevinden (voorbeeld)
Uw service kan een storing ondervinden als een certificaat dat niet voldoende wordt gecontroleerd, niet wordt gedraaid voordat het verloopt. Dit beleid is essentieel om ervoor te zorgen dat uw certificaten die zijn opgeslagen in de sleutelkluis worden gecontroleerd. Het wordt aanbevolen dit beleid meerdere keren toe te passen met verschillende vervaldatums, bijvoorbeeld bij drempelwaarden van 180, 90, 60 en 30 dagen. Dit beleid kan worden gebruikt om de vervaldatum van het certificaat in uw organisatie te controleren en te triage. 

## <a name="example-scenario"></a>Voorbeeldscenario

U beheert een sleutelkluis die wordt gebruikt door meerdere teams die 100 certificaten bevatten, en u wilt ervoor zorgen dat geen van de certificaten in de sleutelkluis langer dan 2 jaar geldig is.

1. U wijst het beleid voor de [geldigheidsperiode van het certificaat beheren](#manage-certificate-validity-period-preview) toe, geeft op dat de maximale geldigheidsperiode van een certificaat 24 maanden is en stelt het effect van het beleid in op 'audit'. 
1. U bekijkt het [nalevingsrapport op de Azure-portal](#view-compliance-results)en ontdekt dat 20 certificaten niet-compatibel zijn en > twee jaar geldig zijn en dat de overige certificaten compatibel zijn. 
1. U neemt contact op met de eigenaren van deze certificaten en deelt de nieuwe beveiligingsvereiste mee dat certificaten niet langer dan 2 jaar geldig mogen zijn. Sommige teams reageren en 15 van de certificaten werden verlengd met een maximale geldigheidsduur van 2 jaar of minder. Andere teams reageren niet en je hebt nog steeds 5 niet-conforme certificaten in je sleutelkluis.
1. U wijzigt het effect van het beleid dat u hebt toegewezen aan 'weigeren'. De 5 niet-conforme certificaten worden niet ingetrokken en blijven functioneren. Ze kunnen echter niet worden verlengd met een geldigheidsduur van meer dan 2 jaar. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Een Key Vault-beleid inschakelen en beheren via de Azure-portal

### <a name="select-a-policy-definition"></a>Een beleidsdefinitie selecteren

1. Meld u aan bij Azure Portal. 
1. Zoek op 'Beleid' in de zoekbalk en **Selecteer beleid**.

    ![Overzicht van hoe Azure Key Vault werkt](./media/policy-img1.png)

1. Selecteer **Definities**in het venster Beleid .

    ![Overzicht van hoe Azure Key Vault werkt](./media/policy-img2.png)

1. Schakel in het categoriefilter de optie **Alles selecteren** uit en selecteer **Toetskluis**. 

    ![Overzicht van hoe Azure Key Vault werkt](./media/policy-img3.png)

1. Nu moet u alle beleidsregels kunnen zien die beschikbaar zijn voor Openbare proefversie, voor Azure Key Vault. Zorg ervoor dat u de sectie beleidsrichtlijnen hierboven hebt gelezen en begrepen en selecteer een beleid dat u aan een bereik wilt toewijzen.  

    ![Overzicht van hoe Azure Key Vault werkt](./media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Een beleid toewijzen aan een bereik 

1. Selecteer een beleid dat u wilt toepassen, in dit voorbeeld wordt het beleid voor de **geldigheidsperiode beheren** weergegeven. Klik op de knop Toewijzen in de linkerbovenhoek.

    ![Overzicht van hoe Azure Key Vault werkt](./media/policy-img5.png)
  
1. Selecteer het abonnement op de plaats waar u het beleid wilt toepassen. U ervoor kiezen om het bereik te beperken tot slechts één resourcegroep binnen een abonnement. Als u het beleid wilt toepassen op het hele abonnement en bepaalde brongroepen wilt uitsluiten, u ook een uitsluitingslijst configureren. Stel de beleidshandhavingskiezer in op **Ingeschakeld** als u wilt dat het effect van het beleid (controleren of weigeren) optreedt of **Uitgeschakeld** wordt om het effect (controleren of weigeren) uit te schakelen. 

    ![Overzicht van hoe Azure Key Vault werkt](./media/policy-img6.png)

1. Klik op het tabblad Parameters boven aan het scherm om de maximale geldigheidsperiode op te geven in de gewenste maanden. Selecteer **audit** of **weigeren** voor het effect van het beleid volgens de richtlijnen in de bovenstaande secties. Selecteer vervolgens de knop Recensie + maken. 

    ![Overzicht van hoe Azure Key Vault werkt](./media/policy-img7.png)

### <a name="view-compliance-results"></a>Nalevingsresultaten weergeven

1. Ga terug naar het blad Beleid en selecteer het tabblad naleving. Klik op de beleidstoewijzing waarvoor u nalevingsresultaten wilt weergeven.

    ![Overzicht van hoe Azure Key Vault werkt](./media/policy-img8.png)

1. Vanaf deze pagina u resultaten filteren op compatibele of niet-conforme kluizen. Hier ziet u een lijst met niet-conforme sleutelkluizen binnen het bereik van de beleidstoewijzing. Een kluis wordt als niet-compatibel beschouwd als een van de onderdelen (certificaten) in de kluis niet-compatibel is. U een afzonderlijke kluis selecteren om de afzonderlijke niet-compatibele componenten (certificaten) weer te geven. 


    ![Overzicht van hoe Azure Key Vault werkt](./media/policy-img9.png)

1. De naam weergeven van de onderdelen in een kluis die niet voldoen


    ![Overzicht van hoe Azure Key Vault werkt](./media/policy-img10.png)

1. Als u wilt controleren of gebruikers de mogelijkheid worden ontzegd om resources te maken in de sleutelkluis, u op het tabblad **Componentgebeurtenissen (voorbeeld)** klikken om een overzicht van geweigerde certificaatbewerkingen te bekijken met de aanvrager en tijdstempels van aanvragen. 


    ![Overzicht van hoe Azure Key Vault werkt](./media/policy-img11.png)

## <a name="feature-limitations"></a>Functiebeperkingen

Het toewijzen van een beleid met een "ontkennen" effect kan tot 30 minuten (gemiddelde case) en 1 uur (worst case) duren om te beginnen met het ontkennen van de creatie van niet-conforme resources. De beleidsevaluatie van bestaande onderdelen in een kluis kan tot 1 uur (gemiddelde aanvraag) en 2 uur (in het ergste geval) duren voordat nalevingsresultaten zichtbaar zijn in de gebruikersinterface van de portal. Als de nalevingsresultaten worden weergegeven als 'Niet gestart' kan dit te wijten zijn aan de volgende redenen:
- De beleidswaardering is nog niet voltooid. De initiële evaluatielatentie kan in het ergste geval tot 2 uur duren. 
- Er zijn geen sleutelkluizen in het bereik van de beleidstoewijzing.
- Er zijn geen sleutelkluizen met certificaten binnen het bereik van de beleidstoewijzing. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Azure Policy-service](../governance/policy/overview.md)
- Zie Key Vault-voorbeelden: [ingebouwde beleidsdefinities van key vault](../governance/policy/samples/built-in-policies.md#key-vault)