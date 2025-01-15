# Self-Taught Reasoner
- [STaR: Bootstrapping Reasoning With Reasoning](https://arxiv.org/pdf/2203.14465)

## reasoning trace 데이터셋의 self-feeding amplification loop
- rationale(reasoning trace)이 있는 데이터로 few-shot을 함.
- 정답 맞춤: 새로 생성한 rationale을 파인튜닝 데이터로 추가함.
- 정답 틀림: 정답을 힌트로 주고 justify하는 rationale을 작성하라고 시킴. 이후 파인튜닝 데이터로 추가함.
![fig1](https://github.com/user-attachments/assets/3c793b9e-7a4d-4802-904e-794f29d42b96)
- 정답이 틀렸을 경우에도 rationale을 작성하게 만들어서 다시 피딩하는게 얘네의 노블한 아이디어.
- 이 과정이 가능하도록 충분히 똑똑한 모델을 써야 한다고 함.
- GPT-J(6B)를 사용하고 (Appendix H에 하이퍼파라미터와 파인튜닝 디테일), n-digit 사칙연산, CommonsenseQA, GSM8K 데이터셋을 사용함.
![fig4](https://github.com/user-attachments/assets/03ae2d94-b836-4cef-af4f-bbe05bb50468)
- 사칙연산의 경우 xml tag `<scratch>`, `</scratch>`로 생각 과정을 구분함.
- 한 번에 한 digit의 숫자들만 계산하고 carry를 명시하는 방법으로 계산해감.
- NN이 gut-feeling으로 단순하고 즉각적인 예측을 하니까 스텝을 나눠줘야지.

## 생각
- 그냥 이게 다.. 인가? reasoning trace를 인간이 작성하고, 이걸 few-shot 삼아 데이터셋을 만들고, 조금 더 똑똑한 모델을 만들고.. 계속 반복하는게 발전 방향일까? 방법론에서는 별다른 breakthrough 없이? 그냥 계속 next-token prediction만 하고? 매번 조금씩만 나은 데이터셋과 모델을 만들어나가면서?
