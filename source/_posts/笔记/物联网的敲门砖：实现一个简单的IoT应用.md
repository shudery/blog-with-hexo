---
title: 物联网的敲门砖：实现一个简单的IoT应用
date: 2017-10-08 23:32:17
tags: [技术研究]
categories: 笔记
---

物联网概念的提出已久，业界早就有相应的实践和应用，受限于网络带宽和传感器技术，尚未普及，此外这种偏智能化的场景，注定需要一定的时间才能走进大众家庭。在物联网中，技术应用架构是怎样的，与传统互联网有何区别？为了弄清其中的关系和技术栈，我通过查阅资料，实现了一个典型的物联网智能场景，在这个过程随手整理了一些资料以及技术实现过程中踩的一些坑。

物联网和传统互联网的 CS 架构其实是相似的，都是前后端的交互通信，区别在于物联网往往有一个局域网链接客户端，局网中的设备 通过一些轻量的通讯协议交互，比如流行的 MQTT，也有直接使用蓝牙或者 WLAN 的，还有一台网关/路由作为中台，收集来自区域设备的数据，最终传递给云端服务器。如果硬要说还有什么区别，那就是在服务器端，往往还涉及到分布式，数据分析这些架构模块。以此来支持处理和分析来自设备的大量上传数据。

物联网的三个过程是：收集数据，传递数据和分析数据。对于收集数据而言，主要依赖传感器和相关的硬件模组，这也是软件工程师接触物联网比较感兴趣和有挑战的地方，因为涉及到一些硬件的技术内容；另外传递数据方面虽然依然采用 IP/TCP 的协议，但是相比传统的互联网，在应用层通讯协议方面已经不局限于采用 HTTP/HTTPS 了，在局域网中，依赖轻量级，低功耗的传输协议可以更大的发挥物联网设备的特点；至于数据分析层面则是现在飞速发展的互联网技术分支，也是目前物联网中相对成熟的模块，通过大数据实现数字世界和现实世界的 Digital Twin，通过数字世界的操控影响现实设备的表现。未来可以支持接入海量设备信息的大数据处理平台必定极具竞争力。在数据处理这一块由于我实现的只是一个简单的活动告警器，只是做一些简单的活动频率统计，所以不会实际都分布式和数据分析的内容。

上班的时候经常有这样一种场景，主管常常不在位置上，有时有事情想找主管，但又不是那种需要打电话直接找过去的事情，所以会特别希望主管一回到位置就能收到一个通知。刚好最近在看物联网的一些资料，这就衍生了我做一个监控告警的 IoT 应用的想法（没错，监视主管。目前暂时只实现了单设备，后续考虑接入多个监控设备，这样常年外出公干的大佬们一回来，我就能先过去挑几份好的礼物了。

说一下实现的思路，开始也说了，物联网关键的地方还是传感器和控制芯片这一块，其他实现方法用老的互联网那一套即可。由于之前接触过一些单片机和树莓派，对这些集成度高的板子还是比较放心的，考虑到社区生态和功能全面，所以就先用一台 Rspaberry 3B 来做实验（投入实用可以用一些更小的模组，比如 omega)，然后在某宝买了一套适配树莓派的传感器和杜邦线。其实一开始是打算用移动公司的 IoT 开发平台的，它提供了自带感应器的芯片模组，但是申请的开发板一直迟迟不给我发货，而且平台的 SDK 包是用 C 的，想写脚本的我不犹豫的跑路了。

由于是单设备的接入，而且树莓派本身就是一台功能强大的 Linux 服务器，所以直接拿来做中台，收集的数据发到搭架在我电脑上的服务器，服务器处理，分析，存储数据，同时提供操控物联网设备的服务以及推送 App 监控器报警的功能。App 采用 apache 给阿里孵化的全端开发框架 weex 写，这样可以一口气搞定 web，android，ios 三个平台的应用。app 可以控制活动监控器的告警灯以及是否开启监控。

