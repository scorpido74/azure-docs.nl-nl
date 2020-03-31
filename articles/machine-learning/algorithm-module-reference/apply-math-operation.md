---
title: Wiskundige bewerking toepassen
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module Wiskundige bewerking toepassen in Azure Machine Learning om een wiskundige bewerking toe te passen op kolomwaarden in een pijplijn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 2b4d6939aa1db4b8321c792898ed421c0d16cc99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456740"
---
# <a name="apply-math-operation"></a>Wiskundige bewerking toepassen

In dit artikel wordt een module van Azure Machine Learning designer (preview) beschreven.

Gebruik de bewerking Wiskundige toepassen om berekeningen te maken die worden toegepast op numerieke kolommen in de invoergegevensset. 

Ondersteunde wiskundige bewerkingen omvatten algemene rekenkundige functies zoals vermenigvuldiging en verdeling, trigonometrische functies, een verscheidenheid aan afrondingsfuncties en speciale functies die worden gebruikt in gegevenswetenschap, zoals gamma- en foutfuncties.  

Nadat u een bewerking hebt gedefinieerd en de pijplijn hebt uitgevoerd, worden de waarden aan uw gegevensset toegevoegd. Afhankelijk van hoe u de module configureert, u:

+ De resultaten toevoegen aan uw gegevensset. Dit is vooral handig wanneer u het resultaat van de bewerking controleert.
+ Vervang kolommenwaarden door de nieuwe, berekende waarden.
+ Genereer een nieuwe kolom voor resultaten en toon de oorspronkelijke gegevens niet weer. 

Zoek naar de bewerking die u nodig hebt in deze categorieën:  

