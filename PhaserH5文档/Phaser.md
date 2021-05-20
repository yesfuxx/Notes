## PhaserH5试玩游戏

+++

### 1 简介

Phaser是一个HTML5游戏框架，它的目的是辅助开发者真正快速地制作强大的、跨浏览器的HTML5游戏

- 优点：功能强大
- 缺点：平台对试玩游戏有空间大小限制，素材过多将超过平台限制，而phaser api 占用1MB，可以考虑使用原生js开发

### 2 试玩小游戏制作流程

创意→素材制作→确定实际游戏中素材大小(压缩图片，音频，视频)→编码→压缩代码

所需基础：css，js，编码过程中参考官方示例和查阅文档

资源加载具有跨域问题，需先将资源文件转换成base64数据，再读出使用

### 3  API对接

##### 3.1 抖音头条

- 1将下面代码放入 body 中，且在开发者自己的 js 代码之前

<script src="https://sf3-ttcdn-tos.pstatp.com/obj/union-fe-nc/playable/sdk/playable-sdk.js" data-tomark-pass  data-tomark-pass  data-tomark-pass ></script>

- 2调用window.playableSDK.openAppStore();

![image-20210520144237188](C:\Users\fudengyi\Desktop\新建文件夹 (4)\image-20210520144237188.png)

##### 3.2 facebook

<img src="C:\Users\fudengyi\Desktop\新建文件夹 (4)\image-20210520144353580.png" alt="image-20210520144353580" style="zoom:50%;" />	

- 只需添加 FbPlayableAd.onCTAClick();

##### 3.3 google

<img src="C:\Users\fudengyi\Desktop\新建文件夹 (4)\image-20210520144756704.png" alt="image-20210520144756704" style="zoom:50%;" />	<img src="C:\Users\fudengyi\Desktop\新建文件夹 (4)\image-20210520145016471.png" alt="image-20210520145016471" style="zoom: 33%;" />

- 1.HTML <head> 标记中添加以下脚本

  <script type="text/javascript" src="https://tpc.googlesyndication.com/pagead/gadgets/html5/api/exitapi.js"> </script>

- 2.然后执行以下 JavaScript 调用以启用最终到达网址：ExitApi.exit()

###  4  案例模板

