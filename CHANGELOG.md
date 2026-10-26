# Changelog

## 2.6.1

### Fixed
- **Bright blue lights no longer get a saturated violet ring** (Lab). A blue LED (stage lights, team lights on a race car, fairy lights) is far outside what a screen can show, and RAW develop kept its glow at full saturation: a hard violet-blue ring between the white core and the scene. New RAW opens use a new develop (Accurate, rendering 5) that lets such a glow fade softly to white, as the camera's own JPEG does. Only blues that render near or above white change; blue paint, sky and dim blue lights keep their colour, and every other colour is exactly as before. Photos developed with 2.6.0 keep their look and show *Update to the new develop*.

## 2.6.0

### Fixed
- **AI denoise on the processor works** (Lab). With *Use GPU for AI tools* off (or no graphics card), AI denoise stayed at "Loading the denoise model…" forever; a tile redone on the processor after a GPU error could hang the same way. It now runs several tiles at once, one per core: about 2 minutes for a 24 MP photo on 4 cores. The result is the same as before on the GPU, and the same on every computer on the processor.
- **The window no longer stops answering when memory runs out** (Lab and Lite). Before a photo opens, Emulsion checks that it fits in the memory left (about 4 GB of an 8 GB computer); one that does not is not opened, and a message says how much it needs and how much is left.

### Faster
- **RAW files open faster** (Lab): the demosaic runs on all cores. A 24 MP RW2 opens in 2.6 s instead of 3.7 s on 4 cores, and the camera's preview shows at once while it develops.
- **RAW panel sliders show a preview while you drag** (Lab): several times a second, where 2.5.0 showed nothing until the pointer stopped. Each quick look takes half the time it did.
- Less memory while a RAW opens (about 170 MB less for 24 MP); the RAW cache and the undo history follow the computer's memory.
- Benchmark and golden check for RAW develops: `scripts/perf/` (docs/PERFORMANCE.md); develop output is byte-identical to 2.5.0.

## 2.5.0

### Fixed
- **Photos from a camera set to Adobe RGB no longer look dull** (Lab and Lite). Cameras in Adobe RGB mode (Lumix, Canon, Nikon, Sony) write no colour profile into their JPEGs, only a mark in the camera info; Emulsion read those colours as sRGB, so reds and greens came out up to 6 dE2000 too weak. Now the mark is read and the photo converted, the way Photoshop and Lightroom show it: when opening such a JPEG, and in Lab's Library for thumbnails, the loupe and compare view, the camera previews inside RAW files, the JPEG of a RAW+JPG pair, batch export and merges. DNGs whose previews are Adobe RGB (PreviewColorSpace) are converted too. A RAW's own develop never depended on this setting and is unchanged. Only files with no profile whose camera info says Adobe RGB are converted; everything else opens exactly as before.
- **Exports never say Adobe RGB beside their sRGB profile.** A photo from a camera set to Adobe RGB kept the camera's Adobe RGB mark (Interop R03) in its exported EXIF, next to the embedded sRGB profile; it is now set to sRGB (R98), and the EXIF colour space is set to sRGB whatever form the camera stored it in.
- **A damaged or unsupported colour profile is reported.** The photo opens as sRGB with a message that its profile could not be used, instead of silently.

### New
- **Open TIFF files** (Lab): 8 and 16 bit, grey or RGB, with transparency; uncompressed, LZW, ZIP or PackBits; strips or tiles. The embedded colour profile (Adobe RGB, Display P3, ProPhoto, ...) is applied in full 16-bit precision before the photo becomes 8-bit sRGB. Works from Open, drag and drop, Recent, the Library (thumbnails, loupe, batch export) and merges. TIFFs with JPEG compression, 32-bit floating point, CMYK or indexed colour are refused with a message that says what to change. Lite says TIFFs open in Lab.
- **PNG exports carry the sRGB profile (iCCP) and are smaller.** A picture with no transparent pixels is written as 8-bit RGB instead of RGBA: the same pixels, about 30% smaller on our test pictures. Pictures with transparency keep it.
- User guide: a **Colour and export** section.

## 2.4.0

### Fixed
- **A .cube file with a LUT size of 1, or with an equal DOMAIN_MIN and DOMAIN_MAX, is refused with a message** instead of being loaded and then rendering wrong colours.

