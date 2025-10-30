# Quick Start: Adding Media Assets

This guide helps you quickly add demo videos and screenshots to the CalCloudSpendIQ website.

---

## ⚡ Quick Steps

### 1️⃣ Add Demo Video (2 minutes)

```bash
# Navigate to the videos folder
cd assets/videos/

# Copy your demo video here and rename it
cp ~/path/to/your/demo.mp4 app-demo.mp4

# (Optional) Add a poster image
cp ~/path/to/thumbnail.jpg app-demo-poster.jpg
```

✅ **Done!** The video will automatically appear on the website.

---

### 2️⃣ Add Screenshots (5 minutes)

```bash
# Navigate to the screenshots folder
cd assets/screenshots/

# Copy your screenshots with correct names
cp ~/path/to/screen1.png dashboard.png
cp ~/path/to/screen2.png copilot.png
cp ~/path/to/screen3.png inventory.png
cp ~/path/to/screen4.png settings.png
cp ~/path/to/screen5.png breakdown.png
cp ~/path/to/screen6.png recommendations.png
```

✅ **Done!** The screenshots will automatically appear on the website.

---

## 📋 Required Files Checklist

### Video Files (at least 1 required)
- [ ] `assets/videos/app-demo.mp4` ⭐ **Required**
- [ ] `assets/videos/app-demo.webm` (Optional - for better compatibility)
- [ ] `assets/videos/app-demo-poster.jpg` (Optional - thumbnail)

### Screenshot Files (6 required)
- [ ] `assets/screenshots/dashboard.png` ⭐
- [ ] `assets/screenshots/copilot.png` ⭐
- [ ] `assets/screenshots/inventory.png` ⭐
- [ ] `assets/screenshots/settings.png` ⭐
- [ ] `assets/screenshots/breakdown.png` ⭐
- [ ] `assets/screenshots/recommendations.png` ⭐

---

## 🎯 What Each Screenshot Should Show

| Filename | What to Capture | Description |
|----------|----------------|-------------|
| **dashboard.png** | Main Dashboard | Cost analytics with charts, trends, and forecast |
| **copilot.png** | AI Copilot | Chat interface with a sample query and results |
| **inventory.png** | Resources | Table showing EC2, RDS, EBS resources across regions |
| **settings.png** | Settings Page | Credential management and configuration options |
| **breakdown.png** | Cost Charts | Service-wise cost breakdown visualization |
| **recommendations.png** | Suggestions | AI recommendations for cost optimization |

---

## 🚀 One-Line Copy Commands

If you have all your files ready in a source folder:

```bash
# Copy demo video
cp /source/folder/demo.mp4 assets/videos/app-demo.mp4

# Copy all screenshots at once (adjust paths as needed)
cp /source/folder/screen-dashboard.png assets/screenshots/dashboard.png
cp /source/folder/screen-copilot.png assets/screenshots/copilot.png
cp /source/folder/screen-inventory.png assets/screenshots/inventory.png
cp /source/folder/screen-settings.png assets/screenshots/settings.png
cp /source/folder/screen-breakdown.png assets/screenshots/breakdown.png
cp /source/folder/screen-recommendations.png assets/screenshots/recommendations.png
```

---

## 🎨 Image/Video Specifications

### Video Requirements
- **Format**: MP4 (H.264 codec)
- **Resolution**: 1920x1080 or 1280x720
- **Duration**: 1-3 minutes
- **Max Size**: 50MB

### Screenshot Requirements
- **Format**: PNG or JPG
- **Resolution**: 1200x900px (recommended)
- **Max Size**: 500KB per image
- **Aspect Ratio**: 4:3 or 16:10

---

## 🔧 Quick Optimization Commands

### Compress Video (using FFmpeg)
```bash
ffmpeg -i input.mp4 -c:v libx264 -crf 23 -preset medium -c:a aac -b:a 128k assets/videos/app-demo.mp4
```

### Compress PNG Images (using pngquant)
```bash
pngquant assets/screenshots/*.png --ext .png --force
```

### Compress JPG Images (using jpegoptim)
```bash
jpegoptim --max=85 assets/screenshots/*.jpg
```

---

## ✅ Test Your Website

After adding files:

1. **Open the website**
   ```bash
   # Simply open index.html in your browser
   open index.html  # macOS
   xdg-open index.html  # Linux
   start index.html  # Windows
   ```

2. **Check the Demo section**
   - Scroll to "See CalCloudSpendIQ App in Action"
   - Video should play with controls
   - All 6 screenshots should be visible (not icons)

3. **Test the Lightbox feature** 🆕
   - Click on any screenshot
   - Image should open in full-screen lightbox
   - Test navigation: Click left/right arrows or use keyboard arrows
   - Test closing: Click X, press ESC, or click outside image
   - Verify image counter shows (e.g., "1 / 6")

4. **Test on different browsers**
   - Chrome/Edge (Chromium)
   - Firefox
   - Safari (macOS)

---

## 🐛 Troubleshooting

### Video Not Showing?
1. Check filename is exactly: `app-demo.mp4` (lowercase)
2. Check file is in: `assets/videos/`
3. Try opening video file directly to test it plays
4. Check browser console for errors (F12 → Console)

### Screenshots Not Showing?
1. Check filenames match exactly (lowercase, correct spelling)
2. Check files are in: `assets/screenshots/`
3. Try opening image files directly to test they're valid
4. If icons show instead, files are missing or misnamed

### File Size Too Large?
1. Use compression commands above
2. Use online tools:
   - Video: HandBrake, CloudConvert
   - Images: TinyPNG, Squoosh

---

## 📚 Additional Resources

- **Detailed Video Specs**: See `assets/videos/README.md`
- **Detailed Screenshot Specs**: See `assets/screenshots/README.md`
- **Full Documentation**: See `WEBSITE_UPDATES.md`

---

## 💡 Pro Tips

1. **Batch Rename**: Use tools like `rename` command or Bulk Rename Utility to rename multiple files at once
2. **Automation**: Create a script to copy and rename files automatically
3. **Version Control**: Keep original high-quality files in a separate folder
4. **Cloud Backup**: Store source files in cloud storage (Google Drive, Dropbox) before compressing

---

## 🤝 Need Help?

If you encounter issues:
1. Check the README files in each asset folder
2. Review the full documentation in WEBSITE_UPDATES.md
3. Test files individually by opening them directly
4. Check browser console for specific error messages

---

**Last Updated**: January 30, 2025
