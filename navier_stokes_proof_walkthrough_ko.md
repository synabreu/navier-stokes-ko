# 나비에–스토크스: 직접 따라가는 계산과 공개 증명 검증 안내

작성일: 2026년 9월 9일

이 문서는 독자가 손으로 계산하고 공개 코드를 추적할 수 있도록 만든 학습 자료다. **밀레니엄 문제의 새로운 완전한 증명은 아니다.** 1–2절의 에너지 계산과 집중 예제에 관한 논증은 여기에서 전개한다. 3절은 실제 특이해의 구성과 비교 정리가 성립한다는 전제 아래 결론을 도출한다. 그 두 전제 전체를 이 문서에서 새로 증명하지는 않는다.

미분·적분과 벡터 미적분을 알고 있으면 1–3절을 따라갈 수 있다. 공개 논문 전체를 이해하려면 편미분방정식, 함수해석학, 소볼레프 공간, 분포, 함수열의 수렴과 정칙성에 대한 지식이 추가로 필요하다.

공식 문제는 나비에–스토크스 방정식의 모든 해를 닫힌 수식으로 구하라는 요구가 아니다. 허용된 초기값에 대해 매끄러운 해가 항상 전역적으로 존재하는지, 또는 허용된 데이터에서 그런 전역해가 존재하지 않는 반례가 있는지를 증명하는 문제다. A·B는 외력 없는 경우의 전역 존재이고, C·D는 적절한 매끄러운 외력을 허용하는 반례다. 공간은 각각 전체 공간과 주기 공간이다. [공식 문제 정의](https://www.claymath.org/wp-content/uploads/2022/06/navierstokes.pdf)

| 이 문서에서 다루는 내용 | 증명·검증 상태 |
|---|---|
| 에너지 항등식과 외력에 의한 에너지 상계 | 가정을 명시하고 계산을 전개함 |
| 유한에너지·무한최대속도 예제와 그 예제의 실패 이유 | 명시적 구성과 모순을 전개함 |
| 실제 특이해로부터 전역해 부존재 도출 | 실제 구성과 비교 정리를 전제로 한 조건부 증명 |
| 공개 Lean 코드의 정리·정의·의존 관계 | 특정 커밋에서 정적 점검함 |
| Lean 커널·Comparator의 실행 결과 | 6절에 실제 수행 상태를 별도로 기록함 |

## 1. 에너지 항등식을 처음부터 증명하기

점성계수 $\nu>0$인 비압축성 나비에–스토크스 방정식을 생각한다.

$$
\partial_tu+(u\cdot\nabla)u=-\nabla p+\nu\Delta u+f,
\qquad \nabla\cdot u=0.
$$

$u=(u_1,u_2,u_3)$는 속도, $p$는 압력, $f$는 외력이다. 여기서는 매끄러운 해를 다루며, 관심 있는 각 닫힌 시간 구간에서 $u$의 공간적 지지집합이 하나의 고정된 콤팩트 집합에 포함된다고 가정한다. 이 가정으로 아래의 부분적분에는 무한대에서 생기는 경계항이 없다. 같은 계산은 충분한 감쇠 조건이나 주기적 경계조건에서도 가능하다.

방정식 양변에 $u$를 내적하고 공간 전체에 적분한다. 시간 미분 항은

$$
\int_{\mathbb R^3}u\cdot\partial_tu\,dx
=\frac12\frac{d}{dt}\int_{\mathbb R^3}|u|^2\,dx
$$

이다. 비선형 항은 발산이 0이라는 조건으로 사라진다.

$$
\begin{aligned}
\int u\cdot(u\cdot\nabla)u\,dx
&=\frac12\int u\cdot\nabla|u|^2\,dx\\
&=\frac12\int\nabla\cdot(|u|^2u)\,dx
-\frac12\int |u|^2\nabla\cdot u\,dx=0.
\end{aligned}
$$

압력 항과 점성 항도 부분적분하면 각각

$$
\int u\cdot\nabla p\,dx=-\int p\,\nabla\cdot u\,dx=0,
\qquad
\int u\cdot\Delta u\,dx=-\int|\nabla u|^2\,dx
$$

이 된다. 따라서 다음 **에너지 항등식**이 나온다.

$$
\boxed{\frac12\frac{d}{dt}\|u(t)\|_2^2
+\nu\|\nabla u(t)\|_2^2
=\int f(x,t)\cdot u(x,t)\,dx.}
$$

기호 $\|u\|_2=(\int|u|^2dx)^{1/2}$는 속도를 공간 전체에서 제곱하여 합친 크기다. $\tfrac12\|u\|_2^2$는 밀도를 1로 둔 운동에너지에 해당한다. 반면 $\|u\|_\infty$는 속도의 최대 크기다. 이 두 양은 서로 다르다.

초기 속도가 0이어도 위 식에서 $\|u\|_2$로 바로 나누면 안 된다. 0으로 나누는 문제를 피하기 위해 $\varepsilon>0$에 대해

$$
Y_\varepsilon(t)=\sqrt{\|u(t)\|_2^2+\varepsilon^2}
$$

라고 두면, 코시–슈바르츠 부등식으로

$$
Y_\varepsilon'(t)
=\frac{\int f\cdot u-\nu\|\nabla u\|_2^2}{Y_\varepsilon(t)}
\le\frac{\|f(t)\|_2\|u(t)\|_2}{Y_\varepsilon(t)}
\le\|f(t)\|_2.
$$

적분한 뒤 $\varepsilon\downarrow0$을 취하면

$$
\boxed{\|u(t)\|_2\le\|u(0)\|_2+\int_0^t\|f(s)\|_2\,ds.}
$$

따라서 $u(0)=0$이고 $f$가 시공간에서 매끄럽고 콤팩트 지지이면, 해가 매끄럽게 존재하는 동안 운동에너지는 시간에 대해 일정한 상계로 제어된다. 하지만 이 계산은 최대 속도 $\|u\|_\infty$의 상계를 주지는 않는다.

## 2. 유한한 에너지와 무한한 최대 속도를 동시에 만드는 예제

$\rho\in C_c^\infty(\mathbb R^3)$를 닫힌 단위공 안에 지지되고 원점 근방에서는 1인 함수로 고른다. 다음과 같이 벡터장을 정의한다.

$$
\chi(y)=y_2\rho(y),\qquad
\phi(y)=\nabla\times(0,0,\chi(y))
=(\partial_2\chi,-\partial_1\chi,0).
$$

그러면 $\phi$는 매끄럽고 닫힌 단위공 안에 지지된다. 혼합 미분의 순서를 바꾸어

$$
\nabla\cdot\phi
=\partial_1\partial_2\chi-\partial_2\partial_1\chi=0
$$

을 확인할 수 있다. 원점 근처에서는 $\phi=(1,0,0)$이므로 0이 아닌 벡터장이다.

시간에 대한 매끄러운 함수 $\eta$는 $0\le\eta\le1$, $t\le1/4$에서 0, $t\ge1/2$에서 1이 되도록 고른다. $0\le t<1$에 대해

$$
\delta(t)=1-t,\qquad
\boxed{u(x,t)=\eta(t)\delta(t)^{-1}
\phi\!\left(\frac{x}{\delta(t)}\right)}
$$

라고 두자. 이 벡터장은 모든 $t<1$에서 매끄럽고, 발산이 0이며, 초기값은 0이다. 지지집합은 반지름 $\delta(t)$인 공 안에 들어간다. 즉 시간이 지날수록 더 좁은 영역으로 집중된다.

$t\ge1/2$에서는 $\eta=1$이므로

$$
\|u(t)\|_\infty
=\delta^{-1}\|\phi\|_\infty\longrightarrow\infty.
$$

한편 변수변환 $x=\delta y$, $dx=\delta^3dy$를 하면

$$
\|u(t)\|_2^2
=\delta^{-2}\delta^3\int|\phi(y)|^2dy
=\delta\|\phi\|_2^2\longrightarrow0.
$$

따라서 **최대 속도가 무한히 커지는데도 전체 운동에너지는 오히려 0으로 갈 수 있다.** 속도는 $\delta^{-1}$배 커지지만, 그 속도가 나타나는 부피는 $\delta^3$배 작아지기 때문이다.

그렇다고 이 예제가 매끄러운 외력을 갖는 나비에–스토크스 반례가 되는 것은 아니다. 실제로 이 특정한 $u$에 대해서는 어떤 압력 $p$를 골라도 $t=1$까지 매끄러운 외력 $f$를 가질 수 없음을 증명할 수 있다.

$$
M=\|\phi\|_2^2>0,\qquad
G=\|\nabla\phi\|_2^2>0
$$

라고 쓰자. $G>0$인 이유는 $G=0$이면 매끄러운 $\phi$가 공간 전체에서 상수여야 하고, 콤팩트 지지인 상수는 0뿐인데 $\phi\ne0$이기 때문이다. 직접 미분하고 변수변환하면

$$
\frac12\frac{d}{dt}\|u(t)\|_2^2=-\frac12M,
\qquad
\|\nabla u(t)\|_2^2=\delta^{-1}G.
$$

만약 이 $u$가 어떤 압력과 외력으로 나비에–스토크스 방정식을 만족한다면, 앞에서 증명한 에너지 항등식에 의해

$$
\int f\cdot u\,dx=\nu\delta^{-1}G-\frac12M
\longrightarrow+\infty.
$$

하지만 $f$가 $t=1$을 포함하여 매끄럽다면, 닫힌 단위공 $K$와 시간 구간 $[1/2,1]$의 곱은 콤팩트이므로

$$
F=\max_{(x,t)\in K\times[1/2,1]}|f(x,t)|<\infty.
$$

따라서 다른 한편으로는

$$
\left|\int f\cdot u\,dx\right|
\le F\|u(t)\|_1
=F\delta^2\|\phi\|_1\longrightarrow0.
$$

같은 양이 무한대로 가면서 동시에 0으로 갈 수는 없다. 모순이다. 압력 항은 에너지 항등식에서 사라지므로 압력을 바꾸는 것으로 이 모순을 피할 수도 없다.

**이 계산이 드러내는 장애물:** 발산하는 속도장을 쓰는 것은 비교적 쉽다. 그러나 점성이 그 속도장의 날카로운 공간 변화를 완화하려는 효과까지 맞추면서 외력을 끝까지 매끄럽게 유지하는 것은 전혀 다른 조건이다. 진짜 반례의 구성은 이 조건을 만족해야 한다.

## 3. 실제 특이해가 구성되었다면 마지막 귀류법은 어떻게 끝나는가

다음 두 내용은 이 문서에서 증명한 것이 아니라, 해당 반례 논증을 완성하기 위해 별도로 입증해야 하는 전제다.

1. 어떤 매끄러운 초기값과 허용된 매끄러운 외력에 대해, $0\le t<1$에서 매끄러운 해 $u$가 존재한다. $u$의 지지집합은 고정된 콤팩트 집합 $K$에 포함되고, 에너지는 균일하게 유계지만 $\limsup_{t\uparrow1}\|u(t)\|_\infty=\infty$이다.
2. 같은 초기값과 외력에 대한 후보 전역해 $v$가 문제에서 요구하는 조건을 만족하면, 모든 $T<1$에 대해 $v=u$가 $[0,T]$에서 성립한다는 비교 정리가 있다. 어떤 해의 범주에서 이 비교 정리가 성립하는지까지 정확히 증명해야 한다.

두 전제가 성립한다고 하자. 전역적으로 매끄러운 후보 해 $v$가 있다고 가정한다. $v$의 연속성과 $K\times[0,1]$의 콤팩트성으로

$$
B=\max_{(x,t)\in K\times[0,1]}|v(x,t)|<\infty.
$$

비교 정리에 의해 모든 $t<1$에서 $u=v$이다. $u$는 $K$ 밖에서 0이므로

$$
\|u(t)\|_\infty=\sup_{x\in K}|v(x,t)|\le B
\qquad(0\le t<1).
$$

이는 첫 번째 전제의 최대 속도 발산과 모순이다. 따라서 이러한 전역적 매끄러운 후보 해 $v$는 존재하지 않는다.

이 마지막 단계는 짧고 완전히 따라갈 수 있다. 그러나 어려운 두 전제, 특히 **외력이 특이점 시각까지 매끄러운 실제 해의 구성**을 이 귀류법으로 대체할 수는 없다.


## 4. 위 계산을 공개 논문에 연결하기

OpenAI의 공개 논문 정리 1.1은 임의의 양의 점성에 대해, 영 초기속도와 매끄러운 시공간 콤팩트 지지 외력으로 유한시간 속도 발산을 구성한다고 주장한다. 해는 특이점 이전에 매끄럽고 에너지는 균일하게 유계이며, 공간 지지는 고정된 콤팩트 집합 안에 있다. [논문 정리 1.1](https://cdn.openai.com/pdf/32d9f210-8b73-45e0-91bc-82a30aef8a9a/navier-stokes.pdf)

이 논문을 읽을 때 가장 먼저 추적할 양은 잔차다.

$$
\mathcal R(u,p)=\partial_tu+(u\cdot\nabla)u-\nu\Delta u+\nabla p.
$$

외력을 $f=\mathcal R(u,p)$로 정하려면 이 잔차가 특이점 시각을 포함해 매끄러워야 한다. 논문은 기본 소용돌이, 진동 성분, 잔차 보정, 공간·시간 국소화를 조합하는 구성을 제시한다. 단순한 속도 집중만으로는 부족하다는 점은 2절에서 직접 확인했다. [논문 제3절](https://cdn.openai.com/pdf/32d9f210-8b73-45e0-91bc-82a30aef8a9a/navier-stokes.pdf)

논문의 비교 보조정리 10.5는 3절의 두 번째 전제에 해당한다. 전체 공간의 경쟁 해에는 무한대에서 압력이나 도함수가 어떻게 커지는지에 관한 추가 조건을 임의로 넣을 수 없다. 따라서 이 비교를 단순한 형식적 부분적분으로 대신하면 논증에 빈틈이 생긴다. 논문은 국소화와 압력 추정을 포함한 비교 논증을 제시한다. [논문 제10절](https://cdn.openai.com/pdf/32d9f210-8b73-45e0-91bc-82a30aef8a9a/navier-stokes.pdf)

실제 구성에 관한 전제는 이 문서에서 미해결인 핵심이다. “여기서 적절한 보정항을 선택한다”라는 문장만으로는 증명이 되지 않는다. 보정항의 존재, 합의 수렴, 미분과 극한의 교환, 외력의 모든 차수 도함수, 속도 발산의 보존을 각각 정당화해야 한다.

## 5. Lean 코드에서 실제로 읽을 부분

아래 링크와 점검 결과는 다음 커밋을 기준으로 한다.

`8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538`

주요 최종 정리는 다음 두 개다.

```lean
NavierStokes.Comparator.navier_stokes_breakdown_R3
NavierStokes.Comparator.navier_stokes_breakdown_periodic
```

전체 공간 정리의 선언을 읽기 쉽게 풀면 다음과 같다.

$$
\forall\nu>0,\quad
\exists u_0,f:\quad
\operatorname{AdmissibleInitial}(u_0)\land
\operatorname{AdmissibleForce}(f)\land
\neg\exists(v,p)\,\operatorname{GlobalSmoothFiniteEnergySolution}(\nu,u_0,f,v,p).
$$

여기서 `AdmissibleInitial` 등의 영문 표기는 설명을 위한 약칭이다. 실제 Lean 정의는 다음 표의 파일에 있다. 정리 선언만 보지 말고 이 정의들을 함께 읽어야 한다.

| 읽는 순서 | 파일 | 확인할 내용 |
|---|---|---|
| 1 | [ComparatorDefinitions.lean](https://github.com/openai/NavierStokesAndEuler/blob/8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538/NavierStokes/ComparatorDefinitions.lean) | 실제 PDE, 외력의 매끄러움과 감쇠, 속도의 적분가능성과 에너지, 주기적 압력 |
| 2 | [ComparatorSolution.lean](https://github.com/openai/NavierStokesAndEuler/blob/8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538/NavierStokes/ComparatorSolution.lean) | 최종 C·D 정리와 `#print axioms` 명령 |
| 3 | [ComparatorR3Theorem.lean](https://github.com/openai/NavierStokesAndEuler/blob/8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538/NavierStokes/ComparatorR3Theorem.lean) | 구성된 해를 C의 명제로 연결하는 과정 |
| 4 | [R3ActualCandidate.lean](https://github.com/openai/NavierStokesAndEuler/blob/8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538/NavierStokes/R3ActualCandidate.lean), [ActualCandidateAssembly.lean](https://github.com/openai/NavierStokesAndEuler/blob/8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538/NavierStokes/ActualCandidateAssembly.lean) | 실제 구성과 그 성질을 공급하는 존재 정리 |
| 5 | [R3FiniteEnergyComparison.lean](https://github.com/openai/NavierStokesAndEuler/blob/8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538/NavierStokes/R3FiniteEnergyComparison.lean), [WholeSpaceUniqueness.lean](https://github.com/openai/NavierStokesAndEuler/blob/8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538/NavierStokes/R3/WholeSpaceUniqueness.lean) | 임의의 경쟁 전역해와의 비교 |
| 6 | [R3CompactCandidate.lean](https://github.com/openai/NavierStokesAndEuler/blob/8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538/NavierStokes/R3CompactCandidate.lean) | 콤팩트 집합 위 연속성으로 모순을 얻는 마지막 단계 |

정적 점검에서는 최종 정리의 내부 의존 경로를 따라갔다. `selected_compact_candidate`가 `selected_witness`에서 해를 받아오고, `selected_candidate`는 반례의 존재를 가정으로 받지 않는 정리임을 확인했다. `Classical.choice`는 이미 증명된 존재 명제에서 대상을 선택하는 데 사용된다. 선택 공리의 사용과 증명할 명제 자체를 가정하는 것은 구분해야 한다.

독립 비교용 `ComparatorChallenges/NavierStokes.lean`에는 의도적으로 비워 놓은 두 정리의 `sorry`가 있다. 이것은 비교할 명제를 선언하는 파일이다. 이번 점검에서 해법의 내부 import 경로가 그 비교용 모듈을 가져오지 않는 것을 확인했다. 따라서 저장소 전체에서 `sorry`라는 문자열을 찾았다는 사실만으로 해법에 구멍이 있다고 판단해서는 안 된다.

반대로 해법에서 `sorry`를 못 찾았다는 사실만으로 정당성을 확정할 수도 없다. 실행 검증과 정의의 의미 검토가 추가로 필요하다.

## 6. 직접 실행하는 재검증 절차와 이번 수행 상태

다음은 Linux·Ubuntu·WSL에서 따라갈 수 있는 절차다. `git`과 [Elan](https://github.com/leanprover/elan)이 설치되어 있어야 한다. 별도 디렉터리에 공개 원본을 복제하고, 검토한 커밋으로 고정한다. 명령 실행에는 필요한 공개 패키지와 바이너리를 내려받을 네트워크가 필요하다.

```bash
git clone https://github.com/openai/NavierStokesAndEuler.git
cd NavierStokesAndEuler
git checkout 8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538
git rev-parse HEAD
elan toolchain install leanprover/lean4:v4.34.0-rc2
lean --version
lake --version
```

저장소의 `lean-toolchain`이 프로젝트의 Lean 버전을 지정한다. 이 프로젝트의 검증을 위해 그 버전이나 의존성 잠금 파일을 임의로 바꾸지 않는다.

```bash
lake exe cache get
lake build NavierStokes
```

위 빌드 대상을 지정하면 나비에–스토크스 라이브러리를 검사한다. 저장소 README의 `lake build`는 기본 대상 전체를 빌드하므로 오일러 쪽까지 포함한다. 빌드가 실패하면 첫 오류의 종류를 구분한다. 다운로드·설치·메모리 오류만으로 수학적 증명이 틀렸다고 결론 내릴 수는 없다. 실제 정리의 타입 검사 오류가 있으면 그 파일과 메시지를 별도로 분석해야 한다. [프로젝트 설정](https://github.com/openai/NavierStokesAndEuler/blob/8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538/lakefile.toml)

최종 파일에 이미 공리 의존성 출력 명령이 들어 있다. 빌드 후 다음처럼 확인할 수 있다.

```bash
lake env lean NavierStokes/ComparatorSolution.lean
```

출력 대상은 앞 절의 두 최종 정리다. 독립 비교 설정이 허용하는 공리는 `propext`, `Quot.sound`, `Classical.choice`이다. 실제 출력에 허용되지 않은 의존성이 나타나는지 확인한다. 정적 검색 결과와 이 실행 출력은 같은 검사가 아니다. [Comparator 설정](https://github.com/openai/NavierStokesAndEuler/blob/8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538/ComparatorChallenges/NavierStokes.json)

독립 검증은 별도 단계다. 저장소 안내에 따라 `landrun`, `lean4export`, `nanoda_bin`이 설치되어 `PATH`에서 실행 가능해야 한다. 도구 설치 방법은 [Comparator 공식 저장소](https://github.com/leanprover/comparator)와 [프로젝트의 독립 검증 안내](https://github.com/openai/NavierStokesAndEuler/blob/8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538/ComparatorChallenges/README.md)를 따른다. 단순 빌드만으로 이 검사가 끝났다고 기록하지 않는다.

```bash
lake exe comparator ComparatorChallenges/NavierStokes.json
```

이 설정은 `enable_nanoda: true`로 두 최종 정리를 비교한다. 성공한다면 그것은 사용한 도구·공리·정의 아래에서 해당 형식 명제의 검증 결과다. 종이에 적힌 모든 부가 주장이나 실제 유체에 대한 해석까지 자동으로 검증한 것은 아니다.

이번 작업의 실제 수행 결과는 다음과 같다.

| 작업 | 실제 결과 |
|---|---|
| 공개 저장소 복제와 커밋 고정 확인 | 완료 |
| 최종 정리·정의·일부 구성 경로의 소스 점검 | 완료 |
| 저장소 내부 import 경로와 특정 토큰 검사 | 완료 |
| 지정된 Lean 4.34.0-rc2 배포본 내려받기·설치 | 배포본 설치 완료, 실행 초기화 오류 발생 |
| `lean --version` | `error: failed to locate application`로 실패 |
| `lake --version` | Lake 5.0.0 / Lean 4.34.0-rc2 표시 확인 |
| `lake exe cache get` | 기본 실행은 설치 위치 감지 실패. 명시적 위치 설정 후 의존성 다운로드가 시작되었으나 완료되지 않음 |
| `lake build NavierStokes` 및 최종 정리 타입 검사 | 실행 단계에 도달하지 못함 |
| Comparator·nanoda 독립 검증 | 실행하지 못함 |

런타임 진단에서 이 환경은 숫자 PID를 사용한 자기 프로세스의 `/proc/<pid>/exe` 접근을 거부했다. Lean은 초기화 과정에서 이 경로를 사용하므로 `LEAN_SYSROOT` 지정만으로는 실행되지 않았다. Lake에는 설치 경로를 명시해 초기 감지를 통과시켰지만, 뒤이은 의존성 내려받기는 `network approval was cancelled before a decision was returned`라는 실행 도구 오류로 완료되지 않았다. 일반 Linux·WSL 등 호환 환경에서 위 절차를 다시 실행해야 한다.

**이번 작업은 Lean 커널 검증 성공을 입증하지 못했다.** 이 초기화·다운로드 실패를 논문이나 형식 증명의 수학적 오류로 판정하지도 않았다. 증명 코드나 검증기의 논리를 바꿔 실행 성공을 만들지 않았다.


정적 검사에서 나비에–스토크스 진입점의 저장소 내부 import 경로에 있는 **581개 파일, 379,523줄**을 대상으로 주석과 문자열을 제외한 특정 토큰 검사를 수행했다. `sorry`, `admit`, `axiom`, `sorryAx`, `unsafe`, `native_decide`, `implemented_by`, `extern` 토큰은 발견되지 않았다. 비교용 정의와 해법의 정의 본문이 텍스트로 일치하는 것도 확인했다. **이는 제한된 정적 검사이며, 379,523줄의 수학적 논증을 사람이 모두 검토했다는 뜻이 아니다.** 외부 Mathlib·컴파일러·커널 전체를 감사한 것도 아니다.

저장소의 `formalization.yaml`에는 검토 상태가 `self-assessed`로 기록되어 있다. 그 메타데이터를 이번 작업의 독립 검증 결과로 사용하지 않았다. [형식화 메타데이터](https://github.com/openai/NavierStokesAndEuler/blob/8937a8f4cbc7abaab5e9e97d1cc7f5d2319d9538/formalization.yaml)

## 7. 손으로 풀어 보는 확인 문제와 해설

### 문제 1. 일반적인 집중의 크기

$\lambda>0$, $a\in\mathbb R$, 0이 아닌 $\phi\in C_c^\infty(\mathbb R^3;\mathbb R^3)$에 대해 $v_\lambda(x)=\lambda^a\phi(\lambda x)$라고 하자. $L^p$ 노름, 최대 노름, 기울기의 $L^2$ 노름을 구하라.

변수변환 $y=\lambda x$를 하면 $1\le p<\infty$에서

$$
\|v_\lambda\|_p^p=\lambda^{ap-3}\|\phi\|_p^p,
\quad \|v_\lambda\|_\infty=\lambda^a\|\phi\|_\infty,
\quad \|\nabla v_\lambda\|_2^2=\lambda^{2a-1}\|\nabla\phi\|_2^2.
$$

$a=1$이고 $\lambda\to\infty$이면 최대 노름은 증가하지만 에너지는 감소한다. 기울기의 제곱 적분은 증가한다. 서로 다른 노름이 왜 서로 다른 역할을 하는지 이 지수에서 볼 수 있다.

### 문제 2. 2절 예제에서 총 점성 소산은 유한한가?

$$
\int_{1/2}^{1}\nu\|\nabla u(t)\|_2^2dt
=\nu G\int_{1/2}^{1}\frac{dt}{1-t}=\infty.
$$

매끄러운 외력 아래 에너지와 외력의 일이 유한하다면 이런 무한 소산은 에너지 항등식과 양립하지 않는다. 이는 2절의 실패를 적분 형태로 확인하는 방법이다.

### 문제 3. 초기속도와 외력이 모두 0이면?

1절에서 $u(0)=0$, $f=0$을 대입하면 $\|u(t)\|_2\le0$이다. 따라서 그 가정 아래 매끄러운 해는 $u=0$이다. 외력이 있는 영 초기값 결과를 외력 없는 영 초기값 결과로 바꿀 수 없다. 외력 없는 문제에서 특이점을 찾으려면 허용된 비영 초기값을 다뤄야 한다.

### 문제 4. 왜 $y'(t)\le Cy(t)^3$만으로 특이점이 증명되지 않는가?

$C>0$이라고 하자. 양의 상수 함수 $y(t)=1$도 이 부등식을 만족하면서 영원히 유계다. 상계로 얻은 미분부등식은 실제 해의 발산을 강제하는 하계가 아니다. 비교 방정식의 상계가 어떤 시간에 무한대로 가는 것은 그 이후의 제어를 잃었다는 뜻이지, 원래 해가 그 시간에 반드시 발산한다는 뜻은 아니다.

## 부록. 예제의 매끄러운 절단함수를 명시적으로 쓰기

$$
b(s)=\begin{cases}e^{-1/s},&s>0,\\0,&s\le0,\end{cases}
\qquad
H(s)=\frac{b(s)}{b(s)+b(1-s)}.
$$

분모는 모든 실수 $s$에서 양수다. $H(s)=0$은 $s\le0$에서, $H(s)=1$은 $s\ge1$에서 성립한다. $b$의 각 도함수는 $s>0$에서 $e^{-1/s}$에 $1/s$의 다항식을 곱한 꼴이다. $s\downarrow0$일 때 모두 0으로 가므로 0의 왼쪽과 매끄럽게 붙는다.

2절에서 다음을 택할 수 있다.

$$
\rho(y)=1-H\!\left(\frac{4|y|^2-1}{3}\right),
\qquad \eta(t)=H(4t-1).
$$

그러면 $\rho=1$은 $|y|\le1/2$에서, $\rho=0$은 $|y|\ge1$에서 성립한다. $\eta$도 앞서 요구한 시간 조건을 정확히 만족한다. 따라서 예제는 실제로 명시적인 함수들로 구성된다.