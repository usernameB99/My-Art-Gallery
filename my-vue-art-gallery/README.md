# My Art Gallery - Dev & Deploy Workflow

Dieses Readme beschreibt meinen persönlichen Workflow für Entwicklung, GitHub Push und Deployment auf GitHub Pages für das Vue + Vite Projekt **My Art Gallery**.

---

## 1️⃣ Projekt starten (lokal)

Um die Galerie lokal zu starten:

```cmd
npm install       # nur einmal, um Dependencies zu installieren
npm run dev       # startet den Vite Dev Server
```

* Die Seite läuft dann unter: `http://localhost:5173`
* Änderungen werden automatisch im Browser geladen

---

## 2️⃣ Änderungen committen & pushen

Alle Änderungen werden auf dem **main** Branch gesichert:

```cmd
git add .
git commit -m "Beschreibung der Änderungen"
git push
```

* VS Code Source Control kann auch verwendet werden:

  * Änderungen `stage` → Commit → Push
* Immer auf **main** arbeiten, nicht auf `gh-pages`

---

## 3️⃣ Deployment auf GitHub Pages

Nach dem Commit/Push auf **main**, deployen wir die gebaute Seite auf den **gh-pages** Branch:

```cmd
npm run build      # erstellt den /dist Ordner
npm run deploy     # pushed dist/ auf gh-pages für GitHub Pages
```

* GitHub Pages liefert automatisch den `gh-pages` Branch aus
* Die Live-Seite:

  ```
  https://usernameB99.github.io/My-Art-Gallery/
  ```

---

## 4️⃣ Workflow Zusammenfassung

1. Code ändern / neue Bilder hinzufügen
2. Änderungen committen & pushen auf **main**
3. Deployment: `npm run deploy` → `gh-pages` aktualisiert
4. GitHub Pages zeigt die aktualisierte Seite

---

## 5️⃣ Hinweise

* **VS Code CMD verwenden**, nicht PowerShell, wegen Execution Policy
* `gh-pages` Branch nie manuell ändern, nur über `npm run deploy`
* Vor jedem Deploy sicherstellen, dass **main sauber und up-to-date** ist
* Für neue Features / Änderungen: immer auf **main** arbeiten, deployen erst, wenn alles fertig

---

## 6️⃣ Optional

* Lokale Vorschau der Seite: `npm run dev`
* Wenn npm / GitHub Pages Probleme machen: einfach CMD neu starten und nochmal `npm run deploy` ausführen
