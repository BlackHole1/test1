# @netless/combine-player

同步 白板回放 和 video 的项目

此项目用于代替 `white-web-sdk` 中的同步功能，`white-web-sdk@2.11.2` 版本后将取消同步功能，并有此项目接手。

## 由来

因 `white-web-sdk` 在同步过程中，没有很好的适配 `video` 丢帧的问题。如果在 `white-web-sdk` 里修复丢帧，将会增加代码的复杂度，所以把同步功能单独抽成一个 `project` 来进行维护。

## 安装

### yarn

```shell
yarn add @netless/combine-player
```

### npm

```shell
npm install @netless/combine-player
```

## 快速上手

```typescript
const CombinePlayerFactory = require("@netless/combine-player");

whiteWebSdk.replayRoom({room: "$UUID", roomToken: "$ROOM_TOKEN"})
    .then(async (player) => {
        const factoryParams = {
            url: "https://my-domain/assets/rtc-record.mp4",
            videoDOM: videoDom, // 用于存放视频播放器的 div 节点
        };

        const combinePlayer = new CombinePlayerFactory(player, factoryParams).create(); 
    });

// 用户点击播放时触发函数
const playButton = () => {
  combinePlayer.play();
}
```

## 兼容

如果您的项目之前使用了 `white-web-sdk@2.11.2` 之前的版本来做同步，并因为业务需求想进行升级时，则需要参考下面的说明，来替换您的项目代码

在使用此项目后，您不应该在直接操作 `white-web-sdk` 里的 `player` 对象，而应该操作 `combinePlayer` 对象。

### 初始化

在 `white-web-sdk@2.11.2` 之前的版本中，我们一直推荐您在页面加载完成的时候，去调用: `player.seekToProgressTime(0)`，来进行初始化回放数据。

而现在这一步操作已经被集成到 `@netless/combine-player` 里了，您不需要在去调用 `player.seekToProgressTime(0)`，如果因为历史问题，必须要调用的话，也请调用 `combinePlayer.seek(0)`。

### 成员属性

#### [playbackSpeed](https://developer.netless.link/javascript-zh/home/player-methods#playbackspeed)

此方法目前由 `@netless/combine-player` 的 `playbackRate` 属性代替。

其用法为:

```typescript
combinePlayer.playbackRate;
```

#### [timeDuration](https://developer.netless.link/javascript-zh/home/player-methods#timeduration)

此方法目前由 `@netless/combine-player` 的 `timeDuration` 属性代替。

其用法为:

```typescript
combinePlayer.timeDuration;
```

其返回值和之前的不一致，详情请参考:

### 成员方法

#### [player.play()](https://developer.netless.link/javascript-zh/home/player-methods#play)

此方法目前由 `@netless/combine-player` 的 `play` 方法代替。

其用法为:

```typescript
combinePlayer.play();
```

#### [player.pause()](https://developer.netless.link/javascript-zh/home/player-methods#pause)

此方法目前由 `@netless/combine-player` 的 `pause` 方法代替。

其用法为:

```typescript
combinePlayer.pause();
```

#### [player.seekToProgressTime(progressTime)](https://developer.netless.link/javascript-zh/home/player-methods#seektoprogresstime)

此方法目前由 `@netless/combine-player` 的 `seek` 方法代替。

其用法为:

```typescript
combinePlayer.seek(ms);
```

#### [player.stop()](https://developer.netless.link/javascript-zh/home/player-methods#stop)

此方法目前由 `@netless/combine-player` 的 `stop` 方法代替。

其用法为:

```typescript
combinePlayer.stop();
```

#### 注意事项

##### 进度

如果您想获取整体的回放进度，可继续使用 `player.progressTime` 来进行获取，因 `@netless/combine-player` 本身就是负责同步的，所以 `回放` 和 `video` 的进度是一致的。

##### 状态

如果您想获取当前回放的进度，您可以继续使用 `player.phase` 来获取，当然最好(**十分推荐**)是使用 `@netless/combine-player` 的成员方法来进行获取。

关于 `@netless/combine-player` 的状态获取，可参考:

## 接口

### 实例

```typescript
const combinePlayerFactory = new CombinePlayerFactory(player, videoOptions, debug);
```

#### 参数

**player**

其中 `player` 为 `replayRoom` 方法创建，具体可见: [ 构造 Player 对象](https://developer.netless.link/javascript-zh/home/construct-room-and-player#%E6%9E%84%E9%80%A0-player-%E5%AF%B9%E8%B1%A1)

**videoOptions**

```typescript
interface VideoOptions {
    readonly url: string;
    readonly videoElementID?: string;
    readonly videoDOM?: HTMLVideoElement;
    readonly videoJsOptions?: VideoJsPlayerOptions;
}
```

**url(required)**

选择要回放视频的 video 地址，以便进行同步

**videoElementID(optional)**

表明要选择哪一个 `video DOM` 元素的 `id`

如果此 `id` 的元素不是 `video` 将会报错

> 如果同时传入 `videoElementID` 和 `videoDOM` 程序将会报错

**videoDOM(optional)**

表明要选择哪一个 `video DOM` 元素

如果元素不是 `video` 将会报错

> 如果 `videoElementID` 和 `videoDOM` 都没传入，程序将自动创建一个 `video` 元素。您可以通过: `getVideoDOM` 方法来获取此元素，详情可参考: 

**videoJsOptions(optional)**

video.js 实例化时的参数，详情可见: [Video.js Options](https://docs.videojs.com/tutorial-options.html)

默认情况下 `@netless/combine-player` 将会传入:

```typescript
{
    preload: "auto"
}
```

**debug(optional)**

是否开启 `debug` 模式，此模式将会把 `@netless/combine-player` 运行时的日志打印到 `console` 里

其默认值为: `false`

#### 成员方法

##### getVideoDOM

获取 `video` 的 `DOM` 元素

```typescript
const combinePlayerFactory = new CombinePlayerFactory(player, videoOptions, debug);
combinePlayerFactory.getVideoDOM();
```

##### create

创建 `combinePlayer` 对象

```typescript
const combinePlayerFactory = new CombinePlayerFactory(player, videoOptions, debug);
const combinePlayer = combinePlayerFactory.create();
```

其成员方法参考: [combinePlayer](#combinePlayer)

### combinePlayer

```typescript
const combinePlayerFactory = new CombinePlayerFactory(player, videoOptions, debug);
const combinePlayer = combinePlayerFactory.create();
```
