# 用于提取视频(.mp4)的帧

```python
import decord
import cv2
import os
video_path = "/root/autodl-tmp/jeep-moving.mp4"
frames_dir = '/root/autodl-tmp/frames'
video_path = os.path.normpath(video_path)
frames_dir = os.path.normpath(frames_dir)
video_dir, video_basename = os.path.split(video_path)
video_filename = video_basename.split('.')[0]
```

```python
# width,height设置图片大小
# vr = decord.VideoReader(video_path,width=512,height=512)
vr = decord.VideoReader(video_path)
frames_list = list(range(0, len(vr), 1))[:8]
frames = vr.get_batch(frames_list).asnumpy()
# frames = vr.get_batch(frame_list)
```

```python
i = 0
for index, frame in zip(frames_list, frames):
    save_path = os.path.join(frames_dir,video_filename,f"{i:05d}.png")
    os.makedirs(os.path.dirname(save_path), exist_ok=True)
    # if not os.path.exists(save_path) or overwrite:  # if it doesn't exist or we want to overwrite anyways
    cv2.imwrite(save_path, cv2.cvtColor(frame, cv2.COLOR_RGB2BGR))  # save the extracted image
    i += 1
```