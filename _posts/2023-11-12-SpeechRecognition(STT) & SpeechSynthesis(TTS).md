---
layout: posts
title:  "SpeechRecognition(STT) & SpeechSynthesis(TTS)"
date:   2023-11-12 12:15:00 +0900
categories: Feature, JavaScript
---
<aside>
💡 JavaScript로 STT/TTS를 구현해봅시다!

</aside>

### STT : SpeechRecognition

```html
<a onclick="startRecord();">
	<img src="img/mic.svg">
</a>
```

```jsx
function availabilityFunc() {
	recognition = new webkitSpeechRecognition() || new SpeechRecognition();
	recognition.lang = "ko";
	recognition.maxAlternatives = 5;
	
	if (!recognition) {
	    alert("현재 브라우저는 사용이 불가능합니다.");
	}
}

function startRecord() {
	recognition.addEventListener("result", function(e) {
	    searchConsole.value = e.results[0][0].transcript;
	});
	
	recognition.start();
}

window.addEventListener("load", availabilityFunc);
```

---

### TTS : SpeechSynthesis

```jsx
let utterance = new SpeechSynthesisUtterance("안녕하세요?");

utterance.lang = 'ko';
utterance.rate = 1;    //말하는 속도(0 ~ 10)
utterance.voice = speechSynthesis.getVoices()[116];    //한국어 - 유나
utterance.volume = 1;    //말 소리 크기(0 ~ 1)
utterance.pitch= 0.8;    //말하는 톤 크기(0 ~ 2)

speechSynthesis.speak(utterance);

console.log(speechSynthesis.getVoices());    //사용가능한 목소리 확인하기
```

---

### 참고링크

[Using the Web Speech API - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API/Using_the_Web_Speech_API#speech_recognition)

---