### New
- **My LUTs: import your .cube LUTs once, then pick them from a list** (Lab and Lite). In the Look panel: import one or several .cube files (button or drag and drop); each is kept in Emulsion's own storage and listed every time, with its name, size and a small preview on your photo. One click applies it; search, rename and delete. The same LUT is never imported twice (the table is compared, not the name). 3D LUTs up to 65 points and 1D LUTs work; damaged or unsupported files are refused with a plain message. After *Load .cube file* the panel offers *Add to My LUTs*. Lab also has *Save .cube* (writes the LUT back exactly), *Open LUT folder*, and a watched folder whose .cube files are listed read only. Projects are unchanged: a Look layer keeps its own copy of its LUT, so deleting or renaming one in My LUTs never breaks a project.
- **Nikon High Efficiency NEF files open through Adobe DNG Converter** (Lab). Where Emulsion says it cannot read a RAW (Nikon HE / HE* NEFs, and any other RAW format it does not support), it now offers **Convert and open**, using your own installation of Adobe's free DNG Converter (Emulsion never downloads or bundles it). A copy is converted to a lossless DNG that keeps the camera's mosaic, then opened and developed like any other RAW; the original is not changed. Works from Open, drag and drop of a file opened from disk, Recent, the Library (also **Convert N files** for a selection, one after another with progress and Cancel) and the Edit Queue. Not installed: the message says where to get it, and **Locate converter** lets you point Lab at it once. Converted files are kept in Lab's cache (at most 5 GB, oldest first; **Clear converted files** in About), batch export uses them, and the photo layer says it was converted from the NEF. A conversion counts only when Lab can open the DNG itself; otherwise it says *Adobe DNG Converter could not convert this file (its version may not support this camera; update it)*.

## 2.3.0

### New
- **Highlight reconstruction and smooth film-like highlight roll-off for RAW files** (Lab, RAW develop v4, the new *Accurate*). Bright skin in sunlight, white clothes and other highlights no longer end in a flat white patch with a hard edge and a pink rim:
  - **Clipped channels are rebuilt.** Where the sensor clipped one or two colour channels (green first, on most frames), they are rebuilt from the channels that still recorded, with the colour of the unclipped pixels around them, so the highlight keeps its shading and texture and gets no magenta or pink cast. Only where all three channels clipped is it plain white.
  - **Highlights roll off like film.** Brightness is compressed by a smooth shoulder on luminance that brings the brightest part of each photo to white with no step, keeping hue; colours fade to white only near the very top. *Highlight roll-off* (Tight / Standard / Filmic) sets where the shoulder starts, *Highlight recovery* how much of the headroom is brought under white.
  - Everything darker than the shoulder's start renders exactly as before. The quick preview and the full-size develop use the same shoulder.
  - Photos developed with 2.2's Accurate keep their look and offer *Update to the new develop* (one undo step).

### Fixed
- **Emulsion Lite builds again.** The web edition's build stopped on a few lines of Lab's AI denoise code that had found their way into it; they are gone, along with a small piece of Lab's TIFF export that Lite had been carrying unused. Nothing changes for either edition: Lab's AI denoise gives exactly the same result.

## 2.2.0

### New
- **RAW develop v2: Accurate rendering** (Lab). RAW photos now open with a new develop that matches Adobe Camera Raw's brightness and keeps the scene's colours:
  - **Mid-tones** are no longer about 0.4 stops brighter than Camera Raw. The tone curve is fitted to Camera Raw (Adobe Standard) on Lumix files; the shadows are unchanged.
  - **Lights keep their colour and shape.** A light with only one channel clipped (a blue LED, a red tail light) stays coloured with a bright core instead of turning into a flat white blob.
  - **Colours keep their hue and saturation.** The curve is applied to the brightest channel with the colour ratios kept, instead of to each channel, which made colours more saturated and shifted their hue (blues by up to 10°).
  - Highlight recovery starts at 100, so everything the sensor recorded fits under white.
- **Lights roll off softly, like film** (RAW develop v3, Lab). With Accurate, a coloured light (LED, tail light, sign) no longer stays at full colour until it suddenly turns into a flat white disc with a hard coloured rim: it walks to white over a few stops, the way Camera Raw and film do. On two night race frames the colour error inside the lights against Camera Raw roughly halves (mean dE00 12.4 → 6.4 and 8.6 → 4.9); the rest of the photo is unchanged, and the clipped core of a light stays pure white (nothing is invented there).
  - **Highlight roll-off**: *Tight* (starts close to the clip), *Standard* (the default, matched to Camera Raw) or *Filmic* (starts earlier, wider transition), and anything in between. Everything darker than where the roll-off starts renders exactly as before.
  - **Warm highlights** (off by default): the colour a light gives up on its way to white turns warm, as on film; a blue light stays blue-white.
  - **Colour noise** (25 by default): removes the coloured speckle of high-ISO RAWs and keeps real colour edges; brightness, and so the grain, is untouched. On ISO 5000-10000 frames it takes colour noise down to Camera Raw's level.
