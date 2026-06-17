# Hey Vedic - ESP32-S3 Deployment

## Files

| File | Description |
|------|-------------|
| `hey_vedic.tflite` | Quantized INT8 streaming wake word model (60.8 KB) |
| `hey_vedic.json` | ESPHome model metadata (host this alongside the .tflite) |
| `esphome_respeaker_lite.yaml` | ESPHome config for ReSpeaker Lite |

## How to use

### Option 1: Host files on GitHub (easiest)

1. Upload `hey_vedic.tflite` and `hey_vedic.json` to a public GitHub repo
2. In your ESPHome config, reference the raw JSON URL:
   ```yaml
   micro_wake_word:
     models:
       - model: "https://raw.githubusercontent.com/YOUR_USER/YOUR_REPO/main/deployment/hey_vedic.json"
   ```
3. Flash to your ReSpeaker Lite (XIAO ESP32-S3)

### Option 2: Local files (ESPHome add-on)

1. Copy both files to `/config/models/hey_vedic/` in your Home Assistant config
2. Use local path in config:
   ```yaml
   micro_wake_word:
     models:
       - model: "/config/models/hey_vedic/hey_vedic.json"
   ```

## Model details

- **Wake word**: Hey Vedic
- **Architecture**: MixedNet (streaming, stateful)
- **Input**: `[1, 3, 40]` int8 (3 frames x 40 mel bands)
- **Output**: `[1, 1]` uint8 (probability 0-255 mapped to 0.0-1.0)
- **Feature step**: 10 ms
- **Sliding window**: 5 frames
- **Probability cutoff**: 0.70
- **Tensor arena**: 30 KB
- **Training data**: Piper TTS (13 phonetic variations x 7500+ clips) + general negatives (speech, noise, ambient)

## tuning tips

- **False accepts too often**: Raise `probability_cutoff` (e.g., 0.80-0.90)
- **Not detecting reliably**: Lower `probability_cutoff` (e.g., 0.50-0.60)
- **Crashes on boot**: Increase `tensor_arena_size` (try 35000, 40000)
