# ∏Ω¬ºB -  ı”Ô

## AGP

AGP is an extension on the older PCI bus. AGP cards act like a PCI card from software perspective, and were introduced to remove various problems that PCI graphics cards had. AGP have a data bus of 32bits just like the standard PCI bus, but the clockspeed can be much higher. The standard AGP bus speed is 66Mhz whereas PCI is 33Mhz, in addition, the AGP bus can operate at 2x, 4x and 8x mode. Where PCI has a theoretically maximum transfer rate of 133Mbyte/sec, AGP in 1x mode starts at 266 Mbyte/sec, rising to a fast 2 Gbyte/sec in 8x mode. 
Furthermore AGP has direct access to the system memory, whereas the PCI bus is shared amonst all cards connected on it. The AGP port has its own dedicated controller as well. 
The AGP port is typically connected via the Northbridge controller in contrast to the PCI bus being connected by the Southbridge controller of the motherboard. The Northbridge controller is directly connected to the CPI and memory system, therefore much faster than the Southbridge controller which has to use a chipset specific and relatively slow bus which can be a speed bottleneck.


## API

Application Programming Interface. The program interface which applications use, supplied by the operating system, to deal with hardware drivers.


## Backend Scaler

See Hardware Overlay.


## Cold start of the video card

The VGA card is initialized at the "cold start" point in time; when it is first powered up. The type and volume of memory is set, the memory speed is set, the memory refresh rate is activated and the chip core speed is set. At the same moment all the needed hardware components on the card are activated. All of these actions are done once, during the cold start.


## Colorkey

A colorkey is a specific color which the video card uses to show the output from the backend scalar, if the output of the video ram buffer is signed. This key value is a color defined in advance. Color keys are generally used by 'hardware overlay' features of the video card.


## CRTC

Cathode Ray Tube Controller. This is a group of registers in a logical unit inside the video card which takes control of the monitor bus at its expense. The place of the electronenstraal (ed: ??) and the speed of scanning the picture are controlled because of this. Synchronisation indicators is the means by which this control takes places.


## DAC

Digital to Analog Converter. This is a graphic card function that takes digital information from video memory and delivers an analog signal. There are three DAC's in use: one for each basic color. The three signals are sent to the color monitor in various intensities, which produces the pattern being displayed on the screen.


## DDC

Display Data Channel. A serial port in the VGA connector with which the properties of the monitor can be asked. Information such as the make and model of the monitor, the maximum and preferred resolution, the minimum and maximum refresh rates, and so forth can be requested.


## DirectX

Microsoft's counterpart for the openGL standard. This standard is not open, and can only be used on Windows systems. Because Microsoft is so powerful, this ensures that most of the graphic card producers support directX. DirectX also supports features other than the mentioned 3D acceleration used for gaming, such as audio.


## DMA

It is possible for several hardware components to access memory directly without passing through the CPU first, which allows the CPU to be occupied with other tasks. Most motherboards have two DMA controllers present for use by ISA cards, but this is no longer necessary for PCI (AGP) devices. Terwijl ISA kaarten daar soms gebruik van maken, is het voor PCI en AGP kaarten niet langer nodig. De PCI (AGP) controller combineert zelfstandig toegangsaanvragen tot aaneengesloten reeksen adressen zodat een soort vervangend DMA systeem ontstaat. Hiervoor is geen programmering nodig: het is in de hardware van de controller 'ingebakken' en gedraagt zich transparant.

### Double Buffering
While one buffer (filled with a picture) is presented on the screen, work on second buffer in the background can occur with another picture. During the vertical refresh period the buffers can be switched and the process repeats, making for very smooth on-screen updates.


## DPMS

Display Power Management System (courtesy: Wikipedia). Power management offer two important advantages: energy saving and lengthening of the life span of the monitor.


## Driver Interface

The combined software interface through the Operating System to the driver.


## Filters

See Interpolers.


## Game Kit

Be, Inc. divided the BeOS API into kits in order to keep the system flexible. The Game Kit is one of these and provides an interface for game programmers to tap into.


## Memory Refresh

Because low-priced dynamic memory is used so heavily its contents must be refreshed on a regular basis. If this update does not occur often enough, the contents will disappear in only a few milliseconds, due to the low capactitor reserve. Memory refresh uses a small percentage of the address bus bandwidth to achieve this.


## Granularity

