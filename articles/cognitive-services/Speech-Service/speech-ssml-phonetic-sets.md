---
title: Spraakfonetische sets - Spraakservice
titleSuffix: Azure Cognitive Services
description: Leer hoe je de spraakservice fonetische alfabetkaarten inkaart bij het International Fonetische Alfabet (IPA) en wanneer je welke set moet gebruiken.
services: cognitive-services
author: zhaoyunED
manager: junwg
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: jiajzhan
ms.openlocfilehash: 770e97ad126f66efb43bf8cf7eb12f7510858192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675329"
---
# <a name="speech-service-phonetic-sets"></a>Spraakservice fonetische sets

De spraakdienst definieert fonetische alfabetten ("telefoonsets" kort), bestaande uit zeven talen; `en-US`, `fr-FR` `de-DE`, `es-ES` `ja-JP`, `zh-CN`, `zh-TW`, en . De spraakservicetelefoon stelt doorgaans de kaart in op het <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Internationale Fonetische <span class="docon docon-navigate-external x-hidden-focus"> </span>Alfabet (IPA). </a> Spraakservicetelefoonsets worden gebruikt in combinatie met de [Spraaksyntheseopmaaktaal (SSML),](speech-synthesis-markup.md)als onderdeel van het aanbod van de tekst-naar-spraakservice. In dit artikel leert u hoe deze telefoonsets in kaart worden gebracht en wanneer u welke telefoonset moet gebruiken.

# <a name="en-us"></a>[nl-VS](#tab/en-US)

### <a name="english-suprasegmentals"></a>Engelse suprasegmentals

| Voorbeeld 1 (Begin voor medeklinker, woordinitiaal voor klinker) | Voorbeeld 2 (Intervocalic voor medeklinker, woord mediale kern voor klinker) | Voorbeeld 3 (Coda voor medeklinker, woord definitief voor klinker) | Opmerkingen |
|--|--|--|--|
| burger /b er **1** r - g ax r/ | falafel /f ax - l aa **1** - f ax l/ | gitaar /g ih - t aa **1** r/ | De reeks van de de diensttelefoon zet spanning na de klinker van de beklemtoonde lettergreep |
| inopportune /ih **2** - n aa - p ax r - t uw 1 n/ | dissimilarity /d ih - s ih **2**- m ax - l eh 1 - r ax - t iy/ | personeelsbestand /w er 1 r k - f ao **2** r s/ | De reeks van de de diensttelefoon zet spanning na de klinker van de onder-beklemtoonde lettergreep |

### <a name="english-vowels"></a>Engelse klinkers

| `sapi` | `ipa` | Voorbeeld 1     | Voorbeeld 2 | Voorbeeld 3                   |
|--------|-------|---------------|-----------|-----------------------------|
| iy iy     | `i`   | **ea**t       | f**ee**l  | vall**ey**                  |
| Ih     | `ɪ`   | **i**f        | f**i**ll  |                             |
| Ey     | `eɪ`  | **een**te       | g**een**te  | d**ay**                     |
| Eh     | `ɛ`   | **e**zeer     | p**e**t   | m**eh** (zeldzaam woord definitief) |
| Ae     | `æ`   | **een**ctive    | c**a**t   | n**ah** (zeldzaam woord eindelijk) |
| aa     | `ɑ`   | **o**bstinate | p**o**ppy | r**ah** (zeldzaam woord tot slot) |
| ao     | `ɔ`   | **o**bereik    | c**au**se | Ut**ah**                    |
| Uh     | `ʊ`   | b**oo**k      |           |                             |
| ow     | `oʊ`  | **o**ld       | cl**o**ne | g**o**                      |
| uw     | `u`   | **U**ber      | b**oo**st | t**oo**                     |
| Ah     | `ʌ`   | **u**ncle     | c**u**t   |                             |
| Ay     | `aɪ`  | **i**ce       | b**i**te  | fl**y**                     |
| Aw     | `aʊ`  | **ou**t       | s**ou**th | c**ow**                     |
| Oy     | `ɔɪ`  | **oi**l       | j**oi**n  | t**oy**                     |
| y uw   | `ju`  | **Yu**ma      | h**u**man | f**ew**                     |
| Ax     | `ə`   | **een**go       | wom**a**n | zijn**een**                    |

### <a name="english-r-colored-vowels"></a>Engelse R-gekleurde klinkers

