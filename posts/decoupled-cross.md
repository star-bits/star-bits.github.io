# Decoupled Cross-Attention
- [IP-Adapter: Text Compatible Image Prompt Adapter for Text-to-Image Diffusion Models](https://arxiv.org/abs/2308.06721)

## 다른 두 모달리티의 feature들을 동시에 conditioning하기
- vanilla stable diffusion에서는 텍스트 프롬프트가 Text Encoder를 통과해 Text Feature가 됨.
- 이 Text Feature들이 K, V가 되어 U-Net에 들어가고, noise predictor를 steer 함.
- IP-Adapter에선 Text Feature와 함께 Image Feature도 함께 들어감.

![eqn1](https://github.com/user-attachments/assets/0e1b511a-417a-4d4a-b44c-68059fc2a02f)

![fig2](https://github.com/star-bits/blog/assets/93939472/89793dad-ad5e-42c0-95a9-09b0f7daa61f)

- 두 종류의 K, V를 먼저 concat하고 각각 cross-attention을 하면 퀄리티가 확연히 낮아진다고 함.
- 하나의 cross-attention만 쓰면 image-specific information을 잃어버림.

## 생각
- 원하는 이미지의 특징을 던져주고 텍스트 프롬프트로 컨디셔닝 하는 것이 이미지 생성 용례 중 사용성이 가장 높은 편이고,
- decoupled cross-attention을 사용한 이 방법이 유사한 시도 중 가장 방법론이 깔끔하며,
- 독보적으로 정성적 결과물이 좋음.
  
![fig9](https://github.com/star-bits/blog/assets/93939472/0efff47d-f691-434c-9172-b1fa26f08262)


