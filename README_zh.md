<div align="center">
<img width="120" style="padding-top: 50px; margin: 0;" src="http://47.104.180.148/go-captcha/gocaptcha_logo.svg?v=1"/>
<h1 style="margin: 0; padding: 0">Go Captcha</h1>
<p>行为式安全验证码</p>
<a href="https://goreportcard.com/report/github.com/wenlng/go-captcha"><img src="https://goreportcard.com/badge/github.com/wenlng/go-captcha"/></a>
<a href="https://godoc.org/github.com/wenlng/go-captcha"><img src="https://godoc.org/github.com/wenlng/go-captcha?status.svg"/></a>
<a href="https://github.com/wenlng/go-captcha/releases"><img src="https://img.shields.io/github/v/release/wenlng/go-captcha.svg"/></a>
<a href="https://github.com/wenlng/go-captcha/blob/master/LICENSE"><img src="https://img.shields.io/github/license/wenlng/go-captcha.svg"/></a>
<a href="https://github.com/wenlng/go-captcha"><img src="https://img.shields.io/github/stars/wenlng/go-captcha.svg"/></a>
<a href="https://github.com/wenlng/go-captcha"><img src="https://img.shields.io/github/last-commit/wenlng/go-captcha.svg"/></a>
</div>

<br/>

> [English](README.md) | 中文 

<p style="text-align: center">
<a style="font-weight: bold" href="https://github.com/wenlng/go-captcha">Go Captcha</a> 是一个简洁易用、交互友好、高安全性的 "行为式验证码" Go 库 ，支持文本点选式、图形点选式、滑动式、拖拽式、旋转式类型验证。
</p>

<p style="text-align: center"> ⭐️ 如果能帮助到你，请随手给点一个star</p>

<br/>

<div align="center"> 
    <img src="http://47.104.180.148/go-captcha/go-captcha-v2.jpg" alt="Poster">
</div>

<br/>

