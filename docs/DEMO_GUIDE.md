# Quick Guide: Creating Demo Videos for Your README

This guide will help you create professional demo videos for your Franka robot manipulation project.

## 🎯 Goal

Create 3 GIF demos:
- `franka_reach.gif` - Showing the robot reaching target positions ✅ DONE
- `franka_lift.gif` - Showing the robot grasping and lifting a cube ✅ DONE
- `franka_stack.gif` - Showing the robot stacking two cubes ✅ DONE

## 📹 Step-by-Step Process

### Step 1: Run Your Trained Agent

```bash
# For Reach task
python scripts/skrl/play.py --task=Template-Reach-Play-v0 \
    --checkpoint=logs/skrl/reach_franka/*/checkpoints/best_agent.pt

# For Lift task
python scripts/skrl/play.py --task=Template-Lift-Play-v0 \
    --checkpoint=logs/skrl/franka_lift/*/checkpoints/best_agent.pt

# For Stack task
python scripts/skrl/play.py --task=Template-Stack-Play-v0 \
    --checkpoint=logs/skrl/franka_stack/*/checkpoints/best_agent.pt
```

### Step 2: Record the Screen

**Option A: Using OBS Studio (Recommended)**

1. **Install OBS Studio:**
   ```bash
   sudo add-apt-repository ppa:obsproject/obs-studio
   sudo apt update
   sudo apt install obs-studio
   ```

2. **Configure OBS:**
   - Open OBS Studio
   - Click "+" under Sources → Add "Window Capture"
   - Select your Isaac Sim window
   - In Settings → Output:
     - Output Mode: Simple
     - Recording Quality: High Quality
     - Recording Format: MP4
     - Encoder: Hardware (NVENC H.264) if available

3. **Record:**
   - Position the camera for a good view of the robot
   - Click "Start Recording"
   - Let the robot perform 2-3 successful attempts (10-30 seconds)
   - Click "Stop Recording"
   - Videos are saved to `~/Videos/` by default

**Option B: Using SimpleScreenRecorder**

```bash
sudo apt install simplescreenrecorder
```

- Launch and select the Isaac Sim window
- Choose MP4 format with H.264 codec
- Record 10-30 seconds of successful behavior

**Option C: Using ffmpeg (Command line)**

```bash
# Record screen area (you need to adjust coordinates)
ffmpeg -video_size 1920x1080 -framerate 30 -f x11grab -i :0.0+100,200 -t 30 output.mp4
```

### Step 3: Convert Video to GIF

**Method 1: Using ffmpeg (Best Quality)**

```bash
# Install ffmpeg if needed
sudo apt install ffmpeg

# Navigate to your video location
cd ~/Videos

# Convert with high quality palette
ffmpeg -i reach_demo.mp4 \
    -vf "fps=10,scale=800:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" \
    -loop 0 franka_reach.gif

# Repeat for lift and stack
ffmpeg -i lift_demo.mp4 \
    -vf "fps=10,scale=800:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" \
    -loop 0 franka_lift.gif

ffmpeg -i stack_demo.mp4 \
    -vf "fps=10,scale=800:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" \
    -loop 0 franka_stack.gif
```

**Method 2: Using Online Tools**

1. Go to [ezgif.com/video-to-gif](https://ezgif.com/video-to-gif)
2. Upload your MP4 file
3. Settings:
   - Size: 800x600 or keep aspect ratio
   - Frame rate: 10-15 FPS
   - Method: FFMPEG
4. Click "Convert to GIF"
5. Download the result

**Method 3: Using GIMP**

1. Open GIMP
2. File → Open as Layers → Select video frames
3. Filters → Animation → Optimize (for GIF)
4. Export as GIF

### Step 4: Optimize GIF Size

If your GIF is larger than 10MB:

```bash
# Install gifsicle
sudo apt install gifsicle

# Optimize
gifsicle -O3 --colors 256 franka_reach.gif -o franka_reach_optimized.gif

# Or reduce colors further
gifsicle -O3 --colors 128 franka_reach.gif -o franka_reach_optimized.gif
```

Or use online tool: [ezgif.com/optimize](https://ezgif.com/optimize)

### Step 5: Move GIFs to Project

```bash
# Copy your GIFs to the media folder
cp ~/Videos/franka_reach.gif docs/media/
cp ~/Videos/franka_lift.gif docs/media/
cp ~/Videos/franka_stack.gif docs/media/
```

### Step 6: Verify and Commit

```bash
# Check if files are there
ls -lh docs/media/*.gif

# Add to git
git add docs/media/*.gif
git add README.md
git commit -m "docs: Add demo videos for reach, lift, and stack tasks"
git push origin main
```

## 💡 Tips for Great Demos

### Camera Positioning
- **Reach**: Side view showing robot arm movement clearly
- **Lift**: Angle showing gripper grasping and cube lifting
- **Stack**: View showing both cubes and the stacking action

### Video Quality
- **Duration**: 10-20 seconds (enough to show 1-2 complete successes)
- **Frame Rate**: 10-15 FPS for GIFs (good balance of smoothness and size)
- **Resolution**: 800x600 or 1024x768 for GIFs
- **File Size**: Aim for under 5-10MB per GIF

### Content
- Show the robot starting from rest position
- Capture at least one complete successful episode
- Avoid showing failures (unless for comparison)
- Make sure objects are clearly visible

### Lighting & Clarity
- Use Isaac Sim's good lighting settings
- Avoid cluttered backgrounds
- Make sure robot and objects contrast well

## 🎨 Advanced: Add Multiple Views

You can create a side-by-side comparison:

```bash
# Combine two videos side by side
ffmpeg -i view1.mp4 -i view2.mp4 -filter_complex hstack output.mp4

# Then convert to GIF as usual
```

## 📊 Optional: Add Training Curves

You can also add training performance plots:

1. Open TensorBoard:
   ```bash
   tensorboard --logdir=logs/skrl/
   ```

2. Navigate to http://localhost:6006
3. Take screenshots of reward curves
4. Save as `training_curves.png` in `docs/media/`
5. Add to README in the Results section

## ✅ Checklist

- [x] Record reach task (10-30 seconds) ✅
- [x] Record lift task (10-30 seconds) ✅
- [x] Record stack task (10-30 seconds) ✅
- [x] Convert all to GIF format ✅
- [x] Optimize GIF sizes (< 10MB each) ✅
- [x] Copy to `docs/media/` ✅
- [ ] Verify GIFs display in README (will appear after push to GitHub)
- [ ] Commit and push to GitHub
- [ ] Check GitHub repo to ensure GIFs display correctly

## 🆘 Troubleshooting

**GIF too large?**
- Reduce frame rate to 8-10 FPS
- Reduce resolution to 640x480
- Reduce color palette to 128 or 64 colors
- Trim video to show only successful attempts

**GIF not looping?**
- Add `-loop 0` to ffmpeg command
- In ezgif.com, check "Loop forever"

**Poor quality?**
- Use the palette generation method in ffmpeg
- Increase colors to 256
- Try higher resolution source video

**Can't record screen?**
- Make sure Isaac Sim window is visible and not minimized
- Try different recording software
- Check if you have necessary permissions

---

Good luck! Once you have your demos, your README will look much more professional and impressive for your CV! 🚀

