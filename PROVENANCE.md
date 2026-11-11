# Arla Norway welcome loop: provenance

Built 11 Nov 2026 for the welcome page of the Arla Foods Norway Power BI prototype (butter and cheese, synthetic data demonstration). Made with the local OpenMontage toolkit (documentary-montage pipeline, `direct_clip_search` with the Pixabay Video source) and ffmpeg 7.

## Outputs

| File | Detail |
|---|---|
| Arla_Norway_Welcome_Loop.mp4 | 19.000 s (456 frames), 1280x720, H.264 High, yuv420p, 24 fps, about 1.48 Mbit/s, 3,565,053 bytes, one video stream and no audio track, faststart (moov before mdat), metadata stripped |
| Arla_Norway_Welcome_Still.jpg | Frame at 17.0 s (shot 6), 1280x720 JPEG, 67,354 bytes |

## Licence

All six clips come from Pixabay and are under the Pixabay Content License: free for commercial and non-commercial use, no attribution required, modification allowed.

- Licence summary: https://pixabay.com/service/license-summary/
- Full terms: https://pixabay.com/service/terms/

Points from the licence that apply here: the clips must not be sold or redistributed on their own unaltered, must not suggest endorsement by the people or brands shown, and must not be used in a misleading way. None of the clips shows a brand, logo, pack, retailer sign or text. No face is visible in any frame; shots 4 and 5 show hands only.

