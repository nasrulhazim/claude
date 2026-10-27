# Toolkit: music and sound effects

`audio.py` synthesises a music bed and every sound effect from `<project>/timeline.json`, which is exported from the same timeline as the picture, so audio can't drift. It is seeded and procedural, so there is nothing to license.

| Music section | Starts at |
|---|---|
| Intro pad + arp | 0s |
| Beat (kick, hats, snap, bass) | Scene 2 start |
| Lift (brighter arp, open hats) | The scene marked `lift:true` |
| Resolve (Cmaj7, no beat, fade) | Last scene start (end card) |

SFX vocabulary: `pop key word open step select tick warn success chime stamp click whoosh draw blip logo final`.

<!-- file: scripts/audio.py -->
````python
"""Synthesize a music bed + SFX from <project>/timeline.json (exported from the same timeline as the picture).
Everything is procedural and seeded: re-running gives the identical mix, and there is nothing to license.
Music follows the timeline: intro pad, beat enters at scene 2, lifts at the scene marked lift:true,
resolves under the last scene (end card).
Usage: python3 audio.py <project_dir>  ->  <project_dir>/audio.wav
"""
import json, pathlib, sys
import numpy as np
from scipy.signal import butter, sosfilt
from scipy.io import wavfile

SR = 44100
PROJ = pathlib.Path(sys.argv[1] if len(sys.argv) > 1 else '.').resolve()
TL = json.loads((PROJ / 'timeline.json').read_text())
DUR = float(TL['dur'])
N = int(SR * DUR)
rng = np.random.default_rng(8)

def t_(d): return np.arange(int(SR * d)) / SR
def lp(x, f, o=2): return sosfilt(butter(o, f, 'low', fs=SR, output='sos'), x)
def hp(x, f, o=2): return sosfilt(butter(o, f, 'high', fs=SR, output='sos'), x)
def bp(x, lo, hi, o=2): return sosfilt(butter(o, [lo, hi], 'band', fs=SR, output='sos'), x)
def midi(m): return 440 * 2 ** ((m - 69) / 12)
def saw(f, t): return 2 * ((f * t) % 1) - 1

L = np.zeros(N); R = np.zeros(N)
def add(sig, at, gain=1.0, pan=0.0, buf=None):
    i = int(at * SR)
    if i >= N: return
    sig = sig[: N - i] * gain
    l, r = np.cos((pan + 1) * np.pi / 4), np.sin((pan + 1) * np.pi / 4)
    (buf or (L, R))[0][i:i + len(sig)] += sig * l
    (buf or (L, R))[1][i:i + len(sig)] += sig * r

# ------------------------------------------------------------------ music bed
BPM = 96; BEAT = 60 / BPM; BAR = 4 * BEAT
# Am9 · Fmaj7 · Cmaj7 · G6  (one chord per bar, loops every 4 bars)
CHORDS = [[57, 60, 64, 67, 71], [53, 57, 60, 64, 69], [48, 55, 59, 64, 67], [55, 59, 62, 64, 67]]
ROOTS = [45, 41, 48, 43]
SC = TL['scenes']
BEAT_IN = SC[1]['s'] if len(SC) > 1 else 2.0
OUT = SC[-1]['s'] if len(SC) > 2 else DUR - 3
LIFT = next((x['s'] for x in SC if x.get('lift')), DUR * 0.72)
mL = np.zeros(N); mR = np.zeros(N); mus = (mL, mR)

