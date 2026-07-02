# Lauffreund – Roadmap: vom Prototyp zur echten App

*Stand: Juli 2026*

Lauffreund lässt Freunde das Rennen eines Läufers in Echtzeit verfolgen und anfeuern.
Der Antrieb ist der **Anfeuer-Sog**: Weil die Crew zuschauen will, aktiviert der Läufer sein Tracking.

---

## Wo wir stehen

Ein vollständiger, testbarer **Prototyp** liegt als einzelne `index.html` im Repository und ist über
GitHub Pages erreichbar. Er zeigt das gesamte Erlebnis – allerdings mit simulierter Bewegung, ohne
Backend:

- Live-Board mit Crew-Punkten auf einer Strecke, km / Tempo / Ziel-ETA pro Person
- Ziel pro Läufer in **Kilometer oder Zeit** (jeder läuft beim selben Event andere Distanzen)
- Onboarding mit der Gabelung **„Ich laufe" / „Ich feuere an"**
- Crew einladen per **WhatsApp / QR / Link**
- Einladungs-Landung auf **Lebendigem** (das laufende Rennen), nicht auf einer leeren Seite
- **Beitritts-Flow**: Eingeladene werden echte Crew-Mitglieder
- **Anfeuer-Sicht des Läufers**: „X feuern dich an" + eingehende Grüße während des Trackings

Bereits getroffene Grundsatz-Entscheidungen: kein Strava (API verbietet das Anzeigen fremder Daten),
keine offiziellen Timing-APIs im Start (partnergebunden) → **Live-GPS vom Läufer-Handy** als Datenquelle,
Crew über **opt-in-Einladungen** statt automatischem Import.

---

## Leitprinzip: zwei Nutzer, zwei technische Wege

Die wichtigste architektonische Erkenntnis – sie ordnet den gesamten Aufwand:

- **Anfeuerer** schaut nur zu → eine Karte mit Live-Punkten. Das kann eine normale **Web-Seite**.
- **Läufer** muss seine GPS-Position **stundenlang im Hintergrund** senden (Handy in der Tasche,
  Bildschirm aus). Das kann eine Web-Seite **nicht** zuverlässig – vor allem iPhones stoppen
  Hintergrund-Code im Browser. Dafür braucht es eine echte, **installierbare App**.

Konsequenz: Die Masse der Nutzer (Zuschauer) kommt über Web rein; nur die Läufer brauchen die
aufwändige App. Das spart erheblich Aufwand und hält den Anfeuer-Sog ab Tag eins am Leben, weil
Eingeladene ohne Installation sofort mitschauen können.

---

## Was „echte App" unter der Haube heißt

Vier Bausteine, die der Prototyp heute nur simuliert:

1. **Konten** – Login mit E-Mail, Apple oder Google
2. **Echtzeit-Verteilung** – ein Server, der die Live-Position eines Läufers an seine Crew streamt
3. **Dauerhafte Daten** – gespeicherte Crews, Einladungen, Rennen
4. **Push-Nachrichten** – „X ist beigetreten", „Rennstart in 15 Min – Tracking an?", Anfeuer-Grüße

Nichts davon musst du selbst bauen. Ein fertiger Baukasten liefert Server, Datenbank, Login,
Echtzeit und Push als Dienst:

- **Backend:** Firebase **oder** Supabase
- **App (Läufer):** React Native **oder** Flutter – iPhone und Android aus einer Codebasis,
  mit fertigen Bibliotheken für Hintergrund-GPS
- **Anfeuer-Ansicht (Web):** kann direkt auf dem heutigen Prototyp aufbauen
- **Karte:** Mapbox / MapLibre für die echte Streckendarstellung (der Prototyp nutzt nur eine
  abstrakte Linie)

---

## Phasen

### Phase 0 – jetzt: validieren, nicht bauen
Den Prototyp mit der eigenen Laufgruppe teilen und die zwei entscheidenden Fragen prüfen:
Aktivieren Läufer das Tracking, **weil** ihre Crew zuschaut? Freut sich der Zuschauer genug,
um beizutreten? Billigste und wichtigste Prüfung – bevor ein Cent in Technik fließt.

### Phase 1 – das dünnste echte MVP
Zuerst das eine Risiko isoliert beweisen, an dem alles hängt:
**Läuft Hintergrund-GPS über einen ganzen Marathon zuverlässig, ohne den Akku zu töten?**
Ein kleines technisches Vorab-Experiment, kein Feature. Steht das, kommen die im Prototyp bereits
entworfenen Flows drumherum:
Konto · Rennen wählen · per Link einladen · Crew · Läufer sendet echtes GPS · Anfeuerer sieht die
Live-Karte. Die Zuschauer-Web-Ansicht ist zugleich die Landung für Eingeladene ohne App.

### Phase 2 – vertrauenswürdig und schön
Push-Nachrichten · Vorfreude-Ansicht zwischen den Rennen · echte Kartendarstellung ·
und – für ein deutsches Produkt nicht verhandelbar – **Datenschutz von Anfang an**
(siehe unten). Nicht nachträglich anschrauben.

### Phase 3 – skalieren und veredeln
Jetzt lohnt sich die **offizielle Zeitmessung** (Race Result & Co.) als Ergänzung für die großen
Events, wo präzise Zwischenzeiten den Unterschied machen. Das Renn-Verzeichnis wächst über
Nutzer-Einträge und eine Kalenderquelle.

---

## Datenschutz (durchgängig, nicht optional)

Standortdaten sind besonders sensibel, und das Publikum ist deutschsprachig. Von Beginn an:

- Standort-Teilen **nur opt-in pro Rennen**, aktiv vom Läufer gestartet
- **Automatischer Stopp im Ziel**
- Sichtbar nur für die eigene Crew, nicht öffentlich
- Klare Einwilligung, Daten jederzeit löschbar

Im Opt-in-Modell ist Datenschutz sogar ein **Verkaufsargument** statt ein Risiko.

---

## Aufwand & Team – ehrlich

- Das ist **Monatsarbeit**, kein Wochenende.
- Fertige Dienste halten die laufenden Kosten anfangs gering; der Hauptaufwand ist
  **Entwicklungszeit**, nicht Serverkosten. Auf Karten- und Push-Kosten erst bei größeren
  Nutzerzahlen achten.
- Die Kernkompetenz, an der es steht oder fällt: **mobile Entwicklung mit Hintergrund-Standort.**
  Das ist der knifflige Teil.
- Zwei realistische Wege: selbst mit **React Native + Firebase/Supabase** lernen (der
  solo-freundlichste Pfad) – oder für die Läufer-App eine Entwicklerin dazuholen und Konzept plus
  Design (hast du schon) selbst einbringen.

---

## Der nächste Schritt

Nicht Programmieren. Sondern **zwei Dinge parallel validieren:**

1. Den Prototyp bei echten Läufern testen (zieht der Anfeuer-Sog?).
2. Das GPS-Hintergrund-Experiment (hält es einen Marathon durch?).

Beweist du beides, sind die zwei Fundamente bestätigt, die das ganze Produkt tragen – und der Bau
lohnt sich.
