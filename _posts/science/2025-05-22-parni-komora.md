---
layout: post
title: Korigování teploty v parní komoře ve wellness pomocí fuzzy regulatoru
category: Science
---

**Autor:** Tibor Malinský  
**Datum:** Květen 2025

{:toc}
- .

---

## 1 Zadání úlohy

Je parní komora o objemu $$V_{\text{komora}}$$ a rozměrech $$3 \times 3 \times 2,5 \, \text{m}$$ a v ní je nutno udržovat teplotu $$T_{\text{komora}}$$ okolo 50 °C. K dispozici máme generátor páry Steam Generator CD 22.5 Compact (viz odkaz níže), který má výkon $$P_{\text{generator}}$$ 22.5 kW. Během tohoto procesu jsou i nějaké tepelné ztráty do okolí. Pro zjednodušení, parní komora je sestavena z kachliček o tloušťce $$d_{\text{kachlička}}$$ 8 mm, které jsou nalepeny na izolaci XPS. Podrobné informace o materiálech jsou přiloženy níže. Každých 15-20 minut někdo otevře dveře parní komory na 10 sekund (vstup nebo odchod). Uvažujte, že se během této jednorázové diskrétní ztrátě ochladí $$\frac{1}{9}$$ množství plynů v komoře na okolních $$T_{\text{okolí}}$$ 25 °C. Chování parní komory nad 100 °C je možné zanedbat. Zohledněte ale že senzor teploty na stropě má pětisekundové zpoždění mezi aktuální teplotou a naměřenou teplotou. *Odkaz na parní generátor:* [https://www.dino-dampf.com/en/electric-steam-generator-type-cd22-5k-e-h-10.html](https://www.dino-dampf.com/en/electric-steam-generator-type-cd22-5k-e-h-10.html).

**Hodnoty veličin a vlasntostí materiálů**:

parní komora:

- $$V_{\text{komora}}$$ = 22,5 m$$^3$$ (3 $$\times$$ 3 $$\times$$ 2,5m)
- $$T_{\text{komora}}$$ = 50 °C ($$T_{\text{požadovaná}}$$)
- rozptyl = 45-55 °C je ok

generátor:

- $$P_{\text{generator}}$$ = 22,5 kW
- $$\dot{m}$$ = 30 kg/h

pára:

- $$p_{\text{nasycené páry vody při 50 °C}}$$ = 12327,51 Pa
- $$c_{\text{vodní páry při 50 °C}}$$ = 1878,55 J/kg/K
- $$M_{\text{voda}}$$ = 18 g/mol

vzduch:

- $$M_{\text{vzduch}}$$ = 29 g/mol
- $$c_{\text{vzduch při 50 °C}}$$ = 998,71 J/kg/K

keramické kachličky v parní komoře:

- $$c_{\text{kachličky}}$$ = 850 J/kg/K
- $$\rho_{\text{kachličky}}$$ = 2500 kg/m$$^3$$
- $$d_{\text{kachlička}}$$ = 8 mm
- $$\lambda_{\text{kachlička}}$$ = 1,1 W/m/K
- $$R_{si}$$ = 0,07 m$$^2$$ K/W
- $$R_{se}$$ = 0,13 m$$^2$$ K/W

izolant (extrudovaný polystyren, XPS):

- $$d_{\text{XPS}}$$ = 0,1 m
- $$\lambda_{\text{XPS}}$$ = 0,035 W/m/K

ostatní:

- $$T_{\text{start}}$$ = 25 °C
- $$T_{\text{okolí}}$$ = 25 °C
- $$p_{\text{celk}}$$ = 101500 Pa
- $$x \in [0;1]$$ (otevření a zavření ventilu)
- celkové tepelné ztráty dodatečně vynásobit faktorem 10

---

## 2 Výpočty

Nejprve je nutné si udělat celkovou bilanční rovnici. V této úloze budeme řešit akumulaci tepla.

$$\dot{H}_{\text{vstup}}\text{d}t + \dot{Q}\text{d}t = \dot{H}_{\text{výstup}}\text{d}t + \text{d}H$$

Následně vydělíme diferenciálem.

$$\dot{H}_{\text{vstup}} + \dot{Q} = \dot{H}_{\text{výstup}} + \frac{\text{d}H}{\text{d}t}$$

Vodní pára je generována generátorem o výkonu $$P_{\text{generator}}$$ v uzavřeném prostoru, proto lze vstupní entalpii nahradit pouze výkonem. Ten je nutné ještě vynásobit $$x$$, což udává, jak moc je ventil uzavřen nebo otevřen. Výstupní proudy jsou tepelné ztráty přes stěny a při otevření dveří, které je ale v čase proměnlivé.

$$xP_{\text{generator}} = x\dot{Q}_{\text{generator}} = \dot{Q}_{\text{ztráty}} + \dot{H}_{\text{dveře}}(t)$$

Vyjádříme-li akumulaci, získáme následující rovnici.

$$\frac{\text{d}H}{\text{d}t} = xP_{\text{generator}} - \dot{Q}_{\text{ztráty}} + \dot{H}_{\text{dveře}}(t)$$

### 2.1 Vstup generátoru

Díky generátoru zavedeného v podlaze parní komory je představován jediným členem. Výkon generátoru je 22,5 kW

$$x\dot{Q}_{\text{generator}} = xP_{\text{generator}} = 22500x$$

### 2.2 Výstup dveřmi

Při jednorázovém otevření dveří uvažujeme, že se vymění nějaké množství uvnitř komory o teplotě 50 °C s plynem o okolní teplotě 25 °C. K tomuto problému jsem přistoupil tak, že jsem si spočítal hmotnost směsi vzduchu a vodní páry při 50 °C s tím, že uvažuji stoprocentní vlhkost. Po zjištění zastoupení jednotlivých složek (vzduch a vodní pára) jsem spočetl jejich průměrnou molární hmotnost a následně jejich hustotu za použití rovnice ideálního plynu. Hmotnost plynu v komoře jsem vynásobil rozumným faktorem jednou třetinou.

$$\begin{aligned} p_{\text{vzduch}} &= p_{\text{celk}} - p_{\text{pára při 50°C}} = 101500 - 12327,51 = 89172,49 \, \text{Pa} \\ y_{\text{pára}} &= \frac{p_{\text{pára}}}{p_{\text{celk}}} = \frac{12327,51}{101500} = 0,1215 \\ y_{\text{vzduch}} &= 1 - y_{\text{pára}} = 1 - 0,1215 = 0,8785 \\ M_{\text{směs}} &= M_{\text{pára}} y_{\text{pára}} + M_{\text{vzduch}} y_{\text{vzduch}} \\ M_{\text{směs}} &= 18 \cdot 0,1215 + 29 \cdot 0,8785 \approx 27,9 \,\text{g/mol}\\ \rho_{\text{směs}} &= \frac{m}{V} = \frac{p_{\text{celk}} M_{\text{směs}}}{R T_{\text{50°C}}} = \frac{101500 \, \text{Pa} \cdot (27,9 \times 10^{-3} \, \text{kg/mol})}{8,314 \, \text{J/(mol K)} \cdot (50 + 273,15) \, \text{K}} = 1,054 \, \text{kg/m}^3 \\ m_{\text{únik}} &= \zeta \cdot V_{\text{komora}} \cdot \rho_{\text{směs}} \\ &\text{kde } \zeta \text{ je konstanta, zvolená jako } \frac{1}{9} \\ m_{\text{únik}} &= \frac{1}{9} \cdot 22,5 \, \text{m}^3 \cdot 1,054 \,\text{kg/m}^3 = 2,635 \, \text{kg} \end{aligned}$$

Nyní lze spočítat uniklou entalpii dveřmi.

$$\begin{aligned} \Delta H_{\text{dveře}} &= m_{\text{únik}} \cdot (y_{\text{pára}} c_{\text{pára, 50°C}} + y_{\text{vzduch}} c_{\text{vzduch, 50°C}}) \cdot (T_{\text{komora}} - T_{\text{okolí}}) \\ \Delta H_{\text{dveře}} &= 2,635 \, \text{kg} \cdot (0,1215 \cdot 1878,55 + 0,8785 \cdot 998,71 \cdot (50 - 25) = 72832,10 \text{ J}\\ \Delta \dot{H}_{\text{dveře}} &= \frac{H_{\text{dveře}}}{t} = \frac{72832,10}{10} = 7283,21 \ \text{J/s} \end{aligned}$$

### 2.3 Tepelné ztráty zdmi

Tepelné ztráty zdmi jsou spočítané přes materiálové vlastnosti použitých materiálů ke konstrukci parní komory. Níže je spočítána propustnost tepla a je i zahrnut plošný odpor.

$$\begin{aligned} R &= R_{\text{si}} + \frac{d_{\text{kachlička}}}{\lambda_{\text{kachlička}}} + \frac{d_{\text{XPS}}}{\lambda_{\text{XPS}}} + R_{\text{se}} \\ R &= 0,07 + \frac{0,08}{1,1}+\frac{0,1}{0,035} + 0,13 = 3,06 \text{ m}^2\text{/K/W} \\ U &= \frac{1}{R} = 0,3263 \text{ W/m}^2\text{/K } \\ \dot{Q}_{\text{ztráta}} &= U A_{\text{celk}} (T_{\text{komora}}-T_{\text{okolí}}) \\ \dot{Q}_{\text{ztráta}} &= 0,3263 \cdot 48 \cdot (T_{\text{komora}} - 25 ) \end{aligned}$$

Nakonec byly tepelné ztrát ještě vynásobeny faktorem 10, což poskytuje realističtější pohled na tepelné ztráty kvůli netěstnostem dveří, ztrátám tepla s kondenzovanou vodou mizející odtokem a jiné ztráty.

$$\begin{aligned} \dot{Q}_{\text{ztráta}} &= 0,3263 \cdot 48 \cdot (T_{\text{komora}} - 25 ) \cdot 10 \\ \dot{Q}_{\text{ztráta}} &= 156,642 \cdot (T_{\text{komora}} - 25 ) \end{aligned}$$

### 2.4 Akumulace

Poslední krok je výpočet akumulace. Pro to je spočtena celková tepelná kapacita systému, což zahrnuje kachličky, vodní páru a vzduch.

$$\begin{aligned} \frac{\text{d}H}{\text{d}t} &= \sum (m_i c_i) \frac{\text{d}T}{\text{d}t} \\ \frac{\text{d}H}{\text{d}t} &= (960 \cdot 850 + 1,8591\cdot1878,55 + 21.67 \cdot 998,71) \frac{\text{d}T}{\text{d}t} \\ \frac{\text{d}H}{\text{d}t} &= 846059\frac{\text{d}T}{\text{d}t} \end{aligned}$$

### 2.5 Výsledná rovnice

Nyní můžeme spojit všechno dohromady, čímž získáme celkovou rovnici.

$$\begin{aligned} 22500x &= 0,3263 \cdot 48 \cdot (T_{\text{komora}}-25)\cdot10 + 846059 \frac{\text{d}T}{\text{d}t} + \dot{H}_{\text{dveře}}(t) \\ \frac{\text{d}T}{\text{d}t} &= \frac{22500x}{C_{\text{celk}}} - \frac{156,621 \cdot (T_{\text{komora}}-25)}{C_{\text{celk}}} - \frac{\dot{H}_{\text{dveře}}(t)}{C_{\text{celk}}} \end{aligned}$$

---

## 3 Řešení v Simulinku

Výsledná rovnice byla namodelována v Simulinku. Veličinu, kterou jsem sledoval, byla teplota v komoře. Ta byla spočtena pomocí integrace zjištěné derivace a sledovaná pomocí bloků Scope. Veličinu, kterou budu regulovat, je ventil od generátoru vodní páry $$x$$. Podle očekávání, bez žádného regulátoru rostla teplota až do 168 °C, kde se ustálila.

### 3.1 Fuzzy regulátor

Než jsem začal implementovat fuzzy regulátor, ještě jsem přidal blok Time Delay mezi zjišťovanou teplotou a výpočtem, abych lépe nasimuloval reálné prostředí. K nepravidelnému otevírání dveří jsem dodal i pulse generátor, který mi každých 1000 sekund (skoro 17 minut, doporučovaný pobyt v parní komoře je 15 až 20 minut) odebíral na 10 sekund entalpii. To se projevilo snížením teploty.

Prvním vstupem pro fuzzy regulator byla odchylka aktuální teploty v komoře od požadované teploty ($$T_{\text{požadovaná}}-T_{\text{komora}}$$). V mém původním návrhu jsem chtěl zohlednit asymetrii situace, tedy že chci být více přísný na přehřátí, poněvadž se teplo obecně hůře odebírá než se přidává. Proto jsem si udělal vstupní fuzzy množiny nepravidelné, abych situaci lépe ohlídal. Tento návrh jsem nakonec opustil a udělal jsem jej normalizovaný [-1,1] pro 5 intervalů. Odchylky byly klasifikovány jako málo, skoro super, super, moc teplo, příliš teplo.

Druhou vstupní množinou byla derivace teploty, abych lépe podchytil, jestli se teplota mění prudce, pomaleji nebo vůbec. I tento vstup byl normalizovaný na intervalu [-1;1].

Nakonec výstupní množinou byla derivace ventilu, který měl buďto hodně zavírat, zavírat, nic nedělat, otevírat a hodně otevírat. Výstup z fuzzy regulátoru procházel před integrátor s limitem, který poskytoval hodnoty [0;1], neboť ventil více než 1, úplně otevřený, být nemůže. Vzhledem k již zmíněné citlivosti na přehřívání jsem plánoval použít jinou defuzzikační metodu, MOM, pro přísnější a ostřejší regulování. Proto i výstupní množiny byl trojúhelníkové. Nakonec jsem přešel k COG a množiny udělal obdélníkové a normalizované [-1;1].

Sestaviv vstupní a výstupní porty jsem se jal zpracovat pravidla. Pravidel bylo 25 pro každou situaci a právě jimi jsem řídil citlivost situace. Ukázalo se, že je to výrazně lepší a příjemnější řešení, než asymetrické, nepravidelné a nestandardní fuzzy množiny. Pravidla pro dané situace jsou shrnuty v následující tabulce.

|  | **rychle klesá** | **klesá** | **nic** | **roste** | **rychle roste** |
| --- | --- | --- | --- | --- | --- |
| **příliš teplo** | nic nedělat | nic nedělat | zavírajzavírat | hodně zavírat | hodně zavírat |
| **moc teplo** | otevírat | nic nedělat | zavírat | hodně zavírat | hodně zavírat |
| **super** | hodně otevírat | otevírat | nic nedělat | zavírat | hodně zavírat |
| **skoro super** | hodně otevírat | hodně otevírat | otevírat | nic nedělat | zavírat |
| **málo** | hodně otevírat | hodně otevírat | otevírat | nic nedělat | nic nedělat |

Posledním krokem pro zajištění, že regulátor bude fungovat podle potřeb a že bude hlídat požadovanou teplotu, byly gainy před vstupem do fuzzy regulátoru a hned za výstupem. Vstup pro odchylku pro požadovanou teplotu procházel přes gain 1/10, neboť očekávané odchylky se pohybovaly okolo 10 °C. Derivace byly nepatrně zmenšeny faktorem 0,9. Nižší odchylka např. 1/5 regulaci teploty zcela rozházela a vyšší se neukázaly být nijak lepší. 2 pro integraci zajistilo skvělé výsledky. Menší gain způsobil akumulaci chyby a vyšší gain neposkytl dobré výsledky.

### 3.2 Vizualizace

Tento problém i s řešením byl vizualizován v prostředí 3D Simulink. V této animaci se mění barva komory podle toho, jaká je zrovna teplota. Zeleně se rozsvítí, pokud je teplota 50 °C. Pokud je teplota o trochu níž, skoro super, tak se zbarví tyrkysově, pokud je teplota ještě nižší, tak do modra a fialova. Je-li ale teplota vyšší než požadavek, prochází přes oranžovou, červenou a rudě červenou barvu.

---

## 4 Závěr

Podařilo se parní komoru dobře a realisticky nasimulovat. Komoře trvá necelou hodinu, než se ustálí na teplotě 50 °C, což je reálný odhad toho, jak se ve wellness rozehřívá parní komora. Okolo 50 °C se pohybuje už dříve, ale s větší oscilací. Teplota osciluje opravdu jen lehce, ani ne o jeden stupeň nad a pod požadovanou teplotou. Fuzzy regulátor zvládá dobře regulovat teplotu i s pětisekundovým zpožděním v záznamu teploty. Tato teplota se ustálí a udrží se několik hodin, což je opět v souladu s tím, kolik hodin ve wellness bývá běžně parní komora v provozu. Při pohledu na ventil generátoru lze vidět, že se prudce zapíná a prudce vypíná. To velmi dobře simuluje reálné prostředí, kdy ve wellness je pára vstřikována do komory diskrétně a nikoliv konstantně. Výsledná vizualizace ukazuje změny barvy podle teploty s tím, že svítí prakticky pořád zeleně a pouze pokud teplota není v rozmezí 49,8 °C až 50,2 °C, tak změní barvu.

___

## 5 Příloha

[![Schéma modelu parní komory](/assets/img/2025-05-22-parni_komora-layout.png)](/assets/img/2025-05-22-parni_komora-layout.png)

