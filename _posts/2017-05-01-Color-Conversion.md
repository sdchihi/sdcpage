---
layout: post
title: "Color Conversion (흑백 / 세피아 효과 사용)"
categories: Shader
tags: [Shader]
image:
  feature: atoz.jpg
  teaser: atoz_teaser.jpg
  credit:
  creditlink:
---
# Color Conversion

#### 기본원리

  사진 편집할때 필터를 입히는 효과를 게임속에서 적용하고 싶다면!<br>
 - 장면을 그린 후에 처리를 하는 포스트 프로세싱 방식을 이용하자.
 - 렌더링 결과 위에 화면을 덮을 사각형 메쉬(필터 역할)를 생성해 입힌다.

 <del>`사실 개념 자체는 간단하다`</del>

#### 구현방법
- 장면을 렌더링한 결과를 렌더 타겟으로 지정한다.
- 투영 공간을 가득 채우게 될 사각형 하나를 만드는 Pass를 추가한다.
- 새로 추가된 Pass의 픽셀 셰이더에서 UV 좌표에 맞춰 색상 값 계산 후 리턴.

> 구현 과정에서 쓰인 사각형 모델이 뒷면으로 인식되서 Back face Culling(은면 제거)가
이뤄져 화면에 결과가 나타나지 않았다. 이 기능을 제거하기 위해 Render State Block에서 CullMode를 None값으로 바꿔줌으로써 해결할 수 있었다.

<br>
<hr>
<br>


### 흑백 효과
```c
  // 픽셀 셰이더에서 흑백 효과를 결정하는 방법
float4 ps_main( PS_INPUT input) : COLOR
{   
   float4 tex = tex2D(SceneSampler , input.mUV);

   tex.rgb = dot(tex.rgb, float3(0.3, 0.59, 0.11));
   //아래 방법도 가능 하지만 더 정확한 값을 얻기 위해선 위의 비율 곱셈 권장
   //tex.rgb =  (tex.r + tex.g + tex.b) / 3;

   return tex;
}
```
 성능에서 조금 더 이익을 보기 위해서 dot 연산을 사용한다.  

 ![예시]({{ site.url }}/images/gray.jpg)

<br>
<br>


### 세피아 효과
```c
float4 ps_main( PS_INPUT input) : COLOR
{   
   float4 tex = tex2D(SceneSampler , input.mUV);

   float4 sepia;
   sepia.a = tex.a;

   sepia.r = dot(tex.rgb ,float3(0.393f, 0.769f, 0.189f));
   sepia.g = dot(tex.rgb ,float3(0.349f, 0.686f, 0.168f));
   sepia.b = dot(tex.rgb, float3(0.272f, 0.534f, 0.131f));


   return sepia;
}
```
 일부 수식은 제외하고 방법은 위와 같다.

 ![예시]({{ site.url }}/images/sepia.jpg)
