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
