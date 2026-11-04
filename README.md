# Fire TV Dolby Digital & DD+ Passthrough v0.3.1

Magisk module that keeps HDMI bypass (`hdmi_format=6`) on the Fire TV Cube 3 (`gazelle`) and the Fire TV Stick 4K Max 2nd gen (`karat`). Bypass leaves Dolby Digital and DD+, including DD+ Atmos, as the original bitstream instead of letting Fire OS convert them to Dolby MAT.

v0.3.0 was posted by [signde](https://xdaforums.com/m/signde.654514/) on the [Cube unlock thread](https://xdaforums.com/t/unlock-magisk-twrp-firetv-3nd-gen-cube-gazelle-ps7717.4800530/) as an XDA attachment. It was not a GitHub release. Later DTS and Fire TV AV packages moved to [signde/firetv-passthrough](https://github.com/signde/firetv-passthrough). This repository publishes the Dolby-only module the same way those later packages are published: source here, installable zip on the release.

v0.3.1 is that v0.3.0 package with one added recovery. Fire OS writes `hdmi_format=5` when the refresh rate changes, and v0.3.0 only put bypass back at boot and when the screen wakes. v0.3.1 also puts it back after that write. If the same change fails to open the HDMI output and nothing is playing, it restarts the audio HAL once and applies bypass again. A latch blocks another restart for 180 seconds.

The Karat `aparam` overlay is the unmodified v0.3.0 file. It is not rebuilt here.

Do not install this beside [Fire TV AV](https://github.com/signde/firetv-passthrough). That package has its own bypass keeper. This zip replaces Magisk module id `gazelle_ddplus_bypass` only. It does not add DTS-HD or DTS:X.

## Install

Root and Magisk are required. Set Fire OS audio to **Best Available** and enable passthrough in the player. Install `firetv-dolby-passthrough-v0.3.1.zip` from [Releases](https://github.com/jzuijlek/firetv-dolby-passthrough/releases) in Magisk and reboot. Allow about a minute after boot. Disable or remove the module and reboot to undo it. Disable it before a firmware update and check that `/system/bin/aparam` is still one of the two hashes below.

From a root shell, after the startup delay:

```sh
aparam get 0 hdmi_format
```

Expected readback is `hdmi_format=6`. The log is `/data/adb/modules/gazelle_ddplus_bypass/boot.log`.

## Checked

On a Stick 4K Max 2, build `RS8185.3891N`, boot left the HAL at mode 5 and the module set mode 6. Writing mode 5 again produced a `DisplayReset` in `boot.log` and the readback returned to 6. A PCM tone then reached the HDMI mixer. Kodi's own IEC packer is unchanged by this module.

Signde's v0.3.0 notes still apply to the rest of the package: DD and DD+ bypass, including DD+ Atmos, were playback-tested on Cube 3 `PS7702/4965` and Stick `RS8182.3811N`.

## Contents

`service.sh` is the late-start keeper. `customize.sh` checks the Karat `aparam` hash and installs the overlay. `payload/aparam-karat` is the corrected utility.

Stock Karat `aparam` SHA256:

`1ec08b6cdf633a683ace6123991b2b5b440f155804cd24b8081de287ad7e41b7`

Corrected Karat `aparam` SHA256:

`690cfd8c33e3c02d68c7e0d1c51415530907bcf41f1cf8784186ba17c897e4f2`

The system partition is not modified.

## Rebuild the zip

```sh
zip -X -r firetv-dolby-passthrough-v0.3.1.zip \
  README.txt customize.sh module.prop service.sh payload/aparam-karat
```

## Notice

The shell and the module text are derived from signde's v0.3.0 zip. His later public tree describes the original audio code as MIT. The v0.3.1 changes in `service.sh` are released under MIT. See `LICENSE`. The `aparam` payload is the file from his v0.3.0 zip and is not relicensed here.
