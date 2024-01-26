## Potrzebny sprzęt

### Moduł ESP
Moduły [Espressif](https://www.espressif.com/en/products/socs) ESP8266, ESP8285, ESP32, ESP32-S2, ESP32-S3, ESP32-C3 oraz oparte na nich urządzenia mogą być sflashowane GUI Genericiem. Pojęcia takie jak _ESP, moduł czy czip_ odnoszą się do każdego z nich.

### Konwerter USB-UART
Do programowania modułów niezbędny jest programator USB. Jednym z najważniejszych elementów przy programowaniu i normalnym działaniu jest [zasilanie urządzenia](https://www.letscontrolit.com/wiki/index.php?title=Power). Pamiętaj, aby upewnić się, że urządzenie jest zasilane poprawnym napięciem o odpowiednio dużej mocy. Inaczej możesz mieć problem z zaprogramowaniem ESP.

* Zaleca się używanie programatora CP2102. Działa on z większością urządzeń (może wymagać zewnętrznego źródła zasilania).
* Innym popularnym programatorem jest CH340.
* Alternatywnie do programowania możesz wykorzystać NodeMCU (i jemu podobne). W tym celu wyłącz ESP zwierając piny GND oraz EN(RST) i podłącz się do innego ESP822xx (połączenie należy wykonać bez krzyżowania, tj. RX-RX TX-TX).

_Pamiętaj, aby przed użyciem konwertera zainstalować do niego sterowniki._
***
UWAGA!
Niektóre konwertery mają zworkę umożliwiającą wybranie 3,3V lub 5V dla pinów od danych (zmiana położenia zworki nie zmienia napięcia na pinie VCC). Zworkę należy ustawić w pozycji 3,3V. Przy pierwszym kontakcie z programatorem warto się upewnić czy piny danych (RX i TX) oraz VCC są w trybie 3,3V. 

***

Przy wybieraniu programatora warto zwrócić uwagę na jego piny. Jeśli wybierzesz taki z wyjściami +3,3V oraz GND, to w większości przypadków nie będziesz potrzebować zewnętrznego źródła zasilania. Pamiętaj, że ESP potrzebuje co najmniej 150mA, więc niektóre stabilizatory napięcia obecne na konwerterach mogą nie dać rady (np. złoty CH340G nie potrafi) zasilić modułu. Sam z CP2102 nigdy nie miałem problemu, jeśli chodzi o zasilanie.

Przy korzystaniu z zewnętrznego źródła zasilania 3,3V pamiętaj o uwspólnieniu mas (zewnętrznego źródła zasilania/ESP i konwertera).

### Przewody
Warto wyposażyć się w przewody do płytki stykowej (ang. jumper wires/DuPont wires). Usprawniają pracę przy łączeniu urządzeń - nie wymagają lutowania.

### Goldpiny
Goldpiny występują zarówno w wersji męskiej, jak i żeńskiej. Warto je sobie dobrać do przewodów (niektóre moduły, np. ESP-01, są sprzedawane z wlutowanymi pinami męskimi 2,54mm).

### Narzędzia lutownicze
Niektóre urządzenia (w szczególności gotowy sprzęt) nie mogą być zaprogramowane za pomocą przewodów do płytek stykowych ze względu na swoją budowę. W takim wypadku trzeba się przylutować do płytki. Warto pamiętać o topniku - mocno ułatwia lutowanie.

W Internecie dostępne są projekty podstawek do programowania niektórych gotowych urządzeń (np. do Sonoffa Mini). Stanowią one dobrą alternatywę do lutowania.

### Komputer
Do wgrania GUI Generica do modułu potrzebny jest komputer z portem USB.

### Telefon (urządzenie Wi-Fi)
Do konfiguracji nowowgranego GUI Generica potrzebne jest urządzenie z wyświetlaczem i dostępem do Wi-Fi (np. telefon komórkowy lub laptop).

## Potrzebne oprogramowanie
**Przeglądarka internetowa wspierająca Web Serial** (np. Chrome czy MS Edge) - za pomocą Buildera możesz nie tylko wygenerować soft, ale również wgrać go na swoje urządzenie. _[wgrywanie GUI Generica poprzez przeglądarkę z szablonów płytek wykorzystujących GPIO3 nie jest możliwe]_

**[NodeMCU PyFlasher](https://github.com/marcelstoer/nodemcu-pyflasher)** - aplikacja z przyjaznym interfejsem graficznym. Jest prostsza w obsłudze niż wymienione poniżej _Flash Download Tools_.

**[Flash Download Tools](https://www.espressif.com/en/support/download/other-toolsv)** - aplikacja udostępniana przez producenta modułów ESP. W odróżnieniu od NodeMCU PyFlashera umożliwia wpisywanie adresów pamięci (z tego względu nie jest polecana początkującym użytkownikom).

**[esptool](https://github.com/espressif/esptool)** - oficjalne narzędzie Espressifa do flashowania ESP82XX i ESP32. Nie ma interfejsu graficznego (dodatkowo wymaga instalacji Pythona).

***
Jeśli chcesz, możesz samodzielnie zmodyfikować kod i skompilować go w odpowiedniej aplikacji. **[dodać link do rozdziału o samodzielnej kompilacji]**
***

## Przygotowanie sprzętu
Aby rozpocząć procedurę programowania musimy zapewnić połączenie ESP z komputerem. W tym celu podłączamy piny TX i RX programatora do RX i TX ESP oraz 3,3V do 3,3V, a GND do GND.

W przypadku gotowych urządzeń potrzebne do programowania piny mogę być wyprowadzone w postaci goldpinów lub pól lutowniczych. Podłączymy się wtedy do nich za pomocą przewodów do płytki stykowej lub poprzez przylutowanie się do nich. Może się zdarzyć, że piny nie są wyprowadzone. Należy wtedy przylutować się bezpośrednio do modułu (wymaga to sprzętu i umiejętności; odwołując się jednak do poprzedniej części tekstu - topnik potrafi czynić cuda!).

NIGDY NIE PROGRAMUJ URZĄDZEŃ PODŁĄCZONYCH POD NAPIĘCIE SIECIOWE. 230V ZAŁĄCZAMY DOPIERO PO ZAMKNIECIU OBUDOWY!
Nieuwaga może skutkować utratą życia i uszkodzeniem sprzętu, dlatego przed podłączeniem modułu upewnij się, że nie ma on kontaktu z napięciem sieciowym.

### Połączenie konwertera
Każde urządzenie ma inne opisy pinów. Jeśli nie są dla Ciebie czytelne, poszukaj poradników w sieci (wiele urządzeń zostało udokumentowanych na stronie Tasmoty). Przy flashowaniu musimy rozpoznać 5 pinów. Podłączmy je zgodnie z tabelką:
|**Moduł ESP**| **Konwerter USB**|
| ----------- | ----------- |
| 3,3V        | 3,3V / VCC  |
| TX          | RX          | 
| RX          | TX          |
| GND         | GND         | 

**Pamiętaj, że do flashowania musimy dokonać tzw. _crossa_, czyli połączyć TX z RX oraz RX z TX.**

### Tryb flashowania
Żeby dało się wgrać soft do ESP, musimy wprowadzić je w tryb programowania poprzez zwarcie pinu GPIO 0 z masą podczas podłączania zasilania.

Gotowe urządzenia mogą mieć przycisk podłączony do GPIO 0 i masy, co ułatwia wprowadzanie ich w tryb flashowania. Niektóre mogą wymagać zwarcia odpowiednich wyprowadzeń, a jeszcze inne - bezpośrednio pinów ESP.

Wśród gotowych urządzeń zdarzają się wyjątki, które mają trochę inną procedurę, niż większość modułów. W takim wypadku warto poszukać instrukcji w Internecie.

Wprowadzanie w tryb flashowania krok-po-kroku:
1. Odłącz konwerter USB od zasilania
2. Zewrzyj GPIO 0 z masą (przewodem lub poprzez naciskanie odpowiedniego przycisku)
3. Podłącz konwerter USB do komputera
4. Po upływie kilku sekund odłącz GPIO 0 od masy (odepnij kabel lub zwolnij przycisk). W przypadku urządzeń, które nie mają wyprowadzonego GPIO 0, może być łatwiej pozostawić zworkę na cały proces flashowania (czyszczenie i wgrywanie). Nie wygeneruje to żadnego problemu. Po sflashoaniu modułu usuń zworkę. aby ESP - po otrzymaniu zasilania - wstało w trybie normalnej pracy.

Jeśli postępowałeś zgodnie z opisaną procedurą, powinieneś być w trybie programowania. Jeśli nie możesz wgrać firmware'u, odepnij moduł i powtórz powyżej opisaną procedurę.
