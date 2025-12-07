# Demo Media Assets

This folder contains demo videos and GIFs for the project README.

## Required Files

- `franka_reach.gif` - Demo of the reach task ✅
- `franka_lift.gif` - Demo of the lift task ✅
- `franka_stack.gif` - Demo of the stack task ✅

## How to Create Demo Videos

### Method 1: Record from Isaac Sim (Recommended)

1. **Run your trained agent in play mode:**
   ```bash
   python scripts/skrl/play.py --task=Template-Lift-Play-v0 \
       --checkpoint=logs/skrl/franka_lift/*/checkpoints/best_agent.pt
   ```

2. **Record with OBS Studio:**
   - Download and install [OBS Studio](https://obsproject.com/)
   - Set up a window capture or screen capture
   - Recommended settings:
     - Resolution: 1920x1080 or 1280x720
     - Frame rate: 30 FPS
     - Format: MP4 (H.264)
   - Record 10-30 seconds of successful behavior

3. **Convert to GIF:**
   
   **Option A - Using online tool:**
   - Go to [ezgif.com/video-to-gif](https://ezgif.com/video-to-gif)
   - Upload your video
   - Recommended settings:
     - Size: 800x600 (or maintain aspect ratio)
     - Frame rate: 10-15 FPS
     - Method: FFMPEG
   
   **Option B - Using ffmpeg command line:**
   ```bash
   # Install ffmpeg if not available
   sudo apt install ffmpeg
   
   # Convert video to GIF (high quality)
   ffmpeg -i input.mp4 -vf "fps=10,scale=800:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" -loop 0 output.gif
   
   # Or simpler version
   ffmpeg -i input.mp4 -vf "fps=10,scale=800:-1:flags=lanczos" output.gif
   ```

4. **Optimize GIF size (if needed):**
   - Try to keep GIF files under 10MB each for GitHub
   - Use [ezgif.com/optimize](https://ezgif.com/optimize) to compress
   - Or use: `gifsicle -O3 --colors 256 input.gif -o output.gif`

### Method 2: Use Isaac Sim's Built-in Recording

1. In Isaac Sim, go to `Window` → `Extensions` → Search for "Video Recorder"
2. Enable the extension
3. Configure output path and quality settings
4. Run your trained agent and record

### Method 3: Alternative - YouTube Videos

If GIFs are too large, you can upload videos to YouTube and embed them:

1. Upload your demo videos to YouTube
2. Get the video ID from the URL
3. Update README.md with:
   ```markdown
   [![Watch Demo](https://img.youtube.com/vi/VIDEO_ID/maxresdefault.jpg)](https://www.youtube.com/watch?v=VIDEO_ID)
   ```

## Tips for Good Demo Videos

- **Show successful behavior** - Feature your best trained agents
- **Keep it short** - 10-30 seconds per task
- **Multiple angles** - Show side view and top view if possible
- **Clear visibility** - Make sure robot and objects are clearly visible
- **Smooth playback** - 10-15 FPS is good for GIFs
- **Consistent quality** - All three demos should have similar quality

## File Naming Convention

- `franka_reach.gif` - Reach task demonstration ✅
- `franka_lift.gif` - Lift task demonstration ✅
- `franka_stack.gif` - Stack task demonstration ✅

Optional additional media:
- `franka_reach.mp4` - High quality video backup
- `franka_lift.mp4` - High quality video backup
- `franka_stack.mp4` - High quality video backup
- `training_curves.png` - TensorBoard training curves
- `architecture_diagram.png` - System architecture

## Current Status

- [x] franka_reach.gif ✅ (2.2MB)
- [x] franka_lift.gif ✅ (3.0MB)
- [x] franka_stack.gif ✅ (2.0MB)

All demo files have been added successfully! They will now appear in the main README.md on GitHub.

