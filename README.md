# G.722 encoder and decoder for Go

Pure Go (no cgo) ITU-T G.722 encoder and decoder supporting 64, 56 and 48
kbit/s, 16 kHz or 8 kHz linear PCM, and packed or unpacked codewords.

```sh
go get github.com/michelepra/g722
```

```go
enc := g722.NewEncoder(g722.Rate64000, 0)
dec := g722.NewDecoder(g722.Rate64000, 0)

payload := make([]byte, 160) // 20 ms at 64 kbit/s
pcm := make([]int16, 320)    // 20 ms at 16 kHz

n := enc.Encode(payload, frame) // frame: 320 samples
m := dec.Decode(pcm, payload[:n])
```

Encoder and Decoder are stateful: use one instance per stream and do not share
it between goroutines.

## Origin and fork

This is a fork of [gotranspile/g722](https://github.com/gotranspile/g722),
which translated [dgoncharov/g722tools](https://github.com/dgoncharov/g722tools)
from C to Go with [cxgo](https://github.com/gotranspile/cxgo) (see `cxgo.yml`).

The generated Go is now maintained as source. Changes compared to the upstream
translation:

- the 16 kHz QMF synthesis output is saturated to int16, as in the reference
  spandsp/libg722 decoder, instead of wrapping around
  ([gotranspile/g722#1](https://github.com/gotranspile/g722/issues/1)).

Keep these fixes if the code is ever regenerated with cxgo; `reference_test.go`
fails if the saturation is lost.

## Tests

`reference_test.go` checks encoder and decoder output against hashes produced
by [sippy/libg722](https://github.com/sippy/libg722), which passes the ITU test
vectors, for full-scale, square, noise and low-level inputs at all three rates.

## License

BSD license; see [LICENSE](LICENSE).

Copyright (c) 2010 Dmitry Goncharov
Distributed under the BSD license.

Parts of this software have the following copyright
Written by Steve Underwood <steveu@coppice.org>
Copyright (C) 2005 Steve Underwood

Despite my general liking of the GPL, I place my own contributions
to this code in the public domain for the benefit of all mankind -
even the slimy ones who might try to proprietize my work and use it
to my detriment.

Based on a single channel G.722 codec which is:

***    Copyright (c) CMU    1993      *****
Computer Science, Speech Group
Chengxiang Lu and Alex Hauptmann
