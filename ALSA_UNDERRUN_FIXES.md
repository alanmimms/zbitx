# ALSA Underrun Fixes for zbitx

## Changes Made

1. **Increased buffer sizes**: 
   - Period size: 2048 frames (was ~1024)
   - Buffer size: 8192 frames with 4 periods
   - Total latency: ~85ms at 96kHz

2. **Improved error recovery**:
   - Better underrun handling with stream recovery
   - Pre-fill playback buffer with silence

3. **Fixed software parameters**:
   - Start threshold set to 1 (immediate start)
   - Proper avail_min configuration

## Additional Solutions

### 1. Use Custom ALSA Configuration
Copy the provided `asoundrc_example` to `~/.asoundrc`:
```bash
cp asoundrc_example ~/.asoundrc
```

Then modify sbitx to use device "sbitx" instead of "plughw:0,0":
```bash
./sbitx sbitx
```

### 2. System Optimizations

#### Set CPU Governor to Performance:
```bash
echo performance | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

#### Use Real-time Kernel:
```bash
sudo apt-get install linux-image-rt
```

#### Increase Audio Thread Priority:
Add to `/etc/security/limits.conf`:
```
@audio - rtprio 95
@audio - memlock unlimited
```

### 3. Try Alternative Device
Instead of `plughw:0,0`, try:
- `default` - Uses system default with dmix
- `pulse` - If PulseAudio is installed (with higher latency)

### 4. Debug Information
Run with debug enabled to see actual buffer sizes:
```bash
# Edit sbitx_sound.c and set DEBUG to 1
# Recompile and run
```

## Testing
Monitor for underruns:
```bash
./sbitx 2>&1 | grep -i underrun
```

The current configuration provides a balance between low latency (85ms) and stability.
For CW operation, this latency is acceptable. If underruns persist, try the 
alternative solutions above.