Stapgrootte. De granulariteit van een coordinaat is het grootste getal waardoor de coordinaat waarden deelbaar zijn zodat de uitkomst een geheel getal blijft. 
In videokaarten wordt de coordinaat gedeeld door de granulariteit als coordinaat-waarde in een register opgeslagen, waardoor deze granulariteit dus ontstaat.


## Harware Overlay

Een 'truc' methode om video goed te kunnen afspelen in computers zonder dat daarvoor enorm veel rekenkracht van de systeem processor nodig is. In het algemeen is het zo dat dingen die veel processorkracht kosten als ze in software zouden worden uitgevoerd, vaak in een stuk speciale hardware worden ondergebracht. 
Naarmate de processoren sneller worden, wordt de behoefte aan 'trucs' kleiner. In de praktijk wordt dit vaak bevorderd doordat minder hardware minder geld kost: Een bekend voorbeeld hiervan zijn de soft- of winmodems.


## Hook

Een hook is een 'standaard entry point' voor software. Een aantal standaard routines wordt in de driver geimplementeerd. De ingang naar zo'n standaard routine is de hook.


## Interface Kit

Be, Inc. divided the BeOS API into kits in order to keep the system flexible. The Interface Kit is one of these and provides an interface for various components of the Operating System such as buttons, sliders, menus, and more.


## Interlaced Mode

The "screen" is divided into two "half" screens before it is shown on the monitor, consisting of "even" and "odd" fields. The "even" field consists of the even numbered horizontal scanlines, and the "odd" field consists of the intermediate scanlines. The original screen is shown in two steps. First the first half segment, and then the second. As both half fields are shown one after another, it is possible that they can be reordered, one after another. Mostly this is the case.
Interlaced mode is used for the television system to save bandwidth. By showing just the half field the human eye sees a normal screen and so blinking is reduced. Older PC VGA cards and PC monitors used this method for the screen too, as this was chearper than the better progressive scan method.


## Interpolers

Also known as 'Filters'. 
A pixel is interpolated when there is no effective information for it, derived from the (weighted) average of the previous and next pixels surrounding it. When this watered effect is present, it prevents sharp edges from forming in the picture.


## ISA

Industry Standard Architecture. The ISA bus was the generally accepted standard before the introduction of the PCI bus. Older motherboard that have PCI systems do have some ISA buses still. The original ISA standard was used in older PC/XT computers and had an 8-bit databus, 64 kilobyte Input/Output address space, and a speed of 8.333 MHZ.
This bus was upgraded to a 16-bit databus when the 286 was introduced. A lot of PCI and even AGP cards are used within the ISA I/O space. the configuration then can be done with the help of the PCI configuration space.


## Little Endian

When a data string consists of more data than can be transmitted at once by the databus (or is bigger than the present memory) the string must be split into smaller parts for transmission one by one. Little Endian first transmits the most important parts. Big Endian does this the other way around.


## OpenGL

Sun's open standard for 3D hardware acceleration. Because this is an open standard, it makes it possible to be used by various platforms.


## OSD

On Screen Display. Modern VGA monitors offer their controls via an OSD, such as the vertical and horizontal refresh rates, pixel speed, timing signals, and more.


## PCI

Pheripheral Component Interconnect. See AGP.


## PCI Configuration Space

PCI (AGP) kaarten hebben alle een aantal configuratie registers welke zich in de configuration space bevinden. 
Deze configuratie registers bepalen waar de eigenlijke resources van de kaart (zoals registers en video geheugen) op de bus zullen verschijnen wanneer ze worden geactiveerd. 
Bij een systeemstart zijn alle kaarten gedeactiveerd: activatie vindt plaats nadat configuratie heeft plaatsgevonden. Activatie vind ook plaats via de configation space. Als een kaart gedeactiveerd is, is alleen de configuration space bereikbaar op een gestandariseerde manier. 
Voor nieuwere ISA kaarten bestaat een vergelijkbaar systeem: de PnP standaard. Oudere ISA kaarten zijn geconfigureerd via jumpers en zijn altijd geactiveerd.


## POST

Power On self test. Deze test wordt door elk moederbord verricht na het inschakelen van het systeem. De eigen componenten worden getest om te zien of het systeem in orde is. Naast de ouderwetse POST meldingen naar de ISA I/O space poort $80 worden tegenwoordig ook meldingen op het scherm gezet. Soms is een audio POST reporter ingebouwd. Een stem laat dan de resultaten van de POST horen.

