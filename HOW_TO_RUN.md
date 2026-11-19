# 📸 How to Run — Bringing Old Photos Back to Life

> Restore damaged old photographs automatically using Microsoft's pretrained AI pipeline.
> No training needed. Just drop your photo and run one command.

---

## ⚙️ Requirements

- Windows 10 / 11
- Python 3.13 (venv already set up at `.\venv\`)
- NVIDIA GPU (RTX 3050 6GB or better) — or use CPU (slower)
- All pretrained models already downloaded and ready

---

## 🚀 Quick Start (3 Steps)

### Step 1 — Open PowerShell in the project folder

```powershell
cd "c:\Users\trilok\OneDrive\Desktop\project\Bringing-Old-Photos-Back-to-Life"
```

### Step 2 — Place your image in a folder

Create a new folder and copy your photo into it:

```powershell
mkdir my_input
# Then manually copy your photo into the my_input folder
```

> **Supported formats:** JPG, PNG  
> **Recommended size:** 300–480px wide (resize if larger — see tips below)

### Step 3 — Run the restoration pipeline

**If your photo has scratches / tears / damage:**
```powershell
$env:PATH = "$PWD\venv\Scripts;$env:PATH"
python run.py --input_folder ./my_input --output_folder ./outputs/my_result --GPU 0 --with_scratch
```

**If your photo is just old/faded (no visible scratches):**
```powershell
$env:PATH = "$PWD\venv\Scripts;$env:PATH"
python run.py --input_folder ./my_input --output_folder ./outputs/my_result --GPU 0
```

### ✅ Find your restored photo at:
```
outputs/my_result/final_output/<your_photo_name>.png
```

---

## 📂 How to Upload / Add Your Images

### Method 1 — Copy manually (easiest)
1. Create a new folder inside the project (e.g. `my_input`)
2. Copy/paste your JPG or PNG photo into that folder
3. Run the pipeline pointing to that folder

### Method 2 — Use PowerShell to copy
```powershell
# Copy a single photo from anywhere on your PC
Copy-Item "C:\Users\YourName\Pictures\old_family_photo.jpg" ".\my_input\"
```

### Method 3 — Process multiple photos at once
Put all your photos in one folder — the pipeline processes every image automatically:
```
my_input/
├── photo1.jpg
├── photo2.jpg
├── photo3.png
└── photo4.jpg
```
Then run once:
```powershell
python run.py --input_folder ./my_input --output_folder ./outputs/batch_result --GPU 0 --with_scratch
```

---

## 🗂️ Output Folder Structure

After running, your results appear here:

```
outputs/my_result/
├── final_output/
│   └── your_photo.png          ← ✅ USE THIS — final restored image
│
├── stage_1_restore_output/
│   ├── masks/mask/             ← scratch/damage detection mask
│   └── restored_image/         ← global restoration (before face enhancement)
│
├── stage_2_detection_output/   ← detected face crops
└── stage_3_face_output/        ← SPADE-enhanced faces
```

---

## 🎛️ Command Options

| Flag | Description | Example |
|---|---|---|
| `--input_folder` | Folder containing your photo(s) | `./my_input` |
| `--output_folder` | Where to save results | `./outputs/result1` |
| `--GPU` | GPU index (`0` = first GPU, `-1` = CPU) | `--GPU 0` |
| `--with_scratch` | Enable scratch/tear detection & removal | *(add if photo is damaged)* |

---

## 📏 Image Size Guidelines

> Large images can cause GPU out-of-memory errors.  
> Resize your photo before running if it's too large.

| Image Width | Status |
|---|---|
| Up to 480px | ✅ Safe — runs perfectly |
| 480–600px | ⚠️ May work — try it |
| Over 600px | ❌ Likely OOM — resize first |

### How to resize before running:
```powershell
$env:PATH = "$PWD\venv\Scripts;$env:PATH"
python -c "
from PIL import Image
img = Image.open('my_input/photo.jpg').convert('RGB')
w, h = img.size
if max(w, h) > 480:
    r = 480 / max(w, h)
    img = img.resize((int(w*r), int(h*r)), Image.LANCZOS)
img.save('my_input/photo.jpg', quality=95)
print(f'Ready: {img.size[0]}x{img.size[1]}')
"
```

---

## 🧪 Test Images (Already Included)

The project comes with 12 ready-to-use test images:

| Folder | Images | Use |
|---|---|---|
| `test_images/old/` | a–h (8 images) | Old photos without visible scratches |
| `test_images/old_w_scratch/` | a–d (4 images) | Old photos with scratches |

Run on all built-in test images:
```powershell
$env:PATH = "$PWD\venv\Scripts;$env:PATH"

# Without scratch detection
python run.py --input_folder ./test_images/old --output_folder ./outputs/test_old --GPU 0

# With scratch detection
python run.py --input_folder ./test_images/old_w_scratch --output_folder ./outputs/test_scratch --GPU 0 --with_scratch
```

---

## ⚡ Performance Reference

| Image Size | Approx. Time | GPU Used |
|---|---|---|
| 256×256 | ~60 seconds | RTX 3050 6GB |
| 380×480 | ~70–80 seconds | RTX 3050 6GB |
| 480×480 | ~90–110 seconds | RTX 3050 6GB |

---

## ✅ What Works Best

| ✅ Great Results | ⚠️ Limited Results |
|---|---|
| Old black & white photographs | Oil paintings / artwork |
| Scratched / torn family portraits | Very dark images |
| Faded / yellowed photos | Abstract images (no face) |
| Photos with a clear face | Images wider than 600px (resize first) |

---

## ❌ Common Errors & Fixes

| Error | Cause | Fix |
|---|---|---|
| `CUDA out of memory` | Image too large | Resize to max 480px (see above) |
| `No face detected` | No face in image | Pipeline still runs — only face step skipped |
| `ModuleNotFoundError` | Venv not activated | Add `$env:PATH = "$PWD\venv\Scripts;$env:PATH"` before running |
| `File not found` | Wrong folder path | Check folder name and use `./` prefix |

---

## 📞 Full Example — Step by Step

```powershell
# 1. Open project folder
cd "c:\Users\trilok\OneDrive\Desktop\project\Bringing-Old-Photos-Back-to-Life"

# 2. Create input folder
mkdir my_input

# 3. Copy your photo in (replace path with your actual photo)
Copy-Item "C:\Users\trilok\Pictures\grandma.jpg" ".\my_input\"

# 4. Activate venv
$env:PATH = "$PWD\venv\Scripts;$env:PATH"

# 5. Run restoration
python run.py --input_folder ./my_input --output_folder ./outputs/grandma_restored --GPU 0 --with_scratch

# 6. Open result
Start-Process "outputs\grandma_restored\final_output\grandma.png"
```

---

*Project: Microsoft Research — Bringing Old Photos Back to Life (CVPR 2020)*  
*Pretrained models: Face Enhancement (100 epochs), Global Restoration (200 epochs), Scratch Detection (91 epochs)*
