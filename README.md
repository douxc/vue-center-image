# vue-image
Center display picture in equal proportions

!['capture'][capture]

# 兼容性

理论上支持[flex](http://caniuse.com/#search=flex)属性的浏览器都支持

# 安装

```
npm install @douxc/vue-image
```
或者
```
yarn add @douxc/vue-image
```

# 使用

``` vue
 import Vue from 'vue';
 import VueImage from '@douxc/vue-image';

 Vue.component('vueImage',VueImage);
 
 ...
 <template>
  <vueImage :src='src'></vueImage>
 </template>
```

### props

props    | required | default | description
-----    | -------- | ------  | ----------
 src     | true     |         | 图片地址
maxWidth | false    | 320px   | 图片最大宽度、高度
inline   | false    | false   | 是否显示为行内元素 "display:inline-block"
clickable| false    | false   | 是否可以点击查看大图
compress | false    | true(!compress) | 是否请求压缩之后的图片（即在请求的地址后添加处理规则）
pdfStyle | false    | vue-image-pdf | 如果地址是pdf文件时添加的class名称


[capture]:./capture.png