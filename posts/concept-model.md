# Latent Sentence (Concept) Model
- [Large Concept Models: Language Modeling in a Sentence Representation Space](https://arxiv.org/pdf/2412.08821)

## Next Token Prediction 대신 Next 'Concept' Prediction을 하자
- "[We] assume that a concept corresponds to a sentence."
- 각 문장당 10-20 토큰. 너무 길면 여러 개로 나눔. sentence segmentation 툴로는 SpaCy segmentor(rule-based)와 Sagment any Text(token level sentence boundary predictor)가 있음.
- 각 문장을 SONAR space(pretrained sentence embedding space임)으로 변환함.
- 이후 1) decoder-only transformer로 next 'concept' prediction을 하거나, 2) diffusion-based generation을 하거나, 3) FAISS clustering을 통해 SONAR space를 quantize하는 방법을 시도함.

![fig1](https://github.com/user-attachments/assets/9a6706dc-e5f0-4c66-998f-c441687d72c3)

## 1) 디코더로 Next Sentence Prediction
- MSE loss를 최소화하는 방법
- 그러나 embedding space에서 MSE를 직접 최소화하는 것은 좋은 결과를 내지 못함.

## 2) Diffusion
- diffusion 방법론은 continuous data에 대한 conditional probability distribution을 학습하고자 하는 것.
- e.g., 이미지 생성의 경우, 같은 프롬프트에 여러 이미지가 대응하므로, 모델은 continuous pixel data에 대한 probability distribution을 학습해야 함.
- SONAR space 속의 문장은 continuous vector로 표현되지만 실제론 discrete한 combinatorial object이기 때문에 text modality에 diffusion을 적용하기 어려움.
- softmax output에 cross-entropy loss를 적용하는 과정의 contrastive한 특징이 높은 정확도를 요구하는 next token prediction의 downstream task에 적합함. 하지만 continuous한 diffusion은 contrastive한 특성이 필요한 task에 불리하다고.

## 3) Space Quantization
- Residual Vector Quantization을 통해 SONAR representation을 discrete한 unit들로 분할함.
- continuous한 input embedding들을 학습한 codebook의 nearest entry에 넣음.
- RVQ가 interative하게 residual error를 여러 coebook에 걸쳐 quantize 함.
- SONAR space는 연속적이지만 text modality는 discrete하기 때문에 문장들 역시 continuous distribution이라기 보다 cloud of points에 가깝다.

## Related Works
- Marfurt and Hendersen (2021) and Cornille et al. (2024): next sentence embedding prediction in a fully generative setting. sentence-level connection을 token-level connection을 보완하는 데에 사용함.
- PLANNER architecture (Zhang et al., 2023): 문단 단위로 VAE와 diffusion 모델을 사용해 latent autoencoder representation을 예측함.
- Lovalace et al. (2024): 텍스트의 sematinc proposal을 diffusion 모델을 이용해 다음 proposal을 예측하고, decoder-only model에 적용함.
- TEncDM model (Shabablin et al., 2024): contextual token embedding space에서 diffusion을 실행하고 non-autoregressive하게 디코딩 함.
- Semformer (Yin et al., 2024): planning token을 추가해 미래 정보에 대한 예측을 하도록 학습.
- Ye et al. (2024): multi-step planning에 더 적합하도록 모델에 autoregressive generation이 아니라 discrete diffusion을 적용함.
- Ubukata et al. (2024): planning 작업을 위한 diffusion 응용 사례들을 정리.

## 생각
- token도 이미 큰 overhead인데 거기다 sentence까지 더하자고?
- 생각을 latent space 상에서 하기 때문에 여러 언어에 잘 대응할 수 있다고 하는데, 그냥 출력된 언어를 다른 언어로 번역하되 그 과정에서 SONAR space를 이용한 것과 다른 의미가 있나?
- 채팅, 코드, 수식 등의 다양한 포맷의 데이터에서는 sentence를 어떻게 잘 자르나? sentence segmentation이 너무 arbitrary 함.
- 기본적으로 one concept per sentence 전제도 불안정함. 하나의 coherent thought을 인위적으로 구분지으려는 시도에서 많은 문제가 발생할 것.
- 게다가 SONAR space는 separately trained됨. 
- continuous end-to-end stream of vector representation으로 깔끔하게 latent reasoning을 했으면 좋겠다.
- next sentence prediction보다는 생각을 모두 전개한 뒤에 한번에 생성하는 방식이 더 직관적으로 보임.
- 그리고 그 생각의 흐름은 diffusion으로 진행하는 것이 구조적으로 깔끔해 보임.
- thought의 latent diffusion을 조절해 각 생각의 information denstiy를 고르게 만들 수 있을지도.
- latent diffusion에서 cross-attention으로 노이즈를 steer하듯 생각의 흐름에도 인위적인, 또는 RL로 학습한 가이던스를 줄 수 있으면 좋을듯.
- diffusion으로 llm이 extrapolation을 할 수 있게 할 수 있지 않을까.
