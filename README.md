# To-33RPM

Python DSP pipeline that transforms an audio file as if a 45rpm-pressed track were played at 33rpm.

The effect applies both:
- Time stretch (slower playback)
- Pitch drop (lower musical key)

This is done by high-quality resampling while preserving the original sample rate and file encoding subtype when possible.

## Why This Works

If original content is intended for 45rpm and playback happens at 33rpm:

- Speed ratio = 33/45 = 0.7333...
- Duration ratio = 45/33 = 1.3636...

So output duration becomes ~36.36% longer and pitch is lowered by the same ratio.

## Processing Methods

- polyphase (default): band-limited FIR interpolation/decimation via `scipy.signal.resample_poly`
- fft: Fourier-domain resampling via `scipy.signal.resample`

`polyphase` is usually the best quality/speed tradeoff and is the recommended default.

## Install

```bash
pip install -r requirements.txt
```

## Usage

```bash
python main.py <input_audio> <output_audio> [--method polyphase|fft] [--no-normalize]
```

Examples:

```bash
python main.py input.wav output_33rpm.wav
python main.py input.flac output_33rpm.flac --method fft
```

## Project Structure

```text
main.py
requirements.txt
to33rpm/
	__init__.py
	cli.py
	io_audio.py
	processing.py
```

## Notes On Quality And Bitrate

- The pipeline keeps the same output sample rate as input.
- Audio data is processed as float32 for stable DSP behavior.
- Output container format/subtype is reused from input metadata where available.
- Duration increases, so total file size usually increases even if bitrate profile is preserved.