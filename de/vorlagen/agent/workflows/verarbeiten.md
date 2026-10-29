---
description: Alle wartenden Dokumente rekonstruieren, ablegen und bearbeiten
---
1. Die wartenden Dokumente mit ihrer Art auflisten.
2. Für jedes Foto / Bild / reine Bild-PDF: als Rolle `rekonstruktion` handeln (auslassen, wenn die Konfiguration
   `on_request` sagt; dann nutzt die Ablage einfache OCR und vermerkt das).
3. Für jede wartende ID als Rolle `ablage` handeln (Sub-Agenten: Gruppen bis zu 5).
4. Für jedes Dokument, dessen Aktion nicht `none` ist oder das Befunde hat, als Rolle `vorgang` handeln, mit ID, Aktionen,
   Fristen, Befunden und Begründung (Sub-Agenten: einer je Dokument, höchstens 5 gleichzeitig). Dokumente aus
   Nur-lokal-Kategorien bearbeitet nur ein lokales Modell.
5. Bericht wie im Orchestrator unter „Am Ende jedes Laufs“. {{args}}
