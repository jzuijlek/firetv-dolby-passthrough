Fire TV Dolby Digital & DD+ Passthrough v0.3.1

v0.3.1 restores HDMI bypass when Fire OS clears it during a refresh-rate change.
The rest of this package, including the Karat aparam overlay, is signde's v0.3.0.

Supports Fire TV Cube 3 (gazelle) and Fire TV Stick 4K Max 2 (karat).
Use Best Available in Fire OS audio settings and enable passthrough in your player.
DD and DD+ bypass, including DD+ Atmos, were playback-tested by the user on
Gazelle PS7702/4965 and Karat RS8182.3811N. App and receiver support still apply.
This does not add DTS-HD/DTS:X passthrough.

Install the ZIP in Magisk and reboot. No TWRP installation is needed.
The legacy ID gazelle_ddplus_bypass is intentionally retained so this replaces
the earlier Cube-only module when installed on a Cube. The display name is unified.

After sys.boot_completed=1, wait 60 seconds for Fire OS audio preferences, then
set hdmi_format=6 and follow up at +5 and +20 seconds. Then block on one logcat
stream for power_screen_state, AudioOutputFormatManager, and the HDMI open-failure
lines. A screen-on/resume event, or Fire OS writing hdmi_format=5 during a
refresh-rate change, triggers another bounded set of checks at 0, 5 and 20
seconds. No periodic HDMI queries while idle and no wake lock. This also responds
when exiting dreams if Fire OS emits the screen-on event. If that display reset
also fails to open the HDMI output and no audio output is active, the audio HAL
is restarted once and bypass is applied again. A latch blocks another restart for
180 seconds so one playback cannot loop it. An open failure while an output is
already active does not restart the HAL. Log-stream failures reconnect after 30
seconds. A replayed open-failure line when the listener reconnects does not
restart the HAL.
Disable/remove stops writes at the next event/check; reboot also ends the listener.
Manual changes to another HDMI mode can be overridden at boot/resume.
The service verifies the live HAL readback, not only a saved property.

Gazelle uses its original /system/bin/aparam.
Karat receives a Magisk overlay at /system/bin/aparam with the unused direct
libmediaplayerservice.so dependency removed. This prevents the observed exit-time
ALooperRoster mutex destruction crash without altering aparam's command code.
The installer accepts only the known stock binary or this exact corrected binary.
The Karat service also checks the corrected binary hash before using it.

Stock Karat aparam SHA256:
1ec08b6cdf633a683ace6123991b2b5b440f155804cd24b8081de287ad7e41b7
Corrected Karat aparam SHA256:
690cfd8c33e3c02d68c7e0d1c51415530907bcf41f1cf8784186ba17c897e4f2

The system partition is not modified. No SELinux or ADB changes are included.
Disable/remove the module and reboot to remove the overlay and startup setting.
Disable before firmware upgrades; re-check utility compatibility afterward.

Verify from a root shell:
aparam get 0 hdmi_format
Expected after the startup delay: hdmi_format=6, with no Karat abort.

Log: /data/adb/modules/gazelle_ddplus_bypass/boot.log
Previous run: /data/adb/modules/gazelle_ddplus_bypass/previous-boot.log
Current-session rotation: boot.log.1 (logs rotate at approximately 64 KiB)

For manual immediate bypass: aparam set 0 hdmi_format=6
For manual default mode: aparam set 0 hdmi_format=5
Disable the module first before intentionally selecting another mode.
