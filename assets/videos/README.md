# Demo Videos Directory

Place your product demo videos in this directory.

## Required Video File

**Filename:** `app-demo.mp4` (or `app-demo.webm`)

### Video Specifications

- **Format**: MP4 (H.264) or WebM recommended
- **Resolution**: 1920x1080 (Full HD) or 1280x720 (HD)
- **Aspect Ratio**: 16:9
- **Duration**: 1-3 minutes recommended
- **File Size**: Keep under 50MB for web performance
- **Audio**: Include audio track or captions

### Content Suggestions

Your demo video should showcase:
1. **Dashboard Overview** (15-20 seconds)
   - Cost analytics charts
   - Monthly trends
   - Forecast visualization

2. **AI FinOps Copilot** (30-45 seconds)
   - Natural language query examples
   - "Show me EC2 instances costing over $100"
   - Results display and interpretation

3. **Resource Inventory** (20-30 seconds)
   - Multi-region view
   - Filter and search functionality
   - Cost attribution per resource

4. **Settings & Configuration** (15-20 seconds)
   - Easy credential setup
   - Region selection
   - Mock data mode

5. **Call to Action** (10-15 seconds)
   - Live demo link
   - Contact information

### Video Compression Tips

Use tools like:
- **FFmpeg**: `ffmpeg -i input.mp4 -c:v libx264 -crf 23 -preset medium -c:a aac -b:a 128k app-demo.mp4`
- **HandBrake**: GUI tool for video compression
- **Online Tools**: CloudConvert, Online-Convert.com

### Alternative: Multiple Format Support

For better browser compatibility, provide multiple formats:
- `app-demo.mp4` (Primary - H.264)
- `app-demo.webm` (Alternative - VP9)
- `app-demo-poster.jpg` (Thumbnail - 1920x1080)

The HTML is configured to automatically use these files when present.