POST is een handig hulpmiddel om fouten in het moederbord op te sporen, hoewel meestal het geheel vervangen van het moederbord noodzakelijk geworden is door de hoge integratie van de componenten.


## Progressive Scan

Bij een (moderne) computermonitor wordt deze manier van scannen door de electronenstraal toegepast. Het volledige beeld ('frame' genoemd) wordt in keer van links-boven met horizontale lijnen naar rechts-onder afgebeeld in licht op de beeldbuis. Interlaced mode is het 'tegenovergestelde' van progressive scan.


## Refresh Rate

De refreshrate gebruikt in een display mode geeft aan hoe vaak per seconde het beeld op de monitor wordt ververst. 
Hoge refreshrates zijn populair omdat het resulterende beeld minder of in het geheel niet flikkert voor het menselijk oog. 
Het menselijk oog kan tot ongeveer 30 bewegende beelden per seconde zien flikkeren: Stilstaande beelden zoals op een computer gebruikt kan men zien flikkeren tot ongeveer 70 beelden per seconde: 70Hz. Refreshrates boven de 70 Hertz worden dan ook als ergonomisch beschouwd. 
Deze uitspraak geldt niet voor moderde (TFT) LCD schermen: die kunnen met 60Hz prima worden aangestuurd, omdat ze intern het beeld bufferen en continue aan de kijker laten zien. Er is dus een statisch beeld dat perfect stabiel is.


## Resource Mapping

Beside I/O address space, it is also possible to use the registers aas if they are normal memory addresses. In this manner the registers can be used much faster, without the use of specific I/O commands (which normally would belong to the kernel of the OS).


## Single Buffering

See also 'Double Buffering'. Single buffering is applied only if there is not sufficient video card memory for more than one hardware overlay bitmap, as frequently the case in some laptops. When single buffering is the only mode available, the CPU is used to maintain sufficient video throughput.


## Slopspace

Als een co?rdinaat-waarde niet voldoet aan de granulariteit die eraan wordt gesteld, dan wordt de co?rdinaat-waarde naar boven afgerond zodat wel aan de granulariteit wordt voldaan. Het verschil tussen de gevraagde en de verkregen co?rdinaat-waarde wordt apart bewaard ter referentie voor de daadwerkelijk gebruikte 'ruimte'.


## TSR

'Terminate and Stay Ready' program. Dit soort programma's betreft vaak een soort drivers voor DOS. Soms was in het DOS tijdperk bijvoorbeeld de grootte van de BIOS-ROM in videokaarten niet groot genoeg om alle functies te bevatten. Of kwamen later updates uit die functionaliteit toevoegden aan de kaart. Een TSR kon dan uitkomst brengen: voor de gebruiker leek het erop alsof het BIOS uitgebreid was zodra deze file geladen was. Op deze wijze kon bijvoorbeeld VBE 2 support aan VBE 1.2 compatible kaarten worden toegevoegd.


## Unified Driver

Een driver die een hele serie, meestal na elkaar door de fabrikant op de markt gebrachte videokaarten ondersteunt.


## VBE

VESA BIOS Extensions. Naast de standaard in het VGA BIOS aanwezige grafische- en tekstmodes, werden later grafische modes geintroduceerd voor modes die niet in de VGA standaard voorzien waren. Om nu applicatieprogrammeurs op eenvoudige wijze toegang tot deze extra modes te geven, werd een standaard door de VESA organisatie bedacht die dit mogelijk maakte zonder gebruik van specifiek voor een kaart geschreven drivers. 
Deze nieuwe standaard maakt gebruik van de zogenaamde VGA BIOS INT10 (hex) hook en conventies om de modes te activeren. Omdat de grafische kaarten steeds hogere resoluties en kleurdiepten aankonden, moest ook VBE regelmatig worden uitgebreid. Zo ontstonden diverse versies van het VBE: 1.0, 1.2, 2.0 en tegenwoordig 3.0. 
Versie 3.0 is de eerste versie van VBE welke op een bruikbare manier uitvoering van de VBE modes in protected mode toestaat. Hiervoor is een speciale 'protected mode hook' geintroduceerd, welke (een aantal van) dezelfde modes kan instellen als mogelijk is via de oude 'realmode' hook: INT10.


## VCO

