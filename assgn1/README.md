# Assignment 1: Lossless Video Compression

## Approach
I kept this encoder fully lossless and pixel-by-pixel, but changed the model so it predicts each pixel from nearby information instead of only using a simple frame difference.

For each pixel, I make a prediction using:
- the pixel to the left,
- the pixel above,
- and the same pixel location in the previous frame.

Then I encode the prediction error (wrapped to 0–255) with arithmetic coding.

I use 256 total contexts. The context is based on:
- how different `left` and `up` are (spatial activity), and
- how different the previous-frame pixel is from the current prediction (temporal activity).

This lets smooth areas, edges, and motion-heavy regions learn different probability distributions.

## Why this helps
A single global model mixes all kinds of pixels together. Splitting into 256 contexts gives a better match between local behavior and the symbol probabilities, so compression improves.

## Lossless verification
I verified correctness with `-check_decode`.  
All checked frames decoded exactly (`correct.` output for each frame).

## Results on `bourne.mp4`
- `count=10`: average size `3,985,429` bits/frame, compression ratio `4.16`
- `count=30`: average size `4,176,788` bits/frame, compression ratio `3.97`

## How to run
```bash
cargo run -p assgn1 -- -count 30 -in data/bourne.mp4 -out data/out.dat
cargo run -p assgn1 -- -check_decode -count 30 -in data/bourne.mp4 -out data/out.dat