```
<!doctype html> 
<html lang="en"> 
<head> 
    <meta charset="UTF-8" />
    <title>示例模板</title>
    <meta name="viewport" content="width=device-width,initial-scale=1, minimum-scale=1, maximum-scale=1 user-scalable=no"/>
    <meta name="apple-mobile-web-app-capable" content="yes"/>
    <meta name="full-screen" content="true"/>
    <meta name="screen-orientation" content="portrait"/>
    <meta name="x5-fullscreen" content="true"/>
    <meta name="360-fullscreen" content="true"/>
    <style type="text/css">
        body{
            width: 100%;
            height: 100%;
            margin: 0;
            background-color: rgb(0, 0, 0);
        }
        .parent{
            width: 100%;
            height: 100%;
            position: absolute;
            z-index: 0;
            background-color: rgb(0, 0, 0);
        }
        /*  view1  */
        #logoStart{
            height: 16%;
            margin-top: 50%;
            z-index: 50;   
            position: relative;
            display: block;
            margin-bottom: 2%;
        } 
        /*  view2  */
        #phaser-example{
            width: 100%; 
            height: 100%;  
            position: absolute;
            z-index: 2;
            display: none;
        }
        #view2{
            display: none;
        }
    </style>
    <!-- 资源base64文件,如：
    var data_asset01 = 'data:image/png;base64,iVB...'; -->
    <script src="js/assetdata.js"></script>
    <!-- phaser 本地api，下载链接：http://phaser.io/download/stable -->
    <script src="js/phaser.min.js"></script>
    <!-- 3.使用google api时，需用添加下方脚本 -->
    <!-- <scripttype="text/javascript" src="https://tpc.googlesyndication.com/pagead/gadgets/html5/api/exitapi.js"> </script> -->
</head>
<!-- 1.使用抖音头条api时，需用添加下方脚本 -->
<!-- <script src="https://sf3-ttcdn-tos.pstatp.com/obj/union-fe-nc/playable/sdk/playable-sdk.js" data-tomark-pass  data-tomark-pass ></script> -->

<body onload="myload()">
    <div class='parent' id='view1' align="center">
        <img id='logoStart'>
    </div>      
    <div class='parent' id='view2' align="center">        
        <div id='phaser-example'></div>
    </div>    
    <script type="text/javascript">    
        function myload(){
            console.log('加载页面');       
            //示例：js设置图片     
            document.getElementById("logoStart").setAttribute("src", data_asset01);  
        }
        function callAdFuc() {
            try {                
                window.playableSDK.openAppStore();//1.调用抖音头条api(还需再index.html同目录下添加config.json,json内容添加“{ "playable_orientation": 0 }”，0横竖)
                // FbPlayableAd.onCTAClick();     //2.facebook api   
                // ExitApi.exit();                //3.google api
            } catch (error) {
                console.log(error);
            }                 
        }
    //#region boot 资源加载
        class Boot extends Phaser.Scene{
            constructor(){
                super('boot');
            }
            preload(){
                //示例：加载视频资源
                this.load.video('video1', data_video1, 'loadeddata', false, false);               
            }            
            create(){       
                //添加初始化事件监听，只执行一次
                this.events.once('initial', this.initial, this);
                this.loadAssets();               
            }
            update(){
                //监听资源加载完成，执行初始化
                if(this.loadIndex < this.loadNums){                 
                    return;
                }else{                    
                    //加载完成后，执行初始化
                    this.events.emit('initial');
                }
            } 
            /**
             * 总加载图片和音频资源
             */
            loadAssets(){
                this.loadIndex = 0;                
                this.loadNums = 2;//下面有几条加载就是几
                this.loadAsset_Audios();//加载所有音频
                this.loadAsset_Image(data_asset01, 'asset01');//加载图片     
                this.loadAsset_Image(data_asset02, 'asset02');//加载图片  
            }
            /**
             * 加载资源——图片
             * @param {*} imgData base64数据图片
             * @param {*} key 创建名为key的texture>>>获取this.textures.get(key); 创建this[key+ '_dom']的HtmlImageDom节点
             */
            loadAsset_Image(imgData, key, spriteSheet = 'false'){
                let img = new Image();
                img.src = imgData;
                //图片加载完成执行
                img.onload = ()=>{//图片加载完成执行
                    //添加Texture    
                    this.textures.addImage(key, img);
                    //添加HTMLImageElement
                    if(spriteSheet){
                        this[key + '_dom'] = img;
                    }                      
                    ++this.loadIndex; //资源加载完成数+1
                };
            }
            /**
             * 加载资源——音频
             */
            loadAsset_Audio(data, key){
                this.sound.once('decoded', ()=>{//音频加载完成执行                    
                    ++this.loadIndex;//资源加载完成数+1
                });
                this.sound.decodeAudio(key, data);
            }
            loadAsset_Audios(){
                let audioFiles = new Array(        
                    { key: 'audio_01', data: data_asset_audio01 },
                    { key: 'audio_02', data: data_asset_audio02 }                              
                );
                this.sound.once('decodedall', ()=>{//音频加载完成执行                    
                    ++this.loadIndex;//资源加载完成数+1
                });
                this.sound.decodeAudio(audioFiles);
            }
            /**
             * 资源加载完成后执行一次
             */
            initial(){
                this.time.addEvent({ delay: 200, callback: ()=>{
                    console.log('——————————loadAsset succuss!——————————');               
                    this.scene.start('game');//关卡跳转
                }, callbackScope: this, repeat: 0 });
            }
        }
    //#endregion 

    //#region game
        class Game extends Phaser.Scene{
            constructor(){
                super('game'); 
            }
            create(){
                console.log("gaming");
                document.getElementById("view1").style['display']= 'none';
                document.getElementById("view2").style['display']= 'block';
                document.getElementById("phaser-example").style['display']= 'block';                
            }                   
        }
    //#endregion

    //#region main
        var config = {
            type: Phaser.AUTO,
            backgroundColor: '#0x000000',                
            scale: {
                mode: Phaser.Scale.FIT,
                parent: 'phaser-example',
                width: 900,
                height: 1600
            },
            scene:[Boot, Game]
        };
        var game = new Phaser.Game(config);
    //#endregion
</script>
</body>
</html>
```

### 5 资源链接

- 资源转化base64：https://base64.us/
- js代码压缩：https://jsmin.bmcx.com/
- 官方文档：https://photonstorm.github.io/phaser3-docs/
- 官方案例：http://phaser.io/examples



