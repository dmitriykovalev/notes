# sox

From `max sox`:

> Use the -V option to see what processing SoX has automatically added. The -D option may be given to override automatic dithering.  To invoke dithering manually (e.g. to select a noise-shaping curve), see the dither effect.

Generate 1000 Hz sine wave for 1 second:
```shell
# sample_rate_hz=48000
sox -V -n -r 48000 -b 16 -c 1 sine-48k-1ch-1000ms.wav synth 1 sin 1000 vol 0.05
sox -V -n -r 48000 -b 16 -c 2 sine-48k-2ch-1000ms.wav synth 1 sin 1000 vol 0.05

# sample_rate_hz=16000
sox -V -n -r 16000 -b 16 -c 1 sine-16k-1ch-1000ms.wav synth 1 sin 1000 vol 0.05
sox -V -n -r 16000 -b 16 -c 2 sine-16k-2ch-1000ms.wav synth 1 sin 1000 vol 0.05
```

Generate 2 second stereo audio file. First second — 1000 Hz in left channel, second second — 2000 Hz in right channel:
```shell
sox -V -c 2 -n -r 48000 -c 2 -b 16 left-right-48k-2ch-2000ms.wav \
  synth 1 sin 1000 vol 0.05 remix 0 1 : \
  synth 1 sin 2000 vol 0.10 remix 2 0
```

Generate stereo files with zero channels using `-D` or `--no-dither`:
```
# sample_rate_hz=48000; left_hz=1000
sox -D -V -n -r 48000 -b 16 sine-48k-5000ms-L.wav synth 5 sine 1000 vol 0.1 remix 1 0

# sample_rate_hz=48000; right_hz=1000
sox -D -V -n -r 48000 -b 16 sine-48k-5000ms-R.wav synth 5 sine 1000 vol 0.1 remix 0 1
```