nbars = int(np.ceil(DUR / BAR))
for b in range(nbars):
    t0 = b * BAR; ch = CHORDS[b % 4]
    if t0 >= OUT: ch = [48, 55, 60, 64, 67, 71]  # resolve to Cmaj7 for the end card
    # pad: detuned saws, soft attack, low-passed
    d = BAR + 0.6; tt = t_(d)
    env = np.minimum(1, tt / 0.5) * np.minimum(1, (d - tt) / 0.6)
    pad = sum(saw(midi(m) * (1 + det), tt) for m in ch for det in (-0.004, 0.004)) / (len(ch) * 2)
    pad = lp(pad, 1400 if t0 < LIFT else 2200) * env
    add(pad, t0, 0.16, -0.3, mus); add(pad, t0 + 0.012, 0.16, 0.3, mus)
    if t0 >= OUT: continue
    # arp: plucked 8ths walking up the chord
    for s in range(8):
        at = t0 + s * BEAT / 2
        if at < 1.5: continue
        m = ch[s % len(ch)] + (12 if (t0 >= LIFT and s % 2) else 12 * (s >= 4))
        tt = t_(0.5); x = (np.sin(2 * np.pi * midi(m) * tt) + 0.3 * np.sin(4 * np.pi * midi(m) * tt)) * np.exp(-tt * 9)
        add(lp(x, 3500), at, 0.06 if at < BEAT_IN else 0.075, -0.5 if s % 2 else 0.5, mus)
    if t0 < BEAT_IN: continue
    # bass: root 8ths with short decay
    for s in range(8):
        tt = t_(BEAT / 2); f = midi(ROOTS[b % 4] - 12 + (12 if s % 4 == 3 else 0))
        x = (np.sin(2 * np.pi * f * tt) + 0.25 * lp(saw(f, tt), 600)) * np.exp(-tt * 5) * np.minimum(1, tt / 0.01)
        add(x, t0 + s * BEAT / 2, 0.22, 0, mus)

# sidechain-ish duck on kicks + drums
duck = np.ones(N)
for k in np.arange(BEAT_IN, OUT, BEAT):
    tt = t_(0.35); i = int(k * SR); seg = 1 - 0.45 * np.exp(-tt * 12); duck[i:i + len(seg)] = np.minimum(duck[i:i + len(seg)], seg)
mL *= duck; mR *= duck
for j, k in enumerate(np.arange(BEAT_IN, OUT, BEAT)):
    tt = t_(0.35); f = 45 + 80 * np.exp(-tt * 30)
    kick = np.sin(2 * np.pi * np.cumsum(f) / SR) * np.exp(-tt * 9)
    add(kick, k, 0.32, 0, mus)
    hat = hp(rng.standard_normal(int(SR * 0.04)), 7000) * np.exp(-t_(0.04) * (60 if k < LIFT else 30))
    add(hat, k + BEAT / 2, 0.07 if k < LIFT else 0.1, 0.2, mus)
    if j % 4 == 2:  # light snap on beat 3
        sn = bp(rng.standard_normal(int(SR * 0.12)), 1500, 6000) * np.exp(-t_(0.12) * 28)
        add(sn, k, 0.09, -0.1, mus)

fade = np.ones(N); fi = int(1.2 * SR); fade[:fi] = np.linspace(0, 1, fi)
fo = int(2.5 * SR); fade[-fo:] = np.linspace(1, 0, fo) ** 1.5
mL *= fade * 0.7; mR *= fade * 0.7

# ------------------------------------------------------------------ SFX
def bell(f, d=0.6, dec=6):
    tt = t_(d); return (np.sin(2*np.pi*f*tt) + .35*np.sin(2*np.pi*f*2.01*tt)*np.exp(-tt*4) + .15*np.sin(2*np.pi*f*3.02*tt)) * np.exp(-tt*dec) * np.minimum(1, tt/.003)
def sweep(f0, f1, d, dec):
    tt = t_(d); f = f0 * (f1 / f0) ** (tt / d); return np.sin(2*np.pi*np.cumsum(f)/SR) * np.exp(-tt*dec) * np.minimum(1, tt/.002)
def noise_sweep(d, lo, hi, gain_curve):
    n = rng.standard_normal(int(SR*d)); out = np.zeros_like(n); seg = int(SR*0.02)
    for i in range(0, len(n), seg):
        k = i/len(n); c = lo*(hi/lo)**k; out[i:i+seg] = bp(n[i:i+seg+256], c*.7, min(c*1.4, SR/2-100))[:len(out[i:i+seg])]
    return out * gain_curve(t_(d)/d)
