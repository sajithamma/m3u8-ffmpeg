# m3u8-ffmpeg

Download the m3u8 using ffmpeg
Eg: ffmpeg -i "https://live.licdn.com/bitmovinwus2prod/bitmovinwus2prodoutputstoragecontainer/f8f95323-0327-449d-9a87-fa5010f273a5/RtmpLiveEncoding/audio_0.m3u8" -c copy output_audio.mp4

Convert to Audio only if needed:
ffmpeg -i output_audio.mp4 -q:a 0 -map a output_audio.mp3

## Run the code to convert it into transcript

```shell
pip install whisper pydub
```

```python

import whisper
import os
from pydub import AudioSegment

# Load Whisper model
model = whisper.load_model("small")

# Input audio file
audio_file = "output_audio.mp3"
output_file = "transcript.txt"

# Split audio into 30-second chunks
chunk_length_ms = 30 * 1000  # 30 seconds
audio = AudioSegment.from_file(audio_file)

chunks = [audio[i:i + chunk_length_ms] for i in range(0, len(audio), chunk_length_ms)]

# Ensure transcript.txt is empty before writing
open(output_file, "w").close()

# Process each chunk
for i, chunk in enumerate(chunks):
    chunk_filename = f"chunk_{i}.mp3"
    chunk.export(chunk_filename, format="mp3")  # Save chunk as temporary file

    # Transcribe the chunk
    result = model.transcribe(chunk_filename, language="en")
    text = result["text"]

    # Write & print in real-time
    with open(output_file, "a") as f:
        f.write(text + "\n")
        f.flush()  # Update file immediately

    print(text, flush=True)  # Print in real-time

    # Clean up chunk file
    os.remove(chunk_filename)

```