| `sapi` | `ipa` | Voorbeeld 1    | Voorbeeld 2      | Voorbeeld 3  |
|--------|-------|--------------|----------------|------------|
| ih r   | `ɪɹ`  | **oor**s     | t**ir**amisu   | n**oor**   |
| eh r   | `ɛɹ`  | **luchtvliegtuig** | app**ar**ently | sc**ar e**  |
| uh r   | `ʊɹ`  |              |                | c**uw**e   |
| ay r   | `aɪɹ` | **Ire**land  | f**ir**eplace  | ch**oir**  |
| aw r   | `aʊɹ` | **uur**s    | p**ower**ful   | s**onze**   |
| ao r   | `ɔɹ`  | **of**ange   | m**of**al      | s**roeispaan**   |
| aa r   | `ɑɹ`  | **ar**tist   | st**ar**t      | c**ar**    |
| er r   | `ɝ`   | **oor**th    | b**ir**d       | f**uw**    |
| ax r   | `ɚ`   |              | alle**er**gy    | supp**er** |

### <a name="english-semivowels"></a>Engelse Semiklinkers

| `sapi` | `ipa` | Voorbeeld 1           | Voorbeeld 2  | Voorbeeld 3 |
|--------|-------|---------------------|------------|-----------|
| w      | `w`   | **w**ith, s**ue**de | al**w**ays |           |
| y      | `j`   | **y**ard, f**e**w   | op**i**op  |           |

### <a name="english-aspirated-oral-stops"></a>Engels aangezogen mondelinge stops

| `sapi` | `ipa` | Voorbeeld 1 | Voorbeeld 2   | Voorbeeld 3  |
|--------|-------|-----------|-------------|------------|
| p      | `p`   | **p**ut   | ha**pp**en  | fla**p**   |
| b      | `b`   | **b**ig   | num**b**er  | cra**b**   |
| t      | `t`   | **t**alk  | capi**t**al | sough**t** |
| d      | `d`   | **d**ig   | liep**d**om  | ro**d**    |
| k      | `k`   | **c**ut   | sla**ck**er | Ira**q**   |
| g      | `g`   | **g**o    | a**g**o     | dra**g**   |

### <a name="english-nasal-stops"></a>Engels Neus stopt

| `sapi` | `ipa` | Voorbeeld 1        | Voorbeeld 2  | Voorbeeld 3   |
|--------|-------|------------------|------------|-------------|
| m      | `m`   | **m**op, smash   | ca**m**tijdperk | roo**m**    |
| n      | `n`   | **n**o, s**n**ow | te**n**t   | chicke**n** |
| Ng     | `ŋ`   |                  | li**n**k   | s**ing**    |

### <a name="english-fricatives"></a>Engelse fricatives

| `sapi` | `ipa` | Voorbeeld 1   | Voorbeeld 2        | Voorbeeld 3  |
|--------|-------|-------------|------------------|------------|
| v      | `f`   | **f**ork f ork f ork f or    | le**f**t         | hal**f**   |
| v      | `v`   | **v**alue   | e**v**ent        | lo**v**e   |
| Th     | `θ`   | **th**in    | empa**th**y      | mon**th**  |
| Dh     | `ð`   | **th**en    | mo**th**er       | smoo**th** |
| s      | `s`   | **s**it     | ri**s**k         | feit**s**  |
| z      | `z`   | **z**ap     | bu**s**y         | kid**s**   |
| Sh     | `ʃ`   | **sh** e    | abbrevia**ti**on | ru**sh**   |
| Zh     | `ʒ`   | **J**acques | plea**s**ure     | gara**g**g. |
| h      | `h`   | **h**elp    | nl**h**ance      | a-**h**a!  |

### <a name="english-affricates"></a>Engels affricates

| `sapi` | `ipa` | Voorbeeld 1 | Voorbeeld 2    | Voorbeeld 3  |
|--------|-------|-----------|--------------|------------|
| ch     | `tʃ`  | **ch**in  | fu**t**ure   | atta**ch** |
| Jh     | `dʒ`  | **j**oy   | ori**g**inal | oran**g.** |

### <a name="english-approximants"></a>Engelse caisten

