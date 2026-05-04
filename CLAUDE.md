# CLAUDE.md — Touch 'n' Lock Landing Page

## Opis i cel

Landing page sprzedażowy produktu **Touch 'n' Lock** — kłódki biometrycznej na odcisk palca.
Strona jest w języku polskim, jednokolumnowa, dark-mode. Cel: konwersja do zakupu (cena 99 zł).
Plik: `Landing Page.html` (single-file, inline CSS + JS).

---

## Kolory (CSS variables + hex)

| Zmienna          | Wartość                          | Użycie                         |
|------------------|----------------------------------|--------------------------------|
| `--bg`           | `#0C0C0C`                        | Główne tło strony              |
| `--bg-warm`      | `#0f0e0d`                        | Cieplejszy wariant tła         |
| `--bg-card`      | `#131211`                        | Tło kart (features, steps)     |
| `--bg-card2`     | `#161514`                        | Tło kart w stanie hover        |
| `--accent`       | `#4A9EFF`                        | Jedyny kolor (niebieski)       |
| `--accent-dim`   | `rgba(74,158,255,0.12)`          | Przygaszony akcent             |
| `--accent-glow`  | `rgba(74,158,255,0.35)`          | Poświata akcentu               |
| `--white`        | `#F0F4FF`                        | Główny kolor tekstu            |
| `--white-dim`    | `rgba(240,244,255,0.55)`         | Drugorzędny tekst              |
| `--white-faint`  | `rgba(240,244,255,0.12)`         | Subtelne bordery/tła           |

---

## Fonty

| Font         | Wagi             | Źródło       | Użycie                                  |
|--------------|------------------|--------------|-----------------------------------------|
| **Syne**     | 400, 500, 600, 700, 800 | Google Fonts | Nagłówki, logo, przyciski CTA, liczby  |
| **Inter**    | 300, 400, 500    | Google Fonts | Treść paragrafów, opisy, nawigacja      |
| **Courier New** | (system)      | monospace    | Tabela specyfikacji (`--mono`)          |

---

## Sekcje strony (w kolejności)

1. **`<nav>`** — stały pasek nawigacji (fixed); linki: Cechy, Działanie, Specyfikacja, Kup teraz
2. **`#heroScrollWrapper`** — wrapper ze `height: calc(100vh + 480px)` tworzący przestrzeń scrollu dla animacji
3. **`#hero`** — sticky sekcja (100vh) z dwoma canvasami kłódki (zamknięta + otwarta) i tłem gradientowym
4. **`.hero-text-reveal`** — blok z tytułem i CTA, pojawia się dopiero po "otwarciu" kłódki scrollem
5. **`#value-prop`** — wielki cytat "Żadnych kluczy. Żadnych kodów. Żadnych kompromisów."
6. **`#features`** — 3 karty z parametrami: 0.2s odblokowanie, 10 grup odcisków, IP67
7. **`#how-it-works`** — 3 kroki: Zarejestruj → Przyłóż → Gotowe
8. **`#specs`** — tabela specyfikacji technicznej (10 wierszy)
9. **`#cta`** — sekcja zakupowa z tytułem "Zamów teraz. 99 zł." i przyciskiem
10. **`<footer>`** — logo + copyright

Między sekcjami 4–8 są pionowe `<div class="section-divider">` (1px gradient).

---

## Animacje i interakcje

### CSS Animations
- **`lockBreathe`** (4s, infinite) — pulsujące drop-shadow na kłódce zamkniętej w stanie idle
- **`scrollPulse`** (2s, infinite) — pulsujący scroll hint (opacity 0.4 → 1)
- **`heroFadeUp`** — wejście elementów hero (translateY(20px) + opacity 0 → 1, staggered delays)

### JavaScript — scroll-driven animacje
- **Animacja otwierania kłódki** (0–480px scrollu):
  - Kłódka zamknięta: opacity 1→0, blur 0→12px, brightness 1→1.8
  - Kłódka otwarta: wchodzi od dołu (translateY 28px→0), scale 0.97→1.02, opacity 0→1
  - Easing: `easeInOut` (quad)
  - Zakres: `ANIM_RANGE = 480` px
- **Hero Text Reveal** — blok `.hero-text-reveal` dostaje klasę `.visible` gdy scroll ≥ 408px (85% ANIM_RANGE); chowa się gdy mniejszy

### JavaScript — IntersectionObserver
- **Scroll Reveal** — elementy `.reveal` dostają klasę `.visible` przy wejściu w viewport (threshold 0.1, rootMargin -40px bottom); `.reveal-delay-1/2/3/4` to staggered delays 0.1–0.4s

### JavaScript — Canvas
- **Białe tło produktu** — oba obrazy kłódki są wczytywane przez `<canvas>` i mają usuwane jasne piksele (brightness > 210, saturation < 0.12 → alpha = 0; strefa przejścia 180–210)
- Obrazy: `uploads/S43b68a6c87cc49dab0f8db8db20c42cc9.avif` (zamknięta), `uploads/S07c3584d8c494dccad72b45402c21d3ch.avif` (otwarta)

### Hover / CSS interakcje
- **Feature card hover** — inset glow (rgba 74,158,255,0.1), ciemniejsze tło, gradient border-top opacity 0→0.6
- **Nav links hover** — color transition 200ms (white-dim → white)
- **CTA buttons hover** — fill akcentem, glow box-shadow
- **Nav scroll** — po 80px scrollu nav dostaje `background: rgba(12,12,12,0.95)` + `backdrop-filter: blur(12px)`

---

## Zasady przy edycji

1. **Nie zmieniaj palety** — `--accent: #4A9EFF` to jedyny kolor. Wszystko inne jest odcieniem czerni lub semi-transparentną bielą. Nie dodawaj nowych kolorów bez wyraźnej potrzeby.

2. **Nie ruszaj scroll-wrapper height** — `#heroScrollWrapper` musi mieć `height: calc(100vh + 480px)`. Zmiana rozbije timing animacji kłódki. `ANIM_RANGE = 480` musi być spójne z tą wartością.

3. **Canvas logika jest krucha** — algorytm cutout białego tła jest pixel-level. Jeśli zmieniasz zdjęcia produktu, testuj wizualnie czy piksele produktu nie są odcinane (próg brightness > 210).

4. **Nazwy plików obrazów** — trzymaj obrazy w katalogu `uploads/`. Jeśli zmieniasz pliki, zaktualizuj ścieżki w obu blokach `(function() { ... })()` w JS.

5. **Fonty** — Syne do wszystkiego co "brandowe" (logo, nagłówki H1/H2/H3, przyciski). Inter do treści opisowej. Courier New wyłącznie do tabeli specs.

6. **Dark design — bez jasnych teł** — żadna sekcja nie może mieć jasnego tła. Dozwolone tylko subtelne gradienty z `--accent` o opacity ≤ 0.1.

7. **Strona w języku polskim** — nie tłumacz treści na angielski.

8. **Single-file** — cały CSS i JS jest inline w `Landing Page.html`. Nie rozbijaj na osobne pliki bez wyraźnej prośby.

9. **Animacje scroll-reveal** — elementy wchodzące ze scrollem muszą mieć klasę `.reveal`. Przy dodawaniu nowych sekcji stosuj `.reveal-delay-1/2/3/4` dla staggeringu.

10. **Przycisk CTA** — zawsze `border: 1.5px solid var(--accent)`, wypełnienie akcentem na hover. Nie używaj filled button jako domyślnego stanu.
