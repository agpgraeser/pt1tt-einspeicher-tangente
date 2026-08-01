# Spezifikation 09V1 – PT1TT_Einspeicher_Tang

**Version:** 09V1 · **Datum:** 2026-08-01 · **Autor:** Axel Graeser
(überarbeitet/präzisiert gemeinsam mit Claude auf Basis von `09-PT1TT_Einspeicher_Tangr.tex`)

## Zielsetzung
Neuaufbau der App zur PT1TT-Parameterbestimmung mit der **Einspeicher-Tangentenmethode**.
Die Darstellung der Tangentenmethode für Einspeichersysteme wird überarbeitet.

## Ausgangspunkt
1:1-Kopie der bestehenden App `PT1TT_Wendetangente_2`
(`F:\Dropbox\1_a_a_WebApp\PT1TT_Wendetangente_2`) nach
`F:\Dropbox\1_a_a_WebApp\PT1TT_Einspeicher_Tang`.
Gleiche Struktur, gleiches Framework (React + Vite + TypeScript, MUI, Plotly.js, xlsx).

**Kern der Änderung = Umbenennung** Wendepunkt (WP) → Tangentenpunkt (TP):
Bei einem Einspeichersystem gibt es keinen Wendepunkt; die zwei Eingabepunkte
definieren einfach eine Gerade (Tangente).

## Berechnungslogik – UNVERÄNDERT
`pt1ttMath.ts` bleibt inhaltlich gleich.
- Die Eingabepunkte (tTP1, XTP1) und (tTP2, XTP2) definieren zwei (t, X)-Datenpunkte
  und damit eine Gerade. Diese Gerade ist nur im Bereich (XA, XE) von Bedeutung.
- **t₁** = Zeitpunkt, zu dem die Gerade den Wert **XA** annimmt.
- **t₂** = Zeitpunkt, zu dem die Gerade den Wert **XE** annimmt.
- Kennwerte wie bisher:
  - kS = ΔX / ΔY = (XE − XA) / (YE − YA)
  - TT = t₁ − t₀
  - TG = t₂ − t₁

## 1. Textänderungen (UI)

| Ort | alt | neu |
|---|---|---|
| App-/Panel-Titel (`ParameterPanel.tsx`) | „PT1TT Parameter – Wendetangenten-Methode" | **„PT1TT Parameter – Einspeicher-Tangentenmethode"** |
| Diagramm-Titel Default (`ResponseChart.tsx`) | „PT1TT – Wendetangente" | **„PT1TT – Einspeicher-Tangente"** |
| Browser-Tab (`index.html`) | „PTn-Parameter Tool" | **„PT1TT Einspeicher-Tangente"** |
| Abschnitts-Überschrift | „Tangentenpunkte (WP1, WP2) – Eingabe" | **„Tangentenpunkte (TP1, TP2) – Eingabe"** |
| Feld-Labels | XWP1 / tWP1 / XWP2 / tWP2 | **XTP1 / tTP1 / XTP2 / tTP2** |
| Tooltips (`tooltips.ts`) | „XWP1: …" usw. | Texte auf **TP1 / TP2** umschreiben |
| Methoden-Caption | „Küpfmüller: TG = t₂ − t₁" | **„Einspeicher-Tangente: TG = t₂ − t₁"** |
| Modell-Trace-Name | „Modell M1 (Küpfmüller)" | **„Modell (Einspeicher-Tangente)"** |
| Diagramm-Marker/Annotation | „WP" / „P₂" | **„TP1" / „TP2"** |

## 2. Methodenwahl
- Nur **Methode 1** ist aktiv.
- Buttons „Methode 2" und „Methode 3" bleiben **sichtbar, aber ausgegraut/disabled**
  (nicht anwählbar).
- Interner Zustand fest auf `methode = 1`.

## 3. Neues Feld „Erläuterung einblenden"
- **Info-Button** neben dem „Datei laden"-Bereich (freier Platz neben „Messdaten laden").
- Öffnet ein **Popup/Dialog** (MUI `Dialog`) mit erklärendem Text.

### Erläuterungstext (Entwurf – bitte prüfen/korrigieren)

> **Einspeicher-Tangentenmethode**
>
> Die Einspeicher-Tangentenmethode bestimmt die Kennwerte eines PT1TT-Modells
> (Verzögerung 1. Ordnung mit Totzeit) grafisch aus der gemessenen Sprungantwort.
>
> **Vorgehen**
> 1. Signalgrenzen festlegen: Anfangswert XA und Endwert XE der Regelgröße,
>    YA/YE der Stellgröße sowie den Sprungzeitpunkt t₀.
> 2. Zwei Tangentenpunkte TP1 (XTP1, tTP1) und TP2 (XTP2, tTP2) auf dem steilsten,
>    nahezu geraden Abschnitt der Sprungantwort wählen. Sie definieren die Tangente.
> 3. Die Tangente wird bis zu den Grenzwerten verlängert:
>    - t₁ = Zeitpunkt, zu dem die Gerade XA erreicht
>    - t₂ = Zeitpunkt, zu dem die Gerade XE erreicht
>
> **Kennwerte**
> - Verstärkung: kS = ΔX / ΔY = (XE − XA) / (YE − YA)
> - Totzeit: TT = t₁ − t₀
> - Ausgleichszeit: TG = t₂ − t₁
>
> **Hinweise**
> Die Gerade ist nur im Bereich zwischen XA und XE von Bedeutung.
> Die Punkte müssen zwischen XA und XE liegen: XA < XTP1 < XTP2 < XE.

## 4. Nicht angetastet
- Excel-Import (`ExcelImport.tsx`, `excelParser.ts`).
- Plotly-Stabilität: feste Array-Größen für Shapes/Annotations/Traces
  (verhindert SVG-Rebuild und Hover-Reset), Achsensynchronisierung `xaxis2.matches = 'x'`,
  unsichtbarer Y-Anker-Trace für Autorange auf XA/XE.
- Validierungsregeln: XA < XTP1 < XE · XTP1 < XTP2 < XE · t₀ ≥ 0.

## 5. Umsetzungs-Defaults
- **Interne Namen bleiben** (`xWP`, `tWP`, `dx`, `dt` …). Nur sichtbare Labels/Tooltips/
  Diagramm-Texte werden geändert → minimales Fehlerrisiko, funktional identisch.
- `package.json` → `"name": "pt1tt-einspeicher-tang"`.
- Kein `node_modules` kopieren; frisches `npm install`.

## Offene Punkte
- Erläuterungstext oben ist ein Entwurf und wartet auf Freigabe/Korrektur.
- Deployment (Netlify) noch nicht festgelegt.
