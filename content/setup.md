---
title: "Setup"
description: "Womit ich arbeite – Hardware, Software und was im Serverschrank steht"
showDate: false
showAuthor: false
showReadingTime: false
showWordCount: false
showTableOfContents: true
aliases: ["/uses/"]
---

Eine Liste von Dingen, die ich benutze. Nicht weil sie die besten sind, sondern weil sie funktionieren und ich mich dran gewöhnt habe.

## Arbeitsplatz

**Hel** ist der Hauptrechner: Ryzen 9 5950X, 64 GB RAM, RTX 3060 Ti. Läuft unter Windows, das eigentliche Arbeiten passiert aber in WSL2. Die 16 Kerne sind Überschuss für das meiste, zahlen sich aber aus, sobald irgendwas kompiliert oder mehrere VMs gleichzeitig laufen.

**MacBook Pro (2022)** für unterwegs und alles, wo ich nicht am Schreibtisch sitze.

Dazu eine **Ducky One 3 RGB**, eine **Logitech G502 Hero** und ein **SteelSeries Arctis Nova Pro**. 

## Software

**Vim** ist der Editor auf der Kommandozeile, und das ist da auch der Normalfall – die meiste Arbeit passiert im Terminal. **VS Code** kommt dazu, wenn ein Projekt groß genug wird, dass ich mehr als drei Dateien gleichzeitig im Blick haben will.

**Zsh** mit angepasstem Oh My Zsh als Shell. Die Konfiguration liegt in einem privaten Repo und wird per ansible auf alle Maschinen verteilt – das erspart mir, auf jedem neuen System wieder von vorn anzufangen.

**Firefox** als Browser, **Obsidian** für Notizen. Obsidian vor allem, weil die Notizen einfach Markdown-Dateien auf der Platte sind und nicht in irgendeiner Datenbank verschwinden.

## Homelab

**Thor** ist der Server: ein ODROID-H3 mit Pentium Silver N6005 und 16 GB DDR4. Klein, sparsam, und für einen Heimserver völlig ausreichend. Der überwiegende Teil der selbstgehosteten Dienste läuft hier.

**Loki** ist der Proxmox-Host. Ein AMD A4-9125 mit knapp 4 GB RAM – deutlich schwächer als Thor und mit dauerhaft hoher Speicherauslastung, aber für ein paar kleine VMs reicht es. Der Ersatz steht auf der Liste. Wie so vieles.

Was genau darauf läuft, wäre eine eigene Seite. Kurz gesagt: eine ganze Menge, und regelmäßig kommt etwas dazu.

## Diese Seite

Gebaut mit [Hugo](https://gohugo.io/) und dem [Blowfish](https://blowfish.page/)-Theme, deployed über GitHub Actions auf einen vServer bei Hetzner. Statisch, ohne Tracking, ohne Datenbank.
