# Setup auf einer neuen Maschine

## 1. Hugo installieren

**Wichtig: immer die `extended`-Variante**, sonst funktioniert das SCSS von Blowfish nicht.

### macOS
```bash
brew install hugo
```

### Debian / Ubuntu / WSL
```bash
cd /tmp
VER=$(curl -s https://api.github.com/repos/gohugoio/hugo/releases/latest | grep -oP '"tag_name": "v\K[^"]+')
curl -LO "https://github.com/gohugoio/hugo/releases/download/v${VER}/hugo_extended_${VER}_linux-amd64.deb"
sudo dpkg -i "hugo_extended_${VER}_linux-amd64.deb"
rm -f "/tmp/hugo_extended_${VER}_linux-amd64.deb"
```

### Prüfen
```bash
hugo version
```
Die Ausgabe **muss** `extended` enthalten.

## 2. Repo klonen

```bash
git clone --recurse-submodules git@github.com:thephilluk/homepage.git
```

`--recurse-submodules` ist Pflicht — ohne das fehlt das Blowfish-Theme.

Falls schon ohne geklont:
```bash
git submodule update --init --recursive
```

## 3. Git-Hook aktivieren

Einmalig pro Maschine:
```bash
git config core.hooksPath .githooks
```

Der Hook baut vor jedem Commit und bricht bei Fehlern ab. Umgehen mit `git commit --no-verify`.

## 4. Lokal starten

```bash
hugo server -D
```

Unter WSL zusätzlich `--bind 0.0.0.0`, damit die Seite im Windows-Browser erreichbar ist.

Aufrufbar unter http://localhost:1313

---

# Arbeiten

## Neuen Artikel anlegen

```bash
hugo new content/projekte/mein-projekt.md
hugo new content/homelab/meine-notiz.md
```

Artikel stehen auf `draft: true` und sind nur mit `hugo server -D` sichtbar.

## Vor jeder Session

```bash
git pull --recurse-submodules
```

Sonst kollidiert die Submodule-Referenz, wenn auf einer anderen Maschine das Theme aktualisiert wurde.

## Veröffentlichen

```bash
git add -A
git commit -m "Beschreibung"
git push
```

GitHub Actions baut und deployt automatisch nach `/srv/www/homepage-neu/` auf dem Server.

## Theme aktualisieren

```bash
git submodule update --remote themes/blowfish
hugo server -D          # erst lokal anschauen!
git add themes/blowfish && git commit -m "Blowfish Update" && git push
```

---

# Struktur

| Pfad | Inhalt |
|---|---|
| `content/` | Alle Inhalte (Markdown) |
| `config/_default/` | Konfiguration, pro Sprache getrennt |
| `assets/css/custom.css` | Eigene CSS-Anpassungen |
| `layouts/shortcodes/` | Eigene Shortcodes (`columns`, `center`) |
| `i18n/en.yaml` | Überschriebene englische Textbausteine |
| `static/` | Favicons, Manifest |
| `.githooks/` | Git-Hooks (siehe Setup) |

## Sprachen

Deutsch liegt unter `/`, Englisch unter `/en/`.

- `datei.md` → deutsch
- `datei.en.md` → englisch

Blogartikel gibt es nur auf Deutsch. Die englischen Section-Indexes verlinken auf die deutschen Listen.

## Eigene Shortcodes
```
{{< columns >}}
Linke Spalte
<--->
Rechte Spalte
{{< /columns >}}

{{< center >}}
Zentrierter Text
{{< /center >}}
```


---

# Server

Läuft auf einem Hetzner-vServer hinter Nginx Proxy Manager.

| | |
|---|---|
| Zielverzeichnis | `/srv/www/homepage-neu/` |
| vHost | `/etc/nginx/sites-available/thephilluk.de` (Port 8081) |
| Wartung an | `/srv/www/maintenance-on.sh` |
| Wartung aus | `/srv/www/maintenance-off.sh` |

Im Wartungsmodus bleiben `/datenschutz/` und `/kontakt/` erreichbar.