PENT = [76, 79, 81, 83, 86, 88]

def sfx(kind, v):
    if kind == 'pop':    return sweep(520, 1150, .09, 38), .16
    if kind == 'key':    x = bp(rng.standard_normal(int(SR*.014)), 1800, 6500) * np.exp(-t_(.014)*260); return x, .07 * rng.uniform(.6, 1)
    if kind == 'word':   return bell(midi(PENT[rng.integers(len(PENT))]), .18, 26), .07
    if kind == 'open':   return noise_sweep(.22, 600, 3500, lambda k: np.sin(np.pi*k)), .11
    if kind == 'step':   return bell(1600, .04, 90), .07
    if kind == 'select': return np.concatenate([bell(midi(88), .09, 30)[:int(SR*.07)], bell(midi(95), .35, 9)]), .17
    if kind == 'tick':   return bell(2100, .05, 70) + .5*bell(3150, .05, 90), .06
    if kind == 'warn':   return bell(330, .35, 9) + bell(311, .35, 9), .12
    if kind == 'success':return np.concatenate([bell(midi(84), .08, 30)[:int(SR*.06)], bell(midi(91), .3, 10)]), .13
    if kind == 'chime':
        x = np.zeros(int(SR*1.1))
        for i, m in enumerate([84, 88, 91, 96]):
            b = bell(midi(m), .9, 5); o = int(i*.07*SR); x[o:o+len(b)] += b[:len(x)-o]
        return x, .11
    if kind == 'stamp':  tt = t_(.3); return np.sin(2*np.pi*np.cumsum(70+60*np.exp(-tt*40))/SR)*np.exp(-tt*14) + .3*lp(rng.standard_normal(len(tt)), 1200)*np.exp(-tt*40), .32
    if kind == 'click':  tt = t_(.06); return (np.sin(2*np.pi*220*tt)*np.exp(-tt*80) + .5*hp(rng.standard_normal(len(tt)), 3000)*np.exp(-tt*300)), .26
    if kind == 'whoosh': return noise_sweep(.55, 250, 4000, lambda k: np.sin(np.pi*k)**1.5), .2
    if kind == 'draw':   return noise_sweep(.35, 400, 2000, lambda k: np.sin(np.pi*k)), .06
    if kind == 'blip':   return bell(1300, .05, 60), .04
    if kind == 'logo':
        x = np.zeros(int(SR*1.6))
        for i, m in enumerate([72, 79, 84, 88, 91]):
            b = bell(midi(m), 1.4, 3.2); o = int(i*.09*SR); x[o:o+len(b)] += b[:len(x)-o]
        return x, .08
    if kind == 'final':
        tt = t_(2.8); boom = np.sin(2*np.pi*np.cumsum(40+30*np.exp(-tt*8))/SR)*np.exp(-tt*2.2)
        x = .9*boom
        for m in [60, 64, 67, 71, 76]: x += .25*bell(midi(m), 2.8, 1.3)
        return x, .22
    return None, 0

cues = TL['cues']
last = {}
for kind, at, v in cues:
    if kind == 'none': continue
    gap = {'key': .03, 'pop': .05, 'tick': .04, 'word': .05}.get(kind, .02)
    if at - last.get(kind, -9) < gap: continue
    last[kind] = at
    x, g = sfx(kind, v)
    if x is None: continue
    pan = float(rng.uniform(-.25, .25))
    add(x, at, g * v, pan)

# ------------------------------------------------------------------ mix
mix = np.stack([L + mL, R + mR], 1)
# gentle bus glue: soft clip, then normalise to -1 dBFS
mix = np.tanh(mix * 1.4) / np.tanh(1.4)
mix /= np.max(np.abs(mix)) / 10 ** (-1 / 20)
wavfile.write(PROJ / 'audio.wav', SR, (mix * 32767).astype(np.int16))
print('ok', mix.shape, f'{len(cues)} cues')
````