- GoCaptcha：[https://github.com/wenlng/go-captcha](https://github.com/wenlng/go-captcha)
- GoCaptcha 文档：[http://gocaptcha.wencodes.com](http://gocaptcha.wencodes.com)
- 后端Go实例：[https://github.com/wenlng/go-captcha-example](https://github.com/wenlng/go-captcha-example)
- 内嵌Go资源：[https://github.com/wenlng/go-captcha-assets](https://github.com/wenlng/go-captcha-assets)
- Vue Package：[https://github.com/wenlng/go-captcha-vue](https://github.com/wenlng/go-captcha-vue)
- React Package：[https://github.com/wenlng/go-captcha-react](https://github.com/wenlng/go-captcha-react)
- 在线演示：[http://gocaptcha.wencodes.com/demo](http://gocaptcha.wencodes.com/demo)
- ...
- 
<br/>

> ⚠️ v1 版本将在 v2.1 版本后移除，请尽快升级到 v2 版本

<br/>

### 设置Go代理
- Window
```shell
$ set GO111MODULE=on
$ set GOPROXY=https://goproxy.io,direct

### The Golang 1.13+ can be executed directly
$ go env -w GO111MODULE=on
$ go env -w GOPROXY=https://goproxy.io,direct
```
- Linux or Mac
```shell
$ export GO111MODULE=on
$ export GOPROXY=https://goproxy.io,direct

### or
$ echo "export GO111MODULE=on" >> ~/.profile
$ echo "export GOPROXY=https://goproxy.cn,direct" >> ~/.profile
$ source ~/.profile
```

## 安装模块
```shell
$ go get -u github.com/wenlng/go-captcha
```

## 引入模块
```go
package main

import "github.com/wenlng/go-captcha/capts"

func main(){
   // ....
}
```

<br />

## 🖖点选式验证码
### 快速使用
```go
package main

import (
	"encoding/json"
	"fmt"
	"image"
	"log"
	"io/ioutil"

	"github.com/golang/freetype"
	"github.com/golang/freetype/truetype"
	"github.com/wenlng/go-captcha/capts/base/option"
	"github.com/wenlng/go-captcha/capts/click"
	"github.com/wenlng/go-captcha/capts/base/codec"
)

var textCapt click.Captcha

func init() {
	textCapt = click.New(
		click.WithRangeLen(option.RangeVal{Min: 4, Max: 6}),
		click.WithRangeVerifyLen(option.RangeVal{Min: 2, Max: 4}),
	)

	fontN, err := loadFont("../resources/fzshengsksjw_cu.ttf")
	if err != nil {
		log.Fatalln(err)
	}

	bgImage, err := loadPng("../resources/bg.png")
	if err != nil {
		log.Fatalln(err)
	}

	textCapt.SetResources(
		click.WithChars([]string{"这", "是", "随", "机", "的", "文", "本", "种", "子", "呀"}),
		click.WithFonts([]*truetype.Font{
			fontN,
		}),
		click.WithBackgrounds([]image.Image{
			bgImage,
		}),
	)
}

func loadPng(p string) (image.Image, error) {
	imgBytes, err := ioutil.ReadFile(p)
	if err != nil {
		return nil, err
	}
	return codec.DecodeByteToPng(imgBytes)
}

func loadFont(p string) (*truetype.Font, error) {
	fontBytes, err := ioutil.ReadFile(p)
	if err != nil {
		panic(err)
	}
	return freetype.ParseFont(fontBytes)
}


func main() {
	captData, err := textCapt.Generate()
	if err != nil {
		log.Fatalln(err)
	}

	dotData := captData.GetData()
	if dotData == nil {
		log.Fatalln(">>>>> generate err")
	}

	dots, _ := json.Marshal(dotData)
	fmt.Println(">>>>> ", string(dots))

	err = captData.GetMasterImage().SaveToFile("../resources/master.jpg", option.QualityNone)
	if err != nil {
		fmt.Println(err)
	}
	err = captData.GetThumbImage().SaveToFile("../resources/thumb.png")
	if err != nil {
		fmt.Println(err)
	}
}
```

### 创建实例方法
- click.New()  中文文本、字母数字混合点选
- click.NewWithShape()  图形点选

### 配置选项
click.New(click.WithXxx(), ...)
- click.WithImageSize(option.Size)  设置主图尺寸，默认 300x240
- click.WithRangeLen(option.RangeVal)  设置随机内容长度范围
- click.WithRangeAnglePos([]option.RangeVal)  设置随机角度范围
- click.WithRangeSize(option.RangeVal)  设置随机内容大小范围
- click.WithRangeColors([]string)  设置随机颜色
- click.WithDisplayShadow(bool)  设置是否显示阴影
- click.WithShadowColor(string)  设置阴影颜色
- click.WithShadowPoint(option.Point)  设置阴影偏移位置
- click.WithImageAlpha(float32)  设置主图透明度
- click.WithUseShapeOriginalColor(bool)  设置是否使用图形原始颜色，"图形点选"有效

- click.WithThumbImageSize(option.Size)  设置缩略尺寸，默认 150x40
- click.WithRangeVerifyLen(option.RangeVal) 设置校验内容的随机长度范围
- click.WithRangeThumbSize(option.RangeVal)  设置随机缩略内容随机大小范围
- click.WithRangeThumbColors([]string)  设置缩略随机颜色范围
- click.WithRangeThumbBgColors([]string)  设置缩略随机背景颜色范围
- click.WithIsThumbNonDeformAbility(bool)  设置缩略图内容不变形，不受背景影响
- click.WithThumbBgDistort(int)  设置缩略图背景扭曲 option.DistortLevel1 至 option.DistortLevel5
- click.WithThumbBgCirclesNum(int)  设置缩略图绘制小圆点数量
- click.WithThumbBgSlimLineNum(int)  设置缩略图绘制线条数量


### 设置资源
xxx.SetResources(click.WithXxx(), ...)
- click.WithChars([]string)  设置文本种子
- click.WithShapes(map[string]image.Image)  设置图形种子
- click.WithFonts([]*truetype.Font)  设置字体
- click.WithBackgrounds([]image.Image)  设置主图背景
- click.WithThumbBackgrounds([]image.Image)  设置缩略图背景

### 验证码数据
- GetData() map[int]*Dot 获取当前校验的信息
- GetMasterImage() imagedata.JPEGImageData  获取主图
- GetThumbImage() imagedata.PNGImageData  获取缩略图

<br />

## 🖖 滑动式验证码
### 快速使用
```go
package main

import (
	"encoding/json"
	"fmt"
	"image"
	"log"
	"io/ioutil"

	"github.com/wenlng/go-captcha/capts/base/option"
	"github.com/wenlng/go-captcha/capts/slide"
	"github.com/wenlng/go-captcha/capts/base/codec"
)

var slideTileCapt slide.Captcha

func init() {
	slideTileCapt = slide.New()

	bgImage, err := loadPng("../resources/bg.png")
	if err != nil {
		log.Fatalln(err)
	}

	bgImage1, err := loadPng("../resources/bg1.png")
	if err != nil {
		log.Fatalln(err)
	}

	graphs := getSlideTileGraphArr()

	slideTileCapt.SetResources(
		slide.WithGraphImages(graphs),
		slide.WithBackgrounds([]image.Image{
			bgImage,
			bgImage1,
		}),
	)
}

func getSlideTileGraphArr() []*slide.GraphImage {
	tileImage1, err := loadPng("../resources/tile-1.png")
	if err != nil {
		log.Fatalln(err)
	}

	tileShadowImage1, err := loadPng("../resources/tile-shadow-1.png")
	if err != nil {
		log.Fatalln(err)
	}
	tileMaskImage1, err := loadPng("../resources/tile-mask-1.png")
	if err != nil {
		log.Fatalln(err)
	}

	return []*slide.GraphImage{
		{
			OverlayImage: tileImage1,
			ShadowImage:  tileShadowImage1,
			MaskImage:    tileMaskImage1,
		},
	}
}

func main() {
	captData, err := slideTileCapt.Generate()
	if err != nil {
		log.Fatalln(err)
	}

	blockData := captData.GetData()
	if blockData == nil {
		log.Fatalln(">>>>> generate err")
	}

	block, _ := json.Marshal(blockData)
	fmt.Println(">>>>>", string(block))

	err = captData.GetMasterImage().SaveToFile("../resources/master.jpg", option.QualityNone)
	if err != nil {
		fmt.Println(err)
	}
	err = captData.GetTileImage().SaveToFile("../resources/thumb.png")
	if err != nil {
		fmt.Println(err)
	}
}

func loadPng(p string) (image.Image, error) {
	imgBytes, err := ioutil.ReadFile(p)
	if err != nil {
		return nil, err
	}
	return codec.DecodeByteToPng(imgBytes)
}
```


### 创建实例方法
- slide.New() 滑动式
- slide.NewWithRegion()  区域内拖拽滑动式


### 配置选项
slide.New(slide.WithXxx(), ...)
- slide.WithImageSize(*option.Size)  设置主图尺寸，默认 300x240
- slide.WithImageAlpha(float32)  设置主图透明度
- slide.WithRangeGraphSize(val option.RangeVal)  设置图形随机尺寸范围
- slide.WithRangeGraphAnglePos([]option.RangeVal)  设置图形随机角度范围
- slide.WithGenGraphNumber(val int)  设置图形个数
- slide.WithEnableGraphVerticalRandom(val bool)  设置图形横向排序，"滑动式"使用
- slide.WithRangeDeadZoneDirections(val []DeadZoneDirectionType)  设置贴图盲区


### 设置资源
xxx.SetResources(slide.WithXxx(), ...)
- slide.WithBackgrounds([]image.Image)  设置主图背景
- slide.WithGraphImages(images []*GraphImage)  设置贴图的图形

### 验证码数据
- GetData() *Block  获取当前校验的信息
- GetMasterImage() imagedata.JPEGImageData  获取主图
- GetTitleImage() imagedata.PNGImageData  获取缩略图


<br />

## 🖖  旋转式验证码
### 快速使用
```go
package main

import (
	"encoding/json"
	"fmt"
	"image"
	"log"
	"io/ioutil"

	"github.com/wenlng/go-captcha/capts/rotate"
	"github.com/wenlng/go-captcha/capts/base/codec"
)

var rotateCapt rotate.Captcha

func init() {
	rotateCapt = rotate.New()

	bgImage, err := loadPng("../resources/bg.png")
	if err != nil {
		log.Fatalln(err)
	}

	bgImage1, err := loadPng("../resources/bg1.png")
	if err != nil {
		log.Fatalln(err)
	}

	rotateCapt.SetResources(
		rotate.WithImages([]image.Image{
			bgImage,
			bgImage1,
		}),
	)
}

func main() {
	captData, err := rotateCapt.Generate()
	if err != nil {
		log.Fatalln(err)
	}

	blockData := captData.GetData()
	if blockData == nil {
		log.Fatalln(">>>>> generate err")
	}

	block, _ := json.Marshal(blockData)
	fmt.Println(">>>>>", string(block))

	err = captData.GetMasterImage().SaveToFile("../resources/master.png")
	if err != nil {
		fmt.Println(err)
	}
	err = captData.GetThumbImage().SaveToFile("../resources/thumb.png")
	if err != nil {
		fmt.Println(err)
	}
}

func loadPng(p string) (image.Image, error) {
	imgBytes, err := ioutil.ReadFile(p)
	if err != nil {
		return nil, err
	}
	return codec.DecodeByteToPng(imgBytes)
}
```


### 创建实例方法
- rotate.New() 旋转式


### 配置选项
rotate.New(rotate.WithXxx(), ...)
- rotate.WithImageSquareSize(val int)  设置主图大小
- rotate.WithRangeAnglePos(vals []option.RangeVal)  设置校验随机角度范围
- rotate.WithRangeThumbImageSquareSize(val []int)  设置缩略图大小
- rotate.WithThumbImageAlpha(val float32)  设置缩略图透明度


### 设置资源
xxx.SetResources(rotate.WithXxx(), ...)
- rotate.WithBackgrounds([]image.Image)  设置主图图片

### 验证码数据
- GetData() *Block  获取当前校验的信息
- GetMasterImage() imagedata.PNGImageData  获取主图
- GetThumbImage() imagedata.PNGImageData  获取缩略图


<br/>

## 验证码图片对象

### JPEGImageData 对象方法
- Get() image.Image  获取原图像
- ToBytes() []byte  转为JPEG字节数组
- ToBytesWithQuality(imageQuality int) []byte   指定清晰度转为JPEG字节数组
- ToBase64() string  转为 JPEG Base64 字符串
- ToBase64WithQuality(imageQuality int) string   指定清晰度转为 JPEG Base64 字符串
- SaveToFile(filepath string, quality int) error 保存 JPEG 到文件


### PNGImageData 对象方法
- Get() image.Image  获取原图像
- ToBytes() []byte  转为PNG字节数组
- ToBase64() string   转为 PNG Base64 字符串
- SaveToFile(filepath string) error  保存 PNG 到文件

<br/>

## 验证模块
- <p>文字点选式 ✔</p>
- <p>图形点选式 ✔</p>
- <p>滑动式 ✔ </p>
- <p>旋转式 ✔ </p>


## B端 / C端 依赖包
- <p>Web原生 ✔</p>
- <p>Vue ✔</p>
- <p>React ✔</p>
- <p>小程序</p>
- <p>UniApp</p>
- <p>Android App</p>
- <p>IOS App</p>
- <p>Flutter App</p>
- <p>... </p>

<br/>

请作者喝杯咖啡：[http://witkeycode.com/sponsor](http://witkeycode.com/sponsor)

<br/>

## LICENSE
Go Captcha source code is licensed under the Apache Licence, Version 2.0 [http://www.apache.org/licenses/LICENSE-2.0.html](http://www.apache.org/licenses/LICENSE-2.0.html)