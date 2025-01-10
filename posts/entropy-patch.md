# Entropy Based Patch 
- [Byte Latent Transformer: Patches Scale Better Than Tokens](https://arxiv.org/pdf/2412.09871)

## Token 대신 Patch를 쓰자
- next-token prediction이 아니라 next-patch prediction.
- patch는 다음 byte의 정보 엔트로피로 결정함. 따라서 토큰마다 information density가 균일해짐.
- patch의 길이에 따라 compute를 dynamic하게 allocate할 수 있음.
- Llama 3는 Llama 2 대비 average token size를 3.7 byte에서 4.4 byte로 늘였지만 embedding table의 크기가 4배 증가하는 비용 발생.
- patch를 이용해 최종 projection layer dimension을 늘리지 않고도 더 큰 사이즈의 vocab을 이용할 수 있음.

![fig2](https://github.com/user-attachments/assets/018fbfbc-62eb-438e-a1e1-36219e16d5c0)
- 엔트로피로 patch boundary를 구별하는 두 가지 방법: global entropy threshold, relative entropy threshold

![fig3fig4](https://github.com/user-attachments/assets/051d8be8-803e-4092-bd0d-4f00ed166092)
- BPE와 Entropy 비교

## entropy model
- 별도의 byte-level language model을 full BLT model과 동일한 데이터 분포상에서 entropy 추정 학습 수행.
- transformer with 100M params, 14 layers, hidden dim size of 512, sliding window attention of 512 bytes.
- 텍스트 구조가 반복적일수록 patch가 점점 커짐. 이런 경우를 대비해 주기적으로 entropy를 리셋함.

## 생각
- next-patch prediction을 한다는데, 그럼 encoding 된 patch는 dynamic하게 룩업테이블에 저장되는건가?
- 아니라면 어떻게 encoding의 역으로 decoding이 제대로 된다는 걸 보장하는 건지 모르겠음.
- 정보 엔트로피를 활용해서 개념적으로 더 완결성이 있는 느낌.
- 특정 용어가 반복해서 사용되거나 도메인에 따라 distribution이 크게 차이나는 데이터의 경우 (e.g., 코드) 효율적일 것.
- 근데 classification task에서 class가 dynamic하게 변하는게 가능한가?
- separately trained entropy model for patching. end-to-end는 여전히 아님.
