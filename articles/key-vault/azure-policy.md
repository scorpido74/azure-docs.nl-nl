---
title: Azure Key Vault integreren met Azure Policy
description: Meer informatie over het integreren van Azure Key Vault met Azure Policy
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/28/2020
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 9bf33aae5de600d6e5b0ab836c99da1d56935db3
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169383"
---
# <a name="integrate-azure-key-vault-with-azure-policy"></a>Azure Key Vault integreren met Azure Policy

[Azure Policy](../governance/policy/index.yml) is een beheer programma waarmee gebruikers hun Azure-omgeving op schaal kunnen controleren en beheren. Azure Policy biedt de mogelijkheid om Guardrails te plaatsen op Azure-resources om ervoor te zorgen dat ze compatibel zijn met de toegewezen beleids regels. Hiermee kunnen gebruikers controle, realtime-afdwinging en herstel van hun Azure-omgeving uitvoeren. De resultaten van controles die door het beleid worden uitgevoerd, zijn beschikbaar voor gebruikers in een compatibiliteits dashboard, waar ze een drilldownbewerking kunnen zien van de resources en onderdelen die compatibel zijn en die niet.  Zie het [overzicht van de Azure Policy-service](../governance/policy/overview.md)voor meer informatie.

Voor beelden van gebruiks Scenario's:

- U wilt de beveiligings postuur van uw bedrijf verbeteren door vereisten te implementeren rond de minimale sleutel grootte en de maximale geldigheids periode van certificaten in de sleutel kluizen van uw bedrijf, maar u weet niet welke teams compatibel zijn en wat niet. 
- U hebt momenteel geen oplossing voor het uitvoeren van een audit in uw organisatie, of u voert hand matige controles uit van uw omgeving door afzonderlijke teams binnen uw organisatie te vragen om hun naleving te rapporteren. U zoekt naar een manier om deze taak te automatiseren, controles in realtime uit te voeren en de nauw keurigheid van de audit te garanderen.
- U wilt het beveiligings beleid van uw bedrijf afdwingen en wilt voor komen dat individuen zelf-ondertekende certificaten maken, maar u hebt geen geautomatiseerde manier om het maken ervan te blok keren. 
- U wilt enkele vereisten voor uw test teams afwijzen, maar u wilt nauw keurige controle over uw productie omgeving behouden. U hebt een eenvoudige, geautomatiseerde manier nodig om de afdwinging van uw resources te scheiden. 
- U wilt er zeker van zijn dat u het afdwingen van nieuwe beleids regels in het geval van een probleem met een live-site kunt terugdraaien. U hebt een oplossing met één klik nodig om de handhaving van het beleid uit te scha kelen. 
- U bent afhankelijk van een oplossing van een derde partij voor het controleren van uw omgeving en u wilt een intern micro soft-aanbod gebruiken. 

## <a name="types-of-policy-effects-and-guidance"></a>Soorten beleids effecten en richt lijnen

**Controle**: wanneer het effect van een beleid is ingesteld op audit, heeft het beleid geen invloed op de wijzigingen in uw omgeving. Er wordt alleen een waarschuwing weer gegeven voor onderdelen zoals certificaten die niet voldoen aan de beleids definities binnen een opgegeven bereik, door deze onderdelen te markeren als niet-compatibel in het dash board voor beleids naleving. De controle is standaard als er geen beleids effect is geselecteerd. 

**Weigeren**: wanneer het effect van een beleid is ingesteld op weigeren, blokkeert het beleid het maken van nieuwe onderdelen, zoals certificaten, en worden nieuwe versies van bestaande onderdelen die niet voldoen aan de beleids definitie, geblokkeerd. Bestaande niet-compatibele resources in een sleutel kluis worden niet beïnvloed. De controle mogelijkheden blijven actief.

## <a name="available-built-in-policy-definitions"></a>Beschik bare ' ingebouwde ' beleids definities

Key Vault heeft een set beleids regels gemaakt die u kunt toewijzen voor algemene scenario's voor het beheren van certificaten. Deze beleids regels zijn ingebouwd, wat betekent dat u geen aangepaste JSON hoeft te schrijven om deze in te scha kelen en ze beschikbaar zijn in de Azure Portal die u wilt toewijzen. U kunt bepaalde para meters aanpassen aan de behoeften van uw organisatie. 

De acht preview-beleids regels zijn als volgt.

### <a name="manage-certificate-validity-period-preview"></a>Geldigheids duur van certificaat beheren (preview-versie)

Met dit beleid kunt u de maximale geldigheids duur van uw certificaten beheren die zijn opgeslagen in de sleutel kluis. Het is een goede beveiligings procedure om de maximale geldigheids duur van uw certificaten te beperken. Als een persoonlijke sleutel van uw certificaat zonder detectie zou worden aangetast, wordt het tijds bestek voor de doorlopende schade geminimaliseerd en wordt de waarde van het certificaat gereduceerd met een aanvaller. 

