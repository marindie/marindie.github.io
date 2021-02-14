---
toc: true
title: "AdSense 광고 삽입 방법들. 광고 종류 및 사용방법 공유"
description: "AdSense 의 자동광고 만으로는 한계가 있어, 수동으로 코드를 삽입하면서 블로그의 광고를 꾸며 봤습니다. 진행한 내용을 정리한 글입니다." 
categories: [Adsense]
tags: [Adsense]
redirect_from:
  - /2021/02/02/
---

> AdSense 의 자동광고 만으로는 한계가 있어, 수동으로 코드를 삽입하면서 블로그의 광고를 꾸며 봤습니다. 진행한 내용을 정리한 글입니다.

### AdSense 광고란? {#toc1}

- 구글의 AdSense 는 사용자 블로그의 적합도를 판단하여, 광고 게제를 허용하고 있습니다.
- 블로그에 적당한 숫자의 글이 존재하고, 해당 사이트의 트레픽이 일정한 정도를 초과하면, 자동으로 승인이 나는 것 같습니다.
- Blogger.com 과 같은 사이트를 사용하는 경우는, 구글에서 자동으로 광고 개제 적합여부를 AdSense 에 노출해주고 있었습니다. 추가적으로 몇개의 사이트가 더 있습니다만, 지금은 잊어버렸네요.
- 쉽다면 쉽고, 어렵다면 어려운 블로그 승인. 주관적인 생각으론 시간 대비 돈으로 보상받는 부분은 아주 미미 합니다.
- 저 역시, 광고 개제를 구글의 광고 종류에 대한 이해 및 사이트 스타일을 어떻게 변경하면 내가 원하는 대로 할 수 있는지 공부 차원에서 진행한 바입니다.
- 돈이 너무너무 중요하시다면, AdSense 의 광고 적합성 여부에서 클릭률이 높은 광고외에 다른 광고들을 블럭하고
- 블로그에 최대한 많은 곳에 광고를 삽입하고
- 광고 클릭당 많은 비용을 지불하는 광고를 이해하셔서, 해당 카테고리의 광고만 노출 하시면 그나마 나으실 수 있습니다.
- 위의 모든 조건들에서 돈이 발생하는 최소 조건은 1000회의 노출 또는 1회 이상의 클릭입니다.
- 유투브의 광고 노출 방식이 그나마 돈이 더 되는 이유 또한, 광고 강제 노출이 있기 때문이겠죠.
- 블로그 사용자에게 돌아가는 수익은, 미국 광고가 한국 광고 보다 훨씬 금액이 높다는 것을 이제는 다들 알고 있으시겠죠.
- AdSense 광고 수익 랭크가 국가별로 랭크시 한국이 50위권, 일본이 20위권 인것만 봐도 한국어 블로그로 돈버는것은 많은 좌절부터 경험하게 합니다.

### AdSense 사이트 기준 광고 (자동광고) {#toc2}

- 자동광고를 사이트에 허용해 놓아야 광고들이 노출 되는 것으로 알고 있습니다.
- 이는 구글이 사이트를 분석하여 적당한 공간에 광고를 노출시켜줍니다.
- 아래 스샷에서 연필 모양의 아이콘을 누릅니다. (수정버튼)

![사이트 광고 기준](/assets/images/screen/사이트 기준 광고.png)

- 이후 아래의 사진처럼 기본 세팅을 확인 합니다.
- 자동광고 와 인페이지 광고가 활성화가 되면 가능한 광고가 자동으로 나온다고 보시면 됩니다.
- 일치하는 광고는 한번 써보시고 결정하시면 됩니다.
- 앵커 광고는 모바일에서 맨위 또는 맨아래에 계속 노출되고 화살표를 눌러서 숨기도록 하는 광고 인데, 제 블로그와는 안맞아서 껏습니다.
- 모바일 전면광고는 클릭시, 화면 전체에 광고가 노출되고 X 버튼을 눌러서 닫는 광고 형태입니다.
- 더 넓은 화면은 데스크톱에 고려되는 광고 사이즈 자동 처리 옵션입니다. 크고 넓은 광고가 나온다고 생각하시면 되겠습니다.

