# Isaac Sim 仿真环境安装配置指南（Ubuntu 20.04）

> 版本：Isaac Sim 4.0+ | 系统：Ubuntu 20.04 LTS | 更新日期：2026-03-06

---

## 一、系统要求

### 硬件要求
| 组件 | 最低配置 | 推荐配置 |
|------|----------|----------|
| GPU | NVIDIA RTX 3060 (8GB) | NVIDIA RTX 4090 (24GB) |
| CPU | 8 核 | 16 核+ |
| 内存 | 32GB | 64GB+ |
| 存储 | 50GB SSD | 100GB+ NVMe SSD |

### 软件要求
- Ubuntu 20.04 LTS（内核 5.4+）
- NVIDIA Driver 535+
- CUDA 12.0+

---

## 二、前置依赖安装

### 1. 更新系统
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. 安装基础依赖
```bash
sudo apt install -y \
    wget \
    git \
    vim \
    curl \
    gnupg2 \
    software-properties-common \
    apt-transport-https \
    ca-certificates
```

### 3. 安装 NVIDIA 驱动

**方法一：使用官方驱动（推荐）**
```bash
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
ubuntu-drivers devices  # 查看推荐驱动
sudo apt install -y nvidia-driver-535
sudo reboot
```

**验证驱动安装**
```bash
nvidia-smi
# 应显示 GPU 信息和驱动版本
```

### 4. 安装 CUDA Toolkit 12.0
```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt update
sudo apt install -y cuda-toolkit-12-0
```

**配置环境变量**
```bash
echo 'export PATH=/usr/local/cuda-12.0/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/cuda-12.0/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc
```

### 5. 安装 Vulkan 支持
```bash
sudo apt install -y vulkan-tools libvulkan-dev
vulkaninfo  # 验证安装
```

---

## 三、Isaac Sim 安装

### 1. 注册 NVIDIA 账号
访问 https://ngc.nvidia.com 注册并获取 API Key

### 2. 安装 Omniverse Launcher（可选）
```bash
wget https://install.launcher.omniverse.nvidia.com/installers/linux/OmniverseLauncher-linux.AppImage
chmod +x OmniverseLauncher-linux.AppImage
./OmniverseLauncher-linux.AppImage
```

### 3. 通过 pip 安装 Isaac Sim（推荐开发者）
```bash
# 创建虚拟环境
python3 -m venv isaac-sim-env
source isaac-sim-env/bin/activate

# 升级 pip
pip install --upgrade pip

# 安装 Isaac Sim
pip install isaacsim==4.0.0
```

### 4. 通过容器安装（推荐生产环境）
```bash
# 拉取 Docker 镜像
docker pull nvcr.io/nvidia/isaac-sim:4.0.0

# 运行容器
docker run --gpus all -it --rm \
    -e "ACCEPT_EULA=Y" \
    -v ~/workspace-coder:/workspace \
    -p 8088:8088 \
    nvcr.io/nvidia/isaac-sim:4.0.0
```

---

## 四、验证安装

### 运行测试脚本
```bash
python3 -c "from omni.isaac.kit import SimulationApp; print('Isaac Sim 安装成功!')"
```

### 启动 GUI（如有显示器）
```bash
./python.sh -c "from omni.isaac.core import World; world = World(); world.play()"
```

---

## 五、第一个示例：立方体掉落

