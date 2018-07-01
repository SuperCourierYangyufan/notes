# 开发要点
1. ##### 设置为webapp
  * index.html中<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,user-scalable-no">
  * 导入border.css(1px问题) reset.css在main.js中
  * cnpm install fastclick --save 
  * 导入fastClick并写入fastClick.attach(document.body)
2. ##### stylus
  * cnpm install --save stylus stylus-loader
  * <style lang="stylus" scoped>
  * @import "../../../assets/styles/vatstyle.styl"导入常量
      * $baColor = red 定义
      * 文件中$baColor便可以使用
3. ##### iconfont
  * 选择到仓库后下载
  * 只需要font文件和iconfont.css文件
  * 修改iconfont.css中的url地址
  * <span class="iconfont">选择网上仓库中图片复制代码</span>
4. ##### vue-awesome-swiper 轮播效果 github查看用法
5. rem是根据html的font大小来决定大小的
	```
	<script type="text/javascript">
		(function (doc, win) {
		var docEl = doc.documentElement,
			resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
			recalc = function () {
				var clientWidth = docEl.clientWidth;
				if (!clientWidth) return;
				if(clientWidth>750) cilentWidth=750;//这里限制最大的宽度尺寸，从而实现PC端的两边留白等
				docEl.style.fontSize = 10 * (clientWidth / 160) + 'px';
			};

		if (!doc.addEventListener) return;
		win.addEventListener(resizeEvt, recalc, false);
		doc.addEventListener('DOMContentLoaded', recalc, false);
	})(document, window);
	</script>		
	```
6. 1PX物理像素
	```
			@media only screen and (-webkit-device-pixel-ratio:2) {
					#test{
						transform: scaleY(.5);
						
					}
				}
			@media only screen and (-webkit-device-pixel-ratio:3) {
				#test{
					transform: scaleY(.33333333333333);
					
				}
			}
			
	```
7. 移动事件
	* touchstart
	* touchmove
	* touchend
	* 绑定事件,取消手机所有默认行为
		```
			document.addEventListener("touchstart",function(ev){
				ev = ev||event;
				ev.preventDefault(); //取消手机所有默认行为,防止自己写的出错
			});
		```
		* 页面上所有滚动条失效
	* 开启部分元素的默认行为
		```
		元素.addEventListener("touchstart",function(ev){
				ev = ev||event;
				ev.stopPropagation
			});
		```
	