- [Basic](#basic-math-operations)  
  
     De functies in de categorie **Basis** kunnen worden gebruikt om één waarde of kolom met waarden te manipuleren. U bijvoorbeeld de absolute waarde van alle getallen in een kolom krijgen of de vierkantswortel van elke waarde in een kolom berekenen.  
  
-   [Vergelijken](#comparison-operations)  
  
      De functies in de categorie **Vergelijken** worden allemaal gebruikt voor vergelijking: u een paarsvergelijking maken van de waarden in twee kolommen of u elke waarde in een kolom vergelijken met een opgegeven constante. U bijvoorbeeld kolommen vergelijken om te bepalen of waarden in twee gegevenssets hetzelfde waren. U ook een constante, zoals een maximaal toegestane waarde, gebruiken om uitschieters in een numerieke kolom te vinden.  
  
-   [Bewerkingen](#arithmetic-operations)  
  
     De categorie **Bewerkingen** omvat de basiswiskundige functies: optellen, aftrekken, vermenigvuldigen en deling. U werken met kolommen of constanten. U bijvoorbeeld de waarde in kolom A toevoegen aan de waarde in kolom B. Of u een constante, zoals een eerder berekend gemiddelde, aftrekken van elke waarde in kolom A.  
  
-   [Afronden](#rounding-operations)  
  
     De categorie **Afronding** omvat een verscheidenheid aan functies voor het uitvoeren van bewerkingen, zoals afronding, plafond, vloer en truncation tot verschillende niveaus van precisie. U het precisieniveau opgeven voor zowel decimale als hele getallen.  
  
-   [Speciale](#special-math-functions)  
  
     De **speciale** categorie omvat wiskundige functies die vooral worden gebruikt in de datascience, zoals elliptische integralen en de Gaussische foutfunctie.  
  
-   [Trigonometrische](#trigonometric-functions)  
  
     De **Trigonometrische** categorie omvat alle standaard trigonometrische functies. U bijvoorbeeld radialen omzetten in graden of rekenfuncties zoals raaklijnen in radialen of graden.
     Deze functies zijn unair, wat betekent dat ze één kolom met waarden als invoer nemen, de trigonometrische functie toepassen en een kolom met waarden als resultaat retourneren.  Daarom moet u ervoor zorgen dat de invoerkolom het juiste type is en de juiste soort waarden bevat voor de opgegeven bewerking.   

## <a name="how-to-configure-apply-math-operation"></a>Bewerking Wiskunde toepassen configureren  

De module **Wiskundige bewerking toepassen** vereist een gegevensset die ten minste één kolom bevat die alleen getallen bevat. De getallen kunnen discreet of continu zijn, maar moeten van een numeriek gegevenstype zijn, geen tekenreeks.

U dezelfde bewerking toepassen op meerdere numerieke kolommen, maar alle kolommen moeten zich in dezelfde gegevensset bevindt. 

Elk exemplaar van deze module kan slechts één type bewerking tegelijk uitvoeren. Als u complexe wiskundige bewerkingen wilt uitvoeren, moet u mogelijk verschillende exemplaren van de module Wiskundige bewerking toepassen aan elkaar **ketenen.**  
  
1.  Voeg de module **Wiskundige bewerking toepassen** toe aan uw pijplijn.

1. Sluit een gegevensset aan die ten minste één numerieke kolom bevat.  

1.  Selecteer een of meer bronkolommen waarop u de berekening wilt uitvoeren.   
  
    - Elke kolom die u kiest, moet een numeriek gegevenstype zijn. 
    - Het gegevensbereik moet geldig zijn voor de geselecteerde wiskundige bewerking. Anders kan er een fout of NaN (niet een getal) resultaat optreden. Ln(-1.0) is bijvoorbeeld een ongeldige bewerking en `NaN`resulteert in een waarde van .
  
1.  Klik **op Categorie** om het **type** wiskundige bewerking te selecteren dat moet worden uitgevoerd.
    
1. Kies een specifieke bewerking in de lijst in die categorie.
  
1.  Stel extra parameters in die vereist zijn voor elk type bewerking.  
  
1.  Gebruik de optie **Uitvoermodus** om aan te geven hoe u de wiskundige bewerking wilt genereren: 

    - **Toevoegen**. Alle kolommen die als invoer worden gebruikt, zijn opgenomen in de uitvoergegevensset, plus een extra kolom die de resultaten van de wiskundige bewerking bevat.
    - **Op zijn plaats.** De waarden in de kolommen die als ingangen worden gebruikt, worden vervangen door de nieuwe berekende waarden. 
    - **Resultaatalleen**. Een enkele kolom wordt geretourneerd met de resultaten van de wiskundige bewerking.
  
1.  Verzend de pijplijn.  
  
## <a name="results"></a>Resultaten

Als u de resultaten genereert met de opties **Toevoegen** of **Resultaatalleen,** geven de kolomkoppen van de geretourneerde gegevensset de bewerking en de kolommen aan die zijn gebruikt. Als u bijvoorbeeld twee kolommen vergelijkt met de operator **Gelijken,** zien de resultaten er als volgt uit:  
  
-   **Gelijkisten(Col2_Col1)**, wat aangeeft dat u Col2 hebt getest tegen Col1.  
-   **Gelijkisten(Col2_$10)**, wat aangeeft dat u kolom 2 vergeleek met de constante 10.  

Zelfs als u de optie **Inplace** gebruikt, worden de brongegevens niet verwijderd of gewijzigd. de kolom in de oorspronkelijke gegevensset is nog steeds beschikbaar in de ontwerper. Als u de oorspronkelijke gegevens wilt weergeven, u de module [Kolommen toevoegen](add-columns.md) aansluiten en deze aansluiten bij de uitvoer van Bewerking **Wiskunde toepassen**.  
    
## <a name="basic-math-operations"></a>Basiswiskundige bewerkingen 

De functies in de categorie **Basis** nemen meestal één waarde uit een kolom, voeren de vooraf gedefinieerde bewerking uit en retourneren één waarde. Voor sommige functies u een constante of een kolomset opgeven als een tweede argument.  
  
 Azure Machine Learning ondersteunt de volgende functies in de categorie **Basis:**  

### <a name="abs"></a>Abs

Geeft als resultaat de absolute waarde van de geselecteerde kolommen.  
  
### <a name="atan2"></a>Atan2

Retourneert een omgekeerde raaklijn van vier kwadranten.  

Selecteer de kolommen die de puntcoördinaten bevatten. Voor het tweede argument, dat overeenkomt met de x-coördinaat, u ook een constante opgeven.  

Komt overeen met de ATAN2-functie in Matlab.  

### <a name="conj"></a>Conj (Conj)

Geeft als resultaat de vervoeging voor de waarden in de geselecteerde kolom.  

### <a name="cuberoot"></a>CubeRoot CubeRoot

Berekent de kubuswortel voor de waarden in de geselecteerde kolom.  

### <a name="doublefactorial"></a>DoubleFactorial  
 Berekent de dubbele factorial voor waarden in de geselecteerde kolom. De dubbele factoriële is een uitbreiding van de normale factoriële functie, en het wordt aangeduid als x!!.  

### <a name="eps"></a>Eps

Geeft als resultaat de grootte van de kloof tussen de huidige waarde en het op één na hoogste getal met dubbele precisie. Komt overeen met de EPS-functie in Matlab.  
  
### <a name="exp"></a>Exp

Retourneert e verhoogd naar de kracht van de waarde in de geselecteerde kolom. Dit is hetzelfde als de functie Excel EXP.  

### <a name="exp2"></a>Exp2

Geeft als resultaat de basis-2 exponentiële van de argumenten, oplossen voor y = x * 2<sup>t</sup> waar t is een kolom van waarden met exponenten.  

Selecteer **in kolomset**de kolom met de exponentwaarden t.

Voor **Exp2** u een tweede argument x opgeven, dat een constante of een andere kolom met waarden kan zijn. Geef in **Het argument Tweede**tekst aan of u de vermenigvuldiger x als constante of een waarde in een kolom wilt geven.  

Als u bijvoorbeeld een kolom selecteert {0,1,2,3,4,5} met de waarden voor zowel de vermenigvuldiger als de exponent, geeft de functie {0, 2, 8, 24, 64 160) als resultaat.  

### <a name="expminus1"></a>ExpMinus1 

Geeft als resultaat de negatieve exponent voor waarden in de geselecteerde kolom.  

### <a name="factorial"></a>Faculteit
Geeft als resultaat de factorial voor waarden in de geselecteerde kolom.  

### <a name="hypotenuse"></a>Hypotenuse Hypotenuse
Berekent de hypotenuse voor een driehoek waarin de lengte van één zijde is opgegeven als een kolom met waarden en de lengte van de tweede zijde wordt opgegeven als een constante of als twee kolommen.  

### <a name="ln"></a>Ln

Geeft als resultaat de natuurlijke logaritme voor de waarden in de geselecteerde kolom.  

### <a name="lnplus1"></a>LnPlus1 (LnPlus1)

Retourneert de natuurlijke logaritme plus één voor de waarden in de geselecteerde kolom.  

### <a name="log"></a>Logboek

Geeft als resultaat het logboek van de waarden in de geselecteerde kolom, gezien de opgegeven basis.  

U de basis (het tweede argument) opgeven als een constante of door een andere kolom met waarden te selecteren.  

### <a name="log10"></a>Log10

Geeft als resultaat de basiswaarden van 10 logaritmewaarden voor de geselecteerde kolom.  

### <a name="log2"></a>Log2

Geeft als resultaat de basis2 logaritmewaarden voor de geselecteerde kolom.  

### <a name="nthroot"></a>NthRoot NthRoot
Geeft als resultaat de zoveelste hoofdvan de waarde met behulp van een n die u opgeeft.  

Selecteer de kolommen waarvoor u de hoofdmap wilt berekenen met de optie **ColumnSet.**  

Selecteer in **Tweede argumenttekst**een andere kolom die de hoofdmap bevat of geef een constante op die als hoofd moet worden gebruikt.  

Als het tweede argument een kolom is, wordt elke waarde in de kolom gebruikt als de waarde van n voor de overeenkomstige rij. Als het tweede argument een constante is, typt u de waarde voor n in het tekstvak **Tweede argument.**
### <a name="pow"></a>Pow

Berekent X verhoogd tot de kracht van Y voor elk van de waarden in de geselecteerde kolom.  

Selecteer eerst de kolommen die de **basis**bevatten , die een vlotter moet zijn, met de optie **ColumnSet.**  

Selecteer in **Tweede argumenttekst**de kolom die de exponent bevat of geef een constante op die u als exponent wilt gebruiken.  

Als het tweede argument een kolom is, wordt elke waarde in de kolom gebruikt als exponent voor de overeenkomstige rij. Als het tweede argument een constante is, typt u de waarde voor de exponent in het tekstvak **Tweede argument.**  

### <a name="sqrt"></a>Sqrt

Geeft als resultaat de vierkantswortel van de waarden in de geselecteerde kolom.  

### <a name="sqrtpi"></a>SqrtPi SqrtPi

Vermenigvuldigt voor elke waarde in de geselecteerde kolom de waarde met pi en geeft de vierkantswortel van het resultaat als resultaat.  

### <a name="square"></a>Square

De waarden in de geselecteerde kolom wordt gevierkant.  

## <a name="comparison-operations"></a>Vergelijkingsoperaties  

Gebruik de vergelijkingsfuncties in Azure Machine Learning-ontwerper op elk gewenst moment die u nodig hebt om twee sets waarden tegen elkaar te testen. In een pijplijn moet u bijvoorbeeld de volgende vergelijkingsbewerkingen uitvoeren:  

- Evalueer een kolom met waarschijnlijkheidsscores model op basis van een drempelwaarde.
- Bepaal of twee reeksen resultaten hetzelfde zijn. Voeg voor elke rij die anders is een VALSE vlag toe die kan worden gebruikt voor verdere verwerking of filtering.  

### <a name="equalto"></a>GelijkAan

Geeft als resultaat True als de waarden hetzelfde zijn.  

### <a name="greaterthan"></a>GreaterThan

Geeft als resultaat True als de waarden in **kolomset** groter zijn dan de opgegeven constante of groter zijn dan de overeenkomstige waarden in de vergelijkingskolom.  

### <a name="greaterthanorequalto"></a>GreaterthanorEqualto

Geeft als resultaat True als de waarden in **kolomset** groter zijn dan of gelijk zijn aan de opgegeven constante of groter zijn dan of gelijk zijn aan de overeenkomstige waarden in de vergelijkingskolom.  

### <a name="lessthan"></a>LessThan

Geeft als resultaat True als de waarden in **kolomset** lager zijn dan de opgegeven constante of kleiner zijn dan de overeenkomstige waarden in de vergelijkingskolom.  
  
### <a name="lessthanorequalto"></a>Lessthanorequalto

Geeft als resultaat True als de waarden in **kolomset** kleiner zijn dan of gelijk zijn aan de opgegeven constante of kleiner zijn dan of gelijk zijn aan de overeenkomstige waarden in de vergelijkingskolom.  

### <a name="notequalto"></a>NotEqualTo NotEqualTo

Geeft als resultaat True als de waarden in **kolomset** niet gelijk zijn aan de constante of vergelijkingskolom en als resultaat Onwaar als ze gelijk zijn.  

### <a name="pairmax"></a>PairMax

Geeft als resultaat de waarde die groter is: de waarde in **kolomset** of de waarde in de constante of vergelijkingskolom.  

### <a name="pairmin"></a>PairMin (PairMin)

Geeft als resultaat de waarde die kleiner is: de waarde in **kolomset** of de waarde in de constante of vergelijkingskolom  
  
##  <a name="arithmetic-operations"></a>Rekenkundige bewerkingen   

Inclusief de basisrekenkundige bewerkingen: optellen en aftrekken, delen en vermenigvuldigen.  Omdat de meeste bewerkingen binair zijn en twee getallen vereisen, kiest u eerst de bewerking en kiest u vervolgens de kolom of getallen die u in de eerste en tweede argumenten wilt gebruiken.

De volgorde waarin u de kolommen voor verdeling en aftrekking kiest, lijkt misschien contra-intuïtief; Om het echter gemakkelijker te maken om de resultaten te begrijpen, bevat de kolomkop de bewerkingsnaam en de volgorde waarin de kolommen zijn gebruikt.

Bewerking|Num1|Num2|Resultaatkolom|Resultaatwaarde|
----|----|----|----|----
|Optelling|1|5|Toevoegen(Num2_Num1)| 4|
|Vermenigvuldiging|1|5|Meervoudig(Num2_Num1)|5|
|Aftrekking|1|5|Aftrekken(Num2_Num1)|4|
|Aftrekking|0|1|Aftrekken(Num2_Num1)|0|
|Deling|1|5|Verdelen(Num2_Num1)|5|
|Deling|0|1|Verdelen(Num2_Num1)|Oneindig|

### <a name="add"></a>Toevoegen

Geef de bronkolommen op met **kolomset**en voeg vervolgens aan de waarden een getal toe dat is opgegeven in **het tweede argument**.  

Als u de waarden in twee kolommen wilt toevoegen, kiest u een kolom of kolommen met **kolomset**en kiest u een tweede kolom met **het argument Tweede .**  

### <a name="divide"></a>Delen

Verdeelt de waarden in **kolom die is ingesteld** door een constante of door de kolomwaarden die zijn gedefinieerd in het tweede **argument**.  Met andere woorden, kies je eerst de deler, en dan het dividend. De uitvoerwaarde is het quotiënt.

### <a name="multiply"></a>Vermenigvuldigen

Hiermee worden de waarden in **kolom ingesteld vermenigvuldigd** met de opgegeven constante of kolomwaarden.  

### <a name="subtract"></a>Aftrekken

Geef de kolom met waarden op waarop u moet werken (de *minuend),* door een andere kolom te kiezen met de optie **Kolomset.** Geef vervolgens het getal op dat moet worden afgetrokken (de *subtrageen)* met behulp van de vervolgkeuzelijst **Tweede argument.** U een constante of kolom met waarden kiezen.

##  <a name="rounding-operations"></a>Afrondingsbewerkingen 

Azure Machine Learning-ontwerper ondersteunt verschillende afrondingsbewerkingen. Voor veel bewerkingen moet u de mate van precisie opgeven die moet worden gebruikt bij het afronden. U een statisch precisieniveau gebruiken, opgegeven als een constante, of u een dynamische precisiewaarde toepassen die is verkregen uit een kolom met waarden.  

- Als u een constante gebruikt, stelt u **Precisietype** in op **Constant** en typt u het aantal cijfers als een geheel getal in het tekstvak **Constant Precision.** Als u een niet-geheel getal typt, leidt de module niet tot een fout, maar kunnen de resultaten onverwacht zijn.  

- Als u een andere precisiewaarde wilt gebruiken voor elke rij in uw gegevensset, stelt u **Precision Type** in op **ColumnSet**en kiest u de kolom met de juiste precisiewaarden.  

### <a name="ceiling"></a>Ceiling

Geeft als resultaat het plafond voor de waarden in **kolomset**.  

### <a name="ceilingpower2"></a>PlafondPower2

Geeft als resultaat het kwadraatplafond voor de waarden in **kolomset**.  

### <a name="floor"></a>Floor

Geeft als resultaat de vloer voor de waarden in **Kolomset**, op de opgegeven precisie.  

### <a name="mod"></a>Mod

Geeft als resultaat het fractionele deel van de waarden in **kolomset**, op de opgegeven precisie.  

### <a name="quotient"></a>Quotiënt

Geeft als resultaat het fractionele deel van de waarden in **kolomset**, op de opgegeven precisie.  

### <a name="remainder"></a>Rest

Geeft als resultaat de rest voor de waarden in **kolomset**.  

### <a name="rounddigits"></a>Rondecijfers

Geeft als resultaat de waarden in **kolomset**, afgerond door de 4/5-regel naar het opgegeven aantal cijfers.  

### <a name="rounddown"></a>RoundDown

Geeft als resultaat de waarden in **kolomset**, afgerond naar beneden naar het opgegeven aantal cijfers.  

### <a name="roundup"></a>RoundUp

Geeft als resultaat de waarden in **kolomset**, naar boven afgerond op het opgegeven aantal cijfers.  

### <a name="toeven"></a>ToEven (ToEven)

Geeft als resultaat de waarden in **kolomset**, afgerond op het dichtstbijzijnde geheel, even getal.  

### <a name="toodd"></a>ToOdd (ToOdd)

Geeft als resultaat de waarden in **kolomset**, afgerond op het dichtstbijzijnde geheel, oneven getal.  

### <a name="truncate"></a>Truncate

De waarden in **Kolom die zijn ingesteld,** worden afgekapt door alle cijfers te verwijderen die niet zijn toegestaan met de opgegeven precisie.  
  
## <a name="special-math-functions"></a>Speciale wiskundige functies

Deze categorie omvat gespecialiseerde wiskundige functies die vaak worden gebruikt in de data science. Tenzij anders vermeld, is de functie unary en retourneert de opgegeven berekening voor elke waarde in de geselecteerde kolom of kolommen.  

### <a name="beta"></a>Bèta

Geeft als resultaat de waarde van de bètafunctie van Euler.  

### <a name="ellipticintegrale"></a>EllipticIntegralE EllipticIntegralE
Geeft als resultaat de waarde van de onvolledige elliptische integraal.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK EllipticIntegralK

Geeft als resultaat de waarde van de volledige elliptische integraal (K).  

### <a name="erf"></a>Evf

Geeft als resultaat de waarde van de foutfunctie.  

De foutfunctie (ook wel de Gauss-foutfunctie genoemd) is een speciale functie van de sigmoïdevorm die waarschijnlijk wordt gebruikt om diffusie te beschrijven.  

### <a name="erfc"></a>Erfc Erfc

Geeft als resultaat de waarde van de aanvullende foutfunctie.  

Erfc wordt gedefinieerd als 1 – erf(x).  

### <a name="erfscaled"></a>ErfGeschaald

Geeft als resultaat de waarde van de functie geschaalde fout.  

De geschaalde versie van de foutfunctie kan worden gebruikt om rekenkundige onderstroom te voorkomen.  

### <a name="erfinverse"></a>ErfInverse

Geeft als resultaat de waarde van de omgekeerde erffunctie.  

### <a name="exponentialintegralein"></a>Exponentieel IntegralEin

Geeft als resultaat de waarde van de exponentiële integrale Ei.  

### <a name="gamma"></a>Gamma gamma

Geeft als resultaat de waarde van de gammafunctie.  

### <a name="gammaln"></a>GammaLn (GammaLn)

Geeft als resultaat de natuurlijke logaritme van de gammafunctie.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Geeft als resultaat de waarde van de geregulariseerde onvolledige gammafunctie.  

Deze functie neemt een tweede argument, dat kan worden verstrekt als een constante of een kolom van waarden.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Geeft als resultaat de waarde van de omgekeerde geregulariseerde gammafunctie.  

Deze functie neemt een tweede argument, dat kan worden verstrekt als een constante of een kolom van waarden.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ GammaRegularizedQ  

Geeft als resultaat de waarde van de geregulariseerde onvolledige gammafunctie.  

Deze functie neemt een tweede argument, dat kan worden verstrekt als een constante of een kolom van waarden.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Geeft als resultaat de waarde van de omgekeerde gegeneraliseerde geregulariseerde, onvolledige gammafunctie.

Deze functie neemt een tweede argument, dat kan worden verstrekt als een constante of een kolom van waarden.  

### <a name="polygamma"></a>PolyGamma (PolyGamma)

Geeft als resultaat de waarde van de polygame functie.  

Deze functie neemt een tweede argument, dat kan worden verstrekt als een constante of een kolom van waarden.

##  <a name="trigonometric-functions"></a>Trigonometrische functies 

Deze categorie iOmvat de meeste belangrijke trigonometrische en omgekeerde trigonometrische functies. Alle trigonometrische functies zijn unary en vereisen geen extra argumenten.  

### <a name="acos"></a>Acos

Berekent de arccosine voor de kolomwaarden.  

### <a name="acosdegree"></a>AcosDegree AcosDegree

Berekent de arccosine van de kolomwaarden in graden.  

### <a name="acosh"></a>Acosh Acosh

Berekent de hyperbolische arccosine van de kolomwaarden.  

### <a name="acot"></a>Acot

Berekent de arccotangens van de kolomwaarden.  

### <a name="acotdegrees"></a>AcotDegrees

Berekent de arccotangens van de kolomwaarden in graden.  

### <a name="acoth"></a>Acoth Acoth

Berekent de hyperbolische arccotangens van de kolomwaarden.  

### <a name="acsc"></a>Acsc Acsc

Berekent de arccosecant van de kolomwaarden.  

### <a name="acscdegrees"></a>AcscGraden AcscDegrees

Berekent de arccosecant van de kolomwaarden in graden.  
### <a name="asec"></a>Asec

Berekent de arcsecans van de kolomwaarden.  

### <a name="asecdegrees"></a>AsecDegrees AsecDegrees

Berekent de arcsecans van de kolomwaarden in graden.  

### <a name="asech"></a>Asech Asech

Berekent de hyperbolische arcsecant van de kolomwaarden.  

### <a name="asin"></a>Asin

Berekent de arcsine van de kolomwaarden.  

### <a name="asindegrees"></a>AsinDegrees AsinDegrees

Berekent de arcsine van de kolomwaarden in graden.  

### <a name="asinh"></a>Asinh Asinh

Berekent de hyperbolische arcsine voor de kolomwaarden.  

### <a name="atan"></a>Atan

Berekent de arctangens van de kolomwaarden.  

### <a name="atandegrees"></a>AtanDegrees AtanDegrees

Berekent de arctangens van de kolomwaarden in graden.  

### <a name="atanh"></a>Atanh Atanh

Berekent de hyperbolische arctangens van de kolomwaarden.  

### <a name="cos"></a>Cos

Berekent de cosine van de kolomwaarden.  

### <a name="cosdegrees"></a>CosDegrees CosDegrees

Berekent de cosine voor de kolomwaarden in graden.  

### <a name="cosh"></a>Cosh

Berekent de hyperbolische cosine voor de kolomwaarden.  

### <a name="cot"></a>Cot

Berekent de cotangens voor de kolomwaarden.  

### <a name="cotdegrees"></a>CotDegrees CotDegrees

Berekent de cotangens voor de kolomwaarden in graden.  

### <a name="coth"></a>Coth Coth
Berekent de hyperbolische cotangent voor de kolomwaarden.  

### <a name="csc"></a>Csc

Berekent het cosecant voor de kolomwaarden.  

### <a name="cscdegrees"></a>CscDegrees CscDegrees

Berekent de cosecant voor de kolomwaarden in graden.  

### <a name="csch"></a>Csch Csch

Berekent de hyperbolische cosecant voor de kolomwaarden.  

### <a name="degreestoradians"></a>DegreesToRadians

Converteert graden naar radialen.  

### <a name="sec"></a>Sec

Berekent de secans van de kolomwaarden.  

### <a name="asecdegrees"></a>aSecDegrees

Berekent de secant voor de kolomwaarden in graden.  

### <a name="asech"></a>aSech

Berekent de hyperbolische secant van de kolomwaarden.  

### <a name="sign"></a>Teken

Geeft als resultaat het teken van de kolomwaarden.  

### <a name="sin"></a>Sin

Berekent de sinin van de kolomwaarden.  

### <a name="sinc"></a>Sinc Sinc

Berekent de sine-cosinewaarde van de kolomwaarden.  

### <a name="sindegrees"></a>SinDegrees

Berekent de sine voor de kolomwaarden in graden.  

### <a name="sinh"></a>Sinh

Berekent de hyperbolische sine van de kolomwaarden.  

### <a name="tan"></a>Tan

Berekent de raaklijn van de kolomwaarden.  

### <a name="tandegrees"></a>TanDegrees TanDegrees

Berekent de raaklijn voor het argument, in graden.  

### <a name="tanh"></a>Tanh

Berekent de hyperbolische raaklijn van de kolomwaarden.  
  
## <a name="technical-notes"></a>Technische notities

Wees voorzichtig wanneer u meer dan één kolom als tweede operator selecteert. De resultaten zijn gemakkelijk te begrijpen als de bewerking eenvoudig is, zoals het toevoegen van een constante aan alle kolommen. 

Stel dat uw gegevensset meerdere kolommen heeft en dat u de gegevensset aan zichzelf toevoegt. In de resultaten wordt elke kolom als volgt aan zichzelf toegevoegd:  
  
|Num1|Num2|Num3|Toevoegen(Num1_Num1)|Toevoegen(Num2_Num2)|Toevoegen(Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Als u complexere berekeningen moet uitvoeren, u meerdere exemplaren van **Bewerking Wiskunde toepassen.** U bijvoorbeeld twee kolommen toevoegen door een instantie van **Bewerking Wiskunde toepassen**te gebruiken en vervolgens een ander exemplaar van Bewerking Wiskunde **toepassen** gebruiken om de som door een constante te verdelen om het gemiddelde te verkrijgen.  
  
U ook een van de volgende modules gebruiken om alle berekeningen tegelijk uit te voeren, met SQL-, R- of Python-script:
 
+ [R-Script uitvoeren](execute-r-script.md)
+ [Python-script uitvoeren](execute-python-script.md)
+ [SQL-transformatie toepassen](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 