先从设备客户端开始搞起，用 python 或者 js 都有相应支持树莓派 GPIO 接口的包文件，由于最近在看 Node-RED 这个基于 Nodejs 的可视化物联网编程框架，所以我就直接用 Nodejs 和 rpio 包写了。

```js
//监听传感器输出引脚
rpio.open(LISTENT_PIN, rpio.INPUT, rpio.PULL_DOWN);
rpio.poll(LISTENT_PIN, pin => {
    // log(':the state of pin ' + pin + ' is '+ rpio.read(pin))
    //将监听信息推送到app
    log('[rpio->app-hock]request ' + WEB_HOCK);
    var time = new Date().toString().split(' ').splice(1,4).join('-');
    var cmdStr = `curl '${WEB_HOCK}' \
      -H 'Content-Type: application/json' \
      -d '
    {
      "text": "[${time}]检测范围有人活动..",
    }'`;
    exec(cmdStr, function(err,stdout,stderr){
            err && log(err);
            // console.log(stdout);
    });
    //将监听信息发送至控制平台
    log('[rpio->server]request ' + CONSOLE_URL + '/warnSignal');
    CONSOLE_URL && http.get(CONSOLE_URL+'/warnSignal',(res)=>{
        log('[server->rpio] ',res);
    }).on('error',e=>{
        log('connect console server '+CONSOLE_URL+' failed.');
    })
    //只有在告警功能打开时才会闪烁
    isWarnLEDFuncOn && twinkLED(WARNLED_PIN, 0.5, 2);
