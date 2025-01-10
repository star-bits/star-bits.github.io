# Latent Diffusion 
- [Denoising Diffusion Probabilistic Models](https://arxiv.org/pdf/2006.11239) (DDPM)
- [High-Resolution Image Synthesis with Latent Diffusion Models](https://arxiv.org/pdf/2112.10752) (Stable Diffusion)
- [Adding Conditional Control to Text-to-Image Diffusion Models](https://arxiv.org/pdf/2302.05543) (ControlNet)

## Denoising in Latent Space
- 모델은 noise predictor임.
- 모델에게 이미지에 더해진 노이즈를 예측하게 하고, 실제로 얼마나 더해졌는지 알려줌.
- 각 sampling step마다 반복함. 약 20~100회.
- 이 과정이 pixel space가 아니라 latent space에서 일어남.
- 인코더가 pixel space에서 latent space로 변환하고 디코더가 latent space에서 pixel space로 되돌림.
- 이 latent representation이 computational load를 order of magnitude로 줄임.
- U-Net 구조를 사용함. Stable Diffusion v1은 512x512 크기의 이미지를 생성함. 

![fig3](https://github.com/user-attachments/assets/d9780620-8214-45d0-b4ef-c3af8025ebec)

## Conditioning: Text-prompt, LoRA, ControlNet, image-to-image
- 이 noise prediction을 해 나가는 과정에 있어 predictor의 steering에 가이던스를 줄 수 있어야 원하는 이미지를 생성할 수 있음. (당연히..)
- 텍스트 프롬프트의 경우, tokenizer, embedding layer, transformer encoder를 거쳐 K와 V를 만들어내고, 일반적으로 cross attention을 하듯이 U-Net의 여러 stage에 먹여줌.
- 이 과정에서 LoRA를 이용해 cross-attention weight들을 줄이는 방법이 사용됨.
- ControlNet은 이미지의 구조(depth, pose 등)에 대한 조건을 추가로 주어, 더욱 세밀한 컨트롤.
- image-to-image transformation의 경우, 랜덤한 latent noise가 아니라 인코더가 생성한 기존 이미지에 노이즈를 더한 latent representation이 사용됨. 