### 1. 创建脚本
```bash
mkdir -p ~/workspace-coder/isaac_examples
cat > ~/workspace-coder/isaac_examples/cube_drop.py << 'EOF'
"""
Isaac Sim 第一个示例：让立方体在重力作用下掉落
"""

from omni.isaac.kit import SimulationApp

# 启动仿真环境
simulation_app = SimulationApp({"headless": False})

from omni.isaac.core import World
from omni.isaac.core.primes import RigidPrim
from omni.isaac.core.utils.stage import create_new_stage
import numpy as np

# 创建新世界
world = World(stage_units_in_meters=1.0)

# 添加地面
ground_plane = world.scene.add(
    RigidPrim(
        prim_path="/World/Ground",
        name="ground",
        position=np.array([0, 0, 0]),
        scale=np.array([10, 10, 0.1]),
    )
)

# 添加立方体
cube = world.scene.add(
    RigidPrim(
        prim_path="/World/Cube",
        name="cube",
        position=np.array([0, 0, 2]),  # 初始高度 2 米
        scale=np.array([0.5, 0.5, 0.5]),
    )
)

# 重置世界
world.reset()

# 运行仿真
for i in range(500):
    world.step(render=True)
    if i % 50 == 0:
        pos, _ = cube.get_world_pose()
        print(f"Step {i}: Cube position = {pos}")

simulation_app.close()
EOF
```

### 2. 运行示例
```bash
cd ~/workspace-coder/isaac_examples
source ~/isaac-sim-env/bin/activate
python cube_drop.py
```

### 3. 预期输出
```
Step 0: Cube position = [0. 0. 2.]
Step 50: Cube position = [0. 0. 1.75]
Step 100: Cube position = [0. 0. 1.20]
...
Step 500: Cube position = [0. 0. 0.05]  # 接近地面
```

---

## 六、常见错误解决

### ❌ 错误 1：`Failed to load NVIDIA OpenGL driver`
**原因**：NVIDIA 驱动未正确加载

**解决**：
```bash
sudo modprobe nvidia
nvidia-smi  # 确认驱动正常
```

### ❌ 错误 2：`Vulkan not found`
**原因**：缺少 Vulkan 支持

**解决**：
```bash
sudo apt install -y vulkan-tools libvulkan-dev
export VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/nvidia_icd.json
```

### ❌ 错误 3：`CUDA out of memory`
**原因**：GPU 显存不足

**解决**：
- 降低仿真分辨率
- 减少场景中物体数量
- 使用 `headless=True` 模式运行

### ❌ 错误 4：`Permission denied: /dev/nvidia0`
**原因**：Docker 容器无 GPU 访问权限

**解决**：
```bash
# 安装 NVIDIA Container Toolkit
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
    sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt update
sudo apt install -y nvidia-docker2
sudo systemctl restart docker
```

### ❌ 错误 5：`ImportError: libGL.so.1`
**原因**：缺少 OpenGL 库

**解决**：
```bash
sudo apt install -y libgl1-mesa-glx libglu1-mesa
```

---

## 七、性能优化建议

### 1. 启用 GPU 加速
```python
simulation_app = SimulationApp({
    "headless": False,
    "width": 1920,
    "height": 1080,
    "multi_gpu": True  # 多 GPU 支持
})
```

### 2. 降低渲染质量（调试时）
```python
simulation_app = SimulationApp({
    "renderer": "RaytracedLighting",  # 或 "PathTraced"
    "anti_aliasing": 2  # 降低抗锯齿
})
```

### 3. 使用 Headless 模式训练
```python
simulation_app = SimulationApp({"headless": True})
```

---

## 八、有用资源

| 资源 | 链接 |
|------|------|
| 官方文档 | https://docs.omniverse.nvidia.com/isaacsim |
| GitHub 示例 | https://github.com/NVIDIA-Omniverse/IsaacGymEnvs |
| 论坛支持 | https://forums.developer.nvidia.com/c/agx-autonomous-machines/isaac |
| NGC 镜像 | https://ngc.nvidia.com/catalog/containers/nvidia:isaac-sim |

---

## 九、快速检查清单

- [ ] NVIDIA 驱动 535+ 已安装 (`nvidia-smi`)
- [ ] CUDA 12.0+ 已配置 (`nvcc --version`)
- [ ] Vulkan 支持已验证 (`vulkaninfo`)
- [ ] Isaac Sim 可导入 (`python -c "from omni.isaac.kit import SimulationApp"`)
- [ ] 立方体示例成功运行

---

**祝仿真顺利！🤖**