```

硬件方面比较简单，买了集成度高的活动传感器，支持 3-10 米的检测范围，0.5s-300s 的报警延时功能，参照树莓派的 GPIO 引脚功能表连接设备和 LED 灯，LED 灯接 3.3V 的引脚就够了，最好还要加个电阻，不加也没啥事。监听对应的引脚的跳高电压，当监听到活动时会给树莓派发送一个波峰电压。接到报警之后将信息传给后端服务器，点亮并闪烁报警的 LED 灯，此外由于告警数据不需要服务器进行处理，所以我直接发给了可以推送信息给 app 的 webHock 地址。这个服务很多类 slack 的协作工具都有提供，我这里用的是 bearychat 的 incoming 机器人。

然后就是写设备提供的接口，服务器通过这些接口可以控制设备的一些功能，比如 LED 灯的开关，告警功能的开关。还有要实现的就是 LED 灯的开关和闪烁函数，这个结合 rpio 的 API 文档，可以快速实现。

```js
//提供服务
server({ port: SERVER_PORT }, [
  //告警灯状态可控
  get('/offWarnLED', ctx => {
    log(' offWarnLED request is coming..');
    offLED(WARNLED_PIN);
  }),
  get('/onWarnLED', ctx => {
    log(' onWarnLED request is coming..');
    onLED(WARNLED_PIN);
  }),
  get('/twinkWarnLED', ctx => {
    log(' twinkWarnLED request is coming..');
    twinkLED(WARNLED_PIN, 1);
  }),
  get('/changeWarnLED', ctx => {
    log(' changeWarnLED request is coming..');
    changeLED(WARNLED_PIN);
  }),
  //是否开启告警灯功能
  get('/onWarnFunc', ctx => {
    isWarnLEDFuncOn = true;
    log(' Warn Functoin is open now..');
  }),
  get('/offWarnFunc', ctx => {
    isWarnLEDFuncOn = false;
    log(' Warn Functoin is close now....');
  }),
  get('/changeWarnFunc', ctx => {
    isWarnLEDFuncOn = isWarnLEDFuncOn ? false : true;
    log(' Warn Functoin is' + isWarnLEDFuncOn + 'now....');
  })
]);
```

服务端方面本来是打算通过 webSocket 来和应用端实时通信，展示告警的状态，但是踩了 weex 的一个坑，没有搞出来，weex 的 android 中没有原生的 webSocket 实现，导致我在 web 调通的功能安装到 android 模拟器里后就不行了，后来实在找不齐 webSocket 实现所需要的库文件，而且考虑到场景不是很需要实时交互，所以就没有用这个方案了，后来想想其实用 ajax 长轮询做应该就没问题了。其他的功能就是接受设备的数据并储存，提供接口转发 app 端的控制请求给设备端，以及处理请求存储的告警信息内容。

```js
//转发改变LED状态的请求
app.get('/changeWarnLED', (req, res) => {
  log('[app->server]changeWarnLED');
  http
    .get(iotRouterUrl + '/changeWarnLED', resp => {
      res.send('[server->app]ok!');
    })
    .on('error', e => {
      log(e);
    });
});
//接收，存储设备的告警信息
app.get('/warnSignal', (req, res) => {
  log('[rpio->server]somebody have a action now.');
  res.send('[server->rpio]The warn msg send success.');
  //存储信息
  var dateInfo = new Date()
    .toString()
    .split(' ')
    .splice(1, 3)
    .join('-');
  var timeInfo = new Date()
    .toString()
    .split(' ')
    .splice(4, 1)
    .join('-');
  var store = {
    timeLabel: Date.now(),
    dateInfo,
    timeInfo,
    msgType: 'warnSignal'
  };
  //存储报警信息
  fs.readFile('store.json', 'utf-8', (e, data) => {
    e && console.log(e);
    var data = JSON.parse(data);
    data.data.push(store);
    fs.writeFile('./store.json', JSON.stringify(data), e => {
      e && console.log(e);
    });
  });
});
//返回报警数据
app.get('/data', (req, res) => {
  log('[app->server]getData');
  fs.readFile('./store.json', 'utf-8', (e, data) => {
    e && console.log(e);
    log('data number:' + JSON.parse(data).data.length);
    res.send(data);
  });
});
```

应用端采用 weex/vue 的 js 全端开发框架，简单的写几个按钮，添加请求相应的接口地址，就能达到控制设备的作用。通过 weex-toolkit 可以在 web 端调试，打包，注意这个过程要有相应的 android 和 ios 开发环境，也就是说 jdk，android-sdk，安卓模拟器，xCode 都是需要的，这方面可以看看 weex 的 helloWorld 教程搭下环境。

在树莓派中跑起代码

```js
pi@raspberrypi:~/iot/demo $ node rpio
[Oct-22-2017-15:01:42]server starts on 8080 port
[Oct-22-2017-15:02:11][rpio->app-hock]request https://hook.bearychat.com/=bwBAI/incoming/55725fec1b3e3b629c8929ba0d41fefe
[Oct-22-2017-15:02:11][rpio->server]request http://192.168.31.170:8066/warnSignal
[Oct-22-2017-15:02:12][server->rpio]
```

在服务端后台跑起 server

```js
@Daguo:) ~/code/iot/demo 13:38:57 >>>master!
$ node console.js
[Oct-22-2017-13:38:58]start server at 8066.
[Oct-22-2017-13:39:54][rpio->server]somebody have a action now.
[Oct-22-2017-14:09:44][rpio->server]somebody have a action now.
[Oct-22-2017-15:02:12][rpio->server]somebody have a action now.
[Oct-22-2017-15:08:54][app->server]changeWarnLED
[Oct-22-2017-15:08:55][app->server]changeWarnLED
```

当监控到环境中有物体活动后，bearyChat 应用会收到弹出消息，通过 app 也可以控制设备 LED 灯，至此完成了物联网应用的基础功能，更加详细的代码放在我的[github](https://github.com/shudery/iot)上。后续还需要改进的地方包括接入多设备的支持，同时要采用新的轻量级协议连接局域网设备，路由对多设备数据的收集和发送，服务端的数据分析和计算，应用端的可视化数据界面展示。
