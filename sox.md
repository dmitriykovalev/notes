# sox


Generate 1000 Hz sine wave for 1 second:
```shell
# Sample rate: 48000 Hz
sox -V -n -r 48000 -b 16 -c 1 sine-48k-1ch-1000ms.wav synth 1 sin 1000 vol 0.05
sox -V -n -r 48000 -b 16 -c 2 sine-48k-2ch-1000ms.wav synth 1 sin 1000 vol 0.05

# Sample rate: 16000 Hz
sox -V -n -r 16000 -b 16 -c 1 sine-16k-1ch-1000ms.wav synth 1 sin 1000 vol 0.05
sox -V -n -r 16000 -b 16 -c 2 sine-16k-2ch-1000ms.wav synth 1 sin 1000 vol 0.05
```

Generate 2 second stereo audio file. First second — 1000 Hz in left channel, second second — 2000 Hz in right channel:
```shell
sox -V -c 2 -n -r 48000 -c 2 -b 16 left-right-48k-2ch-2000ms.wav \
  synth 1 sin 1000 vol 0.05 remix 0 1 : \
  synth 1 sin 2000 vol 0.10 remix 2 0
```