![자동광고설정](/assets/images/screen/자동광고설정.png)
![자동광고설정01](/assets/images/screen/자동광고설정01.png)

### AdSense 광고 단위 기준 (수동광고) {#toc3}

- 수동광고는 말그대로 페이지 전체의 영역에서 원하는 위치에 직접 광고를 다는 것입니다.
- AdSense 에서 만든 소스 코드는 주로 아래와 같습니다.
- 이런 광고 개제를 고민하기 위해서는 기본적으로 사이트 승인이 나야만 합니다. 시작은 블로그 글 부터 작성하는게 시작입니다.

```js
<script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
     style="display:block"
     data-ad-format="fluid"
     data-ad-layout-key="-fb+5w+4e-db+86"
     data-ad-client="ca-pub-5xxxxxxxxxx7"
     data-ad-slot="7xxxxxxx8"></ins>
<script>
     (adsbygoogle = window.adsbygoogle || []).push({});
</script>
```

- 아래의 스샷과 같이 광고 단위 기준 탭을 선택하고, 디스플레이 광고, 인피드 광고, 콘텐츠 내 자동 삽입 광고 중에 하나를 선택하면 해당 광고 종류를 등록하는 화면으로 넘어갑니다.

![광고단위기준](/assets/images/screen/광고단위기준.png)

- 디스플레이 광고를 예로 들겠습니다
- 기본 사각형으로 되어 있습니다.
- 커서는 해당 광고의 이름을 적는 곳에 깜박입니다. 이름을 적습니다.
- 큰 특별한 것은 대부분 없습니다. 이런 내용이 이해가 안된다면, CSS 와 반응형 WEB 에 대한 관심이 1도 없었다는 증거일 뿐입니다.
- 저는 대부분 반응형 광고를 선택하였습니다. 블로그가 반응형으로 되어 있기에 잘 맞는거 같습니다.
- 만들기 버튼을 누르면 위의 예제와 같은 스크립트 소스가 만들어 집니다.

```md
- 해당 소스를 <head></head> 태그 영역 안에서 사용하면 됩니다. 수동 광고들은 주로 <body> 태그 안에 삽입 합니다.
- <head> 태그 안에는 자동광고 및 애널리틱스 관련 스크립트를 넣습니다.
- 아래는 제 블로그의 <head> 태그 안에 넣은 Google Analytics 와 AdSense 자동광고를 위한 스크립트 입니다. 
```

```js
<!-- Global site tag (gtag.js) - Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id=UA-1XXXXXXXX-1"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', 'UA-1xxxxxx6-1');
</script>
<!-- 자동광고 세팅 -->
<script data-ad-client="ca-pub-5xxxxxxxx7" async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
```

![디스플레이광고](/assets/images/screen/디스플레이광고.png)

- 아래는 제가 만든 광고들입니다.

![광고단위기준_생성리스트](/assets/images/screen/광고단위기준_생성리스트.png)

- 제 블로그에 이런 광고를 직접 넣은 곳의 소스를 보여드리겠습니다.

![수동광고샘플소스](/assets/images/screen/수동광고샘플소스.png)

```md
- Footer 영역에 넣는 소스의 일부 입니다. 
- 저는 소스를 <div></div> 를 만들어서 div 안에 AdSense 에서 생성된 소스를 집어 넣습니다.
- 필요에 따라서 inline css style 도 넣어서 테스트 해보면서 사용합니다.
- 생성된 광고 소스안에는 <ins> 태그가 존재하는데요. 이 태그 안에 스타일이 존재 합니다. 
- 저는 광고 사이즈를 고정시키고 왼쪽 정렬해서 4개의 광고를 띄우는데 성공하였습니다.
- 이건 그때 사용한 스타일. style="display:block;width:300px;height:300px;float: left;"
```

### 중요한 팁 및 테스트 경험 공유 {#toc4}

