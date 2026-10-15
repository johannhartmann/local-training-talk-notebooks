# Josev-2B in Unsloth Studio

[`josev-2B.yaml`](josev-2B.yaml) enthält die Trainings-, LoRA- und Logging-Parameter des Josev-2B-Beispiels aus dem Talk. Sie ist eine **partielle Studio-Konfiguration**: Basismodell, Datensatz und Entscheidungsmodell-Modus stehen nicht in der Datei. Sie enthält auch keine Modellgewichte.

## Konfiguration verwenden

1. [Unsloth Studio installieren](https://unsloth.ai/docs/new/studio/install) und mit `unsloth studio` starten. Die lokale Oberfläche öffnet sich unter `http://127.0.0.1:8888`.
2. Im Trainingsformular `unsloth/Qwen3.5-2B` als Basismodell und **QLoRA** (4-Bit) wählen. Als Hugging-Face-Datensatz [`johannhartmann/decisions-de`](https://huggingface.co/datasets/johannhartmann/decisions-de) mit Subset `all` auswählen. Für den im Talk gezeigten Lauf zusätzlich „Trainieren als: Entscheidungsmodell“ wählen, sofern diese Option in der verwendeten Studio-Version vorhanden ist.
3. Unten rechts bei **Training/Config → Upload** `josev-2B.yaml` laden. Danach die angezeigten Werte prüfen: 4096 Kontext-Token, 2 Epochen, Lernrate `1e-4`, Batch 16 × 2 Gradient Accumulation sowie LoRA-Rang und -Alpha jeweils 64. Erst nach Prüfung **Start Training** wählen.

Die Datei ist keine vollständige CLI-Konfiguration für `unsloth train --config`: Sie speichert nur die Studio-Parameter und definiert weder Modell noch Daten noch den speziellen Decision-Head. Ohne Entscheidungsmodell-Modus entsteht nicht das im Talk gezeigte Modell. Der Lauf benötigt eine Studio-Version mit dieser Funktion und ausreichend GPU-Speicher. Die [Studio-Anleitung](https://unsloth.ai/docs/new/studio/start) beschreibt den YAML-Upload und die übrigen Formularfelder.
