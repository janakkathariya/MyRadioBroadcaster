# My Radio Broadcaster — SHOUTcast v1 starter

This Android Studio/Kotlin project implements a prototype live broadcaster with:
- SHOUTcast v1 legacy source handshake (password line, waits for `OK2`)
- MP3 encoding using the TAndroidLame/LAME library
- microphone capture at 44.1 kHz mono
- a user-selected audio file decoded via Android `MediaExtractor`/`MediaCodec`
- a basic PCM mixer (mic gain 0.72 + music gain 0.62, clipped to 16-bit)
- foreground service for ongoing broadcast

## Important: not a verified release APK
The ZIP is source code, not a compiled or device-tested APK. Build and test it in Android Studio against the exact radio provider. The TAndroidLame dependency is fetched from JitPack, so Gradle sync needs internet access. The dependency is GPL-3.0; review license obligations before distributing a closed-source app.

## Build
1. Install Android Studio and Android SDK 35.
2. Open the extracted project folder.
3. Sync Gradle (internet is needed to fetch dependencies).
4. Run on a physical Android phone and grant microphone permission.
5. For APK: **Build → Build Bundle(s) / APK(s) → Build APK(s)**.
6. Output normally appears under `app/build/outputs/apk/debug/`.

## SHOUTcast v1 settings
- **Host**: the server hostname, without `http://` or a path.
- **Source port**: often SHOUTcast portbase + 1 for legacy source connections. Some newer DNAS setups accept source connections on the base port. Use the source port specified by your hosting panel/provider.
- **Password**: source/broadcast password, not the admin password.
- SHOUTcast v1 normally does not use a mount point. The app emits MP3 at 128 kbps / 44.1 kHz mono.

## Mixing notes
Select an audio file with **SELECT MUSIC FILE** before starting. The app attempts to decode supported local audio using Android media codecs, converts decoded PCM to mono 44.1 kHz, then mixes it with microphone PCM before MP3 encoding. Supported file formats depend on the phone's Android codecs. This is a basic mixer, not a studio-grade DSP chain; music can sound quieter/louder depending on the source, and mic monitoring may cause feedback if played through speakers. Use headphones.

## Troubleshooting
- `SHOUTcast source rejected connection`: verify host, source port, and source password. Ensure another encoder is not already connected.
- Connection timeout: confirm the source port is externally reachable and the provider allows external source encoders.
- No music: choose a supported local audio file and check Logcat for `MyRadio` decoder errors.
- Build/dependency failure: sync Gradle with internet access; JitPack availability can vary.
- Android may stop the service if battery optimization is aggressive; test with screen off and adjust device battery settings if necessary.

## Security
Do not share your source password. Use a secure password and only use the broadcaster on a trusted network. This starter does not yet include TLS source connections, automatic reconnect, visible error reporting in the UI, music playlist controls, or a production-grade foreground-service stop notification.
