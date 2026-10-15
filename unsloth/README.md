# Unsloth Studio: Trainingsbeispiele

## Hausaufgabe: Qwen3.5-4B selbst trainieren

Trainiere **Qwen3.5-4B mit QLoRA** auf [`johannhartmann/inclusive-sft-v3-expanded`](https://huggingface.co/datasets/johannhartmann/inclusive-sft-v3-expanded) und vergleiche die Antworten vor und nach dem Training. Das ist ein Sprachmodell-Fine-Tuning; wähle **Language model** als Trainingsmodus.

### 1. Studio installieren und starten

Unsloth Studio bietet eine lokale Weboberfläche für Modellwahl, Daten, Training und Export. Für den hier beschriebenen QLoRA-Lauf verwende einen Rechner mit NVIDIA-GPU und passenden Treibern; unter Linux/WSL auch mit CUDA. Die [offizielle Installation](https://unsloth.ai/docs/new/studio/install) beschreibt die Voraussetzungen und weitere Plattformen.

Unter Linux/WSL:

```bash
curl -fsSL https://unsloth.ai/install.sh | sh
unsloth studio -p 8888
```

Öffne anschließend <http://127.0.0.1:8888> und lege beim ersten Start das lokale Passwort an. Windows verwendet den PowerShell-Installer aus der verlinkten Anleitung.

### 2. Modell und Datensatz auswählen

Auf der Trainingsseite folgende Felder setzen:

| Feld | Wert |
| --- | --- |
| Model | **Qwen3.5-4B** |
| Method | **QLoRA** (4-Bit) |
| Train as | **Language model** |
| Dataset | `johannhartmann/inclusive-sft-v3-expanded` |
| Subset | `chat` |
| Train Split / Evaluation Split | `train` / `validation` |
| Target Format | `ChatML` |

Prüfe in der Datenvorschau, dass die Gesprächsrollen und Antworten korrekt erkannt werden. Notiere **vor dem Training fünf eigene Testprompts** und die Antworten des Basismodells. Verwende dafür keine Beispiele aus den Trainingsdaten.

### 3. YAML importieren und trainieren

Lade [`qwen3.5-4b-inclusive-sft-v3-expanded.yaml`](qwen3.5-4b-inclusive-sft-v3-expanded.yaml) über **Training / Config → Upload** unten rechts. Die Trainingsparameter sind unverändert aus der bereitgestellten Studio-Datei übernommen. Die YAML enthält **keine Modell- oder Datensatzwahl**; diese Felder musst du wie oben setzen und nach dem Import kontrollieren.

Wichtige Werte aus der Datei:

| Parameter | Wert |
| --- | --- |
| Epochen / Kontextlänge | `1` / `8192` Token |
| Lernrate | `0.0002` |
| Batch / Gradient Accumulation | `2` / `4` (effektiv 8 Beispiele auf einer GPU) |
| LoRA Rank / Alpha / Dropout | `16` / `16` / `0` |
| Optimizer / Scheduler | `adamw_8bit` / `linear` |
| Gradient Checkpointing | `unsloth` |
| Training auf Antworten | `train_on_completions: true` |

Die LoRA-Zielmodule stehen ebenfalls in der YAML. Danach **Start Training** wählen und Trainings- sowie Validierungsverlust beobachten. Bei Speichermangel zunächst Batch oder Kontextlänge reduzieren und den geänderten Lauf separat speichern. Der [Studio-Quickstart](https://unsloth.ai/docs/new/studio/start) erklärt den YAML-Import und die Trainingsansicht.

### 4. Ergebnis mitbringen

Speichere bzw. exportiere den trainierten Adapter und lade ihn für den Vergleich mit demselben Basismodell. Stelle dieselben fünf Prompts mit gleichen Generierungseinstellungen erneut. Notiere je Beispiel: Was wurde besser, was schlechter? Bringe zwei Vorher/Nachher-Beispiele, einen Fehlversuch sowie Laufzeit und maximal belegten GPU-Speicher mit. Ein niedrigerer Trainingsverlust allein ist noch kein Nachweis besserer Antworten.

---

## Josev-2B in Unsloth Studio

[`josev-2B.yaml`](josev-2B.yaml) enthält die Trainings-, LoRA- und Logging-Parameter des Josev-2B-Beispiels aus dem Talk. Sie ist eine **partielle Studio-Konfiguration**: Basismodell, Datensatz und Entscheidungsmodell-Modus stehen nicht in der Datei. Sie enthält auch keine Modellgewichte.

### Konfiguration verwenden

1. [Unsloth Studio installieren](https://unsloth.ai/docs/new/studio/install) und mit `unsloth studio` starten. Die lokale Oberfläche öffnet sich unter `http://127.0.0.1:8888`.
2. Im Trainingsformular `unsloth/Qwen3.5-2B` als Basismodell und **QLoRA** (4-Bit) wählen. Als Hugging-Face-Datensatz [`johannhartmann/decisions-de`](https://huggingface.co/datasets/johannhartmann/decisions-de) mit Subset `all` auswählen. Für den im Talk gezeigten Lauf zusätzlich „Trainieren als: Entscheidungsmodell“ wählen, sofern diese Option in der verwendeten Studio-Version vorhanden ist.
3. Unten rechts bei **Training/Config → Upload** `josev-2B.yaml` laden. Danach die angezeigten Werte prüfen: 4096 Kontext-Token, 2 Epochen, Lernrate `1e-4`, Batch 16 × 2 Gradient Accumulation sowie LoRA-Rang und -Alpha jeweils 64. Erst nach Prüfung **Start Training** wählen.

Die Datei ist keine vollständige CLI-Konfiguration für `unsloth train --config`: Sie speichert nur die Studio-Parameter und definiert weder Modell noch Daten noch den speziellen Decision-Head. Ohne Entscheidungsmodell-Modus entsteht nicht das im Talk gezeigte Modell. Der Lauf benötigt eine Studio-Version mit dieser Funktion und ausreichend GPU-Speicher. Die [Studio-Anleitung](https://unsloth.ai/docs/new/studio/start) beschreibt den YAML-Upload und die übrigen Formularfelder.