| `sapi` | `ipa` | Voorbeeld 1          | Voorbeeld 2  | Voorbeeld 3 |
|--------|-------|--------------------|------------|-----------|
| l      | `l`   | **l**id, g**l**ad  | pa**l**aas | chi**ll** |
| r      | `ɹ`   | **r**ed, b**r**ing | bo**rr**ow | ta**r**   |

# <a name="fr-fr"></a>[fr-FR](#tab/fr-FR)

### <a name="french-suprasegmentals"></a>Franse suprasegmentals

De de diensttelefoonreeks van de Toespraak zet spanning na de klinker van de beklemtoonde lettergreep, nochtans; de `fr-FR` spraakservicetelefoonset ondersteunt de IPA-substress '' niet. Als de IPA-substress nodig is, moet u de IPA rechtstreeks gebruiken.

### <a name="french-vowels"></a>Franse klinkers

| `sapi` | `ipa` | Voorbeeld 1     | Voorbeeld 2       | Voorbeeld 3 |
|--------|-------|---------------|-----------------|-----------|
| a      | `a`   | **een**rbre     | p**a**tte       | ir**a**   |
| aa     | `ɑ`   |               | p**â**te        | p**een**s   |
| aa ~   | `ɑ̃`  | **nl**fant    | enf**en**t      | t**em**ps |
| Ax     | `ə`   |               | p**e**tite      | L**e**    |
| Eh     | `ɛ`   | **e**lle      | p**e**rdu       | ét**ai**t |
| Eu     | `ø`   | **œu**fs      | cr**eu**ser     | qu**eu**  |
| Ey     | `e`   | ému ému           | crétin          | ôté ôté       |
| eh ~   | `ɛ̃`  | **im**portant im portant | p**ein**ture    | mat**in** |
| iy iy     | `i`   | **i**dée      | huisdier**i**te      | ben**ik**   |
| Oe     | `œ`   | **œu**f       | p**eu**r        |           |
| Oh     | `ɔ`   | **o**bstacle  | c**o**rps       |           |
| oh ~   | `ɔ̃`  | **op**ze      | r**op**deur     | b**op**   |
| ow     | `o`   | **au**diteur  | b**eau**coup    | p**ô**    |
| oe ~   | `œ̃ ` | **Vn**        | L**un**di       | br**un**  |
| uw     | `u`   | **ou**trage   | intr**ou**vable | **Ou**    |
| Uy     | `y`   | **u**ne       | p**u**nir       | él**u**   |

### <a name="french-consonants"></a>Franse medeklinkers

