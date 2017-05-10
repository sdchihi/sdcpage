---
layout: post
title: "Edge Dectection ( 외곽선 찾기 )"
categories: Shader
tags: [Shader]
image:
  feature: shader.jpg
  teaser: shader.jpg
  credit:
  creditlink:
---



# Edge Detection ( 외곽선 찾기 )
<br>
### 기본 아이디어

* Post processing
* 주변 픽셀과 비교했을때 명암 또는 색조의 차이가 큰 픽셀이 외곽선이 된다고 볼 수 있다.
* 주변에 있는 픽셀마다 가중치를 곱한 뒤 더한 값으로 현재 픽셀 값을 변경하는 `Convolution`을 사용한다.

<br>

>Convolution의 곱 연산에 쓰이는 행렬 형태의 가중치 집합을 **Kernel** 이라고 한다.

<br>

#### 소벨 연산자

외곽선 찾기에 사용될 커널로 소벨 연산자를 사용 하기로 한다.

![예시]({{ site.url }}/images/sobel_oper.jpg)
<br>

 Kx를 통해 좌우 외곽선을, Ky를 통해 상하 외곽선을 검출할 수 있다. <br>
 소벨 연산자를 통해 픽셀을 계산했을때 결과로 나온 값의 절대값이 커질수록 외곽선이 더 확실하게 드러나게되고 0에 가까울수록 외곽선이 희미해진다.

<br>

#### 구현 방법
* 이중 반복문을 통해 주변 픽셀에 접근
* 각각 주변 픽셀마다 UV 좌표값 계산
* 계산한 좌표의 텍스쳐 값을 읽은 후 명암 값만 따로 가져온다. ( dot 연산 )
* 명암 값에 Kx, Ky 를 곱해준다.
* 주변 픽셀 계산이 모두 끝나면 x,y 를 합치기 위해 제곱근 연산을 진행한다. (sprt)



#### 주변 픽셀에 접근하는 방법
 주변 픽셀값을 읽어와야 하는데 곧바로 접근하는 방법이 없기 때문에 UV좌표 계산을 통해 접근한다. <br>
 U 축은 1 / 텍스처 너비 <br>
 V 축은 1 / 텍스처 높이 <br>
 위의 값만큼 현재 픽셀에서 각각 떨어져 있다고 할 수 있는데 구현하는 과정에서 전역변수로 임의의 값을 넣어줘서 처리하였다.

``` c++
// 픽셀 셰이더에서 이뤄지는 구현 과정

for( int y = -1; y <= 1; ++y){
   for(int x = -1; x <=1; ++x){

      // 주변 픽셀에 접근후 텍스처 값 얻어오는 부분
      float2 offset = float2(x,y) * gPixelOffset;
      float3 tex = tex2D(SceneSampler, input.mUV+offset).rgb;

      //dot 연산 통해서 명암 값만 추출
      float luminance = dot(tex, float3(0.3, 0.59, 0.11));

      // Lx, Ly 는 픽셀들에 커널이 곱해져 합쳐진 값을 저장할 변수
      Lx += luminance * Kx[y+1][x+1];
      Ly += luminance * Ky[y+1][x+1];
   }
}

// Convolution 결과를 내기위해 제곱근 연산을 이용한다.
float L = sqrt((Lx*Lx) + (Ly+Ly));

return float4(L.xxx, 1);

```

<br>
![예시]({{ site.url }}/images/Edge.jpg)
<br>
