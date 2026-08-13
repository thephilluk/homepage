---
title: "HSM-Badge: Ein E-Paper-Namensschild, das mitdenkt"
date: 2026-08-16
publishDate: 2026-08-16
tags: ["micropython", "badger2350", "e-paper", "hsm", "pimoroni"]
categories: ["Projekte"]
summary: "Aus einem Pimoroni Badger 2350 ist ein Namensschild fürs Museum geworden – mit Fahrplan, Fahrgastzähler und QR-Code. Und mit ein paar Lektionen über den Unterschied zwischen Simulator und echter Hardware."
---

Es fing an wie die meisten meiner Projekte: Ich habe ein Gerät gesehen, das ich nicht brauchte, und mir überlegt, wofür ich es brauchen könnte.

Das Gerät war ein [Pimoroni Badger 2350](https://shop.pimoroni.com/products/badger-2350) – ein E-Paper-Display mit RP2350, WLAN, Bluetooth und ein paar Knöpfen, gedacht als programmierbares Namensschild für Konferenzen. Der Anwendungsfall war schnell gefunden: Im [Straßenbahnmuseum](https://tram-museum.de) laufe ich an Fahrtagen ohnehin mit einem Schild herum. Warum nicht mit einem, das mehr kann als meinen Namen anzeigen?

## Warum E-Paper hier genau richtig ist

Ein Namensschild soll den ganzen Fahrtag durchhalten, und genau dafür ist E-Paper gebaut: Strom fließt nur beim Bildwechsel, danach steht das Bild von allein. Ein hinterleuchtetes Display wäre nach ein paar Stunden leer.

Praktisch ist außerdem, dass die Daten nicht live kommen müssen. Das Museum hat keine flächendeckende WLAN-Abdeckung – aber das Gerät zieht sich seinen Fahrplan einmal, wo Empfang ist, und zeigt ihn dann offline an. Wenn ich doch frische Daten will, geht der Handy-Hotspot.

Zwei Einschränkungen habe ich unterwegs gelernt: Das WLAN kann nur 2,4 GHz, und Captive Portals funktionieren mit so einem Mikrocontroller praktisch nicht. Gäste-WLAN mit Login-Seite fällt also aus.

## Was drauf ist

Aus dem geplanten Namensschild sind am Ende vier Apps geworden.

**HSM Badge** ist die eigentliche Museums-App. Mehrere Seiten, per Tasten durchblätterbar: Name und Rolle, der Fahrplan des Tages, ein Fahrgastzähler zum Mitzählen, Funkkanäle und Kontakte, Notfallinformationen und ein QR-Code für Besucher.

**Visitenkarte** erzeugt den vCard-QR-Code direkt auf dem Gerät, hat einen Dunkelmodus und einen Präsentationsmodus – für Messen und Veranstaltungen außerhalb des Museums.

**WLAN-Check** ist ein eigenständiges Diagnose-Werkzeug mit Fallback über mehrere hinterlegte Netze. Klingt banal, ist aber erstaunlich oft nützlich.

**IT-Crew** ist die Arbeitsvariante: WLAN-Test, Erreichbarkeitsprüfung, ein per Code gesperrter WLAN-QR-Code zum Weitergeben, ein kleines Netzwerk-Spickzettelchen und Kontakte.

Dazu kam ein gepatchtes Menü, das eigene Anzeigenamen über eine `name.txt` unterstützt und Apps in fester Reihenfolge anpinnt.

## Wo ich hängengeblieben bin

Der interessante Teil war nicht das Schreiben der Apps, sondern das Herausfinden, warum sie auf der echten Hardware anders liefen als gedacht.

**Die Firmware ruft `init()` nie auf.** Das steht so in keiner Dokumentation, die ich gefunden habe. Wer seinen gespeicherten Zustand brav in einer `init()`-Funktion lädt, wundert sich, warum die App nach jedem Aufwachen wieder auf Seite null steht. `State.load()` gehört auf Modulebene.

**Jede `update()`-Funktion muss mit `badge.update()` enden, gefolgt von `wait_for_button_or_alarm()`.** Fehlt das, friert die App beim Öffnen ein. Zwei Zeilen, ein Nachmittag Fehlersuche.

**Der Simulator ist nicht die Hardware.** Ich habe eine ganze Weile gegen den Simulator entwickelt, bis mir aufging, dass dessen Menü andere Importe benutzt als die echte Firmware. Ein Patch, der im Simulator sauber lief, hat das Gerät zuverlässig zerlegt. Seitdem gilt: Referenz ist das [Firmware-Repository](https://github.com/pimoroni/badger2350), nichts anderes.

**Und dann war da noch uQR.** Die QR-Bibliothek stürzte reproduzierbar mit einem Typfehler ab – irgendwo im Optimierungspfad, der intern Reguläre Ausdrücke benutzt und unter MicroPython Strings und Integer durcheinanderbringt. Die Lösung ist ein einziges Argument: `add_data()` mit `optimize=0` aufrufen. Gefunden habe ich das nicht durch Nachdenken, sondern durch Ausprobieren.

## Was ich mitgenommen habe

Mikrocontroller-Entwicklung ist ein anderes Arbeiten als alles, was ich sonst mache. Kein Stacktrace, der einem sagt was los ist. Kein `printf`-Debugging, das nicht selbst das Timing verändert. Man baut eine Hypothese, flasht, wartet, schaut aufs Display und rät weiter.

Was mir dabei gefällt: Am Ende steht ein Ding, das man anfassen kann. Und das an einem Fahrtag zwischen hundert Jahre alten Straßenbahnen hängt, während drinnen ein RP2350 rechnet. Diese Kombination hat was.

## Stand

Läuft. Die vier Apps sind auf dem Gerät, das Menü ist gepatcht, und ich habe es bei mehreren Gelegenheiten getragen.

Was noch offen ist: Der automatische Abgleich mit unseren Termindaten. Aktuell pflege ich den Fahrplan von Hand, was für ein System, das ich selbst geschrieben habe, ein bisschen peinlich ist.
