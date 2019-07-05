var tj = new TJBot(["led","servo","camera", "microphone","speaker"],
                   {
  					 see: {
                       confidenceThreshold : {
                         object : 0.5,
                    	 text: 0.1
                       },
                       camera: {
                    	height: 720,
                    	width: 960,
                    	verticalFlip: false,
                    	horizontalFlip: false
                       }
                     },
  					 listen: {
					   language: "ja-JP"
                     },
  					 robot: {
                       gender: "male"
                     },
                     speak: {
                       language: "en-US"
                     } 
                   },
                   {
                      visual_recognition: {
                      	apikey: "g0h123kjf5h3m620n5h1175mrk54h32vc54ji543"
                      },
                      speech_to_text: {
                        username: "cf63b1f3-ef18-4628-86c8-6b1871e076b9",
                        password: "MWNwz3qcdIab"
                       },
 				      language_translator: {
                        apikey: "EYWHhoht3zoyz1LH4RL6sPbYZRTOWAKbTiGWI2xT3mli"
                      },
                      text_to_speech: { // Lab ではこうだけれど、aim_apikey が使える
						apikey : "b_o3vFwmY7GP2mHMBH3DjYWZkDZPQCrX1P8V1gRVxbqx"
                      }, 
                      tone_analyzer: {
						apikey : "Ecf4mv325YQaiadH8625bpmqv0wmkbiMf8zQ3JziXC-y"
                      }
         		   });

// Lab.1
tj.lowerArm(); // 下位置
tj.raiseArm(); // 真ん中
tj.armBack();  // 上位置

tj.wave();		// 中下中

// Lab.2
tj.shine("red"); // 色変わる
tj.pulse("pink", 5.0); // フェードイン・フェードアウト

// Lab. 3
 // VR 返ってくる答えは、物体についてと色について
tj.see((response) => {
  console.log(response.map(i => i.class).join(", "));
});
 // 写真撮影のみ
//tj.takePhoto("/home/pi/").then((filePath) => {
//});

// Lab.4 
//  Speech to Text
tj.listen((text)=>{
  console.log(text);
//tj.pauseListening(); // エミュレータに無い
//tj.resumeListening();// エミュレータに無い
tj.stopListening();

});

// Lab.5
var text ="bonjour";
// 入力された文字列が、何言語なのかを識別する
tj.identifyLanguage(text).then((langs) => {
  var sourceLanguage = langs.languages[0].language;
  var targetLanguage = "en";
  // 入力された言語から、翻訳したい言語を指定して、
  // 翻訳できるかどうかをチェックする
  tj.isTranslatable(sourceLanguage, targetLanguage).then( res => {
    if(res) 
    {
      // 実際の翻訳
      tj.translate(text, sourceLanguage, targetLanguage).then(
        	(trans) => {
             	console.log(trans.translations[0].translation); 
            });
    } else {
      console.log(`unable to translate from ${sourceLanguage} to ${targetLanguage}`);
    }
  });
});

// Lab.6 
//tj.speak("hello mizuki");

// Lab. 7
var text2 = "This is an amazing demo full of examples.";
tj.analyzeTone(text2).then( response =>
{
  var emotions = response.document_tone.tone_categories[0].tones;
  var top = emotions[Object.keys(emotions).reduce( (a, b) =>
    {
      return emotions[a].score > emotions[b].score ? a : b;
    })];
  console.log(` top tone: ${ top.tone_id}`); 

});