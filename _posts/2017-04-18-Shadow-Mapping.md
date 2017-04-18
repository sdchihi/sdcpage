---
layout: post
title: "Shadow Mapping"
categories: Shader
tags: [Shader]
image:
  feature: atoz.jpg
  teaser: atoz_teaser.jpg
  credit:
  creditlink:
---
# Shadow Mapping ( 그림자 매핑 )
<br>
### 기본 아이디어
**첫번째 Pass - 그림자 생성( 광원 시점에서 물체를 본 결과를 텍스쳐로. )**
* 정점 셰이더
  * 광원에서 바라본 뷰공간으로 변환
  * 그대로 광원 투영 공간으로 변환한 위치 정보 넘김
* 픽셀 셰이더
  * 투영된 데이터를 이용해 깊이 값을 반환
* 렌더 타겟 설정 (결과 값을 텍스쳐로)

<br>
`정점 셰이더에서 받은 Position값의 Z값을 W로 나누는 과정이 필요하다. 이유는 위치 벡터값은 평행이동에 대한 값으로 W를 사용하기 때문에 올바른 좌표 값을 구하려면 W로 나누는 과정이 필수적이다.
( W 가 0이면 방향 벡터임을 추측할 수 있다. )`


**두번째 Pass -  그림자 적용(그리는 픽셀의 깊이를 그림자 맵의 깊이와 비교)**
* 정점 셰이더
  * 일반 렌더링에 필요한 투영 좌표, 난반사 값 등등 구함. (카메라 시점)
  * 광원으로부터 깊이를 구해서 같이 반환한다 ( Shadow Map 과 비교를 위함)
* 픽셀 셰이더
  * 대응시킬 Shadow Map의 UV좌표 값을 구함
  * 비교했을때 광원으로부터 깊이가 더 깊을때 그림자를 씌우면 됨.


**그림자가 부자연스러운 이유**
* 부동소수점으로 인한 오차
  * 비교하는 과정에서 동치 관계를 느슨하도록 아주 작은 값을 더해줌으로써 보정한다.
* Shadow Map의 크기가 너무 작은 경우
* 그림자를 그리는 방향과 카메라가 그리는 방향이 다르기 때문에 품질 저하 발생

`Shadow Map의 UV좌표 구하는 부분은 좀더 공부가 필요할 것 같다.
아직 이해가 잘 안됨.`

<br>

```HLSL
  // Shadow Map의 XY -> UV 좌표 설정

   float2 uv= input.mClipPosition.xy/ input.mClipPosition.w;
   uv.y = uv.y;
   uv = uv * 0.5 +0.5;
```
