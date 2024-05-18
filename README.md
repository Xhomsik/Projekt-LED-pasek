# ROČNÍKOVÝ PROJEKT -- LED PASEK -- Tomáš Jarkovský

## ZADÁNÍ:
#### Navrhnul jsem si zapojení led pasku, který bude měnit barvy, intenzitu světla a také bude možnost si nastavit styl svícení (RGB, fade, postupné zapínání barev).



## POSTUP:
#### Koupil jsem si LED pasek v TEMU s kolegou Faltou. Take jsem si koupil m5stack pres který to programuji (Pres AI si nechám udělat základ, a pak si to nějak upravuji podle sebe jelikož nejsem úplně dobrý programátor). Zapojil jsem si LED pásek do desky, a udělal program viz. přílohy. 

## MOMENTÁLNÍ STAV:
#### Podařilo se mi zprovoznit LED pasek po pár týdnech, takže ted to nějak funguje. Pomocí tlačítek se vypíná/zapíná, mám tam předefinované barvy, měním intenzitu světla a také styl svícení.
V praxi to je skoro nepoužitelné v tomto stavu, jelikož to je zapojené na té desce, ale aspoň jsem se zatím něco naučil, jak to funguje...
![image](https://github.com/Xhomsik/Projekt-LED-pasek/assets/154555027/ece31aa5-28d1-47ee-b6b5-9ee1c31fcc60)
![image](https://github.com/Xhomsik/Projekt-LED-pasek/assets/154555027/b13d77d3-050d-411d-95f6-426535e62fcd)


## ZDROJE: 
#### Používám AI, a prošel jsem si pár projektů na tento styl. Také spolupracuji s Adamem Faltou který dělá obdobný projekt.

## program k předchozímu projektu


#include <Adafruit_NeoPixel.h>
#include <Bounce2.h>

#define LED_PIN 25
#define BUTTON_PIN 23
#define BRIGHTNESS_BUTTON_PIN 33
#define NUM_LEDS 20

Adafruit_NeoPixel strip = Adafruit_NeoPixel(NUM_LEDS, LED_PIN, NEO_GRB + NEO_KHZ800);

bool stripOn = false;
int brightnessLevel = 5; // Počáteční jas 5%

Bounce debouncer = Bounce(); // Objekt pro odbavení tlačítek

void setup() {
  strip.begin();
  strip.show(); // Inicializace LED pásku

  pinMode(BUTTON_PIN, INPUT_PULLUP);
  pinMode(BRIGHTNESS_BUTTON_PIN, INPUT_PULLUP);

  debouncer.attach(BUTTON_PIN);
  debouncer.interval(50); // Interval pro odbavení tlačítka
}

void loop() {
  // Odbavení tlačítka pro zapnutí/vypnutí LED pásu
  debouncer.update();
  if (debouncer.fell()) {
    stripOn = !stripOn;
    delay(250); // Zpoždění pro stabilitu
  }

  // Odbavení tlačítka pro ovládání jasu a různé styly svícení
  debouncer.attach(BRIGHTNESS_BUTTON_PIN);
  debouncer.interval(50); // Interval pro odbavení tlačítka
  debouncer.update();
  if (debouncer.fell()) {
    brightnessLevel += 20; // Zvýší jas o 20%
    if (brightnessLevel > 100) {
      brightnessLevel = 5; // Vrátí jas na 5%, pokud přesáhne 100%
    }
// Různé styly svícení na základě aktuálního jasu
    if (brightnessLevel == 5) {
      setColor(strip.Color(255, 0, 0)); // Červená barva při 5%
    } else if (brightnessLevel == 20) {
      // Blikání náhodnými barvami při 20%
      randomBlink(5, 100);
    } else if (brightnessLevel == 40) {
      // Přehazování barev při 40%
      colorShift(50);
    } else if (brightnessLevel == 60) {
      // Blikání bílou barvou při 60%
      blinkColor(strip.Color(255, 255, 255), 500, 5);
    }
  // Ovládání LED pásu podle stavu
  if (stripOn) {
    strip.setBrightness(map(brightnessLevel, 0, 100, 0, 255)); // Nastavení jasu
  } else {
    setColor(strip.Color(0, 0, 0)); // Vypnutí LED pásu
  }

  strip.show();
}

// Funkce pro nastavení barvy LED pásu
void setColor(uint32_t color) {
  for (int i = 0; i < strip.numPixels(); i++) {
    strip.setPixelColor(i, color);
  }
}

// Funkce pro blikání náhodnými barvami
void randomBlink(int numBlinks, int blinkDelay) {
  for (int i = 0; i < numBlinks; i++) {
    setColor(randomColor());
    delay(blinkDelay);
    setColor(strip.Color(0, 0, 0));
    delay(blinkDelay);
  }
}

// Funkce pro přehazování barev
void colorShift(int shiftDelay) {
  for (int i = 0; i < 256; i++) {
    setColorWheel((i + 85) % 256);
    delay(shiftDelay);
  }
}

// Funkce pro blikání konkrétní barvou
void blinkColor(uint32_t color, int blinkDelay, int numBlinks) {
  for (int i = 0; i < numBlinks; i++) {
    setColor(color);
    delay(blinkDelay);
    setColor(strip.Color(0, 0, 0));
    delay(blinkDelay);
  }
}

// Funkce pro generování náhodné barvy
uint32_t randomColor() {
  return strip.Color(random(256), random(256), random(256));
}
// Funkce pro nastavení barvy pomocí barevného kola
void setColorWheel(byte WheelPos) {
  WheelPos = 255 - WheelPos;
  if (WheelPos < 85) {
    setColor(strip.Color(255 - WheelPos * 3, 0, WheelPos * 3));
  } else if (WheelPos < 170) {
    WheelPos -= 85;
    setColor(strip.Color(0, WheelPos * 3, 255 - WheelPos * 3));
  } else {
    WheelPos -= 170;
    setColor(strip.Color(WheelPos * 3, 255 - WheelPos * 3, 0));
  }
}

Tento program mi ze začátku roku fungoval, když jsem zkoušel tedka dodělat další věci, tak mi to nešlo zprovoznit. Zkoušel jsem se poradit s ostatními spolužáky a nikdo moc nevěděl co stím.
Takže jsem zkusil udělat neco jiného, hledal jsem další způsob jak ten LED pásek ovládat a bylo by to nějak použitelné a šlo by to dát do auta. Našel jsem tento návod: https://www.youtube.com/watch?v=GYxctjukehY&t=1s
Musel jsem si od spolužáka zapůjčit ESP32 jelikož s mým M5stackem to nešlo rozchodit, prostě to asi nepodporuje tak to nefungovalo.
Takže jsem si stáhl program který jsem nahrál do ESP32, musel jsem nakonfigurovat připojení které muselo být v stejné sítí na ESP a zařízení na kterém to chci ovládát. Dále jsem musel připojit k ESP LED pásek, 
přidal jsem tam rezistory a připojil ho. Následně pak už jsem musel zprovoznit komunikaci LED pásku s ESP. To nám trvalo docela dlouho, jelikož jsme se snažili připojit ESP na hotspot přes iPhone. To vůbec
nefungovalo a rozhodilo nám to celý nastavení takže jsme to museli dělat celé asi 5x znovu. Když se nám to povedlo připojit, tak jsme to nechali na WIFI a šli to vyzkoušet jestli vše funguje jak má..

![golfled](https://github.com/Xhomsik/Projekt-LED-pasek/assets/154555027/9c877870-8b08-48c2-b18c-0e6d36d94bba)

## Schémá zapojení
![image](https://github.com/Xhomsik/Projekt-LED-pasek/assets/154555027/08f4b2cd-3ca7-4936-a374-c75cb787ab30)


Zde je krátké video jak to funguje, delší ani nešlo tady nahrát, tak aspoň krátká ukázka jak by to mělo fungovat. Samozřejmě zapojení v autě není dobré, ale pro demonstraci to takle úplně stačí.
https://github.com/Xhomsik/Projekt-LED-pasek/assets/154555027/67cb79a0-1fe7-49ce-bd4b-67401b0cf61e





























































































