| `sapi` | `ipa` | Voorbeeld 1   | Voorbeeld 2     | Voorbeeld 3                        |
|--------|-------|-------------|---------------|----------------------------------|
| b      | `b`   | **b**ête    | ha**b**ille   | ro**b**e                         |
| d      | `d`   | **d**ire    | ron**d**eur   | chau**d**e                       |
| v      | `f`   | **f**emme f emme   | su**ff**ixe   | bo**f**                          |
| g      | `g`   | **g**auche  | é**g**ale     | ba**gu**e                        |
| Ng     | `ŋ`   |             |               | [<sup>1</sup>](#fr-1)**parkeren** |
| Hy     | `ɥ`   | h**u**ile   | n**u**ire     |                                  |
| k      | `k`   | **c**arte   | é**c**aille   | zijn**c**                          |
| l      | `l`   | **L**ong    | é**l**ire     | ba**l**                          |
| m      | `m`   | **m**adame  | ai**m**er     | po**mm**e                        |
| n      | `n`   | **n**ous    | te**n**ir     | bo**nn**e                        |
| Nj     | `ɲ`   |             |               | pei**gn**e                       |
| p      | `p`   | **p**atte   | re**p**als     | ca**p**                          |
| r      | `ʁ`   | **r**bij     | cha**r**iot   | senti**r**                       |
| s      | `s`   | **s**ourir  | een**ss**ez     | pa**ss**e                        |
| Sh     | `ʃ`   | **ch**anter | ma**ch**ine   | po**ch**e                        |
| t      | `t`   | **t**ête    | ô**t**er      | ne**t**                          |
| v      | `v`   | **v**ent    | in**v**enter  | rê**v**e                         |
| w      | `w`   | **ou**i     | f**ou**ine    |                                  |
| y      | `j`   | **y**od     | p**i**étiner  | Marse**ille**                    |
| z      | `z`   | **z **éro   | rai**s**onner | ro**s**e                         |
| Zh     | `ʒ`   | **j**ardin  | man**g**er    | piè**g.**                        |
|        | `n‿`  |             |               | u**n** arbre                     |
|        | `t‿`  |             |               | quan**d**                        |
|        | `z‿`  |             |               | di**x**                          |

<a id="fr-1"></a>
**1** *Alleen voor sommige buitenlandse woorden.*

> [!TIP]
> De `fr-FR` telefoonset van de spraakservice ondersteunt niet `n‿`de `t‿`volgende `z‿`Franse liasions, en . Als ze nodig zijn, moet u overwegen het gebruik van de IPA direct.

# <a name="de-de"></a>[de-DE](#tab/de-DE)

### <a name="german-suprasegmentals"></a>Duitse suprasegmentals

| Voorbeeld 1 (Begin voor medeklinker, woordinitiaal voor klinker) | Voorbeeld 2 (Intervocalic voor medeklinker, woord mediale kern voor klinker) | Voorbeeld 3 (Coda voor medeklinker, woord definitief voor klinker) | Opmerkingen |
|--|--|--|--|
| anders /a **1** n - d ax r s/ | Vermenigvuldigingenzeichen /m uh l - t iy - p l iy - k a - ts y ow **1** n s - ts ay - c n/ | Biologie /b iy - ow - l ow - g iy **1**/ | De reeks van de de diensttelefoon zet spanning na de klinker van de beklemtoonde lettergreep |
| Allgemeinwissen /a **2** l - g ax - m ay 1 n - v ih - s n/ | Abfallentsorgungsfirma /a 1 p - f a l - ^ eh n t - z oh **2** ax r - g uh ng s - f ih ax r - m a/ | Computertomographie /k oh m - p y uw 1 - t ax r - t ow - m ow - g r a - f iy **2**/ | De reeks van de de diensttelefoon zet spanning na de klinker van de onder-beklemtoonde lettergreep |

### <a name="german-vowels"></a>Duitse klinkers

| `sapi` | `ipa`     | Voorbeeld 1                             | Voorbeeld 2     | Voorbeeld 3                          |
|--------|-----------|---------------------------------------|---------------|------------------------------------|
| A:     | `aː`      | **Een**ber                              | Maßst**a**b   | Schem**a**                         |
| a      | `a`       | **Een**bfall                            | B**een**ch      | Agath**a**                         |
| Oh     | `ɔ`       | **O**sten                             | Pf**o**sten   |                                    |
| Eh:    | `ɛː`      | **Ä**hnlichkeit                       | B**ä**r       | [<sup>1.</sup>](#de-v-1) Fasci**ae** |
| Eh     | `ɛ`       | **ä**ndern                            | Proz**e**nt   | Amygdal**ae**                      |
| Ax     | `ə`       | [<sup>2</sup>](#de-v-2)'v**e**rstauen | Aach**e**n    | Frag**e**                          |
| iy iy     | `iː`      | **Ik rende**weg.                              | abb**ie**gt   | Relativitätstheor**ie**            |
| Ih     | `ɪ`       | **Ik**nnung                            | s**i**ngen    | Hout**y**                          |
| Eu     | `øː`      | **Ö**sen                              | abl**ö**sten  | Malm**ö**                          |
| ow     | `o`, `oː` | **o**hne                              | Balk**o**n    | Trept**ow**                        |
| Oe     | `œ`       | **Ö**ffnung Ö ffnung                           | bef**ö**rdern |                                    |
| Ey     | `e`, `eː` | **E**berhard                          | abf**e**gt    | b                                  |
| uw     | `uː`      | **U**doen                               | H**u**t       | Akk**u**                           |
| Uh     | `ʊ`       | **U**nterschiedes                     | b**u**nt      |                                    |
| Ue     | `yː`      | **Ü**bermut Ü bermut                           | pfl**ü**gt    | Mannen**ü**                           |
| Uy     | `ʏ`       | **ü**ppig ü ppig                             | S**y**stam    |                                    |

<a id="de-v-1"></a>
**1** *Alleen in woorden van buitenlandse oorsprong, zoals: Fasci**ae**.*<br>
<a id="de-v-2"></a>
**2** *Woord-intially slechts in woorden van buitenlandse oorsprong zoals **Een**ppzal. Lettergreep-aanvankelijk in: 'v**e**rstauen.*

### <a name="german-diphthong"></a>Duitse diphthong

| `sapi` | `ipa`       | Voorbeeld 1    | Voorbeeld 2          | Voorbeeld 3 |
|--------|-------------|--------------|--------------------|-----------|
| Ay     | `ai`        | **ei**nsam   | Unabhängigk**ei**t | Abt**ei** |
| Aw     | `au`        | **au**ßen    | abb**au**st        | St**au**  |
| Oy     | `ɔy`, `ɔʏ̯` | **Eu-phorie** | tr**äu**mt         | sch**eu** |

### <a name="german-semivowels"></a>Duitse semiklinkers

| `sapi` | `ipa` | Voorbeeld 1 | Voorbeeld 2    | Voorbeeld 3  |
|--------|-------|-----------|--------------|------------|
| ax r   | `ɐ`   |           | abänd**er**n | **lock er** |

### <a name="german-consonants"></a>Duitse medeklinkers

| `sapi` | `ipa` | Voorbeeld 1 | Voorbeeld 2 | Voorbeeld 3 |
|--|--|--|--|--|
| b | `b` | **B**ank |  | [<sup>1.</sup>](#de-c-1) Pu**b** |  |
| c | `ç` | **Ch**emie | mögli**ch**st | [<sup>2</sup>](#de-c-2)i**ch** |
| d | `d` | **d**anken | [<sup>3.</sup>](#de-c-3) Len**d**l | [<sup>4.</sup>](#de-c-4) Clau**d**e |  |
| Jh | `ʤ` | **J**eff | gemana**g**t | [<sup>5.</sup>](#de-c-5) Chan**g.** |
| v | `f` | **F**ahrtdauer | angri**ff**slustig | abbruchrei**f** |  |
| g | `g` | **g**ut |  | [<sup>6.</sup>](#de-c-6) Gre**g** |  |
| h | `h` | **H**ausanbau |  |  |  |
| y | `j` | **J**od | Reakt**i**op | hu**i** |  |
| k | `k` | **K**oma | Aspe**k**t | Flec**k** |  |
| l | `l` | **L**au | ähne**l**n | zuvie**l** |  |
| m | `m` | **M**ut | Een**m**t | Leh**m** |  |
| n | `n` | **n**un | u**n**d | Huh**n** |  |
| Ng | `ŋ` | [<sup>7</sup>](#de-c-7)**Ng**uyen | Schwa**nk** | R**ing** |  |
| p | `p` | **P**artner P artner | abru**p**t | Ti p Ti**p** |  |
| Pf | `pf` | **Pf**erd | dam**pf**t | Om**pf** |  |
| r | `ʀ`, `r`, `ʁ` | **R**eise | knu**rr**t | Haa**r** |  |
| s | `s` | [<sup>8</sup>](#de-c-8)**S**taccato | bi**s**t | mie**s** |  |
| Sh | `ʃ` | **Sch**ule | mi**sch**t | lappi**sch** |  |
| t | `t` | **T**raum | S**t**raße | Mu**t** |  |
| Ts | `ts` | **Z**ug | Ar**z**t | Wit**z** |  |
| ch | `tʃ` | **Tsch**echien Tsch echien | aufgepu**tsch**t | bundesdeu**tsch** |  |
| v | `v` | **w**inken | Q**u**alle | [<sup>9.</sup>](#de-c-9) Gr**oo**ve |  |
| x | [<sup>10</sup>](#de-c-10)`x`,[<sup>11.</sup>](#de-c-11)`ç` | [<sup>12.</sup>](#de-c-12) Ba**ch**erach | Ma**ch**t mögli**ch**st | Schma**ch** 'i**ch** |
| z | `z` | **s**uper |  |  |  |
| Zh | `ʒ` | **G**enre | B**re**ezinski | Edvi**g.** |

<a id="de-c-1"></a>
**1** *Alleen in woorden van buitenlandse oorsprong, zoals: Pu**b**.*<br>
<a id="de-c-2"></a>
**2** *Soft "ch" na "e" en "i"*<br>
<a id="de-c-3"></a>
**3** *Alleen in woorden van buitenlandse oorsprong, zoals: Len**d**l.*<br>
<a id="de-c-4"></a>
**4** *Alleen in woorden van buitenlandse oorsprong zoals: Clau**d**e.*<br>
<a id="de-c-5"></a>
**5** *Slechts in woorden van buitenlandse oorsprong zoals: Chan**g.***<br>
<a id="de-c-6"></a>
**6** *Woord-terminaal alleen in woorden van buitenlandse oorsprong zoals Gre**g**.*<br>
<a id="de-c-7"></a>
**7** *Slechts in woorden van buitenlandse oorsprong zoals: **Ng**uyen.*<br>
<a id="de-c-8"></a>
**8** *Alleen in woorden van buitenlandse oorsprong zoals: **S**taccato.*<br>
<a id="de-c-9"></a>
**9** *Alleen in woorden van buitenlandse oorsprong, zoals: Gr**oo**ve.*<br>
<a id="de-c-10"></a>
**10** *De `x` IPA is een harde "ch" na alle niet-front klinkers (a, aa, oh, ow, uh, uw en de diphthong aw).*<br>
<a id="de-c-11"></a>
**11** *De `ç` IPA is een zachte 'ch' na de voorklinkers (ih, iy, eh, ae, uy, ue, oe, eu ook in diphthongs ay, oy) en medeklinkers*<br>
<a id="de-c-12"></a>
**12** *Woord-aanvankelijk slechts in woorden van buitenlandse oorsprong, zoals: **J**uan. Lettergreep-aanvankelijk ook in woorden als: Ba**ch**erach.*<br>

### <a name="german-oral-consonants"></a>Duitse orale medeklinkers

| `sapi` | `ipa` | Voorbeeld 1                                  |
|--------|-------|--------------------------------------------|
| ^      | `ʔ`   | beachtlich /b ax - ^ a 1 x t - l ih c/ |

> [!NOTE]
> We moeten een [gs\] telefoon tussen twee verschillende klinkers toe te voegen, behalve de twee klinkers zijn een echte diphthong. Deze mondelinge medeklinker is een glottale <a href="http://en.wikipedia.org/wiki/Glottal_stop" target="_blank">stop, voor <span class="docon docon-navigate-external x-hidden-focus"> </a> </a>meer informatie, zie glottale stop .

# <a name="es-es"></a>[es-ES](#tab/es-ES)

### <a name="spanish-vowels"></a>Spaanse klinkers

| `sapi` | `ipa` | Voorbeeld 1    | Voorbeeld 2     | Voorbeeld 3    |
|--------|-------|--------------|---------------|--------------|
| a      | `a`   | **een**lto     | c**een**ntar    | cas**a**     |
| i      | `i`   | **i**bérica  | av**i**spa    | belasting**i**     |
| a      | `e`   | **e**lefante | **e**nto    | elefant**e** |
| o      | `o`   | **o**caso    | enc**o**ntrar | o o**o** |
| U      | `u`   | **u**sted    | p**u**nta     | Juanl**u**   |

### <a name="spanish-consonants"></a>Spaanse medeklinkers

| `sapi` | `ipa`      | Voorbeeld 1  | Voorbeeld 2      | Voorbeeld 3      |
|--------|------------|------------|----------------|----------------|
| b      | `b`        | **b**aobab |                | am**b**        |
|        | `β`        |            | bao**b**ab     | baoba**b**     |
| ch     | `tʃ`       | **ch**eque | co**ch**e      | Marraque**ch** |
| d      | `d`        | **d**edo   |                | portlan**d**   |
|        | `ð`        |            | de**d**o       | verda**d**     |
| v      | `f`        | **f**ácil f ácil  | ele**f**ante   | pu**f**        |
| g      | `g`        | **g**anga  |                | dópin**g**     |
|        | `ɣ`        |            | a**g**ua       | tuare**g**     |
| J      | `j`        | **i**odo   | cal**i**ente   | re**y**        |
| Jj     | `j.j` `jj` |            | vi**ll**a      |                |
| k      | `k`        | **c**oker  | bo**c**a       | titáni**c**    |
| l      | `l`        | **l**ápiz  | a**l**a        | corde**l**     |
| Ll     | `ʎ`        | **ll**ave  | desarro**ll**o |                |
| m      | `m`        | **m**bestellen | a**m**ar       | álbu**m**      |
| n      | `n`        | **n**ada   | ce**n**a       | rató**n**      |
| Nj     | `ɲ`        | **ñ**aña   | ara**ñ**azo    |                |
| p      | `p`        | **p**oca   | naar**p**o       | sto**p**       |
| r      | `ɾ`        |            | ca**r**a       | abri**r**      |
| Rr     | `r`        | **r**adio  | co**rr**e      | pu**rr**       |
| s      | `s`        | **s**aco   | va**s**o       | pelo**s**      |
| t      | `t`        | **t**oldo  | a**t**ar       | disque**t**    |
| Th     | `θ`        | **z**ebra z ebra  | a**z**ul       | lápi**z**      |
| w      | `w`        | h**u**eso  | ag**u a**       | gua**u**       |
| x      | `x`        | **j**ota   | a**j**o        | relo**j**      |

> [!TIP]
> De `es-ES` telefoonset van spraakservice ondersteunt de volgende `β` `ð`Spaanse `ɣ`IPA, en . Als ze nodig zijn, moet u overwegen het gebruik van de IPA direct.

# <a name="zh-cn"></a>[zh-CN](#tab/zh-CN)

De spraakservicetelefoon `zh-CN` ingesteld voor is gebaseerd op de native telefoon <a href="https://en.wikipedia.org/wiki/Pinyin" target="_blank">Pinyin <span class="docon docon-navigate-external x-hidden-focus"></span> </a> set.

### <a name="tone"></a>Toon

| Pinyin toon | `sapi` | Tekenvoorbeeld |
|-------------|--------|-------------------|
| mā          | ma 1  | 妈                 |
| má má          | ma 2  | 麻                 |
| mṭ mṭ          | ma 3  | 马                 |
| mà mà          | ma 4  | 骂                 |
| Ma          | ma 5  | 嘛                 |

#### <a name="example"></a>Voorbeeld

| Teken | Speech Service                |
|-----------|-------------------------------|
| 组织关系      | zu 3 - zhi 1 - guan 1 - xi 5 |
| ??        | lei 3 -jin 4                 |
| 西宅巷       | xi 1 - zhai 2 - xiang 4      |

# <a name="zh-tw"></a>[zh-TW](#tab/zh-TW)

De Spraakservice telefoon `zh-TW` ingesteld voor is gebaseerd op de native telefoon <a href="https://en.wikipedia.org/wiki/Bopomofo" target="_blank">Bopomofo <span class="docon docon-navigate-external x-hidden-focus"></span> </a> ingesteld.

### <a name="tone"></a>Toon

| Spraakservicetoon | Bopomofo toon | Voorbeeld (woord) | Spraakservicetelefoons | Bopomofo | Pinyin |
|---------------------|---------------|----------------|-----------------------|----------|-------------|
| ˉ                   | leeg         | 偵              | Het is niet de eerste keer dat de                   | Het is niet de eerste keer dat de       | zhēn        |
| Het is niet de eerste                   | Het is niet de eerste             | 察              | Het is niet de eerste keer dat de                   | Het is niet de eerste keer dat de      | chá         |
| ーー                   | ーー             | 打              | Het is niet de eerste keer dat de                   | Het is niet de eerste keer dat de      | dṭ          |
| ˋ                   | ˋ             | De man heeft een              | Het is niet de eerste keer dat de                   | Het is niet de eerste keer dat de      | wàng wàng        |
| ˙                   | ˙             | 影子             | Het is niet de eerste keer dat de               | Het is niet de eerste keer dat de  | yṭng zi    |

#### <a name="example"></a>Voorbeeld

| Teken | `sapi`   |
|-----------|----------|
| Het is niet de eerste         | ㄍㄡˇ      |
| 然后        | Het is niet de eerste keer dat de   |
| Het is niet de eerste keer dat de        | Het is niet de eerste keer dat de |

# <a name="ja-jp"></a>[ja-JP](#tab/ja-JP)

De Spraakservice telefoon `ja-JP` ingesteld voor is gebaseerd op de native telefoon <a href="https://en.wikipedia.org/wiki/Kana" target="_blank">Kana <span class="docon docon-navigate-external x-hidden-focus"></span> </a> set.

### <a name="stress"></a>Stress

| `sapi` | `ipa`          |
|--------|----------------|
| `ˈ`    | `ˈ`mainstress |
| `+`    | `ˌ`substress  |

#### <a name="example"></a>Voorbeeld

| Teken | `sapi`  | `ipa`       |
|-----------|---------|-------------|
| 合成        | Het is niet de eerste keer dat de    | goーw?seji   |
| 所有者       | ???? | ?joj???? |
| 最適化       | ??????  | sajitecika |

***