Voltage controller oscillator. Dit is in de context van dit document een onderdeel van een PLL. Het betreft een 'frequentiebron' waarvan de frequentie middels een aantal registers (de 'delers' of 'scalers') kan worden ingesteld.


## Vertical Retrace

Bij de opbouw van een beeld op een beeldbuis (CRT) monitor wordt een electronenstraal over de oppervlakte van de buis bewogen met hoge snelheid, waardoor het beeld ontstaat. De beeldbuis wordt 'gescand' van links naar rechts en van boven naar onder met bijna horizontale lijnen. Als de straal rechtsonder in het beeld aankomt, wordt hij uitgeschakeld en weer snel naar de linker-bovenkant van het scherm gestuurd.
Deze terugstuur actie is de vertical retrace. Tijdens de vertical retrace worden geen beeldpunten opgewekt (omdat de straal uit staat) zodat het beeld kan worden verwisseld of gestoord zonder dat dit te zien is. Het beeld is stabiel voor het menselijk oog omdat het beeld steeds snel na elkaar wordt gescand, en de beeldbuis bovendien een nagloeitijd heeft.


## Warm start of the video card

De driver gaat ervan uit dat de kaart al een koude start heeft gemaakt. Alleen de benodigde display mode wordt ingesteld, waaronder de pixelclock van de DAC. De typische koude start initialisatie wordt niet gedaan. 
Eventuele benodigde informatie verzameld door het VGA BIOS tijdens de koude start zoals de grootte van het geheugen, wordt uit kaartregisters gelezen welke tijdens deze koude start zijn geinitialiseerd. Soms kan de benodigde informatie ook (rechtstreeks) uit het VGA BIOS worden gelezen.
In het kaartBIOS kunnen de bij de productie bepaalde gegevens zijn voorgedefinieerd.


## YCbCr411

Zie YCbCr422. In de colorspace YCbCr411 wordt van elke pixel de helderheidsinformatie opgeslagen (4 bekeken pixels), terwijl van de kleurinformatie op de vier pixels wordt opgeslagen (1 uit de 4 bekeken pixels).


## YCbCr422

Een colorspace met compressie welke wordt gebruikt voor de opslag van video. Oorspronkelijk werd de gebruikte compressietechniek ingevoerd voor het kleurentelevisie signal CVBS (Composite Video Baseband System) wegens beperkte bandbreedte van de bestaande zenders en TV toestellen. De kleurinformatie moest op compatible wijze worden verzonden.
In een TV toestel worden de beelden opgebouwd uit drie basiskleuren. rood (R), groen (G) en blauw (B). Rood, groen en blauw gemengd licht levert namelijk de 'kleur' wit op mits in de juiste verhouding gemengd. Wanneer deze basiskleuren in de verhouding gemengd zijn dat ze wit op leveren kunnen alle grijstinten tussen zwart en wit worden opgewekt door variatie van de gezamelijke intensiteit: De zogenaamde helderheidsinformatie luminantie 'Y'. De kleurinformatie die zich in de basiskleuren bevindt wordt chrominantie genoemd: 'C'. 

Wanneer nu basiskleur mist maar wel luminantie aanwezig is kan de missende basiskleur weer uit de aanwezige informatie worden afgeleid, bijvoorbeeld: G = Y - R - B. 

In YCbCr zijn de luminantie Y, de blauwe basiskleur Cb en de rode basiskleur Cr aanwezig. Hier is voor gekozen omdat het menselijk oog gevoeliger is voor helderheidsinformatie dan voor kleuren. In YCbCr422 wordt namelijk voor elke pixel de helderheidsinformatie opgeslagen, (bekeken per 4 pixels) maar de beide basiskleuren alleen om de pixel (2 pixels in de bekeken 4 pixels). In deze standaard wordt de compressie in richting uitgevoerd. Pixels direct na elkaar (van links naar rechts) zijn zo uitgevoerd. De pixels erboven en eronder staan los van de bekeken 'rij'. Er zijn ook colorspaces waar de compressie twee dimensionaal is uitgevoerd: zo ontstaat een hogere compressie. Een backend scaler van een videokaart kan dit soort colorspaces omzetten naar 'standaard' RGB. Hierbij kan bij sommige scalers worden gekozen voor interpolatie van de kleurinformatie voor de 'missende pixels', terwijl andere scalers alleen pixels kunnen dupliceren.