#done

- Official model: [Hugging Face - Stability AI](https://huggingface.co/stabilityai)
- Official repo: [GitHub - Stability-AI/sd3.5](https://github.com/Stability-AI/sd3.5)

## Set up step by step

Download repo from [GitHub - Stability-AI/sd3.5](https://github.com/Stability-AI/sd3.5) and unzip it

Download model from [Hugging Face - Stability AI](https://huggingface.co/stabilityai) (registration needed):
0. Enter the repo in Hugging Face
1. Select tab "Files and versions"
2. Download the "sd3.5_xxx.safetensors" and move it into `models` directory

Download [CLIP-L model](https://huggingface.co/stabilityai/stable-diffusion-3.5-large/blob/main/text_encoders/clip_l.safetensors) and move it into `models` directory

Download [OpenCLIP bigG](https://huggingface.co/stabilityai/stable-diffusion-3.5-large/blob/main/text_encoders/clip_g.safetensors) and move it into `models` directory

Download [Google T5-XXL](https://huggingface.co/stabilityai/stable-diffusion-3.5-large/blob/main/text_encoders/t5xxl_fp16.safetensors), rename the model file to "t5xxl.safetensors" and move it into `models` directory

Download models for ControlNets and move it into `models` directory (registration needed):
- [Blur ControlNet](https://huggingface.co/stabilityai/stable-diffusion-3.5-controlnets/resolve/main/blur_8b.safetensors)
- [Canny ControlNet](https://huggingface.co/stabilityai/stable-diffusion-3.5-controlnets/resolve/main/canny_8b.safetensors)
- [Depth ControlNet](https://huggingface.co/stabilityai/stable-diffusion-3.5-controlnets/resolve/main/depth_8b.safetensors)

Create and set up the python environment:
0. Create and activate a new conda env
1. Install the requirements `python -s -m pip install -r requirements.txt`

## Test

Activate the conda env and execute `python3 sd3_infer.py --prompt "cute wallpaper art of a cat"`