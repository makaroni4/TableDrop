# How to generate app's icon

## Step 1: export SVG

Export app's icon to an SVG file with a transparent background.

IMPORTANT: keep recommended height from [the official icon template](https://www.figma.com/community/file/1040708197994685442/official-macos-app-icon-template).

## Step 2: generate PNG-s

```sh
SVG="icon.svg"
ASSETS="Assets.xcassets"
OUT="$ASSETS/AppIcon.appiconset"

rm -rf "$ASSETS"
mkdir -p "$OUT"

for PIXELS in 16 32 64 128 256 512 1024; do
  rsvg-convert \
    --keep-aspect-ratio \
    --background-color=none \
    --width="$PIXELS" \
    --height="$PIXELS" \
    --format=png \
    --output="$OUT/icon_${PIXELS}.png" \
    "$SVG"
done

cat > "$ASSETS/Contents.json" <<'JSON'
{
  "info": {
    "author": "xcode",
    "version": 1
  }
}
JSON

cat > "$OUT/Contents.json" <<'JSON'
{
  "images": [
    { "idiom": "mac", "size": "16x16",  "scale": "1x", "filename": "icon_16.png" },
    { "idiom": "mac", "size": "16x16",  "scale": "2x", "filename": "icon_32.png" },
    { "idiom": "mac", "size": "32x32",  "scale": "1x", "filename": "icon_32.png" },
    { "idiom": "mac", "size": "32x32",  "scale": "2x", "filename": "icon_64.png" },
    { "idiom": "mac", "size": "128x128", "scale": "1x", "filename": "icon_128.png" },
    { "idiom": "mac", "size": "128x128", "scale": "2x", "filename": "icon_256.png" },
    { "idiom": "mac", "size": "256x256", "scale": "1x", "filename": "icon_256.png" },
    { "idiom": "mac", "size": "256x256", "scale": "2x", "filename": "icon_512.png" },
    { "idiom": "mac", "size": "512x512", "scale": "1x", "filename": "icon_512.png" },
    { "idiom": "mac", "size": "512x512", "scale": "2x", "filename": "icon_1024.png" }
  ],
  "info": {
    "author": "xcode",
    "version": 1
  }
}
JSON

ls -lh "$OUT"/*.png
```

## Step 3: reload Dock app

If you still see an old icon after rebuilding the app, relaunch dock:

```sh
rm -rf ~/Library/Developer/Xcode/DerivedData

# Replace com.your.bundle.id with your real bundle identifier
rm -rf ~/Library/Caches/com.apple.iconservices.store
rm -rf ~/Library/Caches/com.apple.iconservices
rm -rf /var/folders/*/*/*/com.apple.iconservices 2>/dev/null

killall Dock
killall Finder
```