### <a name="manage-allowed-certificate-key-types-preview"></a>Toegestane certificaat sleutel typen beheren (preview-versie)
Met dit beleid kunt u het type certificaten beperken dat zich in de sleutel kluis kan bevindt. U kunt dit beleid gebruiken om ervoor te zorgen dat uw persoonlijke sleutels voor certificaten RSA, ECC of een HSM-back-up zijn. U kunt kiezen uit de volgende lijst welke certificaat typen zijn toegestaan.
- RSA
- RSA-HSM
- ECC 
- ECC-HSM 

### <a name="manage-certificate-lifetime-action-triggers-preview"></a>Actie triggers voor levens duur van certificaten beheren (preview-versie)

Met dit beleid kunt u de levensduur actie beheren die is opgegeven voor certificaten die binnen een bepaald aantal dagen na verloop van tijd vallen of een bepaald percentage van hun gebruiks duur hebben bereikt. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Certificaten beheren die zijn uitgegeven door een geïntegreerde certificerings instantie (preview-versie)

Als u gebruikmaakt van een Key Vault geïntegreerde certificerings instantie (Digicert of GlobalSign) en u wilt dat gebruikers een of een van deze providers gebruiken, kunt u dit beleid gebruiken om uw selectie te controleren of af te dwingen. Dit beleid kan ook worden gebruikt om het maken van zelfondertekende certificaten in de sleutel kluis te controleren of te weigeren. 

### <a name="manage-certificates-issued-by-an-integrated-ca-preview"></a>Certificaten beheren die zijn uitgegeven door een geïntegreerde certificerings instantie (preview-versie)

Als u een interne certificerings instantie of certificerings instantie gebruikt die niet is geïntegreerd met sleutel kluis en u wilt dat gebruikers een certificerings instantie gebruiken uit een lijst die u opgeeft, kunt u dit beleid gebruiken om een lijst met toegestane certificerings instanties te maken op basis van de naam van de uitgever. Dit beleid kan ook worden gebruikt om het maken van zelfondertekende certificaten in de sleutel kluis te controleren of te weigeren. 

### <a name="manage-allowed-curve-names-for-elliptic-curve-cryptography-certificates-preview"></a>Toegestane curve namen voor elliptische-curve certificaten beheren (preview-versie)
Als u elliptische curve-of ECC-certificaten gebruikt, kunt u een lijst met toegestane curve namen aanpassen in de onderstaande lijst. Met de standaard optie worden alle volgende curve namen toegestaan. 
- P-256
- P-256 KB
- P-384
- P-521

### <a name="manage-minimum-key-size-for-rsa-certificates-preview"></a>Minimale sleutel grootte voor RSA-certificaten beheren (preview-versie)
Als u RSA-certificaten gebruikt, kunt u een minimale sleutel grootte kiezen die uw certificaten moeten hebben. U kunt één optie selecteren in de onderstaande lijst. 
- 2048-bits
- 3072-bits
- 4096-bits

### <a name="manage-certificates-that-are-within-a-specified-number-of-days-of-expiration-preview"></a>Certificaten beheren die binnen een opgegeven aantal dagen verlopen (preview-versie)
Uw service kan een storing ondervinden als een certificaat dat niet op de juiste manier wordt bewaakt niet wordt gedraaid voordat het verloopt. Dit beleid is essentieel om ervoor te zorgen dat uw certificaten die zijn opgeslagen in de sleutel kluis, worden bewaakt. U wordt aangeraden dit beleid meerdere keren toe te passen met verschillende verval drempel waarden, bijvoorbeeld bij 180, 90, 60 en drempel waarden van 30 dagen. Dit beleid kan worden gebruikt om de verval datum van het certificaat in uw organisatie te controleren en te sorteren. 

## <a name="example-scenario"></a>Voorbeeldscenario

U beheert een sleutel kluis die wordt gebruikt door meerdere teams met 100-certificaten, en u wilt er zeker van zijn dat geen van de certificaten in de sleutel kluis langer dan twee jaar geldig is.