All clips were found through the Pixabay API and downloaded on 11 Nov 2026 (the API's largest rendition: 4K where offered, otherwise 1920x1080).

## Clips used, in playback order

| # | Pixabay page | Author (Pixabay user) | What it shows | Source file | In | Out | Position in loop |
|---|---|---|---|---|---|---|---|
| 1 | https://pixabay.com/videos/id-130866/ | Michi_aus_Hopsten | Drone view of sunrise over a Norwegian fjord: sun on the ridge line, water and islands to the left, dark hillside in silhouette (tags: mountains, sunrise, fjord, norway, drone) | 3840x2160, 25 fps, 66.9 s | 10.0 s | 15.0 s | 0.0 to 4.0 s, plus its first 1 s cross-fades in over 18.0 to 19.0 s to close the loop |
| 2 | https://pixabay.com/videos/id-85146/ | NickyPe | Brown and white dairy cows grazing in a green pasture, mid-wide, locked off | 3840x2160, 25 fps, 22.6 s | 6.0 s | 10.0 s | 3.0 to 7.0 s |
| 3 | https://pixabay.com/videos/id-308450/ | Solarselle77 | Wooden board on a dark rustic table with a blue cheese wedge, a washed rind round with a slice cut out, a hard cheese block, grapes and crackers, slow camera move | 3872x2160, 30 fps, 25.3 s | 3.0 s | 7.0 s | 6.0 to 10.0 s |
| 4 | https://pixabay.com/videos/id-63066/ | AlexKopeykin | Hands slicing a home-baked loaf with a bread knife on a dark wooden table | 3840x2160, 25 fps, 22.0 s | 9.0 s | 13.0 s | 9.0 to 13.0 s |
| 5 | https://pixabay.com/videos/id-170912/ | emredoganfilms | Hands cutting a wedge of white cheese with a knife and fork on a breakfast plate with olives, tomato, cucumber and a boiled egg | 1920x1080, 25 fps, 9.0 s | 2.0 s | 6.0 s | 12.0 to 16.0 s |
| 6 | https://pixabay.com/videos/id-112237/ | Jona02 | Slow drone move over a calm Norwegian fjord with a small wooded island, hills behind (tags: fjord, island, lake, norway, water, spring, calm) | 1920x1080, 29.97 fps, 21.5 s | 6.0 s | 10.0 s | 15.0 to 19.0 s, cross-fading into shot 1 |

Note on shot 3: its Pixabay tags include "photorealistic" and "4k" and the look is very styled. It may be computer-generated or AI-assisted, although Pixabay has not tagged it "ai generated". It is still under the Pixabay Content License.

## Edits (same for every clip)

1. Trimmed to the in and out points above.
2. Scaled to fill 1280x720 (Lanczos) and centre-cropped. No letterbox.
3. Converted to 24 fps by dropping frames (25 and 30 fps sources), with no speed change.
4. Audio removed.
5. Joined with 1 s cross-fades. The first 1 s of shot 1 is held back and cross-faded over the last 1 s of shot 6, so the last frame flows into the first. The final frame keeps about 4% of shot 6 mid-fade. In motion this is not visible.
6. One gentle grade across the whole timeline: contrast 1.04, saturation 0.90, gamma 1.02, slight colour balance (shadows a touch cooler and greener, highlights a touch warmer), black level lifted to 3% and white level held at 95%, a soft vignette and light temporal grain to unify the sources. No text, logos or captions were added.

## ffmpeg commands

The build script (`build_loop.sh`, kept privately) was run as:

```
STILL_T=17.0 bash build_loop.sh "<output folder>" \
  "pixabay_video_130866.mp4|10" "pixabay_video_85146.mp4|6" "pixabay_video_308450.mp4|3" \
  "pixabay_video_63066.mp4|9" "pixabay_video_170912.mp4|2" "pixabay_video_112237.mp4|6"
```

Per shot (shot 1 cut to 5 s, the others to 4 s):

```
ffmpeg -ss <in> -t <len> -i <source> -an \
  -vf "scale=1280:720:force_original_aspect_ratio=increase:flags=lanczos,crop=1280:720,setsar=1,fps=24,trim=end_frame=<len*24>,setpts=PTS-STARTPTS" \
  -c:v libx264 -preset slow -crf 12 -pix_fmt yuv420p s<n>.mp4
```

Join, loop and grade (two-pass, 1500 kbit/s, High profile, GOP 48, then `-movflags +faststart -map_metadata -1`):

```
N=settb=1/24,setpts=N,fps=24
[0:v]$N,split[a][b]; [a]trim=start_frame=24,$N[s1]; [b]trim=end_frame=24,$N[head];
[1:v]$N[i2]; ... [5:v]$N[i6];
[s1][i2]xfade=fade:duration=1:offset=3 ... [x5][i6]xfade=fade:duration=1:offset=15[x6];
[x6][head]xfade=fade:duration=1:offset=18,
eq=contrast=1.04:saturation=0.9:gamma=1.02,
colorbalance=rs=-0.02:gs=0.01:bs=0.01:rh=0.02:gh=0.01:bh=-0.02,
curves=all='0/0.03 0.5/0.5 1/0.95',vignette=angle=PI/6,noise=alls=3:allf=t,format=yuv420p
```

Poster still: `ffmpeg -ss 17.0 -i Arla_Norway_Welcome_Loop.mp4 -frames:v 1 -q:v 3 Arla_Norway_Welcome_Still.jpg`

## Shots considered and rejected

- https://pixabay.com/videos/id-27111/ (adege, misty valley with farmhouses, South Tyrol): it was the first choice for shot 1 because it shows farms at first light. I dropped it because it is a time-lapse, so the clouds move too fast for a calm loop.
- https://pixabay.com/videos/id-353010/ (misty village sunrise): Pixabay tags it "ai generated" and the village does not read as Norwegian.
- https://pixabay.com/videos/id-186709/ (spreading chocolate spread on toast): reads as a recognisable spread product, and it is not butter.
- https://pixabay.com/videos/id-156103/ (charcuterie board): a branded snack bag is visible.
- https://pixabay.com/videos/id-150333/ and id-150343/ (cartoon milk carton): animated character with text on the pack.
- https://pixabay.com/videos/id-12181/ (fjord from a ferry): the wake fills the lower third.
- Wikimedia Commons results from an earlier search: only CC0 or public domain clips were eligible, and neither fitted (a horse and US rangeland).
