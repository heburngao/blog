[cocos2d-x中的plist文件格式详解](http://zengrong.net/post/1981.htm)

本文并未全部完成，请耐心等待……
<hr>
## 1. 什么是plist文件格式？

这是一种人类可读的串行化对象文件，由苹果公司发明，最早用于NeXTSTEP系统。详情看这里：[Plist][plist] 。

cocos2d-x 从 cocos2d-iphone 发展而来，因此在引擎中大量使用了这种文件格式。

## 2. 如何编辑plist文件？

在 OS X 系统上，XCode 就可以直接打开和编辑plist文件。而在Windows上，我还没有找到可用的plist编辑软件。

当然，plist是基于XML的纯文本格式，随便找个文本编辑器就可以编辑了。

## 3. cocos2d-x在哪些地方使用了plist格式？

大致有这样几种：<!--more-->

* 图像纹理定义文件  
将多个纹理拼在一张大图上，使用 `CCSpriteFrameCache` 可以载入这类plist文件；  
这里有一个图像纹理定义文件的范例： `[cocos2d-x]\samples\Cpp\TestCpp\Resources\animations\grossini_family.plist` 。  
* Label纹理定义文件
作用与图像纹理定义文件类似，只不过处理的是自己，面向 `CCLabelAtlas`；
这里有一个Label纹理定义文件的范例： `[cocos2d-x]\samples\Cpp\TestCpp\Resources\fonts\tuffy_bold_italic-charmap.plist` 。  
* 帧动画定义
定义一个或多个动画中，使用哪些纹理，使用 `CCAnimationCache` 可以载入这类plist文件；  
这里有一个帧动画定义文件的范例： `[cocos2d-x]\samples\Cpp\TestCpp\Resources\animations\animations.plist` 。  

## 4. 生成plist文件的工具

对于纹理定义文件来说，它的作用是如何在大图中找到碎图的坐标。因此很多拼合碎图的软件可以在拼合碎图的同时生成plist文件。

* [TexturePacker][tp] 是所有平台上最好用的工具了；
* [Zwoptex][zw] 是MAC Only的软件，我不太喜欢用；
* [SpritePacker][sp] 是Windows Only的软件，功能尚可。

## 5. 图像纹理定义文件格式说明

cocos2d-x中的纹理定义格式，是以Zwoptex生成的格式为标准的。

Zwoptex生成的格式，有4种主要不同的版本：

* format值为0，代表Flash版本；
* format值为1，Zwoptex 0.4b以前支持；
* format值为2，Zwoptex 1.0以后支持，与format1的区别在于支持旋转；
* format值为3，属性名称进行了大幅修改，Zwoptes1.0.2之后支持。

这3种格式的plist文件，cocos2d-x都能支持，具体的解析代码在 `CCSpriteFrameCache::addSpriteFramesWithDictionary`。

TexturePacker生成的for cocos2d plist格式与Zwoptex生成的format为2的格式相同。

### 5.1 format为0的plist文件

这里贴一个比较完整plist文件，为了方便描述，其中仅包含一个frame。
<pre lang="XML">
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>texture</key>
	<dict>
		<key>width</key>
		<integer>256</integer>
		<key>height</key>
		<integer>128</integer>
	</dict>
	<key>frames</key>
	<dict>
		<key>grossini.png</key>
		<dict>
			<key>x</key>
			<integer>103</integer>
			<key>y</key>
			<integer>1</integer>
			<key>width</key>
			<integer>51</integer>
			<key>height</key>
			<integer>109</integer>
			<key>offsetX</key>
			<real>0</real>
			<key>offsetY</key>
			<real>-1</real>
			<key>originalWidth</key>
			<integer>85</integer>
			<key>originalHeight</key>
			<integer>121</integer>
		</dict>
	</dict>
</dict>
</plist></pre>
### 5.2 format为2的plist文件内容
<pre lang="XML">
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
    <dict>
        <key>frames</key>
        <dict>
            <key>parts-dragon_leg_l.png</key>
            <dict>
                <key>frame</key>
                <string>{{866,2},{152,254}}</string>
                <key>offset</key>
                <string>{0,0}</string>
                <key>rotated</key>
                <false/>
                <key>sourceColorRect</key>
                <string>{{0,0},{152,254}}</string>
                <key>sourceSize</key>
                <string>{152,254}</string>
            </dict>
        </dict>
        <key>metadata</key>
        <dict>
            <key>format</key>
            <integer>2</integer>
            <key>realTextureFileName</key>
            <string>dragon.png</string>
            <key>size</key>
            <string>{1024,2048}</string>
            <key>smartupdate</key>
            <string>$TexturePacker:SmartUpdate:26d1d28da42d49170ab3142654fea750:1/1$</string>
            <key>textureFileName</key>
            <string>dragon.png</string>
        </dict>
    </dict>
</plist></pre>
### 5.3 format为3的plist文件内容
<pre lang="XML">
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>frames</key>
	<dict>
		<key>armyLevelBg.png</key>
		<dict>
			<key>aliases</key>
			<array/>
			<key>spriteColorRect</key>
			<string>{{0, 0}, {61, 36}}</string>
			<key>spriteOffset</key>
			<string>{0, -0}</string>
			<key>spriteSize</key>
			<string>{61, 36}</string>
			<key>spriteSourceSize</key>
			<string>{61, 36}</string>
			<key>spriteTrimmed</key>
			<true/>
			<key>textureRect</key>
			<string>{{195, 2}, {36, 61}}</string>
			<key>textureRotated</key>
			<true/>
		</dict>
	</dict>
	<key>metadata</key>
	<dict>
		<key>version</key>
		<string>1.6.0</string>
		<key>format</key>
		<integer>3</integer>
		<key>size</key>
		<string>{256, 512}</string>
		<key>name</key>
		<string>army_zw.zwd</string>
		<key>textureFileName</key>
		<string>army_zw_cocos2d.png</string>
		<key>premultipliedAlpha</key>
		<false/>
	</dict>
</dict>
</plist></pre>
Frame:

Top-Left originating rectangle of the sprite’s pixel texture coordinates. Cocos2′d will convert these to UV coordinates (0-1) when loading based on the texture size.

Offset:

Zwoptex trim’s transparency off sprites. Because of this sprite’s need to be offset to ensure their texture is drawn in correct alignment to their original size.

Source Color Rect:

This is the Top-Left originating rectangle that is the valid pixel data of the sprite. Say you have a 512×512 sprite that only has 10×10 pixels of data inside of it located at 500×500. The source color rect could be {500,500,10,10}.

Format:

Version number related to what version of Zwoptex was used so cocos2d knows how to parse the plist properly.

Flash Version: 0

Desktop Version 0-0.4b: 1

Desktop Version 1.x: 2

In general if you’re not trimming images you can set offset to be 0,0 and sourceColorRect to 0,0,frame.size.width,frame.size.height.

http://www.cocos2d-iphone.org/forums/topic/zwoptex-and-their-plist-explanation/

## 5. Label纹理定义文件

对这种格式的具体的解析代码在 `CCLabelAtlas::initWithString` 。

## 6. 帧动画定义文件格式说明

这种文件也有2种格式：

* format 1，这种格式定义了一个动画包含哪些帧，但是这些帧需要从其他的纹理文件中提前载入；
* format 2，这种格式可以定义每帧的播放延迟，也可以直接指定需要的纹理文件的名称。

对这两种格式的具体的解析代码在 `CCAnimationCache::addAnimationsWithDictionary` 。

### format 1动画格式plist文件内容
<pre lang="XML">
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>animations</key>
	<dict>
		<key>dance_1</key>
		<dict>
			<key>delay</key>
			<real>0.2</real>
			<key>frames</key>
			<array>
				<string>grossini_dance_01.png</string>
				<string>grossini_dance_02.png</string>
				<string>grossini_dance_03.png</string>
				<string>grossini_dance_04.png</string>
				<string>grossini_dance_05.png</string>
				<string>grossini_dance_06.png</string>
				<string>grossini_dance_07.png</string>
				<string>grossini_dance_08.png</string>
				<string>grossini_dance_09.png</string>
				<string>grossini_dance_10.png</string>
				<string>grossini_dance_11.png</string>
				<string>grossini_dance_12.png</string>
				<string>grossini_dance_13.png</string>
				<string>grossini_dance_14.png</string>
			</array>
		</dict>
		<key>dance_2</key>
		<dict>
			<key>delay</key>
			<real>0.2</real>
			<key>frames</key>
			<array>
				<string>grossini_dance_gray_01.png</string>
				<string>grossini_dance_gray_02.png</string>
				<string>grossini_dance_gray_03.png</string>
				<string>grossini_dance_gray_04.png</string>
				<string>grossini_dance_gray_05.png</string>
				<string>grossini_dance_gray_06.png</string>
				<string>grossini_dance_gray_07.png</string>
				<string>grossini_dance_gray_08.png</string>
				<string>grossini_dance_gray_09.png</string>
				<string>grossini_dance_gray_10.png</string>
				<string>grossini_dance_gray_11.png</string>
				<string>grossini_dance_gray_12.png</string>
				<string>grossini_dance_gray_13.png</string>
				<string>grossini_dance_gray_14.png</string>
			</array>
		</dict>
		<key>dance_3</key>
		<dict>
			<key>delay</key>
			<real>0.2</real>
			<key>frames</key>
			<array>
				<string>grossini_blue_01.png</string>
				<string>grossini_blue_02.png</string>
				<string>grossini_blue_03.png</string>
				<string>grossini_blue_04.png</string>
			</array>
		</dict>
	</dict>
</dict>
</plist></pre>
### format 2动画格式plist文件内容
<pre lang="XML">
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>animations</key>
	<dict>
		<key>dance_1</key>
		<dict>
			<key>delayPerUnit</key>
			<real>0.2</real>
			<key>restoreOriginalFrame</key>
			<true/>
			<key>loops</key>
			<integer>2</integer>
			<key>frames</key>
			<array>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_01.png</string>
					<key>delayUnits</key>
					<integer>1</integer>
					<key>notification</key>
					<dict>
						<key>firstframe</key>
						<true/>
					</dict>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_02.png</string>
					<key>delayUnits</key>
					<integer>1</integer>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_03.png</string>
					<key>delayUnits</key>
					<real>0.5</real>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_04.png</string>
					<key>delayUnits</key>
					<integer>1</integer>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_05.png</string>
					<key>delayUnits</key>
					<integer>1</integer>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_06.png</string>
					<key>delayUnits</key>
					<integer>1</integer>
					<key>notification</key>
					<dict>
						<key>key1</key>
						<integer>1234</integer>
						<key>key2</key>
						<false/>
					</dict>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_07.png</string>
					<key>delayUnits</key>
					<integer>1</integer>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_08.png</string>
					<key>delayUnits</key>
					<integer>2</integer>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_09.png</string>
					<key>delayUnits</key>
					<real>0.5</real>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_10.png</string>
					<key>delayUnits</key>
					<integer>1</integer>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_11.png</string>
					<key>delayUnits</key>
					<integer>1</integer>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_12.png</string>
					<key>delayUnits</key>
					<integer>1</integer>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_13.png</string>
					<key>delayUnits</key>
					<integer>1</integer>
				</dict>
				<dict>
					<key>spriteframe</key>
					<string>grossini_dance_14.png</string>
					<key>delayUnits</key>
					<integer>1</integer>
					<key>notification</key>
					<dict>
						<key>lastframe</key>
						<true/>
					</dict>
				</dict>
			</array>
		</dict>
	</dict>
	<key>properties</key>
	<dict>
		<key>spritesheets</key>
		<array>
			<string>animations/grossini.plist</string>
			<string>animations/grossini_blue.plist</string>
			<string>animations/grossini_family.plist</string>
		</array>
		<key>format</key>
		<integer>2</integer>
	</dict>
</dict>
</plist></pre>
[tp]: http://www.codeandweb.com/texturepacker
[plist]: http://zh.wikipedia.org/wiki/Plist
[zw]: http://www.zwopple.com/zwoptex/
[sp]: http://spritepacker.kernys.net/
