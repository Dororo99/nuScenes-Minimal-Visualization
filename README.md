# nuScenes Minimal Visualization

nuScenes 데이터셋의 Front Camera + LiDAR + 3D Bounding Box 시각화.
**nuscenes-devkit 없이** JSON 직접 파싱 + 좌표 변환을 구현한 minimal 코드.

## Scenes

| Scene | Description | Samples | Objects |
|-------|-------------|---------|---------|
| scene-0523 | Oncoming bus, parked car on center divider, pedestrian, roundabout, parked trucks | 40 | Bus, Bicycle, Motorcycle, Car, Truck, Trailer, Pedestrian, Worker, Police |
| scene-0916 | Parking lot, bicycle rack, parked bicycles, bus, many peds, parked scooters, parked motorcycle | 41 | Bus, Bicycle, Motorcycle, Car, Truck, Pedestrian |

## Visualizations

각 씬에 대해 아래 4종의 이미지와 3종의 비디오를 생성:

**Images**
- `*_cam_bbox.png` — Front camera에 3D bounding box projection (카테고리별 색상 + 거리)
- `*_lidar_bev.png` — Bird's Eye View 포인트 클라우드 + BBox
- `*_lidar_3d.png` — 3D scatter plot + 와이어프레임 BBox
- `*_lidar_on_cam.png` — LiDAR 포인트를 카메라에 projection (깊이별 색상)
- `*_sequence.png` — 8프레임 시퀀스 overview

**Videos** (H.264, 2fps)
- `*_cam_bbox.mp4` — 전체 프레임 카메라 + 3D BBox
- `*_lidar_on_cam.mp4` — 전체 프레임 LiDAR-on-camera projection
- `*_sidebyside.mp4` — 카메라+BBox | BEV 나란히 합성

## Directory Structure

```
nuscenes_vis/
├── data/                              # 추출된 mini-dataset (~945MB)
│   ├── v1.0-mini-extract/             #   메타데이터 JSON (9 files)
│   ├── samples/                       #   key-frame 센서 데이터
│   │   ├── CAM_FRONT/
│   │   ├── LIDAR_TOP/
│   │   └── ...
│   └── sweeps/
│
├── nuscenes_visualize.ipynb           # 전체 nuScenes 사용 (/data/nuscenes)
├── nuscenes_visualize_standalone.ipynb # standalone (./data/ 만 사용)
│
├── output/                            # 원본 노트북 출력
│   └── videos/
└── output_standalone/                 # standalone 노트북 출력
    └── videos/
```

## Usage

### Standalone (추출 데이터만 사용)

```bash
cd nuscenes_vis/
jupyter notebook nuscenes_visualize_standalone.ipynb
```

`data/` 폴더만 있으면 전체 nuScenes 없이 실행 가능.

### Full dataset

```bash
jupyter notebook nuscenes_visualize.ipynb
```

`/data/nuscenes/v1.0-trainval/` 경로에 전체 nuScenes가 필요.

## Dependencies

- Python 3.8+
- numpy, matplotlib, Pillow, opencv-python
- ffmpeg (비디오 H.264 변환)

## Implementation Notes

- **좌표 변환**: quaternion → rotation matrix, global → ego → sensor 직접 구현
- **3D BBox**: annotation의 translation/size/rotation에서 8개 코너 계산 후 카메라/라이다 프레임으로 변환
- **Line clipping**: Cohen-Sutherland 알고리즘으로 이미지 밖 box edge 처리
- **LiDAR projection**: lidar → global → camera 체인 변환 후 intrinsic matrix로 2D 투영
- **비디오 생성**: matplotlib figure → numpy array → cv2.VideoWriter → ffmpeg H.264 변환