- **Rendering** in the RAW develop panel shows how the photo was developed. Every RAW opened now is *Accurate*; the previous look (*Camera-like*, matched to the camera's JPEG) can no longer be picked. A photo developed with an older rendering shows its name and offers *Update to the new develop* (one undo step).
- **AI denoise: a new model, trained on Lumix noise** (Lab). AI denoise now uses one network, fine-tuned from the previous one (NAFNet-SIDD-width32) on Lumix sensor noise developed the way Lab develops RAWs. Dark areas come out much cleaner: on our ISO 5000-12800 night frames the flat-area noise left is about 0.32-0.38 levels, against 0.62-2.3 with the previous model. No tile of our test frames needs the dark-area retry any more. It works on other cameras' files too (tried on Sony, Fujifilm and Canon RAWs). Known costs: the result is smoother (flat areas can look a little plastic; lower Strength to blend some grain back), and saturated yellows lose a little saturation. A denoised layer made with the previous model keeps its pixels and says so (*Made with an earlier denoise model; Denoise again uses the new one*); Denoise again redoes it with the new model. The installer stays the same size: the new model replaces the old one and is the same size.
- **AI denoise gives the edges back** (Lab). After the network, edges are restored from the photo and lightly sharpened, so a denoised photo keeps its detail (on our test frames slightly more than before denoising) while flat areas stay as clean. Strength works as before.
- **Halation** (Lab): a new adjustment that adds a film-like glow around bright lights, a red-orange fringe close to the light and a wider haze (Strength, Radius, Threshold, Warmth). It looks the same at any photo size and never pushes anything past white. In Lite the tile is shown greyed as an Emulsion Lab feature; a Lab project with a Halation layer opens in Lite with the layer kept (not shown) and saves back intact.
- **Copy edits carries RAW settings.** Copying the edits of a RAW photo also copies its white balance, exposure, highlight recovery, lens corrections and rendering. Pasting onto another RAW develops it with them, in the same undo step as the pasted layers. In the Library they are stored with each RAW and used when it is opened or batch exported.

### Changed
- **Auto tone keeps dark scenes dark.** Auto in Light used to push every photo to a medium brightness: on a night race frame it added 2 stops and a big shadow lift, so the background turned grey, the noise showed and headlights and walls clipped to white. Now:
  - A dark scene stays dark (it changes very little, with a message saying why); a normal scene comes most of the way to a medium brightness; a bright one stays bright.
  - Exposure goes up by at most 1 stop, and less from ISO 800 (a quarter stop from ISO 3200); the photo's ISO is read from its camera info (unknown: ISO 800). Blacks are never lifted, and shadows are not lifted on high-ISO photos.
  - Lights close to white in a dim scene stop any exposure lift, and a check runs the real sliders so neither the photo nor its lights clip more than a little.
  - Auto measures only where the layer applies: through its mask or brightness / colour limit, or inside the selection. Under about 1% of the photo it leaves the sliders alone.
- **Auto white balance** only corrects a clear colour cast. Before, it cooled correctly balanced daylight photos by the full 25 nearly every time, because sunlit scenes, grass and warm light read as a cast. Now a photo whose colour is already right stays as shot (on our test frames Temperature and Tint stay within 2 of zero), while a strong cast is still corrected. It leaves night and stage scenes as shot, and changes Temperature by at most 25 and Tint by at most 15. Auto tone never changes white balance.

### Fixed
- **AI denoise: a striped block in dark areas** (Lab). On some high-ISO night frames, a very dark part of the photo came out as a rectangle of red, blue and purple stripes, and very underexposed photos could be striped all over. The denoise network itself goes wrong on such dark tiles, on the GPU and the processor alike. Every tile is now checked. A bad one is run again with its brightness range lifted for the network and put back afterwards, which fixes these tiles, and then on the processor. Only if it is still wrong does that part keep the photo as it was, with a message when the denoise finishes. Edge restore and sharpening never touch such a part. Which tile was rejected, and why, is written to the developer console.
- **Use GPU for AI tools** (Lab): a new option under the AI denoise button, on by default. Untick it to run every AI tool on the processor, for a graphics driver that gives wrong results. It is kept on this computer.

### Compatibility
- Projects saved before 2.2.0 open exactly as saved. Their RAW layers stay Camera-like when you change a setting, and the panel offers *Update to the new develop*.
- RAW layers made with the first Accurate develop (2.2.0 test builds) keep it, byte for byte; *Update to the new develop* moves them to the soft roll-off. AI denoise layers made before keep their pixels until *Denoise again*.
- HDR and panorama merges are unchanged (they use the Camera-like develop).

### Known limitation
- **Nikon High Efficiency RAW (HE and HE*) cannot be opened yet** (Lab). The Z 9, Z 8, Z f and Z6III can record it, and LibRaw, which Lab reads RAW files with, does not decode it. Before, such a file failed with a technical error; now Lab says what it is and what to do: *This Nikon file uses High Efficiency (HE or HE*) RAW compression, which Emulsion cannot read yet. Set the camera to Lossless Compressed RAW, or convert the file to DNG with Adobe's free DNG Converter first.* The same message shows wherever a RAW is opened (Open, drag and drop, Recent, Library, Edit Queue); batch export skips such files and counts them; the Library still shows the preview, marked *Can't edit: Nikon HE*. Lossless, Compressed and Uncompressed NEFs are not affected. Any other RAW format that cannot be read now says *This RAW format is not supported yet*, with the camera's name.

## 2.1.0

### New
- **Merge selected.** In the Layers list, Ctrl+click adds a layer to the selection or takes it out, and Shift+click selects a range. Selected rows are tinted; a plain click selects one layer as before.
  - *Merge selected* (next to *Merge visible*, or **Ctrl+E**) turns two or more selected layers into one layer, with their opacity, blend modes and masks. It takes the place and name of the topmost one.
  - Hidden layers in the selection are left as they are. An adjustment layer in the selection is baked into the selected layers under it only.
  - Refused, with a message: layers of an applied preset, and an adjustment with no selected layer under it.
  - When other layers sit between or under the selected ones, a one-time note says the result may look a little different.
  - One undo step: undo puts all the layers back and selects them again. Large photos merge a band at a time to keep memory down.

### Fixed
- **Undone selection came back.** After Paint + on a selection and Undo, the next Paint + / Paint − stroke brought the undone part back. The refine brush now always starts from the selection as it is; this also covers Redo, History jumps and a moved or transformed selection.
- **Gradients adjusted the whole photo.** With the photo selected, the Gradient tool faded the photo's own mask, and the sliders moved next changed the whole photo. Now it adds a *Linear gradient* / *Radial gradient* layer (Light & color) whose sliders change the photo only through the gradient (Feather and Flip as before; *Edit mask* still fades the photo itself). A gradient layer is always edited as it is, even with a selection active. Existing projects open and render as before.

## 2.0.0

### New
- **Library: select several photos** (Lab). Use Ctrl+click, Shift+click for a range, Ctrl+A for all and Esc to clear. A bar shows how many are selected.
- **Edit Selected** opens the selected photos in tabs. The first opens right away; the others load when you first click their tab, so a big batch doesn't fill memory. Photos already open are not opened twice.
- **Edit Queue** (Lab): press **A**, or click *Add to Edit Queue*, to put photos aside while you cull.
  - Queued photos show a *Queued* badge.
  - *Edit Queue (n)* lists them, with a remove button for each, plus *Open all*.
  - The queue is saved with the Library.
  - Photos over the open-tab limit stay queued, and a message says how many.
- **Export progress and Cancel** for every format and for Library batch export. A cancelled export writes no file.
- **Splash** (Lab): the start-up screen with the credits shows only on the first start after an install or update. The credits are always in About.

### Performance
- **Export memory:** large photos render in horizontal bands, with the same pixels as before, so a 45 MP export with denoise or dehaze no longer needs several GB.
- **Undo memory:** history also counts the full-size canvases it alone keeps (crops, merges, replaced layers) toward its 500 MB cap.
- **Autosave and Save** re-encode only the layers that changed since the last save.
- **RAW files** are read by Lab directly instead of passing through the page. The embedded preview comes from the same decode, and Library batch export reads the next photo while the current one is written.
- **Spin blur:** works on just the wheel's area instead of copying the whole frame (about 720 MB less memory at 45 MP), and is about 2.5× faster.
- **Smaller installer:** the AI denoise and AI fill weights are stored at half precision and run at full precision. About 70 MB less, with results within 1 level of before.
- **AI on the CPU** (computers without a usable GPU) runs on several cores in the background, so the window stays responsive while it works.
