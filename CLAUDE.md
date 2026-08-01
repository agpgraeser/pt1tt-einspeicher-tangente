# PT1TT Einspeicher-Tangente – Projektnotizen

## Projektbeschreibung
Web-App zur PT1TT-Parameterbestimmung über die **Einspeicher-Tangentenmethode**.
Eingabe zweier Tangentenpunkte TP1=(XTP1, tTP1) und TP2=(XTP2, tTP2) auf der Messkurve;
daraus Berechnung der Tangente (Gerade) und der Kennwerte kS, TT, TG.

Hervorgegangen aus `PT1TT_Wendetangente_2` (Spec 09V1, 2026-08-01). Kernänderung:
Umbenennung Wendepunkt (WP) → Tangentenpunkt (TP), da Einspeichersysteme keinen
Wendepunkt haben. **Die Berechnungslogik ist identisch zur Wendetangenten-App.**

- **Framework:** React + Vite (TypeScript)
- **UI:** Material UI (MUI)
- **Visualisierung:** Plotly.js (direkt, kein react-plotly.js)
- **Excel-Import:** xlsx
- **Start:** `npm run dev` (unter Dropbox-Pfad ggf. `npm run build` + `npx vite preview`)

## Projektstruktur
```
src/
  App.tsx
  main.tsx
  theme.ts
  components/
    ExcelImport.tsx    – Excel-Datei laden
    ParameterPanel.tsx – Parametereingabe, Ergebnisanzeige, Erläuterungs-Dialog
    ResponseChart.tsx  – Plotly-Diagramm (2 Subplots)
  store/
    reducer.ts         – Zustandsverwaltung (useReducer)
  types/
    index.ts           – TypeScript-Typen
  utils/
    pt1ttMath.ts       – Berechnungslogik (Tangente, Modell, Fehlermetriken)
    tooltips.ts        – Tooltip-Texte
```

## Mathematik (unverändert ggü. Wendetangente_2)
- **Eingabe:** TP1=(XTP1, tTP1), TP2=(XTP2, tTP2) – beide als absolute Koordinaten
- **Steigung:** a = (XTP2 − XTP1) / (tTP2 − tTP1)
- **Schnittpunkte:** t₁ = tTP1 + (XA − XTP1)/a,  t₂ = tTP1 + (XE − XTP1)/a
- **Kennwerte:** kS = ΔX/ΔY,  TT = t₁ − t₀,  TG = t₂ − t₁

> Hinweis: Interne State-/Variablennamen (`xWP`, `tWP`, `dx`, `dt`) wurden aus der
> Vorgänger-App beibehalten; nur die sichtbaren Labels/Tooltips/Diagramm-Texte heißen TP1/TP2.

## Unterschiede zu PT1TT_Wendetangente_2 (Spec 09V1)
- Titel: „PT1TT Parameter – Einspeicher-Tangentenmethode"
- Nur **Methode 1** aktiv; Methode 2 & 3 sichtbar, aber ausgegraut/disabled
- Feld-Labels XTP1/tTP1/XTP2/tTP2 (statt XWP1/…); Tooltips entsprechend
- Diagramm-Marker „TP1"/„TP2" (statt „WP"/„P₂")
- Neuer **Info-Button „Erläuterung einblenden"** im „Messdaten laden"-Bereich →
  MUI-Dialog mit Methodenbeschreibung
- Modell-Trace: „Modell (Einspeicher-Tangente)"

## Wichtige Implementierungsdetails (aus Vorgänger übernommen)
- `Plotly.react()` in `useEffect` — kein react-plotly.js-Wrapper
- Shapes/Annotations/Marker-Traces immer **konstante Arraygröße** → verhindert
  SVG-Rebuild und Hover-Reset
- `xaxis2: { matches: 'x' }` synchronisiert beide Zeitachsen
- Unsichtbarer Y-Anker-Trace erzwingt Autorange auf XA/XE

## Validierungsregeln (ParameterPanel)
- XTP1: strikt zwischen XA und XE (XA < XTP1 < XE)
- XTP2: XTP1 < XTP2 < XE
- tTP2: tTP2 > tTP1
- t0: ≥ 0

## Verlauf Zusammenarbeit mit Claude
- **2026-08-01:** Neuaufbau aus Wendetangente_2, Spec 09V1, Erläuterungs-Dialog ergänzt

## Offene Punkte / Nächste Schritte
- Deployment (Netlify) festlegen
