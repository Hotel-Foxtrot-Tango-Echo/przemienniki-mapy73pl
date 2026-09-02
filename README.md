# Info

Baza przemienników rozwijana jest na podstawie wysyłanych zgłoszeń radioamatorów przez strone [Mapy73.pl](https://mapy73.pl) (opcja zgłość błąd na stronie każdego przemiennika). 

Baza w formie plików YAML - czytelnych dla ludzi z których bardzo prosto generuje sie JSON (rownież strutura daych zoptymaizowana by zająć mniej miejsca i szybiej pobrać dane przemiennika - cenne przy wolnych sieciach gdzie liczy się czas)

## Strutura bazy
Przemienniki w plikach YAML pogrupowane wg znaku wywoławczego kraju, np. `db/de/DB0AAT.yaml`, wyjątek: `db/!dziedzictwo-narodowe/` zawiera polskie przemienniki

## Opis struktury YAML przemiennika

Najprościej chyba uczyć się na przykładach
* tak wygląda plik wejsciowy: [SR5KPN.yaml](https://github.com/Hotel-Foxtrot-Tango-Echo/przemienniki-mapy73pl/blob/main/db/!dziedzictwo-narodowe/SR5KPN.yaml)
* a tak wygląda finalny efket: [https://mapy73.pl/repeater/sr5kpn/](https://mapy73.pl/repeater/sr5kpn/)

A tutaj troszkę wiecej inforamcji dla ciekawych:

```yaml
x:                          # Częstotliwości - pogrupowane wg pasm
  {pasmo}:                  # 2m | 70cm | 4m | 6m | 10m | 23cm
    - rx:                   # [Początek struktury RepeaterData]
        f: 145.0625         # Częstotliwość odbioru
        t:                  # Tony dostępu (opcjonalne)
          - c: "94.8"       # a=nośna b=ton 1750Hz c=CTCSS d=DCS e=DTMF f=CC(DMR)
      tx:
        f: 145.6625         # Częstotliwość nadawania
        t: [...]            # Tony TX (opcjonalne)
      h: "56896088623"      # Referencja do lokalizacji w sekcji h
      t: a                  # Typ: a=FM b=D-Star c=ATV d=Echolink e=DMR
                            #       f=APCO-25 g=C4FM h=TETRA i=FM-Link j=FM-Poland
      s: "4"                # Status: 1=planowany 2=budowany 3=testowy
                            #         4=pracujący 5=nieznany 6=wyłączony
      p: 15                 # Moc [W]
      a: 4x5/8 collinear    # Nazwa anteny
      i: 9.5                # Zysk anteny [dBi]
      d: "..."              # Opis
      u: [url, ...]         # Linki
      j0: [grupa]           # tylko dla FM-Poland - grupa domyślna (opcjonalne)
      j1: [grupa, ...]      # tylko dla FM-Poland - monitorowane grupy (opcjonalne)
      e1: [grupa, ...]      # tylko dla DMR - grupy rozmowne na Time Slocie 1 (opcjonalne)
      e2: [grupa, ...]      # tylko dla DMR - grupy rozmowne na Time Slocie 2 (opcjonalne)

h:                          # Lokalizacje (hash map)
  "56896088623":            # Unikalny klucz, zobacz  Dodatek
    a: 56.88023             # Latitude
    o: 16.656235            # Longitude
    e: 5                    # Wysokość n.p.m. [m]
    g: 20                   # Wysokość anteny nad gruntem [m]
    p: Borgholm             # Nazwa miejsca
    x:                      # Które RepeaterData są w tej lokalizacji
      - 2m: 0               # {pasmo: index w tablicy x[pasmo]}
      - 70cm: 0

r:                          # Cross-band powiązania (opcjonalne)
  - - 6m: 0                 # Grupa powiązanych wpisów [{pasmo: index}, ...]
                            # Pierwszy obiekt to ZAWSZE wejście przemiennika (RX),
                            # kolejne to wyjścia (TX).
    - 70cm: 0

o: [SQ5ALQ, ...]            # Opiekuni przemiennika (znaki wywoławcze)
```

**Uwaga:** jeden przemiennik może mieć wiele lokalizacji (np. [SK7RN.R](https://mapy73.pl/repeater/sk7rn.r/) ma nadajniki w Borgholm i Böda ~45km od siebie). Każdy wpis w `x[pasmo]` wskazuje swoją lokalizację przez `h`. Sekcja `h[hash].x` mapuje odwrotnie - które indeksy pasm należą do tej lokalizacji.


### Dodatek

```javascript
  /**
   * Generuje unikalny klucz tekstowy na podstawie współrzędnych geograficznych.
   * Klucz ten odpowiada sekcji `h` w plikach YAML/JSON przemienników.
   * 
   * @param lat - Szerokość geograficzna (Latitude).
   * @param lon - Długość geograficzna (Longitude).
   * @returns Skrót lokalizacji jako ciąg znaków lub '0' w przypadku braku danych.
   * 
   * @example
   * ```typescript
   * const hash = LocationHashHelper.fromLatLon(50.833028, 15.644499);
   * ```
   */
export class LocationHashHelper {
  static fromLatLon(lat: number, lon: number): string {
    if (!lat || !lon) return '0';
    return (lat * 1000 + lon).toFixed(5).replace(/\./g, '0');
  }
}
```


<p align="right">
  A wszystko to opisano z okazji <b>Zjazdu Technicznego Krótkofalowców SP w Burzeninie 2026</b><br>
  <i>Łukasz HF6TE</i>
</p>