1. U wijst het beleid [geldigheids periode beheren](#manage-certificate-validity-period-preview) toe, geeft aan dat de maximale geldigheids periode van een certificaat 24 maanden is en stelt het effect van het beleid in op ' audit '. 
1. U bekijkt het [nalevings rapport op de Azure Portal](#view-compliance-results)en detecteert dat 20 certificaten niet-compatibel zijn en voor > 2 jaar geldig zijn, en de overige certificaten voldoen aan het beleid. 
1. U neemt contact op met de eigen aren van deze certificaten en communiceert de nieuwe beveiligings vereiste die certificaten langer dan 2 jaar niet geldig zijn. Sommige teams reageren en 15 van de certificaten zijn vernieuwd met een maximale geldigheids periode van 2 jaar of minder. Andere teams reageren niet en er zijn nog steeds 5 niet-compatibele certificaten in uw sleutel kluis.
1. U wijzigt het effect van het beleid dat u hebt toegewezen aan "weigeren". De 5 niet-compatibele certificaten worden niet ingetrokken en blijven functioneren. Ze kunnen echter niet worden verlengd met een geldigheids periode die langer is dan 2 jaar. 

## <a name="enabling-and-managing-a-key-vault-policy-through-the-azure-portal"></a>Het inschakelen en beheren van een Key Vault beleid via de Azure Portal

### <a name="select-a-policy-definition"></a>Een beleids definitie selecteren

1. Meld u aan bij Azure Portal. 
1. Zoek ' beleid ' in de zoek balk en selecteer **beleid**.

    ![Overzicht van de werking van Azure Key Vault](./media/policy-img1.png)

1. Selecteer in het venster beleid de optie **definities**.

    ![Overzicht van de werking van Azure Key Vault](./media/policy-img2.png)

1. Schakel in het categorie filter het selectie **vakje alles selecteren** uit en selecteer **Key Vault**. 

    ![Overzicht van de werking van Azure Key Vault](./media/policy-img3.png)

1. Nu moet u alle beleids regels kunnen zien die beschikbaar zijn voor open bare preview, voor Azure Key Vault. Zorg ervoor dat u de sectie beleids richtlijnen hebt gelezen en begrepen en selecteer een beleid dat u aan een bereik wilt toewijzen.  

    ![Overzicht van de werking van Azure Key Vault](./media/policy-img4.png)

### <a name="assign-a-policy-to-a-scope"></a>Een beleid toewijzen aan een bereik 

1. Selecteer een beleid dat u wilt Toep assen. in dit voor beeld wordt het beleid voor de **geldigheids periode van het certificaat beheren** weer gegeven. Klik op de knop toewijzen in de linkerbovenhoek.

    ![Overzicht van de werking van Azure Key Vault](./media/policy-img5.png)
  
1. Selecteer het abonnement waarvoor u het beleid wilt Toep assen. U kunt ervoor kiezen om het bereik te beperken tot één resource groep binnen een abonnement. Als u het beleid wilt Toep assen op het hele abonnement en bepaalde resource groepen wilt uitsluiten, kunt u ook een uitsluitings lijst configureren. Stel de selector voor beleids afdwinging in op **ingeschakeld** als u wilt dat het effect van het beleid (controleren of weigeren) plaatsvindt of wordt **uitgeschakeld** om het effect (controleren of weigeren) uit te scha kelen. 

    ![Overzicht van de werking van Azure Key Vault](./media/policy-img6.png)

1. Klik boven aan het scherm op het tabblad para meters om de maximale geldigheids periode op te geven die u wilt instellen. Selecteer **controleren** of **weigeren** voor het effect van het beleid volgens de richt lijnen in de bovenstaande secties. Selecteer vervolgens de knop beoordeling + maken. 

    ![Overzicht van de werking van Azure Key Vault](./media/policy-img7.png)

### <a name="view-compliance-results"></a>Nalevings resultaten weer geven

1. Ga terug naar de Blade beleid en selecteer het tabblad naleving. Klik op de beleids toewijzing waarvan u de nalevings resultaten wilt weer geven.

    ![Overzicht van de werking van Azure Key Vault](./media/policy-img8.png)

1. Op deze pagina kunt u de resultaten filteren op compatibele of niet-compatibele kluizen. Hier vindt u een lijst met niet-compatibele sleutel kluizen binnen het bereik van de beleids toewijzing. Een kluis wordt als niet-compatibel beschouwd als een van de onderdelen (certificaten) in de kluis niet compatibel is. U kunt een afzonderlijke kluis selecteren om de afzonderlijke niet-compatibele onderdelen (certificaten) weer te geven. 


    ![Overzicht van de werking van Azure Key Vault](./media/policy-img9.png)

1. De naam van de onderdelen in een kluis weer geven die niet-compatibel zijn


    ![Overzicht van de werking van Azure Key Vault](./media/policy-img10.png)

1. Als u wilt controleren of gebruikers de mogelijkheid hebben om resources te maken in de sleutel kluis, klikt u op het tabblad **gebeurtenissen (voor beeld)** om een samen vatting van geweigerde certificaat bewerkingen met de aanvrager en tijds tempels van aanvragen weer te geven. 


    ![Overzicht van de werking van Azure Key Vault](./media/policy-img11.png)

## <a name="feature-limitations"></a>Functie beperkingen

Het toewijzen van een beleid met een ' deny '-effect kan Maxi maal 30 minuten duren (gemiddeld hoofdletter gebruik) en 1 uur (slechtste) als u het maken van niet-compatibele resources wilt weigeren. De beleids evaluatie van bestaande onderdelen in een kluis kan Maxi maal 1 uur duren (gemiddeld aantal minuten) en 2 uur (slechtste) voordat de compatibiliteits resultaten zichtbaar zijn in de portal-gebruikers interface. Als de resultaten van de naleving als ' niet gestart ' worden weer gegeven, kan dit de volgende oorzaken hebben:
- De beleids waardering is nog niet voltooid. De eerste evaluatie latentie kan Maxi maal twee uur duren in het slechtste scenario. 
- Er zijn geen sleutel kluizen binnen het bereik van de beleids toewijzing.
- Er zijn geen sleutel kluizen met certificaten binnen het bereik van de beleids toewijzing. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Azure Policy-service](../governance/policy/overview.md)
- Zie Key Vault [-voor beelden: Key Vault ingebouwde beleids definities](../governance/policy/samples/built-in-policies.md#key-vault)