- 한 줄에 4개의 광고를 작게 집어 넣어서 총 2줄 (총8개)의 광고를 노출 하는것을 다른분들 소스를 보고 따라해보려고 했는데, 소스를 여러개 붙이지 않고 간결하게는 성공하지는 못했습니다.
- 대신 같은 광고를 쪼개서 여러개 붙이는 방식은 성공하였습니다.
- 광고를 많이 달면, 화면 로드 시간의 부하가 발생합니다. 이로 인해, 광고가 사실은 노출이 되는것인데, 안보여서 본인이 뭔가 잘못했나 하고 많이 착각하게 됩니다.
- 그래서 자동광고가 노출이 되는것을 확인하신 분들은 무조건 화면 loading 처리를 해주는것을 추천드립니다.

### 화면 로딩 처리 {#toc5}

- css 파일안에 아래와 같은 스타일을 추가 합니다.
- 내용은 loading ID 가 정의 된 div 가 화면 전체를 잡아 먹도록 하고 백그라운드 색상을 약간 옅게 해준다 입니다.
- loading ID 안에 img 태그를 삽입하여 사용할 것이고, 이때 사용되는 이미지는 화면의 정중앙에 놓이게 됩니다.
- 이미지 파일은 svg 파일을 사용합니다.

```css
/* 로딩 화면 백그라운드 */
#loading {
width: 100%;
height: 100%;
top: 0;
left: 0;
position: fixed;
display: block;
opacity: 0.4;
background: black;
z-index: 99;
text-align: center;
}

/* 로딩 화면 이미지 */
#loading > img {
position: absolute;
top: 50%;
left: 50%;
z-index: 100;
}
```

![Spinner](/assets/images/Spinner-1s-207px.svg)

- 아래의 html 내용을 body 태그 안에 아무곳에나 둡니다.

```html
<div id="loading">
  <img src="/assets/images/Spinner-1s-207px.svg" alt="loading">
</div>
```

- 해당 html 태그를 무조건 노출 시키고, 화면 loading 이 끝나면 사라지도록 하는 스크립트를 추가 합니다.

```js
<script>
$(window).on('load', function() {
    $("#loading").hide();
});
</script>
```

### 반응형으로 4개의 이미지 광고 가로 또는 세로로 노출 {#toc6}

- AdSense 에서 수동 광고 소스를 하나 만드시고, 아래와 같은 형태로 작성해서 Footer 쪽에 붙여넣어 보세요
- Footer 에 넣는것을 추천하는 이유는, 일단 특별한 스타일들이 존재하는 경우가 드물어서, 왠만하면 잘 처리 된다는 장점이 있습니다.
- 많은 광고를 넣어도 크게 부담이 가지 않습니다.

```js
<div style="width: 100%; height: 300px;">
    <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
    <ins class="adsbygoogle"
         style="display:block;width:300px;height:300px;float: left;"
         data-ad-format="fluid"
         data-ad-layout-key="-6t+ed+2i-1n-4w"
         data-ad-client="ca-pub-5746287019974907"
         data-ad-slot="2575166022"></ins>
    <script>
         (adsbygoogle = window.adsbygoogle || []).push({});
    </script>
    <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
    <ins class="adsbygoogle"
         style="display:block;width:300px;height:300px;float: left;"
         data-ad-format="fluid"
         data-ad-layout-key="-6t+ed+2i-1n-4w"
         data-ad-client="ca-pub-5746287019974907"
         data-ad-slot="2575166022"></ins>
    <script>
         (adsbygoogle = window.adsbygoogle || []).push({});
    </script>
    <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
    <ins class="adsbygoogle"
         style="display:block;width:300px;height:300px;float: left;"
         data-ad-format="fluid"
         data-ad-layout-key="-6t+ed+2i-1n-4w"
         data-ad-client="ca-pub-5746287019974907"
         data-ad-slot="2575166022"></ins>
    <script>
         (adsbygoogle = window.adsbygoogle || []).push({});
    </script>
    <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
    <ins class="adsbygoogle"
         style="display:block;width:300px;height:300px;float: left;"
         data-ad-format="fluid"
         data-ad-layout-key="-6t+ed+2i-1n-4w"
         data-ad-client="ca-pub-5746287019974907"
         data-ad-slot="2575166022"></ins>
    <script>
         (adsbygoogle = window.adsbygoogle || []).push({});
    </script>            
</div>
```

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[My Blog]: https://marindie.github.io
